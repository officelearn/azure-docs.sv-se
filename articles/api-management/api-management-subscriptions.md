---
title: Prenumerationer i Azure API Management | Microsoft Docs
description: Läs mer om begreppet prenumerationer i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 6f577530c42952c6340a15110bcd37383a5fca57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60693555"
---
# <a name="subscriptions-in-azure-api-management"></a>Prenumerationer i Azure API Management

Prenumerationer är ett viktigt begrepp i Azure API Management. Det är det vanligaste sättet för API-kunderna att få åtkomst till API: er som publicerats via en API Management-instans. Den här artikeln innehåller en översikt över konceptet.

## <a name="what-are-subscriptions"></a>Vad är prenumerationer?

När du publicera API: er via API Management är det enkelt och gemensamma för säker åtkomst till dessa API: er med hjälp av prenumerationsnycklar. Utvecklare som behöver använda den publicerade API: er måste innehålla en giltig prenumerationsnyckel i HTTP-begäranden när de göra anrop till dessa API: er. I annat fall avvisas anrop omedelbart av API Management-gatewayen. De är inte vidarebefordras till backend-tjänster.

Om du vill ha en prenumerationsnyckel för åtkomst till API: er, krävs en prenumeration. En prenumeration är i grunden en namngiven behållare för ett nyckelpar prenumeration. Utvecklare som behöver använda den publicerade API: er kan hämta prenumerationer. Och de behöver inte godkännande från API-utgivare. API-utgivare kan också skapa prenumerationer direkt för API-kunderna.

> [!TIP]
> API Management stöder även andra metoder för att skydda åtkomst till API: er, inklusive i följande exempel:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Klientcertifikat](api-management-howto-mutual-certificates-for-clients.md)
> - [IP-vitlistning](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Omfånget för prenumerationer

Prenumerationer kan associeras med olika omfång: produkten, alla API: er eller en enskild API.

### <a name="subscriptions-for-a-product"></a>Prenumerationer för en produkt

Traditionellt har prenumerationer i API Management har alltid är associerade med en enda [API-produkt](api-management-terminology.md) omfång. Utvecklare att hitta listan över produkter som på Developer-portalen. Sedan skickar de prenumerationsbegäranden för produkterna som de ville använda. När begäran om en prenumeration har godkänts, antingen automatiskt eller av API-utgivare kan utvecklaren använda nycklarna i den att få åtkomst till alla API: er i produkten.

![Prenumerationer för produkten](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> I vissa fall kanske API-utgivare vill publicera en API-produkt för allmänheten utan krav på prenumerationer. De kan avmarkera den **kräver prenumeration** alternativet på den **inställningar** för produkten i Azure-portalen. Därför kan kan alla API: er under produkten användas utan en API-nyckel.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Prenumerationer för alla API: er eller en enskild API

När vi har infört de [förbrukning](https://aka.ms/apimconsumptionblog) nivå av API Management kan vi gjort några ändringar för att hantera nycklar:
- Först måste vi har lagt till två fler prenumeration omfång: alla API: er och ett enda API. Omfånget för prenumerationer är inte längre begränsad till en API-produkt. Nu är det möjligt att skapa nycklar som beviljar åtkomst till ett API eller alla API: er i en API Management-instans, utan att behöva skapa en produkt och lägga till API: erna i den först. Varje API Management-instans kommer dessutom nu med en prenumeration som inte kan ändras, alla API: er. Den här prenumerationen gör det enklare och enklare att testa och felsöka API: er i test-konsolen.

- Andra API Management nu kan **fristående** prenumerationer. Prenumerationer krävs inte längre att vara associerad med ett utvecklarkonto. Den här funktionen är användbart i scenarier, t.ex när flera utvecklare eller team som delar en prenumeration.

- Slutligen API-utgivare kan nu [skapa prenumerationer](api-management-howto-create-subscriptions.md) direkt i Azure portal:

    ![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Läs andra [begrepp](api-management-terminology.md) i API Management.
+ Följ våra [självstudier](import-and-publish.md) mer information om API Management.
+ Kontrollera våra [FAQ-sida](api-management-faq.md) för vanliga frågor.