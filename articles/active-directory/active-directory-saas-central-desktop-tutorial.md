---
title: "Självstudier: Azure Active Directory-integrering med Central Desktop | Microsoft Docs"
description: "Lär dig hur du använder centrala skrivbordet med Azure Active Directory för att aktivera enkel inloggning, Automatisk etablering och mycket mer!"
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: 9ad6e5946b2c1b68222b3d3a2d29c2ca93db9922
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>Självstudier: Azure Active Directory-integrering med Central Desktop
Syftet med den här kursen är att visa integreringen av Azure och centrala skrivbordet. Det scenario som beskrivs i den här kursen förutsätter att du redan har följande objekt:

* En giltig Azure-prenumeration
* En Central skrivbordet för enkel inloggning aktiverad prenumeration / centrala desktop-klient

Det scenario som beskrivs i den här kursen består av följande byggblock:

* Aktivera programintegrationstyp för Central skrivbord
* Konfigurera enkel inloggning (SSO)
* Konfigurera användaretablering
* Tilldela användare

![Scenariot](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "Scenario")

## <a name="enable-the-application-integration-for-central-desktop"></a>Aktivera application-integrering för centrala skrivbord
Syftet med det här avsnittet är att beskriva hur du aktiverar programintegrationstyp för Central skrivbordet.

**Utför följande steg om du vill aktivera programintegrationstyp för Central skrivbordet:**

1. I den klassiska Azure-portalen i det vänstra navigeringsfönstret klickar du på **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. Från den **Directory** listan, Välj den katalog som du vill aktivera katalogintegrering.
3. Klicka för att öppna vyn program i vyn directory **program** på huvudmenyn.
   
   ![Program](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "program")
4. Klicka på **Lägg till** längst ned på sidan.
   
   ![Lägg till program](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "lägga till program")
5. På den **vad vill du göra** dialogrutan klickar du på **lägga till ett program från galleriet**.
   
   ![Lägga till ett program från gallerry](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "lägga till ett program från gallerry")
6. I den **sökrutan**, typen **centrala Desktop**.
   
   ![Programgalleriet](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "programgalleriet")
7. I resultatfönstret, Välj **centrala Desktop**, och klicka sedan på **Slutför** lägga till programmet.
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "centrala Desktop")
   
## <a name="configure-single-sign-on"></a>Konfigurera enkel inloggning

Syftet med det här avsnittet är att beskriva hur användarna att autentisera till centrala skrivbordet med sitt konto i Azure AD med hjälp av federation baserat på SAML-protokoll.

