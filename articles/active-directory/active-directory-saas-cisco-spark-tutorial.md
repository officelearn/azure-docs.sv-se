---
title: "Självstudier: Azure Active Directory-integrering med Cisco Spark | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Spark."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: a0a221622afe1c801a331e2319f3a7ace3111dad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Självstudier: Azure Active Directory-integrering med Cisco Spark

I kursen får lära du att integrera Cisco Spark med Azure Active Directory (AD Azure).

Integrera Cisco Spark med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cisco Spark
- Du kan aktivera användarna att automatiskt hämta loggat in på Cisco Spark (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Cisco Spark, behöver du följande:

- En Azure AD-prenumeration
- En Cisco-Spark enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Cisco Spark från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cisco-spark-from-the-gallery"></a>Att lägga till Cisco Spark från galleriet
Du måste lägga till Cisco Spark från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Cisco Spark i Azure AD.

**Utför följande steg för att lägga till Cisco Spark från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Cisco Spark**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. Välj i resultatpanelen **Cisco Spark**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Cisco Spark baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Cisco Spark motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Cisco Spark upprättas.

I Cisco Spark, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco Spark, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Cisco Spark](#creating-a-cisco-spark-test-user)**  – du har en motsvarighet för Britta Simon i Cisco Spark som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i Cisco Spark-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cisco Spark:**

1. I Azure-portalen på den **Cisco Spark** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. På den **Cisco Spark domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL som:`https://web.ciscospark.com/#/signin`

    b. I den **identifierare** textruta Skriv en URL med följande mönster:`https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [Cisco Spark klient supportteamet](https://support.ciscospark.com/) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. Cisco Spark program förväntar SAML intyg som innehåller specifika attribut. Konfigurera följande attribut för det här programmet. Du kan hantera värden för attributen från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributets namn  | Attributvärdet |
    | --------------- | -------------------- |    
    |   UID    | User.userPrincipalName |   

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **OK**.

7. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_400.png)

8. Logga in på [Cisco Molnhantering samarbete](https://admin.ciscospark.com/) med din fullständiga administratörsautentiseringsuppgifter.

9. Välj **inställningar** och under den **autentisering** klickar du på **ändra**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. Välj **integrera en identitetsleverantör med 3 part. (Avancerat)**  och gå till nästa sida.

11. På den **importera Idp Metadata** sidan antingen dra och släppa Azure AD-metadatafil till sidan eller Använd en webbläsare filalternativet Leta upp och ladda upp Azure AD-metadatafil. Markera **kräver certifikat som signerats av en certifikatutfärdare i Metadata (säkrare)** och på **nästa**. 
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. Välj **SSO Testanslutningen**, och när en ny webbläsarflik öppnas autentisera med Azure AD genom att logga in.

13. Gå tillbaka till den **Cisco samarbete Molnhantering** flik i webbläsaren. Om testet lyckades, väljer **det här testet lyckades. Aktivera enkel inloggning alternativet** och på **nästa**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-cisco-spark-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Skapa en testanvändare Cisco Spark

I det här avsnittet skapar du en användare som kallas Britta Simon i Cisco Spark. I det här avsnittet skapar du en användare som kallas Britta Simon i Cisco Spark.

1. Gå till den [Cisco Molnhantering samarbete](https://admin.ciscospark.com/) med din fullständiga administratörsautentiseringsuppgifter.

2. Klicka på **användare** och sedan **hantera användare**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. I den **hantera användare** väljer **manuellt lägga till eller ändra användare** och på **nästa**.

4. Välj **namn och e-postadress**. Fyll i textrutan på följande sätt:
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. I den **Förnamn** textruta typen **Britta**. 
    
    b. I den **efternamn** textruta typen **Simon**.
    
    c. I den **e-postadress** textruta typen  **britta.simon@contoso.com** .

5. Klicka på plustecknet för att lägga till Britta Simon. Klicka sedan på **Nästa**.

6. I den **Lägg till tjänster för användare** -fönstret klickar du på **spara** och sedan **Slutför**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Cisco Spark.

![Tilldela användare][200] 

**Om du vill tilldela Cisco Spark Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Cisco Spark**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Cisco Spark på åtkomstpanelen du bör få automatiskt loggat in på ditt Cisco Spark-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-cisco-spark-tutorial/tutorial_general_203.png

