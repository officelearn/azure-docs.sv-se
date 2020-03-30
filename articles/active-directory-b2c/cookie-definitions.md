---
title: Cookie-definitioner
titleSuffix: Azure AD B2C
description: Innehåller definitioner för cookies som används i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189522"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Definitioner av cookies för Azure AD B2C

Följande avsnitt innehåller information om cookies som används i Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>Samma plats

Microsoft Azure AD B2C-tjänsten är kompatibel med SameSite-webbläsarkonfigurationer, inklusive stöd för `SameSite=None` med `Secure` attributet.

För att skydda åtkomsten till webbplatser kommer webbläsare att införa en ny modell som är säker som standard som förutsätter att alla cookies skyddas från extern åtkomst om inte annat anges. Webbläsaren Chrome är den första som implementerar den här ändringen, med [början i Chrome 80 i februari 2020](https://www.chromium.org/updates/same-site). Mer information om hur du förbereder ändringen i Chrome finns i [Utvecklare: Gör dig redo för ny samesite=Ingen; Secure Cookie Inställningar](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) på krom blogg.

Utvecklare måste använda den `SameSite=None`nya cookie-inställningen för att utse cookies för åtkomst på flera webbplatser. När `SameSite=None` attributet finns måste `Secure` ytterligare ett attribut användas så att cookies på flera webbplatser endast kan nås via HTTPS-anslutningar. Validera och testa alla dina program, inklusive de program som använder Azure AD B2C.

Mer information finns i:

* [Hantera SameSite-cookieändringar i webbläsaren Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Effekt på kundens webbplatser och Microsofts tjänster och produkter i Chrome version 80 eller senare](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Cookies

I följande tabell visas de cookies som används i Azure AD B2C.

| Namn | Domain | Förfallodatum | Syfte |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Slutet på [webbläsarsessionen](session-behavior.md) | Innehåller data om användarmedlemskap över klienter. De klienter som en användare är medlem i och nivå av medlemskap (admin eller användare). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Används för att dirigera begäranden till lämplig produktionsinstans. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Används för att spåra transaktionerna (antal autentiseringsbegäranden till Azure AD B2C) och den aktuella transaktionen. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Används för att underhålla SSO-sessionen. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet av [webbläsarsessionen](session-behavior.md), lyckad autentisering | Används för att underhålla begäran tillstånd. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Förfalskningstoken för flera webbplatser som används för CRSF-skydd. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Används för Azure AD B2C-nätverksroutning. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Kontext |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Används för att lagra medlemsdata för resursleverantörens klientorganisation. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, märkesdomän | Slutet på [webbläsarsessionen](session-behavior.md) | Används för att lagra reläcookien. |
