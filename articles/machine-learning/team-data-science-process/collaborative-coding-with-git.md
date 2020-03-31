---
title: Gemensam kodning med Git - Team Data Science Process
description: Hur man gör gemensam kodutveckling för datavetenskap projekt med Git med agil planering.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 0708e395eff90ff5b889c05f0fd5e7a98205c5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721905"
---
# <a name="collaborative-coding-with-git"></a>Samarbetsbaserad kodning med Git

I den här artikeln beskrivs hur du använder Git som samarbetsram för kodutveckling för datavetenskapliga projekt. Artikeln beskriver hur du länkar kod i Azure Repos till [agila utvecklingsarbetsobjekt](agile-development.md) i Azure Boards, hur du gör kodgranskningar och hur du skapar och sammanfogar pull-begäranden för ändringar.

## <a name="link-a-work-item-to-an-azure-repos-branch"></a><a name='Linkaworkitemwithagitbranch-1'></a>Länka ett arbetsobjekt till en Azure Repos-gren 

Azure DevOps är ett praktiskt sätt att ansluta en Azure Boards User Story eller Task work-objekt med en Azure Repos Git-databasgren. Du kan länka din användarartikel eller uppgift direkt till den kod som är kopplad till den. 

Om du vill ansluta ett arbetsobjekt till en ny gren väljer du ellipsen **Åtgärder** (**...**) bredvid arbetsobjektet och bläddrar till på snabbmenyn och väljer **Ny gren**.  

![1](./media/collaborative-coding-with-git/1-sprint-board-view.png)

I dialogrutan **Skapa en gren** anger du det nya grennamnet och basen Azure Repos Git-databas och gren. Basdatabasen måste finnas i samma Azure DevOps-projekt som arbetsobjektet. Basgrenen kan vara huvudgrenen eller en annan befintlig gren. Välj **Skapa gren**. 

![2](./media/collaborative-coding-with-git/2-create-a-branch.png)

Du kan också skapa en ny gren med följande kommandot Git bash i Windows eller Linux:

```bash
git checkout -b <new branch name> <base branch name>

```
Om du inte anger \<ett basgrennamn> baseras den nya `master`grenen på . 

Om du vill växla till arbetsgrenen kör du följande kommando: 

```bash
git checkout <working branch name>
```

När du har växlat till arbetsgrenen kan du börja utveckla kod- eller dokumentationsartefakter för att slutföra arbetsuppgiften. Kör `git checkout master` växlar du tillbaka `master` till grenen.

Det är en bra idé att skapa en Git-gren för varje användarartikelarbetsobjekt. För varje uppgiftsarbetsobjekt kan du sedan skapa en gren baserat på grenen Användarberättelse. Ordna grenarna i en hierarki som motsvarar relationen Användarberättelse och aktivitet när du har flera personer som arbetar med olika användarberättelser för samma projekt eller på olika aktiviteter för samma användarberättelse. Du kan minimera konflikter genom att låta varje gruppmedlem arbeta på en annan gren eller på olika kod eller andra artefakter när du delar en gren. 

