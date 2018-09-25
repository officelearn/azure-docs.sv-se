---
title: Använda en Git-lagringsplats med ett Azure Machine Learning Workbench-projekt | Microsoft Docs
description: Den här artikeln förklarar hur du använder en Git-lagringsplats tillsammans med ett Azure Machine Learning Workbench-projekt.
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ROBOTS: NOINDEX
ms.openlocfilehash: 16c102641321117f4776d761aba6c2148d15f1f5
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995658"
---
# <a name="use-a-git-repo-with-a-machine-learning-workbench-project"></a>Använda en Git-lagringsplats med ett Machine Learning Workbench-projekt

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Lär dig hur Azure Machine Learning Workbench använder Git för att tillhandahålla versionskontroll och säkerställa reproducerbarhet i din dataexperiment. Lär dig hur du kopplar ditt projekt till ett moln Git-lagringsplats (lagringsplats).

Machine Learning Workbench är utformat för Git-integrering. När du skapar ett nytt projekt är projektmappen automatiskt ”Git-initieras” som en lokal Git-lagringsplats. En andra, dolda lokal Git-lagringsplats också skapas med en gren med namnet AzureMLHistory /\<projektet GUID\>. Grenen håller reda på mappen ändringarna för varje körning. 

Associera Azure Machine Learning-projekt med en Git-lagringsplats kan automatisk versionskontroll, lokalt och på distans. Azure DevOps är värd för Git-lagringsplats. Eftersom Machine Learning-projekt är associerad med en Git-lagringsplats, kan vem som helst som har åtkomst till fjärranslutna lagringsplatsen ladda ned den senaste källkoden till en annan dator (centrala).  

> [!NOTE]
> Azure DevOps har sin egen åtkomstkontrollistan (ACL) som är oberoende av Azure Machine Learning-experimentering. Användaråtkomst kan variera mellan en Git-lagringsplats och en Machine Learning-arbetsyta eller projekt. Du kan behöva hantera åtkomst. 
> 
> Om du vill ge en gruppmedlem kodnivå åtkomst till din Machine Learning-projekt eller bara dela arbetsytan, måste du bevilja användaren behörighet att komma åt Azure DevOps Git-lagringsplats. 

För att hantera versionskontroll med Git, kan du använda huvudgrenen eller skapa andra grenar i lagringsplatsen. Du kan också använda den lokala Git-lagringsplatsen och skicka till den fjärranslutna Git-lagringsplatsen, om den har etablerats.

Det här diagrammet visar relationen mellan en Azure DevOps Git-lagringsplats och ett Machine Learning-projekt:

![Azure Machine Learning Git](media/using-git-ml-project/aml_git.png)

Kom igång med en fjärransluten Git-lagringsplats, slutför du stegen som beskrivs i följande avsnitt.

> [!NOTE]
> Azure Machine Learning stöder för närvarande Git-lagringsplatser endast på Azure DevOps-organisationer.

## <a name="step-1-create-a-machine-learning-experimentation-account"></a>Steg 1. Skapa ett konto för Machine Learning-experimentering
Skapa en Machine Learning-experimenteringskonto och installera Azure Machine Learning Workbench-appen. Mer information finns i [installera snabbstarten och skapa](quickstart-installation.md).

