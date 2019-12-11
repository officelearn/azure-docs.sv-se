---
title: Dölj ett program från användarens upplevelse i Azure AD
description: Dölja ett program från användarens upplevelse i Azure Active Directory åtkomst paneler eller Office 365-lanseringar.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: be29f51771e24c67a8cd99a81e6a69be830dacb8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74970643"
---
# <a name="hide-applications-from-end-users-in-azure-active-directory"></a>Dölj program från slutanvändare i Azure Active Directory

Instruktioner för hur du döljer program från användarnas panel för Mina appar eller Office 365-Launcher. När ett program är dolt har användarna fortfarande behörighet till programmet. 

## <a name="prerequisites"></a>Krav

Program administratörs behörighet krävs för att dölja ett program från mina apps-panelen och Office 365-Launcher.

Det krävs globala administratörs behörigheter för att dölja alla Office 365-program.


## <a name="hide-an-application-from-the-end-user"></a>Dölj ett program från slutanvändaren
Använd följande steg för att dölja ett program från panelen för Mina appar och Office 365 Application start.

1.  Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **företags program**. Bladet **företags program – alla program** öppnas.
4.  Under **program typ**väljer du **företags program**, om det inte redan är markerat.
5.  Sök efter det program som du vill dölja och klicka på programmet.  Programmets översikt öppnas.
6.  Klicka på **Egenskaper**. 
7.  För frågan **synlig för användare?** klickar du på **Nej**.
8.  Klicka på **Save** (Spara).

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Använd Azure AD PowerShell för att dölja ett program

Om du vill dölja ett program från panelen Mina appar kan du manuellt lägga till HideApp-taggen till tjänstens huvud namn för programmet. Kör följande [AzureAD PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#service_principals) -kommandon för att ange programmets som **synlig för användarna?** -egenskapen till **Nej**. 

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags.Add("HideApp")
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-office-365-applications-from-the-myapps-panel"></a>Dölj Office 365-program från panelen för Mina appar

Använd följande steg för att dölja alla Office 365-program på panelen för Mina appar. Programmen syns fortfarande i Office 365-portalen.

1.  Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din katalog.
2.  Välj **Azure Active Directory**.
3.  Välj **Användarinställningar**.
4.  Under **företags program**klickar du på hantera hur slutanvändare **startar och visar sina program.**
5.  För **användare kan bara se office 365-appar i office 365-portalen**, klicka på **Ja**.
6.  Klicka på **Save** (Spara).

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företags app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp tilldelning från en företags app](remove-user-or-group-access-portal.md)
* [Ändra namn eller logo typ för en företags app](change-name-or-logo-portal.md)

