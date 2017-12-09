---
title: "Så här ändrar du livslängd för token som standard för en anpassad utvecklade program | Microsoft Docs"
description: "Så här uppdaterar du principer för livslängd för Token för programmet som du utvecklar på Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: a28eacd820ed28a6470992ce86b060e886c00bcb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Så här ändrar du standardvärdena livslängd för token för en anpassad utvecklade program

Azure AD Premium kan utvecklare av program och innehavaradministratörer för att konfigurera livslängd för token som utfärdats för icke-konfidentiella klienter. Livslängd för token policys ställs in på klienten hela bas eller resurser som används.

 * Om du vill ange en livslängd för token-princip måste du hämta den [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).

 * Kör den **Connect-AzureAD-Bekräfta** kommando.

 * Här är en exempelprincip som anger den maximala ålder en faktor uppdateringstoken. Skapa principen:```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Checka ut den [konfigurera livslängd för token](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes) dokument för att lära dig hur du skapar andra anpassade.

## <a name="next-steps"></a>Nästa steg
[Konfigurera livslängd för Token](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referens för Azure AD-Token](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-token-and-claims)

