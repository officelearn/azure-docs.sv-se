---
title: Förstå resurs uppsättningar
description: I den här artikeln beskrivs vilka resurs uppsättningar som är och hur Azure-avdelningens kontroll skapar dem.
author: yaronyg
ms.author: yarong
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/19/2020
ms.openlocfilehash: 55efa9443fd59b66a7677c9c460e473715f201df
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553984"
---
# <a name="understanding-resource-sets"></a>Förstå resurs uppsättningar

Den här artikeln hjälper dig att förstå hur Azure-avdelningens kontroll använder resurs uppsättningar för att mappa data till gångar till logiska resurser.

## <a name="background-info"></a>Bakgrunds information

Vid skalning av data bearbetnings system lagras vanligt vis en enda tabell på en disk som flera filer. Det här konceptet representeras i Azure avdelningens kontroll med hjälp av resurs uppsättningar. En resurs uppsättning är ett enda objekt i katalogen som representerar ett stort antal till gångar i lagringen.

Anta till exempel att ditt Spark-kluster har sparat en DataFrame i en ADLS Gen2-datakälla. Även om tabellen i Spark ser ut som en enda logisk resurs, finns det troligen tusentals Parquet-filer på disken som representerar en partition av den totala DataFrame innehåll. IoT-data och webb logg data har samma utmaning. Anta att du har en sensor som utvärderar loggfiler flera gånger om en sekund. Det tar inte lång tid förrän du har hundratals tusentals loggfiler från den enskilda sensorn.

Azure avdelningens kontroll använder resurs uppsättningar för att åtgärda utmaningen med att mappa ett stort antal data till gångar till en enda logisk resurs.

## <a name="how-azure-purview-detects-resource-sets"></a>Hur Azure avdelningens kontroll identifierar resurs uppsättningar

Azure avdelningens kontroll stöder enbart identifiering av resurs uppsättningar i Azure-blobbar, ADLS Gen1 och ADLS Gen2.

Azure avdelningens kontroll identifierar automatiskt resurs uppsättningar med hjälp av en funktion som kallas automatisk identifiering av resurs uppsättning. Den här funktionen ser till att alla data som matas in via skanning och jämförs med en uppsättning definierade mönster.

Anta till exempel att du skannar en data källa vars URL är `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure-avdelningens kontroll tittar på Sök vägs segmenten och bestämmer om de matchar några inbyggda mönster. Den har inbyggda mönster för GUID, siffror, datum format, lokaliserings koder (till exempel en-US) och så vidare. I det här fallet matchar nummer mönstret *23*. Azure-avdelningens kontroll förutsätter att den här filen ingår i en resurs uppsättning med namnet `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Eller, för en URL som `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` , matchar Azure avdelningens kontroll både lokaliserings mönstret och antalet mönster, vilket skapar en resurs uppsättning med namnet `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Med den här strategin mappar Azure avdelningens kontroll följande resurser till samma resurs uppsättning `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

> [!Note]
> Azure Data Lake Storage Gen2 är nu allmänt tillgänglig. Vi rekommenderar att du börjar använda den idag. Mer information finns på [produkt sidan](https://azure.microsoft.com/en-us/services/storage/data-lake-storage/).

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Filtyper som Azure-avdelningens kontroll inte kan identifiera som resurs uppsättningar

Avdelningens kontroll försöker avsiktligt inte klassificera de flesta dokument fil typer som Word, Excel eller PDF som resurs uppsättningar. Undantaget är CSV: er eftersom det är ett gemensamt partitionerat fil format.

## <a name="how-azure-purview-scans-resource-sets"></a>Hur Azure avdelningens kontroll genomsöker resurs uppsättningar

När Azure avdelningens kontroll identifierar resurser som den anser är en del av en resurs uppsättning växlar den från en fullständig genomsökning till en exempel genomsökning. I en exempels ökning öppnar den bara en delmängd av de filer som den tror är i resurs uppsättningen. För varje fil öppnas den med sitt schema och dess klassificerare körs. Azure-avdelningens kontroll hittar sedan den senaste resursen bland de öppna resurserna och använder resursens schema och klassificeringar i posten för hela resurs uppsättningen i katalogen.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Vad Azure avdelningens kontroll lagrar om resurs uppsättningar

Förutom Single schema och klassificeringar lagrar Azure avdelningens kontroll följande information om resurs uppsättningar:

- Data från den senaste partitionens resurs som den djupade genomsöks.
- Sammanställd information om de diskpartitioner som utgör resurs uppsättningen.
- Ett antal partitioner som visar hur många partitioner som hittas.
- Ett schema antal som visar hur många unika scheman som finns i den exempel uppsättning som det gjorde djupt sökningar på. Värdet är antingen ett tal mellan 1 – 5, eller värden som är större än 5, 5 +.
- En lista med partitionstyper när mer än en enda partitionstyp ingår i resurs uppsättningen. En IoT-sensor kan till exempel mata ut både XML-och JSON-filer, men båda är logiskt indelade i samma resurs uppsättning.

## <a name="built-in-resource-set-patterns"></a>Inbyggda resurs uppsättnings mönster

Azure avdelningens kontroll stöder följande resurs uppsättnings mönster. Dessa mönster kan visas som ett namn i en katalog eller som en del av ett fil namn.

| Mönster namn | Visningsnamn | Beskrivning |
|--------------|--------------|-------------|
| GUID         | LED       | En globalt unik identifierare som definieras i [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Antal       | M          | En eller flera siffror. |
| Datum-/tids format | M     | Azure avdelningens kontroll stöder olika typer av datum-och tids format, men alla minskas till en serie med {N} s. |
| 4ByteHex     | HEXADECIMAL        | Ett fyrsiffrigt hexadecimalt tal. |
| Lokalisering | Loc        | En språkkod som definieras i [BCP 47](https://tools.ietf.org/html/bcp47). Azure avdelningens kontroll stöder taggar som innehåller antingen ett bindestreck (-) eller ett under streck (_). Till exempel en_ca och en-ca. |

## <a name="issues-with-resource-sets"></a>Problem med resurs uppsättningar

Även om resurs uppsättningarna fungerar bra i de flesta fall kan du stöta på följande problem, där Azure-avdelningens kontroll:

- Markerar en till gång felaktigt som en resurs uppsättning
- Placerar en till gång i fel resurs uppsättning
- Markerar en till gång felaktigt som en resurs uppsättning

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Data Catalog finns i [snabb start: skapa ett Azure avdelningens kontroll-konto](create-catalog-portal.md).
