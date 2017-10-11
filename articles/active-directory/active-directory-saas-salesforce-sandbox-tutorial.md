---
title: "Självstudier: Azure Active Directory-integrering med Salesforce Sandbox | Microsoft Docs"
description: "Lär dig hur du använder Salesforce Sandbox med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/21/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: 32835e79188806bb2ff319eea23b1b52ab585ab1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>Självstudier: Azure Active Directory-integrering med Salesforce Sandbox

Syftet med den här kursen är att visa integreringen av Azure och Salesforce Sandbox.  

>[!TIP]
>Feedback, finns det [Azure supportsidan](http://go.microsoft.com/fwlink/?LinkId=521878). 
> 

Sandboxar ger dig möjlighet att skapa flera kopior av din organisation i olika miljöer för olika syften, till exempel utveckling, testning och utbildning, utan att kompromissa med data och program i organisationen Salesforce produktion.  

Mer information finns i [Sandbox-översikt](https://help.salesforce.com/HTViewHelpDoc?id=create_test_instance.htm&language=en_US)

Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En sandbox i Salesforce.com

Om du inte har en giltig sandbox i Salesforce.com ännu, måste du kontakta Salesforce.

Det scenario som beskrivs i den här kursen består av följande byggblock:

1. Aktivera programintegrationstyp för Salesforce begränsat läge
2. Konfigurera enkel inloggning (SSO)
3. Aktivera din domän
4. Konfigurera användaretablering
5. Tilldela användare

![Scenariot](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769571.png "Scenario")

## <a name="enable-the-application-integration-for-salesforce-sandbox"></a>Aktivera application-integration för Salesforce Sandbox
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för Salesforce begränsat läge.

**Utför följande steg om du vill aktivera programintegrationstyp för Salesforce begränsat läge:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
   ![Program](./media/active-directory-saas-salesforce-sandbox-tutorial/IC700994.png "program")
4. Öppna den **Programgalleriet**, klickar du på **Lägg till en App**, och klicka sedan på **lägga till ett program som min organisation använder**.
   
   ![Vad vill du göra? ] (./media/active-directory-saas-salesforce-sandbox-tutorial/IC700995.png "Vad vill du göra?")
5. I den **sökrutan**, typen **Salesforce Sandbox**.
   
   ![Programgalleriet](./media/active-directory-saas-salesforce-sandbox-tutorial/IC710978.png "Programgalleriet")
6. I resultatfönstret, Välj **Salesforce Sandbox**, och klicka sedan på **Slutför** lägga till programmet.
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746474.png "Salesforce Sandbox")
   
## <a name="configur-single-sign-on-sso"></a>Configur enkel inloggning (SSO)

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till Salesforce med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.

**Utför följande steg för att konfigurera enkel inloggning:**

1. I den klassiska Azure-portalen på den **Salesforce Sandbox** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/IC749323.png "Konfigurera enkel inloggning")
2. På den **hur vill du att användarna kan logga in på Salesforce Sandbox** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746479.png "Salesforce Sandbox")
3. På den **konfigurera App-URL** sidan den **logga URL** textruta Skriv URL: en med hjälp av följande mönster `http://company.my.salesforce.com`, och klicka sedan på **nästa**.
   
   ![Konfigurera App-URL](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781022.png "konfigurera App-URL")
4. Om du redan har konfigurerat enkel inloggning för en annan Salesforce Sandbox-instans i katalogen, så måste du även konfigurera den **identifierare** ska ha samma värde som den **inloggning URL**. 
 * Den **identifierare** fältet finns genom att kontrollera den **visa avancerade inställningar** kryssruta på den **konfigurera App-URL** sida i dialogrutan.
5. På den **Konfigurera enkel inloggning på Salesforce Sandbox** klickar du på **hämta certifikat**, och sedan spara certifikatfilen på datorn.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781023.png "Konfigurera enkel inloggning")
6. Logga in på ditt Salesforce-sandbox som en administratör i en annan webbläsarfönster.
7. Klicka på menyn högst upp **installationsprogrammet**.
   
   ![Installationsprogrammet](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781024.png "installationen")
8. I navigeringsfönstret till vänster klickar du på **säkerhetsåtgärder**, och klicka sedan på **inställningar för enkel inloggning**.
   
   ![Enkel inloggning inställningar](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781025.png "enkel inloggning inställningar")
9. Utför följande steg i avsnittet Inställningar för enkel inloggning:
   
   ![Enkel inloggning inställningar](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781026.png "enkel inloggning inställningar")  
 1.  Välj **SAML aktiverat**. 
 2.  Klicka på **Ny**.
