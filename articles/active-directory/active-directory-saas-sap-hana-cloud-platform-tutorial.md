---
title: "Självstudier: Azure Active Directory-integrering med SAP Molnplattform | Microsoft Docs"
description: "Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Molnplattform."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 309415a68308943f638195303ceb236569519472
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Självstudier: Azure Active Directory-integrering med SAP Molnplattform

Lär dig hur du integrerar SAP Molnplattform med Azure Active Directory (AD Azure) i den här självstudiekursen.

Integrera SAP Molnplattform med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP Molnplattform.
- Du kan aktivera användarna att automatiskt hämta inloggade till SAP Molnplattform (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats - Azure-portalen.

Om du vill veta mer information om integrering av SaaS-app med Azure AD finns [vad är programåtkomst och enkel inloggning med Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Krav

För att konfigurera Azure AD-integrering med SAP Molnplattform, behöver du följande:

- En Azure AD-prenumeration
- En SAP Molnplattform enkel inloggning aktiverad prenumeration

Den här kursen Azure AD-användare som du har tilldelat till SAP Molnplattform kommer att kunna enkel inloggning till programmet med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Du måste distribuera ditt eget program eller prenumerera på ett program på SAP Molnplattform-konto för att testa enkel inloggning på. I den här självstudiekursen distribueras ett program i kontot.
> 

Om du vill testa stegen i den här självstudiekursen, bör du följa dessa rekommendationer:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till SAP Molnplattform från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Att lägga till SAP Molnplattform från galleriet
Du måste lägga till SAP Molnplattform från galleriet i listan över hanterade SaaS-appar för att konfigurera integrering av SAP Molnplattform i Azure AD.

**Utför följande steg för att lägga till SAP Molnplattform från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Enterprise program][2]
    
3. Om du vill lägga till nya programmet, klickar du på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP Molnplattform**väljer **SAP Molnplattform** resultatet-panelen klickar **Lägg till** för att lägga till programmet.

    ![SAP Molnplattform i resultatlistan](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med SAP Molnplattform baserat på en testanvändare som kallas ”Britta Simon”.

Azure AD måste du känna till användaren i SAP Molnplattform motsvarighet till en användare i Azure AD för enkel inloggning ska fungera. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Molnplattform upprättas.

I SAP Molnplattform, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** etablera länken relationen.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP Molnplattform, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  - om du vill att användarna kan använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  - om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare SAP Molnplattform](#create-a-sap-cloud-platform-test-user)**  – du har en motsvarighet för Britta Simon i SAP Molnplattform som är kopplad till Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  - om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  - om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt Molnplattform för SAP-program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med SAP Molnplattform:**

1. I Azure-portalen på den **SAP Molnplattform** integreringssidan för programmet, klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning länk][4]

2. På den **enkel inloggning** markerar **läge** som **SAML-baserade inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning dialogrutan](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. På den **SAP Cloud Platform domän och URL: er** avsnittet, utför följande steg:

    ![URL: er och SAP Cloud Platform domän med enkel inloggning information](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. I den **logga URL** textruta, Skriv URL-Adressen används av användarna att logga in på ditt **SAP Molnplattform** program. Detta är en skyddad resurs i ditt program för SAP Molnplattform kontospecifikt URL. URL: en är baserat på följande sätt:`https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Det här är URL-Adressen i din SAP Molnplattform program som kräver att användaren autentiseras.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. I den **identifierare** textruta som du kommer att ge din SAP Molnplattform skriver du en URL med någon av följande mönster: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. I den **Reply URL** textruta Skriv en URL med följande mönster:

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.us1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.ap1.hana.ondemand.com/<instancename>` |
    | `https://<subdomain>.dispatcher.hana.ondemand.com/<instancename>` |

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska inloggnings-URL, identifierare och Reply-URL. Kontakta [SAP Cloud Platform klienten supportteamet](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) få inloggnings-URL och identifierare. Reply-URL som du kan hämta från förtroende management avsnitt som beskrivs senare i självstudierna.
    > 
     
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. I en annan webbläsarfönster som inloggning till SAP Cloud Platform Cockpit på `https://account.<landscape host>.ondemand.com/cockpit`(till exempel: https://account.hanatrial.ondemand.com/cockpit).

7. Klicka på den **förtroende** fliken.
   
    ![Förtroende](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790800.png "förtroende")

8. I avsnittet förtroende hantering under **lokal-leverantör**, utför följande steg:

    ![Förtroende Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793931.png "förtroende för hantering")
   
    a. Klicka på **Redigera**.

    b. Som **konfigurationstypen**väljer **anpassad**.

    c. Som **lokala providernamn**, låt standardvärdet. Kopiera det här värdet och klistrar in det i den **identifierare** i Azure AD-konfiguration för SAP Molnplattform.

    d. Generera en **signeringsnyckeln** och en **signeringscertifikat** nyckelpar, klickar du på **Generera nyckel**.

    e. Som **huvudnamn spridningen**väljer **inaktiverade**.

    f. Som **kraft autentisering**väljer **inaktiverade**.

    g. Klicka på **Spara**.

9. När du har sparat den **lokal-leverantör** inställningar, utför följande för att hämta Reply-URL:
   
    ![Hämta Metadata](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793930.png "hämta Metadata")

    a. Hämta filen SAP Molnplattform metadata genom att klicka på **hämta Metadata**.

    b. Öppna den hämta SAP Molnplattform metadata XML-filen och välj sedan den **ns3:AssertionConsumerService** tagg.
 
    c. Kopiera värdet för den **plats** attributet och klistrar in det i den **Reply URL** i Azure AD-konfiguration för SAP Molnplattform.

10. Klicka på den **betrodda identitetsleverantör** fliken och klicka sedan på **lägga till betrodda identitetsleverantör**.
   
    ![Förtroende Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790802.png "förtroende för hantering")
   
    >[!NOTE]
    >Om du vill hantera listan över betrodda identitetsleverantörer, måste du har valt typen för anpassad konfiguration i avsnittet lokal-leverantör. Standard konfigurationstypen har du en redigeras och implicit förtroende till SAP-ID-tjänst. Ingen har du inte för förtroendeinställningar.
    > 
    > 

11. Klicka på den **allmänna** fliken och klicka sedan på **Bläddra** att överföra metadatafilen hämtade.
    
    ![Förtroende Management](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic793932.png "förtroende för hantering")
    
    >[!NOTE]
    >Efter överföring metadatafil värdena för **URL för enkel inloggning**, **URL för enkel logga ut**, och **signeringscertifikat** fylls i automatiskt.
    > 
     
12. Klicka på fliken **Attribut**.

13. På den **attribut** fliken, utför följande steg:
    
    ![Attribut](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790804.png "attribut") 

    a. Klicka på **Add Assertion-Based attributet**, och Lägg sedan till följande assertion-baserade attribut:
       
    | Attributet för kontrollen | Huvudnamn attribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Förnamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Efternamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-post |
   
     >[!NOTE]
     >Konfigurationen av attributen beror på hur program på SCP utvecklas, det vill säga vilka attribut som de förväntas i SAML-svaret och under vilka namn (Principal attribut) de har åtkomst till det här attributet i koden.
     > 
    
    b. Den **standard attributet** i skärmbilden är precis som illustration. Det krävs inte för att göra scenariot som fungerar.  
 
    c. Namn och värden för **Principal attributet** visas i skärmbilden beror på hur programmet utvecklas. Det är möjligt att ditt program kräver olika mappningar.

###<a name="assertion-based-groups"></a>Assertion-baserade grupper

Som ett valfritt steg kan du konfigurera assertion-baserade grupper för identitetsprovider Azure Active Directory.

Om du använder grupper på SAP Molnplattform kan du dynamiskt tilldela en eller flera användare till en eller flera roller i programmen SAP Molnplattform bestäms av värdena på attributen i SAML 2.0-kontroll. 

Om kontrollen innehåller attributet exempelvis ”*kontraktet = temporära*”, kan du vill att alla berörda användare som ska läggas till i gruppen ”*tillfälliga*”. Gruppen ”*tillfälliga*” kan innehålla en eller flera roller från en eller flera program som distribueras i ditt konto för SAP Molnplattform.
 
Använda assertion-baserade grupper när du vill tilldela många användare samtidigt till en eller flera roller för program i ditt konto för SAP Molnplattform. Om du vill tilldela specifika roller bara ett enda eller litet antal användare, rekommenderar vi att tilldela dem direkt i den ”**tillstånd**” för SAP Molnplattform cockpit.

> [!TIP]
> Du kan nu läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du installerar appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** avsnittet, klickar du på den **enkel inloggning** fliken och få åtkomst till den inbäddade dokumentationen via den **Configuration** avsnittet längst ned. Du kan läsa mer om funktionen inbäddade dokumentationen här: [inbäddade dokumentation för Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

Syftet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en testanvändare i Azure AD][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Öppna den **användare** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Klicka på **Skapa**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Skapa en testanvändare SAP Molnplattform

För att aktivera Azure AD-användare kan logga in till SAP Molnplattform, måste du tilldela roller i Molnplattform SAP dem.

**Utför följande steg om du vill tilldela en roll till en användare:**

1. Logga in på ditt **SAP Molnplattform** cockpit.

2. Utför följande:
   
    ![Tillstånd](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/ic790805.png "tillstånd")
   
    a. Klicka på **auktorisering**.

    b. Klicka på den **användare** fliken.

    c. I den **användaren** textruta Skriv användarens e-postadress.

    d. Klicka på **tilldela** tilldela användaren till en roll.

    e. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du aktivera Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Molnplattform.

![Tilldela rollen][200] 

**Om du vill tilldela SAP Molnplattform Britta Simon utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** Klicka **alla program**.

    ![Tilldela användare][201] 

2. Välj i listan med program **SAP Molnplattform**.

    ![Länken SAP Molnplattform i listan med program](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. Klicka på menyn till vänster **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg uppdrag** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** markerar **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen på **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen på **Lägg uppdrag** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Syftet med det här avsnittet är att testa Azure AD enkel inloggning konfigurationen med hjälp av panelen åtkomst.

När du klickar på panelen SAP Molnplattform på åtkomstpanelen du bör få automatiskt loggat in på ditt Molnplattform för SAP-program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-hana-cloud-platform-tutorial/tutorial_general_203.png

