---
title: 'Självstudier: Azure Active Directory-integration med zoomning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och zoomning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/28/2017
ms.author: jeedes
ms.openlocfilehash: 57ae31245a356a4cd5769fe71ef471922bf6faf9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440142"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Självstudier: Azure Active Directory-integration med zoomning

Lär dig hur du integrerar zoomning med Azure Active Directory (AD Azure) i den här självstudien.

Integrera zoomning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till zoomning.
- Du kan aktivera användarna att automatiskt få loggat in på Zooma (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med zoomning, behöver du följande objekt:

- En Azure AD-prenumeration
- En zoomning enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till zoomning från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zoom-from-the-gallery"></a>Att lägga till zoomning från galleriet
Om du vill konfigurera integreringen av Zooma in Azure AD, som du behöver lägga till zoomning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till zoomning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Zooma**väljer **Zooma** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Zooma in listan med resultat](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med zoomning baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i zoomning till en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i zoomning upprättas.

I zoomning, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med zoomning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare för zoomning](#create-a-zoom-test-user)**  – du har en motsvarighet för Britta Simon i zoomning som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för zoomning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zoom:**

1. I Azure-portalen på den **Zooma** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

1. På den **Zooma domän och URL: er** avsnittet, utför följande steg:

    ![Zooma domän och URL: er med enkel inloggning för information](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.zoom.us`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `<companyname>.zoom.us`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Zooma klienten supportteamet](https://support.zoom.us/hc) att hämta dessa värden.

1. Zooma programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. 

    ![Konfigurera enkel inloggning](./media/zoom-tutorial/tutorial_attribute.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i föregående bild och utför följande steg:
    
    | Attributnamn | Attributvärde | Namespace värde |
    | ------------------- | -----------|--------- |    
    | E-postadress | User.Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Förnamn | User.givenName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Efternamn | User.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Telefonnummer | User.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Avdelning | User.Department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. I den **Namespace** textrutan skriver namnområdesvärdet som visas för den raden.
    
    e. Klicka på **OK**. 
 
1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/zoom-tutorial/tutorial_zoom_certificate.png)

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/zoom-tutorial/tutorial_general_400.png)

1. På den **Zooma Configuration** klickar du på **konfigurera Zooma** att öppna **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Zooma konfiguration](./media/zoom-tutorial/tutorial_zoom_configure.png)

1. I ett annat webbläsarfönster logga du in på webbplatsen för företagets zoomning som administratör.

1. Klicka på den **enkel inloggning** fliken.
   
    ![Enkel inloggning på fliken](./media/zoom-tutorial/IC784700.png "enkel inloggning")

1. Klicka på den **säkerhetskontroll** fliken och gå sedan till den **enkel inloggning** inställningar.

1. Utför följande steg i avsnittet enkel inloggning:
   
    ![Enkel inloggning för avsnittet](./media/zoom-tutorial/IC784701.png "enkel inloggning")
   
    a. I den **inloggning sid-URL** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    b. I den **utloggning sid-URL** textrutan klistra in värdet för **URL: en för utloggning** som du har kopierat från Azure-portalen.
     
    c. Öppna din Base64-kodat certifikat i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textrutan.

    d. I den **utfärdare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen. 

    e. Klicka på **Spara**.

    > [!NOTE] 
    > Mer information finns i dokumentationen för zoomning [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zoom-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/zoom-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zoom-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zoom-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-zoom-test-user"></a>Skapa en testanvändare för zoomning

För att aktivera Azure AD-användare att logga in på zoomning, måste de etableras i zoomning. När det gäller zoomning är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg för att etablera ett användarkonto:

1. Logga in på din **Zooma** företagets plats som administratör.
 
1. Klicka på den **kontohantering** fliken och klicka sedan på **Användarhantering**.

1. Klicka på under Användarhantering **lägga till användare**.
   
    ![Användarhantering](./media/zoom-tutorial/IC784703.png "användarhantering")

1. På den **lägga till användare** utför följande steg:
   
    ![Lägga till användare](./media/zoom-tutorial/IC784704.png "lägga till användare")
   
    a. Som **användartyp**väljer **grundläggande**.

    b. I den **e-postmeddelanden** textrutan, skriver du in e-postadressen till en giltig Azure AD-konto som du vill etablera.

    c. Klicka på **Lägg till**.

> [!NOTE]
> Du kan använda alla andra zoomning användare konto verktyg för att skapa eller API: er som tillhandahålls av zoomning att etablera Azure Active Directory användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till zoomning.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon zoomning, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Zooma**.

    ![Länken zoomning i listan med program](./media/zoom-tutorial/tutorial_zoom_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen zoomning i åtkomstpanelen du bör få automatiskt loggat in på ditt program för zoomning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zoom-tutorial/tutorial_general_01.png
[2]: ./media/zoom-tutorial/tutorial_general_02.png
[3]: ./media/zoom-tutorial/tutorial_general_03.png
[4]: ./media/zoom-tutorial/tutorial_general_04.png

[100]: ./media/zoom-tutorial/tutorial_general_100.png

[200]: ./media/zoom-tutorial/tutorial_general_200.png
[201]: ./media/zoom-tutorial/tutorial_general_201.png
[202]: ./media/zoom-tutorial/tutorial_general_202.png
[203]: ./media/zoom-tutorial/tutorial_general_203.png

