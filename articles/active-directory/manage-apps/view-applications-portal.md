---
title: 'Snabb start: Visa listan över program som använder din Azure Active Directory (Azure AD)-klient för identitets hantering'
description: I den här snabb starten ska du använda Azure Portal för att visa en lista över program som har registrerats för att använda din Azure Active Directory (Azure AD)-klient för identitets hantering.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: quickstart
ms.date: 04/09/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 249cdbef6214eb8c66da841d9eae817bcf794996
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86055400"
---
# <a name="quickstart-view-the-list-of-applications-that-are-using-your-azure-active-directory-azure-ad-tenant-for-identity-management"></a>Snabb start: Visa listan över program som använder din Azure Active Directory (Azure AD)-klient för identitets hantering

Kom igång med att använda Azure AD som identitets-och åtkomst hanterings system (IAM) för de program som organisationen använder. I den här snabb starten kommer du att se programmen, även kallade appar, som redan har kon figurer ATS för att använda Azure AD-klienten som identitets leverantör.

## <a name="prerequisites"></a>Krav

Om du vill visa program som har registrerats i din Azure AD-klient behöver du:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

>[!IMPORTANT]
>Vi rekommenderar att du använder en icke-produktions miljö för att testa stegen i den här snabb starten.

## <a name="find-the-list-of-applications-in-your-tenant"></a>Hitta listan över program i din klient organisation

De program som har registrerats med din Azure AD-klient visas i avsnittet **Enterprise Apps** i Azure Portal.

Så här visar du de program som är registrerade i din klient organisation:

1. Logga in på din [Azure Portal](https://portal.azure.com).
2. På den vänstra navigerings panelen väljer du **Azure Active Directory**.
3. I fönstret **Azure Active Directory** väljer du **företags program**.
4. I list rutan **program typ** väljer du **alla program**och sedan **Använd**. Ett slumpmässigt urval av programmen i din klientorganisation visas.
5. Om du vill visa fler program väljer du **Läs in mer** längst ned i listan. Om det finns flera program i klient organisationen kan det vara lättare att söka efter ett visst program i stället för att bläddra i listan. Sökning efter ett visst program beskrivs senare i den här snabb starten.

## <a name="select-viewing-options"></a>Välj visningsalternativ

Välj alternativ enligt vad du letar efter.

1. Du kan visa programmen efter **program typ**, **program status**och **program synlighet**.
2. Välj något av följande alternativ under **Programtyp**:
    - **Företagsprogram** visar andra program än Microsoft-program.
    - **Microsoft-program** visar Microsoft-program.
    - **Alla program** visar både Microsoft-program och andra program.
3. Välj **Alla**, **Inaktiverade** eller **Aktiverade** under **Programstatus**. Alternativet **Alla** omfattar både inaktiverade och aktiverade program.
4. Välj **Alla** eller **Dolda** under **Synlighet för program**. Alternativet **dold** visar program som finns i klienten, men som inte är synliga för användare.
5. När du har valt de alternativ som du vill använda väljer du **Använd**.

## <a name="search-for-an-application"></a>Söka efter ett program

Så här söker du efter ett visst program:

1. I menyn **program typ** väljer du **alla program**och sedan **Använd**.
2. Ange namnet på det program som du vill söka efter. Om programmet har lagts till i din Azure AD-klient visas det i Sök resultaten. Det här exemplet visar att GitHub inte har lagts till i klient programmet.
    ![Exempel visar att en app inte har lagts till i klient organisationen](media/view-applications-portal/search-for-tenant-application.png)
3. Prova att skriva de första bokstäverna i ett programnamn. I det här exemplet visas alla program som börjar med **Sales**.
    ![Exempel visar alla appar som börjar med Sales](media/view-applications-portal/search-by-prefix.png)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du visar programmen i din Azure AD-klient. Du har lärt dig hur du filtrerar listan över program efter program typ, status och synlighet. Du har också lärt dig hur du söker efter ett specifikt program.

- [Lägga till en app](add-application-portal.md)
- [Konfigurera en app](add-application-portal-configure.md)
- [Konfigurera enkel inloggning](add-application-portal-setup-sso.md)
- [Ta bort en app](delete-application-portal.md)
