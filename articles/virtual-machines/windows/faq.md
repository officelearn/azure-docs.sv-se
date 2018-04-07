---
title: Vanliga frågor och svar om virtuella Windows-datorer i Azure | Microsoft Docs
description: Innehåller svar på några vanliga frågor om Windows-datorer som skapats med Resource Manager-modellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: cynthn
ms.openlocfilehash: f427035f413dde304c2270006c6665120cb3e1e1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Vanliga frågor och svar om virtuella Windows-datorer
Den här artikeln tar några vanliga frågor om Windows-datorer som skapats i Azure med hjälp av Resource Manager-distributionsmodellen. Linux-version av det här avsnittet finns [vanliga frågor om virtuella Linux-datorer](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Information om Supportpolicy för körs Microsoft server-program i Azure finns [support för Microsoft server-programvara för virtuella datorer i Azure](https://support.microsoft.com/kb/2721672)

Vissa versioner av Windows 7, Windows 8.1 och Windows 10 är tillgängliga för MSDN Azure-förmånen prenumeranter och MSDN Dev / Test-betala per användning prenumeranter för utveckling och testning uppgifter. Mer information, bland annat instruktioner och begränsningar, finns i [Windows-klientavbildningar för MSDN-prenumeranter](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 4 TB (4,095 GB). Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure hanterade diskar är de rekommenderade disk lagring erbjudandena för användning med Azure Virtual Machines för beständig lagring av data. Du kan använda flera Managed Disks till varje virtuell dator. Managed Disks tillhandahåller två lagringsalternativ: Premium och Standard. Information om priser finns [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).

Azure storage-konton kan också ge lagring för operativsystemets disk och eventuella hårddiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur kan jag använda min virtuella dator?
Upprätta en anslutning med anslutning till fjärrskrivbord (RDP) för en virtuell Windows-dator. Instruktioner finns i [ansluta och logga in på en virtuell Azure-dator kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Högst två samtidiga anslutningar stöds, såvida inte servern är konfigurerad som en värd för Remote Desktop Services-sessionen.  

Om du har problem med fjärrskrivbord, se [felsökning av anslutning till fjärrskrivbord till en Windows-baserad Azure-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Om du är bekant med Hyper-V kanske du letar efter ett verktyg som liknar VMConnect. Azure tillhandahåller inget liknande verktyg eftersom konsolåtkomst till en virtuell dator inte stöds.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan jag använda den temporära disken (D: enheten som standard) för att lagra data?
Använd inte tillfällig disken för att lagra data. Det är bara tillfällig lagring, så riskerar du att förlora data som inte kan återställas. Dataförlust kan inträffa när den virtuella datorn flyttas till en annan värd. Storleksändring av en virtuell dator, uppdatering av värden eller ett maskinvarufel på värden är några av orsakerna till att en virtuell dator kan flyttas.

Om du har ett program som behöver använda enhetsbeteckningen D: kan omtilldela du enhetsbeteckningar så att den tillfälliga disken använder något annat än D:. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hur ändrar jag enhetsbeteckningen för den temporära disken?
Du kan ändra enhetsbeteckningen genom att flytta växlingsfilen och omtilldela enhetsbeteckningar, men du måste se till att du har utfört stegen i en viss ordning. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning?
Nej. Om du vill att den virtuella datorn ska ingå i en tillgänglighetsuppsättning, måste du skapa den virtuella datorn i uppsättningen. Det finns för närvarande inte ett sätt att lägga till en virtuell dator i en tillgänglighetsuppsättning när den har skapats.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan jag överföra en virtuell dator till Azure?
Ja. Instruktioner finns i [migrera lokala virtuella datorer till Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Kan jag ändra storlek på OS-disken?
Ja. Instruktioner finns i [hur du utökar OS-enhet på en virtuell dator i Azure-resursgrupp](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig Azure VM?
Ja. Med hanterade avbildningar kan du skapa en avbildning av en virtuell dator och sedan använda bilden för att skapa flera nya virtuella datorer. Instruktioner finns i [skapa en anpassad avbildning av en virtuell dator](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför inte visas Kanada Central och Kanada Öst regioner via Azure Resource Manager?

Två nya regionerna Kanada Central och Kanada Öst registreras inte automatiskt för att skapa en virtuell dator för befintliga Azure-prenumerationer. Denna registrering sker automatiskt när en virtuell dator distribueras via Azure portal till en annan region med Azure Resource Manager. När en virtuell dator har distribuerats till andra Azure-region, ska nya områden vara tillgängligt för följande virtuella datorer.

## <a name="does-azure-support-linux-vms"></a>Stöder virtuella Linux-datorer i Azure?
Ja. För att snabbt skapa en Linux VM att testa, se [skapar en Linux-VM på Azure via portalen](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort till en virtuell dator när den har skapats?
Ja, det är nu möjligt. Den virtuella datorn först måste stoppas frigjord. Du kan sedan lägga till eller ta bort ett nätverkskort (om det inte är det sista nätverkskortet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några kraven för dator?
Ja. Namnet kan vara högst 15 tecken. Se [Naming conventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions#compute) mer information om namngivning av dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det någon resurs kraven för gruppen?
Ja. Resursgruppens namn kan vara upp till 90 tecken långt. Se [Naming conventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) mer information om resursgrupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är kraven för användarnamn när du skapar en virtuell dator?

Användarnamn kan vara högst 20 tecken långt och får inte sluta med en punkt (””.). 


Följande användarnamn är inte tillåtna:
<table>
    <tr>
        <td style="text-align:center">Administratören </td><td style="text-align:center"> administratör </td><td style="text-align:center"> användare </td><td style="text-align:center"> Användare1</td>
    </tr>
    <tr>
        <td style="text-align:center">test </td><td style="text-align:center"> user2 </td><td style="text-align:center"> test1 </td><td style="text-align:center"> USER3</td>
    </tr>    <tr>
        <td style="text-align:center">admin1 </td><td style="text-align:center"> 1 </td><td style="text-align:center"> 123 </td><td style="text-align:center"> a</td>
    </tr>
    <tr>
        <td style="text-align:center">actuser  </td><td style="text-align:center"> ADM </td><td style="text-align:center"> admin2 </td><td style="text-align:center"> aspnet</td>
    </tr>
    <tr>
        <td style="text-align:center">säkerhetskopiering </td><td style="text-align:center"> konsol </td><td style="text-align:center"> David </td><td style="text-align:center"> Gäst</td>
    </tr>
    <tr>
        <td style="text-align:center">john </td><td style="text-align:center"> ägare </td><td style="text-align:center"> rot </td><td style="text-align:center"> server</td>
    </tr>
    <tr>
        <td style="text-align:center">sql </td><td style="text-align:center"> support </td><td style="text-align:center"> support_388945a0 </td><td style="text-align:center"> sys</td>
    </tr>
    <tr>
        <td style="text-align:center">test2 </td><td style="text-align:center"> test3 </td><td style="text-align:center"> Användare4 lade </td><td style="text-align:center"> user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösenordskraven för när du skapar en virtuell dator?
Lösenord måste bestå av 12 och 123 tecken och uppfylla 3 av följande 4 komplexitetskrav:

* Har lägre tecken
* Övre tecken
* Har en siffra
* Har ett specialtecken (Regex matcha [\W_])

Följande lösenord är inte tillåtna:

<table>
    <tr>
        <td>abc@123 </td>
        <td>P@$$w0rd </td>
        <td>P@ssw0rd </td>
        <td>P@ssword123 </td>
        <td>Pa$$word </td>
    </tr>
    <tr>
        <td>pass@word1 </td>
        <td>Lösenord! </td>
        <td>Password1 </td>
        <td>Password22 </td>
        <td>ILOVEYOU! </td>
    </tr>
</table>
