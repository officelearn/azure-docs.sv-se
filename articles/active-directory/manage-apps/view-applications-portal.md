---
title: Snabbstart – visa klientprogram med Azure Active Directory
description: I den här snabbstarten använder du Azure-portalen för att visa programmen i din Azure Active Directory -klientorganisation (Azure AD).
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d40e968bb7079d50e3fa18889ae996c9b59c90f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "74421817"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Snabbstart: Visa dina Azure Active Directory-klientprogram

I den här snabbstarten använder du Azure Portal för att visa programmen i din Azure Active Directory-klientorganisation (Azure AD).

## <a name="before-you-begin"></a>Innan du börjar

Det måste finnas minst ett program i Azure AD-klientorganisationen för att du ska se några resultat. Anvisningar som beskriver hur du lägger till ett program finns i snabbstarten [Lägga till ett program](add-application-portal.md).

Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

## <a name="find-the-list-of-tenant-applications"></a>Leta upp listan med program i klientorganisationen

Du kan se programmen i din Azure AD-klientorganisation i avsnittet **Företagsprogram** på Azure Portal.

Så här hittar du programmen i din klientorganisation:

1. Välj Azure Active Directory på den vänstra navigeringspanelen i **[Azure-portalen](https://portal.azure.com)**. **Azure Active Directory**
1. Välj **Enterprise-program**i fönstret **Azure Active Directory** .
1. Välj **Alla program**på den nedrullningsbara menyn **Programtyp** och välj **Använd**. Ett slumpmässigt urval av programmen i din klientorganisation visas.
1. Om du vill visa fler program väljer du **Läs in mer** längst ned i listan. Om det finns många program i din klientorganisation kan det vara enklare att [söka efter ett visst program](#search-for-a-tenant-application), i stället för att bläddra i listan.

## <a name="select-viewing-options"></a>Välj visningsalternativ

Välj alternativ enligt vad du letar efter.

1. Du kan visa programmen efter **programtyp,** **programstatus**och **programsynlighet**.
1. Välj något av följande alternativ under **Programtyp**:

    - **Företagsprogram** visar andra program än Microsoft-program.
    - **Microsoft-program** visar Microsoft-program.
    - **Alla program** visar både Microsoft-program och andra program.

1. Välj **Alla**, **Inaktiverade** eller **Aktiverade** under **Programstatus**. Alternativet **Alla** omfattar både inaktiverade och aktiverade program.
1. Välj **Alla** eller **Dolda** under **Synlighet för program**. Alternativet **Dold** visar program som finns i klienten, men som inte är synliga för användarna.
1. När du har valt önskade alternativ väljer du **Använd**.

## <a name="search-for-a-tenant-application"></a>Söka efter ett klientprogram

Så här söker du efter ett visst program:

1. Välj **Alla program**på **menyn Programtyp** och välj **Använd**.
1. Ange namnet på det program som du vill söka efter. Om programmet har lagts till i din Azure AD-klient visas det i sökresultaten. Det här exemplet visar att GitHub inte har lagts till i klientprogrammen.

    ![Exempel visar att en app inte har lagts till i klienten](media/view-applications-portal/search-for-tenant-application.png)

1. Prova att skriva de första bokstäverna i ett programnamn. I det här exemplet visas alla program som börjar med **Sales**.

    ![Exempel visar alla appar som börjar med Försäljning](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten lärde du dig hur du visar programmen i din Azure AD-klientorganisation. Du har lärt dig hur du filtrerar listan över program efter programtyp, status och synlighet. Du har också lärt dig hur du söker efter ett specifikt program.

Nu när du har hittat programmet du letade efter kan du fortsätta att [lägga till fler program till din klient.](add-application-portal.md) Du kan också välja vilket program som ska visas eller redigeras och konfigurationsalternativ. Du kan till exempel konfigurera enkel inloggning.

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
