---
title: Så här ändrar du livslängd för token som standard för ett egenutvecklat program | Microsoft Docs
description: Så här uppdaterar du principer för livslängd för Token för programmet som du utvecklar på Azure AD
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 4a730c340ea4d1e1137a7449c6d1005ea59917bf
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814016"
---
# <a name="how-to-change-the-token-lifetime-defaults-for-a-custom-developed-application"></a>Så här ändrar du standardinställningarna för ett egenutvecklat program

Azure AD Premium kan apputvecklare och administratörer av klientorganisationer för att konfigurera livslängd för token som utfärdas för icke-konfidentiella klienter. Livslängd för uppdateringstoken policys ställs in på basis av klienten som helhet eller de resurser som används.

1. Om du vill ange en livslängd för token-princip, måste du hämta den [Azure AD PowerShell-modulen](https://www.powershellgallery.com/packages/AzureADPreview).
1. Kör den **Connect-AzureAD-Bekräfta** kommando.

    Här är ett exempel på princip som anger uppdateringstoken maxåldern en faktor. Skapa principen: ```New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"```

## <a name="next-steps"></a>Nästa steg

* Se [konfigurerbara tokenlivslängder i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-configurable-token-lifetimes) och lär dig att konfigurera livslängd för token som utfärdas av Azure AD, inklusive hur du ställer in tokenlivslängder för alla appar i din organisation, för en app för flera klientorganisationer eller för en specifik tjänst huvudnamn i din organisation. 
* [Referens för Azure AD-Token](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

