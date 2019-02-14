---
title: 'Självstudier: Azure Active Directory-katalogintegrering med Zendesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zendesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a40597178a8267febfd6ad18277d6b865c084d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180172"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Självstudier: Azure Active Directory-katalogintegrering med Zendesk

I den här självstudien lär du dig att integrera Zendesk med Azure Active Directory (AD Azure).
När du integrerar Zendesk med Azure AD får du följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till Zendesk.
* Du kan göra så att dina användare automatiskt loggas in på Zendesk (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Zendesk behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Zendesk-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Zendesk stöder **SP**-initierad enkel inloggning

* Zendesk har stöd för [**automatisk** användaretablering](zendesk-provisioning-tutorial.md)

## <a name="adding-zendesk-from-the-gallery"></a>Lägga till Zendesk från galleriet

För att konfigurera integrering av Zendesk i Azure AD måste du lägga till Zendesk från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zendesk från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zendesk**, väljer **Zendesk** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Zendesk i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Zendesk baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Zendesk upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zendesk måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Zendesk](#configure-zendesk-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Zendesk-testanvändare](#create-zendesk-test-user)** – för att ha en motsvarighet till Britta Simon i Zendesk som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Om du vill konfigurera Azure AD enkel inloggning med Zendesk utför du följande steg:

1. På programintegreringssidan **Zendesk** i [Azure-portalen](https://portal.azure.com/) markerar du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för Zendesk-domän och URL:er](common/sp-identifier.png)

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.zendesk.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster:
    | |
    |-|-|
    | `<subdomain>.zendesk.com` |
    | `https://<subdomain>.zendesk.com` |
    | |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL och identifierare. Kontakta [Zendesk-klients supportteam](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Zendesk-program förväntar SAML-försäkran i ett visst format. Det finns inga obligatoriska SAML-attribut, men du kan också hantera från avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** väljer du lämpligt attributvärde.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    > [!NOTE]
    > Du kan använda tilläggsattribut för att lägga till attribut som inte ingår i Azure AD som standard. Klicka på [Användarattribut som kan konfigureras i SAML](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) för att hämta den fullständiga listan med SAML-attribut som **Zendesk** accepterar.

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, kopierar **tumavtrycket** och sparar det på datorn.

    ![Kopiera tumavtrycksvärdet](common/copy-certificatethumbprint.png)

8. I avsnittet **Konfigurera Zendesk** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

9. Du kan konfigurera Zendesk på två sätt – automatiskt och manuellt.
  
10. Om du vill automatisera konfigurationen i Zendesk måste du installera **webbläsartillägget Enkel inloggning för mina appar** genom att klicka på **Installera tillägget**.

    ![image](./media/zendesk-tutorial/install_extension.png)

11. När du har lagt till tillägg i webbläsaren klickar du på **Konfigurera Zendesk**. Du omdirigeras då till Zendesk-programmet. Därifrån anger du administratörsautentiseringsuppgifterna för att logga in på Zendesk. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar stegen i avsnitt **Konfigurera enkel inloggning med för Zendesk**.

    ![image](./media/zendesk-tutorial/d2_saml.png)

### <a name="configure-zendesk-single-sign-on"></a>Konfigurera enkel inloggning för Zendesk

1. Om du vill konfigurera Zendesk manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Zendesk-företagsplats som administratör och utför följande steg:

2. Klicka på **Admin**.

3. I det vänstra navigeringsfönstret klickar du på **Inställningar** och sedan på **Säkerhet**.

4. På sidan **Säkerhet** utför du följande steg:

    ![Säkerhet](././media/zendesk-tutorial/ic773089.png "Säkerhet")

    ![Single sign-on (Enkel inloggning)](././media/zendesk-tutorial/ic773090.png "Single sign-on (Enkel inloggning)")

    a. Klicka på fliken för **administratörer och agenter**.

    b. Välj **Enkel inloggning (SSO) och SAML** och välj sedan **SAML**.

    c. I textrutan **SAML SSO URL** klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    d. I textrutan för **URL för fjärrutloggning** klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    e. I textrutan **Certificate Fingerprint** (Fingeravtryck för certifikat) klistrar du in **tumavtrycksvärdet** för certifikatet som du har kopierat från Azure-portalen.

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
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Zendesk.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Zendesk**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Zendesk**.

    ![Zendesk-länken i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-zendesk-test-user"></a>Skapa Zendesk-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Zendesk. Zendesk stöder automatisk användaretablering, vilket är aktiverat som standard. Du hittar mer information [här](Zendesk-provisioning-tutorial.md) om hur du konfigurerar automatisk användaretablering.

**Om du behöver skapa användare manuellt så gör du följande:**

> [!NOTE]
> **Slutanvändarkonton** tillhandahålls automatiskt när du loggar in. Kontona **Agent** och **Admin** måste tillhandahållas manuellt i **Zendesk** innan du loggar in.

1. Logga in på din **Zendesk**-klient.

2. Välj fliken **Kundlista**.

3. Välj fliken **Användare** och klicka på **Lägg till**.

    ![Lägg till användare](././media/zendesk-tutorial/ic773632.png "Lägg till användare")
4. Skriv **namnet** och **e-postadressen** för ett befintligt Azure AD-konto du vill etablera och klicka sedan på **Spara**.

    ![Ny användare](././media/zendesk-tutorial/ic773633.png "Ny användare")

> [!NOTE]
> Du kan använda andra verktyg eller API:er för att skapa Zendesk-användarkonton som tillhandahålls av Zendesk för att etablera AAD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Zendesk-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Zendesk som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](zendesk-provisioning-tutorial.md)