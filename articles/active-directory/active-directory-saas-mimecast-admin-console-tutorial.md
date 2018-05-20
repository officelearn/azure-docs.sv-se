---
title: 'Självstudier: Azure Active Directory-integrering med Mimecast administratörskonsolen | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Mimecast-administrationskonsolen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 81c50614-f49b-4bbc-97d5-3cf77154305f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: jeedes
ms.openlocfilehash: bef0312a3ee44a441f44eb2ae7e4292b966cec3f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-admin-console"></a>Självstudier: Azure Active Directory-integrering med Mimecast-administrationskonsolen

I kursen får lära du att integrera Mimecast administratörskonsolen med Azure Active Directory (AD Azure).

Integrera Mimecast administratörskonsolen med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Mimecast-administrationskonsolen.
- Du kan aktivera användarna att automatiskt hämta loggat in på administratörskonsolen för Mimecast (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Mimecast administratörskonsolen, behöver du följande:

- En Azure AD-prenumeration
- En Mimecast administratörskonsolen enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Administratörskonsolen för att lägga till Mimecast från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-mimecast-admin-console-from-the-gallery"></a>Administratörskonsolen för att lägga till Mimecast från galleriet
Du måste lägga till Mimecast administratörskonsolen från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Mimecast administrationskonsolen i Azure AD.

**Utför följande steg för att lägga till Mimecast administratörskonsolen från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Mimecast administratörskonsolen**väljer **Mimecast administratörskonsolen** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Mimecast administrationskonsolen i resultatlistan](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med Mimecast administratörskonsolen baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i administratörskonsolen för Mimecast till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i administratörskonsolen för Mimecast upprättas.

I administratörskonsolen för Mimecast tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Mimecast administratörskonsolen måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Mimecast administratörskonsolen](#create-a-mimecast-admin-console-test-user)**  – du har en motsvarighet för Britta Simon i administratörskonsolen för Mimecast som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Mimecast-administrationskonsolen.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Mimecast administratörskonsolen:**

1. I Azure-portalen på den **Mimecast administratörskonsolen** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_samlbase.png)

3. På den **Mimecast Admin Console domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och domänen för Mimecast Admin-konsolen med enkel inloggning information](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_url.png)

    I den **inloggnings-URL** textruta anger du URL:
    | |
    | -- |
    | `https://webmail-uk.mimecast.com`|
    | `https://webmail-us.mimecast.com`|

    > [!NOTE] 
    > URL: en för inloggning är regionspecifika.

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_400.png)

6. På den **Mimecast konsolen Administratörskonfigurationen** klickar du på **konfigurera Mimecast administratörskonsolen** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Mimecast Administratörskonfigurationen-konsolen](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_configure.png) 

7. Logga in på ditt Mimecast administratörskonsolen som en administratör i en annan webbläsarfönster.

8. Gå till **Services \> programmet**.

    ![Tjänster](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794998.png "tjänster")

9. Klicka på **autentisering profiler**.

    ![Profiler för autentisering](./media/active-directory-saas-mimecast-admin-console-tutorial/ic794999.png "profiler för autentisering")
    
10. Klicka på **nya autentiseringsprofil**.

    ![Nya autentisering profiler](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795000.png "nya profiler för autentisering")

11. I den **autentiseringsprofil** avsnittet, utför följande steg:

    ![Autentiseringsprofil](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795015.png "autentiseringsprofil")
    
    a. I den **beskrivning** textruta, ange ett namn för din konfiguration.
    
    b. Välj **genomdriva SAML-autentisering för Mimecast administratörskonsolen**.
    
    c. Som **Provider**väljer **Azure Active Directory**.
    
    d. Klistra in **SAML enhets-ID**, som du har kopierat från Azure-portalen i den **utfärdar-URL** textruta.
    
    e. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **inloggnings-URL** textruta.

    f. Klistra in **SAML enkel inloggning Tjänstwebbadress**, som du har kopierat från Azure-portalen i den **logga ut URL** textruta.
    
    >[!NOTE]
    >Inloggnings-URL-värdet och logga ut URL-värdet är för Administrationskonsolen Mimecast desamma.
    
    g. Öppna base-64-certifikat hämtas från Azure-portalen i anteckningar, ta bort den första raden (”*--*”) och den sista raden (”*--*”), kopiera det återstående innehållet i den till Urklipp och klistra in den till den **identitetscertifikat Provider (Metadata)** textruta.
    
    h. Välj **tillåta enkel inloggning på**.
    
    i. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985) 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-mimecast-admin-console-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-mimecast-admin-console-test-user"></a>Skapa en testanvändare Mimecast administratörskonsolen

För att aktivera Azure AD-användare att logga in på administratörskonsolen för Mimecast etableras de i Mimecast-administrationskonsolen. När det gäller Mimecast administrationskonsolen är etablering en manuell aktivitet.

* Du måste registrera en domän innan du kan skapa användare.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Mimecast administratörskonsolen** som administratör.
2. Gå till **kataloger \> interna**.
   
   ![Kataloger](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795003.png "kataloger")
3. Klicka på **registrera nya domän**.
   
   ![Registrera en ny domän](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795004.png "registrera ny domän")
4. När den nya domänen har skapats, klickar du på **ny adress**.
   
   ![Ny adress](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795005.png "ny adress")
5. I dialogrutan Ny adress utför du följande steg:
   
   ![Spara](./media/active-directory-saas-mimecast-admin-console-tutorial/ic795006.png "spara")
   
   a. Typ av **e-postadress**, **globalt namn**, **lösenord**, och **Bekräfta lösenord** attribut för en giltig Azure AD-kontot du vill etablera i relaterade textrutor.

   b. Klicka på **Spara**.

>[!NOTE]
>Du kan använda andra Mimecast administratörskonsolen användare skapa verktyg eller API: er som tillhandahålls av Mimecast administratörskonsolen för att etablera Azure AD-användarkonton. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till administratörskonsolen för Mimecast.

![Tilldela rollen][200] 

**Om du vill tilldela Mimecast administratörskonsolen Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Mimecast administratörskonsolen**.

    ![Länken Mimecast administrationskonsolen i listan med program](./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_mimecastadminconsole_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Mimecast administrationskonsolen på åtkomstpanelen du ska hämta automatiskt inloggade i tillämpningsprogrammet Mimecast-administrationskonsolen.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-admin-console-tutorial/tutorial_general_203.png

