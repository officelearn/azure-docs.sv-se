---
title: Accepterar min inloggningssida i Azure AD Microsoft-konton | Microsoft-dokument
description: Så här visar meddelanden på skärmen uppslag på användarnamn under inloggningen
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221ab7c50a84650f1b2adf3fdb2b284365795f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024291"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Inloggningsalternativ för Microsoft-konton i Azure Active Directory

Inloggningssidan för Microsoft 365 för Azure Active Directory (Azure AD) stöder arbets- eller skolkonton och Microsoft-konton, men beroende på användarens situation kan det vara det ena eller det andra eller båda. Azure AD-inloggningssidan stöder till exempel:

* Appar som accepterar inloggningar från båda typerna av konton
* Organisationer som tar emot gäster

## <a name="identification"></a>Identification
Du kan se om inloggningssidan som din organisation använder stöder Microsoft-konton genom att titta på tipstexten i användarnamnsfältet. Om tipstexten säger "E-post, telefon eller Skype" stöder inloggningssidan Microsoft-konton.

![Skillnad mellan konto inloggningssidor](./media/signin-account-support/ui-prompt.png)

[Ytterligare inloggningsalternativ fungerar bara för personliga Microsoft-konton](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) men kan inte användas för att logga in på arbets- eller skolkontoresurser.

## <a name="next-steps"></a>Nästa steg

[Anpassa ditt inloggningsmärke](../fundamentals/add-custom-domain.md)