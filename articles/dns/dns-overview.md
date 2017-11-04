---
title: "Översikt över Azure DNS | Microsoft Docs"
description: "Översikt över DNS-värdtjänsten på Microsoft Azure. Värd för din domän i Microsoft Azure."
services: dns
documentationcenter: na
author: KumudD
manager: timlt
editor: 
ms.assetid: 68747a0d-b358-4b8e-b5e2-e2570745ec3f
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: kumud
ms.openlocfilehash: 890c00f3349abd52294e92d27f1b42ab38fe287a
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="azure-dns-overview"></a>Översikt över Azure DNS

Domain Name System- eller DNS, ansvarar för att översätta (eller lösa) en webbplats eller tjänst namn till dess IP-adress. Azure DNS är värdtjänsten för DNS-domäner som tillhandahåller namnmatchning med hjälp av Microsoft Azure-infrastrukturen. Genom att använda Azure som värd för dina domäner kan du hantera dina DNS-poster med samma autentiseringsuppgifter, API:er, verktyg och fakturering som för dina andra Azure-tjänster. Azure DNS stöder nu också privata DNS-domäner. Mer information finns i [med hjälp av Azure DNS för privata domäner](private-dns-overview.md).

![Översikt över DNS](./media/dns-overview/scenario.png)

## <a name="features"></a>Funktioner

* **Tillförlitlighet och prestanda** -DNS-domäner i Azure DNS finns på Azures globalt nätverk av DNS-namnservrar. Azure DNS använder Anycast nätverk så att varje DNS-frågan besvaras närmaste tillgängliga DNS-servern. Detta ger både snabb prestanda och hög tillgänglighet för din domän.

* **Sömlös integration** -Azure DNS-tjänsten kan användas för att hantera DNS-posterna för din Azure-tjänster och kan användas för att tillhandahålla DNS för din externa resurser. Azure DNS är integrerat i Azure-portalen och använder samma autentiseringsuppgifter, fakturering och support-kontrakt som andra Azure-tjänster.

* **Säkerhet** -Azure DNS-tjänsten är baserad på Azure Resource Manager. Därför fördelar från Resource Manager-funktioner, till exempel rollbaserad åtkomstkontroll, granskningsloggar och låsa resurs. Dina domäner och poster kan hanteras via Azure-portalen, Azure PowerShell-cmdlets och plattformsoberoende Azure CLI. Program som kräver automatisk DNS-hantering kan integreras med via REST-API och SDK-tjänsten.

Azure DNS stöder för närvarande inte köp av domännamn. Om du vill köpa domäner måste du använda en tredje parts domännamnsregistratorn. Registratorn debiterar vanligtvis en liten årlig avgift. Domänerna kan finnas i Azure DNS för för hantering av DNS-poster. Se [delegera en domän till Azure DNS](dns-domain-delegation.md) mer information.

## <a name="pricing"></a>Prissättning

DNS-fakturering baseras på antalet DNS-zoner i Azure och av antal DNS-frågor. Mer information om priser finns [priser för Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

Vanliga frågor och svar om Azure DNS, finns det [Azure DNS vanliga frågor om](dns-faq.md).

## <a name="next-steps"></a>Nästa steg

Lär dig mer om DNS-zoner och poster genom att besöka: [DNS-zoner och innehåller en översikt över](dns-zones-records.md).

Lär dig hur du [skapa en DNS-zon](./dns-getstarted-create-dnszone-portal.md) i Azure DNS.

Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.

