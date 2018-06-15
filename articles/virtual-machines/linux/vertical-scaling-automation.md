---
title: Lodrätt skala virtuella Azure-datorn med Azure Automation | Microsoft Docs
description: Hur man lodrätt skala en Linux-dator för att övervaka aviseringar med Azure Automation
services: virtual-machines-linux
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: dcee199e-fa25-44d5-9b25-df564cee9b45
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/29/2016
ms.author: singhkay
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 683348c907484ccd9394eb4aae18e9006ecb5c48
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30913492"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Lodrätt skala Azure Linux-dator med Azure Automation
Lodrät skalning är processen att öka eller minska resurser för en dator som svar på arbetsbelastningen. I Azure kan detta åstadkommas genom att ändra storleken på den virtuella datorn. Detta hjälper i följande scenarier

* Om den virtuella datorn inte används ofta, du kan ändra storlek på den till en mindre storlek för att minska kostnaderna för varje månad
* Om den virtuella datorn ser en belastning, kan den ändras till en större kapacitet

Disposition stegen för att åstadkomma detta är som nedan

1. Konfigurera Azure Automation för att komma åt dina virtuella datorer
2. Importera Lodrät skala för Azure Automation-runbooks till din prenumeration
3. Lägga till en webhook i din runbook
4. Lägga till en avisering till den virtuella datorn

> [!NOTE]
> På grund av storleken på den första virtuella datorn, storleken kan skalas till, kan begränsas på grund av den aktuella virtuella datorn är distribuerad i tillgängligheten för andra storlekar i klustret. I publicerade automation-runbooks som används i denna artikel vi tar hand om det här fallet och kan endast skalas inom den nedan par för VM-storlek. Det innebär att en virtuell dator för Standard_D1v2 inte plötsligt ska skalas till Standard_G5 eller skalas till Basic_A0.
> 
> | VM-storlekar skalning par |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1v2 |Standard_D5v2 |
> | Standard_D11v2 |Standard_D14v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Konfigurera Azure Automation för att komma åt dina virtuella datorer
Det första du behöver göra är att skapa ett Azure Automation-konto som ska vara värd för runbooks som används för att anpassa VM Scale Set-instanser. Automation-tjänsten introducerade nyligen funktionen ”Kör som-konto” som gör att du kan konfigurera tjänstens huvudnamn för att automatiskt köra runbooks för användarens räkning mycket enkelt. Mer information finns i artikeln nedan:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera Lodrät skala för Azure Automation-runbooks till din prenumeration
Runbooks som behövs för att skala lodrätt den virtuella datorn har redan publicerats i Azure Automation Runbook-galleriet. Du behöver importera dem till din prenumeration. Du kan lära dig hur man importerar runbooks genom att läsa följande artikel.

* [Azure Automation Runbook- och stänga](../../automation/automation-runbook-gallery.md)

I bilden nedan visas de runbooks som ska importeras

![Importera runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Lägga till en webhook i din runbook
När du har importerat förse runbooks måste du en webhook runbook så att den kan aktiveras av en avisering från en virtuell dator. Information om hur du skapar en webhook för din Runbook kan läsas här

* [Azure Automation-webhooks](../../automation/automation-webhooks.md)

Kontrollera att du kopierar webhooken innan du stänger dialogrutan webhook som du behöver det i nästa avsnitt.

## <a name="add-an-alert-to-your-virtual-machine"></a>Lägga till en avisering till den virtuella datorn
1. Välj inställningar för virtuell dator
2. Välj ”Varningsregler”
3. Välj ”Lägg till avisering”
4. Markera ett mått för att utlösa en avisering på
5. Välj ett villkor som under uppfyllda kan orsaka att aviseringen ska utlösa
6. Välj ett tröskelvärde för villkoret i steg 5. uppfylls
7. Välj en period under vilken övervakningstjänsten kontrollerar för villkoret och tröskelvärde i steg 5 och 6
8. Klistra in i webhooken som du kopierade i föregående avsnitt.

![Lägga till en avisering till den virtuella datorn 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Lägg till avisering till virtuell dator 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

