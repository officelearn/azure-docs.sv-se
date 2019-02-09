---
title: Ändra namnet eller logotyp på en enterprise-app i Azure Active Directory | Microsoft Docs
description: Så här ändrar du namnet eller en anpassad enterprise-app i Azure Active Directory-logotyp
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
ms.openlocfilehash: 7b3d9558c11f1fc137e939e99dbf544bb22d4063
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/08/2019
ms.locfileid: "55962493"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Ändra namnet eller logotyp på en enterprise-app i Azure Active Directory
Det är enkelt att ändra namnet eller logotyp för ett anpassade företagsprogram i Azure Active Directory (AD Azure). Du måste ha behörighet att göra dessa ändringar, och du måste vara skaparen av den anpassade appen.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Hur ändrar jag en företagsapp namn eller logotyp?
1. Logga in på [Azure Portal](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory - *directoryname***  (det vill säga Azure AD fönstret för den katalog som du hanterar), väljer **företagsprogram**.

    ![Att öppna företagsappar](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5. På den **företagsprogram – alla program** fönstret väljer du en app.
6. På den ***appname*** (det vill säga i rutan med namnet på den valda appen i rubriken) väljer **egenskaper**.

    ![Att välja kommandot Egenskaper](./media/change-name-or-logo-portal/select-app.png)
7. På den ***appname*** **-egenskaper** fönstret, bläddra efter en fil som ska användas som en ny logotyp eller redigera appnamnet eller båda.

    ![Ändra kommandot för app-logotyp eller nameproperties](./media/change-name-or-logo-portal/change-logo.png)
8. Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Se alla mina grupper](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en företagsapp](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en företagsapp](remove-user-or-group-access-portal.md)
* [Inaktivera användarinloggningar för en företagsapp](disable-user-sign-in-portal.md)
