---
title: Anpassa RDP-egenskaper med PowerShell – Azure
description: Anpassa RDP-egenskaperna för virtuella Windows-datorer med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
ms.openlocfilehash: 43110036c685cd17ba912766dd8ec19aa274e7c1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75459533"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Anpassa Remote Desktop Protocol egenskaper för en värd pool

Genom att anpassa en värd Pools egenskaper för Remote Desktop Protocol (RDP), till exempel flera skärmar och omdirigering av ljud, kan du leverera en optimal upplevelse för dina användare utifrån deras behov. Du kan anpassa RDP-egenskaperna i Windows Virtual Desktop med parametern **-CustomRdpProperty** i cmdleten **set-RdsHostPool** .

Se [Inställningar för RDP-filer som stöds](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) för en fullständig lista över vilka egenskaper som stöds och deras standardvärden.

Börja med att [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) som ska användas i PowerShell-sessionen om du inte redan gjort det. Sedan kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Standard egenskaper för RDP

Som standard innehåller publicerade RDP-filer följande egenskaper:

|RDP-egenskaper | Skrivbordsdatorer | RemoteApps |
|---|---| --- |
| Läge för flera övervakare | Enabled | Saknas |
| Aktiverade enhets omdirigeringar | Enheter, Urklipp, skrivare, COM-portar, USB-enheter och smartkort| Enheter, urklipp och skrivare |
| Fjärr ljud läge | Spela lokalt | Spela lokalt |

De anpassade egenskaper som du definierar för poolen åsidosätter dessa standardvärden.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Lägga till eller redigera en enskild anpassad RDP-egenskap

Om du vill lägga till eller redigera en enskild anpassad RDP-egenskap kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Lägga till eller redigera flera anpassade RDP-egenskaper

Om du vill lägga till eller redigera flera anpassade RDP-egenskaper kör du följande PowerShell-cmdletar genom att ange de anpassade RDP-egenskaperna som en semikolonavgränsad sträng:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Återställ alla anpassade RDP-egenskaper

Du kan återställa enskilda anpassade RDP-egenskaper till sina standardvärden genom att följa anvisningarna i [lägga till eller redigera en enskild anpassad RDP-egenskap](#add-or-edit-a-single-custom-rdp-property), eller så kan du återställa alla anpassade RDP-egenskaper för en värd pool genom att köra följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![En skärm bild av PowerShell-cmdleten Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat RDP-egenskaperna för en specifik adresspool kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. Följande två instruktioner visar hur du ansluter till en session med valfri klient:

- [Ansluta till Windows Desktop-klienten](connect-windows-7-and-10.md)
- [Anslut till webb klienten](connect-web.md)
