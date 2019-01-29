---
title: Ett program med Application Proxy tar för lång tid att läsa in | Microsoft Docs
description: Felsöka sidan belastningen prestandaproblem med Azure AD-programproxyn
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: ad2190f3bfa9e943258a55a8b8ecdff429d6576e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165786"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ett program med Application Proxy tar för lång tid att läsa in

Den här artikeln hjälper dig att förstå varför ett program för Azure AD Application Proxy kan ta lång tid att läsa in. Den förklarar vad du kan göra för att lösa problemet.

## <a name="overview"></a>Översikt
Även om dina program fungerar, kan det leda till en lång svarstid. Det kan finnas nätverks topologi justeringar som du kan göra att snabbare. En utvärdering av olika topologier finns i den [nätverk överväganden dokumentet](application-proxy-network-topology.md).

Förutom nätverkets topologi finns det inga ytterligare rekommendationer för prestandajustering. Det kanske kommit till ett datacenter som fysiskt ligger närmare som programproxyn service expanderar. Det finns närmare kan underlätta svarstid. En lista över Azure-datacenter, finns i den [svarstid testsidan](http://www.azurespeed.com/Azure/Latency). 

Datacenter med Application Proxy-tjänsten finns med i [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback om Application Proxy data center platser 
Det kan finnas Azure-datacenter som inte ännu innehåller Application Proxy, men leder till en bra svarstid förbättringar för dig. Skicka data datacenterplats till aadapfeedback@microsoft.com. Microsoft använder din feedback för expansion planer.

Microsoft arbetar på att ytterligare funktioner för att förbättra svarstiden. När dessa förbättringar är tillgängliga, kommer att uppdateras i dokumentationen.

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md)
