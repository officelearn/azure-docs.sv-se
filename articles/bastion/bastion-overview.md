---
title: Azure Bastion | Microsoft-dokument
description: Läs mer om Azure Bastion
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 01/31/2020
ms.author: cherylmc
ms.openlocfilehash: 299a69675eed1ba958c6d13cf447407450df2abb
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411109"
---
# <a name="what-is-azure-bastion"></a>Vad är Azure Bastion?

Azure Bastion-tjänsten är en ny fullständigt plattformshanterad PaaS-tjänst som du etablerar i ditt virtuella nätverk. Det ger säker och sömlös RDP/SSH-anslutning till dina virtuella datorer direkt i Azure-portalen via TLS. När du ansluter via Azure Bastion behöver dina virtuella datorer inte någon offentlig IP-adress.

Bastion ger säker RDP- och SSH-anslutning till alla virtuella datorer i det virtuella nätverket där den etableras. Med Azure Bastion skyddas dina virtuella datorer från att exponera RDP/SSH-portar till omvärlden, samtidigt som du ger säker åtkomst med RDP/SSH. Med Azure Bastion ansluter du till den virtuella datorn direkt från Azure-portalen. Du behöver inte ytterligare en klient, agent eller programvara.

## <a name="architecture"></a>Arkitektur

Azure Bastion-distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator. När du har etablerat en Azure Bastion-tjänst i ditt virtuella nätverk är RDP/SSH-upplevelsen tillgänglig för alla dina virtuella datorer i samma virtuella nätverk.

RDP och SSH är några av de grundläggande sätt på vilka du kan ansluta till dina arbetsbelastningar som körs i Azure. Exponera RDP / SSH-portar över Internet är inte önskvärt och ses som en betydande hot yta. Detta beror ofta på protokollsårbarheter. Om du vill begränsa den här hotytan kan du distribuera skyddsvärdar (kallas även hoppservrar) på den offentliga sidan av perimeternätverket. Bastion värdservrar är utformade och konfigurerade för att motstå attacker. Bastion-servrar tillhandahåller också RDP- och SSH-anslutning till de arbetsbelastningar som sitter bakom bastionen, samt längre in i nätverket.

![Arkitektur](./media/bastion-overview/architecture.png)

Den här bilden visar arkitekturen för en Azure Bastion-distribution. I det här diagrammet:

* Bastion-värden distribueras i det virtuella nätverket.
* Användaren ansluter till Azure-portalen med valfri HTML5-webbläsare.
* Användaren väljer den virtuella datorn att ansluta till.
* Med ett enda klick öppnas RDP/SSH-sessionen i webbläsaren.
* Ingen offentlig IP krävs på den virtuella Azure-datorn.

## <a name="key-features"></a>Huvudfunktioner

Följande funktioner är tillgängliga:

* **RDP och SSH direkt i Azure-portalen:** Du kan direkt komma till RDP- och SSH-sessionen direkt i Azure-portalen med en sömlös upplevelse med ett enda klick.
* **Fjärrsession över TLS och brandväggskörning för RDP/SSH:** Azure Bastion använder en HTML5-baserad webbklient som automatiskt strömmas till din lokala enhet, så att du får din RDP/SSH-session över TLS på port 443 så att du kan korsa företagets brandväggar på ett säkert sätt.
* **Ingen offentlig IP krävs på den virtuella Azure-datorn:** Azure Bastion öppnar RDP/SSH-anslutningen till din virtuella Azure-dator med privat IP på din virtuella dator. Du behöver ingen offentlig IP på din virtuella dator.
* **Inget krångel med att hantera NSGs:** Azure Bastion är en fullständigt hanterad paaS-tjänst från Azure som är härdade internt för att ge dig säker RDP/SSH-anslutning. Du behöver inte använda nsg:er på Azure Bastion-undernätet. Eftersom Azure Bastion ansluter till dina virtuella datorer via privat IP kan du konfigurera dina NSG så att endast RDP/SSH tillåts från Azure Bastion. Detta tar bort besväret med att hantera NSGs varje gång du behöver för att säkert ansluta till dina virtuella datorer.
* **Skydd mot portskanning:** Eftersom du inte behöver exponera dina virtuella datorer för offentligt Internet skyddas dina virtuella datorer mot portskanning av oseriösa och skadliga användare som finns utanför det virtuella nätverket.
* **Skydda mot zero-day bedrifter. Härdning på ett ställe:** Azure Bastion är en helt plattformshanterad PaaS-tjänst. Eftersom den sitter i omkretsen av ditt virtuella nätverk behöver du inte oroa dig för att härda var och en av de virtuella datorerna i det virtuella nätverket. Azure-plattformen skyddar mot zero-day-kryphål genom att hålla Azure Bastion härdade och alltid uppdaterade för dig.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa en Azure Bastion-värdresurs](bastion-create-host-portal.md).
* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
