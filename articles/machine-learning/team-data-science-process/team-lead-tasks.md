---
title: Gruppuppgifter datavetenskap processen Team leda - Azure | Microsoft Docs
description: En översikt över aktiviteter för en grupp leda på en datavetenskap team projekt.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 9d2043808cbd61d5e2a69cbe0f2a5a611e3afa31
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839766"
---
# <a name="team-lead-tasks"></a>Lead gruppuppgifter

Det här avsnittet beskrivs de uppgifter som är ett team förväntas utföra för sina datavetenskap team. Målet är att upprätta grupp användare miljö som standardiserar på den [Team datavetenskap Process](overview.md) (TDSP). TDSP är en flexibel och iterativ datavetenskap metoder att leverera förutsägelseanalyslösningar och intelligent program effektivt. Den är utformad för att förbättra samarbete och team learning. Processen är en destillation bästa praxis och strukturer från både Microsoft och från branschen, behövs för lyckad implementering av datavetenskap initiativ för att hjälpa företag att helt dra nytta av deras analytics-program. En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av ett team för datavetenskap standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md).

En **Team leda** hanterar ett team i vetenskap dataenheten på ett företag. En grupp består av flera datavetare. För vetenskap dataenheten med bara ett fåtal data forskare den **Gruppansvarig** och **Team leda** kan vara samma person eller de kan delegera uppgiften till ett surrogat. Men själva aktiviteterna ändras inte. Arbetsflöde för aktiviteter som ska slutföras av team leder till att konfigurera den här miljön illustreras i följande bild:

![1](./media/team-lead-tasks/team-leads-1-creating-teams.png)

>[AZURE.NOTE] Uppgifter i block 1 och 2 i bilden krävs om du använder Visual Studio Team Services VSTS () som den kod som värd för plattformen och du vill ha ett separat team projekt för gruppen. När dessa aktiviteter har slutförts, kan alla databaser i gruppen skapas under det här grupprojektet. 

När du har flera förutsättningar uppfylls uppgifter som anges i följande avsnitt av hanteraren för gruppen, det finns fem huvudsakliga uppgifter (vissa valfritt) som du har slutfört i den här självstudiekursen. Dessa uppgifter motsvarar den huvudsakliga numrerade i detta avsnitt:

1. Skapa en **grupprojekt** på gruppens VSTS server i gruppen och två team databaserna i projektet:
    - **ProjectTemplate databasen** 
    - **TeamUtilities databasen**
2. Startvärde för teamet **ProjectTemplate** databasen från den **GroupProjectTemplate** databas som har ställts in av din grupp manager. 
3. Skapa data och analyser gruppresurser:
    - Lägg till team-specifika verktyg till den **TeamUtilities** databasen. 
    - (Valfritt) Skapa en **Azure file storage** som används för att lagra datatillgångar som kan vara användbara för hela gruppen. 
4. (Valfritt) Montera Azure file storage till den **datavetenskap virtuella** (DSVM) för teamet leda och Lägg till datatillgångar på den.
5. Ställ in den **säkerhetskontroll** genom att lägga till teammedlemmar och konfigurera sina privilegier.

>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP teammiljö med VSTS i följande instruktioner. Anger vi hur du utför dessa uppgifter med VSTS eftersom det är hur vi implementera TDSP på Microsoft. Om du använder en annan kod som är värd för plattformen för din grupp, ändras inte de uppgifter som måste slutföras av team lead normalt. Men sättet att utföra dessa uppgifter ska vara olika.

## <a name="repositories-and-directories"></a>Databaser och kataloger

Det här avsnittet använder förkortade namn för databaser och kataloger. Dessa namn gör det lättare att följa åtgärder mellan databaser och kataloger. Notation (**R** för Git-databaser och **D** för lokala kataloger på dina DSVM) används i följande avsnitt:

