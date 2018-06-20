---
title: 'Självstudier: Azure Active Directory-integrering med bild Relay | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och avbildningen Relay.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 65bb5990-07ef-4244-9f41-cd28fc2cb5a2
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 1932980e1dd2f9de5ddb7ea3e77acb2a9f85fad5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225355"
---
# <a name="tutorial-azure-active-directory-integration-with-image-relay"></a>Självstudier: Azure Active Directory-integrering med bild Relay

I kursen får lära du att integrera avbildningen Relay med Azure Active Directory (AD Azure).

Integrera avbildningen Relay med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till avbildningen Relay
- Du kan aktivera användarna att automatiskt hämta loggat in på bilden Relay (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med bild Relay behöver du följande:

- En Azure AD-prenumeration
- En avbildning Relay enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till bilden Relay från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-image-relay-from-the-gallery"></a>Att lägga till bilden Relay från galleriet
Du måste lägga till bilden Relay från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av avbildningen Relay i Azure AD.

**Utför följande steg för att lägga till bilden Relay från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **avbildningen Relay**.

    ![Skapa en testanvändare i Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_search.png)

5. Välj i resultatpanelen **avbildningen Relay**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/imagerelay-tutorial/tutorial_imagerelay_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet, konfigurera och testa Azure AD enkel inloggning med bild Relay baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i avbildningen Relay till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i avbildningen Relay upprättas.

I bild Relay tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med bild Relay måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en avbildning Relay testanvändare](#creating-an-image-relay-test-user)**  – du har en motsvarighet för Britta Simon i avbildningen Relay som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i avbildningen Relay-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med bild Relay:**

1. I Azure-portalen på den **avbildningen Relay** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_samlbase.png)

3. På den **avbildningen Relay domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.imagerelay.com/`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.imagerelay.com/sso/metadata`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [avbildningen Relay klienten supportteamet](http://support.imagerelay.com/) att hämta dessa värden. 
 


4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_general_400.png)

6. På den **avbildningen Relay konfiguration** klickar du på **konfigurera avbildningen Relay** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL: en och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_configure.png) 

7. I ett nytt webbläsarfönster loggar du in på din bild Relay företagets webbplats som administratör.

8. Klicka på i verktygsfältet högst upp i **användare och behörigheter** arbetsbelastning.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_06.png) 

9. Klicka på **nya behörighet att skapa**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_08.png)

10. I den **enkel inloggning på inställningar** arbetsbelastning, Välj den **den här gruppen kan bara logga in via enkel inloggning** kryssrutan och klicka sedan på **spara**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_09.png) 

11. Gå till **kontoinställningar**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_10.png) 

12. Gå till den **enkel inloggning på inställningar** arbetsbelastning.
    
     ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_11.png)

13. På den **SAML inställningar** dialogrutan, utför följande steg:
    
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_12.png)
    
    a. I **Inloggningswebbadressen** textruta klistra in värdet för **inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    b. I **logga ut URL** textruta klistra in värdet för **tjänst-URL för enkel Sign-Out** som du har kopierat från Azure-portalen.

    c. Som **Format för namn-Id**väljer **urn: oasis: namn: tc: SAML:1.1:nameid-format: e-postadress**.

    d. Som **bindning alternativ för begäranden från Service Provider (bild relä)** väljer **POST bindning**.

    e. Under **x.509-certifikat**, klickar du på **uppdatering certifikat**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_17.png)

    f. Öppna hämtade certifikatet i anteckningar, kopiera innehållet och klistra in den i textrutan x.509-certifikat.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_18.png)

    g. I **in Användaretablering** väljer den **Aktivera in Användaretablering**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_19.png)

    h. Välj behörighetsgruppen (till exempel **SSO grundläggande**) som tillåts att logga in endast via enkel inloggning.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_20.png)

    i. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/imagerelay-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/imagerelay-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/imagerelay-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/imagerelay-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-image-relay-test-user"></a>Skapa en avbildning Relay testanvändare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i avbildningen Relay.

**Utför följande steg för att skapa en användare som kallas Britta Simon i avbildningen Relay:**

1. Inloggning på webbplatsen avbildningen Relay företag som administratör.

2. Gå till **användare och behörigheter** och välj **skapa SSO användare**.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_21.png) 

3. Ange den **e-post**, **Förnamn**, **efternamn**, och **företagets** för användaren du vill etablera och välj behörighetsgruppen (till exempel SSO grundläggande) vilket är den grupp som kan logga in endast via enkel inloggning.
   
    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_22.png) 

4. Klicka på **Skapa**.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till avbildningen Relay.

![Tilldela användare][200] 

**Om du vill tilldela avbildningen Relay Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **avbildningen Relay**.

    ![Konfigurera enkel inloggning](./media/imagerelay-tutorial/tutorial_imagerelay_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.    

När du klickar på bilden Relay-panelen på åtkomstpanelen du bör få automatiskt loggat in på bilden Relay-programmet.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/imagerelay-tutorial/tutorial_general_01.png
[2]: ./media/imagerelay-tutorial/tutorial_general_02.png
[3]: ./media/imagerelay-tutorial/tutorial_general_03.png
[4]: ./media/imagerelay-tutorial/tutorial_general_04.png


[100]: ./media/imagerelay-tutorial/tutorial_general_100.png

[200]: ./media/imagerelay-tutorial/tutorial_general_200.png
[201]: ./media/imagerelay-tutorial/tutorial_general_201.png
[202]: ./media/imagerelay-tutorial/tutorial_general_202.png
[203]: ./media/imagerelay-tutorial/tutorial_general_203.png

