---
title: 'Självstudier: Azure Active Directory-integrering med Alibaba Cloud Service (Role-based SSO) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Alibaba Molntjänsten (Role-based SSO).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a79ff869ac57a089664bbd7277ad4ddc9ffea08e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59287380"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Självstudier: Azure Active Directory-integrering med Alibaba Cloud Service (Role-based SSO)

I den här självstudien får du lära dig hur du integrerar Alibaba Cloud Service (Role-based SSO) med Azure Active Directory (AD Azure).
Integrera Alibaba Cloud Service (Role-based SSO) med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Alibaba Cloud Service (Role-based SSO).
* Du kan aktivera användarna att vara automatiskt inloggad till Molntjänsten för Alibaba (Role-based SSO) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Alibaba Cloud Service (Role-based SSO), behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Alibaba Cloud Service (Role-based SSO) enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Alibaba Cloud Service (Role-based SSO) stöder **IDP** -initierad SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Att lägga till Alibaba Cloud Service (Role-based SSO) från galleriet

För att konfigurera integrering av Alibaba Cloud Service (Role-based SSO) i Azure AD, som du behöver lägga till Alibaba Cloud Service (Role-based SSO) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Alibaba Cloud Service (Role-based SSO) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Alibaba Cloud Service (Role-based SSO)** väljer **Alibaba Cloud Service (Role-based SSO)** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Alibaba Molntjänsten (Role-based SSO) i listan med resultat](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Alibaba Cloud Service (Role-based SSO) baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Alibaba Cloud Service (Role-based SSO) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Alibaba Cloud Service (Role-based SSO), måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera Alibaba Cloud Service (Role-based SSO) Single Sign-On](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Alibaba Cloud Service (Role-based SSO) testanvändare](#create-alibaba-cloud-service-role-based-sso-test-user)**  – du har en motsvarighet för Britta Simon i Alibaba molntjänst (Role-based SSO) som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Alibaba Cloud Service (Role-based SSO):

1. I den [Azure-portalen](https://portal.azure.com/)på den **Alibaba Cloud Service (Role-based SSO)** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    ![Alibaba Cloud Service (Role-based SSO)-domän och URL: er med enkel inloggning för information](common/idp-intiated.png)

    a. I den **identifierare** text skriver du någon av URL: en:
    
    | |
    |--|
    | `urn:alibaba:cloudcomputing` |
    | `urn:alibaba:cloudcomputing:international` |

    b. I den **svars-URL** text skriver du någon av URL: en:

    | |
    |--|
    | `https://signin.aliyun.com/saml-role/SSO` |
    | `https://signin.alibabacloud.com/saml-role/SSO` |

5. Alibaba Cloud Service (Role-based SSO) program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen  **Redigera** för att öppna dialogrutan **Användarattribut** .

    ![image](common/edit-attribute.png)

6. Förutom ovanstående Alibaba Cloud Service (Role-based SSO) program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Källattribut|
    | ---------------| --------------- |
    | Roll | user.assignedroles |
    | RoleSessionName | user.mail |

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar en **Roll** i Azure AD

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. På den **konfigurera Alibaba Cloud Service (Role-based SSO)** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Konfigurera enkel inloggning för Alibaba Cloud-tjänsten (Role-based SSO)

Att konfigurera enkel inloggning på **Alibaba Cloud Service (Role-based SSO)** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopieras URL: er från Azure portal för att [ Alibaba Cloud Service (Role-based SSO) supportteam](https://www.aliyun.com/service/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Alibaba Cloud Service (Role-based SSO).

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Alibaba Cloud Service (Role-based SSO)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Alibaba Cloud Service (Role-based SSO)**.

    ![Länken Alibaba Cloud Service (Role-based SSO) i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Skapa testanvändare Alibaba Cloud Service (Role-based SSO)

I det här avsnittet skapar du en användare som kallas Britta Simon i Alibaba molntjänst (Role-based SSO). Arbeta med [Alibaba Cloud Service (Role-based SSO) supportteam](https://www.aliyun.com/service/) att lägga till användare i Alibaba Cloud Service (Role-based SSO)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Alibaba Cloud Service (Role-based SSO) i åtkomstpanelen, bör det vara loggas in automatiskt till Alibaba-Molntjänsten (Role-based SSO) som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

