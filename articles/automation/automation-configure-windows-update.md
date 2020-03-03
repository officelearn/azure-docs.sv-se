---
title: Konfigurera Windows Update inställningar så att de fungerar med Azure Uppdateringshantering
description: I den här artikeln beskrivs de Windows Update inställningar som du konfigurerar för att arbeta med Azure Uppdateringshantering.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226262"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurera Windows Update inställningar för Uppdateringshantering

Azure Uppdateringshantering använder [Windows Update klienten](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) för att ladda ned och installera Windows-uppdateringar. Det finns vissa inställningar som används av den Windows Update klienten vid anslutning till Windows Server Update Services (WSUS) eller Windows Update. Många av de här inställningarna kan hanteras med:

- redigerare för lokalt grupprincipobjekt
- Grupprincip
- PowerShell
- Redigera registret direkt

Uppdateringshantering respekterar många av de inställningar som anges för att kontrol lera Windows Update-klienten. Om du använder inställningar för att aktivera uppdateringar som inte kommer från Windows kan Uppdateringshantering även hantera dessa uppdateringar. Om du vill aktivera nedladdning av uppdateringar innan en uppdaterings distribution sker kan uppdaterings distributionen bli snabbare, mer effektiv och mindre troligt att överskrida underhålls perioden.

## <a name="pre-download-updates"></a>För nedladdning av uppdateringar

Om du vill konfigurera automatisk nedladdning av uppdateringar, men inte installera dem automatiskt, kan du använda grupprincip för att ställa in [inställningen konfigurera automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) på **3**. Den här inställningen aktiverar hämtning av nödvändiga uppdateringar i bakgrunden och meddelar dig att uppdateringarna är klara att installeras. På så sätt har Uppdateringshantering fortfarande kontroll över scheman, men uppdateringar kan hämtas utanför Uppdateringshantering underhålls fönstret. Det här beteendet förhindrar att **underhålls fönstret har överskridit** fel i uppdateringshantering.

Du kan aktivera inställningen med PowerShell genom att köra följande kommando:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurera inställningar för omstart

De register nycklar som visas i [Konfigurera automatiska uppdateringar genom att redigera register](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) -och [register nycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan göra att datorerna startas om, även om du **inte anger starta** om i **uppdaterings distributions** inställningarna. Konfigurera de här register nycklarna så att de passar bäst för din miljö.

## <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Som standard är Windows Update-klienten konfigurerad att endast tillhandahålla uppdateringar för Windows. Om du aktiverar alternativet **för att ge mig uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows** -inställningar får du också uppdateringar för andra produkter, inklusive säkerhets korrigeringar för Microsoft SQL Server och annan Microsoft-programvara. Det här alternativet kan konfigureras om du har laddat ned och kopierat de senaste [administrativa mallarna](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) som är tillgängliga för Windows 2016 och högre.

Om du kör Windows Server 2012 R2, kan den här inställningen inte konfigureras av grupprincip. Kör följande PowerShell-kommando på dessa datorer. Uppdateringshantering följer den här inställningen.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Konfigurations inställningar för WSUS

Uppdateringshantering stöder WSUS-inställningar. De WSUS-inställningar som du kan konfigurera för att arbeta med Uppdateringshantering visas nedan.

### <a name="intranet-microsoft-update-service-location"></a>Microsoft-uppdateringstjänst på intranätet

Du kan ange källor för genomsökning och hämtning av uppdateringar under [Ange intranät Microsoft Update tjänst plats](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location). Windows Update-klienten är som standard konfigurerad för att hämta uppdateringar från Windows Update. När du anger en WSUS-server som källa för dina datorer, om uppdateringarna inte är godkända i WSUS, Miss lyckas uppdaterings distributionen. 

Om du vill begränsa datorerna till enbart den interna uppdaterings tjänsten, kan du konfigurera [Anslut inte till någon Windows Update Internet-platser](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Windows Update inställningarna kan du schemalägga en uppdaterings distribution genom att följa anvisningarna i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
