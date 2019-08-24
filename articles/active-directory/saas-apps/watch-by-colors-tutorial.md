---
title: 'Självstudier: Azure Active Directory enkel inloggning (SSO) med titta på färger | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och titta på färger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 586a029c-fb8d-4233-b280-103b9ba7102d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0882be0419cbbbc7d94cb8d517e27bdb06a780
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014141"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-watch-by-colors"></a>Självstudier: Azure Active Directory enkel inloggning (SSO) med titta på färger

I den här självstudien får du lära dig att integrera bevakade färger med Azure Active Directory (Azure AD). När du integrerar bevakade färger med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till titta på färger.
* Gör det möjligt för användarna att logga in automatiskt för att titta på färger med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Titta på en aktive rad prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Titta efter färger stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-watch-by-colors-from-the-gallery"></a>Lägga till titta efter färger från galleriet

Du måste lägga till titta på färger från galleriet till listan över hanterade SaaS-appar för att kunna konfigurera integreringen av titta efter färger i Azure AD.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **titta efter färger** i sökrutan.
1. Välj **titta efter färger** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-watch-by-colors"></a>Konfigurera och testa enkel inloggning med Azure AD för att se färger

Konfigurera och testa Azure AD SSO med titta på färger med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i titta efter färger.

Om du vill konfigurera och testa Azure AD SSO med titta på färger, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD](#assign-the-azure-ad-test-user)** -testuser-för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. Konfigurera de enskilda inloggnings inställningarna på program Sidan **[genom att konfigurera titta efter färger SSO](#configure-watch-by-colors-sso)** .
    1. **[Skapa titta efter färger testa användare](#create-watch-by-colors-test-user)** – för att få en motsvarighet till B. Simon i titta efter färger som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **titta efter färger** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat i **IDP** initierat läge och de nödvändiga URL: erna redan är i förväg ifyllda med Azure. Användaren måste spara konfigurationen genom att klicka på knappen **Spara** .

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: `https://app.colorscorporation.com/login`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , klickar du på Kopiera för att kopiera **URL: en** för appens Federations-metadata och spara den på din dator.

    ![Länk för hämtning av certifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att ge åtkomst till titta på färger.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **titta efter färger**i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-watch-by-colors-sso"></a>Konfigurera bevakning av färger SSO

1. Öppna ett nytt webbläsarfönster och logga in på din bevakning efter färger företags plats som administratör och utför följande steg:

1. Klicka på **profil** > **konto inställningar** > **SSO (enkel inloggning)** i det övre högra hörnet på sidan.

    ![Konfiguration av bevaka färger](./media/watch-by-colors-tutorial/config01.png)

1. Utför följande steg på sidan **SSO (enkel inloggning)** :

    ![Konfiguration av bevaka färger](./media/watch-by-colors-tutorial/config02.png)

    a. Växla **Aktivera SAML** till **på**.

    b. I text rutan **URL** klistrar du in **URL**: en för federationsmetadata som du kopierade från Azure Portal.

    c. Klicka på **Importera**och fyll sedan i följande fält automatiskt på sidan.

    d. Klicka på **Spara**.

### <a name="create-watch-by-colors-test-user"></a>Testa användaren genom att skapa titta efter färger

Om du vill att Azure AD-användare ska kunna logga in för att titta på färger måste de vara etablerade i titta efter färger. I titta efter färger är etableringen en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in för att titta på färger som en säkerhets administratör.

1. Klicka på **profil** > **användare** > **Lägg till användare**i det övre högra hörnet på sidan.

    ![Konfiguration av bevaka färger](./media/watch-by-colors-tutorial/config03.png)

1. Utför följande steg på sidan **användar information** :

    ![Konfiguration av bevaka färger](./media/watch-by-colors-tutorial/config04.png)

    a. I text rutan Förnamn anger du det första namnet på användaren, t. ex. **B**.

    b. I textrutan **Efternamn** anger du efternamnet på användaren som **Simon**.

    c. I text rutan **e-postadress** anger du e-postadressen till användaren `B.Simon@contoso.com`.

    d. I text rutan **lösen ord** anger du lösen ordet.

    e. Välj **konto behörigheter** per organisation.

    f. Klicka på **Spara**.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen titta efter färger i åtkomst panelen, bör du loggas in automatiskt på de färger som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova slack med Azure AD](https://aad.portal.azure.com/)

