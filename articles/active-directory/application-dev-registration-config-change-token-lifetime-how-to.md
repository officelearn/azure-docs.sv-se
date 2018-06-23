---
title: Så här ändrar du livslängd för token som standard för en anpassad utvecklade program | Microsoft Docs
description: Så här uppdaterar du principer för livslängd för Token för programmet som du utvecklar på Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: bd963253ee796fb56405e8da855475df2d2f5d08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36335347"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Så här ändrar du standardvärdena livslängd för token för en anpassad utvecklade program

Azure AD Premium kan utvecklare av program och innehavaradministratörer för att konfigurera livslängd för token som utfärdats för icke-konfidentiella klienter. Livslängd för token policys ställs in på klienten hela bas eller resurser som används.

 * Om du vill ange en livslängd för token-princip måste du hämta den [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).

 * Kör den **Connect-AzureAD-Bekräfta** kommando.

 * Här är en exempelprincip som anger den maximala ålder en faktor uppdateringstoken. Skapa principen: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Checka ut den [konfigurera livslängd för token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokument för att lära dig hur du skapar andra anpassade.

## <a name="next-steps"></a>Nästa steg
[Konfigurera livslängd för Token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referens för Azure AD-Token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

