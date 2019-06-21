---
title: Azure Data Box-Disk felsökning datakopiering problem | Microsoft Docs
description: Beskriver hur du felsöker problem som kan uppstå vid datakopiering av i Azure Data Box-Disk med hjälp av loggar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148353"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Felsökning av problem för kopia av data i Azure Data Box-Disk

Den här artikeln gäller för Microsoft Azure Data Box-Disk och beskriver hur du felsöker eventuella problem som du ser när du kopierar data till diskarna. Artikeln behandlar också problem när du använder verktyget Dela kopia.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Kopiera data problem när du använder en Linux-system

Det här avsnittet beskriver några av de viktigaste problemen kan stöta på när du använder en Linux-klient för att kopiera data till diskarna.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Ärende: Disken är komma monterad som skrivskyddad
 
**Orsak** 

Detta kan bero på en felaktig filsystem.

Ommontering en enhet som Läs-och fungerar inte med Data Box-diskar. Det här scenariot stöds inte med enheter dekrypteras av dislocker. Du kanske har har monteras enheten med följande kommando:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Även om den ommontering lyckades, behålls inte data.

**Lösning**

Gör följande på din Linux-dator:

1. Installera den `ntfsprogs` paketet för verktyget ntfsfix.
2. Demontera monteringspunkterna från verktyget upplåsning av enheten. Antal monteringspunkter varierar för enheter.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Kör `ntfsfix` på motsvarande sökväg. Det markerade talet får vara samma som steg 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Kör följande kommando för att ta bort viloläge metadata som kan orsaka problem för montering.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Gör en ren demonteringen.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Gör en ren upplåsning och montera.
7. Testa monteringspunkten genom att skriva en fil.
8. Demontera och återmontera för att verifiera fil-persistence.
9. Fortsätt med Datakopieringen.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Ärende: Fel med data som inte spara efter kopiering
 
**Orsak** 

Om du ser att enheten inte har data när den var demontera (även om data har kopierats till det), så är det möjligt att du monteras på en enhet som Läs-och när enheten har monterats som skrivskyddade.

**Lösning**
 
Om så är fallet, se lösningen på [enheter komma monterats som skrivskyddade](#issue-drive-getting-mounted-as-read-only).

Om det inte är fallet, kopiera loggarna från mappen med verktyget Data Box Disk låsa upp och [kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Fel med verktyget Data Box Disk Split Copy

De problem som kan uppstå när du använder en delad kopia för att dela upp data över flera diskar sammanfattas i tabellen nedan.

|Felmeddelande/varningar |Rekommendationer |
|---------|---------|
|[Information] Hämta BitLocker-lösenordet för volymen: m <br>[Fel] Undantag inträffade vid hämtning av BitLocker-nyckel för volymen m:<br> Sekvensen innehåller inga element.|Det här felet utlöses om Data Box-måldisken är offline. <br> Använd verktyget `diskmgmt.msc` till onlinediskar.|
|[Fel] Ett undantag uppstod: WMI-åtgärden misslyckades:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = Formatet för det angivna återställningslösenordet är ogiltigt. <br>BitLocker-återställningslösenord är 48 siffror. <br>Kontrollera att återställningslösenordet har rätt format och försök sedan igen.|Använda verktyget Data Box Disk Unlock för att först låsa upp diskarna och prova kommandot igen. Mer information finns i <li> [Låsa upp Data Box-disk för Windows-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Låsa upp Data Box-disk för Linux-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Fel] Ett undantag uppstod: Det finns en DriveManifest.xml-fil på målenheten. <br> Detta anger att målenheten kanske har förberetts med en annan journalfil. <br>För att lägga till mer data till samma enhet använder du föregående journalfil. Om du vill ta bort befintliga data och återanvända målenheten för en ny importjobb, ta bort den *DriveManifest.xml* på enheten. Kör det här kommandot igen med en ny journalfil.| Det här felet tas emot när du försöker använda samma uppsättning enheter för flera importsessioner. <br> Använda endast en uppsättning enheter för endast en delnings- och kopieringssession.|
|[Fel] Ett undantag uppstod: CopySessionId importdata-sept-test-1 refererar till en tidigare session för kopiering och inte kan återanvändas för en ny session för kopiering.|Det här felet rapporteras när du försöker använda samma jobbnamn för ett nytt jobb som använts för ett tidigare slutfört jobb.<br> Tilldela det nya jobbet ett unikt namn.|
|[Info] Målfilens eller katalogens namn överskrider NTFS-längdbegränsningen. |Det här meddelandet rapporteras när målfilen bytte namn på grund av en lång filsökväg.<br> Ändra dispositionsalternativet i `config.json`-filen för att styra det här beteendet.|
|[Fel] Ett undantag uppstod: Felaktiga JSON-escape-sekvensen. |Det här meddelandet rapporteras när config.json har ett format som inte är giltigt. <br> Verifiera din `config.json` med hjälp av [JSONlint](https://jsonlint.com/) innan du sparar filen.|


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöka problem med schemavalidering verktyget](data-box-disk-troubleshoot.md).
