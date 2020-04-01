---
title: 'Självstudiekurs: Azure Active Directory-integrering med SAML SSO för Jira by Resolution GmbH | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAML SSO for Jira by resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160130"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Självstudiekurs: Azure Active Directory-integrering med SAML SSO för Jira efter gmbH upplösning

I den här självstudien får du lära dig hur du konfigurerar SAML SSO för Jira genom resolution GmbH med Azure Active Directory (Azure AD).
Integreringen av SAML SSO for Jira by resolution GmbH med Azure AD medför följande fördelar:

* Du kan styra i Azure AD som kan logga in på Jira med SAML SSO plugin genom resolution GmbH.
* Du kan aktivera dina användare automatiskt inloggade jira med sina Azure AD-konton genom att använda SAML SSO för Jira genom resolution GmbH (Single Sign-On).
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

Om du vill konfigurera Azure AD-integrering och SAML SSO för Jira by resolution GmbH behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En SAML SSO for Jira by resolution GmbH-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* SAML SSO för Jira genom resolution GmbH stöder **SP** och **IDP** initierade SSO

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Lägga till ett företagsprogram för enkel inloggning

För att konfigurera enkel inloggning i Azure AD måste du lägga till ett nytt företagsprogram. I galleriet finns det en förkonfigurerad ansökan förinställning för detta, **SAML SSO för Jira av resolution GmbH**.

**Utför följande steg för att lägga till SAML SSO for Jira by resolution GmbH från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Navigera till **Företagsprogram**och klicka sedan på **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program klickar du på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **SAML SSO för Jira by resolution GmbH**, väljer **SAML SSO för Jira med upplösning GmbH** från resultatpanelen och klickar sedan på knappen **Lägg** till för att lägga till programmet. Du kan också ändra namnet på företagsappen.

     ![SAML SSO for Jira by resolution GmbH i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Konfigurera och testa enkel inloggning med PLUGIN-programmet SAML SSO och Azure AD

I det här avsnittet ska du testa och konfigurera enkel inloggning till Jira för en Azure AD-användare. Detta kommer att göras för en testanvändare som heter **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i SAML SSO for Jira by resolution GmbH upprättas.

Om du vill konfigurera och testa enkel inloggning måste du utföra följande steg:

1. **[Konfigurera Azure AD-företagsprogrammet för enkel inloggning](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** - Konfigurera Azure AD-företagsprogrammet för den enda inloggningen
2. **[Konfigurera SAML SSO-insticksprogrammet för din Jira-instans](#configure-the-saml-sso-plugin-of-your-jira-instance)** - Konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – Skapa en testanvändare i Azure AD.
1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** - Gör det möjligt för testanvändaren att använda den enda inloggningen på Azure-sidan.
1. **[Skapa testanvändaren i Jira](#create-the-test-user-also-in-jira)** - Skapa en motpartstestanvändare i Jira för Azure AD-testanvändaren.
1. **[Testa enkel inloggning](#test-single-sign-on)** - Kontrollera om konfigurationen fungerar.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurera Azure AD-företagsprogrammet för enkel inloggning

I det här avsnittet konfigurerar du den enda inloggningen i Azure-portalen.

Så här konfigurerar du den enda inloggningen med SAML SSO för Jira by resolution GmbH:

1. I [Azure-portalen](https://portal.azure.com/)väljer du Enkel inloggning i den vänstra panelen i den nyligen skapade **SAML SSO för Jira by resolution** GmbH-företagsprogrammet. **Single sign-on**

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. För **Välj en enkel inloggningsmetod**väljer du **SAML-läget** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. Klicka sedan på ikonen **Redigera** för att öppna dialogrutan **Grundläggande SAML-konfiguration.**

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. Om du vill konfigurera programmet i **idp-initierat** läge i avsnittet **Grundläggande SAML-konfiguration** gör du följande:

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/idp-intiated.png)

    a. Skriv en URL med följande mönster i textrutan **Identifierare:**`https://<server-base-url>/plugins/servlet/samlsso`

    b. Skriv en URL med följande mönster i textrutan **Svara URL:**`https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicka på **Ange ytterligare webbadresser** och utför följande steg om du vill konfigurera programmet i det **återupptana SP-läget:**

    ![Information om enkel inloggning med SAML SSO for Jira by resolution GmbH-domäner och -URL:er](common/metadata-upload-additional-signon.png)

    Skriv en URL med hjälp av följande mönster i textrutan **Sign-on-URL:**`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > För identifieraren, svars-URL:en och inloggnings-URL:en ersätter du ** \<server-base-url->** med bas-URL:en för jira-instansen. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen. Om du har problem, kontakta oss på [SAML SSO för Jira genom resolution GmbH Client support team](https://www.resolution.de/go/support).

4. På sidan **Konfigurera enkel inloggning med SAML** i avsnittet **SAML-signeringscertifikat** laddar du ned **XML-koden för federationsmetadata** och sparar den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>Konfigurera SAML SSO-insticksprogrammet för din Jira-instans 

1. I ett annat webbläsarfönster loggar du in på Jira-instansen som administratör.

2. Hovra över kuggen till höger och klicka på **Hantera appar**.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon1.png)

3. Om du omdirigeras till sidan Administratörsåtkomst anger du **lösenordet** och klickar på knappen **Bekräfta.**

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon2.png)

4. Jira omdirigerar normalt dig till Atlassian marknadsplats. Om inte, klicka på **Hitta nya appar** i den vänstra panelen. Sök efter **SAML Single Sign On (SSO) för JIRA** och klicka på **knappen Installera** för att installera SAML-insticksprogrammet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store.png)

5. Installationen av plugin-programmet startar. När det är klart klickar du på knappen **Stäng.**

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-2.png)

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-3.png)

6. Klicka sedan på **Hantera**.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-4.png)
    
