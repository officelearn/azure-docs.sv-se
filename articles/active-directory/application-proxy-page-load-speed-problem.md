---
title: Ett program med Application Proxy tar för lång tid att läsa in | Microsoft Docs
description: Felsökning av problem med sidan belastningen prestanda med Azure AD Application Proxy
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 26acc620184b51719a2ee55b75bd01966d225b8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330935"
---
# <a name="an-application-proxy-application-takes-too-long-to-load"></a>Ett program med Application Proxy tar för lång tid att läsa in

Den här artikeln hjälper dig att förstå varför ett program för Azure AD Application Proxy kan ta lång tid att läsa in. Här beskrivs också vad du kan göra för att lösa problemet.

## <a name="overview"></a>Översikt
Även om dina program fungerar, kan det leda till en lång fördröjning. Det kan finnas nätverks topologi justeringar som du kan göra att snabbare. En utvärdering av olika topologier finns i [nätverk överväganden dokumentet](manage-apps/application-proxy-network-topology.md).

Förutom nätverkstopologi finns det inga ytterligare rekommendationer för prestandajustering. Som Application Proxy service expanderar komma den till ett datacenter som fysiskt ligger närmare. Det finns närmare kan hjälpa dig med svarstid. En lista över Azure-datacenter, finns det [svarstid testsida](http://www.azurespeed.com/Azure/Latency). 

Datacenter med Application Proxy-tjänsten finns med i [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/). 

## <a name="feedback-on-application-proxy-data-center-locations"></a>Feedback om Application Proxy data center platser 
Det kan finnas Azure-datacenter som inte ännu inkludera Application Proxy, men skulle leda till en bra svarstid förbättringar för dig. Data center plats för att skicka aadapfeedback@microsoft.com. Microsoft använder din feedback för expansion planer.

Microsoft arbetar på ytterligare funktioner för att förbättra svarstiden. När dessa förbättringar finns uppdateras i dokumentationen.

## <a name="next-steps"></a>Nästa steg
[Arbeta med befintliga lokala proxyservrar](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md)
