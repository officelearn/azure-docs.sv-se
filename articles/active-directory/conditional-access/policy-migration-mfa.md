---
title: Migrera principer för villkorlig åtkomst – Azure Active Directory
description: Den här artikeln visar hur du migrerar en klassisk princip som kräver multifaktorautentisering i Azure-portalen.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: d637ac464b689a25ce5d5a79cf47da0c85d38d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74846050"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Migrera en klassisk princip i Azure-portalen

Den här artikeln visar hur du migrerar en klassisk princip som kräver **multifaktorautentisering** för en molnapp. Även om det inte är en förutsättning rekommenderar vi att du läser [Migrera klassiska principer i Azure-portalen](policy-migration.md) innan du börjar migrera dina klassiska principer.

![Klassisk principinformation som kräver MFA för Salesforce-appen](./media/policy-migration/33.png)

Migreringsprocessen består av följande steg:

1. [Öppna den klassiska principen](#open-a-classic-policy) för att hämta konfigurationsinställningarna.
1. Skapa en ny Azure AD-princip för villkorlig åtkomst som ersätter din klassiska princip. 
1. Inaktivera den klassiska principen.

## <a name="open-a-classic-policy"></a>Öppna en klassisk princip

1. Navigera till **Azure Active Directory** > **Security** > **Conditional Access**i [Azure](https://portal.azure.com)Portal .
1. Välj **klassiska principer**.

   ![Klassisk policyvy](./media/policy-migration-mfa/12.png)

1. Välj den princip som du vill migrera i listan över klassiska principer. Dokumentera konfigurationsinställningarna så att du kan återskapa med en ny princip för villkorlig åtkomst.

## <a name="create-a-new-conditional-access-policy"></a>Skapa en ny princip för villkorlig åtkomst

1. Navigera till **Azure Active Directory** > **Security** > **Conditional Access**i [Azure](https://portal.azure.com)Portal .
1. Om du vill skapa en ny princip för villkorlig åtkomst väljer du **Ny princip**.
1. Skriv ett namn på principen i textrutan **Namn** på den **nya** sidan.
1. Klicka på **Användare och grupper**i avsnittet **Tilldelningar** .
   1. Om du har markerat alla användare i den klassiska principen klickar du på **Alla användare**. 
   1. Om du har markerat grupper i den klassiska principen klickar du på **Välj användare och grupper**och väljer sedan de användare och grupper som krävs.
   1. Om du har de uteslutna grupperna klickar du på fliken **Uteslut** och väljer sedan de användare och grupper som krävs. 
   1. Välj **Klar**
1. Klicka på **Molnappar eller åtgärder**i avsnittet **Tilldelning** .
1. Gör följande på sidan **Cloud-appar eller åtgärder:**
   1. Klicka på **Välj appar**.
   1. Klicka på **Markera**.
   1. På sidan **Välj** väljer du din molnapp och klickar sedan på **Välj**.
   1. Klicka på **Klar**på sidan **Molnappar** .
1. Om du har **kräv multifaktorautentisering** vald:
   1. Klicka på **Bevilja**i avsnittet **Access-kontroller** .
   1. Klicka på Bevilja **åtkomst**på sidan **Bevilja** och klicka sedan på **Kräv multifaktorautentisering**.
   1. Klicka på **Markera**.
1. Klicka **på På** för att aktivera din princip och välj sedan **Spara**.

   ![Skapa principer för principen för villkorlig åtkomst](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>Inaktivera den klassiska principen

Om du vill inaktivera den klassiska principen klickar du på **Inaktivera** i **informationsvyn.**

![Inaktivera klassiska principer](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om den klassiska principmigreringen finns [i Migrera klassiska principer i Azure-portalen](policy-migration.md).
- [Använd endast rapportläge för villkorlig åtkomst för att fastställa effekten av nya principbeslut.](concept-conditional-access-report-only.md)