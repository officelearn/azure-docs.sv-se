---
title: Bas linje princip Kräv MFA för Service Management (för hands version) – Azure Active Directory
description: Princip för villkorlig åtkomst för att kräva MFA för Azure Resource Manager
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: aab2aa4415345747a0e87b90ef0a7ee770ef3465
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608133"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Bas linje princip: Kräv MFA för Service Management (för hands version)

Du kanske använder en mängd olika Azure-tjänster i din organisation. Dessa tjänster kan hanteras via Azure Resource Manager API:

* Azure Portal
* Azure PowerShell
* Azure CLI

Att använda Azure Resource Manager för att hantera dina tjänster är en mycket privilegie rad åtgärd. Azure Resource Manager kan ändra konfigurationer för hela klienten, till exempel tjänst inställningar och fakturering av prenumerationer. Autentisering med en enda faktor är utsatt för en rad olika attacker, t. ex. nätfiske och lösen ords spridning. Därför är det viktigt att verifiera identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer genom att kräva Multi-Factor Authentication innan åtkomst tillåts.

**KRÄV MFA för Service Management** är en [bas linje princip](concept-baseline-protection.md) som kräver MFA för alla användare som kommer åt Azure Portal, Azure PowerShell eller Azure CLI. Den här principen gäller för alla användare som har åtkomst till Azure Resource Manager, oavsett om de är en administratör.

När den här principen är aktive rad i en klient kommer alla användare som loggar in till Azure-hanterings resurser att bli medveten om Multi-Factor Authentication. Om användaren inte är registrerad för MFA måste användaren registrera sig med hjälp av Microsoft Authenticator-appen för att kunna fortsätta.

Använd cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) för att utföra interaktiv inloggning med [Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

```PowerShell
Connect-AzAccount
```

När du kör cmdleten visas en tokensträng. Logga in genom att kopiera den här strängen och klistra in [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)den  i en webbläsare. PowerShell-sessionen autentiseras därefter för anslutning till Azure.

Om du vill utföra interaktiv inloggning med [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)kör du kommandot [AZ login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) .

```azurecli
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webb sida och följa instruktionerna på kommando raden för att ange en auktoriseringskod efter att du har navigerat till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i webbläsaren. Logga sedan in med dina konto uppgifter i webbläsaren.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom principen **KRÄV MFA för hantering av tjänster** gäller för alla Azure Resource Manager användare måste flera saker göras för att säkerställa en smidig distribution. Dessa överväganden omfattar att identifiera användare och tjänst principer i Azure AD som inte kan eller inte bör utföra MFA, samt program och klienter som används av din organisation som inte stöder modern autentisering.

## <a name="enable-the-baseline-policy"></a>Aktivera bas linje principen

Princip **bas linje princip: Kräv MFA för Service Management (för hands** version) är förkonfigurerade och visas överst när du navigerar till bladet för villkorlig åtkomst i Azure Portal.

Så här aktiverar du den här principen och skyddar dina administratörer:

1. Logga in på **Azure Portal** som global administratör, säkerhets administratör eller administratör för villkorlig åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. I listan med principer väljer **du bas linje princip: Kräv MFA för Service Management (för hands**version).
1. Ange principen för att **aktivera principen** **direkt**.
1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Principer för bas linje skydd för villkorlig åtkomst](concept-baseline-protection.md)
* [Fem steg för att skydda din identitets infrastruktur](../../security/fundamentals/steps-secure-identity.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)