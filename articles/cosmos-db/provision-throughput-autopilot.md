---
title: Skapa Azure Cosmos-behållare och databaser i autopilot-läge.
description: Lär dig mer om fördelar, användnings fall och hur du etablerar Azure Cosmos-databaser och behållare i autopilot-läge.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: a744ac2574f54b0c2934d440ddf5c48e54304595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445121"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Skapa Azure Cosmos-behållare och databaser i autopilot-läge (för hands version)

Med Azure Cosmos DB kan du etablera dataflöde i containrar i antingen manuellt läge eller Autopilot-läge. I den här artikeln beskrivs fördelarna och användningsfallen i Autopilot-läge.

> [!NOTE]
> Autopilot-läget är för närvarande tillgängligt i en offentlig för hands version. Du kan bara [Aktivera autopilot för nya databaser och behållare](#create-a-database-or-a-container-with-autopilot-mode) . Den är inte tillgänglig för befintliga behållare och databaser.

Utöver manuell etablering av dataflöde kan du nu konfigurera Azure Cosmos-containrar i Autopilot-läge. Azure Cosmos-behållare och databaser som kon figurer ATS i autopilot-läge kommer **automatiskt att skala det etablerade data flödet baserat på dina program behov utan att kompromissa med service avtal.**

Du behöver inte längre hantera det etablerade data flödet eller referens begränsnings problemen manuellt. Azure Cosmos-behållare som kon figurer ATS i autopilot-läge kan skalas direkt som svar på arbets belastningen utan att det påverkar tillgänglighet, svars tid, data flöde eller prestanda för arbets belastningen globalt. Under hög användning kan Azure Cosmos-behållare som kon figurer ATS i autopilot-läge skalas upp eller ned utan att det påverkar pågående åtgärder.

När du konfigurerar behållare och databaser i autopilot-läge måste du ange det maximala data flödet `Tmax` inte överskridas. Behållare kan sedan skalas direkt baserat på arbets belastnings behoven inom `0.1*Tmax < T < Tmax`s intervallet. Med andra ord skalar behållare och databaser direkt utifrån arbets belastnings behoven, från så lågt som 10% av det maximala data flödes värde som du har konfigurerat och upp till det konfigurerade maximala data flöde svärdet. Du kan ändra inställningen för maximal data flöde (Tmax) för autopilot-databasen eller containern vid en viss tidpunkt. Med alternativet autopilot är 400 RU/s minsta data flöde per behållare eller databas inte längre tillämpligt.

Under för hands versionen av autopilot, för det angivna maximala data flödet i behållaren eller databasen, tillåter systemet drift inom den beräknade lagrings gränsen. Om lagrings gränsen överskrids justeras det maximala data flödet automatiskt till ett högre värde. När du använder data flöde på databas nivå med autopilot-läge, beräknas antalet behållare som tillåts i en databas som: (0,001 * Max data flöde). Om du till exempel etablerar 20 000 autopiloten RU/s kan databasen ha 20 behållare.

## <a name="benefits-of-autopilot-mode"></a>Fördelar med autopilot-läge

Azure Cosmos-behållare som kon figurer ATS i autopilot-läge har följande fördelar:

* **Enkel:** Behållare i autopilot-läge tar bort komplexiteten för att hantera etablerade data flöden (ru: er) och kapacitet manuellt för olika behållare.

* **Skalbarhet:** Behållare i autopilot-läge skalar sömlöst den allokerade data flödes kapaciteten vid behov. Det uppstår inget avbrott i klient anslutningar, program och de påverkar inte några befintliga service avtal.

* **Kostnads effektiv:** När du använder Azure Cosmos-behållare som kon figurer ATS i autopilot-läge betalar du bara för de resurser som dina arbets belastningar behöver per timme.

* **Hög tillgänglighet:** Azure Cosmos-behållare i autopilot-läge använder samma globalt distribuerade, feltoleranta och hög tillgängliga Server del för att säkerställa data hållbarhet och hög tillgänglighet alltid.

## <a name="use-cases-of-autopilot-mode"></a>Användnings fall i autopilot-läge

Användnings fall för Azure Cosmos-behållare som kon figurer ATS i autopilot-läge är:

* **Variabla arbets belastningar:** När du kör ett program med hög användning med en högsta användning på 1 timme till flera timmar om dagen eller flera gånger per år. Exempel är program för personal, budgetering och drift rapportering. För sådana scenarier kan behållare som kon figurer ATS i autopilot-läge användas, du behöver inte längre etablera manuellt för antingen hög kapacitet eller genomsnitts kapacitet.

* **Oförutsägbara arbets belastningar:** När du kör arbets belastningar där det finns en databas användning under dagen, men även toppar av aktiviteter som är svåra att förutsäga. Ett exempel är en trafik plats som ser en överspänning av aktivitet när väder prognoser ändras. Behållare som kon figurer ATS i autopilot-läge justera kapaciteten för att uppfylla behoven för programmets högsta belastning och skala ned när aktivitetens överbelastning är över.

* **Nya program:** Om du distribuerar ett nytt program och är osäker på hur mycket allokerat data flöde (t. ex. hur många ru: er) du behöver. Med behållare som kon figurer ATS i autopilot-läge kan du automatiskt skala till kapacitets behoven och kraven för ditt program.

* **Program som används sällan:** Om du har ett program som bara används för några timmar flera gånger per dag eller vecka eller månad, till exempel en program-/webb-/blogg webbplats med låg volym.

* **Utvecklings-och test databaser:** Utvecklare använder Azure Cosmos-kontona under arbets tid, men behöver de inte på nätter eller helger. Med behållare som kon figurer ATS i autopilot-läge, skalas de ned till lägsta möjliga användning.

* **Schemalagda produktions arbets belastningar/frågor:** När du har en serie med schemalagda begär Anden/åtgärder/frågor på en enda behållare, och om det finns inaktiva perioder där du vill köra med ett absolut lågt data flöde, kan du nu enkelt göra det. När en schemalagd fråga/begäran skickas till en behållare som kon figurer ATS i autopilot-läge, skalas den automatiskt upp så mycket som behövs och åtgärden körs.

Lösningar på tidigare problem kräver inte bara en enorma del av tiden i implementeringen, men de introducerar också komplexitet i konfigurationen eller din kod och kräver ofta manuella åtgärder för att åtgärda dem. I autopilot-läget aktive ras scenarierna ovan, så att du inte behöver bekymra dig om problemen längre.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Jämförelse – behållare som kon figurer ATS i manuellt läge kontra autopilot-läge

|  | Behållare som kon figurer ATS i manuellt läge  | Behållare som kon figurer ATS i autopilot-läge |
|---------|---------|---------|
| **Allokerat data flöde** | Manuellt etablerade | Automatiskt och automatiskt skalas baserat på användnings mönster för arbets belastning. |
| **Hastighets begränsning av begär Anden/åtgärder (429)**  | Kan inträffa om förbrukningen överskrider den etablerade kapaciteten. | Sker inte om det data flöde som förbrukas är inom det maximala data flöde som du väljer med autopilot-läge.   |
| **Kapacitetsplanering** |  Du måste utföra en första kapacitets planering och tillhandahålla det data flöde du behöver. |    Du behöver inte bekymra dig om kapacitets planering. Systemet tar automatiskt hand om kapacitets planering och kapacitets hantering. |
| **Prissättning** | Manuellt etablerade RU/s per timme. | För enstaka Skriv regions konton betalar du för det data flöde som används per timme med hjälp av den autopiloten RU/s per tim pris. <br/><br/>För konton med flera Skriv regioner finns det ingen extra kostnad för autopilot. Du betalar för det data flöde som används per timme med samma taxa för flera huvud-RU/s per timme. |
| **Passar bäst för arbets belastnings typer** |  Förutsägbara och stabila arbets belastningar|   Oförutsedda och varierande arbets belastningar  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Skapa en databas eller en behållare med autopilot-läge

Du kan konfigurera autopilot för nya databaser eller behållare när du skapar dem via Azure Portal. Använd följande steg för att skapa en ny databas eller behållare, aktivera autopilot och ange maximalt data flöde (RU/s).

1. Logga in på [Azure Portal](https://portal.azure.com) eller [Azure Cosmos Explorer.](https://cosmos.azure.com/)

1. Gå till ditt Azure Cosmos-konto och öppna fliken **datautforskaren** .

1. Välj **ny behållare.** Ange ett namn för din databas, behållare och en partitionsnyckel. Välj alternativet **autopilot** och välj det maximala data flöde (ru/s) som databasen eller behållaren inte får överskrida när du använder alternativet autopilot.

   ![Skapa en behållare i autopilot-läge](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Välj **OK**.

Du kan skapa en delad data flödes databas med autopilot-läge genom att välja **data flödes alternativet etablera databas** .

## <a id="autopilot-limits"></a>Data flödes-och lagrings gränser för autopilot

I följande tabell visas de maximala gränserna för alla och lagrings gränser för olika alternativ i autopilot-läge:

|Maximal data flödes gräns  |Maximal lagrings gräns  |
|---------|---------|
|4000 RU/s  |   50 GB    |
|20 000 RU/s  |  200 GB  |
|100 000 RU/s    |  1 TB   |
|500 000 RU/s    |  5 TB  |

## <a name="next-steps"></a>Nästa steg

* Läs [vanliga frågor och svar om autopilot](autopilot-faq.md).
* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).
