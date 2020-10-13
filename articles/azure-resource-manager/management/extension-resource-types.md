---
title: Filnamnstillägg för resurstyper
description: Visar en lista över Azures resurs typer som används för att utöka funktionerna i andra resurs typer.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8b80c63d361f3ad8199fd669178f7bf88dabe02e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969739"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resurs typer som utökar funktioner i andra resurser

En tilläggs resurs är en resurs som lägger till en annan resurss funktioner. Resurs låsning är till exempel en tilläggs resurs. Du kan använda ett resurs lås på en annan resurs för att förhindra att den tas bort eller ändras. Det är inte klokt att skapa ett resurs lås av sig självt. En tilläggs resurs används alltid på en annan resurs.

## <a name="extension-resource-types"></a>Filnamnstillägg för resurstyper

- Microsoft. Advisor/konfigurationer
- Microsoft. Advisor/rekommendationer
- Microsoft. Advisor/undertrycker
- Microsoft. AlertsManagement/Alerts
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/accessReviewScheduleDefinitions
- Microsoft. Authorization/accessReviewScheduleSettings
- Microsoft. Authorization/check Access
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/findOrphanRoleAssignments
- Microsoft. Authorization/låsen
- Microsoft. Authorization/Permissions
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleAssignmentsUsageMetrics
- Microsoft. Authorization/roleDefinitions
- Microsoft. automanage/configurationProfileAssignments
- Microsoft. fakturering/billingPeriods
- Microsoft. fakturering/billingPermissions
- Microsoft. fakturering/billingRoleAssignments
- Microsoft. fakturering/billingRoleDefinitions
- Microsoft. fakturering/createBillingRoleAssignment
- Microsoft. skiss/blueprintAssignments
- Microsoft. skiss/skisser
- Microsoft. ChangeAnalysis/resourceChanges
- Microsoft. förbrukning/AggregatedCost
- Microsoft. förbrukning/saldon
- Microsoft. förbrukning/budgetar
- Microsoft. förbrukning/avgifter
- Microsoft. förbrukning/CostTags
- Microsoft. förbrukning/kredit
- Microsoft. förbrukning/händelser
- Microsoft. förbrukning/prognoser
- Microsoft. förbrukning/partier
- Microsoft. förbrukning/Marketplace
- Microsoft. förbrukning/OperationResults
- Microsoft. förbrukning/OperationStatus
- Microsoft. förbrukning/Pricesheets
- Microsoft. förbrukning/produkter
- Microsoft. förbrukning/ReservationDetails
- Microsoft. förbrukning/ReservationRecommendationDetails
- Microsoft. förbrukning/ReservationRecommendations
- Microsoft. förbrukning/ReservationSummaries
- Microsoft. förbrukning/ReservationTransactions
- Microsoft. förbrukning/Taggar
- Microsoft. förbrukning/klient organisationer
- Microsoft. förbrukning/villkor
- Microsoft. förbrukning/UsageDetails
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/budgetar
- Microsoft. CostManagement/costAllocationRules
- Microsoft. CostManagement/dimensions
- Microsoft. CostManagement/export
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/Prediktion
- Microsoft. CostManagement/Insights
- Microsoft. CostManagement/fråga
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/-rapporter
- Microsoft. CostManagement/showbackRules
- Microsoft. CostManagement/vyer
- Microsoft. CustomProviders/associationer
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/Software
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/bas linje
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/eventtypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/generateLiveToken
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/loggar
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/mått
- Microsoft. Insights/mina arbets böcker
- Microsoft. Insights/topologi
- Microsoft. Insights/transaktioner
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/Extensions
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/uppdateringar
- Microsoft. ManagedIdentity/identiteter
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/attesteringar
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/-reparationer
- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/Notifications
- Microsoft. Resources/Links
- Microsoft. Resources/Tags
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/bedömningar
- Microsoft. Security/complianceResults
- Microsoft. Security/Compliances
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments
- Microsoft. SecurityInsights/agg regeringar
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/bok märken
- Microsoft. SecurityInsights/fall
- Microsoft. SecurityInsights/dataConnectors
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/entiteter
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/incidenter
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/inställningar
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/supporttickets
- Microsoft. WorkloadMonitor/Components
- Microsoft. WorkloadMonitor/monitorInstances
- Microsoft. WorkloadMonitor/övervakare
- Microsoft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Nästa steg

- Om du vill hämta resurs-ID för en tilläggs resurs i en Azure Resource Manager mall använder du [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Ett exempel på hur du skapar en tilläggs resurs i en mall finns [Event Grid händelse prenumerationer](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).
