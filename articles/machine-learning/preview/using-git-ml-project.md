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
ms.date: 11/16/2017
ms.openlocfilehash: c91eadd69eaf16b2496f4d7247e5b0121904e172
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="using-git-repository-with-an-azure-machine-learning-workbench-project"></a>Med ett projekt för Azure Machine Learning arbetsstationen Git-lagringsplats
Det här dokumentet innehåller information om hur Azure Machine Learning arbetsstationen använder Git så reproducerbara i experimentet vetenskapliga data. Det finns också anvisningar om hur du associerar ditt projekt till ett moln Git-lagringsplats.

## <a name="introduction"></a>Introduktion
Azure Machine Learning arbetsstationen är utformad med Git-integrering från grunden upp. När du skapar ett nytt projekt projektmappen är automatiskt ”Git-initieras” som en lokal Git-lagringsplats (lagringsplatsen) medan en andra dolda lokal Git repo skapas också med en filial som heter _AzureMLHistory / < project_GUID >_ till hålla reda på mappen ändringarna för varje körning. 

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


> [!TIP]
> Se till att logga in med Azure Active Directory (AAD)-kontot som används för att komma åt Azure Machine Learning-arbetsstationen. Annars nytt grupprojekt kan slutet upp under fel klient-ID och Azure Machine Learning kanske inte att hitta den. I det här fallet skulle du behöva använda kommandoradsgränssnittet och ange VSTS token.

När Grupprojektet har skapats är du redo att gå vidare till nästa steg.

Om du vill gå direkt till grupprojekt precis skapade, URL-Adressen är `https://<team_project_name>.visualstudio.com`.

> [!NOTE]
> Azure Machine Learning stöder för närvarande endast tomt Git repor med ingen mastergrenen. Du kan använda från kommandoradsgränssnittet,--kraft argumentet först ta bort din mastergrenen. 

## <a name="step-3-create-a-new-azure-ml-project-with-a-remote-git-repo"></a>Steg 3. Skapa ett nytt Azure ML-projekt med en fjärransluten Git repo
Starta Azure ML-arbetsstationen och skapa ett nytt projekt. Fyll textrutan för Git-lagringsplatsen till lagringsplatsen VSTS Git-URL som du får från steg 2. Det ser oftast ut så här:`http://<vsts_account_name>.visualstudio.com/_git/<project_name>`

![Skapa Azure ML-projekt med Git repo](media/using-git-ml-project/create_project_with_git_rep.png)

Nu skapas ett nytt Azure ML-projekt med fjärransluten Git repo integration aktiverad och redo att sätta igång. Projektmappen är alltid Git-initieras som en lokal Git-lagringsplatsen. Git och _remote_ är inställd på fjärranslutna VSTS Git-lagringsplatsen så incheckningar kan skickas till en fjärransluten Git-lagringsplatsen.

## <a name="step-3a-associate-an-existing-azure-ml-project-with-a-vsts-git-repo"></a>Steg 3a. Koppla ett befintligt Azure ML-projekt till en VSTS Git repo
Alternativt kan du också skapa ett Azure ML-projekt utan en VSTS Git-lagringsplatsen och bara förlita dig på lokala Git-lagringsplatsen för ögonblicksbilder av rapporthistorik kör. Och du kan associera en VSTS Git-lagringsplatsen senare med befintliga Azure ML projektet med följande kommando:

```azurecli
# make sure you are in the project path so CLI has context of your current project
$ az ml project update --repo http://<vsts_account_name>.visualstudio.com/_git/<project_name>
```

## <a name="step-4-capture-project-snapshot-in-git-repo"></a>Steg 4. Avbilda projektet ögonblicksbild i Git repo
Nu kan du köra några körs i projektet, göra vissa ändringar emellan körs. Du kan göra detta från skrivbordsappen, eller från med hjälp av CLI `az ml experiment submit` kommando. Mer information kan du följa den [klassificera Iris kursen](tutorial-classifying-iris-part-1.md). För varje kör om det finns ändringar som gjorts i alla filer i projektmappen en ögonblicksbild av hela projektmappen är allokerade och flyttas fjärransluten Git-lagringsplatsen under en gren med namnet `AzureMLHistory/<Project_GUID>`. Du kan visa filialer och incheckningar genom att bläddra till URL: en för VSTS Git-lagringsplatsen och hitta förgreningssidan. 

![Kör historik gren](media/using-git-ml-project/run_history_branch.png)

