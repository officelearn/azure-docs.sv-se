---
title: Azure Service Fabric patch orchestration-program för linux | Microsoft Docs
description: Programmet att automatisera uppdatering av operativsystemet på ett Linux Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: novino
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 5/22/2018
ms.author: nachandr
ms.openlocfilehash: 537450dbc386a94fa5c2e0d9334435dce041a32f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59266145"
---
# <a name="patch-the-linux-operating-system-in-your-service-fabric-cluster"></a>Uppdatera operativsystemet Linux i Service Fabric-klustret

> [!div class="op_single_selector"]
> * [Windows](service-fabric-patch-orchestration-application.md)
> * [Linux](service-fabric-patch-orchestration-application-linux.md)
>
>

Patch orchestration application är ett Azure Service Fabric-program som automatiserar operativsystemet uppdatering i Service Fabric-kluster utan avbrott.

Patch orchestration appen tillhandahåller följande funktioner:

- **Automatisk uppdatering operativsysteminstallationen**. Operativsystemuppdateringar hämtas och installeras automatiskt. Klusternoder startas om vid behov utan klusterdriftstopp.

- **Klustermedveten uppdatering och hälsotillstånd integration**. När uppdateringar, övervakar patch orchestration appen hälsotillståndet för noderna i klustret. Klusternoder är uppgraderade en nod i taget eller en domän i taget. Om hälsotillståndet för klustret slutar att fungera på grund av korrigeringsprocessen stoppas korrigeringar för att förhindra aggravating problemet.

## <a name="internal-details-of-the-app"></a>Intern information om appen

Appen patch orchestration består av följande delkomponenter:

- **Tjänsten Coordinator**: Den här tillståndskänsliga tjänsten ansvarar för att:
    - Samordna OS Uppdateringsjobbet på hela klustret.
    - Lagra resultatet av slutförda åtgärder för uppdatering av operativsystem.
- **Nod-agenttjänsten**: Den tillståndslösa tjänsten körs på alla noder i Service Fabric. Tjänsten är ansvarig för:
    - Startprogram för Nodagentens daemon på Linux.
    - Övervaka tjänsten daemon.
- **Daemon för nod-agenten**: Den här Linux-daemon-tjänsten körs på en högre nivå behörighet (rot). Däremot körs Node Agent-tjänsten och Coordinator-tjänsten vid privilegium på lägre nivå. Tjänsten ansvarar för att utföra följande uppdateringsjobb på alla noder i klustret:
    - Inaktivera automatisk uppdatering av operativsystem på noden.
    - Hämta och installera uppdatering av operativsystem enligt principen har användaren har angett.
    - Startar om datorn efter installationen av OS-uppdateringen om det behövs.
    - Ladda upp resultatet av OS-uppdateringar till tjänsten Coordinator.
    - Reporting systemhälsorapporter om en åtgärd misslyckades efter få slut på alla nya försök.

> [!NOTE]
> Patch orchestration appen använder tjänsten Service Fabric reparera manager system att inaktivera eller aktivera noden och utföra hälsokontroller. Reparationsuppgiften som skapats av appen patch orchestration förloppet på uppdatering för varje nod.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="ensure-that-your-azure-vms-are-running-ubuntu-1604"></a>Se till att dina virtuella Azure-datorer kör Ubuntu 16.04
Vid tidpunkten för att skriva det här dokumentet, Ubuntu 16.04 (`Xenial Xerus`) är den enda versionen som stöds.

### <a name="ensure-that-the-service-fabric-linux-cluster-is-version-62x-and-above"></a>Se till att service fabric linux-kluster är version 6.2.x och senare

Patch orchestration app linux använder vissa funktioner i runtime som endast är tillgängliga i service fabric runtime-versionen 6.2.x och senare.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera reparation manager-tjänsten (om den inte körs redan)

Patch orchestration appen kräver reparera manager system-tjänsten ska vara aktiverat på klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure linux-kluster i silver och gold hållbarhetsnivå har tjänsten reparera manager aktiverat som standard. Azure-kluster i Brons hållbarhetsnivån, som standard har inte reparera manager-tjänsten aktiveras. Om tjänsten redan är aktiverad, visas den i avsnittet system tjänster i Service Fabric Explorer.

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

Fristående Service Fabric Linux-kluster stöds inte vid tidpunkten för att skriva det här dokumentet.

