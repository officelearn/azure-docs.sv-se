---
title: Ändra livstiden för token för ett anpassat program som har utvecklats | Microsoft Docs
description: Så här uppdaterar du livs längd principer för ditt program som du utvecklar på Azure AD
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: ryanwi
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8dd69ff41c890c654fc41bb601b17d135ff0e984
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/18/2019
ms.locfileid: "68320930"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Ändra standardvärdena för token för token för ett anpassat, utvecklat program

Den här artikeln visar hur du använder Azure AD PowerShell för att ange en livs längd princip för token. Azure AD Premium tillåter utvecklare av appar och klient administratörer att konfigurera livs längden för token som utfärdas för icke-konfidentiella klienter. Livs längds principer för token anges per klient eller de resurser som nås.

1. Om du vill ange en livstids princip för token måste du ladda ned [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot **Connect-AzureAD-Confirm** .

    Här är en exempel princip som anger den högsta ålders uppdaterings-token. Skapa principen:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Nästa steg

* Läs om hur du konfigurerar livs längder för token som utfärdats av Azure AD, inklusive hur du ställer in livs längd för token för alla appar i din organisation, för en app med flera klienter, eller för ett särskilt tjänst huvud namn i din organisation i [Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) . organisation. 
* [Referens för Azure AD-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

