---
title: Använda en Git-lagringsplatsen med ett projekt för Azure Machine Learning arbetsstationen | Microsoft Docs
description: Den här artikeln förklarar hur du använder en Git-lagringsplats tillsammans med ett projekt för Azure Machine Learning-arbetsstationen.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: desktop-workbench
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 134e02f976f1ed0084e24ddbda06ba70b237edad
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34833248"
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Använda en Git-lagringsplatsen med ett projekt för Machine Learning-arbetsstationen
Lär dig hur Azure Machine Learning arbetsstationen använder Git för att ange versionskontroll och kontrollera reproducerbara i experimentet vetenskapliga data. Lär dig hur du associerar ditt projekt till ett moln Git-lagringsplats (lagringsplatsen).

Machine Learning Workbench är utformat för Git-integrering. När du skapar ett nytt projekt är projektmappen automatiskt ”Git-initieras” som en lokal Git-lagringsplatsen. En andra, dolda lokal Git repo skapas också, med en filial som heter AzureMLHistory /\<projektet GUID\>. Grenen håller reda på mappen ändringarna för varje körning. 

Koppla Azure Machine Learning-projektet till en Git-lagringsplatsen aktiverar automatisk versionskontroll, lokalt och på distans. Git-lagringsplatsen finns i Visual Studio Team Services (Team Services). Eftersom projektet Machine Learning är associerad med en Git-lagringsplatsen, hämta vem som helst som har åtkomst till fjärranslutna lagringsplatsen senaste källkoden till en annan dator (centrala).  

> [!NOTE]
> Team Services har sin egen åtkomstkontrollistan (ACL) som är oberoende av tjänsten Azure Machine Learning-experiment. Användaråtkomst kan variera mellan en Git-lagringsplatsen och en Machine Learning-arbetsytan eller projekt. Du kan behöva hantera åtkomst. 
> 
> Om du vill ge en gruppmedlem åtkomst till projektet Machine Learning på objektnivå kod eller bara dela arbetsytan, måste du bevilja användaren behörighet att komma åt Team Services Git-lagringsplatsen. 

Om du vill hantera versionskontroll med Git, kan du använda mastergrenen eller skapa andra filialer i lagringsplatsen. Du kan också använda lokal Git-lagringsplatsen och push till fjärransluten Git-lagringsplatsen, om den har etablerats.

Det här diagrammet visar relationen mellan en Team Services Git-lagringsplatsen och Machine Learning-projekt:

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Om du vill komma igång med en fjärransluten Git-lagringsplatsen, genomför du stegen som beskrivs i följande avsnitt.

