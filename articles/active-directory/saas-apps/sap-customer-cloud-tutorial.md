---
title: 'Självstudiekurs: Azure Active Directory single sign-on (SSO) integration med SAP Cloud för kund | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud for Customer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837787d375a7570b7daf0a149960ca0020bcdced
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72264059"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-for-customer"></a>Självstudiekurs: Azure Active Directory-integrering med enkel inloggning (SSO) med SAP Cloud för kund

I den här självstudien får du lära dig hur du integrerar SAP Cloud för kund med Azure Active Directory (Azure AD). När du integrerar SAP Cloud för kund med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Cloud för kunden.
* Aktivera dina användare så att de automatiskt loggas in i SAP Cloud för kund med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* SAP Cloud för kund enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* SAP Cloud for Customer stöder **SP**-initierad enkel inloggning

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Lägga till SAP Cloud for Customer från galleriet

Om du vill konfigurera integreringen av SAP Cloud for Customer i Azure AD måste du lägga till SAP Cloud for Customer från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **SAP Cloud för kund** i sökrutan i avsnittet Lägg till från **galleriet.**
1. Välj **SAP Cloud för kund** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-for-customer"></a>Konfigurera och testa en azure AD-enkel inloggning för SAP Cloud för kund

Konfigurera och testa Azure AD SSO med SAP Cloud för kund med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAP Cloud för kund.

Så här konfigurerar och testar du Azure AD SSO med SAP Cloud för kund:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa azure AD-enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - så att B.Simon kan använda azure AD-enkel inloggning.
1. **[Konfigurera SAP Cloud för kundSSO](#configure-sap-cloud-for-customer-sso)** - för att konfigurera de enskilda inloggningsinställningarna på programsidan.
    1. **[Skapa SAP Cloud för kundens testanvändare](#create-sap-cloud-for-customer-test-user)** – om du vill ha en motsvarighet till B.Simon i SAP Cloud för kund som är länkad till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. I [Azure-portalen](https://portal.azure.com/)hittar du avsnittet **Hantera** på sidan SAP Cloud for **Customer-programintegration**och väljer enkel inloggning . **SAP Cloud for Customer**
1. På sidan **Välj en enda inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Ange värdena för följande fält i avsnittet **Grundläggande SAML-konfiguration:**

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<server name>.crm.ondemand.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Skaffa dess värden genom att kontakta [supportteamet för SAP Cloud for Customer-klienten](https://www.sap.com/about/agreements.sap-cloud-services-customers.html). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SAP Cloud for Customer-programmet förväntar sig SAML-påståenden i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Gör följande i avsnittet **Användarattribut** i dialogrutan **Användarattribut och anspråk**:

    a. Öppna dialogrutan **Hantera användaranspråk** genom att klicka på **redigeringsikonen**.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Välj **Transformering** som **källa**.

    c. Välj **ExtractMailPrefix()** i listan **Transformering**.

    d. Välj det användarattribut som du vill använda för din implementering i listan **Parameter 1**.
    Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    e. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser baserat på dina krav i avsnittet **Konfigurera SAP Cloud för kund.**

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

I det här avsnittet ska du aktivera B.Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Cloud för kunden.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj SAP Cloud **för kund**i programlistan .
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-sap-cloud-for-customer-sso"></a>Konfigurera SAP Cloud för kundSSO

1. Öppna ett nytt webbläsarfönster och logga in på din SAP Cloud för kundföretag webbplats som administratör.

2. Klicka på **Identity Providers** > **Corporate Identity Providers** > **Add** från vänster sida av menyn och klicka på **SamL** **2.0-konfiguration**på popup-baksidan. **Azure AD**

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure01.png)

3. Gör följande i avsnittet **SAML 2.0-konfiguration:**

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure02.png)

    a. Klicka på **Bläddra** om du vill ladda upp XML-filen federationsmetadata som du har hämtat från Azure-portalen.

    b. När XML-filen har laddats upp kommer nedanstående värden att fyllas i automatiskt och sedan klicka på **Spara**.

### <a name="create-sap-cloud-for-customer-test-user"></a>Skapa en testanvändare för SAP Cloud for Customer

Om du vill att Azure AD-användare ska kunna logga in på SAP Cloud för kunden måste de etableras i SAP Cloud för kund. I SAP Cloud för kund är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på SAP Cloud för kunden som säkerhetsadministratör.

2. Från menyns vänstra sida klickar du på **Användare & Auktoriseringar** > **Användarhantering** > **Lägg till användare**.

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure03.png)

3. Gör följande i avsnittet **Lägg till ny användare:**

    ![SAP-konfiguration](./media/sap-customer-cloud-tutorial/configure04.png)

    a. I textrutan **Förnamn** anger du namnet på användaren som **B**.

    b. Ange namnet på användaren som **Simon**i textrutan **Efternamn** .

    c. I textrutan **E-post** anger du `B.Simon@contoso.com`e-postmeddelandet för användaren som .

    d. I textrutan **Inloggningsnamn** anger du namnet på användaren som **B.Simon**.

    e. Välj **Användartyp** enligt dina krav.

    f. Välj alternativet **Kontoaktivering** enligt dina krav.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på SAP Cloud for Customer-panelen i åtkomstpanelen bör du automatiskt loggas in på SAP Cloud for Customer som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SAP Cloud för kund med Azure AD](https://aad.portal.azure.com/)

