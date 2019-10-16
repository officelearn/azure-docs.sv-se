---
title: Konfigurera Windows Update inställningar så att de fungerar med Azure Uppdateringshantering
description: I den här artikeln beskrivs de Windows Update inställningar som du konfigurerar för att arbeta med Uppdateringshantering
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f50ca9515f12e8c9b5943904c4d0226f2ca3353c
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377569"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurera Windows Update inställningar för Uppdateringshantering

Uppdateringshantering förlitar sig på Windows Update för att ladda ned och installera Windows-uppdateringar. Därför respekterar vi många av de inställningar som används av Windows Update. Om du använder inställningar för att aktivera uppdateringar som inte kommer från Windows hanterar Uppdateringshantering även dessa uppdateringar. Om du vill aktivera nedladdning av uppdateringar innan en uppdaterings distribution sker, kan uppdaterings distributioner gå snabbare och mindre troligt vis överskrida underhålls perioden.

## <a name="pre-download-updates"></a>För hämtnings uppdateringar

Om du vill konfigurera automatisk nedladdning av uppdateringar i grupprincip kan du ange [inställningen konfigurera automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) till **3**. Detta laddar ned de uppdateringar som krävs i bakgrunden, men installerar dem inte. Detta behåller Uppdateringshantering kontroll över scheman, men tillåter att uppdateringar laddas ned utanför Uppdateringshantering underhålls fönstret. Detta kan förhindra att **underhålls fönstret har överskridit** fel i uppdateringshantering.

Du kan också ange detta med PowerShell och köra följande PowerShell på ett system som du vill hämta uppdateringar automatiskt.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="disable-automatic-installation"></a>Inaktivera automatisk installation

Virtuella Azure-datorer har automatisk installation av uppdateringar som är aktiverade som standard. Detta kan medföra att uppdateringar installeras innan du schemalägger dem att installeras av Uppdateringshantering. Du kan inaktivera det här beteendet genom att ange register nyckeln `NoAutoUpdate` till `1`. Följande PowerShell-kodfragment visar ett sätt att göra detta.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

## <a name="configure-reboot-settings"></a>Konfigurera inställningar för omstart

De register nycklar som visas under [konfigurationen av automatiska uppdateringar genom att redigera register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-rej7uijui7jgistry) -och [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan göra att datorerna startas om, även om du inte har angett **omstart** i inställningarna för uppdaterings distribution . Du bör konfigurera dessa register nycklar efter behov för din miljö.

## <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Som standard tillhandahåller Windows Update endast uppdateringar för Windows. Om du aktiverar **ge mig uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows**, får du uppdateringar för andra produkter, inklusive säkerhets korrigeringar för SQL Server eller andra program från den första parten. Det här alternativet kan inte konfigureras av grupprincip. Kör följande PowerShell på de system som du vill aktivera för andra uppdateringar från första part och Uppdateringshantering kommer att använda den här inställningen.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Konfigurations inställningar för WSUS

**Uppdateringshantering** respekterar konfigurations inställningar för WSUS. Listan över WSUS-inställningar som du kan konfigurera för att arbeta med Uppdateringshantering visas nedan.

### <a name="intranet-microsoft-update-service-location"></a>Microsoft-uppdateringstjänst på intranätet

Du kan ange källor för genomsökning och hämtning av uppdateringar under [intranät Microsoft Update tjänstens plats](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Windows Update inställningarna kan du schemalägga en uppdaterings distribution genom att följa anvisningarna under [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md)