---
title: Dölja ett program från användarens upplevelse i Azure AD
description: Så här döljer du ett program från användarens upplevelse i Azure Active Directory-åtkomstpaneler eller Office 365-startprogram.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5718adf4fd76e2fbd0ff793dd2fa33ee08f7c0fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295055"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Dölja program från slutanvändare i Azure Active Directory

Instruktioner för hur du döljer program från slutanvändarnas MyApps-panel eller Office 365-startprogrammet. När ett program är dolt har användarna fortfarande behörighet till programmet. 

## <a name="prerequisites"></a>Krav

Programadministratörsbehörighet krävs för att dölja ett program från panelen MyApps och Office 365-startprogrammet.

Globala administratörsbehörighet krävs för att dölja alla Office 365-program.


## <a name="hide-an-application-from-the-end-user"></a>Dölja ett program från slutanvändaren
Använd följande steg för att dölja ett program från panelen MyApps och Programstartprogrammet för Office 365.

1.  Logga in på [Azure-portalen](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **Företagsprogram**. **Bladet Enterprise - Alla program** öppnas.
4.  Under **Programtyp**väljer du **Företagsprogram**om de inte redan är markerade.
5.  Sök efter det program som du vill dölja och klicka på programmet.  Programmets översikt öppnas.
6.  Klicka på **Egenskaper**. 
7.  Klicka på **Nej**för frågan **Synlig för användarna?**
8.  Klicka på **Spara**.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Använda Azure AD PowerShell för att dölja ett program

Om du vill dölja ett program från panelen MyApps kan du manuellt lägga till HideApp-taggen i tjänstens huvudnamn för programmet. Kör du följande [AzureAD PowerShell-kommandon](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) för att ange programmets **egenskap Synlig för användare?** **No** 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Dölja Office 365-program på panelen Mina Program

Använd följande steg för att dölja alla Office 365-program på panelen Mina program. Programmen visas fortfarande i Office 365-portalen.

1.  Logga in på [Azure-portalen](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **Användarinställningar**.
4.  Under **Företagsprogram**klickar du på **Hantera hur slutanvändare startar och visar sina program.**
5.  För **användare kan bara se Office 365-appar i Office 365-portalen**klickar du på **Ja**.
6.  Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användar- eller grupptilldelning från en företagsapp](remove-user-or-group-access-portal.md)
* [Ändra namn eller logotyp för en företagsapp](change-name-or-logo-portal.md)

