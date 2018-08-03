---
title: 'Självstudier: Azure Active Directory-integration med Bonusly | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 175c00d36491fbf43149aef9a590219b330581c1
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422985"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Självstudier: Azure Active Directory-integration med Bonusly

I den här självstudien får du lära dig hur du integrerar Bonusly med Azure Active Directory (AD Azure).

Integrera Bonusly med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bonusly
- Du kan aktivera användarna att automatiskt få loggat in på Bonusly (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Bonusly, behöver du följande objekt:

- En Azure AD-prenumeration
- En Bonusly enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Bonusly från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bonusly-from-the-gallery"></a>Att lägga till Bonusly från galleriet
För att konfigurera integrering av Bonusly i Azure AD, som du behöver lägga till Bonusly från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Bonusly från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Bonusly**väljer **Bonusly** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Bonusly i resultatlistan](./media/bonus-tutorial/tutorial_bonusly_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Bonusly baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Bonusly är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Bonusly upprättas.

I Bonusly, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bonusly, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Bonusly testanvändare](#create-a-bonusly-test-user)**  – du har en motsvarighet för Britta Simon i Bonusly som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bonusly program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bonusly:**

1. I Azure-portalen på den **Bonusly** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/bonus-tutorial/tutorial_bonusly_samlbase.png)

1. På den **Bonusly domän och URL: er** avsnittet, utför följande steg:

    ![Bonusly domän och URL: er med enkel inloggning för information](./media/bonus-tutorial/tutorial_bonusly_url.png)

    I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med faktiska svars-URL. Kontakta [Bonusly supportteamet](https://bonus.ly/contact) att hämta värdet.
 
1. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCK** värdet från certifikatet.

    ![Länk för hämtning av certifikat](./media/bonus-tutorial/tutorial_bonusly_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/bonus-tutorial/tutorial_general_400.png)

1. På den **Bonusly Configuration** klickar du på **konfigurera Bonusly** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Bonusly konfiguration](./media/bonus-tutorial/tutorial_bonusly_configure.png) 

1. I ett annat webbläsarfönster, logga in på din **Bonusly** klient.

1. I verktygsfältet högst upp, klickar du på **inställningar**, och välj sedan **integreringar och appar**.
   
    ![Bonusly sociala avsnittet](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Under **enkel inloggning**väljer **SAML**.

1. På den **SAML** dialogrutan utför följande steg:
   
    ![Bonusly Saml dialogrutan sidan](./media/bonus-tutorial/ic773687.png "Bonusly")
   
    a. I den **mål-URL för IDP: N SSO** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.
   
    b. I den **IDP: N utfärdar** textrutan klistra in värdet för **SAML entitets-ID**, som du har kopierat från Azure-portalen. 

    c. I den **inloggnings-URL för IDP: N** textrutan klistra in värdet för **SAML enkel inloggning för tjänst-URL**, som du har kopierat från Azure-portalen.

    d. Klistra in den **tumavtryck** värdet som har kopierats från Azure-portalen till den **Cert fingeravtryck** textrutan.
   
1. Klicka på **Spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](./media/bonus-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![”Användare och grupper” och ”alla användare”-länkar](./media/bonus-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Knappen Lägg till](./media/bonus-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Dialogrutan användare](./media/bonus-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-bonusly-test-user"></a>Skapa en Bonusly testanvändare

För att aktivera Azure AD-användare att logga in på Bonusly, måste de etableras i Bonusly. När det gäller Bonusly är etablering en manuell aktivitet.

>[!NOTE]
>Du kan använda alla andra Bonusly användare konto verktyg för att skapa eller API: er som tillhandahålls av Bonusly att etablera AAD-användarkonton.
>  

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på din Bonusly klient i ett webbläsarfönster.

1. Klicka på **inställningar**.
 
    ![Inställningar för](./media/bonus-tutorial/ic781041.png "inställningar")

1. Klicka på den **användare och bonus** fliken.
   
    ![Användare och bonus](./media/bonus-tutorial/ic781042.png "användare och bonus")

1. Klicka på **hantera användare**.
   
    ![Hantera användare](./media/bonus-tutorial/ic781043.png "hantera användare")

1. Klicka på **lägga till användare**.
   
    ![Lägg till användare](./media/bonus-tutorial/ic781044.png "lägga till användare")

1. På den **Lägg till användare** dialogrutan utför följande steg:
   
    ![Lägg till användare](./media/bonus-tutorial/ic781045.png "lägga till användare")  

    a. I den **Förnamn** textrutan Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textrutan Ange efternamn för användaren som **Simon**.
 
    c. I den **e-post** textrutan Ange e-postadress för användaren som **brittasimon@contoso.com**.

    d. Klicka på **Spara**.
   
     >[!NOTE]
     >Azure AD-kontoinnehavare tar emot ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktiv.
     >  

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bonusly.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Bonusly, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Bonusly**.

    ![Bonusly länken i listan med program](./media/bonus-tutorial/tutorial_bonusly_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Bonusly i åtkomstpanelen, du bör få automatiskt loggat in på ditt Bonusly program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bonus-tutorial/tutorial_general_01.png
[2]: ./media/bonus-tutorial/tutorial_general_02.png
[3]: ./media/bonus-tutorial/tutorial_general_03.png
[4]: ./media/bonus-tutorial/tutorial_general_04.png

[100]: ./media/bonus-tutorial/tutorial_general_100.png

[200]: ./media/bonus-tutorial/tutorial_general_200.png
[201]: ./media/bonus-tutorial/tutorial_general_201.png
[202]: ./media/bonus-tutorial/tutorial_general_202.png
[203]: ./media/bonus-tutorial/tutorial_general_203.png

