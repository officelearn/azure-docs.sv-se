---
title: Baslinjeprincip kräver MFA för tjänsthantering (förhandsversion) – Azure Active Directory
description: Princip för villkorlig åtkomst att kräva MFA för Azure Resource Manager
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
ms.openlocfilehash: 203b752f9da67ebf60e373fe7ce0893b4fd7fcb5
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67560968"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Baslinjeprincip: Kräva MFA för tjänsthantering (förhandsversion)

Du kan använda en mängd olika Azure-tjänster i din organisation. De här tjänsterna kan hanteras via Azure Resource Manager API:

* Azure Portal
* Azure PowerShell
* Azure CLI

Med Azure Resource Manager för att hantera dina tjänster är en mycket Privilegierade åtgärd. Azure Resource Manager kan ändra klienttäckande konfigurationer, till exempel prenumerationsärenden och inställningar. En faktorautentisering är sårbar för attacker som nätfiske och lösenord besprutningsmedel olika. Det är därför viktigt att verifiera identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer, genom att kräva multifaktorautentisering innan åtkomst ges.

**Kräva MFA för tjänsthantering** är en [baslinjeprincip](concept-baseline-protection.md) som kräver MFA för alla användare åtkomst till Azure portal, Azure PowerShell eller Azure CLI. Den här principen gäller för alla användare åtkomst till Azure Resource Manager, oavsett om de är en administratör.

När den här principen är aktiverad i en klient, kommer du måste alla användare som loggar in på Azure management-resurser med Multi-Factor authentication. Om användaren inte har registrerats för MFA, uppmanas användaren att registrera med Microsoft Authenticator-appen för att fortsätta.

För interaktiv inloggning med [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), använda den [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

```PowerShell
Connect-AzAccount
```

När du kör cmdleten visas en tokensträng. Kopiera den här strängen för att logga in och klistra in den i [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin)  i en webbläsare. PowerShell-sessionen autentiseras därefter för anslutning till Azure.

För interaktiv inloggning med [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), kör den [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) kommando.

```azurecli
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. I annat fall måste du öppna en Webbläsarsida och följ anvisningarna på kommandoraden för att ange en auktoriseringskod när du har lämnat till [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) i webbläsaren. Därefter kan logga in med autentiseringsuppgifterna för ditt konto i webbläsaren.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom den **kräver MFA för tjänsthantering** principen gäller för alla Azure Resource Manager-användare, flera saker som behöver göras för att säkerställa en smidig distribution. Dessa överväganden omfattar identifiera användare och tjänsten principer i Azure AD som inte kan eller inte utför MFA, samt program och klienter som används av din organisation och som inte stöder modern autentisering.

## <a name="enable-the-baseline-policy"></a>Aktivera baslinjeprincip för

Principen **baslinjeprincip: Kräva MFA för tjänsthantering (förhandsversion)** är förkonfigurerad och visas högst upp när du navigerar till bladet för villkorlig åtkomst i Azure-portalen.

Att aktivera den här principen och skydda dina administratörer:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj i listan med principer, **baslinjeprincip: Kräva MFA för tjänsthantering (förhandsversion)** .
1. Ange **aktiverar principen** till **Använd principen omedelbart**.
1. Klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Protection principer för villkorlig åtkomst baslinje](concept-baseline-protection.md)
* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorlig åtkomst i Azure Active Directory?](overview.md)