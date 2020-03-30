---
title: Skapa Azure Cosmos-behållare och databaser i autopilotläge.
description: Lär dig mer om fördelarna, användningsfallen och hur du etablerar Azure Cosmos-databaser och behållare i autopilotläge.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246661"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Skapa Azure Cosmos-containrar och databaser i Autopilot-läge (förhandsversion)

Med Azure Cosmos DB kan du etablera dataflöde i containrar i antingen manuellt läge eller Autopilot-läge. I den här artikeln beskrivs fördelarna och användningsfallen i Autopilot-läge.

> [!NOTE]
> Autopilotläget är för närvarande tillgängligt i offentlig förhandsversion. Du kan bara [aktivera autopilot för nya databaser och behållare.](#create-a-database-or-a-container-with-autopilot-mode) Den är inte tillgänglig för befintliga behållare och databaser.

Förutom manuell etablering av dataflöde kan du nu konfigurera Azure Cosmos-behållare i autopilotläge. Behållare och databaser som konfigurerats i autopilotläge **skalas automatiskt och omedelbart det etablerade dataflödet baserat på dina programbehov utan att påverka tillgängligheten, svarstiden, dataflödet eller prestanda för arbetsbelastningen globalt.**

När du konfigurerar behållare och databaser i autopilotläge måste `Tmax` du ange det maximala dataflödet som inte ska överskridas. Behållare kan sedan skala sitt `0.1*Tmax < T < Tmax`dataflöde så att . . Med andra ord skalas behållare och databaser direkt baserat på arbetsbelastningsbehoven, från så lågt som 10 % av det maximala dataflödesvärdet som du har konfigurerat upp till det konfigurerade maximala dataflödesvärdet. Du kan ändra inställningen`Tmax`för maximalt dataflöde ( ) på en autopilotdatabas eller behållare när som helst. Med autopilotalternativet är 400 RU/s minsta dataflöde per behållare eller databas inte längre tillämpligt.

Under förhandsgranskningen av autopiloten, för det angivna maximala dataflödet på behållaren eller databasen, tillåter systemet att arbeta inom den beräknade lagringsgränsen. Om lagringsgränsen överskrids justeras det maximala dataflödet automatiskt till ett högre värde. När du använder dataflöde på databasnivå med autopilotläge beräknas antalet `0.001*TMax`behållare som tillåts i en databas som: . Om du till exempel etablerar 20 000 autopilot-RU/s kan databasen ha 20 behållare.

## <a name="benefits-of-autopilot-mode"></a>Fördelar med autopilotläge

Azure Cosmos-behållare som är konfigurerade i autopilotläge har följande fördelar:

* **Enkelt:** Behållare i autopilotläge tar bort komplexiteten för att hantera etablerat dataflöde (RU: er) och kapacitet manuellt för olika behållare.

* **Skalbar:** Behållare i autopilotläge skalar sömlöst den etablerade dataflödeskapaciteten efter behov. Det finns inga störningar i klientanslutningar, program och de påverkar inte några befintliga SLA.There is no disruption to client connections, applications and they don't impact any existing SLAs.

* **Kostnadseffektivt:** När du använder behållare som konfigurerats i autopilotläge betalar du bara för de resurser som dina arbetsbelastningar behöver per timme.

* **Mycket tillgänglig:** Behållare i autopilotläge använder samma globalt distribuerade, feltoleranta, mycket tillgängliga backend för att säkerställa datahållbarhet och hög tillgänglighet.

## <a name="use-cases-of-autopilot-mode"></a>Användningsfall i autopilotläge

Användningsfallen för Azure Cosmos-behållare som konfigurerats i autopilotläge är:

* **Variabla arbetsbelastningar:** När du kör ett lättanvänt program med maximal användning på 1 timme till flera timmar några gånger varje dag eller flera gånger per år. Exempel på detta är ansökningar om personal, budgetering och operativ rapportering. För sådana scenarier kan behållare som konfigurerats i autopilotläge användas, och du behöver inte längre manuellt etablera för antingen topp- eller medelkapacitet.

* **Oförutsägbara arbetsbelastningar:** När du kör arbetsbelastningar där det finns databasanvändning hela dagen, men också toppar av aktivitet som är svåra att förutsäga. Ett exempel är en trafikplats som ser en ökning av aktiviteten när väderprognosen ändras. Behållare som konfigurerats i autopilotläge justerar kapaciteten för att uppfylla behoven hos programmets toppbelastning och skalar ned när aktiviteten är över.

* **Nya applikationer:** Om du distribuerar ett nytt program och är osäker på hur mycket etablerat dataflöde (dvs. hur många ru: er) du behöver. Med behållare konfigurerade i autopilotläge kan du automatiskt skala till programmets kapacitetsbehov och krav.

* **Sällan använda program:** Om du har ett program som bara används för ett par timmar flera gånger per dag eller vecka eller månad, till exempel en låg volym program / webb / blogg webbplats.

* **Utvecklings- och testdatabaser:** Om du har utvecklare som använder behållare under arbetstid men inte behöver dem på nätter eller helger. Med behållare konfigurerade i autopilotläge skalas de ned till ett minimum när de inte används.

* **Schemalagda produktionsarbetsbelastningar/frågor:** När du har en serie schemalagda begäranden/åtgärder/frågor på en enda behållare, och om det finns inaktiva perioder där du vill köra med ett absolut lågt dataflöde, kan du nu göra det enkelt. När en schemalagd fråga/begäran skickas till en behållare som konfigurerats i autopilotläge skalas den automatiskt upp så mycket som behövs och kör åtgärden.

Lösningar på tidigare problem kräver inte bara en enorm tid i genomförandet, men de introducerar också komplexitet i konfigurationen eller din kod, och kräver ofta manuella åtgärder för att hantera dem. Autopilotläge gör det möjligt för ovanstående scenarier att gå ut ur lådan, så att du inte behöver oroa dig för dessa problem längre.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Jämförelse – Behållare konfigurerade i manuellt läge jämfört med autopilotläge

|  | Behållare som konfigurerats i manuellt läge  | Behållare som konfigurerats i autopilotläge |
|---------|---------|---------|
| **Etablerat dataflöde** | Etableras manuellt. | Skalas automatiskt och omedelbart baserat på arbetsbelastningsanvändningsmönstren. |
| **Hastighetsbegränsning av begäranden/åtgärder (429)**  | Kan inträffa om förbrukningen överstiger etablerad kapacitet. | Kommer inte att ske om det dataflöde som förbrukas ligger inom det maximala dataflöde som du väljer med autopilotläge.   |
| **Kapacitetsplanering** |  Du måste göra en inledande kapacitetsplanering och tillhandahållande av dataflödet du behöver. |    Du behöver inte oroa dig för kapacitetsplanering. Systemet tar automatiskt hand om kapacitetsplanering och kapacitetshantering. |
| **Prissättning** | Manuellt etablerade RU/s per timme. | För konton i enstaka skrivregion betalar du för det dataflöde som används per timme genom att använda autopilot-RU/s per timtaxa. <br/><br/>För konton med flera skrivregioner tillkommer ingen extra kostnad för autopilot. Du betalar för det dataflöde som används per timme med samma multi-master RU/s per timtaxa. |
| **Passar bäst för arbetsbelastningstyper** |  Förutsägbara och stabila arbetsbelastningar|   Oförutsägbara och variabla arbetsbelastningar  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Skapa en databas eller en behållare med autopilotläge

Du kan konfigurera autopilot för nya databaser eller behållare när du skapar dem via Azure-portalen. Använd följande steg för att skapa en ny databas eller behållare, aktivera autopilot och ange maximalt dataflöde (RU/s).

1. Logga in på [Azure-portalen](https://portal.azure.com) eller [Azure Cosmos DB explorer.](https://cosmos.azure.com/)

1. Navigera till ditt Azure Cosmos DB-konto och öppna fliken **Data Explorer.**

1. Välj **Ny behållare.** Ange ett namn på databasen, behållaren och en partitionsnyckel. Under **Dataflöde**väljer du **autopilotalternativet** och väljer det maximala dataflöde (RU/s) som databasen eller behållaren inte kan överskrida när autopilotalternativet används.

   ![Skapa en behållare och konfigurera Autopilot-dataflöde](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Välj **OK**.

Du kan skapa en delad dataflödesdatabas med autopilotläge genom att välja alternativet **Etablera databasdataflöde.**

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Genomströmnings- och lagringsgränser för autopilot

Följande tabell visar den maximala genom- och lagringsgränserna för olika alternativ i autopilotläge:

|Maximal gräns för dataflöde  |Maximal lagringsgräns  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Nästa steg

* Granska [autopilot faq](autopilot-faq.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig hur du [etablerar dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
