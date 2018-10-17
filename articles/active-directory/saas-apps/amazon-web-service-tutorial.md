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
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeedes
ms.openlocfilehash: 8e91fbf0befaef9088e9afaa6e69c0cb29ad4858
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363767"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services-aws"></a>Självstudier: Azure Active Directory-integrering med Amazon Web Services (AWS)

I den här självstudien får du lära dig hur du integrerar Amazon Web Services (AWS) med Azure Active Directory (AD Azure).

Integrera Amazon Web Services (AWS) med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Amazon Web Services (AWS).
- Du kan aktivera användarna att automatiskt få loggat in till Amazon Web Services (AWS) (enkel inloggning) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS)](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Du kan konfigurera flera identifierare för flera instanser enligt nedan. 

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Med dessa värden, Azure AD tar bort värdet för **#** och skicka det korrekta värdet `https://signin.aws.amazon.com/saml` som målgrupp-URL: en i SAML-Token.

**Vi rekommenderar att du använder den här metoden av följande skäl:**

a. Varje program ger du unika X509 certifikat och så varje instans kan ha ett annat certifikat förfallodatum och du kan hantera detta på individuellt för AWS-konto. Övergripande förnya certifikatet kommer att vara enkelt i det här fallet.

b. Du kan aktivera etableringen av användare med AWS-app i Azure AD och sedan vår tjänst hämtar alla roller från det AWS-kontot. Du behöver inte manuellt lägger till eller uppdaterar AWS-roller på appen.

c. Du kan tilldela appägaren individuellt för appen som kan hantera appen direkt i Azure AD.

> [!Note]
> Kontrollera att du använder galleri-App

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Amazon Web Services (AWS), behöver du följande objekt:

- En Azure AD-prenumeration
- En Amazon Web Services (AWS) enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

> [!Note]
> Om du vill integrera flera AWS-konton till en Azure-konto för enkelinloggning på [detta](https://docs.microsoft.com/azure/active-directory/active-directory-saas-aws-multi-accounts-tutorial) artikeln.

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Amazon Web Services (AWS) från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Att lägga till Amazon Web Services (AWS) från galleriet
För att konfigurera integrering av Amazon Web Services (AWS) till Azure AD, som du behöver lägga till Amazon Web Services (AWS) från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Amazon Web Services (AWS) från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![image](./media/amazon-web-service-tutorial/selectazuread.png)

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![image](./media/amazon-web-service-tutorial/a_select_app.png)
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![image](./media/amazon-web-service-tutorial/a_new_app.png)

4. I sökrutan skriver **Amazon Web Services (AWS)** väljer **Amazon Web Services (AWS)** resultatet panelen klickar **Lägg till** för att lägga till programmet.

     ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS) baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Amazon Web Services (AWS) till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Amazon Web Services (AWS) upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med Amazon Web Services (AWS), måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare i Amazon Web Services (AWS)](#create-an-amazon-web-services-aws-test-user)**  – du har en motsvarighet för Britta Simon i Amazon Web Services (AWS) och som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska du aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Amazon Web Services (AWS).

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Amazon Web Services (AWS):**

1. I den [Azure-portalen](https://portal.azure.com/)på den **Amazon Web Services (AWS)** application integration markerar **enkel inloggning**.

    ![image](./media/amazon-web-service-tutorial/B1_B2_Select_SSO.png)

2. På den **väljer du en metod för enkel inloggning** dialogrutan Välj **SAML** läge för att aktivera enkel inloggning.

    ![image](./media/amazon-web-service-tutorial/b1_b2_saml_sso.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![image](./media/amazon-web-service-tutorial/b1-domains_and_urlsedit.png)

4. På den **SAML grundkonfiguration** avsnittet användaren behöver inte utföra några steg som appen är redan förintegrerade med Azure.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_url.png)

5. När du konfigurerar mer än en instans, ange ID-värde. Från och med andra instansen, ange ID-värde i följande format. Använd en **#** logga kan ange ett unikt värde för SPN. 

    `https://signin.aws.amazon.com/saml#2`

    ![Amazon Web Services (AWS)-domän och URL: er med enkel inloggning för information](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_identifier.png)

6. Amazon Web Services (AWS) program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut och anspråk** avsnitt på sidan för integrering av programmet. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](./media/amazon-web-service-tutorial/i4-attribute.png)

7. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Namn  | Attribut för datakälla  | Namnrymd |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | User.userPrincipalName | https://aws.amazon.com/SAML/Attributes |
    | Roll            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | ”Ange ett värde mellan 900 sekunder (15 minuter) till 43200 sekunder (12 timmar)” |  https://aws.amazon.com/SAML/Attributes |

    a. Klicka på **Lägg till nytt anspråk** att öppna den **hantera användaranspråk** dialogrutan.

    ![image](./media/amazon-web-service-tutorial/i2-attribute.png)

    ![image](./media/amazon-web-service-tutorial/i3-attribute.png)

    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.

    c. Ange den **Namespace** värde.

    d. Välj källa som **attributet**.

    e. Från den **källattribut** anger attributvärdet som visas för den raden.

    f. Klicka på **Spara**.

8. På den **ange in enkel inloggning med SAML** sidan den **SAML-signeringscertifikat** klickar du på **hämta** att ladda ned den **Federation Metadata-XML**  och spara den på din dator.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

9. I ett annat webbläsarfönster inloggning till webbplatsen för företagets Amazon Web Services (AWS) som administratör.

10. Klicka på **AWS Home**.

    ![Konfigurera enkel inloggning på startsidan][11]

11. Klicka på **Identity and Access Management**.

    ![Konfigurera identitet för enkel inloggning][12]

12. Klicka på **Identitetsprovidrar**, och klicka sedan på **skapa providern**.

    ![Konfigurera Provider för enkel inloggning][13]

13. På den **konfigurera Provider** dialogrutan utför följande steg:

    ![Konfigurera enkel inloggning för dialogrutan][14]

    a. Som **providertyp**väljer **SAML**.

    b. I den **providernamn** textrutan skriver du ett provider-namn (till exempel: *trä*).

    c. Ladda upp din hämtade **metadatafil** från Azure-portalen klickar du på **Välj fil**.

    d. Klicka på **nästa steg**.

14. På den **Kontrollera Providerinformationen** dialogrutan sidan, klickar du på **skapa**.

    ![Konfigurera enkel inloggning kontrollerar][15]

15. Klicka på **roller**, och klicka sedan på **skapa roll**.

    ![Konfigurera roller för enkel inloggning][16]

16. På den **skapa roll** utför följande steg:  

    ![Konfigurera enkel inloggning, förtroende][19]

    a. Välj **SAML 2.0 federation** under **Välj typ av betrodd entitet**.

    b. Under **välja ett avsnitt som SAML 2.0-providern**väljer den **SAML-providern** du skapade tidigare (till exempel: *trä*)

    c. Välj **Tillåt programmässiga och AWS-hanteringskonsolen åtkomst**.
  
    d. Klicka på **nästa: behörigheter**.

17. På den **bifoga Säkerhetsbehörighetsprinciper** dialogrutan, behöver du inte bifoga princip. Klicka på **nästa: granska**.  

    ![Konfigurera principen för enkel inloggning][33]

18. På den **granska** dialogrutan utför följande steg:

    ![Konfigurera granskning för enkel inloggning][34]

    a. I den **rollnamn** textrutan, ange ditt namn för rollen.

    b. I den **Rollbeskrivning** textrutan Ange beskrivningen.

    c. Klicka på **skapa roll**.

    d. Skapa så många roller efter behov och mappa den till identitetsleverantören.

19. Använd autentiseringsuppgifter för AWS-tjänstkontot för hämtning av rollerna från AWS-konto i Azure AD-användare etablering. Öppna konsolen AWS home till detta ändamål.

20. Klicka på **Services** -> **säkerhet, identitet och efterlevnad** -> **IAM**.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole1.png)

