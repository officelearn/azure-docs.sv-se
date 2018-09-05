---
title: Distribuera din app till Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du distribuerar appar till Azure och Azure Stack med en hybrid CI/CD-pipeline.
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
ms.date: 09/04/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 391cc4ca4b34149aeda54a60bfe6f6949e5a379b
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697755"
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Självstudie: distribuera appar till Azure och Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig hur du distribuerar ett program till Azure och Azure Stack med hjälp av en hybrid pipeline för kontinuerlig integrering/kontinuerlig leverans (CI/CD).

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> * Initiera en ny version utifrån kod incheckningar till din lagringsplats för Visual Studio Team Services (VSTS).
> * Distribuera din app automatiskt till globala Azure för acceptanstestet för användare.
> * När koden har passerat testning, automatiskt distribuera appen till Azure Stack.

## <a name="benefits-of-the-hybrid-delivery-build-pipe"></a>Fördelarna med hybrid delivery skapa pipe

Affärskontinuitet, säkerhet och tillförlitlighet är avgörande faktorer i programdistributionen. Dessa element är viktiga för din organisation och kritiska till Utvecklingsteamet. En hybrid CI/CD-pipeline kan du konsolidera build-pipes över din lokala miljö och det offentliga molnet. En hybrid delivery-modell kan du ändra distribution platser utan att ändra ditt program.

Andra fördelar med att använda hybrid-metoden är:

* Du kan bibehålla en konsekvent uppsättning utvecklingsverktyg för Azure Stack-miljön på plats och det offentliga Azure-molnet.  En gemensam verktygsuppsättning gör det enklare att implementera CI/CD-mönster och metoder.
* Appar och tjänster som distribueras i Azure eller Azure Stack är utbytbara och samma kod kan köras på någon av platserna. Du kan dra nytta av lokala och offentliga molnfunktioner och möjligheter.

Läs mer om CI och CD:

