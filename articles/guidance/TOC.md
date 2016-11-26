# Arkitektur

## Grunderna för molnet

### [Skapa elastiska program](guidance-resiliency-overview.md)
#### [Checklista för återhämtning](guidance-resiliency-checklist.md)
#### [Analys av felläge](guidance-resiliency-failure-mode-analysis.md)
#### [Haveriberedskap](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Haveriberedskap och hög tillgänglighet](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Hög tillgänglighet](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Checklista för hög tillgänglighet](..\resiliency\resiliency-high-availability-checklist.md)
#### [Azures tjänstspecifika återhämtningsvägledning](..\resiliency\resiliency-service-guidance-index.md)
#### [Återställning av oavsiktligt borttagna data](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Återställning från lokala fel](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Återställning från regionomfattande tjänststörningar](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Skicka jobb från lokal plats till Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure-återhämtning, tekniska riktlinjer](..\resiliency\resiliency-technical-guidance.md)


## Referensarkitekturer

### [Kör VM-arbetsbelastningar i Azure](guidance-ra-compute.md)
#### [Kör en Linux-VM på Azure](guidance-compute-single-vm-linux.md)
#### [Kör en Windows-VM på Azure](guidance-compute-single-vm.md)
#### [Kör flera VM:ar på Azure för skalbarhet och tillgänglighet](guidance-compute-multi-vm.md)
#### [Kör Linux-VM:ar för en N-tier-arkitektur](guidance-compute-n-tier-vm-linux.md)
#### [Kör Windows-VM:ar för en N-tier-arkitektur](guidance-compute-n-tier-vm.md)
#### [Kör Linux-VM:ar i flera regioner för hög tillgänglighet](guidance-compute-multiple-datacenters-linux.md)
#### [Kör Windows-VM:ar i flera regioner för hög tillgänglighet](guidance-compute-multiple-datacenters.md)

### [Anslut ditt lokala nätverk till Azure](guidance-ra-hybrid-networking.md)
#### [Hybridnätverksarkitektur med Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Hybridnätverksarkitektur med Azure och lokal VPN](guidance-hybrid-network-vpn.md)
#### [Hybridnätverksarkitektur med hög tillgänglighet](guidance-hybrid-network-expressroute-vpn-failover.md)

### [Skydda molngränsen i Azure](guidance-ra-network-security.md)
#### [Säkra hybridnätverksarkitekturen i Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [DMZ mellan Azure och Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Hantera identitet i Azure](guidance-ra-identity.md)
#### [Implementera Azure Active Directory](guidance-identity-aad.md)
#### [Utöka ADDS till Azure](guidance-identity-adds-extend-domain.md)
#### [Skapa en ADDS-resursskog i Azure](guidance-identity-adds-resource-forest.md)
#### [Implementera ADFS i Azure](guidance-identity-adfs.md)

### [Webbprogramsarkitekturer för Azure App Service](guidance-ra-app-service.md)
#### [Grundläggande webbapp](guidance-web-apps-basic.md)
#### [Webbapp med hög tillgänglighet](guidance-web-apps-multi-region.md)
#### [Förbättrad skalbarhet i ett webbprogram](guidance-web-apps-scalability.md)


## Bästa praxis för molnprogram

### [Vägledning för API-design](..\best-practices-api-design.md)
### [Vägledning för API-implementering](..\best-practices-api-implementation.md)
### [Vägledning om autoskalning](..\best-practices-auto-scaling.md)
### [Tillgänglighetschecklista](..\best-practices-availability-checklist.md)
### [Vägledning för bakgrundsjobb](..\best-practices-background-jobs.md)
### [Azure-länkade regioner](..\best-practices-availability-paired-regions.md)
### [Vägledning om cachelagring](..\best-practices-caching.md)
### [Vägledning för CDN (Content Delivery Network)](..\best-practices-cdn.md)
### [Vägledning om datapartitionering](..\best-practices-data-partitioning.md)
### [Vägledning om övervakning och diagnostik](..\best-practices-monitoring.md)
### [Microsofts molntjänster och nätverkssäkerhet](..\best-practices-network-security.md)
### [Mönster för att skapa Azure Resource Manager-mallar](..\best-practices-resource-manager-design-templates.md)
### [Rekommenderade namnkonventioner för Azure-resurser](guidance-naming-conventions.md)
### [Säkerhetsöverväganden för Azure Resource Manager](..\best-practices-resource-manager-security.md)
### [Dela tillstånd i Azure Resource Manager-mallar](..\best-practices-resource-manager-state.md)
### [Allmänna riktlinjer för nytt försök](..\best-practices-retry-general.md)
### [Specifik vägledning för att testa tjänsten igen](..\best-practices-retry-service-specific.md)
### [Skalbarhetschecklista](..\best-practices-scalability-checklist.md)


## Scenarioguider

### [Elasticsearch i Azure, vägledning](guidance-elasticsearch.md)
#### [Kör Elasticsearch på Azure](guidance-elasticsearch-running-on-azure.md)
#### [Justera prestanda för datapåfyllning](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Justera datasammanställnings- och frågeprestanda](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Konfigurera återhämtning och återställning](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Skapa en prestandatestmiljö](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementera en JMeter-testplan](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Distribuera en JMeter JUnit-provtagare](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Kör de automatiserade återhämtningstesterna](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Kör de automatiserade prestandatesterna](guidance-elasticsearch-running-automated-performance-tests.md)

### [Identitetshantering för program med flera klienter](guidance-multitenant-identity.md)
#### [Översikt](guidance-multitenant-identity-intro.md)
#### [Tailspin Surveys-programmet](guidance-multitenant-identity-tailspin.md)
#### [Autentisering med Azure AD och OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Anspråksbaserade identiteter](guidance-multitenant-identity-claims.md)
#### [Registrering och klient-integration](guidance-multitenant-identity-signup.md)
#### [Programroller](guidance-multitenant-identity-app-roles.md)
#### [Rollbaserad och resursbaserad auktorisering](guidance-multitenant-identity-authorize.md)
#### [Skydda ett serverdels-webb-API](guidance-multitenant-identity-web-api.md)
#### [Cacheåtkomsttokens](guidance-multitenant-identity-token-cache.md)
#### [Federation med en kunds AD FS](guidance-multitenant-identity-adfs.md)
#### [Använd klientkontroll för att få åtkomsttokens](guidance-multitenant-identity-client-assertion.md)
#### [Använd Azure Key Vault för att skydda programhemligheter](guidance-multitenant-identity-keyvault.md)
#### [Distribuera virtuella enheter med hög tillgänglighet](guidance-nva-ha.md)


<!--HONumber=Nov16_HO4-->


