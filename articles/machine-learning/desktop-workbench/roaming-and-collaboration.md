---
title: Centrala och samarbete i Azure Machine Learning arbetsstationen | Microsoft Docs
description: Lär dig mer om att ställa in centrala och samarbete i Machine Learning-arbetsstationen.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ms.openlocfilehash: dd83fe3659779e7d49c36e6d906cc3b8019099a1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834218"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Centrala och samarbete i Azure Machine Learning arbetsstationen
Den här artikeln beskriver hur du kan använda Azure Machine Learning-arbetsstationen för att ställa in projekt för växling mellan datorer och samarbeta med gruppmedlemmar. 

När du skapar en Azure Machine Learning-projekt med en fjärransluten Git-lagringsplats (lagringsplatsen) länk lagras metadata för projektet och ögonblicksbilder i molnet. Du kan använda länken molnet åtkomst till projektet från en annan dator (centrala). Du kan också samarbeta med gruppmedlemmar genom att ge dem åtkomst till projektet. 

## <a name="prerequisites"></a>Förutsättningar
1. Installera Maskininlärning arbetsstationen app. Se till att du har åtkomst till ett konto i Azure Machine Learning-experiment. Mer information finns i [installationsguiden](../service/quickstart-installation.md).

2. Åtkomst [Visual Studio Team Services](https://www.visualstudio.com) (Team Services), och sedan skapa en lagringsplatsen för att länka ditt projekt till. Mer information finns i [med en Git-lagringsplatsen med ett projekt för Machine Learning arbetsstationen](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Skapa ett nytt projekt i Machine Learning
Öppna Machine Learning arbetsstationen och sedan skapa ett nytt projekt (till exempel ett projekt med namnet iris). I den **Visualstudio.com URL för GIT-lagringsplatsen** ange en giltig URL för ett Team Services Git-lagringsplatsen. 

> [!IMPORTANT]
> Om du väljer mallen tomt projekt, kanske på Git-lagringsplats som du vill använda redan har en mastergrenen. Machine Learning klonar bara mastergrenen lokalt. Mappen aml_config och andra metadata projektfiler läggs till i den lokala projektmappen. 
>
> Om du väljer alla andra projektmall, Git repo *kan* redan har en mastergrenen. Om det inte finns ett fel. Alternativet är att använda den `az ml project create` kommando för att skapa projektet, med en `--force` växla. Detta tar bort filer i den ursprungliga mastergrenen och ersätter dem med nya filer på den mall som du väljer.

När projektet har skapats kan du skicka några körs på alla skript som finns i projektet. Den här åtgärden genomför Projekttillståndet till den fjärranslutna Git repo körningshistorik grenen. 

> [!NOTE] 
> Endast skript körs utlösaren incheckningar körningshistorik grenen. Data Förbered dig körning och bärbara datorer kör inte utlösa projektet ögonblicksbilder i grenen körningshistorik.

Om du har ställt in Git-autentisering kan du också använda i mastergrenen. Eller skapa en ny gren. 

Exempel: 
```
# Check current repo status.
$ git status

# Stage all changes in the current repo.
$ git add -A

# Commit changes.
$ git commit -m "my commit fixes this weird bug!"

# Push to the remote repo.
$ git push origin master
```

## <a name="roaming"></a>Roaming
<a name="roaming"></a>

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Öppna Machine Learning-arbetsstationen på en annan dator
När Team Services Git-lagringsplatsen är kopplad till ditt projekt, når iris projektet från alla datorer med Machine Learning arbetsstationen installerad. 

För att komma åt iris projektet på en annan dator, måste du logga in i appen genom att använda samma autentiseringsuppgifter som du använde för att skapa projektet. Du måste också finnas i samma konto för Machine Learning-experiment och arbetsytan. Projektet iris visas i alfabetisk ordning med andra projekt i arbetsytan. 

### <a name="download-the-project-on-a-second-computer"></a>Hämta projektet på en annan dator
Ikonen bredvid iris projektet skiljer sig från vanligt mappikon när arbetsytan är öppen på den andra datorn. Hämta ikonen anger att innehållet i projektet är i molnet och att projektet är redo att laddas ned till den aktuella datorn. 

![Skapa projekt](./media/roaming-and-collaboration/downloadable-project.png)

Välj iris projektet ska starta hämtningen. När hämtningen är slutförd är projektet redo att användas på den andra datorn. 

Windows, projektet är placerad på C:\Users\\< användarnamn\>\Documents\AzureML.

MacOS, projektet är placerad på /startsida/\<användarnamn \> /Documents/AzureML.

I en framtida utgåva planerar vi att förbättra funktioner så att du kan välja en målmapp. 

> [!NOTE]
> Om du har en mapp i Machine Learning-katalogen som har exakt samma namn som projektet misslyckas hämtningen. Om du vill undvika det här problemet tillfälligt byta namn på den befintliga mappen.


### <a name="work-on-the-downloaded-project"></a>Arbeta med det hämta projektet 
Det nyligen hämtade projektet visar Projekttillståndet på den senaste körningen i projektet. En ögonblicksbild av Projekttillståndet värnar automatiskt grenen körningshistorik i Team Services Git-lagringsplatsen varje gång du skickar en körning. Ögonblicksbild som är associerad med den senaste körningen används för att skapa en instans av projektet på den andra datorn. 
 

## <a name="collaboration"></a>Samarbete
Du kan samarbeta med gruppmedlemmar på projekt som är länkade till ett Team Services Git-lagringsplatsen. Du kan tilldela behörigheter till användare för kontot Machine Learning-experiment, arbetsytan och projekt. För närvarande kan du utföra Azure Resource Manager-kommandon med hjälp av Azure CLI. Du kan också använda den [Azure-portalen](https://portal.azure.com). Mer information finns i [använda Azure portal för att lägga till användare](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Använda kommandoraden för att lägga till användare
Ett exempel är Alice ägare av iris projektet. Alice vill dela åtkomst till projektet med Bob. 

Alice väljer den **filen** -menyn och väljer den **kommandotolk** menyalternativet. Kommandotolken öppnas med iris projektet. Alice kan välja vilken åtkomstnivå hon vill ge Bob. Hon ger behörighet genom att köra följande kommandon:  

```azurecli
# Find the Resource Manager ID of the Experimentation account.
az ml account experimentation show --query "id"

# Add Bob to the Experimentation account as a Contributor.
# Bob now has read/write access to all workspaces and projects under the account by inheritance.
az role assignment create --assignee bob@contoso.com --role Contributor --scope <Experimentation account Resource Manager ID>

# Find the Resource Manager ID of the workspace.
az ml workspace show --query "id"

# Add Bob to the workspace as an Owner.
# Bob now has read/write access to all projects under the workspace by inheritance. Bob can invite or remove other users.
az role assignment create --assignee bob@contoso.com --role Owner --scope <workspace Resource Manager ID>
```

Efter rolltilldelning ser antingen direkt eller genom arv Bob projektet i listan Machine Learning-arbetsstationen. Bob kan behöva starta om programmet för att se projektet. Bob kan sedan hämta projektet enligt beskrivningen i [centrala](#roaming), och börja samarbeta med Alice. 

Körningshistoriken för alla användare som samarbetar med ett projekt strävar efter att samma fjärransluten Git-lagringsplatsen. När Alice utförs en körning, ser Bob kör i avsnittet körningshistoriken på projektet i appen Machine Learning-arbetsstationen. Bob kan även återställa projektet till tillståndet för alla kör, inklusive körs som Johan har startats. 

Genom att dela en fjärransluten Git-lagringsplatsen för projektet kan Alice och Bob också samarbeta i mastergrenen. Om det behövs, kan de också skapa personliga grenar och använda Git pull-förfrågningar och slår ihop för att samarbeta. 

### <a name="use-the-azure-portal-to-add-users"></a>Använda Azure portal för att lägga till användare
<a name="portal"></a>

Machine Learning-experiment konton, arbetsytorna och projekt är Azure Resource Manager-resurser. Om du vill tilldela roller, kan du använda den **åtkomstkontroll** länken i den [Azure-portalen](https://portal.azure.com). 

Hitta den resurs som du vill lägga till användare med hjälp av den **alla resurser** vyn. Välj den **åtkomstkontroll (IAM)** länka och välj sedan **lägga till användare**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Samarbete Exempelarbetsflöde
För att illustrera samarbete arbetsflödet ska vi gå igenom ett exempel. Contoso anställda Alice och Bob vill samarbeta med ett projekt för datavetenskap med hjälp av Machine Learning-arbetsstationen. Sina identiteter tillhöra samma klientorganisation i Contoso Azure Active Directory (AD Azure). Här följer stegen för Alice och Bob ta:

1. Alice skapar en tom Git-lagringsplatsen i ett Team Services-projekt. Projektet Team Services måste vara i en Azure-prenumeration som har skapats under Contoso Azure AD-klienten. 

2. Anna skapar ett experiment i Machine Learning-konto, en arbetsyta och ett Machine Learning arbetsstationen projekt på en dator. När hon skapar projektet hon URL för Team Services Git-lagringsplatsen.

3. Alice börjar arbeta med projektet. Hon skapar vissa skript och utför några körs. För varje kör skickas automatiskt en ögonblicksbild av hela projektmappen som ett genomförande till en körningshistorik grenen av Team Services Git-lagringsplatsen som skapas i Machine Learning-arbetsstationen.

4. Alice är nöjd med pågående arbete. Hon vill genomför sina ändringar i den lokala mastergrenen och skicka dem till mastergrenen för Team Services Git-lagringsplatsen. Projektet är öppen i Machine Learning arbetsstationen hon öppnar Kommandotolkens fönster och skriver in dessa kommandon:
    
    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Team Services.
    $ git push
    ```

5. Alice lägger till Bob arbetsytan som deltagare. Hon kan göra detta i Azure-portalen eller genom att använda den `az role assignment` kommandot som visas tidigare. Alice ger också Bob Läs-och skrivbehörighet till Team Services Git-lagringsplatsen.

6. Bob loggar in på Machine Learning-arbetsstationen på sin dator. Han kan se arbetsytan Alice delas med honom. Han kan se iris projektet visas under arbetsytan. 

7. Bob väljer projektets namn. Projektet hämtas till datorn.
    * Hämtade projektfiler är en kopia av ögonblicksbild av den senaste körningen som har registrerats i körningshistoriken. De är inte den senaste bekräftelsen på mastergrenen.
    * Lokala projektmappen har angetts till mastergrenen med unstaged ändringarna.

8. Bob kan bläddra som utfördes av Alice. Han kan återställa ögonblicksbilder av alla tidigare körs.

9. Johan vill få de senaste ändringarna Alice pushas och starta sedan fungerar i en annan gren. I Machine Learning arbetsstationen Johan öppnar en kommandotolk och kör följande kommandon:

    ```sh
    # Verify that the Git remote is pointing to the Team Services Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Team Services Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob ändrar projektet och skickar nya körs. Ändringarna som görs på grenen bob. Bobs körs bli synlig för Alice.

11. Bob är redo att skicka sina ändringar till en fjärransluten Git-lagringsplatsen. För att undvika konflikter med mastergrenen där fungerar Alice, skickar Bob sitt arbete till en ny fjärrgren som heter bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Team Services Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Om du vill berätta kall nya lura i sin kod Alice, skapar Bob en pull-begäran på fjärransluten Git-lagringsplatsen från bob gren till mastergrenen. Alice kan sedan Sammanfoga pull-begäran till mastergrenen.

## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [med en Git-lagringsplatsen med ett projekt för Machine Learning arbetsstationen](using-git-ml-project.md).
