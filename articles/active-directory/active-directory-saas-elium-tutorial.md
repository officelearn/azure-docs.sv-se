---
title: 'Självstudier: Azure Active Directory-integrering med Elium | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: jeedes
ms.openlocfilehash: b64f4153908668117ff07b20701c834ce66d3c46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34344059"
---
# <a name="tutorial-azure-active-directory-integration-with-elium"></a>Självstudier: Azure Active Directory-integrering med Elium

I kursen får lära du att integrera Elium med Azure Active Directory (AD Azure).

Integrera Elium med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Elium.
- Du kan aktivera användarna att automatiskt hämta loggat in på Elium (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Elium, behöver du följande:

- En Azure AD-prenumeration
- En Elium enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Elium från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-elium-from-the-gallery"></a>Att lägga till Elium från galleriet
Du måste lägga till Elium från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Elium i Azure AD.

**Utför följande steg för att lägga till Elium från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Elium**väljer **Elium** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Elium i resultatlistan](./media/active-directory-saas-elium-tutorial/tutorial_elium_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Elium baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Elium motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Elium upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Elium, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Elium](#create-an-elium-test-user)**  – du har en motsvarighet för Britta Simon i Elium som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Elium program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Elium:**

1. I Azure-portalen på den **Elium** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-elium-tutorial/tutorial_elium_samlbase.png)

3. På den **Elium domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Elium domän med enkel inloggning information](./media/active-directory-saas-elium-tutorial/tutorial_elium_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<platform-domain>.elium.com/login/saml2/acs`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Elium domän med enkel inloggning information](./media/active-directory-saas-elium-tutorial/tutorial_elium_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: ` https://<platform-domain>.elium.com/login/saml2/login`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Du får dessa värden från den **SP metadatafil** laddas ner `https://<platform-domain>.elium.com/login/saml2/metadata`, som beskrivs senare i den här kursen.

5. Programmet Elium förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/tutorial_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
           
    | Attributnamn | Attributvärde |   
    | ---------------| ----------------|
    | e-post   |User.Mail |
    | Förnamn| User.givenName |
    | Efternamn| User.surname|
    | befattning| User.jobtitle|
    | Företag| User.CompanyName|
    
    > [!NOTE]
    > Det här är standard-anspråk. **Endast e-anspråk krävs**. Anspråk är obligatoriskt för JIT också etablering endast e-post. Andra anpassade anspråk kan variera från en kund plattform för en annan kund-plattformen.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/tutorial_attribute_04.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/tutorial_attribute_05.png)

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna namnområde som tomt.
    
    e. Klicka på **OK**. 

5. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-elium-tutorial/tutorial_elium_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-elium-tutorial/tutorial_general_400.png)
    
7. I en annan webbläsarfönster loggar du in på webbplatsen Elium företag som administratör.

8. Klicka på den **användarprofil** övre högra hörnet och välj sedan **Administration**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/user1.png)

9. Välj **säkerhet** fliken.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/user2.png)

10. Rulla ned till den **enkel inloggning (SSO)** avsnittet och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/user3.png)

    a. Kopiera värdet för **Kontrollera SAML2 autentiseringen fungerar med ditt konto** och klistra in den i den **inloggnings-URL** textruta på den **Elium domän och URL: er** avsnitt i Azure portalen.

    > [!NOTE]
    > När du har konfigurerat SSO, kan du alltid åtkomst till sidan fjärrinloggning på följande URL: `https://<platform_domain>/login/regular/login` 

    b. Välj **aktivera SAML2 federation** kryssrutan.

    c. Välj **JIT etablering** kryssrutan.

    d. Öppna den **SP Metadata** genom att klicka på den **hämta** knappen.

    e. Sök efter den **ID för entiteterna** i den **SP Metadata** fil, kopiera den **ID för entiteterna** värdet och klistrar in den i den **identifierare** textruta på  **URL: er och Elium domän** avsnitt i Azure-portalen. 

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/user4.png)

    f. Sök efter den **AssertionConsumerService** i den **SP Metadata** fil, kopiera den **plats** värdet och klistrar in den i den **Reply URL** textruta på den **Elium domän och URL: er** avsnitt i Azure-portalen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-elium-tutorial/user5.png)

    g. Öppna metadatafilen hämtas från Azure-portalen i anteckningar, kopiera innehållet och klistrar in det i den **IdP Metadata** textruta.

    h. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-elium-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-elium-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-elium-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-elium-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-elium-test-user"></a>Skapa en testanvändare Elium

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Elium. Elium stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Elium om den inte finns.
>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [Elium supportteamet](mailto:support@elium.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Elium.

![Tilldela rollen][200] 

**Om du vill tilldela Elium Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Elium**.

    ![Länken Elium i listan med program](./media/active-directory-saas-elium-tutorial/tutorial_elium_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Elium på åtkomstpanelen du bör få automatiskt loggat in på ditt Elium program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-elium-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-elium-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-elium-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-elium-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-elium-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-elium-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-elium-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-elium-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-elium-tutorial/tutorial_general_203.png