Obs det är bättre fungerar inte i grenen historik själv. Detta kan förstöra med körningshistorik. Använd mastergrenen eller skapa andra filialer i stället för en egen Git-åtgärder.

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

Genom att köra det här kommandot vi skriver över hela projektmappen med ögonblicksbilden utförs när den viss körningen har inletts. Men projektet finns kvar på den aktuella grenen. Det innebär att du kommer att **förlora alla ändringar** i projektmappen aktuella. Så ta vara extra försiktig när du kör det här kommandot.

## <a name="step-6-use-the-master-branch"></a>Steg 6. Använd mastergrenen
Ett sätt att undvika att av misstag är förlorar den aktuella statusen för projektet, att genomföra projektet till mastergrenen (eller valfri gren som du själv har skapat) för Git-lagringsplatsen. Du kan använda Git direkt från kommandoraden (eller din andra favorit Git klientverktyg) för att använda mastergrenen. Exempel:

```
# make sure you are on the master branch (or branch of your choice)
$ git checkout master

# stage all changes
$ git add -A

# commit all changes locally on the master branch
$ git commit -m 'this is my updates so far'

# push changes into the remote VSTS Git repo master branch.
$ git push origin master
```

Nu på ett säkert sätt kan du återställa projekt till en tidigare ögonblicksbild följande steg 5 om du vet att du kan alltid gå tillbaka till genomförandet du precis har gjort i bakgrunden grenen.

## <a name="authentication"></a>Autentisering
Om du bara använder körningshistorik funktioner i Azure ML för att ta ögonblicksbilder av projektet och återställa dem, behöver du inte bekymra dig om autentisering för Git-lagringsplatsen. Det har åtgärdat av experiment Service skiktet.

Om du använder egna Git-verktyg för att hantera versionskontroll, behöver du dock ska kunna hantera autentisering mot fjärransluten Git-lagringsplatsen i VSTS. Det vill säga behöver du konfigurera autentisering med Git-lagringsplatsen på den lokala datorn innan du kan utfärda Git-kommandon mot den fjärranslutna Git-lagringsplatsen. 

Det enklaste sättet att göra detta är att skapa en SSH-nyckel och överföra den offentliga nyckeldelen till säkerhetsinställningar för Git-lagringsplatsen.

### <a name="generate-ssh-key"></a>Skapa SSH-nyckel 
Första vi ska generera ett par SSH-nycklar på datorn.

#### <a name="on-windows"></a>I Windows:
Starta Git GUI-skrivbordsapp för Windows och på _hjälp_ -menyn klickar du på _visa SSH-nyckeln_.

![SSH Key](media/using-git-ml-project/git_gui.png)

Kopiera SSH till Urklipp.

#### <a name="on-macos"></a>I macOS:
Snabbsteg från Kommandotolken:
```
# generate the SSH key
$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"

# start the SSH agent in the background
$ eval "$(ssh-agent -s)"

# add newly generated SSH key to the SSH agent
$ ssh-add -K ~/.ssh/id_rsa

# display the public key so you can copy it.
$ more ~/.ssh/id_rsa.pub
```
Mer information om stegen finns på [GitHub artikeln](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).

### <a name="upload-public-key-to-git-repo"></a>Överför offentliga nyckel till Git repo
Gå till startsidan för Visual Studio-konto: https://<vsts_account_name>.visualstudio.com och logga in, klicka på säkerhet under din avatar.

Lägg till en offentlig SSH-nyckel, klistra in den offentliga SSH-nyckeln som du får från föregående steg och ge det ett namn. Du kan lägga till flera nycklar här.

Nu kan du utfärda Git-kommandon lokalt mot ta bort lagringsplatsen med ingen ytterligare explicit autentisering krävs.

### <a name="read-more"></a>Läs mer
Följ dessa två artiklar (antingen metoden kan arbeta) mer information om hur du aktiverar autentisering med lokala till fjärransluten Git-lagringsplatsen i VSTS.
- [Använda SSH-nyckelautentisering](https://www.visualstudio.com/en-us/docs/git/use-ssh-keys-to-authenticate)
- [Använda Git autentiseringsuppgifter chefer](https://www.visualstudio.com/en-us/docs/git/set-up-credential-managers)


## <a name="next-steps"></a>Nästa steg
Lär dig mer om Team av vetenskapliga data att organisera dina projektstruktur finns [struktur för ett projekt med TDSP](how-to-use-tdsp-in-azure-ml.md)
