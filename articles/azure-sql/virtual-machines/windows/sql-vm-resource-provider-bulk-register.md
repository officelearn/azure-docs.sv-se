---
title: Mass registrering av virtuella SQL-datorer i Azure med den virtuella SQL-adressresursen | Microsoft Docs
description: Mass registrering SQL Server virtuella datorer med resurs leverantören för SQL-VM för att förbättra hanterbarheten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 09/21/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0d6900d0fdf656fa8309b18971691bb35587f7f4
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286077"
---
# <a name="register-multiple-sql-virtual-machines-in-azure-with-the-sql-vm-resource-provider"></a>Registrera flera virtuella SQL-datorer i Azure med providern för SQL VM-resurs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du registrerar SQL Server virtuella datorer i bulk i Azure med den virtuella SQL-adressresursen med hjälp av `Register-SqlVMs` PowerShell-cmdleten. Vid registrering med den virtuella SQL VM-providern installeras [SQL IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md).

I den här artikeln lär du dig att registrera SQL Server virtuella datorer i bulk. Alternativt kan du registrera [alla SQL Server virtuella datorer automatiskt](sql-vm-resource-provider-automatic-registration.md) eller [enskilda SQL Server virtuella datorer](sql-vm-resource-provider-register.md). 

## <a name="overview"></a>Översikt

