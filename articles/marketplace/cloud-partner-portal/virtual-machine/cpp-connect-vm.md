---
title: Ansluta till din Microsoft Azure-baserade virtuella datorn | Azure Marketplace
description: Förklarar hur du ansluter till den nya virtuella datorn som skapats på Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 256952d06e8b417be9ab2bc3c6c8c6857d50e2ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817374"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Anslut till en Azure-baserad virtuell dator

Den här artikeln förklarar hur du ansluter till och loggar in på de virtuella datorer som du skapade i Azure.  När du har anslutit kan du arbeta med den virtuella datorn som om du var inloggad lokalt på värd servern. 

## <a name="connect-to-a-windows-based-vm"></a>Ansluta till en Windows-baserad virtuell dator

Du kommer att använda fjärr skrivbords klienten för att ansluta till den Windows-baserade virtuella datorn som finns på Azure.  De flesta versioner av Windows innehåller inbyggt stöd för Remote Desktop Protocol (RDP).  För andra datorer kan du hitta mer information om klienter i [fjärr skrivbords klienter](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

Följande artikel beskriver hur du använder det inbyggda Windows RDP-stödet för att ansluta till din virtuella dator: [hur du ansluter och loggar in på en virtuell Azure-dator som kör Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Du kan få säkerhets varningar under processen, till exempel att. RDP-filen kommer från en okänd utgivare eller att dina användarautentiseringsuppgifter inte kan verifieras.  Det är säkert att ignorera dessa varningar.


## <a name="connect-to-a-linux-based-vm"></a>Ansluta till en Linux-baserad virtuell dator

För att kunna ansluta den Linux-baserade virtuella datorn behöver du en SSH-klient (Secure Shell Protocol).  I den här diskussionen används den kostnads fria SHH-terminalen för [SparaTillFil](https://www.ssh.com/ssh/putty/) .

1. På bladet **virtuella datorer** i [Azure Portal](https://ms.portal.azure.com)väljer du den virtuella dator som du vill ansluta till.  
2. **Starta** den virtuella datorn om den inte redan körs.
3. Klicka på namnet på den virtuella datorn för att öppna dess **översikts** sida.
4. Anteckna den offentliga IP-adressen och DNS-namnet på den virtuella datorn.  (Om dessa värden inte har angetts måste du [skapa ett nätverks gränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Inställningar för VM-översikt](./media/publishvm_019.png)
 
5. Öppna programmet SparaTillFil.  
6. Ange IP-adressen eller DNS-namnet på den virtuella datorn i dialog rutan SparaTillFil-konfiguration. 

   ![Inställningar för SparaTillFil-Terminal](./media/publishvm_020.png)
 
7. Klicka på **Öppna** för att öppna en SparaTillFil-Terminal.  
8. När du uppmanas till det anger du konto namnet och lösen ordet för ditt Linux VM-konto. 

   Om du har anslutnings problem kan du läsa dokumentationen för SSH-klienten, till exempel [kapitel 10: vanliga fel meddelanden](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Mer information, inklusive hur du lägger till ett skriv bord till en etablerad virtuell Linux-dator finns i [Installera och konfigurera fjärr skrivbord för att ansluta till en virtuell Linux-dator i Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Stoppa oanvända virtuella datorer
Azure-räkningar för VM-värd när en virtuell dator körs *eller är inaktiv*.  Därför är det bästa praxis att stoppa virtuella datorer som inte används för närvarande.  Till exempel test, säkerhets kopiering eller tillbakadragna virtuella datorer är kandidater för avstängning. Gör så här om du vill stänga av en virtuell dator:

1. På bladet **virtuella datorer** väljer du den virtuella dator som du vill stoppa. 
2. Klicka på **stopp** -knappen i verktygsfältet längst upp på sidan.

   ![Stoppa en virtuell dator](./media/publishvm_018.png)

Azure stoppar snabbt den virtuella datorn i en process som kallas *frigörning*, vilket inte bara stänger av operativ systemet på den virtuella datorn, utan även frigör de maskin-och nätverks resurser som tidigare har allokerats för den.

Om du senare vill återaktivera en stoppad virtuell dator markerar du den och klickar på knappen **Starta** .


## <a name="next-steps"></a>Nästa steg

När du har anslutit via fjärr anslutning är du redo att [Konfigurera den virtuella datorn](./cpp-configure-vm.md).
