---
title: 'Självstudier: Azure Active Directory-integration med Kantega SSO för FishEye/genom | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Kantega SSO för FishEye/genom.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 9fe951fd-1530-4d33-a1a4-390385b99ce9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 833c469f70b60eb0a95e65af17956030eed6ee0b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39446046"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-fisheyecrucible"></a>Självstudier: Azure Active Directory-integration med Kantega SSO för FishEye/genom

I den här självstudien får du lära dig hur du integrerar Kantega SSO för FishEye/genom med Azure Active Directory (AD Azure).

Integrera Kantega SSO för FishEye/genom med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Kantega SSO för FishEye/genom
- Du kan aktivera användarna att automatiskt få loggat in på Kantega SSO för FishEye/genom (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Kantega SSO för FishEye/genom, behöver du följande objekt:

- En Azure AD-prenumeration
- En Kantega SSO för FishEye/genom enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Kantega SSO för FishEye/genom från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kantega-sso-for-fisheyecrucible-from-the-gallery"></a>Att lägga till Kantega SSO för FishEye/genom från galleriet
Om du vill konfigurera integreringen av Kantega SSO för FishEye/genom till Azure AD, som du behöver lägga till Kantega SSO för FishEye/genom från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Kantega SSO för FishEye/genom från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Kantega SSO för FishEye/genom**.

    ![Skapa en Azure AD-användare för testning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_search.png)

1. I resultatpanelen väljer **Kantega SSO för FishEye/genom**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Kantega SSO för FishEye/genom baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Kantega SSO för FishEye/genom att en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Kantega SSO för FishEye/genom upprättas.

I Kantega SSO för FishEye/genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Kantega SSO för FishEye/genom, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en Kantega SSO för FishEye/genom testanvändare](#creating-a-kantega-sso-for-fisheyecrucible-test-user)**  – du har en motsvarighet för Britta Simon i Kantega SSO för FishEye/genom som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din Kantega SSO för FishEye/genom program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Kantega SSO för FishEye/genom:**

1. I Azure-portalen på den **Kantega SSO för FishEye/genom** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_samlbase.png)

1. I **IDP** initiera läge för den **Kantega SSO FishEye/genom domän och URL: er** avsnittet utföra följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url1.png)

    a. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

1. I **SP** initierad läge, kontrollera **visa avancerade URL-inställningar** och utföra följande steg:

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_url2.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska identifierare, svars-URL och inloggnings-URL. Dessa värden tas emot under konfigurationen av FishEye/genom plugin-programmet som beskrivs senare i självstudien.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_400.png)
    
1. I ett annat webbläsarfönster, logga in på din FishEye/genom en lokal server som administratör.

1. Hovra över kugghjulet och klicka på den **tillägg**.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon1.png)

1. Under avsnittet inställningar klickar du på **hitta nya tillägg**. 

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/add-on2.png)

1. Sök **Kantega SSO för genom** och klicka på **installera** knappen för att installera den nya SAML-plugin-programmet.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon2.png)

1. Installationen av plugin-programmet startar. 

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon33.png)

1. När installationen är klar. Klicka på **Stäng**.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon34.png)

1.  Klicka på **Hantera**.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon35.png)

1. Klicka på **konfigurera** att konfigurera nya plugin-programmet. 

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon3.png)

1. I den **SAML** avsnittet. Välj **Azure Active Directory (Azure AD)** från den **Lägg till identitetsprovider** listrutan.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon4.png)

1. Välj prenumerationsnivå som **grundläggande**.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon5.png)

1. På den **appegenskaper** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon6.png)

    a. Kopiera den **Appidentitets-URI** värde och använda det som **identifierare, svars-URL och inloggnings-URL** på den **Kantega SSO FishEye/genom domän och URL: er** avsnitt i Azure-portalen.

    b. Klicka på **Nästa**.

1. På den **Metadata import** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon7.png)

    a. Välj **Metadata-filen på datorn**, och ladda upp metadatafilen, som du har hämtat från Azure-portalen.

    b. Klicka på **Nästa**.

1. På den **namn och SSO plats** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon8.png)

    a. Lägg till namnet på identitetsprovider i **namn på identitetsprovider** textrutan (t.ex Azure AD).

    b. Klicka på **Nästa**.

1. Verifiera certifikat för signering och klicka på **nästa**.   

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon9.png)

1. På den **FishEye användarkonton** avsnittet, gör du följande:

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon10.png)

    a. Välj **skapa användare i Fisheyes intern katalog om det behövs** och ange rätt namn i gruppen för användare (kan vara flera Nej. av (grupper avgränsade med kommatecken).

    b. Klicka på **Nästa**.

1. Klicka på **Slutför**.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon11.png)

1. På den **kända domäner för Azure AD** avsnittet, gör du följande:  

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/addon12.png)

    a. Välj **kända domäner** från den vänstra panelen på sidan.

    b. Ange domännamnet i den **kända domäner** textrutan.

    c. Klicka på **Spara**.  

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/kantegassoforfisheyecrucible-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/kantegassoforfisheyecrucible-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/kantegassoforfisheyecrucible-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/kantegassoforfisheyecrucible-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-kantega-sso-for-fisheyecrucible-test-user"></a>Skapa en Kantega SSO för FishEye/genom testanvändare

Om du vill aktivera Azure AD-användare att logga in på FishEye/genom etableras de i FishEye/genom. I Kantega SSO för FishEye/genom att etablering är en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på ditt genom en lokal server som en administratör.

1. Hovra över kugghjulet och klicka på den **användare**.

    ![Lägg till medarbetare](./media/kantegassoforfisheyecrucible-tutorial/user1.png) 

1. Under **användare** fliken avsnittet **Lägg till användare**.

    ![Lägg till medarbetare](./media/kantegassoforfisheyecrucible-tutorial/user2.png)

1. På den **Lägg till ny användare** dialogrutan utför följande steg:

    ![Lägg till medarbetare](./media/kantegassoforfisheyecrucible-tutorial/user3.png) 

    a. I den **användarnamn** textrutan typ e-postmeddelandet av användare som Brittasimon@contoso.com.
    
    b. I den **visningsnamn** textrutan visningsnamn för användaren som Britta Simon.
    
    c. I den **e-postadress** textrutan typ e-postadressen för användaren som Brittasimon@contoso.com.

    d. I den **lösenord** textrutan skriver du lösenordet för användaren.  

    e. I den **Bekräfta lösenord** textrutan, ange lösenordet för användaren.

    f. Klicka på **Lägg till**.   

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Kantega SSO för FishEye/genom.

![Tilldela användare][200] 

**Om du vill tilldela Kantega SSO för FishEye/genom Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Kantega SSO för FishEye/genom**.

    ![Konfigurera enkel inloggning](./media/kantegassoforfisheyecrucible-tutorial/tutorial_kantegassoforfisheyecrucible_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Kantega SSO för FishEye/genom panel i åtkomstpanelen du bör få automatiskt loggat in på ditt Kantega SSO för FishEye/genom programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_01.png
[2]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_02.png
[3]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_03.png
[4]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_04.png

[100]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_100.png

[200]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_200.png
[201]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_201.png
[202]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_202.png
[203]: ./media/kantegassoforfisheyecrucible-tutorial/tutorial_general_203.png

