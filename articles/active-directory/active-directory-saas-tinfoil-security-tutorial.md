---
title: "Självstudier: Azure Active Directory-integrering med TINFOIL SECURITY | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och TINFOIL SECURITY."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: 7794564396c2ade8ba45b9f2b1238f304f6b3921
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>Självstudier: Azure Active Directory-integrering med TINFOIL SECURITY

I kursen får lära du att integrera TINFOIL SECURITY med Azure Active Directory (AD Azure).

Integrera TINFOIL SECURITY med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till TINFOIL SECURITY
- Du kan aktivera användarna att automatiskt hämta loggat in på TINFOIL SECURITY (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med TINFOIL SECURITY, behöver du följande:

- En Azure AD-prenumeration
- En TINFOIL SECURITY enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Lägg till TINFOIL SECURITY från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-tinfoil-security-from-the-gallery"></a>Lägg till TINFOIL SECURITY från galleriet
Du måste lägga till TINFOIL SECURITY från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av TINFOIL SECURITY i Azure AD.

**Utför följande steg för att lägga till TINFOIL SECURITY från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **TINFOIL SECURITY**väljer **TINFOIL SECURITY** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![TINFOIL SECURITY från galleriet](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med TINFOIL SECURITY baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i TINFOIL SECURITY motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i TINFOIL SECURITY upprättas.

I TINFOIL SECURITY tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med TINFOIL SECURITY, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare TINFOIL SECURITY](#create-a-tinfoil-security-test-user)**  – har en motsvarighet för Britta Simon TINFOIL SECURITY som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt TINFOIL SECURITY-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med TINFOIL SECURITY:**

1. I Azure-portalen på den **TINFOIL SECURITY** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![SAML-baserade inloggning](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_samlbase.png)

3. På den **TINFOIL SECURITY domän och URL: er** avsnittet användaren behöver inte utföra några steg som appen före redan är integrerad med Azure.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_url.png)


4. På den **SAML-signeringscertifikat** avsnittet, kopiera den **TUMAVTRYCKET** värde.

    ![Signeringscertifikat för SAML-avsnitt](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_certificate.png) 

5. Utför följande steg för att lägga till nödvändiga attributmappning:
    
    ![Attribut](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute1.png "attribut")
    
    | Attributnamn    |   Attributvärde |
    | ------------------- | -------------------- |
    | accountid | UXXXXXXXXXXXXX |
    
    a. Klicka på **lägga till användarattribut**.
    
    ![Lägg till attributet](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_attribute.png "attribut")
    
    ![Lägg till attributet](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_addatt.png "attribut")
    
    b. I den **attributnamn** textruta typen **accountid**.
    
    c. I den **attributvärdet** textruta klistra in konto-ID-värde som du får senare på kursen.
    
    d. Klicka på **OK**.    

6. Klicka på **spara** knappen.

    ![Knappen Spara](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_400.png)

7. På den **TINFOIL SECURITY Configuration** klickar du på **konfigurera TINFOIL SECURITY** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![TINFOIL SECURITY Configuration](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_configure.png) 

8. Logga in på webbplatsen TINFOIL SECURITY företag som en administratör i en annan webbläsarfönster.

9. Klicka på i verktygsfältet högst upp **mitt konto**.
   
    ![Instrumentpanelen](./media/active-directory-saas-tinfoil-security-tutorial/ic798971.png "instrumentpanelen")

10. Klicka på **säkerhet**.
   
    ![Säkerhet](./media/active-directory-saas-tinfoil-security-tutorial/ic798972.png "säkerhet")

11. På den **enkel inloggning** konfigurationen utför följande steg:
   
    ![Enkel inloggning](./media/active-directory-saas-tinfoil-security-tutorial/ic798973.png "enkel inloggning")
   
    a. Välj **aktivera SAML**.
   
    b. Klicka på **manuell konfiguration**.
   
    c. I **SAML Post URL** textruta klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen
   
    d. I **SAML certifikat fingeravtryck** textruta klistra in värdet för **tumavtrycket** som du har kopierat från **SAML-signeringscertifikat** avsnitt.
  
    e. Kopiera **ditt konto-ID** värdet och klistrar in värdet i **attributvärdet** textruta under **lägga till attributet** avsnitt i Azure-portalen.
   
    f. Klicka på **Spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Användare och grupper -> alla användare ](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Användare](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-tinfoil-security-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="create-a-tinfoil-security-test-user"></a>Skapa en testanvändare TINFOIL SECURITY

För att aktivera Azure AD-användare att logga in på TINFOIL SECURITY etableras de i TINFOIL SECURITY. TINFOIL SECURITY är etablering en manuell aktivitet.

**Utför följande steg för att få en användare som har etablerats:**

1. Om användaren är en del av ett Enterprise-konto, behöver du [Kontakta supportteamet TINFOIL SECURITY](https://www.tinfoilsecurity.com/contact) att hämta det användarkonto som har skapats.

2. Om användaren är en vanlig TINFOIL SECURITY SaaS-användare, kan användaren lägga till en deltagare för användarens platser. Detta utlöser en process för att skicka en inbjudan till den angivna e-postadress för att skapa ett nytt användarkonto TINFOIL SECURITY.

> [!NOTE]
> Du kan använda andra TINFOIL SECURITY användare skapa verktyg eller API: er som tillhandahålls av TINFOIL SECURITY för att etablera Azure AD-användarkonton.
> 
> 

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till TINFOIL SECURITY.

![Tilldela användare][200] 

**Om du vill tilldela TINFOIL SECURITY Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **TINFOIL SECURITY**.

    ![Välj TINFOIL SECURITY](./media/active-directory-saas-tinfoil-security-tutorial/tutorial_tinfoil-security_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen TINFOIL SECURITY på åtkomstpanelen du bör få automatiskt loggat in på ditt TINFOIL SECURITY-program. Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tinfoil-security-tutorial/tutorial_general_203.png

