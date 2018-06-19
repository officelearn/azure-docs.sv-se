---
title: 'Självstudier: Azure Active Directory-integrering med Bime | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bime.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: bdcf0729-c880-4c95-b739-0f6345b17dd8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: c63bfbb898befb1f968a190ccc2caa5bb2d33b57
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35963939"
---
# <a name="tutorial-azure-active-directory-integration-with-bime"></a>Självstudier: Azure Active Directory-integrering med Bime

I kursen får lära du att integrera Bime med Azure Active Directory (AD Azure).

Integrera Bime med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bime
- Du kan aktivera användarna att automatiskt hämta loggat in på Bime (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Bime, behöver du följande:

- En Azure AD-prenumeration
- En Bime enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad här: [utvärderingsversion erbjudande](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Bime från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-bime-from-the-gallery"></a>Att lägga till Bime från galleriet
Du måste lägga till Bime från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Bime i Azure AD.

**Utför följande steg för att lägga till Bime från galleriet:**

1. I den ** [Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Bime**.

    ![Skapa en testanvändare i Azure AD](./media/bime-tutorial/tutorial_bime_search.png)

5. Välj i resultatpanelen **Bime**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/bime-tutorial/tutorial_bime_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Bime baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Bime motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Bime upprättas.

I Bime, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Bime, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on) ** - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user) ** - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Bime](#creating-a-bime-test-user) ** – du har en motsvarighet för Britta Simon i Bime som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user) ** - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on) ** - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Bime program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Bime:**

1. I Azure-portalen på den **Bime** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_samlbase.png)

3. På den **Bime domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<tenant-name>.Bimeapp.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<tenant-name>.Bimeapp.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [Bime klienten supportteamet](https://bime.zendesk.com/hc/categories/202604307-Support-tech-notes-and-tips-) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värde från certifikatet.

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_general_400.png)

6. På den **Bime Configuration** klickar du på **konfigurera Bime** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_configure.png) 

7. Logga in på webbplatsen Bime företag som en administratör i en annan webbläsarfönster.

8. I verktygsfältet klickar du på **Admin**, och sedan **konto**.
   
    ![Admin](./media/bime-tutorial/ic775558.png "Admin")

9. Utför följande steg på konfigurationssidan för kontot:
   
    ![Konfigurera enkel inloggning](./media/bime-tutorial/ic775559.png "Konfigurera enkel inloggning")
   
    a. Välj **aktivera SAML-autentisering**.

    b. I den **Remote inloggnings-URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.

    c.  Klistra in den **tumavtrycket** värdet från Azure-portalen i den **certifikat fingeravtryck** textruta.       
   
    d. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/bime-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/bime-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/bime-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/bime-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-bime-test-user"></a>Skapa en testanvändare Bime

För att aktivera Azure AD-användare kan logga in på Bime etableras de i Bime. När det gäller Bime är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Bime** klient.

2. I verktygsfältet klickar du på **Admin**, och sedan **användare**.
   
    ![Admin](./media/bime-tutorial/ic775561.png "Admin")

3. I den **användarlistan**, klickar du på **Lägg till nya användare** (”+”).
   
    ![Användare](./media/bime-tutorial/ic775562.png "användare")

4. På den **användarinformation** dialogrutan utför följande steg:
   
    ![Användarinformation](./media/bime-tutorial/ic775563.png "användarinformation")
   
    a. I den **Förnamn** textruta Ange först namnet på användaren som **Britta**.

    b. I den **efternamn** textruta Ange efternamn för användaren som **Simon**.
 
    c. I den **e-post** textruta ange e-postadress för användaren som ** brittasimon@contoso.com **.

    d. Klicka på **Spara**.

>[!NOTE]
>Du kan använda något annat Bime användarens konto skapas verktyg eller API: er som tillhandahålls av Bime att etablera AAD-användarkonton.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bime.

![Tilldela användare][200] 

**Om du vill tilldela Bime Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Bime**.

    ![Konfigurera enkel inloggning](./media/bime-tutorial/tutorial_bime_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Bime på åtkomstpanelen du bör få automatiskt loggat in på ditt Bime program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/bime-tutorial/tutorial_general_01.png
[2]: ./media/bime-tutorial/tutorial_general_02.png
[3]: ./media/bime-tutorial/tutorial_general_03.png
[4]: ./media/bime-tutorial/tutorial_general_04.png

[100]: ./media/bime-tutorial/tutorial_general_100.png

[200]: ./media/bime-tutorial/tutorial_general_200.png
[201]: ./media/bime-tutorial/tutorial_general_201.png
[202]: ./media/bime-tutorial/tutorial_general_202.png
[203]: ./media/bime-tutorial/tutorial_general_203.png

