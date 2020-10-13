---
title: Översikt över underhålls kontroll för virtuella Azure-datorer med hjälp av Azure Portal
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 4d126d1e6bb92d2079ac3c0d95f754cb9aab3c21
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978619"
---
# <a name="managing-platform-updates-with-maintenance-control"></a>Hantera plattforms uppdateringar med underhålls kontroll 

Hantera plattforms uppdateringar, som inte kräver omstart, med hjälp av underhålls kontroll. Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitligheten, prestandan, säkerheten eller lansera nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbets belastningar, t. ex. spel, medie direkt uppspelning och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en virtuell dator som fryser eller kopplar från för underhåll. Med underhålls kontrollen får du möjlighet att vänta på plattforms uppdateringar och tillämpa dem i ett rullande 35-dagars fönster. 

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på dina isolerade virtuella datorer och Azure-dedikerade värdar.

Med underhålls kontroll kan du:
- Batch-uppdateringar till ett uppdaterings paket.
- Vänta upp till 35 dagar för att installera uppdateringar. 
- Automatisera plattforms uppdateringar för underhålls perioden med [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Underhålls konfigurationer fungerar mellan prenumerationer och resurs grupper. 

## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas på en [dedikerad värd](./dedicated-hosts.md)eller skapas med en [isolerad VM-storlek](isolation.md).
- Efter 35 dagar tillämpas en uppdatering automatiskt.
- Användaren måste ha åtkomst till **resurs deltagare** .

## <a name="management-options"></a>Hanterings alternativ

Du kan skapa och hantera underhålls konfigurationer med något av följande alternativ:

- [Azure CLI](maintenance-control-cli.md)
- [Azure PowerShell](maintenance-control-powershell.md)
- [Azure-portalen](maintenance-control-portal.md)

Ett Azure Functions-exempel finns i [Schemalägga underhålls uppdateringar med underhålls kontroll och Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).

## <a name="next-steps"></a>Nästa steg

Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).