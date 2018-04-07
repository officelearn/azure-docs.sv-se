---
title: Hantera HPC Pack klustret datornoderna | Microsoft Docs
description: Lär dig mer om PowerShell-skript verktyg för att lägga till, ta bort, starta och stoppa HPC Pack 2012 R2-kluster compute-noder i Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 4193f03b-94e9-4704-a7ad-379abde063a9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 453f53be15b24b96f183b4935cc45fc97ad058bd
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="manage-the-number-and-availability-of-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Hantera antalet och tillgängligheten av beräkningsnoder i ett HPC Pack-kluster i Azure
Om du har skapat ett HPC Pack 2012 R2-kluster i virtuella Azure-datorer kan du kanske sätt enkelt lägga till, ta bort, starta (tillhandahålla) eller stoppa (avetablering) vissa compute-nod virtuella datorer i klustret. Om du vill utföra dessa uppgifter, kör du Azure PowerShell-skript som är installerade på huvudnoden VM. Dessa skript hjälpa dig att styra antalet och tillgängligheten för din HPC Pack klusterresurser så du kan styra kostnader.

> [!IMPORTANT] 
> Den här artikeln gäller bara för HPC Pack 2012 R2-kluster i Azure som skapats med hjälp av den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> PowerShell-skript som beskrivs i den här artikeln är dessutom inte tillgängliga i HPC Pack 2016.

## <a name="prerequisites"></a>Förutsättningar
* **HPC Pack 2012 R2-kluster i Azure Virtual Machines**: skapa ett HPC Pack 2012 R2-kluster i den klassiska distributionsmodellen. Exempelvis kan du automatisera distributionen med HPC Pack 2012 R2 VM-avbildning i Azure Marketplace och ett Azure PowerShell-skript. Information och krav finns i [skapa ett HPC-kluster med HPC Pack IaaS-distributionsskriptet](hpcpack-cluster-powershell-script.md).
  
    Efter distributionen kan hitta noden hanteringsskript i % CCP\_hem % bin-mappen i huvudnod. Kör alla skript som administratör.
* **Azure publicera certifikat för filen eller hantering av inställningar**: du behöver göra något av följande på huvudnoden:
  
  * **Import i Azure inställningsfilen för publicering**. Kör följande Azure PowerShell-cmdlets för att göra detta på huvudnoden:
    
    ```PowerShell
    Get-AzurePublishSettingsFile
    
    Import-AzurePublishSettingsFile –PublishSettingsFile <publish settings file>
    ```
  * **Konfigurera Azure-hanteringscertifikatet på huvudnoden**. Om du har den .cer-fil, importera det i certifikatarkivet CurrentUser\My och kör sedan följande Azure PowerShell-cmdlet för Azure-miljön (AzureCloud eller AzureChinaCloud):
    
    ```PowerShell
    Set-AzureSubscription -SubscriptionName <Sub Name> -SubscriptionId <Sub ID> -Certificate (Get-Item Cert:\CurrentUser\My\<Cert Thrumbprint>) -Environment <AzureCloud | AzureChinaCloud>
    ```

## <a name="add-compute-node-vms"></a>Lägg till Beräkningsnoden virtuella datorer
Lägg till compute-noderna med den **Lägg till HpcIaaSNode.ps1** skript.

### <a name="syntax"></a>Syntax
```PowerShell
Add-HPCIaaSNode.ps1 [-ServiceName] <String> [-ImageName] <String>
 [-Quantity] <Int32> [-InstanceSize] <String> [-DomainUserName] <String> [[-DomainUserPassword] <String>]
 [[-NodeNameSeries] <String>] [<CommonParameters>]

```
### <a name="parameters"></a>Parametrar
* **ServiceName**: namnet på den molntjänst som nya compute-nod virtuella datorer läggs till.
* **Avbildning**: Azure VM avbildningens namn, som kan hämtas via Azure-portalen eller Azure PowerShell-cmdleten **Get-AzureVMImage**. Bilden måste uppfylla följande krav:
  
  1. Ett Windows-operativsystem installeras.
  2. HPC Pack måste vara installerad i rollen compute-nod.
  3. Bilden måste vara en privat bild i kategorin användare, inte en offentlig Azure VM-avbildning.
* **Antal**: antal beräkningsnod virtuella datorer som ska läggas till.
* **InstanceSize**: storleken på Beräkningsnoden virtuella datorer.
* **DomainUserName**: användaren domännamn som används för att ansluta de nya virtuella datorerna till domänen.
* **DomainUserPassword**: lösenordet för domänanvändaren.
* **NodeNameSeries** (valfritt): namnge mönster för compute-noder. Formatet måste vara &lt; *rot\_namn*&gt;&lt;*starta\_nummer*&gt;%. Till exempel MyCN % 10% innebär en serie compute-nod-namn från MyCN11. Om den inte anges använder skriptet som den konfigurerade noden naming serier i HPC-kluster.

