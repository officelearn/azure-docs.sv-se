---
title: Azure Service Fabric fristående kluster Distributionsförberedelser | Microsoft Docs
description: Dokumentation för att förbereda miljön och skapa klusterkonfigurationen, för att anses vara innan du distribuerar ett kluster som är avsedd för hantering av en produktionsarbetsbelastning.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 9/11/2018
ms.author: dekapur
ms.openlocfilehash: 7357577ea7aff467288578673110b1a6dda7ccef
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391055"
---
<a id="preparemachines"></a>

# <a name="plan-and-prepare-your-service-fabric-standalone-cluster-deployment"></a>Planera och förbereda distributionen av Service Fabric fristående kluster
Utför följande steg innan du skapar klustret.

## <a name="plan-your-cluster-infrastructure"></a>Planera klusterinfrastrukturen för
Du kommer att skapa ett Service Fabric-kluster på datorer som du ”äger”, så att du kan avgöra vilka typer av fel som du vill att klustret ska överleva. Till exempel du behöver separata power linjer eller Internet-anslutningar som angetts för dessa datorer? Dessutom kan du överväga att den fysiska säkerheten för dessa datorer. Var finns datorerna och vilka som behöver åtkomst till dem? När du har gjort dessa beslut mappa du logiskt datorerna till olika feldomäner (se nästa steg). Planera för produktionskluster infrastrukturen är mer komplicerat än för testkluster.

## <a name="determine-the-number-of-fault-domains-and-upgrade-domains"></a>Bestämma antalet feldomäner och uppgraderingsdomäner
En [ *feldomän* (FD)](service-fabric-cluster-resource-manager-cluster-description.md) är en fysisk enhet med fel och är direkt relaterad till den fysiska infrastrukturen i datacenter. En feldomän består av maskinvarukomponenter (datorer, växlar, nätverk och mer) som delar en enskild felpunkt. Även om det finns ingen 1:1-mappning mellan feldomäner och rack, kan löst talar varje rack betraktas som en feldomän.

När du anger FD i ClusterConfig.json, väljer du namnet för varje FD. Service Fabric har stöd för hierarkisk FD, så du kan din infrastruktur-topologi i dem.  Till exempel är följande FD giltiga:

* ”faultDomain” ”: fd: / Dator1-Room1/Rack1”
* ”faultDomain” ”: fd: / FD1”
* ”faultDomain” ”: fd: / Room1/Rack1/PDU1/M1”

En *uppgraderingsdomän* (UD) är en logisk enhet för noder. Under Service Fabric dirigerad uppgraderingar (en uppgradering av programmet eller en uppgradering av klustret) tas alla noder i en UD bort att utföra uppgraderingen även noder i andra ud är tillgänglig för att hantera begäranden. Firmware-uppgraderingar som du utför på dina datorer inte respekterar ud, så måste du göra dem något datorer i taget.

Det enklaste sättet att tänka på de här koncepten är att tänka på FD som oplanerade fel och ud som måttenhet för planerat underhåll.

När du anger ud i ClusterConfig.json, väljer du namnet för varje UD. Till exempel är följande namn giltiga:

* ”upgradeDomain”: ”UD0”
* ”upgradeDomain”: ”UD1A”
* ”upgradeDomain”: ”DomainRed”
* ”upgradeDomain”: ”Blue”

Mer information om FD och ud finns [som beskriver ett Service Fabric-kluster](service-fabric-cluster-resource-manager-cluster-description.md).

Ett kluster i produktionen ska omfatta minst tre Feldomäner för att kunna användas i en produktionsmiljö, om du har fullständig kontroll över underhåll och hantering av noderna, det vill säga du ansvarar för att uppdatera och ersätta datorer. Du bör ha minst fem FD för kluster som körs i (det vill säga Amazon Web Services VM-instanser) där du inte har fullständig kontroll över datorerna i klustret. Varje FD kan ha en eller flera noder. Det här är att förhindra problem som orsakas av datorn uppgraderingar och uppdateringar som beroende på deras tidsinställning kan störa körning av program och tjänster i kluster.

## <a name="determine-the-initial-cluster-size"></a>Fastställa inledande klusterstorleken