## <a name="step-2-create-an-azure-devops-project-or-use-an-existing-project"></a>Steg 2. Skapa ett Azure DevOps-projekt eller Använd ett befintligt projekt
I den [Azure-portalen](https://portal.azure.com/), skapa ett nytt projekt:
1. Välj **+**.
2. Sök efter **Team projekt**.
3. Ange informationen som krävs:
    - **Namn på**: ett namn på teamet.
    - **Versionskontroll**: Välj **Git**.
    - **Prenumeration**: Välj en prenumeration som har ett Machine Learning-experimenteringskonto.
    - **Plats**: Vi rekommenderar, Välj en region som ligger nära dina Machine Learning-experimentering-resurser.
4. Välj **Skapa**. 

![Skapa ett projekt i Azure portal](media/using-git-ml-project/create_vsts_team.png)

Se till att du loggar in med hjälp av samma Azure Active Directory (Azure AD)-kontot som används för att få åtkomst till Machine Learning Workbench. I annat fall det går inte att använda Machine Learning Workbench genom att använda dina autentiseringsuppgifter för Azure AD. Ett undantag är om du använda kommandoraden för att skapa Machine Learning-projekt och ange en personlig åtkomsttoken för att komma åt Git-lagringsplats. Detta beskrivs i närmare detalj i artikeln.

Om du vill gå direkt till projektet som du skapade, använder du URL: en https://\<projektnamn\>. visualstudio.com.

## <a name="step-3-set-up-a-machine-learning-project-and-git-repo"></a>Steg 3. Ange ett Machine Learning-projekt och Git-lagringsplats

Om du vill konfigurera en Machine Learning-projekt har du två alternativ:
- Skapa en Machine Learning-projekt som har en fjärransluten Git-lagringsplats
- Koppla ett befintligt Machine Learning-projekt till en Azure DevOps Git-lagringsplats

### <a name="create-a-machine-learning-project-that-has-a-remote-git-repo"></a>Skapa en Machine Learning-projekt som har en fjärransluten Git-lagringsplats
Öppna Machine Learning Workbench och skapa ett nytt projekt. I den **Git-lagringsplats** anger URL: en för Azure DevOps Git-lagringsplats från steg 2. Det ser oftast ut så här: https://\<Azure DevOps-organisationsnamn\>.visualstudio.com/_git/\<projektnamn\>

![Skapa en Machine Learning-projekt som har en Git-lagringsplats](media/using-git-ml-project/create_project_with_git_rep.png)

Du kan också skapa projektet genom att använda Azure kommandoradsverktyget (Azure CLI). Du har möjlighet att ange en personlig åtkomsttoken. Machine Learning kan använda denna token för att få åtkomst till Git-lagringsplats istället för att använda dina autentiseringsuppgifter för Azure AD:

```
# Create a new project that has a Git repo by using a personal access token.
$ az ml project create -a <Experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <Azure DevOps personal access token>
```

> [!IMPORTANT]
> Om du väljer mallen tomt projekt kanske Git-lagringsplats som du väljer att använda redan har en huvudgren. Machine Learning klonar bara huvudgrenen lokalt. Mappen aml_config och andra metadata projektfiler läggs till i den lokala projektmappen. 
>
> Om du väljer alla andra projektmall, Git-lagringsplats *kan* redan har en huvudgren. I annat fall visas ett fel. Alternativet är att använda den `az ml project create` kommando för att skapa projektet, med en `--force` växla. Detta tar bort filer i den ursprungliga huvudgrenen och ersätter dem med de nya filerna i mallen som du väljer.

Ett nytt Machine Learning-projekt skapas med fjärranslutna Git repo-integrering aktiverat. Projektmappen är alltid Git-initieras som en lokal Git-lagringsplats. Fjärransluten Git har angetts till den fjärranslutna Azure DevOps Git-lagringsplatsen, så att du kan skicka incheckningar till den fjärranslutna Git-lagringsplatsen.

### <a name="associate-an-existing-machine-learning-project-with-an-azure-devops-git-repo"></a>Koppla ett befintligt Machine Learning-projekt till en Azure DevOps Git-lagringsplats
Du kan skapa en Machine Learning-projekt utan en Azure DevOps Git-lagringsplats och förlitar sig på den lokala Git-lagringsplatsen för körningshistoriken ögonblicksbilder. Senare, kan du associera en Azure DevOps Git-lagringsplats med den här befintliga Machine Learning-projekt med hjälp av följande kommando:

```azurecli
# Ensure that you are in the project path so Azure CLI has the context of your current project.
$ az ml project update --repo https://<Azure DevOps organization name>.visualstudio.com/_git/<project name>
```

> [!NOTE] 
> Du kan utföra update-lagringsplatsen åtgärden endast för ett Machine Learning-projekt som inte har en Git-lagringsplats som är kopplade till den. Dessutom när du kopplar en Git-lagringsplats en Machine Learning kan du inte ta bort den.

## <a name="step-4-capture-a-project-snapshot-in-the-git-repo"></a>Steg 4. Avbilda en projektögonblicksbild i Git-lagringsplats
Köra några skript i projektet och göra några ändringar mellan körningar. Du kan göra detta i skrivbordsappen eller från Azure CLI med hjälp av den `az ml experiment submit` kommando. Mer information finns i den [klassificera Iris, självstudie](tutorial-classifying-iris-part-1.md). För varje körning om ändringar görs i alla filer i projektmappen, en ögonblicksbild av hela projektmappen är allokerade och skickas till den fjärranslutna Git-lagringsplatsen under en gren med namnet AzureMLHistory /\<projektet GUID\>. Visa grenar och incheckningar, inklusive AzureMLHistory /\<projektet GUID\> gren, gå till URL: en för Azure DevOps Git-lagringsplats. 

> [!NOTE] 
> Ögonblicksbilden har genomförts endast innan en körning av skript. För närvarande en dataförberedelser körning eller körning av en Notebook cell utlöses inte ögonblicksbilden.

![Körningshistorik gren](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Det är bäst om du inte använder grenen historik med hjälp av Git-kommandon. Det kan påverka körningshistoriken. I stället använda huvudgrenen eller skapa andra grenar för din egen Git-åtgärder.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Steg 5. Återställa en tidigare projektögonblicksbild 
Att återställa hela projektmappen till tillståndet för en tidigare körningshistoriken ögonblicksbild i Machine Learning Workbench:
1. I aktiviteten liggande (timglas ikon), Välj **körs**.
2. I den **Jämförelsefönstret** väljer körningen som du vill återställa.
3. I den **kör detalj** väljer **återställa**.

![Körningshistorik gren](media/using-git-ml-project/restore_project.png)

Du kan även använda följande kommandon i Azure CLI-fönstret i Machine Learning Workbench:

```azurecli
# Discover the run I want to restore a snapshot from.
$ az ml history list -o table

# Restore the snapshot from a specific run.
$ az ml project restore --run-id <run ID>
```

Var försiktig när du kör det här kommandot. Kör det här kommandot skriver över hela projektmappen med den ögonblicksbild som vidtogs när den specifika körningen har startats. Ditt projekt kvar i den aktuella grenen. Detta innebär att du **förlora alla ändringar** i den aktuella projektmappen.  

Du kanske vill använda Git för att genomföra ändringarna till den aktuella grenen innan du utför den här åtgärden.

## <a name="step-6-use-the-master-branch"></a>Steg 6. Använd huvudgrenen
Ett sätt att undvika att av misstag förlora den aktuella statusen för projekt är att genomföra projektet huvuddelen av Git-lagringsplats (eller valfri gren som du har skapat själv). Du kan använda Git från kommandoraden eller från din favorit Git-klientverktyg ska fungera på huvudgrenen. Exempel:

```sh
# Check status to make sure you are on the master branch (or branch of your choice).
$ git status

# Stage all changes.
$ git add -A

# Commit all changes locally on the master branch.
$ git commit -m 'these are my updates so far'

# Push changes to the remote Azure DevOps Git repo master branch.
$ git push origin master
```

Du kan nu på ett säkert sätt återställa projektet till en tidigare ögonblicksbild genom att fylla i steg 5. Du kan alltid gå tillbaka till genomförandet som du just har gjort på huvudgrenen.

## <a name="authentication"></a>Autentisering
Om du enbart använda körningshistoriken funktionerna i Machine Learning för att ta projektögonblicksbilder och återställa dem, behöver du inte bekymra dig om autentisering för Git-lagringsplats. Autentisering utförs av Machine Learning-experimentering-tjänstnivå.

Om du använder din egen Git-verktyg för att hantera versionskontroll, måste du dock hanterar autentisering mot den fjärranslutna Git-lagringsplatsen i Azure DevOps används. I Maskininlärning måste har den fjärranslutna Git-lagringsplatsen lagts till den lokala lagringsplatsen som en fjärransluten Git med hjälp av HTTPS-protokollet. Det innebär att när du förse fjärransluten Git-kommandon (till exempel sändning eller hämtning) måste du ange ditt användarnamn och lösenord eller en personlig åtkomsttoken. Om du vill skapa en personlig åtkomsttoken i en Azure DevOps Git-lagringsplats, följer du anvisningarna i [använder en personlig åtkomsttoken för att autentisera](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [använda Team Data Science Process för att organisera din projektstruktur](how-to-use-tdsp-in-azure-ml.md).
