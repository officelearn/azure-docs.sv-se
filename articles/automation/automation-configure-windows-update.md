---
title: Konfigurera Windows Update-inställningar så att de fungerar med Azure Update Management
description: I den hÃ¤r artikeln beskrivs de Windows Update-instÃ¤llningar som du konfigurerar fÃ¶r att fungera med Azure Update Management.
services: automation
ms.subservice: update-management
ms.date: 03/02/2020
ms.topic: conceptual
ms.openlocfilehash: 7f226c4d297d25644b2650d085655f70d8326927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279031"
---
# <a name="configure-windows-update-settings-for-update-management"></a>Konfigurera Windows Update-inställningar för uppdateringshantering

Azure Update Management förlitar sig på [Windows Update-klienten](https://docs.microsoft.com//windows/deployment/update/windows-update-overview) för att hämta och installera Windows-uppdateringar. Det finns specifika inställningar som används av Windows Update-klienten när du ansluter till Windows Server Update Services (WSUS) eller Windows Update. Många av dessa inställningar kan hanteras med:

- Redigerare för lokal gruppolicy
- Grupprincip
- PowerShell
- Direkt redigering av registret

Uppdateringshantering respekterar många av de inställningar som angetts för att styra Windows Update-klienten. Om du använder inställningar för att aktivera uppdateringar som inte kommer från Windows hanterar Uppdateringshantering även dessa uppdateringar. Om du vill aktivera hämtning av uppdateringar innan en uppdateringsdistribution sker kan uppdateringsdistributionen vara snabbare, effektivare och mindre benägna att överskrida underhållsfönstret.

## <a name="pre-download-updates"></a>Uppdateringar före nedladdning

Om du vill konfigurera automatisk hämtning av uppdateringar men inte automatiskt installera dem kan du använda Grupprincip för att ställa in [inställningen Konfigurera automatiska uppdateringar](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates##configure-automatic-updates) till **3**. Den här inställningen aktiverar hämtningar av nödvändiga uppdateringar i bakgrunden och meddelar dig att uppdateringarna är klara att installeras. På så sätt har uppdateringshantering fortfarande kontroll över scheman, men uppdateringar kan hämtas utanför underhållsfönstret för uppdateringshantering. Detta förhindrar **underhållsfönstret som överskridit** fel i Uppdateringshantering.

Du kan aktivera den här inställningen med PowerShell genom att köra följande kommando:

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

## <a name="configure-reboot-settings"></a>Konfigurera omstartsinställningar

De registernycklar som anges i [Konfigurera automatiska uppdateringar genom](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) att redigera register- och [registernycklar som används för att hantera omstart](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) kan orsaka att datorerna startas om, även om du anger Aldrig starta **om** i **inställningarna för uppdateringsdistribution.** Konfigurera dessa registernycklar så att de passar din miljö bäst.

## <a name="enable-updates-for-other-microsoft-products"></a>Aktivera uppdateringar för andra Microsoft-produkter

Som standard är Windows Update-klienten konfigurerad för att endast tillhandahålla uppdateringar för Windows. Om du aktiverar **Ge mig-uppdateringar för andra Microsoft-produkter när jag uppdaterar Windows-inställningen** får du även uppdateringar för andra produkter, inklusive säkerhetskorrigeringar för Microsoft SQL Server och annan Microsoft-programvara. Det här alternativet kan konfigureras om du har hämtat och kopierat de senaste [administrativa mallfilerna](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra) som är tillgängliga för Windows 2016 och senare.

Om du kör Windows Server 2012 R2 kan den här inställningen inte konfigureras av grupprincipen. Kör följande PowerShell-kommando på dessa datorer. Uppdateringshantering följer den här inställningen.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="wsus-configuration-settings"></a>Konfigurationsinställningar för WSUS

Uppdateringshantering stöder WSUS-inställningar. De WSUS-inställningar som du kan konfigurera för att arbeta med uppdateringshantering visas nedan.

### <a name="intranet-microsoft-update-service-location"></a>Plats för Microsoft-uppdateringstjänst för intranät

Du kan ange källor för att skanna och hämta uppdateringar under [Ange tjänstplats för intranät](/windows/deployment/update/waas-wu-settings#specify-intranet-microsoft-update-service-location)Microsoft Update . Som standard är Windows Update-klienten konfigurerad för att hämta uppdateringar från Windows Update. När du anger en WSUS-server som en källa för dina datorer, om uppdateringarna inte godkänns i WSUS, misslyckas uppdateringsdistributionen. 

Om du vill begränsa datorer till just den interna uppdateringstjänsten [konfigurerar du inte anslut till några Windows Update Internet-platser](https://docs.microsoft.com/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#do-not-connect-to-any-windows-update-internet-locations). 

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Windows Update-inställningar kan du schemalägga en uppdateringsdistribution genom att följa instruktionerna i [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
