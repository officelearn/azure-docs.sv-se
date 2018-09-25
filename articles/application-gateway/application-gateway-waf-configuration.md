---
title: Web application firewall begäran storleksgränser och undantagslistor i Azure Application Gateway – Azure-portalen | Microsoft Docs
description: Den här artikeln innehåller information om storleksgränser för web application firewall begäran och undantag visar konfigurationen i Application Gateway med Azure-portalen.
documentationcenter: na
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: ''
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: victorh
ms.openlocfilehash: 995e003422d5a94fe57174dc9733c870e4e003aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46965525"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists-public-preview"></a>Storleksgränser för Web application firewall begäran- och uteslutningsprinciperna visar (offentlig förhandsversion)

Azure Application Gateway brandväggen för webbaserade program (WAF) ger skydd för webbprogram. Den här artikeln beskriver storleksgränser för WAF-begäran och undantag visar konfigurationen.

> [!IMPORTANT]
> Konfiguration av WAF storleksgränser för begäran och undantagslistor är för närvarande i offentlig förhandsversion. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="waf-request-size-limits"></a>Storleksgränser för WAF-begäran
Brandvägg för webbaserade program kan du konfigurera storleksbegränsningar för begäran i lägre och övre gränser. Följande två storlek gränser konfigurationer finns:
- Fältet tillåtna brödtext storleken anges i KB-artiklar och kontroller övergripande storleksgränsen för begäran exkludera en fil laddas upp. Det här fältet kan variera mellan 1 KB lägsta maximala värdet för 128 KB. Standardvärdet för textstorleken för begäran är 128 KB.
- Fältet Spara uppladdning gränsen anges i MB och styr den maximalt tillåtna storleken för filöverföring. Det här fältet kan ha ett minsta värde på 1 MB och högst 500 MB. Standardvärdet för överföring filgränsen är 100 MB.

WAF erbjuder även en konfigurerbar ratten om du vill aktivera begäran brödtext inspektion eller inaktivera. Begäran brödtext granskning är aktiverat som standard. Om begäran brödtext-kontroll är inaktiverad, utvärderar WAF inte innehållet i meddelandetexten för HTTP. I sådana fall kan fortsätter WAF WAF-regler på rubriker, cookies och URI: N. Om begäran brödtext-kontroll är inaktiverad fältet för tillåtna brödtext storlek är inte tillämplig och kan inte anges. Om du inaktiverar begäran brödtext inspektion kan meddelanden större än 128 KB skickas till WAF. Meddelandetexten är dock inte kontrolleras sårbarheter.

## <a name="waf-exclusion-lists"></a>WAF-undantagslistor

WAF-undantagslistor Tillåt användare att utelämna vissa begärandeattribut från en WAF-utvärdering. Ett vanligt exempel är Active Directory infogas token som används för autentisering eller lösenorden. Attribut är felbenägna innehåller specialtecken som kan utlösa en falsk positiv identifiering från WAF-regler. När ett attribut har lagts till i undantagslistan för WAF kan tas inte hänsyn av någon konfigurerad och aktiv WAF-regel. Undantagslistor är globala omfång.
Du kan lägga till begärandehuvuden, begäran cookies eller begäran fråga strängargument undantagslistor för WAF. Du kan ange en exakt begär sidhuvud, cookie eller fråga strängmatchning för attributet eller kan du ange delmatchningar. Här följer stöds matchning kriterier operatorer: 
- **Är lika med**: den här operatorn används efter en exakt matchning. Till exempel för att välja rubrik som heter **bearerToken**”** Använd är lika med operatorn med väljaren som **bearerToken**. 
- **Börjar med**: den här operatorn matchar alla fält som börjar med angivna väljare värde. 
- **Slutar med**: den här operatorn matchar alla fält i begäran som slutar med angivna väljare värde. 
- **Innehåller**: den här operatorn matchar alla fält i begäran som innehåller angivna väljare värde.

I samtliga fall matchar är skiftlägeskänsligt och reguljära uttryck är inte tillåtna som väljare.

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat inställningarna WAF du lära dig hur du visar dina WAF-loggar. Mer information finns i [Application Gateway-diagnostik](application-gateway-diagnostics.md#diagnostic-logging).