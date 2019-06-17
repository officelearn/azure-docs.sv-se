---
title: Felsöka Application Proxy - program i Azure Active Directory | Microsoft Docs
description: Felsöka problem med Azure Active Directory (Azure AD) Application Proxy-program.
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
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172272"
---
# <a name="debug-application-proxy-application-issues"></a>Felsöka problem med Application Proxy-program 

Den här artikeln hjälper dig att felsöka problem med Azure Active Directory (Azure AD) Application Proxy-program. Om du använder tjänsten Application Proxy för fjärråtkomst till ett webbprogram i en lokal, men du har problem med att ansluta till programmet, kan du använda den här flödesschema för att felsöka programfel. 

## <a name="before-you-begin"></a>Innan du börjar

När du felsöker problem med Application Proxy rekommenderar vi att du börjar med kopplingar. Följ först felsökning flödet i [felsöka Application Proxy Connector utfärdar](application-proxy-debug-connectors.md) att kontrollera att programproxyanslutningar är korrekt konfigurerade. Om du fortfarande har problem, kan du gå tillbaka till den här artikeln om du vill felsöka programmet.  

Mer information om Application Proxy finns:

- [Fjärråtkomst till lokala program via programproxy](application-proxy.md)
- [Anslutningar för programproxy](application-proxy-connectors.md)
- [Installera och registrera en anslutningsapp](application-proxy-add-on-premises-application.md)
- [Felsöka problem med Application Proxy och felmeddelanden](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Flödesschema för programfel

Det här flödesschemat vägleder dig genom stegen för att felsöka några av de vanligaste problemen med att ansluta till programmet. Information om varje steg finns i tabellen som följer flödesscheman.

![Flödesschema som visar stegen för felsökning av program](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Åtgärd | Beskrivning | 
|---------|---------|---------|
|1 | Öppna en webbläsare, åtkomst till appen och ange dina autentiseringsuppgifter | Försök att använda dina autentiseringsuppgifter för att logga in på appen och Sök efter alla användare-relaterade fel som [företagets appen kan inte nås](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Kontrollera Användartilldelning till appen | Kontrollera att ditt användarkonto har behörighet att komma åt programmet från inuti företagsnätverket och testar sedan logga in i appen genom att följa stegen i [testa programmet](application-proxy-add-on-premises-application.md#test-the-application). Om problem med användarinloggning kvarstår [så här felsöker du inloggningsfel](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Öppna en webbläsare och försök få åtkomst till appen | Om ett felmeddelande visas omedelbart, kontrollera att Application Proxy har konfigurerats korrekt. Mer information om felmeddelanden finns [felsöka programproxyn problem och felmeddelanden](application-proxy-troubleshoot.md).  |
|4 | Kontrollera din konfiguration för anpassad domän eller felsöka felet | Om sidan inte visas alls, kontrollerar du din anpassade domän har konfigurerats korrekt genom att granska [arbeta med anpassade domäner](application-proxy-configure-custom-domain.md).<br></br>Om sidan inte läsa in och ett felmeddelande visas kan du felsöka problemet genom att referera till [felsöka programproxyn problem och felmeddelanden](application-proxy-troubleshoot.md). <br></br>Om det tar längre tid än 20 sekunder för ett felmeddelande visas, kan det vara problem med nätverksanslutningen. Gå till den [felsöka programproxyanslutningar](application-proxy-debug-connectors.md) felsökningsartikeln.  |
|5 | Om problemen kvarstår så går du till anslutningen felsökning | Det kan finnas ett problem med anslutningen mellan proxyservern och connector eller mellan anslutningstjänsten och backend-servern. Gå till den [felsöka programproxyanslutningar](application-proxy-debug-connectors.md) felsökningsartikeln. |
|6 | Publicera alla resurser, kontrollera utvecklarverktyg i webbläsaren och åtgärda länkar | Kontrollera att Publiceringssökväg innehåller alla nödvändiga bilder, skript och formatmallar för ditt program. Mer information finns i [lägga till en lokal app i Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Använda webbläsarens utvecklingsverktyg (F12 verktyg i Internet Explorer eller Microsoft Edge) och kontrollera för att publicera problem som beskrivs i [programsidan visas inte korrekt](application-proxy-page-appearance-broken-problem.md). <br></br>Granska alternativ för att åtgärda brutna länkar i [länkarna på sidan fungerar inte](application-proxy-page-links-broken-problem.md). |
|7 | Sök efter svarstid för nätverk | Om sidan läses in långsamt, Lär dig mer om olika sätt att minimera Nätverksfördröjningen i [överväganden för att minska svarstiden](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Se ytterligare hjälp med felsökning | Om problemen kvarstår så hittar du ytterligare felsökning artiklar i den [Application Proxy Felsökningsdokumentation](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Nästa steg


* [Publicera program på separata nätverk och platser med hjälp av anslutningsapp-grupper](application-proxy-connector-groups.md)
* [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
* [Felsöka Application Proxy och anslutning](application-proxy-troubleshoot.md)
* [Tyst installation av Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)
