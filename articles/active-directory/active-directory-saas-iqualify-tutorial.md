---
title: "Självstudier: Azure Active Directory-integrering med iQualify LMS | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iQualify LMS."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 6f8a7b7fd155a6ad0df7cb1f9026b4acca2401cb
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Självstudier: Azure Active Directory-integrering med iQualify LMS

I kursen får lära du att integrera iQualify LMS med Azure Active Directory (AD Azure).

Integrera iQualify LMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till iQualify LMS.
- Du kan aktivera användarna att automatiskt hämta loggat in på iQualify LMS (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med iQualify LMS behöver du följande:

- En Azure AD-prenumeration
- En iQualify LMS enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till iQualify LMS från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-iqualify-lms-from-the-gallery"></a>Att lägga till iQualify LMS från galleriet
Du måste lägga till iQualify LMS från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av iQualify LMS i Azure AD.

**Utför följande steg för att lägga till iQualify LMS från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **iQualify LMS**väljer **iQualify LMS** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![iQualify LMS i resultatlistan](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med iQualify LMS baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i iQualify LMS är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i iQualify LMS måste upprättas.

I iQualify LMS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med iQualify LMS, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för iQualify LMS](#create-an-iqualify-lms-test-user)**  – du har en motsvarighet för Britta Simon i iQualify LMS som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt iQualify LMS program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med iQualify LMS:**

1. I Azure-portalen på den **iQualify LMS** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. På den **iQualify LMS domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP initierade läge:

    ![information om iQualify LMS domän URL: er och enkel inloggning](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: 
    | |
    |--|--|
    | Produktionsmiljö:`https://<yourorg>.iqualify.com/`|
    | Testmiljö:`https://<yourorg>.iqualify.io`|
    
    b. I den **Reply URL** textruta Skriv en URL med följande mönster: 
    | |
    |--|--|
    | Produktionsmiljö:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Testmiljö:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![information om iQualify LMS domän URL: er och enkel inloggning](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö:`https://<yourorg>.iqualify.com/login` |
    | Testmiljö:`https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [iQualify LMS klienten supportteamet](https://www.iqualify.com) att hämta dessa värden. 

5. Programmet iQualify LMS förväntar Security Assertion Markup Language (SAML) intyg som ska visas i ett specifikt format. Konfigurera anspråk och hantera värden för attributen i den **användarattribut** avsnitt i iQualify program integration sidan som visas i följande skärmbild:
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-iqualify-tutorial/atb.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan utför följande steg för varje rad som visas i tabellen nedan:
    
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | e-post | User.userPrincipalName |
    | Förnamn | User.givenName |
    | Efternamn | User.surname |
    | person_id | ”din attributet” | 

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-iqualify-tutorial/atb2.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-iqualify-tutorial/atb3.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

    e. Upprepa steg ”a” till ”d” för nästa tabellraderna. 

    > [!Note]
    > Upprepa steg ”a” till ”d” för den **person_id** attributet **valfritt**

7. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base 64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-iqualify-tutorial/tutorial_general_400.png)
    
9. På den **iQualify LMS Configuration** klickar du på **konfigurera iQualify LMS** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![iQualify LMS konfiguration](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Öppna ett nytt webbläsarfönster och sedan logga in på din iQualify miljö som administratör.

11. När du är inloggad på din avatar längst upp till höger Klicka på **”kontoinställningar”.**

    ![Kontoinställningar](./media/active-directory-saas-iqualify-tutorial/setting1.png) 
12. Klicka på menyn till vänster i menyfliksområdet i området konto inställningar och klickar på **”INTEGRERINGAR”.**
    
    ![INTEGRERINGAR](./media/active-directory-saas-iqualify-tutorial/setting2.png)

13. Under INTEGRERINGAR, klicka på den **SAML** ikon.

    ![SAML-ikon](./media/active-directory-saas-iqualify-tutorial/setting3.png)

14. I den **SAML autentiseringsinställningar** dialogrutan utför följande steg:

    ![Inställningar för SAML-autentisering](./media/active-directory-saas-iqualify-tutorial/setting4.png)

    a. I den **SAML SIGN-ON SERVICE-URL för enkel** klistra in den **SAML Sign‑On tjänst-URL för enkel** värde kopieras från Azure AD-konfigurationsfönstret.
    
    b. I den **SAML logga ut URL** klistra in den **Sign‑Out URL** värde kopieras från Azure AD-konfigurationsfönstret.
    
    c. Öppna filen för hämtat certifikat i anteckningar, kopiera innehållet och klistra in den i den **certifikat med offentlig** rutan.
    
    d. I **etikett för inloggningen** anger du namnet för knappen ska visas på inloggningssidan.
    
    e. Klicka på **SPARA**.

    f. Klicka på **uppdatering**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-iqualify-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-iqualify-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-iqualify-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-iqualify-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Skapa en iQualify LMS testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i iQualify. iQualify LMS stöder just‑in‑time användaretablering, som är aktiverad som standard.

Det finns ingen åtgärd objekt i det här avsnittet. Om en användare inte redan finns i iQualify, skapas en ny när du försöker komma åt iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iQualify LMS.

![Tilldela rollen][200] 

**Om du vill tilldela iQualify LMS Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **iQualify LMS**.

    ![Länken iQualify LMS i listan med program](./media/active-directory-saas-iqualify-tutorial/tutorial_iqualify_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Du bör få inloggningssidan för iQualify LMS programmet när du klickar på iQualify LMS panelen på åtkomstpanelen. 

   ![inloggningssidan](./media/active-directory-saas-iqualify-tutorial/login.png) 

Klicka på **logga in med Azure AD** knappen och du bör få automatiskt loggat in på ditt iQualify LMS program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqualify-tutorial/tutorial_general_203.png

