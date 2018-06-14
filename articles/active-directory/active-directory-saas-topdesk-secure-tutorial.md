---
title: 'Självstudier: Azure Active Directory-integrering med TOPdesk - säker | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TOPdesk - säker.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2017
ms.author: jeedes
ms.openlocfilehash: 34296313753eb795bee5d93a8d8f1c8ae5efac9f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352320"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Självstudier: Azure Active Directory-integrering med TOPdesk - säker

I kursen får du lära dig hur du integrerar TOPdesk - skydda med Azure Active Directory (AD Azure).

Integrera TOPdesk - säker med Azure AD ger följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TOPdesk - säker.
- Du kan aktivera användarna att automatiskt hämta loggat in på TOPdesk - säker (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med TOPdesk - säker, behöver du följande:

- En Azure AD-prenumeration
- En TOPdesk - aktiverad prenumeration säker enkel inloggning

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägger till TOPdesk - skydda från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-topdesk---secure-from-the-gallery"></a>Lägger till TOPdesk - skydda från galleriet
Så här konfigurerar du integrering av TOPdesk - skydda till Azure AD, måste du lägga till TOPdesk: skydda från galleriet i listan över hanterade SaaS-appar.

**Om du vill lägga till TOPdesk - säker från galleriet, utför följande steg:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **TOPdesk - säker**väljer **TOPdesk - säker** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![TOPdesk - Secure i resultatlistan](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TOPdesk - säker baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till motsvarande användaren i TOPdesk - säker till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TOPdesk - säker upprättas.

I TOPdesk - säker, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TOPdesk - säker, måste du slutföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en TOPdesk - säker testanvändare](#create-a-topdesk---secure-test-user)**  – du har en motsvarighet för Britta Simon i TOPdesk - säkra som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i din TOPdesk - säkra program.

**Om du vill konfigurera Azure AD enkel inloggning med TOPdesk - säker, utför följande steg:**

1. I Azure-portalen på den **TOPdesk - säker** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. På den **TOPdesk - URL: er och säker domän** avsnittet, utför följande steg:

    ![TOPdesk - URL: er och säker domän med enkel inloggning information](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<companyname>.topdesk.net`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. I den **Reply URL** textruta Skriv en URL med följande mönster: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Reply URL beskrivs senare i självstudiekursen. Kontakta [TOPdesk - Secure klienten supportteamet](http://www.topdesk.com/us/support) att hämta dessa värden. 

4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_400.png)

6. På den **TOPdesk - konfiguration för säker** klickar du på **konfigurera TOPdesk - säker** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL, SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![TOPdesk - säker konfiguration](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)
    
7. Logga in på ditt **TOPdesk - säker** företagets webbplats som administratör.

8. I den **TOPdesk** -menyn klickar du på **inställningar**.

    ![Inställningar för](./media/active-directory-saas-topdesk-secure-tutorial/ic790598.png "inställningar")

9. Klicka på **inloggningsinställningar**.

    ![Inloggningsinställningar](./media/active-directory-saas-topdesk-secure-tutorial/ic790599.png "inloggningsinställningar")

10. Expandera den **inloggningsinställningar** -menyn och klicka sedan på **allmänna**.

    ![Allmän](./media/active-directory-saas-topdesk-secure-tutorial/ic790600.png "Allmänt")

11. I den **Secure** avsnitt i den **SAML inloggningen** konfiguration och utför följande steg:

    ![Tekniska inställningar](./media/active-directory-saas-topdesk-secure-tutorial/ic790855.png "tekniska inställningar")
   
    a. Klicka på **hämta** att hämta metadatafilen offentliga och sedan spara det lokalt på datorn.
   
    b. Öppna metadatafilen och leta upp den **AssertionConsumerService** nod.
    
    ![Assertion kundtjänst](./media/active-directory-saas-topdesk-secure-tutorial/ic790856.png "Assertion kundtjänst")
   
    c. Kopiera den **AssertionConsumerService** värde, klistra in det här värdet i textrutan Reply URL i **TOPdesk - URL: er och säker domän** avsnitt.

12. Utför följande steg för att skapa en certifikatfil:
    
    ![Certifikatet](./media/active-directory-saas-topdesk-secure-tutorial/ic790606.png "certifikat")
    
    a. Öppna metadatafilen hämtas från Azure-portalen.

    b. Expandera den **RoleDescriptor** nod som har en **xsi: type** av **aggregeringsdesignprocessen: ApplicationServiceType**.

    c. Kopiera värdet för den **X509Certificate** nod.

    d. Spara den kopierade **X509Certificate** värdet lokalt på din dator i en fil.

13. I den **offentliga** klickar du på **Lägg till**.
    
    ![Lägg till](./media/active-directory-saas-topdesk-secure-tutorial/ic790607.png "Lägg till")

14. På den **SAML configuration assistenten** dialogrutan utför följande steg:
    
    ![SAML Configuration assistenten](./media/active-directory-saas-topdesk-secure-tutorial/ic790608.png "assistenten för SAML-konfiguration")
    
    a. Att överföra metadatafilen hämtas från Azure-portalen under **Federationsmetadata**, klickar du på **Bläddra**.

    b. Att överföra din certifikatfil under **certifikat (RSA)**, klickar du på **Bläddra**.

    c. Att överföra logotypfilen som du har fått från supportteamet TOPdesk under **logotypen ikonen**, klickar du på **Bläddra**.

    d. I den **användarnamn** textruta typen `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    e. I den **visningsnamn** textruta, ange ett namn för din konfiguration.

    f. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-topdesk-secure-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-topdesk---secure-test-user"></a>Skapa en TOPdesk - säker testanvändare

För att aktivera Azure AD-användare att logga in på TOPdesk - säker, de måste etableras i TOPdesk - säker.  
När det gäller TOPdesk - säker, etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:
1. Logga in på ditt **TOPdesk - säker** företagets webbplats som administratör.
2. Klicka på menyn högst upp **TOPdesk \> ny \> stödfiler \> operatorn**.
   
    ![Operatorn](./media/active-directory-saas-topdesk-secure-tutorial/ic790610.png "Operator")

3. På den **New-operatorn** dialogrutan, utför följande steg:
   
    ![New-operatorn](./media/active-directory-saas-topdesk-secure-tutorial/ic790611.png "New-operatorn")
   
    a. Klicka på den **allmänna** fliken.
   
    b. I den **efternamn** textruta typen efternamn för användaren som **Simon**.
   
    c. Välj en **plats** för kontot i den **plats** avsnitt.
   
    d. I den **inloggningsnamnet** textruta för den **TOPdesk inloggning** skriver ett inloggningsnamn för användaren.
   
    e. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra TOPdesk - säker användare skapa verktyg eller API: er som tillhandahålls av TOPdesk - säker att tillhandahålla AAD användarkonton.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TOPdesk - säker.

![Tilldela rollen][200] 

**Om du vill tilldela TOPdesk - Britta Simon säker, utför följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TOPdesk - säker**.

    ![TOPdesk - säker länk i listan med program](./media/active-directory-saas-topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på TOPdesk - säker sida vid sida på panelen åtkomst du ska hämta automatiskt loggat in på ditt TOPdesk - säkra program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-topdesk-secure-tutorial/tutorial_general_203.png

