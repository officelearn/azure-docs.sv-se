---
title: Uppgifter för teamet leda i Team Data Science Process-teamet
description: En översikt över de uppgifter som ett team på ett team för datavetenskapsprojekt förväntas att slutföra för sina datavetenskapliga team.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 45be3d7f865c7b72ae62efbf99dbbb4594b1846f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55465273"
---
# <a name="tasks-for-the-team-lead-in-the-team-data-science-process-team"></a>Uppgifter för teamet leda i Team Data Science Process-teamet

Det här avsnittet beskrivs de uppgifter som är en gruppledare väntat att slutföra sina data science-teamet. Målet är att upprätta samarbetsteam miljö som standardiserar på den [Team Data Science Process](overview.md) (TDSP). TDSP är en flexibel, iterativ datavetenskapsmetod att leverera prediktiva Analyslösningar och intelligenta program effektivt. Det är utformat för att förbättra samarbete och team learning. Processen är en destillation av metodtipsen och strukturerna från både Microsoft och från branschen, behövs för lyckade implementeringen av data science-åtgärder för att hjälpa företag att fullständigt nytta av fördelarna med sina analytics-program. En översikt över rollerna som personal och förknippade aktiviteter som hanteras av en datavetenskapliga team standardisera om den här processen finns i [roller för Team Data Science Process och uppgifter](roles-tasks.md).

En **gruppledare** hanterar ett team för data science-enheten på ett företag. Ett team består av flera dataexperter. För data science-enhet med endast ett litet antal dataexperter, den **Gruppansvarig** och **gruppledare** kan vara samma person eller de kan delegera uppgiften till en surrogate. Men själva aktiviteterna ändras inte. Arbetsflödet för aktiviteter som ska slutföras av gruppledare att ställa in den här miljön illustreras i följande bild:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Uppgifter i block om 1 och 2 i bilden krävs om du använder Azure DevOps som värdplattform för kod och du vill ha en separat Azure DevOps-projekt för ditt team. När uppgifterna har slutförts, kan alla databaser i ditt team skapas under det här projektet. 

När du har flera förutsättningar uppgifter som anges i följande avsnitt är uppfyllda av hanteraren för gruppen, det är de fem främsta uppgifterna (vissa valfria) som du har slutfört i den här självstudien. Dessa uppgifter motsvara den huvudsakliga numrerade i detta avsnitt:

1. Skapa en **projekt** på gruppens Azure DevOps-tjänsterna i gruppen och två team databaserna i projektet:
    - **ProjectTemplate lagringsplats** 
    - **TeamUtilities lagringsplats**
2. Dirigera teamet **ProjectTemplate** databasen från den **GroupProjectTemplate** databasen som har ställts in av hanteraren för gruppen. 
3. Skapa team och analys resurser:
    - Lägg till team-specifika verktyg till den **TeamUtilities** lagringsplats. 
    - (Valfritt) Skapa en **Azure-fillagring** som används för att lagra datatillgångar som kan vara användbara för hela gruppen. 
4. (Valfritt) Montera Azure file storage för att den **Data Science Virtual Machine** (DSVM) för teamet leda och Lägg till datatillgångar på den.
5. Konfigurera den **säkerhetskontroll** genom att lägga till gruppmedlemmar och konfigurera sina privilegier.

>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö med Azure DevOps i följande anvisningar. Vi anger du hur du utför dessa uppgifter med Azure DevOps eftersom det är hur vi implementerar TDSP på Microsoft. Om du använder en annan kod som är värd för plattformen för din grupp, ändra inte de uppgifter som måste slutföras av gruppledare Allmänt. Men sättet att utföra dessa uppgifter kommer att vara olika.

## <a name="repositories-and-directories"></a>Databaser och kataloger

