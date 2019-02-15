---
title: Hantera användning och fakturering för Azure Stack som en Molntjänstleverantör | Microsoft Docs
description: En genomgång och registrera Azure Stack som en Cloud Provider (CSP) och lägga till kunder för fakturering.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 1ba3697b5c683ed2e892396db71328e0ed41fdce
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2019
ms.locfileid: "56266925"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Hantera användning och fakturering för Azure Stack som en Molntjänstleverantör

*Gäller för: Integrerade Azure Stack-system*

Den här artikeln beskriver hur du registrerar Azure Stack som en Cloud Provider (CSP) och lägga till kunder.

Som en Kryptografiprovider fungerar med olika kunder som använder Azure Stack. Varje kund har en CSP-prenumeration i Azure. Du måste direkt användning från din Azure-Stack på varje användarprenumeration.

Följande bild visar nödvändiga steg för att välja ditt konto för delade tjänster och registrera Azure-konto med Azure Stack-konto. När registrerad, men du kan registrera dina slutkunder:

[![Processen för att aktivera användning och hantering som en Molntjänstleverantör](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "processen för att aktivera användning och hantering som en Molntjänstleverantör")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Skapa en CSP eller APSS prenumeration

### <a name="cloud-service-provider-subscription-types"></a>Molntyper tjänstleverantör prenumeration

Välj typ av konto för delade tjänster som du använder för Azure Stack. Typer av prenumerationer som kan användas för registrering av en multitenant Azure Stack är:

- Cloud Service Provider
- Delade tjänster-partnerprenumeration

#### <a name="azure-partner-shared-services"></a>Azure-Partner delade tjänster

Azure Partner delade tjänster (APSS)-prenumerationer är det önskade valet för registrering när en direkt CSP eller en CSP-återförsäljare fungerar Azure Stack.

APSS prenumerationer är associerade med en klient för delade tjänster. När du registrerar Azure Stack kan ange du autentiseringsuppgifter för ett konto som är ägare till prenumerationen. Det konto som används för att registrera Azure Stack kan skilja sig från det administratörskonto som du använder för distributionen. Dessutom är behöver de två kontona inte tillhöra samma domän. Du kan distribuera med hjälp av den klient som du redan använder. Du kan till exempel använda `ContosoCSP.onmicrosoft.com`, sedan registrerar med hjälp av en annan klient, till exempel `IURContosoCSP.onmicrosoft.com`. Du måste komma ihåg att logga in med `ContosoCSP.onmicrosoft.com` när du utför dagliga Azure Stack-administration. Du loggar in på Azure med hjälp av `IURContosoCSP.onmicrosoft.com` när du behöver göra registreringsåtgärder.

En beskrivning av APSS prenumerationer och hur du skapar dem finns i [Lägg till Azure-Partner delade tjänster](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>CSP-prenumerationer

Molnprenumerationer för Service Provider (CSP) är det önskade alternativet för registrering när en CSP-återförsäljare eller en slutkunden fungerar Azure Stack.

## <a name="register-azure-stack"></a>Registrera Azure Stack

Använda APSS-prenumeration som skapats med hjälp av informationen i föregående avsnitt för att registrera Azure Stack med Azure. Mer information finns i [registrera Azure Stack med din Azure-prenumeration](azure-stack-registration.md).

## <a name="add-end-customer"></a>Lägg till slutkunden

För att konfigurera Azure Stack så att när en ny klient använder resurser, deras användning rapporteras till deras Cloud Service Provider (CSP)-prenumeration, se [Lägg till klient för användning och fakturering i Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Debitera rätt prenumerationer

Azure Stack använder en funktion som kallas registrering. En registrering är ett objekt som lagras i Azure. Objektet registrering dokument vilka Azure-prenumerationerna du använder för att ta betalt för en viss Azure Stack. Det här avsnittet handlar om vikten av registreringen.

Med registrering av kan Azure Stack:

- Vidarebefordra Azure Stack-användningsdata till Azure Commerce och fakturerar en Azure-prenumeration.
- Rapportera varje kund '' s användning på en annan prenumeration med en multitenant Azure Stack-distribution. Flera innehavare kan Azure Stack för olika organisationer på samma Azure Stack-instans.

Det finns en Standardprenumeration för varje Azure Stack, och många klient prenumerationer. Standard-prenumerationen är en Azure-prenumeration som debiteras om det finns ingen klientspecifik-prenumeration. Det måste vara den första prenumerationen som ska registreras. Prenumerationen för användning med flera innehavare rapporterna ska fungera, måste vara en CSP eller APSS prenumeration.

Sedan har registreringen uppdaterats med en Azure-prenumeration för varje klient som använder Azure Stack. Klient-prenumerationer måste vara av typen CSP och måste dyker upp i den partner som äger standard-prenumerationen. Du kan inte registrera någon annans kunder.

När Azure Stack vidarebefordrar användningsinformation till globala Azure, en tjänst i Azure konsultationer registreringen och mappar varje klients användning till lämplig-klientprenumeration. Om en klient inte har registrerats, går denna användning till standard-prenumerationen för Azure Stack-instans som den kommer från.

Eftersom klient-prenumerationer är CSP-prenumerationer, sin faktura som skickas till CSP-partner och information om användning visas inte för slutkunden.

## <a name="next-steps"></a>Nästa steg

- Läs mer om CSP-programmet i [programmet Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](azure-stack-billing-and-chargeback.md).
