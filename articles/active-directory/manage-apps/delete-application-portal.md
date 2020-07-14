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
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec0b3ef559abe1c65872d8ecf87f63e6ff3ed4b0
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223915"
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
- Valfritt: slut för ande av konfiguration av [enkel inloggning](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Använd en miljö som inte är en produktions miljö för att testa stegen i den här snabb starten.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Ta bort ett program från din Azure AD-klient

Ta bort ett program från Azure AD-klienten:

1. I Azure AD-portalen väljer du **företags program**. Hitta och välj sedan det program som du vill ta bort. I det här fallet har vi tagit bort det **GitHub_test** program som vi lade till i föregående snabb start.
1. I avsnittet **Hantera** i det vänstra fönstret väljer du **Egenskaper**.
1. Välj **ta bort**och välj sedan **Ja** för att bekräfta att du vill ta bort appen från din Azure AD-klient.


## <a name="next-steps"></a>Nästa steg

- [Metod tips för program hantering](application-management-fundamentals.md)
- [Vanliga scenarier för program hantering](common-scenarios.md)
- [Synlighet och kontroll av program hantering](cloud-app-security.md)