### Installing Oracle Database on Ubuntu


1. **Install Dependencies**
```bash
sudo apt-get update
sudo apt-get install build-essential unzip libaio1 alien
```

2. **Create Oracle User and Groups**
```bash
sudo groupadd oinstall
sudo groupadd dba
sudo useradd -m -g oinstall -G dba oracle
```
3. **Create password for User oracle**
Run the following command and went prompted, enter a password for user oracle
```bash
sudo passwd oracle
```
5.   **Switch to oracle user**
```bash
     su - oracle
```
1. **Download Oracle Database Software**:
   - use the wget command provided (see course shell, not shown here for security purposes)
   - Note you can download the file as root user and then transfer the file to oracle home as well
```bash
wget https://IP_ADDRESS/LINUX.X64_193000_db_home.zip
```

2. **Extract the Installation Files**:
   - After downloading the installation file, create a new folder
```bash
mkdir oracle-db
cd oracle-db
```
   - Use the `unzip` command to extract the contents of the zip file:
```bash
unzip LINUX.X64_193000_db_home.zip
```
6. **Kernel parameter tuning
   - You'll need to modify some kernel parameters. Edit the /etc/sysctl.conf file and add the following lines: 
```bash
fs.file-max = 6815744
kernel.sem = 250 32000 100 128
kernel.shmmax = 536870912
kernel.shmall = 2097152
```
   - Then, run the following command to apply the changes:

```bash
sudo sysctl -p
```
7. **Set the DISPLAY Variable:
   - Before running the Oracle Universal Installer, set the DISPLAY variable to a dummy display value:
```bash
export DISPLAY=:0.0
```

8. **Run the Oracle Universal Installer**:
   - Change into the directory where the installation files were extracted.
   - Run the Oracle Universal Installer as the `oracle` user:
     ```
     cd path/to/extracted/directory
     ./runInstaller
     ```

9. **Installation Wizard**:
   - Follow the Oracle Universal Installer prompts:
     - Specify the Oracle Home directory.
     - Choose the Oracle Database edition.
     - Configure the database name, system password, and other settings.
     - Configure the listener settings.
     - Specify database storage details.

9. **Verify Installation**:
   - Connect to Oracle Database using SQL*Plus or another Oracle client tool to verify the installation:
     ```
     sqlplus sys as sysdba
     ```

   Replace `sys` and `sysdba` with your actual username and role.

