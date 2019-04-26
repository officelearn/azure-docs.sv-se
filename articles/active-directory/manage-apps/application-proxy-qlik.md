---
title: Azure AD App Proxy och Qlik Sense | Microsoft Docs
description: Aktivera Application Proxy på Azure-portalen och installera anslutningsverktyget för omvänd proxy.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: article
ms.date: 09/06/2018
ms.author: celested
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12e36c49d8c4db60302d4b72dfa81ea4aceec77b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441337"
---
# <a name="application-proxy-and-qlik-sense"></a>Programproxy och Qlik Sense 
Azure Active Directory-programproxy och Qlik Sense samarbetar tillsammans för att kontrollera att du enkelt kan använda Application Proxy för att tillhandahålla fjärråtkomst för din Qlik Sense-distribution.  

## <a name="prerequisites"></a>Förutsättningar 
Resten av det här scenariot förutsätter att du gjort följande:
 
- Konfigurerad [Qlik Sense](https://community.qlik.com/docs/DOC-19822). 
- [Installerat en Application Proxy connector](application-proxy-add-on-premises-application.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 
Om du vill publicera QlikSense, måste du publicera två program i Azure.  

### <a name="application-1"></a>#1-program: 
Följ dessa steg om du vill publicera din app. En mer detaljerad genomgång av steg 1-8, se [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md). 


1. Logga in på Azure-portalen som global administratör. 
2. Välj **Azure Active Directory** > **företagsprogram**. 
3. Välj **Lägg till** överst på bladet. 
4. Välj **lokala program**. 
5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningar: 
   - **Intern URL**: Det här programmet ska ha en intern URL som är den URL som QlikSense själva. Till exempel **https&#58;//demo.qlikemm.com:4244** 
   - **Förautentiseringsmetod**: Azure Active Directory (rekommenderas men krävs inte) 
1. Välj **Lägg till** längst ned på bladet. Programmet har lagts till och snabb start-menyn öppnas. 
2. Snabb start-menyn, Välj **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testkontot har åtkomst till dina lokala program. 
3. Välj **tilldela** att spara Användartilldelning test. 
4. (Valfritt) På bladet hantering väljer du enkel inloggning. Välj **Kerberos-begränsad delegering** från listrutan och Fyll i de obligatoriska fälten baserat på Qlik-konfigurationen. Välj **Spara**. 

### <a name="application-2"></a>Program #2: 
Följ samma steg för programmet #1, med följande undantag: 

**Steg #5**: Den interna URL: en ska nu vara QlikSense-URL: en med den autentiseringsport som används av programmet. Standardvärdet är **4244** för HTTPS och 4248 för HTTP. Exempel: **https&#58;//demo.qlik.com:4244**</br></br> 
**Steg #10:** Inte konfigurera enkel inloggning och lämna den **enkel inloggning inaktiverad**
 
 
## <a name="testing"></a>Testning 
Programmet är nu redo att testa. Åtkomst till den externa URL: en som du använde för att publicera QlikSense i programmet nr 1 och logga in som en användare som tilldelats båda programmen.  

## <a name="additional-references"></a>Ytterligare referenser
Mer information om publicering Qlik Sense med Application Proxy finns Qlik Community-artikel: [Azure AD med integrerad Windows-autentisering med hjälp av en Kerberos-begränsad delegering med Qlik Sense](https://community.qlik.com/docs/DOC-20183).

## <a name="next-steps"></a>Nästa steg

- [Publicera program med Application Proxy](application-proxy-add-on-premises-application.md)
- [Arbeta med programproxyanslutningar](application-proxy-connector-groups.md)

