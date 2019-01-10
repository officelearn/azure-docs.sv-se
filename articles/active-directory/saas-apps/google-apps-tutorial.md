---
title: 'Självstudier: Azure Active Directory-integrering med G Suite | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och G Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 31dc45dbe8d7e3ed9b701f8623fd4e1425320316
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107471"
---
# <a name="tutorial-azure-active-directory-integration-with-g-suite"></a>Självstudier: Azure Active Directory-katalogintegrering med G Suite

I den här självstudien får du lära dig hur du integrerar G Suite med Azure Active Directory (Azure AD).
Att integrera G Suite med Azure AD ger dig följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till G Suite.
* Du kan låta dina användare automatiskt loggas in på G Suite (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med G Suite, behöver du följande objekt:

- En Azure AD-prenumeration
- En prenumeration med enkel inloggning med G Suite aktiverat
- En Google Apps-prenumeration eller Google Cloud Platform-prenumeration.

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien. Det här dokumentet har skapats med den nya användarupplevelsen Enkel inloggning. Om du fortfarande använder den gamla upplevelsen ser installationen annorlunda ut. Du kan aktivera den nya upplevelsen i inställningarna för enkel inloggning i G Suite-programmet. Gå till **Azure AD, företagsprogram**, välj **G Suite**, välj **enkel inloggning** och klicka sedan på **Testa vår nya upplevelse**.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

1. **F: Stöder den här integreringen enkel inloggning med Google Cloud Platform med Azure AD?**

    S: Ja. Google Cloud Platform och Google Apps delar samma autentiseringsplattform. Så om du vill göra GCP-integreringen måste du konfigurera enkel inloggning med Google Apps.

2. **F: Är Chromebooks och andra Chrome-enheter kompatibla med enkel inloggning med Azure AD?**
  
    S: Ja, användare kan logga in på sina Chromebook-enheter med sina autentiseringsuppgifter för Azure AD. Se den här [G Suite-supportartikeln](https://support.google.com/chrome/a/answer/6060880) för information om varför användare kan tillfrågas om autentiseringsuppgifter två gånger.

3. **F: Om jag aktiverar enkel inloggning, kommer användare att kunna använda sina autentiseringsuppgifter för Azure AD för att logga in på en Google-produkt, till exempel Google Classroom, GMail, Google Drive, YouTube och så vidare?**

    S: Ja, beroende på [vilken G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) du väljer att aktivera eller inaktivera för din organisation.

4. **F: Kan jag aktivera enkel inloggning för endast en delmängd av mina G Suite-användare?**

    S: Nej, aktivering av enkel inloggning kräver omedelbart att alla dina G Suite-användare autentiserar sig med sina autentiseringsuppgifter för Azure AD. Eftersom G Suite inte har stöd för att ha flera identitetsproviders, kan identitetsleverantören för din G Suite-miljö antingen vara Azure AD eller Google – men inte båda på samma gång.

5. **F: Om en användare har loggat in via Windows, autentiseras hen automatiskt till G Suite utan att ett lösenord efterfrågas?**

    S: Det finns två alternativ för att aktivera det här scenariot. Antingen kan användare logga in på Windows 10-enheter via [Azure Active Directory Join](../device-management-introduction.md). Alternativt kan användare logga in på Windows-enheter som är domänanslutna till en lokal Active Directory som har aktiverats för enkel inloggning till Azure AD via en [Active Directory Federation Services](../hybrid/plan-connect-user-signin.md)-distribution (AD FS). I båda fallen måste du utföra stegen i följande självstudie för att aktivera enkel inloggning mellan Azure AD och G Suite.

6. **F: Vad gör jag när jag får felmeddelandet ”Ogiltig e-post”?**

    S: För den här konfigurationen krävs e-postadressattribut för användare för att kunna logga in. Det här attributet kan inte anges manuellt.

    E-postadressattribut fylls i automatiskt för alla användare med en giltig Exchange-licens. Om användaren inte är e-postaktiverad, får hen det här felet allteftersom programmet behöver få det här attributet för att ge åtkomst.

    Du kan gå till portal.office.com med ett administratörskonto och sedan i administrationscentret klicka på fakturering, prenumerationer, välja din Office 365-prenumeration och sedan klicka på tilldela till användare, välja de användare som du vill kontrollera prenumerationen för och i den högra rutan klicka på redigera licenser.

    När O365-licensen har tilldelats kan det ta några minuter innan den blir aktiv. Efter det kommer attributet user.mail att fyllas i automatiskt och problemet ska vara löst.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* G Suite har stöd för **SP**-initierad enkel inloggning
* G Suite har stöd för **[automatisk användaretablering](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)**

## <a name="adding-g-suite-from-the-gallery"></a>Att lägga till G Suite från galleriet

Om du vill konfigurera integreringen av G Suite till Azure AD, behöver du lägga till G Suite från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till G Suite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **G Suite**, väljer **G Suite** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![G Suite i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med G Suite baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i G Suite upprättas.

Om du vill konfigurera och testa enkel inloggning till Azure AD med G Suite, måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för G Suite](#configure-g-suite-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare av G Suite](#create-g-suite-test-user)** – för att få en motsvarighet till Britta Simon i G Suite som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning till Azure AD med G Suite:

1. I [Azure-portalen](https://portal.azure.com/), på **G Suite**-sidan för appintegrering väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning till G Suite-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [Supportteamet för G Suite](https://www.google.com/contact/) för att hämta dessa värden.

5. Ditt G Suite-program förväntar sig att SAML-intygen är i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i SAML-tokenattributkonfigurationen. Följande skärmbild visar ett exempel på detta. Standardvärdet för **Unik användaridentifierare** är **user.userprincipalname**, men G Suite förväntar sig att detta mappas med användarens e-postadress. Till det kan du använda **user.mail**-attributet från listan eller rätt attributvärde baserat på organisationens konfiguration.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Namn | Källattribut |
    | ---------------| --------------- |
    | Unik användaridentifierare | User.mail |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

8. I avsnittet **Konfigurera G Suite** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-g-suite-single-sign-on"></a>Konfigurera enkel inloggning för G Suite

1. Öppna en ny flik i webbläsaren och logga in på [G Suite-administratörskonsolen](https://admin.google.com/) med ditt administratörskonto.

2. Klicka på **Säkerhet**. Om du inte ser länken, kan den vara dold under menyn **Fler kontroller** längst ned på skärmen.

    ![Klicka på Säkerhet.][10]

3. På sidan **Säkerhet** klickar du på **Konfigurera enkel inloggning (SSO).**

    ![Klicka på enkel inloggning.][11]

4. Utför följande konfigurationsändringar:

    ![Konfigurera enkel inloggning][12]

    a. Välj **Installera enkel inloggning med tredjeparts identitetsprovider**.

    b. I fältet **URL för inloggningssida** i G Suite, klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure Portal.

    c. I fältet **URL för utloggningssida** i G Suite, klistrar du in det värde för **Utloggnings-URL** som du har kopierat från Azure Portal.

    d. I fältet **URL för att ändra lösenord** i G Suite, klistrar du in det värde för **URL för att ändra lösenord** som du har kopierat från Azure Portal.

    e. I G Suite för **verifieringscertifikatet**, laddar du upp det certifikat som du har hämtat från Azure Portal.

    f. Välj **Använd en domänspecifik utfärdare**.

    g. Klicka på **Spara ändringar**.

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till G Suite.

1. I Azure Portal väljer du **Företagsprogram**, **Alla program** och sedan **G Suite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **G Suite**.

    ![G Suite-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-g-suite-test-user"></a>Skapa G Suite-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i G Suite-programmet. G Suite har stöd för automatisk etablering, vilket som standard är aktiverat. Det finns ingen åtgärd för dig i det här avsnittet. Om en användare inte redan finns i G Suite-programmet, skapas en ny när du försöker få åtkomst till G Suite-programmet.

> [!NOTE]
> Kontrollera att användaren redan finns i G Suite om etablering i Azure AD inte har aktiverats innan du testar enkel inloggning.

> [!NOTE]
> Om du vill skapa en användare manuellt kan du kontakta [Google-supportteamet](https://www.google.com/contact/).

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på G Suite-panelen i åtkomstpanelen så borde du automatiskt loggas in på den G Suite som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Konfigurera användarförsörjning](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png