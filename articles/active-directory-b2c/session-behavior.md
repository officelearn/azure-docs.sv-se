---
title: Konfigurera sessionsbeteende - Azure Active Directory B2C | Microsoft-dokument
description: Konfigurera sessionsbeteende i Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186819"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurera sessionsbeteende i Azure Active Directory B2C

Den här funktionen ger dig finkornig kontroll, [per användare flöde,](user-flow-overview.md)av:

- Livstider för webbprogramsessioner som hanteras av Azure AD B2C.
- SSO-beteende (Single sign-on) över flera appar och användarflöden i din Azure AD B2C-klientorganisation.

Dessa inställningar är inte tillgängliga för användarflöden för återställning av lösenord.

Azure AD B2C stöder [OpenID Connect-autentiseringsprotokollet](openid-connect.md) för att aktivera säker inloggning till webbprogram. Du kan använda följande egenskaper för att hantera webbprogramsessioner:

## <a name="session-behavior-properties"></a>Egenskaper för sessionsbeteende

- **Livstid för webbappsession (minuter)** – Livslängden för Azure AD B2C:s sessionscookie som lagras i användarens webbläsare vid lyckad autentisering.
    - Standard = 1440 minuter.
    - Minimum (inklusive) = 15 minuter.
    - Max (inklusive) = 1440 minuter.
- **Tidsgränsen för webbappsession** – Om den här växeln är inställd på **Absolut**tvingas användaren autentisera igen efter den tidsperiod som anges av **livstiden för webbappsessionen (minuter).** Om den här växeln är inställd på **Rullande** (standardinställningen) förblir användaren inloggad så länge användaren är kontinuerligt aktiv i webbprogrammet.
- **Konfiguration för enkel inloggning** Om du har flera program och användarflöden i din B2C-klient kan du hantera användarinteraktioner mellan dem med egenskapen **Single sign-on configuration.** Du kan ställa in egenskapen på någon av följande inställningar:
    - **Klient** - Den här inställningen är standard. Med den här inställningen kan flera program och användarflöden i din B2C-klient dela samma användarsession. När en användare till exempel loggar in på ett program kan användaren också smidigt logga in på ett annat, Contoso Pharmacy, när han eller hon har tillgång till det.
    - **Program** - Med den här inställningen kan du underhålla en användarsession exklusivt för ett program, oberoende av andra program. Om du till exempel vill att användaren ska logga in på Contoso Pharmacy (med samma autentiseringsuppgifter), även om användaren redan är inloggad på Contoso Shopping, ett annat program på samma B2C-klientorganisation.
    - **Princip** - Med den här inställningen kan du underhålla en användarsession exklusivt för ett användarflöde, oberoende av de program som använder den. Om användaren till exempel redan har loggat in och slutfört ett MFA-steg (Multi Factor Authentication), kan användaren få åtkomst till delar av flera program med högre säkerhet så länge sessionen som är kopplad till användarflödet inte upphör att gälla.
    - **Inaktiverad** - Den här inställningen tvingar användaren att köra igenom hela användarflödet för varje körning av principen.

Följande användningsfall är aktiverade med hjälp av dessa egenskaper:

- Uppfylla branschens säkerhets- och efterlevnadskrav genom att ange lämpliga livstider för webbprogramsessioner.
- Tvinga autentisering efter en viss tidsperiod under en användares interaktion med en högsäkerhetsdel av webbprogrammet.

## <a name="configure-the-properties"></a>Konfigurera egenskaperna

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att välja **katalog + prenumerationsfilter** i den övre menyn och välja den katalog som innehåller din Azure AD B2C-klient.
3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**.
4. Välj **Användarflöden (principer)**.
5. Öppna användarflödet som du tidigare skapade.
6. Välj **egenskaper**.
7. Konfigurera **livstid för webbappsession (minuter),** **timeout för webbappsession,** **enkel inloggningskonfiguration**och **Kräv ID-token i utloggningsbegäranden** efter behov.

    ![Egenskapsinställningar för sessionsbeteende i Azure-portalen](./media/session-behavior/session-behavior.png)

8. Klicka på **Spara**.
