---
title: 'Självstudier: Integrera Azure Active Directory med E försäljningschef Remix | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och E försäljning Manager REMIXA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 89b5022c-0d5b-4103-9877-ddd32b6e1c02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: jeedes
ms.openlocfilehash: f681eb91c1e79eb42b572956dfab93e620489e74
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="integrate-azure-active-directory-with-e-sales-manager-remix"></a>Integrera Azure Active Directory med E försäljningschef Remix

I kursen får lära du att integrera Azure Active Directory (AD Azure) med E försäljning Manager REMIXA.

Genom att integrera Azure AD med E försäljning Manager REMIXA kan få du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till E försäljning Manager REMIXA.
- Du kan aktivera användarna att hämta loggar in automatiskt på E försäljning Manager REMIXA (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats, Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med E försäljning Manager REMIXA behöver du följande:

- En Azure AD-prenumeration
- En prenumeration för E försäljning Manager REMIXA SSO aktiverad

> [!NOTE]
> När du testar stegen i den här självstudiekursen, rekommenderar vi att du *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

* Att lägga till E försäljning Manager REMIXA från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-e-sales-manager-remix-from-the-gallery"></a>Lägg till E försäljning Manager REMIXA från galleriet
För att konfigurera Azure AD-integrering med E försäljning Manager REMIXA, lägga till E försäljning Manager REMIXA från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret ”företagsprogram”][2]
    
3. Om du vill lägga till ett nytt program, Välj **nytt program** längst upp i fönstret.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **E försäljning Manager REMIXA**väljer **E försäljning Manager REMIXA** i resultatlistan och välj sedan **Lägg till**.

    ![E försäljning Manager REMIXA i resultatlistan](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med E försäljning Manager REMIXA, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att identifiera användaren E försäljning Manager REMIXA och dess motsvarighet i Azure AD. Med andra ord upprättas en länk förhållandet mellan en Azure AD-användare och samma användare i E försäljning Manager REMIXA.

Att konfigurera och testa Azure AD enkel inloggning med E försäljning Manager REMIXA Slutför byggblock i följande fem avsnitt:

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt E försäljning Manager REMIXA program genom att göra följande:

1. I Azure-portalen på den **E försäljning Manager REMIXA** programmet integration anger **enkel inloggning**.

    ![Länken ”enkel inloggning”][4]

2. I den **enkel inloggning** fönster i den **läget för enkel inloggning** väljer **SAML-baserade inloggning**.
 
    ![Fönstret ”enkel inloggning”](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_samlbase.png)

3. Under **REMIXA E försäljning Manager-domän och URL: er**, gör du följande:

    ![URL: er och REMIXA E försäljning Manager-domän med enkel inloggning information](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_url.png)

    a. I den **inloggnings-URL** skriver en URL i följande format: *https://\<Server baserad URL > /\<underordnade domän > / esales pc*.

    b. I den **identifierare** skriver en URL i följande format: *https://\<Server baserad URL > /\<underordnade domän > /*.

    c. Observera den **identifierare** värde för senare användning i den här självstudiekursen.
    
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med faktiska inloggning URL och identifierare. Kontakta för att få värdena [REMIXA E försäljning Manager-klienten supportteamet](mailto:esupport@softbrain.co.jp).

4. Under **SAML-signeringscertifikat**väljer **certifikat (Base64)**, och sedan spara certifikatfilen på datorn.

    ![Länken Hämta certifikat (Base64)](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_certificate.png) 

5. Välj den **visa och redigera andra användarattribut** kryssrutan och välj sedan den **e-postadress** attribut.
    
    ![Fönstret användarattribut](./media/active-directory-saas-esalesmanagerremix-tutorial/configure1.png)

    Den **Redigera attribut** öppnas.

6. Kopiera den **Namespace** och **namn** värden. Generera värdet i mönstret  *\<Namespace > /\<namn >*, och spara den för senare användning i den här självstudiekursen.

    ![Fönstret Redigera attribut](./media/active-directory-saas-esalesmanagerremix-tutorial/configure2.png)

7. Under **E försäljning Manager REMIXA Configuration**väljer **konfigurera E försäljning Manager REMIXA**.

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_configure.png) 

    Den **konfigurera inloggning** öppnas.

8. I den **Snabbreferens** avsnittet, kopiera den utloggning Webbadressen och Webbadressen för SAML-tjänst för enkel inloggning.

9. Välj **Spara**.

    ![Knappen Spara](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_400.png)

10. Logga in på ditt E försäljning Manager REMIXA program som administratör.

