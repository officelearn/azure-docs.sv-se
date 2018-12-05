---
title: Skapa en applösning för geo-distribuerad med Azure och Azure Stack | Microsoft Docs
description: Lär dig hur du skapar en applösning för geo-distribuerad med Azure och Azure Stack.
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
ms.date: 09/24/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 215cc45f09e15c74a39347e3a62945b45eafa130
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877674"
---
# <a name="tutorial-create-a-geo-distributed-app-solution-with-azure-and-azure-stack"></a>Självstudier: Skapa en applösning för geo-distribuerad med Azure och Azure Stack

*Gäller för: integrerade Azure Stack-system och Azure Stack Development Kit*

Lär dig att dirigera trafik till specifika slutpunkter baserat på olika mått med hjälp av geo-distribuerad appar mönstret. Skapa en Traffic Manager garanterar-profil med geografiska-baserad Routning och slutpunkten konfiguration information dirigeras till slutpunkterna som baseras på regionala krav, företagets och internationella förordning och dina databehov.

I den här självstudien skapar du en exempel-miljö för att:

> [!div class="checklist"]
> - Skapa en Geo-distribuerad App.
> - Använd Traffic Manager för att fokusera på din app.

## <a name="use-the-geo-distributed-apps-pattern"></a>Använd mönstret geo-distribuerad appar

Med mönstret geo-distribuerad kan din app sträcker sig över regioner. Du kan som standard det offentliga molnet, men vissa av användarna kan kräva att deras data förblir i regionen. Du kan dirigera användare till det lämpligaste molnet utifrån deras behov.

### <a name="issues-and-considerations"></a>Problem och överväganden

#### <a name="scalability-considerations"></a>Skalbarhetsöverväganden

Lösningen skapar du med den här självstudien är inte för skalbarhet. Om används i kombination med andra Azure och lokala tekniker och lösningar kan du hantera skalbarhetskraven. Information om hur du skapar en lösning för hyrbid med automatisk skalning via traffic manager finns i [skapa molnöverskridande skalning lösningar med Azure](azure-stack-solution-cloud-burst.md).

#### <a name="availability-considerations"></a>Överväganden för tillgänglighet

Så är fallet med överväganden angående skalbarhet, upp inte den här lösningen direkt tillgänglighet. Men även liknar våra skalbarhetsöverväganden, Azure, och lokala tekniker och lösningar kan implementeras i den här lösningen för att säkerställa hög tillgänglighet för alla komponenter som ingår.

### <a name="when-to-use-this-pattern"></a>När du ska använda det här mönstret

- Din organisation har internationella grenar som kräver anpassade regionala säkerhet och principer för distribution.

- Var och en av dina organisationer kontor hämtar för anställda, företag och anläggning data, som kräver reporting aktivitet per lokala föreskrifter och tidszon.

- Hög skala kraven kan uppfyllas genom att vågrätt skala ut appar, med flera appdistributioner görs inom en enda region, samt i regioner, för att hantera extrem belastning.

### <a name="planning-the-topology"></a>Planera topologin

Innan du bygga ut en distribuerad app-fotavtryck hjälper det för att ha följande kunskaper:

-   **Anpassad domän för appen:** vad är det anpassade domännamnet som kunder använder för att få åtkomst till appen? Exempelappen, det anpassade domännamnet är *www.scalableasedemo.com.*

-   **Traffic Manager-domän:** ett domännamn måste väljas när du skapar en [Azure Traffic Manager-profil](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-manage-profiles). Det här namnet kommer att kombineras med den *trafficmanager.net* suffix för att registrera en post för domänen som hanteras av Traffic Manager. Valt namnet är för exempelappen, *skalbar ase demo*. Därför är det fullständiga domännamnet som hanteras av Traffic Manager *skalbar ase demo.trafficmanager.net*.

-   **Strategi för att skala app-fotavtryck:** kommer fotavtryck för programmet att distribueras på flera App Service-miljöer i en enda region? Flera regioner? En blandning av båda metoderna? Beslutet bör baseras på förväntningar som kundtrafiken kommer kommer från, samt hur väl resten av en app stöd för backend-infrastruktur kan skala. Till exempel med ett 100% tillståndslösa program, kan en app massivt skalas med hjälp av en kombination av flera App Service-miljöer per Azure-region, multiplicerat med App Service-miljöer som distribueras över flera Azure-regioner. Kunder med 15 + globala Azure-regioner kan välja mellan, kan verkligen skapa ett världsomspännande storskaliga program-fotavtryck. För exempelapp som används för den här artikeln, har tre App Service-miljöer skapats i en enda Azure-region (södra centrala USA).

