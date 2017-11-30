---
title: Samarbetsfunktioner kodning med Git - Azure Machine Learning | Microsoft Docs
description: "Hur du gör samarbetsfunktioner kod utveckling för datavetenskap projekt med Git med flexibel planering."
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
ms.date: 11/28/2017
ms.author: bradsev;
ms.openlocfilehash: e6808c0d9d4ca14101eeebe8b3e286d64e2328f9
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2017
---
# <a name="collaborative-coding-with-git"></a>Samarbetsfunktioner kodning med Git

I den här artikeln beskriver vi hur du gör samarbetsfunktioner kod utveckling för datavetenskap projekt med Git som utvecklingsramverk delad kod. Det täcker hur man länka dessa kodning aktiviteter till arbetet planerat i [flexibel utveckling](agile-development.md) och hur du code granskningar.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Länka ett arbetsobjekt med en Git-gren 

VSTS är ett bekvämt sätt att ansluta ett arbetsobjekt (en artikel eller aktivitet) med en Git-gren. På så sätt kan du länka din artikel eller uppgift direkt till kod som associeras med den. 

För att ansluta ett arbetsobjekt till en ny gren, dubbelklicka på ett arbetsobjekt och i popup-fönstret klickar du på **skapar en ny gren** under **+ Lägg till länk**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Ange information för den här nya grenen som namnet på avdelningskontor, grundläggande Git-lagringsplats och grenen. Git-lagringsplats valt måste vara databasen under samma grupprojekt som arbetsobjektet tillhör. Den grundläggande grenen kan vara mastergrenen eller andra befintliga gren.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Det är en bra idé att skapa en Git-grenen för varje arbetsobjekt för artikeln. För varje aktivitet i arbetsobjektet skapa sedan en gren baserat på grenen artikeln. Ordna grenar därmed hierarkiska som motsvarar artikeln-aktivitet relationerna är användbart när du har flera personer arbetar på olika artiklar av samma projekt eller du har flera personer arbetar med olika uppgifter av samma artikel. Konflikter kan minimeras när varje medlem i gruppen fungerar på olika avdelningskontor och varje medlem fungerar på olika koder eller andra artefakter när du delar en gren. 

Följande bild visar den rekommenderade grenstruktur strategin för TDSP. Du kan inte ha många filialer som visas här, särskilt om du bara har en eller två personer som arbetar på samma projekt, eller endast en person som fungerar med alla aktiviteter i en artikel. Men avgränsa grenen utveckling från mastergrenen är alltid bra. Detta kan förhindra grenen versionen störs av aktiviteter för utveckling. Fullständig beskrivning av Git gren modellen finns i [en lyckad Git förgrening modellen](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Kör följande kommando i ett shell-kommando (Windows eller Linux) för att växla till grenen du vill arbeta med. 

    git checkout <branch name>

Ändra den *< namn på gren\>*  till **master** växlar du tillbaka till den **master** grenen. När du växlar till arbetsgren, kan du börja att arbetsobjekt kan utveckla kod eller dokumentationen artefakter som behövs för att slutföra objektet. 

Du kan också länka ett arbetsobjekt till en befintlig gren. I den **detaljer** sida i ett arbetsobjekt, i stället för att klicka på **skapar en ny gren**, klickar du på **+ Lägg till länk**. Välj filialen som du vill länka arbetsobjekt till. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Du kan också skapa en ny gren i Git Bash kommandon. Om < namn på grundläggande gren\> saknas, det < namn på ny gren\> baseras på _master_ grenen. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Arbeta med en gren och spara ändringarna 

Nu anta att du gör en ändring i *data\_införandet* grenen för arbetsobjekt, t.ex att lägga till en R-fil på grenen i din lokala dator. Du kan genomföra R-filer som läggs till grenen för det här arbetsobjektet, förutsatt att du är på det kontoret i Git-gränssnittet med hjälp av följande Git-kommandon:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Skapa en pull-begäran på VSTS 

När du är klar när du har några incheckningar och push-meddelanden för att sammanfoga den aktuella grenen till dess grundläggande gren, kan du skicka en **pull-begäran** på VSTS server. 

Gå till huvudsidan för ditt team projekt och klicka **kod**. Välj filialen som ska slås samman och Git-databasnamn som du vill koppla gren till. Klicka på **Pull-förfrågningar**, klickar du på **nya pull-begäran** att skapa en pull-begäran granskning innan arbetet på grenen kopplas till dess grundläggande grenen.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Fyll i vissa beskrivning om den här pullbegäran, lägga till granskare och skicka ut.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Granska och dokument 

När pull-begäran har skapats granskarna att få ett e-postmeddelande granskar pull-begäranden. Granskare måste du kontrollera om ändringarna som arbetar eller inte och testa ändringarna med beställaren om möjligt. Baserat på deras utvärdering granskare Godkänn eller avvisa pull-begäran. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

När granskningen är klar i arbetsgren kopplas till dess grundläggande grenen genom att klicka på den **Slutför** knappen. Du kan välja att ta bort arbetsgren när den har slagits samman. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Bekräfta på det övre vänstra hörnet begäran har markerats som **SLUTFÖRD**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

När du går tillbaka till databasen under **kod**, uppmanas du att du har växlats till mastergrenen.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Du kan också använda följande Git-kommandon för att koppla din arbetsgren till dess grundläggande grenen och ta bort arbetsgren efter sammanslagning:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Nästa steg

[Köra datavetenskap uppgifter](execute-data-science-tasks.md) visar hur du använder Verktyg för att utföra ett antal vanliga datavetenskap uppgifter, till exempel interaktiv datagranskning, dataanalys, rapportering och skapa en modell.

Genomgång som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De anges och är kopplad till miniatyr beskrivningar i den [exempel genomgång](walkthroughs.md) artikel. De visar hur du kombinerar moln, lokala verktyg och tjänster i ett arbetsflöde eller en rörledning för att skapa ett intelligent program. 

