---
title: 'Självstudier: Azure Active Directory-integration med Procore SSO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: jeedes
ms.openlocfilehash: 12e7a37c1d2811b44dbf943b460b7d620770d45e
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53017533"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Självstudier: Azure Active Directory-integrering med Procore enkel inloggning

I den här självstudien får du lära dig hur du integrerar Procore enkel inloggning med Azure Active Directory (AD Azure).

Integrera Procore enkel inloggning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Procore SSO.
- Du kan aktivera användarna att automatiskt få loggat in på Procore SSO (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Procore SSO, behöver du följande objekt:

- En Azure AD-prenumeration
- En Procore SSO enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Procore SSO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-procore-sso-from-the-gallery"></a>Att lägga till Procore SSO från galleriet

Om du vill konfigurera integreringen av Procore enkel inloggning till Azure AD, som du behöver lägga till Procore SSO från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Procore SSO från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Procore SSO**väljer **Procore SSO** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Procore SSO i resultatlistan](./media/procoresso-tutorial/tutorial_procoresso_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Procore SSO baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Procore enkel inloggning till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Procore SSO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Procore enkel inloggning, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Procore SSO](#creating-a-procore-sso-test-user)**  – du har en motsvarighet för Britta Simon i Procore enkel inloggning som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Procore SSO-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Procore enkel inloggning:**

1. I Azure-portalen på den **Procore SSO** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. På den **SAML grundkonfiguration** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Procore SSO-domän och URL: er med enkel inloggning för information](./media/procoresso-tutorial/tutorial_procoresso_url.png)

5. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara Metadata för filen på datorn.

    ![Länk för hämtning av certifikat](./media/procoresso-tutorial/tutorial_procoresso_certificate.png) 

6. På den **konfigurera Procore SSO** avsnittet, kopiera den lämpliga URL enligt dina behov.

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

    ![Procore SSO-konfiguration](common/configuresection.png)

7. Att konfigurera enkel inloggning på **Procore SSO** sida, logga in på webbplatsen procore företag som administratör.

8. I verktygslådan listrutan ned, klickar du på **Admin** att öppna inställningssidan för enkel inloggning.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_tool_admin.png)

9. Klistra in värden i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. I den **enkel inloggning på utfärdar-URL** text rutan, klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    b. I den **SAML-inloggning på mål-URL** rutan, klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen.

    c. Öppna nu den **XML-Metadata för Federation** ned ovan från Azure-portalen och kopiera certifikatet i taggen med namnet **X509Certificate**. Klistra in det kopierade värdet till den **Single Sign On x509 certifikat** box.

10. Klicka på **spara ändringar**.

11. När du har dessa inställningar du behöver skicka den **domännamn** (t.ex **contoso.com**) via som du loggar in Procore till den [Procore supportteamet](https://support.procore.com/) och de kommer Aktivera federerad enkel inloggning för domänen.

<!--### Next steps

To ensure users can sign-in to Procore SSO after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Procore SSO prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Procore SSO in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Procore SSO users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
       Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-procore-sso-test-user"></a>Skapa en testanvändare Procore SSO

Följ de stegen nedan för att skapa en Procore testanvändare på Procore SSOc sida.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Procore SSO.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Procore SSO**.

    ![Konfigurera enkel inloggning](./media/procoresso-tutorial/tutorial_procoresso_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Procore SSO i åtkomstpanelen du bör få automatiskt loggat in på ditt Procore SSO-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
