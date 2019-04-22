---
title: Hur vet jag om en Azure AD-inloggningssida accepterar Microsoft-konton | Microsoft Docs
description: Hur anvisningarna på skärmen meddelanden visar användarnamn lookup under inloggning
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/10/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d26ff0f9259e3531259673f94fe477444cc786b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59491603"
---
# <a name="sign-in-options-for-microsoft-accounts-in-azure-active-directory"></a>Inloggningsalternativ för Microsoft-konton i Azure Active Directory

Microsoft 365 på inloggningssidan för Azure Active Directory (Azure AD) stöder arbets- eller skolkonton och Microsoft-konton, men beroende på användarens situation kan det vara en eller den andra eller båda. Till exempel stöder inloggningssidan för Azure AD:

* Appar som har stöd för inloggningar från båda typerna av konto
* Organisationer som accepterar gäster

## <a name="identification"></a>Identifiering
Berätta om din organisation använder på inloggningssidan stöder Microsoft-konton genom att titta på tipstexten i fältet för användarnamn. Om tipstexten texten ”e-post, telefon eller Skype” stöder Microsoft-konton på inloggningssidan.

![Skillnaden mellan inloggningssidorna för konto](./media/signin-account-support/ui-prompt.png)

[Ytterligare inloggningsalternativ fungerar endast för personliga Microsoft-konton](https://azure.microsoft.com/updates/microsoft-account-signin-options/ ) men kan inte användas för att logga in till arbetsplats eller skola resurser för kontot.

## <a name="next-steps"></a>Nästa steg

[Anpassa din inloggning profilering](../fundamentals/add-custom-domain.md)