---
title: Azure hanterade program i Marketplace | Microsoft Docs
description: "Beskriver Azure hanterade program som är tillgängliga via Marketplace."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/20/2017
ms.author: gauravbh
ms.openlocfilehash: b4fda06f85c7dab52ff38558b0d928193e0694f6
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="azure-managed-applications-in-the-marketplace"></a>Azure hanterade program i Marketplace

Leverantörer kan använda Azure hanterade program att erbjuda sina lösningar för alla Azure Marketplace-kunder. Dessa leverantörer kan innehålla hanterade tjänsteleverantörer (MSPs), oberoende programvaruleverantörer (ISV) och systemintegrerare (SIs). Hanterade program minska underhållet och underhåll kostnader för kunder. Leverantörer sälja infrastruktur- och programvara på marknadsplatsen. De kan koppla tjänster och operativa stöd till hanterade program. Mer information finns i [hanteras Programöversikt](overview.md).

Den här artikeln förklarar hur du publicerar ett program till marketplace och göra den tillgänglig för kunder.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Krav för att publicera ett hanterat program

För att slutföra den här artikeln, måste du redan har ZIP-filen för din definition för hanterade program. Mer information finns i [skapa katalogen tjänstprogram](publish-service-catalog-app.md).

Dessutom finns flera förutsättningar för företag. De är:

* Ditt företag eller dess dotterbolag måste finnas i ett land där försäljning stöds av marketplace.
* Produkten måste ha licens på ett sätt som är kompatibel med fakturering modeller som stöds av marketplace.
* Tillhandahålla teknisk support för kunder på ett kommersiellt rimliga sätt. Stöd kan vara fria, betald, eller via community stöd.
* Licens för programvaran och eventuella beroenden för programvara från tredje part.
* Ange innehåll som uppfyller villkoren för ditt erbjudande ska visas i Marketplace och i Azure-portalen.
* Acceptera villkoren i avtalet för utgivaren och Azure Marketplace deltagande principer.
* Acceptera att uppfylla den användningsvillkoren och sekretesspolicyn för Microsoft certifierade programavtalet för Microsoft Azure.

## <a name="set-up-your-account-for-publishing-portal"></a>Konfigurera ditt konto för publishing portal

