---
title: 'Självstudier: Azure Active Directory-integrering med socker CRM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och socker CRM.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: jeedes
ms.openlocfilehash: 99699161e8047932d27b4b124255701d13a4ec41
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225872"
---
# <a name="tutorial-azure-active-directory-integration-with-sugar-crm"></a>Självstudier: Azure Active Directory-integrering med socker CRM

I kursen får lära du att integrera socker CRM med Azure Active Directory (AD Azure).

Integrera socker CRM med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till socker CRM
- Du kan aktivera användarna att automatiskt hämta loggat in på socker CRM (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med socker CRM, behöver du följande:

- En Azure AD-prenumeration
- En socker CRM enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till socker CRM från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sugar-crm-from-the-gallery"></a>Att lägga till socker CRM från galleriet
Du måste lägga till socker CRM från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av socker CRM i Azure AD.

**Utför följande steg för att lägga till socker CRM från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **socker CRM**.

    ![Skapa en testanvändare i Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_search.png)

5. Välj i resultatpanelen **socker CRM**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/sugarcrm-tutorial/tutorial_sugarcrm_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med socker CRM baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i socker CRM motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i socker CRM upprättas.

I socker CRM, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med socker CRM, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare socker CRM](#creating-a-sugar-crm-test-user)**  – du har en motsvarighet för Britta Simon i socker CRM som är länkad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt socker CRM-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med socker CRM:**

1. I Azure-portalen på den **socker CRM** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_samlbase.png)

3. På den **socker CRM-domänen och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_url.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster:
    | |
    |--|
    | `https://<companyname>.sugarondemand.com` |
    | `https://<companyname>.trial.sugarcrm` |

    > [!NOTE] 
    > Värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [socker CRM-klienten supportteamet](https://support.sugarcrm.com/) värdet hämtas. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_general_400.png)

6. På den **socker CRM Configuration** klickar du på **konfigurera socker CRM** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen socker CRM företag som administratör.

8. Gå till **Admin**.
   
    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

9. I den **Administration** klickar du på **lösenordshantering**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795889.png "Administration")

10. Välj **aktivera SAML-autentisering**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795890.png "Administration")

11. I den **SAML-autentisering** avsnittet, utför följande steg:
   
    ![SAML-autentisering](./media/sugarcrm-tutorial/ic795891.png "SAML-autentisering")  
 
    a. I den **Inloggningswebbadressen** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel**, som du har kopierat från Azure-portalen.
  
    b. I den **Servicenivåmål URL** textruta klistra in värdet för **Sign-Out URL**, som du har kopierat från Azure-portalen.
  
    c. Öppna din Base64-kodade certifikatet i anteckningar, kopiera innehållet i den till Urklipp och klistra in hela certifikatet till **X.509-certifikat** textruta.
  
    d. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/sugarcrm-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/sugarcrm-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/sugarcrm-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/sugarcrm-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-sugar-crm-test-user"></a>Skapa en testanvändare socker CRM

För att aktivera Azure AD-användare kan logga in på socker CRM etableras de till socker CRM.

När det gäller socker CRM är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **socker CRM** företagets webbplats som administratör.

2. Gå till **Admin**.
   
    ![Admin](./media/sugarcrm-tutorial/ic795888.png "Admin")

3. I den **Administration** klickar du på **Användarhantering**.
   
    ![Administration](./media/sugarcrm-tutorial/ic795893.png "Administration")

4. Gå till **användare \> Skapa ny användare**.
   
    ![Skapa ny användare](./media/sugarcrm-tutorial/ic795894.png "Skapa ny användare")

5. På den **användarprofil** fliken, utför följande steg:
   
    ![Ny användare](./media/sugarcrm-tutorial/ic795895.png "ny användare")

    a. Typ av **användarnamn**, **efternamn**, och **e-postadress** av en giltig Azure Active Directory-användare i den relaterade textrutor.
  
6. Som **Status**väljer **Active**.

7. Utför följande steg på fliken lösenord:
   
    ![Ny användare](./media/sugarcrm-tutorial/ic795896.png "ny användare")

    a. Ange lösenordet i textrutan relaterade.

    b. Klicka på **Spara**.

>[!NOTE]
>Du kan använda något annat socker CRM användarens konto skapas verktyg eller API: er som tillhandahålls av socker CRM etablera AAD-användarkonton. 
> 

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till socker CRM.

![Tilldela användare][200] 

**Om du vill tilldela socker CRM Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **socker CRM**.

    ![Konfigurera enkel inloggning](./media/sugarcrm-tutorial/tutorial_sugarcrm_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen socker CRM på åtkomstpanelen du bör få automatiskt loggat in på ditt socker CRM-program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sugarcrm-tutorial/tutorial_general_01.png
[2]: ./media/sugarcrm-tutorial/tutorial_general_02.png
[3]: ./media/sugarcrm-tutorial/tutorial_general_03.png
[4]: ./media/sugarcrm-tutorial/tutorial_general_04.png

[100]: ./media/sugarcrm-tutorial/tutorial_general_100.png

[200]: ./media/sugarcrm-tutorial/tutorial_general_200.png
[201]: ./media/sugarcrm-tutorial/tutorial_general_201.png
[202]: ./media/sugarcrm-tutorial/tutorial_general_202.png
[203]: ./media/sugarcrm-tutorial/tutorial_general_203.png

