---
title: Logga in användare och anropa Microsoft Graph API från en .NET Desktop (WPF)-app | Microsoft Docs
description: Lär dig hur du skapar ett .NET Windows Desktop-program som integreras med Azure AD för inloggning och anropar Azure AD-skyddade API:er med hjälp av OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ed33574f-6fa3-402c-b030-fae76fba84e1
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 946d376ef8af7d08b7924bd5b126c8b04e555668
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68324420"
---
# <a name="quickstart-sign-in-users-and-call-the-microsoft-graph-api-from-a-net-desktop-wpf-app"></a>Snabbstart: Logga in användare och anropa Microsoft Graph API från en .NET Desktop (WPF)-app

[Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.

Med [Microsoft Authentication Library (MSAL)](msal-overview.md) kan utvecklare Hämta tokens från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0).

För nya Skriv bords program rekommenderar vi att du använder Microsoft Identity Platform (v 2.0) och MSAL för att hämta tokens och åtkomst till säkra webb-API: er: [Snabbstart: Hämta en token och anropa Microsoft Graph API från en Windows Desktop-app](quickstart-v2-windows-desktop.md)
