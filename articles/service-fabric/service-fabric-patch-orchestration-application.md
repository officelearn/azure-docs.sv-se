---
title: Azure Service Fabric patch orchestration application | Microsoft Docs
description: Programmet att automatisera uppdatering av operativsystemet på Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 3416d257a23e94460199a1ddfe63302ff55ad5a5
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52285058"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Uppdatera Windows-operativsystemet i Service Fabric-klustret

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

[Azure VM-skalningsuppsättningen automatisk operativsystemuppgradering bild](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) är den bästa metoden för att se till att ditt operativsystem korrigerade i Azure och Patch Orchestration Application (POA) är en omslutning runt Service Fabrics RepairManager system service som gör configuration baserade OS patch schemaläggning för icke-Azure-värdbaserade kluster. POA krävs inte för icke-Azure-värdbaserade kluster, men schemaläggning patch-installationen genom att uppgradera domäner som krävs för att korrigera värdar för Service Fabric-kluster utan avbrott.

POA är ett Azure Service Fabric-program som automatiserar operativsystemet uppdatering i Service Fabric-kluster utan avbrott.

Patch orchestration appen tillhandahåller följande funktioner:

- **Automatisk uppdatering operativsysteminstallationen**. Operativsystemuppdateringar hämtas och installeras automatiskt. Klusternoder startas om vid behov utan klusterdriftstopp.

- **Klustermedveten uppdatering och hälsotillstånd integration**. När uppdateringar, övervakar patch orchestration appen hälsotillståndet för noderna i klustret. Klusternoder är uppgraderade en nod i taget eller en domän i taget. Om hälsotillståndet för klustret slutar att fungera på grund av korrigeringsprocessen stoppas korrigeringar för att förhindra aggravating problemet.

## <a name="internal-details-of-the-app"></a>Intern information om appen

Appen patch orchestration består av följande delkomponenter:

- **Tjänsten Coordinator**: den här tillståndskänsliga tjänsten ansvarar för att:
    - Samordna jobbet Windows Update på hela klustret.
    - Lagra resultatet av slutförda åtgärder för Windows Update.
- **Nod-agenttjänsten**: den tillståndslösa tjänsten körs på alla noder i Service Fabric-klustret. Tjänsten är ansvarig för:
    - Startprogram för Node Agent NTService.
    - Monitoring Agent-NTService noden.
- **Noden agenten NTService**: den här Windows NT-tjänst som körs på en högre nivå behörighet (SYSTEM). Däremot körs Node Agent-tjänsten och Coordinator-tjänsten vid privilegium på lägre nivå (NÄTVERKSTJÄNST). Tjänsten ansvarar för att utföra följande Windows Update-jobb på alla noder i klustret:
    - Inaktivera automatisk uppdatering för Windows på noden.
    - Hämta och installera Windows Update enligt principen har användaren har angett.
    - Startar om datorn efter installationen av Windows.
    - Ladda upp resultatet av Windows-uppdateringar till tjänsten Coordinator.
    - Reporting systemhälsorapporter om en åtgärd misslyckades efter få slut på alla nya försök.

> [!NOTE]
> Patch orchestration appen använder tjänsten Service Fabric reparera manager system att inaktivera eller aktivera noden och utföra hälsokontroller. Reparationsuppgiften som skapats av appen patch orchestration förloppet på Windows Update för varje nod.

## <a name="prerequisites"></a>Förutsättningar

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera reparation manager-tjänsten (om den inte körs redan)

