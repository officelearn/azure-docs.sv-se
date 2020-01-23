---
title: 'Arbeta med virtuella datorer och NSG: er i Azure skydds | Microsoft Docs'
description: Den här artikeln beskriver hur du införlivar NSG-åtkomst med Azure skydds
services: bastion
author: ashjain
ms.service: bastion
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ashishj
ms.openlocfilehash: 8fb48b3dbe0d727f92bd2e0fec91ac2dbf33338b
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547633"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Arbeta med NSG-åtkomst och Azure skydds

När du arbetar med Azure-skydds kan du använda nätverks säkerhets grupper (NSG: er). Mer information finns i [säkerhets grupper](../virtual-network/security-overview.md). 

![Arkitektur](./media/bastion-nsg/nsg-architecture.png)

I det här diagrammet:

* Skydds-värden har distribuerats till det virtuella nätverket.
* Användaren ansluter till Azure Portal med valfri HTML5-webbläsare.
* Användaren navigerar till RDP/SSH för den virtuella Azure-datorn.
* Ansluta integrering – RDP/SSH-session med enkel klickning i webbläsaren
* Ingen offentlig IP-adress krävs på den virtuella Azure-datorn.

## <a name="nsg"></a>Nätverks säkerhets grupper

I det här avsnittet visas nätverks trafiken mellan användaren och Azure-skydds och för virtuella datorer i det virtuella nätverket:

### <a name="azurebastionsubnet"></a>AzureBastionSubnet

Azure-skydds distribueras specifikt till AzureBastionSubnet.

* **Ingress trafik:**

   * **Ingress trafik från offentlig Internet:** Azure-skydds skapar en offentlig IP-adress som behöver port 443 aktiverat på den offentliga IP-adressen för inkommande trafik. Port 3389/22 behöver inte öppnas på AzureBastionSubnet.
   * **Ingress trafik från Azure skydds Control plan:** För kontroll Plans anslutning aktiverar du port 443 inkommande från **GatewayManager** service tag. Detta gör det möjligt för kontroll planet, det vill säga att Gateway Manager kan kommunicera med Azure-skydds.

* **Utgående trafik:**

   * **Utgående trafik till virtuella mål datorer:** Azure-skydds kommer att nå de virtuella mål datorerna via privat IP. NSG: er måste tillåta utgående trafik till andra mål-VM-undernät för port 3389 och 22.
   * **Utgående trafik till andra offentliga slut punkter i Azure:** Azure-skydds måste kunna ansluta till olika offentliga slut punkter i Azure (till exempel för lagring av diagnostikloggar och avläsnings loggar). Av den anledningen behöver Azure skydds utgående till 443 till **AzureCloud** service tag.

* **Mål under nät för virtuell dator:** Det här är under nätet som innehåller den virtuella mål datorn som du vill ha RDP/SSH till.

   * **Ingress trafik från Azure skydds:** Azure-skydds kommer att nå den virtuella mål datorn via privat IP. RDP/SSH-portar (portarna 3389/22) måste öppnas på den virtuella mål dator sidan över privat IP. Vi rekommenderar att du lägger till IP-adressintervallet för Azure skydds-undernätet i den här regeln för att bara tillåta skydds att kunna öppna portarna på de virtuella mål datorerna i det virtuella mål dator under nätet.

## <a name="apply"></a>Tillämpa NSG: er på AzureBastionSubnet

Om du skapar och använder en NSG för ***AzureBastionSubnet***kontrollerar du att du har lagt till följande regler i din NSG. Om du inte lägger till de här reglerna kommer NSG för att skapa/uppdatera inte att fungera:

* **Kontroll Plans anslutning:** Inkommande den 443 från GatewayManager
* **Diagnostikloggning och andra:** Utgående på 443 till AzureCloud. Regionala Taggar i denna service tag stöds inte än.
* **Virtuell måldator:** Utgående för 3389 och 22 till VirtualNetwork

Ett exempel på en regel för NSG finns i den här [snabb starts mal len](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg).

## <a name="next-steps"></a>Nästa steg

Mer information om Azure skydds finns i [vanliga frågor och svar](bastion-faq.md).
