---
title: 'Självstudier: Azure Active Directory-integrering med Nomadesk | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Nomadesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: d261b776-b48e-45f0-9722-0297adefabb8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: jeedes
ms.openlocfilehash: df1b8325eafa05fc3c8a32b231ff095a4942f0f5
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963819"
---
# <a name="tutorial-azure-active-directory-integration-with-nomadesk"></a>Självstudier: Azure Active Directory-integrering med Nomadesk

I kursen får lära du att integrera Nomadesk med Azure Active Directory (AD Azure).

Integrera Nomadesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Nomadesk
- Du kan aktivera användarna att automatiskt hämta loggat in på Nomadesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Nomadesk, behöver du följande:

- En Azure AD-prenumeration
- En Nomadesk enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Nomadesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-nomadesk-from-the-gallery"></a>Att lägga till Nomadesk från galleriet
Du måste lägga till Nomadesk från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Nomadesk i Azure AD.

**Utför följande steg för att lägga till Nomadesk från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Nomadesk**.

    ![Skapa en testanvändare i Azure AD](./media/nomadesk-tutorial/tutorial_nomadesk_search.png)

5. Välj i resultatpanelen **Nomadesk**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/nomadesk-tutorial/tutorial_nomadesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Nomadesk baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Nomadesk motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Nomadesk upprättas.

I Nomadesk, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Nomadesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Nomadesk](#creating-a-nomadesk-test-user)**  – du har en motsvarighet för Britta Simon i Nomadesk som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Nomadesk program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Nomadesk:**

1. I Azure-portalen på den **Nomadesk** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/nomadesk-tutorial/tutorial_nomadesk_samlbase.png)

3. På den **Nomadesk domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/nomadesk-tutorial/tutorial_nomadesk_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://mynomadesk.com/logon/saml/<TENANTID>`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://secure.nomadesk.com/saml/<instancename>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Nomadesk klienten supportteamet](mailto:support@nomadesk.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/nomadesk-tutorial/tutorial_nomadesk_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/nomadesk-tutorial/tutorial_general_400.png)

6. På den **Nomadesk Configuration** klickar du på **konfigurera Nomadesk** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/nomadesk-tutorial/tutorial_nomadesk_configure.png) 

7. Konfigurera enkel inloggning på **Nomadesk** sida, måste du skicka den hämtade **certifikat**, **Sign-Out URL, SAML enhets-ID och SAML inloggning tjänst-URL för enkel** till [Nomadesk supportteamet](mailto:support@nomadesk.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/nomadesk-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/nomadesk-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/nomadesk-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/nomadesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-nomadesk-test-user"></a>Skapa en testanvändare Nomadesk

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i Nomadesk. Nomadesk stöder just-in-time-etablering, vilket är aktiverat som standard.

Det finns ingen åtgärd objekt i det här avsnittet. En ny användare skapas under ett försök att komma åt Nomadesk om den inte finns.

>[!NOTE]
>Om du behöver skapa en användare manuellt, måste du kontakta den [Nomadesk supportteamet](mailto:support@nomadesk.com). 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Nomadesk.

![Tilldela användare][200] 

**Om du vill tilldela Nomadesk Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Nomadesk**.

    ![Konfigurera enkel inloggning](./media/nomadesk-tutorial/tutorial_nomadesk_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Nomadesk på åtkomstpanelen du bör få automatiskt loggat in på ditt Nomadesk program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/nomadesk-tutorial/tutorial_general_01.png
[2]: ./media/nomadesk-tutorial/tutorial_general_02.png
[3]: ./media/nomadesk-tutorial/tutorial_general_03.png
[4]: ./media/nomadesk-tutorial/tutorial_general_04.png

[100]: ./media/nomadesk-tutorial/tutorial_general_100.png

[200]: ./media/nomadesk-tutorial/tutorial_general_200.png
[201]: ./media/nomadesk-tutorial/tutorial_general_201.png
[202]: ./media/nomadesk-tutorial/tutorial_general_202.png
[203]: ./media/nomadesk-tutorial/tutorial_general_203.png

