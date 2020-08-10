---
title: Azure Boot-diagnostik
description: Översikt över Azure Boot Diagnostics och Managed Boot Diagnostics
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042979"
---
# <a name="azure-boot-diagnostics"></a>Azure Boot-diagnostik

Startdiagnostik är en fel söknings funktion för Azure Virtual Machines (VM) som gör det möjligt att diagnostisera VM-startfel. Med startdiagnostik kan en användare se statusen för den virtuella datorn när den startas genom att samla in information om seriell logg information och skärm dum par.

## <a name="boot-diagnostics-storage-account"></a>Lagrings konto för startdiagnostik
Som standard aktive ras startdiagnostik för alla virtuella datorer som skapats med Azure Portal och använder ett hanterat lagrings konto. Genom att använda ett hanterat lagrings konto får du en betydande förbättring av distributions tiden för virtuella datorer. Därför rekommenderar vi att kunder använder startdiagnostik med ett hanterat lagrings konto för alla virtuella datorer.

> [!NOTE]
> Azure-kunder debiteras inte för lagring när väljer använder startdiagnostik med ett hanterat lagrings konto fram till den 1 oktober 2020.

En alternativ starts upplevelse är att använda ett anpassat lagrings konto. En användare kan antingen skapa ett nytt lagrings konto eller använda ett befintligt. Mer information om anpassade lagrings konton finns i [Översikt över lagrings konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="Skärm bild som visar hur du aktiverar startdiagnostik":::

## <a name="boot-diagnostics-view"></a>Vyn Boot Diagnostics
Alternativet startdiagnostik finns på bladet för den virtuella datorn under avsnittet *support och fel sökning* i Azure Portal. Om du väljer Boot Diagnostics visas en skärm bild och information om serie loggen. Serie loggen innehåller kernel-meddelanden och skärm bilden är en ögonblicks bild av den virtuella datorns aktuella tillstånd. Baserat på om den virtuella datorn kör Windows eller Linux bestämmer vad den förväntade skärm bilden skulle se ut. För Windows ser användarna en Skriv bords bakgrund och för Linux visas en inloggnings tolk.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Skärm bild av Linux Boot Diagnostics":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Skärm bild av Windows-startdiagnostik":::


## <a name="limitations"></a>Begränsningar
- Startdiagnostik är endast tillgängligt för virtuella datorer med Azure Resource Manager (ARM). 
- Startdiagnostiken har inte stöd för Premium Storage-konton, om ett Premium Storage-konto används för att starta diagnostiska användare får ett `StorageAccountTypeNotSupported` fel meddelande när den virtuella datorn startas. 
- Azures serie konsol har för närvarande inte stöd för ett hanterat lagrings konto för startdiagnostik.

## <a name="next-steps"></a>Efterföljande moment

Lär dig mer om [Azures serie konsol](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) och hur du använder startdiagnostik för att [Felsöka virtuella datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
