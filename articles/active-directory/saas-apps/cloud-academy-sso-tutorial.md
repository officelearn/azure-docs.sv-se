---
title: 'Självstudie: Azure Active Directory SSO-integrering med Cloud Academy – SSO'
description: I den här självstudien får du lära dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cloud Academy-SSO.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: jeedes
ms.openlocfilehash: 822e28402d0b7829b835ad03a3b3cf7d05c3d343
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96181012"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-cloud-academy---sso"></a>Självstudie: Azure Active Directory integration med enkel inloggning med Cloud Academy – SSO

I den här självstudien får du lära dig hur du integrerar Cloud Academy-SSO med Azure Active Directory (Azure AD). När du integrerar Cloud Academy-SSO med Azure AD kan du:

* Använd Azure AD för att kontrol lera vem som har åtkomst till Cloud Academy-SSO.
* Gör det möjligt för användarna att logga in automatiskt till Cloud Academy-SSO med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är enkel inloggning?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En Cloud Academy-SSO-prenumeration med enkel inloggning aktive rad (SSO).

## <a name="tutorial-description"></a>Beskrivning av självstudie

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

Cloud Academy – SSO stöder SP-initierad SSO.

När du har konfigurerat Cloud Academy-SSO kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Sessions kontroller utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-cloud-academy---sso-from-the-gallery"></a>Lägg till Cloud Academy – SSO från galleriet

Om du vill konfigurera integrering av Cloud Academy-SSO i Azure AD måste du lägga till Cloud Academy-SSO från galleriet i listan över hanterade SaaS-appar:

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller med en personlig Microsoft-konto.
1. Välj **Azure Active Directory** i den vänstra rutan.
1. Gå till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett program.
1. I avsnittet **Lägg till från galleriet** , ange **Cloud Academy-SSO** i sökrutan.
1. Välj **Cloud Academy – SSO** i resultat panelen och Lägg till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-cloud-academy---sso"></a>Konfigurera och testa Azure AD SSO för Cloud Academy – SSO

Du konfigurerar och testar Azure AD SSO med Cloud Academy-SSO med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och motsvarande användare i Cloud Academy-SSO.

Om du vill konfigurera och testa Azure AD SSO med Cloud Academy-SSO, slutför du följande steg på hög nivå:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda funktionen.
    1. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD.
    1. **[Bevilja åtkomst till test användaren](#grant-access-to-the-test-user)** för att göra det möjligt för användaren att använda enkel inloggning med Azure AD.
1. **[Konfigurera enkel inloggning för Cloud Academy – SSO](#configure-single-sign-on-for-cloud-academy)** på program sidan.
    1. **[Skapa en molnbaserad Academy-test användare](#create-a-cloud-academy-test-user)** som motsvarighet till Azure AD-representationen av användaren.
1. **[Testa SSO](#test-sso)** för att verifiera att konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)på sidan **Cloud Academy-SSO-** program integration i avsnittet **Hantera** väljer du **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera en enskild Sign-On med SAML** väljer du Penn knappen för **grundläggande SAML-konfiguration** för att redigera inställningarna:

   ![Skärm bild som visar Penn knappen för att redigera den grundläggande SAML-konfigurationen.](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** i rutan **inloggnings-URL** anger du `https://cloudacademy.com/login/enterprise/` .

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , väljer du kopierings knappen för att kopiera **URL: en för appens federationens metadata**. Spara URL: en.

    ![Skärm bild som visar kopierings knappen för URL: en för app Federation-metadata.](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare som heter B. Simon i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**. Välj **användare** och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna slutför du de här stegen:
   1. I rutan **namn** anger du **B. Simon**.  
   1. I rutan **användar namn** anger du \<username> @ \<companydomain> . \<extension> . Exempelvis `B.Simon@contoso.com`.
   1. Välj **Visa lösen ord** och skriv sedan ned värdet som visas i rutan **lösen ord** .
   1. Välj **Skapa**.

### <a name="grant-access-to-the-test-user"></a>Bevilja åtkomst till test användaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja användaren åtkomst till Cloud Academy-SSO.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Cloud Academy-SSO**.
1. I avsnittet **Hantera** på appens översikts sida väljer du **användare och grupper**:

   ![Skärm bild som visar alternativet användare och grupper.](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** :

    ![Skärm bild som visar knappen Lägg till användare.](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** i listan **användare** och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Klicka på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-single-sign-on-for-cloud-academy"></a>Konfigurera enkel inloggning för Cloud Academy

1. Logga in på din Cloud Academy-SSO-företags webbplats som administratör i ett annat webbläsarfönster.

1. Välj företagets namn och välj sedan **inställningar & integreringar** på menyn som visas:

    ![Skärm bild som visar alternativet Inställningar & integration.](./media/cloud-academy-sso-tutorial/config-1.PNG)

1. På sidan **inställningar & integrations** på fliken **integrations** väljer du kortet **SSO** :

    ![Skärm bild som visar kortet SSO på fliken integrationer.](./media/cloud-academy-sso-tutorial/config-2.PNG)

1. Utför följande steg på den här sidan:

    ![Skärm bild som visar Inegrations > SSO-sidan.](./media/cloud-academy-sso-tutorial/config-3.PNG)

    a. I rutan **entitets-ID URL** anger du det entitets-ID-värde som du kopierade från Azure Portal.

    b. I rutan **SSO URL** klistrar du in URL-värdet för inloggning som du kopierade från Azure Portal.

    c. Öppna det hämtade base64-certifikatet från Azure Portal i anteckningar. Klistra in innehållet i rutan **certifikat** .

    d. Behåll standardvärdet i rutan **namn-ID-format** : `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` .

1. Välj **Spara**.

    > [!NOTE]
    > Mer information om hur du konfigurerar Cloud Academy-SSO finns i [Konfigurera enkel inloggning](https://support.cloudacademy.com/hc/articles/360043908452-Setting-Up-Single-Sign-On).

### <a name="create-a-cloud-academy-test-user"></a>Skapa en test användare i Cloud Academy

1. Logga in på Cloud Academy – SSO.

1. Välj företagets namn och välj sedan **medlemmar** på menyn som visas:

    ![Skärm bild som visar alternativet medlemmar.](./media/cloud-academy-sso-tutorial/create-user.PNG)

1. Välj **Bjud in medlemmar** och välj sedan **Bjud in en enskild medlem**:

    ![Skärm bild som visar alternativet Bjud in en enskild medlem.](./media/cloud-academy-sso-tutorial/create-user-1.PNG)

1. Ange värden i de obligatoriska fälten och välj sedan **Bjud in**:

    ![Skärm bild som visar en dialog ruta för att bjuda in en medlem.](./media/cloud-academy-sso-tutorial/create-user-2.PNG)

## <a name="test-sso"></a>Testa SSO 

Nu ska du testa din Azure AD SSO-konfiguration med hjälp av åtkomst panelen.

När du väljer panelen Cloud Academy-SSO i åtkomst panelen, bör du loggas in automatiskt på den moln Academy-SSO-instans som du ställer in SSO för. Mer information finns i [Introduktion till åtkomst panelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier om hur du integrerar SaaS-appar med Azure Active Directory](./tutorial-list.md)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Testa Cloud Academy – SSO med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)

- [Skydda Cloud Academy-SSO med hjälp av avancerad synlighet och kontroller](/cloud-app-security/proxy-intro-aad)