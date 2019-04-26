---
title: 'Självstudier: Azure Active Directory-integrering med TINFOIL SECURITY | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TINFOIL SECURITY.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 135b5719422d1b28a82ac2eda06f76d6dd746800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60339298"
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Självstudier: Azure Active Directory-integrering med TINFOIL SECURITY

I den här självstudien får du lära dig hur du integrerar TINFOIL SECURITY med Azure Active Directory (AD Azure).
Integrera TINFOIL SECURITY med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TINFOIL SECURITY.
* Du kan aktivera användarna att vara automatiskt inloggad till TINFOIL SECURITY (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med TINFOIL SECURITY, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TINFOIL SECURITY enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för TINFOIL SECURITY **IDP** -initierad SSO

## <a name="adding-tinfoil-security-from-the-gallery"></a>Att lägga till TINFOIL SECURITY från galleriet

För att konfigurera integrering av TINFOIL SECURITY i Azure AD, som du behöver lägga till TINFOIL SECURITY från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till TINFOIL SECURITY från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **TINFOIL SECURITY**väljer **TINFOIL SECURITY** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![TINFOIL SECURITY i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med TINFOIL SECURITY baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TINFOIL SECURITY upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med TINFOIL SECURITY, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera TINFOIL SECURITY enkel inloggning](#configure-tinfoil-security-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa TINFOIL SECURITY testanvändare](#create-tinfoil-security-test-user)**  – du har en motsvarighet för Britta Simon i TINFOIL SECURITY som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med TINFOIL SECURITY:

1. I den [Azure-portalen](https://portal.azure.com/)på den **TINFOIL SECURITY** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![TINFOIL SECURITY domän och URL: er med enkel inloggning för information](common/preintegrated.png)

5. TINFOIL SECURITY programmet förväntar sig SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

        ![image](common/edit-attribute.png)

6. Dessutom ovan TINFOIL SECURITY-program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut |
    | ------------------- | -------------|
    | accountid | UXXXXXXXXXXXXX |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. I den **källattribut** textrutan klistra in konto-ID-värde som du får senare i självstudien.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. I avsnittet **SAML-signeringscertifikat** klickar du på knappen **Redigera** för att öppna dialogrutan **SAML-signeringscertifikat**.

    ![Redigera SAML-signeringscertifikat](common/edit-certificate.png)

8. I avsnittet **SAML-signeringscertifikat** kopierar du **Tumavtryck** och sparar det på datorn.

    ![Kopiera värdet för Tumavtryck](common/copy-thumbprint.png)

9. På den **konfigurera TINFOIL SECURITY** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-tinfoil-security-single-sign-on"></a>Konfigurera TINFOIL SECURITY enkel inloggning

1. Logga in på webbplatsen för företagets TINFOIL SECURITY som en administratör i ett annat webbläsarfönster.

2. I verktygsfältet högst upp, klickar du på **mitt konto**.
   
    ![Instrumentpanel](./media/tinfoil-security-tutorial/ic798971.png "Instrumentpanel")

3. Klicka på **Säkerhet**.
   
    ![Säkerhet](./media/tinfoil-security-tutorial/ic798972.png "Säkerhet")

4. På sidan för konfiguration av **Enkel inloggning** utför du följande steg:
   
    ![Enkel inloggning](./media/tinfoil-security-tutorial/ic798973.png "Enkel inloggning")
   
    a. Välj **Aktivera SAML**.
   
    b. Klicka på **manuell konfiguration**.
   
    c. I **SAML post-URL** textrutan klistra in värdet för **inloggnings-URL** som du har kopierat från Azure-portalen
   
    d. I **SAML-certifikatet fingeravtryck** textrutan klistra in värdet för **tumavtryck** som du har kopierat från **SAML-signeringscertifikat** avsnittet.
  
    e. Kopiera **ditt konto-ID** och klistra in värdet i **attributvärdet** textrutan under **lägga till attributet** avsnitt i Azure-portalen.
   
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
  
    b. I den **användarnamn** fälttyp brittasimon@yourcompanydomain.extension. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TINFOIL SECURITY.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **TINFOIL SECURITY**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **TINFOIL SECURITY**.

    ![TINFOIL SECURITY-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-tinfoil-security-test-user"></a>Skapa TINFOIL SECURITY testanvändare

För att aktivera Azure AD-användare att logga in på TINFOIL SECURITY, måste de etableras i TINFOIL SECURITY. När det gäller TINFOIL SECURITY är etablering en manuell aktivitet.

**Utför följande steg för att få en användare som har etablerats:**

1. Om användaren är en del av ett Enterprise-konto, måste du [kontakta supporten TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) att hämta det användarkonto som har skapats.

1. Om användaren är en vanlig TINFOIL SECURITY SaaS-användare, kan du lägga till medarbetare till någon av användarens platser. Detta utlöser en process för att skicka en inbjudan till den angivna e-posten för att skapa ett nytt användarkonto TINFOIL SECURITY.

> [!NOTE]
> Du kan använda andra verktyg för TINFOIL SECURITY användare konto att skapa eller API: er som tillhandahålls av TINFOIL SECURITY för att etablera användarkonton i Azure AD.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TINFOIL SECURITY i åtkomstpanelen, bör det vara loggas in automatiskt till den TINFOIL SECURITY som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

