---
title: Komma igång med Azure AD Xamarin | Microsoft Docs
description: Skapa Xamarin-program som integreras med Azure AD för inloggning och anrop till Azure AD-skyddade API:er med hjälp av OAuth.
services: active-directory
documentationcenter: xamarin
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 198cd2c3-f7c8-4ec2-b59d-dfdea9fe7d95
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36e354894700ec92a444f288b3e09f5d7af88329
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68321043"
---
# <a name="quickstart-build-a-xamarin-app-that-integrates-microsoft-sign-in"></a>Snabbstart: Skapa en Xamarin-app som integrerar Microsoft-inloggning

[Microsoft Identity Platform](v2-overview.md) är en utveckling av Azure Active Directory (Azure AD) Developer Platform. Den hjälper utvecklare att bygga program som loggar in alla Microsoft-identiteter och hämtar tokens för att anropa Microsoft API:er som Microsoft Graph eller API:er som utvecklare har byggt.

Med [Microsoft Authentication Library (MSAL)](msal-overview.md) kan utvecklare Hämta tokens från slut punkten för Microsoft Identity Platform för att få åtkomst till säkra webb-API: er. Active Directory-autentiseringsbibliotek (ADAL) integreras med slut punkten för Azure AD för utvecklare (v 1.0), där MSAL integreras med slut punkten för Microsoft Identity Platform (v 2.0).

För nya Xamarin program, rekommenderar vi att du använder Microsoft Identity Platform (v 2.0) och MSAL för att hämta tokens och åtkomst till säkra webb-API: er: [Integrera Microsoft Identity och Microsoft Graph i en Xamarin Forms-app med MSAL](https://github.com/azure-samples/active-directory-xamarin-native-v2#integrate-microsoft-identity-and-the-microsoft-graph-into-a-xamarin-forms-app-using-msal) (inga valfria steg).

