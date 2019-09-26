---
title: Orchestration-program för Azure Service Fabric-uppdatering | Microsoft Docs
description: Program för att automatisera operativ Systems korrigering i ett Service Fabric-kluster.
services: service-fabric
documentationcenter: .net
author: khandelwalbrijeshiitr
manager: chackdan
editor: ''
ms.assetid: de7dacf5-4038-434a-a265-5d0de80a9b1d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/01/2019
ms.author: brkhande
ms.openlocfilehash: 2aa2dd8373a9568478a02691ca5e6a43e80cd408
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2019
ms.locfileid: "71289413"
---
# <a name="patch-the-windows-operating-system-in-your-service-fabric-cluster"></a>Korrigera operativ systemet Windows i Service Fabric-klustret

> 
> [!IMPORTANT]
> Program version 1,2. * upphör att fungera den 30 april 2019. Uppgradera till den senaste versionen.


[Skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) är den bästa metoden för att hålla operativ systemen som korrigeras i Azure och POA (patch Orchestration Application) är en omslutning runt system tjänsten för Service Fabric-RepairManager. aktiverar konfiguration baserad operativ system korrigerings schemaläggning för icke-Azure-värdbaserade kluster. POA krävs inte för icke-Azure-värdbaserade kluster, men planerings korrigerings installation av uppgraderings domäner krävs för att korrigera Service Fabric kluster värdar utan drift avbrott.

POA är ett Azure Service Fabric-program som automatiserar operativ Systems korrigeringar på ett Service Fabric kluster utan drift avbrott.

Appen för uppdaterings dirigering innehåller följande funktioner:

- **Automatisk installation av operativ system uppdateringar**. Operativ system uppdateringar laddas ned och installeras automatiskt. Klusternoder startas om efter behov utan avbrott i klustret.

- **Kluster medveten korrigering och hälso integrering**. När du använder uppdateringar övervakar appen för uppdaterings dirigering hälso tillståndet för klusternoderna. Klusternoder uppgraderas till en nod i taget eller en uppgraderings domän i taget. Om hälso tillståndet för klustret slutar fungera på grund av korrigerings processen, stoppas korrigeringen för att förhindra att problemet förvärras.

## <a name="internal-details-of-the-app"></a>Intern information om appen

Appen för uppdaterings dirigering består av följande del komponenter:

- **Koordinator tjänst**: Den här tillstånds känsliga tjänsten ansvarar för:
    - Koordinera Windows Update-jobbet på hela klustret.
    - Lagrings resultatet av slutförda Windows Update åtgärder.
- **Node agent-tjänst**: Den här tillstånds lösa tjänsten körs på alla Service Fabric klusternoder. Tjänsten ansvarar för:
    - Starta NTService för Node-agenten.
    - Övervaka NTService för Node-agenten.
- **NTService för Node-agent**: Den här Windows NT-tjänsten körs på en högre nivå (SYSTEM). Dessutom körs Node-agenttjänsten och koordinator tjänsten på en behörighet på lägre nivå (nätverks tjänsten). Tjänsten ansvarar för att utföra följande Windows Update jobb på alla klusternoder:
    - Inaktivera automatisk Windows Update på noden.
    - Hämta och installera Windows Update enligt principen som användaren har angett.
    - Datorn startas om efter Windows Update installationen.
    - Överför resultaten av Windows-uppdateringar till koordinator tjänsten.
    - Rapportera hälso rapporter om en åtgärd Miss lyckas efter att alla återförsök har uppnåtts.

> [!NOTE]
> Appen för uppdaterings dirigering använder Service Fabric Repair Manager system service för att inaktivera eller aktivera noden och utföra hälso kontroller. Den reparations uppgift som skapas av appen för uppdaterings dirigering spårar Windows Update förloppet för varje nod.

## <a name="prerequisites"></a>Förutsättningar

> [!NOTE]
> Den lägsta version av .NET Framework som krävs är 4,6.

### <a name="enable-the-repair-manager-service-if-its-not-running-already"></a>Aktivera tjänsten Repair Manager (om den inte redan körs)

Appen för uppdaterings dirigering kräver att Repair Manager system service är aktiverat på klustret.

#### <a name="azure-clusters"></a>Azure-kluster

Azure-kluster i silver hållbarhets nivån har tjänsten Repair Manager aktive rad som standard. Azure-kluster i guld hållbarhets nivån kan eller kanske inte har tjänsten Repair Manager aktive rad, beroende på när dessa kluster skapades. Azure-kluster på brons hållbarhets nivå har som standard inte tjänsten Repair Manager aktive rad. Om tjänsten redan är aktive rad kan du se att den körs i avsnittet system tjänster i Service Fabric Explorer.

