---
title: SaaS - sälj via Azure | Microsoft Docs
description: Beskriver prenumerationsfaktureringen modell för SaaS-program och hur du implementerar den.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
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
ms.openlocfilehash: f193138fbc5e779c3a6671757320d8918e08db46
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48811645"
---
<a name="saas---sell-through-azure"></a>SaaS - sälj via Azure
=========================

Den här artikeln beskriver prenumerationen faktureringsmodellen för SaaS-program och hur du implementerar den i partnerportalen i molnet.


<a name="overview"></a>Översikt
--------

Azure marketplace kan du publicera och tjäna pengar på dina SaaS-erbjudanden via Azure marketplace. För dina kunder kan de nu tas ut via Azure-fakturering direkt och de bara får en enda faktura från Azure för alla sina resurser och tjänster som de har aktiverat. För att publicera har SaaS-prenumerationer följande fördelar:

-   **Konsekvent publiceringsupplevelse** – om du redan har publicerat SaaS-erbjudanden eller ett annat erbjudande på Azure Marketplace, publiceringsupplevelse via samma publiceringsportalen.
-   **Ny butik för identifiering av** – alla erbjudanden som publicerats som ska visas både i den externa Azure Marketplace-butiken, samt tillägget Azure Marketplace i Azure-portalen.
-   **Integrerad fakturering** – nu kan sälja i alla regioner där Azure säljer, och låt Azure ta hand om fakturering för dig.
-   **Integrerad generering av** – du kan nu automatiskt få leads från Azure i CRM föredrar, när en Azure-användare prenumererar på SaaS-tjänsten med hjälp av Azure marketplace.
-   **Medförsäljning med Microsoft-säljare** – du kan kvalificera dig för dubbelriktad delning av leads, prioriterad katalogplacering, och möjlighet att initiera och slutföra hanterar sida vid sida med Microsoft-säljare.

<a name="billing-models"></a>Fakturering modeller
--------------

Endast stöds för närvarande faktureringsmodellen är en fast månadsavgift per prenumeration SaaS-tjänsten.

**Obs:** ytterligare faktureringsmodellerna kan vara tillgänglig vid ett senare tillfälle.

Varje SaaS-erbjudande kan omfatta en eller flera planer (till exempel Basic eller Premium). Varje prenumeration har några grundläggande metadata som är associerade med den, tillsammans med priset som är associerade med planen.

Du har fullständig kontroll över definitionen av en plan. Till exempel vad en Basic-avtal utgör? Planen har definierats av dig och du kan ange den nödvändiga texten för att beskriva den som en del för att publicera din plan.

Priset som är associerade med planen som är den fasta månatliga avgiften som Azure-användare betalar du använder tjänsten.

### <a name="what-is-not-supported-today"></a>Vad stöds inte i dag?

-   Fakturering baserad på anpassade enheter, till exempel ett fast pris baserat på antalet begäranden.
-   Baserat på plats allokering--kan till exempel Azure-användare att köpa licenser utifrån hur många användare.

<a name="end-to-end-flow"></a>Slutpunkt till slutpunkt-flöde
---------------

SaaS-prenumerationen erbjuder flöde från en slutanvändare perspektiv först

**Obs:** den här flödesbeskrivningen förutsätter att du har publicerat ditt SaaS-erbjudande på Azure marketplace som heter ”Contoso demo SaaS'.

![Användarflödet för SaaS-prenumeration](media/saas-offer-subscription/saas-subscription-user-flow.png)

En Azure-användare kan ha följande interaktioner med SaaS-tjänsten:

-   Identifiera SaaS-tjänsten på Azure Marketplace
-   Prenumerera på SaaS-tjänsten i Azure
-   Navigera till SaaS-tjänsten från Azure portal
-   Skapa ett konto i SaaS-tjänst och hantera (ändra plan/ta bort) kontot i SaaS
-   Avbryt prenumerationen på SaaS-tjänsten från Azure-portalen

<a name="discover-your-saas-service-in-azure-marketplace"></a>Identifiera SaaS-tjänsten på Azure Marketplace
-----------------------------------------------

När användarna startar Azure Marketplace i Azure-portalen, visas en kategori som heter ”programvara som en tjänst (SaaS)”.

![Identifiera SaaS med hjälp av kategorimenyn](media/saas-offer-subscription/saas-category-menu.png)

Användare kan också söka efter SaaS-tjänsten.

![Identifiera SaaS använda sökning](media/saas-offer-subscription/saas-cpp-search.png)

Användare kan sedan visa information om din tjänst och klicka sedan på **skapa**.

![Skapa en SaaS-prenumeration](media/saas-offer-subscription/saas-create-subscription.png)

### <a name="subscribe-to-saas-service-in-azure"></a>Prenumerera på SaaS-tjänst i Azure

Användaren kan sedan prenumererar på SaaS tjänsten från Azure.

![Prenumerera på en SaaS-tjänst](media/saas-offer-subscription/saas-subscribe-signup.png)

