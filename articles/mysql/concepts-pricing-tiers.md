---
title: Prisnivåer för Azure Database for MySQL
description: Den här artikeln beskrivs olika prisnivåer för Azure Database för MySQL.
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: aab3aaafd410c4f85eba7a246d0922128908bf93
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445437"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database för MySQL prisnivåer

Du kan skapa en Azure Database for MySQL-servern på något av tre olika tjänstnivåer: Grundläggande, generell användning och minnesoptimerad. Prisnivåerna åtskiljs av mängden beräkning i vCores som kan etableras och minne per vCore lagringsteknik som används för att lagra data. Alla resurser etableras på servernivå för MySQL. En server kan ha en eller flera databaser.

|    | **Basic** | **Generell användning** | **Optimerat minne** |
|:---|:----------|:--------------------|:---------------------|
| Compute-generering | Gen 4, generation 5 | Gen 4, generation 5 | Generation 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Lagringstyp | Azure Standard-lagring | Azure Premium Storage | Azure Premium Storage |
| Kvarhållningsperiod för databasen | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Använd följande tabell som utgångspunkt för att välja en prisnivå.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbetsbelastningar som kräver lätt beräkning och i/o-prestanda. Exempel är servrar som används för utveckling eller testning eller sällan småskaliga program. |
| Generellt syfte | De flesta företags arbetsbelastningar som kräver belastningsutjämnade beräknings- och minnesresurser med skalbart i/o-dataflöde. Exempel är servrar som värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Högpresterande arbetsbelastningar som kräver minnesprestanda för snabbare bearbetning av transaktioner och högre samtidighet. Exempel är servrar för att bearbeta realtidsdata och transaktionsappar eller analysappar appar med höga prestanda.|

