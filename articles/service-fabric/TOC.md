# [Dokumentation om Service Fabric](/azure/service-fabric)
# Översikt
## [Vad är Service Fabric?](service-fabric-overview.md)

# Snabbstarter
## [Skapa ett .NET-program](service-fabric-quickstart-dotnet.md)
## [Distribuera ett program för Linux-behållare](service-fabric-quickstart-containers-linux.md)
## [Distribuera ett program för Windows-behållare](service-fabric-quickstart-containers.md)
## Java-snabbstarter
### [Distribuera ett Spring Boot-program](service-fabric-quickstart-java-spring-boot.md)
### [Distribuera ett Reliable Services-program](service-fabric-quickstart-java-reliable-services.md)

# Självstudier
## Distribuera ett .NET-program
### [1- Skapa ett .NET-program](service-fabric-tutorial-create-dotnet-app.md)
### [2- Distribuera programmet](service-fabric-tutorial-deploy-app-to-party-cluster.md)
### [3- Konfigurera CI/CD](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
### [4- Övervaka och diagnostisera](service-fabric-tutorial-monitoring-aspnet.md)

## Använd en befintlig .NET-app i behållare
### [1- Distribuera en .NET-app med hjälp av Docker Compose](service-fabric-host-app-in-a-container.md)
### [2- Övervaka din behållare](service-fabric-tutorial-monitoring-wincontainers.md)

## Skapa en app för Linux-behållare
### [1- Skapa behållaravbildningar](service-fabric-tutorial-create-container-images.md)
### [2- Paketera och distribuera behållare](service-fabric-tutorial-package-containers.md)
### [3- Redundans och skalning](service-fabric-tutorial-containers-failover.md)

## Skapa och hantera ett kluster
### 1- Skapa ett kluster på Azure
#### [1a- Skapa ett Windows-kluster](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
#### [1a- Skapa ett Linux-kluster](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
### [2- Skala klustret](service-fabric-tutorial-scale-cluster.md)
### [3- Uppgradera körningstiden för kluster](service-fabric-tutorial-upgrade-cluster.md)
### [4- Distribuera API Management med Service Fabric](service-fabric-tutorial-deploy-api-management.md)



# Exempel
## [Kodexempel](https://azure.microsoft.com/resources/samples/?service=service-fabric)
## [Azure PowerShell](service-fabric-powershell-samples.md)
## [Service Fabric CLI](samples-cli.md)

# Koncept
## [Förstå mikrotjänster](service-fabric-overview-microservices.md)
## [Helhetsbild](service-fabric-content-roadmap.md)
## [Programscenarier](service-fabric-application-scenarios.md)
## [Mönster och scenarier](service-fabric-patterns-and-scenarios.md)
## [Arkitektur](service-fabric-architecture.md)
## [Terminologi](service-fabric-technical-overview.md)

## Skapa program och tjänster
### Programmeringsmodeller som stöds
#### [Översikt](service-fabric-choose-framework.md)
#### Behållare
##### [Översikt](service-fabric-containers-overview.md)
##### [Docker Compose (förhandsversion)](service-fabric-docker-compose.md)
##### [Resurs-styrning](service-fabric-resource-governance.md)
#### Reliable Services
##### [Översikt](service-fabric-reliable-services-introduction.md)
##### [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services-livscykel – Java](service-fabric-reliable-services-lifecycle-java.md)
##### [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
##### [Riktlinjer och rekommendationer för Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Internt i Reliable State Manager och Reliable Collection](service-fabric-reliable-services-reliable-collections-internals.md)
##### [Avancerad användning](service-fabric-reliable-services-advanced-usage.md)

#### Reliable Actors
##### [Översikt](service-fabric-reliable-actors-introduction.md)
##### [Arkitektur](service-fabric-reliable-actors-platform.md)
##### [Livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
##### [Tillståndshantering](service-fabric-reliable-actors-state-management.md)
##### [Polymorfism](service-fabric-reliable-actors-polymorphism.md)
##### [Återinträde](service-fabric-reliable-actors-reentrancy.md)
##### [Typserialisering](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

### [Programmodell](service-fabric-application-model.md)
### [Värdmodell](service-fabric-hosting-model.md)

