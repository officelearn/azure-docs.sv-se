---
title: Kunddatafunktioner för Azure Key Vault – Azure Key Vault | Microsoft-dokument
description: Läs mer om kunddata i Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: reference
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 67e1aeab4211249075b51bd0138d7875756a3483
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70883316"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault kunddatafunktioner

Azure Key Vault tar emot kunddata när valv, nycklar, hemligheter, certifikat och hanterade lagringskonton skapas eller uppdateras. Dessa kunddata är direkt synliga i Azure-portalen och via REST API. Kunddata kan redigeras eller tas bort genom att uppdatera eller ta bort objektet som innehåller data.

Systemåtkomstloggar genereras när en användare eller ett program kommer åt Key Vault. Detaljerade åtkomstloggar är tillgängliga för kunder som använder Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kunddata

Följande information identifierar kunddata i Azure Key Vault:

- Åtkomstprinciper för Azure Key Vault innehåller objekt-ID:er som representerar användare, grupper eller program
- Certifikatämnen kan innehålla e-postadresser eller andra användar- eller organisationsidentifierare
- Certifikatkontakter kan innehålla e-postadresser, namn eller telefonnummer för användare
- Certifikatutfärdare kan innehålla e-postadresser, namn, telefonnummer, kontouppgifter och organisationsinformation
- Godtyckliga taggar kan tillämpas på objekt i Azure Key Vault. Dessa objekt omfattar valv, nycklar, hemligheter, certifikat och lagringskonton. De taggar som används kan innehålla personuppgifter
- Azure Key Vault-åtkomstloggar innehåller objekt-ID:er, [UPN](../active-directory/hybrid/plan-connect-userprincipalname.md)och IP-adresser för varje REST API-anrop
- Diagnostikloggar för Azure Key Vault kan innehålla objekt-ID:er och IP-adresser för REST API-anrop

## <a name="deleting-customer-data"></a>Ta bort kunddata

Samma REST-API:er, portalupplevelsen och SDK:er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade lagringskonton kan också uppdatera och ta bort dessa objekt.

Med mjuk borttagning kan du återställa borttagna data i 90 dagar efter borttagningen. När du använder mjuk borttagning kan data tas bort permanent innan lagringsperioden på 90 dagar upphör att gälla genom att utföra en rensningsåtgärd. Om valvet eller prenumerationen har konfigurerats för att blockera rensningsåtgärder, är det inte möjligt att ta bort data permanent förrän den schemalagda kvarhållningsperioden har passerats.

## <a name="exporting-customer-data"></a>Exportera kunddata

Samma REST-API:er, portalupplevelse och SDK:er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade lagringskonton gör det också möjligt att visa och exportera dessa objekt.

Azure Key Vault åtkomstloggning är en valfri funktion som kan aktiveras för att generera loggar för varje REST API-anrop. Dessa loggar överförs till ett lagringskonto i din prenumeration där du tillämpar bevarandeprincipen som uppfyller organisationens krav.

Diagnostikloggar för Azure Key Vault som innehåller personuppgifter kan hämtas genom att göra en exportbegäran i användarsekretessportalen. Den här begäran måste göras av klientadministratören.

## <a name="next-steps"></a>Nästa steg

- [Loggning av Azure Key Vault](key-vault-logging.md)

- [Översikt av mjuk borttagning för Azure Key Vault](key-vault-soft-delete-cli.md)

- [Azure Key Vault-nyckelåtgärder](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Hemliga åtgärder för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Certifikat och principer för Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault-lagringskontoåtgärder](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
