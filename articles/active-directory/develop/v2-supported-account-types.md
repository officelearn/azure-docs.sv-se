---
title: Stöds konton i appar (målgrupp) - Microsoft identity-plattformen
description: Konceptuell dokumentationen om målgrupper och stöds kontotyper i program
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d751e28859a3ae1104b28c76d0edfedb2a859eb4
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406514"
---
# <a name="supported-account-types"></a>Kontotyper som stöds

Den här artikeln beskriver vilka konton (kallas ibland målgrupper) stöds i program

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Typer av konton som stöds i Microsoft Identity-plattformen program

I det offentliga molnet för Microsoft Azure, kan de flesta typer av appar logga in användare med målgruppen:

- Om du skapar en rad Affärsappar (LOB)-program, kan du logga in användare i din organisation. Sådana program kallas ibland **enskild klient**.
- Du kan skriva ett program om du är en oberoende Programvaruleverantör, vilka användare som loggar in:

  - I en organisation. Sådana program kallas en **flera innehavare** webbprogram. Du kan ibland läsa att den användare loggar in med sina arbets- eller skolkonto konton.
  - Med sina arbets- eller skolkonto eller personligt Microsoft-konto.
  - Med endast personliga Microsoft-konto.
    > [!NOTE]
    > Microsoft identity-plattformen stöder för närvarande personliga Microsoft-konton endast genom att registrera en app för **arbets- eller Skol- eller personliga Microsoft-konton**, och sedan begränsa logga in i koden för programmet genom att ange en Azure AD-auktoritet, när du skapar programmet, till exempel `https://login.onmicrosoftonline.com/consumers`.

- Om du skriver ett företag till konsumenter program, kan du också logga in användare med deras sociala identiteter med Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Vissa autentiseringsflöden stöd inte för alla kontotyper

Vissa typer av konton kan inte användas med vissa autentiseringsflöden. Till exempel på skrivbordet, UWP program eller program som daemon:

- Daemon för program kan bara användas med Azure Active Directory-organisationer. Det meningsfullt inte att försöka använda daemon-program för att manipulera personliga Microsoft-konton (administratörens godkännande aldrig beviljas).  
- Du kan bara använda integrerad Windows-autentisering flödet med arbets-eller skolkonto (i din organisation eller en organisation). Verkligen, integrerad Windows-autentisering fungerar med domänkonton och kräver datorer vara domänansluten eller Azure AD-anslutna. Det här flödet passar inte för personliga Microsoft-Accounts.
- Den [Resource ägare lösenord bevilja](./v2-oauth-ropc.md) (användarnamn/lösenord) kan inte användas med personliga Microsoft-konton. Faktiskt kräver personliga Microsoft-konton att användaren godkänner åtkomst till personliga resurser på varje inloggningssessionen. Därför kan det här beteendet är inte kompatibelt med icke-interaktiv flöden.
- Kodflöde för enheten fungerar inte ännu med personliga Microsoft-konton.

## <a name="supported-account-types-in-national-clouds"></a>Stöds kontotyper i nationella moln

 Appar kan också logga in användare i [nationella moln](authentication-national-cloud.md). Dock stöds personliga Microsoft-konton inte i dessa moln (per definition av dessa moln). Därför stöds kontotyper reduceras för dessa-moln till din organisation (enskild klient) eller alla organisationer (program för flera innehavare).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [innehavare i Azure Active Directory](./single-and-multi-tenant-apps.md)
- Läs mer om [nationella moln](./authentication-national-cloud.md)