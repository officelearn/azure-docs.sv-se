---
title: Ansluta till din Microsoft Azure-baserad virtuell dator | Azure Marketplace
description: Beskriver hur du ansluter till den nya virtuella datorn skapas på Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ca002a77fd7252466f604b995af949b54694bc8c
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938579"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Ansluta till din Azure-baserad virtuell dator

Den här artikeln förklarar hur du ansluter till och logga in på de virtuella datorerna (VM) som du skapade i Azure.  När du har anslutit kan du arbeta med den virtuella datorn som om du loggade in lokalt på värdservern. 

## <a name="connect-to-a-windows-based-vm"></a>Ansluta till en Windows-baserad virtuell dator

Du använder fjärrskrivbordsklienten för att ansluta till den Windows-baserade virtuella datorn finns i Azure.  De flesta versioner av Windows innehåller inbyggt stöd för remote desktop protocol (RDP).  För andra datorer kan du hitta mer information om klienter i [fjärrskrivbordsklienter](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

I följande artikel beskriver hur du använder det inbyggda stödet för Windows RDP för att ansluta till den virtuella datorn: [Hur du ansluter och logga in på Azure-datorer som kör Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Du kan få säkerhetsvarningar under processen, till exempel att RDP-filen är från en okänd utgivare eller att det inte går att verifiera dina autentiseringsuppgifter.  Det är säkert att ignorera dessa varningar.


## <a name="connect-to-a-linux-based-vm"></a>Ansluta till en Linux-baserad virtuell dator

För att kunna ansluta den virtuella Linux-baserade datorn, behöver du en secure shell protocol (SSH)-klient.  Den här diskussionen ska använda den kostnadsfria [PuTTY](https://www.ssh.com/ssh/putty/) SHH-terminal.

1. I den **virtuella datorer** bladet för den [Azure-portalen](https://ms.portal.azure.com), Välj den virtuella datorn som du vill ansluta till.  
2. **Starta** den virtuella datorn om den inte redan körs.
3. Klicka på namnet på den virtuella datorn för att öppna dess **översikt** sidan.
4. Observera den offentliga IP-adress och DNS-namnet på den virtuella datorn.  (Om dessa värden inte har angetts så måste du [skapa ett nätverksgränssnitt](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Översikt över VM-inställningar](./media/publishvm_019.png)
 
5. Öppna programmet PuTTY.  
6. I dialogrutan PuTTY-konfiguration anger du IP-adressen eller DNS-namnet på den virtuella datorn. 

   ![PuTTY-inställningar för terminal](./media/publishvm_020.png)
 
7. Klicka på **öppna** att öppna en terminal för PuTTY.  
8. När du uppmanas, ange kontonamnet och lösenordet för ditt Linux VM-konto. 

   Om du har anslutningsproblem finns i dokumentationen för SSH-klienten, till exempel [kapitel 10: Vanliga felmeddelanden](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Mer information, inklusive hur du lägger till en stationär dator till en etablerad Linux-VM finns i [installera och konfigurera Fjärrskrivbord för att ansluta till en Linux-VM i Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Stoppa oanvända virtuella datorer
Azure-fakturor för virtuell dator som är värd för när en virtuell dator körs *eller inaktiva*.  Därför är det bäst att stoppa virtuella datorer som för närvarande inte används.  Till exempel testa säkerhetskopiering, eller dras tillbaka virtuella datorer är lämpliga kandidater för avstängning. Utför följande steg för att stänga av en virtuell dator:

1. På den **virtuella datorer** bladet Välj den virtuella datorn som du vill stoppa. 
2. I verktygsfältet längst upp på sidan, klickar du på den **stoppa** knappen.

   ![Stoppa en virtuell dator](./media/publishvm_018.png)

Azure snabbt stoppar den virtuella datorn i en process som kallas *frigörs*, som inte bara stänger av operativsystemet på den virtuella datorn, men behöver maskinvaru- och nätverksresurser som har etablerats efter den.

Om du vill återaktivera en stoppad virtuell dator senare markerar du det och klickar på den **starta** knappen.


## <a name="next-steps"></a>Nästa steg

När du är ansluten via en fjärranslutning, är det dags att [konfigurera din virtuella dator](./cpp-configure-vm.md).
