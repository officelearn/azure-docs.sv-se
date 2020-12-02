---
title: Åtgärda ServiceNow-synkroniseringsproblem manuellt
description: Återställ anslutningen till ServiceNow så att aviseringar i Microsoft Azure kan anropa ServiceNow igen
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f7d69c4e112ded678c70a516202492d37ee8f60a
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436869"
---
# <a name="troubleshooting-problems-in-itsm-connector"></a>Felsöka problem i ITSM-anslutningsprogram

## <a name="how-to-manually-fix-servicenow-sync-problems"></a>Åtgärda ServiceNow-synkroniseringsproblem manuellt

Azure Monitor kan ansluta till ITSM-leverantörer (IT Service Management) från tredje part. ServiceNow är en av dessa leverantörer.

Av säkerhets skäl kan du behöva uppdatera autentiseringstoken som används för din anslutning med ServiceNow.
Använd följande synkroniseringsprocess för att återaktivera anslutningen och uppdatera token:


1. Sök efter lösningen i den översta sökbanderollen och välj sedan de relevanta lösningarna

    ![Skärm bild som visar den översta sökbanderollen och var du väljer de relevanta lösningarna.](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. I lösnings skärmen väljer du "Markera alla" i prenumerations filtret och filtrerar sedan efter "ServiceDesk"

    ![Skärm bild som visar var du väljer Markera alla och var du vill filtrera efter ServiceDesk.](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Välj lösning för din ITSM-anslutning.
1. Välj ITSM-anslutning i den vänstra banderollen.

    ![Skärm bild som visar var du väljer ITSM-anslutningar.](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Välj varje koppling i listan. 
    1. Klicka på kopplingens namn för att konfigurera det
    1. Ta bort alla kopplingar som inte längre används

    1. Uppdatera fälten enligt [dessa definitioner](./itsmc-connections.md) baserat på din partner typ

    1. Klicka på synkronisera

       ![Skärm bild som visar knappen Synkronisera.](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Klicka på Spara

        ![Ny anslutning](media/itsmc-resync-servicenow/save-8bit.png)

f.    Granska meddelandena för att se om processen slutfördes utan framgång 

## <a name="next-steps"></a>Nästa steg

Läs mer om [IT-tjänstens hanterings anslutningar](itsmc-connections.md)