- **R1**: den **GroupProjectTemplate** databasen på Git som din gruppansvarig som konfigurerats på servern VSTS grupp.
- **R3**: teamet **ProjectTemplate** databasen på Git som du konfigurerar.
- **R4**: den **TeamUtilities** databasen på Git som du konfigurerar.
- **D1**: den lokala katalogen klonad från R1 och kopieras till D3.
- **D3**: den lokala katalogen klonad från R3, anpassa och kopieras till R3.
- **D4**: den lokala katalogen klonad från R4, anpassa och kopieras till R4.

Namnen på har angetts för databaser och kataloger i den här självstudiekursen har angetts på antagandet att målet är att fastställa ett separat team projekt för gruppen i en större datavetenskap grupp. Men det finns andra alternativ som är öppna för dig som team lead:

- Hela gruppen kan du skapa en enda grupprojekt. Sedan är alla projekt från alla datavetenskap team under den här enda grupprojekt. Du kan ange en git-administratör att följa dessa instruktioner för att skapa en enda grupprojekt för att uppnå. Det här scenariot kan vara giltiga, till exempel:
    -  en liten vetenskap datagrupp som inte har flera datavetenskap team 
    -  en större datavetenskap grupp med flera datavetenskap team som ändå vill optimera mellan gruppsamarbete med aktiviteter, till exempel grupperingsnivå sprint planering. 
- Team kan välja specifika team projektmallar eller team-specifika verktyg under det enda grupprojektet för hela gruppen. I det här fallet bör team leads Skapa team projekt mallen databaser och/eller team verktyg databaser under samma grupprojektet. Namnge dessa databaser *< TeamName\>ProjectTemplate* och *< TeamName\>verktyg*, till exempel *TeamJohnProjectTemplate*och *TeamJohnUtilities*. 

I varje fall måste team leads du låta sina gruppmedlemmar vet vilken mall verktyg-databaser och fatta när de ställer in och klona databaser projektet och verktyg. Projektet leads bör följa den [projekt leda uppgifter för ett datavetenskap team](project-lead-tasks.md) att skapa projektet databaser om under separat grupprojekt eller under en enda grupprojekt. 


## <a name="0-prerequisites"></a>0. Förutsättningar

Krav uppfylls genom att genomföra aktiviteter som tilldelats av din gruppansvarig som beskrivs i [Gruppansvarig uppgifter för ett datavetenskap team](group-manager-tasks.md). Följande krav måste uppfylla innan du börjar team lead uppgifter för att sammanfatta här: 

- Din **grupp VSTS server** (eller gruppkonto på andra kod värd platform) har ställts in av din grupp manager.
- Din **GroupProjectTemplate databasen** (R1) har ställts in på ditt gruppkonto med din grupp manager på den kod som värd för plattform som du tänker använda.
- Du har **behörighet** på din gruppkonto för att skapa databaser för din grupp.
- Git måste vara installerad på datorn. Om du använder en virtuell dator på vetenskap (DSVM) Git redan har installerats och du är redo att börja. Annars går du till den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git autentiseringsuppgifter Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. I filen README.md rulla ned till den **ladda ned och installera** avsnittet och klicka på den *senaste installer*. Då kommer du till sidan senaste installer. Hämta .exe installationsprogrammet härifrån och köra den. 
- Om du använder **Linux DSVM**, skapar en offentlig SSH-nyckel på din DSVM och lägga till den i din grupp VSTS server. Mer information om SSH finns i **skapa SSH offentlig nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 
    
## <a name="1-create-a-team-project-and-repositories"></a>1. Skapa ett grupprojekt och databaser

Slutför det här steget om du använder VSTS som din kod som värd för plattform för versionshantering och samarbete. Det här avsnittet har du skapa tre felaktigheter i VSTS-server i din grupp:

- **MyTeam** projekt i VSTS
- **MyProjectTemplate** databasen (**R3**) på Git
- **MyTeamUtilities** databasen (**R4**) på Git

### <a name="create-the-myteam-project"></a>Skapa MyTeam-projekt

