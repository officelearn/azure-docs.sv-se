---
title: 'Självstudiekurs: Azure Active Directory-integrering med Amazon Web Services (AWS) för att ansluta flera konton | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure AD och flera konton för Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: edd54352b1328c95ae2c3e466003b64eaa0fcfde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77367995"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Självstudiekurs: Azure Active Directory-integrering med flera AWS-konton (Amazon Web Services)

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD) med flera konton i Amazon Web Services (AWS).

Integreringen av Amazon Web Services (AWS) med Azure AD medför följande fördelar:

- Du kan i Azure AD styra vem som har åtkomst till Amazon Web Services (AWS).
- Du kan göra det möjligt för användarna att automatiskt logga in på Amazon Web Services (AWS) (Enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om Integrering av SaaS-appar med Azure AD läser du [vad som är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

>[!NOTE]
>Observera att det inte är vår rekommenderade metod att ansluta en AWS-app till alla dina AWS-konton. I stället rekommenderar vi att du använder [den här](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial) metoden för att konfigurera flera instanser av AWS-konto till flera instanser av AWS-appar i Azure AD.

**Observera att vi inte rekommenderar att du använder denna metod av följande skäl:**

* Du måste använda Microsoft Graph Explorer-metoden för att korrigera alla roller i appen. Vi rekommenderar inte att du använder manifestfilen.

* Vi har sett kunder rapportera att efter att ha lagt till ~ 1200 app roller för en enda AWS app, någon åtgärd på appen började kasta fel relaterade till storlek. Det finns en hård storleksgräns för programobjektet.

* Du måste manuellt uppdatera rollen när rollerna läggs till i något av kontona, vilket är en ersätt-metod och inte lägg till tyvärr. Även om dina konton växer då detta blir n x n relation med konton och roller.

* Alla AWS-konton kommer att använda samma Federation Metadata XML-fil och vid tidpunkten för certifikatet rollover måste du köra denna massiva övning för att uppdatera certifikatet på alla AWS-konton samtidigt

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Amazon Web Services (AWS)-prenumeration med enkel inloggning aktiverat

> [!NOTE]
> Vi rekommenderar att du inte använder en produktionsmiljö för att testa stegen i den här självstudien.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Amazon Web Services (AWS) stöder **SP- och IDP**-initierad enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Lägga till Amazon Web Services (AWS) från galleriet

För att konfigurera integreringen av Amazon Web Services (AWS) med Azure AD måste du lägga till Amazon Web Services (AWS) från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Amazon Web Services (AWS)**, väljer **Amazon Web Services (AWS)** från resultatpanelen och klickar sedan på knappen **Lägg till** för att lägga till programmet.

     ![Amazon Web Services (AWS) i resultatlistan](common/search-new-app.png)

5. När programmet har lagts till går du till sidan **Egenskaper** och **kopierar objekt-ID.**

    ![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som heter "Britta Simon".

För att enkel inloggning ska fungera måste Azure AD veta vad motpartsanvändaren i Amazon Web Services (AWS) är för en användare i Azure AD. Med andra ord måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Amazon Web Services (AWS) upprättas.

I Amazon Web Services (AWS) tilldelar du värdet för **användarnamnet** i Azure AD som värdet för **användarnamnet** för att upprätta länkrelationen.

För att konfigurera och testa enkel inloggning med Azure AD med Amazon Web Services (AWS) behöver du utföra följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Amazon Web Services (AWS)](#configure-amazon-web-services-aws-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du azure AD-enkel inloggning i Azure-portalen och konfigurerar enkel inloggning i ditt AMAZON Web Services (AWS)-program.

**Utför följande steg för att konfigurera enkel inloggning i Azure AD med Amazon Web Services (AWS):**

1. På [Azure-portalen](https://portal.azure.com/) går du till sidan för **Amazon Web Services (AWS)**-programintegrering och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** behöver användaren inte utföra några steg eftersom appen redan är förintegrerad med Azure.

    ![image](common/preintegrated.png)

5. Amazon Web Services (AWS)-programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Knappen **Redigera** för att öppna dialogrutan **Användarattribut & anspråk.**

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:

    | Namn  | Källattribut  | Namnområde |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Roll            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”ange ett värde mellan 900 sekunder (15 minuter) och 43 200 sekunder (12 timmar)” |  https://aws.amazon.com/SAML/Attributes |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Skriv namnområdesvärdet som visas för den raden i textrutan **Namnområde.**

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Ok**

    g. Klicka på **Spara**.

7. Klicka på **Hämta** i avsnittet **SAML-signeringscertifikat** på sidan **Konfigurera enkel inloggning med SAML** **och** spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

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

    b. Skriv ett providernamn i textrutan **Providernamn** (till exempel *WAAD*).

    c. För att ladda upp den nedladdade **metadatafilen** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **Nästa steg**.

6. På dialogsidan **Verifiera providerinformation** klickar du på **Skapa**.

    ![Konfigurera verifiering för enkel inloggning][15]

7. Klicka på **Roller** och sedan på **Skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

8. På sidan **Skapa roll** utför du följande steg:  

    ![Konfigurera förtroende för enkel inloggning][19]

    a. Välj **SAML 2.0-federation** under **Select type of trusted entity** (Välj typ av betrodd entitet).

    b. Under **Välj avsnittet SAML 2.0-provider**väljer du den **SAML-leverantör** som du har skapat tidigare (till exempel *WAAD*)

    c. Välj **Allow programmatic and AWS Management Console access** (Tillåt programmatisk åtkomst och AWS-hanteringskonsolåtkomst).
  
    d. Klicka på **Nästa: Behörigheter**.

9. I dialogrutan **Attach Permissions Policies** (Koppla behörighetsprinciper) kopplar du lämplig princip enligt din organisation. Klicka på **Nästa: Granska**.  

    ![Konfigurera princip för enkel inloggning][33]

10. I dialogrutan **Granskning** utför du följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. I textrutan **Rollnamn** anger du ditt rollnamn.

    b. I textrutan **Rollbeskrivning** anger du beskrivningen.

    c. Klicka på **Skapa roll**.

    d. Skapa så många roller som behövs och mappa dem till identitetsprovidern.

11. Logga ut från aktuellt AWS-konto och logga in med andra konton där du vill konfigurera enkel inloggning med Azure AD.

12. Utför steg-2 till steg-10 för att skapa flera roller som du vill konfigurera för det här kontot. Om du har fler än två konton gör du samma steg för alla konton för att skapa roller för dem.

13. När alla roller har skapats i kontona visas de i listan **Roller** för dessa konton.

    ![Inställningar för roller](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

14. Vi måste fånga alla roll-ARN och betrodda entiteter för alla roller i alla konton, som vi måste mappa manuellt med Azure AD-programmet.

15. Klicka på rollerna för att kopiera **värdena Roll ARN** och **Betrodda entiteter.** Du behöver dessa värden för alla roller som du behöver skapa i Azure AD.

    ![Inställningar för roller](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

16. Utför ovanstående steg för alla roller i alla konton och lagra dem alla i formatet **Roll ARN,Betrodda entiteter** i ett anteckningsblock.

17. Öppna [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på Graph Explorer-webbplatsen med autentiseringsuppgifterna för global administratör/samtidig admin för din klientorganisation.

    b. Du måste ha tillräcklig behörighet för att skapa rollerna. Klicka på **ändra behörigheter** för att få de behörigheter som krävs.

    ![Dialogrutan Diagramutforskare](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte redan har dessa) och klicka på "Ändra behörigheter" 

    ![Dialogrutan Diagramutforskare](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Då kommer du att få logga in igen och godkänna medgivandet. När du har accepterat medgivandet loggas du in i Graph Explorer igen.

    e. Ändra versionsrullgardinsmenyn till **beta**. Om du vill hämta alla tjänsthuvudnamn från din klientorganisation använder du följande fråga:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger kan du använda följande mönster, som har din primära domän i`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogrutan Diagramutforskare](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Hämta den du behöver ändra från listan över hämtade tjänsthuvudnamn. Du kan också använda Ctrl+F för att söka i programmet från alla serviceprinciper i listan. Du kan använda följande fråga med hjälp av **objekt-ID** som du har kopierat från Azure AD-egenskaper sidan för att komma till respektive tjänsthuvudnamn.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan Diagramutforskare](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahera egenskapen appRoles från tjänstens huvudobjekt.

    ![Dialogrutan Diagramutforskare](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nu måste du skapa nya roller för ditt program. 

    i. Nedan JSON är ett exempel på appRoles objekt. Skapa ett liknande objekt för att lägga till de roller du vill ha för ditt program.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Du kan bara lägga till nya roller efter **msiam_access** för korrigeringsåtgärden. Du kan också lägga till så många roller du vill enligt organisationens behov. Azure AD skickar **värdet** för dessa roller som anspråksvärde i SAML-svar.

    j. Gå tillbaka till Microsoft Graph Explorer och ändra metoden från **GET** till **PATCH**. Korrigera objektet Service Principal om du vill ha önskade roller genom att uppdatera egenskapen appRoles som liknar den som visas ovan i exemplet. Klicka på **Kör fråga** om du vill köra korrigeringsåtgärden. Ett lyckat meddelande bekräftar skapandet av rollen för din Amazon Web Services-program.

    ![Dialogrutan Utforskare i Microsoft Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

18. När tjänsthuvudhuvudansvarig har korrigerats med fler roller kan du tilldela användare/grupper till respektive roller. Detta kan göras genom att gå till portalen och navigera till Amazon Web Services ansökan. Klicka på fliken **Användare och grupper** högst upp.

19. Vi rekommenderar att du skapar nya grupper för varje AWS-roll så att du kan tilldela just den rollen i den gruppen. Observera att detta är en till en mappning för en grupp till en roll. Du kan sedan lägga till de medlemmar som tillhör den gruppen.

20. När grupperna har skapats väljer du gruppen och tilldelar till programmet.

    ![Konfigurera lägg till enkel inloggning](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

    > [!Note]
    > Kapslade grupper stöds inte när grupper tilldelas.

21. Om du vill tilldela gruppen rollen väljer du rollen och klickar på **Knappen Tilldela** längst ned på sidan.

    ![Konfigurera lägg till enkel inloggning](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

    > [!Note]
    > Observera att du måste uppdatera din session i Azure-portalen för att se nya roller.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomstpanelen bör du skaffa programsidan Amazon Web Services (AWS) med möjlighet att välja rollen.

![Konfigurera lägg till enkel inloggning](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Du kan också verifiera SAML-svaret för att se de roller som skickas som anspråk.

![Konfigurera lägg till enkel inloggning](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Konfigurera etablering med Hjälp av Microsoft Graph API:er](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-configure-api)
* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
