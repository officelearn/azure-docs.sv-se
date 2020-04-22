---
title: Konfigurera GPU för Windows Virtual Desktop - Azure
description: Aktivera GPU-accelererad rendering och kodning i Windows Virtual Desktop.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: 8b675a78041b68210fa7583510582783c506c720
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767041"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop"></a>Konfigurera GPU-acceleration (Graphics Processing Unit) för Windows Virtual Desktop

Windows Virtual Desktop stöder GPU-accelererad rendering och kodning för förbättrad appprestanda och skalbarhet. GPU-acceleration är särskilt viktigt för grafikintensiva appar.

Följ instruktionerna i den här artikeln om du vill skapa en GPU-optimerad virtuell Azure-dator, lägga till den i värdpoolen och konfigurera den för att använda GPU-acceleration för rendering och kodning. Den här artikeln förutsätter att du redan har en Windows Virtual Desktop-klient konfigurerad.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Välj en GPU-optimerad Azure-storlek för virtuella datorer

Azure erbjuder ett antal [GPU-optimerade storlekar för virtuella datorer](/azure/virtual-machines/windows/sizes-gpu). Rätt val för värdpoolen beror på ett antal faktorer, inklusive dina arbetsbelastningar för appen, önskad användarkvalitet och kostnad. I allmänhet erbjuder större och mer kapabla GPU:er en bättre användarupplevelse med en viss användartäthet.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Skapa en värdpool, etablera din virtuella dator och konfigurera en appgrupp

