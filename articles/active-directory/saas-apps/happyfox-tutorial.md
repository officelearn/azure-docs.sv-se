---
title: 'Självstudier: Azure Active Directory-integrering med HappyFox | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HappyFox.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8204ee77-f64b-4fac-b64a-25ea534feac0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2017
ms.author: jeedes
ms.openlocfilehash: a2aec602fc860b438de75e164ab79a2df8ae8604
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35968019"
---
# <a name="tutorial-azure-active-directory-integration-with-happyfox"></a>Självstudier: Azure Active Directory-integrering med HappyFox

I kursen får lära du att integrera HappyFox med Azure Active Directory (AD Azure).

Integrera HappyFox med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HappyFox
- Du kan aktivera användarna att automatiskt hämta loggat in på HappyFox (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med HappyFox, behöver du följande:

- En Azure AD-prenumeration
- En HappyFox enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till HappyFox från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-happyfox-from-the-gallery"></a>Att lägga till HappyFox från galleriet
Du måste lägga till HappyFox från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av HappyFox i Azure AD.

**Utför följande steg för att lägga till HappyFox från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **HappyFox**.

    ![Skapa en testanvändare i Azure AD](./media/happyfox-tutorial/tutorial_happyfox_search.png)

5. Välj i resultatpanelen **HappyFox**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/happyfox-tutorial/tutorial_happyfox_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med HappyFox baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i HappyFox motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i HappyFox upprättas.

I HappyFox, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med HappyFox, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare HappyFox](#creating-a-happyfox-test-user)**  – du har en motsvarighet för Britta Simon i HappyFox som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt HappyFox program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med HappyFox:**

1. I Azure-portalen på den **HappyFox** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_samlbase.png)

3. På den **HappyFox domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<subdomain>.happyfox.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<subdomain>.happyfox.com/saml/metadata/`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [HappyFox klienten supportteamet](https://support.happyfox.com/home) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_general_400.png)

6. På den **HappyFox Configuration** klickar du på **konfigurera HappyFox** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnittet**.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_configure.png) 

7. Logga in på din HappyFox personal portal och gå till **hantera**, klicka på **integreringar** fliken.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/header.png) 

8. Klicka på fliken integreringar **konfigurera** under **SAML Integration** att öppna enkel inloggning på inställningarna.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/configure.png) 

9. I avsnittet för SAML-konfiguration, klistra in den **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen i **mål-URL för enkel inloggning** textruta.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/targeturl.png)

10. Öppna certifikatet hämtas från Azure-portalen i anteckningar och klistra in innehållet i **IdP signatur** avsnitt.
 
    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/cert.png)

11. Klicka på **Spara inställningar** knappen.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/savesettings.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/happyfox-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/happyfox-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/happyfox-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/happyfox-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-happyfox-test-user"></a>Skapa en testanvändare HappyFox

Programmet stöder bara i tid användaretablering och authentication-användare skapas automatiskt i programmet.
        
### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HappyFox.

![Tilldela användare][200] 

**Om du vill tilldela HappyFox Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **HappyFox**.

    ![Konfigurera enkel inloggning](./media/happyfox-tutorial/tutorial_happyfox_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

1. Du bör få inloggningssidan för HappyFox program när du klickar på panelen HappyFox på åtkomstpanelen. Du bör se den **'SAML-** på sidan logga in.

    ![plugin-programmet](./media/happyfox-tutorial/saml.png) 

2. Klicka på den **'SAML-** knappen Logga in på HappyFox med hjälp av Azure AD-kontot.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/happyfox-tutorial/tutorial_general_01.png
[2]: ./media/happyfox-tutorial/tutorial_general_02.png
[3]: ./media/happyfox-tutorial/tutorial_general_03.png
[4]: ./media/happyfox-tutorial/tutorial_general_04.png

[100]: ./media/happyfox-tutorial/tutorial_general_100.png

[200]: ./media/happyfox-tutorial/tutorial_general_200.png
[201]: ./media/happyfox-tutorial/tutorial_general_201.png
[202]: ./media/happyfox-tutorial/tutorial_general_202.png
[203]: ./media/happyfox-tutorial/tutorial_general_203.png

