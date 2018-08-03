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
ms.openlocfilehash: 797be143284566efcefce5ed6c7ded822d5aa97f
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438905"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS)

I den här självstudien får du lära dig hur du integrerar Amazon Web Services (AWS) med Azure Active Directory (AD Azure).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att automatiskt få loggat in till Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS), behöver du följande objekt:

- En Azure AD-prenumeration
- En Amazon Web Services (AWS) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Amazon Web Services (AWS) från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Att lägga till Amazon Web Services (AWS) från galleriet
För att konfigurera integrering av Amazon Web Services (AWS) till Azure AD, som du behöver lägga till Amazon Web Services (AWS) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

1. I sökrutan skriver **Amazon Web Services (AWS)** väljer **Amazon Web Services (AWS)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![Amazon Web Services (AWS) i resultatlistan](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Amazon Web Services (AWS) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

I Amazon Web Services (AWS), tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare i Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  – du har en motsvarighet för Britta Simon i Amazon Web Services (AWS) och som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. I Azure-portalen på den **Amazon Web Services (AWS)** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

1. På den **Amazon Web Services (AWS)-domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![Amazon Web Services (AWS)-domän och URL: er med enkel inloggning för information](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

1. Programvara för Amazon Web Services (AWS) förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den ”**användarattribut**” på sidan för integrering av program. Följande skärmbild visar ett exempel för detta.

    ![Konfigurera enkel inloggning attb](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_attribute.png) 

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Roll            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Du måste konfigurera användaretablering i Azure AD för att hämta alla roller från AWS-konsolen. Se etablering stegen nedan.

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning för Lägg till](./media/amazon-web-service-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning addattb](./media/amazon-web-service-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Från den **värdet** anger attributvärdet som visas för den raden.

    d. I den **Namespace** textrutan skriver namnområdesvärdet som visas för den raden.
    
    d. Klicka på **OK**.

1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/amazon-web-service-tutorial/tutorial_general_400.png)

1. I ett annat webbläsarfönster inloggning till webbplatsen för företagets Amazon Web Services (AWS) som administratör.

1. Klicka på **AWS Home**.
   
    ![Konfigurera enkel inloggning på startsidan][11]

1. Klicka på **Identity and Access Management**. 
   
    ![Konfigurera identitet för enkel inloggning][12]

1. Klicka på **Identitetsprovidrar**, och klicka sedan på **skapa providern**. 
   
    ![Konfigurera Provider för enkel inloggning][13]

1. På den **konfigurera Provider** dialogrutan utför följande steg: 
   
    ![Konfigurera enkel inloggning för dialogrutan][14]
 
    a. Som **providertyp**väljer **SAML**.

    b. I den **providernamn** textrutan skriver du ett provider-namn (till exempel: *trä*).

    c. Ladda upp din hämtade **metadatafil** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **nästa steg**.

1. På den **Kontrollera Providerinformationen** dialogrutan sidan, klickar du på **skapa**. 
    
    ![Konfigurera enkel inloggning kontrollerar][15]

1. Klicka på **roller**, och klicka sedan på **skapa roll**. 
    
    ![Konfigurera roller för enkel inloggning][16]

1. På den **skapa roll** utför följande steg:  
    
    ![Konfigurera enkel inloggning, förtroende][19] 

    a. Välj **SAML 2.0 federation** under **Välj typ av betrodd entitet**.

    b. Under **välja ett avsnitt som SAML 2.0-providern**väljer den **SAML-providern** du skapade tidigare (till exempel: *trä*)

    c. Välj **Tillåt programmässiga och AWS-hanteringskonsolen åtkomst**.
  
    d. Klicka på **nästa: behörigheter**.

1. På den **bifoga Säkerhetsbehörighetsprinciper** dialogrutan, behöver du inte bifoga princip. Klicka på **nästa: granska**.  
    
    ![Konfigurera principen för enkel inloggning][33]

1. På den **granska** dialogrutan utför följande steg:   
    
    ![Konfigurera granskning för enkel inloggning][34] 

    a. I den **rollnamn** textrutan, ange ditt namn för rollen.

    b. I den **Rollbeskrivning** textrutan Ange beskrivningen.

    c. Klicka på **skapa roll**.

    d. Skapa så många roller efter behov och mappa den till identitetsleverantören.

1. Använd autentiseringsuppgifter för AWS-tjänstkontot för hämtning av rollerna från AWS-konto i Azure AD-användare etablering. Öppna konsolen AWS home till detta ändamål.

1. Klicka på **Services** -> **säkerhet, identitet och efterlevnad** -> **IAM**.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole1.png)

