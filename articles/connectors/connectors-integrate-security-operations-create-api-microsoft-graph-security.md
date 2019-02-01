---
title: Integrera säkerhetsåtgärder med Microsoft Graph-säkerhet – Azure Logic Apps
description: Förbättra appens threat protection, identifiering och åtgärder funktioner genom att hantera säkerhetsåtgärder med säkerhet för Microsoft Graph och Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, LADocs
ms.topic: article
ms.date: 01/30/19
tags: connectors
ms.openlocfilehash: 647df9e73804c8f261b58d5ede7c4b030d448fed
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513521"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Förbättra skydd genom att integrera säkerhetsåtgärder med Microsoft Graph säkerhets- och Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) connector, du kan förbättra hur din app identifierar, skyddar och svara på hot genom att skapa automatiserade arbetsflöden för att integrera Microsoft Security-produkter, tjänster och partner. Du kan till exempel skapa [Azure Security Center spelböcker](../security-center/security-center-playbooks.md) som övervakar och hanterar Microsoft Graph-Security-entiteter, till exempel aviseringar. Här följer några scenarier som stöds av Microsoft Graph-Security-anslutningen:

* Få aviseringar baserat på frågor eller efter avisering-ID. Exempelvis kan du hämta en lista som innehåller varningar med hög angelägenhetsgrad.
* Uppdatera aviseringar. Du kan till exempel uppdatera avisering tilldelningar, lägga till kommentarer i aviseringar eller tagga aviseringar.
* Övervaka när aviseringar skapas eller ändras genom att skapa [Avisera prenumerationer (webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Hantera din aviseringsprenumerationer. Du kan exempelvis ha aktiva prenumerationer, förlänga förfallotiden för en prenumeration eller ta bort prenumerationer.

Logikappens arbetsflöde kan använda åtgärder som får svar från Microsoft Graph-Security-anslutningen och göra dessa utdata ska vara tillgängliga för andra åtgärder i arbetsflödet. Du kan också ha andra åtgärder i arbetsflödet använder utdata från Microsoft Graph Security connector-åtgärder. Om du får varningar med hög angelägenhetsgrad via Microsoft Graph Security connector kan kan du till exempel skicka aviseringarna i ett e-postmeddelande med Outlook-anslutningen. 

Läs mer om Microsoft Graph-säkerhet i den [Microsoft Graph Security API-översikt](https://aka.ms/graphsecuritydocs). Om du är nybörjare till logic apps, granska [vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Om du letar efter Microsoft Flow eller PowerApps, se [vad är Flow?](https://flow.microsoft.com/) eller [vad är PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Om du vill använda Microsoft Graph-Security-anslutningstjänsten måste du ha *explicit angivna* Azure Active Directory (AD) klient administratörens godkännande, som är en del av den [autentiseringskrav för Microsoft Graph-säkerhet ](https://aka.ms/graphsecurityauth). Den här medgivande kräver Microsoft Graph-Security-kopplingens program-ID och namn, som du kan också hitta i den [Azure-portalen](https://portal.azure.com):

   | Egenskap  | Värde |
   |----------|-------|
   | **Programnamn** | `MicrosoftGraphSecurityConnector` |
   | **Program-ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   Om du vill ge ditt medgivande för anslutningstjänsten, din Azure AD-Innehavaradministratör antingen de här stegen:

   * [Bevilja klient administratörens godkännande för Azure AD-program](../active-directory/develop/v2-permissions-and-consent.md).

   * Under din logikapp första körningen, din app kan begära godkännande från din Azure AD-Innehavaradministratör via den [samtycke upplevelsen](../active-directory/develop/application-consent-experience.md).
   
* Grundläggande kunskaper om [hur du skapar logikappar](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Logikappen där du vill komma åt dina Microsoft Graph-Security-entiteter, till exempel aviseringar. Den här anslutningen har för närvarande inga utlösare. Så om du vill använda en Microsoft Graph-Security-åtgärd, starta din logikapp med en utlösare, till exempel, **upprepning** utlösaren.

## <a name="connect-to-microsoft-graph-security"></a>Ansluta till Microsoft Graph-säkerhet 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på den [Azure-portalen](https://portal.azure.com/), och öppna logikappen i Logic App Designer, om inte redan är öppna.

1. Tom logic apps, lägger du till utlösaren och andra åtgärder som du vill innan du lägger till en Microsoft Graph-Security-åtgärd.

   ELLER

   För befintliga logikappar under det sista steget där du vill lägga till en åtgärd i Microsoft Graph Security, Välj **nytt steg**.

   ELLER

   Om du vill lägga till en åtgärd mellan stegen, flyttar du pekaren över pilen mellan stegen. 
   Välj plustecknet (+) som visas och välj **Lägg till en åtgärd**.

1. I sökrutan anger du ”microsoft graph-säkerhet” som filter. Välj vilken åtgärd du önska från åtgärdslistan över.

1. Logga in med dina autentiseringsuppgifter för Microsoft Graph.

1. Ange informationen som krävs för din valda åtgärd och fortsätt att utveckla logikappens arbetsflöde.

## <a name="add-actions"></a>Lägg till åtgärder

Här följer mer information om hur du använder olika åtgärder som är tillgängliga med Microsoft Graph Security connector.

### <a name="manage-alerts"></a>Hantera aviseringar

För att filtrera, sortera, eller få de senaste resultaten, ange *endast* den [ODATA-frågeparametrar som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Ange inte* fullständig bas-URL eller HTTP-åtgärden, till exempel `https://graph.microsoft.com/v1.0/security/alerts`, eller `GET` eller `PATCH` igen. Här är ett exempel som visar parametrarna för en **få aviseringar** åtgärd när du vill ha en lista med aviseringar med hög allvarlighetsgrad:

`Filter alerts value as Severity eq 'high'`

Mer information om frågor som du kan använda med den här anslutningen finns i den [Microsoft Graph säkerhetsaviseringar referensdokumentation](https://docs.microsoft.com/graph/api/alert-list). Om du vill skapa bättre upplevelser med den här anslutningen, Lär dig mer om den [schemat egenskaper aviseringar](https://docs.microsoft.com/graph/api/resources/alert) som stöder anslutningen.

| Åtgärd | Beskrivning |
|--------|-------------|
| **Få aviseringar** | Hämta aviseringar filtrerade baserat på en eller flera [Avisera egenskaper](https://docs.microsoft.com/graph/api/resources/alert), till exempel: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Hämta avisering efter ID** | Hämta en specifik avisering baserat på den avisering-ID. | 
| **Uppdatering av avisering** | Uppdatera en specifik avisering baserat på den avisering-ID. <p>Om du vill se till att du skickar egenskaperna krävs och kan redigeras i din begäran, se den [redigerbara egenskaper för aviseringar](https://docs.microsoft.com/graph/api/alert-update). Om du vill tilldela en avisering till säkerhetsanalytiker så att de kan undersöka, kan du till exempel uppdatera aviseringens **tilldelats** egenskapen. |
|||

### <a name="manage-alert-subscriptions"></a>Hantera aviseringsprenumerationer

Har stöd för Microsoft Graph [ *prenumerationer*](https://docs.microsoft.com/graph/api/resources/subscription), eller [ *webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Om du vill hämta, uppdatera, eller ta bort prenumerationer, ange den [ODATA-frågeparametrar som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) till Microsoft Graph-entitet konstruera och inkludera `security/alerts` följt av ODATA-fråga. 
*Omfattar inte* bas-URL, till exempel `https://graph.microsoft.com/v1.0`. Använd i stället format i det här exemplet:

`security/alerts?$filter=status eq 'New'`

| Åtgärd | Beskrivning |
|--------|-------------|
| **Skapa prenumerationer** | [Skapa en prenumeration](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) som meddelar dig om eventuella ändringar. Du kan filtrera den här prenumerationen för de specifika aviseringstyper som du vill. Du kan till exempel skapa en prenumeration som meddelar dig om varningar med hög angelägenhetsgrad. |
| **Hämta aktiva prenumerationer** | [Hämta läggs prenumerationer](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Uppdatera prenumeration** | [Uppdatera en prenumeration](https://docs.microsoft.com/graph/api/subscription-update) genom att ange prenumerations-ID. Om du vill förlänga din prenumeration kan du till exempel uppdatera prenumerationens `expirationDateTime` egenskapen. | 
| **Ta bort prenumeration** | [Tar bort en prenumeration](https://docs.microsoft.com/graph/api/subscription-delete) genom att ange prenumerations-ID. | 
||| 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och begränsningar som beskrivs av anslutningsappens OpenAPI (tidigare Swagger) beskrivning, granska kopplingens [referenssida](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Få support

Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Läs mer om andra [Logic Apps-anslutningsprogram](../connectors/apis-list.md)
