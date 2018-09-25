---
title: Roaming och samarbete i Azure Machine Learning Workbench | Microsoft Docs
description: Lär dig hur du ställer in roaming och samarbete i Machine Learning Workbench.
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/16/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 07a9d46dff17b43d01a6b411292cf240c32476f3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46983726"
---
# <a name="roaming-and-collaboration-in-azure-machine-learning-workbench"></a>Roaming och samarbete i Azure Machine Learning Workbench

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 

Den här artikeln beskrivs hur du kan använda Azure Machine Learning Workbench för att konfigurera projekt för växling mellan datorer och samarbeta med teammedlemmar. 

När du skapar en Azure Machine Learning-projekt som har en fjärransluten Git-lagringsplats (lagringsplats) länk lagras projekt metadata och ögonblicksbilder i molnet. Du kan använda cloud-länk för åtkomst till projektet från en annan dator (centrala). Du kan också samarbeta med teammedlemmar genom att ge dem tillgång till projektet. 

## <a name="prerequisites"></a>Förutsättningar
1. Installera de Machine Learning Workbench-appen. Kontrollera att du har åtkomst till ett konto i Azure Machine Learning-experimentering. Mer information finns i den [installationsguide](quickstart-installation.md).

2. Åtkomst [Azure DevOps](https://www.visualstudio.com) och skapa sedan en lagringsplats för att länka ditt projekt. Mer information finns i [med en Git-lagringsplats med ett Machine Learning Workbench-projekt](using-git-ml-project.md).

## <a name="create-a-new-machine-learning-project"></a>Skapa ett nytt Machine Learning-projekt
Öppna Machine Learning Workbench och sedan skapa ett nytt projekt (till exempel ett projekt med namnet iris). I den **URL till Visualstudio.com GIT-lagringsplats** , ange en giltig URL för en Azure DevOps Git-lagringsplats. 

> [!IMPORTANT]
> Om du väljer mallen tomt projekt kanske Git-lagringsplats som du väljer att använda redan har en huvudgren. Machine Learning klonar bara huvudgrenen lokalt. Mappen aml_config och andra metadata projektfiler läggs till i den lokala projektmappen. 
>
> Om du väljer alla andra projektmall, Git-lagringsplats *kan* redan har en huvudgren. I annat fall visas ett fel. Alternativet är att använda den `az ml project create` kommando för att skapa projektet, med en `--force` växla. Detta tar bort filer i den ursprungliga huvudgrenen och ersätter dem med de nya filerna i mallen som du väljer.

När projektet har skapats kan du skicka några körs på alla skript som finns i projektet. Den här åtgärden genomför projekt-tillstånd till den fjärranslutna Git-lagringsplats körningshistoriken gren. 

> [!NOTE] 
> Endast skript körs utlösaren incheckningar till grenen körningshistoriken. Data Förbered körning och Notebook körs inte utlösa projektögonblicksbilder i grenen körningshistoriken.

Om du har ställt in Git-autentisering, kan du också fungera i huvudgrenen. Du kan också skapa en ny gren. 

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

### <a name="open-machine-learning-workbench-on-a-second-computer"></a>Öppna Machine Learning Workbench på en annan dator
När Azure DevOps Git-lagringsplatsen är kopplad till ditt projekt, kan du komma åt iris-projektet från alla datorer som har Machine Learning Workbench installerad. 

För att komma åt iris-projektet på en annan dator, måste du logga in i appen genom att använda samma autentiseringsuppgifter som du använde för att skapa projektet. Du måste också finnas i samma konto för Machine Learning-experimentering och arbetsytan. Iris-projektet visas i alfabetisk ordning med andra projekt i arbetsytan. 

### <a name="download-the-project-on-a-second-computer"></a>Ladda ned projekt på en annan dator
När arbetsytan har öppnats på den andra datorn visas skiljer ikonen bredvid iris-projektet sig från vanliga mappikonen. Ikonen hämta anger att innehållet i projektet är i molnet och att projektet är redo att laddas ned till den aktuella datorn. 

![Skapa projekt](./media/roaming-and-collaboration/downloadable-project.png)

Välj iris-projektet ska starta en hämtning. När nedladdningen är klar är projektet klart att kommas åt från den andra datorn. 

På Windows, projektet finns på C:\Users\\< användarnamn\>\Documents\AzureML.

I macOS, projektet finns på /startsida/\<användarnamn \> /dokument/AzureML.

I en senare version planerar vi att förbättra funktioner så att du kan välja en målmapp. 

> [!NOTE]
> Om du har en mapp i Machine Learning-katalogen som har exakt samma namn som projektet misslyckas nedladdningen. Om du vill undvika det här problemet tillfälligt byta namn på den befintliga mappen.


### <a name="work-on-the-downloaded-project"></a>Arbeta med det hämta projektet 
Det nyligen hämtade projektet återspeglar Projekttillståndet i den senaste körningen i projektet. En ögonblicksbild av Projekttillståndet är automatiskt bekräftas till grenen körningshistoriken i Azure DevOps Git-lagringsplats varje gång du skickar en körning. Den ögonblicksbild som är associerad med den senaste körningen används för att skapa en instans av projektet på den andra datorn. 
 

## <a name="collaboration"></a>Samarbete
Du kan samarbeta med teammedlemmar med projekt som är länkade till en Azure DevOps Git-lagringsplats. Du kan tilldela behörigheter till användare för Machine Learning-experimentering konto, arbetsytan och projekt. För närvarande kan du utföra Azure Resource Manager-kommandon med hjälp av Azure CLI. Du kan också använda den [Azure-portalen](https://portal.azure.com). Mer information finns i [använda Azure portal för att lägga till användare](#portal).    

### <a name="use-the-command-line-to-add-users"></a>Använda kommandoraden för att lägga till användare
Exempelvis är Alice ägare av iris-projektet. Alice vill dela åtkomst till projektet med Bob. 

Alice väljer den **filen** menyn och väljer den **kommandotolk** menyalternativ. Kommandotolken öppnas med iris-projektet. Alice kan sedan bestämma vilken åtkomstnivå som hon vill ge till Bob. Hon ger behörighet genom att köra följande kommandon:  

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

Efter rolltilldelning kan ser antingen direkt eller genom arv, Bob projektet i listan över Machine Learning Workbench-projekt. Bob kan behöva starta om programmet för att se projektet. Bob kan sedan hämta projektet enligt beskrivningen i [centrala](#roaming), och börja samarbeta med Alice. 

Körningshistoriken för alla användare som samarbetar med ett projekt strävar efter att samma fjärranslutna Git-lagringsplatsen. När Alice körs en körning, ser Bob körningen i avsnittet körningshistoriken i projektet i Machine Learning Workbench-appen. Bob kan även återställa projektet till tillståndet för alla kör, inklusive körningar som Alice startade. 

Genom att dela en fjärransluten Git-lagringsplats för projektet kan Alice och Bob också samarbeta i huvudgrenen. Om det behövs kan de också skapa personliga grenar och använder Git pull-begäranden och slår ihop för att samarbeta. 

### <a name="use-the-azure-portal-to-add-users"></a>Använd Azure portal för att lägga till användare
<a name="portal"></a>

Machine Learning-experimentering konton, arbetsytor och projekt är Azure Resource Manager-resurser. Om du vill tilldela roller, kan du använda den **Access Control** länken i den [Azure-portalen](https://portal.azure.com). 

Hitta den resurs som du vill lägga till användare med hjälp av den **alla resurser** vy. Välj den **åtkomstkontroll (IAM)** länka och välj sedan **lägga till användare**. 

<img src="./media/roaming-and-collaboration/iam.png" width="320px">

## <a name="sample-collaboration-workflow"></a>Exempelarbetsflöde för samarbete
För att visa samarbete arbetsflödet kan du nu ska vi gå igenom ett exempel. Contoso-anställda Alice och Bob vill samarbeta på ett datavetenskapsprojekt med hjälp av Machine Learning Workbench. Deras identiteter hör till samma Contoso Azure Active Directory (Azure AD)-klient. Här följer stegen Alice och Bob ta:

1. Alice skapar en tom Git-lagringsplats i ett Azure DevOps-projekt. Azure DevOps-projekt måste vara i en Azure-prenumeration som har skapats under Contoso Azure AD-klienten. 

2. Alice skapar ett konto för Machine Learning-experimentering, en arbetsyta och ett Machine Learning Workbench-projekt på en dator. När hon skapar projektet, hon Azure DevOps Git-repo-URL.

3. Alice börjar arbeta med projektet. Hon skapar vissa skript och kör några körningar. För varje körning skickas automatiskt en ögonblicksbild av hela projektmappen som en allokering till en gren för körningshistoriken i Azure DevOps Git-lagringsplatsen som Machine Learning Workbench skapar.

4. Alice är nöjd med pågående arbete. Hon vill spara sina ändringar i lokala huvudgrenen och skicka dem till mastergrenen för Azure DevOps Git-lagringsplats. När projektet är öppen i Machine Learning Workbench hon öppnar du Kommandotolkens fönster och anger sedan följande kommandon:
    
    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Stage all changes.
    $ git add -A

    # Commit changes with a comment.
    $ git commit -m "this is a good milestone"

    # Push the commit to the master branch of the remote Git repo in Azure DevOps.
    $ git push
    ```

5. Alice lägger till Bob till arbetsytan som deltagare. Hon kan göra detta i Azure-portalen eller genom att använda den `az role assignment` kommandot, som visas tidigare. Alice ger även Bob Läs/Skriv behörigheter till Azure DevOps Git-lagringsplats.

6. Bob loggar in på Machine Learning Workbench på sin dator. Han kan se den arbetsyta som Alice delade med honom. Han kan se iris-projektet visas under arbetsytan. 

7. Bob väljer på projektets namn. Projektet hämtas till datorn.
    * Hämtade projektet-filer är en kopia av ögonblicksbild av den senaste körningen som inte har registrerats i körningshistoriken. De är inte den senaste bekräftelsen på huvudgrenen.
    * Den lokala projektmappen har angetts till huvudgrenen, med de icke mellanlagrade ändringarna.

8. Bob kan bläddra körningar som utfördes av Alice. Han kan återställa ögonblicksbilder av alla tidigare körningar.

9. Johan vill få de senaste ändringarna som Alice push-överfört och sedan börja arbeta i en annan gren. I Machine Learning Workbench, Bob öppnas en kommandotolk och kör följande kommandon:

    ```sh
    # Verify that the Git remote is pointing to the Azure DevOps Git repo.
    $ git remote -v

    # Verify that the current branch is master.
    $ git branch

    # Get the latest commit in the Azure DevOps Git master branch and overwrite current files.
    $ git pull --force

    # Create a new local branch named "bob" so that Bob's work is done in the "bob" branch
    $ git checkout -b bob
    ```

10. Bob ändrar projektet och skickar nya körningar. Ändringarna sparas på grenen bob. Bobs körningar bli synlig för Alice.

11. Bob är redo att överföra sina ändringar till den fjärranslutna Git-lagringsplatsen. För att undvika konflikt med huvudgrenen, där fungerar Alice, skickar Bob sitt arbete till en ny filialkontor som heter bob.

    ```sh
    # Verify that the current branch is "bob," and that it has unstaged changes.
    $ git status
    
    # Stage all changes.
    $ git add -A

    # Commit the changes with a comment.
    $ git commit -m "I found a cool new trick."

    # Create a new branch on the remote Azure DevOps Git repo, and then push the changes.
    $ git push origin bob
    ```

12. Om du vill berätta coola nya knep i sin kod Alice, skapar Bob en pull-begäran på fjärranslutna Git-lagringsplatsen från bob-gren till mastergrenen. Alice kan sedan koppla pull-begäran till huvudgrenen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [med en Git-lagringsplats med ett Machine Learning Workbench-projekt](using-git-ml-project.md).
