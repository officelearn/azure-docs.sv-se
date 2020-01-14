---
title: Cookie-definitioner – Azure Active Directory B2C | Microsoft Docs
description: Tillhandahåller definitioner för de cookies som används i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930899"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Cookies-definitioner för Azure Active Directory B2C

I följande tabell visas de cookies som används i Azure Active Directory B2C.

| Namn | Domain | Förfallotid | Syfte |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Avsluta [webbläsarsessionen](session-behavior.md) | Innehåller användar medlemskaps data mellan klienter. Klienterna som en användare är medlem av och medlemskaps nivån (administratör eller användare). |
| x-ms-cpim-slice | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att dirigera begär anden till lämplig produktions instans. |
| x-ms-cpim-trans | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att spåra transaktioner (antal autentiseringsbegäranden till Azure AD B2C) och den aktuella transaktionen. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att underhålla SSO-sessionen. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, anpassad domän | Slutet på [webbläsarsessionen](session-behavior.md), lyckad autentisering | Används för att underhålla status för begäran. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Förfalsknings-token för begäran mellan webbplatser som används för CRSF-skydd. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för Azure AD B2C nätverks routning. |
| x-ms-cpim-ctx | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Kontext |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att lagra medlemskaps data för resurs leverantörens klient organisation. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, anpassad domän | Avsluta [webbläsarsessionen](session-behavior.md) | Används för att lagra relä-cookien. |
