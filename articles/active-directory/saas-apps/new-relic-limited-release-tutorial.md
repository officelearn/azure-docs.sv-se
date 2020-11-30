---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med New Relic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och nya Relic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/04/2020
ms.author: jeedes
ms.openlocfilehash: 4cf3f9d0ae23bab4d2412b47e5841d6b8a56b65a
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327074"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med nya Relic

I den här självstudien får du lära dig att integrera nya Relic med Azure Active Directory (Azure AD). När du integrerar nya Relic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till nya Relic.
* Gör det möjligt för användarna att logga in automatiskt till nya Relic med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ny Relic-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* New Relic stöder **SP-och IDP** -initierad SSO.
* När du har konfigurerat nya Relic kan du framtvinga kontroll av sessionen, som skyddar mot exfiltrering och intrånget av organisationens känsliga data i real tid. Kontroll av sessionen utökas från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).

## <a name="add-new-relic-application-from-the-gallery"></a>Lägg till nytt Relic-program från galleriet

Om du vill konfigurera integrering av nya Relic i Azure AD måste du lägga till **nya Relic (efter organisation)** från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. Välj tjänsten **Azure Active Directory** .
1. Välj **företags program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. På sidan Sök i **Azure AD-galleriet** skriver du **New Relic (av organisation)** i sökrutan.
1. Välj **ny Relic (efter organisation)** på resultat panelen och välj sedan **skapa**. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurera och testa Azure AD SSO för nya Relic

Konfigurera och testa Azure AD SSO med nya Relic med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i nya Relic.

Utför följande steg för att konfigurera och testa Azure AD SSO med New Relic:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
   1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
   1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera nya Relic SSO](#configure-new-relic-sso)** – för att konfigurera inställningarna för enkel inloggning på den nya Relic-sidan.
   1. **[Skapa en ny Relic-test-användare](#create-a-new-relic-test-user)** för att få en motsvarighet för B. Simon i nya Relic som är länkat till Azure AD-användaren.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **ny Relic efter organisations** program integration i [Azure Portal](https://portal.azure.com/), letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.

1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.

1. På sidan **Konfigurera en enskild Sign-On med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** fyller du i värden för **identifierare** och **svars-URL**.

   * Dessa värden hämtas mitt med den nya Relic **My Organization** -appen. Utför följande steg för att använda det här programmet:
      1. [Logga in](https://login.newrelic.com/) på nya Relic.
      1. På den översta menyn väljer du **appar**.
      1. I avsnittet **appar väljer du** **min organisation**.
      1. Klicka på **domän för autentisering**.
      1. Välj den domän för autentisering som du vill att Azure AD SSO ska ansluta till (om du har fler än en domän för autentisering). De flesta företag har bara en autentiserings domän som kallas **standard**. Du behöver inte välja bara en domän för autentisering.
      1. I avsnittet **autentisering** innehåller **intygets konsument-URL** det värde som ska användas för **svars-URL**.
      1. I avsnittet **Authentication** innehåller **vårt entitets-ID** det värde som ska användas för **identifierare**.

1. I avsnittet **användarattribut &-anspråk** kontrollerar du att **unikt användar-ID** är mappat till ett fält som innehåller den e-postadress som används vid nya Relic.

   * Standard fältet **User. UserPrincipalName** fungerar om det är samma som de nya e-postadresserna för Relic.
   * Fält  **användaren. mail** kan fungera bättre för dig om **User. UserPrincipalName** inte är den nya e-postadressen för Relic.

1. I avsnittet **SAML-signeringscertifikat för certifikat** kopierar du **URL för app Federation-Metadata** och sparar dess värde för senare användning.

1. I avsnittet **Konfigurera nya Relic per organisation** , kopierar du **inloggnings-URL** och sparar dess värde för senare användning.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. Välj **Azure Active Directory** tjänst i Azure Portal.
1. Välj **Användare**.
1. Om du vill lägga till en ny användare väljer du **ny användare** överst på skärmen.
1. Följ dessa steg på sidan **ny användare** :
   1. I fältet **användar namn** anger du username@companydomain.extension . Exempelvis `b.simon@contoso.com`. Detta ska matcha den e-postadress som du kommer att använda på den nya Relic-sidan.
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. Markera kryss rutan **Visa lösen ord** och spara sedan värdet som visas i fältet **Ursprungligt lösen ord** .
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure AD genom att bevilja åtkomst till den **nya Relic av organisations** program.

1. Välj **Azure Active Directory** tjänst i Azure Portal.
1. Välj **företags program**.
1. I listan program väljer du **ny Relic efter organisation**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare** och välj sedan **användare och grupper** (eller **användare** beroende på din plan nivå) i dialog rutan **Lägg till tilldelning** .

   ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** (eller **användare**) väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-new-relic-sso"></a>Konfigurera New Relic SSO

Följ de här stegen för att konfigurera SSO vid nya Relic.

1. [Logga in](https://login.newrelic.com/) på nya Relic.

1. På den översta menyn väljer du **appar**.

1. I avsnittet **appar väljer du** **min organisation**.

1. Klicka på **domän för autentisering**.

1. Välj den domän för autentisering som du vill att Azure AD SSO ska ansluta till (om du har fler än en domän för autentisering). De flesta företag har bara en autentiserings domän som kallas **standard**. Du behöver inte välja bara en domän för autentisering.

1. I avsnittet **autentisering** klickar du på **Konfigurera**.

   1. I fältet **källa för SAML-metadata** anger du det värde som du tidigare sparade från fältet URL för Azure AD Side **app Federation Metadata** .

   1. I fältet **SSO target URL** anger du det värde som du tidigare sparade från fältet **inloggnings-URL** för Azure AD Side.

   1. Klicka på **Spara** när du har verifierat att inställningarna ser snygg ut på både Azure AD och nya Relic-sidor. Om båda sidorna inte är korrekt konfigurerade kan användarna inte logga in på nya Relic.

### <a name="create-a-new-relic-test-user"></a>Skapa en ny Relic-test användare

I det här avsnittet skapar du en användare som heter B. Simon i New Relic. Följ de här stegen för att skapa användaren.

1. [Logga in](https://login.newrelic.com/) på nya Relic.

1. På den översta menyn väljer du **appar**.

1. I avsnittet **appar väljer du** **användar hantering**.

1. Klicka på knappen **Lägg till användare** .

   1. I fältet **namn** anger du **B. Simon**.
   
   1. I fältet **e-post** anger du det värde som ska skickas av Azure AD SSO.
   
   1. Välj en användar **typ** och en användar **grupp** för användaren. För en test användare är **grundläggande användare** för typ och **användare** i gruppen rimliga val.
   
   1. Spara användaren genom att klicka på **Lägg till användare** .

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på den **nya Relic av organisations** panelen i åtkomst panelen, bör du loggas in automatiskt på nya Relic. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](./tutorial-list.md)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory? ](../manage-apps/what-is-single-sign-on.md)

- [Vad är villkorlig åtkomst i Azure Active Directory?](../conditional-access/overview.md)

- [Prova nya Relic med Azure AD](https://aad.portal.azure.com/)

- [Vad är session Control i Microsoft Cloud App Security?](/cloud-app-security/proxy-intro-aad)
