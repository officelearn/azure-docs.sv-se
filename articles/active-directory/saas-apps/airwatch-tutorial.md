---
title: 'Självstudier: Azure Active Directory-integration med AirWatch | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AirWatch.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f3bbcbb70759e7a995797cf89ad75a2a39314927
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39048488"
---
# <a name="tutorial-azure-active-directory-integration-with-airwatch"></a>Självstudier: Azure Active Directory-integration med AirWatch

I den här självstudien får du lära dig hur du integrerar AirWatch med Azure Active Directory (AD Azure).

Integrera AirWatch med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AirWatch
- Du kan aktivera användarna att automatiskt få loggat in på AirWatch (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med AirWatch, behöver du följande objekt:

- En Azure AD-prenumeration
- En AirWatch enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till AirWatch från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-airwatch-from-the-gallery"></a>Att lägga till AirWatch från galleriet
För att konfigurera integrering av AirWatch i Azure AD, som du behöver lägga till AirWatch från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AirWatch från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **AirWatch**.

    ![Skapa en Azure AD-användare för testning](./media/airwatch-tutorial/tutorial_airwatch_search.png)

5. I resultatpanelen väljer **AirWatch**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/airwatch-tutorial/tutorial_airwatch_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med AirWatch baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i AirWatch är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i AirWatch upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i AirWatch.

Om du vill konfigurera och testa Azure AD enkel inloggning med AirWatch, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AirWatch](#creating-a-airwatch-test-user)**  – du har en motsvarighet för Britta Simon i AirWatch som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AirWatch program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AirWatch:**

1. I Azure-portalen på den **AirWatch** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/airwatch-tutorial/tutorial_airwatch_samlbase.png)

3. På den **AirWatch domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/airwatch-tutorial/tutorial_airwatch_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    b. I den **identifierare** textrutan skriver värde som `AirWatch`

    > [!NOTE] 
    > Det här värdet är inte verkligt. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [AirWatch klienten supportteamet](http://www.air-watch.com/company/contact-us/) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara XML-filen på datorn.

    ![Konfigurera enkel inloggning](./media/airwatch-tutorial/tutorial_airwatch_certificate.png) 

5. På den **AirWatch Configuration** klickar du på **konfigurera AirWatch** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/airwatch-tutorial/tutorial_airwatch_configure.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/airwatch-tutorial/tutorial_general_400.png)
<CS>
7. I ett annat webbläsarfönster logga du in på webbplatsen AirWatch företag som administratör.

8. I det vänstra navigeringsfönstret klickar du på **konton**, och klicka sedan på **administratörer**.
   
   ![Administratörer](./media/airwatch-tutorial/ic791920.png "administratörer")

9. Expandera den **inställningar** menyn och klicka sedan på **katalogtjänster**.
   
   ![Inställningar för](./media/airwatch-tutorial/ic791921.png "inställningar")

10. Klicka på den **användaren** fliken den **Bas-DN** textrutan skriver du domännamnet på din och klicka sedan på **spara**.
   
   ![Användaren](./media/airwatch-tutorial/ic791922.png "användare")

11. Klicka på den **Server** fliken.
   
   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

12. Utför följande steg:
    
    ![Ladda upp](./media/airwatch-tutorial/ic791924.png "ladda upp")   
    
    a. Som **katalogtyp**väljer **ingen**.

    b. Välj **använder SAML för autentisering**.

    c. Om du vill överföra det nedladdade certifikatet klickar du på **överför**.

13. I den **begära** avsnittet, utför följande steg:
    
    ![Begär](./media/airwatch-tutorial/ic791925.png "för begäran")  

    a. Som **begära bindning typ**väljer **POST**.

    b. I Azure-portalen på den **Konfigurera enkel inloggning på Airwatch** dialogrutan sida, kopiera den **SAML enkel inloggning för tjänst-URL** värdet och klistra in den i den **identitet providern enkel inloggning URL: en** textrutan.

    c. Som **NameID-Format**väljer **e-postadress**.

    d. Klicka på **Spara**.

14. Klicka på den **användaren** fliken igen.
    
    ![Användaren](./media/airwatch-tutorial/ic791926.png "användare")

15. I den **attributet** avsnittet, utför följande steg:
    
    ![Attributet](./media/airwatch-tutorial/ic791927.png "attribut")

    a. I den **objektidentifierare** textrutan typ **http://schemas.microsoft.com/identity/claims/objectidentifier**.

    b. I den **användarnamn** textrutan typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    c. I den **visningsnamn** textrutan typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    d. I den **Förnamn** textrutan typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**.

    e. I den **efternamn** textrutan typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**.

    f. I den **e-post** textrutan typ **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    g. Klicka på **Spara**.

<CE>

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/airwatch-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/airwatch-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/airwatch-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/airwatch-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** för Britta Simon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-airwatch-test-user"></a>Skapa en AirWatch testanvändare

Om du vill aktivera Azure AD-användare att logga in på AirWatch, måste de vara etablerade i att AirWatch.

* När AirWatch, etablering är en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **AirWatch** företagets plats som administratör.
2. I navigeringsfönstret till vänster klickar du på **konton**, och klicka sedan på **användare**.
   
   ![Användare](./media/airwatch-tutorial/ic791929.png "användare")
3. I den **användare** -menyn klickar du på **listvyn**, och klicka sedan på **Lägg till \> Lägg till användare**.
   
   ![Lägg till användare](./media/airwatch-tutorial/ic791930.png "lägga till användare")
4. På den **Lägg till / redigera användare** dialogrutan utför följande steg:

   ![Lägg till användare](./media/airwatch-tutorial/ic791931.png "lägga till användare")   
   1. Skriv den **användarnamn**, **lösenord**, **Bekräfta lösenord**, **Förnamn**, **efternamn**,  **E-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera till relaterade textrutor.
   2. Klicka på **Spara**.

>[!NOTE]
>Du kan använda alla andra AirWatch användare konto verktyg för att skapa eller API: er som tillhandahålls av AirWatch att etablera AAD-användarkonton.
>  

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AirWatch.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon AirWatch, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **AirWatch**.

    ![Konfigurera enkel inloggning](./media/airwatch-tutorial/tutorial_airwatch_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

Öppna panelen om du vill testa dina inställningar för enkel inloggning. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/airwatch-tutorial/tutorial_general_01.png
[2]: ./media/airwatch-tutorial/tutorial_general_02.png
[3]: ./media/airwatch-tutorial/tutorial_general_03.png
[4]: ./media/airwatch-tutorial/tutorial_general_04.png

[100]: ./media/airwatch-tutorial/tutorial_general_100.png

[200]: ./media/airwatch-tutorial/tutorial_general_200.png
[201]: ./media/airwatch-tutorial/tutorial_general_201.png
[202]: ./media/airwatch-tutorial/tutorial_general_202.png
[203]: ./media/airwatch-tutorial/tutorial_general_203.png

