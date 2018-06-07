---
title: Distribuera din app till Azure och Azure Stack | Microsoft Docs
description: Lär dig mer om att distribuera appar till Azure och Azure-stacken med en hybrid CI/CD-pipeline.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/15/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 41e6f64ada7c95674cc2573048eef8afc83e4385
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604360"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Självstudier: distribuera appar till Azure och Azure-stacken

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Lär dig hur du distribuerar ett program till Azure och Azure-stacken använder en hybrid kontinuerlig integration/kontinuerlig leverans (CI/CD) pipeline.

I den här självstudiekursen skapar du en exempel-miljö till:

> [!div class="checklist"]
> * Initiera en ny version utifrån kod incheckningar till lagringsplatsen för Visual Studio Team Services VSTS ().
> * Distribuera din app automatiskt till globala Azure för användargodkännande.
> * När koden har passerat testning, automatiskt distribuera appen till Azure-stacken.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Fördelarna med hybrid delivery skapa pipe

Kontinuitet, säkerhet och tillförlitlighet är viktiga delar i programdistributionen. Dessa element är viktiga för din organisation och kritiska till Utvecklingsteamet. En hybrid CI/CD-pipeline kan du konsolidera build-pipes över din lokala miljö och det offentliga molnet. En hybrid delivery modell kan du ändra distribution platser utan att ändra ditt program.

Andra fördelar med att använda hybrid-metoden är:

* Du kan underhålla en enhetlig uppsättning utvecklingsverktyg över lokala Azure Stack-miljön och det offentliga Azure-molnet.  En gemensam verktygsuppsättning gör det enklare att implementera CI/CD-design- och metodtips.
* Appar och tjänster som distribueras i Azure eller Azure-stacken är utbytbara och köra samma kod på någon av platserna. Du kan dra nytta av lokala och offentliga molnfunktioner och kapaciteter.

Läs mer om CI och CD:

