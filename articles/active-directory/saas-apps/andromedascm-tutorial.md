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
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd26129a6ab8fb6082f9465be71eadcafa292db
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165205"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Självstudier: Azure Active Directory-integrering med Andromeda

I den här självstudien får du lära dig hur du integrerar Andromeda med Azure Active Directory (AD Azure).

Integrera Andromeda med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Andromeda.
- Du kan aktivera användarna att automatiskt få loggat in på Andromeda (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Andromeda, behöver du följande objekt:

- En Azure AD-prenumeration
- En Andromeda enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Andromeda från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-andromeda-from-the-gallery"></a>Att lägga till Andromeda från galleriet
För att konfigurera integrering av Andromeda i Azure AD, som du behöver lägga till Andromeda från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Andromeda från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Andromeda**väljer **Andromeda** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Andromeda i resultatlistan](./media/andromedascm-tutorial/tutorial_andromedascm_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Andromeda baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Andromeda är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Andromeda upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Andromeda, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare Andromeda](#create-an-andromeda-test-user)**  – du har en motsvarighet för Britta Simon i Andromeda som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Andromeda program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Andromeda:**

1. I Azure-portalen på den **Andromeda** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/andromedascm-tutorial/tutorial_andromedascm_samlbase.png)

3. På den **Andromeda domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Andromeda domän och URL: er med enkel inloggning för information](./media/andromedascm-tutorial/tutorial_andromedascm_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

4. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Andromeda domän och URL: er med enkel inloggning för information](./media/andromedascm-tutorial/tutorial_andromedascm_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`
     
    > [!NOTE] 
    > Föregående värde är inte verkliga värdet. Du ska uppdatera värdet med faktiska identifierare, svars-URL och inloggnings-URL som beskrivs senare i självstudien.

5. Andromeda program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. Följande skärmbild visar ett exempel på detta.
    
    ![Konfigurera enkel inloggning attb](./media/andromedascm-tutorial/tutorial_andromedascm_attribute.png)

    > [!Important]
    > Rensa namnområdesdefinitioner när du konfigurerar dessa.
    
6. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | -------------- | -------------------- |    
    | roll        | Ansvarar för att appen |
    | typ        | Apptyp |
    | Företagets       | CompanyName    |

    > [!NOTE]
    > Det finns inga verkliga värden. Dessa värden är endast för demo ändamål, Använd roller för din organisation.

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till](./media/andromedascm-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/andromedascm-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **Namnrymd** tom.
    
    e. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/andromedascm-tutorial/tutorial_andromedascm_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/andromedascm-tutorial/tutorial_general_400.png)
    
9. På den **Andromeda Configuration** klickar du på **konfigurera Andromeda** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Andromeda konfiguration](./media/andromedascm-tutorial/tutorial_andromedascm_configure.png)

10. Inloggning till webbplatsen Andromeda företag som administratör.

11. Överst på menyraden klickar du på **Admin** och gå till **Administration**.

    ![Andromeda admin](./media/andromedascm-tutorial/tutorial_andromedascm_admin.png)

12. På vänster sida av verktygsfältet under **gränssnitt** klickar du på **SAML-konfiguration**.

    ![Andromeda saml](./media/andromedascm-tutorial/tutorial_andromedascm_saml.png)

13. På den **SAML-konfiguration** avsnittet sidan, utför följande steg:

    ![Andromeda config](./media/andromedascm-tutorial/tutorial_andromedascm_config.png)

    a. Kontrollera **aktivera enkel inloggning med SAML**.

    b. Under **Andromeda Information** avsnittet, kopiera den **SP identitet** värde och klistra in den i den **identifierare** textrutan av **Andromeda domän och URL: er** avsnittet.

    c. Kopiera den **konsument URL** värde och klistra in den i den **svars-URL** textrutan av **Andromeda domän och URL: er** avsnittet.

    d. Kopiera den **inloggnings-URL** värde och klistra in den i den **inloggnings-URL** textrutan av **Andromeda domän och URL: er** avsnittet.

    e. Under **SAML-identitetsprovider** Skriv namnet på din IDP: N.

    f. I den **enkel inloggning på slutpunkten** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    g. Öppna den hämtade **Base64-kodat certifikat** från Azure-portalen i anteckningar, klistra in den i den **X 509-certifikat** textrutan.
    
    h. Mappa följande attribut med respektive värdet att underlätta SSO-inloggning från Azure AD. Den **användar-ID** attributet är obligatoriskt för att logga in. För att etablera, **e-post**, **företagets**, **UserType**, och **rollen** krävs. I det här avsnittet ska definiera vi attribut mappning (namn och värden) som korrelera till de som anges i Azure-portalen

    ![Andromeda attbmap](./media/andromedascm-tutorial/tutorial_andromedascm_attbmap.png)

    i. Klicka på **Spara**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/andromedascm-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/andromedascm-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/andromedascm-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/andromedascm-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-andromeda-test-user"></a>Skapa en Andromeda testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Andromeda. Andromeda stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Andromeda om det inte finns ännu.

>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [Andromeda klienten supportteamet](https://www.ngcsoftware.com/support/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Andromeda.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Andromeda, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Andromeda**.

    ![Länken Andromeda i listan med program](./media/andromedascm-tutorial/tutorial_andromedascm_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Andromeda i åtkomstpanelen du bör få automatiskt loggat in på ditt Andromeda program.
Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/andromedascm-tutorial/tutorial_general_01.png
[2]: ./media/andromedascm-tutorial/tutorial_general_02.png
[3]: ./media/andromedascm-tutorial/tutorial_general_03.png
[4]: ./media/andromedascm-tutorial/tutorial_general_04.png

[100]: ./media/andromedascm-tutorial/tutorial_general_100.png

[200]: ./media/andromedascm-tutorial/tutorial_general_200.png
[201]: ./media/andromedascm-tutorial/tutorial_general_201.png
[202]: ./media/andromedascm-tutorial/tutorial_general_202.png
[203]: ./media/andromedascm-tutorial/tutorial_general_203.png