I allmänhet bör antalet noder i klustret bestäms utifrån dina affärsbehov som är, hur många tjänster och behållare körs på klustret och hur många resurser behöver du för hantering av dina arbetsbelastningar. För produktionskluster rekommenderar vi att minst fem noder i klustret, med 5 fd. Dock som beskrivs ovan, om du har fullständig kontroll över dina noder och kan sträcka sig över tre Feldomäner, bör sedan tre noder också göra jobbet.

Testkluster tillståndskänsliga arbetsbelastningar som körs ska ha tre noder, medan testkluster som endast kör tillståndslösa arbetsbelastningar måste en nod. Det bör också noteras att utvecklingssyfte kan du kan ha fler än en nod på en viss dator. I en produktionsmiljö stöder Service Fabric dock bara en nod per fysisk eller virtuell dator.

## <a name="prepare-the-machines-that-will-serve-as-nodes"></a>Förbereda datorer som fungerar som noder

Här är några rekommenderade specifikationer för varje dator som du vill lägga till i klustret:

* Minst 16 GB RAM
* Minst 40 GB ledigt diskutrymme
* En 4 kärnor eller större processor
* Anslutning till ett säkert nätverk eller nätverk för alla datorer
* Windows Server-operativsystem installerat (giltiga versioner: 2012 R2, 2016, 1709 eller 1803)
* [.NET framework 4.5.1 eller senare](https://www.microsoft.com/download/details.aspx?id=40773), fullständig installation
* [Windows PowerShell 3.0](https://msdn.microsoft.com/powershell/scripting/setup/installing-windows-powershell)
* Den [RemoteRegistry service](https://technet.microsoft.com/library/cc754820) måste köras på alla datorer

Klusteradministratören som distribuerar och konfigurerar klustret måste ha [administratörsbehörighet](https://social.technet.microsoft.com/wiki/contents/articles/13436.windows-server-2012-how-to-add-an-account-to-a-local-administrator-group.aspx) på varje dator. Du kan inte installera Service Fabric på en domänkontrollant.

## <a name="download-the-service-fabric-standalone-package-for-windows-server"></a>Ladda ned det fristående paketet för Service Fabric för Windows Server
[Hämta länk - Service Fabric fristående Package - Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) och packa upp paketet till en dator för distribution som inte ingår i klustret eller till en av de datorer som ska ingå i klustret.

## <a name="modify-cluster-configuration"></a>Ändra klusterkonfiguration
Om du vill skapa ett fristående kluster som du behöver skapa en fristående kluster ClusterConfig.json konfigurationsfil, som beskriver specifikation av klustret. Du kan basera konfigurationsfilen om mallar finns i länken nedan. <br>
[Konfigurationer för fristående kluster](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

Mer information om avsnitten i den här filen finns [konfigurationsinställningar för fristående Windows-kluster](service-fabric-cluster-manifest.md).

Öppna en av filerna ClusterConfig.json från paketet som du hämtade och ändra följande inställningar:
| **Konfigurationsinställningen** | **Beskrivning** |
| --- | --- |
| **NodeTypes** |Nodtyper kan du dela dina klusternoder i olika grupper. Ett kluster måste ha minst en NodeType. Alla noder i en grupp har de vanliga förekommande egenskaperna: <br> **Namn på** – det här är namnet på nodtypen. <br>**Slutpunktsportar** – dessa är olika namngivna slutpunkter (portar) som är associerade med den här nodtypen. Du kan använda valfri portnummer som du vill, förutsatt att de inte står i konflikt med något annat i manifestet och som inte redan används av andra program som körs på datorn och VM. <br> **Placeringsegenskaper** -dessa beskriver egenskaperna för den här nodtypen som du använder som placeringsbegränsningar för systemtjänster eller dina tjänster. Dessa egenskaper är en användardefinierad nyckel/värde-par som ger extra metadata för en viss nod. Exempel på nodegenskaper skulle vara om noden har en hårddisk eller grafikkort, antalet spindlar i dess hårddisken, kärnor och andra fysiska egenskaper. <br> **Kapaciteter** -nodkapaciteterna definiera namn och beloppet för en viss resurs att en särskild nod har tillgängligt för förbrukning. En nod kan till exempel ange att den har kapacitet för ett mått som kallas ”MemoryInMb” och att det finns 2 048 MB tillgängligt som standard. Dessa kapaciteter för att säkerställa att tjänster som kräver viss mängder resurser placeras på noder som har resurser som är tillgängliga i de obligatoriska mängder som anges vid körning.<br>**IsPrimary** – om du har mer än en NodeType definierats se till att endast en har angetts till primära med värdet *SANT*, vilket är där systemet services körs. Alla andra typer av noden ska vara inställd på värdet *FALSKT* |
| **Noder** |Det här är informationen för varje nod som ingår i klustret (nodtyp, nodnamnet, IP-adress, feldomän och uppgraderingsdomän för noden). Datorerna du vill att klustret ska skapas på behovet av att listas här med sina IP-adresser. <br> Om du använder samma IP-adress för alla noder, skapas en en-box-kluster, som du kan använda i testsyfte. Använd inte One-box-kluster för att distribuera arbetsbelastningar i produktion. |

Efter att klusterkonfigurationen har haft alla inställningar som konfigurerats i miljön, kan du testa den mot klustermiljön (steg 7).

<a id="environmentsetup"></a>

## <a name="environment-setup"></a>Konfigurera miljön

När en Klusteradministratör konfigurerar fristående Service Fabric-kluster, måste miljön konfigureras med följande kriterier: <br>
1. Den användare som skapar klustret bör ha administratörsrättigheter säkerhetsrättigheter för att alla datorer som listas som noder i klustret konfigurationsfilen.
2. Datorn där klustret har skapats, samt varje noddatorn i klustret måste du göra följande:
* Har avinstallerat Service Fabric SDK
* Har avinstallerat Service Fabric-körningen 
* Har aktiverat tjänsten Windows-brandväggen (mpssvc)
* Har aktiverat tjänsten Remote Registry (remote registry)
* Har aktiverat delning (SMB)-fil
* Ha nödvändiga portar öppnas, baserat på klustret configuration portar
* Har nödvändiga portarna för Windows-SMB- och Remote Registry-tjänsten: 135, 137, 138, 139 och 445
* Ha en nätverksanslutning till varandra
3. Ingen av de kluster nod-datorerna ska vara en domänkontrollant.
4. Om klustret distribueras är ett säkert kluster kan verifiera den säkerhet som behövs krav är placera och är korrekt konfigurerade mot konfigurationen.
5. Om klustret datorer inte är tillgängligt via internet, kan du ange följande i klusterkonfigurationen:
* Inaktivera telemetri: Under *egenskaper* ange *”Konfigurationsparametern”: false*
* Inaktivera automatisk nedladdning av Fabric-version & meddelanden att support upphör snart för den aktuella versionen av klustret: Under *egenskaper* ange *”fabricClusterAutoupgradeEnabled”: false*
* Alternativt, om Internetåtkomst för nätverk är begränsad till vitt visas domäner, domäner nedan krävs för automatisk uppgradering: go.microsoft.com download.microsoft.com

6. Ange rätt Service Fabric-antivirusundantag:

| **Antivirus uteslutna kataloger** |
| --- |
| Program Files\Microsoft Service Fabric |
| FabricDataRoot (från klusterkonfiguration) |
| FabricLogRoot (från klusterkonfiguration) |

| **Antivirus undantagna processer** |
| --- |
| Fabric.exe |
| FabricHost.exe |
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

## <a name="validate-environment-using-testconfiguration-script"></a>Validera miljön med hjälp av TestConfiguration skript
Skriptet TestConfiguration.ps1 finns i det fristående paketet. Den används som en Best Practices Analyzer för att verifiera några av villkoren ovan och bör användas som en förstånd-kontroll för att kontrollera om ett kluster kan distribueras på en viss miljö. Om det finns några fel, finns i listan under [Miljökonfiguration](service-fabric-cluster-standalone-deployment-preparation.md) för felsökning. 

Det här skriptet kan köras på en dator som har administratörsåtkomst till alla datorer som listas som noder i klustret konfigurationsfilen. Den dator som skriptet körs på behöver inte vara en del av klustret.

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

Den här konfigurationen testning modulen validerar för närvarande inte säkerhetskonfigurationen så detta måste göras oberoende av varandra.  

> [!NOTE]
> Vi ständigt gör förbättringar för att göra den här modulen mer robust, så som du tror inte om det är felaktig eller saknas för närvarande fångas av TestConfiguration, meddela oss via vår [stöder kanaler](https://docs.microsoft.com/azure/service-fabric/service-fabric-support).   
> 
> 

## <a name="next-steps"></a>Nästa steg
* [Skapa ett fristående kluster som körs på Windows Server](service-fabric-cluster-creation-for-windows-server.md)
