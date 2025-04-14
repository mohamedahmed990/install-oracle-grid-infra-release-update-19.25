## **OPatchAuto**

  - The OPatch utility has automated the patch application for the Oracle Grid Infrastructure (Grid) home and the Oracle RAC database homes.
    
  -  It operates by querying existing configurations and automating the steps required for patching each Oracle RAC database home of same version and the Grid home.
    
  -  The utility must be executed by an operating system (OS) user with `root` privileges, and it must be executed on each node in the cluster if the Grid home or Oracle RAC database home is in non-shared storage.
  
  -  The utility can be run in parallel on the cluster nodes except for the first (any) node.
  
  -  Depending on command line options specified, one invocation of OPatchAuto can patch the Grid home, Oracle RAC database homes, or both Grid and Oracle RAC database homes of the same Oracle release version as the patch. You can also roll back the patch with the same selectivity.
  
  -  Add the directory containing the OPatchAuto to the $PATH environment variable. For example:

      ```bash
      
        export PATH=$PATH:<GI_HOME>/OPatch
        cd <UNZIPPED_PATCH_LOCATION>
      
      ```
  
  -  Or, when using -oh flag:

     ```bash
     
       export PATH=$PATH:<oracle_home_path>/OPatch
       cd <UNZIPPED_PATCH_LOCATION>
     
     ```
  -  To patch the Grid home and all Oracle RAC database homes of the same version:

      ```bash
      
        patchauto apply <UNZIPPED_PATCH_LOCATION>/36916690

      ```
  -  To patch only the Grid home:

      ```bash
      
        opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690 -oh <GI_HOME>
      ```
      
  -  To patch one or more Oracle RAC database homes:

      ```bash
      
        opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690 -oh <oracle_home1_path>,<oracle_home2_path>
      ```
      
  -  To roll back the patch from the Grid home and each Oracle RAC database home:

      ```bash
      
        opatchauto rollback <UNZIPPED_PATCH_LOCATION>/36916690 

      ```
      
  -  To roll back the patch from the Grid home:

      ```bash
      
        opatchauto rollback <UNZIPPED_PATCH_LOCATION>/36916690 -oh <path to GI home>  

      ```
      
  -  To roll back the patch from the Oracle RAC database home:

      ```bash
      
        opatchauto rollback <UNZIPPED_PATCH_LOCATION>/36916690 -oh <oracle_home1_path>,<oracle_home2_path> 

      ```