När du har skapat en server, antalet virtuella kärnor, skapande av maskinvara och priser nivå (förutom till och från Basic) kan ändras upp eller ned på några sekunder. Du kan även oberoende Justera mängden lagringsutrymme upp och kvarhållningsperioden för säkerhetskopior upp eller ned utan någon nedtid. Du kan inte ändra lagringstypen säkerhetskopiering när en server har skapats. Mer information finns i den [skala resurser](#scale-resources) avsnittet.

## <a name="compute-generations-and-vcores"></a>Beräkningsgenereringar och virtuella kärnor

Compute-resurser som tillhandahålls som vCores, som representerar en logisk CPU som den underliggande maskinvaran. Kina Öst 1, Kina Nord 1, US DoD centrala och US DoD, östra använda Gen 4 logiska processorer som baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer. Alla andra regioner använda Gen 5 logiska processorer som är baserade på Intel E5-2673 v4-processorn (Broadwell) 2.3 GHz-processorer.

## <a name="storage"></a>Storage

Lagring som du etablerar är mängden lagringskapacitet som är tillgängliga för din Azure Database for MySQL-server. Lagringsutrymmet används för databasfilerna, temporära filer, transaktionsloggar och MySQL-server loggar. Den totala mängden lagring som du etablerar definierar också tillgänglig i/o-kapacitet till din server.

|    | **Basic** | **Generell användning** | **Optimerat minne** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Azure Standard-lagring | Azure Premium Storage | Azure Premium Storage |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Öka lagringsstorleken | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Maximalt antal 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Maximalt antal 6000 IOPS |

Du kan lägga till ytterligare lagringskapacitet under och efter skapandet av servern och att systemet kan utöka lagringen automatiskt baserat på arbetsbelastningens lagringsanvändningen. Basic-nivån ger inte en garanti för IOPS. I generell användning och Minnesoptimerad prisnivåer, skala IOPS med den allokerade lagringsstorleken i ett 3:1-förhållande.

Du kan övervaka dina i/o-användningen i Azure portal eller med hjälp av Azure CLI-kommandon. De mått som är relevanta för att övervaka är [gränsen för lagring, lagringsprocent, lagringsutrymme och IO-procent](concepts-monitoring.md).

### <a name="large-storage-preview"></a>Stora storage (förhandsversion)

Lagringsgränserna ökar vi vår generell användning och Minnesoptimerad nivåer. Nyligen skapade servrarna kan att delta i förhandsversionen etablera upp till 16 TB lagring. IOPS skala på ett 3:1-förhållande upp till 20 000 IOPS. Precis som med den aktuella allmänt tillgänglig lagringen, du lägger till ytterligare lagringskapacitet efter skapandet av servern och att systemet kan utöka lagringen automatiskt baserat på arbetsbelastningens lagringsanvändningen.

|              | **Generell användning** | **Optimerat minne** |
|:-------------|:--------------------|:---------------------|
| Lagringstyp | Azure Premium Storage | Azure Premium Storage |
| Lagringsstorlek | 32 GB till 16 TB| 32 GB till 16 TB |
| Öka lagringsstorleken | 1 GB | 1 GB |
| IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Högst 20 000 IOPS| 3 IOPS/GB<br/>Min 100 IOPS<br/>Högst 20 000 IOPS |

> [!IMPORTANT]
> Stora lagring är för närvarande i offentlig förhandsversion i följande regioner: Östra USA, östra USA 2, centrala USA, västra USA, Nordeuropa, Västeuropa, Storbritannien, södra, Storbritannien, västra, Sydostasien, östra Asien, östra Japan, östra och Japan, västra, Korea, centrala Korea, södra, Australien, Östra Australien sydöstra.
>
> Förhandsversionen av stort lagringsutrymme stöder för närvarande inte:
>
> * Geografiskt redundanta säkerhetskopieringar
> * Mellan region replikering

### <a name="reaching-the-storage-limit"></a>Når gränsen för lagring

Servrar med färre än 100 GB etablerad lagring är skrivskyddad om det lediga lagringsutrymmet som är mindre än 512MB eller 5% av den allokerade lagringsstorleken. Servrar med mer än 100 GB etablerad lagring markeras Läs endast när det lediga lagringsutrymmet som är mindre än 5 GB.

Exempel: Om du har etablerat 110 GB lagringsutrymme och den faktiska användningen går över 105 GB, servern är skrivskyddad. Du kan också om du har etablerat 5 GB lagringsutrymme, är servern skrivskyddad när det lediga lagringsutrymmet som når mindre än 512 MB.

När tjänsten försöker göra så att servern blir skrivskyddad blockeras alla nya skrivtransaktionsbegäranden och befintliga aktiva transaktioner fortsätter att köras. När servern är i skrivskyddat läge misslyckas alla efterföljande skrivåtgärder och transaktioner. Läsfrågor fortsätter att fungera utan avbrott. När du har ökat lagringen är servern redo att acceptera skrivtransaktioner igen.

Vi rekommenderar att du aktiverar storage auto-väx eller ställa in en avisering som meddelar dig när ditt serverutrymme närmar sig tröskelvärdet så du kan undvika skrivskyddat läge. Mer information finns i dokumentationen på [hur du ställer in en avisering](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Storage auto-Väx

Om lagring auto väx är aktiverad, lagringen som automatiskt är växer utan att påverka arbetsbelastningen. För servrar med färre än 100 GB etablerad lagring ökar den allokerade lagringsstorleken med 5 GB när det lediga lagringsutrymmet som understiger det större av 1 GB eller 10% av allokerat lagringsutrymme. För servrar med fler än 100 GB allokerat lagringsutrymme ökar den allokerade lagringsstorleken med 5% när det lediga utrymmet är mindre än 5% av den allokerade lagringsstorleken. Maximal lagringsgräns som anges ovan gäller.

Exempel: Om du har etablerat 1 000 GB lagringsutrymme och den faktiska användningen går över 950 GB, serverstorlek ökas till 1050 GB. Du kan också om du har etablerat 10 GB lagringsutrymme är lagringsstorleken ökning på 15 GB när mindre än 1 GB lagringsutrymme är kostnadsfri.

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhetskopior av din server. Minsta kvarhållningsperioden för säkerhetskopior är sju dagar. Du kan ange en kvarhållningsperiod på upp till 35 dagar. Kvarhållning kan justeras när som helst under livslängden för servern. Du kan välja mellan lokalt redundant och geo-redundanta säkerhetskopieringar. GEO-redundanta säkerhetskopieringar lagras också i den [geoparats ihop region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) för den region där din server skapas. Den här redundans ger en nivå av skydd i händelse av en katastrof. Du får också möjlighet att återställa servern till alla andra Azure-regioner där tjänsten är tillgänglig med geo-redundanta säkerhetskopieringar. Det går inte att ändra mellan de två säkerhetskopieringslagring alternativen när servern har skapats.

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server du oberoende av varandra kan ändra den virtuella kärnor, maskinvara generation, prisnivån (förutom till och från Basic), hur mycket lagringsutrymme och kvarhållningsperioden för säkerhetskopior. Du kan inte ändra lagringstypen säkerhetskopiering när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Kvarhållningsperiod för säkerhetskopiering kan skalas upp eller ned från 7 till 35 dagar. Lagringsutrymmet kan bara ökas. Skalning av resurser kan göras antingen via portalen eller Azure CLI. Ett exempel på skalning med hjälp av Azure CLI, se [övervaka och skala en Azure Database for MySQL-server med hjälp av Azure CLI](scripts/sample-scale-server.md).

När du ändrar antalet virtuella kärnor, skapas kontot maskinvara eller prisnivån, en kopia av den ursprungliga servern för den nya compute-fördelningen. När den nya servern är igång kan växlas anslutningar till den nya servern. Inga nya anslutningar kan upprättas vid den tidpunkt då när systemet växlar till den nya servern, och alla ogenomförda transaktioner återställs. Det här fönstret varierar, men i de flesta fall är mindre än en minut.

Skala lagring och ändra kvarhållningsperioden för säkerhetskopior är SANT online åtgärder. Stilleståndstid ingen och ditt program påverkas inte. När IOPS skalar med storleken på den etablerade lagringen, kan du öka IOPS som är tillgängliga till servern genom att skala upp lagring.

## <a name="pricing"></a>Prissättning

Den senaste prisinformationen, finns i tjänsten [prissättningssidan](https://azure.microsoft.com/pricing/details/mysql/). Att se kostnaden för den konfiguration du vill ha den [Azure-portalen](https://portal.azure.com/#create/Microsoft.MySQLServer) visar den månatliga kostnaden på den **prisnivå** fliken baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan använda du Azures priskalkylator för att få ett beräknat pris. På den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/) webbplats, väljer **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure Database for MySQL**att anpassa alternativ.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en MySQL-server i portalen](howto-create-manage-server-portal.md).
- Lär dig mer om [tjänstbegränsningar](concepts-limits.md).
- Lär dig hur du [skala ut med skrivskyddade repliker](howto-read-replicas-portal.md).
