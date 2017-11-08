---
title: "Självstudier: Azure Active Directory-integrering med molntjänster Atlassian | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Atlassian moln."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jeedes
ms.openlocfilehash: 25a7694cefde344b34d3a46535bf09209b306593
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Självstudier: Azure Active Directory-integrering med Atlassian moln

I kursen får lära du att integrera Atlassian moln med Azure Active Directory (AD Azure).

Integrera Atlassian moln med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Atlassian moln.
- Du kan aktivera användarna att automatiskt hämta loggat in på molnet Atlassian (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med Atlassian molnet, behöver du följande:

- En Azure AD-prenumeration
- För att aktivera SAML enkel inloggning för Atlassian moln produkter måste du konfigurera Identity Manager. Lär dig mer om [Identity Manager]( https://www.atlassian.com/enterprise/cloud/identity-manager)

> [!NOTE]
> Om du vill testa stegen i den här kursen rekommenderar vi inte med hjälp av en produktionsmiljö.

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till Atlassian moln från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Att lägga till Atlassian moln från galleriet
Du måste lägga till Atlassian moln från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av Atlassian moln i Azure AD.

**Utför följande steg för att lägga till Atlassian moln från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Atlassian moln**väljer **Atlassian moln** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![Atlassian moln i resultatlistan](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

Du konfigurera och testa Azure AD enkel inloggning med Atlassian molnet baserat på en testanvändare som kallas ”Britta Simon” i det här avsnittet.

Azure AD måste du känna till motsvarande användaren i Atlassian molnet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk mellan en Azure AD-användare och relaterade användaren i Atlassian molnet upprättas.

I Atlassian moln, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med Atlassian moln, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare Atlassian moln](#create-an-atlassian-cloud-test-user)**  – du har en motsvarighet för Britta Simon i Atlassian moln som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i tillämpningsprogrammet Atlassian moln.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med Atlassian molnet:**

1. I Azure-portalen på den **Atlassian moln** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_samlbase.png)

3. På den **Atlassian moln domän och URL: er** avsnittet, utför följande steg om du vill konfigurera programmet i **IDP** initierade läge:

    ![URL: er och Atlassian moln domän med enkel inloggning information](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url_new.png)
    
    a. I den **identifierare** textruta anger du URL:`https://id.atlassian.com/login`
    
    b. I den **Reply URL** textruta anger du URL:`https://id.atlassian.com/login/saml/acs`

    c. I den **Relay tillstånd** textruta Skriv en URL med följande mönster:`https://<instancename>.atlassian.net`

4. Kontrollera **visa avancerade inställningar för URL: en** och utför följande steg om du vill konfigurera programmet i **SP** initierade läge:

    ![URL: er och Atlassian moln domän med enkel inloggning information](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_url1.png)

    I den **inloggnings-URL-textrutan**, ange en URL med hjälp av följande mönster:`https://<instancename>.atlassian.net`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Du kan hämta dessa värden från skärmen Atlassian moln SAML-konfiguration som beskrivs senare i självstudierna.

5. På den **SAML-signeringscertifikat** klickar du på **Certificate(Base64)** och spara certifikatfilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_certificate.png) 

6. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

7. På den **Atlassian Molnkonfigurationen** klickar du på **konfigurera Atlassian moln** att öppna **konfigurera inloggning** fönster. Kopiera den **SAML enhets-ID och SAML enkel inloggning Tjänstwebbadress** från den **Snabbreferens avsnitt.**

    ![Atlassian Molnkonfigurationen](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_configure.png) 

8. Att hämta SSO konfigurerats för ditt program, logga in på Atlassian-portalen med administratörsbehörighet.

9. Gå till **Atlassian Platsadministration** > **organisationer och säkerhet**. Om du inte redan har gjort skapa och namnge din organisation. Klicka på i det vänstra navigeringsfönstret **domäner**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_06.png)

10. Välj hur du vill verifiera din domän - **DNS** eller **HTTPS**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_17.png)

11. DNS-kontroll, Välj den **DNS** fliken på den **domäner** sidan och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_18.png)

    a. Klicka på **kopiera** att kopiera värdet för TXT-posten.

    b. Hitta inställningssidan för att lägga till en ny post från din DNS

    c. Välj alternativet för att lägga till en ny post och klistra in det värde som du kopierade från den **domäner** sidan till den **värdet** fältet. Din DNS kan också referera till den som **svar** eller **beskrivning**.

    d. DNS-post kan också innehålla följande fält:
    
    * **Posttyp**: Ange **TXT**
    * **Namn-värd-Alias**: Låt standardvärdet (@ eller tomt)
    * **Tid för live (TTL)**: Ange **86400**
    
    e.  Spara posten.

