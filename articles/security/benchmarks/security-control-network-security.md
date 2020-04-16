---
title: Azure Security Control - Nätverkssäkerhet
description: Azure Security Control Nätverkssäkerhet
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408306"
---
# <a name="security-control-network-security"></a>Säkerhetskontroll: Nätverkssäkerhet

Nätverkssäkerhetsrekommendationer fokuserar på att ange vilka nätverksprotokoll, TCP/UDP-portar och nätverksanslutna tjänster som tillåts eller nekas åtkomst till Azure-tjänster.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Skydda Azure-resurser i virtuella nätverk

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Kund |

Kontrollera att alla virtuella nätverksundernätsdistributioner har en nätverkssäkerhetsgrupp som tillämpas med nätverksåtkomstkontroller som är specifika för programmets betrodda portar och källor. När det är tillgängligt använder du privata slutpunkter med privat länk för att skydda dina Azure-tjänstresurser till ditt virtuella nätverk genom att utöka VNet-identiteten till tjänsten. När privata slutpunkter och privat länk inte är tillgängliga använder du Tjänstslutpunkter. För servicespecifika krav, se säkerhetsrekommendationen för den specifika tjänsten. 

Alternativt, om du har ett specifikt användningsfall, kan kravet uppfyllas genom att implementera Azure-brandväggen.

