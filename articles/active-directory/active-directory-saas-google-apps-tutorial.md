---
title: "Självstudier: Azure Active Directory-integrering med Google Apps | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: bdcf2f8a06c782270683e34e9a4cbc2ab2c7f443
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Självstudier: Azure Active Directory-integrering med Google Apps

I kursen får lära du att integrera Google Apps med Azure Active Directory (AD Azure).

Integrera Google Apps med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Google Apps.
- Du kan aktivera användarna att automatiskt hämta loggat in på Google Apps (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Google Apps behöver du följande:

- En Azure AD-prenumeration
- En Google Apps enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="video-tutorial"></a>Videosjälvstudie
Så här aktiverar enkel inloggning till Google Apps i två minuter:

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Enable-single-sign-on-to-Google-Apps-in-2-minutes-with-Azure-AD/player]

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
1. **F: är Chromebooks och andra enheter som Chrome kompatibla med Azure AD enkel inloggning?**
   
    S: Ja kan användare logga in på sina Chromebook enheter med hjälp av autentiseringsuppgifterna Azure AD. Se den här [artikel stöd för Google Apps](https://support.google.com/chrome/a/answer/6060880) för information om varför användare kan uppmanas att autentiseringsuppgifter två gånger.

2. **F: om jag aktiverar enkel inloggning, kommer användarna att kunna använda sina Azure AD-autentiseringsuppgifter för att logga in på någon Google-produkt, till exempel Google klassrum, GMail, Google Drive, YouTube och så vidare?**
   
    S: Ja, beroende på [vilka Google apps](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

3. **F: kan jag aktivera enkel inloggning för en delmängd av användarna Google Apps?**
   
    S: inte kräver aktivera enkel inloggning omedelbart att alla Google Apps användare för autentisering med autentiseringsuppgifter för Azure AD. Eftersom Google Apps inte stöder att ha flera identitetsleverantörer, identitetsleverantören för Google Apps-miljö kan antingen vara Azure AD eller Google-- men inte båda samtidigt.

4. **F: om en användare har loggat in via Windows är de automatiskt autentisera till Google Apps utan att uppmanas att ange ett lösenord?**
   
    S: finns två alternativ för att aktivera det här scenariot. Först användare kan logga in på Windows 10-enheter via [Azure Active Directory Join](active-directory-azureadjoin-overview.md). Du kan också användare kan logga in på Windows-enheter som är anslutna till en domän till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services (AD FS)](active-directory-aadconnect-user-signin.md) distribution. I båda fallen måste du utföra stegen i följande guiden för att aktivera enkel inloggning mellan Azure AD och Google Apps.

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägga till Google Apps från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-google-apps-from-the-gallery"></a>Lägga till Google Apps från galleriet
Du måste lägga till Google Apps från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Google Apps i Azure AD.

**Utför följande steg för att lägga till Google Apps från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Google Apps**väljer **Google Apps** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Google Apps i resultatlistan](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Google Apps baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Google Apps motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Google Apps upprättas.

I Google Apps tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Google Apps, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för Google Apps](#create-a-google-apps-test-user)**  – du har en motsvarighet för Britta Simon i Google-appar som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Google Apps-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Google Apps:**

1. I Azure-portalen på den **Google Apps** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_samlbase.png)

3. På den **Google Apps-domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och Google Apps-domän med enkel inloggning information](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://mail.google.com/a/<yourdomain.com>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:

    | |
    |--|
    | `google.com`|
    | `http://google.com`|
    | `google.com/<yourdomain.com>`|
    | `http://google.com/a/<yourdomain.com>`|
       
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Google Apps klienten supportteamet](https://www.google.com/contact/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-googleapps-tutorial/tutorial_general_400.png)

6. På den **Google Apps-konfiguration** klickar du på **konfigurera Google Apps** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enkel inloggning URL: en och ändra URL: en lösenord** från den **Snabbreferens avsnitt.**

    ![Google Apps-konfiguration](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_configure.png) 

7. Öppna en ny flik i webbläsaren och logga in på den [Google Apps-administrationskonsolen](http://admin.google.com/) med ditt administratörskonto.

8. Klicka på **säkerhet**. Om du inte ser länken, det kan vara dolt den **fler kontroller** menyn längst ned på skärmen.
   
    ![Klicka på Security.][10]

9. På den **säkerhet** klickar du på **Konfigurera enkel inloggning (SSO).**
   
    ![Klicka på enkel inloggning.][11]

10. Utför följande konfigurationsändringar:
   
    ![Konfigurera enkel inloggning][12]
   
    a. Välj **installationsprogrammet SSO med tredjeparts identitetsprovider**.

    b. I den **inloggning Sidadress** fältet i Google Apps, klistra in värdet för **inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. I den **URL för utloggning** fältet i Google Apps, klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen. 

    d. I den **ändra lösenord URL** fältet i Google Apps, klistra in värdet för **ändra lösenord URL** som du har kopierat från Azure-portalen. 

    e. I Google Apps för den **verifieringscertifikat**, ladda upp certifikatet som du har hämtat från Azure-portalen.

    f. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-googleapps-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-googleapps-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-googleapps-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-googleapps-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-google-apps-test-user"></a>Skapa en testanvändare för Google Apps

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Google Apps programvara. Google Apps har stöd för automatisk etablering, vilket är aktiverat som standard. Det finns inga åtgärder i det här avsnittet. Om en användare inte redan finns i Google Apps programvara, skapas en ny när du försöker komma åt Google Apps programvara.

>[!NOTE] 
>Om du behöver skapa en användare manuellt kan kontakta den [Google supportteamet](https://www.google.com/contact/).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Google Apps.

![Tilldela rollen][200] 

**Om du vill tilldela Google Apps Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Google Apps**.

    ![Google Apps-länken i listan med program](./media/active-directory-saas-googleapps-tutorial/tutorial_googleapps_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på Google Apps-panelen på åtkomstpanelen du bör få automatiskt loggat in på ditt Google Apps-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-googleapps-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-googleapps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-googleapps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-googleapps-tutorial/gapps-sso-config.png

