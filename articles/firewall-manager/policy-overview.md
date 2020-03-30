---
title: Översikt över förhandsprinciper för Azure Firewall Manager
description: Lär dig mer om Azure Firewall Manager-principer
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77445023"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Översikt över förhandsprinciper för Azure Firewall Manager

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Brandväggsprincipen är en Azure-resurs som innehåller SAMLINGAR FÖR NAT-, nätverks- och programregel samt inställningar för hotinformation. Det är en global resurs som kan användas i flera Azure-brandväggsinstanser i säkra virtuella hubbar och virtuella hubbnätverk. Principer fungerar mellan regioner och prenumerationer.

![Azure-brandväggshanterarens princip](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Skapande och föreningsförening för politik

En princip kan skapas och hanteras på flera sätt, inklusive Azure-portalen, REST API, mallar, Azure PowerShell och CLI.

Du kan också migrera befintliga regler från Azure-brandväggen med hjälp av portalen eller Azure PowerShell för att skapa principer. Mer information finns i [Så här migrerar du Azure-brandväggskonfigurationer till Azure Firewall Policy (förhandsversion).](migrate-to-policy.md) 

Principer kan associeras med en eller flera virtuella hubbar eller virtuella nätverk. Brandväggen kan finnas i alla prenumerationer som är kopplade till ditt konto och i alla regioner.

## <a name="hierarchical-policies"></a>Hierarkiska principer

Nya principer kan skapas från grunden eller ärvs från befintliga principer. Arv gör det möjligt för DevOps att skapa lokala brandväggsprinciper ovanpå den organisationsuppdragade basprincipen.

Principer som skapats med icke-tomma överordnade principer ärver alla regelsamlingar från den överordnade principen. Nätverksregelsamlingar som ärvs från en överordnad princip prioriteras alltid ovanför nätverksregelsamlingar som definieras som en del av en ny princip. Samma logik gäller även för programregelsamlingar. Nätverksregelsamlingar bearbetas dock alltid före programregelsamlingar oavsett arv.

Hot Intelligence-läget ärvs också från den överordnade principen. Du kan ställa in hot Intelligence-läget till ett annat värde för att åsidosätta det här beteendet, men du kan inte stänga av det. Det är bara möjligt att åsidosätta med ett strängare värde. Om den överordnade principen till exempel är inställd **på Avisering kan**du konfigurera den här lokala principen så att **den aviseras och nekas**.

NAT-regelsamlingar ärvs inte eftersom de är specifika för en viss brandvägg.

Med arv tillämpas alla ändringar i den överordnade principen automatiskt ned på associerade underordnade principer för brandvägg.

## <a name="traditional-rules-and-policies"></a>Traditionella regler och policyer

Azure Firewall stöder både traditionella regler och principer. I följande tabell jämförs principer och regler:


|         |Princip  |Regler  |
|---------|---------|---------|
|Innehåller     |INSTÄLLNINGAR FÖR NAT, Nätverk, Program och Hot Intelligence|NAT-, nätverks- och programregler |
|Skyddar     |Virtuella nav och virtuella nätverk|Endast virtuella nätverk|
|Portalmiljö     |Central hantering med hjälp av Brandväggshanteraren|Fristående brandväggsupplevelse|
|Stöd för flera brandväggar     |Brandväggsprincipen är en separat resurs som kan användas i brandväggar|Exportera och importera regler manuellt eller använda hanteringslösningar från tredje part |
|Prissättning     |Faktureras baserat på brandväggsassociation. Se [Priser](#pricing).|Kostnadsfri|
|Distributionsmekanismer som stöds     |Portal, REST API, mallar, Azure PowerShell och CLI|Portal, REST API, mallar, PowerShell och CLI. |
|Frisläppningsstatus     |Offentlig förhandsversion|Allmän tillgänglighet|

## <a name="pricing"></a>Prissättning

Principer faktureras baserat på brandväggsassociationer. En policy med noll eller en brandväggsförening är gratis. En princip med flera brandväggsassociationer faktureras med en fast hastighet. Mer information finns i [Prissättningen för Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Nästa steg

Mer information om hur du distribuerar en Azure-brandvägg finns i [Självstudiekurs: Skydda molnnätverket med förhandsversionen av Azure Firewall Manager med Azure-portalen](secure-cloud-network.md).
