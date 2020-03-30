---
title: Prenumerationer i Azure API Management | Microsoft-dokument
description: Lär dig mer om konceptet med prenumerationer i Azure API Management.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073408"
---
# <a name="subscriptions-in-azure-api-management"></a>Prenumerationer i Azure API Management

Prenumerationer är ett viktigt begrepp i Azure API Management. De är det vanligaste sättet för API-konsumenter att få åtkomst till API:er som publiceras via en API Management-instans. Den här artikeln ger en översikt över konceptet.

## <a name="what-are-subscriptions"></a>Vad är prenumerationer?

När du publicerar API:er via API Management är det enkelt och vanligt att skydda åtkomsten till dessa API:er med hjälp av prenumerationsnycklar. Utvecklare som behöver använda de publicerade API:erna måste inkludera en giltig prenumerationsnyckel i HTTP-begäranden när de ringer anrop till dessa API:er. Annars avvisas anropen omedelbart av API Management-gatewayen. De vidarebefordras inte till backend-tjänsterna.

För att få en prenumerationsnyckel för åtkomst till API:er krävs en prenumeration. En prenumeration är i huvudsak en namngiven behållare för ett par prenumerationsnycklar. Utvecklare som behöver använda de publicerade API:erna kan få prenumerationer. Och de behöver inte godkännande från API-utgivare. API-utgivare kan också skapa prenumerationer direkt för API-konsumenter.

> [!TIP]
> API Management stöder också andra mekanismer för att skydda åtkomsten till API:er, inklusive följande exempel:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Klientcertifikat](api-management-howto-mutual-certificates-for-clients.md)
> - [IP-vitlistning](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Prenumerationernas omfattning

Prenumerationer kan associeras med olika scope: produkt, alla API:er eller ett enskilt API.

### <a name="subscriptions-for-a-product"></a>Prenumerationer på en produkt

Traditionellt har prenumerationer i API Management alltid associerats med ett enda [API-produktomfång.](api-management-terminology.md) Utvecklare hittade listan över produkter på utvecklarportalen. Sedan skickade de prenumerationsförfrågningar för de produkter de ville använda. När en prenumerationsbegäran har godkänts, antingen automatiskt eller av API-utgivare, kan utvecklaren använda nycklarna i den för att komma åt alla API:er i produkten. För närvarande visar utvecklarportalen endast prenumerationer för produktomfattning under avsnittet användarprofil. 

![Produktprenumerationer](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Under vissa scenarier kanske API-utgivare vill publicera en API-produkt för allmänheten utan krav på prenumerationer. De kan avmarkera alternativet **Kräv prenumeration** på sidan **Inställningar** för produkten i Azure-portalen. Därför kan alla API:er under produkten nås utan en API-nyckel.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Prenumerationer för alla API:er eller ett enskilt API

När vi introducerade [förbrukningsnivån](https://aka.ms/apimconsumptionblog) för API Management gjorde vi några ändringar för att effektivisera nyckelhanteringen:
- Först har vi lagt till ytterligare två prenumerationsscope: alla API:er och ett enda API. Prenumerationernas omfattning är inte längre begränsad till en API-produkt. Det är nu möjligt att skapa nycklar som ger åtkomst till ett API, eller alla API:er i en API Management-instans, utan att behöva skapa en produkt och lägga till API:erna i den först. Dessutom levereras varje API Management-instans nu med en oföränderlig prenumeration med alla API:er. Den här prenumerationen gör det enklare och enklare att testa och felsöka API:er i testkonsolen.

- För det andra tillåter API Management nu **fristående** prenumerationer. Prenumerationer behöver inte längre associeras med ett utvecklarkonto. Den här funktionen är användbar i scenarier som när flera utvecklare eller team delar en prenumeration.

- Slutligen kan API-utgivare nu [skapa prenumerationer](api-management-howto-create-subscriptions.md) direkt i Azure-portalen:

    ![Flexibla abonnemang](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Nästa steg
Få mer information om API Management:

+ Lär dig andra [begrepp](api-management-terminology.md) i API Management.
+ Följ våra [tutorials](import-and-publish.md) för att lära dig mer om API Management.
+ Se vår [FAQ-sida](api-management-faq.md) för vanliga frågor.