10. Utför följande steg på avsnittet SAML enkel inloggning inställningar:
    
    ![SAML enkel inloggning inställningar](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781027.png "SAML enkel inloggning inställningar")  
 1. Skriv namnet på konfigurationen i textrutan namn (t.ex.: *SPSSOWAAD\_Test*). 
 2. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på Salesforce Sandbox** dialog sidan, kopiera den **utfärdar-URL** värdet och klistrar in det i den **utfärdaren** textruta.
 3. I den **enhets-Id** textruta typen **https://test.salesforce.com** om det är den första Salesforce Sandbox-instans som du lägger till din katalog. Om du redan har lagt till en instans av Salesforce Sandbox sedan för den **enhets-ID** Skriv i den **logga URL**, som ska vara i formatet:`http://company.my.salesforce.com`   
 4. Klicka på **Bläddra** att överföra hämtat certifikat.  
 5. Som **SAML identitetstyp**väljer **Assertion innehåller Federation-ID från användarobjektet**. 
 6. Som **SAML identitet plats**väljer **identitet är i elementet NameIdentifier i instruktionen ämne**.
 7. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på Salesforce Sandbox** dialog sidan, kopiera den **Remote inloggnings-URL** värdet och klistrar in det i den **identitet providern inloggnings-URL** textruta. 
 8. SFDC har inte stöd för SAML logga ut.  Som en tillfällig lösning kan du klistra in 'https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0' till den **identitet providern logga ut URL** textruta.
 9. Som **providern initierade begära Tjänstbindning**väljer **HTTP POST**. 
 10. Klicka på **Spara**.
11. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781028.png "Konfigurera enkel inloggning")

## <a name="enable-your-domain"></a>Aktivera din domän
Det här avsnittet förutsätter att du redan har skapat en domän.  Mer information finns i [definierar domännamn](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US).

**Utför följande steg om du vill aktivera din domän:**

1. I det vänstra navigeringsfönstret klickar du på **domänhantering**, och klicka sedan på **min domän.**
   
   ![Min domän](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781029.png "min domän")
   
   >[!NOTE]
   >Kontrollera att din domän har konfigurerats korrekt. 
   > 
2. I den **sidan inloggningsinställningar** klickar du på **redigera**, sedan som **Autentiseringstjänsten**, Välj namnet på SAML enkel inloggning inställningen från föregående avsnitt och klickar slutligen **spara**.
   
   ![Min domän](./media/active-directory-saas-salesforce-sandbox-tutorial/IC781030.png "min domän")

Så snart du har en domän som har konfigurerats, bör användarna använda domän-URL för att logga in till Salesforce sandbox.  

Om du vill hämta värdet för URL: en, klickar du på SSO-profil som du har skapat i föregående avsnitt.

## <a name="configure-user-provisioning"></a>Konfigurera användaretablering
Syftet med det här avsnittet är att beskriva hur du aktiverar användaretablering för Active Directory-användarkonton till Salesforce Sandbox.

**Utför följande steg för att konfigurera användaretablering:**

1. Välj ditt namn för att expandera användare-menyn i Salesforce-portalen i det övre navigeringsfältet:
   
   ![Mina inställningar](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698773.png "Mina inställningar")
2. Användare-menyn, Välj **Mina inställningar** att öppna din **Mina inställningar** sidan.
3. I den vänstra rutan klickar du på **personliga** Expandera avsnittet personliga och klicka sedan på **Återställ mina säkerhetstoken**:
   
   ![Mina inställningar](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698774.png "Mina inställningar")
4. På den **Återställ mina säkerhetstoken** klickar du på **återställa säkerhetstoken** att begära ett e-postmeddelande som innehåller din Salesforce.com-säkerhetstoken.
   
   ![Ny Token](./media/active-directory-saas-salesforce-sandbox-tutorial/IC698776.png "ny Token")
5. Kontrollera din inkorg för e-post från Salesforce.com med ”**salesforce.com.com säkerhet bekräftelse**” som ämne.
6. Granska den här e-post och kopiera säkerhet token-värde.
7. I den klassiska Azure-portalen på den **salesforce Sandbox** integreringssidan för programmet, klickar du på **konfigurera användaretablering** att öppna den **konfigurera Användaretablering** dialogrutan.
   
   ![Konfigurera användaretablering](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769573.png "konfigurera användaretablering")
8. På den **ange inloggningsinformation Salesforce begränsat läge om du vill aktivera automatisk användaretablering** anger du följande inställningar:
   
   ![Salesforce Sandbox](./media/active-directory-saas-salesforce-sandbox-tutorial/IC746476.png "Salesforce Sandbox")   
 1. I den **Salesforce Sandbox administratörsanvändarnamnet** textruta typen en Salesforce sandbox kontonamn som har den **systemadministratören** profil i Salesforce.com som tilldelats.
 2. I den **Salesforce Sandbox adminlösenord** textruta skriver du lösenordet för det här kontot.
 3. I den **användartoken säkerhet** textruta klistra in säkerhet token-värde.
 4. Klicka på **verifiera** att kontrollera konfigurationen.
 5. Klicka på den **nästa** för att öppna den **bekräftelse** sidan.
9. På den **bekräftelse** klickar du på **Slutför** att spara konfigurationen.
   
## <a name="assigning-users"></a>Tilldela användare

Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Om du vill tilldela användare till Salesforce Sandbox, utför du följande steg:**

1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den ** Salesforce Sandbox ** integreringssidan för programmet, klickar du på **tilldela användare**.
   
   ![Tilldela användare](./media/active-directory-saas-salesforce-sandbox-tutorial/IC769574.png "tilldela användare")
3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
   ![Ja](./media/active-directory-saas-salesforce-sandbox-tutorial/IC767830.png "Ja")

Nu bör du vänta i 10 minuter och kontrollera att kontot har synkroniserats till Salesforce Sandbox.

Om du vill testa inställningarna för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](https://msdn.microsoft.com/library/dn308586).