##### <a name="azure-portal"></a>Azure Portal
Du kan aktivera reparations hanteraren från Azure Portal när klustret konfigureras. Välj alternativet **inkludera Repair Manager** under **tilläggs funktioner** vid tiden för kluster konfigurationen.
![Bild som aktiverar Repair Manager från Azure Portal](media/service-fabric-patch-orchestration-application/EnableRepairManager.png)

##### <a name="azure-resource-manager-deployment-model"></a>Azure Resource Manager distributions modell
Du kan också använda [Azure Resource Manager distributions modell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) för att aktivera Repair Manager-tjänsten på nya och befintliga Service Fabric-kluster. Hämta mallen för det kluster som du vill distribuera. Du kan antingen använda exempel mallarna eller skapa en anpassad mall för Azure Resource Manager distributions modell. 

Aktivera tjänsten Repair Manager med hjälp av [mallen för Azure Resource Manager distributions modell](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm):

1. Kontrol lera först att `apiversion` har angetts till `2017-07-01-preview` för `Microsoft.ServiceFabric/clusters` resursen. Om den är annorlunda måste du uppdatera `apiVersion` till värdet `2017-07-01-preview` eller högre:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Aktivera nu tjänsten Repair Manager genom att lägga till följande `addonFeatures` avsnitt `fabricSettings` efter avsnittet:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. När du har uppdaterat kluster mal len med de här ändringarna kan du tillämpa dem och låta uppgraderingen slutföras. Nu kan du se system tjänsten Repair Manager som körs i klustret. Den anropas `fabric:/System/RepairManagerService` i avsnittet system tjänster i Service Fabric Explorer. 

### <a name="standalone-on-premises-clusters"></a>Fristående lokala kluster

Du kan använda [konfigurations inställningarna för fristående Windows-kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest) för att aktivera tjänsten Repair Manager på nya och befintliga Service Fabric-kluster.

Så här aktiverar du tjänsten Repair Manager:

