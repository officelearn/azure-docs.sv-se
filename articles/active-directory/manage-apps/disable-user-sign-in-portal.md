---
title: Inaktivera användarinloggningar för en enterprise-app i Azure Active Directory | Microsoft Docs
description: Så här inaktiverar du en enterprise-programmet så att inga användare kan logga in till den i Azure Active Directory
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
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 86ff34bb0780811663d437a9183d0b688b69073d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303922"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användarinloggningar för en enterprise-app i Azure Active Directory
Det är enkelt att inaktivera ett företagsprogram så att inga användare kan logga in till den i Azure Active Directory (AD Azure). Du måste ha behörighet att hantera enterprise-appen och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur inaktiverar användarinloggningar?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory** -  ***directoryname*** (det vill säga den Azure AD rutan för den katalog som du hanterar) väljer du **företagsprogram**.

    ![Öppna företagsappar](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5. På den **företagsprogram - alla program** väljer du en app.
6. På den ***appname*** (det vill säga i fönstret med namnet på den valda appen i namnet), väljer du **egenskaper**.

    ![Att välja kommandot alla program](./media/disable-user-sign-in-portal/select-app.png)
7. På den ***appname*** - **egenskaper** väljer **nr** för **aktiverats för användare att logga in?**.
8. Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Se alla grupper](../active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en enterprise-app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](remove-user-or-group-access-portal.md)
* [Ändra namnet eller logotypen av en enterprise-app](change-name-or-logo-portal.md)
