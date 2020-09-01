---
title: Anslut Azure Firewall-data till Azure Sentinel
description: Lär dig hur du ansluter Azure Firewall-data till Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/30/2020
ms.author: yelevin
ms.openlocfilehash: 167cfe6b8fb1394291866e51ec14ec91bf9035a1
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89183703"
---
# <a name="connect-data-from-azure-firewall"></a>Anslut data från Azure-brandväggen

> [!IMPORTANT]
> Azure Firewall data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Firewall är en hanterad, molnbaserad tjänst för nätverkssäkerhet som skyddar dina Azure Virtual Network-resurser. Det är en fullständigt tillstånds känslig brand vägg som en tjänst med inbyggd hög tillgänglighet och obegränsad moln skalbarhet. 

Du kan ansluta Azure Firewall-loggar till Azure Sentinel, så att du kan visa loggdata i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra din undersökning.

Läs mer om [övervakning av Azure Firewall-loggar](https://docs.microsoft.com/azure/firewall/tutorial-diagnostics).

## <a name="prerequisites"></a>Krav

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

## <a name="connect-to-azure-firewall"></a>Ansluta till Azure-brandväggen
    
1. Välj **data kopplingar**på navigerings menyn i Azure Sentinel.

1. Välj **Azure-brandvägg** från data kopplings galleriet och välj sedan **Öppna kopplings sida**  i förhands gransknings fönstret.

1. Aktivera **diagnostikloggar** på alla brand väggar vars loggar du vill ansluta:

    1. Välj länken [öppna Azure Firewall-resursen >](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Network%2FazureFirewalls) .

    1. Från navigerings menyn **brand väggar** väljer du **diagnostikinställningar**.

    1. Välj **+ Lägg till diagnostisk inställning** längst ned i listan.

    1. På skärmen **diagnostikinställningar** anger du ett namn i fältet namn på  **diagnos inställningar** .
    
    1. Markera kryss rutan **Skicka till Log Analytics** . Två nya fält kommer att visas under det. Välj relevant **prenumeration** och **Log Analytics arbets yta** (där Azure Sentinel finns).

    1. Markera kryss rutorna för de regel typer vars loggar du vill mata in. Vi rekommenderar **AzureFirewallApplicationRule** och **AzureFirewallNetworkRule**.

    1. Välj **Spara** längst upp på skärmen.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure Firewall-aviseringar söker du efter **AzureDiagnostics**.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Azure Firewall-loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
