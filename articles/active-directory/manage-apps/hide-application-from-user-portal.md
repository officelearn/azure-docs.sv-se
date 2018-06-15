---
title: Dölj ett program från användarens upplevelse i Azure Active Directory | Microsoft Docs
description: Så här dölja ett program från användarupplevelse i Azure Active Directory access paneler eller Office 365 startprogram.
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
ms.topic: article
ms.date: 01/04/2018
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: b60384663d79294531225612a767663e0d71723f
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303883"
---
# <a name="hide-an-application-from-users-experience-in-azure-active-directory"></a>Dölj ett program från användarens upplevelse i Azure Active Directory

Om du har ett program som du inte vill ska visas på användarnas åtkomst paneler eller Office 365 startprogram finns alternativ för att dölja den här app-panelen.  Följande två alternativ är tillgängliga för att dölja program från användarens app startprogram.

- Dölj tredjepartsprogram från användare åtkomst paneler och Office 365 app startprogram
- Dölj alla Office 365-program från användare åtkomst till paneler

Genom att dölja appanvändare fortfarande har behörighet att appen men kommer inte se dem visas på deras app startprogram. Du måste ha behörighet att hantera enterprise-appen och du måste vara en global administratör för katalogen.


## <a name="hiding-an-application-from-users-end-user-experiences"></a>Döljer ett program från användarens upplevelse för slutanvändaren
Du kan använda stegen nedan, beroende på din situation för att dölja program från åtkomstpanelen.

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Hur döljer en tredjeparts-app från användarens åtkomstpanelen och O365 app startprogram?
Använd följande steg för att dölja ett program från en användares åtkomstpanelen och Office 365 app startprogram.

1.  Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2.  Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3.  På den **Azure Active Directory - *directoryname***  skärmen (det vill säga Azure AD skärmen för den katalog som du hanterar), Välj **företagsprogram**.
![Företagets appar](./media/hide-application-from-user-portal/app1.png)
4.  På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5.  På den **företagsprogram - alla program** väljer du en app.</br>
![Företagets appar](./media/hide-application-from-user-portal/app2.png)
6.  På den ***appname*** skärmen (det vill säga skärmen med namnet på den valda appen i namnet), välj Egenskaper.
7.  På den  ***appname* -egenskaper** väljer **Ja** för **synlig för användarna?**.
![Företagets appar](./media/hide-application-from-user-portal/app3.png)
8.  Välj den **spara** kommando.

### <a name="how-do-i-hide-office-365-applications-from-users-access-panel"></a>Hur döljer Office 365-program från användarens åtkomstpanelen?

Använd följande steg för att dölja alla Office 365-program från åtkomstpanelen. Dessa appar visas i Office 365-portalen

1.  Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2.  Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3.  På den **Azure Active Directory - *directoryname***  skärmen (det vill säga Azure AD skärmen för den katalog som du hanterar), Välj **användarinställningar**.
4.  På den **användarinställningar** skärmen under **företagsprogram** Välj **Ja** för **användare kan bara visa Office 365-appar i Office 365-portalen**.

![Företagsappar](./media/hide-application-from-user-portal/apps4.png)

## <a name="next-steps"></a>Nästa steg
* [Se alla grupper](../active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en enterprise-app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](remove-user-or-group-access-portal.md)
* [Ändra namnet eller logotypen av en enterprise-app](change-name-or-logo-portal.md)