1. Välj den **principer** fliken i avsnittet IAM.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole2.png)

1. Skapa en ny princip genom att klicka på **skapa princip** för hämtning av rollerna från AWS-konto i Azure AD-användare etablering.

    ![Skapar en ny princip](./media/amazon-web-service-tutorial/fetchingrole3.png)

1. Skapa en egen princip för att hämta alla roller från AWS-konton genom att utföra följande steg:

    ![Skapar en ny princip](./media/amazon-web-service-tutorial/policy1.png)

    a. I den **”skapa principen”** avsnittet klickar du på **”JSON”** fliken.

    b. I dokumentet, lägger du till den nedan JSON.
    
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

1. Definiera den **ny princip** genom att utföra följande steg:

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy2.png)

    a. Ange den **principnamn** som **AzureAD_SSOUserRole_Policy**.

    b. Du kan ange **beskrivning** för principen som **den här principen tillåter för att hämta rollerna från AWS-konton**.
    
    c. Klicka på **”skapa Policy”** knappen.

1.  Skapa ett nytt användarkonto i AWS IAM-tjänsten genom att utföra följande steg:

    a. Klicka på **användare** navigering i AWS IAM-konsolen.

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy3.png)
    
    b. Klicka på **Lägg till användare** för att skapa en ny användare.

    ![Lägga till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I den **Lägg till användare** avsnittet, utför följande steg:
    
    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser1.png)
    
    * Ange namnet på användaren som **AzureADRoleManager**.
    
    * I åtkomst-typ, väljer du den **Programmeringsåtkomst** alternativet. Det här sättet du kan anropa API: erna och hämta rollerna från AWS-konto.
    
    * Klicka på den **nästa behörigheter** knappen i nederkant högra hörnet.

1. Skapa nu en ny princip för den här användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser2.png)
    
    a. Klicka på den **koppla befintliga principer direkt** knappen.

    b. Sök efter den nya principen i avsnittet filter **AzureAD_SSOUserRole_Policy**.
    
    c. Välj den **princip** och klicka sedan på den **nästa: granska** knappen.

1.  Granska principen till den anslutna användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser3.png)
    
    a. Granska användarnamn, åtkomsttyp och principen som är mappad till användaren.
    
    b. Klicka på den **skapa användare** knappen i det nedre högra hörnet att skapa användaren.

1. Hämta autentiseringsuppgifter för en användare genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser4.png)
    
    a. Kopiera användaren **åtkomst ID-nyckeln** och **hemliga åtkomstnyckeln**.
    
    b. Ange autentiseringsuppgifterna i Azure AD-användare etablering avsnittet att hämta rollerna från AWS-konsolen.
    
    c. Klicka på **Stäng** längst ned.

1. Gå till **Användaretablering** avsnittet för Amazon Web Services-appen i Azure AD-hanteringsportalen.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning.png)

1. Ange den **åtkomstnyckeln** och **hemlighet** i den **Klienthemlighet** och **hemlighet Token** fältet respektive.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning1.png)
    
    a. Ange åtkomstnyckel för AWS-användare i den **clientsecret** fält.
    
    b. Ange hemlighet för AWS-användare i den **hemlighet Token** fält.
    
    c. Klicka på den **Testanslutning** knappen och du bör testa har den här anslutningen.

    d. Spara inställningen genom att klicka på den **spara** längst upp.
 
1. Nu se till att du aktiverar den Etableringsstatus **på** i avsnittet inställningar genom att växla på och klicka sedan på den **spara** längst upp.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning2.png)

> [!Note]
> Om du vill integrera flera AWS-konton till en Azure-konto för enkelinloggning på [detta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artikeln. 


### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/amazon-web-service-tutorial/create_aaduser_01.png)

1. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/amazon-web-service-tutorial/create_aaduser_02.png)

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/amazon-web-service-tutorial/create_aaduser_03.png)

1. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/amazon-web-service-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Skapa en testanvändare i Amazon Web Services (AWS)

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Amazon Web Services (AWS). Amazon Web Services (AWS) behöver inte en användare som ska skapas i deras system för enkel inloggning, så du inte behöver göra någonting här.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Amazon Web Services (AWS).

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon till Amazon Web Services (AWS), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Amazon Web Services (AWS)**.

    ![Länken Amazon Web Services (AWS) i listan med program](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)  

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomstpanelen du bör få automatiskt loggat in på ditt program för Amazon Web Services (AWS). Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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
