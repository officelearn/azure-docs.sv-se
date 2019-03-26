---
title: Viktig information för Azure Data Box Gateway allmänt tillgänglig | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för Azure Data Box-Gateway som kör allmänt tillgängliga versionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 3f3e07d745d83041afc9dafd64678a3ac4a65012
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418040"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Viktig information för Azure Data Box Edge/Azure Data Box Gateway allmänt tillgänglig

## <a name="overview"></a>Översikt

Följande viktiga information identifiera kritiska öppna ärenden och löst problemen för allmän tillgänglighet (GA) som är viktig för Azure Data Box Edge och Azure Data Box-Gateway.

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din Data Box Edge/Data Box-Gateway måste du noga igenom uppgifterna i viktig information.

GA-version motsvarar programvaruversionerna:

- **Data Box Gateway 1903 (1.5.810.441)**
- **Data Box Edge 1903 (1.5.810.441)**


## <a name="whats-new"></a>Nyheter

- **Den nya virtuella diskavbildningar** -nya VHDX och VMDK är nu tillgängliga i Azure-portalen. Hämta dessa bilder för att etablera, konfigurera och distribuera nya Data Box Gateway GA-enheter. Data Box Gateway-enheter som skapats i förhandsversionen av tidigare versioner inte kan uppdateras till den här versionen. Mer information går du till [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Stöd för NFS** -stöd för NFS är för närvarande i förhandsversion och är tillgängliga för v3.0 och v4.1 klienter med åtkomst till Data Box Edge och rutan Datagateway-enheter.
- **Storage återhämtning** -fel på en datadisk med återhämtning lagringsfunktionen klarar att din Data Box-Edge-enhet. Den här funktionen är för närvarande en förhandsversion. Du kan aktivera storage återhämtning genom att välja den **elastisk** alternativet i den **Lagringsinställningar** i lokalt webbgränssnitt.


## <a name="known-issues-in-ga-release"></a>Kända problem i GA-version

Följande tabell innehåller en sammanfattning av kända problem för Data Box-Gateway som kör version.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Filtyper | Följande filtyper stöds inte: tecknet filer, filer, sockets, pipes eller symboliska länkar.  |Kopiera filerna resultat i 0-length-filer som skapas på NFS dela. Dessa filer finns kvar i ett feltillstånd och rapporteras även i *error.xml*. <br> Symboliska länkar till kataloger resultera i kataloger aldrig komma markerade offline. Därför kan du inte ser grå mellan på de kataloger som anger att katalogerna som är offline och allt tillhörande innehåll helt har överförts till Azure. |
| **2.** |Ta bort | På grund av ett fel i den här versionen, om en NFS-resurs raderas kan sedan resursen inte tas bort. Resurs-status visas *tas bort*.  |Detta inträffar bara när resursen använder ett namn för filen stöds inte. |
| **3.** |Kopiera | Kopiering av data misslyckas med fel:  Den begärda åtgärden kunde inte slutföras på grund av en begränsning i filsystemet.  |Den alternativa Data Stream (AD) som är associerade med filstorlek är större än 128 KB stöds inte.   |


## <a name="next-steps"></a>Nästa steg

- [Förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Förbereda för distribution av Azure Data Box Edge](data-box-edge-deploy-prep.md).
