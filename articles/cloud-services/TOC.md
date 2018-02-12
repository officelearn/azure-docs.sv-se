# Översikt
## [Vad är Cloud Services?](cloud-services-choose-me.md)
## [Konfigurationsfiler och paketering av molntjänster](cloud-services-model-and-package.md)

# Kom igång
## [Exempel på .NET-molntjänst](cloud-services-dotnet-get-started.md)
## [Exempel på Python för Visual Studio-molntjänst](cloud-services-python-ptvs.md)
## [Konfigurera ett HPC-hybridkluster med Microsoft HPC Pack](cloud-services-setup-hybrid-hpcpack-cluster.md)

# Gör så här för att
## Planera
### [Storlekar för virtuella datorer](cloud-services-sizes-specs.md)
### [Uppdateringar](cloud-services-update-azure-service.md)

## Utveckla
### [Skapa PHP webb- och arbetsroller](../cloud-services-php-create-web-role.md)
### [Bygg och distribuera ett Node.js-program](cloud-services-nodejs-develop-deploy-app.md)
### [Skapa en Node.js-webbapp med Express](cloud-services-nodejs-develop-deploy-express-app.md)
### Storage och Visual Studio
#### [Blob Storage och anslutna tjänster](../visual-studio/vs-storage-cloud-services-getting-started-blobs.md)
#### [Queue Storage och anslutna tjänster](../visual-studio/vs-storage-cloud-services-getting-started-queues.md)
#### [Table Storage och anslutna tjänster](../visual-studio/vs-storage-cloud-services-getting-started-tables.md)
### Konfigurera paket för kontinuerligt skapande och distribution
#### [TFS och Team Build](cloud-services-dotnet-continuous-delivery.md)
### [Konfigurera trafikregler för en roll](cloud-services-enable-communication-role-instances.md)
### [Hantera livscykelhändelser för molntjänst](cloud-services-role-lifecycle-dotnet.md)
### [Socket.io (Node.js)](cloud-services-nodejs-chat-app-socketio.md)
### [Använda Twilio för att ringa ett telefonsamtal (.NET)](../partner-twilio-cloud-services-dotnet-phone-call-web-role.md)
### [New Relic](../store-new-relic-cloud-services-dotnet-application-performance-management.md)

### Konfigurera startuppgifter
#### [Skapa startuppgifter](cloud-services-startup-tasks.md)
#### [Vanliga startuppgifter](cloud-services-startup-tasks-common.md)
#### [Använd en åtgärd för att installera .NET på en molntjänstroll](cloud-services-dotnet-install-dotnet.md)

### Konfigurera Fjärrskrivbord
#### [Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
#### [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)

## Distribuera
### [Skapa och distribuera en molntjänst i portalen](cloud-services-how-to-create-deploy-portal.md)
### [Skapa en tom molntjänstbehållare i PowerShell](cloud-services-powershell-create-cloud-container.md)
### [Konfigurera ett anpassat domännamn](cloud-services-custom-domain-name-portal.md)
### [Anslut till en anpassad domänkontrollant](cloud-services-connect-to-custom-domain.md)

## Hantera tjänst
### [Vanliga administrativa uppgifter](cloud-services-how-to-manage-portal.md)
### [Konfigurera en molntjänst](cloud-services-how-to-configure-portal.md)
### [Hantera en molntjänst med Azure Automation](automation-manage-cloud-services.md)
### [Konfigurera automatisk skalning](cloud-services-how-to-scale-portal.md)
### [Använd Python för att hantera Azure-resurser](cloud-services-python-how-to-use-service-management.md)
### [Minimera spekulativ körning](mitigate-se.md)

### [Gästoperativsystemets korrigeringsfiler](cloud-services-guestos-msrc-releases.md)
### Gästoperativsystem, indragning
#### [Indragningsprincip](cloud-services-guestos-retirement-policy.md)
#### [Meddelande om indragning, familj 1](cloud-services-guestos-family1-retirement.md)
### [Versionsnyheter om gästoperativsystem](cloud-services-guestos-update-matrix.md)
### [XPath-facit för rollkonfiguration i Cloud Services](cloud-services-role-config-xpath.md)

