---
title: 'Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e49bc5a468777197eaf88a492566a606e7b9f93
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961881"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS)

I den här självstudien lär du dig att integrera Amazon Web Services (AWS) med Azure Active Directory (AD Azure).
Integreringen av Amazon Web Services (AWS) med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Amazon Web Services (AWS).
* Du kan göra så att dina användare automatiskt loggas in på Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser enligt nedan.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden tar Azure AD bort värdet för **#** och skickar det korrekta värdet `https://signin.aws.amazon.com/saml` som publik-URL i SAML-token.

**Vi rekommenderar att du använder den här metoden av följande skäl:**

a. Varje program ger dig det unika X509-certifikatet. Därmed kan alla instanser ha olika förfallodatum för certifikat, och du kan hantera detta för enskilt AWS-konto. Övergripande certifikatförnyelse blir enkelt i det här fallet.

b. Du kan aktivera användarförsörjning med AWS-app i Azure AD så hämtar vår tjänst alla roller från det AWS-kontot. Du behöver inte manuellt lägga till eller uppdatera AWS-roller på appen.

c. Du kan tilldela appägaren enskilt för appen, som kan hantera appen direkt i Azure AD.

> [!Note]
> Kontrollera att du enbart använder galleriappen

## <a name="prerequisites"></a>Nödvändiga komponenter

För att konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS)-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Om du vill integrera flera AWS-konton till ett Azure-konto för enkel inloggning läser du [den här](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artikeln.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)**, i den vänstra navigeringspanelen, klickar du på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Amazon Web Services (AWS)**, väljer **Amazon Web Services (AWS)** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Amazon Web Services (AWS) i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med Amazon Web Services (AWS) baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Amazon Web Services (AWS) upprättas.

För att konfigurera och testa enkel inloggning med Azure AD med Amazon Web Services (AWS) behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Amazon Web Services (AWS)-testanvändare](#create-amazon-web-services-aws-test-user)** – för att ha en motsvarighet för Britta Simon i Amazon Web Services (AWS) som är länkad till en Azure AD-representation av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Amazon Web Services (AWS):

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Amazon Web Services (AWS)**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.  Klicka på **Spara**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. När du konfigurerar mer än en instans ska du ange identifierarvärdet. Från och med andra instansen anger du identifierarvärdet i följande format. Använd ett **#**-tecken för att ange ett unikt SPN-värde.

    `https://signin.aws.amazon.com/saml#2`

    ![Information om enkel inloggning med Amazon Web Services (AWS)-domäner och -URL:er](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Amazon Web Services (AWS)-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn  | Källattribut  | Namnområde |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Roll            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  https://aws.amazon.com/SAML/Attributes |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. I textrutan **Namnrymd** anger du det namnrymdsvärde som visas för den raden.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

8. Klicka på **Ladda ned** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** när du ska ladda ned **Federation Metadata XML** från de angivna alternativen enligt dina behov och spara det på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

9. I avsnittet **Konfigurera Amazon Web Services (AWS)** kopierar du lämpliga URL:er enligt dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-amazon-web-services-aws-single-sign-on"></a>Konfigurera enkel inloggning för Amazon Web Services (AWS)

1. I ett annat webbläsarfönster loggar du in på din Amazon Web Services (AWS)-företagsplats som administratör.

2. Klicka på **AWS Home**.

    ![Konfigurera start för enkel inloggning][11]

3. Klicka på **Identitets- och åtkomsthantering**.

    ![Konfigurera identitet för enkel inloggning][12]

4. Klicka på **Identitetsprovidrar** och sedan på **Skapa provider**.

    ![Konfigurera provider för enkel inloggning][13]

5. På dialogsidan **Konfigurera provider** utför du följande steg:

    ![Dialogruta för att konfigurera enkel inloggning][14]

    a. För **Providertyp** väljer du **SAML**.

    b. I textrutan **Providernamn** skriver du ett providernamn (till exempel: *WAAD*).

    c. För att ladda upp den nedladdade **metadatafilen** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **Nästa steg**.

6. På dialogsidan **Verifiera providerinformation** klickar du på **Skapa**.

    ![Konfigurera verifiering för enkel inloggning][15]

7. Klicka på **Roller** och sedan på **Skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Konfigurera förtroende för enkel inloggning][19]

    a. Välj **SAML 2.0-federation** under **Select type of trusted entity** (Välj typ av betrodd entitet).

    b. I avsnittet **Välj en SAML 2.0-provider** väljer du den **SAML-provider** som du skapade tidigare (till exempel: *WAAD*)

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Klicka på **Nästa: Behörigheter**.

9. I dialogrutan **Attach Permissions Policies** (Koppla behörighetsprinciper) kopplar du lämplig princip enligt din organisation. Klicka på **Nästa: Granskning**.  

    ![Konfigurera princip för enkel inloggning][33]

10. I dialogrutan **Granskning** utför du följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. I textrutan **Rollnamn** anger du ditt rollnamn.

    b. I textrutan **Rollbeskrivning** anger du beskrivningen.

    c. Klicka på **Skapa roll**.

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

11. Använd autentiseringsuppgifter för AWS-tjänstkontot för att hämta rollerna från AWS-kontot i användarförsörjning för Azure AD. För detta öppnar du AWS-konsolstarten.

12. Klicka på **Tjänster** -> **Säkerhet, identitet och efterlevnad** -> **IAM**.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. Välj fliken **Principer** i IAM-avsnittet.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Skapa en ny princip genom att klicka på **Skapa princip** för hämtning av rollerna från AWS-konto i användarförsörjning för Azure AD.

    ![Skapar ny princip](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Skapa en egen princip för att hämta alla roller från AWS-konton genom att utföra följande steg:

    ![Skapar ny princip](./media/amazon-web-service-tutorial/policy1.png)

    a. I avsnittet **”Skapa princip”** klickar du på fliken **”JSON”**.

    b. I principdokumentet lägger du till nedanstående JSON.

    ```

    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",

    "Action": [

    "iam:ListRoles"

    ],

    "Resource": "*"

    }

    ]

    }

    ```

    c. Klicka på knappen för att **granska princip** för att verifiera principen.

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy5.png)

16. Definiera den **nya principen** genom att utföra följande steg:

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy2.png)

    a. Ange **principnamnet** som **AzureAD_SSOUserRole_Policy**.

    b. Du kan ange **beskrivningen** av principen som **Den här principen tillåter hämtning av rollerna från AWS-konton**.

    c. Klicka på knappen **”Skapa policy”**.

