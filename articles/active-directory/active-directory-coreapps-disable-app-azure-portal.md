---
title: "Inaktivera användarinloggningar för en enterprise-app i Azure Active Directory | Microsoft Docs"
description: "Så här inaktiverar du en enterprise-programmet så att inga användare kan logga in till den i Azure Active Directory"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: a27562f9-18dc-42e8-9fee-5419566f8fd7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: curtand
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: d31aa3b1399bab2b1dcfdb2eb6ece393610c769f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användarinloggningar för en enterprise-app i Azure Active Directory
Det är enkelt att inaktivera ett företagsprogram så att inga användare kan logga in till den i Azure Active Directory (AD Azure). Du måste ha behörighet att hantera enterprise-appen och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur inaktiverar användarinloggningar?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **fler tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory** -  ***directoryname*** bladet (det vill säga Azure AD bladet för den katalog som du hanterar), Välj **företagsprogram**.

    ![Öppna företagsappar](./media/active-directory-coreapps-disable-app-azure-portal/open-enterprise-apps.png)
4. På den **företagsprogram** bladet väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5. På den **företagsprogram - alla program** bladet väljer du en app.
6. På den ***appname*** bladet (det vill säga bladet med namnet på den valda appen i namnet), Välj **egenskaper**.

    ![Att välja kommandot alla program](./media/active-directory-coreapps-disable-app-azure-portal/select-app.png)
7. På den ***appname*** - **egenskaper** bladet väljer **nr** för **aktiverats för användare att logga in?**.
8. Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Se alla grupper](active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en enterprise-app](active-directory-coreapps-assign-user-azure-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Ändra namnet eller logotypen av en enterprise-app](active-directory-coreapps-change-app-logo-user-azure-portal.md)
