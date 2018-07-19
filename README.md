# fMRI_R REPOSITORY

This project is to utilize R to streamline some fMRI processing and offer flexibility of some degrees.

There are 2 main scripts:
  - dnpl_utility.R
      - The utility functions scripts consists of all the small component functions for the pipe to run. 
  - fslpipe.R
      - The steamlined fsl pipeline

To use these functions, you can pull a copy or alternatively use "source_url" function in the package 'devtools'
```
install.packages("devtools")
devtools::source_url("https://raw.githubusercontent.com/DecisionNeurosciencePsychopathology/fMRI_R/master/dnpl_utility.R")
```

The function will do the following steps:
- Step 1: Generate design matrix, heatmap for regressors, and regressor files for each subject for each run using the DEPENDLAB package: https://github.com/PennStateDEPENdLab/dependlab
- Step 2: Use FEAT pipeline in FSL package to do single subject analysis using a template #This step utilizes parallel processing
- Step 3: Creat symbolic links and use the flirt function to resample the data to standard space
- Step 4: Take subject level statistic for a given subjects for all runs #This step utilizes parallel processing
- Step 5: Do group level analysis (currently limited flexiblity in term of statistic to run)  #This step utilizes parallel processing
- Step 6: Graph the result and save to the group level directory 

Each of these steps can be skipped using arguments but will fail if not enough data or argument is given. 

To utilize the pipeline in this repository, there are couple thing one would need to set up few things before calling the main function:
For an example, see: https://github.com/Jiazhouchen/pecina/blob/master/gen_regressor_signal.R

1, One must set up proper argument environment which contains the following objects
```
argu<-as.environment(list(
nprocess=12,                                         #Number of processes to allow for paralle processing, if NULL will use detectCores()-2
onlyrun=NULL,                                        #Do only these steps, if NULL then do all
forcereg=FALSE,                                      #Force Reg gen restart
cfgpath="/Volumes/xx/xxxx/xxx/xxx.cfg",              #Where is the cfg config file
regpath="/Volumes/xx/xxx/xx/xxxx/xx/xxx_reg",        #Where to put/are the regressors 
gridpath="grid.csv",                                 #Where is the grid to make signal
func.nii.name="nfswudktm*[0-9].nii.gz",              #What pre-proc data to grab:
proc_id_subs="_a",                                   #Does the ID have a tails, if NULL will not add anything
model.name="PE_8C_reg_by_vol",                       #Now set up the model name, will be used as folder name
model.varinames=c("inf",                             #Model variable argument, indicate what variable to grab, must be within grid
                  "noinf",
                  "fb",
                  "nofb"),
regtype=".1D",                                       #Regressor file to use, single col by volume use ".1D" and FSL style use "_FSL3col.txt"
ifnuisa=FALSE,                                       #If to convolve with nuisance regressors with dependlab package
ssub_fsl_templatepath="/Volumes/x/x/x/x/x.fsf",      #Single subject FSL template path
gsub_fsl_templatepath="/Volumes/x/x/x/x/x/gx.fsf",   #Group level FSL template path
ssub_outputroot="/Volumes/x/x/x/x/x/x",              #Single Subject output root path (before model name folder)
glvl_outputroot="/Volumes/x/x/x/x/x/x",              #Group level analysis output path (No Difference with next one)
templatedir="x/x/MNI152_T1_xmm_brain.nii",           #Brain template path
ifoverwrite_secondlvl=FALSE,                         #If to redo all the linking for 2nd level
hig_lvl_path_filter=NULL,                            #If there's anyother folder within $output/$model.name that contains *.feat, please remove it from here
graphic.threshold=0.95                               #Threshold for graphic purposes
))
```
2, Then you must modify the grid.csv and fsl templates to ensure that everything mataches
(More specific examples and how-tos are coming soon)

3, Make an preprocessing function and generate subject level behaviroal data
(More specific examples and how-tos are coming soon)

4, Call the function and it should take off and run. 
```
fsl_pipe(argu=NULL, #Input for an environment object that contains all arguments;
         prep.call.func="", #Input a character string that's the name of the prep proc function.
         prep.call.allsub=list(ID=list(data=NULL)), #List of ID list of arguments for prep.call.
         ) 
```

