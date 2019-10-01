---
title: 'Arbeta med virtuella datorer och NSG: er i Azure skydds | Microsoft Docs'
description: Den här artikeln beskriver hur du införlivar NSG-åtkomst med Azure skydds
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/30/2019
ms.author: cherylmc
ms.openlocfilehash: 4f99b24435998fc4d0c7ab724c66a318586a80d4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71694935"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Arbeta med NSG Access och Azure skydds (för hands version)

När du arbetar med Azure-skydds kan du använda nätverks säkerhets grupper (NSG: er). Mer information finns i [säkerhets grupper](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Arkitektur](./media/bastion-nsg/nsg_architecture.png)

I det här diagrammet:

* Skydds-värden har distribuerats i det virtuella nätverket.
* Användaren ansluter till Azure Portal med valfri HTML5-webbläsare.
* Användaren väljer den virtuella dator som du vill ansluta till.
* Med ett enda klick öppnas RDP/SSH-sessionen i webbläsaren.
* Ingen offentlig IP-adress krävs på den virtuella Azure-datorn.

## <a name="nsg"></a>Nätverks säkerhets grupper

* **AzureBastionSubnet:** Azure-skydds distribueras i den aktuella AzureBastionSubnet.  
    * **Ingress trafik från offentlig Internet:** Azure-skydds skapar en offentlig IP-adress som behöver port 443 aktiverat på den offentliga IP-adressen för inkommande trafik. Port 3389/22 behöver inte öppnas på AzureBastionSubnet.
    * **Utgående trafik till virtuella mål datorer:** Azure-skydds kommer att nå de virtuella mål datorerna via privat IP. NSG: er måste tillåta utgående trafik till andra mål-VM-undernät.
* **Mål under nät för virtuell dator:** Det här är under nätet som innehåller den virtuella mål datorn som du vill ha RDP/SSH till.
    * **Ingress trafik från Azure skydds:** Azure-skydds kommer att nå den virtuella mål datorn via privat IP. RDP/SSH-portar (portarna 3389 och 22) måste öppnas på den virtuella mål datorns sida över privat IP.

## <a name="apply"></a>Tillämpa NSG: er på AzureBastionSubnet

Om du använder NSG: er på **AzureBastionSubnet**ska du tillåta följande två service märken för Azures kontroll plan och infrastruktur:

* **GatewayManager (endast Resource Manager)** : Den här taggen anger adressprefix för tjänsten Azure Gateway Manager. Om du anger GatewayManager för värdet tillåts eller nekas trafik till GatewayManager.  Om du skapar NSG: er på AzureBastionSubnet ska du aktivera GatewayManager-taggen för inkommande trafik.

* **AzureCloud (endast Resource Manager)** : Den här taggen anger IP-adressutrymmet för Azure, inklusive alla offentliga IP-adresser för data Center. Om du anger AzureCloud som värde tillåts eller nekas trafik till offentliga IP-adresser i Azure. Om du bara vill tillåta åtkomst till AzureCloud i en angiven region kan du ange regionen. Om du till exempel bara vill tillåta åtkomst till Azure-AzureCloud i regionen USA, östra, kan du ange AzureCloud. östra som en service tag. Om du skapar NSG: er på AzureBastionSubnet ska du aktivera AzureCloud-taggen för utgående trafik. Om du öppnar port 443 för inkommande till Internet bör du inte behöva aktivera AzureCloud-taggen för inkommande trafik.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure skydds finns i [vanliga frågor och svar](bastion-faq.md)
