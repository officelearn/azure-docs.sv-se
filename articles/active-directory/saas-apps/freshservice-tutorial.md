---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshservice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227522"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshservice

I den här självstudien får du lära dig hur du integrerar Freshservice med Azure Active Directory (Azure AD). När du integrerar Freshservice med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freshservice.
* Gör det möjligt för användarna att logga in automatiskt till Freshservice med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Freshservice för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Freshservice stöder **IDP**-initierad enkel inloggning

## <a name="adding-freshservice-from-the-gallery"></a>Lägga till Freshservice från galleriet

För att konfigurera integreringen av Freshservice till Azure AD behöver du lägga till Freshservice från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Freshservice** i sökrutan.
1. Välj **Freshservice** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Konfigurera och testa enkel inloggning med Azure AD för Freshservice

Konfigurera och testa Azure AD SSO med Freshservice med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Freshservice.

Om du vill konfigurera och testa Azure AD SSO med Freshservice, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FRESHSERVICE SSO](#configure-freshservice-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Freshservice test User](#create-freshservice-test-user)** -om du vill ha en motsvarighet till B. Simon i Freshservice som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Freshservice** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<democompany>.freshservice.com`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster: `https://<democompany>.freshservice.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Hämta dessa värden genom att kontakta [supportteamet för Freshservice-klienten](https://support.freshservice.com/). Du kan även se de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Freshservice kräver SHA-256-finger avtryck för att få SSO-arbete. Utför följande steg för att få SHA-256-finger avtryck:

    ![Läsa](./media/freshservice-tutorial/ic790821.png "Läsa")

    1. Öppna [länken](https://www.samltool.com/fingerprint.php) i en annan webbläsare.

    1. Öppna filen med hämtade certifikat (base64) i anteckningar och klistra in innehåll i text rutan **X. 509-certifikat** .

    1. För algoritmen väljer du **SHA256** i list rutan.

    1. Klicka på **Beräkna finger avtryck**.

    1. Klicka på kopierings ikonen för att kopiera det genererade **finger avtrycket** och spara det på datorn.

1. I avsnittet **Konfigurera Freshservice** på **Azure Portal**kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Freshservice.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **Freshservice**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-freshservice-sso"></a>Konfigurera Freshservice SSO

1. Om du vill automatisera konfigurationen i Freshservice måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations Freshservice** för att dirigera dig till Freshservice-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Freshservice. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Freshservice manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Freshservice-företags webbplats som administratör och utför följande steg:

4. På menyn längst upp klickar du på **Admin**.

    ![Innehavaradministration](./media/freshservice-tutorial/ic790814.png "Admin")

5. I **kundportalen** klickar du på **Säkerhet**.

    ![Säkerhet](./media/freshservice-tutorial/ic790815.png "Säkerhet")

6. I avsnittet **Säkerhet** utför du följande steg:

    ![Enkel inloggning](./media/freshservice-tutorial/ic790816.png "Enkel inloggning")

    a. Växla **Enkel inloggning**.

    b. Välj **SAML SSO**.

    c. I textrutan för **inloggnings-URL för SAML** klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan för **utloggnings-URL:en** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. I text rutan **finger avtryck för säkerhetscertifikat** klistrar du in **finger avtrycks** värdet som du har genererat tidigare.

    f. Klicka på **Spara**

### <a name="create-freshservice-test-user"></a>Skapa Freshservice-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på FreshService måste de tillhandahållas i FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **FreshService** -företags webbplats som administratör.

2. På menyn längst upp klickar du på **Admin**.

    ![Innehavaradministration](./media/freshservice-tutorial/ic790814.png "Admin")

3. I avsnittet **Användarhantering** klickar du på **Beställare**.

    ![Frågare](./media/freshservice-tutorial/ic790818.png "Frågare")

4. Klicka på **Ny beställare**.

    ![Nya beställare](./media/freshservice-tutorial/ic790819.png "Nya beställare")

5. I avsnittet **Ny beställare** utför du följande steg:

    ![Ny beställare](./media/freshservice-tutorial/ic790820.png "Ny beställare")  

    a. Ange attributen **Förnamn** och **E-post** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

    b. Klicka på **Save** (Spara).

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt
    >  

> [!NOTE]
> Du kan använda andra verktyg för FreshService av användar konton eller API: er som tillhandahålls av FreshService för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Freshservice-panelen i åtkomstpanelen bör du automatiskt loggas in på Freshservice som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Freshservice med Azure AD](https://aad.portal.azure.com/)