Det här avsnittet använder förkortade namnen för databaser och kataloger. Dessa namn gör det lättare att följa åtgärderna mellan databaser och kataloger. Den här notationen (**R** för Git-lagringsplatser och **D** för lokala kataloger på din DSVM) används i följande avsnitt:

- **R1**: Den **GroupProjectTemplate** databasen på Git som din gruppansvarig ställts in på din server för Azure DevOps-gruppen.
- **R3**: Teamet **ProjectTemplate** databasen på Git som du har konfigurerat.
- **R4**: Den **TeamUtilities** databasen på Git som du har konfigurerat.
- **D1**: Den lokala katalogen klonas från R1 och kopieras till D3.
- **D3**: Den lokala katalogen klonas från R3, anpassa och kopieras till R3.
- **D4**: Den lokala katalogen klonas från R4, anpassa och kopieras till R4.

Namnen på har angetts för databaser och kataloger i den här självstudien har angetts på antagandet att målet är att upprätta en separat projekt för ditt team i en större grupp för data science. Men det finns andra alternativ som är öppna för dig som gruppledare:

- Hela gruppen kan du skapa ett enda projekt. Sedan är alla projekt från alla data science-team under den här enda projekt. Du kan ange en git-administratör att följa dessa instruktioner för att skapa ett projekt för att uppnå detta. Det här scenariot kan vara giltig, till exempel för:
    -  en små data science-grupp som inte har flera data science-team 
    -  en större data science grupp med flera data datavetenskapsteam som ändå vill optimera samarbete mellan team med aktiviteter, till exempel gruppnivå sprint planera. 
- Teamen kan välja att ha team-specifika projektmallar eller team-specifika verktyg under det enda projektet för hela gruppen. I detta fall bör gruppledare skapa projekt mall databaser och/eller team verktyg databaser under samma projekt. Namnge dessa databaser *< TeamName\>ProjectTemplate* och *< TeamName\>verktyg*, till exempel *TeamJohnProjectTemplate*och *TeamJohnUtilities*. 

I båda fallen måste gruppledare så att deras teammedlemmar vet som mall och hjälpmedel lagringsplatser att införa när de konfigurerar och klona lagringsplatser för projektet och verktyg. Projektet leads bör följa den [projektbeskrivning uppgifter för team data science](project-lead-tasks.md) att skapa projektet lagringsplatser enligt separata projekt eller under ett enda projekt. 


## <a name="0-prerequisites"></a>0. Förutsättningar

Kraven är uppfyllda genom att slutföra de uppgifter som tilldelats till din gruppchef som beskrivs i [Gruppansvarig uppgifter för team data science](group-manager-tasks.md). Följande krav måste uppfylla innan du börjar lead gruppaktiviteter för att sammanfatta här: 

- Din **gruppen Azure DevOps-tjänsterna** (eller gruppkonto på andra kod som är värd för plattform) har ställts in av hanteraren för gruppen.
- Din **GroupProjectTemplate databasen** (R1) har ställts in på ditt gruppkonto med din gruppchef på koden som är värd för plattform som du tänker använda.
- Du har **behörighet** på din gruppkonto för att skapa databaser för ditt team.
- Git måste installeras på din dator. Om du använder en virtuell dator på datavetenskap (DSVM) Git har installerats i förväg och du är redo att börja. Annars läser den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. README.md-filen, rulla ned till den **ladda ned och installera** och klicka på *senaste installationsprogrammet*. Detta tar dig till den senaste sidan för installationsprogrammet. Ladda ned installationsprogrammet .exe här och kör den. 
- Om du använder **Linux DSVM**, skapa en offentlig SSH-nyckel för din DSVM och lägga till den i din grupp Azure DevOps-tjänsterna. Mer information om SSH finns i den **offentlig skapa SSH-nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-project-and-repositories"></a>1. Skapa ett projekt och databaser

Slutför det här steget om du använder Azure DevOps som koden som är värd för plattformen för versionshantering och samarbete. Det här avsnittet har du skapa tre artefakter i Azure DevOps-tjänsterna i din grupp:

