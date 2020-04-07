---
title: Filnamnstillägg för resurstyper
description: Visar vilka Azure-resurstyper som används för att utöka funktionerna för andra resurstyper.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3a3fbc531750bec4b16e38f1fe79f613c1b94f5e
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754866"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resurstyper som utökar funktionerna för andra resurser

En tilläggsresurs är en resurs som lägger till en annan resurs funktioner. Resurslås är till exempel en tilläggsresurs. Du kan använda ett resurslås på en annan resurs för att förhindra att det tas bort eller ändras. Det är inte meningsfullt att skapa ett resurslås av sig själv. En tilläggsresurs tillämpas alltid på en annan resurs.

## <a name="extension-resource-types"></a>Filnamnstillägg för resurstyper

- Microsoft.Advisor/konfigurationer
- Microsoft.Advisor/rekommendationer
- Microsoft.Advisor/dämpningar
- Microsoft.AlertsManagement/aviseringar
- Microsoft.AlertsManagement/alertsSummary
- Microsoft.Auktorisering/checkTillgång
- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/findOrphanRoleAstilldelningar
- Microsoft.Auktorisering/lås
- Microsoft.Auktorisering/behörigheter
- Microsoft.Authorization/policyTilldelningar
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policySetDefinitions
- Tilldelning av Microsoft.Authorization/roleAstilldelningar
- Microsoft.Authorization/roleAssignmentsUsageMetrics
- Microsoft.Authorization/roleDefinitions
- Microsoft.Fakturering/faktureringPerioder
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAstilldelningar
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/ritningar
- Microsoft.Förbrukning/aggregeradcost
- Microsoft.Förbrukning/Saldon
- Microsoft.Förbrukning/Budget
- Microsoft.Förbrukning/avgifter
- Microsoft.Consumption/CostTags
- Microsoft.Förbrukning/Prognoser
- Microsoft.Förbrukning/Marknadsplatser
- Microsoft.Consumption/OperationResultat
- Microsoft.Consumption/OperationStatus
- Microsoft.Förbrukning/Prisblad
- Microsoft.Consumption/ReservationDetaljer
- Microsoft.Consumption/ReservationRekommendationer
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Förbrukning/ReservationTransactions
- Microsoft.Förbrukning/Taggar
- Microsoft.Förbrukning/Villkor
- Microsoft.Consumption/UsageDetails
- Microsoft.Förbrukning/krediter
- Microsoft.Förbrukning/händelser
- Microsoft.Förbrukning/lotter
- Microsoft.Förbrukning/produkter
- Microsoft.Förbrukning/klienter
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Aviseringar
- Microsoft.CostManagement/Budgetar
- Microsoft.CostManagement/Dimensioner
- Microsoft.CostManagement/Export
- Microsoft.CostManagement/ExternalSubscriptions Microsoft.CostManagement/ExternalSubscriptions Microsoft.CostManagement/ExternalSubscriptions Microsoft.
- Microsoft.CostManagement/Prognos
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Rapporter
- Microsoft.CostManagement/Visningar
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associationer
- Microsoft.EventGrid/eventAbonnemang
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software
- Microsoft.GuestConfiguration/softwareUpdateProfile
- Microsoft.GuestConfiguration/softwareUpdates
- microsoft.insights/baseline
- microsoft.insights/calculatebaseline microsoft.insights/calculatebaseline microsoft.insights/calculatebaseline microsoft.
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metricbaselines
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topologi
- microsoft.insights/transaktioner
- microsoft.insights/vmInsightsOnboardingStatuses
- Microsoft.KubernetesKonfiguration/sourceControlConfigurations
- Microsoft.Underhåll/applyUpdates
- Microsoft.Underhåll/configurationAstilldelningar
- Microsoft.Underhåll/uppdateringar
- Microsoft.ManagedIdentity/Identities Microsoft.ManagedIdentity/Identities Microsoft.ManagedIdentity/Identities Microsoft.
- Microsoft.ManagedServices/registrationAstilldelningar
- Microsoft.ManagedServices/registrationDefinitioner
- Microsoft.OperationalInsights/storageInsightConfigs Microsoft.OperationalInsights/storageInsightConfigs Microsoft.OperationalInsights/storageInsightConfigs Microsoft.
- Microsoft.OperationsManagement/managementassociations
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft.RecoveryServices/backupSkyddadeobjektanter
- Microsoft.RecoveryServices/replication-berättigandeResultat    
- Microsoft.ResourceHealth/availabilityStatuses
- Microsoft.ResourceHealth/childAvailabilityStatuses Microsoft.ResourceHealth/childAvailabilityStatuses Microsoft.ResourceHealth/childAvailabilityStatuses Microsoft.
- Microsoft.ResourceHealth/childResources Microsoft.ResourceHealth/childResources Microsoft.ResourceHealth/childResources Microsoft.
- Microsoft.ResourceHealth/events
- Microsoft.ResourceHealth/impactedResources Microsoft.ResourceHealth/impactedResources Microsoft.ResourceHealth/impactedResources Microsoft.
- Microsoft.ResourceHealth/meddelanden
- Microsoft.Resurser/länkar
- Microsoft.Resurser/taggar
- Microsoft.Säkerhet/efterlevnad
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHårningar
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/-utvärderingar
- Microsoft.Security/complianceResults Microsoft.Security/complianceResults Microsoft.Security/complianceResults Microsoft.
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInights/aggregeringar
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInights/alertRules
- Microsoft.SecurityInights/bokmärken
- Microsoft.SecurityInights/ärenden
- Microsoft.SecurityInights/dataConnectors
- Microsoft.SecurityIn-ljus/dataConnectorsCheckRequirements
- Microsoft.SecurityInights/entiteter
- Microsoft.SecurityInights/entityQueries
- Microsoft.SecurityInights/incidenter
- Microsoft.SecurityInights/officeConsents Microsoft.SecurityInights/officeConsents Microsoft.SecurityInights/officeConsents Microsoft.
- Microsoft.SecurityInights/inställningar
- Microsoft.SoftwarePlan/hybridAnvändningspassningar
- Microsoft.Subscription/CreateSubscription
- microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/komponenter
- Microsoft.WorkloadMonitor/monitorIntances
- Microsoft.WorkloadMonitor/monitorer
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Nästa steg

- Om du vill hämta resurs-ID:et för en tilläggsresurs i en Azure Resource Manager-mall använder du [tilläggetResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Ett exempel på hur du skapar en tilläggsresurs i en mall finns i [Event Grid Event Subscriptions](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
