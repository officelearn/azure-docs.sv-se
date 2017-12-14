---
title: "Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS) | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amazon Web Services (AWS)."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 1678d44fc7769c2015c3779ce713870af7a40de9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS)

I kursen får lära du att integrera Amazon Web Services (AWS) med Azure Active Directory (AD Azure).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS)
- Du kan aktivera användarna att automatiskt hämta inloggade till Amazon Web Services (AWS) (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

<!--## Overview

To enable single sign-on with Amazon Web Services (AWS), it must be configured to use Azure Active Directory as an identity provider. This guide provides information and tips on how to perform this configuration in Amazon Web Services (AWS).

>[!Note]: 
>This embedded guide is brand new in the new Azure portal, and we’d love to hear your thoughts. Use the Feedback ? button at the top of the portal to provide feedback. The older guide for using the [Azure classic portal](https://manage.windowsazure.com) to configure this application can be found [here](https://github.com/Azure/AzureAD-App-Docs/blob/master/articles/en-us/_/sso_configure.md).-->


## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS) behöver du följande:

- En Azure AD-prenumeration
- Amazon Web Services (AWS) enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Amazon Web Services (AWS) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Att lägga till Amazon Web Services (AWS) från galleriet
Du måste lägga till Amazon Web Services (AWS) från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Amazon Web Services (AWS) i Azure AD.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I den  **[Azure Portal](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Amazon Web Services (AWS)**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_search.png)

5. Välj i resultatpanelen **Amazon Web Services (AWS)**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Amazon Web Services (AWS) till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Amazon Web Services (AWS).

Om du vill konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Amazon Web Services](#creating-an-amazon-web-services-test-user)**  – du har en motsvarighet för Britta Simon i Amazon Web Services (AWS) och som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. I Azure-portalen på den **Amazon Web Services (AWS)** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_samlbase.png)

3. På den **Amazon Web Services (AWS)-domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_url.png)

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_certificate.png)

5. Amazon Web Services (AWS) program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_attribute.png)

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://AWS.Amazon.com/SAML/Attributes |
    | Roll            | User.assignedroles |  https://AWS.Amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Du måste konfigurera användaretablering i Azure AD för att hämta alla roller från AWS-konsolen. Se etablering stegen nedan.

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_04.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_attribute_05.png)

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden. Lägg till Namespace-värde som anges ovan.
    
    d. Klicka på **OK**.

7. Klicka på **spara** för att spara inställningarna på Azure.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_400.png)

8. I ett annat webbläsarfönster inloggning till webbplatsen Amazon Web Services (AWS) företag som administratör.

9. Klicka på **konsolen Home**.
   
    ![Konfigurera enkel inloggning][11]

10. Klicka på **IAM** från **säkerhet, identitet och efterlevnad** service.
   
    ![Konfigurera enkel inloggning][12]

11. Klicka på **identitetsleverantörer**, och klicka sedan på **skapa providern**.
   
    ![Konfigurera enkel inloggning][13]

12. På den **konfigurera Provider** dialogrutan utför följande steg:
   
    ![Konfigurera enkel inloggning][14]
 
    a. Som **providertyp**väljer **SAML**.

    b. I den **providernamn** textruta skriver ett provider-namn (t.ex.: *trä*).

    c. Om du vill överföra din hämtade metadatafil klickar du på **Välj fil**.

    d. Klicka på **nästa steg**.

13. På den **Kontrollera Providerinformationen** dialogrutan sidan, klickar du på **skapa**. 
    
    ![Konfigurera enkel inloggning][15]

14. Klicka på **roller**, och klicka sedan på **Skapa ny roll**. 
    
    ![Konfigurera enkel inloggning][16]

15. På den **ange rollnamn** dialogrutan, utför följande steg: 
    
    ![Konfigurera enkel inloggning][17] 

    a. I den **rollnamn** textruta, ange ett rollnamn (t.ex.: *TestUser*). 

    b. Klicka på **nästa steg**.

16. På den **Välj typ av roll** dialogrutan, utför följande steg: 
    
    ![Konfigurera enkel inloggning][18] 

    a. Välj **roll för identitets-providerns åtkomst**. 

    b. I den **bevilja Web Single Sign-On (WebSSO) åtkomst till SAML-providers** klickar du på **Välj**.

17. På den **upprätta förtroende** dialogrutan, utför följande steg:  
    
    ![Konfigurera enkel inloggning][19] 

    a. Välj SAML-provider som du har skapat tidigare som SAML-provider (t.ex.: *trä*)
  
    b. Klicka på **nästa steg**.

18. På den **Kontrollera rollen förtroende** dialogrutan klickar du på **nästa steg**.
    
    ![Konfigurera enkel inloggning][32]

