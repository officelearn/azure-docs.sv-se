---
title: Autoskala HPC Pack klusternoder | Microsoft Docs
description: Automatiskt växa eller krympa antalet noder i HPC Pack beräkning i Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: ''
editor: tysonn
ms.assetid: 38762cd1-f917-464c-ae5d-b02b1eb21e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 12/08/2016
ms.author: danlep
ms.openlocfilehash: 4a2350183bc0cb9360e9315cd8a351be20b66584
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2018
ms.locfileid: "31603852"
---
# <a name="automatically-grow-and-shrink-the-hpc-pack-cluster-resources-in-azure-according-to-the-cluster-workload"></a>Automatiskt växa eller krympa HPC Pack klusterresurserna i Azure enligt klustrets arbetsbelastning
Om du distribuerar Azure ”burst” noder i klustret HPC Pack, eller skapar du ett HPC Pack-kluster i virtuella Azure-datorer, kanske ett sätt att automatiskt öka eller minska klustrets resurser, till exempel noder eller kärnor enligt belastningen på klustret. Skalning klusterresurserna på så sätt kan du använda resurserna i Azure mer effektivt och kontrollera deras kostnader.

Den här artikeln lär du dig två sätt som HPC Pack innehåller Autoskala beräkningsresurser:

* Egenskapen HPC Pack klustret **AutoGrowShrink**

* Den **AzureAutoGrowShrink.ps1** HPC PowerShell-skript

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

För närvarande kan endast automatiskt växa och krympa HPC Pack compute-noder som kör ett Windows Server-operativsystem.


## <a name="set-the-autogrowshrink-cluster-property"></a>Ange egenskapen AutoGrowShrink kluster
### <a name="prerequisites"></a>Förutsättningar

* **HPC Pack 2012 R2 uppdatering 2 eller ett senare kluster** -klustrets huvudnod kan distribueras antingen lokalt eller i en Azure VM. Se [ställer in en hybrid-kluster med HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) att komma igång med en lokal huvudnod och Azure ”burst” noder. Finns det [HPC Pack IaaS distributionsskriptet](hpcpack-cluster-powershell-script.md) att snabbt distribuera ett kluster som HPC Pack i virtuella Azure-datorer.

* **För ett kluster med en huvudnod i Azure (Resource Manager-modellen)** – med början i HPC Pack 2016 certifikatautentisering i ett Azure Active Directory-program används för att automatiskt växande och krympande klustrets virtuella datorer distribueras via Azure Resource Manager. Konfigurera ett certifikat på följande sätt:

  1. Efter-Klusterdistribution kan du ansluta av fjärrskrivbord till en huvudnod.

  2. Överför certifikatet (PFX-format med privat nyckel) till varje huvudnod och installera Cert: \LocalMachine\My och Cert: \LocalMachine\Root.

  3. Starta Azure PowerShell som administratör och kör följande kommandon i en huvudnod:

    ```powershell
        cd $env:CCP_HOME\bin

        Connect-AzureRmAccount
    ```
        
    Om ditt konto finns i fler än en Azure Active Directory-klient eller Azure-prenumeration kan köra du följande kommando för att välja rätt klient och prenumeration:
  
    ```powershell
        Connect-AzureRmAccount -TenantId <TenantId> -SubscriptionId <subscriptionId>
    ```     
       
    Kör följande kommando för att visa den markerade klient och prenumeration:
    
    ```powershell
        Get-AzureRMContext
    ```

  4. Kör följande skript

    ```powershell
        .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -CertificateThumbprint "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" -TenantId xxxxxxxx-xxxxx-xxxxx-xxxxx-xxxxxxxxxxxx
    ```

    där

    **DisplayName** -Azure Active programmets visningsnamn. Om programmet inte finns, skapas den i Azure Active Directory.

    **Webbsida** -startsidan för programmet. Du kan konfigurera en dummy URL, som i föregående exempel.

    **IdentifierUri** -identifierare för programmet. Du kan konfigurera en dummy URL, som i föregående exempel.

    **CertificateThumbprint** -tumavtrycket för certifikatet som du har installerat på huvudnod i steg 1.

    **TenantId** -klient-ID för din Azure Active Directory. Du kan hämta klient-ID från Azure Active Directory-portalen **egenskaper** sidan.

    Mer information om **ConfigARMAutoGrowShrinkCert.ps1**kör `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`.