-   **Namnkonvention för App Service-miljöer:** varje App Service Environment krävs ett unikt namn. Utöver en eller två App Service-miljöer är det bra att ha en namngivningskonvention för att identifiera varje App Service Environment. En enkel namngivningskonvention för exempelappen har använts. Namnen på de tre App Service-miljöer är *fe1ase*, *fe2ase*, och *fe3ase*.

-   **Namnkonventionen för apparna:** eftersom flera instanser av appen ska distribueras, ett namn krävs för varje instans av den distribuerade appen. Med App Service-miljöer kan samma appnamn användas över flera App Service-miljöer. Eftersom varje App Service Environment har ett unikt domänsuffix, kan utvecklare du återanvända exakt samma appnamn i varje miljö. Utvecklare kan till exempel ha appar som namnges enligt följande: *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net*osv. Varje app-instans har ett unikt namn för appen i det här scenariot. App-instansnamn som används är *webfrontend1*, *webfrontend2*, och *webfrontend3*.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack är en utökning av Azure. Azure Stack får du flexibilitet och utvecklingsmöjligheterna med molnberäkning i din lokala miljö och aktivera det enda hybridmolnet som hjälper dig att skapa och distribuera hybridappar var som helst.  
> 
> Faktabladet [designöverväganden för Hybridtillämpningar](https://aka.ms/hybrid-cloud-applications-pillars) granskar grundpelare för programkvalitet (placering, skalbarhet, tillgänglighet, återhämtning, hantering och säkerhet) för att utforma, distribuera och driva hybridprogram. Designöverväganden kan optimera hybrid programdesign, minimerar utmaningar i produktionsmiljöer.

## <a name="part-1-create-a-geo-distributed-app"></a>Del 1: Skapa en geo-distribuerad app

I den här delen kommer du skapa en webbapp.

> [!div class="checklist"]
> - Skapa webbappar och publicera
> - Lägg till kod i Azure-lagringsplatser
> - Peka flera mål i cloud app-versionen.
> - Hantera och konfigurera CD-processen

### <a name="prerequisites"></a>Förutsättningar

En Azure-prenumeration och Azure Stack-installation krävs.

### <a name="geo-distributed-app-steps"></a>GEO-distribuerad app steg

### <a name="obtain-a-custom-domain-and-configure-dns"></a>Hämta en anpassad domän och konfigurera DNS

Uppdatera DNS-zon filen foCreate webbappar och publishr domänen. Azure AD kan sedan Verifiera ägarskapet för det anpassade domännamnet. Använd [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) för Azure/Office 365/extern DNS-poster i Azure, eller lägga till DNS-posten på [en annan DNS-registrator](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

1. Registrera en anpassad domän med en offentlig registrator.

2. Logga in hos domännamnsregistratorn för domänen. Administratör godkända kan krävas för att göra DNS-uppdateringar.

3. Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som tillhandahålls av Azure AD. DNS-posten ändrar beteenden, till exempel e-postdirigering eller webbhosting.

### <a name="create-web-apps-and-publish"></a>Skapa webbappar och publicera

Konfigurera hybrid CI/CD för att distribuera Webbapp till Azure och Azure Stack och automatiskt skicka ändringar till båda molnen.

> [!Note]  
> Azure Stack med rätt avbildningar syndikeras kör (Windows Server och SQL) och App Service-distributionen måste anges. Granska dokumentation om App Service ”[innan du sätter igång med App Service i Azure Stack](/articles/azure-stack/azure-stack-app-service-before-you-get-started)” för Azure Stack-operatör.

#### <a name="add-code-to-azure-repos"></a>Lägg till kod i Azure-lagringsplatser

1. Logga in på Visual Studio med en **konto som har behörighet för skapande av projekt** på Azure-databaser.

    Hybrid kontinuerlig integrering/kontinuerlig leverans (CI/CD) kan använda för både programkoden och infrastrukturkod. Använd [Azure Resource Manager-mallar](https://azure.microsoft.com/resources/templates/) för både privata och värdbaserade molnutveckling.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image1.JPG)

2. **Klona lagringsplatsen** genom att skapa och öppna standard-webbapp.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image2.png)

### <a name="create-web-app-deployment-in-both-clouds"></a>Skapa distribution av webbappar i både moln

1.  Redigera den **WebApplication.csproj** fil: Välj **Runtimeidentifier** och Lägg till **win10 x64**. (Se [Self-contained distribution](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) dokumentation.)

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image3.png)

1.  **Checka in kod till Azure-lagringsplatser** med Team Explorer.

2.  Bekräfta att den **programkod** har checkats in Azure-databaser.

### <a name="create-the-build-definition"></a>Skapa build-definition

1. **Logga in på Azure Pipelines** att bekräfta möjligheten att skapa byggdefinitioner.

2. Lägg till **- r win10-x64** kod. Detta är nödvändigt att utlösa en självständig distribution med .net Core.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image4.png)

