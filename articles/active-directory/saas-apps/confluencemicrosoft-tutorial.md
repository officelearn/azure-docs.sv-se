---
title: 'Självstudier: Azure Active Directory-integrering med Confluence SAML SSO från Microsoft | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Confluence SAML SSO från Microsoft.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44f0c99a66088aeb54ba061308fefb111610d4dc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59501237"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Självstudier: Azure Active Directory-integrering med Confluence SAML SSO från Microsoft

I den här självstudien får du lära dig hur du integrerar Confluence SAML SSO från Microsoft med Azure Active Directory (Azure AD).
Genom att integrera Confluence SAML SSO från Microsoft med Azure AD får du följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Confluence SAML SSO från Microsoft.
* Du kan göra så att dina användare automatiskt loggas in på Confluence SAML SSO från Microsoft (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="description"></a>Beskrivning:

Använd ditt Microsoft Azure Active Directory-konto med Atlassian Confluence-servern för att aktivera enkel inloggning. På så sätt alla organisationens användare kan använda Azure AD-autentiseringsuppgifter för att logga in i programmet växer samman. Det här plugin-programmet använder SAML 2.0 för federation.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Confluence SAML SSO från Microsoft behöver du följande:

- En Azure AD-prenumeration
- Confluence-serverprogrammet installerat på en Windows 64-bitars server (lokalt eller i moln-IaaS-infrastrukturen)
- Confluence-servern är HTTPS-aktiverad
- Observera att de versioner som stöds för Confluence-plugin-programmet anges i avsnittet nedan.
- Confluence-servern kan nås via Internet, särskilt för Azure AD-inloggningssidan för autentisering, och bör kunna ta emot token från Azure AD
- Administratörsautentiseringsuppgifter konfigureras i Confluence
- WebSudo är inaktiverat i Confluence
- Testanvändare har skapats i Confluence-serverprogrammet

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte att du använder en produktionsmiljö i Confluence. Testa integrationen först i utvecklings- eller mellanlagringsmiljön för programmet och använd sedan produktionsmiljön.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du få en tre månaders kostnadsfri utvärdering här: [Utvärderingserbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Versioner av Confluence som stöds

För närvarande stöds följande versioner av Confluence:

- Confluence: 5.0 till 5.10
- Confluence: 6.0.1
- Confluence: 6.1.1
- Confluence: 6.2.1
- Confluence: 6.3.4
- Confluence: 6.4.0
- Confluence: 6.5.0
- Confluence: 6.6.2
- Confluence: 6.7.0
- Confluence: 6.8.1
- Confluence: 6.9.0
- Confluence: 6.10.0
- Confluence: 6.11.0
- Confluence: 6.12.0

> [!NOTE]
> Observera att växer samman också stöder Linux Ubuntu version 16.04

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Confluence SAML SSO från Microsoft stöder **IDP**-initierad enkel inloggning

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Lägga till Confluence SAML SSO från Microsoft från galleriet

För att konfigurera integreringen av Confluence SAML SSO från Microsoft till Azure AD behöver du lägga till Confluence SAML SSO från Microsoft från galleriet till listan över hanterade SaaS-appar.

**Lägg till Confluence SAML SSO från Microsoft från galleriet genom att utföra följande steg:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Confluence SAML SSO från Microsoft**, väljer **Confluence SAML SSO från Microsoft** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Confluence SAML SSO från Microsoft i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning för Azure AD med Confluence SAML SSO från Microsoft baserat på en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Confluence SAML SSO från Microsoft upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Confluence SAML SSO från Microsoft behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Confluence SAML SSO från Microsoft](#configure-confluence-saml-sso-by-microsoft-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Confluence SAML SSO från Microsoft-testanvändare](#create-confluence-saml-sso-by-microsoft-test-user)** – för att ha en motsvarighet för Britta Simon i Confluence SAML SSO från Microsoft som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Confluence SAML SSO från Microsoft:

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Confluence SAML SSO från Microsoft**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Confluence SAML SSO från Microsoft-domän och information om URL:er för enkel inloggning](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<domain:port>/`

    c. I textrutan **svars-URL** skriver du en URL med följande mönster: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Porten är valfri ifall den är en namngiven URL. Dessa värden tas emot under konfigurationen av Confluence-pluginprogrammet, som beskrivs senare i självstudien.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>Konfigurera enkel inloggning för Confluence SAML SSO från Microsoft

1. I ett annat webbläsarfönster, loggar du in din växer samman-instans som administratör.

2. Hovra över kugghjulet och klicka på **Tillägg**.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon1.png)

3. Ladda ned plugin-programmet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). Ladda manuellt upp det plugin-program som tillhandahålls av Microsoft med hjälp av menyn **Upload add-on** (Ladda upp tillägg). Nedladdningen av plugin-programmet täcks av [Microsofts serviceavtal](https://www.microsoft.com/servicesagreement/).

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon12.png)

4. När plugin-programmet har installerats visas det i avsnittet för **användarinstallerade** tillägg i avsnittet **Manage Add-on** (Hantera tillägg). Klicka på **Konfigurera** för att konfigurera det nya plugin-programmet.

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon13.png)

5. Utför följande steg på konfigurationssidan:

    ![Konfigurera enkel inloggning](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Kontrollera att det bara finns ett certifikat mappat mot appen så att det inte förekommer några fel vid lösningen av metadata. Om det finns flera certifikat får administratören ett fel vid lösningen av metadata.

    a. I textrutan **Metadata URL** klistrar du in värdet för den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen och klickar på knappen **Lös**. Den läser metadata-URL för IdP och fyller i information i alla fält.

    b. Kopiera värdena för **identifierare, svars-URL och inloggnings-URL** och klistra in dem respektive textrutor för **identifierare, svars-URL och inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    c. I **Login Button Name** (Namn på inloggningsknapp) skriver du namnet på den knapp som organisationen vill att användarna ser på inloggningsskärmen.

    d. I **SAML User ID Locations** (Platser för SAML-användar-ID) väljer du antingen **User ID is in the NameIdentifier element of the Subject statement** (Användar-ID finns i elementet NameIdentifieri instruktionen Ämne) eller **User ID is in an Attribute element** (Användar-ID finns i ett Attribut-element).  Det här ID: T måste vara växer samman användar-ID. Om det användar-ID inte matchas sedan kan inte användare att logga in. 

    > [!Note]
    > Standardplatsen för SAML-användar-ID är Name Identifier (Namnidentifierare). Du kan ändra den till ett attributalternativ och ange lämpligt attributnamn.
    
    e. Om du väljer **användar-ID är i ett attributelement** alternativet i **attributnamnet** textrutan skriver du namnet på attributet som där användar-ID förväntas. 

    f. Om du använder den federerade domänen (till exempel ADFS osv) med Azure AD klickar du på alternativet **Enable Home Realm Discovery** (Aktivera identifiering av hemsfär) och konfigurerar **Domännamn**.
    
    g. I **Domännamn** anger du domännamnet för ADFS-baserad inloggning.

    h. Kontrollera **aktivera enkelinloggning ut** om du vill logga ut från Azure AD när en användare loggar ut från växer samman. 

    i. Klicka på knappen **Spara** för att spara inställningarna.

    > [!NOTE]
    > Mer information om installation och felsökning finns i [administratörsguiden för anslutningsapp för enkel inloggning med MS Confluence](../ms-confluence-jira-plugin-adminguide.md), och du kan läsa [vanliga frågor och svar](../ms-confluence-jira-plugin-faq.md)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp `brittasimon\@yourcompanydomain.extension`. Till exempel BrittaSimon@contoso.com.

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Confluence SAML SSO från Microsoft.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Confluence SAML SSO från Microsoft**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Confluence SAML SSO från Microsoft**.

    ![Confluence SAML SSO från Microsoft-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Skapa Confluence SAML SSO från Microsoft-testanvändare

Om du vill aktivera Azure AD-användare att logga in på växer samman den lokala servern, måste de etableras i antal samverkande SAML SSO av Microsoft. När det gäller Confluence SAML SSO från Microsoft är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din växer samman den lokala servern som administratör.

2. Hovra över kugghjulet och klicka på **Användarhantering**.

    ![Lägga till medarbetare](./media/confluencemicrosoft-tutorial/user1.png) 

3. Klicka på fliken **Lägg till användare** i avsnittet Användare. Utför följande steg i dialogrutan **Lägg till en användare**:

    ![Lägga till medarbetare](./media/confluencemicrosoft-tutorial/user2.png) 

    a. I textrutan **Användarnamn** skriver du e-postadressen för användaren: Britta Simon.

    b. I textrutan **Fullständigt namn** skriver du det fullständiga namnet för användaren: Britta Simon.

    c. I textrutan **E-post** skriver du e-postadressen för användaren: Brittasimon@contoso.com.

    d. I textrutan **Lösenord** skriver du lösenordet för Britta Simon.

    e. Klicka på **Bekräfta lösenord** för att ange lösenordet igen.

    f. Klicka på knappen **Lägg till**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Confluence SAML SSO från Microsoft-panelen i åtkomstpanelen bör du automatiskt loggas in på Confluence SAML SSO från Microsoft som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

