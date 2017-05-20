
# Översikt
## [Vad är Service Fabric?](service-fabric-overview.md)
## [Förstå mikrotjänster](service-fabric-overview-microservices.md)
## [Programscenarier](service-fabric-application-scenarios.md)
## [Mönster och scenarier](service-fabric-patterns-and-scenarios.md)
## [Arkitektur](service-fabric-architecture.md)
## [Terminologi](service-fabric-technical-overview.md)
## [Beskrivning](service-fabric-content-roadmap.md)

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
## [Distribuera .NET-appar i en behållare](service-fabric-host-app-in-a-container.md)
## [Skapa ditt första kluster på Azure](service-fabric-get-started-azure-cluster.md)
## [Skapa ditt första fristående kluster](service-fabric-get-started-standalone-cluster.md)
## [Skapa din första behållarapp](service-fabric-get-started-containers.md)

# Gör så här för att
## Skapa ett program
  
### Koncept
#### [Programmeringsmodeller som stöds](service-fabric-choose-framework.md)
#### [Programmodell](service-fabric-application-model.md)
#### [Värdmodell](service-fabric-hosting-model.md)
#### [Tjänstmanifestresurser](service-fabric-service-manifest-resources.md)
#### [Tjänsttillstånd](service-fabric-concepts-state.md)
#### [Tjänstpartitionering](service-fabric-concepts-partitioning.md)
#### [Tillgänglighet för tjänster](service-fabric-availability-services.md)
#### [Skalbarhet för program](service-fabric-concepts-scalability.md)
#### [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

### [Planera appkapacitet](service-fabric-capacity-planning.md)

### Skapa en körbar gästtjänst
#### [Distribuera en körbar gäst](service-fabric-deploy-existing-app.md)
#### [Distribuera flera körbara gäster](service-fabric-deploy-multiple-apps.md)

### Skapa en behållartjänst
#### [Översikt](service-fabric-containers-overview.md)
#### [Distribuera Windows-behållare](service-fabric-deploy-container.md)
#### [Distribuera Linux-behållare](service-fabric-deploy-container-linux.md)
#### [Docker Compose (förhandsversion)](service-fabric-docker-compose.md)
#### [Resursstyrning för behållare och tjänster](service-fabric-resource-governance.md)

### Skapa en Reliable Service-tjänst
#### [Översikt](service-fabric-reliable-services-introduction.md)
#### Koncept
##### [Reliable Services-livscykel – C#](service-fabric-reliable-services-lifecycle.md)
##### [Reliable Services-livscykel – Java](service-fabric-reliable-services-lifecycle-java.md)

#### Reliable Collections
##### [Tillförlitliga samlingar](service-fabric-reliable-services-reliable-collections.md)
##### [Riktlinjer och rekommendationer för Reliable Collections](service-fabric-reliable-services-reliable-collections-guidelines.md)
##### [Arbeta med Reliable Collections](service-fabric-work-with-reliable-collections.md)
##### [Transaktioner och lås](service-fabric-reliable-services-reliable-collections-transactions-locks.md)
##### [Reliable Concurrent Queue](service-fabric-reliable-services-reliable-concurrent-queue.md)
##### [Reliable Collection-serialisering](service-fabric-reliable-services-reliable-collections-serialization.md)
##### [Internt i Reliable State Manager och Reliable Collection](service-fabric-reliable-services-reliable-collections-internals.md)

#### Kom igång
##### [C# i Windows](service-fabric-reliable-services-quick-start.md)
##### [Java i Linux](service-fabric-reliable-services-quick-start-java.md)

#### Reliable Services-livscykel
##### [Konfigurera](service-fabric-reliable-services-configuration.md)
##### [Skicka aviseringar](service-fabric-reliable-services-notifications.md)
##### [Säkerhetskopiering och återställning](service-fabric-reliable-services-backup-restore.md)

#### Kommunicera med tjänster
##### [Kommunicera med Reliable Services](service-fabric-reliable-services-communication.md)
##### [Fjärrtjänst – C#](service-fabric-reliable-services-communication-remoting.md)
##### [Fjärrtjänst – Java](service-fabric-reliable-services-communication-remoting-java.md)
##### [WCF](service-fabric-reliable-services-communication-wcf.md)
##### [Säker kommunikation – C#](service-fabric-reliable-services-secure-communication.md)
##### [Säker kommunikation – Java](service-fabric-reliable-services-secure-communication-java.md)

