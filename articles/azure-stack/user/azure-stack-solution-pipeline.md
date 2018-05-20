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
ms.openlocfilehash: 483122f5d2e39c8595d0f28a6b937772c4ea2e50
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-deploy-apps-to-azure-and-azure-stack"></a>Självstudier: Distribuera appar till Azure och Azure Stack

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

En hybrid-kontinuerlig integration/kontinuerlig leverans (CI/CD)-pipeline kan du skapa, testa och distribuera din app till flera moln.  I den här självstudiekursen skapar du en exempel-miljö till:
 
> [!div class="checklist"]
> * Initiera en ny version utifrån kod incheckningar till lagringsplatsen för Visual Studio Team Services VSTS ().
> * Distribuera automatiskt nyligen inbyggd koden till globala Azure för användargodkännande.
> * När koden har gått testning, automatiskt distribuera till Azure-stacken.

### <a name="about-the-hybrid-delivery-build-pipe"></a>Skapa pipe om hybrid delivery

Kontinuitet för distribution av program, säkerhet och tillförlitlighet är viktiga för din organisation och kritiska till Utvecklingsteamet. Med en hybrid CI/CD-pipeline kan du konsolidera din pipelines mellan din lokala miljö och det offentliga molnet. Du kan ändra plats utan att växla ditt program.

Den här metoden kan du bibehålla en konsekvent uppsättning utvecklingsverktyg. Konsekvent verktyg i det offentliga Azure-molnet och lokala Azure Stack-miljö innebär att det är mycket enklare att implementera CI/CD dev praxis. Appar och tjänster som distribueras i Azure eller Azure-stacken är utbytbara och samma kod kan köras i båda fallen genom att dra nytta av lokala och offentliga molnfunktioner och möjligheter.

