---
title: 'Självstudier: Azure Active Directory-integrering med vxMaintain | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vxMaintain.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 841a1066-593c-4603-9abe-f48496d73d10
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d0e8f8526d866c308be8684546397f282dcce51
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56194112"
---
# <a name="tutorial-azure-active-directory-integration-with-vxmaintain"></a>Självstudier: Azure Active Directory-integrering med vxMaintain

I den här självstudien får du lära dig hur du integrerar vxMaintain med Azure Active Directory (AD Azure).

Den här integrationen har flera viktiga fördelar. Du kan:

- Styr i Azure AD som har åtkomst till vxMaintain.
- Ge användarna logga in automatiskt till vxMaintain med enkel inloggning (SSO) med hjälp av sina Azure AD-konton.
- Hantera konton på en central plats: Azure-portalen.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med vxMaintain, behöver du följande objekt:

- En Azure AD-prenumeration
- VxMaintain SSO-aktiverad prenumeration

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du inte använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två viktigaste byggstenarna:

* Att lägga till vxMaintain från galleriet
* Konfigurera och testa Azure AD enkel inloggning

## <a name="add-vxmaintain-from-the-gallery"></a>Lägg till vxMaintain från galleriet
Om du vill konfigurera integreringen av vxMaintain med Azure AD, som du behöver lägga till vxMaintain från galleriet i din lista över hanterade SaaS-appar.

Om du vill lägga till vxMaintain från galleriet, gör du följande:

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** knappen. 

    ![Azure Active Directory-knappen][1]

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret ”program”][2]
    
1. Att lägga till ett program i den **alla program** dialogrutan **nytt program**.

    ![”Det nya programmet” knappen][3]

1. I sökrutan skriver **vxMaintain**.

    ![Listrutan ”enkel inloggning på läget”](./media/vxmaintain-tutorial/tutorial_vxmaintain_search.png)

