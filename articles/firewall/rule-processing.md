---
title: Regelbearbetningslogik för Azure Firewall
description: Azure-brandväggen har NAT-regler, nätverksregler och programregler. Reglerna bearbetas enligt regeltypen.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 04/10/2020
ms.author: victorh
ms.openlocfilehash: 93677b3e473ab825665fed5590ac345a8cfcc300
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113439"
---
# <a name="azure-firewall-rule-processing-logic"></a>Regelbearbetningslogik för Azure Firewall
Du kan konfigurera NAT-regler, nätverksregler och programregler på Azure-brandväggen. Regelsamlingar bearbetas enligt regeltypen i prioritetsordning, lägre siffror till högre tal från 100 till 65 000. Ett regelsamlingsnamn kan bara ha bokstäver, siffror, understreck, punkter eller bindestreck. Det måste börja med en bokstav eller siffra, och sluta med en bokstav, siffra eller understreck. Den maximala namnlängden är 80 tecken.

Det är bäst att inledningsvis utrymme din regel samling prioritetsnummer i 100 steg (100, 200, 300 och så vidare) så att du har utrymme för att lägga till fler regelsamlingar om det behövs.

> [!NOTE]
> Om du aktiverar filtrering baserad på hotinformation har dessa regler högsta prioritet och bearbetas alltid först. Hot-intelligens filtrering kan neka trafik innan några konfigurerade regler bearbetas. Mer information finns i [Azure Firewall threat intelligence-baserad filtrering](threat-intel.md).

## <a name="outbound-connectivity"></a>Utgående anslutning

### <a name="network-rules-and-applications-rules"></a>Regler för nätverk och program

Om du konfigurerar nätverksregler och programregler tillämpas nätverksregler i prioritetsordning före programregler. Reglerna är avslutande. Så om en matchning hittas i en nätverksregel bearbetas inga andra regler.  Om det inte finns någon nätverksregelmatchning, och om protokollet är HTTP, HTTPS eller MSSQL, utvärderas paketet sedan av programreglerna i prioritetsordning. Om det fortfarande inte finns någon matchning utvärderas paketet mot [infrastrukturregelsamlingen](infrastructure-fqdns.md). Om det fortfarande inte finns någon matchning nekas paketet som standard.

## <a name="inbound-connectivity"></a>Inkommande anslutning

### <a name="nat-rules"></a>NAT-regler

Inkommande Internet-anslutning kan aktiveras genom att konfigurera DNAT (Destination Network Address Translation) enligt beskrivningen i [Självstudiekurs: Filtrera inkommande trafik med Azure Firewall DNAT med Azure-portalen](tutorial-firewall-dnat.md). NAT-regler tillämpas i prioritet före nätverksregler. Om en matchning hittas läggs en implicit motsvarande nätverksregel för att tillåta den översatta trafiken till. Du kan åsidosätta det här beteendet genom att uttryckligen lägga till en nätverksregelsamling med neka-regler som matchar den översatta trafiken.

Programregler tillämpas inte för inkommande anslutningar. Så om du vill filtrera inkommande HTTP / S-trafik, bör du använda Web Application Firewall (WAF). Mer information finns i [Vad är Azure Web Application Firewall?](../web-application-firewall/overview.md)

## <a name="examples"></a>Exempel

Följande exempel visar resultatet av några av dessa regelkombinationer.

### <a name="example-1"></a>Exempel 1

Anslutning till google.com tillåts på grund av en matchande nätverksregel.

**Nätverksregel**

- Åtgärd: Tillåt


|namn  |Protokoll  |Källtyp  |Källa  |Måltyp  |Måladress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Tillåt-webb     |TCP|IP-adress|*|IP-adress|*|80 443

**Programregel**

- Åtgärd: Neka

|namn  |Källtyp  |Källa  |Protokoll:Port|Mål-FQDN:er|
|---------|---------|---------|---------|----------|----------|
|Neka-google     |IP-adress|*|http:80,https:443|google.com

**Resultat**

Anslutningen till google.com tillåts eftersom paketet matchar regeln *Tillåt-webbnätverk.* Regelbearbetningen stoppas vid denna punkt.

### <a name="example-2"></a>Exempel 2

SSH-trafik nekas eftersom en högre prioritet *Neka* nätverksregelsamling blockerar den.

**Insamling av nätverksregel 1**

- Namn: Allow-collection
- Prioritet: 200
- Åtgärd: Tillåt

|namn  |Protokoll  |Källtyp  |Källa  |Måltyp  |Måladress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Tillåt-SSH     |TCP|IP-adress|*|IP-adress|*|22

**Insamling av nätverksregel 2**

- Namn: Deny-collection
- Prioritet: 100
- Åtgärd: Neka

|namn  |Protokoll  |Källtyp  |Källa  |Måltyp  |Måladress  |Målportar|
|---------|---------|---------|---------|----------|----------|--------|
|Neka-SSH     |TCP|IP-adress|*|IP-adress|*|22

**Resultat**

SSH-anslutningar nekas eftersom en nätverksregelsamling med högre prioritet blockerar den. Regelbearbetningen stoppas vid denna punkt.

## <a name="rule-changes"></a>Regeländringar

Om du ändrar en regel för att neka tidigare tillåten trafik tas alla relevanta befintliga sessioner bort.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [distribuerar och konfigurerar en Azure-brandvägg](tutorial-firewall-deploy-portal.md).