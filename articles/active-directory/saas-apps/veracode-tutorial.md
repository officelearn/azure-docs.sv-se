---
title: 'Självstudier: Azure Active Directory-integrering med Veracode | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Veracode.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: de6a9069d73f4cfe55372eae77d712a893cc348c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54817069"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Självstudier: Azure Active Directory-integrering med Veracode

I den här självstudien får du lära dig hur du integrerar Veracode med Azure Active Directory (AD Azure).

Integrera Veracode med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Veracode.
- Du kan aktivera användarna att automatiskt få loggat in på Veracode (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Veracode, behöver du följande objekt:

- En Azure AD-prenumeration
- En Veracode enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Lägg till Veracode från galleriet
1. Konfigurera och testa enkel inloggning med Azure AD

## <a name="add-veracode-from-the-gallery"></a>Lägg till Veracode från galleriet
För att konfigurera integrering av Veracode i Azure AD, som du behöver lägga till Veracode från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Veracode från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Veracode**väljer **Veracode** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Veracode i resultatlistan](./media/veracode-tutorial/tutorial_veracode_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Veracode baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Veracode är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Veracode upprättas.

I Veracode, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Veracode, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
1. **[Skapa en testanvändare Veracode](#create-a-veracode-test-user)**  – du har en motsvarighet för Britta Simon i Veracode som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Veracode program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Veracode:**

1. I Azure-portalen på den **Veracode** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/veracode-tutorial/tutorial_veracode_samlbase.png)

1. På den **Veracode domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure. 

    ![Konfigurera enkel inloggning](./media/veracode-tutorial/tutorial_veracode_url.png)

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/veracode-tutorial/tutorial_veracode_certificate.png) 

1. Målet med det här avsnittet som beskriver hur du aktiverar användare att autentisera till Veracode med ett konto i Azure AD med federation baserat på SAML-protokoll.

    Programmets Veracode förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar till din **saml-tokenattribut** konfiguration. Följande skärmbild visar ett exempel på detta.
    
    ![Attribut](./media/veracode-tutorial/tutorial_veracode_attr.png "Attribut")

1. Om du vill lägga till de nödvändiga attributmappningarna, utför du följande steg:

    | Attributnamn | Attributvärde |
    |--- |--- |
    | förnamn |User.givenName |
    | efternamn |User.surname |
    | e-post |User.mail |
    
    a. För varje datarad i tabellen ovan, klickar du på **lägga till användarattribut**.
    
    ![Attribut](./media/veracode-tutorial/tutorial_veracode_addattr.png "Attribut")
    
    ![Attribut](./media/veracode-tutorial/tutorial_veracode_addattr1.png "Attribut")
    
    b. I den **attributnamnet** textrutan skriver du attributnamnet som visas för den raden.
    
    c. I den **attributvärdet** textrutan väljer attribut-värde som visas för den raden.
    
    d. Klicka på **OK**.

1. Klicka på knappen **Spara**.

    ![Konfigurera enkel inloggning – knappen Spara](./media/veracode-tutorial/tutorial_general_400.png)

1. På den **Veracode Configuration** klickar du på **konfigurera Veracode** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID** från den **Snabbreferens avsnittet.**

    ![Veracode konfiguration](./media/veracode-tutorial/tutorial_veracode_configure.png) 

1. Logga in på webbplatsen Veracode företag som en administratör i ett annat webbläsarfönster.

1. Klicka på menyn längst upp **inställningar**, och klicka sedan på **Admin**.
   
    ![Administration](./media/veracode-tutorial/ic802911.png "Administration")

1. Klicka på den **SAML** fliken.

1. I den **SAML organisationsinställningar** avsnittet, utför följande steg:
   
    ![Administration](./media/veracode-tutorial/ic802912.png "Administration")
   
    a.  I **utfärdare** textrutan klistra in värdet för **SAML entitets-ID** som du har kopierat från Azure-portalen.
    
    b. Om du vill ladda upp nedladdade certifikatet från Azure-portalen, klickar du på **Välj fil**.
   
    c. Välj **aktivera självregistrering**.

1. I den **Self Registreringsinställningar** avsnittet, utför följande steg och klicka sedan på **spara**:
   
    ![Administration](./media/veracode-tutorial/ic802913.png "Administration")
   
    a. Som **aktivera för nya användare**väljer **ingen aktivering krävs**.
   
    b. Som **användardata uppdateras**väljer **inställning Veracode användardata**.
   
    c. För **SAML attributinformation**, väljer du följande:
      * **Användarroller**
      * **Princip för administratör**
      * **Granskare**
      * **Security Lead**
      * **Chef**
      * **Jobbets avsändare**
      * **Skapare**
      * **Alla typer av genomgångar**
      * **Team-medlemskap**
      * **Standard-teamet**

> [!TIP]
> Nu kan du läsa en kortare version av instruktionerna i [Azure Portal](https://portal.azure.com), samtidigt som du konfigurerar appen!  När du har lagt till appen från avsnittet **Active Directory > Företagsprogram**, behöver du bara klicka på fliken **Enkel inloggning**. Du kommer då till den inbäddade dokumentationen via avsnittet **Konfiguration** längst ned. Du kan läsa mer om funktionen för inbäddad dokumentation här: [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/veracode-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/veracode-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/veracode-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/veracode-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-veracode-test-user"></a>Skapa en Veracode testanvändare
För att aktivera Azure AD-användare att logga in på Veracode, måste de etableras i Veracode. När det gäller Veracode är etablering en automatisk uppgift. Det finns inga uppgift åt dig. Användare skapas automatiskt om det behövs under det första enkla inloggning för försöket.

> [!NOTE]
> Du kan använda alla andra Veracode användare konto verktyg för att skapa eller API: er som tillhandahålls av Veracode att etablera användarkonton i Azure AD.
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Veracode.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Veracode, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Veracode**.

    ![Länken Veracode i listan med program](./media/veracode-tutorial/tutorial_veracode_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Veracode i åtkomstpanelen du bör få automatiskt loggat in på ditt Veracode program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/veracode-tutorial/tutorial_general_01.png
[2]: ./media/veracode-tutorial/tutorial_general_02.png
[3]: ./media/veracode-tutorial/tutorial_general_03.png
[4]: ./media/veracode-tutorial/tutorial_general_04.png

[100]: ./media/veracode-tutorial/tutorial_general_100.png

[200]: ./media/veracode-tutorial/tutorial_general_200.png
[201]: ./media/veracode-tutorial/tutorial_general_201.png
[202]: ./media/veracode-tutorial/tutorial_general_202.png
[203]: ./media/veracode-tutorial/tutorial_general_203.png

