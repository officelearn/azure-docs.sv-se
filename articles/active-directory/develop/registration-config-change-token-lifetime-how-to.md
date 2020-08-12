---
title: Ändra livstid för token standard för anpassade Azure AD-appar
description: Så här uppdaterar du livs längd principer för ditt program som du utvecklar på Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.openlocfilehash: 8de6a7aafdd402e4ee75862e69ac60af3af0e041
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88114939"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Ändra standardvärdena för token för token för ett anpassat, utvecklat program

Den här artikeln visar hur du använder Azure AD PowerShell för att ange en livs längd princip för token. Azure AD Premium tillåter utvecklare av appar och klient administratörer att konfigurera livs längden för token som utfärdas för icke-konfidentiella klienter. Livs längds principer för token anges per klient eller de resurser som nås.

1. Om du vill ange en livstids princip för token måste du ladda ned [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot **Connect-AzureAD-Confirm** .

    Här är en exempel princip som anger den högsta ålders uppdaterings-token. Skapa principen:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar livs längder för token som utfärdats av Azure AD, inklusive hur du ställer in livs längd för token för alla appar i din organisation, för en app med flera klienter, eller för ett särskilt tjänst huvud namn i din organisation, i [Azure AD](./active-directory-configurable-token-lifetimes.md) . 
* [Referens för Azure AD-token](./id-tokens.md)