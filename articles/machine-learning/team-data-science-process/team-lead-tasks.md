---
title: Uppgifter för gruppledarledningen i teamdatascience-teamet
description: En detaljerad genomgång av uppgifterna för en gruppledning i ett teamdatavetenskapliga processteam
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d099d7c233c3f4b5e65bfdb7d4b875a0e4098499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864289"
---
# <a name="tasks-for-the-team-lead-on-a-team-data-science-process-team"></a>Uppgifter för teamledningen i ett team för datavetenskapsprocess

I den här artikeln beskrivs de uppgifter som en *gruppledare* slutför för sitt datavetenskapsteam. Teamledningens mål är att skapa en samarbetsgruppsmiljö som standardiserar [på Team Data Science Process](overview.md) (TDSP). TDSP är utformad för att förbättra samarbete och teaminlärning. 

TDSP är en smidig, iterativ datavetenskapsmetod för att effektivt leverera prediktiva analyslösningar och intelligenta applikationer. Processen destillerar bästa praxis och strukturer från Microsoft och branschen.  Målet är ett framgångsrikt genomförande av datavetenskapliga initiativ och till fullo förverkliga fördelarna med sina analysprogram. En översikt över personalroller och tillhörande uppgifter för ett data science-team som standardiserar på TDSP finns i [Roller och uppgifter för teamdatavetenskapsprocess](roles-tasks.md).

En gruppledare leder ett team bestående av flera datavetare inom datavetenskapsenheten i ett företag. Beroende på datascience-enhetens storlek och struktur kan [gruppchefen](group-manager-tasks.md) och gruppledningen vara samma person, eller så kan de delegera sina uppgifter till ersättningar. Men uppgifterna i sig förändras inte. 

I följande diagram visas arbetsflödet för de uppgifter som gruppledningen slutför för att konfigurera en gruppmiljö:

![Arbetsflöde för gruppleduppgift](./media/team-lead-tasks/team-leads-1-creating-teams.png)

1. Skapa ett **teamprojekt** i gruppens organisation i Azure DevOps. 
  
1. Byt namn på standardlagsarkivet till **TeamUtilities**.
  
1. Skapa en ny **TeamTemplate-lagringsplats** i teamprojektet. 
  
1. Importera innehållet i gruppens **GroupUtilities-** och **GroupProjectTemplate-databaser** till **TeamUtilities-** och **TeamTemplate-databaserna.** 
  
1. Konfigurera **säkerhetskontroll** genom att lägga till gruppmedlemmar och konfigurera deras behörigheter.
  
1. Om det behövs skapar du teamdata och analysresurser:
   - Lägg till teamspecifika verktyg i **TeamUtilities-databasen.** 
   - Skapa **Azure-fillagring** för att lagra datatillgångar som kan vara användbara för hela teamet. 
   - Montera Azure-fillagringen till team leadets **virtuella datavetenskapdator** (DSVM) och lägg till datatillgångar i den.

Följande självstudiekurs går igenom stegen i detalj.

> [!NOTE] 
> Den här artikeln använder Azure DevOps och en DSVM för att konfigurera en TDSP-teammiljö, eftersom det är så här du implementerar TDSP på Microsoft. Om ditt team använder andra kodhosting- eller utvecklingsplattformar är teamleduppgifterna desamma, men sättet att slutföra dem kan vara annorlunda.

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att följande resurser och behörigheter har konfigurerats av [gruppchefen:](group-manager-tasks.md)

- Azure **DevOps-organisationen** för din dataenhet
- **GroupProjectTemplate-** och **GroupUtilities-databaser,** befolkade med innehållet i Microsoft TDSP-teamets **ProjectTemplate-** och **Utilities-databaser**
- Behörigheter för ditt organisationskonto för att skapa projekt och databaser för ditt team

För att kunna klona databaser och ändra deras innehåll på din lokala dator eller DSVM, eller konfigurera Azure-fillagring och montera det på din DSVM, behöver du följande:

