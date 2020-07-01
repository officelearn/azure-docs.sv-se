---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med EasySSO för BitBucket | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EasySSO för BitBucket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ed96e97-b590-4dca-8d00-36288444c641
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a5d905f349caf4aa6e633009de15c9253d6e0fd
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2020
ms.locfileid: "85607021"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-bitbucket"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med EasySSO för BitBucket

I den här självstudien får du lära dig hur du integrerar EasySSO för BitBucket med Azure Active Directory (Azure AD). När du integrerar EasySSO för BitBucket med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till EasySSO för BitBucket.
* Gör det möjligt för användarna att logga in automatiskt till EasySSO för BitBucket med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* EasySSO för BitBucket-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* EasySSO för BitBucket stöder **SP-och IDP** -INITIERAd SSO
* EasySSO för BitBucket stöder **just-in-Time** User-etablering
* När du har konfigurerat EasySSO för BitBucket kan du framtvinga kontroll av sessionen, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-easysso-for-bitbucket-from-the-gallery"></a>Lägga till EasySSO för BitBucket från galleriet

Om du vill konfigurera integreringen av EasySSO för BitBucket i Azure AD måste du lägga till EasySSO för BitBucket från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **EasySSO för BitBucket** i sökrutan.
1. Välj **EasySSO för BitBucket** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-easysso-for-bitbucket"></a>Konfigurera och testa enkel inloggning med Azure AD för EasySSO för BitBucket

Konfigurera och testa Azure AD SSO med EasySSO för BitBucket med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i EasySSO för BitBucket.

