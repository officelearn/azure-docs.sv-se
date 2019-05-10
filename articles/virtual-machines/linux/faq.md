---
title: Vanliga frågor om virtuella Linux-datorer i Azure | Microsoft Docs
description: Innehåller svar på några vanliga frågor om Linux-datorer som skapas med Resource Manager-modellen.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: 0623a7aff15184822ee8abde0b3c751f8a105b5b
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65463573"
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Vanliga frågor och svar om virtuella Linux-datorer
Den här artikeln tar upp några vanliga frågor om Linux-datorer som skapats i Azure med hjälp av Resource Manager-distributionsmodellen. Windows-version av det här avsnittet finns [vanliga frågor och svar om Windows virtuella datorer](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Mer information finns i [Linux på Azure-godkända distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 4 TB (4095 GB). Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure Managed Disks är de rekommenderade disklagringsalternativet för användning med Azure Virtual Machines för beständig datalagring. Du kan använda flera Managed Disks till varje virtuell dator. Hanterade diskar två typer av erbjudanden lagringsalternativ: Premium och Standard Managed Disks. Information om priser finns i [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).

Azure storage-konton kan också ge lagringsutrymme för operativsystemsdisken och eventuella datadiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur kommer jag åt min virtuella dator?
Upprätta en fjärranslutning för att logga in på den virtuella datorn, med hjälp av SSH (Secure Shell). Se anvisningarna om hur du ansluter [från Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [från Linux och Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Som standard tillåter SSH högst 10 samtidiga anslutningar. Du kan öka detta antal genom att redigera konfigurationsfilen.

Om du har problem, kolla [felsöka Secure Shell (SSH)-anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan jag använda den temporära disken (/ dev/sdb1) för att lagra data?
Använd inte den temporära disken (/ dev/sdb1) för att lagra data. Det är bara det för tillfällig lagring. Du riskerar att förlora data som inte kan återställas.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig Azure VM?
Ja. Anvisningar finns i [hur du skapar en kopia av en Linux-dator i distributionsmodellen för Resource Manager](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför ser jag inte centrala Kanada och Östra Kanada regioner via Azure Resource Manager?
Två nya regioner för centrala Kanada och Östra Kanada registreras inte automatiskt för att skapa en virtuell dator för befintliga Azure-prenumerationer. Denna registrering görs automatiskt när en virtuell dator har distribuerats via Azure-portalen till valfri region med Azure Resource Manager. När en virtuell dator har distribuerats till andra Azure-region, ska de nya regionerna vara tillgänglig för efterföljande virtuella datorer.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort till den virtuella datorn när den har skapats?
Ja, det är nu möjligt. Den virtuella datorn måste först stoppas frigörs. Du kan sedan lägga till eller ta bort ett nätverkskort (såvida den inte det senaste nätverkskortet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några krav på dator namn?
Ja. Namnet på datorn kan vara högst 64 tecken långt. Se [Naming conventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions) för mer information om namngivning av dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det någon resurs uppfylla kraven för gruppen?
Ja. Resursgruppens namn kan vara högst 90 tecken. Se [Naming conventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions) för mer information om resursgrupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är kraven för användarnamn när du skapar en virtuell dator?

Användarnamn måste innehålla 1-32 tecken långt.

Följande användarnamn tillåts inte:

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

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösenordskraven för när du skapar en virtuell dator?

Det finns olika lösenord längdkraven, beroende på vilket verktyg som du använder:
 - Portal - mellan 12-72 tecken
 - PowerShell – mellan mellan 8 och 123 tecken
 - CLI - mellan 12-123
 

Lösenord måste uppfylla 3 av följande 4 komplexitetskrav:

* Har lägre tecken
* Övre tecken
* Har en siffra
* Har ett specialtecken (Regex matchar [\W_])

Följande lösenorden är inte tillåtna:

<table>
    <tr>
        <td style="text-align:center">abc@123</td>
        <td style="text-align:center">P@$$w0rd</td>
        <td style="text-align:center">P@ssw0rd</td>
        <td style="text-align:center">P@ssword123</td>
        <td style="text-align:center">Pa$ $word</td>
    </tr>
    <tr>
        <td style="text-align:center">pass@word1</td>
        <td style="text-align:center">Lösenord!</td>
        <td style="text-align:center">Password1</td>
        <td style="text-align:center">Password22</td>
        <td style="text-align:center">ILOVEYOU!</td>
    </tr>
</table>
