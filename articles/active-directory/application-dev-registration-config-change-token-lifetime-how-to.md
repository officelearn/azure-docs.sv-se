---
title: Så här ändrar du livslängd för token som standard för ett egenutvecklat program | Microsoft Docs
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 0ecb1f55309901abd2c623d2c3d23ef717fb176b
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365110"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Så här ändrar du standardinställningarna för ett egenutvecklat program

Azure AD Premium kan apputvecklare och administratörer av klientorganisationer för att konfigurera livslängd för token som utfärdas för icke-konfidentiella klienter. Livslängd för uppdateringstoken policys ställs in på basis av klienten som helhet eller de resurser som används.

 * Om du vill ange en livslängd för token-princip, måste du hämta den [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).

 * Kör den **Connect-AzureAD-Bekräfta** kommando.

 * Här är ett exempel på princip som anger uppdateringstoken maxåldern en faktor. Skapa principen: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

 * Kolla in den [konfigurera livslängd för uppdateringstoken](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) dokumentet för att lära dig hur du skapar andra anpassade.

## <a name="next-steps"></a>Nästa steg
[Konfigurera livslängd för Token](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes)<br>

[Referens för Azure AD-Token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

