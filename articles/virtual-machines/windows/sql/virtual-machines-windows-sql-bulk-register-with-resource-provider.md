---
title: Massregister-virtuella SQL-datorer i Azure med SQL VM-resursprovidern | Microsoft-dokument
description: Massregister SQL Server virtuella datorer med SQL VM-resursprovidern för att förbättra hanterbarheten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/21/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 015aa4c209a99921a930a51b15c3d0230722519a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75353890"
---
# <a name="bulk-register-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Massregister-virtuella SQL-datorer i Azure med SQL VM-resursprovidern

I den här artikeln beskrivs hur du massregistrerar din virtuella SQL Server-dator (VM) i Azure med SQL VM-resursprovidern med `Register-SqlVMs` PowerShell-cmdlet.

Cmdleten `Register-SqlVMs` kan användas för att registrera alla virtuella datorer i en viss lista över prenumerationer, resursgrupper eller en lista över specifika virtuella datorer. Cmdleten registrerar de virtuella datorerna i _lätthanteringsläge_ och genererar sedan både en [rapport och en loggfil](#output-description). 

Registreringsprocessen medför ingen risk, har inga driftstopp och startar inte om SQL Server eller den virtuella datorn. 

Mer information om resursprovidern finns i [SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md). 

## <a name="prerequisites"></a>Krav

Om du vill registrera din virtuella SQL Server-dator med resursleverantören behöver du följande: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/) som har [registrerats hos resursleverantören](virtual-machines-windows-sql-register-with-resource-provider.md#register-subscription-with-rp) och innehåller oregistrerade virtuella SQL Server-datorer. 
- Klientautentiseringsuppgifterna som används för att registrera de virtuella datorerna finns i någon av följande RBAC-roller: **Deltagare,** **Deltagare**eller **Ägare**. 
- Den senaste versionen av [Az PowerShell](/powershell/azure/new-azureps-module-az). 
- Den senaste versionen av [Az.SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="getting-started"></a>Komma igång

Innan du fortsätter måste du först skapa en lokal kopia av skriptet, importera det som en PowerShell-modul och ansluta till Azure. 

### <a name="create-script"></a>Skapa skript

Om du vill skapa skriptet kopierar du [hela skriptet](#full-script) `RegisterSqlVMs.psm1`från slutet av den här artikeln och sparar det lokalt som . 

### <a name="import-script"></a>Importera skript

När skriptet har skapats kan du importera det som en modul i Powershell-terminalen. 

Öppna en administrativ PowerShell-terminal och `RegisterSqlVMs.psm1` navigera till den plats där du sparade filen. Kör sedan följande PowerShell-cmdlet för att importera skriptet som en modul: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Anslut till Azure

Använd följande PowerShell-cmdlet för att ansluta till Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="all-vms-in-list-of-subscriptions"></a>Alla virtuella datorer i listan över prenumerationer 

Använd följande cmdlet för att registrera alla virtuella SQL Server-datorer i en lista över prenumerationer:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Exempel på utdata: 

```
Number of Subscriptions registration failed for 
because you do not have access or credentials are wrong: 1
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 4
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 3
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-single-subscription"></a>Alla virtuella datorer i en enda prenumeration

Använd följande cmdlet för att registrera alla virtuella SQL Server-datorer i en enda prenumeration: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1
```

Exempel på utdata:

```
Total VMs Found: 10
VMs Already registered: 1
Number of VMs registered successfully: 5
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the  guest agent on VM is not running: 2
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-multiple-resource-groups"></a>Alla virtuella datorer i flera resursgrupper

Använd följande cmdlet för att registrera alla virtuella SQL Server-datorer i flera resursgrupper inom en enda prenumeration:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupList ResourceGroup1,ResourceGroup2
```

Exempel på utdata:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="all-vms-in-a-resource-group"></a>Alla virtuella datorer i en resursgrupp

Använd följande cmdlet för att registrera alla virtuella SQL Server-datorer i en enda resursgrupp: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1
```

Exempel på utdata:

```
Total VMs Found: 4
VMs Already registered: 1
Number of VMs registered successfully: 1
Number of VMs failed to register due to error: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vms-in-single-resource-group"></a>Specifika virtuella datorer i enresursgruppen

Använd följande cmdlet för att registrera specifika virtuella SQL Server-datorer inom en enda resursgrupp:

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -VmList VM1,VM2,VM3
```

Exempel på utdata:

```
Total VMs Found: 3
VMs Already registered: 0
Number of VMs registered successfully: 1
Number of VMs skipped as VM or the guest agent on VM is not running: 1
Number of VMs skipped as they are not running SQL Server On Windows: 1

Please find the detailed report in file RegisterSqlVMScriptReport1571314821.txt
Please find the error details in file VMsNotRegisteredDueToError1571314821.log
```

## <a name="specific-vm"></a>Specifik virtuell dator

Använd följande cmdlet för att registrera en specifik virtuell SQL Server-dator: 

```powershell-interactive
Register-SqlVMs -Subscription SubscriptionId1 -ResourceGroupName ResourceGroup1 -Name VM1
```

Exempel på utdata:

```
Total VMs Found: 1
VMs Already registered: 0
Number of VMs registered successfully: 1

Please find the detailed report in  file RegisterSqlVMScriptReport1571314821.txt
```


## <a name="output-description"></a>Beskrivning av utdata

Både en rapport och loggfil `Register-SqlVMs` genereras varje gång cmdlet används. 

### <a name="report"></a>Rapport

Rapporten genereras som `.txt` en `RegisterSqlVMScriptReport<Timestamp>.txt` fil med namnet där tidsstämpeln är den tid då cmdleten startades. I rapporten visas följande uppgifter:

| **Utdatavärde** | **Beskrivning** |
| :--------------  | :-------------- | 
| Antalet prenumerationer som registreringen misslyckades eftersom du inte har åtkomst eller autentiseringsuppgifter är felaktiga | Detta ger nummer och lista över prenumerationer som hade problem med den angivna autentiseringen. Det detaljerade felet finns i loggen genom att söka efter prenumerations-ID. | 
| Antal prenumerationer som inte kunde prövas eftersom de inte är registrerade i RP | Det här avsnittet innehåller antalet och listan över prenumerationer som inte har registrerats till SQL VM-resursprovidern. |
| Totalt antal virtuella datorer hittades | Antalet virtuella datorer som hittades i omfattningen av de parametrar som skickades till cmdleten. | 
| Virtuella datorer som redan är registrerade | Antalet virtuella datorer som hoppades över eftersom de redan har registrerats hos resursprovidern. |
| Antal registrerade virtuella datorer | Antalet virtuella datorer som har registrerats efter att cmdlet har körts. Visar en lista över `SubscriptionID, Resource Group, Virtual Machine`registrerade virtuella datorer i formatet . | 
| Antal virtuella datorer kunde inte registreras på grund av fel | Antal virtuella datorer som inte kunde registreras på grund av något fel. Information om felet finns i loggfilen. | 
| Antal virtuella datorer som hoppas över när den virtuella datorn eller gust-agenten på den virtuella datorn inte körs | Antal och lista över virtuella datorer som inte kunde registreras som antingen den virtuella datorn eller gästagenten på den virtuella datorn kördes inte. Dessa kan göras om när den virtuella datorn eller gästagenten har startats. Information finns i loggfilen. |
| Antal virtuella datorer som hoppas över eftersom de inte kör SQL Server i Windows | Antal virtuella datorer som hoppades över eftersom de inte kör SQL Server eller inte är en virtuell Windows-dator. De virtuella datorerna visas `SubscriptionID, Resource Group, Virtual Machine`i formatet . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Logga 

Fel loggas i loggfilen `VMsNotRegisteredDueToError<Timestamp>.log` med namnet där tidsstämpel är den tidpunkt då skriptet startade. Om felet finns på prenumerationsnivå innehåller loggen det kommaavgränsade SubscriptionID och felmeddelandet. Om felet är med registreringen av den virtuella datorn innehåller loggen prenumerations-ID, resursgruppnamn, namn på virtuella datorer, felkod och meddelande avgränsat med kommatecken. 

## <a name="remarks"></a>Anmärkningar

När du registrerar virtuella SQL Server-datorer med resursprovidern med det medföljande skriptet bör du tänka på följande:

- Registrering hos resursprovidern kräver en gästagent som körs på DEN VIRTUELLA SQL Server-datorn. Windows Server 2008-avbildningar har ingen gästagent, så dessa virtuella datorer misslyckas och måste registreras manuellt med hjälp av [hanteringsläget NoAgent](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes).
- Det finns inbyggt logik för att lösa genomskinliga fel igen. Om den virtuella datorn har registrerats är det en snabb åtgärd. Men om registreringen misslyckas, kommer varje virtuell dator att försökas på nytt.  Därför bör du ge betydande tid för att slutföra registreringsprocessen - även om det faktiska tidskravet är beroende av typen och antalet fel. 

## <a name="full-script"></a>Fullständigt skript

Det fullständiga skriptet på GitHub finns i [Massregister-virtuella SQL-datorer med Az PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Kopiera hela skriptet och `RegisterSqLVMs.psm1`spara det som .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)
