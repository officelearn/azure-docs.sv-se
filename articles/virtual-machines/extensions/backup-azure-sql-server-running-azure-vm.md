---
title: Azure Backup för SQL Server som körs i Azure VM
description: I den här artikeln kan du läsa om hur du registrerar Azure Backup i SQL Server som körs på en virtuell Azure-dator.
services: backup
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: dacurwin
ms.openlocfilehash: b17e4031edaedc6b0a63d305d20a77e5b58f91ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247392"
---
# <a name="azure-backup-for-sql-server-running-in-azure-vm"></a>Azure Backup för SQL Server som körs i Azure VM

Azure Backup, bland andra erbjudanden, ger stöd för säkerhetskopiering av arbetsbelastningar som SQL Server som körs i virtuella Azure-datorer. Eftersom SQL-programmet körs i en Virtuell Azure-dator behöver säkerhetskopieringstjänsten behörighet för att komma åt programmet och hämta nödvändig information.
För att göra det installerar Azure Backup **AzureBackupWindowsWorkload-tillägget** på den virtuella datorn, där SQL Server körs, under registreringsprocessen som utlöses av användaren.

## <a name="prerequisites"></a>Krav

Information om listan över scenarier som stöds finns i [matrisen för supporterbarhet](../../backup/sql-support-matrix.md#scenario-support) som stöds av Azure Backup.

## <a name="network-connectivity"></a>Nätverksanslutningar

Azure Backup stöder NSG-taggar, distribuerar en proxyserver eller listade IP-intervall. för mer information om var och en av metoderna, se den här [artikeln](https://docs.microsoft.com/azure/backup/backup-sql-server-database-azure-vms#establish-network-connectivity).

## <a name="extension-schema"></a>Tilläggsschema

Tilläggsschemat och egenskapsvärdena är de konfigurationsvärden (körningsinställningar) som tjänsten går till CRP API. Dessa konfigurationsvärden används vid registrering och uppgradering. **AzureBackupWindowsWorkload-tillägget** använder också det här schemat. Schemat är förinställt. en ny parameter kan läggas till i objectStr-fältet

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

Följande JSON visar schemat för tillägget WorkloadBackup.  

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

Namn | Värde/exempel | Datatyp
 --- | --- | ---
språk | sv-se  |  sträng
taskId (på uppdrag) | "1c0ae461-9d3b-418c-a505-bb31dfe2095d"  | sträng
objectStr (objektStr) <br/> (offentligaInställningar)  | "eyjjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjJZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjUTNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | sträng
kommandotStartTimeUTCTicks | "636967192566036845"  | sträng
vmType (vmType)  | "microsoft.compute/virtualmachines"  | sträng
objectStr (objektStr) <br/> (protectedSettings) | "eyjjb250YWluZXJQcm9wZXJ0aWVzIjp7IkNvbnRhaW5lcklEIjoiMzVjMjQxYTItOGRjNy00ZGE5LWI4NTMtMjdjYTJhNDZlM2ZkIiwiSWRNZ210Q29udGFpbmVySWQiOjM0NTY3ODg5LCJSZXNvdXJjJZUlkIjoiMDU5NWIwOGEtYzI4Zi00ZmFlLWE5ODItOTkwOWMyMGVjNjVhIiwiU3Vic2NyaXB0aW9uSWQiOiJkNGEzOTliNy1iYjAyLTQ2MWMtODdmYS1jNTM5ODI3ZTgzNTQiLCJVbmlxdWVDb250YWluZXJOYW1lIjoiODM4MDZjUTNTQ4OS00NmNhLWEyZTctNWMzNzNhYjg3OTcyIn0sInN0YW1wTGlzdCI6W3siU2VydmljZU5hbWUiOjUsIlNlcnZpY2VTdGFtcFVybCI6Imh0dHA6XC9cL015V0xGYWJTdmMuY29tIn1dfQ ==" | sträng
loggarBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sträng
statusBlobUri | <https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw> | sträng

## <a name="template-deployment"></a>Malldistribution

Vi rekommenderar att du lägger till AzureBackupWindowsWorkload-tillägget till en virtuell dator genom att aktivera SQL Server-säkerhetskopiering på den virtuella datorn. Detta kan uppnås via [Resource Manager-mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-recovery-services-vm-workload-backup) som utformats för att automatisera säkerhetskopiering på en VIRTUELL SQL Server.

## <a name="powershell-deployment"></a>PowerShell-distribution

Du måste "registrera" den virtuella Azure-datorn som innehåller SQL-programmet med ett recovery-tjänstvalv. Under registreringen installeras AzureBackupWindowsWorkload-tillägget på den virtuella datorn. Använd [Register-AzRecoveryServicesBackupContainerPS](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) cmdlet för att registrera den virtuella datorn.

```powershell
$myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

Kommandot returnerar en **reservbehållare** för den här resursen och statusen **registreras**.

## <a name="next-steps"></a>Nästa steg

- [Läs mer](https://docs.microsoft.com/azure/backup/backup-sql-server-azure-troubleshoot) om felsökningsriktlinjer för säkerhetskopiering av azure SQL Server-datorer
- [Vanliga frågor](https://docs.microsoft.com/azure/backup/faq-backup-sql-server) om säkerhetskopiering av SQL Server-databaser som körs på virtuella Azure-datorer (VMs) och som använder Azure Backup-tjänsten.
