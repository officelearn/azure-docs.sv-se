---
title: "Självstudier: Azure Active Directory-integrering med bild WORKS | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AVBILDNINGEN fungerar."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 635d86a1-b512-442d-8851-3b18ec1a24a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jeedes
ms.openlocfilehash: c6aa188336ecc4c374b5dd651a80b57f6f16fb3a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-image-works"></a>Självstudier: Azure Active Directory-integrering med bild fungerar

I kursen får lära du att integrera AVBILDNINGEN fungerar med Azure Active Directory (AD Azure).

Integrera AVBILDNINGEN fungerar med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AVBILDNINGEN fungerar.
- Du kan aktivera användarna att automatiskt hämta loggat in på BILDEN fungerar (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med bild fungerar, behöver du följande:

- En Azure AD-prenumeration
- En bild fungerar enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till BILDEN fungerar från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-image-works-from-the-gallery"></a>Att lägga till BILDEN fungerar från galleriet
Du måste lägga till BILDEN fungerar från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av AVBILDNINGEN fungerar i Azure AD.

**Utför följande steg för att lägga till BILDEN fungerar från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **AVBILDNINGEN fungerar**väljer **AVBILDNINGEN fungerar** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![IMAGE fungerar i resultatlistan](./media/active-directory-saas-imageworks-tutorial/tutorial_imageworks_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med bild fungerar baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i AVBILDNINGEN fungerar för en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i AVBILDNINGEN fungerar upprättas.

I bild fungerar, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med bild fungerar, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en avbildning fungerar testanvändare](#create-a-image-works-test-user)**  – du har en motsvarighet för Britta Simon i AVBILDNINGEN fungerar som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet AVBILDNINGEN fungerar.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bild fungerar:**

1. I Azure-portalen på den **AVBILDNINGEN fungerar** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-imageworks-tutorial/tutorial_imageworks_samlbase.png)

3. På den **AVBILDNINGEN fungerar domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och AVBILDNINGEN fungerar domän med enkel inloggning information](./media/active-directory-saas-imageworks-tutorial/tutorial_imageworks_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://i-imageworks.jp/iw/<tenantName>/sso/Login.do`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://sp.i-imageworks.jp/iw/<tenantName>/postResponse`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [AVBILDNINGEN fungerar klienten supportteamet](mailto:iw-sd-support@fujifilm.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-imageworks-tutorial/tutorial_imageworks_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-imageworks-tutorial/tutorial_general_400.png)

6. På den **AVBILDNINGEN fungerar Configuration** klickar du på **konfigurera AVBILDNINGEN fungerar** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![BILD fungerar konfiguration](./media/active-directory-saas-imageworks-tutorial/tutorial_imageworks_configure.png) 

7. Konfigurera enkel inloggning på **AVBILDNINGEN fungerar** sida, måste du skicka den hämtade **Certificate(Base64), Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** till [BILDEN fungerar supportteam](mailto:iw-sd-support@fujifilm.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-imageworks-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-imageworks-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-imageworks-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-imageworks-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-image-works-test-user"></a>Skapa en avbildning fungerar testanvändare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i AVBILDNINGEN fungerar. Arbeta med [AVBILDNINGEN fungerar supportteam](mailto:iw-sd-support@fujifilm.com) att lägga till användare i AVBILDNINGEN fungerar-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AVBILDNINGEN fungerar.

![Tilldela rollen][200] 

**Om du vill tilldela AVBILDNINGEN fungerar Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **AVBILDNINGEN fungerar**.

    ![Länken fungerar AVBILDNINGEN i listan med program](./media/active-directory-saas-imageworks-tutorial/tutorial_imageworks_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen AVBILDNINGEN fungerar på åtkomstpanelen du ska hämta automatiskt loggat in i tillämpningsprogrammet AVBILDNINGEN fungerar.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-imageworks-tutorial/tutorial_general_203.png

