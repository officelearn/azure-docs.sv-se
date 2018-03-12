---
title: "Användning rapporteringssystem för molntjänst-Providers för Azure-Stack | Microsoft Docs"
description: "Azure-stacken innehåller den infrastruktur som behövs för att spåra användning när det inträffar och vidarebefordrar det till Azure."
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
ms.date: 03/08/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: 4ac808e0e85b1daeb54a3f2fd7bec0a7c10aa13e
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2018
---
## <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Användning rapporteringssystem för molntjänst-Providers

Azure-stacken innehåller den infrastruktur som behövs för att spåra användning när det inträffar och vidarebefordrar det till Azure. I Azure, Azure Commerce bearbetar användningsdata och avgifter användning till de lämpliga Azure-prenumerationerna på samma sätt som förbrukning som äger rum i globala Azure-molnet.

Du bör vara medveten om att vissa begrepp är konsekventa mellan globala Azure och Azure-stacken. Azure-stacken har lokala prenumerationer som uppfyller en liknande roll till en Azure-prenumeration. Lokala prenumerationer är endast giltiga lokalt. Lokala prenumerationer mappas till Azure-prenumerationer när användning vidarebefordras till Azure.

Azure-stacken har lokal användning mätare. Lokal användning är mappad till mätare som används i Azure handel. Dock är mätaren-ID: N olika. Det finns flera mätare lokalt än Microsoft används för fakturering.

Det finns några skillnader mellan hur tjänster prissatta i Azure-stacken och Azure. Till exempel i Azure-stacken baseras kostnad för virtuella datorer bara på vcore/timmar med samma hastighet för alla VM-serien, till skillnad från Azure. Det beror på att olika priser i globala Azure avspeglar olika typer av maskinvara. I Azure-stacken ger kunden maskinvara, så det finns ingen anledning att debiterar olika kostnader för olika VM-klasser.

Du kan läsa mer om Azure Stack-mätare som används i Commerce och priserna Partner Center på samma sätt som du skulle för Azure-tjänster:

1. Gå till Partner Center den **instrumentpanelen menyn** > **priser och erbjudanden**.
2. Under **användningsbaserad services**väljer **aktuella**.
3. Öppna den **Azure i globala CSP prislista** kalkylblad.
4. Filtrera efter **Region = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Användnings- och fakturering felkoder

Följande felmeddelanden kan uppstå när du lägger till innehavare till en registrering.

| Fel                           | Information                                                                                                                                                                                                                                                                                                                           | Kommentarer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | Den angivna registreringen hittades inte. Kontrollera att följande information har angetts på rätt sätt:<br>1. Prenumerations-ID (mervärde: _prenumerations-ID_),<br>2. Resursgrupp (mervärde: _resursgruppen_),<br>3. Registrering av namn (mervärde: _registrering namnet_).                             | Felet uppstår vanligen när den information som pekar på den första registreringen inte är korrekt. Om du behöver verifiera resursgrupp och namn för din registrering av hittar du den i Azure-portalen genom att lista alla resurser. Om du hittar fler än en resurs för registrering, titta på CloudDeploymentID i egenskaperna för och väljer registreringen vars CloudDeploymentID matchar ditt moln. Du kan använda den här PowerShell i Azure-stacken för att hitta CloudDeploymentID:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | Den angivna _kunden prenumerations-ID_ och _registrering namn_ prenumerations-ID ägs inte av samma Microsoft Molntjänstleverantören. Kontrollera att kunden prenumerations-ID är korrekt. Kontakta supporten om problemet kvarstår. | Det här felet uppstår när kundprenumerationen är en CSP-prenumeration, men den samlar in till en CSP-partner skiljer sig från den som den prenumeration som används i den första registreringen samlar in. Den här kontrollen görs för att förhindra att en situation som skulle resultera i fakturering en CSP-partner som inte är ansvarig för Azure-stacken används.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | Den '_kunden prenumerations-ID_' är inte giltig. Kontrollera att en giltig Azure-prenumeration har angetts.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _Kunden prenumerations-ID_ hittades inte under _registration namn. Kontrollera att en giltig Azure-prenumeration som används och att det prenumerations-ID har lagts till registrering med PUT-åtgärden.                                                   | Det här felet uppstår när du försöker allvarlighetsgrad för inkompatibilitet som en klient har lagts till en prenumeration och kundprenumerationen hittades som ska associeras med registreringen. Kunden har inte lagts till registreringen eller prenumerations-ID har skrivits felaktigt.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | Den angivna _registrering namn_ inte är godkänd för att använda multitenans. Skicka ett e-postmeddelande till azstCSP@microsoft.com och innehåller namnet på din registrering, resursgruppen och prenumerations-ID som används i registreringen.                                                                                    | En registrering måste godkännas för flera innehavare av Microsoft innan du kan börja med att lägga till klienter. Finns i avsnittet registrera klienter i det här dokumentet för ytterligare förklaring.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | Kunden prenumerationer åtgärder stöds inte för frånkopplade kunder. För att kunna använda den här funktionen registrera med betala som använder licensiering.                                                                                                                                                                    | Registreringen av du försöker lägga till klienter är en kapacitet-registrering som är när registreringen har skapats genom att parametern BillingModel kapacitet används. Betalar bara när du använder registreringar är tillåtna för att lägga till klienter. Du måste registrera igen med parametern BillingModel PayAsYouUse.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | Den angivna _kunden prenumerations-ID_ är inte en giltig CSP-prenumeration. Kontrollera att en giltig Azure-prenumeration har angetts.                                                                                                                                                        | Detta är mest sannolikt på grund av kundprenumerationen som skrev du fel.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | En av de överordnade servrarna returnerade ett oväntat fel. Försök igen senare. Om problemet kvarstår, kontakta support.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termer som används för fakturerings- och användningsdata

Följande termer och begrepp är används för användning och fakturering i Azure Stack:

| Period | Definition |
| --- | --- |
| Direkt CSP-partner | En partner för direkta Cloud Solution Providers (CSP) tar emot en faktura direkt från Microsoft för användning av Azure och Azure-stacken och växlar kunder direkt. |
| Indirekt CSP | Indirekt återförsäljare arbeta med en indirekt provider (även kallat en distributör). Återförsäljarna rekrytera slutkunder. indirekt providern innehåller faktureringsrelation med Microsoft hanterar kunden fakturering och ger ytterligare tjänster som Microsoft support. |
| End kunden | End kunder är företag och myndigheter som äger program och andra arbetsbelastningar som körs på Azure-stacken. |

## <a name="next-steps"></a>Nästa steg

 - Läs mer om CSP-programmet i [leverantör programmet](https://partnercenter.microsoft.com/en-us/partner/programs).
 - Läs mer om hur du hämtar information om användning från Azure-stacken i [användnings- och fakturering i Azure-stacken](/azure-stack-billing-and-chargeback.md).
