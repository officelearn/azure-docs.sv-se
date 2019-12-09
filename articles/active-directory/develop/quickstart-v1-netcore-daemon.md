---
title: Hämta token och anropa Microsoft Graph API med konsol appens identitet (v 1.0) | Microsoft Docs
description: 'Lär dig hur du skapar ett .NET daemon-program som integreras med Azure AD-anrop till Azure AD Protected API: er med OAuth 2,0'
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 384aa90fd4edb8f52224bdf2885208fde5789398
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920896"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Snabb start: Hämta token och anropa Microsoft Graph API med konsol appens identitet (v 1.0) 

[Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.

Med [Microsoft Authentication Library (MSAL)](msal-overview.md) kan utvecklare Hämta tokens från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0).

## <a name="next-steps"></a>Nästa steg

För nya .NET daemon-program rekommenderar vi att du använder Microsoft Identity Platform (v 2.0) och MSAL för att hämta tokens och åtkomst till säkra webb-API: er: [snabb start: Hämta en token och anropa Microsoft Graph API från en webbapp med hjälp av appens identitet](quickstart-v2-netcore-daemon.md).
