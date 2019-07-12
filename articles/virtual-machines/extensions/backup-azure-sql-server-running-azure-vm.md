---
title: Azure Backup för SQL Server som körs i Azure VM
description: Så här registrerar du Azure Backup SQL Server som körs i Azure VM
services: backup
author: swatisachdeva
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: swatisachdeva
ms.openlocfilehash: 8710242e04156c8af6e5882a3cb4d42cc31e3677
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67607616"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup för SQL Server som körs i Azure VM

Azure Backup, bland andra erbjudanden har stöd för säkerhetskopiering av arbetsbelastningar som SQL Server som körs i virtuella Azure-datorer. Eftersom SQL-programmet körs inom en Azure-dator, måste säkerhetskopieringstjänsten behörighet att komma åt programmet och hämta nödvändig information.
Gör att Azure Backup installerar den **AzureBackupWindowsWorkload** tillägget på den virtuella datorn där SQL Server körs under registreringsprocessen som utlöses av användaren.

## <a name="prerequisites"></a>Förutsättningar

Lista över scenarier som stöds, finns det [support matrix](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support) stöds av Azure Backup.

## <a name="network-connectivity"></a>Nätverksanslutning

Azure Backup stöder NSG taggar, distribution av en proxyserver eller IP-adressintervall; i listan information om var och en av metoderna som finns i det här [artikeln](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Tilläggsschema

Tillägget schema- och värdena är konfigurationsvärden (körningsinställningar) som tjänsten är att skicka till CRP-API. Dessa konfigurationsvärden används under registreringen och uppgradering. **AzureBackupWindowsWorkload** tillägg också använda det här schemat. Schemat har redan angetts; en ny parameter kan läggas till i fältet objectStr

  ```json
      "runtimeSettings": [{
      "handlerSettings": {
      "protectedSettingsCertThumbprint": "",
      "protectedSettings": {
      "objectStr": "",
      "logsBlobUri": "",
      "statusBlobUri": ""
      }
      },
      "publicSettings": {
      "locale": "en-us",
      "taskId": "1c0ae461-9d3b-418c-a505-bb31dfe2095d",
      "objectStr": "",
      "commandStartTimeUTCTicks": "636295005824665976",
      "vmType": "vmType"
      }
      }]
      }
  ```

Följande JSON visar schemat för WorkloadBackup-tillägget.  

  ```json
  {
    "type": "extensions",
    "name": "WorkloadBackup",
    "location":"<myLocation>",
    "properties": {
      "publisher": "Microsoft.RecoveryServices",
      "type": "AzureBackupWindowsWorkload",
      "typeHandlerVersion": "1.1",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "locale":"<location>",
        "taskId":"<TaskId used by Azure Backup service to communicate with extension>",

        "objectStr": "<The configuration passed by Azure Backup service to extension>",

        "commandStartTimeUTCTicks": "<Scheduled start time of registration or upgrade task>",
        "vmType": "<Type of VM where registration got triggered Eg. Compute or ClassicCompute>"
      },
      "protectedSettings": {
        "objectStr": "<The sensitive configuration passed by Azure Backup service to extension>",
        "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
        "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
      }
    }
  }
  ```

### <a name="property-values"></a>Egenskapsvärden

Namn | Värdet/exempel | Datatyp
 --- | --- | ---
Nationella inställningar | en-us  |  sträng
taskId | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | sträng
objectStr <br/> (publicSettings)  | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == ” | sträng
commandStartTimeUTCTicks | "636967192566036845"  | sträng
vmType  | "microsoft.compute/virtualmachines"  | sträng
objectStr <br/> (protectedSettings) | "eyJjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5O DI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjODUtNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ == ” | sträng
logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng
statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng


## <a name="template-deployment"></a>Malldistribution

Vi rekommenderar att du lägger till AzureBackupWindowsWorkload tillägget till en virtuell dator är genom att aktivera säkerhetskopiering av SQL Server på den virtuella datorn. Detta kan ske via den [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) utformats för att automatisera säkerhetskopiering på en SQL Server VM.


## <a name="powershell-deployment"></a>PowerShell-distribution

Du måste registrera den virtuella Azure-datorer som innehåller SQL-program med ett Recovery services-valv. Under registreringen installeras AzureBackupWindowsWorkload tillägget på den virtuella datorn. Använd [registrera AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdleten för att registrera den virtuella datorn.
 
```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```
 
Kommandot returnerar en **säkerhetskopieringsbehållaren** detta resurs och status kommer att **registrerad**.


## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) om Azure SQL Server VM backup riktlinjer för felsökning
- [Vanliga frågor](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) om säkerhetskopiering av SQL Server-databaser som körs på virtuella Azure-datorer (VM) och som använder Azure Backup-tjänsten.
