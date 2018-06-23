---
title: 'Självstudier: Azure Active Directory-integrering med iPass SmartConnect | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iPass SmartConnect.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.openlocfilehash: 5ae600142f7e90a7f6185c3a948a4174ce4c7797
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320529"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Självstudier: Azure Active Directory-integrering med iPass SmartConnect

I kursen får lära du att integrera iPass SmartConnect med Azure Active Directory (AD Azure).

Integrera iPass SmartConnect med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till iPass SmartConnect.
- Du kan aktivera användarna att automatiskt hämta loggat in på iPass SmartConnect (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med iPass SmartConnect, behöver du följande:

- En Azure AD-prenumeration
- En iPass SmartConnect enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till iPass SmartConnect från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>Att lägga till iPass SmartConnect från galleriet
Du måste lägga till iPass SmartConnect från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av iPass SmartConnect i Azure AD.

**Utför följande steg för att lägga till iPass SmartConnect från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]

3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **iPass SmartConnect**väljer **iPass SmartConnect** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![iPass SmartConnect i resultatlistan](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med iPass SmartConnect baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, Azure AD som behöver veta vilka motsvarande användaren i iPass SmartConnect är att en användare i Azure AD. Med andra ord en länk förhållandet mellan en Azure AD-användare och relaterade användaren i iPass SmartConnect måste upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med iPass SmartConnect, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för iPass SmartConnect](#create-an-ipass-smartconnect-test-user)**  – du har en motsvarighet för Britta Simon i iPass SmartConnect som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din iPass SmartConnect program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med iPass SmartConnect:**

1. I Azure-portalen på den **iPass SmartConnect** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.

    ![Enkel inloggning dialogrutan](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

3. På den **iPass SmartConnect domän och URL: er** om du vill konfigurera programmet i **IDP** initierade läge, behöver du inte utföra några steg.

    ![iPass SmartConnect domän och URL: er med enkel inloggning information](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

4. Kontrollera att visa avancerade inställningar för URL: en och utföra följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![iPass SmartConnect domän och URL: er med enkel inloggning information](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    Ange en URL i textrutan inloggnings-URL: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

5. iPass SmartConnect program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.

    ![Konfigurera enkel inloggning](./media/ipasssmartconnect-tutorial/attribute.png)

6. Klicka på **visa och redigera andra användarattribut** kryssrutan i den **användarattribut** avsnittet för att expandera attribut. Utför följande steg på varje visas attribut-

    | Attributnamn | Attributvärde | Namespace-värde|
    | ---------------| --------------- |----------------|
    | Förnamn | User.givenName |   |
    | Efternamn | User.surname | |
    | e-post | User.userPrincipalName | |
    | användarnamn | User.userPrincipalName | |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Konfigurera enkel inloggning](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. I den **namn** textruta ange attributets namn visas för den raden.

    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.

    d. Behåll namnområdesvärdet tomt för den raden.

    e. Klicka på **OK**.

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

9. Konfigurera enkel inloggning på **iPass SmartConnect** sida, måste du skicka den hämtade **XML-Metadata för** och **domännamn** till [iPass SmartConnect supportteam](mailto:help@ipass.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.

### <a name="create-an-ipass-smartconnect-test-user"></a>Skapa en iPass SmartConnect testanvändare

I det här avsnittet skapar du en användare som kallas Britta Simon i iPass SmartConnect. Arbeta med [iPass SmartConnect supportteam](mailto:help@ipass.com) att lägga till användare eller domän som krävs för att vara godkända i iPass SmartConnect plattformen. Om domänen har lagts till av teamet, kommer användarna få automatiskt tillhandahållas för iPass SmartConnect-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till iPass SmartConnect.

![Tilldela rollen][200]

**Om du vill tilldela iPass SmartConnect Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201]

2. Välj i listan med program **iPass SmartConnect**.

    ![IPass SmartConnect länken i listan med program](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

**Utför följande steg om du vill testa programmet i SP-initierad-flöde:**

a. Hämta windows iPass SmartConnect klienten [här](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![IPass SmartConnect länken i listan med program](./media/ipasssmartconnect-tutorial/testing3.png)

b. Installera klienten och starta.

c. Klicka på **Kom igång**.

![IPass SmartConnect länken i listan med program](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Ange namn på Azure med domänen. Klicka på **fortsätta**. Detta kommer att omdirigeras till inloggningssidan för Azure

![IPass SmartConnect länken i listan med program](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Aktivering av klienten kommer att startas efter en lyckad autentisering. Klienten få aktiverats.

**Utför följande steg om du vill testa programmet i IdP initierade-flöde:**

a. Logga in på [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Klicka på iPass SmartConnect app.

c. Den startar SSA sidan, klickar du på **hämta App för Windows** installera iPass SmartConnect klienten.

![IPass SmartConnect länken i listan med program](./media/ipasssmartconnect-tutorial/testing4.png)

d. När klienten startas för första installationen kommer automatiskt startar aktivering när du godkänner villkoren.

e. Om aktiveringen inte startar, klicka på Aktivera SSA sidan för att påbörja aktiveringen.

f. Klienten få aktiverats.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

