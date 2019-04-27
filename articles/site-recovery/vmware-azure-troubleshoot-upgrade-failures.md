---
title: Felsök Microsoft Azure Site Recovery-providern uppgradera | Microsoft Docs
description: Förstå och
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: fc50be2a960784895947f3f154a0251f41716fc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565709"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Felsöka uppgraderingsfel för Microsoft Azure Site Recovery-providern

Den här artikeln hjälper dig att lösa problem som kan orsaka fel under en Microsoft Azure Site Recovery-providern uppgradera.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>Uppgraderingen misslyckas reporting senaste Site Recovery-providern har installerats

När du uppgraderar Microsoft Azure Site Recovery-providern (DRA), enhetliga installationsprogrammet för uppgraderingen misslyckas och problem med felmeddelandet:

Uppgradering stöds inte eftersom en senare version av programvaran är redan installerad.

Om du vill uppgradera, använder du följande steg:

1. Ladda ned Microsoft Azure Site Recovery ett enhetligt installationsprogram:
   1. I avsnittet ”länkar till samlade uppdateringar som för närvarande stöds” i den [tjänstuppdateringar i Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) artikel, markerar den provider som du uppgraderar.
   2. På sidan samlad letar du upp den **uppdateringsinformation** avsnittet och ladda ned den samlade uppdateringen för Microsoft Azure Site Recovery enhetliga installationsprogrammet.

2. Öppna en kommandotolk och navigera till den mapp som du hämtade enhetliga installationsprogrammet för filen. Extrahera installationsfilerna från nedladdningen med följande kommando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q/x:&lt;mappsökväg för de extrahera filerna&gt;.
    
    Exempel-kommando:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. Navigera till mappen där du extraherade filerna och kör följande installationskommandon för i Kommandotolken:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES/NORESTART UPGRADE

1. Gå tillbaka till den mapp som du hämtade det enhetliga installationsprogrammet och kör MicrosoftAzureSiteRecoveryUnifiedSetup.exe för att slutföra uppgraderingen. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Uppgraderingen misslyckades på grund av mappen 3 part namnbyte

För att uppgraderingen ska lyckas måste mappen 3 part inte ändras.

Du löser problemet.

1. Starta Registereditorn (regedit.exe) och öppna den HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 grenen.
1. Granska den `Build_Version` nyckelvärdet. Om den är inställd på den senaste versionen, minska versionsnumret. Om exempelvis senaste versionen är 9.22. \* och `Build_Version` nyckeln inställd på värdet och minska det till 9.21.\*.
1. Ladda ned den senaste Microsoft Azure Site Recovery enhetliga installationsprogrammet:
   1. I avsnittet ”länkar till samlade uppdateringar som för närvarande stöds” i den [tjänstuppdateringar i Azure Site Recovery](service-updates-how-to.md##links-to-currently-supported-update-rollups) artikel, markerar den provider som du uppgraderar.
   2. På sidan samlad letar du upp den **uppdateringsinformation** avsnittet och ladda ned den samlade uppdateringen för Microsoft Azure Site Recovery enhetliga installationsprogrammet.
1. Öppna en kommandotolk och navigera till den mapp som du hämtade enhetliga installationsprogrammet för fil- och extrahera installationsfilerna från nedladdningen med följande kommando, MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q/x:&lt;mappsökvägen för den extraherade filerna&gt;.

    Exempel-kommando:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. Navigera till mappen där du extraherade filerna och kör följande installationskommandon för i Kommandotolken:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES/NORESTART

1. Använd Aktivitetshanteraren för att övervaka förloppet för installationen. När processen för CX_THIRDPARTY_SETUP. EXE inte längre visas i Aktivitetshanteraren, gå vidare till nästa steg.
1. Kontrollera att C:\thirdparty finns och att mappen innehåller RRD-bibliotek.
1. Gå tillbaka till den mapp som du hämtade det enhetliga installationsprogrammet och kör MicrosoftAzureSiteRecoveryUnifiedSetup.exe för att slutföra uppgraderingen. 