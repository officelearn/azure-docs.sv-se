---
title: Länkad inloggning för Azure AD-appar – Microsoft Identity Platform
description: Konfigurera länkad enkel inloggning (SSO) till dina Azure AD Enterprise-program i Microsoft Identity Platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfcece43ae1b7d7bcf0c38feba14f1e82b29f18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763490"
---
# <a name="configure-linked-sign-on"></a>Konfigurera länkad inloggning

När du lägger till ett galleri eller ett webb program som inte är Galleri, [länkas](what-is-single-sign-on.md)ett av de alternativ för enkel inloggning som är tillgängligt för dig. Välj det här alternativet om du vill lägga till en länk till programmet i organisationens Azure AD Access-panel eller Office 365-portal. Du kan använda den här metoden för att lägga till länkar till anpassade webb program som för närvarande använder Active Directory Federation Services (AD FS) (eller andra Federations tjänster) i stället för Azure AD för autentisering. Du kan också lägga till djup länkar till vissa SharePoint-sidor eller andra webb sidor som du bara vill ska visas på användarens åtkomst paneler.

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure AD-klient kan du läsa [Lägg till en Galleri app](add-gallery-app.md) eller [lägga till en app som inte är en Galleri](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Öppna appen och välj länkad inloggning

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

1. Gå till **Azure Active Directory**  >  **företags program**. Ett slumpmässigt exempel på programmen i din Azure AD-klient visas. 

1. I menyn **program typ** väljer du **alla program**och väljer sedan **Använd**.

1. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

1. Under avsnittet **Hantera** väljer du **enkel inloggning**. 

1. Välj **länkad**.

1. Ange URL: en för det program som ska länkas till. Skriv in URL: en och välj **Spara**. 
 
1. Du kan tilldela användare och grupper till programmet, vilket gör att programmet visas i [Office 365 App Launcher](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomst panelen](end-user-experiences.md) för dessa användare.

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användar konto](../app-provisioning/configure-automatic-user-provisioning-portal.md)
