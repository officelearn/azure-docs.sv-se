---
title: Ansluta DNS-data i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig hur du ansluter DNS-data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2019
ms.author: rkarlin
ms.openlocfilehash: e34db2bdc78eb846cf4885b1ef083fd3b21e21b5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792059"
---
# <a name="connect-your-domain-name-server"></a>Connect your domain name server

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan ansluta alla Server DNS (Domain Name) som körs på Windows till Azure Sentinel. Detta görs genom att installera en agent på den DNS-datorn. Med hjälp av DNS-loggar, du får säkerhet, prestanda och operations-relaterade insikter om DNS-infrastrukturen i organisationen genom att samla in, analysera, och korrelera analytiska och granskningsloggar och andra relaterade data från DNS-servrar.

När du aktiverar DNS-log-anslutning kan du:
- Identifiera klienter som försöker matcha skadliga domännamn
- Identifiera inaktuella poster
- Identifiera ofta efterfrågade domännamn och pratsam DNS-klienter
- Visa belastning för begäranden på DNS-servrar
- Visa dynamiska DNS-registreringsfel

## <a name="how-it-works"></a>Hur det fungerar

DNS-anslutning sker genom att installera en agent på den DNS-datorn. Agenten tar emot händelser från DNS och skickar dem till Log Analytics.

## <a name="connect-your-dns-appliance"></a>Ansluta din DNS-installation

1. I Sentinel-Azure-portalen väljer **datakopplingar** och välj den **DNS** panelen.
1. Om din DNS-datorer finns i Azure:
    1. Klicka på **hämta och installera agenten för Windows-datorer**.
    1. I den **virtuella datorer** väljer du den DNS-datorn som du vill spela in Azure Sentinel. Kontrollera att det här är en virtuell Windows-dator.
    1. I fönstret som öppnas för den virtuella datorn klickar du på **Connect**.  
    1. Klicka på **aktivera** i den **DNS connector** fönster. 

2. Om din DNS-dator inte är en Azure-dator:
    1. Klicka på **hämta och installera agenten för Windows Azure-datorer**.
    1. I den **direktagent** fönstret väljer du antingen **ladda ned Windows agent (64-bitars)** eller **ladda ned Windows agent (32-bitars)**.
    1. Installera agenten på DNS-datorn. Kopiera den **arbetsyte-ID**, **primärnyckel**, och **sekundärnyckel** och använda dem när du uppmanas till detta under installationen.

3. Om du vill använda relevanta schemat i Log Analytics för DNS-loggar, Sök efter **DnsEvents**.

## <a name="validate"></a>Verifiera 

Sök efter schemat i Log Analytics, **DnsEvents** och se till att det finns händelser.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter en lokal DNS-tillämpningar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).
