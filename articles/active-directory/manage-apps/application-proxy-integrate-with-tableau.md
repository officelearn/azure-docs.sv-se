---
title: Azure Active Directory Application Proxy och Tableau | Microsoft Docs
description: Lär dig hur du använder Azure Active Directory (Azure AD) Application Proxy för att tillhandahålla fjärråtkomst för distributionen av bland annat Tableau.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: 8a4231e5d49dad790855293ac50d475e499e0993
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54476163"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Azure Active Directory Application Proxy och Tableau 

Azure Active Directory Application Proxy och Tableau samarbetar för att kontrollera att du enkelt kan använda Application Proxy för att tillhandahålla fjärråtkomst för distributionen av bland annat Tableau. Den här artikeln förklarar hur du konfigurerar det här scenariot.  

## <a name="prerequisites"></a>Förutsättningar 

Scenariot i den här artikeln förutsätter att du har:

- [Tableau](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurerats. 

- En [Application Proxy connector](application-proxy-add-on-premises-application.md) installerad. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Aktiverar programproxy för Tableau 

Programproxyn har stöd för den OAuth 2.0 ge Flow, vilket krävs för Tableau ska fungera korrekt. Det innebär att det inte längre några särskilda åtgärder som krävs för att aktivera det här programmet än konfigurationen av den genom att följa publishing stegen nedan.


## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 

Om du vill publicera Tableau, måste du publicera ett program i Azure Portal.

För:

- Detaljerade instruktioner steg 1-8, se [publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md). 
- Information om hur du hittar Tableau värden för fälten App Proxy Se Tableau-dokumentationen.  

**Att publicera din app**: 


1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör. 

2. Välj **Azure Active Directory > företagsprogram**. 

3. Välj **Lägg till** överst på bladet. 

4. Välj **lokala program**. 

5. Fyll i de obligatoriska fälten med information om den nya appen. Använd följande riktlinjer för inställningar: 

    - **Intern URL**: Det här programmet ska ha en intern URL som är den URL som Tableau själva. Till exempel `https://adventure-works.tableau.com`. 

    - **Förautentiseringsmetod**: Azure Active Directory (rekommenderas men krävs inte). 

6. Välj **Lägg till** överst på bladet. Programmet har lagts till och snabb start-menyn öppnas. 

7. Snabb start-menyn, Välj **tilldela en användare för att testa**, och Lägg till minst en användare till programmet. Kontrollera att det här testkontot har åtkomst till dina lokala program. 

8. Välj **tilldela** att spara Användartilldelning test. 

9. (Valfritt) På sidan för hantering av app väljer **enkel inloggning**. Välj **integrerad Windows-autentisering** från den nedrullningsbara menyn och Fyll i de obligatoriska fälten baserat på konfigurationen Tableau. Välj **Spara**. 

 

## <a name="testing"></a>Testning 

Programmet är nu redo att testa. Få åtkomst till den externa URL: en som du använde för att publicera Tableau, och logga in som en användare som tilldelats båda programmen.



## <a name="next-steps"></a>Nästa steg

Läs mer om Azure AD-programproxy [att tillhandahålla säker fjärråtkomst till lokala program](application-proxy.md).

