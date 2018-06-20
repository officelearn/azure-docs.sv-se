---
title: 'Självstudier: Azure Active Directory-integrering med Adobe upplevelse Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe upplevelse Manager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4279211d130c8fe00a6b89c66222071de480fc52
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36216267"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Självstudier: Azure Active Directory-integrering med Adobe upplevelse Manager

I kursen får lära du att integrera Adobe upplevelse Manager med Azure Active Directory (AD Azure).

Integrera Adobe upplevelse Manager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe upplevelse Manager.
- Du kan aktivera användarna att få loggas in automatiskt till Adobe upplevelse Manager med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Adobe upplevelse Manager behöver du följande:

- En Azure AD-prenumeration
- En Adobe upplevelse Manager enkel inloggning aktiverad prenumeration

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till Adobe upplevelse Manager från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-adobe-experience-manager-from-the-gallery"></a>Lägg till Adobe upplevelse Manager från galleriet
Du måste lägga till Adobe upplevelse Manager från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Adobe upplevelse Manager i Azure AD.

**Vidta följande steg för att lägga till Adobe upplevelse Manager från galleriet:**

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Adobe upplevelse Manager**. Välj **Adobe upplevelse Manager** resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Adobe upplevelse Manager i resultatlistan](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Adobe upplevelse Manager baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i Adobe upplevelse Manager är en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och relaterade användaren i Adobe upplevelse Manager.

Adobe upplevelse Manager ger värde **användarnamn** samma värde i **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna. 

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Adobe upplevelse Manager:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Adobe upplevelse Manager](#create-an-adobe-experience-manager-test-user) har en motsvarighet för Britta Simon i Adobe upplevelse Manager som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Adobe upplevelse Manager-program.

**För att konfigurera Azure AD enkel inloggning med Adobe upplevelse Manager, gör du följande:**

1. I Azure-portalen på den **Adobe upplevelse Manager** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. Aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** nedrullningsbara menyn och väljer **SAML-baserade inloggning**.
 
    ![Enkel inloggning dialogrutan](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. I den **Adobe upplevelse Manager-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera app i **IdP** läge:

    ![URL: er och Adobe upplevelse Manager-domän med enkel inloggning information](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. I den **identifierare** Skriv ett unikt värde som du definierar serverns AEM. 

    b. I den **Reply URL** Skriv en URL med följande mönster: `https://<AEM Server Url>/saml_login`.

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och reply URL. För att få dessa värden kan kontakta den [Adobe upplevelse Manager supportteamet](https://helpx.adobe.com/support/experience-manager.html).
 
4. Kontrollera **visa avancerade inställningar för URL: en**. Sedan gör följande om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Adobe upplevelse Manager-domän med enkel inloggning information](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    I den **logga URL** Skriv din Adobe upplevelse Manager server-URL. 

5. I den **SAML-signeringscertifikat** väljer **certifikat (Base64)**. Spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. För att öppna konfigurationsfönstret inloggning i konfigurationsavsnittet Adobe upplevelse Manager, Välj **Konfigurera Adobe upplevelse Manager**. Kopiera den **SAML Sign-On-tjänstens URL**, **SAML enhets-ID**, och **Sign-Out ID** från avsnittet Snabbreferens.

    ![Avsnittet konfigurationslänken](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Välj **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Öppna i ett nytt webbläsarfönster i **Adobe upplevelse Manager** administrationsportalen.

9. Välj **inställningar** > **säkerhet** > **användare**.

    ![Konfigurera den enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Välj **administratör** och andra relevanta användare.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Välj **kontoinställningar** > **hantera TrustStore**.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Under **Lägg till certifikat från CER-filen**, klickar du på **Välj certifikatfilen**. Bläddra till och markera den certifikatfil som du redan har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certifikatet har lagts till TrustStore. Observera alias för certifikatet.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. På den **användare** väljer **-Autentiseringstjänsten**.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Välj **kontoinställningar** > **skapa/hantera KeyStore**. Skapa KeyStore genom att ange ett lösenord.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Gå tillbaka till skärmen admin. Välj sedan **inställningar** > **Operations** > **webbkonsolen**.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Detta öppnar konfigurationssidan.

    ![Konfigurera den enkel inloggning spara knappen](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

17. Hitta **Adobe Granit SAML 2.0-autentisering hanteraren**. Välj sedan den **Lägg till** ikon.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Vidta följande åtgärder på den här sidan.

    ![Konfigurera enkel inloggning spara](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. I den **sökväg** ange **/**.

    b. I den **IDP URL** anger du den **SAML Sign-On-tjänstens URL** värde som du kopierade från Azure-portalen.

    c. I den **IDP certifikat Alias** anger du den **certifikat Alias** värde som du lade till i TrustStore.

    d. I den **säkerhet tillhandahålls enhets-ID** ange den unika **SAML enhets-ID** värdet som du konfigurerade i Azure-portalen.

    e. I den **Assertion konsument-tjänstens URL** anger du den **Reply URL** värdet som du konfigurerade i Azure-portalen.

    f. I den **Lösenordsarkiv av nyckeln** anger du den **lösenord** som du anger i KeyStore.

    g. I den **användar-ID för attributet** anger du den **namn-ID** eller ett annat användar-ID som är relevant för ditt ärende.

    h. Välj **skapa CRX användare**.

    i. I den **logga ut URL** ange den unika **Sign-Out URL** värde som du har fått från Azure-portalen.

    j. Välj **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken. Komma åt inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan överst i den **alla användare** dialogrutan **Lägg till**.

    ![Knappen Lägg till](./media/adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Dialogrutan användare](./media/adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan. Anteckna det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Skapa en testanvändare Adobe upplevelse Manager

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Adobe upplevelse Manager. Om du har valt den **skapa CRX användare** alternativet användare skapas automatiskt efter en lyckad autentisering. 

Om du vill skapa användare manuellt kan arbeta med den [Adobe upplevelse Manager supportteamet](https://helpx.adobe.com/support/experience-manager.html) att lägga till användare i Adobe upplevelse Manager-plattformen. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till Adobe upplevelse Manager.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Adobe upplevelse Manager, gör du följande:**

1. Öppna program i Azure-portalen. Gå sedan till vyn directory väljer **företagsprogram**, och välj sedan **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Adobe upplevelse Manager**.

    ![Adobe upplevelse Manager länken i listan med program](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

7. I den **Lägg uppdrag** dialogrutan markerar du den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer Adobe upplevelse Manager-panelen i åtkomstpanelen ska hämta loggas du automatiskt till Adobe upplevelse Manager-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/adobeexperiencemanager-tutorial/tutorial_general_203.png

