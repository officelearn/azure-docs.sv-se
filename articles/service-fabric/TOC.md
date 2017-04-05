
# Översikt
## [Vad är Service Fabric?](service-fabric-overview.md)
## [Förstå mikrotjänster](service-fabric-overview-microservices.md)
## [Programscenarier](service-fabric-application-scenarios.md)
## [Arkitektur](service-fabric-architecture.md)
## [Terminologi](service-fabric-technical-overview.md)
## [Innehållsöversikt](service-fabric-content-roadmap.md)

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
### [Mönster och scenarier](service-fabric-patterns-and-scenarios.md)
### Grundläggande inställningar
#### [Programmodell](service-fabric-application-model.md)
#### [Paketera ett program](service-fabric-package-apps.md)
#### [Programmeringsmodell som stöds](service-fabric-choose-framework.md)
#### [Tjänsttillstånd](service-fabric-concepts-state.md)
#### [Tjänstkommunikation](service-fabric-connect-and-communicate-with-services.md)
#### [Lägga till en frontwebb](service-fabric-add-a-web-frontend.md)
#### [Tjänstmanifestresurser](service-fabric-service-manifest-resources.md)
#### [Komma igång med Eclipse-plugin för utveckling med Java](service-fabric-get-started-eclipse.md)
#### [Hantera appar i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Konfigurera säkra anslutningar i Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### Felsökning
##### [Felsök en C#-tjänst i VS](service-fabric-debugging-your-application.md)
##### [Felsök en Java-tjänst i Eclipse](service-fabric-debugging-your-application-java.md)
#### Övervaka och diagnostisera
##### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
##### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
#### [Hantera programhemligheter](service-fabric-application-secret-management.md)  
#### [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md)  
#### [Konfigurera ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md)  
#### [Vanliga fel och undantag](service-fabric-errors-and-exceptions.md) 

### Körbart gästprogram
#### [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
#### [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)

### Behållarprogram
#### [Översikt](service-fabric-containers-overview.md)
#### [Distribuera Windows-behållare](service-fabric-deploy-container.md)
#### [Distribuera Docker-behållare](service-fabric-deploy-container-linux.md)

### Reliable Services-program
#### [Översikt](service-fabric-reliable-services-introduction.md)
#### Kom igång
##### [C# i Windows](service-fabric-reliable-services-quick-start.md)
##### [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
#### [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
#### [Reliable Services-livscykel – Java](service-fabric-reliable-services-lifecycle-java.md)
#### [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
#### [Använd Reliable Collections](service-fabric-work-with-reliable-collections.md)
#### [Konfigurera](service-fabric-reliable-services-configuration.md)
#### [Meddelanden](service-fabric-reliable-services-notifications.md)
#### [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)
#### [Kommunicera med Reliable Services](service-fabric-reliable-services-communication.md)
#### [Säker kommunikation med Reliable Services – C#](service-fabric-reliable-services-secure-communication.md)
#### [Säker kommunikation med Reliable Services – Java](service-fabric-reliable-services-secure-communication-java.md)
##### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
##### [Fjärrtjänst – C#](service-fabric-reliable-services-communication-remoting.md)
##### [Fjärrtjänst – Java](service-fabric-reliable-services-communication-remoting-java.md)
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
#### [Händelser](service-fabric-reliable-actors-events.md) 
#### [Timers och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
#### [Tillståndshantering](service-fabric-reliable-actors-state-management.md)
#### [Konfigurera KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Typserialisering](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)
#### [Konfigurera kommunikationsinställningar](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Konfigurera ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

## Migrera från Cloud Services
### [Jämför Cloud Services med Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrera till Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Rekommenderade metoder](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Skapa och hantera kluster

### Grundläggande inställningar
#### [Översikt](service-fabric-deploy-anywhere.md)
#### [Beskriv ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Kapacitetsplanering](service-fabric-cluster-capacity.md)
#### [Visualisera ett kluster](service-fabric-visualizing-your-cluster.md)
#### [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md)
#### [Hantera ett kluster med Azure CLI](service-fabric-azure-cli.md) 
#### [Säkra ett kluster](service-fabric-cluster-security.md)
#### [Haveriberedskap](service-fabric-disaster-recovery.md)
#### [Funktionsskillnader mellan Linux och Windows](service-fabric-linux-windows-differences.md)