Läs mer om:
 - [Vad är kontinuerlig Integration?](https://www.visualstudio.com/learn/what-is-continuous-integration/)
 - [Vad är kontinuerlig leverans?](https://www.visualstudio.com/learn/what-is-continuous-delivery/)


## <a name="prerequisites"></a>Förutsättningar

Du behöver ha några komponenter för att skapa en hybrid CI/CD-pipeline. De kan ta lite tid att förbereda.
 
 - En Azure OEM/maskinvara partner kan distribuera ett Azure-Stack för produktion och alla användare kan distribuera ett Azure Stack Development Kit (ASDK). 
 - Operatör Azure stacken måste också distribuera App Service, skapa planer och erbjudanden, skapa en klientprenumeration och lägga till Windows Server 2016-avbildningen.

Om du redan har vissa av dessa komponenter, kontrollera att de uppfyller krav innan du börjar.

Det här avsnittet förutsätter också att du har viss erfarenhet av Azure och Azure-stacken. Om du vill veta mer innan du fortsätter bör du börja med dessa avsnitt:


Den här kursen förutsätter också att du har viss erfarenhet av Azure och Azure-stacken. 

Om du vill veta mer innan du fortsätter kan du börja med dessa avsnitt:
 - [Introduktion till Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Azure-stacken viktiga begrepp](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

### <a name="azure"></a>Azure

 - Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

 - Skapa en [Webbapp](https://docs.microsoft.com/azure/app-service/app-service-web-overview) i Azure. Anteckna den nya Web App-URL, eftersom den används senare.

Azure Stack
 - Använda en Azure-stacken integrerat system eller distribuera Azure Stack Development Kit (ASDK) länkade nedan:
    - Du kan hitta detaljerade anvisningar om hur du distribuerar ASDK på ”[genomgång: distribuera ASDK med hjälp av installationsprogrammet](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy)”
    - Du kan automatisera många av dina ASDK efter distributionen steg med följande PowerShell-skript [ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1 ).

    > [!Note]  
    > ASDK installationen tar en sju timmar att slutföra, så planera på lämpligt sätt.

 - Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure-stacken. 
 - Skapa [Plan/erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack-miljön. 
 - Skapa [klient prenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack-miljön. 
 - Skapa en Webbapp i klient-prenumerationen. Anteckna nya Webbappens URL för att använda senare.
 - Distribuera VSTS virtuella datorn fortfarande inom klientprenumeration.
 - Windows Server 2016 VM med .NET 3.5 krävs. Den här virtuella datorn kommer att skapas på Azure-stacken som privat build-agenten. 

### <a name="developer-tools"></a>Utvecklarverktyg

 - Skapa en [VSTS arbetsytan](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services). Registreringsprocessen skapar ett projekt med namnet **MyFirstProject**.
 - [Installera Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) och [logga in på VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).
 - Ansluta till projektet och [klona lokalt](https://www.visualstudio.com/docs/git/gitquickstart).
 
 > [!Note]  
 > Du behöver Azure stacken med rätt bilder automatiska ska köras (Windows Server och SQL) och har distribuerats Apptjänst.
 
## <a name="prepare-the-private-build-and-release-agent-for-visual-studio-team-services-integration"></a>Förbereda privata version och versionen agent för Visual Studio Team Services integration

### <a name="prerequisites"></a>Förutsättningar

Visual Studio Team Services VSTS () autentiseras mot Azure Resource Manager med ett huvudnamn för tjänsten. Den kräver statusen deltagare för VSTS för att kunna etablera resurser i en prenumeration på Azure-stacken.

Följande är de övergripande stegen som behöver konfigureras så att sådana autentisering:

1. Tjänstens huvudnamn ska skapas eller en befintlig kan användas.
2. Autentiseringsnycklar måste skapas för tjänstens huvudnamn.
3. Azure Stack-prenumeration behöver verifieras via rollbaserad åtkomstkontroll för att tillåta SPN vara en del av den deltagarrollen.
4. En ny Service Definition i VSTS måste skapas med hjälp av Azure-stacken slutpunkter samt information om SPN.

### <a name="service-principal-creation"></a>Skapa en tjänst huvudnamn

Referera till den [tjänstens huvudnamn skapa](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) instruktioner för att skapa ett huvudnamn för tjänsten, och välj Web App/API för programmet.

### <a name="access-key-creation"></a>Åtkomst nycklarna skapas

Ett huvudnamn för tjänsten kräver en nyckel för autentisering, följer du stegen i det här avsnittet för att generera en nyckel.


1. Välj ditt program i **Appregistreringar** i Azure Active Directory.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_01.png)

2.  Anteckna värdet för **program-ID**. Värdet används när du konfigurerar tjänstslutpunkten i VSTS.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_02.png)

3. Välj **Inställningar** om du vill generera en autentiseringsnyckel.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_03.png)

4. Välj **Nycklar** om du vill generera en autentiseringsnyckel.
 
    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_04.png)

5. Tillhandahåll beskrivning av och varaktighet för nyckeln. Välj **Spara** när du är klar.
 
    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_05.png)

    När du har sparat nyckeln visas nyckelns värde. Kopiera det här värdet eftersom det inte går att hämta nyckeln senare. Du anger den **nyckelvärdet** med program-ID för att logga in som programmet. Lagra nyckelvärdet där programmet kan hämta det.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_06.png)

### <a name="get-tenant-id"></a>Hämta klientorganisations-ID

Som en del av tjänsten slutpunktskonfigurationen, VSTS kräver den **klient-ID** som motsvarar den AAD-katalog som Azure Stack stämpeln har distribuerats till. Följ stegen i det här avsnittet för att samla in klient-Id.

1. Välj **Azure Active Directory**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_07.png)

2. Om du vill hämta klientorganisations-ID:t väljer du **Egenskaper** för din Microsoft Azure Active Directory-klientorganisation.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_08.png)
 
3. Kopiera **katalog-ID:t**. Det här värdet är ditt klientorganisations-ID.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_09.png)

### <a name="grant-the-service-principal-rights-to-deploy-resources-in-the-azure-stack-subscription"></a>Ge service principal behörighet att distribuera resurser i Azure Stack-prenumeration 

Om du vill komma åt resurser i din prenumeration måste du tilldela program till en roll. Bestäm vilken roll representerar rätt behörigheter för programmet. Mer information om tillgängliga roller, se [RBAC: inbyggda roller](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Du kan ange omfånget för prenumerationen, resursgruppen eller resursen. Behörigheter ärvs på lägre nivåer i omfånget. Till exempel innebär lägga till ett program rollen Läsare för en resursgrupp att den kan läsa resursgruppen och alla resurser som den innehåller.

1. Navigera till den nivå av omfång som du vill tilldela program till. Välj till exempel om du vill tilldela en roll på prenumerationsomfattningen **prenumerationer**. Du kan i stället välja en resursgrupp eller resurs.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_10.png)

