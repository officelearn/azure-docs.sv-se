---
title: "Självstudier: Azure Active Directory-integrering med Replicon | Microsoft Docs"
description: "Lär dig hur du använder Replicon med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/23/2017
ms.author: jeedes
ms.openlocfilehash: 2aeeceb61191962b62892b8409218684f76c6fa8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-replicon"></a>Självstudier: Azure Active Directory-integrering med Replicon
Syftet med den här kursen är att visa integreringen av Azure och Replicon. Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En Replicon-klient

Den här kursen Azure AD-användare som du har tilldelat Replicon kommer att kunna enkel inloggning till programmet på din Replicon företagets plats (service provider initierade inloggning) eller med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

Det scenario som beskrivs i den här kursen består av följande byggblock:

1. Aktivera programintegrationstyp för Replicon
2. Konfigurera enkel inloggning (SSO)
3. Konfigurera användaretablering
4. Tilldela användare

![Scenariot](./media/active-directory-saas-replicon-tutorial/IC777798.png "Scenario")

## <a name="enable-the-application-integration-for-replicon"></a>Aktivera application-integration för Replicon
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för Replicon.

**Utför följande steg om du vill aktivera programmet-integrering för Replicon:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program](./media/active-directory-saas-replicon-tutorial/IC700994.png "program")
4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Lägg till program](./media/active-directory-saas-replicon-tutorial/IC749321.png "lägga till program")
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Lägga till ett program från gallerry](./media/active-directory-saas-replicon-tutorial/IC749322.png "lägga till ett program från gallerry")
6. I den **sökrutan**, typen **Replicon**.
   
    ![Programgalleriet](./media/active-directory-saas-replicon-tutorial/IC777799.png "programgalleriet")
7. I resultatfönstret, Välj **Replicon**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
   
## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till Replicon med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.

**Utför följande steg för att konfigurera enkel inloggning:**

1. I den klassiska Azure-portalen på den **Replicon** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-replicon-tutorial/IC777801.png "Konfigurera enkel inloggning")
2. På den **hur vill du att användarna kan logga in på Replicon** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-replicon-tutorial/IC777802.png "Konfigurera enkel inloggning")
3. På den **konfigurera App-URL** utför följande steg:
   
    ![Konfigurera app-URL](./media/active-directory-saas-replicon-tutorial/IC777803.png "konfigurera app-URL")
  1. I den **Replicon logga URL** textruta Skriv din Replicon klient-URL (t.ex.: *https://na2.replicon.com/company/saml2/sp-sso/post*).
  2. I den **Replicon Reply URL** textruta Skriv din Replicon **AssertionConsumerService** URL (t.ex.: *https://global.replicon.com/! / saml2/företag/sso/post*).  
      
     >[!NOTE]
     >Du kan hämta Webbadressen från Replicon metadata på: **https://global.replicon.com/! /saml2/\<YourCompanyKey\>**.
     > 
     > 
 
  3. Klicka på **Nästa**.

4. På den **Konfigurera enkel inloggning på Replicon** att hämta metadata, klickar du på **hämtar metadata**, och sedan spara metadata på datorn.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-replicon-tutorial/IC777804.png "Konfigurera enkel inloggning")
5. Logga in på webbplatsen Replicon företag som en administratör i en annan webbläsarfönster.

6. Utför följande steg för att konfigurera SAML 2.0:
   
    ![Aktivera SAML-autentisering](./media/active-directory-saas-replicon-tutorial/IC777805.png "aktivera SAML-autentisering")
  
  1. Visa den **EnableSAML Authentication2** dialogrutan Lägg till följande i din URL efter din nyckel för företag: **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**  
    * Nedan visas schemat för fullständig URL:  
   **https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
   2. Klicka på den  **+**  att expandera den **v20Configuration** avsnitt.
   3. Klicka på den  **+**  att expandera den **metaDataConfiguration** avsnitt.
   4. Klicka på **Välj fil**, för att välja identitet providern metadata XML-filen och klicka på **skicka**.

7. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-replicon-tutorial/IC778418.png "Konfigurera enkel inloggning")
   
## <a name="configure-user-provisioning"></a>Konfigurera användaretablering

För att aktivera Azure AD-användare att logga in på Replicon etableras de i Replicon.  

När det gäller Replicon är etablering en manuell aktivitet.

**Utför följande steg för att konfigurera användaretablering:**

1. Logga in på webbplatsen Replicon företag som en administratör i ett webbläsarfönster.
2. Gå till **Administration \> användare**.
   
    ![Användare](./media/active-directory-saas-replicon-tutorial/IC777806.png "användare")
3. Klicka på **+ Lägg till användaren**.
   
    ![Lägg till användare](./media/active-directory-saas-replicon-tutorial/IC777807.png "lägga till användare")
4. I den **användarprofil** avsnittet, utför följande steg:
   
    ![Användarprofil](./media/active-directory-saas-replicon-tutorial/IC777808.png "användarprofil")
   
  1. I den **inloggningsnamnet** textruta typen Azure AD Azure AD-användare som du vill etablera e-postadress.
  2. Som **autentiseringstyp**väljer **SSO**.
  3. I den **avdelning** textruta Skriv användarens avdelning.
  4. Som **typen**väljer **administratör**.
  5. Klicka på **spara användarprofil**.

>[!NOTE]
>Du kan använda något annat Replicon användarens konto skapas verktyg eller API: er som tillhandahålls av Replicon att etablera AAD-användarkonton.
> 
> 

## <a name="assign-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Utför följande steg om du vill tilldela Replicon användare:**

1. Skapa ett testkonto i den klassiska Azure-portalen.

2. På den **Replicon** integreringssidan för programmet, klickar du på **tilldela användare**.
   
    ![Tilldela användare](./media/active-directory-saas-replicon-tutorial/IC777809.png "tilldela användare")

3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
    ![Ja](./media/active-directory-saas-replicon-tutorial/IC767830.png "Ja")

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

