---
title: Team vetenskap processen grupp Datahanteraren uppgifter - Azure | Microsoft Docs
description: En översikt över aktiviteter för en grupp manager på en datavetenskap team projekt.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 9f9cdd52aff5445f64a4780d6e04590a06c21105
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2018
---
# <a name="group-manager-tasks"></a>Gruppen Manager-uppgifter

Det här avsnittet beskrivs de åtgärder som en grupp Manager förväntas slutföras för hist / hennes vetenskap organisation av data. Målet är att fastställa samarbetsfunktioner gruppmiljö som standardiserar på den [Team datavetenskap Process](overview.md) (TDSP). En beskrivning av rollerna personal och förknippade aktiviteter som hanteras av ett team för datavetenskap standardisera på den här processen finns [Team datavetenskap Process roller och aktiviteter](roles-tasks.md).

Den **Gruppansvarig** projektledaren för hela dataenheten vetenskap i ett företag. En dataenhet vetenskap kan ha flera team, som arbetar på flera datavetenskap projekt i olika företag lodräta annonser. En grupp Manager kan delegera sina uppgifter till ett surrogat, men de uppgifter som är associerade med rollen är desamma. Det finns sex huvuduppgifter som visas i följande diagram:

![0](./media/group-manager-tasks/tdsp-group-manager.png)


>[AZURE.NOTE] Vi beskriver de steg som krävs för att ställa in en TDSP gruppmiljö med VSTS i instruktionerna som följer. Anger vi hur du utför dessa uppgifter med VSTS eftersom det är hur vi implementera TDSP på Microsoft. Om du använder en annan kod som är värd för plattformen för din grupp, ändras inte de uppgifter som måste slutföras av hanteraren för gruppen normalt. Men sättet att utföra dessa uppgifter ska vara olika.

1. Ställ in **Visual Studio Team Services VSTS ()-server** för gruppen.
2. Skapa en **grupp grupprojekt** på Visual Studio Team Services-servern (för VSTS användare)
3. Skapa den **GroupProjectTemplate** databasen
4. Skapa den **GroupUtilities** databasen
5. Dirigering av **GroupProjectTemplate** och **GroupUtilities** databaser för VSTS server med innehåll från TDSP-databaser.
6. Ställ in den **säkerhetsåtgärder** för gruppmedlemmar får tillgång till databaser GroupProjectTemplate och GroupUtilities.

Var och en av de föregående stegen beskrivs i detalj. Men först måste vi bekanta dig med förkortningarna och beskrivs kraven för att arbeta med databaser.

### <a name="abbreviations-for-repositories-and-directories"></a>Förkortningar för databaser och kataloger

Den här kursen använder förkortade namn för databaser och kataloger. Dessa definitioner gör det lättare att följa åtgärder mellan databaser och kataloger. Notation används i följande avsnitt:

- **G1**: projektet mallen databasen utvecklas och hanteras av Microsoft TDSP grupp.
- **G2**: verktyg databasen utvecklas och hanteras av Microsoft TDSP grupp.
- **R1**: den GroupProjectTemplate databasen på Git du konfigurerar på servern VSTS grupp.
- **R2**: den GroupUtilities databasen på Git du konfigurerar på servern VSTS grupp.
- **LG1** och **LG2**: lokala kataloger på datorn och att du klonar G1 och G2 till respektive.
- **LR1** och **LR2**: lokala kataloger på datorn och att du klonar R1 och R2 till respektive.

### <a name="pre-requisites-for-cloning-repositories-and-checking-code-in-and-out"></a>Förutsättningar för kloning databaser och checka in och ut koden
 
