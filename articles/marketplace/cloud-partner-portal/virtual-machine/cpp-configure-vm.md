---
title: Konfigurera Microsoft Azure-baserade VM på Azure Marketplace | Microsoft Docs
description: Beskriver hur du storlek, uppdatera och generalisera en virtuell dator på Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 5ccfef8a6ad367e8fac100217713cd323341a535
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183479"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurera den virtuella Azure-baserade datorn

Den här artikeln beskriver hur du storlek, uppdatera och generalisera en virtuell dator (VM) som finns i Azure.  Dessa steg är nödvändiga för att förbereda din virtuella dator som ska distribueras från Azure Marketplace.


## <a name="sizing-the-vhds"></a>Ändra storlek på de virtuella hårddiskarna

<!--TD: Check if the following assertion is true. I didn't understand the original content. --> Om du har valt ett av de virtuella datorerna vara förkonfigurerad med ett operativsystem (och eventuellt ytterligare tjänster) och du redan har valt en standard Azure VM-storlek, enligt beskrivningen i [VM SKU: er fliken](./cpp-skus-tab.md).  Starta din lösning med en förkonfigurerad OS är den rekommenderade metoden.  Men om du installerar ett operativsystem manuellt, måste du storleken din primära VHD i din avbildning:

- För Windows, operativsystemet VHD: N bör skapas som en 127-128 GB VHD i fast format. 
- För Linux, den här virtuella Hårddisken ska skapas som en 30-50 GB VHD i fast format.

Om den fysiska storleken är mindre än 127-128 GB bör VHD: N vara begränsad. Windows- och SQL Server Källavbildningen angetts uppfyller redan dessa krav, så ändra inte format eller storleken på den hämtade VHD: N. 

Datadiskar kan vara så stora som 1 TB. När du bestämmer dig deras storlek, Kom ihåg att kunder inte kan ändra storlek på VHD: er inuti en avbildning vid distributionen. Data disk VHD: er bör skapas som VHD i fast format. De bör också vara begränsad. Datadiskar kan ursprungligen vara tom eller innehålla data.


## <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

Källavbildningen operativsystemet virtuella datorer innehåller de senaste uppdateringarna till datum då den publicerades. Se till att du uppdaterar Operativsystemet och alla installerade tjänster med alla de senaste säkerhet och underhåll korrigeringarna innan du publicerar operativsystemet VHD som du har skapat.

Windows Server 2016, kör den **söka efter uppdateringar** kommando.  I annat fall äldre versioner av Windows finns i [så här hämtar du en uppdatering via Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows update installerar automatiskt de senaste kritiska och viktiga säkerhetsuppdateringarna.

För Linux-distributioner, uppdateringar ofta hämtas och installeras via ett kommandoradsverktyg eller ett grafiskt verktyg.  Till exempel Ubuntu Linux innehåller de [apt-get](http://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) kommandot och [Uppdateringshanterare](http://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) verktyget för att uppdatera Operativsystemet.


## <a name="perform-additional-security-checks"></a>Utföra ytterligare säkerhetskontroller

Du bör behålla en hög säkerhetsnivå för din lösning avbildningar i Azure Marketplace.  I följande artikel finns en checklista av säkerhetskonfigurationer och procedurer för att hjälpa dig i det här målet: [Säkerhetsrekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Några av de här rekommendationerna är specifika för Linux-baserade avbildningar, men gäller de för alla VM-avbildning. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utföra anpassad konfiguration och schemalagda aktiviteter

Om ytterligare konfiguration krävs, är den rekommenderade metoden att använda en schemalagd aktivitet som körs vid start för att göra ytterligare ändringar till den virtuella datorn när den har distribuerats.  Också beakta följande rekommendationer:
- Om det är en aktivitet körs en gång, rekommenderas att uppgiften Radera sig själv när den har slutförts.
- Konfigurationer bör inte förlita dig på några andra enheter än C eller D, eftersom endast dessa två enheter som garanterat alltid finns. Enhet C är operativsystemdisken och enhet D är den temporära lokala disken.

Läs mer om Linux-anpassningar, [virtuella datorer, tillägg och funktioner i Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar i Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå den här återanvändningsmöjligheter operativsystemet VHD: N måste vara *generaliserad*, en åtgärd som tar bort alla instans-specifik identifierare och drivrutiner från en virtuell dator.

### <a name="windows"></a>Windows

Windows OS-diskar är generaliserad med den [sysprep-verktyget](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Om du därefter uppdatera eller konfigurera om Operativsystemet, måste du köra sysprep. 

> [!WARNING]
>  Eftersom uppdateringar kan körs automatiskt när du har kört sysprep, bör du stänga av den virtuella datorn förrän den har distribuerats.  Denna avstängning undviker efterföljande uppdateringar gör instans-specifika ändringar i VHD-OS eller installerade tjänster.

Mer information om hur du kör sysprep finns i [steg för att generalisera en virtuell Hårddisk] ()https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#steps-to-generalize-a-vhd)

### <a name="linux"></a>Linux

Följande två steg generalisera en Linux-VM och distribuera om den som en separat virtuell dator.  Mer information finns i [så här skapar du en avbildning av en virtuell dator eller VHD](../../../virtual-machines/linux/capture-image.md). 

#### <a name="remove-the-azure-linux-agent"></a>Ta bort Azure Linux-agent
1.  Anslut till din Linux-VM med hjälp av en SSH-klient.
2.  I fönstret SSH skriver du följande kommando: <br/>
    `sudo waagent -deprovision+user`
3.  Typ `y` att fortsätta. (Du kan lägga till den `-force` parametern till det föregående kommandot undvika det här steget för bekräftelse.)
4.  När kommandot har slutförts, skriver `exit` att Stäng SSH-klienten.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Samla in en avbildning
1.  Gå till Azure-portalen väljer du resursgruppen (RG) och frigör den virtuella datorn.
2.  Din VHD är generaliserad nu och du kan skapa en ny virtuell dator med hjälp av den här virtuella Hårddisken.


## <a name="create-one-or-more-copies"></a>Skapa en eller flera kopior

Skapa kopior av virtuell dator används ofta för säkerhetskopiering, testning, anpassade redundans eller belastningsutjämning, att erbjuder olika konfigurationer för en lösning och så vidare. Information om hur du duplicera och ladda ned en primär virtuell Hårddisk för att göra en ohanterad klon finns i:

- Virtuell Linux-dator: [Hämta en Linux-VHD från Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [Ladda ned Windows virtuella Hårddisken från Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har konfigurerats kan du är redo att [distribuera en virtuell dator från en virtuell hårddisk](./cpp-deploy-vm-vhd.md).
