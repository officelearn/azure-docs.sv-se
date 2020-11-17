---
title: 'Snabb start: ta bort ett program från din Azure Active Directory (Azure AD)-klient'
description: Den här snabb starten använder Azure Portal för att ta bort ett program från din Azure Active Directory (Azure AD)-klient.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/01/2020
ms.author: kenwith
ms.openlocfilehash: 677c9c0123c174fdf2cf8e94f9c8110656d21306
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94649406"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: ta bort ett program från din Azure Active Directory (Azure AD)-klient

Den här snabb starten använder Azure Portal för att ta bort ett program som har lagts till i din Azure Active Directory (Azure AD)-klient.

## <a name="prerequisites"></a>Förutsättningar

Om du vill ta bort ett program från din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- Valfritt: Slutför [visningen av Visa dina appar](view-applications-portal.md).
- Valfritt: slut för ande av [Lägg till en app](add-application-portal.md).
- Valfritt: Slutför [konfiguration av en app](add-application-portal-configure.md).
- Valfritt: slut för ande av [tilldela användare till en app](add-application-portal-assign-users.md).
- Valfritt: slut för ande av konfiguration av [enkel inloggning](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Använd en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Ta bort ett program från din Azure AD-klient

Ta bort ett program från Azure AD-klienten:

1. I Azure AD-portalen väljer du **företags program**. Hitta och välj sedan det program som du vill ta bort. I det här fallet har vi tagit bort det **GitHub_test** program som vi lade till i föregående snabb start.
1. I avsnittet **Hantera** i det vänstra fönstret väljer du **Egenskaper**.
1. Välj **ta bort** och välj sedan **Ja** för att bekräfta att du vill ta bort appen från din Azure AD-klient.

> [!TIP]
> Du kan automatisera hantering av appar med hjälp av Graph API, se [Automatisera program hantering med Microsoft Graph API](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med den här snabb starts serien bör du ta bort appen för att rensa test klienten. Borttagning av appen täcktes i den här snabb starten.

## <a name="next-steps"></a>Nästa steg

Du har slutfört snabb starts serien! I nästa steg ska du läsa om metod tips i app Management.
> [!div class="nextstepaction"]
> [Metod tips för program hantering](application-management-fundamentals.md)