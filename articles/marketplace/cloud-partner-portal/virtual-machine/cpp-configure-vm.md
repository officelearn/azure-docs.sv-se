---
title: Konfigurera den virtuella datorn som är värd för Microsoft Azure för Azure Marketplace
description: Förklarar hur du ändrar storlek, uppdaterar och generaliserar en virtuell dator som finns på Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: b0ed430098203c5c1a0d00eb7bf17da1be0000cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278100"
---
# <a name="configure-the-azure-hosted-vm"></a>Konfigurera den azure-värddatorn

I den här artikeln beskrivs hur du ändrar storlek, uppdaterar och generaliserar en virtuell dator (VM) som finns på Azure.  Dessa steg är nödvändiga för att förbereda din virtuella dator som ska distribueras från Azure Marketplace.


## <a name="sizing-the-vhds"></a>Dimensionering av virtuella hårddiskar

<!--TD: Check if the following assertion is true. I didn't understand the original content. -->
Om du har valt en av de virtuella datorerna förkonfigurerade med ett operativsystem (och eventuellt ytterligare tjänster), har du redan valt en standardstorlek för Virtuella Azure-datorer, enligt beskrivningen på [fliken Virtuella datorer.](./cpp-skus-tab.md)  Att starta din lösning med ett förkonfigurerat operativsystem är den rekommenderade metoden.  Men om du installerar ett operativsystem manuellt måste du ändra storleken på den primära virtuella hårddisken i vm-avbildningen:

- För Windows bör operativsystemet VHD skapas som en 127-128 GB fast format VHD. 
- För Linux bör denna virtuella hårddisk skapas som en 30-50 GB fast format VHD.

Om den fysiska storleken är mindre än 127-128 GB bör den virtuella hårddisken vara gles. De grundläggande Windows- och SQL Server-avbildningarna som redan uppfyller dessa krav, så ändra inte formatet eller storleken på den virtuella hårddisken som erhållits. 

Datadiskar kan vara så stora som 1 TB. När du bestämmer hur storleken är bör du komma ihåg att kunder inte kan ändra storlek på virtuella hårddiskar i en avbildning vid tidpunkten för distributionen. Virtuella hårddiskar med datadiskar ska skapas som virtuella hårddiskar i fast format. De bör också vara glesa. Datadiskar kan inledningsvis vara tomma eller innehålla data.


## <a name="install-the-most-current-updates"></a>Installera de senaste uppdateringarna

Basavbildningarna för virtuella datorer i operativsystemet innehåller de senaste uppdateringarna fram till det publicerade datumet. Innan du publicerar operativsystemet VHD du har skapat, se till att du uppdaterar operativsystemet och alla installerade tjänster med alla de senaste säkerhets- och underhållskorrigeringarna.

