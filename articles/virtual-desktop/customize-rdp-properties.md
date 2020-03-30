---
title: Anpassa RDP-egenskaper med PowerShell - Azure
description: Anpassa RDP-egenskaper för Windows Virtual Desktop med PowerShell-cmdletar.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4a0f193437353bac1f5998b50b9d7b4d43bedefa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128075"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Anpassa egenskaper för fjärrskrivbordsprotokoll för en värdpool

Genom att anpassa egenskaperna för en värdpools RDP-egenskaper (Remote Desktop Protocol), till exempel erfarenhet med flera bildskärmar och omdirigering av ljud, kan du ge användarna en optimal upplevelse baserat på deras behov. Du kan anpassa RDP-egenskaper i Windows Virtual Desktop med parametern **-CustomRdpProperty** i cmdleten **Set-RdsHostPool.**

Se [rdp-filinställningar som stöds](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) för en fullständig lista över egenskaper som stöds och deras standardvärden.

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det. Därefter kör du följande cmdlet för att logga in på ditt konto:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="default-rdp-properties"></a>Standardegenskaper för fjärrskrivbordsleverantör

Som standard innehåller publicerade RDP-filer följande egenskaper:

|RDP-egenskaper | Stationära datorer | Fjärrappar |
|---|---| --- |
| Läge med flera bildskärmar | Enabled | Ej tillämpligt |
| Omdirigeringar av drivning aktiverat | Enheter, urklipp, skrivare, COM-portar, USB-enheter och smartkort| Enheter, Urklipp och skrivare |
| Fjärrljudläge | Spela lokalt | Spela lokalt |

Alla anpassade egenskaper som du definierar för värdpoolen åsidosätter dessa standardvärden.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Lägga till eller redigera en enskild rdp-egenskap

Om du vill lägga till eller redigera en enskild RDP-egenskap kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```

![En skärmbild av PowerShell cmdlet Get-RDSRemoteApp med Namn och FriendlyName markerat.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Lägga till eller redigera flera anpassade RDP-egenskaper

Om du vill lägga till eller redigera flera anpassade RDP-egenskaper kör du följande PowerShell-cmdlets genom att ange de anpassade RDP-egenskaperna som en semikolonavgränsad sträng:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```

![En skärmbild av PowerShell cmdlet Get-RDSRemoteApp med Namn och FriendlyName markerat.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Återställa alla anpassade RDP-egenskaper

Du kan återställa enskilda anpassade RDP-egenskaper till deras standardvärden genom att följa instruktionerna i [Lägg till eller redigera en enskild anpassad RDP-egenskap](#add-or-edit-a-single-custom-rdp-property)eller återställa alla anpassade RDP-egenskaper för en värdpool genom att köra följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```

![En skärmbild av PowerShell cmdlet Get-RDSRemoteApp med Namn och FriendlyName markerat.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat RDP-egenskaperna för en viss värdpool kan du logga in på en Windows Virtual Desktop-klient för att testa dem som en del av en användarsession. De här två följande how-tos kommer att berätta hur du ansluter till en session med hjälp av den klient som du väljer:

- [Ansluta med Windows-skrivbordsklienten](connect-windows-7-and-10.md)
- [Ansluta med webbklienten](connect-web.md)
