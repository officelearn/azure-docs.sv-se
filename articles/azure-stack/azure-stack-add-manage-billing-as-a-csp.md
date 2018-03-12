---
title: "Hantera användning och fakturering för Azure-stacken som en Molntjänstleverantör | Microsoft Docs"
description: "En genomgång registrera Azure Stack som en Molntjänstleverantör och lägga till kunder."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 23e3a675e6a464c92d26df220c8126c970f590a0
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Hantera användning och fakturering för Azure-stacken som en Molntjänstleverantör 

*Gäller för: Azure Stack integrerat system*

Den här artikeln beskriver hur du registrerar Azure Stack som en molnet Provider (CSP) och lägga till kunder.

Som en Kryptografiprovider troligen kommer att ha många olika kunder som använder Azure-stacken. Varje kund har en CSP-prenumeration i Azure och du behöver direkt användning från Azure-stacken till varje användares prenumeration.

I följande diagram visas de steg som du behöver välja kontot för delade tjänster och registrera azure-konto med kontot. När detta har gjorts, kan du publicera kunderna slutet.

**Steg för att lägga till som en Kryptografiprovider för uppföljning av användningen**

![Processen för att aktivera användning och hantering som en Molntjänstleverantör.](media\azure-stack-add-manage-billing-as-a-csp\process-add-useage-as-a-csp.png)

## <a name="create-a-csp-or-cspss-subscription"></a>Skapa en CSP eller CSPSS prenumeration

### <a name="cloud-service-provider-subscription-types"></a>Molntyper tjänstleverantör prenumeration

Du måste välja vilken typ av konto för delade tjänster som du använder för Azure-stacken. Typer av prenumerationer som kan användas för registrering av en multitenant Azure-stacken är:

 - Cloud Service Provider 
 - Partner delade tjänster prenumeration 

#### <a name="csp-shared-services"></a>CSP delade tjänster

Molnprenumerationer Service providern för delade tjänster (CSPSS) är det bästa valet för registrering när en direkt CSP eller distributör CSP fungerar Azure stacken.

CSPSS prenumerationer är associerade med en klient för delade tjänster. När du registrerar Azure-stacken, måste du ange autentiseringsuppgifter för ett konto som är ägare till prenumerationen. Det konto som används för att registrera Azure Stack kan skilja sig från det administratörskonto som du använder för distribution; två gör *inte* måste tillhöra samma domän. Med andra ord kan du distribuera med den klient som du redan använder. Du kan till exempel använda ContosoCSP.onmicrosoft.com och sedan registrera med hjälp av en annan klient, till exempel IURContosoCSP.onmicrosoft.com. Du kommer att behöva komma ihåg att du loggar in med ContosoCSP.onmicrosoft.com när du gör dag att göra Azure Stack administration. När du loggar in till Azure med IURContosoCSP.onmicrosoft.com när du behöver utföra åtgärder för registrering.

Referera till följande en beskrivning av CSPSS prenumerationer och anvisningar om hur du skapar prenumerationen [Lägg till Azure Partner delade tjänster](https://msdn.microsoft.com/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>CSP-prenumerationer

Molnprenumerationer för Service Provider (CSP) är det bästa valet för registrering när en CSP återförsäljare eller en end-kund fungerar Azure stacken.

## <a name="register-azure-stack"></a>Registrera Azure Stack

Om du vill registrera med Azure-stacken, se [registrera Azure stacken med din Azure-prenumeration](azure-stack-registration.md).

## <a name="add-end-customer"></a>Lägg till slutet kund

Om du vill konfigurera Azure Stack så att när en ny klient använder resurser sin användning kommer att rapporteras till prenumerationen molntjänst-providers (CSP), se [lägga till innehavaren för användning och fakturering till Azure-stacken](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Debitera rätt prenumerationer

Azure-stacken använder en funktion som kallas registrering. En registrering är ett objekt som lagras i Azure, vilket dokument som Azure-prenumeration(er) att använda debiterar för en viss Azure-stacken. Det här avsnittet behandlar vikten av registreringen.

Med registrering kan Azure Stack:
 - Vidarebefordra Azure Stack användningsdata till Azure handel och debiterar en Azure-prenumeration.
 - Rapportera varje kund användning på en annan prenumeration med en multitenant Azure Stack-distribution. Multitenancy kan Azure stackutrymme för att stödja olika organisationer i samma Azure Stack-instans.

För varje Azure-stacken är en standard-prenumeration och många klient prenumerationer vid behov. Standard-prenumerationen är en Azure-prenumeration som debiteras om det finns ingen klient-specifik prenumeration. Det måste vara först som ska registreras. Prenumerationen måste vara en CSP eller CSPSS prenumeration för flera innehavare användning av rapporterna ska fungera.

Sedan har registreringen uppdaterats med en Azure-prenumeration för varje klient som kommer att använda Azure-stacken. Klient-prenumerationer måste vara av typen CSP och måste slås samman till den partner som äger den standard-prenumerationen. Med andra ord kan du registrera någon annans kunder.

När Azure Stack vidarebefordrar användningsinformation till globala Azure, en tjänst i Azure tittar registreringen och mappar varje klientanvändning till lämplig klientprenumeration. Om en klient inte har registrerats blir denna användning till standard-prenumeration för Azure Stack-instansen som den kommer från.

Eftersom klienten prenumerationer är CSP-prenumerationer, deras faktura skickas till CSP-partner och information om användning visas inte för end-kund.



## <a name="next-steps"></a>Nästa steg

 - Läs mer om CSP-programmet i [leverantör programmet](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Läs mer om hur du hämtar information om användning från Azure-stacken i [användnings- och fakturering i Azure-stacken](/azure-stack-billing-and-chargeback.md).
