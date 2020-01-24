---
title: Typer av konton som stöds – Microsoft Identity Platform | Azure
description: Konceptuell dokumentation om mål grupper och konto typer som stöds i program
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76700064"
---
# <a name="supported-account-types"></a>Kontotyper som stöds

I den här artikeln förklaras vilka konto typer (ibland namngivna mål grupper) som stöds i program.

<!-- This section can be in an include for many of the scenarios (SPA, Web App signing-in users, protecting a Web API, Desktop (depending on the flows), Mobile -->

## <a name="supported-accounts-types-in-microsoft-identity-platform-applications"></a>Typer av konton som stöds i Microsoft Identity Platform-program

I det Microsoft Azure offentliga molnet kan de flesta typer av appar logga in användare med valfri mål grupp:

- Om du skriver ett LOB-program (Line of Business) kan du logga in användare i din egen organisation. Ett sådant program kallas ibland en **enda klient**.
- Om du är en ISV kan du skriva ett program som loggar in användare:

  - I vilken organisation som helst. Ett sådant program kallas för ett webb program för **flera innehavare** . Du kommer ibland att läsa att den loggar in användare med sina arbets-eller skol konton.
  - Med deras arbets-eller skol jobb eller personliga Microsoft-konto.
  - Endast med personliga Microsoft-konto.
    > [!NOTE]
    > För närvarande stöder Microsoft Identity Platform bara personliga Microsoft-konton genom att registrera en app för **arbets-eller skol-eller Microsoft personliga konton**, och sedan begränsa inloggningen i koden för programmet genom att ange en Azure AD-auktoritet när du skapar programmet, till exempel `https://login.microsoftonline.com/consumers`.

- Om du skriver ett företag till ett konsument program kan du även logga in användare med sina sociala identiteter med hjälp av Azure AD B2C.

## <a name="certain-authentication-flows-dont-support-all-the-account-types"></a>Vissa autentiserings flöden stöder inte alla konto typer

Vissa konto typer kan inte användas med vissa autentiseringsscheman. Till exempel, i Desktop, UWP-program eller daemon-program:

- Daemon-program kan bara användas med Azure Active Directory organisationer. Det är inte klokt att försöka använda daemon-program för att manipulera Microsoft-personliga konton (administratörs medgivande beviljas aldrig).  
- Du kan bara använda det integrerade Windows-autentiseringsschemat med arbets-eller skol konton (i din organisation eller i en organisation). Den integrerade Windows-autentiseringen fungerar med domän konton och kräver att datorerna är domänanslutna eller Azure AD-ansluten. Det här flödet passar inte för personliga Microsoft-konton.
- [Tilldelningen av lösen ord för resurs ägare](./v2-oauth-ropc.md) (username/Password) kan inte användas med personliga Microsoft-konton. Personliga Microsoft-konton kräver faktiskt att användaren godkänner åtkomst till personliga resurser vid varje inloggnings-session. Därför är det här beteendet inte kompatibelt med icke-interaktiva flöden.
- Enhets kod flödet fungerar ännu inte med personliga Microsoft-konton.

## <a name="supported-account-types-in-national-clouds"></a>Konto typer som stöds i nationella moln

 Appar kan också logga in användare i [nationella moln](authentication-national-cloud.md). Microsoft-personliga konton stöds dock inte i dessa moln (genom definition av dessa moln). Det är därför som de konto typer som stöds minskas, för dessa moln, till din organisation (en enskild klient) eller organisationer (program för flera innehavare).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [innehav i Azure Active Directory](./single-and-multi-tenant-apps.md)
- Läs mer om [nationella moln](./authentication-national-cloud.md)