- Gå till din grupp VSTS server webbsida på URL: en `https://<VSTS Server Name\>.visualstudio.com`. 
- Klicka på **ny** att skapa ett team projekt. 

    ![2](./media/team-lead-tasks/team-leads-2-create-new-team.png)

- Skapa team projekt-fönstret ombedd att ange ett projektnamn (**MyTeam** i det här exemplet). Kontrollera att du väljer **Agile** som den **processmall** och **Git** som den **versionskontroll**. 

    ![3](./media/team-lead-tasks/team-leads-3-create-new-team-2.png)

- Klicka på **skapa projekt**. Team projekt **MyTeam** skapas i mindre än 1 minut. 

- Efter grupprojektet **MyTeam** har skapat, klickar du på **navigera till projektet** att dirigeras till startsidan för ditt team projekt. 

    ![4](./media/team-lead-tasks/team-leads-4-create-new-team-3.png)

- Om du ser en **Grattis!** popup-fönster, klicka på den **Lägg till kod** (knappen i röd ruta). Annars klickar du på **kod** (i gula rutan). Detta leder till sidan Git-lagringsplats i team projekt. 

    ![5](./media/team-lead-tasks/team-leads-5-team-project-home.png)

### <a name="create-the-myprojecttemplate-repository-r3-on-git"></a>Skapa MyProjectTemplate databasen (R3) på Git

- På sidan för Git-lagringsplatsen i team projekt klickar du på nedpilen bredvid databasnamn **MyTeam**, och välj **hantera databaser...** .

    ![6](./media/team-lead-tasks/team-leads-6-rename-team-project-repo.png)

- På den **versionskontroll** fliken i Kontrollpanelen på ditt team projekt, klickar du på **MyTeam**och välj **Byt namn på databasen...** . 

    ![7](./media/team-lead-tasks/team-leads-7-rename-team-project-repo-2.png)

- Ange ett nytt namn i databasen i den **Byt namn på databasen MyTeam** fönster. I det här exemplet *MyTeamProjectTemplate*. Du kan välja något som liknar *< namn på teamet\>ProjectTemplate*. Klicka på **Byt namn på** att fortsätta.

    ![8](./media/team-lead-tasks/team-leads-8-rename-team-project-repo-3.png)

### <a name="create-the-myteamutilities-repository-r4-on-git"></a>Skapa MyTeamUtilities databasen (R4) på Git

- Att skapa en ny lagringsplats *< namn på teamet\>verktyg* under ditt team projekt, klickar du på **nya databasen...**  på den **versionskontroll** fliken i ditt team projekt på Kontrollpanelen.  

    ![9](./media/team-lead-tasks/team-leads-9-create-team-utilities.png)

- I den **skapar en ny lagringsplats** fönster som visas, ange ett namn för den här databasen. I det här exemplet vi namnet som *MyTeamUtilities*, vilket är **R4** i vår notation. Välj något som liknar *< namn på teamet\>verktyg*. Kontrollera att du väljer **Git** för **typen**. Klicka på **skapa** att fortsätta.

    ![10](./media/team-lead-tasks/team-leads-10-create-team-utilities-2.png)

- Bekräfta att du ser två nya Git databaser skapas under din grupprojekt **MyTeam**. I det här exemplet: 

- **MyTeamProjectTemplate** (R3) 
- **MyTeamUtilities** (R4).

    ![11](./media/team-lead-tasks/team-leads-11-two-repo-in-team.png)


## <a name="2-seed-your-team-projecttemplate-and-teamutilities-repositories"></a>2. Startvärde för ditt team ProjectTemplate och TeamUtilities databaser

Seeding används kataloger på din lokala DSVM som mellanliggande fristående platser. Om du behöver anpassa din **ProjectTemplate** och **TeamUtilities** databaser att uppfylla vissa specifika team krävs, gör du det i det näst sista steget i följande procedur. Här följer en sammanfattning av de steg som används som startvärde för innehållet i den **MyTeamProjectTemplate** och **MyTeamUtilities** databaser för en grupp för vetenskapliga data. De enskilda stegen motsvarar underavsnitt i seeding procedur:

