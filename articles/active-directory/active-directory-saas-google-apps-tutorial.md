---
title: "Självstudier: Azure Active Directory-integrering med Google Apps i Azure | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Google Apps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 065841d6b4fe50e953f01bba4d3f23de82b82726
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-google-apps"></a>Självstudier: Azure Active Directory-integrering med Google Apps

I kursen får lära du att integrera Google Apps med Azure Active Directory (AD Azure).

Integrera Google Apps med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Google Apps
- Du kan aktivera användarna att automatiskt hämta loggat in på Google Apps (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Google Apps behöver du följande:

- En Azure AD-prenumeration
- En Google Apps enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

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

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Google Apps**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_search.png)

5. Välj i resultatpanelen **Google Apps**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Google Apps baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Google Apps motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Google Apps upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Google Apps.

Om du vill konfigurera och testa Azure AD enkel inloggning med Google Apps, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för Google Apps](#creating-a-google-apps-test-user)**  – du har en motsvarighet för Britta Simon i Google-appar som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Google Apps-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Google Apps:**

1. I Azure-portalen på den **Google Apps** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_samlbase.png)

3. På den **Google Apps-domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://mail.google.com/a/<yourdomain>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta den [Google supportteamet](https://www.google.com/contact/).
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatet på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-google-apps-tutorial/tutorial_general_400.png)

6. På den **Google Apps-konfiguration** klickar du på **konfigurera Google Apps** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enkel inloggning URL: en och ändra URL: en lösenord** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_configure.png) 

7. Öppna en ny flik i webbläsaren och logga in på den [Google Apps-administrationskonsolen](http://admin.google.com/) med ditt administratörskonto.

8. Klicka på **säkerhet**. Om du inte ser länken, det kan vara dolt den **fler kontroller** menyn längst ned på skärmen.
   
    ![Klicka på Security.][10]

9. På den **säkerhet** klickar du på **Konfigurera enkel inloggning (SSO).**
   
    ![Klicka på enkel inloggning.][11]

10. Utför följande konfigurationsändringar:
   
    ![Konfigurera enkel inloggning][12]
   
    a. Välj **installationsprogrammet SSO med tredjeparts identitetsprovider**.

    b. I den **inloggning Sidadress** fältet i Google Apps, klistra in värdet för **inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    c. I den **URL för utloggning** fältet i Google Apps, klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen. 

    d. I den **ändra lösenord URL** fältet i Google Apps, klistra in värdet för **ändra lösenord URL**, som du har kopierat från Azure-portalen. 

    e. I Google Apps för den **verifieringscertifikat**, ladda upp certifikatet som du har hämtat från Azure-portalen.

    f. Klicka på **spara ändringar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-google-apps-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-google-apps-test-user"></a>Skapa en testanvändare för Google Apps

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Google Apps programvara. Google Apps har stöd för automatisk etablering, vilket är aktiverat som standard. Det finns inga åtgärder i det här avsnittet. Om en användare inte redan finns i Google Apps programvara, skapas en ny när du försöker komma åt Google Apps programvara.

>[!NOTE] 
>Om du behöver skapa en användare manuellt kan kontakta den [Google supportteamet](https://www.google.com/contact/).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Google Apps.

![Tilldela användare][200] 

**Om du vill tilldela Google Apps Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Google Apps**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-google-apps-tutorial/tutorial_googleapps_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen på [https://myapps.microsoft.com](active-directory-saas-access-panel-introduction.md)sedan logga in i testkonto och klicka på **Google Apps** panelen i åtkomstpanelen.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Konfigurera Användaretablering](active-directory-saas-google-apps-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-google-apps-tutorial/tutorial_general_203.png

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png

[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png