17. Skapa ett nytt användarkonto i AWS IAM-tjänsten genom att utföra följande steg:

    a. Klicka på navigering för **Användare** i AWS IAM-konsolen.

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy3.png)

    b. Klicka på knappen **Lägg till användare** för att skapa en ny användare.

    ![Lägga till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I avsnittet **Lägg till användare** utför du följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser1.png)

    * Ange användarnamnet som **AzureADRoleManager**.

    * I Åtkomsttyp väljer du alternativet **Programmatisk åtkomst**. På så sätt kan användaren anropa API:erna och hämta rollerna från AWS-kontot.

    * Klicka på knappen **Nästa behörigheter** i det nedre högra hörnet.

18. Skapa nu en ny princip för den här användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser2.png)

    a. Klicka på knappen **Attach existing policies directly** (Koppla befintliga principer direkt).

    b. Sök efter den nyligen skapade principen i filteravsnittet **AzureAD_SSOUserRole_Policy**.

    c. Markera **principen** och klicka sedan på knappen **Nästa: Granskning**.

19. Granska principen till den kopplade användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamnet, åtkomsttypen och den princip som är mappad till användaren.

    b. Klicka på knappen **Skapa användare** i det nedre högra hörnet för att skapa användaren.

20. Ladda ned autentiseringsuppgifter för en användare genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användarens **åtkomstnyckel-ID** och **hemliga åtkomstnyckel**.

    b. Ange de här autentiseringsuppgifterna i avsnittet för användarförsörjning för Azure AD-användare för att hämta rollerna från AWS-konsolen.

    c. Klicka knappen **Stäng** längst ned.

21. Gå till avsnittet **Användarförsörjning** för Amazon Web Services-appen i Azure AD-hanteringsportalen.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning.png)

22. Ange **åtkomstnyckel** och **hemlighet** i fälten **Klienthemlighet** respektive **Hemlig token**.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange AWS-användaråtkomstnyckeln i fältet **clientsecret**.

    b. Ange AWS-användarhemligheten i fältet **Hemlig token**.

    c. Klicka på knappen **Testanslutning** så bör du kunna testa den här anslutningen.

    d. Spara inställningen genom att klicka på knappen **Spara** längst upp.

23. Se nu till att ange etableringsstatus till **På** i avsnittet Inställningar genom att växla på, och klicka sedan på knappen **Spara** längst upp.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning2.png)

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

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Amazon Web Services (AWS).

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Amazon Web Services (AWS)**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan över program skriver och väljer du **Amazon Web Services (AWS)**.

    ![Amazon Web Services (AWS)-länk i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett rollvärde i SAML-försäkran väljer du i dialogrutan **Välj roll** lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-amazon-web-services-aws-test-user"></a>Skapa Amazon Web Services (AWS)-testanvändare

Målet med det här avsnittet är att skapa en användare som heter Britta Simon i Amazon Web Services (AWS). För Amazon Web Services (AWS) krävs det inte att en användare skapas i deras system för enkel inloggning, så du behöver inte göra någonting här.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Amazon Web Services (AWS)-panelen i åtkomstpanelen bör du automatiskt loggas in på Amazon Web Services (AWS) som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Kända problem

 * I avsnittet **Etablering** visar underavsnittet **Mappningar** meddelandet ”Läser in...” och visar aldrig attributmappningarna. Det enda arbetsflöde för etablering som stöds i dag är import av roller från AWS till Azure AD för val under användar-/grupptilldelning. Attributmappningarna för det här är förinställda och kan inte konfigureras.
 
 * Avsnittet **Etablering** stöder endast inmatning av en uppsättning autentiseringsuppgifter för en AWS-klientorganisation i taget. Alla importerade roller skrivs till egenskapen appRoles för Azure AD [servicePrincipal-objektet](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) för AWS-klientorganisationen. Flera AWS-klientorganisationer (som representeras av servicePrincipals) kan läggas till i Azure AD från galleriet för etablering, men det finns ett känt problem med att det inte går att automatiskt skriva alla importerade roller från flera AWS-servicePrincipals som används för etablering till den enskilda servicePrincipal som används för enkel inloggning. Som en tillfällig lösning kan [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) användas för att extrahera alla appRoles som importeras till varje AWS-servicePrincipal där etablering har konfigurerats. De här rollsträngarna kan därefter läggas till i den AWS-servicePrincipal där enkel inloggning har konfigurerats.

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
