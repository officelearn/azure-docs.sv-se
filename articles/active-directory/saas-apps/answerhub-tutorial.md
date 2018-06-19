---
title: 'Självstudier: Azure Active Directory-integrering med AnswerHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: a0f55b6ee22b617372656584ed5ca505bddaffa7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2018
ms.locfileid: "35978243"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Självstudier: Azure Active Directory-integrering med AnswerHub

I kursen får lära du att integrera AnswerHub med Azure Active Directory (AD Azure).

Integrera AnswerHub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AnswerHub
- Du kan aktivera användarna att automatiskt hämta loggat in på AnswerHub (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton i en central plats - Azure-portalen

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med AnswerHub, behöver du följande:

- En Azure AD-prenumeration
- En AnswerHub enkel inloggning aktiverad prenumeration

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du hämta en utvärderingsversion för en månad [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till AnswerHub från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-answerhub-from-the-gallery"></a>Att lägga till AnswerHub från galleriet
Du måste lägga till AnswerHub från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av AnswerHub i Azure AD.

**Utför följande steg för att lägga till AnswerHub från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **AnswerHub**.

    ![Skapa en testanvändare i Azure AD](./media/answerhub-tutorial/tutorial_answerhub_search.png)

5. Välj i resultatpanelen **AnswerHub**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en testanvändare i Azure AD](./media/answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med AnswerHub baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i AnswerHub motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i AnswerHub upprättas.

I AnswerHub, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med AnswerHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AnswerHub](#creating-an-answerhub-test-user)**  – du har en motsvarighet för Britta Simon i AnswerHub som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AnswerHub program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AnswerHub:**

1. I Azure-portalen på den **AnswerHub** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. På den **AnswerHub domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_url.png)

    a. I den **inloggnings-URL** textruta Skriv en URL med följande mönster: `https://<company>.answerhub.com`

    b. I den **identifierare** textruta Skriv en URL med följande mönster: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL och identifierare. Kontakta [AnswerHub klienten supportteamet](mailto:success@answerhub.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_general_400.png)

6. På den **AnswerHub Configuration** klickar du på **konfigurera AnswerHub** att öppna **konfigurera inloggning** fönster. Kopiera den **Sign-Out URL och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_configure.png) 

7. Logga in på webbplatsen AnswerHub företag som en administratör i en annan webbläsarfönster.
   
    >[!NOTE]
    >Om du behöver hjälp med att konfigurera AnswerHub kontaktar [Answerhubs supportteamet](mailto:success@answerhub.com.).
   
8. Gå till **Administration**.

9. Klicka på den **användar- och** fliken.

10. I navigeringsfönstret till vänster i den **sociala inställningar** klickar du på **SAML installationsprogrammet**.

11. Klicka på **IDP Config** fliken.

12. På den **IDP Config** fliken, utför följande steg:

     ![SAML-installationsprogrammet](./media/answerhub-tutorial/ic785172.png "SAML-installationen")  
  
     a. I **IDP inloggnings-URL** textruta klistra in **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.
  
     b. I **IDP logga ut URL** textruta klistra in **Sign-Out URL** värde som du har kopierat från Azure-portalen.
     
     c. I **IDP namnformat identifierare** textruta ange användaren identifierare värdet samma som väljs i Azure-portalen i **användarattribut** avsnitt.
  
     d. Klicka på **nycklar och certifikat**.

13. Utför följande steg på fliken nycklar och certifikat:
    
     ![Nycklar och certifikat](./media/answerhub-tutorial/ic785173.png "nycklar och certifikat")  
 
     a. Öppna din Base64-kodade certifikat som du har hämtat från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **IDP offentlig nyckel (x 509-Format)** textruta.
  
     b. Klicka på **Spara**.

14. På den **IDP Config** klickar du på **spara**.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD
Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, klicka på det vänstra navigeringsfönstret **Azure Active Directory** ikon.

    ![Skapa en testanvändare i Azure AD](./media/answerhub-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och på **alla användare**.
    
    ![Skapa en testanvändare i Azure AD](./media/answerhub-tutorial/create_aaduser_02.png) 

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en testanvändare i Azure AD](./media/answerhub-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en testanvändare i Azure AD](./media/answerhub-tutorial/create_aaduser_04.png) 

    a. I den **namn** textruta typen **BrittaSimon**.

    b. I den **användarnamn** textruta typ av **e-postadress** av BrittaSimon.

    c. Välj **visa lösenordet** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-answerhub-test-user"></a>Skapa en testanvändare AnswerHub

Om du vill aktivera Azure AD-användare kan logga in på AnswerHub etableras de i AnswerHub.  
När det gäller AnswerHub är etablering en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. Logga in på ditt **AnswerHub** företagets webbplats som administratör.

2. Gå till **Administration**.

3. Klicka på den **användare och grupper** fliken.

4. I navigeringsfönstret till vänster i den **hantera användare** klickar du på **skapa eller importera användare**.
   
   ![Användare och grupper](./media/answerhub-tutorial/ic785175.png "användare och grupper")

5. Typ av **e-postadress**, **användarnamn** och **lösenord** för ett giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor och klicka sedan på  **Spara**.

>[!NOTE]
>Du kan använda något annat AnswerHub användarens konto skapas verktyg eller API: er som tillhandahålls av AnswerHub att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AnswerHub.

![Tilldela användare][200] 

**Om du vill tilldela AnswerHub Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **AnswerHub**.

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_app.png) 

3. Klicka på menyn till vänster **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen AnswerHub på åtkomstpanelen du bör få automatiskt loggat in på ditt AnswerHub program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/answerhub-tutorial/tutorial_general_01.png
[2]: ./media/answerhub-tutorial/tutorial_general_02.png
[3]: ./media/answerhub-tutorial/tutorial_general_03.png
[4]: ./media/answerhub-tutorial/tutorial_general_04.png

[100]: ./media/answerhub-tutorial/tutorial_general_100.png

[200]: ./media/answerhub-tutorial/tutorial_general_200.png
[201]: ./media/answerhub-tutorial/tutorial_general_201.png
[202]: ./media/answerhub-tutorial/tutorial_general_202.png
[203]: ./media/answerhub-tutorial/tutorial_general_203.png

