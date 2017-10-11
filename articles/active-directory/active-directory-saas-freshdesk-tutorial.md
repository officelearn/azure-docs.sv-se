---
title: "Självstudier: Azure Active Directory-integrering med FreshDesk | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och FreshDesk."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f4b47e345a40b64f69ad8a4618564662b4a6c879
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Självstudier: Azure Active Directory-integrering med FreshDesk

I kursen får lära du att integrera FreshDesk med Azure Active Directory (AD Azure).

Integrera FreshDesk med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till FreshDesk
- Du kan aktivera användarna att automatiskt hämta loggat in på FreshDesk (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - till Azure-hanteringsportalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med FreshDesk, behöver du följande:

- En Azure AD-prenumeration
- En FreshDesk enkel inloggning på aktiverade prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Du bör inte använda produktionsmiljön, om det inte är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion av en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till FreshDesk från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-freshdesk-from-the-gallery"></a>Att lägga till FreshDesk från galleriet
Du måste lägga till FreshDesk från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av FreshDesk i Azure AD.

**Utför följande steg för att lägga till FreshDesk från galleriet:**

1. I den  **[Azure-hanteringsportalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Klicka på **Lägg till** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **FreshDesk**.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. Välj i resultatpanelen **FreshDesk**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med FreshDesk baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i FreshDesk motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i FreshDesk upprättas.

Den här länken relationen upprättas genom att tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** i FreshDesk.

Om du vill konfigurera och testa Azure AD enkel inloggning med FreshDesk, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare FreshDesk](#creating-a-freshdesk-test-user)**  – du har en motsvarighet för Britta Simon i FreshDesk som är kopplad till Azure AD-representation av henne.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-hanteringsportalen och konfigurera enkel inloggning i ditt FreshDesk program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med FreshDesk:**

1. I Azure-hanteringsportalen på den **FreshDesk** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan som **läge** Välj **SAML-baserade inloggning** att aktivera enkel inloggning på.
 
    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. På den **FreshDesk domän och URL: er** avsnittet, ange den **inloggnings-URL** som: `https://<tenant-name>.freshdesk.com` eller något annat värde Freshdesk har förslag.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Observera att detta inte är det verkliga värdet. Du måste uppdatera värdet med det faktiska inloggnings-URL. Kontakta [FreshDesk klienten supportteamet](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) att hämta det här värdet.  

4. På den **SAML-signeringscertifikat** klickar du på **certifikat** och spara certifikatet på datorn.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. På den **FreshDesk Configuration** klickar du på **konfigurera FreshDesk** öppna Konfigurera inloggning fönster. Kopiera SAML inloggning tjänst-URL för enkel och Sign-Out URL från den **Snabbreferens** avsnitt.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. Logga in på webbplatsen Freshdesk företag som en administratör i en annan webbläsarfönster.

8. Klicka på menyn högst upp **Admin**.
   
   ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "Admin")

9. I den **allmänna inställningar** klickar du på **säkerhet**.
   
   ![Säkerhet](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "säkerhet")

10. I den **säkerhet** avsnittet, utför följande steg:
   
    ![Enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "enkel inloggning")
   
    a. För **enkel inloggning (SSO)**väljer **på**.

    b. Välj **SAML SSO**.

    c. Typ av **SAML inloggning tjänst-URL för enkel** du kopierade från Azure-portalen i den **inloggnings-URL för SAML** textruta.

    d. Typ av **Sign-Out URL** du kopierade från Azure-portalen i den **logga ut URL** textruta.

    e. Kopiera den **tumavtrycket** värde från det hämta certifikatet från Azure-portalen och klistrar in det i den **säkerhet certifikat fingeravtryck** textruta.  
 
    >[!TIP]
    >Mer information finns i [hur du hämtar ett certifikat tumavtrycksvärde](http://youtu.be/YKQF266SAxI). 
    
    f. Klicka på **Spara**.


### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure Management portal kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-hanteringsportalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Gå till **användare och grupper** och på **alla användare** att visa en lista över användare.
    
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. Klicka på överst i dialogrutan **Lägg till** att öppna den **användaren** dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-a-freshdesk-test-user"></a>Skapa en testanvändare FreshDesk

För att aktivera Azure AD-användare att logga in på FreshDesk etableras de i FreshDesk.  
När det gäller FreshDesk är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på ditt **Freshdesk** klient.
2. Klicka på menyn högst upp **Admin**.
   
   ![Admin](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "Admin")

3. I den **allmänna inställningar** klickar du på **agenter**.
   
   ![Agenter](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "agenter")

4. Klicka på **ny Agent**.
   
    ![Ny Agent](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "ny Agent")

5. I dialogrutan agentinformation utför du följande steg:
   
   ![Agentinformation](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "agentinformation")
   
   a. I den **fullständiga namn** textruta skriver du namnet på Azure AD-kontot som du vill etablera.

   b. I den **e-post** textruta typen Azure AD Azure AD-kontot som du vill etablera e-postadress.

   c. I den **rubrik** textruta Ange rubriken för Azure AD-kontot som du vill etablera.

   d. Välj **agenter rollen**, och klicka sedan på **tilldela**.
       
   e. Klicka på **Spara**.     
   
    >[!NOTE]
    >Azure AD-kontoinnehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan den aktiveras. 
    > 
    
    >[!NOTE]
    >Du kan använda något annat Freshdesk användarens konto skapas verktyg eller API: er som tillhandahålls av Freshdesk att etablera AAD-användarkonton. att FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning med sin åtkomst beviljas till Box.

![Tilldela användare][200] 

**Om du vill tilldela FreshDesk Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-hanteringsportalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **FreshDesk**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

Du bör få inloggningssidan att hämta loggat in på ditt FreshDesk program när du klickar på panelen FreshDesk på åtkomstpanelen.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