2. Välj den **prenumeration** (resursgrupp eller resurs) för att tilldela program till.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_11.png)

3. Välj **åtkomstkontroll (IAM)**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_12.png)

4. Välj **Lägg till**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_13.png)

5. Markera den roll som du vill tilldela till programmet. Följande bild visar den **ägare** roll.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_14.png)

6. Som standard visas inte Azure Active Directory-program i de tillgängliga alternativen. Om du vill hitta ditt program måste du **ange namnet** i sökfältet. Markera den.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_16.png)

7. Välj **spara** Slutför tilldela rollen. Du ser ditt program i listan över användare som har tilldelats en roll för detta omfång.

### <a name="role-based-access-control"></a>Rollbaserad Access Control

Azure rollbaserad åtkomstkontroll (RBAC) Aktivera detaljerad åtkomsthantering för Azure. Med RBAC kan du bevilja exakt den åtkomstnivå som användarna behöver för att kunna utföra sitt arbete. Mer information om rollbaserad åtkomstkontroll finns [hantera åtkomst till resurser i Azure-prenumeration](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

### <a name="vsts-agent-pools"></a>VSTS Agent pooler

I stället för att hantera varje agent individuellt ordna agenter i agenten pooler. En agent poolen definierar delning gränsen för alla agenter i den poolen. I VSTS, är agent pooler begränsade till kontot VSTS; så kan du dela en agent pool över grupprojekt. Mer information och en genomgång om hur du skapar VSTS agent pooler finns [skapa agenten pooler och köer](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts).

### <a name="add-a-personal-access-token-pat-for-azure-stack"></a>Lägga till en personlig åtkomsttoken (PATRIK) för Azure-stacken

1. Logga in på ditt konto i VSTS och välj namnet på profilen ditt konto.
2. Välj **Hantera säkerhet** till sidan för åtkomst-token skapas.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_17.png)

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_18.png)

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_18a.png)

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_18b.png)

3. Kopiera token.
    
    > [!Note]  
    > Hämta token information. Det kommer inte att visas igen när du lämnar den här skärmen. 
    
    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_19.png)
    

### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Installera VSTS skapa agenten i Azure-stacken värdbaserade skapa servern

1.  Ansluta till servern skapa som du har distribuerat på Azure Stack-värden.

2.  Hämta och distribuera build-agenten som en tjänst med ditt personliga åtkomsttoken (PATRIK) och kör som-konto för VM-administratör.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\010_downloadagent.png)

3. Gå till extraherade build-agentmappen. Kör den **run.cmd** filen från en upphöjd kommandotolk. 

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_20.png)

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\000_21.png)

4.  När run.cmd klar mapp med det extraherade innehållet bör se ut som följande:

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\009_token_file.png)

    Du kan nu se agenten i VSTS mapp.

## <a name="endpoint-creation-permissions"></a>Behörighet att skapa slutpunkten

Användare kan skapa slutpunkter så VSTO versioner kan distribuera appar i Azure-tjänsten till stacken. VSTS ansluter till build-agent, som sedan ansluter med Azure-stacken. 

![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\012_securityendpoints.png)

1. På den **inställningar** väljer du **säkerhet**.
2. I den **VSTS grupper** listan till vänster, Välj **Endpoint skapare**. 

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\013_endpoint_creators.png)

3. På den **medlemmar** väljer **+ Lägg till**. 

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\014_members_tab.png)

4. Ange ett användarnamn och väljer användaren från listan.
5. Klicka på **spara ändringar**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\015_save_endpoint.png)

6. I den **VSTS grupper** listan till vänster, Välj **Endpoint administratörer**.
7. På den **medlemmar** väljer **+ Lägg till**.
8. Ange ett användarnamn och väljer användaren från listan.
9. Klicka på **spara ändringar**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\016_save_changes.png)

    Build-agenten i Azure-stacken får instruktioner från VSTS som meddelar slutpunktsinformation för kommunikation med Azure-stacken. 
    
    VSTS till Azure-stacken anslutning är nu klar.

## <a name="develop-your-application"></a>Utveckla ditt program

Ställ in hybrid CI/CD för att distribuera Webbappen till Azure och Azure-stacken och automatisk push ändras till både moln.

