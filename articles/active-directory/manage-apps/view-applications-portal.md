---
title: Quickstart - View tenant applications using Azure Active Directory
description: In this Quickstart, use the Azure portal to view the applications in your Azure Active Directory (Azure AD) tenant.
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
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74421817"
---
# <a name="quickstart-view-your-azure-active-directory-tenant-applications"></a>Quickstart: View your Azure Active Directory tenant applications

I den här snabbstarten använder du Azure Portal för att visa programmen i din Azure Active Directory-klientorganisation (Azure AD).

## <a name="before-you-begin"></a>Innan du börjar

Det måste finnas minst ett program i Azure AD-klientorganisationen för att du ska se några resultat. Anvisningar som beskriver hur du lägger till ett program finns i snabbstarten [Lägga till ett program](add-application-portal.md).

Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

## <a name="find-the-list-of-tenant-applications"></a>Leta upp listan med program i klientorganisationen

Du kan se programmen i din Azure AD-klientorganisation i avsnittet **Företagsprogram** på Azure Portal.

Så här hittar du programmen i din klientorganisation:

1. In the **[Azure portal](https://portal.azure.com)** , on the left navigation panel, select **Azure Active Directory**.
1. In the **Azure Active Directory** pane, select **Enterprise applications**.
1. From the **Application Type** drop-down menu, select **All Applications**, and choose **Apply**. Ett slumpmässigt urval av programmen i din klientorganisation visas.
1. To view more applications, select **Load more** at the bottom of the list. Om det finns många program i din klientorganisation kan det vara enklare att [söka efter ett visst program](#search-for-a-tenant-application), i stället för att bläddra i listan.

## <a name="select-viewing-options"></a>Välj visningsalternativ

Select options according to what you're looking for.

1. You can view the applications by **Application Type**, **Application Status**, and **Application visibility**.
1. Välj något av följande alternativ under **Programtyp**:

    - **Företagsprogram** visar andra program än Microsoft-program.
    - **Microsoft-program** visar Microsoft-program.
    - **Alla program** visar både Microsoft-program och andra program.

1. Välj **Alla**, **Inaktiverade** eller **Aktiverade** under **Programstatus**. Alternativet **Alla** omfattar både inaktiverade och aktiverade program.
1. Välj **Alla** eller **Dolda** under **Synlighet för program**. The **Hidden** option shows applications that are in the tenant, but aren't visible to users.
1. After choosing the options you want, select **Apply**.

## <a name="search-for-a-tenant-application"></a>Söka efter ett klientprogram

To search for a particular application:

1. In the **Application Type** menu, select **All applications**, and choose **Apply**.
1. Ange namnet på det program som du vill söka efter. If the application has been added to your Azure AD tenant, it appears in the search results. This example shows that GitHub hasn't been added to the tenant applications.

    ![Example shows an app hasn't been added to the tenant](media/view-applications-portal/search-for-tenant-application.png)

1. Prova att skriva de första bokstäverna i ett programnamn. I det här exemplet visas alla program som börjar med **Sales**.

    ![Example shows all apps that start with Sales](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Nästa steg

In this quickstart, you learned how to view the applications in your Azure AD tenant. You learned how to filter the list of applications by application type, status, and visibility. Du har också lärt dig hur du söker efter ett specifikt program.

Now that you've found the application you were looking for, you can continue to [Add more applications to your tenant](add-application-portal.md). Or, you can select the application to view or edit properties and configuration options. Du kan till exempel konfigurera enkel inloggning.

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](configure-single-sign-on-non-gallery-applications.md)
