---
title: Flexibel utveckling av datavetenskap projekt - Azure Machine Learning | Microsoft Docs
description: Hur utvecklare kan köra ett datavetenskap projekt i en systematiskt, version kontrollerade och samarbetsfunktioner sätt projektteamet med hjälp av Team av vetenskapliga data.
documentationcenter: ''
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: bradsev
ms.openlocfilehash: dbaf2df0f5572c9b269000c741f1d736a7521d73
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
---
# <a name="agile-development-of-data-science-projects"></a>Flexibel utveckling av datavetenskap projekt

Det här dokumentet beskriver hur utvecklare kan köra en vetenskap dataprojektet i en systematiskt, version kontrollerade och samarbetsfunktioner sätt en projektteamet med hjälp av den [Team datavetenskap Process](overview.md) (TDSP). TDSP är ett ramverk som utvecklats av Microsoft som innehåller en strukturerad sekvensen av aktiviteter att köra molnbaserade, prediktiva Analyslösningar effektivt. En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av en datavetenskap team standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md). 

Den här artikeln innehåller anvisningar om hur du: 

1. gör **sprint planera** för arbetsobjekt som ingår i ett projekt.<br> Om du inte känner till sprint planera hittar du information och allmän information [här](https://en.wikipedia.org/wiki/Sprint_(software_development) "här"). 
2. **Lägg till arbetsobjekt** till sprints. 

> [!NOTE]
> De steg som krävs för att ställa in en TDSP teammiljö med hjälp av Visual Studio Team Services VSTS () beskrivs i följande uppsättning instruktioner. De kan ange hur du utför dessa uppgifter med VSTS eftersom det är hur du implementerar TDSP hos Microsoft.  Om du väljer att använda VSTS, artiklar (3) och (4) i listan ovan är fördelarna med naturligt. Om du använder en annan kod som är värd för plattformen för din grupp, ändras inte de uppgifter som måste slutföras av team lead normalt. Men sättet att utföra dessa uppgifter ska vara olika. Till exempel objekt i avsnittet sex, **länka ett arbetsobjekt med en Git-gren**, kanske inte är lika enkelt som i VSTS.
>
>

Följande bild illustrerar en typisk sprint planering, kodning och källkontroll arbetsflöde som ingår i genomförandet av ett projekt för datavetenskap:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologi 

I TDSP Skriv ut planering framework finns det fyra vanliga typer av **arbetsobjekt**: **funktionen**, **användaren artikeln**, **uppgiften**, och **Bug**. Varje grupprojekt upprätthåller en enda eftersläpning för alla arbetsobjekt. Det finns några kvarvarande uppgifter på Git databas under en grupprojekt. Här följer deras definitioner:

- **Funktionen**: en funktion som motsvarar ett projekt engagement. Olika lösningar med en klient betraktas som olika funktioner. På samma sätt är det bäst att överväga olika faser i projektet med en klient som olika funktioner. Om du väljer ett schema som ***ClientName EngagementName*** för att namnge din funktioner, sedan du känner enkelt kontexten för projektet/engagement från namnen sig själva.
- **Artikeln**: artiklar är olika objekt som behövs för att slutföra en funktion (projekt) slutpunkt till slutpunkt. Exempel på artiklar:
    - Hämtning av Data 
    - Utforska Data 
    - Generera funktioner
    - Skapa modeller
    - Operationalizing modeller 
    - Omtränings modeller
- **Uppgiften**: aktiviteter är tilldelas koden eller dokumentet arbetsobjekt eller andra aktiviteter som måste göras för att slutföra en specifik artikel. Till exempel aktiviteter i artikeln *hämtar Data* kan vara:
    -  Hämta autentiseringsuppgifterna för SQLServer 
    -  Ladda upp Data till SQL Data Warehouse. 
- **Programfel**: programfel avser vanligtvis korrigeringar som krävs för en befintlig kod eller ett dokument som är klar när du slutför en aktivitet. Om felet orsakas av respektive saknas steg eller uppgifter, eskalera den till som en artikel eller en uppgift. 

> [!NOTE]
> Begrepp lånar av funktioner, artiklar, uppgifter och buggar från programvaruhantering kod (SCM) som ska användas i datavetenskap. De kan skilja sig något från deras vanliga SCM-definitioner.
>
>

> [!NOTE]
> Data forskare anser bekvämare med en smidig mall som specifikt justeras med livscykelstadier för TDSP. Med detta i åtanke har en Agile-härledda sprint planeringsmall skapats, där Epics artiklar osv ersätts med TDSP livscykelstadier eller substages. Anvisningar om hur du skapar en mall för flexibel finns [konfigurera flexibel vetenskap av data i Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Skriv ut planering 

Sprint planering är användbart för prioritering av projektet och resursplanering och tilldelning. Många datavetare är engagerade med flera projekt, som kan ta månader att slutföra. Projekt fortsätta ofta på olika bbar. På servern VSTS, kan du enkelt skapa, hantera, och spåra arbetsobjekt i team projekt och genomföra sprint planering att säkerställa att dina projekt flyttar framåt som förväntat. 

Följ [länken](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) stegvisa anvisningar för planering i VSTS sprint. 


## 3. <a name='AddFeature-3'></a>Lägga till en funktion  

När projektet databasen skapas under en grupprojekt går du till gruppen **översikt** och klickar på **hantera arbete**.

![2](./media/agile-development/2-sprint-team-overview.png)

Om du vill inkludera en funktion i eftersläpningen, klickar du på **eftersläpningar** --> **funktioner** --> **ny**, typen i funktionen **rubrik**(vanligtvis ditt projektnamn), och klicka sedan på **Lägg till** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Dubbelklicka på den funktion som du skapade. Fyll i beskrivningar, tilldela gruppmedlemmar för den här funktionen och ange planering parametrar för den här funktionen. 

Du kan också länka den här funktionen i projekt-databasen. Klicka på **länken Lägg till** under den **Development** avsnitt. När du har redigerat funktionen klickar du på **spara och Stäng** att avsluta.


## 4. <a name='AddStoryunderfeature-4'></a>Lägga till artikeln under funktionen 

Under funktionen kan artiklar läggas till beskriver viktiga steg som behövs för att slutföra projektet (funktion). Lägg till en ny artikel, klicka på den **+** logga till vänster om funktionen i eftersläpning vyn.  

![4](./media/agile-development/4-sprint-add-story.png)

Du kan redigera informationen i artikeln som status, beskrivning, kommentarer, planering och prioritet i popup-fönstret.

![5](./media/agile-development/5-sprint-edit-story.png)

Du kan länka den här artikeln till en befintlig databas genom att klicka på **+ Lägg till länk** under **Development**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Lägga till en aktivitet i en artikel 

Uppgifter som är specifika detaljerade steg som krävs för att slutföra varje artikel. När alla aktiviteter i en artikel har slutförts, bör artikeln slutföras för. 

Om du vill lägga till en aktivitet i en artikel, klickar du på den **+** bredvid objektet artikeln väljer **uppgiften**, och fyll sedan i den detaljerade informationen för den här aktiviteten i popup-fönstret.

![7](./media/agile-development/7-sprint-add-task.png)

När funktioner, artiklar och uppgifter har skapats kan du visa dem i den **eftersläpning** eller **Hanteringsstyrenhetens** vyer för att spåra deras status.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Konfigurera en flexibel TDSP arbete mall i Visual Studio Online

Den här artikeln förklarar hur du ställer in en flexibel datavetenskap processmall som använder livscykelstadier för TDSP datavetenskap och spårar arbetsuppgifter med Visual Studio Online (vso). Stegen nedan gå igenom ett exempel på hur du konfigurerar det data vetenskap-specifikt flexibel bearbeta mallen *AgileDataScienceProcess* och visar hur du skapar arbetsuppgifter vetenskap utifrån mallen.

### <a name="agile-data-science-process-template-setup"></a>Flexibel datavetenskap processen inställningar

1. Gå till startsidan för server **konfigurera** -> **processen**.

    ![10](./media/agile-development/10-settings.png) 

2. Gå till **alla processer** -> **processer**under **Agile** och klicka på **skapa ärvt processen**. Placera processnamn ”AgileDataScienceProcess” och klicka sedan **Skapa process**.

    ![11](./media/agile-development/11-agileds.png)

3. Under den **AgileDataScienceProcess** -> **arbetsobjekt typer** fliken, inaktivera **legendarisk**, **funktionen**,  **Användaren artikeln**, och **aktivitet** arbetsobjekt typer av **konfigurera -> Inaktivera**

    ![12](./media/agile-development/12-disable.png)

4. Gå till **AgileDataScienceProcess** -> **eftersläpning nivåer** fliken. Byt namn på ”Epics” till ”TDSP projekt” genom att klicka på den **konfigurera** -> **redigera/Byt namn på**. Klicka i dialogrutan samma **+ ny typ av arbetsobjekt** i ”datavetenskap projekt” och ange värdet för **standardtyp av arbetsobjekt** ”TDSP projektet” 

    ![13](./media/agile-development/13-rename.png)  

5. På samma sätt kan ändra eftersläpning namnet ”funktioner” till ”TDSP steg” och Lägg till följande för att den **nytt arbete objekttypen**:

    - Förståelse för verksamheten
    - Datainsamling
    - Modellering
    - Distribution

6. Byt namn på ”användare artikeln” till ”TDSP Substages” med typ av arbetsobjekt standard inställd på nyskapade ”TDSP Substage” typen.

7. Ange de ”aktiviteter” som nyligen skapade typ av arbetsobjekt ”TDSP Task” 

8. Efter de här stegen nivåerna eftersläpning bör se ut så här:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Skapa datavetenskap arbetsobjekt

När du har skapat mallen datavetenskap processer du skapar och spårar dina datavetenskap arbetsuppgifter som motsvarar TDSP livscykel.

1. När du skapar ett nytt grupprojekt väljer du ”Agile\AgileDataScienceProcess” som den **arbetsobjekt processen**:

    ![15](./media/agile-development/15-newproject.png)

2. Navigera till det nyligen skapade grupprojektet och klicka på **arbete** -> **eftersläpningar**.

3. Se ”TDSP projekt” genom att klicka på **konfigurera inställningar för team** och kontrollera ”TDSP projekt”; spara.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Nu kan du börja skapa arbetsobjekt för vetenskap specifika data.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Här är ett exempel på hur datavetenskap projektet arbetsobjekt som ska visas:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Nästa steg

[Samarbetsfunktioner kodning med Git](collaborative-coding-with-git.md) beskriver hur du gör samarbetsfunktioner kod utveckling för datavetenskap projekt med Git som utvecklingsramverk delad kod och hur du länka dessa kodning aktiviteter till arbetet planerat med flexibel processen.

Här är ytterligare länkar till resurser på flexibel processer.

- Flexibel process   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Flexibel processen objekttyper för arbetsobjekt och arbetsflöde   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Genomgång som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) artikel. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 
