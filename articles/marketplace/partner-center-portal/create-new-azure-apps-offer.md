---
title: Skapa ett nytt Azure Apps-erbjudande på den kommersiella Marketplace
description: Så här skapar du ett nytt Azure Apps-erbjudande för att lista eller sälja på Azure Marketplace, AppSource eller via program varan för Cloud Solution Provider (CSP) med hjälp av den kommersiella Marketplace på Microsoft Partner Center.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: d7e05f12c04136c8394dbcb27b7a950fc5ce85d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281277"
---
# <a name="create-an-azure-application-offer"></a>Skapa ett erbjudande för Azure-program

Stegen för att publicera ett erbjudande om Azure-program i kommersiella marknads platser beskrivs här.

## <a name="azure-application-offer-type"></a>Typ av erbjudande för Azure-program

Det här avsnittet beskriver grunderna om erbjudanden i Azure-program.  Du bör känna till dessa begrepp innan du påbörjar publiceringen av ett nytt Azure-programerbjudande på Marketplace.

### <a name="publishing-overview"></a>Publicerings översikt

[Mallarna för video skapande av lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603) är en introduktion till Azure-programmets erbjudande typ:

* Vilka erbjudande typer är tillgängliga.
* Det krävs tekniska till gångar.
* Så här redigerar du en Azure Resource Manager mall;
* Utveckla och testa appens användar gränssnitt;
* Så här publicerar du app-erbjudandet
* Programmets gransknings process.

### <a name="types-of-azure-application-plans"></a>Typer av program planer i Azure

Det finns två typer av Azure-programplaner, hanterade program och Solution-mallar.

* **Lösnings mal len** är ett av de största sätten att publicera en lösning på Marketplace. Den här plan typen används när lösningen kräver ytterligare distributions-och konfigurations automatisering utöver en enskild virtuell dator (VM).  Med en lösnings mall kan du automatisera att tillhandahålla mer än en resurs, inklusive virtuella datorer, nätverk och lagrings resurser för att tillhandahålla komplexa IaaS-lösningar.  Mer information om hur du skapar Solution-mallar finns i [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) -dokumentationen.

* Det **hanterade programmet** liknar Solution-mallar med en viktig skillnad. I ett hanterat program distribueras resurserna till en resursgrupp som hanteras av appens utgivare. Resursgruppen finns i kundens prenumeration, men en identitet i utgivarens klient har åtkomst till resursgruppen. Som utgivare anger du kostnaden för fortlöpande support för lösningen. Använd hanterade program för att enkelt skapa och leverera fullständigt hanterade, nyckel färdiga program till dina kunder.  Mer information om fördelarna och typerna av hanterade program finns i [Översikt över Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/overview).

Alla Azure-program innehåller minst två filer i rotmappen för ett `.zip` Arkiv:

* En Resource Manager-mallfil med namnet [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Detta är den mall som definierar vilka resurser som ska distribueras till kundens Azure-prenumeration.  Exempel på Resource Manager-mallar finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) eller motsvarande [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates) lagrings platsen.

