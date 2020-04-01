---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med PurelyHR | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56049e1b1253cd749a8e16061957c6b5b8786e3c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594537"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med PurelyHR

I den här självstudien får du lära dig hur du integrerar PurelyHR med Azure Active Directory (Azure AD). När du integrerar PurelyHR med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PurelyHR.
* Gör det möjligt för användarna att automatiskt loggas in på PurelyHR med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Rent HR enkel inloggning (SSO) aktiverat abonnemang.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* PurelyHR stöder **SP och IDP** initierade SSO
* PurelyHR stöder **just in time-användares** etablering

## <a name="adding-purelyhr-from-the-gallery"></a>Lägga rent HR från galleriet

Om du vill konfigurera integreringen av PurelyHR i Azure AD måste du lägga till RenodlatHR från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Rent HR** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **PurelyHR** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Konfigurera och testa en azure AD-inloggning för PurelyHR

Konfigurera och testa Azure AD SSO med PurelyHR med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i PurelyHR.

Om du vill konfigurera och testa Azure AD SSO med PurelyHR slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera Renodlad HR SSO](#configure-purelyhr-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa RentHR-testanvändare](#create-purelyhr-test-user)** - att ha en motsvarighet till B.Simon i PurelyHR som är kopplad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)på sidan RentHR-programintegrering hittar du avsnittet **Hantera** och väljer **enkel inloggning**. **PurelyHR**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** anger du värdena för följande fält:

    Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<companyID>.purelyhr.com/sso-consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Kontakta [PurelyHR Client support team](https://support.purelyhr.com/) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **Certifikat (Base64)** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera renodlade rättigheter.**

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. Välj Azure Active Directory i den vänstra rutan i **Azure-portalen,** välj **Användare**och välj sedan **Alla användare**.
1. Välj **Ny användare** högst upp på skärmen.
1. Gör så här i egenskaperna **Användare:**
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Ange **.** username@companydomain.extension Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till PurelyHR.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. I programlistan väljer du **PurelyHR**.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-purelyhr-sso"></a>Konfigurera Renodlat HR SSO

1. Om du vill automatisera konfigurationen inom PurelyHR måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Ställ in PurelyHR** kommer att hänvisa dig till PurelyHR ansökan. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på PurelyHR. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera PurelyHR manuellt öppnar du ett nytt webbläsarfönster och loggar in på din RentHR-företagswebbplats som administratör och utför följande steg:

1. Öppna **instrumentpanelen** från alternativen i verktygsfältet och klicka på **SSO-inställningar**.

1. Klistra in värdena i rutorna enligt beskrivningen nedan-

    ![Konfigurera enkel inloggning](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Öppna **certifikat(Bas64)** som hämtats från Azure-portalen i anteckningar och kopiera certifikatvärdet. Klistra in det kopierade värdet i rutan **X.509 Certificate.**

    b. Klistra in **Azure AD-identifieraren** som kopierats från Azure-portalen i **url-rutan för Idp Issuer.**

    c. Klistra in **inloggnings-URL:en** som kopierats från Azure-portalen i url-url:en i **Idp-slutpunkt.** 

    d. Markera kryssrutan **Skapa användare automatiskt** om du vill aktivera automatisk etablering av användare i PurelyHR.

    e. Spara inställningarna genom att klicka på **Spara ändringar.**

### <a name="create-purelyhr-test-user"></a>Skapa RentHR-testanvändare

Det här steget krävs vanligtvis inte eftersom programmet stöder just i tid användaretablering. Om den automatiska användaretablering inte är aktiverad kan manuellt användarskapande göras enligt beskrivningen nedan.

Logga in på din Velpic SAML-företagswebbplats som administratör och utför följande steg:

1. Klicka på Fliken Hantera och gå till avsnittet Användare och klicka sedan på Knappen Nytt för att lägga till användare.

    ![lägga till användare](./media/velpicsaml-tutorial/velpic_7.png)

2. Gör följande på dialogsidan **"Skapa ny användare".**

    ![användare](./media/velpicsaml-tutorial/velpic_8.png)

    a. Skriv förnamnet B i textrutan **Förnamn.**

    b. Skriv efternamnet på Simon i textrutan **Efternamn.**

    c. Skriv användarnamnet för B.Simon i textrutan **Användarnamn.**

    d. Skriv kontots B.Simon@contoso.com e-postadress i **textrutan e-post.**

    e. Resten av informationen är valfri kan du fylla i den om det behövs.

    f. Klicka på **SPARA**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen RentHR på åtkomstpanelen ska du automatiskt loggas in på den rent hr-plats som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova PurelyHR med Azure AD](https://aad.portal.azure.com/)
