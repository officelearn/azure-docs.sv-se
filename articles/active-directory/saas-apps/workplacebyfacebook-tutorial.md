---
title: 'Självstudier: Azure Active Directory-integrering med Workplace by Facebook | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Workplace by Facebook.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e233de6f0909477e5033d2a0104f9165e86a9077
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311043"
---
# <a name="tutorial-azure-active-directory-integration-with-workplace-by-facebook"></a>Självstudier: Azure Active Directory-integrering med Workplace by Facebook

I den här självstudien lär du dig att integrera Workplace by Facebook med Azure Active Directory (Azure AD).
Genom att integrera Workplace by Facebook med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Workplace by Facebook.
* Du kan göra så att dina användare automatiskt loggas in på Workplace by Facebook (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver följande saker för att konfigurera Azure AD-integrering med Workplace by Facebook:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Prenumeration som aktiverats för enkel inloggning med Workplace by Facebook

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du skaffa en månads utvärderingsperiod [här](https://azure.microsoft.com/pricing/free-trial/).

> [!NOTE]
> Facebook har två produkter, Workplace Standard (kostnadsfri) och Workplace Premium (betalprodukt). Alla Workplace Premium-klientorganisationer kan konfigurera integrering med SCIM och enkel inloggning utan att det påverkar kostnaden eller de licenser som behövs. Enkel inloggning och SCIM är inte tillgängliga i Workplace Standard-instanser.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Workplace by Facebook har stöd för **SP**-initierad enkel inloggning
* Workplace by Facebook har stöd för **just-in-time-etablering**
* Workplace by Facebook har stöd för **[automatisk användarförsörjning](workplacebyfacebook-provisioning-tutorial.md)**

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Lägga till Workplace by Facebook från galleriet

För att konfigurera integrering av Workplace by Facebook med Azure AD behöver du lägga till Workplace by Facebook från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Workplace by Facebook från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Workplace by Facebook**, väljer **Workplace by Facebook** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Workplace by Facebook i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Workplace by Facebook baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Workplace by Facebook upprättas.

Du behöver slutföra följande byggstenar för att konfigurera och testa enkel inloggning med Azure AD för Workplace by Facebook:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Workplace by Facebook](#configure-workplace-by-facebook-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Workplace by Facebook-testanvändare](#create-workplace-by-facebook-test-user)** – för att ha en motsvarighet till Britta Simon i Workplace by Facebook som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Workplace by Facebook:

1. På [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Workplace by Facebook** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Workplace by Facebook-domän och information om URL:er för enkel inloggning](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<instancename>.facebook.com`

    b. I textrutan **Identifierare (entitets-ID)** skriver du en URL med följande mönster: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE] 
    > De här värdena är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Korrekta värden för din Workplace-community finns på sidan Autentisering på företagets instrumentpanel för Workplace.

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera Workplace by Facebook** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-workplace-by-facebook-single-sign-on"></a>Konfigurera enkel inloggning för Workplace by Facebook

1. I ett annat webbläsarfönster loggar du in på din Workplace by Facebook-företagsplats som administratör.
  
    > [!NOTE]
    > Som en del av SAML-autentiseringsprocessen kan Workplace by Facebook utnyttja frågesträngar på upp till 2,5 kB i storlek för att skicka parametrar till Azure AD.

2. I **administrationspanelen** går du till fliken **Säkerhet**.

    ![Administrationspanel](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

3. Under fliken **Autentisering** väljer du **Enkel inloggning** och utför följande steg:

    ![Fliken Autentisering](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. I textrutan **SAML URL** klistrar du in det värde för **Login URL** (Inloggnings-URL) som du har kopierat från Azure-portalen.

    b. I **textrutan SAML Issuer URI** (URI för SAML-utfärdare) klistrar du in det värde för **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. I **SAML Logout Redirect** (Omdirigering för SAML-utloggning) (valfritt) klistrar du in det värde för **Utloggnings-URL** som du har kopierat från Azure-portalen.

    d. Öppna ditt **base-64-kodade certifikat** som du har laddat ned från Azure-portalen i Anteckningar, kopiera innehållet i Urklipp och klistra sedan in den i textrutan **SAML Certificate** (SAML-certifikat).

    e. Kopiera **Målgrupps-URL** för din instans och klistra in den i textrutan  **Identifierare (entitets-ID)** i avsnittet  **Grundläggande SAML-konfiguration**  i Azure-portalen.

    f. Kopiera **Mottagar-URL** för din instans och klistra in den i textrutan  **Inloggnings-URL** i avsnittet  **Grundläggande SAML-konfiguration**  i Azure-portalen.

    g. Rulla ned till slutet av avsnittet och klicka på knappen **Test SSO** (Testa enkel inloggning). Då visas ett popup-fönster med Azure AD-inloggningssidan. Ange dina autentiseringsuppgifter som vanligt för att autentisera.

    **Felsökning:** Kontrollera att den e-postadress som returneras från Azure AD är samma som det Workplace-konto som du har loggat in med.

    h. När testet är klart rullar du längst ned på sidan och klickar på knappen **Spara**.

    i. Alla användare som använder Workplace ser nu Azure AD-inloggningssidan för autentisering.

4. **SAML Logout Redirect** - (Omdirigering för SAML-utloggning) (valfritt)

    Du kan även välja att konfigurera en SAML-utloggnings-URL som kan användas för att peka på utloggningssidan för Azure AD. När den här inställningen aktiveras och konfigureras omdirigeras användarna inte längre till utloggningssidan för Workplace. I stället omdirigeras de till den URL som lades till i inställningen för SAML-utloggningsomdirigering.

### <a name="configuring-reauthentication-frequency"></a>Konfigurera omautentiseringsfrekvens

Du kan konfigurera Workplace att fråga efter en SAML-kontroll varje dag, var tredje dag, varje vecka, varannan vecka, varje månad eller aldrig.

> [!NOTE]
> Minimivärdet för SAML-kontrollen i mobilprogram är inställt på en vecka.

Du kan även tvinga en SAML-återställning för alla användare med knappen: Kräv SAML-autentisering för alla användare nu.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Workplace by Facebook.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Workplace by Facebook**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Workplace by Facebook**.

    ![Länken för Workplace by Facebook i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-workplace-by-facebook-test-user"></a>Skapa Workplace by Facebook-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i Workplace by Facebook. Workplace by Facebook har stöd för just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd för dig i det här avsnittet. Om det inte finns någon användare i Workplace by Facebook skapas en ny när du försöker få åtkomst till Workplace by Facebook.

>[!Note]
>Kontakta [Workplace by Facebook-kundsupporten](https://workplace.fb.com/faq/) om du behöver skapa en användare manuellt

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Workplace by Facebook-panelen i åtkomstpanelen bör du automatiskt loggas in på Workplace by Facebook som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](workplacebyfacebook-provisioning-tutorial.md)
