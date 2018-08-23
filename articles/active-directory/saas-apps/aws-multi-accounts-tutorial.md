---
title: 'Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) att ansluta flera konton | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure AD och flera konton för Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: jeedes
ms.openlocfilehash: 55a271e49977feb6d8e5c188f1724b66db4cb4f5
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055407"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Självstudier: Azure Active Directory-integration med flera konton för Amazon Web Services (AWS)

I den här självstudien får du lära dig hur du integrerar Azure Active Directory (Azure AD) med flera konton för Amazon Web Services (AWS).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att automatiskt få loggat in till Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS), behöver du följande objekt:

- En Azure AD basic- eller premium-prenumeration
- Amazon Web Services (AWS) flera enkel inloggning aktiverat konton

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Amazon Web Services (AWS) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Att lägga till Amazon Web Services (AWS) från galleriet
För att konfigurera integrering av Amazon Web Services (AWS) till Azure AD, som du behöver lägga till Amazon Web Services (AWS) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Amazon Web Services (AWS)** väljer **Amazon Web Services (AWS)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. När programmet har lagts till, går du till **egenskaper** sida och kopiera den **objekt-ID**.

    ![Amazon Web Services (AWS) i resultatlistan](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Amazon Web Services (AWS) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

I Amazon Web Services (AWS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. I Azure-portalen på den **Amazon Web Services (AWS)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. På den **Amazon Web Services (AWS)-domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Amazon Web Services (AWS)-domän och URL: er med enkel inloggning för information](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Programvara för Amazon Web Services (AWS) förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning för attributet](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)    

5. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:

    | Attributnamn  | Attributvärde | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Roll            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”Ge värdet för talsession per dina behov” |  https://aws.amazon.com/SAML/Attributes |

    >[!TIP]
    >Du måste konfigurera användaretablering i Azure AD för att hämta alla roller från AWS-konsolen. Se etablering stegen nedan.

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning för attributet](./media/aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. I den **Namespace** textrutan skriver namnområdesvärdet som visas för den raden.

    d. Klicka på **OK**.

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/aws-multi-accounts-tutorial/tutorial_general_400.png)

8. I ett annat webbläsarfönster inloggning till webbplatsen för företagets Amazon Web Services (AWS) som administratör.

9. Klicka på **AWS Home**.

    ![Konfigurera enkel inloggning på startsidan][11]

10. Klicka på **IAM** (Identity and Access Management).

    ![Konfigurera identitet för enkel inloggning][12]

11. Klicka på **Identitetsprovidrar**, och klicka sedan på **skapa providern**.

    ![Konfigurera Provider för enkel inloggning][13]

12. På den **konfigurera Provider** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning för dialogrutan][14]

    a. Som **providertyp**väljer **SAML**.

    b. I den **providernamn** textrutan skriver du ett provider-namn (till exempel: *trä*).

    c. Ladda upp din hämtade **metadatafil** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **nästa steg**.

13. På den **Kontrollera Providerinformationen** dialogrutan sidan, klickar du på **skapa**.

    ![Konfigurera enkel inloggning kontrollerar][15]

14. Klicka på **roller**, och klicka sedan på **skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

15. På den **skapa roll** utför följande steg:  

    ![Konfigurera enkel inloggning, förtroende][19]

    a. Välj **SAML 2.0 federation** under **Välj typ av betrodd entitet**.

    b. Under **välja ett avsnitt som SAML 2.0-providern**väljer den **SAML-providern** du skapade tidigare (till exempel: *trä*)

    c. Välj **Tillåt programmässiga och AWS-hanteringskonsolen åtkomst**.
  
    d. Klicka på **nästa: behörigheter**.

16. På den **bifoga Säkerhetsbehörighetsprinciper** dialogrutan klickar du på **nästa: granska**.  

    ![Konfigurera principen för enkel inloggning][33]

17. På den **granska** dialogrutan utför följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. I den **rollnamn** textrutan, ange ditt namn för rollen.

    b. I den **Rollbeskrivning** textrutan Ange beskrivningen.

    a. Klicka på **skapa roll**.

    b. Skapa så många roller efter behov och mappa den till identitetsleverantören.

