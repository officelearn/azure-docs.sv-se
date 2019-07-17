---
title: 'Självstudier: Azure Active Directory-integrering med HyperAnna | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HyperAnna.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3e47846e-eddd-4116-aa51-6b4bb772d2ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed5370fda9be2573f773bd2fe653feb74defeb7d
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/16/2019
ms.locfileid: "68248867"
---
# <a name="tutorial-integrate-hyperanna-with-azure-active-directory"></a>Självstudier: Integrera HyperAnna med Azure Active Directory

I de här självstudierna lär du dig att integrera HyperAnna med Azure Active Directory (AD Azure). När du integrerar HyperAnna med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till HyperAnna.
* Ge dina användare att automatiskt inloggad till HyperAnna med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration HyperAnna enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö.

* Har stöd för HyperAnna **SP och IDP** -initierad SSO

## <a name="adding-hyperanna-from-the-gallery"></a>Att lägga till HyperAnna från galleriet

För att konfigurera integrering av HyperAnna i Azure AD, som du behöver lägga till HyperAnna från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **HyperAnna** i sökrutan.
1. Välj **HyperAnna** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med HyperAnna med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i HyperAnna för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med HyperAnna, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera HyperAnna SSO](#configure-hyperanna-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare HyperAnna](#create-hyperanna-test-user)**  – du har en motsvarighet för Britta Simon i HyperAnna som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **HyperAnna** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierade läge, anger du värden för följande fält:

    I den **svars-URL** text skriver en URL som använder någon av följande mönster:

    | |
    |--|
    | `https://microsoft.hyperanna.com/userservice/auth/saml` |
    | `https://anna.hyperanna.com/userservice/auth/saml` |

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I den **inloggnings-URL** text skriver en URL som använder någon av följande mönster:

    | |
    |--|
    | `https://microsoft.hyperanna.com/` |
    | `https://anna.hyperanna.com/` |

4. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

6. På den **konfigurera HyperAnna** avsnittet, kopiera den lämpliga URL: er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-hyperanna-sso"></a>Konfigurera HyperAnna SSO

Att konfigurera enkel inloggning på **HyperAnna** sida, som du behöver skicka de hämtade **certifikat (Base64)** och lämpliga kopierade URL: er från Azure portal för att [HyperAnna supportteamet](mailto:support@hyperanna.com). De ställer du in SAML SSO ansluta till korrekt inställda på båda sidorna.
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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HyperAnna.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **HyperAnna**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-hyperanna-test-user"></a>Skapa HyperAnna testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i HyperAnna. Arbeta med [HyperAnna supportteamet](mailto:support@hyperanna.com) att lägga till användare i HyperAnna-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-sso"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen HyperAnna i åtkomstpanelen, bör det vara loggas in automatiskt till HyperAnna som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