* **För ett kluster med en huvudnod i Azure (klassiska distributionsmodellen)** - om du använder HPC Pack IaaS-distributionsskriptet skapa klustret i den klassiska distributionsmodellen genom att aktivera den **AutoGrowShrink** kluster egenskap med Ange alternativet AutoGrowShrink i konfigurationsfilen för klustret. Mer information finns i dokumentationen som medföljer den [hämtning av](https://www.microsoft.com/download/details.aspx?id=44949).

    Du kan också aktivera den **AutoGrowShrink** kluster egenskapen när du har distribuerat klustret med hjälp av HPC PowerShell-kommandon som beskrivs i följande avsnitt. För att förbereda för det här gör du följande:

  1. Konfigurera ett Azure-hanteringscertifikat i huvudnod och i Azure-prenumerationen. En test-distribution kan du använda det självsignerade certifikatet som är standard Microsoft HPC Azure som HPC Pack installeras på huvudnoden och sedan ladda upp certifikatet till din Azure-prenumeration. Alternativ och anvisningar finns i [TechNet-biblioteket vägledning](https://technet.microsoft.com/library/gg481759.aspx).

  2. Kör **regedit** på huvudnoden, gå till HKLM\SOFTWARE\Micorsoft\HPC\IaasInfo och Lägg till ett strängvärde. Ange namnet på värdet till ”tumavtryck” och värdedata tumavtryck för certifikatet i steg 1.

### <a name="hpc-powershell-commands-to-set-the-autogrowshrink-property"></a>HPC PowerShell-kommandon för att ange egenskapen AutoGrowShrink
Följande är exempel HPC PowerShell-kommandon för att ange **AutoGrowShrink** och för att finjustera sitt beteende med ytterligare parametrar. Se [AutoGrowShrink parametrar](#AutoGrowShrink-parameters) senare i den här artikeln för en fullständig lista över inställningar.

Starta HPC PowerShell för att köra dessa kommandon på klustrets huvudnod som administratör.

**Så här aktiverar du egenskapen AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 1
```

**Så här inaktiverar du egenskapen AutoGrowShrink**

```powershell
Set-HpcClusterProperty –EnableGrowShrink 0
```

**Så här ändrar du ökar intervallet i minuter**

```powershell
Set-HpcClusterProperty –GrowInterval <interval>
```

**Ändra förminskas intervallet i minuter**

```powershell
Set-HpcClusterProperty –ShrinkInterval <interval>
```

**Visa den aktuella konfigurationen av AutoGrowShrink**

```powershell
Get-HpcClusterProperty –AutoGrowShrink
```

**Att noden grupper i AutoGrowShrink**

```powershell
Set-HpcClusterProperty –ExcludeNodeGroups <group1,group2,group3>
```

>[!NOTE] 
> Den här parametern stöds från och med HPC Pack 2016
>

### <a name="autogrowshrink-parameters"></a>AutoGrowShrink parametrar
Följande är AutoGrowShrink parametrar som du kan ändra med hjälp av den **Set HpcClusterProperty** kommando.

* **EnableGrowShrink** -växeln för att aktivera eller inaktivera den **AutoGrowShrink** egenskapen.
* **ParamSweepTasksPerCore** -antal parametrisk rensning uppgifter att utöka en kärna. Standardvärdet är att utöka en kärnor per aktivitet.

  > [!NOTE]
  > HPC Pack QFE KB3134307 ändringar **ParamSweepTasksPerCore** till **TasksPerResourceUnit**. Den är baserad på resurstypen jobb och kan vara nod, socket eller kärnor.
  >
  >
* **GrowThreshold** -tröskelvärdet köade aktiviteter som utlöser automatisk ökning. Standardvärdet är 1, vilket innebär som om det finns 1 eller flera aktiviteter i tillståndet köade Utöka automatiskt noder.
* **GrowInterval** -intervall i minuter som utlöser automatisk ökning. Standardintervallet är 5 minuter.
* **ShrinkInterval** -intervall i minuter som utlöser automatisk krymper. Standardintervallet är 5 minuter. |
* **ShrinkIdleTimes** -antalet kontinuerlig kontroller för att minska som indikerar att noderna är inaktiv. Standardvärdet är 3 gånger. Till exempel om den **ShrinkInterval** är 5 minuter, HPC Pack kontrollerar om noden är inaktiv var femte minut. Om noderna i inaktivt läge efter 3 kontinuerlig kontrollerar (15 minuter), krymper HPC Pack noden.
* **ExtraNodesGrowRatio** -ytterligare procentandelen noder kan växa för Message Passing Interface (MPI) jobb. Standardvärdet är 1, vilket innebär att HPC Pack växer noder 1% för MPI-jobb.
* **GrowByMin** -växeln för att ange om principen för automatisk storleksökning baseras på de minsta resurser som krävs för jobbet. Standardvärdet är FALSKT, vilket innebär att HPC Pack växer noder för jobb baserat på de maximala resurser som krävs för jobb.
* **SoaJobGrowThreshold** -tröskelvärde av inkommande SOA-begäranden som utlöser automatiskt växa processen. Standardvärdet är 50000.

  > [!NOTE]
  > Den här parametern stöds från och med HPC Pack 2012 R2 uppdatering 3.
  >
  >
* **SoaRequestsPerCore** -antal inkommande SOA-begäranden för att utöka en kärna. Standardvärdet är 20000.

  > [!NOTE]
  > Den här parametern stöds från och med HPC Pack 2012 R2 uppdatering 3.
  >
* **ExcludeNodeGroups** – noder i de angivna noden grupperna inte automatiskt växa eller krympa.
  
  > [!NOTE]
  > Den här parametern stöds från och med HPC Pack 2016.
  >

### <a name="mpi-example"></a>MPI-exempel
Som standard HPC Pack växer 1% extra noder för MPI-jobb (**ExtraNodesGrowRatio** har angetts till 1). Det beror på att MPI kan kräva flera noder och jobbet kan bara köras när alla noder är klar. När Azure startar noder behöva ibland en nod mer tid att starta än andra orsakar andra noder att vara inaktiv under väntan på noden för att förbereda. HPC Pack av växande extra noder minskar väntetiden för den här resursen och kostnaderna potentiellt. Om du vill öka procentandelen extra noder för MPI-jobb (till exempel till 10%), köra ett kommando som liknar

    Set-HpcClusterProperty -ExtraNodesGrowRatio 10

### <a name="soa-example"></a>SOA-exempel
Som standard **SoaJobGrowThreshold** är inställd på 50000 och **SoaRequestsPerCore** är inställd på 20000. Om du skickar ett SOA-jobb med 70000 begäranden, det finns en köad aktivitet och inkommande begäranden 70000. I det här fallet HPC Pack växer 1 kärna för aktiviteten i kö och för inkommande begäranden, växer (70000 50000) / 20000 = 1 core i totalt växer 2 kärnor för denna SOA-jobb.

## <a name="run-the-azureautogrowshrinkps1-script"></a>Kör skriptet AzureAutoGrowShrink.ps1
### <a name="prerequisites"></a>Förutsättningar

* **HPC Pack 2012 R2 Update 1 eller ett senare kluster** – **AzureAutoGrowShrink.ps1** skript är installerad på % CCP_HOME % bin-mappen. Klustrets huvudnod kan distribueras antingen lokalt eller i en Azure VM. Se [ställer in en hybrid-kluster med HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md) att komma igång med en lokal huvudnod och Azure ”burst” noder. Finns det [HPC Pack IaaS distributionsskriptet](hpcpack-cluster-powershell-script.md) att snabbt distribuera ett kluster som HPC Pack i virtuella Azure-datorer eller använda en [Azure quickstart mallen](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/).
* **Azure PowerShell 1.4.0** -skriptet för närvarande är beroende av den här specifika versionen av Azure PowerShell.
* **För ett kluster med Azure burst-noder** -kör skriptet på en klientdator där HPC Pack är installerad eller på huvudnoden. Om körs på en klientdator, se till att du anger variabeln $env: CCP_SCHEDULER så att den pekar till huvudnod. Azure ”burst” noder måste läggas till i klustret, men de kan vara i tillståndet inte distribueras.
* **För ett kluster som distribueras på virtuella Azure-datorer (Resource Manager-distributionsmodellen)** -för ett kluster med Azure virtuella datorer som distribueras i Resource Manager-distributionsmodellen skriptet stöder två metoder för Azure-autentisering: Logga in på ditt Azure-konto för att köra den varje gång-skript (genom att köra `Connect-AzureRmAccount`, eller konfigurera ett huvudnamn för tjänsten att autentisera med ett certifikat. HPC Pack tillhandahåller skript **ConfigARMAutoGrowShrinkCert.ps** att skapa ett huvudnamn för tjänsten med certifikat. Skriptet skapar ett program för Azure Active Directory (Azure AD) och ett huvudnamn för tjänsten och tilldelar deltagarrollen till tjänstens huvudnamn. Starta Azure PowerShell som administratör för att köra skriptet och kör följande kommandon:

    ```powershell
    cd $env:CCP_HOME\bin

    Connect-AzureRmAccount

    .\ConfigARMAutoGrowShrinkCert.ps1 -DisplayName “YourHpcPackAppName” -HomePage "https://YourHpcPackAppHomePage" -IdentifierUri "https://YourHpcPackAppUri" -PfxFile "d:\yourcertificate.pfx"
    ```

    Mer information om **ConfigARMAutoGrowShrinkCert.ps1**kör `Get-Help .\ConfigARMAutoGrowShrinkCert.ps1 -Detailed`,

* **För ett kluster som distribueras på virtuella Azure-datorer (klassiska distributionsmodellen)** -kör skriptet på huvudnoden VM, eftersom den är beroende av **Start HpcIaaSNode.ps1** och **stoppa HpcIaaSNode.ps1** skript som installeras det. Dessa skript Dessutom kräver ett Azure-hanteringscertifikat eller inställningsfilen för publicering (se [hantera compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster-node-manage.md)). Kontrollera att alla Beräkningsnoden virtuella datorer som du redan har lagts till i klustret. De kan vara i stoppat tillstånd.



### <a name="syntax"></a>Syntax
```powershell
AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfActiveQueuedTasksPerNodeToGrow <Single> [-NumOfActiveQueuedTasksToGrowThreshold <Int32>]
    [-NumOfInitialNodesToGrow <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>]
    [-ShrinkCheckIdleTimes <Int32>] [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>]
    [<CommonParameters>]

AzureAutoGrowShrink.ps1 [-NodeTemplates <String[]>] [-JobTemplates <String[]>] [-NodeType <String>]
    -NumOfQueuedJobsPerNodeToGrow <Single> [-NumOfQueuedJobsToGrowThreshold <Int32>] [-NumOfInitialNodesToGrow
    <Int32>] [-GrowCheckIntervalMins <Int32>] [-ShrinkCheckIntervalMins <Int32>] [-ShrinkCheckIdleTimes <Int32>]
    [-ExtraNodesGrowRatio <Int32>] [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]

AzureAutoGrowShrink.ps1 -UseLastConfigurations [-ArgFile <String>] [-LogFilePrefix <String>] [<CommonParameters>]
```
### <a name="parameters"></a>Parametrar
* **NodeTemplates** -namnen på noden mallar för att definiera omfattningen för noder att växa eller krympa. Om inget annat anges (standardvärdet är @()), alla noder i den **AzureNodes** nodgrupp är i omfattning när **NodeType** har värdet AzureNodes och alla noder i den **ComputeNodes** nodgrupp är i omfattning när **NodeType** har värdet ComputeNodes.
* **JobTemplates** -namnen på jobbmallarna att definiera omfattningen för noder att växa.
* **NodeType** -typ av noden för att växa eller krympa. Värden som stöds är:

  * **AzureNodes** – för Azure PaaS (burst) noder i ett lokalt eller Azure IaaS-kluster.
  * **ComputeNodes** - för beräkningsnod virtuella datorer i ett Azure IaaS-kluster.

* **NumOfQueuedJobsPerNodeToGrow** -antalet köade jobb som krävs för att utöka en nod.
* **NumOfQueuedJobsToGrowThreshold** -tröskelvärdet för antal köade jobb för att starta processen växa.
* **NumOfActiveQueuedTasksPerNodeToGrow** - antalet av aktiva i kö uppgifter som krävs för att utöka en nod. Om **NumOfQueuedJobsPerNodeToGrow** anges med ett värde som är större än 0, ignoreras den här parametern.
* **NumOfActiveQueuedTasksToGrowThreshold** -tröskelvärdet för antal aktiva köade aktiviteter för att starta processen växa.
* **NumOfInitialNodesToGrow** -inledande minsta antalet noder att växa om alla noder i omfånget är **inte distribuerade** eller **Stoppad (Deallocated)**.
* **GrowCheckIntervalMins** -intervallet i minuter mellan kontrollerar att växa.
* **ShrinkCheckIntervalMins** -intervallet i minuter mellan kontroller för att minska.
* **ShrinkCheckIdleTimes** -antal kontinuerlig krympa kontrollerar (avgränsade med **ShrinkCheckIntervalMins**) att noderna är inaktiv.
* **UseLastConfigurations** -tidigare konfigurationer i argumentet-filen.
* **ArgFile**-namnet på argumentet-fil som används för att spara och uppdatera konfigurationer för att köra skriptet.
* **LogFilePrefix** -prefixet i loggfilen. Du kan ange en sökväg. Som standard skrivs loggen till den aktuella arbetskatalogen.

### <a name="example-1"></a>Exempel 1
I följande exempel konfigureras Azure burst-noder som distribuerats med mallen standard AzureNode att växa eller krympa automatiskt. Om alla noder är först i den **inte distribuerade** tillstånd, minst 3 noder har startats. Om antalet köade jobb överskrider 8, skriptet börjar noder tills deras nummer överskrider kvoten av jobb i kön till **NumOfQueuedJobsPerNodeToGrow**. Om en nod befinns vara inaktiv i 3 på varandra följande ledig tid, stoppas.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates @('Default AzureNode
 Template') -NodeType AzureNodes -NumOfQueuedJobsPerNodeToGrow 5
 -NumOfQueuedJobsToGrowThreshold 8 -NumOfInitialNodesToGrow 3
 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 3
```

### <a name="example-2"></a>Exempel 2
I följande exempel konfigureras Azure compute-nod virtuella datorer som distribueras med mallen standard ComputeNode att växa eller krympa automatiskt.
Jobb som konfigurerats av jobbet standardmallen definiera omfattningen av arbetsbelastningen i klustret. Om alla noder från början är stoppad, startas minst 5 noder. Om antalet aktiva köade aktiviteter överskrider 15, skriptet börjar noder tills deras nummer överskrider kvoten av aktiva köade aktiviteter till **NumOfActiveQueuedTasksPerNodeToGrow**. Om en nod befinns vara inaktiv i 10 på varandra följande ledig tid, stoppas.

```powershell
.\AzureAutoGrowShrink.ps1 -NodeTemplates 'Default ComputeNode Template' -JobTemplates 'Default' -NodeType ComputeNodes -NumOfActiveQueuedTasksPerNodeToGrow 10 -NumOfActiveQueuedTasksToGrowThreshold 15 -NumOfInitialNodesToGrow 5 -GrowCheckIntervalMins 1 -ShrinkCheckIntervalMins 1 -ShrinkCheckIdleTimes 10 -ArgFile 'IaaSVMComputeNodes_Arg.xml' -LogFilePrefix 'IaaSVMComputeNodes_log'
```
