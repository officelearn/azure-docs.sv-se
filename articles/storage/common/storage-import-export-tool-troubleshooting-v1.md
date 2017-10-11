---
title: "Felsökning av verktyget Azure Import/Export | Microsoft Docs"
description: "Lär dig mer om några vanliga problem som visas när du använder verktyget Azure Import/Export och hur du hanterar dem.."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: b91ca5eb-c557-460a-9afc-0590b38471f9
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 7bfda602dbc0ea47828a7c9243b8b9b09ec78432
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2017
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Felsökning av Azures import-/exportverktyg
Verktyget Microsoft Azure Import/Export returnerar felmeddelanden om den körs i problem. Det här avsnittet beskrivs några vanliga problem som användarna kan köra till.  
  
## <a name="a-copy-session-fails-what-i-should-do"></a>En kopia session misslyckas vad jag ska göra?  
 När en kopia session misslyckas, finns det två alternativ:  
  
 Om felet är återförsökbart, till exempel om nätverksresursen var offline under en kort period och nu är online igen kan återuppta du kopiera sessionen. Om felet inte är återförsökbart, till exempel om du har angett fel fil källkatalogen i parametrar på kommandoraden, måste du avbryta kopiera sessionen. Se [förbereder hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md) för mer information om att återuppta och avbryter kopiera sessioner.  
  
## <a name="i-cant-resume-or-abort-a-copy-session"></a>Jag kan inte återuppta eller avsluta en session kopia.  
 Om den kopia sessionen är den första kopia sessionen för en enhet och sedan felmeddelandet ska ange: ”den första kopia sessionen inte kan återupptas eller avbröts”. I det här fallet kan du ta bort den gamla journalfilen och kör kommandot igen.  
  
 Om en kopia session inte är den första för en enhet, den alltid återupptas eller avbröts.  
  
## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Journalfilen gick förlorad, kan jag fortfarande skapa jobbet?  
 Journal-fil för en enhet innehåller fullständig information för att kopiera data till den här enheten och den krävs för att lägga till fler filer på enheten och används för att skapa ett importjobb. Om journalfilen tappas bort, behöver du göra om alla kopiera sessioner för enheten.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget azure import/export](../storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)