För Windows Server 2016 kör du kommandot **Sök efter uppdateringar.**  Annars läser du så här för äldre versioner av Windows, så [här hämtar du en uppdatering via Windows Update](https://support.microsoft.com/help/3067639/how-to-get-an-update-through-windows-update).  Windows Update installerar automatiskt de senaste viktiga och viktiga säkerhetsuppdateringarna.

För Linux-distributioner hämtas och installeras uppdateringar ofta via ett kommandoradsverktyg eller ett grafiskt verktyg.  Ubuntu Linux tillhandahåller till exempel kommandot [apt-get](https://manpages.ubuntu.com/manpages/cosmic/man8/apt-get.8.html) och [uppdateringshanterarens](https://manpages.ubuntu.com/manpages/cosmic/man8/update-manager.8.html) verktyg för uppdatering av operativsystemet.


## <a name="perform-additional-security-checks"></a>Utföra ytterligare säkerhetskontroller

Du bör upprätthålla en hög säkerhetsnivå för dina lösningsavbildningar på Azure Marketplace.  Följande artikel innehåller en checklista över säkerhetskonfigurationer och säkerhetsprocedurer som hjälper dig med det här målet: [Säkerhetsrekommendationer för Azure Marketplace-avbildningar](https://docs.microsoft.com/azure/security/security-recommendations-azure-marketplace-images).  Några av dessa rekommendationer är specifika för Linux-baserade avbildningar, men de flesta gäller för alla VM-avbildningar. 


## <a name="perform-custom-configuration-and-scheduled-tasks"></a>Utföra anpassade konfigurations- och schemalagda aktiviteter

Om ytterligare konfiguration behövs är den rekommenderade metoden att använda en schemalagd aktivitet som körs vid start för att göra eventuella slutliga ändringar i den virtuella datorn när den har distribuerats.  Tänk också på följande rekommendationer:
- Om det är en run-once-aktivitet rekommenderar vi att uppgiften tas bort när den har slutförts.
- Konfigurationer bör inte förlita sig på andra enheter än C eller D, eftersom endast dessa två enheter som alltid är garanterade att existera. Enhet C är operativsystemdisken och enhet D är den tillfälliga lokala disken.

Mer information om Linux-anpassningar finns i [Tillägg och funktioner för virtuella datorer för Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux).


## <a name="generalize-the-image"></a>Generalisera bilden

Alla avbildningar på Azure Marketplace måste kunna återanvändas på ett generiskt sätt. För att uppnå denna återanvändning måste operativsystemets virtuella hårddisk vara *generaliserad*, en åtgärd som tar bort alla instansspecifika identifierare och programvarudrivrutiner från en virtuell dator.

### <a name="windows"></a>Windows

Windows OS-diskar är generaliserade med [sysprep-verktyget](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview). Om du sedan uppdaterar eller konfigurerar om operativsystemet måste du köra om sysprep. 

> [!WARNING]
>  Eftersom uppdateringar kan köras automatiskt, när du kör sysprep, bör du stänga av den virtuella datorn tills den distribueras.  Den här avstängningen undviker efterföljande uppdateringar från att göra instansspecifika ändringar i VHD OS eller installerade tjänster.

Mer information om hur du kör sysprep finns i [Steg för att generalisera en virtuell hårddisk](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep)

### <a name="linux"></a>Linux

Följande tvåstegsprocess generaliserar en Virtuell Linux-dator och distribuerar om den som en separat virtuell dator. Dessa två steg är bara det väsentliga i processen. Mer information om dessa två steg och varför de måste göras finns i [Så här skapar du en bild av en virtuell dator eller virtuell dator](../../../virtual-machines/linux/capture-image.md). För att skapa den virtuella hårddisken för ditt Azure Marketplace-erbjudande kan du sluta när du når avsnittet "Skapa en virtuell dator från den tagna avbildningen".

#### <a name="remove-the-azure-linux-agent"></a>Ta bort Azure Linux-agenten
1.  Anslut till din virtuella Linux-dator med en SSH-klient.
2.  Skriv följande kommando i SSH-fönstret: <br/>
    `sudo waagent -deprovision+user`
3.  Skriv `y` för att fortsätta. (Du kan `-force` lägga till parametern i föregående kommando undvik det här bekräftelsesteget.)
4.  När kommandot är klart `exit` skriver du för att stänga SSH-klienten.

<!-- TD: I need to add meat and/or references to the following steps -->
#### <a name="capture-the-image"></a>Fånga bilden
1.  Gå till Azure-portalen, välj din resursgrupp (RG) och avallokera den virtuella datorn.
2.  Din virtuella hårddisk är generaliserad nu och du kan skapa en ny virtuell dator med hjälp av den här virtuella hårddisken.


## <a name="create-one-or-more-copies"></a>Skapa en eller flera kopior

Det är ofta användbart att skapa kopior av den virtuella datorn för säkerhetskopiering, testning, anpassad felväxling eller belastningsutjämning, för att erbjuda olika konfigurationer av en lösning och så vidare. Information om hur du duplicerar och hämtar en primär virtuell hårddisk finns i:

- Linux VM: [Ladda ner en Linux VHD från Azure](../../../virtual-machines/linux/download-vhd.md)
- Windows VM: [Hämta en Windows VHD från Azure](../../../virtual-machines/windows/download-vhd.md)


## <a name="next-steps"></a>Nästa steg

När den virtuella datorn har generaliserats, har tagits bort och du har skapat en avbildning av den virtuella datorn är du redo att [distribuera en virtuell dator från en virtuell hårddisk](./cpp-deploy-vm-vhd.md).