- **MyTeam** projekt i Azure DevOps
- **MyProjectTemplate** lagringsplats (**R3**) på Git
- **MyTeamUtilities** lagringsplats (**R4**) på Git

### <a name="create-the-myteam-project"></a>Skapa MyTeam-projekt

- Gå till startsidan för din grupp Azure DevOps-tjänsterna på URL: en `https://<Azure DevOps Services Name\>.visualstudio.com`. 
- Klicka på **New** att skapa ett projekt. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Ett fönster för skapa projektet ber dig att ange projektnamnet (**MyTeam** i det här exemplet). Kontrollera att du väljer **Agile** som den **processmall** och **Git** som den **versionskontroll**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Klicka på **skapa projekt**. Ditt projekt **MyTeam** skapas i mindre än 1 minut. 

- När projektet **MyTeam** är skapat, klickar du på **navigera till projektet** knapp, så att de dirigeras till startsidan i ditt projekt. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Om du ser en **Grattis!** popup-fönstret klickar du på den **lägger du till kod** (knapp med röd ram). Annars klickar du på **kod** (i gul ruta). Detta leder till sidan Git-lagringsplatsen i ditt projekt. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Skapa MyProjectTemplate databasen (R3) på Git

- På sidan för Git-lagringsplatsen i ditt projekt klickar du på nedpilen bredvid namnet på lagringsplatsen **MyTeam**, och välj **hantera databaser...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- På den **versionskontroll** fliken i Kontrollpanelen på ditt projekt, klickar du på **MyTeam**och välj sedan **Byt namn på databasen...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Ange ett nytt namn i databasen i den **Byt namn på databasen MyTeam** fönster. I det här exemplet *MyTeamProjectTemplate*. Du kan välja något som liknar *< ditt Lagnamn\>ProjectTemplate*. Klicka på **Byt namn på** att fortsätta.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Skapa MyTeamUtilities databasen (R4) på Git

- Att skapa en ny lagringsplats *< ditt Lagnamn\>verktyg* under ditt projekt klickar du på **nya databasen...**  på den **versionskontroll** fliken i ditt projekt på Kontrollpanelen.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- I den **skapar en ny lagringsplats** fönstret som visas, ange ett namn för den här databasen. I det här exemplet vi ger den namnet *MyTeamUtilities*, vilket är **R4** i vår notation. Välj något som liknar *< ditt Lagnamn\>verktyg*. Kontrollera att du väljer **Git** för **typ**. Klicka sedan på **skapa** att fortsätta.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Kontrollera att du ser de två nya Git lagringsplatser som har skapats under ditt projekt **MyTeam**. I det här exemplet: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-projecttemplate-and-teamutilities-repositories"></a>2. Dirigera dina ProjectTemplate och TeamUtilities lagringsplatser

Seeding proceduren använder katalogerna på din lokala DSVM som mellanliggande mellanlagring platser. Om du vill anpassa din **ProjectTemplate** och **TeamUtilities** databaser att uppfylla vissa specifika team behöver, gör du det i det näst sista steget i följande procedur. Här är en sammanfattning av de steg som används till att dirigera innehållet i den **MyTeamProjectTemplate** och **MyTeamUtilities** lagringsplatser för team data science. De enskilda stegen motsvarar underavsnitt i seeding procedur:

- Klonade grupp lagringsplats till lokal katalog: team R1 - klonas för att lokala D1 ->
- Klona team-databaser till lokala kataloger: team R3 & R4 - klonas för att lokal D3 och D4 ->
- Kopiera gruppinnehåll för mallen i projektet till mappen lokala team:  D1 - innehållet kopieras till D3 ->
- (Valfritt) anpassning av lokal D3 och D4
- Skicka lokala katalogens innehåll till teamet databaser: D3 & D4 - contents add to -> team R3 & R4


