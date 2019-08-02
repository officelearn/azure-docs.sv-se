---
title: Registrera SQL Server virtuell dator i Azure med providern för SQL VM-resurs | Microsoft Docs
description: Registrera din SQL Server VM med resurs leverantören för SQL-VM för att förbättra hanterbarheten.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 95a167cbc8fde4488e3f46ffd850f0619cf1b651
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305876"
---
# <a name="register-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrera SQL Server virtuell dator i Azure med providern för SQL VM-resurs

I den här artikeln beskrivs hur du registrerar din virtuella Azure SQL Server-dator med providern för SQL VM-resurs. 

Genom att distribuera en SQL Server VM Marketplace-avbildning via Azure Portal registreras SQL Server VM automatiskt med resurs leverantören. Men om du väljer att själv installera SQL Server på en virtuell Azure-dator i stället för att välja en avbildning från Azure Marketplace bör du registrera din SQL Server VM med resurs leverantören idag för:

-  **Kompatibilitet** – enligt Microsofts produkt villkor måste kunderna som använder [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/) ange att Microsoft ska använda den Azure Hybrid-förmån-och för att göra det, måste de registreras med resurs leverantören för SQL-VM. 

-  **Funktions förmåner** – genom att registrera SQL Server VM med Resource providern låser du upp [Automatisk uppdatering](virtual-machines-windows-sql-automated-patching.md), [Automatisk säkerhets kopiering](virtual-machines-windows-sql-automated-backup-v2.md), övervakning och hanterings funktioner, samt [licensierings](virtual-machines-windows-sql-ahb.md) -och [versions](virtual-machines-windows-sql-change-edition.md) flexibilitet. Tidigare var dessa endast tillgängliga för SQL Server VM avbildningar från Azure Marketplace.

Att själv installera SQL Server på en virtuell Azure-dator eller genom att tillhandahålla en virtuell Azure-dator från en anpassad virtuell hård disk med SQL Server är kompatibel via Azure Hybrid-förmån för SQL Server med villkoret att kunderna anger att de använder Microsoft genom att registrera med en virtuell SQL-resurs CSP. 

Om du vill använda en SQL VM-adressresurs måste du också registrera SQL VM-resurspoolen med din prenumeration. Detta kan åstadkommas med Azure Portal, Azure CLI och PowerShell. 

## <a name="prerequisites"></a>Förutsättningar

