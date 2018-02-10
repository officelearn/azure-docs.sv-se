---
title: "Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS)."
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
ms.date: 1/16/2017
ms.author: jeedes
ms.openlocfilehash: 0ff14365323d66a101e5847d7959045c3f20dea2
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS)

I kursen får lära du att integrera Amazon Web Services (AWS) med Azure Active Directory (AD Azure).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att få loggas in automatiskt till Amazon Web Services (AWS) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats--Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

- En Azure AD-prenumeration
- En Amazon Web Services (AWS) enkel inloggning aktiverad prenumeration

> [!NOTE]
> Vi rekommenderar inte använda en produktionsmiljö för att testa stegen i den här självstudiekursen.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte din produktionsmiljö om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [skaffa en kostnadsfri utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Amazon Web Services (AWS) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-amazon-web-services-aws-from-the-gallery"></a>Lägg till Amazon Web Services (AWS) från galleriet
Du måste lägga till Amazon Web Services (AWS) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Amazon Web Services (AWS) i Azure AD.

**Om du vill lägga till Amazon Web Services (AWS) från galleriet, gör du följande:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, i det vänstra navigeringsfönstret väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Amazon Web Services (AWS)**. Välj **Amazon Web Services (AWS)** resultatrutan och välj sedan den **Lägg till** för att lägga till programmet.

    ![Amazon Web Services (AWS) i resultatlistan](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att veta vilka motsvarande användaren i Amazon Web Services (AWS) är en användare i Azure AD. Med andra ord måste du skapa en länk mellan en Azure AD-användare och en relaterad användare i Amazon Web Services (AWS).

För att upprätta länken i Amazon Web Services (AWS), ger värde **användarnamn** samma värde som **användarnamn** i Azure AD. 

Slutför följande byggblock för att konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS):

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) att användarna ska kunna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user) har en motsvarighet för Britta Simon i Amazon Web Services (AWS) och som är kopplad till Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Amazon Web Services (AWS).

**För att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS), gör du följande:**

