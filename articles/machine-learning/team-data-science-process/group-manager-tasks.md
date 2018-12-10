---
title: Team Data Science Process manager gruppaktiviteter
description: En översikt över aktiviteter för gruppchef i ett team för datavetenskapsprojekt.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: d44b13bb087f171c3f0474cde73167481221274f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136590"
---
# <a name="tasks-for-a-group-manager-on-a-data-science-team-project"></a>Uppgifter för gruppchef i ett team för datavetenskapsprojekt

Det här avsnittet beskrivs de uppgifter som gruppchef förväntas slutföra för hist / hennes data science-organisation. Målet är att upprätta samarbetsfunktioner gruppmiljö som standardiserar på den [Team Data Science Process](overview.md) (TDSP). En översikt över rollerna som personal och förknippade aktiviteter som hanteras av en datavetenskapliga team standardisera om den här processen finns i [roller för Team Data Science Process och uppgifter](roles-tasks.md).

Den **Gruppansvarig** projektledaren för hela data science-enhet i ett företag. En data science-enhet kan ha flera team som arbetar med flera dataforskningsprojekt i olika företag branscher. Gruppchef kan delegera sina uppgifter till en surrogate, men de uppgifter som är associerade med rollen är desamma. Det finns sex viktigaste uppgifterna som du ser i följande diagram:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP grupp-miljö med Azure DevOps-tjänsterna i instruktionerna som följer. Vi anger hur du utför dessa uppgifter med Azure DevOps Services eftersom det är hur vi implementerar TDSP på Microsoft. Om du använder en annan kod som är värd för plattformen för din grupp, ändra inte de uppgifter som måste utföras av hanteraren för gruppen Allmänt. Men sättet att utföra dessa uppgifter kommer att vara olika.

1. Konfigurera **Azure DevOps-tjänsterna** för gruppen.
2. Skapa en **projekt** på Azure DevOps-tjänsterna (för Azure DevOps-tjänsterna användare)
3. Skapa den **GroupProjectTemplate** lagringsplats
4. Skapa den **GroupUtilities** lagringsplats
5. Startvärdesklassen den **GroupProjectTemplate** och **GroupUtilities** lagringsplatser för Azure DevOps-tjänster med innehåll från TDSP-databaser.
6. Konfigurera den **säkerhetskontroller** för gruppmedlemmar att komma åt databaser GroupProjectTemplate och GroupUtilities.

Var och en av de föregående stegen beskrivs i detalj. Men först måste vi bekanta dig med förkortningarna och diskutera förutsättningar för att arbeta med databaser.

### <a name="abbreviations-for-repositories-and-directories"></a>Förkortningar för databaser och kataloger

Den här självstudien använder förkortade namnen för databaser och kataloger. Dessa definitioner gör det lättare att följa åtgärderna mellan databaser och kataloger. Den här notationen används i följande avsnitt:

- **G1**: projekt-mallagret utvecklas och hanteras av TDSP team med Microsoft.
- **G2**: verktyg databasen utvecklas och hanteras av TDSP team med Microsoft.
- **R1**: The GroupProjectTemplate databasen på Git du ställer in på din server för Azure DevOps-gruppen.
- **R2**: The GroupUtilities databasen på Git du ställer in på din server för Azure DevOps-gruppen.
- **LG1** och **LG2**: lokala kataloger på datorn att du klonar G1 och G2, respektive.
- **LR1** och **LR2**: lokala kataloger på datorn att du klonar R1 och R2 till respektive.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Förutsättningar för kloning av databaser och checka in och ut koden
 
- Git måste installeras på din dator. Om du använder en virtuell dator på datavetenskap (DSVM) Git har installerats i förväg och du är redo att börja. Annars läser den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git Credential Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. README.md-filen, rulla ned till den **ladda ned och installera** och klicka på *senaste installationsprogrammet*. Det här steget kommer du till den senaste sidan för installationsprogrammet. Ladda ned installationsprogrammet .exe här och kör den. 
- Om du använder **Linux DSVM**, skapa en offentlig SSH-nyckel för din DSVM och lägga till den i din grupp Azure DevOps-tjänsterna. Mer information om SSH finns i den **offentlig skapa SSH-nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-azure-devops-services"></a>1. Skapa konto på Azure DevOps-tjänsterna

