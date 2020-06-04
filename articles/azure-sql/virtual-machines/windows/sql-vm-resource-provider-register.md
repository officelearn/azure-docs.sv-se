---
title: Registrera dig hos resurspoolen för SQL VM-providern
description: Registrera din virtuella Azure SQL Server-dator med resurs leverantören för SQL-VM för att aktivera funktioner för SQL Server virtuella datorer som distribueras utanför Azure Marketplace, samt efterlevnad och förbättrad hanterbarhet.
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
ms.openlocfilehash: 768b71aae66a73cea4ef50a00db5a35f1f6588d4
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/03/2020
ms.locfileid: "84342363"
---
# <a name="register-a-sql-server-vm-in-azure-with-the-sql-vm-resource-provider"></a>Registrera en SQL Server VM i Azure med providern för SQL VM-resurs
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du registrerar SQL Server virtuell dator (VM) i Azure med providern för SQL VM-resurs. När du registrerar med resurs leverantören skapas den **virtuella SQL-datorns** _resurs_ i din prenumeration, som är en separat resurs från den virtuella dator resursen. När du avregistrerar SQL Server VM från resurs leverantören tas den **virtuella SQL-datorn** bort _, men den faktiska virtuella datorn tas_ inte bort. 

Genom att distribuera en SQL Server VM Azure Marketplace-avbildning via Azure Portal registreras SQL Server VM automatiskt med resurs leverantören. Men om du väljer att själv installera SQL Server på en virtuell Azure-dator eller etablera en virtuell Azure-dator från en anpassad virtuell hård disk bör du registrera din SQL Server VM med resurs leverantören för:

- **Funktions förmåner**: genom att registrera SQL Server VM med Resource providern låser du upp [automatiserad uppdatering](automated-patching.md), [Automatisk säkerhets kopiering](automated-backup.md), samt övervaknings-och hanterings funktioner. Den låser också upp flexibiliteten för [licensiering](licensing-model-azure-hybrid-benefit-ahb-change.md) och [utgåvor](change-sql-server-edition.md) . Tidigare var dessa funktioner bara tillgängliga för SQL Server VM avbildningar som distribueras från Azure Marketplace. 

- **Kompatibilitet**: registrering med SQL VM-resurs-providern tillhandahåller en förenklad metod för att uppfylla kravet på att meddela Microsoft att Azure Hybrid-förmån har Aktiver ATS enligt vad som anges i produkt villkoren. Den här processen är en negation som behöver hantera licens registrerings formulär för varje resurs.  

- **Kostnads fri hantering**: om du registrerar med resurs leverantören för SQL-VM i alla tre hanterbarhets lägen är helt kostnads fri. Det finns ingen ytterligare kostnad kopplad till resurs leverantören eller med ändring av hanterings lägen. 

