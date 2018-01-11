---
title: "Självstudier: Azure Active Directory-integrering med SilkRoad livslängd Suite | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SilkRoad livslängd Suite."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 0d6af7af7d6b28ff3ea9d518a65b8267a83b71b4
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Självstudier: Azure Active Directory-integrering med SilkRoad livslängd Suite

I kursen får lära du att integrera SilkRoad livslängd Suite med Azure Active Directory (AD Azure).

Integrera SilkRoad livslängd Suite med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SilkRoad livslängd Suite.
- Du kan aktivera användarna att automatiskt hämta loggat in på SilkRoad livslängd Suite (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med SilkRoad livslängd Suite, behöver du följande:

- En Azure AD-prenumeration
- En SilkRoad livslängd Suite enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SilkRoad livslängd Suite från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>Att lägga till SilkRoad livslängd Suite från galleriet
Du måste lägga till SilkRoad livslängd Suite från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SilkRoad livslängd Suite i Azure AD.

**Utför följande steg för att lägga till SilkRoad livslängd Suite från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SilkRoad livslängd Suite**väljer **SilkRoad livslängd Suite** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SilkRoad livslängd Suite i resultatlistan](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med SilkRoad livslängd Suite baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SilkRoad livslängd Suite motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SilkRoad livslängd Suite upprättas.

I SilkRoad livslängd Suite tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SilkRoad livslängd Suite, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SilkRoad livslängd Suite](#create-a-silkroad-life-suite-test-user)**  – har en motsvarighet för Britta Simon SilkRoad livslängd Suite som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt SilkRoad livslängd Suite-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SilkRoad livslängd Suite:**

1. I Azure-portalen på den **SilkRoad livslängd Suite** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. På den **SilkRoad livslängd Suite domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och SilkRoad livslängd Suite domän med enkel inloggning information](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<subdomain>.silkroad-eng.com/Authentication/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [SilkRoad livslängd Suite klienten supportteamet](https://www.silkroad.com/locations/) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. På den **SilkRoad livslängd Suite Configuration** klickar du på **konfigurera SilkRoad livslängd Suite** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfiguration av SilkRoad livslängd programsvit](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Inloggning på webbplatsen SilkRoad företag som administratör. 
 
    >[!NOTE] 
    > För att få åtkomst till programmet SilkRoad livslängd Suite autentisering för att konfigurera federation med Microsoft Azure AD kan kontakta SilkRoad Support eller genom ditt ombud SilkRoad tjänster.

8. Gå till **tjänstleverantör**, och klicka sedan på **Federation information**. 
   
    ![Azure AD-Single Sign-On][10]

9. Klicka på **hämta Federationsmetadata**, och spara sedan metadatafilen på datorn.
   
    ![Azure AD-Single Sign-On][11] 

10. I din **SilkRoad** programmet, klickar du på **autentisering källor**.
   
    ![Azure AD-Single Sign-On][12] 

11. Klicka på **Lägg till autentiseringskälla**. 
   
    ![Azure AD-Single Sign-On][13] 

12. I den **Lägg till autentiseringskälla** avsnittet, utför följande steg: 
   
    ![Azure AD-Single Sign-On][14]
  
    a. Under **alternativ 2 - metadatafil**, klickar du på **Bläddra** att överföra metadatafilen hämtas från Azure-portalen.
  
    b. Klicka på **skapa identitetsleverantör med hjälp av fildata**.

13. I den **autentisering källor** klickar du på **redigera**. 
    
     ![Azure AD-Single Sign-On][15] 

14. På den **redigera autentiseringskälla** dialogrutan, utför följande steg: 
    
     ![Azure AD-Single Sign-On][16] 

    a. Som **aktiverad**väljer **Ja**.

    b. I den **ID för entiteterna** textruta klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.
   
    c. I den **IdP beskrivning** textruta skriver du en beskrivning för konfigurationen (till exempel: *Azure AD SSO*).

    d. I den **metadatafil** textruta överför den **metadata** -fil som du har hämtat från Azure-portalen.
  
    e. I den **IdP namnet** textruta, ange ett namn som är specifik för din konfiguration (till exempel: *Azure SP*).
  
    f. I den **logga ut Tjänstwebbadress** textruta klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.

    g. I den **Tjänstwebbadress inloggning** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    h. Klicka på **Spara**.

15. Inaktivera alla autentisering källor. 
    
     ![Azure AD-Single Sign-On][17]

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>Skapa en testanvändare SilkRoad livslängd Suite

I det här avsnittet kan du skapa en användare som kallas Britta Simon i SilkRoad livslängd Suite. Arbeta med [SilkRoad livslängd Suite klienten supportteamet](https://www.silkroad.com/locations/) att lägga till användare i SilkRoad livslängd Suite-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SilkRoad livslängd Suite.

![Tilldela rollen][200] 

**Om du vill tilldela SilkRoad livslängd Suite Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SilkRoad livslängd Suite**.

    ![Länken SilkRoad livslängd Suite i listan med program](./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SilkRoad livslängd Suite på åtkomstpanelen du bör få automatiskt loggat in på ditt SilkRoad livslängd Suite-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/active-directory-saas-silkroad-life-suite-tutorial/tutorial_silkroad_13.png
