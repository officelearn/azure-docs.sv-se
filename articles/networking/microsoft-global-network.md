---
title: Microsofts globala nätverk – Azure
description: Beskriver hur Microsoft bygger sitt snabba och pålitliga globala nätverk
services: networking
documentationcenter: ''
author: KumudD
manager: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/13/2019
ms.author: kumud
ms.reviewer: ypitsch
ms.openlocfilehash: 10a061163447a60f1c25b386ef28028436284650
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75453063"
---
# <a name="microsoft-global-network"></a>Microsofts globala nätverk

Microsoft äger och driver ett av världens största stamnät. Den här globala och sofistikerade arkitekturen, som spänner över mer än 100 000 miles, förbinder våra datacenter och kunder. 
 
Varje dag ansluter kunder runt om i världen och skickar biljoner förfrågningar till Microsoft Azure, Bing, Dynamics 365, Office 365, Xbox och många andra. Oavsett typ förväntar sig kunderna omedelbar tillförlitlighet och lyhördhet från våra tjänster. 
 
[Microsofts globala nätverk](https://azure.microsoft.com/global-infrastructure/global-network/) (WAN) är en central del av att leverera en fantastisk molnupplevelse. Vårt globala nätverk kopplar samman våra [Microsoft-datacenter](https://azure.microsoft.com/global-infrastructure/) i 54 Azure-regioner och stora nät av kantnoder som är strategiskt placerade runt om i världen och erbjuder både tillgänglighet, kapacitet och flexibilitet för att möta alla behov.

![Microsofts globala nätverk](./media/microsoft-global-network/microsoft-global-wan.png)
 
## <a name="get-the-premium-cloud-network"></a>Skaffa premiummolnnätverket
 
Det är enkelt att välja [bästa möjliga upplevelse](https://www.sdxcentral.com/articles/news/azure-tops-aws-gcp-in-cloud-performance-says-thousandeyes/2018/11/) när du använder Microsofts moln. Från det ögonblick då kundtrafiken kommer in i vårt globala nätverk genom våra strategiskt placerade kantnoder färdas dina data genom optimerade rutter i närheten av ljusets hastighet. Detta säkerställer optimal latens för bästa prestanda. Dessa kant-noder, alla sammankopplade med mer än 3500 unika Internet-partners (kamrater) genom tusentals anslutningar på mer än 145 platser, utgör grunden för vår sammanlänkningsstrategi. 
 
Oavsett om du ansluter från London till Tokyo eller från Washington DC till Los Angeles kvantifieras nätverksprestanda och påverkas av saker som latens, jitter, paketförlust och dataflöde.  På Microsoft föredrar och använder vi direkta sammanlänkningar i motsats till transitlänkar, detta håller svarstrafiken symmetrisk och hjälper till att hålla humle, peering-parter och vägar så korta och enkla som möjligt. 

Om en användare i London till exempel försöker få tillgång till en tjänst i Tokyo går Internettrafiken in i en av våra kanter i London, går över Microsoft WAN via Frankrike, våra Trans-Arabia-vägar mellan Europa och Indien och sedan till Japan där tjänsten finns. Responstrafiken är symmetrisk. Detta kallas ibland [kallpotatis routing](https://en.wikipedia.org/wiki/Hot-potato_and_cold-potato_routing) vilket innebär att trafiken stannar på Microsoft-nätverket så länge som möjligt innan vi lämnar ut den.  
  
Så, betyder det någon och all trafik när du använder Microsoft-tjänster? Ja, all trafik mellan datacenter, inom Microsoft Azure eller mellan Microsoft-tjänster som virtuella datorer, Office 365, Xbox, SQL DBs, Storage och virtuella nätverk dirigeras inom vårt globala nätverk och aldrig över det offentliga Internet, för att säkerställa optimal prestanda och integritet.  
 
Massiva investeringar i fiberkapacitet och mångfald över tunnelbane-, mark- och ubåtsvägar är avgörande för att vi ska kunna hålla konsekvent och hög servicenivå samtidigt som vi underblåser den extrema tillväxten av våra moln- och onlinetjänster. Senaste tillägg till vårt globala nätverk är vår [MAREA](https://www.submarinecablemap.com/#/submarine-cable/marea) undervattenskabel, branschens första Open Line System (OLS) över undervattens, mellan Bilbao, Spanien och Virginia Beach, Virginia, USA, samt [AEC](https://www.submarinecablemap.com/#/submarine-cable/aeconnect-1) mellan New York, USA och Dublin, Irland och [New Cross Pacific (NCP)](https://www.submarinecablemap.com/#/submarine-cable/new-cross-pacific-ncp-cable-system) mellan Tokyo, Japan och Portland, Oregon, USA. 
 

## <a name="our-network-is-your-network"></a>Vårt nätverk är ditt nätverk

Vi har lagt två decenniers erfarenhet, tillsammans med massiva investeringar i nätverket, för att säkerställa optimal prestanda hela tiden. Företag kan dra full nytta av våra nätverkstillgångar och bygga avancerade överlagringsarkitekturer ovanpå. 
 
Microsoft Azure erbjuder den rikaste portföljen av tjänster och funktioner, så att kunderna snabbt och enkelt kan skapa, expandera och uppfylla nätverkskraven var som helst. Vår familj av anslutningstjänster sträcker sig över virtuella nätverks peering mellan regioner, hybrid- och i-molnet point-to-site- och site-to-site-arkitekturer samt globala IP-transitscenarier.  För företag som vill ansluta sitt eget datacenter eller nätverk till Azure, eller kunder med massiva datainmatnings- eller transitbehov, ger [ExpressRoute](../expressroute/expressroute-introduction.md)och [ExpressRoute Direct](../expressroute/expressroute-erdirect-about.md) alternativ upp till 100 Gbit/s bandbredd, direkt till Microsofts globala nätverk på peering-platser över hela världen.  
 
[ExpressRoute Global Reach](../expressroute/expressroute-global-reach.md) är utformad för att komplettera din tjänsteleverantörs WAN-implementering och ansluta dina lokala webbplatser över hela världen. Om du kör en global åtgärd kan du använda ExpressRoute Global Reach tillsammans med dina önskade och lokala tjänsteleverantörer för att ansluta alla dina globala webbplatser med hjälp av Microsofts globala nätverk. Utöka ditt nya nätverk i molnet (WAN) till att omfatta ett stort antal filial-platser kan åstadkommas via Azure Virtual WAN, vilket ger möjlighet att sömlöst ansluta dina grenar till Microsofts globala nätverk med SDWAN & VPN-enheter (det vill ha, Customer Premises Equipment eller CPE) med inbyggd användarvänlighet och automatiserad anslutning och konfigurationshantering. 
 
[Global VNet-peering](../virtual-network/virtual-network-peering-overview.md) gör det möjligt för kunder att ansluta två eller flera virtuella Azure-nätverk mellan regioner sömlöst. När de virtuella nätverken har peerats visas de som ett. Trafiken mellan virtuella datorer i peered virtuella nätverk dirigeras genom Microsofts stamnät infrastruktur, ungefär som trafiken dirigeras mellan virtuella datorer i samma virtuella nätverk - endast via privata IP-adresser. 
 

## <a name="well-managed-using-software-defined-innovation"></a>Välskött med hjälp av programvarudefinierad innovation

Microsoft driver ett av de ledande molnen i världen och har fått stor insikt och erfarenhet av att bygga och hantera högpresterande global infrastruktur.  
 
Vi följer en robust uppsättning operativa principer: 
 
- Använd bästa av rasens växelmaskinvara över de olika nivåerna i nätverket.  
- Distribuera nya funktioner med noll påverkan på slutanvändare.  
- Rulla ut uppdateringar på ett säkert och tillförlitligt sätt i hela flottan, så fort som möjligt. Timmar istället för veckor.  
- Använd djup telemetri i molnskala och helt automatiserad felreducering.  
- Använd enhetlig och programvarudefinierad nätverksteknik för att styra alla maskinvaruelement i nätverket.  Eliminera dubbelarbete och minska fel. 
 
Dessa principer gäller för alla lager i nätverket: från värdens nätverksgränssnitt, kopplingsplattform, nätverksfunktioner i datacentret som belastningsutjämnare, hela vägen upp till WAN med vår trafiktekniska plattform och våra optiska nätverk.  
 
Den exponentiella tillväxten av Azure och dess nätverk har nått en punkt där vi så småningom insåg att mänsklig intuition inte längre kunde åberopas för att hantera den globala nätverksverksamheten. För att uppfylla behovet av att validera långsiktiga, medellånga och kortsiktiga förändringar i nätverket utvecklade vi en plattform för att spegla och efterlikna vårt produktionsnätverk syntetiskt. Möjligheten att skapa speglade miljöer och köra miljontals simuleringar, gör det möjligt för oss att testa förändringar av programvara och hårdvara och deras inverkan, innan de förbinder sig till vår produktionsplattform och nätverk. 

## <a name="next-steps"></a>Nästa steg
- [Läs mer om nätverkstjänsterna i Azure](https://azure.microsoft.com/product-categories/networking/)