- Git måste vara installerad på datorn. Om du använder en virtuell dator på vetenskap (DSVM) Git redan har installerats och du är redo att börja. Annars går du till den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix).  
- Om du använder en **Windows DSVM**, måste du ha [Git autentiseringsuppgifter Manager (GCM)](https://github.com/Microsoft/Git-Credential-Manager-for-Windows) installerat på datorn. I filen README.md rulla ned till den **ladda ned och installera** avsnittet och klicka på den *senaste installer*. Det här steget tar dig till den senaste sidan för installationsprogrammet. Hämta .exe installationsprogrammet härifrån och köra den. 
- Om du använder **Linux DSVM**, skapar en offentlig SSH-nyckel på din DSVM och lägga till den i din grupp VSTS server. Mer information om SSH finns i **skapa SSH offentlig nyckel** i avsnittet den [plattformar och verktyg bilaga](platforms-and-tools.md#appendix). 


## <a name="1-create-account-on-vsts-server"></a>1. Skapa kontot på VSTS server

VSTS server är värd för följande databaser:

- **Gruppera vanliga databaser**: allmänna databaser som kan antas av flera team i en grupp för flera datavetenskap projekt. Till exempel den *GroupProjectTemplate* och *GroupUtilities* databaser.
- **teamet databaser**: databaser för specifika grupper i en grupp. Dessa databaser är specifika för en grupps behov och kan vara antagna av flera projekt som körs av detta team, men inte allmänna nog för att vara användbara för flera team i en grupp för vetenskapliga data. 
- **projektet databaser**: databaser som finns tillgängliga för särskilda projekt. Sådana databaser får inte vara Allmänt kan vara lämpligt att flera projekt som utförs av ett team och flera team i en grupp för vetenskapliga data.


### <a name="setting-up-the-vsts-server-sign-into-your-microsoft-account"></a>Konfigurera VSTS Server logga in på ditt Microsoft-konto
    
Gå till [Visual Studio online](https://www.visualstudio.com/), klickar du på **logga in** i övre högra hörnet och logga in på ditt Microsoft-konto. 
    
![1](./media/group-manager-tasks/login.PNG)

Om du inte har ett Microsoft-konto, klickar du på **registrera nu** att skapa ett Microsoft-konto och logga sedan in med det här kontot. 

Om din organisation har en Visual Studio/MSDN-prenumeration, klickar du på gröna **logga in med ditt arbets- eller skolkonto** rutan och logga in med autentiseringsuppgifterna som associeras med den här prenumerationen. 
        
![2](./media/group-manager-tasks/signin.PNG)


        
När du loggar in, klickar du på **Skapa nytt konto** i det övre högra hörnet som visas i följande bild:
        
![3](./media/group-manager-tasks/create-account-1.PNG)
        
Fyll i informationen för VSTS-server som du vill skapa i den **skapa kontot** guiden med följande värden: 

- **Serveradress**: ersätta *mysamplegroup* med dina egna *servernamn*. URL-Adressen till servern kommer att vara: *https://\<servername\>. visualstudio.com*. 
- **Hantera med hjälp av kod:** Välj  **_Git_**.
- **Projektnamn:** RETUR *GroupCommon*. 
- **Ordna arbete med hjälp av:** Välj *Agile*.
- **Värd för ditt projekt i:** välja en geoplats. I det här exemplet väljer vi *södra centrala USA*. 
        
![4](./media/group-manager-tasks/fill-in-account-information.png)

>[AZURE.NOTE] Om du ser följande popup-fönstret när du klickar på **Skapa nytt konto**, måste du klicka på **ändra uppgifter** att visa alla fält som är specificerade.

![5](./media/group-manager-tasks/create-account-2.png)


Klicka på **fortsätta**. 

## <a name="2-groupcommon-team-project"></a>2. GroupCommon grupprojekt

Den **GroupCommon** sida (*https://\<servername\>.visualstudio.com/GroupCommon*) öppnas när servern VSTS har skapats.
                            
![6](./media/group-manager-tasks/server-created-2.PNG)

## <a name="3-create-the-grouputilities-r2-repository"></a>3. Skapa databasen GroupUtilities (R2)

Att skapa den **GroupUtilities** (R2)-databasen under VSTS server:

- Öppna den **skapar en ny lagringsplats** guiden, klickar du på **nya databasen** på den **versionskontroll** fliken i ditt team projekt. 

![7](./media/group-manager-tasks/create-grouputilities-repo-1.png) 

- Välj *Git* som den **typen**, och ange *GroupUtilities* som den **namn**, och klicka sedan på **skapa**. 

![8](./media/group-manager-tasks/create-grouputilities-repo-2.png)
                
Nu bör du se två Git databaser **GroupProjectTemplate** och **GroupUtilities** i den vänstra kolumnen i den **versionskontroll** sidan: 

![9](./media/group-manager-tasks/two-repo-under-groupCommon.PNG)


## <a name="4-create-the-groupprojecttemplate-r1-repository"></a>4. Skapa GroupProjectTemplate (R1) databasen

Installationen av databaser för servern VSTS grupp består av två aktiviteter:

- Byt namn på standardvärdet **GroupCommon** databasen***GroupProjectTemplate***.
- Skapa den **GroupUtilities** databasen på servern VSTS under grupprojekt **GroupCommon**. 

Instruktioner för den första aktiviteten finns i det här avsnittet efter anmärkningar på namngivningskonventioner eller våra databaser och kataloger. Instruktioner för den andra aktiviteten finns i följande avsnitt i steg 4.

### <a name="rename-the-default-groupcommon-repository"></a>Byt namn på GroupCommon standarddatabas

Att byta namn på standardvärdet **GroupCommon** databasen som *GroupProjectTemplate* (kallas **R1** i den här självstudiekursen):
    
- Klicka på **samarbeta på koden** på den **GroupCommon** team projekt sidan. Du kommer till sidan standard Git-lagringsplats i grupprojektet **GroupCommon**. Den här Git-lagringsplatsen är för närvarande tomt. 

![10](./media/group-manager-tasks/rename-groupcommon-repo-3.png)
        
- Klicka på **GroupCommon** på det övre vänstra hörnet (markeras med en röd ruta i följande bild) på sidan Git-lagringsplats i **GroupCommon** och välj **hantera databaser**(markeras med en grön visas i följande bild). Den här proceduren tar fram den **KONTROLLPANELEN**. 
- Välj den **versionskontroll** fliken i ditt team projekt. 

![11](./media/group-manager-tasks/rename-groupcommon-repo-4.png)

- Klicka på den **...**  till höger om den **GroupCommon** databasen i den vänstra panelen och välj **Byt namn på databasen**. 

![12](./media/group-manager-tasks/rename-groupcommon-repo-5.png)
        
- I den **Byt namn på databasen GroupCommon** guiden POP, ange *GroupProjectTemplate* i den **databasnamn** rutan och klicka på **Byt namn** . 

![13](./media/group-manager-tasks/rename-groupcommon-repo-6.png)



## <a name="5-seed-the-r1--r2-repositories-on-the-vsts-server"></a>5. Startvärde för R1 & R2 databaser på servern VSTS

I det här steget av hur du initiera den *GroupProjectTemplate* (R1) och *GroupUtilities* (R2)-databaser som du skapade i föregående avsnitt. Dessa databaser dirigeras med den ***ProjectTemplate*** (**G1**) och ***verktyg*** (**G2**) databaser som hanteras av Microsofts Team av vetenskapliga data. När den här seeding har slutförts:

- R1 databasen ska ha samma uppsättning kataloger och mallar som G1
- R2-databasen kommer att innehålla uppsättning datavetenskap verktyg som utvecklats av Microsoft.

Seeding används kataloger på din lokala DSVM som mellanliggande fristående platser. Här är de åtgärder som vidtas i detta avsnitt:

- G1 & G2 - klonas till -> LG1 & LG2
- R1 & R2 - klonas till -> LR1 & LR2
- LG1 & LG2 - filer som kopieras till -> LR1 & LR2
- (Valfritt) anpassning av LR1 & LR2
- LR1 & LR2 - innehållet Lägg till -> R1 & R2


### <a name="clone-g1--g2-repositories-to-your-local-dsvm"></a>Klona G1 & G2 databaser till din lokala DSVM

I det här steget kan klona du Team Data vetenskap processen (TDSP) ProjectTemplate databasen (G1) och verktyg (G2) från github-lagringsplatser TDSP till mappar i din lokala DSVM som LG1 och LG2:

- Skapa en katalog som fungerar som värd för alla kloner databaser rotkatalog. 
    -  Skapa en katalog i Windows-DSVM *C:\GitRepos\TDSPCommon*. 
    -  Skapa en katalog i Linux DSVM, *GitRepos\TDSPCommon* i arbetskatalogen. 

- Kör sedan följande kommandon från den *GitRepos\TDSPCommon* directory.

    `git clone https://github.com/Azure/Azure-TDSP-ProjectTemplate`<br>
    `git clone https://github.com/Azure/Azure-TDSP-Utilities`
        
![14](./media/group-manager-tasks/two-folder-cloned-from-TDSP-windows.PNG)

- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnåtts: 
    - G1 - klonas till -> LG1
    - G2 - klonas till -> LG2
- När Kloningen har slutförts kan du ska kunna se två kataloger _ProjectTemplate_ och _verktyg_under **GitRepos\TDSPCommon** directory. 

### <a name="clone-r1--r2-repositories-to-your-local-dsvm"></a>Klona R1 & R2 databaser till din lokala DSVM

I det här steget kan du klona lagringsplatsen GroupProjectTemplate (R1) och GroupUtilities databasen (R2) på lokala kataloger (kallas LR1 och LR2, respektive) under **GitRepos\GroupCommon** på din DSVM.

- För att få URL: er R1 och R2 databaser, gå till din **GroupCommon** hemsida på VSTS. URL: en har vanligtvis *https://\<din VSTS servernamn\>.visualstudio.com/GroupCommon*. 
- Klicka på **kod**. 
- Välj den **GroupProjectTemplate** och **GroupUtilities** databaser. Kopiera och spara vart och ett av de URL: er (HTTPS för Windows. SSH för Linux) från den **klon-URL för** element i sin tur för användning i följande skript:  

![15](./media/group-manager-tasks/find_https_ssh_2.PNG)

- Ändra till den **GitRepos\GroupCommon** katalog på din Windows eller Linux DSVM och kör något av följande uppsättningar med kommandon för att klona R1 och R2 till den katalogen.
        
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

- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnåtts: 
    - R1 - klonas till -> LR1
    - R2 - klonas till -> LR2   


### <a name="seed-your-groupprojecttemplate-lr1-and-grouputilities-lr2"></a>Dirigera dina GroupProjectTemplate (LR1) och GroupUtilities (LR2)

I den lokala datorn, kopiera innehållet i ProjectTemplate och verktyg kataloger (utom metadata i kataloger för .git) under GitRepos\TDSPCommon till katalogerna GroupProjectTemplate och GroupUtilities under **GitRepos\ GroupCommon**. Här följer två aktiviteter för att slutföra i det här steget:

- Kopiera filerna i GitRepos\TDSPCommon\ProjectTemplate (**LG1**) till GitRepos\GroupCommon\GroupProjectTemplate (**LR1**) 
- Kopiera filerna i GitRepos\TDSPCommon\Utilities (**LG2** till GitRepos\GroupCommon\Utilities (**LR2**). 

Kör följande skript i PowerShell-konsolen (Windows) eller konsolen Shell-skript (Linux) för att uppnå dessa två aktiviteter. Du uppmanas att ange de fullständiga sökvägarna till LG1, LR1, LG2 och LR2. Sökvägar som du matar in verifieras. Om du ange en katalog som inte finns, uppmanas du att ange den igen. 

    # Windows DSVM      
    
    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_win.ps1" -outfile "tdsp_local_copy_win.ps1"
    .\tdsp_local_copy_win.ps1 1

![18](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows-2.PNG)

Nu kan du se att filerna i kataloger LG1 och LG1 (förutom filer i katalogen .git) har kopierats till LR1 och LR2, respektive.

![19](./media/group-manager-tasks/copy-two-folder-to-group-folder-windows.PNG)

    # Linux DSVM

    wget "https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/TDSP/tdsp_local_copy_linux.sh"
    bash tdsp_local_copy_linux.sh 1

![20](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux-2.PNG)
        
Du ser nu att filerna i två mappar (förutom filer i katalogen .git) kopieras till GroupProjectTemplate och GroupUtilities respektive.
    
![21](./media/group-manager-tasks/copy-two-folder-to-group-folder-linux.PNG)

- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnåtts:
    - LG1 - filer som kopieras till -> LR1
    - LG2 - filer som kopieras till -> LR2

### <a name="option-to-customize-the-contents-of-lr1--lr2"></a>Alternativet för att anpassa innehållet i LR1 & LR2
    
Om du vill anpassa innehållet i LR1 och LR2 för att uppfylla de specifika behoven i din grupp är det här steget i förfarandet om så är lämpligt. Du kan ändra mall-dokument, ändra katalogstrukturen och lägga till befintliga verktyg som din grupp har utvecklat eller som är användbara för hela gruppen. 

### <a name="add-the-contents-in-lr1--lr2-to-r1--r2-on-group-server"></a>Lägga till innehållet i LR1 & LR2 R1 & R2 på en server

Nu måste du lägga till innehållet i LR1 och LR2 databaser R1 och R2. Här följer git bash-kommandon som du kan köra Windows PowerShell- eller Linux. 

Kör följande kommandon från katalogen GitRepos\GroupCommon\GroupProjectTemplate:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

-M-alternativet kan du ange ett meddelande för git-incheckning.

![22](./media/group-manager-tasks/push-to-group-server-2.PNG)

Du kan se att i din grupp VSTS-servern i databasen GroupProjectTemplate filer synkroniseras omedelbart.

![23](./media/group-manager-tasks/push-to-group-server-showed-up-2.PNG)

Slutligen, ändra till den **GitRepos\GroupCommon\GroupUtilities** katalog och kör samma uppsättning git bash kommandon:

    git status
    git add .
    git commit -m"push from DSVM"
    git push

>[AZURE.NOTE] Om det här är första gången du genomför en Git-lagringsplatsen, måste du konfigurera globala parametrar *user.name* och *user.email* innan du kör den `git commit` kommando. Kör följande två kommandon:
        
    git config --global user.name <your name>
    git config --global user.email <your email address>
 
>Om du checkar in till flera Git-databaser, använda samma namn och e-postadress när du gör dem. Med samma namn och e-postadress bevisar praktiskt vid ett senare tillfälle när du skapar PowerBI instrumentpaneler för att spåra Git-aktiviteter i flera databaser.


- Med hjälp av vår förkortade databasnamn, är det här vad dessa skript har uppnåtts:
    - LR1 - innehållet Lägg till -> R1
    - LR2 - innehållet Lägg till R2 ->

## <a name="6-add-group-members-to-the-group-server"></a>6. Lägga till medlemmar till gruppen-servern

Gruppen VSTS serverns webbsida, klicka på den **kugghjulet ikonen** bredvid ditt användarnamn i det övre högra hörnet, Välj den **säkerhet** fliken. Du kan lägga till medlemmar i gruppen med olika behörigheter.

![24](./media/group-manager-tasks/add_member_to_group.PNG) 


## <a name="next-steps"></a>Nästa steg

Här är länkar till mer detaljerade beskrivningar av de roller och aktiviteter som definierats av Team av vetenskapliga data:

- [Gruppera Manager aktiviteter för en datavetenskap-teamet](group-manager-tasks.md)
- [Gruppuppgifter Lead för ett datavetenskap team](team-lead-tasks.md)
- [Projektet Lead uppgifter för ett datavetenskap team](project-lead-tasks.md)
- [Projektet enskilda deltagare för ett datavetenskap team](project-ic-tasks.md)