---
title: Migrera en klassiska princip som kräver multifaktorautentisering i Azure portal | Microsoft Docs
description: Den här artikeln visar hur du migrerar en klassiska princip som kräver multifaktorautentisering i Azure-portalen.
services: active-directory
keywords: villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 52d4297d2e86ad67672d38f1c527041b813f8d61
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34723695"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>Migrera en klassiska princip som kräver multifaktorautentisering i Azure-portalen 

Den här artikeln visar hur du migrerar en klassiska princip som kräver **multifaktorautentisering** för en molnapp. Även om det inte är en förutsättning, rekommenderar vi att du läser [migrera klassiska principer i Azure portal](active-directory-conditional-access-migration.md) innan du börjar migrera dina klassiska principer.


 
## <a name="overview"></a>Översikt 

Scenario i den här artikeln visar hur du migrerar en klassiska princip som kräver **multifaktorautentisering** för en molnapp. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


Migreringen består av följande steg:

1. [Öppna principen klassiska](#open-a-classic-policy) att hämta den konfigurationsinställningarna.
2. Skapa en ny princip för villkorlig åtkomst i Azure AD att ersätta din klassiska princip. 
3. Inaktivera klassiska principen.



## <a name="open-a-classic-policy"></a>Öppna en klassiska princip

1. I den [Azure-portalen](https://portal.azure.com), klicka på den vänstra navigeringsfält för, **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration-mfa/02.png)

3. I den **hantera** klickar du på **klassisk principer (förhandsgranskning)**.

    ![Klassiska principer](./media/active-directory-conditional-access-migration-mfa/12.png)

4. I listan över klassiska principer, klickar du på den princip som kräver **multifaktorautentisering** för en molnapp.

    ![Klassiska principer](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>Skapa en ny princip för villkorlig åtkomst


1. I den [Azure-portalen](https://portal.azure.com), klicka på den vänstra navigeringsfält för, **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/02.png)



3. På den **villkorlig åtkomst** sidan för att öppna den **ny** i verktygsfältet högst upp på sidan klickar du på **Lägg till**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/03.png)

4. På den **ny** sidan den **namn** textruta, ange ett namn för principen.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/29.png)

5. I den **tilldelningar** klickar du på **användare och grupper**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/05.png)

    a. Om du har alla användare som har valts i den klassiska principen, klickar du på **alla användare**. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/35.png)

    b. Om du har grupper som väljs i den klassiska principen, klickar du på **Välj användare och grupper**, och välj sedan de nödvändiga användare och grupper.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/36.png)

    c. Om du har de undantagna grupperna klickar du på den **undanta** och välj sedan de nödvändiga användare och grupper. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/37.png)

6. På den **ny** sidan för att öppna den **Molnappar** sidan den **tilldelning** klickar du på **Molnappar**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. På den **Molnappar** utför följande steg:

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/08.png)

    a. Klicka på **Välj appar**.

    b. Klicka på **Välj**.

    c. På den **Välj** , Välj en molnapp i, och klickar sedan på **Välj**.

    d. På den **Molnappar** klickar du på **klar**.



9. Om du har **kräver Multi-Factor authentication** valda:

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/26.png)

    a. I den **åtkomstkontroller** klickar du på **bevilja**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/27.png)

    b. På den **bevilja** klickar du på **bevilja åtkomst**, och klicka sedan på **kräver Multi-Factor authentication**.

    c. Klicka på **Välj**.


10. Klicka på **på** att aktivera din princip.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>Inaktivera klassiska principen

Om du vill inaktivera din princip för klassiska **inaktivera** i den **information** vyn.

![Klassiska principer](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>Nästa steg

- Mer information om migrering klassiska princip finns [migrera klassiska principer i Azure portal](active-directory-conditional-access-migration.md).


- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
