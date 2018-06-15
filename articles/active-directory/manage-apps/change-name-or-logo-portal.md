---
title: Ändra namnet eller logotypen av en enterprise-app i Azure Active Directory | Microsoft Docs
description: Så här ändrar du namnet eller logotypen för en anpassad enterprise-app i Azure Active Directory
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
ms.openlocfilehash: ad424d6ca8ea8c35aa502a3d1bd98940591c38e8
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303898"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Ändra namn på eller av en enterprise-app i Azure Active Directory-logotypen
Det är enkelt att ändra namnet eller logotyp för en anpassad företagsprogram i Azure Active Directory (AD Azure). Du måste ha behörighet att göra dessa ändringar, och du måste vara skapare av anpassad app.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Hur ändrar jag enterprise appens namn eller logotyp?
1. Logga in på den [Azure-portalen](https://portal.azure.com) med ett konto som är en global administratör för katalogen.
2. Välj **alla tjänster**, ange **Azure Active Directory** i textrutan och välj sedan **RETUR**.
3. På den **Azure Active Directory - *directoryname***  (det vill säga den Azure AD rutan för den katalog som du hanterar) väljer du **företagsprogram**.

    ![Öppna företagsappar](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. På den **företagsprogram** väljer **alla program**. Du kan se en lista över appar som du kan hantera.
5. På den **företagsprogram - alla program** väljer du en app.
6. På den ***appname*** (det vill säga i fönstret med namnet på den valda appen i namnet), väljer du **egenskaper**.

    ![Att välja kommandot Egenskaper](./media/change-name-or-logo-portal/select-app.png)
7. På den ***appname*** **-egenskaper** rutan, bläddra efter en fil som ska användas som en logotyp som är nya eller redigera namnet på appen, eller båda.

    ![Ändra kommandot app logotyp eller nameproperties](./media/change-name-or-logo-portal/change-logo.png)
8. Välj den **spara** kommando.

## <a name="next-steps"></a>Nästa steg
* [Visa alla mina grupper](../active-directory-groups-view-azure-portal.md)
* [Tilldela en användare eller grupp till en enterprise-app](assign-user-or-group-access-portal.md)
* [Ta bort en användare eller grupp från en enterprise-app](remove-user-or-group-access-portal.md)
* [Inaktivera användarinloggningar för en enterprise-app](disable-user-sign-in-portal.md)