> [!Note]  
> Du behöver Azure stacken med rätt bilder automatiska ska köras (Windows Server och SQL) och har distribuerats Apptjänst. Läs dokumentationen Apptjänst avsnittet ”förutsättningar” Azure Stack operatorn krav.

### <a name="add-code-to-vsts-project"></a>Lägg till kod i VSTS projekt

1. Logga in på Visual Studio med ett konto som har behörighet för projektet skapa Azure-stacken.

    Hybrid CI/CD gäller både programkoden och infrastrukturkod. Använd [Azure Resource Manager-mallar som web ](https://azure.microsoft.com/resources/templates/) app koden från VSTS till både moln.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\017_connect_to_project.png)

2. **Klona lagringsplatsen** genom att skapa och öppna standard-webbprogrammet.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\018_link_arm.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Skapa fristående web app-distribution för Apptjänster i både moln

1. Redigera den **WebApplication.csproj** fil: Välj **Runtimeidentifier** och Lägg till `win10-x64.` mer information finns [fristående distribution](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentation .

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\019_runtimeidentifer.png)

2. Kontrollera koden i VSTS Team Utforskaren.

3. Bekräfta att programkoden har kontrollerats i Visual Studio Team Services. 

### <a name="create-the-build-definition"></a>Skapa build-definition

1. Logga in på VSTS bekräfta möjlighet att skapa build-definitioner.

2. Lägg till **- r win10-x64** kod. Detta är nödvändigt för att utlösa en fristående distribution med .net Core. 

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\020_publish_additions.png)

3. Kör versionen. Den [självständiga distributionen bygga](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processen ska publicera artefakter som kan köras på Azure och Azure-stacken.

### <a name="using-an-azure-hosted-agent"></a>Med hjälp av en Azure värdbaserade Agent

Använda en värdbaserad agent i VSTS är ett enkelt alternativ för att skapa och distribuera webbprogram. Underhåll och uppgraderingar utförs automatiskt av Microsoft Azure, aktivera kontinuerliga, oavbruten utveckling, testning och distribution.

### <a name="manage-and-configure-the-continuous-deployment-cd-process"></a>Hantera och konfigurera kontinuerlig distribution (CD)-processen

Visual Studio Team Services VSTS () och Team Foundation Server (TFS) ger en mycket konfigurerbar och hanterbar pipeline för versioner till flera miljöer, till exempel utveckling, mellanlagring, QA och produktionsmiljöer; inklusive som kräver godkännanden i specifika led.

### <a name="create-release-definition"></a>Skapa en definition för versionen

![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\021a_releasedef.png)

1. Välj den  **\[ +]** att lägga till en ny version under den **versioner fliken** på sidan version och utgåva av VSO.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\102.png)

2. Tillämpa den **Azure App Service-distributionen** mall.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\103.png)

3. Lägg till artefakt nedrullningsbara menyn **lägga till artefakten** för Azure-molnet bygga appen.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\104.png)

4. Pipeline-fliken, Välj den **fasen**, **aktivitet** länka miljön och ange Azure-molnet miljövärden.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\105.png)

5. Ange den **miljönamn** och välj Azure **prenumeration** för slutpunkten för Azure-molnet.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\106.png)

6. Ange de nödvändiga under miljönamn **Azure apptjänst namn**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\107.png)

7. Ange **finns VS2017** under Agent kö för Azure värdbaserad molnmiljö.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\108.png)

8. Distribuera Azure App Service-menyn, Välj det giltiga **paket eller mappen** för miljön. Välj **OK** till **mappen**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\109.png)

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\110.png)

9. Spara alla ändringar och gå tillbaka till **versionen pipeline**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\111.png)

10. Lägg till en **ny artefakt** välja build för Azure Stack-app.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\112.png)

11. Lägg till en mer miljö tillämpa den **Azure App Service-distributionen**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\113.png)

12. Namnge den nya miljön **Azure Stack**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\114.png)

13. Hitta Azure-stacken miljön under **aktivitet** fliken.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\115.png)

14. Välj den **prenumeration** för Azure Stack-slutpunkten.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\116.png)

15. Ange Azure-stacken webbprogrammets namn som den **Apptjänst namn**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\117.png)

16. Välj den **Azure Stack agent**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\118.png)

17. Under distribuera Azure App Service avsnittet väljer du det giltiga **paket eller mappen** för miljön. Välj OK om du vill **mappen**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\119.png)

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\120.png)