### Tjänster
#### [Tjänstresurser](service-fabric-service-manifest-resources.md)
#### [Tjänsttillstånd](service-fabric-concepts-state.md)
#### [Tjänstpartitionering](service-fabric-concepts-partitioning.md)
#### [Tillgänglighet för tjänster](service-fabric-availability-services.md)
#### [Repliker och instanser](service-fabric-concepts-replica-lifecycle.md)
#### [Omkonfiguration](service-fabric-concepts-reconfiguration.md)
#### Tjänstkommunikation
##### [Översikt](service-fabric-connect-and-communicate-with-services.md)
##### [DNS-tjänst](service-fabric-dnsservice.md)
##### [Omvänd proxy](service-fabric-reverseproxy.md)
##### [Konfigurera omvänd proxy för säker kommunikation](service-fabric-reverseproxy-configure-secure-communication.md)
##### [Diagnostik för omvänd proxy](service-fabric-reverse-proxy-diagnostics.md)
### [Skalbarhet för program](service-fabric-concepts-scalability.md)
### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planera programkapacitet](service-fabric-capacity-planning.md)

## Hantera program
### [Översikt](service-fabric-application-lifecycle.md)
### [ImageStoreConnectionString-inställningen](service-fabric-image-store-connection-string.md)
### Programuppgradering
#### [Översikt](service-fabric-application-upgrade.md)
#### [Konfiguration](service-fabric-visualstudio-configure-upgrade.md)
#### [Programuppgraderingsparametrar](service-fabric-application-upgrade-parameters.md)
#### [Dataserialisering i programuppgraderingar](service-fabric-application-upgrade-data-serialization.md)
#### [Programuppgraderingar: avancerade ämnen](service-fabric-application-upgrade-advanced.md)
### [Översikt över felanalyser](service-fabric-testability-overview.md)

## Skapa och hantera kluster
### [Översikt](service-fabric-deploy-anywhere.md)
### Planera och förbereda
#### [Kapacitetsplanering](service-fabric-cluster-capacity.md)
#### [Haveriberedskap](service-fabric-disaster-recovery.md)
### [Beskriva ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)
### [Klustersäkerhet](service-fabric-cluster-security.md)
### [Funktionsskillnader mellan Linux och Windows](service-fabric-linux-windows-differences.md)
### Kluster i Azure
#### [Nodtyper och skalningsuppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md)
#### [Nätverksmönster för kluster](service-fabric-patterns-networking.md)
### Klusterresurshanterare
#### [Översikt](service-fabric-cluster-resource-manager-introduction.md)
#### [Arkitektur](service-fabric-cluster-resource-manager-architecture.md)
#### [Beskriv ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Översikt över programgrupper](service-fabric-cluster-resource-manager-application-groups.md)
#### [Konfigurera inställningar för Cluster Resource Manager](service-fabric-cluster-resource-manager-configure-services.md)
#### [Statistik över resursförbrukning](service-fabric-cluster-resource-manager-metrics.md)
#### [Använd tjänsttillhörighet](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
#### [Placeringsprinciper av tjänster](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
#### [Hantera ett kluster](service-fabric-cluster-resource-manager-management-integration.md)
#### [Klusterdefragmentering](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
#### [Balansera ett kluster](service-fabric-cluster-resource-manager-balancing.md)
#### [Begränsning](service-fabric-cluster-resource-manager-advanced-throttling.md)
#### [Tjänstflöde](service-fabric-cluster-resource-manager-movement-cost.md)

## [Integrera med API Management](service-fabric-api-management-overview.md)

## Övervaka och diagnostisera
### [Övervaka och diagnostisera program](service-fabric-diagnostics-overview.md)
### Generera händelser
#### [Generera händelser på plattformsnivå](service-fabric-diagnostics-event-generation-infra.md)
##### [Användningskanal](service-fabric-diagnostics-event-generation-operational.md)
##### [Reliable Services-händelser](service-fabric-reliable-services-diagnostics.md)
##### [Reliable Actors-händelser](service-fabric-reliable-actors-diagnostics.md)
##### [Prestandamått](service-fabric-diagnostics-event-generation-perf.md)
##### [Övervaka Service Remoting](service-fabric-reliable-serviceremoting-diagnostics.md)
#### [Generera händelser på programnivå](service-fabric-diagnostics-event-generation-app.md)
### Inspektera program- och klusterhälsa
#### [Övervaka Service Fabrics hälsa](service-fabric-health-introduction.md)
#### [Rapportera och kontrollera hälsan hos tjänster](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
#### [Lägg till anpassade hälsorapporter](service-fabric-report-health.md)
#### [Felsök med systemhälsorapporter](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
#### [Visa hälsorapporter](service-fabric-view-entities-aggregated-health.md)
### Sammanfatta händelser
#### [Sammanfatta händelser med EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)
#### Sammanfatta händelser med Azure-diagnostik
##### [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
##### [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
### Analysera händelser
#### [Analysera händelser med Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)
#### [Analysera händelser med OMS](service-fabric-diagnostics-event-analysis-oms.md)
### [Felsök ditt lokala kluster](service-fabric-troubleshoot-local-cluster-setup.md)