Följande diagram visar den rekommenderade förgreningsstrategin för TDSP. Du kanske inte behöver så många grenar som visas här, särskilt när bara en eller två personer arbetar med ett projekt, eller bara en person arbetar med alla aktiviteter i en användarberättelse. Men att skilja utvecklingsgrenen från huvudgrenen är alltid en bra idé och kan bidra till att förhindra att frigöringsgrenen avbryts av utvecklingsaktiviteter. En fullständig beskrivning av Git-grenmodellen finns i [En framgångsrik Git-förgreningsmodell](https://nvie.com/posts/a-successful-git-branching-model/).

![3](./media/collaborative-coding-with-git/3-git-branches.png)

Du kan också länka ett arbetsobjekt till en befintlig gren. På sidan **Detalj i** ett arbetsobjekt väljer du Lägg **till länk**. Välj sedan en befintlig gren att länka arbetsobjektet till och välj **OK**. 

![4](./media/collaborative-coding-with-git/4-link-to-an-existing-branch.png)

## <a name="work-on-the-branch-and-commit-changes"></a><a name='WorkonaBranchandCommittheChanges-2'></a>Arbeta med grenen och genomföra ändringar 

När du har gjort en ändring för arbetsobjektet, till exempel `script` att lägga till en R-skriptfil i den lokala datorns gren, kan du genomföra ändringen från den lokala grenen till den uppströmsarbetsgrenen med hjälp av följande Git-bash-kommandon:

```bash
git status
git add .
git commit -m "added an R script file"
git push origin script
```

![5](./media/collaborative-coding-with-git/5-sprint-push-to-branch.png)

## <a name="create-a-pull-request"></a><a name='CreateapullrequestonVSTS-3'></a>Skapa en pull-begäran

När en eller flera genomför och skickar, när du är redo att slå samman din nuvarande arbetsgren till basgrenen, kan du skapa och skicka en *pull-begäran* i Azure Repos. 

Från huvudsidan i ditt Azure DevOps-projekt pekar du på **Repos** > **Pull-begäranden** i den vänstra navigeringen. Välj sedan någon av knapparna **För ny pull-begäran** eller länken **Skapa en pull-begäran.**

![6](./media/collaborative-coding-with-git/6-spring-create-pull-request.png)

På skärmen **Ny pull-begäran** navigerar du vid behov till den Git-databas och gren som du vill koppla ändringarna till. Lägg till eller ändra annan information som du vill ha. Lägg till granskarnas namn under **Granskare**och välj sedan **Skapa**. 

![7](./media/collaborative-coding-with-git/7-spring-send-pull-request.png)

## <a name="review-and-merge"></a><a name='ReviewandMerge-4'></a>Granska och slå samman

När du har skapat pull-begäran får granskarna ett e-postmeddelande för att granska pull-begäran. Granskarna testar om ändringarna fungerar och kontrollerar ändringarna med beställaren om möjligt. Granskarna kan göra kommentarer, begära ändringar och godkänna eller avvisa pull-begäran baserat på deras bedömning. 

![8](./media/collaborative-coding-with-git/8-add_comments.png)

När granskarna har godkänt ändringarna kan du eller någon annan med kopplingsbehörighet slå samman arbetsgrenen med basgrenen. Välj **Slutför**och välj sedan **Slutför koppling** i dialogrutan Fullständig **pull-begäran.** Du kan välja att ta bort arbetsgrenen när den har slagits samman. 

![10](./media/collaborative-coding-with-git/10-spring-complete-pullrequest.png)

Bekräfta att begäran är markerad som **SLUTFÖRD**. 

![11](./media/collaborative-coding-with-git/11-spring-merge-pullrequest.png)

När du går tillbaka till **Repos** i den vänstra navigeringen kan du se `script` att du har bytt till huvudgrenen sedan grenen togs bort.

![12](./media/collaborative-coding-with-git/12-spring-branch-deleted.png)

Du kan också använda följande Git-bash-kommandon för att sammanfoga arbetsgrenen `script` till basgrenen och ta bort arbetsgrenen efter sammanslagningen:

```bash
git checkout master
git merge script
git branch -d script
```

![13](./media/collaborative-coding-with-git/13-spring-branch-deleted-commandline.png)

## <a name="next-steps"></a>Nästa steg

[Kör datavetenskapsuppgifter](execute-data-science-tasks.md) visar hur du använder verktyg för att slutföra flera vanliga datavetenskapsuppgifter, till exempel interaktiv datautforskning, dataanalys, rapportering och skapande av modeller.

[Exempel genomgångar](walkthroughs.md) visar genomgångar av specifika scenarier med länkar och miniatyrbeskrivningar. De länkade scenarierna illustrerar hur du kombinerar moln- och lokala verktyg och tjänster i arbetsflöden eller pipelines för att skapa intelligenta program. 

