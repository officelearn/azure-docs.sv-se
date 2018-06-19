---
title: 'Självstudier: Azure Active Directory-integrering med vice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och vice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 7aa3309c48b56872df259d4383294e758ac9fc08
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35964121"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Självstudier: Azure Active Directory-integrering med vice

I kursen får lära du att integrera vice med Azure Active Directory (AD Azure).

Integrera vice med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till vice
- Du kan aktivera användarna att automatiskt hämta loggat in på vice (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med vice behöver du följande:

- En Azure AD-prenumeration
- En vice enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till vice från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-deputy-from-the-gallery"></a>Att lägga till vice från galleriet
Du måste lägga till vice från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av vice i Azure AD.

**Utför följande steg för att lägga till vice från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **vice**.

    ![Skapa en testanvändare i Azure AD](./media/deputy-tutorial/tutorial_deputy_search.png)

5. Välj i resultatpanelen **vice**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/deputy-tutorial/tutorial_deputy_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med vice baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i vice motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i vice upprättas.

I vice, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med vice, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare vice](#creating-a-deputy-test-user)**  – har en motsvarighet för Britta Simon vice som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet vice.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med vice:**

1. I Azure-portalen på den **vice** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_samlbase.png)

3. På den **URL: er och vice domän** om du vill konfigurera programmet i **IDP** initierade läge:

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_url1.png)

    a. I den **identifierare** textruta Skriv en URL med följande mönster:
    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. I den **Reply URL** textruta Skriv en URL med följande mönster:
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs.` |

4. Kontrollera **visa avancerade inställningar för URL: en**. Om du vill konfigurera programmet i **SP** initierade läge:

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_url2.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<your-subdomain>.<region>.deputy.com`
    
    >[!NOTE]
    > Vice region suffix är valfritt eller den ska använda något av följande: Australien | na | Europa | som | la | af | en | ent Australien | ent na | ent Europa | ent-som | överordnad la | överordnad af | en överordnad

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifierare Reply URL och inloggnings-URL. Kontakta [vice supportteamet](https://www.deputy.com/call-centers-customer-support-scheduling-software) att hämta dessa värden. 

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_general_400.png)
    
7. På den **vice Configuration** klickar du på **konfigurera vice** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_configure.png) 

8. Navigera till följande URL:[https://(your-subdomain).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config). Gå till **säkerhetsinställningar** och på **redigera**.
   
    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_004.png)

9. På den här **säkerhetsinställningar** utför nedanstående steg.

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Aktivera **inloggning via sociala medier**.
   
    b. Öppna din Base64-kodat certifikat hämtas från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **OpenSSL certifikat** textruta.
   
    c. Ange i textrutan SAML SSO-URL `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. I textrutan URL för SAML SSO ersätter `<your subdomain>` med din underdomänen.
   
    e. I textrutan URL för SAML SSO ersätter `<saml sso url>` med den **SAML inloggning tjänst-URL för enkel** du har kopierat från Azure-portalen.
   
    f. Klicka på **Spara inställningar**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/deputy-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/deputy-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/deputy-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/deputy-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-deputy-test-user"></a>Skapa en vice testanvändare

Om du vill aktivera Azure AD-användare kan logga in på vice etableras de i vice. Vid vice är etablering en manuell aktivitet.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Utför följande steg om du vill konfigurera ett användarkonto:
1. Logga in på webbplatsen vice företag som administratör.

2. I det övre navigeringsfönstret, klickar du på **personer**.
   
   ![Personer](./media/deputy-tutorial/tutorial_deputy_001.png "personer")

3. Klicka på den **lägga till personer** och klicka sedan på **lägga till en enda person**.
   
   ![Lägg till personer](./media/deputy-tutorial/tutorial_deputy_002.png "lägga till personer")

4. Utför följande steg och klicka på **Spara & bjuda in**.
   
   ![Ny användare](./media/deputy-tutorial/tutorial_deputy_003.png "ny användare")

   a. I den **namn** textruta, ange namnet på användaren som **BrittaSimon**.
   
   b. I den **e-post** textruta skriver en Azure AD-kontot som du vill etablera e-postadress.
   
   c. I den **fungerar på** textruta typnamn företag.
   
   d. Klicka på **Spara & bjuda in** knappen.

5. AAD kontoinnehavaren får ett e-postmeddelande och följer en länk för att bekräfta sina konton innan den aktiveras. Du kan använda något annat vice användarens konto skapas verktyg eller API: er som tillhandahålls av vice etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till vice.

![Tilldela användare][200] 

**Om du vill tilldela vice Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **vice**.

    ![Konfigurera enkel inloggning](./media/deputy-tutorial/tutorial_deputy_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD SSO-konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen vice på åtkomstpanelen du bör få automatiskt loggat in på ditt vice program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/deputy-tutorial/tutorial_general_01.png
[2]: ./media/deputy-tutorial/tutorial_general_02.png
[3]: ./media/deputy-tutorial/tutorial_general_03.png
[4]: ./media/deputy-tutorial/tutorial_general_04.png

[100]: ./media/deputy-tutorial/tutorial_general_100.png

[200]: ./media/deputy-tutorial/tutorial_general_200.png
[201]: ./media/deputy-tutorial/tutorial_general_201.png
[202]: ./media/deputy-tutorial/tutorial_general_202.png
[203]: ./media/deputy-tutorial/tutorial_general_203.png

