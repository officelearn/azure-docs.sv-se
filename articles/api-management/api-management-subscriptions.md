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
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621862"
---
# <a name="subscriptions-in-azure-api-management"></a>Prenumerationer i Azure API Management

Prenumerationer är ett viktigt begrepp i Azure API Management (APIM). Det är det vanligaste sättet för API-kunderna att få åtkomst till API: er som publicerats via en APIM-instansen. Den här artikeln innehåller en översikt över konceptet.

## <a name="what-is-subscriptions"></a>Vad är prenumerationer

När du publicerar API: er via APIM, är det enklaste och vanligaste sättet att skydda åtkomsten till dessa API: er med hjälp av Prenumerationsnycklar. Med andra ord måste utvecklare som behöver använda den publicerade API: er innehålla en giltig prenumeration-nyckel i HTTP-begäranden när anrop till dessa API: er. I annat fall avvisas omedelbart av APIM-gatewayen anrop och vidarebefordras inte till backend-tjänster.

Om du vill ha en Prenumerationsnyckel för åtkomst till API: er, krävs en prenumeration. Prenumerationen är i grunden en namngiven behållare för ett nyckelpar prenumeration. Prenumerationer kan hämtas av utvecklare som behöver använda den publicerade API: er, med eller utan godkännande av API-utgivare. API-utgivare kan också skapa prenumerationer direkt, åt API-kunderna.

> [!TIP]
> APIM stöder också andra mekanismer för att skydda åtkomsten till API: er, inklusive [OAuth2.0](api-management-howto-protect-backend-with-aad.md), [klientcertifikat](api-management-howto-mutual-certificates-for-clients.md), och [IP-vitlistning](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Omfånget för prenumerationer

Prenumerationer kan associeras med olika omfång: produkten, alla API: er eller en enskild API.

### <a name="subscriptions-for-a-product"></a>Prenumerationer för en produkt

Traditionellt har prenumerationer i APIM alltid är kopplade till en enda [API-produkt](api-management-terminology.md) omfång. Utvecklare skulle hittar listan över produkter på Developer-portalen och skicka Prenumerationsbegäranden för de produkter som de vill använda. När en prenumeration som begäran har godkänts (automatiskt eller av API-utgivare), kan utvecklaren använda nycklarna i den att få åtkomst till alla API: er i produkten.

![Prenumerationer för produkten](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> I vissa fall vilja API-utgivare publicera en API-produkt för allmänheten utan krav på prenumerationer. De kan avmarkera den **kräver prenumeration** alternativet i den **inställningar** för produkten i Azure-portalen. Därför kan kan alla API: er under produkten användas utan en API-nyckel.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Prenumerationer för alla API: er eller en enskild API

> [!NOTE]
> Den här funktionen är för närvarande tillgängliga i den API Management-användningen nivån.

När vi har infört de [förbrukning](https://aka.ms/apimconsumptionblog) nivå av APIM, vi gjort några ändringar för att hantera nycklar. Först måste vi har lagt till två mer prenumeration scope - alla API: er och ett enda API. Omfånget för prenumerationer är inte längre begränsad till en API-produkt. Nu är det möjligt att skapa nycklar som beviljar åtkomst till ett API (eller alla API: er i en APIM-instansen), utan att behöva skapa en produkt och lägga till API: erna i den först. Varje APIM-instansen kommer dessutom nu med en oföränderlig, alla API: er-prenumeration, vilket gör det enklare och enklare att testa och felsöka API: er i Test-konsolen.

Dessutom kan APIM nu ”fristående” prenumerationer. Prenumerationer krävs inte längre att vara associerad med ett utvecklarkonto. Detta är användbart i scenarier, till exempel när en prenumeration delas av flera utvecklare eller team.

Slutligen API-utgivare kan nu [skapa prenumerationer](api-management-howto-create-subscriptions.md) direkt i Azure Portal:

![Flexibla prenumerationer](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Nästa steg
Mer information om API Management:

+ Läs andra [begrepp](api-management-terminology.md) i API Management
+ Följ våra [självstudier](import-and-publish.md) mer information om API Management
+ Kontrollera våra [FAQ-sida](api-management-faq.md) för vanliga frågor