`Register-SqlVMs`Cmdleten kan användas för att registrera alla virtuella datorer i en angiven lista med prenumerationer, resurs grupper eller en lista med virtuella datorer. Cmdleten registrerar de virtuella datorerna i läget för _förenklad_ hantering och genererar sedan både en [rapport och en loggfil](#output-description). 

Registrerings processen har ingen risk, har ingen stillestånds tid och kommer inte att starta om SQL Server eller den virtuella datorn. 

Mer information finns i [SQL VM Resource Provider](sql-vm-resource-provider-register.md). 

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna registrera SQL Server VM med resurs leverantören: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/) som har [registrerats med resurs leverantören](sql-vm-resource-provider-register.md#register-subscription-with-rp) och innehåller oregistrerade SQL Server virtuella datorer. 
- De klientautentiseringsuppgifter som används för att registrera de virtuella datorerna finns i någon av följande Azure-roller: **virtuell dator** , **deltagare** eller **ägare**. 
- Den senaste versionen av [AZ PowerShell](/powershell/azure/new-azureps-module-az). 
- Den senaste versionen av [AZ. SqlVirtualMachine](https://www.powershellgallery.com/packages/Az.SqlVirtualMachine/0.1.0).

## <a name="get-started"></a>Kom igång

Innan du fortsätter måste du först skapa en lokal kopia av skriptet, importera det som en PowerShell-modul och ansluta till Azure. 

### <a name="create-the-script"></a>Skapa skriptet

Om du vill skapa skriptet kopierar du det [fullständiga skriptet](#full-script) från slutet av den här artikeln och sparar det lokalt som `RegisterSqlVMs.psm1` . 

### <a name="import-the-script"></a>Importera skriptet

När skriptet har skapats kan du importera det som en modul i PowerShell-terminalen. 

Öppna en administrativ PowerShell-Terminal och navigera till den plats där du sparade `RegisterSqlVMs.psm1` filen. Kör sedan följande PowerShell-cmdlet för att importera skriptet som en modul: 

```powershell-interactive
Import-Module .\RegisterSqlVMs.psm1
```

### <a name="connect-to-azure"></a>Anslut till Azure

Använd följande PowerShell-cmdlet för att ansluta till Azure:

```powershell-interactive
Connect-AzAccount
```


## <a name="register-all-vms-in-a-list-of-subscriptions"></a>Registrera alla virtuella datorer i en lista över prenumerationer 

Använd följande cmdlet för att registrera alla SQL Server virtuella datorer i en lista över prenumerationer:

```powershell-interactive
Register-SqlVMs -SubscriptionList SubscriptionId1,SubscriptionId2
```

Exempel på utdata: 

```
Number of subscriptions registration failed for 
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

## <a name="register-all-vms-in-a-single-subscription"></a>Registrera alla virtuella datorer i en enda prenumeration

Använd följande cmdlet för att registrera alla SQL Server virtuella datorer i en enda prenumeration: 

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

## <a name="register-all-vms-in-multiple-resource-groups"></a>Registrera alla virtuella datorer i flera resurs grupper

Använd följande cmdlet för att registrera alla SQL Server virtuella datorer i flera resurs grupper inom en enda prenumeration:

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

## <a name="register-all-vms-in-a-resource-group"></a>Registrera alla virtuella datorer i en resurs grupp

Använd följande cmdlet för att registrera alla SQL Server virtuella datorer i en enda resurs grupp: 

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

## <a name="register-specific-vms-in-a-single-resource-group"></a>Registrera vissa virtuella datorer i en enda resurs grupp

Använd följande cmdlet för att registrera vissa SQL Server virtuella datorer i en enda resurs grupp:

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

## <a name="register-a-specific-vm"></a>Registrera en speciell virtuell dator

Använd följande cmdlet för att registrera en speciell SQL Server virtuell dator: 

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

Både en rapport och en loggfil genereras varje gång `Register-SqlVMs` cmdleten används. 

### <a name="report"></a>Rapport

Rapporten skapas som en `.txt` fil med namnet `RegisterSqlVMScriptReport<Timestamp>.txt` där tidsstämpeln är den tid då cmdleten startades. Rapporten visar följande information:

| **Utmatnings värde** | **Beskrivning** |
| :--------------  | :-------------- | 
| Det gick inte att registrera prenumerationen eftersom du inte har åtkomst eller autentiseringsuppgifter | Detta ger antalet och listan över prenumerationer som har problem med den angivna autentiseringen. Du hittar det detaljerade felet i loggen genom att söka efter prenumerations-ID. | 
| Antal prenumerationer som inte kunde göras på grund av att de inte är registrerade i RP | Det här avsnittet innehåller antalet och listan över prenumerationer som inte har registrerats till resurs leverantören för SQL-VM. |
| Totalt antal virtuella datorer som hittades | Antalet virtuella datorer som hittades i omfånget för parametrarna som skickades till cmdleten. | 
| Virtuella datorer har redan registrerats | Antalet virtuella datorer som hoppades över eftersom de redan har registrerats hos resurs leverantören. |
| Antal registrerade virtuella datorer | Antalet virtuella datorer som har registrerats efter att-cmdleten har körts. Visar en lista över registrerade virtuella datorer i formatet `SubscriptionID, Resource Group, Virtual Machine` . | 
| Antalet virtuella datorer som inte kunde registreras på grund av fel | Antal virtuella datorer som inte kunde registreras på grund av ett fel. Information om felet finns i logg filen. | 
| Antalet virtuella datorer som hoppades över eftersom den virtuella datorn eller vindby-agenten på den virtuella datorn inte körs | Antal och lista över virtuella datorer som inte kunde registreras som antingen den virtuella datorn eller gäst agenten på den virtuella datorn inte kördes. De kan upprepas när den virtuella datorn eller gäst agenten har startats. Information hittar du i logg filen. |
| Antal virtuella datorer som hoppades över eftersom de inte körs SQL Server på Windows | Antal virtuella datorer som hoppades över eftersom de inte körs SQL Server eller inte är en virtuell Windows-dator. De virtuella datorerna visas i formatet `SubscriptionID, Resource Group, Virtual Machine` . | 
| &nbsp; | &nbsp; |

### <a name="log"></a>Loggas 

Fel loggas i logg filen med namnet `VMsNotRegisteredDueToError<Timestamp>.log` , där tidsstämpel är den tid då skriptet startade. Om felet finns på prenumerations nivån innehåller loggen det kommaavgränsade prenumerations-ID: t och fel meddelandet. Om felet är med den virtuella dator registreringen innehåller loggen prenumerations-ID, resurs gruppens namn, namn på virtuell dator, felkod och meddelande avgränsade med kommatecken. 

## <a name="remarks"></a>Kommentarer

När du registrerar SQL Server virtuella datorer med resurs leverantören med hjälp av det tillhandahållna skriptet bör du tänka på följande:

- Registrering med resurs leverantören kräver att en gästa Gent körs på SQL Server VM. Windows Server 2008-avbildningar har ingen gästa Gent, så de här virtuella datorerna kommer att Miss Miss sen och måste registreras manuellt med [hanterings läget för noagent](sql-server-iaas-agent-extension-automate-management.md#management-modes).
- Det finns ett omprövnings logik som är inbyggt för att lösa transparenta fel. Om den virtuella datorn har registrerats är det en snabb åtgärd. Men om registreringen Miss lyckas görs ett nytt försök för varje virtuell dator.  Därför bör du tillåta betydande tid att slutföra registrerings processen – även om det faktiska tids kravet är beroende av typ och antal fel. 

## <a name="full-script"></a>Fullständigt skript

Det fullständiga skriptet på GitHub finns i [Mass registrering av SQL-VM: ar med AZ PowerShell](https://github.com/Azure/azure-docs-powershell-samples/blob/master/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1). 

Kopiera det fullständiga skriptet och spara det som `RegisterSqLVMs.psm1` .

[!code-powershell-interactive[main](../../../../powershell_scripts/sql-virtual-machine/register-sql-vms/RegisterSqlVMs.psm1 "Bulk register SQL Server virtual machines")]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](../../database/doc-changes-updates-release-notes.md)
