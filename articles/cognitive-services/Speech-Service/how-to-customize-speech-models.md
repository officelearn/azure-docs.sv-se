---
title: Anpassa tal till Text modeller | Microsoft Docs
description: Förbättra taligenkänning genom att anpassa tal till Text-modeller.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 380c585f57737c0aa4ec99303c52d4567500b5f4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354153"
---
# <a name="customize-speech-to-text-models-using-speech-service"></a>Anpassa ”tal Text” modeller med tjänsten för tal

För att hjälpa dig att uppnå bättre resultat tal tjänsten tal kan du anpassa tre modeller som används av den **tal till Text** API. Modeller tränas automatiskt från exempeldata som du anger.

| Modell | Exempeldata | Syfte |
|-------|---------------|---------|
| Ljud modellen      | Tal, text | Anpassa ljud (fonemen) som är associerade med särskilda ord. Träna en ny accent eller dialekt som talar miljö, osv. |
| Språk-modellen      | Text | Anpassa ord som är kända för tjänsten och hur de används i utterances. Lägg till termerna, lokala platsnamn, osv. |
| Uttal | Text | Förbättra igenkänning av program som krånglar ord, föreningar och förkortningar. Till exempel mappa ”finns threepio” för att identifieras som ”C3PO” |

När du skapar nya modeller, skapa en anpassad slutpunkt som använder din modell för en eller flera av de ovanstående ändamål. Du kan också välja en grundläggande modell som tillhandahålls av tjänsten tal om du vill använda, till exempel en anpassad ljud modell och inte en standardspråket modell. Du kan sedan använda anpassade slutpunkten i stället för standardslutpunkten för REST-begäranden. Varje slutpunkt har en associerad *distributions-ID* så att den kan användas med tal SDK.

