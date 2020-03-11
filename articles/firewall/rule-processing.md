---
title: Regelbearbetningslogik för Azure Firewall
description: Azure-brandväggen har NAT-regler, nätverks regler och program regler. Reglerna bearbetas enligt regel typen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 03/10/2020
ms.author: victorh
ms.openlocfilehash: d3f8e52b4582c9467ae3ec61ee984771b801fe4f
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082607"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall
Du kan konfigurera NAT-regler, nätverks regler och program regler på Azure-brandväggen. Reglerna bearbetas enligt regel typen. 

> [!NOTE]
> Om du aktiverar Hot information-baserad filtrering är reglerna högst prioriterade och bearbetas alltid först. Hot-Intelligence-filtrering kan neka trafik innan eventuella konfigurerade regler bearbetas. Mer information finns i [Azure Firewall Threat Intelligence-baserad filtrering](threat-intel.md).

## <a name="outbound"></a>Utgående

### <a name="network-rules-and-applications-rules"></a>Regler för nätverks regler och program

Om du konfigurerar nätverks regler och program regler tillämpas nätverks regler i prioritetsordning före program regler. Reglerna avslutas. Så om en matchning hittas i en nätverks regel bearbetas inga andra regler.  Om det inte finns någon nätverks regel matchning, och om protokollet är HTTP, HTTPS eller MSSQL utvärderas paketet av program reglerna i prioritetsordning. Om ingen matchning hittas utvärderas paketet mot [regel samlingen för infrastrukturen](infrastructure-fqdns.md). Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="inbound"></a>Inkommande

### <a name="nat-rules"></a>NAT-regler

Inkommande Internet anslutning kan aktive ras genom konfiguration av mål nätverks adress översättning (DNAT) enligt beskrivningen i [Självstudier: filtrera inkommande trafik med Azure FIREWALL DNAt med hjälp av Azure Portal](tutorial-firewall-dnat.md). NAT-regler tillämpas i prioritetsordning före nätverks regler. Om en matchning hittas läggs en implicit motsvarande nätverks regel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken.

Program regler tillämpas inte för inkommande anslutningar. Så om du vill filtrera inkommande HTTP/S-trafik bör du använda brand vägg för webbaserade program (WAF). Mer information finns i [Vad är en brand vägg för Azure Web Application?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exempel

I följande exempel visas resultatet av några av de här regel kombinationerna.

### <a name="example-1"></a>Exempel 1

Anslutning till google.com tillåts på grund av en matchande nätverks regel.

**Nätverks regel**

- Åtgärd: Tillåt


|namn  |Protokoll  |Typ av källa  |Källa  |Måltyp  |Mål adress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Tillåt – webb     |TCP|IP-adress|*|IP-adress|*|80,443

**Program regel**

- Åtgärd: neka

|namn  |Typ av källa  |Källa  |Protokoll: port|Mål-FQDN|
|---------|---------|---------|---------|----------|----------|
|Neka – Google     |IP-adress|*|http: 80, https: 443|google.com

**Medför**

Anslutningen till google.com tillåts eftersom paketet matchar regeln *Allow-Web* Network. Regel bearbetningen stoppas nu.

### <a name="example-2"></a>Exempel 2

SSH-trafik nekas eftersom en högre *prioritet blockerar* nätverks regel samlingen.

**Nätverks regel samling 1**

- Namn: Tillåt-samling
- Prioritet: 200
- Åtgärd: Tillåt

|namn  |Protokoll  |Typ av källa  |Källa  |Måltyp  |Mål adress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Tillåt – SSH     |TCP|IP-adress|*|IP-adress|*|22

**Nätverks regel samling 2**

- Namn: neka-samling
- Prioritet: 100
- Åtgärd: neka

|namn  |Protokoll  |Typ av källa  |Källa  |Måltyp  |Mål adress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Neka – SSH     |TCP|IP-adress|*|IP-adress|*|22

**Medför**

SSH-anslutningar nekas eftersom en nätverks regel samling med högre prioritet blockerar den. Regel bearbetningen stoppas nu.

## <a name="rule-changes"></a>Regel ändringar

Om du ändrar en regel för att neka tidigare tillåten trafik, släpps alla relevanta befintliga sessioner.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).