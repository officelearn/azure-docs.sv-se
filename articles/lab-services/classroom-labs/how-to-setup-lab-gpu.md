---
title: 'Konfigurera ett labb med GPU: er i Azure Lab Services | Microsoft Docs'
description: Lär dig hur du konfigurerar ett labb med GPU (Graphics Processing Unit) virtuella datorer.
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
ms.openlocfilehash: 9f4bc77366aaeaef9c66467e540219763228d317
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450775"
---
# <a name="set-up-a-lab-with-gpu-graphics-processing-unit-virtual-machine-size"></a>Konfigurera ett labb med GPU (Graphics Processing Unit) storlek för virtuell dator
Den här artikeln visar hur du utför följande uppgifter:

- Välj mellan virtualisering och beräknings-GPU: er
- Se till att lämpliga GPU-drivrutiner är installerade
- Konfigurera RDP-inställningar för att ansluta till en virtuell GPU-dator (VM)

## <a name="choose-between-virtualization-and-compute-gpu-sizes"></a>Välj mellan virtualisering och beräkning av GPU-storlekar
På den första sidan i guiden Skapa labb väljer du **storleken på de virtuella datorer** som behövs för din klass.  

![Välj storlek på virtuell dator](../media/how-to-setup-gpu/lab-gpu-selection.png)

Här har du möjlighet att välja mellan **visualisering** och **beräknings** -GPU: er.  Det är viktigt att du väljer rätt typ av GPU baserat på den program vara som dina studenter använder.  

