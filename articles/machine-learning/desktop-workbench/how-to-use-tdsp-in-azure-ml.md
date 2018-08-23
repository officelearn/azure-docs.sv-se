---
title: Strukturera projekt med mallen Team Data Science Process | Microsoft Docs
description: Hur ska instansieras Team Data Science Process TDSP ()-mallar i Azure Machine Learning-projekt struktur för samarbete
services: machine-learning
documentationcenter: ''
author: deguhath
ms.author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.openlocfilehash: 05cb2a62cf0f001012f5faa022de233d7cbdce97
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42062091"
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Strukturera projekt med mallen Team Data Science Process

Det här dokumentet innehåller instruktioner om hur du skapar dataforskningsprojekt i Azure Machine Learning med mallar för Team Data Science Process (TDSP). Dessa mallar hjälpa dig att strukturera projekt för samarbete och reproducerbarhet. 


## <a name="what-is-the-team-data-science-process"></a>Vad är TDSP (Team Data Science Process)?
TDSP är en flexibel, iterativ, data science process för att köra och leverera lösningar för avancerade analyser. Det är utformat för att förbättra samarbete och effektiviteten för data science team i företagsorganisationer. Den stöder dessa mål med fyra nyckelkomponenter:

   * En vanlig [livscykeln för datavetenskap](../team-data-science-process/lifecycle.md) definition.
   * En standardiserad projektstruktur [projektet dokumentation och rapportering mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * En infrastruktur och resurser för projektkörning av, till exempel, en beräknings- och infrastruktur och kod databaser.
   * [Verktyg och hjälpmedel](https://github.com/Azure/Azure-TDSP-Utilities) för datavetenskap projektet aktiviteter, till exempel:
      - Samarbetsfunktioner versionskontroll
      - Kodgranskning
      - Datagranskning och modellering
      - Planera arbete

En fullständig beskrivning av TDSP finns i den [Team Data Science Process översikt](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Varför ska du använda TDSP struktur och mallar?
Standardisering av strukturen, livscykel och dokumentation om dataforskningsprojekt är nyckeln till att underlätta effektivt samarbete på data science-team. Skapa machine learning-projekt med TDSP-mallen för att ge sådana ett ramverk för samordnad samarbete.

Tidigare släppte vi en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för att uppnå dessa mål. Men det var inte möjligt, fram till nu att skapa en instans av TDSP struktur och mallar i ett verktyg för data science. Nu är det möjligt att skapa en machine learning-projekt som instantierar TDSP struktur och dokumentation mallar. 

## <a name="things-to-note-before-creating-a-new-project"></a>Saker att tänka på innan du skapar ett nytt projekt
Granska följande *innan* du skapar ett nytt projekt:
* Granska TDSP Machine Learning [mallen](https://aka.ms/tdspamlgithubrepo).
* Innehållet (annat än vad som redan finns i dokumentmappen ””) måste vara mindre än 25 MB i storlek. Se anmärkningen efter den här listan.
* Exemplet\_datamapp är endast avsedd för små filer (mindre än 5 MB) som du kan använda för att testa din kod eller starta tidig utveckling.
* Lagring av filer, till exempel Word och PowerPoint-filer, öka storleken på dokumentmappen ”” avsevärt. Vi rekommenderar att som du kan söka efter en samarbetsfunktioner Wiki [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), eller annan samarbetsfunktioner resurs att lagra dessa filer.
* Läs hur du hanterar stora filer och utdata i Machine Learning, [spara ändringar och ta itu med stora filer](http://aka.ms/aml-largefiles).

> [!NOTE]
> Alla dokumentation-relaterat innehåll (text, markdowns, bilder och andra dokumentfiler) som är *inte* används under projektkörning, andra än readme.md-filen måste finnas i mappen med namnet ”docs” (endast gemener). Dokumentmappen ”” är en särskild mapp ignoreras av Machine Learning-körning så att innehållet i den här mappen inte får kopieras för att beräkna mål i onödan. Objekt i den här mappen räknas inte mot gränsen 25 MB för Projektstorlek. Dokumentmappen ”” är till exempel plats för att lagra stora bildfiler som behövs i dokumentationen. De här filerna spåras fortfarande med Git via körningshistoriken. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Skapa en instans av TDSP struktur och mallar från mallgalleriet Machine Learning
Slutför följande procedurer för att skapa ett nytt projekt med TDSP-strukturen och dokumentation mallar.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt
Öppna Azure Machine Learning för att skapa ett nytt projekt. Under **projekt** i den vänstra rutan, klicka på plustecknet (**+**), och välj sedan **nytt projekt**.

![Nytt projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Skapa ett nytt projekt med TDSP-strukturerad
   1. Ange parametrar och information i motsvarande ruta eller listan:

      - Projektnamn
      - Projektkatalog
      - Projektbeskrivning
      - En tom sökväg för Git-lagringsplats
      - Namn på arbetsyta

   2. I den **Search** anger **TDSP**. 
   3. När den **strukturera ett projekt med TDSP** alternativ visas, väljer mallen. 
   4. Välj den **skapa** för att skapa ditt nya projekt med en TDSP-struktur. Om du anger en tom Git-lagringsplatsen när du skapar projekt (i motsvarande textruta) kan sedan fylls databasen med projektstruktur och innehåll när projektet har skapats.

![Skapa ett projekt med TDSP](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Granska projektstruktur TDSP
När det nya projektet har skapats, kan du granska dess struktur (finns i den vänstra panelen i följande bild). Den innehåller alla aspekter av standardiserad dokumentationen för förståelse för verksamheten. Dessa objekt omfattar olika TDSP-livscykeln, plats, definitioner och arkitekturen i den här dokumentationen mallen. 

Strukturen härleds från TDSP-struktur som publiceras i [TDSP struktur, dokument och artefakt projektmallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate), med vissa ändringar. Till exempel flera dokumentmallarna slås samman i en markdown nämligen [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Mappstruktur för projektet
TDSP-projektmallen innehåller följande översta mappar:
   - **kod**: innehåller kod.
   - **docs**: innehåller nödvändig dokumentation om projektet (till exempel markdown-filer och relaterade media).
   - **sample_data**: innehåller **EXEMPLET (liten)** data som du kan använda för tidig utveckling och testning. Vanligtvis dessa uppsättningar inte är större än flera (5) MB. Den här mappen är inte för fullständig eller stora datauppsättningar.

![Exempeldata](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Använda TDSP struktur och mallar
Du måste lägga till projekt-specifik information struktur och mallar. Du är förväntat att fylla dem med koden och informationen som behövs för att köra och leverera ditt projekt. Den [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) filen är en mall som du behöver ändra med information som är relevanta för ditt projekt. Den levereras med en uppsättning frågor som hjälper dig att fylla i information för var och en av de fyra stegen i den [TDSP livscykel](../team-data-science-process/lifecycle.md).

Ett exempel på hur en projektstruktur ser ut vid körning eller efter slutförande visas i den vänstra panelen i följande bild. Det här projektet är från den [exempelprojektet för Team Data Science Process: klassificera inkomster från USA insamlade data i Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) exempelprojektet.

![Exempel projektstruktur](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Dokumentera dina projekt
Referera till den [TDSP dokumentation mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate) information om hur du dokumenterar ditt projekt. I den aktuella Machine Learning TDSP dokumentation mallen, rekommenderar vi att du inkluderar all information på den [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) fil. Den här mallen ska fyllas i med information som är specifik för ditt projekt. 

Vi ger även en [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) mall. Du kan använda den här mallen om du vill ta med information som inte ingår i det primära projektdokumentet, men det är fortfarande användbart att dokumentera. 

### <a name="example-project-report"></a>Projektet exempelrapport
Du kan få en [projekt exempelrapport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Den här projektrapporten för den [USA inkomst klassificering exempelprojektet](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) visar hur du skapa en instans av och använder TDSP-mallen för ett datavetenskapsprojekt.

## <a name="next-steps"></a>Nästa steg
För att underlätta din förståelse för hur du använder TDSP struktur och mallar i Machine Learning-projekt, erbjuder vi flera slutförda projekt-exempel i dokumentationen för Machine Learning:

- Ett exempel som visar hur du skapar en TDSP-projekt i Machine Learning, se [exempelprojektet för Team Data Science Process: klassificera inkomster från USA insamlade data i Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Ett exempel som använder djupinlärning i naturlig-språkbearbetning (NLP) i ett projekt med TDSP-instans i Machine Learning finns [biografi medical igenkänning av entiteter med hjälp av naturligt språk bearbetning med djupinlärning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