18. Logga ut från aktuella AWS-konto och logga in med andra konto där du vill konfigurera enkel inloggning på med Azure AD.

19. Utför steg – 9 för-steg-17 för att skapa flera roller som du vill att installationsprogrammet för det här kontot. Om du har fler än två konton, utför samma steg för alla konton för att skapa roller för dessa.

20. När alla roller har skapats i konton som de visas i den **roller** för dessa konton.

    ![Installationen av roller](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Vi behöver att avbilda alla rollen ARN och betrodda entiteter för alla roller för samtliga konton, vilket vi behöver för att mappa manuellt med Azure AD-program. 

22. Klicka på rollerna som ska kopiera **rollen ARN** och **betrodda entiteter** värden. Du behöver dessa värden för alla roller som du behöver skapa i Azure AD.

    ![Installationen av roller](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

23. Utför ovanstående steg för alla roller i alla konton och lagra dem alla i formatet **rollen ARN, betrodda entiteter** i anteckningar.

24. Öppna [Azure AD Graph-testaren](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på webbplatsen Graph-testaren med autentiseringsuppgifterna som Global administratör/medadministratör för din klient.

    b. Du måste ha tillräcklig behörighet för att skapa roller. Klicka på **ändringsbehörigheter** att få behörigheterna som krävs.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte har dessa redan) och klicka på ”Ändra behörigheter” 

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Detta blir du ombedd att logga in igen och acceptera samtycke. Efter att du godkänt medgivande, är du inloggad på Grafutforskaren igen.

    e. Ändra version listrutan till **beta**. Om du vill hämta alla huvudnamn från din klient, använder du följande fråga:

     `https://graph.microsoft.com/beta/servicePrincipals`

    Om du använder flera kataloger kan du använda följande mönster som har din primära domän `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Från listan över tjänstens huvudnamn som hämtas, får du det du behöver ändra. Du kan också använda Ctrl + F för att söka efter programmet från alla listade ServicePrincipals. Du kan använda följande fråga med hjälp av den **objekt-id** som du har kopierat från Azure AD-egenskaper-sidan för att komma till respektive tjänstens huvudnamn.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahera egenskapen appRoles från det tjänstens huvudnamnsobjekt.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Nu måste du generera nya roller för programmet. 

    i. Är ett exempel på appRoles objekt nedan JSON. Skapa ett liknande objekt för att lägga till de roller som du vill använda för ditt program.

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
    > Du kan bara lägga till nya roller efter den **msiam_access** för patch-åtgärden utfördes. Dessutom kan du lägga till så många roller som du vill ha per behovet av din organisation. Azure AD skickar den **värdet** av dessa roller som anspråksvärdet i SAML-svar.

    j. Gå tillbaka till Graph-testaren och ändra metod från **hämta** till **KORRIGERA**. Korrigera Service Principal-objekt om du vill ha önskad roller genom att uppdatera appRoles egenskapen liknar det ovan i det här exemplet. Klicka på **Kör fråga** att köra patch-åtgärden utfördes. Ett meddelande bekräftar att skapa rollen för Amazon Web Services-program.

    ![Dialogrutan för Graph explorer](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. När tjänstens huvudnamn är uppdaterad med flera roller, kan du tilldela olika roller användare/grupper. Detta kan göras genom att gå till portalen och gå till Amazon Web Services-programmet. Klicka på den **användare och grupper** fliken längst upp. 

26. Vi rekommenderar att du kan skapa nya grupper för varje roll i AWS så att du kan tilldela specifika rollen i gruppen. Observera att detta är en-till-en-mappning för en grupp till en roll. Du kan sedan lägga till de medlemmar som tillhör denna grupp.

27. När gruppen har skapats, Välj gruppen och tilldela till programmet.

    ![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Om du vill tilldela rollen till gruppen, Välj rollen och klicka på **tilldela** längst ned på sidan.

    ![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Observera att du behöver uppdatera din session i Azure portal för att se nya roller.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomstpanelen, bör du få programsidan för Amazon Web Services (AWS) med möjlighet att välja rollen.

![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Du kan också kontrollera SAML-svar för att se de roller som skickas som anspråk.

![Konfigurera enkel inloggning för Lägg till](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/aws-multi-accounts-tutorial/tutorial_general_203.png
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
[41]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

