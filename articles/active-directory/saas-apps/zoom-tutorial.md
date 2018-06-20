---
title: 'Självstudier: Azure Active Directory-integrering med zoomning | Microsoft Docs'
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
ms.openlocfilehash: ed0018277502d2689d28009e58cdffd39a02cee7
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231186"
---
# <a name="tutorial-azure-active-directory-integration-with-zoom"></a>Självstudier: Azure Active Directory-integrering med zoomning

I kursen får lära du att integrera zoomning med Azure Active Directory (AD Azure).

Integrera zoomning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till zoomning.
- Du kan aktivera användarna att automatiskt hämta loggat in på Zooma (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med zoomning, behöver du följande:

- En Azure AD-prenumeration
- En zoomning enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till zoomning från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zoom-from-the-gallery"></a>Att lägga till zoomning från galleriet
Du måste lägga till zoomning från galleriet i listan över hanterade SaaS-appar för att konfigurera Zooma till Azure AD-integrering.

**Utför följande steg för att lägga till zoomning från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Zooma**väljer **Zooma** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Zooma in resultatlistan](./media/zoom-tutorial/tutorial_zoom_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med zoomning baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i zoomning motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Zoom upprättas.

I zoomning, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med zoomning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare zoomning](#create-a-zoom-test-user)**  – har en motsvarighet för Britta Simon zoomning som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för zoomning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med zoomning:**

1. I Azure-portalen på den **Zooma** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/zoom-tutorial/tutorial_zoom_samlbase.png)

3. På den **URL: er och zooma domän** avsnittet, utför följande steg:

    ![URL: er och zoomning domän med enkel inloggning information](./media/zoom-tutorial/tutorial_zoom_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.zoom.us`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `<companyname>.zoom.us`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Zooma klienten supportteamet](https://support.zoom.us/hc) att hämta dessa värden.

4. Zooma programmet förväntar SAML-intyg i ett specifikt format, vilket kräver att du kan lägga till anpassade attributmappning konfigurationen för SAML-token attribut. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. 

    ![Konfigurera enkel inloggning](./media/zoom-tutorial/tutorial_attribute.png)

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i den föregående bilden och utför följande steg:
    
    | Attributnamn | Attributvärde | Namespace-värde |
    | ------------------- | -----------|--------- |    
    | E-postadress | User.Mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail`|
    | Förnamn | User.givenName | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`|
    | Efternamn | User.surname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname `|
    | Telefonnummer | User.telephonenumber | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone`|
    | Avdelning | User.Department | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department`|

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/zoom-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/zoom-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. I den **Namespace** textruta skriver namnområdesvärdet som visas för den raden.
    
    e. Klicka på **OK**. 
 
6. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/zoom-tutorial/tutorial_zoom_certificate.png)

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/zoom-tutorial/tutorial_general_400.png)

8. På den **Zooma Configuration** klickar du på **konfigurera Zooma** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Zooma konfiguration](./media/zoom-tutorial/tutorial_zoom_configure.png)

9. I en annan webbläsarfönster loggar du in på webbplatsen zoomning företag som administratör.

10. Klicka på den **enkel inloggning** fliken.
   
    ![Enkel inloggning fliken](./media/zoom-tutorial/IC784700.png "enkel inloggning")

11. Klicka på den **säkerhetskontroll** fliken och gå sedan till den **enkel inloggning** inställningar.

12. Utför följande steg i avsnittet enkel inloggning:
   
    ![Enkel inloggning avsnittet](./media/zoom-tutorial/IC784701.png "enkel inloggning")
   
    a. I den **inloggning Sidadress** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    b. I den **URL för utloggning** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
     
    c. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **providern identitetscertifikat** textruta.

    d. I den **utfärdaren** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen. 

    e. Klicka på **Spara**.

    > [!NOTE] 
    > Mer information finns i dokumentationen för zoomning [https://zoomus.zendesk.com/hc/en-us/articles/115005887566](https://zoomus.zendesk.com/hc/en-us/articles/115005887566)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/zoom-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/zoom-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/zoom-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/zoom-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-zoom-test-user"></a>Skapa en testanvändare zoomning

För att aktivera Azure AD-användare kan logga in på Zooma etableras de i zoomning. Zooma är etablering en manuell aktivitet.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg om du vill konfigurera ett användarkonto:

1. Logga in på ditt **Zooma** företagets webbplats som administratör.
 
2. Klicka på den **kontohantering** fliken och klicka sedan på **Användarhantering**.

3. Klicka på avsnittet Användarhantering **lägga till användare**.
   
    ![Användarhantering](./media/zoom-tutorial/IC784703.png "användarhantering")

4. På den **lägga till användare** utför följande steg:
   
    ![Lägg till användare](./media/zoom-tutorial/IC784704.png "lägga till användare")
   
    a. Som **användartyp**väljer **grundläggande**.

    b. I den **e-postmeddelanden** textruta, Skriv ett giltigt Azure AD e-postadress konto du vill etablera.

    c. Klicka på **Lägg till**.

> [!NOTE]
> Du kan använda något annat zoomning användarens konto skapas verktyg eller API: er som tillhandahålls av zoomning att etablera Azure Active Directory användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till zoomning.

![Tilldela rollen][200] 

**Om du vill tilldela zoomning Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Zooma**.

    ![Zooma-länken i listan med program](./media/zoom-tutorial/tutorial_zoom_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen zoomning på åtkomstpanelen du ska hämta automatiskt loggat in på ditt program för zoomning.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

