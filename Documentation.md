## **Table of content**
  1. [Validation of Oracle Inventory](#validation-of-oracle-inventory)
  2. [Download and Unzip the Patch](#download-and-Unzip-the-patch)
  3. [Run OPatch Conflict Check](#run-opatch-conflict-check)



## **Validation of Oracle Inventory**

Before beginning patch application, check the consistency of inventory information for Grid home and each Oracle home to be patched. Run this command as the respective Oracle home owner to check the consistency: 

```bash

$ORACLE_HOME/OPatch/opatch lsinventory -detail -oh &ORACLE_HOME

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

    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36912597
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /home/grid/p36916690/36916690/36917416

  ```
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
        /home/grid/p36916690/36916690/36912597
        /home/grid/p36916690/36916690/36917416
      ```

    2. Run OPatch command to check if enough free space is available in the Oracle home:
       
       ```bash
         $ORACLE_HOME/OPatch/opatch prereq CheckSystemSpace -phBaseFile /tmp/patch_list_dbhome.txt  
       ```

       
       

