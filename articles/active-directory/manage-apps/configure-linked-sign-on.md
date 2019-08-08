---
title: Länkad inloggning för Azure AD-program – Microsoft Identity Platform | Microsoft Docs
description: Konfigurera länkad enkel inloggning (SSO) till dina Azure AD Enterprise-program i Microsoft Identity Platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc1e26a3c7d284a60b830f6f66cdcecef97db4d6
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834618"
---
# <a name="configure-linked-sign-on"></a>Konfigurera länkad inloggning

När du lägger till ett galleri eller ett webb program som inte är Galleri, länkas ett av de alternativ för enkel inloggning [](what-is-single-sign-on.md)som är tillgängligt för dig. Välj det här alternativet om du vill lägga till en länk till programmet i organisationens Azure AD Access-panel eller Office 365-portal. Du kan använda den här metoden för att lägga till länkar till anpassade webb program som för närvarande använder Active Directory Federation Services (AD FS) (eller andra Federations tjänster) i stället för Azure AD för autentisering. Du kan också lägga till djup länkar till vissa SharePoint-sidor eller andra webb sidor som du bara vill ska visas på användarens åtkomst paneler.

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure AD-klient kan du läsa [Lägg till en Galleri app](add-gallery-app.md) eller [lägga till en app som inte är en Galleri](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Öppna appen och välj länkad inloggning

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

1. Gå till **Azure Active Directory** > **företags program**. Ett slumpmässigt exempel på programmen i din Azure AD-klient visas. 

1. I menyn **program typ** väljer du **alla program**och väljer sedan **Använd**.

1. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

1. Under avsnittet **Hantera** väljer du **enkel inloggning**. 

1. Välj **länkad**.

1. Ange URL: en för det program som ska länkas till. Skriv in URL: en och välj **Spara**. 
 
1. Du kan tilldela användare och grupper till programmet, vilket gör att programmet visas i [Office 365 App Launcher](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomst panelen](end-user-experiences.md) för dessa användare.

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användar konto](configure-automatic-user-provisioning-portal.md)
