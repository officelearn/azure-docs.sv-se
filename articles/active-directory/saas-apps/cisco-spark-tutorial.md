---
title: 'Självstudiekurs: Azure Active Directory Single sign-on (SSO) integration med Cisco Webex | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Webex.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01fe4d06d5f73eacee1d1cdaf1963232b84daf05
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046795"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Självstudiekurs: Azure Active Directory Single sign-on (SSO) integration med Cisco Webex

I den här självstudien får du lära dig hur du integrerar Cisco Webex med Azure Active Directory (Azure AD). När du integrerar Cisco Webex med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco Webex.
* Gör att användarna automatiskt loggas in på Cisco Webex med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om Integrering av SaaS-appar med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Cisco Webex enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö.

* Cisco Webex stöder **SP** initierade SSO.
* Cisco Webex stöder **automatisk** användaretablering.
* När du har konfigurerat Cisco Webex kan du framtvinga Sessionskontroll, som skyddar exfiltration och infiltration av organisationens känsliga data i realtid. Sessionskontrollen sträcker sig från villkorlig åtkomst. [Lär dig hur du tillämpar sessionskontroll med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

För att konfigurera integrering av Cisco Webex i Azure AD måste du lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. Välj **Azure Active Directory-tjänsten** i det vänstra navigeringsfönstret.
1. Navigera till **företagsprogram** och välj sedan **Alla program**.
1. Om du vill lägga till ett nytt program väljer du **Nytt program**.
1. Skriv **Cisco Webex** i sökrutan i avsnittet **Lägg till från galleriet.**
1. Välj **Cisco Webex** från resultatpanelen och lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Konfigurera och testa en azure AD-inloggning för Cisco Webex

Konfigurera och testa Azure AD SSO med Cisco Webex med en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Cisco Webex.

Om du vill konfigurera och testa Azure AD SSO med Cisco Webex slutför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa Azure AD enkel inloggning med B.Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera B.Simon att använda Azure AD enkel inloggning.
2. **[Konfigurera Cisco Webex](#configure-cisco-webex)** för att konfigurera SSO-inställningarna på programsidan.
    1. **[Skapa Cisco Webex-testanvändare](#create-cisco-webex-test-user)** för att ha en motsvarighet till B.Simon i Cisco Webex som är länkad till Azure AD-representationen av användaren.
3. **[Testa SSO](#test-sso)** för att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg för att aktivera Azure AD SSO i Azure-portalen.

1. Leta reda på avsnittet **Hantera** på sidan **Hantera** i [Azure-portalen](https://portal.azure.com/)och välj **Enkel inloggning**.
1. På sidan **Välj en enskild inloggningsmetod** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på redigerings-/pennikonen för Grundläggande **SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    >[!Note]
    >Du kommer att få tjänsten Provider Metadata fil från **Konfigurera Cisco Webex** avsnitt, som förklaras senare i handledningen. 

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    Klistra in värdet för **Svars-URL**i textrutan **Logga** in, som fylls i automatiskt av SP-metadatafilöverföring.

1. Cisco Webex-programmet förväntar sig att hitta SAML-försäkringar i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattributen. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovanstående förväntar sig Cisco Webex-programmet att få fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda men du kan granska dem enligt dina krav.
  
    | Namn |  Källattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** hittar du **XML för federationsmetadata** och väljer **Hämta** för att hämta certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. Kopiera lämpliga webbadresser i avsnittet **Konfigurera Cisco Webex** baserat på dina krav.

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

I det här avsnittet aktiverar du B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cisco Webex.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.
1. Välj **Cisco Webex** i listan över program.
1. På appens översiktssida letar du reda på avsnittet **Hantera** och väljer **Användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialogrutan **Användare och grupper** väljer du **B.Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

## <a name="configure-cisco-webex"></a>Konfigurera Cisco Webex

1. Om du vill automatisera konfigurationen i Cisco Webex måste du installera **webbläsartillägget My Apps Secure Sign-in** genom att klicka på **Installera tillägget**.

    ![Tillägg för mina appar](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera Cisco Webex** kommer att hänvisa dig till Cisco Webex-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Cisco Webex. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3-8.

    ![Konfiguration av installationsprogrammet](common/setup-sso.png)

3. Om du vill konfigurera Cisco Webex manuellt loggar du in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) med fullständiga administratörsuppgifter.

4. Välj **Settings** (Inställningar) och klicka på **Modify** (Ändra) under **Authentication** (Autentisering).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Välj **Integrera en identitetsprovider från tredje part. (Avancerat)** och gå till nästa skärm.

6. På sidan **Import Idp Metadata** (Importera ldp-metadata) kan du antingen dra och släppa Azure AD-metadatafilen på sidan eller använda filbläddraren för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare) och klicka på **Next** (Nästa).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

8. Gå tillbaka till webbläsarfliken **Cisco Cloud Collaboration Management.** Om testet lyckades väljer du **Det här testet lyckades. Aktivera alternativet Enkel inloggning** och klicka på **Nästa**.

### <a name="create-cisco-webex-test-user"></a>Skapa en testanvändare i Cisco Webex

I det här avsnittet skapar du en användare som heter B.Simon i Cisco Webex. I det här avsnittet skapar du en användare som heter B.Simon i Cisco Webex.

1. Logga in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Klicka på **Users** (Användare) och sedan på **Manage Users** (Hantera användare).
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. I rutan **Manage Users** (Hantera användare) väljer du **Manually add or modify users** (Ändra eller lägga till användare manuellt) och klickar på **Next** (Nästa).

4. Välj alternativet **Name and Email address** (Namn och e-postadress). Fyll sedan i textrutan så här:

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Skriv förnamn för användaren som **B**i textrutan **Förnamn.**

    b. I textrutan för **efternamn** skriver du efternamnet: **Simon**.

    c. I textrutan för **e-postadress** skriver du användarens e-postadress: b.simon@contoso.com.

5. Klicka på plustecknet för att lägga till B.Simon. Klicka sedan på **Nästa**.

6. I rutan **Add Services for Users** (Lägg till tjänster för användare) klickar du på **Save** (Spara) och sedan på **Finish** (Slutför).

## <a name="test-sso"></a>Testa SSO

När du väljer Cisco Webex-panelen på åtkomstpanelen bör du automatiskt loggas in på den Cisco Webex som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Cisco Webex med Azure AD](https://aad.portal.azure.com)

- [Vad är sessionskontroll i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du Cisco Webex med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/protect-webex)