---
title: Azure Service Fabric korrigering orchestration-program | Microsoft Docs
description: "Program för att automatisera operativsystemet korrigering på ett Service Fabric-kluster."
services: service-fabric
documentationcenter: .net
author: novino
manager: timlt
editor: 
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/16/2018
ms.author: nachandr
ms.openlocfilehash: bb3afdd3afa81664589f738945a63d20013d5291
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Korrigering av Windows-operativsystemet i Service Fabric-kluster

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Korrigering av orchestration-programmet är ett Azure Service Fabric som automatiserar operativsystemet korrigering på ett Service Fabric-kluster utan driftavbrott.

Korrigering orchestration appen tillhandahåller följande funktioner:

- **Automatisk uppdatering operativsysteminstallation**. Uppdateringar av operativsystemet hämtas och installeras automatiskt. Noder i klustret startas om vid behov utan klusterdriftstopp.

- **Klustermedveten uppdatering och hälsa integration**. När du tillämpar uppdateringar övervakar korrigering orchestration appen hälsan för klusternoderna. Klusternoder är uppgraderade en nod i taget eller en domän i taget. Om hälsotillståndet för klustret slutar att fungera på grund av uppdatering processen stoppas korrigering för att förhindra aggravating problemet.

## <a name="internal-details-of-the-app"></a>Intern information om appen

Korrigering orchestration appen består av följande delkomponenter:

- **Coordinator-tjänsten**: tillståndskänslig tjänsten ansvarar för att:
    - Samordning av Windows Update-jobbet på hela klustret.
    - Lagra resultatet av slutförda Windows Update-åtgärder.
- **Nod-agenttjänsten**: tillståndslösa tjänsten körs på alla klusternoder för Service Fabric. Tjänsten ansvarar för:
    - Startprogram för noden Agent NTService.
    - Övervakning av noden Agent NTService.
- **Noden Agent NTService**: den här Windows NT-tjänsten körs på en högre nivå behörighet (SYSTEM). Däremot nod Agent-tjänsten och Coordinator-tjänsten som körs på en lägre nivå behörighet (NETWORK SERVICE). Tjänsten ansvarar för att utföra följande Windows Update-jobb på alla noder i klustret:
    - Inaktiverar automatisk Windows Update på noden.
    - Hämta och installera Windows Update enligt principen har användaren angett.
    - Starta om datorn efter installationen av Windows Update.
    - Överför resultat av Windows-uppdateringar till Coordinator-tjänsten.
    - Reporting systemhälsorapporter om en åtgärd misslyckades efter överbelastar alla nya försök.

> [!NOTE]
> Korrigering orchestration appen använder tjänsten Service Fabric reparera manager system för att inaktivera eller aktivera noden och utföra hälsokontroller. Reparationsuppgiften som skapats av korrigering orchestration appen spårar förloppet för Windows Update för varje nod.

## <a name="prerequisites"></a>Förutsättningar

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera tjänsten reparera manager (om det inte redan körs)

