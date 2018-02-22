---
title: "Azure Service Fabric korrigering orchestration-program för linux | Microsoft Docs"
description: "Program för att automatisera operativsystemet uppdatering på en Linux Service Fabric-klustret."
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
ms.date: 1/22/2018
ms.author: nachandr
ms.openlocfilehash: dac8068705e284b04d84d128eb1ce62c459d44ff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Korrigering av Linux-operativsystem i Service Fabric-kluster

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
    - Samordna OS Uppdateringsjobbet på hela klustret.
    - Lagra resultatet av slutförda OS uppdateringsåtgärder.
- **Nod-agenttjänsten**: tillståndslösa tjänsten körs på alla klusternoder för Service Fabric. Tjänsten ansvarar för:
    - Startprogram för noden Agent daemon på Linux.
    - Övervaka tjänsten daemon.
- **Noden Agent daemon**: den här Linux daemon-tjänsten körs på en högre nivå behörighet (rot). Däremot nod Agent-tjänsten och Coordinator-tjänsten som körs på en lägre nivå behörighet. Tjänsten ansvarar för att utföra följande uppdateringsjobb på alla noder i klustret:
    - Inaktiverar automatisk OS-uppdatering på noden.
    - Hämta och installera uppdateringen OS enligt principen har användaren angett.
    - Starta om datorn efter installationen av uppdateringen OS om det behövs.
    - Ladda upp resultatet av operativsystemuppdateringar Coordinator-tjänsten.
    - Reporting systemhälsorapporter om en åtgärd misslyckades efter överbelastar alla nya försök.

> [!NOTE]
> Korrigering orchestration appen använder tjänsten Service Fabric reparera manager system för att inaktivera eller aktivera noden och utföra hälsokontroller. Reparationsuppgiften som skapats av korrigering orchestration appen spårar förloppet för uppdatering för varje nod.

## <a name="prerequisites"></a>Förutsättningar

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Se till att ditt virtuella Azure-datorer kör Ubuntu 16.04
Vid tidpunkten för med det här dokumentet, Ubuntu 16.04 (`Xenial Xerus`) är den enda versionen som stöds.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-61x-and-above"></a>Kontrollera att service fabric linux-kluster har version 6.1.x och senare

Korrigering av orchestration app linux använder vissa funktioner i runtime som endast är tillgängliga i service fabric runtime-versionen 6.1.x och högre.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera tjänsten reparera manager (om det inte redan körs)

Korrigering orchestration appen kräver tjänsten reparera manager system måste vara aktiverade på klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure linux-kluster i silver och guld hållbarhetsnivån har tjänsten reparera manager aktiverad som standard. Azure-kluster i Brons hållbarhetsnivån som standard har inte reparera manager-tjänsten aktiverad. Om den redan är aktiverad, visas den i avsnittet system tjänster i Service Fabric Explorer.

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

Fristående Service Fabric Linux-kluster stöds inte vid tidpunkten för med det här dokumentet.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Inaktivera automatisk uppdatering för Operativsystemet på alla noder

