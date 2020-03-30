---
title: Egenskaper för datainmatning för Azure Data Explorer
description: Lär dig mer om de olika datainmatningsegenskaper som stöds av Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109581"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Egenskaper för inmatning av azure data explorer-data 

Datainmatning är den process genom vilken data läggs till i en tabell och görs tillgängliga för frågor i Azure Data Explorer. Du lägger till egenskaper i `with` kommandot för inmatning efter nyckelordet.

## <a name="ingestion-properties"></a>Egenskaper för intag

I följande tabell visas de egenskaper som stöds av Azure Data Explorer, beskriver dem och innehåller exempel: 

|Egenskap              |Beskrivning                                              |Exempel                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |Ett strängvärde som anger hur data från källfilen mappas till de faktiska kolumnerna i tabellen. Definiera `format` värdet med relevant mappningstyp. Se [datamappningar](/azure/kusto/management/mappings).|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>(föråldrad: `avroMapping`, `csvMapping`, `jsonMapping`) |
|`ingestionMappingReference`|Ett strängvärde som anger hur data från källfilen mappas till de faktiska kolumnerna i tabellen med hjälp av ett namngivet mappningsprincipobjekt. Definiera `format` värdet med relevant mappningstyp. Se [datamappningar](/azure/kusto/management/mappings).|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>(föråldrad: `avroMappingReference`, `csvMappingReference`, `jsonMappingReference`)|
|`creationTime` |Datetime-värdet (formaterat som en ISO8601-sträng) som ska användas vid den skapande tiden för de intjesterade datastemorna. Om det inte anges`now()`används det aktuella värdet ( ). Att åsidosätta standardinställningen är användbart vid intag av äldre data, så att bevarandeprincipen tillämpas korrekt.|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|Ett booleskt värde som, om det anges, instruerar kommandot att `false`utöka tabellens schema (standard till ). Det här alternativet `.append` `.set-or-append` gäller endast kommandon och kommandon. De enda tillåtna schematilläggen har ytterligare kolumner tillagda i tabellen i slutet.|Om det ursprungliga `(a:string, b:int)`tabellschemat är , `(a:string, b:int, c:datetime, d:string)`skulle `(a:string, c:datetime)` ett giltigt schematillägg vara , men skulle inte vara giltigt|
|`folder` |För [kommandon som intas från frågefrågan,](/azure/kusto/management/data-ingestion/ingest-from-query) mappen som ska tilldelas tabellen. Om tabellen redan finns åsidosätter den här egenskapen tabellens mapp.|`with (folder="Tables/Temporary")`|
|`format` |Dataformatet (se [dataformat som stöds](ingestion-supported-formats.md)).|`with (format="csv")`|
|`ingestIfNotExists`|Ett strängvärde som, om det anges, förhindrar att inmatningen lyckas om `ingest-by:` tabellen redan har data som har taggats med en tagg med samma värde. Detta säkerställer idempotent datainmatning. Mer information finns [i intag av: taggar](/azure/kusto/management/extents-overview#ingest-by-extent-tags).|Egenskaperna `with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')` anger att om det `ingest-by:Part0001` redan finns data med taggen, slutför du inte det aktuella inmatningen. Om den inte redan finns bör det nya inmatningsstödet ha den här taggen inställd (om ett framtida inmatningsförsök försöker inta samma data igen.)|
|`ignoreFirstRecord` |Ett booleskt värde som, om värdet är `true`inställt på , anger att inmatning ska ignorera den första posten i varje fil. Den här egenskapen `CSV`är användbar för filer i och liknande format, om den första posten i filen är kolumnnamnen. Som standard `false` antas.|`with (ignoreFirstRecord=false)`|
|`persistDetails` |Ett booleskt värde som, om det anges, anger att kommandot ska spara de detaljerade resultaten (även om det lyckas) så att kommandot [.show-åtgärdsinformation](/azure/kusto/management/operations#show-operation-details) kan hämta dem. Standardvärdet `false`till .|`with (persistDetails=true)`|
|`policy_ingestiontime`|Ett booleskt värde som, om det anges, beskriver om tidsprincipen för [inmatning ska](/azure/kusto/management/ingestiontimepolicy) aktiveras i en tabell som skapas av det här kommandot. Standardvärdet är `true`.|`with (policy_ingestiontime=false)`|
|`recreate_schema` |Ett booleskt värde som, om det anges, beskriver om kommandot kan återskapa schemat för tabellen. Den här egenskapen `.set-or-replace` gäller endast kommandot. Den här egenskapen `extend_schema` har företräde framför egenskapen om båda är inställda.|`with (recreate_schema=true)`|
|`tags`|En lista med [taggar](/azure/kusto/management/extents-overview#extent-tagging) som ska associeras med intövade data, formaterad som en JSON-sträng |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|En JSON-sträng som anger vilka valideringar som ska köras under intag. Se [Datainmatning](/azure/kusto/management/data-ingestion/) för en förklaring av de olika alternativen.| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`(Detta är faktiskt standardprincipen)|
|`zipPattern`|Använd den här egenskapen när du inmatar data från lagring som har ett ZIP-arkiv. Det här är ett strängvärde som anger vilket reguljärt uttryck som ska användas när du väljer vilka filer i ZIP-arkivet som ska användas.  Alla andra filer i arkivet ignoreras.|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [datainmatning](/azure/data-explorer/ingest-data-overview)
* Läs mer om [dataformat som stöds](ingestion-supported-formats.md)
