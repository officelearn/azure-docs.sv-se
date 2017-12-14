---
title: "Självstudier: Azure Active Directory-integrering med Adobe upplevelse Manager | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Adobe upplevelse Manager."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: jeedes
ms.openlocfilehash: 4a4fccc4210fd6cf0ddbe99089c84a1fd38d5b09
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Självstudier: Azure Active Directory-integrering med Adobe upplevelse Manager

I kursen får lära du att integrera Adobe upplevelse Manager med Azure Active Directory (AD Azure).

Integrera Adobe upplevelse Manager med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Adobe upplevelse Manager.
- Du kan aktivera användarna att automatiskt hämta inloggade till Adobe upplevelse Manager (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Adobe upplevelse Manager behöver du följande:

- En Azure AD-prenumeration
- En Adobe upplevelse Manager enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till Adobe upplevelse Manager från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Lägger till Adobe upplevelse Manager från galleriet
Du måste lägga till Adobe upplevelse Manager från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Adobe upplevelse Manager i Azure AD.

**Utför följande steg för att lägga till Adobe upplevelse Manager från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Adobe upplevelse Manager**väljer **Adobe upplevelse Manager** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Adobe upplevelse Manager i resultatlistan](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Adobe upplevelse Manager baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Adobe upplevelse Manager till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Adobe upplevelse Manager upprättas.

Adobe upplevelse Manager tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Adobe upplevelse Manager, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Adobe upplevelse Manager](#create-an-adobe-experience-manager-test-user)**  – du har en motsvarighet för Britta Simon i Adobe upplevelse Manager som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Adobe upplevelse Manager-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Adobe upplevelse Manager:**

1. I Azure-portalen på den **Adobe upplevelse Manager** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_samlbase.png)

3. På den **Adobe upplevelse Manager-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera app i **IdP** läge:

    ![URL: er och Adobe upplevelse Manager-domän med enkel inloggning information](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_url1.png)

    a. I den **identifierare** textruta Skriv ett unikt värde som du definierar serverns AEM. 

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:`https://<AEM Server Url>/saml_login`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Adobe upplevelse Manager supportteamet](https://helpx.adobe.com/support/experience-manager.html) att hämta dessa värden.
 
4. Kontrollera att visa avancerade inställningar för URL: en och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Adobe upplevelse Manager-domän med enkel inloggning information](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_spconfigure.png)

    I den **logga URL** textruta skriver Adobe upplevelse Manager-Serveradress. 

5. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_certificate.png) 

6. Klicka på Konfigurera Adobe upplevelse Manager så att konfigurera inloggning fönstret konfigurationsavsnittet Adobe upplevelse Manager. Kopiera den **SAML Sign-On-tjänstens URL**, **SAML enhets-ID** och **Sign-Out ID** från avsnittet Snabbreferens.

    ![Avsnittet konfigurationslänken](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_configure.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_400.png)

8. Öppna **Adobe upplevelse Manager** administrationsportalen i ett nytt webbläsarfönster.

9. Välj **inställningar** -> **säkerhet** -> **användare**.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

10. Välj **administratör** och andra relevanta användare.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

11. Välj **kontoinställningar** -> **skapa/hantera TrustStore**.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

12. Klicka på **Välj certifikatfilen** från **Lägg till certifikat från CER-filen** knappen. Bläddra och välj certifikatfilen, som du har hämtat från Azure-portalen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

13. Certifikatet har lagts till TrustStore. Observera alias för certifikatet.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

14. På **användare** väljer **-Autentiseringstjänsten**.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

15. Välj **kontoinställningar** -> **skapa/hantera KeyStore**. Skapa KeyStore genom att ange ett lösenord.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

16. Gå tillbaka till admin skärmen och välj **inställningar** -> **Operations** -> **webbkonsolen**.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

17. Detta öppnar konfigurationssidan.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

18. Hitta **Adobe Granit SAML 2.0-autentisering hanteraren** och klicka på **Lägg till** ikon.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

19. Utför följande åtgärder på den här sidan.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. I den **sökväg** textruta ange  **/** .

    b. I **IDP URL** textruta anger du värdet för **SAML Sign-On-tjänstens URL**, som du har kopierat från Azure-portalen.

    c. I **IDP certifikat Alias** textruta anger du värdet för **certifikat Alias**, som du har lagt till i TrustStore.

    d. I **säkerhet tillhandahålls enhets-ID** textruta anger du värdet för unika **SAML enhets-ID**, som du har konfigurerat i Azure Portal.

    e. I **Assertion konsument-tjänstens URL** textruta anger du värdet för **Reply URL**, som du har konfigurerat i Azure Portal.

    f. I **Lösenordsarkiv av nyckeln** textruta, ange den **lösenord**, som du har angett i KeyStore.

    g. I **användar-ID för attributet** textruta ange **namn-ID** eller andra användar-ID som är relevant för ditt ärende.

    h. Välj **CRX skapa användare.**

    Jag. I **logga ut URL** textruta anger du värdet för unika **Sign-Out URL** som du har från Azure-portalen.

    j. Klicka på **spara**

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-adobeexperiencemanager-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-an-adobe-experience-manager-test-user"></a>Skapa en testanvändare Adobe upplevelse Manager

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Adobe upplevelse Manager. Om du har valt **skapa CRX användare** alternativet användare skapas automatiskt efter en lyckad autentisering. 

Om du vill skapa användare manuellt kan arbeta med [Adobe upplevelse Manager supportteamet](https://helpx.adobe.com/support/experience-manager.html) att lägga till användare i Adobe upplevelse Manager-plattformen. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Adobe upplevelse Manager.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Adobe upplevelse Manager, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Adobe upplevelse Manager**.

    ![Adobe upplevelse Manager länken i listan med program](./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Adobe upplevelse Manager på åtkomstpanelen du bör få automatiskt loggat in på ditt Adobe upplevelse Manager-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-adobeexperiencemanager-tutorial/tutorial_general_203.png

