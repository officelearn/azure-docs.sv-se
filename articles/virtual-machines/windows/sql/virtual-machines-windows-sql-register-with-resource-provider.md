---
title: Registrera en SQL Server virtuell dator i Azure med providern för SQL VM-resurs | Microsoft Docs
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
ms.openlocfilehash: a0e5076f6ecb102b239a94b986830235eb720125
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72512357"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrera en SQL Server virtuell dator i Azure med providern för SQL VM-resurs

Den här artikeln beskriver hur du registrerar SQL Server virtuell dator (VM) i Azure med providern för SQL VM-resurs. 

Genom att distribuera en SQL Server VM Azure Marketplace-avbildning via Azure Portal registreras SQL Server VM automatiskt med resurs leverantören. Om du väljer att själv installera SQL Server på en virtuell Azure-dator i stället för att välja en avbildning från Azure Marketplace, eller om du etablerar en virtuell Azure-dator från en anpassad virtuell hård disk med SQL Server, bör du registrera dina SQL Server VM med resurs leverantören för :

- **Förenkla licens hanteringen**: enligt Microsofts produkt villkor måste kunderna berätta för Microsoft när de använder [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/). Genom att registrera med den virtuella SQL-AZ kan du förenkla hanteringen av SQL Server licens hantering, och du kan snabbt identifiera SQL Server virtuella datorer med hjälp av Azure Hybrid-förmån i [portalen](virtual-machines-windows-sql-manage-portal.md) eller CLI: 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **Funktions förmåner**: om du registrerar SQL Server VM med Resource providern låser du upp [automatiserad uppdatering](virtual-machines-windows-sql-automated-patching.md), [Automatisk säkerhets kopiering](virtual-machines-windows-sql-automated-backup-v2.md)och övervakning och hanterings funktioner. Den låser också upp flexibiliteten för [licensiering](virtual-machines-windows-sql-ahb.md) och [utgåvor](virtual-machines-windows-sql-change-edition.md) . Tidigare var dessa funktioner bara tillgängliga för SQL Server VM avbildningar från Azure Marketplace.

- **Kostnads fri hantering**: om du registrerar med resurs leverantören för SQL VM-resursen och alla hanterbarhets lägen är helt kostnads fria. Det finns ingen ytterligare kostnad kopplad till resurs leverantören eller med ändring av hanterings lägen. 

Om du vill använda en SQL VM-adressresurs måste du också registrera SQL VM-resurspoolen med din prenumeration. Du kan göra detta med hjälp av Azure Portal, Azure CLI eller PowerShell. 

  > [!NOTE]
  > Det finns inga ytterligare licensierings krav kopplade till registrering med resurs leverantören. Registrering med SQL VM Resource providern erbjuder en förenklad metod för att uppfylla kravet på att meddela Microsoft att Azure Hybrid-förmån har Aktiver ATS i stället för att hantera licens registrerings formulär för varje resurs. 

Mer information om fördelarna med att använda providern för SQL VM-resursen finns i följande [channel9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) -video: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Dra nytta av SQL VM Resource Provider när du själv installerar SQL Server på Azure-Microsoft Channel 9-video"></iframe>


## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna registrera SQL Server VM med resurs leverantören: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller [PowerShell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Registrera dig för SQL VM Resource Provider
Om [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md) inte är installerat på den virtuella datorn, kan du registrera dig för SQL VM-adressresursen genom att ange läget för förenklad SQL-hantering. 

