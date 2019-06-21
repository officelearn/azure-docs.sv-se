---
title: Azure Skyddsmiljö | Microsoft Docs
description: Lär dig mer om Azure Skyddsmiljö
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: overview
ms.date: 06/17/2019
ms.author: cherylmc
ms.openlocfilehash: d153c876366c8c747e06bf50a0ea26bcdcea10eb
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303596"
---
# <a name="what-is-azure-bastion-preview"></a>Vad är Azure Skyddsmiljö? (Förhandsversion)

Tjänsten Azure Skyddsmiljö är en ny platform totalhanterad PaaS-tjänst som du etablerar i ditt virtuella nätverk. Det ger säker och smidig RDP/SSH-anslutning till dina virtuella datorer direkt i Azure-portalen via SSL. När du ansluter via Azure Bastion behöver dina virtuella datorer inte någon offentlig IP-adress.

 Skyddsmiljö tillhandahåller säker RDP och SSH-anslutning till alla virtuella datorer i det virtuella nätverket där den har etablerats. Med hjälp av Azure Skyddsmiljö skyddar dina virtuella datorer från att exponera RDP/SSH-portar till omvärlden samtidigt som det ger säker åtkomst med hjälp av RDP/SSH. Med Azure Skyddsmiljö ansluta du till den virtuella datorn direkt från Azure-portalen. Du behöver inte en ytterligare, agent, eller program.

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Arkitektur

Azure Skyddsmiljö distribueras i det virtuella nätverket och, när den har distribuerats, den ger säker RDP/SSH-upplevelse för alla virtuella datorer i det virtuella nätverket. När du etablerar en Skyddsmiljö för Azure-tjänst i det virtuella nätverket är RDP/SSH-upplevelsen tillgängliga för alla dina virtuella datorer i samma virtuella nätverk. Distributionen är per virtuellt nätverk, inte per prenumeration/konto eller virtuell dator.

RDP och SSH är några av de grundläggande innebär genom vilka du kan ansluta till dina arbetsbelastningar som körs i Azure. Exponera RDP/SSH-portar via Internet inte är önskat och ses som en stor risk för angrepp. Det här är ofta på grund av sårbarheter i protokoll. Du kan distribuera skyddade värdar (även kallat jump-servrar) på den offentliga delen av perimeternätverket så att den innehåller den här threat ytan. Skyddsmiljö värdservrar designas och konfigurerad för att hantera attacker. Skyddade servrar även ge RDP och SSH-anslutning till arbetsbelastningarna bakom skyddsmiljön, samt ytterligare i nätverket.

![Arkitektur](./media/bastion-overview/architecture.png)

Den här bilden visar arkitekturen för en Skyddsmiljö för Azure-distribution. I det här diagrammet:

* Skyddsmiljö-värd har distribuerats i det virtuella nätverket.
* Användaren ansluter till Azure-portalen med alla HTML5-webbläsare.
* Användaren väljer den virtuella datorn ska ansluta till.
* RDP/SSH-sessionen öppnas i webbläsaren med ett enda klick.
* Ingen offentlig IP-adress krävs för virtuella Azure-datorn.

## <a name="key-features"></a>Huvudfunktioner

Följande funktioner är tillgängliga för allmänt tillgängliga förhandsversionen:

* **RDP och SSH direkt i Azure-portalen:** Du får direkt RDP och SSH-session direkt i Azure-portalen med en enda klickning sömlös upplevelse.
* **Fjärrsession över SSL och brandvägg edge traversal för RDP/SSH:** Azure Skyddsmiljö använder en HTML5-baserade web-klient som strömmas automatiskt till din lokala enhet så att du får RDP/SSH-sessionen via SSL på port 443 så att du kan bläddra i företags brandväggar på ett säkert sätt.
* **Ingen offentlig IP-adress krävs för virtuella Azure-datorn:** Azure Skyddsmiljö öppnar RDP/SSH-anslutningen till din Azure-dator som använder privat IP-adress på den virtuella datorn. Du behöver inte en offentlig IP-adress på den virtuella datorn.
* **Inget krångel med att hantera NSG: er:** Azure Skyddsmiljö är en helt hanterad plattform PaaS-tjänst från Azure som är härdade internt för att tillhandahålla du secure RDP/SSH-anslutningar. Du behöver inte tillämpa alla NSG: er i Azure Skyddsmiljö undernät. Eftersom Azure Skyddsmiljö ansluter till dina virtuella datorer över privat IP-adress måste konfigurera du dina NSG: er för att tillåta RDP/SSH från Azure Skyddsmiljö endast. Detta eliminerar krånglet med att hantera NSG: er varje gång som du behöver på ett säkert sätt ansluta dina virtuella datorer.
* **Skydd mot portskanning:** Eftersom du inte behöver att exponera dina virtuella datorer till offentliga Internet, är dina virtuella datorer skyddade mot skanna av otillåten och obehöriga användare utanför ditt virtuella nätverk.
* **Skydda dig mot nolldagarshot. Härdning på samma plats:** Azure Skyddsmiljö är en platform totalhanterad PaaS-tjänst. Eftersom den befinner sig i perimetern till ditt virtuella nätverk, behöver du inte bekymra dig om Härdning av var och en av de virtuella datorerna i det virtuella nätverket. Azure-plattformen som skyddar mot nolldagarskryphål genom att hålla Azure Skyddsmiljö strikt skyddad och ständigt uppdaterade åt dig.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

[!INCLUDE [Bastion FAQ](../../includes/bastion-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

* [Skapa en resurs för Azure Skyddsmiljö-värd](bastion-create-host-portal.md).
* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.
