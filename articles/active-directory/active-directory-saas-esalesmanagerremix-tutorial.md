---
title: "Självstudier: Azure Active Directory-integrering med E försäljning Manager REMIXA | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och E försäljning Manager REMIXA."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: 56c2860b6aeac9fdc66f2b1fdd1ed9126bf77d3f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="tutorial-azure-active-directory-integration-with-e-sales-manager-remix"></a>Självstudier: Azure Active Directory-integrering med E försäljning Manager REMIXA

I kursen får lära du att integrera E försäljning Manager REMIXA med Azure Active Directory (AD Azure).

Integrera E försäljning Manager REMIXA med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till E försäljning Manager REMIXA.
- Du kan aktivera användarna att automatiskt hämta loggat in på E försäljning Manager REMIXA (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med E försäljning Manager REMIXA behöver du följande:

- En Azure AD-prenumeration
- En E försäljning Manager REMIXA enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till E försäljning Manager REMIXA från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-e-sales-manager-remix-from-the-gallery"></a>Att lägga till E försäljning Manager REMIXA från galleriet
Du måste lägga till E försäljning Manager REMIXA från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av E försäljning Manager REMIXA i Azure AD.

**Om du vill lägga till E försäljning Manager REMIXA från galleriet, utför du följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **E försäljning Manager REMIXA**väljer **E försäljning Manager REMIXA** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![E försäljning Manager REMIXA i resultatlistan](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med E försäljning Manager REMIXA baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i E försäljning Manager REMIXA till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i E försäljning Manager REMIXA upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med E försäljning Manager REMIXA, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare E försäljning Manager REMIXA](#create-an-e-sales-manager-remix-test-user)**  – har en motsvarighet för Britta Simon E försäljning Manager REMIXA som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt E försäljning Manager REMIXA program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med E försäljning Manager REMIXA:**

1. I Azure-portalen på den **E försäljning Manager REMIXA** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. På den **REMIXA E försäljning Manager-domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och REMIXA E försäljning Manager-domän med enkel inloggning information](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<Server-Based-URL>/<sub-domain>/esales-pc`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<Server-Based-URL>/<sub-domain>/`

    c. Kopiera den **identifierare** värde i anteckningar. ID-värde används senare i den här kursen.
    
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [REMIXA E försäljning Manager-klienten supportteamet](mailto:esupport@softbrain.co.jp) att hämta dessa värden.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Välj **visa och redigera andra användarattribut** och klicka på **e-postadress** attribut.
    
    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

6. Kopiera den **Namespace** och **namn** från textrutan anspråkets värde. Generera värdet i följande mönster - `<Namespace>/<Name>`. Du kommer att använda det här värdet senare under självstudiekursen.

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. På den **E försäljning Manager REMIXA Configuration** klickar du på **konfigurera E försäljning Manager REMIXA** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

9. Logga in på ditt E försäljning Manager REMIXA program som administratör.

10. Välj **menyn till administratören** på menyn längst upp till höger.

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

11. Välj **systeminställningar**>**samarbete med externa system**

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
12. Välj **SAML**

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

13. I den **inställningen för SAML-autentisering** avsnittet, utför följande steg:

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Välj **PC-version**
    
    b. Välj **e-post** i listrutan i samarbetet objektet avsnitt.

    c. Klistra in i textrutan samarbete objekt i **anspråksvärde** som du har kopierat från Azure portal engångsfaktorautentisering `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. I den **utfärdaren (enhets-ID)** textruta klistra in den **identifierare** värde som du har kopierat från Azure-portalen för **REMIXA E försäljning Manager-domän och URL: er** avsnitt.

    e. Att överföra din hämtade **certifikat** från Azure portal, Välj **filen markeringen**.

    f. I **ID providern inloggnings-URL** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    g. I **identitet providern logga ut URL** textruta klistra in **Sign-Out URL** värde som du har kopierat från Azure-portalen.

    h. Klicka på **inställningen klar**

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-esalesmanagerremix-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Skapa en testanvändare E försäljning Manager REMIXA

1. Logga in på ditt E försäljning Manager REMIXA program som administratör.

2. Välj **menyn till administratören** på menyn längst upp till höger.

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Välj **din Företagsinställningar**>**underhåll av avdelningar och anställda** och välj **medarbetare som registrerat**.

    ![Användaren](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. I den **nya medarbetare registreringen** avsnittet, utför följande steg:
    
    ![Användaren](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. I den **medarbetarens namn** textruta skriver du namnet på användaren som Britta.

    b. Alla respektive obligatoriska fält fylls med användarinformation.
    
    c. Om du aktiverar SAML administratören inte kommer att kunna logga in på inloggningssidan, ge så administratör Inloggningsprivilegier för användaren genom att välja den **inloggning för serveradministratör**

    d. Klicka på **registrering**

5. I framtiden, om du vill logga in som administratör logga in med den användare som har beviljats behörigheten Administratör och klicka på **menyn till administratören** på menyn längst upp till höger.

    ![Användaren](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till E försäljning Manager REMIXA.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon E försäljning Manager REMIXA, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **E försäljning Manager REMIXA**.

    ![Länken E försäljning Manager REMIXA i listan med program](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen E försäljning Manager REMIXA på åtkomstpanelen du bör få automatiskt loggat in på ditt E försäljning Manager REMIXA program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

