---
title: Felsökning av problem med azure data boxdisksã¶kning av datakopiering| Microsoft-dokument
description: Beskriver felsöka problem som visas under datakopiering i Azure Data Box Disk med hjälp av loggar.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148353"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Felsöka problem med datakopiering i Azure Data Box Disk

Den här artikeln gäller Microsoft Azure Data Box Disk och beskriver hur du felsöker eventuella problem som visas när du kopierar data till diskar. Artikeln täcker också problem när du använder verktyget för delad kopia.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problem med datakopiering när du använder ett Linux-system

I det här avsnittet beskrivs några av de vanligaste problemen när du använder en Linux-klient för att kopiera data till diskar.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Problem: Enheten monteras som skrivskyddad
 
**Orsak** 

Detta kan bero på ett orent filsystem.

Att återmontera en enhet som läs-skriv fungerar inte med Data Box Diskar. Det här scenariot stöds inte med enheter dekrypteras av dislocker. Du kan ha återmonterat enheten med följande kommando:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Även om återmonteringen lyckades kommer data inte att kvarstå.

**Upplösning**

Gör så här i ditt Linux-system:

1. Installera `ntfsprogs` paketet för ntfsfix-verktyget.
2. Avmontera monteringspunkterna som enheten har med verktyget. Antalet monteringspunkter varierar för enheter.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Kör `ntfsfix` på motsvarande bana. Det markerade talet ska vara samma som steg 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Kör följande kommando för att ta bort vilolägesmetadata som kan orsaka monteringsproblemet.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Gör en ren avmontering.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Gör en ren låsa upp och montera.
7. Testa monteringspunkten genom att skriva en fil.
8. Avmontera och montera om för att validera filens persistens.
9. Fortsätt med datakopian.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Problem: Fel med data som inte kvarstår efter kopian
 
**Orsak** 

Om du ser att enheten inte har data efter att den har avmonterats (även om data kopierades till den), är det möjligt att du har monterat om en enhet som läs-skriv efter att enheten har monterats som skrivskyddad.

**Upplösning**
 
Om så är fallet, se upplösningen för [enheter som monteras som skrivskyddade](#issue-drive-getting-mounted-as-read-only).

Om så inte var fallet kopierar du loggarna från mappen som har verktyget Diskupplåsning av databox och [kontaktar Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Fel med verktyget Data Box Disk Split Copy

De problem som visas när du använder ett verktyg för delad kopia för att dela upp data över flera diskar sammanfattas i följande tabell.

|Felmeddelande/varningar |Rekommendationer |
|---------|---------|
|- Jag vet inte vad du säger. Hämtar BitLocker-lösenord för volym: m <br>[Fel] Undantag som fångats när BitLocker-tangenten hämtas för volym m:<br> Sekvensen innehåller inga element.|Det här felet utlöses om Data Box-måldisken är offline. <br> Använd verktyget `diskmgmt.msc` till onlinediskar.|
|[Fel] Ett undantag utlöstes: WMI-åtgärden misslyckades:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message = Formatet för det angivna återställningslösenordet är ogiltigt. <br>BitLocker-återställningslösenord är 48 siffror. <br>Kontrollera att återställningslösenordet har rätt format och försök sedan igen.|Använda verktyget Data Box Disk Unlock för att först låsa upp diskarna och prova kommandot igen. Mer information finns i <li> [Låsa upp Data Box-disk för Windows-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Låsa upp Data Box-disk för Linux-klienter](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Fel] Ett undantag utlöstes: En DriveManifest.xml-fil finns på målenheten. <br> Detta anger att målenheten kanske har förberetts med en annan journalfil. <br>För att lägga till mer data till samma enhet använder du föregående journalfil. Om du vill ta bort befintliga data och återanvända målenheten för ett nytt importjobb tar du bort *DriveManifest.xml* på enheten. Kör det här kommandot igen med en ny journalfil.| Det här felet tas emot när du försöker använda samma uppsättning enheter för flera importsessioner. <br> Använda endast en uppsättning enheter för endast en delnings- och kopieringssession.|
|[Fel] Ett undantag utlöstes: CopySessionId importdata-sept-test-1 refererar till en tidigare kopieringssession och kan inte återanvändas för en ny kopieringssession.|Det här felet rapporteras när du försöker använda samma jobbnamn för ett nytt jobb som använts för ett tidigare slutfört jobb.<br> Tilldela det nya jobbet ett unikt namn.|
|[Info] Målfilens eller katalogens namn överskrider NTFS-längdbegränsningen. |Det här meddelandet rapporteras när målfilen bytte namn på grund av en lång filsökväg.<br> Ändra dispositionsalternativet i `config.json`-filen för att styra det här beteendet.|
|[Fel] Ett undantag utlöses: Felaktig JSON-escape-sekvens. |Det här meddelandet rapporteras när config.json har ett format som inte är giltigt. <br> Verifiera din `config.json` med hjälp av [JSONlint](https://jsonlint.com/) innan du sparar filen.|


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker problem med valideringsverktyg](data-box-disk-troubleshoot.md).
