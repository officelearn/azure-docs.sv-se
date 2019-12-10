---
title: Konfiguration av session och enkel inloggning
titleSuffix: Azure AD B2C
description: Konfiguration av sessioner och enkel inloggning (SSO) i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 736dd1f0490c2c9c7c4f526df96dd5ace6a1f819
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74950077"
---
# <a name="session-and-single-sign-on-configuration-in-azure-active-directory-b2c"></a>Konfiguration av sessionen och enkel inloggning i Azure Active Directory B2C

Den här funktionen ger dig detaljerad kontroll över [varje användares flöde](active-directory-b2c-reference-policies.md)av:

- Livs längd för webbappens sessioner som hanteras av Azure AD B2C.
- Beteende för enkel inloggning (SSO) i flera appar och användar flöden i din Azure AD B2C klient.

## <a name="session-behavior"></a>Sessionens beteende

Azure AD B2C stöder [OpenID Connect-autentiseringsprotokollet](active-directory-b2c-reference-oidc.md) för att aktivera säker inloggning till webb program. Du kan använda följande egenskaper för att hantera WebApplication-sessioner:

- **Web App session livs längd (minuter)** – livs längden för Azure AD B2C's-sessionens cookie som lagras i användarens webbläsare vid lyckad autentisering.
    - Standard = 1440 minuter.
    - Minst (inklusive) = 15 minuter.
    - Max (inklusive) = 1440 minuter.
- **Timeout för webbapp** – om den här växeln är inställd på **absolut**tvingas användaren att autentisera igen när den tids period som anges av **Web App-sessionens livs längd (minuter)** förflutit. Om den här växeln är inställd på **rullande** (standardinställning) förblir användaren inloggad så länge användaren alltid är aktiv i ditt webb program.

Följande användnings fall är aktiverade med följande egenskaper:

- Uppfylla din branschs krav på säkerhet och efterlevnad genom att ange lämpliga livs längder för webbappens sessioner.
- Tvinga autentisering efter en angiven tids period under en användares interaktion med en hög säkerhets del av ditt webb program.

De här inställningarna är inte tillgängliga för användar flöden för lösen ords återställning.

## <a name="single-sign-on-sso-configuration"></a>Konfiguration av enkel inloggning (SSO)

Om du har flera program och användar flöden i B2C-klienten kan du hantera användar interaktioner mellan dem med hjälp av **konfigurations egenskapen för enkel inloggning** . Du kan ställa in egenskapen på någon av följande inställningar:

- **Klient** – den här inställningen är standard. Med den här inställningen kan flera program och användare flöda i B2C-klienten för att dela med sig av samma användarsession. När en användare till exempel loggar in på ett program, kan användaren också sömlöst logga in på en annan, contoso apotek, vid åtkomst till den.
- **Program** – med den här inställningen kan du underhålla en användarsession exklusivt för ett program, oberoende av andra program. Om du till exempel vill att användaren ska logga in på Contoso apotek (med samma autentiseringsuppgifter), även om användaren redan har loggat in på Contoso shopping, ett annat program på samma B2C-klient.
- **Princip** – med den här inställningen kan du underhålla en användarsession exklusivt för ett användar flöde, oberoende av de program som använder den. Om användaren till exempel redan har loggat in och slutfört ett Multi Factor Authentication-steg (MFA), kan användaren få åtkomst till högre säkerhets delar av flera program så länge sessionen som är kopplad till användar flödet inte upphör att gälla.
- **Disabled** – inställningen tvingar användaren att köra genom hela användar flödet vid varje körning av principen.

De här inställningarna är inte tillgängliga för användar flöden för lösen ords återställning.

