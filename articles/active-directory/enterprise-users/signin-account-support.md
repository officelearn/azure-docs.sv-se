---
title: Godkänner inloggnings sidan för Azure AD Microsoft-konton | Microsoft Docs
description: Hur meddelanden på skärmen visar användar namns sökning under inloggning
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: overview
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2a3c2257311f208ae704a54f1369e10b2ff160b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94646856"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Inloggnings alternativ för Microsoft-konton i Azure Active Directory

Microsoft 365 inloggnings sidan för Azure Active Directory (Azure AD) stöder arbets-eller skol konton och Microsoft-konton, men beroende på användarens situation kan det vara en eller båda. Inloggnings sidan för Azure AD stöder till exempel följande:

* Appar som accepterar inloggningar från båda typerna av konto
* Organisationer som accepterar gäster

## <a name="identification"></a>Identification
Du kan se om inloggnings sidan som din organisation använder stöder Microsoft-konton genom att titta på tips texten i fältet användar namn. Om tips texten heter "e-post, telefon eller Skype", stöder inloggnings sidan Microsoft-konton.

![Skillnad mellan konto inloggnings sidor](./media/signin-account-support/ui-prompt.png)

[Ytterligare inloggnings alternativ fungerar bara för personliga Microsoft-konton](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) , men kan inte användas för att logga in på arbets-eller skol konto resurser.

## <a name="next-steps"></a>Nästa steg

[Anpassa din inloggnings anpassning](../fundamentals/add-custom-domain.md)