---
title: "Kom igång med villkorlig åtkomst i Azure Active Directory | Microsoft Docs"
description: "Testa villkorlig åtkomst med ett villkor för platsen."
services: active-directory
keywords: "villkorlig åtkomst till appar, villkorlig åtkomst med Azure AD, säker åtkomst till företagets resurser, principer för villkorlig åtkomst"
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: f31487c5b5d8b888c7eb58aea2e42199b1614af8
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-conditional-access-in-azure-active-directory"></a>Kom igång med villkorlig åtkomst i Azure Active Directory

Villkorlig åtkomst är en funktion i Azure Active Directory som gör att du kan definiera villkor under vilka behöriga användare kan komma åt dina appar. 

Det här avsnittet innehåller anvisningar för att testa en villkorad tillgång baserat på en plats-villkor i din miljö.  


## <a name="scenario-description"></a>Scenariobeskrivning

Ett vanligt krav i många organisationer är att bara kräver Multi-Factor authentication för åtkomst till appar som inte utförs från företagets intranät. Du kan enkelt göra det här målet genom att konfigurera en princip för plats-baserad villkorlig åtkomst med Azure Active Directory. Det här avsnittet ger detaljerade anvisningar för att konfigurera en relaterad metod. Principen använder [tillförlitliga IP-adresser](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips) har alla andra platser i intranätet och för att särskilja åtkomstförsök från företaget.


## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i det här avsnittet förutsätter att du är bekant med principerna som beskrivs i [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

Om du vill testa det här scenariot måste du:

- Skapa en testanvändare 

- Tilldela en Azure AD Premium-licens till testanvändaren

- Konfigurera en hanterad app och tilldela den din testanvändare

- Konfigurera tillförlitliga IP-adresser

Om du behöver mer information om betrodda IP-adresser, se [tillförlitliga IP-adresser](../multi-factor-authentication/multi-factor-authentication-whats-next.md#trusted-ips).


## <a name="policy-configuration-steps"></a>Konfigurationssteg för principen

**Om du vill konfigurera din princip för villkorlig åtkomst, gör du:**

1. I Azure-portalen på den vänstra navigeringsfält för, klickar du på **Azure Active Directory**. 

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/01.png)

2. På den **Azure Active Directory** blad i den **hantera** klickar du på **villkorlig åtkomst**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/02.png)
 
3. På den **villkorlig åtkomst** bladet för att öppna den **ny** bladet i verktygsfältet högst upp, klickar du på **Lägg till**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/03.png)

4. På den **ny** blad i den **namn** textruta, ange ett namn för principen.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/04.png)

5. I den **tilldelning** klickar du på **användare och grupper**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/05.png)

6. På den **användare och grupper** bladet utför följande steg:

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/06.png)

    a. Klicka på **Välj användare och grupper**.

    b. Klicka på **Välj**.

    c. På den **Välj** bladet Välj din testanvändare och klicka sedan på **Välj**.

    d. På den **användare och grupper** bladet, klickar du på **klar**.

7. På den **ny** bladet för att öppna den **Molnappar** blad i den **tilldelning** klickar du på **Molnappar**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. På den **Molnappar** bladet utför följande steg:

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/08.png)

    a. Klicka på **Välj appar**.

    b. Klicka på **Välj**.

    c. På den **Välj** bladet Välj en molnapp i och klicka sedan på **Välj**.

    d. På den **Molnappar** bladet, klickar du på **klar**.

9. På den **ny** bladet för att öppna den **villkor** blad i den **tilldelning** klickar du på **villkor**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/09.png)

10. På den **villkor** bladet för att öppna den **platser** bladet, klickar du på **platser**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/10.png)

11. På den **platser** bladet utför följande steg:

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/11.png)

    a. Under **konfigurera**, klickar du på **Ja**.

    b. Under **inkludera**, klickar du på **alla platser**.

    c. Klicka på **undanta**, och klicka sedan på **alla betrodda IP-adresser**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/12.png)

    d. Klicka på **Klar**.

12. På den **villkor** bladet, klickar du på **klar**.

13. På den **ny** bladet för att öppna den **bevilja** blad i den **kontroller** klickar du på **bevilja**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/13.png)

14. På den **bevilja** bladet utför följande steg:

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/14.png)

    a. Välj **kräver Multi-Factor authentication**.

    b. Klicka på **Välj**.

15. På den **ny** bladet under **aktiverar principen**, klickar du på **på**.

    ![Villkorlig åtkomst](./media/active-directory-conditional-access-azure-portal-get-started/15.png)

16. På den **ny** bladet, klickar du på **skapa**.


## <a name="testing-the-policy"></a>Testa principen

Om du vill testa din princip bör du komma åt din app från en enhet som: 

1. Har en IP-adress som ligger inom den konfigurerade betrodda IP-adresser 

1. Har en IP-adress som inte ligger inom den konfigurerade betrodda IP-adresser

Multifaktorautentisering bör endast krävas under ett anslutningsförsök gjordes från en enhet som inte ligger inom den konfigurerade betrodda IP-adresser. 


## <a name="next-steps"></a>Nästa steg

Om du vill lära dig mer om villkorlig åtkomst kan se [villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal.md).

