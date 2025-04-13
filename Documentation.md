## ** Validation of Oracle Inventory **

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

## **  Run OPatch Conflict Check **

Determine whether any currently installed one-off patches conflict with this patch 36916690 as follows:
  - As the Grid home user:
  ```bash
    $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36912597
  
   $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36917416
  
   $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36917397
  
   $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36940756
  
   $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36758186
  ```
  - For Oracle home, as home user:
      ```bash
        $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36912597
      % $ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <UNZIPPED_PATCH_LOCATION>/36916690/36917416

      ```
## **Run OPatch System Space Check**
      
    

