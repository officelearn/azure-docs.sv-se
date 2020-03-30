---
title: Felsöka dataflöden
description: Lär dig hur du felsöker problem med dataflödet i Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472136"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Felsöka dataflöden i Azure Data Factory

I den här artikeln beskrivs vanliga felsökningsmetoder för dataflöden i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Felkod: DF-Executor-SourceInvalidPayload
- **Meddelande**: Dataförhandsgranskning, felsökning och körning av pipeline-dataflöde misslyckades eftersom behållaren inte finns
- **Orsaker**: När datauppsättningen innehåller en behållare som inte finns i lagringen
- **Rekommendation**: Kontrollera att behållaren som refereras i datauppsättningen finns eller är tillgänglig.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Felkod: DF-Executor-SystemImplicitCartesian

- **Meddelande**: Implicit cartesian produkt för INNER-koppling stöds inte, använd CROSS JOIN istället. Kolumner som används i kopplingen bör skapa en unik nyckel för rader.
- **Orsaker**: Implicit cartesian produkt för INNER koppling mellan logiska planer stöds inte. Om kolumnerna som används i kopplingen skapar den unika nyckeln krävs minst en kolumn från båda sidor av relationen.
- **Rekommendation:** För icke-jämställdhetsbaserade kopplingar måste du välja ANPASSAD CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Felkod: DF-Executor-SystemInvalidJson

- **Meddelande**: JSON-tolkningsfel, kodning utan stöd eller flerradslinje
- **Orsaker:** Möjliga problem med JSON-filen: kodning utan stöd, skadade byte eller användning av JSON-källa som ett enda dokument på många kapslade linjer
- **Rekommendation**: Kontrollera att JSON-filens kodning stöds. Expandera JSON-inställningar i källomvandlingen som använder en JSON-datauppsättning och aktivera "Ett enda dokument".
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Felkod: DF-Executor-BroadcastTimeout

- **Meddelande:** Broadcast join timeout-fel, se till att broadcast-strömmen producerar data inom 60 sekunder i felsökningskörningar och 300 sekunder i jobbkörningar
- **Orsaker:** Sändningen har en standardtidsgränsen på 60 sekunder i felsökningskörningar och 300 sekunder i jobbkörningar. Stream som valts för sändning verkar vara för stor för att producera data inom den här gränsen.
- **Rekommendation**: Undvik att sända stora dataströmmar där bearbetningen kan ta mer än 60 sekunder. Välj en mindre ström som ska sändas i stället. Stora SQL/DW-tabeller och källfiler är vanligtvis felaktiga kandidater.

### <a name="error-code-df-executor-conversion"></a>Felkod: DF-executor-konvertering

- **Meddelande**: Konvertering till ett datum eller en tid misslyckades på grund av ett ogiltigt tecken
- **Orsaker:** Data är inte i det förväntade formatet
- **Rekommendation**: Använd rätt datatyp

### <a name="error-code-df-executor-invalidcolumn"></a>Felkod: DF-Executor-InvalidColumn

- **Meddelande**: Kolumnnamn måste anges i frågan, ange ett alias om du använder en SQL-funktion
- **Orsaker**: Inget kolumnnamn har angetts
- **Rekommendation**: Ange ett alias om du använder en SQL-funktion som min()/max(), etc.

## <a name="general-troubleshooting-guidance"></a>Allmän felsökningsvägledning

1. Kontrollera status för dina datauppsättningsanslutningar. I varje käll- och sink-omvandling besöker du den länkade tjänsten för varje datauppsättning som du använder och testar anslutningar.
1. Kontrollera status för fil- och tabellanslutningarna från dataflödesdesignern. Aktivera Felsökning och klicka på Förhandsgranskning av data på källomvandlingarna för att säkerställa att du kan komma åt dina data.
1. Om allt ser bra ut från förhandsgranskning av data går du till Pipeline-designern och placerar dataflödet i en pipeline-aktivitet. Felsök pipelinen för ett end-to-end-test.

## <a name="next-steps"></a>Nästa steg

Mer felsökningshjälp finns i följande resurser:
*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Begäran om datafabriksfunktion](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Spill forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information om Data Factory](https://twitter.com/hashtag/DataFactory)
*  [ADF-mappning av dataflöden Prestandaguide](concepts-data-flow-performance.md)
