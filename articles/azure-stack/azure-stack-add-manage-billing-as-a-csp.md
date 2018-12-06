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
ms.date: 10/15/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: e65f85d8349c4c8c69a50e93b093c04250e73a4e
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52961474"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Hantera användning och fakturering för Azure Stack som en Molntjänstleverantör 

*Gäller för: integrerade Azure Stack-system*

Den här artikeln beskriver hur du registrerar Azure Stack som en Cloud Provider (CSP) och lägga till kunder.

Som en Kryptografiprovider fungerar med olika kunder som använder Azure Stack. Varje kund har en CSP-prenumeration i Azure. Du behöver dirigera användning från din Azure-Stack på varje användarprenumeration.

I följande diagram visas de steg som du måste välja ditt konto för delade tjänster och registrera Azure-konto med Azure Stack-konto. Registrerad, kan du registrera dina slutkunder.

**Hur du lägger till användningsspårning som en Kryptografiprovider**

[ ![Processen för att aktivera användning och hantering som en Molntjänstleverantör](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "processen för att aktivera användning och hantering som en Molntjänstleverantör") ](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Skapa en CSP eller APSS prenumeration

### <a name="cloud-service-provider-subscription-types"></a>Molntyper tjänstleverantör prenumeration

Du måste välja vilken typ av konto för delade tjänster som du använder för Azure Stack. Typer av prenumerationer som kan användas för registrering av en multitenant Azure Stack är:

 - Molntjänstleverantör 
 - Delade tjänster-partnerprenumeration 

#### <a name="azure-partner-shared-services"></a>Azure-Partner delade tjänster

Azure Partner delade tjänster (APSS)-prenumerationer är det önskade valet för registrering när en direkt CSP eller en CSP-återförsäljare fungerar Azure Stack.

APSS prenumerationer är associerade med en klient för delade tjänster. När du registrerar Azure Stack kan behöva du ange autentiseringsuppgifter för ett konto som är ägare till prenumerationen. Det konto som används för att registrera Azure Stack kan skilja sig från det administratörskonto som du använder för distributionen. Dessutom är de två kontona gör *inte* måste tillhöra samma domän. Med andra ord kan du distribuera med hjälp av den klient som du redan använder. Du kan till exempel använda ContosoCSP.onmicrosoft.com och sedan registrera dig med en annan klient, till exempel IURContosoCSP.onmicrosoft.com. Du måste komma ihåg att du loggar in med ContosoCSP.onmicrosoft.com när du gör dag att göra Azure Stack-administration. När du loggar in på Azure med hjälp av IURContosoCSP.onmicrosoft.com när du behöver göra registreringsåtgärder.

Se nedan för en beskrivning av APSS prenumerationer och instruktioner om hur du skapar prenumerationen [Lägg till Azure-Partner delade tjänster](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>CSP-prenumerationer

Molnprenumerationer för Service Provider (CSP) är det önskade alternativet för registrering när en CSP-återförsäljare eller en slutkunden fungerar Azure Stack.

## <a name="register-azure-stack"></a>Registrera Azure Stack

Använd den APSS-prenumeration som skapats efter information i föregående avsnitt för att registrera Azure Stack med Azure. Mer information finns i [registrera Azure Stack med din Azure-prenumeration](azure-stack-registration.md).

## <a name="add-end-customer"></a>Lägg till slutkunden

För att konfigurera Azure Stack så att när en ny klient använder resurser sin användning rapporteras till sin Cloud Service Provider (CSP)-prenumeration, se [Lägg till klient för användning och fakturering i Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Debitera rätt prenumerationer

Azure Stack använder en funktion som kallas registrering. En registrering är ett objekt som lagras i Azure. Objektet registrering dokument vilka Azure-prenumerationerna du använder för att ta betalt för en viss Azure Stack. Det här avsnittet handlar om vikten av registreringen.

Med registrering av kan Azure Stack:
 - Vidarebefordra Azure Stack-användningsdata till Azure Commerce och fakturerar en Azure-prenumeration.
 - Rapportera alla kunders användning på en annan prenumeration med en multitenant Azure Stack-distribution. Flera innehavare kan Azure Stack för olika organisationer på samma Azure Stack-instans.

Det finns en Standardprenumeration för varje Azure Stack, och många klient prenumerationer. Standard-prenumerationen är en Azure-prenumeration som debiteras om det inte finns en klientspecifik-prenumeration. Det måste vara först som prenumeration som har registrerats. Prenumerationen för användning med flera innehavare rapporterna ska fungera, måste vara en CSP eller APSS prenumeration.

Sedan har registreringen uppdaterats med en Azure-prenumeration för varje klient som kommer att använda Azure Stack. Klient-prenumerationer måste vara av typen CSP och måste dyker upp i den partner som äger standard-prenumerationen. Med andra ord kan du registrera någon annans kunder.

När Azure Stack vidarebefordrar användningsinformation till globala Azure, en tjänst i Azure konsultationer registreringen och mappar varje klients användning till lämplig-klientprenumeration. Om en klient inte har registrerats, går denna användning till standard-prenumerationen för Azure Stack-instans som den kommer från.

Eftersom klient-prenumerationer är CSP-prenumerationer, sin faktura som skickas till CSP-partner och information om användning visas inte för slutkunden.

## <a name="next-steps"></a>Nästa steg

 - Läs mer om CSP-programmet i [programmet Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
 - Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](azure-stack-billing-and-chargeback.md).