- En Azure-prenumeration.
- Git installerad på din maskin. Om du använder en DSVM är Git förinstallerad. Annars kan du se [bilagan plattformar och verktyg](platforms-and-tools.md#appendix).
- Om du vill använda en DSVM skapas och konfigureras Windows eller Linux DSVM i Azure. Mer information och instruktioner finns i [dokumentationen för virtuell dator för datavetenskap](/azure/machine-learning/data-science-virtual-machine/).
- För en Windows DSVM, [Git Autentiseringshanteraren (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerad på din dator. Bläddra ned till avsnittet Hämta och **installera** i *README.md* och välj det **senaste installationsprogrammet**. Hämta *.exe-installationsprogrammet* från installationssidan och kör det. 
- För en Linux DSVM, en SSH offentlig nyckel som konfigureras på din DSVM och läggas till i Azure DevOps. Mer information och instruktioner finns i avsnittet **Skapa SSH-nyckel** i [bilagan Plattformar och verktyg](platforms-and-tools.md#appendix). 

## <a name="create-a-team-project-and-repositories"></a>Skapa ett gruppprojekt och databaser

I det här avsnittet skapar du följande resurser i gruppens Azure DevOps-organisation:

- **MyTeam-projektet** i Azure DevOps
- **TeamTemplate-lagringsplatsen**
- **Databasen TeamUtilities**

De namn som anges för databaserna och katalogerna i den här självstudien förutsätter att du vill skapa ett separat projekt för ditt eget team inom din större datavetenskapsorganisation. Hela gruppen kan dock välja att arbeta under ett enda projekt som skapats av gruppchefen eller organisationsadministratören. Sedan skapar alla data science-team databaser under det här enskilda projektet. Det här scenariot kan vara giltigt för:
- En liten datavetenskapsgrupp som inte har flera datascience-team. 
- En större datavetenskapsgrupp med flera datavetenskapliga team som ändå vill optimera samarbete mellan teamen med aktiviteter som sprintplanering på gruppnivå. 

Om teamen väljer att ha sina teamspecifika databaser under ett enda gruppprojekt bör gruppledarna skapa databaserna med namn som * \<TeamName>Mall* och * \<TeamName>Utilities*. Till exempel: *TeamATemplate* och *TeamAUtilities*. 

I vilket fall som helst måste teamleadsna låta sina gruppmedlemmar veta vilken mall och verktygsdatabaser som ska konfigureras och klonas. Projektleads bör följa [projektledaraktiviteterna för ett datavetenskapsteam för](project-lead-tasks.md) att skapa projektdatabaser, oavsett om de är under separata projekt eller ett enskilt projekt. 

### <a name="create-the-myteam-project"></a>Skapa MyTeam-projektet

Så här skapar du ett separat projekt för ditt team:

1. I webbläsaren går du till gruppens startsida för Azure DevOps-organisationen på URL *\//\<https: servernamn>/\<organisationsnamn>* och väljer Nytt **projekt**. 
   
   ![Välj Nytt projekt](./media/team-lead-tasks/team-leads-2-create-new-team.png)
   
1. I dialogrutan **Skapa projekt** anger du gruppnamnet, till exempel *MyTeam*, under **Projektnamn**och väljer sedan **Avancerat**. 
   
1. Under **Versionskontroll**väljer du **Git**och under **Arbetsartikelprocess**väljer du **Agile**. Välj sedan **Skapa**. 
   
   ![Skapa projekt](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)
   
Sidan **Sammanfattning av** teamen öppnas med sid-URL *https:\//\<servernamn>/\<\<organisationsnamn>/ gruppnamnet>*.

### <a name="rename-the-myteam-default-repository-to-teamutilities"></a>Byta namn på MyTeams standarddatabas till TeamUtilities

1. På sidan **Sammanfattning** **av MyTeam-projekt** **under**Vilken tjänst vill du **börja med?** 
   
   ![Välj Repos](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)
   
1. På sidan Repo för **MyTeam** väljer du **Databasen MyTeam** högst upp på sidan och väljer sedan **Hantera databaser i** listrutan. 
   
   ![Välj Hantera databaser](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)
1. På sidan **Projektinställningar** markerar du **...** bredvid **MyTeam-databasen** och väljer sedan **Byt namn på databasen**. 
   
   ![Välj Byt namn på databas](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)
   
1. Ange *TeamUtilities*i **popup-fönstret Byt namn på MyTeam-databasen** och välj sedan **Byt namn**. 

### <a name="create-the-teamtemplate-repository"></a>Skapa Databasen TeamTemplate

1. Välj **Ny databas** på sidan **Projektinställningar.** 
   
   ![Välj Ny databas](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)
   
   Du kan också välja **Repos** från den vänstra navigeringen på sidan **Sammanfattning** av **My Team-projekt,** välja en databas högst upp på sidan och välj sedan Ny **databas** i listrutan.
   
1. Kontrollera att **Git** är markerat under **Text**i dialogrutan **Skapa en ny databas** . Ange *TeamTemplate* under **Databasnamn**och välj sedan **Skapa**.
   
   ![Skapa databas](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)
   
1. Bekräfta att du kan se de två databaserna **TeamUtilities** och **TeamTemplate** på sidan projektinställningar. 
   
   ![Två teamdatabaser](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)

### <a name="import-the-contents-of-the-group-common-repositories"></a>Importera innehållet i gruppens gemensamma databaser

Så här fyller du i gruppdatabaserna med innehållet i gruppens gemensamma databaser som har konfigurerats av gruppchefen:

1. Välj **Repos** i den vänstra navigeringen på startsidan för **MyTeam-projektet.** Om du får ett meddelande om att **MyTeam-mallen** inte hittas väljer du länken i **Annars navigerar du till standarddatabasen TeamTemplate.** 
   
   Standarddatabasen **För TeamTemplate** öppnas. 
   
1. På sidan **TeamTemplate är tom** väljer du **Importera**. 
   
   ![Välj Importera](./media/team-lead-tasks/import-repo.png)
   
1. I dialogrutan **Importera en Git-databas** väljer du **Git** som **källtyp**och anger URL:en för gruppens gemensamma malldatabas under **Klona URL**. URL:en är *\//\<https: servernamn\<>/ organisationsnamn>/_git/\<databasnamn>*. Till exempel: *\/https: /dev.azure.com/DataScienceUnit/GroupCommon/_git/GroupProjectTemplate*. 
   
1. Välj **Importera**. Innehållet i gruppmalldatabasen importeras till gruppmalldatabasen. 
   
   ![Gemensam malldatabas för importgrupp](./media/team-lead-tasks/import-repo-2.png)
   
1. Högst upp på projektets **Repos-sida** kan du lista ned och välja **databasen TeamUtilities.**
   
1. Upprepa importprocessen för att importera innehållet i databasen med gemensamma verktyg i gruppen, till exempel *GroupUtilities,* till databasen **TeamUtilities.** 
   
Var och en av dina två teamdatabaser innehåller nu filerna från motsvarande grupps gemensamma databas. 

### <a name="customize-the-contents-of-the-team-repositories"></a>Anpassa innehållet i teamdatabaserna

Om du vill anpassa innehållet i teamdatabaserna så att de uppfyller teamets specifika behov kan du göra det nu. Du kan ändra filer, ändra katalogstrukturen eller lägga till filer och mappar.

Så här ändrar, överför eller skapar du filer eller mappar direkt i Azure DevOps:

1. På **sidan** Sammanfattning **av MyTeam-projekt** väljer du **Repos**. 
   
1. Högst upp på sidan väljer du den databas som du vill anpassa.

1. Navigera till den mapp eller fil som du vill ändra i katalogstrukturen. 
   
   - Om du vill skapa nya mappar eller filer markerar du pilen bredvid **Ny**. 
     
     ![Skapa ny fil](./media/team-lead-tasks/new-file.png)
     
   - Om du vill ladda upp filer väljer du **Ladda upp filer.** 
     
     ![Överföra filer](./media/team-lead-tasks/upload-files.png)
     
   - Om du vill redigera befintliga filer navigerar du till filen och väljer sedan **Redigera**. 
     
     ![Redigera en fil](./media/team-lead-tasks/edit-file.png)
     
1. När du har lagt till eller redigerat filer väljer du **Commit**.
   
   ![Genomför ändringar](./media/team-lead-tasks/commit.png)

Om du vill arbeta med databaser på din lokala dator eller DSVM kopierar eller *klonar* du först databaserna till den lokala datorn och genomför och vidarebefordrar sedan ändringarna till de delade teamdatabaserna. 

Så här klonar du databaser:

1. På **sidan** Sammanfattning av **MyTeam-projekt** väljer du **Repos**och väljer den databas som du vill klona högst upp på sidan.
   
1. På reposidan väljer du **Klona** längst upp till höger.
   
1. Välj **HTTPS** för en HTTP-anslutning eller SSH för en **SSH-anslutning** under **Kommandoraden**och kopiera klon-URL:en till Urklipp. **Clone repository**
   
   ![Kopiera klon-URL](./media/team-lead-tasks/clone.png)
   
1. Skapa följande kataloger på den lokala datorn:
   
   - För Windows: **C:\GitRepos\MyTeam**
   - För Linux, **$home/GitRepos/MyTeam** 
   
1. Ändra till katalogen som du skapade.
   
1. I Git Bash kör `git clone <clone URL>`du \<kommandot , där klon-URL> är den URL som du kopierade från dialogrutan **Klon.**
   
   Använd till exempel något av följande kommandon för att klona **TeamUtilities-databasen** till *MyTeam-katalogen* på den lokala datorn. 
   
   **HTTPS-anslutning:**
   
   ```bash
   git clone https://DataScienceUnit@dev.azure.com/DataScienceUnit/MyTeam/_git/TeamUtilities
   ```
   
   **SSH-anslutning:**
   
   ```bash
   git clone git@ssh.dev.azure.com:v3/DataScienceUnit/MyTeam/TeamUtilities
   ```

När du har gjort de ändringar du vill ha i den lokala klonen av databasen, genomför och skickar du ändringarna till de delade teamdatabaserna. 

Kör följande Git Bash-kommandon från din lokala **GitRepos\MyTeam\TeamTemplate** eller **GitRepos\MyTeam\TeamUtilities-katalogen.**

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

## <a name="add-team-members-and-configure-permissions"></a>Lägga till gruppmedlemmar och konfigurera behörigheter

Så här lägger du till medlemmar i teamet:

1. I Azure DevOps väljer du **Project-inställningar** från den vänstra navigeringen på startsidan för **MyTeam-projektet.** 
   
1. Välj **Teams**på sidan **Teams** på sidan Teams **i**navigeringen **Projektinställningar** . 
   
   ![Konfigurera team](./media/team-lead-tasks/teams.png)
   
1. Välj **Lägg till**på sidan **Gruppprofil.**
   
   ![Lägg till i MyTeam-teamet](./media/team-lead-tasks/add-to-team.png)
   
1. I dialogrutan **Lägg till användare och grupper** söker du efter och väljer medlemmar som ska läggas till i gruppen och väljer sedan Spara **ändringar**. 
   
   ![Lägga till användare och grupper](./media/team-lead-tasks/add-users.png)
   

Så här konfigurerar du behörigheter för gruppmedlemmar:

1. Välj **Behörigheter**i navigeringen **i Project Settings** left . 
   
1. På sidan **Behörigheter** markerar du den grupp som du vill lägga till medlemmar i. 
   
1. På sidan för den gruppen väljer du **Medlemmar**och väljer sedan **Lägg till**. 
   
1. I **popup-fönstret Bjud in medlemmar** söker du efter och väljer medlemmar som ska läggas till i gruppen och väljer sedan **Spara**. 
   
   ![Bevilja behörigheter till medlemmar](./media/team-lead-tasks/grant-permissions.png)

## <a name="create-team-data-and-analytics-resources"></a>Skapa teamdata och analysresurser

Det här steget är valfritt, men att dela data- och analysresurser med hela teamet har prestanda och kostnadsfördelar. Gruppmedlemmar kan utföra sina projekt på delade resurser, spara på budgetar och samarbeta mer effektivt. Du kan skapa Azure-fillagring och montera den på din DSVM för att dela med gruppmedlemmar. 

Information om hur du delar andra resurser med ditt team, till exempel Azure HDInsight Spark-kluster, finns [i Plattformar och verktyg](platforms-and-tools.md). Det avsnittet ger vägledning från ett datavetenskapligt perspektiv på att välja resurser som är lämpliga för dina behov, och länkar till produktsidor och andra relevanta och användbara självstudier.

>[!NOTE]
> Undvik att överföra data över datacenter, vilket kan vara långsamt och dyrt, se till att din Azure-resursgrupp, lagringskonto och DSVM alla finns i samma Azure-region. 

### <a name="create-azure-file-storage"></a>Skapa Azure-fillagring

1. Kör följande skript för att skapa Azure-fillagring för datatillgångar som är användbara för hela teamet. Skriptet frågar dig efter din Azure-prenumerationsinformation, så har det klart att gå in. 

   - På en Windows-dator kör du skriptet från PowerShell-kommandotolken:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
     .\CreateFileShare.ps1
     ```
     
   - På en Linux-maskin kör du skriptet från Linux-skalet:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
     bash CreateFileShare.sh
     ```
   
1. Logga in på ditt Microsoft Azure-konto när du uppmanas att göra det och välj den prenumeration du vill använda.
   
1. Välj det lagringskonto som ska användas eller skapa ett nytt under din valda prenumeration. Du kan använda gemener, siffror och bindestreck för Azure-fillagringsnamnet.
   
1. Om du vill underlätta montering och delning av lagringen trycker du på Retur eller anger *Y* för att spara Azure-fillagringsinformationen i en textfil i den aktuella katalogen. Du kan checka in den här textfilen till **din TeamTemplate-databas,** helst under **Dokument\DataDictionaries**, så att alla projekt i teamet kan komma åt den. Du behöver också filinformation för att montera din Azure-fillagring till din Azure DSVM i nästa avsnitt. 
   
### <a name="mount-azure-file-storage-on-your-local-machine-or-dsvm"></a>Montera Azure-fillagring på din lokala dator eller DSVM

1. Om du vill montera din Azure-fillagring på din lokala dator eller DSVM använder du följande skript.
   
   - På en Windows-dator kör du skriptet från PowerShell-kommandotolken:
     
     ```powershell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
     .\AttachFileShare.ps1
     ```
     
   - På en Linux-maskin kör du skriptet från Linux-skalet:
     
     ```shell
     wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
     bash AttachFileShare.sh
     ```
   
1. Tryck på Retur eller ange *Y* för att fortsätta om du har sparat en Azure-fillagringsinformationsfil i föregående steg. Ange den fullständiga sökvägen och namnet på filen som du skapade. 
   
   Om du inte har en Azure-fillagringsinformationsfil anger du *n*och följer instruktionerna för att ange din prenumeration, Azure-lagringskonto och Azure-fillagringsinformation.
   
1. Ange namnet på en lokal enhet eller TDSP-enhet som filresursen ska monteras på. På skärmen visas en lista över befintliga enhetsnamn. Ange ett enhetsnamn som inte redan finns.
   
1. Kontrollera att den nya enheten och lagringen har monterats på din dator.

## <a name="next-steps"></a>Nästa steg

Här är länkar till detaljerade beskrivningar av andra roller och uppgifter som definieras av Team Data Science Process:

- [Gruppchefsuppgifter för ett data science-team](group-manager-tasks.md)
- [Projektledaraktiviteter för ett datavetenskapslag](project-lead-tasks.md)
- [Projekt enskilda deltagaruppgifter för ett data science-team](project-ic-tasks.md)