### <a name="disable-automatic-os-update-on-all-nodes"></a>Inaktivera automatisk uppdatering av operativsystem på alla noder

Automatiska OS-uppdateringar kan leda till förlust av tillgänglighet och eller ändras i beteendet för program som körs. Patch orchestration appen, försöker som standard inaktivera automatisk uppdatering av operativsystem på varje klusternod för att förhindra sådana scenarier.
För Ubuntu [obevakad uppgraderingar](https://help.ubuntu.com/community/AutomaticSecurityUpdates) är inaktiverade som patch orchestration-app.

## <a name="download-the-app-package"></a>Hämta app-paket

Programmet tillsammans med installationsskript kan laddas ned från [Arkiv länk](https://go.microsoft.com/fwlink/?linkid=867984).

Program i sfpkg format kan laddas ned från [sfpkg länk](https://aka.ms/POA/POA_v2.0.3.sfpkg). Detta är praktiskt för [Azure Resource Manager-baserade programdistribution](service-fabric-application-arm-resource.md).

## <a name="configure-the-app"></a>Konfigurera appen

Patch orchestration appens beteende kan konfigureras för att uppfylla dina behov. Åsidosätta standardvärdena genom att skicka in parametern program under programmet skapas eller uppdatera. Programparametrar kan anges genom att ange `ApplicationParameter` till den `Start-ServiceFabricApplicationUpgrade` eller `New-ServiceFabricApplication` cmdletar.

|**Parametern**        |**Typ**                          | **Detaljer**|
|:-|-|-|
|MaxResultsToCache    |Lång                              | Maximalt antal resultat för uppdatering, som ska cachelagras. <br>Standardvärdet är 3000 förutsatt att den: <br> -Antalet noder är 20. <br> -Antalet uppdateringar som sker på en nod per månad är fem. <br> – Antal resultat per åtgärd kan vara 10. <br> -Resultat för de senaste tre månaderna ska lagras. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy anger den princip som ska användas av Coordinator-tjänsten för att installera uppdateringar för Service Fabric-klusternoder.<br>                         Tillåtna värden är: <br>                                                           <b>NodeWise</b>. Uppdateringarna är installerade en nod i taget. <br>                                                           <b>UpgradeDomainWise</b>. Uppdateringarna är installerade en uppgraderingsdomän i taget. (På högsta alla noder som tillhör en uppgraderingsdomän kan gå för uppdatering.)
| UpdateOperationTimeOutInMinutes | Int <br>(Standard: 180)                   | Anger tidsgränsen för uppdateringen åtgärder (ladda ned eller installera). Om åtgärden inte har slutförts inom den angivna tidsgränsen, avbryts.       |
| RescheduleCount      | Int <br> (Standard: 5)                  | Om en åtgärd misslyckas så att uppdatera det maximala antalet gånger som tjänsten schemalägger om Operativsystemet.          |
| RescheduleTimeInMinutes  | Int <br>(Standard: 30) | Intervallet då tjänsten schemalägger om Operativsystemet uppdatera om felet kvarstår. |
| UpdateFrequency           | Kommaseparerad sträng (standard: ”Vecka, Onsdag 7:00:00”)     | Frekvensen för att installera uppdateringar av Operativsystemet på klustret. Format och möjliga värden är: <br>-Månads-, DD,: mm: ss, till exempel varje månad, 5, 12:22:32. <br> -Varje vecka, dag,: mm: ss, för exempelvis varje vecka, tisdag, 12:22:32.  <br> -Varje dag,: mm: ss, till exempel varje dag, 12:22:32.  <br> -Ingen indikerar att update inte bör göras.  <br><br> Hela tiden är i UTC.|
| UpdateClassification | Kommaseparerad sträng (standard: ”securityupdates”) | Typ av uppdateringar som ska installeras på noderna i klustret. Godkända värden är securityupdates, alla. <br> -securityupdates - installeras endast säkerhetsuppdateringar <br> -alla - skulle installera alla tillgängliga uppdateringar från apt.|
| ApprovedPatches | Kommaseparerad sträng (standard ”:”) | Det här är listan över godkända uppdateringar som ska installeras på klusternoderna. Kommaavgränsad lista innehåller godkända paket och du kan också önskat målversion.<br> till exempel ”: apt utils = 1.2.10ubuntu1, python3 jwt, apt transport https < 1.2.194, libsystemd0 > = 229 4ubuntu16” <br> Ovanstående installeras <br> -apt utils med version 1.2.10ubuntu1 om den är tillgänglig i apt-cache. Om den här specifika versionen är inte tillgänglig, är det en icke-alternativ. <br> -python3 jwt-uppgraderingar till senast tillgängliga versionen. Om paketet inte är tillgänglig, är det en icke-alternativ. <br> -apt-transport-https-uppgraderingar på högsta version som är mindre än 1.2.194. Om den här versionen inte är tillgänglig, är det en icke-alternativ. <br> -libsystemd0 uppgraderingar till högsta version som är större än lika med 229 4ubuntu16. Om en sådan version inte finns, är det en icke-alternativ.|
| RejectedPatches | Kommaseparerad sträng (standard ”:”) | Det här är listan över uppdateringar som inte ska installeras på noderna i klustret <br> till exempel: ”bash, sudo” <br> Föregående filtrerar ut bash, sudo från att ta emot uppdateringar. |


> [!TIP]
> Om du vill OS-uppdateringen sker omedelbart anger `UpdateFrequency` i förhållande till tidpunkten för distribution av programmet. Anta att du har ett testkluster med fem noder och planerar att distribuera appen cirka 17:00:00 UTC. Om du anta att uppgradera programmet eller distributionen tar 30 minuter på högsta, anger du UpdateFrequency som ”varje dag, 17:30:00”.

## <a name="deploy-the-app"></a>Distribuera appen

1. Förbered klustret genom att slutföra alla nödvändiga steg.
2. Distribuera patch orchestration-appen som med andra Service Fabric-app. Du kan distribuera appen med hjälp av PowerShell eller Azure Service Fabric CLI. Följ stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) eller [distribuera program med Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/scripts/cli-deploy-application)
3. För att konfigurera programmet vid tidpunkten för distribution, skickar den `ApplicationParameter` till den `New-ServiceFabricApplication` cmdlet eller skripten som tillhandahålls. Powershell (Deploy.ps1) och bash (Deploy.sh)-skript har angetts tillsammans med programmet för din bekvämlighet. Du använder skriptet:

    - Anslut till Service Fabric-kluster.
    - Kör skriptet Deploy. Du kan också skicka program-parametern till skriptet. till exempel:.\Deploy.ps1 - ApplicationParameter @{UpdateFrequency = ”varje dag, 11:00:00”} eller./Deploy.sh ”{\"UpdateFrequency\":\"dagligen, 11:00:00\"}” 

> [!NOTE]
> Behåll skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="upgrade-the-app"></a>Uppgradera appen

Om du vill uppgradera en befintlig patch orchestration app, följer du stegen i [uppgradering av Service Fabric-programmet med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell) eller [uppgradering av Service Fabric-programmet med hjälp av Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-upgrade)

## <a name="remove-the-app"></a>Ta bort appen

Om du vill ta bort programmet, följer du stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications) eller [ta bort ett program med hjälp av Azure Service Fabric CLI](https://docs.microsoft.com/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-delete)

Powershell (Undeploy.ps1) och bash (Undeploy.sh)-skript har angetts tillsammans med programmet för din bekvämlighet. Du använder skriptet:

  - Anslut till Service Fabric-kluster.
  - Kör skriptet Undeploy.ps1 eller Undeploy.sh

> [!NOTE]
> Behåll skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="view-the-update-results"></a>Visa resultat för uppdatering

Appen patch orchestration exponerar REST-API: er för att visa historiska resultat för användaren. Nedan följer ett exempel-resultat: ```testadm@bronze000001:~$ curl -X GET http://10.0.0.5:20002/PatchOrchestrationApplication/v1/GetResults```
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

Fält för JSON beskrivs på följande sätt:

Fält | Värden | Information
-- | -- | --
OperationResult | 0 – lyckades<br> 1 – slutfördes med fel<br> 2 – misslyckades<br> 3 – avbröts<br> 4 – avbröts med tidsgräns | Visar resultatet av övergripande åtgärden (vanligtvis som rör installation av en eller flera uppdateringar).
Resultatkod | Samma som OperationResult | Det här fältet visar resultatet av installationen igen för en enskild uppdatering.
OperationType | 1 – installation<br> 0 – Sök efter och ladda ned.| Installationen är den enda OperationType som visas i resultaten som standard.
UpdateClassification | Standardvärdet är ”securityupdates” | Typ av uppdateringar som installeras under uppdateringen
UpdateFrequency | Standardvärdet är ”varje vecka, Onsdag 7:00:00” | Uppdatera frekvens som konfigurerats för den här uppdateringen.
RebootRequired | True - krävdes omstart<br> FALSE - omstart behövs inte | Anger en omstart krävdes för att slutföra installationen av uppdateringar.
ApprovedList | Standardvärdet är ”” | Lista över godkända korrigeringar för den här uppdateringen
RejectedList | Standardvärdet är ”” | Listan över nekade korrigeringar för den här uppdateringen

Om ingen uppdatering har schemalagts ännu är resultatet JSON tom.

Logga in på klustret för att uppdatera frågeresultat. Sedan ta reda på replik-adressen för primärt av Coordinator-tjänsten och tryck på URL: en i webbläsaren: http://&lt;REPLIKEN IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/GetResults .

REST-slutpunkten för tjänsten Coordinator har en dynamisk port. Du kan kontrollera den exakta Webbadressen genom att referera till Service Fabric Explorer. Till exempel resultaten är tillgängliga på `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetResults`.

![Bild av REST-slutpunkt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)

## <a name="diagnosticshealth-events"></a>Diagnostik/health-händelser

### <a name="diagnostic-logs"></a>Diagnostikloggar

Patch orchestration-appen loggar samlas in som en del av loggar för Service Fabric runtime.

Om du vill samla in loggar via diagnostiska verktyg/pipeline valfri. Patch orchestration application använder följande fasta provider-ID: N för att logga händelser via [eventsource](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netstandard-2.0)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälsorapporter

Patch orchestration appen publiceras även hälsorapporter mot tjänsten Coordinator eller nod-agenttjänsten i följande fall:

#### <a name="an-update-operation-failed"></a>En uppdateringsåtgärd misslyckades

Om en uppdateringsåtgärd misslyckas på en nod, skapas en hälsorapport mot Node Agent-tjänsten. Information om hälsorapporten innehålla problematiska nodnamnet.

När uppdateringar har slutförts på noden problematiska, rensas automatiskt rapporten.

#### <a name="the-node-agent-daemon-service-is-down"></a>Noden agenten Daemon-tjänsten har stoppats

Om noden agenten Daemon-tjänsten är igång på en nod kan genereras en varning på servernivå hälsorapport mot Node Agent-tjänsten.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Reparera manager-tjänsten är inte aktiverad

En varning på servernivå hälsorapport genereras för tjänsten Coordinator om reparationen manager-tjänsten inte kan hittas på klustret.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. **Varför ser jag mitt kluster i ett feltillstånd när patch orchestration-appen körs?**

A. Under installationen, patch orchestration appen inaktiverar eller startar om noder. Den här åtgärden kan tillfälligt resultera i hälsotillståndet för klustret slutar fungera.

Baserat på principen för programmet, antingen en nod kan gå under en uppdatering *eller* en hel uppgraderingsdomän kan gå samtidigt.

I slutet av installationen, noderna reenabled efter omstart.

I följande exempel klustret gick in i ett feltillstånd tillfälligt eftersom två noder har nedåt och MaxPercentageUnhealthyNodes principen har överskridits. Felet är tillfälligt tills uppdatering åtgärd pågår.

![Bild av felaktiga kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Om problemet kvarstår, finns i avsnittet Felsökning.

F. **Patch orchestration-appen är i varningstillstånd**

A. Kontrollera om en hälsorapport som publicerats i programmet är den grundläggande orsaken. Varningen innehåller vanligtvis information om problemet. Om problemet är tillfälligt, förväntas programmet återskapa automatiskt från det här läget.

F. **Vad kan jag göra om mitt kluster är i feltillstånd och jag behöver du en brådskande operativsystemuppdateringen?**

A. Patch orchestration appen installeras inte uppdateringar medan klustret är i feltillstånd. För att låsa upp den patch orchestration arbetsflöden, sätta ditt kluster till ett felfritt tillstånd.

F. **Varför korrigeringar mellan datorkluster tar så lång tid för att köra?**

A. Den tid som krävs av patch orchestration appen beror huvudsakligen på följande faktorer:

- Princip för Coordinator-tjänsten. 
  - Standardprincipen, `NodeWise`, resulterar i korrigeringar bara en nod i taget. Särskilt om det finns ett större kluster, rekommenderar vi att du använder den `UpgradeDomainWise` att uppnå snabbare uppdatering i klustret.
- Antalet uppdateringar som är tillgängliga för hämtning och installation. 
- Den genomsnittliga tiden som behövs för att ladda ned och installera en uppdatering, vilket får innehålla högst ett par timmar.
- Prestanda för den virtuella dator och nätverket bandbredden.

F. **Hur gör patch orchestration app avgör vilka uppdateringar finns säkerhetsuppdateringar.**

A. Patch orchestration appen använder distribution-specifik logik för att bestämma vilka uppdateringar bland de tillgängliga uppdateringarna som säkerhetsuppdateringar. Exempel: I ubuntu appen söker efter uppdateringar från Arkiv $RELEASE-säkerhet, $RELEASE-uppdateringar ($RELEASE = xenial eller linux standard grundläggande slutversionen). 

 
F. **Hur kan jag för att låsa in på en specifik version av paketet?**

A. Använd ApprovedPatches-inställningar för att låsa dina paket för en viss version. 


F. **Vad händer med automatiska uppdateringar som är aktiverad i Ubuntu?**

A. När du installerar patch orchestration-app på ditt kluster, skulle obevakad uppgraderingar på din klusternod inaktiveras. Periodisk uppdatering arbetsflödet skulle styrs patch orchestration-app.
För att få konsekvens miljö i klustret ska rekommenderar vi att du installerar uppdateringar via patch orchestration-enbart app. 
 
F. **Efter uppgraderingen patch orchestration appen gör rensningen av oanvända paket?**

A. Ja, rensa inträffar som en del av anvisningarna efter installation. 

F. **Dirigering app användas för att korrigera mitt dev-kluster (kluster med en nod)?**

A. Nej, Patch orchestration appen kan inte användas till patch kluster med en nod. Den här begränsningen är avsiktligt, som [service fabric-systemtjänster](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) eller kundappar kommer att stöta på avbrott och reparation jobb för uppdatering skulle därför aldrig godkännas av reparationshanteraren.

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

Patch orchestration appen försöker installera en uppdatering enligt omplanering principen. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt princip för program.

I detta fall är genereras en varning på servernivå hälsorapport mot Node Agent-tjänsten. Resultatet för uppdateringen innehåller också möjlig orsak till felet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Hälsotillståndet för klustret som leder till fel när uppdateringen har installerats

En felaktig uppdatering kan påverkar hälsotillståndet för ett program eller -kluster i en viss nod eller en uppgraderingsdomän. Patch orchestration-appen upphör alla efterföljande uppdateringsåtgärder tills att klustret är felfritt igen.

En administratör måste ingripa och avgöra varför programmet eller kluster fick dåligt hälsotillstånd på grund av en uppdatering.

## <a name="disclaimer"></a>Ansvarsfriskrivning

Patch orchestration appen samlar in telemetri för att spåra användning och prestanda. Programmets telemetri följer inställningen för Service Fabric-körningen telemetri inställningen (som är aktiverad som standard).

## <a name="release-notes"></a>Viktig information

### <a name="version-010"></a>Version 0.1.0
- Privata förhandsversionen

### <a name="version-200"></a>Version 2.0.0
- Publiceringen

### <a name="version-201"></a>Version 2.0.1
- Kompileras appen med senaste Service Fabric SDK

### <a name="version-202"></a>Version 2.0.2 
- Ett problem har åtgärdats med hälsotillstånd varning komma kvar under omstarten.

### <a name="version-203-latest"></a>Version 2.0.3 (senaste)
- Åtgärda problem där nått CPU-användningen för Nodagentens daemontjänst upp till 99% på Standard_D1_v2 VM: ar.
- Åtgärda de problem som berörs uppdatering liv-cyle på en nod om det finns noder med namn som är del av namnet på aktuella noden. För sådana noder, möjliga korrigeringar saknas eller omstart väntar.
- Ett fel på grund av som håller Nodagentens daemon kraschar när skadad inställningar skickas till tjänsten har åtgärdats.