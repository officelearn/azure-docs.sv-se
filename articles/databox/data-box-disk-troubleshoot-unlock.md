---
title: Felsökning av Azure Data Box-Disk disk att låsa upp problem | Microsoft Docs
description: Beskriver hur du felsöker problem i Azure Data Box-disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 02cbf64261bbfbf50561e1b7466b46b27b688e0a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148288"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Felsöka disk att låsa upp problem i Azure Data Box-Disk

Den här artikeln gäller för Microsoft Azure Data Box-Disk och beskriver de arbetsflöden som används för att felsöka eventuella problem när du använder verktyget Lås upp. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Fel med verktyget Data Box Disk Unlock


| Felmeddelande/verktygsbeteende      | Rekommendationer                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| Aktuellt .NET Framework stöds inte. De versioner som stöds är 4.5 och senare.<br><br>Verktyget avslutas med ett meddelande.  | .NET 4.5 är inte installerat. Installera .NET 4.5 eller senare på den värddator som kör upplåsningsverktyget för Data Box-disk.                                                                            |
| Det gick inte att låsa upp eller verifiera några volymer. Kontakta Microsoft-supporten.  <br><br>Verktyget misslyckas med att låsa upp eller verifiera låsta enheter. | Verktyget kunde inte låsa upp någon av de låsta enheterna med den angivna nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp.                                                |
| Följande volymer är olåsta och verifierade. <br>Enhetsbeteckningar för volymen: E:<br>Det gick inte att låsa upp alla volymer med följande nycklar: werwerqomnf, qwerwerqwdfda <br><br>Verktyget låser upp vissa enheter och visar lyckade och misslyckade enhetsbeteckningar.| Slutfördes delvis. Det gick inte att låsa upp några av enheterna med den angivna nyckeln. Kontakta Microsoft-supporten om du vill ha hjälp. |
| Det gick inte att hitta låsta volymer. Kontrollera att den disk som mottogs från Microsoft är korrekt ansluten och är låst.          | Verktyget kan inte hitta några låsta enheter. Antingen är enheterna redan upplåsta eller så identifieras de inte. Kontrollera att enheterna är anslutna och att de är låsta.                                                           |
| Allvarligt fel: Ogiltig parameter<br>Parameternamn: invalid_arg<br>ANVÄNDNING:<br>DataBoxDiskUnlock /PassKeys:<lista_över_nycklar_separerade_med_semikolon><br><br>Exempel: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Exempel: DataBoxDiskUnlock /SystemCheck<br>Exempel: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Hämta den här nyckeln från Azure DataBox diskbeställning. Nyckeln låser upp dina diskar.<br>/ Hjälp:           Det här alternativet innehåller hjälp om cmdlet-syntax och exempel.<br>/SystemCheck:    Det här alternativet kontrollerar om datorn uppfyller kraven för att köra verktyget.<br><br>Tryck på valfri tangent för att avsluta. | En ogiltig parameter har angetts. De enda tillåtna parametrarna är /SystemCheck och /PassKey/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Låsa upp problem för diskar när du använder en Windows-klient

Det här avsnittet beskriver några av de viktigaste problemen inför under distributionen av Data Box-Disk när du använder en Windows-klient för kopiering av data.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Ärende: Det gick inte att låsa upp enheten från BitLocker
 
**Orsak** 

Du har använt lösenord i BitLocker-dialogrutan och försök att låsa upp disken via BitLocker låsa upp enheter dialogrutan. Detta kan inte fungera.

**Lösning**

För att låsa upp Data Box-diskar, måste du använda verktyget Data Box Disk låsa upp och ange lösenord från Azure-portalen. Mer information finns i [Självstudie: Packa upp, Anslut och lås Azure Data Box-Disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Ärende: Det gick inte att låsa upp eller verifiera några volymer. Kontakta Microsoft-supporten.
 
**Orsak**

Du kan se följande fel i felloggen och går inte att låsa upp eller verifiera några volymer.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Detta anger att du förmodligen saknar rätt version av Windows PowerShell på Windows-klient.

**Lösning**

Du kan installera [Windows PowerShell-v 5.0](https://www.microsoft.com/download/details.aspx?id=54616) och försök igen.
 
Om du fortfarande inte kan låsa upp volymerna, kopiera loggar från mappen med verktyget Data Box Disk låsa upp och [kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöka problem med schemavalidering](data-box-disk-troubleshoot.md).
