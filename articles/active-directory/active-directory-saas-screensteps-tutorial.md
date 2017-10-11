---
title: "Självstudier: Azure Active Directory-integrering med ScreenSteps | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ScreenSteps."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: b6ded8ba1adf03fdccbdb7573c09fae1857c8b16
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Självstudier: Azure Active Directory-integrering med ScreenSteps

I kursen får lära du att integrera ScreenSteps med Azure Active Directory (AD Azure).

Integrera ScreenSteps med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ScreenSteps.
- Du kan aktivera användarna att automatiskt hämta loggat in på ScreenSteps (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med ScreenSteps, behöver du följande:

- En Azure AD-prenumeration
- En ScreenSteps enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till ScreenSteps från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-screensteps-from-the-gallery"></a>Att lägga till ScreenSteps från galleriet
Du måste lägga till ScreenSteps från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av ScreenSteps i Azure AD.

**Utför följande steg för att lägga till ScreenSteps från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **ScreenSteps**väljer **ScreenSteps** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![ScreenSteps i resultatlistan](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med ScreenSteps baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i ScreenSteps motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ScreenSteps upprättas.

I ScreenSteps, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med ScreenSteps, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare ScreenSteps](#create-a-screensteps-test-user)**  – du har en motsvarighet för Britta Simon i ScreenSteps som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt ScreenSteps program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ScreenSteps:**

1. I Azure-portalen på den **ScreenSteps** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_samlbase.png)

3. På den **ScreenSteps domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och ScreenSteps domän med enkel inloggning information](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<tenantname>.ScreenSteps.com`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL, som beskrivs senare i den här kursen. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-screensteps-tutorial/tutorial_general_400.png)

6. På den **ScreenSteps Configuration** klickar du på **konfigurera ScreenSteps** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![ScreenSteps konfiguration](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_configure.png) 

7. Logga in på webbplatsen ScreenSteps företag som en administratör i en annan webbläsarfönster.

8. Klicka på **kontoinställningar**.

    ![Kontohantering](./media/active-directory-saas-screensteps-tutorial/ic778523.png "kontohantering")

9. Klicka på **enkel inloggning**.

    ![Fjärrautentiseringen](./media/active-directory-saas-screensteps-tutorial/ic778524.png "Remote authentication")

10. Klicka på **skapa slutpunkten för enkel inloggning**.

    ![Fjärrautentiseringen](./media/active-directory-saas-screensteps-tutorial/ic778525.png "Remote authentication")

11. I den **skapa enkel inloggning Endpoint** avsnittet, utför följande steg:

    ![Skapa en slutpunkt för autentisering](./media/active-directory-saas-screensteps-tutorial/ic778526.png "skapa en slutpunkt för autentisering")
    
    a. I den **rubrik** textruta skriver du ett namn.
    
    b. Från den **läge** väljer **SAML**.
    
    c. Klicka på **Skapa**.

12. **Redigera** ny slutpunkt.

    ![Redigera endpoint](./media/active-directory-saas-screensteps-tutorial/ic778528.png "redigera slutpunkt")

13. I den **Redigera enkel inloggning Endpoint** avsnittet, utför följande steg:

    ![Remote autentiseringsslutpunkten](./media/active-directory-saas-screensteps-tutorial/ic778527.png "Remote authentication slutpunkt")

    a. Klicka på **överför ny SAML certifikatfilen**, och sedan ladda upp certifikatet som du har hämtat från Azure-portalen.
    
    b. Klistra in **SAML enkel inloggning Tjänstwebbadress** -värde som du har kopierat från Azure-portalen i den **Remote inloggnings-URL** textruta.
    
    c. Klistra in **Sign-Out URL** -värde som du har kopierat från Azure-portalen i den **URL för utloggning** textruta.
    
    d. Välj en **grupp** att tilldela användare till när de har etablerats.
    
    e. Klicka på **uppdatering**.

    f. Kopiera den **konsument-URL för SAML** till Urklipp och klistra in till den **inloggnings-URL** TextBox-kontroll i **ScreenSteps domän och URL: er** avsnitt.
    
    g. Gå tillbaka till den **redigera slutpunkten för enkel inloggning**.
    
    h. Klicka på den **göra standard för kontot** om du vill använda den här slutpunkten för alla användare som loggar in i ScreenSteps. Du kan också klicka på den **lägga till webbplatsen** om du vill använda den här slutpunkten för specifika platser i **ScreenSteps**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-screensteps-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-screensteps-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-screensteps-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-screensteps-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-screensteps-test-user"></a>Skapa en testanvändare ScreenSteps

I det här avsnittet skapar du en användare som kallas Britta Simon i ScreenSteps. Arbeta med [ScreenSteps klienten supportteamet](https://www.screensteps.com/contact) att lägga till användare i ScreenSteps-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ScreenSteps.

![Tilldela rollen][200] 

**Om du vill tilldela ScreenSteps Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **ScreenSteps**.

    ![Länken ScreenSteps i listan med program](./media/active-directory-saas-screensteps-tutorial/tutorial_screensteps_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen ScreenSteps på åtkomstpanelen du bör få automatiskt loggat in på ditt ScreenSteps program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-screensteps-tutorial/tutorial_general_203.png

