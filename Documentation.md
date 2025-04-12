# **Oracle RAC Patch Installation Guide (2-Node Cluster)**  
**Patch 36916690 - GI Release Update 19.25.0.0.241015**  

This guide provides **step-by-step instructions** for patching a **2-node Oracle RAC** environment with detailed explanations.  

---

## **📌 Pre-Patch Checklist**  
### **1. Verify OPatch Version**  
```bash
$ORACLE_HOME/OPatch/opatch version
```  
🔹 **Requirement**: OPatch **12.2.0.1.43 or later**  
🔹 **Why?** Older versions may fail or miss critical fixes.  

### **2. Backup Oracle Homes & Inventory**  
```bash
# Backup Grid Home
tar -czvf /backup/grid_home_backup.tar.gz $GRID_HOME

# Backup Oracle Home
tar -czvf /backup/oracle_home_backup.tar.gz $ORACLE_HOME

# Backup Central Inventory
tar -czvf /backup/oraInventory.tar.gz $(cat /etc/oraInst.loc | grep inventory_loc | cut -d= -f2)
```  
🔹 **Why?** Ensures rollback is possible if patching fails.  

### **3. Check for Conflicts**  
```bash
$ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir <PATCH_DIR>/36916690/36912597
```  
🔹 **Why?** Detects if existing patches conflict with the new update.  

### **4. Stop Databases & Services**  
```bash
# Stop all databases on both nodes
srvctl stop database -d <DB_UNIQUE_NAME>

# Verify shutdown
srvctl status database -d <DB_UNIQUE_NAME>
```  
🔹 **Why?** Prevents corruption during patching.  

---

## **🔧 Patching Steps (Node 1 First, Then Node 2)**  

### **Step 1: Patch Grid Infrastructure (GI) on Node 1**  
```bash
# As root, apply GI patch on Node 1
<GI_HOME>/OPatch/opatchauto apply <PATCH_DIR>/36916690 -oh <GI_HOME>
```  
🔹 **What Happens?**  
- OPatchAuto updates Grid Infrastructure binaries.  
- Automatically restarts cluster services.  

### **Step 2: Patch Oracle Home on Node 1**  
```bash
# As root, apply DB patch on Node 1
<GI_HOME>/OPatch/opatchauto apply <PATCH_DIR>/36916690 -oh <ORACLE_HOME>
```  
🔹 **What Happens?**  
- Updates Oracle RAC binaries.  
- Restarts database services on Node 1.  

### **Step 3: Verify Node 1 Patch**  
```bash
# Check applied patches
<ORACLE_HOME>/OPatch/opatch lsinventory

# Verify cluster status
crsctl check cluster -all
```  

### **Step 4: Repeat Steps 1-3 on Node 2**  
🔹 **Why Sequentially?** Ensures **rolling update** (minimal downtime).  

---

## **🔄 Post-Patch Steps (On One Node Only)**  

### **Step 5: Run Datapatch (SQL Updates)**  
```bash
cd $ORACLE_HOME/OPatch
./datapatch -verbose
```  
🔹 **What Happens?**  
- Applies SQL changes to the database.  
- Updates `dba_registry_sqlpatch`.  

### **Step 6: Recompile Invalid Objects**  
```sql
-- Run in SQL*Plus as SYSDBA
@?/rdbms/admin/utlrp.sql
```  
🔹 **Why?** Ensures all database objects are valid.  

### **Step 7: Start All Instances**  
```bash
srvctl start database -d <DB_UNIQUE_NAME>
```  

---

## **🛠 Rollback Procedure (If Needed)**  
### **1. Stop Databases**  
```bash
srvctl stop database -d <DB_UNIQUE_NAME>
```  

### **2. Rollback Patch on Both Nodes**  
```bash
# On each node:
<GI_HOME>/OPatch/opatchauto rollback <PATCH_DIR>/36916690
```  

### **3. Restart Cluster & DB**  
```bash
crsctl stop cluster -all
crsctl start cluster -all
srvctl start database -d <DB_UNIQUE_NAME>
```  

---

## **🚨 Known Issues & Fixes**  
| Issue | Solution |  
|-------|----------|  
| `ACFS drivers not loading` | **Reboot node** after patching |  
| `Datapatch fails` | Check `$ORACLE_BASE/cfgtoollogs/sqlpatch` logs |  
| `OPatchAuto hangs` | Kill stuck processes & retry |  

---

## **📚 References**  
- [Oracle GI Patching Guide](https://docs.oracle.com)  
- [My Oracle Support Doc 2246888.1](https://support.oracle.com)  

---

### **✅ Summary**  
1. **Pre-check** (Backup, OPatch, conflicts).  
2. **Patch Node 1** (GI → Oracle Home).  
3. **Patch Node 2** (Same as Node 1).  
4. **Post-patch** (`datapatch`, `utlrp.sql`).  
5. **Verify** (`opatch lsinventory`, `crsctl check cluster`).  

This ensures a **smooth, zero-downtime** RAC patch deployment. 🚀  

Would you like a **troubleshooting appendix** for common errors?
