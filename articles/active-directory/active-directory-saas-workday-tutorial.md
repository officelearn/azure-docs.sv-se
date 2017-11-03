---
title: "Självstudier: Azure Active Directory-integrering med Workday | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workday."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 164d5c644f120fa86e2b690649241892764b64b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Självstudier: Azure Active Directory-integrering med Workday

I kursen får lära du att integrera Workday med Azure Active Directory (AD Azure).

Integrera Workday med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workday
- Du kan aktivera användarna att automatiskt hämta loggat in på Workday (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med arbetsdagen, behöver du följande:

- En Azure AD-prenumeration
- En Workday enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Workday från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workday-from-the-gallery"></a>Att lägga till Workday från galleriet
Du måste lägga till Workday från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av arbetsdagen i Azure AD.

**Utför följande steg för att lägga till Workday från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Workday**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. Välj i resultatpanelen **Workday**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Workday baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Workday motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Workday upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Workday.

Om du vill konfigurera och testa Azure AD enkel inloggning med arbetsdagen, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Workday](#creating-a-workday-test-user)**  – du har en motsvarighet för Britta Simon i Workday som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Workday-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workday:**

1. I Azure-portalen på den **Workday** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. På den **Workday domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. I den **inloggnings-URL** textruta Skriv värdet som:`https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska inloggnings-URL och Reply-URL. Svars-URL: en måste ha en underdomän till exempel: www wd2, wd3, wd3 impl, wd5, wd5 impl). Med hjälp av något som liknar ”*http://www.myworkday.com*” fungerar men ”*http://myworkday.com*” finns inte. Kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta dessa värden. 
 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. På den **Workday Configuration** klickar du på **konfigurera Workday** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. I en annan webbläsarfönster loggar du in på webbplatsen Workday företag som administratör.

8. Gå till **menyn \> arbetsstationen**.
   
    ![Arbetsstationen](./media/active-directory-saas-workday-tutorial/IC782923.png "arbetsstationen")

9. Gå till **konto Administration**.
   
    ![Kontot Administration](./media/active-directory-saas-workday-tutorial/IC782924.png "konto Administration")

10. Gå till **redigera inställningar för klient – säkerhet**.
   
    ![Redigera klient säkerhet](./media/active-directory-saas-workday-tutorial/IC782925.png "redigera klient säkerhet")

11. I den **omdirigering av URL: er** avsnittet, utför följande steg:
   
    ![Omdirigering av URL: er](./media/active-directory-saas-workday-tutorial/IC7829581.png "omdirigerings-URL: er")
   
    a. Klicka på **lägga till raden**.
   
    b. I den **inloggningen omdirigerings-URL** textruta och **Mobile omdirigerings-URL** textruta typ av **inloggnings-URL** du har angett på den **Workday domän och URL: er** på Azure portal.
   
    c. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **Sign-Out URL**, och klistrar in det i den **logga ut omdirigerings-URL** textruta.
   
    d.  I **miljö** textruta typnamn miljö.  

    >[!NOTE]
    > Värdet för attributet miljön är knutna till värdet för klient-URL:  
    >-Om domännamnet för Workday-klient-URL börjar med impl till exempel: *https://impl.workday.com/\<klient\>/login-saml2.htmld*), **miljö** attributet måste anges till implementering.  
    >-Om domännamnet startas med ett annat, måste du kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta det matchande **miljö** värde.

12. I den **SAML installationsprogrammet** avsnittet, utför följande steg:
   
    ![SAML-installationsprogrammet](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML-installationen")
   
    a.  Välj **aktivera SAML-autentisering**.
   
    b.  Klicka på **lägga till raden**.

13. I avsnittet SAML identitetsleverantörer utför du följande steg:
   
    ![SAML identitetsleverantörer](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML identitetsleverantörer")
   
    a. Ange ett provider-namn i textrutan identitet providernamn (till exempel: *SPInitiatedSSO*).
   
    b. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **SAML enhets-ID** värdet och klistrar in det i den **utfärdaren** textruta.
   
    c. Välj **aktivera arbetsdagar initierade logga ut**.
   
    d. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **Sign-Out URL** värdet och klistrar in det i den **logga ut begäran URL** textruta.

    e. Klicka på **providern offentliga nyckel identitetscertifikat**, och klicka sedan på **skapa**. 

    ![Skapa](./media/active-directory-saas-workday-tutorial/IC782928.png "skapa")

    f. Klicka på **skapa x509 offentliga nyckel**. 

    ![Skapa](./media/active-directory-saas-workday-tutorial/IC782929.png "skapa")


14. I den **visa x509 offentliga nyckel** avsnittet, utför följande steg: 
   
    ![Visa x509 offentliga nyckel](./media/active-directory-saas-workday-tutorial/IC782930.png "visa x509 offentliga nyckel") 
   
    a. I den **namn** textruta, ange ett namn för certifikatet (till exempel: *utrustningen\_SP*).
   
    b. I den **giltigt från** textruta skriver giltig från attributvärde för ditt certifikat.
   
    c.  I den **till** textruta skriver det giltiga attributvärde för ditt certifikat.
   
    > [!NOTE]
    > Du kan hämta det giltiga från datum och giltiga datum från hämtade certifikatet genom att dubbelklicka på den.  Datumen visas under den **information** fliken.
    > 
    >
   
    d.  Öppna din Base64-kodade certifikatet i anteckningar och kopiera innehållet i den.
   
    e.  I den **certifikat** textruta klistra in innehållet i Urklipp.
   
    f.  Klicka på **OK**.

15. Utför följande steg: 
   
    ![Konfiguration av SSO](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguration")
   
    a.  Aktivera den **x509 privata nyckel**.
   
    b.  I den **Service Provider-ID** textruta typen **http://www.workday.com**.
   
    c.  Välj **aktivera SP initierade SAML-autentisering**.
   
    d.  I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **SAML inloggning tjänst-URL för enkel** värdet och klistrar in det i den **IdP SSO-tjänstens URL** textruta.
   
    e. Välj **inte Deflate SP-initierad autentiseringsbegäran**.
   
    f. Som **begära signatur autentiseringsmetod**väljer **SHA256**. 
   
    ![Autentiseringsmetod begäran signatur](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "autentiseringsmetod begäran signatur") 
   
    g. Klicka på **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")
<CE>

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
>

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-workday-test-user"></a>Skapa en arbetsdag testanvändare

För att få en testanvändare etableras i Workday, måste du kontakta den [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html).
Den [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) skapar användaren åt dig.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workday.

![Tilldela användare][200] 

**Om du vill tilldela Workday Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Workday**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-workday-inbound-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

