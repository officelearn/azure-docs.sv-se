---
title: Reparera ett Azure Import/Export-importjobb - v1 | Microsoft Docs
description: Lär dig mer om att reparera ett importjobb som har skapats och körs med hjälp av tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fda1d3d626c91ba984f08b96c79ab6a2fd2ec74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477594"
---
# <a name="repairing-an-import-job"></a>Reparera ett importjobb
Microsoft Azure Import/Export-tjänsten kanske inte kan kopiera en del av dina filer eller delar av en fil till Windows Azure Blob service. Några orsaker till fel är:  
  
-   Skadade filer  
  
-   Skadade enheter  
  
-   Lagringskontonyckeln ändra när filen överfördes.  
  
Du kan köra verktyget Microsoft Azure Import/Export med importen loggfiler för jobbets kopia och verktyget Överför filer saknas (eller delar av en fil) till ditt Windows Azure storage-konto för att slutföra importjobbet.  
  
## <a name="repairimport-parameters"></a>RepairImport parametrar

Följande parametrar kan bara anges med **RepairImport**: 
  
|||  
|-|-|  
|**/ r:**< RepairFile\>|**Krävs.** Sökväg till filen reparation som spårar förloppet för reparation och gör att du kan återuppta en avbruten reparation. Varje enhet måste ha en repair-fil. När du startar en reparation för en viss enhet, kan du skicka in sökvägen till en repair-fil som inte finns ännu. Om du vill återuppta en avbruten reparation, överför du namnet på en befintlig repair-fil. Reparera filen motsvarar målenheten måste alltid anges.|  
|**/logdir:**< LogDirectory\>|**Valfritt.** Loggkatalogen. Utförliga loggfiler skrivs till den här katalogen. Om inga loggkatalogen anges, används den aktuella katalogen som log-katalogen.|  
|**/ d:**< TargetDirectories\>|**Krävs.** En eller flera semikolonavgränsad kataloger som innehåller de ursprungliga filerna som har importerats. Import-enhet kan också användas, men är inte nödvändigt om alternativa platser för ursprungliga filerna är tillgängliga.|  
|**/bk:**<BitLockerKey\>|**Valfritt.** Du bör ange BitLocker-nyckel om du vill att verktyget för att låsa upp en krypterad enhet där de ursprungliga filerna är tillgängliga.|  
|**/sn:**<StorageAccountName\>|**Krävs.** Namnet på lagringskontot för importjobbet.|  
|**/sk:**<StorageAccountKey\>|**Krävs** endast om en SAS-behållare inte har angetts. Kontonyckel för lagringskontot för importjobbet.|  
|**/csas:**< ContainerSas\>|**Krävs** endast om lagringskontonyckeln inte har angetts. Behållare SAS för åtkomst till blobbarna som är associerade med importjobbet.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Krävs.** Sökvägen till loggfilen för enheten kopia (antingen utförliga loggen eller fel loggning). Filen har genererats av Windows Azure Import/Export-tjänsten och kan laddas ned från blob-lagringen som är kopplade till jobbet. Kopiera loggfilen innehåller information om misslyckade blobar eller filer som kommer att repareras.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Valfritt.** Sökväg till en textfil som kan användas för att lösa tvetydigheter om du har flera filer med samma namn som du importerar i samma jobb. Första gången verktyget körs kan det fylla i den här filen med alla tvetydig namn. Efterföljande körningar av verktyget använda den här filen för att lösa tvetydigheter.|  
  
## <a name="using-the-repairimport-command"></a>Med hjälp av kommandot RepairImport  
Om du vill reparera importera data med strömmande data över nätverket, måste du ange de kataloger som innehåller de ursprungliga filerna som du importerar med hjälp av den `/d` parametern. Du måste även ange loggfilen kopia som du laddade ned från lagringskontot. En typisk kommandoraden för att reparera ett importjobb med misslyckades delvis ser ut som:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
I följande exempel på en loggfil för kopiera var ett 64-K typ av en fil skadad på enheten som levererades för importjobbet. Eftersom detta är endast fel indikeras resten av blobarna i jobbet har importerats.  
  
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
  
När den här kopiera loggen skickas till verktyget Azure Import/Export, försöker verktyget Slutför import för den här filen genom att saknas innehållet över nätverket. I exemplet ovan, verktyget söker efter den ursprungliga filen `\animals\koala.jpg` inom de två katalogerna `C:\Users\bob\Pictures` och `X:\BobBackup\photos`. Om filen `C:\Users\bob\Pictures\animals\koala.jpg` finns verktyget Azure Import/Export kopierar en saknas uppsättning data till motsvarande blob `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Lösa konflikter när du använder RepairImport  
I vissa situationer kan verktyget inte kan hitta eller öppna filen behövs för en av följande orsaker: gick inte att hitta filen, filen är inte tillgänglig, filnamnet är tvetydigt eller innehållet i filen inte längre är korrekt.  
  
Ett tvetydigt fel kan uppstå om verktyget försöker hitta `\animals\koala.jpg` och det finns en fil med detta namn under både `C:\Users\bob\pictures` och `X:\BobBackup\photos`. Det vill säga både `C:\Users\bob\pictures\animals\koala.jpg` och `X:\BobBackup\photos\animals\koala.jpg` finns på import jobbet enheter.  
  
Den `/PathMapFile` gör det möjligt att åtgärda dessa fel. Du kan ange namnet på den fil som innehåller listan över filer som verktyget inte gick att identifiera. Följande kommandorad exempel fyller `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Verktyget skriver sedan problematiska sökvägarna till `9WM35C2V_pathmap.txt`, ett per rad. Filen kan exempelvis innehålla följande poster när du har kört kommandot:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 För varje fil i listan, bör du försöka att hitta och öppna filen för att säkerställa att den är tillgänglig för verktyget. Om du vill instruera verktyget uttryckligen var du hittar en fil kan du ändra mappningsfilen sökväg och lägga till sökvägen till varje fil på samma rad, avgränsade med ett tabbtecken:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Efter att göra de nödvändiga filerna tillgängliga för verktyget eller uppdatering av mappningsfilen sökväg, kan du köra verktyget för att slutföra importen.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera Azure Import/Export-verktyget](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
