---
title: 'Självstudier: Azure Active Directory-integration med Hightail | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Hightail.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e15206ac-74b0-46e4-9329-892c7d242ec0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: jeedes
ms.openlocfilehash: 1151044d5c1002c808ae1214086aff5fad84a55e
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431342"
---
# <a name="tutorial-azure-active-directory-integration-with-hightail"></a>Självstudier: Azure Active Directory-integration med Hightail

I den här självstudien får du lära dig hur du integrerar Hightail med Azure Active Directory (AD Azure).

Integrera Hightail med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Hightail
- Du kan aktivera användarna att automatiskt få loggat in på Hightail (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Hightail, behöver du följande objekt:

- En Azure AD-prenumeration
- En Hightail enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Hightail från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-hightail-from-the-gallery"></a>Att lägga till Hightail från galleriet
För att konfigurera integrering av Hightail i Azure AD, som du behöver lägga till Hightail från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Hightail från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Hightail**.

    ![Skapa en Azure AD-användare för testning](./media/hightail-tutorial/tutorial_hightail_search.png)

1. I resultatpanelen väljer **Hightail**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/hightail-tutorial/tutorial_hightail_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Hightail baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Hightail är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Hightail upprättas.

I Hightail, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Hightail, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Hightail](#creating-a-hightail-test-user)**  – du har en motsvarighet för Britta Simon i Hightail som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Hightail program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Hightail:**

1. I Azure-portalen på den **Hightail** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_samlbase.png)

1. På den **Hightail domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_url.png)

    I den **svars-URL** textrutan skriver du URL: en som: `https://www.hightail.com/samlLogin?phi_action=app/samlLogin&subAction=handleSamlResponse`

    > [!NOTE]
    > Svars-URL-värdet är inte verkliga värdet. Du uppdaterar svars-URL-värdet med faktiska svars-URL som beskrivs senare i självstudien.

1. Kontrollera **visa avancerade URL-inställningar** och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_url1.png)

    I den **inloggning på URL: en** textrutan skriver du URL: en som: `https://www.hightail.com/loginSSO`

1. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_certificate.png) 

1. Hightail program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **”attributet”** fliken av programmet. Följande skärmbild visar ett exempel för detta. 

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_attribute.png) 

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ------------------- | -------------------- |
    | FirstName | User.givenName |
    | LastName | User.surname |
    | E-post | User.Mail |    
    | UserIdentity | User.Mail |
    
    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_officespace_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. Lämna den **Namespace** tom.

    e. Klicka på **OK**.

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_general_400.png)

1. På den **Hightail Configuration** klickar du på **konfigurera Hightail** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_configure.png)

    >[!NOTE]
    >Innan du konfigurerar den enkel inloggning på Hightail app, kan du lista för tillåten din e-postdomän med Hightail team så att alla användare som använder den här domänen kan använda enkel inloggning funktioner.

1. Öppna i ett nytt webbläsarfönster i **Hightail** administrationsportalen.

1. Klicka på **Användarikon** från det övre högra hörnet på sidan. 

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure1.png)

1. Klicka på **visa administratörskonsolen** fliken.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure2.png)

1. Klicka på menyn längst upp i **SAML** fliken och utför följande steg:

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/configure3.png)

    a. I den **inloggnings-URL** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL** kopieras från Azure-portalen.

    b. Öppna din Base64-kodat certifikat i anteckningar som hämtats från Azure-portalen, kopiera innehållet i den till Urklipp och klistra in den till den **SAML-certifikatet** textrutan.

    c. Klicka på **kopia** Kopiera URL för SAML-konsument för din instans och klistra in den i **svars-URL** -textrutan i **Hightail domän och URL: er** avsnittet på Azure-portalen.

    d. Klicka på **spara konfigurationer**.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/hightail-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/hightail-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/hightail-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/hightail-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-hightail-test-user"></a>Skapa en Hightail testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Hightail. 

Det finns inga uppgift åt dig i det här avsnittet. Hightail stöder just-in-time-användaretablering baserat på de anpassade anspråk. Om du har konfigurerat anpassade anspråk som visas i avsnittet **[konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)** ovan, en användare skapas automatiskt i programmet som det inte finns ännu. 

>[!NOTE]
>Om du vill skapa en användare manuellt kan du behöva kontakta den [Hightail supportteamet](mailto:support@hightail.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Hightail.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Hightail, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Hightail**.

    ![Konfigurera enkel inloggning](./media/hightail-tutorial/tutorial_hightail_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Hightail i åtkomstpanelen du bör få automatiskt loggat in på ditt Hightail program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/hightail-tutorial/tutorial_general_01.png
[2]: ./media/hightail-tutorial/tutorial_general_02.png
[3]: ./media/hightail-tutorial/tutorial_general_03.png
[4]: ./media/hightail-tutorial/tutorial_general_04.png

[100]: ./media/hightail-tutorial/tutorial_general_100.png

[200]: ./media/hightail-tutorial/tutorial_general_200.png
[201]: ./media/hightail-tutorial/tutorial_general_201.png
[202]: ./media/hightail-tutorial/tutorial_general_202.png
[203]: ./media/hightail-tutorial/tutorial_general_203.png

