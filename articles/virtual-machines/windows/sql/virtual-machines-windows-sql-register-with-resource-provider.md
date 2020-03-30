---
title: Registrera dig hos SQL VM-resursprovidern
description: Registrera din virtuella Azure SQL Server-dator med SQL VM-resursprovidern för att aktivera funktioner för virtuella SQL Server-datorer som distribueras utanför Azure Marketplace, samt efterlevnad och förbättrad hanterbarhet.
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 01e683e31905281d25fdcf976bc58397c052a6c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243190"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Registrera en virtuell SQL Server-dator i Azure med SQL VM-resursprovidern

I den här artikeln beskrivs hur du registrerar den virtuella datorn för SQL Server (VM) i Azure med SQL VM-resursprovidern. Om du registrerar dig hos resursleverantören skapas **sql-resursen för virtuella datorer** _resource_ i prenumerationen, som är en separat resurs från resursen för den virtuella datorn. Om du avregistrerar sql server-datorn från resursprovidern tas **SQL-resursen** _resource_ bort, men den faktiska virtuella datorn kommer inte att släppas. 

Distribuera en SQL Server VM Azure Marketplace avbildning via Azure-portalen registrerar automatiskt SQL Server VM med resursprovidern. Om du väljer att själv installera SQL Server på en virtuell Azure-dator eller etablera en virtuell Azure-dator från en anpassad virtuell hårddisk, bör du registrera din virtuella SQL Server-dator med resursprovidern för:

- **Funktionsfördelar:** Om du registrerar den virtuella datorn för SQL Server med resursleverantören låses [automatisk korrigering,](virtual-machines-windows-sql-automated-patching.md) [automatisk säkerhetskopiering](virtual-machines-windows-sql-automated-backup-v2.md)samt övervaknings- och hanterbarhetsfunktioner upp. Det låser också upp [licensiering](virtual-machines-windows-sql-ahb.md) och [utgåva](virtual-machines-windows-sql-change-edition.md) flexibilitet. Tidigare var dessa funktioner endast tillgängliga för SQL Server VM-avbildningar som distribuerades från Azure Marketplace. 

- **Efterlevnad**: Registrering med SQL VM-resursprovidern erbjuder en förenklad metod för att uppfylla kravet på att meddela Microsoft att Azure Hybrid-förmånen har aktiverats enligt produkttermerna. Den här processen förnekar behovet av att hantera licensiering registreringsformulär för varje resurs.  

- **Kostnadsfri hantering:** Det är helt gratis att registrera sig hos SQL VM-resursprovidern i alla tre hanterbarhetslägen. Det finns ingen extra kostnad som är associerad med resursleverantören eller med ändrade hanteringslägen. 

