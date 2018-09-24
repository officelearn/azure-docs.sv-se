---
title: Självstudie om att skapa en språkmodell med Custom Speech Service – Microsoft Cognitive Services | Microsoft Docs
description: I den här självstudien lär du dig att skapa språkmodell med Custom Speech Service i Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 29f5c5efb78e85e265b56cba9ba20daa123d334e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961050"
---
# <a name="tutorial-create-a-custom-language-model"></a>Självstudie: Skapa en anpassad språkmodell

I den här självstudien skapar du en anpassad språkmodell för textfrågor eller yttranden som du förväntar dig att användare säger eller skriver i ett program. Du kan sedan använda den här anpassade språkmodellen tillsammans med befintliga, avancerade talmodeller från Microsoft för att lägga till röstinteraktion i ditt program.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Förbereda data
> * Importera språkdatamängden
> * Skapa den anpassade språkmodellen

Om du inte har ett Cognitive Services-konto kan du skapa ett [kostnadsfritt konto](https://cris.ai) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Se till att ditt Cognitive Services-konto är anslutet till en prenumeration genom att öppna sidan [Cognitive Services Subscriptions](https://cris.ai/Subscriptions) (Cognitive Services-prenumerationer).

Om det inte finns några prenumerationer kan Cognitive Services skapa ett konto åt dig om du klickar på knappen **Get free subscription** (Skaffa kostnadsfri prenumeration). Alternativt kan du ansluta till en Custom Search Service-prenumeration som skapats i Azure-portalen genom att klicka på knappen **Connect existing subscription** (Anslut befintlig prenumeration).

Information om hur du skapar en Custom Search Service-prenumeration i Azure-portalen finns på sidan om att [skapa ett Cognitive Services APIs-konto i Azure-portalen](../../cognitive-services-apis-create-account.md).

## <a name="prepare-the-data"></a>Förbereda data

För att kunna skapa en anpassad språkmodell för ditt program behöver du ange en lista över exempelyttranden till systemet, till exempel:

*   ”Han har haft nässelutslag under den gångna veckan.”
*   ”Patienten hade ett ärr från bråckkirurgi som läkt ordentligt.”

Meningarna behöver inte vara hela meningar eller grammatiskt korrekta, och de bör spegla de talade indata som du förväntar dig att systemet stöter på vid drift. De här exemplen bör spegla både stilen och innehållet i den uppgift som användarna kommer att utföra med ditt program.

Språkmodelldata ska skrivas i en oformaterad textfil med antingen US-ASCII eller UTF-8, beroende på de nationella inställningarna. För en-US stöds båda kodningarna. För zh-CN stöds endast UTF-8 (BOM är valfritt). Textfilen ska innehålla ett exempel (mening, yttrande eller fråga) per rad.

Om du vill att vissa meningar ska ha en högre vikt (prioritet) kan du lägga till dem flera gånger i dina data. En bra antal repetitioner är 10–100. Om du normaliserar till 100 kan du enkelt vikta meningar i förhållande till detta.

De grundläggande kraven för språkdata sammanfattas i följande tabell.

| Egenskap | Värde |
|----------|-------|
| Textkodning | en-US: USA ACSII eller UTF-8 eller zh-CN: UTF-8|
| antal yttrande per rad | 1 |
| Maximal filstorlek | 200 MB |
| Kommentarer | undvik att upprepa tecken mer än 4 gånger, till exempel ”aaaaa”|
| Kommentarer | använd inga specialtecken som ”\t” eller andra UTF-8 tecken ovanför U+00A1 i [Unicode-teckentabellen](http://www.utf8-chartable.de/)|
| Kommentarer | URI:er avvisas också eftersom det inte finns något unikt sätt att uttala en URI|

När texten importeras textnormaliseras den så att den kan bearbetas av systemet. Det finns dock vissa viktiga normaliseringar som måste utföras av användaren _innan_ data överförs. Se [Riktlinjer för transkription](cognitive-services-custom-speech-transcription-guidelines.md) för att avgöra lämpliga språk när du förbereder dina språkdata.

## <a name="import-the-language-data-set"></a>Importera språkdatamängden

Klicka på knappen ”Import” (Importera) på raden ”Acoustic Datasets” (Akustiska datamängder) så visar webbplatsen en sida för att ladda upp en ny datamängd.

När du är redo att importera språkdatamängden loggar du in på [Custom Speech Service Portal](https://cris.ai).  Klicka sedan på listrutan ”Custom Speech” (anpassat tal) på det översta menyfliksområdet och välj ”Adaptation Data” (Anpassningsdata). Om det här är första gången du laddar upp data till Custom Speech Service visas en tom tabell med namnet ”Datasets” (Datamängder).

För att importera en ny datamängd klickar du på knappen ”Import” (Importera) på raden ”Language Datasets” (Språkdatamängder) så visar webbplatsen en sida för att ladda upp en ny datamängd. Ange ett namn och en beskrivning som hjälper dig att identifiera datamängden i framtiden. Sedan använder du knappen ”Choose File” (Välj fil) för att hitta textfilen med språkdata. Efter det klickar du på ”Import” (Importera) så laddas datamängden upp. Beroende på datamängdens storlek kan det ta flera minuter.

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

När importen är klar kommer du tillbaka till tabellen för språkdata, och en post som motsvarar din språkdatamängd visas. Observera att den har tilldelats ett unikt ID (GUID). Data får även en status som anger det aktuella tillståndet. Statusen för data blir ”Waiting” (Väntar) när de har placerats i kö för bearbetning, ”Processing” (Bearbetar) medan de verifieras och ”Complete” (Klar) när de är redo att användas. Dataverifieringen utför en rad kontroller av texten i filen samt viss textnormalisering av data.

När statusen är ”Complete” (Klar) kan du klicka på ”View Report” (Visa rapport) om du vill se verifieringsrapporten för språkdata. Det antal yttranden som godkändes och inte godkändes av verifieringen visas tillsammans med information om de yttranden som inte godkändes. I exemplet nedan godkändes inte två exempel av verifieringen på grund av felaktiga tecken (i den här datamängden hade det första exemplet två uttryckssymboler och det andra flera tecken utanför uppsättningen med utskrivbara ASCII-tecken).

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

När statusen för språkdatamängden är ”Complete” (Klar) kan den användas för att skapa en anpassad språkmodell.

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Skapa en anpassad språkmodell

När språkdata är klara klickar du på ”Language Models” (Språkmodeller) på listrutan ”Menu” (Meny) för att starta processen med att skapa en anpassad språkmodell. Den här sidan innehåller en tabell med namnet ”Language Models” (Språkmodeller) med dina aktuella anpassade språkmodeller. Om du inte har skapat några anpassade språkmodeller är tabellen tom. Den aktuella nationella inställningen visas i tabellrubriken. Om du vill skapa en språkmodell för ett annat språk klickar du på ”Change Locale” (Ändra nationella inställningar). Mer information om språk som stöds finns i avsnittet [Changing Locale](cognitive-services-custom-speech-change-locale.md) (Ändra nationella inställningar). För att skapa en ny modell klickar du på länken ”Create New” (Skapa ny) under tabellrubriken.

På sidan ”Create Language Model” (Skapa språkmodell) anger du ett ”Name” (Namn) och en ”Description” (Beskrivning) som hjälper dig att hålla reda på viktig information om den här modellen, till exempel den datamängd som används. Välj sedan ”Base Language Model” (Basspråkmodell) i listrutan. Den här modellen blir startpunkten för din anpassning. Det finns två basspråkmodeller att välja mellan. _Microsoft Search och Dictation LM_ (Microsofts språkmodell för sökning och diktamen) är lämplig för tal som riktas mot ett program, till exempel kommandon, sökfrågor och diktamen. _Microsoft Conversational LM_ (Microsofts språkmodell för konversation) är lämplig för igenkänning av tal i konversationsstil. Den här typen av tal riktas vanligtvis mot en annan person och används i kundtjänst och i möten.

När du har angett basspråkmodellen väljer du den språkdatamängd som du vill använda för anpassning med hjälp av listrutan ”Language Data” (Språkdata)

![testa](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Precis som vid skapandet av den akustiska modellen kan du välja att utföra offlinetestning av den nya modellen när bearbetningen är klar. Eftersom det här är en utvärdering av prestanda för tal till text kräver offlinetestning en akustisk datamängd.

För att utföra offlinetestning av din språkmodell väljer du kryssrutan bredvid ”Offline Testing” (Offlinetestning). Välj sedan en akustisk modell i listrutan. Om du inte har skapat några anpassade akustiska modeller innehåller menyn endast Microsofts akustiska basmodeller. Om du har valt en basspråkmodell för konversation behöver du använda en akustisk modell för konversation här. Om du använder en språkmodell för sökning och diktamen måste du välja en akustisk modell för sökning och diktamen.

Välj slutligen den akustiska datamängd som du vill använda för att utföra utvärderingen.

När du är redo att börja bearbeta trycker du på ”Create” (Skapa). Då kommer du tillbaka till tabellen för språkmodeller. Det kommer att finnas en ny post i tabellen som motsvarar den här modellen. Statusen visar modellens tillstånd och går igenom flera tillstånd såsom ”Waiting” (Väntar), ”Processing” (Bearbetar) och ”Complete” (Klar).

När modellen har nått tillståndet ”Complete” (Klar) kan den distribueras till en slutpunkt. Om du klickar på ”View Result” (Visa resultat) visas resultatet av offlinetestning, om sådan utförs.

Om du vill ändra modellens ”Name” (Namn) eller ”Description” (Beskrivning) kan du använda länken ”Edit” (Redigera) på en lämplig rad i tabellen för språkmodeller.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du utvecklat en anpassad språkmodell för användning med text. Om du vill skapa en anpassad akustisk modell för användning med ljudfiler och transkriptioner fortsätter du till självstudien om att skapa en akustisk modell.

> [!div class="nextstepaction"]
> [Skapa en anpassad akustisk modell](cognitive-services-custom-speech-create-acoustic-model.md)
