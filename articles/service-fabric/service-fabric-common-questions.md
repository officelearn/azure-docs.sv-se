---
title: Vanliga frågor om Microsoft Azure Service Fabric | Microsoft Docs
description: Vanliga frågor om Service Fabric och deras svar
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: ''
ms.assetid: 5a179703-ff0c-4b8e-98cd-377253295d12
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2017
ms.author: chackdan
ms.openlocfilehash: aa287111a9880911c7807734b566bf60f42db85e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58447791"
---
# <a name="commonly-asked-service-fabric-questions"></a>Vanliga frågor och svar om Service Fabric

Det finns många vanliga frågor om vad Service Fabric kan göra och hur den ska användas. Det här dokumentet beskriver många av dessa vanliga frågor och deras svar.

## <a name="cluster-setup-and-management"></a>Konfigurera och hantera kluster

### <a name="how-do-i-roll-back-my-service-fabric-cluster-certificate"></a>Hur jag återställa mitt certifikat för Service Fabric-kluster?

Återställa kräver alla uppgradering till ditt program hälsotillstånd felidentifiering innan dina Service Fabric-klusterkvorum genomför ändringen. allokerade ändringar kan bara återställas framåt. Eskalering engineer via kundsupport, eventuellt krävs återställa klustret, om en oövervakade stora certifikat ändringen har introducerats.  [Service Fabric-Programuppgradering](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade?branch=master) gäller [programuppgraderingsparametrar](https://review.docs.microsoft.com/azure/service-fabric/service-fabric-application-upgrade-parameters?branch=master), och levererar noll avbrottstid uppgradera löftet.  Efter vårt rekommenderade program Uppgraderingsläge övervakade, automatisk förloppet via uppdateringsdomäner baseras på hälsokontroller skickar, löpande tillbaka automatiskt vid uppdatering av standardtjänst misslyckas.
 
Om klustret är fortfarande att använda egenskapen klassiska certifikatets tumavtryck i Resource Manager-mallen, rekommenderar vi du [ändra kluster från tumavtrycket för certifikatet till nätverksnamn](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-change-cert-thumbprint-to-cn), för att utnyttja moderna hemligheter hanteringsfunktioner.

### <a name="can-i-create-a-cluster-that-spans-multiple-azure-regions-or-my-own-datacenters"></a>Kan jag skapa ett kluster som sträcker sig över flera Azure-regioner eller mitt eget Datacenter?

Ja. 

Kärnan i Service Fabric-klusterteknik kan användas för att kombinera datorer som körs var som helst i världen, så länge som de har nätverksanslutning till varandra. Bygga och köra sådant kluster kan dock vara komplicerat.

Om du är intresserad av att det här scenariot kan vi uppmanar dig att komma i kontakt antingen via den [Service Fabric GitHub-Problemlista](https://github.com/azure/service-fabric-issues) eller via supportrepresentant för att få ytterligare hjälp. Service Fabric-teamet arbetar för att tillhandahålla ytterligare tydlighet, vägledning och rekommendationer för det här scenariot. 

Några saker som du bör tänka på: 

1. Resurs för Service Fabric-kluster i Azure är idag, regional eftersom VM-skalningsuppsättningen anger att klustret är byggt på. Det innebär att du kan förlora möjligheten att hantera klustret via Azure Resource Manager eller Azure-portalen vid regionala fel. Detta kan hända även om klustret är igång och du skulle kunna interagera med den direkt. Dessutom erbjuder Azure idag inte möjlighet att aktivera ett enda virtuellt nätverk som kan användas i olika regioner. Det innebär att ett kluster för flera regioner i Azure kräver antingen [offentliga IP-adresser för varje virtuell dator i VM Scale Sets](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine) eller [Azure VPN gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Dessa nätverk alternativ har olika påverkan på kostnader, prestanda, och att vissa grad programdesign, så noggrann analys och planering krävs innan skapa sådan miljö.
2. Underhåll, hantering och övervakning av dessa virtuella datorer kan bli komplicerad, särskilt när omfatta _typer_ över miljöer, till exempel mellan olika molnleverantörer eller mellan lokala resurser och Azure. Försiktighet måste vidtas för att se till att uppgraderingar, övervakning, hantering och diagnostik förstås både klustret och programmen innan du kör produktionsarbetsbelastningar i en sådan miljö. Om du redan har erfarenhet lösa dessa problem i Azure eller i ditt eget datacenter, är det troligt att lösningarna samma kan användas när att bygga ut eller som kör Service Fabric-klustret. 

### <a name="do-service-fabric-nodes-automatically-receive-os-updates"></a>Tar emot Service Fabric-noder automatiskt OS-uppdateringar?

Du kan använda [VM Scale ställa in automatisk OS uppdateringar](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade) allmänt tillgänglig funktion i dag.

För kluster som inte körs i Azure, har vi [tillhandahålls ett program](service-fabric-patch-orchestration-application.md) att korrigera operativsystem under dina Service Fabric-noder.

### <a name="can-i-use-large-virtual-machine-scale-sets-in-my-sf-cluster"></a>Kan jag använda stora VM-skalningsuppsättningar i mitt SF-kluster? 

**Kort svar** – Nej 

**Svara på länge** – även om stora VM-skalningsuppsättningar kan du skala en VM scale Sets upp till 1 000 instanser av virtuella datorer, sker detta med hjälp av Placeringsgrupper (PGs). Feldomäner (fd) och uppgraderingsdomäner (ud) är endast konsekventa i placering grupp Service fabric använder sig FD och ud för att fatta beslut om placering av dina service repliker/Service-instanser. Eftersom FD och ud är jämförbara endast i en placeringsgrupp är kan inte SF använda den. Exempelvis kan VM1 i SG1 har en topologi av FD = 0 och VM9 i SG2 har en topologi av FD = 4, det innebär inte att VM1 och VM2 finns på två olika maskinvara rack, därför SF kan inte använda FD-värden i det här fallet att fatta beslut om placeringen.

Det finns andra problem med stor VM-skalningsuppsättningar, som om bristen på nivå 4-ladda stöd för belastningsutjämning. Det finns [information om stora skalningsuppsättningar](../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md)



### <a name="what-is-the-minimum-size-of-a-service-fabric-cluster-why-cant-it-be-smaller"></a>Vad är den minsta storleken på Service Fabric-kluster? Varför kan det vara mindre?

Den minsta storleken som stöds för Service Fabric-kluster som kör produktionsarbetsbelastningar är fem noder. För scenarier för utveckling stöder vi en nod (optimerad för snabb utvecklingsupplevelse i Visual Studio) och kluster med fem noder.

Vi kräver ett produktionskluster ha minst 5 noder på grund av följande tre orsaker:
1. Även om inga användare-tjänsterna körs, körs Service Fabric-kluster en uppsättning tillståndskänsliga systemtjänster, inklusive namngivningstjänsten och redundans manager-tjänsten. Tjänsterna system är nödvändiga för att klustret ska fortsätta att fungera.
2. Vi prioriterar alltid en replik av en tjänst per nod, så klusterstorlek är den övre gränsen för antalet repliker som en tjänst (faktiskt en partition) kan ha.
3. Eftersom en uppgradering av klustret kommer att få ned minst en nod, vill vi ha en buffert på minst en nod, därför vill vi ett produktionskluster ha minst två noder *dessutom* till minst. Minst är kvorum storleken på en systemtjänst som beskrivs nedan.  

Vi vill att klustret ska vara tillgängliga om samtidiga fel med två noder. Systemtjänster måste vara tillgänglig för ett Service Fabric-kluster ska vara tillgängliga. Tillståndskänsliga systemtjänster som namntjänst och redundans manager-tjänsten, som att spåra vilka tjänster som har distribuerats till klustret och där de är för närvarande finns, beror på stark konsekvens. Den stark konsekvensen i sin tur är beroende av möjligheten att hämta en *kvorum* Services-tillståndet för de angivna uppdateringar, där ett kvorum representerar en strikt majoritet av repliker (N/2 + 1) för en viss tjänst. Så om vi vill vara motståndskraftiga mot samtidiga förlust av två noder (alltså samtidiga förlust av två repliker på en systemtjänst), vi måste ha ClusterSize - QuorumSize > = 2, vilket tvingar den minsta storleken är fem. För att se som klustret har N noder och det finns N replikeringar av en system-tjänst – en på varje nod. Kvorum-storleken för en systemtjänst är (N/2 + 1). Ovanstående ojämlikhet ut N - (N/2 + 1) > = 2. Det finns två fall att tänka på: när N är jämnt och när N är udda. Om N är jämnt, exempelvis N = 2\*m där m > = 1, 2 ojämlikhet ut\*m - (2\*m/2 + 1) > = 2 eller m > = 3. Minimum för N är 6 och som kan uppnås när m = 3. Å andra sidan, om N är udda, säger N = 2\*m + 1 var m > = 1, 2 ojämlikhet ut\*m + 1 – ((2\*m + 1) / 2 + 1) > = 2 eller 2\*m + 1 – (m + 1) > = 2 eller m > = 2. Minimum för N är 5 och som kan uppnås när m = 2. Därför bland alla värden i N som uppfyller ojämlikhet ClusterSize - QuorumSize > = 2, vilket är 5.

Observera att i argumentet ovan har vi antas att varje nod har en replik av en system-tjänst, alltså kvorum storleken beräknas baserat på antalet noder i klustret. Men genom att ändra *TargetReplicaSetSize* vi kan göra kvorum storleken mindre än (N / 2 + 1) som kan ge intryck av att vi kan har ett kluster som är mindre än 5 noder och fortfarande har 2 extra noder ovan storleken kvorum. Till exempel i ett kluster med 4 noder om vi ger TargetReplicaSetSize till 3, kvorum storleken utifrån TargetReplicaSetSize är (3/2 + 1) eller 2, så vi har ClusterSize - QuorumSize = 4-2 > = 2. Men vi kan inte garantera att system-tjänsten är tillgänglig med eller över kvorum om vi förlorar eventuella par noder samtidigt, kan det att de två noderna som vi förlorar som är värd för två repliker, så att systemtjänsten hamnar i förlorar kvorum (med endast en enskild replik till vänster) en ND blir otillgänglig.

Mot den bakgrunden, låt oss nu undersöka några möjliga klusterkonfigurationer:

**En nod**: det här alternativet ger inte hög tillgänglighet eftersom förlusten av en enda nod för märks innebär förlust av hela klustret.

**Två noder**: ett kvorum för en tjänst som distribueras över två noder (N = 2) är 2 (2/2 + 1 = 2). När en enskild replik tappas bort, går det inte att skapa ett kvorum. Eftersom utför en uppgradering av tjänsten kräver att tillfälligt ta ned en replik, men detta är inte en användbar konfiguration.

**Tre noder**: med tre noder (N = 3) kan kravet på att skapa ett kvorum är fortfarande två noder (3/2 + 1 = 2). Det innebär att du kan förlora en enskild nod och fortfarande behålla kvorum, men samtidigt fel hos två noder kommer att öka systemtjänster i förlorar kvorum och kommer att orsaka klustret blir otillgänglig.

**Fyra noder**: med fyra noder (N = 4) kravet på att skapa ett kvorum är tre noder (4/2 + 1 = 3). Det innebär att du kan förlora en enskild nod och fortfarande behålla kvorum, men samtidigt fel hos två noder kommer att öka systemtjänster i förlorar kvorum och kommer att orsaka klustret blir otillgänglig.

**Fem noder**: med fem noder (N = 5) kravet på att skapa ett kvorum är fortfarande tre noder (5/2 + 1 = 3). Det innebär att du kan förlora två noder på samma gång och fortfarande behålla kvorum för systemtjänster.

För arbetsbelastningar för produktion, måste du vara motståndskraftiga mot samtidiga fel på minst två noder (till exempel en på grund av uppgradering av klustret, en på grund av andra orsaker), så att fem noder som krävs.

### <a name="can-i-turn-off-my-cluster-at-nightweekends-to-save-costs"></a>Kan jag inaktivera mitt kluster på natten/helger för att spara kostnader?

I allmänhet Nej. Service Fabric lagrar tillstånd på lokala differentierande diskar, vilket innebär att om den virtuella datorn flyttas till en annan värd, inte flyttar data med den. Under normal drift, som är inte ett problem eftersom den nya noden är online uppdaterade på andra noder. Men om du stoppar alla noder och starta sedan om dem senare, finns det en betydande risk som de flesta av noderna som börjar på nya värdar och gör det går inte att återställa systemet.

Om du vill skapa kluster för att testa ditt program innan det distribueras rekommenderar vi att du skapar dessa kluster dynamiskt som en del av din [pipeline för kontinuerlig integrering/kontinuerlig distribution](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).


### <a name="how-do-i-upgrade-my-operating-system-for-example-from-windows-server-2012-to-windows-server-2016"></a>Hur uppgraderar jag min operativsystemet (till exempel från Windows Server 2012 till Windows Server 2016)?

Medan vi arbetar med en bättre upplevelse idag, är du ansvarig för uppgraderingen. Du måste uppgradera den OS-avbildningen på de virtuella datorerna i klustret en virtuell dator i taget. 

### <a name="can-i-encrypt-attached-data-disks-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan jag kryptera anslutna datadiskar i en klusternodstyp (virtual machine scale Sets)?
Ja.  Mer information finns i [skapa ett kluster med anslutna datadiskar](../virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks.md#create-a-service-fabric-cluster-with-attached-data-disks), [kryptera diskar (PowerShell)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-ps.md), och [kryptera diskar (CLI)](../virtual-machine-scale-sets/virtual-machine-scale-sets-encrypt-disks-cli.md).

### <a name="can-i-use-low-priority-vms-in-a-cluster-node-type-virtual-machine-scale-set"></a>Kan jag använda lågprioriterade virtuella datorer i en klusternodstyp (virtual machine scale Sets)?
Nej. Lågprioriterade virtuella datorer stöds inte. 

### <a name="what-are-the-directories-and-processes-that-i-need-to-exclude-when-running-an-anti-virus-program-in-my-cluster"></a>Vad är kataloger och processer som jag behöver ska undantas när du kör ett antivirusprogram i mitt kluster?

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
 
### <a name="how-can-my-application-authenticate-to-keyvault-to-get-secrets"></a>Hur kan mitt program ska autentiseras mot KeyVault för att hämta hemligheter?
Här följer innebär för ditt program att hämta autentiseringsuppgifter för att autentisera till KeyVault:

A. Under dina program build/förpackning jobb, kan du hämta ett certifikat till din SF-app-paket för data och används för att autentisera till KeyVault.
B. För VM-skalningsuppsättningen MSI aktiverat värdar, kan du utveckla en enkel PowerShell SetupEntryPoint för din SF-app att hämta [en åtkomsttoken från MSI-slutpunkten](https://docs.microsoft.com/azure/active-directory/managed-service-identity/how-to-use-vm-token), och sedan [hämta dina hemligheter från KeyVault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Get-AzureKeyVaultSecret)

## <a name="application-design"></a>Programmets Design

### <a name="whats-the-best-way-to-query-data-across-partitions-of-a-reliable-collection"></a>Vad är det bästa sättet att köra frågor mot data över partitioner i en tillförlitlig samling?

Tillförlitliga samlingar är vanligtvis [partitionerade](service-fabric-concepts-partitioning.md) att skala ut för bättre prestanda och dataflöde. Det innebär att tillståndet för en viss tjänst kan spridas till tiotusen datorer. Om du vill utföra åtgärder utifrån den fullständiga datauppsättningen, har du några alternativ:

- Skapa en tjänst som frågar alla partitioner i en annan tjänst för att dra in nödvändiga data.
- Skapa en tjänst som kan ta emot data från alla partitioner i en annan tjänst.
- Skicka data regelbundet från varje tjänst till en extern lagring. Den här metoden är endast om de frågor som du utför inte är en del av affärslogiken core.


### <a name="whats-the-best-way-to-query-data-across-my-actors"></a>Vad är det bästa sättet att köra frågor mot data över min aktörer?

Aktörer är avsedda att vara oberoende enheter av tillstånd och göra beräkningar, så det inte rekommenderas att utföra bred frågor av aktörstillstånd vid körning. Om du har ett behov av att fråga i den fullständiga uppsättningen aktörstillstånd, bör du antingen:

- Ersätter din aktörstjänster med tillståndskänsliga reliable services så att antalet nätverk förfrågningar om att samla in alla data från antalet aktörer att antalet partitioner i din tjänst.
- Designa din aktörer att regelbundet skicka deras tillstånd till en extern lagring för enklare frågor. Som är ovan, med den här metoden bara användbara om de frågor som du utför inte krävs för din runtime-beteende.

### <a name="how-much-data-can-i-store-in-a-reliable-collection"></a>Hur mycket data kan jag lagra i en tillförlitlig samling?

Tillförlitliga tjänster partitioneras vanligtvis, så att den mängd som du kan lagra begränsas bara av antalet datorer som du har i klustret och mängden tillgängligt minne på dessa datorer.

Anta exempelvis att du har en tillförlitlig samling i en tjänst med 100 partitioner och 3 repliker lagra objekt som genomsnittlig 1 kb stora. Anta nu att du har ett 10 dator-kluster med 16gb minne per dator. För enkelhetens skull och för att vara konservativ, förutsätts att operativsystemet och systemtjänster, Service Fabric-körningen och dina tjänster kan du använda 6gb på det lämnar 10gb som är tillgängliga per dator eller 100 gb för klustret.

Med tanke på att varje objekt måste vara lagrade tre gånger (en primär och två repliker), du skulle ha tillräckligt med minne för cirka 35 miljoner objekt i samlingen när vid full kapacitet. Vi rekommenderar dock att vara motståndskraftiga mot samtidiga förlusten av en fel domän och en uppgraderingsdomän som representerar ungefär 1/3 av kapacitet och kan minska antalet till ungefär 23 miljoner.

Observera att den här beräkningen förutsätter också:

- Att distributionen av data över partitionerna är ungefär uniform eller att du rapporterar inläsningsmåtten till Cluster Resource Manager. Läser in saldo som baseras på replikantal som standard Service Fabric. I föregående exempel, som placerar 10 primära repliker och 20 sekundära repliker på varje nod i klustret. Som fungerar bra för belastningen jämnt fördelade mellan partitionerna. Om belastningen inte är ännu, måste du rapportera belastning så att Resource Manager kan pack mindre repliker tillsammans och tillåta större repliker förbruka mer minne på en enskild nod.

- Att tillförlitlig tjänsten i fråga är bara en lagra tillståndet i klustret. Eftersom du kan distribuera flera tjänster till ett kluster, måste du vara uppmärksam på resurser att var och en måste köra och hantera dess tillstånd.

- Att växande inte eller minska storleken på själva klustret. Om du lägger till flera datorer, Service Fabric balansera om dina repliker för att använda den ytterligare kapaciteten tills antalet datorer överskrider antalet partitioner i din tjänst, eftersom en enskild replik inte kan omfatta datorer. Däremot om du minskar storleken på klustret genom att ta bort datorer dina repliker är förpackade tätare och har mindre totala kapaciteten.

### <a name="how-much-data-can-i-store-in-an-actor"></a>Hur mycket data kan jag lagra i en aktör?

Precis som med reliable services måste begränsas endast mängden data som du kan lagra i en aktörstjänsten av de totalt diskutrymme och tillgängligt minne mellan noderna i klustret. Enskilda aktörer är mest effektiva när de används för att kapsla in en liten mängd tillstånd och associerade affärslogik. Som en allmän regel ska en enskild aktör ha tillstånd som mäts i kilobyte.

## <a name="other-questions"></a>Andra frågor

### <a name="how-does-service-fabric-relate-to-containers"></a>Hur relaterar Service Fabric till behållare?

Behållare erbjuder ett enkelt sätt att paketet tjänster och deras beroenden, så att de körs konsekvent i alla miljöer och kan fungera i isolerad miljö på en enda dator. Service Fabric ger ett sätt att distribuera och hantera tjänster, inklusive [tjänster som har paketerats i en behållare](service-fabric-containers-overview.md).

### <a name="are-you-planning-to-open-source-service-fabric"></a>Planerar du att öppen källkod Service Fabric?

Vi har öppen källkod delar av Service Fabric ([tillförlitliga tjänster framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [tillförlitliga aktörer framework](https://github.com/Azure/service-fabric-services-and-actors-dotnet), [ASP.NET Core-integrering bibliotek](https://github.com/Azure/service-fabric-aspnetcore), [ Service Fabric Explorer](https://github.com/Azure/service-fabric-explorer), och [Service Fabric CLI](https://github.com/Azure/service-fabric-cli)) på GitHub och acceptera community-bidrag till projekt. 

Vi [tillkännagav nyligen](https://blogs.msdn.microsoft.com/azureservicefabric/2018/03/14/service-fabric-is-going-open-source/) att vi planerar att öppen källkod Service Fabric-körningen. Nu har vi den [Service Fabric-repon](https://github.com/Microsoft/service-fabric/) upp på GitHub med Linux skapa och testa verktyg, vilket innebär att du kan klona lagringsplatsen, skapa Service Fabric för Linux, kör grundläggande tester, öppna frågor och skicka in pull-begäranden. Vi arbetar hårt för att hämta Windows-kompileringsmiljö migreras under, tillsammans med en fullständig CI-miljö.

Följ den [Service Fabric-bloggen](https://blogs.msdn.microsoft.com/azureservicefabric/) för mer information som de har meddelats.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [viktiga begrepp för Service Fabric](service-fabric-technical-overview.md) och [bästa praxis](service-fabric-best-practices-overview.md)
