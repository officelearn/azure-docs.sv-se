---
title: Så här skapar du en språkmodell med Speech Service – Microsoft Cognitive Services
description: Lär dig hur du skapar en språkmodell med Speech Service in Microsoft Cognitive Services.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 0161a691cbec45a87ade218d1707a2784d7f1cfc
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39283892"
---
# <a name="tutorial-create-a-custom-language-model"></a>Självstudie: Skapa en anpassad språkmodell

I det här dokumentet skapar du en anpassad språkmodell som du sedan kan använda tillsammans med befintliga, avancerade talmodeller från Microsoft för att lägga till röstinteraktion i ditt program.

Dokumentet beskriver följande:
> [!div class="checklist"]
> * Förbereda data
> * Importera språkdatamängden
> * Skapa den anpassade språkmodellen

Om du inte har ett Cognitive Services-konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Se till att ditt Cognitive Services-konto är anslutet till en prenumeration genom att öppna sidan [Cognitive Services Subscriptions](https://customspeech.ai/Subscriptions) (Cognitive Services-prenumerationer).

Du kan ansluta till en Speech Service-prenumeration som skapats i Azure-portalen genom att klicka på knappen **Connect existing subscription** (Anslut befintlig prenumeration).

Information om hur du skapar en Speech Service-prenumeration i Azure-portalen finns på [get-started](get-started.md)-sidan.

## <a name="prepare-the-data"></a>Förbereda data

För att kunna skapa en anpassad språkmodell för ditt program behöver du ange en lista över exempelyttranden till systemet, till exempel:

*   ”Patienten har haft nässelutslag under den gångna veckan.”
*   ”Patienten hade ett ärr från bråckkirurgi som läkt ordentligt.”

Meningarna behöver inte vara hela meningar eller grammatiskt korrekta, och de bör spegla de talade indata som systemet förväntas stöta på vid drift. De här exemplen bör spegla både stilen och innehållet i den uppgift som användarna kommer att utföra med ditt program.

Språkmodelldata ska skrivas i UTF-8 BOM. Textfilen ska innehålla ett exempel (mening, yttrande eller fråga) per rad.

Om du vill att vissa termer ska ha en högre vikt (prioritet) kan du lägga till flera uttryck som innehåller de termerna till dina data. 

De grundläggande kraven för språkdata sammanfattas i följande tabell.

| Egenskap | Värde |
|----------|-------|
| Textkodning | UTF-8 BOM|
| antal yttrande per rad | 1 |
| Maximal filstorlek | 1,5 GB |
| Kommentarer | undvik att upprepa tecken mer än fyra gånger, till exempel ”aaaaa”|
| Kommentarer | använd inga specialtecken som ”\t” eller andra UTF-8 tecken ovanför U+00A1 i [Unicode-teckentabellen](http://www.utf8-chartable.de/)|
| Kommentarer | URI:er avvisas också eftersom det inte finns något unikt sätt att uttala en URI|

När texten importeras textnormaliseras den så att den kan bearbetas av systemet. Det finns dock vissa viktiga normaliseringar som måste utföras av användaren _innan_ data överförs. Se [Riktlinjer för transkription](prepare-transcription.md) för att avgöra tillämpligt språk när du förbereder dina språkdata.

## <a name="language-support"></a>Stöd för språk

Följande språk stöds för anpassade **tal till text**-språkmodeller.

Klicka för att få en fullständig lista över [språk som stöds](supported-languages.md)

## <a name="import-the-language-data-set"></a>Importera språkdatamängden

Klicka på knappen ”Import” (Importera) på raden ”Language Datasets” (språkdatamängder) så visar webbplatsen en sida för att ladda upp en ny datamängd.

När du är redo att importera språkdatamängden loggar du in på [Speech Service Portal](https://customspeech.ai).  Klicka sedan på listrutan ”Custom Speech” (Anpassat tal) på det översta menyfliksområdet och välj ”Adaptation Data” (Anpassningsdata). Första gången du försöker ladda upp data till Speech Service visas en tom tabell med namnet ”Datasets” (Datamängder).

För att importera en ny datamängd klickar du på knappen ”Import” (Importera) på raden ”Language Datasets” (Språkdatamängder) så visar webbplatsen en sida för att ladda upp en ny datamängd. Ange ett namn och en beskrivning som hjälper dig att identifiera datamängden i framtiden, och välj nationella inställningar. Sedan använder du knappen ”Choose File” (Välj fil) för att hitta textfilen med språkdata. Efter det klickar du på ”Import” (Importera) så laddas datamängden upp. Beroende på datamängdens storlek kan importen ta flera minuter.

![testa](media/stt/speech-language-datasets-import.png)

När importen är klar kommer du tillbaka till tabellen för språkdata, och en post som motsvarar din språkdatamängd visas. Observera att den har tilldelats ett unikt ID (GUID). Data får även en status som anger det aktuella tillståndet. Statusen för data blir ”Waiting” (Väntar) när de har placerats i kö för bearbetning, ”Processing” (Bearbetar) medan de verifieras och ”Complete” (Klar) när de är redo att användas. Dataverifieringen utför en rad kontroller av texten i filen samt viss textnormalisering av data.

När statusen är ”Complete” (Klar) kan du klicka på ”View Report” (Visa rapport) om du vill se verifieringsrapporten för språkdata. Det antal yttranden som godkändes och inte godkändes av verifieringen visas tillsammans med information om de yttranden som inte godkändes. I exemplet nedan godkändes inte två exempel av verifieringen på grund av felaktiga tecken (i den här datamängden hade den första raden två tabbtecken och den andra flera tecken utanför uppsättningen med utskrivbara ASCII-tecken, medan den tredje raden var tom).

![testa](media/stt/speech-language-datasets-report.png)

När statusen för språkdatamängden är ”Complete” (Klar) kan den användas för att skapa en anpassad språkmodell.

![testa](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Skapa en anpassad språkmodell

När språkdata är klara klickar du på ”Language Models” (Språkmodeller) på listrutan ”Menu” (Meny) för att starta processen med att skapa en anpassad språkmodell. Den här sidan innehåller en tabell med namnet ”Language Models” (Språkmodeller) med dina aktuella anpassade språkmodeller. Om du inte har skapat några anpassade språkmodeller är tabellen tom. De aktuella nationella inställningarna visas i tabellen bredvid motsvarande datapost.

Du måste välja rätt nationella inställningar innan du vidtar åtgärder. De aktuella nationella inställningarna anges i tabellrubriken på alla sidor för data, modeller och distribution. Om du vill ändra de nationella inställningarna klickar du på knappen ”Change Locale” (Ändra nationella inställningar) under tabellrubriken. Då öppnas en sida för att bekräfta nationella inställningar. Klicka på ”OK” för att gå tillbaka till tabellen.

På sidan ”Create Language Model” (Skapa språkmodell) anger du ett ”Name” (Namn) och en ”Description” (Beskrivning) som hjälper dig att hålla reda på viktig information om den här modellen, till exempel den datamängd som används. Välj sedan ”Base Language Model” (Basspråkmodell) i listrutan. Den här modellen blir startpunkten för din anpassning. Det finns två basspråkmodeller att välja mellan. Search and Dictation-modellen (Språkmodell för sökning och diktamen) är lämplig för tal som riktas mot ett program, till exempel kommandon, sökfrågor och diktamen. Conversational-modellen (Konversationsmodell) är lämplig för igenkänning av tal i konversationsstil. Den här typen av tal riktas vanligtvis mot en annan person och används i kundtjänst och i möten. En ny modell som kallas ”Universal” (Universell) är också allmänt tillgänglig. Universal syftar till att hantera alla scenarier och småningom ersätta modellerna Search and Dictation och Conversational.

5.  När du i exemplet nedan har angett basspråkmodellen väljer du den språkdatamängd som du vill använda för anpassning med hjälp av listrutan ”Language Data” (Språkdata)

![testa](media/stt/speech-language-models-create2.png)

Precis som vid skapandet av den akustiska modellen kan du välja att utföra offlinetestning av den nya modellen när bearbetningen är klar. Modellutvärderingar kräver en akustisk datamängd.

För att utföra offlinetestning av din språkmodell väljer du kryssrutan bredvid ”Offline Testing” (Offlinetestning). Välj sedan en akustisk modell i listrutan. Om du inte har skapat några anpassade akustiska modeller innehåller menyn endast Microsofts akustiska basmodeller. Om du har valt en basspråkmodell för konversation behöver du använda en akustisk modell för konversation här. Om du använder en språkmodell för sökning och diktamen måste du välja en akustisk modell för sökning och diktamen.

Välj slutligen den akustiska datamängd som du vill använda för att utföra utvärderingen.

När du är redo att börja bearbeta trycker du på ”Create” (Skapa), så visas tabellen med språkmodeller. Det kommer att finnas en ny post i tabellen som motsvarar den här modellen. Statusen visar modellens tillstånd och går igenom flera tillstånd såsom ”Waiting” (Väntar), ”Processing” (Bearbetar) och ”Complete” (Klar).

När modellen har nått tillståndet ”Complete” (Klar) kan den distribueras till en slutpunkt. Om du klickar på ”View Result” (Visa resultat) visas resultatet av offlinetestning, om sådan utförs.

Om du vill ändra modellens ”Name” (Namn) eller ”Description” (Beskrivning) kan du använda länken ”Edit” (Redigera) på en lämplig rad i tabellen för språkmodeller.

## <a name="next-steps"></a>Nästa steg

- [Hämta en kostnadsfri utvärderingsprenumeration på Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Så identifierar du tal i C#](quickstart-csharp-dotnet-windows.md)
- [Git-exempeldata](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
