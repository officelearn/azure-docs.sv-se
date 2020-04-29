---
title: Regelbearbetningslogik för Azure Firewall
description: Azure-brandväggen har NAT-regler, nätverks regler och program regler. Reglerna bearbetas enligt regel typen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113439"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall
Du kan konfigurera NAT-regler, nätverks regler och program regler på Azure-brandväggen. Regel samlingar bearbetas enligt regel typen i prioritetsordning, lägre siffror till högre tal från 100 till 65 000. Ett namn på en regel samling får bara innehålla bokstäver, siffror, under streck, punkter eller bindestreck. Det måste börja med en bokstav eller en siffra, och sluta med en bokstav, en siffra eller ett under streck. Den maximala namn längden är 80 tecken.

Det är bäst att först få plats med prioritets numren för regel samlingen i 100-steg (100, 200, 300 osv.), så att du har plats att lägga till fler regel samlingar om det behövs.

> [!NOTE]
> Om du aktiverar Hot information-baserad filtrering är reglerna högst prioriterade och bearbetas alltid först. Hot-Intelligence-filtrering kan neka trafik innan eventuella konfigurerade regler bearbetas. Mer information finns i [Azure Firewall Threat Intelligence-baserad filtrering](threat-intel.md).

## <a name="outbound-connectivity"></a>Utgående anslutning

### <a name="network-rules-and-applications-rules"></a>Regler för nätverks regler och program

Om du konfigurerar nätverks regler och program regler tillämpas nätverks regler i prioritetsordning före program regler. Reglerna avslutas. Så om en matchning hittas i en nätverks regel bearbetas inga andra regler.  Om det inte finns någon nätverks regel matchning, och om protokollet är HTTP, HTTPS eller MSSQL utvärderas paketet av program reglerna i prioritetsordning. Om ingen matchning hittas utvärderas paketet mot [regel samlingen för infrastrukturen](infrastructure-fqdns.md). Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="inbound-connectivity"></a>Inkommande anslutning

### <a name="nat-rules"></a>NAT-regler

Inkommande Internet anslutning kan aktive ras genom konfiguration av mål nätverks adress översättning (DNAT) enligt beskrivningen i [Självstudier: filtrera inkommande trafik med Azure FIREWALL DNAt med hjälp av Azure Portal](tutorial-firewall-dnat.md). NAT-regler tillämpas i prioritetsordning före nätverks regler. Om en matchning hittas läggs en implicit motsvarande nätverks regel för att tillåta den översatta trafiken. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken.

Program regler tillämpas inte för inkommande anslutningar. Så om du vill filtrera inkommande HTTP/S-trafik bör du använda brand vägg för webbaserade program (WAF). Mer information finns i [Vad är en brand vägg för Azure Web Application?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exempel

I följande exempel visas resultatet av några av de här regel kombinationerna.

### <a name="example-1"></a>Exempel 1

Anslutning till google.com tillåts på grund av en matchande nätverks regel.

**Nätverks regel**

- Åtgärd: Tillåt


|name  |Protokoll  |Källtyp  |Källa  |Måltyp  |Mål adress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Tillåt – webb     |TCP|IP-adress|*|IP-adress|*|80 443

**Program regel**

- Åtgärd: neka

|name  |Källtyp  |Källa  |Protokoll: port|Mål-FQDN|
|---------|---------|---------|---------|----------|----------|
|Neka – Google     |IP-adress|*|http: 80, https: 443|google.com

**Resultat**

Anslutningen till google.com tillåts eftersom paketet matchar regeln *Allow-Web* Network. Regel bearbetningen stoppas nu.

### <a name="example-2"></a>Exempel 2

SSH-trafik nekas eftersom en högre *prioritet blockerar* nätverks regel samlingen.

**Nätverks regel samling 1**

- Namn: Tillåt-samling
- Prioritet: 200
- Åtgärd: Tillåt

|name  |Protokoll  |Källtyp  |Källa  |Måltyp  |Mål adress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Tillåt – SSH     |TCP|IP-adress|*|IP-adress|*|22

**Nätverks regel samling 2**

- Namn: neka-samling
- Prioritet: 100
- Åtgärd: neka

|name  |Protokoll  |Källtyp  |Källa  |Måltyp  |Mål adress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Neka – SSH     |TCP|IP-adress|*|IP-adress|*|22

**Resultat**

SSH-anslutningar nekas eftersom en nätverks regel samling med högre prioritet blockerar den. Regel bearbetningen stoppas nu.

## <a name="rule-changes"></a>Regel ändringar

Om du ändrar en regel för att neka tidigare tillåten trafik, släpps alla relevanta befintliga sessioner.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).