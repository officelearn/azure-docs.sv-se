---
title: Montera en Azure-filresurs via SMB med macOS | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresurs via SMB med macOS.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 7f2abbb355513c175329d01b9d5ed8884f2c27ef
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763549"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Montera en Azure-filresurs via SMB med macOS
[Azure Files](storage-files-introduction.md) är Microsofts lättanvända filsystem i molnet. Azure-filresurser kan monteras med SMB-3 protokollet, som är branschstandard, av macOS El Capitan 10.11+. Den här artikeln visar två olika sätt att montera en Azure-filresurs på macOS: med hjälp av användargränssnittet Finder och med terminalen.

> [!Note]  
> Innan du monterar en Azure-filresurs via SMB rekommenderar vi att du inaktiverar signering av SMB-paket. Om du inte gör det kan det orsaka sämre prestanda när du får åtkomst till Azure-filresursen från macOS. SMB-anslutningen krypteras så att det inte påverkar din anslutnings säkerhet. Från terminalen används följande kommandon för att inaktivera signering av SMB-paket, vilket beskrivs i den här [Apple Support-artikeln om hur du inaktiverar signering av SMB-paket](https://support.apple.com/HT205926):  
>    ```
>    sudo -s
>    echo "[default]" >> /etc/nsmb.conf
>    echo "signing_required=no" >> /etc/nsmb.conf
>    exit
>    ```

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Krav för att montera en Azure-filresurs på macOS
* **Lagringskontonamn**: Om du vill montera en Azure-filresurs behöver du namnet på lagringskontot.

* **Lagringskontonyckel**: Om du vill montera en Azure-filresurs behöver du den primära (eller sekundära) lagringsnyckeln. SAS-nycklar stöds inte för montering.

* **Se till att port 445 är öppen**: SMB kommunicerar via TCP-port 445. Kontrollera att brandväggen på klientdatorn (Mac) inte blockerar TCP-port 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montera en Azure-filresurs via Finder
1. **Öppna Finder**: Som standard är Finder redan öppet på macOS, men du kan kontrollera att det är det för tillfället valda programmet genom att klicka på ”ikonen med macOS-ansiktet” i Dock:  
    ![ikonen med macOS-ansiktet](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Välj ”Anslut till Server” från menyn ”Gå”**: Med hjälp av UNC-sökvägen från kraven konverterar du de första dubbla omvända snedstrecken (`\\`) till `smb://` och alla andra omvända snedstreck (`\`) till snedstreck (`/`). Din länk bör se ut så här: ![Dialogrutan ”Anslut till server”](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Använd lagringskontonamnet och lagringskontonyckeln när du tillfrågas om användarnamn och lösenord**: När du klickar på ”Anslut” i dialogrutan ”Anslut till server” uppmanas du att ange ett användarnamn och lösenord (det kommer att fyllas i automatiskt med ditt macOS-användarnamn). Du har möjlighet att lägga till ditt lagringskontonamnet/lagringskontonyckel i din nyckelring för macOS.

4. **Använd Azure-filresursen enligt önskemål**: När du har ersatt resursnamnet och lagringskontonyckeln med användarnamnet och lösenordet monteras resursen. Du kan använda det här på samma sätt som du vanligtvis använder en lokal mapp/filresurs, inklusive att dra och släppa filer till filresursen:

    ![En ögonblicksbild av en monterad Azure-filresurs](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montera en Azure-filresurs via Terminal
1. Ersätt `<storage-account-name>` med namnet på ditt lagringskonto. Ange lagringskontonyckeln som lösenord när du uppmanas att ange lösenord. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Använd Azure-filresursen enligt önskemål**: Azure-filresursen monteras på den monteringspunkt som angavs av det föregående kommandot.  

    ![En ögonblicksbild av den monterade Azure-filresursen](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar.

* [Apple Support-artikel – Så här ansluter du med fildelning på din Mac.](https://support.apple.com/HT204445)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning i Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Felsökning i Linux](storage-troubleshoot-linux-file-connection-problems.md)    