Som en del av den här proceduren måste krävs du för att överföra en Base64-kodade certifikat till din centrala Desktop-klient.  
Om du inte är bekant med den här proceduren, se [hur du konverterar ett binärt certifikat till en textfil](http://youtu.be/PlgrzUZ-Y1o).

**Utför följande steg för att konfigurera enkel inloggning:**

1. I den klassiska Azure-portalen på den **centrala Desktop** integreringssidan för programmet, klickar du på **Konfigurera enkel inloggning** att öppna den ** Konfigurera enkel inloggning ** dialogrutan.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Konfigurera enkel inloggning")
2. På den **hur vill du att användarna kan logga in på centrala Desktop** väljer **Microsoft Azure AD enkel inloggning**, och klicka sedan på **nästa**.
   
   ![Konfigurera enkel inloggning](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Konfigurera enkel inloggning")
3. På den **konfigurera App-URL** , utför följande steg, och klickar sedan på **nästa**: 
   
   1. I den **Desktop tecken i URL: en Central** textruta anger du URL för din centrala Desktop-klient (t.ex.: *http://contoso.centraldesktop.com*).
   2. Ange din centrala Desktop AssertionConsumerService URL i textrutan centrala Desktop Reply URL (t.ex.: https://contoso.centraldesktop.com/saml2-assertion.php).
   
   >[!NOTE]
   >Du kan hämta värdet från central skrivbord metadata (t.ex.: *http://contoso.centraldesktop.com*).
   >  
   
   ![Konfigurera app-URL](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "konfigurera app-URL")
4. På den **Konfigurera enkel inloggning på centrala Desktop** att ladda ned ditt certifikat klickar du på **hämta certifikat**, och sedan spara certifikatfilen på datorn.
   
  ![Konfigurera enkel inloggning](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Konfigurera enkel inloggning")
5. Logga in på ditt **centrala Desktop** klient.
6. Gå till **inställningar**, klickar du på **Avancerat**, och klicka sedan på **enkel inloggning**.
   
  ![-Installation - avancerade](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "-installation - avancerade")
7. På den **enkel inloggning på inställningar** utför följande steg:
   
  ![Enkel inloggning på inställningar](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "enkel inloggning på inställningar")
   
  1. Välj **aktivera SAML v2 för enkel inloggning**.
  2. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på centrala Desktop** sidan, kopiera den **utfärdar-URL** värdet och klistrar in det i den **SSO URL** textruta.
  3. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på centrala Desktop** sidan, kopiera den **Remote inloggnings-URL** värdet och klistrar in det i den **inloggnings-URL för SSO** textruta .
  4. I den klassiska Azure-portalen på den **Konfigurera enkel inloggning på centrala Desktop** sidan, kopiera den **tjänst-URL för enkel Sign-Out** värdet och klistrar in det i den **SSO logga ut URL**textruta.
8. I den **meddelandet signatur verifieringsmetod** avsnittet, utför följande steg:
   
   ![Meddelandet signatur verifieringsmetod](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "meddelande signatur verifieringsmetod")
   
  1. Välj **certifikat**.
  2. Från den **SSO certifikat** väljer **RSH SHA256**.
  3. Skapa en textfil från hämtade certifikatet, kopiera innehållet i filen och klistrar in det i den **SSO certifikat** fältet.  
     >[!TIP]
     >Mer information finns i [hur du konverterar ett binärt certifikat till en textfil](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. Välj **visas en länk till inloggningssidan för SAMLv2**.
9. Klicka på **uppdatering**.
10. Välj bekräftelsen konfiguration för enkel inloggning på den klassiska Azure-portalen och klicka sedan på **Slutför** att stänga den **Konfigurera enkel inloggning** dialogrutan.
    
    ![Konfigurera enkel inloggning](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Konfigurera enkel inloggning")
    
## <a name="configure-user-provisioning"></a>Konfigurera användaretablering

För AAD-användare för att kunna logga in, måste de etableras till centrala Desktop-programmet. Det här avsnittet beskriver hur du skapar AAD användarkonton i centrala Desktop.

**Att etablera användarkonton till centrala skrivbordet:**
1. Logga in på din centrala Desktop-klient.
2. Gå till **personer \> inre medlemmar**.
3. Klicka på **lägga till inre medlemmar**.
   
  ![Personer](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "personer")
4. I den **e-postadressen för nya medlemmar** textruta skriver ett AAD-konto du vill etablera och klicka sedan på **nästa**.
   
  ![E-postadresser för nya medlemmar](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "e-postadresser för nya medlemmar")
5. Klicka på **lägga till interna medlem(mar)**.
   
  ![Lägga till inre medlem](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "lägga till inre medlem")
   
   >[!NOTE]
   >Du har lagt till användare får ett e-postmeddelande som innehåller en bekräftelse-länk som de behöver klicka om du vill aktivera kontot.
   > 

>[!NOTE]
>Du kan använda något annat centrala Desktop användarens konto skapas verktyg eller API: er som tillhandahålls av centrala skrivbordet till etablera AAD-användarkonton
>  

## <a name="assign-users"></a>Tilldela användare
Om du vill testa konfigurationen måste du bevilja Azure AD-användare som du vill tillåta med hjälp av ditt programåtkomst till den genom att tilldela dem.

**Utför följande steg för att tilldela användare till centrala skrivbordet:**

1. Skapa ett testkonto i den klassiska Azure-portalen.
2. På den **centrala Desktop** integreringssidan för programmet, klickar du på **tilldela användare**.
   
   ![Tilldela användare](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "tilldela användare")
3. Välj din testanvändare, klicka på **tilldela**, och klicka sedan på **Ja** att bekräfta din tilldelning.
   
   ![Ja](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "Ja")

Om du vill testa dina inställningar för enkel inloggning, öppna åtkomstpanelen. Mer information om åtkomstpanelen finns [introduktion till åtkomstpanelen](active-directory-saas-access-panel-introduction.md).

