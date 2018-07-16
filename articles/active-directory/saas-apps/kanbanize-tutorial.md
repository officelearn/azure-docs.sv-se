---
title: 'Självstudier: Azure Active Directory-integration med Kanbanize | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kanbanize.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b436d2f6-bfa5-43fd-a8f9-d2144dc25669
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.openlocfilehash: 746eaadcdb9a588087367c4c70237922cf0f14bf
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059601"
---
# <a name="tutorial-azure-active-directory-integration-with-kanbanize"></a>Självstudier: Azure Active Directory-integration med Kanbanize

I den här självstudien får du lära dig hur du integrerar Kanbanize med Azure Active Directory (AD Azure).

Integrera Kanbanize med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Kanbanize.
- Du kan aktivera användarna att automatiskt få loggat in på Kanbanize (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Kanbanize, behöver du följande objekt:

- En Azure AD-prenumeration
- En Kanbanize enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Kanbanize från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kanbanize-from-the-gallery"></a>Att lägga till Kanbanize från galleriet
För att konfigurera integrering av Kanbanize i Azure AD, som du behöver lägga till Kanbanize från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kanbanize från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Kanbanize**väljer **Kanbanize** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Kanbanize i resultatlistan](./media/kanbanize-tutorial/tutorial_kanbanize_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Kanbanize baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Kanbanize är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Kanbanize upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kanbanize, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Kanbanize](#create-a-kanbanize-test-user)**  – du har en motsvarighet för Britta Simon i Kanbanize som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Kanbanize program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Kanbanize:**

1. I Azure-portalen på den **Kanbanize** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/kanbanize-tutorial/tutorial_kanbanize_samlbase.png)

3. På den **Kanbanize domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Kanbanize domän och URL: er med enkel inloggning för information](./media/kanbanize-tutorial/tutorial_kanbanize_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.kanbanize.com/`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.kanbanize.com/saml/acs`

    c. Kontrollera **visa avancerade URL-inställningar**.

    d.  I den **Vidarebefordransstatus** textrutan anger du ett URL: `/ctrl_login/saml_login`

    e. Om du vill konfigurera programmet i **SP** har initierat läge, **inloggnings-URL** textrutan skriver du en URL med hjälp av följande mönster: `https://<subdomain>.kanbanize.com`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [Kanbanize klienten supportteamet](mailto:support@ms.kanbanize.com) att hämta dessa värden. 

5. Kanbanize program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar ett exempel för detta. Standardvärdet för **användaridentifierare** är **user.userprincipalname** men Kanbanize förväntar sig detta mappas med användarens e-postadress. Som du kan använda **user.mail** attribut i listan eller använder rätt attribut-värde baserat på din organisationskonfiguration
    
    ![Konfigurera enkel inloggning](./media/kanbanize-tutorial/tutorial_Kanbanize_attribute.png)

6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/kanbanize-tutorial/tutorial_kanbanize_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/kanbanize-tutorial/tutorial_general_400.png)
    
8. På den **Kanbanize Configuration** klickar du på **konfigurera Kanbanize** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Kanbanize konfiguration](./media/kanbanize-tutorial/tutorial_kanbanize_configure.png)

9. I ett annat webbläsarfönster, logga in på Kanbanize som en administratör. 

10. Gå till överst till höger på sidan, klickar du på **inställningar** logotyp.

    ![Kanbanize inställningar](./media/kanbanize-tutorial/tutorial_kanbanize_set.png)

11. Klicka på panelen administrationssidan från vänster sida av menyn **integreringar** och sedan aktivera **enkel inloggning**. 

    ![Kanbanize integreringar](./media/kanbanize-tutorial/tutorial_kanbanize_admin.png)

12. Under avsnittet integreringar, klickar du på **konfigurera** att öppna **integrering för enkel inloggning** sidan.

    ![Kanbanize config](./media/kanbanize-tutorial/tutorial_kanbanize_config.png)

13. På den **integrering för enkel inloggning** sidan **konfigurationer**, utför följande steg:

    ![Kanbanize integreringar](./media/kanbanize-tutorial/tutorial_kanbanize_save.png)

    a. I den **Idp entitets-Id** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    b. I den **Idp Inloggningsslutpunkt** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. I den **Idp-slutpunkten för utloggning** textrutan klistra in värdet för **URL: en för utloggning**, som du har kopierat från Azure-portalen.

    d. I **attributets namn för e-post** textrutan anger du det här värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    e. I **attributets namn för förnamn** textrutan anger du det här värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    f. I **attributets namn för efternamn** textrutan anger du det här värdet `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` 
    > [!Note]
    > Du kan hämta dessa värden genom att kombinera värdena för namnområde och namn för respektive attributet från avsnittet användaren attribut i Azure-portalen.

    g. I anteckningar, öppna Base64-kodade certifikatet som du laddade ned från Azure-portalen, kopiera innehållet (utan start- och markörer) och klistra in den i den **Idp X.509-certifikat** box.

    h. Kontrollera **aktivera logga in med både enkel inloggning och Kanbanize**.
    
    i. Klicka på **spara inställningarna för**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/kanbanize-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/kanbanize-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/kanbanize-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/kanbanize-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-kanbanize-test-user"></a>Skapa en Kanbanize testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Kanbanize. Kanbanize stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inga uppgift åt dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Kanbanize om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Kanbanize klienten supportteamet](mailto:support@ms.kanbanize.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kanbanize.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Kanbanize, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Kanbanize**.

    ![Länken Kanbanize i listan med program](./media/kanbanize-tutorial/tutorial_kanbanize_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Kanbanize i åtkomstpanelen du bör få automatiskt loggat in på ditt Kanbanize program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kanbanize-tutorial/tutorial_general_01.png
[2]: ./media/kanbanize-tutorial/tutorial_general_02.png
[3]: ./media/kanbanize-tutorial/tutorial_general_03.png
[4]: ./media/kanbanize-tutorial/tutorial_general_04.png

[100]: ./media/kanbanize-tutorial/tutorial_general_100.png

[200]: ./media/kanbanize-tutorial/tutorial_general_200.png
[201]: ./media/kanbanize-tutorial/tutorial_general_201.png
[202]: ./media/kanbanize-tutorial/tutorial_general_202.png
[203]: ./media/kanbanize-tutorial/tutorial_general_203.png

