---
title: Lodrätt skalning av Azure-dator med Azure Automation | Microsoft Docs
description: Så här lodrätt skalning av en Linux-dator som svar på övervakning av aviseringar med Azure Automation
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
ms.date: 04/18/2019
ms.author: singhkay
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c959b42086df2df816583ea9d2f8f2fc7a9243fd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743586"
---
# <a name="vertically-scale-azure-linux-virtual-machine-with-azure-automation"></a>Lodrätt skalning av virtuella Linux-datorer med Azure Automation
Vertikal skalning är processen för att öka eller minska resurser på en dator som svar på arbetsbelastningen. I Azure kan detta åstadkommas genom att ändra storleken på den virtuella datorn. Det kan hjälpa dig i följande scenarier

* Om den virtuella datorn inte används ofta, du kan ändra storlek på den till en mindre storlek för att minska dina månatliga kostnader
* Om den virtuella datorn ser en hög belastning, kan storleken ändras till en större kapacitet

Kantlinje för stegen för att åstadkomma detta är som nedan

1. Konfigurera Azure Automation för att komma åt dina virtuella datorer
2. Importera vertikal skalning i Azure Automation-runbooks till din prenumeration
3. Lägg till en webhook i din runbook
4. Lägga till en avisering till din virtuella dator

> [!NOTE]
> På grund av storleken på den första virtuella datorn, storlekar kan den skalas till, kan begränsas på grund av den aktuella virtuella datorn har distribuerats i tillgängligheten för andra storlekar i klustret. I den publicerade automation-runbooks som används i den här artikeln vi tar hand om det här fallet och kan endast skalas inom den nedan par för VM-storlek. Det innebär att en virtuell dator för Standard_D1v2 inte plötsligt ska skalas upp till Standard_G5 eller skalas till Basic_A0. Också stöds begränsad VM storlekar skala upp och ned inte. Du kan välja att skala mellan de följande paren storlekar:
> 
> | VM-storlekar som skalning par |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Konfigurera Azure Automation för att komma åt dina virtuella datorer
Det första du behöver göra är att skapa ett Azure Automation-konto som är värd för runbooks som används för att skala VM Scale Set-instanser. Automation-tjänsten införde nyligen funktionen ”Kör som-konto” som gör att du kan konfigurera tjänstens huvudnamn för att automatiskt köra runbooks på användarens vägnar lättare än någonsin. Mer information finns i artikeln nedan:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera vertikal skalning i Azure Automation-runbooks till din prenumeration
Runbooks som behövs för lodrätt skalning av den virtuella datorn har redan publicerats i Azure Automation Runbook-galleriet. Du måste importera dem till din prenumeration. Du kan lära dig hur man importerar runbooks genom att läsa följande artikel.

* [Runbook- och modulgallerier för Azure Automation](../../automation/automation-runbook-gallery.md)

I bilden nedan visas de runbooks som ska importeras

![Importera runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Lägg till en webhook i din runbook
När du har importerat runbooks måste du lägga till en webhook för runbooken så att den kan aktiveras via en avisering från en virtuell dator. Information om att skapa en webhook för din Runbook kan läsas här

* [Azure Automation-webhookar](../../automation/automation-webhooks.md)

Kontrollera att du kopierar webhooken innan du stänger dialogrutan webhook eftersom du behöver det i nästa avsnitt.

## <a name="add-an-alert-to-your-virtual-machine"></a>Lägga till en avisering till din virtuella dator
1. Välj inställningar för virtuella datorer
2. Välj ”Varningsregler”
3. Välj ”Lägg till avisering”
4. Välj ett mått för utlöses aviseringen i
5. Välj ett villkor som under uppfyllda kommer orsaka aviseringen utlöses
6. Välj ett tröskelvärde för villkoret i steg 5. uppfylls
7. Välj en period som kontrollerar övervakningstjänsten för villkoret och tröskelvärdet i steg 5 och 6
8. Klistra in webhook som du kopierade i föregående avsnitt.

![Lägg till avisering till den virtuella datorn 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Lägg till avisering till virtuell dator 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