- Klona grupp databasen till lokal katalog: team R1 - klona om du vill -> lokala D1
- Klona team-databaser till lokala kataloger: team R3 & R4 - klona om du vill -> lokala D3 & D4
- Kopiera mallinnehållet grupp projektet till mappen lokala team: D1 - innehållet kopieras till D3 ->
- (Valfritt) anpassning av lokal D3 och D4
- Push-lokala katalogens innehåll till team databaser: D3 & D4 - innehållet Lägg till -> team R3 & R4


### <a name="initialize-the-team-repositories"></a>Initiera team-databaser

I det här steget kan du initiera team projekt mallen databasen från gruppen projekt mallen databasen:

- **MyTeamProjectTemplate** databasen (**R3**) från din **GroupProjectTemplate** (**R1**) databasen


### <a name="clone-group-repositories-into-local-directories"></a>Klona grupp databaser till lokala kataloger

Starta den här proceduren:

- Skapa kataloger på den lokala datorn:
    - För **Windows**: **C:\GitRepos\GroupCommon** och **C:\GitRepos\MyTeam**
    - För **Linux**: **GitRepos\GroupCommon** och **GitRepos\MyTeam** på arbetskatalogen 
- Ändra till katalogen **GitRepos\GroupCommon**.
- Kör följande kommando, efter behov, på operativsystemet på den lokala datorn.

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/GroupCommon/_git/GroupProjectTemplate
    

![12](./media/team-lead-tasks/team-leads-12-create-two-group-repos.png)

**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/GroupCommon/_git/GroupProjectTemplate
    
    
![13](./media/team-lead-tasks/team-leads-13-clone_two_group_repos_linux.png)

Dessa kommandon klona din **GroupProjectTemplate** (R1) databasen på din grupp VSTS server till lokal katalog i **GitRepos\GroupCommon** på den lokala datorn. Efter kloning directory **GroupProjectTemplate** (D1) har skapats i directory **GitRepos\GroupCommon**. Här kan vi antar att chefen grupp skapas en grupprojekt **GroupCommon**, och **GroupProjectTemplate** databasen är under det här grupprojektet. 


### <a name="clone-your-team-repositories-into-local-directories"></a>Klona team-databaser till lokala kataloger

Dessa kommandon klona din **MyTeamProjectTemplate** (R3) och **MyTeamUtilities** (R4) databaser under din grupprojekt **MyTeam** på gruppen VSTS servern för att  **MyTeamProjectTemplate** (D3) och **MyTeamUtilities** (D4) kataloger i **GitRepos\MyTeam** på den lokala datorn. 

- Ändra till katalogen **GitRepos\MyTeam**
- Kör följande kommandon efter behov, på operativsystemet på den lokala datorn. 

**Windows**

    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone https://<Your VSTS Server name>.visualstudio.com/<Your Team Name>/_git/MyTeamUtilities

![14](./media/team-lead-tasks/team-leads-14-clone_two_empty_team_repos.png)
        
**Linux**
    
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamProjectTemplate
    git clone ssh://<Your VSTS Server name>@<Your VSTS Server name>.visualstudio.com:22/<Your Team Name>/_git/MyTeamUtilities
    
![15](./media/team-lead-tasks/team-leads-15-clone_two_empty_team_repos_linux.png)

Efter kloning av två kataloger **MyTeamProjectTemplate** (D3) och **MyTeamUtilities** (D4) skapas i directory **GitRepos\MyTeam**. Vi har antas här du gav ditt team projekt databaser mall och verktyg **MyTeamProjectTemplate** och **MyTeamUtilities**. 

### <a name="copy-the-group-project-template-content-to-the-local-team-project-template-directory"></a>Kopiera grupp projekt mallens innehåll till lokala team mallen projektkatalogen

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
        

