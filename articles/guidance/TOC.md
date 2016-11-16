# Arkitektur

## Grunderna för molnet

### [Utforma återhämtningsprogram för Azure](guidance-resiliency-overview.md)
#### [Checklista för återhämtning](guidance-resiliency-checklist.md)
#### [Analys av felläge](guidance-resiliency-failure-mode-analysis.md)

#### [Haveriberedskap för program som bygger på Microsoft Azure](..\resiliency\resiliency-disaster-recovery-azure-applications.md)
#### [Haveriberedskap och hög tillgänglighet för program som bygger på Microsoft Azure](..\resiliency\resiliency-disaster-recovery-high-availability-azure-applications.md)
#### [Haveriberedskap för program som bygger på Microsoft Azure](..\resiliency\resiliency-high-availability-azure-applications.md)
#### [Checklista för hög tillgänglighet](..\resiliency\resiliency-high-availability-checklist.md)
#### [Azures tjänstspecifika återhämtningsvägledning](..\resiliency\resiliency-service-guidance-index.md)
#### [Återställning av oavsiktligt borttagna data](..\resiliency\resiliency-technical-guidance-recovery-data-corruption.md)
#### [Återställning från lokala fel i Azure](..\resiliency\resiliency-technical-guidance-recovery-local-failures.md)
#### [Återställning från regionomfattande tjänststörningar](..\resiliency\resiliency-technical-guidance-recovery-loss-azure-region.md)
#### [Skicka jobb från lokal plats till Azure](..\resiliency\resiliency-technical-guidance-recovery-on-premises-azure.md)
#### [Azure-återhämtning, tekniska riktlinjer](..\resiliency\resiliency-technical-guidance.md)


## Referensarkitekturer

### Referensarkitektur för Compute
#### [Köra en virtuell Linux-dator på Azure](guidance-compute-single-vm-linux.md)
#### [Köra en virtuell Windows-dator på Azure](guidance-compute-single-vm.md)
#### [Köra flera virtuella datorer på Azure för skalbarhet och tillgänglighet](guidance-compute-multi-vm.md)
#### [Köra virtuella Linux-datorer för en N-tier-arkitektur på Azure](guidance-compute-n-tier-vm-linux.md)
#### [Köra virtuella Windows-datorer för en N-tier-arkitektur på Azure](guidance-compute-n-tier-vm.md)
#### [Köra virtuella Linux-datorer i flera områden för hög tillgänglighet](guidance-compute-multiple-datacenters-linux.md)
#### [Köra virtuella Windows-datorer i flera områden för hög tillgänglighet](guidance-compute-multiple-datacenters.md)

### [Anslut ditt lokala nätverk till Azure](guidance-connecting-your-on-premises-network-to-azure.md)
#### [Implementera en hybridnätverksarkitektur med Azure ExpressRoute](guidance-hybrid-network-expressroute.md)
#### [Implementera en hybridnätverksarkitektur med Azure och lokal VPN](guidance-hybrid-network-vpn.md)
#### [Implementera en hybridnätverksarkitektur med hög tillgänglighet](guidance-hybrid-network-expressroute-vpn-failover.md)

### Skydda molngränsen i Azure
#### [Implementera en säker hybridnätverksarkitektur i Azure](guidance-iaas-ra-secure-vnet-hybrid.md)
#### [Implementera en DMZ mellan Azure och Internet](guidance-iaas-ra-secure-vnet-dmz.md)

### [Hantera identitet i Azure](guidance-ra-identity.md)
#### [Implementera Azure Active Directory](guidance-identity-aad.md)
#### [Utöka Active Directory Domain Services (ADDS) till Azure](guidance-identity-adds-extend-domain.md)
#### [Skapa en Active Directory Directory Services (ADDS) resursskog i Azure](guidance-identity-adds-resource-forest.md)
#### [Implementera Active Directory Federation Services (ADFS) i Azure](guidance-identity-adfs.md)