1. Kontrol lera först att `apiversion` de [allmänna klusterkonfigurationer](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-manifest#general-cluster-configurations) är inställda på `04-2017` eller högre:

    ```json
    {
        "name": "SampleCluster",
        "clusterConfigurationVersion": "1.0.0",
        "apiVersion": "04-2017",
        ...
    }
    ```

2. Aktivera nu tjänsten Repair Manager genom att lägga till `addonFeatures` följande avsnitt `fabricSettings` efter avsnittet som visas nedan:

    ```json
    "fabricSettings": [
        ...      
    ],
    "addonFeatures": [
        "RepairManager"
    ],
    ```

3. Uppdatera ditt kluster manifest med dessa ändringar med hjälp av det uppdaterade kluster manifestet [skapa ett nytt kluster](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-for-windows-server) eller [uppgradera kluster konfigurationen](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-upgrade-windows-server). När klustret körs med det uppdaterade kluster manifestet kan du nu se reparations hanterarens system tjänst som körs i klustret, vilket kallas `fabric:/System/RepairManagerService`under system tjänster i Service Fabric Explorer.

### <a name="configure-windows-updates-for-all-nodes"></a>Konfigurera Windows-uppdateringar för alla noder

Automatiska Windows-uppdateringar kan leda till tillgänglighets förlust eftersom flera klusternoder kan startas om samtidigt. Appen för uppdaterings dirigering försöker som standard inaktivera den automatiska Windows Update på varje klusternod. Men om inställningarna hanteras av en administratör eller grupprincip rekommenderar vi att du ställer in Windows Updates principen på "meddela innan nedladdning" explicit.

## <a name="download-the-app-package"></a>Ladda ned app-paketet

Om du vill hämta programpaketet går du till GitHub release- [sidan](https://github.com/microsoft/Service-Fabric-POA/releases/latest/) i programmet för uppdaterings dirigering.

## <a name="configure-the-app"></a>Konfigurera appen

Beteendet för appen för uppdaterings dirigering kan konfigureras så att det passar dina behov. Åsidosätt standardvärdena genom att skicka i program parametern när programmet skapas eller uppdateras. Du kan ange program parametrar genom `ApplicationParameter` `Start-ServiceFabricApplicationUpgrade` att ange till cmdletarna eller `New-ServiceFabricApplication` .

|**Parametern**        |**Typ**                          | **Detaljer**|
|:-|-|-|
|MaxResultsToCache    |Lång                              | Maximalt antal Windows Updates resultat som ska cachelagras. <br>Standardvärdet är 3000 som antar: <br> -Antalet noder är 20. <br> – Antalet uppdateringar som sker på en nod per månad är fem. <br> -Antalet resultat per åtgärd kan vara 10. <br> – Resultat för de senaste tre månaderna bör lagras. |
|TaskApprovalPolicy   |Enum <br> { NodeWise, UpgradeDomainWise }                          |TaskApprovalPolicy anger den princip som ska användas av koordinator tjänsten för att installera Windows-uppdateringar över Service Fabric klusternoder.<br>                         Tillåtna värden är: <br>                                                           <b>NodeWise</b>. Windows Update installeras en nod i taget. <br>                                                           <b>UpgradeDomainWise</b>. Windows Update installeras en uppgraderings domän i taget. (Maximalt kan alla noder som tillhör en uppgraderings domän gå Windows Update.)<br> Se avsnittet med [vanliga frågor och svar](#frequently-asked-questions) om hur du bestämmer vilken princip som passar bäst för klustret.
|LogsDiskQuotaInMB   |Lång  <br> Objekt 1024)               |Maximal storlek för loggning av korrigeringsfiler för att dirigera om i MB, som kan sparas lokalt på noder.
| WUQuery               | sträng<br>Objekt "IsInstalled = 0")                | Fråga för att hämta Windows-uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
| InstallWindowsOSOnlyUpdates | Boolesk <br> (standard: falskt)                 | Använd den här flaggan för att styra vilka uppdateringar som ska hämtas och installeras. Följande värden är tillåtna <br>True – installerar endast uppdateringar för Windows-operativsystem.<br>false-installerar alla tillgängliga uppdateringar på datorn.          |
| WUOperationTimeOutInMinutes | Int <br>Objekt 90)                   | Anger tids gränsen för alla Windows Update åtgärder (Sök eller ladda ned eller installera). Om åtgärden inte slutförs inom den angivna tids gränsen avbryts den.       |
| WURescheduleCount     | Int <br> Objekt 5)                  | Det maximala antalet gånger som tjänsten schemalägger om Windows Update om en åtgärd Miss lyckas beständigt.          |
| WURescheduleTimeInMinutes | Int <br>Objekt 30) | Intervallet då tjänsten omplanerar om Windows Update om det inte går att spara. |
| WUFrequency           | Kommaavgränsad sträng (standard: "Vecka, onsdag, 7:00:00")     | Frekvensen för att installera Windows Update. Formatet och möjliga värden är: <br>– Varje månad, DD, HH: MM: SS, till exempel månad, 5, 12:22:32.<br>Tillåtna värden för fältet DD (dag) är tal mellan intervallet 1-28 och "sista". <br> – Varje vecka, dag, HH: MM: SS, till exempel varje vecka, tisdag 12:22:32.  <br> – Varje dag, HH: MM: SS, till exempel dagligen, 12:22:32.  <br> -Ingen anger att Windows Update inte ska göras.  <br><br> Observera att tiderna är UTC.|
| AcceptWindowsUpdateEula | Boolesk <br>(Standard: sant) | Genom att ange den här flaggan godkänner programmet slut användar avtalet för Windows Update för datorns ägare.              |

> [!TIP]
> Om du vill att Windows Update ska ske omedelbart anger `WUFrequency` du i förhållande till programmets distributions tid. Anta till exempel att du har ett test kluster med fem noder och planerar att distribuera appen med cirka 5:00 PM UTC. Om du antar att program uppgraderingen eller distributionen tar 30 minuter maximalt, ställer du in WUFrequency som "dagligen, 17:30:00"

## <a name="deploy-the-app"></a>Distribuera appen

1. Slutför alla nödvändiga steg för att förbereda klustret.
2. Distribuera appen för uppdaterings dirigering som alla andra Service Fabric-appar. Du kan distribuera appen med hjälp av PowerShell. Följ stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).
3. Om du vill konfigurera programmet vid tidpunkten för distributionen skickar `ApplicationParameter` `New-ServiceFabricApplication` du till cmdleten. För din bekvämlighet har vi tillhandahållit skriptet Deploy. ps1 tillsammans med programmet. Använda skriptet:

    - Anslut till ett Service Fabric-kluster med `Connect-ServiceFabricCluster`hjälp av.
    - Kör PowerShell-skriptet Deploy. ps1 med lämpligt `ApplicationParameter` värde.

> [!NOTE]
> Behåll skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="upgrade-the-app"></a>Uppgradera appen

Om du vill uppgradera en befintlig app för uppdaterings dirigering med hjälp av PowerShell följer du stegen i [Service Fabric program uppgradering med PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-tutorial-powershell).

## <a name="remove-the-app"></a>Ta bort appen

Om du vill ta bort programmet följer du stegen i [distribuera och ta bort program med hjälp av PowerShell](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-remove-applications).

För din bekvämlighet har vi tillhandahållit skriptet avdistribuerat. ps1 tillsammans med programmet. Använda skriptet:

  - Anslut till ett Service Fabric-kluster med ```Connect-ServiceFabricCluster```hjälp av.

  - Kör PowerShell-skriptet undeploy. ps1.

