---
title: 'Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS).
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
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 884e07880b76ec1ff53071d03d0ce340188586e3
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018158"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS)

I kursen får lära du att integrera Amazon Web Services (AWS) med Azure Active Directory (AD Azure).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att automatiskt hämta inloggade till Amazon Web Services (AWS) (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

- En Azure AD-prenumeration
- En Amazon Web Services (AWS) enkel inloggning aktiverad prenumeration

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

    ![Amazon Web Services (AWS) i resultatlistan](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Amazon Web Services (AWS) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

I Amazon Web Services (AWS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  – du har en motsvarighet för Britta Simon i Amazon Web Services (AWS) och som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. I Azure-portalen på den **Amazon Web Services (AWS)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. På den **Amazon Web Services (AWS)-domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![URL: er och Amazon Web Services (AWS)-domän med enkel inloggning information](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Amazon Web Services (AWS) program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning attb](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png) 

5. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Roll            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Du måste konfigurera användaretablering i Azure AD för att hämta alla roller från AWS-konsolen. Läs etablering stegen nedan.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning Lägg till](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning addattb](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. I den **Namespace** textruta skriver namnområdesvärdet som visas för den raden.
    
    d. Klicka på **OK**.

6. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/amazon-web-service-tutorial/tutorial_general_400.png)

8. I ett annat webbläsarfönster inloggning till webbplatsen Amazon Web Services (AWS) företag som administratör.

9. Klicka på **AWS Home**.
   
    ![Konfigurera enkel inloggning startsida][11]

10. Klicka på **identitets- och åtkomsthantering**. 
   
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

16. På den **ansluta behörigheterna principer** dialogrutan, behöver du inte bifoga alla principer. Klicka på **nästa: granska**.  
    
    ![Konfigurera principen för enkel inloggning][33]

17. På den **granska** dialogrutan, utför följande steg:   
    
    ![Konfigurera granskning för enkel inloggning][34] 

    a. I den **rollnamn** textruta ange Role-namn.

    b. I den **Rollbeskrivning** textruta registrerar du en beskrivning.

    c. Klicka på **skapa roll**.

    d. Skapa så många roller vid behov och koppla dem till identitetsleverantören.

18. Använd AWS autentiseringsuppgifterna för tjänstkontot för hämtning av rollerna från AWS-konto i Azure AD-användare etablering. Öppna konsolen AWS hem för det här.

19. Klicka på **Services** -> **säkerhet, identitet och efterlevnad** -> **IAM**.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole1.png)

20. Välj den **principer** fliken i IAM-avsnittet.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole2.png)

21. Skapa en ny princip genom att klicka på **skapa princip** för hämtning av rollerna från AWS-konto i Azure AD-användare etablering.

    ![Skapar en ny princip](./media/amazon-web-service-tutorial/fetchingrole3.png)

22. Skapa din egen policy för att hämta alla roller från AWS-konton genom att utföra följande steg:

    ![Skapar en ny princip](./media/amazon-web-service-tutorial/policy1.png)

    a. I den **”skapa principen”** avsnittet klickar du på **”JSON”** fliken.

    b. Lägg till i principdokument i nedan JSON.
    
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

    c. Klicka på **granska princip knappen** att verifiera principen.

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy5.png)

23. Definiera den **ny princip** genom att utföra följande steg:

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy2.png)

    a. Ange den **principnamn** som **AzureAD_SSOUserRole_Policy**.

    b. Du kan ange **beskrivning** till principen som **principen tillåter för att hämta rollerna från AWS konton**.
    
    c. Klicka på **”skapa Policy”** knappen.

24. Skapa ett nytt användarkonto i AWS IAM-tjänsten genom att utföra följande steg:

    a. Klicka på **användare** navigering i IAM-AWS-konsolen.

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy3.png)
    
    b. Klicka på **Lägg till användare** för att skapa en ny användare.

    ![Lägga till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I den **Lägg till användare** avsnittet, utför följande steg:
    
    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser1.png)
    
    * Ange namnet på användaren som **AzureADRoleManager**.
    
    * Välj i vilken åtkomst av **Programmeringsåtkomst** alternativet. Det här sättet användaren kan anropa API: erna och AWS-konto för att hämta rollerna.
    
    * Klicka på den **nästa behörigheter** knappen i nederkant högra hörnet.

25. Nu skapa en ny princip för den här användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser2.png)
    
    a. Klicka på den **koppla befintliga principer direkt** knappen.

    b. Sök efter den nyskapade principen i avsnittet filter **AzureAD_SSOUserRole_Policy**.
    
    c. Välj den **princip** och klicka sedan på den **nästa: granska** knappen.

26. Granska principen till den anslutna användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser3.png)
    
    a. Granska användarnamn, åtkomsttyp och principer som är mappad till användaren.
    
    b. Klicka på den **skapa användare** längst ned till höger att skapa användaren.

27. Hämta autentiseringsuppgifter för en användare genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser4.png)
    
    a. Kopiera användaren **åtkomst nyckeln ID** och **hemliga åtkomstnyckel**.
    
    b. Ange autentiseringsuppgifterna i Azure AD-användare etablering avsnittet att hämta rollerna från AWS-konsolen.
    
    c. Klicka på **Stäng** längst ned.

28. Gå till **Användaretablering** avsnitt i Amazon Web Services-app i Azure AD-hanteringsportalen.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning.png)

29. Ange den **åtkomstnyckeln** och **hemlighet** i den **Klienthemlighet** och **hemlighet Token** fältet respektive.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning1.png)
    
    a. Ange nyckeln AWS användaren åtkomst i den **clientsecret** fältet.
    
    b. Ange hemligheten för AWS-användare i den **hemlighet Token** fältet.
    
    c. Klicka på den **Testa anslutning** knappen och du ska kunna testa har den här anslutningen.

    d. Spara inställningen genom att klicka på den **spara** längst upp.
 
30. Nu se till att du aktiverar Status för etablering **på** i avsnittet inställningar genom att byta på och klicka sedan på den **spara** längst upp.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning2.png)

> [!Note]
> Om du vill integrera flera AWS-konton till en Azure-konto för enkel inloggning på [detta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artikel. 


### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/amazon-web-service-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/amazon-web-service-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/amazon-web-service-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Skapa en testanvändare Amazon Web Services (AWS)

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Amazon Web Services (AWS). Amazon Web Services (AWS) behöver inte en användare ska kunna skapas i systemet för enkel inloggning, så du inte behöver göra någonting här.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Amazon Web Services (AWS).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Amazon Web Services (AWS), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Amazon Web Services (AWS)**.

    ![Länken Amazon Web Services (AWS) i listan med program](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Amazon Web Services (AWS) på åtkomstpanelen du bör få automatiskt loggat in på ditt program för Amazon Web Services (AWS). Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/amazon-web-service-tutorial/tutorial_general_203.png
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
