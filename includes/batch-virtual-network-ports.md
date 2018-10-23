---
title: ta med fil
description: ta med fil
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 10/05/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 9246dea7fa12e5ac9378203e96352e917679525b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49312421"
---
### <a name="general-requirements"></a>Allmänna krav

* Det virtuella nätverket måste vara i samma prenumeration och region som det Batch-konto som du använder för att skapa din pool.

* Den pool som använder det virtuella nätverket kan ha högst 4 096 noder.

* Det undernät som anges för poolen måste ha tillräckliga otilldelade IP-adresser för det antal virtuella datorer som är mål för poolen. Summan av egenskaperna `targetDedicatedNodes` och `targetLowPriorityNodes` för poolen. Om undernätet inte har tillräckligt med lediga IP-adresser, allokerar poolen datornoderna partiellt och ett storleksändringsfel inträffar. 

* Azure Storage-slutpunkt behöver matchas av eventuella anpassade DNS-servrar som servar ditt virtuella nätverk. Mer specifikt, ska URL:er av formen `<account>.table.core.windows.net`, `<account>.queue.core.windows.net` och `<account>.blob.core.windows.net` vara matchningsbara. 

Ytterligare krav för virtuella nätverk varierar beroende på huruvida Batch-poolen finns i den virtuella datorkonfigurationen eller Cloud Services-konfigurationen. För nya pooldistributioner till ett virtuellt nätverk rekommenderas konfigurationen för virtuell dator.

### <a name="pools-in-the-virtual-machine-configuration"></a>Pooler i konfigurationen för virtuell dator

**Virtuella nätverk som stöds** – endast Azure Resource Manager-baserade virtuella nätverk

**Undernät-ID** – när du anger undernätet med hjälp av Batch API:er använder du *resursidentifieraren* för undernätet. Undernät-ID har formatet:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

**Behörigheter** – kontrollera huruvida dina säkerhetsprinciper eller lås på det virtuella nätverkets prenumeration eller resursgruppen begränsar en användares behörigheter att hantera det virtuella nätverket.

**Ytterligare nätverksresurser** – Batch tilldelar automatiskt ytterligare nätverksresurser i den resursgrupp som innehåller det virtuella nätverket. För varje 50 dedikerade noder (eller varje 20 lågprioritetsnoder) allokerar Batch: 1 nätverkssäkerhetsgrupp (NSG), 1 offentlig IP-adress och 1 lastbalanserare. Dessa resurser begränsas av prenumerationens [resurskvoter](../articles/azure-subscription-service-limits.md). För stora pooler kan du behöva begära en kvotökning för en eller flera av dessa resurser.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna samt utgående kommunikation för att kommunicera med Azure Storage eller andra resurser. För pooler i konfigurationen för virtuell dator lägger Batch till nätverkssäkerhetsgrupper (NSG:er) på nivån för nätverksgränssnitt (NIC) som är kopplade till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

* Inkommande TCP-trafik på portarna 29876 och 29877 från Batch-tjänstrollens IP-adresser. 
* Inkommande TCP-trafik på port 22 (Linux-noder) eller port 3389 (Windows-noder) för att tillåta fjärråtkomst.
* Utgående trafik på vilken port som helst till det virtuella nätverket.
* Utgående trafik på vilken port som helst till Internet.

> [!IMPORTANT]
> Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om kommunikation till beräkningsnoderna i det angivna undernätet nekas av en NSG, ställer Batch-tjänsten in status för beräkningsnoderna till **oanvändbara**.

Du behöver inte ange NSG:er på undernätverksnivån eftersom Batch konfigurerar sina egna NSG:er. Om det angivna undernätet dock har associerade nätverkssäkerhetsgrupper (NSG:er) och/eller en brandvägg konfigurerar du säkerhetsreglerna för inkommande och utgående trafik enligt det som visas i följande tabeller. Konfigurera inkommande trafik på port 3389 (Windows) eller 22 (Linux) endast om du behöver tillåta fjärråtkomst till de virtuella pooldatorerna. Det krävs inte för att de virtuella pooldatorerna ska kunna användas.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
Alla <br /><br />Även om detta kräver att du aktivt ”tillåter alla” gäller batchtjänsten för en nätverkssäkerhetsgrupp på nätverkets gränssnittsnivå på varje virtuell dator som skapats under konfigurationen av en virtuell dator som filtrerar ut alla IP-adresser för icke-Batch-tjänster. | * | Alla | 29876–29877 | TCP | Tillåt |
| Användardatorer som används i felsökningssyfte för fjärråtkomst till de virtuella pooldatorerna. | * | Alla |  3389 (Windows), 22 (Linux) | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Måltjänsttagg | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
| Alla | 443 | [Tjänsttagg](../articles/virtual-network/security-overview.md#service-tags) | Lagring (i samma region som dit Batch-konto och virtuella nätverk)  | Alla | Tillåt |

### <a name="pools-in-the-cloud-services-configuration"></a>Pooler i Cloud Services-konfigurationen

**Virtuella nätverk som stöds** – endast klassiska virtuella nätverk

**Undernät-ID** – när du anger undernätet med hjälp av Batch API:er använder du *resursidentifieraren* för undernätet. Undernät-ID har formatet:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicVirtualNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Behörigheter** – tjänstens huvudnamn för `MicrosoftAzureBatch` måste ha rollen rollbaserad åtkomstkontroll (RBAC) för `Classic Virtual Machine Contributor` för den angivna virtuella nätverket.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna samt utgående kommunikation för att kommunicera med Azure Storage eller andra resurser.

Du behöver inte ange en NSG eftersom Batch endast konfigurerar inkommande kommunikation från Batch-IP-adresser till poolnoderna. Om det angivna undernätet dock har associerade NSG:er (NSG:er) och/eller en brandvägg konfigurerar du säkerhetsreglerna för inkommande och utgående trafik enligt det som visas i följande tabeller. Om kommunikation till beräkningsnoderna i det angivna undernätet nekas av en NSG, ställer Batch-tjänsten in status för beräkningsnoderna till **oanvändbara**.

 Konfigurera inkommande trafik på port 3389 (Windows) eller 22 (Linux) endast om du behöver tillåta fjärråtkomst till poolnoderna. Det krävs inte för att poolnoderna ska kunna användas.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
Alla <br /><br />Även om detta i princip kräver ”tillåt alla” så tillämpar Batch-tjänsten en ACL-regel på nivån för varje nod som filtrerar ut alla IP-adresser som inte gäller för Batch-tjänsten. | * | Alla | 10100, 20100, 30100 | TCP | Tillåt |
| Användardatorer som används i felsökningssyfte för fjärråtkomst till de virtuella pooldatorerna. | * | Alla |  3389 (Windows), 22 (Linux) | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
| Alla | * | Alla | 443  | Alla | Tillåt |