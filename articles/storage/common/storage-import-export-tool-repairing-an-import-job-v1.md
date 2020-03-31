---
title: Reparera ett importjobb för Import/export i Azure - v1 | Microsoft-dokument
description: Lär dig hur du reparerar ett importjobb som har skapats och körts med hjälp av Azure Import/Export-tjänsten.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973891"
---
# <a name="repairing-an-import-job"></a>Reparera ett importjobb
Microsoft Azure Import/Export-tjänsten kan misslyckas med att kopiera vissa av dina filer eller delar av en fil till Windows Azure Blob-tjänsten. Några orsaker till fel är:  
  
-   Skadade filer  
  
-   Skadade enheter  
  
-   Lagringskontonyckeln ändrades medan filen överfördes.  
  
Du kan köra Microsoft Azure Import/Export Tool med importjobbets kopieringsloggfiler och verktyget överför de filer som saknas (eller delar av en fil) till ditt Windows Azure-lagringskonto för att slutföra importjobbet.  
  
## <a name="repairimport-parameters"></a>Parametrar för RepairImport

Följande parametrar kan anges med **RepairImport:** 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Krävs.** Sökväg till reparationsfilen, som spårar förloppet för reparationen, och låter dig återuppta en avbruten reparation. Varje enhet måste ha en och endast en reparationsfil. När du startar en reparation för en viss enhet, skicka in sökvägen till en reparationsfil, som ännu inte finns. Om du vill återuppta en avbruten reparation bör du skicka in namnet på en befintlig reparationsfil. Reparationsfilen som motsvarar målenheten måste alltid anges.|  
|**/logdir:**<LogDirectory\>|**Valfri.** Loggkatalogen. Utförliga loggfiler skrivs till den här katalogen. Om ingen loggkatalog har angetts används den aktuella katalogen som loggkatalog.|  
|**/d:**<TargetDirectories\>|**Krävs.** En eller flera semikolonavgränsade kataloger som innehåller de ursprungliga filerna som importerades. Importenheten kan också användas, men krävs inte om alternativa platser för originalfiler är tillgängliga.|  
|**/bk:**<BitLockerKey\>|**Valfri.** Du bör ange BitLocker-tangenten om du vill att verktyget ska låsa upp en krypterad enhet där originalfilerna är tillgängliga.|  
|**/sn:**<StorageAccountName\>|**Krävs.** Namnet på lagringskontot för importjobbet.|  
|**/sk:**<StorageAccountKey\>|**Krävs** om och endast om en behållar SAS inte har angetts. Kontonyckeln för lagringskontot för importjobbet.|  
|**/csas:**<ContainerSas\>|**Krävs** om och endast om lagringskontonyckeln inte har angetts. Behållar-SAS för åtkomst till blobbar som är associerade med importjobbet.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Krävs.** Sökväg till enhetens kopieringsloggfil (antingen utförlig logg eller fellogg). Filen genereras av Tjänsten Windows Azure Import/Export och kan hämtas från blob-lagringen som är associerad med jobbet. Kopieringsloggfilen innehåller information om misslyckade blobbar eller filer som ska repareras.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Valfri.** Sökväg till en textfil som kan användas för att lösa tvetydigheter om du har flera filer med samma namn som du importerade i samma jobb. Första gången verktyget körs kan det fylla den här filen med alla tvetydiga namn. Efterföljande körningar av verktyget använder den här filen för att lösa tvetydigheterna.|  
  
## <a name="using-the-repairimport-command"></a>Använda kommandot RepairImport  
Om du vill reparera importdata genom att strömma data över nätverket måste du ange `/d` vilka kataloger som innehåller de ursprungliga filerna som du importerade med parametern. Du måste också ange kopieringsloggfilen som du hämtade från ditt lagringskonto. En typisk kommandorad för att reparera ett importjobb med partiella fel ser ut:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
I följande exempel på en kopieringsloggfil har en 64 K-bit av en fil skadats på den enhet som levererades för importjobbet. Eftersom detta är det enda fel som anges har resten av blobbar i jobbet importerats.  
  
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
  
När den här kopieringsloggen skickas till Azure Import/Export Tool försöker verktyget slutföra importen för den här filen genom att kopiera det innehåll som saknas i nätverket. I exemplet ovan letar verktyget efter `\animals\koala.jpg` originalfilen i `C:\Users\bob\Pictures` de `X:\BobBackup\photos`två katalogerna och . Om filen `C:\Users\bob\Pictures\animals\koala.jpg` finns kopieras det saknade dataintervallet som saknas `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`till motsvarande blob .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Lösa konflikter vid användning av RepairImport  
I vissa situationer kanske verktyget inte kan hitta eller öppna den nödvändiga filen av något av följande skäl: det gick inte att hitta filen, filen är inte tillgänglig, filnamnet är tvetydigt eller innehållet i filen är inte längre korrekt.  
  
Ett tvetydigt fel kan uppstå om `\animals\koala.jpg` verktyget försöker hitta och det `C:\Users\bob\pictures` `X:\BobBackup\photos`finns en fil med det namnet under båda och . Det vill `C:\Users\bob\pictures\animals\koala.jpg` vara, både och `X:\BobBackup\photos\animals\koala.jpg` finns på importjobbenheterna.  
  
Med `/PathMapFile` alternativet kan du lösa dessa fel. Du kan ange namnet på filen, som innehåller en lista över filer som verktyget inte kunde identifiera korrekt. Följande kommandoradsexempel `9WM35C2V_pathmap.txt`fyller i:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Verktyget kommer då att skriva problematiska filsökvägar till `9WM35C2V_pathmap.txt`, en på varje rad. Filen kan till exempel innehålla följande poster när du har kört kommandot:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 För varje fil i listan bör du försöka hitta och öppna filen för att säkerställa att den är tillgänglig för verktyget. Om du uttryckligen vill tala om för verktyget var du kan hitta en fil kan du ändra sökvägsmappningsfilen och lägga till sökvägen till varje fil på samma rad, avgränsad med ett fliktecken:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
När du har gjort nödvändiga filer tillgängliga för verktyget eller uppdaterat sökvägsmappningsfilen kan du köra verktyget igen för att slutföra importen.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget för import/export av Azure](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett exportjobb](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
