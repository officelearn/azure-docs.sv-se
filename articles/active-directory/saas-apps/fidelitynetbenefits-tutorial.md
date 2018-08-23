---
title: 'Självstudier: Azure Active Directory-integration med exakthet NetBenefits | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och återgivning NetBenefits.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 77dc8a98-c0e7-4129-ab88-28e7643e432a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: a1308035a8b758a9e2f824de3a78c03103c19931
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42054745"
---
# <a name="tutorial-azure-active-directory-integration-with-fidelity-netbenefits"></a>Självstudier: Azure Active Directory-integration med exakthet NetBenefits

Lär dig hur du integrerar återgivning NetBenefits med Azure Active Directory (AD Azure) i den här självstudien.

Integrera återgivning NetBenefits med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till återgivning NetBenefits.
- Du kan aktivera användarna att automatiskt få loggat in på exakthet NetBenefits (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med exakthet NetBenefits, behöver du följande objekt:

- En Azure AD-prenumeration
- En återgivning NetBenefits enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö.
Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till återgivning NetBenefits från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fidelity-netbenefits-from-the-gallery"></a>Att lägga till återgivning NetBenefits från galleriet

För att konfigurera integrering av återgivning NetBenefits i Azure AD, som du behöver lägga till återgivning NetBenefits från galleriet i din lista över hanterade SaaS-appar.

**Lägg till återgivning NetBenefits från galleriet, utför du följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **exakthet NetBenefits**väljer **exakthet NetBenefits** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Återgivning NetBenefits i resultatlistan](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med exakthet NetBenefits baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i återgivning NetBenefits är till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i återgivning NetBenefits upprättas.

I återgivning NetBenefits **användaren** mappning ska göras med **Azure AD-användare** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med exakthet NetBenefits, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare återgivning NetBenefits](#create-a-fidelity-netbenefits-test-user)**  – du har en motsvarighet för Britta Simon i återgivning NetBenefits som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för återgivning NetBenefits.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med exakthet NetBenefits:**

1. I Azure-portalen på den **exakthet NetBenefits** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_samlbase.png)

3. På den **exakthet NetBenefits domän och URL: er** avsnittet, utför följande steg:

    ![Återgivning NetBenefits domän och URL: er med enkel inloggning för information](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_url.png)

    a. I den **identifierare** textrutan anger du ett URL:

    För testning miljö:  `urn:sp:fidelity:geninbndnbparts20:uat:xq1`

    För produktionsmiljö:  `urn:sp:fidelity:geninbndnbparts20`

    b. I den **svars-URL** textrutan, ange en URL som ska tillhandahållas av återgivning vid tidpunkten för implementering eller kontakta din tilldelade återgivning Client Service Manager.

4. Återgivning NetBenefits program som förväntar SAML-intyg i ett visst format. Vi har mappat den **användaridentifierare** med den **user.userprincipalname**. Du kan mappa detta med **employeeid** eller andra krav som gäller för din organisation som **användaridentifierare**. I följande skärmbild visas bara ett exempel för detta.

    ![Återgivning NetBenefits attribut](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_attribute.png)

    >[!Note]
    >Återgivning NetBenefits stöd för statisk och dynamisk Federation. Statisk innebär att det inte kommer att använda SAML-baserad precis i tid användaren etablering och dynamiska gör att den stöder just-in-time användaretablering. För att använda JIT baserat etablering kunder som har att lägga till lite mer anspråk i Azure AD, som användarens födelsedatum etc. Informationen tillhandahålls av den dina tilldelade **exakthet Client Service Manager** och de måste aktivera den här dynamiska federation för din instans.

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_certificate.png)

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/fidelitynetbenefits-tutorial/tutorial_general_400.png)

7. På den **exakthet NetBenefits Configuration** klickar du på **konfigurera återgivning NetBenefits** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Återgivning NetBenefits konfiguration](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_configure.png)

8. Att konfigurera enkel inloggning på **exakthet NetBenefits** sida, som du behöver skicka de hämtade **XML-Metadata för**, **SAML enkel inloggning för tjänst-URL** och  **SAML entitets-ID** till **tilldelade återgivning klienten Service Manager**. De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/fidelitynetbenefits-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/fidelitynetbenefits-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/fidelitynetbenefits-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/fidelitynetbenefits-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-fidelity-netbenefits-test-user"></a>Skapa en återgivning NetBenefits testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i återgivning NetBenefits. Om du skapar statiska federation, kan du prata med din tilldelade **exakthet Client Service Manager** att skapa användare i återgivning NetBenefits-plattformen. Dessa användare måste skapas och aktiveras innan du använder enkel inloggning.

För dynamiska Federation skapas användare med Just In Time användaretablering. För att använda JIT baserat etablering kunder som har att lägga till lite mer anspråk i Azure AD, som användarens födelsedatum etc. Informationen tillhandahålls av den dina tilldelade **exakthet Client Service Manager** och de måste aktivera den här dynamiska federation för din instans.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till återgivning NetBenefits.

![Tilldela rollen][200]

**Om du vill tilldela återgivning NetBenefits Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **exakthet NetBenefits**.

    ![Länken återgivning NetBenefits i listan med program](./media/fidelitynetbenefits-tutorial/tutorial_fidelitynetbenefits_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen återgivning NetBenefits i åtkomstpanelen du bör få automatiskt loggat in på ditt återgivning NetBenefits program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fidelitynetbenefits-tutorial/tutorial_general_01.png
[2]: ./media/fidelitynetbenefits-tutorial/tutorial_general_02.png
[3]: ./media/fidelitynetbenefits-tutorial/tutorial_general_03.png
[4]: ./media/fidelitynetbenefits-tutorial/tutorial_general_04.png

[100]: ./media/fidelitynetbenefits-tutorial/tutorial_general_100.png

[200]: ./media/fidelitynetbenefits-tutorial/tutorial_general_200.png
[201]: ./media/fidelitynetbenefits-tutorial/tutorial_general_201.png
[202]: ./media/fidelitynetbenefits-tutorial/tutorial_general_202.png
[203]: ./media/fidelitynetbenefits-tutorial/tutorial_general_203.png