---
title: 'Självstudier: Azure Active Directory-integrering med E försäljning Manager REMIXA | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och E försäljning Manager REMIXA.
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
ms.openlocfilehash: 5f2a3bb666e40f1aafd921d70d93133ca9139b38
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65406956"
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrera Azure Active Directory med E-försäljningschef Remix

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD) med E försäljning Manager REMIXA.

Genom att integrera Azure AD med E försäljning Manager REMIXA kan få du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till E försäljning Manager REMIXA.
- Du kan aktivera användarna att få loggar in automatiskt på E försäljning Manager REMIXA (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats, Azure-portalen.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med E försäljning Manager REMIXA, behöver du följande objekt:

- En Azure AD-prenumeration
- En prenumeration på E försäljning Manager REMIXA SSO-aktiverad

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du gör *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

* Att lägga till E försäljning Manager REMIXA från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Lägg till E försäljning Manager REMIXA från galleriet
Om du vill konfigurera integreringen av Azure AD med E försäljning Manager REMIXA, lägga till E försäljning Manager REMIXA från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den vänstra rutan i [Azure-portalen](https://portal.azure.com) väljer du **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret ”program”][2]
    
1. Om du vill lägga till ett nytt program, Välj **nytt program** överst i fönstret.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **E försäljning Manager REMIXA**väljer **E försäljning Manager REMIXA** i resultatlistan och välj sedan **Lägg till**.

    ![E försäljning Manager REMIXA i resultatlistan](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med E försäljning Manager REMIXA, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD att identifiera användaren E försäljning Manager REMIXA och dess motsvarighet i Azure AD. Med andra ord etableras en länk förhållandet mellan en Azure AD-användare och samma användare i E försäljning Manager REMIXA.

Om du vill konfigurera och testa Azure AD enkel inloggning med E försäljning Manager REMIXA, slutför du byggblocken i nästa fem avsnitt:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt E försäljning Manager REMIXA program genom att göra följande:

1. I Azure-portalen på den **E försäljning Manager REMIXA** application integration markerar **enkel inloggning**.

    ![Länken ”enkel inloggning”][4]

1. I den **enkel inloggning** fönstret i den **läge för enkel inloggning** väljer **SAML-baserad inloggning**.
 
    ![Fönstret ”enkel inloggning”](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

1. Under **REMIXA E försäljning Manager-domän och URL: er**, gör du följande:

    ![REMIXA E försäljning Manager-domän och URL: er med enkel inloggning för information](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. I den **inloggnings-URL** skriver du en URL i formatet: *https://\<Server-baserade-URL > /\<underdomän > / esales-pc*.

    b. I den **identifierare** skriver du en URL i formatet: *https://\<Server-baserade-URL > /\<underdomän > /*.

    c. Obs den **identifierare** värdet för senare användning i den här självstudien.
    
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med faktiska inloggning URL och identifierare. Kontakta för att få värdena [REMIXA E försäljning Manager-klienten supportteamet](mailto:esupport@softbrain.co.jp).

1. Under **SAML-signeringscertifikat**väljer **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länk för hämtning av certifikat (Base64)](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

1. Välj den **visa och redigera alla andra användarattribut** kryssrutan och välj sedan den **e-postadress** attribut.
    
    ![Fönstret användarattribut](./media/esalesmanagerremix-tutorial/configure1.png)

    Den **redigera attributet** öppnas.

1. Kopiera den **Namespace** och **namn** värden. Generera värdet i mönstret  *\<Namespace > /\<namn >*, och spara den för senare användning i den här självstudien.

    ![Fönstret Redigera attribut](./media/esalesmanagerremix-tutorial/configure2.png)

1. Under **E försäljning Manager REMIXA Configuration**väljer **konfigurera E försäljning Manager REMIXA**.

    ![E-försäljningschef Remix konfiguration](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Den **konfigurera inloggning** öppnas.

1. I den **Snabbreferens** avsnittet, kopiera utloggnings-URL och URL för SAML enkel inloggning.

1. Välj **Spara**.

    ![Knappen Spara](./media/esalesmanagerremix-tutorial/tutorial_general_400.png)

1. Logga in på ditt E försäljning Manager REMIXA program som administratör.

1. Längst upp till höger, Välj **till administratör-menyn**.

    ![Kommandot ”till administratör-meny](./media/esalesmanagerremix-tutorial/configure4.png)

1. I den vänstra rutan väljer **systeminställningar** > **samarbete med externa system**.

    ![”Systeminställningar” och ”samarbete med externa system”-länkar](./media/esalesmanagerremix-tutorial/configure5.png)
    
1. I den **samarbete med externa system** väljer **SAML**.

    ![Fönstret ”samarbete med externa system”](./media/esalesmanagerremix-tutorial/configure6.png)

1. Under **inställningen för SAML-autentisering**, gör du följande:

    ![Avsnittet ”inställningen för SAML-autentisering”](./media/esalesmanagerremix-tutorial/configure3.png)
    
    a. Välj den **PC version** markerar du kryssrutan.
    
    b. I den **samarbete objekt** , i listrutan, väljer du **e-post**.

    c. I den **samarbete objekt** rutan, klistra in anspråksvärdet för som du kopierade tidigare från Azure-portalen (det vill säga **https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. I den **utfärdare (entitets-ID)** rutan, klistra in ID-värde som du kopierade tidigare från den **REMIXA E försäljning Manager-domän och URL: er** på Azure portal.

    e. Om du vill ladda upp nedladdade certifikatet från Azure portal, Välj **filval**.

    f. I den **inloggnings-URL för ID** rutan, klistra in Webbadressen för SAML enkel inloggning som du kopierade tidigare i Azure-portalen.

    g. I **utloggnings-URL för identitetsprovider** rutan, klistra in utloggning URL-värdet som du kopierade tidigare i Azure-portalen.

    h. Välj **Ange fullständig**.

> [!TIP]
> När du konfigurerar appen, du kan läsa en kortare version av föregående anvisningar i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen i den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och öppna den Embedded-dokumentation i den **Configuration** avsnittet längst ned. Mer information om funktionen embedded-dokumentation finns i [embedded-dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i Azure portal genom att göra följande:

![Skapa en Azure AD-testanvändare][100]

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**.

    ![Azure Active Directory-länk](./media/paloaltoadmin-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över aktuella användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/paloaltoadmin-tutorial/create_aaduser_02.png)

1. Överst på den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Den **användaren** öppnas.

1. I den **användaren** fönstret gör du följande:

    ![Fönstret användare](./media/paloaltoadmin-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** och anteckna värdet som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Skapa en testanvändare E försäljning Manager REMIXA

1. Logga in på ditt E försäljning Manager REMIXA program som administratör.

1. Välj **till administratör-menyn** från menyn längst upp till höger.

    ![E-försäljningschef Remix konfiguration](./media/esalesmanagerremix-tutorial/configure4.png)

1. Välj **företagets inställningar** > **underhåll av avdelningar och anställda**, och välj sedan **personal som är registrerad**.

    ![Fliken ”anställda registered”](./media/esalesmanagerremix-tutorial/user1.png)

1. I den **registrering av nya medarbetare** avsnittet, gör du följande:
    
    ![”Ny anställd registrering” avsnittet](./media/esalesmanagerremix-tutorial/user2.png)

    a. I den **medarbetarnamn** skriver du namnet på användaren (till exempel **Britta**).

    b. Slutför de återstående obligatoriska fälten.
    
    c. Om du aktiverar SAML kan kan inte administratören logga in på sidan logga in. Bevilja inloggning administratörsbehörighet till användare genom att välja den **administratörsinloggning** markerar du kryssrutan.

    d. Välj **registrering**.

1. I framtiden, för att logga in som administratör, logga in som den användare som har administratörsbehörighet och välj sedan längst upp till höger, **till administratör-menyn**.

    ![Kommandot ”till administratör-meny](./media/esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du användaren Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till E försäljning Manager REMIXA. Du gör detta genom att göra följande: 

![Tilldela rollen][200] 

1. I Azure-portalen öppnar du den **program** visa, gå till den **Directory** visa och välj sedan **företagsprogram** > **alla program**.

    ![”Program” och ”alla program”-länkar][201] 

1. I den **program** väljer **E försäljning Manager REMIXA**.

    ![Länken E försäljning Manager REMIXA](./media/esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

1. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Välj **Lägg till** och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

1. I den **användare och grupper** fönstret i den **användare** väljer **Britta Simon**.

1. Välj den **Välj** knappen.

1. I den **Lägg till tilldelning** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du din konfiguration av enkel inloggning för Azure AD med hjälp av Åtkomstpanelen.

När du väljer panelen E försäljning Manager REMIXA i åtkomstpanelen, bör du vara inloggad automatiskt till ditt E försäljning Manager REMIXA program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om integrering av SaaS-appar med Azure Active Directory](tutorial-list.md)
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

