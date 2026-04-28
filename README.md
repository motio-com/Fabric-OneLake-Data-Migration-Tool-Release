# Fabric Lakehouse Data Migration Tool

A powerful Windows desktop application for migrating CSV and Excel files into Microsoft Fabric Lakehouse with ease.

**Features:**
- 📊 Migrate CSV and Excel files to Fabric Lakehouse
- 🔄 Real-time migration progress tracking
- 🖥️ User-friendly web-based interface
- 🔐 Secure Azure AD authentication
- ⚙️ Service principal support for automated workflows
- 📈 Batch file processing capabilities

---

## 🚀 Quick Start

### Installation

1. **Download the installer**
   - Get `OneLakeDataMigration-latest-installer.exe`

2. **Run the installer**
   - Double-click the `.exe` file
   - Follow the installation wizard
   - The app will be installed to Program Files
   - Desktop and Start Menu shortcuts will be created

3. **Launch the application**
   - Click the desktop shortcut or search for "OneLake Data Migration" in Start Menu
   - The app will start with both backend API and web interface
   - Right click on the system tray "Motio" icon and select "Open"

4. **Sign in**
   - Click "Sign In" button
   - Authenticate with your Microsoft account
   - Grant permissions when prompted

5. **Start migrating**
   - Select your Fabric workspace and lakehouse
   - Upload CSV or Excel files
   - Configure table loading options
   - Click "Start Migration" and monitor progress

---

## 🔧 Initial Setup

### 1. Register Your Application in Azure AD

Follow these steps to register the application in Azure AD:

#### Required Permissions:

The application needs the following permissions from your Azure AD administrator:

```
Azure Storage
├── user_impersonation (Delegated)

Power BI Service
├── Dataset.ReadWrite.All (Delegated)
├── Item.ReadWrite.All (Delegated)
└── Lakehouse.ReadWrite.All (Delegated)
```

#### Detailed Registration Steps:

