---
title: "Självstudier: Azure Active Directory-integrering med Coupa | Microsoft Docs"
description: "Lär dig hur du använder Coupa med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 47f27746-9057-4b9c-991e-3abf77710f73
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: c952975919cfd948f1b9ea93ff2ac2641a53f923
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Självstudier: Azure Active Directory-integrering med Coupa
Syftet med den här kursen är att visa integreringen av Azure och Coupa.  
Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En Coupa enkel inloggning (SSO) aktiverat prenumeration

Den här kursen Azure AD-användare som du har tilldelat Coupa kommer att kunna enkel inloggning till programmet med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

Det scenario som beskrivs i den här kursen består av följande byggblock:

* Aktivera programintegrationstyp för Coupa
* Konfigurera enkel inloggning
* Konfigurera användaretablering
* Tilldela användare

![Scenariot](./media/active-directory-saas-coupa-tutorial/IC791897.png "Scenario")

## <a name="enable-the-application-integration-for-coupa"></a>Aktivera application-integration för Coupa
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för Coupa.

**Utför följande steg om du vill aktivera programmet-integrering för Coupa:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
   ![Program](./media/active-directory-saas-coupa-tutorial/IC700994.png "program")
4. Klicka på **Lägg till** längst ned på sidan.
   
   ![Lägg till program](./media/active-directory-saas-coupa-tutorial/IC749321.png "lägga till program")
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
   ![Lägga till ett program från gallerry](./media/active-directory-saas-coupa-tutorial/IC749322.png "lägga till ett program från gallerry")
6. I den **sökrutan**, typen **Coupa**.
   
   ![Programgalleriet](./media/active-directory-saas-coupa-tutorial/IC791898.png "Programgalleriet")
7. I resultatfönstret, Välj **Coupa**, och klicka sedan på **Slutför** lägga till programmet.
   
   ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
   
## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till Coupa med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.  

Konfigurera enkel inloggning för Coupa måste du hämta ett tumavtrycksvärde från ett certifikat. Om du inte är bekant med den här proceduren, se [hur du hämtar ett certifikat tumavtrycksvärde](http://youtu.be/YKQF266SAxI).

**Utför följande steg för att konfigurera enkel inloggning:**

1. Logga in på webbplatsen Coupa företag som administratör.
2. Gå till **installationsprogrammet \> säkerhetskontroll**.
   
   ![Säkerhetsåtgärder](./media/active-directory-saas-coupa-tutorial/IC791900.png "säkerhetsåtgärder")
3. Om du vill hämta metadatafil Coupa till datorn klickar du på **hämta och importera SP metadata**.
   
   ![Coupa SP metadata](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP metadata")
4. Logga in på den klassiska Azure-portalen i ett annat webbläsarfönster.
5. På den **Coupa** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-coupa-tutorial/IC791902.png "Konfigurera enkel inloggning")
6. På den **hur vill du att användarna kan logga in på Coupa** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-coupa-tutorial/IC791903.png "Konfigurera enkel inloggning")
7. På den **konfigurera App-URL** utför följande steg:
   
   ![Konfigurera App-URL](./media/active-directory-saas-coupa-tutorial/IC791904.png "konfigurera App-URL")   
   1. I den **logga URL** textruta typen URL som används av dina användare logga in i tillämpningsprogrammet Coupa (t.ex. ”:*http://company.Coupa.com*”).
   2. Öppna din hämtade Coupa metadatafilen och kopiera den **AssertionConsumerService index-URL**.
   3. I den **Coupa Reply URL** textruta klistra in den **AssertionConsumerService index-URL** värde.
   4. Klicka på **Nästa**.
8. På den **Konfigurera enkel inloggning på Coupa** att hämta metadatafil klickar du på **hämtar metadata**, och sedan spara filen lokalt på datorn.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-coupa-tutorial/IC791905.png "Konfigurera enkel inloggning")
9. Gå till på webbplatsen Coupa företagets **installationsprogrammet \> säkerhetskontroll**.
   
   ![Säkerhetsåtgärder](./media/active-directory-saas-coupa-tutorial/IC791900.png "säkerhetsåtgärder")
10. I den **logga in med autentiseringsuppgifterna för Coupa** avsnittet, utför följande steg:  

   ![Logga in med autentiseringsuppgifterna för Coupa](./media/active-directory-saas-coupa-tutorial/IC791906.png "logga in med autentiseringsuppgifterna för Coupa") 
   1. Välj **logga in med SAML**.
   2. Klicka på **Bläddra** att överföra din hämtade Azure Active metadatafil.
   3. Klicka på **Spara**.
11. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
    
   ![Konfigurera enkel inloggning](./media/active-directory-saas-coupa-tutorial/IC791907.png "Konfigurera enkel inloggning")
    
## <a name="configure-user-provisioning"></a>Konfigurera användaretablering

För att aktivera Azure AD-användare att logga in på Coupa etableras de i Coupa.  

* När det gäller Coupa är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på ditt **Coupa** företagets webbplats som administratör.
2. Klicka på menyn högst upp **installationsprogrammet**, och klicka sedan på **användare**.
   
   ![Användare](./media/active-directory-saas-coupa-tutorial/IC791908.png "användare")
3. Klicka på **Skapa**.
   
   ![Skapa användare](./media/active-directory-saas-coupa-tutorial/IC791909.png "skapa användare")
4. I den **användare skapa** avsnittet, utför följande steg:
   
   ![Användarinformation](./media/active-directory-saas-coupa-tutorial/IC791910.png "användarinformation")
   
   1. Typ av **inloggning**, **Förnamn**, **efternamn**, **ID för enkel inloggning**, **e-post** attribut för ett giltigt Azure Active Directory-konto som du vill etablera i relaterade textrutor.
   2. Klicka på **Skapa**.   
   >[!NOTE]
   >Azure Active Directory kontoinnehavaren får ett e-postmeddelande med en länk till bekräfta kontot innan den aktiveras. 
   > 

>[!NOTE]
>Du kan använda något annat Coupa användarens konto skapas verktyg eller API: er som tillhandahålls av Coupa att etablera AAD-användarkonton. 
> 

## <a name="assign-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Utför följande steg om du vill tilldela Coupa användare:**

1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den ** Coupa ** integreringssidan för programmet, klickar du på **tilldela användare**.
   
   ![Tilldela användare](./media/active-directory-saas-coupa-tutorial/IC791911.png "tilldela användare")
3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
   ![Ja](./media/active-directory-saas-coupa-tutorial/IC767830.png "Ja")

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

