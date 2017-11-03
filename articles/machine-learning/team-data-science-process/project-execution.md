---
title: "Körningen av datavetenskap projekt - Azure | Microsoft Docs"
description: "Hur en data-forskare kan köra ett datavetenskap projekt i en trackable version kontrollerade och samarbetsfunktioner sätt."
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: bradsev;
ms.openlocfilehash: 8c318f87243d0c98b6a42bebcdffb433f9cc456e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="execution-of-data-science-projects"></a>Körningen av datavetenskap projekt

Det här dokumentet beskrivs hur en data-forskare kan köra en vetenskap dataprojektet i en systematiskt, version kontrollerade och samarbetsfunktioner sätt en projektteamet med hjälp av den [Team datavetenskap Process](overview.md) (TDSP). TDSP är ett ramverk som utvecklats av Microsoft som innehåller en strukturerad sekvensen av aktiviteter att köra molnbaserade, prediktiva Analyslösningar effektivt. En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av en datavetenskap team standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md). 

Det här avsnittet innehåller instruktioner om hur du: 

1. gör **sprint planera** för arbetsobjekt som ingår i ett projekt.<br> Om du inte känner till sprint planera hittar du informationen nedan och allmän information [här](https://en.wikipedia.org/wiki/Sprint_(software_development) "här"). 
2. **Lägg till arbetsobjekt** till sprints.
3. **länka arbetsobjekt med kodning aktiviteter** spåras av git.
4. gör **code granska**. 


>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö med hjälp av Visual Studio Team Services VSTS () i följande uppsättning instruktioner. Anger vi hur du utför dessa uppgifter med VSTS eftersom det är hur vi implementera TDSP på Microsoft. Objekt (3) och (4) i listan ovan är fördelarna med naturligt om du väljer att använda VSTS. Om du använder en annan kod som är värd för plattformen för din grupp, ändras inte de uppgifter som måste slutföras av team lead normalt. Men sättet att utföra dessa uppgifter ska vara olika. Till exempel objekt i avsnittet sex, **länka ett arbetsobjekt med en git-gren**, kanske inte är lika enkelt som i VSTS.

Följande bild illustrerar en typisk sprint planering, kodning och källkontroll arbetsflöde som ingår i genomförandet av ett projekt för datavetenskap:

![1](./media/project-execution/project-execution-1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologi 

I TDSP Skriv ut planering framework finns det fyra vanliga typer av **arbetsobjekt**: **funktionen**, **användaren artikeln**, **uppgiften**, och **Bug**. Varje grupprojekt upprätthåller en enda eftersläpning för alla arbetsobjekt. Det finns några kvarvarande uppgifter på git databas under en grupprojekt. Här följer deras definitioner:

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
- **Programfel**: programfel avser vanligtvis korrigeringar som krävs för en befintlig kod eller ett dokument som är klar när du slutför en aktivitet. Det kan Eskalera till som en artikel eller en uppgift om felet orsakas av respektive saknas steg eller uppgifter. 

>[AZURE.NOTE] Vi lånar begreppet funktioner, artiklar, uppgifter och buggar från programvaruhantering kod (SCM) som ska användas i datavetenskap. De kan skilja sig något från deras vanliga SCM-definitioner.

##  2. <a name='SprintPlanning-2'></a>Skriv ut planering 

Sprint planering är användbart för prioritering av projektet och resursplanering och tilldelning. Många datavetare är engagerade med flera projekt, som kan ta månader att slutföra. Projekt fortsätta ofta på olika bbar. På servern VSTS, kan du enkelt skapa, hantera, och spåra arbetsobjekt i team projekt och genomföra sprint planering att säkerställa att dina projekt flyttar framåt som förväntat. 

Följ [länken](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) stegvisa anvisningar för planering i VSTS sprint. 


##  3. <a name='AddFeature-3'></a>Lägga till en funktion  

När projektet databasen skapas under en grupprojekt går du till gruppen **översikt** och klickar på **hantera arbete**.

![2](./media/project-execution/project-execution-2-sprint-team-overview.png)

Om du vill inkludera en funktion i eftersläpningen, klickar du på **eftersläpningar** --> **funktioner** --> **ny**, typen i funktionen **rubrik**(vanligtvis ditt projektnamn), och klicka sedan på **Lägg till** .

![3](./media/project-execution/project-execution-3-sprint-team-add-work.png)

Dubbelklicka på funktionen som du nyss skapade. Fyll i beskrivningar, tilldela gruppmedlemmar för den här funktionen och ange planering parametrar för den här funktionen. 

Du kan också länka den här funktionen i projekt-databasen. Klicka på **länken Lägg till** under den **Development** avsnitt. När du har redigerat funktionen klickar du på **spara och Stäng** att avsluta.


##  4. <a name='AddStoryunderfeature-4'></a>Lägga till artikeln under funktionen 

Under funktionen kan artiklar läggas till beskriver viktiga steg som behövs för att slutföra projektet (funktion). Lägg till en ny artikel, klicka på den  **+**  logga till vänster om funktionen i eftersläpning vyn.  

![4](./media/project-execution/project-execution-4-sprint-add-story.png)

Du kan redigera informationen i artikeln som status, beskrivning, kommentarer, planering och prioritet i popup-fönstret.

![5](./media/project-execution/project-execution-5-sprint-edit-story.png)

Du kan länka den här artikeln till en befintlig databas genom att klicka på **+ Lägg till länk** under **Development**. 

![6](./media/project-execution/project-execution-6-sprint-link-existing-branch.png)


##  5. <a name='AddTaskunderstory-5'></a>Lägga till en aktivitet i en artikel 

Uppgifter som är specifika detaljerade steg som krävs för att slutföra varje artikel. När alla aktiviteter i en artikel har slutförts, bör artikeln slutföras för. 

Om du vill lägga till en aktivitet i en artikel, klickar du på den  **+**  bredvid objektet artikeln väljer **uppgiften**, och fyll sedan i den detaljerade informationen för den här aktiviteten i popup-fönstret.

![7](./media/project-execution/project-execution-7-sprint-add-task.png)

När funktioner, artiklar och uppgifter har skapats kan du visa dem i den **eftersläpning** eller **Hanteringsstyrenhetens** vyer för att spåra deras status.

![8](./media/project-execution/project-execution-8-sprint-backlog-view.png)

![9](./media/project-execution/project-execution-9-link-to-a-new-branch.png)


##  6. <a name='Linkaworkitemwithagitbranch-6'></a>Länka ett arbetsobjekt med en git-gren 

VSTS är ett bekvämt sätt att ansluta ett arbetsobjekt (en artikel eller aktivitet) med en git-gren. På så sätt kan du länka din artikel eller uppgift direkt till kod som associeras med den. 

För att ansluta ett arbetsobjekt till en ny gren, dubbelklicka på ett arbetsobjekt och i popup-fönstret klickar du på **skapar en ny gren** under **+ Lägg till länk**.  

![10](./media/project-execution/project-execution-10-sprint-board-view.png)

Ange information för den här nya grenen som namnet på avdelningskontor, grundläggande git-lagringsplats och grenen. Git-lagringsplats valt måste vara databasen under samma grupprojekt som arbetsobjektet tillhör. Den grundläggande grenen kan vara mastergrenen eller andra befintliga gren.

![11](./media/project-execution/project-execution-11-create-a-branch.png)

Det är en bra idé att skapa en git-grenen för varje arbetsobjekt för artikeln. För varje aktivitet i arbetsobjektet skapa sedan en gren baserat på grenen artikeln. Ordna grenar därmed hierarkiska som motsvarar artikeln-aktivitet relationerna är användbart när du har flera personer arbetar på olika artiklar av samma projekt eller du har flera personer arbetar med olika uppgifter av samma artikel. Konflikter kan minimeras när varje medlem i gruppen fungerar på olika avdelningskontor och varje medlem fungerar på olika koder eller andra artefakter när du delar en gren. 

Följande bild visar den rekommenderade grenstruktur strategin för TDSP. Du kan inte ha många filialer som visas här, särskilt om du bara har en eller två personer som arbetar på samma projekt, eller endast en person som fungerar med alla aktiviteter i en artikel. Men avgränsa grenen utveckling från mastergrenen är alltid bra. Detta kan förhindra grenen versionen störs av aktiviteter för utveckling. Fullständig beskrivning av git gren modellen finns i [en lyckad Git förgrening modellen](http://nvie.com/posts/a-successful-git-branching-model/).

![12](./media/project-execution/project-execution-12-git-branches.png)

Kör följande kommando i ett shell-kommando (Windows eller Linux) för att växla till grenen du vill arbeta med. 

    git checkout <branch name>

Ändra den *< namn på gren\>*  till **master** växlar du tillbaka till den **master** grenen. När du växlar till arbetsgren, kan du börja att arbetsobjekt kan utveckla kod eller dokumentationen artefakter som behövs för att slutföra objektet. 

Du kan också länka ett arbetsobjekt till en befintlig gren. I den **detaljer** sida i ett arbetsobjekt, i stället för att klicka på **skapar en ny gren**, klickar du på **+ Lägg till länk**. Välj filialen som du vill länka arbetsobjekt till. 

![13](./media/project-execution/project-execution-13-link-to-an-existing-branch.png)

Du kan också skapa en ny gren i git bash-kommandon. Om < namn på grundläggande gren\> saknas, det < namn på ny gren\> baseras på _master_ grenen. 
    
    git checkout -b <new branch name> <base branch name>


##  7. <a name='WorkonaBranchandCommittheChanges-7'></a>Arbeta med en gren och spara ändringarna 

Nu anta att du gör en ändring i *data\_införandet* grenen för arbetsobjekt, t.ex att lägga till en R-fil på grenen i din lokala dator. Du kan genomföra R-filer som läggs till grenen för det här arbetsobjektet, förutsatt att du är på det kontoret i Git-gränssnittet med hjälp av följande Git-kommandon:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![14](./media/project-execution/project-execution-14-sprint-push-to-branch.png)

##  8. <a name='CreateapullrequestonVSTS-8'></a>Skapa en pull-begäran på VSTS 

När du är klar när du har några incheckningar och push-meddelanden för att sammanfoga den aktuella grenen till dess grundläggande gren, kan du skicka en **pull-begäran** på VSTS server. 

Gå till huvudsidan för ditt team projekt och klicka **kod**. Välj filialen som ska slås samman och git-databasnamn som du vill koppla gren till. Klicka på **Pull-förfrågningar**, klickar du på **nya pull-begäran** att skapa en pull-begäran granskning innan arbetet på grenen kopplas till dess grundläggande grenen.

![15](./media/project-execution/project-execution-15-spring-create-pull-request.png)

Fyll i vissa beskrivning om den här pullbegäran, lägga till granskare och skicka ut.

![16](./media/project-execution/project-execution-16-spring-send-pull-request.png)

##  9. <a name='ReviewandMerge-9'></a>Granska och dokument 

När pull-begäran har skapats granskarna att få ett e-postmeddelande granskar pull-begäranden. Granskare måste du kontrollera om ändringarna som arbetar eller inte och testa ändringarna med beställaren om möjligt. Baserat på deras utvärdering granskare Godkänn eller avvisa pull-begäran. 

![17](./media/project-execution/project-execution-17-add_comments.png)

![18](./media/project-execution/project-execution-18-spring-approve-pullrequest.png)

När granskningen är klar i arbetsgren kopplas till dess grundläggande grenen genom att klicka på den **Slutför** knappen. Du kan välja att ta bort arbetsgren när den har slagits samman. 

![19](./media/project-execution/project-execution-19-spring-complete-pullrequest.png)

Bekräfta på det övre vänstra hörnet begäran har markerats som **SLUTFÖRD**. 

![20](./media/project-execution/project-execution-20-spring-merge-pullrequest.png)

När du går tillbaka till databasen under **kod**, uppmanas du att du har växlats till mastergrenen.

![21](./media/project-execution/project-execution-21-spring-branch-deleted.png)

Du kan också använda följande Git-kommandon för att koppla din arbetsgren till dess grundläggande grenen och ta bort arbetsgren efter sammanslagning:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![22](./media/project-execution/project-execution-22-spring-branch-deleted-commandline.png)


##  10. <a name='DataQualityReportUtility-10'></a>Interaktiv Datagranskning, analys och rapportering (IDEAR)-verktyget

Det här verktyget med R-markdown ger ett flexibelt och interaktiva verktyg för att utvärdera och utforska datauppsättningar. Användarna kan snabbt skapa rapporter från datauppsättningen med minimal kodning. Användarna kan klicka på knappar för att exportera resultaten utforskning ser han i verktyget interaktivt till en slutlig rapport som kan skickas till klienter eller används för att fatta beslut om vilka variabler som ska inkluderas i efterföljande modellering steg.

För tillfället fungerar verktyget bara på dataramar i minnet. En fil .yaml behövs för att ange parametrarna för de data som ska undersökas. Mer information finns i [IDEAR i TDSP datavetenskap verktyg](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/DataReport-Utils).


##  11. <a name='ModelingUtility-11'></a>Baslinje modellering och rapportering av verktyget

Det här verktyget innehåller en anpassningsbar, delvis automatiserad tool för att utföra modellen skapas med hyper-parametern till omfattande och jämföra riktighet dessa modeller. 

Verktyget Skapa modellen är en R markdown-fil som kan köras för att skapa fristående html-utdata skapas med en innehållsförteckning för enkel navigering via dess olika avsnitt. Tre algoritmer utförs när markdown-fil körs (knit): reglerats regression som använder glmnet paketet slumpmässiga skog randomForest paketera och förstärkning träd med xgboost-paket). Var och en av dessa algoritmer producerar en tränad modell. Riktighet modellers jämförs sedan och relativa funktionen vikten områden rapporteras. Det finns två verktyg: en är en binär klassificering aktivitet och en är en regression-aktivitet. De huvudsakliga skillnaderna mellan dem är kontrollparametrar sätt och noggrannhet mått har angetts för åtgärderna learning. 

En Yaml-fil används för att ange:

- data-indata (en SQL-källa eller ett R-fil) 
- hur stor del av informationen som används för träning och vilken del för testning
- vilka algoritmer som ska köras 
- valet av kontrollparametrar för optimering av modellen:
    - korsvalidering 
    - startprogram
    - vikningar för korsvalidering
- hyper-parametern anger för varje algoritm. 

Antalet algoritmer, antalet vikningar för optimering, hyper-parametrarna och antalet hyper-parametern anger att Sopa över kan också ändras i Yaml-filen för att köra modeller snabbt. De kan till exempel köras med ett lägre antal KA vikningar, ett lägre antal parameteruppsättningar. De kan även köra mer utförligt med ett högre antal KA vikningar eller ett större antal parametern anger om som garanteras.

Mer information finns i [automatisk modellering och rapportering Utility i TDSP datavetenskap verktyg](https://github.com/Azure/Azure-TDSP-Utilities/tree/master/DataScienceUtilities/Modeling).


##  12. <a name='PowerBI-12'></a>Spåra förloppet för projekt med Power BI-instrumentpaneler

Datavetenskap gruppchefer, team leads och projektet leads måste följa förloppet för deras projekt, arbetet som har gjorts på dem och av vem och finns kvar på att göra-listor. Om du använder VSTS, kan du skapa Power BI-instrumentpaneler för att spåra aktiviteter och arbetsobjekt som är associerade med en Git-lagringsplats. Mer information om hur du ansluter Power BI till Visual Studio Team Services finns [Anslut Power BI till Team Services](https://www.visualstudio.com/en-us/docs/report/powerbi/connect-vso-pbi-vs). 

Information om hur du skapar Power BI-instrumentpaneler och rapporter för att spåra dina aktiviteter för Git-lagringsplatsen och arbetsobjekt som när data för VSTS är ansluten till Power BI finns [skapa Power BI-instrumentpaneler och rapporter](https://www.visualstudio.com/en-us/docs/report/powerbi/report-on-vso-with-power-bi-vs). 

Här följer två enkelt exempel instrumentpaneler som vi bygga om du vill spåra Git aktiviteter och arbetsobjekt. I den första exemplet instrumentpanelen visas git åtagande aktiviteter av olika användare vid olika tidpunkter och på olika databaser. Du kan enkelt statistikforskning för att filtrera de som du är intresserad av.

![23](./media/project-execution/project-execution-23-powerbi-git.png)

Arbetsobjekt (artiklar och aktiviteter) i olika iterationer presenteras i den andra exempel instrumentpanelen. De är grupperade efter användare som tilldelats och prioritetsnivå och färgade efter status.

![24](./media/project-execution/project-execution-24-powerbi-workitem.png)

 
## <a name="next-steps"></a>Nästa steg

Fullständig genomgång för slutpunkt-till-slutpunkt som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) avsnittet. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

Exempel körs steg i Team av vetenskapliga data som använder Azure Machine Learning Studio finns i [med Azure ML](http://aka.ms/datascienceprocess) utbildningsvägar.