Anpassning av alla modeller görs via den [anpassade tal portal](https://www.cris.ai/).

## <a name="language-support"></a>Språkstöd

Följande språk stöds för anpassad **tal till Text** språk modeller.

| Kod | Språk |
|-|-|
| sv-SE | Engelska (USA) 
| zh-CN | Kinesiska 
| SP-SP | Spanska (Spanien) 
| fr-FR | Franska (Frankrike) 
| IT-IT | Italienska 
| de-DE | Tyska
| pt-BR | Portugisiska (Brasilien)
| ru-RU | Ryska
| JP-JP | Japanska
| ar T.ex | Arabiska (Egypten)

Anpassade ljud modeller stöder endast amerikansk engelska (en-US). Kinesiska ljud datauppsättningar kan dock importeras för att testa kinesiska modeller.

Anpassade uttal stöder endast amerikansk engelska (en-US) och tyska (de-DE) just nu.

## <a name="prepare-data-sets"></a>Förbereda datauppsättningar

Varje typ av modellen kräver lite olika data och formatering, som beskrivs här.

| Modell | Vad du erbjuder      |
|-------|-----------------------|
| Ljud | En ZIP-fil som innehåller ljudfiler för fullständig utterances och en textfil som innehåller transcriptions av dessa filer. Varje rad i filen måste bestå av namnet på filen, fliken (ASCII-9) och texten.|
| Språk | En textfil som innehåller en utterance per rad. |
| Uttal | En textfil som innehåller ett uttal tips på varje rad. Varje tips är en skärm (ett ord eller förkortning), följt av en flik (ASCII-9) och talade (önskade uttal).  |

Textfiler bör följa den [text skrivfel riktlinjer](prepare-transcription.md) för modellens språk.

## <a name="prepare-audio-files"></a>Förbereda ljudfiler

Ljudfiler för ljud modeller ska registreras i en representativ plats av en mängd representativt användare (om målet är att optimera recognition för en talare), med hjälp av en mikrofon liknar dina användare har. Formatet som krävs av alla ljud prover beskrivs i den här tabellen.

| Egenskap  | Obligatoriskt värde |
|----------|------|
Filformat | RIFF (WAV)
Samplingsfrekvens | 8000 eller 16 000 Hz
Kanaler | 1 (mono)
Exempel-format | PCM, 16-bitars heltal
Filen varaktighet | Mellan 0,1 och 60 sekunder
Tystnad krage | 0,1 sekund
Arkivformat | ZIP-
Maximal arkivstorlek | 2 GB

Om du tränar en modell för mycket brus bakgrund, till exempel en fabrik eller en bil ta några sekunder för vanliga bakgrundsljud i början eller slutet av några exempel. Inkludera inte bara brus prover.

## <a name="upload-data-sets"></a>Överför datauppsättningar

Om du vill skapa en anpassad modell först ladda upp data och påbörja utbildning.

1.  Logga in på den [anpassade tal portal](https://www.cris.ai/).

1.  Välj den typ av data som du vill skapa från den **anpassade tal** menyn - anpassning Data för ljud modeller, språk-Data för språket modeller eller uttal. En lista över befintliga datauppsättningar av den typen (eventuella) visas.

1. Välj språket du genom att klicka på **ändra språk**.

1.  Klicka på **Importera nya** och ange ett namn och beskrivning för den nya datauppsättningen.

1. Välj de filer du har förberett.

1. Klicka på **importera** att överföra data och börja valideringen. Validering säkerställer att alla filer finns i rätt format. Verifieringen kan ta en stund.

## <a name="create-a-model"></a>Skapa en modell

 När din datauppsättning har verifierats, kan du träna modellen på följande sätt.

> [!NOTE]
> Uttal data behöver inte vara tränats.

1. Välj vilken typ av modell som skapas från den **anpassade tal** -menyn och klicka sedan på **Skapa nytt.**

1. Välj språk för modellen.

1. Välj en modell som bas för din nya modell. Valet av grundläggande modellen styr recognition lägen som din modell kan användas, samt fungerar som en reserv för alla data som inte finns i datauppsättningen.

1.  Välj den datamängd som modellen är skapas. En datauppsättning kan användas av valfritt antal modeller.

1. Klicka på **skapa** att påbörja en ny modell.

## <a name="test-a-model"></a>Testa en modell

Du kan testa din modell mot en uppsättning med ljud data som en del av processen modellen. Den nya modellen används för att identifiera tal i den datamängd ljudfiler och resultaten testats mot motsvarande text. Använd en annan ljud datauppsättning än den som du använde för att skapa modellen för bästa resultat.

## <a name="custom-endpoint"></a>Anpassad slutpunkt

När du har skapat anpassade ljud modeller eller språk modeller, kan du distribuera dem till en anpassad **tal till Text** slutpunkt. Det konto som har skapat en slutpunkt tillåts att göra anrop till den.

Välj för att skapa en slutpunkt **distributioner** från den **anpassade tal** menyn överst på sidan. Den **distributioner** sidan innehåller en tabell med aktuella anpassade slutpunkter, om du har skapat någon. Klicka på **Skapa nytt** att skapa en ny slutpunkt.

Välj modeller som du vill använda i den **ljud modellen** och **språk modellen** visas. Tillgängliga alternativ inkluderar alltid de grundläggande Microsoft-modellerna. Du kan inte blanda vardagliga samtalsuttryck modeller med Sök och styr modeller, så om du väljer en modell för ljud begränsas tillgängligt språk-modeller och vice versa. Du kan använda samma modeller i valfritt antal slutpunkter.

Klicka på **skapa** när du har valt modeller. Din nya slutpunkt kan ta upp till 30 minuter att etablera.

När slutpunkten är klar, väljer du den i den **distributioner** tabellen för att se URI och distribution-ID. Du kan använda anpassade slutpunkter med den [Rest API](rest-apis.md#speech-to-text) och [tal SDK](speech-sdk.md). Den [kodexempel](samples.md) innehåller ett exempel på användning av en anpassad tal till Text-slutpunkten.

## <a name="next-steps"></a>Nästa steg

- [Hämta din utvärderingsprenumeration tal](https://azure.microsoft.com/try/cognitive-services/)
- [Hur du identifierar tal i C#](quickstart-csharp-windows.md)
