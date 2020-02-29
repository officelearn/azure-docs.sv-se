---
title: Konfigurera sessionens beteende – Azure Active Directory B2C | Microsoft Docs
description: Konfigurera sessionens beteende i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c1d39fdbca9484f47ce0c8537c82247b75b2e3db
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78186819"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurera sessionens beteende i Azure Active Directory B2C

Den här funktionen ger dig detaljerad kontroll över [varje användares flöde](user-flow-overview.md)av:

- Livs längd för webbappens sessioner som hanteras av Azure AD B2C.
- Beteende för enkel inloggning (SSO) i flera appar och användar flöden i din Azure AD B2C klient.

De här inställningarna är inte tillgängliga för användar flöden för lösen ords återställning.

Azure AD B2C stöder [OpenID Connect-autentiseringsprotokollet](openid-connect.md) för att aktivera säker inloggning till webb program. Du kan använda följande egenskaper för att hantera WebApplication-sessioner:

## <a name="session-behavior-properties"></a>Egenskaper för sessionens beteende

- **Web App session livs längd (minuter)** – livs längden för Azure AD B2C's-sessionens cookie som lagras i användarens webbläsare vid lyckad autentisering.
    - Standard = 1440 minuter.
    - Minst (inklusive) = 15 minuter.
    - Max (inklusive) = 1440 minuter.
- **Timeout för webbapp** – om den här växeln är inställd på **absolut**tvingas användaren att autentisera igen när den tids period som anges av **Web App-sessionens livs längd (minuter)** förflutit. Om den här växeln är inställd på **rullande** (standardinställning) förblir användaren inloggad så länge användaren alltid är aktiv i ditt webb program.
- **Konfiguration av enkel inloggning** Om du har flera program och användar flöden i B2C-klienten kan du hantera användar interaktioner mellan dem med hjälp av **konfigurations egenskapen för enkel inloggning** . Du kan ställa in egenskapen på någon av följande inställningar:
    - **Klient** – den här inställningen är standard. Med den här inställningen kan flera program och användare flöda i B2C-klienten för att dela med sig av samma användarsession. När en användare till exempel loggar in på ett program, kan användaren också sömlöst logga in på en annan, contoso apotek, vid åtkomst till den.
    - **Program** – med den här inställningen kan du underhålla en användarsession exklusivt för ett program, oberoende av andra program. Om du till exempel vill att användaren ska logga in på Contoso apotek (med samma autentiseringsuppgifter), även om användaren redan har loggat in på Contoso shopping, ett annat program på samma B2C-klient.
    - **Princip** – med den här inställningen kan du underhålla en användarsession exklusivt för ett användar flöde, oberoende av de program som använder den. Om användaren till exempel redan har loggat in och slutfört ett Multi Factor Authentication-steg (MFA), kan användaren få åtkomst till högre säkerhets delar av flera program så länge sessionen som är kopplad till användar flödet inte upphör att gälla.
    - **Disabled** – inställningen tvingar användaren att köra genom hela användar flödet vid varje körning av principen.

Följande användnings fall är aktiverade med följande egenskaper:

- Uppfylla din branschs krav på säkerhet och efterlevnad genom att ange lämpliga livs längder för webbappens sessioner.
- Tvinga autentisering efter en angiven tids period under en användares interaktion med en hög säkerhets del av ditt webb program.

## <a name="configure-the-properties"></a>Konfigurera egenskaperna

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrol lera att du använder den katalog som innehåller din Azure AD B2C klient genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din Azure AD B2C-klient.
3. Välj **Alla tjänster** på menyn uppe till vänster i Azure Portal. Sök sedan efter och välj **Azure AD B2C**.
4. Välj **användar flöden (principer)** .
5. Öppna det användar flöde som du skapade tidigare.
6. Välj **Egenskaper**.
7. Konfigurera **livs längd för webbappens session (minuter)** , **tids gräns för webbapp**, **konfiguration av enkel inloggning**och **Kräv ID-token i utloggnings begär Anden** efter behov.

    ![Inställningar för sessionens beteende egenskap i Azure Portal](./media/session-behavior/session-behavior.png)

8. Klicka på **Save** (Spara).
