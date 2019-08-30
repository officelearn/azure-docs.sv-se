---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med F5 | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och F5.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea4167f5a8f4e29641a999c72f57b368190a34e0
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70166095"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med F5

I den här självstudien får du lära dig hur du integrerar F5 med Azure Active Directory (Azure AD). När du integrerar F5 med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till F5.
* Gör det möjligt för användarna att logga in automatiskt på F5 med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* F5-aktiverad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* F5 SSO kan konfigureras på tre olika sätt.

- [Konfigurera F5 enkel inloggning för sidhuvud baserat program](#configure-f5-single-sign-on-for-header-based-application)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Lägga till F5 från galleriet

Om du vill konfigurera integrering av F5 i Azure AD måste du lägga till F5 från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **F5** i sökrutan.
1. Välj **F5** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurera och testa enkel inloggning med Azure AD för F5

Konfigurera och testa Azure AD SSO med F5 med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i F5.

Om du vill konfigurera och testa Azure AD SSO med F5 slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera F5 SSO](#configure-f5-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa F5 test användare](#create-f5-test-user)** – om du vill ha en motsvarighet till B. Simon i F5 som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **F5** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<YourCustomFQDN>.f5.com/`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<YourCustomFQDN>.f5.com/`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [F5-klientens support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för hämtning av certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera F5** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till F5.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **F5**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-f5-sso"></a>Konfigurera F5 SSO

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Konfigurera F5 enkel inloggning för sidhuvud baserat program

1. Öppna ett nytt webbläsarfönster och logga in på din "F5"-företags webbplats som administratör och utför följande steg:

1. Du måste importera metadata-certifikatet till F5 (Rubrikbaserade) som ska användas senare i installations processen. Gå till **System > certifikat hantering > hantering av trafik certifikat > > lista över SSL-certifikat**. Klicka på **Importera** till höger hörn

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure12.png)
 
1. Dessutom behöver du också ett **SSL-certifikat** för värd namnet`headerapp.superdemo.live`(), i det här exemplet användes certifikat med jokertecken.

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure13.png)

1. Gå till – **F5 (sidhuvud baserat) Big-IP klicka på åtkomst > guidad konfiguration > Federation > SAML-** tjänstprovider.

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure01.png)

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure02.png)
 
1. Ange **entitets-ID: t** (samma som det som du konfigurerade i Azure AD-programkonfigurationen)

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure03.png) 

1. Skapa en ny virtuell server, ange **mål adress**, omdirigerings **port** är valfritt. Välj det **jokertecken** (eller det **certifikat** som du laddade upp för programmet) som vi laddade upp tidigare och den **tillhör ande privata nyckeln**.

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure04.png) 

1. Ladda upp konfigurations- **metadata** och ange ett nytt **namn för SAML IDP Connector** så måste du också ange det Federations certifikat som överfördes tidigare.

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure05.png)
 
1. **Skapa ny** Backend-programpoolen anger du **IP-adressen** för Server dels program servrarna.

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure06.png)

1. Under enkel inloggning väljer du **http-huvudbaserad**. Du kan lägga till andra rubriker baserat på ditt program. Se bilagan för listan med variabler för SAML-sessioner

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure07.png) 

1. Kontakta [F5 (rubrikbaserade) klient support team](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för information om **Egenskaper för slut punkts kontroll** .

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure08.png)

1. Kontakta [F5 (rubrikbaserade) klient support grupp](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för information om **sessions hanterings egenskaper** .

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure09.png)

1. **Granska Sammanfattning** och klicka på **distribuera**.

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure10.png)

    ![F5 (rubrik baserad) konfiguration](./media/headerf5-tutorial/configure11.png)

### <a name="create-f5-test-user"></a>Skapa F5-test användare

I det här avsnittet skapar du en användare som heter B. Simon i F5. Arbeta med [F5-klient support teamet](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) för att lägga till användarna på F5-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på F5-panelen i åtkomst panelen, bör du loggas in automatiskt på F5 som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova F5 med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera enkel inloggning med F5 för Kerberos-program](kerbf5-tutorial.md)

- [Konfigurera enkel inloggning med F5 för avancerat Kerberos-program](advance-kerbf5-tutorial.md)

