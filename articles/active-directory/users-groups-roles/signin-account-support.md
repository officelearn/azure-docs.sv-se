---
title: Godkänner inloggnings sidan för Azure AD Microsoft-konton | Microsoft Docs
description: Hur meddelanden på skärmen visar användar namns sökning under inloggning
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "74024291"
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