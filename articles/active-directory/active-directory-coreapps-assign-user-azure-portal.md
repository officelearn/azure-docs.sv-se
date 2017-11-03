---
title: "Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory | Microsoft Docs"
description: "Hur du väljer en enterprise-app för att tilldela en användare eller grupp till den i Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.openlocfilehash: 8e61044f261033a473241e2de152026bf49c4c70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Tilldela en användare eller grupp till en enterprise-app i Azure Active Directory
Det är lätt att tilldela en användare eller grupp i enterprise-programmen i Azure Active Directory (AD Azure). Du måste ha behörighet att hantera enterprise-appen och du måste vara global administratör för katalogen.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Hur tilldelar åtkomst till en enterprise-app?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange Azure Active Directory i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory - *directoryname***  bladet (det vill säga Azure AD bladet för den katalog som du hanterar), Välj **företagsprogram**.

    ![Öppna företagsappar](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. På den **företagsprogram** bladet väljer **alla program**. Visas en lista över appar som du kan hantera.
5. På den **företagsprogram - alla program** bladet väljer du en app.
6. På den ***appname*** bladet (det vill säga bladet med namnet på den valda appen i namnet), Välj **användare och grupper**.

    ![Att välja kommandot alla program](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. På den ***appname*** **-användaren & grupptilldelning** bladet väljer den **Lägg till** kommando.
8. På den **Lägg uppdrag** bladet väljer **användare och grupper**.

    ![Tilldela en användare eller grupp i appen](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. På den **användare och grupper** bladet Välj en eller flera användare eller grupper i listan och välj sedan den **Välj** längst ned på bladet.
10. På den **Lägg uppdrag** bladet väljer **rollen**. Klicka sedan på den **Välj roll** bladet Välj en roll ska tillämpas på de valda användare eller grupper och välj sedan den **OK** längst ned på bladet.
11. På den **Lägg uppdrag** bladet väljer den **tilldela** längst ned på bladet. Tilldelade användare eller grupper har de behörigheter som definieras av den valda rollen för den här enterprise-appen.

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](active-directory-groups-view-azure-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Inaktivera användarinloggningar för en enterprise-app](active-directory-coreapps-disable-app-azure-portal.md)
* [Ändra namnet eller logotypen av en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
