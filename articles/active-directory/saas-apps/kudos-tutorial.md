---
title: 'Självstudier: Azure Active Directory-integrering med bra jobbat | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Bra jobbat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 39c47ce6-4944-47ba-8f53-3afa95398034
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jeedes
ms.openlocfilehash: 286b0509e9b281eed4c811f8baa0d55787190a64
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35966525"
---
# <a name="tutorial-azure-active-directory-integration-with-kudos"></a>Självstudier: Azure Active Directory-integrering med bra jobbat

I kursen får lära du att integrera Bra jobbat med Azure Active Directory (AD Azure).

Integrera Bra jobbat med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Bra jobbat
- Du kan aktivera användarna att automatiskt hämta loggat in på Bra jobbat (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med bra jobbat behöver du följande:

- En Azure AD-prenumeration
- En bra jobbat enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Bra jobbat från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-kudos-from-the-gallery"></a>Att lägga till Bra jobbat från galleriet
Du måste lägga till Bra jobbat från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Bra jobbat i Azure AD.

**Utför följande steg för att lägga till Bra jobbat från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Bra jobbat**.

    ![Skapa en testanvändare i Azure AD](./media/kudos-tutorial/tutorial_kudos_search.png)

5. Välj i resultatpanelen **Bra jobbat**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/kudos-tutorial/tutorial_kudos_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med bra jobbat baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Bra jobbat motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Bra jobbat upprättas.

I Bra jobbat, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med bra jobbat, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en bra jobbat testanvändare](#creating-a-kudos-test-user)**  – har en motsvarighet för Britta Simon Bra jobbat som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Bra jobbat.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bra jobbat:**

1. I Azure-portalen på den **Bra jobbat** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_samlbase.png)

3. På den **Bra jobbat domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<company>.kudosnow.com`
    
    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Bra jobbat klienten supportteamet](http://success.kudosnow.com/home) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_general_400.png)

6. På den **Bra jobbat Configuration** klickar du på **konfigurera Bra jobbat** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out Webbadressen och SAML enkel inloggning Service** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_configure.png) 

7. Logga in på webbplatsen Bra jobbat företag som en administratör i en annan webbläsarfönster.

8. Klicka på menyn högst upp **inställningar**.
   
    ![Inställningar för](./media/kudos-tutorial/ic787806.png "inställningar")

9. Klicka på **integreringar \> SSO**.

10. I den **SSO** avsnittet, utför följande steg:
   
    ![SSO](./media/kudos-tutorial/ic787807.png "ENKEL INLOGGNING")
   
    a. I **inloggning URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen. 

    b. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **X.509-certifikat** textruta
   
    c. I **logga ut till URL**, klistra in värdet för **Sign-Out URL** som du har kopierat från Azure-portalen.
   
    d. I den **din Bra jobbat URL** textruta Ange företagets namn.
   
    e. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/kudos-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/kudos-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/kudos-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/kudos-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-kudos-test-user"></a>Skapa en bra jobbat testanvändare

För att aktivera Azure AD-användare att logga in på Bra jobbat etableras de i Bra jobbat. 

Bra jobbat är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **Bra jobbat** företagets webbplats som administratör.

2. Klicka på menyn högst upp **inställningar**.
   
   ![Inställningar för](./media/kudos-tutorial/ic787806.png "inställningar")

3. Klicka på **Användaradministration**.

4. Klicka på den **användare** fliken och klicka sedan på **lägga till en användare**.
   
   ![Användaradministration](./media/kudos-tutorial/ic787809.png "Användaradministration")

5. I den **lägga till en användare** avsnittet, utför följande steg:
   
    ![Lägga till en användare](./media/kudos-tutorial/ic787810.png "lägga till en användare")
   
    a. Typ av **Förnamn**, **efternamn**, **e-post** och annan information om ett giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor.
   
    b. Klicka på **skapa användare**.

>[!NOTE]
>Du kan använda något annat Bra jobbat användarens konto skapas verktyg eller API: er som tillhandahålls av Bra jobbat etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Bra jobbat.

![Tilldela användare][200] 

**Om du vill tilldela Bra jobbat Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Bra jobbat**.

    ![Konfigurera enkel inloggning](./media/kudos-tutorial/tutorial_kudos_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Bra jobbat på åtkomstpanelen du bör få automatiskt loggat in i tillämpningsprogrammet Bra jobbat. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/kudos-tutorial/tutorial_general_01.png
[2]: ./media/kudos-tutorial/tutorial_general_02.png
[3]: ./media/kudos-tutorial/tutorial_general_03.png
[4]: ./media/kudos-tutorial/tutorial_general_04.png

[100]: ./media/kudos-tutorial/tutorial_general_100.png

[200]: ./media/kudos-tutorial/tutorial_general_200.png
[201]: ./media/kudos-tutorial/tutorial_general_201.png
[202]: ./media/kudos-tutorial/tutorial_general_202.png
[203]: ./media/kudos-tutorial/tutorial_general_203.png

