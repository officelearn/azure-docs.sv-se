---
title: 'Självstudier: Azure Active Directory-integrering med Certifikatutfärdare PPM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Certifikatutfärdaren PPM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: ca9d5e71-e429-4891-8d10-3498e7210e89
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: 47dac840cd4c9b76f5ae63681390c6e439518c92
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964141"
---
# <a name="tutorial-azure-active-directory-integration-with-ca-ppm"></a>Självstudier: Azure Active Directory-integrering med Certifikatutfärdare PPM

I kursen får lära du att integrera CA PPM med Azure Active Directory (AD Azure).

Integrera CA PPM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till CA PPM
- Du kan aktivera användarna att automatiskt hämta loggat in på CA PPM (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Certifikatutfärdare PPM, behöver du följande:

- En Azure AD-prenumeration
- En CA PPM enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till CA PPM från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ca-ppm-from-the-gallery"></a>Att lägga till CA PPM från galleriet
Du måste lägga till CA PPM från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Certifikatutfärdaren PPM i Azure AD.

**Utför följande steg för att lägga till CA PPM från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **CA PPM**.

    ![Skapa en testanvändare i Azure AD](./media/cappm-tutorial/tutorial_cappm_search.png)

5. Välj i resultatpanelen **CA PPM**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/cappm-tutorial/tutorial_cappm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med CA PPM baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i Certifikatutfärdaren PPM till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Certifikatutfärdaren PPM upprättas.

I Certifikatutfärdarens PPM tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med CA PPM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare CA PPM](#creating-a-ca-ppm-test-user)**  – du har en motsvarighet för Britta Simon i CA-PPM som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt CA PPM-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med CA PPM:**

1. I Azure-portalen på den **CA PPM** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/cappm-tutorial/tutorial_cappm_samlbase.png)

3. På den **URL: er och CA PPM domän** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/cappm-tutorial/tutorial_cappm_url.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster: `https://ca.ondemand.saml.20.post.<companyname>`
    
    b. I den **Reply URL** textruta typ som: `https://fedsso.ondemand.ca.com/affwebservices/public/saml2assertionconsumer`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med den faktiska identifieraren. Kontakta [CA PPM supportteamet](mailto:catechnicalsupport@ca.com) att hämta det här värdet.
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/cappm-tutorial/tutorial_cappm_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/cappm-tutorial/tutorial_general_400.png)

6. På den **PPM certifikatutfärdarkonfiguration** klickar du på **konfigurera Certifikatutfärdaren PPM** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/cappm-tutorial/tutorial_cappm_configure.png) 

7. Konfigurera enkel inloggning på **CA PPM** sida, måste du skicka den hämtade **Certificate(Base64)** och **SAML enhets-ID** till [CA PPM supportteamet](mailto:catechnicalsupport@ca.com).

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/cappm-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/cappm-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/cappm-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/cappm-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-ca-ppm-test-user"></a>Skapa en CA PPM testanvändare

I det här avsnittet kan du skapa en användare som kallas Britta Simon i Certifikatutfärdaren PPM. Arbeta med [CA PPM supportteamet](mailto:catechnicalsupport@ca.com) att lägga till användare i Certifikatutfärdaren PPM-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till CA PPM.

![Tilldela användare][200] 

**Om du vill tilldela CA PPM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **CA PPM**.

    ![Konfigurera enkel inloggning](./media/cappm-tutorial/tutorial_cappm_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du klickar på panelen CA PPM på åtkomstpanelen du bör få automatiskt loggat in på ditt CA PPM-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cappm-tutorial/tutorial_general_01.png
[2]: ./media/cappm-tutorial/tutorial_general_02.png
[3]: ./media/cappm-tutorial/tutorial_general_03.png
[4]: ./media/cappm-tutorial/tutorial_general_04.png

[100]: ./media/cappm-tutorial/tutorial_general_100.png

[200]: ./media/cappm-tutorial/tutorial_general_200.png
[201]: ./media/cappm-tutorial/tutorial_general_201.png
[202]: ./media/cappm-tutorial/tutorial_general_202.png
[203]: ./media/cappm-tutorial/tutorial_general_203.png

