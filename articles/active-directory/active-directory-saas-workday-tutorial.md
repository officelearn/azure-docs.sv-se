---
title: 'Självstudier: Azure Active Directory-integrering med Workday | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: jeedes
ms.openlocfilehash: 1bc10894a7d86937628b8c8563df9d88453d4ccb
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34353575"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Självstudier: Azure Active Directory-integrering med Workday

I kursen får lära du att integrera Workday med Azure Active Directory (AD Azure).

Integrera Workday med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Workday.
- Du kan aktivera användarna att automatiskt hämta loggat in på Workday (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med arbetsdagen, behöver du följande:

- En Azure AD-prenumeration
- En Workday enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Workday från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-workday-from-the-gallery"></a>Att lägga till Workday från galleriet
Du måste lägga till Workday från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av arbetsdagen i Azure AD.

**Utför följande steg för att lägga till Workday från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Workday**väljer **Workday** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Arbetsdag i resultatlistan](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Workday baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Workday motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Workday upprättas.

I Workday, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med arbetsdagen, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Workday](#create-a-workday-test-user)**  – du har en motsvarighet för Britta Simon i Workday som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Workday-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Workday:**

1. I Azure-portalen på den **Workday** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. På den **Workday domän och URL: er** avsnittet, utför följande steg:

    ![Arbetsdagar domän och URL: er med enkel inloggning information](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml2.htmld`

    b. I den **identifierare** textruta, ange ett URL-Adressen: `http://www.workday.com`

4. Kontrollera **visa avancerade inställningar för URL: en** och utföra följande steg:

    ![Arbetsdagar domän och URL: er med enkel inloggning information](./media/active-directory-saas-workday-tutorial/tutorial_workday_url1.png)

    I den **Reply URL** textruta Skriv en URL med följande mönster: `https://impl.workday.com/<tenant>/login-saml.htmld`
     
    > [!NOTE] 
    > Dessa värden är inte verkligt. Uppdatera dessa värden med den faktiska inloggnings-URL och Reply-URL. Svars-URL: en måste ha en underdomän till exempel: www wd2, wd3, wd3 impl, wd5, wd5 impl). Med hjälp av något som liknar ”*http://www.myworkday.com*” fungerar men ”*http://myworkday.com*” finns inte. Kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta dessa värden.  

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)
    
7. På den **Workday Configuration** klickar du på **konfigurera Workday** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Arbetsdagar konfiguration](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 

8. I en annan webbläsarfönster loggar du in på webbplatsen Workday företag som administratör.

9. I den **sökrutan** sökning med namnet **redigera klient inställningar – säkerhet** överst vänster på startsidan.
   
    ![Redigera klient säkerhet](./media/active-directory-saas-workday-tutorial/IC782925.png "redigera klient säkerhet")

10. I den **omdirigering av URL: er** avsnittet, utför följande steg:
   
    ![Omdirigering av URL: er](./media/active-directory-saas-workday-tutorial/IC7829581.png "omdirigerings-URL: er")
   
    a. Klicka på **lägga till raden**.
   
    b. I den **inloggningen omdirigerings-URL** textruta och **Mobile omdirigerings-URL** textruta typ av **inloggnings-URL** du har angett på den **Workday domän och URL: er** på Azure portal.
   
    c. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **Sign-Out URL**, och klistrar in det i den **logga ut omdirigerings-URL** textruta.

    d. I **används för miljöer** textruta väljer du namnet på miljön.  

    >[!NOTE]
    > Värdet för attributet miljön är knutna till värdet för klient-URL:  
    >-Om domännamnet för Workday-klient-URL börjar med impl till exempel: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*), **miljö** attributet måste anges till implementering.  
    >-Om domännamnet startas med ett annat, måste du kontakta [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att hämta det matchande **miljö** värde.

11. I den **SAML installationsprogrammet** avsnittet, utför följande steg:
   
    ![SAML-installationsprogrammet](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML-installationen")
   
    a.  Välj **aktivera SAML-autentisering**.
   
    b.  Klicka på **lägga till raden**.

12. I den **SAML identitetsleverantörer** avsnittet, utför följande steg:
   
    ![SAML identitetsleverantörer](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML identitetsleverantörer")
   
    a. I den **identitet providernamn** textruta skriver ett provider-namn (till exempel: *SPInitiatedSSO*).
   
    b. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **SAML enhets-ID** värdet och klistrar in det i den **utfärdaren** textruta.

    ![SAML identitetsleverantörer](./media/active-directory-saas-workday-tutorial/IC7829272.png "SAML identitetsleverantörer")
   
    c. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **Sign-Out URL** värdet och klistrar in det i den **logga ut svars-URL** textruta.

    d. I Azure-portalen på den **konfigurera inloggning** fönster, kopiera den **SAML inloggning tjänst-URL för enkel** värdet och klistrar in det i den **IdP SSO-tjänstens URL** textruta.

    e. I **används för miljöer** textruta väljer du namnet på miljön.

    f. Klicka på **providern offentliga nyckel identitetscertifikat**, och klicka sedan på **skapa**. 

    ![Skapa](./media/active-directory-saas-workday-tutorial/IC782928.png "skapa")

    g. Klicka på **skapa x509 offentliga nyckel**. 

    ![Skapa](./media/active-directory-saas-workday-tutorial/IC782929.png "skapa")

13. I den **visa x509 offentliga nyckel** avsnittet, utför följande steg: 
   
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

14. Utför följande steg: 
   
    ![Konfiguration av SSO](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO-konfiguration")
   
    a.  I den **Service Provider-ID** textruta typen **http://www.workday.com**.
   
    b. Välj **inte Deflate SP-initierad autentiseringsbegäran**.
   
    c. Som **begära signatur autentiseringsmetod**väljer **SHA256**. 
   
    ![Autentiseringsmetod begäran signatur](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "autentiseringsmetod begäran signatur") 
   
    d. Klicka på **OK**. 
   
    ![OK](./media/active-directory-saas-workday-tutorial/IC782933.png "OK")

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-workday-test-user"></a>Skapa en arbetsdag testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Workday. Arbeta med [Workday klienten supportteamet](https://www.workday.com/en-us/partners-services/services/support.html) att lägga till användare i Workday-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Workday.

![Tilldela rollen][200] 

**Om du vill tilldela Workday Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Workday**.

    ![Länken Workday i listan med program](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen arbetsdag på åtkomstpanelen du bör få automatiskt loggat in på ditt Workday-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)


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
