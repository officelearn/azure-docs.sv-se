---
title: 'Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fe362eb90793c831fc48d6fdc5a871c12e1b560
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442762"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Självstudier: Integrera Amazon Web Services (AWS) med Azure Active Directory

I de här självstudierna lär du dig att integrera Amazon Web Services (AWS) med Azure Active Directory (AD Azure). När du integrerar Amazon Web Services (AWS) med Azure AD, kan du:

* Styr i Azure AD som har åtkomst till Amazon Web Services (AWS).
* Ge dina användare att automatiskt inloggad till Amazon Web Services (AWS) med sina Azure AD-konton.
* Hantera konton på en central plats – Azure portal.

Läs mer om integrering av SaaS-app med Azure AD i [vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser enligt nedan.

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden tar Azure AD bort värdet för **#** och skickar det korrekta värdet `https://signin.aws.amazon.com/saml` som publik-URL i SAML-token.

**Vi rekommenderar att du använder den här metoden av följande skäl:**

a. Varje program får du ett unikt X509 certifikat. Varje instans av AWS-App-instansen får sedan ett annat certifikat förfallodatum som kan hanteras på AWS-konto individuellt. Övergripande blir förnya certifikatet enklare i det här fallet.

b. Du kan aktivera användarförsörjning med AWS-app i Azure AD så hämtar vår tjänst alla roller från det AWS-kontot. Du behöver inte manuellt lägga till eller uppdatera AWS-roller på appen.

c. Du kan tilldela appägaren enskilt för appen, som kan hantera appen direkt i Azure AD.

> [!Note]
> Kontrollera att du enbart använder galleriappen

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få en [kostnadsfritt konto](https://azure.microsoft.com/free/).
* Aktiverat prenumeration Amazon Web Services (AWS) enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien, konfigurera och testa Azure AD enkel inloggning i en testmiljö. Har stöd för Amazon Web Services (AWS) **SP och IDP** -initierad SSO.

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigeringsfönstret, väljer den **Azure Active Directory** service.
1. Gå till **företagsprogram** och välj sedan **alla program**.
1. Om du vill lägga till nytt program, Välj **nytt program**.
1. I den **Lägg till från galleriet** Skriv **Amazon Web Services (AWS)** i sökrutan.
1. Välj **Amazon Web Services (AWS)** från resultaten panelen och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klient.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med Amazon Web Services (AWS) med en testanvändare kallas **B.Simon**. För enkel inloggning ska fungera, måste du upprätta en länk förhållandet mellan en Azure AD-användare och den relaterade användaren i Amazon Web Services (AWS).

Om du vill konfigurera och testa Azure AD SSO med Amazon Web Services (AWS), utför du följande byggblock:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)**  vill tillåta att användarna använda den här funktionen.
2. **[Konfigurera Amazon Web Services (AWS)](#configure-amazon-web-services-aws)**  att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  att testa Azure AD enkel inloggning med B.Simon.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  att aktivera B.Simon att använda Azure AD enkel inloggning.
5. **[Skapa testanvändare i Amazon Web Services (AWS)](#create-amazon-web-services-aws-test-user)**  har en motsvarighet för B.Simon i Amazon Web Services (AWS) och som är länkad till en Azure AD-representation av användaren.
6. **[Testa SSO](#test-sso)**  att kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ dessa steg om du vill aktivera enkel inloggning för Azure AD i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)på den **Amazon Web Services (AWS)** programsidan integration, hitta den **hantera** och väljer **enkelinloggning**.
1. På den **väljer du en metod för enkel inloggning** väljer **SAML**.
1. På den **ange in enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **SAML grundkonfiguration** att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På den **SAML grundkonfiguration** avsnittet programmet är förkonfigurerad och nödvändiga URL: er är redan ifyllda på förhand med Azure. Användaren behöver för att spara konfigurationen genom att klicka på den **spara** knappen.

5. När du konfigurerar mer än en instans ska du ange identifierarvärdet. Från och med andra instansen anger du identifierarvärdet i följande format. Använd ett **#** -tecken för att ange ett unikt SPN-värde.

    `https://signin.aws.amazon.com/saml#2`

6. Amazon Web Services (AWS) program som förväntar SAML-intyg i ett visst format, vilket kräver att du kan lägga till anpassade attributmappningar i SAML-tokenattribut konfigurationen. I följande skärmbild visas listan över standardattribut. Klicka på ikonen **Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

7. Förutom ovanstående Amazon Web Services (AWS) program som förväntar få fler attribut som ska skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Namn  | Källattribut  | Namnrymd |
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

1. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** avsnittet, hitta **XML-Metadata för Federation** och välj **hämta** att hämta certifikatet och spara den på din dator.

   ![Länk för hämtning av certifikat](common/metadataxml.png)

1. På den **ange upp Amazon Web Services (AWS)** avsnittet, kopiera den lämpliga URL: er efter behov.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-amazon-web-services-aws"></a>Konfigurera Amazon Web Services (AWS)

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

    a. I avsnittet **”Skapa princip”** klickar du på fliken **”JSON”** .

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

    c. Klicka på knappen **”Skapa policy”** .

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

I det här avsnittet skapar du en användare i Azure-portalen kallas B.Simon.

1. På menyn till vänster i Azure-portalen väljer du **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I den **användaren** egenskaper, Följ dessa steg:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I den **användarnamn** fältet, anger du den username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska du aktivera B.Simon att använda Azure enkel inloggning genom att ge åtkomst till Amazon Web Services (AWS).

1. I Azure-portalen väljer du **företagsprogram**, och välj sedan **alla program**.
1. I listan med program väljer **Amazon Web Services (AWS)** .
1. Appens översiktssidan, hitta den **hantera** och väljer **användare och grupper**.

   ![Länken ”användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I den **användare och grupper** dialogrutan **B.Simon** från listan över användare klickar på **Välj** längst ned på skärmen.
1. Om du förväntar dig något rollvärde i SAML-försäkran i den **Välj roll** dialogrutan Välj rätt roll för användaren i listan och klicka sedan på den **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-amazon-web-services-aws-test-user"></a>Skapa Amazon Web Services (AWS)-testanvändare

Målet med det här avsnittet är att skapa en användare som kallas B.Simon i Amazon Web Services (AWS). För Amazon Web Services (AWS) krävs det inte att en användare skapas i deras system för enkel inloggning, så du behöver inte göra någonting här.

### <a name="test-sso"></a>Testa enkel inloggning

När du väljer panelen Amazon Web Services (AWS) i åtkomstpanelen, bör du vara loggas in automatiskt till i Amazon Web Services (AWS) som du ställer in enkel inloggning. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

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