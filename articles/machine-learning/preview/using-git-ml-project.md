---
title: Med en Azure Machine Learning arbetsstationen projektet Git Repo | Microsoft Docs
description: "Den här artikeln beskrivs hur du använder en Git-lagringsplats tillsammans med ett projekt för Azure Machine Learning arbetsstationen."
services: machine-learning
author: hning86
ms.author: haining
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 11/18/2017
ms.openlocfilehash: 0cd447a52964578dd2348a786dd57a45ea87516e
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Med ett projekt för Azure Machine Learning arbetsstationen Git-lagringsplats
Det här dokumentet innehåller information om hur Azure Machine Learning arbetsstationen använder Git för att ange versionskontroll och kontrollera reproducerbara i experimentet vetenskapliga data. Det finns också anvisningar om hur du associerar ditt projekt till ett moln Git-lagringsplats.

## <a name="introduction"></a>Introduktion
Azure Machine Learning arbetsstationen är utformad med Git-integrering från grunden upp. När du skapar ett nytt projekt är projektmappen automatiskt ”Git-initieras” som en lokal Git-lagringsplats (lagringsplatsen). Under tiden kan en andra dolda lokal Git repo skapas också med en filial som heter _AzureMLHistory / < project_GUID >_ att hålla reda på mappen ändringarna för varje körning. 

Koppla Azure ML-projektet till en Git-lagringsplatsen finns inom ett Visual Studio Team Service VSTS ()-projekt kan automatisk versionskontroll både lokalt och på distans. Den här associationen kan vem som helst med åtkomst till fjärranslutna lagringsplatsen Hämta senaste källkoden till en annan dator (centrala).  

> [!NOTE]
> VSTS har sin egen åtkomstkontrollista som är oberoende av Azure Machine Learning-experiment tjänsten. Användaråtkomst kan variera mellan en Git-lagringsplatsen och en arbetsyta för Azure ML eller projekt och måste hanteras. Så om du vill dela dela din Azure ML-projekt med en gruppmedlem inklusive kodåtkomst, förutom bara arbetsytan, måste du explicit ge denne tillgång till VSTS Git-lagringsplatsen. 

Med Git, är det också möjligt att hantera versionskontroll uttryckligen med hjälp av den _master_ Förgrena eller genom att skapa andra filialer på lagringsplatsen. Du kan bara använda lokal Git-lagringsplatsen, och du kan också push-installera fjärransluten Git-lagringsplatsen om har etablerats.

Det här diagrammet visar relationen mellan en VSTS Git-lagringsplatsen och ett Azure ML-projekt:

![AML Git](media/using-git-ml-project/aml_git.png)

Om du vill komma igång med en fjärransluten Git-lagringsplatsen, följer du anvisningarna grundläggande.

> [!NOTE]
> Azure Machine Learning stöder för närvarande endast Git-databaser på VSTS konton. Stöd för allmänna Git repor (till exempel GitHub och o.s.v.) är planerad i framtiden.

## <a name="step-1-create-an-azure-ml-experimentation-account"></a>Steg 1. Skapa ett experiment Azure ML-konto
Om den inte redan, skapa ett konto i Azure ML experiment och installera appen Azure ML-arbetsstationen. Se mer information i den [installera och skapa Quickstart](quickstart-installation.md).

