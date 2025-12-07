# S3 Versioning & Delete Markers  

## Topics Covered
- What is Versioning
- Why Versioning is used
- Key name & versions
- How Versioning behaves on upload
- Delete Markers (what happens on delete)
- Restoring deleted objects (remove delete marker)
- Making an old version the current version
- Keeping both versions (A and B) together
- Permanently deleting specific versions
- Important behaviors from hands-on

---

## 1. What is Versioning

Versioning allows a bucket to keep multiple versions of an object (same key name).  
When you upload the same key (same file name) again, S3 creates **a new version** instead of overwriting the old one.

Example:

- Bucket: `my-app-bucket`
- Key (object name): `s3.txt`
- Upload #1 → Version 1
- Upload #2 (same key) → Version 2 (both exist)

---

## 2. Why Versioning is Used

- To recover from accidental overwrites  
- To recover from accidental deletes  
- To keep history of changes  
- To support features like replication & MFA delete  

Without versioning, overwrites and deletes are final.

---

## 3. Key Name and Versions

- **Key name**: the full object path/name, e.g.:
  - `s3.txt`
  - `reports/2025/Jan/invoice.pdf`
- **Version ID**: internal unique ID S3 assigns when versioning is enabled.

Under one key (e.g. `s3.txt`), you can have many versions:
- v1, v2, v3, …

---

## 4. How Versioning Behaves on Upload 

### Step 1 — Enable Versioning
- Go to: Bucket → **Properties** → **Bucket Versioning**
- Set to **Enabled**

### Step 2 — First upload
- Upload `s3.txt`
- This becomes **Version 1 (current)**

### Step 3 — Second upload (same key)
- Upload another `s3.txt` (with different content)
- Now S3 has:
  - Version 2 → **current**
  - Version 1 → **non-current**

In console, when you enable **Show versions**, you see both versions listed under `s3.txt`.

---

## 5. Delete Markers (What Happens When You Delete)

With versioning enabled, when you delete `s3.txt` from the **Objects** view:

- S3 does **not** delete the actual data.
- It creates a **delete marker** as the latest “version”.
- The object disappears from normal view.
- Older versions stay in storage.

So after delete, under `s3.txt` you will see (with Show versions ON):

- `Delete marker` (latest)
- `Version 2`
- `Version 1`

The delete marker hides the object in normal listing.

---

## 6. Restoring a Deleted Object (Removing Delete Marker)

To restore a “deleted” object:

1. Go to the bucket.
2. Turn on **Show versions**.
3. Find your object (e.g. `s3.txt`).
4. You will see a `Delete marker` as the latest entry.
5. Select the **delete marker**.
6. Click **Delete** and confirm.

Result:
- Delete marker is removed.
- The previous latest version (e.g. Version 2) becomes **current**.
- `s3.txt` appears again in normal object view.

**Key idea:**  
Deleting the delete marker **restores** the object.

---

## 7. Making an Old Version the Current Version

learn **two important ideas** here:

### 7.1 Concept: S3 always keeps all versions

If you have:

- Version 3 (latest)
- Version 2
- Version 1

You **already have** both “old” and “new” versions.  
The “current” one is just whichever version is on top (latest).  
The others are still available under “Show versions”.

### 7.2 How to bring back an old version as the current version

Scenario:  
- Version 3 has wrong content  
- Version 2 is the correct content  
You want Version 2 to become the **new current** version.

**Method (Console, practical way we discussed):**

1. Turn on **Show versions**.
2. Click the **old version** (e.g. Version 2 of `s3.txt`).
3. **Download** that old version to your machine.
4. Go back to **Objects** tab.
5. Upload that file again as `s3.txt` (same key name).

Now S3 will:

- Create a **new version** (Version 4) with Version 2 content.
- Version 3 becomes non-current.
- All versions (1, 2, 3, 4) are preserved.

So after this:
- Version 4 → current (restored content from Version 2)
- Version 3 → non-current
- Version 2 → non-current
- Version 1 → non-current

You have:
- **Old content restored as current**
- **Old and new versions still stored**

---

## 8. Keeping Both Versions (A and B) at the Same Time

There are **two ways** to “have both A and B”.

### 8.1 Both versions under the same key (normal versioning behavior)

Example:
- Version A → one content
- Version B → another content

When you upload B over A (same key), you do **not** lose A.

S3 now has:
- Latest version (B)  
- Older version (A)  

You already “have both A and B” under one key.  
You can always download any version from the **Versions** tab.

If you “restore A” by re-uploading its content, then S3 will store **A again as a new version** — still keeping B in history.

### 8.2 Having A and B as separate visible objects

If you want two **separate keys** (two objects in Objects view), e.g.:

- `fileA.txt`
- `fileB.txt`

Then:

1. Download the old version (A).
2. Upload it as a **different key name**, e.g. `s3-old.txt`.

Now your bucket has:

- `s3.txt` (with latest version)
- `s3-old.txt` (fixed older version as a separate object)

This is how you keep both explicitly visible in the object list.

---

## 9. Permanently Deleting a Specific Version

### Deleting a version:
- Go to **Show versions**
- Select a specific version (not the delete marker)
- Click **Delete**
- Confirm permanent delete

Result:
- That version is removed from S3.
- Other versions are unaffected.
- If you delete the **current** version directly from versions view, S3 will mark another existing version as latest (if any), or object may be effectively gone if no versions remain.

### Deleting all versions:
If you delete **all** versions + delete marker:
- Object is completely gone from S3.
- There is nothing to restore.

---

## 10. Summary of Behaviors (from Hands-On)

- Upload same key with versioning ON → S3 stores multiple versions.
- Delete an object with versioning ON → S3 creates a **delete marker**, does not delete underlying data.
- Restore deleted object → delete the delete marker.
- Make old version current again → download old version and upload it again with same key.
- Have both versions A & B:
  - S3 ALWAYS has both internally as versions.
  - Optionally, upload old version as a separate key to see both in object list.
- Permanent cleanup → delete specific versions from **Show versions**.

Versioning + Delete Markers together give you:
- Safety from overwrite
- Safety from delete
- Control over which content is current
- Ability to keep or clean history as needed

---

