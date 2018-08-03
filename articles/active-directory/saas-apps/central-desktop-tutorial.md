---
title: 'Självstudier: Azure Active Directory-integrering med Central Desktop | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och centrala Desktop.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 82a6911c85dd1438aa8f60cb36194a2916bc91e7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429054"
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Självstudier: Azure Active Directory-integrering med Central Desktop

Lär dig hur du integrerar centrala Desktop med Azure Active Directory (AD Azure) i den här självstudien.

Integrera centrala Desktop med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till centrala Desktop.
- Du kan aktivera användarna att få loggas in automatiskt Central Desktop med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Central Desktop behöver du följande objekt:

- En Azure AD-prenumeration
- En Central Desktop enkel inloggning på aktiverad prenumeration

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte redan har en Azure AD-utvärderingsmiljö [skaffa en månads kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till centrala Desktop från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-central-desktop-from-the-gallery"></a>Lägg till centrala Desktop från galleriet
För att konfigurera integrering av centrala Desktop i Azure AD, som du behöver lägga till centrala Desktop från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till centrala Desktop från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Om du vill lägga till nya program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **centrala Desktop**. Välj **centrala Desktop** i resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Central Desktop i resultatlistan](./media/central-desktop-tutorial/tutorial_centraldesktop_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Central Desktop baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vem användaren motsvarighet i Central Desktop är till en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och en tillhörande användare i Central Desktop.

I Central Desktop ger **användarnamn** samma värde som **användarnamn** i Azure AD. Du har nu skapat länken mellan de två användarna.

Om du vill konfigurera och testa Azure AD enkel inloggning med Central Desktop, måste du utföra följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
1. [Skapa en Central Desktop testanvändare](#create-a-central-desktop-test-user) har en motsvarighet för Britta Simon i Central Desktop som är länkad till en Azure AD-representation av användaren.
1. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
1. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för centrala Desktop.

**Om du vill konfigurera Azure AD enkel inloggning med Central Desktop, gör du följande:**

1. I Azure-portalen på den **centrala Desktop** application integration markerar **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. Att aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** listrutan, väljer **SAML-baserad inloggning**.
 
    ![Enkel inloggning för dialogrutan](./media/central-desktop-tutorial/tutorial_centraldesktop_samlbase.png)

1. I den **centrala Desktop domän och URL: er** avsnittet, gör följande:

    ![Central skrivbord domän och URL: er med enkel inloggning för information](./media/central-desktop-tutorial/tutorial_centraldesktop_url.png)

    a. I den **inloggnings-URL** skriver du en URL med följande mönster: `https://<companyname>.centraldesktop.com`

    b. I den **identifierare** skriver du en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.centraldesktop.com/saml2-metadata.php`|
    | `https://<companyname>.imeetcentral.com/saml2-metadata.php`|

    c. I den **svars-URL** skriver du en URL med följande mönster: `https://<companyname>.centraldesktop.com/saml2-assertion.php`    
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren svars-URL och inloggnings-URL. Kontakta den [centrala Desktop client supportteamet](https://imeetcentral.com/contact-us) att hämta dessa värden. 

1. I den **SAML-signeringscertifikat** väljer **certifikat**. Spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat](./media/central-desktop-tutorial/tutorial_centraldesktop_certificate.png) 

1. Välj knappen **Spara**.

    ![Konfigurera den enkel inloggning spara knappen](./media/central-desktop-tutorial/tutorial_general_400.png)
    
1. I den **Central konfiguration av fjärrskrivbord** väljer **konfigurera centrala Desktop** att öppna den **konfigurera inloggning** fönster. Kopiera den **URL för utloggning, SAML entitets-ID och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens** avsnittet.

    ![Central konfiguration av fjärrskrivbord](./media/central-desktop-tutorial/tutorial_centraldesktop_configure.png) 

1. Logga in på din **centrala Desktop** klient.

1. Gå till **inställningar**. Välj **Avancerat**, och välj sedan **Single Sign On**.

    ![-Installation - avancerade](./media/central-desktop-tutorial/ic769563.png "-installation - Avancerat")

1. På den **enkel inloggning på inställningar** utför följande steg:

    ![Enkel inloggning inställningar](./media/central-desktop-tutorial/ic769564.png "enkel inloggning på inställningar")
    
    a. Välj **aktivera SAML v2 för enkel inloggning**.
    
    b. I den **SSO URL** rutan, klistra in den **SAML entitets-ID** värde som du kopierade från Azure-portalen.
    
    c. I den **inloggnings-URL för enkel inloggning** rutan, klistra in den **SAML enkel inloggning för tjänst-URL** värde som du kopierade från Azure-portalen.
    
    d. I den **utloggnings-URL för enkel inloggning** rutan, klistra in den **URL: en för utloggning** värde som du kopierade från Azure-portalen.

1. I den **meddelandet signatur verifieringsmetod** avsnittet, gör följande:

    ![Meddelandet signatur verifieringsmetod](./media/central-desktop-tutorial/ic769565.png "meddelandet signatur verifieringsmetod") en. Välj **Certifikat**.
    
    b. I den **SSO-certifikat** väljer **RSH SHA256**.
    
    c. Öppna det nedladdade certifikatet i anteckningar. Kopiera innehållet i certifikatet och klistra in den i den **SSO-certifikat** fält.
        
    d. Välj **visar en länk till inloggningssidan för SAMLv2**.
    
    e. Välj **uppdatering**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan komma åt den inbäddade dokumentation genom den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen embedded-dokumentation på [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/central-desktop-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/central-desktop-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/central-desktop-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan rutan, gör följande:

    ![Dialogrutan användare](./media/central-desktop-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-central-desktop-test-user"></a>Skapa en Central Desktop testanvändare

För Azure AD-användare för att kunna logga in, måste de vara etablerade i Central Desktop-programmet. Det här avsnittet beskriver hur du skapar användarkonton i Azure AD i Central Desktop.

> [!NOTE]
> Du kan använda andra verktyg för Central Desktop att skapa användaren-konto eller API: er som tillhandahålls av centrala Desktop för att etablera användarkonton i Azure AD.

**Att etablera användarkonton till centrala Desktop:**

1. Logga in på din centrala Desktop-klient.

1. Gå till **personer** > **interna medlemmar**.

1. Välj **lägga till interna medlemmar**.

    ![Personer](./media/central-desktop-tutorial/ic781051.png "personer")
    
1. I den **e-postadress för nya medlemmar** skriver du ett Azure AD-konto som du vill etablera och välj sedan **nästa**.

    ![E-postadresser för nya medlemmar](./media/central-desktop-tutorial/ic781052.png "e-postadresser för nya medlemmar")

1. Välj **lägga till interna medlem (mar)**.

    ![Lägg till interna medlem](./media/central-desktop-tutorial/ic781053.png "Lägg till interna medlem")
   
   >[!NOTE]
   >Användare som du lägger till får ett e-postmeddelande som innehåller en länk för bekräftelse för att aktivera sina konton.
   
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till centrala Desktop.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon centrala Desktop, gör du följande:**

1. Öppna program i Azure-portalen. Gå till vyn directory och gå sedan till **företagsprogram**.

1. Välj **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **centrala Desktop**.

    ![Central Desktop länken i listan med program](./media/central-desktop-tutorial/tutorial_centraldesktop_app.png)  

1. I menyn till vänster väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Välj den **Lägg till** knappen. Välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. I den **användare och grupper** dialogrutan **Britta Simon** i den **användare** lista.

1. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen.

1. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen centrala Desktop i åtkomstpanelen kan hämta du loggas in automatiskt Central Desktop programmet.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/central-desktop-tutorial/tutorial_general_01.png
[2]: ./media/central-desktop-tutorial/tutorial_general_02.png
[3]: ./media/central-desktop-tutorial/tutorial_general_03.png
[4]: ./media/central-desktop-tutorial/tutorial_general_04.png

[100]: ./media/central-desktop-tutorial/tutorial_general_100.png

[200]: ./media/central-desktop-tutorial/tutorial_general_200.png
[201]: ./media/central-desktop-tutorial/tutorial_general_201.png
[202]: ./media/central-desktop-tutorial/tutorial_general_202.png
[203]: ./media/central-desktop-tutorial/tutorial_general_203.png

