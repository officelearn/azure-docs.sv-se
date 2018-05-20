---
title: 'Självstudier: Azure Active Directory-integrering med Andromeda | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Andromeda.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7a142c86-ca0c-4915-b1d8-124c08c3e3d8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.openlocfilehash: cdceb81319529a3db8b5c9809818d024acc3f80b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Självstudier: Azure Active Directory-integrering med Andromeda

I kursen får lära du att integrera Andromeda med Azure Active Directory (AD Azure).

Integrera Andromeda med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Andromeda.
- Du kan aktivera användarna att automatiskt hämta loggat in på Andromeda (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Andromeda, behöver du följande:

- En Azure AD-prenumeration
- En Andromeda enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Andromeda från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-andromeda-from-the-gallery"></a>Att lägga till Andromeda från galleriet
Du måste lägga till Andromeda från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Andromeda i Azure AD.

**Utför följande steg för att lägga till Andromeda från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Andromeda**väljer **Andromeda** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Andromeda i resultatlistan](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Andromeda baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Andromeda motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Andromeda upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Andromeda, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Andromeda](#create-an-andromeda-test-user)**  – du har en motsvarighet för Britta Simon i Andromeda som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Andromeda program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Andromeda:**

1. I Azure-portalen på den **Andromeda** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. På den **Andromeda domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Andromeda domän med enkel inloggning information](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Andromeda domän med enkel inloggning information](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Föregående värde är inte verkliga värde. Du uppdaterar värdet med faktiska identifierare, Reply URL och inloggnings-URL som beskrivs senare i självstudierna.

5. Andromeda program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning attb](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Rensa namnområdesdefinitioner när du konfigurerar dessa.
    
6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | -------------- | -------------------- |    
    | roll        | App-specifik roll |
    | typ        | Apptyp |
    | Företag       | Företagsnamn    |

    > [!NOTE]
    > Det finns inga verkliga värden. Dessa värden är endast för demo ändamål, Använd roller för din organisation.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-andromedascm-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/active-directory-saas-andromedascm-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Lämna den **Namespace** tomt.
    
    e. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-andromedascm-tutorial/tutorial_general_400.png)
    
9. På den **Andromeda Configuration** klickar du på **konfigurera Andromeda** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Andromeda konfiguration](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Inloggning på webbplatsen Andromeda företag som administratör.

11. Överst på menyraden klickar du på **Admin** och gå till **Administration**.

    ![Andromeda admin](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. På vänster sida i verktygsfältet under **gränssnitt** klickar du på **SAML-konfiguration**.

    ![Andromeda saml](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. På den **SAML-konfiguration** avsnittet sida, utför följande steg:

    ![Andromeda config](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Kontrollera **aktivera enkel inloggning med SAML**.

    b. Under **Andromeda Information** avsnittet, kopiera den **SP identitet** värdet och klistrar in det i den **identifierare** textruta för **Andromeda domän och URL: er** avsnitt.

    c. Kopiera den **konsumenten URL** värdet och klistrar in det i den **Reply URL** textruta för **Andromeda domän och URL: er** avsnitt.

    d. Kopiera den **inloggnings-URL** värdet och klistrar in det i den **inloggnings-URL** textruta för **Andromeda domän och URL: er** avsnitt.

    e. Under **SAML-identitetsprovider** skriver IDP-namn.

    f. I den **enkel inloggning på slutpunkten** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    g. Öppna den hämtade **Base64-kodat certifikat** från Azure-portalen i anteckningar klistrar du in det i den **X 509-certifikat** textruta.
    
    h. Mappa följande attribut med värdet respektive för att underlätta SSO-inloggning från Azure AD. Den **användar-ID** -attribut krävs för att logga in. För att etablera, **e-post**, **företagets**, **UserType**, och **rollen** krävs. I det här avsnittet definiera vi attributen mappning (namn och värden) som korrelera till dem som anges i Azure-portalen

    ![Andromeda attbmap](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-andromedascm-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-andromeda-test-user"></a>Skapa en testanvändare Andromeda

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Andromeda. Andromeda stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Andromeda om den inte finns.

>[!Note]
>Om du behöver skapa en användare manuellt Kontakta [Andromeda klienten supportteamet](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Andromeda.

![Tilldela rollen][200] 

**Om du vill tilldela Andromeda Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Andromeda**.

    ![Länken Andromeda i listan med program](./media/active-directory-saas-andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Andromeda på åtkomstpanelen du bör få automatiskt loggat in på ditt Andromeda program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-andromedascm-tutorial/tutorial_general_203.png
