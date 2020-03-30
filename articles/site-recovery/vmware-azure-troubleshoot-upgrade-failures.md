---
title: Felsöka uppgradering av Microsoft Azure Site Recovery Provider
description: Lös vanliga problem som uppstår vid uppgradering av Microsoft Azure Site Recovery-providern.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893903"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Felsöka uppgraderingsfel för Microsoft Azure Site Recovery-providern

Den här artikeln hjälper dig att lösa problem som kan orsaka fel under en uppgradering av Microsoft Azure Site Recovery Provider.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uppgraderingen misslyckas med att rapportera att den senaste site recovery-leverantören redan är installerad

När du uppgraderar Microsoft Azure Site Recovery Provider (DRA) misslyckas uppgraderingen av den enhetliga installationen och felmeddelandet får ett felmeddelande:

Uppgradering stöds inte eftersom en högre version av programvaran redan är installerad.

Så här uppgraderar du:

1. Hämta installationsprogrammet för Enhetlig Microsoft Azure-webbplatsåterställning:
   1. I avsnittet "Länkar till för närvarande sponsrade samlade uppdateringar" i artikeln [Tjänstuppdateringar i](service-updates-how-to.md#links-to-currently-supported-update-rollups) Azure Site Recovery väljer du den leverantör som du uppgraderar till.
   2. Leta reda på avsnittet **Uppdatera information** på den samlade sidan och hämta den samlade uppdateringen för microsoft Azure Site Recovery Unified Setup.

2. Öppna en kommandotolk och navigera till mappen som du hämtade filen Enhetlig installation. Extrahera installationsfilerna från hämtningen med följande kommando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;mappsökväg för de extraherade filerna&gt;.
    
    Exempel kommando:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extraherad

3. I kommandotolken navigerar du till mappen som du extraherade filerna till och kör följande installationskommandon:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Gå tillbaka till mappen som du hämtade unified setup och kör MicrosoftAzureSiteRecoveryUnifiedSetup.exe för att slutföra uppgraderingen. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Uppgraderingsfel på grund av att mappen från tredje part har bytt namn

För att uppgraderingen ska lyckas får mappen med tredje part inte byta namn.

För att lösa problemet.

1. Starta registereditorn (regedit.exe) och öppna grenen HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Kontrollera `Build_Version` nyckelvärdet. Om den är inställd på den senaste versionen minskar du versionsnumret. Till exempel om den senaste versionen är 9,22. \* och `Build_Version` nyckeln inställd på det värdet, sedan minska den till 9,21. \*.
1. Hämta den senaste enhetliga installationen av Microsoft Azure Site Recovery:
   1. I avsnittet "Länkar till för närvarande sponsrade samlade uppdateringar" i artikeln [Tjänstuppdateringar i](service-updates-how-to.md#links-to-currently-supported-update-rollups) Azure Site Recovery väljer du den leverantör som du uppgraderar till.
   2. Leta reda på avsnittet **Uppdatera information** på den samlade sidan och hämta den samlade uppdateringen för microsoft Azure Site Recovery Unified Setup.
1. Öppna en kommandotolk och navigera till mappen som du hämtade Unified Setup-filen till och extrahera installationsfilerna från hämtningen med följande kommando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;mappsökvägen för de extraherade filerna&gt;.

    Exempel kommando:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extraherad

1. I kommandotolken navigerar du till mappen som du extraherade filerna till och kör följande installationskommandon:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Använd Aktivitetshanteraren för att övervaka installationens förlopp. När processen för CX_THIRDPARTY_SETUP. EXE visas inte längre i Aktivitetshanteraren, gå vidare till nästa steg.
1. Kontrollera att C:\thirdparty finns och att mappen innehåller RRD-biblioteken.
1. Gå tillbaka till mappen som du hämtade unified setup och kör MicrosoftAzureSiteRecoveryUnifiedSetup.exe för att slutföra uppgraderingen. 