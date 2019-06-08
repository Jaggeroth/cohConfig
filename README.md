# Jagged's i24 Build log
The steps taken to produce a local build on a clean Win10 machine.

# Required Downloads
* Microsoft Visual Studio 2010 - much of the build requires the older tool chain.
* Microsoft Visual Studio 2019 - I have access to the Developer version but I believe Community version is good enough.
* Microsoft SQL Server 2017 Developer - A full-featured free edition, licensed for use as a development and test database in a non-production environment.
* Microsoft SQL Server Management Studio 2018
* Git - I use SourceTree from https://www.sourcetreeapp.com/
* CoHDev_Source_Files_Current(Updated).zip from here: magnet:?xt=urn:btih:CCCE574F7F89229B0FC2EF8AD898BE451864863A&dn=CoHDev_Source_Files_Current%28Updated%29.zip&tr=udp%3a%2f%2ftracker.openbittorrent.com%3a80%2fannounce&tr=udp%3a%2f%2ftracker.opentrackr.org%3a1337%2fannounce
* ServerData.7z from here: magnet:?xt=urn:btih:8CE9DE8ECCF29B899B247512F1082F20B59DC4B2&dn=ServerData.7z&tr=udp%3a%2f%2ftracker.openbittorrent.com%3a80%2fannounce&tr=udp%3a%2f%2ftracker.opentrackr.org%3a1337%2fannounce
* Client software from here: magnet:?xt=urn:btih:n642zelj2se2yn2ovmfy2z74tyyf7scs&dn=City%20of%20Heroes&tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A80%2Fannounce

# Installation steps
1. Install Visual Studio 2010
2. Install Visual Studio 2019
3. Add the location of MSBuild to your path. On my machine this was:
   C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin
4. Download git repo https://git.ourodev.com/CoX/Source
   You want the "develop" branch.
   For reference this build was done using commit "a4895517" from 7th of June, but I would expect it to work on the latest commit.
   Download to a directory such a "C:\coh"
5. Compile the server code:
   1. Open a Powershell window.
   2. Enter "Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine" and accept all at the prompt.
      You may have to run thia command as admin for it to be accepted. 
   3. Change directory to "C:\coh\MasterSolution"
   4. run build.ps1 (running .\build.ps1)
   5. Go make some tea. Takes a little while (16 mins on my i7).
6. In source directory create a new "data" folder under /bin"
   i.e. "C:\coh\bin\data"
7. Open ServerData.7z and copy contents to your new data folder.
8. Open CoHDev_Source_Files_Current(Updated).zip:
   1. Copy data from "i24 Server and Client Piggs (extracted with CoHTools)" folder to "C:\coh\bin\data\"
9. Copy "maps.db" file from "C:\coh\Assets\ConfigFiles\data\server\db" folder to 
   "C:\coh\bin\data\server\db", overwriting the old maps.db
10. Install the client to somewhere like "C:\City of Heroes"
    1. Copy the "C:\City Of Heroes\piggs" folder to "C:\coh\bin\piggs"
11. Create a folder called "C:\CoH_Data\attributes". It has to be that exactly.
    1. Open a command window in "C:\coh\bin" and run "mapserver.exe -productionmode -templates"
    2. Copy contents of attributes folder to "C:\coh\bin\data\server\db\templates"
12. Use SQL Server 2017 to create a new database
    Give it an Instance name of "COH"
    Install to a directory like "C:\mssql" and not in somewhere like programme files
    Select mixed authentication and enter and record a password for the "sa" account.
13. Edit the following sql scripts from the directory "C:\coh\Assets\DBSchemas"
    1. cohacc.sql
    2. cohauc.sql
    3. cohauth.sql
    4. cohchat.sql
    You will need to edit each sql script to point to the location of you database instance.
    Look for line in the create database statement like
    
    ```
    FILENAME = N'C:\MSSQL\MSSQL14.SQLEXPRESS\MSSQL\DATA\cohacc.mdf'
    ```
    and point it to your installation. Something like:
    ```
    FILENAME = N'C:\mssql\coh\MSSQL14.COH\MSSQL\DATA\cohacc.mdf'
    ```
    
14. From SQL Server Management Studio, open your "COH" db instance with your "sa" account.
    Run the scripts from the directory "C:\coh\DBSchemas"
    1. cohacc.sql
    2. cohauc.sql
    3. cohauth.sql
    4. cohchat.sql
    DO NOT run the cohdb.sql script that database will be created by the server the first time it runs
	Instead simply create a new database called "cohdb" and leave it empty.
15. Download the master branch from my [git repo](https://github.com/Jaggeroth/cohConfig) and copy the config files from "\bin\data\server\db" to the matching directory in your build i.e "C:\coh\bin\data\server\db"
16. Edit config files and update sql driver to point to your instance i.e.
    SqlLogin "DRIVER={SQL Server Native Client 11.0};Server=YOURPC\COH;Uid=sa;Pwd=yoursapassword;"
17. Time to start running things!
18. Open a command prompt at "C:\coh\bin"
    Run "mapserver.exe -chatservernames localhost"
    You only need to run this once and it should create the "cohdb" database
    and populate templates and other unknown stuff.  

# Launch Server
Now you can run the server elements you need to run each time.
From the "C:\coh\bin" directory run:
```
chatserver.exe -noreserved 
launcher.exe
dbserver.exe
```

# Launch Client
Find the "Ouroboros.exe" in the "C:\coh\bin" folder and create a shortcut:
```
C:\coh\bin\Ouroboros.exe -db 127.0.0.1 -console -noperforce -project "coh"
```

The "-project" parameter doesn't actually do anything unless you have certain registry stuff setup, 
so you can ignore this.

# Useful Commands
The first characters you create will have admin permissions until you change the DefaultAccessLevel in server.cfg down to 0.
For characters with access of 11 or more the following will give you access to an admin menu:
```
/mmm
```
Set your level to 50:
```
/levelup_xp 50
```
Change your costume to a Police Drone:
```
/benpc police_drone
```
 