---
title: Felsöka verktyget för import/export av Azure | Microsoft-dokument
description: Lär dig mer om några av de vanligaste problemen när du använder Verktyget för import/export i Azure och hur du hanterar dem.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 4eeeb538bcd39eed40a92dd45e7ba7bed25558e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978408"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Felsökning av Azures import-/exportverktyg
Microsoft Azure Import/Export Tool returnerar felmeddelanden om det uppstår problem. I det här avsnittet visas några vanliga problem som användare kan stöta på.  

## <a name="a-copy-session-fails-what-i-should-do"></a>En kopieringssession misslyckas, vad jag ska göra?  
 När en kopieringssession misslyckas finns det två alternativ:  

 Om felet kan återanvändas, till exempel om nätverksresursen var offline under en kort period och nu är online igen, kan du återuppta kopieringssessionen. Om felet inte kan försökas igen, till exempel om du har angett fel källfilkatalog i kommandoradsparametrarna, måste du avbryta kopieringssessionen. Mer information om hur du återupptar och avbryter kopieringssessioner finns i [Förbereda hårddiskar för ett importjobb.](../storage-import-export-tool-preparing-hard-drives-import-v1.md)  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Jag kan inte återuppta eller avbryta en kopieringssession.  
 Om kopieringssessionen är den första kopieringssessionen för en enhet ska felmeddelandet anges: "Den första kopieringssessionen kan inte återupptas eller avbrytas." I det här fallet kan du ta bort den gamla journalfilen och köra kommandot igen.  

 Om en kopieringssession inte är den första för en enhet kan den alltid återupptas eller avbrytas.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Jag förlorade journalfilen, kan jag fortfarande skapa jobbet?  
 Journalfilen för en enhet innehåller fullständig information om kopiering av data till den här enheten, och det behövs för att lägga till fler filer på enheten och kommer att användas för att skapa ett importjobb. Om journalfilen går förlorad måste du göra om alla kopieringssessioner för enheten.  

## <a name="next-steps"></a>Nästa steg

* [Ställa in verktyget för azure import/export](../storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)
