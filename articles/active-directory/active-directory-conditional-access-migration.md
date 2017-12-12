---
title: Migrera klassiska principer i Azure portal | Microsoft Docs
description: Migrera klassiska principer i Azure-portalen.
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 416ae171564fa23473d09bad950d149e201bcc98
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>Migrera klassiska principer i Azure-portalen 


[Villkorlig åtkomst](active-directory-conditional-access-azure-portal.md) är en funktion i Azure Active directory (AD Azure) som gör det möjligt att styra hur behöriga användare har åtkomst till dina molnappar. Syftet är fortfarande samma, har lanseringen av den nya Azure-portalen också medfört betydande förbättringar i hur villkorlig åtkomst fungerar. Principer för villkorlig åtkomst som du har konfigurerat utanför Azure-portalen kan samexistera med de nya principerna som du skapar i Azure-portalen. Så länge du inte inaktivera eller ta bort dem, tillämpas de i din miljö. Vi rekommenderar dock att du migrerar dina klassiska principer till den nya Azure AD villkorlig åtkomstprinciper eftersom:

- De nya principerna kan du hantera scenarier som du inte kan hantera med klassiska principer.

- Du kan minska antalet principer som du måste hantera genom att konsolidera dem.   

Det här avsnittet får du hjälp med migreringen av din befintliga klassiska principer till den nya nya Azure AD-principer för villkorlig åtkomst.


## <a name="classic-policies"></a>Klassiska principer

Principer för villkorlig åtkomst för Azure AD och Intune som du inte har skapat i Azure-portalen kallas också **klassiska principer**. Om du vill migrera din klassiska principer behöver du inte har åtkomst till den klassiska Azure-portalen. Azure-portalen tillhandahåller en [ **klassisk principer (förhandsgranskning)** visa](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies) som gör att du kan granska din klassiska principer.

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>Öppna en klassiska princip

**Så här öppnar du en princip för klassiska:**

1. I den [Azure-portalen](https://portal.azure.com), klicka på den vänstra navigeringsfält för, **Azure Active Directory**.

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. På den **Azure Active Directory** sidan den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/02.png)
 
2. På den **villkorlig åtkomst - principer** sidan den **hantera** klickar du på **klassisk principer (förhandsgranskning)**.

3. Välj den princip som du är intresserad i listan över klassiska principer.   

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Principer för villkorlig åtkomst till Azure AD

Det här avsnittet ger dig med detaljerade anvisningar som hjälper dig att migrera din klassiska principer utan att vara bekant med villkorlig åtkomst i Azure AD-principerna. Dock är bekant med grundläggande begrepp och termer Azure AD villkorlig åtkomst som hjälper till att förbättra din migreringsupplevelse.

Se:

- [Villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md) mer information om grundläggande begrepp, terminologi

- [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md) att bekanta dig med användargränssnittet i Azure-portalen


 





## <a name="multi-factor-authentication-policy"></a>Princip för multifaktorautentisering 

Det här exemplet illustrerar hur du migrerar en klassiska princip som kräver **multifaktorautentisering** för en molnapp. 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**För att migrera en klassiska princip.**

1. [Öppna principen klassiska](#open-a-classic-policy) att hämta den konfigurationsinställningarna.
2. Skapa en ny princip för villkorlig åtkomst i Azure AD att ersätta din klassiska princip. 


### <a name="create-a-new-conditional-access-policy"></a>Skapa en ny princip för villkorlig åtkomst


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

11. Inaktivera klassiska principen. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>Nästa steg

- Om du vill veta hur du konfigurerar en princip för villkorlig åtkomst finns [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

- Om du är redo att konfigurera principer för villkorlig åtkomst för din miljö finns i [bästa praxis för villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-best-practices.md). 
