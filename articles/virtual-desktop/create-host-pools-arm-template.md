---
title: Skapa en värd-pool med en Azure Resource Manager-mall (förhandsversion), Azure
description: Hur du skapar en pool för värden i virtuella Windows-skrivbordet med en Azure Resource Manager-mall.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 7f4849c19d56bb385e7ad3ce0aa95e16d5c53c23
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318471"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template-preview"></a>Skapa en värd-pool med en Azure Resource Manager-mall (förhandsversion)

Värd-pooler är en samling av en eller flera identiska virtuella datorer i miljöer med virtuella skrivbord i Windows-klient (förhandsversion). Varje värd-pool kan innehålla en appgrupp som användare kan interagera med precis som på en fysisk dator.

Följ instruktionerna för det här avsnittet att skapa en värd-pool för ett virtuellt skrivbord i Windows-klient med en Azure Resource Manager-mall som tillhandahålls av Microsoft. Den här artikeln kommer information om hur du skapar en pool för värden i virtuella Windows-skrivbordet, skapa en resursgrupp med virtuella datorer i en Azure-prenumeration, ansluta till dessa virtuella datorer till AD-domänen och registrera de virtuella datorerna med virtuella Windows-skrivbordet.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Vad du behöver att köra Azure Resource Manager-mall

Kontrollera följande innan du kör Azure Resource Manager-mallen:

- Där är källan för den avbildning som du vill använda. Är det från Azure-galleriet eller är det anpassade?
- Dina autentiseringsuppgifter för domänanslutning.
- Dina autentiseringsuppgifter för virtuella Windows-skrivbordet.

När du skapar en pool med virtuella Windows-skrivbordet värden med Azure Resource Manager-mall kan skapa du en virtuell dator från Azure-galleriet, en hanterad avbildning eller en ohanterad avbildning. Mer information om hur du skapar VM-avbildningar finns [förbereda en Windows-VHD eller VHDX för att överföra till Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) och [skapa en hanterad avbildning av en generaliserad virtuell dator i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Kör Azure Resource Manager-mall för att etablera en ny värd-pool

Starta, gå till [GitHub URL: en](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Distribuera mallen till Azure

Om du distribuerar i en prenumeration på Enterprise, rulla nedåt och välj **distribuera till Azure**, hoppa över ahead fylla i parametrarna beroende på din källa.

Om du distribuerar i en Cloud Solution Provider-prenumeration, följer du dessa steg för att distribuera till Azure:

1. Rulla nedåt och högerklicka på **distribuera till Azure**och välj sedan **kopia länkplats**.
2. Öppna en textredigerare, till exempel Anteckningar och klistra in det på länken.
3. Direkt efter ”https://portal.azure.com/” och innan hashtagg (#) anger ett snabel-a (@) följt av klientens domännamn. Här är ett exempel på bör du använda formatet: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Logga in på Azure Portal som en användare med behörigheter för administratör/deltagare i Cloud Solution Provider-prenumerationen.
5. Klistra in länken som du kopierade till textredigeraren i adressfältet.

Information om vilka parametrar du anger för ditt scenario finns i det virtuella Windows-skrivbordet [Readme-filen](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). I filen Viktigt är alltid uppdaterad med de senaste ändringarna.

## <a name="assign-users-to-the-desktop-application-group"></a>Tilldela användare till gruppen skrivbordsprogram

När GitHub Azure Resource Manager-mallen har slutförts kan du tilldela användaråtkomst innan du startar testerna fullständig session-skrivbord på dina virtuella datorer.

Först [hämta och importera modulen Windows PowerShell för virtuella skrivbord](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) ska användas i PowerShell-sessionen om du inte redan har gjort.

Om du vill tilldela användare till gruppen skrivbordsprogram, öppna ett PowerShell-fönster och kör denna cmdlet för att logga in på den virtuella skrivbordet i Windows-miljön:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Nu ska vi konfigurera kontexten i klient-gruppen som anges i Azure Resource Manager-mall med denna cmdlet:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Efter det att lägga till användare i gruppen skrivbordsprogram med denna cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Användarens UPN måste matcha användarens identitet i Azure Active Directory (till exempel user1@contoso.com). Om du vill lägga till flera användare måste du köra denna cmdlet för varje användare.

När du har slutfört de här stegen kan kan användare läggs till i gruppen skrivbordsprogram logga in på virtuella Windows-skrivbordet med Remote Desktop-klienter som stöds och se en resurs för ett session-skrivbord.