## Hantera certifikat
### [Cloud Services och hanteringscertifikat](cloud-services-certs-create.md)
### [Konfigurera SSL](cloud-services-configure-ssl-certificate-portal.md)

## Övervaka
### [Övervaka molntjänster](cloud-services-how-to-monitor.md)
### [Använda prestandaräknare](diagnostics-performance-counters.md)
### [Testa prestanda](../vs-azure-tools-performance-profiling-cloud-services.md)
#### [Testa med Visual Studio Profiler](cloud-services-performance-testing-visual-studio-profiler.md)
### Aktivera diagnostik
#### [Azure PowerShell](cloud-services-diagnostics-powershell.md)
#### [NET](cloud-services-dotnet-diagnostics.md)
#### [Visual Studio](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
### [Lagra och visa diagnostikdata i Azure Storage](cloud-services-dotnet-diagnostics-storage.md)
### [Spåra en molntjänst med diagnostik](cloud-services-dotnet-diagnostics-trace-flow.md)
### [Skicka diagnostikdata till App Insights](cloud-services-dotnet-diagnostics-applicationinsights.md)

## Felsöka
### Felsökning 
#### [Aktivera fjärrfelsökning med fortsatt leverans](cloud-services-virtual-machines-dotnet-continuous-delivery-remote-debugging.md)
#### [Alternativ för en molntjänst](../vs-azure-tools-debugging-cloud-services-overview.md)
#### [Lokal molntjänst med Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md)
#### [Publicerad molntjänst med Visual Studio](../vs-azure-tools-intellitrace-debug-published-cloud-services.md)
### [Tilldelningsfel i molntjänst](cloud-services-allocation-failures.md)
### [Vanliga orsaker till rollåteranvändning i molntjänst](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)
### [TEMP-standardmappen är för liten för en roll](cloud-services-troubleshoot-default-temp-folder-size-too-small-web-worker-role.md)
### [Vanliga distributionsproblem](cloud-services-troubleshoot-deployment-problems.md)
### [Rollen startades inte](cloud-services-troubleshoot-roles-that-fail-start.md)
### [Vägledning för återställning](cloud-services-disaster-recovery-guidance.md)
### Vanliga frågor och svar om Cloud Services
#### [Vanliga frågor och svar om program- och tjänsttillgänglighet](cloud-services-application-and-service-availability-faq.md)
#### [Vanliga frågor och svar om konfiguration och hantering](cloud-services-configuration-and-management-faq.md)
#### [Vanliga frågor och svar om anslutningar och nätverk](cloud-services-connectivity-and-networking-faq.md)
#### [Vanliga frågor och svar om distribution](cloud-services-deployment-faq.md)

# Referens
## [Kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=cloud-services)
## [.csdef XML-schema](schema-csdef-file.md)
### [LoadBalancerProbe-schema](schema-csdef-loadbalancerprobe.md)
### [WebRole-schema](schema-csdef-webrole.md)
### [WorkerRole-schema](schema-csdef-workerrole.md)
### [NetworkTrafficRules-schema](schema-csdef-networktrafficrules.md)
## [.cscfg XML-schema](schema-cscfg-file.md)
### [Rollschema](schema-cscfg-role.md)
### [NetworkConfiguration-schema](schema-cscfg-networkconfiguration.md)
## [REST](/rest/api/compute/cloudservices/)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=compute)
## [Utbildningsväg](https://azure.microsoft.com/documentation/learning-paths/cloud-services/)
## [MSDN-forum](https://social.msdn.microsoft.com/Forums/en-us/home?forum=windowsazuredevelopment)
## [Prissättning](https://azure.microsoft.com/pricing/details/cloud-services/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=cloud-services&updatetype=&platform=)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=cloud-services)
