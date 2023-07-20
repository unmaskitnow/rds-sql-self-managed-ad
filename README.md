# rds-sql-self-managed-ad
Join Amazon RDS for SQL Server to self Managed Active Directory on Amazon EC2 Windows Server

## Link to YouTube video : [Join Amazon RDS for SQL Server to self Managed Active Directory](TBD)
Agenda
* Windows Authentication Options for Amazon RDS for SQL Server
* Windows Authentication Options for Amazon RDS Custom for SQL Server
* Management Instance & Amazon RDS SQL Server Port Requirements
* Launch Management Instance and join to the self Managed Active Directory Domain and Install SQL Server Management Studio (SSMS)
* Create Organizational Unit  (OU) in Active Directory
* Create a domain user with delegated permissions to perform domain join of RDS DB Instance
* Create a domain user for SQL login
* Create a encryoted secret in AWS Secrets Manager with domain join credentials
* Join Amazon RDS to self Managed Active Directory
* Test SQL login for domain user
  
## [AWS documentation for Working with Self Managed Active Directory with an Amazon RDS for SQL Server DB instance](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_SQLServer_SelfManagedActiveDirectory.html)


## Powershell Commands

### MGMT

Rename Computer
  ```powershell
Rename-Computer -NewName "<newName>" -Restart


Get Interface Index for Ethernet adapter (Elastic Network Adapter)
  ```powershell
Get-NetIPInterface 
  ```

Set the primary DNS Server IP on the Ethernet Adapter
  ```powershell
Set-DnsClientServerAddress -InterfaceIndex <InterfaceIndex> -ServerAddresses ("<DC01 IP>", "<DC02 IP>")
  ```

Join MGMT to self Managed Active Directory domain
  ```powershell
Add-Computer -DomainName <domainName> -Restart
  ```

[Download SQL Server Management Studio (SSMS)](https://learn.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-ver16)

Install SQL Server Management Studio (SSMS)

  ```powershell
$media_path = "<PathToInstaller>\SSMS-Setup-ENU.exe"
$install_path = "$env:SystemDrive\SSMSto"
$params = " /Install /Quiet SSMSInstallRoot=$install_path"

Start-Process -FilePath $media_path -ArgumentList $params -Wait 
  ```

### RDS

Create Windows Authentication SQL Server login
  ```powershell
USE [master]
GO
CREATE LOGIN [<domainName>\<domainUserName>] FROM WINDOWS WITH DEFAULT_DATABASE = [master], DEFAULT_LANGUAGE = [us_english];
GO
  ```




Add `VIEW SERVER STATE` permissions to domain user
  ```powershell
USE [master]
GO
GRANT VIEW SERVER STATE TO [<domainName>\<domainUser>]
  ```

DB query
  ```powershell
select SUSER_NAME()
select net_transport, auth_scheme from sys.dm_exec_connections where session_id = @@spid
select * from sys.databases 
  ```