### <a name="initialize-the-team-repositories"></a>Initiera team-databaser

I det här steget kan du initiera dina projekt mallagret från malldatabasen grupp projekt:

- **MyTeamProjectTemplate** lagringsplats (**R3**) från din **GroupProjectTemplate** (**R1**) databasen


### <a name="clone-group-repositories-into-local-directories"></a>Klona grupp databaser till lokala kataloger

Att starta den här proceduren:

- Skapa kataloger på den lokala datorn:
    - För **Windows**: **C:\GitRepos\GroupCommon** och **C:\GitRepos\MyTeam**
    - För **Linux**: **GitRepos\GroupCommon** och **GitRepos\MyTeam** på din hemkatalog 
- Ändra till katalogen **GitRepos\GroupCommon**.
- Kör följande kommando, efter behov, på operativsystemet på den lokala datorn.

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Dessa kommandon klona din **GroupProjectTemplate** (R1) databasen på din grupp Azure DevOps-tjänsterna till lokal katalog i **GitRepos\GroupCommon** på den lokala datorn. Efter kloning av directory **GroupProjectTemplate** (D1) skapas i katalogen **GitRepos\GroupCommon**. Här kan vi antar att din gruppansvarig skapat ett projekt **GroupCommon**, och **GroupProjectTemplate** databasen är under det här projektet. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Klona team-databaser till lokala kataloger

Dessa kommandon klona din **MyTeamProjectTemplate** (R3) och **MyTeamUtilities** (R4) databaser under projektet **MyTeam** på gruppen Azure DevOps-tjänster till den **MyTeamProjectTemplate** (D3) och **MyTeamUtilities** (D4) kataloger i **GitRepos\MyTeam** på den lokala datorn. 

- Ändra till katalogen **GitRepos\MyTeam**
- Kör följande kommandon och efter behov, på operativsystemet på den lokala datorn. 

**Windows**

    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your Azure DevOps Services name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your Azure DevOps Services name>@<Your Azure DevOps Services name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Efter kloning av två kataloger **MyTeamProjectTemplate** (D3) och **MyTeamUtilities** (D4) skapas i katalogen **GitRepos\MyTeam**. Vi har antas här du gav ditt projekt mall och hjälpmedel databaser **MyTeamProjectTemplate** och **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-project-template-directory"></a>Kopiera grupp projekt mallinnehållet till den lokala projektkatalogen för mallen

Kopiera innehållet i lokalt **GroupProjectTemplate** (D1) mappen till lokalt **MyTeamProjectTemplate** (D3) kör något av följande kommandoskript: 

#### <a name="from-the-powershell-command-line-for-windows"></a>Från kommandoraden för Windows PowerShell       

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 2

    
![16](./media/team-lead-tasks/team-leads-16-local_copy_team_lead_new.png)

#### <a name="from-the-linux-shell-for-the-linux-dsvm"></a>Från Linux-gränssnittet för den **Linux DSVM**
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 2
    
![17](./media/team-lead-tasks/team-leads-17-local-copy-team-lead-linux-new.png)

Skripten exkludera innehållet i katalogen .git. Skripten uppmanas att ange den **slutföra sökvägar** källkatalogen D1 och målkatalogen D3.
        

### <a name="customize-your-project-template-or-team-utilities-optional"></a>Anpassa ditt projekt mall eller team verktyg (valfritt)

Anpassa din **MyTeamProjectTemplate** (D3) och **MyTeamUtilities** (D4), om det behövs i det här skedet av installationen. 

- Om du vill anpassa innehållet i D3 att uppfylla de specifika behoven i ditt team kan du ändra mallen dokument eller ändra katalogstrukturen.

- Om ditt team har utvecklat vissa verktyg som du vill dela med hela arbetsgruppen kan kopiera och klistra in verktygen i katalogen D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Skicka innehåll för lokal katalog som team-lagringsplatser

