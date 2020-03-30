---
title: Återställning av lagringskontobehållare
description: Återställning av lagringskontobehållare
services: storage
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/30/2019
ms.service: storage
manager: dcscontentpm
ms.openlocfilehash: 8b9b69fb32edab54fb2df558ea292df264e00cbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562283"
---
# <a name="storage-account-container-recovery"></a>Återställning av container för lagringskonto

Azure Storage ger dataåtersåtersåtare genom automatiserade repliker. Detta hindrar dock inte programkod eller användare från att skada data, vare sig oavsiktligt eller uppsåtligt. För att upprätthålla dataåtergivning inför program- eller användarfel krävs mer avancerade tekniker, till exempel att kopiera data till en sekundär lagringsplats med en granskningslogg.

## <a name="checking-azure-storage-account-type"></a>Kontrollera azure storage-kontotyp

1. Navigera till [Azure-portalen](https://portal.azure.com/).

2. Leta rätt på ditt lagringskonto.

3. Sök efter **Replikering**i avsnittet **Översikt** .

   ![Bild](media/storage-account-container-recovery/1.png)

4. Om replikeringstypen är **GRS/RA-GRS**är återställning av kontobehållaren möjlig utan garanti. För alla andra replikeringstyper är det inte möjligt.

5. Samla in följande information och lämna in en supportbegäran med Microsoft Support.

   * Namn på lagringskonto:
   * Behållarens namn:
   * Tid för radering:

   I följande tabell finns en översikt över omfattningen av återställning av lagringskontobehållare beroende på replikeringsstrategin.

   |Typ av innehåll|LRS|ZRS|GRS|RA - GRS| 
   |---|---|---|---|---|
   |Behållare för lagring|Inga|Inga|Ja|Ja| 

   * Vi kan försöka återställa behållaren för lagringskontot, men utan någon garanti. 

## <a name="things-not-to-do-for-recover-to-be-successful"></a>Saker att inte göra för att återhämta sig för att lyckas

* Återskapa inte behållaren (med samma namn).  
* Om du redan har återskapat behållaren måste du ta bort behållaren innan du skickar in en supportbegäran för återställning.

## <a name="steps-to-prevent-this-in-the-future"></a>Åtgärder för att förhindra detta i framtiden

1. För att undvika detta i framtiden är den mest rekommenderade funktionen som ska användas [Soft Delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete).

2. Vi rekommenderar också [funktionen Ögonblicksbild.](https://docs.microsoft.com/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob)
 
## <a name="next-steps"></a>Nästa steg

Här är två exempelkoder på funktionen:

  * [Skapa och hantera en blob-ögonblicksbild i .NET](https://docs.microsoft.com/azure/storage/storage-blob-snapshots)
  * [Använda blob-ögonblicksbilder med PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)
  