Azure DevOps-tjänsterna är värd för följande databaser:

- **Gruppera vanliga lagringsplatser**: allmänna databaser som kan vidtas flera team inom en grupp med flera dataforskningsprojekt. Till exempel den *GroupProjectTemplate* och *GroupUtilities* databaser.
- **team databaser**: lagringsplatser för specifika team i en grupp. Dessa databaser är specifika för ett team och kan vara antagen av flera projekt som körs av teamet, men inte allmänna nog för att vara praktiskt att flera team i en data science-grupp. 
- **projektet databaser**: databaser som finns tillgängliga för särskilda projekt. Sådana databaser kanske inte är allmän vara användbart att flera projekt som utförs av ett team och flera team i en data science-grupp.


### <a name="setting-up-the-azure-devops-services-sign-into-your-microsoft-account"></a>Ställa in den Azure DevOps tjänster logga in på ditt Microsoft-konto
    
Gå till [Visual Studio online](https://www.visualstudio.com/), klickar du på **logga in** i övre högra hörnet och logga in på ditt Microsoft-konto. 
    
![1](./media/group-manager-tasks/login.PNG)

Om du inte har ett Microsoft-konto, klickar du på **registrera dig nu** att skapa ett Microsoft-konto och logga sedan in med det här kontot. 

Om din organisation har en Visual Studio/MSDN-prenumeration, klickar du på gröna **logga in med ditt arbets- eller skolkonto** box och logga in med autentiseringsuppgifterna som associeras med den här prenumerationen. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
När du har loggat in klickar du på **Skapa nytt konto** i det övre högra hörnet som visas i följande bild:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Fyll i information för Azure DevOps-tjänster som du vill skapa i den **skapa ditt konto** guiden med följande värden: 

- **Serveradress**: Ersätt *mysamplegroup* med dina egna *servernamn*. URL: en för din server ska vara: *https://\<servername\>. visualstudio.com*. 
- **Hantera med hjälp av kod:** Välj  **_Git_**.
- **Projektnamn:** RETUR *GroupCommon*. 
- **Organisera arbete med hjälp av:** Välj *Agile*.
- **Vara värd för dina projekt i:** väljer en geografisk plats. I det här exemplet väljer vi *södra centrala USA*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Om du ser följande popup-fönstret när du klickar på **Skapa nytt konto**, måste du klicka på **ändringsinformation** att visa alla fält som uppdelat.

![5](./media/group-manager-tasks/create-account-2.png)


Klicka på **Fortsätt**. 

## <a name="2-groupcommon-project"></a>2. GroupCommon projekt

Den **GroupCommon** sidan (*https://\<servername\>.visualstudio.com/GroupCommon*) öppnas när din Azure DevOps-tjänsterna har skapats.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Skapa databasen GroupUtilities (R2)

Att skapa den **GroupUtilities** (R2) databasen under Azure DevOps-tjänsterna:

- Öppna den **skapar en ny lagringsplats** guiden klickar du på **ny lagringsplats** på den **versionskontroll** fliken i ditt projekt. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Välj *Git* som den **typ**, och ange *GroupUtilities* som den **namn**, och klicka sedan på **skapa**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Nu bör du se två Git-lagringsplatser **GroupProjectTemplate** och **GroupUtilities** i den vänstra kolumnen i den **versionskontroll** sidan: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Skapa GroupProjectTemplate (R1) databasen

Installationen av databaser för servern för Azure DevOps-gruppen består av två saker:

- Byt namn på standard **GroupCommon** databasen***GroupProjectTemplate***.
- Skapa den **GroupUtilities** lagringsplatsen för Azure DevOps-tjänsterna under projektet **GroupCommon**. 

Anvisningar om hur den första aktiviteten finns i det här avsnittet när du Anmärkning på namnkonventioner eller våra databaser och kataloger. Anvisningarna för den andra aktiviteten finns i följande avsnitt innehåller steg 4.

### <a name="rename-the-default-groupcommon-repository"></a>Byt namn på GroupCommon standarddatabas

Att byta namn på standard **GroupCommon** lagringsplatsen som *GroupProjectTemplate* (kallas **R1** i den här självstudien):
    
- Klicka på **samarbeta på kod** på den **GroupCommon** projektsida. Detta tar dig till standardsidan för Git-lagringsplats för projektet **GroupCommon**. Git-lagringsplatsen är för närvarande är tom. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Klicka på **GroupCommon** i det övre vänstra hörnet (markerat med en röd ruta i följande bild) på sidan för Git-lagringsplats i **GroupCommon** och välj **hantera databaser**(markerat med en grön ruta i följande bild). Den här proceduren öppnas den **KONTROLLPANELEN**. 
- Välj den **versionskontroll** fliken i ditt projekt. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Klicka på den **...**  till höger om den **GroupCommon** databasen på den vänstra panelen och välj **Byt namn på databasen**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- I den **Byt namn på databasen GroupCommon** guiden POP, ange *GroupProjectTemplate* i den **lagringsplatsnamn** rutan och klicka sedan på **Byt namn på** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-azure-devops-services"></a>5. Dirigera R1 & R2 databaser på Azure DevOps-tjänster

I den här fasen av proceduren du initiera den *GroupProjectTemplate* (R1) och *GroupUtilities* (R2)-databaser som du skapade i föregående avsnitt. Dessa databaser har angetts med den ***ProjectTemplate*** (**G1**) och ***verktyg*** (**G2**) databaser som hanteras av Microsoft för Team Data Science Process. När den här seeding är slutförd:

- R1 databasen kommer att ha samma uppsättning kataloger och mallar som gör G1
- din R2 lagringsplats kommer att innehålla en uppsättning data science utilities har utvecklats av Microsoft.

Seeding proceduren använder katalogerna på din lokala DSVM som mellanliggande mellanlagring platser. Här följer de åtgärder som vidtas i det här avsnittet:

- G1 & G2 - klonas till -> LG1 & LG2
- R1 & R2 – klonas till -> LR1 & LR2
- LG1 & LG2 - filer som kopieras till -> LR1 & LR2
- (Valfritt) anpassning av LR1 & LR2
- LR1 & LR2 - innehållet Lägg till -> R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klona G1 & G2 lagringsplatser för din lokala DSVM

I det här steget ska klona du lagringsplatsen för Team Data Science Process (TDSP) ProjectTemplate (G1) och verktyg (G2) från github-lagringsplatser TDSP till mappar i din lokala DSVM som LG1 och LG2:

- Skapa en katalog som fungerar som rotkatalog som värd för din kloner databaser. 
    -  Skapa en katalog i Windows-DSVM *C:\GitRepos\TDSPCommon*. 
    -  Skapa en katalog i Linux-DSVM *GitRepos\TDSPCommon* i din arbetskatalog. 

- Kör följande kommandon från den *GitRepos\TDSPCommon* directory.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnått: 
    - G1 - klonas till -> LG1
    - G2 - klonas till -> LG2
- När Kloningen har slutförts kan du ska kunna se två kataloger _ProjectTemplate_ och _verktyg_under **GitRepos\TDSPCommon** directory. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klona R1 & R2 lagringsplatser för din lokala DSVM

I det här steget ska du klona GroupProjectTemplate lagringsplatsen (R1) och GroupUtilities databasen (R2) på lokala kataloger (kallas LR1 och LR2, respektive) under **GitRepos\GroupCommon** på din DSVM.

- URL: er R1 och R2 databaser, gå till din **GroupCommon** startsidan på Azure DevOps-tjänsterna. Detta har vanligtvis URL: en *https://\<Your Azure DevOps-tjänstnamn\>.visualstudio.com/GroupCommon*. 
- Klicka på **kod**. 
- Välj den **GroupProjectTemplate** och **GroupUtilities** databaser. Kopiera och spara vart och ett av de URL: er (HTTPS för Windows; SSH för Linux) från den **URL-klonen** element i tur och ordning för användning i följande skript:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Ändra till den **GitRepos\GroupCommon** på din Windows eller Linux DSVM och kör en av följande uppsättningar kommandon för att klona R1 och R2 till katalogen.
        
Här är de Windows- och Linux-skript:

    # Windows DSVM

    git clone <the HTTPS URL of the GroupProjectTemplate repository>
    git clone <the HTTPS URL of the GroupUtilities repository>

![16](./media/group-manager-tasks/clone-two-empty-group-reo-windows-2.PNG)

    # Linux DSVM

    git clone <the SSH URL of the GroupProjectTemplate repository>
    git clone <the SSH URL of the GroupUtilities repository>

![17](./media/group-manager-tasks/clone-two-empty-group-reo-linux-2.PNG)

>[AZURE.NOTE] Förvänta dig att ta emot varningsmeddelanden att LR1 och LR2 är tom.    

- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnått: 
    - R1 - klonas till -> LR1
    - R2 - klonas till -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Dirigera dina GroupProjectTemplate (LR1) och GroupUtilities (LR2)

I den lokala datorn Kopiera innehållet i ProjectTemplate och hjälpmedel kataloger (förutom metadata i .git kataloger) under GitRepos\TDSPCommon till katalogerna GroupProjectTemplate och GroupUtilities under **GitRepos\ GroupCommon**. Här följer två uppgifterna har slutförts i det här steget:

- Kopiera filerna i GitRepos\TDSPCommon\ProjectTemplate (**LG1**) till GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Kopiera filerna i GitRepos\TDSPCommon\Utilities (**LG2** till GitRepos\GroupCommon\Utilities (**LR2**). 

Kör följande skript i PowerShell-konsolen (Windows) eller Shell-skript (Linux) för att uppnå dessa två aktiviteter. Du uppmanas att ange de fullständiga sökvägarna till LG1, LR1, LG2 och LR2. Sökvägar som du anger verifieras. Om du anger en katalog som inte finns uppmanas du att ange den igen. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Nu kan du se att filer i kataloger LG1 och LG1 (utom filer i katalogen .git) har kopierats till LR1 och LR2, respektive.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Du ser nu att filerna i två mappar (utom filer i katalogen .git) kopieras till GroupProjectTemplate och GroupUtilities respektive.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnått:
    - LG1 - filer som kopieras till -> LR1
    - LG2 - filer som kopieras till -> LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Möjlighet att anpassa innehållet i LR1 & LR2
    
Om du vill anpassa innehållet i LR1 och LR2 för att uppfylla de specifika behoven i din grupp, är detta steget i proceduren om så är lämpligt. Du kan ändra mallen dokument, ändra katalogstrukturen och lägga till befintliga verktyg som gruppen har utvecklat eller som är användbara för hela hanteringsgruppen. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Lägg till innehåll i LR1 & LR2 till R1 & R2 på en server

Nu kan behöva du lägga till innehållet i LR1 och LR2 databaser R1 och R2. Här är git bash-kommandon som du kan köra i Windows PowerShell- eller Linux. 

Kör följande kommandon från katalogen GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

-M-alternativet kan du ange ett meddelande för git-incheckning.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Du kan se att i din grupp Azure DevOps-tjänster, i databasen GroupProjectTemplate filerna synkroniseras omedelbart.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Slutligen ändra till den **GitRepos\GroupCommon\GroupUtilities** och kör samma uppsättning git bash-kommandon:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Om det här är första gången du skickar till en Git-lagringsplats, måste du konfigurera globala parametrar *user.name* och *user.email* innan du kör den `git commit` kommando. Kör följande två kommandon:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Om du checkar in till flera Git-lagringsplatser, Använd samma namn och e-postadress när du skickar dem. Med samma namn och e-postadress bevisar praktiskt vid ett senare tillfälle när du skapar Power BI-instrumentpaneler för att spåra dina Git-aktiviteter i flera databaser.


- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnått:
    - LR1 - innehållet Lägg till -> R1
    - LR2 - innehållet Lägg till R2 ->

## <a name="6-add-group-members-to-the-group-server"></a>6. Lägg till medlemmar i gruppen på grupp-servern

På startsidan för din grupp Azure DevOps Services, klickar du på den **kugghjulsikonen** bredvid ditt användarnamn i det övre högra hörnet, väljer den **Security** fliken. Du kan lägga till medlemmar i gruppen med olika behörigheter.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Nästa steg

Här finns länkar till mer detaljerade beskrivningar av de roller och uppgifter som definieras av Team Data Science Process:

- [Gruppansvarig uppgifter för team data science](group-manager-tasks.md)
- [Lead gruppaktiviteter för team data science](team-lead-tasks.md)
- [Lead projektaktiviteter för team data science](project-lead-tasks.md)
- [Projektet enskilda deltagare för team data science](project-ic-tasks.md)