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
ms.openlocfilehash: 00ed40bef3f3cbe59825f546ffa39c3ebfb2e41f
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003183"
---
# <a name="baseline-policy-require-mfa-for-service-management-preview"></a>Baslinjeprincip: Kräva MFA för tjänsthantering (förhandsversion)

Du kan använda en mängd olika Azure-tjänster i din organisation. De här tjänsterna kan hanteras via Azure Resource Manager API:

* Azure Portal
* Azure PowerShell
* Azure CLI

Med Azure Resource Manager för att hantera dina tjänster är en mycket Privilegierade åtgärd. Azure Resource Manager kan ändra klienttäckande konfigurationer, till exempel prenumerationsärenden och inställningar. En faktorautentisering är sårbar för attacker som nätfiske och lösenord besprutningsmedel olika. Det är därför viktigt att verifiera identiteten för användare som vill komma åt Azure Resource Manager och uppdatera konfigurationer, genom att kräva multifaktorautentisering innan åtkomst ges.

**Kräva MFA för tjänsthantering** är en [baslinjeprincip](concept-baseline-protection.md) som kräver MFA för alla användare åtkomst till Azure portal, Azure PowerShell eller Azure CLI. Den här principen gäller för alla användare åtkomst till Azure Resource Manager, oavsett om de är en administratör.

När den här principen är aktiverad i en klient, kommer du måste alla användare som loggar in på Azure management-resurser med Multi-Factor authentication. Om användaren inte har registrerats för MFA, uppmanas användaren att registrera med Microsoft Authenticator-appen för att fortsätta.

![Kräva MFA för Azure Resource Manager](./media/howto-baseline-protect-azure/baseline-policy-require-mfa-for-service-management.png)

För interaktiv inloggning med [Azure Powershell](https://docs.microsoft.com/powershell/azure/authenticate-azureps), använda den [Connect AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) cmdlet.

```PowerShell
Connect-AzAccount
```

När du kör cmdleten visas en tokensträng. Kopiera den här strängen för att logga in och klistra in den i [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin) i en webbläsare. PowerShell-sessionen autentiseras därefter för anslutning till Azure.

För interaktiv inloggning med [Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest), kör den [az-inloggning](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) kommando.

```azurecli
az login
```

Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. I annat fall måste du öppna en Webbläsarsida och följ anvisningarna på kommandoraden för att ange en auktoriseringskod när du har lämnat till [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) i webbläsaren. Därefter kan logga in med autentiseringsuppgifterna för ditt konto i webbläsaren.

## <a name="deployment-considerations"></a>Distributionsöverväganden

Eftersom den **kräver MFA för tjänsthantering** principen gäller för alla Azure Resource Manager-användare, flera saker som behöver göras för att säkerställa en smidig distribution. Dessa överväganden omfattar identifiera användare och tjänsten principer i Azure AD som inte kan eller inte utför MFA, samt program och klienter som används av din organisation och som inte stöder modern autentisering.

### <a name="user-exclusions"></a>Undantag för användaren

Den här baslinjeprincip ger dig möjlighet att exkludera användare. Innan du aktiverar principen för din klient, rekommenderar vi att du exkluderar följande konton:

* **För åtkomst vid akutfall** eller **glas** konton för att förhindra klienttäckande kontoutelåsning. I det osannolika scenariot alla administratörer har låsts ute från din klient, kan dina administrativa åtkomstkonto i nödfall användas för att logga in på klienten gör stegen tillgång.
   * Mer information finns i artikeln [hantera åtkomst vid akutfall i Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Tjänstkonton** och **tjänsten principer**, till exempel Azure AD Connect Sync-kontot. Tjänstkonton är icke-interaktiva konton som inte är knutna till en viss användare. De som vanligtvis används av backend-tjänster och Tillåt Programmeringsåtkomst till program. Tjänstkonton ska uteslutas eftersom inte går att slutföra MFA genom programmering.
   * Om din organisation har dessa konton som används i skript eller kod kan du överväga att ersätta dem med [hanterade identiteter](../managed-identities-azure-resources/overview.md). Som en tillfällig lösning kan undanta du dessa specifika konton från baslinje-principen.
* Användare som inte har eller inte kommer att kunna använda en Smartphone.
   * Den här principen kräver att användare registrerar sig för MFA med hjälp av Microsoft Authenticator-appen.

## <a name="enable-the-baseline-policy"></a>Aktivera baslinjeprincip för

Principen **baslinjeprincip: Kräva MFA för tjänsthantering (förhandsversion)** är förkonfigurerad och visas högst upp när du navigerar till bladet för villkorlig åtkomst i Azure-portalen.

Att aktivera den här principen och skydda dina administratörer:

1. Logga in på den **Azure-portalen** som global administratör, säkerhetsadministratör eller administratör för villkorsstyrd åtkomst.
1. Bläddra till **Azure Active Directory** > **villkorlig åtkomst**.
1. Välj i listan med principer, **baslinjeprincip: Kräva MFA för tjänsthantering (förhandsversion)**.
1. Ange **aktiverar principen** till **Använd principen omedelbart**.
1. Lägg till användare undantag genom att klicka på **användare** > **Välj exkluderade användare** och välja de användare som behöver som ska undantas. Klicka på **Välj** sedan **klar**.
1. Klicka på **spara**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i:

* [Principer för villkorlig åtkomst baslinje-skydd](concept-baseline-protection.md)
* [Fem steg för att skydda din infrastruktur för Identitetshantering](../../security/azure-ad-secure-steps.md)
* [Vad är villkorsstyrd åtkomst i Azure Active Directory?](overview.md)