### Arkitektur för PaaS-webbappreferens
#### [Referensarkitektur för Azure: Grundläggande webbapp](guidance-web-apps-basic.md)
#### [Referensarkitektur för Azure: Webbapp med hög tillgänglighet](guidance-web-apps-multi-region.md)
#### [Förbättrad skalbarhet i en webbapp](guidance-web-apps-scalability.md)


## Cloud Design-mönster

## Bästa praxis för molnprogram

### [Vägledning för API-design](..\best-practices-api-design.md)
### [Vägledning för API-implementering](..\best-practices-api-implementation.md)
### [Vägledning om autoskalning](..\best-practices-auto-scaling.md)
### [Tillgänglighetschecklista](..\best-practices-availability-checklist.md)
### [Vägledning för bakgrundsjobb](..\best-practices-background-jobs.md)
### [Business Continuity and Disaster Recovery (BCDR): Azure-kopplade områden](..\best-practices-availability-paired-regions.md)
### [Vägledning om cachelagring](..\best-practices-caching.md)
### [Vägledning om CDN (Content Delivery Network)](..\best-practices-cdn.md)
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
#### [Kör Elasticsearchi Azure](guidance-elasticsearch-running-on-azure.md)
#### [Justera datainmatningens prestanda för Elasticsearch på Azure](guidance-elasticsearch-tuning-data-ingestion-performance.md)
#### [Justera dataaggregering och frågeprestanda med Elasticsearch på Azure](guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md)
#### [Konfigurera flexibilitet och återställning för Elasticsearch i Azure](guidance-elasticsearch-configuring-resilience-and-recovery.md)
#### [Skapa en testmiljö för Elasticsearch för Azure-prestanda](guidance-elasticsearch-creating-performance-testing-environment.md)
#### [Implementera en JMeter-testplan för Elasticsearch](guidance-elasticsearch-implementing-jmeter-test-plan.md)
#### [Distribuera JMeter JUnit-provet för att testa Elasticsearch-prestanda](guidance-elasticsearch-deploying-jmeter-junit-sampler.md)
#### [Kör återhämtningstesterna för Elasticsearch automatiskt](guidance-elasticsearch-running-automated-resilience-tests.md)
#### [Kör prestandatesterna för Elasticsearch automatiskt](guidance-elasticsearch-running-automated-performance-tests.md)

### [Identitetshantering för program med flera klienter i Microsoft Azure](guidance-multitenant-identity.md)
#### [Introduktion till identitetshantering för program med flera klienter i Microsoft Azure](guidance-multitenant-identity-intro.md)
#### [Om programmet Tailspin Surveys](guidance-multitenant-identity-tailspin.md)
#### [Autentisering i appar med flera klienter, med hjälp av Azure AD och OpenID Connect](guidance-multitenant-identity-authenticate.md)
#### [Arbeta med anspråksbaserade identiteter i program med flera klienter](guidance-multitenant-identity-claims.md)
#### [Registrering och klientanvändning i ett program med flera klienter](guidance-multitenant-identity-signup.md)
#### [Programroller i program med flera klienter](guidance-multitenant-identity-app-roles.md)
#### [Rollbaserad och resursbaserad auktorisering i program med flera klienter](guidance-multitenant-identity-authorize.md)
#### [Skydda en serverdelswebb-API i ett program med flera klienter](guidance-multitenant-identity-web-api.md)
#### [Cachelagring av åtkomsttoken i ett program med flera klienter](guidance-multitenant-identity-token-cache.md)
#### [Federera med en kunds AD FS för appar med flera klienter i Azure](guidance-multitenant-identity-adfs.md)
#### [Använda klientkontroll för att få åtkomsttoken från Azure AD](guidance-multitenant-identity-client-assertion.md)
#### [Använda Azure Key Vault för att skydda programhemligheter](guidance-multitenant-identity-keyvault.md)

#### [Distribuera virtuella enheter med hög tillgänglighet](guidance-nva-ha.md)


<!--HONumber=Nov16_HO2-->


