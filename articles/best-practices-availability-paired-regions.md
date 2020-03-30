---
title: Säkerställa kontinuitet & katastrofåterställning med azure paired regions
description: Säkerställa att programmet är resiliens med hjälp av regionalkoppling i Azure
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248260"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Business Continuity and Disaster Recovery (BCDR): Azure-kopplade områden

## <a name="what-are-paired-regions"></a>Vad är parade regioner?

En Azure-region består av en uppsättning datacenter som distribueras inom en svarstidsdefinierad perimeter och ansluten via ett dedikerat nätverk med låg latens.  Detta säkerställer att Azure-tjänster inom en Azure-region erbjuder bästa möjliga prestanda och säkerhet.  

En Azure-geografi definierar ett område i världen som innehåller minst en Azure-region. Geografiska områden definierar en diskret marknad, som vanligtvis innehåller två eller flera regioner, som bevarar datahemvist och efterlevnadsgränser.  Här hittar du mer information om Azures globala [infrastruktur](https://azure.microsoft.com/global-infrastructure/regions/)

Ett regionalt par består av två regioner inom samma geografi. Azure serialiserar plattformsuppdateringar (planerat underhåll) över regionala par, vilket säkerställer att endast en region i varje par uppdateras åt gången. Om ett avbrott påverkar flera regioner prioriteras minst en region i varje par för återställning.

![AzureGeografi](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Vissa Azure-tjänster drar ytterligare nytta av parade regioner för att säkerställa kontinuitet i verksamheten och skydda mot dataförlust.  Azure tillhandahåller flera [lagringslösningar](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) som utnyttjar parade regioner för att säkerställa datatillgänglighet. [Azure Geo-redundant Storage](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replikerar till exempel data automatiskt till en sekundär region, vilket säkerställer att data är varaktiga även i händelse av att den primära regionen inte kan återställas. 

Observera att inte alla Azure-tjänster replikerar data automatiskt, inte heller alla Azure-tjänster automatiskt tillbaka från en misslyckad region till sitt par.  I sådana fall måste återställning och replikering konfigureras av kunden.

## <a name="can-i-select-my-regional-pairs"></a>Kan jag välja mina regionala par?

Nej. Vissa Azure-tjänster är beroende av regionala par, till exempel Azures [redundanta lagring](./storage/common/storage-redundancy.md). Dessa tjänster tillåter inte att du skapar nya regionala parningar.  På samma sätt, eftersom Azure styr planerade underhålls- och återställningsprioriteringar för regionala par, kan du inte definiera dina egna regionala par för att dra nytta av dessa tjänster. Du kan dock skapa en egen katastrofåterställningslösning genom att skapa tjänster i valfritt antal regioner och utnyttja Azure-tjänster för att para ihop dem. 

Du kan till exempel använda Azure-tjänster som [AzCopy](./storage/common/storage-use-azcopy-v10.md) för att schemalägga säkerhetskopiering av data till ett lagringskonto i en annan region.  Med Hjälp av [Azure DNS och Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)kan kunder utforma en flexibel arkitektur för sina program som överlever förlusten av den primära regionen.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Är jag begränsad till att använda tjänster inom mina regionala par?

Nej. Även om en viss Azure-tjänst kan förlita sig på ett regionalt par, kan du vara värd för dina andra tjänster i alla regioner som uppfyller dina affärsbehov.  En Azure GRS-lagringslösning kan koppla data i Canada Central med en peer i Östra Kanada när du använder Beräkningsresurser i östra USA.  

## <a name="must-i-use-azure-regional-pairs"></a>Måste jag använda Azure-regionpar?

Nej. Kunder kan utnyttja Azure-tjänster för att skapa en flexibel tjänst utan att förlita sig på Azures regionala par.  Vi rekommenderar dock att du konfigurerar återställning av affärskontinuitetskatastrofer (BCDR) över regionala par för att dra nytta av [isolering](./security/fundamentals/isolation-choices.md) och förbättra [tillgängligheten](./availability-zones/az-overview.md). För program med stöd för flera aktiva regioner rekommenderar vi att du använder båda regionerna i ett regionpar där det är möjligt. Detta säkerställer optimal tillgänglighet för program och minimerad återställningstid i händelse av en katastrof. När det är möjligt kan du utforma ditt program för [maximal återhämtning](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) och enkel [katastrofåterställning.](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)

## <a name="azure-regional-pairs"></a>Regionala azure-par

| Geografi | Regionalt par A | Regionalt par B  |
|:--- |:--- |:--- |
| Asien-Stillahavsområdet |Östasien (Hongkong) | Sydostasien (Singapore) |
| Australien |Australien, östra |Australien, sydöstra |
| Australien |Australien, centrala |Australien, centrala 2 |
| Brasilien |Brasilien, södra |USA, södra centrala |
| Kanada |Kanada, centrala |Kanada, östra |
| Kina |Kina, norra |Kina, östra|
| Kina |Kina Norra 2 |Kina Öst 2|
| Europa |Norra Europa (Irland) |Västeuropa (Nederländerna) |
| Frankrike |Frankrike, centrala|Frankrike, södra|
| Tyskland |Tyskland, centrala |Tyskland, nordöstra |
| Indien |Indien, centrala |Indien, södra |
| Indien |Indien, västra |Indien, södra |
| Japan |Japan, östra |Japan, västra |
| Korea |Sydkorea, centrala |Sydkorea, södra |
| Nordamerika |USA, östra |USA, västra |
| Nordamerika |USA, östra 2 |USA, centrala |
| Nordamerika |USA, norra centrala |USA, södra centrala |
| Nordamerika |USA, västra 2 |USA, västra centrala |
| Norge | Norge Öst | Norge Väst |
| Sydafrika | Sydafrika North |Sydafrika Väst |
| Schweiz | Schweiz Norr |Schweiz Väst |
| Storbritannien |Storbritannien, västra |Storbritannien, södra |
| Förenade Arabemiraten | Uae Norra | Centrala Förenade Arabemiraten
| Usa:s försvarsdepartement |USA DoD, östra |USA DoD, centrala |
| US Government |US Gov, Arizona |US Gov, Texas |
| US Government |USA Gov, Iowa |US Gov, Virginia |
| US Government |US Gov, Virginia |US Gov, Texas |

> [!Important]
> - Västra Indien paras ihop i en riktning endast. Västra Indiens sekundära region är södra Indien, men södra Indiens sekundära region är centrala Indien.
> - Brasilien South är unik eftersom det är parat med en region utanför sin geografi. Brasilien Södras sekundära region är Södra centrala USA. Södra centrala USA: s sekundära region är inte Brasilien South.


## <a name="an-example-of-paired-regions"></a>Ett exempel på länkade regioner
Bilden nedan illustrerar ett hypotetiskt program som använder det regionala paret för haveriberedskap. De gröna numren markerar aktiviteter mellan regioner för tre Azure-tjänster (Azure-beräkning, lagring och databas) och hur de är konfigurerade för att replikera mellan regioner. De unika fördelarna med att distribuera mellan parade regioner markeras med de orangea talen.

![Översikt över fördelar med parkopplad region](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Bild 2 – Hypotetiskt regionalt Azure-par

## <a name="cross-region-activities"></a>Verksamhet mellan regioner
Enligt figur 2.

1. **Azure Compute (IaaS)** – Du måste etablera ytterligare beräkningsresurser i förväg för att säkerställa att resurser är tillgängliga i en annan region under en katastrof. Mer information finns i [teknisk vägledning för Azure resiliency](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** – Om du använder hanterade diskar kan du läsa om [säkerhetskopieringar mellan regioner](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) med Azure Backup och replikera virtuella [datorer](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) från en region till en annan med Azure Site Recovery. Om du använder lagringskonton konfigureras GEO-redundant lagring (GRS) som standard när ett Azure Storage-konto skapas. Med GRS replikeras dina data automatiskt tre gånger inom den primära regionen och tre gånger i den parade regionen. Mer information finns i [Azure Storage Redundansalternativ](storage/common/storage-redundancy.md).

3. **Azure SQL Database** – Med Azure SQL Database Geo-Replication kan du konfigurera asynkron replikering av transaktioner till alla regioner i världen. Vi rekommenderar dock att du distribuerar dessa resurser i en parkopplad region för de flesta katastrofåterställningsscenarier. Mer information finns [i Geo-Replication i Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** – i Resource Manager isoleras komponenter logiskt mellan olika regioner automatiskt. Detta innebär att logiska fel i en region är mindre benägna att påverka en annan.

## <a name="benefits-of-paired-regions"></a>Fördelar med parade regioner

5. **Fysisk isolering** – När det är möjligt föredrar Azure minst 300 miles av separation mellan datacenter i ett regionalt par, även om detta inte är praktiskt eller möjligt i alla geografiska områden. Fysisk datacenterseparation minskar risken för naturkatastrofer, oroligheter, strömavbrott eller fysiska nätverksavbrott som påverkar båda regionerna samtidigt. Isolering är föremål för begränsningar inom geografin (geografistorlek, tillgänglighet för kraft-/nätverksinfrastruktur, föreskrifter osv.).  

6. **Plattformsbaserad replikering** – Vissa tjänster som geo redundant lagring ger automatisk replikering till den parade regionen.

7. **Regionens återställningsorder** – I händelse av ett brett avbrott prioriteras återställning av en region av varje par. Program som distribueras i länkade regioner körs då garanterat i minst en prioriterad region. Om ett program distribueras mellan regioner som inte är ihopkopplade kan återställningen fördröjas – i värsta fall kan de valda regionerna vara de två sista som ska återställas.

8. **Sekventiella uppdateringar** – Planerade Azure-systemuppdateringar distribueras till parade regioner sekventiellt (inte samtidigt) för att minimera driftstopp, effekten av fel och logiska fel i den sällsynta händelsen av en felaktig uppdatering.

9. **Data hemvist** - En region finns inom samma geografi som sitt par (med undantag för Brasilien South) för att uppfylla kraven på data hemvist för skatte-och brottsbekämpande jurisdiktion ändamål.
