---
title: Åtgärda ServiceNow-synkroniseringsproblem manuellt
description: Återställ anslutningen till ServiceNow så att aviseringar i Microsoft Azure kan anropa ServiceNow igen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: 9d32b66b7e3ab27012ee671ba8d70735dce73884
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274281"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Åtgärda ServiceNow-synkroniseringsproblem manuellt

Azure Monitor kan ansluta till ITSM-leverantörer (ITM) från tredje part. ServiceNow är en av dessa leverantörer. 

Av säkerhetsskäl kan du behöva uppdatera autentiseringstoken som används för anslutningen till ServiceNow.
Använd följande synkroniseringsprocess för att återaktivera anslutningen och uppdatera token: 


1. Sök efter lösningen i den övre sökbannern och välj sedan relevanta lösningar

    ![Ny anslutning](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. På lösningsskärmen väljer du "Välj alla" i prenumerationsfiltret och filtrerar sedan efter "ServiceDesk"

    ![Ny anslutning](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Välj lösningen för din ITSM-anslutning.
1. Välj ITSM-anslutning i den vänstra banderollen.

    ![Ny anslutning](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Markera varje koppling i listan. 
    1. Klicka på kopplingsnamnet för att konfigurera det
    1. Ta bort kopplingar som inte längre används

    1. Uppdatera fälten enligt [dessa definitioner](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) baserat på din partnertyp

    1. Klicka på synkronisering

       ![Ny anslutning](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klicka på spara

        ![Ny anslutning](media/itsmc-resync-servicenow/save-8bit.png)

f.    Granska meddelandena för att se om processen har slutförts med framgång 

## <a name="next-steps"></a>Efterföljande moment

Läs mer om [IT Service Management-anslutningar](itsmc-connections.md)
