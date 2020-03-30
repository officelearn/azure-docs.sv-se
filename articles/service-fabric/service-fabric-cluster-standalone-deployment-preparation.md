---
title: Förbered fristående klusterdistribution
description: Dokumentation som är relaterad till att förbereda miljön och skapa klusterkonfigurationen, som ska beaktas innan ett kluster som är avsett för hantering av en produktionsarbetsbelastning distribueras.
author: dkkapur
ms.topic: conceptual
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 6a00b7d1b72d594c08021982b2448de6275414c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610071"
---
# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planera och förbered den fristående klusterdistributionen av service fabric

<a id="preparemachines"></a>Utför följande steg innan du skapar klustret.

## <a name="plan-your-cluster-infrastructure"></a>Planera klusterinfrastrukturen
Du är på väg att skapa en Service Fabric kluster på datorer du "äger", så att du kan bestämma vilka typer av fel du vill att klustret ska överleva. Behöver du till exempel separata kraftledningar eller Internetanslutningar som levereras till dessa datorer? Tänk dessutom på den fysiska säkerheten för dessa maskiner. Var finns maskinerna och vem behöver tillgång till dem? När du har fattat dessa beslut kan du logiskt mappa datorerna till olika feldomäner (se nästa steg). Infrastrukturplaneringen för produktionskluster är mer involverad än för testkluster.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Fastställa antalet feldomäner och uppgraderingsdomäner
En [ *feldomän* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) är en fysisk felenhet och är direkt relaterad till den fysiska infrastrukturen i datacenter. En feldomän består av maskinvarukomponenter (datorer, växlar, nätverk med mera) som delar en enda felpunkt. Även om det inte finns någon 1:1 mappning mellan feldomäner och rack, löst sett, kan varje rack betraktas som en feldomän.

När du anger FD:er i ClusterConfig.json kan du välja namnet för varje FD. Service Fabric stöder hierarkiska FD:er, så att du kan återspegla din infrastrukturtopologi i dem.  Följande FD:er är till exempel giltiga:

* "faultDomain": "fd:/Room1/Rack1/Machine1"
* "felDomain": "FD:/FD1"
* "faultDomain": "fd:/Room1/Rack1/PDU1/M1"

En *uppgraderingsdomän* (UD) är en logisk enhet med noder. Under Service Fabric-orkestrerade uppgraderingar (antingen en programuppgradering eller en klusteruppgradering) tas alla noder i en UD ned för att utföra uppgraderingen medan noder i andra UD:er förblir tillgängliga för att betjäna begäranden. Firmware uppgraderingar du utför på dina maskiner inte hedra UDs, så du måste göra dem en maskin i taget.

Det enklaste sättet att tänka på dessa begrepp är att betrakta FDs som enheten för oplanerade fel och UDs som enheten för planerat underhåll.

När du anger UD:er i ClusterConfig.json kan du välja namnet för varje UD. Följande namn är till exempel giltiga:

* "upgradeDomain": "UD0"
* "upgradeDomain": "UD1A"
* "upgradeDomain": "DomainRed"
* "upgradeDomain": "Blå"

Mer detaljerad information om FD och UDs finns i [Beskriva ett service fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md).

Ett kluster i produktion bör sträcka sig över minst tre FD:er för att stödjas i en produktionsmiljö, om du har full kontroll över underhåll och hantering av noderna, det vill säga du ansvarar för att uppdatera och ersätta datorer. För kluster som körs i miljöer (det vill säga Amazon Web Services VM-instanser) där du inte har full kontroll över datorerna bör du ha minst fem FD:er i klustret. Varje FD kan ha en eller flera noder. Detta för att förhindra problem som orsakas av datoruppgraderingar och uppdateringar, som beroende på deras timing kan störa körningen av program och tjänster i kluster.

## <a name="determine-the-initial-cluster-size"></a>Fastställa den ursprungliga klusterstorleken

I allmänhet bestäms antalet noder i klustret baserat på dina affärsbehov, det vill säga hur många tjänster och behållare som körs i klustret och hur många resurser behöver du för att upprätthålla dina arbetsbelastningar. För produktionskluster rekommenderar vi att du har minst fem noder i klustret och spänner över 5 FD:er. Men som beskrivits ovan, om du har full kontroll över dina noder och kan spänna över tre FDs, bör tre noder också göra jobbet.

