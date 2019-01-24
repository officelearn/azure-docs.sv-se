---
title: 'Självstudier: Azure Active Directory-integrering med NetSuite | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetSuite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2018
ms.author: jeedes
ms.openlocfilehash: f93f6d01019b38fc157732ee221a5f73a156e809
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54810405"
---
# <a name="tutorial-azure-active-directory-integration-with-netsuite"></a>Självstudier: Azure Active Directory-katalogintegrering med NetSuite

I den här självstudien får du lära dig hur du integrerar NetSuite med Azure Active Directory (Azure AD).
Att integrera NetSuite med Azure AD ger dig följande fördelar:

* I Azure AD kan du styra vem som har åtkomst till NetSuite.
* Du kan låta dina användare automatiskt loggas in på NetSuite (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med NetSuite så behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* NetSuite-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* NetSuite stöder **IDP**-initierad enkel inloggning
* NetSuite stöder **Just-in-time**-användaretablering
* NetSuite stöder [Automatiserad användaretablering](NetSuite-provisioning-tutorial.md)

## <a name="adding-netsuite-from-the-gallery"></a>Lägga till NetSuite från galleriet

Om du vill konfigurera integrering av NetSuite till Azure AD så behöver du lägga till NetSuite från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till NetSuite från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du in **NetSuite**, väljer **NetSuite** från resultatpanelen och klickar därefter på **Lägg till**-knappen för att lägga till programmet.

     ![NetSuite i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med NetSuite baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera så måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i NetSuite upprättas.

Om du vill konfigurera och testa enkel inloggning i Azure AD med NetSuite så måste du slutföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för NetSuite](#configure-netsuite-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa NetSuite-testanvändaren](#create-netsuite-test-user)** – för att få en motpart för Britta Simon i NetSuite som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med NetSuite:

1. I [Azure-portalen](https://portal.azure.com/), på **NetSuite**-sidan för programintegrering så väljer du **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om NetSuite-domän och -URL:er för enkel inloggning](common/idp-reply.png)

    I textrutan **Svars-URL** skriver du en URL med följande mönster:

    `https://<tenant-name>.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.NetSuite.com/saml2/acs`

    `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs`

    `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [NetSuite-klientsupportteamet](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) för att få värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. NetSuite-programmet förväntar sig SAML-intyget i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:
    
    | Namn | Källattribut | 
    | ---------------| --------------- |
    | konto  | `account id` |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    >[!NOTE]
    >Värdet för kontoattributet är inte verkligt. Du kommer att uppdatera det här värdet som förklaras senare i självstudien.

4. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera NetSuite** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-netsuite-single-sign-on"></a>Konfigurera enkel inloggning med NetSuite

1. Öppna en ny flik i din webbläsare och logga in på din NetSuite-företagswebbplats som administratör.

2. I verktygsfältet högst upp på sidan, klickar du på **Konfigurera** och går sedan till **Företag** och klickar på **Aktivera funktioner**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setupsaml.png)

3. I verktygsfältet i mitten på sidan, klickar du på **SuiteCloud**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Under avsnittet **Hantera autentisering** väljer du **SAML enkel inloggning** för att aktivera alternativet SAML enkel inloggning i NetSuite.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-ticksaml.png)

5. I verktygsfältet i mitten på sidan, klickar du på **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

6. Från listan **INSTALLATIONSÅTGÄRDER** klickar du på **Integrering**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-integration.png)

7. I avsnittet **Hantera autentisering** klickar du på **SAML enkel inloggning**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml.png)

8. På sidan **SAML-konfiguration** under avsnittet **NetSuite-konfiguration** så utför du följande steg:

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Välj **Primär autentiseringsmetod**.

    b. För fältet som är märkt **Metadata för SAMLV2-identitetsprovider** så väljer du **Överför IDP-metadatafil**. Klicka därefter på **Bläddra** för att överföra den metadatafil som du hämtade från Azure-portalen.

    c. Klicka på **Skicka**.

9. I NetSuite, klickar du på **Konfiguration** och går sedan till **Företag** och klickar på **Företagsinformation** från den övre navigeringsmenyn.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-com.png)

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-account-id.png)

    b. På sidan **Företagsinformation** i den högra kolumnen kopierar du **Konto-ID**.

    c. Klistra in det **Konto-ID** som du har kopierat från NetSuite-kontot till fältet **Attributvärde** i Azure AD. 

10. Innan användare kan utföra enkel inloggning till NetSuite så måste de först tilldelas lämpliga behörigheter i NetSuite. Följ anvisningarna nedan för att tilldela dessa behörigheter.

    a. I den övre navigeringsmenyn klickar du på **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    b. I den vänstra navigeringsmenyn väljer du **Användare/roller** och klickar sedan på **Hantera roller**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Klicka på **Ny roll**.

    d. Ange ett **Namn** för din nya roll.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-new-role.png)

    e. Klicka på **Spara**.

    f. I den översta menyn klickar du på **Behörigheter**. Klicka sedan på **Konfigurera**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-sso.png)

    g. Välj **SAML enkel inloggning** och klicka sedan på **Lägg till**.

    h. Klicka på **Spara**.

    i. I den övre navigeringsmenyn klickar du på **Konfigurera** och klickar sedan på **Konfigurationshanteraren**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-setup.png)

    j. I den vänstra navigeringsmenyn väljer du **Användare/roller** och klickar sedan på **Hantera användare**.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Välj en testanvändare. Klicka sedan på **Redigera** och gå till **Åtkomst**-fliken.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-edit-user.png)

    l. I dialogrutan Roller, tilldela den roll som du har skapat.

    ![Konfigurera enkel inloggning](./media/NetSuite-tutorial/ns-add-role.png)

    m. Klicka på **Spara**.

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

I det här avsnittet så låter du Britta Simon använda Azure enkel inloggning genom att ge åtkomst till NetSuite.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och därefter **NetSuite**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan skriver du in och väljer **NetSuite**.

    ![NetSuite-länken i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-netsuite-test-user"></a>Skapa NetSuite-testanvändare

I det här avsnittet skapas en användare som heter Britta Simon i NetSuite. NetSuite stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i NetSuite så skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på NetSuite-panelen i åtkomstpanelen så borde du automatiskt loggas in på den NetSuite som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Konfigurera användarförsörjning](NetSuite-provisioning-tutorial.md)

