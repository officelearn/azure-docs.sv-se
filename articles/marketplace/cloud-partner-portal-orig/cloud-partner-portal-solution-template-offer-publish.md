---
title: Publicera en Lösningsmall | Microsoft Docs
description: Publicera en mall på Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: pbutlerm
ms.openlocfilehash: c7c7912860568aea497e327f29a1b7b71b8f5e87
ms.sourcegitcommit: 96527c150e33a1d630836e72561a5f7d529521b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2018
ms.locfileid: "51345597"
---
# <a name="publish-a-solution-template-to-azure-marketplace"></a>Publicera en Lösningsmall Azure Marketplace

Den här artikeln innehåller steg för att publicera en Lösningsmall erbjuder Azure Marketplace.

## <a name="prerequisites"></a>Förutsättningar

Följande krav för teknisk och icke-teknisk gäller för att visa en lista över en lösningsmall på Azure Marketplace.

### <a name="technical"></a>Teknik

- [Förstå Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

- Azure Quickstart-mallar:

    - [Snabbstart för Azure-malldokumentationen](https://azure.microsoft.com/documentation/templates/)

    - [Snabbstart för Azure-dokumentation på GitHub](https://github.com/azure/azure-quickstart-templates)

 - [Skapa Azure portalanvändare gränssnittet fil](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

### <a name="non-technical-business-requirements"></a>Icke-teknisk (affärskrav)

-   Ditt företag (eller dess filial) måste finnas i en säljer från stöds av Azure Marketplace.

-   Produkten måste licensieras på ett sätt som är kompatibel med faktureringsmodellerna som stöds av Azure Marketplace.

-   Du är ansvarig för att göra teknisk support tillgänglig för kunder på ett kommersiellt rimligt sätt, oavsett om de är kostnadsfria, betald eller via support av communityn.

-   Du är ansvarig för att licensiera ditt program och eventuella beroenden för programvara från tredje part.

-   Ange innehåll som uppfyller villkoren för ditt erbjudande ska visas på Azure Marketplace och i Azure-hanteringsportalen.

-   Samtycker till villkoren i Deltagandepolicyer för Azure Marketplace och avtalet för utgivare.

-   Acceptera användningsvillkoren, Microsofts sekretesspolicy och Microsoft Azure Certified-programavtalet.

## <a name="before-you-begin"></a>Innan du börjar

Du kan börja redigera erbjudandet lösningsmallen när du har uppfyllt alla krav. Innan du börjar måste du granska följande erbjudande och SKU-information.

**Erbjudande**

Ett program för Azure-erbjudande motsvarar en klass av produkten tjänsterbjudande från en utgivare. Om du har en ny typ av lösning/program som du vill ska vara tillgängliga i Azure Marketplace är ett nytt erbjudande den bästa metoden. Ett erbjudande är en samling SKU: er. Varje erbjudande visas som en egen enhet på Azure Marketplace.

**SKU**

En SKU är den minsta köpbara enheten i ett erbjudande. Under samma produkt klass (erbjudandet), kan du skilja mellan olika funktioner som stöds i SKU: er. Till exempel erbjudandet är hanterade eller ohanterade och olika faktureringsmodellerna stöds.

Lägg till flera SKU: er i följande scenarier:
- Du vill kunna använda olika faktureringsmodellerna, till exempel Bring Your Own License (BYOL) eller betala (PAYG).
- Varje SKU stöder en mängd olika funktionsuppsättningar och varje funktionsuppsättningen prissätts på olika sätt.

En SKU visas den under det överordnade erbjudandet på Azure Marketplace och visas som en egen köpbara entitet i Azure-portalen.

## <a name="to-create-a-new-offer"></a>Skapa ett nytt erbjudande

1.  Logga in på den [Cloud Partner Portal](http://cloudpartner.azure.com/).

2.  På vänstra navigeringsfältet väljer **+ nytt erbjudande**, och välj sedan **Azure Applications**.

    ![Skapa ett nytt erbjudande](./media/cloud-partner-portal-publish-managed-app/newOffer.png)

3.  Under **nytt erbjudande**väljer **redigeraren**.

    ![Nytt erbjudande-redigeraren](./media/cloud-partner-portal-publish-managed-app/newOffer_OfferSettings.png)

4.  Under **redigeraren**, anger du informationen i följande vyer:
    - Erbjudandeinställningar
    - SKU:er
    - Marketplace
    - Support

Varje vy innehåller en uppsättning fält för att fylla. Obligatoriskt fält är markerade med en röd asterisk (\*)

## <a name="to-configure-offer-settings"></a>Konfigurera inställningar för erbjudande

1. Konfigurera följande **erbjuder identitet** fält i erbjuder inställningar.

    **Erbjudande-ID**

     En unik identifierare för erbjudandet i en utgivarprofil för. Detta ID visas i produkten URL: er, ARM-mallar och fakturering rapporter. Du kan endast använda gemena alfanumeriska tecken eller bindestreck (-). ID: T får inte sluta med ett bindestreck och kan ha högst 50 tecken. 
    >[!Note]
    >Det här fältet är låsta när ett erbjudande lanseras.

    **Utgivar-ID**

    En listruta för publisher-profilen. Välj den profil som du vill publicera erbjudandet under. 
    >[!Note]
    >Det här fältet är låsta när ett erbjudande lanseras.

    **Namn**

    Visningsnamn för ditt erbjudande. Det här namnet visas i Azure Marketplace och i Azure Portal. Det får innehålla högst 50 tecken. Använd följande riktlinjer för erbjudandenamn:
    -  Ange ett igenkännligt varumärke för produkten. 
    - Omfattar inte företagets namn här såvida detta inte hur erbjudandet släpps.
    - Om du marknadsföring erbjudandet på din egen webbplats, kontrollera att namnet är identiskt med namnet på din webbplats.

2. Välj **spara** Slutför erbjuder inställningar.
för ditt erbjudande.

## <a name="to-create-skus"></a>Skapa SKU: er
------------------

1. Välj **SKU: er**. 

    ![Nya SKU: N](./media/cloud-partner-portal-publish-managed-app/newOffer_skus.png)

    SKU-ID är en unik identifierare för SKU: N i ett erbjudande. Detta ID visas i produkten URL: er, ARM-mallar och fakturering rapporter. SKU-ID:
    - Kan bara ha högst 50 tecken.
    - Endast bestå av alfanumeriska gemener eller bindestreck (-).
    - ID:t får inte avslutas med ett bindestreck.

    >[!Note]
    >När en SKU har lagts till, visas den i listan över SKU: er i vyn SKU: er. Välj SKU-namnet för att se SKU-information. 

2. Välj **nya SKU: N** ange informationen som visas i följande skärmbild. 

    ![SKU-information](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku.png)


### <a name="sku-settings"></a>SKU-inställningar

Anger du följande inställningar för SKU.

- **Rubrik** – en rubrik för SKU: N. Den här rubriken visas i galleriet för det här objektet.

- **Sammanfattning av** – en kort sammanfattande beskrivning av SKU: N. (Maximal längd är 100 tecken.)

- **Beskrivning av** – en detaljerad beskrivning av SKU: N.

- **SKU-typen** – en listruta med följande värden: ”hanterade program (förhandsversion)” och ”mall”. Det här scenariot väljer **lösningsmallen**.

- **Tillgänglighet i molnet** -platsen för SKU: N. Standardvärdet är offentlig Azure.

### <a name="package-details"></a>Paketinformation

Ange följande Paketinformation när du har slutfört SKU-inställningar.

![Paketinformation](./media/cloud-partner-portal-publish-managed-app/newOffer_newsku_ST_package.png)

- **Aktuell version** -versionen av paketet som du överför. Version taggar måste vara i formatet X.Y.Z, där X, Y och Z är heltal.

- **Paketfil** – det här paketet innehåller följande filer som sparas i en .zip-fil.

    -   MainTemplate.json - distribution mallfilen som används för att distribuera lösningen/program och skapa de resurser som definierats för lösningen. Mer information finns i [hur du skapar distributionen-mallfiler](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)

    -   createUIDefinition.json - den här filen används av Azure-portalen för att generera användargränssnittet för att etablera den här lösningen/application. Mer information finns i [skapa Azure portal användargränssnittet för det hanterade programmet](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview)

    >[!IMPORTANT]
    >Det här paketet innehåller alla andra kapslade mallar eller skript som behövs för att etablera det här programmet. MainTemplate.json och createUIDefinition.json måste vara i rotmappen.

## <a name="to-configure-the-marketplace"></a>Konfigurera Marketplace

Använd vyn Marketplace för att konfigurera de fält som visas för erbjudandet på [Azure Marketplace](https://azuremarketplace.microsoft.com) på [Azure-portalen](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Förhandsversion av prenumerations-ID: N

Lista med Azure-prenumeration-ID som du vill ha åtkomst till erbjudandet om erbjudandet har publicerats. Dessa white visas prenumerationer kan du testa förhandsgranskade erbjudandet innan du gör den live. Du kan vitlista upp till 100 prenumerationer partnerportalen.

### <a name="suggested-categories"></a>Föreslagna kategorier

Välj upp till 5 kategorier från den angivna listan som ditt erbjudande bäst kan associeras med. De valda kategorierna som används för att mappa ditt erbjudande till produktkategorierna i [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](https://portal.azure.com/).

I följande exempel visas information för marketplace i Azure Marketplace och Azure-portalen.

**Azure Marketplace**

![publishvm10](./media/cloud-partner-portal-publish-managed-app/publishvm10.png)


![publishvm11](./media/cloud-partner-portal-publish-managed-app/publishvm11.png)


![publishvm15](./media/cloud-partner-portal-publish-managed-app/publishvm15.png)


**Azure Portal**


![publishvm12](./media/cloud-partner-portal-publish-managed-app/publishvm12.png)


![publishvm13](./media/cloud-partner-portal-publish-managed-app/publishvm13.png)


### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Följ dessa riktlinjer för logotyper som överförts till Cloud Partner Portal:

-   Azure-designen har en enkel färgpalett. Håll nere antalet primära och sekundära färger på din logotyp.

-   Temafärger i Azure Portal är vit och svart. Undvik att använda dessa färger som bakgrundsfärgen för din logotyper. Använd en färg som gör din logotyper framstående i Azure-portalen. Vi rekommenderar enkla primärfärger.

    >[!Note] 
    >Om du använder en genomskinlig bakgrund och sedan kontrollera att logotyper/text inte är vit, svart eller blå.

-   Använd inte en tonad bakgrund för logotypen.

-   Undvik att placera texten på logotypen. Detta inkluderar ditt företag eller varumärke. Utseendet och känslan av din logotyp måste vara *fast* och du bör undvika att toningar.

-   Logotypen bör inte att sträckas ut.

#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfritt. Utgivaren kan du inte ladda upp en Hero-logotyp. Men när logotypen har överförts, den kan inte tas bort. Partnern måste följa riktlinjerna för Azure Marketplace för Hero-ikoner.

#### <a name="guidelines-for-the-hero-logo-icon"></a>Riktlinjer för ikonen Hero-logotyp

-   Visningsnamn för utgivaren, plan rubrik och erbjudandets lång sammanfattning visas med en vit färgade teckensnitt. Undvik att använda alla ljusare i bakgrunden. Svart, bakgrunder med vit och transparent är inte tillåtna för Hero-ikoner.

-   Utgivaren visningsnamn, plan rubrik, lång sammanfattning av erbjudandet och knappen Skapa bäddas programmässigt inuti Hero-logotypen när erbjudandets visas. Ange inte någon text när du utformar Hero-logotypen. Lämna ett tomt utrymme till höger om logotypen. Här ska vara 415 × 100 bildpunkter och förskjutas av 370 px från vänster.

![publishvm14](./media/cloud-partner-portal-publish-managed-app/publishvm14.png)

## <a name="to-configure-support"></a>Konfigurera stöd

Använd vyn stöd för att ange följande information:

- Stöd för kontakter från ditt företag, till exempel tekniker.
- Kontakter för Customer support.

## <a name="to-publish-the-offer"></a>Att publicera erbjudandet

Det sista steget är att publicera erbjudandet. Välj **Publicera**.
