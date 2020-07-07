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
ms.openlocfilehash: 02d12538fce5ccc3905ea1170fc1a8324309004f
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2020
ms.locfileid: "85956006"
---
# <a name="quickstart-delete-an-application-from-your-azure-active-directory-azure-ad-tenant"></a>Snabb start: ta bort ett program från din Azure Active Directory (Azure AD)-klient

Den här snabb starten använder Azure Portal för att ta bort ett program som har lagts till i din Azure AD-klient.

## <a name="prerequisites"></a>Förutsättningar

Om du vill ta bort ett program från din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- En av följande roller: global administratör, moln program administratör, program administratör eller ägare av tjänstens huvud namn.
- (Valfritt: slut för ande av [Visa dina appar](view-applications-portal.md)).
- (Valfritt: slut för ande av [Lägg till en app](add-application-portal.md)).
- (Valfritt: Slutför [konfiguration av en app](add-application-portal-configure.md)).
- (Valfritt: slut för ande av konfiguration av [enkel inloggning](add-application-portal-setup-sso.md)).

>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Ta bort ett program från din Azure AD-klient

Ta bort ett program från Azure AD-klienten:

1. I Azure AD-portalen väljer du **företags program** och sedan söker du efter och väljer det program som du vill ta bort. I det här fallet tas vi bort GitHub_test programmet som vi lade till i föregående snabb start.
2. I avsnittet hantera i navigeringen väljer du **Egenskaper**.
3. Välj Ta bort och välj sedan Ja för att bekräfta att du vill ta bort appen från din Azure AD-klient.


## <a name="next-steps"></a>Nästa steg

- [Metod tips för program hantering](application-management-fundamentals.md)
- [Vanliga scenarier för program hantering](common-scenarios.md)
- [Synlighet och kontroll av program hantering](cloud-app-security.md)