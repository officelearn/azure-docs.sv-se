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
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d0ed4cd677392a6a48038eed80c36664ccef9494
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879885"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Självstudier: Integrera Amazon Web Services (AWS) med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Amazon Web Services (AWS) med Azure Active Directory (Azure AD). När du integrerar AWS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AWS.
* Gör det möjligt för användarna att logga in automatiskt till AWS med sina Azure AD-konton.
* Hantera dina konton på en central plats, Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

![Diagram över Azure AD-och AWS-relationer](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser. Exempel:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden tar Azure AD bort värdet **#** och skickar rätt värde `https://signin.aws.amazon.com/saml` som mål grupps-URL i SAML-token.

Vi rekommenderar den här metoden av följande orsaker:

- Varje program ger dig ett unikt X509-certifikat. Varje instans av en AWS-serverinstans kan sedan ha ett annat förfallo datum för certifikatet, som kan hanteras på ett enskilt AWS-konto. Övergripande certifikat förnyelse är enklare i det här fallet.

- Du kan aktivera användar etablering med en AWS-app i Azure AD och sedan hämtar tjänsten alla roller från det AWS-kontot. Du behöver inte manuellt lägga till eller uppdatera AWS-roller på appen.

- Du kan tilldela program ägaren individuellt för appen. Den här personen kan hantera appen direkt i Azure AD.

> [!Note]
> Se till att du endast använder ett galleri program.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* En AWS-aktiverad (SSO)-prenumeration med enkel inloggning.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. AWS stöder **SP-och IDP** -initierad SSO.

## <a name="add-aws-from-the-gallery"></a>Lägg till AWS från galleriet

Om du vill konfigurera integreringen av AWS i Azure AD måste du lägga till AWS från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I den vänstra rutan väljer du tjänsten **Azure Active Directory** .
1. Gå till **Företagsprogram** och välj sedan **Alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Amazon Web Services (AWS)** i sökrutan.
1. Välj **Amazon Web Services (AWS)** i resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Konfigurera och testa Azure AD SSO med AWS med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länkad relation mellan en Azure AD-användare och den relaterade användaren i AWS.

Om du vill konfigurera och testa Azure AD SSO med AWS, slutför du följande Bygg stenar:

1. **Konfigurera Azure AD SSO** så att användarna kan använda den här funktionen.
2. **Konfigurera AWS** för att konfigurera SSO-inställningar på program sidan.
3. **Skapa en Azure AD** -testanvändare för att testa Azure AD SSO med B. Simon.
4. **Tilldela Azure AD** -testanvändaren för att aktivera B. Simon att använda Azure AD SSO.
5. **Skapa en AWS** -testanvändare som ska ha en motsvarighet till B. Simon i AWS som är länkad till Azure AD-representation av användaren.
6. **Testa SSO** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **Amazon Web Services (AWS)** program integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** väljer du Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Skärm bild som visar hur du konfigurerar enkel inloggning med SAML-sidan med Penn ikonen markerad](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** är programmet förkonfigurerat och de nödvändiga URL: erna är redan ifyllda med Azure. Användaren måste spara konfigurationen genom att välja **Spara**.

5. Ange ett ID-värde när du konfigurerar fler än en instans. Använd följande format från den andra instansen, inklusive ett **#** tecken för att ange ett unikt SPN-värde.

    `https://signin.aws.amazon.com/saml#2`

6. AWS-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Välj Penn ikonen för att öppna dialog rutan användarattribut.

    ![Skärm bild av dialog rutan användarattribut med Penn ikonen markerad](common/edit-attribute.png)

7. Förutom föregående attribut förväntar sig AWS-programmet fler attribut att skickas tillbaka i SAML-svaret. I avsnittet **användar anspråk** i dialog rutan användarattribut, utför följande steg för att lägga till SAML-token-attributet.

    | Namn  | Källattribut  | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  https://aws.amazon.com/SAML/Attributes |

    a. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![Skärm bild av avsnittet användar anspråk, med Lägg till nytt anspråk och spara markerat](common/new-save-attribute.png)

    ![Skärm bild av dialog rutan hantera användar anspråk](common/new-attribute-details.png)

    b. I **namn**anger du det attributnamn som visas för raden.

    c. I **namn område**anger du det namn områdes värde som visas för den raden.

    d. I **källa**väljer du **attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Välj **OK**.

    g. Välj **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata**. Välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Skärm bild av avsnittet SAML-signerings certifikat med hämtning markerat](common/metadataxml.png)

