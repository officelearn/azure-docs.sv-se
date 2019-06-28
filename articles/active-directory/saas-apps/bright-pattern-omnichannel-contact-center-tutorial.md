---
title: 'Självstudier: Azure Active Directory-integrering med ljusstyrka mönstret Omnichannel kontakta Center | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ljusstyrka mönstret Omnichannel kontakta Center.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: de1e0091-ca10-4e7e-8014-f4579d8eabf6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 205b1746bac30a015d4efe4bde573be44563e2f1
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450263"
---
# <a name="tutorial-integrate-bright-pattern-omnichannel-contact-center-with-azure-active-directory"></a>Självstudier: Integrera ljusstyrka mönstret Omnichannel kontakta Center med Azure Active Directory

I de här självstudierna lär du dig att integrera ljusstyrka mönstret Omnichannel kontakta Center med Azure Active Directory (AD Azure). När du integrerar ljusstyrka mönstret Omnichannel kontakta Center med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till ljusstyrka mönstret Omnichannel kontakta Center.
* Ge dina användare att automatiskt inloggad till ljusstyrka mönstret Omnichannel kontakta Center med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration ljusstyrka mönstret Omnichannel kontakta Center enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Ljus mönstret Omnichannel kontakta Center har stöd för **SP och IDP** -initierad SSO
* Ljus mönstret Omnichannel kontakta Center har stöd för **Just In Time** etableringen av användare

## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Att lägga till ljusstyrka mönstret Omnichannel kontakta Center från galleriet

För att konfigurera integrering av ljusstyrka mönstret Omnichannel kontakta Center i Azure AD, som du behöver lägga till ljusstyrka mönstret Omnichannel kontakta Center från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **ljusstyrka mönstret Omnichannel kontakta Center** i sökrutan.
1. Välj **ljusstyrka mönstret Omnichannel kontakta Center** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med ljusstyrka mönstret Omnichannel kontakta Center med hjälp av en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i ljusstyrka mönstret Omnichannel kontakta Center.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med ljusstyrka mönstret Omnichannel kontakta Center:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera ljusstyrka mönstret Omnichannel kontakta Center SSO](#configure-bright-pattern-omnichannel-contact-center-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa ljusstyrka mönstret Omnichannel kontakta Center testanvändare](#create-bright-pattern-omnichannel-contact-center-test-user)**  – du har en motsvarighet för B.Simon i ljusstyrka mönstret Omnichannel kontakta Center som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **ljusstyrka mönstret Omnichannel kontakta Center** programsidan integration, hitta den **hantera** och väljer **enda inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `<SUBDOMAIN>_sso`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [ljusstyrka mönstret Omnichannel kontakta Center Client supportteamet](mailto:support@brightpattern.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ljus mönstret Omnichannel kontakta Center-program förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Namnrymd  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | email | user.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera ljusstyrka mönstret Omnichannel kontakta Center** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Konfigurera ljusstyrka mönstret Omnichannel kontakta Center SSO

Att konfigurera enkel inloggning på **ljusstyrka mönstret Omnichannel kontakta Center** sida, som du behöver skicka de hämtade **certifikat (Base64)** och lämpliga kopieras URL: er från Azure portal för att [ Ljus mönstret Omnichannel kontakta Center supportteamet](mailto:support@brightpattern.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning om du beviljar åtkomst till ljusstyrka mönstret Omnichannel kontakta Center.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **ljusstyrka mönstret Omnichannel kontakta Center**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Skapa ljusstyrka mönstret Omnichannel kontakta Center testanvändare

I det här avsnittet skapas en användare som kallas B.Simon i ljusstyrka mönstret Omnichannel kontakta Center. Ljus mönstret Omnichannel kontakta Center stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i ljusstyrka mönstret Omnichannel kontakta Center, skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen ljusstyrka mönstret Omnichannel kontakta Center i åtkomstpanelen, bör det vara loggas in automatiskt till ljusstyrka mönstret Omnichannel kontakta-Center där du konfigurerar enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)