Patch orchestration appen kräver reparera manager system-tjänsten ska vara aktiverat på klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure-kluster i silver hållbarhetsnivån har tjänsten reparera manager aktiverat som standard. Azure-kluster i guld hållbarhetsnivån kanske eller kanske inte tjänsten reparera manager aktiverat, beroende på när dessa kluster har skapats. Azure-kluster i Brons hållbarhetsnivån, som standard har inte reparera manager-tjänsten aktiveras. Om tjänsten redan är aktiverad, visas den i avsnittet system tjänster i Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Du kan aktivera reparationshanteraren från Azure-portalen vid tidpunkten för att ställa in i klustret. Välj **inkluderar Reparationshanteraren** alternativet **tilläggsfunktioner** vid tidpunkten för klusterkonfigurationen.
![Bild för att aktivera Reparationshanteraren från Azure-portalen](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager-distributionsmodellen
Du kan också använda den [distributionsmodellen Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) att reparera manager-tjänsten på nya och befintliga Service Fabric-kluster. Hämta mallen för det kluster som du vill distribuera. Du kan använda exempelmallarna, eller så kan du skapa en anpassad mall för Azure Resource Manager deployment model. 

Aktivera reparation manager tjänsten med [Azure Resource Manager-modellen Distributionsmall](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Först kontrollerar du att den `apiversion` är inställd på `2017-07-01-preview` för den `Microsoft.ServiceFabric/clusters` resurs. Om det skiljer sig så måste du uppdatera den `apiVersion` till värdet `2017-07-01-preview` eller senare:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera nu reparera manager-tjänsten genom att lägga till följande `addonFeatures` avsnittet efter den `fabricSettings` avsnittet:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. När du har uppdaterat klustermallen med de här ändringarna, tillämpar dem och låt uppgraderingen är klar. Du kan nu se reparera manager system-tjänsten körs i klustret. Den kallas `fabric:/System/RepairManagerService` i avsnittet system tjänster i Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Fristående lokala kluster

Du kan använda den [konfigurationsinställningar för fristående Windows-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) att reparera manager-tjänsten på nya och befintliga Service Fabric-kluster.

Aktivera reparation manager-tjänsten:

1. Först kontrollerar du att den `apiversion` i [Allmänt klusterkonfigurationer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) är inställd på `04-2017` eller senare:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Aktivera nu reparera manager-tjänsten genom att lägga till följande `addonFeatures` avsnittet efter den `fabricSettings` som visas nedan:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Uppdatera din klustermanifestet med dessa ändringar, med hjälp av den uppdaterade klustermanifestet [skapar ett nytt kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) eller [uppgradera klusterkonfigurationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). När klustret är i gång med uppdaterade klustermanifestet, kan du nu se reparera manager system-tjänsten körs i klustret, som kallas `fabric:/System/RepairManagerService`under system services-avsnittet i Service Fabric explorer.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Inaktivera automatisk uppdatering för Windows på alla noder

Automatiska uppdateringar för Windows kan leda till förlust av tillgängligheten eftersom flera klusternoder kan starta om på samma gång. Patch orchestration appen, försöker som standard inaktivera den automatiska uppdateringen för Windows på varje klusternod. Om inställningarna hanteras av en administratör eller en Grupprincip, rekommenderar vi dock att Windows Update-princip att ”meddela innan hämta” uttryckligen.

## <a name="download-the-app-package"></a>Hämta app-paket

Programmet tillsammans med installationsskript kan laddas ned från [Arkiv länk](https://go.microsoft.com/fwlink/?linkid=869566).

Program i sfpkg format kan laddas ned från [sfpkg länk](https://aka.ms/POA/POA_v1.2.2.sfpkg). Detta är praktiskt för [Azure Resource Manager-baserade programdistribution](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurera appen

Patch orchestration appens beteende kan konfigureras för att uppfylla dina behov. Åsidosätta standardvärdena genom att skicka in parametern program under programmet skapas eller uppdatera. Programparametrar kan anges genom att ange `ApplicationParameter` till den `Start-ServiceFabricApplicationUpgrade` eller `New-ServiceFabricApplication` cmdletar.

|**Parametern**        |**Typ**                          | **Detaljer**|
|:-|-|-|
|MaxResultsToCache    |Lång                              | Maximalt antal Windows Update-resultat, som ska cachelagras. <br>Standardvärdet är 3000 förutsatt att den: <br> -Antalet noder är 20. <br> -Antalet uppdateringar som sker på en nod per månad är fem. <br> – Antal resultat per åtgärd kan vara 10. <br> -Resultat för de senaste tre månaderna ska lagras. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy anger den princip som ska användas av Coordinator-tjänsten för att installera Windows-uppdateringar för Service Fabric-klusternoder.<br>                         Tillåtna värden är: <br>                                                           <b>NodeWise</b>. Windows Update är installerade en nod i taget. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update är installerade en uppgraderingsdomän i taget. (På högsta alla noder som tillhör en uppgraderingsdomän kan gå för Windows Update.)<br> Referera till [vanliga frågor och svar](#frequently-asked-questions) avsnittet om hur du avgör vilket är bäst lämpade princip för klustret.
|LogsDiskQuotaInMB   |Lång  <br> (Standard: 1024)               |Maximal storlek för patch orchestration app loggar i MB, vilket kan vara kvar lokalt på noderna.
| WUQuery               | sträng<br>(Standard ”: IsInstalled = 0”)                | Fråga för att hämta Windows-uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Boolesk <br> (standard: SANT)                 | Den här flaggan kan uppdateringarna för Windows-operativsystemet installeras.            |
| WUOperationTimeOutInMinutes | Int <br>(Standard: 90).                   | Anger tidsgränsen för alla Windows Update-åtgärder (Sök eller ladda ned eller installera). Om åtgärden inte har slutförts inom den angivna tidsgränsen, avbryts.       |
| WURescheduleCount     | Int <br> (Standard: 5).                  | Om en åtgärd misslyckas så att uppdatera det maximala antalet gånger som tjänsten schemalägger om i Windows.          |
| WURescheduleTimeInMinutes | Int <br>(Standard: 30). | Intervallet då tjänsten schemalägger om Windows-uppdateringen om felet kvarstår. |
| WUFrequency           | Kommaseparerad sträng (standard: ”varje vecka, Onsdag 7:00:00”)     | Frekvensen för att installera Windows-uppdatering. Format och möjliga värden är: <br>-: Som mm: ss månads-, DD, till exempel varje månad, 5, 12: 22:32. <br> -Varje vecka, dag,: mm: ss, för exempelvis varje vecka, tisdag, 12:22:32.  <br> -Varje dag,: mm: ss, till exempel varje dag, 12:22:32.  <br> -Ingen indikerar att Windows Update inte bör göras.  <br><br> Observera att tiderna är i UTC.|
| AcceptWindowsUpdateEula | Boolesk <br>(Standard: SANT) | Genom att ange den här flaggan accepterar programmet slutanvändarens licens för Windows Update för ägare för datorn.              |

> [!TIP]
> Om du vill att Windows Update sker omedelbart anger `WUFrequency` i förhållande till tidpunkten för distribution av programmet. Anta att du har ett testkluster med fem noder och planerar att distribuera appen cirka 17:00:00 UTC. Om du anta att uppgradera programmet eller distributionen tar 30 minuter på högsta, anger du WUFrequency som ”varje dag, 17:30:00”.

## <a name="deploy-the-app"></a>Distribuera appen

1. Slut alla nödvändiga steg för att förbereda klustret.
2. Distribuera patch orchestration-appen som med andra Service Fabric-app. Du kan distribuera appen med hjälp av PowerShell. Följ stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. För att konfigurera programmet vid tidpunkten för distribution, skickar den `ApplicationParameter` till den `New-ServiceFabricApplication` cmdlet. Vi tillhandahåller skriptet Deploy.ps1 tillsammans med programmet för din bekvämlighet. Du använder skriptet:

    - Ansluta till ett Service Fabric-kluster med hjälp av `Connect-ServiceFabricCluster`.
    - Kör PowerShell-skriptet Deploy.ps1 med lämplig `ApplicationParameter` värde.

> [!NOTE]
> Behåll skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="upgrade-the-app"></a>Uppgradera appen

Om du vill uppgradera en befintlig patch orchestration app med hjälp av PowerShell, följer du stegen i [uppgradering av Service Fabric-programmet med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Ta bort appen

Om du vill ta bort programmet, följer du stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Vi tillhandahåller skriptet Undeploy.ps1 tillsammans med programmet för din bekvämlighet. Du använder skriptet:

  - Ansluta till ett Service Fabric-kluster med hjälp av ```Connect-ServiceFabricCluster```.

  - Kör PowerShell-skriptet Undeploy.ps1.

> [!NOTE]
> Behåll skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="view-the-windows-update-results"></a>Visa resultat för Windows Update

Appen patch orchestration exponerar REST-API: er för att visa historiska resultat för användaren. Ett exempel på resultatet JSON:
```json
[
  {
    "NodeName": "_stg1vm_1",
    "WindowsUpdateOperationResults": [
      {
        "OperationResult": 0,
        "NodeName": "_stg1vm_1",
        "OperationTime": "2017-05-21T11:46:52.1953713Z",
        "UpdateDetails": [
          {
            "UpdateId": "7392acaf-6a85-427c-8a8d-058c25beb0d6",
            "Title": "Cumulative Security Update for Internet Explorer 11 for Windows Server 2012 R2 (KB3185319)",
            "Description": "A security issue has been identified in a Microsoft software product that could affect your system. You can help protect your system by installing this update from Microsoft. For a complete listing of the issues that are included in this update, see the associated Microsoft Knowledge Base article. After you install this update, you may have to restart your system.",
            "ResultCode": 0
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

Fälten i JSON som beskrivs nedan.

Fält | Värden | Information
-- | -- | --
OperationResult | 0 – lyckades<br> 1 – slutfördes med fel<br> 2 – misslyckades<br> 3 – avbröts<br> 4 – avbröts med tidsgräns | Visar resultatet av övergripande åtgärden (vanligtvis som rör installation av en eller flera uppdateringar).
Resultatkod | Samma som OperationResult | Det här fältet visar resultatet av installationen igen för en enskild uppdatering.
Åtgärdstyp | 1 – installation<br> 0 – Sök efter och ladda ned.| Installationen är den enda OperationType som visas i resultaten som standard.
WindowsUpdateQuery | Standardvärdet är ”IsInstalled = 0” |Windows uppdaterar fråga som används för att söka efter uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | True - krävdes omstart<br> FALSE - omstart behövs inte | Anger om omstart krävdes för att slutföra installationen av uppdateringar.

Om ingen uppdatering har schemalagts ännu är resultatet JSON tom.

Logga in till klustret för att fråga Windows Update resultat. Sedan ta reda på replik-adressen för primärt av Coordinator-tjänsten och tryck på URL: en i webbläsaren: http://&lt;REPLIKEN IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1 / GetWindowsUpdateResults.

REST-slutpunkten för tjänsten Coordinator har en dynamisk port. Du kan kontrollera den exakta Webbadressen genom att referera till Service Fabric Explorer. Till exempel resultaten är tillgängliga på `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Bild av REST-slutpunkt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Du kan komma åt URL: en från utanför klustret samt om omvänd proxy är aktiverat i klustret.
Den slutpunkt som måste vara når är http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Om du vill aktivera omvänd proxy i klustret, följer du stegen i [omvänd proxy i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> När omvänd proxy har konfigurerats kan är alla mikrotjänster i klustret som Exponerar en HTTP-slutpunkt adresserbara från utanför klustret.

## <a name="diagnosticshealth-events"></a>Diagnostik/health-händelser

### <a name="diagnostic-logs"></a>Diagnostikloggar

Patch orchestration-appen loggar samlas in som en del av loggar för Service Fabric runtime.

Om du vill samla in loggar via diagnostiska verktyg/pipeline valfri. Patch orchestration application använder nedan fast provider-ID: N för att logga händelser via [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälsorapporter

Patch orchestration appen publiceras även hälsorapporter mot tjänsten Coordinator eller nod-agenttjänsten i följande fall:

#### <a name="a-windows-update-operation-failed"></a>En Windows Update-åtgärden misslyckades

Om en Windows Update-åtgärd misslyckas på en nod, genereras en hälsorapport mot Node Agent-tjänsten. Information om hälsorapporten innehålla problematiska nodnamnet.

När uppdateringar har slutförts på noden problematiska, rensas automatiskt rapporten.

#### <a name="the-node-agent-ntservice-is-down"></a>Noden agenten NTService är nere

Om noden agenten NTService är nere på en nod kan genereras en varning på servernivå hälsorapport mot Node Agent-tjänsten.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Reparera manager-tjänsten är inte aktiverad

Om tjänsten reparera manager inte kan hittas på klustret, genereras en varning på servernivå hälsorapport för Coordinator-tjänsten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. **Varför ser jag mitt kluster i ett feltillstånd när patch orchestration-appen körs?**

A. Under installationen, patch orchestration appen inaktiverar eller startar om noderna, vilket kan tillfälligt resultera i hälsotillståndet för klustret slutar fungera.

Baserat på principen för programmet, antingen en nod kan gå under en uppdatering *eller* en hel uppgraderingsdomän kan gå samtidigt.

I slutet av installationen av uppdateringen för Windows-noderna är reenabled efter omstart.

I följande exempel klustret gick in i ett feltillstånd tillfälligt eftersom två noder har nedåt och MaxPercentageUnhealthNodes principen har överskridits. Felet är tillfälligt tills uppdatering åtgärd pågår.

![Bild av felaktiga kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Om problemet kvarstår, finns i avsnittet Felsökning.

F. **Patch orchestration-appen är i varningstillstånd**

A. Kontrollera om en hälsorapport som publicerats i programmet är den grundläggande orsaken. Varningen innehåller vanligtvis information om problemet. Om problemet är tillfälligt, förväntas programmet återskapa automatiskt från det här läget.

F. **Vad kan jag göra om mitt kluster är i feltillstånd och jag behöver du en brådskande operativsystemuppdateringen?**

A. Patch orchestration appen installeras inte uppdateringar medan klustret är i feltillstånd. Försök att ta med ditt kluster till ett felfritt tillstånd att avblockera patch orchestration app-arbetsflöde.

F. **Bör ange i TaskApprovalPolicy som 'NodeWise' eller 'UpgradeDomainWise' för mitt kluster?**

A. 'UpgradeDomainWise' gör övergripande klustret korrigeringar snabbare genom att åtgärda alla noder som tillhör en uppgraderingsdomän parallellt. Det innebär att noder som tillhör en hela uppgraderingsdomän skulle vara otillgänglig (i [inaktiverad](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) tillstånd) under korrigeringsprocessen.

Däremot NodeWise' princip korrigeringar bara en nod åt gången, detta innebär övergripande uppdatering av klustret tar längre tid. Men vid max, endast en nod blir otillgänglig (i [inaktiverad](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) tillstånd) under korrigeringsprocessen.

Om klustret kan tolerera som körs på N-1 antal uppgraderingsdomäner under uppdatering cykel (där N är det totala antalet uppgraderingsdomäner i ditt kluster), kan du ställa in principen som ”UpgradeDomainWise”, annars ange den till ”NodeWise”.

F. **Hur mycket tid gör det gör att korrigera en nod?**

A. Uppdatering av en nod kan ta några minuter (till exempel: [Windows Defenders definitionsuppdateringar](https://www.microsoft.com/wdsi/definitions)) till timmar (till exempel: [Windows kumulativa uppdateringar](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Tid som krävs för att korrigera en nod beror oftast på 
 - Storleken på uppdateringar
 - Antal uppdateringar som ska användas i en uppdateringsperiod
 - Tiden det tar att installera uppdateringarna, starta om noden (vid behov) och slutför installationsstegen utförs efter omstart.
 - Prestanda för virtuell dator/dator och nätverk.

F. **Hur lång tid tar det för att korrigera ett helt kluster?**

A. Den tid som behövs för att korrigera ett helt kluster beror på följande faktorer:

- Tid som krävs att korrigera en nod.
- Princip för Coordinator-tjänsten. -Standardprincipen `NodeWise`, resulterar i korrigeringar bara en nod i taget, som är långsammare än `UpgradeDomainWise`. Till exempel: om en nod tar ca 1 timme att korrigera kan gå igenom att korrigera uzel 20 (samma typ av noder) kluster med 5 uppgraderingsdomäner, som innehåller 4 noder.
    - Det bör ta ~ 20 timmar att korrigera hela klustret, om principen är `NodeWise`
    - Det bör ta ~ 5 timmar om principen är `UpgradeDomainWise`
- Klustret Läs in – varje uppdatering åtgärden kräver att flytta arbetsbelastningen kunden till andra tillgängliga noder i klustret. Noden som för tillfället patch stå i [inaktiveras](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) tillstånd under den här tiden. Om klustret körs nära hög belastning, tar Inaktivera processen längre tid. Därför verkar övergripande korrigeringsprocessen vara långsam under under belastning förhållanden.
- Alla kluster hälsofel under uppdatering - [försämring](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) i [för klustrets hälsotillstånd](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) skulle avbryta korrigeringsprocessen. Detta lägger till i den totala tid som krävs för att korrigera hela klustret.

F. **Varför ser jag vissa uppdateringar i Windows Update resultaten via REST-API, men inte under Windows Update-historiken på datorn?**

A. Vissa produktuppdateringar endast visas i deras respektive uppdateringar/korrigeringar historiken. Uppdateringar för Windows Defender kan eller inte kan visas i Windows Update-historiken på Windows Server 2016.

F. **Dirigering app användas för att korrigera mitt dev-kluster (kluster med en nod)?**

A. Nej, Patch orchestration appen kan inte användas till patch kluster med en nod. Den här begränsningen är avsiktligt, som [service fabric-systemtjänster](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) eller kundappar kommer att stöta på avbrott och reparation jobb för uppdatering skulle därför aldrig godkännas av reparationshanteraren.

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- Appen patch orchestration accepterar slutanvändarens licens av Windows Update för användarens räkning. Du kan också kan inställningen inaktiveras i konfigurationen av programmet.

- Patch orchestration appen samlar in telemetri för att spåra användning och prestanda. Programmets telemetri följer inställningen för Service Fabric-körningen telemetri inställningen (som är aktiverad som standard).

## <a name="troubleshooting"></a>Felsökning

### <a name="a-node-is-not-coming-back-to-up-state"></a>En nod kommer inte tillbaka till upp tillstånd

**Noden kan ha fastnat i tillståndet inaktivera eftersom**:

En kontroll för säkerhet är väntande. Du kan åtgärda detta genom att se till att tillräckligt många noder är tillgängliga i felfritt tillstånd.

**Noden kan ha fastnat i ett inaktiverat tillstånd eftersom**:

- Noden har inaktiverats manuellt.
- Noden har inaktiverats på grund av ett pågående Azure-infrastruktur-jobb.
- Noden har tillfälligt inaktiverats av appen patch orchestration att korrigera noden.

**Noden kan ha fastnat i en ned-läge eftersom**:

- Noden placerades i tillståndet på manuellt.
- Noden genomgår en omstart (som kan utlösas av patch orchestration-app).
- Noden är nere på grund av en felaktig VM eller dator eller nätverk anslutningsproblem.

### <a name="updates-were-skipped-on-some-nodes"></a>Uppdateringar hoppades över på vissa noder

Patch orchestration appen försöker installera en Windows-uppdatering enligt omplanering principen. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt princip för program.

I detta fall är genereras en varning på servernivå hälsorapport mot Node Agent-tjänsten. Resultatet för Windows Update innehåller också möjlig orsak till felet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Hälsotillståndet för klustret som leder till fel när uppdateringen har installerats

En felaktig Windows-uppdatering kan påverkar hälsotillståndet för ett program eller -kluster i en viss nod eller en uppgraderingsdomän. Patch orchestration-appen upphör alla efterföljande Windows Update-åtgärder tills att klustret är felfritt igen.

En administratör måste ingripa och avgöra varför programmet eller kluster fick dåligt hälsotillstånd på grund av Windows Update.

## <a name="release-notes"></a>Viktig information

### <a name="version-110"></a>Version 1.1.0
- Publiceringen

### <a name="version-111"></a>Version 1.1.1
- Ett fel har åtgärdats i SetupEntryPoint av NodeAgentService som hindrar installationen av NodeAgentNTService.

### <a name="version-120"></a>Version 1.2.0

- Felkorrigeringar runt system starta om arbetsflödet.
- Felkorrigering i skapandet av RM uppgifter på grund av vilka hälsotillstånd Kontrollera under förberedelserna reparera uppgifter inte händer som förväntat.
- Ändra startmetoden för windows-tjänsten POANodeSvc från automatisk fördröjd Auto.

### <a name="version-121"></a>Version 1.2.1

- Felkorrigering i klustret skala ned arbetsflöde. Introducerade skräpinsamling samling logik för POA reparera uppgifter som hör till icke-existerande noder.

### <a name="version-122-latest"></a>Version 1.2.2 (senaste)

- Diverse felkorrigeringar.
- Binärfiler är nu inloggad.
- länk för hämtning av sfpkg pekar nu på en specifik version.