### <a name="customize-your-team-project-template-or-team-utilities-optional"></a>Anpassa ditt team projektmall eller team verktyg (valfritt)

Anpassa din **MyTeamProjectTemplate** (D3) och **MyTeamUtilities** (D4), om det behövs i det här skedet av installationen. 

- Om du vill anpassa innehållet i D3 att uppfylla de specifika behoven i din grupp kan du ändra mall-dokument eller ändra katalogstrukturen.

- Om din grupp har utvecklat vissa verktyg som du vill dela med hela arbetsgruppen kan kopiera och klistra in de här verktygen i katalogen D4. 


### <a name="push-local-directory-content-to-team-repositories"></a>Push-lokala katalogens innehåll till team databaser

Om du vill lägga till innehållet i de (eventuellt anpassade) lokala katalogerna D3 och D4 till team databaser R3 och R4, kör följande git bash kommandon från en Windows PowerShell-konsolen eller från Linux-gränssnittet. Kör kommandon från den **GitRepos\MyTeam\MyTeamProjectTemplate** directory.

    git status
    git add .
    git commit -m"push from DSVM"
    git push
    
![18](./media/team-lead-tasks/team-leads-18-push-to-group-server-2.png)

Filerna i databasen för din grupp VSTS server MyTeamProjectTemplate synkroniseras nästan omedelbart när skriptet körs.

![19](./media/team-lead-tasks/team-leads-19-push-to-group-server-showed-up.png)

Nu köra samma uppsättning fyra git-kommandon från den **GitRepos\MyTeam\MyTeamUtilities** directory. 

> [AZURE.NOTE]Om det här är första gången du genomför en Git-lagringsplatsen, måste du konfigurera globala parametrar *user.name* och *user.email* innan du kör den `git commit` kommando. Kör följande två kommandon:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
> Om du checkar in till flera Git-databaser, använda samma namn och e-postadress när du gör dem. Med samma namn och e-postadress bevisar praktiskt vid ett senare tillfälle när du skapar PowerBI instrumentpaneler för att spåra Git-aktiviteter i flera databaser.

![20](./media/team-lead-tasks/team-leads-20-git-config-name.png)


## <a name="3-create-team-data-and-analytics-resources-optional"></a>3. Skapa data och analyser gruppresurser (valfritt)

Dela data och analyser resurser med hela gruppen har fördelar prestanda och kostnad: gruppmedlemmar kan köra sina projekt på delade resurser, spara på budgetar och samarbeta mer effektivt. I det här avsnittet ger vi anvisningar om hur du skapar Azure file storage. I nästa avsnitt ge vi instruktioner för hur till montera Azure file storage till din lokala dator. För ytterligare information om att dela andra resurser, till exempel datavetenskap virtuella datorer i Azure, Azure HDInsight Spark-kluster, se [plattformar och verktyg](platforms-and-tools.md). Det här avsnittet ger vägledning ur datavetenskap på välja resurser som passar dina behov och länkar till produktsidor och andra relevanta och användbara självstudiekurser som vi har publicerat.

>[AZURE.NOTE] Kontrollera att resursgruppen, storage-konto och den virtuella Azure-datorn (t.ex. DSVM) är i samma Azure-datacentret för att undvika att data överföring mellan datacenter, vilket kan vara långsam och dyra. 

Kör följande skript för att skapa Azure fillagring för din grupp. Azure fillagring för ditt team kan användas för att lagra datatillgångar som är användbara för hela gruppen. Skripten uppmaning om ditt Azure-konto och prenumeration, så måste dessa kan du ange autentiseringsuppgifter. 

### <a name="create-azure-file-storage-with-powershell-from-windows"></a>Skapa Azure file storage med PowerShell i Windows

Kör skriptet från PowerShell-kommandorad:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.ps1" -outfile "CreateFileShare.ps1"
    .\CreateFileShare.ps1

