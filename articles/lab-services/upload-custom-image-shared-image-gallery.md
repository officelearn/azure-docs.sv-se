---
title: Azure Lab Services Ladda upp en anpassad avbildning till ett delat avbildnings Galleri
description: Beskriver hur du överför en anpassad avbildning till ett delat avbildnings Galleri. Vår IT-avdelning för universitet söker efter att importera bilder särskilt fördelaktiga.
ms.date: 09/30/2020
ms.topic: how-to
ms.openlocfilehash: cd701215eb375b7f9b867ba05082afc7ed348ff7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91712416"
---
# <a name="upload-a-custom-image-to-shared-image-gallery"></a>Ladda upp en anpassad bild till Shared Image Gallery

Det finns ett delat avbildnings galleri som du kan använda för att importera egna anpassade avbildningar för att skapa labb i Azure Lab Services. Vår IT-avdelning för universitet söker efter att importera bilder särskilt fördelaktigt av följande skäl: 

* Du behöver inte skapa bilder manuellt med hjälp av en labbs mall för virtuella datorer.
* Du kan ladda upp bilder som skapats med andra verktyg, till exempel SCCM, slut punkts hanteraren osv.

I den här artikeln beskrivs steg som kan vidtas för att ta med en anpassad avbildning och använda den i Azure Lab Services. 

> [!IMPORTANT]
> När du flyttar dina avbildningar från en fysisk labb miljö till AZ Labs måste du strukturera om dem appropriatly. Återanvänd inte bara dina befintliga bilder från fysiska labb. <br/>Mer information finns i [flytta från ett fysiskt labb till Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931) blogg inlägg.

## <a name="bring-custom-image-from-a-physical-lab-environment"></a>Hämta anpassad avbildning från en fysisk labb miljö

Följande steg visar hur du importerar en anpassad avbildning som börjar från en fysisk labb miljö. En virtuell hård disk skapas sedan från den här miljön och importeras till det delade avbildnings galleriet i Azure så att den kan användas i Azure Lab Services.

Det finns många alternativ för att skapa en virtuell hård disk från en fysisk labb miljö. Följande steg visar hur du skapar en virtuell hård disk från en virtuell Windows Hyper-V-dator:

1. Börja med en virtuell Hyper-V-dator i din fysiska labb miljö som har skapats från avbildningen.
    1. Den virtuella datorn måste skapas som en virtuell dator i generation 1.
    1. Den virtuella datorn måste ha en fast disk storlek. Du kan också ange disk storleken i det här fönstret. Disk storleken får inte vara större än 128 GB.<br/>    
    Avbildningar med disk storlek > 128 GB stöds inte av Azure Lab Services. 
       
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/connect-virtual-hard-disk.png" alt-text="Anslut virtuell hård disk":::   
    1. Avbildning av den virtuella datorn som vanligt.
1. [Anslut till den virtuella datorn och Förbered den för Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image).
    1. [Ange Windows-konfigurationer för Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#set-windows-configurations-for-azure)
    1. [Kontrol lera de Windows-tjänster som krävs för att säkerställa VM-anslutningen](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#check-the-windows-services)
    1. [Uppdatera inställningar för fjärr skrivbords register](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#update-remote-desktop-registry-settings)
    1. [Konfigurera regler för Windows-brandväggen](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#configure-windows-firewall-rules)
    1. Installera Windows-uppdateringar
    1. [Installera Azure VM-agenten och ytterligare konfiguration som visas här](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#complete-the-recommended-configurations) 
    
    Genom att följa stegen skapas en specialiserad avbildning. Om du skapar en generaliserad avbildning måste du också köra [Sysprep](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image#determine-when-to-use-sysprep). <br/>
        Du bör skapa en specialiserad avbildning om du vill underhålla användar katalogen (som kan innehålla filer, användar konto information osv.) som krävs av program varan som ingår i avbildningen.
1. Eftersom **Hyper-V** skapar en **VHDX** -fil som standard måste du konvertera den till en VHD-fil.
    1. Navigera till **Hyper-V Manager**  ->  **åtgärden**  ->  **Redigera disk**för Hyper-V Manager.
    1. Här kan du välja att **konvertera** disken från en VHDX till en virtuell hård disk
    1. Se till att inte överstiga 128 GB när du försöker expandera disk storleken.        
        :::image type="content" source="./media/upload-custom-image-shared-image-gallery/choose-action.png" alt-text="Anslut virtuell hård disk" i Azure Portal för att välja disk storlek. Som tidigare nämnts måste storleken vara > 128 GB.
1. Ta en ögonblicks bild av den hanterade disken.
    Detta kan göras antingen från PowerShell, med hjälp av Azure Portal eller från Storage Explorer, enligt beskrivningen i [skapa en ögonblicks bild med hjälp av portalen eller PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).
1. Skapa en avbildnings definition och-version i galleriet för delade avbildningar:
    1. [Skapa en avbildnings definition](https://docs.microsoft.com/azure/virtual-machines/windows/shared-images-portal#create-an-image-definition).
    1. Du måste också ange detta om du skapar en specialiserad/generaliserad avbildning.
1. Skapa labbet i Azure Lab Services och välj den anpassade avbildningen från galleriet för delade avbildningar.

    Om du expanderade disk efter att operativ systemet har installerats på den ursprungliga virtuella Hyper-V-datorn måste du också utöka C-enheten i Windows för att använda det oallokerade disk utrymmet. Om du vill göra det loggar du in på mallen VM när labbet har skapats och följer sedan stegen som liknar vad som visas i [utöka en enkel volym](https://docs.microsoft.com/windows-server/storage/disk-management/extend-a-basic-volume). Det finns alternativ för att göra detta via användar gränssnittet samt med PowerShell.

## <a name="next-steps"></a>Nästa steg

* [Översikt över delade avbildnings Galleri](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)
* [Använda delade avbildnings Galleri](how-to-use-shared-image-gallery.md)
