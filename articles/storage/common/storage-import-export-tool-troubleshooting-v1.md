---
title: Felsökning av verktyget Azure Import/Export | Microsoft Docs
description: Lär dig mer om några vanliga problem som visas när du använder verktyget Azure Import/Export och hur du hanterar dem.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 58ba44488e8ef211e7c318fc9ba6497a5b1b69bb
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523282"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Felsökning av Azures import-/exportverktyg
Microsoft Azure Import/Export-verktyget returnerar felmeddelanden om den körs i problem. Det här avsnittet innehåller några vanliga problem som användarna kan köra i.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>En kopia session misslyckas, vad jag ska göra?  
 När en session för kopiering inte, finns det två alternativ:  
  
 Om felet är återförsökbart, till exempel om nätverksresursen var offline under en kort period och nu är online igen kan återuppta du session för kopiering. Om felet inte är återförsökbart, till exempel om du har angett fel filen källkatalogen i parametrarna för kommandoraden, måste du avbryta kopia sessionen. Se [förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md) för mer information om att återuppta och avbryts kopia sessioner.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Jag kan inte återuppta eller avbryta en kopia-session.  
 Om sessionen kopia är den första kopia sessionen för en enhet så felmeddelandet ska ange: ”den första kopia sessionen inte kan återupptas eller avbröts”. I det här fallet kan du ta bort den gamla journalfilen och kör kommandot igen.  
  
 Om en kopia session inte är den första mallen för en enhet kan det alltid återupptas eller avbröts.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Försvann journal-fil, kan jag fortfarande skapa jobbet?  
 Journalfil för en enhet innehåller fullständig information om att kopiera data till den här enheten och det behövs för att lägga till fler filer till enheten och används för att skapa ett importjobb. Om journalfilen tappas bort, behöver du göra om alla kopia sessioner för enheten.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera azure import/export-verktyget](../storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)