#### [Avancerad användning](service-fabric-reliable-services-advanced-usage.md)

### Skapa en Reliable Actor-tjänst
#### [Översikt](service-fabric-reliable-actors-introduction.md)
#### Koncept
#### [Arkitektur](service-fabric-reliable-actors-platform.md)
#### [Livscykel och skräpinsamling](service-fabric-reliable-actors-lifecycle.md)
#### [Tillståndshantering](service-fabric-reliable-actors-state-management.md)
#### [Polymorfism](service-fabric-reliable-actors-polymorphism.md)
#### [Återinträde](service-fabric-reliable-actors-reentrancy.md)
#### [Typserialisering](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

#### Kom igång
##### [C# i Windows](service-fabric-reliable-actors-get-started.md)
##### [Java i Linux](service-fabric-reliable-actors-get-started-java.md)

#### [Skicka aviseringar](service-fabric-reliable-actors-events.md) 
#### [Ställ in timrar och påminnelser](service-fabric-reliable-actors-timers-reminders.md)
#### [Konfigurera KvsActorStateProvider](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)
#### [Konfigurera kommunikationsinställningar](service-fabric-reliable-actors-fabrictransportsettings.md) 
#### [Konfigurera ReliableDictionaryActorStateProvider](service-fabric-reliable-actors-reliabledictionarystateprovider-configuration.md)

### Kommunicera med tjänster
#### [Tjänstkommunikation](service-fabric-connect-and-communicate-with-services.md)
#### [DNS-tjänst](service-fabric-dnsservice.md)
#### [Omvänd proxy](service-fabric-reverseproxy.md)

### [Lägga till en frontwebb](service-fabric-add-a-web-frontend.md)

### Arbeta i IDE
#### [Komma igång med Eclipse-plugin för utveckling med Java](service-fabric-get-started-eclipse.md)
#### [Hantera appar i Visual Studio](service-fabric-manage-application-in-visual-studio.md)
#### [Konfigurera säkra anslutningar i Visual Studio](service-fabric-visualstudio-configure-secure-connections.md)
#### [Konfigurera ditt program för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md)

### Konfigurera säkerhet
#### [Hantera programhemligheter](service-fabric-application-secret-management.md)  
#### [Konfigurera säkerhetsprinciper för ditt program](service-fabric-application-runas-security.md)

### Felsökning
#### [Felsök en C#-tjänst i VS](service-fabric-debugging-your-application.md)
#### [Felsök en Java-tjänst i Eclipse](service-fabric-debugging-your-application-java.md)
#### [Vanliga fel och undantag](service-fabric-errors-and-exceptions.md)

### Övervaka och diagnostisera lokalt
#### [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
#### [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)

### Migrera från Cloud Services
#### [Jämför Cloud Services med Service Fabric](service-fabric-cloud-services-migration-differences.md)
#### [Migrera till Service Fabric](service-fabric-cloud-services-migration-worker-role-stateless-service.md)
#### [Rekommenderade metoder](/azure/architecture/service-fabric/migrate-from-cloud-services)

## Hantera programlivscykel
### [Översikt](service-fabric-application-lifecycle.md)
### [Paketera ett program](service-fabric-package-apps.md)
### [Om inställningen ImageStoreConnectionString](service-fabric-image-store-connection-string.md)
### Distribuera eller ta bort program
#### [PowerShell](service-fabric-deploy-remove-applications.md)
#### [Visual Studio](service-fabric-publish-app-remote-cluster.md)
#### [FabricClient-API:er](service-fabric-deploy-remove-applications-fabricclient.md)
### Uppgradera ett program
#### [Översikt](service-fabric-application-upgrade.md)
#### [Konfigurera programuppgradering](service-fabric-visualstudio-configure-upgrade.md)
#### [Programuppgraderingsparametrar](service-fabric-application-upgrade-parameters.md)
#### Uppgradera
##### [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
##### [Visual Studio](service-fabric-application-upgrade-tutorial.md)
#### [Felsök programuppgraderingar](service-fabric-application-upgrade-troubleshooting.md)
#### [Dataserialisering i programuppgraderingar](service-fabric-application-upgrade-data-serialization.md)
#### [Programuppgraderingar: avancerade ämnen](service-fabric-application-upgrade-advanced.md)

