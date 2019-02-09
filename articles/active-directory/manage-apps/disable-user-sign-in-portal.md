---
title: Inaktivera användarinloggningar för en företagsapp i Azure Active Directory | Microsoft Docs
description: Så här inaktiverar du ett företagsprogram så att inga användare kan logga in till det i Azure Active Directory
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: celested
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 00953911aa1dd2ffe05b78e5b63c45d67719193f
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963003"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Inaktivera användarinloggningar för en företagsapp i Azure Active Directory
Det är enkelt att inaktivera ett företagsprogram så att inga användare kan logga in till det i Azure Active Directory (AD Azure). Du måste ha behörighet att hantera appen enterprise och du måste vara global administratör för katalogen.

## <a name="how-do-i-disable-user-sign-ins"></a>Hur jag för att inaktivera användarinloggningar?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory** -  ***directoryname*** (det vill säga Azure AD fönstret för den katalog som du hanterar), väljer **företagsprogram**.

    ![Att öppna företagsappar](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5. På den **företagsprogram – alla program** fönstret väljer du en app.
6. På den ***appname*** (det vill säga i rutan med namnet på den valda appen i rubriken) väljer **egenskaper**.

    ![Att välja kommandot alla program](./media/disable-user-sign-in-portal/select-app.png)
7. På den ***appname*** - **egenskaper** väljer **nr** för **aktiverad för användare att logga in?**.
8. Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Ändra namnet eller logotyp i en företagsapp](change-name-or-logo-portal.md)
