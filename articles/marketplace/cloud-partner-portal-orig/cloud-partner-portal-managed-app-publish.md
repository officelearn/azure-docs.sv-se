---
title: Publicera en Azure-hanterade program på Azure Marketplace
description: Publicera en Azure-hanterade program på Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 6e5bcd6d9923b4051d44d51ff1a2534bc5e02f41
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54445668"
---
<a name="publish-an-azure-managed-application-to-azure-marketplace"></a>Publicera ett Azure-hanterade program på Azure Marketplace 
========================================================

Den här artikeln innehåller olika steg som ingår i Publicera ett hanterat program erbjudande på Azure Marketplace.

<a name="pre-requisites-for-publishing-a-managed-application"></a>Förutsättningar för att publicera ett hanterat program 
---------------------------------------------------

Förutsättningar för att lista på Azure Marketplace

1.  Teknik

    -   [Skapa Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

    -   Azure Quickstart-mallar:

        -   <https://azure.microsoft.com/documentation/templates/>

        -   <https://github.com/azure/azure-quickstart-templates>

    -   Skapa UI-Definition

        -   [Skapa användare definitionsfilen för gränssnittet](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

2.  Icke-teknisk (affärskrav)

    -   Ditt företag (eller dess filial) måste finnas i en säljer ursprungsland som stöds av Azure Marketplace

    -   Produkten måste ha licens på ett sätt som är kompatibel med faktureringsmodellerna som stöds av Azure Marketplace

    -   Du ansvarar för att ge teknisk support för dina kunder: kostnadsfri, betald, eller via support av communityn.

    -   Du är ansvarig för licensiering för ditt program och eventuella beroenden för programvara från tredje part.

    -   Du måste ange innehåll som uppfyller villkoren för ditt erbjudande ska visas på Azure Marketplace och i Azure-hanteringsportalen

    -   Du måste godkänna villkoren i Deltagandepolicyer för Azure Marketplace och avtalet för utgivare

    -   Du måste godkänna att följa användningsvillkor, Microsofts sekretesspolicy och Microsoft Azure Certified-programavtalet.

<a name="how-to-create-a-new-azure-application-offer"></a>Så här skapar du ett nytt erbjudande för Azure-program 
-------------------------------------------

När alla krav har uppfyllts, är du redo att börja skapa ditt erbjudande om hanterat program. Innan vi börjar, en snabb överblick över ett erbjudande och en SKU

**Erbjudande**

Ett program för Azure-erbjudande motsvarar en klass av produkten tjänsterbjudande från en utgivare. Om du har en ny lösning/program som ska publiceras på Azure Marketplace är bättre att i ett nytt erbjudande. Ett erbjudande är en samling SKU: er. Varje erbjudande visas som en egen enhet på Azure Marketplace.

**SKU**

En SKU är ett erbjudande med den minsta köpbara enheten. Under samma produkt klass (erbjudandet), kan du skilja mellan olika funktioner som stöds, om erbjudandet är hanterade eller ohanterade och faktureringsmodellerna med SKU: er.

Lägg till flera SKU: er om du vill att stödja olika faktureringsmodellerna: till exempel Bring Your Own License (BYOL), betala efter användning (PAYG), osv. 

Lägg till flera SKU: er när varje SKU: N stöder en olika funktionsuppsättningar, och prissätts på olika sätt.

En SKU som visas i det överordnade erbjudandet på Azure Marketplace när den visas som en egen köpbara enhet på azure.com.

1.  Logga in på den [Cloud Partner Portal](http://cloudpartner.azure.com).

2.  Från det vänstra navigeringsfältet, klickar du på \"+ nytt erbjudande\" och välj \"Azure Applications\".

    ![Nytt erbjudande](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Ett nytt erbjudande \"redigeraren\" Visa nu öppnas och du kan börja redigera.

4.  Den \"formulär\" som behöver anges visas till vänster i den \"redigeraren\" vy. Varje \"formuläret\" består av en uppsättning fält som ska fyllas i. Obligatoriskt fält är markerade med en röd asterisk (\*).

    > Det finns 4 huvudformulär för redigering av ett hanterat program

    -   Erbjudandeinställningar

    -   SKU:er

    -   Marketplace

    -   Support

<a name="how-to-fill-out-the-offer-settings-form"></a>Hur du fyller i formuläret erbjuder inställningar 
---------------------------------------

Erbjudandet inställningar formuläret är en grundläggande och ange inställningar för erbjudandet.
Olika fält beskrivs nedan.

**Erbjudande-ID**

En unik identifierare för erbjudandet i en utgivarprofil för.
Det kommer att synas på produkten URL: er, Resource Manager-mallar och fakturering rapporter. Endast gemena alfanumeriska tecken eller bindestreck (-) tillåts. Det kan inte sluta med ett bindestreck eller överstiga 50 tecken. Det här fältet är låst när ett erbjudande lanseras.

**Utgivar-ID**

Den här listrutan kan du välja publisher-profil som du vill publicera det här erbjudandet under. Det här fältet är låst när ett erbjudande lanseras.

**Namn**

Visningsnamn för ditt erbjudande. Namnet som visas i Azure Marketplace och i Azure-portalen. Det får innehålla högst 50 tecken. Här vägledningen är att inkludera ett beskrivande namn för varumärke för din produkt. Omfattar inte ditt företagsnamn, såvida inte som är hur den släpps. Om du marknadsföring erbjudandet på din egen webbplats, se till att namnet exakt hur den visas på din webbplats.

Klicka på \"spara\" spara förloppet. Nästa steg är att lägga till SKU: er för ditt erbjudande.

<a name="how-to-create-skus"></a>Så här skapar du SKU: er 
------------------

Klicka på den \"SKU: er\" formuläret. Här kan du se ett alternativ för att \"Lägg till en SKU\" Klicka på vilket gör att du kan ange en \"SKU ID\".

![nytt erbjudande om SKU: er](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

\"SKU-ID\" är en unik identifierare för SKU: N i ett erbjudande. Detta ID kommer att synas på produkten URL: er, ARM-mallar och fakturering rapporter. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-).
Det får inte sluta med ett bindestreck och kan ha högst 50 tecken. Det här fältet är låst när ett erbjudande lanseras. Du kan ha flera SKU:er i ett erbjudande. Du behöver en SKU för varje bild som du planerar att publicera.

När en SKU har lagts till, visas den i listan över SKU: er inom den \"SKU: er\" formuläret. Klicka på SKU-namnet för att få information om denna SKU. Här är viss information för vissa fält.

När du klickar på \"nya SKU: N\", måste du fylla i följande format:

![Nytt erbjudande - nya SKU: N](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Hur du fyller Sku informationsavsnittet 

**Rubrik** – innehåller en rubrik för denna Sku. Det här är vad kommer att visas i galleriet för det här objektet.

**Sammanfattning av** – innehåller en kort sammanfattning för denna sku. Den här texten visas direkt under rubriken.

**Beskrivning av** – innehåller en detaljerad beskrivning om SKU: N.

**SKU-typen** -tillåtna värden är \"Managed Application\" och \"Lösningsmallar\". För det här fallet väljer du \"Managed Application\".

### <a name="how-to-fill-package-details-section"></a>Hur du fyller Paketinformation avsnittet 

Paket-avsnittet innehåller följande fält som måste fyllas i

![Nytt erbjudande - nya SKU-paketet](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_package.png)

**Aktuell Version** – innehåller en version för det paket som du kommer att överföra.
Det bör vara i formatet.

**Paketfil** -paketet innehåller följande filer som är komprimerade i en .zip-fil.

applianceMainTemplate.json - distribution mallfilen som används för att distribuera lösningen/program och skapa de resurser som definierats i den. Du hittar mer information om hur du författare distribution mallfiler här – <https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template>

applianceCreateUIDefinition.json – den här filen används av portalen på azure.com för att generera användargränssnittet för att etablera den här lösningen/application. Du hittar mer information om hur du skapar den här filen här – <https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview>

mainTemplate.json - mallfilen som innehåller endast Microsoft.Solution/appliances resursen. Nyckelegenskaperna för den här resursen ska vara medveten om följande:

-   \"typ\" -värdet bör vara \"Marketplace\" när det gäller Marketplace-hanterade program scenario
-   \"ManagedResourceGroupId\": resursgruppen i kunden\'s prenumeration där alla resurser som definierats i applianceMainTemplate.json ska distribueras.
-   \"Formatet för PublisherPackageId\": sträng som unikt identifierar paketet. 

Värdet måste således – det är en sammansättning av \[publisherId\].\[ OfferId\]-förhandsversion\[SKUID\].\[ PackageVersion\].
Var och en av dessa värden kan hämtas som visas nedan.

Det här paketet innehåller kapslade mallar eller skript som krävs för att etablera har det här programmet. MainTemplate.json, applianceMainTemplate.json och applianceCreateUIDefinition.json måste finnas i rotmappen.

**Auktoriseringar** – den här egenskapen definierar vilka som får åtkomst och vilken åtkomstnivå till resurserna i prenumerationer för kunder. Detta gör att utgivaren för att hantera programmet för kundens räkning.

-   PrincipalId – den Azure Active directory-identifieraren för en användare, grupp eller program som kommer att beviljas särskilda behörigheter (som beskrivs av rolldefinitionen) på resurserna i en kundprenumeration.

-   Rolldefinitionen – en lista över alla de inbyggda RBAC-roller tillhandahålls av Azure AD. Du kan välja den lämpligaste, rollen som hjälper dig att hantera resurserna för kundens räkning.

Flera godkännanden kan läggas till. Men det rekommenderas att skapa en Active Directory-användargrupp och ange dess ID i den \"PrincipalId\.”  Detta kommer att lägga till fler användare i användargruppen utan att behöva uppdatera SKU: N.

Mer information om RBAC finns här- <https://docs.microsoft.com/azure/active-directory/role-based-access-control-what-is>

<a name="marketplace-form"></a>Marketplace-formulär
----------------

Formuläret marketplace inom ett erbjudande för azure-program begär fält som visas på [Azure Marketplace](https://azuremarketplace.microsoft.com) på [Azure-portalen](https://portal.azure.com/). Här är viss information för vissa fält.

#### <a name="preview-subscription-ids"></a>Förhandsversion av prenumerations-ID: N

Ange en lista med ID i Azure-prenumeration som du vill ha åtkomst till erbjudandet när den publiceras. Dessa white visas prenumerationer gör att du kan testa förhandsgranskade erbjudandet innan du gör den live. Partnerportalen låter dig placera upp till 100 prenumerationer i listan över tillåtna.

#### <a name="suggested-categories"></a>Föreslagna kategorier

Välj upp till fem kategorier från den angivna listan som ditt erbjudande bäst kan associeras med. De valda kategorierna som används för att mappa ditt erbjudande till produktkategorierna i [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](https://portal.azure.com/).

Här följer några av de platser som de data som du anger i det här formuläret som visas i.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)

![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)

![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)

##### <a name="portal-at-azurecom"></a>Portalen på azure.com

![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)

![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)

##### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Alla logotyper som laddades upp i Cloud Partner Portal bör följa den riktlinjerna nedan:

-   Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.

-   Temafärger i portalen på azure.com är vit och svart. Undvik att använda dessa färger som bakgrundsfärgen för din logotyper. Använd en färg som gör din logotyper framstående i portalen på azure.com.
    Vi rekommenderar enkla primärfärger. **Om du använder genomskinlig bakgrund, se till att logotyper och text inte är vit, svart eller blå.**

-   Använd inte en toning bakgrund på logotypen.

-   Undvika att placera text, även företaget eller varumärken namn på logotypen.
    Utseendet och känslan av din logotyp måste vara \'fast\' och du bör undvika att toningar.

-   Undvik att utvidga logotypen.

##### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfritt. Utgivaren kan du inte ladda upp en Hero-logotyp. Men en gång överförda ikonen hero-komponenten inte kan tas bort. Partnern måste i så fall följer du riktlinjerna för Azure Marketplace för Hero-ikoner.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Riktlinjer för ikonen Hero-logotyp

-   Visningsnamn för utgivaren, plan rubrik och erbjudandet lång sammanfattning visas i vit teckenfärg. Undvik att hålla alla ljusare i bakgrunden på Hero-ikonen. Svart, vit och transparent bakgrunder tillåts inte för Hero-ikoner.

-   När erbjudandet hämtar listan visningsnamn för utgivaren, plan rubrik, lång sammanfattning av erbjudandet och knappen Skapa programmässigt bäddas in i Hero-logotyp. Du behöver inte ange valfri text när du utformar Hero-logotypen. Lämna tomt utrymme till höger för utgivaren visa namnet, plan rubrik, lång sammanfattning av erbjudandet och osv. De ingår programmässigt.
    Tom blanksteg för texten bör vara 415 × 100 till höger och förskjutas av 370 px från vänster.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

<a name="support-form"></a>Stödformulär
------------

Nästa formuläret att fylla i är support. Det här formuläret begär supportkontakter från ditt företag.  Några exempel är din engineering kontaktinformation och kontaktinformation för support för kunden.

<a name="how-to-publish-an-offer"></a>Hur du publicerar ett erbjudande
-----------------------

Nu när ditt erbjudande upprättats, är nästa steg att publicera erbjudandet på Azure Marketplace. Klicka på den \"publicera\" knappen Starta processen med att göra ditt erbjudande live.
