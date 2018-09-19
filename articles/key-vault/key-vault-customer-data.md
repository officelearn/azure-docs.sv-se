---
title: Azure Key Vault kunden datafunktioner | Microsoft Docs
description: Lär dig mer om kunddata i Key Vault
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 05/22/2018
ms.author: barclayn
ms.openlocfilehash: 807b8a17570dfdcefa07a42719b7b0b24e4f31d7
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297170"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault-funktioner som kundens data

Azure Key Vault får kundernas data under generering och uppdatering av valv, nycklar, hemligheter, certifikat och hanterade storage-konton. Den här kunddata syns direkt i Azure-portalen och via REST API. Kundinformation kan redigeras eller tas bort av uppdatering eller borttagning av objektet som innehåller data.

Systemloggar åtkomst genereras när en användare eller ett program har åtkomst till Key Vault. Detaljerad åtkomstloggar är tillgängliga för kunder som använder Azure Insights.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kunddata

Följande information visas kundinformation i Azure Key Vault:

- Åtkomstprinciper för Azure Key Vault innehåller objekt-ID: N som representerar användare, grupper eller program
- Certifikatämnen kan innehålla e-postadresser eller andra användare eller organisations-ID: n
- Certifikatskontakterna kan innehålla användarnas e-postadresser, namn eller telefonnummer
- Certifikatutfärdare kan innehålla e-postadresser, namn, telefonnummer, autentiseringsuppgifter och organisatorisk information
- Godtycklig taggar kan användas på objekt i Azure Key Vault. Dessa objekt innefattar valv, nycklar, hemligheter, certifikat och storage-konton. De taggar som används kan innehålla personuppgifter
- Azure Key Vault-åtkomstloggar innehåller objekt-ID: N, [UPN-namnen](../active-directory/hybrid/plan-connect-userprincipalname.md), och IP-adresser för varje REST API-anrop
- Diagnostikloggar för Azure Key Vault kan innehålla objekt-ID: N och IP-adresser för REST API-anrop

## <a name="deleting-customer-data"></a>Tar bort kunddata

Samma REST API: er, hanteringsportal och SDK: er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade storage-konton kan också uppdatera och ta bort de här objekten.

Mjuk borttagning kan du återställa borttagna data i 90 dagar efter borttagningen. När du använder mjuk borttagning, kan data tas bort permanent innan de 90 dagar kvarhållningsperiod upphör att gälla genom att utföra en åtgärd för rensning. Om valvet eller prenumeration har konfigurerats att blockera Rensa åtgärder, det går inte att ta bort data permanent tills schemalagda kvarhållningsperioden har passerat.

## <a name="exporting-customer-data"></a>Exportera kunddata

Samma REST API: er, portalen och SDK: er som används för att skapa valv, nycklar, hemligheter, certifikat och hanteras storage-konton också kan du visa och exportera de här objekten.

Azure Key Vault åtkomst loggning är en valfri funktion som kan aktiveras på att generera loggar för varje REST API-anrop. Dessa loggar överförs till ett lagringskonto i din prenumeration där du kan använda bevarandeprincip som uppfyller organisationens krav.

Azure Key Vault diagnostikloggar som innehåller personliga data kan hämtas genom att göra en exportbegäran i portalen användarnas integritet. Den här begäran måste göras av klientadministratören.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault-loggning](key-vault-logging.md)

- [Översikt över mjuk borttagning i Azure Key Vault](key-vault-soft-delete-cli.md)

- [Azure Key Vault-nyckelåtgärder](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Azure Key Vault-hemlighetsåtgärder](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault-certifikat och principer](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Azure Key Vault-konto lagringsåtgärder](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
