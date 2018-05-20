---
title: 'Självstudier: Azure Active Directory-integrering med Procore SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Procore enkel inloggning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: eaa340e94311f5e70f473eab534df3fb3a87c866
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Självstudier: Azure Active Directory-integrering med Procore enkel inloggning

I kursen får lära du att integrera Procore enkel inloggning med Azure Active Directory (AD Azure).

Integrera Procore enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Procore SSO
- Du kan aktivera användarna att automatiskt hämta loggat in på Procore SSO (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Procore enkel inloggning, behöver du följande:

- En Azure AD-prenumeration
- En Procore SSO enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Procore SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-procore-sso-from-the-gallery"></a>Att lägga till Procore SSO från galleriet
Du måste lägga till Procore SSO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Procore SSO i Azure AD.

**Utför följande steg för att lägga till Procore SSO från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Procore SSO**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_search.png)

5. Välj i resultatpanelen **Procore SSO**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Procore SSO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren Procore SSO till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren Procore SSO upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** Procore sso.

Om du vill konfigurera och testa Azure AD enkel inloggning med Procore enkel inloggning måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Procore SSO](#creating-a-procore-sso-test-user)**  – du har en motsvarighet för Britta Simon Procore SSO som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Procore SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Procore enkel inloggning:**

1. I Azure-hanteringsportalen på den **Procore SSO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. På den **Procore domän för SSO och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_url.png)

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/tutorial_general_400.png)

6. På den **Procore SSO Configuration** klickar du på **Konfigurera enkel inloggning för Procore** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Konfigurera enkel inloggning på **Procore SSO** sida, logga in på webbplatsen procore företag som administratör.

8. I nedrullningsbara verktygslådan nedåt, klickar du på **Admin** att öppna inställningssidan för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/procore_tool_admin.png)

9. Klistra in värden i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/procore_setting_admin.png)    

    a. I den **enkel inloggning på utfärdar-URL** rutan och klistra in SAML enhets-ID som kopieras från Azure-portalen.

    b. I den **SAML-inloggning på mål-URL** rutan och klistra in SAML enkel inloggning tjänstens Webbadress kopieras från Azure-portalen.

    c. Öppna den **XML-Metadata för** hämtade ovan från Azure-portalen och kopiera certifkatets i taggen med namnet **X509Certificate**. Klistra in det kopierade värdet till den **enkel inloggning x509 certifikat** rutan.

10. Klicka på **spara ändringar**.

11. När dessa inställningar du måste skicka den **domännamn** (t.ex **contoso.com**) via som du loggar in Procore till den [Procore supportteamet](https://support.procore.com/) och de aktiveras federerad enkel inloggning för domänen.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-procoresso-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-procore-sso-test-user"></a>Skapa en testanvändare Procore SSO

Följ de nedanstående steg för att skapa en Procore testanvändare på sidan.

1. Logga in på webbplatsen procore företag som administratör.  

2. I nedrullningsbara verktygslådan nedåt, klickar du på **Directory** att öppna sidan företagets katalog.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/Procore_sso_directory.png)

3. Klicka på **lägga till en Person** alternativet för att öppna formuläret och ange utföra följande alternativ -

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/Procore_user_add.png)

    a. I den **Förnamn** textruta typen användarens förnamn som **Britta**.

    b. I den **efternamn** textruta typen användarens efternamn som **Simon**.

    c. I den **e-postadress** textruta typen användarens e-postadress som **BrittaSimon@contoso.com**.

    d. Välj **behörighet mallen** som **tillämpa behörighet mall senare**.

    e. Klicka på **Skapa**.

4. Kontrollera och uppdatera informationen för den nya kontakten.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/Procore_user_check.png)

5. Klicka på **spara och skicka Invitiation** (om det krävs en inbjudan via e-post) eller **spara** (spara direkt) för att slutföra registreringen av användaren.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/Procore_user_save.png)    

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja sin åtkomst till Procore enkel inloggning.

![Tilldela användare][200] 

**Om du vill tilldela Procore SSO Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Procore SSO**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-procoresso-tutorial/tutorial_procoresso_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586). När du klickar på panelen Procore SSO på åtkomstpanelen du bör få automatiskt loggat in på ditt Procore SSO-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-procoresso-tutorial/tutorial_general_203.png

