---
title: Vanliga frågor och svar om virtuella Windows-datorer i Azure | Microsoft Docs
description: Innehåller svar på några vanliga frågor om virtuella Windows-datorer som skapats med Resource Manager-modellen.
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
ms.openlocfilehash: 0b1af2052e8aa916bf5fd6a40bea3502ff81a02d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079487"
---
# <a name="frequently-asked-question-about-windows-virtual-machines"></a>Vanliga frågor om Virtuella Windows-datorer
Den här artikeln behandlar några vanliga frågor om virtuella Windows-datorer som skapats i Azure med hjälp av distributions modellen Resource Manager. Linux-versionen av det här avsnittet finns i [vanliga frågor och svar om virtuella Linux-datorer](../linux/faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="what-can-i-run-on-an-azure-vm"></a>Vad kan jag köra på en virtuell Azure-dator?
Alla prenumeranter kan köra serverprogramvara på en virtuell Azure-dator. Information om support policyn för att köra Microsoft Server-programvara i Azure finns i [Microsoft Server Software support för Azure Virtual Machines](https://support.microsoft.com/kb/2721672).

Vissa versioner av Windows 7, Windows 8,1 och Windows 10 är tillgängliga för MSDN Azure-förmåns prenumeranter och MSDN dev och testa prenumerationer enligt principen betala per användning, för utveckling och test. Mer information, bland annat instruktioner och begränsningar, finns i [Windows-klientavbildningar för MSDN-prenumeranter](https://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/). 

## <a name="how-much-storage-can-i-use-with-a-virtual-machine"></a>Hur mycket lagringsutrymme kan jag använda med en virtuell dator?
Varje data disk kan vara upp till 32 767 GiB. Antalet datadiskar som du kan använda beror på storleken på den virtuella datorn. Mer information finns i [Storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Azure Managed Disks är de rekommenderade disk lagrings erbjudanden som används med Azure Virtual Machines för beständig data lagring. Du kan använda flera Managed Disks till varje virtuell dator. Managed Disks erbjuder två typer av varaktiga lagrings alternativ: Premium-och standard Managed Disks. Information om priser finns i [Managed disks priser](https://azure.microsoft.com/pricing/details/managed-disks).

Azure Storage-konton kan också tillhandahålla lagring för operativ system disken och alla data diskar. Varje disk är en VHD-fil som lagras som en sidblob. Information om priser finns i [Information om lagringspriser](https://azure.microsoft.com/pricing/details/storage/).

## <a name="how-can-i-access-my-virtual-machine"></a>Hur får jag åtkomst till min virtuella dator?
Upprätta en fjärr anslutning med hjälp av Anslutning till fjärrskrivbord (RDP) för en virtuell Windows-dator. Instruktioner finns i [så här ansluter du och loggar in på en virtuell Azure-dator som kör Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Högst två samtidiga anslutningar stöds, om inte servern har kon figurer ATS som en Fjärrskrivbordstjänster-värd för sessionen.  

Om du har problem med fjärr skrivbord kan du läsa [Felsök fjärr skrivbords anslutningar till en Windows-baserad virtuell Azure-dator](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Om du är bekant med Hyper-V kanske du letar efter ett verktyg som liknar VMConnect. Azure tillhandahåller inget liknande verktyg eftersom konsolåtkomst till en virtuell dator inte stöds.

## <a name="can-i-use-the-temporary-disk-the-d-drive-by-default-to-store-data"></a>Kan jag använda den temporära disken (enheten D: som standard) för att lagra data?
Använd inte den tillfälliga disken för att lagra data. Det är bara tillfällig lagring, så du riskerar att förlora data som inte kan återställas. Data förlust kan uppstå när den virtuella datorn flyttas till en annan värd. Storleksändring av en virtuell dator, uppdatering av värden eller ett maskinvarufel på värden är några av orsakerna till att en virtuell dator kan flyttas.

Om du har ett program som behöver använda D: s enhets beteckning kan du omtilldela enhets beteckningar så att den temporära disken använder något annat än D:. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).


## <a name="how-can-i-change-the-drive-letter-of-the-temporary-disk"></a>Hur ändrar jag enhetsbeteckningen för den temporära disken?
Du kan ändra enhets beteckningen genom att flytta växlings filen och tilldela om enhets beteckningar, men du måste se till att du utför stegen i en speciell ordning. Instruktioner finns i [Ändra enhetsbeteckningen för den temporära Windows-disken](change-drive-letter.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="can-i-add-an-existing-vm-to-an-availability-set"></a>Kan jag lägga till en befintlig virtuell dator i en tillgänglighets uppsättning?
Nej. Om du vill att den virtuella datorn ska ingå i en tillgänglighets uppsättning måste du skapa den virtuella datorn i uppsättningen. Det finns för närvarande inget sätt att lägga till en virtuell dator i en tillgänglighets uppsättning när den har skapats.

## <a name="can-i-upload-a-virtual-machine-to-azure"></a>Kan jag ladda upp en virtuell dator till Azure?
Ja. Instruktioner finns i [migrera lokala virtuella datorer till Azure](on-prem-to-azure.md).

## <a name="can-i-resize-the-os-disk"></a>Kan jag ändra storlek på OS-disken?
Ja. Instruktioner finns i [så här expanderar du operativ system enheten för en virtuell dator i en Azure-resurs grupp](expand-os-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="can-i-copy-or-clone-an-existing-azure-vm"></a>Kan jag kopiera eller klona en befintlig virtuell Azure-dator?
Ja. Med hanterade avbildningar kan du skapa en avbildning av en virtuell dator och sedan använda avbildningen för att skapa flera nya virtuella datorer. Instruktioner finns i [skapa en anpassad avbildning av en virtuell dator](tutorial-custom-images.md).

## <a name="why-am-i-not-seeing-canada-central-and-canada-east-regions-through-azure-resource-manager"></a>Varför ser jag inte Kanada Central-och Kanada östra regioner genom Azure Resource Manager?

De två nya regionerna i Kanada Central och Kanada, öst registreras inte automatiskt för att skapa virtuella datorer för befintliga Azure-prenumerationer. Den här registreringen görs automatiskt när en virtuell dator distribueras via Azure Portal till någon annan region som använder Azure Resource Manager. När en virtuell dator har distribuerats till någon annan Azure-region bör de nya regionerna vara tillgängliga för efterföljande virtuella datorer.

## <a name="does-azure-support-linux-vms"></a>Stöder Azure virtuella Linux-datorer?
Ja. Om du snabbt vill skapa en virtuell Linux-dator för att testa, se [skapa en virtuell Linux-dator i Azure med hjälp av portalen](../linux/quick-create-portal.md).

## <a name="can-i-add-a-nic-to-my-vm-after-its-created"></a>Kan jag lägga till ett nätverkskort till min virtuella dator när det har skapats?
Ja, det är nu möjligt. Den virtuella datorn måste först stoppas. Sedan kan du lägga till eller ta bort ett nätverkskort (om det inte är det sista NÄTVERKSKORTet på den virtuella datorn). 

## <a name="are-there-any-computer-name-requirements"></a>Finns det några dator namns krav?
Ja. Dator namnet får innehålla högst 15 tecken. Se [namn konventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions#compute) för mer information om hur du namnger dina resurser.

## <a name="are-there-any-resource-group-name-requirements"></a>Finns det några resurs grupp namns krav?
Ja. Resurs gruppens namn får innehålla högst 90 tecken. Se [namn konventions regler och begränsningar](/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions) för mer information om resurs grupper.

## <a name="what-are-the-username-requirements-when-creating-a-vm"></a>Vilka är användar namns kraven när du skapar en virtuell dator?

Användar namn får innehålla högst 20 tecken och får inte sluta med en punkt ("."). 

Följande användar namn är inte tillåtna:

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


## <a name="what-are-the-password-requirements-when-creating-a-vm"></a>Vilka är lösen ords kraven när du skapar en virtuell dator?

Det finns varierande krav på lösen ords längd, beroende på vilket verktyg du använder:
 - Portal – mellan 12-72 tecken
 - PowerShell – mellan 8-123 tecken
 - CLI – mellan 12-123

* Ha färre tecken
* Ha övre tecken
* Ha en siffra
* Ha ett specialtecken (regex match [\W_])

Följande lösen ord är inte tillåtna:

<table>
    <tr>
        <td>abc@123</td>
        <td>iloveyou!</td>
        <td>P@$$w0rd</td>
        <td>P@ssw0rd</td>
        <td>P@ssword123</td>
    </tr>
    <tr>
        <td>PA $ $word</td>
        <td>pass@word1</td>
        <td>Ords!</td>
        <td>Password1</td>
        <td>Password22</td>
    </tr>
</table>
