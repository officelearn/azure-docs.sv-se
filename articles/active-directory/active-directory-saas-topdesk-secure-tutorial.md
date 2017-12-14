---
title: "Självstudier: Azure Active Directory-integrering med TOPdesk - säker | Microsoft Docs"
description: "Lär dig hur du använder TOPdesk - skydda med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!."
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 8e149d2d-7849-48ec-9993-31f4ade5fdb4
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
ms.openlocfilehash: e165a18c5a24071d52838ca95edbb5e6b39e63a5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Självstudier: Azure Active Directory-integrering med TOPdesk - säker
Syftet med den här kursen är att visa integreringen av Azure och TOPdesk - säker.  
Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En TOPdesk - aktiverad prenumeration säker enkel inloggning

Den här kursen Azure AD-användare som du har tilldelat TOPdesk - säker kommer att kunna enkel inloggning till programmet på din TOPdesk - säkra företagets webbplats (service provider initierade inloggning) eller med hjälp av den [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

Det scenario som beskrivs i den här kursen består av följande byggblock:

1. Aktivera programintegrationstyp för TOPdesk - säker
2. Konfigurera enkel inloggning
3. Konfigurera användaretablering
4. Tilldela användare

![Scenariot](./media/active-directory-saas-topdesk-secure-tutorial/IC790596.png "Scenario")

## <a name="enabling-the-application-integration-for-topdesk---secure"></a>Aktivera programintegrationstyp för TOPdesk - säker
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för TOPdesk - säker.

### <a name="to-enable-the-application-integration-for-topdesk---secure-perform-the-following-steps"></a>Om du vill aktivera programmet-integration för TOPdesk - säker, utför följande steg:
1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-topdesk-secure-tutorial/IC700993.png "Active Directory")

2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.

3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
    ![Program](./media/active-directory-saas-topdesk-secure-tutorial/IC700994.png "program")

4. Klicka på **Lägg till** längst ned på sidan.
   
    ![Lägg till program](./media/active-directory-saas-topdesk-secure-tutorial/IC749321.png "lägga till program")

5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
    ![Lägga till ett program från gallerry](./media/active-directory-saas-topdesk-secure-tutorial/IC749322.png "lägga till ett program från gallerry")

6. I den **sökrutan**, typen **TOPdesk - säker**.
   
    ![Programgalleriet](./media/active-directory-saas-topdesk-secure-tutorial/IC790597.png "Programgalleriet")

7. I resultatfönstret, Välj **TOPdesk - säker**, och klicka sedan på **Slutför** lägga till programmet.
   
    ![TOPdesk - säker](./media/active-directory-saas-topdesk-secure-tutorial/IC791933.png "TOPdesk - säker")

## <a name="configuring-single-sign-on"></a>Konfigurera enkel inloggning
Syftet med det här avsnittet är att beskriva hur användarna att autentisera till TOPdesk - skydda med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.  
Konfigurera enkel inloggning för TOPdesk - säker kräver att du överför en logotyp ikonfil. Kontakta supportteamet TOPdesk för att få ikonfilen.

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Utför följande steg för att konfigurera enkel inloggning:
1. Logga in på ditt **TOPdesk - säker** företagets webbplats som administratör.
2. I den **TOPdesk** -menyn klickar du på **inställningar**.
   
    ![Inställningar för](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "inställningar")

3. Klicka på **inloggningsinställningar**.
   
    ![Inloggningsinställningar](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "inloggningsinställningar")

4. Expandera den **inloggningsinställningar** -menyn och klicka sedan på **allmänna**.
   
    ![Allmän](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Allmänt")

5. I den **Secure** avsnitt i den **SAML inloggningen** konfiguration och utför följande steg:
   
    ![Tekniska inställningar](./media/active-directory-saas-topdesk-secure-tutorial/IC790855.png "tekniska inställningar")
   
    a. Klicka på **hämta** att hämta metadatafilen offentliga och sedan spara det lokalt på datorn.
   
    b. Öppna metadatafilen och leta upp den **AssertionConsumerService** nod.
    
    ![Assertion kundtjänst](./media/active-directory-saas-topdesk-secure-tutorial/IC790856.png "Assertion kundtjänst")
   
    c. Kopiera den **AssertionConsumerService** värde.  
      
    > [!NOTE]
    > Du behöver värdet i den **konfigurera App-URL** senare i den här kursen.
    > 
    > 

6. Logga in i ett annat webbläsarfönster din **klassiska Azure-portalen** som administratör.

7. På den **TOPdesk - säker** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den ** Konfigurera enkel inloggning ** dialogrutan.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-topdesk-secure-tutorial/IC790602.png "Konfigurera enkel inloggning")

8. På den **hur vill du att användarna kan logga in på TOPdesk - säker** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
    ![Konfigurera enkel inloggning](./media/active-directory-saas-topdesk-secure-tutorial/IC790603.png "Konfigurera enkel inloggning")