![21](./media/team-lead-tasks/team-leads-21-create-fileshare-win.png)   

Logga in på Microsoft Azure-konto när du uppmanas till detta:

![22](./media/team-lead-tasks/team-leads-22-file-create-s1.png)

Välj den Azure-prenumeration du vill använda:

![23](./media/team-lead-tasks/team-leads-23-file-create-s2.png)

Välj vilka lagringskonto som ska använda eller skapa en ny under den valda prenumerationen:

![24](./media/team-lead-tasks/team-leads-24-file-create-s3.png)

Ange namnet på Azure file storage att skapa. Endast lägre fall tecken, siffror och - accepteras:

![25](./media/team-lead-tasks/team-leads-25-file-create-s4.png)

För att underlätta montera och dela den här lagring när den har skapats, spara Azure file storage-informationen i en textfil och anteckna sökvägen till platsen. I synnerhet måste den här filen för att montera din Azure fillagring till din Azure virtuella datorer i nästa avsnitt. 

Det är en bra idé att kontrollera i den här filen i ditt team ProjectTemplate databas. Vi rekommenderar för att placera i katalogen **Docs\DataDictionaries**. Därför kan tillgången data nås av alla projekt i ditt team. 

![26](./media/team-lead-tasks/team-leads-26-file-create-s5.png)


### <a name="create-azure-file-storage-with-a-linux-script"></a>Skapa Azure file storage med Linux-skript

Kör skriptet från Linux-gränssnittet:

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/CreateFileShare.sh"
    bash CreateFileShare.sh

Logga in på ditt Microsoft Azure-konto följa anvisningarna på skärmen:

![27](./media/team-lead-tasks/team-leads-27-file-create-linux-s1.png)

Välj den Azure-prenumeration som du vill använda:

![28](./media/team-lead-tasks/team-leads-28-file-create-linux-s2.png)

Välj vilka lagringskonto som ska använda eller skapa en ny under den valda prenumerationen:

![29](./media/team-lead-tasks/team-leads-29-file-create-linux-s3.png)

Ange namnet på Azure file storage för att skapa, endast gemener, siffror och - accepteras:

![30](./media/team-lead-tasks/team-leads-30-file-create-linux-s4.png)

För att underlätta åtkomst till den här lagring när den har skapats, spara Azure file storage-informationen i en textfil och anteckna sökvägen till platsen. I synnerhet måste den här filen för att montera din Azure fillagring till din Azure virtuella datorer i nästa avsnitt.

Det är en bra idé att kontrollera i den här filen i ditt team ProjectTemplate databas. Vi rekommenderar för att placera i katalogen **Docs\DataDictionaries**. Därför kan tillgången data nås av alla projekt i ditt team. 

![31](./media/team-lead-tasks/team-leads-31-file-create-linux-s5.png)


## <a name="4-mount-azure-file-storage-optional"></a>4. Montera Azure file storage (valfritt)

När Azure file storage har skapats kan den monteras på din lokala dator som använder du någon av följande PowerShell- eller Linux-skript.

### <a name="mount-azure-file-storage-with-powershell-from-windows"></a>Montera Azure fillagring med PowerShell i Windows

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.ps1" -outfile "AttachFileShare.ps1"
    .\AttachFileShare.ps1
    
Du uppmanas att logga in först om du inte har loggat in. 

Klicka på **RETUR** eller **y** fortsätta när du tillfrågas om du har en Azure file storage-informationen filen och sedan ange den ***slutföra sökvägen och namnet** av filen som du skapar i föregående steg. Montera en Azure fillagring läses information direkt från att fil- och du är redo att gå till nästa steg.

![32](./media/team-lead-tasks/team-leads-32-attach-s1.png)

> [AZURE.NOTE] Om du inte har en fil som innehåller Azure file storage-informationen finns steg för att mata in information från tangentbord i slutet av det här avsnittet.

