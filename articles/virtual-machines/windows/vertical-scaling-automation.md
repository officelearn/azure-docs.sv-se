---
title: Använd Azure Automation för att skala virtuella Windows-datorer vertikalt | Microsoft Docs
description: Skala en virtuell Windows-dator lodrätt som svar på övervaknings aviseringar med Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5d255662f7db12537365f57eb71355ca2e11cc51
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947257"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Skala virtuella Windows-datorer vertikalt med Azure Automation

Vertikal skalning är en process för att öka eller minska resurserna på en dator som svar på arbets belastningen. I Azure kan du åstadkomma detta genom att ändra storleken på den virtuella datorn. Detta kan vara till hjälp i följande scenarier

* Om den virtuella datorn inte används ofta kan du ändra storlek på den till en mindre storlek för att minska månads kostnaderna
* Om den virtuella datorn ser en hög belastning kan du ändra storlek till en större storlek för att öka dess kapacitet

En disposition för stegen för att åstadkomma detta är som nedan

1. Installations Azure Automation för att få åtkomst till din Virtual Machines
2. Importera Azure Automation vertikala skalnings-Runbooks till din prenumeration
3. Lägg till en webhook i din Runbook
4. Lägg till en avisering till den virtuella datorn


## <a name="scale-limitations"></a>Skalnings begränsningar

På grund av storleken på den första virtuella datorn kan storlekarna som kan skalas till, begränsas på grund av tillgängligheten för de andra storlekarna i klustret som den aktuella virtuella datorn har distribuerats i. I de publicerade Automation-runbooks som används i den här artikeln tar vi hand om det här fallet och skalar bara inom de följande virtuella datorernas storleks par. Det innebär att en virtuell Standard_D1v2-dator plötsligt inte kan skalas upp till Standard_G5 eller skalas ned till Basic_A0. Även begränsade storlekar för virtuella datorer skalar upp/ned stöds inte. 

Du kan välja att skala mellan följande par storlekar:

