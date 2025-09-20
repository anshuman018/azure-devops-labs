# Azure Blob Storage Lab - Direct File Download

## Objective
Create a blob container, upload a ZIP file, enable anonymous access, and generate a direct download URL.

---

## Step 1: Create Storage Account

1. Go to **Azure Portal** → **Create Resource** → **Storage Account**
2. Fill basic details:
   - **Name**: `bloblab[yourname]` (must be unique)
   - **Region**: Choose nearest
   - **Performance**: Standard
   - **Redundancy**: LRS
3. **Important**: In **Advanced** tab, enable **"Allow Blob anonymous access"**
4. Click **Create**

---

## Step 2: Create Container

1. Go to your Storage Account → **Containers**
2. Click **+ Container**
3. Set:
   - **Name**: `downloads`
   - **Anonymous access level**: **Blob (anonymous read access for blobs only)**
4. Click **Create**

---

## Step 3: Upload ZIP File

1. Open your `downloads` container
2. Click **Upload**
3. Select your ZIP file
4. Click **Upload**

> **Don't have a ZIP file?** Create one with any files (documents, images, etc.)

---

## Step 4: Get Download URL

1. Click on your uploaded ZIP file
2. Copy the **URL** from the Overview tab

URL format:
```
https://[storageaccountname].blob.core.windows.net/downloads/[filename].zip
```

---

## Step 5: Test Download

1. Open a new browser tab
2. Paste the URL
3. Press Enter
4. File should automatically download

---

## Expected Result

✅ **Success**: Browser downloads the ZIP file immediately when you visit the URL

---

## Troubleshooting

**Access Denied Error?**
- Check anonymous access is enabled on storage account
- Verify container access level is set to "Blob"

**File opens instead of downloads?**
- Right-click URL → "Save link as"

**404 Error?**
- Check URL spelling
- Verify file uploaded successfully

---

## Submission

Provide:
1. **Storage Account Name**: `_________________`
2. **Download URL**: `_________________`
3. **Screenshot**: Successful download

---

## Bonus: Custom Download Name

Add this to your URL to force custom filename:
```
?response-content-disposition=attachment;filename=CustomName.zip
```

Example:
```
https://yourstorage.blob.core.windows.net/downloads/file.zip?response-content-disposition=attachment;filename=MyProject.zip
```
