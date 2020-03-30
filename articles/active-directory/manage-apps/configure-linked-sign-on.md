---
title: Länkad inloggning för Azure AD-appar – Microsofts identitetsplattform
description: Konfigurera länkad enkel inloggning (SSO) till dina Azure AD-företagsprogram i Microsoft Identity Platform (Azure AD)
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
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063551"
---
# <a name="configure-linked-sign-on"></a>Konfigurera länkad inloggning

När du lägger till ett webbprogram för galleri eller icke-galleri [länkas](what-is-single-sign-on.md)ett av de enkla inloggningsalternativ som är tillgängliga för dig. Välj det här alternativet om du vill lägga till en länk till programmet i organisationens Azure AD Access Panel eller Office 365-portal. Du kan använda den här metoden för att lägga till länkar till anpassade webbprogram som för närvarande använder Active Directory Federation Services (eller annan federationstjänst) i stället för Azure AD för autentisering. Du kan också lägga till djuplänkar till specifika SharePoint-sidor eller andra webbsidor som du bara vill ska visas på användarens åtkomstpaneler.

## <a name="before-you-begin"></a>Innan du börjar

Om programmet inte har lagts till i din Azure [AD-klient](add-non-gallery-app.md)läser du Lägga till en [galleriapp](add-gallery-app.md) eller Lägg till en app som inte är galleri.

### <a name="open-the-app-and-select-linked-sign-on"></a>Öppna appen och välj länkad inloggning

1. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

1. Navigera till **Azure Active Directory** > **Enterprise-program**. Ett slumpmässigt urval av programmen i din Azure AD-klient visas. 

1. Välj **Alla program**på **menyn Programtyp** och välj sedan **Använd**.

1. Ange namnet på programmet i sökrutan och välj sedan programmet från resultaten.

1. Under avsnittet **Hantera** väljer du **Enkel inloggning**. 

1. Välj **Länkad**.

1. Ange URL:en för programmet som du vill länka till. Skriv URL:en och välj **Spara**. 
 
1. Du kan tilldela användare och grupper till programmet, vilket gör att programmet visas i Startprogrammet för [Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) eller [Azure AD-åtkomstpanelen](end-user-experiences.md) för dessa användare.

1. Välj **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Tilldela användare eller grupper till programmet](methods-for-assigning-users-and-groups.md)
- [Konfigurera automatisk etablering av användarkonton](../app-provisioning/configure-automatic-user-provisioning-portal.md)