Enligt beskrivningen i tabellen nedan är **beräkningens** GPU-storlek avsedd för beräknings intensiva program.  Till exempel visar den [djup inlärningen i bearbetnings klass typen för naturliga språk](./class-type-deep-learning-natural-language-processing.md) att använder den **små GPU-storleken (Compute)** .  **Compute** GPU är lämplig för den här typen av klass eftersom eleverna använder djup inlärnings ramverk och verktyg som tillhandahålls av [data science Virtual Machine-avbildningen](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) för att träna djup inlärnings modeller med stora data uppsättningar.

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten GPU (Compute) | <ul><li>6 kärnor</li><li>56 GB RAM</li></ul>  | [Standard_NC6](https://docs.microsoft.com/azure/virtual-machines/nc-series) |Den här storleken passar bäst för dator intensiva program som artificiell intelligens och djup inlärning. |

**Visualiserings** storlekarna är avsedda för grafik intensiva program.  [Klass typen solidworker Engineering](./class-type-solidworks.md) visar till exempel användningen av den **små GPU-storleken (visualisering)** .  GPU för **visualisering** är lämplig för den här typen av klass eftersom eleverna interagerar med SolidWorks-miljön 3D Computer-Aided Design (CAD) för att modellera och visualisera solida objekt.

| Storlek | Kärnor | RAM | Beskrivning | 
| ---- | ----- | --- | ----------- | 
| Liten GPU (visualisering) | <ul><li>6 kärnor</li><li>56 GB RAM</li>  | [Standard_NV6](https://docs.microsoft.com/azure/virtual-machines/nv-series) | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. |
| Medelhög GPU (visualisering) | <ul><li>12 kärnor</li><li>112 GB RAM</li></ul>  | [Standard_NV12](https://docs.microsoft.com/azure/virtual-machines/nv-series?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json) | Den här storleken passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX. |

## <a name="ensure-the-appropriate-gpu-drivers-are-installed"></a>Se till att lämpliga GPU-drivrutiner är installerade
För att kunna dra nytta av GPU-funktionerna i dina virtuella labb datorer måste du se till att lämpliga GPU-drivrutiner är installerade.  I guiden Skapa labb när du väljer en storlek på GPU-datorer, finns det möjlighet att **Installera GPU-drivrutiner**.  

![Installera GPU-drivrutiner](../media/how-to-setup-gpu/lab-gpu-drivers.png)

Det här alternativet är **aktiverat** som standard och säkerställer att de *senaste* driv rutinerna är installerade för den typ av GPU och avbildning som du har valt:
- När du väljer **beräknings** -GPU-storlek drivs dina virtuella labb datorer av GPU för [NVIDIA-Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) .  I det här fallet installeras de senaste [CUDA](https://www.nvidia.com/object/io_69526.html) -drivrutinerna för att möjliggöra data behandling med höga prestanda.
- När du väljer en GPU-storlek för **visualiseringen** drivs dina virtuella labb datorer av teknik för [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU och [Grid](https://www.nvidia.com/content/dam/en-zz/Solutions/design-visualization/solutions/resources/documents1/NVIDIA_GRID_vPC_Solution_Overview.pdf).  I det här fallet installeras de senaste RUTNÄTs driv rutinerna för att möjliggöra grafik intensiva program.

> [!IMPORTANT]
> För att få den bästa användar upplevelsen med **visualiserings** -GPU: er måste du se till att *båda* driv rutinerna är installerade *och* att GPU är aktiverat via RDP-anslutningar.  Se stegen i avsnittet [Aktivera GPU över RDP-anslutning till virtuella Windows-datorer](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).

### <a name="manually-install-drivers"></a>Installera driv rutiner manuellt
Du kan ha scenarier där du behöver installera en annan version av driv rutinerna än den senaste versionen.  Stegen i det här avsnittet visar hur du installerar lämpliga driv rutiner manuellt beroende på om du använder en GPU för **beräkning** eller **visualisering** .

#### <a name="compute-gpu-drivers"></a>Compute GPU-drivrutiner
Följ de här stegen för att installera driv rutiner manuellt för **beräkningens** GPU-storlek:
1. När du [skapar ditt labb](./how-to-manage-classroom-labs.md) **inaktiverar** du inställningen **Installera GPU-drivrutiner** i guiden för att skapa labb.
1. När ditt labb har skapats ansluter du till mallen VM för att installera lämpliga driv rutiner.
   ![Nedladdning av NVIDIA-drivrutiner](../media/how-to-setup-gpu/nvidia-driver-download.png) 
   1. Öppna sidan med NVIDIA: [s driv rutins hämtningar](https://www.nvidia.com/Download/index.aspx)i webbläsaren.
   2. Ange **produkt typen** till **Tesla**.
   3. Ange **produkt serien** till **K-serien**.
   4. Ställ in **produkten** på **Tesla K80**.
   5. Ange **operativ systemet** enligt den typ av bas avbildning som du valde när du skapade labbet.
   6. Ange **CUDA-verktygslådan** till den version av CUDA-drivrutinerna som du behöver.
   7. Klicka på **Sök** för att hitta dina driv rutiner.
   8. Klicka på **Hämta** för att ladda ned installations programmet.
   9. Kör installations programmet så att driv rutinerna är installerade på mallen VM.  
2. Kontrol lera att driv rutinerna är korrekt installerade genom att följa stegen i avsnittet [validera installerade driv rutiner](how-to-setup-lab-gpu.md#validate-installed-drivers). 
3. När du har installerat driv rutinerna och annan program vara som krävs för din klass kan du klicka på **publicera** för att skapa dina studenters virtuella datorer.

> [!NOTE]
> Om du använder en Linux-avbildning kan du också se följande steg för att installera driv rutinerna när du har hämtat installations programmet: [Installera CUDA-drivrutiner på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#install-cuda-drivers-on-n-series-vms).

#### <a name="visualization-gpu-drivers"></a>GPU-drivrutiner för visualisering
Följ dessa steg om du vill installera driv rutiner manuellt för GPU-storlekarna för **visualisering** :
1. När du [skapar ditt labb](./how-to-manage-classroom-labs.md) **inaktiverar** du inställningen **Installera GPU-drivrutiner** i guiden för att skapa labb.
1. När ditt labb har skapats ansluter du till mallen VM för att installera lämpliga driv rutiner.
1. Installera de RUTNÄTs driv rutiner som tillhandahålls av Microsoft på mallen VM genom att följa dessa steg:
   -  [Windows NVIDIA GRID-drivrutiner](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers)
   -  [Linux NVIDIA GRID-drivrutiner](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup?toc=/azure/virtual-machines/linux/toc.json#nvidia-grid-drivers)
  
1. Starta om mallen VM.
1. Kontrol lera att driv rutinerna är korrekt installerade genom att följa stegen i avsnittet [validera installerade driv rutiner](how-to-setup-lab-gpu.md#validate-installed-drivers).
1. Konfigurera RDP-inställningar för att aktivera GPU genom att följa stegen i avsnittet [Aktivera GPU över RDP-anslutning till virtuella Windows-datorer](how-to-setup-lab-gpu.md#enable-gpu-over-rdp-connection-to-windows-vms).
1. När du har installerat driv rutinerna och annan program vara som krävs för din klass kan du klicka på **publicera** för att skapa dina studenters virtuella datorer.

### <a name="validate-installed-drivers"></a>Verifiera installerade driv rutiner
Stegen i det här avsnittet beskriver hur du verifierar att GPU-drivrutinerna är korrekt installerade.

#### <a name="windows-images"></a>Windows-avbildningar
1.  Följ stegen i artikeln som visar hur du [verifierar driv rutins installation i Windows](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation).
1.  Om du använder en **visualiserings** -GPU kan du också:
    - Öppna **kontroll panelen i NVIDIA** om du vill visa och justera dina GPU-inställningar.  Öppna **kontroll panelen > maskin vara** och välj **Nvidia-kontrollpanelen**för att komma åt de här inställningarna.
      ![Öppna NVIDIA kontroll panel](../media/how-to-setup-gpu/control-panel-nvidia-settings.png) 
     - Visa din GPU-prestanda med **aktivitets hanteraren**.  Öppna **aktivitets hanteraren** och välj fliken **prestanda** om du vill visa det. ![ Prestanda för Task Manager GPU](../media/how-to-setup-gpu/task-manager-gpu.png) 

      > [!IMPORTANT]
      > Det går bara att komma åt de **NVIDIA-kontroll panels** inställningarna för **visualiserings** -GPU: er.  Om du försöker öppna NVIDIA- **Kontrollpanelen** för en **beräknings** -GPU visas följande fel: **Inställningar för NVIDIA-visning är inte tillgängliga.  Du använder för närvarande inte en bildskärm som är ansluten till en NVIDIA-GPU.**  På samma sätt tillhandahålls GPU-prestanda informationen i **aktivitets hanteraren** bara för **visualiserings** -GPU: er.

#### <a name="linux-images"></a>Linux-avbildningar
Följ stegen i artikeln som visar hur du [verifierar driv rutins installation på Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#verify-driver-installation).

## <a name="enable-gpu-over-rdp-connection-to-windows-vms"></a>Aktivera GPU över RDP-anslutning till virtuella Windows-datorer
När du använder RDP (Remote Desktop Protocol) för att ansluta till en virtuell Windows-dator som drivs av en **visualiserings** -GPU måste du göra en extra konfiguration så att GPU: n används för bild åter givning (annars används processorn).

Stegen nedan måste slutföras på den virtuella mallen.

1. Konfigurera först RDP-inställningar för att använda GPU: en:

   1. Följ stegen i den här artikeln som visar hur du [konfigurerar GPU-accelererad program åter givning](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-app-rendering).

   2. Följ stegen i den här artikeln som visar hur du [konfigurerar GPU-accelererad ram kodning](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#configure-gpu-accelerated-frame-encoding).

2. Verifiera sedan konfigurationen: 

   1. Följ stegen i den här artikeln som visar hur du [kontrollerar GPU-accelererad app rendering](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-app-rendering).

   2.  Följ stegen i den här artikeln som visar hur du [kontrollerar GPU-accelererad ram kodning](https://docs.microsoft.com/azure/virtual-desktop/configure-vm-gpu#verify-gpu-accelerated-frame-encoding).

3. Slutligen har du nu installerat driv rutinerna och har RDP-inställningar konfigurerade att använda din GPU.  När du har installerat annan program vara som krävs för din klass kan du klicka på **publicera** för att skapa dina studenters virtuella datorer.  När dina studenter ansluter till sina virtuella datorer med RDP kommer deras skriv bord att återges med hjälp av deras GPU för virtuella datorer.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Skapa och hantera klassrumslabb](how-to-manage-classroom-labs.md)
- [SolidWorks-klass typ för Computer-Aided Design (CAD)](class-type-solidworks.md)
- [MATLAB-klass typ](class-type-matlab.md)



