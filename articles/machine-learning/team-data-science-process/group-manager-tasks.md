---
title: Gruppchefsuppgifter för Team Data Science Process
description: Följ den här detaljerade genomgången av de uppgifter som en gruppchef slutför i ett data science-teamprojekt.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ec7f4242e5046e90fdf0eb8c6c0579f402e4f55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721361"
---
# <a name="team-data-science-process-group-manager-tasks"></a>Gruppchefsuppgifter för Team Data Science Process

I den här artikeln beskrivs de uppgifter som en *gruppchef* slutför för en datavetenskapsorganisation. Gruppchefen hanterar hela datavetenskapsenheten i ett företag. En datavetenskapsenhet kan ha flera team, som alla arbetar med många datavetenskapliga projekt i olika affärsvertikaser. Gruppchefens mål är att skapa en samarbetsgruppsmiljö som standardiserar [på Team Data Science Process](overview.md) (TDSP). En översikt över alla personalroller och tillhörande uppgifter som hanteras av ett data science-team som standardiserar på TDSP finns i [Roller och uppgifter för teamdatavetenskap.](roles-tasks.md)

I följande diagram visas de sex huvudkonfigurationsuppgifterna för gruppchef. Gruppchefer kan delegera sina uppgifter till ersättningar, men de uppgifter som är associerade med rollen ändras inte.

![Gruppchefsuppgifter](./media/group-manager-tasks/tdsp-group-manager.png)

1. Konfigurera en **Azure DevOps-organisation** för gruppen.
2. Skapa **standardprojektet GroupCommon** i Azure DevOps-organisationen.
3. Skapa **groupprojectTemplate-databasen** i Azure Repos.
4. Skapa **databasen GroupUtilities** i Azure Repos.
5. Importera innehållet i Microsoft TDSP-teamets **ProjectTemplate-** och **Utilities-databaser** till gruppens gemensamma databaser.
6. Konfigurera **medlemskap** och **behörigheter** för gruppmedlemmar att komma åt gruppen.

Följande självstudiekurs går igenom stegen i detalj. 

> [!NOTE] 
> Den här artikeln använder Azure DevOps för att konfigurera en TDSP-gruppmiljö, eftersom det är så du implementerar TDSP på Microsoft. Om din grupp använder andra plattformar för kodhosting eller utveckling är gruppchefens uppgifter desamma, men sättet att slutföra dem kan vara annorlunda.

## <a name="create-an-organization-and-project-in-azure-devops"></a>Skapa en organisation och ett projekt i Azure DevOps

