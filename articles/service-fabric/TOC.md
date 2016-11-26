# Översikt
## [Vad är Service Fabric?](service-fabric-overview.md)
## [Förstå mikrotjänster](service-fabric-overview-microservices.md)
## [Programscenarier](service-fabric-application-scenarios.md)
## [Arkitektur](service-fabric-architecture.md)

# Kom igång
## Ställ in din utvecklingsmiljö
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)
## Skapa ditt första program
### [C# i Windows](service-fabric-create-your-first-application-in-visual-studio.md)
### [Java i Linux](service-fabric-create-your-first-linux-application-with-java.md)
### [C# i Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
## [Distribuera appar i ett lokalt kluster](service-fabric-get-started-with-a-local-cluster.md)

# Gör så här för att
## Skapa ett program
### Grundläggande inställningar
#### [Programmeringsmodell](service-fabric-choose-framework.md)
#### [Programmodell](service-fabric-application-model.md)
#### [Tjänstkommunikation](service-fabric-connect-and-communicate-with-services.md)
#### [Verktyg](service-fabric-manage-application-in-visual-studio.md)
#### [Felsökning](service-fabric-debugging-your-application.md)
#### Övervaka och diagnostisera
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md)
#### [Konfigurera ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md)

### Reliable Services-program
#### [Översikt](service-fabric-reliable-services-introduction.md)
#### Kom igång
##### [C# i Windows](service-fabric-reliable-services-quick-start.md)
##### [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Arkitektur](service-fabric-reliable-services-platform-architecture.md)
#### [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
#### [Använd Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Konfigurera](service-fabric-reliable-services-configuration.md)
#### [Meddelanden](service-fabric-reliable-services-notifications.md)
#### [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
#### [Kommunicera med Reliable Services](service-fabric-reliable-services-communication.md)
##### [ASP.NET](service-fabric-reliable-services-communication-webapi.md)
##### [Fjärrtjänst](service-fabric-reliable-services-communication-remoting.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Omvänd proxy](service-fabric-reverseproxy.md)
#### [Avancerad användning](service-fabric-reliable-services-advanced-usage.md)

### Reliable Actor-program
#### [Översikt](service-fabric-reliable-actors-introduction.md)
#### Kom igång
##### [C# i Windows](service-fabric-reliable-actors-get-started.md)
##### [Java i Linux](service-fabric-reliable-actors-get-started-java.md)
#### [Arkitektur](service-fabric-reliable-actors-platform.md)
#### [Livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
#### [Polymorfism](service-fabric-reliable-actors-polymorphism.md)
#### [Återinträde](service-fabric-reliable-actors-reentrancy.md)
#### [Timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
#### [Händelser](service-fabric-reliable-actors-events.md)
#### [Tillståndshantering](service-fabric-reliable-actors-state-management.md)
#### [Konfigurera tillståndsprovider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Typserialisering](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### Körbart gästprogram
#### [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
#### [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)

### Behållarprogram
#### [Översikt](service-fabric-containers-overview.md)
#### [Distribuera Windows-behållare](service-fabric-deploy-container.md)
#### [Distribuera Docker-behållare](service-fabric-deploy-container-linux.md)

## Migrera från Cloud Services
### [Jämför Cloud Services med Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrera till Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)

## Skapa och hantera kluster

### Grundläggande inställningar
#### [Översikt](service-fabric-deploy-anywhere.md)
#### [Beskriv ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Kapacitetsplanering](service-fabric-cluster-capacity.md)
#### [Visualisera ett kluster](service-fabric-visualizing-your-cluster.md)
#### [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md)
#### [Säkerhet](service-fabric-cluster-security.md)
#### [Haveriberedskap](service-fabric-disaster-recovery.md)

### Kluster i Azure
#### Skapa ett kluster på Azure
##### [Azure-portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### [Nodtyper och skalningsuppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md)
#### [Skala ett kluster](service-fabric-cluster-scale-up-down.md)
#### [Uppgradera ett kluster](service-fabric-cluster-upgrade.md)
#### [Ta bort ett kluster](service-fabric-cluster-delete.md)
#### [Åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera ett kluster](service-fabric-cluster-fabric-settings.md)
#### [Testa ett Party-kluster utan kostnad](http://aka.ms/tryservicefabric)

### Fristående kluster
#### [Skapa ett fristående kluster](service-fabric-cluster-creation-for-windows-server.md)
#### [Skala ett kluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Uppgradera ett kluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Säkra ett kluster](service-fabric-windows-cluster-x509-security.md)
#### [Åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera ett kluster](service-fabric-cluster-manifest.md)

