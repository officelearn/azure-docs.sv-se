---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Bizagi Studio för digital process Automation | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bizagi Studio för digital process Automation.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3d4613-c3fb-485c-b7b9-c385713e6f8f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f7dd96bc2767b98174bee2cadaa93a6bfa1459
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78207514"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-studio-for-digital-process-automation"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med Bizagi Studio för digital process Automation

I den här självstudien får du lära dig att integrera Bizagi Studio för digital process Automation med Azure Active Directory (Azure AD). När du integrerar Bizagi Studio för digital process Automation med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Bizagi Studio för digital process Automation.
* Gör det möjligt för användarna att logga in automatiskt till Bizagi Studio för digital process Automation med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Bizagi Studio för digital process-till-inloggning med enkel inloggning (SSO) som är aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Bizagi Studio för digital process Automation stöder **SP** -INITIERAd SSO
* När du har konfigurerat Bizagi Studio för digital process Automation kan du genomdriva sessionsnycklar som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-studio-for-digital-process-automation-from-the-gallery"></a>Lägga till Bizagi Studio för digital process automation från galleriet

Om du vill konfigurera integreringen av Bizagi Studio för digital process automatisering i Azure AD måste du lägga till Bizagi Studio för digital process automation från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Bizagi Studio för digital process Automation** i sökrutan.
1. Välj **Bizagi Studio för digital process automatisering** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-studio-for-digital-process-automation"></a>Konfigurera och testa enkel inloggning med Azure AD för Bizagi Studio för digital process Automation

Konfigurera och testa Azure AD SSO med Bizagi Studio för digital process automatisering med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Bizagi Studio för automatisering av digitala processer.

Om du vill konfigurera och testa Azure AD SSO med Bizagi Studio för digital process Automation, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Bizagi Studio för digital process Automation SSO](#configure-bizagi-studio-for-digital-process-automation-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Bizagi Studio för digital process Automation test User](#create-bizagi-studio-for-digital-process-automation-test-user)** -om du vill ha en motsvarighet till B. Simon i Bizagi Studio för digital process Automation som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/) **Studio för digital process Automation** -programintegration i, letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I rutan **Inloggnings-URL** anger du en URL: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL:`https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL:en och identifieraren. Kontakta [Bizagi Studio för digital process Automation support team](mailto:jarvein.rivera@bizagi.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en för appens Federations-metadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Bizagi Studio för digital process automatisering.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Bizagi Studio för automatisering av digitala processer**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-bizagi-studio-for-digital-process-automation-sso"></a>Konfigurera Bizagi Studio för digital process Automation SSO

Om du vill konfigurera enkel inloggning på **Bizagi Studio för digital process Automation** -sidan måste du skicka **URL: en för appens Federations-metadata** till [Bizagi Studio för support teamet för digital process Automation](mailto:jarvein.rivera@bizagi.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-bizagi-studio-for-digital-process-automation-test-user"></a>Skapa Bizagi Studio för digital process Automation test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i Bizagi Studio för digital process automatisering. Arbeta med [Bizagi Studio för digital process Automation support team](mailto:jarvein.rivera@bizagi.com) för att lägga till användare i Bizagi Studio för digital process Automation-plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Bizagi Studio för digital process automatisering i åtkomst panelen, bör du loggas in automatiskt på Bizagi Studio för digital process Automation för vilken du konfigurerar SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Bizagi Studio för digital process Automation med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)