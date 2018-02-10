---
title: "Översikt över Azure Load Balancer Standard | Microsoft Docs"
description: "Översikt över Azure Load Balancer standardfunktioner"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: cf7be370ab0d79be9068534f0c43b88f454bc024
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Load Balancer Standard översikt över Azure (förhandsversion)

Azure Load Balancer Standard SKU och offentliga IP-Standard SKU kan tillsammans du skapa skalbar och tillförlitlig arkitekturer. Program som använder Load Balancer Standard kan dra nytta av nya funktioner. Låg latens och hög genomströmning skala är tillgängliga för miljontals flöden för alla TCP och UDP-program.

>[!NOTE]
> Load Balancer Standard-SKU är för närvarande under förhandsgranskning. Under förhandsgranskningen gör kanske funktionen inte samma grad av tillgänglighet och tillförlitlighet som viktiga funktioner som är i allmänhet tillgänglighet. Mer information finns i [de kompletterande villkoren för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Använd den allmänt tillgängliga [Load Balancer grundläggande SKU](load-balancer-overview.md) för produktion-tjänster. Att använda [tillgänglighet zoner Preview](https://aka.ms/availabilityzones) med den här förhandsversionen kräver en [separat anmälan](https://aka.ms/availabilityzones), förutom att registrera dig för belastningsutjämnaren [Standard preview](#preview-sign-up).

## <a name="why-use-load-balancer-standard"></a>Varför använda Load Balancer Standard?

Du kan använda Load Balancer Standard för en fullständig uppsättning virtuella datacenter. Liten skala distributioner till stora och komplexa flera zonen arkitekturer, Använd från Load Balancer Standard dra nytta av följande funktioner:

- [Företagsnivå](#enterprisescale) kan uppnås med Load Balancer Standard. Den här funktionen kan användas med en virtuell dator (VM)-instans i ett virtuellt nätverk, upp till 1 000 VM-instanser.

- [Nya diagnostiska insikter](#diagnosticinsights) hjälper dig att förstå, hantera och felsöka det här mycket viktig komponent i ditt virtuella datacenter. Använda Azure-Monitor (förhandsversion) för att visa, filtrera och gruppera nya flerdimensionella mått för kontinuerliga data sökväg hälsa mått. Övervaka dina data från frontend VM endpoint hälsoavsökningar, för TCP-anslutningsförsök och utgående anslutningar.

- [Nätverkssäkerhetsgrupper](#nsg) är nu krävs för VM-instans som är associerad med Load Balancer Standard SKU: er eller offentlig IP-Standard SKU: er. Nätverkssäkerhetsgrupper (NSG: er) ger förbättrad säkerhet för ditt scenario.

- [Portar för hög tillgänglighet ger hög tillförlitlighet](#highreliability) och skala för virtuella nätverksinstallationer (NVAs) och andra scenarier för programmet. Hög tillgänglighet portar belastningsutjämna alla portar på ett Azure interna ladda belastningsutjämnare (ILB) frontend till en pool av VM-instanser.

- [Utgående anslutningar](#outboundconnections) nu använda en ny allokering modell för källa Network Address Translation (SNAT) port som ger större flexibilitet och skala.

- [Läsa in belastningsutjämning Standard med tillgänglighet zoner](#availabilityzones) kan användas för att konstruera zonredundant och zonal arkitekturerna. Båda dessa arkitekturer kan innehålla mellan zon belastningsutjämning. Du kan uppnå redundans utan beroendet av DNS-poster. En IP-adress är zonredundant som standard.  En IP-adress kan nå någon virtuell dator i ett virtuellt nätverk i en region som är över alla zoner för tillgänglighet.


Du kan använda Load Balancer Standard antingen i en offentlig eller intern konfiguration till stöd för följande grundläggande scenarier:

- Belastningsutjämna inkommande trafik till Felfri backend-instanser.
- Port vidarebefordra inkommande trafik till en backend-instans.
- Översätta utgående trafik från en privat IP-adress i det virtuella nätverket till en offentlig IP-adress.

### <a name = "enterprisescale"></a>Företagsnivå

 Använd Load Balancer Standard att utforma din virtuella datacenter med hög prestanda och stöder alla TCP eller UDP-program. Använd fristående VM-instanser eller upp till 1 000 instanser av den virtuella datorn anger du i en backend-adresspool. Fortsätta att använda vidarebefordran av låg latens, hög genomströmning prestanda och skalning till miljontals flöden i en helt hanterad Azure-tjänster.
 
Load Balancer Standard kan vidarebefordra trafik till alla VM-instanser i ett virtuellt nätverk i en region. Backend-adresspool storlekar kan vara upp till 1 000 instanser med en kombination av följande VM-scenarier:

- Fristående virtuella datorer utan tillgänglighetsuppsättningar
- Fristående virtuella datorer med tillgänglighetsuppsättningar
- Anger virtuella skala upp till 1 000 instanser
- Flera virtuella skalningsuppsättningarna
- Överlappande av virtuella datorer och virtuella datorer

Det finns inte längre ett krav för tillgänglighetsuppsättningar. Du kan välja att använda tillgänglighetsuppsättningar för de fördelar som de tillhandahåller.

### <a name = "diagnosticinsights"></a>Diagnostiska insikter

Load Balancer Standard innehåller nya flerdimensionella diagnostik för offentliga och interna belastningsutjämnare konfigurationer. Dessa nya mått tillhandahålls via Azure-Monitor (förhandsversion) och använda alla relaterade funktioner, inklusive möjligheten att integrera med underordnade konsumenter.

| Mått | Beskrivning |
| --- | --- |
| VIP-tillgänglighet | Load Balancer Standard utför kontinuerligt datasökväg från inom en region till belastningsutjämnaren frontend ända till SDN stacken som har stöd för den virtuella datorn. Så länge felfri instanser förblir följer mätningen samma sökväg som ditt program belastningsutjämnad trafik. Datasökväg som används av dina kunder också valideras. Måttet är osynliga för ditt program och störa inte andra åtgärder.|
| DIP tillgänglighet | Läsa in Standard belastningsutjämnare använder en distribuerad hälsa avsökning av tjänst som övervakar dina program endpoint hälsa enligt inställningarna. Det här måttet ger en aggregering eller per slutpunkt filtrerad vy för varje enskild instans slutpunkt i belastningsutjämnaren poolen.  Du kan se hur belastningsutjämnaren visar hälsotillståndet för programmet som anges av din konfiguration för avsökning av hälsotillstånd.
| SYN-paket | Load Balancer Standard inte avsluta TCP-anslutningar och interagera med TCP eller UDP-paket-flöden. Flöden och deras handskakningar på är alltid mellan käll- och VM-instans. Om du vill felsöka bättre scenariet TCP-protokollet, kan du använda SYN paket räknare för att förstå hur många TCP-anslutning görs försök. Måttet rapporterar antalet TCP SYN-paket som tagits emot.|
| SNAT anslutningar | Load Balancer Standard rapporterar antalet utgående flöden masqueraded frontend offentliga IP-adress. SNAT portar är en icke förnybara resurs. Det här måttet kan ge en indikation på hur mycket tillämpningsprogrammet förlitar sig på SNAT för utgående flöden som har sitt ursprung.  Räknare för lyckade och misslyckade utgående SNAT flöden rapporteras och kan användas för att felsöka och förstå hälsotillståndet för din utgående flöden.|
| Räknare för byte | Load Balancer Standard rapporterar data bearbetas per frontend.|
| Paketet räknare | Load Balancer Standard rapporterar de paket som bearbetas per frontend.|

### <a name = "highreliability"></a>Hög tillförlitlighet

Konfigurera regler för att göra skala ditt program och mycket pålitlig för belastningsutjämning. Du kan konfigurera regler för enskilda portar eller kan du använda hög tillgänglighet portarna ska hantera all trafik oavsett TCP eller UDP-portnummer.  

Du kan använda den nya funktionen för hög tillgänglighet portar för att låsa upp en mängd olika scenarier, inklusive hög tillgänglighet och skala för interna NVAs. Funktionen kan användas för andra scenarier där det är opraktiska eller önskvärt att ange enskilda portar. Portar för hög tillgänglighet ger redundans och skala genom att tillåta att så många instanser som du behöver. Konfigurationen är inte längre begränsad till aktiv/passiv-scenarier. Hälsotillstånd avsökningen konfigurationerna skydda din tjänst genom att vidarebefordra trafik endast till Felfri instanser.

NVA leverantörer kan ange leverantör-stöds helt, flexibel scenarier för sina kunder. Enskild felpunkt tas bort och flera aktiva instanser stöds för skalan. Du kan skala till minst två instanser, beroende på din enhet. Kontakta leverantören NVA mer information om dessa scenarier.

### <a name = "availabilityzones"></a>Tillgänglighet zoner

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

Avancera återhämtning för ditt program med hjälp av tillgänglighet zoner i vilka regioner som stöds. Tillgänglighet zoner för närvarande under förhandsgranskning i vissa områden och kräver ytterligare Anmäl.

### <a name="automatic-zone-redundancy"></a>Automatisk-redundans

Du kan välja om belastningsutjämnare bör ge en zonredundant eller zonal frontend för var och en av dina program. Det är enkelt att skapa redundans med Load Balancer Standard. En frontend IP-adress är automatiskt zonredundant. En zonredundant frontend hanteras av alla zoner för tillgänglighet i en region samtidigt. En zonredundant datasökväg skapas för inkommande och utgående anslutningar. Redundans i Azure kräver inte flera IP-adresser och DNS-poster. 

Redundans är tillgänglig för offentlig eller intern framför-servrar. Din offentliga IP-adress och klientdelen privata IP för din interna belastningsutjämnare kan vara zonredundant.

Använd följande skript för att skapa en zonredundant offentliga IP-adressen för din interna belastningsutjämnare. Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Använd följande skript för att skapa en zonredundant frontend IP-adress för din interna belastningsutjämnare. Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Om din offentliga IP-Adressen frontend zonredundant, blir utgående anslutningar som görs automatiskt från VM-instanser zonredundant. Klientdelen är skyddat från zonen fel. SNAT-porttilldelning FRISKRIVNINGEN också zonen fel.

#### <a name="cross-zone-load-balancing"></a>Belastningsutjämning mellan zon

Belastningsutjämning mellan zonen är tillgänglig i en region för backend-poolen och ger maximal flexibilitet för VM-instanser. En frontend levererar flöden till någon virtuell dator i det virtuella nätverket, oavsett zonen tillgänglighet för VM-instans.

Du kan även ange en viss zon för dina frontend och backend-instanser, justera dina datasökväg och resurser med en viss zon.

Virtuella nätverk och undernät är aldrig begränsad av en zon. Definiera bara en backend-pool med önskad VM-instanser och konfigurationen är klar.

#### <a name="zonal-deployments"></a>Zonal distributioner

Som ett alternativ kan du justera din belastningsutjämnare frontend till en viss zon genom att definiera en zonal frontend. En zonal frontend hanteras av den avsedda tillgänglighet zon bara. Klientdelen i kombination med zonal VM-instanser, kan du justera resurser till specifika zoner.

Det finns en offentlig IP-adress som skapas i en viss zon alltid endast i zonen. Det går inte att ändra zonen av en offentlig IP-adress. Skapa en zonredundant offentliga IP-adress i stället för en offentlig IP-adress som kan kopplas till resurser i flera zoner.

Använd följande skript för att skapa en zonal offentliga IP-adress i tillgänglighet zon 1. Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

Använd följande skript om du vill skapa en intern belastningsutjämnare frontend i tillgänglighet zon 1.

Om du använder befintliga Resource Manager-mallar i konfigurationen, lägger du till den **sku** avsnitt till dessa mallar. Dessutom definiera den **zoner** egenskap i frontend IP-konfigurationen för den underordnade resursen.

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

Lägg till mellan zon för belastningsutjämning för din serverdel pool genom att lägga till dina VM-instanser som är i ett virtuellt nätverk i poolen.

Load Balancer Standard resursen är alltid regionala och zonredundant där tillgänglighet zoner stöds. Du kan distribuera en offentlig IP-adress eller en intern belastningsutjämnare frontend som inte har en tilldelad zon i en region. Stöd för zoner tillgänglighet påverkar inte funktioner för distribution. Om en region får senare tillgänglighet zoner, distribuerat tidigare offentliga IP-adresser eller interna belastningsutjämnare framför-servrar blir automatiskt zonredundant. En zonredundant datasökväg innebär inte 0% paketförlust.

### <a name = "nsg"></a>Nätverkssäkerhetsgrupper

Läsa in belastningsutjämning Standard och offentliga IP-Standard fullständigt publicera till det virtuella nätverket som kräver användning av Nätverkssäkerhetsgrupper (NSG: er). NSG: er gör det möjligt att godkända trafikflöde. Du kan använda NSG: er för att få fullständig kontroll över trafik för din distribution. Du behöver inte längre vänta tills andra trafikflöden att slutföra.

Koppla NSG: er till undernät eller till nätverksgränssnitt (NIC) för VM-instanser i backend-poolen. Använd den här konfigurationen med belastningen belastningsutjämnaren Standard och offentliga IP-Standard när den används som en offentlig IP på instansnivå. NSG: N måste uttryckligen godkända trafik som du vill tillåta för trafiken flöda.

Mer information om NSG: er och hur de ska användas för ditt scenario finns [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md).

### <a name ="outboundconnections"></a>Utgående anslutningar

Load Balancer Standard ger utgående anslutningar för virtuella datorer som inte ingår i det virtuella nätverket när en belastningsutjämnare använder port låtsas SNAT. Algoritmen port låtsas SNAT ger ökad stabilitet och skala.

När en offentlig belastningsutjämnare resurs associeras med VM-instanser, om varje utgående anslutningskälla. Källan om från det virtuella privata IP-adressutrymmet frontend offentliga IP-adressen för belastningsutjämnaren.

När utgående anslutningar används med en zonredundant frontend, anslutningarna är också zonredundant och SNAT port allokeringar klara zonen fel.

Den nya algoritmen i Load Balancer Standard preallocates SNAT portar till nätverkskort på varje virtuell dator. När ett nätverkskort läggs till i poolen, förallokerade SNAT portar är baserat på poolstorleken. I följande tabell visas port preallocations för sex nivåer med backend-adresspool:

| Poolstorleken (VM-instanser) | Förallokerade antalet SNAT portar |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

SNAT portar översätta inte direkt till antal utgående anslutningar. En SNAT port kan återanvändas för flera unika mål. Mer information finns i [utgående anslutningar](load-balancer-outbound-connections.md) artikel.

Om backend-poolens storlek ökar och övergår till en högre nivå, frigöras hälften av din allokerade portar. Anslutningar som är associerade med återvunnet port-timeout och måste återskapas. Nya anslutningsförsök lyckas omedelbart. Om backend-poolstorleken minskar och övergår till en lägre nivå, ökar antalet tillgängliga SNAT portar. I det här fallet påverkas inte befintliga anslutningar.

Load Balancer Standard har ett ytterligare konfigurationsalternativ som kan användas på grundval av per regel. Du kan styra vilka frontend används för port låtsas SNAT när flera framför-servrar är tillgängliga.

När endast läsa in belastningsutjämning Standard har VM-instanser, utgående SNAT anslutningar inte tillgänglig. Du kan återställa den här möjligheten uttryckligen genom att tilldela också VM-instanser till en offentlig belastningsutjämnare. Du kan också direkt tilldela offentliga IP-adresser som instansnivå offentliga IP-adresser för varje VM-instans. Det här konfigurationsalternativet kan krävas för vissa operativsystem och Programscenarier. 

### <a name="port-forwarding"></a>Vidarebefordrade portar

Basic och Standard belastningsutjämnare ger möjlighet att konfigurera inkommande NAT-regler för att mappa en frontend-port till en enskild backend-instans. Genom att konfigurera de här reglerna kan du exponera Remote Desktop Protocol-slutpunkter och SSH-slutpunkter eller utföra andra scenarier för programmet.

Load Balancer Standard fortsätter att ge möjlighet för vidarebefordrade portar via ingående NAT-regler. När det används med zonredundant framför-servrar blir zonredundant ingående NAT-regler och klara zonen fel.

### <a name="multiple-front-ends"></a>Flera framför-servrar

Konfigurera flera framför-ends design flexibilitet när program kräver flera enskilda IP-adresser som ska exponeras, till exempel TLS-webbplatser eller slutpunkter för SQL AlwaysOn-Tillgänglighetsgruppen. 

Load Balancer Standard fortsätter att tillhandahålla flera framför-servrar där du måste exponera en programslutpunkt för särskilda på en unik IP-adress.

Mer information om hur du konfigurerar flera frontend IP-adresser finns [flera IP-konfiguration](load-balancer-multivip-overview.md).

## <a name = "sku"></a>Om SKU: er

SKU: er är bara tillgängliga i Azure Resource Manager-distributionsmodellen. Den här förhandsversionen lanserar två SKU: er för belastningsutjämnaren och offentliga IP-resurser: Basic och Standard. De SKU: er skiljer sig åt i förmågor, prestandaegenskaperna, begränsningar och vissa inbyggda beteende. Virtuella datorer kan användas med antingen SKU. För både belastningsutjämnare och offentliga IP-resurser förblir SKU: er valfria attribut. När SKU: er har uteslutits i en scenariodefinition av standard konfigurationen med hjälp av grundläggande SKU: N.

>[!IMPORTANT]
>SKU: N av en resurs som är inte föränderliga. Du kan inte ändra SKU av en befintlig resurs.  

### <a name="load-balancer"></a>Belastningsutjämnare

Den [befintliga belastningsutjämnaren resursen](load-balancer-overview.md) blir grundläggande SKU: N och vanligtvis förblir tillgängliga och oförändrade.

Läs in belastningsutjämnaren Standard SKU är ny och är för närvarande under förhandsgranskning. I augusti 1 2017, API-version för Microsoft.Network/loadBalancers lägger till den **sku** egenskapen i resursdefinitionen:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer Standard används automatiskt zonen-flexibel regioner som erbjuder tillgänglighet zoner. Om belastningsutjämnaren har deklarerats zonal, är det inte automatiskt zonen-flexibel.

### <a name="public-ip"></a>Offentlig IP-adress

Den [befintlig offentlig IP-resurs](../virtual-network/virtual-network-ip-addresses-overview-arm.md) blir grundläggande SKU: N och förblir allmänt tillgänglig med alla dess funktioner, prestandaegenskaper och begränsningar.

Offentliga IP-Standard SKU är ny och är för närvarande under förhandsgranskning. I augusti 1 2017, API-version för Microsoft.Network/publicIPAddresses lägger till den **sku** egenskapen i resursdefinitionen:

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

Till skillnad från offentliga IP-Basic, som erbjuder flera fördelningsmetoder för använder offentliga IP-Standard alltid statisk tilldelning.

Offentliga IP-Standard som inte är automatiskt zonen-känsligt i områden som erbjuder tillgänglighet zoner. Om den offentliga IP-Adressen har deklarerats zonal, är det inte automatiskt zonen-flexibel. En zonal offentliga IP-adress kan inte ändras från en zon till en annan.

## <a name="migration-between-skus"></a>Migrering mellan SKU: er

SKU: er är inte föränderliga. Följ stegen i det här avsnittet för att flytta från en resurs SKU till en annan.

### <a name="migrate-from-basic-to-standard-sku"></a>Migrera från enkel till Standard-SKU

1. Skapa en ny Standard resurs (belastningsutjämning och offentliga IP-adresser, vid behov). Skapa dina regler och avsökning definitioner.

2. Ta bort grundläggande SKU-resurser (belastningsutjämning och offentliga IP-adresser, i tillämpliga fall) från alla VM-instanser. Se till att även ta bort alla VM-instanser i tillgänglighetsuppsättningen.

3. Bifoga alla VM-instanser till de nya Standard-SKU-resurserna.

### <a name="migrate-from-standard-to-basic-sku"></a>Migrera från Standard till grundläggande SKU

1. Skapa en ny grundläggande resurs (belastningsutjämning och offentliga IP-adresser, vid behov). Skapa dina regler och avsökning definitioner. 

2. Ta bort Standard-SKU-resurser (belastningsutjämning och offentliga IP-adresser, i tillämpliga fall) från alla VM-instanser. Se till att även ta bort alla VM-instanser i tillgänglighetsuppsättningen.

3. Bifoga alla VM-instanser till de nya grundläggande SKU-resurserna.

>[!IMPORTANT]
>
>Det finns begränsningar avseende användning av Basic och Standard-SKU: er.
>
>Hög tillgänglighet är portar och diagnostik för Standard-SKU bara tillgängliga i Standard-SKU. Du kan inte migrera från Standard-SKU till grundläggande SKU: N och behåller även dessa funktioner.
>
>Matchar SKU: er måste användas för belastningsutjämnaren och offentliga IP-resurser. Du kan inte ha en blandning av grundläggande SKU-resurser och Standard-SKU-resurser. Du kan inte koppla en virtuell dator, virtuella datorer i en Tillgänglighetsuppsättning, eller en virtuell dator skala inställd på båda SKU: er samtidigt.
>

## <a name="region-availability"></a>Regional tillgänglighet

Load Balancer Standard finns för närvarande i alla regioner för offentliga moln.

>[!IMPORTANT]
> En kort tidsperiod, åtkomst till regioner utanför en första start regioner (östra USA 2 centrala USA, Norra Europa, västra centrala USA, västra Europa, Sydostasien) kräver registrering av ytterligare prenumeration funktioner (AllowLBPreviewWave2 och AllowLBPreviewWave3).  [Följ dessa anvisningar](#additionalpreviewregions). Kör alla även om du redan har registrerat dig för AllowLBPreview redan.
> Det här kravet tas bort under de kommande veckorna.

## <a name="sku-service-limits-and-abilities"></a>SKU-tjänsten begränsningar och förmåga

Azure [Tjänstbegränsningarna för nätverk](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits) gäller per region per prenumeration. 

I följande tabell jämförs de gränser och förmågor belastningen belastningsutjämnaren Basic och Standard-SKU: er:

| Belastningsutjämnare | Basic | Standard |
| --- | --- | --- |
| Storlek på backend-pool | upp till 100 | upp till 1 000 |
| Backend-pool-gräns | Tillgänglighetsuppsättning | virtuella nätverk, region |
| Backend-adresspool design | Virtuella datorer i Tillgänglighetsuppsättningen, virtuella skaluppsättningen i Tillgänglighetsuppsättningen | Alla VM-instanser i det virtuella nätverket |
| Hög tillgänglighet portar | Stöds inte | Tillgänglig |
| Diagnostik | Begränsad, offentliga endast | Tillgänglig |
| VIP-tillgänglighet  | Stöds inte | Tillgänglig |
| Snabb IP-Mobility | Stöds inte | Tillgänglig |
|Scenarier för tillgänglighet zoner | Endast Zonal | Zonal, zonredundant, mellan zon belastningsutjämning |
| Utgående SNAT algoritm | På begäran | Förallokerade |
| Utgående SNAT frontend markering | Kan inte konfigureras, flera kandidater | Valfri konfiguration för att minska kandidater |
| Nätverkssäkerhetsgrupp | Valfria på NIC-undernät | Krävs |

I följande tabell jämförs de gränser och förmågan offentliga IP-Basic och Standard-SKU: er:

| Offentlig IP-adress | Basic | Standard |
| --- | --- | --- |
| Scenarier för tillgänglighet zoner | Endast Zonal | Zonredundant (standard), zonal (valfritt) | 
| Snabb IP-Mobility | Stöds inte | Tillgänglig |
| VIP-tillgänglighet | Stöds inte | Tillgänglig |
| Räknare | Stöds inte | Tillgänglig |
| Nätverkssäkerhetsgrupp | Valfria på NIC | Krävs |


## <a name="preview-sign-up"></a>Förhandsgranska registrering

Registrera prenumerationen för att delta i förhandsgranskningen för belastningen belastningsutjämnaren Standard SKU och tillhörande offentliga IP-Standard-SKU.  Registrera din prenumeration du får åtkomst från PowerShell eller Azure CLI 2.0. Om du vill registrera, utför du följande steg:

>[!NOTE]
>Registrering av funktionen Load Balancer Standard kan ta upp till en timme att börja gälla globalt. Om du vill använda Load Balancer Standard med [tillgänglighet zoner](https://aka.ms/availabilityzones), [separat anmälan](https://aka.ms/availabilityzones) krävs för AZ förhandsgranskning.

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> Åtkomst till regioner utanför en första start för en kort tidsperiod regioner (östra USA 2 centrala USA, Norra Europa, västra centrala USA, västra Europa, Sydostasien) kräva ytterligare prenumeration funktioner (AllowLBPreviewWave2 och AllowLBPreviewWave3).  Stegen nedan har ändrats för att aktivera ytterligare prenumeration funktioner. Kör alla även om du redan har registrerat dig för AllowLBPreview redan. Det här kravet tas bort under de kommande veckorna.


### <a name="sign-up-by-using-azure-cli-20"></a>Logga med hjälp av Azure CLI 2.0

1. Registrera funktionen med providern:

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. Åtgärden kan ta upp till 10 minuter att slutföra. Du kan kontrollera status för åtgärden med följande kommando:

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    Gå vidare till nästa steg när funktionen registreringstillstånd returnerar 'Registrerade' för var och en av ovanstående funktioner i prenumerationen. Exempel:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. Slutför registreringen förhandsgranskningen genom att registrera prenumerationen med resursprovidern:

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>Logga med hjälp av PowerShell

1. Registrera funktionen med providern:

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. Åtgärden kan ta upp till 10 minuter att slutföra. Du kan kontrollera status för åtgärden med följande kommando:

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  Gå vidare till nästa steg när funktionen registreringstillstånd returnerar 'Registrerade' för var och en av ovanstående funktioner i prenumerationen. Exempel:

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. Slutför registreringen förhandsgranskningen genom att registrera prenumerationen med resursprovidern:

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>Prissättning

Belastningen belastningsutjämnaren Standard SKU fakturering baseras på konfigurerade regler och bearbetade data. Inga avgifter tas ut i förhandsversionen. Mer information finns i [belastningsutjämnaren](https://aka.ms/lbpreviewpricing) och [offentliga IP-Adressen](https://aka.ms/lbpreviewpippricing) priser sidor.

Kunder fortsätta att få Load Balancer grundläggande SKU utan kostnad.

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller vid tidpunkten för förhandsgranskning och kan komma att ändras:

- Läsa in belastningsutjämning backend-instanser kan inte finnas i peerkoppla virtuella nätverk just nu. Backend-instanser måste vara i samma region.
- SKU: er är inte föränderliga. Du kan inte ändra SKU av en befintlig resurs.
- Båda SKU: er kan användas med en fristående VM, VM-instanser i en Tillgänglighetsuppsättning eller skalan för en virtuell dator. VM kombinationer kan inte användas med både SKU: er samtidigt. En konfiguration som innehåller en blandning av SKU: er tillåts inte.
- Med hjälp av ett internt belastningen belastningsutjämnaren Standard med en VM-instans (eller någon del av en Tillgänglighetsuppsättning) inaktiverar [SNAT utgående anslutningar som standard](load-balancer-outbound-connections.md). Du kan återställa den här möjligheten att en fristående VM, VM-instanser i en Tillgänglighetsuppsättning eller en skaluppsättning för virtuell dator. Du kan även återställa möjligheten att göra utgående anslutningar. Om du vill återställa dessa funktioner samtidigt tilldela en public Load Balancer Standard, eller offentlig IP-Standard som en instansnivå offentliga IP-adress, till samma VM-instans. När tilldelningen är klar, har port låtsas SNAT till en offentlig IP-adress angetts igen.
- VM-instanser kan behöva grupperas i tillgänglighetsuppsättningar för att uppnå fullständig backend-adresspool skala. Upp till 150 tillgänglighet placeras uppsättningar och fristående virtuella datorer i en enda backend-adresspool.
- IPv6 stöds inte.
- I samband med tillgänglighet zoner är en frontend inte föränderliga från zonal till zonredundant eller vice versa. När en frontend skapas som zonredundant, förblir den zonredundant. När en frontend skapas som zonal, förblir den zonal.
- I samband med tillgänglighet zoner kan zonal offentliga IP-adressen inte flyttas från en zon till en annan.
- [Azure övervaka aviseringar](../monitoring-and-diagnostics/monitoring-overview-alerts.md) stöds inte just nu.
- Portalen stöder ännu inte utökade preview regioner.  Använd klientverktyg som mallar, Azure CLI 2.0 eller PowerShell som en lösning.


## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Load Balancer grundläggande](load-balancer-overview.md).
- Lär dig mer om [tillgänglighet zoner](../availability-zones/az-overview.md).
- Lär dig mer om [Nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md).
- Lär dig mer om den andra nyckeln [nätverk](../networking/networking-overview.md) i Azure.
- Lär dig mer om [mått som exponeras](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers) i [Azure-Monitor](../monitoring-and-diagnostics/monitoring-overview.md).
