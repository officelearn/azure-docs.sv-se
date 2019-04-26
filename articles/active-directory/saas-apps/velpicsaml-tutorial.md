---
title: 'Självstudier: Azure Active Directory-integrering med Velpic SAML | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Velpic SAML.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc8431e2c45cc3bfdfa08dd0078220ab2d290309
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316092"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Självstudier: Azure Active Directory-integrering med Velpic SAML

I den här självstudien får du lära dig hur du integrerar Velpic SAML med Azure Active Directory (AD Azure).
Integrera Velpic SAML med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Velpic SAML.
* Du kan aktivera användarna att vara automatiskt inloggad till Velpic SAML (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Velpic SAML, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Velpic SAML enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för Velpic SAML **SP** -initierad SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Att lägga till Velpic SAML från galleriet

För att konfigurera integrering av Velpic SAML i Azure AD, som du behöver lägga till Velpic SAML från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Velpic SAML från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Velpic SAML**väljer **Velpic SAML** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Velpic SAML i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Velpic SAML baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Velpic SAML upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Velpic SAML, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Velpic SAML enkel inloggning](#configure-velpic-saml-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Velpic SAML testanvändare](#create-velpic-saml-test-user)**  – du har en motsvarighet för Britta Simon i Velpic SAML som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Velpic SAML:

1. I den [Azure-portalen](https://portal.azure.com/)på den **Velpic SAML** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Velpic SAML domän och URL: er med enkel inloggning för information](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<sub-domain>.velpicsaml.net`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Observera att URL: en inloggning kommer att tillhandahållas av Velpic SAML-teamet och ID-värde ska vara tillgängliga när du konfigurerar plugin-programmet för SSO längs Velpic SAML. Du måste kopiera detta värde från Velpic SAML programsidan och klistra in den här.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. På den **konfigurera Velpic SAML** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-velpic-saml-single-sign-on"></a>Konfigurera Velpic SAML enkel inloggning

1. Logga in på webbplatsen Velpic SAML-företag som en administratör i ett annat webbläsarfönster.

2. Klicka på **hantera** fliken och gå till **integrering** avsnitt där du måste klicka på **plugin-program** för att skapa nya plugin-programmet för att logga In.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_1.png)

3. Klicka på den **”Lägg till plugin-program”** knappen.
    
    ![Plugin-program](./media/velpicsaml-tutorial/velpic_2.png)

4. Klicka på den **SAML** panelen på sidan Lägg till plugin-programmet.
    
    ![Plugin-program](./media/velpicsaml-tutorial/velpic_3.png)

5. Ange namnet på den nya SAML-plugin-programmet och klicka på den **”Lägg till”** knappen.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_4.png)

6. Ange information på följande sätt:

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_5.png)

    a. I den **namn** textrutan skriver du namnet på SAML-plugin-programmet.

    b. I den **utfärdar-URL** textrutan klistra in den **Azure AD-identifierare** du kopierade från den **konfigurera inloggning** fönstret i Azure-portalen.

    c. I den **providern Metadata Config** överför Metadata-XML-filen som du har hämtat från Azure-portalen.

    d. Du kan också välja att aktivera SAML just-in-time-etablering genom att aktivera den **”automatiskt skapa nya användare”** kryssrutan. Om en användare finns inte i Velpic och den här flaggan är inte aktiverad, misslyckas inloggningen från Azure. Om flaggan är aktiverad kommer automatiskt att tillhandahållas till Velpic användaren vid tidpunkten för inloggningen. 

    e. Kopiera den **enkel inloggning på URL: en** från texten rutan och klistra in den i Azure-portalen.
    
    f. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Velpic SAML.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Velpic SAML**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Velpic SAML**.

    ![Velpic SAML-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-velpic-saml-test-user"></a>Skapa Velpic SAML testanvändare

Det här steget krävs vanligtvis inte eftersom programmet stöder just-in-time användaretablering. Om automatisk användaretablering inte är aktiverat kan sedan manuell användarskapelse göras enligt beskrivningen nedan.

Logga in på webbplatsen Velpic SAML företagets som administratör och utför följande steg:
    
1. Klicka på Hantera fliken och gå till avsnittet för användare, och klicka sedan på nytt för att lägga till användare.

    ![Lägg till användare](./media/velpicsaml-tutorial/velpic_7.png)

2. På den **”skapa nya användare”** dialogrutan utför följande steg.

    ![Användare](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. I den **Förnamn** textrutan Ange först namnet på Britta.

    b. I den **efternamn** textrutan skriver Simon efternamn.

    c. I den **användarnamn** textrutan skriver du användarnamnet för Britta Simon.

    d. I den **e-post** textrutan skriver du e-postadress Brittasimon@contoso.com konto.

    e. Resten av informationen är valfri, så kan du fylla det om det behövs.
    
    f. Klicka på **SPARA**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

1. När du klickar på panelen Velpic SAML i åtkomstpanelen bör du få inloggningssidan i Velpic SAML-programmet. Du bör se den **”logga in med Azure AD-** knappen på inloggningssidan.

    ![Plugin-program](./media/velpicsaml-tutorial/velpic_6.png)

1. Klicka på den **”logga in med Azure AD-** knappen för att logga in på Velpic med hjälp av Azure AD-konto.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

