---
title: Så här stänger du av en Microsoft Azure FXT Edge Filer-enhet
description: Procedurer för start och säker avstängning av en Azure FXT Edge Filer-nod
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 91445c4219abaa67385105f52515f52c87e2cf06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72255999"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Så här stänger du av Azure FXT Edge Filer-maskinvara på ett säkert sätt

Även om du kan använda den fysiska strömbrytaren för att slå på en enskild nod, bör du inte använda den för att stänga av enheten under normala omständigheter.

När en Azure FXT Edge Filer-nod används som en del av ett kluster bör du använda klusterkontrollpanelens programvara för att stänga av maskinvaran. 

> [!NOTE] 
> För att undvika eventuell dataförlust eller skada använder du alltid Kontrollpanelens programvara för att stänga av en Azure FXT Edge Filer. Använd inte den fysiska strömbrytaren för avstängning om du inte uppmanas att göra det av Microsofts kundtjänst och support.
> 
> I en elektrisk nödsituation ska du koppla bort nätsladdar eller använda datacentrets mekanism för elkoppling.

## <a name="shut-down-a-node-from-the-control-panel"></a>Stänga av en nod från Kontrollpanelen

Följ dessa instruktioner för att på ett säkert sätt stänga av en Azure FXT Edge Filer-nod:

1. Logga in på kontrollpanelen i klustret. (Vägbeskrivning i [Öppna sidorna Inställningar](fxt-cluster-create.md#open-the-settings-pages))
1. Klicka på fliken **Inställningar** och läs sedan in sidan**Kluster-FXT-noder.** **Cluster** > 
1. Leta reda på den som du vill stänga av i listan över klusternoder. Klicka på knappen **Nedmatning** i kolumnen **Åtgärder.** 
1. Vänta en stund. Noden stängs av och stänger av sig själv.

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om statuslysdioder och andra indikatorer i [maskinvarustatusen Monitor Azure FXT Edge Filer](fxt-monitor.md).
* Läs mer om Azure FXT Edge Filer nätaggregat i [Ansluta strömkablar](fxt-network-power.md#connect-power-cables).