21. Välj den **principer** fliken i avsnittet IAM.

    ![hämtning av rollerna från AWS-konto](./media/amazon-web-service-tutorial/fetchingrole2.png)

22. Skapa en ny princip genom att klicka på **skapa princip** för hämtning av rollerna från AWS-konto i Azure AD-användare etablering.

    ![Skapar en ny princip](./media/amazon-web-service-tutorial/fetchingrole3.png)

23. Skapa en egen princip för att hämta alla roller från AWS-konton genom att utföra följande steg:

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

24. Definiera den **ny princip** genom att utföra följande steg:

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy2.png)

    a. Ange den **principnamn** som **AzureAD_SSOUserRole_Policy**.

    b. Du kan ange **beskrivning** för principen som **den här principen tillåter för att hämta rollerna från AWS-konton**.

    c. Klicka på **”skapa Policy”** knappen.

25. Skapa ett nytt användarkonto i AWS IAM-tjänsten genom att utföra följande steg:

    a. Klicka på **användare** navigering i AWS IAM-konsolen.

    ![Definiera den nya principen](./media/amazon-web-service-tutorial/policy3.png)

    b. Klicka på **Lägg till användare** för att skapa en ny användare.

    ![Lägga till användare](./media/amazon-web-service-tutorial/policy4.png)

    c. I den **Lägg till användare** avsnittet, utför följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser1.png)

    * Ange namnet på användaren som **AzureADRoleManager**.

    * I åtkomst-typ, väljer du den **Programmeringsåtkomst** alternativet. Det här sättet du kan anropa API: erna och hämta rollerna från AWS-konto.

    * Klicka på den **nästa behörigheter** knappen i nederkant högra hörnet.