> [!NOTE]
> Behåll skriptet och programmappen PatchOrchestrationApplication i samma katalog.

## <a name="view-the-windows-update-results"></a>Visa Windows Update resultat

Appen för uppdaterings dirigering exponerar REST-API: er för att visa de historiska resultaten för användaren. Ett exempel på resultatet JSON:
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

Fält i JSON beskrivs nedan.

Fält | Värden | Information
-- | -- | --
OperationResult | 0-lyckades<br> 1 – lyckades med fel<br> 2 – misslyckades<br> 3-avbruten<br> 4 – avbruten med timeout | Visar resultatet av den övergripande åtgärden (som vanligt vis avser installation av en eller flera uppdateringar).
resultCode | Samma som OperationResult under pågående | Det här fältet visar resultatet av en installations åtgärd för en enskild uppdatering.
OperationType | 1 – installation<br> 0 – Sök och hämta.| Installation är den enda OperationType som visas i resultatet som standard.
WindowsUpdateQuery | Standardvärdet är "IsInstalled = 0" |Windows Update-fråga som användes för att söka efter uppdateringar. Mer information finns i [WuQuery.](https://msdn.microsoft.com/library/windows/desktop/aa386526(v=vs.85).aspx)
RebootRequired | True-omstart krävs<br> false-omstart krävs inte | Anger om omstart krävs för att slutföra installationen av uppdateringar.
OperationStartTime | DateTime | Anger tiden då åtgärden (hämtning/installation) startades.
OperationTime | DateTime | Anger när åtgärden (hämtning/installation) har slutförts.
HResult | 0 – lyckad<br> annat – haveri| Anger orsaken till ett problem med Windows-uppdateringen med updateID "7392acaf-6a85-427c-8a8d-058c25beb0d6".

Om ingen uppdatering har schemalagts ännu är resultatet JSON tomt.

Logga in på klustret för att fråga Windows Update resultat. Ta reda på replik adressen för den primära koordinator tjänsten och besök URL: en&lt;från webbläsaren: http://Replica-IP&gt;:&lt;ApplicationPort&gt;/PatchOrchestrationApplication/v1/ GetWindowsUpdateResults.

REST-slutpunkten för koordinator tjänsten har en dynamisk port. Se Service Fabric Explorer för att kontrol lera den exakta URL: en. Resultatet är till exempel tillgängligt på `http://10.0.0.7:20000/PatchOrchestrationApplication/v1/GetWindowsUpdateResults`.

![Bild av REST-slutpunkt](media/service-fabric-patch-orchestration-application/Rest_Endpoint.png)


Om den omvända proxyn är aktive rad i klustret, kan du även komma åt URL: en från utanför klustret.
Slut punkten som måste vara träffen är http://&lt;SERVERURL&gt;:&lt;REVERSEPROXYPORT&gt;/PatchOrchestrationApplication/CoordinatorService/v1/GetWindowsUpdateResults.

Om du vill aktivera omvänd proxy i klustret följer du stegen i [omvänd proxy i Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-reverseproxy). 

> 
> [!WARNING]
> När den omvända proxyn har kon figurer ATS kan alla Micro-tjänster i klustret som exponerar en HTTP-slutpunkt adresseras utanför klustret.

## <a name="diagnosticshealth-events"></a>Diagnostik/hälso händelser

I följande avsnitt lär du dig hur du felsöker/diagnostiserar problem med uppdaterings uppdateringar via uppdaterings programmet för uppdatering i Service Fabric kluster.

> [!NOTE]
> Du bör ha v 1.4.0-versionen av POA installerad för att få många av de nedan anropade förbättringarna av diagnostiken.

NodeAgentNTService skapar [reparations uppgifter](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtask?view=azure-dotnet) för att installera uppdateringar på noderna. Varje uppgift förbereds sedan av CoordinatorService enligt principen för uppgifts godkännande. De för beredda aktiviteterna godkänns slutligen av Repair Manager som inte godkänner några uppgifter om klustret inte är i felfritt tillstånd. Gör det möjligt att gå stegvisa instruktioner för att förstå hur uppdateringar fortsätter på en nod.

1. NodeAgentNTService, som körs på varje nod, letar efter tillgängliga Windows Update vid den schemalagda tiden. Om det finns uppdateringar är de kvar och de hämtas på noden.
2. När uppdateringarna har hämtats skapar NodeAgentNTService motsvarande reparations uppgift för noden med namnet POS___ < unique_id >. En kan visa dessa reparations aktiviteter med cmdlet [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps) eller i SFX i avsnittet Node details. När reparations uppgiften har skapats flyttas snabbt till [begärt tillstånd](https://docs.microsoft.com/dotnet/api/system.fabric.repair.repairtaskstate?view=azure-dotnet).
3. Koordinator tjänsten söker regelbundet efter reparations uppgifter i begärt tillstånd och går vidare och uppdaterar dem för att förbereda tillstånd baserat på TaskApprovalPolicy. Om TaskApprovalPolicy har kon figurer ATS att vara NodeWise, förbereds en reparations uppgift som motsvarar en nod bara om det inte finns någon annan reparations uppgift för närvarande för att förbereda/godkänna/köra/återställa tillståndet. I händelse av UpgradeWise TaskApprovalPolicy är det på samma sätt alltid säkert att det finns uppgifter i ovanstående tillstånd endast för noder som tillhör samma uppgraderings domän. När en reparations aktivitet har flyttats till förberedelse tillståndet [inaktive ras](https://docs.microsoft.com/powershell/module/servicefabric/disable-servicefabricnode?view=azureservicefabricps) motsvarande Service Fabric nod med avsikt som "starta om".

   POA (v 1.4.0 och senare) skickar händelser med egenskapen "ClusterPatchingStatus" på CoordinaterService för att visa de noder som korrigeras. Bilden nedan visar att uppdateringar installeras på _poanode_0:

    [![Avbildning av status för kluster uppdatering](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png)](media/service-fabric-patch-orchestration-application/clusterpatchingstatus.png#lightbox)

4. När noden är inaktive rad flyttas reparations uppgiften till att tillståndet körs.
   
   >[!NOTE]
   > En nod som fastnar i ett inaktiverat tillstånd kan blockera en ny reparations uppgift som stoppar uppdaterings åtgärden i klustret.

5. När reparations åtgärden körs i tillståndet börjar korrigerings installationen på noden. Här när korrigeringen har installerats kan noden startas om, beroende på korrigeringen. Publicera att reparations uppgiften har flyttats till återställnings läge, vilket gör att du kan återställa noden igen och sedan markeras som slutförd.

   I v-1.4.0 och senare versioner av programmet kan status för uppdateringen hittas genom att titta på hälso händelser på NodeAgentService med egenskapen "WUOperationStatus-[nodnamn]". De markerade avsnitten i bilderna nedan visar status för Windows Update på noden ' poanode_0 ' och ' poanode_2 ':

   [![Bild av åtgärds status för Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusa.png#lightbox)

   [![Bild av åtgärds status för Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusb.png#lightbox)

   En kan också hämta information med PowerShell, genom att ansluta till klustret och hämta tillståndet för reparations uppgiften med [Get-ServiceFabricRepairTask](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricrepairtask?view=azureservicefabricps). Som i exemplet nedan visar att aktiviteten "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15" är i DownloadComplete-tillstånd. Det innebär att uppdateringar har laddats ned på noden "poanode_2" och att installationen görs när aktiviteten flyttas till körnings tillstånd.

   ``` powershell
    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k = Get-ServiceFabricRepairTask -TaskId "POS__poanode_2_125f2969-933c-4774-85d1-ebdf85e79f15"

    D:\service-fabric-poa-bin\service-fabric-poa-bin\Release> $k.ExecutorData
    {"ExecutorSubState":2,"ExecutorTimeoutInMinutes":90,"RestartRequestedTime":"0001-01-01T00:00:00"}
    ```

   Om det fortfarande finns mer att hitta kan du logga in på en eller flera virtuella datorer för att få mer information om problemet med hjälp av Windows-händelseloggar. Ovanstående reparations aktivitet kan bara ha dessa utförar under tillstånd:

      ExecutorSubState | Detalj
    -- | -- 
      Ingen = 1 |  Innebär att det inte fanns någon pågående åtgärd på noden. Möjliga tillstånds över gångar.
      DownloadCompleted = 2 | Innebär att hämtningen har slutförts med slutförd, delvis misslyckad eller misslyckad.
      InstallationApproved = 3 | Innebär att hämtningen slutfördes tidigare och Repair Manager har godkänt installationen.
      InstallationInProgress=4 | Motsvarar tillståndet för körningen av reparations uppgiften.
      InstallationCompleted=5 | Innebär att installationen slutfördes med lyckades, delvis lyckades eller misslyckades.
      RestartRequested = 6 | Innebär att korrigerings installationen slutförs och att det finns en väntande omstart på noden.
      RestartNotNeeded = 7 |  Innebär att omstart inte behövdes efter att korrigerings installationen slutförts.
      RestartCompleted = 8 | Innebär att omstart har slutförts.
      OperationCompleted = 9 | Windows Update-åtgärden har slutförts.
      OperationAborted = 10 | Innebär att Windows Update-åtgärden avbryts.

6. När uppdaterings försöket på en nod har slutförts i v-1.4.0 och senare, publiceras en händelse med egenskapen "WUOperationStatus-[nodnamn]" på NodeAgentService för att meddela vid nästa försök att ladda ned och installera Update, starta. Se bilden nedan:

     [![Bild av åtgärds status för Windows Update](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png)](media/service-fabric-patch-orchestration-application/wuoperationstatusc.png#lightbox)

### <a name="diagnostic-logs"></a>Diagnostikloggar

Korrigeringsfiler för loggning av Orchestration-appar samlas in som en del av Service Fabric körnings loggar.

Om du vill avbilda loggar via diagnostikverktyg/pipeline som du väljer. Uppdaterings Dirigerings programmet använder under fasta Provider-ID: n för att logga händelser via [händelse källa](https://docs.microsoft.com/dotnet/api/system.diagnostics.tracing.eventsource?view=netframework-4.5.1)

- e39b723c-590c-4090-abb0-11e3e6616346
- fc0028ff-bfdc-499f-80dc-ed922c52c5e9
- 24afa313-0d3b-4c7c-b485-1047fd964b60
- 05dc046c-60e9-4ef7-965e-91660adffa68

### <a name="health-reports"></a>Hälso rapporter

Appen för uppdaterings dirigering publicerar också hälso rapporter mot koordinator tjänsten eller Node Agent-tjänsten i följande fall:

#### <a name="the-node-agent-ntservice-is-down"></a>NTService för Node-agenten är inte tillgänglig

Om Node agent-NTService inte finns på en nod genereras en hälso rapport på varnings nivå mot Node Agent-tjänsten.

#### <a name="the-repair-manager-service-is-not-enabled"></a>Reparations hanterarens tjänst är inte aktive rad

Om tjänsten Repair Manager inte hittas i klustret genereras en hälso rapport på varnings nivå för koordinator tjänsten.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

F. **Varför visas mitt kluster i fel tillstånd när appen för uppdaterings dirigering körs?**

A. Under installationen inaktive ras eller startas noderna om, vilket tillfälligt kan leda till att klustrets hälso tillstånd går ur funktion.

Baserat på principen för programmet kan antingen en nod gå nedåt under en uppdaterings åtgärd *eller* så kan en hel uppgraderings domän gå nedåt samtidigt.

I slutet av Windows Update-installationen återaktiveras noderna efter omstart.

I följande exempel gick klustret till ett fel tillstånd tillfälligt eftersom två noder var nere och MaxPercentageUnhealthyNodes-principen har överskridits. Felet är tillfälligt tills uppdaterings åtgärden pågår.

![Bild av ohälsosamt kluster](media/service-fabric-patch-orchestration-application/MaxPercentage_causing_unhealthy_cluster.png)

Läs fel söknings avsnittet om problemet kvarstår.

F. **Appen för uppdaterings dirigering är i varnings tillstånd**

A. Kontrol lera om en hälso rapport som har publicerats i programmet är rotor saken. Varningen innehåller vanligt vis information om problemet. Om problemet är tillfälligt förväntas programmet automatiskt återställas från det här läget.

F. **Vad kan jag göra om mitt kluster inte är felfritt och jag behöver göra en brådskande uppdatering av operativ systemet?**

A. Appen för uppdaterings dirigering installerar inte uppdateringar när klustret inte är felfritt. Försök att försätta ditt kluster i felfritt tillstånd för att avblockera arbets flödet för patch-Orchestration-appen.

F. **Ska jag ange TaskApprovalPolicy som "NodeWise" eller "UpgradeDomainWise" för mitt kluster?**

A. "UpgradeDomainWise" gör det övergripande kluster korrigeringen snabbare genom att korrigera alla noder som tillhör en uppgraderings domän parallellt. Det innebär att noder som tillhör en hel uppgraderings domän inte är tillgängliga (i [inaktiverat](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) tillstånd) under korrigerings processen.

Om NodeWise-principen däremot bara uppdaterar en nod i taget innebär det att den övergripande kluster korrigeringen tar längre tid. Men vid max är bara en nod otillgänglig (i [inaktiverat](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabled) tillstånd) under uppdaterings processen.

Om klustret kan tolerera körning på N-1 antal uppgraderings domäner under uppdaterings cykeln (där N är det totala antalet uppgraderings domäner i klustret) kan du ange principen som "UpgradeDomainWise", annars ange den till "NodeWise".

F. **Hur lång tid tar det att korrigera en nod?**

A. Det kan ta några minuter att korrigera en nod (till exempel: [Windows Defender Definitions uppdateringar](https://www.microsoft.com/en-us/wdsi/definitions)) till timmar (till exempel: [Kumulativa Windows-uppdateringar](https://www.catalog.update.microsoft.com/Search.aspx?q=windows%20server%20cumulative%20update)). Tiden som krävs för att korrigera en nod beror främst på 
 - Storlek på uppdateringar
 - Antal uppdateringar som måste tillämpas i ett korrigerings fönster
 - Tid det tar att installera uppdateringarna, starta om noden (om det behövs) och slutför installations stegen efter omstart.
 - Prestanda för VM/maskin-och nätverks förhållanden.

F. **Hur lång tid tar det att korrigera ett helt kluster?**

A. Den tid som krävs för att korrigera ett helt kluster beror på följande faktorer:

- Tid som krävs för att korrigera en nod.
- Principen för koordinator tjänsten. -Standard principen, `NodeWise`, resulterar i att endast en nod i taget korrigeras, vilket kan vara långsammare än. `UpgradeDomainWise` Exempel: Om en nod tar ~ 1 timme att korrigeras, för att korrigera en 20-nod (samma typ av noder) med 5 uppgraderings domäner, var och en som innehåller 4 noder.
    - Det bör ta ~ 20 timmar att korrigera hela klustret, om principen är`NodeWise`
    - Det bör ta ~ 5 timmar om principen är`UpgradeDomainWise`
- Kluster belastning – varje uppdaterings åtgärd kräver omlokalisering av kund arbets belastningen till andra tillgängliga noder i klustret. En nod som underhåller korrigerings filen har [inaktiverats](https://docs.microsoft.com/dotnet/api/system.fabric.query.nodestatus?view=azure-dotnet#System_Fabric_Query_NodeStatus_Disabling) under den här tiden. Om klustret körs nära högsta belastning tar det längre tid att inaktivera processen. Därför kan en fullständig uppdaterings process förefalla vara långsam under sådana ingångs förhållanden.
- Kluster hälso fel under korrigeringen – eventuell [försämring](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet#System_Fabric_Health_HealthState_Error) [av hälso tillståndet för klustret](https://docs.microsoft.com/azure/service-fabric/service-fabric-health-introduction) skulle avbryta korrigerings processen. Detta lägger till den övergripande tiden som krävs för att korrigera hela klustret.

F. **Varför visas vissa uppdateringar i Windows Update resultat som erhållits via REST API, men inte under Windows Update historik på datorn?**

A. Vissa produkt uppdateringar visas bara i respektive uppdaterings-/korrigerings historik. Till exempel kan Windows Defender-uppdateringar kanske inte visas i Windows Update historik på Windows Server 2016.

F. **Kan uppdatering av Orchestration-appen användas för att uppdatera mitt dev-kluster (kluster med en nod)?**

A. Nej, appen för uppdaterings dirigering kan inte användas för att korrigera ett kluster med en nod. Den här begränsningen är avsiktlig, eftersom [Service Fabric-systemtjänster eller-kund-](https://docs.microsoft.com/azure/service-fabric/service-fabric-technical-overview#system-services) appar kommer att vara i drift stillestånds tid och därför kommer eventuella reparations jobb inte att godkännas av Repair Manager.

F. **Hur gör jag för att korrigera klusternoder på Linux?**

A. Se [skalnings uppsättningar för virtuella Azure-datorer](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) för att dirigera uppdateringar i Linux.

F.**Varför tar uppdaterings cykeln så länge?**

A. Fråga för resultat-JSON, gå sedan till posten för uppdaterings cykeln för alla noder och försök sedan att ta reda på hur lång tid det tar att installera uppdateringen på varje nod med hjälp av OperationStartTime och OperationTime (OperationCompletionTime). Om det var en stor tids period då ingen uppdatering skedde, kan det bero på att klustret var i fel tillstånd och att reparations hanteraren inte godkänt några andra POA reparations uppgifter. Om installationen av uppdateringen tog lång tid på någon nod, kan det vara möjligt att noden inte har uppdaterats från lång tid och att en stor del uppdateringar väntade på att installeras, vilket tog tid. Det kan också finnas ett fall där korrigeringen på en nod blockeras på grund av att noden har fastnat i inaktive ring, vilket vanligt vis sker eftersom inaktive ring av noden kan leda till att kvorum/data går förlorade.

F. **Varför krävs det att inaktivera noden när POA korrigerar den?**

A. Uppdaterings Dirigerings programmet inaktiverar noden med "starta om"-funktionen som stoppar/allokerar om alla Service Fabric-tjänster som körs på noden. Detta görs för att säkerställa att program inte upphör med en blandning av nya och gamla DLL-filer, så det rekommenderas inte att korrigera en nod utan att inaktivera den.

## <a name="disclaimers"></a>Ansvarsfriskrivningar

- Appen för uppdaterings dirigering accepterar licens avtalet för slutanvändare av Windows Update för användarens räkning. Du kan också stänga av inställningen i programmets konfiguration.

- Appen för uppdaterings dirigering samlar in telemetri för att spåra användning och prestanda. Programmets telemetri följer inställningen för inställningen för Service Fabric körningens telemetri (som är aktiverat som standard).

## <a name="troubleshooting"></a>Felsökning

### <a name="a-node-is-not-coming-back-to-up-state"></a>En nod kommer inte tillbaka till tillstånd

**Noden kanske fastnar i ett inaktiverat tillstånd eftersom**:

En säkerhets kontroll väntar. För att åtgärda den här situationen ser du till att tillräckligt många noder är tillgängliga i felfritt tillstånd.

**Noden kan ha fastnat i ett inaktiverat tillstånd eftersom**:

- Noden har inaktiverats manuellt.
- Noden har inaktiverats på grund av ett pågående Azure Infrastructure-jobb.
- Noden har inaktiverats tillfälligt av appen för uppdaterings dirigering för att korrigera noden.

**Noden kan ha fastnat i ett läge eftersom**:

- Noden försattes i ett tillstånd manuellt.
- Noden genomgår en omstart (som kan utlösas av appen för uppdaterings dirigering).
- Noden är avstängd på grund av problem med en virtuell dator eller dator eller nätverks anslutning.

### <a name="updates-were-skipped-on-some-nodes"></a>Uppdateringar hoppades över på vissa noder

Appen för uppdaterings dirigering försöker installera en Windows-uppdatering enligt principen för att ändra schemat. Tjänsten försöker återställa noden och hoppa över uppdateringen enligt användnings principen.

I sådana fall genereras en hälso rapport på varnings nivå mot Node Agent-tjänsten. Resultatet av Windows Update innehåller också möjliga orsaker till problemet.

### <a name="the-health-of-the-cluster-goes-to-error-while-the-update-installs"></a>Hälso tillståndet för klustret går fel när uppdateringen installeras

En felaktig Windows-uppdatering kan ta bort hälso tillståndet för ett program eller kluster på en viss nod eller uppgraderings domän. Appen för uppdaterings dirigering fortsätter alla efterföljande Windows Update åtgärder tills klustret är felfritt.

En administratör måste ingripa och ta reda på varför programmet eller klustret blev skadat på grund av Windows Update.

## <a name="release-notes"></a>Viktig information

>[!NOTE]
> Från och med version 1.4.0 finns viktig information och versioner på GitHub release- [sidan](https://github.com/microsoft/Service-Fabric-POA/releases/).

### <a name="version-110"></a>Version 1.1.0
- Offentlig version

### <a name="version-111"></a>Version 1.1.1
- Ett fel har åtgärd ATS i SetupEntryPoint av NodeAgentService som förhindrade installation av NodeAgentNTService.

### <a name="version-120"></a>Version 1.2.0

- Fel korrigeringar kring arbets flödet för omstart av systemet.
- Fel korrigering vid skapande av RM-uppgifter på grund av vilken hälso kontroll under förberedelserna av reparations aktiviteter som förväntades.
- Start läget för Windows-POANodeSvc ändrades från Auto till fördröjt automatiskt.

### <a name="version-121"></a>Version 1.2.1

- Fel korrigering i kluster för skalbar arbets flöde. Introducerade skräp insamlings logik för POA reparations uppgifter som hör till obefintliga noder.

### <a name="version-122"></a>Version 1.2.2

- Diverse fel korrigeringar.
- Binärfiler är nu signerade.
- Lade till sfpkg-länk för programmet.

### <a name="version-130"></a>Version 1.3.0

- Om du anger InstallWindowsOSOnlyUpdates till false installeras alla tillgängliga uppdateringar nu.
- Ändrade logiken för att inaktivera automatiska uppdateringar. Detta åtgärdar ett fel där automatiska uppdateringar inte inaktiverades på Server 2016 och senare.
- Parametriserad placerings begränsning för båda mikrotjänsterna i POA för avancerade användnings fall.

### <a name="version-131"></a>Version 1.3.1
- Korrigera regression där POA 1.3.0 inte fungerar på Windows Server 2012 R2 eller lägre på grund av att det inte går att inaktivera automatiska uppdateringar. 
- Åtgärda fel där InstallWindowsOSOnlyUpdates-konfigurationen alltid plockas som sant.
- Ändrar standardvärdet för InstallWindowsOSOnlyUpdates till false.

### <a name="version-132"></a>Version 1.3.2
- Åtgärda ett problem som har åtgärdat livs cykeln för korrigering på en nod, om det finns noder med namn som är delmängd av det aktuella nodnamnet. För sådana noder är det möjligt att korrigeringen saknas eller att omstart väntar. 
