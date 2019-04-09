---
title: 'Självstudier: Azure Active Directory-integrering med Springer länk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Springer länk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58cdf029-bdc0-43c4-a469-b921c2a669bd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/13/2019
ms.author: jeedes
ms.openlocfilehash: 00154ac3c94a1f43872aff3be29ddd108a4173f0
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59268849"
---
# <a name="tutorial-azure-active-directory-integration-with-springer-link"></a>Självstudier: Azure Active Directory-integrering med Springer länk

Lär dig hur du integrerar Springer länken med Azure Active Directory (AD Azure) i den här självstudien.
Integrera Springer länken med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Springer länk.
* Du kan aktivera användarna att vara automatiskt inloggad Springer länk (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Springer länk, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Enkel inloggning för springer-länk aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Stöd för springer **SP** och **IDP** -initierad SSO

## <a name="adding-springer-link-from-the-gallery"></a>Att lägga till Springer länk från galleriet

Om du vill konfigurera integreringen av Springer länken till Azure AD, som du behöver lägga till Springer länk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Springer länk från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Springer länken**väljer **Springer länken** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![Springer länk i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Springer länk baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Springer länk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Springer länk, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Springer-länk](#configure-springer-link-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Springer länk testanvändare](#create-springer-link-test-user)**  – du har en motsvarighet för Britta Simon i Springer länk som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Springer länk:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Springer länken** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration**, om du vill konfigurera appen i **IDP**-initierat läge, gör du följande:

    ![Springer länk domän och URL: er med enkel inloggning för information](common/idp-relay.png)

    a. I textrutan **Identifierare** skriver du en URL: `https://fsso.springer.com`

    b. I textrutan **Svars-URL** skriver du en URL: `https://fsso.springer.com/federation/Consumer/metaAlias/SpringerServiceProvider`

    c. Klicka på **Ange ytterligare URL:er**.

    d. I den **Vidarebefordransstatus** text skriver en URL: `https://link.springer.com`

5. Om du vill konfigurera programmet i **SP** initierade läge, utföra följande steg:

    ![Springer länk domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster: `https://fsso.springer.com/saml/login?idp=<entityID>&targetUrl=https://link.springer.com`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. `<entityID>` kopieras det Azure AD-ID: T från den **konfigurera Springer länken** avsnittet beskrivs senare i självstudien. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på ikonen för Kopiera för att kopiera **Appfederationsmetadata** och spara den på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

7. På den **konfigurera Springer länken** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-springer-link-single-sign-on"></a>Konfigurera enkel inloggning för Springer-länk

Att konfigurera enkel inloggning på **Springer länken** sida, som du behöver skicka den kopierade **Appfederationsmetadata** och lämpliga kopierade URL: er från Azure portal för att [Springer länken support-teamet ](mailto:identity@springernature.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Springer länk.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Springer länk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Springer länk**.

    ![Länken Springer länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-springer-link-test-user"></a>Skapa Springer länk testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i Springer länk. Arbeta med [Springer länk supportteamet](mailto:identity@springernature.com) att lägga till användare i Springer länk-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Springer länken i åtkomstpanelen, bör det vara loggas in automatiskt på Springer-länk som du konfigurerar enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