Du uppmanas sedan att ange namnet på enheten som ska läggas till den virtuella datorn. En lista över befintliga enhetsnamn skrivs ut på skärmen. Du bör ange ett enhetsnamn som inte redan finns i listan.

![33](./media/team-lead-tasks/team-leads-33-attach-s2.png)

Bekräfta att en ny F-enhet har monterats till din dator.

![34](./media/team-lead-tasks/team-leads-34-attach-s3.png)

**Hur du manuellt ange Azure file storage-informationen:** om du inte har dina Azure file storage-informationen i en textfil, kan du följa instruktionerna på skärmen följande ange obligatoriskt prenumerations, storage-konto och Azure information om lagring:

![35](./media/team-lead-tasks/team-leads-35-attach-s4.png)

Ange i ditt Azure-prenumerationsnamn, Välj lagring konto där Azure file storage skapas, och ange i Azure storage filnamnet:

![36](./media/team-lead-tasks/team-leads-36-attach-s5.png)

### <a name="mount-azure-file-storage-with-a-linux-script"></a>Montera Azure fillagring med ett Linux-skript

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/AttachFileShare.sh"
    bash AttachFileShare.sh

![37](./media/team-lead-tasks/team-leads-37-attach-s1-linux.png)

Du uppmanas att logga in först om du inte har loggat in. 

Klicka på **RETUR** eller **y** fortsätta när du tillfrågas om du har en Azure file storage-informationen filen och sedan ange den ***slutföra sökvägen och namnet** av filen som du skapar i föregående steg. Montera en Azure fillagring läses information direkt från att fil- och du är redo att gå till nästa steg.

![38](./media/team-lead-tasks/team-leads-38-attach-s2-linux.png)

Du uppmanas sedan att ange namnet på enheten som ska läggas till den virtuella datorn. En lista över befintliga enhetsnamn skrivs ut på skärmen. Du bör ange ett enhetsnamn som inte redan finns i listan.

![39](./media/team-lead-tasks/team-leads-39-attach-s3-linux.png)

Bekräfta att en ny F-enhet har monterats till din dator.

![40](./media/team-lead-tasks/team-leads-40-attach-s4-linux.png)

**Hur du manuellt ange Azure file storage-informationen:** om du inte har dina Azure file storage-informationen i en textfil, kan du följa instruktionerna på skärmen följande ange obligatoriskt prenumerations, storage-konto och Azure information om lagring:

- Indata **n**.
- Välj index för prenumerationsnamn där Azure file storage skapades i föregående steg:

    ![41](./media/team-lead-tasks/team-leads-41-attach-s5-linux.png)

- Välj lagringskonto under din prenumeration och typ i Azure storage filnamnet:

    ![42](./media/team-lead-tasks/team-leads-42-attach-s6-linux.png)

- Ange namnet på enheten som ska läggas till din dator som ska vara avskild från alla befintliga:

    ![43](./media/team-lead-tasks/team-leads-43-attach-s7-linux.png)


## <a name="5-set-up-security-control-policy"></a>5. Konfigurera princip för åtkomstkontroll av säkerhet 

Gruppen VSTS serverns webbsida, klicka på den **kugghjulet ikonen** bredvid ditt användarnamn i det övre högra hörnet, Välj den **säkerhet** fliken. Du kan lägga till medlemmar i din grupp med olika behörigheter.

![44](./media/team-lead-tasks/team-leads-44-add-team-members.png)

## <a name="next-steps"></a>Nästa steg

Här är länkar till mer detaljerade beskrivningar av de roller och aktiviteter som definierats av Team av vetenskapliga data:

- [Gruppera Manager aktiviteter för en datavetenskap-teamet](group-manager-tasks.md)
- [Gruppuppgifter Lead för ett datavetenskap team](team-lead-tasks.md)
- [Projektet Lead uppgifter för ett datavetenskap team](project-lead-tasks.md)
- [Projektet enskilda deltagare för ett datavetenskap team](project-ic-tasks.md)