- **Förenklad licenshantering:** Registrering med SQL VM-resursprovidern förenklar SQL Server-licenshanteringen och gör att du snabbt kan identifiera virtuella SQL Server-datorer med Azure Hybrid Benefit aktiverat med [Azure-portalen,](virtual-machines-windows-sql-manage-portal.md)Az CLI eller PowerShell: 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[Powershell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Om du vill använda SQL VM-resursprovidern måste du först [registrera prenumerationen hos resursleverantören](#register-subscription-with-rp), vilket ger resursleverantören möjlighet att skapa resurser inom den specifika prenumerationen.

Mer information om fördelarna med att använda SQL VM-resursprovidern finns i den här [channel9-videon:](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Dra nytta av SQL VM Resource Provider vid självinstallation av SQL Server på Azure - Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Krav

Om du vill registrera din virtuella SQL Server-dator med resursleverantören behöver du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En virtuell [azure](virtual-machines-windows-portal-sql-server-provision.md) resource-modell SQL Server som distribueras till det offentliga molnet eller Azure Government-molnet. 
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Hanteringslägen

Om [SQL IaaS-tillägget](virtual-machines-windows-sql-server-agent-extension.md) inte redan har installerats installeras SQL VM-resursprovidern automatiskt i SQL Server IaaS-tillägget i ett av tre hanteringslägen som anges under registreringsprocessen. Om du inte anger hanteringsläget installeras SQL IaaS-tillägget i fullständigt hanteringsläge.  

Om SQL IaaS-tillägget redan har installerats manuellt är det redan i fullständigt hanteringsläge och registreringen med resursprovidern i fullt läge startar inte om SQL Server-tjänsten.

De tre hanteringslägena är:

- **Lightweight** mode kräver inte omstart av SQL Server, men stöder bara ändra licenstyp och utgåva av SQL Server. Använd det här alternativet för virtuella SQL Server-datorer med flera instanser eller som deltar i en REDUNDAnsklusterinstans (FCI). Det finns ingen inverkan på minne eller CPU när du använder lightweight-läget, och det finns ingen tillhörande kostnad. Vi rekommenderar att du först registrerar den virtuella datorn för SQL Server i lättviktsläge och sedan uppgraderar till fullständigt läge under ett schemalagt underhållsfönster.  

- **Fullständigt** läge ger alla funktioner, men kräver en omstart av SQL Server- och systemadministratörsbehörigheterna. Detta är det alternativ som installeras som standard när du installerar SQL IaaS-tillägget manuellt. Använd den för att hantera en VIRTUELL SQL Server med en enda instans. I fullständigt läge installeras två Windows-tjänster som har minimal inverkan på minne och CPU - dessa kan övervakas via Aktivitetshanteraren. Det finns ingen kostnad i samband med att använda det fullständiga hanterbarhetsläget. 

- **NoAgent-läget** är dedikerat till SQL Server 2008 och SQL Server 2008 R2 installerat på Windows Server 2008. Det finns ingen inverkan på minne eller CPU när du använder NoAgent-läget. Det finns ingen kostnad i samband med att använda noagent hanterbarhetsläget. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten med PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Registrera prenumeration med RP

Om du vill registrera din VIRTUELLA SQL Server-dator med SQL VM-resursprovidern måste du först registrera prenumerationen hos resursprovidern. Detta ger SQL VM-resursprovidern möjlighet att skapa resurser i din prenumeration.  Du kan göra det med hjälp av Azure-portalen, Azure CLI eller PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Öppna Azure-portalen och gå till **Alla tjänster**. 
1. Gå till **Prenumerationer** och välj prenumeration av intresse.  
1. Gå till **Resursleverantörer**på sidan **Prenumerationer** . 
1. Ange **sql** i filtret för att visa sql-relaterade resursleverantörer. 
1. Välj **Registrera,** registrera om eller **Avregistrera** dig för **Microsoft.SqlVirtualMachine-leverantören,** beroende på önskad åtgärd. **Re-register** 

![Ändra providern](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Kommandorad

Registrera din SQL VM-resursleverantör till din Azure-prenumeration med antingen Az CLI eller PowerShell. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>Registrera SQL VM med RP 

### <a name="lightweight-management-mode"></a>Lättviktshanteringsläge

Om [SQL Server IaaS-agenttillägget](virtual-machines-windows-sql-server-agent-extension.md) inte har installerats på den virtuella datorn är rekommendationen att registrera sig hos SQL VM-resursprovidern i lättviktsläge. Detta kommer att installera SQL IaaS-tillägget i [lättläge](#management-modes) och förhindra att SQL Server-tjänsten startas om. Du kan sedan uppgradera till fullt läge när som helst, men om du gör det startas SQL Server-tjänsten om så att det rekommenderas att vänta tills ett schemalagt underhållsfönster. 

Ange SQL Server-licenstyp som antingen användningsbaserad betalning`PAYG`( ) för`AHUB`att betala per användning, Azure`DR`Hybrid Benefit ( ) för att använda din egen licens eller haveriberedskap ( ) för att aktivera den [kostnadsfria DR-repliklicensen](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure).

Redundansklusterinstanser och multiinstansdistributioner kan bara registreras med SQL VM-resursprovidern i lättviktsläge. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registrera VIRTUELL SQL Server i lättviktsläge med Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[Powershell](#tab/powershell)

Registrera virtuell SQL Server i lättläge med PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Fullständigt hanteringsläge


Om SQL IaaS-tillägget redan har installerats på den virtuella datorn manuellt kan du registrera DEN VIRTUELLA SQL Server-datorn i fullt läge utan att starta om SQL Server-tjänsten. **Men om SQL IaaS-tillägget inte har installerats, kommer registrering i fullt läge att installera SQL IaaS-tillägget i fullt läge och starta om SQL Server-tjänsten. Var snäll och var försiktig.**


Om du vill registrera din VIRTUELLA SQL Server direkt i fullt läge (och eventuellt starta om SQL Server-tjänsten) använder du följande PowerShell-kommando: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Hanteringsläge för NoAgent 

SQL Server 2008 och 2008 R2 installerat på Windows Server 2008 _(inte R2_) kan registreras med SQL VM-resursprovidern i [noagentläge](#management-modes). Det här alternativet säkerställer efterlevnad och gör att SQL Server-virtuella datorer kan övervakas i Azure-portalen med begränsad funktionalitet.

Ange `AHUB`antingen `PAYG`, `DR` , eller som **sqlLicenseType**och antingen `SQL2008-WS2008` eller `SQL2008R2-WS2008` som **sqlImageOffer**. 

Om du vill registrera SQL Server 2008- eller 2008 R2-instansen i Windows Server 2008 använder du följande Az CLI- eller PowerShell-kodavsnitt: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registrera din virtuella SQL Server 2008-dator i NoAgent-läge med Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```
 
 
Registrera din VIRTUELLA SQL Server 2008 R2 i NoAgent-läge med Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008R2
 ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Registrera virtuell DATOR FÖR SQL Server 2008 i NoAgent-läge med PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registrera VIRTUELL SQL Server 2008 R2 i NoAgent-läge med PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Uppgradera till fullständigt hanteringsläge 

Virtuella datorer med SQL Server som har det lätta IaaS-tillägget installerat kan uppgradera läget till _fullo_ med Azure-portalen, Az CLI eller PowerShell. *lightweight* Virtuella SQL Server-datorer i _NoAgent-läge_ kan uppgraderas till _full_ när operativsystemet har uppgraderats till Windows 2008 R2 och senare. Det går inte att nedgradera - för att göra det måste du [avregistrera](#unregister-vm-from-rp) SQL Server-vm-datorn från SQL VM-resursprovidern. Om du gör det tas **resursen för virtuella SQL-datorer** _resource_bort, men den faktiska virtuella datorn tas inte bort. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten med PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Så här uppgraderar du agentläget till fullt: 


### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till [resursen virtuella SQL-datorer.](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) 
1. Välj den virtuella SQL Server-datorn och välj **Översikt**. 
1. För virtuella SQL Server-datorer med noagent- eller lättläget IaaS väljer du de **enda licenstypen och utgåvorna är tillgängliga med sql IaaS-tilläggsmeddelandet.**

   ![Val för att ändra läget från portalen](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Markera kryssrutan **Jag godkänner att starta om SQL Server-tjänsten på den virtuella datorn** och välj sedan **Bekräfta** om du vill uppgradera IaaS-läget till fullo. 

    ![Kryssrutan om du vill komma överens om att starta om SQL Server-tjänsten på den virtuella datorn](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Kommandorad

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Kör följande Az CLI-kodavsnitt:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

Kör följande PowerShell-kodavsnitt:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verifiera registreringsstatus
Du kan kontrollera om din VIRTUELLA SQL Server redan har registrerats hos SQL VM-resursprovidern med hjälp av Azure-portalen, Azure CLI eller PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till [virtuella SQL Server-datorer](virtual-machines-windows-sql-manage-portal.md).
1. Välj din virtuella SQL Server-dator i listan. Om din VIRTUELLA SQL Server-dator inte finns med här har den troligen inte registrerats hos SQL VM-resursprovidern. 
1. Visa värdet under **Status**. Om **status** **har lyckats**har SQL Server-vm registrerats hos SQL VM-resursprovidern. 

![Verifiera status med SQL RP-registrering](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Kommandorad

Verifiera aktuell registreringsstatus för SQL Server-vm med antingen Az CLI eller PowerShell. `ProvisioningState`visas `Succeeded` om registreringen lyckades. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[Powershell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ett fel indikerar att SQL Server-virtuella datorer inte har registrerats hos resursprovidern. 


## <a name="unregister-vm-from-rp"></a>Avregistrera VM från RP

Om du vill avregistrera sql server-datorn med SQL VM-resursprovidern tar du bort SQL Virtual *Machine-resursen* med Azure-portalen eller Azure CLI. Om du tar bort SQL Virtual *Machine-resursen* tas inte SQL Server-datorn bort. Var dock försiktig och följ stegen noggrant eftersom det är möjligt att oavsiktligt ta bort den virtuella datorn när du försöker ta bort *resursen*. 

Avregistrera SQL VM med SQL VM-resursprovidern är nödvändigt för att nedgradera hanteringsläget från full. 

### <a name="azure-portal"></a>Azure Portal

Så här avregistrerar du din virtuella SQL Server-dator med resursleverantören med Azure-portalen:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Navigera till SQL Server VM-resursen. 
  
   ![Resurs för virtuella SQL-datorer](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Välj **Ta bort**. 

   ![Ta bort SQL VM-resursprovider](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Skriv namnet på den virtuella SQL-datorn och **avmarkera kryssrutan bredvid den virtuella datorn**.

   ![Ta bort SQL VM-resursprovider](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Om du inte avmarkerar kryssrutan bredvid namnet på den virtuella datorn tas den virtuella datorn *bort* helt. Avmarkera kryssrutan om du vill avregistrera SQL Server-datorn från resursprovidern men *ta inte bort den virtuella datorn*. 

1. Välj **Ta bort** för att bekräfta borttagningen av SQL-resursen för virtuella datorer och inte den virtuella SQL Server-datorn. *resource* 

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill avregistrera den virtuella SQL Server-datorn från resursprovidern med Azure CLI använder du kommandot [az sql vm delete.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Detta tar bort sql server-resursen *för* virtuella datorer, men tar inte bort den virtuella datorn. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Om du vill avregistrera den virtuella SQL Server-datorn från resursprovidern med Azure CLI använder du kommandot [Ny-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Detta tar bort sql server-resursen *för* virtuella datorer, men tar inte bort den virtuella datorn. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Begränsningar

SQL VM-resursprovidern stöder endast:
- Virtuella SQL Server-datorer som distribueras via Azure Resource Manager. Virtuella SQL Server-datorer som distribueras via den klassiska modellen stöds inte. 
- Virtuella SQL Server-datorer som distribueras till det offentliga molnet eller Azure Government-molnet. Distributioner till andra privata moln eller myndighetsmoln stöds inte. 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

**Ska jag registrera min VIRTUELLA SQL Server-dator som etablerats från en SQL Server-avbildning på Azure Marketplace?**

Nej. Microsoft registrerar automatiskt virtuella datorer som etablerats från SQL Server-avbildningarna på Azure Marketplace. Registrering med SQL VM-resursprovidern krävs endast om den virtuella datorn *inte* har etablerats från SQL Server-avbildningarna i Azure Marketplace och SQL Server självinstallerades.

**Är resursprovidern för virtuell SQL-dator tillgänglig för alla kunder?** 

Ja. Kunder bör registrera sina virtuella SQL Server-datorer med SQL VM-resursprovidern om de inte använde en SQL Server-avbildning från Azure Marketplace och i stället självinstallerade SQL Server, eller om de tog med sig sin anpassade virtuella hårddisk. Virtuella datorer som ägs av alla typer av prenumerationer (Direct, Enterprise Agreement och Cloud Solution Provider) kan registrera sig hos SQL VM-resursprovidern.

**Ska jag registrera mig hos SQL VM-resursprovidern om sql server-vm-tillägget redan har SQL Server IaaS-tillägget installerat?**

Om din VIRTUELLA SQL Server-dator är självinstallerad och inte har etablerats från SQL Server-avbildningarna på Azure Marketplace bör du registrera dig hos SQL VM-resursprovidern även om du har installerat SQL Server IaaS-tillägget. Om du registrerar dig hos SQL VM-resursprovidern skapas en ny resurs av typen Microsoft.SqlVirtualMachines. Om du installerar IaaS-tillägget för SQL Server skapas inte den resursen.

**Vilket är standardhanteringsläget när du registrerar dig hos SQL VM-resursprovidern?**

Standardhanteringsläget när du registrerar dig hos SQL VM-resursprovidern är *fullt*. Om SQL Server-hanteringsegenskapen inte anges när du registrerar dig hos SQL VM-resursprovidern ställs läget in som fullständig hanterbarhet och SQL Server-tjänsten startas om. Vi rekommenderar att du registrerar dig med SQL VM-resursprovidern i lättviktsläge först och sedan uppgradera till full under ett underhållsfönster. 

**Vilka är förutsättningarna för att registrera sig hos SQL VM-resursprovidern?**

Det finns inga förutsättningar för att registrera sig med SQL VM-resursprovidern i lättviktsläge eller ingen agentläge. Förutsättningen för att registrera sig hos SQL VM-resursprovidern i fullt läge är att ha SQL Server IaaS-tillägget installerat på den virtuella datorn, eftersom SQL Server-tjänsten annars startas om. 

**Kan jag registrera mig hos SQL VM-resursprovidern om jag inte har SQL Server IaaS-tillägget installerat på den virtuella datorn?**

Ja, du kan registrera dig med SQL VM-resursprovidern i lätthanteringsläge om du inte har SQL Server IaaS-tillägget installerat på den virtuella datorn. I lättläge använder SQL VM-resursprovidern en konsolapp för att verifiera versionen och utgåvan av SQL Server-instansen. 

Standardläge för SQL-hantering när du registrerar dig hos SQL VM-resursprovidern är _Fullständig_. Om SQL Management-egenskapen inte anges när du registrerar dig hos SQL VM-resursprovidern ställs läget in som fullständig hanterbarhet. Vi rekommenderar att du registrerar dig med SQL VM-resursprovidern i lättviktsläge först och sedan uppgradera till full under ett underhållsfönster. 

**Kommer registrering med SQL VM-resursprovidern att installera en agent på min virtuella dator?**

Nej. Om du registrerar dig hos SQL VM-resursprovidern skapas bara en ny metadataresurs. Det kommer inte att installera en agent på den virtuella datorn.

SQL Server IaaS-tillägget behövs endast för att möjliggöra fullständig hanterbarhet. Om du uppgraderar hanterbarhetsläget från lättvikt till full installeras SQL Server IaaS-tillägget och SQL Server startas om.

**Startar SQL Server-servern om SQL Server på min virtuella dator?**

Det beror på vilket läge som anges under registreringen. Om lightweight- eller NoAgent-läge anges startar inte SQL Server-tjänsten om. Om du anger hanteringsläget som fullständigt eller lämnar hanteringsläget tomt installeras dock SQL IaaS-tillägget i fullständigt hanteringsläge, vilket gör att SQL Server-tjänsten startas om. 

**Vad är skillnaden mellan lätta och no-agent hanteringslägen när du registrerar dig med SQL VM resursprovidern?** 

Hanteringsläge för no-agent är endast tillgängligt för SQL Server 2008 och SQL Server 2008 R2 på Windows Server 2008. Det är det enda tillgängliga hanteringsläget för dessa versioner. För alla andra versioner av SQL Server är de två tillgängliga hanterbarhetslägena lätta och fulla. 

Inget agentläge kräver att SQL Server-versions- och versionsegenskaper ställs in av kunden. Lightweight mode frågar den virtuella datorn för att hitta den version och utgåva av SQL Server-instansen.

**Kan jag registrera mig hos SQL VM-resursprovidern utan att ange SQL Server-licenstypen?**

Nej. SQL Server-licenstypen är inte en valfri egenskap när du registrerar dig hos SQL VM-resursprovidern. Du måste ange SQL Server-licenstypen som användningsbaserad betalning eller Azure Hybrid Benefit när du registrerar dig hos SQL VM-resursprovidern i alla hanterbarhetslägen (ingen agent, lätt och fullständig).

**Kan jag uppgradera SQL Server IaaS-tillägget från no-agent-läge till fullt läge?**

Nej. Det går inte att uppgradera hanterbarhetsläget till fullt eller lättfritt för inget agentläge. Detta är en teknisk begränsning av Windows Server 2008. Du måste uppgradera operativsystemet först till Windows Server 2008 R2 eller mer, och sedan kommer du att kunna uppgradera till fullt hanteringsläge. 

**Kan jag uppgradera SQL Server IaaS-tillägget från lättviktsläge till fullt läge?**

Ja. Det går inte att uppgradera hanterbarhetsläget från lätt till fullständig via PowerShell eller Azure-portalen. Det kräver omstart av SQL Server-tjänsten.

**Kan jag nedgradera SQL Server IaaS-tillägget från fullt läge till ingen agent- eller lätthanteringsläge?**

Nej. Det går inte att nedgradera läget för SQL Server IaaS-tilläggshanterbarhet. Hanterbarhetsläget kan inte nedgraderas från helläge till lättläge eller ingen agentläge, och det kan inte nedgraderas från lättviktsläge till no-agent-läge. 

Om du vill ändra hanterbarhetsläget från fullständig hanterbarhet [avregistrerar du](#unregister-vm-from-rp) den virtuella SQL Server-datorn från SQL Server-resursprovidern genom att släppa SQL Server-resursen och registrera om SQL Server-datorn med SQL VM-resursprovidern igen i ett annat hanteringsläge. *resource*

**Kan jag registrera mig hos SQL VM-resursprovidern från Azure-portalen?**

Nej. Att registrera sig hos SQL VM-resursprovidern är inte tillgängligt i Azure-portalen. Att registrera sig hos SQL VM-resursprovidern stöds endast med Azure CLI eller PowerShell. 

**Kan jag registrera en virtuell dator med SQL VM-resursprovidern innan SQL Server installeras?**

Nej. En virtuell dator bör ha minst en SQL Server-instans (Database Engine) för att registrera sig hos SQL VM-resursprovidern. Om det inte finns någon SQL Server-instans på den virtuella datorn kommer den nya Microsoft.SqlVirtualMachine-resursen att vara i ett misslyckat tillstånd.

**Kan jag registrera en virtuell dator med SQL VM-resursprovidern om det finns flera SQL Server-instanser?**

Ja. SQL VM-resursprovidern registrerar bara en SQL Server-instans (Database Engine). SQL VM-resursprovidern registrerar standard-SQL Server-instansen för flera instanser. Om det inte finns någon standardinstans stöds bara registrering i lättviktsläge. Om du vill uppgradera från lättviktsläge till fullständigt hanterbarhetsläge bör antingen standard-SQL Server-instansen finnas eller så bör den virtuella datorn bara ha en namngiven SQL Server-instans.

**Kan jag registrera en SQL Server-redundansklusterinin med SQL VM-resursprovidern?**

Ja. SQL Server redundansklusterinstanser på en Azure VM kan registreras med SQL VM-resursprovidern i lättviktsläge. Sql Server-redundansklusterinstanser kan dock inte uppgraderas till fullständigt hanterbarhetsläge.

**Kan jag registrera min virtuella dator med SQL VM-resursprovidern om en alltid på tillgänglighetsgrupp har konfigurerats?**

Ja. Det finns inga begränsningar för att registrera en SQL Server-instans på en Azure VM-resursprovider med SQL VM-resursprovidern om du deltar i en konfiguration av alltid på tillgänglighet.

**Vad kostar det att registrera sig hos SQL VM-resursprovidern eller med uppgradering till fullständigt hanterbarhetsläge?**
Inga. Det finns ingen avgift i samband med registrering med SQL VM-resursprovidern eller med användning av något av de tre hanterbarhetslägena. Det är helt gratis att hantera den virtuella datorn för SQL Server med resursleverantören. 

**Vad har prestandapåverkan med att använda de olika hanterbarhetslägena?**
Det finns ingen påverkan när du använder *NoAgent* och *lätta* hanterbarhetslägen. Det är minimal påverkan när du använder *det fullständiga* hanterbarhetsläget från två tjänster som är installerade i operativsystemet. Dessa kan övervakas via Aktivitetshanteraren och ses i den inbyggda Windows Services-konsolen. 

De två servicenamnen är:
- `SqlIaaSExtensionQuery`(Visningsnamn `Microsoft SQL Server IaaS Query Service`- )
- `SQLIaaSExtension`(Visningsnamn `Microsoft SQL Server IaaS Agent`- )


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om SQL Server på en Virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prisvägledning för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](virtual-machines-windows-sql-server-iaas-release-notes.md)