- [Förstå slutpunkter för virtual network service](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Förstå Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Så här skapar du ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Så här skapar du en NSG med en säkerhetskonfiguration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Distribuera och konfigurera Azure-brandväggen](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Övervaka och logga konfiguration och trafik av virtuella nätverk, undernät och nätverkskort

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Kund |

Använd Azure Security Center och följ rekommendationer för nätverksskydd för att skydda dina nätverksresurser i Azure. Aktivera NSG-flödesloggar och skicka loggar till ett lagringskonto för trafikgranskning. Du kan också skicka NSG-flödesloggar till en Log Analytics Workspace och använda Traffic Analytics för att ge insikter om trafikflödet i ditt Azure-moln. Vissa fördelar med Traffic Analytics är möjligheten att visualisera nätverksaktivitet och identifiera aktiva punkter, identifiera säkerhetshot, förstå trafikflödesmönster och identifiera felkonfigurationer i nätverket.

- [Aktivera NSG-flödesloggar](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Aktivera och använda Traffic Analytics](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Förstå nätverkssäkerhet som tillhandahålls av Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Skydda kritiska webbprogram

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.3 | 9.5 | Kund |

Distribuera WAF (Azure Web Application Firewall) framför kritiska webbprogram för ytterligare inspektion av inkommande trafik. Aktivera diagnostikinställning för WAF och inta loggar till ett lagringskonto, händelsehubb eller logganalysarbetsyta.

- [Distribuera Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Neka kommunikation med kända skadliga IP-adresser

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.4 | 12.3 | Kund |

Aktivera DDoS Standard-skydd på dina virtuella Azure-nätverk för att skydda mot DDoS-attacker. Använd Azure Security Center Integrated Threat Intelligence för att neka kommunikation med kända skadliga IP-adresser.

Distribuera Azure-brandväggen vid var och en av organisationens nätverksgränser med Threat Intelligence aktiverat och konfigurerat för att "Varna och neka" för skadlig nätverkstrafik.

Använd Azure Security Center Just In Time Network access för att konfigurera NSG:er för att begränsa exponeringen av slutpunkter till godkända IP-adresser under en begränsad period.

Använd Azure Security Center Adaptive Network Hardening för att rekommendera NSG-konfigurationer som begränsar portar och käll-IPs baserat på faktisk trafik- och hotinformation.

- [Konfigurera DDoS-skydd](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Distribuera Azure-brandväggen](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Förstå Azure Security Center Integrated Threat Intelligence](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Förstå Azure Security Center Adaptive Network Hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Förstå Azure Security Center precis i tid nätverksåtkomstkontroll](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Spela in nätverkspaket

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.5 | 12.5 | Kund |

Aktivera Network Watcher-paketinsamling för att undersöka avvikande aktiviteter.

- [Aktivera Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuera nätverksbaserade system för intrångsdetektering/intrångsförebyggande (IDS/IPS)

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.6 | 12.6, 12.7 | Kund |

Välj ett erbjudande från Azure Marketplace som stöder IDS/IPS-funktioner med funktioner för nyttolastinspektion.  Om intrångsidentifiering och/eller förebyggande baserat på nyttolastinspektion inte är ett krav kan Azure Firewall with Threat Intelligence användas. Azure Firewall Threat Intelligence-baserad filtrering kan varna och neka trafik till och från kända skadliga IP-adresser och domäner. IP-adresser och domäner kommer från Microsoft Threat Intelligence-feeden.

Distribuera den brandväggslösning som du väljer vid var och en av organisationens nätverksgränser för att identifiera och/eller neka skadlig trafik.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Distribuera Azure-brandväggen](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Konfigurera aviseringar med Azure-brandväggen](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Hantera trafik till webbapplikationer

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.7 | 12.9, 12.10 | Kund |

Distribuera Azure Application Gateway för webbprogram med HTTPS/SSL aktiverat för betrodda certifikat.

- [Distribuera programgateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Konfigurera programgateway för att använda HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Förstå belastningsutjämning för lager 7 med Azure-gateways för webbprogram](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimera komplexiteten och de administrativa omkostnaderna för nätverkssäkerhetsreglerna

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.8 | 1.5 | Kund |

Använd taggar för virtual network service för att definiera nätverksåtkomstkontroller i nätverkssäkerhetsgrupper eller Azure-brandväggen. Du kan använda tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (t.ex. ApiManagement) i lämpligt käll- eller målfält för en regel kan du tillåta eller neka trafik för motsvarande tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras.

Du kan också använda programsäkerhetsgrupper för att förenkla komplex säkerhetskonfiguration. Med programsäkerhetsgrupper kan du konfigurera nätverkssäkerhet som ett naturligt tillägg till ett programs struktur, så att du kan gruppera virtuella datorer och definiera nätverkssäkerhetsprinciper baserat på dessa grupper.

- [Förstå och använda tjänsttaggar](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Förstå och använda programsäkerhetsgrupper](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Underhåll standardsäkerhetskonfigurationer för nätverksenheter

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.9 | 11.1 | Kund |

Definiera och implementera standardsäkerhetskonfigurationer för nätverksresurser med Azure Policy.

Du kan också använda Azure Blueprints för att förenkla storskaliga Azure-distributioner genom att paketera viktiga miljöartefakter, till exempel Azure Resources Manager-mallar, RBAC-kontroller och principer, i en enda skissdefinition. Du kan använda skissen på nya prenumerationer och finjustera kontroll och hantering via versionshantering.

- [Konfigurera och hantera Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Azure Princip-exempel för nätverk](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Så här skapar du en Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regler för konfiguration av dokumenttrafik

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1,10 | 11.2 | Kund |

Använd taggar för NSG:er och andra resurser som är relaterade till nätverkssäkerhet och trafikflöde. För enskilda NSG-regler använder du fältet "Beskrivning" för att ange affärsbehov och/eller varaktighet (etc.) för alla regler som tillåter trafik till/från ett nätverk.

Använd någon av de inbyggda Azure-principdefinitionerna som är relaterade till taggning, till exempel "Kräv tagg och dess värde" för att säkerställa att alla resurser skapas med taggar och för att meddela dig om befintliga otaggade resurser.

Du kan använda Azure PowerShell eller Azure CLI för att slå upp eller utföra åtgärder på resurser baserat på deras taggar.

- [Så här skapar och använder du taggar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Så här skapar du ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Hur man skapar en NSG med en säkerhets config](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Använd automatiserade verktyg för att övervaka nätverksresurskonfigurationer och identifiera ändringar

| Azure-ID | CIS-ID:er | Ansvar |
|--|--|--|
| 1.11 | 11.3 | Kund |

Använd Azure Activity Log för att övervaka resurskonfigurationer och identifiera ändringar i dina Azure-resurser. Skapa aviseringar i Azure Monitor som utlöses när ändringar av kritiska resurser sker.

- [Så här visar och hämtar du Azure Activity Log-händelser](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Så här skapar du aviseringar i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Nästa steg

- Se nästa säkerhetskontroll: [Loggning och övervakning](security-control-logging-monitoring.md)
