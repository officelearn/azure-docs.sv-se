---
title: Korrigera Windows-operativsystemet i service fabric-klustret
description: I den här artikeln beskrivs hur du automatiserar operativsystemets korrigering på ett Service Fabric-kluster med hjälp av Patch Orchestration Application.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: atsenthi
ms.openlocfilehash: 857a4da0b24d600ecc572933af578e2e8faf501a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366327"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Korrigera Windows-operativsystemet i service fabric-klustret

> 
> [!IMPORTANT]
> Från och med den 30 april 2019 stöds inte längre Patch Orchestration Application version 1.2.*. Var noga med att uppgradera till den senaste versionen.

> [!NOTE]
> Att få [automatiska os-avbildningsuppgraderingar på din virtuella datorskalauppsättning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) är det bästa sättet att hålla operativsystemet korrigerat i Azure. Scale Set-uppsättningen med baserad automatisk OS-avbildning kräver silver eller större hållbarhet på en skalningsuppsättning.
>

 POA (Patch Orchestration Application) är ett omslag runt Azure Service Fabric Repair Manager-tjänsten, som möjliggör konfigurationsbaserad os-korrigeringsplanering för kluster som inte är azure-värd. POA krävs inte för kluster som inte är azure-värd, men schemaläggning av korrigeringsinstallation per uppdateringsdomän krävs för att korrigera Service Fabric-klustervärdar utan att drabbas av driftstopp.

POA är ett Service Fabric-program som automatiserar operativsystemets korrigering på ett Service Fabric-kluster utan att drabbas av driftstopp.

POA innehåller följande funktioner:

- **Automatisk installation av uppdatering av operativsystemet**. Uppdateringar av operativsystemet hämtas och installeras automatiskt. Klusternoder startas om efter behov utan att ådra sig driftstopp i klustret.

- **Klustermedveten korrigering och hälsointegrering**. Medan POA tillämpar uppdateringar övervakar den hälsotillståndet för klusternoderna. Klusternoder uppdateras en nod i taget eller en uppdateringsdomän i taget. Om klustrets hälsotillstånd går ner på grund av korrigeringsprocessen stoppas korrigering för att förhindra att problemet förvärras.

## <a name="internal-details-of-poa"></a>Interna uppgifter om POA

POA består av följande delkomponenter:

- **Koordinatorservice:** Den här tillståndskänsliga tjänsten ansvarar för:
    - Samordna Windows Update-jobbet i hela klustret.
    - Lagra resultatet av slutförda Windows Update-åtgärder.

- **Nod Agent Service**: Den här tillståndslösa tjänsten körs på alla Service Fabric-klusternoder. Tjänsten ansvarar för:
    - Bootstrapping nodagent NTService.
    - Övervakar NODAgent NTService.

- **Node Agent NTService**: Den här Windows NT-tjänsten körs på en högre nivå(SYSTEM). Node Agent-tjänsten och koordinatortjänsten körs däremot på en lägre nivå (NETWORK SERVICE). Tjänsten ansvarar för att utföra följande Windows Update-jobb på alla klusternoder:
    - Inaktivera automatiska Windows-uppdateringar på noden.
    - Hämta och installera Windows-uppdateringar enligt den princip som användaren har angett.
    - Om du startar om installationen efter Windows uppdateras installationen.
    - Ladda upp resultaten av Windows-uppdateringar till koordinatortjänsten.
    - Rapportera hälsorapporter om en åtgärd har misslyckats efter att den har tömt alla försök.

> [!NOTE]
> POA använder servicetjänsten Service Fabric Repair Manager för att inaktivera eller aktivera noden och utföra hälsokontroller. Reparationsuppgiften som skapas av POA spårar windows update-förloppet för varje nod.

## <a name="prerequisites"></a>Krav

> [!NOTE]
> Den minsta .NET Framework-versionen krävs är 4.6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera tjänsten Repair Manager (om den inte redan körs)

POA kräver att reparationshanteraren-tjänsten aktiveras i klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure-kluster på silverhållbarhetsnivån har tjänsten Repair Manager aktiverad som standard. Azure-kluster på guldhållbarhetsnivån kanske eller kanske inte har tjänsten Repair Manager aktiverad, beroende på när dessa kluster skapades. Azure-kluster på bronshållbarhetsnivån har som standard inte tjänsten Repair Manager aktiverad. Om tjänsten redan är aktiverad kan du se den körs i avsnittet systemtjänster i Service Fabric Explorer.