1. **Go to Azure Portal**
   - Navigate to [portal.azure.com](https://portal.azure.com)
   - Sign in with your Azure AD admin account

2. **Access App Registrations**
   - Click "Azure Active Directory" in the left sidebar
   - Click "App registrations"
   - Click "+ New registration"

3. **Register the Application**
   - **Name**: `Fabric Lakehouse Data Migration Tool` (or your preferred name)
   - **Supported account types**: Select "Accounts in this organizational directory only"
   - **Redirect URI**: Platform: `Web`, URI: `https://localhost:8000`
   - Click "Register"

4. **Configure Permissions**
   - From the app registration page, click "API permissions"
   - Click "+ Add a permission"

5. **Add Azure Storage Permission**
   - Select "Azure Storage" from the list
   - Click "Delegated permissions"
   - Check ☑️ `user_impersonation`
   - Click "Add permissions"

6. **Add Power BI Service Permissions**
   - Click "+ Add a permission" again
   - Search for "Power BI Service"
   - Select it from the results
   - Click "Delegated permissions"
   - Check the following permissions:
     - ☑️ `Dataset.ReadWrite.All`
     - ☑️ `Item.ReadWrite.All`
     - ☑️ `Lakehouse.ReadWrite.All`
   - Click "Add permissions"

7. **Grant Admin Consent** (if required)
   - After adding permissions, click "Grant admin consent for [Your Organization]"
   - Click "Yes" to confirm
   - A green checkmark will appear next to each permission

8. **Get Your Credentials**
   - From the app registration page, copy the **Application (client) ID**
   - From the left sidebar, click "Certificates & secrets"
   - Click "+ New client secret"
   - Set expiration (e.g., 12 months)
   - Click "Add"
   - **Copy the secret value immediately** (you won't be able to see it again)

9. **Get Your Tenant ID**
   - From the left sidebar, click "Overview"
   - Copy the **Directory (tenant) ID**

---

## ⚙️ Configuration

### User Authentication (Required)

The application uses your personal Microsoft account for login and file operations.

**Required Setup:**

1. **Configure User Credentials in .env**
   - Press `Win + R`
   - Type: `%APPDATA%\OneLakeDataMigration`
   - Press Enter
   - Open the `.env` file with Notepad

2. **Fill in Your Azure AD Application Details**
   ```
   AZURE_TENANT_ID=your-tenant-id-here
   AZURE_CLIENT_ID=your-client-id-here
   ```

3. **Get Your Values From Azure Portal**
   - Go to Azure Portal → Azure Active Directory → App registrations
   - Select your registered application
   - **Client ID**: Copy from "Application (client) ID" field
   - **Tenant ID**: Copy from "Directory (tenant) ID" field

4. **Save and Restart**
   - Press `Ctrl + S` to save
   - Close and reopen the application
   - Click "Sign In" with your Microsoft account

### Service Principal (Optional - Advanced)

For automated migrations or unattended operations, you can configure the backend to use a **Service Principal** instead of your personal credentials.

**Why use a Service Principal?**
- Unattended/automated migrations
- No individual user sign-in required
- Separate credentials from personal account
- Better for scheduled tasks

#### Setup Service Principal:

1. **Create Service Principal Credentials**
   - In Azure Portal, go to your app registration
   - Click "Certificates & secrets" (left sidebar)
   - Click "+ New client secret"
   - Set expiration and description
   - Copy the **secret value immediately** (can't view again)

2. **Grant Service Principal Access to Lakehouse**
   - In Microsoft Fabric, open your workspace
   - Go to "Workspace settings" → "Members"
   - Click "Add service principal or user" (or similar)
   - Search for your app by name
   - Grant necessary permissions (Contributor or Admin)

#### Configure Service Principal in .env:

1. **Open the .env file** (same as above)
   - `Win + R` → `%APPDATA%\OneLakeDataMigration`
   - Open `.env` with Notepad

2. **Uncomment and Fill Service Principal Section**
   ```
   # Uncomment and fill these lines:
   #SP_TENANT_ID=your-service-principal-tenant-id-here
   #SP_CLIENT_ID=your-service-principal-client-id-here
   #SP_CLIENT_SECRET=your-service-principal-client-secret-here
   ```

3. **Add Your Values**
   - **SP_TENANT_ID**: Same as AZURE_TENANT_ID
   - **SP_CLIENT_ID**: Your app's Client ID from Azure Portal
   - **SP_CLIENT_SECRET**: The secret value you copied (NOT the ID)

4. **Save and Restart Backend**
   - Press `Ctrl + S` to save
   - Restart the backend service
   - Backend will now use service principal credentials for API operations

**Important Notes:**
- User Authentication (AZURE_*) is always required for login
- Service Principal (SP_*) is optional and only for backend API operations
- If both are configured: frontend uses user credentials for login, backend uses service principal for API calls
- If only user credentials are configured: everything uses your personal account

#### Finding Service Principal Values:

- **Tenant ID**: Azure Portal → Azure AD → Properties → Directory ID (same as AZURE_TENANT_ID)
- **Client ID**: Azure Portal → App Registrations → Your App → Application ID
- **Client Secret**: Azure Portal → App Registrations → Your App → Certificates & secrets → Value column

---

## 📋 Usage Guide

### Basic Migration Workflow

1. **Sign In**
   - Launch the application
   - Click "Sign In"
   - Authenticate with your Microsoft account or use service principal credentials

2. **Select Target**
   - Choose your **Fabric Workspace** from the dropdown
   - Choose your **Lakehouse** from the dropdown
   - Click "Connect"

3. **Upload Files**
   - Click "Select Files" or drag & drop files
   - Supported formats: CSV, XLSX (Excel)
   - Multiple files can be uploaded at once

4. **Configure Tables**
   - For each file, configure:
     - **Table Name**: Name for the table in Lakehouse
     - **Enable Schema Detection**: Auto-detect column types
     - **Delimiter** (CSV only): Specify column separator (comma, semicolon, pipe, tab)

5. **Review and Start**
   - Review all configurations
   - Click "Start Migration"
   - Monitor real-time progress in the progress panel

6. **View Results**
   - Tables will appear in Microsoft Fabric Lakehouse
   - Click "View in Fabric" to open your workspace

### Advanced Features

**Application Settings** (⚙️ icon in top-right):
- **Enable Debug Logging**: Detailed error information for troubleshooting
- **View Application Logs**: Real-time logs of all operations
- **Report a Problem**: Submit GitHub issue with auto-included diagnostics

**Keyboard Shortcuts**:
- `F5` - Refresh workspace connection
- `Ctrl + L` - Clear application logs
- `Ctrl + ,` - Open settings

---

## 🔒 Security & Best Practices

### User Authentication
- Uses standard OAuth 2.0 with Azure AD
- Tokens are cached locally and automatically refreshed
- Your credentials are never stored in the application
- Sign out to clear all cached authentication data

### Service Principal Authentication
- Ideal for automated migrations and scheduled tasks
- Credentials stored in AppData folder
- **Protect your `.env` file** - treat it like a password
- Tokens are automatically rotated
- Rotate client secrets regularly (every 6-12 months)

### File Security
- Files are uploaded directly to Microsoft Fabric OneLake
- No files are stored on the application server
- Encrypted in transit via HTTPS
- Access controlled by Fabric workspace permissions

### Recommendations

1. **Use User Authentication** for manual, one-time migrations
2. **Use Service Principal** only for recurring automated workflows
3. **Protect Service Principal Credentials**
   - Don't share your `.env` file
   - Rotate secrets regularly
   - Use strong client secrets
4. **Limit Permissions**
   - Only grant the required permissions listed above
   - Don't use admin credentials for service principals
5. **Monitor Activity**
   - Check Fabric audit logs for suspicious activity
   - Enable debug logging if issues occur

---

## 🆘 Troubleshooting

### Common Issues

**"Sign in failed" or "Authentication error"**
- Verify your Tenant ID, Client ID are correct
- Check that the application is registered in your Azure AD
- Ensure redirect URI is set to `http://localhost:3000`
- Administrator may need to grant consent to permissions

**"Lakehouse not found"**
- Verify you have access to the workspace
- Check that the lakehouse exists
- Service principal may need to be added as workspace member

**"Permission denied" when uploading files**
- Verify your Lakehouse has write permissions
- Check that your role in the workspace is Contributor or higher
- Service principal needs Contributor role in workspace

**"File upload failed"**
- Check file size (recommend under 1 GB)
- Ensure file is valid CSV or Excel format
- Check your internet connection
- Try uploading a smaller test file first

**Slow performance or timeouts**
- Reduce file size or batch size
- Check your internet connection speed
- Close other applications using bandwidth
- Enable Debug Logging to see detailed operation times

### Get Help

1. **View Application Logs**
   - Click ⚙️ Settings (top-right)
   - Click "Application Logs"
   - Look for error messages
   - Set log level to DEBUG for more details

2. **Check System Requirements**
   - Windows 10 or later (64-bit)
   - 4 GB RAM minimum, 8 GB recommended
   - Stable internet connection
   - Modern web browser (Chrome, Edge, Firefox)

3. **Report Issues**
   - Click ⚙️ Settings → "Report a Problem"
   - Or visit the GitHub repository
   - Include relevant log entries from Application Logs

---

## 🌐 System Requirements

| Requirement | Minimum | Recommended |
|------------|---------|------------|
| **OS** | Windows 10 (64-bit) | Windows 11 |
| **RAM** | 4 GB | 8 GB |
| **Disk Space** | 60 MB | 100 <B> |
| **Internet** | Broadband connection | High-speed connection |
| **Browser** | Edge, Chrome, Firefox | Latest version |

---

## 📚 Additional Resources

- **[Azure AD App Registration Guide](https://learn.microsoft.com/en-us/azure/active-directory/develop/quickstart-register-app)**
- **[Microsoft Fabric Documentation](https://learn.microsoft.com/en-us/fabric/)**
- **[Power BI Service Permissions Guide](https://learn.microsoft.com/en-us/power-bi/enterprise/service-admin-role)**
- **[GitHub Repository](https://github.com/[your-org]/fabric-lakehouse-data-migration-tool)**

---

## 📝 Version Information

**Current Version**: 1.0.3  
**Last Updated**: April 2026  
**License**: See LICENSE.txt included in the installation

---

## 💬 Support

For issues, questions, or feature requests:
- Create an issue on GitHub
- Check existing GitHub issues for solutions
- Review Application Logs for error details (⚙️ Settings → Application Logs)

---

## ©️ License

This software is provided under the terms of the End User License Agreement (EULA) included with the installation. By using this software, you agree to the terms and conditions outlined in the EULA.

---

**Made with ❤️ for Microsoft Fabric enthusiasts**
