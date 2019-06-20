---
title: 'Arbeta med virtuella datorer och NSG: er i Azure Skyddsmiljö | Microsoft Docs'
description: Den här artikeln beskrivs hur du införlivar NSG åtkomst med Azure Skyddsmiljö
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: e7210b2b1be072f5326070d768d5fe12c386ee0b
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191608"
---
# <a name="working-with-nsg-access-and-azure-bastion-preview"></a>Arbeta med NSG åtkomst och Azure Skyddsmiljö (förhandsversion)

När du arbetar med Azure Skyddsmiljö, kan du använda nätverkssäkerhetsgrupper (NSG). Mer information finns i [säkerhetsgrupper](../virtual-network/security-overview.md). 

> [!IMPORTANT]
> Den offentliga förhandsversionen tillhandahålls utan serviceavtal och bör inte användas för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller har begränsad funktionalitet, eller så är de inte tillgängliga på alla Azure-platser. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

![Arkitektur](./media/bastion-nsg/nsg_architecture.png)

I det här diagrammet:

* Skyddsmiljö-värd har distribuerats i det virtuella nätverket.
* Användaren ansluter till Azure-portalen med alla HTML5-webbläsare.
* Användaren väljer den virtuella datorn ska ansluta till.
* RDP/SSH-sessionen öppnas i webbläsaren med ett enda klick.
* Ingen offentlig IP-adress krävs för virtuella Azure-datorn.

## <a name="nsg"></a>Nätverkssäkerhetsgrupper

* **AzureBastionSubnet:** Azure Skyddsmiljö distribueras i specifika AzureBastionSubnet.  
    * **Inkommande trafik från offentliga internet:** Azure-Skyddsmiljö skapar en offentlig IP-adress som behöver port 443 aktiverad på den offentliga IP-Adressen för inkommande trafik. Porten 3389/22 måste inte öppnas på AzureBastionSubnet.
    * **Utgående trafik till målet virtuella datorer:** Azure Skyddsmiljö når de virtuella måldatorerna över privat IP-adress. NSG: erna måste tillåta utgående trafik till andra mål VM-undernät.
* **Målundernätet för virtuell dator:** Det här är det undernät som innehåller den virtuella måldatorn som du vill RDP/SSH till.
    * **Inkommande trafik från Azure Skyddsmiljö:** Azure Skyddsmiljö når över privat IP-adress till den Virtuella måldatorn. RDP/SSH-portar (port 3389 och 22, respektive) måste du öppna på målet VM sida över privat IP-adress.

## <a name="apply"></a>Tillämpa Nätverkssäkerhetsgrupper på AzureBastionSubnet

Om du använder NSG: er till de **AzureBastionSubnet**, Tillåt att följande två tjänsttaggar för Azure kontrollplanet och infrastruktur:

* **GatewayManager (endast Resource Manager)** : Den här taggen anger adressprefix för tjänsten Azure Gateway Manager. Om du anger GatewayManager för värdet trafik tillåts eller nekas till GatewayManager.

* **AzureCloud (endast Resource Manager)** : Den här taggen anger IP-adressutrymmet för Azure, inklusive alla datacenter offentliga IP-adresser. Om du anger AzureCloud för värdet trafik tillåts eller nekas åtkomst till Azure offentliga IP-adresser. Om du bara vill tillåta åtkomst till AzureCloud i en viss region kan du ange regionen. Om du vill tillåta endast åtkomst till Azure AzureCloud i regionen östra USA kan du exempelvis ange AzureCloud.EastUS som tjänsttagg.

## <a name="next-steps"></a>Nästa steg

Läs mer om Azure Skyddsmiljö den [vanliga frågor och svar](bastion-faq.md)