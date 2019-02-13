---
title: 'Självstudier: Azure Active Directory-integrering med ON24 virtuell miljö SAML anslutning | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ON24 virtuell miljö SAML anslutning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0827895d58b0b7633ee4543495014c62b5394312
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56209497"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Självstudier: Azure Active Directory-integrering med ON24 virtuell miljö SAML anslutning

I den här självstudien får du lära dig hur du integrerar ON24 virtuell miljö SAML anslutning med Azure Active Directory (AD Azure).

Integrera ON24 virtuell miljö SAML anslutning med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till ON24 virtuell miljö SAML anslutning.
- Du kan aktivera användarna att automatiskt få loggat in på ON24 virtuell miljö SAML anslutning (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med ON24 virtuell miljö SAML anslutning behöver du följande objekt:

- En Azure AD-prenumeration
- En ON24 virtuell miljö SAML anslutning enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till ON24 virtuell miljö SAML-anslutning från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>Att lägga till ON24 virtuell miljö SAML-anslutning från galleriet
Om du vill konfigurera integreringen av ON24 virtuell miljö SAML anslutning till Azure AD, som du behöver lägga till ON24 virtuell miljö SAML anslutning från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ON24 virtuell miljö SAML anslutning från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![image](./media/on24-tutorial/a_new_app.png)

4. I sökrutan skriver **ON24 virtuell miljö SAML anslutning**väljer **ON24 virtuell miljö SAML anslutning** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ON24 virtuell miljö SAML anslutning baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i ON24 virtuell miljö SAML anslutning till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i ON24 virtuell miljö SAML anslutning upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ON24 virtuell miljö SAML-anslutning, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
3. **[Skapa en testanvändare ON24 virtuell miljö SAML anslutning](#create-an-on24-virtual-environment-saml-connection-test-user)**  – du har en motsvarighet för Britta Simon i ON24 virtuell miljö SAML anslutning som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för ON24 virtuell miljö SAML anslutning.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med ON24 virtuell miljö SAML-anslutning:**

1. I den [Azure-portalen](https://portal.azure.com/)på den **ON24 virtuell miljö SAML anslutning** application integration markerar **enkel inloggning**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Klicka på **ändra enkel inloggning läge** på skärmen för att välja den **SAML** läge.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. På den **SAML grundkonfiguration** avsnittet utför följande steg om du vill konfigurera programmet i **IDP** intiated läge:

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. I den **identifierare** text skriver en URL:

     **URL: en för produktion-miljö**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **URL för QA-miljön**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. I textrutan **Svars-URL** skriver du en URL:
    
     **URL: en för produktion-miljö**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **URL för QA-miljön**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Klicka på **Ange ytterligare URL:er**. 

    d. Skriv en URL i textrutan **Vidarebefordransstatus**: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Om du vill konfigurera programmet i **SP** intiated läge i den **inloggnings-URL** text skriver en URL: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned rätt certifikat som uppfyller dina krav och spara den på din dator.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Att konfigurera enkel inloggning på **ON24 virtuell miljö SAML anslutning** sida, som du behöver skicka certifikat/metadata som du har hämtat från Azure portal för att [ON24 virtuell miljö SAML anslutning supportteam](https://www.on24.com/about-us/support/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Välj **Ny användare** överst på skärmen.

    ![image](./media/on24-tutorial/d_adduser.png)

3. Genomför följande steg i Användaregenskaper.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Skapa en testanvändare ON24 virtuell miljö SAML anslutning

I det här avsnittet skapar du en användare som kallas Britta Simon i ON24 virtuell miljö SAML anslutning. Arbeta med [ON24 virtuell miljö SAML anslutning supportteamet](https://www.on24.com/about-us/support/) att lägga till användare i ON24 virtuell miljö SAML anslutning-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ON24 virtuell miljö SAML anslutning.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. I listan med program väljer **ON24 virtuell miljö SAML anslutning**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

5. I dialogrutan **Lägg till tilldelning** väljer du knappen **Tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ON24 virtuell miljö SAML anslutning i åtkomstpanelen du bör få automatiskt loggat in på programmets ON24 virtuell miljö SAML anslutning.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

