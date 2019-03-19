---
title: 'Självstudier: Azure Active Directory-integrering med SAP Cloud Platform Identity Authentication | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform Identity Authentication.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e242e85525b446fcbe8a2ec05da539fb45acf487
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "57850317"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>Självstudier: Azure Active Directory-integrering med SAP Cloud Platform Identity Authentication

I den här självstudien får du lära dig hur du integrerar SAP Cloud Platform Identity Authentication med Azure Active Directory (Azure AD).
Integreringen av SAP Cloud Platform Identity Authentication med Azure AD medför följande fördelar:

* Du kan styra i Azure AD vem som har åtkomst till SAP Cloud Platform Identity Authentication.
* Du kan göra så att dina användare automatiskt loggas in på SAP Cloud Platform Identity Authentication (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att konfigurera Azure AD-integrering med SAP Cloud Platform Identity Authentication:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SAP Cloud Platform Identity Authentication-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAP Cloud Platform Identity Authentication stöder **SP**- och **IDP**-initierad enkel inloggning

Innan du går in närmare på de tekniska detaljerna är det viktigt att förstå de begrepp som kommer att påträffa. Med SAP Cloud Platform Identity Authentication och Active Directory Federation Services kan du implementera enkel inloggning över flera program eller tjänster som skyddas av Azure AD (som IdP) med SAP-program och -tjänster som skyddas av SAP Cloud Platform Identity Authentication.

För närvarande fungerar SAP Cloud Platform Identity Authentication som Proxy Identity Provider (proxyidentitetsprovider) för SAP-program. Azure Active Directory fungerar i sin tur som ledande identitetsprovider i den här konfigurationen. 

Följande diagram illustrerar den här relationen:

![Skapa en Azure AD-användare för testning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Med den här konfigurationen är din klientorganisation för SAP Cloud Platform Identity Authentication konfigurerad som ett betrott program i Azure Active Directory.

Alla SAP-program och -tjänster som du vill skydda på det här sättet konfigureras sedan i hanteringskonsolen för SAP Cloud Platform Identity Authentication.

Därför behöver auktoriseringen för att bevilja åtkomst till SAP-program och -tjänster ske i SAP Cloud Platform Identity Authentication (i stället för i Azure Active Directory).

Genom att konfigurera SAP Cloud Platform Identity Authentication som ett program via Azure Active Directory Marketplace behöver du inte konfigurera enskilda anspråk eller SAML-intyg.

> [!NOTE]
> För närvarande har endast enkel inloggning på webben testats av båda parter. Flöden som är nödvändiga för kommunikation app-till-API eller API-till-app bör fungera men har inte testats ännu. De kommer att testas under senare aktiviteter.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägga till SAP Cloud Platform Identity Authentication från galleriet

För att konfigurera integrering av SAP Cloud Platform Identity Authentication med Azure AD behöver du lägga till SAP Cloud Platform Identity Authentication från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP Cloud Platform Identity Authentication från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAP Cloud Platform Identity Authentication**, väljer **SAP Cloud Platform Identity Authentication** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![SAP Cloud Platform Identity Authentication i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning Azure AD med [programnamn] baserat på en testanvändare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i [Programnamn] upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med [Programnamn] behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för SAP Cloud Platform Identity Authentication](#configure-sap-cloud-platform-identity-authentication-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa en SAP Cloud Platform Identity Authentication-testanvändare](#create-sap-cloud-platform-identity-authentication-test-user)** – för att ha en motsvarighet till Britta Simon i SAP Cloud Platform Identity Authentication som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Konfigurera enkel inloggning med Azure AD med [Programnamn] genom att utföra följande steg:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **SAP Cloud Platform Identity Authentication** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du vill konfigurera i **IDP**-initierat läge:

    ![SAP Cloud Platform Identity Authentication-domän och information om URL:er för enkel inloggning](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `<IAS-tenant-id>.accounts.ondemand.com`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [kundsupporten för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) och be om dessa värden. Om du inte förstår identifierarvärde läser du dokumentationen för SAP Cloud Platform Identity Authentication om [SAML 2.0-konfiguration för klientorganisation](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![SAP Cloud Platform Identity Authentication-domän och information om URL:er för enkel inloggning](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Använd inloggnings-URL för ditt specifika affärsprogram. Kontakta [kundsupporten för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) om du behöver hjälp.

6. SAP Cloud Platform Identity Authentication-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. Om ditt SAP-program förväntar sig ett attribut såsom **firstName** lägger du till attributet **firstName** i avsnittet **Användaranspråk** i dialogrutan **Användarattribut**, konfigurerar SAML-tokenattributet på det sätt som visas på bilden ovan och utför följande steg:

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du attributnamnet firstName.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** väljer du attributvärdet user.givenname.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. I avsnittet **Konfigurera SAP Cloud Platform Identity Authentication** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sap-cloud-platform-identity-authentication-single-sign-on"></a>Konfigurera enkel inloggning för SAP Cloud Platform Identity Authentication

1. För att konfigurera enkel inloggning för programmet går du till administrationskonsolen för SAP Cloud Platform Identity Authentication. URL:en har följande mönster: `https://<tenant-id>.accounts.ondemand.com/admin`. Läs sedan dokumentationen om SAP Cloud Platform Identity Authentication i avsnittet om [integrering med Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html).

2. I Azure-portalen väljer du knappen **Spara**.

3. Fortsätt endast med följande om du vill lägga till och aktivera enkel inloggning för ett annat SAP-program. Upprepa stegen från avsnittet **Lägga till SAP Cloud Platform Identity Authentication från galleriet**.

4. I Azure-portalen går du till programintegreringssidan för **SAP Cloud Platform Identity Authentication** och väljer **Linked Sign-on** (Länkad inloggning).

    ![Konfigurera länkad inloggning](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Spara konfigurationen.

> [!NOTE]
> Det nya programmet använder konfigurationen för enkel inloggning från föregående SAP-program. Se till att du använder samma företagsidentitetsprovidrar (Corporate Identity Providers) i administrationskonsolen för SAP Cloud Platform Identity Authentication.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till SAP Cloud Platform Identity Authentication.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **SAP Cloud Platform Identity Authentication**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SAP Cloud Platform Identity Authentication**.

    ![Länken för SAP Cloud Platform Identity Authentication i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Skapa en testanvändare för SAP Cloud Platform Identity Authentication

Du behöver inte skapa en användare i SAP Cloud Platform Identity Authentication. Användare som finns i Azure AD-användararkivet kan använda funktionen för enkel inloggning.

SAP Cloud Platform Identity Authentication stöder alternativet för identitetsfederation. Det här alternativet gör att programmet kan kontrollera huruvida användare som autentiseras av företagsidentitetsprovidern finns i användararkivet för SAP Cloud Platform Identity Authentication.

Alternativet för identitetsfederation är inaktiverat som standard. Om identitetsfederation är aktiverat kan endast de användare som importeras i SAP Cloud Platform Identity Authentication få åtkomst till programmet.

Mer information om hur du aktiverar eller inaktiverar identitetsfederation med SAP Cloud Platform Identity Authentication finns i ”Aktivera identitetsfederation med SAP Cloud Platform Identity Authentication” i avsnittet om att [konfigurera identitetsfederation med användararkivet för SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på SAP Cloud Platform Identity Authentication-panelen i åtkomstpanelen bör du automatiskt loggas in på SAP Cloud Platform Identity Authentication som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
