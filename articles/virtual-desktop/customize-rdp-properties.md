---
title: Anpassa egenskaper för RDP med PowerShell - Azure
description: Hur du anpassar RDP egenskaper för Windows virtuellt skrivbord med PowerShell-cmdletar.
services: virtual-desktop
author: v-hevem
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 06/03/2019
ms.author: v-hevem
ms.openlocfilehash: 21d0b45b974f4bc806b26423b7deaef96e4bf350
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082656"
---
# <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Anpassa Remote Desktop Protocol-egenskaperna för en värd-pool

Anpassa egenskaper för poolen värd Remote Desktop Protocol (RDP), till exempel flera bildskärmar upplevelse och omdirigering av ljud, kan du leverera en optimal upplevelse för användarna utifrån deras behov. Du kan anpassa RDP-egenskaper i Windows virtuellt skrivbord med hjälp av den **- CustomRdpProperty** parametern i den **Set-RdsHostPool** cmdlet.

Se [Remote Desktop RDP-filinställningar](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files) för en fullständig lista över egenskaper som stöds och deras standardvärden.

Först [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

## <a name="add-or-edit-a-single-custom-rdp-property"></a>Lägg till eller redigera en enskild egenskap med anpassade RDP

Om du vill lägga till eller redigera en enskild egenskap med anpassade RDP, kör du följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty "<property>"
```
![En skärmbild av PowerShell-cmdlet Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/singlecustomrdpproperty.png)

## <a name="add-or-edit-multiple-custom-rdp-properties"></a>Lägg till eller redigera flera anpassade RDP-egenskaper

Kör följande PowerShell-cmdletar för att lägga till eller redigera flera anpassade egenskaper i RDP, genom att tillhandahålla anpassade RDP-egenskaper som en semikolonavgränsad sträng:

```powershell
$properties="<property1>;<property2>;<property3>"
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty $properties
```
![En skärmbild av PowerShell-cmdlet Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/multiplecustomrdpproperty.png)

## <a name="reset-all-custom-rdp-properties"></a>Återställ alla anpassade RDP-egenskaper

Du kan återställa enskilda anpassade RDP-egenskaper till sina standardvärden genom att följa instruktionerna i [lägga till eller redigera en enskild egenskap med anpassade RDP](#add-or-edit-a-single-custom-rdp-property), eller återställa alla anpassade RDP-egenskaper för en värd-pool genom att köra följande PowerShell-cmdlet:

```powershell
Set-RdsHostPool -TenantName <tenantname> -Name <hostpoolname> -CustomRdpProperty ""
```
![En skärmbild av PowerShell-cmdlet Get-RDSRemoteApp med namnet och FriendlyName markerat.](media/resetcustomrdpproperty.png)

## <a name="next-steps"></a>Nästa steg

Nu när du har anpassat RDP-egenskaperna för en viss värd-pool kan logga du in till ett virtuellt skrivbord i Windows-klienten att testa dem som en del av en användarsession. Du gör detta genom att fortsätta att ansluta till virtuella skrivbord instruktioner för Windows:

- [Ansluta från Windows 10 och Windows 7](connect-windows-7-and-10.md)
- [Ansluta från en webbläsare](connect-web.md)
