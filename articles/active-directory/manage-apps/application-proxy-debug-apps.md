---
title: Debug Application Proxy-program - Azure Active Directory | Microsoft-dokument
description: Felsök problem med Azure Active Directory (Azure AD) Application Proxy-program.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382078"
---
# <a name="debug-application-proxy-application-issues"></a>Felsöka problem med programproxy 

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory (Azure AD) Application Proxy-program. Om du använder tjänsten Programproxy för fjärråtkomst till ett lokalt webbprogram, men du har problem med att ansluta till programmet, använder du det här flödesschemat för att felsöka programproblem. 

## <a name="before-you-begin"></a>Innan du börjar

Vid felsökning av programproxyproblem rekommenderar vi att du börjar med kontakterna. Följ först felsökningsflödet i [Debug Application Proxy Connector-problem](application-proxy-debug-connectors.md) för att kontrollera att Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem går du tillbaka till den här artikeln för att felsöka programmet.  

Mer information om Programproxy finns i:

- [Fjärråtkomst till lokala program via Programproxy](application-proxy.md)
- [Proxy-anslutningsappar för program](application-proxy-connectors.md)
- [Installera och registrera ett anslutningsprogram](application-proxy-add-on-premises-application.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Flödesschema för programproblem

Det här flödesschemat går igenom stegen för felsökning av några av de vanligaste problemen med att ansluta till programmet. Mer information om varje steg finns i tabellen efter flödesschemat.

![Flödesschema som visar steg för felsökning av ett program](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Åtgärd | Beskrivning | 
|---------|---------|---------|
|1 | Öppna en webbläsare, komma åt appen och ange dina autentiseringsuppgifter | Prova att använda dina autentiseringsuppgifter för att logga in på appen och kontrollera om det finns användarrelaterade fel, till exempel [Den här företagsappen kan inte nås](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Verifiera användartilldelning till appen | Kontrollera att ditt användarkonto har behörighet att komma åt appen inifrån företagsnätverket och testa sedan inloggningen i appen genom att följa stegen i [Testa programmet](application-proxy-add-on-premises-application.md#test-the-application). Om inloggningsproblem kvarstår läser du [Felsöka inloggningsfel](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Öppna en webbläsare och försök komma åt appen | Om ett fel visas omedelbart kontrollerar du att programproxy är korrekt konfigurerad. Mer information om specifika felmeddelanden finns i [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md).  |
|4 | Kontrollera din anpassade domänkonfiguration eller felsöka felet | Om sidan inte visas alls kontrollerar du att den anpassade domänen är korrekt konfigurerad genom att granska [Arbeta med anpassade domäner](application-proxy-configure-custom-domain.md).<br></br>Om sidan inte läses in och ett felmeddelande visas felsöker du felet genom att referera till [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md). <br></br>Om det tar längre tid än 20 sekunder innan ett felmeddelande visas kan det uppstå anslutningsproblem. Gå till [felsökningsartikeln för felsökning av felsökningsprogram.](application-proxy-debug-connectors.md)  |
|5 | Om problemen kvarstår går du till felsökning av anslutning | Det kan finnas ett anslutningsproblem mellan proxyn och anslutningen eller mellan kopplingen och serverdelen. Gå till [felsökningsartikeln för felsökning av felsökningsprogram.](application-proxy-debug-connectors.md) |
|6 | Publicera alla resurser, kontrollera webbläsarutvecklare och åtgärda länkar | Kontrollera att publiceringssökvägen innehåller alla nödvändiga bilder, skript och formatmallar för ditt program. Mer information finns i [Lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Använd webbläsarens utvecklarverktyg (F12-verktyg i Internet Explorer eller Microsoft Edge) och kontrollera att publiceringsproblem enligt beskrivningen på [programsidan inte visas korrekt](application-proxy-page-appearance-broken-problem.md). <br></br>Granskningsalternativ för att lösa brutna länkar i [Länkar på sidan fungerar inte](application-proxy-page-links-broken-problem.md). |
|7 | Sök efter nätverksfördröjning | Om sidan läses in långsamt kan du läsa om olika sätt att minimera nätverksfördröjningen i [Överväganden för att minska svarstiden](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Se ytterligare felsökningshjälp | Om problemen kvarstår kan du hitta ytterligare felsökningsartiklar i [dokumentationen för felsökning av programproxy](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Nästa steg


* [Publicera program i separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka programproxy och anslutningsfel](application-proxy-troubleshoot.md)
* [Så här installerar du Azure AD Application Proxy Connector tyst](application-proxy-register-connector-powershell.md)