11. Längst upp till höger, Välj **till administratör-menyn**.

    ![”Till administratör” kommandot](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

12. I den vänstra rutan, Välj **systeminställningar** > **samarbete med externa system**.

    ![”Inställningar” och ”samarbete med externa system” länkar](./media/active-directory-saas-esalesmanagerremix-tutorial/configure5.png)
    
13. I den **samarbete med externa system** väljer **SAML**.

    ![Fönstret ”samarbete med externa system”](./media/active-directory-saas-esalesmanagerremix-tutorial/configure6.png)

14. Under **inställningen för SAML-autentisering**, gör du följande:

    ![Avsnittet ”inställningen för SAML-autentisering”](./media/active-directory-saas-esalesmanagerremix-tutorial/configure3.png)
    
    a. Välj den **PC version** kryssrutan.
    
    b. I den **samarbete objektet** avsnitt i listrutan, Välj **e-post**.

    c. I den **samarbete objektet** klistra in anspråksvärdet som du kopierade tidigare från Azure-portalen (det vill säga **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**).

    d. I den **utfärdaren (enhets-ID)** klistra in ID-värde som du kopierade tidigare från den **REMIXA E försäljning Manager-domän och URL: er** på Azure portal.

    e. Om du vill överföra din hämtat certifikat från Azure portal, Välj **filen markeringen**.

    f. I den **ID providern inloggnings-URL** klistra in Webbadressen för SAML-tjänst för enkel inloggning som du kopierade tidigare i Azure-portalen.

    g. I **identitet providern logga ut URL** klistra in utloggning URL-värdet som du kopierade tidigare i Azure-portalen.

    h. Välj **inställningen fullständig**.

> [!TIP]
> När du konfigurerar appen, kan du läsa en kortare version av föregående anvisningarna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen i den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan ansluta till den inbäddade dokumentation i den **Configuration** avsnittet längst ned. Mer information om funktionen inbäddade dokumentation finns [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen genom att göra följande:

![Skapa en testanvändare i Azure AD][100]

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-länk](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över aktuella användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_02.png)

3. Längst upp i den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_03.png)
    
    Den **användaren** öppnas.

4. I den **användaren** fönster, gör du följande:

    ![Fönstret användare](./media/active-directory-saas-paloaltoadmin-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** och anteckna värdet som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-an-e-sales-manager-remix-test-user"></a>Skapa en testanvändare E försäljning Manager REMIXA

1. Logga in på ditt E försäljning Manager REMIXA program som administratör.

2. Välj **menyn till administratören** på menyn längst upp till höger.

    ![E försäljningschef Remix konfiguration](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

3. Välj **företagets inställningar** > **underhåll av avdelningar och anställda**, och välj sedan **medarbetare som registrerat**.

    ![Fliken ”anställda registrerade”](./media/active-directory-saas-esalesmanagerremix-tutorial/user1.png)

4. I den **nya medarbetare registreringen** avsnittet, gör du följande:
    
    ![”Nya medarbetare registreringen” avsnittet](./media/active-directory-saas-esalesmanagerremix-tutorial/user2.png)

    a. I den **medarbetarens namn** skriver du namnet på användaren (till exempel **Britta**).

    b. Slutför de återstående obligatoriska fälten.
    
    c. Om du aktiverar SAML logga administratören inte in på sidan logga in. Bevilja inloggning administratörsbehörighet till användare genom att välja den **administratörsinloggning** kryssrutan.

    d. Välj **registrering**.

5. I framtiden för att logga in som administratör, logga in som den användare som har administratörsbehörighet och välj sedan längst upp till höger, **till administratör-menyn**.

    ![”Till administratör” kommandot](./media/active-directory-saas-esalesmanagerremix-tutorial/configure4.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta användare Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till E försäljning Manager REMIXA. Det gör du genom att göra följande: 

![Tilldela rollen][200] 

1. I Azure-portalen öppnar den **program** visa, gå till den **Directory** visa och välj sedan **företagsprogram** > **alla program**.

    ![”Företagsprogram” och ”alla program” länkar][201] 

2. I den **program** väljer **E försäljning Manager REMIXA**.

    ![Länken E försäljning Manager REMIXA](./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_esalesmanagerremix_app.png)  

3. I den vänstra rutan, Välj **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till** och sedan, i den **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönster i den **användare** väljer **Britta Simon**.

6. Välj den **Välj** knappen.

7. I den **Lägg uppdrag** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen E försäljning Manager REMIXA på åtkomstpanelen bör du vara inloggad automatiskt till ditt E försäljning Manager REMIXA program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-esalesmanagerremix-tutorial/tutorial_general_203.png

