# Release Notes

## v1.2.2 (May 21, 2026)

### Improvements

- Update End User License Agreement with more details with version 1.0 of the document.
- Optimized End User License Agreement formatting for improved readability in Windows installer

---

## v1.2.1 (May 18, 2026)

### Improvements

- Added release notes to public repository root for easier access to documentation
- Improved public release distribution organization

---

## v1.2.0 (May 13, 2026)

### New Features

#### Schema-Enabled Lakehouses Support
- **Full schema support**: The tool now supports data migration to schema-enabled lakehouses (resolves #1)
- Seamless handling of both traditional and schema-enabled Lakehouse destinations
- Improved file existence check logic for better accuracy

### Improvements

- Refactored `DFSAPIClient` to pass user tokens for improved authentication handling
- Enhanced `SanitizationDialog` component to default all selected files on initialization
- Fixed `overwriteEnabled` default state to 'false' and improved initialization to correctly reflect localStorage values on startup

### Bug Fixes

- Fixed issue where overwrite state wasn't properly synchronized with browser storage
- Corrected file existence check logic to work properly with schema-enabled lakehouses

---

## v1.1.1 (May 11, 2026)

### Bug Fixes

- Maintenance release with stability improvements

---

## v1.1.0 (May 11, 2026)

### New Features

#### Excel Sheet Name Specification
- **Granular control**: Users can now specify which Excel sheet names to migrate from multi-sheet workbooks
- More precise migration targeting for complex Excel files

### UI/UX Improvements

- Updated default label from "Overwrite" to "New Table" in FileUploadComponent for better clarity
- Enhanced system tray icon with increased stroke width for improved visibility on light backgrounds

---

## v1.0.8 (May 7, 2026)

### Improvements

- Enhanced version checking system with improved logging capabilities
- Implemented dynamic version fetching from remote sources
- Improved logging in version fetching and cache handling for better troubleshooting

---

## v1.0.7 (May 7, 2026)

### New Features

#### Version Checking and Notifications
- **Automatic update notifications**: Tool now checks for available updates and notifies users
- Improved version management system with better logging

### UI/UX Improvements

- Enhanced system tray icon visibility with increased stroke width for better display on light-mode operating systems

---

## v1.0.6 (May 4, 2026)

### New Features

#### Auto-Launch Browser
- System tray startup now automatically opens the browser, improving user experience
- Eliminates the need to manually navigate to localhost

#### YouTube Demo Integration
- Added embedded YouTube demo video to the landing page
- Users can now watch feature demonstrations directly in the application

---

## v1.0.5 (May 1, 2026)

### Improvements

- CSV output path handling now defaults to the system temporary directory when not explicitly specified
- Better handling of file output locations for CSV migration results

---

## v1.0.4 (May 1, 2026)

### New Features

#### Azure AD Configuration Setup
- **Interactive setup page**: Added Azure AD configuration setup page in the UI
- **Simplified .env management**: Improved `.env` file handling with guided setup
- Users can configure authentication directly from the application without manual file editing

---

## v1.0.3 (April 28, 2026)

### Improvements

- Added documentation link to navigation for easier access to help resources
- Enhanced authentication setup documentation for both Azure AD and Service Principal scenarios
- Updated installer file size references

### Bug Fixes

- Fixed copyright typo

---

## v1.0.2 (April 24, 2026)

### New Features

#### Migration History
- Track and view all previous migrations performed with detailed history records
- Backend database integration for persistent history storage
- Frontend UI component for easy access to migration records

#### System Tray Integration
- Application now includes system tray icon management
- Logo support with proper icon conversion (PNG to ICO)
- Better system integration and visibility

#### End User License Agreement (EULA)
- Comprehensive EULA included with installer
- EULA display during installation process
- Updated NSIS installer configuration

#### Feedback and Telemetry
- Added feedback option to report problems from application settings menu
- Telemetry preference management available in installer and application configuration
- User controls for data collection and privacy

### UI/UX Improvements

- Hidden console window by default in executable configuration for a cleaner user experience
- Added beta version badge to indicate application maturity
- Updated application description for clarity
- Application icon support for executable branding

---

## v1.0.1 (April 23, 2026)

### Major Changes

#### Service Principal Credentials Now Optional
- **USER CREDENTIALS FIRST**: By default, the tool now uses your Azure AD user credentials for all operations
- **SERVICE PRINCIPAL OPTIONAL**: Service principal credentials are now completely optional
- **NO FORCED CONFIGURATION**: The app no longer exits on first run requiring credential configuration
- **BACKWARD COMPATIBLE**: Existing .env files with SP credentials continue to work as before

### How It Works in v1.0.1

The authentication model is now:

1. **Default Flow** (User credentials only):
   - You log in with your Azure AD credentials in the application
   - Your credentials are used to enumerate workspaces, lakehouses, and tables
   - Your credentials are used to perform migrations
   - **No service principal setup required**

2. **Optional Service Principal Flow**:
   - Uncomment the SP credentials in `.env` file
   - If configured, SP credentials take complete precedence
   - Both enumeration and migration operations use SP credentials instead of your user credentials
   - Useful for restricted workspace access or manager-controlled deployments

3. **Hybrid Approach** (User Login + Optional SP Override):
   - Log in with your user credentials (always required for UI access)
   - If SP credentials are configured in `.env`, they override user credentials for backend operations
   - If SP credentials are not configured, user credentials are used for all operations

### Configuration Changes

#### `.env` File

The `.env` template now reflects optional credentials:

```env
# OPTIONAL: Azure AD Service Principal Credentials
# Leave commented to use your user credentials for authentication.

#AZURE_TENANT_ID=your-tenant-id-here
#AZURE_CLIENT_ID=your-client-id-here
#AZURE_CLIENT_SECRET=your-client-secret-here
```

**To enable service principal credentials:**
1. Open the `.env` file in the configuration directory:
   - Windows: `C:\Users\<username>\AppData\Roaming\OneLakeDataMigration\.env`
2. Uncomment the three credential lines
3. Fill in your service principal values
4. Save and restart the application

### New API Behavior

All enumeration endpoints now support both authentication methods:

- `/workspaces/list`
- `/lakehouses/list`
- `/tables/list`

**API Authentication**:
- If you provide an `Authorization: Bearer <token>` header with your user token, it's used directly
- If no user token is provided, the backend falls back to SP credentials (if configured)
- If neither is available, the API returns a 401 Unauthorized with helpful instructions

### Migration Behavior

The migration process respects the credential priority:
1. If user is logged in (has user token), their credentials are used
2. If SP credentials are configured in `.env`, they take precedence
3. Both scenarios allow you to perform migrations within your authorization scope

### Benefits

✅ **Simpler Setup**: New users can start immediately without configuring service principals  
✅ **Least Privilege**: Use your own credentials instead of elevated SP accounts  
✅ **Admin Control**: Organizations can optionally configure SP credentials for controlled access  
✅ **Backward Compatible**: v1.0.0 configurations work without changes  
✅ **Flexible**: Mix of user and SP credentials for different scenarios  

### Breaking Changes

⚠️ **None** - v1.0.1 is fully backward compatible with v1.0.0

Existing installations will:
- Continue to work if SP credentials are configured in `.env`
- Have SP credentials take precedence over user credentials (v1.0.0 behavior)
- Can opt into user-only workflow by commenting out SP credentials

### Migration Path from v1.0.0

**If you're using SP credentials:**
- No action required - your current setup continues to work
- SP credentials will be used as before

**If you want to switch to user-only workflow:**
1. Comment out the SP credential lines in `.env`:
   ```env
   #AZURE_TENANT_ID=...
   #AZURE_CLIENT_ID=...
   #AZURE_CLIENT_SECRET=...
   ```
2. Restart the application
3. Log in with your user credentials
4. Continue using the tool normally

### Known Limitations

- If neither user credentials nor SP credentials are available, the tool cannot enumerate workspaces
- User must have appropriate Fabric/OneLake permissions in their own account or assigned SP

### Support

For questions about authentication, credential setup, or migration issues, please refer to:
- [Configuration Guide](./docs/CONFIGURATION.md)
- [Troubleshooting Guide](./docs/TROUBLESHOOTING.md)
- [GitHub Issues](https://github.com/motio-com/Fabric-OneLake-Data-Migration-Tool/issues)

---

## v1.0.0 (April 8, 2026)

### Initial Release

The first stable release of the Fabric OneLake Data Migration Tool. This release provides a comprehensive solution for migrating data to Microsoft Fabric's OneLake platform.

### Core Features

#### Data Migration
- Migrate data from local CSV and Excel files to OneLake lakehouses
- Support for multiple file formats and encodings
- Batch file processing capabilities

#### Workspace Management
- Browse and select from available Fabric workspaces
- View available lakehouses within selected workspace
- Table enumeration and selection

#### Authentication
- Azure AD user login with MSAL integration
- Service Principal authentication support
- Dual-token support for Fabric and DFS operations
- OAuth2 authorization flow

#### File Management
- Drag-and-drop file upload interface
- Multiple file and folder selection
- File sanitization for data quality
- CSV delimiter auto-detection
- Excel file format support
- Duplicate file handling

#### User Interface
- Modern React-based web interface built with Vite
- Real-time migration status tracking
- Progress indicators for file uploads
- Health check endpoint for server connectivity
- Responsive design for various screen sizes

#### Data Sanitization
- CSV sanitization capabilities
- Excel sheet support
- Data validation options

#### Backend Infrastructure
- FastAPI-based REST API
- SQLite database for job tracking
- Asynchronous migration worker
- Logging and error handling
- Environment configuration management

#### Deployment
- PyInstaller-based executable packaging
- NSIS installer for Windows deployment
- System tray application integration
- Self-contained distribution

### Technology Stack

**Frontend:**
- React 18
- Vite
- MSAL for authentication
- Modern CSS with responsive design

**Backend:**
- Python 3.9+
- FastAPI
- SQLAlchemy for database operations
- Azure SDK for Fabric/DFS interactions

**Deployment:**
- PyInstaller for packaging
- NSIS for Windows installer
- GitHub Actions for CI/CD

### Known Limitations

- Windows-only installation (via NSIS installer)
- Service Principal credentials required for backend operations in early versions (made optional in v1.0.1)
- Single-threaded migration processing

### Architecture

- **Frontend**: Web-based UI running on localhost
- **Backend**: Python API server with async processing
- **Storage**: Local SQLite database for state management
- **Authentication**: Azure AD with OAuth2 flow