### Kluster i Azure
#### Skapa ett kluster på Azure
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio och Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### [Nätverksmönster för kluster](service-fabric-patterns-networking.md)
#### [Nodtyper och skalningsuppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md)
#### [Skala ett kluster](service-fabric-cluster-scale-up-down.md)
#### [Skala ett kluster programmässigt](service-fabric-cluster-programmatic-scaling.md)
#### [Uppgradera ett kluster](service-fabric-cluster-upgrade.md)
#### [Ta bort ett kluster](service-fabric-cluster-delete.md)
#### [Åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera ett kluster](service-fabric-cluster-fabric-settings.md)
#### [Lägg till eller förnya klustercertifikat](service-fabric-cluster-security-update-certs-azure.md) 
#### [Testa ett Party-kluster utan kostnad](http://aka.ms/tryservicefabric)

### Fristående kluster
#### [Planera och förbereda för distributionen](service-fabric-cluster-standalone-deployment-preparation.md)
#### [Innehåll i fristående Service Fabric-paket](service-fabric-cluster-standalone-package-contents.md)
#### [Skapa ett fristående kluster](service-fabric-cluster-creation-for-windows-server.md)
#### [Skapa ett fristående kluster på Azure Virtual Machines](service-fabric-cluster-creation-with-windows-azure-vms.md)
#### [Skala ett kluster](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Uppgradera ett kluster](service-fabric-cluster-upgrade-windows-server.md)
#### [Åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera ett kluster](service-fabric-cluster-manifest.md)
#### [Skydda ett kluster med certifikat](service-fabric-windows-cluster-x509-security.md)  
#### [Skydda ett kluster med Windows-säkerhet](service-fabric-windows-cluster-windows-security.md) 

## Hantera programlivscykel
### [Översikt](service-fabric-application-lifecycle.md)
### [Konfigurera kontinuerlig integrering med VSTS](service-fabric-set-up-continuous-integration.md)
### [Distribuera din Linux Java-app med hjälp av Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)
### [Om inställningen ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
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

## Inspektera program- och klusterhälsa
### [Övervaka Service Fabrics hälsa](service-fabric-health-introduction.md)
### [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
### [Lägg till anpassade hälsorapporter](service-fabric-report-health.md)
### [Felsök med systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
### [Visa hälsorapporter](service-fabric-view-entities-aggregated-health.md)

## Övervaka och diagnostisera
### [Övervaka och diagnostisera program](service-fabric-diagnostics-overview.md)
### Övervaka och diagnostisera tjänster lokalt
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
### Azure Diagnostics-loggar
#### [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
#### [Linux](service-fabric-diagnostics-how-to-setup-lad.md)
### [Samla in loggar från en tjänstprocess](service-fabric-diagnostic-collect-logs-without-an-agent.md)
### [Diagnostik för tillståndskänslig Reliable Services](service-fabric-reliable-services-diagnostics.md)
### [Diagnostik i Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Felsök ditt lokala kluster](service-fabric-troubleshoot-local-cluster-setup.md)

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
#### [Använda testscenarier](service-fabric-testability-scenarios.md)
#### [Använda API:er för nodövergång](service-fabric-node-transition-apis.md)
### [Belastningstesta ditt program](service-fabric-vso-load-test.md)

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

# Referens
## [PowerShell](//powershell/servicefabric/vlatest/servicefabric)
## [Java-API](/java/api/microsoft.servicefabric.services)
## [.NET](/dotnet/api/microsoft.servicefabric.services)
## [REST](/rest/api/servicefabric)

# Resurser
## [Vanliga frågor om Service Fabric](service-fabric-common-questions.md)
## [Service Fabric-supportalternativ](service-fabric-support.md)
## [Exempelkod](http://aka.ms/servicefabricsamples)
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [Prissättning](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=service-fabric)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)
