---
title: Azure Data Box Gateway & Azure Data Box Edge 1906-versionsanteckningar| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för Azure Data Box Gateway och Azure Data Box Edge som kör 1906-versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/18/2019
ms.author: alkohli
ms.openlocfilehash: 69c905176a5beb1c7d442cd5f1a69161cd520c5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099491"
---
# <a name="azure-data-box-edge-and-azure-data-box-gateway-1906-release-notes"></a>Azure Data Box Edge och Azure Data Box Gateway 1906-versionsanteckningar

Följande versionsfakturor identifierar de kritiska öppna problemen och de lösta problemen för 1906-versionen för Azure Data Box Edge och Azure Data Box Gateway.

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar databoxens edge/databoxgateway bör du noggrant granska informationen i viktig information.

Den här versionen motsvarar programvaruversionerna:

- **Data Box Gateway 1906 (1.6.978.743)**
- **Data Box Edge 1906 (1.6.978.743)**

> [!NOTE]
> Uppdatering 1906 kan endast tillämpas på Data Box Edge-enheter som kör allmän tillgänglighet (GA) eller 1905-versionen av programvaran.

## <a name="whats-new"></a>Nyheter

- **Buggfix i arbetsflödet för hantering av återställningsnyckel** - I den tidigare versionen fanns det ett fel som var beroende på vilket återställningsnyckeln inte tillämpades. Det här felet är åtgärdat i den här versionen. Vi rekommenderar starkt att du installerar den här uppdateringen eftersom återställningsnyckeln gör att du kan återställa data på enheten, i händelse av att enheten inte startar upp. Mer information finns i hur du [sparar återställningsnyckeln när du distribuerar Data Box Edge eller Data Box Gateway](data-box-edge-deploy-connect-setup-activate.md#set-up-and-activate-the-physical-device).
- **Fpga-loggningsförbättringar (Field Programmemable Gate Array)** - Från och med 1905 gjordes förbättringar av loggning och varning relaterade till FPGA. Detta fortsätter att vara en nödvändig uppdatering för Data Box Edge om du använder Edge-beräkningsfunktionen med FPGA. Mer information finns i hur du [omvandlar data med Edge-beräkning på din Data Box Edge](data-box-edge-deploy-configure-compute-advanced.md).

## <a name="known-issues-in-ga-release"></a>Kända problem i GA-utgåvan

Inga nya problem är kända för den här versionen. Alla release noterade frågor har fört över från tidigare utgåvor. Om du vill se en lista över kända problem går du till [Kända problem i GA-versionen](data-box-gateway-release-notes.md#known-issues-in-ga-release).


## <a name="next-steps"></a>Nästa steg

- [Förbereda distributionen av Azure Data Box Gateway](data-box-gateway-deploy-prep.md)
- [Förbered att distribuera Azure Data Box Edge](data-box-edge-deploy-prep.md)
