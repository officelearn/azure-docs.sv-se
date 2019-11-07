---
title: Återställning av lagrings konto behållare
description: Återställning av lagrings konto behållare
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
ms.openlocfilehash: f095bdfe7bbb5777a2ad2aabb3bda92d0974457d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693584"
---
# <a name="storage-account-container-recovery"></a>Återställning av lagrings konto behållare

Azure Storage ger data återhämtning via automatiserade repliker. Detta förhindrar dock inte program kod eller användare från att skada data, vare sig de är oavsiktligt eller skadligt. Att upprätthålla data åter givningen i program-eller användar fel kräver mer avancerade tekniker, till exempel att kopiera data till en sekundär lagrings plats med en Gransknings logg.

## <a name="checking-azure-storage-account-type"></a>Kontrollerar Azure Storage konto typ

1. Navigera till [Azure-portalen](https://portal.azure.com/).

2. Leta rätt på ditt lagringskonto.

3. I avsnittet **Översikt** kontrollerar du om det finns någon **replikering**.

   ![Bild](media/storage-account-container-recovery/1.png)

4. Om typen av replikering är **GRS/RA-GRS**kan återställningen av konto behållaren vara möjlig utan garanti. Det går inte att utföra alla andra typer av replikering.

5. Samla in följande information och skicka en support förfrågan med Microsoft Support.

   * Lagrings konto namn:
   * Behållar namn:
   * Tid för borttagning:

   Följande tabell innehåller en översikt över omfånget för lagrings kontots behållar återställning beroende på replikeringens strategi.

   |Typ av innehåll|LRS|ZRS|GRS|RA-GRS| 
   |---|---|---|---|---|
   |Lagrings behållare|Nej|Nej|Ja|Ja| 

   * Vi kan försöka återställa lagrings konto behållaren men utan någon garanti. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Saker att inte göra för att återställning ska lyckas

* Återskapa inte behållaren (med samma namn).  
* Om du redan har återskapat behållaren måste du ta bort behållaren innan du kan skicka en support förfrågan om återställning.

## <a name="steps-to-prevent-this-in-the-future"></a>Steg för att förhindra detta i framtiden

1. För att undvika detta i framtiden är den mest rekommenderade funktionen för att använda [mjuk borttagning](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Vi rekommenderar också [ögonblicks bilds](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob) funktionen.
 
## <a name="next-steps"></a>Nästa steg

Här följer två exempel koder i funktionen:

  * [Skapa och hantera en BLOB-ögonblicksbild i .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Använda BLOB-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