8. Efteråt klickar du på **Konfigurera** för att konfigurera den just installerade insticksprogrammet.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/store-5.png)

9. I konfigurationsguiden för **SIM-insticksprogrammet** klickar du på **Lägg till ny IdP** för att konfigurera Azure AD som ny identitetsprovider.

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon4.png) 

10. Gör följande på sidan **Välj din SAML-identitetsprovider:**

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5a.png)
 
    a. Ange **Azure AD** som IdP-typ.
    
    b. Lägg till **namnet** på identitetsprovidern (t.ex.
    
    c. Lägg till en (valfri) **beskrivning** av identitetsprovidern (t.ex.
    
    d. Klicka på **Nästa**.
    
11. Klicka på **Nästa**på **konfigurationssidan för identitetsprovidern** .
 
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5b.png)

12. Utför följande steg på sidan **Importera SAML IdP-metadata**:

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5c.png)

    a. Klicka på knappen **Välj metadata-XML-fil** och välj **XML-filen Federation Metadata** som du hämtade tidigare.

    b. Klicka på knappen **Importera.**
     
    c. Vänta en kort stund tills importen lyckas.  
     
    d. Klicka på knappen **Nästa**.
    
13. Klicka på knappen **Nästa** på sidan Attribut och omvandling av **användar-ID.**

    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon5d.png)
    
14. På sidan **Skapa och uppdatera användare** klickar du på Spara & **Nästa** för att spara inställningarna.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6a.png)
    
15. På sidan **Testa dina inställningar** klickar du på Hoppa över test & konfigurera **manuellt** för att hoppa över användartestet för tillfället. Detta kommer att utföras i nästa avsnitt och kräver vissa inställningar i Azure-portalen.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6b.png)
    
