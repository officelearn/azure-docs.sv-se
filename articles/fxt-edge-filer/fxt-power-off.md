---
title: Hur du stänger en Filer för Microsoft Azure FXT Edge-enhet
description: Procedurer för nystartade företag och säker nedstängning av en Azure FXT Edge Filer-nod
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: v-erkell
ms.openlocfilehash: 8d779547040da9855409d3408827af2e4acd17a7
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542867"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Hur du säkert stänga av Azure FXT Edge Filer maskinvara

Men du kan använda fysiska strömknappen för att växla till en enskild nod, bör du inte använda den för att stängas av enhet under normala omständigheter.

När en nod i Azure FXT Edge Filer är på som en del av ett kluster, bör du använda Kontrollpanelen klusterprogramvaran för att stänga av maskinvaran. 

> [!NOTE] 
> För att undvika dataförlust eller skadade data kan alltid använda Kontrollpanelen programmet för att stänga av en Azure FXT Edge-Filer. Använd inte knappen fysisk strömkälla för avstängning, om du inte uppmanas att göra detta av Microsofts kundservice och Support.
> 
> Koppla från strömkablar eller Använd dina data i nödfall elektriska Centers elektricitet koppla från mekanism.

## <a name="shut-down-a-node-from-the-control-panel"></a>Stänga av en nod från Kontrollpanelen

Följ dessa instruktioner för att säkert stänga av en nod i Azure FXT Edge Filer:

1. Logga in på klustret på Kontrollpanelen. (Anvisningarna i [öppna inställningssidorna](fxt-cluster-create.md#open-the-settings-pages))
1. Klicka på den **inställningar** och sedan läsa in den **kluster** > **FXT noder** sidan.
1. I listan över klusternoder, hittar du det du vill stänga av. Klicka på den **Stäng** knappen i dess **åtgärder** kolumn. 
1. Vänta en stund. Noden stängs av och Stäng av sig själv.

## <a name="next-steps"></a>Nästa steg

* Läs mer om status-led: ar och andra indikatorer [övervaka Azure FXT Edge Filer maskinvarustatus](fxt-monitor.md).
* Läs mer om Azure FXT Edge Filer power på lämnar [ansluta strömkablar](fxt-network-power.md#connect-power-cables).