Om du vill registrera SQL Server VM med resurs leverantören behöver du följande: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- [Azure CLI](/cli/azure/install-azure-cli) och [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="register-with-sql-vm-resource-provider"></a>Registrera dig för SQL VM Resource Provider
Om [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md) redan är installerat på den virtuella datorn skapar du bara en metadata-resurs av typen Microsoft. SqlVirtualMachine/SqlVirtualMachines när du registrerar med SQL VM Resource Provider. Nedan visas kodfragmentet som ska registreras med en SQL VM-adressresurs om SQL IaaS-tillägget redan är installerat på den virtuella datorn. Du måste ange vilken typ av SQL Server licens du vill när du registrerar med en SQL VM-adressresurs som antingen "PAYG" eller "AHUB". 

Registrera SQL Server VM med PowerShell med följande kodfragment:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register with SQL VM resource provider
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}  
  
  ```

Om SQL IaaS-tillägget inte är installerat på den virtuella datorn kan du registrera dig för en SQL VM-adressresurs genom att ange läget för förenklad SQL-hantering. I läget för Lightweight SQL Management kommer SQL VM-IaaS automatiskt att installera SQL-tillägget i [Lightweight-läge](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) och verifiera SQL Server instansens metadata. Detta kommer inte att startas om SQL Server-tjänsten. Du måste ange vilken typ av SQL Server licens du vill när du registrerar med en SQL VM-adressresurs som antingen "PAYG" eller "AHUB". 

Registrera SQL Server VM i läget för Lightweight SQL Management med hjälp av PowerShell med följande kodfragment:

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

Registrering med den virtuella SQL-adressresursen i [Lightweight-läge](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) garanterar efterlevnad och möjliggör flexibel licensiering samt SQL Server uppdateringar på plats. Kluster instanser för växling vid fel och distributioner med flera instanser kan bara registreras med en SQL VM-adressresurs i Lightweight-läge. Du kan följa anvisningarna som finns på Azure Portal för att uppgradera till [fullständigt läge](virtual-machines-windows-sql-server-agent-extension.md#full-mode-installation) och aktivera omfattande hanterings funktioner med en SQL Server omstart när som helst. 

## <a name="register-sql-server-2008r2-on-windows-server-2008-vms"></a>Registrera SQL Server 2008/R2 på virtuella Windows Server 2008-datorer

SQL Server 2008 och 2008 R2 som är installerade på Windows Server 2008 kan registreras med den virtuella SQL-resursen i [](virtual-machines-windows-sql-server-agent-extension.md) läget noagent. Det här alternativet säkerställer efterlevnad och tillåter att SQL Server VM övervakas i Azure Portal med begränsad funktionalitet.

I följande tabell visas de acceptabla värdena för de parametrar som angavs under registreringen:

| Parameter | Acceptabla värden                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `'AHUB'`eller`'PAYG'`                    |
| **sqlImageOffer**  | `'SQL2008-WS2008'` eller `'SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Om du vill registrera din SQL Server 2008 eller 2008 R2 på Windows Server 2008-instansen använder du följande PowerShell-kodfragment:  

  ```powershell-interactive
     # Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

## <a name="verify-registration-status"></a>Verifiera registrerings status
Du kan kontrol lera om din SQL Server redan har registrerats med providern för SQL-VM-resursen med hjälp av Azure Portal, Azure CLI eller PowerShell. 

### <a name="azure-portal"></a>Azure Portal 
Gör så här för att kontrol lera statusen för registreringen med hjälp av Azure Portal:

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Navigera till dina [virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md).
1. Välj din SQL Server VM i listan. Om din SQL Server VM inte visas här, är det troligt att ditt SQL Server VM inte har registrerats med resurs leverantören för SQL-VM. 
1. Visa värdet under `Status`. Om `Status = Succeeded`har SQL Server VM registrerats med den virtuella SQL-adressresursen. 

    ![Verifiera status med SQL RP-registrering](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="az-cli"></a>Az CLI

Verifiera aktuell SQL Server VM registrerings status med följande AZ CLI-kommando. `ProvisioningState`visar `Succeeded` om registreringen lyckades. 

  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
  ```


### <a name="powershell"></a>PowerShell

