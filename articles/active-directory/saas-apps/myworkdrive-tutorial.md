---
title: 'Självstudier: Azure Active Directory-integrering med MyWorkDrive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 61173d21c52d061f0d02ab02eb2f1083507983c2
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991649"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Självstudier: Integrera MyWorkDrive med Azure Active Directory

I de här självstudierna lär du dig att integrera MyWorkDrive med Azure Active Directory (AD Azure). När du integrerar MyWorkDrive med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till MyWorkDrive.
* Ge dina användare att automatiskt inloggad till MyWorkDrive med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration MyWorkDrive enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för MyWorkDrive **SP** och **IDP** -initierad SSO

## <a name="adding-myworkdrive-from-the-gallery"></a>Att lägga till MyWorkDrive från galleriet

För att konfigurera integrering av MyWorkDrive i Azure AD, som du behöver lägga till MyWorkDrive från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **MyWorkDrive** i sökrutan.
1. Välj **MyWorkDrive** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med MyWorkDrive med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i MyWorkDrive för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med MyWorkDrive, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera MyWorkDrive SSO](#configure-myworkdrive-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare MyWorkDrive](#create-myworkdrive-test-user)**  – du har en motsvarighet för Britta Simon i MyWorkDrive som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **MyWorkDrive** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, anger du värden för följande fält:

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med faktisk svars-URL och inloggnings-URL. Ange ditt företag MyWorkDrive Server värd name:e.g.
    > 
    > Svars-URL: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Inloggnings-URL:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Kontakta [MyWorkDrive supportteamet](mailto:support@myworkdrive.com) om du är osäker på hur du ställer in dina egna värdnamn och SSL-certifikat för dessa värden.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** till Urklipp.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Configure MyWorkDrive SSO

1. I ett annat webbläsarfönster, loggar du in MyWorkDrive som en administratör.

2. På MyWorkDrive servern admin-panelen, klickar du på **ENTERPRISE** och utför följande steg:

    ![Administratören](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Aktivera **SAML/ADFS SSO**.

    b. Välj **SAML - Azure AD**

    c. I den **Azure Appfederationsmetadata** textrutan klistra in värdet för **Appfederationsmetadata** som du har kopierat från Azure-portalen.

    d. Klicka på **Spara**

    > [!NOTE]
    > För ytterligare information finns i [MyWorkDrive Azure AD support-artikeln](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till MyWorkDrive.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **MyWorkDrive**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-myworkdrive-test-user"></a>Skapa MyWorkDrive testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i MyWorkDrive. Arbeta med [MyWorkDrive supportteamet](mailto:support@myworkdrive.com) att lägga till användare i MyWorkDrive-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen MyWorkDrive i åtkomstpanelen, bör det vara loggas in automatiskt till MyWorkDrive som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)