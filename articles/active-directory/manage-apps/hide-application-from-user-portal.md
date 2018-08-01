---
title: Dölja ett program från användarupplevelsen i Azure Active Directory | Microsoft Docs
description: Hur du kan dölja ett program från användarupplevelsen i åtkomstpaneler för Azure Active Directory eller Office 365-startprogram.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 55f80396df4cbfe7d0a16a6a5066b68aadc0bdd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39369347"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Dölja ett program från användarupplevelsen i Azure Active Directory

Om du har ett program som du inte vill ska visas på användarnas åtkomstpaneler eller Office 365-startprogram finns alternativ för att dölja den här app-panelen.  Följande två alternativ är tillgängliga för att dölja program från användarens app startprogram.

- Dölja ett program från tredje part från användare åtkomstpaneler och startprogram för Office 365-app
- Dölj alla Office 365-program från åtkomstpaneler för användare

Genom att dölja app-användare fortfarande har behörighet att appen men visas inte de visas på deras app startprogram. Du måste ha behörighet att hantera appen enterprise och du måste vara en global administratör för katalogen.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Dölja ett program från användarens slutanvändarupplevelse
Du kan använda stegen nedan, beroende på din situation för att dölja program från åtkomstpanelen.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Hur jag för att dölja en tredjeparts-app från användarens åtkomstpanelen och startprogram för O365-app?
Använd följande steg för att dölja ett program från en användares åtkomstpanelen och startprogram för Office 365-app.

1.  Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2.  Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3.  På den **Azure Active Directory - *directoryname***  skärmen (det vill säga Azure AD skärmen för den katalog som du hanterar), Välj **företagsprogram**.
![Företagets appar](./media/hide-application-from-user-portal/app1.png)
4.  På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5.  På den **företagsprogram – alla program** , väljer en app.</br>
![Företagets appar](./media/hide-application-from-user-portal/app2.png)
6.  På den ***appname*** skärmen (det vill säga skärmen med namnet på den valda appen i rubriken), välj Egenskaper.
7.  På den  ***appname* -egenskaper** väljer **Ja** för **synlig för användarna?**.
![Företagets appar](./media/hide-application-from-user-portal/app3.png)
8.  Välj den **spara** kommando.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Hur jag för att dölja Office 365-program från användarens åtkomstpanelen?

Använd följande steg för att dölja alla Office 365-program från åtkomstpanelen. De här apparna kommer fortfarande att visas i Office 365-portalen.

1.  Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2.  Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3.  På den **Azure Active Directory - *directoryname***  skärmen (det vill säga Azure AD skärmen för den katalog som du hanterar), Välj **användarinställningar**.
4.  På den **användarinställningar** skärmen under **företagsprogram** Välj **Ja** för **användare kan bara se Office 365-appar i Office 365-portalen**.

![Företagsappar](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>Nästa steg
* [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Ändra namnet eller logotyp i en företagsapp](change-name-or-logo-portal.md)