* [A-serien](#a-series)
* [B-serien](#b-series)
* [D-serien](#d-series)
* [E-serien](#e-series)
* [F-serien](#f-series)
* [G-serien](#g-series)
* [H-serien](#h-series)
* [L-serien](#l-series)
* [M-serien](#m-series)
* [N-serien](#n-series)

### <a name="a-series"></a>A-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Basic_A0 | Basic_A1 |
| Basic_A1 | Basic_A2 |
| Basic_A2 | Basic_A3 |
| Basic_A3 | Basic_A4 |
| Standard_A0 | Standard_A1 |
| Standard_A1 | Standard_A2 |
| Standard_A2 | Standard_A3 |
| Standard_A3 | Standard_A4 |
| Standard_A5 | Standard_A6 |
| Standard_A6 | Standard_A7 |
| Standard_A8 | Standard_A9 |
| Standard_A10 | Standard_A11 |
| Standard_A1_v2 | Standard_A2_v2 |
| Standard_A2_v2 | Standard_A4_v2 |
| Standard_A4_v2 | Standard_A8_v2 |
| Standard_A2m_v2 | Standard_A4m_v2 |
| Standard_A4m_v2 | Standard_A8m_v2 |

### <a name="b-series"></a>B-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_B1s | Standard_B2s |
| Standard_B1ms | Standard_B2ms |
| Standard_B2ms | Standard_B4ms |
| Standard_B4ms | Standard_B8ms |

### <a name="d-series"></a>D-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_D1 | Standard_D2 |
| Standard_D2 | Standard_D3 |
| Standard_D3 | Standard_D4 |
| Standard_D11 | Standard_D12 |
| Standard_D12 | Standard_D13 |
| Standard_D13 | Standard_D14 |
| Standard_DS1 | Standard_DS2 |
| Standard_DS2 | Standard_DS3 |
| Standard_DS3 | Standard_DS4 |
| Standard_DS11 | Standard_DS12 |
| Standard_DS12 | Standard_DS13 |
| Standard_DS13 | Standard_DS14 |
| Standard_D1_v2 | Standard_D2_v2 |
| Standard_D2_v2 | Standard_D3_v2 |
| Standard_D3_v2 | Standard_D4_v2 |
| Standard_D4_v2 | Standard_D5_v2 |
| Standard_D11_v2 | Standard_D12_v2 |
| Standard_D12_v2 | Standard_D13_v2 |
| Standard_D13_v2 | Standard_D14_v2 |
| Standard_DS1_v2 | Standard_DS2_v2 |
| Standard_DS2_v2 | Standard_DS3_v2 |
| Standard_DS3_v2 | Standard_DS4_v2 |
| Standard_DS4_v2 | Standard_DS5_v2 |
| Standard_DS11_v2 | Standard_DS12_v2 |
| Standard_DS12_v2 | Standard_DS13_v2 |
| Standard_DS13_v2 | Standard_DS14_v2 |
| Standard_D2_v3 | Standard_D4_v3 |
| Standard_D4_v3 | Standard_D8_v3 |
| Standard_D8_v3 | Standard_D16_v3 |
| Standard_D16_v3 | Standard_D32_v3 |
| Standard_D32_v3 | Standard_D64_v3 |
| Standard_D2s_v3 | Standard_D4s_v3 |
| Standard_D4s_v3 | Standard_D8s_v3 |
| Standard_D8s_v3 | Standard_D16s_v3 |
| Standard_D16s_v3 | Standard_D32s_v3 |
| Standard_D32s_v3 | Standard_D64s_v3 |
| Standard_DC2s | Standard_DC4s |

### <a name="e-series"></a>E-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_E2_v3 | Standard_E4_v3 |
| Standard_E4_v3 | Standard_E8_v3 |
| Standard_E8_v3 | Standard_E16_v3 |
| Standard_E16_v3 | Standard_E20_v3 |
| Standard_E20_v3 | Standard_E32_v3 |
| Standard_E32_v3 | Standard_E64_v3 |
| Standard_E2s_v3 | Standard_E4s_v3 |
| Standard_E4s_v3 | Standard_E8s_v3 |
| Standard_E8s_v3 | Standard_E16s_v3 |
| Standard_E16s_v3 | Standard_E20s_v3 |
| Standard_E20s_v3 | Standard_E32s_v3 |
| Standard_E32s_v3 | Standard_E64s_v3 |

### <a name="f-series"></a>F-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_F1 | Standard_F2 |
| Standard_F2 | Standard_F4 |
| Standard_F4 | Standard_F8 |
| Standard_F8 | Standard_F16 |
| Standard_F1s | Standard_F2s |
| Standard_F2s | Standard_F4s |
| Standard_F4s | Standard_F8s |
| Standard_F8s | Standard_F16s |
| Standard_F2s_v2 | Standard_F4s_v2 |
| Standard_F4s_v2 | Standard_F8s_v2 |
| Standard_F8s_v2 | Standard_F16s_v2 |
| Standard_F16s_v2 | Standard_F32s_v2 |
| Standard_F32s_v2 | Standard_F64s_v2 |
| Standard_F64s_v2 | Standard_F7s_v2 |

### <a name="g-series"></a>G-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_G1 | Standard_G2 |
| Standard_G2 | Standard_G3 |
| Standard_G3 | Standard_G4 |
| Standard_G4 | Standard_G5 |
| Standard_GS1 | Standard_GS2 |
| Standard_GS2 | Standard_GS3 |
| Standard_GS3 | Standard_GS4 |
| Standard_GS4 | Standard_GS5 |

### <a name="h-series"></a>H-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_H8 | Standard_H16 |
| Standard_H8m | Standard_H16m |

### <a name="l-series"></a>L-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_L4s | Standard_L8s |
| Standard_L8s | Standard_L16s |
| Standard_L16s | Standard_L32s |
| Standard_L8s_v2 | Standard_L16s_v2 |
| Standard_L16s_v2 | Standard_L32s_v2 |
| Standard_L32s_v2 | Standard_L64s_v2 |
| Standard_L64s_v2 | Standard_L80s_v2 |

### <a name="m-series"></a>M-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_M8ms | Standard_M16ms |
| Standard_M16ms | Standard_M32ms |
| Standard_M32ms | Standard_M64ms |
| Standard_M64ms | Standard_M128ms |
| Standard_M32ls | Standard_M64ls |
| Standard_M64s | Standard_M128s |
| Standard_M64 | Standard_M128 |
| Standard_M64m | Standard_M128m |

### <a name="n-series"></a>N-serien

| Ursprunglig storlek | Skala upp storlek | 
| --- | --- |
| Standard_NC6 | Standard_NC12 |
| Standard_NC12 | Standard_NC24 |
| Standard_NC6s_v2 | Standard_NC12s_v2 |
| Standard_NC12s_v2 | Standard_NC24s_v2 |
| Standard_NC6s_v3 | Standard_NC12s_v3 |
| Standard_NC12s_v3 | Standard_NC24s_v3 |
| Standard_ND6 | Standard_ND12 |
| Standard_ND12 | Standard_ND24 |
| Standard_NV6 | Standard_NV12 |
| Standard_NV12 | Standard_NV24 |
| Standard_NV6s_v2 | Standard_NV12s_v2 |
| Standard_NV12s_v2 | Standard_NV24s_v2 |
| Standard_NV12s_v3 |Standard_NV48s_v3 |

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Installations Azure Automation för att få åtkomst till din Virtual Machines
Det första du behöver göra är att skapa ett Azure Automation-konto som ska vara värd för de Runbooks som används för att skala en virtuell dator. Nyligen introducerade Automation-tjänsten funktionen kör som-konto som gör det enkelt att konfigurera tjänstens huvud namn för att automatiskt köra Runbooks i användarens räkning. Du kan läsa mer om det här i artikeln nedan:

* [Autentisera runbooks med ett ”Kör som”-konto i Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importera Azure Automation vertikala skalnings-Runbooks till din prenumeration
Runbooks som behövs för vertikal skalning av den virtuella datorn är redan publicerade i Azure Automation Runbook-galleriet. Du måste importera dem till din prenumeration. Du kan lära dig hur du importerar runbooks genom att läsa följande artikel.

* [Runbook- och modulgallerier för Azure Automation](../../automation/automation-runbook-gallery.md)

Runbooks som behöver importeras visas i bilden nedan

![Importera runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Lägg till en webhook i din Runbook
När du har importerat Runbooks måste du lägga till en webhook till Runbook så att den kan utlösas av en avisering från en virtuell dator. Information om hur du skapar en webhook för din Runbook kan läsas här

* [Azure Automation Webhooks](../../automation/automation-webhooks.md)

Se till att du kopierar webhooken innan du stänger dialog rutan för webhooken eftersom du kommer att behöva detta i nästa avsnitt.

## <a name="add-an-alert-to-your-virtual-machine"></a>Lägg till en avisering till den virtuella datorn
1. Välj inställningar för virtuell dator
2. Välj aviserings regler
3. Välj Lägg till avisering
4. Välj ett mått för att utlösa aviseringen
5. Välj ett villkor, som när det är uppfyllt, orsakar aviseringen att utlösa
6. Välj ett tröskelvärde för villkoret i steg 5. att vara uppfyllda
7. Välj en period under vilken övervaknings tjänsten ska söka efter villkoret och tröskelvärdet i steg 5 & 6
8. Klistra in webhooken som du kopierade från föregående avsnitt.

![Lägg till avisering till virtuell dator 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Lägg till avisering till virtuell dator 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

