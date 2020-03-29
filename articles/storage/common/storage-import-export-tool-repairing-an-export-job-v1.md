---
title: Reparera ett Azure Import/Export-exportjobb – v1 | Microsoft-dokument
description: Lär dig hur du reparerar ett exportjobb som har skapats och körts med hjälp av Azure Import/Export-tjänsten.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b2ba30bddfc6364c79e1bb01d30cde63b261a07f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74978023"
---
# <a name="repairing-an-export-job"></a>Reparera ett exportjobb
När ett exportjobb har slutförts kan du köra Microsoft Azure Import/Export Tool lokalt för att:  
  
1.  Hämta alla filer som azure import/export-tjänsten inte kunde exportera.  
  
2.  Kontrollera att filerna på enheten har exporterats korrekt.  
  
Du måste ha anslutning till Azure Storage för att kunna använda den här funktionen.  
  
Kommandot för att reparera ett importjobb är **RepairExport**.

## <a name="repairexport-parameters"></a>Parametrar för RepairExport

Följande parametrar kan anges med **RepairExport:**  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Krävs. Sökväg till reparationsfilen, som spårar förloppet för reparationen, och låter dig återuppta en avbruten reparation. Varje enhet måste ha en och endast en reparationsfil. När du startar en reparation för en viss enhet, kommer du att passera i sökvägen till en reparationsfil som ännu inte finns. Om du vill återuppta en avbruten reparation bör du skicka in namnet på en befintlig reparationsfil. Reparationsfilen som motsvarar målenheten måste alltid anges.|  
|**/logdir:<LogDirectory\>**|Valfri. Loggkatalogen. Utförliga loggfiler kommer att skrivas till den här katalogen. Om ingen loggkatalog har angetts används den aktuella katalogen som loggkatalog.|  
|**/d:<TargetDirectory\>**|Krävs. Katalogen för att validera och reparera. Detta är vanligtvis rotkatalogen för exportenheten, men kan också vara en nätverksfilresurs som innehåller en kopia av de exporterade filerna.|  
|**/bk:<BitLockerKey\>**|Valfri. Du bör ange BitLocker-tangenten om du vill att verktyget ska låsa upp en krypterad plats där de exporterade filerna lagras.|  
|**/sn:<StorageAccountName\>**|Krävs. Namnet på lagringskontot för exportjobbet.|  
|**/sk:<StorageAccountKey\>**|**Krävs** om och endast om en behållar SAS inte har angetts. Kontonyckeln för lagringskontot för exportjobbet.|  
|**/csas:<ContainerSas\>**|**Krävs** om och endast om lagringskontonyckeln inte har angetts. Behållar-SAS för åtkomst till blobbar som är associerade med exportjobbet.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Krävs. Sökvägen till loggfilen för enhetskopiering. Filen genereras av Tjänsten Windows Azure Import/Export och kan hämtas från blob-lagringen som är associerad med jobbet. Kopieringsloggfilen innehåller information om misslyckade blobbar eller filer som ska repareras.|  
|**/ManifestFile:<DriveManifestFile\>**|Valfri. Sökvägen till exportenhetens manifestfil. Den här filen genereras av Tjänsten Windows Azure Import/Export och lagras på exportenheten, och eventuellt i en blob i lagringskontot som är associerat med jobbet.<br /><br /> Innehållet i filerna på exportenheten verifieras med MD5-hasharen i den här filen. Alla filer som är fast beslutna att vara skadade hämtas och skrivas om till målkatalogerna.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Använda RepairExport-läge för att korrigera misslyckade exporter  
Du kan använda Azure Import/Export Tool för att hämta filer som inte kunde exporteras. Kopieringsloggfilen innehåller en lista över filer som inte kunde exporteras.  
  
Orsakerna till exportfel är följande möjligheter:  
  
-   Skadade enheter  
  
-   Lagringskontonyckeln ändrades under överföringsprocessen  
  
Om du vill köra verktyget i **RepairExport-läge** måste du först ansluta enheten som innehåller de exporterade filerna till datorn. Kör sedan Verktyget för import/export av Azure och ange `/d` sökvägen till den enheten med parametern. Du måste också ange sökvägen till enhetens kopieringsloggfil som du hämtade. Följande kommandoradsexempel nedan kör verktyget för att reparera filer som inte kunde exporteras:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Följande är ett exempel på en kopieringsloggfil som visar att ett block i bloben inte kunde exportera:  
  
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
  
Kopieringsloggfilen anger att ett fel uppstod när Windows Azure Import/Export-tjänsten hämtade ett av blob-blocken till filen på exportenheten. De andra komponenterna i filen har hämtats och fillängden har angetts korrekt. I det här fallet öppnar verktyget filen på enheten, hämtar blocket från lagringskontot och skriver det till filområdet från offset 65536 med längden 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Använda RepairExport för att validera enhetsinnehåll  
Du kan också använda Azure Import/Export med alternativet **RepairExport** för att verifiera att innehållet på enheten är korrekt. Manifestfilen på varje exportenhet innehåller MD5:or för enhetens innehåll.  
  
Azure Import/Export-tjänsten kan också spara manifestfilerna i ett lagringskonto under exportprocessen. Platsen för manifestfilerna är tillgänglig via åtgärden [Hämta jobb](/rest/api/storageimportexport/jobs) när jobbet har slutförts. Mer information om formatet för en enhetsmanifestfil finns i [Manifestformatet för import/export.](storage-import-export-file-format-metadata-and-properties.md)  
  
I följande exempel visas hur du kör Azure Import/Export Tool med parametrarna **/ManifestFile** och **/CopyLogFile:**  
  
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
  
När reparationsprocessen är klar läser verktyget igenom varje fil som refereras i manifestfilen och verifierar filens integritet med MD5-hashar. För manifestet ovan kommer det att gå igenom följande komponenter.  

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

Alla komponenter som inte uppfyller verifieringen hämtas av verktyget och skrivs om till samma fil på enheten.  
  
## <a name="next-steps"></a>Nästa steg
 
* [Konfigurera verktyget för import/export av Azure](storage-import-export-tool-setup-v1.md)   
* [Förbereda hårddiskar för ett importjobb](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Granska jobbstatus med kopiera loggfiler](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparera ett importjobb](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Felsökning av Azures import-/exportverktyg](storage-import-export-tool-troubleshooting-v1.md)
