---
title: 'Självstudier: Azure Active Directory integrering med Otsuka Shokai | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Otsuka Shokai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c04bb636-a3c7-4940-9b36-810425b855d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f558b33079821efcf56731eb95073e0170a72795
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943538"
---
# <a name="tutorial-integrate-otsuka-shokai-with-azure-active-directory"></a>Självstudier: Integrera Otsuka-Shokai med Azure Active Directory

I den här självstudien får du lära dig att integrera Otsuka-Shokai med Azure Active Directory (Azure AD). När du integrerar Otsuka-Shokai med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Otsuka-Shokai.
* Gör det möjligt för användarna att logga in automatiskt till Otsuka Shokai med sina Azure AD-konton.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Otsuka Shokai-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Otsuka Shokai stöder **IDP** initierad SSO.

## <a name="adding-otsuka-shokai-from-the-gallery"></a>Lägga till Otsuka Shokai från galleriet

Om du vill konfigurera integreringen av Otsuka-Shokai i Azure AD måste du lägga till Otsuka Shokai från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Otsuka Shokai** i sökrutan.
1. Välj **Otsuka Shokai** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.
1. Klicka på fliken **Egenskaper** , kopiera **program-ID: t** och spara det på datorn för senare användning.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med Otsuka Shokai med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Otsuka Shokai.

Om du vill konfigurera och testa Azure AD SSO med Otsuka Shokai slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Otsuka Shokai](#configure-otsuka-shokai)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD](#create-an-azure-ad-test-user)** -testanvändare för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testanvändaren att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa Otsuka Shokai test User](#create-otsuka-shokai-test-user)** för att ha en motsvarighet till B. Simon i Otsuka Shokai som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Otsuka Shokai** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** är programmet förkonfigurerat och de nödvändiga URL: erna redan i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Otsuka Shokai-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. Otsuka Shokai-programmet förväntar sig att **NameIdentifier** mappas med **User. ObjectID**, så du måste redigera mappningen av attributet genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. Utöver ovan förväntar sig Otsuka Shokai-program att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ---------------| --------------- |
    | Undanta | `<Application ID>` |

    >[!NOTE]
    >`<Application ID>`är värdet som du har kopierat från fliken **Egenskaper** i Azure Portal.

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

### <a name="configure-otsuka-shokai"></a>Konfigurera Otsuka-Shokai

1. När du ansluter till kundens min sida från SSO-appen startas guiden för SSO-inställningen.

2. Om Otsuka-ID inte är registrerat går du vidare till Otsuka-ID ny registrering.   Om du har registrerat Otsuka-ID går du vidare till länknings inställningen.

3. Fortsätt till slutet och när den övre skärmen visas när du har loggat in på kundens min sida, är SSO-inställningarna slutförda.

4. Nästa gången du ansluter till kundens min sida från SSO-appen visas den översta skärmen när du har loggat in på kundens sida.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Otsuka Shokai.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Otsuka Shokai**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-otsuka-shokai-test-user"></a>Skapa Otsuka Shokai-test användare

Den nya registreringen av SaaS-kontot utförs vid den första åtkomsten till Otsuka Shokai. Dessutom kopplar vi även Azure AD-konto och SaaS-konto vid tidpunkten för ny generering.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Otsuka Shokai på åtkomst panelen, bör du loggas in automatiskt på Otsuka-Shokai som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
