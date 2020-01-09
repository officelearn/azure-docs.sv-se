---
title: Azure säkerhets kontroll – nätverks säkerhet
description: Säkerhets kontroll nätverks säkerhet
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: d052226470042d374544de0b7e1ced4ca0f48a14
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564158"
---
# <a name="security-control-network-security"></a>Säkerhets kontroll: nätverks säkerhet

Nätverks säkerhets rekommendationer fokusera på att ange vilka nätverks protokoll, TCP/UDP-portar och nätverksanslutna tjänster som tillåts eller nekas åtkomst till Azure-tjänster.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: skydda resurser med hjälp av nätverks säkerhets grupper eller Azure-brandvägg på din Virtual Network

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1-14.3 | Kund |

Se till att alla distributioner av Virtual Network undernät har en nätverks säkerhets grupp som tillämpas med nätverks åtkomst kontroller som är specifika för programmets betrodda portar och källor. Använd Azure-tjänster med privat länk aktiverat, distribuera tjänsten i ditt VNet eller Anslut privat med privata slut punkter. Information om tjänstspecifika krav finns i säkerhets rekommendationer för den aktuella tjänsten.

Om du har ett speciellt användnings fall kan du uppfylla kraven genom att implementera Azure-brandväggen.

Allmän information om privat länk: https://docs.microsoft.com/azure/private-link/private-link-overview

Så här skapar du en Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG med en säkerhets konfiguration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Så här distribuerar och konfigurerar du Azure-brand väggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: övervaka och logga konfigurationen och trafiken för virtuella nätverk, undernät och nätverkskort

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.2 | 9,3, 12,2 | Kund |

Använd Azure Security Center och följ rekommendationerna för nätverks skydd för att skydda dina nätverks resurser i Azure. Aktivera NSG Flow-loggar och skicka loggar till ett lagrings konto för trafik granskning.

Så här aktiverar du NSG Flow-loggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Förstå nätverks säkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: skydda viktiga webb program

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.3 | 9.5 | Kund |

Distribuera Azure Web Application Firewall (WAF) framför viktiga webb program för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställningar för WAF och mata in loggar till ett lagrings konto, en Event Hub-eller Log Analytics-arbetsyta.

Så här distribuerar du Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: neka kommunikation med kända skadliga IP-adresser

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.4 | 12,3 | Kund |

Aktivera DDoS standard skydd på dina virtuella Azure-nätverk för att skydda mot DDoS-attacker. Använd Azure Security Center integrerad Hot information för att neka kommunikation med kända skadliga IP-adresser.

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för att &quot;avisering och neka&quot; för skadlig nätverks trafik.

Använd Azure Security Center just-in-Time Network Access för att konfigurera NSG: er för att begränsa exponering av slut punkter till godkända IP-adresser under en begränsad period.

Använd Azure Security Center anpassad nätverks härdning för att rekommendera NSG-konfigurationer som begränsar portar och käll-IP-adresser baserat på faktisk trafik och hot information.

Så här konfigurerar du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Så här distribuerar du Azure-brand väggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Förstå Azure Security Center integrerad Hot information:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Förstå Azure Security Center anpassad nätverks härdning

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Förstå Azure Security Center just-in-Time-nätverk Access Control

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrera nätverks paket och flödes loggar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.5 | 12,5, 15,8 | Kund |

Registrera NSG Flow-loggar i ett lagrings konto för att generera flödes poster. Om det behövs för att undersöka avvikande aktivitet aktiverar du Network Watcher paket fångst.

Så här aktiverar du NSG Flow-loggar: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Så här aktiverar du Network Watcher: https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: Distribuera Network-baserad intrångs identifiering/intrångs skydd system (ID/IP-adresser)

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.6 | 12,6, 12,7 | Kund |

Distribuera Azure-brandväggen på var och en av organisationens nätverks gränser med hot information aktive rad och konfigurerad för att &quot;avisering och neka&quot; för skadlig nätverks trafik.

Så här distribuerar du Azure-brand väggen: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurera aviseringar med Azure Firewall: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: hantera trafik till webb program

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.7 | 12,9, 12,10 | Kund |

Distribuera Azure Application Gateway för webb program med HTTPS/SSL aktiverat för betrodda certifikat.

Så här distribuerar du Application Gateway: https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Så här konfigurerar du Application Gateway att använda HTTPS: https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Förstå belastnings utjämning för Layer 7 med Azure Web Application Gateway: https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: minimera komplexitet och administrativa kostnader för nätverks säkerhets regler

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.8 | 1.5 | Kund |

Använd Virtual Network Service tag-&nbsp;för att definiera nätverks åtkomst kontroller i nätverks säkerhets grupper eller Azure-brandvägg. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange service tag-namnet (t. ex. API Management) i lämpligt käll-eller mål fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras.

Förstå och använda service märken: https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: underhåll standardkonfigurationer för nätverks enheter

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.9 | 11,1 | Kund |

Definiera och implementera standardinställda säkerhetskonfigurationer för nätverks resurser med Azure Policy.

Du kan också använda Azure-ritningar för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljö artefakter, till exempel Azure Resource Manager mallar, RBAC-kontroller och principer, i en enda skiss definition. Du kan använda skissen för nya prenumerationer och miljöer och finjustera kontroll och hantering genom versions hantering.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exempel för nätverk:

https://docs.microsoft.com/azure/governance/policy/samples/#network

Så här skapar du en Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: dokumentera trafik konfigurations regler

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1.1 | 11,2 | Kund |

Använd taggar för NSG: er och andra resurser som är relaterade till nätverks säkerhets-och trafikflödet. För enskilda NSG-regler använder du fältet &quot;Beskrivning&quot; för att ange affärs behov och/eller varaktighet (osv.) för alla regler som tillåter trafik till/från ett nätverk.

Skapa och använda Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du en Virtual Network:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG med en säkerhets konfiguration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: Använd automatiserade verktyg för att övervaka konfigurationer för nätverks resurser och identifiera ändringar

| Azure-ID | CIS-ID: n | Ansvar |
|--|--|--|
| 1,11 | 11,3 | Kund |

Använd Azure Policy för att validera (och/eller åtgärda) konfigurationen för nätverks resurser.

Så här konfigurerar och hanterar du Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy exempel för nätverk:

https://docs.microsoft.com/azure/governance/policy/samples/#network

## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhets kontroll: [loggning och övervakning](security-control-logging-monitoring.md)