16. Klicka på **OK** för att hoppa över varningen.
    
    ![Konfigurera enkel inloggning](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon. Med användaren kommer du att testa enkel inloggning.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **Ny användare** högst upp på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Gör följande i **egenskapen Användare:**

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **Britta Simon**.
  
    b. Ange <b>BrittaSimon@contoso.com</b>i fältet **Användarnamn** .

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet lägger du till Britta Simon i företagsprogrammet, vilket gör att hon kan använda enkel inloggning.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**. 

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Sök efter det företagsprogram som du har skapat i början av den här självstudien i programlistan. Om du följer stegen i handledningen, det kallas **SAML SSO för Jira av resolution GmbH**. Om du har gett det ett annat namn, sök efter det namnet.

    ![SAML SSO for Jira by resolution GmbH-länken i listan över program](common/all-applications.png)

3. Klicka på Användare **och grupper**på den vänstra panelen .

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Välj **Lägg till användare**och välj sedan Användare och **grupper** i dialogrutan Lägg **till tilldelning.**

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan Användare och klickar sedan på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig något rollvärde i SAML-påståendet väljer du lämplig roll för användaren i listan i dialogrutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **Tilldela** i dialogrutan **Lägg till tilldelning.**

### <a name="create-the-test-user-also-in-jira"></a>Skapa testanvändaren även i Jira

Om du vill att Azure AD-användare ska kunna logga in på SAML SSO för Jira genom gmbH upplösning måste de etableras i SAML SSO för Jira genom resolution GmbH. När det gäller den här självstudien måste du göra etableringen för hand. Det finns dock också andra etableringsmodeller tillgängliga för SAML SSO-insticksprogrammet genom upplösning, till exempel **just in time-etablering.** Se deras dokumentation på [SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Om du har en fråga om det kontaktar du supporten för [att lösa lösningar](https://www.resolution.de/go/support).

**Så här etablerar du ett användarkonto manuellt:**

1. Logga in på Jira-instans som administratör.

2. Hovra över kuggen och välj **Användarhantering**.

   ![Lägga till medarbetare](./media/samlssojira-tutorial/user1.png)

3. Om du omdirigeras till sidan Administratörsåtkomst anger du **lösenordet** och klickar på knappen **Bekräfta.**

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user2.png) 

4. Klicka på **Skapa användare**under avsnittet **Användarhanteringsfliken** .

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user3-new.png) 

5. På dialogsidan **"Skapa ny användare"** utför du följande steg. Du måste skapa användaren exakt som i Azure AD:

    ![Lägga till medarbetare](./media/samlssojira-tutorial/user4-new.png) 

    a. Skriv användarens e-postadress i textrutan **E-postadress:** <b>BrittaSimon@contoso.com</b>.

    b. Skriv användarens fullständiga namn i textrutan **Fullständigt namn:** **Britta Simon**.

    c. Skriv användarens e-postadress i textrutan <b>BrittaSimon@contoso.com</b> **Användarnamn:** . 

    d. Ange lösenordet för användaren i textrutan **Lösenord.**

    e. Klicka på **Skapa användare** om du vill slutföra användarskapandet.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för SAML SSO for Jira by resolution GmbH på åtkomstpanelen bör du loggas in automatiskt i SAML SSO for Jira by resolution GmbH-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Du kan också testa enkel inloggning, om du navigerar till [https://\<server-base-url>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Ersätt ** \<server-base-url>** med bas-URL för din Jira-instans.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Aktivera enkel omdirigering av inloggning för Jira

Som nämnts i avsnittet tidigare finns det för närvarande två sätt att utlösa den enda inloggningen. Antingen genom att använda **Azure-portalen** eller använda **en särskild länk till jira-instansen**. SAML SSO plugin genom resolution GmbH kan du också utlösa enda inloggning genom att helt enkelt komma åt någon webbadress som pekar på **din Jira instans**.

I huvudsak kommer alla användare som får tillgång till Jira omdirigeras till den enda inloggningen efter att ha aktiverat ett alternativ i plugin.

Så här aktiverar du SSO-omdirigering genom att göra följande i **din Jira-instans:**

1. Öppna konfigurationssidan för SAML SSO-plugin:t i Jira.
1. Klicka på **Omdirigering** i den vänstra panelen.
![](./media/samlssojira-tutorial/ssore1.png)

1. Markera **Aktivera SSO-omdirigering**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Tryck på knappen **Spara inställningar** i det övre högra hörnet.

När du har aktiverat alternativet kan du fortfarande nå frågan om användarnamn/lösenord om alternativet **Aktivera nosso** är markerat genom att navigera till [https://\<server-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Som alltid, ersätta ** \<server-base-url>** med din bas-URL.


## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

