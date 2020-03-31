---
title: Arbeta med virtuella datorer och NSG:er i Azure Bastion
description: I den här artikeln beskrivs hur du införlivar NSG-åtkomst med Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087265"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Arbeta med NSG-åtkomst och Azure Bastion

När du arbetar med Azure Bastion kan du använda NSG:er (Network Security Groups). Mer information finns i [Säkerhetsgrupper](../virtual-network/security-overview.md). 

![Arkitektur](./media/bastion-nsg/nsg-architecture.png)

I det här diagrammet:

* Bastion-värden distribueras till det virtuella nätverket.
* Användaren ansluter till Azure-portalen med valfri HTML5-webbläsare.
* Användaren navigerar till den virtuella Azure-datorn till RDP/SSH.
* Connect Integration - En gång klick RDP/SSH-session i webbläsaren
* Ingen offentlig IP krävs på den virtuella Azure-datorn.

## <a name="network-security-groups"></a><a name="nsg"></a>Nätverkssäkerhetsgrupper

I det här avsnittet visas nätverkstrafiken mellan användaren och Azure Bastion och genom att rikta virtuella datorer i ditt virtuella nätverk:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure Bastion distribueras specifikt till AzureBastionSubnet.

* **Inträngning Trafik:**

   * **Ingress Traffic från offentligt internet:** Azure Bastion skapar en offentlig IP som behöver port 443 aktiverad på den offentliga IP-adressen för inkommande trafik. Port 3389/22 behöver INTE öppnas på AzureBastionSubnet.
   * **Inträngningstrafik från Azure Bastion kontrollplan:** För anslutning till kontrollplan aktiverar du port 443 inkommande från **GatewayManager-servicetag.** Detta gör det möjligt för kontrollplanet, det vill säga Gateway Manager att kunna prata med Azure Bastion.

* **Utgående trafik:**

   * **Egress Traffic till mål virtuella datorer:** Azure Bastion når virtuella mål virtuella datorer via privat IP. NSG:erna måste tillåta utgående trafik till andra virtuella mål-undernät för port 3389 och 22.
   * **Utgående trafik till andra offentliga slutpunkter i Azure:** Azure Bastion måste kunna ansluta till olika offentliga slutpunkter i Azure (till exempel för lagring av diagnostikloggar och mätarloggar). Därför behöver Azure Bastion utgående till 443 till **AzureCloud-tjänsttagg.**

* **Mål-VM-undernät:** Det här är undernätet som innehåller den virtuella måldator som du vill RDP/SSH till.

   * **Inträngningstrafik från Azure Bastion:** Azure Bastion når till måldatorn via privat IP. RDP/SSH-portar (portar 3389/22) måste öppnas på mål-VM-sidan över privat IP. På bästa sätt kan du lägga till IP-adressintervallet för Azure Bastion Undernät i den här regeln så att endast Bastion kan öppna dessa portar på måldatorn i ditt virtuella mål-VM-undernät.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>Använda NSG:er på AzureBastionSubnet

Om du skapar och tillämpar en NSG på ***AzureBastionSubnet***kontrollerar du att du har lagt till följande regler i NSG. Om du inte lägger till dessa regler misslyckas NSG-skapandet/uppdateringen:

* **Kontrollera planetanslutning:** Inkommande på 443 från GatewayManager
* **Diagnostikloggning och andra:** Utgående på 443 till AzureCloud. Regionala taggar i den här tjänsttaggen stöds inte ännu.
* **Mål-VM:** Utgående för 3389 och 22 till VirtualNetwork

Ett NSG-regelexempel finns tillgängligt som referens i den här [snabbstartsmallen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Bastion finns i [vanliga frågor](bastion-faq.md)och svar .
