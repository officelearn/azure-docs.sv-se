---
title: 'Självstudier: Azure Active Directory-integrering med Palo Alto Networks – Admin UI | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Palo Alto Networks – Admin UI.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: a826eaec-15af-4c85-8855-8a3374d1efb9
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 06c32dbd9ee0d960e4cd17476349ba3d1a784d81
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54819007"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---admin-ui"></a>Självstudier: Azure Active Directory-integrering med Palo Alto Networks – Admin UI

I den här självstudien får du lära dig hur du integrerar Palo Alto Networks – Admin UI med Azure Active Directory (Azure AD).
Integreringen av Palo Alto Networks – Admin UI med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Palo Alto Networks – Admin UI från Azure AD.
* Du kan konfigurera inställningar så att dina användare automatiskt loggas in i Palo Alto Networks – Admin UI (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Palo Alto Networks – Admin UI behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En prenumeration på Palo Alto Networks – Admin UI med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Palo Alto Networks – Admin UI stöder **SP**-initierad enkel inloggning
* Palo Alto Networks – Admin UI stöder **JIT**-etablering (Just In Time)

## <a name="adding-palo-alto-networks---admin-ui-from-the-gallery"></a>Lägga till Palo Alto Networks – Admin UI från galleriet

För att konfigurera integreringen av Palo Alto Networks – Admin UI med Azure AD måste du lägga till Palo Alto Networks – Admin UI från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Palo Alto Networks – Admin UI från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. Skriv **Palo Alto Networks – Admin UI** i sökrutan, välj **Palo Alto Networks – Admin UI** från resultatpanelen och klicka sedan på **Lägg till** för att lägga till programmet.

     ![Palo Alto Networks – Admin UI i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Palo Alto Networks – Admin UI baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Palo Alto Networks – Admin UI upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Palo Alto Networks – Admin UI måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning med Palo Alto Networks – Admin UI](#configure-palo-alto-networks---admin-ui-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Palo Alto Networks – Admin UI-testanvändare](#create-palo-alto-networks---admin-ui-test-user)** – för att skapa en motsvarighet till Britta Simon i Palo Alto Networks – Admin UI som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

För att konfigurera enkel inloggning i Azure AD med Palo Alto Networks – Admin UI måste du utföra följande steg:

1. Välj **Enkel inloggning** på sidan för programintegrering av **Palo Alto Networks – Admin UI** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med domäner och URL:er för Palo Alto Networks – Admin UI](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<Customer Firewall FQDN>/php/login.php`

    b. I rutan **Identifierare** skriver du en URL med följande mönster: `https://<Customer Firewall FQDN>:443/SAML20/SP`

    c. Ange ACS-URL:en (Assertion Consumer Service) i textrutan **Svars-URL** i följande format: `https://<Customer Firewall FQDN>:443/SAML20/SP/ACS`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Palo Alto Networks – Admin UI-kundsupporten](https://support.paloaltonetworks.com/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Palo Alto Networks – Admin UI-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

   > [!NOTE]
   > Eftersom attributvärdena bara är exempel mappar du relevanta värden för *username* och *adminrole*. Det finns ett annat valfritt attribut, *accessdomain*, som används för att begränsa administratörsåtkomst till specifika virtuella datorer i brandväggen.
   >

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn |  Källattribut|
    | --- | --- |
    | användarnamn | user.userprincipalname |
    | adminrole | customadmin |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

    > [!NOTE]
    > Mer information om attributen finns i följande artiklar:
    > * [Profil för administrativ roll för Admin UI (adminrole)](https://www.paloaltonetworks.com/documentation/80/pan-os/pan-os/firewall-administration/manage-firewall-administrators/configure-an-admin-role-profile)
    > * [Enhetsåtkomstdomän för Admin UI (accessdomain)](https://www.paloaltonetworks.com/documentation/80/pan-os/web-interface-help/device/device-access-domain)

7. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar på **Hämta** för att hämta **Metadata-XML för federationen** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **Konfigurera Palo Alto Networks – Admin UI** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-palo-alto-networks---admin-ui-single-sign-on"></a>Konfigurera enkel inloggning för Palo Alto Networks – Admin UI

1. Öppna Palo Alto Networks-brandväggens administratörsgränssnitt som administratör i ett nytt fönster.

2. Välj fliken **Enhet**.

    ![Fliken Enhet](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Välj **SAML-identitetsprovider** och välj sedan **Importera** för att importera metadatafilen.

    ![Knapp för att importera metadatafil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Gör följande i fönstret **Importera serverprofil för SAML-identitetsprovider**:

    ![Fönstret Importera serverprofil för SAML-identitetsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp.png)

    a. Ange ett namn (t.ex. **AzureAD Admin UI**) i rutan **Profilnamn**.
    
    b. Välj **Bläddra** under **Metadata för identitetsprovider** och välj metadata.xml-filen som du hämtade tidigare från Azure-portalen.
    
    c. Avmarkera kryssrutan för att **verifiera certifikat för identitetsprovider**.
    
    d. Välj **OK**.
    
    e. Välj **Genomför** för att tillämpa konfigurationen på brandväggen.

5. Välj **SAML-identitetsprovider** i den vänstra rutan och välj sedan profilen för SAML-identitetsprovidern (t.ex. **AzureAD Admin UI**) som du skapade i föregående steg.

    ![Profil för SAML-identitetsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_idp_select.png)

6. Gör följande i fönstret **Serverprofil för SAML-identitetsprovider**:

    ![Fönstret Serverprofil för SAML-identitetsprovider](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_slo.png)
  
    a. I rutan för **SLO-URL för identitetsprovider** ersätter du den tidigare importerade SLO-URL:en med följande URL: `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`
  
    b. Välj **OK**.

7. Välj **Enhet** i Palo Alto Networks-brandväggens administratörsgränssnitt och välj sedan **Administratörsroller**.

8. Välj knappen **Lägg till**.

9. Ange ett namn för administratörsrollen (t.ex. **fwadmin**) i rutan **Namn** i fönstret **Profil för administratörsroll**. Namnet på administratörsrollen bör matcha namnet på attributet för SAML-administratörsrollen som skickades av identitetsprovidern. Namnet och värdet för administratörsrollen skapades i avsnittet **Användarattribut** på Azure-portalen.

    ![Konfigurera administratörsrollen för Palo Alto Networks](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_adminrole.png)
  
10. Välj **Enhet** i brandväggens administratörsgränssnitt och välj sedan **Autentiseringsprofil**.

11. Välj knappen **Lägg till**.

12. Gör följande i fönstret **Autentiseringsprofil**: 

    ![Fönstret Autentiseringsprofil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authentication_profile.png)

    a. Ange ett namn (t.ex. **AzureSAML_Admin_AuthProfile**) i rutan **Namn**.

    b. Välj **SAML** i listrutan **Typ**. 

    c. I listrutan **Serverprofil för IdP** väljer du lämplig serverprofil för SAML-identitetsprovidern (t.ex. **AzureAD Admin UI**).

    c. Markera kryssrutan **Aktivera enkel utloggning**.

    d. Ange attributnamnet (t.ex. **adminrole**) i rutan **Attribut för administratörsroll**.

    e. Välj fliken **Avancerat** och välj sedan **Lägg till** under **Listan Tillåt**.

    ![Knappen Lägg till på fliken Avancerat](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_allowlist.png)

    f. Markera kryssrutan **Alla** eller välj de användare och grupper som kan autentiseras med den här profilen.  
    När en användare autentiseras matchar brandväggen det associerade användarnamnet eller den associerade gruppen mot posterna i den här listan. Om du inte lägger till poster kan inga användare autentiseras.

    g. Välj **OK**.

13. Välj **Enhet** > **Installation** om du vill att administratörer ska kunna använda enkel inloggning med SAML via Azure. I fönstret **Installation** väljer du fliken **Hantering** och väljer sedan knappen **Inställningar** (”kugghjulsikon”) under **Autentiseringsinställningar**.

    ![Knappen Inställningar](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsetup.png)

14. Välj den SAML-autentiseringsprofil som du skapade i fönstret Autentiseringsprofil (t.ex. **AzureSAML_Admin_AuthProfile**).

    ![Fältet Autentiseringsprofil](./media/paloaltoadmin-tutorial/tutorial_paloaltoadmin_authsettings.png)

15. Välj **OK**.

16. Tillämpa konfigurationen genom att välja **Genomför**.

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

I det här avsnittet ska du göra det möjligt för Britta Simon att använda enkel inloggning i Azure genom att ge åtkomst till Palo Alto Networks – Admin UI.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Palo Alto Networks – Admin UI**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Palo Alto Networks – Admin UI** i programlistan.

    ![Länken för Palo Alto Networks – Admin UI i listan över program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-palo-alto-networks---admin-ui-test-user"></a>Skapa testanvändare för Palo Alto Networks – Admin UI

Palo Alto Networks – Admin UI stöder JIT-etablering (Just-In-Time) Om en användare inte redan finns skapas den automatiskt i systemet efter en lyckad autentisering. Ingen åtgärd krävs av dig för att skapa användaren.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för Palo Alto Networks – Admin UI på åtkomstpanelen bör du loggas in automatiskt i programmet för Palo Alto Networks – Admin UI som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)