---
title: Lägga till inloggning med Microsoft till en ASP.NET-webbapp | Microsoft Docs
description: Lär dig hur du lägger till Microsoft-inloggning på en ASP.NET-lösning med ett traditionellt webbläsarbaserat program med OpenID Connect standard.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.topic: quickstart
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc68db841b900ca96899e6fda8f8f3e07f41256a
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921100"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Snabbstart: Lägga till inloggning med Microsoft till en ASP.NET-webbapp

[Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.

Med [Microsoft Authentication Library (MSAL)](msal-overview.md) kan utvecklare Hämta tokens från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0).

## <a name="next-steps"></a>Nästa steg

För nya webb program rekommenderar vi att du använder Microsoft Identity Platform (v 2.0) och MSAL för att hämta tokens och åtkomst till säkra webb-API: er. Se [snabb start: Lägg till inloggning med Microsoft i en ASP.NET-webbapp](quickstart-v2-aspnet-webapp.md) för att komma igång.
