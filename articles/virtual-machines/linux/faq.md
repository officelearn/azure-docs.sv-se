---
title: "Vanliga frågor om virtuella Linux-datorer i Azure | Microsoft Docs"
description: "Innehåller svar på några vanliga frågor om Linux virtuella datorer som skapats med Resource Manager-modellen."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-management
ms.assetid: 3648e09c-1115-4818-93c6-688d7a54a353
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: f7fb3f24e9ca6b1827028d118cf833aad830e6a1
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2017
---
# <a name="frequently-asked-question-about-linux-virtual-machines"></a>Vanliga frågor och svar om virtuella Linux-datorer
Den här artikeln tar några vanliga frågor om Linux virtuella datorer som skapats i Azure med hjälp av Resource Manager-distributionsmodellen. Windows-versionen av det här avsnittet finns [vanliga frågor om Windows-datorer](../windows/faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Mer information finns i [Linux på Azure-Endorsed distributioner](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 4 TB (4,095 GB). Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Azure hanterade diskar är de rekommenderade disk lagring erbjudandena för användning med Azure Virtual Machines för beständig lagring av data. Du kan använda flera Managed Disks till varje virtuell dator. Managed Disks tillhandahåller två lagringsalternativ: Premium och Standard. Information om priser finns [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).

Azure storage-konton kan också ge lagring för operativsystemets disk och eventuella hårddiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur kan jag använda min virtuella dator?
Upprätta en fjärranslutning för att logga in på den virtuella datorn med SSH (Secure Shell). Se anvisningar för hur du ansluter [från Windows](ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) eller [från Linux och Mac](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Som standard tillåter SSH högst 10 samtidiga anslutningar. Du kan öka detta antal genom att redigera konfigurationsfilen.

Om du får problem, kolla [felsökning av SSH (Secure Shell) anslutningar](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="can-i-use-the-temporary-disk-devsdb1-to-store-data"></a>Kan jag använda den tillfälliga disken (/ dev/sdb1) för att lagra data?
Använd inte tillfällig disken (/ dev/sdb1) för att lagra data. Det är bara det för tillfällig lagring. Riskerar du att förlora data som inte kan återställas.

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig Azure VM?
Ja. Instruktioner finns i [hur du skapar en kopia av en virtuell Linux-dator i Resource Manager-distributionsmodellen](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför inte visas Kanada Central och Kanada Öst regioner via Azure Resource Manager?
Två nya regionerna Kanada Central och Kanada Öst registreras inte automatiskt för att skapa en virtuell dator för befintliga Azure-prenumerationer. Denna registrering sker automatiskt när en virtuell dator distribueras via Azure portal till en annan region med Azure Resource Manager. När en virtuell dator har distribuerats till andra Azure-region, ska nya områden vara tillgängligt för följande virtuella datorer.

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort till en virtuell dator när den har skapats?
Ja, det är nu möjligt. Den virtuella datorn först måste stoppas frigjord. Du kan sedan lägga till eller ta bort ett nätverkskort (om det inte är det sista nätverkskortet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några kraven för dator?
Ja. Namnet kan vara högst 64 tecken. Se [Naming conventions regler och begränsningar](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer information om namngivning av dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det någon resurs kraven för gruppen?
Ja. Resursgruppens namn kan vara upp till 90 tecken långt. Se [Naming conventions regler och begränsningar](/architecture/best-practices/naming-conventions#naming-rules-and-restrictions?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mer information om resursgrupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är kraven för användarnamn när du skapar en virtuell dator?
Användarnamn måste vara 1 och 64 tecken.

Följande användarnamn är inte tillåtna:

<table>
    <tr>
        <td style="text-align:center">Administratören </td><td style="text-align:center"> Admin </td><td style="text-align:center"> Användaren </td><td style="text-align:center"> Användare1</td>
    </tr>
    <tr>
        <td style="text-align:center">Test </td><td style="text-align:center"> Användare2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> USER3</td>
    </tr>
    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> A</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> ASPNET</td>
    </tr>
    <tr>
        <td style="text-align:center">säkerhetskopiering </td><td style="text-align:center"> Konsolen </td><td style="text-align:center"> David </td><td style="text-align:center"> Gäst</td>
    </tr>
    <tr>
        <td style="text-align:center">John </td><td style="text-align:center"> Ägare </td><td style="text-align:center"> rot </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">SQL </td><td style="text-align:center"> Support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">Test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> Användare4 lade </td><td style="text-align:center"> user5</td>
    </tr>
</table>


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösenordskraven för när du skapar en virtuell dator?
Lösenord måste vara 6 72 tecken och uppfylla 3 av följande 4 komplexitetskrav:

* Har lägre tecken
* Övre tecken
* Har en siffra
* Har ett specialtecken (Regex matcha [\W_])

Följande lösenord är inte tillåtna:

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
