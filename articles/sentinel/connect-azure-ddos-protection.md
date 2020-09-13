---
title: Anslut Azure DDoS Protection data till Azure Sentinel
description: Lär dig hur du matar in Azure DDoS Protection data i Azure Sentinel, så att du kan visa den, analysera den och undersöka den.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 09/01/2020
ms.author: yelevin
ms.openlocfilehash: e8e44f69965af1987bd5f023644d966b3caf1c77
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505876"
---
# <a name="connect-data-from-azure-ddos-protection"></a>Anslut data från Azure DDoS Protection

> [!IMPORTANT]
> Azure DDoS Protection data Connector i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

DDoS-attacker (distributed denial of Service) försöker avsluta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet. [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md), kombinerat med bästa praxis för program design, ger ett robust försvar mot DDoS-attacker. Du kan ansluta Azure DDoS Protection loggar till Azure Sentinel, så att du kan visa loggdata i arbets böcker, använda den för att skapa anpassade aviseringar och införliva den för att förbättra dina undersökningar. 

## <a name="prerequisites"></a>Krav

- Du måste ha läs-och Skriv behörighet på Azure Sentinel-arbetsytan.

- Du måste ha en konfigurerad [Azure DDoS standard Protection-Plan](../virtual-network/manage-ddos-protection.md#create-a-ddos-protection-plan).

- Du måste ha ett konfigurerat [virtuellt nätverk med Azure DDoS standard aktiverat](../virtual-network/manage-ddos-protection.md#enable-ddos-for-a-new-virtual-network).

## <a name="connect-to-azure-ddos-protection"></a>Anslut till Azure DDoS Protection
    
1. Välj **data kopplingar**på navigerings menyn i Azure Sentinel.

1. Välj **Azure DDoS Protection** i data kopplings galleriet och välj sedan **Öppna kopplings sida** i förhands gransknings fönstret.

1. Aktivera **diagnostikloggar** på alla brand väggar vars loggar du vill ansluta:

    1. Välj länken **Öppna diagnostikinställningar >** och välj en **offentlig IP** -adressresurs i listan.

    1. Välj **+ Lägg till diagnostisk inställning**.

    1. På skärmen **diagnostikinställningar** :
       - Ange ett namn i fältet  **namn på diagnos inställning** .

       - Markera kryss rutan **Skicka till Log Analytics** . Två nya fält kommer att visas under det. Välj relevant **prenumeration** och **Log Analytics arbets yta** (där Azure Sentinel finns).

       - Markera kryss rutorna för de regel typer vars loggar du vill mata in. Vi rekommenderar **DDoSProtectionNotifications**, **DDoSMitigationFlowLogs**och **DDoSMitigationReports**.

    1. Klicka på **Spara** längst upp på skärmen. Upprepa den här processen för eventuella ytterligare brand väggar (offentliga IP-adresser) som du har aktiverat DDoS-skydd för.

1. Om du vill använda det relevanta schemat i Log Analytics för Azure DDoS Protection aviseringar söker du efter **AzureDiagnostics**.

> [!NOTE]
>
> Med den här specifika data kopplingen visas anslutnings status indikatorerna (en färg remsa i galleriet för data anslutningar och anslutnings ikoner bredvid data typens namn) som *ansluten* (grön) endast om data har matats in vid någon tidpunkt under de senaste två veckorna. När två veckor har passerat utan data inmatning visas kopplingen som frånkopplad. Den tid då mer data kommer till kommer den *anslutna* statusen att returneras.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Azure DDoS Protection loggar till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