Skapa en ny värdpool med en virtuell dator av den storlek du har valt. Instruktioner finns i [Självstudiekurs: Skapa en värdpool med Azure Marketplace](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop stöder GPU-accelererad rendering och kodning i följande operativsystem:

* Windows 10 version 1511 eller nyare
* Windows Server 2016 eller senare

Du måste också konfigurera en appgrupp eller använda standardappgruppen för skrivbordsappar (med namnet "Desktop Application Group") som skapas automatiskt när du skapar en ny värdpool. Instruktioner finns i [Självstudiekurs: Hantera appgrupper för Windows Virtuella skrivbord](/azure/virtual-desktop/manage-app-groups).

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installera grafikdrivrutiner som stöds i den virtuella datorn

Om du vill dra nytta av GPU-funktionerna i virtuella azure N-serien-datorer i Windows Virtual Desktop måste du installera lämpliga grafikdrivrutiner. Följ instruktionerna på [operativsystem och drivrutiner som stöds](/azure/virtual-machines/windows/sizes-gpu#supported-operating-systems-and-drivers) för att installera drivrutiner från lämplig grafikleverantör, antingen manuellt eller med hjälp av ett Azure VM-tillägg.

Endast drivrutiner som distribueras av Azure stöds för Windows Virtual Desktop. Dessutom, för Virtuella Azure-datorer med NVIDIA GPU: er, endast [NVIDIA GRID-drivrutiner](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) stöds för Windows Virtual Desktop.

Efter installationen av drivrutinen krävs en omstart av den virtuella datorn. Följ verifieringsstegen i instruktionerna ovan för att bekräfta att grafikdrivrutiner har installerats.

## <a name="configure-gpu-accelerated-app-rendering"></a>Konfigurera GPU-accelererad apprendering

Som standard återges appar och skrivbord som körs i konfigurationer med flera sessioner med processorn och utnyttjar inte tillgängliga gpu:er för rendering. Konfigurera grupprincip för sessionsvärden så att GPU-accelererad rendering aktiveras:

1. Anslut till skrivbordet på den virtuella datorn med hjälp av ett konto med lokala administratörsbehörighet.
2. Öppna Start-menyn och skriv "gpedit.msc" för att öppna redigeraren för grupprinciper.
3. Navigera i trädet till**administrativa mallar för** >  **datorkonfiguration** > **Windows Components** > **Fjärrskrivbordssession** > **värd för** > **fjärrsession.**
4. Välj princip **Använd standardgrafikkortet för maskinvara för alla sessioner för Fjärrskrivbordstjänster** och ange den här principen till **Aktiverad** för att aktivera GPU-rendering i fjärrsessionen.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Konfigurera GPU-accelererad ramkodning

Fjärrskrivbord kodar all grafik som återges av appar och stationära datorer (oavsett om den återges med GPU eller med CPU) för överföring till fjärrskrivbordsklienter. Som standard utnyttjar Fjärrskrivbord inte tillgängliga GPU:er för den här kodningen. Konfigurera grupprincip för sessionsvärden för att aktivera GPU-accelererad ramkodning. Fortsätter stegen ovan:

1. Välj princip **Prioritera H.264/AVC 444-grafikläge för fjärrskrivbordsanslutningar** och ange den här principen på **Aktiverad** för att tvinga H.264/AVC 444 codec i fjärrsessionen.
2. Välj princip **Konfigurera H.264/AVC-maskinvarukodning för fjärrskrivbordsanslutningar** och ange den här principen på **Aktiverad** för att aktivera maskinvarukodning för AVC/H.264 i fjärrsessionen.

    >[!NOTE]
    >I Windows Server 2016 anger du alternativet **Föredrar AVC-maskinvarukodning** för att **alltid försöka**.

3. Nu när grupprinciperna har redigerats tvingar du fram en grupprincipuppdatering. Öppna kommandotolken och skriv:

    ```batch
    gpupdate.exe /force
    ```

4. Logga ut från sessionen med fjärrskrivbord.

## <a name="verify-gpu-accelerated-app-rendering"></a>Verifiera GPU-accelererad appåtergivning

Om du vill kontrollera att appar använder grafikprocessorn för rendering provar du något av följande:

* För virtuella Azure-datorer med en `nvidia-smi` NVIDIA GPU använder du verktyget enligt beskrivningen i [Verifiera drivrutinsinstallation](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) för att söka efter GPU-användning när du kör dina appar.
* I operativsystemversioner som stöds kan du använda Aktivitetshanteraren för att söka efter GPU-användning. Välj GPU på fliken "Prestanda" för att se om appar använder GPU.Select the GPU in the "Performance" tab to see whether apps are utilizing the GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Verifiera GPU-accelererad ramkodning

Så här kontrollerar du att fjärrskrivbord använder GPU-accelererad kodning:

1. Anslut till skrivbordet för den virtuella datorn med Windows Virtual Desktop-klienten.
2. Starta Loggboken och navigera till följande nod: **Program och tjänster loggar** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreCDV** > **Operational**
3. Om du vill ta reda på om GPU-accelererad kodning används letar du efter händelse-ID 170. Om du ser "AVC-maskinvarukodare aktiverad: 1" används GPU-kodning.
4. Om du vill ta reda på om AVC 444-läget används letar du efter händelse-ID 162. Om du ser "AVC Tillgänglig: 1 Initial Profil: 2048" då AVC 444 används.

## <a name="next-steps"></a>Nästa steg

Dessa instruktioner bör få dig igång med GPU acceleration på en session värd (en virtuell dator). Några ytterligare överväganden för att aktivera GPU-acceleration över en större värdpool:

* Överväg att använda ett [VM-tillägg](/azure/virtual-machines/extensions/overview) för att förenkla installationen av drivrutiner och uppdateringar för ett antal virtuella datorer. Använd [NVIDIA GPU Driver Extension](/azure/virtual-machines/extensions/hpccompute-gpu-windows) för virtuella datorer med NVIDIA GPU: er och använd AMD GPU Driver Extension (kommer snart) för virtuella datorer med AMD GPU: er.
* Överväg att använda Active Directory-grupprincip för att förenkla grupprincipkonfigurationen för ett antal virtuella datorer. Information om hur du distribuerar grupprinciper i Active Directory-domänen finns i [Arbeta med grupprincipobjekt](https://go.microsoft.com/fwlink/p/?LinkId=620889).
