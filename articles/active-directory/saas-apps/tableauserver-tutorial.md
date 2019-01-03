---
title: 'Självstudier: Azure Active Directory-integrering med Tableau Server | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2018
ms.author: jeedes
ms.openlocfilehash: c727cddf41c269c214b541134cd9f688017ee687
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789729"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Självstudier: Azure Active Directory-integrering med Tableau Server

I den här självstudien får du lära dig hur du integrerar Tableau Server med Azure Active Directory (AD Azure).

Integrera Tableau Server med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tableau Server.
- Du kan aktivera användarna att automatiskt få loggat in på Tableau Server (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Tableau Server behöver du följande objekt:

- En Azure AD-prenumeration
- En Tableau Server enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Tableau Server från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-tableau-server-from-the-gallery"></a>Att lägga till Tableau Server från galleriet

För att konfigurera integrering av Tableau Server i Azure AD, som du behöver lägga till Tableau Server från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Tableau Server från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Tableau Server**väljer **Tableau Server** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Tableau Server i listan med resultat](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tableau Server baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Tableau Server är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Tableau Server upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Server, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Tableau Server enkel inloggning](#configure-tableau-server-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa Tableau Server testanvändare](#create-tableau-server-test-user)**  – du har en motsvarighet för Britta Simon i Cisco samlingsnamnet som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Tableau Server-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Server:**

1. I Azure-portalen på den **Tableau Server** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial-general-301.png)

3. Tableau Server-programmet förväntar sig ett anpassat anspråk **användarnamn** som måste definieras enligt nedan. Detta används som användar-ID i stället för unik identifierare för användaranspråk. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. Klicka på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde | Namnområde |
    | ---------------| --------------- | ----------- |   
    | användarnamn | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Ange den **Namespace** värde.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

5. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

6. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I textrutan **Inloggnings-URL** anger du en URL med följande mönster: `https://azure.<domain name>.link`
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Föregående värden är inte riktiga värden. Uppdatera värden med faktiska URL och identifierare från sidan Tableau Server konfiguration som beskrivs senare i självstudien.

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara certifikatfilen på datorn.

    ![Länk för nedladdning av certifikatet](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png)

### <a name="configure-tableau-server-single-sign-on"></a>Konfigurera Tableau Server enkel inloggning 

1. För att få SSO konfigurerats för ditt program kan behöva du inloggning till Tableau Server-klienten som en administratör.

2. På den **CONFIGURATION** fliken **användarens identitet och åtkomst**, och välj sedan den **autentisering** metoden fliken.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. På den **CONFIGURATION** utför följande steg:

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. För **autentiseringsmetod**, Välj SAML.
    
    b. Markera kryssrutan för **aktivera SAML-autentisering för servern**.

    c. Tableau Server Retur-URL – URL: en som Tableau Server-användare kommer att använda, till exempel http://tableau_server. Med hjälp av http://localhost rekommenderas inte. Med en URL med ett avslutande snedstreck (till exempel http://tableau_server/) stöds inte. Kopiera **Tableau Server Retur-URL** och klistra in den till Azure AD **inloggning på URL: en** -textrutan i **Tableau Server-domän och URL: er** avsnittet.

    d. SAML entitets-ID – entitets-ID som unikt identifierar din Tableau Server-installation att IDP: N. Du kan ange din Tableau Server URL: en igen här, om du vill, men den behöver inte vara din Tableau Server-URL. Kopiera **SAML entitets-ID** och klistra in den till Azure AD **identifierare** -textrutan i **Tableau Server-domän och URL: er** avsnittet.

    e. Klicka på den **hämta XML-metadatafil** och öppna den i redigeringsprogrammet text. Leta upp URL för Konsumenttjänst för försäkran med Http Post och Index 0 och kopiera URL: en. Klistra in den till Azure AD **svars-URL** -textrutan i **Tableau Server-domän och URL: er** avsnittet.

    f. Leta upp din Federationsmetadata-fil som hämtats från Azure-portalen och ladda upp den i den **IDP: N för SAML-metadatafil**.

    g. Ange namn för de attribut som IDP: N använder för att lagra användarnamn, visningsnamn och e-postadresser.

    h. Klicka på **Spara**

    >[!NOTE] 
    >Kunden behöver ladda upp alla certifikat i Tableau Server SAML SSO-konfigurationen och kommer få ignoreras i flödet för enkel inloggning.
    >Om du behöver för att konfigurera SAML på Tableau Server finns i den här artikeln [konfigurera SAML](https://onlinehelp.tableau.com/v2018.2/server/en-us/saml_config_steps_tsm_ui.htm).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create-aaduser-01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create-aaduser-02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
  
### <a name="create-tableau-server-test-user"></a>Skapa Tableau Server testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Tableau Server. Du måste etablera alla användare i Tableau server. 

Det aktuella användarnamnet för användaren bör matcha värdet som du har konfigurerat i det anpassade attributet Azure AD av **användarnamn**. Integrationen ska fungera med korrekt mappning [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Om du vill skapa en användare manuellt kan behöva du kontakta Tableau Server-administratör i din organisation.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Server.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Tableau Server**.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Tableau Server i åtkomstpanelen du bör få automatiskt loggat in på ditt Tableau Server-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial-general-01.png
[2]: common/tutorial-general-02.png
[3]: common/tutorial-general-03.png
[4]: common/tutorial-general-04.png

[100]: common/tutorial-general-100.png

[201]: common/tutorial-general-201.png
[202]: common/tutorial-general-202.png
[203]: common/tutorial-general-203.png