26. Skapa nu en ny princip för den här användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser2.png)

    a. Klicka på den **koppla befintliga principer direkt** knappen.

    b. Sök efter den nya principen i avsnittet filter **AzureAD_SSOUserRole_Policy**.

    c. Välj den **princip** och klicka sedan på den **nästa: granska** knappen.

27. Granska principen till den anslutna användaren genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser3.png)

    a. Granska användarnamn, åtkomsttyp och principen som är mappad till användaren.

    b. Klicka på den **skapa användare** knappen i det nedre högra hörnet att skapa användaren.

28. Hämta autentiseringsuppgifter för en användare genom att utföra följande steg:

    ![Lägga till användare](./media/amazon-web-service-tutorial/adduser4.png)

    a. Kopiera användaren **åtkomst ID-nyckeln** och **hemliga åtkomstnyckeln**.

    b. Ange autentiseringsuppgifterna i Azure AD-användare etablering avsnittet att hämta rollerna från AWS-konsolen.

    c. Klicka på **Stäng** längst ned.

29. Gå till **Användaretablering** avsnittet för Amazon Web Services-appen i Azure AD-hanteringsportalen.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning.png)

30. Ange den **åtkomstnyckeln** och **hemlighet** i den **Klienthemlighet** och **hemlighet Token** fältet respektive.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Ange åtkomstnyckel för AWS-användare i den **clientsecret** fält.

    b. Ange hemlighet för AWS-användare i den **hemlighet Token** fält.

    c. Klicka på den **Testanslutning** knappen och du bör testa har den här anslutningen.

    d. Spara inställningen genom att klicka på den **spara** längst upp.

31. Nu se till att du aktiverar den Etableringsstatus **på** i avsnittet inställningar genom att växla på och klicka sedan på den **spara** längst upp.

    ![Lägga till användare](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

1. I Azure-portalen, i den vänstra rutan väljer **Azure Active Directory**väljer **användare**, och välj sedan **alla användare**.

    ![image](./media/amazon-web-service-tutorial/d_users_and_groups.png)

2. Välj **ny användare** överst på skärmen.

    ![image](./media/amazon-web-service-tutorial/d_adduser.png)

3. Utför följande steg i egenskaperna för användaren.

    ![image](./media/amazon-web-service-tutorial/d_userproperties.png)

    a. I den **namn** ange **BrittaSimon**.
  
    b. I den **användarnamn** fälttyp **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.
 
### <a name="create-an-amazon-web-services-aws-test-user"></a>Skapa en testanvändare i Amazon Web Services (AWS)

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Amazon Web Services (AWS). Amazon Web Services (AWS) behöver inte en användare som ska skapas i deras system för enkel inloggning, så du inte behöver göra någonting här.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att ge åtkomst till Amazon Web Services (AWS).

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![image](./media/amazon-web-service-tutorial/d_all_applications.png)

2. I listan med program väljer **Amazon Web Services (AWS)**.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices(aws)_app.png)

3. I menyn till vänster väljer **användare och grupper**.

    ![image](./media/amazon-web-service-tutorial/d_leftpaneusers.png)

4. Välj den **Lägg till** knappen och välj **användare och grupper** i den **Lägg till tilldelning** dialogrutan.

    ![image](./media/amazon-web-service-tutorial/d_assign_user.png)

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_users.png)

6. I den **Välj roll** dialogrutan Välj en lämplig användarroll i listan och klicka sedan på den **Välj** längst ned på skärmen.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_roles.png)

    >[!NOTE]
    >När du har aktiverat användaretablering med programmet, bör du måste vänta i 30 minuter att hämta alla roller från Amazon Web Services (AWS) måste du uppdatera sidan och sedan medan tilldela program till användare och grupper som du ser rollerna för användning r.

7. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

    ![image](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_assign.png)
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Amazon Web Services (AWS) i åtkomstpanelen du bör få automatiskt loggat in på ditt program för Amazon Web Services (AWS).
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

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
