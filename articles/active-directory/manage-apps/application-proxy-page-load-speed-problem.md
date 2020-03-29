---
title: Ett programproxy-program tar för lång tid att läsa in | Microsoft-dokument
description: Felsöka prestandaproblem för sidinläsning med Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac1182d719d7c90129115e1fadf94f4f86a28e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65782651"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ett programproxyprogram tar för lång tid att läsa in

Den här artikeln hjälper dig att förstå varför ett Azure AD Application Proxy-program kan ta lång tid att läsa in. Det förklarar också vad du kan göra för att lösa problemet.

## <a name="overview"></a>Översikt
Även om dina program fungerar kan de uppleva en lång svarstid. Det kan finnas nätverk topologi tweaks som du kan göra för att förbättra hastigheten. En utvärdering av olika topologier finns i [dokumentet om nätverksöverväganden](application-proxy-network-topology.md).

Förutom nätverkstopologi finns det för närvarande inga ytterligare rekommendationer för prestandajustering. När tjänsten Application Proxy expanderar kan den komma till ett datacenter som är fysiskt närmare. Ju närmare närheten kan hjälpa till med svarstid. En lista över Azure-datacenter finns på [sidan svarstidstest](http://www.azurespeed.com/Azure/Latency). 

Datacenter med tjänsten Application Proxy finns med [testverktyget för anslutningsportar](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback på datacenterplatser för programproxy 
Det kan finnas Azure-datacenter som ännu inte innehåller Application Proxy, men som skulle leda till en stor latensförbättring för dig. Skicka datacenterplatsen till aadapfeedback@microsoft.com. Microsoft använder din feedback för expansionsplaner.

Microsoft arbetar med ytterligare funktioner för att förbättra svarstiden. Så snart dessa förbättringar är tillgängliga kommer dokumentationen att uppdateras.

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
