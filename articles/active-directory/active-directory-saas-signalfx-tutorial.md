---
title: 'Självstudier: Azure Active Directory-integrering med SignalFx | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 67e9bb69e1e8741e4a4ff7e7b78cb06856d2450f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-signalfx"></a>Självstudier: Azure Active Directory-integrering med SignalFx

I kursen får lära du att integrera SignalFx med Azure Active Directory (AD Azure).

Integrera SignalFx med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SignalFx.
- Du kan aktivera användarna att automatiskt hämta loggat in på SignalFx (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SignalFx, behöver du följande:

- En Azure AD-prenumeration
- En SignalFx enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SignalFx från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-signalfx-from-the-gallery"></a>Att lägga till SignalFx från galleriet
Du måste lägga till SignalFx från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SignalFx i Azure AD.

**Utför följande steg för att lägga till SignalFx från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SignalFx**väljer **SignalFx** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SignalFx i resultatlistan](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SignalFx baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SignalFx motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SignalFx upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med SignalFx, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SignalFx](#create-a-signalfx-test-user)**  – du har en motsvarighet för Britta Simon i SignalFx som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SignalFx program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SignalFx:**

1. I Azure-portalen på den **SignalFx** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_samlbase.png)

3. På den **SignalFx domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och SignalFx domän med enkel inloggning information](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_url.png)

    a. I den **identifierare** textruta, ange ett URL-Adressen: `https://api.signalfx.com/v1/saml/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE] 
    > Föregående värde är inte verkliga värde. Du uppdaterar värdet med faktiska Reply-URL, vilket beskrivs senare i självstudierna.

4. SignalFx program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.   

    ![Konfigurera enkel inloggning](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_attribute.png)

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | User.FirstName          | User.givenName |
    | User.email          | User.Mail |
    | PersonImmutableID       | User.userPrincipalName    |
    | User.LastName       | User.surname    |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/active-directory-saas-signalfx-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.
 
6. På den **SAML-signeringscertifikat** avsnittet, utför följande steg: 

    ![Länken hämta certifikatet](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_certificate.png)

    a. Klicka på kopieringsknappen för att kopiera **webbadress Federation Metadata** och klistra in den i anteckningar.

    b. Klicka på **Certificate(Base64)** och spara certifikatfilen på datorn.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-signalfx-tutorial/tutorial_general_400.png)

8. På den **SignalFx Configuration** klickar du på **konfigurera SignalFx** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![SignalFx konfiguration](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_configure.png) 

9. Inloggning på webbplatsen SignalFx företag som administratör.

10. I SignalFx övre klickar du på **integreringar** att öppna sidan integreringar.

    ![SignalFx integrering](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_intg.png)

11. Klicka på **Azure Active Directory** panelen **inloggningstjänster** avsnitt.
 
    ![SignalFx saml](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_saml.png)

12. Klicka på **ny INTEGRATION** och under den **installera** fliken utför följande steg:
 
    ![SignalFx samlintgpage](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_azure.png)

    a. I den **namn** textruta typ, ett nytt namn i integration som **OurOrgName SAML SSO**.

    b. Kopiera den **integrering ID** värde och lägga till i den **Reply URL** som `https://api.signalfx.com/v1/saml/acs/<integration ID>` i den **Reply URL** textruta för **SignalFx domän och URL: er** avsnitt i Azure-portalen.

    c. Klicka på **överför filen** att överföra den **Base64-kodat certifikat** hämtas från Azure-portalen i den **certifikat** textruta.

    d. I den **utfärdar-URL** textruta klistra in värdet för **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    e. I den **URL för tjänstmetadata** textruta klistra in den **webbadress Federation Metadata** som du har kopierat från Azure-portalen.

    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-signalfx-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-signalfx-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-signalfx-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-signalfx-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-a-signalfx-test-user"></a>Skapa en testanvändare SignalFx

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i SignalFx. SignalFx stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt SignalFx om den inte finns.

När en användare loggar in på SignalFx från SAML SSO för första gången [SignalFx supportteamet](mailto:kmazzola@signalfx.com) skickas ett e-postmeddelande som innehåller en länk som de måste klicka här för att autentisera. Detta sker bara den första gången användaren loggar in; efterföljande inloggningsförsök kräver inte e-verifiering.

>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [SignalFx supportteamet](mailto:kmazzola@signalfx.com)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SignalFx.

![Tilldela rollen][200] 

**Om du vill tilldela SignalFx Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SignalFx**.

    ![Länken SignalFx i listan med program](./media/active-directory-saas-signalfx-tutorial/tutorial_signalfx_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SignalFx på åtkomstpanelen du bör få automatiskt loggat in på ditt SignalFx program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-signalfx-tutorial/tutorial_general_203.png

