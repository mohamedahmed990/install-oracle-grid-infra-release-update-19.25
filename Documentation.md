## **Table of content**
  + [Validation of Oracle Inventory](#validation-of-oracle-inventory)
  + [Download and Unzip the Patch](#download-and-Unzip-the-patch)
  + [Run OPatch Conflict Check](#run-opatch-conflict-check)
  + [Patch Installation Checks](#patch-installation-checks)

  



## **Validation of Oracle Inventory**

Before beginning patch application, check the consistency of inventory information for Grid home and each Oracle home to be patched. Run this command as the respective Oracle home owner to check the consistency: 

```bash

$ORACLE_HOME/OPatch/opatch lsinventory -detail -oh $ORACLE_HOME

```

-If this command succeeds, it lists the Oracle components that are installed in the home. Save the output so that you have the status prior to the patch application.
-If this command fails, contact Oracle Support for assistance.

----

## **Download and Unzip the Patch**

To apply the patch, it must be accessible from all nodes in the Oracle cluster. Download the patch and unzip it to a shared location called the `<UNZIPPED_PATCH_LOCATION>`. This directory must be empty and cannot be `/tmp`. Additionally, the directory should have read permission for the `ORA_INSTALL` group:

```bash
cd <UNZIPPED_PATCH_LOCATION>
```

Ensure that the directory is empty:

```bash
ls
```

Unzip the patch as the Grid home owner except for installations that do not have any Grid homes. For installations where this patch is applied to the Oracle home only, the patch must be unzipped as the Oracle home owner:

```bash

unzip p36916690_190000_<platform>.zip

```

----

## **Run OPatch Conflict Check**

Determine whether any currently installed one-off patches conflict with this patch 36916690 as follows:
  - As the Grid home user:
    
  ```bash

    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36912597
  
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36917416
  
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36917397
  
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36940756
  
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36758186

  ```
  - For Oracle home, as home user:
    
  ```bash

    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/oracle/p36916690/36916690/36912597
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/oracle/p36916690/36916690/36917416

  ```
-----

## **Run OPatch System Space Check**

Check if enough free space is available on the ORACLE_HOME filesystem for the patches to be applied as given below:

  - For Grid Infrastructure home, as home user:
    1. Create file /tmp/patch_list_gihome.txt with the following content:
       ```bash
       
        cat /tmp/patch_list_gihome.txt
       
        /home/grid/p36916690/36916690/36912597
        /home/grid/p36916690/36916690/36912597
        /home/grid/p36916690/36916690/36917416
        /home/grid/p36916690/36916690/36917397
        /home/grid/p36916690/36916690/36940756
        /home/grid/p36916690/36916690/36758186
       
       ```
    2. Run the OPatch command to check if enough free space is available in the Grid Infrastructure home:
      ```bash
        $ORACLE_HOME/OPatch/opatch prereq CheckSystemSpace -phBaseFile /tmp/patch_list_gihome.txt
      ```
  - For Oracle home, as home user:
    
    1. Create file /tmp/patch_list_dbhome.txt with the following content:
       
      ```bash
        cat /tmp/patch_list_dbhome.txt
        /home/oracle/p36916690/36916690/36912597
        /home/oracle/p36916690/36916690/36917416
      ```

    2. Run OPatch command to check if enough free space is available in the Oracle home:
       
       ```bash
       
         $ORACLE_HOME/OPatch/opatch prereq CheckSystemSpace -phBaseFile /tmp/patch_list_dbhome.txt
        
       ```

## **Patch Installation Checks** 

  -  The Cluster Verification Utility (CVU) command line interface (CLUVFY) may be used to verify the readiness of the Grid_Home to apply the patch. The CLUVFY command may be issued from the configured Grid_Home or from the downloaded latest version of the CVU standalone release (preferred) from My Oracle Support patch 30839369

  -  Before applying the patch, the readiness of the Grid_Home can be verified by issuing the following command from any one of the cluster nodes. This command reports issues, if any are detected, in the Grid_Home which may affect the patching process:
    
      ```bash

      cluvfy stage -pre patch

      ```

  -  The CLUVFY command line for patching ensures that the Grid_Home can receive the new patch and also ensures that the patch application process completed successfully leaving the home in the correct state.

--------

## **OPatchAuto Out-of-Place Patching**
  -  Out-of-place patching is a mechanism where patching is done by creating a clone of the Oracle home, applying patches on the cloned home, and switching services to the newly created cloned home. This approach to patching reduces unavailability or downtime of the service by separating the step to create the patched clone home from the step of switching services when they must be restarted.


------

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
       

