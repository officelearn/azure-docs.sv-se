---
title: 'Självstudier: Azure Active Directory integrering med TimeOffManager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 69c5d30632e187efe36655a17a91c9e373062955
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68943191"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Självstudier: Azure Active Directory integrering med TimeOffManager

I den här självstudien får du lära dig hur du integrerar TimeOffManager med Azure Active Directory (Azure AD).
Genom att integrera TimeOffManager med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till TimeOffManager.
* Du kan göra det möjligt för användarna att logga in automatiskt till TimeOffManager (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure portal.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TimeOffManager behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* TimeOffManager-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* TimeOffManager stöder **IDP** INITIERAd SSO

* TimeOffManager stöder **just-in-Time** User-etablering

## <a name="adding-timeoffmanager-from-the-gallery"></a>Lägga till TimeOffManager från galleriet

Om du vill konfigurera integreringen av TimeOffManager i Azure AD måste du lägga till TimeOffManager från galleriet i listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till TimeOffManager från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)** , klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök skriver du **TimeOffManager**, väljer **TimeOffManager** från resultat panelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![TimeOffManager i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med TimeOffManager baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i TimeOffManager upprättas.

Om du vill konfigurera och testa enkel inloggning med TimeOffManager i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera TimeOffManager-enkel inloggning](#configure-timeoffmanager-single-sign-on)** för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Skapa TimeOffManager test User](#create-timeoffmanager-test-user)** – om du vill ha en motsvarighet till Britta Simon i TimeOffManager som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med TimeOffManager i Azure AD:

1. Välj **enkel inloggning**på sidan **TimeOffManager** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera enkel inloggning för länken](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning för TimeOffManager-domän och URL: er](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera det här värdet med den faktiska svars-URL:en. Du kan hämta det här värdet från **sidan Inställningar för enkel inloggning** , som beskrivs senare i själv studie kursen eller kontakta [TimeOffManager support team](https://www.purelyhr.com/contact-us). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. TimeOffManager-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

6. Utöver ovan förväntar sig TimeOffManager-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan: 

    | Namn | Källattribut|
    | --- | --- |
    | Förnamn |User. givenName |
    | Efternamn |Användare. efter namn |
    | Email |User.mail |

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

    ![Länk för hämtning av certifikat](common/certificatebase64.png)

8. I avsnittet **Konfigurera TimeOffManager** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggningswebbadress

    b. Microsoft Azure Active Directory-identifierare

    c. Utloggnings-URL

### <a name="configure-timeoffmanager-single-sign-on"></a>Konfigurera TimeOffManager enkel inloggning

1. Logga in på din TimeOffManager-företags webbplats som administratör i ett annat webbläsarfönster.

2. Gå till **konto \> konto alternativ \> inställningar för enkel inloggning**.
   
    ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795917.png "Inställningar för enkel inloggning")

3. I avsnittet **Inställningar för enkel inloggning** utför du följande steg:
   
    ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795918.png "Inställningar för enkel inloggning")
   
    a. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra sedan in hela certifikatet i text rutan för **X. 509-certifikat** .
   
    b. I text rutan för **IDP** -utfärdare klistrar du in värdet för **Azure AD** -identifieraren som du kopierade från Azure Portal.
   
    c. I text rutan **URL för IDP-slutpunkt** klistrar du in värdet för inloggnings- **URL: en** som du har kopierat från Azure Portal.
   
    d. Som **FRAMTVINGA SAML**väljer du **Nej**.
   
    e. Välj **Ja**som **skapa användare automatiskt**.
   
    f. I textrutan för **utloggnings-URL** klistrar du in värdet för **utloggnings-URL:en** som du har kopierat från Azure-portalen.
   
    g. Klicka på **Spara ändringar**.

4. På sidan **Inställningar för enkel inloggning** kopierar du värdet för **URL: en** för den försäkrans konsument tjänst och klistrar in den i text rutan svars- **URL** i avsnittet **grundläggande SAML-konfiguration** i Azure Portal. 

      ![Inställningar för enkel inloggning](./media/timeoffmanager-tutorial/ic795915.png "Inställningar för enkel inloggning")

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I brittasimon@yourcompanydomain.extensionfältet **användar namn** . Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till TimeOffManager.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **TimeOffManager**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TimeOffManager**.

    ![TimeOffManager-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-timeoffmanager-test-user"></a>Skapa TimeOffManager test användare

I det här avsnittet skapas en användare som kallas Britta Simon i TimeOffManager. TimeOffManager stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i TimeOffManager skapas en ny efter autentiseringen.

>[!NOTE]
>Du kan använda andra verktyg för TimeOffManager av användar konton eller API: er som tillhandahålls av TimeOffManager för att etablera Azure AD-användarkonton.
> 

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen TimeOffManager på åtkomst panelen, bör du loggas in automatiskt på den TimeOffManager som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

