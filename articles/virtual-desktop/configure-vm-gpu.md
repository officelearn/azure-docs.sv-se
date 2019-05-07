---
title: Konfigurera GPU för Windows virtuella skrivbord Preview – Azure
description: Hur du aktiverar GPU-accelererade rendering och kodning i förhandsversion för virtuella skrivbord i Windows.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159577"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Konfigurera grafikprocessorer (GPU) unit acceleration för Windows Virtual Desktop-förhandsversion

Windows Virtual Desktop Preview stöder GPU-accelererade rendering och kodning för förbättrad prestanda och skalbarhet. GPU-acceleration är särskilt viktigt för grafikintensiva appar.

Följ anvisningarna i den här artikeln för att skapa en GPU-optimerad för Azure-dator, lägga till den i din pool för värden och konfigurera den att använda GPU-acceleration för rendering och kodning. Den här artikeln förutsätter att du redan har ett virtuellt skrivbord i Windows-klient som konfigurerats.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Välj en GPU-optimerad Azure VM-storlek

Azure erbjuder ett antal [GPU-optimerad storlekar för virtuella datorer](/azure/virtual-machines/windows/sizes-gpu). Det rätta valet för värd poolen beror på ett antal faktorer, bland annat viss app arbetsbelastningar, önskad kvaliteten på användarupplevelse och kostnad. I allmänhet erbjuder större och mer kompatibla GPU: er en bättre användarupplevelse med en viss användare densitet.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Skapa en pool med värden, etablera den virtuella datorn och konfigurera en appgrupp

Skapa en ny värd-pool med hjälp av en virtuell dator av samma storlek som du har valt. Anvisningar finns i [självstudien: Skapa en värd-pool med Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop Preview stöder GPU-accelererade rendering och kodning i följande operativsystem:

* Windows 10 version 1511 eller senare
* Windows Server 2016 eller senare

Du måste också konfigurera en appgrupp, eller använda skrivbordsappen standardgruppen (med namnet ”Desktop programgruppen”) som skapas automatiskt när du skapar en ny värd-pool. Anvisningar finns i [självstudien: Hantera app-grupper för Windows Virtual Desktop förhandsversion](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows virtuella Desktop Preview stöder endast ”Desktop” app grupptypen för GPU-aktiverad värd pooler. App-grupper av typen ”RemoteApp” stöds inte för GPU-aktiverad värd pooler.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installera stöds grafikdrivrutiner i den virtuella datorn

Om du vill dra nytta av GPU-funktionerna i Azure virtuella datorer i N-serien i förhandsversion för virtuella skrivbord i Windows, måste du installera NVIDIA-grafikdrivrutiner. Följ anvisningarna på [installera NVIDIA GPU-drivrutiner på N-serien virtuella datorer som kör Windows](/azure/virtual-machines/windows/n-series-driver-setup) installerar drivrutiner, antingen manuellt eller med hjälp av den [NVIDIA GPU-drivrutinen tillägget](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Observera att det bara [NVIDIA GRID-drivrutiner](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribueras av Azure har stöd för Windows Virtual Desktop-förhandsversion.

Efter installation av drivrutiner krävs en omstart av virtuella datorer. Använd verifieringsstegen i ovanstående anvisningar för att bekräfta att grafikdrivrutiner har installerats.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurera GPU-accelererade app-återgivning

Som standard, appar och stationära datorer som körs i flera sessionskonfigurationer återges med CPU och använda inte tillgängliga GPU: er för rendering. Konfigurera en grupprincip för värd för fjärrskrivbordssession att aktivera GPU-accelererade återgivning:

1. Ansluta till skrivbordet för den virtuella datorn med ett konto med lokal administratörsbehörighet.
2. Öppna den Start-menyn och Skriv ”gpedit.msc” om du vill öppna redigeraren.
3. Navigera i trädet till **Datorkonfiguration** > **Administrationsmallar** > **Windows-komponenter**  >   **Fjärrskrivbordstjänster** > **värd för fjärrskrivbordssession** > **fjärrsession miljö**.
4. Välj princip **använda grafikkortet maskinvara standard för alla Remote Desktop Services-sessioner** och ställa in principen **aktiverad** att aktivera GPU-rendering i fjärrsessionen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurera GPU-accelererade ramens kodning

Fjärrskrivbord kodar alla bilder som renderas av appar och skrivbord (om återges med GPU eller CPU) för överföring till fjärrskrivbordsklienter. Som standard utnyttja fjärrskrivbord inte grafikprocessorer som är tillgängliga för den här kodningen. Konfigurera en grupprincip för värd för fjärrskrivbordssession att aktivera GPU-accelererade ramens kodning. Du kan fortsätta stegen ovan:

1. Välj princip **prioritera H.264/AVC 444 ska hämtas för anslutningar till fjärrskrivbord** och ställa in principen **aktiverad** att tvinga H.264/AVC 444 codec i fjärrsessionen.
2. Välj princip **Konfigurera H.264/AVC maskinvara kodning för anslutningar till fjärrskrivbord** och ställa in principen **aktiverad** att aktivera maskinvara kodning för AVC/H.264 i fjärrsessionen.

    >[!NOTE]
    >I Windows Server 2016, ställer du in alternativet **föredrar AVC maskinvara kodning** till **Försök alltid**.

3. Nu när grupprinciper har redigerats, kan du tvinga en uppdatering av Grupprincip. Öppna Kommandotolken och skriv:

    ```batch
    gpupdate.exe /force
    ```

4. Logga ut från fjärrskrivbordssessionen.

## <a name="verify-gpu-accelerated-app-rendering"></a>Kontrollera GPU-accelererade app-återgivning

Prova något av följande för att verifiera att appar använder GPU för återgivning:

* Använd den `nvidia-smi` verktyget som beskrivs i [verifiera drivrutinsinstallation](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) att söka efter GPU användning när du kör dina appar.
* Du kan använda Aktivitetshanteraren på vilka operativsystemversioner, för att söka efter GPU-användning. Välj GPU: på fliken ”prestanda” för att se om appar använder GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Kontrollera GPU-accelererade ramens kodning

För att verifiera att fjärrskrivbord använder GPU-accelererade kodning:

1. Ansluta till skrivbordet för den virtuella datorn med virtuella skrivbord i Windows-klienten.
2. Starta Loggboken och gå till följande nod: **Program- och tjänstloggar** > **Microsoft** > **Windows** > **RemoteDesktopServices RdpCoreTS**  >  **Operativa**
3. Sök efter händelse-ID 170 för att avgöra om GPU-accelererade kodning används. Om du ser ”AVC maskinvara encoder aktiverat: 1 ”och sedan GPU kodning används.
4. Sök efter händelse-ID 162 för att avgöra om AVC 444 läge används. Om du ser ”AVC tillgänglig: 1 inledande profil: 2 048 ”och sedan AVC 444 används.

## <a name="next-steps"></a>Nästa steg

De här anvisningarna bör ha du igång med GPU-acceleration på en enda session värd VM. Vissa ytterligare överväganden för att aktivera GPU-acceleration i en större pool för värden:

* Överväg att använda den [NVIDIA GPU-drivrutinen tillägget](/azure/virtual-machines/extensions/hpccompute-gpu-windows) att förenkla drivrutinsinstallation och uppdateringar i ett antal virtuella datorer.
* Överväg att använda Grupprincip i Active Directory för att förenkla konfigurationen för Grupprincip över ett antal virtuella datorer. Information om hur du distribuerar en Grupprincip i Active Directory-domän finns i [arbeta med grupprincipobjekt](https://go.microsoft.com/fwlink/p/?LinkId=620889).
