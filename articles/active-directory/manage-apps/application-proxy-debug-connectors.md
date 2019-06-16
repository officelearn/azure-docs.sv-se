---
title: Felsöka programproxyanslutningar - Azure Active Directory | Microsoft Docs
description: Felsöka problem med Azure Active Directory (Azure AD) Application Proxy-kopplingar.
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172242"
---
# <a name="debug-application-proxy-connector-issues"></a>Felsöka problem med Application Proxy-koppling 

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory (Azure AD) Application Proxy-kopplingar. Om du använder tjänsten Application Proxy för fjärråtkomst till ett webbprogram i en lokal, men du har problem med att ansluta till programmet, kan du använda den här flödesschema för att felsöka problem med anslutningen. 

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har installerat programproxy-kopplingen och har problem med. När du felsöker problem med Application Proxy rekommenderar vi att du börjar med det här flödet som felsökning för att avgöra om programproxyanslutningar är korrekt konfigurerade. Om du fortfarande har problem med att ansluta till programmet, följer du felsökning flödet i [felsöka programproxyn programproblem](application-proxy-debug-apps.md).  


Mer information om Application Proxy och med hjälp av dess anslutningar finns:

- [Fjärråtkomst till lokala program via programproxy](application-proxy.md)
- [Anslutningar för programproxy](application-proxy-connectors.md)
- [Installera och registrera en anslutningsapp](application-proxy-add-on-premises-application.md)
- [Felsöka problem med Application Proxy och felmeddelanden](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Flödesschema för problem med anslutningen

Det här flödesschemat vägleder dig genom stegen för felsökning av några av de vanliga connector problemen. Information om varje steg finns i tabellen som följer flödesscheman.

![Flödesschema som visar stegen för att felsöka en anslutning](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Åtgärd | Beskrivning | 
|---------|---------|---------|
|1 | Hitta anslutningsgruppen tilldelad till appen | Har du förmodligen en koppling installeras på flera servrar, i så fall anslutningarna bör vara [tilldelats anslutningsappgrupper](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Läs mer om anslutningsapp-grupper i [publicera program på separata nätverk och platser med hjälp av anslutningsappgrupper](application-proxy-connector-groups.md). |
|2 | Installera connector och tilldela en grupp | Om du inte har en anslutning som är installerat, se [installera och registrera en koppling](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Om anslutningen inte är tilldelad till en grupp, se [tilldela anslutningen till en grupp](application-proxy-connector-groups.md#create-connector-groups).<br></br>Om programmet inte är tilldelad till en anslutningsprogramgrupp, se [tilldela programmet till en anslutningsgrupp](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Köra ett test för port på connector-server | Kör en port-test på connector-servern med hjälp av [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) eller andra port testning för att kontrollera att portarna 443 och 80 är öppen.|
|4 | Konfigurera portar och domäner | [Se till att dina domäner och portar är korrekt konfigurerade](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) för att anslutningsappen ska fungera korrekt, det finns vissa portar som måste vara öppna och URL: er som din server måste kunna komma åt. |
|5 | Kontrollera om det är en backend-proxy som används | Kontrollera om kopplingarna använder backend-proxyservrar eller kringgå dem. Mer information finns i [felsöka problem med anslutningen proxy och tjänsten anslutningsproblem](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Uppdatera anslutning och updater att använda backend-proxy | Om en backend-proxyserver används, ska du kontrollera att anslutningen använder samma proxy. Mer information om felsökning och konfiguration av kopplingar för att arbeta med proxy-servrar finns i [fungerar med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Läsa in appens interna URL: en på anslutningsservern | Läsa in appens interna URL: en på connector-servern. |
|8 | Kontrollera interna nätverksanslutningar | Det finns ett anslutningsproblem i det interna nätverket som det här felsökning flödet inte kan diagnostisera. Programmet måste vara tillgänglig internt för kopplingar ska fungera. Du kan aktivera och visa händelseloggar för anslutningen, enligt beskrivningen i [programproxyanslutningar](application-proxy-connectors.md#under-the-hood). |
|9 | Förlänga timeout-värdet på serverdelen | I den **ytterligare inställningar** för ditt program, ändra den **tidsgräns för Backend-programmet** att ställa in **lång**. Se [lägga till en lokal app till Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Om problemen kvarstår så rikta specifikt flöde problem, granska app och SSO felsökning flöden | Använd den [felsöka programproxyn programproblem](application-proxy-debug-apps.md) felsöka flöde. |

## <a name="next-steps"></a>Nästa steg


* [Publicera program på separata nätverk och platser med hjälp av anslutningsapp-grupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka Application Proxy och anslutning](application-proxy-troubleshoot.md)
* [Tyst installation av Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)