* En definition av användar gränssnitt för att skapa Azure-program med namnet [createUiDefinition. JSON](https://docs.microsoft.com/azure/managed-applications/create-uidefinition-overview).  I användargränssnittet anger du element som ger konsumenterna möjlighet att ange parametervärden.

Alla nya Azure-programerbjudanden måste innehålla en [Azure-partner kund användnings-GUID](??).

### <a name="before-you-begin"></a>Innan du börjar

Läs följande dokumentation om Azure-program, som innehåller snabb starter, självstudier och exempel.

* [Förstå Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
* Snabbstarter:

    * [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/)
    * [GitHub Azure snabb starts mallar](https://github.com/azure/azure-quickstart-templates)
    * [Publicera program definition](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
    * [Distribuera tjänst katalog program](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

* Självstudier:

    * [Skapa definitionsfiler](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
    * [Publicera marknadsplatsprogram](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

* Stickprov

    * [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    * [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    * [Hanterade program lösningar](https://docs.microsoft.com/azure/managed-applications/sample-projects)

### <a name="fundamentals-in-technical-knowledge"></a>Grunderna i teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet.

Teknik teamet bör ha kunskap om följande Microsoft-tekniker:

* Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/).
* Hur du [utformar och arkitekterar Azure-program](https://azure.microsoft.com/solutions/architecture/).
* Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking#networking).
* Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/).
* Arbeta med [JSON](https://www.json.org/).

### <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera ditt Azure-program:

* [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar att du lägger till följande verktyg i utvecklings miljön:

* [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
* [Visual Studio Code](https://code.visualstudio.com/) med följande fil namns tillägg:
    * Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    * Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    * Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Du kan granska de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/tools/) -sidan.  Även om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-an-azure-application-offer"></a>Skapa ett erbjudande för Azure-program

Innan du kan skapa ett erbjudande för Azure-program måste du först [skapa ett partner Center-konto](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-account) och öppna [instrument panelen för instrument panelen](https://partner.microsoft.com/dashboard/commercial-marketplace/offers), med fliken **Översikt** vald.

>[!Note]
>När ett erbjudande har publicerats uppdateras bara ändringar i erbjudandet som gjorts i Partner Center i systemet och butiker efter ompubliceringen.  Kontrol lera att du har skickat in erbjudandet för publicering när du har gjort ändringar.

### <a name="create-a-new-offer"></a>Skapa ett nytt erbjudande

Välj knappen **+ ny erbjudande** och välj sedan meny alternativet för **Azure Application** . Dialog rutan **nytt erbjudande** visas.

### <a name="offer-id-and-alias"></a>Erbjudande-ID och alias

* **Erbjudande-ID**: en unik identifierare för varje erbjudande i ditt konto. Detta ID visas för kunder i URL-adressen för Marketplace-erbjudandet och Azure Resource Manager mallar (om tillämpligt). <br> <br> Ditt erbjudande-ID måste bestå av gemena alfanumeriska tecken (inklusive bindestreck och under streck, men inget blank steg). Den är begränsad till 50 tecken och kan inte ändras när du har valt skapa. <br> <br> Om du till exempel anger `test-offer-1` här, kommer URL: en för erbjudandet att `https://azuremarketplace.microsoft.com/marketplace/../test-offer-1`. 

* **Erbjudande Ali Aset**: namnet som används för att referera till erbjudandet i Partner Center. Det här namnet används inte på Marketplace och skiljer sig från namnet på erbjudandet och andra värden som ska visas för kunderna. Det går inte att ändra det här värdet när du har valt **skapa**.

När du har angett ditt **erbjudande-ID** och **erbjud alias**väljer du **skapa**. Du kommer sedan att kunna arbeta med alla andra delar av erbjudandet.

## <a name="offer-setup"></a>Erbjudande konfiguration

På sidan **erbjudande konfiguration** ber vi om följande information. Se till att välja **Spara** när du är klar med fälten.

### <a name="test-drive"></a>Testkör

En testen het är ett bra sätt att presentera ditt erbjudande för potentiella kunder genom att ge dem möjlighet att "prova innan du köper", vilket resulterar i ökad konvertering och skapandet av mycket kvalificerade leads. [Läs mer om test enheter.](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)

Markera kryss rutan **Aktivera en testenhet** om du vill aktivera en test enhet. Sedan måste du konfigurera en demonstrations miljö i den [tekniska konfigurationen för test enheten](#types-of-azure-application-plans) konfigurera så att kunderna kan testa ditt erbjudande under en viss tids period. 

>[!Note]
>Eftersom alla Azure-program implementeras med hjälp av en Azure Resource Manager-mall är den enda typen av test enhet som kan konfigureras för en Azure Application en [Azure Resource Manager baserad testen het](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/azure-resource-manager-test-drive).

#### <a name="additional-test-drive-resources"></a>Ytterligare resurser för enhets test

- [Test Drive tekniska metod tips](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Metod tips för marknadsföring av test enheter](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/marketing-and-best-practices)
- [Översikt över test enhet en pager](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf)

## <a name="connect-lead-management"></a>Anslut lead-hantering

[!INCLUDE [Connect lead management](./includes/connect-lead-management.md)]

Mer information finns i [Översikt över hantering av leads](./commercial-marketplace-get-customer-leads.md).

Kom ihåg att **Spara** innan du går vidare till nästa avsnitt!

## <a name="properties"></a>Egenskaper

På sidan **Egenskaper** kan du definiera de kategorier och branscher som ska användas för att gruppera ditt erbjudande på Marketplace, din app-version och de juridiska avtal som stöder ditt erbjudande. Välj **Spara** när du är klar med den här sidan.

### <a name="category"></a>Kategori

Välj minst en och högst tre kategorier som ska användas för att placera ditt erbjudande i lämpliga sökområden för Marketplace. Var noga med att ta reda på hur ditt erbjudande stöder dessa kategorier i beskrivningen av erbjudandet. 

### <a name="standard-marketplace-terms-and-conditions"></a>Standard villkor för Marketplace

För att förenkla inköps processen för kunder och minska den juridiska komplexiteten för program varu leverantörer erbjuder Microsoft en standard kontrakts mal len för att hjälpa till att under lätta en transaktion i Marketplace.

I stället för att använda anpassade allmänna villkor kan du välja att erbjuda program varan under standard kontraktet, som kunderna bara behöver Undersök och godkänna en gång.

Du hittar standard kontraktet här: https://go.microsoft.com/fwlink/?linkid=2041178

Om du vill använda standard kontraktet kontrollerar du rutan **Använd standard kontrakt?** .

#### <a name="terms-of-use"></a>Användningsvillkor

Om du inte markerar kryss rutan **Använd standard kontrakt?** måste du ange dina egna juridiska användnings villkor i fältet **användningsvillkor** . Ange upp till 10 000 tecken text, eller ange URL: en där du kan hitta ytterligare licens villkor om dina användnings villkor kräver en längre beskrivning. Kunder måste acceptera dessa villkor innan de kan testa din app.

## <a name="offer-listing"></a>Erbjudande lista

På sidan erbjudande lista visas de språk som ditt erbjudande visas i. För närvarande är **engelska (USA)** det enda tillgängliga alternativet.

Du måste definiera Marketplace-information (erbjudande namn, beskrivning, bilder osv.) för varje språk/marknad. Välj språk/marknads namn för att ange den här informationen.

> [!NOTE]
> Erbjudande för List innehåll (till exempel beskrivning, dokument, skärm bilder, användnings villkor osv.) behöver inte vara på engelska, förutsatt att beskrivningen börjar med frasen, "det här programmet är endast tillgängligt på [språk som inte är engelska]". Det är också acceptabelt att tillhandahålla en *användbar länk-URL* för att erbjuda innehåll på ett annat språk än det som används i innehålls förteckningen för erbjudandet.

### <a name="name"></a>Name

Det namn som du anger här visas för kunder som rubrik på din erbjudande lista. Det här fältet fylls i automatiskt med texten du angav för **erbjud alias** när du skapade erbjudandet, men du kan ändra det här värdet. Det här namnet kan vara ett varumärke (och du kan inkludera varumärkes-eller Copyright-symboler). Namnet får innehålla högst 50 tecken och får inte innehålla några emojis.

### <a name="summary"></a>Sammanfattning

Ange en kort beskrivning av ditt erbjudande (upp till 100 tecken) som kan användas i Sök resultaten för Marketplace.

### <a name="long-summary"></a>Lång Sammanfattning

Ange en längre Beskrivning av ditt erbjudande (upp till 256 tecken). Beskrivningen kan användas i Sök resultaten för Marketplace.

### <a name="description"></a>Beskrivning

Ange en längre Beskrivning av ditt erbjudande (upp till 3 000 tecken). Den här beskrivningen visas för kunder i Översikt över Marketplace-registrering. Ta med ditt erbjudandes värde, viktiga fördelar, kategori-och/eller bransch associationer, inköps möjligheter för appar och eventuella uppgifter som krävs. 

Några tips för att skriva din beskrivning:  

- Beskriv tydligt ditt erbjudande värde i de första meningarna i beskrivningen. Ta med följande objekt i ditt värde förslag:
  - Beskrivning av produkten
  - Typ av användare som fördelar från produkten
  - Kunden behöver eller smärta om produkt adresser
- Tänk på att de första meningarna kan visas i Sök motorns resultat.  
- Förlita dig inte på funktioner och funktioner för att sälja produkten. Fokusera i stället på det värde som du levererar.  
- Använd branschspecifika vokabulär eller kunskapsbaserade formuleringar så mycket som möjligt. 
- Överväg att använda HTML-taggar för att formatera beskrivningen och göra den mer intressant.

### <a name="search-keywords"></a>Sök efter nyckelord

Du kan också ange upp till tre Sök nyckelord för att hjälpa kunderna att hitta ditt erbjudande i Marketplace. För bästa resultat bör du även försöka använda dessa nyckelord i beskrivningen.

### <a name="support-urls"></a>Support-URL: er

I det här avsnittet kan du ange länkar som hjälper kunderna att förstå mer om ditt erbjudande.

#### <a name="privacy-policy-url"></a>URL för sekretess policy

Ange URL: en till din organisations sekretess policy. Du ansvarar för att se till att appen uppfyller sekretess lagar och föreskrifter och att tillhandahålla en giltig sekretess policy.

#### <a name="useful-links"></a>Användbara länkar

Ange valfria kompletterande online-dokument om din lösning.  Lägg till ytterligare användbara länkar genom att klicka på **+ Lägg till en länk**.

### <a name="contacts"></a>Kontakter

I det här avsnittet måste du ange namn, e-postadress och telefonnummer för en **support kontakt** och en **teknisk kontakt**. Den här informationen visas inte för kunder, men är tillgänglig för Microsoft och kan tillhandahållas till CSP-partner.

I avsnittet **support kontakt** måste du också ange **Support-URL:** en där CSP-partner kan hitta support för ditt erbjudande.

### <a name="marketplace-images"></a>Marketplace-avbildningar

I det här avsnittet kan du ange logo typer och avbildningar som ska användas när du visar ditt erbjudande för kunden. Alla bilder måste vara i PNG-format.

#### <a name="store-logos"></a>Butiks logo typer

Ange ditt erbjudandes logo typ i tre storlekar: **liten (48 x 48)** , **medium (90 x 90)** och **stor (216 x 216)** .

#### <a name="hero"></a>Hjälte

Hjälte avbildningen är valfri. Om du anger ett värde måste du mäta 815 x 290 pixlar.

#### <a name="screenshots"></a>Skärmbilder

Lägg till skärm bilder som visar hur ditt erbjudande fungerar. Du kan lägga till upp till fem skärm bilder. Alla skärm dum par måste vara 1280 x 720 bild punkter.

#### <a name="videos"></a>Videor

Du kan också lägga till upp till fem videor som demonstrerar ditt erbjudande. Dessa videor bör finnas på YouTube och/eller Vimeo. För var och en anger du videons namn, URL och en miniatyr bild av videon (1280 x 720 pixlar).

#### <a name="additional-marketplace-listing-resources"></a>Ytterligare platser för Marketplace-lista

- [Metod tips för Marketplace-erbjudanden](https://docs.microsoft.com/azure/marketplace/gtm-offer-listing-best-practices)

## <a name="preview"></a>Förhandsversion

På fliken **förhands granskning** kan du definiera en begränsad **förhands gransknings grupp** för att verifiera ditt erbjudande innan du publicerar erbjudandet Live till den bredare Marketplace-publiken.

> [!IMPORTANT]
> Du måste välja **Go Live** innan ditt erbjudande publiceras Live till offentlig Marketplace-publik när du har kontrollerat ditt erbjudande i för hands versionen.

Din förhands gransknings mål identifieras av ID: n för Azure-prenumerations-ID, tillsammans med en valfri beskrivning av varje.  Inget av dessa fält är synliga för kunderna.

Lägg till upp till 10 Azure-prenumerations-ID: n manuellt, eller upp till 100 om du laddar upp en CSV-fil.  Genom att lägga till dessa prenumerationer definierar du en mål grupp som ska få för hands versions åtkomst till ditt erbjudande innan den publiceras fullständigt.  Om erbjudandet redan är Live kan du fortfarande definiera en förhands gransknings grupp för att testa ändringar eller uppdateringar av ditt erbjudande.

>[!Note]
>Förhands gransknings publiken skiljer sig från en privat publik. En förhands gransknings mål grupp har åtkomst till ditt erbjudande *innan* de publiceras Live på Marketplace. Du kan också välja att skapa en plan och bara göra den tillgänglig för en privat mål grupp (med fliken prenumerations tillgänglighet).  Din förhands gransknings grupp kommer att kunna se och validera alla planer, inklusive planer som bara är tillgängliga för en privat mål grupp när ditt erbjudande är fullständigt publicerat på Marketplace.

## <a name="plan-overview"></a>Plan översikt

På fliken **plan översikt** kan du ange olika prenumerations alternativ i samma erbjudande. Dessa planer (kallas SKU: er i Cloud Partner Portal) kan variera beroende på typ av plan (lösnings mal len vs. hanterat program), monetarisering eller mål grupp.  Konfigurera minst en plan för att lista ditt erbjudande i Marketplace.

När du har skapat dina prenumerations namn, ID, plan typ, tillgänglighet (offentlig eller privat), aktuell publicerings status och alla tillgängliga åtgärder på den här fliken.

Vilka **åtgärder** som är tillgängliga i **plan översikten** varierar beroende på planens aktuella status och kan vara:

* Om plan status är **Draft** – ta bort utkast.
* Om plan status är **Live** – stoppa Sälj plan eller synkronisera privat publik.

### <a name="create-new-plan"></a>Skapa ny plan

***Plan-ID*** – skapa ett unikt plan-ID för varje plan i det här erbjudandet. Detta ID visas för kunder i produkt-URL: en.  Använd endast gemener, alfanumeriska tecken, bindestreck eller under streck. Högst 50 tecken tillåts för det här plan-ID: t. Detta ID kan inte ändras efter att du valt skapa.

***Plan namn*** – kunder ser det här namnet när du bestämmer vilken plan du vill välja i erbjudandet. Skapa ett unikt erbjudande namn för varje plan i det här erbjudandet. Plan namnet används för att särskilja program varu planer som kan vara en del av samma erbjudande (t. ex. Namn på erbjudande: Windows Server; planer: Windows Server 2016, Windows Server 2019).

### <a name="plan-setup"></a>Planera installationen

På fliken **plan konfiguration** kan du ange en hög nivå konfiguration för typen av plan, om den återanvänder paket från ett annat abonnemang och vilka moln planen ska vara tillgänglig i.  Dina svar på den här fliken kommer att påverka vilka fält som visas på andra flikar för samma plan.

#### <a name="plan-type"></a>Typ av plan

Som beskrivs i [typerna av Azure-programplaner](#types-of-azure-application-plans)väljer du om din plan ska innehålla en lösnings mall eller ett hanterat program.

#### <a name="this-plan-reuses-packages"></a>Den här planen återanvänder paket

Om du har mer än en plan av samma typ och paketen är identiska mellan dem kan du välja **det här schemat Återanvänd paket från ett annat abonnemang**.  När du väljer det här alternativet kommer du att kunna välja något av de andra planerna av samma typ för det här erbjudandet för att återanvända paket från. 

>[!Note]
>När du återanvänder paket från ett annat abonnemang försvinner fliken teknisk konfiguration från den här planen.  Den tekniska konfigurations informationen från den andra planen, inklusive eventuella uppdateringar som du gör i framtiden, kommer även att användas för den här planen. <br> <br> Dessutom kan den här inställningen inte ändras när den här planen har publicerats.

#### <a name="cloud-availability"></a>Moln tillgänglighet

Den här planen måste göras tillgänglig i minst ett moln. 

Välj alternativet **offentlig Azure** som gör att din lösning kan distribueras till kunder i alla offentliga Azure-regioner som har Marketplace-integration.  Lär dig mer om [geografisk tillgänglighet](https://docs.microsoft.com/azure/marketplace/marketplace-geo-availability-currencies).

Välj **moln alternativet Azure Government** som gör att din lösning kan distribueras i [Azure Government molnet](https://docs.microsoft.com/azure/azure-government/documentation-government-welcome), ett moln i samhället med kontrollerad åtkomst för kunder från amerikanska federala, statliga, lokala eller stambaserad och partner berättigade till att betjäna dessa entiteter.  Du, som utgivare, ansvarar för alla kontroller, säkerhets åtgärder och bästa praxis för att hantera den här moln gruppen.  Azure Government använder fysiskt isolerade Data Center och nätverk (finns endast i USA).  Innan du publicerar till [Azure Government](https://aka.ms/azuregovpublish)rekommenderar Microsoft att du testar och validerar din lösning i miljön eftersom vissa slut punkter kan skilja sig. Om du vill mellanlagra och testa din lösning ber vi dig att be om ett utvärderings konto från den här [länken](https://azure.microsoft.com/global-infrastructure/government/request/).

>[!Note]
>När en plan publiceras som tillgänglig i ett särskilt moln går det inte att ta bort molnet.

**Azure Government moln certifieringar**

Det här alternativet visas bara om **Azure Government Cloud** är valt under **moln tillgänglighet**.

Azure Government Services hanterar data som omfattas av vissa myndighets bestämmelser och krav, till exempel FedRAMP, NIST 800,171 (DIB), ITAR, IRS 1075, DoD L4 och CJIS.  För att få kännedom om dina certifieringar för dessa program kan du ge upp till 100 länkar som beskriver dina certifieringar.  Dessa länkar kan antingen vara länkar till din lista över programmet direkt, eller länkar till beskrivningar av dina efterlevnad med dem på dina egna webbplatser.  Dessa länkar visas bara för Azure Government moln kunder.

## <a name="plan-listing"></a>Plan lista

På fliken **plan List** visas den plan-/regionsspecifika registrerings informationen som kan skilja sig mellan olika planer för samma erbjudande.

### <a name="name"></a>Name

Fylldes i automatiskt baserat på ditt namn du tilldelade din plan när du skapade den.  Det här namnet visas som rubrik för den här "program planen" som visas i Marketplace.  Får innehålla upp till 100 tecken.

### <a name="summary"></a>Sammanfattning

Ange en kort sammanfattning av din program varu plan.  Får innehålla upp till 100 tecken.

### <a name="description"></a>Beskrivning

Den här beskrivningen är en möjlighet att förklara vad som gör att den här program varu planen är unik och eventuella skillnader jämfört med andra program varu planer i erbjudandet. Får innehålla upp till 2 000 tecken.

Välj **Spara** när du är klar med fälten.

## <a name="availability"></a>Tillgänglighet

Fliken **tillgänglighet** är bara synlig för lösnings mal len planer.  Du kan göra planen synlig för alla, endast för vissa kunder (en privat mål grupp) och om du vill att planen ska vara dold för användning av annan lösnings mall eller endast hanterade program.

### <a name="plan-audience"></a>Planera mål grupp

Du har möjlighet att konfigurera varje plan så att den blir synlig för alla eller bara för en speciell grupp som du väljer. Du kan tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azures prenumerations-ID.

**Sekretess/det här är en privat plan** (valfri kryss ruta) – Markera den här kryss rutan om du vill att planen ska vara privat och endast synlig för den begränsade gruppen som du väljer. När du har publicerat som privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan publiceras som synlig för alla måste den vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

**Begränsad mål grupp (ID för Azure-prenumeration)** – tilldela den mål grupp som ska ha åtkomst till den privata planen. Åtkomst tilldelas med hjälp av Azures prenumerations-ID med alternativet att inkludera en beskrivning av varje tilldelad Azure-prenumeration. Högst 10 prenumerations-ID: n kan läggas till, eller 20 000 kunders prenumerations-ID om du importerar en. csv-kalkylbladsfilen.  ID: n för Azure-prenumerationen visas som GUID och bokstäverna måste vara lägre-bokstäver.

>[!Note]
>Den privata mål gruppen (eller den begränsade mål gruppen) skiljer sig från den förhands visnings mål som du definierade på fliken [**Förhandsgranska**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  En förhands gransknings mål grupp har åtkomst till ditt erbjudande *innan* erbjudandet publiceras Live på Marketplace. Även om den privata mål beteckningen endast gäller för en speciell plan, kan förhands gransknings publiken Visa alla planer (privata eller inte) i validerings syfte.

### <a name="hide-plan"></a>Dölj plan

Om din lösnings mall är avsedd att distribueras endast indirekt när den refereras till, även om en annan lösning eller ett hanterat program, markerar du den här kryss rutan om du vill publicera din lösnings mall men dölja den från kunder som söker efter den direkt.

## <a name="pricing-and-availability"></a>Priser och tillgänglighet

Fliken **priser och tillgänglighet** är endast synlig för hanterade program planer.  Du kan konfigurera de marknader som den här planen kommer att vara tillgänglig i, priset per månad för hantering av lösningen och om du vill göra planen synlig för alla eller bara för specifika kunder (en privat mål grupp).

### <a name="markets"></a>Marknaden

Varje plan måste vara tillgänglig på minst en marknad. Markera kryss rutan för alla marknads platser där du vill att den här planen ska vara tillgänglig. En sökruta och knapp för att välja "skatte mottagare", där Microsoft betalar försäljnings-och användnings skatt för din räkning, ingår i hjälpen.

Om du redan har angett priser för din plan i USA dollar (USD) och lagt till en annan marknads plats kommer priset för den nya marknaden att beräknas enligt de aktuella växelkurserna. Granska alltid priset för varje marknad innan du publicerar. Priserna kan granskas med hjälp av länken "export priser (xlsx)" när du har sparat ändringarna.

### <a name="pricing"></a>Prissättning

Ange priset per månad för den här planen.  Detta pris är utöver alla Azure-infrastrukturer eller program kostnader som du betalar per användning av de resurser som distribueras av den här lösningen.

Priser som anges i lokal valuta (USD = USA dollar) konverteras till den lokala valutan för alla valda marknader med de aktuella växelkurserna som är tillgängliga under installationen. Validera dessa priser innan du publicerar genom att exportera pris kalkyl bladet och granska priset på varje marknad. Om du vill ange anpassade priser på en enskild marknad ändrar du och importerar pris kalkyl bladet. 

>[!Note]
>Du måste först spara dina pris ändringar för att möjliggöra export av pris information.

Granska dina priser noggrant innan du publicerar eftersom det finns vissa begränsningar för vad som kan ändras när en plan har publicerats.  

>[!Note]
>När ett pris för en marknad i planen har publicerats kan det inte ändras senare.

### <a name="plan-audience"></a>Planera mål grupp

Du har möjlighet att konfigurera varje plan så att den blir synlig för alla eller bara för en speciell grupp som du väljer. Du kan tilldela medlemskap i den här begränsade mål gruppen med hjälp av Azures prenumerations-ID.

**Sekretess/det här är en privat plan** (valfri kryss ruta) – Markera den här kryss rutan om du vill att planen ska vara privat och endast synlig för den begränsade gruppen som du väljer. När du har publicerat som privat plan kan du uppdatera mål gruppen eller välja att göra planen tillgänglig för alla. När en plan publiceras som synlig för alla måste den vara synlig för alla. (Planen kan inte konfigureras som en privat plan igen).

**Begränsad mål grupp (ID för Azure-prenumeration)** – tilldela den mål grupp som ska ha åtkomst till den privata planen. Åtkomst tilldelas med hjälp av Azures prenumerations-ID med alternativet att inkludera en beskrivning av varje tilldelad Azure-prenumeration. Högst 10 prenumerations-ID: n kan läggas till, eller 20 000 kunders prenumerations-ID om du importerar en. csv-kalkylbladsfilen.  ID: n för Azure-prenumerationen visas som GUID och bokstäverna måste vara lägre-bokstäver.

>[!Note]
>Den privata mål gruppen (eller den begränsade mål gruppen) skiljer sig från den förhands visnings mål som du definierade på fliken [**Förhandsgranska**](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-saas-offer#preview) .  En förhands gransknings mål grupp har åtkomst till ditt erbjudande *innan* erbjudandet publiceras Live på Marketplace. Även om den privata mål beteckningen endast gäller för en speciell plan, kan förhands gransknings publiken Visa alla planer (privata eller inte) i validerings syfte.

## <a name="technical-configuration"></a>Teknisk konfiguration 

På fliken **teknisk konfiguration** kan du överföra distributions paketet som gör det möjligt för kunder att distribuera planen.

>[!Note]
>Den här fliken visas inte om du har konfigurerat den här planen för att återanvända paket från en annan plan på fliken **Planera konfiguration** .

### <a name="package-details"></a>Paket information

Med **paket information** underfliken kan du redigera utkast versionen av din tekniska konfiguration.

***Version*** – tilldela den aktuella versionen av den tekniska konfigurationen.  Öka den här versionen varje gången du publicerar en ändring på den här sidan. Versionen måste vara i formatet `{integer}.{integer}.{integer}`.

***Paketfil*** (`.zip`) – det här paketet innehåller alla mallfiler som behövs för den här planen, samt ytterligare resurser, paketerade som en `.zip` fil.

Alla paket för Azure-programplanen måste innehålla de här två filerna i rotmappen för ett `.zip` Arkiv:

* En Resource Manager-mallfil med namnet [mainTemplate. JSON](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).  Den här mallen automatiserar distributionen av resurser till Azure-prenumerationen för kunder.  Exempel på Resource Manager-mallar finns i [galleriet för Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/) eller motsvarande [GitHub: Azure Resource Manager snabb starts mallar](https://github.com/azure/azure-quickstart-templates) lagrings platsen.

* En definition av användar gränssnitt för att skapa Azure-program med namnet [createUiDefinition. JSON](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

Alla nya erbjudanden för Azure-program måste också innehålla ett GUID för [Azure-partner kund användnings behörighet](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution) .

### <a name="previously-published-packages"></a>Tidigare publicerade paket 

Med underfliken **tidigare publicerade paket** kan du Visa alla publicerade versioner av den tekniska konfigurationen.

## <a name="technical-configuration-managed-application-plans-only"></a>Teknisk konfiguration (endast hanterade program planer)

Hanterade program planer har ytterligare komplexitet på fliken **teknisk konfiguration** utöver fälten **version** och **paket fil** som beskrivs ovan. 

### <a name="enable-just-in-time-jit-access"></a>Aktivera JIT-åtkomst (just-in-Time)

Välj det här alternativet för att aktivera JIT-åtkomst (just-in-Time) för den här planen.  Med JIT-åtkomst kan du begära utökad åtkomst till ett hanterat programs resurser för fel sökning eller underhåll. Du har alltid skrivskyddad åtkomst till resurserna, men under en viss tids period kan du ha större åtkomst.  Mer information finns i [Aktivera och begär just-in-Time-åtkomst för Azure Managed Applications](https://docs.microsoft.com/azure/managed-applications/request-just-in-time-access).  Om du vill kräva att konsumenter av ditt hanterade program ger ditt konto permanent åtkomst, lämnar du alternativet omarkerat.

>[!Note]
>Se till att uppdatera `createUiDefinition.json`-filen för att stödja den här funktionen.  

### <a name="deployment-mode"></a>Distributions läge

Välj om du vill konfigurera **slutfört** eller **stegvis distributions läge** när du distribuerar den här planen: 

* I **komplett läge**leder en omdistribution av programmet av kunden till att resurser i den hanterade resurs gruppen tas bort om resurserna inte är definierade i `mainTemplate.json`. 
* I **stegvist läge**lämnar en omdistribution av programmet befintliga resurser oförändrade.

Läs mer om distributions lägen i [Azure Resource Manager distributions lägen](https://docs.microsoft.com/azure/azure-resource-manager/deployment-modes).

### <a name="notification-endpoint-url"></a>URL för meddelande slut punkt

Ange en HTTPS webhook-slutpunkt för att ta emot aviseringar om alla CRUD-åtgärder på hanterade program instanser av den här plan versionen.

### <a name="customize-allowed-customer-actions"></a>Anpassa tillåtna kund åtgärder

Välj det här alternativet om du vill ange vilka åtgärder som kunder kan utföra på de hanterade resurserna utöver de "`*/read`"-åtgärder som är tillgängliga som standard. 

Lista de ytterligare åtgärder som du vill att kunden ska utföra här, avgränsade med semikolon.  Mer information finns i [förstå neka tilldelningar för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments).  Information om tillgängliga åtgärder finns i [Azure Resource Manager Resource Provider-åtgärder](https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations). Om du till exempel vill tillåta att användare startar om virtuella datorer, lägger du till `Microsoft.Compute/virtualMachines/restart/action` i de tillåtna åtgärderna.

### <a name="global-azure--azure-government-cloud"></a>Globalt Azure/Azure Government-moln

Ange vem som ska ha hanterings åtkomst till det här hanterade programmet i varje moln som stöds.  Användare, grupper eller program som du vill ska beviljas behörighet till den hanterade resurs gruppen identifieras med hjälp av Azure Active Directory-identiteter (AAD).

***Azure Active Directory klient-ID*** : AAD-klient-ID (även kallat katalog-ID) som innehåller identiteterna för de användare, grupper eller program som du vill ge behörighet till.  Du hittar ditt AAD-klient-ID på Azure Portal i [Egenskaper för Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties).

***Auktoriseringar*** – lägg till Azure Active Directory objekt-ID för den användare, grupp eller program som du vill bevilja behörighet till den hanterade resurs gruppen. Identifiera användaren med sitt huvud-ID, som finns på [bladet Azure Active Directory användare på Azure Portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers).

För varje huvud konto väljer du en av de inbyggda Azure AD-rollerna i listan (ägare eller deltagare). Rollen du väljer beskriver de behörigheter som huvud kontot kommer att ha på resurserna i kund prenumerationen. Mer information finns i [Inbyggda roller för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).  Mer information om rollbaserad åtkomst kontroll (RBAC) finns i [Kom igång med RBAC i Azure Portal](https://docs.microsoft.com/azure/role-based-access-control/overview).

>[!Note]
>Även om du kan lägga till upp till 100-auktoriseringar per moln är det oftast enklare att skapa en Active Directory användar grupp och ange dess ID i "huvud-ID".  På så sätt kan du lägga till fler användare i hanterings gruppen när planen har distribuerats och minska behovet av att uppdatera planen för att lägga till fler auktoriseringar.

### <a name="policy-settings"></a>Principinställningar

Använd [Azure-principer](https://docs.microsoft.com/azure/governance/policy/overview) för det hanterade programmet för att ange krav för kompatibilitet för den distribuerade lösningen.  Du kan läsa om principdefinitioner och parametervärdenas format i [Azure Policy-exempel](https://docs.microsoft.com/azure/governance/policy/samples/index).  Du kan konfigurera högst fem principer och bara en instans av varje princip alternativ.  Vissa principer kräver ytterligare parametrar.  Standard-SKU: n krävs för gransknings principer.  Princip namnet är begränsat till 50 tecken.

## <a name="co-sell"></a>Co-försäljning

Att tillhandahålla information på fliken förs är helt valfritt för att publicera erbjudandet. Det krävs för att uppnå en färdig status för alla färdiga och IP-adresser för samtidig försäljning. Den information du anger kommer att användas av Microsoft Sales Teams för att lära dig mer om din lösning när du utvärderar den efter kund behov. Den är inte tillgänglig direkt för kunderna.

Mer information om hur du slutför den här fliken finns [i Co-Sälj alternativet i Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-co-sell).

## <a name="test-drive"></a>Testkör

På fliken **testenhet** kan du konfigurera en demonstration (eller "testenhet") som gör det möjligt för kunder att prova ditt erbjudande innan de genomför det. Läs mer i artikeln [Vad är en test enhet?](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/test-drive/what-is-test-drive)  Om du inte längre vill ange en testen het för ditt erbjudande går du tillbaka till sidan **erbjudande konfiguration** och avmarkerar **Aktivera test enhet**.

### <a name="technical-configuration"></a>Teknisk konfiguration

Azure-program använder Azure Resource Manager test enhets typ.  Mer information finns i [teknisk konfiguration för Azure Resource Manager Test Drive](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-new-customer-engagement-offer#technical-configuration-for-azure-resource-manager-test-drive) .

### <a name="deployment-subscription-details"></a>Information om distributions prenumeration

För att kunna distribuera test enheten åt dig kan du skapa och tillhandahålla en separat, unik Azure-prenumeration. (Krävs inte för Power BI test enheter).

- **ID för Azure-prenumeration** (krävs för Azure Resource Manager-och logi Kap par): ange prenumerations-ID för att bevilja åtkomst till dina Azure-Kontotjänster för rapportering och fakturering av resursanvändning. Vi rekommenderar att du [skapar en separat Azure-prenumeration](https://docs.microsoft.com/azure/billing/billing-create-subscription) som ska användas för test enheter om du inte redan har en. Du hittar ditt prenumerations-ID för Azure genom att logga in på [Azure Portal](https://portal.azure.com/) och navigera till fliken **prenumerationer** på den vänstra menyn. Om du väljer fliken visas ditt prenumerations-ID (t. ex. "a83645ac-1234-5AB6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (krävs): ange ditt Azure Active Directory (AD) [klient organisations-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer * * egenskaper och letar efter **katalog-ID** -nummer som anges (t. ex. 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp din organisations klient-ID med hjälp av din domän namns webb adress på: [https://www.whatismytenantid.com](https://www.whatismytenantid.com).

- **Namn på Azure AD-klient** (krävs för dynamisk 365): ange ditt Azure Active Directory (AD) namn. Du hittar det här namnet genom att logga in på [Azure Portal](https://portal.azure.com/)i det övre högra hörnet ditt klient namn visas under ditt konto namn.

- **ID för Azure AD-App** (krävs): ange ditt Azure Active Directory (AD) [program-ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Appregistreringar**och letar sedan efter det **program-ID** som anges (t. ex. 50c464d3-4930-494c-963c-1e951d15360e).

- **Azure AD-appens klient hemlighet** (krävs): Ange din Azure AD-programs [klient hemlighet](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#certificates-and-secrets). Du hittar det här värdet genom att logga in på [Azure Portal](https://portal.azure.com/). Välj fliken **Azure Active Directory** på den vänstra menyn, Välj **Appregistreringar**och välj sedan appen Test Drive. Välj sedan **certifikat och hemligheter**, Välj **ny klient hemlighet**, ange en beskrivning, Välj **aldrig** under **upphör ande**och välj sedan **Lägg till**. Glöm inte att kopiera värdet. (Du kan inte navigera bort från sidan innan du har kopierat värdet, eller så har du inte till gång till värdet.)

Kom ihåg att **Spara** innan du går vidare till nästa avsnitt!

### <a name="test-drive-listings-optional"></a>Testa enhets listor (valfritt)

Alternativet **test enhets listor** på fliken **testenhet** visar de språk (och marknader) där test enheten är tillgänglig, för närvarande är engelska (USA) den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera information om test enheten (beskrivning, Användar handbok, videor osv.) för varje språk/marknad.

- **Beskrivning** (krävs): Beskriv test enheten, vad som visas, mål för användaren att experimentera med, vilka funktioner som ska utforskas och all relevant information som hjälper användaren att avgöra om ditt erbjudande ska erhållas. Upp till 3 000 tecken text kan anges i det här fältet. 

- **Åtkomst information** (krävs för Azure Resource Manager-och logik test enheter): förklara vad en kund behöver veta för att få åtkomst till och använda den här test enheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör känna till för att få åtkomst till funktioner i hela test enheten. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användar handbok** (krävs): en djupgående genom gång av test enhets upplevelsen. Användar handboken bör bestå av exakt vad du vill att kunden ska få från att använda test enheten och fungera som referens för alla frågor som de kan ha. Filen måste vara i PDF-format och ha namnet (högst 255 tecken) efter överföringen.

- **Videor: Lägg till videor** (valfritt): videor kan laddas upp till YouTube eller Vimeo och refereras till med en länk och miniatyr bild (533 x 324 bild punkter) så att en kund kan visa en genom gång av information för att hjälpa dem att bättre förstå test enheten, inklusive hur du kan använda funktionerna i ditt erbjudande och förstå scenarier som fokuserar på deras fördelar.
  - **Namn** (obligatoriskt)
  - **URL (endast YouTube eller Vimeo)** (obligatoriskt)
  - **Miniatyr (533 x 324 BPT)** : bild filen måste vara i PNG-format.

Välj **Spara** när du är klar med fälten.

## <a name="publish"></a>Publicera

### <a name="submit-offer-to-preview"></a>Skicka erbjudande till för hands version

När du har slutfört alla obligatoriska avsnitt i erbjudandet väljer du **publicera** i det övre högra hörnet i portalen. Du kommer att omdirigeras till sidan **Granska och publicera** . 

Om det är första gången du publicerar det här erbjudandet kan du:

- Se slut för ande status för varje avsnitt i erbjudandet.
    - *Inte startad* – innebär att avsnittet inte har vidrör ATS och måste slutföras.
    - *Uncompleted* – innebär att avsnittet innehåller fel som behöver åtgärdas eller som kräver mer information. Gå tillbaka till avsnitten och uppdatera det.
    - *Complete* – innebär att avsnittet har slutförts, att alla nödvändiga data har angetts och att det inte finns några fel. Alla avsnitt i erbjudandet måste vara i ett komplett tillstånd innan du kan skicka in erbjudandet.
- Tillhandahåll test instruktioner till certifierings teamet för att se till att appen testas korrekt, förutom eventuella kompletterande anteckningar som hjälper dig att förstå din app.
- Skicka in erbjudandet om publicering genom att välja **Skicka**. Vi skickar dig ett e-postmeddelande för att meddela dig när en för hands version av erbjudandet kan granskas och godkännas. Gå tillbaka till Partner Center och välj **Go-Live** för erbjudandet att publicera ditt erbjudande till allmänheten (eller om ett privat erbjudande till den privata mål gruppen).

### <a name="errors-and-review-feedback"></a>Fel och granska feedback

Det **manuella validerings** steget i publicerings processen representerar en omfattande granskning av ditt erbjudande och dess tillhör ande tekniska till gångar (särskilt Azure Resource Manager mal len), problem visas vanligt vis som pull-begäranden (PR). En förklaring av hur du kan visa och svara på dessa pull finns i [hantera granskning av feedback](./azure-apps-review-feedback.md).

Om du har påträffat fel i ett eller flera publicerings steg måste du korrigera dem och publicera erbjudandet på annat sätt.

## <a name="next-steps"></a>Nästa steg

- [Uppdatera ett befintligt erbjudande i Commercial Marketplace](./update-existing-offer.md)
