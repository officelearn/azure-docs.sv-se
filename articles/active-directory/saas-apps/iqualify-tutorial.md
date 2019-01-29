---
title: 'Självstudier: Azure Active Directory-integrering med iQualify LMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iQualify LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 74242d5e539824b913a68a7a1116b16565fd1253
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55170129"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Självstudier: Azure Active Directory-integrering med iQualify LMS

I den här självstudien får du lära dig hur du integrerar iQualify LMS med Azure Active Directory (AD Azure).

Integrera iQualify LMS med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till iQualify LMS.
- Du kan aktivera användarna att automatiskt få loggat in på iQualify LMS (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med iQualify LMS, behöver du följande objekt:

- En Azure AD-prenumeration
- En iQualify LMS-enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till iQualify LMS från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-iqualify-lms-from-the-gallery"></a>Att lägga till iQualify LMS från galleriet
För att konfigurera integrering av iQualify LMS i Azure AD, som du behöver lägga till iQualify LMS från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till iQualify LMS från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **iQualify LMS**väljer **iQualify LMS** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![iQualify LMS i resultatlistan](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med iQualify LMS baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilka motsvarande användaren i iQualify LMS är att en användare i Azure AD. Med andra ord en länk relationen mellan en Azure AD-användare och relaterade användaren i iQualify LMS måste upprättas.

I iQualify LMS, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med iQualify LMS, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare för iQualify LMS](#create-an-iqualify-lms-test-user)**  – du har en motsvarighet för Britta Simon i iQualify LMS som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt iQualify LMS-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med iQualify LMS:**

1. I Azure-portalen på den **iQualify LMS** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

1. På den **iQualify LMS-domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP-initierad läge:

    ![information om iQualify LMS-domän och URL: er enkel inloggning](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. I textrutan **Identifierare** anger du en URL med följande mönster: 
    | |
    |--|--|
    | Produktionsmiljö: `https://<yourorg>.iqualify.com/`|
    | Test-miljö: `https://<yourorg>.iqualify.io`|
    
    b. I textrutan **Svars-URL** anger du en URL med följande mönster: 
    | |
    |--|--|
    | Produktionsmiljö: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Test-miljö: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![information om iQualify LMS-domän och URL: er enkel inloggning](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    I textrutan **Inloggnings-URL** anger du en URL med följande mönster:
    | |
    |--|--|
    | Produktionsmiljö: `https://<yourorg>.iqualify.com/login` |
    | Test-miljö: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [iQualify LMS-klienten supportteamet](https://www.iqualify.com) att hämta dessa värden. 

1. IQualify LMS-programmet förväntar sig Security Assertion Markup Language (SAML) intyg som ska visas i ett visst format. Konfigurera anspråk och hantera värden för attributen i den **användarattribut** på iQualify application integrationssidan, enligt följande skärmbild:
    
    ![Konfigurera enkel inloggning](./media/iqualify-tutorial/atb.png)

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan utför följande steg för varje rad som visas i tabellen nedan:
    
    | Attributnamn | Attributvärde |
    | --- | --- |    
    | e-post | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | ”din attributet” | 

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/iqualify-tutorial/atb2.png)

    ![Konfigurera enkel inloggning](./media/iqualify-tutorial/atb3.png)
    
    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **Ok**

    e. Upprepa steg ”a” till ”d” för nästa tabellraderna. 

    > [!Note]
    > Upprepa stegen ”a” till ”d” för den **person_id** attributet är **valfritt**

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning – knappen Spara](./media/iqualify-tutorial/tutorial_general_400.png)
    
1. På den **iQualify LMS Configuration** klickar du på **konfigurera iQualify LMS** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![iQualify LMS-konfiguration](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

1.  Öppna ett nytt webbläsarfönster och logga sedan in på din iQualify miljö som administratör.

1. När du är inloggad, klicka på din avatar längst upp till höger och sedan klicka på **”kontoinställningar”.**

    ![Kontoinställningar](./media/iqualify-tutorial/setting1.png) 
1. I området konto inställningar klickar du på menyfliksområdet-menyn till vänster och klicka på **”INTEGRERINGAR”.**
    
    ![INTEGRERINGAR](./media/iqualify-tutorial/setting2.png)

1. Under INTEGRERINGAR, klickar du på den **SAML** ikon.

    ![SAML-ikon](./media/iqualify-tutorial/setting3.png)

1. I den **SAML autentiseringsinställningar** dialogrutan utför följande steg:

    ![Inställningar för SAML-autentisering](./media/iqualify-tutorial/setting4.png)

    a. I den **URL för SAML enkel inloggnings-tjänsten** rutan, klistra in den **URL för SAML enkel Sign‑On Service** värdet kopieras från konfigurationsfönstret för Azure AD-program.
    
    b. I den **URL för SAML-UTLOGGNING** rutan, klistra in den **Sign‑Out URL** värdet kopieras från konfigurationsfönstret för Azure AD-program.
    
    c. Öppna filen nedladdade certifikatet i anteckningar, kopiera innehållet och klistra in den i den **OFFENTLIGT certifikat** box.
    
    d. I **etikett för inloggning** anger du namnet för knappen som ska visas på inloggningssidan.
    
    e. Klicka på **SPARA**.

    f. Klicka på **uppdatering**.

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/iqualify-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/iqualify-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/iqualify-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/iqualify-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-iqualify-lms-test-user"></a>Skapa en iQualify LMS-testanvändare

I det här avsnittet skapas en användare som kallas Britta Simon i iQualify. iQualify LMS stöder just‑in‑time etableringen av användare, som är aktiverat som standard.

Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i iQualify, skapas en ny när du försöker komma åt iQualify LMS.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iQualify LMS.

![Tilldela rollen][200] 

**Om du vill tilldela iQualify LMS Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **iQualify LMS**.

    ![IQualify LMS-länken i listan med program](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på iQualify LMS-panelen i åtkomstpanelen, bör du få inloggningssidan programmets iQualify LMS. 

   ![inloggningssidan](./media/iqualify-tutorial/login.png) 

Klicka på **inloggning med Azure AD** du bör få automatiskt loggat in på ditt iQualify LMS-program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png

