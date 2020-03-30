---
title: Ytterdörren i Azure – stöd för jokerteckendomäner
description: Den här artikeln hjälper dig att förstå hur Azure Front Door stöder mappning och hantering av jokerteckendomäner i listan över anpassade domäner
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537445"
---
# <a name="wildcard-domains"></a>Jokerteckendomäner

Förutom apex-domäner och underdomäner kan du också mappa ett jokerteckendomännamn till listan över frontend-värdar eller anpassade domäner i din front door-profil. Med jokerteckendomäner i frontdörrens konfiguration förenklar trafikroutningsbeteendet för flera underdomäner för ett API, ett program eller en webbplats från samma routningsregel utan att behöva ändra konfigurationen för att lägga till och/eller ange varje underdomän separat. Som ett exempel kan du definiera `customer1.contoso.com` `customer2.contoso.com`routningen `customerN.contoso.com` för , och använda samma routningsregel genom att lägga till en jokerteckendomän `*.contoso.com`.

Några av de viktigaste scenarierna som löses med stöd för jokerteckendomäner är:

- Behövs inte längre för att gå in på varje underdomän på ytterdörren och sedan aktivera HTTPS för att binda ett certifikat för varje underdomän.
- Om ett program lägger till en ny underdomän behöver du inte längre ändra konfigurationen av front door-produktionen. Annars, tidigare det krävs att lägga till underdomänen, binda ett certifikat till den, bifoga ett webbprogram brandvägg (WAF) princip, lägga till domänen till olika routningsregler.

> [!NOTE]
> För närvarande stöds jokerteckendomäner endast via API, PowerShell och CLI. Stöd för att lägga till hantering av jokerteckendomäner via Azure-portalen är inte tillgängligt.

## <a name="adding-wildcard-domains"></a>Lägga till jokerteckendomäner

Du kan gå ombord på en jokerteckendomän under avsnittet Frontend Hosts eller Domains. I likhet med underdomäner validerar Ytterdörren att det finns en CNAME-mappning även för jokerteckendomänen. Den här DNS-mappningen kan `*.contoso.com` vara en `contoso.azurefd.net` direkt CNAME-mappning som `afdverify.contoso.com` mappas till eller via den tillfälliga mappningen för afdverify som mappas för att `afdverify.contoso.azurefd.net` validera CNAME-mappning för jokertecken (Azure DNS stöder jokerteckenposter).

Du kan också lägga till så många underdomäner på en nivå i jokerteckendomänen i frontend-värdar om de inte träffar max. gränsen för frontend värdar. Den här funktionen kan krävas för att definiera en annan väg för en underdomän än resten av domänerna (från jokerteckendomänen) eller med en annan WAF-princip för en viss underdomän. Så, `*.contoso.com` kommer `foo.contoso.com` att tillåta att lägga till `foo.bar.contoso.com` utan att återigen bevisa domän ägande `*.contoso.com`men inte eftersom det inte är en enda nivå underdomän av . Om `foo.bar.contoso.com` du vill lägga `*.bar.contosonews.com` till utan ytterligare validering av domänägarskap måste du läggas till.

### <a name="limitations"></a>Begränsningar

1. Om en jokerteckendomän läggs till i en viss frontdörrprofil kan inte samma sak läggas till i någon annan frontdörrsprofil. 
2. Om en jokerteckendomän läggs till i en viss frontdörrprofil kan inga underdomäner till den jokerteckendomänen läggas till i andra ytterdörren eller en Azure CDN från Microsoft-profil
3. Om en underdomän till en jokerteckendomän läggs till antingen i en frontdörrprofil eller en Azure CDN från Microsoft-profil kan jokertecknet inte läggas till i någon annan frontdörrsprofil. 
4. Om två profiler (Ytterdörren eller Azure CDN från Microsoft) har olika underdomäner i en rotdomän, kan jokerteckendomäner inte läggas till på någon av profilerna.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Certifikatbindning för jokerteckendomäner och dess underdomäner

För att acceptera HTTPS-trafik på jokerteckendomänen måste du aktivera HTTPS på jokerteckendomänen. Certifikatbindningen för jokerteckendomänen kräver ett jokerteckencertifikat, det vill säga certifikatets ämnesnamn bör också ha jokerteckendomänen.

> [!NOTE]
> För närvarande är det bara att använda ditt eget anpassade SSL-certifikatalternativ för att aktivera HTTPS för jokerteckendomäner. Klientdörrhanterade certifikat kan inte användas för jokerteckendomäner. 

Du kan välja att använda samma jokerteckencertifikat från key vault för underdomänerna, eller så stöds också användning av klientdörrhanterade certifikat för underdomäner.
Om en underdomän läggs till för en jokerteckendomän och jokerteckendomänen redan har ett certifikat associerat, kan inte HTTPS för den här underdomänen inaktiveras. Underdomänen använder som standard jokerteckendomänens certifikatbindning, såvida den inte åsidosätts av ett annat Key Vault-certifikat eller certifikat för frontdörrhanterad.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Brandvägg för webbprogram för jokerteckendomäner och dess underdomäner

WAF-principer kan kopplas till en jokerteckendomän som liknar andra domäner. En annan WAF-princip kan tillämpas på en underdomän till en jokerteckendomän. För underdomänerna måste du uttryckligen ange waf-principen som ska användas och även om det är samma princip som jokerteckendomänen. Underdomäner ärver **inte** WAF-principen automatiskt från jokerteckendomänen.

Om du har ett scenario där du inte vill att WAF ska köras för en underdomän kan du skapa en tom WAF-princip utan hanterade eller anpassade regeluppsättningar.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Routningsregler för jokerteckendomäner och dess underdomäner

När du konfigurerar en routningsregel kan du välja en jokerteckendomän som frontend-värd. Du kan också ha olika flödesbeteende för jokerteckendomäner jämfört med underdomäner. Som beskrivs i [hur Ytterdörren gör ruttmatchning](front-door-route-matching.md), den mest specifika matchningen för domänen över olika routningsregler kommer att väljas vid körning.

> [!WARNING]
> Om du har två routningsregler `*.foo.com/*` som **Route 1:** mappas till `bar.foo.com/somePath/*` Backend Pool A och Route `bar.foo.com/anotherPath/*` **2:** mappas till Backend Pool B och om en begäran anländer för, kommer dina klienter att se fel som Ytterdörren inte hittar någon matchning över båda rutterna. Detta beror på att enligt vår [ruttmatchningsalgoritm](front-door-route-matching.md)väljer Ytterdörren Route 2 baserat på mer specifik domänmatchning, men bara för att upptäcka att det inte finns några matchande banmönster. 


## <a name="next-steps"></a>Nästa steg

- Läs hur du [skapar en Front Door](quickstart-create-front-door.md).
- Läs om hur du [lägger till en anpassad domän på Ytterdörren](front-door-custom-domain.md).
- Lär dig hur du [aktiverar HTTPS på en anpassad domän](front-door-custom-domain-https.md).
