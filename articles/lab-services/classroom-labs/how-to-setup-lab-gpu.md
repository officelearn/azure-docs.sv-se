---
title: 'Konfigurera ett labb med GPU: er i Azure Lab Services | Microsoft Docs'
description: Lär dig hur du konfigurerar ett labb med virtuella GPU-datorer (Graphics Processing Unit).
services: lab-services
documentationcenter: na
author: nicolela
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2020
ms.author: nicolela
ms.openlocfilehash: ebc3d34e23c740bc6307e26a2ac0308062367119
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84628897"
---
# <a name="set-up-a-lab-with-gpu-virtual-machines"></a>Konfigurera ett labb med virtuella GPU-datorer

Den här artikeln visar hur du utför följande uppgifter:

- Välj mellan *visualisering* och GPU ( *Compute* Graphics Processing Unit).
- Kontrol lera att rätt GPU-drivrutiner är installerade.
- Konfigurera inställningar för Remote Desktop Protocol (RDP) för att ansluta till en virtuell GPU-dator (VM).

## <a name="choose-between-visualization-and-compute-gpu-sizes"></a>Välj mellan visualiserings-och beräknings-GPU-storlekar
På den första sidan i guiden för att skapa labb i guiden **vilken virtuell dator storlek du behöver?** väljer du storleken på de virtuella datorer som behövs för klassen.  

![Skärm bild av fönstret "nytt labb" för att välja en storlek på virtuell dator](../media/how-to-setup-gpu/lab-gpu-selection.png)

I den här processen har du möjlighet att välja antingen **visualisering** eller **Compute** GPU: er.  Det är viktigt att välja vilken typ av GPU som är baserad på den program vara som dina studenter kommer att använda.  

Som det beskrivs i följande tabell är *beräkningens* GPU-storlek avsedd för beräknings intensiva program.  Den [djup inlärningen i bearbetnings klass typen för naturligt språk](./class-type-deep-learning-natural-language-processing.md) använder till exempel den **små GPU-storleken (Compute)** .  Compute-GPU är lämplig för den här typen av klass eftersom studenter använder djup inlärnings ramverk och verktyg som tillhandahålls av [data science Virtual Machine-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) för att träna djup inlärnings modeller med stora data uppsättningar.

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten GPU (Compute) | -&nbsp;6 &nbsp; kärnor<br>-&nbsp;56 &nbsp; GB &nbsp; ram  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Den här storleken passar bäst för dator intensiva program, till exempel artificiell intelligens (AI) och djup inlärning. |

GPU-storlekarna för *visualisering* är avsedda för grafik intensiva program.  [Klass typen solidworker Engineering](./class-type-solidworks.md) visar till exempel användningen av den **små GPU-storleken (visualisering)** .  GPU för visualisering är lämplig för den här typen av klass eftersom eleverna interagerar med SolidWorks-miljön 3D Computer-Aided Design (CAD) för att modellera och visualisera solida objekt.

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten GPU (visualisering) | -&nbsp;6 &nbsp; kärnor<br>-&nbsp;56 &nbsp; GB &nbsp; ram  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Den här storleken passar bäst för fjärrvisualisering, strömning, spel och kodning som använder ramverk som OpenGL och DirectX. |
| Medelhög GPU (visualisering) | -&nbsp;12 &nbsp; kärnor<br>-&nbsp;112 &nbsp; GB &nbsp; ram  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för fjärrvisualisering, strömning, spel och kodning som använder ramverk som OpenGL och DirectX. |

## <a name="ensure-that-the-appropriate-gpu-drivers-are-installed"></a>Kontrol lera att rätt GPU-drivrutiner är installerade
För att dra nytta av GPU-funktionerna i dina virtuella labb datorer, se till att rätt GPU-drivrutiner är installerade.  När du väljer en storlek på GPU-datorer i guiden för att skapa labb kan du välja alternativet **Installera GPU-drivrutiner** .  