Publishing portal används för att publicera och hantera dina erbjudanden. För att publicera en marketplace-program, måste du ha en godkända Microsoft Developer för Azure Marketplace. Om du inte har registrerats för ett godkänt konto, se [skapa ett konto på Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

Om du har en godkänd **Microsoft Developer Center** konto, men inte har använt den [Azure Publishing Portal](https://cloudpartner.azure.com/), måste du registrera dig för publishing portal.

1. Öppna en ny Chrome Incognito eller Internet Explorer InPrivate-surfning session för att säkerställa att du inte är inloggad till ett personligt konto.
2. Gå till [https://cloudpartner.azure.com/](https://cloudpartner.azure.com/).
3. Om du är en ny användare och logga in på publicering Företagsportalen för första gången kan sedan du måste logga in med samma e-post-ID som developer center-konto. Nu länkas developer center-konto och publishing portal konto.

Du kan senare lägga till andra medlemmar i företaget som en [medadministratör](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md#4-steps-to-add-a-co-admin-in-the-publishing-portal) i publishing portal. Om du har lagts till som medadministratör i publishing portal, kan du logga in med ditt medadministratör.

> [!TIP]
> Principer för deltagande beskrivs på den [Azure-webbplatsen](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).
>
>

## <a name="create-a-new-azure-application-offer"></a>Skapa ett nytt erbjudande för Azure-program

Efter att du uppfyller kraven som är du redo att skapa erbjudandet hanterade program.

### <a name="set-up-an-offer"></a>Konfigurera ett erbjudande

Erbjudande för ett hanterat program motsvarar en klass av produkten erbjudande från en utgivare. Om du har en ny typ av program som du vill ska vara tillgängliga i marketplace kan konfigurera du den som ett nytt erbjudande. Ett erbjudande är en samling av SKU: er. Varje erbjudande visas som sin egen enhet i marketplace.

1. Logga in på den [molnpartnerportalen](https://cloudpartner.azure.com/).

1. I navigeringsfönstret till vänster, Välj **+ nytt erbjudande** > **Azure program**.

   ![Nytt erbjudande](./media/publish-marketplace-app/newOffer.png)

1. I den **Editor** kan du se de nödvändiga formulär. Varje formulär beskrivs senare i den här artikeln.

   ![Inställningar för erbjudande](./media/publish-marketplace-app/newOffer_OfferSettings.png)

## <a name="offer-settings-form"></a>Erbjudande inställningar för formulär

Fälten för den **erbjuder inställningar** formatet är:

* **Erbjudande-ID**: den unika identifieraren identifierar erbjudandet i en profil för utgivaren. Detta ID visas i produkten URL: er, Resource Manager-mallar och fakturering rapporter. Det kan bara består av gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett streck. Det är begränsat till högst 50 tecken. När ett erbjudande lanseras är i det här fältet låst.
* **Publicerings-ID**: Använd den här listrutan för att välja publisher-profil som du vill publicera det här erbjudandet under. När ett erbjudande lanseras är i det här fältet låst.
* **Namnet**: det här visningsnamnet för ditt erbjudande visas i Marketplace och i portalen. Det kan ha högst 50 tecken. Innehåller ett okänt varumärke för produkten. Inkludera inte ditt företagsnamn, om det inte är hur släpps. Om du marknadsföring erbjudandet på din egen webbplats, måste du kontrollera att namnet är exakt hur den visas på webbplatsen.

När du är klar väljer **spara** att spara ditt arbete.

## <a name="skus-form"></a>SKU: er formulär

Nästa steg är att lägga till SKU: er för ditt erbjudande.

En SKU är den minsta köpbara enheten ett erbjudande. Du kan använda en SKU inom samma produkten klass (erbjudandet) för att skilja mellan:

* Olika funktioner som stöds
* Om erbjudandet är hanterade eller ohanterade
* Fakturering modeller som stöds

En SKU visas under överordnade erbjudandet på marketplace. Det verkar som sin egen köpbara enhet i Azure-portalen.

1. Välj **SKU: er** > **nya SKU**.

   ![Välj ny SKU](./media/publish-marketplace-app/newOffer_skus.png)

1. Ange en **SKU ID**. En SKU-ID är en unik identifierare för SKU: N i ett erbjudande. Detta ID visas i produkten URL: er, Resource Manager-mallar och fakturering rapporter. Det kan bara består av gemena alfanumeriska tecken och bindestreck (-). ID: T får inte sluta med ett streck och är begränsat till högst 50 tecken. När ett erbjudande lanseras är i det här fältet låst. Du kan ha flera SKU: er i ett erbjudande. Du behöver en SKU för varje avbildning som du planerar att publicera.

1. Fyll i den **SKU information** avsnitt i följande format:

   ![Ange nya SKU](./media/publish-marketplace-app/sku-settings.png)

   Fylla i följande fält:

   * **Rubrik**: Ange en rubrik för denna SKU. Den här rubriken visas i galleriet för det här objektet.
   * **Översikt över**: Ange en kort sammanfattning för denna SKU. Den här texten visas under rubriken.
   * **Beskrivning**: Ange en detaljerad beskrivning av SKU: N.
   * **SKU-typen**: de tillåtna värdena är *hanterat program* och *Lösningsmallar*. Det här fallet markerar *hanterat program*.
   * **Land/Region tillgänglighet**: Välj de länder där det hanterade programmet är tillgängligt.

      ![Välj land](./media/publish-marketplace-app/select-country.png)

   * **Priser**: Ange ett pris för hanteringen av programmet. Välj de tillgängliga länderna innan du anger priset.

1. Lägg till ett nytt paket. Fyll i den **Paketinformation** avsnitt i följande format:

   ![Paket](./media/publish-marketplace-app/new-package.png)

   Fylla i följande fält:

   * **Aktuell Version**: Ange en version för det paket som du överför. Det bör vara i formatet `{number}.{number}.{number}{number}`.
   * **Välj en paketfil**: det här paketet innehåller den nödvändiga filen komprimerade till en ZIP-paketet. Mer information finns i [skapa katalogen tjänstprogram](publish-service-catalog-app.md).
   * **PrincipalId**: den här egenskapen är Azure Active Directory (Azure AD)-ID för en användare, grupp eller ett program som beviljas åtkomst till resurser i kundens prenumeration. Rolldefinitionen beskriver behörigheten.
   * **Rolldefinitionen**: den här egenskapen är en lista med alla inbyggda rollbaserad åtkomstkontroll (RBAC) roller tillhandahålls av Azure AD. Du kan välja den roll som är mest lämpligt att använda för att hantera resurserna för kundens räkning.

Du kan lägga till flera tillstånd. Vi rekommenderar att du skapar en grupp i AD-användare och ange dess ID i **PrincipalId**. På så sätt kan du lägga till fler användare i användargruppen utan att behöva uppdatera SKU: N.

Läs mer om hur RBAC [komma igång med RBAC på Azure portal](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Marketplace-formulär

Marketplace-formulär uppmanar för fält som visas på den [Azure Marketplace](https://azuremarketplace.microsoft.com) och på den [Azure-portalen](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>Prenumerations-ID: N för förhandsgranskning

Ange en lista över Azure-prenumeration ID: N som kan komma åt erbjudandet när den har publicerats. Du kan använda dessa vitt visas prenumerationer för att testa förhandsgranskade erbjudandet innan du gör den live. Du kan sammanställa en lista för tillåten av upp till 100 prenumerationer i partnerportalen.

### <a name="suggested-categories"></a>Föreslagna kategorier

Välj upp till fem kategorier i listan som erbjudandet bäst kan associeras med. Dessa kategorier som används för att mappa erbjudandet till produktkategorier som är tillgängliga i den [Azure Marketplace](https://azuremarketplace.microsoft.com) och [Azure-portalen](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Sammanfattning av det hanterade programmet visar följande fält:

![Marketplace-sammanfattning](./media/publish-marketplace-app/publishvm10.png)

Den **översikt över** för det hanterade programmet visar följande fält:

![Marketplace-översikt](./media/publish-marketplace-app/publishvm11.png)

Den **planer + priser** för det hanterade programmet visar följande fält:

![Marketplace-planer](./media/publish-marketplace-app/publishvm15.png)

#### <a name="azure-portal"></a>Azure Portal

Sammanfattning av det hanterade programmet visar följande fält:

![Översikt över Portal](./media/publish-marketplace-app/publishvm12.png)

Översikt för det hanterade programmet visar följande fält:

![Portalen översikt](./media/publish-marketplace-app/publishvm13.png)

#### <a name="logo-guidelines"></a>Logotypen riktlinjer

Följ dessa riktlinjer för en logotyp som du överför i molnpartnerportalen:

*   Azure designen har en enkel färgpalett. Begränsa antalet primära och sekundära färger i din logotyp.
*   Färger med portalen är vit och svart. Använd inte färgerna som bakgrundsfärg för din logotyp. Använd en färg som gör din logotyp framträdande i portalen. Vi rekommenderar enkla primära färger. *Om du använder en genomskinlig bakgrund, se till att logotyp och texten inte vitt, svart eller blå.*
*   Använd inte en toning bakgrund på logotypen.
*   Placera inte text på logotypen, inte ens företaget eller varumärke. Utseendet och känslan logotypens bör platt och undvika toningar.
*   Kontrollera att logotypen inte har sträckts ut.

#### <a name="hero-logo"></a>Hjälte-logotyp

Logotypen hjälte är valfritt. Utgivaren kan du inte överföra en hjälte logotyp. När ikonen hjälte har överförts kan inte tas bort. Partnern måste följa Marketplace riktlinjer för hjälte ikoner som helst.

Följ dessa riktlinjer för ikonen hjälte logotyp:

*   Visningsnamn för utgivaren, plan rubrik och erbjudandet lång sammanfattning visas i vitt. Därför inte använda en ljusare ikonen hjälte bakgrunden. En svart, vit eller genomskinlig bakgrund är inte tillåten för hjälte ikoner.
*   När erbjudandet visas inbäddade element via programmering i hjälte logotypen. Inbäddade elementen omfattar visningsnamn utgivare, planera rubrik, erbjudandet lång sammanfattning och **skapa** knappen. Därför inte ange valfri text när du utformar hjälte logotypen. Lämna tomt utrymme till höger eftersom texten programmässigt ingår i detta utrymme. Det tomma utrymmet för texten som ska vara 415 x 100 bildpunkter till höger. Det är förskjutning med 370 bildpunkter från vänster.

    ![Hjälte logotypen exempel](./media/publish-marketplace-app/publishvm14.png)

## <a name="support-form"></a>Stöd för formulär

Fyll i den **stöder** formuläret med stöd för kontakter från ditt företag. Den här informationen kan vara engineering customer support kontakter.

## <a name="publish-an-offer"></a>Publicera ett erbjudande

När du fyller i alla avsnitt markerar **publicera** att starta processen som gör erbjudandet tillgängliga för kunder.

## <a name="next-steps"></a>Nästa steg

* En introduktion till hanterade program, se [hanteras Programöversikt](overview.md).
* Information om hur du publicerar ett program för Tjänstkatalog hanteras finns [skapa och publicera en applikation för Tjänstkatalog hanteras](publish-service-catalog-app.md).
