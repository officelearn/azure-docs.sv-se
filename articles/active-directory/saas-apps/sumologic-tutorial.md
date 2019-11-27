---
title: 'Självstudie: Azure Active Directory integrering med SumoLogic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SumoLogic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fbb76765-92d7-4801-9833-573b11b4d910
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 7548d7d7b808472b3f5446fadfe800584f61b1df
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233355"
---
# <a name="tutorial-azure-active-directory-integration-with-sumologic"></a>Självstudie: Azure Active Directory integrering med SumoLogic

I den här självstudien får du lära dig hur du integrerar SumoLogic med Azure Active Directory (Azure AD).
Genom att integrera SumoLogic med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till SumoLogic.
* Du kan göra det möjligt för användarna att logga in automatiskt till SumoLogic (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med SumoLogic behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* SumoLogic-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SumoLogic stöder **SP** -INITIERAd SSO

## <a name="adding-sumologic-from-the-gallery"></a>Lägga till SumoLogic från galleriet

Om du vill konfigurera integreringen av SumoLogic i Azure AD måste du lägga till SumoLogic från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till SumoLogic från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **SumoLogic**, väljer **SumoLogic** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![SumoLogic i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med SumoLogic baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i SumoLogic upprättas.

Om du vill konfigurera och testa enkel inloggning med SumoLogic i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SumoLogic-enkel inloggning](#configure-sumologic-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa SumoLogic test User](#create-sumologic-test-user)** – om du vill ha en motsvarighet till Britta Simon i SumoLogic som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med SumoLogic i Azure AD:

1. Välj **enkel inloggning**på sidan **SumoLogic** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för SumoLogic-domän och URL: er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<tenantname>.SumoLogic.com`

   b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:

    | |
    |--|
    | `https://<tenantname>.us2.sumologic.com` |
    | `https://<tenantname>.sumologic.com` |
    | `https://<tenantname>.us4.sumologic.com` |
    | `https://<tenantname>.eu.sumologic.com` |
    | `https://<tenantname>.au.sumologic.com` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [SumoLogic client support team](https://www.sumologic.com/contact-us/) för att hämta dessa värden. Du kan även se de mönster som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera SumoLogic** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-sumologic-single-sign-on"></a>Konfigurera SumoLogic enkel inloggning

1. Logga in på din SumoLogic-företags webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **hantera \> säkerhet**.

    ![Hantera](./media/sumologic-tutorial/ic778556.png "Hantera")

1. Klicka på **SAML**.

    ![Globala säkerhets inställningar](./media/sumologic-tutorial/ic778557.png "Globala säkerhets inställningar")

1. Välj **Azure AD**i listan **Välj en konfiguration eller skapa en ny** , och klicka sedan på **Konfigurera**.

    ![Konfigurera SAML 2,0](./media/sumologic-tutorial/ic778558.png "Konfigurera SAML 2,0")

1. I dialog rutan **Konfigurera SAML 2,0** utför du följande steg:

    ![Konfigurera SAML 2,0](./media/sumologic-tutorial/ic778559.png "Konfigurera SAML 2,0")

    a. I text rutan **konfigurations namn** skriver du **Azure AD**.

    b. Välj **fel söknings läge**.

    c. I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    d. I text rutan **authn-URL för begäran** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    e. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra sedan in hela certifikatet i text rutan för **X. 509-certifikat** .

    f. Som **email-attribut**väljer du **Använd SAML subject**.  

    g. Välj **SP initierad inloggnings konfiguration**.

    h. I text rutan **inloggnings Sök väg** skriver du **Azure** och klickar på **Spara**.

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

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SumoLogic.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **SumoLogic**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SumoLogic**.

    ![SumoLogic-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-sumologic-test-user"></a>Skapa SumoLogic test användare

För att Azure AD-användare ska kunna logga in på SumoLogic måste de tillhandahållas till SumoLogic. När det gäller SumoLogic är etableringen en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **SumoLogic** -klient.

1. Gå till **Hantera \> Användare**.

    ![Användare](./media/sumologic-tutorial/ic778561.png "Användare")

1. Klicka på **Lägg till**.

    ![Användare](./media/sumologic-tutorial/ic778562.png "Användare")

1. Utför följande steg i dialog rutan **ny användare** :

    ![Ny användare](./media/sumologic-tutorial/ic778563.png "Ny användare") 

    a. Skriv relaterad information för det Azure AD-konto som du vill etablera i text rutorna för **förnamn**, **efter namn**och **e-post** .
  
    b. Välj en roll.
  
    c. Som **status**väljer du **aktiv**.
  
    d. Klicka på **Save** (Spara).

> [!NOTE]
> Du kan använda andra verktyg för SumoLogic av användar konton eller API: er som tillhandahålls av SumoLogic för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SumoLogic på åtkomst panelen, bör du loggas in automatiskt på den SumoLogic som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

