---
title: Bygg en .NET-webb-API med Azure AD för autentisering och auktorisering | Microsoft Docs
description: Hur du skapar ett .NET MVC-webb-API som integreras med Azure AD för autentisering och auktorisering.
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 67e74774-1748-43ea-8130-55275a18320f
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27af5d6782b0fe45df1a82e320c593b9a0f332cd
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921066"
---
# <a name="quickstart-build-a-net-web-api-that-integrates-with-azure-ad-for-authentication-and-authorization"></a>Snabbstart: Skapa ett .NET-webb-API som integreras med Azure AD för autentisering och auktorisering

[Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.

Med [Microsoft Authentication Library (MSAL)](msal-overview.md) kan utvecklare Hämta tokens från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0).

För nya webb-API: er rekommenderar vi att du använder Microsoft Identity Platform (v 2.0) och MSAL för att hämta tokens och åtkomst till säkra webb-API: er: [snabb start: lägga till inloggning med Microsoft i en ASP.NET-webbapp](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2#calling-an-aspnet-core-web-api-from-a-wpf-application-using-azure-ad-v2)
