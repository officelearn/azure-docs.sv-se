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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: a6a7861b95f82180b72045f10db99d5bc45eed73
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345664"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Självstudier: Azure Active Directory-integrering med flera konton för Amazon Web Services (AWS)

I kursen får lära du att integrera Azure Active Directory (AD Azure) med flera konton för Amazon Web Services (AWS).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att automatiskt hämta inloggade till Amazon Web Services (AWS) (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

- En Azure AD basic- eller premium-prenumeration
- Amazon Web Services (AWS) flera enkel inloggning aktiverad konton

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Amazon Web Services (AWS) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Att lägga till Amazon Web Services (AWS) från galleriet
Du måste lägga till Amazon Web Services (AWS) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Amazon Web Services (AWS) i Azure AD.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Amazon Web Services (AWS)** väljer **Amazon Web Services (AWS)** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Amazon Web Services (AWS) i resultatlistan](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. När programmet har lagts till, gå till **egenskaper** och kopiera den **objekt-ID**.

    ![Amazon Web Services (AWS) i resultatlistan](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Amazon Web Services (AWS) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

I Amazon Web Services (AWS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. I Azure-portalen på den **Amazon Web Services (AWS)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. På den **Amazon Web Services (AWS)-domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![URL: er och Amazon Web Services (AWS)-domän med enkel inloggning information](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Amazon Web Services (AWS) program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning attribut](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Roll            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Du måste konfigurera användaretablering i Azure AD för att hämta alla roller från AWS-konsolen. Läs etablering stegen nedan.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning attribut](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. I den **Namespace** textruta skriver namnområdesvärdet som visas för den raden.
    
    d. Klicka på **OK**.

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_400.png)

8. I ett annat webbläsarfönster inloggning till webbplatsen Amazon Web Services (AWS) företag som administratör.

9. Klicka på **AWS Home**.
   
    ![Konfigurera enkel inloggning startsida][11]

10. Klicka på **IAM** (identitets- och åtkomsthantering). 
   
    ![Konfigurera identitet för enkel inloggning][12]

11. Klicka på **identitetsleverantörer**, och klicka sedan på **skapa providern**. 
   
    ![Konfigurera enkel inloggning-Provider][13]

12. På den **konfigurera Provider** dialogrutan utför följande steg: 
   
    ![Konfigurera enkel inloggning, dialogruta][14]
 
    a. Som **providertyp**väljer **SAML**.

    b. I den **providernamn** textruta skriver ett provider-namn (till exempel: *trä*).

    c. Att överföra din hämtade **metadatafil** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **nästa steg**.

13. På den **Kontrollera Providerinformationen** dialogrutan sidan, klickar du på **skapa**. 
    
    ![Konfigurera enkel inloggning Kontrollera][15]

14. Klicka på **roller**, och klicka sedan på **skapa roll**. 
    
    ![Konfigurera roller för enkel inloggning][16]

15. På den **skapa roll** utför följande steg:  
    
    ![Konfigurera enkel inloggning förtroende][19] 

    a. Välj **SAML 2.0 federation** under **Välj typ av betrodd entitet**.

    b. Under **välja ett avsnitt som SAML 2.0-providern**, Välj den **SAML-providern** du skapade tidigare (till exempel: *trä*)

    c. Välj **Tillåt programmässiga och AWS-konsolen för hantering av åtkomst**.
  
    d. Klicka på **nästa: behörigheter**.

16. På den **ansluta behörigheterna principer** dialogrutan klickar du på **nästa: granska**.  
    
    ![Konfigurera principen för enkel inloggning][33]

17. På den **granska** dialogrutan, utför följande steg:   
    
    ![Konfigurera granskning för enkel inloggning][34] 

    a. I den **rollnamn** textruta ange Role-namn.

    b. I den **Rollbeskrivning** textruta registrerar du en beskrivning.

    a. Klicka på **skapa roll**.

    b. Skapa så många roller vid behov och koppla dem till identitetsleverantören.

18. Logga ut från aktuella AWS-konto och logga in med andra konto där du vill konfigurera enkel inloggning på med Azure AD.

19. Utför steg 9 steg 17 att skapa flera roller som du vill att installationsprogrammet för det här kontot. Om du har fler än två konton, utför samma steg att skapa roller för alla konton.

20. När alla roller skapas i konton, de visas i den **roller** lista för dessa konton.

    ![Installationen av roller](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Vi behöver avbilda alla rollen ARN och betrodda entiteter för alla roller för alla konton som vi behöver mappar manuellt med Azure AD-program. 

22. Klicka på rollerna som ska kopiera **rollen ARN** och **betrodda enheter** värden. Du behöver dessa värden för alla roller som du behöver skapa i Azure AD.

    ![Installationen av roller](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Utför ovanstående steg för alla roller i alla konton och lagra dem i formatet **rollen ARN, betrodda enheter** i en anteckningar. 

24. Öppna [Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) i ett annat fönster.

    a. Logga in på webbplatsen Explorer diagram med autentiseringsuppgifterna som Global administratör/medadministratör för din klient.

    b. Du måste ha behörighet att skapa roller. Klicka på **ändringsbehörighet** få behörigheterna som krävs. 

    ![Dialogrutan för diagram explorer](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Välj följande behörigheter i listan (om du inte har dessa redan) och klicka på ”behörighet att ändra” 

    ![Dialogrutan för diagram explorer](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Detta blir du ombedd att logga in igen och acceptera samtycke. När du godkänt samtycke är du inloggad i diagrammet Explorer igen.

    e. Ändra version dropdown till **beta**. Använd följande fråga för att hämta tjänstens huvudnamn från din klient:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Om du använder flera kataloger kan du använda följande mönster som har din primär domän `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Dialogrutan för diagram explorer](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. Hämta det måste du ändra listan över tjänstens huvudnamn hämtas och. Du kan också använda Ctrl + F för att söka efter programmet från de angivna ServicePrincipals. Du kan använda följande fråga med hjälp av den **objekt-id** som du har kopierat från Azure AD egenskapssidan för att hämta till respektive tjänstens huvudnamn.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Dialogrutan för diagram explorer](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Extrahera appRoles egenskapen från tjänsten huvudobjektet. 

    ![Dialogrutan för diagram explorer](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new3.png)

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
    > Du kan bara lägga till nya roller efter den **msiam_access** för åtgärden korrigering. Dessutom kan du lägga till så många roller som du vill per behöver din organisation. Azure AD skickar den **värdet** av dessa roller som anspråksvärdet SAML-svar.
    
    j. Gå tillbaka till ditt diagram Explorer och ändra metod från **hämta** till **korrigering**. Korrigera Service Principal-objekt om du vill ha önskad roller genom att uppdatera appRoles egenskapen liknar det i exemplet ovan. Klicka på **Kör fråga** att köra åtgärden korrigering. Ett meddelande bekräftar skapandet av rollen för Amazon Web Services-program.

    ![Dialogrutan för diagram explorer](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. När tjänstens huvudnamn har korrigerats med flera roller, kan du tilldela respektive roller användare eller grupper. Detta kan göras genom att gå till portalen och gå till Amazon Web Services-programmet. Klicka på den **användare och grupper** högst upp. 

26. Vi rekommenderar att du kan skapa nya grupper för varje roll för AWS så att du kan tilldela specifika rollen i gruppen. Observera att detta är en till en mappning för en grupp till en roll. Du kan sedan lägga till medlemmar som hör till gruppen.

27. Efter att grupperna har skapats, Välj gruppen och tilldela till programmet. 

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Om du vill tilldela rollen till gruppen, Välj rollen och klicka på **tilldela** knappen längst ned på sidan.

    ![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Observera att du behöver uppdatera din session i Azure portal och se nya roller.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Du bör få Amazon Web Services (AWS) appen på sidan med alternativet att välja rollen när du klickar på panelen Amazon Web Services (AWS) på åtkomstpanelen.

![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Du kan också kontrollera SAML-svaret för att se de roller som skickas som anspråk.

![Konfigurera enkel inloggning Lägg till](./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