![Skärm bild av "New Lab" som visar alternativet "installera GPU-drivrutiner"](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Som du ser i föregående bild är det här alternativet aktiverat som standard, vilket säkerställer att de *senaste* driv rutinerna är installerade för den typ av GPU och avbildning som du har valt.
- När du väljer en *beräknings* -GPU-storlek drivs dina virtuella labb datorer av GPU för [NVIDIA-Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  I det här fallet installeras de senaste [CUDA-drivrutinerna (Compute Unified Device Architecture)](https://www.nvidia.com/object/io_69526.html) , vilket möjliggör data behandling med höga prestanda.
- När du väljer en GPU-storlek för *visualiseringen* drivs dina virtuella labb datorer av teknik för [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU och [Grid](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  I det här fallet installeras de senaste RUTNÄTs driv rutinerna, vilket möjliggör användning av grafik intensiva program.

> [!IMPORTANT]
> För att få den bästa användar upplevelsen med *visualiserings* -GPU: er, se till att *båda* driv rutinerna är installerade *och* att GPU är aktiverat via RDP-anslutningar Mer information finns i avsnittet [Aktivera GPU över RDP-anslutning till virtuella Windows-datorer](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) i den här artikeln.

### <a name="install-the-drivers-manually"></a>Installera driv rutinerna manuellt
Du kan behöva installera en annan driv rutins version än den senaste versionen.  Det här avsnittet visar hur du installerar lämpliga driv rutiner manuellt, beroende på om du använder en *beräknings* -GPU eller en *visualiserings* -GPU.

#### <a name="install-the-compute-gpu-drivers"></a>Installera Compute GPU-drivrutinerna

Om du vill installera driv rutiner manuellt för beräknings-GPU-storleken gör du följande:

1. I guiden Skapa labb, när du [skapar ditt labb](./how-to-manage-classroom-labs.md), inaktiverar du inställningen **Installera GPU-drivrutiner** .

1. När ditt labb har skapats ansluter du till mallen VM för att installera lämpliga driv rutiner.

   ![Skärm bild av sidan med nedladdnings bara NVIDIA-drivrutiner](../media/how-to-setup-gpu/nvidia-driver-download.png) 

   a. I en webbläsare går du till [sidan med nedladdnings bara NVIDIA-drivrutiner](https://www.nvidia.com/Download/index.aspx).  
   b. Ange **produkt typen** till **Tesla**.  
   c. Ange **produkt serien** till **K-serien**.  
   d. Ange **operativ systemet** enligt den typ av bas avbildning som du valde när du skapade labbet.  
   e. Ange **CUDA Toolkit** till den version av CUDA-drivrutinen som du behöver.  
   f. Välj **Sök** för att söka efter driv rutinerna.  
   g. Klicka på **Hämta** för att ladda ned installations programmet.  
   h. Kör installations programmet så att driv rutinerna är installerade på mallen VM.  
1. Kontrol lera att driv rutinerna har installerats korrekt genom att följa anvisningarna i avsnittet [validera installerade driv rutiner](how-to-setup-lab-gpu.md#validate-the-installed-drivers) . 
1. När du har installerat driv rutinerna och annan program vara som krävs för din klass väljer du **publicera** för att skapa dina studenters virtuella datorer.

> [!NOTE]
> Om du använder en Linux-avbildning, efter att du har hämtat installations programmet, installerar du driv rutinerna genom att följa anvisningarna i [Installera CUDA-drivrutiner på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="install-the-visualization-gpu-drivers"></a>Installera GPU-drivrutinerna för visualisering

Så här installerar du driv rutiner för GPU-storleken för visualiseringen manuellt:

1. I guiden Skapa labb, när du [skapar ditt labb](./how-to-manage-classroom-labs.md), inaktiverar du inställningen **Installera GPU-drivrutiner** .
1. När ditt labb har skapats ansluter du till mallen VM för att installera lämpliga driv rutiner.
1. Installera de RUTNÄTs driv rutiner som tillhandahålls av Microsoft på mallen VM genom att följa anvisningarna för ditt operativ system:
   -  [Windows NVIDIA GRID-drivrutiner](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID-drivrutiner](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Starta om mallen VM.
1. Kontrol lera att driv rutinerna har installerats korrekt genom att följa anvisningarna i avsnittet [validera installerade driv rutiner](how-to-setup-lab-gpu.md#validate-the-installed-drivers) .
1. Konfigurera RDP-inställningar för att aktivera GPU-anslutningen genom att följa anvisningarna i avsnittet [Aktivera GPU över RDP-anslutning till virtuella Windows-datorer](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms) .
1. När du har installerat driv rutinerna och annan program vara som krävs för din klass väljer du **publicera** för att skapa dina studenters virtuella datorer.

### <a name="validate-the-installed-drivers"></a>Validera installerade driv rutiner
I det här avsnittet beskrivs hur du verifierar att GPU-drivrutinerna är korrekt installerade.

#### <a name="windows-images"></a>Windows-avbildningar
1.  Följ anvisningarna i avsnittet "verifiera driv rutins installation" i [Installera nVidia GPU-drivrutiner på virtuella datorer i N-serien som kör Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Om du använder en *visualiserings* -GPU kan du också:
    - Visa och justera dina GPU-inställningar på kontroll panelen i NVIDIA. Det gör du genom att välja **maskin vara**på **kontroll panelen i Windows**och sedan välja **NVIDIA kontroll panel**.

      ![Skärm bild av kontroll panelen i Windows med länken NVIDIA-Kontrollpanelen](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 

     - Visa GPU-prestanda med hjälp av **aktivitets hanteraren**.  Det gör du genom att välja fliken **prestanda** och sedan välja **GPU** -alternativet.

       ![Skärm bild som visar fliken för GPU-prestanda i Task Manager](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Inställningarna för NVIDIA-Kontrollpanelen kan bara användas för *visualiserings* -GPU: er.  Om du försöker öppna NVIDIA-Kontrollpanelen för en beräknings-GPU får du följande fel meddelande: "NVIDIA-bildskärms inställningar är inte tillgängliga.  Du använder för närvarande inte en bildskärm som är ansluten till en NVIDIA-GPU. "  På samma sätt tillhandahålls GPU-prestanda informationen i aktivitets hanteraren endast för visualiserings-GPU: er.

#### <a name="linux-images"></a>Linux-avbildningar
Följ anvisningarna i avsnittet "verifiera driv rutins installation" i [Installera nVidia GPU-drivrutiner på virtuella datorer i N-serien som kör Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>Aktivera GPU över RDP-anslutning till virtuella Windows-datorer
När du använder RDP för att ansluta till en virtuell Windows-dator som drivs av en *visualiserings* -GPU måste du göra en extra konfiguration så att GPU: n används för att återge grafik. Annars används processorn för att återge grafik.

Gör följande på mallen VM:

1. Konfigurera RDP-inställningar för användning av GPU: n.

   a. Följ anvisningarna i [Konfigurera GPU-accelererad app rendering](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).  
   b. Följ instruktionerna i [Konfigurera GPU-accelererad ram kodning](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

1. Verifiera konfigurationen. 

   a. Följ instruktionerna i [kontrol lera åter givning av GPU-accelererade appar](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).  
   b. Följ instruktionerna i [kontrol lera en GPU-accelererad ram kodning](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

1. Du har nu installerat driv rutinerna och RDP-inställningarna som kon figurer ATS för att använda din GPU.  När du har installerat den andra program varan som krävs för din klass kan du välja **publicera** för att skapa dina studenters virtuella datorer.  

När dina studenter ansluter till sina virtuella datorer med hjälp av RDP, kommer deras skriv bord att återges enligt deras GPU för virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera klassrumslabb](how-to-manage-classroom-labs.md)
- [SolidWorks-klass typ för Computer-Aided Design (CAD)](class-type-solidworks.md)
- [Klass typ för MATLAB (mat ris labb)](class-type-matlab.md)



