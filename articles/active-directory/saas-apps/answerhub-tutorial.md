---
title: 'Självstudier: Azure Active Directory-integrering med AnswerHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.openlocfilehash: 41a1eef4ff417890114addcac00e2df3e49dc529
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55453271"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Självstudier: Azure Active Directory-integrering med AnswerHub

I den här självstudien lär du dig att integrera AnswerHub med Azure Active Directory (Azure AD).
Integreringen av AnswerHub med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till AnswerHub.
* Du kan göra så att dina användare automatiskt loggas in på AnswerHub (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande saker för att konfigurera Azure AD-integrering med AnswerHub:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* AnswerHub-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* AnswerHub stöder **IDP**-initierad enkel inloggning

## <a name="adding-answerhub-from-the-gallery"></a>Lägga till AnswerHub från galleriet

För att konfigurera integrering av AnswerHub med Azure AD behöver du lägga till AnswerHub från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AnswerHub från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **AnswerHub**, väljer **AnswerHub** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![AnswerHub i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med AnswerHub baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i AnswerHub upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för AnswerHub:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för AnswerHub](#configure-answerhub-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa AnswerHub-testanvändare](#create-answerhub-test-user)** – för att ha en motsvarighet till Britta Simon i AnswerHub som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för AnswerHub:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **AnswerHub** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![AnswerHub-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<company>.answerhub.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company>.answerhub.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [kundsupporten för AnswerHub](mailto:success@answerhub.com) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera AnswerHub** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-answerhub-single-sign-on"></a>Konfigurera enkel inloggning för AnswerHub

1. I ett nytt webbläsarfönster loggar du in på din AnswerHub-företagsplats som administratör.

    > [!NOTE]
    > Om du behöver hjälp med att konfigurera AnswerHub kontaktar du [supportteamet för AnswerHub](mailto:success@answerhub.com.).

2. Gå till **Administration**.

3. Klicka på fliken **Användare och grupp**.

4. I navigeringsfönstret till vänster går du till avsnittet **Social Settings** (Sociala inställningar) och klickar på **SAML-konfiguration**.

5. Klicka på fliken **IDP Config** (IDP-konfiguration).

6. På fliken **IDP Config** (IDP-konfiguration) utför du följande steg:

    ![SAML-konfiguration](./media/answerhub-tutorial/ic785172.png "SAML-konfiguration")  
  
    a. I textrutan för **inloggnings-URL för IDP** klistrar du in den **inloggnings-URL** som du har kopierat från Azure-portalen.
  
    b. I textrutan för **utloggnings-URL för IDP** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    c. I textrutan för **format för IDP-namnidentifierare** anger du samma användar-ID-värde som valts i Azure-portalen i avsnittet **Användarattribut**.
  
    d. Klicka på **Nycklar och certifikat**.

7. På fliken **Nycklar och certifikat** utför du följande steg:

    ![Nycklar och certifikat](./media/answerhub-tutorial/ic785173.png "Nycklar och certifikat")  

    a. Öppna ditt base-64-kodade certifikat som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in den i textrutan **IDP Public Key (x509 Format)** (Offentlig nyckel för IDP (x509-format)).
  
    b. Klicka på **Spara**.

8. På fliken **IDP Config** (IDP-konfiguration) klickar du på **Spara**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till AnswerHub.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **AnswerHub**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **AnswerHub**.

    ![Länken för AnswerHub i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-answerhub-test-user"></a>Skapa AnswerHub-testanvändare

Om du vill ge Azure AD-användare möjlighet att logga in på AnswerHub måste de etableras i AnswerHub. När det gäller AnswerHub är etablering en manuell uppgift.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **AnswerHub**-företagsplats som administratör.

2. Gå till **Administration**.

3. Klicka på fliken **Användare och grupper**.

4. I navigeringsfönstret till vänster går du till avsnittet **Hantera användare**, klickar på **Skapa eller importera användare** och klickar sedan på **Användare och grupper**.

   ![Användare och grupper](./media/answerhub-tutorial/ic785175.png "Användare och grupper")

5. Skriv **E-postadress**, **Användarnamn** och **Lösenord** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna, och klicka sedan på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg eller API:er för skapande av AnswerHub-användare som tillhandahålls av AnswerHub för att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på AnswerHub-panelen i åtkomstpanelen bör du automatiskt loggas in på AnswerHub som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