* [Vad är kontinuerlig Integration?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Vad är kontinuerlig leverans?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Förutsättningar

Du måste ha komponenter för att skapa en hybrid CI/CD-pipeline. Det tar tid att förbereda följande komponenter:

* En Azure OEM/maskinvara partner kan distribuera ett Azure-Stack för produktion. Alla användare kan distribuera Azure Stack Development Kit (ASDK).
* En Operator för Azure-stacken måste också: distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och lägga till Windows Server 2016-avbildningen.

>[!NOTE]
>Om du redan har några av komponenterna distribueras, kontrollera att de uppfyller alla krav innan du börjar den här kursen.

Den här kursen förutsätter att du har några grundläggande kunskaper om Azure och Azure-stacken. Mer information innan du startar guiden, finns i följande artiklar:

* [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Azure-stacken viktiga begrepp](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Krav för Azure

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Skapa en [Webbapp](https://docs.microsoft.com/azure/app-service/app-service-web-overview) i Azure. Observera av Webbappens URL måste du använda den i kursen.

### <a name="azure-stack-requirements"></a>Krav för Azure Stack

* Använda en Azure-stacken integrerat system eller distribuera Azure Stack Development Kit (ASDK). Att distribuera ASDK:
    * Den [genomgång: distribuera ASDK med hjälp av installationsprogrammet](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) ger detaljerade stegvisa anvisningar.
    * Använd den [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell-skript för att automatisera ASDK efter distributionen steg.

    > [!Note]
    > ASDK installationen tar ungefär sju timmar att slutföra, så planera på lämpligt sätt.

 * Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure-stacken.
 * Skapa [Plan/erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure-stacken.
 * Skapa en [klient prenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure-stacken.
 * Skapa en Webbapp i klientprenumeration. Anteckna nya Webbappens URL för att använda senare.
 * Distribuera VSTS virtuella datorn i klientprenumeration.
* Ange en Windows Server 2016-avbildning med .NET 3.5 för en virtuell dator (VM). Den här virtuella datorn skapas på Azure-stacken som en privat build-agent.

### <a name="developer-tool-requirements"></a>Krav för utvecklare

* Skapa en [VSTS arbetsytan](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Registreringsprocessen skapar ett projekt med namnet **MyFirstProject**.
* [Installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) och [logga in på VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Ansluta till ditt projekt och [klona den lokalt](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Din Azure Stack-miljön måste korrigera bilder automatiska för att köra Windows Server och SQL Server. Det måste också ha App-tjänsten har distribuerats.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Förbereda privata version och versionen agent för Visual Studio Team Services integration

### <a name="prerequisites"></a>Förutsättningar

Visual Studio Team Services VSTS () autentiseras mot Azure Resource Manager med ett huvudnamn för tjänsten. VSTS måste ha den **deltagare** roll att etablera resurser i en prenumeration på Azure-stacken.

Följande steg beskriver vad krävs för att konfigurera autentisering:

1. Skapa ett huvudnamn för tjänsten eller Använd ett befintligt huvudnamn för tjänsten.
2. Skapa autentiseringsnycklar för tjänstens huvudnamn.
3. Verifiera Azure-prenumeration via rollbaserad åtkomstkontroll för att tillåta den namnet SPN (Service Principal) ska vara en del av den deltagarrollen för stacken.
4. Skapa en ny Service Definition i VSTS Azure Stack slutpunkter och SPN-informationen.

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Referera till den [tjänstens huvudnamn skapa](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instruktioner för att skapa ett huvudnamn för tjänsten, och välj sedan **Web App/API** för programmet.

### <a name="create-an-access-key"></a>Skapa en snabbtangent

Ett huvudnamn för tjänsten kräver en nyckel för autentisering. Använd följande steg för att generera en nyckel.

1. Välj ditt program i **Appregistreringar** i Azure Active Directory.

    ![Välj program](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Anteckna värdet för **program-ID**. Värdet används när du konfigurerar tjänstslutpunkten i VSTS.

    ![Program-ID:t](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Välj **Inställningar** om du vill generera en autentiseringsnyckel.

    ![Redigera appinställningar för](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

    ![Konfigurera inställningar](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Ange en beskrivning för nyckeln och ange varaktigheten för nyckeln. Välj **Spara** när du är klar.

    ![Beskrivning av nyckeln och varaktighet](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    När du har sparat nyckeln nyckeln **värdet** visas. Kopiera det här värdet eftersom du inte kan få det här värdet senare. Du anger den **nyckelvärdet** med program-ID för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

    ![VÄRDET för nyckeln](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Hämta klient-ID

Som en del av tjänsten slutpunktskonfigurationen, VSTS kräver den **klient-ID** som motsvarar AAD-katalogen som Azure Stack stämpeln distribueras till. Använd följande steg för att hämta klient-ID.

1. Välj **Azure Active Directory**.

    ![Azure Active Directory för klient](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Om du vill hämta klientorganisations-ID:t väljer du **Egenskaper** för din Microsoft Azure Active Directory-klientorganisation.

    ![Visa egenskaper för klient](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Kopiera **katalog-ID:t**. Det här värdet är ditt klientorganisations-ID.

    ![Katalog-ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Ge service principal behörighet att distribuera resurser i Azure Stack-prenumeration

Om du vill komma åt resurser i din prenumeration måste du tilldela program till en roll. Bestäm vilken roll representerar de bästa behörigheterna för programmet. Mer information om tillgängliga roller, se [RBAC: inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Du kan ange omfånget för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs på lägre nivåer i omfånget. Till exempel innebär lägga till ett program rollen Läsare för en resursgrupp att den kan läsa resursgruppen och alla dess resurser.

1. Navigera till den nivå av omfång som du vill tilldela program till. Välj till exempel om du vill tilldela en roll på prenumerationsomfattningen **prenumerationer**.

    ![Välj prenumerationer](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. I **prenumeration**, välj Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Välj i Visual Studio Enterprise **Access Control (IAM)**.

    ![Åtkomstkontroll (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Välj **Lägg till**.

    ![Lägg till](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. I **lägga till behörigheter**, väljer du rollen som du vill tilldela till programmet. I det här exemplet i **ägare** roll.

    ![Ägarrollen](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Som standard visas inte Azure Active Directory-program i de tillgängliga alternativen. Om du vill hitta ditt program måste du ange dess namn i den **Välj** fält som du vill söka efter den. Välj appen.

    ![Sökresultat för App](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

### <a name="role-based-access-control"></a>Rollbaserad Access Control

Azure rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure. Med RBAC kan styra du åtkomstnivån som användarna behöver för att utföra sitt arbete. Mer information om rollbaserad åtkomstkontroll finns [hantera åtkomst till resurser i Azure-prenumeration](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>VSTS Agent pooler

Du kan sortera agenter i agenten pooler i stället för att hantera varje agent separat. En agent poolen definierar delning gränsen för alla agenter i den poolen. I VSTS, är agent pooler begränsade till VSTS konto, vilket innebär att du kan dela en agent pool över grupprojekt. Läs mer om agenten pooler i [skapa agenten pooler och köer](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Lägga till en personlig åtkomsttoken (PATRIK) för Azure-stacken

Skapa en personlig åtkomst-Token för att komma åt VSTS.

1. Logga in på ditt konto i VSTS och välj namnet på profilen ditt konto.
2. Välj **Hantera säkerhet** till sidan för åtkomst-token skapas.

    ![Användaren logga in](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Välj grupprojekt](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Lägg till personlig åtkomsttoken](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Skapa token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Kopiera token.

    > [!Note]
    > Spara token information. Den här informationen lagras inte och kommer inte att visas igen när du lämnar sidan.

    ![Personlig åtkomsttoken](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installera VSTS skapa agenten i Azure-stacken värdbaserade skapa servern

1. Ansluta till servern skapa som du har distribuerat på Azure Stack-värden.
2. Hämta och distribuera build-agenten som en tjänst med ditt personliga åtkomsttoken (PATRIK) och kör som-konto för VM-administratör.

    ![Ladda ned build-agent](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Navigera till mappen för extraherade build-agenten. Kör den **run.cmd** filen från en upphöjd kommandotolk.

    ![Extraherade build-agent](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Registrera build-agent](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. När run.cmd är klar uppdateras mappen build-agenten med ytterligare filer. Mappen med det extraherade innehållet bör se ut ungefär så här:

    ![Skapa agent mappen uppdatering](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Du kan se agenten i VSTS mapp.

## <a name="endpoint-creation-permissions"></a>Behörighet att skapa slutpunkten

En version av Visual Studio Online (VSTO) kan distribuera appar i Azure Service till Azure-stacken genom att skapa slutpunkter. VSTS ansluter till build-agent som ansluter till Azure-stacken.

![NorthwindCloud exempelapp i VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Logga in på VSTO och gå till inställningssidan för appen.
2. På **inställningar**väljer **säkerhet**.
3. I **VSTS grupper**väljer **Endpoint skapare**.

    ![Skapare av NorthwindCloud slutpunkt](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. På den **medlemmar** väljer **Lägg till**.

    ![Lägga till en medlem](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. I **lägga till användare och grupper**, ange ett användarnamn och väljer användaren från listan över användare.
6. Välj **spara ändringar**.
7. I den **VSTS grupper** väljer **Endpoint administratörer**.

    ![NorthwindCloud Endpoint administratörer](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. På den **medlemmar** väljer **Lägg till**.
9. I **lägga till användare och grupper**, ange ett användarnamn och väljer användaren från listan över användare.
10. Välj **spara ändringar**.

Nu endpoint-informationen finns, VSTS till Azure-stacken anslutning är redo att användas. Build-agenten i Azure-stacken får instruktioner från VSTS och meddelar agenten slutpunktsinformation för kommunikation med Azure-stacken.

![Skapa agent](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Utveckla program-version

I den här delen av kursen ska du:

* Lägg till kod i ett VSTS projekt.
* Skapa fristående web app-distribution.
* Konfigurera kontinuerlig distributionsprocessen

> [!Note]
 > Din Azure Stack-miljön måste korrigera bilder automatiska för att köra Windows Server och SQL Server. Det måste också ha App-tjänsten har distribuerats. Läs dokumentationen Apptjänst avsnittet ”förutsättningar” Azure Stack operatorn krav.

Hybrid CI/CD gäller både programkoden och infrastrukturkod. Använd [Azure Resource Manager-mallar som web ](https://azure.microsoft.com/resources/templates/) Appkod från VSTS att distribuera till både moln.

### <a name="add-code-to-a-vsts-project"></a>Lägg till kod i ett VSTS-projekt

1. Logga in på VSTS med ett konto som har behörighet för projektet skapa Azure-stacken. Nästa skärmdump visar hur du ansluter till HybridCICD-projektet.

    ![Ansluta till ett projekt](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klona lagringsplatsen** genom att skapa och öppna standard-webbprogrammet.

    ![Klona lagringsplatsen](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Skapa fristående web app-distribution för Apptjänster i både moln

1. Redigera den **WebApplication.csproj** fil: Välj **Runtimeidentifier** och Lägg sedan till `win10-x64.` mer information finns i [självständiga distribution](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentation.

    ![Konfigurera Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Team Utforskaren för att kontrollera koden i VSTS.

3. Bekräfta att programkoden checkats in i Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Skapa build-definition

1. Logga in på VSTS med ett konto som kan skapa en build-definition.
2. Navigera till den **skapa Web Flersvalsstart** sidan för projektet.

3. I **argument**, lägga till **- r win10-x64** kod. Detta krävs för att utlösa en fristående distribution med .net Core.

    ![Lägg till argumentet build-definition](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Kör versionen. Den [självständiga distributionen bygga](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processen ska publicera artefakter som kan köras på Azure och Azure-stacken.

### <a name="use-an-azure-hosted-build-agent"></a>Använd en av Azure finns build-agent

Använda en värdbaserad build-agent i VSTS är ett bekvämt alternativ för att skapa och distribuera webbprogram. Agentunderhåll och uppgraderingar utförs automatiskt av Microsoft Azure, vilket gör det möjligt för en kontinuerlig och oavbruten utvecklingscykeln.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurera kontinuerlig distributionsprocessen (CD)

Visual Studio Team Services VSTS () och Team Foundation Server (TFS) ger en mycket konfigurerbar och hanterbar pipeline för versioner till flera miljöer, till exempel utveckling, mellanlagring, quality assurance (QA) och produktion. Den här processen kan ta som kräver godkännanden i specifika led i livscykeln för programmet.

### <a name="create-release-definition"></a>Skapa en definition för versionen

Att skapa en definition av versionen är det sista steget i ditt program Byggprocessen. Den här versionen definitionen används för att skapa en version och distribuera en version.

1. Logga in i VSTS och navigera till **bygga och släpper** för projektet.
2. På den **versioner** väljer  **\[ +]** och välj sedan **skapa versionen definition**.

   ![Skapa en definition för versionen](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. På **Välj en mall**, Välj **Azure App Service-distributionen**, och välj sedan **tillämpa**.

    ![Använd mall](media\azure-stack-solution-hybrid-pipeline\102.png)

4. På **Lägg till artefakt**, från den **källa (Build definition)** nedrullningsbara menyn väljer du den Azure-molnet build-app.

    ![Lägg till artefakt](media\azure-stack-solution-hybrid-pipeline\103.png)

5. På den **Pipeline** väljer den **1 fasen**, **1 aktivitet** länka till **visa miljöuppgifter**.

    ![Visa uppgifter i pipelinen](media\azure-stack-solution-hybrid-pipeline\104.png)

6. På den **uppgifter** ange Azure som den **miljönamn** och välj AzureCloud Traders Web EP från den **Azure-prenumeration** listrutan.

    ![Ange miljövariabler](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Ange den **Azure apptjänst namn**, vilket är ”northwindtraders” i nästa skärmdump.

    ![Apptjänst-namn](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Fasen Agent väljer **finns VS2017** från den **Agent kön** listrutan.

    ![Värdbaserade agent](media\azure-stack-solution-hybrid-pipeline\107.png)

9. I **distribuera Azure App Service**, Välj det giltiga **paket eller mappen** för miljön.

    ![Markera paketet eller mappen](media\azure-stack-solution-hybrid-pipeline\108.png)

10. I **Välj filen eller mappen**väljer **OK** till **plats**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Spara alla ändringar och gå tillbaka till **Pipeline**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\110.png)

12. På den **Pipeline** väljer **Lägg till artefakt**, och välj den **NorthwindCloud Traders-fartyg** från den **källa (skapa Definition)** listrutan.

    ![Lägg till ny artefakt](media\azure-stack-solution-hybrid-pipeline\111.png)

13. På **Välj en mall**, lägga till en annan miljö. Välj **Azure App Service-distributionen** och välj sedan **tillämpa**.

    ![Välj mall](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Ange ”Azure Stack” som den **miljönamn**.

    ![Miljönamn](media\azure-stack-solution-hybrid-pipeline\113.png)

15. På den **uppgifter** , söka efter och välj Azure-stacken.

    ![Azure Stack-miljön](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Från den **Azure-prenumeration** listrutan väljer du ”AzureStack Traders fartyget EP” för Azure Stack-slutpunkten.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Ange Azure-stacken webbprogrammets namn som den **Apptjänst namn**.

    ![Apptjänst-namn](media\azure-stack-solution-hybrid-pipeline\116.png)

18. Under **Agent markeringen**, Välj ”AzureStack - bDouglas Fir” från den **Agent kön** listrutan.

    ![Välj agent](media\azure-stack-solution-hybrid-pipeline\117.png)

19. För **distribuera Azure App Service**, Välj det giltiga **paket eller mappen** för miljön. På **Välj filen eller mappen**väljer **OK** för mappen **plats**.

    ![Välj paketet eller mappen](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Godkänn plats](media\azure-stack-solution-hybrid-pipeline\119.png)

20. På den **variabeln** fliken, hitta variabeln med namnet **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Ange variabeln värdet till **SANT**, och ange sitt omfång och **Azure Stack**.

    ![Konfigurera variabeln](media\azure-stack-solution-hybrid-pipeline\120.png)

21. På den **Pipeline** väljer den **kontinuerlig distribution utlösaren** ikonen för NorthwindCloud Traders-Web artefakt och ange den **kontinuerlig distribution utlösaren** till **Aktiverat**.  Gör samma sak för artefakten ”NorthwindCloud Traders-fartyg”.

    ![Ange kontinuerlig distribution utlösare](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Azure Stack-miljön, Välj den **före distributionen villkor** ikonen sägs utlösaren **efter utgivningen**.

    ![Uppsättning före distributionen villkor utlösare](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Spara alla ändringar.

> [!Note]
> Vissa inställningar för versionen uppgifter kan ha automatiskt definierats som [miljövariabler](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) när du skapade en definition av utgåva från en mall. De här inställningarna kan inte ändras i Aktivitetsinställningarna för. Men kan du redigera de här inställningarna i överordnade miljö artiklar.

## <a name="create-a-release"></a>Skapa en version

Nu när du har slutfört ändringarna versionen definition, är det dags att starta distributionen. Om du vill göra detta måste skapa du en version från versionen definition. En version som kan skapas automatiskt. till exempel anges kontinuerlig distribution utlösaren i definitionen för versionen. Det innebär att ändringar i källkoden startar en ny version och från som en ny version. Dock viktiga manuellt i det här avsnittet skapar du en ny.

1. På den **Pipeline** och öppna den **släpper** nedrullningsbara listan och välj **skapa version**.

    ![Skapa en version](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Ange en beskrivning för versionen, kontrollera att rätt artefakterna är markerade och välj **skapa**. Efter en liten stund visas en banderoll som anger att den nya versionen har skapats och versionen namnet visas som en länk. Välj länken för att se versionen sammanfattningssidan.

    ![Versionen skapa banderoll](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Versionen sammanfattningssidan för innehåller information om versionen. I följande skärmbild för ”version 2”, den **miljöer** avsnittet visas de **Distributionsstatus** för Azure som ”pågående” och status för Azure-stacken är ”lyckades”. När distributionsstatusen för Azure-miljön ändras till ”SUCCEEDED”, visas en banderoll som anger att versionen är klara för godkännande. När en distribution är i vänteläge eller misslyckad, en blå **(i)** informationsikon som visas. Hovra över ikonen så visas ett popup-fönster som innehåller orsaken till fördröjningen eller fel.

    ![Sammanfattningssida för versionen](media\azure-stack-solution-hybrid-pipeline\202.png)

Andra vyer, till exempel listan över versioner, visar även en ikon som indikerar godkännande väntar. Popup-fönstret för den här ikonen visar namnet på miljön och mer information som rör distributionen. Det är enkelt för en administratör finns det övergripande förloppet för versioner och se vilka versioner väntar på godkännande.

### <a name="monitor-and-track-deployments"></a>Övervaka och spåra distributioner

Det här avsnittet visar hur du kan övervaka och spåra alla distributioner. Versionen för att distribuera de två webbplatserna i Azure App Service tillhandahåller ett bra exempel.

1. På sidan ”version 2” sammanfattning väljer **loggar**. Den här sidan visar live loggen under en distribution från agenten. Den vänstra rutan visas status för varje åtgärd i distributionen för varje miljö.

    Du kan välja en person ikon i den **åtgärd** kolumn för en före eller efter distributionen godkännande för att se vem som ska godkänna (eller avvisa) distributionen och de angivna meddelandet.

2. När distributionen är klar visas hela loggfilen i den högra rutan. Du kan välja en annan **steg** i den vänstra rutan att visa loggfilen för ett enskilt steg, till exempel ”initiera jobb”. Kan visa enskilda loggar gör det enklare att spåra och felsöka delar av den övergripande distributionen. Du kan också **spara** loggfilen för ett steg eller **hämta alla loggar som zip**.

    ![Släpp loggar](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Öppna den **sammanfattning** fliken för att se allmän information om versionen. Den här vyn visar information om versionen, i miljöer som den har distribuerats till, Distributionsstatus och annan information om versionen.

4. Välj en länk i miljön (**Azure** eller **Azure Stack**) att se information om befintliga och väntande distributioner till en viss miljö. Du kan använda dessa vyer är ett snabbt sätt att kontrollera att samma version har distribuerats till båda miljöerna.

5. Öppna den **distribueras produktionsprogrammet** i webbläsaren. Till exempel öppna URL: en för webbplatsen för Azure App Service `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Cloud mönster i [designmönster](https://docs.microsoft.com/azure/architecture/patterns).