## <a name="step-2-create-a-team-project-or-use-an-existing-team-project"></a>Steg 2. Skapa ett Team projekt eller använda ett befintligt Team projekt
Från [Azure-portalen](https://portal.azure.com/), skapa en ny **grupprojekt**.
1. Klicka på**+**
2. Sök efter **”Team projekt”**
3. Ange informationen som krävs.
    - Namn: Ett namn på teamet.
    - Versionskontroll: **Git**
    - Prenumerationen: En med ett Azure Machine Learning experiment konto.
    - Plats: Förblir helst i en region som ligger nära resurserna i Azure Machine Learning-Experiment.
4. Klicka på **Skapa**. 

![Skapa ett Team projekt från Azure-portalen](media/using-git-ml-project/create_vsts_team.png)

Kontrollera att du loggar in med samma konto för Azure Active Directory (AAD) som du använder för att komma åt Azure Machine Learning-arbetsstationen. Annars systemet kan inte komma åt den med hjälp av AAD-autentiseringsuppgift, såvida inte du använda kommandoraden för att skapa Azure ML-projektet och ange en personlig åtkomsttoken för att komma åt Git-lagringsplatsen. Mer om detta senare.

När Grupprojektet har skapats är du redo att gå vidare till nästa steg.

Om du vill gå direkt till grupprojekt precis skapade, URL-Adressen är `https://<team_project_name>.visualstudio.com`.

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Steg 3. Skapa ett nytt Azure ML-projekt med en fjärransluten Git repo
Starta Azure ML-arbetsstationen och skapa ett nytt projekt. Fyll textrutan för Git-lagringsplatsen till lagringsplatsen VSTS Git-URL som du får från steg 2. Det ser oftast ut så här:`http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Skapa Azure ML-projekt med Git repo](media/using-git-ml-project/create_project_with_git_rep.png)

Du kan också skapa projektet med kommandoradsverktyget. Du har möjlighet att ange en personlig åtkomsttoken. Azure ML kan använda denna token för att komma åt Git-lagringsplatsen för din räkning, i stället för att förlita dig på din AAD-certifiering:

```
# create a new project with a Git repo and personal access token.
$ az ml project create -a <experimentation account name> -n <project name> -g <resource group name> -w <workspace name> -r <Git repo URL> --vststoken <VSTS personal access token>
```
> [!IMPORTANT]
> Om du väljer mallen tomt projekt är det OK att Git-lagringsplatsen som du redan har en _master_ grenen. Azure ML klonar bara den _master_ Förgrena lokalt och lägger till den `aml_config` mappen och andra projektfiler metadata till lokala projektmappen. Men om du väljer andra projektmall Git-lagringsplatsen får inte redan ha en _master_ branch eller visas ett felmeddelande. Alternativet är att använda `az ml project create` kommandoradsverktyget för att skapa projektet och ange en `--force` växla. Detta tar bort filer på den ursprungliga mastergrenen och ersätta dem med nya filer på den mall som du väljer.

Nu skapas ett nytt Azure ML-projekt med fjärransluten Git repo integration aktiverad och redo att sätta igång. Projektmappen är alltid Git-initieras som en lokal Git-lagringsplatsen. Git och _remote_ är inställd på fjärranslutna VSTS Git-lagringsplatsen så incheckningar kan skickas till en fjärransluten Git-lagringsplatsen.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Steg 3a. Koppla ett befintligt Azure ML-projekt till en VSTS Git repo
Alternativt kan du också skapa ett Azure ML-projekt utan en VSTS Git-lagringsplatsen och bara förlita dig på lokala Git-lagringsplatsen för ögonblicksbilder av rapporthistorik kör. Och du kan associera en VSTS Git-lagringsplatsen senare med befintliga Azure ML projektet med följande kommando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

> [!NOTE] 
> Du kan bara utföra update lagringsplatsen åtgärden på ett Azure ML-projekt som inte har en Git-lagringsplatsen som är kopplade till den. Och när Git-lagringsplatsen är associerad kan inte tas bort.

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Steg 4. Avbilda projektet ögonblicksbild i Git repo
Nu kan du köra några skript körs i projektet, göra vissa ändringar emellan körs. Du kan göra detta från skrivbordsappen, eller från med hjälp av CLI `az ml experiment submit` kommando. Mer information kan du följa den [klassificera Iris kursen](tutorial-classifying-iris-part-1.md). För varje kör om det finns ändringar som gjorts i alla filer i projektmappen en ögonblicksbild av hela projektmappen är allokerade och flyttas fjärransluten Git-lagringsplatsen under en gren med namnet `AzureMLHistory/<Project_GUID>`. Du kan visa filialer och incheckningar genom att bläddra till URL: en för VSTS Git-lagringsplatsen och hitta förgreningssidan. 

> [!NOTE] 
> Ögonblicksbilden genomförs endast före körning av skript. För närvarande data prep körning eller en bärbar dator cell körningen inte utlöser ögonblicksbilden.

![Kör historik gren](media/using-git-ml-project/run_history_branch.png)

> [!IMPORTANT] 
> Det är bäst om du inte fungerar i grenen historik själv med hjälp av Git-kommandon. Detta kan förstöra körningshistorik. Använd mastergrenen eller skapa andra filialer i stället för en egen Git-åtgärder.

## <a name="step-5-restore-a-previous-project-snapshot"></a>Steg 5. Återställa en tidigare ögonblicksbild i projektet 
Att återställa hela projektmappen till tillståndet för en tidigare körningshistorik projekt tillstånd ögonblicksbild, från Azure ML-arbetsstationen:
1. Klicka på **körs** i aktiviteten liggande (timme om ikonen).
2. Från den **Kör listan** visa, klicka på kör du vill återställa.
3. Från den **kör detalj** visa, klicka på **återställa**.

![Kör historik gren](media/using-git-ml-project/restore_project.png)

Du kan också använda följande kommando från fönstret Azure ML-arbetsstationen CLI.

```azurecli
# discover the run I want to restore snapshot from:
$ az ml history list -o table

# restore the snapshot from a particular run
$ az ml project restore --run-id <run_id>
```

Genom att köra det här kommandot vi skriver över hela projektmappen med ögonblicksbilden utförs när den viss körningen har inletts. Men projektet finns kvar på den aktuella grenen. Det innebär att du kommer att **förlora alla ändringar** i projektmappen aktuella. Så ta vara extra försiktig när du kör det här kommandot. Du kanske vill Git för att utföra ändringarna i den aktuella grenen innan du utför åtgärden ovan. Mer information finns i innan.

## <a name="step-6-use-the-master-branch"></a>Steg 6. Använd mastergrenen
Ett sätt att undvika att av misstag är förlorar den aktuella statusen för projektet, att genomföra projektet till mastergrenen (eller valfri gren som du själv har skapat) för Git-lagringsplatsen. Du kan använda Git direkt från kommandoraden (eller din andra favorit Git klientverktyg) för att använda mastergrenen. Exempel:

```sh
# check status to make sure you are on the master branch (or branch of your choice)
$ git status

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'these are my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Nu på ett säkert sätt kan du återställa projekt till en tidigare ögonblicksbild följande steg 5 om du vet att du kan alltid gå tillbaka till genomförandet du precis har gjort i bakgrunden grenen.

## <a name="authentication"></a>Autentisering
Om du bara använder körningshistorik funktioner i Azure ML för att ta ögonblicksbilder av projektet och återställa dem, behöver du inte bekymra dig om autentisering för Git-lagringsplatsen. Det har åtgärdat av experiment Service skiktet.

Om du använder egna Git-verktyg för att hantera versionskontroll, behöver du dock ska kunna hantera autentisering mot fjärransluten Git-lagringsplatsen i VSTS. I Azure ML läggs fjärransluten Git-lagringsplatsen till den lokala lagringsplatsen som ett Git-fjärråtkomst med hjälp av HTTPS-protokollet. Detta innebär att när du skickar Git-kommandon fjärrdatorn (till exempel push och pull-), måste du ange användarnamn och lösenord eller personliga åtkomsttoken. Följ [instruktionerna](https://docs.microsoft.com/vsts/accounts/use-personal-access-tokens-to-authenticate) skapa personlig åtkomst-token i en VSTS Git-lagringsplatsen.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om Team av vetenskapliga data att organisera dina projektstruktur finns [struktur för ett projekt med TDSP](how-to-use-tdsp-in-azure-ml.md)