# Instruktionsguider
## Ställ in din utvecklingsmiljö
### [Windows](service-fabric-get-started.md)
### [Linux](service-fabric-get-started-linux.md)
### [Mac OS](service-fabric-get-started-mac.md)

## [Konfigurera Service Fabric CLI](service-fabric-cli.md)

## Skapa ett program
### [Skapa din första C#-app i Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)
### Skapa en körbar gästtjänst
#### [Vara värd för ett Node.js-program i Windows](quickstart-guest-app.md)
#### [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
#### [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)
### Skapa en behållartjänst
#### [Skapa ett program för Windows-behållare](service-fabric-get-started-containers.md)
#### [Skapa ett program för Linux-behållare](service-fabric-get-started-containers-linux.md)
#### [Behållarsäkerhet](service-fabric-securing-containers.md)
#### [Docker Compose (förhandsversion)](service-fabric-docker-compose.md)
#### [Resursstyrning för behållare och tjänster](service-fabric-resource-governance.md)
#### [Drivrutiner för volym och loggning](service-fabric-containers-volume-logging-drivers.md)
#### [Tjänster i behållare](service-fabric-services-inside-containers.md)
#### [Nätverkslägen för behållare](service-fabric-networking-modes.md)

### Skapa en Reliable Service-tjänst
#### Reliable Collections
##### [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)

#### Kom igång
##### [C# i Windows](service-fabric-reliable-services-quick-start.md)
##### [Java i Linux](service-fabric-reliable-services-quick-start-java.md)
##### [Skapa C#-program på Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
#### Kommunicera med tjänster
##### [Kommunicera med Reliable Services](service-fabric-reliable-services-communication.md)

##### [Fjärrtjänst – C#](service-fabric-reliable-services-communication-remoting.md)
##### [Fjärrtjänst – Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Säker kommunikation – C#](service-fabric-reliable-services-secure-communication.md)
##### [Säker kommunikation – Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Konfigurera](service-fabric-reliable-services-configuration.md)
#### [Skicka aviseringar](service-fabric-reliable-services-notifications.md)
#### [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)

### Skapa en Reliable Actors-tjänst
#### Kom igång
##### [C# i Windows](service-fabric-reliable-actors-get-started.md)
##### [Java-aktör i Linux](service-fabric-create-your-first-linux-application-with-java.md)
#### [Skicka aviseringar](service-fabric-reliable-actors-events.md)
#### [Ställ in timrar och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
#### [Konfigurera KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Konfigurera kommunikationsinställningar](service-fabric-reliable-actors-fabrictransportsettings.md)
#### [Konfigurera ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### [Migrera gamla Java-program som stöder Maven](service-fabric-migrate-old-javaapp-to-use-maven.md)

### [Konfigurera omvänd proxy för säker kommunikation](service-fabric-reverseproxy-configure-secure-communication.md)

### [Skapa en ASP.NET Core-tjänst](service-fabric-add-a-web-frontend.md)

### Konfigurera säkerhet
#### [Hantera programhemligheter](service-fabric-application-secret-management.md)  
#### [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md)

## Arbeta i en Windows-utvecklarmiljö
### [Hantera program i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
### [Konfigurera säkra anslutningar i Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
### [Konfigurera ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md)
### [Felsöka en .NET-tjänst i VS](service-fabric-debugging-your-application.md)
### [Vanliga fel och undantag](service-fabric-errors-and-exceptions.md)
### [Övervaka och diagnostisera lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
### [Konfigurera ett Linux-kluster på Windows](service-fabric-local-linux-cluster-windows.md)

## Arbeta i en Linux-utvecklarmiljö
### [Komma igång med Eclipse-plugin för utveckling med Java](service-fabric-get-started-eclipse.md)
### [Felsök en Java-tjänst i Eclipse](service-fabric-debugging-your-application-java.md)
### [Övervaka och diagnostisera lokalt](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

## Migrera från Cloud Services
### [Jämför Cloud Services med Service Fabric](service-fabric-cloud-services-migration-differences.md)
### [Migrera till Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
### [Rekommenderade metoder](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Hantera programlivscykel
### [Paketera ett program](service-fabric-package-apps.md)

