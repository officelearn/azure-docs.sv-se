---
title: Reparera ett Azure Import/Export-exportjobb - v1 | Microsoft Docs
description: Lär dig mer om att reparera ett exportjobb som har skapats och körs med hjälp av tjänsten Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 395ff654bcacf1a4f70a9032492deb2a9d5202f3
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454767"
---
# <a name="repairing-an-export-job"></a>Reparera ett exportjobb
När en export-jobbet har slutförts kan köra du de Microsoft Azure Import/Export-verktyget lokalt till:  
  
1.  Ladda ned alla filer som Azure Import/Export-tjänsten kunde inte exportera.  
  
2.  Verifiera att filerna på enheten har exporterats korrekt.  
  
Du måste ha en anslutning till Azure Storage för att använda den här funktionen.  
  
Kommandot för att reparera ett importjobb är **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport parametrar

Följande parametrar kan bara anges med **RepairExport**:  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Krävs. Sökväg till filen reparation som spårar förloppet för reparation och gör att du kan återuppta en avbruten reparation. Varje enhet måste ha en repair-fil. När du startar en reparation för en viss enhet, ska du skicka i sökvägen till en repair-fil som inte finns ännu. Om du vill återuppta en avbruten reparation, överför du namnet på en befintlig repair-fil. Reparera filen motsvarar målenheten måste alltid anges.|  
|**/logdir: < LogDirectory\>**|Valfri. Loggkatalogen. Utförliga loggfiler ska skrivas till den här katalogen. Om inga loggkatalogen anges, används den aktuella katalogen som log-katalogen.|  
|**/d:<TargetDirectory\>**|Krävs. Katalogen för att verifiera och reparera. Detta är vanligtvis rotkatalogen för export-enhet, men kan också vara en nätverksansluten filresurs som innehåller en kopia av de exporterade filerna.|  
|**/bk:<BitLockerKey\>**|Valfri. Du bör ange BitLocker-nyckel om du vill att verktyget för att låsa upp ett krypterat där de exporterade filerna lagras.|  
|**/sn:<StorageAccountName\>**|Krävs. Namnet på lagringskontot för export-jobbet.|  
|**/sk:<StorageAccountKey\>**|**Krävs** endast om en SAS-behållare inte har angetts. Kontonyckel för lagringskontot för export-jobbet.|  
|**/csas:<ContainerSas\>**|**Krävs** endast om lagringskontonyckeln inte har angetts. Behållare SAS för åtkomst till blobbarna som är associerade med export-jobbet.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Krävs. Sökvägen till loggfilen enhet kopia. Filen har genererats av Windows Azure Import/Export-tjänsten och kan laddas ned från blob-lagringen som är kopplade till jobbet. Kopiera loggfilen innehåller information om misslyckade blobar eller filer som kommer att repareras.|  
|**/ManifestFile:<DriveManifestFile\>**|Valfri. Sökvägen till export enhetens manifestfilen. Den här filen av Windows Azure Import/Export-tjänsten och lagras på enheten för export och eventuellt i en blob i lagringskontot som är associerat med jobbet.<br /><br /> Innehållet i filerna på export enheten kontrolleras med MD5-hashar i den här filen. Alla filer som bestäms är skadade ska hämtas och har skrivits till mål-kataloger.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Använda RepairExport läge för att korrigera misslyckade export  
Du kan använda Azure Import/Export-verktyget för att hämta filer som inte gick att exportera. Kopiera loggfilen innehåller en lista över filer som inte gick att exportera.  
  
Exportera felorsakerna innefattar följande:  
  
-   Skadade enheter  
  
-   Lagringskontonyckeln som ändrats under överföringen  
  
Att köra verktyget **RepairExport** läge, måste du först ansluta den enhet som innehåller de exporterade filerna på din dator. Kör verktyget Azure Import/Export, ange sökvägen till den här enheten med den `/d` parametern. Du måste också ange sökvägen till loggfilen för enhetens kopia som du laddade ned. Följande kommandorad exemplet nedan kör verktyget för att reparera alla filer som inte kunde exportera:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Följande är ett exempel på en kopia loggfil som visas som ett block i blob inte kunde exportera:  
  
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
  
Kopiera loggfilen anger att ett fel inträffade när Windows Azure Import/Export-tjänsten har nedladdning av en av blobens block till filen på enheten för export. Andra komponenter i filen hämtats har och fillängden har angetts korrekt. Verktyget kommer i så fall öppna filen på enheten, hämta blocket från storage-kontot och skriva till filen intervallet med början från förskjutningen 65536 med längd 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Med hjälp av RepairExport för att verifiera enheten innehållet  
Du kan också använda Azure Import/Export med den **RepairExport** alternativet om du vill validera innehållet på enheten är korrekta. Manifestfilen på varje enhet för export innehåller MD5s för innehållet på enheten.  
  
Azure Import/Export-tjänsten kan också spara manifest-filer till ett lagringskonto under exporten. Manifest-filer finns tillgängliga via den [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) igen när jobbet har slutförts. Se [Import/Export service Manifest filformat](storage-import-export-file-format-metadata-and-properties.md) för mer information om formatet för en enhet manifestfil.  
  
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
  
När du har gått reparationsprocessen verktyget läst igenom varje fil som refereras i manifestfilen och verifiera filintegritet med MD5-hashar. För manifestet ovan, kommer det gå igenom följande komponenter.  

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

Alla komponenter som misslyckas verifieringen kommer hämtas av verktyget och har skrivits till samma fil på enheten.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera Azure Import/Export-verktyget](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
