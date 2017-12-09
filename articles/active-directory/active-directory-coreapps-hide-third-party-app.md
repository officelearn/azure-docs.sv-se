---
title: "Dölj tredjepartsprogram från användarens upplevelse i Azure Active Directory | Microsoft Docs"
description: "Hur du döljer tredjepartsprogram från användarens upplevelse i Azure Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: billmath
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 976cbb1341493186b9996d250ebca8f2f3688fdf
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="hide-a-third-party-application-from-users-experience-in-azure-active-directory"></a>Dölj tredjepartsprogram från användarens upplevelse i Azure Active Directory

Om du har en tredje parts-program (en app som publicerats av andra än Microsoft) att du inte vill visas på användarnas åtkomst paneler eller Office 365 startprogram, finns alternativet att dölja den här app-panelen. Genom att dölja appanvändare fortfarande har behörighet att appen men kommer inte se dem visas på deras app startprogram. Du måste ha behörighet att hantera enterprise-appen och du måste vara global administratör för katalogen.

## <a name="hiding-a-third-party-app-from-a-users-experience"></a>Dölja en tredjeparts-app från användarens upplevelse
Använd följande steg för att dölja en tredjeparts-app från en användares åtkomstpanelen och Office 365 app startprogram

### <a name="how-do-i-hide-a-third-party-app-from-users-access-panel-and-o365-app-launchers"></a>Hur döljer en tredjeparts-app från användarens åtkomstpanelen och O365 app startprogram?

1.  Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2.  Välj **fler tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3.  På den **Azure Active Directory - *directoryname***  skärmen (det vill säga Azure AD skärmen för den katalog som du hanterar), Välj **företagsprogram**.
![Företagsappar](media/active-directory-coreapps-hide-third-party-app/app1.png)
4.  På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5.  På den **företagsprogram - alla program** väljer du en app.</br>
![Företagsappar](media/active-directory-coreapps-hide-third-party-app/app2.png)
6.  På den ***appname*** skärmen (det vill säga skärmen med namnet på den valda appen i namnet), välj Egenskaper.
7.  På den  ***appname* -egenskaper** väljer **Ja** för **synlig för användarna?**.
![Företagsappar](media/active-directory-coreapps-hide-third-party-app/app3.png)
8.  Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Se alla grupper](active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Ändra namnet eller logotypen av en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
