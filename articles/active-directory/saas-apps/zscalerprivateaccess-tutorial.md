---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler privat åtkomst (ZPA) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler privat åtkomst (ZPA).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 984498a2b9d4d72ee6bb6b9f0a9e62636bf870bf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226441"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Självstudier: Integrera Zscaler privat åtkomst (ZPA) med Azure Active Directory

I de här självstudierna lär du dig att integrera Zscaler privat åtkomst (ZPA) med Azure Active Directory (AD Azure). När du integrerar Zscaler privat åtkomst (ZPA) med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Zscaler privat åtkomst (ZPA).
* Ge dina användare att automatiskt inloggad till Zscaler privat åtkomst (ZPA) med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Zscaler privat åtkomst (ZPA) enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Zscaler privat åtkomst (ZPA) stöder **SP** -initierad SSO.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Att lägga till Zscaler privat åtkomst (ZPA) från galleriet

För att konfigurera integrering av Zscaler privat åtkomst (ZPA) till Azure AD, som du behöver lägga till Zscaler privat åtkomst (ZPA) från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Zscaler privat åtkomst (ZPA)** i sökrutan.
1. Välj **Zscaler privat åtkomst (ZPA)** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med Zscaler privat åtkomst (ZPA) med en testanvändare kallas **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Zscaler privat åtkomst (ZPA).

Slutför följande byggblock för att konfigurera och testa Azure AD SSO med Zscaler privat åtkomst (ZPA):

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Zscaler privat åtkomst (ZPA)](#configure-zscaler-private-access-zpa)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Zscaler privat åtkomst (ZPA)](#create-zscaler-private-access-zpa-test-user)**  har en motsvarighet för Britta Simon i Zscaler privat åtkomst (ZPA) som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Zscaler privat åtkomst (ZPA)** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > Värdet för **inloggnings-URL** är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Zscaler privat åtkomst (ZPA) klient-supportteamet](https://help.zscaler.com/zpa-submit-ticket) att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **ange upp Zscaler privat åtkomst (ZPA)** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurera Zscaler privat åtkomst (ZPA)

1. Om du vill automatisera konfigurationen inom Zscaler privat åtkomst (ZPA), måste du installera **Mina appar skyddat inloggning webbläsartillägget** genom att klicka på **installera tillägget**.

    ![Mina appar-tillägg](common/install-myappssecure-extension.png)

2. När du lägger till tillägg till webbläsaren, klickar på **installationsprogrammet Zscaler privat åtkomst (ZPA)** omdirigerar dig till programmet Zscaler privat åtkomst (ZPA). Ange administratörsautentiseringsuppgifter för att logga in i Zscaler privat åtkomst (ZPA) därifrån. Webbläsartillägget automatiskt att konfigurera program för dig. och automatisera steg 3 – 6.

    ![Installationskonfiguration](common/setup-sso.png)

3. Om du vill konfigurera Zscaler privat åtkomst (ZPA) manuellt, öppna ett nytt webbläsarfönster och logga till Zscaler privat åtkomst (ZPA) företagets webbplatsen som administratör och utför följande steg:

4. Till vänster i menyn klickar du på **Administration** och gå till **AUTENTISERING** klickar du på avsnittet **IdP-konfigurationen**.

    ![Administratör för Zscaler privat administration](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. I övre högra hörnet, klickar du på **lägga till IdP-konfigurationen**. 

    ![Zscaler privat åtkomst administratören idp](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. På den **lägga till IdP-konfigurationen** sidan utför följande steg:
 
    ![Zscaler privat åtkomst administratören väljer](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Klicka på **Välj fil** att ladda upp den hämta filen Metadata från Azure AD i den **IdP Metadata filöverföring** fält.

    b. Det läser de **IdP metadata** från Azure AD och fylls informationen för fält som visas nedan.

    ![Zscaler privat åtkomst administratören config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Välj din domän från **domäner** fält.
    
    d. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en användare i Azure-portalen kallas Britta Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Zscaler privat åtkomst (ZPA).

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Zscaler privat åtkomst (ZPA)** .
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Skapa testanvändare Zscaler privat åtkomst (ZPA)

I det här avsnittet skapar du en användare som kallas Britta Simon i Zscaler privat åtkomst (ZPA). Kontakta [Zscaler privat åtkomst (ZPA) supportteamet](https://help.zscaler.com/zpa-submit-ticket) att lägga till användare i Zscaler privat åtkomst (ZPA)-plattformen.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Zscaler privat åtkomst (ZPA) i åtkomstpanelen, bör du vara loggas in automatiskt till den Zscaler privat åtkomst (ZPA) som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)