3. **Kör versionen**. Den [självständigt distributionsbuild](https://docs.microsoft.com/dotnet/core/deploying/#self-contained-deployments-scd) processen ska publicera artefakter som kan köras på Azure och Azure Stack.

**Med hjälp av en Azure-värdbaserade Agent**

Med hjälp av en värdbaserad agent i Azure Pipelines är ett praktiskt alternativ att bygga och distribuera webbappar. Underhåll och uppgraderingar utförs automatiskt av Microsoft Azure, så att du kontinuerliga, oavbruten utveckling, testning och distribution.

### <a name="manage-and-configure-the-cd-process"></a>Hantera och konfigurera CD-processen

Azure DevOps och Azure DevOps-servern ger en mycket konfigurerbar och hanterbara pipeline för versioner till flera miljöer, till exempel utveckling, mellanlagring, QA och produktionsmiljöer; inklusive att kräva godkännanden på specifika steg.

#### <a name="create-release-definition"></a>Skapa versionsdefinition


![Alternativ text](media\azure-stack-solution-geo-distributed\image5.png)

1.  Välj den **plus** för att lägga till en ny version under den **versioner fliken** på sidan Skapa och släpp av Visual Studio Online (VSO).

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image6.png)

2. Tillämpa den **Azure App Service-distribution** mall.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image7.png)

3. Lägg till artefakt nedrullningsbara menyn **lägga till artefakten** för Azure build molnappen.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image8.png)

4. Under fliken Pipeline, väljer du den **fas, uppgift** länka av miljön och ange miljövärden för för Azure-molnet.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image9.png)

5. Ange den **miljönamn** och välj Azure **prenumeration** för Azure Cloud-slutpunkten.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image10.png)

6. Ange de nödvändiga under miljönamn, **Azure app service-namn**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image11.png)

7. Ange **finns VS2017** under agenten kö för Azure värdbaserad molnmiljö.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image12.png)

8. Distribuera Azure App Service-menyn väljer du det giltiga **paket eller mappen** för miljön. Välj OK om du vill **mapplatsen**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image13.png)

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image14.png)

9. Spara alla ändringar och gå tillbaka till **releasepipeline**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image15.png)

10. Lägg till en **nya artefakten** att välja build för Azure Stack-appen.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image16.png)

11. Lägg till en mer miljö tillämpa den **Azure App Service-distribution.**

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image17.png)

12. Namnge den nya miljön **Azure Stack.**

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image18.png)

13. Hitta Azure Stack-miljön under **uppgift** fliken.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image19.png)

14. Välj den **prenumeration** för Azure Stack-slutpunkten.

  ![Alternativ text](media\azure-stack-solution-geo-distributed\image20.png)

15. Ange Azure Stack webbappens namn som den **App service-namn**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image21.png)

16. Välj den **Azure Stack-agenten**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image22.png)

17. Under distribuera Azure App Service-avsnittet väljer giltiga **paket eller mappen** för miljön. Välj OK om du vill **mapplatsen**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image23.png)

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image24.png)

18. Under **variabeln** fliken Lägg till en variabel med namnet `VSTS\_ARM\_REST\_IGNORE\_SSL\_ERRORS`, ange värdet som `true`, och begränsa omfånget till `Azure Stack`.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image25.png)