När Lightweight anges under registrerings processen installeras SQL IaaS-tillägget automatiskt i [Lightweight-läge](#change-management-modes) och verifierar SQL Server instansens metadata. Detta kommer inte att startas om SQL Server-tjänsten. Du måste ange vilken typ av SQL Server licens du vill när du registrerar med en SQL VM-adressresurs som antingen "PAYG" eller "AHUB".

Registrering med den virtuella SQL-adressresursen i Lightweight-läge garanterar efterlevnad och möjliggör flexibel licensiering samt SQL Server uppdateringar på plats. Kluster instanser för växling vid fel och distributioner med flera instanser kan bara registreras med en SQL VM-adressresurs i Lightweight-läge. Du kan när som helst [Uppgradera](#change-management-modes) till ett fullständigt hanterings läge, men om du gör det startas tjänsten om SQL Server. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Använd följande kodfragment för att registrera dig hos resurs leverantören för SQL-VM om SQL Server IaaS-tillägget redan är installerat på den virtuella datorn. Du måste ange den typ av SQL Server-licens som du vill använda när du registrerar dig för SQL VM-resurs leverantören: betala per användning (`PAYG`) eller Azure Hybrid-förmån (`AHUB`). 

Registrera SQL Server VM med hjälp av följande PowerShell-kodfragment:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

För betalda utgåvor (Enterprise eller standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

För kostnads fria versioner (utvecklare, webb eller Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Om SQL IaaS-tillägget har installerats på den virtuella datorn manuellt kan du registrera dig för en SQL VM-adressresurs i fullständigt läge genom att helt enkelt skapa en metadata-resurs av typen Microsoft. SqlVirtualMachine/SqlVirtualMachines. Nedan visas kodfragmentet som ska registreras med en SQL VM-adressresurs om SQL IaaS-tillägget redan är installerat på den virtuella datorn. Du måste ange den typ av SQL Server-licens som du vill använda som antingen "PAYG" eller "AHUB". Om du vill registrera dig i fullständigt hanterings läge använder du följande PowerShell-kommando:

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registrera SQL Server 2008 eller 2008 R2 på virtuella datorer med Windows Server 2008

SQL Server 2008 och 2008 R2 som är installerade på Windows Server 2008 kan registreras med resurs leverantören för SQL-VM i [no-agent-läge](#change-management-modes). Det här alternativet säkerställer efterlevnad och tillåter att SQL Server VM övervakas i Azure Portal med begränsad funktionalitet.

I följande tabell visas de acceptabla värdena för de parametrar som angavs under registreringen:

| Parameter | Acceptabla värden                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` eller `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` eller `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


För att registrera din SQL Server 2008-eller 2008 R2-instans på Windows Server 2008-instansen använder du följande PowerShell-eller AZ CLI-kodfragment:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Verifiera registrerings status
Du kan kontrol lera om din SQL Server VM redan har registrerats med resurs leverantören för SQL-VM med hjälp av Azure Portal, Azure CLI eller PowerShell. 

### <a name="azure-portal"></a>Azure portal 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till dina [SQL Server virtuella datorer](virtual-machines-windows-sql-manage-portal.md).
1. Välj din SQL Server VM i listan. Om din SQL Server VM inte visas här, har den antagligen inte registrerats med den virtuella SQL-resurs leverantören. 
1. Visa värdet under **status**. Om **statusen** är **klar**har SQL Server VM registrerats med den virtuella SQL-adressresursen. 

![Verifiera status med SQL RP-registrering](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Kommandoraden

Verifiera aktuell SQL Server VM registrerings status med antingen AZ CLI eller PowerShell. `ProvisioningState` visas `Succeeded` om registreringen lyckades. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Ett fel indikerar att SQL Server VM inte har registrerats hos resurs leverantören. 

## <a name="change-management-modes"></a>Ändra hanterings lägen

Det finns tre kostnads fria hanterings lägen för SQL Server IaaS-tillägget: 

- **Fullständigt** läge ger alla funktioner, men kräver omstart av SQL Server-och system administratörs behörighet. Detta är det alternativ som installeras som standard. Använd den för att hantera en SQL Server VM med en enda instans. I fullständigt läge installeras två Windows-tjänster som har minimal påverkan på minne och CPU – dessa kan övervakas via aktivitets hanteraren. Det finns ingen kostnad för att använda läget fullständig hanterbarhet. 

- **Lightweight** kräver inte omstart av SQL Server, men den stöder bara ändring av licens typ och utgåva av SQL Server. Använd det här alternativet för SQL Server virtuella datorer med flera instanser eller för deltagande i en instans av en redundanskluster (FCI). Det går inte att påverka minne eller CPU när du använder Lightweight-läge. Det finns ingen kostnad för att använda läget för förenklad hanterbarhet. 

- **Noagent** är dedikerat till SQL Server 2008 och SQL Server 2008 R2 installerat på Windows Server 2008. Det påverkar inte minne eller CPU när du använder noagent-läge. Det finns ingen kostnad för att använda noagent Managed mode. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten med hjälp av PowerShell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server virtuella datorer som har tillägget *Lightweight* IaaS installerat kan uppgradera läget till _full_ med hjälp av Azure Portal. SQL Server virtuella datorer i _no-agent-_ läge kan uppgraderas till _full_ efter att operativ systemet har uppgraderats till Windows 2008 R2 och senare. Det går inte att nedgradera – om du vill göra det måste du ta bort resurs leverantörs resursen för SQL-resursen med hjälp av Azure Portal och registrera dig för den virtuella SQL VM-providern igen. 

Så här uppgraderar du agentens läge till fullständigt: 


### <a name="azure-portal"></a>Azure portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till resursen för [virtuella SQL-datorer](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Välj din SQL Server virtuella dator och välj **Översikt**. 
1. För SQL Server virtuella datorer med noagent-eller Lightweight IaaS-läge väljer du de **enda licens typ-och versions uppdateringar som är tillgängliga med meddelandet SQL IaaS-tillägg** .

   ![Val för att ändra läget från portalen](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Markera kryss rutan **Jag accepterar att starta om SQL Server tjänsten på den virtuella datorn** och välj sedan **Bekräfta** för att uppgradera IaaS-läget till full. 

    ![Kryss ruta för att komma överens om att starta om SQL Server tjänsten på den virtuella datorn](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Kommandoraden

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Kör följande AZ CLI-kodfragment:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Kör följande PowerShell-kodfragment:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```
---

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>Registrera providern för SQL VM-resursen med en prenumeration 

Du måste registrera resurs leverantören med din prenumeration för att kunna registrera SQL Server VM med den virtuella SQL-adressresursen. Du kan göra detta med hjälp av Azure Portal, Azure CLI eller PowerShell.

### <a name="azure-portal"></a>Azure portal

1. Öppna Azure Portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj en prenumeration på intresse.  
1. På sidan **prenumerationer** går du till **Resource providers**. 
1. Ange **SQL** i filtret för att ta fram SQL-relaterade resurs leverantörer. 
1. Välj **Registrera**, **Registrera**om eller **avregistrera** för **Microsoft. SqlVirtualMachine** -providern, beroende på vilken åtgärd du önskar. 

![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Kommandoraden

Registrera din SQL VM-adressresurs till din Azure-prenumeration med hjälp av antingen AZ CLI eller PowerShell. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Följande kodfragment registrerar SQL VM-adressresursen till din Azure-prenumeration. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="remarks"></a>Kommentarer

- Resurs leverantören för SQL-VM stöder bara SQL Server virtuella datorer som distribueras via Azure Resource Manager. SQL Server virtuella datorer som distribueras via den klassiska modellen stöds inte. 
- Resurs leverantören för SQL-VM stöder endast SQL Server virtuella datorer som distribuerats till det offentliga molnet. Distributioner till det privata molnet eller myndighets molnet stöds inte. 
 

## <a name="frequently-asked-questions"></a>Vanliga frågor 

**Bör jag registrera mina SQL Server VM som tillhandahålls från en SQL Server-avbildning på Azure Marketplace?**

Nej. Microsoft registrerar automatiskt virtuella datorer som tillhandahålls från SQL Server avbildningar på Azure Marketplace. Registrering med SQL VM Resource Provider krävs bara om den virtuella datorn *inte* har tillhandahållits från SQL Server avbildningar på Azure Marketplace och SQL Server var själv installerad.

**Är resursprovidern för virtuell SQL-dator tillgänglig för alla kunder?** 

Ja. Kunderna bör registrera sina SQL Server virtuella datorer med resurs leverantören för SQL-VM om de inte använde en SQL Server avbildning från Azure Marketplace och i stället själv installerade SQL Server, eller om de har gjort sin anpassade virtuella hård disk. Virtuella datorer som ägs av alla typer av prenumerationer (direkt, Enterprise-avtal och moln lösnings leverantör) kan registreras med providern för SQL VM-resurs.

**Bör jag registrera mig för SQL VM Resource Provider om SQL Server VM redan har SQL Server IaaS-tillägget installerat?**

Om din SQL Server VM är självinstallerad och inte tillhandahålls från SQL Server avbildningar på Azure Marketplace, bör du registrera dig för den virtuella SQL-adressresursen även om du har installerat SQL Server IaaS-tillägget. Om du registrerar med en SQL VM-adressresurs skapas en ny resurs av typen Microsoft. SqlVirtualMachines. Att installera SQL Server IaaS-tillägget skapar inte den resursen.

**Vad är standard hanterings läget vid registrering med den virtuella SQL-resurs leverantören?**

Standard hanterings läget när du registrerar med resurs leverantören för SQL-VM är *full*. Om egenskapen SQL Server hantering inte har angetts när du registrerar dig hos resurs leverantören för SQL-VM, anges läget som fullständig hanterbarhet. Att ha SQL Server IaaS-tillägget installerat på den virtuella datorn är förutsättningen att du registrerar med resurs leverantören för SQL-VM i fullständigt hanterbarhets läge.

**Vilka är kraven för att registreras med resurs leverantören för den virtuella SQL-datorn?**

Det finns inga krav för att registrera med resurs leverantören för SQL-VM i läget för förenklad eller No-agent. Förutsättningen för att registrera med resurspoolen för SQL VM-IaaS i fullständigt läge har SQL Server-tillägget installerat på den virtuella datorn.

**Kan jag registrera med resurs leverantören för SQL-VM om jag inte har installerat SQL Server IaaS-tillägget på den virtuella datorn?**

Ja, du kan registrera dig för SQL VM Resource Provider i läget för förenklad hantering om du inte har installerat SQL Server IaaS-tillägget på den virtuella datorn. I Lightweight-läge kommer den virtuella SQL-adressresursen att använda en-konsol för att kontrol lera versionen och versionen av SQL Server-instansen. 

Standard läget för SQL-hantering vid registrering med SQL VM Resource Provider är _full_. Om egenskapen för SQL-hantering inte har angetts vid registrering med SQL VM Resource Provider, anges läget som fullständig hanterbarhet. Om SQL IaaS-tillägget är installerat på den virtuella datorn är det nödvändigt att registrera med resurs leverantören för SQL-VM i fullständigt hanterings läge.

**Kommer att registreras med den virtuella SQL-adressresursen installera en agent på den virtuella datorn?**

Nej. Vid registrering med den virtuella SQL-adressresursen skapas endast en ny metadata-resurs. Den installerar inte en agent på den virtuella datorn.

SQL Server IaaS-tillägget krävs bara för att aktivera fullständig hanterbarhet. Om du uppgraderar hanterbarhets läget från Lightweight till full installeras SQL Server IaaS-tillägget och startas om SQL Server.

**Kommer att registreras med SQL Server VM Resource Provider starta om SQL Server på den virtuella datorn?**

Nej. Vid registrering med den virtuella SQL-adressresursen skapas endast en ny metadata-resurs. Den startar inte om SQL Server på den virtuella datorn. 

Det krävs bara att starta om SQL Server för att installera SQL Server IaaS-tillägget. Och SQL Server IaaS-tillägget krävs för att aktivera fullständig hanterbarhet. Om du uppgraderar hanterbarhets läget från Lightweight till full installeras SQL Server IaaS-tillägget och startas om SQL Server.

**Vad är skillnaden mellan enkla och inga hanterings lägen vid registrering med den virtuella SQL-adressresursen?** 

Hanterings läget No-agent är bara tillgängligt för SQL Server 2008 och SQL Server 2008 R2 på Windows Server 2008. Det är det enda tillgängliga hanterings läget för de här versionerna. För alla andra versioner av SQL Server är de två tillgängliga lägena för hanterbarhet lätta och fullständiga. 

Inget-agent läge kräver att SQL Server versions-och versions egenskaper anges av kunden. I läget för lätta från den virtuella datorn hittar du versionen och versionen av SQL Server-instansen.

**Kan jag registrera mig för resurs leverantören för SQL-VM i läget för förenklad eller No-agent med hjälp av Azure CLI?**

Nej. Egenskapen hanterings läge är bara tillgänglig när du registrerar dig för en SQL VM-adressresurs genom att använda Azure PowerShell. Azure CLI stöder inte inställning av egenskapen SQL Server hanterbarhet. Den registreras alltid med resurs leverantören för SQL-VM i standard läget, fullständig hanterbarhet.

**Kan jag registrera med den virtuella SQL-adressresursen utan att ange licens typen SQL Server?**

Nej. Den SQL Server licens typen är inte en valfri egenskap när du registrerar med providern för SQL VM-resursen. Du måste ange SQL Server licens typ som du betalar per användning eller Azure Hybrid-förmån när du registrerar med resurs leverantören för SQL-VM i alla hanterbarhets lägen (inga agenter, lätta och fullständiga) med hjälp av både Azure CLI och PowerShell.

**Kan jag uppgradera SQL Server IaaS-tillägget från inget agent läge till fullständigt läge?**

Nej. Uppgradering av hanterbarhets läget till full eller Lightweight är inte tillgängligt för läget No-agent. Detta är en teknisk begränsning i Windows Server 2008.

**Kan jag uppgradera SQL Server IaaS-tillägget från förenklat läge till fullständigt läge?**

Ja. Uppgradering av hanterbarhets läget från Lightweight till full stöds via PowerShell eller Azure Portal. Det krävs en omstart SQL Server.

**Kan jag nedgradera SQL Server IaaS-tillägget från fullständigt läge till No-agent eller Lightweight Management mode?**

Nej. Det finns inte stöd för att nedgradera SQL Server IaaS-tilläggets hanterbarhets läge. Läget för hanterbarhets kan inte nedgraderas från fullständigt läge till läget för förenklad eller No-agent, och det kan inte nedgraderas från läget Lightweight till No-agent. 

Om du vill ändra hanterbarhets läget från fullständig hanterbarhet, släpper du Microsoft. SqlVirtualMachine-resursen och omregistrerar SQL Server VM med resurspoolen för SQL VM-providern.

**Kan jag registrera med resurs leverantören för SQL-VM från Azure Portal?**

Nej. Registrering med providern för SQL VM-resursen är inte tillgänglig i Azure Portal. Registrering med den virtuella SQL-adressresursen i läget fullständig hanterbarhet stöds med Azure CLI eller PowerShell. Registrering med den virtuella SQL-resurs-providern i läget för förenklad eller No-agent hantering stöds endast av Azure PowerShell API: er.

**Kan jag registrera en virtuell dator med resurs leverantören för SQL-VM innan SQL Server installeras?**

Nej. En virtuell dator måste ha minst en SQL Server instans för att kunna registreras hos resurs leverantören för SQL-VM. Om det inte finns någon SQL Server instans på den virtuella datorn, kommer den nya Microsoft. SqlVirtualMachine-resursen att vara i ett felaktigt tillstånd.

**Kan jag registrera en virtuell dator med resurs leverantören för SQL-VM om det finns flera SQL Server instanser?**

Ja. Resurs leverantören för SQL-VM registrerar bara en SQL Server instans. Resurs leverantören för SQL-VM registrerar standard SQL Server-instansen om flera instanser används. Om det inte finns någon standard instans, stöds bara registrering i Lightweight-läge. Om du vill uppgradera från läget för förenklad till fullständig hantering måste du antingen använda standard SQL Server-instansen eller den virtuella datorn får bara ha en namngiven SQL Server-instans.

**Kan jag registrera en instans av SQL Server-redundanskluster med den virtuella SQL-adressresursen?**

Ja. SQL Server instanser av kluster för växling vid fel på en virtuell Azure-dator kan registreras med resurs leverantören för SQL-VM i lättviktigt läge. SQL Server kan dock inte uppgraderas till läget fullständig hanterbarhet.

**Kan jag registrera min virtuella dator med resurs leverantören för SQL-VM om en Always on-tillgänglighetsgruppen är konfigurerad?**

Ja. Det finns inga begränsningar för att registrera en SQL Server-instans på en virtuell Azure-dator med resurs leverantören för SQL-VM om du deltar i en konfiguration med Always on-tillgänglighetsgrupper.

**Vad kostar det att registrera med den virtuella SQL VM-adressresursen eller genom att uppgradera till fullständigt hanterbarhets läge?**
Inget. Det finns ingen avgift kopplad till registrering med den virtuella SQL VM-adressresursen eller med något av de tre hanterbarhets lägena. Att hantera SQL Server VM med Resource providern är helt kostnads fritt. 

**Vilken prestanda påverkas av att använda olika hanterbarhets lägen?**
Det påverkas inte när du använder *Noagent* -och *Lightweight* hanterbarhets läge. Det finns minimal påverkan när du använder läget *fullständig* hantering från två tjänster som är installerade på operativ systemet. Dessa kan övervakas via aktivitets hanteraren. 

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)
