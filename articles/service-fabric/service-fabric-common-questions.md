---
title: Vanliga frågor om Microsoft Azure Service Fabric
description: Vanliga frågor och svar om Service Fabric, inklusive funktioner, användningsfall och vanliga scenarier.
ms.topic: troubleshooting
ms.date: 08/18/2017
ms.author: pepogors
ms.openlocfilehash: bf61858b446c1ac6d4a0210571fffaa721ad0166
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254890"
---
# <a name="commonly-asked-service-fabric-questions"></a>Vanliga frågor och svar om Service Fabric

Det finns många vanliga frågor om vad Service Fabric kan göra och hur den ska användas. Detta dokument täcker många av dessa vanliga frågor och deras svar.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cluster-setup-and-management"></a>Installation och hantering av kluster

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hur återställer jag mitt Service Fabric-klustercertifikat?

Om du återställar alla uppgraderingar till ditt program krävs identifiering av hälsofel innan service fabric-klustrets kvorum begår ändringen. genomförda ändringar kan endast rullas framåt. Eskaleringstekniker via kundtjänsttjänster kan krävas för att återställa klustret om en oövervakad brottscertifikatändring har införts.  [Service Fabric programuppgradering](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) tillämpar [parametrar för uppgradering av program](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master)och ger noll uppgraderingslöfte om driftstopp.  Efter vårt rekommenderade programuppgradering övervakas läge, automatisk förlopp genom uppdatera domäner är baserat på hälsokontroller passerar, rulla tillbaka automatiskt om uppdateringen av en standardtjänst misslyckas.
 
Om klustret fortfarande utnyttjar den klassiska egenskapen Certifikattumavtryck i mallen Resource Manager rekommenderar vi att du [ändrar kluster från certifikattumavtryck till vanligt namn](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn)för att utnyttja moderna hemligheterhanteringsfunktioner.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Kan jag skapa ett kluster som sträcker sig över flera Azure-regioner eller mina egna datacenter?

Ja. 

Kärntekniken för Service Fabric-kluster kan användas för att kombinera maskiner som körs var som helst i världen, så länge de har nätverksanslutning till varandra. Det kan dock vara komplicerat att skapa och driva ett sådant kluster.