19. Välj den **kontinuerlig** utlösaren ikon för distribution i både artefakter och aktivera den **fortsätter** distributionsutlösare.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image26.png)

20. Välj den **före** villkor ikon i Azure Stack-miljön och ange utlösaren som **när utgåvan blir tillgänglig.**

21. Spara alla ändringar.

> [!Note]  
>  Vissa inställningar för uppgifter kan ha automatiskt definierats som [miljövariabler](https://docs.microsoft.com/vsts/build-release/concepts/definitions/release/variables?view=vsts#custom-variables) när du skapade en versionsdefinition från en mall. De här inställningarna kan inte ändras i Aktivitetsinställningarna; i stället måste du välja den överordnade miljö artikeln att redigera inställningarna.

## <a name="part-2-update-web-app-options"></a>Del 2: Uppdateringsalternativ web app

Med [Azure Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) får du en mycket skalbar och automatiskt uppdaterad webbvärdtjänst. 

![Alternativ text](media\azure-stack-solution-geo-distributed\image27.png)

> [!div class="checklist"]
> - Mappa ett befintligt anpassat DNS-namn till Azure Web Apps
> - Använd en ** CNAME recorder en **en post** att mappa ett anpassat DNS-namn till App Service.

### <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>Mappa ett befintligt anpassat DNS-namn till Azure Web Apps

> [!Note]  
>  Använd en CNAME-post för alla anpassade DNS-namn utom en rotdomän (för example,northwind.com).

Om du vill migrera en live-webbplats och dess DNS-domännamn till App Service kan du läsa [Migrera ett aktivt DNS-namn till Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-custom-domain-name-migrate).

### <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

-   [Skapa en App Service-app](https://docs.microsoft.com/azure/app-service/), eller Använd en app som skapats för en annan självstudie.

-   Köp ett domännamn och kontrollera åtkomst till DNS-registret för domänleverantören.

Uppdatera DNS-zonfilen för domänen. Azure AD ska verifiera ägarskapet för det anpassade domännamnet. Använd [Azure DNS](https://docs.microsoft.com/azure/dns/dns-getstarted-portal) för Azure/Office 365/extern DNS-poster i Azure, eller lägga till DNS-posten på [en annan DNS-registrator](https://support.office.com/article/Create-DNS-records-for-Office-365-when-you-manage-your-DNS-records-b0f3fdca-8a80-4e8e-9ef3-61e8a2a9ab23/).

-   Registrera en anpassad domän med en offentlig registrator.

-   Logga in hos domännamnsregistratorn för domänen. (Administratör godkända kan krävas att göra DNS-uppdateringar.)

-   Uppdatera DNS-zonfilen för domänen genom att lägga till DNS-posten som tillhandahålls av Azure AD.

Till exempel om du vill lägga till DNS-poster fornorthwindcloud.comand www.northwindcloud.com, konfigurera DNS-inställningarna för rotdomänen för thenorthwindcloud.com.

> [!Note]  
>  Ett domännamn kan köpas med hjälp av den [Azure-portalen](https://docs.microsoft.com/azure/app-service/custom-dns-web-site-buydomains-web-app).  
> För att kunna mappa ett anpassat DNS-namn till en webbapp måste webbappens [App Service-plan](https://azure.microsoft.com/pricing/details/app-service/) vara en betalplan (**Delad**, **Basic**, **Standard** eller **Premium**).



### <a name="create-and-map-cname-and-a-records"></a>Skapa och mappa CNAME- och A-poster

#### <a name="access-dns-records-with-domain-provider"></a>Använda DNS-poster med domänleverantör

> [!Note]  
>  Använda Azure DNS för att konfigurera ett anpassat DNS-namn för Azure Web Apps. Mer information finns i [Använda Azure DNS för att skapa inställningar för anpassad domän för en Azure-tjänst](https://docs.microsoft.com/azure/dns/dns-custom-domain).

1.  Logga in på webbplatsen till den huvudsakliga providern.

2.  Sök upp sidan för hantering av DNS-poster. Leverantören för varje domän har sin egen DNS-postgränssnitt. Leta efter områden på webbplatsen med namnet **Domännamn**, **DNS**, eller **Namnserverhantering**.

DNS-poster kan visas i **Mina domäner**. Hitta länken **zonfil**, **DNS-poster**, eller **avancerad konfiguration**.

Skärmbilden nedan är ett exempel på en sida med DNS-poster:

![Exempelsida för DNS-poster](media\azure-stack-solution-geo-distributed\image28.png)

1.  Välj i Domännamnsregistrator **Lägg till eller skapa** att skapa en post. Vissa providrar har olika länkar för att lägga till olika posttyper. Läs leverantörens dokumentation.

2.  Lägg till en CNAME-post för att mappa en underdomän till appens Standardvärdnamn.

  Lägg till en CNAME-post som mappar namewwwto www.northwindcloud.comdomain exempelvis < app\_namn >. azurewebsites.net.

När du lagt till CNAME ser sidan för DNS-poster ut som i följande exempel:

![Portalnavigering till Azure-app](media\azure-stack-solution-geo-distributed\image29.png)

### <a name="enable-the-cname-record-mapping-in-azure"></a>Aktivera CNAME-postmappning i Azure

1.  I en ny flik loggar du in på Azure Portal

2.  Gå till App Services.

3.  Välj webbapp.

4.  Välj **Anpassade domäner** i det vänstra navigeringsfönstret på appsidan i Azure Portal.

5.  Välj ikonen **+** bredvid **Lägg till värddatornamn**.

1.  Ange det fullständigt kvalificerade domännamnet, till exempel `www.northwindcloud.com`.

2.  Välj **Verifiera**.

3.  Om anges, lägger du till ytterligare poster för andra typer (`A` eller `TXT`) domain name registratorer DNS-poster. Azure tillhandahåller värden och typer av dessa poster:

    a.  En **A**-post för att mappa till appens IP-adress.

    b.  En **TXT** post att mappa till appens Standardvärdnamn < app_name >. azurewebsites.net. App Service använder den här posten endast vid konfigurationen, för att verifiera ditt ägarskap anpassad domän. Ta bort TXT-posten när kontrollen är klar.

4.  Slutför den här uppgiften på fliken domän Registratorn och verifiera tills den **Lägg till värddatornamn** knappen aktiveras.

5.  Se till att ** posttyp för värddatornamn har angetts till **CNAME (www.example.com eller valfri underdomän)**.

6.  Välj **Lägg till värddatornamn**.

7.  Ange det fullständigt kvalificerade domännamnet, till exempel `northwindcloud.com`.

8.  Välj **Verifiera**.

9.  Den **Lägg till** har aktiverats.

10. Se till att ** posttyp för värddatornamn har angetts till **en post (example.com)**.

11. **Lägg till värddatornamn**.

  Det kan ta lite tid innan nytt värdnamnen återspeglas i appens **anpassade domäner** sidan. Försök att uppdatera webbläsaren så att informationen uppdateras.
  
  ![Alternativ text](media\azure-stack-solution-geo-distributed\image31.png) 
  
  Om det finns ett fel visas ett meddelande om verifieringen längst ned på sidan. ![Verifieringsfel](media\azure-stack-solution-geo-distributed\image32.png)

> [!Note]  
>  Stegen ovan kan upprepas för att mappa en domän med jokertecken (\*. northwindcloud.com)... På så sätt kan lägga till några ytterligare underdomäner till den här app service utan att behöva skapa en separat CNAME-post för var och en. Följ anvisningarna för Registratorn att konfigurera den här inställningen.

#### <a name="test-in-a-browser"></a>Testa i en webbläsare

Bläddra till DNS-namn som tidigare har konfigurerat (till exempel `northwindcloud.com`, www.northwindcloud.com.

## <a name="part-3-bind-a-custom-ssl-cert"></a>Del 3: Binda ett anpassat SSL-certifikat

I den här delen:

> [!div class="checklist"]
> - Binda anpassat SSL-certifikat till App Service
> - Använda HTTPS i appen
> - Automatisera SSL-certifikatsbindning med skript

> [!Note]  
> Om det behövs och hämta en kund SSL-certifikat i Azure portal och koppla det till webbappen. Följ [Självstudie för App Service Certificate](https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site).

### <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du:

-   [Skapa en App Service-app](https://docs.microsoft.com/azure/app-service/)
-   [Mappa ett anpassat DNS-namn till din webbapp](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain)
-   Skaffa ett SSL-certifikat från en betrodd certifikatutfärdare och använda nyckeln för att signera begäran

### <a name="requirements-for-your-ssl-certificate"></a>Krav för ditt SSL-certifikat

Om du vill använda ett certifikat i App Service måste certifikatet uppfylla alla följande krav:

-   Vara signerat av en betrodd certifikatutfärdare

-   Ha exporterats som en lösenordsskyddad PFX-fil

-   Innehålla en privat nyckel som är minst 2 048 bitar lång

-   Innehålla alla mellanliggande certifikat i certifikatkedjan

> [!Note]  
>  **Elliptic Curve Cryptography (ECC) certifikat** fungera med App Service, men ingår inte i den här guiden. Kontakta en certifikatutfärdare för att få hjälp när man skapar ECC-certifikat. 

#### <a name="prepare-the-web-app"></a>Förbereda webbappen

Binda ett anpassat SSL-certifikat till webbappen, den [apptjänstplan](https://azure.microsoft.com/pricing/details/app-service/) måste vara i den **grundläggande**, **Standard**, eller **Premium** nivå.

#### <a name="sign-in-to-azure"></a>Logga in på Azure

1.  Öppna den [Azure-portalen](https://portal.azure.com/) och navigera till webbappen.

2.  I den vänstra menyn, Välj **Apptjänster**, och välj sedan namnet på webbappen.

![Välja webbapp](media\azure-stack-solution-geo-distributed\image33.png)

#### <a name="check-the-pricing-tier"></a>Kontrollera prisnivån

1.  I det vänstra navigeringsfältet på sidan web app, bläddrar du till den **inställningar** och väljer **skala upp (App Service-plan)**.

    ![Skala upp-menyn](media\azure-stack-solution-geo-distributed\image34.png)

1.  Se till att webbappen inte är i den **kostnadsfri** eller **delad** nivå. Webbappens aktuella nivå markeras i en mörkblå ruta.

    ![Kontrollera prisnivå](media\azure-stack-solution-geo-distributed\image35.png)

Anpassat SSL stöds inte på nivån **Kostnadsfri** eller **Delad**. Följ stegen i nästa avsnitt om du vill skala upp eller **Välj din prisnivå** och hoppar till [ladda upp och binda SSL-certifikatet](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

#### <a name="scale-up-your-app-service-plan"></a>Skala upp App Service-planen

1.  Välj en av nivåerna **Basic**, **Standard** eller **Premium**.

2.  Välj **Välj**.

![Välja prisnivå](media\azure-stack-solution-geo-distributed\image36.png)

Åtgärden har slutförts när meddelandet visas.

![Uppskalningsmeddelande](media\azure-stack-solution-geo-distributed\image37.png)

#### <a name="bind-your-ssl-certificate-and-merge-intermediate-certificates"></a>Binda SSL-certifikat och sammanfoga mellanliggande certifikat

Sammanfoga flera certifikat i kedjan.

1. **Öppna varje certifikat** du fick i en textredigerare.

2. Skapa en fil för det sammanfogade certifikatet med namnet *mergedcertificate.crt*. I redigeringsprogrammet kopierar du innehållet i varje certifikat till den här filen. Ordningen på dina certifikat ska följa ordningen i certifikatkedjan, först med ditt certifikat och sist med rotcertifikatet. Det ser ut som i följande exempel:

    ```Text

    -----BEGIN CERTIFICATE-----

    <your entire Base64 encoded SSL certificate>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 1>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded intermediate certificate 2>

    -----END CERTIFICATE-----

    -----BEGIN CERTIFICATE-----

    <The entire Base64 encoded root certificate>

    -----END CERTIFICATE-----
    ```

#### <a name="export-certificate-to-pfx"></a>Exportera certifikat till PFX

Exportera sammanfogade SSL-certifikatet med den privata nyckeln genereras av certifikatet.

En fil för privat nyckel har skapats via OpenSSL. Om du vill exportera certifikatet till PFX, kör du följande kommando och ersätter platshållarna *< private-key-file >* och *< samman-certificate-file >* med privat nyckel sökvägar och kopplade certifikatfil.

```PowerShell
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

När du uppmanas, definierar du ett exportlösenord för att ladda upp SSL-certifikat till App Service senare.

När IIS eller **Certreq.exe** används för att generera certifikatbegäran, installerar du certifikatet till en lokal dator och sedan [exportera certifikatet till PFX](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

#### <a name="upload-the-ssl-certificate"></a>Ladda upp SSL-certifikatet

1.  Välj **SSL-inställningar** i den vänstra navigeringen i webbappen.

2.  Välj **överför certifikat**.

3.  I **PFX-certifikatsfil**väljer PFX-fil.

4.  4. I **certifikatlösenord**, skriver du lösenordet som skapats när du exporterar PFX-filen.

5.  Välj **Överför**.

![Överför certifikat](media\azure-stack-solution-geo-distributed\image38.png)

När App Service har överfört certifikatet, visas den i den **SSL-inställningar** sidan.

![Alternativ text](media\azure-stack-solution-geo-distributed\image39.png)

#### <a name="bind-your-ssl-certificate"></a>Binda SSL-certifikatet

1.  I den **SSL-bindningar** väljer **Lägg till bindning**.

    > [!Note]  
    >  Om certifikatet har laddats upp, men visas inte i domänens namn i den **värdnamn** listrutan, försök uppdatera webbläsarsidan.

1.  I den **Lägg till SSL-bindning** kan du använda listrutorna för att välja domännamn för att skydda och certifikatet som ska användas.

2.  I **SSL-typ**, Välj om du vill använda [ **(Servernamnindikator)**](http://en.wikipedia.org/wiki/Server_Name_Indication)eller IP-baserad SSL.

-   **SNI-baserad SSL**-flera SNI-baserad SSL-bindningar kan läggas till. Med det här alternativet kan flera SSL-certifikat skydda flera domäner på samma IP-adress. De flesta moderna webbläsare (inklusive Internet Explorer, Chrome, Firefox och Opera) stöder SNI (mer information om webbläsare som stöds finns i [Servernamnindikator](http://wikipedia.org/wiki/Server_Name_Indication)).

-   **IP-baserad SSL**-endast en IP-baserad SSL-bindning kan läggas till. Med det här alternativet tillåts endast ett SSL-certifikat för att skydda en dedikerad offentlig IP-adress. Om du vill skydda flera domäner, skydda dem alla med hjälp av samma SSL-certifikat. Detta är det vanligaste alternativet för SSL-bindning.

    1.  Välj **Lägg till bindning**.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image40.png)

När App Service har överfört certifikatet, visas den i den **SSL-bindningar** avsnitt.

![Alternativ text](media\azure-stack-solution-geo-distributed\image41.png)

#### <a name="remap-the-a-record-for-ip-ssl"></a>Mappa om A-posten för IP SSL

Om IP-baserad SSL inte används i webbapp, gå vidare till [testa HTTPS för din anpassade domän](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-ssl).

Som standard använder webbappen en delad offentlig IP-adress. När certifikatet binds med IP-baserad SSL, skapar en ny och dedikerade IP-adress för webbappen i App Service.

När en A-post mappas till webbappen, måste du uppdatera registret domän med dedikerad IP-adress.

Den **Custom domain** sidan uppdateras med den nya dedikerade IP-adressen. Kopiera [IP-adress](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain), sedan mappa om den [en post](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain) till den nya IP-adressen.

#### <a name="test-https"></a>Testa HTTPS

Se till webben ap hanteras olika webbläsare och bläddra till https://<your.custom.domain>to.

![Alternativ text](media\azure-stack-solution-geo-distributed\image42.png)

> [!Note]  
> Om det inträffar certifikatfel för verifiering, orsaken kan vara ett självsignerat certifikat eller mellanliggande certifikat kan ha varit slutade när du exporterar till PFX-fil.

#### <a name="enforce-https"></a>Använda HTTPS

Som standard kan alla komma åt webbappen med HTTP. alla HTTP-begäranden till HTTPS-porten kan omdirigeras.

På sidan web app väljer **SL inställningar**. I **Endast HTTPS** väljer du **På**.

![Använda HTTPS](media\azure-stack-solution-geo-distributed\image43.png)

När åtgärden är klar, går du till någon av de HTTP-webbadresser som pekar på appen. Exempel:

-   http://<app_name>.azurewebsites.NET
-   http://northwindcloud.com
-   <http://www.northwindcloud.com>

#### <a name="enforce-tls-1112"></a>Använda TLS 1.1/1.2

Appen tillåter [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.0 som standard, vilket är inte längre anses som säkert enligt branschstandarder, t.ex [PCI DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard). Följ dessa steg om du vill göra en högre TLS-version obligatorisk:

1.  I webbsida app i det vänstra navigeringsfönstret väljer **SSL-inställningar**.

2.  I **TLS-version**, välja den lägsta TLS-versionen.

![Kräv TLS 1.1 eller 1.2](media\azure-stack-solution-geo-distributed\image44.png)

### <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

1.  Välj **skapa en resurs** > **nätverk** > **Traffic Manager-profil** > **skapa**.

2.  I **Skapa Traffic Manager-profil** gör du följande:

    1.  I **namn**, ange ett namn för profilen. Det här namnet måste vara unikt inom den trafik manager.net zon och resulterar i DNS-namn, trafik manager.net som används för att få åtkomst till Traffic Manager-profilen.

    2.  I **routningsmetod**väljer den **Geographicrouting metoden**.

    3.  I **prenumeration**, Välj den prenumeration som du skapar den här profilen.

    4.  I **Resursgrupp** skapar du en ny resursgrupp att placera profilen under.

    5.  I **Resursgruppsplats** väljer du plats för resursgruppen. Den här inställningen refererar till platsen för resursgruppen och har ingen inverkan på Traffic Manager-profilen som ska distribueras globalt.

    6.  Välj **Skapa**.

    7.  När den globala distribueringen av Traffic Manager-profilen är klar visas den i respektive resursgrupp som en av resurserna.

    ![Alternativ text](media\azure-stack-solution-geo-distributed\image45.png)

### <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter

1.  I sökfältet portaler, söker du efter den ** Traffic Manager-profil ** namnet som skapas i föregående avsnitt och välj traffic manager-profilen i resultaten som visas.

2.  I **Traffic Manager-profil**i den **inställningar** väljer **slutpunkter**.

3.  Välj **Lägg till**.

4.  Lägger till Azure Stack-slutpunkten.

5.  För **typ**väljer **extern slutpunkt**.

6.  Ange en **namn** för den här slutpunkten, helst namnet på Azure Stack.

7.  För fullständigt kvalificerade domännamnet (**FQDN**), Använd externa URL: en för Azure Stack Web App.

8.  Välj en region/kontinent där resursen finns, till exempel under Geo-mappning, **Europa.**

9.  Under Land/Region listrutan som visas, väljer du det land som gäller för den här slutpunkten, till exempel **Tyskland**.

10. Behåll **Lägg till som inaktiverad** som avmarkerat.

11. Välj **OK**.

12. Lägga till Azure-slutpunkten:

    1.  För **typ**väljer **Azure-slutpunkt**.

    2.  Ange en **namn** för den här slutpunkten.

    3.  För **Målresurstyp**väljer **Apptjänst**.

    4.  För **målresurs**väljer **väljer en app service** att visa listor över Web Apps i samma prenumeration. I **Resource**, Välj App service-användningen som den första slutpunkten.

13. Välj en region/kontinent där resursen finns, till exempel under Geo-mappning **Nordamerika/centrala America/Karibien.**

14. Under Land/Region listrutan som visas, lämna det tomt om du vill markera alla ovanstående regional gruppering.

15. Behåll **Lägg till som inaktiverad** som avmarkerat.

16. Välj **OK**

  > [!Note]  
  >  Skapa minst en slutpunkt med geografiska omfattning av alla (världen) som fungerar som standardslutpunkt för resursen.

1.  När båda slutpunkterna har lagts till visas de i **Traffic Manager-profilen** tillsammans med sin övervakningsstatus, som är **Online**.

  ![Alternativ text](media\azure-stack-solution-geo-distributed\image46.png)

**Globala företag förlitar sig på Azure Geodistribution funktioner**

Dirigera trafik via Azure Traffic Manager och geografi slutpunkter kan globala företag Följ regionala bestämmelser och hålla data kompatibel och säkra förutsättning för ett framgångsrikt av lokala företag och mellan fjärranslutna platser.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Azure molnmönster i [Molndesignmönster](https://docs.microsoft.com/azure/architecture/patterns).