1. I Azure-portalen på den **Amazon Web Services (AWS)** programmet integration anger **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. Aktivera enkel inloggning, i den **enkel inloggning** i dialogrutan den **läge** listrutan, Välj **SAML-baserade inloggning**.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. I den **Amazon Web Services (AWS)-domän och URL: er** avsnittet användaren behöver vidta åtgärder eftersom appen före redan är integrerad med Azure.

    ![URL: er och Amazon Web Services (AWS)-domän med enkel inloggning information](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Programmet Amazon Web Services (AWS) förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan program integrering. Följande skärmbild visar ett exempel:

    ![Konfigurera attribut för enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png)  

5. I den **användarattribut** under den **enkel inloggning** konfigurerar attributet SAML-token som visas i föregående bild, och sedan gör du följande:
    
    | Attributets namn  | Attributvärdet | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Roll            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Konfigurera användaretablering i Azure AD för att hämta alla roller från konsolen Amazon Web Services (AWS). Referera till följande etablering.

    a. Öppna den **lägga till attributet** dialogrutan **Lägg till attributet**.

    ![Konfigurera attribut för enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurera attribut för enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. I den **namnet** skriver attributets namn som visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. I den **Namespace** skriver namnområdesvärdet som visas för den raden.
    
    d. Välj **Ok**.

6. I den **SAML-signeringscertifikat** väljer **XML-Metadata för**. Spara metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Välj **Spara**.

    ![Konfigurera enkel inloggning spara knappen](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. I ett annat webbläsarfönster loggar du in på ditt Amazon Web Services (AWS) företagets webbplats som administratör.

9. Välj **konsolen Home**.
   
    ![Konfigurera enkel inloggning, hem][11]

10. Välj **identitets- och åtkomsthantering**. 
   
    ![Konfigurera identitet för enkel inloggning][12]

11. Välj **identitetsleverantörer**. Välj sedan **skapa providern**. 
   
    ![Konfigurera enkel inloggning-provider][13]

12. I den **konfigurera Provider** dialogrutan rutan, gör du följande: 
   
    ![Konfigurera enkel inloggning n dialogrutan][14]
 
    a. För **providertyp**väljer **SAML**.

    b. I den **providernamn** skriver ett provider-namn (till exempel: *trä*).

    c. Att överföra din hämtade **metadatafil** från Azure portal, Välj **Välj fil**.

    d. Välj **nästa steg**.

13. I den **Kontrollera Providerinformationen** dialogrutan **skapa**. 
    
    ![Konfigurera enkel inloggning verifiering][15]

14. Välj **roller**. Välj sedan **Skapa ny roll**. 
    
    ![Konfigurera roller för enkel inloggning][16]

15. I den **ange rollnamn** dialogrutan rutan, gör du följande: 
    
    ![Konfigurera enkel inloggning namn][17] 

    a. I den **rollnamn** skriver ett rollnamn (till exempel *TestUser*). 

    b. Välj **nästa steg**.

16. I den **Välj typ av roll** dialogrutan rutan, gör du följande: 
    
    ![Konfigurera enkel inloggning rolltyp][18] 

    a. Välj **roll för identitets-providerns åtkomst**. 

    b. I den **bevilja Web Single Sign-On (WebSSO) åtkomst till SAML-providers** klickar du på **Välj**.

17. I den **upprätta förtroende** dialogrutan rutan, gör du följande:  
    
    ![Konfigurera enkel inloggning förtroende][19] 

    a. Välj den SAML-provider som du skapade tidigare (till exempel: *trä*). 
  
    b. Välj **nästa steg**.

18. I den **Kontrollera rollen förtroende** dialogrutan **nästa steg**. 
    
    ![Konfigurera enkel inloggning rollen förtroende][32]

19. I den **koppla principen** dialogrutan **nästa steg**.  
    
    ![Konfigurera principen för enkel inloggning][33]

20. I den **granska** dialogrutan rutan, gör du följande:   
    
    ![Konfigurera granskning för enkel inloggning][34] 

    a. Välj **skapa roll**.

    b. Skapa så många roller vid behov och koppla dem till identitetsleverantören.

21. Använda Amazon Web Services (AWS) autentiseringsuppgifterna för tjänstkontot för hämtning av rollerna från Amazon Web Services (AWS)-konto i Azure AD användaretablering. Öppna konsolen Amazon Web Services (AWS) hem för att starta den här uppgiften.

22. Välj **Services** > **säkerhet, identitet och efterlevnad** > **IAM**.

    ![Hämtning av rollerna från Amazon Web Services (AWS) konto](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole1.png)

23. I IAM-avsnittet väljer du den **principer** fliken.

    ![Hämtning av rollerna från Amazon Web Services (AWS) konto](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole2.png)

24. Om du vill skapa en ny princip **skapa princip**.

    ![Skapa en ny princip](./media/active-directory-saas-amazon-web-service-tutorial/fetchingrole3.png)
 
25. För att skapa din egen policy för att hämta alla roller från Amazon Web Services (AWS) konton, gör du följande:

    ![Skapa en ny princip](./media/active-directory-saas-amazon-web-service-tutorial/policy1.png)

    a. I den **skapa princip** väljer den **JSON** fliken.

    b. Lägg till följande JSON i dokumentets princip:
    
    ```
    
    {

    "Version": "2012-10-17",

    "Statement": [

    {

    "Effect": "Allow",
        
    "Action": [
        
    "iam: ListRoles"
        
    ],

    "Resource": "*"

    }

    ]

    }
    
    ```

    c. Om du vill verifiera principen, Välj den **granska princip knappen**.

    ![Definiera den nya principen](./media/active-directory-saas-amazon-web-service-tutorial/policy5.png)

26. Definiera den **ny princip** genom att utföra följande steg:

    ![Definiera den nya principen](./media/active-directory-saas-amazon-web-service-tutorial/policy2.png)

    a. Ange den **principnamn** som **AzureAD_SSOUserRole_Policy**.

    b. Du kan ange följande **beskrivning** för principen: **den här principen kan du hämta rollerna från AWS konton**.
    
    c. Välj den **skapa princip** knappen.
        
27. Om du vill skapa ett nytt användarkonto i Amazon Web Services (AWS) IAM-tjänsten, gör du följande:

    a. Välj **användare** i Amazon Web Services (AWS) IAM-konsolen.

    ![Definiera den nya principen](./media/active-directory-saas-amazon-web-service-tutorial/policy3.png)
    
    b.To skapa en ny användare väljer den **Lägg till användare** knappen.

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/policy4.png)

    c. I den **Lägg till användare** avsnittet, gör du följande:
    
    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/adduser1.png)
    
    * Ange **AzureADRoleManager** i användarnamnsrutan.
    
    * Typ av åtkomst, Välj den **Programmeringsåtkomst** alternativet. Det här sättet användaren kan anropa API: erna och kontot Amazon Web Services (AWS) för att hämta rollerna.
    
    * Välj den **nästa behörigheter** i det nedre högra hörnet.

28. Skapa en ny princip för den här användaren genom att utföra följande steg:

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/adduser2.png)
    
    a. Välj den **koppla befintliga principer direkt** knappen.

    b. Sök efter den nyskapade principen i avsnittet filter **AzureAD_SSOUserRole_Policy**.
    
    c. Välj den **princip**. Välj sedan den **nästa: granska** knappen.

