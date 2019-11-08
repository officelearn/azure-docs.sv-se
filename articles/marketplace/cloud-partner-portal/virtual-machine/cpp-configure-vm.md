---
title: Konfigurera den virtuella datorn med Microsoft Azure-värd för Azure Marketplace
description: Förklarar hur du ändrar storlek, uppdaterar och generaliserar en virtuell dator som finns på Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: ce7fe49b07dc250a9f56ff73229e347b997f0cc0
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824497"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurera den virtuella Azure-värdbaserade datorn

Den här artikeln förklarar hur du ändrar storlek, uppdaterar och generaliserar en virtuell dator (VM) som finns på Azure.  Dessa steg är nödvändiga för att förbereda din virtuella dator så att den distribueras från Azure Marketplace.


## <a name="sizing-the-vhds"></a>Ändra storlek på de virtuella hård diskarna

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Om du har valt en av de virtuella datorer som är förkonfigurerade med ett operativ system (och eventuellt ytterligare tjänster) har du redan valt en standard storlek för virtuella Azure-datorer, enligt beskrivningen i [fliken SKU: er för virtuella datorer](./cpp-skus-tab.md).  Den rekommenderade metoden är att starta din lösning med ett förkonfigurerat operativ system.  Om du installerar ett operativ system manuellt måste du dock ändra den primära virtuella hård disken i din VM-avbildning:

- För Windows bör den virtuella hård disken för operativ systemet skapas som en virtuell hård disk på 127-128 GB. 
- För Linux bör denna virtuella hård disk skapas som en virtuell hård disk på 30-50 GB.

Om den fysiska storleken är mindre än 127-128 GB ska den virtuella hård disken vara sparse. De grundläggande Windows-och SQL Server-avbildningarna uppfyller redan dessa krav, så ändra inte formatet eller storleken på den virtuella hård disken som hämtades. 

Data diskar kan vara så stora som 1 TB. Kom ihåg att kunder inte kan ändra storlek på VHD: er i en avbildning vid tidpunkten för distributionen när de bestämmer sig för deras storlek. Datadisk-VHD: er ska skapas som virtuella hård diskar med fast format. De bör också vara glesa. Data diskar kan inlednings vis vara tomma eller innehålla data.


## <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

De grundläggande avbildningarna av operativ systemets virtuella datorer innehåller de senaste uppdateringarna upp till Publicerings datum. Innan du publicerar den virtuella hård disk som du har skapat måste du uppdatera operativ systemet och alla installerade tjänster med alla de senaste säkerhets-och underhålls korrigeringarna.

För Windows Server 2016 kör du kommandot **Sök efter uppdateringar** .  För äldre versioner av Windows, se [hur du får en uppdatering via Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update kommer automatiskt att installera de senaste kritiska och viktiga säkerhets uppdateringarna.

För Linux-distributioner hämtas och installeras uppdateringar ofta med hjälp av ett kommando rads verktyg eller grafiskt verktyg.  Till exempel tillhandahåller Ubuntu Linux [apt-get-](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) kommandot och [Update Manager](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) -verktyget för att uppdatera operativ systemet.


## <a name="perform-additional-security-checks"></a>Utför ytterligare säkerhets kontroller

Du bör ha en hög säkerhets nivå för dina lösnings avbildningar på Azure Marketplace.  Följande artikel innehåller en check lista över säkerhetskonfigurationer och procedurer som hjälper dig i det här målet: [säkerhets rekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Några av de här rekommendationerna är speciella för Linux-baserade avbildningar, men de flesta gäller för alla VM-avbildningar. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utför anpassad konfiguration och schemalagda aktiviteter

Om ytterligare konfiguration krävs är den rekommenderade metoden att använda en schemalagd aktivitet som körs vid start för att göra eventuella slutliga ändringar i den virtuella datorn efter att den har distribuerats.  Tänk också på följande rekommendationer:
- Om det är en körnings uppgift rekommenderar vi att du tar bort uppgiften när den har slutförts.
- Konfigurationer bör inte förlita sig på andra enheter än C eller D, eftersom endast dessa två enheter som garanterat alltid finns. Enhet C är operativ systemets disk och enhet D är den tillfälliga lokala disken.

Mer information om Linux-anpassningar finns i [tillägg och funktioner för virtuella datorer för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalisera avbildningen

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå den här omanvändbarheten måste det virtuella operativ systemet vara *generaliserat*, en åtgärd som tar bort alla instans identifierare och program driv rutiner från en virtuell dator.

### <a name="windows"></a>Windows

Windows OS-diskar generaliseras med [Sysprep-verktyget](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Om du senare uppdaterar eller omkonfigurerar operativ systemet måste du köra Sysprep igen. 

> [!WARNING]
>  Eftersom uppdateringar kan köras automatiskt när du har kört Sysprep, bör du stänga av den virtuella datorn tills den har distribuerats.  Den här avstängningen kommer att undvika efterföljande uppdateringar från att göra instansbaserade ändringar i VHD-OS eller installerade tjänster.

Mer information om hur du kör Sysprep finns i [steg för att generalisera en virtuell hård disk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

I följande två stegs process generaliserar en virtuell Linux-dator och distribuerar den igen som en separat virtuell dator. De här två stegen är bara grunderna för processen. Mer information om de här två stegen och varför de måste utföras finns i [så här skapar du en avbildning av en virtuell dator eller virtuell hård disk](../../../virtual-machines/linux/capture-image.md). För att kunna skapa den virtuella hård disken för Azure Marketplace-erbjudandet kan du stoppa när du når avsnittet "skapa en virtuell dator från avbildningen".

#### <a name="remove-the-azure-linux-agent"></a>Ta bort Azure Linux-agenten
1.  Anslut till din virtuella Linux-dator med hjälp av en SSH-klient.
2.  I SSH-fönstret skriver du följande kommando: <br/>
    `sudo waagent -deprovision+user`
3.  Skriv `y` för att fortsätta. (Du kan lägga till parametern `-force` i föregående kommando, Undvik det här bekräftelse steget.)
4.  När kommandot har slutförts skriver du `exit` för att stänga SSH-klienten.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Avbilda avbildningen
1.  Gå till Azure Portal, välj din resurs grupp (RG) och ta bort den virtuella datorn.
2.  Din virtuella hård disk är generaliserad nu och du kan skapa en ny virtuell dator med hjälp av den här virtuella hård disken.


## <a name="create-one-or-more-copies"></a>Skapa en eller flera kopior

Att skapa kopior av virtuella datorer är ofta användbart för säkerhets kopiering, testning, anpassad redundans eller belastnings utjämning, för att erbjuda olika konfigurationer av en lösning och så vidare. Information om hur du duplicerar och laddar ned en primär virtuell hård disk, för att göra en ohanterad klon, finns i:

- Virtuell Linux-dator: [Ladda ned en Linux-VHD från Azure](../../../virtual-machines/linux/download-vhd.md)
- Virtuell Windows-dator: [Hämta en Windows-VHD från Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har generaliserats har frigjorts och du har skapat en avbildning av den virtuella datorn, är du redo att [distribuera en virtuell dator från en virtuell hård disk](./cpp-deploy-vm-vhd.md).
