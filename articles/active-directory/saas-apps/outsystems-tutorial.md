---
title: 'Självstudier: Azure Active Directory-integrering med OutSystems Azure AD | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och OutSystems Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: cf6f99b7-0604-4db2-a72e-0d1a1d643a08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ff4f8f45bee86013b3f3603ba12fc121de9d7ce
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475701"
---
# <a name="tutorial-integrate-outsystems-azure-ad-with-azure-active-directory"></a>Självstudier: Integrera OutSystems Azure AD med Azure Active Directory

I de här självstudierna lär du dig att integrera OutSystems Azure AD med Azure Active Directory (AD Azure). När du integrerar OutSystems Azure AD med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till OutSystems Azure AD.
* Ge dina användare att automatiskt inloggad till OutSystems Azure AD med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration OutSystems Azure AD enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. OutSystems Azure AD stöder **SP och IDP** initierad SSO och stöder **Just In Time** etableringen av användare.

## <a name="adding-outsystems-azure-ad-from-the-gallery"></a>Att lägga till OutSystems Azure AD från galleriet

Om du vill konfigurera integreringen av OutSystems Azure AD till Azure AD, som du behöver lägga till OutSystems Azure AD från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **OutSystems Azure AD** i sökrutan.
1. Välj **OutSystems Azure AD** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Azure AD med en testanvändare kallas OutSystems **B. Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i OutSystems Azure AD.

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med OutSystems Azure AD:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera OutSystems Azure AD](#configure-outsystems-azure-ad)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B. Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B. Simon att använda Azure AD enkel inloggning.
5. **[Skapa OutSystems Azure AD-testanvändare](#create-outsystems-azure-ad-test-user)**  har en motsvarighet för B. Simon i OutSystems Azure AD som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **OutSystems Azure AD** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du vill konfigurera programmet i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `http://<YOURBASEURL>/IdP`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<YOURBASEURL>/IdP/SSO.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<YOURBASEURL>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [OutSystems klienten supportteamet](mailto:support@outsystems.com) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **konfigurera OutSystems Azure AD** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-outsystems-azure-ad"></a>Configure OutSystems Azure AD

Att konfigurera enkel inloggning på **OutSystems** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopierade URL: er från Azure portal för att [OutSystems supportteam](mailto:support@outsystems.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas B. Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon att använda Azure enkel inloggning genom att bevilja åtkomst till OutSystems Azure AD.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **OutSystems Azure AD**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B. Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-outsystems-azure-ad-test-user"></a>Skapa testanvändare OutSystems Azure AD

I det här avsnittet skapas en användare som kallas Britta Simon i OutSystems. OutSystems stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i OutSystems, skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen OutSystems Azure AD i åtkomstpanelen, det bör vara loggas in automatiskt till OutSystems Azure AD som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
