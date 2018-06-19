---
title: 'Självstudier: Azure Active Directory-integrering med SAML SSO för Bitbucket resolution GmbH | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO för Bitbucket resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: fc947df1-f24e-43ae-9a34-518293583d69
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: jeedes
ms.openlocfilehash: 91767eb4aa93d7bf59cc263e2f2390ab6e49070e
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966561"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Självstudier: Azure Active Directory-integrering med SAML SSO för Bitbucket resolution GmbH

I kursen får lära du att integrera SAML SSO för Bitbucket resolution GmbH med Azure Active Directory (AD Azure).

Integrera SAML SSO för Bitbucket resolution GmbH med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAML SSO för Bitbucket resolution GmbH.
- Du kan aktivera användarna att automatiskt hämta loggat in på SAML SSO för Bitbucket resolution GmbH (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med SAML SSO för Bitbucket resolution GmbH, behöver du följande:

- En Azure AD-prenumeration
- En SAML SSO för Bitbucket av upplösning GmbH enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAML SSO för Bitbucket resolution GmbH från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Att lägga till SAML SSO för Bitbucket resolution GmbH från galleriet
Du måste lägga till SAML SSO för Bitbucket resolution GmbH från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAML SSO för Bitbucket resolution GmbH i Azure AD.

**Utför följande steg för att lägga till SAML SSO för Bitbucket resolution GmbH från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAML SSO för Bitbucket resolution GmbH**väljer **SAML SSO för Bitbucket resolution GmbH** resultatet-panelen klickar **Lägg till** för att lägga till den programmet.

    ![SAML SSO för Bitbucket resolution GmbH i resultatlistan](./media/bitbucket-tutorial/tutorial_bitbucket_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAML SSO för Bitbucket resolution GmbH baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i SAML SSO för Bitbucket resolution GmbH är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAML SSO för Bitbucket resolution GmbH måste upprättas.

SAML SSO för Bitbucket resolution GmbH, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAML SSO för Bitbucket resolution GmbH, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en SAML SSO för Bitbucket av upplösning GmbH testanvändare](#create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)**  – du har en motsvarighet för Britta Simon SAML SSO för Bitbucket resolution GmbH som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din SAML SSO för Bitbucket resolution GmbH program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAML SSO för Bitbucket resolution GmbH:**

1. I Azure-portalen på den **SAML SSO för Bitbucket resolution GmbH** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/bitbucket-tutorial/tutorial_bitbucket_samlbase.png)

3. På den **SAML SSO Bitbucket resolution GmbH domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP initierade läge:

    ![URL: er och SAML SSO för Bitbucket resolution GmbH domän med enkel inloggning information](./media/bitbucket-tutorial/tutorial_bitbucket_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och SAML SSO för Bitbucket resolution GmbH domän med enkel inloggning information](./media/bitbucket-tutorial/tutorial_bitbucket_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [SAML SSO för Bitbucket resolution GmbH klienten supportteam](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/bitbucket-tutorial/tutorial_bitbucket_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/bitbucket-tutorial/tutorial_general_400.png)
    
7. Inloggning till SAML SSO för Bitbucket av upplösning GmbH företagets plats som administratör.

8. Klicka till höger i verktygsfältet på **inställningar**.

9. Gå till kontoavsnittet, klicka på **SAML SingleSignOn** på menyraden.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

10. På den **Plugin konfigurationssidan för SAML-SIngleSignOn**, klickar du på **lägga till idp**. 

    ![Den lägger till idp](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

11. På den **Välj SAML-identitetsprovider** sidan utför följande steg:

    ![Identitetsleverantören.](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Välj **Idp typen** som **AZURE AD**.

    b. I den **namn** textruta skriver du namnet.

    c. I den **beskrivning** textruta, ange en beskrivning.

    d. Klicka på **Nästa**.

12. På den **identitet konfigurationssidan för providern**, klickar du på **nästa**.

    ![Identity-konfiguration](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

13.  På den **importera SAML Idp Metadata** , klickar du på **Läs in fil** att överföra den **XML-METADATA för** -fil som du har hämtat från Azure-portalen.

    ![Idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)
    
14. Klicka på **Nästa**.

15. Klicka på **Spara inställningar**.

    ![Spara](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/bitbucket-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/bitbucket-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/bitbucket-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/bitbucket-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Skapa en SAML SSO för Bitbucket av upplösning GmbH testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon SAML SSO för Bitbucket av upplösning GmbH. SAML SSO Bitbucket resolution GmbH stöder just-in-time-etablering och även användare kan skapas manuellt, kontakta [SAML SSO för Bitbucket resolution GmbH klienten supportteam](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support) enligt dina behov.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAML SSO för Bitbucket resolution GmbH.

![Tilldela rollen][200] 

**Om du vill tilldela SAML SSO för Bitbucket Britta Simon resolution GmbH, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAML SSO för Bitbucket resolution GmbH**.

    ![SAML SSO för Bitbucket av upplösning GmbH länken i listan med program](./media/bitbucket-tutorial/tutorial_bitbucket_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på SAML SSO för Bitbucket av upplösning GmbH panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt SAML SSO för Bitbucket resolution GmbH program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

