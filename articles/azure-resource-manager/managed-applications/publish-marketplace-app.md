---
title: Hanterade appar på Marketplace
description: Beskrivning av Azure-hanterade program som är tillgängliga via Marketplace.
author: tfitzmac
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: tomfitz
ms.openlocfilehash: a42ee5d6f7f40d391acb743ef85f671f25804749
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "79473004"
---
# <a name="tutorial-publish-azure-managed-applications-in-the-marketplace"></a>Självstudie: publicera Azure-hanterade program på Marketplace

Leverantörer kan använda Azure-hanterade program för att erbjuda sina lösningar till alla Azure Marketplace-kunder. Sådana leverantörer kan omfatta leverantörer av hanterade tjänster (MSP), oberoende programvaruleverantörer (ISV) och systemintegrerare (SIs). Med hanterade program sänks kundens kostnader för underhåll och service. Leverantörerna säljer infrastruktur och programvara på marknadsplatsen. De kan koppla tjänster och driftstöd till sina hanterade program. Mer information finns i [Översikt över hanterade program](overview.md).

I den här artikeln beskrivs hur du publicerar ett program på marknadsplatsen och gör den tillgänglig för kunder.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Krav för publicering av ett hanterat program

För att kunna slutföra detta måste du redan ha en ZIP-fil för din definition för hanterade program. Mer information finns i [Skapa tjänstkatalogprogram](publish-service-catalog-app.md).

Det finns flera företags krav. De är:

* Företaget eller dess dotter bolag måste finnas i ett land/en region där försäljningen stöds av Marketplace.
* Din produkt måste vara licensierad på ett sätt som fungerar med de faktureringsmodeller som används på marknadsplatsen.
* Du måste tillhandahålla teknisk support för kunderna på ett kommersiellt rimligt sätt. Supporten kan vara gratis, betald eller bestå av community-support.
* Du måste licensiera din programvara och eventuell tillhörande programvara från tredje part.
* Du måste ange innehåll som uppfyller villkoren för att ditt erbjudande ska visas på Marketplace och i Azure Portal.
* Acceptera avtalsvillkoren för deltagare och utgivare på Azure Marketplace.
* Acceptera användningsvillkoren, Microsofts sekretesspolicy och Microsoft Azure Certified-programavtalet.

Du måste också ha ett Marketplace-konto. Information om hur du skapar ett konto finns i [så här skapar du ett kommersiellt marknads plats konto i Partner Center](../../marketplace/partner-center-portal/create-account.md).

## <a name="create-a-new-azure-application-offer"></a>Skapa ett nytt erbjudande för Azure-program

När du har skapat ditt konto på partnerportalen kan du börja skapa ditt erbjudande om hanterade program.

### <a name="set-up-an-offer"></a>Skapa ett erbjudande

Ett erbjudande för ett hanterat program motsvarar ett produktklasserbjudande från en utgivare. Om du har en ny typ av program som du vill göra tillgängligt på Marketplace kan du konfigurera det som ett nytt erbjudande. Ett erbjudande är en samling SKU: er. Varje erbjudande visas som en egen entitet på Marketplace.

