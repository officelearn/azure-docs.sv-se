---
title: "Självstudier: Azure Active Directory-integrering med Evernote | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Evernote."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: be94152a84bbbeacb623d7dd8b540e3981931a8e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-evernote"></a>Självstudier: Azure Active Directory-integrering med Evernote

I kursen får lära du att integrera Evernote med Azure Active Directory (AD Azure).

Integrera Evernote med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Evernote.
- Du kan aktivera användarna att automatiskt hämta loggat in på Evernote (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Evernote, behöver du följande:

- En Azure AD-prenumeration
- En Evernote enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Evernote från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-evernote-from-the-gallery"></a>Att lägga till Evernote från galleriet
Du måste lägga till Evernote från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Evernote i Azure AD.

**Utför följande steg för att lägga till Evernote från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Evernote**väljer **Evernote** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Evernote i resultatlistan](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Evernote baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Evernote motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Evernote upprättas.

I Evernote, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Evernote, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Evernote](#create-an-evernote-test-user)**  – du har en motsvarighet för Britta Simon i Evernote som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Evernote program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Evernote:**

1. I Azure-portalen på den **Evernote** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_samlbase.png)

3. På den **Evernote domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP initierade läge:

    ![URL: er och Evernote domän med enkel inloggning information](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url.png)

    I den **identifierare** textruta anger du URL:`https://www.evernote.com/saml2`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Evernote domän med enkel inloggning information](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_url1.png)

    I den **inloggning URL** textruta anger du URL:`https://www.evernote.com/Login.action`   

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-evernote-tutorial/tutorial_general_400.png)

7. På den **Evernote Configuration** klickar du på **konfigurera Evernote** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Evernote konfiguration](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_configure.png) 

8. Logga in på webbplatsen Evernote företag som en administratör i en annan webbläsarfönster.

9. Gå till **Admin Console**

    ![Administrationskonsolen](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

10. Från den **Admin Console**, gå till **”säkerhet”** och välj **' Single Sign-On-**

    ![SSO-inställning](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_sso.png)

11. Konfigurera följande värden:

    ![Certifikat-inställning](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Aktivera enkel inloggning:** enkel inloggning är aktiverad som standard (klicka på **inaktivera enkel inloggning** att ta bort kravet SSO)

    b. Klistra in **SAML enkel inloggning Tjänstwebbadress** -värde som du har kopierat från Azure-portalen i den **SAML HTTP-begäran URL** textruta.

    c. Öppna hämtade certifikatet från Azure AD i en anteckningar och kopiera innehållet inklusive ”BEGIN CERTIFICATE” och ”END CERTIFICATE” och klistrar in det i den **X.509-certifikat** textruta. 

    d.Click **spara ändringar**

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-evernote-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-evernote-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-evernote-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-evernote-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-evernote-test-user"></a>Skapa en testanvändare Evernote

För att aktivera Azure AD-användare att logga in på Evernote etableras de i Evernote.  
När det gäller Evernote är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på webbplatsen Evernote företag som administratör.

2. Klicka på den **Admin Console**.

    ![Administrationskonsolen](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Från den **Admin Console**, gå till **'Lägg till användare ”**.

    ![Lägg till testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0001.png)

4. **Lägg till teammedlemmar** i den **e-post** textruta Skriv e-postadressen för användarkontot och klicka på **bjuda in.**

    ![Lägg till testUser](./media/active-directory-saas-evernote-tutorial/create_aaduser_0002.png)
    
5. När inbjudan har skickats får kontoinnehavaren Azure Active Directory ett e-postmeddelande för att tacka ja till inbjudan.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Evernote.

![Tilldela rollen][200] 

**Om du vill tilldela Evernote Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Evernote**.

    ![Länken Evernote i listan med program](./media/active-directory-saas-evernote-tutorial/tutorial_evernote_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Evernote på åtkomstpanelen du ska hämta loggat in på ditt Evernote program. Du måste logga in som en organisation konto men sedan måste du logga in med ditt eget konto. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-evernote-tutorial/tutorial_general_203.png

