---
title: 'Självstudier: Azure Active Directory-integrering med SAP-Molnplattform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Cloud Platform.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: bd398225-8bd8-4697-9a44-af6e6679113a
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: jeedes
ms.openlocfilehash: 176ed1718818b24866620d86d7eff4d9af1d378f
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050882"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform"></a>Självstudier: Azure Active Directory-integrering med SAP-Molnplattform

Lär dig hur du integrerar SAP-Molnplattform med Azure Active Directory (AD Azure) i den här självstudien.

Integrera SAP-Molnplattform med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till SAP Cloud Platform.
- Du kan aktivera användarna att automatiskt få loggat in på SAP Cloud Platform (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med SAP-Molnplattform, behöver du följande objekt:

- En Azure AD-prenumeration
- En SAP-Molnplattform enkel inloggning aktiverat prenumeration

När du har slutfört den här självstudien, Azure AD-användare som du har tilldelat till SAP Cloud Platform kommer att kunna enkel inloggning i program med den [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

>[!IMPORTANT]
>Du måste distribuera dina egna program eller prenumerera på ett program på ditt SAP Cloud Platform-konto för att testa enkel inloggning. I den här självstudien distribueras ett program i kontot.
> 

Om du vill testa stegen i den här självstudien bör du följa dessa rekommendationer:

- Använd inte din produktionsmiljö, om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till SAP Cloud Platform från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-sap-cloud-platform-from-the-gallery"></a>Att lägga till SAP Cloud Platform från galleriet
För att konfigurera integrering av SAP Cloud Platform i Azure AD, som du behöver lägga till SAP Cloud Platform från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till SAP Cloud Platform från galleriet:**

1. I den  **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]
    
3. Lägg till nytt program, klicka på **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **SAP Cloud Platform**väljer **SAP Cloud Platform** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![SAP-Molnplattform i resultatlistan](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med SAP-Molnplattform baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i SAP-Molnplattform är att en användare i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och relaterade användaren i SAP Cloud Platform upprättas.

I SAP-Molnplattform, tilldela värdet för den **användarnamn** i Azure AD som värde för den **användarnamn** att upprätta länken-relation.

Om du vill konfigurera och testa Azure AD enkel inloggning med SAP-Molnplattform, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare för SAP-Molnplattform](#create-a-sap-cloud-platform-test-user)**  – du har en motsvarighet för Britta Simon i SAP-Molnplattform som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#test-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för SAP-Molnplattform.

**Om du vill konfigurera Azure AD enkel inloggning med SAP-Molnplattform, utför du följande steg:**

1. I Azure-portalen på den **SAP Cloud Platform** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera enkel inloggning för länken][4]

2. På den **enkel inloggning** dialogrutan **läge** som **SAML-baserad inloggning** att aktivera enkel inloggning.
 
    ![Enkel inloggning för dialogrutan](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_samlbase.png)

3. På den **SAP Cloud Platform domän och URL: er** avsnittet, utför följande steg:

    ![SAP Cloud Platform domän och URL: er med enkel inloggning för information](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_url.png)

    a. I den **inloggning på URL: en** textrutan, skriver du in URL: en används av användarna att logga in på din **SAP Cloud Platform** program. Detta är kontospecifikt-URL för en skyddad resurs i ditt program för SAP-Molnplattform. URL: en baseras på följande mönster: `https://<applicationName><accountName>.<landscape host>.ondemand.com/<path_to_protected_resource>`
      
     >[!NOTE]
     >Det här är URL: en i ditt SAP Cloud Platform-program som kräver att användaren autentiseras.
     > 

    | |
    |--|
    | `https://<subdomain>.hanatrial.ondemand.com/<instancename>` |
    | `https://<subdomain>.hana.ondemand.com/<instancename>` |

    b. I den **identifierare** textrutan som du tillhandahåller din SAP Cloud Platform anger en URL med någon av följande mönster: 

    | |
    |--|
    | `https://hanatrial.ondemand.com/<instancename>` |
    | `https://hana.ondemand.com/<instancename>` |
    | `https://us1.hana.ondemand.com/<instancename>` |
    | `https://ap1.hana.ondemand.com/<instancename>` |

    c. I den **svars-URL** textrutan anger du ett URL med hjälp av följande mönster:

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
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL, identifierare och svars-URL. Kontakta [SAP Cloud Platform-klienten supportteamet](https://help.sap.com/viewer/65de2977205c403bbc107264b8eccf4b/Cloud/5dd739823b824b539eee47b7860a00be.html) att hämta inloggnings-URL och identifierare. Svars-URL som du kan hämta från förtroende hanteringsavsnittet som beskrivs senare i självstudien.
    > 
     
4. På den **SAML-signeringscertifikat** klickar du på **XML-Metadata för** och spara sedan metadatafilen på datorn.

    ![Länk för hämtning av certifikat](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_certificate.png) 

5. Klicka på **spara** knappen.

    ![Konfigurera enkel inloggning spara-knapp](./media/sap-hana-cloud-platform-tutorial/tutorial_general_400.png)

6. I ett annat webbläsarfönster, loggar in på SAP Cloud Platform Cockpit på `https://account.<landscape host>.ondemand.com/cockpit`(till exempel: https://account.hanatrial.ondemand.com/cockpit).

7. Klicka på den **förtroende** fliken.
   
    ![Lita på](./media/sap-hana-cloud-platform-tutorial/ic790800.png "förtroende")

8. I avsnittet förtroende Management under **lokala tjänstleverantör**, utför följande steg:

    ![Lita på Management](./media/sap-hana-cloud-platform-tutorial/ic793931.png "lita på hantering")
   
    a. Klicka på **Redigera**.

    b. Som **konfigurationstypen**väljer **anpassad**.

    c. Som **lokala providernamn**, lämna standardvärdet. Kopiera detta värde och klistra in den i den **identifierare** i Azure AD-konfiguration för SAP-Molnplattform.

    d. Generera en **signeringsnyckeln** och en **signeringscertifikat** nyckelpar, klickar du på **Generera nyckel**.

    e. Som **huvudnamn spridning**väljer **inaktiverad**.

    f. Som **kraft autentisering**väljer **inaktiverad**.

    g. Klicka på **Spara**.

9. När du har sparat den **lokala tjänstleverantör** inställningar, utför följande för att hämta svars-URL:
   
    ![Hämta Metadata för](./media/sap-hana-cloud-platform-tutorial/ic793930.png "hämta Metadata")

    a. Hämta SAP Cloud Platform metadatafilen genom att klicka **GetMetaData**.

    b. Öppna den hämtade SAP Cloud Platform XML-fil och välj sedan den **ns3:AssertionConsumerService** tagg.
 
    c. Kopiera värdet för den **plats** attributet och klistra in den i den **svars-URL** i Azure AD-konfiguration för SAP-Molnplattform.

10. Klicka på den **betrodd identitetsleverantör** fliken och klicka sedan på **Lägg till betrodd identitetsleverantör**.
   
    ![Lita på Management](./media/sap-hana-cloud-platform-tutorial/ic790802.png "lita på hantering")
   
    >[!NOTE]
    >Om du vill hantera listan över betrodda identitetsleverantörer, måste du har valt typ av anpassad konfiguration i avsnittet lokal-leverantör. Konfiguration av standardtyp har du ett icke-redigerbart och implicit förtroende till SAP-ID-tjänsten. Ingen har du inte någon förtroendeinställningarna.
    > 
    > 

11. Klicka på den **Allmänt** fliken och klicka sedan på **Bläddra** att ladda upp den hämtade metadatafilen.
    
    ![Lita på Management](./media/sap-hana-cloud-platform-tutorial/ic793932.png "lita på hantering")
    
    >[!NOTE]
    >När du har överfört metadatafilen, värdena för **URL för enkel inloggning**, **URL för enkel utloggning**, och **signeringscertifikat** fylls i automatiskt.
    > 
     
12. Klicka på fliken **Attribut**.

13. På den **attribut** fliken, utföra följande steg:
    
    ![Attribut](./media/sap-hana-cloud-platform-tutorial/ic790804.png "attribut") 

    a. Klicka på **Add Assertion-Based attributet**, och Lägg sedan till följande assertion-baserade attribut:
       
    | Attributet för kontrollen | Huvudnamn attribut |
    | --- | --- |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |Förnamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |Efternamn |
    | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |e-post |
   
     >[!NOTE]
     >Konfigurationen av attributen beror på hur programmen på SCP utvecklas, det vill säga vilka attribut som de förväntar sig i SAML-svar och under vilka namn (huvudnamn attribut) de har åtkomst till det här attributet i koden.
     > 
    
    b. Den **standard attributet** på skärmbilden är bara för tydlighetens skull. Det är inte krävs för att göra scenariot fungerar.  
 
    c. Namn och värden för **huvudnamn attributet** visas i skärmbilden beror på hur programmet utvecklas. Det är möjligt att ditt program kräver olika mappningar.

### <a name="assertion-based-groups"></a>Assertion grupper

Som ett valfritt steg kan konfigurera du assertion grupper för din Azure-identitetsprovider för Active Directory.

Med hjälp av grupper på SAP-Molnplattform kan du dynamiskt tilldela en eller flera användare till en eller flera roller i din SAP Cloud Platform-program, bestäms av värden för attributen i SAML 2.0-kontroll. 

Exempel: om kontrollen innehåller attributet ”*kontrakt = tillfälliga*”, kan du alla berörda användare som ska läggas till i gruppen ”*tillfälliga*”. Gruppen ”*tillfälliga*” kan innehålla en eller flera roller från en eller flera program som distribueras i ditt SAP Cloud Platform-konto.
 
Använd assertion grupper när du vill tilldela många användare samtidigt till en eller flera roller av program i ditt SAP Cloud Platform-konto. Om du vill tilldela specifika roller bara ett enda eller litet antal användare, rekommenderar vi att tilldela dem direkt i den ”**auktoriseringar**” fliken cockpit SAP-Molnplattform.

> [!TIP]
> Du kan läsa en kortare version av instruktionerna i den [Azure-portalen](https://portal.azure.com), medan du ställer in appen!  När du lägger till den här appen från den **Active Directory > företagsprogram** bara klickar du på den **enkel inloggning** fliken och komma åt den inbäddade dokumentationen genom den  **Konfigurationen** avsnittet längst ned. Du kan läsa mer om här funktionen embedded-dokumentation: [Azure AD embedded-dokumentation]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen kallas Britta Simon.

   ![Skapa en Azure AD-testanvändare][100]

**Utför följande steg för att skapa en testanvändare i Azure AD:**

1. I Azure-portalen, i den vänstra rutan klickar du på den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/sap-hana-cloud-platform-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**, och klicka sedan på **alla användare**.

    ![”Användare och grupper” och ”alla användare”-länkar](./media/sap-hana-cloud-platform-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan klickar du på **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/sap-hana-cloud-platform-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan utför följande steg:

    ![Dialogrutan användare](./media/sap-hana-cloud-platform-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Klicka på **Skapa**.
 
### <a name="create-a-sap-cloud-platform-test-user"></a>Skapa en testanvändare för SAP-Molnplattform

För att aktivera Azure AD-användare att logga in till SAP Cloud Platform måste du tilldela roller i SAP-Molnplattform till dem.

**Om du vill tilldela en roll till en användare utför du följande steg:**

1. Logga in på din **SAP Cloud Platform** cockpit.

2. Utför följande:
   
    ![Auktoriseringar](./media/sap-hana-cloud-platform-tutorial/ic790805.png "auktoriseringar")
   
    a. Klicka på **auktorisering**.

    b. Klicka på den **användare** fliken.

    c. I den **användaren** textrutan anger användarens e-postadress.

    d. Klicka på **tilldela** att tilldela användaren till en roll.

    e. Klicka på **Spara**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till SAP Cloud Platform.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon SAP-Molnplattform, utför du följande steg:**

1. Öppna vyn program i Azure-portalen och gå till vyn directory och gå till **företagsprogram** klickar **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **SAP Cloud Platform**.

    ![Länken SAP Cloud Platform i listan med program](./media/sap-hana-cloud-platform-tutorial/tutorial_sapcloudplatform_app.png)  

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Fönstret Lägg till tilldelning][203]

5. På **användare och grupper** dialogrutan **Britta Simon** på listan användare.

6. Klicka på **Välj** knappen **användare och grupper** dialogrutan.

7. Klicka på **tilldela** knappen **Lägg till tilldelning** dialogrutan.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

Målet med det här avsnittet är att prova Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen SAP Cloud Platform i åtkomstpanelen du bör få automatiskt loggat in på ditt SAP Cloud Platform-program.


## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_01.png
[2]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_02.png
[3]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_03.png
[4]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_04.png

[100]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_100.png

[200]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_200.png
[201]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_201.png
[202]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_202.png
[203]: ./media/sap-hana-cloud-platform-tutorial/tutorial_general_203.png

