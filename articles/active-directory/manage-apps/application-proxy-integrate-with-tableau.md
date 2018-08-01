---
title: Azure Active Directory Application Proxy och Tableau | Microsoft Docs
description: Lär dig hur du använder Azure Active Directory (Azure AD) Application Proxy för att tillhandahålla fjärråtkomst för distributionen av bland annat Tableau.  .
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 7cace1af527c1c7c80bf0e23f7a88aa9ac9f9d03
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365030"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy och Tableau 

Azure Active Directory Application Proxy och Tableau samarbetar för att kontrollera att du enkelt kan använda Application Proxy för att tillhandahålla fjärråtkomst för distributionen av bland annat Tableau. Den här artikeln förklarar hur du konfigurerar det här scenariot.  

## <a name="prerequisites"></a>Förutsättningar 

Scenariot i den här artikeln förutsätter att du har:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurerats. 

- En [Application Proxy connector](application-proxy-enable.md) installerad. 

 

## <a name="enabling-application-proxy-for-tableau"></a>Aktiverar programproxy för Tableau 

Om du vill använda programproxy för Tableau, måste du skicka ett e- [ aadapfeedback@microsoft.com ](mailto:aadapfeedback@microsoft.com) att hämta det här scenariot aktiverat.
I din e-post:

-   Använd aktivera Application Proxy för Tableau som ämne
-   Ta med ditt klient-ID i brödtexten    

Du får en bekräftelse när du är redo att använda programmet. Du kan slutföra konfigurationerna under väntan på bekräftelse.


 

## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 

Om du vill publicera Tableau, måste du publicera ett program i Azure Portal.

För:

- Detaljerade instruktioner steg 1-8, se [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md). 
- Information om hur du hittar Tableau värden för fälten App Proxy Se Tableau-dokumentationen.  

**Att publicera din app**: 


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör. 

2. Välj **Azure Active Directory > företagsprogram**. 

3. Välj **Lägg till** överst på bladet. 

4. Välj **lokala program**. 

5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningar: 

    - **Intern URL**: det här programmet ska ha en intern URL som är den URL som Tableau själva. Till exempel `https://adventure-works.tableau.com`. 

    - **Förautentiseringsmetod**: Azure Active Directory (rekommenderas men krävs inte). 

6. Välj **Lägg till** överst på bladet. Programmet har lagts till och snabb start-menyn öppnas. 

7. Snabb start-menyn, Välj **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testkontot har åtkomst till dina lokala program. 

8. Välj **tilldela** att spara Användartilldelning test. 

9. (Valfritt) På sidan för hantering av app väljer **enkel inloggning**. Välj **integrerad Windows-autentisering** från den nedrullningsbara menyn och Fyll i de obligatoriska fälten baserat på konfigurationen Tableau. Välj **Spara**. 

 

## <a name="testing"></a>Testning 

Programmet är nu redo att testa. Få åtkomst till den externa URL: en som du använde för att publicera Tableau, och logga in som en användare som tilldelats båda programmen.



## <a name="next-steps"></a>Nästa steg

Läs mer om Azure AD-programproxy [att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md).