För att lägga till innehållet i (du kan också anpassade) lokala kataloger D3 och D4 team lagringsplatser R3 och R4, kör följande git bash-kommandon från en Windows PowerShell-konsolen eller från Linux-gränssnittet. Kör kommandon från den **GitRepos\MyTeam\MyTeamProjectTemplate** directory.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Filerna i MyTeamProjectTemplate lagringsplatsen för din grupp Azure DevOps-tjänsterna synkroniseras nästan direkt när det här skriptet körs.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Kör nu samma uppsättning fyra git-kommandon från den **GitRepos\MyTeam\MyTeamUtilities** directory. 

> [AZURE.NOTE]Om det här är första gången du skickar till en Git-lagringsplats, måste du konfigurera globala parametrar *user.name* och *user.email* innan du kör den `git commit` kommando. Kör följande två kommandon:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Om du checkar in till flera Git-lagringsplatser, Använd samma namn och e-postadress när du skickar dem. Med samma namn och e-postadress bevisar praktiskt vid ett senare tillfälle när du skapar Power BI-instrumentpaneler för att spåra dina Git-aktiviteter i flera databaser.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Skapa team och analys resurser (valfritt)

Delning av data och analyser resurser med hela arbetsgruppen har fördelar för prestanda och kostnad: teammedlemmar kan köra sina projekt på delade resurser, spara på budgetar och samarbeta mer effektivt. Det här avsnittet innehåller anvisningar om hur du skapar Azure-fillagring. Nästa avsnitt innehåller anvisningar om hur du montera Azure file storage till den lokala datorn. Läs mer om hur du delar andra resurser, till exempel Azure Data Science Virtual Machines, Azure HDInsight Spark-kluster i [plattformar och verktyg](platforms-and-tools.md). Det här avsnittet ger dig vägledning från ett data science perspektiv för att välja resurser som passar dina behov och länkar till produktsidor och andra relevanta och användbara självstudiekurser som vi har publicerat.

>[AZURE.NOTE] Kontrollera att resursgruppen, lagringskontot och den virtuella Azure-datorn (t.ex. DSVM) är i samma Azure-Datacenter för att undvika att data som skickas mellan datacenter, vilket kan vara långsam och kostsamt. 

Kör följande skript för att skapa Azure file storage för ditt team. Azure file storage för ditt team kan användas för att lagra datatillgångar som är användbara för hela gruppen. Skripten uppmanar dig om ditt Azure-konto och prenumeration, så har de här autentiseringsuppgifterna som är redo att ange. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Skapa Azure-fillagring med PowerShell från Windows

Kör det här skriptet från PowerShell-kommandorad:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Logga in på Microsoft Azure-kontot när du uppmanas till detta:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Ange den prenumeration du vill använda:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Välj vilket lagringskonto som ska användas eller skapa ett nytt lösenord i din valda prenumeration:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Ange namnet på Azure file storage för att skapa. Endast lägre fall tecken, siffror och - godkänns:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

För att underlätta montera och dela den här lagringen när den har skapats, spara Azure file storage-information i en textfil och anteckna sökvägen till platsen. I synnerhet måste den här filen för att montera din Azure file storage till Azure-datorer i nästa avsnitt. 

Det är en bra idé att checka in den här filen till din ProjectTemplate lagringsplats. Vi rekommenderar att du placerar i katalogen **Docs\DataDictionaries**. Den här datatillgången kan därför användas av alla projekt i ditt team. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Skapa Azure-fillagring med Linux-skript

Kör det här skriptet från Linux-gränssnittet:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Logga in på Microsoft Azure-kontot följa anvisningarna i den här skärmen:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Välj den prenumeration som du vill använda:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Välj vilket lagringskonto som ska användas eller skapa ett nytt lösenord i din valda prenumeration:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Ange namnet på Azure med fillagring för att skapa, endast gemener, siffror och - godkänns:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

