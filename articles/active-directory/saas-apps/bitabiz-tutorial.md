---
title: 'Självstudier: Azure Active Directory-integrering med BitaBIZ | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BitaBIZ.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1a51e677-c62b-4aee-9c61-56926aaaa899
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 368625a325a1a36458491193315d67f139b53cb2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36223373"
---
# <a name="tutorial-azure-active-directory-integration-with-bitabiz"></a>Självstudier: Azure Active Directory-integrering med BitaBIZ

I kursen får lära du att integrera BitaBIZ med Azure Active Directory (AD Azure).

Integrera BitaBIZ med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till BitaBIZ.
- Du kan aktivera användarna att automatiskt hämta loggat in på BitaBIZ (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med BitaBIZ, behöver du följande:

- En Azure AD-prenumeration
- En BitaBIZ enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till BitaBIZ från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bitabiz-from-the-gallery"></a>Att lägga till BitaBIZ från galleriet
Du måste lägga till BitaBIZ från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av BitaBIZ i Azure AD.

**Utför följande steg för att lägga till BitaBIZ från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **BitaBIZ**väljer **BitaBIZ** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![BitaBIZ i resultatlistan](./media/bitabiz-tutorial/tutorial_bitabiz_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med BitaBIZ baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i BitaBIZ motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i BitaBIZ upprättas.

I BitaBIZ, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med BitaBIZ, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare BitaBIZ](#create-a-bitabiz-test-user)**  – du har en motsvarighet för Britta Simon i BitaBIZ som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt BitaBIZ program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med BitaBIZ:**

1. I Azure-portalen på den **BitaBIZ** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/bitabiz-tutorial/tutorial_bitabiz_samlbase.png)

3. På den **BitaBIZ domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i IDP initierade läge:

    ![URL: er och BitaBIZ domän med enkel inloggning information](./media/bitabiz-tutorial/tutorial_bitabiz_url.png)

    I den **identifierare** textruta Skriv en URL med följande mönster: `https://www.bitabiz.com/<instanceId>`

    > [!NOTE] 
    > Värdet i URL: en ovan är bara exempel. Uppdatera värdet med det faktiska ID, som beskrivs senare i självstudierna.

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och BitaBIZ domän med enkel inloggning information](./media/bitabiz-tutorial/tutorial_bitabiz_url1.png)

    I den **inloggnings-URL** textruta anger du URL: `https://www.bitabiz.com/dashboard`

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/bitabiz-tutorial/tutorial_bitabiz_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/bitabiz-tutorial/tutorial_general_400.png)
    
7. På den **BitaBIZ Configuration** klickar du på **konfigurera BitaBIZ** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![BitaBIZ konfiguration](./media/bitabiz-tutorial/tutorial_bitabiz_configure.png) 

8. I en annan webbläsarfönster inloggning till BitaBIZ-klient som administratör.

9. Klicka på **installationsprogrammet ADMIN**.

    ![BitaBIZ konfiguration](./media/bitabiz-tutorial/settings1.png)

10. Klicka på **Microsoft integreringar** under **lägga till värdet** avsnitt.

    ![BitaBIZ konfiguration](./media/bitabiz-tutorial/settings2.png)

11. Rulla ned till avsnittet **Microsoft Azure AD (aktivera enkel inloggning)** och utför följande steg:

    ![BitaBIZ konfiguration](./media/bitabiz-tutorial/settings3.png)

    a. Kopiera värdet från den **enhets-ID (”ID” i Azure AD)** textruta och klistrar in det i den **identifierare** textruta på den **BitaBIZ domän och URL: er** avsnitt i Azure-portalen. 
    
    b. I den **Azure AD-inloggning med tjänst-URL för enkel** textruta klistra in **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
    
    c. I den **enhets-ID för Azure AD SAML** textruta klistra in **SAML enhets-ID**, som du har kopierat från Azure-portalen.

    d. Öppna din hämtade **Certificate(Base64)** fil i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **Azure AD signeringscertifikat (Base64-kodade)** textruta.

    e. Lägg till din e-postdomän för företag som är namnet tilldelats i **domännamn** textruta för att tilldela användare i ditt företag med den här e-postdomän SSO (inte obligatoriskt).
    
    f. Markera **SSO aktiverad** BitaBIZ-konto.
    
    g. Klicka på **spara konfigurationen av Azure AD** att spara och aktivera SSO-konfiguration.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/bitabiz-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/bitabiz-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/bitabiz-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/bitabiz-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-bitabiz-test-user"></a>Skapa en testanvändare BitaBIZ

Om du vill aktivera Azure AD-användare kan logga in på BitaBIZ etableras de i BitaBIZ.  
När det gäller BitaBIZ är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på webbplatsen BitaBIZ företag som administratör.

2. Klicka på **installationsprogrammet ADMIN**.

    ![BitaBIZ lägga till användare](./media/bitabiz-tutorial/settings1.png)

3. Klicka på **lägga till användare** under **organisation** avsnitt.

    ![BitaBIZ lägga till användare](./media/bitabiz-tutorial/user1.png)

4. Klicka på **Lägg till ny medarbetare**.

    ![BitaBIZ lägga till användare](./media/bitabiz-tutorial/user2.png)

5. På den **”Lägg till ny medarbetare”** dialogrutan utför följande steg:

    ![BitaBIZ lägga till användare](./media/bitabiz-tutorial/user3.png)

    a. I den **Förnamn** textruta, ange först namnet på användaren som Britta.

    b. I den **efternamn** textruta anger efternamn för användaren som Simon.

    c. I den **e-post** textruta typen e-postadressen för användaren som Brittasimon@contoso.com.

    d. Välj ett datum i **datum för anställningen**.

    e. Det finns andra icke-obligatoriska användarattribut som kan ställas in för användaren. Mer information finns i [medarbetare installationsprogrammet Doc](https://help.bitabiz.dk/manage-or-set-up-your-account/on-boarding-employees/new-employee) för mer information.    
    
    f. Klicka på **spara medarbetare**.
    
    > [!NOTE]
    > Azure Active Directory kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras.
    
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till BitaBIZ.

![Tilldela rollen][200] 

**Om du vill tilldela BitaBIZ Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **BitaBIZ**.

    ![Länken BitaBIZ i listan med program](./media/bitabiz-tutorial/tutorial_bitabiz_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen BitaBIZ på åtkomstpanelen du bör få automatiskt loggat in på ditt BitaBIZ program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bitabiz-tutorial/tutorial_general_01.png
[2]: ./media/bitabiz-tutorial/tutorial_general_02.png
[3]: ./media/bitabiz-tutorial/tutorial_general_03.png
[4]: ./media/bitabiz-tutorial/tutorial_general_04.png

[100]: ./media/bitabiz-tutorial/tutorial_general_100.png

[200]: ./media/bitabiz-tutorial/tutorial_general_200.png
[201]: ./media/bitabiz-tutorial/tutorial_general_201.png
[202]: ./media/bitabiz-tutorial/tutorial_general_202.png
[203]: ./media/bitabiz-tutorial/tutorial_general_203.png

