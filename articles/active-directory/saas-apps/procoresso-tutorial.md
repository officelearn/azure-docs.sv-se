---
title: 'Självstudier: Azure Active Directory-integration med Procore SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: jeedes
ms.openlocfilehash: bd84224f4c3a8a498a296ff50190713111895472
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051623"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Självstudier: Azure Active Directory-integrering med Procore enkel inloggning

I den här självstudien får du lära dig hur du integrerar Procore enkel inloggning med Azure Active Directory (AD Azure).

Integrera Procore enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Procore SSO
- Du kan aktivera användarna att automatiskt få loggat in på Procore SSO (enkel inloggning) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats - Azure-hanteringsportalen

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Procore SSO, behöver du följande objekt:

- En Azure AD-prenumeration
- En Procore SSO enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Du bör inte använda din produktionsmiljö såvida inte detta är nödvändigt.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Procore SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-procore-sso-from-the-gallery"></a>Att lägga till Procore SSO från galleriet
Om du vill konfigurera integreringen av Procore enkel inloggning till Azure AD, som du behöver lägga till Procore SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Procore SSO från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Procore SSO**.

    ![Skapa en Azure AD-användare för testning](./media/procoresso-tutorial/tutorial_procoresso_search.png)

5. I resultatpanelen väljer **Procore SSO**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Procore SSO baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Procore enkel inloggning till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Procore SSO upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Procore SSO.

Om du vill konfigurera och testa Azure AD enkel inloggning med Procore enkel inloggning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Procore SSO](#creating-a-procore-sso-test-user)**  – du har en motsvarighet för Britta Simon i Procore enkel inloggning som är länkad till en Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt Procore SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Procore enkel inloggning:**

1. I hanteringsportalen för Azure på den **Procore SSO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_procoresso_samlbase.png)

3. På den **Procore SSO-domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_procoresso_url.png)

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_general_400.png)

6. På den **Procore SSO-konfiguration** klickar du på **Konfigurera enkel inloggning för Procore** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_procoresso_configure.png) 

7. Att konfigurera enkel inloggning på **Procore SSO** sida, logga in på webbplatsen procore företag som administratör.

8. I verktygslådan listrutan ned, klickar du på **Admin** att öppna inställningssidan för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_tool_admin.png)

9. Klistra in värden i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. I den **enkel inloggning på utfärdar-URL** box och klistra in SAML entitets-ID som kopieras från Azure-portalen.

    b. I den **SAML-inloggning på mål-URL** box och klistra in SAML enkel inloggning för tjänstens URL kopieras från Azure-portalen.

    c. Öppna nu den **XML-Metadata för** ned ovan från Azure-portalen och kopiera certifkatets i taggen med namnet **X509Certificate**. Klistra in det kopierade värdet till den **Single Sign On x509 certifikat** box.

10. Klicka på **spara ändringar**.

11. När du har dessa inställningar du behöver skicka den **domännamn** (t.ex **contoso.com**) via som du loggar in Procore till den [Procore supportteamet](https://support.procore.com/) och de kommer Aktivera federerad enkel inloggning för domänen.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/procoresso-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och klicka på **alla användare** att visa en lista över användare.
    
    ![Skapa en Azure AD-användare för testning](./media/procoresso-tutorial/create_aaduser_02.png) 

3. Överst i dialogrutan klickar du på **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/procoresso-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/procoresso-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-procore-sso-test-user"></a>Skapa en testanvändare Procore SSO

Följ de stegen nedan för att skapa en Procore testanvändare på sidan.

1. Logga in på webbplatsen procore företag som administratör.  

2. I verktygslådan listrutan ned, klickar du på **Directory** att öppna sidan företagets katalog.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klicka på **lägga till en Person** alternativet för att öppna formuläret och ange utföra följande alternativ -

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_add.png)

    a. I den **Förnamn** textrutan typ användarens förnamn som **Britta**.

    b. I den **efternamn** textrutan typ användarens efternamn som **Simon**.

    c. I den **e-postadress** textrutan typ användarens e-postadress som **BrittaSimon@contoso.com**.

    d. Välj **behörighet mallen** som **tillämpa behörighet mallen senare**.

    e. Klicka på **Skapa**.

4. Kontrollera och uppdatera informationen för den nyligen tillagda kontakten.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_check.png)

5. Klicka på **spara och skicka Invitiation** (om det krävs en inbjudan via e-post) eller **spara** (spara direkt) för att slutföra registreringen för användaren.
    
    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/Procore_user_save.png)  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning ger användarens företagsidentitet åtkomst Procore SSO.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Procore SSO, utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och sedan gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Procore SSO**.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_procoresso_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

Öppna panelen om du vill testa dina inställningar för enkel inloggning. Mer information om åtkomstpanelen finns i [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). När du klickar på panelen Procore SSO i åtkomstpanelen du bör få automatiskt loggat in på ditt Procore SSO-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/procoresso-tutorial/tutorial_general_01.png
[2]: ./media/procoresso-tutorial/tutorial_general_02.png
[3]: ./media/procoresso-tutorial/tutorial_general_03.png
[4]: ./media/procoresso-tutorial/tutorial_general_04.png

[100]: ./media/procoresso-tutorial/tutorial_general_100.png

[200]: ./media/procoresso-tutorial/tutorial_general_200.png
[201]: ./media/procoresso-tutorial/tutorial_general_201.png
[202]: ./media/procoresso-tutorial/tutorial_general_202.png
[203]: ./media/procoresso-tutorial/tutorial_general_203.png

