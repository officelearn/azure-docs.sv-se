---
title: 'Självstudier: Azure Active Directory-integration med Zscaler | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Zscaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 68c453f7-aff1-4614-92d3-9b86f3ad99dc
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2017
ms.author: jeedes
ms.openlocfilehash: b7a011baeb9b526e8de22ecaa34672327c6aab17
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050175"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler"></a>Självstudier: Azure Active Directory-integration med Zscaler

I den här självstudien får du lära dig hur du integrerar Zscaler med Azure Active Directory (AD Azure).

Integrera Zscaler med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Zscaler
- Du kan aktivera användarna att automatiskt få loggat in på Zscaler (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Zscaler, behöver du följande objekt:

- En Azure AD-prenumeration
- En Zscaler enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Zscaler från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-zscaler-from-the-gallery"></a>Att lägga till Zscaler från galleriet
För att konfigurera integrering av Zscaler i Azure AD, som du behöver lägga till Zscaler från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Zscaler från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **Zscaler**.

    ![Skapa en Azure AD-användare för testning](./media/zscaler-tutorial/tutorial_zscaler_search.png)

5. I resultatpanelen väljer **Zscaler**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/zscaler-tutorial/tutorial_zscaler_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Zscaler baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i Zscaler är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i Zscaler upprättas.

I Zscaler, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med Zscaler, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Konfigurera proxyinställningar](#configuring-proxy-settings)**  – om du vill konfigurera proxyinställningarna i Internet Explorer
3. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
4. **[Skapa en testanvändare Zscaler](#creating-a-zscaler-test-user)**  – du har en motsvarighet för Britta Simon i Zscaler som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
6. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Zscaler-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Zscaler:**

1. I Azure-portalen på den **Zscaler** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/zscaler-tutorial/tutorial_zscaler_samlbase.png)

3. På den **Zscaler domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/zscaler-tutorial/tutorial_zscaler_url.png)

    I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<companyname>.zsclaer.net`

    > [!NOTE] 
    > Det här värdet är inte verkliga. Uppdatera det här värdet med faktiska inloggnings-URL: en. Kontakta [Zscaler klienten supportteamet](https://www.zscaler.com/company/contact) att hämta det här värdet. 

4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/zscaler-tutorial/tutorial_zscaler_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/zscaler-tutorial/tutorial_general_400.png)

6. På den **Zscaler Configuration** klickar du på **konfigurera Zscaler** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/zscaler-tutorial/tutorial_zscaler_configure.png) 

7. I ett annat webbläsarfönster logga du in på webbplatsen ZScaler företag som administratör.

8. Klicka på menyn längst upp **Administration**.
   
    ![Administration](./media/zscaler-tutorial/ic800206.png "Administration")

9. Under **hanterar administratörer och roller**, klickar du på **hantera användare och autentisering**.   
            
    ![Hantera användare och autentisering](./media/zscaler-tutorial/ic800207.png "hantera användare och autentisering")

10. I den **väljer autentiseringsalternativ för din organisation** avsnittet, utför följande steg:   
                
    ![Autentisering](./media/zscaler-tutorial/ic800208.png "autentisering")
   
    a. Välj **autentisera med hjälp av SAML enkel inloggning**.

    b. Klicka på **konfigureras SAML enkel inloggning**.

11. På den **konfigurera SAML enkel inloggning parametrar** dialogrutan sida, utför följande steg och klicka sedan på **klar**

    ![Enkel inloggning](./media/zscaler-tutorial/ic800209.png "enkel inloggning")
    
    a. Klistra in den **SAML enkel inloggning för tjänst-URL** värde, som du har kopierat från Azure-portalen till den **URL för SAML-portalen som användarna skickas för autentisering** textrutan.
    
    b. I den **attributet som innehåller inloggningsnamn** textrutan typ **NameID**.
    
    c. Om du vill ladda upp din hämtade certifikatet klickar du på **Zscaler pem**.
    
    d. Välj **aktivera SAML Automatisk etablering**.

12. På den **konfigurera användarautentisering** dialogrutan utför följande steg:

    ![Administration](./media/zscaler-tutorial/ic800210.png "Administration")
    
    a. Klicka på **Spara**.

    b. Klicka på **aktivera nu**.

## <a name="configuring-proxy-settings"></a>Konfigurera proxyinställningar
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Konfigurera proxyinställningarna i Internet Explorer

1. Starta **Internet Explorer**.

2. Välj **Internetalternativ** från den **verktyg** meny för att öppna den **Internetalternativ** dialogrutan.   
    
     ![Internetalternativ](./media/zscaler-tutorial/ic769492.png "Internet-alternativ")

3. Klicka på den **anslutningar** fliken.   
  
     ![Anslutningar](./media/zscaler-tutorial/ic769493.png "anslutningar")

4. Klicka på **LAN-inställningar** att öppna den **LAN-inställningar** dialogrutan.

5. Utför följande steg i avsnittet Proxy server:   
   
    ![Proxyserver](./media/zscaler-tutorial/ic769494.png "proxyserver")

    a. Välj **använder en proxyserver för ditt lokala nätverk**.

    b. Ange i textrutan adress **gateway.zscaler.net**.

    c. Ange i textrutan Port **80**.

    d. Välj **Använd ingen proxyserver för lokala adresser**.

    e. Klicka på **OK** att Stäng den **inställningar för lokalt nätverk (LAN)** dialogrutan.

6. Klicka på **OK** att Stäng den **Internetalternativ** dialogrutan.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/zscaler-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/zscaler-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/zscaler-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/zscaler-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-zscaler-test-user"></a>Skapa en testanvändare Zscaler

Om du vill aktivera Azure AD-användare att logga in på ZScaler, måste de etableras till ZScaler.  
När det gäller ZScaler är etablering en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:

1. Logga in på din **Zscaler** klient.

2. Klicka på **Administration**.   
   
    ![Administration](./media/zscaler-tutorial/ic781035.png "Administration")

3. Klicka på **Användarhantering**.   
        
     ![Lägg till](./media/zscaler-tutorial/ic781036.png "Lägg till")

4. I den **användare** fliken **Lägg till**.
      
    ![Lägg till](./media/zscaler-tutorial/ic781037.png "Lägg till")

5. Utför följande steg i avsnittet Lägg till användare:
        
    ![Lägg till användare](./media/zscaler-tutorial/ic781038.png "lägga till användare")
   
    a. Skriv den **UserID**, **användarens visningsnamn**, **lösenord**, **Bekräfta lösenord**, och välj sedan **grupper**och **avdelning** för ett giltigt AAD-konto som du vill etablera.

    b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra ZScaler användare konto verktyg för att skapa eller API: er som tillhandahålls av ZScaler att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Zscaler.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon Zscaler, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Zscaler**.

    ![Konfigurera enkel inloggning](./media/zscaler-tutorial/tutorial_zscaler_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen Zscaler i åtkomstpanelen du bör få automatiskt loggat in på ditt Zscaler-program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/zscaler-tutorial/tutorial_general_01.png
[2]: ./media/zscaler-tutorial/tutorial_general_02.png
[3]: ./media/zscaler-tutorial/tutorial_general_03.png
[4]: ./media/zscaler-tutorial/tutorial_general_04.png

[100]: ./media/zscaler-tutorial/tutorial_general_100.png

[200]: ./media/zscaler-tutorial/tutorial_general_200.png
[201]: ./media/zscaler-tutorial/tutorial_general_201.png
[202]: ./media/zscaler-tutorial/tutorial_general_202.png
[203]: ./media/zscaler-tutorial/tutorial_general_203.png

