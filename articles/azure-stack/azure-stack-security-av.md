---
title: Uppdatera Windows Defender Antivirus på Azure Stack
description: Information om hur antivirus hålls uppdaterad på Azure Stack
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/13/2018
ms.author: patricka
ms.reviewer: fiseraci
ms.openlocfilehash: 3c4be228442bf67ccf16236e36cbf015eca6d4e0
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092066"
---
# <a name="update-windows-defender-antivirus-on-azure-stack"></a>Uppdatera Windows Defender Antivirus på Azure Stack

[Windows Defender Antivirus](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-in-windows-10) är en lösning för program mot skadlig kod som ger skydd för säkerhet och virus. Varje Azure Stack-infrastrukturkomponenter (Hyper-V-värdar och virtuella datorer) är skyddad med Windows Defender Antivirus. För skyddet kräva periodiska uppdateringar av Windows Defender Antivirus definitioner, engine och plattform. Hur uppdateringar tillämpas beror på din konfiguration. 

## <a name="connected-scenario"></a>Scenariot

För program mot skadlig kod definitioner och motor uppdateringar, Azure Stack [uppdatering resursprovidern](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-updates#the-update-resource-provider) laddar ned definitioner för skadlig kod och motoruppdateringar flera gånger per dag. Varje Azure Stack-infrastrukturkomponenter hämtar uppdateringen från resursprovidern uppdatering och tillämpar uppdateringen automatiskt.

Uppdateringar för plattformen för program mot skadlig kod, tillämpa den [månatliga Azure Stack-uppdatering](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-apply-updates). Månatliga Azure Stack uppdateringen innehåller uppdateringar för Windows Defender Antivirus-plattform för månaden.

## <a name="disconnected-scenario"></a>Frånkopplade scenario

 Tillämpa den [månatliga Azure Stack-uppdatering](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-apply-updates). Den månatliga Azure Stack-uppdateringen innehåller Windows Defender Antivirus definitioner, engine och plattformsuppdateringar för månaden.

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure Stack-säkerhet](azure-stack-security-foundations.md)
