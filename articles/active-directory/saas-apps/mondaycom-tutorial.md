---
title: 'Självstudier: Azure Active Directory integrering med monday.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 771b7a07416458fd8523223ca57d3575ae2c85ff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033661"
---
# <a name="tutorial-integrate-mondaycom-with-azure-active-directory"></a>Självstudier: Integrera monday.com med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar monday.com med Azure Active Directory (Azure AD). När du integrerar monday.com med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till monday.com.
* Gör det möjligt för användarna att logga in automatiskt till monday.com med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* monday.com för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. monday.com stöder **SP-och IDP** -initierad SSO och stöder **just-in-Time** User-etablering.

## <a name="adding-mondaycom-from-the-gallery"></a>Lägga till monday.com från galleriet

Om du vill konfigurera integreringen av monday.com i Azure AD måste du lägga till monday.com från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Monday.com** i sökrutan.
1. Välj **Monday.com** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med monday.com med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i monday.com.

Om du vill konfigurera och testa Azure AD SSO med monday.com, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Monday.com](#configure-mondaycom)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD](#create-an-azure-ad-test-user)** -testanvändare för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testanvändaren att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa Monday.com test User](#create-mondaycom-test-user)** för att ha en motsvarighet till B. Simon i Monday.com som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Monday.com** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I fönstret **grundläggande SAML-konfiguration** , om du har en metadata-fil för Service Provider och du vill konfigurera i **IDP** läge, så gör du följande:

    1. Välj **Ladda upp metadatafil**.

    1. Om du vill välja metadatafilen väljer du mappikonen och väljer sedan **Ladda upp**.

    1. När metadatafilen har laddats upp fylls **ID** -och **svars-URL** -värdena automatiskt i fönstret **grundläggande SAML-konfiguration** :

       > [!Note]
       > Om **ID** -och **svars-URL** -värdena inte fylls i automatiskt fyller du i värdena manuellt. **Identifieraren** och svars- **URL** : en är samma och värdet är i följande mönster:`https://<your-domain>.monday.com/saml/saml_callback`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<your-domain>.monday.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL: en och inloggnings-URL: en. Kontakta [Monday.com client support team](mailto:dev@food.ee) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ditt monday.com-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![Fönstret användarattribut](common/edit-attribute.png)

1. Utöver ovan förväntar sig monday.com-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut|
    | ---------------| --------------- |
    | Email | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Ta bort **namn området**.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**.

    g. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. I avsnittet **konfigurera Monday.com** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-mondaycom"></a>Konfigurera monday.com

1. Om du vill automatisera konfigurationen i monday.com måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren klickar du på **konfigurera Monday.com** som leder dig till Monday.com-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på monday.com. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera monday.com manuellt öppnar du ett nytt webbläsarfönster och loggar in på monday.com som administratör och utför följande steg:

1. Gå till **profilen** i det övre högra hörnet av sidan och klicka på **administratör**.

    ![monday.com-konfiguration](./media/mondaycom-tutorial/configuration01.png)

1. Välj **säkerhet** och se till att klicka på **Öppna** bredvid SAML.

    ![monday.com-konfiguration](./media/mondaycom-tutorial/configuration02.png)

1. Fyll i informationen nedan från din IDP.

    ![monday.com-konfiguration](./media/mondaycom-tutorial/configuration03.png)

    >[!NOTE]
    >Mer information finns i [den här](https://support.monday.com/hc/articles/360000460605-SAML-Single-Sign-on?abcb=34642) artikeln

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till monday.com.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Monday.com**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-mondaycom-test-user"></a>Skapa monday.com test användare

I det här avsnittet skapas en användare som heter B. Simon i monday.com. monday.com stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i monday.com skapas en ny när du försöker få åtkomst till monday.com.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen monday.com på åtkomst panelen, bör du loggas in automatiskt på den monday.com som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