Automatiska uppdateringar av OS kan leda till förlust av tillgänglighet och eller ändrar i beteendet för program som körs. Appen korrigering orchestration försöker som standard inaktivera automatisk uppdatering av OS på varje nod i klustret för att förhindra sådana scenarier.
För Ubuntu [obevakad uppgraderingar](https://help.ubuntu.com/community/AutomaticSecurityUpdates) har inaktiverats av korrigering orchestration app.

## <a name="download-the-app-package"></a>Hämta app-paket

Hämta programmet från den [Hämta länk](https://go.microsoft.com/fwlink/?linkid=867984).

## <a name="configure-the-app"></a>Konfigurera appen

Korrigering av orchestration appens beteende kan konfigureras för att uppfylla dina behov. Åsidosätta standardvärdena genom att passera i parametern program under skapa program eller uppdatering. Parametrar för program kan anges genom att ange `ApplicationParameter` till den `Start-ServiceFabricApplicationUpgrade` eller `New-ServiceFabricApplication` cmdlets.

|**Parameter**        |**Typ**                          | **Detaljer**|
|:-|-|-|
|MaxResultsToCache    |Lång                              | Maximalt antal Update resultat som ska cachelagras. <br>Standardvärdet är 3000 under förutsättning att den: <br> -Antalet noder är 20. <br> -Antalet uppdateringar som händer på en nod per månad är fem. <br> -Antalet resultat per åtgärd kan vara 10. <br> -Resultat för de senaste tre månaderna ska lagras. |
|TaskApprovalPolicy   |Enum <br> {NodeWise, UpgradeDomainWise}                          |TaskApprovalPolicy anger den princip som ska användas av Coordinator-tjänsten för att installera uppdateringar för Service Fabric-klusternoder.<br>                         Tillåtna värden är: <br>                                                           <b>NodeWise</b>. Uppdateringarna är installerade en nod i taget. <br>                                                           <b>UpgradeDomainWise</b>. Uppdateringarna är installerade en domän i taget. (Max, alla noder som tillhör en domän kan gå för uppdateringen.)
| UpdateOperationTimeOutInMinutes | Int <br>(Standard: 180)                   | Anger timeout för en uppdateringsåtgärd (ladda ned eller installera). Om åtgärden inte slutförs inom den angivna tidsgränsen, avbryts.       |
| RescheduleCount      | Int <br> (Standard: 5).                  | Det maximala antalet gånger som tjänsten schemaläggs automatiskt på nytt OS uppdatera om en åtgärd misslyckas så.          |
| RescheduleTimeInMinutes  | Int <br>(Standard: 30). | Tidsintervall service planerar OS uppdatera om felet kvarstår. |
| UpdateFrequency           | Kommaavgränsad sträng (standard: ”vecka, Onsdag 7:00:00”)     | Frekvensen för att installera uppdateringar av operativsystem på klustret. Format och möjliga värden är: <br>-Varje månad, DD,: mm: ss, till exempel varje månad, 5, 12:22:32. <br> -Varje vecka, dag,: mm: ss, för exempelvis varje vecka, tisdag, 12:22:32.  <br> -Varje dag,: mm: ss, till exempel dagligen, 12:22:32.  <br> -Ingen anger att update bör göras.  <br><br> Alla tider är i UTC.|
| UpdateClassification | Kommaavgränsad sträng (standard: ”securityupdates”) | Typ av uppdateringar som ska installeras på klusternoderna. Giltiga värden är securityupdates, alla. <br> -securityupdates - installeras endast säkerhetsuppdateringar <br> -alla - skulle installera alla tillgängliga uppdateringar från lgh.|
| ApprovedPatches | Kommaavgränsad sträng (standard ”:”) | Detta är listan över godkända uppdateringar som ska installeras på klusternoder. Kommaavgränsad lista innehåller godkända paket och eventuellt önskade målversionen.<br> till exempel ”: lgh-verktyg för webbplatsuppgradering = 1.2.10ubuntu1, python3 jwt, lgh transport https < 1.2.194, libsystemd0 > = 229 4ubuntu16” <br> Ovanstående installeras <br> -lgh-verktyg för webbplatsuppgradering med version 1.2.10ubuntu1 om den är tillgänglig i lgh cache. Om att viss version inte är tillgänglig, är det en no-op. <br> -python3 jwt uppgraderingar till senast tillgängliga versionen. Om paketet inte är tillgänglig är det en no-op. <br> -lgh transport https uppgraderingar till högsta version som är mindre än 1.2.194. Om den här versionen inte finns, är det en no-op. <br> -libsystemd0 uppgraderingar till högsta version som är större än lika med 229 4ubuntu16. Om en sådan version inte finns, är det en no-op.|
| RejectedPatches | Kommaavgränsad sträng (standard ”:”) | Detta är listan över uppdateringar som inte ska installeras på klusternoder <br> till exempel: ”bash, sudo” <br> Den föregående filtrerar ut bash sudo tar emot uppdateringar. |


> [!TIP]
> Om du vill att OS-uppdatering sker omedelbart `UpdateFrequency` i förhållande till tidpunkten för distribution av programmet. Anta att du har ett testkluster med fem noder och planerar att distribuera appen vid cirka 17:00:00 UTC. Om du anta att programmet uppgraderingen eller distributionen tar 30 minuter Max, anger du UpdateFrequency som ”varje dag, 17:30:00”.

## <a name="deploy-the-app"></a>Distribuera appen

1. Förbereda klustret genom att slutföra alla nödvändiga steg.
2. Distribuera appen korrigering orchestration som andra Service Fabric-app. Du kan distribuera appen med hjälp av PowerShell eller Azure Service Fabric CLI. Följ stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) eller [distribuera program med hjälp av Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. För att konfigurera programmet vid tidpunkten för distribution, skickar den `ApplicationParamater` till den `New-ServiceFabricApplication` cmdlet eller skript. För din bekvämlighet har powershell (Deploy.ps1) och bash (Deploy.sh)-skript angetts tillsammans med programmet. Du använder skriptet:

    - Ansluta till ett Service Fabric-kluster.
    - Kör skriptet distribuera. Du kan också skicka parametern programmet till skriptet. till exempel:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = ”varje dag 11:00:00”} eller./Deploy.sh ”{\"UpdateFrequency\":\"dagligen, 11:00:00\"}” 

> [!NOTE]
> Spara skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="upgrade-the-app"></a>Uppgradera appen

Om du vill uppgradera en befintlig korrigering orchestration app, följer du stegen i [uppgradering av Service Fabric-programmet med PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) eller [uppgradering av Service Fabric-programmet med hjälp av Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Ta bort appen

Om du vill ta bort programmet, följer du stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) eller [ta bort ett program med Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

För din bekvämlighet har powershell (Undeploy.ps1) och bash (Undeploy.sh)-skript angetts tillsammans med programmet. Du använder skriptet:

  - Ansluta till ett Service Fabric-kluster.
  - Kör skriptet Undeploy.ps1 eller Undeploy.sh

> [!NOTE]
> Spara skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="view-the-update-results"></a>Visa resultaten för uppdatering

Korrigering orchestration appen exponerar REST API: er för att visa historiska resultat för användaren. Följande är ett exempel resultat: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
```json
[ 
  { 
    "NodeName": "_bronze_0", 
    "UpdateOperationResults": [ 
      { 
        "OperationResult": "succeeded", 
        "NodeName": "_bronze_0", 
        "OperationTime": "2017-11-21T12:39:29.0435917Z", 
        "UpdateDetails": [ 
          { 
            "UpdateId": "linux-cloud-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-headers-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-image-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "linux-tools-azure:amd64=4.11.0.1015.15", 
            "ResultCode": "succeeded" 
          }, 
          { 
            "UpdateId": "python3-apport:amd64=2.20.1-0ubuntu2.13", 
            "ResultCode": "succeeded" 
          }, 
        ], 
        "OperationType": "installation", 
        "UpdateClassification": "securityupdates", 
        "UpdateFrequency": "Daily, 7:00:00", 
        "RebootRequired": true, 
        "ApprovedList": "", 
        "RejectedList": "" 
      } 
    ] 
  } 
] 
```

Fält av JSON beskrivs enligt följande:

Fält | Värden | Information
-- | -- | --
OperationResult | 0 - lyckades<br> 1 - slutförd med fel<br> 2 - misslyckades<br> 3 - avbröts<br> 4 - avbröts med tidsgräns | Visar resultatet av övergripande (vanligtvis som innefattar installationen av en eller flera uppdateringar).
ResultCode | Samma som OperationResult | Fältet visar resultatet av installationen för en enskild uppdatering.
OperationType | 1 - installation<br> 0 - sökning och hämtning.| Installationen är den enda OperationType som visas i resultaten som standard.
UpdateClassification | Standardvärdet är ”securityupdates” | Typ av uppdateringar som installeras under uppdateringen
UpdateFrequency | Standardvärdet är ”vecka, Onsdag 7:00:00” | Uppdatera den konfigurerade synkroniseringsfrekvensen för den här uppdateringen.
RebootRequired | True - krävdes omstart<br> FALSE - omstart behövs inte | Anger en omstart krävdes för att slutföra installationen av uppdateringar.
ApprovedList | Standardvärdet är ”” | Lista över godkända korrigeringsfiler för den här uppdateringen
RejectedList | Standardvärdet är ”” | Listan över nekade korrigeringsfiler för den här uppdateringen

Om ingen uppdatering har schemalagts ännu är resultatet JSON tom.

Logga in till klustret för att uppdatera frågeresultat. Sedan reda på replik-adressen för primär av Coordinator-tjänsten och tryck URL i webbläsaren: http://&lt;REPLIK-IP-&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

REST-slutpunkt för Coordinator-tjänsten har en dynamisk port. Referera till Service Fabric Explorer för att kontrollera en exakt Webbadress. Till exempel resultaten finns på `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Bild av REST-slutpunkt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnostik/hälsotillstånd händelser

### <a name="diagnostic-logs"></a>Diagnostikloggar

Korrigering av orchestration app loggarna har samlats in som en del av Service Fabric runtime loggar.

Om du vill samla in loggar via diagnostiska verktyg/pipeline önskat. Korrigering orchestration program använder följande fasta providern ID händelser via [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälsorapporter

Korrigering orchestration appen publiceras även hälsorapporter mot Coordinator-tjänsten eller nod Agent-tjänsten i följande fall:

#### <a name="an-update-operation-failed"></a>En uppdatering misslyckades

Om en uppdatering misslyckas på en nod, skapas en hälsorapport mot nod Agent-tjänsten. Information om hälsorapporten innehålla problematiska nodnamn.

När uppdatering har slutförts på noden problematiskt, avmarkeras automatiskt i rapporten.

#### <a name="the-node-agent-daemon-service-is-down"></a>Daemon för noden agenttjänsten är nere

Om noden Agent Daemon-tjänsten har stoppats på en nod, skapas en varningsnivå hälsorapport mot nod Agent-tjänsten.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Reparera manager-tjänsten är inte aktiverad

En varningsnivå hälsorapport genereras för tjänsten Coordinator om reparationen manager-tjänsten inte kan hittas på klustret.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

FRÅGOR. **Varför ser jag min kluster i ett feltillstånd när korrigering orchestration appen körs?**

A. Under installationen av appen korrigering orchestration inaktiverar eller startar om noder. Den här åtgärden kan tillfälligt resultera i hälsotillståndet för klustret gå.

Baserat på principen för programmet, antingen en nod kan gå under en uppdatering åtgärd *eller* en hel uppgraderingsdomän kan gå samtidigt.

I slutet av installationen noderna reenabled efter omstart.

I följande exempel klustret ledde till ett feltillstånd tillfälligt eftersom två noder har ned och MaxPercentageUnhealthyNodes principen har överskridits. Felet är tillfällig tills åtgärden uppdatering pågår.

![Bild av feltillstånd kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Om problemet kvarstår finns i avsnittet Felsökning.

FRÅGOR. **Korrigering av orchestration appar är i varningstillstånd**

A. Kontrollera om en hälsorapport bokförd i programmet är den grundläggande orsaken. Varningen innehåller vanligtvis information om problemet. Om problemet är tillfälligt, förväntas programmet återskapa automatiskt från det här läget.

FRÅGOR. **Vad gör jag om min klustret är i feltillstånd och måste du göra en uppdatering för brådskande operativsystem?**

A. Korrigering orchestration appen installeras inte uppdateringar när klustret är i feltillstånd. Om du vill avblockera korrigering orchestration app arbetsflödet ta med ditt kluster till ett felfritt tillstånd.

FRÅGOR. **Varför korrigering genom datorkluster tar så lång tid för att köra?**

A. Den tid som krävs av korrigering orchestration appen beror oftast på följande faktorer:

- Principen för Coordinator-tjänsten. 
  - Standardprincipen, `NodeWise`, resulterar i korrigering endast en nod i taget. Särskilt om det finns ett större kluster, rekommenderar vi att du använder den `UpgradeDomainWise` att uppnå snabbare korrigering över kluster.
- Antalet uppdateringar som är tillgängliga för hämtning och installation. 
- Genomsnittlig tid det behövs för att ladda ned och installera en uppdatering som får inte överstiga några timmar.
- Prestanda för den virtuella dator och nätverket bandbredden.

FRÅGOR. **Hur har korrigering orchestration app bestämmer vilka uppdateringar finns säkerhetsuppdateringar.**

A. Korrigering av orchestration appen använder distro-specifik logik för att fastställa vilka säkerhetsuppdateringar bland de tillgängliga uppdateringarna. Exempel: I appen söker efter uppdateringar från Arkiv $RELEASE ubuntu-säkerhet, $RELEASE-uppdateringar ($RELEASE = xenial eller linux standard grundläggande versionen). 

 
FRÅGOR. **Hur kan låsa in på en viss version av paketet?**

A. Använd ApprovedPatches inställningar för att låsa dina paket för en viss version. 


FRÅGOR. **Vad händer med automatiska uppdateringar aktiverad i Ubuntu?**

A. När du har installerat korrigering orchestration-appen på ditt kluster skulle obevakad uppgraderingar på en nod i klustret inaktiveras. Periodisk uppdatering arbetsflödet skulle styrs av korrigering orchestration app.
Om du vill att konsekvenskontroll av miljö över kluster rekommenderar vi installerar uppdateringarna via korrigering orchestration app. 
 
FRÅGOR. **Efter uppgraderingen korrigering orchestration app vill rensa oanvända paket?**

A. Ja, rensa händer som en del av stegen efter installationen. 

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

Korrigering orchestration-appen försöker installera en uppdatering enligt omplanering principen. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt principen för program.

I sådana fall genereras en varning nivå hälsorapport mot nod Agent-tjänsten. Resultatet för uppdateringen innehåller också möjliga orsaker till felet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Hälsotillståndet för klustret går till när uppdateringen har installerats

En felaktig uppdatering kan sätta ned hälsotillståndet för ett program eller ett kluster på en viss nod eller en domän. Korrigering orchestration app upphör alla efterföljande uppdateringsåtgärder tills klustret är felfri igen.

En administratör måste ingripa och fastställa varför programmet eller kluster blev inte hälsosamt eftersom en uppdatering.

## <a name="disclaimer"></a>Ansvarsfriskrivning

Korrigering orchestration appen samlar in telemetri om du vill spåra användning och prestanda. Programmets telemetri följer inställningen för Service Fabric-körningsmiljön telemetri inställningen (som är aktiverad som standard).

## <a name="release-notes"></a>Viktig information

### <a name="version-010"></a>Version 0.1.0
- Privata förhandsversionen

### <a name="version-200-latest"></a>Version 2.0.0 (Latest)
- Offentliga utgåvan
