---
title: 'Självstudier: Azure Active Directory-integrering med ZIVVER | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ZIVVER.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 64cb7ea0-df6c-4963-84d8-6f435980e2de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: 2e2ec9a007d4b729120ec9ecdb25e7c782fd13ea
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114742"
---
# <a name="tutorial-azure-active-directory-integration-with-zivver"></a>Självstudier: Azure Active Directory-integrering med ZIVVER

I den här självstudien får du lära dig hur du integrerar ZIVVER med Azure Active Directory (AD Azure).
Integrera ZIVVER med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till ZIVVER.
* Du kan aktivera användarna att vara automatiskt inloggad till ZIVVER (Single Sign-On) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med ZIVVER, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* ZIVVER enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Har stöd för ZIVVER **IDP** -initierad SSO

## <a name="adding-zivver-from-the-gallery"></a>Att lägga till ZIVVER från galleriet

För att konfigurera integrering av ZIVVER i Azure AD, som du behöver lägga till ZIVVER från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till ZIVVER från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **ZIVVER**väljer **ZIVVER** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![ZIVVER i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med ZIVVER baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i ZIVVER upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med ZIVVER, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ZIVVER Single Sign-On](#configure-zivver-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare ZIVVER](#create-zivver-test-user)**  – du har en motsvarighet för Britta Simon i ZIVVER som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med ZIVVER:

1. I den [Azure-portalen](https://portal.azure.com/)på den **ZIVVER** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![ZIVVER domän och URL: er med enkel inloggning för information](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du en URL: `https://app.zivver.com/SAML/Zivver`

5. ZIVVER program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. Följande skärmbild visar en lista över standardattribut, var som **nameidentifier** mappas med **user.userprincipalname**. ZIVVER program som förväntar **nameidentifier** mappas med **user.mail**, så måste du redigera attribut mappar genom att klicka på **redigera**  ikon och ändra attributet mappningen.

    ![image](common/edit-attribute.png)

6. Förutom ovanstående ZIVVER program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Namnområde | Källattribut|
    | ---------------| --------------- |
    | ZivverAccountKey | https://zivver.com/SAML/Attributes | user.objectid |

    >[!NOTE]
    >Om du använder en hybrid-konfiguration med Active Directory på plats och Azure AD Connect-verktyget ska värdet anges `user.objectGUID`

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

7. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **Federation Metadata-XML**  och klicka på **kopia** ikon för att kopiera den **Federationsmetadata för appen url** från de angivna alternativen enligt dina behov och spara den på din dator.

    ![Certifikatets Webbadress nedladdningslänk](./media/zivver-tutorial/metadataxmlurl.png)

8. På den **konfigurera ZIVVER** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-zivver-single-sign-on"></a>Konfigurera ZIVVER enkel inloggning

1. I ett annat webbläsarfönster, loggar du in företaget ZIVVER [plats](https://app.zivver.com/login) som administratör.

2. Klicka på den **organisationsinställningar** ikonen längst till vänster i webbläsarfönstret.

3. Gå till **enkel inloggning**.

4. Öppna Federation Metadata-XML-filen som du laddade ned från Azure-portalen.

5. I den **metadata-URL för identitetsprovider** textrutan, klistra in den **Appfederationsmetadata** du har sparat tidigare från Azure-portalen.

6. Markera kryssrutan **aktivera SSO**.

7. Klicka på **SPARA**.

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

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till ZIVVER.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **ZIVVER**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **ZIVVER**.

    ![Länken ZIVVER i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zivver-test-user"></a>Skapa ZIVVER testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i ZIVVER. Arbeta med [ZIVVER supportteam](https://support.zivver.com/) att lägga till användare i ZIVVER-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen ZIVVER i åtkomstpanelen, bör det vara loggas in automatiskt till ZIVVER som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

