---
title: Cookie definitions - Azure Active Directory B2C | Microsoft Docs
description: Innehåller definitioner för de cookies som används i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 7864320b71416d1b06661b8ae96c6113962250dd
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64703981"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Cookies definitioner för Azure Active Directory B2C

I följande tabell visas de cookies som används i Azure Active Directory B2C.

| Namn | Domain | Utgångsdatum | Syfte |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Innehåller medlemskapsdata för användare över klienter. Innehavarna som en användare är medlem av och nivån av medlemskap (administratör eller användare). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Används för att dirigera begäranden till lämpliga produktionsinstansen. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Används för att spåra transaktioner (antal begäranden om autentisering till Azure AD B2C) och den aktuella transaktionen. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Används för att underhålla SSO-session. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md), lyckad autentisering | Används för att underhålla tillstånd för begäran. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Begär förfalskning token som används för CRSF skydd. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Används för Azure AD B2C nätverksroutning. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Kontext |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Används för att lagra medlemskapsdata för resource provider-klient. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, branded domain | Avsluta placering av [webbläsarsession](active-directory-b2c-token-session-sso.md) | Används för att lagra relay-cookie. |

