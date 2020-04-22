---
title: Skapa ett nytt Azure Apps-erbjudande på den kommersiella marknaden
description: Så här skapar du ett nytt Azure Apps-erbjudande för att lista eller sälja i Azure Marketplace, AppSource eller csp-programmet (Cloud Solution Provider) med hjälp av Den kommersiella Marketplace-portalen på Microsoft Partner Center.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: a62551b31c51836dfa50534ee6db907c8a4e111a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730624"
---
# <a name="create-an-azure-application-offer"></a>Skapa ett erbjudande för Azure-program

Stegen för att publicera ett Azure-programerbjudande på kommersiell marknadsplats beskrivs här.

## <a name="azure-application-offer-type"></a>Typ av azure-programerbjudande

Det här avsnittet beskriver grunderna om Azure-programerbjudanden.  Du bör känna till dessa begrepp innan du börjar publicera ett nytt Azure-programerbjudande på Marketplace.

### <a name="publishing-overview"></a>Översikt över publicering

[VideoBuilding Solution Templates och Managed Applications for the Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) är en introduktion till typen av Azure-programerbjudande:

* Vilka erbjudandetyper är tillgängliga;
* Vilka tekniska tillgångar som krävs.
* Så här skapar du en Azure Resource Manager-mall.
* Utveckla och testa appgränssnittet.
* Så här publicerar du apperbjudandet.
* Processen för granskning av ansökningar.

### <a name="types-of-azure-application-plans"></a>Typer av Azure-programplaner

Det finns två typer av Azure-programplaner, hanterade program och lösningsmallar.

