# MoveFilestoAzure

Broadly the options are as follows (not in any order of preference)

1. Logic Apps standard with Built-in Files connector
2. Logic Apps standard with Managed Files connector
3. Azure Data Factory
4. Azure File Sync
5. Push the files to Azure Storage with SFTP enabled
6. Producers of these files to write to Storage


# Logic Apps Standard with Built-in (a.k.a InApp) Files Connector

There is a File System connector in Azure, which can be used in Logic Apps or Power Platform to get the files from on-prem to Azure. The connector comes in 2 flavours, Built-in (a.k.a InApp) Connector or Managed (a.k.a Shared) Connector. The built-in version of this connector can only be used in Logic Apps standard. https://techcommunity.microsoft.com/t5/azure-integration-services-blog/using-file-system-connector-in-logic-apps-standard/ba-p/3711426 

The built-in connector's operations are supported only for Standard logic app workflows hosted in an App Service Environment v3.Because of the SMB 445 ports being blocked in the regular App Service Plans, you need to have ASE v3. However, there is no need for on-prem data gateway resource with built-in connector, which makes it simpler.

Important:
1. File system connector currently support only Windows file systems on Windows operating systems
2. Mapped network drives aren't supported
3. ASE v3 is needed to enable this Built-in File system connector operations

# Logic Apps with Managed (a.k.a shared) Files Connector

Using Logic Apps Standard (or consumption), with managed File system connector. This managed connector depends on on-prem data gateway to be installed on a VM on-prem - https://learn.microsoft.com/en-us/azure/logic-apps/install-on-premises-data-gateway - through which it will establish connection to move files from on-prem to Azure. To understand what this on-prem data gateway is, and how it works - https://learn.microsoft.com/en-us/data-integration/gateway/service-gateway-onprem 

# Azure Data Factory 
Using Azure Data factory - https://learn.microsoft.com/en-us/azure/data-factory/connector-file-system?tabs=data-factory - , and if your data store is located inside an -n-prem network, an Azure visturl network, you need to configure Self-hosted integration runtime - https://learn.microsoft.com/en-us/azure/data-factory/create-self-hosted-integration-runtime?tabs=data-factory - to connect to it.


# Azure File Sync
Using Azure File Sync - https://learn.microsoft.com/en-us/azure/storage/file-sync/file-sync-introduction - d- which does a constant syncing between your on-prem file location and Azure File share. This is a good option to have near real time replication of data from on-prem to Azure. However the downside is, if any changes made to the file on-prem will mean those changes will be synced as well, or if someone deletes the file on-prem, it gets deleted on Azure as well (works exactly like dropbox or onedrive sync). You can always copy files from this Azure Storage Account which is syncing from on-prem, to a newer storage account in Azure - which is not set to sync with on-prem. AzCopy is an option to move files from one storage account to another.

# On-prem SFTP Client
Using your existing on-prem SFTP client (open SSH), you can push these files into Azure storage with SFTP enabled. Your SFTP client will scan these folders, and if there are any files - it will SFTP connect to Azure storage account and move the files there.

# Producers of these files to write to SFTP in Azure directly
Producers of these files to directly write to a new SFTP location (which can be SFTP enabled Azure storage) instead of writing to on-prem location
