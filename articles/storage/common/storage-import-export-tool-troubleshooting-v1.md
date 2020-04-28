---
title: Felsöka Azure import/export-verktyget | Microsoft Docs
description: Lär dig mer om några av de vanliga problemen som visas när du använder verktyget Azure import/export och hur du hanterar dem.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "75978408"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Felsökning av Azures import-/exportverktyg
Microsoft Azure Import/Export-verktyget returnerar fel meddelanden om det körs i problem. Det här avsnittet innehåller några vanliga problem som användarna kan köra.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Går det inte att kopiera sessionen? vad jag ska göra?  
 När en kopierings session Miss lyckas finns det två alternativ:  

 Om felet går att försöka igen, till exempel om nätverks resursen var offline under en kort period och nu är online igen, kan du återuppta kopierings sessionen. Om felet inte går att försöka igen, till exempel om du har angett fel käll fils katalog i kommando rads parametrarna, måste du avbryta kopierings sessionen. Se [förbereda hård diskar för ett import jobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md) för mer information om att återuppta och avbryta kopiering av sessioner.  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Jag kan inte återuppta eller avbryta en kopierings session.  
 Om kopierings sessionen är den första redigeringssessionen för en enhet bör fel meddelandet tillstånd: "den första kopierings sessionen kan inte återupptas eller avbrytas." I så fall kan du ta bort den gamla Journal filen och köra kommandot igen.  

 Om en Copy-session inte är den första för en enhet, kan den alltid återupptas eller avbrytas.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Jag förlorade Journal filen, kan jag ändå skapa jobbet?  
 Journal filen för en enhet innehåller all information om att kopiera data till den här enheten och det behövs för att lägga till fler filer på enheten och används för att skapa ett import jobb. Om journal filen förloras måste du göra om alla kopierings sessioner för enheten.  

## <a name="next-steps"></a>Nästa steg

* [Konfigurera verktyget Azure import/export](../storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)
