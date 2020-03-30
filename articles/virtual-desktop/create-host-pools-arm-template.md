---
title: Värdpoolen för Windows Virtual Desktop Azure Resource Manager – Azure
description: Så här skapar du en värdpool i Windows Virtual Desktop med en Azure Resource Manager-mall.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292331"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Skapa en värdpool med en Azure Resource Manager-mall

Värdpooler är en samling av en eller flera identiska virtuella datorer i Windows Virtual Desktop-klientmiljöer. Varje värdpool kan innehålla en appgrupp som användarna kan interagera med på samma sätt som på ett fysiskt skrivbord.

Följ det här avsnittets instruktioner om hur du skapar en värdpool för en Windows Virtual Desktop-klient med en Azure Resource Manager-mall som tillhandahålls av Microsoft. I den här artikeln får du information om hur du skapar en värdpool i Windows Virtual Desktop, skapar en resursgrupp med virtuella datorer i en Azure-prenumeration, ansluter dessa virtuella datorer till AD-domänen och registrerar de virtuella datorerna med Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Vad du behöver för att köra Azure Resource Manager-mallen

Se till att du vet följande innan du kör Azure Resource Manager-mallen:

- Där källan till bilden du vill använda är. Är det från Azure Gallery eller är det anpassat?
- Autentiseringsuppgifterna för domänen ansluter.
- Dina autentiseringsuppgifter för Windows Virtual Desktop.

När du skapar en Värdpool för Windows Virtual Desktop med Azure Resource Manager-mallen kan du skapa en virtuell dator från Azure-galleriet, en hanterad avbildning eller en ohanterad avbildning. Mer information om hur du skapar VM-avbildningar finns i [Förbereda en Windows VHD eller VHDX för att ladda upp till Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) och skapa en [hanterad avbildning av en generaliserad virtuell dator i Azure](../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Kör Azure Resource Manager-mallen för etablering av en ny värdpool

Börja med att gå till [den här GitHub-URL:en](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Distribuera mallen till Azure

Om du distribuerar i en Enterprise-prenumeration bläddrar du nedåt och väljer **Distribuera till Azure**och går sedan vidare och fyller i parametrarna baserat på din avbildningskälla.

Om du distribuerar i en cloud solution provider-prenumeration gör du så här för att distribuera till Azure:

1. Bläddra nedåt och högerklicka på **Distribuera till Azure**och välj sedan Kopiera **länkplats**.
2. Öppna en textredigerare som Anteckningar och klistra in länken där.
3. Direkt efterhttps://portal.azure.com/" " och innan hashtaggen (#) anger ett at-tecken (@) följt av klientdomännamnet. Här är ett exempel på det `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`format du bör använda: .
4. Logga in på Azure-portalen som användare med administratörs-/deltagarbehörighet till cloud solution provider-prenumerationen.
5. Klistra in länken som du kopierade till textredigeraren i adressfältet.

Mer information om vilka parametrar du ska ange för ditt scenario finns i filen Windows Virtual Desktop [Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). Readme uppdateras alltid med de senaste ändringarna.

## <a name="assign-users-to-the-desktop-application-group"></a>Tilldela användare till programgruppen för skrivbord

När GitHub Azure Resource Manager-mallen är klar tilldelar du användaråtkomst innan du börjar testa de fullständiga sessionsskrivatorna på dina virtuella datorer.

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det.

Om du vill tilldela användare till skrivbordsprogramgruppen öppnar du ett PowerShell-fönster och kör den här cmdleten för att logga in på Windows Virtual Desktop-miljön:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Efter det lägger du till användare i skrivbordsprogramgruppen med den här cmdleten:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Användarens UPN ska matcha användarens identitet i Azure Active user1@contoso.comDirectory (till exempel). Om du vill lägga till flera användare måste du köra den här cmdleten för varje användare.

När du har slutfört de här stegen kan användare som läggs till i skrivbordsprogramgruppen logga in på Windows Virtual Desktop med fjärrskrivbordsklienter som stöds och se en resurs för ett sessionsskrivbord.

>[!IMPORTANT]
>För att skydda din Windows Virtual Desktop-miljö i Azure rekommenderar vi att du inte öppnar inkommande port 3389 på dina virtuella datorer. Windows Virtual Desktop kräver inte en öppen inkommande port 3389 för att användare ska komma åt värdpoolens virtuella datorer. Om du måste öppna port 3389 för felsökning rekommenderar vi att du använder [ny vm-åtkomst](../security-center/security-center-just-in-time.md).