1. Logga in på [Cloud Partner Portal](https://cloudpartner.azure.com/).

1. I navigerings fönstret till vänster väljer du **+ nytt erbjudande** > för**Azure-program**.

1. I vyn **Redigerare** kan du se de formulär du behöver. De olika formulären beskrivs längre fram i den här artikeln.

## <a name="offer-settings-form"></a>Inställningsformulär för erbjudande

Följande fält finns på formuläret för **erbjudandeinställningar**:

* **Erbjudande-ID**: ett unikt ID som identifierar erbjudandet i en utgivarprofil. ID:t visas i produktens webbadresser, Resource Manager-mallar och faktureringsrapporter. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID:t får inte avslutas med ett bindestreck. Det kan innehålla högst 50 tecken. När ett erbjudande lanseras är det här fältet låst.
* **Utgivar-ID**: I den här listrutan väljer du den utgivarprofil som du vill använda när du publicerar erbjudandet. När ett erbjudande lanseras är det här fältet låst.
* **Namn**: Det här visningsnamnet för ditt erbjudande visas på Marketplace och i portalen. Det får innehålla högst 50 tecken. Ange ett igenkännligt varumärke för produkten. Ta inte med ditt företagsnamn här om det inte ingår i produktnamnet. Om du marknadsför erbjudandet på din egen webbplats måste du se till att namnet anges exakt som det visas på webbplatsen.

När du är klar sparar du ditt arbete genom att välja **Spara**.

## <a name="skus-form"></a>Formulär för SKU:er

Nästa steg är att lägga till SKU: er för erbjudandet.

En SKU är den minsta köpbara enheten i ett erbjudande. Du kan använda en SKU inom samma produktklass (erbjudande) för att skilja mellan:

* Olika funktioner som stöds
* Om erbjudandet är hanterat eller ohanterat
* Faktureringsmodeller som stöds

En SKU visas under det överordnade erbjudandet på Marketplace. Det visas som en egen köpbar enhet i Azure Portal.

1. Välj **SKU** > **ny SKU**.

1. Ange ett **SKU-ID**. Ett SKU-ID är en unik identifierare för en SKU i ett erbjudande. ID:t visas i produktens webbadresser, Resource Manager-mallar och faktureringsrapporter. Det får bara innehålla gemena alfanumeriska tecken och bindestreck (-). ID:t får inte avslutas med ett bindestreck och får innehålla högst 50 tecken. När ett erbjudande lanseras är det här fältet låst. Du kan ha flera SKU:er i ett erbjudande. Du behöver en SKU för varje avbildning som du tänker publicera.

1. Fyll i avsnittet **SKU-information** i följande formulär:

   Fyll i följande fält:

   * **Rubrik**: Ange en rubrik för SKU:n. Rubriken visas i galleriet för det här objektet.
   * **Sammanfattning**: Ange en kort sammanfattning för SKU:n. Texten visas nedanför rubriken.
   * **Beskrivning**: Ange en detaljerad beskrivning av SKU:n.
   * **SKU-typ**: Tillåtna värden är *Hanterat program* och *Lösningsmallar*. I det här fallet väljer du *Hanterat program*.
   * **Tillgänglighet för land/region**: Välj de länder/regioner där det hanterade programmet är tillgängligt.
   * **Priser**: Ange ett pris för hanteringen av programmet. Välj de tillgängliga länderna/regionerna innan du anger priset.

1. Lägg till ett nytt paket. Fyll i avsnittet **Paketinformation** i följande formulär:

   Fyll i följande fält:

   * **Version**: Ange versionen för det paket du laddar upp. Versionsformatet ska vara `{number}.{number}.{number}{number}`.
   * **Paketfil (.zip)**: Det här paketet innehåller två nödvändiga filer som komprimerats i ett .zip-paket. En av filerna är Resource Manager-mallen som definierar de resurser som ska distribueras för det hanterade programmet. Den andra filen definierar [användargränssnittet](create-uidefinition-overview.md) för konsumenter som distribuerar det hanterade programmet via portalen. I användargränssnittet anger du element som ger konsumenterna möjlighet att ange parametervärden.
   * **Klient-ID**: klient-ID för det konto som ska få åtkomst.
   * **Aktivera JIT-åtkomst**: Välj **Ja** om du vill aktivera [just-in-Time-åtkomstkontroll](request-just-in-time-access.md) för kontot. När den är aktive rad begär du åtkomst till konsumentens konto under en angiven tids period. Om du vill kräva att användare av ditt hanterade program ger ditt konto permanent åtkomst väljer du **Nej**.
   * Vill du **Anpassa tillåtna kund åtgärder?**: Välj **Ja** om du vill ange vilka åtgärder som konsumenter kan utföra på de hanterade resurserna.
   * **Tillåtna kund åtgärder**: om du väljer **Ja** för den föregående inställningen kan du ange vilka åtgärder som tillåts för konsumenter genom att använda [neka-tilldelningar för Azure-resurser](../../role-based-access-control/deny-assignments.md).

     Information om tillgängliga åtgärder finns i [Azure Resource Manager Resource Provider-åtgärder](../../role-based-access-control/resource-provider-operations.md). Om du till exempel vill tillåta att användare startar om virtuella datorer `Microsoft.Compute/virtualMachines/restart/action` kan du lägga till dem i de tillåtna åtgärderna. `*/read` Åtgärden tillåts automatiskt så att du inte behöver ta med den inställningen.
   * **PrincipalId**: den här egenskapen är Azure Active Directory-ID (Azure AD-ID) för en användare, en användargrupp eller ett program som beviljas åtkomst till resurser i kundens prenumeration. Rolldefinitionen beskriver behörigheterna.
   * **Rolldefinition**: Den här egenskapen är en lista med alla inbyggda RBAC-roller (rollbaserade åtkomstkontroller) som tillhandahålls av Azure AD. Välj den roll som är mest lämplig för hantering av resurserna för kundens räkning.
   * **Principinställningar**: Använd [Azure Policy](../../governance/policy/overview.md) på det hanterade programmet och ange efterlevnadskrav för de distribuerade lösningarna. Välj vilka principer du vill använda bland de tillgängliga alternativen. I fältet **Principparametrar** anger du en JSON-sträng med parametervärdena. Du kan läsa om principdefinitioner och parametervärdenas format i [Azure Policy-exempel](../../governance/policy/samples/index.md).

Du kan lägga till flera auktoriseringar. Vi rekommenderar att du skapar en AD-användargrupp och anger gruppens ID i **PrincipalId**. På det sättet kan du lägga till fler användare i användargruppen utan att behöva uppdatera SKU:n.

Mer information om RBAC finns i [Komma igång med RBAC på Azure Portal](../../role-based-access-control/overview.md).

## <a name="marketplace-form"></a>Formulär för Marketplace

I Marketplace-formulär anges fält som visas på [Azure Marketplace](https://azuremarketplace.microsoft.com) och på [Azure Portal](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID:n för prenumerationer i förhandsversion

Ange en lista över ID:n för Azure-prenumerationer som har åtkomst till erbjudandet när det har publicerats. Du kan använda de här vitlistade prenumerationerna för en förhandstestning av erbjudandet innan du gör det live. Du kan kompilera en lista över tillåtna till 100 prenumerationer i Partner portalen.

### <a name="suggested-categories"></a>Föreslagna kategorier

Välj upp till fem kategorier i listan som passar bäst för ditt erbjudande. Kategorierna används för att mappa erbjudandet till tillgängliga produktkategorier på [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure Portal](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

I sammanfattningen av det hanterade programmet visas följande fält:

![Marketplace-sammanfattning](./media/publish-marketplace-app/publishvm10.png)

På fliken **Översikt** för det hanterade programmet visas följande fält:

![Marketplace-översikt](./media/publish-marketplace-app/publishvm11.png)

På fliken **Prenumerationer + priser** för det hanterade programmet visas följande fält:

![Marketplace-prenumerationer](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

I sammanfattningen av det hanterade programmet visas följande fält:

![Portalsammanfattning](./media/publish-marketplace-app/publishvm12.png)

I sammanfattningen för ditt hanterade program visas följande fält:

![Portalöversikt](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Riktlinjer för logotyp

Följ de här riktlinjerna för logotyper som du laddar upp i Cloud Partner Portal:

*   Azure-designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i logotypen.
*   Portalens temafärger är svart och vitt. Använd inte de färgerna som bakgrundsfärg för logotypen. Använd en färg som gör att logotyp framträder tydligt i portalen. Vi rekommenderar enkla primärfärger. *Om du använder en genomskinlig bakgrund måste du se till att logotypen och texten inte är vit, svart eller blå.*
*   Använd inte en tonad bakgrund för logotypen.
*   Placera inte text på logotypen – inte ens företagets eller varumärkets namn. Logotypens utseende och känsla bör vara platt och inte innehålla toningar.
*   Se till att logotypen inte har sträckts ut.

#### <a name="hero-logo"></a>Hero-logotyp

Hero-logotypen är valfri. Utgivaren kan välja att inte ladda upp en Hero-logotyp. När Hero-ikonen har laddats upp kan den inte tas bort. Då måste partnern följa Marketplace-riktlinjerna för Hero-ikoner.

Följ de här riktlinjerna för Hero-logotypikonen:

*   Utgivarens visningsnamn, prenumerationsrubriken och den långa sammanfattningen för erbjudandet visas i vitt. Använd därför inte en ljus färg för Hero-ikonens bakgrund. En svart, vit eller genomskinlig bakgrund tillåts inte för Hero-ikoner.
*   När erbjudandet visas bäddas elementen in via programmering i Hero-logotypen. De inbäddade elementen omfattar utgivarens visningsnamn, prenumerationsrubrik, erbjudandets långa sammanfattning och knappen **Skapa**. Ange därför ingen text när du utformar Hero-logotypen. Lämna tomt utrymme till höger eftersom texten infogas i det området via programmering. Det tomma utrymmet för texten ska vara 415 x 100 bildpunkter till höger. Det förskjuts med 370 bildpunkter från vänster.

    ![Exempel på Hero-logotyp](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Supportformulär

Fyll i formuläret **Support** med supportkontakter från ditt företag. Detta kan vara både teknikerkontakter och kundtjänstkontakter.

## <a name="publish-an-offer"></a>Publicera ett erbjudande

När du har fyllt i alla avsnitt väljer du **Publicera** för att påbörja processen som gör erbjudandet tillgängligt för kunder.

## <a name="next-steps"></a>Nästa steg

* Information om vad som händer när du har klickat på **publicera**finns i [publicera Azure-program erbjudande](../../marketplace/cloud-partner-portal/azure-applications/cpp-publish-offer.md)
* En introduktion till hanterade program finns i [Managed application overview](overview.md) (Översikt över hanterade program).
* Information om hur du publicerar en tjänstkatalog för hanterade program finns i [Skapa och publicera en tjänstkatalog för hanterade program](publish-service-catalog-app.md).