1. Gå till [visualstudio.microsoft.com](https://visualstudio.microsoft.com)väljer du **Logga in längst** upp till höger och logga in på ditt Microsoft-konto. 
   
   ![Logga in på ditt Microsoft-konto](./media/group-manager-tasks/signinvs.png)
   
   Om du inte har ett Microsoft-konto väljer du **Registrera dig nu,** skapar ett Microsoft-konto och loggar in med det här kontot. Om din organisation har en Visual Studio-prenumeration loggar du in med autentiseringsuppgifterna för den prenumerationen.
   
1. När du har loggat in väljer du Skapa **ny organisation**längst upp till höger på sidan Azure DevOps .
   
   ![Skapa ny organisation](./media/group-manager-tasks/create-organization.png)
   
1. Om du uppmanas att godkänna användarvillkoren, sekretesspolicyn och uppförandekoden väljer du **Fortsätt**.
   
1. I registreringsdialogrutan namnger du din Azure DevOps-organisation och accepterar värdområdestilldelningen, eller listrutan och väljer en annan region. Välj sedan **Fortsätt**. 

1. Under **Skapa ett projekt för att komma igång**anger du *GroupCommon*och väljer sedan **Skapa projekt**. 
   
   ![Skapa projekt](./media/group-manager-tasks/create-project.png)

Sidan **Sammanfattning av GroupCommon-projektet** **Summary** öppnas. Sidans URL är *\//\<https: servernamn\<>/ organisationsnamn>/GroupCommon*.

![Sidan Sammanfattning av projekt](./media/group-manager-tasks/project-summary.png)

## <a name="set-up-the-group-common-repositories"></a>Konfigurera gruppkonskontensivt databaser

Azure-lagringsplatser är värd för följande typer av databaser för din grupp:

- **Grupp gemensamma databaser:** Allmänna databaser som flera team inom en datavetenskapsenhet kan anta för många datavetenskapsprojekt. 
- **Teamdatabaser**: Databaser för specifika team inom en datavetenskapsenhet. Dessa databaser är specifika för ett teams behov och kan användas för flera projekt inom det teamet, men är inte tillräckligt allmänna för att användas i flera team inom en datascience-enhet.
- **Projektdatabaser**: Databaser för specifika projekt. Sådana databaser kanske inte är tillräckligt allmänna för flera projekt inom ett team eller för andra team i en datavetenskapsenhet.

Om du vill konfigurera gruppkondyst förlagor i projektet: 
- Byta namn på **standarddatabasen GroupCommon** till **GroupProjectTemplate**
- Skapa en ny **GroupUtilities-databas**

### <a name="rename-the-default-project-repository-to-groupprojecttemplate"></a>Byta namn på standardprojektdatabasen till GroupProjectTemplate

Så här byter du namn på **standardgruppenCommon-projektlagringsplats** till **GroupProjectTemplate:**

1. På **sidan** Sammanfattning av **GroupCommon-projekt** väljer du **Repos**. Den här åtgärden tar dig till **standarddatabasen GroupCommon** för GroupCommon-projektet, som för närvarande är tomt.
   
1. Högst upp på sidan rullgardinspilen bredvid **GroupCommon** och välj **Hantera databaser**.
   
   ![Hantera databaser](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
   
1. På sidan **Projektinställningar** väljer du **...** bredvid **GroupCommon**och väljer sedan **Byt namn på databasen**. 
   
   ![Välj... och välj sedan Byt namn på databas](./media/group-manager-tasks/rename-groupcommon-repo-4.png)
   
1. Ange *GruppProjektmall*i fönstret **Byt namn på groupcommon-databasen** och välj sedan **Byt namn**på . 
   
   ![Byta namn på databas](./media/group-manager-tasks/rename-groupcommon-repo-6.png)

### <a name="create-the-grouputilities-repository"></a>Skapa databasen GroupUtilities

Så här skapar du databasen **GroupUtilities:**

1. På **sidan** Sammanfattning av **GroupCommon-projekt** väljer du **Repos**. 
   
1. Högst upp på sidan rullgardinspilen bredvid **GroupProjectTemplate** och välj **Ny databas**.
   
   ![Välj Ny databas](./media/group-manager-tasks/create-grouputilities-repo-1.png)
   
1. I dialogrutan **Skapa en ny databas** väljer du **Git** som **typ,** anger *GroupUtilities* som **databasnamn**och väljer sedan **Skapa**.
   
   ![Skapa databas för grupputverktyg](./media/group-manager-tasks/create-grouputilities-repo-2.png)
   
1. På sidan **Projektinställningar** väljer du **Databaser** under **Databaser** i den vänstra navigeringen för att se de två gruppdatabaserna: **GroupProjectTemplate** och **GroupUtilities**.
   
   ![Två gruppdatabaser](./media/group-manager-tasks/two-repositories.png)

## <a name="import-the-microsoft-tdsp-team-repositories"></a>Importera Microsoft TDSP-teamdatabaser

I den här delen av självstudien importerar du innehållet i **ProjectTemplate-** och **Utilities-databaserna** som hanteras av Microsoft TDSP-teamet till dina **GroupProjectTemplate-** och **GroupUtilities-databaser.** 

Så här importerar du TDSP-teamdatabaserna:

1. På startsidan för **GroupCommon-projektet** väljer du **Repos** i den vänstra navigeringen. **Standardreponypon för GroupProjectTemplate** öppnas. 
   
1. På sidan **GroupProjectTemplate är tom** väljer du **Importera**. 
   
   ![Välj Importera](./media/group-manager-tasks/import-repo.png)
   
1. I dialogrutan **Importera en Git-databas** väljer du **Git** som **källtyp**och anger *https:\//github.com/Azure/Azure-TDSP-ProjectTemplate.git* för **klon-URL:en**. Välj sedan **Importera**. Innehållet i Microsoft TDSP-teamet ProjectTemplate-databasen importeras till databasen GroupProjectTemplate. 
   
   ![Importera Microsoft TDSP-gruppdatabas](./media/group-manager-tasks/import-repo-2.png)
   
1. Högst upp på sidan **Repos** rullgardinsmenyn och välj databasen **GroupUtilities.**
   
1. Upprepa importprocessen för att importera innehållet i **Utilities** Microsoft TDSP-teamutilities-databasen, *https:\//github.com/Azure/Azure-TDSP-Utilities.git*, till databasen **GroupUtilities.** 
   
Var och en av dina två gruppdatabaser innehåller nu alla filer, utom filerna i *.git-katalogen,* från Microsoft TDSP-teamets motsvarande databas. 

## <a name="customize-the-contents-of-the-group-repositories"></a>Anpassa innehållet i gruppdatabaserna

Om du vill anpassa innehållet i gruppdatabaserna så att det uppfyller gruppens specifika behov kan du göra det nu. Du kan ändra filerna, ändra katalogstrukturen eller lägga till filer som gruppen har utvecklat eller som är användbara för gruppen.

### <a name="make-changes-in-azure-repos"></a>Gör ändringar i Azure Repos

Så här anpassar du databasinnehållet:

1. På **sidan** Sammanfattning av **GroupCommon-projekt** väljer du **Repos**. 
   
1. Högst upp på sidan väljer du den databas som du vill anpassa.

1. Navigera till den mapp eller fil som du vill ändra i katalogstrukturen. 
   
   - Om du vill skapa nya mappar eller filer markerar du pilen bredvid **Ny**. 
     
     ![Skapa ny fil](./media/group-manager-tasks/new-file.png)
     
   - Om du vill ladda upp filer väljer du **Ladda upp filer.** 
     
     ![Överföra filer](./media/group-manager-tasks/upload-files.png)
     
   - Om du vill redigera befintliga filer navigerar du till filen och väljer sedan **Redigera**. 
     
     ![Redigera en fil](./media/group-manager-tasks/edit-file.png)
     
1. När du har lagt till eller redigerat filer väljer du **Commit**.
   
   ![Genomför ändringar](./media/group-manager-tasks/commit.png)

### <a name="make-changes-using-your-local-machine-or-dsvm"></a>Göra ändringar med din lokala dator eller DSVM

Om du vill göra ändringar med din lokala dator eller DSVM och driva ändringarna upp till gruppdatabaserna, se till att du har förutsättningar för att arbeta med Git och DSVMs:

- En Azure-prenumeration om du vill skapa en DSVM.
- Git installerad på din maskin. Om du använder en DSVM är Git förinstallerad. Annars kan du se [bilagan plattformar och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows eller Linux DSVM i Azure. Mer information och instruktioner finns i [dokumentationen för virtuell dator för datavetenskap](/azure/machine-learning/data-science-virtual-machine/).
- För en Windows DSVM, [Git Autentiseringshanteraren (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på din dator. Bläddra ned till avsnittet Hämta och **installera** i *README.md* och välj det **senaste installationsprogrammet**. Hämta *.exe-installationsprogrammet* från installationssidan och kör det. 
- För en Linux DSVM, en SSH offentlig nyckel som konfigureras på din DSVM och läggas till i Azure DevOps. Mer information och instruktioner finns i avsnittet **Skapa SSH-nyckel** i [bilagan Plattformar och verktyg](platforms-and-tools.md#appendix). 

Kopiera eller *klona* först databasen till den lokala datorn. 
   
1. På **sidan** Sammanfattning av **GroupCommon-projekt** väljer du **Repos**och väljer den databas som du vill klona högst upp på sidan.
   
1. På reposidan väljer du **Klona** längst upp till höger.
   
1. I dialogrutan **Klona databas** väljer du **HTTPS** för en HTTP-anslutning eller **SSH** för en SSH-anslutning och kopierar klon-URL:en under **Kommandoraden** till Urklipp.
   
   ![Klona repo](./media/group-manager-tasks/clone.png)
   
1. Skapa följande kataloger på den lokala datorn:
   
   - För Windows: **C:\GitRepos\GroupCommon**
   - För Linux, **$/GitRepos/GroupCommon** på din hemkatalog 
   
1. Ändra till katalogen som du skapade.
   
1. I Git Bash kör du kommandot`git clone <clone URL>.`
   
   Något av följande kommandon klonar till exempel **GroupUtilities-databasen** till *groupcommon-katalogen* på den lokala datorn. 
   
   **HTTPS-anslutning:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupUtilities
   ```
   
   **SSH-anslutning:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/GroupCommon/GroupUtilities
   ```

När du har gjort de ändringar du vill ha i den lokala klonen av databasen kan du skicka ändringarna till gemensamma databaser för delade grupper. 

Kör följande Git Bash-kommandon från din lokala **GroupProjectTemplate** eller **GroupUtilities-katalog.**

```bash
git add .
git commit -m "push from local"
git push
```

> [!NOTE]
> Om det är första gången du åtar dig till en Git-databas kan du behöva `git commit` konfigurera globala parametrar *user.name* och *user.email* innan du kör kommandot. Kör följande två kommandon:
> 
> `git config --global user.name <your name>`
> 
> `git config --global user.email <your email address>`
> 
> Om du förbinder dig till flera Git-databaser använder du samma namn och e-postadress för dem alla. Det är praktiskt att använda samma namn och e-postadress när du skapar Power BI-instrumentpaneler för att spåra dina Git-aktiviteter i flera databaser.

## <a name="add-group-members-and-configure-permissions"></a>Lägga till gruppmedlemmar och konfigurera behörigheter

Så här lägger du till medlemmar i gruppen:

1. I Azure DevOps väljer du **Project-inställningar** från vänster navigering på startsidan för **GroupCommon-projektet.** 
   
1. Välj **Teams**på sidan **Teams** i navigeringen **Projektinställningar** och välj sedan **GroupCommon Team**på sidan Teams . 
   
   ![Konfigurera team](./media/group-manager-tasks/teams.png)
   
1. Välj **Lägg till**på sidan **Gruppprofil.**
   
   ![Lägg till i GroupCommon Team](./media/group-manager-tasks/add-to-team.png)
   
1. I dialogrutan **Lägg till användare och grupper** söker du efter och väljer medlemmar som ska läggas till i gruppen och väljer sedan Spara **ändringar**. 
   
   ![Lägga till användare och grupper](./media/group-manager-tasks/add-users.png)
   

Så här konfigurerar du behörigheter för medlemmar:

1. Välj **Behörigheter**i navigeringen **i Project Settings** left . 
   
1. På sidan **Behörigheter** markerar du den grupp som du vill lägga till medlemmar i. 
   
1. På sidan för den gruppen väljer du **Medlemmar**och väljer sedan **Lägg till**. 
   
1. I **popup-fönstret Bjud in medlemmar** söker du efter och väljer medlemmar som ska läggas till i gruppen och väljer sedan **Spara**. 
   
   ![Bevilja behörigheter till medlemmar](./media/group-manager-tasks/grant-permissions.png)

## <a name="next-steps"></a>Nästa steg

Här är länkar till detaljerade beskrivningar av andra roller och uppgifter i teamdatavetenskapsprocessen:

- [Team lead-uppgifter för ett data science-team](team-lead-tasks.md)
- [Projektledaraktiviteter för ett datavetenskapslag](project-lead-tasks.md)
- [Projekt enskilda deltagaruppgifter för ett data science-team](project-ic-tasks.md)
