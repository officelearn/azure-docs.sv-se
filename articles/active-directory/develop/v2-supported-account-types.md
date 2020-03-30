---
title: Typer av konton som stöds – Microsoft identity platform | Azure
description: Konceptuell dokumentation om målgrupper och kontotyper som stöds i program
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: de90c81f56b6017b2d53ecbfb2c400a4c9f05d81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262287"
---
# <a name="supported-account-types"></a>Kontotyper som stöds

I den här artikeln beskrivs vilka kontotyper (ibland namngivna målgrupper) som stöds i program.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Konton som stöds i Microsoft Identity-plattformsprogram

I Microsoft Azure public Cloud kan de flesta typer av appar logga in användare med valfri målgrupp:

- Om du skriver ett LOB-program (Line of Business) kan du logga in användare i din egen organisation. Ett sådant program heter ibland **en enda klient .**
- Om du är en ISV kan du skriva ett program som loggar in användare:

  - I alla organisationer. Ett sådant program heter ett webbprogram **för flera innehavare.** Ibland läser du att den loggar in användare med deras arbets- eller skolkonton.
  - Med sitt arbete, skola eller personliga Microsoft-konto.
  - Med endast personligt Microsoft-konto.
    > [!NOTE]
    > För närvarande stöder Microsofts identitetsplattform endast personliga Microsoft-konton genom att registrera en app för **arbets- eller skolkonton eller Microsofts personliga konton**och `https://login.microsoftonline.com/consumers`sedan begränsa inloggningen i koden för programmet genom att ange en Azure AD-behörighet när du skapar programmet, till exempel .

- Om du skriver ett företag till konsumenter program, kan du också logga in användare med sina sociala identiteter, med Hjälp av Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Vissa autentiseringsflöden stöder inte alla kontotyper

Vissa kontotyper kan inte användas med vissa autentiseringsflöden. Till exempel i skrivbords-, UWP-program eller demonprogram:

- Daemon-program kan endast användas med Azure Active Directory-organisationer. Det är inte meningsfullt att försöka använda demonprogram för att manipulera Microsofts personliga konton (administratörssamtyckeet beviljas aldrig).  
- Du kan bara använda flödet Integrerad Windows-autentisering med arbets- eller skolkonton (i din organisation eller i någon organisation). Integrerad Windows-autentisering fungerar med domänkonton och kräver att datorerna är domänanslutna eller Azure AD-anslutna. Det här flödet är inte meningsfullt för personliga Microsoft-konton.
- Det går inte att använda [resursägarelösenordsbeslag](./v2-oauth-ropc.md) (användarnamn/lösenord) med personliga Microsoft-konton. Personliga Microsoft-konton kräver att användaren samtycker till att komma åt personliga resurser vid varje inloggningssession. Det är därför det här beteendet inte är kompatibelt med icke-interaktiva flöden.
- Enhetskodflödet fungerar ännu inte med personliga Microsoft-konton.

## <a name="supported-account-types-in-national-clouds"></a>Kontotyper som stöds i nationella moln

 Appar kan också logga in användare i [nationella moln](authentication-national-cloud.md). Microsofts personliga konton stöds dock inte i dessa moln (per definition av dessa moln). Det är därför som de kontotyper som stöds minskas, för dessa moln, till din organisation (en enda klient) eller organisationer (program med flera innehavare).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Hyresrätt i Azure Active Directory](./single-and-multi-tenant-apps.md)
- Läs mer om [National Clouds](./authentication-national-cloud.md)
