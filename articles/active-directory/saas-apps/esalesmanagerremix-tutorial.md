---
title: 'Självstudiekurs: Azure Active Directory-integrering med E Sales Manager Remix | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och E Sales Manager Remix.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895fb0d83e383618818325263ac80c5919a0ee7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65406956"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrera Azure Active Directory med Remix av E-försäljningschef

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD) med E Sales Manager Remix.

Genom att integrera Azure AD med E Sales Manager Remix får du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till E Sales Manager Remix.
- Du kan göra det möjligt för användarna att logga in automatiskt på E Sales Manager Remix (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med E Sales Manager Remix behöver du följande:

- En Azure AD-prenumeration
- En E Sales Manager Remix SSO-aktiverad prenumeration

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien testar du azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här självstudien består av två huvudbyggstenar:

* Lägga till E Sales Manager Remix från galleriet
* Konfigurera och testa enkel inloggning i Azure AD

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Lägg till E Sales Manager Remix från galleriet
Om du vill konfigurera integreringen av Azure AD med E Sales Manager Remix lägger du till E Sales Manager Remix från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret "Företagsprogram"][2]
    
1. Om du vill lägga till ett nytt program väljer du **Nytt program** högst upp i fönstret.

    ![Knappen Nytt program][3]

1. Skriv E Sales **Manager Remix**i sökrutan, välj **E Sales Manager Remix** i resultatlistan och välj sedan **Lägg till**.

    ![E Sales Manager Remix i resultatlistan](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du azure AD enkel inloggning med E Sales Manager Remix, baserat på en testanvändare som heter "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD identifiera E Sales Manager Remix-användaren och dess motsvarighet i Azure AD. Med andra ord måste en länkrelation mellan en Azure AD-användare och samma användare i E Sales Manager Remix upprättas.

Om du vill konfigurera och testa en enkel Azure AD-inloggning med E Sales Manager Remix slutför du byggblocken i de följande fem avsnitten:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

Aktivera enkel Azure AD-inloggning i Azure-portalen och konfigurera enkel inloggning i ditt E Sales Manager Remix-program genom att göra följande:

1. Välj Enkel inloggning på sidan Integrering av E Sales Manager **Remix-program**på sidan **E Sales Manager Remix-** programintegration .

    ![Länken "Enkel inloggning"][4]

1. I fönstret **Enkel inloggning** väljer du **SAML-baserad inloggning**i rutan Enkel **inloggningsläge** .
 
    ![Fönstret "Enkel inloggning"](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Gör följande under **E Sales Manager Remix Domain and URL:er:**

    ![E Sales Manager Remix Domän och webbadresser enkel inloggningsinformation](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. I rutan **Inloggnings-URL** skriver du en URL i följande format: *https://\<Server-Based-URL>/\<underdomän>/esales-pc*.

    b. Skriv en URL i följande format i rutan **Identifierare:** *https://\<Serverbaserad URL->/\<underdomän>/*.

    c. Observera **identifierare** värdet för senare användning i den här självstudien.
    
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med den faktiska inloggnings-URL:en och identifieraren. Kontakta [supportteamet](mailto:esupport@softbrain.co.jp)för E Sales Manager Remix Client om du vill hämta värdena.

1. Under **SAML-signeringscertifikat**väljer du **Certifikat (Base64)** och sparar sedan certifikatfilen på datorn.

    ![Nedladdningslänken för certifikat (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Markera kryssrutan **Visa och redigera alla andra användarattribut** och markera sedan **attributet emailaddress.**
    
    ![Fönstret Användarattribut](./media/esalesmanagerremix-tutorial/configure1.png)

    Fönstret **Redigera attribut** öppnas.

1. Kopiera **värdena Namnområde** och **Namn.** Generera värdet i mönstret * \<Namnområde>/\<Namn>* och spara det för senare användning i den här självstudien.

    ![Fönstret Redigera attribut](./media/esalesmanagerremix-tutorial/configure2.png)

1. Under **Remixkonfiguration för E-försäljningschef**väljer du **Konfigurera Remix av E-försäljningschef**.

    ![Remixkonfiguration för E-försäljningschef](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Fönstret **Konfigurera inloggning** öppnas.

1. I avsnittet **Snabbreferens** kopierar du utskrivningsadressen och SAML:s url för en enda inloggningstjänst.

1. Välj **Spara**.

    ![Knappen Spara](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Logga in på ditt E Sales Manager Remix-program som administratör.

1. Längst upp till höger väljer du **Till administratörsmeny**.

    ![Kommandot "Till administratörsmeny"](./media/esalesmanagerremix-tutorial/configure4.png)

1. Välj **Systeminställningar** > **Samarbete med externt system**i den vänstra rutan .

    ![Länkarna "Systeminställningar" och "Samarbete med externt system"](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. I fönstret **Samarbete med externa system** väljer du **SAML**.

    ![Fönstret "Samarbete med externt system"](./media/esalesmanagerremix-tutorial/configure6.png)

1. Gör följande under **INSTÄLLNINGEN FÖR SAML-autentisering:**

    ![Avsnittet "SAML-autentiseringsinställning"](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Markera kryssrutan **PC-version.**
    
    b. Välj **e-post**i listrutan **i avsnittet Samarbete.**

    c. I **rutan Samarbetsobjekt** klistrar du in anspråksvärdet som du kopierade tidigare från Azure-portalen (det vill säga **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. I rutan **Utfärdare (entitets-ID)** klistrar du in identifierarvärdet som du kopierade tidigare från avsnittet **E Sales Manager Remix Domain och URL:er** i Azure-portalen.

    e. Om du vill ladda upp det nedladdade certifikatet från Azure-portalen väljer du **Filval**.

    f. I rutan **ID-providerns inloggnings-URL** klistrar du in den SAML-url för en enda inloggningstjänst som du kopierade tidigare i Azure-portalen.

    g. I **url-url-rutan för identitetsprovsprovsanloggning** klistrar du in url-värdet för utloggning som du kopierade tidigare i Azure-portalen.

    h. Välj **Inställning klar**.

> [!TIP]
> När du konfigurerar appen kan du läsa en kortfattad version av föregående instruktioner i [Azure-portalen](https://portal.azure.com). När du har lagt till appen i avsnittet **Active Directory** > **Enterprise Applications** väljer du fliken Enkel **inloggning** och öppnar sedan den inbäddade dokumentationen i avsnittet **Konfiguration** längst ned. Mer information om den inbäddade dokumentationsfunktionen finns i [Inbäddad Azure AD-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du testanvändaren Britta Simon i Azure-portalen genom att göra följande:

![Skapa en Azure AD-testanvändare][100]

1. Välj Azure Active Directory i Den vänstra rutan i **Azure-portalen**.

    ![Azure Active Directory-länken](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över aktuella användare väljer du **Användare och grupper** > **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Välj **Lägg till**högst upp i fönstret **Alla användare.**

    ![Knappen Lägg till](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    **Fönstret Användare** öppnas.

1. Gör följande i **fönstret Användare:**

    ![Fönstret Användare](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. Skriv **BrittaSimon**i rutan **Namn** .

    b. Skriv e-postadressen till användaren Britta Simon i rutan **Användarnamn.**

    c. Markera kryssrutan **Visa lösenord** och notera sedan värdet som visas i rutan **Lösenord.**

    d. Välj **Skapa**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Skapa en E Sales Manager Remix-testanvändare

1. Logga in på ditt E Sales Manager Remix-program som administratör.

1. Välj **Till administratörsmenyn** längst upp till höger.

    ![Remixkonfiguration för E-försäljningschef](./media/esalesmanagerremix-tutorial/configure4.png)

1. Välj **Företagets inställningar** > **Underhåll av avdelningar och medarbetare**och välj sedan Anställda **registrerade**.

    ![Fliken "Anställda registrerade"](./media/esalesmanagerremix-tutorial/user1.png)

1. Gör följande i avsnittet **Registrering av nya medarbetare:**
    
    ![Avsnittet "Registrering av nya medarbetare"](./media/esalesmanagerremix-tutorial/user2.png)

    a. Skriv namnet på användaren (till exempel **Britta**) i rutan **Medarbetarnamn** .

    b. Fyll i de återstående obligatoriska fälten.
    
    c. Om du aktiverar SAML kan administratören inte logga in från inloggningssidan. Bevilja administratörsinloggningsbehörighet till användaren genom att markera kryssrutan **Administratörsinloggning.**

    d. Välj **Registrering**.

1. Om du vill logga in som administratör i framtiden loggar du in som den användare som har administratörsbehörighet och väljer sedan **till administratörsmeny**längst upp till höger .

    ![Kommandot "Till administratörsmeny"](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du göra det möjligt för användare Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till E Sales Manager Remix. Gör så här: 

![Tilldela användarrollen][200] 

1. Öppna **programvyn i** Azure-portalen, gå till **katalogvyn** och välj sedan **Enterprise-program** > **Alla program**.

    ![Länkarna "Företagsprogram" och "Alla program"][201] 

1. Välj E Sales **Manager Remix**i listan **Program** .

    ![Länken E Sales Manager Remix](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. Välj Användare och **grupper**i den vänstra rutan .

    ![Länken ”Användare och grupper”][202]

1. Välj **Lägg till** och välj sedan **Användare och grupper**i fönstret Lägg till **tilldelning** .

    ![Fönstret Lägg till tilldelning][203]

1. Välj **Britta Simon**i listan **Användare** **och grupper** .

1. Välj knappen **Välj.**

1. Välj **Tilldela**i fönstret **Lägg till tilldelning** .
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen E Sales Manager Remix på åtkomstpanelen bör du loggas in automatiskt i ditt E Sales Manager Remix-program.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/esalesmanagerremix-tutorial/tutorial_general_203.png