- **Förenklad licens hantering**: registrering med den virtuella Azure-providern för virtuella datorer fören klar SQL Server licens hanteringen, och gör att du snabbt kan identifiera SQL Server virtuella datorer med Azure Hybrid-förmån aktiverat med hjälp av [Azure Portal](manage-sql-vm-portal.md), Azure CLI eller PowerShell: 

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Om du vill använda en SQL VM-adressresurs måste du först [Registrera din prenumeration med resurs leverantören](#register-a-subscription-with-the-resource-provider), vilket ger resurs leverantören möjlighet att skapa resurser i den aktuella prenumerationen.

## <a name="prerequisites"></a>Förutsättningar

För att registrera SQL Server VM med resurs leverantören behöver du: 

- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En Azure-resurs modell [SQL Server VM](create-sql-vm-portal.md) distribueras till det offentliga molnet eller Azure Government molnet. 
- Den senaste versionen av [Azure CLI](/cli/azure/install-azure-cli) eller [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Hanterings lägen

Om [SQL IaaS-tillägget](sql-server-iaas-agent-extension-automate-management.md) inte redan har installerats installerar providern för SQL VM-providern automatiskt SQL Server IaaS-tillägget i ett av tre hanterings lägen som anges under registrerings processen. Om du inte anger hanterings läget kommer SQL IaaS-tillägget att installeras i fullständigt hanterings läge.  

Om SQL IaaS-tillägget redan har installerats manuellt, är det redan i fullständigt hanterings läge och registrering med resurs leverantören i full läge startar inte om SQL Server tjänsten.

De tre hanterings lägena är:

- **Lightweight** mode kräver inte omstart av SQL Server, men stöder bara ändring av licens typ och utgåva av SQL Server. Använd det här alternativet för SQL Server virtuella datorer med flera instanser, eller som deltar i en instans av en redundanskluster (FCI). Det går inte att påverka minne eller CPU när du använder Lightweight-läge och det finns ingen associerad kostnad. Vi rekommenderar att du först registrerar SQL Server VM i Lightweight-läge och sedan uppgraderar till fullständigt läge under en schemalagd underhålls period.  

- **Fullständigt** läge ger alla funktioner, men kräver omstart av SQL Server-och system administratörs behörighet. Det här är det alternativ som installeras som standard när du installerar SQL IaaS-tillägget manuellt. Använd den för att hantera en SQL Server VM med en enda instans. I fullständigt läge installeras två Windows-tjänster som har minimal påverkan på minne och CPU – dessa kan övervakas via aktivitets hanteraren. Det finns ingen kostnad för att använda läget fullständig hanterbarhet. 

- **Noagent** -läge är dedikerat till SQL Server 2008 och SQL Server 2008 R2 installerat på Windows Server 2008. Det påverkar inte minne eller CPU när du använder noagent-läge. Det finns ingen kostnad för att använda noagent Managed mode. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten med hjälp av PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-a-subscription-with-the-resource-provider"></a>Registrera en prenumeration med resurs leverantören

Om du vill registrera din SQL Server VM med resurs leverantören för SQL-VM måste du först registrera din prenumeration med resurs leverantören. Detta ger den virtuella SQL-adressresursen möjlighet att skapa resurser i din prenumeration.  Du kan göra detta med hjälp av Azure Portal, Azure CLI eller PowerShell.

### <a name="azure-portal"></a>Azure-portalen

1. Öppna Azure Portal och gå till **alla tjänster**. 
1. Gå till **prenumerationer** och välj en prenumeration på intresse.  
1. På sidan **prenumerationer** går du till **Resource providers**. 
1. Ange **SQL** i filtret för att ta fram SQL-relaterade resurs leverantörer. 
1. Välj **Registrera**, **Registrera**om eller **avregistrera** för **Microsoft. SqlVirtualMachine** -providern, beroende på vilken åtgärd du önskar. 

   ![Ändra providern](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Kommandorad

Registrera din SQL VM-adressresurs i Azure-prenumerationen med hjälp av Azure CLI eller PowerShell. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-the-resource-provider"></a>Registrera med resursprovidern 

### <a name="lightweight-management-mode"></a>Läge för förenklad hantering

Om [SQL Server IaaS agent Extension](sql-server-iaas-agent-extension-automate-management.md) inte har installerats på den virtuella datorn, är rekommendationen att registreras hos resurs leverantören för SQL-VM i Lightweight-läge. Detta kommer att installera SQL IaaS-tillägget i [lättviktigt läge](#management-modes) och förhindra att SQL Server tjänsten startar om. Du kan sedan uppgradera till fullständigt läge när som helst, men om du gör det startas tjänsten om SQL Server tjänsten så att du kan vänta tills en schemalagd underhålls period är aktiv. 

Ange SQL Server licens typ som antingen betala `PAYG` per användning () för att betala per användning, Azure Hybrid-förmån ( `AHUB` ) för att använda din egen licens eller haveri beredskap ( `DR` ) för att aktivera den [kostnads fria Dr Replica-licensen](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure).

Kluster instanser för växling vid fel och distributioner med flera instanser kan bara registreras med resurs leverantören för SQL-VM i Lightweight-läge. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrera en SQL Server VM i lättviktigt läge med Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera en SQL Server VM i Lightweight-läge med PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Fullständigt hanterings läge


Om SQL IaaS-tillägget redan har installerats på den virtuella datorn manuellt kan du registrera SQL Server VM i fullständigt läge utan att starta om SQL Server tjänsten. **Men om SQL IaaS-tillägget inte har installerats, kommer registrering i fullständigt läge att installera SQL IaaS-tillägget i fullständigt läge och starta om tjänsten SQL Server. Fortsätt med försiktighet.**


Använd följande PowerShell-kommando om du vill registrera SQL Server VM direkt i fullständigt läge (och eventuellt starta om din SQL Server tjänst): 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Hanterings läge för noagent 

SQL Server 2008 och 2008 R2 som är installerade på Windows Server 2008 (_inte R2_) kan registreras med resurs leverantören för SQL-VM i [läget noagent](#management-modes). Det här alternativet säkerställer efterlevnad och tillåter att SQL Server VM övervakas i Azure Portal med begränsad funktionalitet.

Ange antingen `AHUB` , `PAYG` eller `DR` som **sqlLicenseType**, och `SQL2008-WS2008` eller `SQL2008R2-WS2008` som **sqlImageOffer**. 

För att registrera SQL Server 2008 eller 2008 R2 på Windows Server 2008-instansen använder du följande Azure CLI-eller PowerShell-kodfragment: 


# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Registrera din virtuella SQL Server 2008-dator i noagent-läge med Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registrera din virtuella SQL Server 2008 R2-dator i noagent-läge med Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registrera din virtuella SQL Server 2008-dator i noagent-läge med PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registrera din virtuella SQL Server 2008 R2-dator i noagent-läge med PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Uppgradera till fullständigt hanterings läge 

SQL Server virtuella datorer som har tillägget *Lightweight* IaaS installerat kan uppgradera läget till _full_ med Azure Portal, Azure CLI eller PowerShell. SQL Server virtuella datorer i _Noagent_ -läge kan uppgraderas till _full_ efter att operativ systemet har uppgraderats till Windows 2008 R2 och senare. Det går inte att nedgradera – om du vill göra det måste du [avregistrera](#unregister-from-the-resource-provider) SQL Server VM från providern för SQL VM-resursen. Om du gör det tas den **virtuella SQL-datorns** _resurs_bort, men den faktiska virtuella datorn tas inte bort. 

Du kan visa det aktuella läget för SQL Server IaaS-agenten med hjälp av PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Så här uppgraderar du agentens läge till fullständigt: 


### <a name="azure-portal"></a>Azure-portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Gå till resursen för [virtuella SQL-datorer](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Välj din SQL Server VM och välj **Översikt**. 
1. För SQL Server virtuella datorer med noagent-eller Lightweight IaaS-läge väljer du de **enda licens typ-och versions uppdateringar som är tillgängliga med meddelandet SQL IaaS-tillägg** .

   ![Val för att ändra läget från portalen](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Markera kryss rutan **Jag accepterar att starta om SQL Server tjänsten på den virtuella datorn** och välj sedan **Bekräfta** för att uppgradera IaaS-läget till full. 

    ![Kryss ruta för att komma överens om att starta om SQL Server tjänsten på den virtuella datorn](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)

Kör följande kodfragment i Azure CLI:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Kör följande PowerShell-kodfragment:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Verifiera registrerings status
Du kan kontrol lera om din SQL Server VM redan har registrerats med resurs leverantören för SQL-VM med hjälp av Azure Portal, Azure CLI eller PowerShell. 

### <a name="azure-portal"></a>Azure-portalen 

1. Logga in på [Azure-portalen](https://portal.azure.com). 
1. Gå till dina [SQL Server virtuella datorer](manage-sql-vm-portal.md).
1. Välj din SQL Server VM i listan. Om din SQL Server VM inte visas här, har den antagligen inte registrerats med den virtuella SQL-resurs leverantören. 
1. Visa värdet under **status**. Om **statusen** är **klar**har SQL Server VM registrerats med den virtuella SQL-adressresursen. 

   ![Verifiera status med SQL RP-registrering](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Kommandorad

Verifiera aktuell SQL Server VM registrerings status med hjälp av antingen Azure CLI eller PowerShell. `ProvisioningState`visar `Succeeded` om registreringen lyckades. 

# <a name="azure-cli"></a>[Azure CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Ett fel indikerar att SQL Server VM inte har registrerats hos resurs leverantören. 


## <a name="unregister-from-the-resource-provider"></a>Avregistrera från resurs leverantören

Ta bort den virtuella SQL-datorns *resurs* med hjälp av Azure Portal eller Azure CLI om du vill avregistrera din SQL Server VM med den virtuella SQL-providern. Om du tar bort den virtuella SQL-datorns *resurs* tas inte SQL Server VM bort. Använd dock försiktighet och följ stegen noggrant, eftersom det är möjligt att oavsiktligt ta bort den virtuella datorn när du försöker ta bort *resursen*. 

Att avregistrera den virtuella SQL-datorn med den virtuella SQL-adressresursen är nödvändig för att nedgradera hanterings läget från full. 

### <a name="azure-portal"></a>Azure-portalen

Följ dessa steg om du vill avregistrera SQL Server VM med resurs leverantören med hjälp av Azure Portal:

1. Logga in på [Azure Portal](https://portal.azure.com).
1. Navigera till den virtuella SQL-resursen. 
  
   ![Resurs för virtuella SQL-datorer](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Välj **Ta bort**. 

   ![Ta bort Provider för SQL VM-resurs](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Skriv namnet på den virtuella SQL-datorn och **avmarkera kryss rutan bredvid den virtuella datorn**.

   ![Ta bort Provider för SQL VM-resurs](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Om du inte avmarkerar kryss rutan bredvid namnet på den virtuella datorn tas den virtuella datorn *bort* helt. Avmarkera kryss rutan för att avregistrera SQL Server VM från resurs leverantören, men *Ta inte bort den faktiska virtuella datorn*. 

1. Välj **ta bort** för att bekräfta borttagningen av den virtuella SQL-datorns *resurs*, inte SQL Server VM. 

### <a name="command-line"></a>Kommandorad

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill avregistrera SQL Server VM från resurs leverantören med Azure CLI använder du kommandot [AZ SQL VM Delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Detta tar bort den SQL Server VM *resursen* men tar inte bort den virtuella datorn. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill avregistrera SQL Server VM från resurs leverantören med Azure CLI använder du kommandot [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm). Detta tar bort den SQL Server VM *resursen* men tar inte bort den virtuella datorn. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Begränsningar

Resurs leverantören för SQL-VM stöder bara:
- SQL Server virtuella datorer som distribueras via Azure Resource Manager. SQL Server virtuella datorer som distribueras via den klassiska modellen stöds inte. 
- SQL Server virtuella datorer som distribueras till det offentliga molnet eller Azure Government molnet. Distributioner till andra privata eller offentliga moln stöds inte. 


## <a name="frequently-asked-questions"></a>Vanliga frågor och svar 

**Bör jag registrera mina SQL Server VM som tillhandahålls från en SQL Server-avbildning på Azure Marketplace?**

Nej. Microsoft registrerar automatiskt virtuella datorer som tillhandahålls från SQL Server avbildningar på Azure Marketplace. Registrering med SQL VM Resource Provider krävs bara om den virtuella datorn *inte* har tillhandahållits från SQL Server avbildningar på Azure Marketplace och SQL Server var själv installerad.

**Är resursprovidern för virtuell SQL-dator tillgänglig för alla kunder?** 

Ja. Kunderna bör registrera sina SQL Server virtuella datorer med resurs leverantören för SQL-VM om de inte använde en SQL Server avbildning från Azure Marketplace och i stället själv installerade SQL Server, eller om de har gjort sin anpassade virtuella hård disk. Virtuella datorer som ägs av alla typer av prenumerationer (direkt, Enterprise-avtal och moln lösnings leverantör) kan registreras med providern för SQL VM-resurs.

**Bör jag registrera mig för SQL VM Resource Provider om SQL Server VM redan har SQL Server IaaS-tillägget installerat?**

Om din SQL Server VM är självinstallerad och inte tillhandahålls från SQL Server avbildningar på Azure Marketplace, bör du registrera dig för den virtuella SQL-adressresursen även om du har installerat SQL Server IaaS-tillägget. Om du registrerar med en SQL VM-adressresurs skapas en ny resurs av typen Microsoft. SqlVirtualMachines. Att installera SQL Server IaaS-tillägget skapar inte den resursen.

**Vad är standard hanterings läget vid registrering med den virtuella SQL-resurs leverantören?**

Standard hanterings läget när du registrerar med resurs leverantören för SQL-VM är *full*. Om egenskapen SQL Server hantering inte har angetts när du registrerar dig hos providern för SQL VM-resurs, anges läget som fullständig hanterbarhet och SQL Servers tjänsten startas om. Vi rekommenderar att du registrerar med resurs leverantören för SQL-VM: en i Lightweight-läge först och sedan uppgraderar till fullständig under en underhålls period. 

**Vilka är kraven för att registreras med resurs leverantören för den virtuella SQL-datorn?**

Det finns inga krav för att registrera med resurs leverantören för SQL-VM i läget för förenklad eller No-agent. Förutsättningen för att registrera med resurspoolen för SQL VM-IaaS i fullständigt läge är att SQL Server-tillägget installerat på den virtuella datorn, i annat fall då SQL Server tjänsten startas om. 

**Kan jag registrera med resurs leverantören för SQL-VM om jag inte har installerat SQL Server IaaS-tillägget på den virtuella datorn?**

Ja, du kan registrera dig för SQL VM Resource Provider i läget för förenklad hantering om du inte har installerat SQL Server IaaS-tillägget på den virtuella datorn. I Lightweight-läge kommer den virtuella SQL-adressresursen att använda en-konsol för att kontrol lera versionen och versionen av SQL Server-instansen. 

Standard läget för SQL-hantering vid registrering med SQL VM Resource Provider är _full_. Om egenskapen för SQL-hantering inte har angetts vid registrering med SQL VM Resource Provider, anges läget som fullständig hanterbarhet. Vi rekommenderar att du registrerar med resurs leverantören för SQL-VM: en i Lightweight-läge först och sedan uppgraderar till fullständig under en underhålls period. 

**Kommer att registreras med den virtuella SQL-adressresursen installera en agent på den virtuella datorn?**

Nej. Vid registrering med den virtuella SQL-adressresursen skapas endast en ny metadata-resurs. Den installerar inte en agent på den virtuella datorn.

SQL Server IaaS-tillägget krävs bara för att aktivera fullständig hanterbarhet. Om du uppgraderar hanterbarhets läget från Lightweight till full installeras SQL Server IaaS-tillägget och startas om SQL Server.

**Kommer registreringen med den virtuella SQL-adressresursen att starta om SQL Server på den virtuella datorn?**

Det beror på vilket läge som anges under registreringen. Om läget för enkel eller utan agent anges, kommer SQL Server-tjänsten inte att starta om. Men om du anger hanterings läget som full eller låter hanterings läget vara tomt installeras SQL IaaS-tillägget i fullständigt hanterings läge, vilket leder till att SQL Server tjänsten startas om. 

**Vad är skillnaden mellan enkla och inga hanterings lägen vid registrering med den virtuella SQL-adressresursen?** 

Hanterings läget No-agent är bara tillgängligt för SQL Server 2008 och SQL Server 2008 R2 på Windows Server 2008. Det är det enda tillgängliga hanterings läget för de här versionerna. För alla andra versioner av SQL Server är de två tillgängliga lägena för hanterbarhet lätta och fullständiga. 

Inget-agent läge kräver att SQL Server versions-och versions egenskaper anges av kunden. I läget för lätta från den virtuella datorn hittar du versionen och versionen av SQL Server-instansen.

**Kan jag registrera med den virtuella SQL-adressresursen utan att ange licens typen SQL Server?**

Nej. Den SQL Server licens typen är inte en valfri egenskap när du registrerar med providern för SQL VM-resursen. Du måste ange SQL Server licens typ som betala per användning eller Azure Hybrid-förmån när du registrerar med resurs leverantören för SQL-VM i alla hanterbarhets lägen (utan agent, Lightweight och full).

**Kan jag uppgradera SQL Server IaaS-tillägget från inget agent läge till fullständigt läge?**

Nej. Uppgradering av hanterbarhets läget till full eller Lightweight är inte tillgängligt för läget No-agent. Detta är en teknisk begränsning i Windows Server 2008. Du måste först uppgradera operativ systemet till Windows Server 2008 R2 eller senare och sedan kan du uppgradera till fullständigt hanterings läge. 

**Kan jag uppgradera SQL Server IaaS-tillägget från förenklat läge till fullständigt läge?**

Ja. Uppgradering av hanterbarhets läget från Lightweight till full stöds via PowerShell eller Azure Portal. Du måste starta om SQL Server tjänsten.

**Kan jag nedgradera SQL Server IaaS-tillägget från fullständigt läge till No-agent eller Lightweight Management mode?**

Nej. Det finns inte stöd för att nedgradera SQL Server IaaS-tilläggets hanterbarhets läge. Läget för hanterbarhets kan inte nedgraderas från fullständigt läge till läget för förenklad eller No-agent, och det kan inte nedgraderas från läget Lightweight till No-agent. 

Om du vill ändra hanterbarhets läget från fullständig hanterbarhet [avregistrerar](#unregister-from-the-resource-provider) du SQL Server VM från providern för SQL VM-resurs genom att släppa SQL Server *resursen* och omregistrera SQL Server VM med RESURSPOOLEN för den virtuella SQL-datorn i ett annat hanterings läge.

**Kan jag registrera med resurs leverantören för SQL-VM från Azure Portal?**

Nej. Registrering med providern för SQL VM-resursen är inte tillgänglig i Azure Portal. Registrering med SQL VM Resource providern stöds bara med Azure CLI eller PowerShell. 

**Kan jag registrera en virtuell dator med resurs leverantören för SQL-VM innan SQL Server installeras?**

Nej. En virtuell dator måste ha minst en SQL Server (databas motor) för att kunna registreras hos resurs leverantören för SQL-VM. Om det inte finns någon SQL Server instans på den virtuella datorn, kommer den nya Microsoft. SqlVirtualMachine-resursen att vara i ett felaktigt tillstånd.

**Kan jag registrera en virtuell dator med resurs leverantören för SQL-VM om det finns flera SQL Server instanser?**

Ja. Resurs leverantören för den virtuella SQL-datorn registrerar bara en SQL Server instans (databas motor). Resurs leverantören för SQL-VM registrerar standard SQL Server-instansen om flera instanser används. Om det inte finns någon standard instans, stöds bara registrering i Lightweight-läge. Om du vill uppgradera från läget för förenklad till fullständig hantering måste du antingen använda standard SQL Server-instansen eller den virtuella datorn får bara ha en namngiven SQL Server-instans.

**Kan jag registrera en instans av SQL Server-redundanskluster med den virtuella SQL-adressresursen?**

Ja. SQL Server instanser av kluster för växling vid fel på en virtuell Azure-dator kan registreras med resurs leverantören för SQL-VM i lättviktigt läge. SQL Server kan dock inte uppgraderas till läget fullständig hanterbarhet.

**Kan jag registrera min virtuella dator med resurs leverantören för SQL-VM om en Always on-tillgänglighetsgruppen är konfigurerad?**

Ja. Det finns inga begränsningar för att registrera en SQL Server-instans på en virtuell Azure-dator med resurs leverantören för SQL-VM om du deltar i en konfiguration med Always on-tillgänglighetsgrupper.

**Vad kostar det att registrera med den virtuella SQL VM-adressresursen eller genom att uppgradera till fullständigt hanterbarhets läge?**
Inga. Det finns ingen avgift kopplad till registrering med den virtuella SQL VM-adressresursen eller med något av de tre hanterbarhets lägena. Att hantera SQL Server VM med Resource providern är helt kostnads fritt. 

**Vilken prestanda påverkas av att använda olika hanterbarhets lägen?**
Det påverkas inte när du använder *Noagent* -och *Lightweight* hanterbarhets läge. Det finns minimal påverkan när du använder läget *fullständig* hantering från två tjänster som är installerade på operativ systemet. Dessa kan övervakas via aktivitets hanteraren och visas i den inbyggda konsolen för Windows-tjänster. 

De två tjänst namnen är:
- `SqlIaaSExtensionQuery`(Visnings namn- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension`(Visnings namn- `Microsoft SQL Server IaaS Agent` )


## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Vanliga frågor och svar om SQL Server på en virtuell Windows-dator](frequently-asked-questions-faq.md)
* [Pris vägledning för SQL Server på en virtuell Windows-dator](pricing-guidance.md)
* [Viktig information för SQL Server på en virtuell Windows-dator](../../database/doc-changes-updates-release-notes.md)