Verifiera aktuell SQL Server VM registrerings status med följande PowerShell-cmdlet. `ProvisioningState`visar `Succeeded` om registreringen lyckades. 

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```
Ett fel indikerar att SQL Server VM inte har registrerats hos resurs leverantören. 

---

## <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrera SQL VM Resource Provider med prenumeration 

Du måste registrera resurs leverantören för din prenumeration för att kunna registrera SQL Server VM med den virtuella SQL-adressresursen. Du kan göra det med Azure Portal eller Azure CLI.

### <a name="azure-portal"></a>Azure Portal

Följande steg registrerar providern för SQL-VM-resursen i Azure-prenumerationen med hjälp av Azure Portal. 

1. Öppna Azure Portal och navigera till **alla tjänster**. 
1. Gå till **prenumerationer** och välj en prenumeration på intresse.  
1. På sidan **prenumerationer** navigerar du till **resurs leverantörer**. 
1. Skriv `sql` in filtret för att ta fram SQL-relaterade resurs leverantörer. 
1. Välj antingen *Registrera*, *Registrera*om eller avregistrera *dig* för **Microsoft. SqlVirtualMachine** -providern beroende på vilken åtgärd du önskar. 

   ![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

### <a name="az-cli"></a>Az CLI
Följande kodfragment registrerar SQL VM-adressresursen till din Azure-prenumeration. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

### <a name="powershell"></a>PowerShell

Följande PowerShell-kodfragment registrerar providern för SQL-VM-resursen i din Azure-prenumeration.

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Kommentarer

 - Resurs leverantören för SQL-VM stöder bara SQL Server virtuella datorer som har distribuerats med Resource Manager. SQL Server virtuella datorer som distribueras med den klassiska modellen stöds inte. 
 - Resurs leverantören för SQL-VM stöder bara SQL Server virtuella datorer som distribuerats till det offentliga molnet. Distributioner till det privata molnet eller det offentliga molnet stöds inte. 
 
## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

**Bör jag registrera mina SQL Server VM som tillhandahålls från en SQL-avbildning på Azure Marketplace?**

Nej. Microsoft registrerar virtuella datorer som tillhandahålls från SQL-avbildningarna på Azure Marketplace. Registrering med SQL VM Resource Provider krävs bara om den virtuella datorn inte har tillhandahållits från SQL-avbildningarna på Azure Marketplace och SQL Server var själv installerad.

**Är resursprovidern för virtuell SQL-dator tillgänglig för alla kunder?** 

Ja. Kunderna bör registrera sina SQL Server VM med en SQL VM-adressresurs om de inte använde en SQL Server avbildning från Azure Marketplace och själv installerad SQL Server eller har gjort sin anpassade virtuella hård disk. Virtuella datorer som ägs av alla typer av prenumerationer (Direct, EA och CSP) kan registreras med en SQL VM-adressresurs.

**Bör jag registrera mig för SQL VM Resource Provider om mitt SQL Server VM redan har installerat SQL IaaS-tillägget?**

Om din SQL Server VM är självinstallerad, inte etablerad från SQL-avbildningarna på Azure Marketplace, bör du registrera dig för SQL VM Resource Provider även om du har installerat SQL IaaS-tillägget. Registrering med en SQL VM-resurspool skapar en ny resurs av typen Microsoft. SqlVirtualMachines. Att installera SQL IaaS-tillägget skapar inte den resursen.

**Vad är standard läget för SQL-hantering vid registrering med en SQL VM-resurs leverantör?**

Standard läge för SQL-hantering vid registrering med SQL VM RP är _full_. Om egenskapen för SQL-hantering inte har angetts vid registrering med SQL VM Resource Provider, anges läget som fullständig hanterbarhet. Om SQL IaaS-tillägget är installerat på den virtuella datorn är det nödvändigt att registrera med resurs leverantören för SQL-VM i fullständigt hanterings läge.

**Vilka är de nödvändiga förutsättningarna för att registrera med en SQL VM-resurs leverantör?**

Det finns inga krav för att registrera med resurs leverantören för SQL-VM i läget för förenklad eller No-agent. För hands kraven för att registrera med resurspoolen för SQL VM-resursen i fullständigt läge har SQL IaaS-tillägget installerat på den virtuella datorn.

**Kan jag registrera med den virtuella SQL-IaaS om jag inte har installerat SQL-tillägget på den virtuella datorn?**

Ja, du kan registrera dig för en SQL VM-resurspool i läget för förenklad hantering om du inte har installerat SQL IaaS-tillägget på den virtuella datorn. I Lightweight-läge kommer den virtuella SQL-datorn att använda en-konsol för att kontrol lera version och utgåva av SQL-instansen. Konsol programmet stängs av när det har verifierats att det finns minst en SQL-instans som körs på den virtuella datorn. Registrering med en SQL VM-adressresurs i Lightweight-läge kommer inte att startas om SQL Server och ingen agent skapas på den virtuella datorn.

**Kommer registreringen med SQL VM Resource Provider att installera en agent på den virtuella datorn?**

Nej. Registrering med SQL VM Resource Provider kommer bara att skapa en ny metadata-resurs och kommer inte att installera en agent på den virtuella datorn. SQL IaaS-tillägget krävs bara för att aktivera fullständig hanterbarhet, så uppgradering av hanterbarhets läget från Lightweight till full kommer att installera SQL IaaS-tillägget och startas om SQL Server.

**Kommer registreringen med SQL VM Resource Provider att startas om SQL Server på den virtuella datorn?**

Nej. Registrering med SQL VM Resource Provider skapar bara en ny metadata-resurs och startar inte om SQL Server på den virtuella datorn. Det krävs bara att starta om SQL Server för att installera SQL IaaS-tillägget; och SQL IaaS-tillägget krävs för att aktivera fullständig hanterbarhet. Om du uppgraderar hanterbarhets läget från Lightweight till full installeras SQL IaaS-tillägget och startas om SQL Server.

**Vad är skillnaden mellan självbetjänings-och hanterings lägen vid registrering med SQL VM Resource Provider?** 

Hanterings läget No-agent är bara tillgängligt för SQL Server 2008/R2 på Windows Server 2008; och det är det enda tillgängliga hanterings läget för dessa versioner. För alla andra versioner av SQL Server är de två hanterings lägena som är tillgängliga som enkla och fullständiga. Inget-agent läge kräver att SQL Server versions-och versions egenskaper anges av kunden. i läget för lätta från den virtuella datorn hittar du version och utgåva av SQL-instansen.

**Kan jag registrera mig för en SQL VM-resurspool i läget för förenklad eller ingen agent med Azure CLI?**

Nej. Egenskapen SQL Management mode är bara tillgänglig när du registrerar med en SQL VM-adressresurs med Azure PowerShell. Azure CLI stöder inte inställning av SQL hanterbarhets-egenskap och registreras alltid med resurs leverantören för SQL-VM i standard läget – fullständig hanterbarhet.

**Kan jag registrera med en SQL VM-resurspool utan att ange typen av SQL-licens?**

Nej. SQL-licens typen är inte en valfri egenskap vid registrering med SQL VM RP. Du måste ange SQL-licensserver som PAYG eller AHUB när du registrerar med en SQL VM-adressresurs i alla hanterbarhets lägen (inga agenter, lätta och fullständiga) både med AZ CLI och PowerShell.

**Kan jag uppgradera SQL IaaS-tillägget från No-agent-läge till fullständigt läge?**

Nej. Att uppgradera SQL hanterbarhets mode till full eller Lightweight är inte tillgängligt för läget No-agent. Detta är en teknisk begränsning i Windows Server 2008.

**Kan jag uppgradera SQL IaaS-tillägget från Lightweight-läge till fullständigt läge?**

Ja. Det går att uppgradera SQL hanterbarhets mode från Lightweight till full, via PowerShell eller Azure Portal; och kräver att SQL Server startas om.

**Kan jag nedgradera SQL IaaS-tillägget från hel skärms läge till inga agent-eller Lightweight Management-lägen?**

Nej. Det finns inte stöd för att nedgradera SQL IaaS-tillägg för hanterings läge. Läge för läge för SQL-hantering kan inte nedgraderas från fullständigt läge till läget för lätt läge eller No-agent. och kan inte nedgraderas från läget Lightweight till No-agent. Ändra hanterbarhets läget från fullständig hanterbarhet; ta bort SQL IaaS-tillägget; släpp Microsoft. SqlVirtualMachine-resurs och registrera om SQL Server VM med providern för SQL VM-resursen.

**Kan jag registrera mig för en SQL VM-adressresurs från Azure Portal?**

Nej. Registrering med SQL VM Resource Provider är inte tillgänglig i Azure Portal. Registrering med SQL VM Resource Provider i fullständigt hanterbarhets läge stöds med Azure CLI eller PowerShell. och registrering med den virtuella SQL-resurs-providern i läget för enkel eller icke-agent hantering stöds bara av Azure PowerShell API: er.

**Kan jag registrera en virtuell dator med en virtuell SQL-resurs leverantör innan SQL Server installeras?**

Nej. Den virtuella datorn måste ha minst en SQL-instans för att kunna registreras med en SQL VM-resurs-Provider. Om det inte finns någon SQL-instans på den virtuella datorn, kommer den nya Micosoft. SqlVirtualMachine-resursen att vara i ett felaktigt tillstånd.

**Kan jag registrera en virtuell dator med en virtuell SQL-resurs om det finns flera SQL-instanser?**

Ja. En virtuell SQL-resurs-Provider registrerar bara en SQL-instans. SQL VM-adressresursen registrerar SQL-standardinstansen om flera instanser används. Om det inte finns någon standard instans, stöds bara registrering i Lightweight-läge. Om du vill uppgradera från läget för förenklad till fullständig hantering ska du antingen använda standard-SQL-instansen eller den virtuella datorn får bara ha en namngiven SQL-instans.

**Kan jag registrera en instans av SQL Server-redundanskluster med en SQL VM-resurs leverantör?**

Ja. SQL FCI-instanser på en virtuell Azure-dator kan registreras med resurs leverantören för SQL-VM i Lightweight-läge. SQL FCI-instanser kan dock inte uppgraderas till fullständigt hanterbarhets läge.

**Kan jag registrera min virtuella dator med SQL VM RP om Always ON-tillgänglighetsgrupper har kon figurer ATS?**

Ja. Det finns inga begränsningar för att registrera en SQL Server-instans på en virtuell Azure-dator med en SQL VM-adressresurs om du deltar i en konfiguration med alltid tillgänglighets grupp.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server vanliga frågor och svar om Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server om pris vägledning för Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server en versions information för Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
