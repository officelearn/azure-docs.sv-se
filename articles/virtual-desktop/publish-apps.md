---
title: Publicera inbyggda appar i Windows Virtual Desktop våren 2020 – Azure
description: Publicera inbyggda appar i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 63d20bbb09af6f464a331ddfbad823f5d3b18d76
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86527593"
---
# <a name="publish-built-in-apps-in-windows-virtual-desktop"></a>Publicera inbyggda appar i Windows Virtual Desktop

>[!IMPORTANT]
>Det här innehållet gäller för våren 2020-uppdateringen med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder den virtuella Windows-datorn med version 2019 utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/publish-apps-2019.md).
>
> Den virtuella Windows-skrivbordets våren 2020-uppdateringen är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I den här artikeln får du lära dig hur du publicerar appar i din Windows-miljö för virtuella skriv bord.

## <a name="publish-built-in-apps"></a>Publicera inbyggda appar

Publicera en inbyggd app:

1. Anslut till en av de virtuella datorerna i din värd bassäng.
2. Hämta **PackageFamilyName** för den app som du vill publicera genom att följa anvisningarna i [den här artikeln](/powershell/module/appx/get-appxpackage?view=win10-ps/).
3. Kör slutligen följande cmdlet med `<PackageFamilyName>` ersatt av de **PackageFamilyName** som du hittade i föregående steg:

   ```powershell
   New-AzWvdApplication -Name <applicationname> -ResourceGroupName <resourcegroupname> -ApplicationGroupName <appgroupname> -FilePath "shell:appsFolder\<PackageFamilyName>!App" -CommandLineSetting <Allow|Require|DoNotAllow> -IconIndex 0 -IconPath <iconpath> -ShowInPortal:$true
   ```

>[!NOTE]
> Det virtuella Windows-skrivbordet stöder endast publicering av appar med installations platser som börjar med `C:\Program Files\WindowsApps` .

## <a name="update-app-icons"></a>Uppdatera app-ikoner

När du har publicerat en app kommer den att ha standard ikonen för Windows-appen i stället för dess vanliga ikon bild. Om du vill ändra ikonen till dess vanliga ikon, ska du placera avbildningen av den ikon som du vill använda på en nätverks resurs. Bild formaten som stöds är PNG, BMP, GIF, JPG, JPEG och ICO.

## <a name="publish-microsoft-edge"></a>Publicera Microsoft Edge

Processen du använder för att publicera Microsoft Edge skiljer sig lite från publicerings processen för andra appar. Om du vill publicera Microsoft Edge med standard start sidan kör du denna cmdlet:

```powershell
New-AzWvdApplication -Name -ResourceGroupName -ApplicationGroupName -FilePath "shell:Appsfolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge" -CommandLineSetting <Allow|Require|DoNotAllow> -iconPath "C:\Windows\SystemApps\Microsoft.MicrosoftEdge_8wekyb3d8bbwe\microsoftedge.exe" -iconIndex 0 -ShowInPortal:$true
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar feeds för att ordna hur appar visas för användare i [Anpassa feed för virtuella Windows-användare](customize-feed-for-virtual-desktop-users.md).
- Lär dig mer om MSIX app Attach-funktionen i [Konfigurera MSIX app Attach](app-attach.md).

