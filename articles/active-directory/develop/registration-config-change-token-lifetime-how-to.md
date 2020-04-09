---
title: Ändra standardvärden för tokenlivstid för anpassade Azure AD-appar
description: Så här uppdaterar du principer för tokenlivstid för ditt program som du utvecklar på Azure AD
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883361"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Så här ändrar du standardvärden för tokenlivstid för ett specialutvecklat program

Den här artikeln visar hur du använder Azure AD PowerShell för att ange en princip för tokenlivslängd. Azure AD Premium gör det möjligt för apputvecklare och klientadministratörer att konfigurera livslängden för token som utfärdats för icke-konfidentiella klienter. Principer för tokenlivstid anges på klientomfattande basis eller de resurser som används.

1. Om du vill ange en princip för tokenlivstid måste du hämta [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör kommandot **Connect-AzureAD -Confirm.**

    Här är en exempelprincip som anger uppdateringstoken för en enda faktor med högsta ålder. Skapa principen:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Nästa steg

* Se [Konfigurerbara tokenlivstider i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) om du vill lära dig hur du konfigurerar tokenlivslängder som utfärdats av Azure AD, inklusive hur du anger tokenlivstid för alla appar i organisationen, för en app med flera innehavare eller för ett specifikt tjänsthuvudnamn i organisationen. 
* [Referens för Azure AD-token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)
