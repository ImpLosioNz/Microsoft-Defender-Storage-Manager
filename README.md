# Defender Storage Manager

Web application for managing file uploads to Azure Blob Storage with Microsoft Defender for Storage integration. Upload files, monitor antivirus scans, and manage your storage.

## 🎯 Features

- **📤 File Upload**: Drag & drop or click to upload files to Azure Blob Storage
- **🔍 Antivirus Scanning**: Automatic Microsoft Defender for Storage scanning
- **📋 File Browser**: View all uploaded files with scan status
- **⏱️ Activity Log**: Complete operation history with timestamps and performance metrics
- **📊 Real-time Monitoring**: Track upload speed, scan duration, and file size
- **💾 File Management**: Download and delete files directly from the interface
- **🎨 Cyberpunk UI**: Dark theme with neon green/blue aesthetics
- **💾 Config Persistence**: Automatically saves your Azure settings locally
- **⚡ Performance Tracking**: Measures upload, scan, and list operation times

## 🛠️ Prerequisites

Before using this application, you need:

1. **Azure Account** with:
   - Storage Account created
   - Blob Container set up
   - Microsoft Defender for Storage enabled
   - Proper CORS configuration

2. **Modern Web Browser** supporting:
   - ES6 JavaScript
   - Fetch/Axios
   - LocalStorage API

## 🚀 Quick Start

### 1. Azure Setup

#### Create Storage Account
1. Go to [Azure Portal](https://portal.azure.com)
2. Create a new Storage Account (if you don't have one)
3. Note your **Storage Account Name**

#### Create Container
1. Go to your Storage Account → Containers
2. Create a new container (e.g., `uploads`)
3. Note the **Container Name**

#### Enable Defender for Storage
1. Go to Storage Account → Defender Plans
2. Enable **Microsoft Defender for Storage**
3. This will automatically scan uploaded files for malware

#### Generate SAS Token
1. Storage Account → Shared Access Signature
2. Select permissions:
   - ✅ Blob
   - ✅ Container
   - ✅ List
   - ✅ Read
   - ✅ Write
   - ✅ Delete
   - ✅ Create
3. Set expiration date (e.g., 1 year)
4. Click **Generate SAS and connection string**
5. Copy the **SAS token** (the part that starts with `sv=2024-...`)

#### Configure CORS
1. Storage Account → Resource sharing (CORS)
2. Click on **Blob service**
3. Add the following configuration:

```
Allowed origins: *
Allowed methods: GET, PUT, HEAD, POST, DELETE, OPTIONS
Allowed headers: *
Exposed headers: x-ms-blob-type,x-ms-meta-malware-status,x-ms-meta-scan-status,date,x-ms-version,content-md5,x-ms-request-id
Max age: 86400
```

4. Click **Save**

### 2. Using the Application

1. **Open** `index.html` in your web browser
2. **Fill in Configuration**:
   - Storage Account Name: `youraccountname`
   - Container Name: `uploads`
   - SAS Token: `sv=2024-...` (paste the full token)
3. **Upload File**:
   - Click upload area or drag & drop a file
   - Click "Upload & Scan" button
4. **Monitor**:
   - Check File Browser tab for scan status
   - View Activity Log for operation history
5. **Manage Files**:
   - Download files using the download button
   - Delete files using the delete button

## 🔍 Understanding Scan Status

The application displays three scan statuses for each file:

| Status | Badge Color | Meaning |
|--------|-------------|---------|
| **Clean ✓** | 🟢 Green | File passed antivirus scan - safe to use |
| **Infected ✗** | 🔴 Red | Malware detected - file is quarantined |
| **Scanning** | 🟡 Yellow | Antivirus scan still in progress |

## 📊 Activity Log Details

The Activity Log (⏱️ tab) shows:

- **Operation Type**: Upload, Scan, Download, Delete
- **File Name**: Which file the operation involved
- **Duration**: How long the operation took (e.g., 2.45s)
- **Timestamp**: Date and time of operation
- **Status**: Success, Error, or In-Progress

Example entries:
```
📤 Uploading File: document.pdf (1.23 MB)
✅ Upload Complete: document.pdf - 2.15s
🔍 Scan Initiated: document.pdf
✅ Scan Complete: document.pdf - Clean ✓
```

## 🔐 Security Best Practices

1. **SAS Token Security**:
   - Don't share your SAS token publicly
   - Set reasonable expiration dates (1 year max)
   - Regenerate tokens regularly
   - The token is stored locally - only you have access

2. **Container Permissions**:
   - Consider private containers instead of public
   - Use SAS tokens with limited scopes for different use cases

3. **Defender Integration**:
   - Keep Defender for Storage enabled
   - Review scan results regularly
   - Quarantine infected files

## 🐛 Troubleshooting

### "Network Error" when uploading

**Problem**: Upload fails with network error
**Solution**: 
1. Check CORS configuration (see Azure Setup section)
2. Wait 2-3 minutes for CORS settings to apply
3. Clear browser cache (Ctrl+Shift+Delete)
4. Try a different browser

### Files not appearing in list

**Problem**: Uploaded files don't show in File Browser
**Solution**:
1. Click "Refresh" button
2. Verify container name is correct
3. Check SAS token hasn't expired
4. Try uploading a new test file

### Scan status shows "Scanning" for too long

**Problem**: Files stuck in "Scanning" status
**Solution**:
1. This is normal for large files (5+ MB)
2. Defender may take up to 5-10 minutes
3. Refresh the page to see updated status
4. Close and reopen the browser if it persists

### Configuration not saving

**Problem**: Settings are cleared on refresh
**Solution**:
1. Browser may have localStorage disabled
2. Try a different browser
3. Check if private/incognito mode is enabled

## 📈 Performance Tips

1. **Optimal File Sizes**:
   - Small files (<10 MB): Scan completes within 30 seconds
   - Medium files (10-100 MB): 1-5 minutes
   - Large files (>100 MB): 10+ minutes

2. **Batch Operations**:
   - Upload one file at a time
   - Wait for scan to complete before next upload

3. **Browser Performance**:
   - Close unnecessary tabs
   - Use a modern browser (Chrome, Firefox, Edge)
   - Clear browser cache regularly

## 🔧 API Integration

If you want to integrate Defender Storage Manager with other systems:

### Using curl to upload files

```bash
STORAGE_ACCOUNT="youraccountname"
CONTAINER="uploads"
SAS_TOKEN="sv=2024-..."
FILE="document.pdf"

curl -X PUT \
  -H "x-ms-blob-type: BlockBlob" \
  -d @"$FILE" \
  "https://${STORAGE_ACCOUNT}.blob.core.windows.net/${CONTAINER}/${FILE}?${SAS_TOKEN}"
```

### Using PowerShell

```powershell
$storageAccount = "youraccountname"
$container = "uploads"
$sasToken = "sv=2024-..."
$filePath = "C:\path\to\file.pdf"

$blobUrl = "https://${storageAccount}.blob.core.windows.net/${container}/$(Split-Path $filePath -Leaf)?${sasToken}"

Invoke-WebRequest -Uri $blobUrl -Method PUT -InFile $filePath -Headers @{"x-ms-blob-type"="BlockBlob"}
```

## 📝 Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+Shift+Delete` | Clear browser cache |
| `F12` | Open developer console for debugging |

## 🤝 Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request
