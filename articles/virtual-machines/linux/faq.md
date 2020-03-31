---
title: Vanliga frågor och svar för virtuella Linux-datorer i Azure
description: Ger svar på några av de vanligaste frågorna om virtuella Linux-datorer som skapats med Resource Manager-modellen.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 3c6a5e011a536cc9c34565d4f72a9bee6c6a5254
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945171"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Vanliga frågor om Virtuella Linux-maskiner
Den här artikeln innehåller några vanliga frågor om virtuella Linux-datorer som skapats i Azure med hjälp av distributionsmodellen för Resource Manager. Windows-versionen av det här avsnittet finns [i Vanliga frågor och frågor om virtuella datorer i Windows](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Mer information finns i [Linux på Azure-signerade distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 32 767 GiB. Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Managed Disks är de rekommenderade disklagringserbjudandena för användning med Virtuella Azure-datorer för beständig lagring av data. Du kan använda flera hanterade diskar med varje virtuell dator. Hanterade diskar erbjuder två typer av hållbara lagringsalternativ: Premium och Standard Hanterade diskar. Prisinformation finns i [Priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks).

Azure-lagringskonton kan också tillhandahålla lagring för operativsystemdisken och alla datadiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur kommer jag åt min virtuella dator?
Upprätta en fjärranslutning för att logga in på den virtuella datorn med Secure Shell (SSH). Se instruktionerna om hur du ansluter [från Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller från Linux [och Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Som standard tillåter SSH högst 10 samtidiga anslutningar. Du kan öka detta antal genom att redigera konfigurationsfilen.

Om du har problem kan du [läsa SSH-anslutningar (Troubleshoot Secure Shell).](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan jag använda den temporära disken (/dev/sdb1) för att lagra data?
Använd inte den temporära disken (/dev/sdb1) för att lagra data. Det är bara där för tillfällig förvaring. Du riskerar att förlora data som inte kan återställas.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig Azure VM?
Ja. Instruktioner finns i [Så här skapar du en kopia av en virtuell Linux-dator i resurshanterarens distributionsmodell](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför ser jag inte regioner i Östra Kanada och Kanada via Azure Resource Manager?
De två nya regionerna i Canada Central och Canada East registreras inte automatiskt för att skapa virtuella datorer för befintliga Azure-prenumerationer. Den här registreringen görs automatiskt när en virtuell dator distribueras via Azure-portalen till någon annan region med Hjälp av Azure Resource Manager. När en virtuell dator har distribuerats till någon annan Azure-region bör de nya regionerna vara tillgängliga för efterföljande virtuella datorer.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort i min virtuella dator när det har skapats?
Ja, det är nu möjligt. Den virtuella datorn måste först stoppas deallocated. Sedan kan du lägga till eller ta bort ett nätverkskort (såvida det inte är det sista nätverkskortet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några krav på datornamn?
Ja. Datornamnet kan vara högst 64 tecken långt. Se [Namngivningskonventioner regler och begränsningar](/azure/architecture/best-practices/resource-naming) för mer information om namngivning av dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det några resursgruppsnamnskrav?
Ja. Resursgruppsnamnet kan vara högst 90 tecken långt. Se [Namngivningskonventioner regler och begränsningar](/azure/architecture/best-practices/resource-naming) för mer information om resursgrupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är användarnamnskraven när du skapar en virtuell dator?

Användarnamn bör vara 1 - 32 tecken i längd.

Följande användarnamn är inte tillåtna:

| | | | |
|-----------------|-----------|--------------------|----------|
| `administrator` | `admin`   | `user`             | `user1`  |
| `test`          | `user2`   | `test1`            | `user3`  |
| `admin1`        | `1`       | `123`              | `a`      |
| `actuser`       | `adm`     | `admin2`           | `aspnet` |
| `backup`        | `console` | `david`            | `guest`  |
| `john`          | `owner`   | `root`             | `server` |
| `sql`           | `support` | `support_388945a0` | `sys`    |
| `test2`         | `test3`   | `user4`            | `user5`  |
| `video`         |

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösenordskraven när du skapar en virtuell dator?

Det finns olika krav på lösenordslängd, beroende på vilket verktyg du använder:
 - Portal - mellan 12 till 72 tecken
 - PowerShell - mellan 8 och 123 tecken
 - CLI - mellan 12 - 123
 

Lösenord måste också uppfylla 3 av följande 4 komplexitetskrav:

* Innehålla gemener
* Innehålla versaler
* Innehålla en siffra
* Innehålla ett specialtecken (Regex matchar [\W_])

Följande lösenord är inte tillåtna:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa $$word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Lösenord!</td>
        <td style="text-align:center">Lösenord1</td>
        <td style="text-align:center">Lösenord22</td>
        <td style="text-align:center">iloveyou!</td>
    </tr>
</table>