18. Variabeln fliken lägger du till en variabel med namnet **VSTS_ARM_REST_IGNORE_SSL_ERRORS**, ange värdet som **SANT**, och begränsa till **Azure Stack**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\121.png)

19. Välj den **kontinuerlig** distribution utlösa ikonen i båda artefakter och aktivera fortsätter distributionen utlösaren.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\122.png)

20. Välj den **före distributionen** villkor ikonen i azure stacken miljön och ange utlösaren till **efter utgivningen**.

21. Spara alla ändringar.

> [!Note]  
> Vissa inställningar för uppgifter kan ha automatiskt definierats som [miljövariabler](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) när du skapade en definition av utgåva från en mall. De här inställningarna kan inte ändras i aktivitetsinställningar; Du måste i stället välja överordnade miljöobjekt du vill redigera inställningarna.

## <a name="create-a-release"></a>Skapa en version

Nu när du har slutfört ändringarna versionen definition, är det dags att starta distributionen. Om du vill göra detta måste skapa du en version från versionen definition. En version som kan skapas automatiskt. till exempel anges kontinuerlig distribution utlösaren i definitionen för versionen. Det innebär att ändringar i källkoden startar en ny version och från som en ny version. Dock viktiga manuellt i det här avsnittet skapar du en ny.

1. Öppna den **släpper** nedrullningsbara listan och välj **skapa version**.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\200.png)
 
2. Ange en beskrivning för versionen, kontrollera att rätt artefakterna är markerade och välj sedan **skapa**. Efter en liten stund visas en banderoll som anger att den nya versionen har skapats. Välj länken (namnet på versionen).

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\201.png)
 
3. Sammanfattningssida för versionen öppnas som visar information om versionen. I den **miljöer** avsnittet visas Distributionsstatus för miljön ”QA” ändra från ”Pågående” till ”SUCCEEDED”, och då en banderoll visas som anger att versionen nu väntar på godkännande. Informationsikon som visas när en distribution i en miljö som är i vänteläge eller har misslyckats, blå (i). Peka på det här alternativet om du vill se ett popup-fönster som innehåller orsaken.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\202.png)

Andra vyer, till exempel listan över versioner, visas en ikon som indikerar godkännande är också väntande. Ikonen visas ett popup-fönster som innehåller namnet på miljön och mer information när du pekar på den. Detta gör det enkelt för en administratör att se vilka versioner väntar på godkännande, samt det övergripande förloppet för alla versioner.

### <a name="monitor-and-track-deployments"></a>Övervaka och spåra distributioner

I det här avsnittet visas hur du kan övervaka och spåra distributioner – i det här exemplet att två webbplatser i Azure App Service - från den version som du skapade i föregående avsnitt.

1. I version sammanfattnings-sidan väljer du den **loggar** länk. Medan distributionen sker visas den här sidan live loggen från agenten och, i den vänstra rutan, uppgift om statusen för varje åtgärd i distributionsprocessen för varje miljö.

    Välj ikonen i den **åtgärd** för ett godkännande av före eller efter distributionen visas detaljerad information om vem som godkänna (eller avvisa) distributionen och meddelandet som anges av användaren.

2. När installationen är klar, visas hela loggfilen i den högra rutan. Välj någon av de **steg i processen** i den vänstra rutan att visa bara loggen innehållet för steget. Detta gör det enklare att spåra och felsöka enskilda delar av den övergripande distributionen. Du kan också hämta individuella loggfiler eller en zip alla loggfiler från ikoner och länkar på sidan.

    ![Alternativ Text](media\azure-stack-solution-hybrid-pipeline\203.png)
 
3. Öppna den **sammanfattning** fliken för att se övergripande information om versionen. Den visar information om att bygga och miljöer som det distribuerades till – tillsammans med distributionens status och annan information om versionen.

4. Markera den **miljö länkar** att se mer information om befintliga och väntande distributioner till den specifika miljön. Du kan använda dessa sidor för att verifiera att samma version har distribuerats till båda miljöerna.

5. Öppna den **distribueras produktionsprogrammet** i din Bläddra. Till exempel för en webbplats för Azure App Service, från URL: en `http://[your-app-name].azurewebsites.net`.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure Cloud mönster i [designmönster](https://docs.microsoft.com/azure/architecture/patterns).
