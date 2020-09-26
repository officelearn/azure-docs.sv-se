---
title: Felsöka ögonblicks bilds principer för Azure NetApp Files | Microsoft Docs
description: Beskriver fel meddelanden och lösningar som kan hjälpa dig att felsöka problem med hantering av ögonblicks bilds principer för Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 1d7a91de8fa505fe4c2b06eea59f3acc2ae1f7e8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345499"
---
# <a name="troubleshoot-snapshot-policies"></a>Felsöka ögonblicks bilds principer

Den här artikeln beskriver fel scenarier som du kan stöta på när du hanterar Azure NetApp Files ögonblicks bilds principer. Den innehåller också lösningar som kan hjälpa dig att lösa problemen.

## <a name="snapshot-policy-creation-failing-with-invalid-snapshot-policy-name"></a>Det gick inte att skapa ögonblicks bild princip med ogiltigt namn på ögonblicks bild princip

Det uppstår ett fel under skapandet av ögonblicks bild principen om ditt namn på ögonblicks bild principen är ogiltigt. Följande rikt linjer gäller för namn på ögonblicks bilds principer:  

* Namnet på ögonblicks bilds principen får inte innehålla icke-ASCII-tecken eller specialtecken.
* Namnet på ögonblicks bilds principen måste börja med en bokstav eller en siffra och får innehålla bokstäver, siffror, under streck (_) och bindestreck (-).
* Namnet på ögonblicks bilds principen måste innehålla mellan 1 och 64 tecken.  

Du bör ändra namnet på ögonblicks bilds principen enligt rikt linjerna.  

## <a name="snapshot-policy-creation-failing-with-invalid-values"></a>Det gick inte att skapa ögonblicks bild princip med ogiltiga värden 

Azure NetApp Files inte att skapa en ögonblicks bild princip om du anger ett ogiltigt värde för ett fält, till exempel `Number of snapshots to keep` eller `Minute on the hour to take snapshot` .  
 
Giltiga värden är följande:   

* Värdet måste vara ett giltigt tal.
* Värdet måste vara mellan 0 och 59.

Kontrol lera att det finns ett giltigt värde för fälten.

## <a name="snapshot-policy-creation-failing-with-total-number-of-snapshots-to-keep-exceeds-255-error"></a>Det gick inte att skapa ögonblicks bild principen med det totala antalet ögonblicks bilder att behålla överskrider 255 

Varje volym kan ha [högst 255 ögonblicks bilder](azure-netapp-files-resource-limits.md). Det högsta antalet inkluderar summan av alla ögonblicks bilder per timme, dag, vecka och månad. Minska `Snapshots to keep` värdet och försök igen.

## <a name="assigning-policy-to-a-volume-failing-with-total-snapshot-policy-is-over-the-max-255-error"></a>Det går inte att tilldela en princip till en volym med "total ögonblicks bild princip" i "255"-fel

Varje volym kan ha [högst 255 ögonblicks bilder](azure-netapp-files-resource-limits.md). När summan av alla ögonblicks bilder på begäran, varje timme, varje dag, varje vecka och månad överskrider det högsta antalet uppstår ett fel. Minska `snapshots to keep` värdet eller ta bort några ögonblicks bilder på begäran och försök igen.

## <a name="next-steps"></a>Nästa steg  

* [Hantera ögonblicks bilds principer](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)
