---
title: Vanliga frågor och svar om virtuella Windows-datorer i Azure
description: Innehåller svar på några av de vanligaste frågorna om virtuella Windows-datorer som skapats med Resource Manager-modellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-management
ms.assetid: 757da816-a050-4889-a010-6f75d7978eb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 05/08/2019
ms.author: cynthn
ms.openlocfilehash: e3d2673ba52ba0cca36e2a999558313b64716ade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299622"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Vanliga frågor och frågor om Virtuella Datorer i Windows
Den här artikeln innehåller några vanliga frågor om virtuella Windows-datorer som skapats i Azure med hjälp av distributionsmodellen för Resource Manager. För Linux-versionen av det här avsnittet, se [Vanliga frågor om Linux Virtuella maskiner](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Information om supportprincipen för att köra Microsoft-serverprogramvara i Azure finns i [Stöd för Microsoft-serverprogramvara för Virtuella Azure-datorer](https://support.microsoft.com/kb/2721672).

Vissa versioner av Windows 7, Windows 8.1 och Windows 10 är tillgängliga för MSDN Azure-förmånsprenumeranter och MSDN Dev- och Test Pay-As-You-Go-prenumeranter för utveckling och testuppgifter. Mer information, bland annat instruktioner och begränsningar, finns i [Windows-klientavbildningar för MSDN-prenumeranter](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje datadisk kan vara upp till 32 767 GiB. Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure Managed Disks är de rekommenderade disklagringserbjudandena för användning med Virtuella Azure-datorer för beständig lagring av data. Du kan använda flera hanterade diskar med varje virtuell dator. Hanterade diskar erbjuder två typer av hållbara lagringsalternativ: Premium och Standard Hanterade diskar. Prisinformation finns i [Priser för hanterade diskar](https://azure.microsoft.com/pricing/details/managed-disks).

Azure-lagringskonton kan också tillhandahålla lagring för operativsystemdisken och alla datadiskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur kommer jag åt min virtuella dator?
Upprätta en fjärranslutning med rdp (Remote Desktop Connection) för en Virtuell Windows.Establish a remote connection using Remote Desktop Connection (RDP) for a Windows VM. Instruktioner finns i [Så här ansluter och loggar du in på en virtuell Azure-dator som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Högst två samtidiga anslutningar stöds, såvida inte servern är konfigurerad som en session värd för Fjärrskrivbordstjänster.  

Om du har problem med Fjärrskrivbord läser [du Felsöka anslutning till fjärrskrivbord till en Windows-baserad virtuell Virtuell dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)från Windows . 

Om du är bekant med Hyper-V kanske du letar efter ett verktyg som liknar VMConnect. Azure erbjuder inte ett liknande verktyg eftersom konsolåtkomst till en virtuell dator inte stöds.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan jag använda den temporära disken (D:-enheten som standard) för att lagra data?
Använd inte den tillfälliga disken för att lagra data. Det är bara tillfällig lagring, så du riskerar att förlora data som inte kan återställas. Dataförlust kan uppstå när den virtuella datorn flyttas till en annan värd. Storleksändring av en virtuell dator, uppdatering av värden eller ett maskinvarufel på värden är några av orsakerna till att en virtuell dator kan flyttas.

Om du har ett program som behöver använda enhetsbeteckningen D: kan du tilldela om enhetsbeteckningar så att den temporära disken använder något annat än D:. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hur ändrar jag enhetsbeteckningen för den temporära disken?
Du kan ändra enhetsbeteckningen genom att flytta sidfilen och tilldela om enhetsbeteckningar, men du måste se till att du gör stegen i en viss ordning. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig virtuell dator i en tillgänglighetsuppsättning?
Nej. Om du vill att den virtuella datorn ska vara en del av en tillgänglighetsuppsättning måste du skapa den virtuella datorn i uppsättningen. Det finns för närvarande inte ett sätt att lägga till en virtuell dator i en tillgänglighetsuppsättning när den har skapats.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan jag ladda upp en virtuell dator till Azure?
Ja. Instruktioner finns i [Migrera lokala virtuella datorer till Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Kan jag ändra storlek på OS-disken?
Ja. Instruktioner finns i [Så här expanderar du os-enheten](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)för en virtuell dator i en Azure Resource Group .

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig Azure VM?
Ja. Med hanterade avbildningar kan du skapa en avbildning av en virtuell dator och sedan använda avbildningen för att skapa flera nya virtuella datorer. Instruktioner finns i [Skapa en anpassad avbildning av en virtuell dator](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför ser jag inte regioner i Östra Kanada och Kanada via Azure Resource Manager?

De två nya regionerna i Canada Central och Canada East registreras inte automatiskt för att skapa virtuella datorer för befintliga Azure-prenumerationer. Den här registreringen görs automatiskt när en virtuell dator distribueras via Azure-portalen till någon annan region med Hjälp av Azure Resource Manager. När en virtuell dator har distribuerats till någon annan Azure-region bör de nya regionerna vara tillgängliga för efterföljande virtuella datorer.

## <a name="does-azure-support-linux-vms"></a>Stöder Azure virtuella Linux-datorer?
Ja. Information om hur du snabbt skapar en virtuell Linux-dator att prova finns i [Skapa en virtuell Linux-dator på Azure med portalen](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort i min virtuella dator när det har skapats?
Ja, det är nu möjligt. Den virtuella datorn måste först stoppas deallocated. Sedan kan du lägga till eller ta bort ett nätverkskort (såvida det inte är det sista nätverkskortet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några krav på datornamn?
Ja. Datornamnet kan vara högst 15 tecken långt. Se [Namngivningskonventioner regler och begränsningar](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging) för mer information om namngivning av dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det några resursgruppsnamnskrav?
Ja. Resursgruppsnamnet kan vara högst 90 tecken långt. Se [Namngivningskonventioner regler och begränsningar](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming) för mer information om resursgrupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är användarnamnskraven när du skapar en virtuell dator?

Användarnamn kan vara högst 20 tecken långa och kan inte sluta i en period ("."). 

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösenordskraven när du skapar en virtuell dator?

Det finns olika krav på lösenordslängd, beroende på vilket verktyg du använder:
 - Portal - mellan 12 till 72 tecken
 - PowerShell - mellan 8 och 123 tecken
 - CLI - mellan 12 - 123

* Innehålla gemener
* Innehålla versaler
* Innehålla en siffra
* Innehålla ett specialtecken (Regex matchar [\W_])

Följande lösenord är inte tillåtna:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>Pa $$word</td>
        <td>pass@word1</td>
        <td>Lösenord!</td>
        <td>Lösenord1</td>
        <td>Lösenord22</td>
    </tr>
</table>