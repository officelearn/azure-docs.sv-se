---
title: "Självstudier: Azure Active Directory-integrering med Intralinks | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Intralinks."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 147f2bf9-166b-402e-adc4-4b19dd336883
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: ee7fd5b88ac806104002ffb41af11bab4fd1b2dc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-intralinks"></a>Självstudier: Azure Active Directory-integrering med Intralinks

I kursen får lära du att integrera Intralinks med Azure Active Directory (AD Azure).

Integrera Intralinks med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Intralinks
- Du kan aktivera användarna att automatiskt hämta loggat in på Intralinks (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Intralinks, behöver du följande:

- En Azure AD-prenumeration
- En Intralinks enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Intralinks från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-intralinks-from-the-gallery"></a>Att lägga till Intralinks från galleriet
Du måste lägga till Intralinks från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Intralinks i Azure AD.

**Utför följande steg för att lägga till Intralinks från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Intralinks**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_search.png)

5. Välj i resultatpanelen **Intralinks**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Intralinks baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Intralinks motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Intralinks upprättas.

I Intralinks, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Intralinks, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Intralinks](#creating-an-intralinks-test-user)**  – du har en motsvarighet för Britta Simon i Intralinks som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Intralinks program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Intralinks:**

1. I Azure-portalen på den **Intralinks** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_samlbase.png)

3. På den **Intralinks domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:`https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Intralinks klienten supportteamet](https://www.intralinks.com/contact-1) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_general_400.png)

6. Konfigurera enkel inloggning på **Intralinks** sida, måste du skicka den hämtade **XML-Metadata för** [Intralinks supportteam](https://www.intralinks.com/contact-1). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-intralinks-test-user"></a>Skapa en testanvändare Intralinks

I det här avsnittet skapar du en användare som kallas Britta Simon i Intralinks. Se tillsammans med [Intralinks supportteam](https://www.intralinks.com/contact-1) att lägga till användare i Intralinks-plattformen.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Intralinks.

![Tilldela användare][200] 

**Om du vill tilldela Intralinks Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Intralinks**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.

### <a name="add-intralinks-via-or-elite-application"></a>Lägg till Intralinks VIA eller Elite program

Intralinks använder samma SSO-identitetsplattformen för alla andra Intralinks program utom affären Nexus program. Så om du planerar att använda något annat program för Intralinks sedan du först konfigurera enkel inloggning för en primär Intralinks program med hjälp av proceduren ovan.

Efter det att du kan följa det under proceduren för att lägga till en annan Intralinks program i din klient som kan använda den här primära programmet för enkel inloggning. 

>[!NOTE]
>Den här funktionen är endast tillgänglig för Azure AD Premium-SKU kunder och inte tillgängliga för kostnadsfri eller grundläggande SKU-kunder.

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]


2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Intralinks**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_search.png)

5. På **Intralinks Lägg till app** utför följande steg:

    ![Lägga till Intralinks VIA eller Elite program](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_addapp.png)

    a. I **namn** textruta, t.ex. Ange rätt namn på programmet **Intralinks Elite**.

    b. Klicka på **Lägg till** knappen.

6.  I Azure-portalen på den **Intralinks** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

7. På den **enkel inloggning** markerar **läge** som **inloggning länkade**.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_linkedsignon.png)

8. Hämta den SP initierad SSO URL från [Intralinks team](https://www.intralinks.com/contact-1) för andra Intralinks program och ange den i **konfigurera inloggnings-URL** enligt nedan. 
    
     ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_intralinks_customappurl.png)
    
     Skriv den URL som används av dina användare logga in i tillämpningsprogrammet Intralinks med hjälp av följande mönster i textrutan loggar URL:
   
    `https://<company name>.Intralinks.com/?PartnerIdpId=https://sts.windows.net/<AzureADTenantID>`

9. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-intralinks-tutorial/tutorial_general_400.png)

10. Tilldela program till användare eller grupper som visas i avsnittet  **[tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Intralinks på åtkomstpanelen du bör få automatiskt loggat in på ditt Intralinks program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-intralinks-tutorial/tutorial_general_203.png