När du prenumererar på SaaS-tjänsten, innehåller användaren följande information

-   Namn – Namnet att användare kan identifiera och hantera den här instansen med SaaS-prenumeration i Azure.
-   Prenumeration – Den prenumerationskontext som de vill associera faktureringen för SaaS-tjänsten.
-   Plan – SaaS tjänsten planen som de vill prenumerera på.

Användningsvillkor dokumentet tillhandahålls som en del för att publicera erbjudandet filterläget visas för användaren innan du prenumererar på SaaS-tjänsten.

Användaren kan nu prenumererar på tjänsten genom att klicka på **prenumerera**.
Azure skickar ett meddelande i portalen när prenumerationen har slutförts.

### <a name="navigate-to-the-saas-service-from-azure-portal"></a>Navigera till SaaS-tjänsten från Azure-portalen

Användare klickar **gå till resurs** att visa eller hantera sin prenumeration SaaS-instans.

![Visa eller hantera din SaaS-instans](media/saas-offer-subscription/saas-go-to-resource.png)

Användaren meddelas att de måste konfigurera kontot i SaaS-tjänsten först. Deras faktureringen påbörjas när SaaS tjänsten meddelar Azure att starta fakturering som när du skapar ett konto på webbplatsen SaaS tjänsten.

![Konfigurera din SaaS-instans](media/saas-offer-subscription/saas-configure-account.png)

När användaren klickar på **Konfigurera konto**, de omdirigeras till din SaaS-tjänstslutpunkt. Under den här omdirigering skickas en token tillsammans som en frågeparameter. Exempel:

![SaaS-konto token](media/saas-offer-subscription/saas-account-token.png)

Anteckna värdet för den här token. Denna token är en tillfällig och måste lösas för att hämta ett prenumerations-ID i Azure.

<a name="creating-a-saas-offer-subscription"></a>Skapa en prenumeration för SaaS-erbjudande
----------------------------------

Det finns två arbeten som krävs för att skapa den här upplevelsen:

-   Anslut din SaaS tjänsten webbplats med Microsoft\'s SaaS APIs. Det här dokumentet [SaaS sälj via Azure - API: er](./cloud-partner-portal-saas-subscription-apis.md) förklarar hur du bygger den här anslutningen.  
-   Aktivera **sälj via Azure** på partnerportalen i molnet i den **teknisk information** och anger alla dina konfigurationsinformation.

![SaaS nya erbjudandet teknisk information](media/cpp-creating-saas-offers/saas-new-offer-technical-info-2.png)

Nedan visas en förklaring av alla obligatoriska fält för den **teknisk information** avsnittet:

|  **Erbjudandet fält**                 |  **Beskrivning**                                   |
|  ----------------                 |  -------------------------------------             |
| Förhandsversion av prenumerations-ID: N          | Alla Azure-prenumeration identifierare används för att testa ditt erbjudande i en förhandsversion innan den är allmänt tillgängliga. |
| Komma igång-anvisningar      | Anvisningar för att dela med dina kunder att hjälpa dem att ansluta till din SaaS-app. Grundläggande HTML-användning tillåts, sådana etiketter som `<p>`, `<h1>`, `<li>`osv.  |
| Webbadress för informationssida                  | Din webbplats-URL som du kommer att dirigera kunderna hamnar på när du hämtar från Azure-portalen. Den här URL: en kommer också att den slutpunkt som ska ta emot anslutningen API: er för att underlätta handel med Microsoft.  |
| Anslutningen Webhook                | För alla asynkrona händelser som behöver skickar till dig för kundens räkning (exempel: Azure-prenumeration har gått ogiltig), vi kräver att du tillhandahåller en anslutning webhook. Om du inte redan har ett webhook-system på plats, är den enklaste konfigurationen att ha en Logikapp för HTTP-slutpunkt som ska lyssna efter alla händelser publiceras till den och hantera dem på rätt sätt.  Mer information finns i [anropa, utlösare, eller kapsla arbetsflöden med HTTP-slutpunkter i logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint). |
| Azure AD-klient-ID och App-ID     | I Azure-portalen kräver att du skapar en Active Directory-App så att vi kan verifiera anslutningen mellan våra två tjänster som finns bakom en autentiserad kommunikation. För dessa fält, skapa en AD-App och klistra in i motsvarande klient-Id och App-Id krävs. |
|  |  |


Slutligen, om du väljer **sälj via Azure**, det finns en har lagts till avsnitt som heter **planer**. Planer krävs endast om sälj via Azure väljs. Det här avsnittet innehåller specifika planer och deras motsvarande priser som har stöd för din SaaS-app. Från och med dagens datum tillåter vi månatliga priser, med möjlighet att tillåta för 1 - eller 3-månaders kostnadsfri åtkomst. Dessa abonnemang och priser måste matcha den exakta planer och priser som du har i en egen plats för SaaS-app.
