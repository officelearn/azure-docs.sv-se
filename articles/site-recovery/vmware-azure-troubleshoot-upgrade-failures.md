---
title: Felsök uppgradering av Microsoft Azure Site Recovery-providern
description: Lös vanliga problem när du uppgraderar Microsoft Azure Site Recovery-providern
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: 73309103d422391aae3d72e3b0267d5d5a85515a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953759"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Felsöka uppgraderingsfel för Microsoft Azure Site Recovery-providern

Den här artikeln hjälper dig att lösa problem som kan orsaka fel under uppgraderingen av Microsoft Azure Site Recovery providern.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uppgraderingen kan inte rapportera att den senaste Site Recovery-providern redan är installerad

När du uppgraderar Microsoft Azure Site Recovery-providern (DRA) Miss lyckas uppgraderingen av den enhetliga installationen och det uppstår ett fel meddelande:

Uppgradering stöds inte eftersom en högre version av program varan redan är installerad.

Använd följande steg för att uppgradera:

1. Hämta Microsoft Azure Site Recovery enhetlig installation:
   1. I avsnittet "länkar till de samlade uppdateringar som stöds" i artikeln [tjänst uppdateringar i Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) , väljer du den provider som du uppgraderar till.
   2. På sidan sammanställning letar du upp avsnittet **uppdaterings information** och laddar ned Samlad uppdatering för Microsoft Azure Site Recovery enhetlig installation.

2. Öppna en kommando tolk och navigera till den mapp som du laddade ned den enhetliga installations filen till. Extrahera installationsfilerna från nedladdningen med hjälp av följande kommando, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;mappsökväg för de extraherade filerna&gt;.
    
    Exempel-kommando:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. I kommando tolken navigerar du till mappen där du extraherade filerna och kör följande installations kommandon:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Gå tillbaka till den mapp där du laddade ned den enhetliga installationen och kör MicrosoftAzureSiteRecoveryUnifiedSetup. exe för att slutföra uppgraderingen. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Uppgraderings problem på grund av att mappen från tredje part har bytt namn

För att uppgraderingen ska lyckas får inte mappen tredje part byta namn.

För att lösa problemet.

1. Starta Registereditorn (regedit. exe) och öppna HKEY_LOCAL_MACHINE \SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 gren.
1. Granska `Build_Version` nyckel svärdet. Om den är inställd på den senaste versionen minskar du versions numret. Till exempel, om den senaste versionen är 9,22.\* och `Build_Version`-nyckeln har angetts till det värdet, så minska den till 9,21.\*.
1. Ladda ned den senaste Microsoft Azure Site Recovery Unified-installationen:
   1. I avsnittet "länkar till de samlade uppdateringar som stöds" i artikeln [tjänst uppdateringar i Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) , väljer du den provider som du uppgraderar till.
   2. På sidan sammanställning letar du upp avsnittet **uppdaterings information** och laddar ned Samlad uppdatering för Microsoft Azure Site Recovery enhetlig installation.
1. Öppna en kommando tolk och navigera till mappen som du laddade ned den enhetliga installations filen till och Extrahera installationsfilerna från nedladdningen med hjälp av följande kommando, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x:&lt;mappsökväg för de extraherade filerna&gt;.

    Exempel-kommando:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. I kommando tolken navigerar du till mappen där du extraherade filerna och kör följande installations kommandon:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. Använd aktivitets hanteraren för att övervaka förloppet för installationen. När processen för CX_THIRDPARTY_SETUP. EXE visas inte längre i aktivitets hanteraren, Fortsätt till nästa steg.
1. Kontrol lera att C:\thirdparty finns och att mappen innehåller RRD-biblioteken.
1. Gå tillbaka till den mapp där du laddade ned den enhetliga installationen och kör MicrosoftAzureSiteRecoveryUnifiedSetup. exe för att slutföra uppgraderingen. 