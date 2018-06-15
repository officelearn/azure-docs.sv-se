---
title: Reparera ett Azure Import/Export-importjobb - v1 | Microsoft Docs
description: Lär dig mer om att reparera ett importjobb som har skapats och att köras med tjänsten Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: c837713fd9e7d03287ae5a3644fd6bb47714c9d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927469"
---
# <a name="repairing-an-import-job"></a>Reparera ett importjobb
Microsoft Azure Import/Export-tjänsten kanske inte kan kopiera vissa av dina filer eller delar av en fil till Windows Azure Blob-tjänsten. Vissa fel anledningar:  
  
-   Skadade filer  
  
-   Skadade enheter  
  
-   Lagringskontots åtkomstnyckel ändras när filen överfördes.  
  
Du kan köra verktyget Microsoft Azure Import/Export med importen jobbets kopiera loggfiler och verktyget Överför filer saknas (eller delar av en fil) till ditt Windows Azure storage-konto för att slutföra importjobbet.  
  
## <a name="repairimport-parameters"></a>RepairImport parametrar

Följande parametrar kan anges med **RepairImport**: 
  
|||  
|-|-|  
|**/ r:**< RepairFile\>|**Krävs.** Sökvägen till filen reparation, som spårar förloppet för att reparera och gör att du kan återuppta en avbruten reparation. Varje enhet måste ha en repair-fil. När du startar en reparation för en viss enhet kan du skicka i sökvägen till en repair-fil som ännu inte finns. Om du vill återuppta ett avbryts reparation, överför du namnet på en befintlig fil för reparation. Reparera filen motsvarar målenheten måste alltid anges.|  
|**/logdir:**< LogDirectory\>|**Valfritt.** Loggkatalogen. Utförlig loggfilerna skrivs till den här katalogen. Om inga loggkatalogen anges, används den aktuella katalogen som loggkatalogen.|  
|**/ d:**< TargetDirectories\>|**Krävs.** En eller flera semikolonavgränsad kataloger som innehåller de ursprungliga filerna som har importerats. Importera enheten kan också användas, men är inte nödvändigt om alternativa platser för ursprungliga filerna är tillgängliga.|  
|**/BK:**< BitLockerKey\>|**Valfritt.** Du bör ange BitLocker-nyckel om du vill använda verktyget för att låsa upp en krypterad enhet där de ursprungliga filerna är tillgängliga.|  
|**/SN:**< StorageAccountName\>|**Krävs.** Namnet på lagringskontot för importjobbet.|  
|**/Sk:**< StorageAccountKey\>|**Krävs** endast om en behållare SAS inte har angetts. Kontonyckel för lagringskontot för importjobbet.|  
|**/csas:**< ContainerSas\>|**Krävs** om lagringskontots åtkomstnyckel inte har angetts. Behållaren SAS för att komma åt blobar som är associerad med importjobbet.|  
|**/ CopyLogFile:**< DriveCopyLogFile\>|**Krävs.** Sökvägen till loggfilen för enheten kopia (antingen utförlig loggen eller fel logga). Filen har genererats av Windows Azure Import/Export-tjänsten och kan hämtas från blob storage som är associerat med jobbet. Kopiera loggfilen innehåller information om misslyckade blobbar eller filer, vilket är repareras.|  
|**/ PathMapFile:**< DrivePathMapFile\>|**Valfritt.** Sökväg till en textfil som kan användas för att lösa tvetydigheter om du har flera filer med samma namn som du importerar i samma jobb. Första gången verktyget körs kan det fylla i den här filen med alla tvetydigt namn. Efterföljande körningar av verktyget använder den här filen för att lösa tvetydigheter.|  
  
## <a name="using-the-repairimport-command"></a>Med hjälp av kommandot RepairImport  
Om du vill reparera importera data med strömmande data över nätverket, måste du ange de kataloger som innehåller de ursprungliga filerna som du importerar med hjälp av den `/d` parameter. Du måste också ange kopiera loggfilen som du hämtade från ditt lagringskonto. Det ser ut som en typisk kommandoraden för att reparera ett importjobb med partiellt fel:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
I följande exempel på en loggfil för kopiera är en 64-K del av en fil skadad på enheten som levererades för importjobbet. Eftersom detta är endast felet anges resten av blobbar i jobbet har importerats.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
När den här loggfilen kopia skickas till verktyget Azure Import/Export försöker verktyget Slutför import för den här filen genom att kopiera saknas innehållet över nätverket. Följande exempel ovan, verktyget söker efter den ursprungliga filen `\animals\koala.jpg` i de två katalogerna `C:\Users\bob\Pictures` och `X:\BobBackup\photos`. Om filen `C:\Users\bob\Pictures\animals\koala.jpg` finns verktyget Azure Import/Export kopierar saknas dataområdet till motsvarande blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Lösa konflikter när du använder RepairImport  
I vissa situationer kan verktyget kanske inte kan hitta eller öppna filen behövs för en av följande orsaker: gick inte att hitta filen, filen är inte tillgänglig, filnamnet är tvetydigt eller innehållet i filen är inte korrekta längre.  
  
Ett tvetydigt fel kan inträffa om verktyget försöker hitta `\animals\koala.jpg` och det finns en fil med detta namn under både `C:\Users\bob\pictures` och `X:\BobBackup\photos`. Det vill säga både `C:\Users\bob\pictures\animals\koala.jpg` och `X:\BobBackup\photos\animals\koala.jpg` finns på import jobbet enheter.  
  
Den `/PathMapFile` alternativet kan du lösa problemet. Du kan ange namnet på filen som innehåller en lista över filer som verktyget går inte att identifiera. Följande kommandorad exempel fyller `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Verktyget kommer sedan skriva problematiska sökvägarna till `9WM35C2V_pathmap.txt`, ett på varje rad. Filen kan exempelvis innehålla följande poster efter körning av kommandot:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 För varje fil i listan, bör du försöka hitta och öppna filen för att säkerställa att den är tillgänglig för verktyget. Du kan ändra sökvägen mappningsfilen och Lägg till sökvägen till varje fil på samma rad, avgränsade med semikolon fliken om du vill berätta verktyget uttryckligen var du hittar en fil:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Efter att göra nödvändiga filer tillgängliga för verktyget eller uppdatering av mappningsfilen sökväg, kan du köra verktyget för att slutföra importen.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
