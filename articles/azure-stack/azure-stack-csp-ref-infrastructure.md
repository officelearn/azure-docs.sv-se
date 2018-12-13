---
title: Användning infrastruktur för rapportering för Molntjänstleverantörer för Azure Stack | Microsoft Docs
description: Azure Stack innehåller den infrastruktur som behövs för att spåra användning för klienter som underhålls av en Cloud Service Provider (CSP) när det inträffar och vidarebefordrar den till Azure.
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
ms.date: 07/12/2018
ms.author: sethm
ms.reviewer: alfredo
ms.openlocfilehash: 4d7d1407ff57c5adcc4790db4d2f47b70737fece
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250500"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Infrastruktur för rapportering för Molntjänstleverantörer användning

Azure Stack innehåller den infrastruktur som behövs för att spåra användning när det inträffar och vidarebefordrar den till Azure. I Azure, Azure Commerce bearbetar användningsdata och debiteras användningen av lämplig Azure-prenumerationer. Detta sker på samma sätt som användningsspårning övervakas i globala Azure-molnet.

Observera att vissa begrepp är konsekvent mellan globala Azure och Azure Stack. Azure Stack har lokala prenumerationer som uppfyller en liknande roll till en Azure-prenumeration. Lokala prenumerationer är endast giltiga lokalt. Lokala prenumerationer har mappats till Azure-prenumerationer när användning vidarebefordras till Azure.

Azure Stack har taxor för lokal användning. Lokal användning mappas till de mätare som används i Azure commerce. Mätnings-ID: N är dock olika. Det finns flera taxor lokalt än det som Microsoft använder för fakturering.

Det finns några skillnader mellan hur tjänster prissätts i Azure Stack och Azure. Till exempel i Azure Stack baseras kostnaden för virtuella datorer endast på vcore/timmar med samma avgift för alla VM-serier, till skillnad från Azure. Det beror på att olika priserna i globala Azure är olika typer av maskinvara. I Azure Stack som, ger kunden maskinvara, så det finns ingen anledning att debitera dig enligt olika kostnader för olika VM-klasser.

Du kan läsa mer om Azure Stack-taxor som används i handel och sina priser på Partner Center på samma sätt som för Azure-tjänster:

1. Partner Center går du till den **instrumentpanelen** > **priser och erbjudanden**.
2. Under **användningsbaserad services**väljer **aktuella**.
3. Öppna den **Azure i Global CSP-prislistan** kalkylblad.
4. Filtrera på **Region = Azure Stack**.

## <a name="usage-and-billing-error-codes"></a>Användning och fakturering felkoder

Följande felmeddelanden kan uppstå när du lägger till innehavare till en registrering.

| Fel                           | Information                                                                                                                                                                                                                                                                                                                           | Kommentarer                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| RegistrationNotFound            | Den angivna registreringen hittades inte. Kontrollera att följande har angetts korrekt:<br>1. Prenumerations-ID (angett värde: _prenumerations-ID_),<br>2. Resursgrupp (angett värde: _resursgrupp_),<br>3. Registreringssteg namn (angett värde: _registreringssteg namn_).                             | Det här felet uppstår vanligen när den information som pekar på den första registreringen inte är korrekt. Om du behöver verifiera resursgruppens namn och namnet på din registrering, kan du hitta den i Azure-portalen genom att lista alla resurser. Om du hittar fler än en resurs för registrering, titta på CloudDeploymentID i egenskaperna och välj registreringen vars CloudDeploymentID överensstämmer med ditt moln. Du kan använda det här PowerShell på Azure Stack för att hitta CloudDeploymentID:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId       | Den angivna _kundens prenumerations-ID_ och _registreringssteg namn_ prenumerations-ID inte ägs av samma Microsoft Molntjänstleverantören. Kontrollera att kunden prenumerations-ID är korrekt. Kontakta supporten om problemet kvarstår. | Det här felet uppstår när kundprenumerationen är en CSP-prenumeration, men den samlar in till en CSP-partner skiljer sig från det som den prenumeration som används i den första registreringen samlar in. Den här kontrollen görs för att förhindra att en situation som skulle resultera i fakturering CSP-partner som inte är ansvarig för Azure Stack används.                                                                                                                                                                                                                                                                          |
| InvalidCustomerSubscriptionId   | Den ”_kundens prenumerations-ID_' är inte giltig. Kontrollera att en giltig Azure-prenumeration har angetts.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| CustomerSubscriptionNotFound    | _Kundens prenumerations-ID_ hittades inte under _registration namn. Kontrollera att en giltig Azure-prenumeration som används och att det prenumerations-ID har lagts till registreringen med hjälp av PUT-åtgärden.                                                   | Det här felet uppstår när du försöker varlighetsgrad för inkompatibilitet som en klient har lagts till en prenumeration och kundprenumerationen hittades som ska associeras med registreringen. Kunden har inte lagts till i registreringen eller prenumerations-ID har skrivits felaktigt.                                                                                                                                                                                                                                                                                                                                |
| UnauthorizedCspRegistration     | Den angivna _registreringssteg namn_ är inte godkänd för att använda flera innehavare. Skicka ett e- azstCSP@microsoft.com och inkludera namnet på din registrering, resursgrupp och prenumerations-ID som används i registreringen.                                                                                    | En registrering måste godkännas för flera innehavare av Microsoft innan du kan börja med att lägga till klienter. Se avsnittet registrera klienter i det här dokumentet för ytterligare förklaring.                                                                                                                                                                                                                                                                                                                                                                                                             |
| CustomerSubscriptionsNotAllowed | Kunden prenumerationer åtgärder stöds inte för frånkopplade kunder. För att kunna använda den här funktionen Omregistrera med betala som använder licensiering.                                                                                                                                                                    | Registreringen som du försöker lägga till klientorganisationer är en kapacitet-registrering, det vill säga när registreringen har skapats genom att parametern BillingModel kapacitet används. Endast betala för det du använder registreringar tillåts för att lägga till klienter. Du måste registrera igen med parametern BillingModel PayAsYouUse.                                                                                                                                                                                                                                                                                          |
| InvalidCSPSubscription          | Den angivna _kundens prenumerations-ID_ är inte en giltig CSP-prenumeration. Kontrollera att en giltig Azure-prenumeration har angetts.                                                                                                                                                        | Det här är mest sannolikt på grund av kundprenumerationen som skrev du fel.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| MetadataResolverBadGatewayError | En av de överordnade servrarna returnerade ett oväntat fel. Försök igen senare. Om problemet kvarstår, kontakta supporten.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Termer som används för fakturering och användning

Följande termer och begrepp finns används för användning och fakturering i Azure Stack:

| Period | Definition |
| --- | --- |
| Direkta CSP-partner | En direkt Cloud Solution Provider (CSP) partner får en faktura direkt från Microsoft för användning av Azure och Azure Stack och fakturor kunder direkt. |
| Indirekta CSP | Indirect-återförsäljare arbeta med en indirekt-leverantörer (även kallat en distributör). Återförsäljarna rekrytera slutkunder; indirekt-leverantörer innehåller faktureringsrelation med Microsoft hanterar kundfaktureringen och tillhandahåller ytterligare tjänster som produktsupport. |
| Slutkunden | Slutkunder är företag och myndigheter som äger program och andra arbetsbelastningar som körs på Azure Stack. |

## <a name="next-steps"></a>Nästa steg

 - Läs mer om CSP-programmet i [programmet Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
 - Läs mer om hur du hämtar information om användning från Azure Stack i [användning och fakturering i Azure Stack](azure-stack-billing-and-chargeback.md).
