---
title: 'Självstudier: Azure Active Directory-integrering med tydliga granska | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och rensa granskning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: jeedes
ms.openlocfilehash: aa3514d348b812a96b55c4d47950cd064d391dc1
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231550"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Självstudier: Azure Active Directory-integrering med rensa granskning

I kursen får lära du att integrera Rensa granska med Azure Active Directory (AD Azure).

Rensa granska integrera med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till rensa granskning.
- Du kan aktivera användarna att automatiskt hämta loggat in på Rensa granska (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med rensa granskning, behöver du följande:

- En Azure AD-prenumeration
- En tydlig granska enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Rensa granska från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-clear-review-from-the-gallery"></a>Att lägga till Rensa granska från galleriet
Du måste lägga till Rensa granska från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av rensa granskning i Azure AD.

**Utför följande steg för att lägga till Rensa granska från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Rensa granska**väljer **Rensa granska** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Rensa granskning i resultatlistan](./media/clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med tydliga granska baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i rensa granskning för en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i rensa granskning upprättas.

I rensa granskning, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med rensa granskning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Rensa granska](#create-a-clear-review-test-user)**  – du har en motsvarighet för Britta Simon i rensa granskning som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet rensa granskning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med rensa granskning:**

1. I Azure-portalen på den **Rensa granska** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. På den **URL: er och avmarkera granska domän** avsnittet, utför följande steg om du vill konfigurera programmet i **IdP initierade** läge:

    ![Tydliga URL: er och granska domän med enkel inloggning information](./media/clearreview-tutorial/tutorial_clearreview_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<customer name>.clearreview.com/sso/metadata/`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<customer name>.clearreview.com/sso/acs/`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Tydliga URL: er och granska domän med enkel inloggning information](./media/clearreview-tutorial/tutorial_clearreview_url_sp.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare Reply-URL. Kontakta [Rensa granska supportteamet](https://clearreview.com/contact/) att hämta dessa värden.

5. Rensa granska programmet förväntat identifierarvärde unik användare i namnidentifierare anspråket. Du ska mappa användare identifierarvärde till **user.mail**.

    ![Avsnittet attribut](./media/clearreview-tutorial/attribute.png)


6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/clearreview-tutorial/tutorial_clearreview_certificate.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/clearreview-tutorial/tutorial_general_400.png)

8. På den **Rensa Granska konfigurationen** klickar du på **konfigurera Rensa granska** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Rensa Granska konfigurationen](./media/clearreview-tutorial/tutorial_clearreview_configure.png) 

9. Konfigurera enkel inloggning på **Rensa granska** sida, öppna den **Rensa granska** Företagsportalen med administratörsautentiseringsuppgifter.

10. Välj **Admin** från det vänstra navigeringsfönstret.

    ![Konfigurera enkel inloggning spara](./media/clearreview-tutorial/tutorial_clearreview_app_admin1.png)

11. Välj **ändra** längst ned på sidan.

    ![Konfigurera enkel inloggning spara](./media/clearreview-tutorial/tutorial_clearreview_app_admin2.png)

12. Utför följande steg **inställningar för enkel inloggning** sidan

    ![Konfigurera enkel inloggning spara](./media/clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. I den **utfärdar-URL** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    b. I den **SAML Endpoint** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.    

    c. I den **Servicenivåmål Endpoint** textruta klistra in värdet för **inloggning Tjänstwebbadress** som du har kopierat från Azure-portalen. 

    d. Öppna hämtade certifikatet i anteckningar och klistra in innehållet i den **X.509-certifikat** textruta.   

13. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/clearreview-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/clearreview-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/clearreview-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/clearreview-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-clear-review-test-user"></a>Skapa en tydlig granska testanvändare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i rensa granskning. Se tillsammans med [Rensa granska supportteamet](https://clearreview.com/contact/) att lägga till användare i Rensa granska-plattformen.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till rensa granskning.

![Tilldela rollen][200] 

**Om du vill tilldela Rensa granska Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Rensa granska**.

    ![Rensa granska länken i listan med program](./media/clearreview-tutorial/tutorial_clearreview_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Rensa granskning på åtkomstpanelen du bör få automatiskt loggat in på Rensa granska programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/clearreview-tutorial/tutorial_general_01.png
[2]: ./media/clearreview-tutorial/tutorial_general_02.png
[3]: ./media/clearreview-tutorial/tutorial_general_03.png
[4]: ./media/clearreview-tutorial/tutorial_general_04.png

[100]: ./media/clearreview-tutorial/tutorial_general_100.png

[200]: ./media/clearreview-tutorial/tutorial_general_200.png
[201]: ./media/clearreview-tutorial/tutorial_general_201.png
[202]: ./media/clearreview-tutorial/tutorial_general_202.png
[203]: ./media/clearreview-tutorial/tutorial_general_203.png
