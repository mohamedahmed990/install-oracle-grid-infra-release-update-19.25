## ** Validation of Oracle Inventory **

Before beginning patch application, check the consistency of inventory information for Grid home and each Oracle home to be patched. Run this command as the respective Oracle home owner to check the consistency: 

```bash

<ORACLE_HOME>/OPatch/opatch lsinventory -detail -oh <ORACLE_HOME>

```

If this command succeeds, it lists the Oracle components that are installed in the home. Save the output so that you have the status prior to the patch application.
