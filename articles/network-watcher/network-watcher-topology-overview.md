---
title: "Introduktion till topologi i Azure-Nätverksbevakaren | Microsoft Docs"
description: "Den här sidan innehåller en översikt över funktionerna Nätverksbevakaren topologi"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: d5cb5ba431eeae1956a9dbf1d48561c66faef9a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-topology-in-azure-network-watcher"></a>Introduktion till topologi i Azure Nätverksbevakaren

Topologi returnerar ett diagram över nätverksresurser i ett virtuellt nätverk. Diagrammet visar sambandet mellan resurser för att representera nätverksanslutning för slutpunkt till slutpunkt.

![Översikt över labbtopologi][1]

I portalen returnerar topologi resursobjekt på en per virtuellt nätverk basis. Relationerna illustreras med linjer mellan resurser resurser utanför Nätverksbevakaren region, även om resursen gruppen inte visas. Resurser som returneras i vyn portal är en delmängd av nätverkskomponenter som är visas i diagram registreringen. Se en fullständig lista över nätverksresurser som du kan använda [PowerShell](network-watcher-topology-powershell.md) eller [REST](network-watcher-topology-rest.md)

> [!NOTE]
> En instans av Nätverksbevakaren krävs för varje region som du vill köra topologi på.

Som resurser returneras anslutningen mellan dem modelleras under två relationer.

- **Inneslutning** -exempel: virtuellt nätverk innehåller ett undernät som innehåller ett nätverkskort
- **Associerade** -exempel: ett nätverkskort är kopplat till en virtuell dator

### <a name="next-steps"></a>Nästa steg

Lär dig hur du använder PowerShell för att hämta topologiska vyn genom att besöka [Nätverksbevakaren topologi med PowerShell](network-watcher-topology-powershell.md)

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png