### <a name="example"></a>Exempel
I följande exempel läggs 20 storlek stora beräkningsnod virtuella datorer i Molntjänsten *hpcservice1*, baserat på den Virtuella datoravbildningen *hpccnimage1*.

```PowerShell
Add-HPCIaaSNode.ps1 –ServiceName hpcservice1 –ImageName hpccniamge1
–Quantity 20 –InstanceSize Large –DomainUserName <username>
-DomainUserPassword <password>
```


## <a name="remove-compute-node-vms"></a>Ta bort beräkningsnod virtuella datorer
Ta bort compute-noderna med den **ta bort HpcIaaSNode.ps1** skript.

### <a name="syntax"></a>Syntax
```PowerShell
Remove-HPCIaaSNode.ps1 -Name <String[]> [-DeleteVHD] [-Force] [-WhatIf] [-Confirm] [<CommonParameters>]

Remove-HPCIaaSNode.ps1 -Node <Object> [-DeleteVHD] [-Force] [-Confirm] [<CommonParameters>]
```

### <a name="parameters"></a>Parametrar
* **Namnet**: namnen på de klusternoder som ska tas bort. Jokertecken stöds. Namnet på parametern är namn. Du kan inte ange båda i **namn** och **nod** parametrar.
* **Noden**: den HpcNode objekt för de noder som ska tas bort, som kan hämtas via HPC PowerShell-cmdleten [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Namnet på parametern är nod. Du kan inte ange båda i **namn** och **nod** parametrar.
* **DeleteVHD** (valfritt): inställningen för att ta bort de associera diskarna för virtuella datorer som har tagits bort.
* **Tvinga** (valfritt): inställningen för att tvinga HPC noder offline innan du tar bort dem.
* **Bekräfta** (valfritt): fråga efter bekräftelse innan kommandot körs.
* **WhatIf**: inställningen som beskriver vad som skulle hända om du körde kommandot, utan att kommandot verkligen körs.

### <a name="example"></a>Exempel
I följande exempel tvingar offline noder med namn som börjar *HPCNode-CN -* och de tar bort noder och deras associerade diskar.

```PowerShell
Remove-HPCIaaSNode.ps1 –Name HPCNodeCN-* –DeleteVHD -Force
```

## <a name="start-compute-node-vms"></a>Starta beräkningsnod virtuella datorer
Starta compute-noder med den **Start HpcIaaSNode.ps1** skript.

### <a name="syntax"></a>Syntax
```PowerShell
Start-HPCIaaSNode.ps1 -Name <String[]> [<CommonParameters>]

Start-HPCIaaSNode.ps1 -Node <Object> [<CommonParameters>]
```
### <a name="parameters"></a>Parametrar
* **Namnet**: namnen på klusternoderna kan startas. Jokertecken stöds. Namnet på parametern är namn. Du kan inte ange båda i **namn** och **nod** parametrar.
* **Noden**-objekt i HpcNode för noder som ska startas, som kan hämtas via HPC PowerShell-cmdleten [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Namnet på parametern är nod. Du kan inte ange båda i **namn** och **nod** parametrar.

### <a name="example"></a>Exempel
Följande exempel startar noder med namn som börjar *HPCNode-CN -*.

```PowerShell
Start-HPCIaaSNode.ps1 –Name HPCNodeCN-*
```

## <a name="stop-compute-node-vms"></a>Stoppa beräkningsnod virtuella datorer
Stoppa compute-noderna med den **stoppa HpcIaaSNode.ps1** skript.

### <a name="syntax"></a>Syntax
```PowerShell
Stop-HPCIaaSNode.ps1 -Name <String[]> [-Force] [<CommonParameters>]

Stop-HPCIaaSNode.ps1 -Node <Object> [-Force] [<CommonParameters>]
```

### <a name="parameters"></a>Parametrar
* **Namnet**-namnen på klusternoderna måste stoppas. Jokertecken stöds. Namnet på parametern är namn. Du kan inte ange båda i **namn** och **nod** parametrar.
* **Noden**: den HpcNode objekt för de noder som ska stoppas, som kan hämtas via HPC PowerShell-cmdleten [Get-HpcNode](https://technet.microsoft.com/library/dn887927.aspx). Namnet på parametern är nod. Du kan inte ange båda i **namn** och **nod** parametrar.
* **Tvinga** (valfritt): inställningen för att tvinga HPC noder offline innan du stoppar dem.

### <a name="example"></a>Exempel
I följande exempel tvingar offline noder med namn som börjar *HPCNode-CN -* och stoppar noderna.

```PowerShell
Stop-HPCIaaSNode.ps1 –Name HPCNodeCN-* -Force
```

## <a name="next-steps"></a>Nästa steg
* För att automatiskt öka eller minska klusternoderna enligt den nuvarande arbetsbelastningen för jobb och aktiviteter på klustret, se [automatiskt växa eller krympa HPC Pack klusterresurserna i Azure enligt klustrets arbetsbelastning](hpcpack-cluster-node-autogrowshrink.md).

