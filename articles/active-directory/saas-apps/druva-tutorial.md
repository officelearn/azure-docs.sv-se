---
title: 'Självstudier: Azure Active Directory-integrering med Druva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f034a206ca114b484bd29c72d8e53f9ae5aa498
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67103695"
---
# <a name="tutorial-integrate-druva-with-azure-active-directory"></a>Självstudier: Integrera Druva med Azure Active Directory

I de här självstudierna lär du dig att integrera Druva med Azure Active Directory (AD Azure). När du integrerar Druva med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Druva.
* Ge dina användare att automatiskt inloggad till Druva med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en månads kostnadsfri utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktiverat prenumeration Druva enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Druva har stöd för **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-druva-from-the-gallery"></a>Lägga till Druva från galleriet

För att konfigurera integrering av Druva i Azure AD behöver du lägga till Druva från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Druva** i sökrutan.
1. Välj **Druva** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD enkel inloggning med Druva med en testanvändare kallas **Britta Simon**. Du måste upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Druva för SSO ska fungera.

Om du vill konfigurera och testa Azure AD enkel inloggning med Druva, utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Druva SSO](#configure-druva-sso)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa Druva-testanvändare](#create-druva-test-user)** – för att ha en motsvarighet för Britta Simon i Druva som är länkad till Azure AD-representationen av användaren.
6. **[Testa SSO](#test-sso)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Druva** programsidan integration, hitta den **hantera** och väljer **enkel inloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På den **SAML grundkonfiguration** om du vill konfigurera programmet i **IDP** initierad läge, användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I rutan **Inloggnings-URL** anger du en URL: `https://login.druva.com/login`

1. Druva-programmet förväntar sig SAML-försäkran i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn | Källattribut|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Ange det tokengenererade värdet |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **certifikat (Base64)** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/certificatebase64.png)

1. På den **konfigurera Druva** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-druva-sso"></a>Configure Druva SSO

1. I ett annat webbläsarfönster, loggar du in din Druva företagets webbplats som administratör.

1. Gå till **Hantera \> Inställningar**.

    ![Inställningar](./media/druva-tutorial/ic795091.png "Inställningar")

1. I dialogrutan Inställningar för enkel inloggning utför du följande steg:

    ![Inställningar för enkel inloggning](./media/druva-tutorial/ic795092.png "Inställningar för enkel inloggning")

    a. I textrutan **ID Provider Login URL** (Inloggnings-URL för ID-provider) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. I textrutan **ID Provider Logout URL** (Utloggnings-URL för ID-provider) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen

    c. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **ID Provider Certificate** (Certifikat för ID-provider)

    d. Öppna sidan **Inställningar** genom att klicka på **Spara**.

1. På sidan **Inställningar** klickar du på **Generate SSO Token** (Generera token för enkel inloggning).

    ![Inställningar](./media/druva-tutorial/ic795093.png "Inställningar")

1. I dialogrutan **Single Sign-on Authentication Token** (Token för autentisering med enkel inloggning) utför du följande steg:

    ![Token för enkel inloggning](./media/druva-tutorial/ic795094.png "Token för enkel inloggning")

    a. Klicka på **Kopia**, klistra in det kopierade värdet i textrutan **Värde** i avsnittet **Lägg till attribut** i Azure-portalen.

    b. Klicka på **Stäng**.

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

I det här avsnittet ska du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Druva.

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I programlistan väljer du **Druva**.
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **Britta Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-druva-test-user"></a>Skapa Druva-testanvändare

För att aktivera Azure AD-användare att logga in på Druva, måste de etableras i Druva. När det gäller Druva är etablering en manuell uppgift.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Druva** företagets plats som administratör.

1. Gå till **Hantera \> Användare**.

    ![Hantera användare](./media/druva-tutorial/ic795097.png "Hantera användare")

1. Klicka på **Skapa ny**.

    ![Hantera användare](./media/druva-tutorial/ic795098.png "Hantera användare")

1. I dialogrutan Skapa ny användare utför du följande steg:

    ![Skapa ny användare](./media/druva-tutorial/ic795099.png "Skapa ny användare")

    a. I den **e-postadress** textrutan Ange e-postadress för användaren som **brittasimon\@contoso.com**.

    b. I textrutan **Namn** anger du namnet på användaren: **BrittaSimon**.

    c. Klicka på **Skapa användare**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för Druva-kontoskapande som tillhandahålls av Druva för att etablera Azure AD-användarkonton.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Druva i åtkomstpanelen, bör det vara loggas in automatiskt till Druva som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
