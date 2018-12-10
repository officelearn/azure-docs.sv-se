---
title: Smidig utveckling av dataforskningsprojekt - Team Data Science Process
description: Hur utvecklare kan köra ett datavetenskapsprojekt i en systematisk, version kontrolleras och samarbetsfunktioner sätt projektteamet genom att använda Team Data Science Process.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ee8e3e3ef33a8b09b92d4dfc262fce26dd60abb3
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134411"
---
# <a name="agile-development-of-data-science-projects"></a>Smidig utveckling av dataforskningsprojekt

Det här dokumentet beskriver hur utvecklare kan köra ett datavetenskapsprojekt i en systematisk, version kontrolleras och samarbetsfunktioner sätt inom en projektgrupp med hjälp av den [Team Data Science Process](overview.md) (TDSP). TDSP är ett ramverk som utvecklats av Microsoft och som tillhandahåller en strukturerade sekvensen av aktiviteter att köra molnbaserad förutsägande Analyslösningar effektivt. En översikt över rollerna som personal och förknippade aktiviteter som hanteras av en data science team standardisera om den här processen finns i [roller för Team Data Science Process och uppgifter](roles-tasks.md). 

Den här artikeln innehåller instruktioner om hur du: 

1. gör **sprint planera** för arbetsobjekt som ingår i ett projekt.<br> Om du inte känner till sprint planering, hittar du information om och allmän information [här](https://en.wikipedia.org/wiki/Sprint_(software_development) "här"). 
2. **Lägg till arbetsobjekt** till sprintar. 

> [!NOTE]
> De steg som krävs för att ställa in en TDSP teammiljö med Azure DevOps Services beskrivs i följande uppsättning instruktioner. De anger du hur du utför dessa uppgifter med Azure DevOps Services eftersom det är hur du implementerar TDSP på Microsoft.  Om du väljer att använda Azure DevOps-tjänsterna, objekt (3) och (4) i listan ovan är fördelar som du får naturligt. Om du använder en annan kod som är värd för plattformen för din grupp, ändra inte de uppgifter som måste slutföras av gruppledare Allmänt. Men sättet att utföra dessa uppgifter kommer att vara olika. Till exempel objekt i avsnittet sex, **länka ett arbetsobjekt med en Git-gren**, kanske inte är lika enkelt som i Azure DevOps-tjänsterna.
>
>

Följande bild visar en typisk sprint planering, kodning och källkontroll arbetsflödet som ingår i att implementera ett datavetenskapsprojekt:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologi 

I TDSP sprint planering framework finns det fyra vanliga typer av **arbetsobjekt**: **funktionen**, **Användarhistoria**, **uppgift**, och **Bugg**. Varje projekt har en enda kvarvarande uppgifter för alla arbetsobjekt. Det finns några kvarvarande uppgifter på nivån för Git-lagringsplatsen under ett projekt. Här är deras definitioner:

- **Funktionen**: en funktion som motsvarar ett projekt engagement. Olika engagemang med en klient betraktas som olika funktioner. På samma sätt är det bäst att tänka på olika faser i ett projekt med en klient som olika funktioner. Om du väljer ett schema som ***ClientName EngagementName*** för att namnge dina funktioner, sedan du känner enkelt kontexten för projekt/engagement från namnen själva.
- **Berättelse**: berättelser är olika arbetsobjekt som behövs för att slutföra en funktion (projekt) slutpunkt till slutpunkt. Exempel på artiklar:
    - Hämta Data 
    - Utforska Data 
    - Generera funktioner
    - Skapa modeller
    - Operationalisera modeller 
    - Träna modeller
- **Uppgiften**: uppgifter är tilldelningsbara arbetsobjekt i kod eller ett dokument eller andra aktiviteter som måste utföras för att slutföra en specifik historia. Till exempel aktiviteter i artikeln *hämta Data* kan vara:
    -  Hämta autentiseringsuppgifterna för SQLServer 
    -  Ladda upp Data till SQL Data Warehouse. 
- **Bugg**: buggar avser vanligtvis korrigeringar som behövs för en befintlig kod eller ett dokument som utförs när du går igenom en uppgift. Om felet orsakas av respektive saknas steg eller uppgifter, eskalera den till som en artikel eller en uppgift. 

> [!NOTE]
> Begrepp lånar av funktioner, artiklar, uppgifter och buggar från programvaruhantering kod (SCM) som ska användas i datavetenskap. De kan skilja sig något från deras vanliga SCM-definitioner.
>
>

> [!NOTE]
> Dataexperter kan känner dig bekvämade med en smidig mall som specifikt överensstämmer med livscykelstadier för TDSP. Med det i åtanke har en Agile-härledd sprint planeringsmallen skapats, där Epics berättelser osv har ersatts av livscykelstadier för TDSP eller substages. Anvisningar om hur du skapar en flexibel mall finns i [ställa in flexibla datavetenskapsprocess i Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Sprint planering 

Sprint planering är användbart för projektet prioritering och resursplanering och allokering. Många dataanalytiker arbetade med flera projekt, som kan ta månader att slutföra. Projekt fortsätta ofta på olika bbar. För Azure DevOps-tjänsterna, kan du enkelt skapa, hantera, och spåra arbetsobjekt i ditt projekt och utför sprint planerar att se till att dina projekt flyttar framåt som förväntat. 

Följ [den här länken](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) stegvisa instruktioner om sprint planering i Azure DevOps-tjänsterna. 


## 3. <a name='AddFeature-3'></a>Lägg till en funktion  

När projektet databasen skapas under ett projekt, går du till teamet **översikt** och klicka på **hantera arbete**.

![2](./media/agile-development/2-sprint-team-overview.png)

För att inkludera en funktion i planeringen, klickar du på **eftersläpningar** --> **funktioner** --> **New**, typ i funktionen **rubrik**(vanligtvis ditt projektnamn), och klicka sedan på **Lägg till** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Dubbelklicka på den funktion som du skapade. Fyll i beskrivningar, tilldela medlemmar för den här funktionen och ange planera parametrar för den här funktionen. 

Du kan också länka den här funktionen till lagringsplatsen för projektet. Klicka på **länken Lägg till** under den **utveckling** avsnittet. När du har redigerat funktionen klickar du på **spara och Stäng** att avsluta.


## 4. <a name='AddStoryunderfeature-4'></a>Lägg till berättelse under funktionen 

Under funktionen kan berättelser läggas till beskriver viktiga steg som behövs för att slutföra projektet (funktion). Lägg till en ny artikel, klicka på den **+** logga till vänster om funktionen för eftersläpning i vyn.  

![4](./media/agile-development/4-sprint-add-story.png)

Du kan redigera information om budskapet, till exempel status, beskrivning, kommentarer, planering och prioritet i popup-fönstret.

![5](./media/agile-development/5-sprint-edit-story.png)

Du kan länka den här artikeln till en befintlig databas genom att klicka på **+ Lägg till länk** under **utveckling**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Lägg till en aktivitet i en artikel 

Uppgifter är specifika detaljerade steg som behövs för att slutföra varje artikel. När alla aktiviteter i en artikel har slutförts, bör budskapet slutföras för. 

Lägg till en uppgift i en artikel, klicka på den **+** bredvid berättelse-objektet och välj **uppgift**, och sedan fylla i den detaljerade informationen i den här uppgiften i popup-fönstret.

![7](./media/agile-development/7-sprint-add-task.png)

När funktionerna, berättelser och uppgifter har skapats kan du se dem i den **eftersläpning** eller **tavla** vyer för att spåra deras status.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Konfigurera en flexibel TDSP arbete-mall i Visual Studio Online

Den här artikeln förklarar hur du ställer in en flexibel data science process-mall som använder TDSP data science-livscykelstadier och spårar arbetsobjekt med Visual Studio Online (vso). Stegen nedan genomgången ett exempel på hur du konfigurerar den data science-specifikt smidigt att bearbeta mallen *AgileDataScienceProcess* och visar hur du skapar arbetsuppgifter för data science baserat på mallen.

### <a name="agile-data-science-process-template-setup"></a>Flexibel Data Science Process inställningar

1. Gå till startsidan för server **konfigurera** -> **processen**.

    ![10](./media/agile-development/10-settings.png) 

2. Gå till **alla processer** -> **processer**under **Agile** och klicka på **skapa ärvt processen**. Sedan placera processnamn ”AgileDataScienceProcess” och klicka på **Skapa process**.

    ![11](./media/agile-development/11-agileds.png)

3. Under den **AgileDataScienceProcess** -> **typer av arbetsobjekt** fliken, inaktivera **Epic**, **funktionen**,  **Användarhistoria**, och **uppgift** arbetsobjekt typer av **konfigurera -> Inaktivera**

    ![12](./media/agile-development/12-disable.png)

4. Gå till **AgileDataScienceProcess** -> **eftersläpning nivåer** fliken. Byt namn på ”Epics” till ”TDSP-projekt” genom att klicka på den **konfigurera** -> **redigera/Byt namn på**. I dialogrutan samma klickar du på **+ ny typ av arbetsobjekt** i ”Datavetenskapsprojekt” och ange värdet för **standard arbetsobjektstyp** till ”TDSP-projekt” 

    ![13](./media/agile-development/13-rename.png)  

5. På samma sätt kan ändra eftersläpning namnet ”funktioner” till ”TDSP faser” och Lägg till följande till den **nytt verk objekttyp**:

    - Förståelse för verksamheten
    - Datainsamling
    - Modellering
    - Distribution

6. Byt namn på ”Användarhistoria” till ”TDSP Substages” med standard arbetsobjektstyp inställd på nyligen skapade ”TDSP Substage” typen.

7. Ange ”uppgifterna” nyligen skapade arbetsobjektstyp ”TDSP-uppgiften” 

8. Efter de här stegen eftersläpning nivåer bör se ut så här:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Skapa arbetsobjekt för Data Science

Du kan skapa och spåra dina data science-arbetsuppgifter som relaterar till TDSP-livscykel när data science process-mallen har skapats.

1. När du skapar ett nytt projekt, väljer du ”Agile\AgileDataScienceProcess” som den **arbetsobjektet bearbeta**:

    ![15](./media/agile-development/15-newproject.png)

2. Navigera till det nyskapade projektet och klicka på **Work** -> **eftersläpningar**.

3. Se ”TDSP-projekt” genom att klicka på **konfigurera inställningar för team** och kontrollera ”TDSP-projekt”; spara.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Nu kan du börja skapa data science-specifika arbetsuppgifter.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Här är ett exempel på hur arbetsobjekt för data science projektet ska visas:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Nästa steg

[Samarbetsbaserad kodning med Git](collaborative-coding-with-git.md) beskriver hur du gör samarbetsfunktioner kodutveckling för data science-projekt med Git som utvecklingsramverk delad kod och hur du länka dessa kodning aktiviteter i arbetet planerat med agil process.

Här följer ytterligare länkar till resurser i flexibla processer.

- Agil process   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Agil process objekttyper för arbetsobjekt och arbetsflöde   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Genomgångar som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och som är kopplad till miniatyrbilder beskrivningarna i den [exempel genomgångar](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 
