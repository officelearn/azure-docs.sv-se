---
title: Anslut data från Windows-brandväggen till Azure Sentinel | Microsoft Docs
description: Lär dig hur du ansluter Windows-brandväggens data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0e41f896-8521-49b8-a244-71c78d469bc3
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 5d2f68261143c3fc5bbcda0b739af17251eeee63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588067"
---
# <a name="connect-windows-firewall"></a>Ansluta till Windows-brandvägg



Med anslutnings programmet för Windows-brandväggen kan du enkelt ansluta dina Windows-brandväggens loggar, om de är anslutna till din Azure Sentinel-arbetsyta. Med den här anslutningen kan du Visa instrument paneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig bättre insikt i din organisations nätverk och förbättrar dina säkerhets åtgärder. Lösningen samlar in händelser i Windows-brandväggen från Windows-datorer där en Log Analytics-agent är installerad. 


> [!NOTE]
> - Data lagras på den geografiska platsen för den arbets yta där du kör Azure Sentinel.
> - Om Azure Sentinel och Azure Security Center samlas in i samma arbets yta behöver du inte aktivera Windows brand Väggs lösningen via den här anslutningen. Om du har aktiverat den ändå kommer det inte att orsaka dubbletter av data. 

## <a name="enable-the-connector"></a>Aktivera anslutningen 

1. I Azure Sentinel-portalen väljer du **data kopplingar** och klickar sedan på panelen **Windows-brandväggen** . 
1.  Om dina Windows-datorer finns i Azure:
    1. Klicka på **Installera agent på virtuell Azure Windows-dator**.
    1. I listan med **virtuella datorer** väljer du den Windows-dator som du vill strömma till Azure Sentinel. Se till att det här är en virtuell Windows-dator.
    1. I fönstret som öppnas för den virtuella datorn klickar du på **Anslut**.  
    1. Klicka på **Aktivera** i fönstret **anslutning till Windows-brandväggen** . 

2. Om din Windows-dator inte är en virtuell Azure-dator:
    1. Klicka på **Installera agent på datorer som inte är Azure-datorer**.
    1. I fönstret **direkt agent** väljer du antingen **Ladda ned Windows-agent (64 bitar)** eller **ladda ned Windows agent (32 bitar)**.
    1. Installera agenten på Windows-datorn. Kopiera **arbetsyte-ID**, **primär nyckel**och **sekundär nyckel** och Använd dem när du uppmanas att göra det under installationen.

4. Välj vilka data typer du vill strömma.
5. Klicka på **installera lösning**.
6. Om du vill använda det relevanta schemat i Log Analytics för Windows-brandväggen söker du efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta upp till 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Windows-brandväggen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

