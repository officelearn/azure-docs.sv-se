---
title: 'Självstudier: Azure Active Directory-integrering med Azure AD SAML Toolkit | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Azure AD SAML Toolkit.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091925"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Självstudier: Integrera Azure AD-SAML Toolkit med Azure Active Directory

I de här självstudierna lär du dig att integrera Azure AD SAML Toolkit med Azure Active Directory (AD Azure). När du integrerar Azure AD SAML Toolkit med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Azure AD SAML Toolkit.
* Ge dina användare att automatiskt inloggad till Azure AD SAML Toolkit med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Azure Toolkit för AD-SAML enkel inloggning (SSO) aktiverat prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Azure AD SAML Toolkit **SP** -initierad SSO.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Att lägga till Azure AD SAML Toolkit från galleriet

För att konfigurera integrering av Azure AD SAML Toolkit i Azure AD, som du behöver lägga till Azure AD SAML Toolkit från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Azure AD SAML Toolkit** i sökrutan.
1. Välj **Azure AD SAML Toolkit** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Azure AD SAML Toolkit med en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Azure AD SAML Toolkit.

Om du vill konfigurera och testa Azure AD enkel inloggning med Azure AD SAML Toolkit, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Azure AD SAML Toolkit SSO](#configure-azure-ad-saml-toolkit-sso)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa Azure AD SAML Toolkit testanvändare](#create-azure-ad-saml-toolkit-test-user)**  har en motsvarighet för B.Simon i Azure AD SAML-verktyg som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Azure AD SAML Toolkit** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    1. I rutan **Inloggnings-URL** anger du en URL: `https://samltoolkit.azurewebsites.net/`

    1. I textrutan **Identifierare (entitets-ID)** anger du en URL: `https://samltoolkit.azurewebsites.net`

    1. I textrutan **Svars-URL** skriver du en URL: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **Federation Metadata-XML**  från de angivna alternativen enligt dina behov och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **konfigurera Azure AD SAML Toolkit** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Konfigurera Azure AD SAML Toolkit SSO

1. Öppna ett nytt webbläsarfönster, om du inte har registrerat i Azure AD SAML Toolkit-webbplatsen först registrera genom att klicka på den **registrera**. Om du har redan är registrerad kan du logga in på din Azure AD SAML Toolkit företagets webbplats med det registrerade i autentiseringsuppgifter.

    ![Azure AD SAML Toolkit Register](./media/saml-toolkit-tutorial/register.png)

1. Klicka på den **SAML-konfiguration**.

    ![Azure AD-SAML Toolkit SAML-konfiguration](./media/saml-toolkit-tutorial/saml-configure.png)

1. Klicka på **Skapa**.

    ![Azure AD SAML Toolkit Create SSO](./media/saml-toolkit-tutorial/createsso.png)

1. På den **SAML SSO-konfiguration** utför följande steg:

    ![Azure AD SAML Toolkit Create SSO](./media/saml-toolkit-tutorial/fill-details.png)

    1. I den **inloggnings-URL** textrutan klistra in den **inloggnings-URL** värde, som du har kopierat från Azure-portalen.

    1. I den **Azure AD-identifierare** textrutan klistra in den **Azure AD-identifierare** värde, som du har kopierat från Azure-portalen.

    1. I textrutan **Utloggnings-URL** klistrar du värdet för **Utloggnings-URL**, som du har kopierat från Azure-portalen.

    1. Klicka på **Välj fil** och ladda upp den **federationsmetadata XML** fil som du har hämtat från Azure-portalen.

    1. Klicka på **Skapa**.

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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning om du beviljar åtkomst till Azure AD SAML Toolkit.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Azure AD SAML Toolkit**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Skapa Azure AD SAML Toolkit testanvändare

I det här avsnittet skapas en användare som kallas B.Simon i Azure AD SAML Toolkit. Azure AD-SAML Toolkit stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Azure AD SAML Toolkit, skapas en ny efter autentisering.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer Azure AD SAML Toolkit-panelen i åtkomstpanelen, bör det vara loggas in automatiskt till Azure AD SAML-verktyg som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
