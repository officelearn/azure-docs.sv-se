---
title: Azure AD App-Proxy och Qlik mening | Microsoft Docs
description: Aktivera Application Proxy på Azure-portalen och installera anslutningsverktyget för omvänd proxy.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/03/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e10fefdd3bd46aeb90fd2cfc82d4fee3b17d867b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="application-proxy-and-qlik-sense"></a>Application Proxy och Qlik mening 
Azure Active Directory Application Proxy och Qlik mening samarbetar tillsammans för att säkerställa att du kan enkelt använda programproxy för att ge fjärråtkomst för distributionen Qlik mening.  

## <a name="prerequisites"></a>Krav 
Resten av det här scenariot förutsätter att du har gjort följande:
 
- Konfigurerad [Qlik meningsfullt](https://community.qlik.com/docs/DOC-19822). 
- [Har installerat en Application Proxy connector](active-directory-application-proxy-enable.md#install-and-register-a-connector) 
 
## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 
Om du vill publicera QlikSense, måste du publicera två program i Azure.  

### <a name="application-1"></a>Programmet #1: 
Följ dessa steg om du vill publicera en app. En mer detaljerad genomgång av steg 1 – 8, se [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md). 


1. Logga in på Azure-portalen som global administratör. 
2. Välj **Azure Active Directory** > **företagsprogram**. 
3. Välj **Lägg till** längst upp på bladet. 
4. Välj **lokalt program**. 
5.       Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningarna: 
    - **Intern URL**: det här programmet ska ha en intern URL är Webbadressen QlikSense sig själv. Till exempel **https&#58;//demo.qlikemm.com:4244** 
    - **Förautentiseringsmetoden**: Azure Active Directory (rekommenderas men krävs inte) 
1.       Välj **Lägg till** längst ned på bladet. Programmet har lagts till och snabb start-menyn öppnas. 
2.       Snabb startmenyn väljer du **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testet kontot har åtkomst till lokala program. 
3.       Välj **tilldela** spara Användartilldelning test. 
4.       (Valfritt) Välj enkel inloggning på bladet hantering. Välj **Kerberos-begränsad delegering** från den nedrullningsbara menyn och Fyll i obligatoriska fält baserat på konfigurationen av Qlik. Välj **Spara**. 

### <a name="application-2"></a>Programmet #2: 
Följ samma steg för programmet #1, med följande undantag: 

**Steg #5**: Intern URL: en ska nu vara QlikSense-URL: en med den autentiseringsport som används av programmet. Standardvärdet är **4244** för HTTPS och 4248 för HTTP. Ex: **https&#58;//demo.qlik.com:4244**</br></br> 
**Steg #10:** inte konfigurera enkel inloggning och lämna den **enkel inloggning inaktiverad**
 
 
## <a name="testing"></a>Testning 
Tillämpningsprogrammet är nu redo att testa. Åtkomst till den externa Webbadressen som du använde för att publicera QlikSense i programmet nr 1 och logga in som en användare som tilldelats båda programmen.  

## <a name="next-steps"></a>Nästa steg

- [Publicera program med Application Proxy](application-proxy-publish-azure-portal.md)
- [Arbeta med Application Proxy kopplingar](active-directory-application-proxy-connectors-azure-portal.md).
