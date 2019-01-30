---
title: Skapa molnöverskridande skalning lösningar med Azure | Microsoft Docs
description: Lär dig hur du skapar molnöverskridande skalning lösningar med Azure.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: a8c4ef5df586c87862ea8e1634e9a72356401d0b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247433"
---
# <a name="tutorial-create-cross-cloud-scaling-solutions-with-azure"></a>Självstudier: Skapa molnöverskridande skalning lösningar med Azure

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig hur du skapar en molnöverskridande lösning för att tillhandahålla en manuellt utlösta process för att växla mellan ett Azure Stack web Apps som värd, värd till en Azure webbapp med automatisk skalning via traffic manager, säkerställer flexibel och skalbar molnlagring verktyget när du är under belastning.

Med det här mönstret kanske inte din klient är redo att köra ditt program i det offentliga molnet. Men kanske det inte är ekonomiskt möjligt för företag att underhålla den kapacitet som krävs i sina lokala miljöer för att hantera toppar i efterfrågan på appen. Din klient kan ta utnyttja elasticiteten hos det offentliga molnet med sin lokala lösning.

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> - Skapa ett webbprogram med flera noder.
> - Konfigurera och hantera kontinuerlig distribution (CD).
> - Publicera webbappen i Azure Stack.
> - Skapa en version.
> - Lär dig att övervaka och spåra dina distributioner.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack är en utökning av Azure. Azure Stack får du flexibilitet och utvecklingsmöjligheterna med molnberäkning i din lokala miljö och aktivera det enda hybridmolnet som hjälper dig att skapa och distribuera hybridappar var som helst.  
> 
> Faktabladet [designöverväganden för Hybridtillämpningar](https://aka.ms/hybrid-cloud-applications-pillars) granskar grundpelare för programkvalitet (placering, skalbarhet, tillgänglighet, återhämtning, hantering och säkerhet) för att utforma, distribuera och driva hybridprogram. Designöverväganden kan optimera hybrid programdesign, minimerar utmaningar i produktionsmiljöer.

## <a name="prerequisites"></a>Förutsättningar

-   En Azure-prenumeration. Om det behövs skapar en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Ett integrerat Azure Stack-System eller distribution av Azure Stack Development Kit.
    - Du hittar anvisningar för att installera Azure Stack på [installera Azure Stack Development Kit](../asdk/asdk-install.md).
    - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Den här installationen kan kräva ett par timmar att slutföra.

-   Distribuera [Apptjänst](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) PaaS-tjänster till Azure Stack.

-   [Skapa Plan/erbjudanden](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) i Azure Stack-miljön.

-   [Skapa klientprenumeration](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) i Azure Stack-miljön.

-   Skapa en Webbapp i klientprenumeration. Anteckna den nya Web App-URL för att använda senare.

-   Distribuera virtuell dator i Azure Pipelines i klientprenumeration.

-   Windows Server 2016-dator med .NET 3.5 krävs. Den här virtuella datorn skapas i klientprenumeration på Azure Stack som privat skapandeagent.

-   [Windows Server 2016 med SQL 2017 VM-avbildning](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image#add-a-vm-image-through-the-portal) är tillgängligt i Azure Stack Marketplace. Om den här bilden inte är tillgänglig och fungerar med en Azure Stack-operatör att se till att den har lagts till i miljön.

## <a name="issues-and-considerations"></a>Problem och överväganden

### <a name="scalability-considerations"></a>Skalbarhetsöverväganden

Viktig komponent i molnöverskridande skalning är möjligheten att leverera omedelbara och på begäran skalning mellan offentliga och lokala moln-infrastruktur, bevisa sin skicklighet konsekvent och tillförlitlig tjänst som föreskrivs av begäran.

### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Kontrollera lokalt distribuerade appar har konfigurerats för hög tillgänglighet via en lokal maskinvara konfiguration och distribution av programvara.

### <a name="manageability-considerations"></a>Överväganden för hantering

Molnöverskridande lösningen säkerställer sömlös hantering och välbekanta gränssnitt mellan miljöer. PowerShell rekommenderas för hantering av flera plattformar.

## <a name="cross-cloud-scaling"></a>Molnöverskridande skalning

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Hämta en anpassad domän och konfigurera DNS

Uppdatera DNS-zonfilen för domänen. Azure AD ska verifiera ägarskapet för det anpassade domännamnet. Använd [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) för Azure/Office 365/extern DNS-poster i Azure, eller lägga till DNS-posten på [en annan DNS-registrator](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1.  Registrera en anpassad domän med en offentlig registrator.

2.  Logga in hos domännamnsregistratorn för domänen. Administratör godkända kan krävas för att göra DNS-uppdateringar. 

3.  Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som tillhandahålls av Azure AD. (DNS-posten påverkar inte e-postdirigering eller webbvärdtjänster beteenden.) 

### <a name="create-a-default-multi-node-web-app-in-azure-stack"></a>Skapa en standard multinods-webbapp i Azure Stack

Konfigurera hybrid kontinuerlig integrering och kontinuerlig distribution (CI/CD) för att distribuera Webbapp till Azure och Azure Stack och automatiskt skicka ändringar till båda molnen.

> [!Note]  
> Azure Stack med rätt avbildningar syndikeras kör (Windows Server och SQL) och App Service-distributionen måste anges. Granska dokumentation om App Service ”[innan du sätter igång med App Service i Azure Stack](../azure-stack-app-service-before-you-get-started.md)” för Azure Stack-operatör.

### <a name="add-code-to-azure-repos"></a>Lägg till kod i Azure-lagringsplatser

Azure Repos

1. Logga in på Azure-databaser med ett konto som har rättigheter för projekt skapas på Azure-databaser.

    Hybrid CI/CD kan använda för både programkoden och infrastrukturkod. Använd [Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/) för både privata och värdbaserade molnutveckling.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image1.JPG)

2. **Klona lagringsplatsen** genom att skapa och öppna standard-webbapp.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image2.png)

### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Skapa distribution av fristående webbappar för App Services i både moln

1.  Redigera den **WebApplication.csproj** fil. Välj **Runtimeidentifier** och Lägg till **win10 x64**. (Se [Self-contained distribution](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentation.) 

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image3.png)

2.  Checka in kod till Azure-centrallager med Team Explorer.

3.  Bekräfta att programkoden har checkats in Azure-databaser.

## <a name="create-the-build-definition"></a>Skapa build-definition

1. Logga in på Azure-Pipelines för att bekräfta möjligheten att skapa skapa definitioner.

2. Lägg till **- r win10-x64** kod. Detta är nödvändigt att utlösa en självständig distribution med .net Core.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image4.png)

