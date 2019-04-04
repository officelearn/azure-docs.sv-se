---
title: Visa klientprogram – Azure Active Directory | Microsoft Docs
description: Använd Azure Portal och visa programmen i din Azure Active Directory-klientorganisation (Azure AD).
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/25/2018
ms.author: celested
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ca57746d1b13d2020f78d3c527e7d0c7a444769
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918407"
---
# <a name="view-your-azure-active-directory-tenant-applications"></a>Visa programmen i din Azure Active Directory-klientorganisation

I den här snabbstarten använder du Azure Portal för att visa programmen i din Azure Active Directory-klientorganisation (Azure AD).

## <a name="before-you-begin"></a>Innan du börjar

Det måste finnas minst ett program i Azure AD-klientorganisationen för att du ska se några resultat. Anvisningar som beskriver hur du lägger till ett program finns i snabbstarten [Lägga till ett program](add-application-portal.md).

Logga in på [Azure Portal](https://portal.azure.com) som global administratör för din Azure AD-klientorganisation, som administratör för molnprogram eller som programadministratör.

## <a name="find-the-list-of-tenant-applications"></a>Leta upp listan med program i klientorganisationen

Du kan se programmen i din Azure AD-klientorganisation i avsnittet **Företagsprogram** på Azure Portal.

Så här hittar du programmen i din klientorganisation:

1. I den  **[Azure-portalen](https://portal.azure.com)**, på den vänstra navigeringspanelen väljer **Azure Active Directory**. 

2. I den **Azure Active Directory** väljer **företagsprogram**. 

3. Från den **programtyp** nedrullningsbara menyn och välj **alla program**, och välj **tillämpa**. Ett slumpmässigt urval av programmen i din klientorganisation visas.
   
4. Om du vill visa fler program, Välj **Läs in fler** längst ned i listan. Om det finns många program i din klientorganisation kan det vara enklare att [söka efter ett visst program](#search-for-a-tenant-application), i stället för att bläddra i listan.

## <a name="select-viewing-options"></a>Välj visningsalternativ

Välj alternativ enligt vad du letar efter.

1. Du kan se program som **programtyp**, **programstatus**, och **synlighet för program**. 

2. Välj något av följande alternativ under **Programtyp**:

    - **Företagsprogram** visar andra program än Microsoft-program.
    - **Microsoft-program** visar Microsoft-program.
    - **Alla program** visar både Microsoft-program och andra program.

3. Välj **Alla**, **Inaktiverade** eller **Aktiverade** under **Programstatus**. Alternativet **Alla** omfattar både inaktiverade och aktiverade program.

4. Välj **Alla** eller **Dolda** under **Synlighet för program**. Den **Hidden** alternativet visar program som finns i klienten, men inte är synliga för användare.

5. När du har valt önskade alternativ, Välj **tillämpa**.
 

## <a name="search-for-a-tenant-application"></a>Söka efter ett klientprogram

Att söka efter ett visst program:

1. I den **programtyp** menyn och välj **alla program**, och välj **tillämpa**.

2. Ange namnet på det program som du vill söka efter. Om programmet har lagts till Azure AD-klienten, visas den i sökresultatet. Det här exemplet visar att GitHub inte har lagts till klient-program.

    ![Söka efter ett program](media/view-applications-portal/search-for-tenant-application.png)

3. Prova att skriva de första bokstäverna i ett programnamn. I det här exemplet visas alla program som börjar med **Sales**.

    ![Söka med ett prefix](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du visar program i Azure AD-klienten. Du har lärt dig hur du kan filtrera listan med program som programtyp, status och synlighet. Du har också lärt dig hur du söker efter ett specifikt program.

Nu när du har hittat programmet du letar efter kan du fortsätta att [lägga till fler program i din klient](add-application-portal.md). Du kan också välja programmet för att visa eller redigera egenskaper och alternativ. Du kan till exempel konfigurera enkel inloggning. 

> [!div class="nextstepaction"]
> [Konfigurera enkel inloggning](configure-single-sign-on-portal.md)


