---
title: Integrera och hantera säkerhets åtgärder & Microsoft Graph säkerhet
description: Förbättra appens hot skydd, identifiering och svar med Microsoft Graph Security & Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/30/2019
tags: connectors
ms.openlocfilehash: 7e9cc2d8d38af7e5e6cf26ccc3659ee58ef17e59
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789060"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Förbättra hot skyddet genom att integrera säkerhets åtgärder med Microsoft Graph säkerhets & Azure Logic Apps

Med [Azure Logic Apps](../logic-apps/logic-apps-overview.md) och [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) Connector kan du förbättra hur appen identifierar, skyddar och svarar på hot genom att skapa automatiserade arbets flöden för att integrera Microsofts säkerhets produkter, tjänster och partners. Du kan till exempel skapa [Azure Security Center spel böcker](../security-center/security-center-playbooks.md) som övervakar och hanterar Microsoft Graph säkerhetsentiteter, till exempel aviseringar. Här följer några scenarier som stöds av Microsoft Graph Security Connector:

* Hämta aviseringar baserat på frågor eller aviserings-ID. Du kan till exempel hämta en lista som innehåller aviseringar med hög allvarlighets grad.
* Uppdatera aviseringar. Du kan till exempel uppdatera aviserings tilldelningar, lägga till kommentarer till aviseringar eller tagga aviseringar.
* Övervaka när aviseringar skapas eller ändras genom att skapa [aviserings prenumerationer (Webhooks)](https://docs.microsoft.com/graph/api/resources/webhooks).
* Hantera dina aviserings prenumerationer. Du kan till exempel få aktiva prenumerationer, förlänga förfallo tiden för en prenumeration eller ta bort prenumerationer.

Din Logic Apps-arbetsflöde kan använda åtgärder som får svar från Microsoft Graph Security Connector och göra utdata tillgängliga för andra åtgärder i ditt arbets flöde. Du kan också använda andra åtgärder i arbets flödet för att använda utdata från åtgärder för Microsoft Graph säkerhets anslutning. Om du till exempel får aviseringar med hög allvarlighets grad via Microsoft Graph Security Connector kan du skicka aviseringarna i ett e-postmeddelande med hjälp av Outlook-anslutningen. 

Mer information om Microsoft Graph säkerhet finns i [Översikt över Microsoft Graph Security API](https://aka.ms/graphsecuritydocs). Om du inte har arbetat med Logic Apps läser du [Vad är Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Om du letar efter Microsoft Flow eller PowerApps, se [Vad är Flow?](https://flow.microsoft.com/) eller [Vad är PowerApps?](https://powerapps.microsoft.com/)

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du heller inte har någon Azure-prenumeration kan du [registrera ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/). 

* Om du vill använda Microsoft Graph-säkerhetsanslutningen måste du *uttryckligen* angett Azure Active Directory (AD) klient administratörs medgivande, som är en del av [kraven för Microsoft Graph säkerhets autentisering](https://aka.ms/graphsecurityauth). Detta medgivande kräver Microsoft Graph Security Connectors program-ID och namn, som du även hittar i [Azure Portal](https://portal.azure.com):

   | Egenskap | Värde |
   |----------|-------|
   | **Program namn** | `MicrosoftGraphSecurityConnector` |
   | **Program-ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
   |||

   För att bevilja medgivande för anslutningen kan din Azure AD-innehavaradministratör följa de här stegen:

   * [Bevilja klient organisationens administratörs medgivande för Azure AD-program](../active-directory/develop/v2-permissions-and-consent.md).

   * Under den första körningen av din Logic Apps kan din app begära medgivande från din Azure AD-innehavaradministratör genom [programmets medgivande upplevelse](../active-directory/develop/application-consent-experience.md).
   
* Grundläggande information om [hur du skapar Logic Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Den Logic app där du vill få åtkomst till dina Microsoft Graph säkerhetsentiteter, till exempel aviseringar. Den här anslutningen har för närvarande inga utlösare. Om du vill använda en Microsoft Graph säkerhets åtgärd startar du din Logic app med en utlösare, till exempel utlösaren **upprepning** .

## <a name="connect-to-microsoft-graph-security"></a>Anslut till Microsoft Graph säkerhet 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Logga in på [Azure Portal](https://portal.azure.com/)och öppna din Logic app i Logic App Designer, om du inte redan har gjort det.

1. För tomma Logi Kap par lägger du till utlösaren och andra åtgärder som du vill ha innan du lägger till en Microsoft Graph säkerhets åtgärd.

   ELLER

   För befintliga Logi Kap par, under det sista steget där du vill lägga till en Microsoft Graph säkerhets åtgärd, väljer du **nytt steg**.

   ELLER

   Om du vill lägga till en åtgärd mellan stegen flyttar du pekaren över pilen mellan stegen. 
   Välj plus tecknet (+) som visas och välj **Lägg till en åtgärd**.

1. Skriv "Microsoft Graph Security" som filter i rutan Sök. Välj den åtgärd du vill använda i listan åtgärder.

1. Logga in med dina Microsoft Graph säkerhets uppgifter.

1. Ange nödvändig information för den valda åtgärden och fortsätt att skapa din Logic Apps-arbetsflöde.

## <a name="add-actions"></a>Lägg till åtgärder

Här är mer information om hur du använder de olika åtgärderna som finns tillgängliga med Microsoft Graph Security Connector.

### <a name="manage-alerts"></a>Hantera aviseringar

Om du vill filtrera, sortera eller få de senaste resultaten anger du *bara* [OData-frågeparametrar som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters). *Ange inte* den fullständiga bas-URL: en eller http-åtgärden, till exempel `https://graph.microsoft.com/v1.0/security/alerts`eller åtgärden `GET` eller `PATCH`. Här är ett speciellt exempel som visar parametrar för en åtgärd för att **Hämta aviseringar** när du vill ha en lista med aviseringar med hög allvarlighets grad:

`Filter alerts value as Severity eq 'high'`

Mer information om frågor som du kan använda med den här anslutnings tjänsten finns i [referens dokumentationen för Microsoft Graph säkerhets aviseringar](https://docs.microsoft.com/graph/api/alert-list). Om du vill bygga förbättrade upplevelser med den här anslutningen kan du läsa mer om de [schema egenskaper](https://docs.microsoft.com/graph/api/resources/alert) som stöds av anslutnings programmet.

| Åtgärd | Beskrivning |
|--------|-------------|
| **Hämta aviseringar** | Hämta aviseringar som filtrerats baserat på en eller flera [aviserings egenskaper](https://docs.microsoft.com/graph/api/resources/alert), till exempel: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **Hämta avisering efter ID** | Få en speciell avisering baserat på aviserings-ID: t. | 
| **Uppdatera avisering** | Uppdatera en speciell avisering baserat på aviserings-ID: t. <p>För att se till att du överför de nödvändiga och redigerbara egenskaperna i din begäran, se de [redigerbara egenskaperna för aviseringar](https://docs.microsoft.com/graph/api/alert-update). Om du till exempel vill tilldela en avisering till en säkerhetsanalytiker så att de kan undersöka, kan du uppdatera aviseringens **tilldelade** egenskap. |
|||

### <a name="manage-alert-subscriptions"></a>Hantera aviserings prenumerationer

Microsoft Graph stöder [*prenumerationer*](https://docs.microsoft.com/graph/api/resources/subscription)eller [*Webhooks*](https://docs.microsoft.com/graph/api/resources/webhooks). Om du vill hämta, uppdatera eller ta bort prenumerationer anger du [OData-frågeparametrar som stöds av Microsoft Graph](https://docs.microsoft.com/graph/query-parameters) till entiteten Microsoft Graph entitets konstruktion och inkluderar `security/alerts` följt av OData-frågan. 
*Ta inte med* bas-URL: en, till exempel `https://graph.microsoft.com/v1.0`. Använd i stället formatet i det här exemplet:

`security/alerts?$filter=status eq 'New'`

| Åtgärd | Beskrivning |
|--------|-------------|
| **Skapa prenumerationer** | [Skapa en prenumeration](https://docs.microsoft.com/graph/api/subscription-post-subscriptions) som meddelar dig om eventuella ändringar. Du kan filtrera den här prenumerationen för de olika aviserings typer som du vill använda. Du kan till exempel skapa en prenumeration som meddelar dig om aviseringar med hög allvarlighets grad. |
| **Hämta aktiva prenumerationer** | [Hämta prenumerationer som inte har gått ut](https://docs.microsoft.com/graph/api/subscription-list). | 
| **Uppdatera prenumeration** | [Uppdatera en prenumeration](https://docs.microsoft.com/graph/api/subscription-update) genom att ange prenumerations-ID: t. Om du till exempel vill utöka din prenumeration kan du uppdatera prenumerationens `expirationDateTime`-egenskap. | 
| **Ta bort prenumeration** | [Ta bort en prenumeration](https://docs.microsoft.com/graph/api/subscription-delete) genom att ange prenumerations-ID: t. | 
||| 

## <a name="connector-reference"></a>Referens för anslutningsapp

Teknisk information om utlösare, åtgärder och gränser, som beskrivs av kopplingens OpenAPI (tidigare Swagger) Beskrivning, finns i kopplingens [referens sida](https://aka.ms/graphsecurityconnectorreference).

## <a name="get-support"></a>Få support

Om du har frågor kan du besöka [forumet för Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
Om du vill skicka in eller rösta på förslag på funktioner besöker du [webbplatsen för Logic Apps-användarfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om andra [Logic Apps anslutningar](../connectors/apis-list.md)
