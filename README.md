# Fixing SSH Private Key Permission Issues on Windows

## Overview
When connecting to an EC2 instance using SSH on Windows, you may encounter an error due to improper file permissions on your private key (`.pem` file). This guide walks you through fixing those permissions to establish a successful SSH connection.

## Error Message Example
```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
Permissions for 'C:\path\to\your-key.pem' are too open.
It is required that your private key files are NOT accessible by others.
This private key will be ignored.
Load key "C:\path\to\your-key.pem": bad permissions
Permission denied (publickey).
```

## Step-by-Step Solution

### 1️⃣ **Open PowerShell as Administrator**
1. Press `Win + X` and select **PowerShell (Admin)**.
2. Navigate to the directory where your `.pem` file is stored:
   ```powershell
   cd "C:\path\to\your-key-folder"
   ```

### 2️⃣ **Remove Inherited Permissions**
Run the following command to remove inherited permissions:
```powershell
icacls "your-key.pem" /inheritance:r
```

### 3️⃣ **Remove Unauthorized Users**
To remove permissions for unwanted users (like `Authenticated Users` or `Everyone`), run:
```powershell
icacls "your-key.pem" /remove "Authenticated Users" "Everyone" "BUILTIN\Users"
```

### 4️⃣ **Grant Read-Only Access to Your User**
```powershell
icacls "your-key.pem" /grant:r "%USERNAME%:R"
```

### 5️⃣ **Verify Correct Permissions**
Check the current permissions by running:
```powershell
icacls "your-key.pem"
```
You should see output similar to this:
```
your-key.pem YOUR-PC\YourUsername:(R)
```
No other users should have access.

### 6️⃣ **Attempt SSH Connection Again**
Now, try connecting to your EC2 instance:
```powershell
ssh -i "C:\path\to\your-key.pem" ubuntu@your-ec2-instance.amazonaws.com
```

## Conclusion
These steps ensure your private key has the correct permissions, preventing SSH from rejecting it. You should now be able to connect securely to your EC2 instance without any permission errors.

### ✅ **Troubleshooting**
- **Error: File Not Found?** Double-check the file path.
- **Still getting `bad permissions`?** Re-run all `icacls` commands carefully.
- **Using Git Bash?** Try this instead:
  ```bash
  chmod 400 /c/path/to/your-key.pem
  ```

This guide helps securely configure SSH private key permissions on Windows. 🚀

