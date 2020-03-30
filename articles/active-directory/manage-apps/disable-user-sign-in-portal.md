---
title: Inaktivera användarloggningar för en företagsapp i Azure AD
description: Inaktivera ett företagsprogram så att inga användare kan logga in på det i Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10553898376c4b9236ee62718fffccd45b12d70b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274097"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användarloggningar för en företagsapp i Azure Active Directory

Det är enkelt att inaktivera ett företagsprogram så att inga användare kan logga in på det i Azure Active Directory (Azure AD). Du behöver rätt behörighet för att hantera företagsappen. Och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur inaktiverar jag användarloggningar?

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
1. Välj **Alla tjänster,** ange **Azure Active Directory** i textrutan och välj sedan **Retur**.
1. I fönstret Azure Active -  ***Directory-katalognamn*** (det vill säga Azure AD-fönstret för den katalog som du hanterar) väljer du **Enterprise-program**. **Azure Active Directory**
1. I fönstret **Företagsprogram – Alla program** visas en lista över de appar du kan hantera. Välj en app.
1. I ***fönstret appnamn*** (det vill än fönstret med namnet på den markerade appen i rubriken) väljer du **Egenskaper**.
1. I fönstret**Egenskaper** för ***appnamn*** - väljer du **Nej** för aktiverat för användare att **logga in?**
1. Markera kommandot **Spara.**

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Använda Azure AD PowerShell för att inaktivera en olistad app

Om du känner till AppId för en app som inte visas i listan företagsappar (till exempel på grund av att du har tagit bort appen eller tjänstens huvudnamn ännu inte har skapats på grund av att appen är förauktoriserat av Microsoft), kan du manuellt skapa tjänstens huvudnamn för appen och sedan inaktivera den med hjälp av [AzureAD PowerShell cmdlet](https://docs.microsoft.com/powershell/module/azuread/New-AzureADServicePrincipal?view=azureadps-2.0).

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>Nästa steg

* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användar- eller grupptilldelning från en företagsapp](remove-user-or-group-access-portal.md)
* [Ändra namn eller logotyp för en företagsapp](change-name-or-logo-portal.md)