3. Kör versionen. Den [självständigt distributionsbuild](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processen ska publicera artefakter som kan köras på Azure och Azure Stack.

## <a name="use-an-azure-hosted-agent"></a>Använd Azure som värd agent

Använd en värdbaserad agent i Azure Pipelines är ett praktiskt alternativ att bygga och distribuera webbappar. Underhåll och uppgraderingar utförs automatiskt av Microsoft Azure, så att du kontinuerliga, oavbruten utveckling, testning och distribution.

### <a name="manage-and-configure-the-cd-process"></a>Hantera och konfigurera CD-processen

Pipelines och Azure DevOps-Server för Azure ger en mycket konfigurerbar och hanterbara pipeline för versioner till flera miljöer, till exempel utveckling, mellanlagring, QA och produktionsmiljöer; inklusive att kräva godkännanden på specifika steg.

## <a name="create-release-definition"></a>Skapa versionsdefinition

![Alternativ text](media/azure-stack-solution-cloud-burst/image5.png)

1.  Välj den **plus** för att lägga till en ny version under den **versioner fliken** på sidan Skapa och släpp i VSO.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image6.png)

2. Tillämpa Distributionsmall för Azure App Service.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image7.png)

3. Lägg till artefakt för Azure-molnet build appen under Lägg till artefakt.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image8.png)

4. Under fliken Pipeline, väljer du den **fas, uppgift** länka av miljön och ange miljövärden för för Azure-molnet.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image9.png)

5. Ange den **miljönamn** och välj Azure **prenumeration** för Azure Cloud-slutpunkten.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image10.png)

6. Ange de nödvändiga under miljönamn, **Azure app service-namn**.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image11.png)

