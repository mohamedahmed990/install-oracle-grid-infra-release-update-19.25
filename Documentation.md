## **Table of content**
  + [Validation of Oracle Inventory](#validation-of-oracle-inventory)
  + [Download and Unzip the Patch](#download-and-Unzip-the-patch)
  + [Run OPatch Conflict Check](#run-opatch-conflict-check)
  + [Patch Installation Checks](#patch-installation-checks)
  + [Patch Post Installation Instructions](#patch-post-installation-instructions)


  



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

  -  The Cluster Verification Utility (CVU) command line interface (CLUVFY) may be used to verify the readiness of the Grid_Home to apply the patch. The CLUVFY command may be issued from the configured Grid_Home or from the downloaded latest version of the CVU standalone release (preferred) from My Oracle Support patch [30839369](https://updates.oracle.com/Orion/PatchDetails/handle_plat_lang_change?release=600000000153639&plat_lang=233P&patch_file=&file_id=&password_required=&password_required_readme=&merged_trans=&aru=25614629&patch_num=30839369&patch_num_id=4037790&default_release=600000000153639&default_plat_lang=226P&default_compatible_with=&patch_password=&orderby=&direction=&no_header=0&sortcolpressed=&tab_number=)

  -  Before applying the patch, the readiness of the Grid_Home can be verified by issuing the following command from any one of the cluster nodes. This command reports issues, if any are detected, in the Grid_Home which may affect the patching process:
    
      ```bash

          cluvfy stage -pre patch

      ```

  -  The CLUVFY command line for patching ensures that the Grid_Home can receive the new patch and also ensures that the patch application process completed successfully leaving the home in the correct state.

--------

## **OPatchAuto Out-of-Place Patching**
  -  Out-of-place patching is a mechanism where patching is done by creating a clone of the Oracle home, applying patches on the cloned home, and switching services to the newly created cloned home. This approach to patching reduces unavailability or downtime of the service by separating the step to create the patched clone home from the step of switching services when they must be restarted.


------


       
## **Patch Installation**

  -  The patch instructions will differ based on the configuration of the Grid infrastructure and the Oracle RAC database homes.
  
  -  The most common configurations are listed as follows:
    
      -  Case 1: Oracle RAC, where the Grid home and the Oracle homes are not shared and Oracle ACFS file system is not configured

      -  Case 2: Oracle RAC, where the Grid home is not shared, Oracle home is shared, and Oracle ACFS may be used

      -  Case 3: Single-instance homes not managed by Oracle Grid Infrastructure

  -  Our configuration of the Grid infrastructure and the Oracle RAC database homes is case 1 , so we will apply installation instructions as follow:

      -As root user, execute the following command on each node of the cluster:
     
        ```bash

             <GI_HOME>/OPatch/opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690

             cd <UNZIPPED_PATCH_LOCATION>
        ```

---------

## **Patch Post Installation Instructions**

  1.  **Load Modified SQL Files into the Database**:

       -  The following steps load modified SQL files into the database. For an Oracle RAC environment, perform these steps on only one node.

             1. For each separate database running on the same shared Oracle home being patched, run the datapatch utility as described in the following table

                  | Steps | Non-CDB or Non-PDB Database            | Steps | Multitenant (CDB/PDB) Oracle Database          |
                  |-------|----------------------------------------|-------|------------------------------------------------|
                  | 1     | sqlplus /nolog                         | 1     | sqlplus /nolog                                 |
                  | 2     | SQL> Connect / as sysdba               | 2     | SQL> Connect / as sysdba                       |
                  | 3     | SQL> startup                           | 3     | SQL> startup                                   |
                  | 4     | quit                                   | 4     | SQL> alter pluggable database all open;        |
                  | 5     | cd $ORACLE_HOME/OPatch                 | 5     | quit                                           |
                  | 6     | ./datapatch -sanity_checks (optional)  | 6     | cd $ORACLE_HOME/OPatch                         |
                  | 7     | ./datapatch -verbose                   | 7     | ./datapatch -sanity_checks (optional)          |
                  |       |                                        | 8     | ./datapatch -verbose                           |

                
             2.  Check the following log files in `$ORACLE_BASE/cfgtoollogs/sqlpatch/36912597/<unique patch ID>` for errors:

                   ```bash
                   
                     36912597_apply_<database SID>_<CDB name>_<timestamp>.log
                   
                   ```

            3.  Any (pluggable) database that has invalid objects after the execution of datapatch should have catcon.pl run to revalidate those objects. For example:

                   ```bash

                    export PATH=$PATH:$ORACLE_HOME/bin
                    cd $ORACLE_HOME/rdbms/admin
                    $ORACLE_HOME/perl/bin/perl $ORACLE_HOME/rdbms/admin/catcon.pl -n 1 -e -b utlrp -d $ORACLE_HOME/rdbms/admin utlrp.sql

                   ```


                   
                  

          



     