1. I resultatlistan väljer **vxMaintain**, och välj sedan **Lägg till**.

    ![Länken vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD
I det här avsnittet ska du konfigurera och testa enkel inloggning för Azure AD med hjälp av vxMaintain, baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD behöver veta vxMaintain motsvarigheten till Azure AD-användare för SSO ska fungera. Det vill säga måste du upprätta en relation med länken mellan Azure AD-användare och motsvarande vxMaintain användaren.

För att upprätta länken relationen, tilldela vxMaintain **användarnamn** värde som Azure AD **användarnamn** värde.

Slutför följande byggblock för att konfigurera och testa enkel inloggning för Azure AD med hjälp av vxMaintain.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

I det här avsnittet kan du både aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt vxMaintain program genom att göra följande:

1. I Azure-portalen på den **vxMaintain** application integration markerar **enkel inloggning**.

    ![Kommandot ”enkel inloggning”][4]

1. Att aktivera enkel inloggning, i den **läge för enkel inloggning** listrutan, väljer **SAML-baserad inloggning**.
 
    ![Kommandot ”SAML-baserad inloggning”](./media/vxmaintain-tutorial/tutorial_vxmaintain_samlbase.png)

1. Under **vxMaintain domän och URL: er**, gör du följande:

    ![VxMaintain domän och URL: er](./media/vxmaintain-tutorial/tutorial_vxmaintain_url.png)

    a. I den **identifierare** skriver en URL som har följande syntax: `https://<company name>.verisae.com`

    b. I den **svars-URL** skriver en URL som har följande syntax: `https://<company name>.verisae.com/DataNett/action/ssoConsume/mobile?_log=true`

    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med det faktiska ID: t och svars-URL. Om du vill hämta värdena genom att kontakta den [vxMaintain supportteamet](https://www.hubspot.com/company/contact).
 
1. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**, och spara sedan metadatafilen till datorn.

    ![Avsnittet ”SAML-signeringscertifikat”](./media/vxmaintain-tutorial/tutorial_vxmaintain_certificate.png) 

1. Välj **Spara**.

    ![Knappen Spara](./media/vxmaintain-tutorial/tutorial_general_400.png)

1. Konfigurera **vxMaintain** SSO, skicka de hämtade **XML-Metadata för** filen till den [vxMaintain supportteamet](https://www.hubspot.com/company/contact).

> [!TIP]
> När du konfigurerar appen kan du läsa en kortare version av föregående anvisningar i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen från den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan komma åt den inbäddade dokumentationen från den **Configuration** avsnittet. 
>
>Mer information om funktionen embedded-dokumentation finns [hantera enkel inloggning för företagsappar](https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare
I det här avsnittet skapar du testanvändare Britta Simon i Azure portal genom att göra följande:

![Azure AD-testanvändare][100]

1. I den **Azure-portalen**, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![”Azure Active Directory”-knappen](./media/vxmaintain-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** > **alla användare**.
    
    ![”Alla användare”-länk](./media/vxmaintain-tutorial/create_aaduser_02.png)  
    Den **alla användare** öppnas dialogrutan. 

1. Öppna den **användaren** dialogrutan **Lägg till**.
 
    ![Knappen Lägg till](./media/vxmaintain-tutorial/create_aaduser_03.png) 

1. I den **användaren** dialogrutan Gör följande:
 
    ![Dialogrutan Användare](./media/vxmaintain-tutorial/create_aaduser_04.png) 

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver testanvändare Britta Simon e-postadress.

    c. Välj den **visa lösenord** och anteckna värdet som har genererats i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-vxmaintain-test-user"></a>Skapa en vxMaintain testanvändare

I det här avsnittet skapar du testanvändare Britta Simon i vxMaintain. Om du vill lägga till användare i vxMaintain-plattformen, arbeta med den [vxMaintain supportteamet](https://www.hubspot.com/company/contact). Innan du använder SSO, skapa och aktivera användarna.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du testanvändare Britta Simon att använda enkel inloggning för Azure genom att bevilja åtkomst till vxMaintain. Du gör detta genom att göra följande:

![Testanvändare i listan visningsnamn][200] 

1. I Azure-portalen **program** visa, gå till **Directory** Visa > **företagsprogram** > **alla program**.

    ![Länken ”alla program”][201] 

1. I den **program** väljer **vxMaintain**.

    ![Länken vxMaintain](./media/vxmaintain-tutorial/tutorial_vxmaintain_app.png) 

1. I den vänstra rutan väljer **användare och grupper**.

    ![Länken ”Användare och grupper”][202] 

1. Välj **Lägg till** och klicka sedan på **Lägg till tilldelning** väljer **användare och grupper**.

    ![Länken ”Användare och grupper”][203]

1. I den **användare och grupper** i dialogrutan den **användare** väljer **Britta Simon**, och välj sedan den **Välj** knappen.

1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.
    
### <a name="test-your-azure-ad-single-sign-on"></a>Testa din Azure AD enkel inloggning

I det här avsnittet ska testa du din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

Att välja den **vxMaintain** panel i åtkomstpanelen ska logga in på ditt vxMaintain program automatiskt.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="next-steps"></a>Nästa steg

* [Lista över självstudier om integrering av SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/vxmaintain-tutorial/tutorial_general_01.png
[2]: ./media/vxmaintain-tutorial/tutorial_general_02.png
[3]: ./media/vxmaintain-tutorial/tutorial_general_03.png
[4]: ./media/vxmaintain-tutorial/tutorial_general_04.png

[100]: ./media/vxmaintain-tutorial/tutorial_general_100.png

[200]: ./media/vxmaintain-tutorial/tutorial_general_200.png
[201]: ./media/vxmaintain-tutorial/tutorial_general_201.png
[202]: ./media/vxmaintain-tutorial/tutorial_general_202.png
[203]: ./media/vxmaintain-tutorial/tutorial_general_203.png