1. I avsnittet **konfigurera Amazon Web Services (AWS)** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Skärm bild av avsnittet Konfigurera Amazon Web Services (AWS) med konfigurations-URL: er markerade](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>Konfigurera AWS

1. Logga in på din AWS företags webbplats som administratör i ett annat webbläsarfönster.

2. Välj **AWS Home**.

    ![Skärm bild av AWS företags webbplats med AWS start ikon markerad][11]

3. Välj **identitets-och åtkomst hantering**.

    ![Skärm bild av sidan AWS Services med IAM markerat][12]

4. Välj **identitets leverantörer** > **skapa Provider**.

    ![Skärm bild av IAM-sidan med identitets leverantörer och skapa Provider markerad][13]

5. Utför följande steg på sidan **Konfigurera Provider** :

    ![Skärm bild av konfigurera Provider][14]

    a. För **typ av Provider**väljer du **SAML**.

    b. För **Providernamn**anger du ett providernamn (till exempel: *WAAD*).

    c. Om du vill ladda upp den hämtade **metadatafilen** från Azure Portal väljer du **Välj fil**.

    d. Välj **Nästa steg**.

6. På sidan **Verifiera leverantörs information** väljer du **skapa**.

    ![Skärm bild av verifiera leverantörs information med skapa markerat][15]

7. Välj **roller** > **skapa roll**.

    ![Skärm bild av roll Sidan][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Skärm bild av sidan Skapa roll][19]

    a. Under **Välj typ av betrodd entitet**väljer du **SAML 2,0 Federation**.

    b. Under **Välj en saml 2,0-Provider**väljer du den **SAML-Provider** som du skapade tidigare (till exempel: *WAAD*).

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Välj **Nästa: Behörigheter**.

9. I dialog rutan **bifoga behörighets principer** bifogar du lämplig princip per organisation. Välj **sedan Nästa: Granskning**.  

    ![Skärm bild av dialog rutan bifoga behörighets princip][33]

10. I dialog rutan **Granska** utför du följande steg:

    ![Skärm bild av dialog rutan granska][34]

    a. I **roll namn**anger du ditt roll namn.

    b. Ange beskrivningen i **roll Beskrivning**.

    c. Välj **skapa roll**.

    d. Skapa så många roller som du behöver och mappa dem till identitets leverantören.

11. Använd AWS tjänst konto uppgifter för att hämta rollerna från AWS-kontot i användar etableringen för Azure AD. För detta öppnar du AWS-konsolstarten.

12. Välj **tjänster**. Under **säkerhet, identitet & efterlevnad**väljer du **IAM**.

    ![Skärm bild av AWS-konsolens start sida med tjänster och IAM markerat](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. I avsnittet IAM väljer du **principer**.

    ![Skärm bild av IAM-avsnittet med principer markerade](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Skapa en ny princip genom att välja **Skapa princip** för att hämta rollerna från AWS-kontot i användar etableringen för Azure AD.

    ![Skärm bild av sidan Skapa roll med skapa princip markerad](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Skapa en egen princip för att hämta alla roller från AWS-konton.

    ![Skärm bild av sidan Skapa princip med JSON markerat](./media/amazon-web-service-tutorial/policy1.png)

    a. I **Skapa princip**väljer du fliken **JSON** .

    b. I princip dokumentet lägger du till följande JSON:

    ```json
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

    c. Välj **Granska princip** för att validera principen.

    ![Skärm bild av sidan Skapa princip](./media/amazon-web-service-tutorial/policy5.png)

16. Definiera den nya principen.

    ![Skärm bild av sidan Skapa princip med namn-och beskrivnings fälten markerade](./media/amazon-web-service-tutorial/policy2.png)

    a. Som **namn**anger du **AzureAD_SSOUserRole_Policy**.

    b. I **Beskrivning**anger **du den här principen för att hämta ROLLerna från AWS-konton**.

    c. Välj **Skapa princip**.

17. Skapa ett nytt användar konto i AWS IAM-tjänsten.

    a. I AWS IAM-konsolen väljer **du användare**.

    ![Skärm bild av AWS IAM-konsolen med användare markerade](./media/amazon-web-service-tutorial/policy3.png)

    b. Om du vill skapa en ny användare väljer du **Lägg till användare**.

    ![Skärm bild av knappen Lägg till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I avsnittet **Lägg till användare** :

    ![Skärm bild av sidan Lägg till användare med användar namn och åtkomst typ markerad](./media/amazon-web-service-tutorial/adduser1.png)

    * Ange användarnamnet som **AzureADRoleManager**.

    * För åtkomst typen väljer du **programmerings åtkomst**. På så sätt kan användaren anropa API: erna och hämta rollerna från AWS-kontot.

    * Välj **Nästa behörigheter**.

18. Skapa en ny princip för den här användaren.

    ![Skärm bild av Lägg till användare](./media/amazon-web-service-tutorial/adduser2.png)

    a. Välj **koppla befintliga principer direkt**.

    b. Sök efter den nyligen skapade principen i filteravsnittet **AzureAD_SSOUserRole_Policy**.

    c. Välj principen och välj **sedan Nästa: Granskning**.

19. Granska principen för den anslutna användaren.

    ![Skärm bild av sidan Lägg till användare med skapa användare markerad](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamnet, åtkomsttypen och den princip som är mappad till användaren.

    b. Välj **skapa användare**.

20. Hämta användarens autentiseringsuppgifter.

    ![Skärm bild av Lägg till användare](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användarens **åtkomstnyckel-ID** och **hemliga åtkomstnyckel**.

    b. Ange autentiseringsuppgifterna i avsnittet användar etablering i Azure AD för att hämta rollerna från AWS-konsolen.

    c. Välj **Stäng**.

21. I AWS-appen i Azure AD-hanteringsportalen går du till **etablering**.

    ![Skärm bild av AWS-appen med etablering markerad](./media/amazon-web-service-tutorial/provisioning.png)

22. Ange åtkomst nyckeln och hemligheten i fälten **clientsecret** och **hemlig token** .

    ![Skärm bild av dialog rutan autentiseringsuppgifter för administratörer](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange AWS-användaråtkomstnyckeln i fältet **clientsecret**.

    b. Ange AWS-användarhemligheten i fältet **Hemlig token**.

    c. Välj **test anslutning**.

    d. Spara inställningen genom att välja **Spara**.

23. I avsnittet **Inställningar** , för **etablerings status**, väljer du **på**. Välj sedan **Spara**.

    ![Skärm bild av avsnittet inställningar med på markerad](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en test användare, B. Simon, i Azure Portal.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory** > **användare** > **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   
   a. I **Namn**-fältet skriver du `B.Simon`.  
   b. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.   
   c. Välj **Visa lösen ord**och skriv ned det. Välj sedan **Create** (Skapa).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet aktiverar du B. Simon för att använda Azure SSO genom att bevilja åtkomst till AWS.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Amazon Web Services (AWS)** .
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Skärm bild av avsnittet hantera med användare och grupper markerade](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I dialog rutan **Lägg till tilldelning** väljer **du användare och grupper**.

    ![Skärm bild av Lägg till användare](common/add-assign-user.png)

1. Välj **B. Simon**i dialog rutan **användare och grupper** . Välj sedan **Välj**.
1. Om du förväntar dig ett roll värde i SAML-intyget väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** . Välj sedan **Välj**.
1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

När du väljer panelen AWS på åtkomst panelen, bör du loggas in automatiskt på den AWS som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Kända problem

 * I avsnittet om **etablering** visas en "inläsning..." i underavsnittet **mappningar** . meddelande, och visar aldrig mappningar för attribut. Det enda etablerings arbets flödet som stöds idag är att importera roller från AWS till Azure AD för val under en användar-eller grupp tilldelning. Attributens mappningar för detta är för bestämda och kan inte konfigureras.
 
 * Avsnittet **Etablering** stöder endast inmatning av en uppsättning autentiseringsuppgifter för en AWS-klientorganisation i taget. Alla importerade roller skrivs till `appRoles` egenskapen för Azure AD [ `servicePrincipal` -objektet](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) för AWS-klienten. 
 
   Flera AWS-klienter (som representeras av `servicePrincipals`) kan läggas till i Azure AD från galleriet för etablering. Det finns dock ett känt problem, men det går inte att automatiskt skriva alla importerade roller från de flera AWS `servicePrincipals` som används för etablering i den enda `servicePrincipal` som används för SSO. 
   
   Som en lösning kan du använda [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) för att extrahera alla `appRoles` importerade till varje AWS `servicePrincipal` där etableringen har kon figurer ATS. Du kan senare lägga till de här roll strängarna `servicePrincipal` i AWS där SSO är konfigurerat.

## <a name="additional-resources"></a>Ytterligare resurser

- [Självstudier för att integrera SaaS-program med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

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
