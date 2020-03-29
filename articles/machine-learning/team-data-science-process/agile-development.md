---
title: Agil utveckling av datavetenskapliga projekt - Team Data Science Process
description: Kör ett datavetenskapsprojekt på ett systematiskt, versionskontrollerat och samarbetsinriktat sätt inom ett projektteam med hjälp av Team Data Science Process.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722109"
---
# <a name="agile-development-of-data-science-projects"></a>Agil utveckling av datavetenskapliga projekt

I det här dokumentet beskrivs hur utvecklare kan utföra ett datavetenskapsprojekt på ett systematiskt, versionskontrollerat och samarbetsinriktat sätt inom ett projektteam med hjälp av [Team Data Science Process](overview.md) (TDSP). TDSP är ett ramverk utvecklat av Microsoft som tillhandahåller en strukturerad sekvens av aktiviteter för att effektivt köra molnbaserade, prediktiva analyslösningar. En översikt över de roller och uppgifter som hanteras av ett data science-team som standardiserar på TDSP finns i [Roller och uppgifter för teamdatavetenskap.](roles-tasks.md) 

Den här artikeln innehåller instruktioner om hur du: 

- Gör *sprint planering* för arbetsuppgifter som deltar i ett projekt.
- Lägg till *arbetsobjekt* i sprintar.
- Skapa och använd en *flexibel-härledd arbetsobjektmall* som specifikt justerar med TDSP-livscykelfaser.

Följande instruktioner beskriver de steg som behövs för att konfigurera en TDSP-teammiljö med Azure Boards och Azure Repos i Azure DevOps. Instruktionerna använder Azure DevOps eftersom det är så du implementerar TDSP på Microsoft. Om din grupp använder en annan kodvärdplattform ändras inte gruppleduppgifterna, men sättet att slutföra uppgifterna är annorlunda. Att länka ett arbetsobjekt med en Git-gren kanske inte är samma sak med GitHub som det är med Azure Repos.

Följande bild illustrerar ett typiskt arbetsflöde för sprintplanering, kodning och källkontroll för ett datavetenskapsprojekt:

![TDSP (Team Data Science Process)](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Typer av arbetsobjekt

I ramverket för planering av TDSP finns det fyra vanliga typer av *arbetsobjekt:* *Funktioner*, *Användarberättelser*, *Uppgifter*och *Buggar*. Eftersläpningen för alla arbetsobjekt finns på projektnivå, inte Git-databasnivån. 

Här är definitionerna för arbetsobjekttyperna:

- **Funktion:** En funktion motsvarar ett projektengagemang. Olika åtaganden med en kund är olika funktioner, och det är bäst att betrakta olika faser av ett projekt som olika funktioner. Om du väljer ett schema som * \<ClientName>-\<EngagementName>* för att namnge dina funktioner, kan du enkelt känna igen projektets kontext och engagemang från själva namnen.
  
- **Användarberättelse:** Användarberättelser är arbetsobjekt som behövs för att slutföra en funktion från på nytt. Exempel på användarberättelser är:
  - Hämta data 
  - Utforska data 
  - Generera funktioner
  - Skapa modeller
  - Operationalisera modeller 
  - Omtrimningsmodeller
  
- **Uppgift:** Uppgifter kan tilldelas arbetsobjekt som måste göras för att slutföra en viss användarartikel. Uppgifter i användarartikel *hämtar data* kan till exempel vara:
  - Hämta SQL Server-autentiseringsuppgifter
  - Ladda upp data till SQL Data Warehouse
  
- **Fel:** Fel är problem i befintlig kod eller dokument som måste åtgärdas för att slutföra en uppgift. Om fel orsakas av saknade arbetsobjekt kan de eskalera till att vara användarberättelser eller uppgifter. 

Dataexperter kan känna sig mer bekväma med hjälp av en flexibel mall som ersätter funktioner, användarberättelser och uppgifter med TDSP-livscykelstadier och understeg. Information om hur du skapar en flexibel-härledd mall som specifikt stämmer överens med TDSP-livscykelfaserna finns [i Använda en flexibel TDSP-arbetsmall](#set-up-agile-dsp-6).

> [!NOTE]
> TDSP lånar begreppen funktioner, användarberättelser, uppgifter och buggar från software code management (SCM). TDSP-begreppen kan skilja sig något från deras konventionella SCM-definitioner.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Planera sprintar

Många datavetare är engagerade i flera projekt, vilket kan ta månader att slutföra och fortsätta i olika takt. Sprintplanering är användbart för projektprioritering och resursplanering och allokering. I Azure Boards kan du enkelt skapa, hantera och spåra arbetsobjekt för dina projekt och genomföra sprintplanering för att säkerställa att projekt går framåt som förväntat.

Mer information om sprintplanering finns i [Scrum sprintar](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Mer information om sprintplanering i Azure Boards finns i [Tilldela eftersläpningsobjekt till en sprint](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Lägga till en funktion i eftersläpningen 

När projekt- och projektkoddatabasen har skapats kan du lägga till en funktion i eftersläpningen för att representera projektets arbete.

1. På projektsidan väljer du > **Anslagstavlor i** den vänstra navigeringen. **Boards** 
   
1. Om arbetsobjekttypen i det övre fältet är **Stories**på fliken **Eftersläpning** är du ned och väljer **Funktioner**. Välj sedan **Nytt arbetsobjekt.**
   
   ![Välj nytt arbetsobjekt](./media/agile-development/2-sprint-team-overview.png)
   
1. Ange en rubrik för funktionen, vanligtvis ditt projektnamn, och välj sedan **Lägg till överst**. 
   
   ![Ange en rubrik och välj Lägg till överst](./media/agile-development/3-sprint-team-add-work.png)
   
1. Välj och öppna den nya funktionen i listan **Eftersläpning.** Fyll i beskrivningen, tilldela en gruppmedlem och ange planeringsparametrar. 
   
   Du kan också länka funktionen till projektets Azure Repos-koddatabas genom att välja **Lägg till länk** under avsnittet **Utveckling.** 
   
   När du har redigerat funktionen väljer du **Spara & Stäng**.
   
   ![Redigera funktionen och välj Spara & Stäng](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Lägga till en användarartikel i funktionen 

Under funktionen kan du lägga till Användarberättelser för att beskriva de viktigaste stegen som behövs för att slutföra projektet. 

Så här lägger du till en ny användarartikel i en funktion:

1. Välj **+** till vänster om funktionen på fliken **Eftersläpning.** 
   
   ![Lägga till en ny användarartikel under funktionen](./media/agile-development/4-sprint-add-story.png)
   
1. Ge användarberättelsen en rubrik och redigera information som tilldelning, status, beskrivning, kommentarer, planering och prioritet. 
   
   Du kan också länka användarartikeln till en gren av projektets Azure Repos-koddatabas genom att välja **Lägg till länk** under avsnittet **Utveckling.** Markera den databas och gren som du vill länka arbetsobjektet till och välj sedan **OK**.
   
   ![Lägg till länk](./media/agile-development/5-sprint-edit-story.png)
   
1. När du har redigerat användarartikeln väljer du **Spara & Stäng**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Lägga till en uppgift i en användarartikel 

Uppgifter är specifika detaljerade steg som behövs för att slutföra varje användarartikel. När alla uppgifter i en användarartikel har slutförts bör även användarberättelsen slutföras. 

Om du vill lägga till en **+** uppgift i en användarartikel markerar du bredvid objektet Användarartikel och väljer **Uppgift**. Fyll i rubriken och annan information i uppgiften.

![Lägga till en uppgift i en användarartikel](./media/agile-development/7-sprint-add-task.png)

När du har skapat Funktioner, Användarberättelser och Uppgifter kan du visa dem i **vyerna Eftersläpningar** eller **Anslagstavlor** för att spåra deras status.

![Vyn Eftersläpningar](./media/agile-development/8-sprint-backlog-view.png)

![Vyn Anslagstavlor](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Använda en flexibel TDSP-arbetsmall

Dataexperter kan känna sig mer bekväma med hjälp av en flexibel mall som ersätter funktioner, användarberättelser och uppgifter med TDSP-livscykelstadier och understeg. I Azure Boards kan du skapa en flexibel härledd mall som använder TDSP-livscykelfaser för att skapa och spåra arbetsobjekt. Följande steg går igenom att konfigurera en datavetenskaplig agil processmall och skapa arbetsobjekt för datavetenskap baserat på mallen.

### <a name="set-up-an-agile-data-science-process-template"></a>Konfigurera en flexibel datavetenskapsprocessmall

1. På huvudsidan för Azure DevOps-organisationen väljer du **Organisationsinställningar** från vänster navigering. 
   
1. Välj **Process**under **Anslagstavlor**i navigeringen **Organisationsinställningar** som lämnas . 
   
1. I fönstret **Alla processer** markerar du **...** bredvid **Agile**och väljer sedan **Skapa ärvd process**.
   
   ![Skapa ärvd process från Agile](./media/agile-development/10-settings.png) 
   
1. I dialogrutan **Skapa ärvd process från Agile** anger du namnet *AgileDataScienceProcess*och väljer **Skapa process**.
   
   ![Skapa Process För AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. I **Alla processer**väljer du den nya **AgileDataScienceProcess**. 
   
1. På fliken **Arbetsobjekttyper** inaktiverar du **Epic**, **Feature**, **User Story**och **Task** genom att välja **...** bredvid varje objekt och sedan välja **Inaktivera**. 
   
   ![Inaktivera typer av arbetsobjekt](./media/agile-development/12-disable.png)
   
1. Välj fliken **Eftersläpningsnivåer** i **Alla processer.** Under **Eftersläpningar i portföljer**väljer du **...** **bredvid Epic (inaktiverad)** och väljer sedan **Redigera/byt namn**. 
   
1. I dialogrutan **Redigera eftersläpningsnivå:**
   1. Under **Namn**ersätter du **Epic** med *TDSP-projekt*. 
   1. Under **Arbetsobjekttyper på den här eftersläpningsnivån**väljer du **Ny arbetsobjekttyp,** anger *TDSP-projekt*och väljer **Lägg till**. 
   1. Under **Standardtyp för arbetsobjekt**kan du lista ned och välja **TDSP Project**. 
   1. Välj **Spara**.
   
   ![Ange eftersläpningsnivå för portföljen](./media/agile-development/13-rename.png)  
   
1. Följ samma steg för att byta namn på **funktioner** till *TDSP-faser*och lägg till följande nya arbetsobjekttyper:
   
   - *Affärssyssning*
   - *Datainsamling*
   - *Modellering*
   - *Distribution*
   
1. Under **Eftersläpningsfördränta**villkor byter du namn på **Stories** till *TDSP Substages*, lägger till den nya arbetsobjekttypen *TDSP-underläge*och anger standardtypen för arbetsobjekt till **TDSP-underläget**.
   
1. Lägg till en ny *TDSP-aktivitet*för arbetsobjekttyp under eftersläpning i **iteration**och ange att den är standardtyp för arbetsobjekt. 
   
När du har slutfört stegen bör eftersläpningsnivåerna se ut så här:
   
 ![Eftersläpnivåer för TDSP-mall](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Skapa arbetsobjekt för agil datavetenskapsprocess

Du kan använda processmallen data science för att skapa TDSP-projekt och spåra arbetsobjekt som motsvarar TDSP-livscykelfaser.

1. Välj **Nytt projekt**på huvudsidan för Azure DevOps-organisationen . 
   
1. Ge projektet ett namn i dialogrutan **Skapa nytt projekt** och välj sedan **Avancerat**. 
   
1. Under **Arbetsartikelprocess**kan du lista ned och välja **AgileDataScienceProcess**och välj sedan **Skapa**.
   
   ![Skapa ett TDSP-projekt](./media/agile-development/15-newproject.png)
   
1. I det nyskapade projektet väljer du > **Anslagstavlor Eftersläpningar** i den vänstra navigeringen. **Boards**
   
1. Om du vill göra TDSP-projekt synliga väljer du ikonen **Konfigurera gruppinställningar.** Markera kryssrutan **TDSP-projekt** på skärmen **Inställningar** och välj sedan **Spara och stänga**.
   
   ![Kryssrutan Markera TDSP-projekt](./media/agile-development/16-enabledsprojects1.png)
   
1. Om du vill skapa ett datavetenskapligt TDSP-projekt väljer du **TDSP-projekt** i det övre fältet och väljer sedan **Nytt arbetsobjekt**. 
   
1. I popup-fönstret ger du arbetsobjektet TDSP Project ett namn och väljer **Lägg till överst**.
   
   ![Skapa projektarbetsobjekt för datavetenskap](./media/agile-development/17-dsworkitems0.png)
   
1. Om du vill lägga till en arbetsuppgift **+** under TDSP-projektet markerar du bredvid projektet och väljer sedan vilken typ av arbetsobjekt som ska skapas. 
   
   ![Välj arbetsobjekttyp för datavetenskap](./media/agile-development/17-dsworkitems1.png)
   
1. Fyll i informationen i det nya arbetsobjektet och välj **Spara & Stäng**.
   
1. Fortsätt att **+** markera symbolerna bredvid arbetsobjekt för att lägga till nya TDSP-faser, understeg och aktiviteter. 
   
Här är ett exempel på hur data science-projektets arbetsobjekt ska visas i **vyn Eftersläpningar:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Nästa steg

[Samarbete kodning med Git](collaborative-coding-with-git.md) beskriver hur man gör samarbete kodutveckling för datavetenskap projekt med Git som delad kod utvecklingsram, och hur man kopplar dessa kodning aktiviteter till det arbete som planeras med den agila processen.

[Exempel genomgångar](walkthroughs.md) visar genomgångar av specifika scenarier med länkar och miniatyrbeskrivningar. De länkade scenarierna illustrerar hur du kombinerar moln- och lokala verktyg och tjänster i arbetsflöden eller pipelines för att skapa intelligenta program.
  
Ytterligare resurser för agila processer:

- [Agile-processen](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agila processarbetsobjekttyper och arbetsflöde](/azure/devops/boards/work-items/guidance/agile-process-workflow)

