---
title: 'Självstudier: Azure Active Directory-integrering med Cisco Spark | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Cisco Spark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: bebc8d674d7448ea0ce6a1f11b7ae80335df9cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431706"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Självstudier: Azure Active Directory-integrering med Cisco Spark

Lär dig hur du integrerar Cisco Spark med Azure Active Directory (AD Azure) i den här självstudien.

Integrera Cisco Spark med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Cisco Spark
- Du kan aktivera användarna att automatiskt få loggat in på Cisco Spark (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Cisco Spark, behöver du följande objekt:

- En Azure AD-prenumeration
- En Cisco Spark enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Cisco Spark från galleriet
1. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-cisco-spark-from-the-gallery"></a>Att lägga till Cisco Spark från galleriet
För att konfigurera integrering av Cisco Spark i Azure AD, som du behöver lägga till Cisco Spark från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Cisco Spark från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

1. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
1. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

1. I sökrutan skriver **Cisco Spark**.

    ![Skapa en Azure AD-användare för testning](./media/cisco-spark-tutorial/tutorial_ciscospark_search.png)

1. I resultatpanelen väljer **Cisco Spark**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Cisco Spark baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du känna till motsvarande användare i Cisco Spark till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Cisco Spark upprättas.

I Cisco Spark, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Cisco Spark, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
1. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
1. **[Skapa en testanvändare Cisco Spark](#creating-a-cisco-spark-test-user)**  – du har en motsvarighet för Britta Simon i Cisco Spark som är länkad till en Azure AD-representation av användaren.
1. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
1. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Cisco Spark-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Cisco Spark:**

1. I Azure-portalen på den **Cisco Spark** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

1. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

1. På den **Cisco Spark domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL: en som: `https://web.ciscospark.com/#/signin`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska ID: T. Kontakta [Cisco Spark klienten supportteamet](https://support.ciscospark.com/) att hämta det här värdet. 
 
1. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

1. Cisco Spark-programmet förväntar sig SAML-intyg innehålla specifika attribut. Konfigurera följande attribut för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för detta.
    
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_ciscospark_07.png) 

1. I den **användarattribut** avsnittet på den **enkel inloggning** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Attributnamn  | Attributvärde |
    | --------------- | -------------------- |    
    |   UID    | User.userPrincipalName |   

    a. Klicka på **Lägg till attribut** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textrutan skriver du attributnamnet som visas för den raden.
    
    c. Från den **värdet** anger attributvärdet som visas för den raden.
    
    d. Klicka på **OK**.

1. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_general_400.png)

1. Logga in på [Cisco Molnhantering samarbete](https://admin.ciscospark.com/) med din fullständiga administratörsautentiseringsuppgifter.

1. Välj **inställningar** och under den **autentisering** klickar du på **ändra**.
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
1. Välj **integrera en identitetsprovider med 3 part. (Avancerat)**  och gå till nästa sida.

1. På den **importera Idp Metadata** sidan antingen dra och släpp Azure AD-metadatafil till sidan eller Använd filalternativet för webbläsare för att leta upp och ladda upp metadatafilen Azure AD. Välj **kräver certifikat som signerats av en certifikatutfärdare i Metadata (säkrare)** och klicka på **nästa**. 
    
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

1. Välj **SSO Testanslutningen**, och när en ny webbläsarflik öppnas autentisera med Azure AD genom att logga in.

1. Gå tillbaka till den **Cisco samarbete Molnhantering** flik i webbläsaren. Om testet lyckades, väljer **det här testet lyckades. Aktivera enkel inloggning för alternativet** och klicka på **nästa**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/cisco-spark-tutorial/create_aaduser_01.png) 

1. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/cisco-spark-tutorial/create_aaduser_02.png) 

1. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/cisco-spark-tutorial/create_aaduser_03.png) 

1. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/cisco-spark-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-cisco-spark-test-user"></a>Skapa en testanvändare Cisco Spark

I det här avsnittet skapar du en användare som kallas Britta Simon i Cisco Spark. I det här avsnittet skapar du en användare som kallas Britta Simon i Cisco Spark.

1. Gå till den [Cisco Molnhantering samarbete](https://admin.ciscospark.com/) med din fullständiga administratörsautentiseringsuppgifter.

1. Klicka på **användare** och sedan **hantera användare**.
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

1. I den **hantera användare** väljer **manuellt lägga till eller ändra användare** och klicka på **nästa**.

1. Välj **namn och e-postadress**. Sedan fylla i textrutan på följande sätt:
   
    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. I den **Förnamn** textrutan typ **Britta**. 
    
    b. I den **efternamn** textrutan typ **Simon**.
    
    c. I den **e-postadress** textrutan typ **britta.simon@contoso.com**.

1. Klicka på plustecknet för att lägga till Britta Simon. Klicka sedan på **Nästa**.

1. I den **Lägg till tjänster för användare** fönstret klickar du på **spara** och sedan **Slutför**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning om du beviljar åtkomst till Cisco Spark.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Cisco Spark, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

1. I listan med program väljer **Cisco Spark**.

    ![Konfigurera enkel inloggning](./media/cisco-spark-tutorial/tutorial_ciscospark_app.png) 

1. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

1. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

1. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

1. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

1. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen Cisco Spark i åtkomstpanelen du bör få automatiskt loggat in på ditt Cisco Spark-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/cisco-spark-tutorial/tutorial_general_203.png

