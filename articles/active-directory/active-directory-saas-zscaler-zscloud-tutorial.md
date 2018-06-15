---
title: 'Självstudier: Azure Active Directory-integrering med Zscaler ZSCloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler ZSCloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: jeedes
ms.openlocfilehash: b44e6c7c47eec15dab2294ca7ebcb7b7dcff3a4c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354477"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Självstudier: Azure Active Directory-integrering med Zscaler ZSCloud

I kursen får lära du att integrera Zscaler ZSCloud med Azure Active Directory (AD Azure).

Integrera Zscaler ZSCloud med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler ZSCloud
- Du kan aktivera användarna att automatiskt hämta loggat in på Zscaler ZSCloud (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Zscaler ZSCloud, behöver du följande:

- En Azure AD-prenumeration
- En Zscaler ZSCloud enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Zscaler ZSCloud från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Att lägga till Zscaler ZSCloud från galleriet
Du måste lägga till Zscaler ZSCloud från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Zscaler ZSCloud i Azure AD.

**Utför följande steg för att lägga till Zscaler ZSCloud från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Zscaler ZSCloud**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_search.png)

5. Välj i resultatpanelen **Zscaler ZSCloud**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med Zscaler ZSCloud baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i Zscaler ZSCloud motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i Zscaler ZSCloud upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i Zscaler ZSCloud.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler ZSCloud, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Konfigurera proxyinställningar](#configuring-proxy-settings)**  - om du vill konfigurera proxyinställningarna i Internet Explorer
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Zscaler ZSCloud](#creating-a-zscaler-zscloud-test-user)**  – har en motsvarighet för Britta Simon Zscaler ZSCloud som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Zscaler ZSCloud.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler ZSCloud:**

1. I Azure-portalen på den **Zscaler ZSCloud** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_samlbase.png)

3. På den **Zscaler ZSCloud domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_url.png)

     I den **inloggnings-URL** textruta, Skriv URL: en som används av dina användare logga in i tillämpningsprogrammet ZScaler ZSCloud.
    
    > [!NOTE] 
    > Du måste uppdatera det här värdet med det faktiska inloggnings-URL. Kontakta [Zscaler ZSCloud klienten supportteamet](https://support.zscaler.com/hc/articles/210172606-Zscaler-is-Expanding-Its-Zscloud-Global-Footprint) att hämta det här värdet. 
 
4. På den **SAML-signeringscertifikat** klickar du på **certifikat (Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_400.png)

6. På den **Zscaler ZSCloud Configuration** klickar du på **konfigurera Zscaler ZSCloud** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_configure.png) 

7. I en annan webbläsarfönster loggar du in på webbplatsen ZScaler ZSCloud företag som administratör.

8. Klicka på menyn högst upp **Administration**.
   
    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800206.png "Administration")

9. Under **hantera administratörer & roller**, klickar du på **hantera användare och autentisering**.   
            
    ![Hantera användare och autentisering](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800207.png "hantera användare och autentisering")

10. I den **Välj autentiseringsalternativ för din organisation** avsnittet, utför följande steg:   
                
    ![Autentisering](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800208.png "autentisering")
   
    a. Välj **autentisera med SAML enkel inloggning**.

    b. Klicka på **konfigureras SAML enkel inloggning**.

11. På den **konfigurera SAML enkel inloggning parametrar** dialogrutan sida, utför följande steg och klicka sedan på **klar**

    ![Enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800209.png "enkel inloggning")
    
    a. Klistra in den **SAML inloggning tjänst-URL för enkel** värde i den **URL för SAML-portalen som användare om autentisering skickas** textruta.
    
    b. I den **attributet som innehåller inloggningsnamnet** textruta typen **NameID**.
    
    c. Om du vill överföra din hämtat certifikat klickar du på **Zscaler pem**.
    
    d. Välj **aktivera etablering av SAML-automatiskt**.

12. På den **konfigurera användarautentisering** dialogrutan utför följande steg:

    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/ic800210.png "Administration")
    
    a. Klicka på **Spara**.

    b. Klicka på **aktivera nu**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurera proxyinställningarna i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** från den **verktyg** menyn för att öppna den **Internetalternativ** dialogrutan.   
    
     ![Internet-alternativ](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769492.png "Internet-alternativ")

3. Klicka på den **anslutningar** fliken.   
  
     ![Anslutningar](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769493.png "anslutningar")

4. Klicka på **LAN-inställningar** att öppna den **LAN-inställningar** dialogrutan.

5. Utför följande steg i avsnittet Proxy-server:   
   
    ![Proxyserver](./media/active-directory-saas-zscaler-zscloud-tutorial/ic769494.png "proxyserver")

    a. Välj **använder en proxyserver för nätverket**.

    b. Ange i textrutan adress **gateway.zscalerone.net**.

    c. Ange i textrutan Port **80**.

    d. Välj **Använd ingen proxyserver för lokala adresser**.

    e. Klicka på **OK** att stänga den **inställningar för lokalt nätverk (LAN)** dialogrutan.

6. Klicka på **OK** att stänga den **Internetalternativ** dialogrutan.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-zscaler-zscloud-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.

### <a name="creating-a-zscaler-zscloud-test-user"></a>Skapa en testanvändare Zscaler ZSCloud

Om du vill aktivera Azure AD-användare kan logga in på ZScaler ZSCloud etableras de till ZScaler ZSCloud.  
När det gäller ZScaler ZSCloud är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:

1. Logga in på ditt **Zscaler** klient.

2. Klicka på **Administration**.   
   
    ![Administration](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781035.png "Administration")

3. Klicka på **Användarhantering**.   
        
     ![Lägg till](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Lägg till")

4. I den **användare** klickar du på **Lägg till**.
      
    ![Lägg till](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781037.png "Lägg till")

5. Utför följande steg i avsnittet Lägg till användare:
        
    ![Lägg till användare](./media/active-directory-saas-zscaler-zscloud-tutorial/ic781038.png "lägga till användare")
   
    a. Typ av **UserID**, **användarens visningsnamn**, **lösenord**, **Bekräfta lösenord**, och välj sedan **grupper**och **avdelning** av en giltig AAD-konto som du vill etablera.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda något annat ZScaler ZSCloud användarens konto skapas verktyg eller API: er som tillhandahålls av ZScaler ZSCloud att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler ZSCloud.

![Tilldela användare][200] 

**Om du vill tilldela Zscaler ZSCloud Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Zscaler ZSCloud**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_zscalerzscloud_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen.

När du klickar på panelen Zscaler ZSCloud på åtkomstpanelen du bör få automatiskt loggat in på ditt Zscaler ZSCloud program.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zscaler-zscloud-tutorial/tutorial_general_203.png