Testkluster som kör tillståndskänsliga arbetsbelastningar bör ha tre noder, medan testkluster som bara kör tillståndslösa arbetsbelastningar bara behöver en nod. Det bör också noteras att för utvecklingsändamål kan du ha mer än en nod på en viss maskin. I en produktionsmiljö stöder dock Service Fabric endast en nod per fysisk eller virtuell dator.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Förbered de maskiner som fungerar som noder

Här är några rekommenderade specifikationer för varje dator som du vill lägga till i klustret:

* Minst 16 GB RAM
* Minst 40 GB ledigt diskutrymme
* En 4-kärnig eller större PROCESSOR
* Anslutning till ett säkert nätverk eller nätverk för alla datorer
* Windows Server OS installerat (giltiga versioner: 2012 R2, 2016, 1709 eller 1803). Service Fabric version 6.4.654.9590 och senare stöder även Server 2019 och 1809.
* [.NET Framework 4.5.1 eller senare](https://www.microsoft.com/download/details.aspx?id=40773), fullständig installation
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/install/installing-windows-powershell)
* [Fjärrregistertjänsten](https://technet.microsoft.com/library/cc754820) ska köras på alla datorer
* Service Fabric-installationsenheten måste vara NTFS-filsystem

Klusteradministratören som distribuerar och konfigurerar klustret måste ha [administratörsbehörighet](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) på var och en av datorerna. Du kan inte installera Service Fabric på en domänkontrollant.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Ladda ned fristående servicepaket för Windows Server
[Hämta länk - Service Fabric Fristående paket - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) och packa upp paketet, antingen till en distributionsmaskin som inte ingår i klustret eller till en av de datorer som kommer att vara en del av klustret.

## <a name="modify-cluster-configuration"></a>Ändra klusterkonfiguration
Om du vill skapa ett fristående kluster måste du skapa en fristående klusterkonfiguration ClusterConfig.json-fil, som beskriver specifikationen för klustret. Du kan basera konfigurationsfilen på mallarna som finns på länken nedan. <br>
[Fristående klusterkonfigurationer](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Mer information om avsnitten i den här filen finns i [Konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md).

Öppna en av ClusterConfig.json-filerna från paketet som du hämtade och ändra följande inställningar:

| **Konfigurationsinställning** | **Beskrivning** |
| --- | --- |
| **NodeTyper** |Med nodtyper kan du separera klusternoderna i olika grupper. Ett kluster måste ha minst en NodeType. Alla noder i en grupp har följande gemensamma egenskaper: <br> **Namn** - Det här är nodtypens namn. <br>**Slutpunktsportar** - Det här är olika namngivna slutpunkter (portar) som är associerade med den här nodtypen. Du kan använda vilket portnummer som helst som du vill, så länge de inte står i konflikt med något annat i det här manifestet och inte redan används av något annat program som körs på datorn/den virtuella datorn. <br> **Placeringsegenskaper** – Dessa beskriver egenskaper för den här nodtypen som du använder som placeringsbegränsningar för systemtjänsterna eller dina tjänster. Dessa egenskaper är användardefinierade nyckel-/värdepar som ger extra metadatadata för en viss nod. Exempel på nodegenskaper är om noden har en hårddisk eller ett grafikkort, antalet spindlar i hårddisken, kärnorna och andra fysiska egenskaper. <br> **Kapacitet -** Nodkapacitet definierar namn och belopp för en viss resurs som en viss nod har tillgänglig för förbrukning. En nod kan till exempel definiera att den har kapacitet för ett mått som kallas "MemoryInMb" och att den har 2048 MB tillgängligt som standard. Dessa kapaciteter används vid körning för att säkerställa att tjänster som kräver särskilda mängder resurser placeras på noderna som har dessa resurser tillgängliga i de belopp som krävs.<br>**IsPrimary** - Om du har mer än en NodeType definierad se till att endast en är inställd på primär med värdet *sant*, vilket är där systemtjänsterna körs. Alla andra nodtyper ska anges till värdet *false* |
| **Noder** |Dessa är information om var och en av noderna som ingår i klustret (nodtyp, nodnamn, IP-adress, feldomän och uppgraderingsdomän för noden). De datorer som du vill att klustret ska skapas på måste visas här med sina IP-adresser. <br> Om du använder samma IP-adress för alla noder skapas ett enluxet kluster som du kan använda för testning. Använd inte one-box-kluster för att distribuera produktionsarbetsbelastningar. |

När klusterkonfigurationen har konfigurerat alla inställningar för miljön kan den testas mot klustermiljön (steg 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Miljökonfiguration

När en klusteradministratör konfigurerar ett fristående service fabric-kluster måste miljön konfigureras med följande villkor: <br>
1. Användaren som skapar klustret bör ha säkerhetsbehörighet på administratörsnivå för alla datorer som visas som noder i klusterkonfigurationsfilen.
2. Dator som klustret skapas från, liksom varje klusternoddator måste:
   * Har Service Fabric SDK avinstallerat
   * Har Service Fabric-körningen avinstallerats
   * Ha tjänsten Windows Firewall (mpssvc) aktiverad
   * Ha fjärrregistertjänsten (fjärrregistret) aktiverad
   * Ha fildelning (SMB) aktiverat
   * Öppna nödvändiga portar, baserat på klusterkonfigurationsportar
   * Har nödvändiga portar öppna för Windows SMB och Fjärrregistertjänst: 135, 137, 138, 139 och 445
   * Ha nätverksanslutning till varandra
3. Ingen av klusternoddatorerna ska vara domänkontrollant.
4. Om klustret som ska distribueras är ett säkert kluster, validera de nödvändiga säkerhetskraven är på plats och konfigureras korrekt mot konfigurationen.
5. Om klusterdatorerna inte är tillgängliga för Internet ställer du in följande i klusterkonfigurationen:
   * Inaktivera telemetri: Under *egenskaper* som *"enableTelemetry": false*
   * Inaktivera automatisk Fabric-version nedladdning & meddelanden om att den aktuella klusterversionen närmar sig slutet av stöd: Under *egenskaper* som *"fabricClusterAutoupgradeEnabled": false*
   * Alternativt, om nätverksanslutningen till Internet är begränsad till vitlistade domäner, krävs domänerna nedan för automatisk uppgradering: go.microsoft.com download.microsoft.com

6. Ange lämpliga antivirusundanskap för Service Fabric:

| **Antivirus uteslutna kataloger** |
| --- |
| Programfiler\Microsoft-tjänstinfrastruktur |
| FabricDataRoot (från klusterkonfiguration) |
| FabricLogRoot (från klusterkonfiguration) |

| **Antivirus uteslutna processer** |
| --- |
| Tyg.exe |
| FabricHost.exe (på nytt) |
| FabricInstallerService.exe |
| FabricSetup.exe |
| FabricDeployer.exe |
| ImageBuilder.exe |
| FabricGateway.exe |
| FabricDCA.exe |
| FabricFAS.exe |
| FabricUOS.exe |
| FabricRM.exe |
| FileStoreService.exe |

## <a name="validate-environment-using-testconfiguration-script"></a>Validera miljö med testkonfigurationsskript
Skriptet TestConfiguration.ps1 finns i det fristående paketet. Det används som en Best Practices Analyzer för att validera några av kriterierna ovan och bör användas som en sanity kontroll för att validera om ett kluster kan distribueras på en viss miljö. Om det finns något fel läser du listan under [Miljöinställningar](service-fabric-cluster-standalone-deployment-preparation.md) för felsökning.

Det här skriptet kan köras på alla datorer som har administratörsbehörighet till alla datorer som visas som noder i klusterkonfigurationsfilen. Datorn som skriptet körs på behöver inte vara en del av klustret.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json
Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
Running Best Practices Analyzer...
Best Practices Analyzer completed successfully.


LocalAdminPrivilege        : True
IsJsonValid                : True
IsCabValid                 : True
RequiredPortsOpen          : True
RemoteRegistryAvailable    : True
FirewallAvailable          : True
RpcCheckPassed             : True
NoConflictingInstallations : True
FabricInstallable          : True
Passed                     : True
```

För närvarande validerar inte den här konfigurationstestmodulen säkerhetskonfigurationen så detta måste göras oberoende av dem.

> [!NOTE]
> Vi gör kontinuerligt förbättringar för att göra denna modul mer robust, så om det finns en felaktig eller saknas fall som du tror för närvarande inte fångas av TestConfiguration, vänligen meddela oss genom våra [supportkanaler](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).
>
>

## <a name="next-steps"></a>Nästa steg
* [Skapa ett fristående kluster som körs på Windows Server](service-fabric-cluster-creation-for-windows-server.md)
