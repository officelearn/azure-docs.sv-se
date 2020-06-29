---
title: Stänga av en Microsoft Azure FXT Edge-enhet
description: Procedurer för start och säker avstängning av en Azure FXT Edge-nod
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 92364de82bc3de8229eced4ee02997a27afbde45
ms.sourcegitcommit: 374e47efb65f0ae510ad6c24a82e8abb5b57029e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2020
ms.locfileid: "85506420"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Hur man på ett säkert sätt stänger av maskin vara från Azure FXT Edge-filer

Även om du kan använda den fysiska strömbrytaren för att växla på en enskild nod bör du inte använda den för att stänga av enheten under normala omständigheter.

När en Azure FXT Edge-nod används som en del av ett kluster, bör du använda kluster kontroll panelens program vara för att stänga av maskin varan. 

> [!NOTE] 
> För att undvika data förlust eller skada bör du alltid använda program varan på kontroll panelen för att stänga av en Azure FXT Edge-filer. Använd inte den fysiska ström knappen för avstängning om du inte uppmanas att göra det av Microsofts kund service och support.
> 
> I en elektrisk nöd situation kan du koppla bort ström sladdarna eller använda data centrets mekanism för el från koppling.

## <a name="shut-down-a-node-from-the-control-panel"></a>Stänga av en nod från kontroll panelen

Följ de här anvisningarna för att säkert stänga av en Azure FXT Edge-nod:

1. Logga in på kluster kontroll panelen. (Riktningar i [Öppna inställnings sidorna](fxt-cluster-create.md#open-the-settings-pages))
1. Klicka på fliken **Inställningar** och Läs sedan in sidan **cluster**  >  **FXT Nodes** .
1. Leta upp den som du vill stänga av i listan över klusternoder. Klicka på knappen **ström** i kolumnen **åtgärder** . 
1. Vänta en stund. Noden stängs av och stängs av.

## <a name="next-steps"></a>Nästa steg

* Läs om status lampor och andra indikatorer i [övervaka maskin varu status för Azure FXT Edge](fxt-monitor.md)-filer.
* Läs mer om Azure FXT Edge-material med strömförsörjning i [ansluta ström kablar](fxt-network-power.md#connect-power-cables).
