---
title: Azure Active Directory Application Proxy och Tableau | Microsoft Docs
description: Lär dig hur du använder Azure Active Directory (Azure AD) Application Proxy för att ge fjärråtkomst för distributionen Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c79689ae7527a715266bad62ec50fddaab90129d
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy och Tableau 

Azure Active Directory Application Proxy och Tableau samarbetar för att säkerställa att du kan enkelt använda programproxy för att ge fjärråtkomst för distributionen Tableau. Den här artikeln förklarar hur du konfigurerar det här scenariot.  

## <a name="prerequisites"></a>Förutsättningar 

Scenario i den här artikeln förutsätter att du har:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurerats. 

- En [Application Proxy connector](active-directory-application-proxy-enable.md) installerad. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Aktivera Application Proxy för Tableau 

Om du vill använda programproxy för Tableau, måste du skicka ett e-postmeddelande till [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) att hämta det här scenariot aktiveras.
I din e-post:

-   Använda Aktivera Application Proxy för Tableau som ämne
-   Ta din klient-ID i brödtexten    

Du får en bekräftelse när du är redo att använda programmet. Detta tar vanligtvis om en vecka. Du kan dock slutföra konfigurationerna under väntan på bekräftelse.


 

## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 

Om du vill publicera Tableau, måste du publicera ett program i Azure Portal.

För:

- Detaljerade instruktioner steg 1 – 8, se [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md). 
- Information om hur du hittar Tableau värden för fälten App Proxy finns i dokumentationen för Tableau.  

**Publicera din app**: 


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör. 

2. Välj **Azure Active Directory > företagsprogram**. 

3. Välj **Lägg till** längst upp på bladet. 

4. Välj **lokalt program**. 

5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningarna: 

    - **Intern URL**: det här programmet ska ha en intern URL är Webbadressen Tableau sig själv. Till exempel `https://adventure-works.tableau.com`. 

    - **Förautentiseringsmetoden**: Azure Active Directory (rekommenderas men krävs inte). 

6. Välj **Lägg till** längst upp på bladet. Programmet har lagts till och snabb start-menyn öppnas. 

7. Snabb startmenyn väljer du **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testet kontot har åtkomst till lokala program. 

8. Välj **tilldela** spara Användartilldelning test. 

9. (Valfritt) På sidan för hantering av app väljer **enkel inloggning**. Välj **integrerad Windows-autentisering** från den nedrullningsbara menyn och Fyll i obligatoriska fält baserat på konfigurationen Tableau. Välj **Spara**. 

 

## <a name="testing"></a>Testning 

Tillämpningsprogrammet är nu redo att testa. Åtkomst till den externa Webbadressen som du använde för att publicera Tableau, och logga in som en användare som tilldelats båda programmen.



## <a name="next-steps"></a>Nästa steg

Läs mer om Azure AD Application Proxy [ge säker fjärråtkomst till lokala program](active-directory-application-proxy-get-started.md).

