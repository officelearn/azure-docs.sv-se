# Översikt

## [Vad är Service Bus-meddelanden?](service-bus-messaging-overview.md)
## [Service Bus-grunder](service-bus-fundamentals-hybrid-solutions.md)
## [Service Bus-arkitektur](service-bus-architecture.md)
## [Vanliga frågor och svar](service-bus-faq.md)

# Kom igång
## [Skapa ett namnområde](service-bus-create-namespace-portal.md)
## Använda köer
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Använda ämnen och prenumerationer
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)

# Gör så här för att
## Planera och designa
### [Hanterad tjänstidentitet (förhandsgranskning)](service-bus-managed-service-identity.md)
### [Rollbaserad åtkomstkontroll (förhandsgranskning)](service-bus-role-based-access-control.md)
### [Premium-meddelandetjänster](service-bus-premium-messaging.md)
### [Jämför Azure-köer och Service Bus-köer](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Skapa en Service Bus-flernivåapp](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)
### [Optimera prestanda](service-bus-performance-improvements.md)
### [Geo-haveriberedskap och geo-replikering](service-bus-geo-dr.md)
### [Asynkrona meddelanden och hög tillgänglighet](service-bus-async-messaging.md)
### [Hantera avbrott och katastrofer](service-bus-outages-disasters.md)

## Utveckla
### Meddelandehantering
#### [Köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
#### [Meddelanden, nyttolaster och serialisering](service-bus-messages-payloads.md)
#### [Överföringar av meddelanden, lås och uppgörelser](message-transfers-locks-settlement.md)
#### [Ordningsföljd och tidsstämplar för meddelanden](message-sequencing.md)
#### [Förfallodatum för meddelanden (Time to Live)](message-expiration.md)
### [Autentisering och auktorisering](service-bus-authentication-and-authorization.md)
#### [Migrera från ACS till SAS](service-bus-migrate-acs-sas.md)
#### [Autentisering med signaturer för delad åtkomst](service-bus-sas.md)
### [Ämnesfilter och åtgärder](topic-filters.md)
### [Partitionerade köer och ämnen](service-bus-partitioning.md)
### [Meddelandesessioner](message-sessions.md)
### AMQP
#### [Översikt över AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java-meddelandetjänst och AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [AMQP-protokollguide](service-bus-amqp-protocol-guide.md)
#### [Åtgärder som är baserade på begäran-svar med AMQP](service-bus-amqp-request-response.md)
### Avancerade funktioner
#### [Köer för obeställbara meddelanden](service-bus-dead-letter-queues.md)
#### [Förhämtning av meddelanden](service-bus-prefetch.md)
#### [Identifiering av dubbla meddelanden](duplicate-detection.md)
#### [Meddelanderäknare](message-counters.md)
#### [Skjut upp meddelanden](message-deferral.md)
#### [Bläddra i meddelanden](message-browsing.md)
#### [Chain-enheter med automatisk vidarebefordran](service-bus-auto-forwarding.md)
#### [Transaktionsbearbetning](service-bus-transactions.md)
#### [Implementering av kopplat namnområde](service-bus-paired-namespaces.md)
### [Slutpunkt till slutpunkt – spårning och diagnostik](service-bus-end-to-end-tracing.md)
## Hantera
### [Övervaka Service Bus med Azure-övervakning](service-bus-metrics-azure-monitor.md)
### [Service Bus-hanteringsbibliotek](service-bus-management-libraries.md)
### [Diagnostikloggar](service-bus-diagnostic-logs.md)
### [Skjut upp och återaktivera meddelandeentiteter](entity-suspend.md)
### [Använda Azure Resource Manager-mallar](service-bus-resource-manager-overview.md)
#### [Skapa ett namnområde](service-bus-resource-manager-namespace.md)
#### [Skapa ett namnområde och en kö](service-bus-resource-manager-namespace-queue.md)
#### [Skapa ett namnområde med ämne och prenumeration](service-bus-resource-manager-namespace-topic.md)
#### [Skapa en auktoriseringsregel för namnområde och kö](service-bus-resource-manager-namespace-auth-rule.md)
#### [Skapa ett namnområde med ämne, prenumeration och regel](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Etablera entiteter med Azure PowerShell](service-bus-manage-with-ps.md)

# Referens
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Undantag](service-bus-messaging-exceptions.md)
## [Kvoter](service-bus-quotas.md)
## [SQLFilter-syntax](service-bus-messaging-sql-filter.md)
## [SQLRuleAction-syntax](service-bus-messaging-sql-rule-action.md)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blogg](https://blogs.msdn.microsoft.com/servicebus/)
## [MSDN-forum](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Prissättning](https://azure.microsoft.com/pricing/details/service-bus/)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Prisinformation](service-bus-pricing-billing.md)
## [Exempel](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=service-bus)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