* [Vad är kontinuerlig integrering?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
* [Vad är kontinuerlig leverans?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)

## <a name="prerequisites"></a>Förutsättningar

Du måste ha komponenter på plats för att skapa en hybrid CI/CD-pipeline. Följande komponenter tar tid att förbereda:

* En Azure OEM-maskinvaran partner kan distribuera en Azure Stack i produktionsmiljön. Alla användare kan distribuera Azure Stack Development Kit (ASDK).
* En Azure Stack-Operator måste också: distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och Lägg till Windows Server 2016-avbildning.

>[!NOTE]
>Om du redan har några av dessa komponenter distribueras kan du kontrollera att de uppfyller alla krav innan du påbörjar den här självstudien.

Den här självstudien förutsätter att du har några grundläggande kunskaper om Azure och Azure Stack. Mer information innan du påbörjar självstudiekursen, finns i följande artiklar:

* [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
* [Viktiga begrepp för Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure-requirements"></a>Krav för Azure

* Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
* Skapa en [Web App](https://docs.microsoft.com/azure/app-service/app-service-web-overview) i Azure. Gör Observera av Webbappens URL måste du använda den i självstudien.

### <a name="azure-stack-requirements"></a>Krav för Azure Stack

* Använd ett integrerat Azure Stack-system eller distribuera Azure Stack Development Kit (ASDK). För att distribuera ASDK:
    * Den [självstudie: distribuera ASDK med hjälp av installationsprogrammet](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy) ger detaljerad distributionsanvisningarna.
    * Använd den [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ) PowerShell-skript för att automatisera ASDK efter distributionen steg.

    > [!Note]
    > ASDK installationen tar ungefär sju timmar att slutföra, så planera på lämpligt sätt.

 * Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure Stack.
 * Skapa [Plan/erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack.
 * Skapa en [klient prenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack.
 * Skapa en Webbapp i klientprenumeration. Anteckna den nya Web App-URL för att använda senare.
 * Distribuera VSTS-dator i klientprenumeration.
* Ange en Windows Server 2016-avbildning med .NET 3.5 för en virtuell dator (VM). Den här virtuella datorn kommer att skapas på Azure Stack som en privat skapandeagent.

### <a name="developer-tool-requirements"></a>Krav för Developer-verktyget

* Skapa en [VSTS arbetsytan](https://docs.microsoft.com/vsts/repos/tfvc/create-work-workspaces). Registreringen skapar ett projekt med namnet **MyFirstProject**.
* [Installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) och [logga in på VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
* Anslut till ditt projekt och [klona den lokalt](https://www.visualstudio.com/docs/git/gitquickstart).

 > [!Note]
 > Azure Stack-miljön måste rätt bilderna syndikeras för att köra Windows Server och SQL Server. Det måste också ha App Service som har distribuerats.

## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Förbereda privata infrastruktursgenereringsinställningar och versionen agent för Visual Studio Team Services-integrering

### <a name="prerequisites"></a>Förutsättningar

Visual Studio Team Services (VSTS) autentiseras mot Azure Resource Manager med ett huvudnamn för tjänsten. VSTS måste ha den **deltagare** roll att etablera resurser i en Azure Stack-prenumeration.

Följande steg beskriver vad krävs för att konfigurera autentisering:

1. Skapa ett huvudnamn för tjänsten eller Använd ett befintligt huvudnamn för tjänsten.
2. Skapa autentiseringsnycklar för tjänstens huvudnamn.
3. Verifiera Azure Stack-prenumerationen via rollbaserad åtkomstkontroll för att tillåta den namn SPN (Service Principal) ska vara en del av den deltagarrollen.
4. Skapa en ny tjänstdefinition i VSTS med hjälp av Azure Stack-slutpunkter och SPN-information.

### <a name="create-a-service-principal"></a>Skapa ett huvudnamn för tjänsten

Referera till den [tjänstens huvudnamn skapas](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instruktioner för att skapa ett huvudnamn för tjänsten. Välj **Web App/API** för vilken typ av program eller [använder PowerShell-skriptet](https://github.com/Microsoft/vsts-rm-extensions/blob/master/TaskModules/powershell/Azure/SPNCreation.ps1#L5) enligt beskrivningen i artikeln [skapa en tjänstanslutning för Azure Resource Manager-med en befintlig tjänst huvudnamn ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal).

 > [!Note]  
 > Om du använder skriptet för att skapa en Azure Stack Azure Resource Manager-slutpunkt, måste du skicka den **- azureStackManagementURL** parametern och **- environmentName** parametern. Exempel:  
> `-azureStackManagementURL https://management.local.azurestack.external -environmentName AzureStack`

### <a name="create-an-access-key"></a>Skapa en åtkomstnyckel

Ett huvudnamn för tjänsten kräver en nyckel för autentisering. Använd följande steg för att generera en nyckel.

1. Välj ditt program i **Appregistreringar** i Azure Active Directory.

    ![Välj ett program](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2. Anteckna värdet för **program-ID**. Du använder detta värde när du konfigurerar tjänstslutpunkten i VSTS.

    ![Program-ID:t](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Välj **Inställningar** om du vill generera en autentiseringsnyckel.

    ![Redigera appinställningar](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Välj **Nycklar** om du vill generera en autentiseringsnyckel.

    ![Konfigurera inställningar för datornyckel](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Ange en beskrivning för nyckeln och ange varaktigheten för nyckeln. Välj **Spara** när du är klar.

    ![Nyckelbeskrivning och varaktighet](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    När du har sparat nyckeln, nyckeln **värdet** visas. Kopiera det här värdet eftersom du inte kan hämta det här värdet senare. Du anger den **nyckelvärdet** med program-ID för inloggning som programmet. Lagra nyckelvärdet där programmet kan hämta det.

    ![Nyckel-värde](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-the-tenant-id"></a>Hämta klient-ID

Som en del av konfigurationen av slutpunkten, VSTS kräver den **klient-ID** som motsvarar den AAD-katalog som Azure Stack-stämpel har distribuerats till. Använd följande steg för att hämta klient-ID.

1. Välj **Azure Active Directory**.

    ![Azure Active Directory för klient](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Om du vill hämta klientorganisations-ID:t väljer du **Egenskaper** för din Microsoft Azure Active Directory-klientorganisation.

    ![Visa egenskaper för klient](media\azure-stack-solution-hybrid-pipeline\000_08.png)

3. Kopiera **katalog-ID:t**. Det här värdet är ditt klientorganisations-ID.

    ![Katalog-ID](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Ge tjänstens huvudnamn behörighet att distribuera resurser i Azure Stack-prenumerationen

För att komma åt resurser i din prenumeration, måste du tilldela programmet till en roll. Bestäm vilken roll representerar de bästa behörigheterna för programmet. Läs om tillgängliga roller i [RBAC: inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Du kan ange omfånget för den prenumerationen, resursgruppen eller resursen. Behörigheter ärvs till lägre nivåer av omfång. Till exempel lägga till ett program till rollen Läsare för en resursgrupp innebär att den kan läsa resursgruppen och alla dess resurser.

1. Navigera till den nivå av omfång som du vill tilldela programmet till. Om du vill tilldela en roll prenumerationsområde, väljer du exempelvis **prenumerationer**.

    ![Välj prenumerationer](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. I **prenumeration**, välj Visual Studio Enterprise.

    ![Visual Studio Enterprise](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. I Visual Studio Enterprise, väljer **åtkomstkontroll (IAM)**.

    ![Åtkomstkontroll (IAM)](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Välj **Lägg till**.

    ![Lägg till](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. I **Lägg till behörigheter**, Välj rollen som du vill tilldela till programmet. I det här exemplet på **ägare** roll.

    ![Ägarrollen](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Som standard visas inte Azure Active Directory-program i de tillgängliga alternativen. För att hitta ditt program, måste du ange dess namn i den **Välj** fält som du vill söka efter den. Välj appen.

    ![Sökresultat för App](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

### <a name="role-based-access-control"></a>Rollbaserad Access Control

Azure rollbaserad åtkomstkontroll (RBAC) ger detaljerad åtkomsthantering för Azure. Med RBAC kan styra du vilken åtkomstnivå som användarna behöver för att göra sina jobb. Mer information om rollbaserad åtkomstkontroll finns i [hantera åtkomst till Azure-prenumerationsresurser](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>VSTS-Agentpooler

Du kan ordna agenter i agentpooler istället för att hantera varje agent separat. En agentpool definierar delningsapplikationen gränsen för alla agenter i poolen. Agentpooler är begränsade till VSTS-konto, vilket innebär att du kan dela en agentpool över projekt i VSTS. Läs mer om agentpooler i [skapa Agentpooler och köer](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Lägg till en personlig åtkomsttoken (PAT) för Azure Stack

Skapa en personlig åtkomsttoken för att komma åt VSTS.

1. Logga in på ditt VSTS-konto och välj namnet på ditt profil.
2. Välj **Hantera säkerhet** till sidan för åtkomst-token skapas.

    ![Användarinloggning](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Välj grupprojekt](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Lägg till personlig åtkomsttoken](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Skapa token](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Kopiera token.

    > [!Note]
    > Spara token information. Den här informationen lagras inte och kommer inte att visas igen när du lämnar sidan.

    ![Personlig åtkomsttoken](media\azure-stack-solution-hybrid-pipeline\000_19.png)

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installera VSTS skapa agenten i Azure Stack finns skapa Server

1. Ansluta till servern skapa som du har distribuerat på Azure Stack-värden.
2. Ladda ned och distribuera skapandeagent som en tjänst med ditt personliga åtkomsttoken (PAT) och kör som-konto för VM-administratör.

    ![Ladda ned skapandeagent](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Navigera till mappen för den extraherade skapandeagent. Kör den **config.cmd** filen från en upphöjd kommandotolk.

    ![Extraherade skapandeagent](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Registrera skapandeagent](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4. När config.cmd är klar uppdateras agentmappen version med ytterligare filer. Mappen med extraherad innehållet bör se ut så här:

    ![Skapa mappen uppdatering för agenten](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Du kan se agenten i VSTS-mappen.

## <a name="endpoint-creation-permissions"></a>Behörighet att skapa slutpunkten

Genom att skapa slutpunkter kan distribuera en version av Visual Studio Online (VSTO) Azure Service-appar till Azure Stack. VSTS ansluter till build-agenten, som ansluter till Azure Stack.

![NorthwindCloud exempelprogram i VSTO](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. Logga in på VSTO och gå till inställningssidan.
2. På **inställningar**väljer **Security**.
3. I **VSTS grupper**väljer **Endpoint skapare**.

    ![Skapare av NorthwindCloud slutpunkt](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

4. På den **medlemmar** fliken **Lägg till**.

    ![Lägg till medlem](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

5. I **lägga till användare och grupper**, ange ett användarnamn och Välj användaren i listan med användare.
6. Välj **spara ändringar**.
7. I den **VSTS grupper** väljer **Endpoint administratörer**.

    ![NorthwindCloud Endpoint administratörer](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

8. På den **medlemmar** fliken **Lägg till**.
9. I **lägga till användare och grupper**, ange ett användarnamn och Välj användaren i listan med användare.
10. Välj **spara ändringar**.

## <a name="create-an-azure-stack-endpoint"></a>Skapa en Azure Stack-slutpunkt

Du kan följa anvisningarna i [skapa en tjänstanslutning för Azure Resource Manager-med en befintlig tjänsts huvudnamn ](https://docs.microsoft.com/vsts/pipelines/library/connect-to-azure?view=vsts#create-an-azure-resource-manager-service-connection-with-an-existing-service-principal) artikeln om du vill skapa en anslutning för tjänsten med en befintlig tjänsts huvudnamn och använda följande mappning:

- Miljö: AzureStack
- Miljö-URL: Något som liknar `https://management.local.azurestack.external`
- Prenumerations-ID: Användarens prenumerations-ID från Azure Stack
- Prenumerationsnamn: namn på användarens prenumeration från Azure Stack
- Klient-ID för tjänstens huvudnamn: ägar-ID från [detta](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#create-a-service-principal) i den här artikeln.
- Nyckel för tjänstens huvudnamn: nyckeln från samma artikel (eller lösenordet om du använder skriptet).
- Klient-ID: Klient-ID du hämta följande instruktionen på [hämta klient-ID](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline#get-the-tenant-id).

Nu när slutpunkten har skapats är VSTS till Azure Stack-anslutningen klar att användas. Build-agenten i Azure Stack får anvisningar från VSTS och agenten meddelar sedan slutpunktsinformation för kommunikation med Azure Stack.

![Skapa agenten](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

## <a name="develop-your-application-build"></a>Utveckla din program-version

I den här delen av självstudiekursen ska du:

* Lägg till kod i ett VSTS-projekt.
* Skapa distribution av fristående webbappar.
* Konfigurera kontinuerlig distributionsprocessen

> [!Note]
 > Azure Stack-miljön måste rätt bilderna syndikeras för att köra Windows Server och SQL Server. Det måste också ha App Service som har distribuerats. Läs den App Service-dokumentationen avsnittet ”förutsättningar” krav för Azure Stack-Operator.

Hybrid CI/CD kan använda för både programkoden och infrastrukturkod. Använd [Azure Resource Manager-mallar som web ](https://azure.microsoft.com/resources/templates/) kod från VSTS för att distribuera till båda molnen.

### <a name="add-code-to-a-vsts-project"></a>Lägg till kod i ett VSTS-projekt

1. Logga in på VSTS med ett konto som har behörighet för projektet skapas på Azure Stack. Nästa skärmdump visar hur du ansluter till HybridCICD-projektet.

    ![Ansluta till ett projekt](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klona lagringsplatsen** genom att skapa och öppna standard-webbapp.

    ![Klonade lagringsplats](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Skapa distribution av fristående webbappar för App Services i både moln

1. Redigera den **WebApplication.csproj** fil: Välj **Runtimeidentifier** och Lägg sedan till `win10-x64.` mer information finns i [självständigt distribution](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentation.

    ![Konfigurera Runtimeidentifier](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Använd Team Explorer för att kontrollera koden i VSTS.

3. Bekräfta att programkoden har markerats i Visual Studio Team Services.

### <a name="create-the-build-definition"></a>Skapa build-definition

1. Logga in på VSTS med ett konto som kan skapa en byggesdefinition.
2. Navigera till den **skapa webbprogram** sidan för projektet.

3. I **argument**, lägga till **- r win10-x64** kod. Detta krävs för att utlösa en självständig distribution med .net Core.

    ![Lägg till argumentet build-definition](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

4. Kör versionen. Den [självständigt distributionsbuild](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processen ska publicera artefakter som kan köras på Azure och Azure Stack.

### <a name="use-an-azure-hosted-build-agent"></a>Använd Azure som värd skapandeagent

Med hjälp av en värdbaserad skapandeagent i VSTS är ett praktiskt alternativ för att skapa och distribuera webbappar. Agentunderhåll och uppgraderingar utförs automatiskt av Microsoft Azure, vilket gör det möjligt för en kontinuerlig och oavbruten utvecklingscykeln.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurera kontinuerlig distribution (CD)-processen

Visual Studio Team Services (VSTS) och Team Foundation Server (TFS) ger en mycket konfigurerbar och hanterbara pipeline för versioner till flera miljöer, till exempel utveckling, mellanlagring, kvalitetssäkring (kvalitetskontroll) och produktion. Den här processen kan inkludera att kräva godkännanden i specifika led i programmets hela livscykel.

### <a name="create-release-definition"></a>Skapa versionsdefinition

Skapa en versionsdefinition är det sista steget i ditt program genereringsprocessen. Den här versionsdefinition används för att skapa en version och distribuera en version.

1. Logga in på VSTS och gå till **Build and Release** för ditt projekt.
2. På den **versioner** fliken  **\[ +]** och välj **skapa versionsdefinition**.

   ![Skapa versionsdefinition](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

3. På **Välj en mall**, Välj **Azure App Service-distribution**, och välj sedan **tillämpa**.

    ![Använd mall](media\azure-stack-solution-hybrid-pipeline\102.png)

4. På **Lägg till artefakt**, från den **källa (byggesdefinition)** nedrullningsbara menyn, Välj den Azure-molnet build-app.

    ![Lägg till artefakt](media\azure-stack-solution-hybrid-pipeline\103.png)

5. På den **Pipeline** fliken den **1 fas**, **1 aktivitet** länka till **visa miljöuppgifter**.

    ![Pipelinen i uppgiftsvyn](media\azure-stack-solution-hybrid-pipeline\104.png)

6. På den **uppgifter** ange Azure som den **miljönamn** och välj AzureCloud Traders Web EP från den **Azure-prenumeration** listrutan.

    ![Ange miljövariabler](media\azure-stack-solution-hybrid-pipeline\105.png)

7. Ange den **Azure app service-namn**, vilket är ”northwindtraders” i nästa skärmdumpen.

    ![App service-namn](media\azure-stack-solution-hybrid-pipeline\106.png)

8. Agent-fasen väljer **finns VS2017** från den **fronta agenta** listrutan.

    ![Värdbaserade agent](media\azure-stack-solution-hybrid-pipeline\107.png)

9. I **distribuera Azure App Service**, Välj det giltiga **paket eller mappen** för miljön.

    ![Markera paket eller mapp](media\azure-stack-solution-hybrid-pipeline\108.png)

10. I **Välj fil eller mapp**väljer **OK** till **plats**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\109.png)

11. Spara alla ändringar och gå tillbaka till **Pipeline**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\110.png)

12. På den **Pipeline** fliken **Lägg till artefakt**, och välj den **NorthwindCloud Traders-fartyg** från den **källa (Build-Definition)** listrutan.

    ![Lägg till ny artefakt](media\azure-stack-solution-hybrid-pipeline\111.png)

13. På **Välj en mall**, lägga till en annan miljö. Välj **Azure App Service-distribution** och välj sedan **tillämpa**.

    ![Välj mall](media\azure-stack-solution-hybrid-pipeline\112.png)

14. Ange ”Azure Stack” som den **miljönamn**.

    ![Miljönamn](media\azure-stack-solution-hybrid-pipeline\113.png)

15. På den **uppgifter** fliken, hitta och välj Azure Stack.

    ![Azure Stack-miljön](media\azure-stack-solution-hybrid-pipeline\114.png)

16. Från den **Azure-prenumeration** listrutan väljer du ”AzureStack Traders fartyg EP” för Azure Stack-slutpunkten.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\115.png)

17. Ange Azure Stack webbappens namn som den **App service-namn**.

    ![App service-namn](media\azure-stack-solution-hybrid-pipeline\116.png)

18. Under **val av**, Välj ”AzureStack - bDouglas filnamnsdelen” mellan den **fronta agenta** listrutan.

    ![Välj agent](media\azure-stack-solution-hybrid-pipeline\117.png)

19. För **distribuera Azure App Service**, Välj det giltiga **paket eller mappen** för miljön. På **Välj fil eller mapp**väljer **OK** för mappen **plats**.

    ![Välj paketet eller mapp](media\azure-stack-solution-hybrid-pipeline\118.png)

    ![Godkänn plats](media\azure-stack-solution-hybrid-pipeline\119.png)

20. På den **variabeln** fliken, hitta variabeln med namnet **VSTS_ARM_REST_IGNORE_SSL_ERRORS**. Ange variabelvärdet **SANT**, och ange dess räckvidd till **Azure Stack**.

    ![Konfigurera variabeln](media\azure-stack-solution-hybrid-pipeline\120.png)

21. På den **Pipeline** fliken den **utlösare av kontinuerlig distribution** ikon för NorthwindCloud Traders-Web-artefakt och ange den **utlösare av kontinuerlig distribution** till **Aktiverat**.  Gör samma sak för artefakten ”NorthwindCloud Traders-fartyg”.

    ![Ställ in utlösare av kontinuerlig distribution](media\azure-stack-solution-hybrid-pipeline\121.png)

22. Azure Stack-miljön, Välj den **före villkor** ikonen Ange utlösaren **efter**.

    ![Uppsättning före villkor utlösare](media\azure-stack-solution-hybrid-pipeline\122.png)

23. Spara alla ändringar.

> [!Note]
> Vissa inställningar för publiceringsuppgifter kan ha automatiskt definierats som [miljövariabler](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) när du skapade en versionsdefinition från en mall. De här inställningarna kan inte ändras i uppgiftsinställningarna för. Du kan dock redigera dessa inställningar i de överordnade objekten för miljön.

## <a name="create-a-release"></a>Skapa en version

Nu när du har slutfört ändringar i versionsdefinitionen, är det dags att starta distributionen. Om du vill göra detta måste skapa du en version från versionsdefinitionen. En version som kan skapas automatiskt. till exempel har utlösare av kontinuerlig distribution angetts i versionsdefinitionen. Det innebär att ändra källkoden startar en ny version och, än den som en ny version. Men i det här avsnittet skapar du en ny version manuellt.

1. På den **Pipeline** fliken, öppna den **viktig** nedrullningsbara listan och välj **skapa version**.

    ![Skapa en version](media\azure-stack-solution-hybrid-pipeline\200.png)

2. Ange en beskrivning för versionen, kontrollera att rätt artefakter är markerade och välj sedan **skapa**. Efter en liten stund visas en banderoll som anger att den nya versionen skapades versionen visas som en länk. Välj länken för att se versionen sammanfattningssidan.

    ![Skapa en banderoll för versionen](media\azure-stack-solution-hybrid-pipeline\201.png)

3. Versionen sammanfattningssidan för visar information om versionen. I följande skärmdump för ”Release-2”, den **miljöer** avsnittet visar den **Distributionsstatus** för Azure som ”pågående” och status för Azure Stack är ”SLUTFÖRT”. När distributionsstatusen för Azure-miljön ändras till ”SUCCEEDED”, visas ett popup-meddelande som anger att versionen är klara för godkännande. När en distribution väntar eller har misslyckats, en blå **(i)** informationsikon som visas. Hovra över ikonen så visas ett popup-fönster som innehåller dröjsmål eller underlåtenhet orsaken.

    ![Sammanfattningssida för versionen](media\azure-stack-solution-hybrid-pipeline\202.png)

Andra vyer, till exempel listan över versioner, visas också en ikon som indikerar godkännande är väntande. Popup-fönstret för den här ikonen visas miljönamnet och mer information om distribution. Det är enkelt för en administratör, se det övergripande förloppet för versioner och se vilka versioner väntar på godkännande.

### <a name="monitor-and-track-deployments"></a>Övervaka och spåra distributioner

Det här avsnittet visar hur du kan övervaka och spåra dina distributioner. Versionen för att distribuera de två webbplatserna i Azure App Services ger en bra exempel.

1. På sammanfattningssidan ”Release-2”, Välj **loggar**. Den här sidan visas under distributionen av en live loggen från agenten. Den vänstra rutan visar status för varje åtgärd i distributionen för varje miljö.

    Du kan välja en person ikon i den **åtgärd** kolumnen för en före eller efter distributionen godkännande som godkände (eller avvisade) distributionen, och de angivna meddelandet.

2. När distributionen är klar visas hela loggfilen i den högra rutan. Du kan välja en annan **steg** i den vänstra rutan att visa loggfilen för ett enda steg, till exempel ”initieras jobbet”. Kan visa enskilda loggar gör det enklare att spåra och Felsök delar av den övergripande distributionen. Du kan också **spara** loggfilen för ett steg eller **hämta alla loggar som zip**.

    ![Versionen loggar](media\azure-stack-solution-hybrid-pipeline\203.png)

3. Öppna den **sammanfattning** fliken för att se allmän information om versionen. Den här vyn visar information om versionen, de miljöer som det distribuerades till, Distributionsstatus och annan information om versionen.

4. Välj en miljö-länk (**Azure** eller **Azure Stack**) att se information om befintliga och väntande distributioner till en viss miljö. Du kan använda dessa vyer som ett snabbt sätt att kontrollera att samma version har distribuerats till båda miljöerna.

5. Öppna den **distribueras produktionsapp** i webbläsaren. Till exempel öppna URL: en för Azure App Services-webbplats, `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).
