---
title: Ett program med Application Proxy tar för lång tid att läsa in | Microsoft Docs
description: Felsöka sidan belastningen prestandaproblem med Azure AD-programproxyn
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4110ceddb55de1990d85597f8c1a9618743f946
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60292732"
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
