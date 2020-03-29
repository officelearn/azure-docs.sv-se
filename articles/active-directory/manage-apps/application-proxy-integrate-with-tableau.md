---
title: Azure Active Directory Application Proxy och Tableau | Microsoft-dokument
description: Lär dig hur du använder Azure Active Directory (Azure AD) Application Proxy för att ge fjärråtkomst för din Tableau-distribution.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1aa99e7e71ad78a62c1a9da303b2ecc8347ebeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65783855"
---
# <a name="azure-active-directory-application-proxy-and-tableau"></a>Proxy och tabell i Azure Active Directory-program 

Azure Active Directory Application Proxy och Tableau har samarbetat för att säkerställa att du enkelt kan använda Programproxy för att ge fjärråtkomst för din Tableau-distribution. I den här artikeln beskrivs hur du konfigurerar det här scenariot.  

## <a name="prerequisites"></a>Krav 

Scenariot i den här artikeln förutsätter att du har:

- [Tablå](https://onlinehelp.tableau.com/current/server/en-us/proxy.htm#azure) konfigurerad. 

- En [application proxy-anslutning](application-proxy-add-on-premises-application.md) installerad. 

 
## <a name="enabling-application-proxy-for-tableau"></a>Aktivera programproxy för Tableau 

Programproxy stöder OAuth 2.0 Grant Flow, som krävs för att Tableau ska fungera korrekt. Det innebär att det inte längre finns några särskilda steg som krävs för att aktivera det här programmet, förutom att konfigurera det genom att följa publiceringsstegen nedan.


## <a name="publish-your-applications-in-azure"></a>Publicera dina program i Azure 

För att publicera Tableau måste du publicera ett program i Azure Portal.

För:

- Detaljerade instruktioner för steg 1-8, se [Publicera program med Azure AD Application Proxy](application-proxy-add-on-premises-application.md). 
- Information om hur du hittar Tableau-värden för appproxyfälten finns i Tableau-dokumentationen.  

**Så här publicerar du appen:** 


1. Logga in på [Azure-portalen](https://portal.azure.com) som programadministratör. 

2. Välj **Azure Active Directory > Enterprise-program**. 

3. Välj **Lägg till** högst upp på bladet. 

4. Välj **Lokalt program**. 

5. Fyll i de obligatoriska fälten med information om din nya app. Använd följande anvisningar för inställningarna: 

    - **Intern URL:** Det här programmet bör ha en intern URL som är själva tableau-URL:en. Till exempel `https://adventure-works.tableau.com`. 

    - **Förautentiseringsmetod:** Azure Active Directory (rekommenderas men krävs inte). 

6. Välj **Lägg till** högst upp på bladet. Programmet läggs till och snabbstartsmenyn öppnas. 

7. På snabbstartsmenyn väljer du **Tilldela en användare för testning**och lägger till minst en användare i programmet. Kontrollera att det här testkontot har åtkomst till det lokala programmet. 

8. Välj **Tilldela** om du vill spara testanvändartilldelningen. 

9. (Valfritt) På sidan apphantering väljer du **Enkel inloggning**. Välj **Integrerad Windows-autentisering** på den nedrullningsliga menyn och fyll i de obligatoriska fälten baserat på tableau-konfigurationen. Välj **Spara**. 

 

## <a name="testing"></a>Testning 

Din ansökan är nu redo att testa. Öppna den externa URL som du använde för att publicera Tableau och logga in som en användare som tilldelats båda programmen.



## <a name="next-steps"></a>Nästa steg

Mer information om Azure AD Application Proxy finns i [Så här ger du säker fjärråtkomst till lokala program](application-proxy.md).