##### <a name="the-azure-portal"></a>Azure Portal
Du kan aktivera Repair Manager från Azure-portalen när du konfigurerar klustret. När du konfigurerar om klustret väljer du alternativet **Inkludera reparationshanterare** under **Tilläggsfunktioner**.

![Avbildning av aktivera reparationshanteraren från Azure-portalen](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="the-azure-resource-manager-deployment-model"></a>Distributionsmodellen för Azure Resource Manager
Alternativt kan du använda [distributionsmodellen för Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) för att aktivera tjänsten Repair Manager på nya och befintliga Service Fabric-kluster. Hämta mallen för det kluster som du vill distribuera. Du kan antingen använda exempelmallarna eller skapa en anpassad Azure Resource Manager-distributionsmodellmall. 

Så här aktiverar du tjänsten Repair Manager med hjälp av [distributionsmodellmallen för Azure Resource Manager:](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm)

1. Kontrollera att `apiVersion` den är inställd på *2017-07-01-förhandsversionen* för *Microsoft.ServiceFabric/clusters-resursen.* Om det är annorlunda måste `apiVersion` du uppdatera till *2017-07-01-preview* eller senare:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

1. Aktivera reparationshanterarens tjänst `addonFeatures` genom att `fabricSettings` lägga till följande avsnitt efter avsnittet:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. När du har uppdaterat klustermallen med dessa ändringar använder du dem och låter uppdateringen slutföras. Du kan nu se reparationshanteraren-tjänsten som körs i klustret. Det kallas *fabric:/System/RepairManagerService* i avsnittet systemtjänster i Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Fristående lokala kluster

Om du vill aktivera tjänsten Repair Manager i ett nytt eller befintligt Service Fabric-kluster kan du använda [konfigurationsinställningarna för fristående Windows-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest).

Så här aktiverar du tjänsten Repair Manager:

1. Kontrollera att `apiVersion` klusterkonfigurationerna är inställda på *04-2017* eller senare i [Allmänna klusterkonfigurationer,](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) som visas här:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

1. Aktivera reparationshanterarens tjänst `addonFeatures` genom att `fabricSettings` lägga till följande avsnitt efter avsnittet, som du ser här:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

1. Uppdatera klustermanifestet med dessa ändringar med hjälp av det uppdaterade klustermanifestet [skapa ett nytt kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) eller uppgradera [klusterkonfigurationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). 

   När klustret körs med ett uppdaterat klustermanifest kan du se reparationshanteraren-tjänsten som körs i klustret. Det kallas *fabric:/System/RepairManagerService*, och det finns i avsnittet systemtjänster i Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurera Windows-uppdateringar för alla noder

Automatiska Windows-uppdateringar kan leda till tillgänglighetsförlust, eftersom flera klusternoder kan startas om samtidigt. POA försöker som standard inaktivera automatiska Windows-uppdateringar på varje klusternod. Om inställningarna hanteras av en administratör eller en grupprincip rekommenderar vi dock att du uttryckligen anger principen För Windows Update på "Meddela före hämtning".

## <a name="download-the-application-package"></a>Ladda ner programpaketet

Om du vill hämta programpaketet går du till [utgivningssidan](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) för Patch Orchestration Application på GitHub.

## <a name="configure-poa-behavior"></a>Konfigurera POA-beteende

Du kan konfigurera POA-beteende för att uppfylla dina behov. Åsidosätt standardvärdena genom att skicka in programparametern när du skapar eller uppdaterar programmet. Du kan ange programparametrar genom `ApplicationParameter` att ange till `Start-ServiceFabricApplicationUpgrade` eller `New-ServiceFabricApplication` cmdlets.

| Parameter        | Typ                          | Information |
|:-|-|-|
|MaxResultsToCache    |Lång                              | Det maximala antalet Windows Update-resultat som ska cachelagras. <br><br>Standardvärdet är 3000, förutsatt att: <br> &nbsp;&nbsp;- Antalet noder är 20. <br> &nbsp;&nbsp;- Antalet uppdateringar av en nod per månad är 5. <br> &nbsp;&nbsp;- Antalet resultat per operation kan vara 10. <br> &nbsp;&nbsp;- Resultaten för de senaste tre månaderna bör lagras. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UppgraderaDomainWise }                          |TaskApprovalPolicy anger den princip som ska användas av koordinatorn för att installera Windows-uppdateringar över klusternoderna Service Fabric.<br><br>De tillåtna värdena är: <br>*NodeWise*: Windows uppdateringar installeras en nod i taget. <br> *UpgradeDomainWise*: Windows-uppdateringar installeras en uppdateringsdomän i taget. (Högst kan alla noder som tillhör en uppdateringsdomän gå till en Windows-uppdatering.)<br><br> Information om vilken princip som passar bäst för klustret finns i avsnittet [Vanliga frågor och svar.](#frequently-asked-questions)
|LoggarDiskQuotaInMB   |Lång  <br> (Standard: *1024*)               | Den maximala storleken på patchorkestreringsfördelning loggar in MB, som kan sparas lokalt på noder.
| WUQuery (på andra sätt)               | sträng<br>(Standard: *IsInstalled=0*)                | Fråga för att hämta Windows-uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstalleraWindowsOSOnlyUppdater | *Boolean* <br> (standard: falskt)                 | Använd den här flaggan för att styra vilka uppdateringar som ska hämtas och installeras. Följande värden tillåts <br>true - Installerar endast Windows operativsystem uppdateringar.<br>false - Installerar alla tillgängliga uppdateringar på datorn.          |
| WUOperationTimeOutInMinutes | Int <br>(Standard: *90*)                   | Anger tidsgränsen för alla Windows Update-åtgärder (sök eller hämta eller installera). Om åtgärden inte slutförs inom den angivna tidsgränsen avbryts den.       |
| WURescheduleCount     | Int <br> (Standard: *5*)                  | Det maximala antalet gånger tjänsten schemalägger om Windows-uppdateringen om en åtgärd misslyckas beständigt.          |
| WURescheduleTimeInMinutes | Int <br>(Standard: *30*) | Det intervall med vilket tjänsten schemalägger om Windows-uppdateringarna om felet kvarstår. |
| WUFrequency (på nytt)           | Kommaavgränsad sträng (Standard: *Varje vecka, onsdag, 7:00:00*)     | Frekvensen för installation av Windows-uppdateringar. Formatet och möjliga värden är: <br>&nbsp;&nbsp;- Månadsvis: DD, HH:MM:SS (till exempel *månadsvis, 5,12:22:32*)<br>Tillåtna värden för fält DD (dag) är tal från 1 till 28 och "sista". <br> &nbsp;&nbsp;- Varje vecka, DAG, HH:MM:SS (till exempel *Varje vecka, tisdag, 12:22:32*)  <br> &nbsp;&nbsp;- Dagligen, HH:MM:SS (till exempel *Dagligen, 12:22:32*)  <br> &nbsp;&nbsp;-  *Ingen* anger att Windows-uppdateringar inte bör göras.  <br><br> Tiderna är i UTC.|
| AccepteraWindowsUpdateEula | Boolean <br>(Standard: *sant)* | Genom att ange den här flaggan godkänner programmet licensavtalet för slutanvändare för Windows Update på uppdrag av maskinens ägare.              |

> [!TIP]
> Om du vill att Windows-uppdateringar ska ske omedelbart anger du `WUFrequency` i förhållande till programdistributionstiden. Anta till exempel att du har ett testkluster med fem noder och planerar att distribuera appen runt 17:00 UTC. Om du antar att programuppgraderingen eller distributionen tar högst 30 minuter ställer du in WUFrequency som *Daily, 17:30:00*.

## <a name="deploy-poa"></a>Distribuera POA

1. Slutför alla nödvändiga steg för att förbereda klustret.
1. Distribuera POA som alla andra Service Fabric-appar. Information om hur du distribuerar den med PowerShell finns i [Distribuera och ta bort program med PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
1. Om du vill konfigurera programmet vid `ApplicationParameter` tidpunkten `New-ServiceFabricApplication` för distributionen skickar du den till cmdleten. För din bekvämlighet har vi tillhandahållit skriptet Deploy.ps1 tillsammans med programmet. Så här använder du skriptet:

    - Anslut till ett Service `Connect-ServiceFabricCluster`Fabric-kluster med hjälp av .
    - Kör PowerShell-skriptet Deploy.ps1 `ApplicationParameter` med rätt värde.

> [!NOTE]
> Behåll skriptet och programmappen *PatchOrchestrationApplication* i samma katalog.

## <a name="upgrade-poa"></a>Uppgradera POA

Om du vill uppgradera DIN POA-version med PowerShell följer du instruktionerna i [Service Fabric-programuppgraderingen med PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-poa"></a>Ta bort POA

Om du vill ta bort programmet följer du instruktionerna i [Distribuera och tar bort program med PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

För din bekvämlighet har vi tillhandahållit Undeploy.ps1-skriptet tillsammans med programmet. Så här använder du skriptet:

  - Anslut till ett Service ```Connect-ServiceFabricCluster```Fabric-kluster med hjälp av .
  - Kör PowerShell-skriptet Undeploy.ps1.

> [!NOTE]
> Behåll skriptet och programmappen *PatchOrchestrationApplication* i samma katalog.

## <a name="view-the-windows-update-results"></a>Visa resultaten för Windows Update

POA exponerar REST API:er för att visa de historiska resultaten för användarna. Här är ett exempel på resultatet JSON:

```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2019-05-13T08:44:56.4836889Z",
        "OperationStartTime": "2019-05-13T08:44:33.5285601Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0,
            "HResult": 0
          }
        ],
        "OperationType": 1,
        "WindowsUpdateQuery": "IsInstalled=0",
        "WindowsUpdateFrequency": "Daily,10:00:00",
        "RebootRequired": false
      }
    ]
  },
  ...
]
```

JSON-fälten beskrivs i följande tabell:

Field | Värden | Information
-- | -- | --
OperationResultat | 0 - Lyckades<br> 1 - Lyckades med fel<br> 2 - Misslyckades<br> 3 - Avbruten<br> 4 - Avbruten med timeout | Anger resultatet av den övergripande åtgärden, som vanligtvis innebär installation av en eller flera uppdateringar.
Resultatkod | Samma som OperationResult | I det här fältet anges resultatet av installationsåtgärden för en enskild uppdatering.
OperationType (olikartade) | 1 - Installation<br> 0 - Sök och ladda ner| Som standard är installation den enda OperationType som visas i resultatet.
WindowsUpdateQuery | Standard är "IsInstalled=0" | Den Windows Update-fråga som användes för att söka efter uppdateringar. Mer information finns i [WuQuery](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx).
RebootRequired | sant - omstart krävdes<br> false - omstart krävdes inte | Anger om en omstart krävdes för att slutföra installationen av uppdateringar.
OperationStartTime | DateTime | Anger vid vilken tidpunkt åtgärden (Hämtning/installation) startade.
OperationTime (DriftTime) | DateTime | Anger vid vilken tidpunkt åtgärden (Hämtning/installation) slutfördes.
Hresult | 0 - Framgångsrik<br> annat - fel| Anger orsaken till fel på Windows-uppdateringen med updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Om ingen uppdatering är schemalagd ännu är resultatet JSON tomt.

Logga in i klustret för att fråga om Windows Update-resultat. Ta reda på replik-IP-adressen för den primära adressen för koordinatortjänsten och&lt;öppna&gt;följande URL från webbläsaren: http://&lt;REPLICA-IP:&gt;ApplicationPort /PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

REST-slutpunkten för koordinatortjänsten har en dynamisk port. Information om den exakta webbadressen finns i Service Fabric Explorer. Resultaten är till exempel *http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults*tillgängliga på .

![Bild av REST-ändpunkten](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

Om den omvända proxyn är aktiverad i klustret kan du komma åt URL:en även utanför klustret.

Slutpunkten som du behöver träffa är *http://&lt;SERVERURL:&gt;&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults*.

Om du vill aktivera den omvända proxyn i klustret följer du instruktionerna i [Omvänd proxy i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> När den omvända proxyn har konfigurerats kan alla mikrotjänster i klustret som exponerar en HTTP-slutpunkt adresseras från utanför klustret.

## <a name="diagnostics-and-health-events"></a>Diagnostik och hälsohändelser

I det här avsnittet beskrivs hur du felsöker eller diagnostiserar problem med korrigeringsuppdateringar via POA på Service Fabric-kluster.

> [!NOTE]
> För att få många av följande utringande, självdiagnostiska förbättringar, bör du ha POA version 1.4.0 eller senare installerat.

Node Agent NTService skapar [reparationsuppgifter](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) för att installera uppdateringar på noderna. Varje uppgift förbereds sedan av koordinatorn enligt uppgiftsgodkännandeprincipen. Slutligen godkänns de förberedda aktiviteterna av Reparationshanteraren, som inte godkänner någon aktivitet om klustret är i feltillstånd. 

Låt oss gå steg för steg för steg för att förstå hur uppdateringarna fortsätter på en nod:

1. NodeAgentNTService, som körs på varje nod, söker efter tillgängliga Windows-uppdateringar vid den schemalagda tiden. Om det finns tillgängliga uppdateringar hämtas dem på noden.

1. När uppdateringarna har hämtats skapar nodagent NTService en motsvarande reparationsuppgift för noden med namnet *POS___\<unique_id>*. Du kan visa dessa reparationsuppgifter med hjälp av cmdleten [Get-ServiceFabricRepAirTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) eller använda SFX i avsnittet information om nod. När reparationsuppgiften har skapats flyttas den snabbt till [ *anspråkslöst* tillstånd](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).

1. Koordinatornstjänsten söker regelbundet efter reparationsuppgifter i *anspråkslöst* tillstånd och uppdaterar dem sedan till *Förbereda* tillstånd baserat på TaskApprovalPolicy. Om TaskApprovalPolicy är konfigurerad för att vara NodeWise, förbereds en reparationsuppgift som motsvarar en nod endast om ingen annan reparationsuppgift för närvarande är i *Förbereda*, *Godkänd*, *Kör*eller *Återställa* tillstånd. 

   På samma sätt, när det gäller UpgradeWise TaskApprovalPolicy, det finns uppgifter i föregående stater endast för noder som tillhör samma uppdateringdomän. När en reparationsaktivitet har flyttats till *Förberedelse-tillstånd* [inaktiveras](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) motsvarande service fabric-nod med avsikten inställd *på Starta om*.

   POA-versioner 1.4.0 och senare publicera händelser med egenskapen ClusterPatchingStatus på CoordinatorService för att visa de noder som korrigeras. Uppdateringarna installeras på _poanode_0, vilket visas i följande bild:

    [![Bild av status för klusterkorrigering](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

1. När noden har inaktiverats flyttas reparationsaktiviteten till *Körtillstånd.* 
   
   > [!NOTE]
   > En nod som har fastnat i *inaktiverat* tillstånd kan blockera en ny reparationsaktivitet, vilket stoppar korrigeringsåtgärden i klustret.

1. När reparationsaktiviteten är i *körläge* börjar korrigeringsinstallationen på den noden. När korrigeringsfilen har installerats kan noden startas om eller inte, beroende på korrigeringsfilen. Därefter flyttas reparationsaktiviteten till *Återställningsläge,* vilket återerbara noden. Reparationsuppgiften markeras sedan som slutförd.

   I POA-versionerna 1.4.0 och senare kan du hitta status för uppdateringen genom att visa\<hälsohändelserna på NodeService med egenskapen WUOperationStatus- NodeName>. De markerade avsnitten i följande bilder visar status för Windows-uppdateringar på noder *poanode_0* och *poanode_2:*

   [![Bild av windows update-åtgärdsstatus](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Bild av windows update-åtgärdsstatus](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   Du kan också få information med hjälp av PowerShell. För att göra det ansluter du till klustret och hämtar reparationsuppgiftens tillstånd med [get-serviceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). 
   
   I följande exempel är aktiviteten "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" i *downloadcomplete-tillstånd.* Det innebär att uppdateringar har hämtats på *noden poanode_2* och installationen görs när aktiviteten flyttas till *Körtillstånd.*

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Om det återstår fler problem loggar du in på den virtuella datorn (VM) eller virtuella datorer och lär dig mer om dem med hjälp av Windows-händelseloggar. Den tidigare nämnda reparationsaktiviteten kan bara finnas i följande executor-undertillstånd:

      ExecutorSubState | Beskrivning
    -- | -- 
      Ingen=1 |  Innebär att det inte fanns en pågående åtgärd på noden. Tillståndet kan vara i förändring.
      DownloadCompleted=2 | Innebär att hämtningen slutfördes med lyckade, partiella fel eller fel.
      Installation Godkänd=3 | Innebär att hämtningen slutfördes tidigare och reparationshanteraren har godkänt installationen.
      InstallationInProgress=4 | Motsvarar tillståndet för körningen av reparationsuppgiften.
      InstallationKommer uttömd=5 | Innebär att installationen slutfördes med framgång, partiell framgång eller misslyckande.
      RestartRequested=6 | Innebär att korrigeringsinstallationen slutfördes och att det finns en väntande omstartsåtgärd på noden.
      RestartNotNeeded=7 |  Innebär att omstarten inte behövdes efter att korrigeringsinstallationen slutfördes.
      RestartCompleted=8 | Innebär att omstarten slutfördes.
      OperationCompleted=9 | Åtgärden Windows Update slutfördes.
      OperationAborted=10 | Innebär att Windows Update-åtgärden avbröts.

1. I POA-versionerna 1.4.0 och senare, när ett noduppdateringsförsök är avslutat, publiceras en händelse med egenskapen "WUOperationStatus-[NodeName]" på NodeAgentService för att meddela dig när nästa försök att hämta och installera Windows-uppdateringarna börjar. Detta visas i följande bild:

     [![Bild av windows update-åtgärdsstatus](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostics-logs"></a>Diagnostikloggar

Programloggar för patchorkestrering samlas in som en del av service fabric-körningsloggarna.

Du kan samla in loggar med hjälp av det diagnosverktyg eller den pipeline du väljer. POA använder följande fasta provider-ID:er för att logga händelser via [händelsekälla:](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälsorapporter

POA publicerar också hälsorapporter mot nodagenttjänsten eller koordinatortjänsten i följande scenarier:

* NOD Agent NTService är nere

   Om NOD Agent NTService är nere på en nod genereras en hälsorapport på varningsnivå mot nodagenttjänsten.

* Tjänsten Repair Manager är inte aktiverad

   Om tjänsten Repair Manager inte hittas i klustret genereras en hälsorapport på varningsnivå för koordinatorn.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

**F: Varför ser jag mitt kluster i feltillstånd när POA körs?**

S: Under installationsprocessen inaktiverar eller startar POA om noder, vilket tillfälligt kan resultera i ett felaktigt kluster.

Beroende på programmets princip kan antingen en nod gå ner under en korrigeringsåtgärd *eller* så kan en hel uppdateringsdomän gå ner på en gång.

I slutet av installationen av Windows-uppdateringar kan noderna återinbytas efter omstarten.

I följande exempel gick klustret tillfälligt till ett feltillstånd eftersom två noder var nere och maxpercentageUnhealthyNodes-principen överträddes. Felet är tillfälligt tills korrigeringsåtgärden kan påbörjas.

![Bild av felfritt kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Om problemet kvarstår läser du avsnittet Felsökning.

**F: Vad kan jag göra om POA är i varningstillstånd?**

S: Kontrollera om en hälsorapport som har publicerats mot programmet anger grundorsaken. Vanligtvis innehåller varningen information om problemet. Om problemet är tillfälligt förväntas programmet återställas automatiskt.

**F: Vad kan jag göra om mitt kluster är felfritt och jag behöver göra en brådskande uppdatering av operativsystemet?**

S: POA installerar inte uppdateringar medan klustret är felfritt. Försök att få klustret att vara felfritt för att häva blockeringen av POA-arbetsflödet.

**F: Ska jag ställa in TaskApprovalPolicy som "NodeWise" eller "UpgradeDomainWise" för mitt kluster?**

S: Inställningen "UpgradeDomainWise" snabbar upp den övergripande klusterreparationen genom att parallellt korrigera alla noder som tillhör en uppdateringsdomän. Under processen är noder som tillhör en hel uppdateringsdomän inte tillgängliga (i [ *inaktiverat* tillstånd](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled)).

Däremot "NodeWise" inställning patchar bara en nod i taget, vilket skulle innebära att övergripande kluster patchning kan ta längre tid. Det är dock bara en nod som högst skulle vara otillgänglig (i *inaktiverat* tillstånd) under korrigeringsprocessen.

Om ditt kluster kan tolerera att köras på ett N-1 antal uppdateringsdomäner under korrigeringscykeln (där N är det totala antalet uppdateringsdomäner i klustret) kan du ställa in principen som "UpgradeDomainWise". Annars ställer du in den på "NodeWise".

**F: Hur lång tid tar det att korrigera en nod?**

S: Korrigering av en nod kan ta från minuter (till exempel [Windows Defender-definitionsuppdateringar](https://www.microsoft.com/en-us/wdsi/definitions)) till timmar (till exempel [kumulativa Uppdateringar](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)av Windows). Den tid som krävs för att korrigera en nod beror oftast på: 
 - Storleken på uppdateringarna.
 - Antalet uppdateringar som måste tillämpas i ett korrigeringsfönster.
 - Den tid det tar att installera uppdateringarna, starta om noden (om det behövs) och slutföra installationsstegen efter omstarten.
 - Prestanda för den virtuella datorn eller datorn och nätverksförhållanden.

**F: Hur lång tid tar det att korrigeringsfilen för ett helt kluster?**

S: Den tid som krävs för att korrigera ett helt kluster beror på:

- Den tid som behövs för att korrigera en nod.

- Samordnartjänstens policy. Standardpolicyn, "NodeWise", resulterar i att bara lappa en nod i taget, en metod som är långsammare än att använda "UpgradeDomainWise". 

   Till exempel: Om en nod tar ~ 1 timme att lappa, korrigering av en 20-nod (samma typ av noder) kluster med 5 uppdateringsdomäner, var och en innehåller 4 noder, kräver:
    - För "NodeWise": ~20 timmar.
    - För "UpgradeDomainWise": ~5 timmar.

- Klusterbelastningen. Varje korrigeringsåtgärd kräver att du flyttar kundarbetsbelastningen till andra tillgängliga noder i klustret. En nod som lappas skulle vara i [ *inaktivera tillstånd* ](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) under denna tid. Om klustret körs nära toppbelastningen tar det längre tid att inaktivera. Därför kan den övergripande korrigeringsprocessen verka långsam under sådana stressade förhållanden.

- Klusterhälsofel under korrigering. Eventuell [försämring](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [av klustrets hälsotillstånd](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) skulle avbryta korrigeringsprocessen. Det här problemet skulle lägga till den totala tid som krävs för att korrigera hela klustret.

**F: Varför ser jag några uppdateringar i Windows Update-resultaten som erhålls via REST API, men inte under Windows Update-historiken på datorn?**

S: Vissa produktuppdateringar visas bara i deras egen uppdaterings- eller korrigeringshistorik. Windows Defender-uppdateringar kanske till exempel inte visas i Windows Update-historiken på Windows Server 2016.

**F: Kan POA användas för att korrigera mitt utvecklingskluster (ennodskluster)?**

S: Nej, POA kan inte användas för att korrigera ett ennodskluster. Den här begränsningen är avsiktligt eftersom [Service Fabric-systemtjänster](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) eller andra kundappar skulle medföra driftstopp. Därför skulle korrigering reparation jobb aldrig få godkänts av Repair Manager.

**F: Hur korrigerar jag klusternoder på Linux?**

S: Mer information om hur du dirigerar uppdateringar på Linux finns i [Azures skala för virtuella datorer som automatiska os-avbildningsuppgraderingar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade).

**F: Varför tar uppdateringscykeln så lång tid?**

S: Fråga efter resultatet JSON, ange uppdateringscykeln för alla noder och sedan kan du försöka ta reda på den tid det tar för uppdateringsinstallationen på varje nod med hjälp av OperationStartTime och OperationTime (OperationCompletionTime). 

Om det finns ett stort tidsfönster där ingen uppdatering äger rum kan klustret vara i feltillstånd och därför kan Repair Manager inte godkänna några POA-reparationsuppgifter. Om uppdateringsinstallationen tar lång tid på en nod kanske noden inte har uppdaterats på ett tag. Många uppdateringar kan vänta på installation, vilket kan leda till förseningar. 

Det kan också vara möjligt att nodkorrigeringen är blockerad eftersom den har fastnat i *inaktiverat* tillstånd. Detta händer vanligtvis eftersom inaktivering av noden kan leda till kvorum eller dataförlustsituationer.

**F: Varför måste noden inaktiveras när POA korrigerar den?**

S: POA inaktiverar noden med en *omstartsmetod,* som stoppar eller omfördelar alla Service Fabric-tjänster som körs på noden. POA gör detta för att säkerställa att program inte slutar med en blandning av nya och gamla DLL-filer, så vi rekommenderar att du inte korrigerar en nod utan att inaktivera den.

**F: Vilket är det maximala antalet noder som kan uppdateras med poa?**

S: POA använder Service Fabric Repair Manager för att skapa reparationsuppgifter för noder för uppdateringar. Men inte mer än 250 reparationsuppgifter kan vara närvarande samtidigt. Poa skapar för närvarande reparationsuppgifter för varje nod samtidigt, så POA kan uppdatera högst 250 noder i ett kluster. 

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- POA accepterar licensavtalet för slutanvändare för Windows Update för användarens räkning. Du kan också inaktivera inställningen i konfigurationen av programmet.

- POA samlar in telemetri för att spåra användning och prestanda. Programmets telemetri följer inställningen för service fabric-körningens telemetriinställning (som är aktiverad som standard).

## <a name="troubleshooting"></a>Felsökning

Det här avsnittet innehåller möjliga felsökningslösningar för problem med korrigeringsnoder.

### <a name="a-node-is-not-coming-back-to-up-state"></a>En nod kommer inte tillbaka till upptillstånd

* Noden kan ha fastnat i *inaktiverande* tillstånd eftersom:

  - En säkerhetskontroll pågår. För att åtgärda denna situation, se till att tillräckligt många noder är tillgängliga i ett felfritt tillstånd.

* Noden kan ha fastnat i *inaktiverat* tillstånd eftersom:

  - Det inaktiverades manuellt.
  - Den inaktiverades på grund av ett pågående Azure-infrastrukturjobb.
  - Det inaktiverades tillfälligt av POA för att korrigera noden.

* Noden kan ha fastnat i nedläge eftersom:

  - Det placerades i ett dunläge manuellt.
  - Det genomgår en omstart (som kan utlösas av POA).
  - Den har en felaktig virtuell dator eller dator, eller det har problem med nätverksanslutningen.

### <a name="updates-were-skipped-on-some-nodes"></a>Uppdateringar hoppades över vissa noder

POA försöker installera en Windows-uppdatering enligt omplaneringsprincipen. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt programprincipen.

I ett sådant fall genereras en hälsorapport på varningsnivå mot nodagenttjänsten. Resultatet för Windows Update innehåller också den möjliga orsaken till felet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-is-being-installed"></a>Hälsotillståndet för klustret går till fel medan uppdateringen installeras

En felaktig Windows-uppdatering kan sänka hälsotillståndet för ett program eller kluster på en viss nod eller uppdateringsdomän. POA avbryter alla efterföljande Windows Update-åtgärder tills klustret är felfritt igen.

En administratör måste ingripa och avgöra varför programmet eller klustret blev felfritt på grund av Windows Update.

## <a name="poa-release-notes"></a>POA-viktig information

>[!NOTE]
> För POA-versioner 1.4.0 och senare kan du hitta viktig information och versioner på [utgivningssidan](https://github.com/microsoft/Service-Fabric-POA/releases/) för Patch Orchestration Application på GitHub.

### <a name="version-110"></a>Version 1.1.0
- Offentlig publicering

### <a name="version-111"></a>Version 1.1.1
- Åtgärdade ett fel i SetupEntryPoint för NodeAgentService som förhindrade installation av NodeAgentNTService.

### <a name="version-120"></a>Version 1.2.0

- Buggfixar runt arbetsflödet för omstart av systemet.
- Buggfix i skapandet av RM-uppgifter på grund av vilken hälsokontroll under utarbetandet av reparationsuppgifter inte hände som förväntat.
- Ändrade startläget för Windows-tjänsten POANodeSvc från auto till fördröjd auto.

### <a name="version-121"></a>Version 1.2.1

- Buggfix i arbetsflöde för klusterskalning. Introducerade skräpinsamlingslogik för POA-reparationsuppgifter som tillhör noder som inte finns.

### <a name="version-122"></a>Version 1.2.2

- Diverse buggfixar.
- Binärfilerna är nu signerade.
- Lade till sfpkg-länk för programmet.

### <a name="version-130"></a>Version 1.3.0

- Ställa installWindowsOSOnlyUpdates till false installerar nu alla tillgängliga uppdateringar.
- Ändrade logiken för att inaktivera automatiska uppdateringar. Detta åtgärdar ett fel där automatiska uppdateringar inte inaktiverades på Server 2016 och uppåt.
- Parameteriserad placeringsbegränsning för både mikrotjänster för POA för avancerade användningsfall.

### <a name="version-131"></a>Version 1.3.1
- Åtgärda regression där POA 1.3.0 inte fungerar på Windows Server 2012 R2 eller tidigare på grund av ett fel i att inaktivera automatiska uppdateringar. 
- Åtgärda fel där InstallWindowsOSOnlyUpdates konfiguration alltid plockas som sant.
- Ändra standardvärdet för InstallWindowsOSOnlyUpdates till False.

### <a name="version-132"></a>Version 1.3.2
- Åtgärda ett problem som påverkar korrigeringslivscykeln på en nod, om det finns noder med ett namn som är en delmängd av det aktuella nodnamnet. För sådana noder är det möjligt att korrigering har missats eller att en omstart väntar.
