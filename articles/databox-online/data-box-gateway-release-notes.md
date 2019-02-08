---
title: Azure Data Box Gateway förhandsversion viktig information | Microsoft Docs
description: Beskriver viktiga öppna problem och lösningar för Azure Data Box-Gateway som kör förhandsversionen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: 0265de5b224e62d188fe6e3b9322d5c2e3f77fa1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883142"
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
| **1.** | I den här versionen, när en fil som har överförts av ett annat verktyg (AzCopy) uppdateras och sedan uppdateras på ett sätt som ökar/utökar filstorlek, observeras sedan följande fel: *Fel 400: InvalidBlobOrBlock (det angivna blob eller block-innehållet är ogiltig.)*|
| **2.** |På grund av ett programfel i den här versionen kan det hända att instanser av felkoden 110 i *error.xml* med okänt objektnamn. | 
| **3.** |På grund av ett fel i den här versionen kan du se instanser av felkoden 2003 vid uppladdning av specifika filer. | 
| **4.** |I den här versionen kan du uppdatera endast en resurs åt gången. | 


## <a name="known-issues-in-preview-release"></a>Kända problem i förhandsversionen

Följande tabell innehåller en sammanfattning av kända problem för Data Box-Gateway som kör förhandsversionen.

| Nej. | Funktion | Problem | Lösning/kommentarer |
| --- | --- | --- | --- |
| **1.** |Uppdateringar |Data Box Gateway-enheter som skapats i förhandsversionen av tidigare versioner inte kan uppdateras till den här versionen. |Hämta avbildningar för virtuell disk från den nya versionen, konfigurera och distribuera nya enheter. Mer information går du till [förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md). |
| **2.** |Etablerade datadisk |När du har etablerat en datadisk med en viss angivna storlek och skapat den motsvarande Data Box-Gateway, måste du inte krympa datadisken. Försök att minska disk resulterar i förlust av lokala data på enheten. | |
| **3.** |Byt namn |Namnbyte av objekt stöds inte. |Kontakta Microsoft Support om den här funktionen är avgörande för ditt arbetsflöde. |
| **4.** |Kopiera| Om en skrivskyddad fil kopieras till enheten, bevaras inte skrivskyddad egenskap. | |
| **5.** |Filtyper | Följande Linux-filtyper stöds inte: tecknet filer, filer, sockets, pipes eller symboliska länkar.  |Kopiera filerna resultat i 0-length-filer som skapas på NFS dela. Dessa filer finns kvar i ett feltillstånd och rapporteras även i *error.xml*. |
| **6.** |Ta bort | På grund av ett fel i den här versionen, om en NFS-resurs raderas kan sedan resursen inte tas bort. Resurs-status visas *tas bort*.  |Detta inträffar bara när resursen använder ett namn för filen stöds inte. |
| **7.** |Uppdatera | Behörigheter och åtkomstkontrollistor (ACL) bevaras inte över en uppdatering.  | |
| **8.** |Kopiera | Kopiering av data misslyckas med fel:  Den begärda åtgärden kunde inte slutföras på grund av en begränsning i filsystemet.  |Det här felet uppstår när den alternativa Data Stream (AD) som är associerade med filen överskrider 128 KB (maxgränsen för ReFS).  |
| **9.** |Symboliska länkar |Symboliska länkar stöds inte.  |Symboliska länkar till kataloger resultera i kataloger aldrig komma markerade offline. Därför kan du inte ser grå mellan på de kataloger som anger att katalogerna som är offline och allt tillhörande innehåll helt har överförts till Azure. |
| **10.** |Resurser |Uppdatera en befintlig behållare med Page Blobs, till en Blockblob resurs (eller vice versa) leder till uppladdningsfel på filändring.  |Det här beteendet som visas när du följer dessa steg: <li> Skapa en Blockblob-resurs på enheten. </li><li> Koppla resursen till en befintlig cloud-behållare som har Page Blobs.</li><li>Uppdatera denna resurs. </li><li>Ändra några av de uppdatera filer som redan lagras som Sidblobar i molnet.</li> Ladda upp fel visas. |
| **11.** |Onlinehjälp |Hjälplänkar i Azure-portalen kan inte länka till dokumentationen.|Länken i fungerar i den allmänt tillgängliga versionen. |

## <a name="next-steps"></a>Nästa steg

- [Förbereda för distribution av Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