### Distribuera eller ta bort program
#### [Distribuera program i ett lokalt kluster](service-fabric-get-started-with-a-local-cluster.md)
#### [Azure Resource Manager](service-fabric-application-arm-resource.md)
#### [Azure PowerShell](service-fabric-deploy-remove-applications.md)
#### [Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md)
#### [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)

### Uppgradera program
#### [Uppgradera med Azure PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
#### [Uppgradera med Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Felsök programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md)

### Testa program och tjänster
#### [Testa tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md)
#### Simulera fel
##### [Använda kontrollerat kaos](service-fabric-controlled-chaos.md)
##### [Använda teståtgärder](service-fabric-testability-actions.md)
##### [Under arbetsbelastningar](service-fabric-testability-workload-tests.md)
##### [Använda testscenarier](service-fabric-testability-scenarios.md)
##### [Använda API:er för nodövergång](service-fabric-node-transition-apis.md)

### Konfigurera kontinuerlig integrering
#### [Konfigurera kontinuerlig integrering med VSTS](service-fabric-set-up-continuous-integration.md)
#### [Distribuera ditt Linux-program med Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)

## Skapa och hantera kluster
### Kluster i Azure
#### Skapa
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
#### Skala
##### [Manuellt](service-fabric-cluster-scale-up-down.md)
##### [Programmässigt](service-fabric-cluster-programmatic-scaling.md)
#### [Uppgradera](service-fabric-cluster-upgrade.md)
#### [Ange åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera](service-fabric-cluster-fabric-settings.md)
#### [Öppna en port i belastningsutjämnaren](create-load-balancer-rule.md)
#### [Hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md)
#### [Ta bort](service-fabric-cluster-delete.md)

### Fristående kluster
#### [Planera och förbereda för distributionen](service-fabric-cluster-standalone-deployment-preparation.md)
#### Skapa
##### [Skapa ditt första fristående kluster](service-fabric-get-started-standalone-cluster.md)
##### [Skapa lokalt](service-fabric-cluster-creation-for-windows-server.md)
##### [Skydda med hjälp av certifikat](service-fabric-windows-cluster-x509-security.md)  
##### [Skydda med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md)
##### [Innehåll i det fristående paketet](service-fabric-cluster-standalone-package-contents.md)
#### [Skalning](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Ange åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera](service-fabric-cluster-manifest.md)
#### [Uppgradera](service-fabric-cluster-upgrade-windows-server.md)

### [Visualisera ett kluster](service-fabric-visualizing-your-cluster.md)
### [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md)
### [Korrigera klusternoder](service-fabric-patch-orchestration-application.md)

## Övervaka och diagnostisera
### OMS
#### [Konfigurera OMS Log Analytics](service-fabric-diagnostics-oms-setup.md)
#### [Lägga till OMS-agenten](service-fabric-diagnostics-oms-agent.md)
#### [Övervaka behållare](service-fabric-diagnostics-oms-containers.md)
### Prestandaövervakning
#### [Prestandaövervakning med WAD](service-fabric-diagnostics-perf-wad.md)

# Referens
## [Azure PowerShell](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/module/servicefabric/?view=azureservicefabricps)
## [Azure CLI (az sf)](/cli/azure/sf)
## [Service Fabric CLI (sfctl)](service-fabric-sfctl.md)
### [sfctl application](service-fabric-sfctl-application.md)
### [sfctl chaos](service-fabric-sfctl-chaos.md)
### [sfctl cluster](service-fabric-sfctl-cluster.md)
### [sfctl compose](service-fabric-sfctl-compose.md)
### [sfctl is](service-fabric-sfctl-is.md)
### [sfctl node](service-fabric-sfctl-node.md)
### [sfctl partition](service-fabric-sfctl-partition.md)
### [sfctl replica](service-fabric-sfctl-replica.md)
### [sfctl rpm](service-fabric-sfctl-rpm.md)
### [sfctl service](service-fabric-sfctl-service.md)
### [sfctl store](service-fabric-sfctl-store.md)
## [Java-API](/java/api/overview/azure/servicefabric)
## [.NET](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet)
## [REST](/rest/api/servicefabric)
## [XML-schema för tjänstmodell](service-fabric-service-model-schema.md)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/)
## [Vanliga frågor](service-fabric-common-questions.md)
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/service-fabric/)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?forum=AzureServiceFabric)
## [Prissättning](https://azure.microsoft.com/pricing/details/service-fabric/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Exempelkod](http://aka.ms/servicefabricsamples)
## [Supportalternativ](service-fabric-support.md)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=service-fabric)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=service-fabric)