> [!NOTE]
> Azure Machine Learning stöder för närvarande Git databaser endast på Team Services-konton. Stöd för allmän Git repor som GitHub, är planerad för framtiden.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Steg 1. Skapa ett experiment i Machine Learning-konto
Skapa ett experiment i Machine Learning-konto och installera appen Azure Machine Learning-arbetsstationen. Mer information finns i [installera och skapa Quickstart](../service/quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Steg 2. Skapa ett team projekt eller använda ett befintligt team projekt
I den [Azure-portalen](https://portal.azure.com/), skapa ett nytt grupprojekt:
1. Välj **+**.
2. Sök efter **Team projekt**.
3. Ange informationen som krävs:
    - **Namnet**: ett namn på teamet.
    - **Versionskontroll**: Välj **Git**.
    - **Prenumerationen**: Välj en prenumeration som har ett experiment i Machine Learning-konto.
    - **Plats**: väljer vi rekommenderar region som ligger nära experiment i Machine Learning-resurser.
4. Välj **Skapa**. 

![Skapa ett team projekt i Azure-portalen](media/using-git-ml-project/create_vsts_team.png)

Se till att du loggar in med samma konto för Azure Active Directory (AD Azure) som används för att få åtkomst till Machine Learning-arbetsstationen. Annars det går inte att använda Machine Learning arbetsstationen med hjälp av Azure AD-autentiseringsuppgifter. Ett undantag är om du använder kommandoraden för att skapa Machine Learning-projektet och ange en personlig åtkomsttoken för att komma åt Git-lagringsplatsen. Detta diskuterar vi i detalj senare i artikeln.

Gå direkt till grupprojekt som du skapade genom att använda URL: en https://\<teamprojektets namn\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Steg 3. Ange ett Machine Learning-projektet och Git repo

Om du vill konfigurera ett Machine Learning-projekt har du två alternativ:
- Skapa ett Machine Learning-projekt som har en fjärransluten Git repo
- Koppla ett befintligt Machine Learning-projekt till ett Team Services Git repo

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Skapa ett Machine Learning-projekt som har en fjärransluten Git repo
Öppna Machine Learning arbetsstationen och skapa ett nytt projekt. I den **Git repo** ange URL för Team Services Git-lagringsplatsen från steg 2. Det ser oftast ut så här: https://\<Team Services kontonamn\>.visualstudio.com/_git/\<projektnamn\>

![Skapa ett Machine Learning-projekt som har en Git-lagringsplatsen](media/using-git-ml-project/create_project_with_git_rep.png)

Du kan också skapa projektet genom att använda Azure kommandoradsverktyget (Azure CLI). Du har möjlighet att ange en personlig åtkomsttoken. Machine Learning kan använda denna token för att komma åt Git-lagringsplatsen istället för att använda dina autentiseringsuppgifter för Azure AD:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Team Services personal access token>
```

> [!IMPORTANT]
> Om du väljer mallen tomt projekt, kanske på Git-lagringsplats som du vill använda redan har en mastergrenen. Machine Learning klonar bara mastergrenen lokalt. Mappen aml_config och andra metadata projektfiler läggs till i den lokala projektmappen. 
>
> Om du väljer alla andra projektmall, Git repo *kan* redan har en mastergrenen. Om det inte finns ett fel. Alternativet är att använda den `az ml project create` kommando för att skapa projektet, med en `--force` växla. Detta tar bort filer i den ursprungliga mastergrenen och ersätter dem med nya filer på den mall som du väljer.

Ett nytt projekt i Machine Learning skapas med en fjärransluten Git repo-integration aktiverad. Projektmappen är alltid Git-initieras som en lokal Git-lagringsplatsen. Fjärransluten Git har angetts till fjärranslutna Team Services Git-lagringsplatsen, så du kan skicka incheckningar till fjärransluten Git-lagringsplatsen.

### <a name="associate-an-existing-machine-learning-project-with-a-team-services-git-repo"></a>Koppla ett befintligt Machine Learning-projekt till ett Team Services Git repo
Du kan skapa ett Machine Learning-projekt utan ett Team Services Git-lagringsplatsen och förlitar sig på lokal Git-lagringsplatsen för ögonblicksbilder av rapporthistorik kör. Senare kan du associera ett Team Services Git-lagringsplatsen med befintliga Machine Learning projektet med hjälp av följande kommando:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Team Services account name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Du kan utföra åtgärden Uppdatera lagringsplatsen enbart på ett Machine Learning-projekt som inte har en Git-lagringsplatsen som är kopplade till den. Dessutom när du kopplar en Git-lagringsplatsen en Machine Learning kan du inte ta bort den.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Steg 4. Samla in en ögonblicksbild av projektet i Git-lagringsplatsen
Köra några skript körs i projektet och göra några ändringar mellan körs. Du kan göra detta i appen skrivbord eller från Azure CLI med hjälp av den `az ml experiment submit` kommando. Mer information finns i [klassificera Iris kursen](tutorial-classifying-iris-part-1.md). För varje kör om ändringar görs i en fil i projektmappen en ögonblicksbild av hela projektmappen är allokerat och flyttas till fjärransluten Git-lagringsplatsen under en gren med namnet AzureMLHistory /\<projektet GUID\>. Visa filialer och incheckningar, inklusive AzureMLHistory /\<projektet GUID\> gren, gå till URL för Team Services Git-lagringsplatsen. 

> [!NOTE] 
> Ögonblicksbilden har genomförts endast före körning av skript. För närvarande data prep körning eller en bärbar dator cell körning utlöses inte ögonblicksbilden.

![Kör historik gren](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Det är bäst om du inte använder i grenen tidigare med hjälp av Git-kommandon. Det kan påverka körningshistoriken. I stället använda mastergrenen eller skapa andra grenar för egna Git-åtgärder.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Steg 5. Återställa en tidigare ögonblicksbild i projektet 
Att återställa hela projektmappen till tillståndet för en tidigare körningshistorik ögonblicksbild i Machine Learning arbetsstationen:
1. I aktiviteten liggande (timglas ikonen), Välj **körs**.
2. I den **Kör listan** Välj Kör som du vill återställa.
3. I den **kör detalj** väljer **återställa**.

![Kör historik gren](media/using-git-ml-project/restore_project.png)

Du kan även använda följande kommandon i fönstret Azure CLI i Machine Learning arbetsstationen:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Var försiktig när du kör det här kommandot. Kör det här kommandot skriver över hela projektmappen med ögonblicksbilden som skapades när den specifika kör har inletts. Projektet finns kvar i den aktuella grenen. Detta innebär att du **förlora alla ändringar** i projektmappen aktuella.  

Du kanske vill använda Git för att utföra ändringarna i den aktuella grenen innan du utför den här åtgärden.

## <a name="step-6-use-the-master-branch"></a>Steg 6. Använd mastergrenen
Ett sätt att undvika att förlora den aktuella statusen för projektet av misstag är att genomföra projektet till mastergrenen av Git-lagringsplatsen (eller någon gren som du själv har skapat). Du kan använda Git från kommandoraden eller från dina Favoriter Git-klientverktyg för att använda mastergrenen. Exempel:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Team Services Git repo master branch.
$ git push origin master
```

Du kan nu på ett säkert sätt återställa projektet till en tidigare ögonblicksbild genom att fylla i steg 5. Du kan alltid gå tillbaka till commit som du har gjort på mastergrenen.

## <a name="authentication"></a>Autentisering
Om du enbart använda de körningshistorik funktionerna i Machine Learning ta ögonblicksbilder av projektet och återställa dem, behöver du inte bekymra dig om autentisering för Git-lagringsplatsen. Autentiseringen utförs av experiment i Machine Learning-tjänstnivå.

Om du använder egna Git-verktyg för att hantera versionskontroll, måste du dock hantera autentisering mot fjärransluten Git-lagringsplatsen i Team Services används. I Machine Learning har fjärransluten Git-lagringsplatsen lagts till lokala lagringsplatsen som en fjärransluten Git med hjälp av HTTPS-protokollet. Det innebär att du måste ange ditt användarnamn och lösenord eller en personlig åtkomsttoken när du skickar Git-kommandon (till exempel sändning eller hämtning) fjärrdatorn. Om du vill skapa en personlig åtkomsttoken i ett Team Services Git-lagringsplatsen, följ instruktionerna i [använder en personlig åtkomsttoken för att autentisera](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [använda Team av vetenskapliga data för att organisera dina projektstrukturen](how-to-use-tdsp-in-azure-ml.md).
