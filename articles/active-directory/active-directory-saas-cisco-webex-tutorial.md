---
title: "Självstudier: Azure Active Directory-integrering med Cisco Webex | Microsoft Docs"
description: "Lär dig hur du använder Cisco Webex med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Självstudier: Azure Active Directory-integrering med Cisco Webex
Syftet med den här kursen är att visa integreringen av Azure och Cisco Webex.  
Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En Cisco-Webex-klient

Den här kursen Azure AD-användare som du har tilldelat Cisco Webex kommer att kunna enkel inloggning till programmet på din Cisco Webex företagets plats (service provider initierade inloggning) eller med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

Det scenario som beskrivs i den här kursen består av följande byggblock:

* Aktivera application-integrering för Cisco Webex
* Konfigurera enkel inloggning (SSO)
* Konfigurera användaretablering
* Tilldela användare

![Scenariot](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "Scenario")

## <a name="enable-the-application-integration-for-cisco-webex"></a>Aktivera application-integration för Cisco Webex
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för Cisco Webex.

**Om du vill aktivera programmet-integrering för Cisco Webex utför du följande steg:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
   ![Program](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "program")
4. Klicka på **Lägg till** längst ned på sidan.
   
   ![Lägg till program](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "lägga till program")
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
   ![Lägga till ett program från gallerry](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "lägga till ett program från gallerry")
6. I den **sökrutan**, typen **Cisco Webex**.
   
   ![Programgalleriet](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "Programgalleriet")
7. I resultatfönstret, Välj **Cisco Webex**, och klicka sedan på **Slutför** lägga till programmet.
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till Cisco Webex med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.  

Som en del av den här proceduren måste krävs du för att skapa en Base64-kodade certifikatet. Om du inte är bekant med den här proceduren, se [hur du konverterar ett binärt certifikat till en textfil](http://youtu.be/PlgrzUZ-Y1o)

**Utför följande steg för att konfigurera enkel inloggning:**

1. I den klassiska Azure-portalen på den **Cisco Webex** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Konfigurera enkel inloggning")
2. På den **hur vill du att användarna kan logga in på Cisco Webex** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Konfigurera enkel inloggning")
3. På den **konfigurera App-URL** , utför följande steg, och klickar sedan på **nästa**.
   
   ![Konfigurera app-URL](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "konfigurera app-URL")   
   1. I den **vaken URL** textruta Skriv din Cisco Webex klient-URL (t.ex.: *http://contoso.webex.com*).
   2. I den **Cisco Webex Reply URL** textruta typen din **Cisco Webex AssertionConsumerService URL** (t.ex.: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).
4. På den **Konfigurera enkel inloggning på Cisco Webex** att ladda ned ditt certifikat klickar du på **hämta certifikat**, och sedan spara certifikatfilen på datorn.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Konfigurera enkel inloggning")
5. Logga in på webbplatsen Cisco Webex företag som en administratör i en annan webbläsarfönster.
6. Klicka på menyn högst upp **Platsadministration**.
   
   ![Platsadministration](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Platsadministration")
7. I den **Hantera plats** klickar du på **SSO Configuration**.
   
   ![Konfiguration av SSO](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO-konfiguration")
8. Utför följande steg i avsnittet Federerad Web SSO-konfiguration:
   
   ![Federerad enkel inloggning Configuration](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "federerad enkel inloggning konfiguration")  
   1. Från den **Federation-protokollet** väljer **SAML 2.0**.
   2. Skapa en **Base64-kodad** fil från din hämtat certifikat.  
    >[!TIP]
    >Mer information finns i [hur du konverterar ett binärt certifikat till en textfil](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. Öppna din Base64-kodade certifikatet i anteckningar och kopiera innehållet i den.
   4. Klicka på **importera SAML Metadata**, och klistra sedan in din Base64-kodade certifikatet.
   5. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på Cisco Webex** dialogrutan sidan, kopiera den **utfärdar-URL** värdet och klistrar in det i den **utfärdaren för SAML (IdP-ID)** textruta.
   6. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på Cisco Webex** dialogrutan sidan, kopiera den **Remote inloggnings-URL** värdet och klistrar in det i den **inloggnings-URL för kunden SSO-Service** textruta.
   7. Från den **NameID Format** väljer **e-postadress**.
   8. I den **AuthnContextClassRef** textruta typen **urn: oasis: namn: tc: SAML:2.0:ac:classes:Password**.
   9. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på Cisco Webex** dialogrutan sidan, kopiera den **Remote logga ut URL** värdet och klistrar in det i den **kunden URL för enkel inloggning logga ut** textruta.
   10. Klicka på **uppdatering**.
9. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på Cisco Webex** dialogrutan sidan Välj konfiguration för enkel inloggning bekräftelsen och klicka sedan på **Slutför**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Konfigurera enkel inloggning")
   
## <a name="configure-user-provisioning"></a>Konfigurera användaretablering

För att aktivera Azure AD-användare att logga in på Cisco Webex etableras de i Cisco Webex.  

* Cisco Webex är etablering en manuell aktivitet.

**Utför följande steg för att etablera en användarkonton:**

1. Logga in på ditt **Cisco Webex** klient.
2. Gå till **hantera användare \> lägga till användare**.
   
   ![Lägg till användare](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "lägga till användare")
3. Utför följande steg på avsnittet Lägg till användare:
   
   ![Lägg till användare](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "Lägg till användare")   
   1. Som **kontotyp**väljer **värden**.
   2. Ange information för en befintlig Azure AD-användare i följande textrutor: **förnamn, efternamn**, **användarnamn**, **e-post**, **lösenord**, **Bekräfta lösenord**.
   3. Klicka på **Lägg till**.

>[!NOTE]
>Du kan använda något annat Cisco Webex användarens konto skapas verktyg eller API: er som tillhandahålls av Cisco Webex etablera AAD-användarkonton. 
> 

## <a name="assign-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Utför följande steg om du vill tilldela Cisco Webex användare:**

1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den **Cisco Webex** integreringssidan för programmet, klickar du på **tilldela användare**.
   
   ![Tilldela användare](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "tilldela användare")
3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
   ![Ja](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "Ja")

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

