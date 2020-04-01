---
title: 'Självstudiekurs: Azure Active Directory-integrering med Five9 Plus-kort (CTI, Kontaktcenteragenter) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Five9 Plus-kort (CTI, Contact Center Agents).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: dd4eab2d8e877cac468097c0a6ed6aa3cfb750b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102458"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Självstudiekurs: Azure Active Directory-integrering med Five9 Plus-kort (CTI, Kontaktcenteragenter)

I den här självstudien får du lära dig hur du integrerar Five9 Plus-kort (CTI, Kontaktcenteragenter) med Azure Active Directory (Azure AD).
Genom att integrera Five9 Plus-adapter (CTI, Kontaktcenteragenter) med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Five9 Plus-adapter (CTI, Contact Center Agents).
* Du kan aktivera dina användare så att de automatiskt loggas in på Five9 Plus-adapter (CTI, Kontaktcenteragenter) (Enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Five9 Plus-kort (CTI, Contact Center Agents) behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Five9 Plus-adapter (CTI, Kontaktcenteragenter) enkel inloggning aktiverad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Five9 Plus Adapter (CTI, Contact Center Agents) stöder **IDP** initierad SSO

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Lägga till Five9 Plus-adapter (CTI, Kontaktcenteragenter) från galleriet

Om du vill konfigurera integreringen av Five9 Plus-adaptern (CTI, Kontaktcenteragenter) i Azure AD måste du lägga till Five9 Plus-adapter (CTI, Kontaktcenteragenter) från galleriet i listan över hanterade SaaS-appar.

**Så här lägger du till Five9 Plus-adapter (CTI, Kontaktcenteragenter) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Five9 Plus-adapter (CTI, Kontaktcenteragenter)** i sökrutan och välj **Five9 Plus-adapter (CTI, Kontaktcenteragenter)** från resultatpanelen och klicka sedan på **Lägg** till knappen för att lägga till programmet.

     ![Five9 Plus-adapter (CTI, Kontaktcenteragenter) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Five9 Plus-adapter (CTI, Contact Center Agents) baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Five9 Plus-kortet (CTI, Contact Center Agents) upprättas.

Om du vill konfigurera och testa azure AD-enkel inloggning med Five9 Plus-adapter (CTI, Contact Center Agents) måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Five9 Plus-adapter (CTI, Contact Center Agents) Enkel inloggning](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Five9 Plus Adapter (CTI, Contact Center Agents) testanvändare](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** - att ha en motsvarighet till Britta Simon i Five9 Plus Adapter (CTI, Contact Center Agents) som är kopplad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Så här konfigurerar du en enkel Azure AD-inloggning med Five9 Plus-adapter (CTI, Kontaktcenteragenter) och utför följande:

1. På programintegreringen **för Five9 Plus-kort (CTI, Kontaktcenteragenter)** i [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Five9 Plus-adapter (CTI, Kontaktcenteragenter) Domän och WEBBADRESSER enkel inloggningsinformation](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    
    |    Miljö      |       URL      |
    | :-- | :-- |
    | För "Five9 Plus-adapter för Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | För "Five9 Plus Adapter för Zendesk" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | För "Five9 Plus Adapter för Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    |      Miljö     |      URL      |
    | :--                  | :--           |
    | För "Five9 Plus-adapter för Microsoft Dynamics CRM" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | För "Five9 Plus Adapter för Zendesk" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | För "Five9 Plus Adapter för Agent Desktop Toolkit" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. Kopiera lämpliga webbadresser enligt dina behov i avsnittet **Konfigurera Five9 Plus-adapter (CTI, Kontaktcenteragenter)** enligt dina krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Konfigurera Five9 Plus-adapter (CTI, Kontaktcenteragenter) Enkel inloggning

1. Om du vill konfigurera en enda inloggning på **Five9 Plus-adaptersidan (CTI, Contact Center Agents)** måste du skicka det nedladdade **supportteamet för certifikat(Base64)** och lämpliga kopierade webbadresser till [Five9 Plus-adapter (CTI, Kontaktcenteragenter).](https://www.five9.com/about/contact) Dessutom, för att konfigurera SSO ytterligare följ nedanstående steg enligt adaptern:

    a. "Five9 Plus adapter för Agent Desktop Toolkit" Admin Guide:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Administratörsguide för Five9 Plus-adapter för Microsoft Dynamics CRM:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 Plus Adapter för Zendesk" Admin Guide:[https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera Britta Simon för att använda Azure enkel inloggning genom att bevilja åtkomst till Five9 Plus Adapter (CTI, Contact Center Agents).

1. I Azure-portalen väljer du **Företagsprogram**, väljer **Alla program**och väljer sedan **Five9 Plus-adapter (CTI, Kontaktcenteragenter)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Five9 Plus-adapter (CTI, Kontaktcenteragenter)** i programlistan .

    ![Länken Five9 Plus-adapter (CTI, Kontaktcenteragenter) i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Skapa five9 Plus-adapter (CTI, Contact Center Agents) testanvändare

I det här avsnittet skapar du en användare som heter Britta Simon i Five9 Plus Adapter (CTI, Contact Center Agents). Arbeta med [Five9 Plus Adapter (CTI, Contact Center Agents) supportteam](https://www.five9.com/about/contact) för att lägga till användarna i Five9 Plus Adapter (CTI, Contact Center Agents) plattform. Användare måste skapas och aktiveras innan du använder enkel inloggning. 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Five9 Plus-adapter (CTI, Kontaktcenteragenter på åtkomstpanelen bör du automatiskt loggas in på de Five9 Plus-kort (CTI, Kontaktcenteragenter) som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

