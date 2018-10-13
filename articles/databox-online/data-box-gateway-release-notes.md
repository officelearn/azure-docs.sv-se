---
title: Azure Data Box Gateway förhandsversion viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för Azure Data Box-Gateway som kör förhandsversionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: f5e19d59dfddc3be849700f3678519179b5b39ba
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/12/2018
ms.locfileid: "49164577"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Viktig information om Azure Data Box Gateway förhandsversion

## <a name="overview"></a>Översikt

Följande viktiga information identifiera kritiska öppna ärenden och löst problem för Microsoft Azure Data Box Gateway förhandsversionen.

Den viktiga informationen uppdateras kontinuerligt och allteftersom allvarliga problem som kräver en lösning upptäcks, läggs de. Innan du distribuerar din Data Box-Gateway måste du noga igenom uppgifterna i viktig information.

Förhandsversionen motsvarar programvaruversionen **Data Box Gateway Preview Version 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problem som åtgärdas i förhandsversionen

Följande tabell innehåller en översikt över problem som åtgärdas i den här versionen.

| Nej. | Problem |
| --- | --- |
| 1 | I den här versionen, när en fil som har överförts av ett annat verktyg (AzCopy) uppdateras och sedan uppdateras på ett sätt som ökar/utökar filstorleken sedan följande fel observeras: *fel 400: InvalidBlobOrBlock (angiven blob eller block innehållet är ogiltigt.)*|


## <a name="known-issues-in-preview-release"></a>Kända problem i förhandsversionen

Följande tabell innehåller en sammanfattning av kända problem för Data Box-Gateway som kör förhandsversionen.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |Data Box Gateway-enheter som skapats i förhandsversionen av tidigare versioner inte kan uppdateras till den här versionen. |Hämta avbildningar för virtuell disk från den nya versionen, konfigurera och distribuera nya enheter. Mer information går du till [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angivna storlek och skapat den motsvarande Data Box-Gateway, måste du inte krympa datadisken. Försök att minska disk resulterar i förlust av lokala data på enheten. | |
| **3.** |Uppdatera |I den här versionen kan du uppdatera endast en resurs åt gången. | |
| **4.** |Byt namn |Namnbyte av objekt stöds inte. |Kontakta Microsoft Support om den här funktionen är avgörande för ditt arbetsflöde. |
| **5.** |Kopiera| Om en skrivskyddad fil kopieras till enheten, bevaras inte skrivskyddad egenskap. | |
| **6.** |Logs| På grund av ett programfel i den här versionen kan det hända att instanser av felkoden 110 i *error.xml* med okänt objektnamn. | |
| **7.** |Ladda upp | På grund av ett fel i den här versionen kan du se instanser av felkoden 2003 vid uppladdning av specifika filer. | |
| **8.** |Filtyper | Följande Linux-filtyper stöds inte: tecknet filer, filer, sockets, pipes eller symboliska länkar.  |Kopiera filerna resultat i 0-length-filer som skapas på NFS dela. Dessa filer finns kvar i ett feltillstånd och rapporteras även i *error.xml*. |
| **9.** |Ta bort | På grund av ett fel i den här versionen, om en NFS-resurs raderas kan sedan resursen inte tas bort. Resurs-status visas *tas bort*.  |Detta inträffar bara när resursen använder ett namn för filen stöds inte. |
| **10.** |Uppdatera | Behörigheter och åtkomstkontrollistor (ACL) bevaras inte över en uppdatering.  | |
| **11.** |Onlinehjälp |Hjälplänkar i Azure-portalen kan inte länka till dokumentationen.|Länken i fungerar i den allmänt tillgängliga versionen. |



## <a name="next-steps"></a>Nästa steg

- [Förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


