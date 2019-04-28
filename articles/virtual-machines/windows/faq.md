---
title: Vanliga frågor och svar om Windows virtuella datorer i Azure | Microsoft Docs
description: Innehåller svar på några vanliga frågor om Windows virtuella datorer som skapas med Resource Manager-modellen.
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
ms.openlocfilehash: 65d4326763ef9754159e94c9426f3aee69f80ffd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61095683"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Vanliga frågor och svar om Windows virtuella datorer
Den här artikeln tar upp några vanliga frågor om Windows virtuella datorer som skapats i Azure med hjälp av Resource Manager-distributionsmodellen. Linux-version av det här avsnittet finns [vanliga frågor och svar om virtuella Linux-datorer](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Information om Supportpolicy för köra serverprogramvara från Microsoft i Azure finns i [Microsofts serverprogramsupport för Azure Virtual Machines](https://support.microsoft.com/kb/2721672)

Vissa versioner av Windows 7, Windows 8.1 och Windows 10 är tillgängliga för MSDN Azure-förmånsprenumeranter och MSDN Dev och Test betala per användning-prenumeranter för utvecklings- och uppgifter. Mer information, bland annat instruktioner och begränsningar, finns i [Windows-klientavbildningar för MSDN-prenumeranter](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 4 TB (4095 GB). Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure Managed Disks är de rekommenderade disklagringsalternativet för användning med Azure Virtual Machines för beständig datalagring. Du kan använda flera Managed Disks till varje virtuell dator. Hanterade diskar två typer av erbjudanden lagringsalternativ: Premium och Standard Managed Disks. Information om priser finns i [hanterade diskar priser](https://azure.microsoft.com/pricing/details/managed-disks).

Azure storage-konton kan också ge lagringsutrymme för operativsystemsdisken och eventuella datadiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur kommer jag åt min virtuella dator?
Upprätta en fjärranslutning med Remote Desktop Connection (RDP) för en virtuell Windows-dator. Anvisningar finns i [hur du ansluter och logga in på Azure-datorer som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Högst två samtidiga anslutningar stöds, om inte servern är konfigurerad som en värd för Remote Desktop Services-sessionen.  

Om du har problem med fjärrskrivbord [felsöka fjärrskrivbordsanslutningar till en Windows-baserade Azure-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Om du är bekant med Hyper-V kanske du letar efter ett verktyg som liknar VMConnect. Azure tillhandahåller inget liknande verktyg eftersom konsolåtkomst till en virtuell dator inte stöds.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan jag använda den temporära disken (enheten D: som standard) för att lagra data?
Använd inte den temporära disken för att lagra data. Det är bara tillfälligt lagringsutrymme, så du riskerar att förlora data som inte kan återställas. Dataförlust kan inträffa när den virtuella datorn flyttas till en annan värd. Storleksändring av en virtuell dator, uppdatering av värden eller ett maskinvarufel på värden är några av orsakerna till att en virtuell dator kan flyttas.

Om du har ett program som behöver använda enhetsbeteckningen D: kan omtilldela du enhetsbeteckningar så att den temporära disken använder något annat än D:. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hur ändrar jag enhetsbeteckningen för den temporära disken?
Du kan ändra enhetsbeteckningen genom att flytta växlingsfilen och omtilldela enhetsbeteckningar, men du måste se till att du gör i en viss ordning. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig virtuell dator till en tillgänglighetsuppsättning?
Nej. Om du vill att den virtuella datorn ska ingå i en tillgänglighetsuppsättning, måste du skapa den virtuella datorn i uppsättningen. Det finns för närvarande inte ett sätt att lägga till en virtuell dator i en tillgänglighetsuppsättning när den har skapats.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan jag överföra en virtuell dator till Azure?
Ja. Anvisningar finns i [migrera lokala virtuella datorer till Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Kan jag ändra storlek på operativsystemdisken?
Ja. Anvisningar finns i [så öka storleken på operativsystemenheten för en virtuell dator i en Azure-resursgrupp](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig Azure VM?
Ja. Med hjälp av hanterade avbildningar, kan du skapa en avbildning av en virtuell dator och sedan använda avbildningen för att skapa flera nya virtuella datorer. Anvisningar finns i [skapa en anpassad avbildning av en virtuell dator](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför ser jag inte centrala Kanada och Östra Kanada regioner via Azure Resource Manager?

Två nya regioner för centrala Kanada och Östra Kanada registreras inte automatiskt för att skapa en virtuell dator för befintliga Azure-prenumerationer. Denna registrering görs automatiskt när en virtuell dator har distribuerats via Azure-portalen till valfri region med Azure Resource Manager. När en virtuell dator har distribuerats till andra Azure-region, ska de nya regionerna vara tillgänglig för efterföljande virtuella datorer.

## <a name="does-azure-support-linux-vms"></a>Azure har stöd för virtuella Linux-datorer?
Ja. För att snabbt skapa en Linux VM du prova att använda, se [skapar en Linux-VM på Azure via portalen](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort till den virtuella datorn när den har skapats?
Ja, det är nu möjligt. Den virtuella datorn måste först stoppas frigörs. Du kan sedan lägga till eller ta bort ett nätverkskort (såvida den inte det senaste nätverkskortet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några krav på dator namn?
Ja. Namnet på datorn kan vara högst 15 tecken. Se [Naming conventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions#compute) för mer information om namngivning av dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det någon resurs uppfylla kraven för gruppen?
Ja. Resursgruppens namn kan vara högst 90 tecken. Se [Naming conventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) för mer information om resursgrupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är kraven för användarnamn när du skapar en virtuell dator?

Användarnamn kan vara högst 20 tecken långt och får inte sluta med en punkt (””.). 


Följande användarnamn tillåts inte:
<table>
    <tr>
        <td style="text-align:center">1</td><td style="text-align:center">123</td><td style="text-align:center">a</td><td style="text-align:center">actuser</td>
    </tr>
    <tr>
        <td style="text-align:center">adm</td><td style="text-align:center">admin</td><td style="text-align:center">admin1</td><td style="text-align:center">admin2</td>
    </tr>   <tr>
        <td style="text-align:center">administratör</td><td style="text-align:center">aspnet</td><td style="text-align:center">säkerhetskopiering</td><td style="text-align:center">konsol</td>
    </tr>
    <tr>
        <td style="text-align:center">David </td><td style="text-align:center">Gäst</td><td style="text-align:center">john</td><td style="text-align:center">ägare</td>
    </tr>
    <tr>
        <td style="text-align:center">rot</td><td style="text-align:center">server</td><td style="text-align:center">sql</td><td style="text-align:center">support</td>
    </tr>
    <tr>
        <td style="text-align:center">support_388945a0</td><td style="text-align:center">sys</td><td style="text-align:center">test</td><td style="text-align:center">test1</td>
    </tr>
    <tr>
        <td style="text-align:center">test2</td><td style="text-align:center">test3</td><td style="text-align:center">Användare</td><td style="text-align:center">User1</td>
    </tr>
    <tr>
        <td style="text-align:center">Användare2</td><td style="text-align:center">USER3</td><td style="text-align:center">Användare4 lade</td><td style="text-align:center">user5</td>
    </tr>
</table>

## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösenordskraven för när du skapar en virtuell dator?
Lösenord måste vara mellan 12 och 123 tecken långt och uppfylla 3 av följande 4 komplexitetskrav:

* Har lägre tecken
* Övre tecken
* Har en siffra
* Har ett specialtecken (Regex matchar [\W_])

Följande lösenorden är inte tillåtna:

<table>
    <tr>
        <td>abc@123</td>
        <td>ILOVEYOU!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa$ $word</td>
        <td>pass@word1</td>
        <td>Lösenord!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
