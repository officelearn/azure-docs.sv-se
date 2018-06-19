---
title: 'Självstudier: Azure Active Directory-integrering med Fieldglass | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Fieldglass.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2510195f-d5b1-4684-b3da-283fb8619df2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 5becf5aea7921277c9116de61a6e6907268dae68
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35978222"
---
# <a name="tutorial-azure-active-directory-integration-with-fieldglass"></a>Självstudier: Azure Active Directory-integrering med Fieldglass

I kursen får lära du att integrera Fieldglass med Azure Active Directory (AD Azure).

Integrera Fieldglass med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Fieldglass
- Du kan aktivera användarna att automatiskt hämta loggat in på Fieldglass (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Fieldglass, behöver du följande:

- En Azure AD-prenumeration
- En Fieldglass enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Fieldglass från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-fieldglass-from-the-gallery"></a>Att lägga till Fieldglass från galleriet
Du måste lägga till Fieldglass från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Fieldglass i Azure AD.

**Utför följande steg för att lägga till Fieldglass från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Fieldglass**.

    ![Skapa en testanvändare i Azure AD](./media/fieldglass-tutorial/tutorial_fieldglass_search.png)

5. Välj i resultatpanelen **Fieldglass**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/fieldglass-tutorial/tutorial_fieldglass_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Fieldglass baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Fieldglass motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Fieldglass upprättas.

I Fieldglass, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Fieldglass, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Fieldglass](#creating-a-fieldglass-test-user)**  – du har en motsvarighet för Britta Simon i Fieldglass som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Fieldglass program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Fieldglass:**

1. I Azure-portalen på den **Fieldglass** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/fieldglass-tutorial/tutorial_fieldglass_samlbase.png)

3. På den **Fieldglass domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/fieldglass-tutorial/tutorial_fieldglass_url.png)

    a. I den **identifierare** textruta Skriv en URL som `https://www.fieldglass.com` eller följer mönstret:  `https://<company name>.fgvms.com`

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://www.fieldglass.net/<company name>`|
    | `https://<company name>.fgvms.com/<company name>`|

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare och Reply-URL. Kontakta [Fieldglass supportteam](http://www.fieldglass.com/solutions/support) att hämta dessa värden.
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/fieldglass-tutorial/tutorial_fieldglass_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/fieldglass-tutorial/tutorial_general_400.png)

6. På den **Fieldglass Configuration** klickar du på **konfigurera Fieldglass** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enhets-ID** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/fieldglass-tutorial/tutorial_fieldglass_configure.png) 

7. Konfigurera enkel inloggning på **Fieldglass** sida, måste du skicka den hämtade **Certificate(Base64)** och **Sign-Out URL, SAML enhets-ID** till [Fieldglass supportteam](http://www.fieldglass.com/solutions/support). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/fieldglass-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/fieldglass-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/fieldglass-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/fieldglass-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-fieldglass-test-user"></a>Skapa en testanvändare Fieldglass

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i FieldGlass. Se tillsammans med din [Fieldglass supportteam](http://www.fieldglass.com/solutions/support) att lägga till användare i Fieldglass-konto.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Fieldglass.

![Tilldela användare][200] 

**Om du vill tilldela Fieldglass Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Fieldglass**.

    ![Konfigurera enkel inloggning](./media/fieldglass-tutorial/tutorial_fieldglass_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Fieldglass på åtkomstpanelen du bör få automatiskt loggat in på ditt Fieldglass program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/fieldglass-tutorial/tutorial_general_01.png
[2]: ./media/fieldglass-tutorial/tutorial_general_02.png
[3]: ./media/fieldglass-tutorial/tutorial_general_03.png
[4]: ./media/fieldglass-tutorial/tutorial_general_04.png

[100]: ./media/fieldglass-tutorial/tutorial_general_100.png

[200]: ./media/fieldglass-tutorial/tutorial_general_200.png
[201]: ./media/fieldglass-tutorial/tutorial_general_201.png
[202]: ./media/fieldglass-tutorial/tutorial_general_202.png
[203]: ./media/fieldglass-tutorial/tutorial_general_203.png