* **Lösningsmall är** ett av de viktigaste sätten att publicera en lösning på Marketplace. Den här plantypen används när din lösning kräver ytterligare distribution och konfigurationsautomatisering utöver en enda virtuell dator (VM).  Med en lösningsmall kan du automatisera tillhandahållandet av mer än en resurs, inklusive virtuella datorer, nätverk och lagringsresurser för att tillhandahålla komplexa IaaS-lösningar.  Mer information om hur du skapar lösningsmallar finns i Dokumentationen till [Azure Resource Manager.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

* **Hanterade program** liknar lösningsmallar, med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använd hanterade program för att enkelt skapa och leverera helt hanterade, nyckelfärdiga program till dina kunder.  Mer information om fördelar och typer av hanterade program finns i [översikten över hanterade azure-program](https://docs.microsoft.com/azure/managed-applications/overview).

Alla Azure-program innehåller minst två filer `.zip` i rotmappen i ett arkiv:

* En Resource Manager-mallfil med namnet [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Det här är mallen som definierar resurserna som ska distribueras till kundens Azure-prenumeration.  Exempel på Resource Manager-mallar finns i [azure Quickstart Templates-galleriet](https://azure.microsoft.com/resources/templates/) eller motsvarande [snabbstartsmallar för GitHub: Azure Resource Manager.](https://github.com/azure/azure-quickstart-templates)

* En användargränssnittsdefinition för azure-programskapande upplevelse som heter [createUiDefinition.json](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  I användargränssnittet anger du element som ger konsumenterna möjlighet att ange parametervärden.

Alla nya Azure-programerbjudanden måste innehålla ett GUID för [azure-partnerkundanvändningsattribution](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution).

### <a name="before-you-begin"></a>Innan du börjar

Granska följande Azure-programdokumentation, som innehåller snabbstarter, självstudier och exempel.

* [Förstå Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Snabbstarter:

    * [Snabbstartsmallar för Azure](https://azure.microsoft.com/documentation/templates/)
    * [Snabbstartsmallar för GitHub Azure](https://github.com/azure/azure-quickstart-templates)
    * [Publicera programdefinition](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Distribuera tjänstkatalogapp](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Självstudier:

    * [Skapa definitionsfiler](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publicera marknadsplatsprogram](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Prover:

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Hanterade programlösningar](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Grunderna i teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet.

Ditt ingenjörsteam bör ha kunskap om följande Microsoft-tekniker:

* Grundläggande förståelse av [Azure Services](https://azure.microsoft.com/services/).
* Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/).
* Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och Azure [Networking](https://azure.microsoft.com/services/?filter=networking#networking).
* Kunskap om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Arbetskunskap om [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Föreslagna verktyg

Välj en eller båda av följande skriptmiljöer för att hantera ditt Azure-program:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar att du lägger till följande verktyg i utvecklingsmiljön:

* [Azure Lagringsutforskaren](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio-kod](https://code.visualstudio.com/) med följande tillägg:
    * Tillägg: [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Förlängning: [Försköna](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Förlängning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Du kan granska de tillgängliga verktygen på sidan [Azure Developer Tools.](https://azure.microsoft.com/tools/)  Även om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Skapa ett erbjudande för Azure-program

Innan du kan skapa ett Azure-programerbjudande måste du först [skapa ett Partner Center-konto](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) och öppna [instrumentpanelen för kommersiella marknadsplatser](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), med fliken **Översikt** vald.

>[!Note]
>När ett erbjudande har publicerats uppdateras redigeringarna av erbjudandet i Partner Center endast i systemet och skyltfönster efter återpublicering.  Se till att du skickar in erbjudandet för publicering när du har gjort ändringar.

### <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Välj knappen **+ Nytt erbjudande** och välj sedan menyalternativet för Azure **Application.** Dialogrutan **Nytt erbjudande** visas.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

* **Erbjudande-ID:** En unik identifierare för varje erbjudande i ditt konto. Det här ID:t visas för kunder i URL-adressen för marketplace-erbjudandet och Azure Resource Manager-mallar (om tillämpligt). <br> <br> Erbjudande-ID:t måste vara gemener alfanumeriska tecken (inklusive bindestreck och understreck, men inget blanktecken). Den är begränsad till 50 tecken och kan inte ändras när du har valt Skapa. <br> <br> Om du till `test-offer-1` exempel anger här blir `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`erbjudandeadressen . 

* **Offeralias**: Namnet som används för att referera till erbjudandet i partnercentret. Det här namnet används inte på marknadsplatsen och skiljer sig från erbjudandenamnet och andra värden som visas för kunderna. Det går inte att ändra det här värdet när du har valt **Skapa**.

När du har angett ditt **offer-ID** och **Offeralias**väljer du **Skapa**. Du kommer då att kunna arbeta med alla andra delar av ditt erbjudande.

## <a name="offer-setup"></a>Inställningar för erbjudande

På **inställningssidan** för Erbjudande frågar du efter följande information. Var noga med att välja **Spara** när du har slutfört dessa fält.

### <a name="test-drive"></a>Provkörning

En provkörning är ett bra sätt att visa upp ditt erbjudande till potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och generering av högt kvalificerade leads. [Läs mer om provkörningar.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Om du vill aktivera en provkörning markerar du rutan **Aktivera en provkörning.** Du måste sedan konfigurera en demonstrationsmiljö i den tekniska konfigurationen för [testlusten](#types-of-azure-application-plans) som konfigureras så att kunderna kan prova erbjudandet under en viss tidsperiod. 

>[!Note]
>Eftersom alla Azure-program implementeras med en Azure Resource Manager-mall är den enda typen av testenhet som kan konfigureras för ett Azure-program en [Azure Resource Manager-baserad testenhet](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Ytterligare provkörningsresurser

- [Metodtips för testkörning](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metodtips för provkörningsmarknadsföring](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över provkörning en personsökare](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Anslut leadhantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över leadhantering](./commercial-marketplace-get-customer-leads.md).

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** kan du definiera de kategorier och branscher som används för att gruppera ditt erbjudande på marknadsplatsen, din appversion och de juridiska kontrakt som stöder ditt erbjudande. Välj **Spara** när du har slutfört den här sidan.

### <a name="category"></a>Kategori

Välj minst en och högst tre kategorier, som kommer att användas för att placera ditt erbjudande i lämpliga marknadsplats sökområden. Var noga med att ropa ut hur ditt erbjudande stöder dessa kategorier i erbjudandebeskrivningen. 

### <a name="standard-contract-for-the-microsoft-commercial-marketplace"></a>Standardkontrakt för Microsofts kommersiella marknadsplats

[!INCLUDE [Commercial marketplace Standard Contract](./includes/marketplace-contract-text.md)]

## <a name="offer-listing"></a>Lista över erbjudande

På sidan Erbjudandelista visas de språk på vilka ditt erbjudande visas. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Du måste definiera marknadsplatsinformation (erbjudandenamn, beskrivning, bilder, etc.) för varje språk/marknad. Välj språk-/marknadsnamn för att ange den här informationen.

> [!NOTE]
> Erbjudande notering innehåll (såsom beskrivning, dokument, skärmdumpar, användarvillkor, etc.) är inte skyldig att vara på engelska, så länge erbjudandet beskrivningen börjar med frasen"Denna ansökan är endast tillgänglig på [icke-engelska språket]." Det är också acceptabelt att ange en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehåll för erbjudandelistor.

### <a name="name"></a>Namn

Namnet du anger här visas för kunderna som titeln på din erbjudandebeskrivning. Det här fältet är förifyllt med den text som du angav för **Offer alias** när du skapade erbjudandet, men du kan ändra det här värdet. Detta namn kan vara varumärkesskyddat (och du kan inkludera varumärkes- eller upphovsrättssymboler). Namnet får inte vara fler än 50 tecken och kan inte innehålla några emojis.

### <a name="summary"></a>Sammanfattning

Ge en kort beskrivning av ditt erbjudande (upp till 100 tecken), som kan användas i marknadsplats sökresultat.

### <a name="long-summary"></a>Lång sammanfattning

Ge en längre beskrivning av ditt erbjudande (upp till 256 tecken). Beskrivningen kan användas i marketplace-sökresultat.

### <a name="description"></a>Beskrivning

Ge en längre beskrivning av ditt erbjudande (upp till 3 000 tecken). Den här beskrivningen visas för kunder i översikten för marknadsplatslistning. Inkludera erbjudandets värdeerbjudande, viktiga fördelar, kategori- och/eller branschorganisationer, köpmöjligheter i appen och eventuella nödvändiga upplysningar. 

Några tips för att skriva din beskrivning:  

- Beskriv tydligt erbjudandets värdeerbjudande i de första meningarna i din beskrivning. Inkludera följande objekt i ditt värdeerbjudande:
  - Beskrivning av produkten
  - Den typ av användare som drar nytta av produkten
  - Kundens behov eller smärta som produkten adresserar
- Tänk på att de första meningarna kan visas i sökmotorresultat.  
- Lita inte på funktioner och funktioner för att sälja din produkt. Fokusera istället på det värde du levererar.  
- Använd branschspecifik vokabulär eller förmånsbaserad formulering så mycket som möjligt. 
- Överväg att använda HTML-taggar för att formatera beskrivningen och göra den mer engagerande.

### <a name="search-keywords"></a>Sök nyckelord

Du kan också ange upp till tre sökord för att hjälpa kunderna att hitta ditt erbjudande på marknaden. För bästa resultat, försök att använda dessa sökord i din beskrivning också.

### <a name="support-urls"></a>Stöd webbadresser

I det här avsnittet kan du tillhandahålla länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>Url till sekretesspolicy

Ange webbadressen till organisationens sekretesspolicy. Du är ansvarig för att din app följer sekretesslagar och sekretessregler och för att tillhandahålla en giltig sekretesspolicy.

#### <a name="useful-links"></a>Användbara länkar

Tillhandahålla extra kompletterande onlinedokument om din lösning.  Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en länk**.

### <a name="contacts"></a>Contacts

I det här avsnittet måste du ange namn, e-post och telefonnummer för en **supportkontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men kommer att vara tillgänglig för Microsoft och kan tillhandahållas CSP-partner.

I avsnittet **Supportkontakt** måste du också ange **support-URL:en** där CSP-partner kan hitta support för ditt erbjudande.

### <a name="marketplace-images"></a>Marketplace-bilder

I det här avsnittet kan du ange logotyper och bilder som ska användas när du visar ditt erbjudande till kunden. Alla bilder måste vara i PNG-format.

#### <a name="store-logos"></a>Lagra logotyper

Ange ditt erbjudandes logotyp i tre storlekar: **Small (48 x 48)**, **Medium (90 X 90)** och **Large (216 x 216)**.

#### <a name="hero"></a>Hjälte

Hjältebilden är valfri. Om du anger en, måste den mäta 815 x 290 pixlar.

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärmdumpar som visar hur erbjudandet fungerar. Du kan lägga till upp till fem skärmdumpar. Alla skärmdumpar måste vara 1280 x 720 pixlar.

#### <a name="videos"></a>Videoklipp

Du kan också lägga till upp till fem videor som visar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en anger du videons namn, webbadress och en miniatyrbild av videon (1 280 x 720 pixlar).

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare resurser för marknadsplatsnotering

- [Metodtips för marknadsplatserbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Förhandsversion

På fliken **Förhandsgranska** kan du definiera en begränsad målgrupp för **att** validera ditt erbjudande innan du publicerar ditt erbjudande live för den bredare marknadsplatsens målgrupp.

> [!IMPORTANT]
> Du måste välja **Gå live** innan ditt erbjudande publiceras live till den offentliga målgruppen på marknaden efter att ha kontrollerat ditt erbjudande i förhandsversionen.

Din förhandsgranskningsmålgrupp identifieras av Azure-prenumerations-ID-GUID: er, parat med en valfri beskrivning för varje.  Inget av dessa fält är synliga för kunder.

Lägg till upp till 10 Azure-prenumerations-ID:n manuellt eller upp till 100 om du laddar upp en CSV-fil.  Genom att lägga till dessa prenumerationer definierar du en målgrupp som får förhandsåtkomst till ditt erbjudande innan det publiceras helt.  Om ditt erbjudande redan är live kan du fortfarande definiera en målgrupp för förhandsversionen för att testa eventuella ändringar eller uppdateringar av ditt erbjudande.

>[!Note]
>Förhandsgranskningspubliken skiljer sig från en privat målgrupp. En förhandsgranskningsmålgrupp får åtkomst till ditt erbjudande *innan* den publiceras live på marknadsplatserna. Du kan också välja att skapa en plan och göra den tillgänglig endast för en privat målgrupp (med hjälp av fliken Abonnemangstillgänglighet).  Din förhandsgranskningsmålgrupp kan se och validera alla planer, inklusive planer som endast är tillgängliga för en privat målgrupp när ditt erbjudande har publicerats helt på marknadsplatsen.

## <a name="plan-overview"></a>Översikt över plan

På fliken **Planera översikt** kan du ange olika planalternativ i samma erbjudande. Dessa planer (kallas SKU:er i Cloud Partner Portal) kan skilja sig åt när det gäller plantyp (lösningsmall kontra hanterat program), intäktsgenerering eller målgrupp.  Konfigurera minst en plan för att lista ditt erbjudande på marknadsplatsen.

När du har skapat dina plannamn, ID:er, plantyp, tillgänglighet (offentlig eller privat), aktuell publiceringsstatus och alla tillgängliga åtgärder på den här fliken.

**Vilka åtgärder** som är tillgängliga i **översikten Planera** varierar beroende på planens aktuella status och kan omfatta:

* Om planstatusen är **Utkast** – Ta bort utkast.
* Om planstatusen är **Live** – Sluta sälja plan eller Synkronisera privat målgrupp.

### <a name="create-new-plan"></a>Skapa ny plan

***Plan-ID*** - Skapa ett unikt plan-ID för varje plan i det här erbjudandet. Detta ID visas för kunder i produktadressen.  Använd bara gemener, alfanumeriska tecken, streck eller understreck. Högst 50 tecken tillåts för det här plan-ID:t. Det går inte att ändra det här ID:t när du har valt skapa.

***Plannamn*** - Kunderna ser det här namnet när de bestämmer vilken plan som ska väljas inom ditt erbjudande. Skapa ett unikt erbjudandenamn för varje plan i det här erbjudandet. Plannamnet används för att differentiera programvaruplaner som kan vara en del av samma erbjudande (t.ex. Erbjudandenamn: Windows Server; abonnemang: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Planera inställningar

På **fliken Planera inställningar** kan du ange konfigurationen på hög nivå för typen av plan, om den återanvänder paket från en annan plan och vilka moln planen ska vara tillgänglig i.  Dina svar på den här fliken påverkar vilka fält som visas på andra flikar för samma plan.

#### <a name="plan-type"></a>Typ av plan

Som beskrivs i [typerna av Azure-programplaner](#types-of-azure-application-plans)väljer du om din plan ska innehålla en lösningsmall eller ett hanterat program.

#### <a name="this-plan-reuses-packages"></a>Den här planen återanvänder paket

Om du har mer än en plan av samma typ och paketen är identiska mellan dem, kan du välja **den här planen återanvända paket från en annan plan**.  När du väljer det här alternativet kan du välja en av de andra abonnemangen av samma typ för det här erbjudandet att återanvända paket från. 

>[!Note]
>När du återanvänder paket från en annan plan försvinner hela fliken Teknisk konfiguration från den här planen.  Den tekniska konfigurationsinformationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, kommer också att användas för den här planen. <br> <br> Den här inställningen kan inte heller ändras när den här planen har publicerats.

#### <a name="cloud-availability"></a>Molntillgänglighet

Den här planen måste göras tillgänglig i minst ett moln. 

Välj alternativet **Offentlig Azure** för att göra din lösning distribuerad till kunder i alla offentliga Azure-regioner som har Marketplace-integrering.  Läs mer om [geografisk tillgänglighet](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Välj alternativet **Azure Government Cloud** för att göra din lösning distribuerad i Azure Government [Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), ett moln för myndigheter med kontrollerad åtkomst för kunder från usa:s federala, statliga, lokala eller stam- och stam- och partner som är kvalificerade att betjäna dessa entiteter.  Du som utgivare är ansvarig för alla efterlevnadskontroller, säkerhetsåtgärder och bästa praxis för att betjäna den här molncommunityn.  Azure Government använder fysiskt isolerade datacenter och nätverk (endast i USA).  Innan du publicerar till [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)rekommenderar Microsoft att du testar och validerar din lösning i miljön eftersom vissa slutpunkter kan skilja sig åt. Om du vill arrangera och testa din lösning begär du ett utvärderingskonto från den här [länken](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>När en plan har publicerats som tillgänglig i ett visst moln kan inte molnet tas bort.

**Azure Government Cloud-certifieringar**

Det här alternativet visas bara om **Azure Government Cloud** har valts under **Molntillgänglighet**.

Azure Government-tjänster hanterar data som omfattas av vissa myndighetsregler och krav, till exempel FedRAMP, NIST 800.171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS.  För att öka medvetenheten om dina certifieringar för dessa program kan du tillhandahålla upp till 100 länkar som beskriver dina certifieringar.  Dessa länkar kan vara antingen länkar till dina uppgifter på programmet direkt, eller en länkar till beskrivningar av din efterlevnad av dem på dina egna webbplatser.  Dessa länkar visas endast för Azure Government Cloud-kunder.

## <a name="plan-listing"></a>Planera listning

På fliken **Lista plan** visas den planspecifika listinformation som kan skilja sig mellan olika planer för samma erbjudande.

### <a name="name"></a>Namn

Förifylld baserat på ditt namn som du tilldelade planen när du skapade den.  Det här namnet visas som titeln på denna "programvaruplan" som visas på marknadsplatsen.  Kan innehålla upp till 100 tecken.

### <a name="summary"></a>Sammanfattning

Ge en kort sammanfattning av din programvaruplan.  Kan innehålla upp till 100 tecken.

### <a name="description"></a>Beskrivning

Denna beskrivning är ett tillfälle att förklara vad som gör denna programvara plan unik och eventuella skillnader från andra programplaner inom ditt erbjudande. Kan innehålla upp till 2 000 tecken.

Välj **Spara** när du har slutfört dessa fält.

## <a name="availability"></a>Tillgänglighet

Fliken **Tillgänglighet** är endast synlig för lösningsmallplaner.  Du kan göra planen synlig för alla, bara för specifika kunder (en privat målgrupp) och om planen ska döljas för användning av annan lösningsmall eller hanterade program.

### <a name="plan-audience"></a>Planera målgrupp

Du har möjlighet att konfigurera varje plan så att den är synlig för alla eller för endast en viss målgrupp som du väljer. Du kan tilldela medlemskap i den här begränsade målgruppen med hjälp av Azure-prenumerations-ID:er.

**Sekretess / Detta är en privat plan** (Valfri kryssruta) - Markera den här rutan för att göra din plan privat och synlig endast för den begränsade målgrupp du väljer. När du har publicerats som en privat plan kan du uppdatera målgruppen eller välja att göra planen tillgänglig för alla. När en plan har publicerats som synlig för alla måste den vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

**Begränsad målgrupp (Azure-prenumerations-ID)** – Tilldela målgruppen som har åtkomst till den här privata planen. Åtkomst tilldelas med hjälp av Azure-prenumerations-ID:er med möjlighet att inkludera en beskrivning av varje Azure-prenumerations-ID som tilldelats. Högst 10 prenumerations-ID:n kan läggas till, eller 20 000 kunders prenumerations-ID om du importerar en CSV-kalkylbladsfil.  Azure-prenumerations-ID:er representeras som GUID och bokstäver måste vara gemener.

>[!Note]
>Den privata målgruppen (eller den begränsade målgruppen) skiljer sig från den målgrupp du definierade på fliken [**Förhandsgranska.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  En förhandsgranskningsmålgrupp får åtkomst till ditt erbjudande *innan* erbjudandet publiceras live på marknaden. Medan den privata målgruppsbeteckningen endast gäller för en viss plan kan förhandsgranskningspubliken visa alla planer (privata eller inte) för valideringsändamål.

### <a name="hide-plan"></a>Dölj plan

Om lösningsmallen endast är avsedd att distribueras indirekt när den refereras genom en annan lösningsmall eller ett hanterat program markerar du den här rutan för att publicera lösningsmallen men döljer den från kunder som söker och surfar efter den direkt.

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Fliken **Prissättning och tillgänglighet** är endast synlig för hanterade programplaner.  Du kan konfigurera de marknader som den här planen ska vara tillgänglig på, priset per månad för hanteringen av lösningen och om planen ska göras synlig för alla eller bara för specifika kunder (en privat målgrupp).

### <a name="markets"></a>Marknader

Varje plan måste finnas tillgänglig på minst en marknad. Markera kryssrutan för alla marknadsplatser där du vill göra planen tillgänglig. En sökruta och knapp för att välja "Moms remitterade" länder, där Microsoft skickar försäljning och använda skatt för din räkning, ingår för att hjälpa dig.

Om du redan har angett priser för din plan i USA-dollar (USD) och lägger till en annan marknadsplats, kommer priset för den nya marknaden att beräknas enligt aktuella valutakurser. Granska alltid priset för varje marknad innan publicering. Prissättningen kan granskas med hjälp av länken "Exportpriser (xlsx)" när du har sparat ändringarna.

### <a name="pricing"></a>Prissättning

Ange månadspriset för den här planen.  Det här priset är utöver alla Azure-infrastruktur- eller användningsbaserade programvarukostnader som uppstår för de resurser som distribueras av den här lösningen.

Priserna som anges i lokal valuta (USD = US-dollar) konverteras till lokal valuta för alla valda marknader med hjälp av aktuella valutakurser som är tillgängliga under installationen. Validera dessa priser innan du publicerar genom att exportera priskalkylbladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar och importerar du priskalkylbladet. 

>[!Note]
>Du måste först spara dina prisändringar för att aktivera export av prisdata.

Granska dina priser noggrant innan du publicerar, eftersom det finns vissa begränsningar för vad som kan ändras när en plan har publicerats.  

>[!Note]
>När ett pris för en marknad i din plan har publicerats kan det inte ändras senare.

### <a name="plan-audience"></a>Planera målgrupp

Du har möjlighet att konfigurera varje plan så att den är synlig för alla eller för endast en viss målgrupp som du väljer. Du kan tilldela medlemskap i den här begränsade målgruppen med hjälp av Azure-prenumerations-ID:er.

**Sekretess / Detta är en privat plan** (Valfri kryssruta) - Markera den här rutan för att göra din plan privat och synlig endast för den begränsade målgrupp du väljer. När du har publicerats som en privat plan kan du uppdatera målgruppen eller välja att göra planen tillgänglig för alla. När en plan har publicerats som synlig för alla måste den vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

**Begränsad målgrupp (Azure-prenumerations-ID)** – Tilldela målgruppen som har åtkomst till den här privata planen. Åtkomst tilldelas med hjälp av Azure-prenumerations-ID:er med möjlighet att inkludera en beskrivning av varje Azure-prenumerations-ID som tilldelats. Högst 10 prenumerations-ID:n kan läggas till, eller 20 000 kunders prenumerations-ID om du importerar en CSV-kalkylbladsfil.  Azure-prenumerations-ID:er representeras som GUID och bokstäver måste vara gemener.

>[!Note]
>Den privata målgruppen (eller den begränsade målgruppen) skiljer sig från den målgrupp du definierade på fliken [**Förhandsgranska.**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview)  En förhandsgranskningsmålgrupp får åtkomst till ditt erbjudande *innan* erbjudandet publiceras live på marknaden. Medan den privata målgruppsbeteckningen endast gäller för en viss plan kan förhandsgranskningspubliken visa alla planer (privata eller inte) för valideringsändamål.

## <a name="technical-configuration"></a>Teknisk konfiguration 

Med fliken **teknisk konfiguration** kan du ladda upp distributionspaketet som gör det möjligt för kunder att distribuera din plan.

>[!Note]
>Den här fliken visas inte om du har konfigurerat den här planen för att återanvända paket från ett annat abonnemang på fliken **Planera inställningar.**

### <a name="package-details"></a>Information om paketet

Med underfliken **Paketinformation** kan du redigera utkastversionen av din tekniska konfiguration.

***Version*** - Tilldela den aktuella versionen av den tekniska konfigurationen.  Öka den här versionen varje gång du publicerar en ändring på den här sidan. Versionen måste vara `{integer}.{integer}.{integer}`i formatet .

***Paketfil*** `.zip`( ) - Det här paketet innehåller alla mallfiler som behövs för den `.zip` här planen, samt eventuella ytterligare resurser, paketerade som en fil.

Alla Azure-programplanpaket måste innehålla dessa två filer `.zip` i rotmappen i ett arkiv:

* En Resource Manager-mallfil med namnet [mainTemplate.json](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Den här mallen automatiserar distributionen av resurser till kundens Azure-prenumeration.  Exempel på Resource Manager-mallar finns i [azure Quickstart Templates-galleriet](https://azure.microsoft.com/documentation/templates/) eller motsvarande [snabbstartsmallar för GitHub: Azure Resource Manager.](https://github.com/azure/azure-quickstart-templates)

* En användargränssnittsdefinition för azure-programskapande upplevelse som heter [createUiDefinition.json](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Alla nya Azure-programerbjudanden måste också innehålla ett GUID för [azure-partnerkundanvändningsattribution.](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution)

### <a name="previously-published-packages"></a>Tidigare publicerade paket 

Med underfliken **Tidigare publicerade paket** kan du visa alla publicerade versioner av din tekniska konfiguration.

## <a name="technical-configuration-managed-application-plans-only"></a>Teknisk konfiguration (endast hanterade programplaner)

Hanterade programplaner har ytterligare komplexitet på fliken **Teknisk konfiguration** utöver de **filfält** **för version** och paket som beskrivs ovan. 

### <a name="enable-just-in-time-jit-access"></a>Aktivera jit-åtkomst (just-in-time)

Välj det här alternativet om du vill aktivera jit-åtkomst (Just-in-time) för den här planen.  JIT-åtkomst gör att du kan begära förhöjd åtkomst till ett hanterat programs resurser för felsökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tidsperiod kan du få större åtkomst.  Mer information finns [i Aktivera och begära just-in-time-åtkomst för Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Om du vill att konsumenter av ditt hanterade program ska bevilja ditt konto permanent åtkomst lämnar du det här alternativet omarkerat.

>[!Note]
>Var noga med `createUiDefinition.json` att uppdatera filen för att stödja den här funktionen.  

### <a name="deployment-mode"></a>Driftsättningsläge

Välj om **du** vill konfigurera fullständigt eller **inkrementellt distributionsläge** när du distribuerar den här planen: 

* I **fullständigt läge**resulterar en omfördelning av programmet av kunden i att resurser tas bort i den `mainTemplate.json`hanterade resursgruppen om resurserna inte har definierats i . 
* I **inkrementellt läge**lämnar en omfördelning av programmet befintliga resurser oförändrade.

Mer information om distributionslägen finns i [Distributionslägen för Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL till meddelandeslutpunkt

Ange en HTTPS Webhook-slutpunkt för att ta emot meddelanden om alla CRUD-åtgärder på hanterade programinstanser i den här planversionen.

### <a name="customize-allowed-customer-actions"></a>Anpassa tillåtna kundåtgärder

Välj det här alternativet om du vill ange vilka åtgärder`*/read`kunder kan utföra på de hanterade resurserna utöver de " " åtgärder som är tillgängliga som standard. 

Lista de ytterligare åtgärder som du vill göra det möjligt för kunden att utföra här, avgränsade med semikolon.  Mer information finns i [Förstå neka tilldelningar för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Tillgängliga åtgärder finns i [Azure Resource Manager-resursprovideråtgärder](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Om du till exempel vill tillåta `Microsoft.Compute/virtualMachines/restart/action` konsumenter att starta om virtuella datorer lägger du till de tillåtna åtgärderna.

### <a name="global-azure--azure-government-cloud"></a>Globalt Azure / Azure Government Cloud

Ange vem som ska ha hanteringsåtkomst till det här hanterade programmet i varje moln som stöds.  Användare, grupper eller program som du vill ska beviljas behörighet till den hanterade resursgruppen identifieras med hjälp av Azure Active Directory -identiteter (AAD).

***Azure Active Directory Tenant ID*** - AAD Tenant ID (kallas även katalog-ID) som innehåller identiteterna för användare, grupper eller program som du vill bevilja behörigheter till.  Du hittar ditt AAD-klient-ID på Azure-portalen i [Egenskaper för Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Auktoriseringar*** - Lägg till Azure Active Directory-objekt-ID för den användare, grupp eller det program som du vill ska beviljas behörighet till den hanterade resursgruppen. Identifiera användaren med deras huvud-ID, som finns på [Azure Active Directory-användarbladet på Azure-portalen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

För varje huvudnamn väljer du en av de azure AD-inbyggda rollerna i listan (Ägare eller Deltagare). Den roll du väljer beskriver de behörigheter som huvudmannen har på resurserna i kundprenumerationen. Mer information finns i [Inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Mer information om rollbaserad åtkomstkontroll (RBAC) finns [i Komma igång med RBAC i Azure-portalen](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Även om du kan lägga till upp till 100 auktoriseringar per moln är det i allmänhet enklare att skapa en Active Directory-användargrupp och ange dess ID i "Huvud-ID".  På så sätt kan du lägga till fler användare i hanteringsgruppen när planen har distribuerats och minska behovet av att uppdatera planen bara för att lägga till fler auktoriseringar.

### <a name="policy-settings"></a>Principinställningar

Tillämpa [Azure-principer](https://docs.microsoft.com/azure/governance/policy/overview) på ditt hanterade program för att ange efterlevnadskrav för den distribuerade lösningen.  Du kan läsa om principdefinitioner och parametervärdenas format i [Azure Policy-exempel](https://docs.microsoft.com/azure/governance/policy/samples/index).  Du kan konfigurera högst fem principer och bara en instans av varje alternativ för principer.  Vissa principer kräver ytterligare parametrar.  Standard-SKU krävs för granskningsprinciper.  Principnamnet är begränsat till 50 tecken.

## <a name="co-sell"></a>Säljsamarbete

Att tillhandahålla information på fliken Cosell är helt valfritt för att publicera ditt erbjudande. Det krävs för att uppnå Co-sell Ready och IP Co-sell Ready status. Informationen du anger kommer att användas av Microsofts säljteam för att lära dig mer om din lösning när du utvärderar dess lämplighet för kundernas behov. Den är inte tillgänglig direkt för kunder.

Mer information om hur du fyller i den här fliken finns [i Alternativet Samsälja i Partnercenter](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Provkörning

På fliken **Provkörning** kan du ställa in en demonstration (eller "provkörning") som gör det möjligt för kunder att prova ditt erbjudande innan de bestämmer sig för att köpa det. Läs mer i artikeln [Vad är Provkörning?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Om du inte längre vill tillhandahålla en provkörning för erbjudandet går du tillbaka till **inställningssidan För Erbjudande** och **avmarkerar Aktivera provkörning**.

### <a name="technical-configuration"></a>Teknisk konfiguration

Azure-program använder till sin natur testenhetstypen Azure Resource Manager.  Mer information finns i [Teknisk konfiguration för Azure Resource Manager-provkörning.](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive)

### <a name="deployment-subscription-details"></a>Information om distributionsprenumeration

Skapa och tillhandahålla en separat, unik Azure-prenumeration för att distribuera testenheten för din räkning. (Krävs inte för Power BI-provkörningar).

- **Azure-prenumerations-ID** (krävs för Azure Resource Manager- och Logic-appar): Ange prenumerations-ID för att bevilja åtkomst till dina Azure-kontotjänster för resursanvändningsrapportering och fakturering. Vi rekommenderar att du överväger att [skapa en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för testenheter om du inte redan har en. Du hittar ditt Azure-prenumerations-ID genom att logga in på [Azure-portalen](https://portal.azure.com/) och navigera till fliken **Prenumerationer** på menyn till vänster. Om du väljer fliken visas ditt prenumerations-ID (t.ex. "a83645ac-1234-5ab6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (obligatoriskt): Ange ditt Azure Active Directory (AD) [klient-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta det här ID:t loggar du in på [Azure-portalen,](https://portal.azure.com/)väljer fliken Active Directory på menyn, väljer **Egenskaper och letar sedan efter **katalog-ID-numret** som anges (t.ex. 50c464d3-4930-494c-963c-1e951d15360e). Du kan också slå upp organisationens klient-ID med [https://www.whatismytenantid.com](https://www.whatismytenantid.com)din domännamnsadress på: .

- **Azure AD-klientnamn** (krävs för Dynamic 365): Ange ditt AD-namn (Azure Active Directory). För att hitta det här namnet loggar du in på [Azure-portalen](https://portal.azure.com/), i det övre högra hörnet kommer ditt klientnamn att visas under ditt kontonamn.

- **Azure AD-app-ID** (obligatoriskt): Ange ditt AZURE Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta det här ID:t loggar du in på [Azure-portalen](https://portal.azure.com/), väljer fliken Active Directory på menyn till vänster, väljer **Appregistreringar**och letar sedan efter **program-ID-numret** som anges (t.ex. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appklienthemma** (obligatoriskt): Ange din [Azure AD-programklienthemlighet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Logga in på [Azure-portalen](https://portal.azure.com/)om du vill hitta det här värdet . Välj fliken **Azure Active Directory** på den vänstra menyn, välj Appregistreringar och välj sedan din provkörningsapp. **App registrations** Välj sedan **Certifikat och hemligheter**, välj Ny **klienthemlighet**, ange en beskrivning, välj **Aldrig** under Upphör **att gälla**och välj sedan Lägg **till**. Se till att kopiera ner värdet. (Navigera inte bort från sidan innan du har kopierat värdet, annars har du inte tillgång till värdet.)

Kom ihåg att **spara** innan du går vidare till nästa avsnitt!

### <a name="test-drive-listings-optional"></a>Provkörningslistor (valfritt)

Alternativet **Test drive-listor** som finns under fliken **Provkörning** visar de språk (och marknader) där provkörningen är tillgänglig, för närvarande engelska (USA) är den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera provkörningsinformation (beskrivning, bruksanvisning, videor, etc.) för varje språk / marknad.

- **Beskrivning** (obligatoriskt): Beskriv din provkörning, vad som visas, mål för användaren att experimentera med, funktioner att utforska och all relevant information som hjälper användaren att avgöra om du vill hämta ditt erbjudande. Upp till 3 000 tecken text kan anges i det här fältet. 

- **Åtkomstinformation** (krävs för Azure Resource Manager och Logic testenheter): Förklara vad en kund behöver veta för att komma åt och använda den här testenheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör veta för att komma åt funktioner under hela provkörningen. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användarhandbok** (krävs): En djupgående genomgång av din provkörningsupplevelse. Användarhandboken bör omfatta exakt vad du vill att kunden ska vinna på att uppleva provkörningen och fungera som referens för eventuella frågor som de kan ha. Filen måste vara i PDF-format och namnges (max 255 tecken) efter uppladdning.

- **Videor: Lägg till videor** (valfritt): Videor kan laddas upp till YouTube eller Vimeo och refereras här med en länk och miniatyrbild (533 x 324 pixlar) så att en kund kan visa en genomgång av information för att hjälpa dem att bättre förstå provkörningen, inklusive hur du framgångsrikt använder funktionerna i ditt erbjudande och förstår scenarier som belyser deras fördelar.
  - **Namn** (obligatoriskt)
  - **URL (endast YouTube eller Vimeo)** (obligatoriskt)
  - **Miniatyr (533 x 324 px):** Bildfilen måste vara i PNG-format.

Välj **Spara** när du har slutfört dessa fält.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till förhandsgranskning

När du har slutfört alla nödvändiga delar av erbjudandet väljer du **publicera** i det övre högra hörnet på portalen. Du omdirigeras till sidan **Granska och publicera.** 

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slutförandestatusen för varje avsnitt i erbjudandet.
    - *Inte startad* - innebär att avsnittet inte har berörts och måste slutföras.
    - *Ofullständig* - innebär att avsnittet har fel som måste åtgärdas eller kräver mer information som ska tillhandahållas. Gå tillbaka till avsnittet /erna och uppdatera den.
    - *Komplett* - betyder att avsnittet är komplett, alla nödvändiga data har lämnats och det finns inga fel. Alla delar av erbjudandet måste vara i ett fullständigt tillstånd innan du kan skicka erbjudandet.
- Ge testinstruktioner till certifieringsteamet för att säkerställa att din app testas korrekt, utöver eventuella kompletterande anteckningar som kan vara till hjälp för att förstå din app.
- Skicka erbjudandet för publicering genom att välja **Skicka**. Vi skickar ett e-postmeddelande till dig när en förhandsversion av erbjudandet är tillgänglig för dig att granska och godkänna. Gå tillbaka till Partner Center och välj **Go-live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande, till den privata målgruppen).

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Manuellt **valideringssteg** i publiceringsprocessen representerar en omfattande genomgång av ditt erbjudande och dess associerade tekniska resurser (särskilt Azure Resource Manager-mallen), problem presenteras vanligtvis som pull request (PR)-länkar. En förklaring av hur du visar och svarar på dessa PRs, se [Hantera feedback.](./azure-apps-review-feedback.md)

Om du stötte på fel i ett eller flera av publiceringsstegen måste du korrigera dem och publicera om erbjudandet.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
