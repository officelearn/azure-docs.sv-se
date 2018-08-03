---
title: 'Självstudier: Azure Active Directory-integrering med The Cloud Security Fabric | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och The Cloud Security Fabric.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 549e8810-1b3b-4351-bf4b-f07de98980d1
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 9908dae627ae11a42e8e01a9a4f4d11f35ce0f8d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422968"
---
# <a name="tutorial-azure-active-directory-integration-with-the-cloud-security-fabric"></a>Självstudier: Azure Active Directory-integrering med The Cloud Security Fabric

I den här självstudien får du lära dig hur du integrerar The Cloud Security Fabric med Azure Active Directory (AD Azure).

Integrera The Cloud Security Fabric med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till The Cloud Security Fabric.
- Du kan aktivera användarna att automatiskt få loggat in The Cloud Security-Infrastrukturen (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med The Cloud Security Fabric behöver du följande objekt:

- En Azure AD-prenumeration
- En The Cloud Security Fabric enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till The Cloud Security Fabric från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-the-cloud-security-fabric-from-the-gallery"></a>Att lägga till The Cloud Security Fabric från galleriet
För att konfigurera integrering av The Cloud Security Fabric i Azure AD, som du behöver lägga till The Cloud Security Fabric från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till The Cloud Security Fabric från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **The Cloud Security Fabric**väljer **The Cloud Security Fabric** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Cloud Security Fabric i listan med resultat](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med The Cloud Security Fabric baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad du motsvarighet i The Cloud Security-infrastrukturen är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i The Cloud Security-Infrastrukturen upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med The Cloud Security Fabric, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare The Cloud Security Fabric](#create-a-the-cloud-security-fabric-test-user)**  – du har en motsvarighet för Britta Simon i The Cloud Security-infrastrukturen som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt The Cloud Security Fabric-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med The Cloud Security Fabric:**

1. I Azure-portalen på den **The Cloud Security Fabric** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_samlbase.png)

1. På den **The Cloud Security Fabric domän och URL: er** avsnittet, utför följande steg:

    ![Cloud Security Fabric domän och URL: er med enkel inloggning för information](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL:
    | |
    |--|
    | `https://platform.cloudlock.com` |
    | `https://app.cloudlock.com` |

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster:
    | |
    |--|
    | `https://platform.cloudlock.com/gate/saml/sso/<subdomain>` |
    | `https://app.cloudlock.com/gate/saml/sso/<subdomain>` |

    > [!NOTE]
    > ID-värde är inte verkliga. Uppdatera värdet med det faktiska ID: T. Kontakta [Cloud Security Fabric klienten supportteamet](mailto:support@cloudlock.com) att hämta värdet. 

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/ciscocloudlock-tutorial/tutorial_general_400.png)

1. Att konfigurera enkel inloggning på **The Cloud Security Fabric** sida, som du behöver skicka de hämtade **XML-Metadata för** till [The Cloud Security Fabric supportteamet](mailto:support@cloudlock.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ciscocloudlock-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/ciscocloudlock-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ciscocloudlock-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ciscocloudlock-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.

### <a name="create-a-the-cloud-security-fabric-test-user"></a>Skapa en testanvändare The Cloud Security Fabric

I det här avsnittet skapar du en användare som kallas Britta Simon i The Cloud Security-infrastrukturen. Arbeta med [The Cloud Security Fabric supportteamet](mailto:support@cloudlock.com) att lägga till användare i The Cloud Security Fabric-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till The Cloud Security Fabric.

![Tilldela rollen][200]

**Om du vill tilldela The Cloud Security Fabric Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

1. I listan med program väljer **The Cloud Security Fabric**.

    ![Länken Cloud Security Fabric i listan med program](./media/ciscocloudlock-tutorial/tutorial_ciscocloudlock_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på The Cloud Security Fabric panel i åtkomstpanelen du bör få automatiskt loggat in på ditt The Cloud Security Fabric-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ciscocloudlock-tutorial/tutorial_general_01.png
[2]: ./media/ciscocloudlock-tutorial/tutorial_general_02.png
[3]: ./media/ciscocloudlock-tutorial/tutorial_general_03.png
[4]: ./media/ciscocloudlock-tutorial/tutorial_general_04.png

[100]: ./media/ciscocloudlock-tutorial/tutorial_general_100.png

[200]: ./media/ciscocloudlock-tutorial/tutorial_general_200.png
[201]: ./media/ciscocloudlock-tutorial/tutorial_general_201.png
[202]: ./media/ciscocloudlock-tutorial/tutorial_general_202.png
[203]: ./media/ciscocloudlock-tutorial/tutorial_general_203.png