29. Granska principen för den anslutna användaren genom att utföra följande steg:

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/adduser3.png)
    
    a. Granska användarnamn, åtkomsttyp och principer som är mappade till användaren.
    
    b. För att skapa användaren, Välj den **skapa användare** knappen i det nedre högra hörnet för att skapa användaren.

30. Hämta autentiseringsuppgifterna för en användare genom att utföra följande steg:

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/adduser4.png)
    
    a. Kopiera användaren **åtkomst nyckeln ID** och **hemliga åtkomstnyckel**.
    
    b. Ange autentiseringsuppgifterna i Azure AD användaren etablering avsnittet att hämta rollerna från konsolen Amazon Web Services (AWS).
    
    c. Välj den **Stäng** i det nedre högra hörnet.

31. Navigera till den **Användaretablering** avsnitt i appen Amazon Web Services (AWS) i hanteringsportalen för Azure AD.

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/provisioning.png)

32. Ange den **åtkomstnyckeln** och **hemlighet** i den **Klienthemlighet** och **hemlighet Token** fältet respektive.

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/provisioning1.png)
    
    a. Ange nyckeln Amazon Web Services (AWS) användaren åtkomst i den **clientsecret** fältet.
    
    b. Ange hemligheten som Amazon Web Services (AWS) användare i den **hemlighet Token** fältet.
    
    c. Välj den **Testanslutningen** knappen. Du ska kunna testa har den här anslutningen.

    d. Spara inställningen genom att välja den **spara** längst upp.
 
33. Kontrollera att du aktiverar den Status för etablering **på** i **inställningar**. Det gör du genom att välja **på**, och sedan välja den **spara** längst upp.

    ![Lägga till användare](./media/active-directory-saas-amazon-web-service-tutorial/provisioning2.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com) medan du installerar appen. När du lägger till den här appen från den **Active Directory > företagsprogram** väljer den **enkel inloggning** fliken. Komma åt inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentation på [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen i den vänstra rutan, Välj den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör du följande:

    ![Användardialogrutan](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Skapa en testanvändare Amazon Web Services (AWS)

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Amazon Web Services (AWS). Amazon Web Services (AWS) behöver inte en användare ska kunna skapas i systemet för enkel inloggning, så du inte behöver göra någonting här.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att ge dem åtkomst till Amazon Web Services (AWS).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Amazon Web Services (AWS), gör du följande:**

1. Öppna program i Azure-portalen. Gå till vyn directory och välj sedan **företagsprogram**. Välj därefter **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Amazon Web Services (Amazon Web Services (AWS)**.

    ![Länken Amazon Web Services (AWS) i listan med program](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

3. Välj på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj den **Lägg till** knappen. I den **Lägg uppdrag** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen. 

7. I den **Lägg uppdrag** dialogrutan markerar du den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer panelen Amazon Web Services (AWS) på åtkomstpanelen du bör få automatiskt inloggad i tillämpningsprogrammet Amazon Web Services (AWS). Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_203.png
[11]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795031.png
[12]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795032.png
[13]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795033.png
[14]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795034.png
[15]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795035.png
[16]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795022.png
[17]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795023.png
[18]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795024.png
[19]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795025.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png

