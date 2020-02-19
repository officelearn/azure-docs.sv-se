---
title: Översikt över Azure Firewall Manager Preview-princip
description: Lär dig mer om Azure Firewall Manager-principer
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 1308f4ba3335f2fd2633f6e39a679cd6477a4b5c
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2020
ms.locfileid: "77445023"
---
# <a name="azure-firewall-manager-preview-policy-overview"></a>Översikt över Azure Firewall Manager Preview-princip

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Brand Väggs principen är en Azure-resurs som innehåller samlingar för NAT, nätverk och program regler samt hot information-inställningar. Det är en global resurs som kan användas i flera Azure Firewall-instanser i säkra virtuella hubbar och virtuella hubb nätverk. Principer fungerar mellan regioner och prenumerationer.

![Azure Firewall Manager-princip](media/policy-overview/policy-overview.png)

## <a name="policy-creation-and-association"></a>Skapa och associera princip

En princip kan skapas och hanteras på flera sätt, inklusive Azure Portal, REST API, mallar, Azure PowerShell och CLI.

Du kan också migrera befintliga regler från Azure-brandväggen med hjälp av portalen eller Azure PowerShell för att skapa principer. Mer information finns i [så här migrerar du Azure Firewall-konfigurationer till en Azure Firewall-princip (för hands version)](migrate-to-policy.md). 

Principer kan associeras med en eller flera virtuella nav eller virtuella nätverk. Brand väggen kan finnas i alla prenumerationer som är kopplade till ditt konto och i vilken region som helst.

## <a name="hierarchical-policies"></a>Hierarkiska principer

Nya principer kan skapas från grunden eller ärvas från befintliga principer. Arv gör att DevOps kan skapa lokala brand Väggs principer överst i organisationen som har tilldelats bas principen.

Principer som skapats med icke-tomma överordnade principer ärver alla regel samlingar från den överordnade principen. Nätverks regel samlingarna som ärvs från en överordnad princip prioriteras alltid ovanför nätverks regel samlingar som definierats som en del av en ny princip. Samma logik gäller även för program regel samlingar. Nätverks regel samlingar bearbetas dock alltid innan program regel samlingar, oavsett arv.

Hot informations läget ärvs också från den överordnade principen. Du kan ställa in ditt hot informations läge på ett annat värde om du vill åsidosätta det här beteendet, men du kan inte inaktivera det. Det går bara att åsidosätta med ett striktare värde. Om din överordnade princip till exempel är inställd på **endast avisering**, kan du konfigurera den här lokala principen för att **Varna och neka**.

NAT-regel samlingar ärvs inte eftersom de är specifikt för en specifik brand vägg.

Med arv tillämpas eventuella ändringar i den överordnade principen automatiskt ned till associerade underordnade brand Väggs principer.

## <a name="traditional-rules-and-policies"></a>Traditionella regler och principer

Azure-brandväggen stöder både traditionella regler och principer. I följande tabell jämförs principer och regler:


|         |Princip  |Regler  |
|---------|---------|---------|
|Innehåller     |Inställningar för NAT, nätverk, program regler och hot information|Regler för NAT, nätverk och program |
|Skyddar     |Virtuella hubbar och virtuella nätverk|Endast virtuella nätverk|
|Portalmiljö     |Central hantering med hjälp av brand Väggs hanteraren|Fristående brand Väggs upplevelse|
|Stöd för flera brand väggar     |Brand Väggs principen är en separat resurs som kan användas i olika brand väggar|Exportera och importera regler manuellt eller med hjälp av hanterings lösningar från tredje part |
|Priser     |Faktureras baserat på brand Väggs Association. Se [prissättning](#pricing).|Kostnadsfri|
|Distributions metoder som stöds     |Portal, REST API, mallar, Azure PowerShell och CLI|Portal, REST API, mallar, PowerShell och CLI. |
|Versionsstatus     |Offentlig förhandsversion|Allmän tillgänglighet|

## <a name="pricing"></a>Priser

Principer faktureras baserat på brand Väggs associationer. En princip med noll eller en brand Väggs koppling är kostnads fri. En princip med flera brand Väggs associationer debiteras enligt ett fast pris. Mer information finns i [priser för Azure Firewall Manager](https://azure.microsoft.com/pricing/details/firewall-manager/).

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar en Azure-brandvägg finns i [Självstudier: skydda ditt moln nätverk med för hands versionen av Azure Firewall Manager med hjälp av Azure Portal](secure-cloud-network.md).
