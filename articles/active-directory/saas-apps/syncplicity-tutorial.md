---
title: 'Självstudier: Azure Active Directory-integrering med Syncplicity | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: e6a8a25e88d4193562c818f30efd5eb017c372fd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67089298"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Självstudier: Integrera Syncplicity med Azure Active Directory

I de här självstudierna lär du dig att integrera Syncplicity med Azure Active Directory (AD Azure). När du integrerar Syncplicity med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Syncplicity.
* Ge dina användare att automatiskt inloggad till Syncplicity med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Syncplicity enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Syncplicity **SP** -initierad SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Att lägga till Syncplicity från galleriet

För att konfigurera integrering av Syncplicity i Azure AD, som du behöver lägga till Syncplicity från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Syncplicity** i sökrutan.
1. Välj **Syncplicity** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurera och testa Azure AD SSO

Konfigurera och testa Azure AD enkel inloggning med Syncplicity med en testanvändare kallas **B.Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Syncplicity för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Syncplicity, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Syncplicity SSO](#configure-syncplicity-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare Syncplicity](#create-syncplicity-test-user)**  – du har en motsvarighet för B.Simon i Syncplicity som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Syncplicity** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** ange värdena för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Syncplicity klienten supportteamet](https://www.syncplicity.com/contact-us) att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Syncplicity** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurera Syncplicity SSO

1. Logga in på din **Syncplicity** klient.

1. Klicka på menyn längst upp **admin**väljer **inställningar**, och klicka sedan på **anpassad domän och enkel inloggning**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. På den **enkel inloggning (SSO)** dialogrutan utför följande steg:

    ![Enkel inloggning \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. I den **Custom Domain** textrutan skriver du namnet på din domän.
  
    b. Välj **aktiverat** som **enkel inloggningsstatus**.

    c. I den **entitets-Id** textrutan klistra in den **identifierare (entitets-ID)** värde, som du har använt i den **SAML grundkonfiguration** i Azure-portalen.

    d. I den **inloggning sid-URL** textrutan klistra in den **inloggnings-URL** som du har kopierat från Azure-portalen.

    e. I den **sid-URL för utloggning** textrutan klistra in den **URL för utloggning** som du har kopierat från Azure-portalen.

    f. I **providern identitetscertifikat**, klickar du på **Välj fil**, och sedan ladda upp det certifikat som du har hämtat från Azure-portalen.

    g. Klicka på **SPARA ÄNDRINGAR** för att spara ändringarna.

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

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Syncplicity.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Syncplicity**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-syncplicity-test-user"></a>Skapa Syncplicity testanvändare

För AAD-användare för att kunna logga in, måste de etableras till Syncplicity program. Det här avsnittet beskrivs hur du skapar AAD-användarkonton i Syncplicity.

**Om du vill konfigurera ett användarkonto till Syncplicity, utför du följande steg:**

1. Logga in på din **Syncplicity** klient (till exempel: `https://company.Syncplicity.com`).

1. Klicka på **admin** och välj **användarkonton** och klicka sedan på **ADD A USER**.

    ![Hantera användare](./media/syncplicity-tutorial/ic769764.png "Hantera användare")

1. Typen i **e-postadresser** ett Azure AD-konto som du vill etablera, väljer du **användaren** som **rollen**, och klicka sedan på **nästa**.

    ![Kontoinformation](./media/syncplicity-tutorial/ic769765.png "kontoinformation")

    > [!NOTE]
    > Kontoinnehavare för AAD får ett e-postmeddelande, inklusive en länk för att bekräfta och aktivera kontot.

1. Välj en grupp i ditt företag som de nya användarna ska bli medlem i och klicka sedan på **nästa**.

    ![Gruppmedlemskap](./media/syncplicity-tutorial/ic769772.png "gruppmedlemskap")

    > [!NOTE]
    > Om det finns inga grupper som visas, klickar du på **nästa**.

1. Markera de mappar som du vill placera under Syncplicitys kontroll på användarens dator och klicka sedan på **nästa**.

    ![Syncplicity mappar](./media/syncplicity-tutorial/ic769773.png "Syncplicity mappar")

> [!NOTE]
> Du kan använda alla andra Syncplicity användare konto verktyg för att skapa eller API: er som tillhandahålls av Syncplicity att etablera AAD-användarkonton.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Syncplicity i åtkomstpanelen, bör det vara loggas in automatiskt till Syncplicity som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)