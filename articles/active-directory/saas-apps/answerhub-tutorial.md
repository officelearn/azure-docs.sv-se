---
title: 'Självstudier: Azure Active Directory-integration med AnswerHub | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AnswerHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 447a3911bc1f021fb1ca2658716de1910b5379b6
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044092"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Självstudier: Azure Active Directory-integration med AnswerHub

I den här självstudien får du lära dig hur du integrerar AnswerHub med Azure Active Directory (AD Azure).

Integrera AnswerHub med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till AnswerHub
- Du kan aktivera användarna att automatiskt få loggat in på AnswerHub (Single Sign-On) med sina Azure AD-konton
- Du kan hantera dina konton på en central plats – Azure portal

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med AnswerHub, behöver du följande objekt:

- En Azure AD-prenumeration
- En AnswerHub enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö kan du få en månads utvärdering [här](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till AnswerHub från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-answerhub-from-the-gallery"></a>Att lägga till AnswerHub från galleriet
För att konfigurera integrering av AnswerHub i Azure AD, som du behöver lägga till AnswerHub från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till AnswerHub från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Active Directory][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Program][3]

4. I sökrutan skriver **AnswerHub**.

    ![Skapa en Azure AD-användare för testning](./media/answerhub-tutorial/tutorial_answerhub_search.png)

5. I resultatpanelen väljer **AnswerHub**, och klicka sedan på **Lägg till** för att lägga till programmet.

    ![Skapa en Azure AD-användare för testning](./media/answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning
I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med AnswerHub baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i AnswerHub är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i AnswerHub upprättas.

I AnswerHub, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med AnswerHub, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare AnswerHub](#creating-an-answerhub-test-user)**  – du har en motsvarighet för Britta Simon i AnswerHub som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt AnswerHub program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med AnswerHub:**

1. I Azure-portalen på den **AnswerHub** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. På den **AnswerHub domän och URL: er** avsnittet, utför följande steg:

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://<company>.answerhub.com`

    b. I den **identifierare** textrutan anger du ett URL med hjälp av följande mönster: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare. Kontakta [AnswerHub klienten supportteamet](mailto:success@answerhub.com) att hämta dessa värden. 
 
4. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_general_400.png)

6. På den **AnswerHub Configuration** klickar du på **konfigurera AnswerHub** att öppna **konfigurera inloggning** fönster. Kopiera den **URL: en för utloggning och SAML enkel inloggning för tjänst-URL** från den **Snabbreferens avsnittet.**

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_configure.png) 

7. Logga in på webbplatsen AnswerHub företag som en administratör i ett annat webbläsarfönster.
   
    >[!NOTE]
    >Om du behöver hjälp med att konfigurera AnswerHub Kontakta [Answerhubs supportteamet](mailto:success@answerhub.com.).
   
8. Gå till **Administration**.

9. Klicka på den **användar- och** fliken.

10. I navigeringsfönstret till vänster i den **sociala inställningar** klickar du på **SAML installationsprogrammet**.

11. Klicka på **IDP-Config** fliken.

12. På den **IDP-Config** fliken, utför följande steg:

     ![SAML-installationsprogrammet](./media/answerhub-tutorial/ic785172.png "SAML-konfiguration")  
  
     a. I **inloggnings-URL för IDP: N** textrutan klistra in **SAML inloggnings-tjänst-URL för enkel** som du har kopierat från Azure-portalen.
  
     b. I **utloggnings-URL för IDP: N** textrutan klistra in **URL: en för utloggning** värde som du har kopierat från Azure-portalen.
     
     c. I **identifierare för IDP: N namnformat** textrutan, ange den användare som identifierare värdet samma som väljs i Azure-portalen i **användarattribut** avsnittet.
  
     d. Klicka på **nycklar och certifikat**.

13. Utför följande steg på fliken nycklar och certifikat:
    
     ![Nycklar och certifikat](./media/answerhub-tutorial/ic785173.png "nycklar och certifikat")  
 
     a. Öppna ditt Base64-kodat certifikat som du har hämtat från Azure-portalen i anteckningar, kopiera innehållet i den till Urklipp och klistra in den till den **IDP offentlig nyckel (x 509-Format)** textrutan.
  
     b. Klicka på **Spara**.

14. På den **IDP-Config** fliken **spara**.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning
Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

![Skapa en Azure AD-användare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I den **Azure-portalen**, i det vänstra navigeringsfönstret klickar du på **Azure Active Directory** ikon.

    ![Skapa en Azure AD-användare för testning](./media/answerhub-tutorial/create_aaduser_01.png) 

2. Om du vill visa en lista över användare, gå till **användare och grupper** och klicka på **alla användare**.
    
    ![Skapa en Azure AD-användare för testning](./media/answerhub-tutorial/create_aaduser_02.png) 

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i dialogrutan.
 
    ![Skapa en Azure AD-användare för testning](./media/answerhub-tutorial/create_aaduser_03.png) 

4. På den **användaren** dialogrutan utför följande steg:
 
    ![Skapa en Azure AD-användare för testning](./media/answerhub-tutorial/create_aaduser_04.png) 

    a. I den **namn** textrutan typ **BrittaSimon**.

    b. I den **användarnamn** textrutan skriver den **e-postadress** av BrittaSimon.

    c. Välj **visa lösenord** och anteckna värdet för den **lösenord**.

    d. Klicka på **Skapa**.
 
### <a name="creating-an-answerhub-test-user"></a>Skapa en AnswerHub testanvändare

Om du vill aktivera Azure AD-användare att logga in på AnswerHub, måste de etableras i AnswerHub.  
När det gäller AnswerHub är etablering en manuell aktivitet.

**Utför följande steg för att etablera ett användarkonto:**

1. Logga in på din **AnswerHub** företagets plats som administratör.

2. Gå till **Administration**.

3. Klicka på den **användare och grupper** fliken.

4. I navigeringsfönstret till vänster i den **hantera användare** klickar du på **skapa eller importera användare**.
   
   ![Användare och grupper](./media/answerhub-tutorial/ic785175.png "användare och grupper")

5. Skriv den **e-postadress**, **användarnamn** och **lösenord** för ett giltigt Azure Active Directory-konto som du vill etablera i den relaterade textrutor och klicka sedan på  **Spara**.

>[!NOTE]
>Du kan använda alla andra AnswerHub användare konto verktyg för att skapa eller API: er som tillhandahålls av AnswerHub att etablera AAD-användarkonton.

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till AnswerHub.

![Tilldela användare][200] 

**Om du vill tilldela Britta Simon AnswerHub, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **AnswerHub**.

    ![Konfigurera enkel inloggning](./media/answerhub-tutorial/tutorial_answerhub_app.png) 

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202] 

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen AnswerHub i åtkomstpanelen du bör få automatiskt loggat in på ditt AnswerHub program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
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