Om du är intresserad av det här scenariot rekommenderar vi att du kontaktar dig antingen via [Service Fabric GitHub-ärendelistan](https://github.com/azure/service-fabric-issues) eller via din supportrepresentant för att få ytterligare vägledning. Service Fabric-teamet arbetar för att ge ytterligare tydlighet, vägledning och rekommendationer för det här scenariot. 

Några saker att tänka på: 

1. Service Fabric-klusterresursen i Azure är regional idag, liksom de skalningsuppsättningar för virtuella datorer som klustret är byggt på. Det innebär att i händelse av ett regionalt fel kan du förlora möjligheten att hantera klustret via Azure Resource Manager eller Azure-portalen. Detta kan inträffa även om klustret körs och du kan interagera med det direkt. Dessutom erbjuder Azure idag inte möjligheten att ha ett enda virtuellt nätverk som kan kan tillämpas i olika regioner. Det innebär att ett kluster med flera regioner i Azure kräver antingen [offentliga IP-adresser för varje virtuell dator i vm-skalningsuppsättningarna](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) eller [Azure VPN Gateways](../vpn-gateway/vpn-gateway-about-vpngateways.md). Dessa nätverksval har olika inverkan på kostnader, prestanda och i viss mån programdesign, så noggrann analys och planering krävs innan du står upp en sådan miljö.
2. Underhåll, hantering och övervakning av dessa datorer kan bli komplicerat, särskilt när de sträcker sig över _olika typer_ av miljöer, till exempel mellan olika molnleverantörer eller mellan lokala resurser och Azure. Du måste se till att uppgraderingar, övervakning, hantering och diagnostik förstås för både klustret och programmen innan produktionsarbetsbelastningar körs i en sådan miljö. Om du redan har erfarenhet av att lösa dessa problem i Azure eller i dina egna datacenter är det troligt att samma lösningar kan användas när du skapar eller kör ditt Service Fabric-kluster. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Får Service Fabric-noder automatiskt OS-uppdateringar?

Du kan använda [funktionen För automatisk os-avbildningsuppdatering för virtuell dators skalauppsättning](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) idag.

För kluster som INTE körs i Azure har vi [tillhandahållit ett program](service-fabric-patch-orchestration-application.md) för att korrigera operativsystemen under dina Service Fabric-noder.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Kan jag använda stora skalningsuppsättningar för virtuella datorer i mitt SF-kluster? 

**Kort svar** - Nej. 

**Långt svar** - Även om de stora skalningsuppsättningarna för virtuella datorer gör att du kan skala en skala för virtuella datorer som ställts in till 1 000 VM-instanser, gör det det med hjälp av placeringsgrupper (PGs). Feldomäner (FD: er) och uppgradera domäner (UDs) är endast konsekventa inom en placeringsgrupp Tjänst fabric använder FDs och UDs för att fatta placeringsbeslut av dina tjänstrepliker / tjänstinstanser. Eftersom FD och UDs är jämförbara endast inom en placeringsgrupp kan SF inte använda den. Om VM1 i PG1 till exempel har en topologi för FD=0 och VM9 i PG2 har en topologi för FD=4, betyder det inte att VM1 och VM2 finns på två olika maskinvaruställ, varför SF inte kan använda FD-värdena i det här fallet för att fatta placeringsbeslut.

Det finns andra problem med stora virtuella datorskalauppsättningar för närvarande, som bristen på nivå 4 belastningsutjämningsstöd. Se för [mer information om storskaliga uppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Vilken är den minsta storleken på ett Service Fabric-kluster? Varför kan den inte vara mindre?

Den minsta storleken som stöds för ett Service Fabric-kluster som kör produktionsarbetsbelastningar är fem noder. För dev-scenarier stöder vi en nod (optimerad för snabb utveckling i Visual Studio) och fem nodkluster.

Vi kräver att ett produktionskluster har minst 5 noder på grund av följande tre orsaker:
1. Även om inga användartjänster körs kör ett Service Fabric-kluster en uppsättning tillståndskänsliga systemtjänster, inklusive namngivningstjänsten och redundanshanterarens tjänst. Dessa systemtjänster är nödvändiga för att klustret ska kunna fortsätta att fungera.
2. Vi placerar alltid en replik av en tjänst per nod, så klusterstorlek är den övre gränsen för antalet repliker som en tjänst (faktiskt en partition) kan ha.
3. Eftersom en klusteruppgradering kommer att få ner minst en nod, vill vi ha en buffert på minst en nod, därför vill vi att ett produktionskluster ska ha minst två noder *utöver* det minsta. Det minsta är beslutförhetsstorleken för en systemtjänst som förklaras nedan.  

Vi vill att klustret ska vara tillgängligt inför samtidiga fel på två noder. För att ett Service Fabric-kluster ska vara tillgängligt måste systemtjänsterna vara tillgängliga. Tillståndskänsliga systemtjänster som namngivningstjänst och redundanshanterare, som spårar vilka tjänster som har distribuerats till klustret och var de för närvarande finns, beror på stark konsekvens. Denna starka konsekvens beror i sin tur på förmågan att förvärva *beslutförhet* för en viss uppdatering av tillståndet för dessa tjänster, där beslutförhet utgör en strikt majoritet av replikerna (N/2 +1) för en viss tjänst. Således om vi vill vara motståndskraftiga mot samtidig förlust av två noder (alltså samtidig förlust av två kopior av ett system tjänst), måste vi ha ClusterSize - QuorumSize >= 2, vilket tvingar den minsta storleken vara fem. Tänk på att klustret har N-noder och det finns N-repliker av en systemtjänst – en på varje nod. Kvorumstorleken för en systemtjänst är (N/2 + 1). Ovanstående ojämlikhet ser ut som N - (N/2 + 1) >= 2. Det finns två fall att tänka på: när N är jämn och när N är udda. Om N är jämnt, säg\*N = 2 m där m >=\*1, ser\*ojämlikheten ut 2 m - (2 m/2 + 1) >= 2 eller m >= 3. Minimivärdet för N är 6 och det uppnås när m = 3. Å andra sidan, om N är udda,\*säger N = 2 m +1 där m >\*= 1, ser\*ojämlikheten ut 2 m+1 - ( (2 m+1)/2 + 1 ) >= 2 eller 2\*m+1 - (m+1) >= 2 eller m >= 2. Minimivärdet för N är 5 och det uppnås när m = 2. Därför, bland alla värden i N som uppfyller ojämlikheten ClusterSize - QuorumSize >= 2, är det minsta 5.

Observera att i argumentet ovan har vi antagit att varje nod har en replik av en systemtjänst, vilket innebär att kvorumstorleken beräknas baserat på antalet noder i klustret. Men genom att ändra *TargetReplicaSetSize* kan vi göra kvorumstorleken mindre än (N/2+1) vilket kan ge intryck av att vi kunde ha ett kluster som är mindre än 5 noder och fortfarande har 2 extra noder ovanför kvorumstorleken. Till exempel, i en 4 nod kluster, om vi ställer in TargetReplicaSetSize till 3, kvorum storlek baserat på TargetReplicaSetSize är (3/ 2 + 1) eller 2, alltså har vi ClusterSize - QuorumSize = 4-2 >= 2. Vi kan dock inte garantera att systemtjänsten kommer att vara på eller över kvorum om vi förlorar några par noder samtidigt, kan det vara så att de två noder vi förlorade var värd två repliker, så att systemtjänsten kommer att gå in kvorum förlust (med bara en enda replik kvar) och kommer att bli otillgänglig.

Med den bakgrunden ska vi undersöka några möjliga klusterkonfigurationer:

**En nod**: det här alternativet ger inte hög tillgänglighet eftersom förlusten av den enda noden av någon anledning innebär att hela klustret går förlorade.

**Två noder**: kvorum för en tjänst som distribueras över två noder (N = 2) är 2 (2/2 + 1 = 2). När en enda replik går förlorad är det omöjligt att skapa ett kvorum. Eftersom utföra en tjänst uppgradering kräver att tillfälligt ta ner en replik, är detta inte en användbar konfiguration.

**Tre noder**: med tre noder (N=3), kravet att skapa ett kvorum är fortfarande två noder (3/2 + 1 = 2). Det innebär att du kan förlora en enskild nod och fortfarande upprätthålla kvorum, men samtidiga fel på två noder kommer att driva systemtjänsterna till kvorumförlust och orsaka att klustret blir otillgängligt.

**Fyra noder**: med fyra noder (N=4) är kravet på att skapa ett kvorum tre noder (4/2 + 1 = 3). Det innebär att du kan förlora en enskild nod och fortfarande upprätthålla kvorum, men samtidiga fel på två noder kommer att driva systemtjänsterna till kvorumförlust och orsaka att klustret blir otillgängligt.

**Fem noder**: med fem noder (N= 5), kravet att skapa ett kvorum är fortfarande tre noder (5/2 + 1 = 3). Det innebär att du kan förlora två noder samtidigt och ändå upprätthålla kvorum för systemtjänsterna.

För produktionsarbetsbelastningar måste du vara motståndskraftig mot samtidiga fel på minst två noder (till exempel en på grund av klusteruppgradering, en på grund av andra orsaker), så fem noder krävs.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kan jag stänga av mitt kluster på natten/helgerna för att spara kostnader?

Nej. I allmänhet inte. Service Fabric lagrar tillstånd på lokala, tillfälliga diskar, vilket innebär att om den virtuella datorn flyttas till en annan värd, flyttas inte data med den. Vid normal drift är det inte ett problem eftersom den nya noden uppdateras av andra noder. Men om du stoppar alla noder och startar om dem senare finns det en betydande möjlighet att de flesta noder börjar på nya värdar och gör att systemet inte kan återställas.

Om du vill skapa kluster för att testa programmet innan det distribueras rekommenderar vi att du dynamiskt skapar dessa kluster som en del av din pipeline för [kontinuerlig integrering/kontinuerlig distribution](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hur uppgraderar jag mitt operativsystem (till exempel från Windows Server 2012 till Windows Server 2016)?

Medan vi arbetar på en förbättrad upplevelse, idag är du ansvarig för uppgraderingen. Du måste uppgradera OS-avbildningen på de virtuella datorerna i klustret en virtuell dator i taget. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan jag kryptera anslutna datadiskar i en klusternodtyp (skaluppsättning för virtuella datorer)?
Ja.  Mer information finns i [Skapa ett kluster med anslutna datadiskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks) och Azure Disk Encryption för [skalningsuppsättningar för virtuella datorer](../virtual-machine-scale-sets/disk-encryption-overview.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan jag använda virtuella datorer med låg prioritet i en klusternodtyp (skaluppsättning för virtuell dator)?
Nej. Virtuella datorer med låg prioritet stöds inte. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Vilka kataloger och processer måste jag utesluta när jag kör ett antivirusprogram i mitt kluster?

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
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Hur kan mitt program autentisera till KeyVault för att få hemligheter?
Följande är medel för ditt program att hämta autentiseringsuppgifter för autentisering till KeyVault:

A. Under programbygg-/förpackningsjobbet kan du hämta ett certifikat till SF-appens datapaket och använda det för att autentisera till KeyVault.
B. För MSI-aktiverade msi-värdar för virtuell datorskala kan du utveckla en enkel PowerShell SetupEntryPoint för SF-appen för att hämta [en åtkomsttoken från MSI-slutpunkten](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token)och sedan [hämta dina hemligheter från KeyVault](/powershell/module/azurerm.keyvault/get-azurekeyvaultsecret).

## <a name="application-design"></a>Programdesign

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Vilket är det bästa sättet att fråga data över partitioner i en tillförlitlig samling?

Tillförlitliga samlingar [partitioneras](service-fabric-concepts-partitioning.md) vanligtvis för att aktivera skala ut för bättre prestanda och dataflöde. Det innebär att staten för en viss tjänst kan spridas över tiotals eller hundratals maskiner. Om du vill utföra åtgärder över den fullständiga datauppsättningen har du några alternativ:

- Skapa en tjänst som frågar alla partitioner i en annan tjänst för att hämta nödvändiga data.
- Skapa en tjänst som kan ta emot data från alla partitioner i en annan tjänst.
- Skicka regelbundet data från varje tjänst till ett externt arkiv. Den här metoden är endast lämplig om de frågor du utför inte är en del av din kärnaffärslogik, eftersom det externa arkivets data är inaktuella.
- Du kan också lagra data som måste stödja frågor i alla poster direkt i ett datalager i stället för i en tillförlitlig samling. Detta eliminerar problemet med inaktuella data, men tillåter inte att fördelarna med tillförlitliga samlingar kan utnyttjas.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Vilket är det bästa sättet att fråga data mellan mina aktörer?

Aktörer är utformade för att vara oberoende enheter av tillstånd och beräkning, så det rekommenderas inte att utföra breda frågor av aktörstillstånd vid körning. Om du har ett behov av att fråga över hela uppsättningen aktörstillstånd bör du överväga antingen:

- Ersätta dina aktörstjänster med tillståndskänsliga tillförlitliga tjänster, så att antalet nätverksbegäranden för att samla in alla data från antalet aktörer till antalet partitioner i tjänsten.
- Utforma dina aktörer att regelbundet driva sitt tillstånd till ett externt arkiv för enklare frågor. Som ovan är den här metoden endast genomförbar om de frågor du utför inte krävs för din körning beteende.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Hur mycket data kan jag lagra i en tillförlitlig samling?

Tillförlitliga tjänster partitioneras vanligtvis, så det belopp du kan lagra begränsas endast av antalet datorer som du har i klustret och mängden minne som är tillgängligt på dessa datorer.

Anta till exempel att du har en tillförlitlig samling i en tjänst med 100 partitioner och 3 repliker, vilket lagrar objekt som i genomsnitt 1 kb i storlek. Anta nu att du har ett kluster på 10 datorer med 16 GB minne per dator. För enkelhet och för att vara konservativ, anta att operativsystemet och systemtjänster, Service Fabric runtime, och dina tjänster förbrukar 6GB av det, vilket ger 10GB tillgängliga per dator, eller 100 gb för klustret.

Med tanke på att varje objekt måste lagras tre gånger (en primär och två repliker), skulle du ha tillräckligt med minne för cirka 35 miljoner objekt i samlingen när du arbetar med full kapacitet. Vi rekommenderar dock att du är motståndskraftig mot samtidig förlust av en feldomän och en uppgraderingsdomän, vilket motsvarar ungefär 1/3 kapacitet, och skulle minska antalet till ungefär 23 miljoner.

Observera att denna beräkning också förutsätter:

- Att fördelningen av data mellan partitionerna är ungefär enhetlig eller att du rapporterar inläsningsmått till Cluster Resource Manager. Som standard läser Service Fabric in balans baserat på replikantal. I föregående exempel skulle det placera 10 primära repliker och 20 sekundära repliker på varje nod i klustret. Det fungerar bra för belastning som är jämnt fördelad över partitionerna. Om inläsningen inte är jämn måste du rapportera inläsning så att Resource Manager kan packa mindre repliker tillsammans och tillåta större repliker att förbruka mer minne på en enskild nod.

- Att den tillförlitliga tjänsten i fråga är den enda som lagrar tillståndet i klustret. Eftersom du kan distribuera flera tjänster till ett kluster måste du vara uppmärksam på de resurser som var och en behöver för att köra och hantera dess tillstånd.

- Att klustret i sig inte växer eller krymper. Om du lägger till fler datorer balanserar Service Fabric om replikerna för att utnyttja den ytterligare kapaciteten tills antalet datorer överskrider antalet partitioner i tjänsten, eftersom en enskild replik inte kan spänna över datorer. Om du däremot minskar storleken på klustret genom att ta bort datorer packas replikerna hårdare och har mindre total kapacitet.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Hur mycket data kan jag lagra i en skådespelare?

Precis som med tillförlitliga tjänster begränsas mängden data som du kan lagra i en aktörstjänst endast av det totala diskutrymmet och minnet som är tillgängligt för noderna i klustret. Enskilda aktörer är dock mest effektiva när de används för att kapsla in en liten mängd tillstånd och tillhörande affärslogik. Som en allmän regel bör en enskild aktör ha tillstånd som mäts i kilobyte.

## <a name="other-questions"></a>Övriga frågor

### <a name="how-does-service-fabric-relate-to-containers"></a>Hur relaterar Service Fabric till behållare?

Behållare erbjuder ett enkelt sätt att paketera tjänster och deras beroenden så att de körs konsekvent i alla miljöer och kan fungera på ett isolerat sätt på en enda dator. Service Fabric erbjuder ett sätt att distribuera och hantera tjänster, inklusive [tjänster som har paketerats i en behållare](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Planerar du att service fabric med öppen källkod?

Vi har delar av Service Fabric med öppen källkod ([reliable services framework,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [reliable actors framework,](https://github.com/Azure/service-fabric-services-and-actors-dotnet) [ASP.NET Core integration libraries](https://github.com/Azure/service-fabric-aspnetcore), [Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer)och Service [Fabric CLI](https://github.com/Azure/service-fabric-cli)) på GitHub och accepterar samhällsbidrag till dessa projekt. 

Vi [meddelade nyligen](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) att vi planerar att öppen källkod Tjänsten Fabric runtime. Vid denna punkt har vi [Service Fabric repo](https://github.com/Microsoft/service-fabric/) upp på GitHub med Linux bygga och testa verktyg, vilket innebär att du kan klona repo, bygga Service Fabric för Linux, köra grundläggande tester, öppna frågor, och skicka pull förfrågningar. Vi arbetar hårt för att få Windows build-miljön migrerad över också, tillsammans med en komplett CI-miljö.

Följ [Service Fabric-bloggen](https://blogs.msdn.microsoft.com/azureservicefabric/) för mer information när de meddelas.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [grundläggande service fabric-koncept](service-fabric-technical-overview.md) och [metodtips](service-fabric-best-practices-overview.md)