## Hantera och dirigera klusterresurser
### [Översikt över Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
### [Cluster Resource Manager-arkitektur](service-fabric-cluster-resource-manager-architecture.md)
### [Beskriv ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Översikt över programgrupper](service-fabric-cluster-resource-manager-application-groups.md)
### [Konfigurera inställningar för Cluster Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)
### [Statistik över resursförbrukning](service-fabric-cluster-resource-manager-metrics.md)
### [Använd tjänsttillhörighet](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
### [Placeringsprinciper av tjänster](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
### [Hantera ett kluster](service-fabric-cluster-resource-manager-management-integration.md)
### [Klusterdefragmentering](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
### [Balansera ett kluster](service-fabric-cluster-resource-manager-balancing.md)
### [Begränsning](service-fabric-cluster-resource-manager-advanced-throttling.md)
### [Tjänstflöde](service-fabric-cluster-resource-manager-movement-cost.md)

## Hantera programlivscykel
### [Översikt](service-fabric-application-lifecycle.md)
### [Konfigurera kontinuerlig integrering](service-fabric-set-up-continuous-integration.md)
### Distribuera eller ta bort program
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
### [Översikt över programuppgradering](service-fabric-application-upgrade.md)
### [Konfigurera programuppgradering](service-fabric-visualstudio-configure-upgrade.md)
### [Programuppgraderingsparametrar](service-fabric-application-upgrade-parameters.md)
### Uppgradera ett program
#### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
### [Felsök programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md)
### [Dataserialisering i programuppgraderingar](service-fabric-application-upgrade-data-serialization.md)
### [Programuppgraderingar: avancerade ämnen](service-fabric-application-upgrade-advanced.md)
### [Exempel på REST-baserad livscykel för program](service-fabric-rest-based-application-lifecycle-sample.md)

## Inspektera program- och klusterhälsa
### [Övervaka Service Fabrics hälsa](service-fabric-health-introduction.md)
### [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Lägg till anpassade hälsorapporter](service-fabric-report-health.md)
### [Felsök med systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Visa hälsorapporter](service-fabric-view-entities-aggregated-health.md)

## Övervaka och diagnostisera
### Övervaka och diagnostisera tjänster lokalt
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure Diagnostics-loggar
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Service Fabric-programspårning](service-fabric-diagnostic-how-to-use-elasticsearch.md)
### [Diagnostik i Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Diagnostik för tillståndskänslig Reliable Services](service-fabric-reliable-services-diagnostics.md)
### [Felsök ditt lokala kluster](service-fabric-troubleshoot-local-cluster-setup.md)
### [Felsök vanliga problem](service-fabric-diagnostics-troubleshoot-common-scenarios.md)

## Skala program
### [Partitionera Reliable Services](service-fabric-concepts-partitioning.md)
### [Tillgänglighet för tjänster](service-fabric-availability-services.md)
### [Skala program](service-fabric-concepts-scalability.md)
### [Planera programkapacitet](service-fabric-capacity-planning.md)

## Testa program och tjänster
### [Översikt av felanalyser](service-fabric-testability-overview.md)
### [Testa tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md)
### Simulera fel
#### [Använda kontrollerat kaos](service-fabric-controlled-chaos.md)
#### [Använda teståtgärder](service-fabric-testability-actions.md)
#### [Under arbetsbelastningar](service-fabric-testability-workload-tests.md)
#### [Genom att aktivera dataförlust](service-fabric-use-data-loss-api.md)
#### [Använda testscenarier](service-fabric-testability-scenarios.md)
### [Belastningstesta ditt program](service-fabric-vso-load-test.md)

# Referens
## [Terminologi](service-fabric-technical-overview.md)
## [Reliable Actors](https://go.microsoft.com/fwlink/p/?linkid=833398)
## [Reliable Actors WCF](https://go.microsoft.com/fwlink/p/?linkid=833401)
## [Reliable Services](https://go.microsoft.com/fwlink/p/?linkid=833402)
## [Reliable Services WCF](https://go.microsoft.com/fwlink/p/?linkid=833403)
## [Data](https://go.microsoft.com/fwlink/p/?linkid=833404)
## [Gränssnitt för data](https://go.microsoft.com/fwlink/p/?linkid=833406)
## [System](https://go.microsoft.com/fwlink/p/?linkid=833407)
## [Exempelkod](http://aka.ms/servicefabricsamples)
## [PowerShell](/powershell/servicefabric/vlatest/servicefabric)
## [Java-API](https://go.microsoft.com/fwlink/p/?linkid=833410)
## [NET](/dotnet/api)
## [REST](/rest/api/servicefabric/)

# Resurser
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Prissättning](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)


<!--HONumber=Nov16_HO4-->