Om du vill konfigurera och testa Azure AD SSO med EasySSO för BitBucket slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera EasySSO för BITBUCKET SSO](#configure-easysso-for-bitbucket-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa EasySSO för BitBucket test User](#create-easysso-for-bitbucket-test-user)** -om du vill ha en motsvarighet till B. Simon i EasySSO för BitBucket som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **EasySSO för BitBucket** . Leta reda på avsnittet **Hantera** och välj **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster:`https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster:`https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:`https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [EasySSO support team](mailto:support@techtime.co.nz) för att få dessa värden om du är tveksam. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. EasySSO för BitBucket-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig EasySSO för BitBucket-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.
    
    | Name | Källattribut|
    | ---------------| --------- |
    | urn: OID: 0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn: OID: 2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Om dina Azure AD-användare har konfigurerat **sAMAccountName** måste du mappa **urn: OID: 0.9.2342.19200300.100.1.1** till **sAMAccountName** -attributet.
    
1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på **Hämta** Länkar för **certifikat (base64)** eller **federationsmetadata XML-** alternativ och sparar antingen eller alla på datorn. Du behöver den senare för att kunna konfigurera BitBucket-EasySSO.

    ![Länk för nedladdning av certifikatet](./media/easysso-for-bitbucket-tutorial/certificate.png)
    
    Om du planerar att utföra EasySSO för BitBucket-konfiguration manuellt med certifikat, måste du också kopiera **inloggnings-URL** och **Azure AD-identifierare** från avsnittet nedan och spara dem på din dator.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till EasySSO för BitBucket.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **EasySSO för BitBucket**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-easysso-for-bitbucket-sso"></a>Konfigurera EasySSO för BitBucket SSO

1. Logga in på Atlassian BitBucket-instansen med administratörs behörighet och navigera till avsnittet **Administration** . 

    ![Hantera appar](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-1.png)
1. På sidan hittar du **EasySSO** och klickar på den.

    ![Enkel inloggning](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-2.png)

1. Välj **SAML** -alternativ. Detta tar dig till avsnittet om SAML-konfiguration.

    ![SAML](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-3.png)

1. Fliken Välj **certifikat** längst upp och visas på följande skärm:

    ![Metadata-URL](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-4.png)

1. Nu ska du leta upp **certifikat (base64)** eller **metadatafilen** som du har sparat i de tidigare stegen i **Azure AD SSO** -konfigurationen. Du har följande alternativ för hur du går vidare:

    a. Använd den app Federation- **metadatafil** som du laddade ned till en lokal fil på din dator. Välj **Ladda upp** alternativ knapp och följ dialog rutan Ladda upp fil som är unik för ditt operativ system

    **ELLER**

    b. Öppna appens Federations **ETA data** för att se innehållet (i valfri text redigerare) i filen och kopiera den till Urklipp. Välj **inmatat** alternativ och klistra in innehåll i Urklipp i textfältet.
 
    **ELLER**

    c. Helt manuell konfiguration. Öppna appens Federations **certifikat (base64)** om du vill se innehållet (i valfri text redigerare) i filen och kopiera det till Urklipp. Klistra in det i textfältet **IDP token Signature certificates** . Gå sedan till fliken **Allmänt** och fyll **i bindnings-URL** och **entitets-ID** med respektive värden för **inloggnings-URL** och **Azure AD-identifierare** som du sparade tidigare.
 
1. Klicka på knappen **Spara** längst ned på sidan. Du kan se innehållet i metadata eller certifikatfiler parsas i konfigurations fälten. EasySSO för BitBucket-konfigurationen har slutförts.

1. För bästa test upplevelse går du till fliken **titta & känsla** och markerar knappen för **SAML-inloggning** på. Detta aktiverar separat knapp på BitBucket-inloggnings skärmen för att testa att Azure AD SAML-integration slutar till slutet. Du kan lämna den här knappen och konfigurera dess placering, färg och översättning för produktions läget.

    ![Titta & känsla](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-5.png)
    > [!NOTE]
    >Kontakta [EasySSO support team](mailto:support@techtime.co.nz)om du har några problem.

### <a name="create-easysso-for-bitbucket-test-user"></a>Skapa EasySSO för BitBucket test användare

I det här avsnittet skapas en användare som kallas Britta Simon i BitBucket. EasySSO för BitBucket har stöd för just-in-Time User-etablering, som är **inaktive rad** som standard. Om du vill aktivera användar etablering måste du markera kryss rutan **skapa användare på ett lyckat inloggnings** alternativ i avsnittet Allmänt i konfiguration av EasySSO-plugin-programmet. Om en användare inte redan finns i BitBucket skapas en ny efter autentiseringen.

Men om du inte vill aktivera automatisk användar etablering för användaren första inloggningen måste användarna finnas i backend-användargrupper som BitBucket-instansen använder, till exempel LDAP eller Atlassian Fully.

![Användaretablering](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-6.png)

## <a name="test-sso"></a>Testa SSO 

### <a name="idp-initiated-workflow"></a>IdP-initierat arbets flöde

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen EasySSO för BitBucket på åtkomst panelen, bör du loggas in automatiskt på BitBucket-instansen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

### <a name="sp-initiated-workflow"></a>SP-initierat arbets flöde

I det här avsnittet ska du testa din Azure AD-konfiguration för enkel inloggning med BitBucket **SAML Logid** -knappen.

![SAML-inloggning för användare](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-7.png)

I det här scenariot förutsätter vi att du har aktiverat **knappen SAML-inloggning** på fliken **Sök & känner** på EasySSO-BitBucket (se ovan). Öppna din inloggnings-URL för BitBucket i webbläsarens Incognito-läge för att undvika störningar i befintliga sessioner. Klicka på knappen **SAML-inloggning** så omdirigeras du till Azure AD User Authentication Flow. När du har slutfört omdirigeras du tillbaka till BitBucket-instansen som autentiserad användare via SAML.

Det finns en risk att du kan stöta på följande skärm när du har omdirigerat tillbaka från Azure AD

![Skärmen EasySSO-krasch](./media/easysso-for-bitbucket-tutorial/bitbucket-admin-8.png)

I det här fallet måste du följa [anvisningarna på den här sidan]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) för att få åtkomst till **Atlassian-Bitbucket. log** -filen. Information om felet är tillgängligt med referens-ID: t som finns på fel sidan för EasySSO.

Om du har några problem med att sammanfatta logg meddelandena kontaktar du [EasySSO support team](mailto:support@techtime.co.nz).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa EasySSO för BitBucket med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Så här skyddar du EasySSO för BitBucket med avancerad synlighet och kontroller](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
