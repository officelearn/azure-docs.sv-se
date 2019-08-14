---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med Cisco WebEx | Microsoft Docs'
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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99e6839443a75376224fe6a1007802b6b948a395
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989469"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med Cisco WebEx

I den här självstudien får du lära dig hur du integrerar Cisco WebEx med Azure Active Directory (Azure AD). När du integrerar Cisco WebEx med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Cisco WebEx.
* Gör det möjligt för användarna att logga in automatiskt till Cisco WebEx med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Cisco WebEx-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Cisco WebEx stöder **SP** -initierad SSO och har stöd för **Automatisk** användar etablering.

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

För att konfigurera integrering av Cisco Webex i Azure AD måste du lägga till Cisco Webex från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Cisco WebEx** i sökrutan.
1. Välj **Cisco WebEx** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Konfigurera och testa enkel inloggning med Azure AD för Cisco WebEx

Konfigurera och testa Azure AD SSO med Cisco WebEx med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Cisco WebEx.

Om du vill konfigurera och testa Azure AD SSO med Cisco WebEx slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD](#create-an-azure-ad-test-user)** -testanvändare för att testa enkel inloggning i Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testanvändaren att aktivera B. Simon för att använda enkel inloggning i Azure AD.
2. **[Konfigurera Cisco-WebEx](#configure-cisco-webex)** för att konfigurera SSO-inställningarna på program sidan.
    1. **[Skapa Cisco WebEx-test](#create-cisco-webex-test-user)** för att få en motsvarighet till B. Simon i Cisco WebEx som är länkat till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Cisco WebEx** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** laddar du upp den nedladdade filen med **tjänstleverantörmetadata** och konfigurerar programmet med följande steg:

    >[!Note]
    >Du hämtar metadata-filen för tjänst leverantören från avsnittet **Konfigurera Cisco-WebEx** , som beskrivs senare i självstudien. 

    a. Klicka på **ladda upp metadatafilen**.

    b. Klicka på **mappen logotyp** att välja metadatafilen och klicka på **överför**.

    c. När metadatafilen för tjänstleverantör har laddats upp fylls värdena för **Identifierare** och **Svars-URL** i automatiskt i avsnittet **Grundläggande SAML-konfiguration**:

    I text rutan för inloggnings- **URL** klistrar du in värdet för svars-URL, som **automatiskt**fylls med fil uppladdning av SP-metadatafil.

5. Cisco WebEx-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

6. Utöver ovanstående förväntar sig Cisco Webex-programmet att några fler attribut skickas tillbaka i SAML-svaret. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Namn |  Källattribut|
    | ---------------|--------- |
    | uid | user.userprincipalname |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. I avsnittet **Konfigurera Cisco WebEx** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Cisco WebEx.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Cisco Webex** i listan över program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-cisco-webex"></a>Konfigurera Cisco-WebEx

1. Logga in i [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Välj **Settings** (Inställningar) och klicka på **Modify** (Ändra) under **Authentication** (Autentisering).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
3. Välj **Integrate a 3rd-party identity provider. (Avancerat)** (Integrera en tredjeparts identitetsprovider. (Avancerat)) och gå till nästa skärm.

4. På sidan **Import Idp Metadata** (Importera ldp-metadata) kan du antingen dra och släppa Azure AD-metadatafilen på sidan eller använda filbläddraren för att hitta och ladda upp Azure AD-metadatafilen. Välj sedan **Require certificate signed by a certificate authority in Metadata** (Kräv signerat certifikat av certifikatutfärdare i metadata (säkrare) och klicka på **Next** (Nästa).

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

5. Välj **Test SSO Connection** (Testa den enkla inloggningen) och autentisera med Azure AD-inloggning när en ny webbläsarflik öppnas.

6. Gå tillbaka till webbläsarfliken **Cisco Cloud Collaboration Management** (Ciscos samarbetshantering i molnet). Om testet lyckades väljer du **This test was successful. Enable Single Sign-On option** (Testet lyckades. Aktivera alternativet för enkel inloggning) och klickar på **Next** (Nästa).

### <a name="create-cisco-webex-test-user"></a>Skapa en testanvändare i Cisco Webex

I det här avsnittet skapar du en användare som heter B. Simon i Cisco WebEx. I det här avsnittet skapar du en användare som heter B. Simon i Cisco WebEx.

1. Logga in på [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) (Ciscos samarbetshantering i molnet) med dina autentiseringsuppgifter som administratör.

2. Klicka på **Users** (Användare) och sedan på **Manage Users** (Hantera användare).
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. I rutan **Manage Users** (Hantera användare) väljer du **Manually add or modify users** (Ändra eller lägga till användare manuellt) och klickar på **Next** (Nästa).

4. Välj alternativet **Name and Email address** (Namn och e-postadress). Fyll sedan i textrutan så här:

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. I text rutan Förnamn skriver du första namnet på användaren, t. ex. **B**.

    b. I textrutan för **efternamn** skriver du efternamnet: **Simon**.

    c. Skriv e -postadressen till användaren b.simon@contoso.comi text rutan e-postadress.

5. Klicka på plus tecknet för att lägga till B. Simon. Klicka sedan på **Nästa**.

6. I rutan **Add Services for Users** (Lägg till tjänster för användare) klickar du på **Save** (Spara) och sedan på **Finish** (Slutför).

## <a name="test-sso"></a>Testa SSO

När du väljer panelen Cisco-WebEx på åtkomst panelen, bör du loggas in automatiskt på Cisco-WebEx som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa Cisco WebEx med Azure AD](https://aad.portal.azure.com)