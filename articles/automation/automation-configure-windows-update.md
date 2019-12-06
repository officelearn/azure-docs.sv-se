---
title: Konfigurera Windows Update inställningar så att de fungerar med Azure Uppdateringshantering
description: I den här artikeln beskrivs de Windows Update inställningar som du konfigurerar för att arbeta med Azure Uppdateringshantering.
services: automation
ms.service: automation
ms.subservice: update-management
author: mgoedtel
ms.author: magoedte
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 804f42121293e142cf77ad73c4aab36e62e3242d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850422"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurera Windows Update inställningar för Uppdateringshantering

Azure Uppdateringshantering förlitar sig på Windows Update för att ladda ned och installera Windows-uppdateringar. Det innebär att Uppdateringshantering respekterar många av de inställningar som används av Windows Update. Om du använder inställningar för att aktivera uppdateringar som inte kommer från Windows kan Uppdateringshantering även hantera dessa uppdateringar. Om du vill aktivera nedladdning av uppdateringar innan en uppdaterings distribution sker kan uppdaterings distributionen bli snabbare, mer effektiv och mindre troligt att överskrida underhålls perioden.

## <a name="pre-download-updates"></a>För nedladdning av uppdateringar

Om du vill konfigurera automatisk nedladdning av uppdateringar i grupprincip anger du [inställningen konfigurera automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) till **3**. Med den här inställningen kan du hämta de uppdateringar som krävs i bakgrunden, men de installeras inte. På så sätt har Uppdateringshantering fortfarande kontroll över scheman, men uppdateringar kan hämtas utanför Uppdateringshantering underhålls fönstret. Det här beteendet förhindrar att "underhålls fönster" har överskridits "fel i Uppdateringshantering.

Du kan också aktivera den här inställningen genom att köra följande PowerShell-kommando på ett system som du vill konfigurera för automatisk nedladdning av uppdateringar:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Inaktivera automatisk installation

Automatisk installation av uppdateringar är aktiverat som standard på virtuella datorer i Azure. Detta kan leda till att uppdateringar installeras innan du schemalägger dem för installation genom att Uppdateringshantering. Du kan inaktivera det här beteendet genom att ange `NoAutoUpdate` register nyckeln som `1`. Följande PowerShell-kodfragment visar hur du gör detta:

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Konfigurera inställningar för omstart

De register nycklar som visas i [Konfigurera automatiska uppdateringar genom att redigera register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) -och [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan göra att datorerna startas om, även om du **inte anger starta** om i **uppdaterings distributions** inställningarna. Du bör konfigurera dessa register nycklar så att de passar bäst för din miljö.

## <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Windows Update innehåller som standard endast uppdateringar för Windows. Om du aktiverar alternativet **för att ge mig uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows** -inställningar får du också uppdateringar för andra produkter, inklusive säkerhets korrigeringar för Microsoft SQL Server och annan Microsoft-programvara. Det här alternativet kan inte konfigureras av grupprincip. Kör följande PowerShell-kommando på de system som du vill aktivera andra Microsoft-uppdateringar på. Uppdateringshantering är kompatibel med den här inställningen.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Konfigurations inställningar för WSUS

Uppdateringshantering följer inställningarna för Windows Server Update Services (WSUS). De WSUS-inställningar som du kan konfigurera för att arbeta med Uppdateringshantering visas nedan.

### <a name="intranet-microsoft-update-service-location"></a>Microsoft-uppdateringstjänst på intranätet

Du kan ange källor för genomsökning och hämtning av uppdateringar under [Ange intranät Microsoft Update tjänst plats](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Windows Update inställningarna kan du schemalägga en uppdaterings distribution genom att följa anvisningarna i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
