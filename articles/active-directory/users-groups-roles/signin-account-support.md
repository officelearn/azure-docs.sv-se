---
title: Godkänner inloggnings sidan för Azure AD Microsoft-konton | Microsoft Docs
description: Hur meddelanden på skärmen visar användar namns sökning under inloggning
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: overview
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81ba7f77bb3396f49c3cf41d2266567aa48a8257
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84731366"
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