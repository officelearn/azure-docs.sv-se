---
title: 'Självstudier: Azure Active Directory-integration med Adobe Creative Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe Creative Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2018
ms.author: jeedes
ms.openlocfilehash: 506f52faf916aa0d5ca2e8587bdbcc16ab88e130
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054292"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Självstudier: Azure Active Directory-integration med Adobe Creative Cloud

I den här självstudien får du lära dig hur du integrerar Adobe Creative Cloud med Azure Active Directory (AD Azure).

Integrera Adobe Creative Cloud med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe Creative Cloud.
- Du kan aktivera användarna att automatiskt få loggat in på Adobe Creative Cloud (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Adobe Creative Cloud, behöver du följande objekt:

- En Azure AD-prenumeration
- Ett Adobe Creative Cloud enkel inloggning aktiverat prenumeration
- Ett Adobe Creative Cloud Enterprise-version som krävs

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Adobe Creative Cloud från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Att lägga till Adobe Creative Cloud från galleriet

Om du vill konfigurera integreringen av Adobe Creative Cloud till Azure AD, som du behöver lägga till Adobe Creative Cloud från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Adobe Creative Cloud från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Adobe Creative Cloud**väljer **Adobe Creative Cloud** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Adobe Creative Cloud i resultatlistan](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Adobe Creative Cloud baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Adobe Creative Cloud till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Adobe Creative Cloud upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Adobe Creative Cloud, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för Adobe Creative Cloud](#create-an-adobe-creative-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Adobe Creative Cloud som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Adobe Creative Cloud.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Adobe Creative Cloud:**

1. I Azure-portalen på den **Adobe Creative Cloud** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning för dialogrutan](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_samlbase.png)

3. På den **Adobe Creative Cloud domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![Adobe Creative Cloud domän och URL: er med enkel inloggning för information](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://www.okta.com/saml2/service-provider/<token>`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare och svars-URL. Kontakta [Adobe Creative Cloud Enterprise](https://www.adobe.com/au/creativecloud/business/teams/plans.html) att hämta dessa värden.

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Adobe Creative Cloud domän och URL: er med enkel inloggning för information](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_url2.png)

    I den **inloggnings-URL** textrutan Ange värdet som: `https://adobe.com`

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_certificate.png)
     
6. Adobe Creative Cloud program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** fliken av programmet. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning](./media/adobe-creative-cloud-tutorial/tutorial_attribute.png)

7. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Attributnamn | Attributvärde |
    | ---------------| ----------------|
    | FirstName |User.givenName |
    | LastName |User.surname |
    | E-post |User.Mail |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/adobe-creative-cloud-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/adobe-creative-cloud-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Klicka på **OK**.

    > [!NOTE]
    > Användare måste ha en giltig ExO för Office 365-licens för e-post anspråksvärde fyllas i SAML-svar.

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/adobe-creative-cloud-tutorial/tutorial_general_400.png)

9. På den **Adobe Creative Cloud konfiguration** klickar du på **Konfigurera Adobe Creative Cloud** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet**.

    ![Adobe Creative Cloud-konfiguration](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_configure.png)

10. I ett annat webbläsarfönster, loggar in [Adobe-administratörskonsolen](https://adminconsole.adobe.com) som administratör.

11. Gå till **inställningar** i det övre navigeringsfältet och väljer sedan **identitet**. Listan över domäner öppnas. Klicka på **konfigurera** länken mot din domän. Utför följande steg på **enkel inloggning på konfiguration krävs** avsnittet. Mer information finns i [installera en domän](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Inställningar för](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "inställningar")

    a. Klicka på **Bläddra** att ladda upp det nedladdade certifikatet från Azure AD för att **IDP certifikat**.

    b. I den **IDP: N utfärdar** textrutan anger du värdet för **SAML entitets-Id** som du kopierade från **konfigurera inloggning** avsnitt i Azure-portalen.

    c. I den **inloggnings-URL för IDP: N** textrutan anger du värdet för **tjänst-URL för SAML SSO** som du kopierade från **konfigurera inloggning** avsnitt i Azure-portalen.

    d. Välj **HTTP - omdirigering** som **IDP-bindning**.

    e. Välj **e-postadress** som **inloggning Användarinställning**.

    f. Klicka på **spara** knappen.

12. Instrumentpanelen kommer nu att presentera XML **”hämta Metadata”** fil. Den innehåller Adobes EntityDescriptor Webbadressen och Webbadressen för AssertionConsumerService. Öppna filen och konfigurera dem i Azure AD-programmet.

    ![Konfigurera enkel inloggning på App-sida](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Använd värdet EntityDescriptor Adobe försett dig för **identifierare** på den **konfigurera Appinställningar** dialogrutan.

    b. Använd värdet AssertionConsumerService Adobe försett dig för **svars-URL** på den **konfigurera Appinställningar** dialogrutan.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/adobe-creative-cloud-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/adobe-creative-cloud-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/adobe-creative-cloud-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/adobe-creative-cloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-adobe-creative-cloud-test-user"></a>Skapa en testanvändare för Adobe Creative Cloud

För att aktivera Azure AD-användare att logga in på Adobe Creative Cloud, måste de etableras i Adobe Creative Cloud. När det gäller Adobe Creative Cloud är etablering en manuell aktivitet.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>Utför följande steg för att tillhandahålla en användarkonton:

1. Logga in på [Adobe-administratörskonsolen](https://adminconsole.adobe.com) plats som administratör.

2. Lägga till användaren i Adobe-konsolen som federerade ID och tilldela dem till en produkt-profil. Detaljerad information om att lägga till användare finns i [lägga till användare i administratörskonsolen för Adobe](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Nu ange din e-post/upn i Adobe inloggning från formuläret genom att trycka på fliken, och du bör vara federerat tillbaka till Azure AD:
    * Webbåtkomst: www.adobe.com > Logga in
    * I verktyget skrivbordsapp > Logga in
    * I programmet > Hjälp > Logga in

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Adobe Creative Cloud.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Adobe Creative Cloud, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Adobe Creative Cloud**.

    ![Adobe Creative Cloud-länk i listan med program](./media/adobe-creative-cloud-tutorial/tutorial_adobecreativecloud_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Adobe Creative Cloud i åtkomstpanelen du bör få automatiskt loggat in på ditt Adobe Creative Cloud-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera en domän (adobe.com)](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
* [Konfigurera Azure för användning med Adobe SSO (adobe.com)](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)

<!--Image references-->

[1]: ./media/adobe-creative-cloud-tutorial/tutorial_general_01.png
[2]: ./media/adobe-creative-cloud-tutorial/tutorial_general_02.png
[3]: ./media/adobe-creative-cloud-tutorial/tutorial_general_03.png
[4]: ./media/adobe-creative-cloud-tutorial/tutorial_general_04.png

[100]: ./media/adobe-creative-cloud-tutorial/tutorial_general_100.png

[200]: ./media/adobe-creative-cloud-tutorial/tutorial_general_200.png
[201]: ./media/adobe-creative-cloud-tutorial/tutorial_general_201.png
[202]: ./media/adobe-creative-cloud-tutorial/tutorial_general_202.png
[203]: ./media/adobe-creative-cloud-tutorial/tutorial_general_203.png