19. På den **koppla principen** dialogrutan klickar du på **nästa steg**.
    
    ![Konfigurera enkel inloggning][33]

20. På den **granska** dialogrutan, utför följande steg:
    
    ![Konfigurera enkel inloggning][34]
 
    a. Klicka på **skapa roll**.

    b. Skapa så många roller vid behov och koppla dem till identitetsleverantören.

21. Konfigurera användaretablering för att hämta alla roller från AWS

    a. I AWS-konsolen logga in med ditt konto rot

    b. Klicka på ditt namn i övre högra hörnet och klicka sedan på den **Mina säkerhetsreferenser** alternativet. Då öppnas en skärm som en varning visas. Klicka på knappen **säkerhetsreferenser** knapp för att ge skärmen.
        
       ![Konfigurera enkel inloggning][36]

       ![Konfigurera enkel inloggning][37]

    c. I avsnittet snabbtangenter klickar du på den **skapa nya åtkomstnyckel** knappen. Detta genererar åtkomst nyckel-ID och ett token värde.
    
       ![Konfigurera enkel inloggning][38]

    d. Kopiera båda dessa värden och även hämta den, så att du inte förlorar den.

    e. Klicka på Azure Portal på sidan Amazon Web Services (AWS) program integration **etablering**.
        
       ![Konfigurera enkel inloggning][35]

    f. Anger läget för etablering till **automatisk**
        
       ![Konfigurera enkel inloggning][39]

    g. I den **clientsecret** och **hemlighet Token** klistra in motsvarande värden som du har kopierat från AWS-konsolen.
    
    h. Du kan klicka på den **Testa anslutning** knappen för att testa anslutningen. När det går bra kan du starta etablering kopplingen.
       
       ![Konfigurera enkel inloggning][40]

    Jag. Nu aktivera etablering Status till **på**. Detta startar hämtning av rollerna från programmet.

       ![Konfigurera enkel inloggning][41]

    > [!NOTE]
    > Azure AD-etablering-tjänsten körs varje efter en stund att synkronisera roller från AWS. Du bör se alla identitetsleverantör ansluten AWS roller till Azure AD och du kan använda dem vid tilldela program till användare eller grupper.

<!--### Next steps

To ensure users can sign-in to Amazon Web Services (AWS) after it has been configured to use Azure Active Directory, review the following tasks and topics:

- User accounts must be pre-provisioned into Amazon Web Services (AWS) prior to sign-in. To set this up, see Provisioning.
 
- Users must be assigned access to Amazon Web Services (AWS) in Azure AD to sign-in. To assign users, see Users.
 
- To configure access polices for Amazon Web Services (AWS) users, see Access Policies.
 
- For additional information on deploying single sign-on to users, see [this article](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis#deploying-azure-ad-integrated-applications-to-users).-->


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-amazon-web-service-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-amazon-web-services-test-user"></a>Skapa en testanvändare Amazon Web Services

De måste etableras i Amazon Web Services (AWS) för att aktivera Azure AD-användare kan logga in till Amazon Web Services (AWS). När det gäller Amazon Web Services (AWS) är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Amazon Web Services (AWS)** företagets webbplats som administratör.

2. Klicka på den **konsolen Home** ikon. 
   
    ![Konfigurera enkel inloggning][11]

3. Klicka på identitet och åtkomsthantering. 
   
    ![Konfigurera enkel inloggning][28]

4. I instrumentpanelen, klickar du på **användare**, och klicka sedan på **Create New Users**. 
   
    ![Konfigurera enkel inloggning][29]

5. I dialogrutan Skapa användare utför du följande steg: 
   
    ![Konfigurera enkel inloggning][30]   
    
    a. I den **ange användarnamn** textrutor, Skriv Brita Simon användarnamn (userprincipalname) i Azure AD.

    b. Klicka på **skapa.**
        
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning med sin åtkomst beviljas till Amazon Web Services (AWS).

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon till Amazon Web Services (AWS), utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Amazon Web Services (AWS)**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. På **Välj roll** väljer sedan rätt roll för användaren. Dessa roller visas med rollnamnet och identitetsnamn för providern. Det här sättet kan du enkelt identifiera roller från AWS.

8. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Amazon Web Services (AWS) på åtkomstpanelen du bör få automatiskt loggat in på ditt program för Amazon Web Services (AWS). 

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
[20]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950351.png
[21]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_80.png
[22]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950352.png
[23]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_81.png
[24]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950353.png
[25]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_general_15.png

[28]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950321.png
[29]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795037.png
[30]: ./media/active-directory-saas-amazon-web-service-tutorial/ic795038.png
[32]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950251.png
[33]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950252.png
[34]: ./media/active-directory-saas-amazon-web-service-tutorial/ic7950253.png
[35]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[36]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/active-directory-saas-amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