7. Ange **finns VS2017** under agenten kö för Azure värdbaserad molnmiljö.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image12.png)

8. Distribuera Azure App Service-menyn väljer du det giltiga **paket eller mappen** för miljön. Välj **OK** till **mapplatsen**.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image13.png)

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image14.png)

9. Spara alla ändringar och gå tillbaka till **releasepipeline**.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image15.png)

10. Lägg till en ny artefakt som att välja build för Azure Stack-appen.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image16.png)

11. Lägg till en mer miljö som använder Azure App Service-distribution.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image17.png)

12. Namnge den nya miljön Azure Stack.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image18.png)

13. Hitta Azure Stack-miljön under **uppgift** fliken.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image19.png)

14. Välj prenumerationen för Azure Stack-slutpunkten.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image20.png)

15. Ange namnet på Azure Stack webbappen som App service-namn.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image21.png)

16. Välj Azure Stack-agent.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image22.png)

17. Under distribuera Azure App Service-avsnittet väljer giltiga **paket eller mappen** för miljön. Välj **OK** till mappen.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image23.png)

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image24.png)

18. Lägg till en variabel med namnet under varierande fliken `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, ange värdet som **SANT**, och omfång i Azure Stack.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image25.png)

19. Välj den **kontinuerlig** utlösaren ikon för distribution i både artefakter och aktivera den **fortsätter** distributionsutlösare.

    ![Alternativ text](media/azure-stack-solution-cloud-burst/image26.png)

20. Välj den **före** villkor ikon i Azure Stack-miljön och ange utlösaren som **när utgåvan blir tillgänglig.**

21. Spara alla ändringar.

> [!Note]  
> Vissa inställningar för uppgifter kan ha automatiskt definierats som [miljövariabler](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) när du skapar en versionsdefinition från en mall. De här inställningarna kan inte ändras i Aktivitetsinställningarna; i stället måste miljöobjekt överordnade väljas för att redigera inställningarna

## <a name="publish-to-azure-stack-via-visual-studio"></a>Publicera till Azure Stack via Visual Studio

Genom att skapa slutpunkter kan distribuera en version av Visual Studio Online (VSTO) Azure Service-appar till Azure Stack. Azure Pipelines ansluter till build-agenten, som ansluter till Azure Stack.

1.  Logga in på VSTO och gå till inställningssidan.

2.  På **inställningar**väljer **Security**.

3.  I **VSTS grupper**väljer **Endpoint skapare**.

4.  På den **medlemmar** fliken **Lägg till**.

5.  I **lägga till användare och grupper**, ange ett användarnamn och Välj användaren i listan med användare.

6.  Välj **Spara ändringar**.

7.  I den **VSTS grupper** väljer **Endpoint administratörer**.

8.  På den **medlemmar** fliken **Lägg till**.

9.  I **lägga till användare och grupper**, ange ett användarnamn och Välj användaren i listan med användare.

10. Välj **Spara ändringar**.

Nu när information om slutpunkten finns är Pipelines Azure till Azure Stack-anslutningen klar att användas. Build-agenten i Azure Stack får anvisningar från Azure Pipelines och agenten meddelar sedan slutpunktsinformation för kommunikation med Azure Stack.

## <a name="develop-the-application-build"></a>Utveckla program build

> [!Note]  
> Azure Stack med rätt avbildningar syndikeras kör (Windows Server och SQL) och App Service-distributionen måste anges. Granska dokumentation om App Service ”[innan du sätter igång med App Service i Azure Stack](../azure-stack-app-service-before-you-get-started.md)” för Azure Stack-operatör.

Använd [Azure Resource Manager-mallar som web](https://azure.microsoft.com/resources/templates/) kod från Azure-databaser för att distribuera till båda molnen.

### <a name="add-code-to-a-azure-repos-project"></a>Lägg till kod i ett projekt i Azure-lagringsplatser

1.  Logga in på Azure-databaser med ett konto som har behörighet för projektet skapas på Azure Stack. Nästa skärmdump visar hur du ansluter till HybridCICD-projektet.

2.  **Klona lagringsplatsen** genom att skapa och öppna standard-webbapp.

#### <a name="create-self-contained-web-app-deployment-for-app-services-in-both-clouds"></a>Skapa distribution av fristående webbappar för App Services i både moln

1.  Redigera den **WebApplication.csproj** fil: Välj **Runtimeidentifier** och Lägg sedan till win10 x64. Mer information finns i [självständigt distribution](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentation.

2.  Använd Team Explorer för att kontrollera koden i Azure-databaser.

3.  Bekräfta att programkoden checkades in Azure-databaser.

### <a name="create-the-build-definition"></a>Skapa build-definition

1.  Logga in på Azure-Pipelines med ett konto som kan skapa en byggesdefinition.

2.  Navigera till den **skapa webbprogram** sidan för projektet.

3.  I **argument**, lägga till **- r win10-x64** kod. Detta krävs för att utlösa en självständig distribution med .net Core.

4.  Kör versionen. Den [självständigt distributionsbuild](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processen ska publicera artefakter som kan köras på Azure och Azure Stack.

#### <a name="use-an-azure-hosted-build-agent"></a>Använd Azure som värd skapandeagent

Med hjälp av en värdbaserad skapandeagent i Pipelines för Azure är ett praktiskt alternativ för att skapa och distribuera webbappar. Agentunderhåll och uppgraderingar utförs automatiskt av Microsoft Azure, vilket gör det möjligt för en kontinuerlig och oavbruten utvecklingscykeln.

### <a name="configure-the-continuous-deployment-cd-process"></a>Konfigurera kontinuerlig distribution (CD)-processen

Azure Pipelines och Azure DevOps-servern ger en mycket konfigurerbar och hanterbara pipeline för versioner till flera miljöer, till exempel utveckling, mellanlagring, kvalitetssäkring (kvalitetskontroll) och produktion. Den här processen kan inkludera att kräva godkännanden i specifika led i programmets hela livscykel.

#### <a name="create-release-definition"></a>Skapa versionsdefinition

Skapa en versionsdefinition är det sista steget i programmet genereringsprocessen. Den här versionsdefinition används för att skapa en version och distribuera en version.

1.  Logga in på Azure Pipelines och gå till **Build and Release** för projektet.

2.  På den **versioner** fliken **[+]** och välj **skapa versionsdefinition**.

3.  På **Välj en mall**, Välj **Azure App Service-distribution**, och välj sedan **tillämpa**.

4.  På **Lägg till artefakt**, från den **källa (byggesdefinition)** väljer du den Azure-molnet build-app.

5.  På den **Pipeline** fliken den **1 fas**, **1 aktivitet** länka till **visa miljöuppgifter**.

6.  På den **uppgifter** ange Azure som den **miljönamn** och välj AzureCloud Traders Web EP från den **Azure-prenumeration** lista.

7.  Ange den **Azure app service-namn**, vilket är `northwindtraders` i nästa skärmdumpen.

8.  Agent-fasen Välj **finns VS2017** från den **fronta agenta** lista.

9.  I **distribuera Azure App Service**, Välj det giltiga **paket eller mappen** för miljön.

10. I **Välj fil eller mapp**väljer **OK** till **plats**.

11. Spara alla ändringar och gå tillbaka till **Pipeline**.

12. På den **Pipeline** fliken **Lägg till artefakt**, och välj den **NorthwindCloud Traders-fartyg** från den **källa (Build-Definition)** lista.

13. På **Välj en mall**, lägga till en annan miljö. Välj **Azure App Service-distribution** och välj sedan **tillämpa**.

14. Ange `Azure Stack` som den **miljönamn**.

15. På den **uppgifter** fliken, hitta och välj Azure Stack.

16. Från den **Azure-prenumeration** väljer **AzureStack Traders fartyg EP** för Azure Stack-slutpunkten.

17. Ange Azure Stack webbappens namn som den **App service-namn**.

18. Under **val av**, Välj **AzureStack -b Douglas filnamnsdelen** från den **fronta agenta** lista.

19. För **distribuera Azure App Service**, Välj det giltiga **paket eller mappen** för miljön. På **Välj fil eller mapp**väljer **OK** för mappen **plats**.

20. På den **variabeln** fliken, hitta variabeln med namnet `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`. Ange variabelvärdet **SANT**, och ange dess räckvidd till **Azure Stack**.

21. På den **Pipeline** fliken den **utlösare av kontinuerlig distribution** ikon för NorthwindCloud Traders-Web-artefakt och ange den **utlösare av kontinuerlig distribution** till **Aktiverat**. Gör samma sak den **NorthwindCloud Traders-fartyg** artefakten.

22. Azure Stack-miljön, Välj den **före villkor** ikonen Ange utlösaren **efter**.

23. Spara alla ändringar.

> [!Note]  
> Vissa inställningar för publiceringsuppgifter definieras automatiskt som [miljövariabler](https://docs.microsoft.com/azure/devops/pipelines/release/variables?view=vsts&tabs=batch#custom-variables) när du skapar en versionsdefinition från en mall. De här inställningarna kan inte ändras i Aktivitetsinställningarna, men kan ändras i de överordnade objekten för miljön.

## <a name="create-a-release"></a>Skapa en version

1.  På den **Pipeline** fliken, öppna den **viktig** listan och välj **skapa version**.

2.  Ange en beskrivning för versionen, kontrollera att rätt artefakter är markerade och välj sedan **skapa**. Efter en liten stund visas en banderoll som anger att den nya versionen skapades versionen visas som en länk. Välj länken för att se versionen sammanfattningssidan.

3.  Versionen sammanfattningssidan för visar information om versionen. I följande skärmdump för ”Release-2”, den **miljöer** avsnittet visar den **Distributionsstatus** för Azure som ”pågående” och status för Azure Stack är ”SLUTFÖRT”. När distributionsstatusen för Azure-miljön ändras till ”SUCCEEDED”, visas ett popup-meddelande som anger att versionen är klara för godkännande. När en distribution väntar eller har misslyckats, en blå **(i)** informationsikon som visas. Hovra över ikonen så visas ett popup-fönster som innehåller dröjsmål eller underlåtenhet orsaken.

4.  Andra vyer, till exempel listan över versioner också visa en ikon som indikerar godkännande är väntande. Popup-fönstret för den här ikonen visas miljönamnet och mer information om distribution. Det är enkelt för en administratör, se det övergripande förloppet för versioner och se vilka versioner väntar på godkännande.

## <a name="monitor-and-track-deployments"></a>Övervaka och spåra distributioner

1.  På den **Release 2** sammanfattningssidan, väljer **loggar**. Den här sidan visas under distributionen av en live loggen från agenten. Den vänstra rutan visar status för varje åtgärd i distributionen för varje miljö.

2.  Välj en person ikon i den **åtgärd** kolumnen för en före eller efter distributionen godkännande som godkände (eller avvisade) distributionen, och de angivna meddelandet.

3.  När distributionen är klar visas hela loggfilen i den högra rutan. Välj valfri **steg** i det vänstra fönstret för att se loggfilen för ett enda steg som **initieras jobbet**. Kan visa enskilda loggar gör det enklare att spåra och Felsök delar av den övergripande distributionen. **Spara** loggfilen för ett steg eller **hämta alla loggar som zip**.

4.  Öppna den **sammanfattning** fliken för att se allmän information om versionen. Den här vyn visar information om versionen, de miljöer som det distribuerades till, Distributionsstatus och annan information om versionen.

5.  Välj en miljö-länk (**Azure** eller **Azure Stack**) att se information om befintliga och väntande distributioner till en viss miljö. Använd dessa vyer som ett snabbt sätt att kontrollera att samma version har distribuerats till båda miljöerna.

6.  Öppna den **distribueras produktionsapp** i webbläsaren. Till exempel öppna URL: en för webbplatsen Azure App Services [http://[your app name\]. azurewebsites.net](http:// [your-app-name].azurewebsites.net).

**Integrering av Azure och Azure Stack ger en skalbar lösning i molnet**

En flexibel och stabil flera molnbaserad tjänst tillhandahåller säkerhets-data, tillbaka upp och redundans, snabb och konsekvent tillgänglighet, skalbar lagring och distribution och geo-kompatibla routning. Den här manuellt utlösta processen säkerställer pålitliga och effektiva belastningen växlar mellan värd för webbappar, att se till att omedelbar tillgänglighet av viktiga data. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).
