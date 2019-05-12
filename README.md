# Jagged's i24 Build log
The steps taken to produce a local build on a clean Win10 machine.

# Required Downloads
* Microsoft Visual Studio 2010 - much of the build requires the older tool chain.
* Microsoft Visual Studio 2019 - I have access to the Developer version but I believe Community version is good enough.
* Microsoft SQL Server 2017 Developer - A full-featured free edition, licensed for use as a development and test database in a non-production environment.
* Microsoft SQL Server Management Studio 2018
* Git - I use SourceTree from https://www.sourcetreeapp.com/
* ServerData.7z from here: 
* Client software from here: magnet:?xt=urn:btih:n642zelj2se2yn2ovmfy2z74tyyf7scs&dn=City%20of%20Heroes&tr=udp%3A%2F%2Ftracker.openbittorrent.com%3A80%2Fannounce

# Installation steps
1. Install Visual Studio 2010
2. Install Visual Studio 2019
3. Add the location of MSBuild to your path. On my machine this was:
   C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin
4. Download git repo https://git.ourodev.com/CoX/Source
   You want the "develop" branch.
   Download to a directory such a "C:\coh"
5. Compile the server code:
   1. Open a Powershell window.
   2. Enter "Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope LocalMachine" and accept all at the prompt.
   3. Change directory to "C:\coh\MasterSolution"
   4. run build.ps1 (running .\build.ps1)
   5. Go make some tea. Takes a little while.
6. In source directory create a new "data" folder under /bin"
   i.e. "C:\coh\bin\data"
7. Open ServerData.7z and copy contents to your new data folder.
8. Open CoHDev_Source_Files_Current(Updated).zip.
   1. Copy data from "i24 Server and Client Piggs (extracted with CoHTools)" folder to "C:\coh\bin\data\"
   2. Copy "maps.db" file from "map.db (modified, working on current VS2010 build)" folder to 
      "C:\coh\bin\data\server\db", overwriting the old maps.db
   3. Copy piggs folder client to "C:\coh\bin\piggs"
9. Create a folder called "C:\CoH_Data\attributes". It has to be that exactly.
   1. Open a command window in "c:\coh\bin" and run "mapserver.exe -productionmode -templates"
   2. Copy contents of attributes folder to "C:\coh\bin\data\server\db\templates"
10. Use SQL Server 2017 to create a new database
   Give it an Instance name of "COH"
   Install to a directory like "c:\mssql" and not in somewhere like programme files
   Select mixed authentication and enter and record a password for the "sa" account.
11. Edit the following sql scripts from the directory "C:\coh\DBSchemas"
   1. cohacc.sql
   2. cohauc.sql
   3. cohauth.sql
   4. cohchat.sql
   You will need to edit each sql script to point to the location of you database instance.
   Look for line in the create database statement like
   FILENAME = N'C:\MSSQL\MSSQL14.SQLEXPRESS\MSSQL\DATA\cohacc.mdf'
   and point it to your installation. Something like:
   FILENAME = N'C:\mssql\coh\MSSQL14.COH\MSSQL\DATA\cohacc.mdf'
12. From SQL Server Management Studio, open your "COH" db instance with your "sa" account.
   Run the scripts from the directory "C:\coh\DBSchemas"
   1. cohacc.sql
   2. cohauc.sql
   3. cohauth.sql
   4. cohchat.sql
   DO NOT run the cohdb.sql script that database will be created by the server the first time it runs.
13. Load the config files from my git to "c:\coh\bin\data\server\db"
14. Edit config files and update sql driver to point to your instance i.e.
    SqlLogin "DRIVER={SQL Server Native Client 11.0};Server=YOURPC\COH;Uid=sa;Pwd=yoursapassword;"
15. Time to start running things!
16. Open a command prompt at "c:\coh\bin"
    Run "mapserver.exe -chatservernames localhost"
    You only need to run this once and it should create the "cohdb" database
    and populate templates and other unknown stuff.  

# Launch Server
Now you can run the server elements you need to run each time.
From the "c:\coh\bin" directory run:
chatserver.exe -noreserved 
launcher.exe
dbserver.exe

# Launch Client
Extract the cient to a directory like "c:\City of Heroes"
and set up the followiing shortcut:
C:\City of Heroes\cityofheroes.exe -db 127.0.0.1 -console -noperforce -project "coh"

The "-project" parameter doesn't actually do anything unless you have certain registry stuff setup, 
so you can ignore this.