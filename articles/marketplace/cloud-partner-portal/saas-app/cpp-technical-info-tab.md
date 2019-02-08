---
title: Azure SaaS-erbjudande teknisk programkonfiguration | Microsoft Docs
description: Konfigurera teknisk information för erbjudande för SaaS-program på Azure Marketplace.
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
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: f0f6bbf5f235d6ae3e86114e583d55c502296470
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895405"
---
# <a name="saas-application-technical-info-tab"></a>Tekniska informationsfliken för SaaS-program

Teknisk informationsfliken innehåller formuläret teknisk konfiguration. Välj typ av SaaS-program (app) som du skapar och konfigurera hur din app tillhandahålls till kunder med hjälp av det här formuläret.

![Teknisk konfigurationsformuläret](./media/saas-techinfo-techconfig.png)

## <a name="technical-configuration-form"></a>Teknisk konfigurationsformuläret

Det här formuläret har 2 fält: Produkten och anrop till åtgärden.

### <a name="product-field"></a>Produktnamn

Du kan ange en SaaS-app för båda följande butiker:
- För företagsanvändare genom att välja den **lista** alternativet.
- För en IT-administratör genom att välja **sälj via Microsoft**.
För att avgöra vilken typ av SaaS-app som du utvecklar, läsa [förstå storefront val av](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type#understand-storefront-selection).

#### <a name="sell-through-microsoft"></a>Sälj via Microsoft
Om du vill skapa den här upplevelsen måste du konfigurera följande delar:

- Anslut din SaaS tjänsten webbplats med Microsofts SaaS APIs. Den [SaaS sälj via Azure – API: er](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-subscription-apis) artikeln förklarar hur du skapar den här anslutningen.
- Aktivera sälj via Azure på partnerportalen i molnet i konfigurationsformuläret för teknisk och ange nödvändig information. Läs mer om detta faktureringsmodellen och hur den har implementerats [SaaS – sälj via Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-saas-offer-subscriptions).

 ![Sälj via Microsoft-formulär](./media/saas-techinfo-sellthrough-ms.png)

I följande tabell beskrivs de obligatoriska fälten för sälj via Microsoft.

|  **Fältnamn**   |  **Beskrivning**  |
|  ---------------  |  ---------------  |
|    Förhandsversion av prenumerations-ID: N               |    Alla Azure-prenumeration identifierare används för att testa ditt erbjudande i en förhandsversion innan den är allmänt tillgängliga.               |
|     Komma igång-anvisningar              |   Anvisningar för att dela med dina kunder att hjälpa dem att ansluta till din SaaS-app. Grundläggande HTML-taggar är tillåtna, till exempel: &lt;p&gt;, &lt;h1&gt;, &lt;li&gt;osv.                |
|    Webbadress för informationssida  |   Din webbplats-URL som du kommer att dirigera kunderna hamnar på när du hämtar från Azure-portalen. Den här URL: en kommer också att den slutpunkt som ska ta emot anslutningen API: er för att underlätta handel med Microsoft.                |
|  Anslutningen Webhook    |  För alla asynkrona händelser som behöver skickar till dig för kundens räkning (exempel: Azure-prenumeration har gått ogiltig), vi kräver att du tillhandahåller en anslutning webhook. Om du inte redan har ett webhook-system på plats, är den enklaste konfigurationen att ha en Logikapp för HTTP-slutpunkt som ska lyssna efter alla händelser publiceras till den och hantera dem på rätt sätt. Mer information finns i <a href="https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint">anropa, utlösare, eller kapsla arbetsflöden med HTTP-slutpunkter i logic apps</a>                |
|  Azure AD-klient-ID och App-ID      |   I Azure-portalen kräver att du skapar en Active Directory-App så att vi kan verifiera anslutningen mellan våra två tjänster som finns bakom en autentiserad kommunikation. För dessa fält, skapa en AD-App och klistra in i motsvarande klient-Id och App-Id krävs. Observera att App-id är kopplad till din publisherID. Därför se till att samma App-ID som i alla erbjudanden.             |


Slutligen, om du väljer **sälj via Microsoft**, det finns en annan ny erbjuder flik med namnet **planer**. 

Den [planer fliken](./cpp-plans-tab.md) visar en lista över specifika planer och deras motsvarande priser som har stöd för din SaaS-app. Från och med dagens datum tillåter vi månatliga priser, med möjlighet att tillåta för 1 - eller 3-månaders kostnadsfri åtkomst. Dessa abonnemang och priser måste matcha den exakta planer och priser som du har i en egen plats för SaaS-app.

>[!NOTE] 
>Planer krävs endast om du väljer sälj via Microsoft.

### <a name="call-to-action-field"></a>Anrop till åtgärdsfält

Du kan välja meddelandet som visas på knappen för anskaffning av ditt erbjudande i anropet till åtgärdsfält. Följande alternativ är tillgängliga:

- Kostnadsfria – om du väljer det här alternativet kan du uppmanas att ange en utvärderingsversion URL där kunder kan få åtkomst till din SaaS-app. Exempel: https://contoso.com/trial
- Kostnadsfri utvärderingsversion – om du väljer det här alternativet kan du uppmanas för att ange en utvärderingsversion URL där kunder kan få åtkomst till din SaaS-app. Exempel: https://contoso.com/trial
- Kontakta mig

Mer information om anropet till åtgärdsalternativ finns Välj ett alternativ för publicering.

## <a name="next-steps"></a>Nästa steg

- [Planer fliken (valfritt)](./cpp-plans-tab.md)
- [Fliken Kanalinformation](./cpp-channel-info-tab.md)
