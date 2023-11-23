### Installing Oracle Database on Ubuntu

1. **Download Oracle Database Software**:
   - Visit the official Oracle Technology Network (OTN) or Oracle Software Delivery Cloud website to download the Oracle Database installation files for Ubuntu.

#### Step 1: Install Dependencies
```bash
sudo apt-get update
sudo apt-get install build-essential unzip libaio1 alien
```

2. **Extract the Installation Files**:
   - After downloading the installation file, transfer it to your Ubuntu server.
   - Use the `unzip` command to extract the contents of the zip file:
     ```
     unzip filename.zip
     ```

3. **Run the Oracle Universal Installer**:
   - Change into the directory where the installation files were extracted.
   - Run the Oracle Universal Installer as the `oracle` user:
     ```
     su - oracle
     cd path/to/extracted/directory
     ./runInstaller
     ```

4. **Installation Wizard**:
   - Follow the Oracle Universal Installer prompts:
     - Specify the Oracle Home directory.
     - Choose the Oracle Database edition.
     - Configure the database name, system password, and other settings.
     - Configure the listener settings.
     - Specify database storage details.

5. **Installation Progress**:
   - Wait for the installation process to complete.

6. **Run Root Scripts**:
   - After installation, the installer will prompt you to run root scripts as the root user. Execute these scripts to configure the system for Oracle Database.

7. **Completing the Installation**:
   - Once the root scripts are executed, the installation process is complete.

8. **Verify Installation**:
   - Connect to Oracle Database using SQL*Plus or another Oracle client tool to verify the installation:
     ```
     sqlplus sys as sysdba
     ```

   Replace `sys` and `sysdba` with your actual username and role.

Please note that this is a simplified overview of the installation process. Refer to Oracle's official documentation for detailed instructions and troubleshooting.

**Important**: Oracle Database is primarily designed for Oracle Linux and other supported operating systems. Using it on Ubuntu may require additional configuration and is not officially supported by Oracle.
