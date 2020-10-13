---
title: Azure Boot-diagnostik
description: Översikt över Azure Boot Diagnostics och Managed Boot Diagnostics
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 52a2b5e27cd5857416343e559237d08ea9a591be
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91972398"
---
# <a name="azure-boot-diagnostics"></a>Azure Boot-diagnostik

Startdiagnostik är en fel söknings funktion för Azure Virtual Machines (VM) som gör det möjligt att diagnostisera VM-startfel. Med startdiagnostik kan en användare se statusen för den virtuella datorn när den startas genom att samla in information om seriell logg information och skärm dum par.

## <a name="boot-diagnostics-storage-account"></a>Lagrings konto för startdiagnostik
När du skapar en virtuell dator i Azure Portal aktive ras startdiagnostik som standard. Den rekommenderade versionen av startdiagnostik är att använda ett hanterat lagrings konto, eftersom det ger avsevärda prestanda förbättringar i tiden för att skapa en virtuell Azure-dator. Detta beror på att ett Azure-hanterat lagrings konto kommer att användas och tar bort den tid det tar att skapa ett nytt användar lagrings konto för att lagra startdiagnostikens data.

En alternativ starts upplevelse är att använda ett hanterat lagrings konto för användare. En användare kan antingen skapa ett nytt lagrings konto eller använda ett befintligt.

> [!IMPORTANT]
> Azure-kunder debiteras inte för de lagrings kostnader som är kopplade till startdiagnostik med ett hanterat lagrings konto till och med oktober 2020.
>
> Data blobbar för startdiagnostik (som består av loggar och ögonblicks bilds avbildningar) lagras i ett hanterat lagrings konto. Kunderna debiteras bara på använda GiBs av Blobbarna, inte på diskens etablerade storlek. Bildernas mätare används för fakturering av det hanterade lagrings kontot. Eftersom de hanterade kontona skapas på antingen standard-LRS eller standard-ZRS debiteras kunderna med $0,05/GB per månad enbart för storleken på deras diagnostikdata. Mer information om den här prissättningen finns i [priser för Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). Kunderna ser den här avgiften som är kopplad till deras resurs-URI för virtuella datorer. 

## <a name="boot-diagnostics-view"></a>Vyn Boot Diagnostics
Alternativet startdiagnostik finns på bladet för den virtuella datorn under avsnittet *support och fel sökning* i Azure Portal. Om du väljer Boot Diagnostics visas en skärm bild och information om serie loggen. Serie loggen innehåller kernel-meddelanden och skärm bilden är en ögonblicks bild av den virtuella datorns aktuella tillstånd. Baserat på om den virtuella datorn kör Windows eller Linux bestämmer vad den förväntade skärm bilden skulle se ut. För Windows ser användarna en Skriv bords bakgrund och för Linux visas en inloggnings tolk.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Skärm bild av Linux Boot Diagnostics":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Skärm bild av Linux Boot Diagnostics":::


## <a name="limitations"></a>Begränsningar
- Startdiagnostik är endast tillgängligt för virtuella datorer med Azure Resource Manager. 
- Startdiagnostiken har inte stöd för Premium Storage-konton, om ett Premium Storage-konto används för att starta diagnostiska användare får ett `StorageAccountTypeNotSupported` fel meddelande när den virtuella datorn startas. 
- Hanterade lagrings konton stöds i Resource Manager API-versionen "2020-06-01" och senare.
- Azures serie konsol är för närvarande inte kompatibel med ett hanterat lagrings konto för startdiagnostik. Lär dig mer om [Azures serie konsol](./troubleshooting/serial-console-overview.md).
- Startdiagnostik med ett hantera lagrings konto kan för närvarande endast tillämpas via Azure Portal. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azures serie konsol](./troubleshooting/serial-console-overview.md) och hur du använder startdiagnostik för att [Felsöka virtuella datorer i Azure](./troubleshooting/boot-diagnostics.md).