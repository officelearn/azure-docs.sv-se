---
title: 'Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SmartDraw | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SmartDraw.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6f8fbbe8-c771-4fa1-9326-5a9dac991ace
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe08f1523b4b61653d89a9b3472355dd3eeaf69f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75640116"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-smartdraw"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SmartDraw

I den här självstudien får du lära dig hur du integrerar SmartDraw med Azure Active Directory (Azure AD). När du integrerar SmartDraw med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SmartDraw.
* Gör att användarna automatiskt loggas in på SmartDraw med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SmartDraw enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SmartDraw stöder **SP och IDP** initierad SSO
* SmartDraw stöder just in time-användaretablering **Just In Time**

## <a name="adding-smartdraw-from-the-gallery"></a>Lägga till SmartDraw från galleriet

Om du vill konfigurera integreringen av SmartDraw i Azure AD måste du lägga till SmartDraw från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SmartDraw** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **SmartDraw** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-smartdraw"></a>Konfigurera och testa en azure AD-inloggning för SmartDraw

Konfigurera och testa Azure AD SSO med SmartDraw med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SmartDraw.

Så här konfigurerar och testar du Azure AD SSO med SmartDraw:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SmartDraw SSO](#configure-smartdraw-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    * **[Skapa SmartDraw-testanvändare](#create-smartdraw-test-user)** – om du vill ha en motsvarighet till B.Simon i SmartDraw som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan Hantera på sidan **Azure-portalen**och välj enkel inloggning . [Azure portal](https://portal.azure.com/) **SmartDraw**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://cloud.smartdraw.com/sso/saml/login/<domain>`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Du uppdaterar värdet för inloggnings-URL med den faktiska inloggnings-URL:en, vilket förklaras senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Klicka på **Spara**.

1. SmartDraw-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig SmartDraw-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-post | user.mail |
    | Grupper | användare.grupper |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina behov i avsnittet **Konfigurera SmartDraw.**

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SmartDraw.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **SmartDraw**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-smartdraw-sso"></a>Konfigurera SmartDraw SSO

1. Om du vill automatisera konfigurationen i SmartDraw måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

1. När du har lagt till tillägget i webbläsaren, klicka på **Konfigurera SmartDraw** kommer att leda dig till SmartDraw ansökan. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på SmartDraw. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-5.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

1. Om du vill konfigurera SmartDraw manuellt öppnar du ett nytt webbläsarfönster och loggar in på smartritingsföretagets webbplats som administratör och utför följande steg:

1. Klicka på **Enkel inloggning** under Hantera din SmartDraw-licens.

    ![SmartDraw-konfiguration](./media/smartdraw-tutorial/configure01.png)

1. Gör följande på sidan Konfiguration:

    ![SmartDraw-konfiguration](./media/smartdraw-tutorial/configure02.png)

    a. Skriv domänen i textrutan **Domän (t.o.acme.com).**

    b. Kopiera **din SP Initierade inloggningsadress kommer att vara** för din instans och klistra in den i Sign-on URL textruta i **Grundläggande SAML-konfiguration** på Azure-portalen.

    c. Skriv **Alla**i **textrutan Säkerhet grupper om du vill tillåta SmartDraw Access.**

    d. Klistra in värdet för **Azure AD-identifierare** som du har kopierat från Azure-portalen i textrutan Url för **DIN SAML-utfärdare.**

    e. Öppna XML-filen metadata som du hämtade från Azure-portalen i Anteckningar, kopiera dess innehåll och klistra sedan in den i rutan **Saml MetaData.**

    f. Klicka på **Spara konfiguration** 

### <a name="create-smartdraw-test-user"></a>Skapa SmartDraw-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i SmartDraw. SmartDraw stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i SmartDraw skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SmartDraw-panelen på åtkomstpanelen ska du automatiskt loggas in på smartritw som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SmartDraw med Azure AD](https://aad.portal.azure.com/)