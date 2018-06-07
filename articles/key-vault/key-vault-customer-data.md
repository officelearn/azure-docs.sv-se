---
title: Azure Key Vault kunden datafunktioner | Microsoft Docs
description: Lär dig mer om customer data i Key Vault
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
ms.openlocfilehash: 1ddc74b1960095509a77d4b3072017847df42d90
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34637369"
---
# <a name="azure-key-vault-customer-data-features"></a>Azure Key Vault kunden datafunktioner

Azure Key Vault tar emot kundinformation under skapande eller uppdatering av valv, nycklar, hemligheter, certifikat och hanterade storage-konton. Den här kundinformation syns direkt i Azure-portalen och via REST API. Kundinformation kan redigeras eller tas bort av uppdatering eller borttagning av objektet som innehåller data.

Åtkomst systemloggar genereras när en användare eller ett program har åtkomst till Nyckelvalvet. Detaljerad åtkomstloggar är tillgängliga för kunder som använder Azure insikter.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="identifying-customer-data"></a>Identifiera kundinformation

Följande information identifierar kundens data i Azure Key Vault:

- Åtkomstprinciper för Azure Key Vault innehålla objekt-ID: N som representerar användare, grupper eller program
- Certifikatämnen kan inkludera e-postadresser eller andra användare eller organisationens identifierare
- Certifikatet kontakter kan innehålla användare e-postadresser, namn eller telefonnummer
- Certifikatutfärdare kan innehålla e-postadresser, namn, telefonnummer, autentiseringsuppgifter och organisatoriska information
- Godtycklig taggar kan tillämpas på objekt i Azure Key Vault. Dessa objekt innefattar valv, nycklar, hemligheter, certifikat och storage-konton. De taggar som används kan innehålla personliga data
- Azure Key Vault-åtkomstloggar innehålla objekt-ID [UPN-namnen](../active-directory/connect/active-directory-aadconnect-userprincipalname.md), och IP-adresser för varje REST API-anrop
- Azure Key Vault diagnostiska loggar kan innehålla objekt-ID och IP-adresser för REST API-anrop

## <a name="deleting-customer-data"></a>Om du tar bort kundinformation

Samma REST API: er, Portal upplevelse och SDK: er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade storage-konton kan även uppdatera och ta bort dessa objekt.

Mjuk borttagning kan du återställa borttagna data under 90 dagar efter borttagningen. När du använder mjuk borttagning, kan data tas bort permanent innan 90 dagar loggperioden förfaller genom att utföra en åtgärd för rensning. Om valvet eller prenumeration har konfigurerats att blockera Rensa operations, det går inte att permanent ta bort data förrän schemalagda kvarhållningsperioden har passerat.

## <a name="exporting-customer-data"></a>Exportera kundinformation

Samma REST API: er, portal upplevelse och SDK: er som används för att skapa valv, nycklar, hemligheter, certifikat och hanterade storage-konton är också kan du visa och exportera dessa objekt.

Azure Key Vault åtkomst loggning är en valfri funktion som kan aktiveras för att generera loggarna för varje REST API-anrop. Dessa loggar kommer att överföras till ett lagringskonto i din prenumeration där du kan använda den bevarandeprincip som uppfyller organisationens krav.

Azure Key Vault-diagnostiska loggar som innehåller personuppgifter kan hämtas genom att göra en begäran för export i Användarsekretess-portalen. Den här begäran måste göras av klientorganisationsadministratören.

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault-loggning](key-vault-logging.md)

- [Översikt för mjuk borttagning av Azure Key Vault](key-vault-soft-delete-cli.md)

- [Valv](https://docs.microsoft.com/rest/api/keyvault/vaults)

- [Viktiga åtgärder i Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/key-operations)

- [Hemlig åtgärder i Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/secret-operations)

- [Azure Key Vault-certifikat och principer](https://docs.microsoft.com/rest/api/keyvault/certificates-and-policies)

- [Certifikatutfärdare](https://docs.microsoft.com/rest/api/keyvault/certificate-issuers)

- [Azure Key Vault konto lagringsåtgärder](https://docs.microsoft.com/rest/api/keyvault/storage-account-key-operations)