### Testa program och tjänster
#### [Översikt över felanalyser](service-fabric-testability-overview.md)
#### [Testa tjänst-till-tjänst-kommunikation](service-fabric-testability-scenarios-service-communication.md)
#### Simulera fel
##### [Använda kontrollerat kaos](service-fabric-controlled-chaos.md)
##### [Använda teståtgärder](service-fabric-testability-actions.md)
##### [Under arbetsbelastningar](service-fabric-testability-workload-tests.md)
##### [Använda testscenarier](service-fabric-testability-scenarios.md)
##### [Använda API:er för nodövergång](service-fabric-node-transition-apis.md)
#### [Belastningstesta ditt program](service-fabric-vso-load-test.md)

### Konfigurera kontinuerlig integrering
#### [Konfigurera kontinuerlig integrering med VSTS](service-fabric-set-up-continuous-integration.md)
#### [Distribuera din Linux Java-app med hjälp av Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md)

## Skapa och hantera kluster

### [Översikt](service-fabric-deploy-anywhere.md)
### Koncept
#### [Beskriv ett kluster](service-fabric-cluster-resource-manager-cluster-description.md)
#### [Klustersäkerhet](service-fabric-cluster-security.md)
#### [Funktionsskillnader mellan Linux och Windows](service-fabric-linux-windows-differences.md)

### Planera och förbereda
#### [Kapacitetsplanering](service-fabric-cluster-capacity.md)
#### [Haveriberedskap](service-fabric-disaster-recovery.md)

### Kluster i Azure
#### Koncept
##### [Nodtyper och skalningsuppsättningar för virtuella datorer](service-fabric-cluster-nodetypes.md)
##### [Nätverksmönster för kluster](service-fabric-patterns-networking.md)
#### Skapa 
##### [Azure Portal](service-fabric-cluster-creation-via-portal.md)
##### [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
##### [Visual Studio och Azure Resource Manager](service-fabric-cluster-creation-via-visual-studio.md)
#### Skala 
##### [Manuellt](service-fabric-cluster-scale-up-down.md)
##### [Programmässigt](service-fabric-cluster-programmatic-scaling.md)
#### [Uppgradera](service-fabric-cluster-upgrade.md)
#### [Ange åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera](service-fabric-cluster-fabric-settings.md)
#### [Hantera klustercertifikat](service-fabric-cluster-security-update-certs-azure.md) 
#### [Ta bort](service-fabric-cluster-delete.md)

### Fristående kluster
#### [Innehåll i det fristående paketet](service-fabric-cluster-standalone-package-contents.md)
#### [Planera och förbereda för distributionen](service-fabric-cluster-standalone-deployment-preparation.md)
#### Skapa
##### [Skapa lokalt](service-fabric-cluster-creation-for-windows-server.md)
##### [Skapa på virtuella Azure-datorer](service-fabric-cluster-creation-with-windows-azure-vms.md)
##### [Skydda med hjälp av certifikat](service-fabric-windows-cluster-x509-security.md)  
##### [Skydda med hjälp av Windows-säkerhet](service-fabric-windows-cluster-windows-security.md)
#### [Skalning](service-fabric-cluster-windows-server-add-remove-nodes.md)
#### [Ange åtkomstkontroll](service-fabric-cluster-security-roles.md)
#### [Konfigurera](service-fabric-cluster-manifest.md)
#### [Uppgradera](service-fabric-cluster-upgrade-windows-server.md) 

### [Visualisera ett kluster](service-fabric-visualizing-your-cluster.md)
### [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md)

### [Hantera ett kluster med Azure CLI](service-fabric-azure-cli.md)
### [Korrigera klusternoder](service-fabric-patch-orchestration-application.md)

### Hantera och dirigera klusterresurser
#### [Översikt över Cluster Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
#### [Cluster Resource Manager-arkitektur](service-fabric-cluster-resource-manager-architecture.md)
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

### [Ansluta till ett säkert kluster](service-fabric-connect-to-secure-cluster.md)

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
### [Övervaka Windows Server-behållare](service-fabric-diagnostics-containers-windowsserver.md)
### [Diagnostik för tillståndskänslig Reliable Services](service-fabric-reliable-services-diagnostics.md)
### [Diagnostik i Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
### [Felsök ditt lokala kluster](service-fabric-troubleshoot-local-cluster-setup.md)

# Referens
## [PowerShell (Azure)](/powershell/module/azurerm.servicefabric/)
## [PowerShell](/powershell/azure/overview?view=azureservicefabricps)
## [Java-API](/java/api/)
## [.NET](/dotnet/api/)
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
