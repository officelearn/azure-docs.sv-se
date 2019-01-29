---
title: 'Självstudier: Azure Active Directory-integrering med Grovo | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Grovo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 399cecc3-aa62-4914-8b6c-5a35289820c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2018
ms.author: jeedes
ms.openlocfilehash: 057775f7818d1a6dc521fe81b01748fa40085cc8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174252"
---
# <a name="tutorial-azure-active-directory-integration-with-grovo"></a>Självstudier: Azure Active Directory-integrering med Grovo

I den här självstudien får du lära dig hur du integrerar Grovo med Azure Active Directory (AD Azure).

Integrera Grovo med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Grovo.
- Du kan aktivera användarna att automatiskt få loggat in på Grovo (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Grovo, behöver du följande objekt:

- En Azure AD-prenumeration
- En Grovo enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Grovo från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-grovo-from-the-gallery"></a>Att lägga till Grovo från galleriet
För att konfigurera integrering av Grovo i Azure AD, som du behöver lägga till Grovo från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Grovo från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Grovo**väljer **Grovo** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Grovo i resultatlistan](./media/grovo-tutorial/tutorial_grovo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Grovo baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Grovo är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Grovo upprättas.

I Grovo, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Grovo, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Grovo](#create-a-grovo-test-user)**  – du har en motsvarighet för Britta Simon i Grovo som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Grovo program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Grovo:**

1. I Azure-portalen på den **Grovo** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/grovo-tutorial/tutorial_grovo_samlbase.png)

1. På den **Grovo domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Grovo domän och URL: er med enkel inloggning för information](./media/grovo-tutorial/tutorial_grovo_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: `https://<subdomain>.grovo.com/sso/saml2/metadata`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

1. Kontrollera **visa avancerade URL-inställningar**, utföra följande steg:

    ![Grovo domän och URL: er med enkel inloggning för information](./media/grovo-tutorial/tutorial_grovo_url1.png)

    a. I den **vidarebefordrar tillstånd** textrutan anger du ett URL med hjälp av följande mönster:`https://<subdomain>.grovo.com`

    b. Om du vill konfigurera programmet i **SP** initierade läge, utför följande steg:

    ![Grovo domän och URL: er med enkel inloggning för information](./media/grovo-tutorial/tutorial_grovo_url2.png)
    
    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.grovo.com/sso/saml2/saml-assertion`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska identifierare, svars-URL, inloggnings-URL och Relay. Kontakta [Grovo supportteamet](https://www.grovo.com/contact-us) att hämta dessa värden.
 
1. Grovo program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Mappa **användaridentifierare** med **user.mail** och konfigurera andra attribut som visas i skärmbilden nedan.
    
    ![Konfigurera enkel inloggning attb](./media/grovo-tutorial/tutorial_grovo_attribute.png)
    
1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |    
    | Förnamn          | user.givenname |
    | Efternamn           | user.surname |
    | E-postadress       | user.mail    |
    | employeeID          | user.employeeid |

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till](./media/grovo-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning Addattb](./media/grovo-tutorial/tutorial_attribute_05.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna **Namnrymd** tom.
    
    e. Klicka på **OK**.


1. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/grovo-tutorial/tutorial_grovo_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/grovo-tutorial/tutorial_general_400.png)

1. På den **Grovo Configuration** klickar du på **konfigurera Grovo** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Grovo konfiguration](./media/grovo-tutorial/tutorial_grovo_configure.png) 

1. I ett annat webbläsarfönster, logga in på Grovo som administratör.

1. Gå till **Admin** > **integreringar**.
 
    ![Grovo konfiguration](./media/grovo-tutorial/tutorial_grovo_admin.png) 

1. Klicka på **SET UP** under **SP-initierat SAML 2.0** avsnittet.

    ![Grovo konfiguration](./media/grovo-tutorial/tutorial_grovo_setup.png)

1. I **SP-initierat SAML 2.0** popup-fönstret utför följande steg:

    ![Grovo konfiguration](./media/grovo-tutorial/tutorial_grovo_saml.png)

    a. I den **entitets-id** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen.

    b. I den **enkel inloggning på tjänstslutpunkt** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    c. Välj **enkel inloggning på slutpunkten tjänstbindning** som `urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect`.
    
    d. Öppna den hämtade **Base64-kodat certifikat** från Azure-portalen i anteckningar, klistra in den i den **offentlig nyckel** textrutan.

    e. Klicka på **Nästa**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/grovo-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/grovo-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/grovo-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/grovo-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
  
### <a name="create-a-grovo-test-user"></a>Skapa en Grovo testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Grovo. Grovo stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt Grovo om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt, kontakta [Grovo supportteamet](https://www.grovo.com/contact-us).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Grovo.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Grovo, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Grovo**.

    ![Länken Grovo i listan med program](./media/grovo-tutorial/tutorial_grovo_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Grovo i åtkomstpanelen du bör få automatiskt loggat in på ditt Grovo program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/grovo-tutorial/tutorial_general_01.png
[2]: ./media/grovo-tutorial/tutorial_general_02.png
[3]: ./media/grovo-tutorial/tutorial_general_03.png
[4]: ./media/grovo-tutorial/tutorial_general_04.png

[100]: ./media/grovo-tutorial/tutorial_general_100.png

[200]: ./media/grovo-tutorial/tutorial_general_200.png
[201]: ./media/grovo-tutorial/tutorial_general_201.png
[202]: ./media/grovo-tutorial/tutorial_general_202.png
[203]: ./media/grovo-tutorial/tutorial_general_203.png

