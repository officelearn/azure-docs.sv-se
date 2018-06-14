---
title: Reparera ett Azure Import/Export-exportjobb - v1 | Microsoft Docs
description: Lär dig mer om att reparera ett exportjobb som har skapats och att köras med tjänsten Azure Import/Export.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 728e2a42-04ce-4be8-9375-e9e2bc6827a5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 57ab58fa1fd8371d0b6f019f94bb162bcc1e0e43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873879"
---
# <a name="repairing-an-export-job"></a>Reparera ett exportjobb
När ett exportjobb har slutförts kan köra du verktyget Azure Import/Export lokal till:  
  
1.  Hämta alla filer som Azure Import/Export-tjänsten kunde inte exportera.  
  
2.  Verifiera att filerna på enheten exporterats korrekt.  
  
Du måste ha en anslutning till Azure Storage att använda den här funktionen.  
  
Kommandot för att reparera ett importjobb är **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport parametrar

Följande parametrar kan anges med **RepairExport**:  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|**/ r: < RepairFile\>**|Krävs. Sökvägen till filen reparation, som spårar förloppet för att reparera och gör att du kan återuppta en avbruten reparation. Varje enhet måste ha en repair-fil. När du startar en reparation för en viss enhet skickar du i sökvägen till en repair-fil som ännu inte finns. Om du vill återuppta ett avbryts reparation, överför du namnet på en befintlig fil för reparation. Reparera filen motsvarar målenheten måste alltid anges.|  
|**/logdir: < LogDirectory\>**|Valfri. Loggkatalogen. Utförlig loggfilerna skrivs till den här katalogen. Om inga loggkatalogen anges, används den aktuella katalogen som loggkatalogen.|  
|**/ d: < TargetDirectory\>**|Krävs. Katalogen för att kontrollera och reparera. Detta är vanligtvis rotkatalogen för export-enhet, men kan också vara en nätverksresurs som innehåller en kopia av de exporterade filerna.|  
|**/BK: < BitLockerKey\>**|Valfri. Du bör ange BitLocker-nyckel om du vill använda verktyget för att låsa upp ett krypterat där de exporterade filerna lagras.|  
|**/SN: < StorageAccountName\>**|Krävs. Namnet på lagringskontot för exportjobbet.|  
|**/Sk: < StorageAccountKey\>**|**Krävs** endast om en behållare SAS inte har angetts. Kontonyckel för lagringskontot för exportjobbet.|  
|**/csas: < ContainerSas\>**|**Krävs** om lagringskontots åtkomstnyckel inte har angetts. Behållaren SAS för att komma åt blobar som är kopplad till exportjobbet.|  
|**/ CopyLogFile: < DriveCopyLogFile\>**|Krävs. Sökvägen till loggfilen enhet kopia. Filen har genererats av Windows Azure Import/Export-tjänsten och kan hämtas från blob storage som är associerat med jobbet. Kopiera loggfilen innehåller information om misslyckade blobbar eller filer som är repareras.|  
|**/ ManifestFile: < DriveManifestFile\>**|Valfri. Sökvägen till enheten export manifestfilen. Den här filen skapas av tjänsten Windows Azure Import/Export och lagras på enheten för export och eventuellt i en blob i storage-konto som är associerat med jobbet.<br /><br /> Innehållet i filerna på enhetens export kontrolleras med MD5 hash-värden i den här filen. Alla filer som bedöms vara skadad ska hämtas och anges till mål-kataloger.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Med RepairExport läge för att korrigera misslyckade export  
Du kan använda verktyget Azure Import/Export för att hämta filer som inte gick att exportera. Kopiera filen innehåller en lista över filer som inte gick att exportera.  
  
Exportera orsakerna är följande möjligheter:  
  
-   Skadade enheter  
  
-   Lagringskontonyckel som ändrats under överföringen  
  
Du kan köra verktyget **RepairExport** läge, måste du först ansluta enheten som innehåller de exporterade filerna till datorn. Kör verktyget Azure Import/Export, ange sökvägen till den här enheten med den `/d` parameter. Du måste ange sökvägen till loggfilen för enhetens kopia som du hämtat. Följande kommandorad exemplet nedan körs verktyget för att reparera alla filer som kunde inte exportera:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Följande är ett exempel på en kopia loggfil som visar att ett block i blob kunde inte exportera:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Kopiera filen anger att ett fel uppstod när tjänsten Windows Azure Import/Export var hämta någon av de blob-block till filen på enheten för export. Andra komponenter i filen har hämtats och fillängden angavs korrekt. Verktyget kommer i så fall öppna filen på enheten, hämta blocket från lagringskontot och skriva till filen intervallet från förskjutningen 65536 med längden 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Med hjälp av RepairExport för att validera enhet innehållet  
Du kan också använda Azure Import/Export med den **RepairExport** alternativet om du vill validera innehållet på enheten är korrekta. Manifestfilen på varje enhet export innehåller MD5s för innehållet på enheten.  
  
Tjänsten Azure Import/Export kan också spara manifest-filer till ett lagringskonto under exporten. Manifest-filer finns tillgängliga via den [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen när jobbet har slutförts. Se [Import/Export service Manifest filformatet](storage-import-export-file-format-metadata-and-properties.md) för mer information om formatet för en enhet manifestfil.  
  
I följande exempel visas hur du kör verktyget Azure Import/Export med den **/ManifestFile** och **/CopyLogFile** parametrar:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Följande är ett exempel på en manifestfil:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
När reparationen är klar, kommer verktyget att läsa igenom varje fil som refereras i manifestfilen och kontrollera filintegritet med MD5 hash-värden. Det ska gå igenom följande komponenter för manifestet ovan.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Alla komponenter misslyckas verifieringen kommer hämtas av verktyget och anges till samma fil på enheten.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget Azure Import/Export](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
