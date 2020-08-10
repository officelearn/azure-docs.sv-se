---
title: Montera en Azure-filresurs via SMB med macOS | Microsoft Docs
description: Lär dig hur du monterar en Azure-filresurs via SMB med macOS med hjälp av Finder eller Terminal. Azure Files är Microsofts lättanvända filsystem i molnet.
author: RenaShahMSFT
ms.service: storage
ms.topic: how-to
ms.date: 09/19/2017
ms.author: renash
ms.subservice: files
ms.openlocfilehash: 2cddf8a7d3dbc7abcc25fb76aba8a0af1790fe4d
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88034455"
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
* **Lagrings konto namn**: om du vill montera en Azure-filresurs behöver du namnet på lagrings kontot.

* **Lagrings konto nyckel**: om du vill montera en Azure-filresurs behöver du den primära (eller sekundära) lagrings nyckeln. SAS-nycklar stöds inte för montering.

* **Kontrollera att port 445 är öppen**: SMB kommunicerar via TCP-port 445. Kontrollera att brandväggen på klientdatorn (Mac) inte blockerar TCP-port 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Montera en Azure-filresurs via Finder
1. **Öppna Finder**: Som standard är Finder redan öppet på macOS, men du kan kontrollera att det är det för tillfället markerade programmet genom att klicka på "ikonen med macOS-ansiktet" i Dock:  
    ![ikonen med macOS-ansiktet](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Välj "Anslut till Server" från "gå till"-menyn**: Använd UNC-sökvägen från kraven, konvertera det inledande dubbla omvända snedstrecket ( `\\` ) till `smb://` och alla andra omvänt snedstreck ( `\` ) för att vidarebefordra snedstreck ( `/` ). Länken bör se ut ungefär så här: ![Dialogrutan "Anslut till server"](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Använd lagringskontonamnet och lagringskontonyckeln när du tillfrågas om användarnamn och lösenord**: När du klickar på "Anslut" i dialogrutan "Anslut till server" uppmanas du att ange ett användarnamn och lösenord (det kommer att vara ifyllt automatiskt med ditt macOS-användarnamn). Du har möjlighet att lägga till ditt lagringskontonamnet/lagringskontonyckel i din nyckelring för macOS.

4. **Använd Azure-filresursen som du vill**: efter att du har bytt resurs namnet och lagrings konto nyckeln i för användar namnet och lösen ordet monteras resursen. Du kan använda det här på samma sätt som du vanligtvis använder en lokal mapp/filresurs, inklusive att dra och släppa filer till filresursen:

    ![En ögonblicksbild av en monterad Azure-filresurs](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Montera en Azure-filresurs via Terminal
1. Ersätt `<storage-account-name>` med namnet på ditt lagringskonto. Ange lagringskontonyckeln som lösenord när du uppmanas att ange lösenord. 

    ```
    mount_smbfs //<storage-account-name>@<storage-account-name>.file.core.windows.net/<share-name> <desired-mount-point>
    ```

2. **Använd Azure-filresursen som du vill**: Azure-filresursen monteras på den monterings punkt som anges av föregående kommando.  

    ![En ögonblicksbild av den monterade Azure-filresursen](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Files finns på följande länkar.

* [Apple Support-artikel – Så här ansluter du med fildelning på din Mac.](https://support.apple.com/HT204445)
* [Vanliga frågor och svar](../storage-files-faq.md)
* [Felsökning i Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Felsökning i Linux](storage-troubleshoot-linux-file-connection-problems.md)    
