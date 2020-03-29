---
title: Azure AD App Proxy och Qlik Sense| Microsoft-dokument
description: Aktivera Programproxy i Azure-portalen och installera kopplingarna för omvänd proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2edf63da0fd09f829f936b54eb088c34dfe029a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79036998"
---
# <a name="application-proxy-and-qlik-sense"></a>Programproxy och Qlik Sense 
Azure Active Directory Application Proxy och Qlik Sense samarbetar för att säkerställa att du enkelt kan använda Programproxy för att ge fjärråtkomst för din Qlik Sense-distribution.  

## <a name="prerequisites"></a>Krav 
Resten av det här scenariot förutsätter att du har gjort följande:
 
- Konfigurerad [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Installerat en programproxyanslutning](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 
Om du vill publicera QlikSense måste du publicera två program i Azure.  

### <a name="application-1"></a>Ansökan #1: 
Följ dessa steg för att publicera din app. En mer detaljerad genomgång av steg 1-8 finns i [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md). 


1. Logga in på Azure Portal som global administratör. 
2. Välj **Azure Active Directory** > **Enterprise-program**. 
3. Välj **Lägg till** högst upp på bladet. 
4. Välj **Lokalt program**. 
5. Fyll i de obligatoriska fälten med information om din nya app. Använd följande anvisningar för inställningarna: 
   - **Intern URL:** Det här programmet bör ha en intern URL som är QlikSense-URL:en. Till exempel **https&#58;//demo.qlikemm.com:4244** 
   - **Metod för förautentisering**: Azure Active Directory (Rekommenderas men krävs inte) 
1. Välj **Lägg till** längst ned på bladet. Programmet läggs till och snabbstartsmenyn öppnas. 
2. På snabbstartsmenyn väljer du **Tilldela en användare för testning**och lägger till minst en användare i programmet. Kontrollera att det här testkontot har åtkomst till det lokala programmet. 
3. Välj **Tilldela** om du vill spara testanvändartilldelningen. 
4. (Valfritt) Välj Enkel inloggning på apphanteringsbladet. Välj **Kerberos Begränsad delegering** på den nedrullningsliga menyn och fyll i de obligatoriska fälten baserat på Qlik-konfigurationen. Välj **Spara**. 

### <a name="application-2"></a>Ansökan #2: 
Följ samma steg som för #1 program, med följande undantag: 

**Steg #5**: Den interna URL:en ska nu vara QlikSense-URL:en med autentiseringsporten som används av programmet. Standardär **4244** för HTTPS och **4248** för HTTP för QlikSense-versioner före april 2018. Standardvärdet för QlikSense-versioner efter april 2018 är **443** för HTTPS och **80** för HTTP.  Ex: **https&#58;//demo.qlik.com:4244**</br></br>
**Steg #10:** Ställ inte in SSO och lämna enkel **inloggning inaktiverad**
 
 
## <a name="testing"></a>Testning 
Din ansökan är nu redo att testa. Öppna den externa URL som du använde för att publicera QlikSense i Application #1 och logga in som en användare som tilldelats båda programmen.  

## <a name="additional-references"></a>Ytterligare referenser
Mer information om hur du publicerar Qlik Sense med Application Proxy finns i följande artiklar om Qlik-gemenskapen: 
- [Azure AD med integrerad Windows-autentisering med en Kerberos-begränsad delegering med Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense-integrering med Azure AD Application Proxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Nästa steg

- [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
- [Arbeta med Application Proxy-kopplingar](application-proxy-connector-groups.md)

