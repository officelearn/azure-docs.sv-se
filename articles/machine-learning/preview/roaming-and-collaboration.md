---
title: "Centrala och samarbete i Azure Maskininlärning arbetsstationen | Microsoft Docs"
description: "Lista över kända problem och en guide för att felsöka"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: 856348c07a198a8c53c6661441d5c49196ef3af5
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Centrala och samarbete i Azure Machine Learning arbetsstationen
Det här dokumentet vägleder dig genom Azure Machine Learning arbetsstationen hur flyttas dina projekt över datorer, samt aktivera samarbete med dina gruppmedlemmar. 

När du skapar en Azure Machine Learning-projekt med en fjärransluten Git-lagringsplats (lagringsplatsen)-länk, lagras metadata för projektet och ögonblicksbilder i molnet. Länken molnet kan du få åtkomst till projektet från en annan dator (centrala). Du kan också ge åtkomst till dina medarbetare, vilket gör att samarbete. 

## <a name="prerequisites"></a>Krav
Installera först i Azure Machine Learning-arbetsstationen med åtkomst till ett experiment-konto. Följ den [installationsguiden](quickstart-installation.md) för mer information.

Andra kan komma åt [Visual Studio Team System](https://www.visualstudio.com) och skapa en lagringsplatsen för att länka ditt projekt till. Detaljerad information om Git refererar till den [med Git Repo med en Azure Machine Learning arbetsstationen projekt](using-git-ml-project.md) artikel.

## <a name="create-a-new-azure-machine-learning-project"></a>Skapa ett nytt Azure Machine Learning-projekt
Starta Azure Machine Learning arbetsstationen och skapa ett nytt projekt (till exempel _iris_). Fyll i **Visualstudio.com URL för GIT-lagringsplatsen** textruta med en giltig URL för VSTS Git-lagringsplatsen. 
>[!IMPORTANT]
>Det går inte att skapa projektet om du inte har läs-/ skrivbehörighet på Git-lagringsplatsen och Git-lagringsplatsen är inte tom, d.v.s. den har redan en mastergrenen.

När projektet har skapats kan du skicka några körs på alla skript i projektet. Den här åtgärden genomför projekt tillstånd till den fjärranslutna Git repo körningshistorik grenen. 

Måste du konfigurera Git-autentisering kan du också explicit fungerar i mastergrenen eller skapa en ny gren. 

Som exempel: 
```
# check current repo status
$ git status

# stage all changes in the current repo
$ git add -A

# commit changes
$ git commit -m "my commit fixes this weird bug!"

# push to remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-azure-machine-learning-workbench-on-second-machine"></a>Öppna Azure Machine Learning-arbetsstationen på andra datorer
När VSTS Git-lagringsplatsen är kopplad till ditt projekt, kan du komma åt den _iris_ projekt från valfri dator där du har installerat Azure Machine Learning-arbetsstationen. 

Om du vill komma åt iris projektet på en annan dator, måste du logga in i appen med samma autentiseringsuppgifter används när du skapar projektet. Dessutom måste du gå till samma experiment konto och arbetsytan. Den _iris_ projektet visas i alfabetisk ordning med andra projekt i arbetsytan. 

### <a name="download-project-on-second-machine"></a>Hämta projektet på den andra datorn
När arbetsytan öppnas på andra datorn ikonen bredvid den _iris_ projektet skiljer sig från vanligt mappikon. Hämta ikonen indikerar att innehållet i projektet är i molnet och måste hämtas till den aktuella datorn. 

![Skapa projekt](./media/roaming-and-collaboration/downloadable-project.png)

Klicka på den _iris_ projektet startar en åtgärd för hämtning. Projektet är redo att användas på den andra datorn efter en kort när När nedladdningen är klar. 

På Windows är det`C:\Users\<username>\Documents\AzureML`

I macOS är det här:`/home/<username>/Documents/AzureML`

I en framtida utgåva planerar vi att utöka funktionaliteten så att du kan välja en målmapp. 

>Observera att om du råkar ha en mapp i Azure ML-katalogen som har exakt samma namn som projektet download misslyckas. För den tid som, måste du byta namn på den befintliga mappen för att undvika problemet.


### <a name="work-on-the-downloaded-project"></a>Arbeta med det hämta projektet 
Det nyligen hämtade projektet visar Projekttillståndet från och med den senaste körningen i projektet. En ögonblicksbild av Projekttillståndet värnar automatiskt grenen körningshistorik i VSTS Git-lagringsplatsen varje gång du skickar en körning. Vi använder den ögonblicksbild som är associerat med den senaste körs när en instans skapades av projektet på den andra datorn. 
 

## <a name="collaboration"></a>Samarbete
Du kan samarbeta med dina gruppmedlemmar på projekt som är kopplade till en VSTS Git-lagringsplatsen. Du kan tilldela behörigheter till användare på konto experiment, arbetsytan och projekt. Du kan utföra Azure Resource Manager-kommandon med hjälp av Azure CLI för tillfället. Du kan också använda [Azure-portalen](https://portal.azure.com). Se [efter avsnittet](#portal).    

### <a name="using-command-line-to-add-users"></a>Kommandoraden för att lägga till användare
Kan använda ett exempel. Anta Alice är ägare till th e_Iris_ projekt och hon vill dela access med Bob. 

Alice klickar på den **filen** -menyn och väljer den **kommandotolk** menyalternativet att starta den kommandotolk som konfigurerats för att den _iris_ projekt. Alice kan sedan välja vilka administratörsnivå för hon vill ge till Bob genom att köra följande kommandon.  

```azurecli
# Find ARM ID of the experimnetation account
az ml account experimentation show --query "id"

# Add Bob to the Experimentation Account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the Account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <experimentation account ARM ID>

# Find ARM ID of the workspace
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the Workspace by inheritance. And he can invite or remove others.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace ARM ID>
```

Efter rolltilldelning ser direkt eller genom arv Bob projektet i listan arbetsstationen. Programmet kanske måste startas om för att kunna se projektet. Bob kan sedan hämta projektet enligt beskrivningen i den [centrala avsnittet](#roaming) och samarbeta med Alice. 

Körningshistorik för alla användare som samarbetar med ett projekt strävar efter att samma fjärransluten Git-lagringsplatsen. När Alice utförs en körning, så se Bob kör i avsnittet körningshistoriken på projektet i appen arbetsstationen. Bob kan även återställa projektet till tillståndet för alla kör inklusive körs startas av Alice. 

Dela en fjärransluten Git-lagringsplatsen för projektet kan Alice och Bob också samarbeta på mastergrenen. Om det behövs, kan de också skapa personliga grenar och använda Git pull-förfrågningar och slår ihop för att samarbeta. 

### <a name="using-azure-portal-to-add-users"></a>Med hjälp av Azure portal för att lägga till användare
<a name="portal"></a>

Azure Machine Learning-experiment konton, arbetsytorna och projekt är Azure Resource Manager-resurser. Du kan använda åtkomstkontroll länken i den [Azure-portalen](https://portal.azure.com) att tilldela roller. 

Hitta den resursen som du vill lägga till användare från alla resurser att visa. Klicka på åtkomstkontrollen (IAM)-länk-sidan. Lägga till användare 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Samarbete Exempelarbetsflöde
För att illustrera samarbete flödet, låt oss gå igenom ett exempel. Contoso anställda Alice och Bob vill samarbeta med ett projekt för vetenskap av data med hjälp av Azure ML-arbetsstationen. Deras identitet tillhöra samma Contoso Azure AD-klienten.

1. Alice först skapar en tom Git-lagringsplatsen i VSTS projekt. Det här projektet i VSTS ska finnas i en Azure-prenumeration skapade under Contoso AAD-klient. 

2. Alice skapar sedan ett experiment Azure ML-konto, en arbetsyta och ett projekt för Azure ML-arbetsstationen på sin dator. Hon tillhandahåller URL för Git-lagringsplatsen när att skapa projektet.

3. Alice börjar arbeta med projektet. Hon skapar vissa skript och utför några körs. För varje kör flyttas en ögonblicksbild av hela projektmappen automatiskt en körningshistorik gren i VSTS Git-lagringsplatsen som skapats av Workbench som ett genomförande.

4. Alice är nöjd med pågående arbete. Hon vill spara sitt ändringen i lokalt _master_ Förgrena och skickar den till VSTS Git-lagringsplatsen _master_ grenen. Hon startar du Kommandotolken från Azure ML-arbetsstationen för att göra det, öppna projektet, och problem följande kommandon:
    
    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # stage all changes
    $ git add -A

    # commit changes with a comment
    $ git commit -m "this is a good milestone"

    # push the commit to the master branch of the remote Git repo in VSTS
    $ git push
    ```

5. Alice lägger sedan till Bob i arbetsytan som deltagare. Hon kan göra detta från Azure-portalen eller med hjälp av `az role assignment` kommando visar ovan. Hon ger också Bob/skrivbehörighet till VSTS Git-lagringsplatsen.

6. Bob loggar nu i Azure ML-arbetsstationen på sin dator. Han kan se arbetsytan-Alice delas med honom och projektet visas under arbetsytan. 

7. Bob klickar på projektnamnet och projektet hämtas till datorn.
    
    a. De hämta projektfilerna är klonerna för ögonblicksbilden av senast körs registreras i körningshistoriken. De är inte den senaste bekräftelsen på mastergrenen.
    
    b. Lokala projektmappen är inställd på _master_ gren med unstaged ändringar.

8. Bob kan nu bläddra körs som körs av Alice och Återställ ögonblicksbild av alla tidigare körs.

9. Johan vill få de senaste ändringarna pushas av Alice och börjar arbeta på en annan gren. Så att han öppnar du Kommandotolken från Azure ML-arbetsstationen och kör följande kommandon:

    ```sh
    # verify the Git remote is pointing to the VSTS Git repo
    $ git remote -v

    # verify that the current branch is master
    $ git branch

    # get the latest commit in VSTS Git master branch and overwrite current files
    $ git pull --force

    # create a new local branch named "bob" so Bob's work is done on the "bob" branch
    $ git checkout -b bob
    ```

10. Bob nu ändrar projektet och skicka nya körs. Ändringarna som görs på den _bob_ grenen. Och bObs körs blir också synliga för Alice.

11. Bob är nu redo att skicka sin ändringar i en fjärransluten Git-lagringsplatsen. För att undvika konflikter med _master_ avdelningskontor där Alice fungerar på han bestämmer sig för att skicka sitt arbete till en ny fjärrgren som också kallas _bob_.

    ```sh
    # verify that the current branch is "bob" and it has unstaged changes
    $ git status
    
    # stage all changes
    $ git add -A

    # commit them with a comment
    $ git commit -m "I found a cool new trick."

    # create a new branch on the remote VSTS Git repo, and push changes
    $ git push origin bob
    ```

12. Bob sedan vet Alice om nya kall lura i sin kod och skapar en pull-begäran på fjärransluten Git-lagringsplatsen från den _bob_ förgrening till den _master_ grenen. Alice sedan inkludera pull-begäran till _master_ grenen.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om att använda Git med Azure ML-arbetsstationen: [med Git-lagringsplats med ett projekt för Azure Machine Learning arbetsstationen](using-git-ml-project.md)