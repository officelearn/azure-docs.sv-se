---
title: 'Självstudier: Azure Active Directory-integration med SAML SSO för Bitbucket resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för Bitbucket resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 21b6adb98fdb973b75aa1d6db519777bab730d73
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048131"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Självstudier: Azure Active Directory-integration med SAML SSO för Bitbucket resolution GmbH

I den här självstudien får du lära dig hur du integrerar SAML SSO för Bitbucket resolution GmbH med Azure Active Directory (AD Azure).

Integrera SAML SSO för Bitbucket resolution GmbH med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAML SSO för Bitbucket resolution GmbH.
- Du kan aktivera användarna att automatiskt få loggat in på SAML SSO för Bitbucket resolution GmbH (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAML SSO för Bitbucket resolution GmbH, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAML SSO för Bitbucket av upplösning GmbH, enkel inloggning på aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAML SSO för Bitbucket resolution GmbH från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Att lägga till SAML SSO för Bitbucket resolution GmbH från galleriet
För att konfigurera integrering av SAML SSO för Bitbucket resolution GmbH i Azure AD, som du behöver lägga till SAML SSO för Bitbucket resolution GmbH från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till SAML SSO för Bitbucket resolution GmbH från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAML SSO för Bitbucket resolution GmbH**väljer **SAML SSO för Bitbucket resolution GmbH** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![SAML SSO för Bitbucket av lösningen GmbH i listan med resultat](./media/bitbucket-tutorial/tutorial_bitbucket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAML SSO för Bitbucket resolution GmbH baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i SAML SSO för Bitbucket resolution GmbH är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i SAML SSO för Bitbucket resolution GmbH måste upprättas.

I SAML SSO för Bitbucket resolution GmbH, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAML SSO för Bitbucket resolution GmbH, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en SAML SSO för Bitbucket av lösning GmbH testanvändare](#create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**  – du har en motsvarighet för Britta Simon i SAML SSO för Bitbucket resolution GmbH som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAML SSO för Bitbucket resolution GmbH program.

**Om du vill konfigurera Azure AD enkel inloggning med SAML SSO för Bitbucket resolution GmbH, utför du följande steg:**

1. I Azure-portalen på den **SAML SSO för Bitbucket resolution GmbH** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/bitbucket-tutorial/tutorial_bitbucket_samlbase.png)

3. På den **SAML SSO Bitbucket resolution GmbH domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![SAML SSO för Bitbucket resolution GmbH domän och URL: er med enkel inloggning för information](./media/bitbucket-tutorial/tutorial_bitbucket_url.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![SAML SSO för Bitbucket resolution GmbH domän och URL: er med enkel inloggning för information](./media/bitbucket-tutorial/tutorial_bitbucket_url1.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Kontakta [SAML SSO för Bitbucket resolution GmbH klienten supportteam](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/bitbucket-tutorial/tutorial_bitbucket_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/bitbucket-tutorial/tutorial_general_400.png)
    
7. Inloggning till SAML SSO för Bitbucket av lösning GmbH företagets plats som administratör.

8. Till höger i verktygsfältet klickar du på **inställningar**.

9. Gå till avsnittet om konton, klickar du på **SAML SingleSignOn** på menyraden.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

10. På den **konfigurationssidan för SAML SIngleSignOn plugin-programmet**, klickar du på **lägga till idp**. 

    ![Den lägger till idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

11. På den **väljer SAML-identitetsprovider** kan utföra följande steg:

    ![Identitetsprovidern](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Välj **Idp typ** som **AZURE AD**.

    b. I den **namn** textrutan skriver du namnet.

    c. I den **beskrivning** textrutan anger du beskrivningen.

    d. Klicka på **Nästa**.

12. På den **konfigurationssidan för identitets-provider**, klickar du på **nästa**.

    ![Identity-config](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

13.  På den **importera SAML Idp Metadata** , klickar du på **Läs in fil** att ladda upp den **XML-METADATA för** fil som du har hämtat från Azure-portalen.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
14. Klicka på **Nästa**.

15. Klicka på **spara inställningarna för**.

    ![Spara](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/bitbucket-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/bitbucket-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/bitbucket-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/bitbucket-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Skapa en SAML SSO för Bitbucket av lösning GmbH testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i SAML SSO för Bitbucket resolution GmbH. SAML SSO för Bitbucket resolution GmbH har stöd för just-in-time-etablering och även användare kan skapas manuellt, kontakta [SAML SSO för Bitbucket resolution GmbH klienten supportteam](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) enligt dina behov.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAML SSO för Bitbucket resolution GmbH.

![Tilldela rollen][200] 

**Om du vill tilldela SAML SSO för Bitbucket Britta Simon resolution GmbH, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAML SSO för Bitbucket resolution GmbH**.

    ![SAML SSO för Bitbucket per lösning GmbH länk i listan med program](./media/bitbucket-tutorial/tutorial_bitbucket_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SAML SSO för Bitbucket av lösning GmbH panel i åtkomstpanelen du bör få automatiskt loggat in på ditt SAML SSO för Bitbucket resolution GmbH program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitbucket-tutorial/tutorial_general_01.png
[2]: ./media/bitbucket-tutorial/tutorial_general_02.png
[3]: ./media/bitbucket-tutorial/tutorial_general_03.png
[4]: ./media/bitbucket-tutorial/tutorial_general_04.png

[100]: ./media/bitbucket-tutorial/tutorial_general_100.png

[200]: ./media/bitbucket-tutorial/tutorial_general_200.png
[201]: ./media/bitbucket-tutorial/tutorial_general_201.png
[202]: ./media/bitbucket-tutorial/tutorial_general_202.png
[203]: ./media/bitbucket-tutorial/tutorial_general_203.png