För att underlätta åtkomst till den här lagringen när den har skapats, spara Azure file storage-information i en textfil och anteckna sökvägen till platsen. I synnerhet måste den här filen för att montera din Azure file storage till Azure-datorer i nästa avsnitt.

Det är en bra idé att checka in den här filen till din ProjectTemplate lagringsplats. Vi rekommenderar att du placerar i katalogen **Docs\DataDictionaries**. Den här datatillgången kan därför användas av alla projekt i ditt team. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Montera Azure file storage (valfritt)

När Azure file storage har skapats, kan den monteras till den lokala datorn använder du någon av följande PowerShell- eller Linux-skript.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Montera Azure file storage med PowerShell från Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Du uppmanas att logga in först om du inte har loggat in. 

Klicka på **RETUR** eller **y** att fortsätta när du tillfrågas om du har ett Azure storage filinformation fil och sedan ange den ***slutföra sökvägen och namnet** av filen som du skapar i föregående steg. Information att montera en Azure-fillagring läses direkt från att fil- och du är redo att gå till nästa steg.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Om du inte har en fil med information för Azure file storage finns stegen för att mata in informationen från tangentbord i slutet av det här avsnittet.

Du uppmanas sedan att ange namnet på enheten som ska läggas till den virtuella datorn. En lista över den befintliga enhetsnamn skrivs ut på skärmen. Du bör ange ett enhetsnamn som inte redan finns i listan.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Bekräfta att en ny F-enhet har monterats på din dator.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Så här anger du Azure file storage information manuellt:** Om du inte har din Azure file storage information på en textfil, kan du följa instruktionerna på följande skärm att skriva in den nödvändiga prenumeration, lagringskontot och information om Azure storage:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Skriv namnet på ditt Azure-prenumeration, Välj lagringskontot där Azure file storage skapas, och skriver in namnet för Azure file storage:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Montera Azure file storage med en Linux-skript

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Du uppmanas att logga in först om du inte har loggat in. 

Klicka på **RETUR** eller **y** att fortsätta när du tillfrågas om du har ett Azure storage filinformation fil och sedan ange den ***slutföra sökvägen och namnet** av filen som du skapar i föregående steg. Information att montera en Azure-fillagring läses direkt från att fil- och du är redo att gå till nästa steg.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Du uppmanas sedan att ange namnet på enheten som ska läggas till den virtuella datorn. En lista över den befintliga enhetsnamn skrivs ut på skärmen. Du bör ange ett enhetsnamn som inte redan finns i listan.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Bekräfta att en ny F-enhet har monterats på din dator.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Så här anger du Azure file storage information manuellt:** Om du inte har din Azure file storage information på en textfil, kan du följa instruktionerna på följande skärm att skriva in den nödvändiga prenumeration, lagringskontot och information om Azure storage:

- Indata **n**.
- Välj indexet som namnet på prenumerationen där Azure file storage har skapats i föregående steg:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Välj lagringskontot under din prenumeration och typ i Azure storage filnamnet:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Ange namnet på enheten som ska läggas till din dator, vilket ska vara skiljer sig från alla befintliga:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Konfigurera principer för åtkomstkontroll av säkerhet 

På startsidan för din grupp Azure DevOps Services, klickar du på den **kugghjulsikonen** bredvid ditt användarnamn i det övre högra hörnet, väljer den **Security** fliken. Du kan lägga till medlemmar till ditt team med olika behörigheter.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Nästa steg

Här finns länkar till mer detaljerade beskrivningar av de roller och uppgifter som definieras av Team Data Science Process:

- [Gruppansvarig uppgifter för team data science](group-manager-tasks.md)
- [Lead gruppaktiviteter för team data science](team-lead-tasks.md)
- [Lead projektaktiviteter för team data science](project-lead-tasks.md)
- [Projektet enskilda deltagare för team data science](project-ic-tasks.md)