12. Gå tillbaka till den **domäner** Organisationsadministration och klicka på den **verifiera domän** knappen. Ange ditt domännamn i popup-fönstret och klicka på den **verifiera domän** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_19.png)
    
    > [!NOTE]
    > Det kan ta upp till 72 timmar för TXT-posten ändringarna ska börja gälla, vet du inte direkt om domänen verifieringen lyckades. Kontrollera din **domäner** sidan strax efter att du utför de här stegen för att verifiera statusen. Du ser följande skärmen med uppdaterad status som **VERIFIERAD**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_20.png)

13. HTTPS-kontroll, Välj den **HTTPS** fliken på den **domäner** sidan och utför följande steg:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_21.png)

    a.  Klicka på **filen** att ladda ned HTML-fil.

    b.  Överför HTML-fil till rotkatalogen för din domän.

14. Gå tillbaka till den **domäner** i Organisationsadministration och klicka på den **verifiera domän** knappen. Ange din **domännamn** i popup-fönstret och klicka på den **verifiera domän** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_22.png)

15. Om verifieringsprocessen kan hitta filen du överförde i rotkatalogen, status för domänen uppdateringar för **verifierad**.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_23.png)

    > [!NOTE]
    > Mer information finns [Atlassians domän verifiering dokumentation](https://confluence.atlassian.com/cloud/domain-verification-873871234.html)

16. I det vänstra navigeringsfältet klickar du på **SAML enkel inloggning**. Om du inte redan gjort prenumerera på Atlassian's Identity Manager.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

17. I **lägga till SAML-konfiguration** dialogrutan Lägg till providern Identitetsinställningar enligt följande:

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. I den **identitetsleverantör enhets-ID** text klistra in värdet för **SAML enhets-ID** som du har kopierat från Azure-portalen.

    b. I den **identitetsleverantör SSO URL** text klistra in värdet för **SAML inloggning tjänst-URL för enkel** som du har kopierat från Azure-portalen.

    c. Öppna hämtat certifikat i en anteckningar och kopiera värdena utan start- och avsluta rader och klistra in den i den **offentliga X509 certifikat** rutan.
    
    d. Klicka på **spara konfigurationen** spara inställningarna.
     
18. Uppdatera Azure AD-inställningar och kontrollera att du har installerat rätt URL-adresser.
  
    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. Kopiera den **SP identitet ID** från SAML skärmen och klistra in värdet i den **identifierare** rutan i Azure-portalen under Atlassian moln **domän och URL: er** avsnitt.
    
    b. Kopiera den **SP Assertion konsument-tjänstens URL** från SAML skärmen och klistra in värdet i den **Reply URL** rutan i Azure-portalen under Atlassian moln **domän och URL: er** avsnittet.
    
    c. Inloggning på URL: en är klient-URL för ditt Atlassian moln. 
    
19. I Azure-portalen klickar du på **spara** knappen.

    ![Konfigurera enkel inloggning](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_400.png)

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-atlassian-cloud-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
  
### <a name="create-an-atlassian-cloud-test-user"></a>Skapa en testanvändare Atlassian moln

Om du vill aktivera Azure AD-användare kan logga in på molnet Atlassian etableras de i Atlassian moln. Vid Atlassian moln är att etablera en manuell aktivitet.

**Utför följande steg om du vill konfigurera ett användarkonto:**

1. I avsnittet plats Atlassian portalen klickar du på den **användare** knappen

    ![Skapa Atlassian moln användare](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png) 

2. Klicka på den **bjuda in användare** för att skapa en användare i Atlassian molnet.

    ![Skapa Atlassian moln användare](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png) 

3. Ange användarens **e-postadress** och tilldela åtkomst till program. 

    ![Skapa Atlassian moln användare](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)
 
4. Klicka på **bjuda in användare** knappen, kommer att skicka e-postinbjudan för användaren och efter acceptera inbjudan användaren kommer att vara aktiv i systemet. 

>[!NOTE] 
>Du kan också skapa grupp användare genom att klicka på den **Bulk skapa** i avsnittet användare.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Atlassian moln.

![Tilldela rollen][200] 

**Om du vill tilldela Atlassian moln Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **Atlassian moln**.

    ![Länken Atlassian moln i listan med program](./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_atlassiancloud_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet kan du testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen Atlassian moln på åtkomstpanelen du ska hämta automatiskt loggat in i tillämpningsprogrammet Atlassian moln.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-atlassian-cloud-tutorial/tutorial_general_203.png

