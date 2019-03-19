---
title: 'Självstudier: Azure Active Directory-integrering med Druva | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: da134672224c5881a69f002d418c79af97036d8b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57835573"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Självstudier: Azure Active Directory-integrering med Druva

I den här självstudien lär du dig att integrera Druva med Azure Active Directory (AD Azure).
Integreringen av Druva med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Druva.
* Du kan göra så att dina användare automatiskt loggas in på Druva (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Druva behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Druva-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Druva har stöd för **SP**- och **IDP**-initierad enkel inloggning

## <a name="adding-druva-from-the-gallery"></a>Lägga till Druva från galleriet

För att konfigurera integrering av Druva i Azure AD behöver du lägga till Druva från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Druva från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Druva**, väljer **Druva** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Druva i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD till Druva baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Druva upprättas.

För att konfigurera och testa enkel inloggning för Azure AD med Druva behöver du slutföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Druva](#configure-druva-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Druva-testanvändare](#create-druva-test-user)** – för att ha en motsvarighet för Britta Simon i Druva som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med Azure AD för Druva:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Druva** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera appen i **IDP**-initierat läge gör du följande i avsnittet **Grundläggande SAML-konfiguration**:

    ![Druva-domän och information om URL:er för enkel inloggning](common/idp-identifier.png)

    I textrutan **Identifierare** skriver du ett strängvärde:  `druva-cloud`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![image](common/both-preintegrated-signon.png)

    I rutan **Inloggnings-URL** anger du en URL: `https://cloud.druva.com/home`

6. Druva-programmet förväntar sig SAML-försäkran i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg: 

    | Namn | Källattribut|
    | ------------------- | -------------------- |
    | insync\_auth\_token |Ange det tokengenererade värdet |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Druva** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-druva-single-sign-on"></a>Konfigurera enkel inloggning för Druva

1. I ett annat webbläsarfönster loggar du in på din Druva-företagswebbplats som administratör.

2. Gå till **Hantera \> Inställningar**.

    ![Inställningar](./media/druva-tutorial/ic795091.png "Inställningar")

3. I dialogrutan Inställningar för enkel inloggning utför du följande steg:

    ![Inställningar för enkel inloggning](./media/druva-tutorial/ic795092.png "Inställningar för enkel inloggning")
    
    a. I textrutan **ID Provider Login URL** (Inloggnings-URL för ID-provider) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.
        
    b. I textrutan **ID Provider Logout URL** (Utloggnings-URL för ID-provider) klistrar du in värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen
        
    c. Öppna ditt Base64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra in det i textrutan **ID Provider Certificate** (Certifikat för ID-provider)
     
    d. Öppna sidan **Inställningar** genom att klicka på **Spara**.

4. På sidan **Inställningar** klickar du på **Generate SSO Token** (Generera token för enkel inloggning).

    ![Inställningar](./media/druva-tutorial/ic795093.png "Inställningar")

5. I dialogrutan **Single Sign-on Authentication Token** (Token för autentisering med enkel inloggning) utför du följande steg:

    ![Token för enkel inloggning](./media/druva-tutorial/ic795094.png "Token för enkel inloggning")
    
    a. Klicka på **Kopia**, klistra in det kopierade värdet i textrutan **Värde** i avsnittet **Lägg till attribut** i Azure-portalen.
    
    b. Klicka på **Stäng**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon\@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Druva.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Druva**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Druva**.

    ![Länken för Druva i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-druva-test-user"></a>Skapa Druva-testanvändare

För att göra det möjligt för Azure AD-användare att logga in på Druva måste de etableras till Druva. När det gäller Druva är etablering en manuell uppgift.

**Utför följande steg för att konfigurera användarförsörjning:**

1. Logga in på din **Druva**-företagswebbplats som administratör.

2. Gå till **Hantera \> Användare**.
   
    ![Hantera användare](./media/druva-tutorial/ic795097.png "Hantera användare")

3. Klicka på **Skapa ny**.
   
    ![Hantera användare](./media/druva-tutorial/ic795098.png "Hantera användare")

4. I dialogrutan Skapa ny användare utför du följande steg:
   
    ![Skapa ny användare](./media/druva-tutorial/ic795099.png "Skapa ny användare")
   
    a. I den **e-postadress** textrutan Ange e-postadress för användaren som **brittasimon\@contoso.com**.
   
    b. I textrutan **Namn** anger du namnet på användaren: **BrittaSimon**.
   
    c. Klicka på **Skapa användare**.

>[!NOTE]
>Du kan använda andra verktyg eller API:er för Druva-kontoskapande som tillhandahålls av Druva för att etablera Azure AD-användarkonton.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på Druva-panelen i åtkomstpanelen bör du automatiskt loggas in på Druva som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

