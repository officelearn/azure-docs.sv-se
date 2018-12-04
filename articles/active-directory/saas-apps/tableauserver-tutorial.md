---
title: 'Självstudier: Azure Active Directory-integration med Tableau Server | Microsoft Docs'
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
ms.date: 11/29/2018
ms.author: jeedes
ms.openlocfilehash: 84ea1d999a26ce0ce1d548da92549c6a718d5978
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52850371"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Självstudier: Azure Active Directory-integration med Tableau Server

I den här självstudien får du lära dig hur du integrerar Tableau Server med Azure Active Directory (AD Azure).

Integrera Tableau Server med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Tableau Server.
- Du kan aktivera användarna att automatiskt få loggat in på Tableau Server (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

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
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Tableau Server**väljer **Tableau Server** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Tableau Server i listan med resultat](./media/tableauserver-tutorial/tutorial-tableauserver-addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Tableau Server baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Tableau Server är att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Tableau Server upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Tableau Server, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Tableau Server](#creating-a-tableau-server-test-user)**  – du har en motsvarighet för Britta Simon i Tableau Server som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Tableau Server-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Tableau Server:**

1. I Azure-portalen på den **Tableau Server** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial-general-301.png)

3. Tableau Server-programmet förväntar sig ett anpassat anspråk **användarnamn** som måste definieras enligt nedan. Detta används som användar-ID i stället för unik identifierare för användaranspråk. Du kan hantera värdena för dessa attribut från den **användarattribut och anspråk** avsnitt på sidan för integrering av programmet. Klicka på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-attribute.png)

4. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | användarnamn | User.userPrincipalName |

    a. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-add-attribute.png)

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-manage-attribute.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Ange den **Namespace** värde.

    d. Välj källa som **attributet**.

    e. Från den **källattribut** anger attributvärdet som visas för den raden.

    f. Klicka på **Spara**.

5. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

6. På den **SAML grundkonfiguration** avsnittet, utför följande steg:

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link`
    
    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link`

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    ![image](./media/tableauserver-tutorial/tutorial-tableauserver-url.png)
     
    > [!NOTE] 
    > Föregående värden är inte riktiga värden. Uppdatera värden med faktiska URL och identifierare från sidan Tableau Server konfiguration som beskrivs senare i självstudien.

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned **Federation Metadata XML** och spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/tableauserver-tutorial/tutorial-tableauserver-certificate.png) 

8. För att få SSO konfigurerats för ditt program kan behöva du inloggning till Tableau Server-klienten som en administratör.

9. På den **Tableau serverkonfiguration** utför följande steg:
   
    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-001.png)

    a. I Tableau Server-konfiguration klickar du på den **SAML** fliken. 
  
    b. Markera kryssrutan för **Använd SAML för enkel inloggning**.
   
    c. Tableau Server Retur-URL – URL: en som Tableau Server-användare kommer att använda, till exempel http://tableau_server. Med hjälp av http://localhost rekommenderas inte. Med en URL med ett avslutande snedstreck (till exempel http://tableau_server/) stöds inte. Kopiera **Tableau Server Retur-URL** och klistra in den till Azure AD **inloggning på URL: en** -textrutan i **Tableau Server-domän och URL: er** avsnittet.
   
    d. SAML entitets-ID – entitets-ID som unikt identifierar din Tableau Server-installation att IDP: N. Du kan ange din Tableau Server URL: en igen här, om du vill, men den behöver inte vara din Tableau Server-URL. Kopiera **SAML entitets-ID** och klistra in den till Azure AD **identifierare** -textrutan i **Tableau Server-domän och URL: er** avsnittet.
     
    e. Klicka på den **exportera metadatafil** och öppna den i redigeringsprogrammet text. Leta upp URL för Konsumenttjänst för försäkran med Http Post och Index 0 och kopiera URL: en. Klistra in den till Azure AD **svars-URL** -textrutan i **Tableau Server-domän och URL: er** avsnittet.
   
    f. Leta upp din Federationsmetadata-fil som hämtats från Azure-portalen och ladda upp den i den **IDP: N för SAML-metadatafil**.
   
    g. Klicka på den **OK** knappen Tableau Server konfigurationssida.
   
    >[!NOTE] 
    >Kunden behöver ladda upp alla certifikat i Tableau Server SAML SSO-konfigurationen och kommer få ignoreras i flödet för enkel inloggning.
    >Om du behöver för att konfigurera SAML på Tableau Server finns i den här artikeln [konfigurera SAML](https://onlinehelp.tableau.com/current/server/en-us/config_saml.htm).

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create-aaduser-01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create-aaduser-02.png)

    a. I den **namn** anger **BrittaSimon**.
  
    b. I den **användarnamn** skriver **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
  
### <a name="creating-a-tableau-server-test-user"></a>Skapa en testanvändare Tableau Server

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Tableau Server. Du måste etablera alla användare i Tableau server. 

Det aktuella användarnamnet för användaren bör matcha värdet som du har konfigurerat i det anpassade attributet Azure AD av **användarnamn**. Integrationen ska fungera med korrekt mappning [konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on).

>[!NOTE]
>Om du vill skapa en användare manuellt kan behöva du kontakta Tableau Server-administratör i din organisation.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Tableau Server.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **Tableau Server**.

    ![Konfigurera enkel inloggning](./media/tableauserver-tutorial/tutorial-tableauserver-app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

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
