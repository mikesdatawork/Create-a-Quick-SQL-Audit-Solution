![MIKES DATA WORK GIT REPO](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_01.png "Mikes Data Work")        

# Create a Quick SQL Audit Solution
**Post Date: July 10, 2014**




## Contents    
- [About Process](##About-Process)  
- [SQL Logic](#SQL-Logic)  
- [Build Info](#Build-Info)  
- [Author](#Author)  
- [License](#License)       

## About-Process

<p>Here's a quick audit solution using SQL Server Auditing. This will only work if you are using Enterprise Edition.</p>      


## SQL-Logic
```SQL
use master;
set nocount on
create server audit [General_SQL_Server_Audit_Set]
to file
(
      filepath = '\MyShareMyFolder' -- you do not have to enter a file name. this is automatic based on the audit name.
  ,   maxsize = 0 mb
  ,   max_rollover_files = 2147483647
  ,   reserve_disk_space = off
)
with ( queue_delay = 1000, on_failure = continue )
alter server audit [General_SQL_Server_Audit_Set] with (state = on)
go
waitfor delay '00:00:03'
create server audit specification [UserAccessAudit]
for server audit [General_SQL_Server_Audit_Set]
add (server_principal_change_group),
add (database_object_access_group)
with (state = on)
declare   @create_db_audits varchar(max)
set       @create_db_audits = ''
select    @create_db_audits = @create_db_audits +
'use [' + name + '];' + char(10) +
'create database audit specification [UserAccessAudit_' + name + ']' + char(10) +
'for server audit [General_SQL_Server_Audit_Set]' + char(10) +
'add (select, update, insert, delete, execute, receive, references on database::[' + name + '] by public)' + char(10) +
'with (state = on);' + char(10) + char(10)
from 
  sys.databases where name not in ('master', 'model', 'msdb', 'tempdb')
exec (@create_db_audits)
```

Just remember to right-click the Audit file, and make sure it's enabled.
Hope this is useful. 


[![WorksEveryTime](https://forthebadge.com/images/badges/60-percent-of-the-time-works-every-time.svg)](https://shitday.de/)

## Build-Info

| Build Quality | Build History |
|--|--|
|<table><tr><td>[![Build-Status](https://ci.appveyor.com/api/projects/status/pjxh5g91jpbh7t84?svg?style=flat-square)](#)</td></tr><tr><td>[![Coverage](https://coveralls.io/repos/github/tygerbytes/ResourceFitness/badge.svg?style=flat-square)](#)</td></tr><tr><td>[![Nuget](https://img.shields.io/nuget/v/TW.Resfit.Core.svg?style=flat-square)](#)</td></tr></table>|<table><tr><td>[![Build history](https://buildstats.info/appveyor/chart/tygerbytes/resourcefitness)](#)</td></tr></table>|

## Author

[![Gist](https://img.shields.io/badge/Gist-MikesDataWork-<COLOR>.svg)](https://gist.github.com/mikesdatawork)
[![Twitter](https://img.shields.io/badge/Twitter-MikesDataWork-<COLOR>.svg)](https://twitter.com/mikesdatawork)
[![Wordpress](https://img.shields.io/badge/Wordpress-MikesDataWork-<COLOR>.svg)](https://mikesdatawork.wordpress.com/)

 
## License
[![LicenseCCSA](https://img.shields.io/badge/License-CreativeCommonsSA-<COLOR>.svg)](https://creativecommons.org/share-your-work/licensing-types-examples/)

![Mikes Data Work](https://raw.githubusercontent.com/mikesdatawork/images/master/git_mikes_data_work_banner_02.png "Mikes Data Work")

