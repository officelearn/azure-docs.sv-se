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
ms.openlocfilehash: fc71ebe61fba8c1bdb6b7625b16a50d8995a581a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "80883361"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Ändra standardvärdena för token för token för ett anpassat, utvecklat program

Den här artikeln visar hur du använder Azure AD PowerShell för att ange en livs längd princip för token. Azure AD Premium tillåter utvecklare av appar och klient administratörer att konfigurera livs längden för token som utfärdas för icke-konfidentiella klienter. Livs längds principer för token anges per klient eller de resurser som nås.

1. Om du vill ange en livstids princip för token måste du ladda ned [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot **Connect-AzureAD-Confirm** .

    Här är en exempel princip som anger den högsta ålders uppdaterings-token. Skapa principen:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du konfigurerar livs längder för token som utfärdats av Azure AD, inklusive hur du ställer in livs längd för token för alla appar i din organisation, för en app med flera klienter, eller för ett särskilt tjänst huvud namn i din organisation, i [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) . 
* [Referens för Azure AD-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
