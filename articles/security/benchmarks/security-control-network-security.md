---
title: Azure Security Control - Nätverkssäkerhet
description: Säkerhetskontroll för säkerhetskontroll
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251880"
---
# <a name="security-control-network-security"></a>Säkerhetskontroll: Nätverkssäkerhet

Nätverkssäkerhetsrekommendationer fokuserar på att ange vilka nätverksprotokoll, TCP/UDP-portar och nätverksanslutna tjänster som tillåts eller nekas åtkomst till Azure-tjänster.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Skydda resurser med nätverkssäkerhetsgrupper eller Azure-brandväggen i det virtuella nätverket

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Kund |

Kontrollera att alla virtuella nätverksundernätsdistributioner har en nätverkssäkerhetsgrupp som tillämpas med nätverksåtkomstkontroller som är specifika för programmets betrodda portar och källor. Använd Azure Services med privat länk aktiverad, distribuera tjänsten i ditt virtuella nätverk eller anslut privat med privata slutpunkter. För servicespecifika krav, se säkerhetsrekommendationen för den specifika tjänsten.

Alternativt, om du har ett specifikt användningsfall, kan kraven uppfyllas genom att implementera Azure-brandväggen.

Allmän information om privat länk:

https://docs.microsoft.com/azure/private-link/private-link-overview

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Så här skapar du en NSG med en säkerhetskonfiguration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Så här distribuerar och konfigurerar du Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.2 | 9.3, 12.2 | Kund |

Använd Azure Security Center och följ rekommendationer för nätverksskydd för att skydda dina nätverksresurser i Azure. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning.

Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.3 | 9.5 | Kund |

Distribuera WAF (Azure Web Application Firewall) framför kritiska webbprogram för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställning för WAF och inta loggar till ett lagringskonto, händelsehubb eller logganalysarbetsyta.

Distribuerar Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.4 | 12.3 | Kund |

Aktivera DDoS Standard-skydd på dina virtuella Azure-nätverk för att skydda mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga IP-adresser.

Distribuera Azure-brandväggen vid var och en av organisationens nätverksgränser &quot;med Threat&quot; Intelligence aktiverat och konfigurerat för att varna och neka skadlig nätverkstrafik.

Använd Azure Security Center Just In Time Network access för att konfigurera NSG:er för att begränsa exponeringen av slutpunkter till godkända IP-adresser under en begränsad period.

Använd Azure Security Center Adaptive Network Hardening för att rekommendera NSG-konfigurationer som begränsar portar och käll-IPs baserat på faktisk trafik- och hotinformation.

KonfigureraR du DDoS-skydd:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Distribuerar Azure-brandväggen:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Förstå Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Förstå Azure Security Center Adaptive Network Hardening:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Förstå Azure Security Center precis i tid nätverksåtkomstkontroll:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Spela in nätverkspaket och flödesloggar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.5 | 12.5, 15.8 | Kund |

Registrera NSG-flödesloggar till ett lagringskonto för att generera flödesposter. Om det behövs för att undersöka avvikande aktivitet aktiverar du insamling av nätverksbevakningspaket.

Aktivera NSG-flödesloggar:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Aktivera Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.6 | 12.6, 12.7 | Kund |

Distribuera Azure-brandväggen vid var och en av organisationens nätverksgränser &quot;med Threat&quot; Intelligence aktiverat och konfigurerat för att varna och neka skadlig nätverkstrafik.

Distribuerar Azure-brandväggen:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Konfigurera aviseringar med Azure-brandväggen:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.7 | 12.9, 12.10 | Kund |

Distribuera Azure Application Gateway för webbprogram med HTTPS/SSL aktiverat för betrodda certifikat.

Så här distribuerar du Application Gateway:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Konfigurera programgateway för att använda HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Förstå belastningsutjämning på lager 7 med Gateways för Azure-webbprogram:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.8 | 1.5 | Kund |

Använd taggar för virtual network service för att definiera nätverksåtkomstkontroller i nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Förstå och använd tjänsttaggar:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.9 | 11.1 | Kund |

Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser med Azure Policy.

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resource Manager-mallar, RBAC-kontroller och principer, i en enda skissdefinition. Du kan använda skissen på nya prenumerationer och finjustera kontroll och hantering via versionshantering.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Princip-exempel för nätverk:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Så här skapar du en Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.1 | 11.2 | Kund |

Använd taggar för NSG:er och andra resurser som är relaterade till nätverkssäkerhet och trafikflöde. För enskilda NSG-regler &quot;&quot; använder du fältet Beskrivning för att ange affärsbehov och/eller varaktighet (etc.) för alla regler som tillåter trafik till/från ett nätverk.

Så här skapar och använder du Taggar:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Så här skapar du ett virtuellt nätverk:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hur man skapar en NSG med en Security Config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.11 | 11.3 | Kund |

Använd Azure Policy för att validera (och/eller åtgärda) konfiguration för nätverksresurser.

Konfigurera och hantera Azure-princip:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Princip-exempel för nätverk:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Loggning och övervakning](security-control-logging-monitoring.md)