Korrigering orchestration appen kräver tjänsten reparera manager system måste vara aktiverade på klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure-kluster i silver hållbarhetsnivån har tjänsten reparera manager aktiverad som standard. Azure-kluster i guld hållbarhetsnivån kanske eller kanske inte reparera manager-tjänsten aktiverad, beroende på när dessa kluster skapades. Azure-kluster i Brons hållbarhetsnivån som standard har inte reparera manager-tjänsten aktiverad. Om den redan är aktiverad, visas den i avsnittet system tjänster i Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Du kan aktivera reparera manager från Azure-portalen när du konfigurerar i klustret. Välj **inkluderar reparera Manager** alternativ **tilläggsfunktioner** vid tidpunkten för klusterkonfigurationen.
![Bild för att aktivera reparera Manager från Azure-portalen](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager-distributionsmodellen
Du kan också använda den [Azure Resource Manager-distributionsmodellen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) att reparera manager-tjänsten på nya och befintliga Service Fabric-kluster. Hämta mallen för det kluster som du vill distribuera. Du kan använda exempelmallarna, eller så kan du skapa en anpassad mall för Azure Resource Manager distribution modellen. 

Så här aktiverar du reparera manager service med [Azure Resource Manager modellen Distributionsmall](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Kontrollera först att den `apiversion` är inställd på `2017-07-01-preview` för den `Microsoft.ServiceFabric/clusters` resurs. Om det är olika, så måste du uppdatera den `apiVersion` till värdet `2017-07-01-preview` eller senare:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera nu reparera manager-tjänsten genom att lägga till följande `addonFeatures` avsnittet efter den `fabricSettings` avsnitt:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. När du har uppdaterat mallen för klustret med de här ändringarna, använda dem och låt uppgraderingen är klar. Du kan nu se reparera manager system-tjänsten körs i klustret. Den kallas `fabric:/System/RepairManagerService` i avsnittet system tjänster i Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Fristående lokala kluster

Du kan använda den [konfigurationsinställningar för Windows-kluster för fristående](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) att reparera manager-tjänsten på nya och befintliga Service Fabric-klustret.

Aktivera tjänsten reparera manager:

1. Kontrollera först att den `apiversion` i [allmänna klusterkonfigurationer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) är inställd på `04-2017` eller senare:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Aktivera nu reparera manager-tjänsten genom att lägga till följande `addonFeatures` avsnittet efter den `fabricSettings` enligt nedan:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Uppdatera din klustermanifestet med ändringarna med hjälp av det uppdaterade klustermanifestet [skapa ett nytt kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) eller [uppgradera klusterkonfigurationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server#Upgrade-the-cluster-configuration). När klustret körs med uppdaterade klustermanifestet kan du nu se reparera manager system-tjänsten körs i klustret, som kallas `fabric:/System/RepairManagerService`under system services-avsnittet i Service Fabric explorer.

### <a name="disable-automatic-windows-update-on-all-nodes"></a>Inaktivera automatisk uppdatering för Windows på alla noder

Automatiska uppdateringar kan det leda till förlust av tillgänglighet eftersom flera klusternoder kan starta om på samma gång. Appen korrigering orchestration försöker som standard inaktivera automatisk uppdatering för Windows på varje nod i klustret. Om inställningarna hanteras av en administratör eller en Grupprincip, rekommenderar vi dock ställa in Windows Update-princip att ”Avisera innan hämta” explicit.

## <a name="download-the-app-package"></a>Hämta app-paket

Hämta programmet från den [Hämta länk](https://go.microsoft.com/fwlink/P/?linkid=849590).

## <a name="configure-the-app"></a>Konfigurera appen

Korrigering av orchestration appens beteende kan konfigureras för att uppfylla dina behov. Åsidosätta standardvärdena genom att passera i parametern program under skapa program eller uppdatering. Parametrar för program kan anges genom att ange `ApplicationParameter` till den `Start-ServiceFabricApplicationUpgrade` eller `New-ServiceFabricApplication` cmdlets.

|**Parameter**        |**Typ**                          | **Detaljer**|
|:-|-|-|
|MaxResultsToCache    |Lång                              | Maximalt antal Windows Update-resultat, som ska cachelagras. <br>Standardvärdet är 3000 under förutsättning att den: <br> -Antalet noder är 20. <br> -Antalet uppdateringar som händer på en nod per månad är fem. <br> -Antalet resultat per åtgärd kan vara 10. <br> -Resultat för de senaste tre månaderna ska lagras. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy anger den princip som ska användas av Coordinator-tjänsten för att installera Windows-uppdateringar för Service Fabric-klusternoder.<br>                         Tillåtna värden är: <br>                                                           <b>NodeWise</b>. Windows Update är installerade en nod i taget. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update är installerade en domän i taget. (Max, alla noder som tillhör en domän kan gå för Windows Update.)
|LogsDiskQuotaInMB   |Lång  <br> (Standard: 1024)               |Maximal storlek för korrigering orchestration app loggar i MB, vilket kan sparas lokalt på noder.
| WUQuery               | sträng<br>(Default: "IsInstalled=0")                | Frågan att hämta Windows-uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Boolesk <br> (standard: True)                 | Den här flaggan kan uppdateringar av Windows-operativsystemet installeras.            |
| WUOperationTimeOutInMinutes | Int <br>(Standard: 90).                   | Anger timeout för någon Windows Update-åtgärd (Sök eller ladda ned eller installera). Om åtgärden inte slutförs inom den angivna tidsgränsen, avbryts.       |
| WURescheduleCount     | Int <br> (Standard: 5).                  | Om en åtgärd misslyckas så att uppdatera det maximala antalet gånger som tjänsten schemaläggs automatiskt på nytt i Windows.          |
| WURescheduleTimeInMinutes | Int <br>(Standard: 30). | Intervallet då tjänsten schemaläggs automatiskt på nytt Windows update om felet kvarstår. |
| WUFrequency           | Kommaavgränsad sträng (standard: ”vecka, Onsdag 7:00:00”)     | Frekvensen för att installera Windows Update. Format och möjliga värden är: <br>-: Som mm: ss varje månad, DD, till exempel varje månad, 5, 12: 22:32. <br> -Varje vecka, dag,: mm: ss, för exempelvis varje vecka, tisdag, 12:22:32.  <br> -Varje dag,: mm: ss, till exempel dagligen, 12:22:32.  <br> -Ingen anger att Windows Update inte utföras.  <br><br> Observera att gånger i UTC.|
| AcceptWindowsUpdateEula | Boolesk <br>(Standard: true) | Genom att ange den här flaggan accepterar programmet slutanvändarens licensavtal för Windows Update för ägare för datorn.              |

> [!TIP]
> Om du vill att Windows Update sker omedelbart anger `WUFrequency` i förhållande till tidpunkten för distribution av programmet. Anta att du har ett testkluster med fem noder och planerar att distribuera appen vid cirka 17:00:00 UTC. Om du anta att programmet uppgraderingen eller distributionen tar 30 minuter Max, anger du WUFrequency som ”varje dag, 17:30:00”.

## <a name="deploy-the-app"></a>Distribuera appen

1. Slut alla nödvändiga steg för att förbereda klustret.
2. Distribuera appen korrigering orchestration som andra Service Fabric-app. Du kan distribuera appen med hjälp av PowerShell. Följ stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. För att konfigurera programmet vid tidpunkten för distribution, skickar den `ApplicationParamater` till den `New-ServiceFabricApplication` cmdlet. Vi har samlat skriptet Deploy.ps1 tillsammans med programmet för din bekvämlighet. Du använder skriptet:

    - Ansluta till ett Service Fabric-kluster med hjälp av `Connect-ServiceFabricCluster`.
    - Köra PowerShell-skriptet Deploy.ps1 med lämplig `ApplicationParameter` värde.

> [!NOTE]
> Spara skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="upgrade-the-app"></a>Uppgradera appen

Om du vill uppgradera en befintlig korrigering orchestration app med hjälp av PowerShell, följer du stegen i [uppgradering av Service Fabric-programmet med PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Ta bort appen

Om du vill ta bort programmet, följer du stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

Vi har samlat skriptet Undeploy.ps1 tillsammans med programmet för din bekvämlighet. Du använder skriptet:

  - Ansluta till ett Service Fabric-kluster med hjälp av ```Connect-ServiceFabricCluster```.

  - Köra PowerShell-skriptet Undeploy.ps1.

> [!NOTE]
> Spara skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="view-the-windows-update-results"></a>Visa resultaten för Windows Update

Korrigering orchestration appen exponerar REST API: er för att visa historiska resultat för användaren. Ett exempel på resultat JSON:
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

Fält av JSON beskrivs nedan.

Fält | Värden | Information
-- | -- | --
OperationResult | 0 - lyckades<br> 1 - slutförd med fel<br> 2 - misslyckades<br> 3 - avbröts<br> 4 - avbröts med tidsgräns | Visar resultatet av övergripande (vanligtvis som innefattar installationen av en eller flera uppdateringar).
ResultCode | Samma som OperationResult | Fältet visar resultatet av installationen för en enskild uppdatering.
OperationType | 1 - installation<br> 0 - sökning och hämtning.| Installationen är den enda OperationType som visas i resultaten som standard.
WindowsUpdateQuery | Standardvärdet är ”IsInstalled = 0” |Windows update-fråga som användes för att söka efter uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | True - krävdes omstart<br> FALSE - omstart behövs inte | Anger om omstart krävs för att slutföra installationen av uppdateringar.

Om ingen uppdatering har schemalagts ännu är resultatet JSON tom.

Logga in till klustret för att fråga Windows Update resultat. Sedan reda på replik-adressen för primär av Coordinator-tjänsten och tryck URL i webbläsaren: http://&lt;REPLIK-IP-&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetWindowsUpdateResults.

REST-slutpunkt för Coordinator-tjänsten har en dynamisk port. Referera till Service Fabric Explorer för att kontrollera en exakt Webbadress. Till exempel resultaten finns på `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Bild av REST-slutpunkt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Du kan komma åt URL: en från utanför klustret samt om omvänd proxy är aktiverat på klustret.
Den slutpunkt som måste vara träffar är http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

För att aktivera omvänd proxy i klustret, följer du stegen i [omvänd proxy i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> När omvänd proxy har konfigurerats, är alla micro tjänster i klustret som Exponerar en HTTP-slutpunkt adresserbara från utanför klustret.

## <a name="diagnosticshealth-events"></a>Diagnostik/hälsotillstånd händelser

### <a name="diagnostic-logs"></a>Diagnostikloggar

Korrigering av orchestration app loggarna har samlats in som en del av Service Fabric runtime loggar.

Om du vill samla in loggar via diagnostiska verktyg/pipeline önskat. Korrigering orchestration programmet använder nedan fast providern ID: N för att logga händelser via [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälsorapporter

Korrigering orchestration appen publiceras även hälsorapporter mot Coordinator-tjänsten eller nod Agent-tjänsten i följande fall:

#### <a name="a-windows-update-operation-failed"></a>En Windows Update-åtgärden misslyckades

Om en Windows Update-åtgärden misslyckas på en nod, skapas en hälsorapport mot nod Agent-tjänsten. Information om hälsorapporten innehålla problematiska nodnamn.

När uppdatering har slutförts på noden problematiskt, avmarkeras automatiskt i rapporten.

#### <a name="the-node-agent-ntservice-is-down"></a>Noden Agent NTService är nere

Om noden Agent NTService är igång på en nod kan genereras en varning nivå hälsorapport mot nod Agent-tjänsten.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Reparera manager-tjänsten är inte aktiverad

Om reparationen manager-tjänsten inte kan hittas på klustret, skapas en varningsnivå hälsorapport för Coordinator-tjänsten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

FRÅGOR. **Varför ser jag min kluster i ett feltillstånd när korrigering orchestration appen körs?**

A. Under installationen av appen korrigering orchestration inaktiverar eller startar om noder som tillfälligt kan resultera i hälsotillståndet för klustret gå.

Baserat på principen för programmet, antingen en nod kan gå under en uppdatering åtgärd *eller* en hel uppgraderingsdomän kan gå samtidigt.

I slutet av installationen av uppdateringen för Windows noderna reenabled efter omstart.

I följande exempel klustret ledde till ett feltillstånd tillfälligt eftersom två noder har ned och MaxPercentageUnhealthNodes principen har överskridits. Felet är tillfällig tills åtgärden uppdatering pågår.

![Bild av feltillstånd kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Om problemet kvarstår finns i avsnittet Felsökning.

FRÅGOR. **Korrigering av orchestration appar är i varningstillstånd**

A. Kontrollera om en hälsorapport bokförd i programmet är den grundläggande orsaken. Varningen innehåller vanligtvis information om problemet. Om problemet är tillfälligt, förväntas programmet återskapa automatiskt från det här läget.

FRÅGOR. **Vad gör jag om min klustret är i feltillstånd och måste du göra en uppdatering för brådskande operativsystem?**

A. Korrigering orchestration appen installeras inte uppdateringar när klustret är i feltillstånd. Försök att ta med ditt kluster till ett felfritt tillstånd att avblockera korrigering orchestration app arbetsflödet.

FRÅGOR. **Varför korrigering genom datorkluster tar så lång tid för att köra?**

A. Den tid som krävs av korrigering orchestration appen beror oftast på följande faktorer:

- Principen för Coordinator-tjänsten. 
  - Standardprincipen, `NodeWise`, resulterar i korrigering endast en nod i taget. Särskilt om det finns ett större kluster, rekommenderar vi att du använder den `UpgradeDomainWise` att uppnå snabbare korrigering genom datorkluster.
- Antalet uppdateringar som är tillgängliga för hämtning och installation. 
- Genomsnittlig tid det behövs för att ladda ned och installera en uppdatering som får inte överstiga några timmar.
- Prestanda för den virtuella dator och nätverket bandbredden.

FRÅGOR. **Varför ser vissa uppdateringar i Windows Update resultaten via REST-API, men inte under Windows Update-historiken på datorn?**

A. Vissa produktuppdateringar visas endast i sina respektive uppdatering/korrigering historiken. Till exempel visas uppdateringar för Windows Defender inte i Windows Update-historiken på Windows Server 2016.

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- Korrigering orchestration appen accepterar slutanvändarens licensavtal för Windows Update för användarens räkning. Alternativt kan kan inställningen inaktiveras i konfigurationen för programmet.

- Korrigering orchestration appen samlar in telemetri om du vill spåra användning och prestanda. Programmets telemetri följer inställningen för Service Fabric-körningsmiljön telemetri inställningen (som är aktiverad som standard).

## <a name="troubleshooting"></a>Felsökning

### <a name="a-node-is-not-coming-back-to-up-state"></a>En nod som inte kommer tillbaka på upp tillstånd

**Noden kan ha fastnat i tillståndet inaktivera eftersom**:

En säkerhets-kontroll väntar. Du kan åtgärda detta genom att se till att tillräckligt många noder är tillgängliga i felfritt tillstånd.

**Noden kan ha fastnat i ett inaktiverat tillstånd eftersom**:

- Noden har inaktiverats manuellt.
- Noden har inaktiverats på grund av ett pågående Azure-infrastrukturen jobb.
- Noden har inaktiverats tillfälligt av korrigering orchestration appen att uppdatera noden.

**Noden kan ha fastnat i ned-läge eftersom**:

- Noden placerades i tillståndet nedåt manuellt.
- Noden genomgår en omstart (som kan utlösas av korrigering orchestration-app).
- Noden inte är på grund av en felaktig virtuell dator eller datorn eller nätverket anslutningsproblem.

### <a name="updates-were-skipped-on-some-nodes"></a>Uppdateringar hoppades över på vissa noder

Korrigering orchestration-appen försöker installera en Windows update enligt omplanering principen. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt principen för program.

I sådana fall genereras en varning nivå hälsorapport mot nod Agent-tjänsten. Resultat för Windows Update innehåller också möjliga orsaker till felet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Hälsotillståndet för klustret går till när uppdateringen har installerats

En felaktig Windows update kan skada hälsotillståndet för ett program eller ett kluster på en viss nod eller en domän. Korrigering orchestration app upphör alla efterföljande Windows Update-åtgärden tills klustret är felfri igen.

En administratör måste ingripa och fastställa varför programmet eller kluster blev inte hälsosamt eftersom Windows Update.

## <a name="release-notes"></a>Viktig information

### <a name="version-110"></a>Version 1.1.0
- Offentliga utgåvan

### <a name="version-111"></a>Version 1.1.1
- Fast ett programfel i SetupEntryPoint av NodeAgentService som förhindrade installationen av NodeAgentNTService.

### <a name="version-120-latest"></a>Version 1.2.0 (Latest)

- Felkorrigeringar runt system starta om arbetsflödet.
- Buggfix för att skapa RM uppgifter på grund av vilka hälsotillstånd Kontrollera under förberedelserna reparera uppgifter inte händer som förväntat.
- Ändra startmetoden för windows-tjänst POANodeSvc från automatisk till fördröjd auto.
