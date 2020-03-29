---
title: Dokumentformat och namngivningskonventioner – anpassad översättare
titleSuffix: Azure Cognitive Services
description: Det här är en guide om dokumentformat och namngivningskonvention i Custom Translator. Det här konceptet hjälper till att hantera dokumentnamn bättre abd undvika att namnge konflikter.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41b15cc998a7bacd033ef2fe083fc99f1bff0286
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595851"
---
# <a name="document-formats-and-naming-convention-guidance"></a>Vägledning för dokumentformat och namngivningskonvention

Alla filer som används för anpassad översättning måste vara minst **fyra** tecken långa.

Den här tabellen innehåller alla filformat som stöds som du kan använda för att skapa översättningssystemet:

| Format            | Tillägg   | Beskrivning                                                                                                                                                                                                                                                                    |
|-------------------|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| XLIFF (OLIKA)             | . Xlf. XLIFF (OLIKA) | Ett parallellt dokumentformat, export av översättningsminnessystem. De språk som används definieras i filen.                                                                                                                                                              |
| Tmx               | . Tmx         | Ett parallellt dokumentformat, export av översättningsminnessystem. De språk som används definieras i filen.                                                                                                                                                              |
| Zip               | . Zip         | ZIP är ett arkivfilformat.                                                                                                                                                                                                        |
| Locstudio (på plats)         | . Lcl         | Ett Microsoft-format för parallella dokument                                                                                                                                                                                                                                      |
| Microsoft Word    | . Docx        | Microsoft Word-dokument                                                                                                                                                                                                                                                        |
| Adobe Acrobat     | . Pdf         | Bärbart dokument i Adobe Acrobat                                                                                                                                                                                                                                                |
| HTML              | . Html. HTM ( HTM )  | HTML-dokument                                                                                                                                                                                                                                                                  |
| Textfil         | . Txt         | UTF-16 eller UTF-8 kodade textfiler. Filnamnet får inte innehålla japanska tecken.                                                                                                                                                                                        |
| Justerad textfil | . Justera       | Tillägget `.ALIGN` är ett speciellt tillägg som du kan använda om du vet att meningarna i dokumentparet är perfekt justerade. Om du `.ALIGN` tillhandahåller en fil justeras inte meningarna åt dig. |
| Excel-fil        | . Xlsx        | Excel-fil (2013 eller senare). Kalkylbladets första rad/rad ska vara språkkod.                                                                                                                                                                                                                                                      |

## <a name="dictionary-formats"></a>Ordlisteformat

För ordlistor stöder Custom Translator alla filformat som stöds för utbildningsuppsättningar. Om du använder en Excel-ordlista ska den första raden/raden i kalkylbladet vara språkkoder.

## <a name="zip-file-formats"></a>Zip-filformat

Dokument kan grupperas i en enda zip-fil och laddas upp. Den anpassade översättaren stöder zip-filformat (ZIP, GZ och TGZ).

Varje dokument i zip-filen med tillägget TXT, HTML, HTM, PDF, DOCX, ALIGN måste följa den här namngivningskonventionen:

{dokumentnamn} \_{språkkod} där {dokumentnamn} är namnet på dokumentet, {språkkod} är ISO LanguageID (två tecken), vilket anger att dokumentet innehåller meningar på det språket. Det måste finnas ett understreck (_) före språkkoden.

Om du till exempel vill ladda upp två parallella dokument i en zip för en engelsk till spanskt system ska filerna heta "data_en" och "data_es".

Översättningsminnesfiler (TMX, XLF, XLIFF, LCL, XLSX) behöver inte följa den specifika språknamngivningskonventionen.  

## <a name="next-steps"></a>Nästa steg

- Läs om [projektet](workspace-and-project.md#what-is-a-custom-translator-project) för att skapa och hantera dem.
