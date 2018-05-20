---
title: 'Självstudier: Azure Active Directory-integrering med EBSCO | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och EBSCO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: jeedes
ms.openlocfilehash: 9da10e134db019162abdff35a8eb742ccdc30626
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Självstudier: Azure Active Directory-integrering med EBSCO

I kursen får lära du att integrera EBSCO med Azure Active Directory (AD Azure).

Integrera EBSCO med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till EBSCO.
- Du kan aktivera användarna att automatiskt hämta loggat in på EBSCO (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med EBSCO, behöver du följande:

- En Azure AD-prenumeration
- En EBSCO enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till EBSCO från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-ebsco-from-the-gallery"></a>Att lägga till EBSCO från galleriet
Du måste lägga till EBSCO från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av EBSCO i Azure AD.

**Utför följande steg för att lägga till EBSCO från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **EBSCO**väljer **EBSCO** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![EBSCO i resultatlistan](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med EBSCO baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i EBSCO motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i EBSCO upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med EBSCO, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare EBSCO](#create-an-ebsco-test-user)**  -du kan automatisera EBSCOhost etablering/Användaranpassning. EBSCO stöder Just-In-Time användaretablering.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt EBSCO program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med EBSCO:**

1. I Azure-portalen på den **EBSCO** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_samlbase.png)

3. På den **EBSCO domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och EBSCO domän med enkel inloggning information](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url.png)

    I den **identifierare** textruta, ange ett URL-Adressen: `pingsso.ebscohost.com`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och EBSCO domän med enkel inloggning information](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_url1.png)

    I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `http://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`
     
    > [!NOTE] 
    > Inloggnings-URL-värdet är inte verkliga. Uppdatera värdet med det faktiska inloggnings-URL. Kontakta [EBSCO klienten supportteamet](mailto:sso@ebsco.com) värdet hämtas. 

    o **unika element:**  

    o **Kundnr** = ange unika EBSCO kund-ID 

    o **profil** = klienter kan anpassa länken för att dirigera användare till en viss profil (beroende på vad de köpa från EBSCO). De kan ange en specifik profil-ID. Main-ID: N är eds (EBSCO Discovery-tjänsten) och ehost (EBSOCOhost-databaser). Instruktioner för samma ges [här](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile).

5. EBSCO program förväntar SAML-intyg i ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värden för attributen från den ”**användarattribut**” avsnitt på sidan för integrering av programmet. Följande skärmbild visar ett exempel för det här.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_attribute.png)

    > [!Note]
    > Den **namn** attributet är obligatoriskt och är mappad med **användar-ID** i EBSCO program. Detta läggs som standard så du inte behöver lägga till det manuellt.
    
6. I den **användarattribut** avsnitt på den **enkel inloggning** dialogrutan Konfigurera attribut för SAML-token som visas i bilden ovan och utför följande steg:
    
    | Attributnamn | Attributvärde |
    | ---------------| --------------- |    
    | Förnamn   | User.givenName |
    | Efternamn   | User.surname |
    | E-post   | User.Mail |

    a. Klicka på **Lägg till attributet** att öppna den **lägga till attributet** dialogrutan.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ebsco-tutorial/tutorial_officespace_04.png)

    ![Konfigurera enkel inloggning](./media/active-directory-saas-ebsco-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** textruta ange attributets namn visas för den raden.
    
    c. Från den **värdet** listan, ange det attributvärde som visas för den raden.
    
    d. Klicka på **Ok**

7. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_certificate.png) 

8. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-ebsco-tutorial/tutorial_general_400.png)
    
9. Konfigurera enkel inloggning på **EBSCO** sida, måste du skicka den hämtade **XML-Metadata för** till [EBSCO supportteam](mailto:sso@ebsco.com). De kan ange den här inställningen att ha SAML SSO anslutningen korrekt på båda sidor.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-ebsco-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-ebsco-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-ebsco-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-ebsco-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-an-ebsco-test-user"></a>Skapa en testanvändare EBSCO

När det gäller EBSCO är användaretablering automatisk.

**Utför följande steg om du vill konfigurera ett användarkonto:**

Azure AD skickar data som krävs till EBSCO program. EBSCOS användaretablering kan vara automatisk eller kräver ett enstaka formulär. Det beror på om klienten har mycket befintlig EBSCOhost konton med personliga inställningar sparas. Samma kan diskuteras med den [EBSCO supportteam](mailto:sso@ebsco.com) under genomförandet. Oavsett hur klienten behöver inte skapa någon EBSCOhost konton innan du testar.

   >[!Note]
   >Du kan automatisera EBSCOhost etablering/Användaranpassning. Kontakta [EBSCO supportteam](mailto:sso@ebsco.com) om Just-In-Time användaretablering. 
 
### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till EBSCO.

![Tilldela rollen][200] 

**Om du vill tilldela EBSCO Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **EBSCO**.

    ![Länken EBSCO i listan med program](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

1. När du klickar på panelen EBSCO på åtkomstpanelen du bör få automatiskt loggat in på ditt EBSCO program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

2. När du loggar in i programmet, klickar du på den **logga in** -knappen i övre högra hörnet.

    ![EBSCO inloggning i listan med program](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. Du får en enstaka uppmaning om att koppla institutionella/SAML logga in med ett **länka ditt befintliga konto MyEBSCOhost till ditt institut konto nu** eller **skapar ett nytt konto MyEBSCOhost och länka det till din institut konto**. Kontot används för användaranpassning på EBSCOhost-programmet. Välj alternativet **skapar ett nytt konto** och du kan se att formuläret för anpassning före slutfördes med värden från saml-svar som visas i skärmbilden nedan. Klicka på **”Fortsätt”** spara det här urvalet.
    
     ![EBSCO användaren i listan med program](./media/active-directory-saas-ebsco-tutorial/tutorial_ebsco_user.png)

4. När du har slutfört inställningarna ovan, avmarkerar du cookies-cache och logga in igen. Du inte manuellt logga in igen och anpassningsinställningarna ska sparas

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-ebsco-tutorial/tutorial_general_203.png

