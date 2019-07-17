---
title: 'Självstudier: Azure Active Directory-integrering med abstrakt | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och abstrakt.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8038784c9da30a42541688536169576cd9a92e9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235013"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Självstudier: Integrera abstrakt med Azure Active Directory

I de här självstudierna lär du dig att integrera abstrakt med Azure Active Directory (AD Azure). När du integrerar abstrakt med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till abstrakt.
* Ge dina användare att automatiskt inloggad till abstrakt med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration abstrakt enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Abstrakt stöder **SP och IDP** -initierad SSO

## <a name="adding-abstract-from-the-gallery"></a>Att lägga till abstrakt från galleriet

För att konfigurera integrering av abstrakt i Azure AD, som du behöver lägga till abstrakt från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **abstrakta** i sökrutan.
1. Välj **abstrakta** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med abstrakt med en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i abstrakt.

Om du vill konfigurera och testa Azure AD enkel inloggning med abstrakt, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera abstrakt SSO](#configure-abstract-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa abstrakta testanvändare](#create-abstract-test-user)**  – du har en motsvarighet för Britta Simon i abstrakt som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **abstrakta** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** avsnittet programmet är förinställd i **IDP** initierad läge och nödvändiga URL: er är redan ifyllda på förhand med Azure. Användaren behöver för att spara konfigurationen genom att klicka på den **spara** knappen.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: `https://app.abstract.com/signin`

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Konfigurera abstrakt SSO

Se till att hämta din `App Federation Metadata Url` och `Azure AD Identifier` från Azure-portalen som du behöver dem för att konfigurera enkel inloggning på abstrakt.

Du hittar dessa information på den **ange in enkel inloggning med SAML** sidan:

* Den `App Federation Metadata Url` finns i den **SAML-signeringscertifikat** avsnittet.
* Den `Azure AD Identifier` finns i den **konfigurera abstrakt** avsnittet.


Du är nu redo att konfigurera enkel inloggning på abstrakt:

>[!Note]
>Du behöver att autentisera med en organisation administratörskonto för att komma åt inställningarna för enkel inloggning på abstrakt.

1. Öppna den [abstrakta webbapp](https://app.abstract.com/).
2. Gå till den **behörigheter** sida i vänster Sidopanel.
3. I den **Konfigurera SSO** anger din **Metadata_url** och **entitets-ID**.
4. Ange eventuella manuella undantag som du kanske har. E-postmeddelanden som anges i avsnittet Manuell undantag kommer kringgå SSO och logga in med e-post och lösenord. 
5. Klicka på **Spara ändringar**.

>[!Note] 
>Du måste använda primära e-postadresser i undantagslistan över manuella. Aktivering av enkel inloggning misslyckas om e-postmeddelandet du lista är en användares sekundära e-postadress. Om det händer visas ett felmeddelande med den primära e-postadress för kontot misslyckas. Lägg till den primära e-post i listan manuell undantag när du har kontrollerat att du vet att användaren.

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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till abstrakt.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **abstrakta**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-abstract-test-user"></a>Skapa abstrakta testanvändare

Testa enkel inloggning på abstrakt:

1. Öppna den [abstrakta webbapp](https://app.abstract.com/).
2. Gå till den **behörigheter** sida i vänster Sidopanel.
3. Klicka på **Test med mitt konto**. Om testet misslyckas [kontakta Vårt supportteam](https://www.abstract.com/help/contact/).

>[!Note]
>Du behöver att autentisera med en organisation administratörskonto för att komma åt inställningarna för enkel inloggning på abstrakt.
Den här organisationen administratörskonto måste tilldelas till abstrakt på Azure portal.

### <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen abstrakt i åtkomstpanelen, du bör vara loggas in automatiskt Abstract som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

