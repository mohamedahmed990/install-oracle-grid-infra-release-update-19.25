Here’s a neatly **ordered and structured patching plan** that follows the correct **logical and chronological flow**:

---

## ✅ **Oracle Grid Infrastructure & RAC DB Patching Plan**

---

### 🔧 **1. Pre-Patching Preparation**

#### 1.1 **OPatch Utility Requirements**
- Required version: **12.2.0.1.43** or later
- Download from: [Patch 6880880](https://support.oracle.com/epmos/faces/PatchDetail?patchId=6880880)
- Extract and install in **each Oracle home** (GI and DB) as the **home owner**
- Check `README.txt` for installation steps
- Version 12.2.0.1.37+ cleans up **inactive patches**

#### 1.2 **Validate Oracle Inventory**
Run this for each home:
```bash
$ORACLE_HOME/OPatch/opatch lsinventory -detail -oh $ORACLE_HOME
```

---

### 📦 **2. Patch Setup and Checks**

#### 2.1 **Download & Extract Patch**
- Download **Patch 36916690**
- Extract to a **shared location** (not `/tmp`)
- Ensure it's **readable by `ORA_INSTALL` group**:
```bash
unzip p36916690_190000_<platform>.zip
```

#### 2.2 **Conflict Check**
Run for each sub-patch:
```bash
$ORACLE_HOME/OPatch/opatch prereq CheckConflictAgainstOHWithDetail -phBaseDir /patch/36916690/<subpatch_id>
```
> Run all 5 sub-patches for **GI**, only 2 for **DB**

#### 2.3 **System Space Check**
**Create file list:**

- For GI:
```bash
cat <<EOF > /tmp/patch_list_gihome.txt
/patch/36916690/36912597
/patch/36916690/36917416
/patch/36916690/36917397
/patch/36916690/36940756
/patch/36916690/36758186
EOF
```

- For DB:
```bash
cat <<EOF > /tmp/patch_list_dbhome.txt
/patch/36916690/36912597
/patch/36916690/36917416
EOF
```

**Check space:**
```bash
$ORACLE_HOME/OPatch/opatch prereq CheckSystemSpace -phBaseFile /tmp/patch_list_gihome.txt
```

---

### 🔍 **3. Cluster Readiness Checks**

#### 3.1 **Pre-Patch Check**
```bash
cluvfy stage -pre patch -n <node_list> -rolling
```

> Use CVU from GI home or standalone [Patch 30839369](https://support.oracle.com/epmos/faces/PatchDetail?patchId=30839369)

---

### ⚙️ **4. Patching Process Options**

#### 4.1 **OPatchAuto Utility Overview**
- Automates patching for **Grid** and **RAC DB homes**
- Must be run as **root**
- Supports **in-place**, **out-of-place**, and **parallel** patching

**Add OPatchAuto to PATH:**
```bash
export PATH=$PATH:<GI_HOME>/OPatch
cd <UNZIPPED_PATCH_LOCATION>
```

#### 4.2 **Common OPatchAuto Commands**

| Operation                     | Command Example |
|------------------------------|-----------------|
| Patch Grid + RAC DB homes    | `opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690` |
| Patch Grid home only         | `opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690 -oh <GI_HOME>` |
| Patch RAC DB home(s) only    | `opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690 -oh <oracle_home1>,<oracle_home2>` |
| Rollback Grid + RAC DB homes | `opatchauto rollback <UNZIPPED_PATCH_LOCATION>/36916690` |
| Rollback Grid home only      | `opatchauto rollback <UNZIPPED_PATCH_LOCATION>/36916690 -oh <GI_HOME>` |
| Rollback RAC DB home(s) only | `opatchauto rollback <UNZIPPED_PATCH_LOCATION>/36916690 -oh <oracle_home1>,<oracle_home2>` |

---

### 🔁 **5. Patching Execution by Use Case**

#### 🔹 **Case 1: RAC with Non-shared Grid and DB Homes**
Run on **each node**:
```bash
# <GI_HOME>/OPatch/opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690
```

---

#### 🔹 **Case 2: RAC with Shared DB Home and ACFS**
1. Stop DB:
```bash
$ <ORACLE_HOME>/bin/srvctl stop database -d <db_unique_name>
```
2. Unmount ACFS (first node only)
3. Patch GI (first node):
```bash
# <GI_HOME>/OPatch/opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690 -oh <GI_HOME>
```
4. Reboot if required
5. Remount ACFS
6. Patch DB:
```bash
# <GI_HOME>/OPatch/opatchauto apply <UNZIPPED_PATCH_LOCATION>/36916690 -oh <ORACLE_HOME>
```
7. Start instance:
```bash
$ <ORACLE_HOME>/bin/srvctl start instance -d <db_unique_name> -n <node_name>
```
8. Repeat steps 2–7 for other nodes

---

#### 🔹 **Case 3: Single-instance DB (Non-GI)**
1. Shut down all instances and listeners
2. Apply sub-patch:
```bash
cd <UNZIPPED_PATCH_LOCATION>/36916690/36912597
opatch apply
```

---

### 🛡️ **6. Data Guard — Standby-First Patching**

> See Doc ID [1265700.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=1265700.1)

1. Apply patch **36912597** to **standby** using `opatch apply`
2. **Do NOT run `datapatch` on standby**
3. Apply to **primary**, then run:
```bash
$ORACLE_HOME/OPatch/datapatch
```

---

### 🚀 **7. Post-Patching Activities**

#### 7.1 **Post-Patch CVU Check**
```bash
cluvfy stage -post patch -n <node_list> -rolling
```

#### 7.2 **Apply Conflict Resolution Patches**
Refer to **Section 2.1.8.1** if applicable

#### 7.3 **Manual SQL Patch Loading (if no OPatchAuto)**
See **Section 2.1.8.2**

#### 7.4 **Upgrade RMAN Catalog**
See **Section 2.1.8.3**

#### 7.5 **Review Optimizer Plan Changes**
May require performance plan evaluation — see **Section 2.1.8.4**

---

### 🧰 **8. Reference Tools & Documentation**

- [1091294.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=1091294.1): Conflict Checker Tool  
- [1061295.1](https://support.oracle.com/epmos/faces/DocumentDisplay?id=1061295.1): Patch Conflict Resolution  
- [603505.2](https://support.oracle.com/epmos/faces/DocumentDisplay?id=603505.2): Oracle Patch How-to Videos  

---

Let me know if you want this in **Markdown**, **Word**, **PDF**, or a **shell script summary** for practical use!
