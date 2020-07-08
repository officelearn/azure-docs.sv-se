---
title: Azure Firewall FQDN-filtrering i nätverks regler (förhands granskning)
description: Så här använder du FQDN-filtrering i nätverks regler
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 46d1f2eae1342901a660f99891d1ac1529e8ad77
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85569315"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>FQDN-filtrering i nätverks regler (förhands granskning)

> [!IMPORTANT]
> FQDN-filtrering i nätverks regler är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ett fullständigt kvalificerat domän namn (FQDN) representerar ett domän namn för en värd. Ett domän namn är associerat med en eller flera IP-adresser. Du kan tillåta eller blockera FQDN: er och FQDN-Taggar i program regler. Med anpassade inställningar för DNS och DNS-proxy kan du också använda FQDN-filtrering i nätverks regler.

## <a name="how-it-works"></a>Så här fungerar det

Azure-brandväggen översätter FQDN till en IP-adress (er) med sina DNS-inställningar och bearbetar regel bearbetningen baserat på Azure DNS eller en anpassad DNS-konfiguration.

Om du vill använda FQDN i nätverks regler bör du aktivera DNS-proxy. Om du inte aktiverar DNS-proxy, är tillförlitlig regel bearbetning utsatt för risk. När den är aktive rad dirigeras DNS-trafik till Azure-brandväggen där du kan konfigurera en anpassad DNS-server. Sedan använder brand väggen och klienterna samma konfigurerade DNS-server. Om DNS-proxy inte är aktive rad kan Azure-brandväggen producera ett annat svar eftersom klienten och brand väggen kan använda olika servrar för namn matchning. FQDN-filtrering i nätverks regler kan vara fel eller inkonsekvent om klienten och brand väggen får olika DNS-svar.

## <a name="next-steps"></a>Nästa steg

[DNS-inställningar för Azure-brandväggen](dns-settings.md)
