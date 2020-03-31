---
title: Anslut till din Microsoft Azure-baserade virtuella dator | Azure Marketplace
description: Förklarar hur du ansluter till den nya virtuella datorn som skapats på Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2b7eb6d321a64835254b684c8faeedc53645dffe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278066"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Anslut till din Azure-baserade virtuella dator

I den här artikeln beskrivs hur du ansluter till och loggar in på de virtuella datorer som du skapade på Azure.  När du har anslutit kan du arbeta med den virtuella datorn som om du var lokalt inloggad på värdservern. 

## <a name="connect-to-a-windows-based-vm"></a>Ansluta till en Windows-baserad virtuell dator

Du använder fjärrskrivbordsklienten för att ansluta till den Windows-baserade virtuella datorn som finns på Azure.  De flesta versioner av Windows innehåller internt stöd för fjärrskrivbordsprotokollet (RDP).  För andra datorer hittar du mer information om klienter i [fjärrskrivbordsklienter](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

I följande artikel beskrivs hur du använder det inbyggda Windows RDP-stödet för att ansluta till din virtuella dator: [Så här ansluter och loggar du in på en virtuell Azure-dator som kör Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Du kan få säkerhetsvarningar under processen, till exempel att RDP-filen kommer från en okänd utgivare eller att dina användaruppgifter inte kan verifieras.  Det är säkert att ignorera dessa varningar.


## <a name="connect-to-a-linux-based-vm"></a>Anslut till en Linux-baserad virtuell dator

För att ansluta den Linux-baserade virtuella datorn behöver du en säker SSH-klient (Shell Protocol).  Denna diskussion kommer att använda den fria [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminal.

1. Gå till [Azure-portalen](https://ms.portal.azure.com). Sök efter och välj **Virtuella datorer**. 
2. Välj den virtuella dator som du vill ansluta till.  
3. **Starta** den virtuella datorn om den inte redan körs.
4. Klicka på namnet på den virtuella datorn för att öppna sidan **Översikt.**
5. Observera den offentliga IP-adressen och DNS-namnet på den virtuella datorn.  (Om dessa värden inte är inställda måste du [skapa ett nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Inställningar för VM-översikt](./media/publishvm_019.png)
 
6. Öppna PuTTY-programmet.  
7. I dialogrutan PuTTY-konfiguration anger du IP-adressen eller DNS-namnet på den virtuella datorn. 

   ![PuTTY-terminalinställningar](./media/publishvm_020.png)
 
8. Klicka på **Öppna** för att öppna en PuTTY-terminal.  
9. När du uppmanas att ange kontonamnet och lösenordet för ditt Linux VM-konto. 

Om du har anslutningsproblem läser du dokumentationen för SSH-klienten, till exempel [kapitel 10: Vanliga felmeddelanden](https://www.ssh.com/ssh/putty/putty-manuals).

Mer information, inklusive hur du lägger till ett skrivbord i en etablerad Virtuell Linux-dator, finns i [Installera och konfigurera Fjärrskrivbord för att ansluta till en Virtuell Linux-dator i Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Stoppa oanvända virtuella datorer
Azure-räkningar för VM-värd när en virtuell dator körs *eller är inaktiv*.  Därför är det bäst att stoppa virtuella datorer som för närvarande inte används.  Test, säkerhetskopiering eller pensionerade virtuella datorer är till exempel kandidater för avstängning. Så här stänger du av en virtuell dator:

1. På bladet **Virtuella datorer** väljer du den virtuella dator som du vill stoppa. 
2. Klicka på **stoppknappen** i verktygsfältet högst upp på sidan.

   ![Stoppa en virtuell dator](./media/publishvm_018.png)

Azure stoppar snabbt den virtuella datorn i en process som kallas *deallocation*, som inte bara stänger av operativsystemet på den virtuella datorn, men också frigör de maskinvaru- och nätverksresurser som tidigare etablerats för det.

Om du senare vill återaktivera en stoppad **Start** virtuell dator markerar du den och klickar på Start-knappen.


## <a name="next-steps"></a>Nästa steg

När du är fjärransluten är du redo att [konfigurera den virtuella datorn](./cpp-configure-vm.md).
