---
title: 'Självstudie: Azure Active Directory integrering med HeyBuddy | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HeyBuddy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d51b5af6-018e-4678-9a3f-b70438394f67
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7edfc0a90a8477bdee50b871e00cd988e511b401
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159056"
---
# <a name="tutorial-azure-active-directory-integration-with-heybuddy"></a>Självstudie: Azure Active Directory integrering med HeyBuddy

I den här självstudien får du lära dig hur du integrerar HeyBuddy med Azure Active Directory (Azure AD).
Genom att integrera HeyBuddy med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till HeyBuddy.
* Du kan göra det möjligt för användarna att logga in automatiskt till HeyBuddy (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med HeyBuddy behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* HeyBuddy-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* HeyBuddy stöder **SP** -INITIERAd SSO
* HeyBuddy stöder **just-in-Time** User-etablering

## <a name="adding-heybuddy-from-the-gallery"></a>Lägga till HeyBuddy från galleriet

Om du vill konfigurera integreringen av HeyBuddy i Azure AD måste du lägga till HeyBuddy från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till HeyBuddy från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** , i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **HeyBuddy**, väljer **HeyBuddy** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![HeyBuddy i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med HeyBuddy baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i HeyBuddy upprättas.

Om du vill konfigurera och testa enkel inloggning med HeyBuddy i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera HeyBuddy-enkel inloggning](#configure-heybuddy-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa en	 med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa HeyBuddy test User](#create-heybuddy-test-user)** – om du vill ha en motsvarighet till Britta Simon i HeyBuddy som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med HeyBuddy i Azure AD:

1. Välj **enkel inloggning**på sidan **HeyBuddy** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning-länk](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för HeyBuddy-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://api.heybuddy.com/auth/<ENTITY ID>`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `YourCompanyInstanceofHeyBuddy`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och identifieraren (enhets-ID). `Entity ID` i inloggnings-URL: en genereras automatiskt för varje organisation. Kontakta [HeyBuddy client support team](mailto:support@heybuddy.com) för att hämta dessa värden.

5. Ditt HeyBuddy-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![mallar](common/edit-attribute.png)

    > [!NOTE]
    > Se den här [länken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) om hur du konfigurerar och konfigurerar roller för programmet.

6. Utöver ovan förväntar sig HeyBuddy-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn |  Källattribut|
    | -------- | --------- |
    | Roller  | user.assignedroles |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![mallar](common/new-save-attribute.png)

    ![mallar](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Save** (Spara).

7. Klicka på kopieringsknappen i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** om du vill kopiera **URL:en för federationsmetadata** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-heybuddy-single-sign-on"></a>Konfigurera HeyBuddy enkel inloggning

Om du vill konfigurera enkel inloggning på **HeyBuddy** sida måste du skicka **URL: en för appens Federations-metadata** till [support teamet för HeyBuddy](mailto:support@heybuddy.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon\@yourcompanydomain. extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till HeyBuddy.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **HeyBuddy**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **HeyBuddy**.

    ![HeyBuddy-länken i program listan](common/all-applications.png)

3. I menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-heybuddy-test-user"></a>Skapa HeyBuddy test användare

I det här avsnittet skapas en användare som kallas Britta Simon i HeyBuddy. HeyBuddy stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i HeyBuddy skapas en ny efter autentiseringen.

> [!Note]
> Kontakta [HeyBuddy support team](mailto:support@heybuddy.com)om du behöver skapa en användare manuellt.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen HeyBuddy på åtkomst panelen, bör du loggas in automatiskt på den HeyBuddy som du ställer in SSO för. I [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) får du mer information.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)