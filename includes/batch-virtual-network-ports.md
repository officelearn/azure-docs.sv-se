---
title: ta med fil
description: ta med fil
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 07/16/2019
ms.author: lahugh
ms.custom: include file
ms.openlocfilehash: 98f5269c27643e7ce6c0aaf9b359503a124d9232
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75663183"
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

**Ytterligare nätverksresurser** – Batch tilldelar automatiskt ytterligare nätverksresurser i den resursgrupp som innehåller det virtuella nätverket. För varje 50 dedikerade noder (eller varje 20 lågprioritetsnoder) allokerar Batch: 1 nätverkssäkerhetsgrupp (NSG), 1 offentlig IP-adress och 1 lastbalanserare. Dessa resurser begränsas av prenumerationens [resurskvoter](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). För stora pooler kan du behöva begära en kvotökning för en eller flera av dessa resurser.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna samt utgående kommunikation för att kommunicera med Azure Storage eller andra resurser. För pooler i konfigurationen för virtuell dator lägger Batch till nätverkssäkerhetsgrupper (NSG:er) på nivån för nätverksgränssnitt (NIC) som är kopplade till virtuella datorer. De här NSG:erna konfigurerar automatiskt regler för inkommande och utgående trafik för att tillåta följande trafik:

* Inkommande TCP-trafik på portarna 29876 och 29877 från Batch-tjänstrollens IP-adresser. 
* Inkommande TCP-trafik på port 22 (Linux-noder) eller port 3389 (Windows-noder) för att tillåta fjärråtkomst. För vissa typer av aktiviteter med flera instanser i Linux (till exempel MPI) måste du även tillåta SSH port 22-trafik för IP-adresser i under nätet som innehåller batch-datornoder.
* Utgående trafik på vilken port som helst till det virtuella nätverket.
* Utgående trafik på vilken port som helst till Internet.

> [!IMPORTANT]
> Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om kommunikation till beräkningsnoderna i det angivna undernätet nekas av en NSG, ställer Batch-tjänsten in status för beräkningsnoderna till **oanvändbara**.

Du behöver inte ange NSG:er på undernätverksnivån eftersom Batch konfigurerar sina egna NSG:er. Om det angivna undernätet dock har associerade nätverkssäkerhetsgrupper (NSG:er) och/eller en brandvägg konfigurerar du säkerhetsreglerna för inkommande och utgående trafik enligt det som visas i följande tabeller. Konfigurera inkommande trafik på port 3389 (Windows) eller 22 (Linux) endast om du behöver tillåta fjärråtkomst till poolens virtuella datorer från externa källor. Det krävs inte för att de virtuella pooldatorerna ska kunna användas. Observera att du måste aktivera trafik för det virtuella nätverkets undernät på port 22 för Linux om du använder vissa typer av aktiviteter med flera instanser, till exempel MPI.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Service tag för källa | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- | --- |
| Gäller inte | `BatchNodeManagement` [service tag](../articles/virtual-network/security-overview.md#service-tags) | * | Alla | 29876–29877 | TCP | Tillåt |
| Användar Källans IP-adresser för fjärråtkomst fjärråtkomst till Compute-noder och/eller Compute Node-undernät för Linux-aktiviteter med flera instanser, om det behövs. | Gäller inte | * | Alla | 3389 (Windows), 22 (Linux) | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Måltjänsttagg | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- | --- |
| Alla | * | [Tjänsttagg](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (i samma region som batch-kontot och VNet) | 443 | TCP | Tillåt |

### <a name="pools-in-the-cloud-services-configuration"></a>Pooler i Cloud Services-konfigurationen

**Virtuella nätverk som stöds** – endast klassiska virtuella nätverk

**Undernät-ID** – när du anger undernätet med hjälp av Batch API:er använder du *resursidentifieraren* för undernätet. Undernät-ID har formatet:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.ClassicNetwork /virtualNetworks/{network}/subnets/{subnet}
  ```

**Behörigheter** – tjänstens huvudnamn för `Microsoft Azure Batch` måste ha rollen rollbaserad åtkomstkontroll (RBAC) för `Classic Virtual Machine Contributor` för den angivna virtuella nätverket.

#### <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna samt utgående kommunikation för att kommunicera med Azure Storage eller andra resurser.

Du behöver inte ange en NSG eftersom Batch endast konfigurerar inkommande kommunikation från Batch-IP-adresser till poolnoderna. Om det angivna undernätet dock har associerade NSG:er (NSG:er) och/eller en brandvägg konfigurerar du säkerhetsreglerna för inkommande och utgående trafik enligt det som visas i följande tabeller. Om kommunikation till beräkningsnoderna i det angivna undernätet nekas av en NSG, ställer Batch-tjänsten in status för beräkningsnoderna till **oanvändbara**.

Konfigurera inkommande trafik på port 3389 för Windows om du behöver tillåta RDP-åtkomst till noderna i poolen. Det krävs inte för att poolnoderna ska kunna användas.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
Alla <br /><br />Även om detta i princip kräver ”tillåt alla” så tillämpar Batch-tjänsten en ACL-regel på nivån för varje nod som filtrerar ut alla IP-adresser som inte gäller för Batch-tjänsten. | * | Alla | 10100, 20100, 30100 | TCP | Tillåt |
| Valfritt, för att tillåta RDP-åtkomst till Compute-noder. | * | Alla | 3389 | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
| Alla | * | Alla | 443  | Alla | Tillåt |
