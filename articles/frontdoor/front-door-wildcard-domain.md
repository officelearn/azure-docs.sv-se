---
title: Ytterdörren i Azure – stöd för jokerteckendomäner
description: Den här artikeln hjälper dig att förstå hur Azure Front Door stöder mappning och hantering av jokerteckendomäner i listan över anpassade domäner.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768314"
---
# <a name="wildcard-domains"></a>Jokerteckendomäner

Förutom apex-domäner och underdomäner kan du mappa ett jokerteckendomännamn till din lista över frontend-värdar eller anpassade domäner i din Azure Front Door-profil. Med jokerteckendomäner i azure front door-konfigurationen förenklar trafikroutningsbeteendet för flera underdomäner för ett API, program eller en webbplats från samma routningsregel. Du behöver inte ändra konfigurationen för att lägga till eller ange varje underdomän separat. Som ett exempel kan du definiera `customer1.contoso.com` `customer2.contoso.com`routningen `customerN.contoso.com` för , och genom att använda `*.contoso.com`samma routningsregel och lägga till jokerteckendomänen .

Viktiga scenarier som förbättras med stöd för jokerteckendomäner är:

- Du behöver inte gå in på varje underdomän i din Azure Front Door-profil och sedan aktivera HTTPS för att binda ett certifikat för varje underdomän.
- Du behöver inte längre ändra din produktion Azure Front Door konfiguration om ett program lägger till en ny underdomän. Tidigare var du tvungen att lägga till underdomänen, binda ett certifikat till den, bifoga en WAF-princip (Web Application Firewall) och sedan lägga till domänen i olika routningsregler.

> [!NOTE]
> För närvarande stöds jokerteckendomäner endast via API, PowerShell och Azure CLI. Stöd för att lägga till och hantera jokerteckendomäner i Azure-portalen är inte tillgängligt.

## <a name="adding-wildcard-domains"></a>Lägga till jokerteckendomäner

Du kan lägga till en jokerteckendomän under avsnittet för frontend-värdar eller domäner. I likhet med underdomäner verifierar Azure Front Door att det finns CNAME-postmappning för din jokerteckendomän. Den här DNS-mappningen kan vara `*.contoso.com` en `contoso.azurefd.net`direkt CNAME-postmappning som mappas till . Eller så kan du använda afdverify tillfällig mappning. `afdverify.contoso.com` Mappas till `afdverify.contoso.azurefd.net` exempel för att validera CNAME-postmappningen för jokertecknet.

> [!NOTE]
> Azure DNS stöder poster med jokertecken.

Du kan lägga till så många underdomäner på en nivå som jokertecken i frontend-värdar, upp till gränsen för frontend-värdarna. Den här funktionen kan krävas för:

- Definiera en annan väg för en underdomän än resten av domänerna (från jokerteckendomänen).

- Har en annan WAF-princip för en specifik underdomän. Till exempel `*.contoso.com` tillåter `foo.contoso.com` att lägga till utan att återigen bevisa domän ägande. Men det tillåter `foo.bar.contoso.com` inte eftersom det inte är en enda `*.contoso.com`nivå underdomän av . Om `foo.bar.contoso.com` du vill lägga `*.bar.contosonews.com` till utan ytterligare validering av domänägarskap måste du läggas till.

Du kan lägga till jokerteckendomäner och deras underdomäner med vissa begränsningar:

- Om en jokerteckendomän läggs till i en Azure Front Door-profil:
  - Jokerteckendomänen kan inte läggas till i någon annan Azure Front Door-profil.
  - Underdomäner på första nivån för jokerteckendomänen kan inte läggas till i en annan Azure Front Door-profil eller en Azure Content Delivery Network-profil.
- Om en underdomän till en jokerteckendomän läggs till i en Azure Front Door-profil eller Azure Content Delivery Network-profil kan jokerteckendomänen inte läggas till i andra Azure Front Door-profiler.
- Om två profiler (Azure Front Door eller Azure Content Delivery Network) har olika underdomäner i en rotdomän, kan jokerteckendomäner inte läggas till någon av profilerna.

## <a name="certificate-binding"></a>Certifikatbindning

För att acceptera HTTPS-trafik på jokerteckendomänen måste du aktivera HTTPS på jokerteckendomänen. Certifikatbindningen för en jokerteckendomän kräver ett jokerteckencertifikat. Det vill säga, ämnet namnet på certifikatet bör också ha jokertecken domän.

> [!NOTE]
> För närvarande är det bara att använda ditt eget anpassade SSL-certifikatalternativ för att aktivera HTTPS för jokerteckendomäner. Azure Front Door hanterade certifikat kan inte användas för jokerteckendomäner.

Du kan välja att använda samma jokerteckencertifikat från Azure Key Vault eller från Azure Front Door-hanterade certifikat för underdomäner.

Om en underdomän läggs till för en jokerteckendomän som redan har ett certifikat kopplat till det, kan inte HTTPS för underdomänen inaktiveras. Underdomänen använder certifikatbindningen för jokerteckendomänen, såvida inte ett annat Key Vault- eller Azure Front Door-hanterat certifikat åsidosätter det.

## <a name="waf-policies"></a>WAF-principer

WAF-principer kan kopplas till jokerteckendomäner, liknande andra domäner. En annan WAF-princip kan tillämpas på en underdomän till en jokerteckendomän. För underdomänerna måste du ange den WAF-princip som ska användas även om det är samma princip som jokerteckendomänen. Underdomäner ärver *inte* automatiskt WAF-principen från jokerteckendomänen.

Om du inte vill att en WAF-princip ska köras för en underdomän kan du skapa en tom WAF-princip utan hanterade eller anpassade regeluppsättningar.

## <a name="routing-rules"></a>Dirigeringsregler

När du konfigurerar en routningsregel kan du välja en jokerteckendomän som frontend-värd. Du kan också ha olika flödesbeteende för jokerteckendomäner och underdomäner. Som beskrivs i [Hur Azure Front Door gör ruttmatchning](front-door-route-matching.md)väljs den mest specifika matchningen för domänen över olika routningsregler vid körning.

> [!IMPORTANT]
> Du måste ha matchande sökvägsmönster över dina routningsregler, annars ser dina klienter fel. Du har till exempel två routningsregler`*.foo.com/*` som Route 1 (mappad till backend-pool A) och Route 2 (mappad`bar.foo.com/somePath/*` till backend-pool B). Sedan kommer en `bar.foo.com/anotherPath/*`begäran för . Azure Front Door väljer Route 2 baserat på en mer specifik domänmatchning, bara för att hitta några matchande sökvägsmönster över rutterna.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en Azure Front Door-profil](quickstart-create-front-door.md).
- Lär dig hur du [lägger till en anpassad domän på Azure Front Door](front-door-custom-domain.md).
- Lär dig hur du [aktiverar HTTPS på en anpassad domän](front-door-custom-domain-https.md).