9. På den **konfigurera App-URL** utför följande steg:
   
    ![Konfigurera App-URL](./media/active-directory-saas-topdesk-secure-tutorial/IC790604.png "konfigurera App-URL")
   
    a. I den **TOPdesk - säker inloggning på URL: en** textruta, Skriv URL-Adressen används av användarna att logga in på din TOPdesk - säkert program (t.ex. ”:*https://qssolutions.topdesk.net*”).
   
    b. I den **TOPdesk – offentliga Reply URL** textruta klistra in den **TOPdesk - URL för säker AssertionConsumerService** (t.ex. ”:*https://qssolutions.topdesk.net/tas/public/login/saml*”)
   
    c. Klicka på **Nästa**.

10. På den **Konfigurera enkel inloggning på TOPdesk - säker** att hämta metadatafil klickar du på **hämtar metadata**, och sedan spara filen lokalt på datorn.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-topdesk-secure-tutorial/IC790605.png "Konfigurera enkel inloggning")

11. Utför följande steg för att skapa en certifikatfil:
    
    ![Certifikatet](./media/active-directory-saas-topdesk-secure-tutorial/IC790606.png "certifikat")
    
    a. Öppna metadatafilen hämtade.
    b. Expandera den **RoleDescriptor** nod som har en **xsi: type** av **aggregeringsdesignprocessen: ApplicationServiceType**.
    c. Kopiera värdet för den **X509Certificate** nod.
    d. Spara den kopierade **X509Certificate** värdet lokalt på din dator i en fil.

12. Skydda företagets webbplats på din TOPdesk - på den **TOPdesk** -menyn klickar du på **inställningar**.
    
    ![Inställningar för](./media/active-directory-saas-topdesk-secure-tutorial/IC790598.png "inställningar")

13. Klicka på **inloggningsinställningar**.
    
    ![Inloggningsinställningar](./media/active-directory-saas-topdesk-secure-tutorial/IC790599.png "inloggningsinställningar")

14. Expandera den **inloggningsinställningar** -menyn och klicka sedan på **allmänna**.
    
    ![Allmän](./media/active-directory-saas-topdesk-secure-tutorial/IC790600.png "Allmänt")

15. I den **offentliga** klickar du på **Lägg till**.
    
    ![Lägg till](./media/active-directory-saas-topdesk-secure-tutorial/IC790607.png "Lägg till")

16. På den **SAML configuration assistenten** dialogrutan utför följande steg:
    
    ![SAML Configuration assistenten](./media/active-directory-saas-topdesk-secure-tutorial/IC790608.png "assistenten för SAML-konfiguration")
    
    a. Att överföra metadatafilen hämtade under **Federationsmetadata**, klickar du på **Bläddra**.

    b. Att överföra din certifikatfil under **certifikat (RSA)**, klickar du på **Bläddra**.

    c. Att överföra logotypfilen som du har fått från supportteamet TOPdesk under **logotypen ikonen**, klickar du på **Bläddra**.

    d. I den **användarnamn** textruta typen **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**.

    e. I den **visningsnamn** textruta, ange ett namn för din konfiguration.

    f. Klicka på **Spara**.

17. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-topdesk-secure-tutorial/IC790609.png "Konfigurera enkel inloggning")

## <a name="configuring-user-provisioning"></a>Konfigurera användaretablering
För att aktivera Azure AD-användare att logga in på TOPdesk - säker, de måste etableras i TOPdesk - säker.  
När det gäller TOPdesk - säker, etablering är en manuell aktivitet.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Utför följande steg för att konfigurera användaretablering:
1. Logga in på ditt **TOPdesk - säker** företagets webbplats som administratör.
2. Klicka på menyn högst upp **TOPdesk \> ny \> stödfiler \> operatorn**.
   
    ![Operatorn](./media/active-directory-saas-topdesk-secure-tutorial/IC790610.png "Operator")

3. På den **New-operatorn** dialogrutan, utför följande steg:
   
    ![New-operatorn](./media/active-directory-saas-topdesk-secure-tutorial/IC790611.png "New-operatorn")
   
    a. Klicka på fliken Allmänt.
   
    b. I den **efternamn** textruta för den **allmänna** Skriv ett giltigt Azure Active Directory-konto som du vill etablera efternamn.
   
    c. Välj en **plats** för kontot i den **plats** avsnitt.
   
    d. I den **inloggningsnamnet** textruta för den **TOPdesk inloggning** skriver ett inloggningsnamn för användaren.
   
    e. Klicka på **Spara**.

> [!NOTE]
> Du kan använda alla andra TOPdesk - säker användare skapa verktyg eller API: er som tillhandahålls av TOPdesk - säker att tillhandahålla AAD användarkonton.
> 
> 

## <a name="assigning-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

### <a name="to-assign-users-to-topdesk---secure-perform-the-following-steps"></a>Om du vill tilldela användare till TOPdesk - säker, utför följande steg:
1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den ** TOPdesk - säker ** integreringssidan för programmet, klickar du på **tilldela användare**.
   
    ![Tilldela användare](./media/active-directory-saas-topdesk-secure-tutorial/IC790612.png "tilldela användare")

3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
    ![Ja](./media/active-directory-saas-topdesk-secure-tutorial/IC767830.png "Ja")

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

