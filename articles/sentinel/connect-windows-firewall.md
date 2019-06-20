---
title: Ansluta Windows-brandväggen data till Azure Sentinel-förhandsgranskning | Microsoft Docs
description: Lär dig hur du ansluter Windows-brandväggen data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/17/2019
ms.author: rkarlin
ms.openlocfilehash: 09e63612d6e0e70b1bb21c23b158f650d4c34080
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190590"
---
# <a name="connect-windows-firewall"></a>Ansluta till Windows-brandvägg

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Connector för Windows-brandväggen kan du enkelt ansluta dina brandväggar loggar Windows om de är anslutna till din Azure Sentinel-arbetsyta. Den här anslutningen kan du visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen. Detta ger dig en överblick över nätverket och förbättrar din säkerhetsfunktioner för åtgärden. Lösningen samlar in händelser för Windows-brandväggen från Windows-datorer där en Log Analytics-agenten är installerad. 


> [!NOTE]
> Data lagras i den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="enable-the-connector"></a>Aktivera anslutningsprogrammet 

1. I Sentinel-Azure-portalen väljer **datakopplingar** och klicka sedan på den **Windows-brandväggen** panelen. 
1.  Om din Windows-datorer finns i Azure:
    1. Klicka på **installera agent i Windows Azure VM**.
    1. I den **virtuella datorer** väljer du den Windows-datorn som du vill spela in Azure Sentinel. Kontrollera att det här är en virtuell Windows-dator.
    1. I fönstret som öppnas för den virtuella datorn klickar du på **Connect**.  
    1. Klicka på **aktivera** i den **Windows-brandväggen connector** fönster. 

2. Om din Windows-dator inte är en Azure-dator:
    1. Klicka på **installera agent i Azure-datorer**.
    1. I den **direktagent** fönstret väljer du antingen **ladda ned Windows agent (64-bitars)** eller **ladda ned Windows agent (32-bitars)** .
    1. Installera agenten på din Windows-dator. Kopiera den **arbetsyte-ID**, **primärnyckel**, och **sekundärnyckel** och använda dem när du uppmanas till detta under installationen.

4. Välj vilka datatyper som du vill spela.
5. Klicka på **installera lösningen för**.
6. Om du vill använda relevanta schemat i Log Analytics för Windows-brandväggen, Sök efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutningen

Det kan ta höjningen tjugonde minut tills loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows-brandväggen till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

