---
title: Samarbetsbaserad kodning med Git - Azure Machine Learning | Microsoft Docs
description: Hur du gör samarbetsfunktioner kodutveckling för data science-projekt med hjälp av Git med flexibel planering.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: 78bbdb244d9bd52a06623f7a6fa3bca123ef3828
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44300077"
---
# <a name="collaborative-coding-with-git"></a>Samarbetsbaserad kodning med Git

I den här artikeln beskriver vi hur du gör samarbetsfunktioner kodutveckling för data science-projekt med Git som utvecklingsramverk delad kod. Det täcker hur man länka dessa kodning aktiviteter i arbetet planerat i [smidig utveckling](agile-development.md) och hur du code granskningar.


## 1. <a name='Linkaworkitemwithagitbranch-1'></a>Länka ett arbetsobjekt med en Git-gren 

Azure DevOps-tjänster är ett enkelt sätt att ansluta ett arbetsobjekt (en artikel eller uppgiften) med en Git-gren. På så sätt kan du länka din berättelse eller uppgift direkt till den kod som är kopplade till den. 

Om du vill ansluta ett arbetsobjekt till en ny gren, dubbelklicka på ett arbetsobjekt och i popup-fönstret klickar du på **skapa en ny gren** under **+ Lägg till länk**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

Ange information för den här nya grenen som grennamnet, grundläggande Git-lagringsplatsen och grenen. Git-lagringsplats valt måste vara databasen under samma projekt som arbetsobjektet tillhör. Den grundläggande grenen kan vara huvudgrenen eller andra befintliga grenen.

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Ett bra tips är att skapa en Git-gren för arbetsobjekt för varje artikel. För varje aktivitet i arbetsobjektet skapa sedan en gren baserat på grenen historia. Ordna grenarna på så vis hierarkisk som motsvarar relationerna berättelse-aktivitet är användbart när du har flera personer som arbetar på olika berättelser om samma projekt eller om du har flera personer som arbetar med olika uppgifter av samma artikel. Konflikter kan arbetskostnaderna minimeras när varje medlem i gruppen fungerar på en annan gren och när varje medlem fungerar på olika koder eller andra artefakter när du delar en gren. 

Följande bild visar den rekommenderade branchning strategin för TDSP. Du kanske inte behöver flera grenar som visas här, särskilt om du bara har en eller två personer som arbetar på samma projekt eller bara en person som fungerar på alla uppgifter i en artikel. Men att separera grenen utveckling från huvudgrenen är alltid en bra idé. Detta kan förhindra versionsgrenen störs av utveckling-aktiviteter. Fullständig beskrivning av Git gren modellen finns i [en lyckad Git branchning modellen](http://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Om du vill växla till grenen som du vill arbeta med, kör du följande kommando i ett skalkommando (Windows eller Linux). 

    git checkout <branch name>

Ändra den *< namn på arbetsgren\>*  till **master** växlar du tillbaka till den **master** gren. När du växlar till arbetsgrenen som kan du börja att arbetsobjekt kan utveckla kod eller dokumentation artefakter som krävs för att slutföra objektet. 

Du kan också länka ett arbetsobjekt i en befintlig gren. I den **detalj** för ett arbetsobjekt, i stället för att klicka på **skapa en ny gren**, du har klickat på **+ Lägg till länk**. Välj den gren du vill länka arbetsobjekt till. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

Du kan också skapa en ny gren i Git Bash kommandon. Om < grundläggande grennamnet\> saknas, den < namn på ny gren\> baseras på _master_ gren. 
    
    git checkout -b <new branch name> <base branch name>


## 2. <a name='WorkonaBranchandCommittheChanges-2'></a>Arbeta med en gren och sedan spara ändringarna 

Nu anta att du gör en ändring i *data\_inmatning* grenen för arbetsobjekt, t.ex att lägga till en R-fil på grenen i den lokala datorn. Du kan genomföra R-filer som läggs till grenen för det här arbetsobjektet förutsatt att du är i den grenen i Git-gränssnittet med hjälp av följande Git-kommandon:

    git status
    git add .
    git commit -m"added a R scripts"
    git push origin data_ingestion

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## 3. <a name='CreateapullrequestonVSTS-3'></a>Skapa en pull-begäran på Azure DevOps-tjänsterna 

När du är klar när du har några incheckningar och push-meddelanden för att sammanfoga den aktuella grenen i dess grundläggande gren, kan du skicka en **pull-begäran** på Azure DevOps-tjänsterna. 

Gå till huvudsidan för projektet och klicka **kod**. Välj grenen som ska slås samman och namnet på Git-lagringsplatsen som du vill sammanfoga grenen i. Klicka sedan på **Pull-begäranden**, klickar du på **ny pull-begäran** att skapa en granskning av pull-begäran innan arbetet i grenen kopplas till dess grundläggande gren.

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

Fyll i vissa beskrivning om den här pull-begäran, Lägg till granskare och skicka ut.

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## 4. <a name='ReviewandMerge-4'></a>Granskning och koppling 

När pull-begäran har skapats, granskarna att få ett e-postmeddelande att granska pull-begäranden. Granskarna måste du kontrollera om ändringarna jobbar eller inte och testa ändringarna med beställaren om möjligt. Baserat på deras utvärdering, granskarna kan godkänna eller avvisa pull-begäran. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

![9](./media/collaborative-coding-with-git/9-spring-approve-pullrequest.png)

När granskningen är klar kan arbetsgrenen som kopplas till dess grundläggande gren genom att klicka på den **Slutför** knappen. Du kan välja att ta bort arbetsgrenen när den har slagits samman. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Bekräfta i det övre vänstra hörnet som begäran har markerats som **SLUTFÖRD**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

När du går tillbaka till databasen under **kod**, uppmanas du att du har växlats till huvudgrenen.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Du kan också använda följande Git-kommandon för att slå samman din arbetsgren till dess grundläggande gren och tar bort arbetsgrenen efter sammanslagning:

    git checkout master
    git merge data_ingestion
    git branch -d data_ingestion

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)


 
## <a name="next-steps"></a>Nästa steg

[Köra av datavetenskapsuppgifter](execute-data-science-tasks.md) visar hur du använder Verktyg för att slutföra flera vanliga datavetenskapsuppgifter som interaktiv datagranskning, dataanalys, rapportering och skapa en modell.

Genomgångar som visar alla steg i processen för **specifika scenarier** tillhandahålls också. De visas och som är kopplad till miniatyrbilder beskrivningarna i den [exempel genomgångar](walkthroughs.md) artikeln. De visar hur du kombinerar molnlösningar, lokala verktyg och tjänster i ett arbetsflöde eller en pipeline för att skapa ett intelligenta program. 

