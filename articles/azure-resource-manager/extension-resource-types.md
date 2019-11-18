---
title: Filnamnstillägg för resurstyper
description: Visar en lista över Azures resurs typer som används för att utöka funktionerna i andra resurs typer.
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 16dcd4cccea4445b6839ac639094d18e29ed49a9
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74147031"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resurs typer som utökar funktioner i andra resurser

En tilläggs resurs är en resurs som lägger till en annan resurss funktioner. Resurs låsning är till exempel en tilläggs resurs. Du kan använda ett resurs lås på en annan resurs för att förhindra att den tas bort eller ändras. Det är inte klokt att skapa ett resurs lås av sig självt. En tilläggs resurs används alltid på en annan resurs.

## <a name="extension-resource-types"></a>Filnamnstillägg för resurstyper

- Microsoft. Advisor/konfigurationer
- Microsoft. Advisor/rekommendationer
- Microsoft. Advisor/undertrycker
- Microsoft. AlertsManagement/Alerts
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/check Access
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/låsen
- Microsoft. Authorization/Permissions
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions
- Microsoft. fakturering/billingPeriods
- Microsoft. fakturering/billingPermissions
- Microsoft. fakturering/billingRoleAssignments
- Microsoft. fakturering/billingRoleDefinitions
- Microsoft. fakturering/createBillingRoleAssignment
- Microsoft. skiss/blueprintAssignments
- Microsoft. skiss/skisser
- Microsoft. förbrukning/AggregatedCost
- Microsoft. förbrukning/saldon
- Microsoft. förbrukning/budgetar
- Microsoft. förbrukning/avgifter
- Microsoft. förbrukning/CostTags
- Microsoft. förbrukning/prognoser
- Microsoft. förbrukning/Marketplace
- Microsoft. förbrukning/OperationResults
- Microsoft. förbrukning/OperationStatus
- Microsoft. förbrukning/Pricesheets
- Microsoft. förbrukning/ReservationDetails
- Microsoft. förbrukning/ReservationRecommendations
- Microsoft. förbrukning/ReservationSummaries
- Microsoft. förbrukning/ReservationTransactions
- Microsoft. förbrukning/Taggar
- Microsoft. förbrukning/villkor
- Microsoft. förbrukning/UsageDetails
- Microsoft. förbrukning/kredit
- Microsoft. förbrukning/händelser
- Microsoft. förbrukning/partier
- Microsoft. förbrukning/produkter
- Microsoft. förbrukning/klient organisationer
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/budgetar
- Microsoft. CostManagement/dimensions
- Microsoft. CostManagement/export
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Prediktion
- Microsoft. CostManagement/fråga
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/-rapporter
- Microsoft. CostManagement/vyer
- Microsoft. CostManagement/showbackRules
- Microsoft. CustomProviders/associationer
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/Software
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/automatedExportSettings
- Microsoft. Insights/bas linje
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/eventtypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/loggar
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/mått
- Microsoft. Insights/mina arbets böcker
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/uppdateringar
- Microsoft. ManagedIdentity/identiteter
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations
- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/Notifications
- Microsoft. Resources/Links
- Microsoft. Resources/Tags
- Microsoft. Security/Compliances
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/bedömningar
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/networkData
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. SecurityInsights/agg regeringar
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/bok märken
- Microsoft. SecurityInsights/fall
- Microsoft. SecurityInsights/dataConnectors
- Microsoft. SecurityInsights/entiteter
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/inställningar
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/createsupportticket
- Microsoft. support/supporttickets
- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/övervakare
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Nästa steg

- Om du vill hämta resurs-ID för en tilläggs resurs i en Azure Resource Manager mall använder du [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid).
- Ett exempel på hur du skapar en tilläggs resurs i en mall finns [Event Grid händelse prenumerationer](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
