---
title: 'Självstudier: Azure Active Directory-integration med Picturepark | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2017
ms.author: jeedes
ms.openlocfilehash: 2414240d3ab4b5cedce734579f0d39a3df59c3cf
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422203"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Självstudier: Azure Active Directory-integration med Picturepark

I den här självstudien får du lära dig hur du integrerar Picturepark med Azure Active Directory (AD Azure).

Integrera Picturepark med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Picturepark
- Du kan aktivera användarna att automatiskt få loggat in på Picturepark (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Picturepark, behöver du följande objekt:

- En Azure AD-prenumeration
- En Picturepark enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Picturepark från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-picturepark-from-the-gallery"></a>Att lägga till Picturepark från galleriet
För att konfigurera integrering av Picturepark i Azure AD, som du behöver lägga till Picturepark från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Picturepark från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Picturepark**.

    ![Skapa en Azure AD-användare för testning](./media/picturepark-tutorial/tutorial_picturepark_search.png)

1. I resultatpanelen väljer **Picturepark**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/picturepark-tutorial/tutorial_picturepark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Picturepark baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Picturepark är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Picturepark upprättas.

I Picturepark, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Picturepark, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Picturepark](#creating-a-picturepark-test-user)**  – du har en motsvarighet för Britta Simon i Picturepark som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Picturepark program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Picturepark:**

1. I Azure-portalen på den **Picturepark** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_samlbase.png)

1. På den **Picturepark domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.picturepark.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: 
    
    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [Picturepark klienten supportteamet](https://picturepark.com/about/contact/) att hämta dessa värden. 
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet för certifikatet.

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_general_400.png)

1. På den **Picturepark Configuration** klickar du på **konfigurera Picturepark** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_configure.png) 

1. Logga in på webbplatsen Picturepark företag som en administratör i ett annat webbläsarfönster.

1. I verktygsfältet högst upp, klickar du på **Administrationsverktyg**, och klicka sedan på **hanteringskonsolen**.
   
    ![Konsolen Grupprinciphantering](./media/picturepark-tutorial/ic795062.png "-hanteringskonsolen")

1. Klicka på **autentisering**, och klicka sedan på **identitetsprovidrar**.
   
    ![Autentisering](./media/picturepark-tutorial/ic795063.png "autentisering")

1. I den **identitet providerkonfigurationen** avsnittet, utför följande steg:
   
    ![Identitet providerkonfigurationen](./media/picturepark-tutorial/ic795064.png "identitet Providerkonfiguration")
   
    a. Klicka på **Lägg till**.
  
    b. Skriv ett namn för din konfiguration.
   
    c. Välj **ange som standard**.
   
    d. I **utfärdare-URI** textrutan klistra in värdet för **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
   
    e. I **betrodda utfärdare tumavtryck** textrutan klistra in värdet för **tumavtryck** som du har kopierat från **SAML-signeringscertifikat** avsnittet. 

1. Klicka på **JoinDefaultUsersGroup**.

1. Ange den **e-postadress** attribut i den **anspråk** textrutan typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` och klicka på **spara**.

      ![Konfigurationen](./media/picturepark-tutorial/ic795065.png "konfiguration")

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/picturepark-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/picturepark-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/picturepark-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/picturepark-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-picturepark-test-user"></a>Skapa en Picturepark testanvändare

För att aktivera Azure AD-användare att logga in på Picturepark, måste de etableras i Picturepark. När det gäller Picturepark är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **Picturepark** klient.

1. I verktygsfältet högst upp, klickar du på **Administrationsverktyg**, och klicka sedan på **användare**.
   
    ![Användare](./media/picturepark-tutorial/ic795067.png "användare")

1. I den **översikt över användare** fliken **New**.
   
    ![Användarhantering](./media/picturepark-tutorial/ic795068.png "användarhantering")

1. På den **Create User** dialogrutan utför följande steg på en giltig användare för Azure Active Directory som du vill att etablera:
   
    ![Skapa användare](./media/picturepark-tutorial/ic795069.png "skapa användare")
   
    a. I den **e-postadress** textrutan skriver den **e-postadress** användarens **BrittaSimon@contoso.com**.  
   
    b. I den **lösenord** och **Bekräfta lösenord** textrutor, skriver den **lösenord** av BrittaSimon. 
   
    c. I den **Förnamn** textrutan skriver den **Förnamn** användarens **Britta**. 
   
    d. I den **efternamn** textrutan skriver den **efternamn** användarens **Simon**.
   
    e. I den **företagets** textrutan skriver den **företagsnamn** för användaren. 
   
    f. I den **land** textrutan, väljer den **land** för användaren.
  
    g. I den **ZIP** textrutan skriver den **postnummer** på ort.
   
    h. I den **Stad** textrutan skriver den **Ortnamn** för användaren.

    i. Välj en **språk**.
   
    j. Klicka på **Skapa**.

>[!NOTE]
>Du kan använda alla andra Picturepark användare konto verktyg för att skapa eller API: er som tillhandahålls av Picturepark att etablera användarkonton i Azure AD.
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Picturepark.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Picturepark, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Picturepark**.

    ![Konfigurera enkel inloggning](./media/picturepark-tutorial/tutorial_picturepark_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Picturepark i åtkomstpanelen du bör få automatiskt loggat in på ditt Picturepark program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/picturepark-tutorial/tutorial_general_01.png
[2]: ./media/picturepark-tutorial/tutorial_general_02.png
[3]: ./media/picturepark-tutorial/tutorial_general_03.png
[4]: ./media/picturepark-tutorial/tutorial_general_04.png

[100]: ./media/picturepark-tutorial/tutorial_general_100.png

[200]: ./media/picturepark-tutorial/tutorial_general_200.png
[201]: ./media/picturepark-tutorial/tutorial_general_201.png
[202]: ./media/picturepark-tutorial/tutorial_general_202.png
[203]: ./media/picturepark-tutorial/tutorial_general_203.png

