---
title: Azure Data Box Gateway Allmänna tillgänglighet viktig information| Microsoft-dokument
description: Beskriver kritiska öppna problem och lösningar för Azure Data Box Gateway som kör allmän tillgänglighetsversion.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265407"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Azure Data Box Edge/Azure Data Box Gateway Allmänna tillgänglighetsutgåva

## <a name="overview"></a>Översikt

Följande versionsfakturor identifierar de kritiska öppna problemen och de lösta problemen för GA-versionen (General Availability) för Azure Data Box Edge och Azure Data Box Gateway.

Viktig information uppdateras kontinuerligt och när kritiska problem som kräver en lösning upptäcks läggs de till. Innan du distribuerar databoxens edge/databoxgateway bör du noggrant granska informationen i viktig information.

GA-versionen motsvarar programvaruversionerna:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Nyheter

- **Nya virtuella diskavbildningar** - Nya VHDX och VMDK är nu tillgängliga i Azure-portalen. Ladda ned dessa avbildningar för att etablera, konfigurera och distribuera nya GA-enheter för Data Box Gateway. Data Box Gateway-enheter som skapats i tidigare förhandsversioner kan inte uppdateras till den här versionen. Mer information finns i [Förbereda för att distribuera Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **NFS-stöd** – NFS-stöd är för närvarande i förhandsversion och är tillgängligt för v3.0- och v4.1-klienter som har åtkomst till databoxens edge- och databoxgatewayenheter.
- **Lagringsåtersåterslag** – Din Data Box Edge-enhet tål fel på en datadisk med funktionen Lagringsåtersåterslag. Den här funktionen är för närvarande en förhandsversion. Du kan aktivera lagringsåtersåterslag genom att välja alternativet **Flexibel i** **lagringsinställningarna** i det lokala webbgränssnittet.


## <a name="known-issues-in-ga-release"></a>Kända problem i GA-utgåvan

Följande tabell innehåller en sammanfattning av kända problem för databoxgatewayen som körs.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Filtyper | Följande filtyper stöds inte: teckenfiler, blockfiler, sockets, pipes, symboliska länkar.  |Om du kopierar dessa filer skapas 0-längdsfiler på NFS-resursen. Dessa filer förblir i ett feltillstånd och rapporteras också i *error.xml*. <br> Symboliska länkar till kataloger resulterar i kataloger aldrig bli markerade offline. Därför kanske du inte ser det grå krysset på katalogerna som anger att katalogerna är offline och att allt associerat innehåll har överförts helt till Azure. |
| **2.** |Borttagning | På grund av ett fel i den här versionen, om en NFS-resurs tas bort, kan det hända att resursen inte tas bort. Delningsstatusen visar *Ta bort*.  |Detta inträffar endast när resursen använder ett filnamn som inte stöds. |
| **3.** |Kopiera | Datakopiering misslyckas med fel: Den begärda åtgärden kunde inte slutföras på grund av en filsystembegränsning.  |Ads (Alternate Data Stream) som är associerad med filstorlek som är större än 128 kB stöds inte.   |


## <a name="next-steps"></a>Nästa steg

- [Förbered distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Förbered distribution av Azure Data Box Edge](data-box-edge-deploy-prep.md).
