---
title: ta med fil
description: ta med fil
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/04/2020
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: e9460108499ca76d1b149b61cebe3d3081bf6544
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2020
ms.locfileid: "79086278"
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

**Ytterligare nätverksresurser** – Batch tilldelar automatiskt ytterligare nätverksresurser i den resursgrupp som innehåller det virtuella nätverket.

> [!IMPORTANT]
>För varje 50 dedikerade noder (eller varje 20 lågprioritetsnoder) allokerar Batch: en nätverkssäkerhetsgrupp (NSG), en offentlig IP-adress och en lastbalanserare. Dessa resurser begränsas av prenumerationens [resurskvoter](../articles/azure-resource-manager/management/azure-subscription-service-limits.md). För stora pooler kan du behöva begära en kvotökning för en eller flera av dessa resurser.

#### <a name="network-security-groups-batch-default"></a>Nätverkssäkerhetsgrupper: Batchstandard

Undernätet måste tillåta inkommande kommunikation från Batch-tjänsten för att kunna schemalägga uppgifter på beräkningsnoderna samt utgående kommunikation för att kommunicera med Azure Storage eller andra resurser efter behov för din arbetsbelastning. För pooler i konfigurationen för virtuell dator lägger Batch till nätverkssäkerhetsgrupper (NSG:er) på nivån för nätverksgränssnitt (NIC) som är kopplade till beräkningsnoder. Dessa NSG:er konfigureras med följande ytterligare regler:

* Inkommande TCP-trafik på portarna 29876 och 29877 från Batch-tjänstens IP-adresser som motsvarar tjänsttaggen `BatchNodeManagement`.
* Inkommande TCP-trafik på port 22 (Linux-noder) eller port 3389 (Windows-noder) för att tillåta fjärråtkomst. För vissa typer av flerinstansuppgifter på Linux (till exempel MPI) behöver du även tillåta SSH port 22-trafik för IP-adresser i undernätet som innehåller Batch-beräkningsnoder. Detta kan blockeras efter NSG-regler på undernätsnivå (se nedan).
* Utgående trafik på vilken port som helst till det virtuella nätverket. Detta kan ändras efter NSG-regler på undernätsnivå (se nedan).
* Utgående trafik på vilken port som helst till Internet. Detta kan ändras efter NSG-regler på undernätsnivå (se nedan).

> [!IMPORTANT]
> Var försiktig om du ändrar eller lägger till regler för inkommande eller utgående trafik i Batch-konfigurerade NSG:er. Om kommunikation till beräkningsnoderna i det angivna undernätet nekas av en NSG, ställer Batch-tjänsten in status för beräkningsnoderna till **oanvändbara**. Dessutom bör inga resurslås tillämpas på någon resurs som skapats av Batch, eftersom det kan leda till att rensning av resurser förhindras som ett resultat av användarinitierade åtgärder som att ta bort en pool.

#### <a name="network-security-groups-specifying-subnet-level-rules"></a>Nätverkssäkerhetsgrupper: Ange regler på undernätsnivå

Du behöver inte ange NSG:er på undernätverksnivån för virtuellt nätverk eftersom Batch konfigurerar sina egna NSG:er (se ovan). Om du har en NSG associerad med undernätet där Batch-beräkningsnoder distribueras eller vill tillämpa anpassade NSG-regler för att åsidosätta tillämpade standardvärden, måste du sedan konfigurera denna NSG med åtminstone de inkommande och utgående säkerhetsreglerna enligt följande tabell.

Konfigurera inkommande trafik på port 3389 (Windows) eller 22 (Linux) endast om du behöver tillåta fjärråtkomst till beräkningsnoderna från externa källor. Du kan behöva aktivera port 22-regler i Linux om du behöver stöd för flerinstansuppgifter med vissa MPI-körningar. Det är inte absolut nödvändigt att tillåta trafik på dessa portar för att poolberäkningsnoderna ska kunna användas.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Källtjänsttagg | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- | --- |
| Ej tillämpligt | `BatchNodeManagement` [Tjänsttagg](../articles/virtual-network/security-overview.md#service-tags) (om du använder regional variant, i samma region som Batch-kontot) | * | Alla | 29876–29877 | TCP | Tillåt |
| Använd käll-IP-adresser för fjärråtkomst till beräkningsnoder och/eller beräkningsnodundernät för Linux-flerinstansuppgifter, om det behövs. | Ej tillämpligt | * | Alla | 3389 (Windows), 22 (Linux) | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Måltjänsttagg | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- | --- |
| Alla | * | [Tjänsttagg](../articles/virtual-network/security-overview.md#service-tags) | `Storage` (om du använder regional variant, i samma region som Batch-kontot) | 443 | TCP | Tillåt |

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

Konfigurera inkommande trafik på port 3389 för Windows endast om du behöver tillåta RDP-åtkomst till poolnoderna. Det krävs inte för att poolnoderna ska kunna användas.

**Säkerhetsregler för inkommande trafik**

| Käll-IP-adresser | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
Alla <br /><br />Även om detta i princip kräver ”tillåt alla” så tillämpar Batch-tjänsten en ACL-regel på nivån för varje nod som filtrerar ut alla IP-adresser som inte gäller för Batch-tjänsten. | * | Alla | 10100, 20100, 30100 | TCP | Tillåt |
| Valfritt, för att tillåta RDP-åtkomst till beräkningsnoder. | * | Alla | 3389 | TCP | Tillåt |

**Säkerhetsregler för utgående trafik**

| Källa | Källportar | Mål | Målportar | Protokoll | Åtgärd |
| --- | --- | --- | --- | --- | --- |
| Alla | * | Alla | 443  | Alla | Tillåt |
