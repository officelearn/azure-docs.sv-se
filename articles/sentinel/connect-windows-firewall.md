---
title: Ansluta Windows-brandväggsdata till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Windows-brandväggsdata till Azure Sentinel.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588067"
---
# <a name="connect-windows-firewall"></a>Ansluta till Windows-brandvägg



Med Windows-brandväggsappen kan du enkelt ansluta dina Windows-brandväggarsloggar om de är anslutna till din Azure Sentinel-arbetsyta. Med den här anslutningen kan du visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer insikt i organisationens nätverk och förbättrar dina funktioner för säkerhetsdrift. Lösningen samlar in Windows-brandväggshändelser från Windows-datorer där en Log Analytics-agent är installerad. 


> [!NOTE]
> - Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.
> - Om Azure Sentinel och Azure Security Center samlas in på samma arbetsyta behöver du inte aktivera Windows-brandväggen via den här anslutningen. Om du har aktiverat den ändå kommer det inte att orsaka duplicerade data. 

## <a name="enable-the-connector"></a>Aktivera kopplingen 

1. I Azure Sentinel-portalen väljer du **Data-kopplingar** och klickar sedan på **Windows-brandväggspanelen.** 
1.  Om dina Windows-datorer finns i Azure:
    1. Klicka på **Installera agent på den virtuella Azure Windows-datorn**.
    1. I listan **Virtuella datorer** väljer du den Windows-dator som du vill strömma till Azure Sentinel. Kontrollera att det här är en virtuell windows-dator.
    1. Klicka på **Anslut**i fönstret som öppnas för den virtuella datorn.  
    1. Klicka på **Aktivera** i **anslutningsfönstret för Windows-brandväggen.** 

2. Om din Windows-dator inte är en virtuell Azure-dator:
    1. Klicka på **Installera agent på datorer som inte är Azure.**
    1. I fönstret **Direct-agent** väljer du antingen **Hämta Windows-agent (64 bitar)** eller **Hämta Windows-agent (32 bitar)**.
    1. Installera agenten på din Windows-dator. Kopiera **arbetsyte-ID,** **primärnyckel**och **sekundärnyckel** och använd dem när du uppmanas att göra det under installationen.

4. Välj vilka datatyper du vill strömma.
5. Klicka på **Installera lösning**.
6. Om du vill använda det relevanta schemat i Log Analytics för Windows-brandväggen söker du efter **SecurityEvent**.

## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics. 



## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du ansluter Windows-brandväggen till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

