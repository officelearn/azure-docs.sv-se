---
title: Prisnivåer för Azure Database for MariaDB
description: Den här artikeln beskrivs olika prisnivåer för Azure Database for MariaDB.
author: jan-eng
ms.author: janeng
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 561244efd653294694cc16a1115962473e9a7cec
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48249040"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB prisnivåer

Du kan skapa en Azure Database for MariaDB-servern på något av tre olika tjänstnivåer: Basic, generell användning och Minnesoptimerad. Prisnivåerna åtskiljs av mängden beräkning i vCores som kan etableras och minne per vCore lagringsteknik som används för att lagra data. Alla resurser etableras på servernivå MariaDB. En server kan ha en eller flera databaser.

|    | **Basic** | **Generell användning** | **Optimerat minne** |
|:---|:----------|:--------------------|:---------------------|
| Compute-generering | Generation 5 |Generation 5 | Generation 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
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

När du har skapat en server, antalet virtuella kärnor och prisnivå (förutom till och från Basic) kan ändras upp eller ned på några sekunder. Du kan även oberoende Justera mängden lagringsutrymme upp och kvarhållningsperioden för säkerhetskopior upp eller ned utan någon nedtid. Du kan inte ändra lagringstypen säkerhetskopiering när en server har skapats. Mer information finns i den [skala resurser](#scale-resources) avsnittet.

## <a name="compute-generations-and-vcores"></a>Beräkningsgenereringar och virtuella kärnor

Compute-resurser som tillhandahålls som vCores, som representerar en logisk CPU som den underliggande maskinvaran. Gen 5 logiska CPU baseras på Intel E5-2673 v4-processorn (Broadwell) 2.3 GHz-processorer.

## <a name="storage"></a>Storage

Lagring som du etablerar är mängden lagringskapacitet som är tillgängliga för din Azure Database for MariaDB-server. Lagringsutrymmet används för databasfilerna, temporära filer, transaktionsloggar och MariaDB servern loggar. Den totala mängden lagring som du etablerar definierar också tillgänglig i/o-kapacitet till din server.

|    | **Basic** | **Generell användning** | **Optimerat minne** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Azure Standard-lagring | Azure Premium Storage | Azure Premium Storage |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Öka lagringsstorleken | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Maximalt antal 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Maximalt antal 6000 IOPS |

Du kan lägga till ytterligare lagringskapacitet under och efter skapandet av servern. Basic-nivån ger inte en garanti för IOPS. I generell användning och Minnesoptimerad prisnivåer, skala IOPS med den allokerade lagringsstorleken i ett 3:1-förhållande.

Du kan övervaka dina i/o-användningen i Azure portal eller med hjälp av Azure CLI-kommandon. De mått som är relevanta för att övervaka är gränsen för lagring, lagringsprocent, lagringsutrymme och IO-procent.
<!--[storage limit, storage percentage, storage used, and IO percent](concepts-monitoring.md)-->

### <a name="reaching-the-storage-limit"></a>Når gränsen för lagring

Servern är skrivskyddad när mängden ledigt utrymme uppnår mindre än 5 GB eller 5% av etablerad lagring, beroende på vilket som är mindre. Exempel: Om du har etablerat 100 GB lagringsutrymme och den faktiska användningen går över 95 GB, servern är skrivskyddad. Du kan också om du har etablerat 5 GB lagringsutrymme, är servern skrivskyddad när det lediga lagringsutrymmet som når mindre än 250 MB.  

När tjänsten försöker skrivskydda servern, alla nya transaktion skrivbegäranden blockeras och befintliga aktiva transaktioner fortsätter att köra. När servern har angetts till skrivskyddat läge, genomför alla efterföljande skrivåtgärder och transaktionen misslyckas. Läs frågor kommer att fortsätta att arbeta utan avbrott. När du ökar den etablerade lagringen, kommer servern vara redo att acceptera skrivtransaktioner igen.

Vi rekommenderar att du ställer in en avisering som meddelar dig när tröskelvärdet närmar sig din serverlagring för att undvika hämta till skrivskyddat läge. 

<!--For more information, see the documentation on [how to set up an alert](howto-alert-on-metric.md).-->

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhetskopior av din server. Minsta kvarhållningsperioden för säkerhetskopior är sju dagar. Du kan ange en kvarhållningsperiod på upp till 35 dagar. Kvarhållning kan justeras när som helst under livslängden för servern. Du kan välja mellan lokalt redundant och geo-redundanta säkerhetskopieringar. GEO-redundanta säkerhetskopieringar lagras också i den [geoparats ihop region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) för den region där din server skapas. Den här redundans ger en nivå av skydd i händelse av en katastrof. Du får också möjlighet att återställa servern till alla andra Azure-regioner där tjänsten är tillgänglig med geo-redundanta säkerhetskopieringar. Det går inte att ändra mellan de två säkerhetskopieringslagring alternativen när servern har skapats.

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server kan du oberoende av varandra kan ändra vCores, prisnivån (förutom till och från Basic), hur mycket lagringsutrymme och kvarhållningsperioden för säkerhetskopior. Du kan inte ändra lagringstypen säkerhetskopiering när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Kvarhållningsperiod för säkerhetskopiering kan skalas upp eller ned från 7 till 35 dagar. Lagringsutrymmet kan bara ökas. Skalning av resurser kan göras antingen via portalen eller Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

När du ändrar antalet virtuella kärnor eller prisnivån, skapas en kopia av den ursprungliga servern för den nya compute-fördelningen. När den nya servern är igång kan växlas anslutningar till den nya servern. Inga nya anslutningar kan upprättas vid den tidpunkt då när systemet växlar till den nya servern, och alla ogenomförda transaktioner återställs. Det här fönstret varierar, men i de flesta fall är mindre än en minut.

Skala lagring och ändra kvarhållningsperioden för säkerhetskopior är SANT online åtgärder. Stilleståndstid ingen och ditt program påverkas inte. När IOPS skalar med storleken på den etablerade lagringen, kan du öka IOPS som är tillgängliga till servern genom att skala upp lagring.

## <a name="pricing"></a>Prissättning

Den senaste prisinformationen, finns i tjänsten [prissättningssidan](https://azure.microsoft.com/pricing/details/mariadb/). Att se kostnaden för den konfiguration du vill ha den [Azure-portalen](https://portal.azure.com/#create/Microsoft.MariaDBServer) visar den månatliga kostnaden på den **prisnivå** fliken baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan använda du Azures priskalkylator för att få ett beräknat pris. På den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/) webbplats, väljer **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure Database for MariaDB**att anpassa alternativ.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om den [tjänsten begränsningar](concepts-limits.md).
- Lär dig hur du [skapar en MariaDB-server i Azure portal](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->