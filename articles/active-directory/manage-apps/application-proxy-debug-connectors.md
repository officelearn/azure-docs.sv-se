---
title: Felsöka programproxy-kopplingar – Azure Active Directory | Microsoft-dokument
description: Felsök problem med Azure Active Directory (Azure AD) Application Proxy-kopplingar.
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311846"
---
# <a name="debug-application-proxy-connector-issues"></a>Felsöka problem med anslutningsprogram för programproxy 

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory (Azure AD) Application Proxy-kopplingar. Om du använder tjänsten Programproxy för fjärråtkomst till ett lokalt webbprogram, men du har problem med att ansluta till programmet, använder du det här flödesschemat för att felsöka anslutningsproblem. 

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har installerat Application Proxy-anslutningen och har ett problem. Vid felsökning av programproxyproblem rekommenderar vi att du börjar med det här felsökningsflödet för att avgöra om Application Proxy-kopplingar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet följer du felsökningsflödet i [Problem med felsökningsprogramproxyprogrammet](application-proxy-debug-apps.md).  


Mer information om Programproxy och använda dess kopplingar finns i:

- [Fjärråtkomst till lokala program via Programproxy](application-proxy.md)
- [Proxy-anslutningsappar för program](application-proxy-connectors.md)
- [Installera och registrera ett anslutningsprogram](application-proxy-add-on-premises-application.md)
- [Felsöka problem med programproxy och felmeddelanden](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Flödesschema för anslutningsproblem

Det här flödesschemat går igenom stegen för felsökning av några av de vanligaste anslutningsproblemen. Mer information om varje steg finns i tabellen efter flödesschemat.

![Flödesschema som visar steg för felsökning av en koppling](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Åtgärd | Beskrivning | 
|---------|---------|---------|
|1 | Hitta den kopplingsgrupp som tilldelats appen | Du har förmodligen en anslutning installerad på flera servrar, i vilket fall kopplingarna ska [tilldelas anslutningsgrupper](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Mer information om anslutningsgrupper finns i [Publicera program i separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md). |
|2 | Installera kopplingen och tilldela en grupp | Om du inte har en anslutning installerad läser du [Installera och registrera en anslutning](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Om du har problem med att installera kopplingen läser [du Problem med att installera kopplingen](application-proxy-connector-installation-problem.md).<br></br> Om kopplingen inte har tilldelats en grupp läser du [Tilldela kopplingen till en grupp](application-proxy-connector-groups.md#create-connector-groups).<br></br>Om programmet inte har tilldelats en kopplingsgrupp läser du [Tilldela programmet till en kopplingsgrupp](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Köra ett porttest på anslutningsservern | På anslutningsservern kör du ett porttest med [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) eller annat porttestverktyg för att kontrollera om portarna 443 och 80 är öppna.|
|4 | Konfigurera domäner och portar | [Kontrollera att dina domäner och portar är korrekt konfigurerade](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) För att anslutningen ska fungera korrekt finns det vissa portar som måste vara öppna och webbadresser som servern måste kunna komma åt. |
|5 | Kontrollera om en backend-proxy används | Kontrollera om kontakterna använder backend-proxyservrar eller kringgår dem. Mer information finns i [Felsöka anslutningsproxyproblem och problem med tjänstanslutningen](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Uppdatera kopplingen och updater för att använda backend-proxyn | Om en backend-proxy används, bör du se till att anslutningen använder samma proxy. Mer information om felsökning och konfiguration av anslutningsappar så att de fungerar med proxyservrar finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Läsa in appens interna URL på anslutningsservern | Läs in appens interna URL på anslutningsservern. |
|8 | Kontrollera intern nätverksanslutning | Det finns ett anslutningsproblem i det interna nätverket som felsökningsflödet inte kan diagnostisera. Programmet måste vara tillgängligt internt för att kopplingarna ska fungera. Du kan aktivera och visa anslutningshändelseloggar enligt beskrivningen i [Application Proxy-kopplingar](application-proxy-connectors.md#under-the-hood). |
|9 | Förläng time-out-värdet på baksidan | Ändra timeout-inställningen för **backend-program** till **Lång**i **ytterligare inställningar** för ditt program . Se [Lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Om problemen kvarstår, rikta specifika flödesproblem, granska app- och SSO-felsökningsflöden | Använd [felsökningsprogrammets proxyprogram problem](application-proxy-debug-apps.md) med felsökningsflödet. |

## <a name="next-steps"></a>Nästa steg


* [Publicera program i separata nätverk och platser med hjälp av anslutningsgrupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka programproxy och anslutningsfel](application-proxy-troubleshoot.md)
* [Så här installerar du Azure AD Application Proxy Connector tyst](application-proxy-register-connector-powershell.md)
