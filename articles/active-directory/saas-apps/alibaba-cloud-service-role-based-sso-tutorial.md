---
title: 'Självstudier: Azure Active Directory-integrering med Alibaba Cloud Service (Role-based SSO) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Alibaba Molntjänsten (Role-based SSO).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8de2f7cb90e004673c59282a8023d55df364220a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65140674"
---
# <a name="tutorial-azure-active-directory-integration-with-alibaba-cloud-service-role-based-sso"></a>Självstudier: Azure Active Directory-integrering med Alibaba Cloud Service (Role-based SSO)

I den här självstudien får du lära dig hur du integrerar Alibaba Cloud Service (Role-based SSO) med Azure Active Directory (AD Azure).
Integrera Alibaba Cloud Service (Role-based SSO) med Azure AD ger dig följande fördelar:

* Du kan styra i Azure AD som har åtkomst till Alibaba Cloud Service (Role-based SSO).
* Du kan aktivera användarna att vara automatiskt inloggad till Molntjänsten för Alibaba (Role-based SSO) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med Alibaba Cloud Service (Role-based SSO), behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/)
* Alibaba Cloud Service (Role-based SSO) enkel inloggning aktiverat prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Alibaba Cloud Service (Role-based SSO) stöder **IDP** -initierad SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>Att lägga till Alibaba Cloud Service (Role-based SSO) från galleriet

För att konfigurera integrering av Alibaba Cloud Service (Role-based SSO) i Azure AD, som du behöver lägga till Alibaba Cloud Service (Role-based SSO) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Alibaba Cloud Service (Role-based SSO) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver **Alibaba Cloud Service (Role-based SSO)** väljer **Alibaba Cloud Service (Role-based SSO)** resultatet panelen klickar **Lägg till** för att lägga till den programmet.

    ![Alibaba Molntjänsten (Role-based SSO) i listan med resultat](common/search-new-app.png)

5. På den **Alibaba Cloud Service (Role-based SSO)** klickar du på **egenskaper** i navigeringsfönstret i vänster och kopiera den **objekt-ID** och spara den på din dator för senare användning.

    ![Egenskaper för config](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)
    
## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Alibaba Cloud Service (Role-based SSO) baserat på en testanvändare kallas **Britta Simon**.
För enkel inloggning ska fungera, måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Alibaba Cloud Service (Role-based SSO) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Alibaba Cloud Service (Role-based SSO), måste du utföra följande byggblock:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera rollbaserad enkel inloggning i Alibaba molntjänst](#configure-role-based-single-sign-on-in-alibaba-cloud-service)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera Alibaba Cloud Service (Role-based SSO) Single Sign-On](#configure-alibaba-cloud-service-role-based-sso-single-sign-on)**  – om du vill konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Alibaba Cloud Service (Role-based SSO) testanvändare](#create-alibaba-cloud-service-role-based-sso-test-user)**  – du har en motsvarighet för Britta Simon i Alibaba molntjänst (Role-based SSO) som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera Azure AD enkel inloggning med Alibaba Cloud Service (Role-based SSO):

1. I den [Azure-portalen](https://portal.azure.com/)på den **Alibaba Cloud Service (Role-based SSO)** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg om du har **metadatafilen för tjänstleverantör**:

    >[!NOTE]
    >Du får tjänstleverantör metadata från den här [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml)

    a. Klicka på **Ladda upp metadatafil**.

    ![image](common/upload-metadata.png)

    b. Klicka på **mappikonen** för att välja metadatafilen och klicka på **Ladda upp**.

    ![image](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp den **identifierare** och **svars-URL** värden hämta fylls i i Alibaba Cloud Service (Role-based SSO) avsnittet textrutan automatiskt:

    ![image](common/idp-intiated.png)

    > [!Note]
    > Om den **identifierare** och **svars-URL** värden inte hämta fylls i automatiskt, och Fyll i värden manuellt enligt dina behov.

5. Alibaba Cloud Service (Role-based SSO) program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på **redigera** ikonen för att öppna **användarattribut** dialogrutan.

    ![image](common/edit-attribute.png)

6. Förutom ovanstående Alibaba Cloud Service (Role-based SSO) program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn | Namnområde | Källattribut|
    | ---------------| ------------| --------------- |
    | Roll | https://www.aliyun.com/SAML-Role/Attribute | user.assignedroles |
    | RoleSessionName | https://www.aliyun.com/SAML-Role/Attribute | user.userprincipalname |

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) för att få veta hur du konfigurerar en **Roll** i Azure AD

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

7. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. På den **konfigurera Alibaba Cloud Service (Role-based SSO)** avsnittet, kopiera den lämpliga URL: er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>Konfigurera rollbaserad enkel inloggning i Alibaba molntjänst

1. Logga in på molnet Alibaba [RAM konsolen](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9) med hjälp av konto 1.

2. I vänster navigeringsfönstret väljer **SSO**.

3. På den **rollbaserad SSO** fliken **skapa IdP**.

4. På sidan som visas anger `AAD` i fältet IdP namn anger du en beskrivning i den **Obs** klickar **överför** överföra federation metadatafilen du laddade ner innan och klicka på  **OK**.

5. När IDP: N har skapats klickar du på **skapa RAM roll**.

6. I den **RAM rollnamn** ange `AADrole`väljer `AAD` från den **Välj IdP** nedrullningsbara listan och klicka på OK.

    >[!NOTE]
    >Du kan ge behörighet till rollen efter behov. När du har skapat IDP: N och motsvarande rollen, rekommenderar vi att du sparar ARNs för IDP: N och rollen för senare användning. Du kan hämta ARNs på informationssidan för IDP: N och rollsidan för information.

7. Associera rollen Alibaba molnet RAM-minne (AADrole) med Azure AD-användare (u2): Om du vill associera rollen RAM-minne med Azure AD-användare, måste du skapa en roll i Azure AD genom att följa dessa steg:

    a. Logga in på den [Azure AD Graph-testaren](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9).

    b. Klicka på **ändringsbehörigheter** att få behörigheter som krävs för att skapa en roll.

    ![Graph konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. Välj följande behörigheter i listan och klicka på **behörighet att ändra**, enligt följande bild.

    ![Graph konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >När behörigheterna har beviljats, logga du in på Grafutforskaren igen.

    d. På sidan Graph-testaren väljer **hämta** från den första listrutan och **beta** från den andra listan. Ange sedan `https://graph.microsoft.com/beta/servicePrincipals` i fältet bredvid den nedrullningsbara listor och klicka på **Kör fråga**.

    ![Graph konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >Om du använder flera kataloger kan du ange `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` i fältet i frågan.

    e. I den **svar förhandsversion** avsnittet, extrahera egenskapen appRoles från den huvudnamn för tjänsten för senare användning.

    ![Graph konfiguration](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >Du kan hitta egenskapen appRoles genom att ange `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` i fältet i frågan. Observera att den `objectID` är objekt-ID som du har kopierat från Azure AD **egenskaper** sidan.

    f. Gå tillbaka till Graph-testaren, ändra metod från **hämta** till **KORRIGERA**, klistra in följande innehåll till den **Begärandetext** , och klicka **Kör fråga** :
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > Den `value` är ARNs för IDP: N och rollen som du skapade i konsolen RAM-minne. Här kan du lägga till flera roller efter behov. Azure AD skickar värdet för dessa roller anspråksvärdet i SAML-svar. Men du kan bara lägga till nya roller efter den `msiam_access` ingår för patch-åtgärden utfördes. Om du vill jämna skapandeprocessen, rekommenderar vi att du använder en ID-generator, till exempel GUID-Generator för att generera ID: N i realtid.

    g. När tjänstens huvudnamn är uppdaterad med de nödvändiga rolltjänsterna, Lägg till rollen med Azure AD-användare (u2) genom att följa stegen i **tilldela Azure AD-testanvändare** avsnittet av självstudiekursen.

### <a name="configure-alibaba-cloud-service-role-based-sso-single-sign-on"></a>Konfigurera enkel inloggning för Alibaba Cloud-tjänsten (Role-based SSO)

Att konfigurera enkel inloggning på **Alibaba Cloud Service (Role-based SSO)** sida, som du behöver skicka de hämtade **XML-Metadata för Federation** och lämpliga kopieras URL: er från Azure portal för att [ Alibaba Cloud Service (Role-based SSO) supportteam](https://www.aliyun.com/service/). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I den **användarnamn** skriver `brittasimon@yourcompanydomain.extension`. Till exempel, BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Alibaba Cloud Service (Role-based SSO).

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**och välj sedan **Alibaba Cloud Service (Role-based SSO)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Alibaba Cloud Service (Role-based SSO)**.

    ![Länken Alibaba Cloud Service (Role-based SSO) i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. På den **användare och grupper** , Välj u2 från användarlistan och på **Välj**. Klicka sedan på **tilldela**.

    ![Test-config](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

6. Visa den tilldelade rollen och testa Alibaba Cloud Service (Role-based SSO).

    ![Test-config](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >När du har tilldelat användaren (u2), kopplas automatiskt skapade rollen till användaren. Om du har skapat flera roller kan behöva du koppla rätt roll till användaren vid behov. Om du vill implementera rollbaserad SSO från Azure AD till flera Alibaba molnkonton Upprepa föregående steg.

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>Skapa testanvändare Alibaba Cloud Service (Role-based SSO)

I det här avsnittet skapar du en användare som kallas Britta Simon i Alibaba molntjänst (Role-based SSO). Arbeta med [Alibaba Cloud Service (Role-based SSO) supportteam](https://www.aliyun.com/service/) att lägga till användare i Alibaba Cloud Service (Role-based SSO)-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

När de föregående konfigurationerna har slutförts testar du Alibaba Cloud Service (Role-based SSO) genom att följa dessa steg:

1. I Azure-portalen går du till den **Alibaba Cloud Service (Role-based SSO)** väljer **enkel inloggning**, och klicka på **Test**.

    ![Test-config](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. Klicka på **Logga in aktuell användare**.

    ![Test-config](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. På valsidan av konto väljer du u2.

    ![Test-config](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. Följande sida visas som anger att roll-baserad enkel inloggning har lyckats.

    ![Test-config](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

