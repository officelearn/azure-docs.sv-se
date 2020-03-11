---
title: Azure AD App Proxy och Qlik Sense | Microsoft Docs
description: Aktivera Application Proxy på Azure-portalen och installera anslutningsverktyget för omvänd proxy.
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
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79036998"
---
# <a name="application-proxy-and-qlik-sense"></a>Programproxy och Qlik Sense 
Azure Active Directory-programproxy och Qlik Sense samarbetar tillsammans för att kontrollera att du enkelt kan använda Application Proxy för att tillhandahålla fjärråtkomst för din Qlik Sense-distribution.  

## <a name="prerequisites"></a>Förutsättningar 
Resten av det här scenariot förutsätter att du gjort följande:
 
- Konfigurerat [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Installerat en Application Proxy-koppling](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 
Om du vill publicera QlikSense, måste du publicera två program i Azure.  

### <a name="application-1"></a>#1-program: 
Följ dessa steg om du vill publicera din app. En mer detaljerad genom gång av steg 1-8 finns i [Publicera program med hjälp av Azure AD-programproxy](application-proxy-add-on-premises-application.md). 


1. Logga in på Azure-portalen som global administratör. 
2. Välj **Azure Active Directory** > **företags program**. 
3. Välj **Lägg till** överst på bladet. 
4. Välj **lokalt program**. 
5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningar: 
   - **Intern URL**: det här programmet bör ha en intern URL som är själva QLIKSENSE-URL: en. Till exempel **https&#58;//demo.qlikemm.com:4244** 
   - **Metod för förautentisering**: Azure Active Directory (rekommenderas men krävs inte) 
1. Välj **Lägg till** längst ned på bladet. Programmet har lagts till och snabb start-menyn öppnas. 
2. I snabb starts menyn väljer du **tilldela en användare för testning**och lägger till minst en användare i programmet. Kontrollera att det här testkontot har åtkomst till dina lokala program. 
3. Välj **tilldela** för att spara test användar tilldelningen. 
4. (Valfritt) På bladet hantering väljer du enkel inloggning. Välj **Kerberos-begränsad delegering** på den nedrullningsbara menyn och fyll i de obligatoriska fälten baserat på din Qlik-konfiguration. Välj **Spara**. 

### <a name="application-2"></a>Program #2: 
Följ samma steg för programmet #1, med följande undantag: 

**Steg #5**: den interna URL: en ska nu vara QLIKSENSE-URL: en med den port för autentisering som används av programmet. Standardvärdet är **4244** för HTTPS och **4248** för http för QlikSense-versioner före april 2018. Standardvärdet för QlikSense-versioner efter 2018 april är **443** för HTTPS och **80** för http.  T. ex. **https&#58;//demo.Qlik.com:4244**</br></br>
**Steg #10:** Konfigurera inte SSO och lämna **enkel inloggning inaktive rad**
 
 
## <a name="testing"></a>Testning 
Programmet är nu redo att testa. Åtkomst till den externa URL: en som du använde för att publicera QlikSense i programmet nr 1 och logga in som en användare som tilldelats båda programmen.  

## <a name="additional-references"></a>Ytterligare referenser
Mer information om hur du publicerar Qlik Sense med Application Proxy finns i följande Qlik community-artiklar: 
- [Azure AD med integrerad Windows-autentisering med en Kerberos-begränsad delegering med Qlik Sense](https://community.qlik.com/docs/DOC-20183)
- [Qlik Sense-integrering med Azure AD-programproxy](https://community.qlik.com/t5/Technology-Partners-Ecosystem/Azure-AD-Application-Proxy/ta-p/1528396)

## <a name="next-steps"></a>Nästa steg

- [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
- [Arbeta med Application Proxy-kopplingar](application-proxy-connector-groups.md)

