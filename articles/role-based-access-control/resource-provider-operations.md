---
title: Azure Resource Manager åtgärder för resursprovider | Microsoft Docs
description: Visar en lista över åtgärderna som är tillgängliga för Microsoft Azure Resource Manager-resursprovider
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 424995369ca0d993d78a5d5e4dd1e2b52a0d64d9
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428339"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Azure Resource Manager åtgärder för resursprovider

Den här artikeln visar en lista över åtgärderna som är tillgängliga för varje Azure Resource Manager-resursprovidern. Dessa åtgärder kan användas i [anpassade roller](custom-roles.md) att tillhandahålla detaljerade [rollbaserad åtkomstkontroll (RBAC)](overview.md) till resurser i Azure. Åtgärden strängar har följande format: `{Company}.{ProviderName}/{resourceType}/{action}`

Åtgärder för resursprovider är alltid under utveckling. Hämta de senaste åtgärderna [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) eller [az provider operation list](/cli/azure/provider/operation#az-provider-operation-list).

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AAD/domainServices/delete | Delete Domain Service |
> | Åtgärd | Microsoft.AAD/domainServices/oucontainer/delete | Ta bort Organisationsenhetsbehållaren |
> | Åtgärd | Microsoft.AAD/domainServices/oucontainer/read | Läsa Ou-behållare |
> | Åtgärd | Microsoft.AAD/domainServices/oucontainer/write | Skriva Organisationsenhetsbehållaren |
> | Åtgärd | Microsoft.AAD/domainServices/read | Read Domain Services |
> | Åtgärd | Microsoft.AAD/domainServices/replicaSets/delete | Ta bort klustret webbplats |
> | Åtgärd | Microsoft.AAD/domainServices/replicaSets/read | Läs Klusterplatser |
> | Åtgärd | Microsoft.AAD/domainServices/replicaSets/write | Skriva Klusterplatser |
> | Åtgärd | Microsoft.AAD/domainServices/write | Write Domain Service |
> | Åtgärd | Microsoft.AAD/locations/operationresults/read |  |
> | Åtgärd | Microsoft.AAD/Operations/read |  |
> | Åtgärd | Microsoft.AAD/register/action | Register Domain Service |
> | Åtgärd | Microsoft.AAD/unregister/action | Unregister Domain Service |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.aadiam/diagnosticsettings/delete | Tar bort en diagnostikinställning |
> | Åtgärd | microsoft.aadiam/diagnosticsettings/read | Läser en diagnostikinställning |
> | Åtgärd | microsoft.aadiam/diagnosticsettings/write | Skriva en diagnostikinställning |
> | Åtgärd | Microsoft.aadiam/diagnosticsettingscategories/Read | Läser en diagnostikinställning kategorier |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Addons/operations/read | Hämtar stöds RP-åtgärder. |
> | Åtgärd | Microsoft.Addons/register/action | Registrera den angivna prenumerationen med Microsoft Addons |
> | Åtgärd | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Visar aktuell information om supportavtalet för den angivna prenumerationen. |
> | Åtgärd | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Tar bort det angivna supportavtalet för Canonical |
> | Åtgärd | Microsoft.Addons/supportProviders/supportPlanTypes/read | Hämta den angivna Canonical-support-plan. |
> | Åtgärd | Microsoft.Addons/supportProviders/supportPlanTypes/write | Lägger till den angivna Canonical-support plantypen. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/action | Skapa en ny skog för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Hämtar alla servrar för det angivna tjänstnamnet. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/alerts/read | Hämtar information om aviseringar för skogen som alertid, varning har aktiverats datum, senaste identifierade, avisering aviseringsbeskrivningen, senaste uppdaterade sätt, på sätt, tillstånd, aviseringar felsökning länkar osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/configuration/read | Hämtar tjänstkonfigurationen för skogen. Exempel - skogsnamn, funktionsnivå, domäner master FSMO-rollen, FSMO schemahanterare osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/delete | Tar bort en tjänst och det är servrar tillsammans med data för hälsotillstånd. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Hämtar information om domäner och platser för skogen. Exempel-hälsostatus, aktiva aviseringar, lösta aviseringar, egenskaper som domänens funktionsnivå, skog, infrastrukturhanteraren, PDC och RID master osv.  |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Hämtar användarens inställning för skogen.<br>Example- MetricCounterName like ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Inställningar för Användargränssnittet diagram osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Hämtar skog sammanfattning för den angivna skogen som skogsnamn antalet domäner under den här skogen, antalet platser och platser information osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Hämtar listan över mått som stöds för en viss tjänst.<br>För exempel extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för AD FS-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomainService.<br>Kör profil svarstid, TCP-anslutningar upprättas, Insights agenten privata byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Med en tjänst kan hämtar detta API information för mått.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomain Service.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Detta API hämtar listan över alla integrerat ADDomainServices för en premium-klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/read | Hämtar information om tjänsten för det angivna tjänstnamnet. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Hämtar information om replikering för alla servrar för det angivna tjänstnamnet. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Hämtar antalet domänkontrollanter och deras replikeringsfel eventuellt. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Hämtar Slutför domain controller lista tillsammans med replikeringsinformationen för den angivna skogen. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Lägga till en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Denna api anropas under servern registrerades ADDomainService, för att hämta autentiseringsuppgifter för onboarding av nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Tar bort en server för en viss tjänst och klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/write | Skapar eller uppdaterar ADDomainService-instans för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/action | Klientkonfiguration för uppdateringar. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/read | Läser klient-konfigurering. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/write | Skapar en klientkonfiguration. |
> | Åtgärd | Microsoft.ADHybridHealthService/logs/contents/read | Hämtar innehållet i agentinstallation och registreringsloggar som lagras i blob. |
> | Åtgärd | Microsoft.ADHybridHealthService/logs/read | Hämtar agentinstallation och registrering loggfiler för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/operations/read | Hämtar lista över åtgärder som stöds av system. |
> | Åtgärd | Microsoft.ADHybridHealthService/register/action | Registrerar ADHybrid Health Service-Resursprovidern och gör möljligt ADHybrid-tjänsten för hälsotillstånd resurs. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Hämtar listan över tillgängliga regioner som används av DevOps för att stödja kundincidenter når. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/badpassword/read | Hämtar listan över försök med felaktigt lösenord för alla användare i Active Directory Federation Service. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Hämtar Blob SAS-URI som innehåller status och eventuell resultatet av nyligen köas rapporten jobb för frekvensen av felaktigt användarnamn/lösenord försöker per användar-ID per IP-adress per dag för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Hämtar listan över DevOps samtyckt klienter. Används vanligtvis för kundsupport. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/isdevops/read | Hämtar ett värde som anger om klienten är DevOps godkänt villkoren eller inte. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Uppdaterar userid(objectid) för den valda dev ops-klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Hämtar valda distributionen för den angivna klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Får ett klient-id hämtar klient-lagringsplats. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Hämtar geo-plats där data ska användas. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/action | Uppdaterar en tjänstinstans i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringar för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringar för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Beroende på en funktion som kontrollerar namn om en tjänst har allt som krävs för att använda funktionen. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/delete | Tar bort en instans av tjänsten på klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/exporterrors/read | Hämtar exportfel för en viss synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/exportstatus/read | Hämtar exporteringsstatus för för en viss tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Hämtar aviseringar feedback för en viss tjänst och server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metricmetadata/read | Hämtar listan över mått som stöds för en viss tjänst.<br>För exempel extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för AD FS-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomainService.<br>Kör profil svarstid, TCP-anslutningar upprättas, Insights agenten privata byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Med en tjänst kan hämtar detta API medelvärdet för mått för en viss tjänst.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomain Service.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/read | Med en tjänst kan hämtar detta API information för mått.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomain Service.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Med en tjänst kan hämtar detta API aggregerade vyn för mått för en viss tjänst.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomain Service.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Lägg till eller uppdaterar övervakningskonfigurationen för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Hämtar övervakningsinställningar för en viss tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Lägg till eller uppdaterar övervakningsinställningar för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/premiumcheck/read | Detta API hämtar listan över alla integrerade tjänster för en premium-klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/read | Läser tjänstinstanser i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Hämtar alla rapport för riskfyllda IP URI: er för de senaste 7 dagarna. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/details/read | Hämtar rapporten över upp 50 användare med felaktiga lösenord fel från de senaste 7 dagarna |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Rapport för riskfyllda IP-adresser och returnerar en URI som pekar till den. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/action | Skapar en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Läser aviseringarna för en server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Under server registration anropas denna api för att hämta autentiseringsuppgifter för onboarding av nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | För en viss server hämtar detta API en lista med datatyper som laddas av servrarna och den senaste tid för varje överföring. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/delete | Tar bort en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Hämtar exportera synkroniseringsfel-information för en viss tjänst för synkronisering. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Med en tjänst kan hämtar detta API information för mått.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totala för ADDomain Service.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Hämtar listan över kopplingar och körningsprofil namn för den angivna tjänsten och tjänsten medlem. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/read | Läser server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Hämtar tjänstkonfigurationen för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Hämtar lista över tillåtna funktionsstatus för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/write | Skapar en instans av tjänsten på klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/unregister/action | Avregistrerar prenumerationen för ADHybrid Health Service-Resursprovider. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Advisor/configurations/read | Hämta konfigurationer |
> | Åtgärd | Microsoft.Advisor/configurations/write | Konfiguration av skapar/uppdateringar |
> | Åtgärd | Microsoft.Advisor/generateRecommendations/action | Genererar rekommendationer |
> | Åtgärd | Microsoft.Advisor/generateRecommendations/read | Hämtar generera rekommendationer status |
> | Åtgärd | Microsoft.Advisor/metadata/read | Get Metadata |
> | Åtgärd | Microsoft.Advisor/operations/read | Hämtar åtgärderna för Microsoft Advisor |
> | Åtgärd | Microsoft.Advisor/recommendations/available/action | Ny rekommendation är tillgängligt i Microsoft advisor |
> | Åtgärd | Microsoft.Advisor/recommendations/read | Läser rekommendationer |
> | Åtgärd | Microsoft.Advisor/recommendations/suppressions/delete | Tar bort Undertryckning |
> | Åtgärd | Microsoft.Advisor/recommendations/suppressions/read | Hämtar suppressions |
> | Åtgärd | Microsoft.Advisor/recommendations/suppressions/write | Skapat/uppdaterat suppressions |
> | Åtgärd | Microsoft.Advisor/register/action | Registrerar prenumerationen för Microsoft Advisor |
> | Åtgärd | Microsoft.Advisor/suppressions/delete | Tar bort Undertryckning |
> | Åtgärd | Microsoft.Advisor/suppressions/read | Hämtar suppressions |
> | Åtgärd | Microsoft.Advisor/suppressions/write | Skapat/uppdaterat suppressions |
> | Åtgärd | Microsoft.Advisor/unregister/action | Avregistrerar prenumerationen för Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AlertsManagement/actionRules/delete | Ta bort åtgärdsregeln för i en viss prenumeration. |
> | Åtgärd | Microsoft.AlertsManagement/actionRules/read | Hämta alla Åtgärdsregler för inkommande trafik. |
> | Åtgärd | Microsoft.AlertsManagement/actionRules/write | Skapa eller uppdatera åtgärdsregel i en viss prenumeration |
> | Åtgärd | Microsoft.AlertsManagement/alerts/changestate/action | Ändra status för aviseringen. |
> | Åtgärd | Microsoft.AlertsManagement/alerts/diagnostics/read | Hämta alla diagnostik för aviseringen |
> | Åtgärd | Microsoft.AlertsManagement/alerts/history/read | Hämta historiken för aviseringen |
> | Åtgärd | Microsoft.AlertsManagement/alerts/read | Hämta alla aviseringar för inkommande trafik. |
> | Åtgärd | Microsoft.AlertsManagement/alertsList/read | Hämta alla aviseringar för inkommande trafik mellan prenumerationer |
> | Åtgärd | Microsoft.AlertsManagement/alertsSummary/read | Få en sammanfattning av aviseringar |
> | Åtgärd | Microsoft.AlertsManagement/alertsSummaryList/read | Få en sammanfattning av aviseringar i olika prenumerationer |
> | Åtgärd | Microsoft.AlertsManagement/Operations/read | Läser de åtgärder som anges |
> | Åtgärd | Microsoft.AlertsManagement/register/action | Registrerar prenumerationen för aviseringar för Microsoft Management |
> | Åtgärd | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Ta bort Smart detektor varningsregel för en viss prenumeration |
> | Åtgärd | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Hämta alla Smart detektor avisering regler för inkommande trafik |
> | Åtgärd | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Skapa eller uppdatera Smart detektor varningsregel för en viss prenumeration |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/changestate/action | Ändra tillstånd för gruppen smart |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/history/read | Hämta historiken i gruppen smart |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/read | Hämta alla smart grupper för inkommande trafik |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Kontrollerar att namnet givna Analysis Server är giltigt och inte används. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationresults/read | Hämtar information om det angivna åtgärdsresultatet. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationstatuses/read | Hämtar information om den angivna åtgärdsstatusen. |
> | Åtgärd | Microsoft.AnalysisServices/operations/read | Hämtar information om åtgärder |
> | Åtgärd | Microsoft.AnalysisServices/register/action | Registrerar Analysis Services-resursprovidern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/delete | Tar bort analysservern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Lista statusen för gatewayen som är associerade med servern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/read | Hämtar information om den angivna analysservern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/resume/action | Återupptar analysservern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/skus/read | Hämta tillgänglig SKU-information för servern |
> | Åtgärd | Microsoft.AnalysisServices/servers/suspend/action | Inaktiverar analysservern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/write | Skapar eller uppdaterar den angivna analysservern. |
> | Åtgärd | Microsoft.AnalysisServices/skus/read | Hämtar information om SKU: er |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ApiManagement/checkNameAvailability/read | Kontrollerar om tjänstens namn är tillgängligt |
> | Åtgärd | Microsoft.ApiManagement/operations/read | Läs alla API-åtgärder tillgängliga för Microsoft.ApiManagement resursen |
> | Åtgärd | Microsoft.ApiManagement/register/action | Registrera prenumeration för resursprovidern för Microsoft.ApiManagement |
> | Åtgärd | Microsoft.ApiManagement/reports/read | Hämta rapporter sammanställs efter tidsperioder, geografisk region, utvecklare, produkter, API: er, åtgärder, prenumeration och byRequest. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/delete | Tar bort den angivna API i API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/delete | Tar bort angivna diagnostiken från ett API. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/read | Visar en lista över alla diagnostik i ett API. eller hämtar information om diagnostik för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/write | Skapar en ny diagnostik för ett API eller uppdaterar en befintlig. eller uppdateringar information om diagnostik för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Tar bort den angivna kommentaren från ett problem. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/read | Visar alla bifogade filer för problemet som är associerade med den angivna API. eller hämtar information om problemet bifogad fil för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/write | Skapar en ny bifogad fil på problem i ett API eller uppdaterar en befintlig. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/delete | Tar bort den angivna kommentaren från ett problem. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/read | Visar alla kommentarer om problemet som är associerade med den angivna API. eller hämtar information om problemet kommentera för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/write | Skapar en ny kommentar för problemet i ett API eller uppdaterar en befintlig. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/delete | Tar bort angivna problemet från ett API. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/read | Visar en lista över alla problem som är associerade med den angivna API. eller hämtar information om problemet för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/write | Skapar ett nytt ärende för ett API eller uppdaterar en befintlig. eller uppdaterar en befintlig utfärda för ett API. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/delete | Tar bort den angivna åtgärden i API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/delete | Tar bort principkonfigurationen på Api-åtgärden. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/read | Hämta listan över principkonfigurationen på nivån API-åtgärden. eller skaffa principkonfigurationen på nivån API-åtgärden. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/write | Skapar eller uppdaterar konfiguration för API-åtgärden-nivå. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/delete | Ta bort principkonfigurationen på åtgärdsnivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/read | Hämta principkonfigurationen på åtgärdsnivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/write | Skapa konfiguration vid drift nivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/read | Visar en samling åtgärder för den angivna API. eller hämtar information om API-åtgärden som specificerats av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/delete | Koppla bort taggen från åtgärd. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/read | Visar en lista över alla taggar som är associerade med åtgärden. eller hämta tagg som är associerade med åtgärden. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/write | Tilldela åtgärden taggen. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/write | Skapar en ny åtgärd i API: et eller uppdaterar en befintlig. eller uppdateringar information om åtgärd i API: et anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operationsByTags/read | Visar en uppsättning åtgärder som är associerade med taggar. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/delete | Tar bort principkonfigurationen på API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/read | Hämta principkonfigurationen på API-nivå. eller skaffa principkonfigurationen på API-nivå. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/write | Skapar eller uppdaterar konfiguration för API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/delete | Ta bort principkonfigurationen på API-nivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/read | Hämta principkonfigurationen vid API-nivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/write | Skapa principkonfigurationen på API-nivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/products/read | Visar en lista över alla produkter som API: et är en del av. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/read | Visar en lista över alla API: er för API Management-tjänstinstans. eller hämtar information om API: et som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/delete | Tar bort alla versioner av API: et eller tar bort den angivna versionen i API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/read | Visar en lista över alla versioner av ett API.<br>En API-version skapas när du gör en API-revidering aktuella.<br>Versioner används också för att återställa till tidigare revisioner.<br>Resultatet kommer att växla och kan begränsas av parametrarna $top och $skip.<br>eller returnerar information om ett API-versionen. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/write | Skapar en ny version för API: et. eller uppdateringar information om versionen av API: et anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/revisions/delete | Tar bort alla revisioner av ett API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/revisions/read | Visar en lista över alla revisioner av ett API. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/delete | Tar bort schemakonfiguration på API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/read | Hämta schema-konfigurationen på API-nivå. eller skaffa schema-konfigurationen på API-nivå. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/write | Skapar eller uppdaterar schemat konfigurationen för API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Ta bort taggen beskrivning för API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Visar alla taggar beskrivningar i omfattningen av API: et. Modellera liknar swagger - tagDescription har definierats för API-nivå men tagg kan tilldelas till åtgärder eller tagg beskrivning av Erhåll i omfånget för API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Skapa/uppdatera taggen beskrivning i omfattningen av API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/delete | Koppla bort taggen från API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/read | Visar en lista över alla taggar som är associerade med API: et. eller hämta tagg som är associerade med API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/write | Tilldela tagg-API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/write | Skapar ny eller uppdaterar befintliga angiven API: et för API Management-tjänstinstans. eller uppdaterar den angivna API i API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/apisByTags/read | Visar en uppsättning API: er som är associerade med taggar. |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/delete | Tar bort specifika Api-versionen och inställd. |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/read | Visar en samling med API-versionen i den angivna tjänstinstansen. eller hämtar information om den Api-Version som Set angivna efter dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Hämta lista över version entiteter |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/write | Skapar eller uppdaterar en uppsättning Api-versionen. eller uppdateringar information om Api-VersionSet som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Uppdaterar de Microsoft.ApiManagement-resurser som körs i virtuella nätverk för att hämta uppdaterade nätverksinställningar. |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/delete | Tar bort specifika auktorisering server-instansen. |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/read | Visar en lista över en mängd auktorisering servrar definierats inom en tjänstinstans. eller hämtar information om auktoriseringsservern som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/write | Skapar ny auktoriseringsservern eller uppdaterar en befintlig auktoriseringsservern. eller uppdateringar information om auktoriseringsservern som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/backends/delete | Tar bort den angivna serverdelen. |
> | Åtgärd | Microsoft.ApiManagement/service/backends/read | Visar en uppsättning serverdelar i den angivna tjänstinstansen. eller hämtar information om serverdelen som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/backends/reconnect/action | Meddelar APIM-proxy för att skapa en ny anslutning till serverdelen efter en angiven tidsgräns. Om ingen tidsgräns har angetts används tidsgränsen 2 minuter. |
> | Åtgärd | Microsoft.ApiManagement/service/backends/write | Skapar eller uppdaterar en serverdel. eller uppdaterar en befintlig serverdel. |
> | Åtgärd | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiering API Management-tjänsten till den angivna behållaren i en användare tillhandahålls storage-konto |
> | Åtgärd | Microsoft.ApiManagement/service/caches/delete | Tar bort specifika Cache. |
> | Åtgärd | Microsoft.ApiManagement/service/caches/read | Visar en samling med alla externa cacheminnen i den angivna tjänstinstansen. eller hämtar information om cachen som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/caches/write | Skapar eller uppdaterar en extern Cache som ska användas i Api Management-instans. eller uppdateringar information om cachen som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/delete | Tar bort specifika certifikat. |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/read | Visar en samling med alla certifikat i den angivna tjänstinstansen. eller hämtar information om certifikatet som anges efter dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/write | Skapar eller uppdaterar det certifikat som används för autentisering med serverdelen. |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Tar bort angivna innehållsobjektet. |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Returnerar listan över innehållsposter eller returnerar innehållsobjekt detaljer |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Skapar nytt innehåll objekt eller uppdaterar angivna innehållsobjekt |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/read | Returnerar lista över typer av innehåll |
> | Åtgärd | Microsoft.ApiManagement/service/delete | Ta bort instansen av tjänsten API Management |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/delete | Tar bort angivna diagnostiken. |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/read | Visar en lista över alla diagnostik av API Management-tjänstinstans. eller hämtar information om diagnostiken som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/write | Skapar en ny diagnostik eller uppdaterar en befintlig. eller uppdateringar information om diagnostiken som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/getssotoken/action | Hämtar SSO-token som kan användas för att logga in i API Management-tjänsten äldre portal som administratör |
> | Åtgärd | Microsoft.ApiManagement/service/groups/delete | Tar bort viss grupp av API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/groups/read | Visar en uppsättning grupper som definierats i en tjänstinstans. eller hämtar information om den grupp som har angetts efter dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/delete | Ta bort befintliga användare från befintlig grupp. |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/read | Visar en samling användarentiteter som är associerade med gruppen. |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/write | Lägga till befintliga användare till befintlig grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/write | Skapar eller uppdaterar en grupp. eller uppdateringar information om den grupp som har angetts efter dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/delete | Tar bort providerkonfigurationen angiven identitet. |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/read | Visar en uppsättning identitetsleverantör har konfigurerats i den angivna tjänstinstansen. eller hämtar konfigurationsinformationen för identiteten providern som konfigurerats i angivna tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/write | Skapar eller uppdaterar IdentityProvider konfigurationen. eller uppdaterar en befintlig IdentityProvider-konfiguration. |
> | Åtgärd | Microsoft.ApiManagement/service/issues/read | Visar en samling av problemen i den angivna tjänstinstansen. eller hämtar API Management Probleminformation |
> | Åtgärd | Microsoft.ApiManagement/service/locations/networkstatus/read | Hämtar nätverksstatus för åtkomst av resurser där tjänsten är beroende på plats. |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/delete | Tar bort den angivna loggaren. |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/read | Visar en samling av tangenttryckningar i den angivna tjänstinstansen. eller hämtar information om loggaren som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/write | Skapar eller uppdaterar en loggare. eller uppdaterar en befintlig loggaren. |
> | Åtgärd | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort de regionala distributionerna av API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/networkstatus/read | Hämtar nätverksstatus för åtkomst av resurser där tjänsten är beroende av. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/action | Skickar meddelande till en angiven användare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/read | Visar en lista över egenskaper som definierats i en tjänstinstans. eller hämtar information om meddelandet som anges efter dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Tar bort e-postmeddelandet från listan över meddelanden. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Hämtar listan över de e-postaviseringar om mottagaren prenumerera på ett meddelande. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Lägger till den e-postadressen i listan över mottagare för meddelandet. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Tar bort API Management-användaren i listan med meddelanden. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Hämtar listan över mottagare Aviseringsanvändare prenumerera på meddelandet. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Lägger till API Management-användaren i listan över mottagare för meddelandet. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/write | Skapa eller uppdatera API Management publisher-meddelande. |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/delete | Tar bort specifika OpenID Connect-providern för API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/read | Listor över alla de OpenId Connect-providern. eller hämtar specifika OpenID Connect-providern. |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/write | Skapar eller uppdaterar OpenID Connect-providern. eller uppdateringar för den specifika OpenID Connect-providern. |
> | Åtgärd | Microsoft.ApiManagement/service/operationresults/read | Hämtar aktuell status för långvarig åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/policies/delete | Tar bort den globala principkonfigurationen av Api Management-tjänsten. |
> | Åtgärd | Microsoft.ApiManagement/service/policies/read | Visar en lista över alla globala principdefinitioner i Api Management-tjänsten. eller skaffa Global principdefinitionen av Api Management-tjänsten. |
> | Åtgärd | Microsoft.ApiManagement/service/policies/write | Skapar eller uppdaterar den globala principkonfigurationen av Api Management-tjänsten. |
> | Åtgärd | Microsoft.ApiManagement/service/policy/delete | Ta bort principkonfigurationen på klientnivån |
> | Åtgärd | Microsoft.ApiManagement/service/policy/read | Hämta principkonfigurationen på klientnivån |
> | Åtgärd | Microsoft.ApiManagement/service/policy/write | Skapa konfiguration på klient nivå |
> | Åtgärd | Microsoft.ApiManagement/service/policySnippets/read | Visar en lista över alla principkodavsnitt. |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/read | Få inloggning i inställningarna för portalen eller Get logga inställningar för portalen eller hämta delegeringsinställningar för portalen. |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/write | Uppdatera inställningarna för inloggning. eller skapa eller uppdatera logga In. eller uppdatera registrera dig inställningar eller uppdatering registrera dig inställningar eller uppdatera Delegeringsinställningarna. eller skapa eller uppdatera delegering inställningar. |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/delete | Tar bort den angivna API från den angivna produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/read | Visar en samling API: er som är associerade med en produkt. |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/write | Lägger till ett API till den angivna produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/delete | Ta bort produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/delete | Tar bort kopplingen mellan den angivna gruppen och produkt. |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/read | Visar en lista över insamlingen av utvecklare grupper som är associerade med den angivna produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/write | Lägger till kopplingen mellan angivna developer-gruppen med den angivna produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/delete | Tar bort principkonfigurationen i produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/read | Hämta principkonfigurationen på produktnivå. eller skaffa principkonfigurationen på produktnivå. |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/write | Skapar eller uppdaterar konfiguration för produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/delete | Ta bort principkonfigurationen på produktnivå |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/read | Hämta principkonfigurationen på produktnivå |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/write | Skapa konfiguration på produkten nivå |
> | Åtgärd | Microsoft.ApiManagement/service/products/read | Visar en samling av produkter i den angivna tjänstinstansen. eller hämtar information om den produkt som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/products/subscriptions/read | Visar en lista över insamlingen av prenumerationer på den angivna produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/delete | Koppla bort taggen från produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/read | Visar en lista över alla taggar som är associerade med produkten. eller hämta tagg som är associerade med produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/write | Tilldela tagg till produkten. |
> | Åtgärd | Microsoft.ApiManagement/service/products/write | Skapar eller uppdaterar en produkt. eller uppdatera befintliga produktinformation. |
> | Åtgärd | Microsoft.ApiManagement/service/productsByTags/read | Visar en uppsättning produkter som är associerade med taggar. |
> | Åtgärd | Microsoft.ApiManagement/service/properties/delete | Tar bort viss egenskap från API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/properties/read | Visar en lista över egenskaper som definierats i en tjänstinstans. eller hämtar information om egenskapen som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/properties/write | Skapar eller uppdaterar en egenskap. eller uppdaterar den specifika egenskapen. |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/periods/read | Hämta räknarvärde kvot för perioden |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/periods/write | Ange kvot aktuella räknarvärdet |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/read | Hämta de värden för kvot |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/write | Ange kvot aktuella räknarvärdet |
> | Åtgärd | Microsoft.ApiManagement/service/read | Läsa metadata för en API Management-tjänstinstans |
> | Åtgärd | Microsoft.ApiManagement/service/regions/read | Visar en lista över alla azure-regioner där tjänsten finns. |
> | Åtgärd | Microsoft.ApiManagement/service/reports/read | Hämta rapport sammanställs efter tidsperioder eller Get-rapport som sammanställs efter geografiskt område eller Get-rapport som aggregeras av utvecklare.<br>eller hämta rapport sammanställs av produkter.<br>eller hämta rapport aggregeras av API: er eller Get rapporten aggregeras av driftsupport- eller Get-rapport som aggregeras av prenumerationen.<br>eller Get-begäranden som rapporterar data |
> | Åtgärd | Microsoft.ApiManagement/service/restore/action | Återställa API Management-tjänsten från den angivna behållaren i en användare som tillhandahålls av storage-konto |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/delete | Tar bort den angivna prenumerationen. |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/read | Visar en lista över alla prenumerationer för API Management-tjänstinstans. Hämtar den angivna prenumeration entiteten eller. |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Återskapar primär nyckel för befintlig prenumeration av API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Återskapar sekundär nyckel för befintlig prenumeration av API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/write | Skapar eller uppdaterar prenumerationen för angiven användare till den angivna produkten. eller uppdateringar information om en prenumeration som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/tagResources/read | Visar en samling resurser som är associerade med taggar. |
> | Åtgärd | Microsoft.ApiManagement/service/tags/delete | Tar bort specifika taggen för API Management-tjänstinstans. |
> | Åtgärd | Microsoft.ApiManagement/service/tags/read | Visar en samling taggar som definierats i en tjänstinstans. eller hämtar information om taggen som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/tags/write | Skapar en tagg. eller uppdateringar information om taggen som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/templates/delete | Återställ standard API Management e-postmall |
> | Åtgärd | Microsoft.ApiManagement/service/templates/read | Hämtar alla e-postmallar eller hämtar API Management e-mallinformationen |
> | Åtgärd | Microsoft.ApiManagement/service/templates/write | Skapa eller uppdatera e-postmall som API Management eller uppdaterar API Management e-postmall |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/delete | Ta bort konfiguration för klienten |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/deploy/action | Kör en distributionsuppgift för att tillämpa ändringar från den angivna git-grenen till konfigurationen i databasen. |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/operationResults/read | Hämta lista över Åtgärdsresultat eller hämta resultatet av en viss åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/read | Få Global principdefinitionen av Api Management-tjänsten. eller Get klientinformation åtkomst information |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Återskapa primär åtkomstnyckel |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Återskapa sekundära åtkomstnyckeln |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/save/action | Skapar commit med ögonblicksbilden av webbplatskonfigurationen till den angivna förgreningen i databasen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/syncState/read | Hämta status för den senaste synkroniseringen av git |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/validate/action | Verifierar ändringar från den angivna git-grenen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/write | Ange konfiguration för klient- eller Update klientinformation åtkomst information |
> | Åtgärd | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikat för en API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/users/action | Registrera en ny användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/confirmations/send/action | Skickar en bekräftelse |
> | Åtgärd | Microsoft.ApiManagement/service/users/delete | Tar bort specifika användare. |
> | Åtgärd | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Hämtar en omdirigerings-URL som innehåller en Autentiseringstoken för en viss användare loggar in på utvecklarportalen. |
> | Åtgärd | Microsoft.ApiManagement/service/users/groups/read | Visar en lista över alla användargrupper. |
> | Åtgärd | Microsoft.ApiManagement/service/users/identities/read | Lista över alla användaridentiteter. |
> | Åtgärd | Microsoft.ApiManagement/service/users/keys/read | Hämta nycklar som är associerade med användaren |
> | Åtgärd | Microsoft.ApiManagement/service/users/read | Visar en uppsättning registrerade användare i den angivna tjänstinstansen. eller hämtar information om användare som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/users/subscriptions/read | Visar en lista över insamlingen av prenumerationer för den angivna användaren. |
> | Åtgärd | Microsoft.ApiManagement/service/users/token/action | Hämtar tillståndet för delad åtkomst-Token för användaren. |
> | Åtgärd | Microsoft.ApiManagement/service/users/write | Skapar eller uppdaterar en användare. eller uppdateringar information om den användare som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/write | Skapa en ny instans av tjänsten API Management |
> | Åtgärd | Microsoft.ApiManagement/unregister/action | Avregistrera prenumerationen för Microsoft.ApiManagement resursprovidern |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/delete | Tar bort administratören från prenumerationen. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Hämtar status för administratörsåtgärder för prenumerationen. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/read | Läser in prenumerationens administratörer. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/write | Lägg till eller ta bort en administratör till en prenumeration. |
> | Åtgärd | Microsoft.Authorization/denyAssignments/delete | Ta bort en neka-tilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/denyAssignments/read | Få information om en neka-tilldelning. |
> | Åtgärd | Microsoft.Authorization/denyAssignments/write | Skapa en neka-tilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/elevateAccess/action | Ger anroparen åtkomst för administratör för användaråtkomst klientomfattningen |
> | Åtgärd | Microsoft.Authorization/locks/delete | Ta bort lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/locks/read | Hämtar lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/locks/write | Lägg till lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/operations/read | Hämtar listan över åtgärder |
> | Åtgärd | Microsoft.Authorization/permissions/read | Listar alla behörigheter som anroparen har i ett givet omfång. |
> | Åtgärd | Microsoft.Authorization/policyAssignments/delete | Ta bort en principtilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/policyAssignments/read | Få information om en principtilldelning. |
> | Åtgärd | Microsoft.Authorization/policyAssignments/write | Skapa en principtilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/policyDefinitions/delete | Ta bort en principdefinition. |
> | Åtgärd | Microsoft.Authorization/policyDefinitions/read | Få information om en principdefinition. |
> | Åtgärd | Microsoft.Authorization/policyDefinitions/write | Skapa en anpassad principdefinition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/delete | Ta bort en principuppsättningsdefinition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/read | Få information om en principuppsättningsdefinition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/write | Skapa en anpassad principuppsättningsdefinition. |
> | Åtgärd | Microsoft.Authorization/providerOperations/read | Hämta åtgärder för alla providrar som kan användas i rolldefinitioner. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/delete | Ta bort en rolltilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/read | Få information om en rolltilldelning. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/write | Skapa en rolltilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/delete | Ta bort den angivna anpassa rolldefinitionen. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/read | Få information om en rolldefinition. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/write | Skapa eller uppdatera en anpassad rolldefinition med angivna behörigheter och tilldelningsbara scope. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Läs en Azure Automation DSC registreringsinformation |
> | Åtgärd | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Skriver en begäran om att återskapa nycklar för Azure Automation DSC |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/delete | Tar bort en Azure Automation-certifikattillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/getCount/action | Läser antalet certifikat |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/read | Hämtar en Azure Automation-certifikattillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/write | Skapar eller uppdaterar en Azure Automation-certifikattillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/read | Läser en Azure Automation DSCs kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/read | Läser en Azure Automation DSCs kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/write | Skriver ett Azure Automation DSCs kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/write | Skriver ett Azure Automation DSCs kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/content/read | Läser konfiguration av medieinnehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/delete | Tar bort en Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/getCount/action | Läser antalet i ett Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/read | Hämtar en Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/write | Skriver ett Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/delete | Tar bort en Azure Automation-anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/getCount/action | Läser antalet anslutningar |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/read | Hämtar en Azure Automation-anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/write | Skapar eller uppdaterar en Azure Automation-anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connectionTypes/delete | Tar bort en Azure Automation-typ anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connectionTypes/read | Hämtar en Azure Automation-typ anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connectionTypes/write | Skapar en tillgång av typen Azure Automation-anslutning |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/delete | Tar bort en Azure Automation-autentiseringsuppgiftstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/getCount/action | Läser antalet autentiseringsuppgifter |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/read | Hämtar en Azure Automation-autentiseringsuppgiftstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/write | Skapar eller uppdaterar en Azure Automation-autentiseringsuppgiftstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/delete | Tar bort ett Azure Automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Deletes Hybrid Runbook Worker Resources |
> | Åtgärd | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker-resurser |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/output/read | Hämtar utdata för ett jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/read | Hämtar en Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/runbookContent/action | Hämtar innehållet i Azure Automation-runbook vid tidpunkten för jobbkörningen |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation-jobbström |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation-jobbström |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobSchedules/delete | Tar bort ett Azure Automation-Jobbschema |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation-Jobbschema |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobSchedules/write | Skapar ett Azure Automation-Jobbschema |
> | Åtgärd | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar den arbetsyta som är länkad till automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/listKeys/action | Läser nycklarna för automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/activities/read | Hämtar Azure Automation-aktiviteter |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/delete | Tar bort en Azure Automation Powershell-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/getCount/action | Hämtar antalet Powershell-moduler i Automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/read | Hämtar en Azure Automation Powershell-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/write | Skapar eller uppdaterar en Azure Automation Powershell-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Tar bort en Azure Automation DSC-nodkonfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Läser innehåll i en Azure Automation DSC-nod |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Läser en Azure Automation DSC-nodkonfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Skriver ett Azure Automation DSC-nodkonfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodecounts/read | Läser nodantal sammanfattning för den angivna typen |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/delete | Tar bort Azure Automation DSC-noder |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/read | Läser Azure Automation DSC-noder |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Läser innehåll i Azure Automation DSC |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/reports/read | Läser Azure Automation DSC-rapporter |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/write | Skapar eller uppdaterar Azure Automation DSC-noder |
> | Åtgärd | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Hämtar Azure Automation TypeFields |
> | Åtgärd | Microsoft.Automation/automationAccounts/python2Packages/delete | Tar bort ett Azure Automation Python 2-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python2Packages/read | Hämtar ett Azure Automation Python 2-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python2Packages/write | Skapar eller uppdaterar ett Azure Automation Python 2-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python3Packages/delete | Tar bort ett Azure Automation Python 3-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python3Packages/read | Hämtar ett Azure Automation Python 3-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python3Packages/write | Skapar eller uppdaterar ett Azure Automation Python 3-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/content/read | Hämtar innehållet i en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/delete | Tar bort en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Skapar innehållet i ett utkast till Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Hämtar Azure Automation-runbook draft Åtgärdsresultat |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/read | Hämtar ett utkast till Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Hämtar ett testjobb för Azure Automation runbook utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Återupptar ett testjobb för Azure Automation runbook utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Stoppar ett testjobb för Azure Automation runbook utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Pausar ett testjobb för Azure Automation runbook utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Skapar ett testjobb för Azure Automation runbook utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Ångra ändringar i ett utkast till Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Hämtar antalet i Azure Automation-runbooks |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publicerar ett utkast till Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/write | Skapar eller uppdaterar en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/delete | Tar bort en Azure Automation-schematillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/getCount/action | Hämtar antalet scheman för Azure Automation |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation-schematillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation-schematillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Tar bort en Azure Automation-Uppdateringskonfigurationen |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Hämtar en Azure Automation-Uppdateringskonfigurationen |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Skapar eller uppdaterar Uppdateringskonfigurationen för Azure Automation |
> | Åtgärd | Microsoft.Automation/automationAccounts/statistics/read | Hämtar Azure Automation-statistik |
> | Åtgärd | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Hämta en Azure Automation datorn för distribution av uppdatering |
> | Åtgärd | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Hämtar en Azure Automation update management patch-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/usages/read | Hämtar Azure Automation-användning |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/delete | Tar bort en Azure Automation-variabeltillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/read | Läser en Azure Automation-variabeltillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/write | Skapar eller uppdaterar en Azure Automation-variabeltillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/delete | Ta bort ett Azure Automation watcher-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/read | Hämtar en Azure Automation watcher jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/start/action | Starta en watcher Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/stop/action | Stoppa ett Azure Automation watcher-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/streams/read | Hämtar en Azure Automation watcher jobbström |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Ta bort en Azure Automation watcher jobbåtgärder |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Hämtar en Azure Automation-watcher jobbåtgärder |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Skapa en Azure Automation-watcher jobbåtgärder |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/write | Skapar ett Azure Automation watcher-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/action | Genererar en URI för en Azure Automation-webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/delete | Tar bort en Azure Automation-webhook  |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/read | Läser en Azure Automation-webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/write | Skapar eller uppdaterar en Azure Automation-webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/write | Skapar eller uppdaterar ett Azure Automation-konto |
> | Åtgärd | Microsoft.Automation/operations/read | Hämtar tillgängliga åtgärder för Azure Automation-resurser |
> | Åtgärd | Microsoft.Automation/register/action | Registrerar prenumerationen på Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Ta bort B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visa B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/write | Skapa eller uppdatera en B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2ctenants/read | Visar en lista över alla B2C-klienter där användaren är medlem |
> | Åtgärd | Microsoft.AzureActiveDirectory/operations/read | Läs alla API-åtgärder tillgängliga för resursprovidern Microsoft.AzureActiveDirectory |
> | Åtgärd | Microsoft.AzureActiveDirectory/register/action | Registrera prenumeration för resursprovidern Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AzureStack/Operations/read | Hämtar egenskaperna för en resource provider-åtgärd |
> | Åtgärd | Microsoft.AzureStack/register/action | Registrerar prenumerationen med Microsoft.AzureStack-resursprovidern |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Tar bort en prenumeration för Azure Stack-kund |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/read | Hämtar egenskaperna för en Kundprenumeration för Azure Stack |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/write | Skapar eller uppdaterar en Kundprenumeration för Azure Stack |
> | Åtgärd | Microsoft.AzureStack/registrations/delete | Tar bort en Azure Stack-registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/getActivationKey/action | Hämtar senaste aktiveringsnyckeln för Azure Stack |
> | Åtgärd | Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökade informationen för en produkt i Azure Stack Marketplace |
> | Åtgärd | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en produkt i Azure Stack Marketplace |
> | Åtgärd | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure Stack-registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/write | Skapar eller uppdaterar en Azure Stack-registrering |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/delete | Tar bort ett program |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/read | Visar en lista över program eller hämtar egenskaperna för ett program |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktiverar ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/delete | Tar bort ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/read | Hämtar egenskaperna för ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/write | Skapar ett nytt programpaket eller uppdaterar ett befintligt programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/write | Skapar ett nytt program eller uppdaterar ett befintligt program |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Hämtar resultaten för en långvarig certifikatåtgärd på ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Avbryter misslyckad borttagning av ett certifikat på ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/delete | Tar bort ett certifikat från ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/read | Visar en lista över certifikat på ett Batch-konto eller hämtar egenskaperna för ett certifikat |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/write | Skapar ett nytt certifikat på ett Batch-konto eller uppdaterar ett befintligt certifikat |
> | Åtgärd | Microsoft.Batch/batchAccounts/delete | Tar bort ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/listkeys/action | Listor åtkomstnycklar för ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/operationResults/read | Hämtar resultaten för en långvarig åtgärd i Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/poolOperationResults/read | Hämtar resultaten för en långvarig åtgärd i poolen på ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/delete | Tar bort en pool från ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Inaktiverar automatisk skalning för en pool för Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/read | Visar en lista över programpooler på ett Batch-konto eller hämtar egenskaperna för en pool |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/stopResize/action | Stoppar ett pågående ändra storlek på åtgärden på en pool för Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/write | Skapar en ny pool på ett Batch-konto eller uppdaterar en befintlig pool |
> | Åtgärd | Microsoft.Batch/batchAccounts/read | Visar en lista över Batch-konton eller hämtar egenskaperna för ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/regeneratekeys/action | Återskapar åtkomstnycklar för ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synkroniserar åtkomstnycklarna för lagringskontot automatiskt konfigurerade för ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/write | Skapar ett nytt Batch-konto eller uppdaterar ett befintligt Batch-konto |
> | Åtgärd | Microsoft.Batch/locations/accountOperationResults/read | Hämtar resultaten för en långvarig åtgärd i Batch-konto |
> | Åtgärd | Microsoft.Batch/locations/checkNameAvailability/action | Kontrollerar att kontonamnet är giltigt och inte är i användning. |
> | Åtgärd | Microsoft.Batch/locations/quotas/read | Hämtar Batch-kvoter för den angivna prenumerationen på den angivna Azure-regionen |
> | Åtgärd | Microsoft.Batch/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft.Batch-resursprovidern |
> | Åtgärd | Microsoft.Batch/register/action | Registrerar prenumerationen för Batch-Resursprovidern och gör det möjligt att skapa Batch-konton |
> | Åtgärd | Microsoft.Batch/unregister/action | Avregistrerar prenumerationen för Batch-Resursprovidern som förhindrar skapandet av Batch-konton |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Billing/billingAccounts/departments/read | Lista över alla avdelningar under en faktureringsperiod kontoomfånget |
> | Åtgärd | Microsoft.Billing/billingAccounts/enrollmentAccounts/read | Lista alla registreringskonton under en faktureringsperiod kontoomfånget |
> | Åtgärd | Microsoft.Billing/billingAccounts/read | Lista över alla fakturering konton som användare har åtkomst till |
> | Åtgärd | Microsoft.Billing/billingPeriods/read | Visar en lista över tillgängliga faktureringsperioder |
> | Åtgärd | Microsoft.Billing/departments/read | Lista över alla avdelningar som användare har åtkomst till |
> | Åtgärd | Microsoft.Billing/invoices/read | Visar tillgängliga fakturor |
> | Åtgärd | Microsoft.Billing/register/action | Registrerar prenumerationen med Microsoft.Billing-resursprovidern |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.BingMaps/mapApis/Delete | För att ta bort |
> | Åtgärd | Microsoft.BingMaps/mapApis/listSecrets/action | Lista hemligheterna |
> | Åtgärd | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Läs Auktoriseringstoken för resursen för enkel inloggning |
> | Åtgärd | Microsoft.BingMaps/mapApis/Read | Läsåtgärd |
> | Åtgärd | Microsoft.BingMaps/mapApis/regenerateKey/action | Återskapa nyckeln |
> | Åtgärd | Microsoft.BingMaps/mapApis/Write | Skrivåtgärd |
> | Åtgärd | Microsoft.BingMaps/Operations/read | Beskrivning av åtgärden. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/delete | Tar bort en befintlig Blockchain-medlem. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Hämtar eller listar befintliga Blockchain medlem Api-nycklar. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/read | Hämtar eller listar befintliga Blockchain medlem (mar). |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Ansluter till en medlem i Blockchain transaktion nod. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Tar bort en befintlig Blockchain medlem transaktion nod. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Hämtar eller listar befintliga Blockchain medlem-transaktion noden Api-nycklar. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hämtar eller listar befintliga Blockchain medlem transaktion nod(er). |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Skapar eller uppdaterar en Blockchain medlem transaktion nod. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/write | Skapar eller uppdaterar en Blockchain-medlem. |
> | Åtgärd | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Hämtar Åtgärdsresultat över Blockchain medlemmar. |
> | Åtgärd | Microsoft.Blockchain/locations/checkNameAvailability/action | Kontrollerar resursnamnet är giltigt och används inte. |
> | Åtgärd | Microsoft.Blockchain/operations/read | Lista alla åtgärder i Microsoft Blockchain Resource Provider. |
> | Åtgärd | Microsoft.Blockchain/register/action | Registrerar prenumerationen för Blockchain-Resursprovidern. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Läs eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/delete | Ta bort eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/read | Läs eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/whoisblueprint/action | Hämta Azure skisser tjänstens huvudnamnsobjekt Id. |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/write | Skapa eller uppdatera eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprints/artifacts/delete | Ta bort eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprints/artifacts/read | Läs eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprints/artifacts/write | Skapa eller uppdatera eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprints/delete | Ta bort eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/blueprints/read | Läs eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/artifacts/read | Läs eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/delete | Ta bort eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/read | Läs eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/write | Skapa eller uppdatera eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/blueprints/write | Skapa eller uppdatera eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/register/action | Registrerar Resursprovidern Azure skisser |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.BotService/botServices/channels/delete | Ta bort en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/channels/read | Läs en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/channels/write | Skriv en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/connections/delete | Ta bort en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/connections/read | Läs en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/connections/write | Skriv en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/delete | Ta bort en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/read | Läs en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/write | Skriv en Bot-tjänst |
> | Åtgärd | Microsoft.BotService/locations/operationresults/read | Läs av status för en asynkron åtgärd |
> | Åtgärd | Microsoft.BotService/Operations/read | Läs-och skrivåtgärder för alla resurstyper |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Cache/checknameavailability/action | Kontrollerar om ett namn är tillgängliga för användning med en ny Redis-Cache |
> | Åtgärd | Microsoft.Cache/locations/operationresults/read | Hämtar resultaten för en långvarig åtgärd som körs som Location-rubriken tidigare returnerats till klienten |
> | Åtgärd | Microsoft.Cache/operations/read | Listar åtgärderna som har stöd för ”Microsoft.Cache-providern. |
> | Åtgärd | Microsoft.Cache/redis/delete | Ta bort hela Redis-Cache |
> | Åtgärd | Microsoft.Cache/redis/export/action | Exportera Redis-data till prefixade lagringsblobbar i angivet format |
> | Åtgärd | Microsoft.Cache/redis/firewallRules/delete | Ta bort IP-brandväggsregler för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/firewallRules/read | Hämta IP-brandväggsregler för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/firewallRules/write | Redigera IP-brandväggsregler för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/forceReboot/action | Tvinga omstart av en cache-instans, eventuellt med dataförlust. |
> | Åtgärd | Microsoft.Cache/redis/import/action | Importera data i ett format som anges från flera blobar i Redis |
> | Åtgärd | Microsoft.Cache/redis/linkedservers/delete | Ta bort länkad Server från Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/linkedservers/read | Hämta länkade servrar som är associerade med en redis-cache. |
> | Åtgärd | Microsoft.Cache/redis/linkedservers/write | Lägg till länkad Server till en Redis-Cache |
> | Åtgärd | Microsoft.Cache/redis/listKeys/action | Visa värdet för åtkomstnycklarna för Redis-Cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista de senaste Uppgraderingsmeddelandena för cacheklienten. |
> | Åtgärd | Microsoft.Cache/redis/metricDefinitions/read | Hämtar tillgängliga mått för Redis-Cache |
> | Åtgärd | Microsoft.Cache/redis/patchSchedules/delete | Ta bort uppdateringsschemat för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/patchSchedules/read | Hämtar uppdateringsschemat för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/patchSchedules/write | Ändra uppdateringsschemat för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/read | Visa inställningar och konfiguration för Redis-Cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/redis/regenerateKey/action | Ändra värdet för åtkomstnycklarna för Redis-Cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/redis/start/action | Starta en cacheinstans. |
> | Åtgärd | Microsoft.Cache/redis/stop/action | Stoppa en cacheinstans. |
> | Åtgärd | Microsoft.Cache/redis/write | Ändra inställningar och konfiguration i hanteringsportalen för Redis-Cache |
> | Åtgärd | Microsoft.Cache/register/action | Registrerar 'Microsoft.Cache-resursprovidern med en prenumeration |
> | Åtgärd | Microsoft.Cache/unregister/action | Avregistrerar 'Microsoft.Cache-resursprovidern med en prenumeration |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Capacity/appliedreservations/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/calculateexchange/action | Beräknar exchange av och priset för nya inköp och returnerar princip fel. |
> | Åtgärd | Microsoft.Capacity/calculateprice/action | Beräkna alla reserverade pris |
> | Åtgärd | Microsoft.Capacity/catalogs/read | Läs katalog med Reservation |
> | Åtgärd | Microsoft.Capacity/checkoffers/action | Kontrollera alla prenumerationserbjudanden |
> | Åtgärd | Microsoft.Capacity/checkscopes/action | Kontrollera alla prenumerationer |
> | Åtgärd | Microsoft.Capacity/commercialreservationorders/read | Hämta Reservationsbeställningar som skapats i alla klienter |
> | Åtgärd | Microsoft.Capacity/exchange/action | Exchange-alla Reservation |
> | Åtgärd | Microsoft.Capacity/operations/read | Läsa alla åtgärder |
> | Åtgärd | Microsoft.Capacity/register/action | Registrerar resursprovidern kapaciteten och gör det möjligt att skapa kapacitet resurser. |
> | Åtgärd | Microsoft.Capacity/reservationorders/action | Uppdatera alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/availablescopes/action | Hitta alla tillgängliga Scope |
> | Åtgärd | Microsoft.Capacity/reservationorders/calculaterefund/action | Beräknar återbetalning av och priset för nya inköp och returnerar princip fel. |
> | Åtgärd | Microsoft.Capacity/reservationorders/delete | Ta bort alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/merge/action | Slå samman alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/action | Uppdatera alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/delete | Ta bort alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/revisions/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/write | Skapa en Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/return/action | Returnera alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/split/action | Dela upp en Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/swap/action | Växla alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/write | Skapa en Reservation |
> | Åtgärd | Microsoft.Capacity/tenants/register/action | Registrera valfri klient |
> | Åtgärd | Microsoft.Capacity/unregister/action | Avregistrera Klientorganisation |
> | Åtgärd | Microsoft.Capacity/validatereservationorder/action | Validera alla Reservation |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Cdn/CheckNameAvailability/action |  |
> | Åtgärd | Microsoft.Cdn/CheckResourceUsage/action |  |
> | Åtgärd | Microsoft.Cdn/edgenodes/delete |  |
> | Åtgärd | Microsoft.Cdn/edgenodes/read |  |
> | Åtgärd | Microsoft.Cdn/edgenodes/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/CheckResourceUsage/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/CheckResourceUsage/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/Load/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/Purge/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/Start/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/write |  |
> | Åtgärd | Microsoft.Cdn/operations/read |  |
> | Åtgärd | Microsoft.Cdn/profiles/CheckResourceUsage/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/delete |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/CheckResourceUsage/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/delete |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/read |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/write |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/delete |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/Load/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/origins/delete |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/origins/read |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/origins/write |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/Purge/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/read |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/Start/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/Stop/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/ValidateCustomDomain/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/write |  |
> | Åtgärd | Microsoft.Cdn/profiles/GenerateSsoUri/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/GetSupportedOptimizationTypes/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/read |  |
> | Åtgärd | Microsoft.Cdn/profiles/write |  |
> | Åtgärd | Microsoft.Cdn/register/action | Registrerar prenumerationen för CDN-resursprovidern och gör det möjligt att skapa CDN-profiler. |
> | Åtgärd | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/certificates/Delete | Ta bort ett befintligt certifikat |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/certificates/Read | Hämta listan över certifikat |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/certificates/Write | Lägg till ett nytt certifikat eller uppdatera en befintlig |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/Delete | Ta bort en befintlig AppServiceCertificate |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/Read | Hämta listan över CertificateOrders |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Utfärda en befintlig certificateorder igen |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Förnya ett befintligt certificateorder |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Skicka e-post med certifikat |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Skicka om begäran e-postmeddelanden till en annan e-postadress |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Hämta plats försegla för ett utfärdat App Service Certificate |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Hämta listan över certifikatåtgärder |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Hämta historik för certifikat som e-post |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verifiera domänägarskap |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/Write | Lägg till en ny certificateOrder eller uppdatera en befintlig |
> | Åtgärd | Microsoft.CertificateRegistration/operations/Read | Lista över alla åtgärder från certifikatregistreringsplatsen för app service |
> | Åtgärd | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Etablera tjänstens huvudnamn för app-tjänstens huvudnamn |
> | Åtgärd | Microsoft.CertificateRegistration/register/action | Registrera resursprovidern Microsoft Certificates för prenumerationen |
> | Åtgärd | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Verifiera certifikat köp objekt utan att skicka den |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicCompute/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Kontrollerar tillgängligheten för ett visst domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Hämtar tillgängligheten för ett visst domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/active/write | Anger det aktiva domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Visa tillgänglighetsuppsättningen för resursen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/capabilities/read | Visar domännamnsfunktionerna |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/delete | Ta bort resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Visar Distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Hämta rollen på distributionsplatsen för domännamn |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Hämta rollinstans för rollen på distributionsplatsen för domännamn |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Hämta Distributionsplatsens status. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Lägga till Distributionsplatsens status. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Hämta uppgraderingsdomän för distributionsplatsen på domännamnet |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Uppdatera uppgraderingsdomän för distributionsfacket på domännamnet |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/delete | Ta bort domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Läser Åtgärdsstatus för domänen domännamnstilläggens. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/read | Returnerar domänen filnamnstillägg. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/write | Lägg till domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Ta bort en ny intern belastningsutjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Läser Åtgärdsstatus för domännamnens interna belastningsutjämnare. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Hämtar de interna belastningsutjämnarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Skapar en ny intern belastningsutjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Läser Åtgärdsstatus för belastningsutjämnade slutpunktsuppsättningar domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Hämta belastningsutjämnade slutpunktsuppsättningar. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Lägg till belastningsutjämnad slutpunktsuppsättning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Hämta Åtgärdsstatus för domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Läser Åtgärdsstatus för domänen domännamnstilläggens. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/read | Returnera resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Ta bort de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Läser Åtgärdsstatus för tjänstcertifikat. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Returnerar de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Lägg till eller ändra de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Avbryter migreringen av en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Utför migrering av en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/delete | Tar bort en given distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Läser Åtgärdsstatus för domänen domännamnsplatsernas. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Förbereder migrering av en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/read | Visar Distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Ta bort tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Läser Åtgärdsstatus för domän namn fack roller resurstilläggets referenser. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Returnerar tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Lägg till eller ändra tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Hämta rolldefinition för mått för domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Hämta rollen mått för domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Hämta Åtgärdsstatus för rollen fack namn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/read | Hämta rollen för distributionsplatsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Laddar ned fjärrskrivbordsanslutning filen för rollinstansen på rollen fack namn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Hämtar Åtgärdsstatus för rollinstansen på domänroll namn fack. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Hämta rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Återskapar rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Återställer avbildningen för rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uppgradera domän |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Hämta rollen sku för distributionsplatsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/write | Lägg till rollen för distributionsplatsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/start/action | Startar en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Ändrar Distributionsplatsens status till Stoppad. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Ändrar Distributionsplatsens status till igång. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/stop/action | Göra uppehåll. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Gå igenom uppgraderingsdomän. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Verifierar migreringen av en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/write | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/swap/action | Byta mellanlagringsplatsen till produktionsplatsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/write | Lägg till eller ändra resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/moveSubscriptionResources/action | Flytta alla klassiska resurser till en annan prenumeration. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystemFamilies/read | Visar en lista över guest operating system familjer som är tillgängliga i Microsoft Azure och listar även versionerna av operativsystemet som är tillgängliga för respektive familj. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystems/read | Listar de versioner av gästoperativsystemet som är tillgängliga i Microsoft Azure. |
> | Åtgärd | Microsoft.ClassicCompute/operations/read | Hämtar listan över åtgärder. |
> | Åtgärd | Microsoft.ClassicCompute/operationStatuses/read | Läser Åtgärdsstatus för resursen. |
> | Åtgärd | Microsoft.ClassicCompute/quotas/read | Hämta kvoten för prenumerationen. |
> | Åtgärd | Microsoft.ClassicCompute/register/action | Registrera för klassisk beräkning |
> | Åtgärd | Microsoft.ClassicCompute/resourceTypes/skus/read | Hämtar Sku-listan för resurstyper som stöds. |
> | Åtgärd | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Verifiera prenumerationens tillgänglighet för klassisk flyttåtgärd. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är associerade med den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Läser Åtgärdsstatus för virtuella datorer som är associerade nätverkssäkerhetsgrupper. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är associerade med den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är associerad med den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Hämtar möjliga asynkrona åtgärder |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Bifogar en datadisk till en virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/capture/action | Avbilda en virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/delete | Tar bort virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Tar bort en datadisk från den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Hämta diagnostikinställningar för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/disks/read | Hämtar listan över datadiskar |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Laddar ned RDP-filen för den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Läser Åtgärdsstatus för tillägg för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/read | Hämtar tillägget för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/write | Placerar tillägget för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Hämta måttdefinition för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är associerad med nätverksgränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Läser Åtgärdsstatus för virtuella datorer som är associerade nätverkssäkerhetsgrupper. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är associerad med nätverksgränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är associerad med nätverksgränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Läser Åtgärdsstatus för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Utför underhåll på den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/read | Hämtar listan över virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Distribuerar om den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/restart/action | Startar om virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Stäng av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/start/action | Starta den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/stop/action | Stoppar den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/write | Lägg till eller ändra virtuella datorer. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Få en expressroute mellan anslutningsstatus för åtgärden. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Ta bort expressroute mellan anslutning peering. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Få en expressroute mellan peering status för anslutningen. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Hämta expressroute mellan anslutning peering. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Lägg till expressroute mellan anslutning peering. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Få snabb dirigera mellan anslutningar. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Lägg till expressroute mellan anslutningar. |
> | Åtgärd | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Hämtar listan över enheter som stöds. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Läser Åtgärdsstatus för nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar de diagnostiska för Nätverkssäkerhetsgrupper |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för den här åtgärden kompletteras av resursprovidern för insights Nätverkssäkerhetsgrupperna. |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Tar bort säkerhetsregeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Läser Åtgärdsstatus för de security reglerna för nätverkssäkerhetsgrupper. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Hämtar säkerhetsregeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Lägger till eller uppdaterar en säkerhetsregel. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/write | Lägger till en ny grupp. |
> | Åtgärd | Microsoft.ClassicNetwork/operations/read | Hämta åtgärder för klassiskt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/quotas/read | Hämta kvoten för prenumerationen. |
> | Åtgärd | Microsoft.ClassicNetwork/register/action | Registrera för klassiskt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/delete | Ta bort en reserverad Ip. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/join/action | Ansluta till en reserverad Ip |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad Ip |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Läser Åtgärdsstatus för reserverade IP-adresser. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/read | Hämtar reserverade IP-adresser |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/write | Lägg till en ny reserverad Ip |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Avbryter migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Kontrollerar tillgängligheten för en given IP-adress i ett virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Genomför migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/delete | Tar bort det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Upphäver återkallningen av ett klientcertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Läs de återkallade klientcertifikaten. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Återkallar ett klientcertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Tar bort klientcertifikatet för gateway för virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Hämtar certifikat efter tumavtryck. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Listar gatewaycertifikatspaketet för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Hitta klienten rotcertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Överför ett nytt klientrotscertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Ansluter en gatewayanslutning från plats till plats. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Kopplar från en plats till plats-gatewayanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Hämtar listan över anslutningar. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testar en plats till plats-gatewayanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Tar bort den virtuella nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Hämtar konfigurationsskriptet för enhet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Hämtar gateway-diagnostiken. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Hämtar kretstjänstnyckeln. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listar gateway-paketet för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Läser Åtgärdsstatus för virtuella nätverksgatewayar. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Hämtar virtuella nätverkets gateway-paketet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Hämtar de virtuella nätverksgatewayerna. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Startar diagnostik för den virtuella nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Stoppar diagnostiken för den virtuella nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Lägger till en virtuell nätverksgateway. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/join/action | Ansluter till det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Läser Åtgärdsstatus för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Peerar ett virtuellt nätverk med ett annat virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Förbereder migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/read | Hämta det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Tar bort peering-proxyservern för det virtuella fjärrnätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Hämtar peering-proxyservern för det virtuella fjärrnätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Lägger till eller uppdaterar peering-proxyservern för det virtuella fjärrnätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Läser Åtgärdsstatus för virtuella undernät kopplade nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Verifierar migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Hämtar det virtuella nätverkets peering. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/write | Lägg till ett nytt virtuellt nätverk. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicStorage/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Kontrollerar tillgängligheten för ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Hämta tillgängligheten för ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/disks/read | Returnerar lagringskontodisken. |
> | Åtgärd | Microsoft.ClassicStorage/images/operationstatuses/read | Hämtar Åtgärdsstatus för avbildningen. |
> | Åtgärd | Microsoft.ClassicStorage/images/read | Returnerar bilden. |
> | Åtgärd | Microsoft.ClassicStorage/operations/read | Hämtar klassiska lagringsåtgärder |
> | Åtgärd | Microsoft.ClassicStorage/osImages/read | Returnerar avbildningen av operativsystemet. |
> | Åtgärd | Microsoft.ClassicStorage/osPlatformImages/read | Hämtar avbildningen av operativsystemet-plattformen. |
> | Åtgärd | Microsoft.ClassicStorage/publicImages/read | Hämtar avbildningen av offentliga virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/quotas/read | Hämta kvoten för prenumerationen. |
> | Åtgärd | Microsoft.ClassicStorage/register/action | Registrera för klassisk lagring |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Avbryter migreringen av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Utför migrering av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/delete | Ta bort lagringskontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/delete | Tar bort en angiven lagringskontodisk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Läser Åtgärdsstatus för resursen. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagringskontodisken. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/write | Lägger till en lagringskontodisk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/delete | Tar bort en viss lagringskontoavbildning. (Inaktuell. Använd ”Microsoft.ClassicStorage/storageAccounts/vmImages”) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Returnerar den avbildningen åtgärdsstatusen för lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagringskontoavbildning. (Inaktuell. Använd ”Microsoft.ClassicStorage/storageAccounts/vmImages”) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar åtkomstnycklarna för storage-konton. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Läser Åtgärdsstatus för resursen. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Tar bort en angiven avbildning av operativsystem. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returnerar avbildningen av lagringskontots operativsystem. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/write | Lägger till en angiven avbildning av operativsystem. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Förbereder migrering av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/read | Returnera lagringskontot med det angivna kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Återskapar befintliga åtkomstnycklarna för lagringskontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/read | Hämta tillgängliga tjänster. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Verifierar migreringen av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Tar bort en viss virtuell datoravbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Hämtar Åtgärdsstatus för avbildningen en viss virtuell dator. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Returnerar avbildningen av virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Lägger till en viss VM-avbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/write | Lägger till ett nytt lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/vmImages/read | Visar en lista över avbildningar av virtuella datorer. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/analyze/action | Den här åtgärden extraherar en omfattande uppsättning visuella funktioner baserat på innehållet i.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/areaofinterest/action | Den här åtgärden returnerar en avgränsningsruta omkring området är viktigast för avbildningen. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/describe/action | Den här åtgärden genererar en beskrivning av en avbildning i mänskliga läsbara språk med fullständiga meningar.<br> Beskrivningen är baserad på en uppsättning innehåll taggar som returneras också av åtgärden.<br>Fler än en beskrivning kan genereras för varje bild.<br> Beskrivningar ordnas efter deras förtroendepoäng.<br>Alla beskrivningar är på engelska. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/detect/action | Den här åtgärden utför objektidentifiering på den angivna avbildningen.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Den här åtgärden genererar en miniatyrbild med användardefinierade bredd och höjd.<br> Som standard tjänsten analyserar avbildningen, identifierar region of interest (ROI) och genererar smart beskärning koordinater baserat på vad du TJÄNAR.<br> Smart beskärning hjälper dig när du anger proportionerna som skiljer sig från inmatad bild |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Den här åtgärden identifierar innehåll inuti en avbildning genom att tillämpa en domänspecifika-modell.<br> Listan över domänspecifika modeller som stöds av den API för visuellt innehåll kan hämtas med hjälp av /models GET-begäran.<br> För närvarande API: et tillhandahåller följande domänspecifika modeller: kändisar, landmärken. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/read | Den här åtgärden returnerar listan över domänspecifika modeller som stöds av den API för visuellt innehåll.  API: et stöder för närvarande följande domänspecifika modeller: kändisar igenkännande, Landmärke Igenkännande. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/ocr/action | Optisk teckenläsning (OCR) identifierar text i en bild och extraherar lästa tecken till en dator teckenström.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Använd det här gränssnittet för att hämta resultatet av en identifiera Text-åtgärd. När du använder gränssnittet identifiera Text innehåller svaret ett fält med namnet ”åtgärden plats”. ”Åtgärden plats” innehåller den URL som du måste använda för att utföra åtgärden hämta identifiera Text resultatet för åtgärden. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/tag/action | Den här åtgärden genererar en lista över ord eller taggar, som är relevanta för innehållet i den angivna avbildningen.<br>I API för visuellt innehåll returnerar taggar baserat på objekt, levande varelser, landskap eller åtgärder som finns i avbildningar.<br>Till skillnad från kategorier, taggar inte är uppdelade enligt ett hierarkisk klassificeringssystem, men motsvarar innehållet.<br>Taggar kan innehålla tips för att undvika tvetydighet eller tillhandahålla kontext, till exempel taggen ”cello” kan åtföljas av tipset ”musik betalningsinstrument”.<br>Alla taggar är på engelska. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/textoperations/read | Det här gränssnittet används för att hämta identifiera text resultat. URL-Adressen till det här gränssnittet ska hämtas från <b>”åtgärden plats”</b> fält som returneras från identifiera Text-gränssnittet. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/delete | Tar bort API-konton |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/detect/action | Identifiera ansikten i en bild, gå tillbaka ansiktsrektanglar och eventuellt med faceIds, landmärken och attribut. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/delete | Ta bort en angiven ansikts-lista. Ansikts-relaterade bilder i listan över ansikte tas bort, för. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Ta bort ett ansikte från en lista med face genom angivna faceListId och persisitedFaceId. Relaterade ansiktsbilden tas bort, för. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/write | Lägg till ett ansikte i en angiven ansikts-lista, upp till 1 000 ansikten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Hämta en ansikte lista faceListId, namn, userData och ansikten i listan över ansikte. Lista ansikte listor faceListId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Skapa en tom ansikts-lista med användardefinierade faceListId, namn och en valfri userData. Upp till 64 ansikte tillåts listor uppdateringsinformation för en ansikts-lista, inklusive namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Den angivna förfrågat ansikte faceId att söka i liknande ansikten från en matris med faceId, en ansikts-lista eller en stor ansikts-lista. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Dela upp kandidat ansikten i grupper baserat på likheter ansikte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1-till-många-identifiering för att hitta de närmaste matchningarna av specifika fråga person ansikte från en persongrupp eller stora persongrupp. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Ta bort en angiven stora ansikts-lista. Ansikts-relaterade bilder i listan över stora ansikte tas bort, för. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Ta bort ett ansikte från en stor ansikts-lista med angivna largeFaceListId och persisitedFaceId. Relaterade ansiktsbilden tas bort, för. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Hämta beständiga ansikte i stora ansikte lista med largeFaceListId och persistedFaceId. Lista ansikten persistedFaceId och userData i en angiven stora ansikts-lista. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Lägg till ett ansikte i en angiven stora ansikts-lista, upp till 1 000 000 ansikten. Uppdatera ett angivna ansikte userData fält i en stor ansikte lista av dess persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Hämta en stor ansikte lista largeFaceListId, namn, userData. Lista över stora ansikte visar information för largeFaceListId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Skicka en stor ansikte lista utbildning uppgift. Utbildning är ett viktigt steg som kan använda för endast en tränad stora ansikts-lista. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Kontrollera stora ansikte lista utbildning status slutförda eller fortfarande pågår. LargeFaceList utbildning är en asynkron åtgärd |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Skapa en tom stora ansikts-lista med användardefinierade largeFaceListId, namn och en valfri userData. Uppdatera information för en stor ansikts-lista, inklusive namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Ta bort en befintlig persongrupp för stora med angivna personGroupId. Beständiga data i den här stora person gruppen tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Skapa en ny person i en angiven stora person-grupp. Om du vill lägga till ansiktsigenkänning i den här personen Ring |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Ta bort en befintlig person från en stor persongrupp. Alla lagrade personliga data och ansikts-avbildningar i posten person kommer att tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Ta bort ett ansikte från en person i en stor persongrupp. Ansikts-data och avbildning som är relaterade till den här ansikts-posten tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Hämta information om personen ansikte. Beständiga person ansiktet anges av dess largePersonGroupId, personId och persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Lägg till en ansiktsbilden till en person i en persongrupp med stora ansiktsidentifiering eller verifieringen. Beständiga ansiktes userData fält för att klara av avbildningen av uppdatering av en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Hämta ett namn och userData och beständiga faceIds som representerar ansiktsbilden registrerade person. Lista alla personer informationen i den angivna stora persongrupp, inklusive personId, namn, userData och persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Uppdatera namn eller userData för en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Hämta information för stora person grupper, inklusive dess namn och userData. Den här API: et returnerar stora person gruppinformation lista över alla befintliga stora persongrupper largePesonGroupId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Skicka en stor person grupp utbildning uppgift. Utbildning är ett viktigt steg som kan användas i endast en utbildad stora person-grupp. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | För att kontrollera stora person utbildning Gruppstatus slutförda eller fortfarande pågår. LargePersonGroup utbildning är en asynkron åtgärd |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Skapa en ny persongrupp för stora med användardefinierade largePersonGroupId, namn och valfri userData. Uppdatera en befintlig stora person gruppens namn och userData. Egenskaperna bibehålla om de inte är i begärandetexten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Ta bort en befintlig persongrupp med angivna personGroupId. Beständiga data i den här personen gruppen tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Skapa en ny person i en angiven person-grupp. Om du vill lägga till ansiktsigenkänning i den här personen Ring |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Ta bort en befintlig person från en persongrupp. Alla lagrade personliga data och ansikts-avbildningar i posten person kommer att tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Ta bort ett ansikte från en person i en persongrupp. Ansikts-data och avbildning som är relaterade till den här ansikts-posten tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Hämta information om personen ansikte. Beständiga person ansiktet anges av dess personGroupId, personId och persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Lägg till en ansiktsbilden till en person i en persongrupp med ansiktsidentifiering eller verifieringen. Beständiga ansiktes userData fält för att klara av avbildningen av flera uppdatering en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Hämta ett namn och userData och beständiga faceIds som representerar ansiktsbilden registrerade person. Visa information om alla personer i gruppen angiven person, inklusive personId, namn, userData och persistedFaceIds av registrerad. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Uppdatera namn eller userData för en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Hämta person gruppnamn och userData. Om du vill hämta information om personen under den här personGroup, Använd lista persongrupper pesonGroupId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Skicka en person grupp utbildning uppgift. Utbildning är ett viktigt steg som kan använda för en tränad persongrupp. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | För att kontrollera person utbildning Gruppstatus slutförda eller fortfarande pågår. PersonGroup utbildning är en asynkron åtgärd som utlöste |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Skapa en ny persongrupp med angivna personGroupId, namn och användaren userData. Uppdatera en befintlig persongrupp namn och userData. Egenskaperna bibehålla om de inte är i begärandetexten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Kontrollera om två ansikten tillhör samma person eller om ett ansikte tillhör en person. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/listKeys/action | Lista över nycklar |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Hämtar förutsägelsen publicerade slutpunkten för den givna frågan. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/read | Läser API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/regenerateKey/action | Återskapa nyckeln |
> | Åtgärd | Microsoft.CognitiveServices/accounts/skus/read | Läser tillgängliga SKU: er för en befintlig resurs. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | API: et returnerar en lista med kända enheter och allmänna med namnet entiteter (\"Person\", \"plats\", \"organisation\" osv) i ett visst dokument. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | API:t returnerar en lista med strängar som anger samtalsämnen i den inmatade texten. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | API:t returnerar det identifierade språket och en poäng mellan 0 och 1. 1 poäng visar att korrekt språk har identifierats med 100 procents säkerhet. 120 språk stöds totalt. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | API:t returnerar en poäng mellan 0 och 1.<br>Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd.<br>Ett resultat på 0,5 anger bristen på sentiment (t.ex.)<br>en factoid-instruktion). |
> | Åtgärd | Microsoft.CognitiveServices/accounts/usages/read | Hämta kvotanvändning för en befintlig resurs. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/write | Skriver API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Läser tillgängliga SKU: er för en prenumeration. |
> | Åtgärd | Microsoft.CognitiveServices/Operations/read | Lista över alla tillgängliga åtgärder |
> | Åtgärd | Microsoft.CognitiveServices/register/action | Registrerar prenumerationen för Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Commerce/RateCard/read | Returnerar erbjuder data, resurs/mätaren metadata och för den givna prenumerationen. |
> | Åtgärd | Microsoft.Commerce/UsageAggregates/read | Hämtar Microsoft Azure-förbrukning av en prenumeration. Resultatet innehåller aggregeringar användningsdata, prenumeration och resursgrupp relaterad information, på ett visst tidsintervall. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Compute/availabilitySets/delete | Tar bort tillgänglighetsuppsättningen |
> | Åtgärd | Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighetsuppsättning |
> | Åtgärd | Microsoft.Compute/availabilitySets/vmSizes/read | Lista över tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i tillgänglighetsuppsättningen |
> | Åtgärd | Microsoft.Compute/availabilitySets/write | Skapar en ny tillgänglighetsuppsättning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/disks/beginGetAccess/action | Hämta diskens SAS-URI för blobåtkomst |
> | Åtgärd | Microsoft.Compute/disks/delete | Tar bort disken |
> | Åtgärd | Microsoft.Compute/disks/endGetAccess/action | Återkalla diskens SAS-URI |
> | Åtgärd | Microsoft.Compute/disks/read | Hämta egenskaperna för en Disk |
> | Åtgärd | Microsoft.Compute/disks/write | Skapar en ny Disk eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/galleries/delete | Tar bort galleriet |
> | Åtgärd | Microsoft.Compute/galleries/images/delete | Tar bort Gallery-Image |
> | Åtgärd | Microsoft.Compute/galleries/images/read | Hämtar egenskaperna för galleriet bilden |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/delete | Tar bort Avbildningsversion galleriet |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/read | Hämtar egenskaperna för galleriet Avbildningsversion |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/write | Skapar en ny Version i galleriet bilden eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/galleries/images/write | Skapar en ny avbildning i galleriet eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/galleries/read | Hämtar egenskaperna för galleriet |
> | Åtgärd | Microsoft.Compute/galleries/write | Skapar ett nytt galleri eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/hostGroups/delete | Tar bort värdgruppen |
> | Åtgärd | Microsoft.Compute/hostGroups/read | Hämta egenskaperna för en värdgrupp |
> | Åtgärd | Microsoft.Compute/hostGroups/write | Skapar en ny värdgrupp eller uppdaterar en befintlig värdgrupp |
> | Åtgärd | Microsoft.Compute/hosts/delete | Tar bort värden |
> | Åtgärd | Microsoft.Compute/hosts/read | Hämta egenskaperna för en värd |
> | Åtgärd | Microsoft.Compute/hosts/write | Skapar en ny värd eller uppdaterar en befintlig värd |
> | Åtgärd | Microsoft.Compute/images/delete | Avbildningen tas bort |
> | Åtgärd | Microsoft.Compute/images/read | Hämta egenskaperna för avbildningen |
> | Åtgärd | Microsoft.Compute/images/write | Skapar en ny avbildning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/locations/capsOperations/read | Hämtar status för en asynkron Caps-åtgärd |
> | Åtgärd | Microsoft.Compute/locations/diskOperations/read | Hämtar status för en asynkron diskåtgärd |
> | Åtgärd | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Skapa loggar för att visa totalt antal begäranden efter tidsintervall för att underlätta begränsning diagnostik. |
> | Åtgärd | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Skapa loggar för att visa aggregeringar av begränsade begäranden grupperade efter ResourceName, OperationName eller den tillämpade begränsa principen. |
> | Åtgärd | Microsoft.Compute/locations/operations/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Hämta egenskaperna för ett Plattformsavbildningserbjudande |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Hämta egenskaperna för en Plattformsavbildnings-Sku |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Hämta egenskaperna för en Plattformsavbildningsversion |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Hämta egenskaperna för en VMExtension-typ |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Hämta egenskaperna för en VMExtension-Version |
> | Åtgärd | Microsoft.Compute/locations/publishers/read | Hämta egenskaperna för en utgivare |
> | Åtgärd | Microsoft.Compute/locations/runCommands/read | Listar tillgängliga körningskommandon på platsen |
> | Åtgärd | Microsoft.Compute/locations/usages/read | Hämtar tjänstbegränsningar och aktuella användningsmängd för prenumerationens beräkningsresurser på en plats |
> | Åtgärd | Microsoft.Compute/locations/vmSizes/read | Visar en lista över tillgängliga virtuella datorstorlekar på en plats |
> | Åtgärd | Microsoft.Compute/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft.Compute-resursprovidern |
> | Åtgärd | Microsoft.Compute/proximityPlacementGroups/delete | Tar bort Placeringsgrupp närhet |
> | Åtgärd | Microsoft.Compute/proximityPlacementGroups/read | Hämta egenskaperna för en grupp för placering av närhet |
> | Åtgärd | Microsoft.Compute/proximityPlacementGroups/write | Skapar en ny närhet Placeringsgrupp eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/register/action | Registrerar prenumeration med Microsoft.Compute-resursprovidern |
> | Åtgärd | Microsoft.Compute/restorePointCollections/delete | Tar bort den samlingen med återställningspunkter och innehöll återställningspunkter |
> | Åtgärd | Microsoft.Compute/restorePointCollections/read | Hämta egenskaperna för en samling med återställningspunkter |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/delete | Tar bort återställningspunkten |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/read | Hämta egenskaperna för en återställningspunkt |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Hämta egenskaperna för en återställningspunkt tillsammans med blob-SAS URI: er |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/write | Skapar en ny återställningspunkt |
> | Åtgärd | Microsoft.Compute/restorePointCollections/write | Skapar en ny samling med återställningspunkter eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/sharedVMImages/delete | Tar bort SharedVMImage |
> | Åtgärd | Microsoft.Compute/sharedVMImages/read | Hämta egenskaperna för en SharedVMImage |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/delete | Ta bort en SharedVMImageVersion |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/read | Hämta egenskaperna för en SharedVMImageVersion |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replikera en SharedVMImageVersion till målregioner |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/write | Skapa en ny SharedVMImageVersion eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Compute/sharedVMImages/write | Skapar en ny SharedVMImage eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/skus/read | Hämtar listan över Microsoft.Compute-SKU: er tillgängliga för din prenumeration |
> | Åtgärd | Microsoft.Compute/snapshots/beginGetAccess/action | Hämta ögonblicksbilden SAS-URI för blobåtkomst |
> | Åtgärd | Microsoft.Compute/snapshots/delete | Ta bort en ögonblicksbild |
> | Åtgärd | Microsoft.Compute/snapshots/endGetAccess/action | Återkalla diskens SAS-URI ögonblicksbilden |
> | Åtgärd | Microsoft.Compute/snapshots/read | Hämta egenskaperna för en ögonblicksbild |
> | Åtgärd | Microsoft.Compute/snapshots/write | Skapa en ny ögonblicksbild eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Compute/unregister/action | Avregistrerar prenumeration med Microsoft.Compute-resursprovidern |
> | Åtgärd | Microsoft.Compute/virtualMachines/capture/action | Avbildar den virtuella datorn genom att kopiera virtuella hårddiskar och genererar en mall som kan användas för att skapa liknande virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konverterar den virtuella datorns blobbaserade diskar till hanterade diskar |
> | Åtgärd | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurser |
> | Åtgärd | Microsoft.Compute/virtualMachines/delete | Tar bort den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/delete | Tar bort tillägget för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/read | Hämta egenskaperna för tillägg för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/write | Skapar ett nytt tillägg för virtuell dator eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/virtualMachines/generalize/action | Anger tillståndet för virtuella datorn på generaliserad och förbereder den virtuella datorn för avbildning |
> | Åtgärd | Microsoft.Compute/virtualMachines/instanceView/read | Hämtar detaljerad Körningsstatus för den virtuella datorn och dess resurser |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med Windows-administratör eller Linux-rotanvändare |
> | Åtgärd | Microsoft.Compute/virtualMachines/performMaintenance/action | Utför underhållsåtgärd på den virtuella datorn. |
> | Åtgärd | Microsoft.Compute/virtualMachines/powerOff/action | Stänger av den virtuella datorn. Observera att den virtuella datorn fortsätter att debiteras. |
> | Åtgärd | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/redeploy/action | Distribuerar om virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/reimage/action | Avbildningen virtuella dator som använder differentierande disk. |
> | Åtgärd | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/runCommand/action | Kör ett fördefinierat skript på den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar för den virtuella datorn kan uppdateras till |
> | Åtgärd | Microsoft.Compute/virtualMachines/write | Skapar en ny virtuell dator eller uppdaterar en befintlig virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Stänger av och startar om beräkningsresurserna för instanser av Virtual Machine Scale Sets  |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/delete | Tar bort virtuella datorns Skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/delete/action | Tar bort instanser av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Tar bort VM-Skalningsuppsättningen ange tillägg |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Hämtar egenskaperna för skala ange tillägg för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Skapar ny skala ange tillägg för virtuell dator eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Gå igenom plattformsuppdateringsdomäner av ett service fabric Virtual Machine Scale Sets att slutföra en väntande uppdatering som har fastnat manuellt |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Hämtar instansvyn för Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Uppdaterar manuellt instanser till senaste modellen för VM-Skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Hämta egenskaper för alla nätverksgränssnitt för en Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Startar en löpande uppgradering för att flytta alla instanser för Virtual Machine Scale Sets till den senaste tillgängliga versionen plattform bild OS. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Hämtar historiken för operativsystemuppgraderingar för VM-Skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Utför planerat underhåll på instanser av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Stänger av instanser av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Hämta egenskaper för alla offentliga IP-adresser för en Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/read | Hämta egenskaperna för en Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Distribuera om instanser av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Skapar ny avbildning av instanser av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Skapar ny avbildning av alla diskar (OS-disken och Datadiskar) för instanser av en Virtual Machine Scale Sets  |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/restart/action | Startar om instanserna för VM-Skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Avbryter samlad uppgradering av en Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Hämta senaste status för löpande uppgradering för en Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/scale/action | Kontrollera om en befintlig VM-Skalningsuppsättning kan skala In / ut till det angivna instansantalet |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/skus/read | Visar en lista över giltiga SKU: er för en befintlig Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/start/action | Startar instanser av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Stänger av och startar om beräkningsresurserna för en virtuell dator i en Skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Ta bort en specifik virtuell dator i en Skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Hämtar instansvyn för en virtuell dator i en Skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Hämta egenskaper för offentliga IP-adressen som skapats med hjälp av Virtual Machine Scale Sets. Virtual Machine Scale Sets kan skapa högst en offentlig IP-adress per IP-konfiguration (privat IP) |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Hämta egenskaper för en eller alla IP-konfigurationer för ett nätverksgränssnitt som skapats med hjälp av Virtual Machine Scale Sets. IP-konfigurationer motsvarar privata IP-adresser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Hämta egenskaper för en eller alla nätverksgränssnitt för en virtuell dator som skapats med hjälp av Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Utför planerat underhåll på en virtuell datorinstans i Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Stänger av en virtuell datorinstans i VM Scale Sets. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Hämtar egenskaperna för en virtuell dator i VM Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Distribuerar om en virtuell datorinstans i Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Skapar ny avbildning av en virtuell datorinstans i Virtual Machine Scale Sets. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Skapar ny avbildning av alla diskar (OS-disken och Datadiskar) för instansen för virtuella datorer i Virtual Machine Scale Sets. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Startar om en virtuell datorinstans i en Skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Kör ett fördefinierat skript på en virtuell datorinstans i Virtual Machine Scale Sets. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Startar en virtuell datorinstans i en Skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Uppdaterar egenskaperna för en virtuell dator i VM Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Lista över tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i Virtual Machine Scale Sets |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/write | Skapar en ny VM-Skalningsuppsättning eller uppdaterar en befintlig |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Consumption/balances/read | Lista användning sammanfattning för faktureringsperioden för en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/budgets/delete | Ta bort budgetar genom en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/budgets/read | Lista över budgetar genom en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/budgets/write | Skapar och uppdatera budgetar genom en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/charges/read | Lista avgifter |
> | Åtgärd | Microsoft.Consumption/credits/read | Ange personer |
> | Åtgärd | Microsoft.Consumption/events/read | Visa händelser |
> | Åtgärd | Microsoft.Consumption/forecasts/read | Lista prognoser |
> | Åtgärd | Microsoft.Consumption/lots/read | Lista över många |
> | Åtgärd | Microsoft.Consumption/marketplaces/read | Lista över marketplace resource användningsinformation för ett omfång för EA- och WebDirect. |
> | Åtgärd | Microsoft.Consumption/operationresults/read | Lista operationresults |
> | Åtgärd | Microsoft.Consumption/operations/read | Lista över åtgärder som alla stöds av Microsoft.Consumption-resursprovidern. |
> | Åtgärd | Microsoft.Consumption/operationstatus/read | Lista operationstatus |
> | Åtgärd | Microsoft.Consumption/pricesheets/read | Lista över Pricesheets data för en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/register/action | Registrera dig för RP-förbrukning |
> | Åtgärd | Microsoft.Consumption/reservationDetails/read | Lista användning-information för reserverade instanser av reservationen ordning eller hantering av grupper. Information om data är per instans per dag nivå. |
> | Åtgärd | Microsoft.Consumption/reservationRecommendations/read | Lista enkel eller delade rekommendationer för reserverade instanser för en prenumeration. |
> | Åtgärd | Microsoft.Consumption/reservationSummaries/read | Lista användning sammanfattning för reserverade instanser av reservationen ordning eller hantering av grupper. Sammanfattningsdata är antingen på varje månad eller varje dag. |
> | Åtgärd | Microsoft.Consumption/reservationTransactions/read | Lista över transaktionshistoriken för reserverade instanser av hanteringsgrupper. |
> | Åtgärd | Microsoft.Consumption/tags/read | Lista över taggar för EA-kunder och prenumerationer. |
> | Åtgärd | Microsoft.Consumption/tenants/read | Lista över klienter |
> | Åtgärd | Microsoft.Consumption/tenants/register/action | Registrera åtgärden för omfånget av Microsoft.Consumption av en klient. |
> | Åtgärd | Microsoft.Consumption/terms/read | Lista över villkoren för en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/usageDetails/read | Lista över användningsinformation för ett omfång för EA- och WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec till en specifik behållare. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Hämta loggar för en specifik behållare. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/delete | Ta bort den specifika behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/read | Hämta alla behållargrupper. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/restart/action | Startar om en särskild behållargrupp. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/start/action | Startar en särskild behållargrupp. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/stop/action | Stoppar en särskild behållargrupp. Databearbetningen kommer att frigöra resurser och fakturering stoppas. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/write | Skapa eller uppdatera en särskild behållargrupp. |
> | Åtgärd | Microsoft.ContainerInstance/locations/cachedImages/read | Hämtar de cachelagra avbildningarna för prenumerationen i en region. |
> | Åtgärd | Microsoft.ContainerInstance/locations/capabilities/read | Hämta funktionerna för en region. |
> | Åtgärd | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.ContainerInstance att virtuella nätverket eller undernätet tas bort. |
> | Åtgärd | Microsoft.ContainerInstance/locations/usages/read | Hämta användningen för en specifik region. |
> | Åtgärd | Microsoft.ContainerInstance/register/action | Registrerar prenumerationen för container-instans-resursprovidern och gör det möjligt att skapa grupper med behållare. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontrollerar om namnet på behållarregistret är tillgängliga för användning. |
> | Åtgärd | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.ContainerRegistry att virtuella nätverket eller undernätet tas bort |
> | Åtgärd | Microsoft.ContainerRegistry/locations/operationResults/read | Hämtar en asynkron resultat |
> | Åtgärd | Microsoft.ContainerRegistry/operations/read | Visar en lista över alla tillgängliga Azure Container Registry REST API-åtgärder |
> | Åtgärd | Microsoft.ContainerRegistry/register/action | Registrerar prenumerationen för container registry-resursprovidern och gör möljligt behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/artifacts/delete | Ta bort artefakt i ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/cancel/action | Avbryter en version. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Hämtar en länk för att hämta build-loggarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/read | Hämtar egenskaperna för den angivna versionen eller listar alla versioner för angivna container registry. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/write | Uppdaterar en version för ett container registry med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/delete | Tar bort en build-uppgift från ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/listSourceRepositoryProperties/action | Visar en lista över egenskaper för datakälla-kontroll för en build-aktivitet. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/read | Hämtar egenskaperna för denna angivna build task eller listar alla build-uppgifter för det angivna container registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/steps/delete | Tar bort ett byggsteg från en build-aktivitet. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/steps/listBuildArguments/action | Visar en lista över build-argumenten för ett byggsteg inklusive hemliga argument. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/steps/read | Hämtar egenskaperna för den angivna byggsteg eller listar alla byggsteg för den angivna build-uppgiften. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/steps/write | Skapar eller uppdaterar ett byggsteg för en build-aktivitet med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/buildTasks/write | Skapar eller uppdaterar en build-uppgift för ett container registry med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/delete | Tar bort ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Tar bort en event grid-filtret från ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Hämtar egenskaperna för den angivna event grid-filtret eller listar alla event grid filter för det angivna container registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Skapar eller uppdaterar en event grid-filtret för ett container registry med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Hämtar överföringsplatsen för användaren ska kunna ladda upp källan. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/importImage/action | Importera avbildningen till behållarregistret med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Hämta källan överför url-plats för ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listCredentials/action | Visar en lista över inloggningsuppgifterna för angivna container registry. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listPolicies/read | Visar en lista över principerna för det angivna container registret |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listUsages/read | Visar en lista över kvot användningsområden för det angivna container registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/metadata/read | Hämtar metadata för en specifik lagringsplats för ett behållarregister |
> | Åtgärd | Microsoft.ContainerRegistry/registries/metadata/write | Uppdaterar metadata för en lagringsplats för ett behållarregister |
> | Åtgärd | Microsoft.ContainerRegistry/registries/operationStatuses/read | Hämtar en registret async Åtgärdsstatus |
> | Åtgärd | Microsoft.ContainerRegistry/registries/pull/read | Hämta eller hämta avbildningar från ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/push/write | Push- eller skriva avbildningar till ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/quarantineRead/read | Hämta eller hämta har satts i karantän bilder från container registry |
> | Åtgärd | Microsoft.ContainerRegistry/registries/quarantineWrite/write | Skriva/ändra karantän tillstånd har satts i karantän avbildningar |
> | Åtgärd | Microsoft.ContainerRegistry/registries/queueBuild/action | Skapar en ny version baserat på parametrarna och lägger till den i build-kön. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/read | Hämtar egenskaperna för det angivna behållarregistret eller listar alla behållarregister under den angivna resursgruppen eller prenumerationen. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Återskapar en av autentiseringsuppgifterna för det angivna container registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/delete | Tar bort en replikering från ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Hämtar en replikeringsstatus asynkrona åtgärden |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/read | Hämtar egenskaperna för den angivna replikeringen eller listar alla replikeringar för det angivna container registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/write | Skapar eller uppdaterar en replikering för ett container registry med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/runs/cancel/action | Avbryta en befintlig körning. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Hämtar loggen SAS-URL för en körning. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/runs/read | Hämtar egenskaperna för en körning mot ett behållarregister eller lista körningar. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/runs/write | Uppdaterar en körning. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/scheduleRun/action | Schemalägga en körning mot ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/sign/write | Flyttningar innehåll förtroende metadata för ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/delete | Tar bort en uppgift för ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Lista alla information om en uppgift för ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/read | Hämtar en uppgift för ett container registry eller lista alla uppgifter. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/write | Skapar eller uppdaterar en uppgift för ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/updatePolicies/write | Uppdaterar inställningen för det angivna container registret |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/delete | Tar bort en webhook från ett behållarregister. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Hämtar konfigurationen av tjänstens URI och anpassade huvuden för webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Visar en lista över de senaste händelserna för den angivna webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Hämtar en webhook async Åtgärdsstatus |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Utlöser en ping-händelse skickas till webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/read | Hämtar egenskaperna för den angivna webhooken eller listar alla webhooks för angivna container registry. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/write | Skapar eller uppdaterar en webbhook för ett container registry med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/write | Skapar eller uppdaterar ett behållarregister med de angivna parametrarna. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerService/containerServices/delete | Tar bort en behållartjänst |
> | Åtgärd | Microsoft.ContainerService/containerServices/read | Hämta en behållartjänst |
> | Åtgärd | Microsoft.ContainerService/containerServices/write | Skapar en ny behållartjänst eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/locations/operationresults/read | Hämtar status för en asynkron åtgärd-resultat |
> | Åtgärd | Microsoft.ContainerService/locations/operations/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft.ContainerService/locations/orchestrators/read | Visar en lista över initierare som stöds |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Få en profil för åtkomst av hanterade kluster efter rollnamn med listan över autentiseringsuppgifter |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/read | Få en profil för åtkomst av hanterade kluster efter rollnamn |
> | Åtgärd | Microsoft.ContainerService/managedClusters/agentPools/delete | Tar bort en agentpool |
> | Åtgärd | Microsoft.ContainerService/managedClusters/agentPools/read | Hämtar en agentpool |
> | Åtgärd | Microsoft.ContainerService/managedClusters/agentPools/write | Skapar en ny agentpool eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/managedClusters/delete | Tar bort ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lista över clusterAdmin autentiseringsuppgifterna för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista över clusterUser autentiseringsuppgifterna för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/read | Hämta ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Återställning av AAD-profilen för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Återställa tjänstens huvudnamn profilen för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Hämtar uppgraderingsprofil i klustret |
> | Åtgärd | Microsoft.ContainerService/managedClusters/write | Skapar ett nytt hanterade kluster eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/openShiftClusters/delete | Ta bort ett Open Shift-kluster |
> | Åtgärd | Microsoft.ContainerService/openShiftClusters/read | Hämta ett Open Shift-kluster |
> | Åtgärd | Microsoft.ContainerService/openShiftClusters/write | Skapar ett nytt kluster för Open Shift eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/openShiftManagedClusters/delete | Ta bort ett hanterat kluster Open Shift |
> | Åtgärd | Microsoft.ContainerService/openShiftManagedClusters/read | Hämta ett hanterat kluster Open Shift |
> | Åtgärd | Microsoft.ContainerService/openShiftManagedClusters/write | Skapar ett nytt Open Shift hanterade kluster eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/operations/read | Visar en lista över åtgärder som är tillgängliga på resursprovidern Microsoft.ContainerService |
> | Åtgärd | Microsoft.ContainerService/register/action | Registrerar prenumerationen med Microsoft.ContainerService-resursprovidern |
> | Åtgärd | Microsoft.ContainerService/unregister/action | Avregistrerar prenumeration med Microsoft.ContainerService-resursprovidern |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContentModerator/applications/delete | För att ta bort |
> | Åtgärd | Microsoft.ContentModerator/applications/listSecrets/action | En lista över hemligheterna |
> | Åtgärd | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Läsa token för enkel inloggning |
> | Åtgärd | Microsoft.ContentModerator/applications/read | Läsåtgärd |
> | Åtgärd | Microsoft.ContentModerator/applications/write | Skrivåtgärd |
> | Åtgärd | Microsoft.ContentModerator/applications/write | Skrivåtgärd |
> | Åtgärd | Microsoft.ContentModerator/listCommunicationPreference/action | Lista kommunikationsinställningar |
> | Åtgärd | Microsoft.ContentModerator/operations/read | läsåtgärder |
> | Åtgärd | Microsoft.ContentModerator/updateCommunicationPreference/action | Uppdatera kommunikationsinställningar |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/delete | Ta bort den angivna cloudConnector. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/read | Lista över cloudConnectors för autentiserade användare. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/write | Skapa eller uppdatera den angivna cloudConnector. |
> | Åtgärd | Microsoft.CostManagement/dimensions/read | Lista alla dimensioner av ett omfång. |
> | Åtgärd | Microsoft.CostManagement/exports/action | Kör den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/delete | Ta bort den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/read | Lista över export av omfång. |
> | Åtgärd | Microsoft.CostManagement/exports/run/action | Kör export. |
> | Åtgärd | Microsoft.CostManagement/exports/write | Skapa eller uppdatera den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Lista över externalSubscriptions inom en externalBillingAccount för autentiserade användare. |
> | Åtgärd | Microsoft.CostManagement/externalBillingAccounts/read | Lista över externalBillingAccounts för autentiserade användare. |
> | Åtgärd | Microsoft.CostManagement/externalSubscriptions/read | Lista över externalSubscriptions för autentiserade användare. |
> | Åtgärd | Microsoft.CostManagement/externalSubscriptions/write | Uppdatera associerade hanteringsgrupp av externalSubscription |
> | Åtgärd | Microsoft.CostManagement/query/action | Fråga användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/query/read | Fråga användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/register/action | Registrera åtgärden för omfånget av Microsoft.CostManagement av en prenumeration. |
> | Åtgärd | Microsoft.CostManagement/reports/action | Schema för rapporter om användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/reports/read | Schema för rapporter om användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/tenants/register/action | Registrera åtgärden för omfånget av Microsoft.CostManagement av en klient. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.CustomerInsights/hubs/adobemetadata/action | Skapa eller uppdatera alla Azure-kund insikter Adobe Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/adobemetadata/read | Läsa alla Azure-kund insikter Adobe Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Ta bort princip för delad åtkomst-signatur för alla Azure-kund insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Läsa delad Azure Customer Insights åtkomstprincip för signatur |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Återskapa primärnyckel i Azure Customer Insights signatur Policy för delad åtkomst |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Återskapa den sekundära nyckeln för Azure Customer Insights signatur Policy för delad åtkomst |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Skapa eller uppdatera princip för delad åtkomst-signatur för alla Azure-kund insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivera alla Azure-kund Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivera alla Azure-kund Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/delete | Ta bort alla Azure-kund Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Hämta alla Azure Customer Insights Connector Körningsstatus |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktivera alla Azure-kund Insights Connector-mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Ta bort alla Azure-kund Insights Connector-mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Läsa alla Azure Customer Insights Connector mappning Åtgärdsresultat |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Läsa alla Azure-kund Insights Connector-mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Skapa eller uppdatera alla Azure-kund Insights Connector-mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/read | Läsa alla Azure-kund Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Skapa eller uppdatera alla Azure-kund Insights Connector autentisering information om Gatewayanslutningen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/update/action | Uppdatera alla Azure-kund Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/write | Skapa eller uppdatera eventuella Azure Customer Insights-anslutningsprogram |
> | Åtgärd | Microsoft.CustomerInsights/hubs/crmmetadata/action | Skapa eller uppdatera alla Azure-kund insikter Crm-Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/crmmetadata/read | Läsa alla Azure-kund insikter Crm-Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/delete | Ta bort alla Azure-kund insikter hubb |
> | Åtgärd | Microsoft.CustomerInsights/hubs/gdpr/delete | Ta bort alla Azure-kund insikter Gdpr |
> | Åtgärd | Microsoft.CustomerInsights/hubs/gdpr/read | Läsa alla Azure-kund insikter Gdpr |
> | Åtgärd | Microsoft.CustomerInsights/hubs/gdpr/write | Skapa eller uppdatera alla Azure-kund insikter Gdpr |
> | Åtgärd | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Skaffa Azure Customer Insights Hub fakturering krediter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Hämta Azure Customer Insights Hub faktureringshistorik |
> | Åtgärd | Microsoft.CustomerInsights/hubs/images/delete | Ta bort alla Azure-kund insikter avbildning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/images/read | Läsa Azure Customer Insights bilder |
> | Åtgärd | Microsoft.CustomerInsights/hubs/images/write | Skapa eller uppdatera alla Azure-kund insikter avbildning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/delete | Ta bort eventuella azure Customer Insights interaktioner |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/operations/read | Läs Åtgärdsresultat för alla Azure kundinteraktion insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/read | Läs en Azure-kund insikter interaktion |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Föreslå relation länkar för alla Azure-kund insikter interaktioner |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/write | Skapa eller uppdatera en Azure-kund insikter interaktion |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/delete | Ta bort alla Azure-kund insikter KPI-indikator |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/operations/read | Läsa alla KPI för Azure Customer Insights Åtgärdsresultat |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/read | Läsa alla Azure-kund Insights-KPI |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Bearbeta alla Azure-kund insikter KPI: er |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/write | Skapa eller uppdatera alla Azure-kund Insights-KPI |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/delete | Ta bort alla Azure-kund insikter länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/read | Läsa alla Azure-kund insikter länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/write | Skapa eller uppdatera alla Azure-kund-länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/msemetadata/action | Skapa eller uppdatera alla Azure-kund insikter Mse Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/msemetadata/read | Läsa alla Azure-kund insikter Mse Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/operationresults/read | Hämta Åtgärdsresultat för Azure-kund insikter Hub |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/delete | Ta bort alla Azure-kund insikter förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/read | Läsa alla Azure-kund insikter förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/write | Skapa eller uppdatera alla Azure-kund-förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Ta bort alla Azure-kund insikter Prediktiv matchning principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights förutsägande matchning principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Läsa alla Azure-kund insikter Prediktiv matchning principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Skapa eller uppdatera eventuella Azure Customer Insights Prediktiv matchning principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/delete | Ta bort alla Azure-kund Insights-profilen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights-profilen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/read | Läsa alla Azure-kund Insights-profilen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/write | Skriva eventuella Azure Customer Insights-profilen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/read | Läsa alla Azure-kund insikter Hub |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Ta bort alla Azure-kund insikter relation länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights relation länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Läsa alla Azure-kund insikter relation länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Skapa eller uppdatera alla Azure-kund insikter relation länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/delete | Ta bort alla Azure-kund Insights relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/operations/read | Läs Åtgärdsresultat för alla Azure Insights kundrelationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/read | Läsa Azure Customer Insights relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/write | Skapa eller uppdatera Azure Customer Insights relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Ta bort alla Azure-kund insikter Rbac-tilldelning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Läs Åtgärdsresultat för alla Azure Customer Insights Rbac tilldelning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/read | Läsa alla Azure-kund insikter Rbac-tilldelning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/write | Skapa eller uppdatera alla Azure-kund insikter Rbac-tilldelning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roles/read | Läsa alla Azure-kund insikter Rbac-roller |
> | Åtgärd | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Skapa eller uppdatera alla Azure-kund insikter SalesForce-Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Läsa alla Azure-kund insikter SalesForce-Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/delete | Ta bort alla Azure-kund insikter segment |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Hantering av alla Azure-kund Insight dynamiska segment |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/read | Läsa alla Azure-kund insikter segment |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/static/action | Hantering av alla Azure-kund Insight statiska segment |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/write | Skapa eller uppdatera eventuella Azure kundsegment insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Ta bort alla Azure-kund insikter SqlConnectionStrings |
> | Åtgärd | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Läsa alla Azure-kund insikter SqlConnectionStrings |
> | Åtgärd | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Skapa eller uppdatera alla Azure-kund insikter SqlConnectionStrings |
> | Åtgärd | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generera föreslår typ schemat från exempeldata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Hantera alla Azure Customer Insights hub-inställningar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/views/delete | Ta bort alla Azure-kund insikter App-vy |
> | Åtgärd | Microsoft.CustomerInsights/hubs/views/read | Läsa alla Azure-kund Insights App-vy |
> | Åtgärd | Microsoft.CustomerInsights/hubs/views/write | Skapa eller uppdatera alla Azure-kund Insights App-vy |
> | Åtgärd | Microsoft.CustomerInsights/hubs/widgettypes/read | Läs några Azure-kund insikter App Widget typer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/write | Skapa eller uppdatera alla Azure-kund insikter Hub |
> | Åtgärd | Microsoft.CustomerInsights/operations/read | Läsa Azure Customer Insights Api Metadatas |
> | Åtgärd | Microsoft.CustomerInsights/register/action | Registrerar prenumerationen för Customer Insights-resursprovidern och gör det möjligt att skapa Customer Insights-resurser |
> | Åtgärd | Microsoft.CustomerInsights/unregister/action | Avregistrerar prenumerationen för Customer Insights-resursprovidern |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Ger möjlighet för att boka en returleveranser. |
> | Åtgärd | Microsoft.DataBox/jobs/cancel/action | Avbryter en pågående beställning. |
> | Åtgärd | Microsoft.DataBox/jobs/delete | Ta bort order |
> | Åtgärd | Microsoft.DataBox/jobs/listCredentials/action | Visar en lista över okrypterade autentiseringsuppgifter beställningen. |
> | Åtgärd | Microsoft.DataBox/jobs/read | Visa eller hämta beställningar |
> | Åtgärd | Microsoft.DataBox/jobs/write | Skapa eller uppdatera order |
> | Åtgärd | Microsoft.DataBox/locations/availableSkus/action | Den här metoden returnerar listan över tillgängliga SKU: er. |
> | Åtgärd | Microsoft.DataBox/locations/operationResults/read | Lista eller hämta den Åtgärdsresultat |
> | Åtgärd | Microsoft.DataBox/locations/validateAddress/action | Verifierar leveransadressen och anger alternativa adresser om sådana. |
> | Åtgärd | Microsoft.DataBox/register/action | Registrera providern Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Visar en lista över eller hämtar aviseringarna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Visar en lista över eller hämtar aviseringarna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Tar bort bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Visar en lista över eller hämtar bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Visar en lista över eller hämtar bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Skapar eller uppdaterar bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Tar bort Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Ladda ned uppdateringar i enhet |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | Hämtar resurs utökad information |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Installera uppdateringar på enheten |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Visar en lista över eller hämtar jobb |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Visar en lista över eller hämtar nätverksinställningarna för enheten |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationsStatus/read | Visar en lista över eller hämtar Åtgärdsstatus |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Tar bort order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Visar en lista över eller hämtar order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Visar en lista över eller hämtar order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Skapar eller uppdaterar order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Visar en lista över eller hämtar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Visar en lista över eller hämtar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Visar en lista över eller hämtar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Tar bort rollerna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Hämtar rollerna eller visar en lista över |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Hämtar rollerna eller visar en lista över |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Skapar eller uppdaterar rollerna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Söka efter uppdateringar |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Uppdatera säkerhetsinställningar |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Tar bort filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Visar en lista över eller hämtar filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Visar en lista över eller hämtar filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Uppdatera metadata för resursen med data från molnet |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Skapar eller uppdaterar filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Tar bort autentiseringsuppgifter för lagringskonto |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Visar en lista över eller hämtar autentiseringsuppgifterna för lagringskontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Visar en lista över eller hämtar autentiseringsuppgifterna för lagringskontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Skapar eller uppdaterar autentiseringsuppgifterna för lagringskontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Tar bort utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Visar en lista över eller hämtar utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Visar en lista över eller hämtar utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Skapar eller uppdaterar utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Visar en lista över eller hämtar uppdateringen sammanfattning |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Överför certifikat för registrering av enheten |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Tar bort användare av den |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Visar en lista över eller hämtar resultat |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Hämtar användarna eller visar en lista över |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Hämtar användarna eller visar en lista över |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Skapar eller uppdaterar användarna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Skapar eller uppdaterar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Skapar eller uppdaterar Data Box Edge-enheter |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Databricks/register/action | Registrera på Databricks. |
> | Åtgärd | Microsoft.Databricks/workspaces/delete | Tar bort en Databricks-arbetsyta. |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Anger tillgängliga diagnostikinställningarna för Databricks-arbetsyta |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för Databricks-arbetsyta |
> | Åtgärd | Microsoft.Databricks/workspaces/read | Hämtar en lista över Databricks-arbetsytor. |
> | Åtgärd | Microsoft.Databricks/workspaces/write | Skapar en Databricks-arbetsyta. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataCatalog/catalogs/delete | Ta bort kataloger resurs för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/read | Läs kataloger resurs för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/write | Skriv kataloger resurs för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/checkNameAvailability/read | Kontrollera namntillgänglighet för katalogen för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/datacatalogs/delete | Ta bort DataCatalog resurs för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/datacatalogs/read | Läs DataCatalog resurs för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/datacatalogs/write | Skriv DataCatalog resurs för Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/operations/read | Läser alla tillgängliga åtgärder i Data Catalog-Resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/register/action | Registrera prenumeration för Resursprovidern för Data Catalog |
> | Åtgärd | Microsoft.DataCatalog/unregister/action | Avregistrera prenumerationen för Data Catalog-Resursprovidern |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Kontrollerar om Datafabrikens namn kan användas. |
> | Åtgärd | Microsoft.DataFactory/datafactories/activitywindows/read | Läser aktivitet Windows i Datafabriken med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Läser aktivitet Windows för Pipeline-aktivitet med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Läser aktivitet Windows för pipelinen med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/delete | Tar bort en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pausar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/read | Läser en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Återupptar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/update/action | Uppdaterar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/write | Skapar eller uppdaterar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Läser aktivitet Windows för datauppsättningen med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/delete | Tar bort alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/read | Läser alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Läser Data-segmentet som körs för den angivna datauppsättningen med den angivna starttiden. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/slices/read | Hämtar Datasektorer i den angivna perioden. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/slices/write | Uppdatera Status för Datasektorn. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/write | Skapar eller uppdaterar alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/delete | Tar bort Data Factory. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Läser anslutningsinformationen för en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/delete | Tar bort en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Visar en lista över autentiseringsnycklar för en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/read | Läser en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Återskapar autentiseringsnycklar för en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/write | Skapar eller uppdaterar en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/linkedServices/delete | Tar bort alla länkade tjänst. |
> | Åtgärd | Microsoft.DataFactory/datafactories/linkedServices/read | Läser alla länkade tjänsten. |
> | Åtgärd | Microsoft.DataFactory/datafactories/linkedServices/write | Skapar eller uppdaterar någon länkad tjänst. |
> | Åtgärd | Microsoft.DataFactory/datafactories/read | Läser Data Factory. |
> | Åtgärd | Microsoft.DataFactory/datafactories/runs/loginfo/read | Läser en SAS-URI till en blobbehållare som innehåller loggarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/delete | Tar bort alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/read | Läser alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/write | Skapar eller uppdaterar alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/write | Skapar eller uppdaterar Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/cancelpipelinerun/action | Avbryter pipelinekörningen som anges av körnings-ID. |
> | Åtgärd | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Skapar en felsökningssession för flödet av data. |
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/delete | Tar bort dataflöde. |
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/read | Läser dataflöde. |
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/write | Skapa eller uppdatera dataflöde |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/delete | Tar bort alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/read | Läser alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/write | Skapar eller uppdaterar alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/factories/delete | Deletes Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Tar bort en felsökningssession för flödet av data. |
> | Åtgärd | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Hämtar åtkomst till ADF dataplanen-tjänsten. |
> | Åtgärd | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Läsningar åtkomst till ADF dataplanen-tjänsten. |
> | Åtgärd | Microsoft.DataFactory/factories/getFeatureValue/action | Hämta exponering kontroll funktionen värdet för den specifika användarplatsen. |
> | Åtgärd | Microsoft.DataFactory/factories/getFeatureValue/read | Läser exponering kontroll funktionen värde för den specifika användarplatsen. |
> | Åtgärd | Microsoft.DataFactory/factories/getGitHubAccessToken/action | Hämtar GitHub åtkomst-token. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/delete | Tar bort alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Läser anslutningsinformation för Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Hämta SSIS Integration Runtime-metadata för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Läser Status för Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Skapa länkade Integration Runtime-referens för den angivna dela Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Visar en lista över autentiseringsnycklar för alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Hämtar övervakningsdata för alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Tar bort noden för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Returnerar IP-adressen för den angivna noden för Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Läser noden för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Uppdaterar en lokal Integration Runtime-noden. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/read | Läser alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Uppdatera SSIS Integration Runtime-metadata för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Återskapar nycklar för autentisering för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Tar bort länkade Integration Runtime referenser från den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/start/action | Startar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Stoppar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synkroniserar autentiseringsuppgifterna för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uppgraderar angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/write | Skapar eller uppdaterar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/linkedServices/delete | Tar bort länkad tjänst. |
> | Åtgärd | Microsoft.DataFactory/factories/linkedServices/read | Läsningar länkad tjänst. |
> | Åtgärd | Microsoft.DataFactory/factories/linkedServices/write | Skapa eller uppdatera länkade tjänst |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Läser aktivitetskörningar för den angivna pipelinekörnings-ID. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/cancel/action | Avbryter pipelinekörningen som anges av körnings-ID. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/action | Frågor som aktiviteten körs för angivna pipelinen körs ID. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/queryactivityruns/read | Läser resultatet av frågeaktivitet körs i den angivna pipelinekörnings-ID. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/read | Läser Pipeline-körningar. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/createrun/action | Skapar en körning för pipelinen. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/delete | Tar bort Pipeline. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/pipelineruns/activityruns/progress/read | Hämtar förloppet för Aktivitetskörningar. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/pipelineruns/read | Läser Pipeline-körning. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/read | Läser Pipeline. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/write | Skapa eller uppdatera pipelinen |
> | Åtgärd | Microsoft.DataFactory/factories/querydebugpipelineruns/action | Frågar Pipelinekörningar för felsökning. |
> | Åtgärd | Microsoft.DataFactory/factories/querypipelineruns/action | Frågar Pipeline-körningar. |
> | Åtgärd | Microsoft.DataFactory/factories/querypipelineruns/read | Läser resultatet av frågan Pipelinekörningar. |
> | Åtgärd | Microsoft.DataFactory/factories/querytriggerruns/action | Frågar Trigger Runs. |
> | Åtgärd | Microsoft.DataFactory/factories/querytriggerruns/read | Läser resultatet av utlösaren körs. |
> | Åtgärd | Microsoft.DataFactory/factories/read | Läser Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Startar en felsökningssession för flödet av data. |
> | Åtgärd | Microsoft.DataFactory/factories/triggerruns/read | Läser Trigger Runs. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/delete | Tar bort alla utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/read | Läser en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/start/action | Startar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/stop/action | Stoppar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/triggerruns/read | Läser Trigger Runs. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/write | Skapar eller uppdaterar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/write | Skapa eller uppdatera Data Factory |
> | Åtgärd | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfigurerar lagringsplatsen för fabriken. |
> | Åtgärd | Microsoft.DataFactory/locations/getFeatureValue/action | Hämta exponering kontroll funktionen värdet för den specifika användarplatsen. |
> | Åtgärd | Microsoft.DataFactory/locations/getFeatureValue/read | Läser exponering kontroll funktionen värde för den specifika användarplatsen. |
> | Åtgärd | Microsoft.DataFactory/operations/read | Läser alla åtgärder i Microsoft Data Factory-providern. |
> | Åtgärd | Microsoft.DataFactory/register/action | Registrerar prenumerationen för Data Factory-Resursprovidern. |
> | Åtgärd | Microsoft.DataFactory/unregister/action | Avregistrerar prenumerationen för Data Factory-Resursprovidern. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Ta bort en princip för beräkning. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Få information om en princip för beräkning. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Skapa eller uppdatera en princip för beräkning. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Ta bort länk till ett DataLakeStore-konto från ett DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Få information om ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/delete | Ta bort ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Ta bort en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Få information om en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Skapa eller uppdatera en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Hämta resultatet av en DataLakeAnalytics konto igen. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/read | Få information om ett befintligt DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Lista över SAS-token för storage-behållare för ett länkat Storage-konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Hämta behållare för ett länkat Storage-konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Ta bort länk till ett lagringskonto från ett DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Få information om ett länkat lagringskonto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Skapa eller uppdatera ett länkat lagringskonto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Beviljar behörigheter att avbryta jobb som skickats av andra användare. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Överföra SystemMaxAnalyticsUnits mellan DataLakeAnalytics-konton. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Ta bort en regel för virtuella nätverk. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Få information om en regel för virtuella nätverk. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Skapa eller uppdatera en virtuell nätverksregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/capability/read | Hämta information om kapaciteten för en prenumeration angående med hjälp av DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Kontrollera tillgängligheten för namnet på ett DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/operationResults/read | Hämta resultatet av en DataLakeAnalytics konto igen. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/usages/read | Hämta kvotinformation användningsområden för en prenumeration angående med hjälp av DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/operations/read | Hämta tillgängliga åtgärder av DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/register/action | Registrera prenumeration datalakeanalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataLakeStore/accounts/delete | Ta bort ett DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Aktivera KeyVault för ett DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Ta bort ett EventGrid-Filter. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Få ett EventGrid-Filter. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Skapa eller uppdatera ett EventGrid-Filter. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/delete | Ta bort en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/read | Få information om en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/write | Skapa eller uppdatera en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/operationResults/read | Hämta resultatet av en åtgärd för DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/read | Få information om ett befintligt DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/Superuser/action | Bevilja superanvändare på Data Lake Store när den beviljas med Microsoft.Authorization/roleAssignments/write. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Ta bort en betrodd identitetsleverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Få information om en betrodd identitetsleverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Skapa eller uppdatera en betrodd identitetsleverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Ta bort en regel för virtuella nätverk. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Få information om en regel för virtuella nätverk. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Skapa eller uppdatera en virtuell nätverksregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/write | Skapa eller uppdatera ett DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/locations/capability/read | Hämta information om kapaciteten för en prenumeration angående med hjälp av DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Kontrollera tillgängligheten för namnet på ett DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/locations/operationResults/read | Hämta resultatet av en åtgärd för DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/locations/usages/read | Hämta kvotinformation användningsområden för en prenumeration angående med hjälp av DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/operations/read | Hämta tillgängliga åtgärder av DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/register/action | Registrera prenumeration datalakestore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataMigration/locations/operationResults/read | Hämta status för en tidskrävande åtgärd som berör 202 accepterade svar |
> | Åtgärd | Microsoft.DataMigration/locations/operationStatuses/read | Hämta status för en tidskrävande åtgärd som berör 202 accepterade svar |
> | Åtgärd | Microsoft.DataMigration/register/action | Registrerar prenumerationen med Azure Database Migration Service-providern |
> | Åtgärd | Microsoft.DataMigration/services/addWorker/action | Lägger till en DMS-arbetare i tjänstens är tillgänglig arbetare |
> | Åtgärd | Microsoft.DataMigration/services/checkStatus/action | Kontrollera om tjänsten distribueras och körs |
> | Åtgärd | Microsoft.DataMigration/services/configureWorker/action | Konfigurerar en DMS worker till tjänstens är tillgänglig arbetare |
> | Åtgärd | Microsoft.DataMigration/services/delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft.DataMigration/services/projects/accessArtifacts/action | Generera en URL som kan användas för att hämta eller PUT-projektsartefakter |
> | Åtgärd | Microsoft.DataMigration/services/projects/delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/read/action | Hämta en URL som kan användas för att läsa innehållet i filen |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/readWrite/action | Hämta en URL som kan användas för att läsa eller skriva innehållet i filen |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/services/projects/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/cancel/action | Avbryt uppgiften om den körs |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/write | Köra uppgifter uppgifter för Azure Database Migration Service |
> | Åtgärd | Microsoft.DataMigration/services/projects/write | Köra uppgifter uppgifter för Azure Database Migration Service |
> | Åtgärd | Microsoft.DataMigration/services/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/removeWorker/action | Tar bort en DMS worker till tjänstens är tillgänglig arbetare |
> | Åtgärd | Microsoft.DataMigration/services/serviceTasks/cancel/action | Avbryt uppgiften om den körs |
> | Åtgärd | Microsoft.DataMigration/services/serviceTasks/delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft.DataMigration/services/serviceTasks/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/serviceTasks/write | Köra uppgifter uppgifter för Azure Database Migration Service |
> | Åtgärd | Microsoft.DataMigration/services/slots/delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft.DataMigration/services/slots/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/slots/write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/services/start/action | Starta DMS-tjänsten så att den kan behandla migreringar igen |
> | Åtgärd | Microsoft.DataMigration/services/stop/action | Stoppa DMS-tjänsten för att kostnaderna minimeras |
> | Åtgärd | Microsoft.DataMigration/services/updateAgentConfig/action | Uppdaterar DMS agentkonfiguration med angivna värden. |
> | Åtgärd | Microsoft.DataMigration/services/write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/skus/read | Hämta en lista över SKU: er som stöds av DMS-resurser. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforMariaDB/checkNameAvailability/action | Kontrollera om namn angivna servern är tillgänglig för etablering i hela världen för en viss prenumeration. |
> | Åtgärd | Microsoft.DBforMariaDB/locations/azureAsyncOperation/read | Returnera MariaDB Server Åtgärdsresultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/operationResults/read | Gå tillbaka ResourceGroup baserat MariaDB Server Åtgärdsresultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera MariaDB Server Åtgärdsresultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/read | Returnera listan över Server threat Identifieringsresultat igen. |
> | Åtgärd | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Returnera listan över Server threat Identifieringsresultat igen. |
> | Åtgärd | Microsoft.DBforMariaDB/operations/read | Returnera listan över MariaDB-åtgärder. |
> | Åtgärd | Microsoft.DBforMariaDB/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMariaDB/register/action | Registrera Resursprovidern för MariaDB |
> | Åtgärd | Microsoft.DBforMariaDB/servers/administrators/delete | Tar bort en befintlig administratör för MariaDB-server. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/administrators/read | Hämtar en lista över MariaDB serveradministratörer. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/administrators/write | Skapar eller uppdaterar MariaDB serveradministratör med de angivna parametrarna. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/advisors/createRecommendedActionSession/action | Skapa en ny rekommendation åtgärd session |
> | Åtgärd | Microsoft.DBforMariaDB/servers/advisors/read | Returnera lista över rådgivare |
> | Åtgärd | Microsoft.DBforMariaDB/servers/advisors/read | Returnera en advisor |
> | Åtgärd | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Returnera listan med rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Returnera listan med rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforMariaDB/servers/advisors/recommendedActions/read | Returnera en rekommenderad åtgärd |
> | Åtgärd | Microsoft.DBforMariaDB/servers/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/databases/delete | Tar bort en befintlig databas för MariaDB. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/databases/read | Returnera listan över MariaDB databaser eller hämtar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/databases/write | Skapar en databas för MariaDB med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/logFiles/read | Returnera listan över MariaDB LogFiles. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för MariaDB-servrar |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texten till en lista över frågor |
> | Åtgärd | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texten i en fråga |
> | Åtgärd | Microsoft.DBforMariaDB/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/recoverableServers/read | Returnera den återställningsbara MariaDB Server-informationen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/replicas/read | Få läsa replikeringar av en MariaDB-server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/restart/action | Startar om en specifik server. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Returnera listan över Frågestatistik för de viktigaste frågorna. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Returnera en fråga statistik |
> | Åtgärd | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/waitStatistics/read | Vänta statistik för en instans |
> | Åtgärd | Microsoft.DBforMariaDB/servers/waitStatistics/read | Returnera en wait-statistik |
> | Åtgärd | Microsoft.DBforMariaDB/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforMySQL/checkNameAvailability/action | Kontrollera om namn angivna servern är tillgänglig för etablering i hela världen för en viss prenumeration. |
> | Åtgärd | Microsoft.DBforMySQL/locations/azureAsyncOperation/read | Returnera resultat för MySQL Server igen |
> | Åtgärd | Microsoft.DBforMySQL/locations/operationResults/read | Gå tillbaka ResourceGroup baserat Åtgärdsresultat för MySQL-Server |
> | Åtgärd | Microsoft.DBforMySQL/locations/operationResults/read | Returnera resultat för MySQL Server igen |
> | Åtgärd | Microsoft.DBforMySQL/locations/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Returnera listan över Server threat Identifieringsresultat igen. |
> | Åtgärd | Microsoft.DBforMySQL/locations/securityAlertPoliciesOperationResults/read | Returnera listan över Server threat Identifieringsresultat igen. |
> | Åtgärd | Microsoft.DBforMySQL/operations/read | Returnera listan över MySQL-åtgärder. |
> | Åtgärd | Microsoft.DBforMySQL/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMySQL/register/action | Registrera Resursprovidern för MySQL |
> | Åtgärd | Microsoft.DBforMySQL/servers/administrators/delete | Tar bort en befintlig administratör för MySQL-server. |
> | Åtgärd | Microsoft.DBforMySQL/servers/administrators/read | Hämtar en lista över MySQL server-administratörer. |
> | Åtgärd | Microsoft.DBforMySQL/servers/administrators/write | Skapar eller uppdaterar MySQL server-administratören med de angivna parametrarna. |
> | Åtgärd | Microsoft.DBforMySQL/servers/advisors/createRecommendedActionSession/action | Skapa en ny rekommendation åtgärd session |
> | Åtgärd | Microsoft.DBforMySQL/servers/advisors/read | Returnera lista över rådgivare |
> | Åtgärd | Microsoft.DBforMySQL/servers/advisors/read | Returnera en advisor |
> | Åtgärd | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Returnera listan med rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Returnera listan med rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforMySQL/servers/advisors/recommendedActions/read | Returnera en rekommenderad åtgärd |
> | Åtgärd | Microsoft.DBforMySQL/servers/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforMySQL/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforMySQL/servers/databases/delete | Tar bort en befintlig MySQL-databas. |
> | Åtgärd | Microsoft.DBforMySQL/servers/databases/read | Returnera listan med MySQL-databaser eller hämtar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforMySQL/servers/databases/write | Skapar en MySQL-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforMySQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/logFiles/read | Returnera listan över PostgreSQL LogFiles. |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för MySQL-servrar |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforMySQL/servers/queryTexts/action | Returnera texten till en lista över frågor |
> | Åtgärd | Microsoft.DBforMySQL/servers/queryTexts/action | Returnera texten i en fråga |
> | Åtgärd | Microsoft.DBforMySQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforMySQL/servers/recoverableServers/read | Returnera den återställningsbara MySQL-Server-informationen |
> | Åtgärd | Microsoft.DBforMySQL/servers/replicas/read | Få läsa replikeringar av en MySQL-server |
> | Åtgärd | Microsoft.DBforMySQL/servers/restart/action | Startar om en specifik server. |
> | Åtgärd | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Returnera listan över Frågestatistik för de viktigaste frågorna. |
> | Åtgärd | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Returnera en fråga statistik |
> | Åtgärd | Microsoft.DBforMySQL/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/waitStatistics/read | Vänta statistik för en instans |
> | Åtgärd | Microsoft.DBforMySQL/servers/waitStatistics/read | Returnera en wait-statistik |
> | Åtgärd | Microsoft.DBforMySQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Kontrollera om namn angivna servern är tillgänglig för etablering i hela världen för en viss prenumeration. |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Returnera resultat från åtgärd för PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/operationResults/read | Gå tillbaka ResourceGroup baserat Åtgärdsresultat för PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/operationResults/read | Returnera resultat från åtgärd för PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Returnera listan över Server threat Identifieringsresultat igen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Returnera listan över Server threat Identifieringsresultat igen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/operations/read | Returnera listan över åtgärder för PostgreSQL. |
> | Åtgärd | Microsoft.DBforPostgreSQL/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforPostgreSQL/register/action | Registrera Resursprovidern med PostgreSQL |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/administrators/delete | Tar bort en befintlig administratör för PostgreSQL-server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/administrators/read | Hämtar en lista över PostgreSQL server-administratörer. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/administrators/write | Skapar eller uppdaterar PostgreSQL server-administratören med de angivna parametrarna. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/read | Returnera lista över rådgivare |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Returnera listan med rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Se rekommendationer |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/databases/delete | Tar bort en befintlig databas för PostgreSQL. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/databases/read | Returnera listan med PostgreSQL-databaser eller hämtar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/databases/write | Skapar en PostgreSQL-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/logFiles/read | Returnera listan över PostgreSQL LogFiles. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för Postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Returnera texten i en fråga |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Returnera texten till en lista över frågor |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Returnera den återställningsbara PostgreSQL-Server-informationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/replicas/read | Få läsa replikeringar av en PostgreSQL-server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/restart/action | Startar om en specifik server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Returnera listan över Frågestatistik för de viktigaste frågorna. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Vänta statistik för en instans |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för Postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Devices/checkNameAvailability/Action | Kontrollera om IotHub-namn är tillgänglig |
> | Åtgärd | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Kontrollera om etablering tjänstnamnet är tillgänglig |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Skapa Verifieringskod |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifiera certifikat-resurs |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Ta bort resursen för IotHub-klient |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Ta bort konsumentgrupp för EventHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Hämta EventHub konsumentgrupper |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Skapa konsumentgrupp för EventHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportera enheter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Hämtar IotHub-klient stats resurs |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importera enheter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Hämtar IotHub-klientnyckel |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Hämta jobb information som skickade in den angivna IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Hämtar IotHub klientnycklar |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för IotHub-Service |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Hämta kvot mått |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Read | Hämtar resurs för IotHub-klient |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testmeddelande mot alla befintliga vägar |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testmeddelande mot ett angivet test väg |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Hämtar hälsotillståndet för alla slutpunkter som routning för en IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Write | Skapa eller uppdatera resursen för IotHub-klient |
> | Åtgärd | Microsoft.Devices/ElasticPools/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Skapa Verifieringskod |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/verify/Action | Verifiera certifikat-resurs |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/Delete | Ta bort IotHub-resurs |
> | Åtgärd | Microsoft.Devices/IotHubs/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/IotHubs/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Tar bort Event Grid-filtret |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Read | Hämtar Event Grid-filtret |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Write | Skapa en ny eller uppdatera befintlig Event Grid-filtret |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Ta bort konsumentgrupp för EventHub |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Hämta EventHub konsumentgrupper |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Skapa konsumentgrupp för EventHub |
> | Åtgärd | Microsoft.Devices/iotHubs/exportDevices/Action | Exportera enheter |
> | Åtgärd | Microsoft.Devices/iotHubs/importDevices/Action | Importera enheter |
> | Åtgärd | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Hämta IotHub Key för det angivna namnet |
> | Åtgärd | Microsoft.Devices/iotHubs/iotHubStats/Read | Få IotHub-statistik |
> | Åtgärd | Microsoft.Devices/iotHubs/jobs/Read | Hämta jobb information som skickade in den angivna IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/listkeys/Action | Hämta alla IotHub-nycklar |
> | Åtgärd | Microsoft.Devices/IotHubs/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för IotHub-Service |
> | Åtgärd | Microsoft.Devices/IotHubs/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/iotHubs/operationresults/Read | Hämta Åtgärdsresultat (föråldrade API) |
> | Åtgärd | Microsoft.Devices/iotHubs/quotaMetrics/Read | Hämta kvot mått |
> | Åtgärd | Microsoft.Devices/iotHubs/Read | Hämtar IotHub-resurser |
> | Åtgärd | Microsoft.Devices/iotHubs/routing/$testall/Action | Testmeddelande mot alla befintliga vägar |
> | Åtgärd | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testmeddelande mot ett angivet test väg |
> | Åtgärd | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Hämtar hälsotillståndet för alla slutpunkter som routning för en IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/skus/Read | Hämta giltiga IotHub Skus |
> | Åtgärd | Microsoft.Devices/iotHubs/Write | Skapa eller uppdatera IotHub-resurs |
> | Åtgärd | Microsoft.Devices/locations/operationresults/Read | Hämta platsbaserade Åtgärdsresultat |
> | Åtgärd | Microsoft.Devices/operationresults/Read | Hämta Åtgärdsresultat |
> | Åtgärd | Microsoft.Devices/operations/Read | Hämta alla ResourceProvider-åtgärder |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Skapa Verifieringskod |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verifiera certifikat-resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/Delete | Ta bort IotDps resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Hämta IotDps nycklar efter nyckelnamn |
> | Åtgärd | Microsoft.Devices/provisioningServices/listkeys/Action | Hämta alla IotDps nycklar |
> | Åtgärd | Microsoft.Devices/provisioningServices/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för Etableringstjänsten |
> | Åtgärd | Microsoft.Devices/provisioningServices/metricDefinitions/read | Hämtar tillgängliga mått för etableringstjänsten |
> | Åtgärd | Microsoft.Devices/provisioningServices/operationresults/Read | Hämta Åtgärdsresultat för DPS |
> | Åtgärd | Microsoft.Devices/provisioningServices/Read | Hämta IotDps resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/skus/Read | Hämta giltiga IotDps Skus |
> | Åtgärd | Microsoft.Devices/provisioningServices/Write | Skapa IotDps resurs |
> | Åtgärd | Microsoft.Devices/register/action | Registrera prenumerationen för IotHub-resursprovidern och gör det möjligt att skapa IotHub-resurser |
> | Åtgärd | Microsoft.Devices/register/action | Registrera prenumerationen för IotHub-resursprovidern och gör det möjligt att skapa IotHub-resurser |
> | Åtgärd | Microsoft.Devices/usages/Read | Hämta prenumeration användningsinformation för den här providern. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DevSpaces/controllers/delete | Ta bort Azure Dev blanksteg Controller och dataplanen |
> | Åtgärd | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Lista anslutningsinformationen för den Azure Dev blanksteg kontrollantens infrastruktur |
> | Åtgärd | Microsoft.DevSpaces/controllers/read | Läs Azure Dev blanksteg Controller egenskaper |
> | Åtgärd | Microsoft.DevSpaces/controllers/write | Skapa eller uppdatera Azure Dev blanksteg Controller egenskaper |
> | Åtgärd | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Kontrollera den befintliga domänkontrollanten mappningen för en behållarvärd |
> | Åtgärd | Microsoft.DevSpaces/locations/operationresults/read | Läsa status för en asynkron åtgärd |
> | Åtgärd | Microsoft.DevSpaces/register/action | Registrera resursprovidern i Microsoft Dev blanksteg med en prenumeration |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DevTestLab/labCenters/delete | Ta bort lab datacenter. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/read | Läsa lab datacenter. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/write | Lägg till eller ändra labb datacenter. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Läsa azure resource manager-mallar. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Genererar en ARM-mall för den angivna artefakten, överför de nödvändiga filerna till ett lagringskonto och verifierar den genererade artefakten. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Läsa artefakter. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/delete | Ta bort artefaktkällor. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/read | Läsa artefaktkällor. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/write | Lägg till eller ändra artefaktkällor. |
> | Åtgärd | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Gör anspråk på en slumpmässig tillgängliga virtuell dator i labbet. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/read | Läsa kostnader. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/write | Lägg till eller ändra kostnader. |
> | Åtgärd | Microsoft.DevTestLab/labs/CreateEnvironment/action | Skapa virtuella datorer i ett labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/delete | Ta bort anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/read | Läsa anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/write | Lägg till eller ändra anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/delete | Ta bort labs. |
> | Åtgärd | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Se till att den aktuella användaren har en giltig profil i laboratoriet. |
> | Åtgärd | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporterar resursanvändningen lab till ett lagringskonto |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/read | Läsa formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/galleryImages/read | Läsa galleriavbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generera en URI för att ladda upp anpassade diskavbildningar till ett labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/idleShutdowns/delete | Ta bort inaktiva avstängningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/idleShutdowns/read | Läsa inaktiv avstängningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/idleShutdowns/write | Lägg till eller ändra inaktiv avstängningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importera en virtuell dator till ett annat labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/ListVhds/action | Lista över diskavbildningar som är tillgängliga för att skapa en anpassad avbildning. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/delete | Ta bort notificationchannels. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Skicka meddelande till angivna kanalen. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/read | Läsa notificationchannels. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/write | Lägg till eller ändra notificationchannels. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Utvärderar lab principen. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/delete | Ta bort principer. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/read | Läsa principer. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/write | Lägg till eller ändra principer. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/read | Läsa principuppsättningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/read | Läsa labs. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Visar en lista över alla tillämpliga scheman |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/delete | Ta bort tjänsten deltagare. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/read | Läsa service deltagare. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/write | Lägg till eller ändra service deltagare. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/delete | Ta bort delade gallerier. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/read | Läsa delade gallerier. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Ta bort delade bilder. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Läsa delade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Lägg till eller ändra delad bilderna. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/write | Lägg till eller ändra delad gallerier. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/delete | Tar bort användarprofiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Attach/action | Ansluta och skapa lånet på disken för att den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/delete | Ta bort diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Detach/action | Koppla från och avbryta lånet av den disk som är ansluten till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/read | Läsa diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/write | Lägg till eller ändra diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/read | Läs miljö. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/read | Läsa användarprofiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/delete | Ta bort hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/read | Läsa hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/write | Lägg till eller ändra hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Ta bort service fabrics. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Visar en lista över tillämpliga Starta/Stoppa scheman, om sådana. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Läsa service fabrics. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Starta ett service fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stoppa ett service fabric |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Lägg till eller ändra service fabrics. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/write | Lägg till eller ändra användarprofiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Koppla en ny eller befintlig datadisk till virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Tillämpa artefakter till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Bli ägare till en befintlig virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/delete | Ta bort virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Koppla från den angivna disken från den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Visar en lista över tillämpliga Starta/Stoppa scheman, om sådana. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/read | Läsa virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Distribuera om en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ändra storlek på virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Starta om en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Starta en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Stoppa en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Överför alla diskar som är anslutna till den virtuella datorn som ägs av den aktuella användaren. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Versionen ägarskap för en befintlig virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/write | Lägg till eller ändra virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/delete | Ta bort virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/read | Läsa virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/write | Lägg till eller ändra virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/delete | Ta bort virtual machine-pooler. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/read | Läsa virtuella datorpooler. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/write | Lägg till eller ändra virtual machine-pooler. |
> | Åtgärd | Microsoft.DevTestLab/labs/write | Lägg till eller ändra labb. |
> | Åtgärd | Microsoft.DevTestLab/locations/operations/read | Läsåtgärder. |
> | Åtgärd | Microsoft.DevTestLab/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.DevTestLab/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/schedules/Retarget/action | Uppdaterar ett schema target resurs-Id. |
> | Åtgärd | Microsoft.DevTestLab/schedules/write | Lägg till eller ändra scheman. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DocumentDB/databaseAccountNames/read | Söker efter namn är tillgängliga. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/delete | Ta bort en samling. Gäller endast för API-typer: 'mongodb'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'mongodb'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/read | Läsa en samling eller visa en lista över alla samlingar. Gäller endast för API-typer: 'mongodb'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'mongodb'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/read | Läsningsdataflöde som är en samling. Gäller endast för API-typer: 'mongodb'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/write | Uppdatera en samling genomströmning. Gäller endast för API-typer: 'mongodb'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/write | Skapa eller uppdatera en samling. Gäller endast för API-typer: 'mongodb'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/delete | Ta bort en behållare. Gäller endast för API-typer: 'sql'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'sql'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/read | Läsa en behållare eller lista över alla behållare. Gäller endast för API-typer: 'sql'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'sql'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/read | Läs en dataflöden i behållare. Gäller endast för API-typer: 'sql'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/settings/write | Uppdatera en dataflöden i behållare. Gäller endast för API-typer: 'sql'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/containers/write | Skapa eller uppdatera en behållare. Gäller endast för API-typer: 'sql'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/delete | Ta bort en databas. Gäller endast för API-typer: sql, mongodb, 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/delete | Ta bort en graf. Gäller endast för API-typer: 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/read | Läsa ett diagram eller visa en lista över alla diagram. Gäller endast för API-typer: 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'gremlin'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/read | Läsa ett graph-dataflöde. Gäller endast för API-typer: 'gremlin'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/settings/write | Uppdatera en graph-genomströmning. Gäller endast för API-typer: 'gremlin'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Skapa eller uppdatera ett diagram. Gäller endast för API-typer: 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: sql, mongodb, 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Läsa en databas eller lista över alla databaser. Gäller endast för API-typer: sql, mongodb, 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: sql, mongodb, 'gremlin'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Läsa ett databas-dataflöde. Gäller endast för API-typer: sql, mongodb, 'gremlin'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Uppdatera en databas-genomströmning. Gäller endast för API-typer: sql, mongodb, 'gremlin'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Skapa en databas. Gäller endast för API-typer: sql, mongodb, 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Ta bort ett keyspace. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Läsa ett keyspace eller visa en lista över alla keyspaces. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'cassandra'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Läsa ett keyspace-dataflöde. Gäller endast för API-typer: 'cassandra'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Uppdatera ett keyspace-dataflöde. Gäller endast för API-typer: 'cassandra'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Ta bort en tabell. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Läsa en tabell eller alla tabeller. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'cassandra'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Läsa ett tabell-dataflöde. Gäller endast för API-typer: 'cassandra'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Uppdatera en tabell-genomströmning. Gäller endast för API-typer: 'cassandra'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Skapa eller uppdatera en tabell. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Skapa ett keyspace. Gäller endast för API-typer: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Ta bort en tabell. Gäller endast för API-typer: 'tabell'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'tabell'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Läsa en tabell eller alla tabeller. Gäller endast för API-typer: 'tabell'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Läsa statusen för den asynkrona åtgärden. Gäller endast för API-typer: 'tabell'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Läsa ett tabell-dataflöde. Gäller endast för API-typer: 'tabell'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Uppdatera en tabell-genomströmning. Gäller endast för API-typer: 'tabell'. Gäller endast för att ställa in typer: 'dataflöde'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Skapa eller uppdatera en tabell. Gäller endast för API-typer: 'tabell'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/backup/action | Skicka en begäran om att konfigurera säkerhetskopiering |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Ändra resursgruppen för ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Läser måttdefinitioner för samlingen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Läser mätvärden för samlingen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Läsa databas konto partition viktig statistik |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Läsa databas konto partition statistik |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Läsa databas konto partitioner i en samling |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Läsa databaskonto partition på användningsområden |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Läser de samling användningarna. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Läser måttdefinitioner för databasen |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Läser mätvärden för databasen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Läser användningar för databasen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/delete | Tar bort databaskonton. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Ändra prioriteter för redundans över regioner för ett databaskonto. Detta används för att utföra manuell redundans |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Hämta anslutningssträngen för ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Lista över nycklar i ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Läser måttdefinitioner för kontot för databasen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/metrics/read | Läser måtten för databasen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/offlineRegion/action | Offline ett område på ett databaskonto.  |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/onlineRegion/action | Online en region i ett databaskonto. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Läsa status för asynkron åtgärd |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Läsa mått för datainmatningssvarstider |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/read | Läsa percentilerna för replikeringsfördröjningar |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Läsa mått för datainmatningssvarstider för en specifik källa och mål-region |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Läsa mått för datainmatningssvarstider för ett specifikt mål-region |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/delete | Ta bort en proxy för anslutning av privat slutpunkt av databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/read | Läsa en proxy för anslutning av privat slutpunkt av databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/action | Validera en proxy för anslutning av privat slutpunkt av databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/privateEndpointConnectionProxies/write | Skapa eller uppdatera en privat slutpunkt anslutning proxy av databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/read | Läser ett databaskonto. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databasen readonly-kontonycklar. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Läser databasen readonly-kontonycklar. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Rotera nycklarna för ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Läser de regionala samling mått. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Läsa regionala databasen konto partition viktig statistik |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Läsa regionala databasen konto partition statistik |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Läsa regionala databasen konto partitioner i en samling |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Läser måtten för region och databasen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/restore/action | Skicka en begäran om återställning |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/usages/read | Läser databasen konto användningar. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/write | Uppdatera en databaskonton. |
> | Åtgärd | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.DocumentDB att VirtualNetwork eller undernät tas bort |
> | Åtgärd | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Läsa Status för deleteVirtualNetworkOrSubnets asynkron åtgärd |
> | Åtgärd | Microsoft.DocumentDB/locations/operationsStatus/read | Läser Status för asynkrona åtgärder |
> | Åtgärd | Microsoft.DocumentDB/operationResults/read | Läsa status för asynkron åtgärd |
> | Åtgärd | Microsoft.DocumentDB/operations/read | Läs-och skrivåtgärder som är tillgängliga för DocumentDB-Microsoft  |
> | Åtgärd | Microsoft.DocumentDB/register/action |  Registrera resursprovidern Microsoft DocumentDB för prenumerationen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DomainRegistration/checkDomainAvailability/Action | Kontrollera om en domän kan köpas |
> | Åtgärd | Microsoft.DomainRegistration/domains/Delete | Ta bort en befintlig domän. |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Ta bort ägarskap identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Lista över ägarskapet identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Få ägarskap identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Skapa eller uppdatera identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/operationresults/Read | Åtgärd för hämtning av en domän |
> | Åtgärd | Microsoft.DomainRegistration/domains/Read | Hämta listan över domäner |
> | Åtgärd | Microsoft.DomainRegistration/domains/Read | Hämta domän |
> | Åtgärd | Microsoft.DomainRegistration/domains/renew/Action | Förnya en befintlig domän. |
> | Åtgärd | Microsoft.DomainRegistration/domains/Write | Lägg till en ny domän eller uppdatera en befintlig |
> | Åtgärd | Microsoft.DomainRegistration/generateSsoRequest/Action | Skapa en begäran för att logga in på domänen kontrollcenter. |
> | Åtgärd | Microsoft.DomainRegistration/listDomainRecommendations/Action | Hämta lista domän rekommendationer baserat på nyckelord |
> | Åtgärd | Microsoft.DomainRegistration/operations/Read | Lista över alla åtgärder från app service-domänregistrering |
> | Åtgärd | Microsoft.DomainRegistration/register/action | Registrera resursprovidern Microsoft Domains för prenumerationen |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Lista avtal åtgärd |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/Read | Hämta toplevel domäner |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/Read | Hämta toplevel domän |
> | Åtgärd | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Verifiera domänen köp objekt utan att skicka den |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventGrid/domains/delete | Ta bort en domän |
> | Åtgärd | Microsoft.EventGrid/domains/listKeys/action | Lista över nycklar för en domän |
> | Åtgärd | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för domäner |
> | Åtgärd | Microsoft.EventGrid/domains/read | Läsa en domän |
> | Åtgärd | Microsoft.EventGrid/domains/regenerateKey/action | Återskapa nyckeln för en domän |
> | Åtgärd | Microsoft.EventGrid/domains/topics/read | Läs avsnittet för en domän |
> | Åtgärd | Microsoft.EventGrid/domains/write | Skapa eller uppdatera en domän |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/delete | Ta bort en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Hämta fullständig url för händelseprenumerationen |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för händelseprenumerationer |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för händelseprenumerationer |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för eventSubscriptions |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/read | Läsa en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/write | Skapa eller uppdatera en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för ämnen |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för ämnen |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för ämnen |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/read | Läs en extensionTopic. |
> | Åtgärd | Microsoft.EventGrid/locations/eventSubscriptions/read | Lista över regionala händelseprenumerationer |
> | Åtgärd | Microsoft.EventGrid/locations/operationResults/read | Läsa resultatet av en regional åtgärd |
> | Åtgärd | Microsoft.EventGrid/locations/operationsStatus/read | Läs av status för en regional åtgärd |
> | Åtgärd | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | Lista över regionala händelseprenumerationer av topictype |
> | Åtgärd | Microsoft.EventGrid/operationResults/read | Läsa resultatet av en åtgärd |
> | Åtgärd | Microsoft.EventGrid/operations/read | Liståtgärder EventGrid. |
> | Åtgärd | Microsoft.EventGrid/operationsStatus/read | Läs av status för en åtgärd |
> | Åtgärd | Microsoft.EventGrid/register/action | Registrerar prenumerationen för EventGrid-resursprovidern. |
> | Åtgärd | Microsoft.EventGrid/topics/delete | Ta bort ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/listKeys/action | Lista över nycklar för ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för ämnen |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för ämnen |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för ämnen |
> | Åtgärd | Microsoft.EventGrid/topics/read | Läsa ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/regenerateKey/action | Återskapa nyckeln för ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/write | Skapa eller uppdatera ett ämne |
> | Åtgärd | Microsoft.EventGrid/topictypes/eventSubscriptions/read | Lista med globala händelseprenumerationer av typ av ämne |
> | Åtgärd | Microsoft.EventGrid/topictypes/eventtypes/read | Läsa eventtypes som stöds av en topictype |
> | Åtgärd | Microsoft.EventGrid/topictypes/read | Läsa en topictype |
> | Åtgärd | Microsoft.EventGrid/unregister/action | Avregistrerar prenumerationen för EventGrid-resursprovidern. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventHub/availableClusterRegions/read | Läsåtgärd att lista tillgängliga företablerade kluster av Azure-region. |
> | Åtgärd | Microsoft.EventHub/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.EventHub/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell Använd CheckNameAvailability istället. |
> | Åtgärd | Microsoft.EventHub/clusters/delete | Tar bort en befintlig resurs i klustret. |
> | Åtgärd | Microsoft.EventHub/clusters/namespaces/read | Lista över namnområde ARM-ID: N för namnområden i ett kluster. |
> | Åtgärd | Microsoft.EventHub/clusters/operationresults/read | Hämta status för en asynkron klusteråtgärden. |
> | Åtgärd | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över klustermått resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/clusters/read | Hämtar den beskrivning av klusterresurs |
> | Åtgärd | Microsoft.EventHub/clusters/write | Skapar eller ändrar en befintlig resurs i klustret. |
> | Åtgärd | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Tar bort VNet-regler i EventHub-Resursprovidern för det angivna virtuella nätverket |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/action | Uppdateringar Namespace-auktoriseringsregeln. Detta API är inaktuell. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace istället... Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till Namespace |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/read | Hämta listan över auktoriseringsregler för namnområden beskrivning. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/namespaces/Delete | Ta bort Namespace-resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för Haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för Disaster Recovery primära Namespaces |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar Haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden under en viss prenumeration. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Tar bort den haveriberedskapskonfiguration som är kopplat till namnområdet. Den här åtgärden kan bara anropas via det primära namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och Omkonfigurerar namnområdesalias så att den pekar på det sekundära namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Åtgärd för att uppdatera EventHub. Den här åtgärden stöds inte på API-versionen 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för EventHub |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Hämta anslutningssträngen till EventHub |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Hämta listan över auktoriseringsregler för EventHub |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Skapa auktoriseringsregler för EventHub och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Åtgärd för att ta bort ConsumerGroup resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Hämta lista över ConsumerGroup resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Skapa eller uppdatera ConsumerGroup egenskaper. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/Delete | Åtgärd för att ta bort EventHub-resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/read | Hämta listan över resursbeskrivningar för EventHub |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/write | Skapa eller uppdatera EventHub-egenskaper. |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/delete | Ta bort IP-Filter resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/read | Hämta IP-Filter |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/write | Skapa resurs för IP-Filter |
> | DataAction | Microsoft.EventHub/namespaces/messages/receive/action | Ta emot meddelanden |
> | DataAction | Microsoft.EventHub/namespaces/messages/send/action | Skicka meddelanden |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/read | Hämtar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/write | Uppdaterar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/delete | Ta bort VNET-regel-resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/read | Hämtar NetworkRuleSet resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/write | Skapa resurs för VNET-regel |
> | Åtgärd | Microsoft.EventHub/namespaces/operationresults/read | Hämta status för Namespace åtgärden |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta listan över resursbeskrivningar för Namespace diagnostikinställningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta listan över resursbeskrivningar för Namespace diagnostikinställningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta lista över Namespace loggar resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/read | Hämta listan över Namespace beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Ta bort VNET-regel-resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Hämtar Regelresursen för virtuellt nätverk |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Skapa resurs för VNET-regel |
> | Åtgärd | Microsoft.EventHub/namespaces/write | Skapa en Namespace-resurs och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.EventHub/register/action | Registrerar prenumerationen för EventHub-resursprovidern och gör det möjligt att skapa EventHub-resurser |
> | Åtgärd | Microsoft.EventHub/sku/read | Hämta lista över SKU: N resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/sku/regions/read | Hämta lista över SkuRegions resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/unregister/action | Registrerar EventHub-Resursprovidern |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Features/features/read | Hämtar en prenumerations funktioner. |
> | Åtgärd | Microsoft.Features/operations/read | Hämtar listan över åtgärder. |
> | Åtgärd | Microsoft.Features/providers/features/read | Hämtar en funktion till en prenumeration i en given resursprovider. |
> | Åtgärd | Microsoft.Features/providers/features/register/action | Registrerar funktionen för en prenumeration i en given resursprovider. |
> | Åtgärd | Microsoft.Features/providers/features/unregister/action | Avregistrerar funktionen för en prenumeration under en given resursprovider. |
> | Åtgärd | Microsoft.Features/register/action | Registrerar funktionen för en prenumeration. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/delete | Ta bort tilldelningen för gäst-konfiguration. |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Hämta gäst configuration tilldelning. |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Konfigurationsrapport för tilldelning på Get gäst. |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Skapa ny tilldelning för gäst-konfiguration. |
> | Åtgärd | Microsoft.GuestConfiguration/register/action | Registrerar prenumerationen för resursprovidern Microsoft.GuestConfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.HDInsight/clusters/applications/delete | Ta bort program för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/applications/read | Hämta program för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/applications/write | Skapa eller uppdatera program för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/changerdpsetting/action | Ändra RDP-inställningen för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/action | Uppdateringskonfigurationen för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/action | Hämta HDInsight klusterkonfigurationer |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/read | Hämta HDInsight klusterkonfigurationer |
> | Åtgärd | Microsoft.HDInsight/clusters/delete | Ta bort ett HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/getGatewaySettings/action | Hämta gateway-inställningar för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/read | Få information om HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/roles/resize/action | Ändra storlek på ett HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/updateGatewaySettings/action | Uppdatera gateway-inställningar för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/write | Skapa eller uppdatera HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/locations/capabilities/read | Hämta prenumerationens funktioner |
> | Åtgärd | Microsoft.HDInsight/locations/checkNameAvailability/read | Kontrollera Namntillgänglighet |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ImportExport/jobs/delete | Tar bort ett befintligt jobb. |
> | Åtgärd | Microsoft.ImportExport/jobs/listBitLockerKeys/action | Hämtar BitLocker-nycklar för det angivna jobbet. |
> | Åtgärd | Microsoft.ImportExport/jobs/read | Hämtar egenskaperna för det angivna jobbet eller returnerar listan över jobb. |
> | Åtgärd | Microsoft.ImportExport/jobs/write | Skapar ett jobb med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för det angivna jobbet. |
> | Åtgärd | Microsoft.ImportExport/locations/read | Hämtar egenskaperna för den angivna platsen eller returnerar listan över platser. |
> | Åtgärd | Microsoft.ImportExport/register/action | Registrerar prenumerationen för import/export-resursprovidern och gör möljligt import/export-jobb. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Insights/ActionGroups/Delete | Ta bort en åtgärdsgrupp |
> | Åtgärd | Microsoft.Insights/ActionGroups/Read | Läs en åtgärdsgrupp |
> | Åtgärd | Microsoft.Insights/ActionGroups/Write | Skapa eller uppdatera en åtgärdsgrupp |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivitet Log avisering aktiveras |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Delete | Ta bort en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Read | Läs en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Write | Skapa eller uppdatera en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Activated/Action | Klassisk måttavisering aktiverat |
> | Åtgärd | Microsoft.Insights/AlertRules/Delete | Ta bort en klassisk måttavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Incidents/Read | Läsa en klassisk måttavisering incidentavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Read | Läsa en klassisk måttavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Resolved/Action | Klassisk måttavisering löst |
> | Åtgärd | Microsoft.Insights/AlertRules/Throttled/Action | Klassiska måttaviseringsregel begränsas |
> | Åtgärd | Microsoft.Insights/AlertRules/Write | Skapa eller uppdatera en klassisk måttavisering |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Delete | Ta bort en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Läsa en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Skapa eller uppdatera en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Läs Loggdefinitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Read | Läsa en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Automatisk skalning skala ned initierad |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Automatisk skalning skala ned slutförd |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Skala automatiskt skala upp initierad |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Skala automatiskt skala upp slutförd |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Write | Skapa eller uppdatera en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/Baseline/Read | Läsa utgångspunkt mått (förhandsversion) |
> | Åtgärd | Microsoft.Insights/CalculateBaseline/Read | Beräkna baslinjen för måttvärden (förhandsversion) |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Delete | Tar bort ett Application Insights analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Read | Läser en Application Insights analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Write | Skriver en Application Insights analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Action | Åtgärd för Application Insights analytics-tabell |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Delete | Tar bort ett Application Insights analytics-tabellschema |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Read | Läser ett Application Insights analytics-tabellschema |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Write | Skriver ett Application Insights analytics-tabellschema |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Delete | Tar bort en Application Insights-anteckning |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Read | Läser en Application Insights-anteckning |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Write | Skriver en Application Insights-anteckning |
> | Åtgärd | Microsoft.Insights/Components/Api/Read | Läsa API för Application Insights-komponentdata |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Action | Skapar en Application Insights API-nyckel |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Delete | Tar bort en Application Insights API-nyckel |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Read | Läser en Application Insights API-nyckel |
> | Åtgärd | Microsoft.Insights/Components/BillingPlanForComponent/Read | Läser ett faktureringsavtal för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Läser aktuella faktureringsfunktioner för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Skriva aktuella faktureringsfunktioner för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Läser en konfiguration av Application Insights standard ALM-integrering |
> | Åtgärd | Microsoft.Insights/Components/Delete | Tar bort en konfiguration av application insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Events/Read | Hämta loggar från Application Insights med OData-frågeformat |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Action | Åtgärd för exportinställningar i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Delete | Ta bort Application Insights-exportinställningar |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Read | Läser Application Insights-exportinställningar |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Write | Skriver Application Insights-exportinställningar |
> | Åtgärd | Microsoft.Insights/Components/ExtendQueries/Read | Utökade frågeresultat läser Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Delete | Tar bort en Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Read | Läser en Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Write | Skriver en Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/FeatureCapabilities/Read | Funktioner för läser Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Läser Application Insights tillgängliga faktureringsfunktioner för komponent |
> | Åtgärd | Microsoft.Insights/Components/GetToken/Read | Läser en token för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/MetricDefinitions/Read | Läsa definitioner av mätvärden för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Metrics/Read | Mätvärden för läser Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Move/Action | Flytta en Application Insights-komponent till en annan resursgrupp eller prenumeration |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Tar bort ett Application Insights personliga analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Read | Läser en Application Insights personliga analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Write | Skriver en Application Insights personliga analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/MyFavorites/Read | Läser en personlig Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/Operations/Read | Hämta status för långvariga åtgärder i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Read | Läser ett prisavtal Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Write | Skriver ett prisavtal Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Läser Application Insights konfiguration av proaktiv identifiering |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Skriva konfiguration av proaktiv identifiering för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/Components/Purge/Action | Tar bort alla data från Application Insights |
> | Åtgärd | Microsoft.Insights/Components/Query/Read | Kör frågor mot loggar i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/QuotaStatus/Read | Läsa kvotstatus för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Read | Läser en konfiguration av application insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Läser Application Insights-webbtestplatser |
> | Åtgärd | Microsoft.Insights/Components/Webtests/Read | Läser en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Delete | Tar bort en konfiguration av Application Insights ALM-integrering |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Read | Läser en konfiguration av Application Insights ALM-integrering |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Write | Skriver en konfiguration av Application Insights ALM-integrering |
> | Åtgärd | Microsoft.Insights/Components/Write | Skrivning till en konfiguration av application insights-komponent |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Delete | Ta bort en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Read | Läsa en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Write | Skapa eller uppdatera en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/EventCategories/Read | Läs tillgängliga aktivitetsloggen händelsekategorier |
> | Åtgärd | Microsoft.Insights/eventtypes/digestevents/Read | Läs hanteringshändelsetyp |
> | Åtgärd | Microsoft.Insights/eventtypes/values/Read | Läs aktivitetslogghändelser |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Ta bort en nätverk flow log diagnostikinställning |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Läs diagnostikinställning en nätverk flow log |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Skapa eller uppdatera en nätverk flow log diagnostikinställning |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Action | Hämta prenumerationsmigrering |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Read | Get-prenumerationsmigrering |
> | Åtgärd | Microsoft.Insights/LogDefinitions/Read | Läs Loggdefinitioner |
> | Åtgärd | Microsoft.Insights/LogProfiles/Delete | Ta bort en aktivitetsloggen av loggprofil |
> | Åtgärd | Microsoft.Insights/LogProfiles/Read | Läsa en aktivitetsloggen log-profil |
> | Åtgärd | Microsoft.Insights/LogProfiles/Write | Skapa eller uppdatera en aktivitetsloggen av loggprofil |
> | Åtgärd | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Läsa data från tabellen ADAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/ADReplicationResult/Read | Läsa data från tabellen ADReplicationResult |
> | Åtgärd | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Läsa data från tabellen ADSecurityAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/Alert/Read | Läsa data från tabellen avisering |
> | Åtgärd | Microsoft.Insights/Logs/AlertHistory/Read | Läsa data från tabellen AlertHistory |
> | Åtgärd | Microsoft.Insights/Logs/ApplicationInsights/Read | Läsa data från tabellen ApplicationInsights |
> | Åtgärd | Microsoft.Insights/Logs/AzureActivity/Read | Läsa data från tabellen AzureActivity |
> | Åtgärd | Microsoft.Insights/Logs/AzureMetrics/Read | Läsa data från tabellen AzureMetrics |
> | Åtgärd | Microsoft.Insights/Logs/BoundPort/Read | Läsa data från tabellen BoundPort |
> | Åtgärd | Microsoft.Insights/Logs/CommonSecurityLog/Read | Läsa data från tabellen CommonSecurityLog |
> | Åtgärd | Microsoft.Insights/Logs/ComputerGroup/Read | Läsa data från tabellen ComputerGroup |
> | Åtgärd | Microsoft.Insights/Logs/ConfigurationChange/Read | Läsa data från tabellen ConfigurationChange |
> | Åtgärd | Microsoft.Insights/Logs/ConfigurationData/Read | Läsa data från tabellen ConfigurationData |
> | Åtgärd | Microsoft.Insights/Logs/ContainerImageInventory/Read | Läsa data från tabellen ContainerImageInventory |
> | Åtgärd | Microsoft.Insights/Logs/ContainerInventory/Read | Läsa data från tabellen ContainerInventory |
> | Åtgärd | Microsoft.Insights/Logs/ContainerLog/Read | Läsa data från tabellen ContainerLog |
> | Åtgärd | Microsoft.Insights/Logs/ContainerServiceLog/Read | Läsa data från tabellen ContainerServiceLog |
> | Åtgärd | Microsoft.Insights/Logs/DeviceAppCrash/Read | Läsa data från tabellen DeviceAppCrash |
> | Åtgärd | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Läsa data från tabellen DeviceAppLaunch |
> | Åtgärd | Microsoft.Insights/Logs/DeviceCalendar/Read | Läsa data från tabellen DeviceCalendar |
> | Åtgärd | Microsoft.Insights/Logs/DeviceCleanup/Read | Läsa data från tabellen DeviceCleanup |
> | Åtgärd | Microsoft.Insights/Logs/DeviceConnectSession/Read | Läsa data från tabellen DeviceConnectSession |
> | Åtgärd | Microsoft.Insights/Logs/DeviceEtw/Read | Läsa data från tabellen DeviceEtw |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Läsa data från tabellen DeviceHardwareHealth |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHealth/Read | Läsa data från tabellen devicehealth koppla |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Läsa data från tabellen DeviceHeartbeat |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Läsa data från tabellen DeviceSkypeHeartbeat |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Läsa data från tabellen DeviceSkypeSignIn |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSleepState/Read | Läsa data från tabellen DeviceSleepState |
> | Åtgärd | Microsoft.Insights/Logs/DHAppFailure/Read | Läsa data från tabellen DHAppFailure |
> | Åtgärd | Microsoft.Insights/Logs/DHAppReliability/Read | Läsa data från tabellen DHAppReliability |
> | Åtgärd | Microsoft.Insights/Logs/DHDriverReliability/Read | Läsa data från tabellen DHDriverReliability |
> | Åtgärd | Microsoft.Insights/Logs/DHLogonFailures/Read | Läsa data från tabellen DHLogonFailures |
> | Åtgärd | Microsoft.Insights/Logs/DHLogonMetrics/Read | Läsa data från tabellen DHLogonMetrics |
> | Åtgärd | Microsoft.Insights/Logs/DHOSCrashData/Read | Läsa data från tabellen DHOSCrashData |
> | Åtgärd | Microsoft.Insights/Logs/DHOSReliability/Read | Läsa data från tabellen DHOSReliability |
> | Åtgärd | Microsoft.Insights/Logs/DHWipAppLearning/Read | Läsa data från tabellen DHWipAppLearning |
> | Åtgärd | Microsoft.Insights/Logs/DnsEvents/Read | Läsa data från tabellen DnsEvents |
> | Åtgärd | Microsoft.Insights/Logs/DnsInventory/Read | Läsa data från tabellen DnsInventory |
> | Åtgärd | Microsoft.Insights/Logs/ETWEvent/Read | Läsa data från tabellen ETWEvent |
> | Åtgärd | Microsoft.Insights/Logs/Event/Read | Läsa data från tabellen händelse |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Läsa data från tabellen ExchangeAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Läsa data från tabellen ExchangeOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/Heartbeat/Read | Läsa data från tabellen pulsslag |
> | Åtgärd | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Läsa data från tabellen IISAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/InboundConnection/Read | Läsa data från tabellen InboundConnection |
> | Åtgärd | Microsoft.Insights/Logs/KubeNodeInventory/Read | Läsa data från tabellen KubeNodeInventory |
> | Åtgärd | Microsoft.Insights/Logs/KubePodInventory/Read | Läsa data från tabellen KubePodInventory |
> | Åtgärd | Microsoft.Insights/Logs/LinuxAuditLog/Read | Läsa data från tabellen LinuxAuditLog |
> | Åtgärd | Microsoft.Insights/Logs/MAApplication/Read | Läsa data från tabellen MAApplication |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealth/Read | Läsa data från tabellen MAApplicationHealth |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Läsa data från tabellen MAApplicationHealthAlternativeVersions |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Läsa data från tabellen MAApplicationHealthIssues |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationInstance/Read | Läsa data från tabellen MAApplicationInstance |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Läsa data från tabellen MAApplicationInstanceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Läsa data från tabellen MAApplicationReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MADeploymentPlan/Read | Läsa data från tabellen MADeploymentPlan |
> | Åtgärd | Microsoft.Insights/Logs/MADevice/Read | Läsa data från tabellen MADevice |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Läsa data från tabellen MADevicePnPHealth |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Läsa data från tabellen MADevicePnPHealthAlternativeVersions |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Läsa data från tabellen MADevicePnPHealthIssues |
> | Åtgärd | Microsoft.Insights/Logs/MADeviceReadiness/Read | Läsa data från tabellen MADeviceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Läsa data från tabellen MADriverInstanceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MADriverReadiness/Read | Läsa data från tabellen MADriverReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddin/Read | Läsa data från tabellen MAOfficeAddin |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Läsa data från tabellen MAOfficeAddinHealth |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Läsa data från tabellen MAOfficeAddinHealthIssues |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Läsa data från tabellen MAOfficeAddinInstance |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Läsa data från tabellen MAOfficeAddinInstanceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Läsa data från tabellen MAOfficeAddinReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeApp/Read | Läsa data från tabellen MAOfficeApp |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Läsa data från tabellen MAOfficeAppHealth |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Läsa data från tabellen MAOfficeAppInstance |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Läsa data från tabellen MAOfficeAppReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Läsa data från tabellen MAOfficeBuildInfo |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Läsa data från tabellen MAOfficeCurrencyAssessment |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Läsa data från tabellen MAOfficeCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Läsa data från tabellen MAOfficeDeploymentStatus |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Läsa data från tabellen MAOfficeMacroHealth |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Läsa data från tabellen MAOfficeMacroHealthIssues |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Läsa data från tabellen MAOfficeMacroIssueInstanceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Läsa data från tabellen MAOfficeMacroIssueReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Läsa data från tabellen MAOfficeMacroSummary |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeSuite/Read | Läsa data från tabellen MAOfficeSuite |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Läsa data från tabellen MAOfficeSuiteInstance |
> | Åtgärd | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Läsa data från tabellen MAProposedPilotDevices |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Läsa data från tabellen MAWindowsBuildInfo |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Läsa data från tabellen MAWindowsCurrencyAssessment |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Läsa data från tabellen MAWindowsCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Läsa data från tabellen MAWindowsDeploymentStatus |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Läsa data från tabellen MAWindowsSysReqInstanceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/NetworkMonitoring/Read | Läsa data från tabellen NetworkMonitoring |
> | Åtgärd | Microsoft.Insights/Logs/OfficeActivity/Read | Läsa data från tabellen OfficeActivity |
> | Åtgärd | Microsoft.Insights/Logs/Operation/Read | Läsa data från tabellen åtgärden |
> | Åtgärd | Microsoft.Insights/Logs/OutboundConnection/Read | Läsa data från tabellen OutboundConnection |
> | Åtgärd | Microsoft.Insights/Logs/Perf/Read | Läsa data från tabellen Perf |
> | Åtgärd | Microsoft.Insights/Logs/ProtectionStatus/Read | Läsa data från tabellen ProtectionStatus |
> | Åtgärd | Microsoft.Insights/Logs/Read | Läsa data från alla loggar |
> | Åtgärd | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Läsa data från tabellen ReservedAzureCommonFields |
> | Åtgärd | Microsoft.Insights/Logs/ReservedCommonFields/Read | Läsa data från tabellen ReservedCommonFields |
> | Åtgärd | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Läsa data från tabellen SCCMAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Läsa data från tabellen SCOMAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SecurityAlert/Read | Läsa data från tabellen SecurityAlert |
> | Åtgärd | Microsoft.Insights/Logs/SecurityBaseline/Read | Läsa data från tabellen SecurityBaseline |
> | Åtgärd | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Läsa data från tabellen SecurityBaselineSummary |
> | Åtgärd | Microsoft.Insights/Logs/SecurityDetection/Read | Läsa data från tabellen SecurityDetection |
> | Åtgärd | Microsoft.Insights/Logs/SecurityEvent/Read | Läsa data från tabellen SecurityEvent |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Läsa data från tabellen ServiceFabricOperationalEvent |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Läsa data från tabellen ServiceFabricReliableActorEvent |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Läsa data från tabellen ServiceFabricReliableServiceEvent |
> | Åtgärd | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Läsa data från tabellen SfBAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Läsa data från tabellen SfBOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Läsa data från tabellen SharePointOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Läsa data från tabellen SPAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Läsa data från tabellen SQLAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Läsa data från tabellen SQLQueryPerformance |
> | Åtgärd | Microsoft.Insights/Logs/Syslog/Read | Läsa data från tabellen Syslog |
> | Åtgärd | Microsoft.Insights/Logs/SysmonEvent/Read | Läsa data från tabellen SysmonEvent |
> | Åtgärd | Microsoft.Insights/Logs/UAApp/Read | Läsa data från tabellen UAApp |
> | Åtgärd | Microsoft.Insights/Logs/UAComputer/Read | Läsa data från tabellen UAComputer |
> | Åtgärd | Microsoft.Insights/Logs/UAComputerRank/Read | Läsa data från tabellen UAComputerRank |
> | Åtgärd | Microsoft.Insights/Logs/UADriver/Read | Läsa data från tabellen UADriver |
> | Åtgärd | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Läsa data från tabellen UADriverProblemCodes |
> | Åtgärd | Microsoft.Insights/Logs/UAFeedback/Read | Läsa data från tabellen UAFeedback |
> | Åtgärd | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Läsa data från tabellen UAHardwareSecurity |
> | Åtgärd | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Läsa data från tabellen UAIESiteDiscovery |
> | Åtgärd | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Läsa data från tabellen UAOfficeAddIn |
> | Åtgärd | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Läsa data från tabellen UAProposedActionPlan |
> | Åtgärd | Microsoft.Insights/Logs/UASysReqIssue/Read | Läsa data från tabellen UASysReqIssue |
> | Åtgärd | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Läsa data från tabellen UAUpgradedComputer |
> | Åtgärd | Microsoft.Insights/Logs/Update/Read | Läsa data från tabellen Update |
> | Åtgärd | Microsoft.Insights/Logs/UpdateRunProgress/Read | Läsa data från tabellen UpdateRunProgress |
> | Åtgärd | Microsoft.Insights/Logs/UpdateSummary/Read | Läsa data från tabellen UpdateSummary |
> | Åtgärd | Microsoft.Insights/Logs/Usage/Read | Läsa data från tabellen användning |
> | Åtgärd | Microsoft.Insights/Logs/W3CIISLog/Read | Läsa data från tabellen W3CIISLog |
> | Åtgärd | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Läsa data från tabellen WaaSDeploymentStatus |
> | Åtgärd | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Läsa data från tabellen WaaSInsiderStatus |
> | Åtgärd | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Läsa data från tabellen WaaSUpdateStatus |
> | Åtgärd | Microsoft.Insights/Logs/WDAVStatus/Read | Läsa data från tabellen WDAVStatus |
> | Åtgärd | Microsoft.Insights/Logs/WDAVThreat/Read | Läsa data från tabellen WDAVThreat |
> | Åtgärd | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Läsa data från tabellen WindowsClientAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/WindowsFirewall/Read | Läsa data från tabellen WindowsFirewall |
> | Åtgärd | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Läsa data från tabellen WindowsServerAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/WireData/Read | Läsa data från tabellen WireData |
> | Åtgärd | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Läsa data från tabellen WUDOAggregatedStatus |
> | Åtgärd | Microsoft.Insights/Logs/WUDOStatus/Read | Läsa data från tabellen WUDOStatus |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Delete | Ta bort en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Read | Läs en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Status/Read | Läs måttaviseringsstatus |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Write | Skapa eller uppdatera en metrisk varning |
> | Åtgärd | Microsoft.Insights/MetricBaselines/Read | Läsa mått baslinjer |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/Metricnamespaces/Read | Läsa mått namnområden |
> | Åtgärd | Microsoft.Insights/Metrics/Action | Metrisk åtgärd |
> | Åtgärd | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Läs mått |
> | Åtgärd | Microsoft.Insights/Metrics/providers/Metrics/Read | Läs mått |
> | Åtgärd | Microsoft.Insights/Metrics/Read | Läs mått |
> | DataAction | Microsoft.Insights/Metrics/Write | Skrivning av mått |
> | Åtgärd | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrera prenumerationen till ny Prismodell |
> | Åtgärd | Microsoft.Insights/Operations/Read | Läsåtgärder |
> | Åtgärd | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Återställ prenumerationen till den äldre prismodellen |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Delete | Tar bort en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Read | Läser en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Write | Skriver en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/Tenants/Register/Action | Initierar Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Unregister/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Webtests/Delete | Tar bort en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Webtests/GetToken/Read | Läser en token för webbtest |
> | Åtgärd | Microsoft.Insights/Webtests/MetricDefinitions/Read | Läser en definitioner av mått för webbtest |
> | Åtgärd | Microsoft.Insights/Webtests/Metrics/Read | Läser ett webbtestmätvärde |
> | Åtgärd | Microsoft.Insights/Webtests/Read | Läser en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Webtests/Write | Skrivning till en webbtestkonfiguration |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Intune/diagnosticsettings/delete | Tar bort en diagnostikinställning |
> | Åtgärd | Microsoft.Intune/diagnosticsettings/read | Läser en diagnostikinställning |
> | Åtgärd | Microsoft.Intune/diagnosticsettings/write | Skriva en diagnostikinställning |
> | Åtgärd | Microsoft.Intune/diagnosticsettingscategories/read | Läser en diagnostikinställning kategorier |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.IoTCentral/appTemplates/action | Hämtar alla tillgängliga mallar på Azure IoT Central |
> | Åtgärd | Microsoft.IoTCentral/checkNameAvailability/action | Kontrollerar om det finns ett IoT Central programnamn |
> | Åtgärd | Microsoft.IoTCentral/checkSubdomainAvailability/action | Kontrollerar om det finns ett centralt program för IoT-underdomän |
> | Åtgärd | Microsoft.IoTCentral/IoTApps/delete | Tar bort en IoT Central-program |
> | Åtgärd | Microsoft.IoTCentral/IoTApps/read | Hämtar ett enda centralt IoT-program |
> | Åtgärd | Microsoft.IoTCentral/IoTApps/write | Skapar eller uppdaterar en IoT Central-program |
> | Åtgärd | Microsoft.IoTCentral/operations/read | Hämtar alla tillgängliga åtgärder på IoT Central-program |
> | Åtgärd | Microsoft.IoTCentral/register/action | Registrera prenumeration för Azure IoT Central-resursprovidern |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.IoTSpaces/Graph/delete | Tar bort Microsoft.IoTSpaces graphresursen |
> | Åtgärd | Microsoft.IoTSpaces/Graph/read | Hämtar Microsoft.IoTSpaces Graph-resurser |
> | Åtgärd | Microsoft.IoTSpaces/Graph/write | Skapa Microsoft.IoTSpaces Graph-resurs |
> | Åtgärd | Microsoft.IoTSpaces/register/action | Registrera prenumeration för resursprovidern för Microsoft.IoTSpaces Graph att skapa resurser |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.KeyVault/checkNameAvailability/read | Kontrollerar att ett key vault-namn är giltigt och används inte |
> | Åtgärd | Microsoft.KeyVault/deletedVaults/read | Visa egenskaperna för ej permanent borttagna nyckelvalven |
> | Åtgärd | Microsoft.KeyVault/hsmPools/delete | Ta bort en HSM-pool |
> | Åtgärd | Microsoft.KeyVault/hsmPools/joinVault/action | Anslut ett nyckelvalv till en HSM-pool |
> | Åtgärd | Microsoft.KeyVault/hsmPools/read | Visa egenskaperna för en HSM-pool |
> | Åtgärd | Microsoft.KeyVault/hsmPools/write | Skapa en ny HSM-pool Update egenskaperna för en befintlig HSM-pool |
> | Åtgärd | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent Borttaget nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/locations/deletedVaults/read | Visa egenskaperna för ett ej permanent Borttaget nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.KeyVault att ett virtuellt nätverk eller undernät tas bort |
> | Åtgärd | Microsoft.KeyVault/locations/operationResults/read | Kontrollera resultatet för en långkörd åtgärd |
> | Åtgärd | Microsoft.KeyVault/operations/read | Visar en lista över åtgärder som är tillgängliga på resursprovidern Microsoft.keyvault |
> | Åtgärd | Microsoft.KeyVault/register/action | Registrerar en prenumeration |
> | Åtgärd | Microsoft.KeyVault/unregister/action | Avregistrerar en prenumeration |
> | Åtgärd | Microsoft.KeyVault/vaults/accessPolicies/write | Uppdatera en befintlig åtkomstprincip genom att sammanfoga ersätta eller lägga till en ny åtkomstprincip till ett valv. |
> | Åtgärd | Microsoft.KeyVault/vaults/delete | Ta bort ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/deploy/action | Tillåter åtkomst till hemligheter i key vault när du distribuerar Azure-resurser |
> | Åtgärd | Microsoft.KeyVault/vaults/eventGridFilters/delete | Aviserar Microsoft.KeyVault att en EventGrid-prenumeration för Key Vault tas bort |
> | Åtgärd | Microsoft.KeyVault/vaults/eventGridFilters/read | Aviserar Microsoft.KeyVault att visas en EventGrid-prenumeration för Key Vault |
> | Åtgärd | Microsoft.KeyVault/vaults/eventGridFilters/write | Aviserar Microsoft.KeyVault att skapas en ny EventGrid-prenumeration för Key Vault |
> | Åtgärd | Microsoft.KeyVault/vaults/read | Visa egenskaperna för ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/read | Visa egenskaperna för en hemlighet, men inte dess värde. |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/write | Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet. |
> | Åtgärd | Microsoft.KeyVault/vaults/write | Skapa ett nytt nyckelvalv eller uppdatera egenskaperna för ett befintligt nyckelvalv |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Kusto/Clusters/Activate/action | Startar klustret. |
> | Åtgärd | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/delete | Tar bort en resurs för konfiguration av anslutna databasen. |
> | Åtgärd | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/read | Läser en konfiguration för anslutna databasresurser. |
> | Åtgärd | Microsoft.Kusto/Clusters/AttachedDatabaseConfigurations/write | Skriver en konfiguration för anslutna databasresurser. |
> | Åtgärd | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Kontrollerar tillgängligheten för klustret namn. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Lägger till huvudkonton. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/CheckNameAvailability/action | Kontrollerar tillgängligheten för namnet för en viss typ. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/DataConnections/delete | Tar bort en resurs för anslutningar av data. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/DataConnections/read | Läser en resurs för anslutningar av data. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/DataConnections/write | Skriver en resurs för anslutningar av data. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/DataConnectionValidation/action | Verifierar data databasanslutning. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/delete | Tar bort en databasresurs. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Tar bort en resurs för Event Hub-anslutningar. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Läser en resurs för Event Hub-anslutningar. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Skriver en resurs för Event Hub-anslutningar. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Verifierar databasanslutning för Event Hub. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/ListPrincipals/action | Visar databasens säkerhetsobjekt. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/read | Läser en databasresurs. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/RemovePrincipals/action | Tar bort databasen säkerhetsobjekt. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/write | Skriver en databasresurs. |
> | Åtgärd | Microsoft.Kusto/Clusters/Deactivate/action | Stoppar klustret. |
> | Åtgärd | Microsoft.Kusto/Clusters/delete | Tar bort en klusterresurs. |
> | Åtgärd | Microsoft.Kusto/Clusters/read | Läser en klusterresurs. |
> | Åtgärd | Microsoft.Kusto/Clusters/SKUs/read | Läser ett kluster SKU-resurs. |
> | Åtgärd | Microsoft.Kusto/Clusters/Start/action | Startar klustret. |
> | Åtgärd | Microsoft.Kusto/Clusters/Stop/action | Stoppar klustret. |
> | Åtgärd | Microsoft.Kusto/Clusters/write | Skriver en klusterresurs. |
> | Åtgärd | Microsoft.Kusto/Locations/CheckNameAvailability/action | Kontrollerar namntillgänglighet för resursen. |
> | Åtgärd | Microsoft.Kusto/locations/operationresults/read | Läser åtgärder resurser |
> | Åtgärd | Microsoft.Kusto/Operations/read | Läser åtgärder resurser |
> | Åtgärd | Microsoft.Kusto/Register/action | Registrerar prenumerationen på Kusto-Resursprovidern. |
> | Åtgärd | Microsoft.Kusto/SKUs/read | Läser en SKU-resurs. |
> | Åtgärd | Microsoft.Kusto/Unregister/action | Avregistrerar prenumerationen på Kusto-Resursprovidern. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LabServices/labAccounts/CreateLab/action | Skapa ett labb i ett labbkonto. |
> | Åtgärd | Microsoft.LabServices/labAccounts/delete | Ta bort lab-konton. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/delete | Ta bort galleriavbildningar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/read | Läsa galleriavbildningar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/write | Lägg till eller ändra galleriavbildningar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Hämta information om regional tillgänglighet för varje kategori för storlek som konfigurerats under ett labbkonto |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Lägga till användare i ett labb |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/delete | Ta bort labs. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Anspråk en slumpmässig miljö för en användare i en miljöinställningar |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Ta bort miljöinställning. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Anspråk i miljön och tilldelar den till användaren |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Läs miljö. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Återställer användarlösenord i en miljö |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stoppar en miljö genom att stoppa alla resurser i miljön |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Etablerar/deprovisions krävs resurser för en miljö som inställningen baserat på aktuell status för laboratoriemiljön /-inställningen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Inställningen för Läs miljö. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Återställer lösenordet på den virtuella datorn för mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Sparar aktuella mall-bild till delade galleriet i labbkonto |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Startar en mall genom att starta alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stoppar en mall genom att stoppa alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Lägg till eller ändra inställningen för miljön. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/read | Läsa labs. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/Register/action | Registrera till hanterade labb. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Skicka e-postmeddelande med registreringslänk till labbet |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/delete | Ta bort användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/read | Skrivskyddad användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/write | Lägg till eller ändra användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/write | Lägg till eller ändra labb. |
> | Åtgärd | Microsoft.LabServices/labAccounts/read | Läsa lab-konton. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Ta bort sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/read | Läsa sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/write | Lägg till eller ändra sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/delete | Ta bort sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/read | Läsa sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/write | Lägg till eller ändra sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/write | Lägg till eller ändra labb-konton. |
> | Åtgärd | Microsoft.LabServices/locations/operations/read | Läsåtgärder. |
> | Åtgärd | Microsoft.LabServices/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.LabServices/users/GetEnvironment/action | Hämtar information för virtuell dator |
> | Åtgärd | Microsoft.LabServices/users/GetOperationBatchStatus/action | Hämta Åtgärdsstatus för batch |
> | Åtgärd | Microsoft.LabServices/users/GetOperationStatus/action | Hämtar status för långvarig åtgärd |
> | Åtgärd | Microsoft.LabServices/users/GetPersonalPreferences/action | Få personliga inställningar för en användare |
> | Åtgärd | Microsoft.LabServices/users/ListAllEnvironments/action | Lista över alla miljöer för användaren |
> | Åtgärd | Microsoft.LabServices/users/ListEnvironments/action | Lista miljöer för användaren |
> | Åtgärd | Microsoft.LabServices/users/ListLabs/action | Lista över labs för användaren. |
> | Åtgärd | Microsoft.LabServices/users/Register/action | Registrera en användare till en hanterad labb |
> | Åtgärd | Microsoft.LabServices/users/ResetPassword/action | Återställer användarlösenord i en miljö |
> | Åtgärd | Microsoft.LabServices/users/StartEnvironment/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft.LabServices/users/StopEnvironment/action | Stoppar en miljö genom att stoppa alla resurser i miljön |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/delete | Tar bort avtalet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Hämtar Motringnings-URL för avtalsinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/read | Läser avtalet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/write | Skapar eller uppdaterar avtalet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/delete | Tar bort sammansättningen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Hämtar Motringnings-URL för sammansättningens innehåll i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/read | Läser sammansättningen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/write | Skapar eller uppdaterar sammansättningen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Tar bort batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Läser batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Skapar eller uppdaterar batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/delete | Tar bort certifikatet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/read | Läser certifikatet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/write | Skapar eller uppdaterar certifikatet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/delete | Tar bort integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/join/action | Ansluter Integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Hämtar Motringnings-URL för integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Hämtar nycklarna i nyckelvalvet. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Loggar spårningshändelser i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/delete | Tar bort kartan i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Hämtar Motringnings-URL för kartinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/read | Läser kartan i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/write | Skapar eller uppdaterar kartan i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/delete | Tar bort partnern i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Hämtar Motringnings-URL för partnerinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/read | Läser partnern i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/write | Skapar eller uppdaterar partnern i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Läser Integrationskontots Loggdefinitioner. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/read | Läser integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Återskapar åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/delete | Tar bort schemat i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Hämtar Motringnings-URL för schemainnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/read | Läser schemat i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/write | Skapar eller uppdaterar schemat i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/delete | Tar bort sessionen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/read | Läser batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/write | Skapar eller uppdaterar sessionen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/write | Skapar eller uppdaterar integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/delete | Tar bort integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/join/action | Kopplar ihop Integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Läser integreringstjänstmiljön hanterade API-åtgärden. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Läser integreringstjänstmiljön hanterad API. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Läser integrationen administratörsåtgärder för service-miljö. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitioner för service-miljö för integrationen. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/read | Läser integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/write | Skapar eller uppdaterar integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Hämtar spåren av grupper med det rekommenderas att åtgärder. |
> | Åtgärd | Microsoft.Logic/locations/workflows/validate/action | Verifierar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/operations/read | Hämtar åtgärden. |
> | Åtgärd | Microsoft.Logic/register/action | Registrerar Microsoft.Logic-resursprovidern för en viss prenumeration. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/delete | Tar bort åtkomstnyckeln. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/list/action | Visar en lista över åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/read | Läser åtkomstnyckeln. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/regenerate/action | Återskapar åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/write | Skapar eller uppdaterar åtkomstnyckeln. |
> | Åtgärd | Microsoft.Logic/workflows/delete | Tar bort arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/disable/action | Inaktiverar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/enable/action | Aktiverar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/listCallbackUrl/action | Hämtar Motringnings-URL för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/listSwagger/action | Hämtar arbetsflödets swagger-definitioner. |
> | Åtgärd | Microsoft.Logic/workflows/move/action | Flyttar arbetsflödet från dess befintliga prenumerations-id, resursgrupp och/eller namn till ett annat prenumerations-id, resursgrupp och/eller namn. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Läser diagnostikinställningar för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställning för arbetsflöde. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Läser Loggdefinitioner för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitioner för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/read | Läser arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/regenerateAccessKey/action | Återskapar åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/workflows/run/action | Startar en körning av arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Hämtar spåren av arbetsflödeskörningens åtgärdsuttryck. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/read | Läser arbetsflödets körningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Hämtar upprepningsspåren av arbetsflödeskörningens åtgärdsuttryck. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/read | Läser arbetsflödets körningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Läser arbetsflödeskörningen upprepning i historiken för begäran. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Läser arbetsflödeskörningen i historiken för begäran. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Läser arbetsflödet körningsåtgärdsomfång. |
> | Åtgärd | Microsoft.Logic/workflows/runs/cancel/action | Avbryter körningen av ett arbetsflöde. |
> | Åtgärd | Microsoft.Logic/workflows/runs/delete | Tar bort en körning av ett arbetsflöde. |
> | Åtgärd | Microsoft.Logic/workflows/runs/operations/read | Läser Åtgärdsstatus för arbetsflödeskörning. |
> | Åtgärd | Microsoft.Logic/workflows/runs/read | Läser arbetsflödeskörningen. |
> | Åtgärd | Microsoft.Logic/workflows/suspend/action | Gör uppehåll i arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/histories/read | Läser utlösarhistorik. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Skickar om arbetsflödesutlösare. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Hämtar Motringnings-URL för utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/read | Läser utlösarna. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/reset/action | Återställer utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/run/action | Utför utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/setState/action | Ställer in utlösartillstånd. |
> | Åtgärd | Microsoft.Logic/workflows/validate/action | Verifierar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/versions/read | Läser arbetsflödesversionen. |
> | Åtgärd | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Hämtar Motringnings-URL för utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/write | Skapar eller uppdaterar arbetsflödet. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Flytta alla Machine Learning-åtagande Plan Association |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Läsa alla Machine Learning-åtagande Plan Association |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/delete | Ta bort en dator som utbildningsplan åtagande |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/join/action | Ansluta till en dator som utbildningsplan åtagande |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/read | Läs valfri dator utbildningsplan åtagande |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/write | Skapa eller uppdatera eventuella Machine Learning åtagande Plan |
> | Åtgärd | Microsoft.MachineLearning/locations/operationresults/read | Hämta resultatet av en Machine Learning-åtgärd |
> | Åtgärd | Microsoft.MachineLearning/locations/operationsstatus/read | Hämta status för en pågående åtgärd i Machine Learning |
> | Åtgärd | Microsoft.MachineLearning/operations/read | Hämta Machine Learning-åtgärder |
> | Åtgärd | Microsoft.MachineLearning/register/action | Registrerar prenumerationen för web service-resursprovider för maskininlärning och gör det möjligt att skapa webbtjänster. |
> | Åtgärd | Microsoft.MachineLearning/skus/read | Hämta Machine Learning-åtagande Plan SKU: er |
> | Åtgärd | Microsoft.MachineLearning/webServices/action | Skapa regionala Web Service-Properties för regioner som stöds |
> | Åtgärd | Microsoft.MachineLearning/webServices/delete | Ta bort alla Machine Learning-webbtjänsten |
> | Åtgärd | Microsoft.MachineLearning/webServices/listkeys/read | Hämta nycklar till en Machine Learning-webbtjänst |
> | Åtgärd | Microsoft.MachineLearning/webServices/read | Läsa alla Machine Learning-webbtjänsten |
> | Åtgärd | Microsoft.MachineLearning/webServices/write | Skapa eller uppdatera eventuella Machine Learning-webbtjänsten |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/delete | Ta bort alla Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Lista över nycklar för ett Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/read | Läsa alla Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Omsynkronisering av nycklar för storage-konto som konfigurerats för ett Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/write | Skapa eller uppdatera eventuella Machine Learning-arbetsyta |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Kontrollera om det finns uppdateringar för systemtjänster för klustret driftsättning |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Ta bort alla som värd-konto |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Lista över de nycklar som associeras med klustret för driftsättning |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/read | Läs valfri värd-konto |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Uppdatera systemtjänster i ett kluster för driftsättning |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/write | Skapa eller uppdatera eventuella värdbaserade konto |
> | Åtgärd | Microsoft.MachineLearningCompute/register/action | Registrerar prenumerations-ID till resursprovidern och gör det möjligt att skapa en machine learning-beräkningsresurser |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Hämtar status för en viss beräkning-åtgärd |
> | Åtgärd | Microsoft.MachineLearningServices/locations/usages/read | Användningsrapport för aml beräkningsresurser i en prenumeration |
> | Åtgärd | Microsoft.MachineLearningServices/locations/vmsizes/read | Hämta storlekar som stöds |
> | Åtgärd | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Hämtar status för en viss arbetsyta-åtgärd |
> | Åtgärd | Microsoft.MachineLearningServices/register/action | Registrerar prenumerationen för Machine Learning Services-Resursprovidern |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/delete | Tar bort beräkningsresurser i Machine Learning-tjänster |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista över hemligheterna för beräkningsresurser i Machine Learning Services-arbetsyta |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/listNodes/action | Lista över noder för beräkningsresurs i Machine Learning Services-arbetsyta |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/read | Hämtar beräkningsresurserna i Machine Learning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/write | Skapar eller uppdaterar beräkningsresurser i Machine Learning-tjänster |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/delete | Tar bort de Machine Learning Services arbetsytor |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Skapar eller uppdaterar experiment i Machine Learning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista över hemligheterna för en arbetsyta för Machine Learning-tjänster |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/read | Hämtar de Machine Learning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/write | Skapar eller uppdaterar en arbetsyta/arbetsytor för Machine Learning-tjänster |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ManagedIdentity/register/action | Registrerar prenumerationen för resursprovidern hanterad identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC-åtgärd för att tilldela en befintlig användare Användartilldelad identitet till en resurs |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Tar bort en befintlig Användartilldelad identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hämtar en befintlig Användartilldelad identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/write | Skapar en ny Användartilldelad identitet eller uppdaterar taggarna associerade med en befintlig Användartilldelad identitet |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Management/checkNameAvailability/action | Kontrollerar om det angivna namnet är giltigt och unikt. |
> | Åtgärd | Microsoft.Management/getEntities/action | Lista över alla entiteter (Hanteringsgrupper, prenumerationer, osv.) för autentiserade användare. |
> | Åtgärd | Microsoft.Management/managementGroups/delete | Ta bort hanteringsgruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för autentiserade användare. |
> | Åtgärd | Microsoft.Management/managementGroups/subscriptions/delete | Ta bort associerar prenumeration från hanteringsgruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/subscriptions/write | Associates befintliga prenumeration med hanteringsgruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/write | Skapa eller uppdatera en hanteringsgrupp. |
> | Åtgärd | Microsoft.Management/register/action | Registrera den angivna prenumerationen med Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | DataAction | Microsoft.Maps/accounts/data/read | Ger tillgång till data läsåtkomst till en maps-konto. |
> | Åtgärd | Microsoft.Maps/accounts/delete | Ta bort en Maps-konto. |
> | Åtgärd | Microsoft.Maps/accounts/eventGridFilters/delete | Ta bort en Event Grid-filtret |
> | Åtgärd | Microsoft.Maps/accounts/eventGridFilters/read | Hämta en Event Grid-filtret |
> | Åtgärd | Microsoft.Maps/accounts/eventGridFilters/write | Skapa eller uppdatera en Event Grid-filtret |
> | Åtgärd | Microsoft.Maps/accounts/listKeys/action | Lista över nycklar för Maps-konto |
> | Åtgärd | Microsoft.Maps/accounts/read | Få en Maps-konto. |
> | Åtgärd | Microsoft.Maps/accounts/regenerateKey/action | Skapa ny Maps-konto primär eller sekundär nyckel |
> | Åtgärd | Microsoft.Maps/accounts/write | Skapa eller uppdatera en Maps-konto. |
> | Åtgärd | Microsoft.Maps/operations/read | Läs åtgärder för resursprovider |
> | Åtgärd | Microsoft.Maps/register/action | Registrera providern |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Returnerar ett avtal. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Godkänner en signerad avtal. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importerar en bild till slutanvändarens ACR. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Returnerar en konfiguration. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Sparar en konfiguration. |
> | Åtgärd | Microsoft.Marketplace/register/action | Registrerar resursprovidern för Microsoft.Marketplace i prenumerationen. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/delete | Gör en borttagningsåtgärd på en resurs för klassiska dev-tjänsten. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Hämtar en klassiska dev tjänstnycklar resource management. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Hämtar enkel inloggning på URL: en för en klassiska dev-tjänst. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/read | Gör en GET-åtgärd i en klassiska dev-tjänst. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Genererar en klassiska dev service resource management-nycklar. |
> | Åtgärd | Microsoft.MarketplaceApps/Operations/read | Läs-och skrivåtgärder för alla typer av resurser. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Avbryt ett avtal för en viss marketplace-objekt |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Returnera ett avtal för en viss marketplace-objekt |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Registrera ett avtal för en viss marketplace-objekt |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/read | Returnera alla avtal under den angivna prenumerationen |
> | Åtgärd | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Hämta ett avtal för en viss marketplace-objekt för virtuell dator |
> | Åtgärd | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Logga in eller Avbryt ett avtal för en viss marketplace-objekt för virtuell dator |
> | Åtgärd | Microsoft.MarketplaceOrdering/operations/read | Lista över alla möjliga åtgärder i API: et |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Media/checknameavailability/action | Kontrollerar om det finns ett Media Services-kontonamn |
> | Åtgärd | Microsoft.Media/locations/checkNameAvailability/action | Kontrollerar om det finns ett Media Services-kontonamn |
> | Åtgärd | Microsoft.Media/mediaservices/accountfilters/delete | Ta bort alla kontofilter |
> | Åtgärd | Microsoft.Media/mediaservices/accountfilters/read | Läsa alla kontofilter |
> | Åtgärd | Microsoft.Media/mediaservices/accountfilters/write | Skapa eller uppdatera eventuella kontofilter |
> | Åtgärd | Microsoft.Media/mediaservices/assets/assetfilters/delete | Ta bort tillgången Filter |
> | Åtgärd | Microsoft.Media/mediaservices/assets/assetfilters/read | Läsa tillgången Filter |
> | Åtgärd | Microsoft.Media/mediaservices/assets/assetfilters/write | Skapa eller uppdatera tillgången Filter |
> | Åtgärd | Microsoft.Media/mediaservices/assets/delete | Ta bort en |
> | Åtgärd | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Hämta krypteringsnyckel för tillgången |
> | Åtgärd | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista tillgången behållare SAS URL: er |
> | Åtgärd | Microsoft.Media/mediaservices/assets/listStreamingLocators/action | Lista Strömningslokaliserare för tillgången |
> | Åtgärd | Microsoft.Media/mediaservices/assets/read | Läsa alla tillgångar |
> | Åtgärd | Microsoft.Media/mediaservices/assets/write | Skapa eller uppdatera en |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Ta bort innehåll viktiga princip |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Hämta Principegenskaper för med hemligheter |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/read | Läsa innehåll viktiga princip |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/write | Skapa eller uppdatera innehåll viktiga princip |
> | Åtgärd | Microsoft.Media/mediaservices/delete | Ta bort alla Media Services-konto |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/delete | Ta bort några Event Grid-filtret |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/read | Läs några Event Grid-filtret |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/write | Skapa eller uppdatera några Event Grid-filtret |
> | Åtgärd | Microsoft.Media/mediaservices/liveEventOperations/read | Läsa alla åtgärder som Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/delete | Ta bort alla Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Ta bort några Live utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Läsa alla Live utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Skapa eller uppdatera några Live utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/read | Läsa alla Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/reset/action | Återställ alla åtgärder som Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/start/action | Starta direktsändningen åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/stop/action | Stoppa alla åtgärder som Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/write | Skapa eller uppdatera alla Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveOutputOperations/read | Läsa alla Live utdata-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/read | Läsa alla Media Services-konto |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Läsa alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/delete | Ta bort alla slutpunkten för direktuppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/read | Läs valfri slutpunkt för direktuppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skala alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Starta alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Stoppa alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/write | Skapa eller uppdatera valfri slutpunkt för direktuppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/delete | Ta bort alla positionerare för direktuppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista nycklar |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista över sökvägar |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/read | Läsa alla positionerare för direktuppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/write | Skapa eller uppdatera alla positionerare för direktuppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingPolicies/delete | Ta bort alla strömmande princip |
> | Åtgärd | Microsoft.Media/mediaservices/streamingPolicies/read | Läsa strömmande princip |
> | Åtgärd | Microsoft.Media/mediaservices/streamingPolicies/write | Skapa eller uppdatera eventuella strömmande principer |
> | Åtgärd | Microsoft.Media/mediaservices/syncStorageKeys/action | Synkronisera Lagringsnycklar för ett anslutna Azure Storage-konto |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/delete | Ta bort en transformering |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Avbryt jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/delete | Ta bort alla jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/read | Läsa alla jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/write | Skapa eller uppdatera jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/read | Läsa en transformering |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/write | Skapa eller uppdatera en transformering |
> | Åtgärd | Microsoft.Media/mediaservices/write | Skapa eller uppdatera alla Media Services-konto |
> | Åtgärd | Microsoft.Media/operations/read | Hämta tillgängliga åtgärder |
> | Åtgärd | Microsoft.Media/register/action | Registrerar prenumerationen för Media Services-resursprovidern och gör det möjligt att skapa Media Services-konton |
> | Åtgärd | Microsoft.Media/unregister/action | Avregistrerar prenumerationen för Media Services-resursprovidern |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/assessments/read | Listor utvärderingar i ett projekt |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/delete | Tar bort projektet utvärdering |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Hämta egenskaperna för en utvärderad dator |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Tar bort en utvärdering |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Hämtar en utvärderingsrapport URL |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Hämtar egenskaperna för en utvärdering |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Skapar en ny utvärdering eller uppdaterar en befintlig utvärdering |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/delete | Tar bort en grupp |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/read | Hämta egenskaperna för en grupp |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Uppdateringsgrupp genom att lägga till eller ta bort datorer |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/write | Skapar en ny grupp eller uppdaterar en befintlig grupp |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Tar bort Hyper-v-insamlaren |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Hämtar egenskaperna för Hyper-v-insamlare |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Skapar en ny Hyper-v-insamlare eller uppdaterar en befintlig Hyper-v-insamlare |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/machines/read | Hämtar egenskaperna för en virtuell dator |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/read | Hämtar egenskaperna för utvärderingsprojekt |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Tar bort insamlaren VMware |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Hämtar egenskaperna för VMware-insamlare |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Skapar en ny VMware-insamlare eller uppdaterar en befintlig VMware-insamlare |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/write | Skapar ett nytt utvärderingsprojekt eller uppdaterar ett befintligt utvärderingsprojekt |
> | Åtgärd | Microsoft.Migrate/locations/assessmentOptions/read | Hämtar de utvärderingsalternativ som är tillgängliga i den angivna platsen |
> | Åtgärd | Microsoft.Migrate/locations/checknameavailability/action | Kontrollerar tillgängligheten för resursnamnet för den givna prenumerationen på den angivna platsen |
> | Åtgärd | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Hämtar egenskaperna för en databasinstans |
> | Åtgärd | Microsoft.Migrate/migrateprojects/Databases/read | Hämtar egenskaperna för en databas |
> | Åtgärd | Microsoft.Migrate/migrateprojects/delete | Tar bort ett migrate-projekt |
> | Åtgärd | Microsoft.Migrate/migrateprojects/machines/read | Hämtar egenskaperna för en virtuell dator |
> | Åtgärd | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Tar bort en migrera händelse |
> | Åtgärd | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Hämtar egenskaperna för en migrera händelser. |
> | Åtgärd | Microsoft.Migrate/migrateprojects/read | Hämtar egenskaperna för migrate-projekt |
> | Åtgärd | Microsoft.Migrate/migrateprojects/registerTool/action | Registrerar verktyget till ett migrate-projekt |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Rensa datamigrering för projekt-lösning |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/Delete | Tar bort en lösning för migrate-projekt |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Hämtar migrera lösning projektkonfiguration |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/read | Hämtar egenskaperna för migrera projektlösning |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/write | Skapar en ny lösning för migrate-projekt eller uppdaterar en befintlig lösning för migrate-projekt |
> | Åtgärd | Microsoft.Migrate/migrateprojects/write | Skapar ett nytt migrate-projekt eller uppdaterar ett befintligt migrate-projekt |
> | Åtgärd | Microsoft.Migrate/Operations/read | Visar en lista över åtgärder som är tillgängliga på resursprovidern Microsoft.Migrate |
> | Åtgärd | Microsoft.Migrate/projects/assessments/read | Listor utvärderingar i ett projekt |
> | Åtgärd | Microsoft.Migrate/projects/delete | Tar bort projektet |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Hämta egenskaperna för en utvärderad dator |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/delete | Tar bort en utvärdering |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Hämtar en utvärderingsrapport URL |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/read | Hämtar egenskaperna för en utvärdering |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/write | Skapar en ny utvärdering eller uppdaterar en befintlig utvärdering |
> | Åtgärd | Microsoft.Migrate/projects/groups/delete | Tar bort en grupp |
> | Åtgärd | Microsoft.Migrate/projects/groups/read | Hämta egenskaperna för en grupp |
> | Åtgärd | Microsoft.Migrate/projects/groups/write | Skapar en ny grupp eller uppdaterar en befintlig grupp |
> | Åtgärd | Microsoft.Migrate/projects/keys/action | Hämtar delade nycklar för projektet |
> | Åtgärd | Microsoft.Migrate/projects/machines/read | Hämtar egenskaperna för en virtuell dator |
> | Åtgärd | Microsoft.Migrate/projects/read | Hämtar egenskaperna för ett projekt |
> | Åtgärd | Microsoft.Migrate/projects/write | Skapar ett nytt projekt eller uppdaterar ett befintligt projekt |
> | Åtgärd | Microsoft.Migrate/register/action | Registrerar prenumerationen med Microsoft.Migrate-resursprovidern |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MixedReality/register/action | Registrerar en prenumeration för resursprovidern Mixad verklighet. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Skapa spatial fästpunkter |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Ta bort spatial ankare |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Upptäcka Närliggande spatial ankare |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Hämta egenskaper för spatial ankare |
> | Åtgärd | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Åtgärd | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för Microsoft.MixedReality/SpatialAnchorsAccounts |
> | Åtgärd | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Microsoft.MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Leta upp spatial ankare |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att förbättra kvaliteten på tjänsten Azure Spatial ankare |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Uppdatera egenskaper för spatial ankare |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.NetApp/locations/operationresults/read | Läser en resurs för resultatet av åtgärden. |
> | Åtgärd | Microsoft.NetApp/locations/read | Läsningar ett tillgänglighet kontrollera resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Tar bort en poolresurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/read | Läser en poolresurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Tar bort en resurs för volymen. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Läser en monteringspunkt målresurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Läser en volymresursen. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Tar bort en ögonblicksbild-resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Läser en ögonblicksbild-resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Skriver en ögonblicksbild-resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Skriver en volymresursen. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/write | Skriver en poolresurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/delete | Tar bort en konto-resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/read | Läser en konto-resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/write | Skriver en konto-resurs. |
> | Åtgärd | Microsoft.NetApp/Operations/read | Läser en åtgärd resurser. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Hämta Application Gateway tillgänglig begärandehuvuden |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Hämta Application Gateway tillgänglig svarshuvud |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Hämta tillgängliga servervariabler för Application Gateway |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl fördefinierade princip |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway tillgänglig Ssl-alternativ |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Hämtar tillgängliga Waf regeluppsättningar Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter till en application gateway backend-adresspool. Det kanske inte. |
> | Åtgärd | Microsoft.Network/applicationGateways/backendhealth/action | Hämtar en application gateway serverdelens hälsotillstånd |
> | Åtgärd | Microsoft.Network/applicationGateways/delete | Tar bort en Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/read | Hämtar en application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/start/action | Startar en application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/stop/action | Stoppar en application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/write | Skapar en application gateway eller uppdaterar en application gateway |
> | Åtgärd | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/delete | Tar bort en Application Gateway WAF-princip |
> | Åtgärd | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/read | Hämtar en Application Gateway WAF-princip |
> | Åtgärd | Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/write | Skapar en princip för Application Gateway WAF eller uppdaterar en Application Gateway WAF-princip |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/delete | Tar bort en säkerhetsgrupp för programmet |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Ansluter till en IP-konfiguration till Programsäkerhetsgrupper. Det kanske inte. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Ansluter till en säkerhetsregel till Programsäkerhetsgrupper. Det kanske inte. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Visar IP-konfigurationer i ApplicationSecurityGroup |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/read | Hämtar en Programsäkerhetsgrupp-ID. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/write | Skapar en Programsäkerhetsgrupp eller uppdaterar en befintlig Programsäkerhetsgrupp. |
> | Åtgärd | Microsoft.Network/azureFirewallFqdnTags/read | Hämtar Azure-brandväggen FQDN taggar |
> | Åtgärd | Microsoft.Network/azurefirewalls/delete | Ta bort Brandvägg för Azure |
> | Åtgärd | Microsoft.Network/azurefirewalls/read | Få Azure-brandväggen |
> | Åtgärd | Microsoft.Network/azurefirewalls/write | Skapar eller uppdaterar en Azure-brandvägg |
> | Åtgärd | Microsoft.Network/bastionHosts/delete | Tar bort en Skyddsmiljö-värd |
> | Åtgärd | Microsoft.Network/bastionHosts/read | Hämtar en Skyddsmiljö-värd |
> | Åtgärd | Microsoft.Network/bastionHosts/write | Skapa eller uppdatera en Skyddsmiljö-värd |
> | Åtgärd | Microsoft.Network/bgpServiceCommunities/read | Hämta Bgp Tjänstwebbgrupper |
> | Åtgärd | Microsoft.Network/checkFrontDoorNameAvailability/action | Kontrollerar om det finns ett ytterdörren namn |
> | Åtgärd | Microsoft.Network/checkTrafficManagerNameAvailability/action | Kontrollerar tillgängligheten för ett Traffic Manager relativt DNS-namn. |
> | Åtgärd | Microsoft.Network/connections/delete | Tar bort VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/connections/read | Hämtar VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/connections/revoke/action | Markerar en Express Route-anslutningsstatus som återkallats |
> | Åtgärd | Microsoft.Network/connections/sharedkey/action | Hämta VirtualNetworkGatewayConnection SharedKey |
> | Åtgärd | Microsoft.Network/connections/sharedKey/read | Hämtar VirtualNetworkGatewayConnection SharedKey |
> | Åtgärd | Microsoft.Network/connections/sharedKey/write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection SharedKey |
> | Åtgärd | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Hämtar Vpn-enhetskonfiguration av VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/connections/write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/delete | Tar bort en DDoS anpassad princip |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/read | Hämtar en DDoS anpassad principdefinition Definition |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/write | Skapar en princip för anpassad DDoS eller uppdaterar en befintlig anpassad DDoS-princip |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/delete | Tar bort en DDoS-skyddsplanen |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/join/action | Ansluter till en DDoS-skyddsplan. Det kanske inte. |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/read | Hämtar en DDoS-skyddsplanen |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/write | Skapar en Plan för DDoS Protection eller uppdaterar en Plan för DDoS Protection  |
> | Åtgärd | Microsoft.Network/dnsoperationresults/read | Hämtar resultaten för en DNS-åtgärd |
> | Åtgärd | Microsoft.Network/dnsoperationstatuses/read | Hämtar status för en DNS-åtgärd  |
> | Åtgärd | Microsoft.Network/dnszones/A/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”A” från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/A/read | Hämta postuppsättningen av typen ”A” i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/A/write | Skapa eller uppdatera en postuppsättning av typen ”A” i en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/delete | Ta bort uppsättningen av poster för ett givet namn och skriva AAAA om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/read | Hämta postuppsättningen av typen AAAA, i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/write | Skapa eller uppdatera en postuppsättning av typen ”AAAA” inom en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/all/read | Hämtar DNS-postuppsättningar för typer |
> | Åtgärd | Microsoft.Network/dnszones/CAA/delete | Ta bort uppsättningen av poster för ett givet namn och skriver CAA om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/CAA/read | Hämta postuppsättningen av typen CAA, i JSON-format. Uppsättningen av poster innehåller TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CAA/write | Skapa eller uppdatera en postuppsättning av typen ”CAA” inom en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/delete | Ta bort uppsättningen av poster för ett givet namn och skriver CNAME om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/read | Hämta postuppsättningen av typen CNAME, i JSON-format. Uppsättningen av poster innehåller TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/write | Skapa eller uppdatera en postuppsättning av typen ”CNAME” inom en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/delete | Ta bort DNS-zonen i JSON-format. Zonegenskaperna är taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. |
> | Åtgärd | Microsoft.Network/dnszones/MX/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”MX” från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/MX/read | Hämta postuppsättningen av typen ”MX” i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/MX/write | Skapa eller uppdatera en postuppsättning av typen ”MX” inom en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/NS/delete | Tar bort DNS-postuppsättning av typen NS |
> | Åtgärd | Microsoft.Network/dnszones/NS/read | Hämtar DNS-postuppsättning av typen NS |
> | Åtgärd | Microsoft.Network/dnszones/NS/write | Skapar eller uppdaterar DNS-postuppsättning av typen NS |
> | Åtgärd | Microsoft.Network/dnszones/PTR/delete | Ta bort uppsättningen av poster för ett givet namn och skriver PTR om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/PTR/read | Hämta postuppsättningen av typen PTR, i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/PTR/write | Skapa eller uppdatera en postuppsättning av typen ”PTR” inom en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/read | Hämta DNS-zonen i JSON-format. Zonegenskaperna är taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. Observera att det här kommandot inte hämta postuppsättningar som ingår i zonen. |
> | Åtgärd | Microsoft.Network/dnszones/recordsets/read | Hämtar DNS-postuppsättningar för typer |
> | Åtgärd | Microsoft.Network/dnszones/SOA/read | Hämtar DNS-postuppsättning av typen SOA |
> | Åtgärd | Microsoft.Network/dnszones/SOA/write | Skapar eller uppdaterar DNS-postuppsättning av typen SOA |
> | Åtgärd | Microsoft.Network/dnszones/SRV/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”SRV” från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/read | Hämta postuppsättningen av typen ”SRV” i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/write | Skapa eller uppdatera postuppsättningen av typen SRV |
> | Åtgärd | Microsoft.Network/dnszones/TXT/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”TXT” från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/read | Hämta postuppsättningen av typen TXT, i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/write | Skapa eller uppdatera en postuppsättning av typen ”TXT” inom en DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/write | Skapa eller uppdatera en DNS-zon i en resursgrupp.  Används för att uppdatera taggar på en DNS-zonresurs. Observera att det här kommandot inte kan användas för att skapa eller uppdatera postuppsättningar i zonen. |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/delete | Tar bort en ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/read | Hämtar en ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/delete | Tar bort en expressroute-krets |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/join/action | Ansluter till en Expressroute-krets. Det kanske inte. |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Hämtar en ExpressRouteCircuit Peering ArpTable |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Tar bort en ExpressRouteCircuit-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Hämtar en ExpressRouteCircuit-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit Anslutningsresurs |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/delete | Tar bort en ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Hämtar Peer Express Route-kretsen-anslutningen |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/read | Hämtar en ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Hämtar en ExpressRouteCircuit Peering RouteTable |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Hämtar en ExpressRouteCircuit Peering RouteTable sammanfattning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Hämtar en ExpressRouteCircuit Peering Stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/read | Hämta en expressroute-krets |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/stats/read | Hämtar en ExpressRouteCircuit Stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/write | Skapar eller uppdaterar en befintlig expressroute-krets |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/join/action | Ansluter till en Express Route mellan anslutning. Det kanske inte. |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Hämtar en Express-Route mellan anslutning Peering Arp-tabell |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Tar bort en Express-Route mellan anslutning Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/read | Hämtar en Express-Route mellan anslutning Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Hämtar en Express-Route mellan anslutning Peering routningstabell |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Hämtar en Express-Route Peering väg tabell Anslutningssammanfattning för olika plattformar |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/write | Skapar en mellan anslutning Peering i expressroute eller uppdaterar en befintlig Express Route mellan anslutning Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/read | Hämta Express Route mellan anslutning |
> | Åtgärd | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Tar bort en Expressroute-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Hämtar en Expressroute-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Skapar en Express Route-anslutningen eller uppdaterar en befintlig anslutning för Express Route |
> | Åtgärd | Microsoft.Network/expressRouteGateways/join/action | Ansluter till en Expressroute-Gateway. Det kanske inte. |
> | Åtgärd | Microsoft.Network/expressRouteGateways/read | Hämta Express Route-Gateway |
> | Åtgärd | Microsoft.Network/expressRoutePorts/delete | Tar bort ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePorts/join/action | Kopplar ihop Express Route-portar. Det kanske inte. |
> | Åtgärd | Microsoft.Network/expressRoutePorts/links/read | Gets ExpressRouteLink |
> | Åtgärd | Microsoft.Network/expressRoutePorts/read | Gets ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePorts/write | Skapar eller uppdaterar ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePortsLocations/read | Hämta Express Route portar platser |
> | Åtgärd | Microsoft.Network/expressRouteServiceProviders/read | Hämtar Express Route-leverantörer |
> | Åtgärd | Microsoft.Network/frontDoors/backendPools/delete | Tar bort en serverdelspool |
> | Åtgärd | Microsoft.Network/frontDoors/backendPools/read | Hämtar en serverdelspool |
> | Åtgärd | Microsoft.Network/frontDoors/backendPools/write | Skapar eller uppdaterar en serverdelspool |
> | Åtgärd | Microsoft.Network/frontDoors/delete | Tar bort en ytterdörren |
> | Åtgärd | Microsoft.Network/frontDoors/frontendEndpoints/delete | Tar bort en frontend-slutpunkt |
> | Åtgärd | Microsoft.Network/frontDoors/frontendEndpoints/disableHttps/action | Inaktiverar HTTPS på en Frontend-slutpunkt |
> | Åtgärd | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Aktiverar HTTPS på en Frontend-slutpunkt |
> | Åtgärd | Microsoft.Network/frontDoors/frontendEndpoints/read | Hämtar en frontend-slutpunkt |
> | Åtgärd | Microsoft.Network/frontDoors/frontendEndpoints/write | Skapar eller uppdaterar en frontend-slutpunkt |
> | Åtgärd | Microsoft.Network/frontDoors/healthProbeSettings/delete | Tar bort inställningar för avsökning av hälsotillstånd |
> | Åtgärd | Microsoft.Network/frontDoors/healthProbeSettings/read | Hämtar hälsotillstånd avsökningen inställningar |
> | Åtgärd | Microsoft.Network/frontDoors/healthProbeSettings/write | Skapar eller uppdaterar inställningar för avsökning av hälsotillstånd |
> | Åtgärd | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Skapar eller uppdaterar inställningar för belastningsutjämning |
> | Åtgärd | Microsoft.Network/frontDoors/loadBalancingSettings/read | Hämtar belastningsutjämning inställningar |
> | Åtgärd | Microsoft.Network/frontDoors/loadBalancingSettings/write | Skapar eller uppdaterar inställningar för belastningsutjämning |
> | Åtgärd | Microsoft.Network/frontDoors/purge/action | Ta bort cachelagrat innehåll från en ytterdörren |
> | Åtgärd | Microsoft.Network/frontDoors/read | Hämtar en ytterdörren |
> | Åtgärd | Microsoft.Network/frontDoors/routingRules/delete | Tar bort en routningsregel |
> | Åtgärd | Microsoft.Network/frontDoors/routingRules/read | Hämtar en routningsregel |
> | Åtgärd | Microsoft.Network/frontDoors/routingRules/write | Skapar eller uppdaterar en routningsregel |
> | Åtgärd | Microsoft.Network/frontDoors/validateCustomDomain/action | Verifierar en frontend-slutpunkt för en ytterdörren |
> | Åtgärd | Microsoft.Network/frontDoors/write | Skapar eller uppdaterar en ytterdörren |
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallManagedRuleSets/read | Hämtar Brandvägg för webbaserade program hanteras regeluppsättningar |
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Tar bort en brandväggsprincip för Web Application |
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Hämtar en brandväggsprincip för Web Application |
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Skapar eller uppdaterar en brandväggsprincip för Web Application |
> | Åtgärd | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en load balancer-serverdelsadresspool. Det kanske inte. |
> | Åtgärd | Microsoft.Network/loadBalancers/backendAddressPools/read | Hämtar en load balancer serverdel adresspool definition |
> | Åtgärd | Microsoft.Network/loadBalancers/delete | Tar bort en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/frontendIPConfigurations/join/action | Ansluter till en Frontend IP-konfigurationen för belastningsutjämnaren. Det kanske inte. |
> | Åtgärd | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Hämtar en load balancer frontend IP-konfigurationsdefinition |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter till en belastningsutjämnare inkommande NAT-pool. Det kanske inte. |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatPools/read | Hämtar en belastningsutjämnare inkommande nat-pooldefinitionen |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/delete | Tar bort en belastningsutjämningsregel för ingående nat |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter till en inkommande nat regel för belastningsutjämnaren. Det kanske inte. |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/read | Hämtar en belastningsutjämnare inkommande nat-Regeldefinitionen |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/write | Skapar en inkommande nat regel för belastningsutjämnaren eller uppdaterar en befintlig belastningen belastningsutjämnarens inkommande nat-regel |
> | Åtgärd | Microsoft.Network/loadBalancers/loadBalancingRules/read | Hämtar en load balancer belastningen belastningsutjämning Regeldefinitionen |
> | Åtgärd | Microsoft.Network/loadBalancers/networkInterfaces/read | Hämtar referenser till alla nätverksgränssnitt under en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/outboundRules/read | Hämtar en definition för load balancer utgående regel |
> | Åtgärd | Microsoft.Network/loadBalancers/probes/join/action | Kan använda avsökningar av en belastningsutjämnare. Med den här behörigheten healthProbe-egenskapen för VM scale referera set till exempel till avsökningen. Det kanske inte. |
> | Åtgärd | Microsoft.Network/loadBalancers/probes/read | Hämtar en belastningsutjämnaravsökning |
> | Åtgärd | Microsoft.Network/loadBalancers/read | Hämtar en definition för load balancer |
> | Åtgärd | Microsoft.Network/loadBalancers/virtualMachines/read | Hämtar referenser till alla virtuella datorer under en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/write | Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare |
> | Åtgärd | Microsoft.Network/localnetworkgateways/delete | Tar bort LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/localnetworkgateways/read | Hämtar LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/localnetworkgateways/write | Skapar eller uppdaterar en befintlig LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Hämtar automatiskt godkänd privat länk tjänster |
> | Åtgärd | Microsoft.Network/locations/availableDelegations/read | Hämtar tillgängliga delegeringar |
> | Åtgärd | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Hämtar tillgängliga privata Endpoint-resurser |
> | Åtgärd | Microsoft.Network/locations/bareMetalTenants/action | Allokerar eller validerar en Bare Metal-klient |
> | Åtgärd | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Kontrollerar stöd för Accelererat nätverk |
> | Åtgärd | Microsoft.Network/locations/checkDnsNameAvailability/read | Kontrollerar om dns-etikett är tillgängliga på den angivna platsen |
> | Åtgärd | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Kontrollerar privat Länktjänsten synlighet |
> | Åtgärd | Microsoft.Network/locations/operationResults/read | Hämtar resultat av en asynkron INLÄGG eller borttagningsåtgärd |
> | Åtgärd | Microsoft.Network/locations/operations/read | Hämtar åtgärden resurs som representerar statusen för en asynkron åtgärd |
> | Åtgärd | Microsoft.Network/locations/serviceTags/read | Hämta Tjänsttaggar |
> | Åtgärd | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Hämtar stöds storlekar på virtuella datorer |
> | Åtgärd | Microsoft.Network/locations/usages/read | Hämtar resurserna användningsstatistik |
> | Åtgärd | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Hämtar en lista med tillgängliga virtuella slutpunkten nätverkstjänster |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/delete | Tar bort en avsiktlig nätverksprincip |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/read | Hämtar en avsiktlig Principbeskrivningen för nätverk |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/write | Skapar en avsikt nätverksprincip eller uppdaterar en befintlig nätverk avsikt princip |
> | Åtgärd | Microsoft.Network/networkInterfaces/delete | Tar bort ett nätverksgränssnitt |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hämta Nätverkssäkerhetsgrupper konfigurerade på nätverksgränssnittet för den virtuella datorn |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Hämta routningstabellen som konfigurerats i nätverksgränssnitt för den virtuella datorn |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Ansluter till en IP-konfiguration av nätverksgränssnitt. Det kanske inte. |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/read | Hämtar en definition av nätverksgränssnittet IP-konfiguration.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/join/action | Ansluter till en virtuell dator till ett nätverksgränssnitt. Det kanske inte. |
> | Åtgärd | Microsoft.Network/networkInterfaces/loadBalancers/read | Hämtar alla belastningsutjämnare som nätverksgränssnittet är en del av |
> | Åtgärd | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverk-gränssnittet.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Tar bort en tryck konfiguration av nätverksgränssnitt. |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/read | Hämtar en tryck konfiguration av nätverksgränssnitt. |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/write | Skapar en trycker du på konfiguration av nätverksgränssnitt eller uppdaterar en befintlig trycker du på konfiguration av nätverksgränssnitt. |
> | Åtgärd | Microsoft.Network/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar en befintlig nätverksgränssnitt.  |
> | Åtgärd | Microsoft.Network/networkProfiles/delete | Tar bort en profil för nätverk |
> | Åtgärd | Microsoft.Network/networkProfiles/read | Hämtar en nätverksprofil |
> | Åtgärd | Microsoft.Network/networkProfiles/removeContainers/action | Tar bort behållare |
> | Åtgärd | Microsoft.Network/networkProfiles/setContainers/action | Anger behållare |
> | Åtgärd | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Anger behållaren nätverksgränssnitt |
> | Åtgärd | Microsoft.Network/networkProfiles/write | Skapar eller uppdaterar en nätverksprofil |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Hämtar en standard-säkerhetsregler |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/delete | Tar bort en grupp |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/join/action | Kopplar en nätverkssäkerhetsgrupp. Det kanske inte. |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/read | Hämtar en Gruppdefinition för network security |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/delete | Tar bort en säkerhetsregel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/read | Hämtar en säkerhetsregeldefinition |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/write | Skapar en säkerhetsregel eller uppdaterar en befintlig säkerhetsregel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/write | Skapar en grupp eller uppdaterar en befintlig nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.Network/networkWatchers/availableProvidersList/action | Returnerar alla tillgängliga internet som tillhandahåller tjänster för en angiven Azure-region. |
> | Åtgärd | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returnerar poängen relativ svarstid för internet-leverantörer från en angiven plats till Azure-regioner. |
> | Åtgärd | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfigurerar flödesloggar för en målresurs. |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/delete | Tar bort en Anslutningsövervakare |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Fråga övervaka anslutningen mellan angivna slutpunkter |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/read | Hämta information om Anslutningsövervakaren |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Börja övervaka anslutningen mellan angivna slutpunkter |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Stoppa/pausar övervakning anslutning mellan angivna slutpunkter |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/write | Skapar en Anslutningsövervakare |
> | Åtgärd | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifierar möjligheten att upprätta en TCP direktanslutning från en virtuell dator till en viss slutpunkt, inklusive en annan virtuell dator eller en godtycklig fjärrservern. |
> | Åtgärd | Microsoft.Network/networkWatchers/delete | Tar bort en nätverksbevakaren |
> | Åtgärd | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returnerar om paketet tillåts eller nekas till eller från en viss målplats. |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/delete | Tar bort en lins |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/query/action | Fråga övervaka nätverkstrafik på den angivna slutpunkten |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/read | Hämta information om lins |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/start/action | Börja övervaka nätverkstrafik på den angivna slutpunkten |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/stop/action | Stoppa/pausar övervaka nätverkstrafik på den angivna slutpunkten |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/write | Skapar en lins |
> | Åtgärd | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostik av nätverkskonfiguration. |
> | Åtgärd | Microsoft.Network/networkWatchers/nextHop/action | Returnera nästa hopptyp för angivet mål och målets IP-adress, och därefter hoppas att IP-adress. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/delete | Tar bort ett infångat paket |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Hämtar information om egenskaper och status för en resurs för avbildningen av paketet. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/read | Hämta packet capture definition |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stoppa pågående paket avbildningssessionen. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/write | Skapar ett infångat paket |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/delete | Tar bort en PingMesh |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/read | Hämta information om PingMesh |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/start/action | Starta PingMesh mellan angivna virtuella datorer |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Stoppa PingMesh mellan angivna virtuella datorer |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/write | Skapar en PingMesh |
> | Åtgärd | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Övervaka anslutning mellan angivna slutpunkter batchfråga |
> | Åtgärd | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Hämtar status för flow loggning på en resurs. |
> | Åtgärd | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Felsökning resultatet från körts tidigare eller för närvarande hämtar kör felsökning åtgärden. |
> | Åtgärd | Microsoft.Network/networkWatchers/read | Hämta network watcher-definition |
> | Åtgärd | Microsoft.Network/networkWatchers/securityGroupView/action | Visa de konfigurerade och gällande reglerna för nätverkssäkerhetsgrupper tillämpas på en virtuell dator. |
> | Åtgärd | Microsoft.Network/networkWatchers/topology/action | Hämtar en nätverk på vy över resurser och deras relationer i en resursgrupp. |
> | Åtgärd | Microsoft.Network/networkWatchers/troubleshoot/action | Startar felsökning på en resurs för nätverk i Azure. |
> | Åtgärd | Microsoft.Network/networkWatchers/write | Skapar en nätverksbevakare eller uppdaterar en befintlig nätverksbevakaren |
> | Åtgärd | Microsoft.Network/operations/read | Hämta tillgängliga åtgärder |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/delete | Tar bort en P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generera Vpn-profil för P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Hämtar en P2S Vpn-anslutningen hälsotillstånd för P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/read | Hämtar en P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/write | Placerar en P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/privateDnsOperationResults/read | Hämtar resultaten av en privat DNS åtgärden |
> | Åtgärd | Microsoft.Network/privateDnsOperationStatuses/read | Hämtar status för en privat DNS åtgärden |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”A” från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/read | Hämta postuppsättningen av typen ”A” i en privat DNS-zon i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/write | Skapa eller uppdatera en postuppsättning av typen ”A” i en privat DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/delete | Ta bort uppsättningen av poster för ett givet namn och skriva AAAA om du från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/read | Hämta postuppsättningen av typen AAAA om du i en privat DNS-zon i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/write | Skapa eller uppdatera en postuppsättning av typen ”AAAA” inom en privat DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/ALL/read | Hämtar privata DNS-postuppsättningar för typer |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/delete | Ta bort uppsättningen av poster för ett givet namn och skriver CNAME om du från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/read | Hämta postuppsättningen av typen CNAME om du i en privat DNS-zon i JSON-format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/write | Skapa eller uppdatera en postuppsättning av typen ”CNAME” inom en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/delete | Ta bort en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”MX” från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/read | Hämta postuppsättningen av typen ”MX” inom en privat DNS-zon i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/write | Skapa eller uppdatera en postuppsättning av typen ”MX” inom en privat DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/PTR/delete | Ta bort uppsättningen av poster för ett givet namn och skriver PTR om du från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/PTR/read | Hämta postuppsättningen av typen PTR om du i en privat DNS-zon i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/PTR/write | Skapa eller uppdatera en postuppsättning av typen ”PTR” inom en privat DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/read | Hämta privata DNS zonegenskaper i JSON-format. Observera att det här kommandot inte hämta virtuella nätverk som den privata DNS-zonen är länkad eller registrerar postuppsättningar finns i zonen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/recordsets/read | Hämtar privata DNS-postuppsättningar för typer |
> | Åtgärd | Microsoft.Network/privateDnsZones/SOA/read | Hämta postuppsättningen av typen SOA om du i en privat DNS-zon i JSON-format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SOA/write | Uppdatera en postuppsättning av typen ”SOA” inom en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”SRV” från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/read | Hämta postuppsättningen av typen ”SRV” inom en privat DNS-zon i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/write | Skapa eller uppdatera en postuppsättning av typen ”SRV” inom en privat DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/delete | Ta bort uppsättningen av poster för ett givet namn och skriver ”TXT” från en privat DNS-zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/read | Hämta postuppsättningen av typen ”TXT” inom en privat DNS-zon i JSON-format. Uppsättningen av poster innehåller en lista med poster som TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/write | Skapa eller uppdatera en postuppsättning av typen ”TXT” inom en privat DNS-zon. De poster som anges kommer att ersätta de aktuella posterna i postuppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Ta bort en privat DNS-zon länk till virtuellt nätverk. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Hämta privata DNS zon länk till egenskaper för virtuellt nätverk i JSON-format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Skapa eller uppdatera en privat DNS-zon länk till det virtuella nätverket. |
> | Åtgärd | Microsoft.Network/privateDnsZones/write | Skapa eller uppdatera en privat DNS-zon i en resursgrupp. Observera att det här kommandot inte kan användas för att skapa eller uppdatera virtuella nätverkslänkar eller uppsättningar av poster i zonen. |
> | Åtgärd | Microsoft.Network/privateEndpoints/delete | Tar bort en privat slutpunktsresurs. |
> | Åtgärd | Microsoft.Network/privateEndpoints/read | Hämtar en privat slutpunktsresurs. |
> | Åtgärd | Microsoft.Network/privateEndpoints/write | Skapar en ny privat slutpunkt eller uppdaterar en befintlig privat slutpunkt. |
> | Åtgärd | Microsoft.Network/privateLinkServices/delete | Tar bort en privat länk-tjänstresurs. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Tar bort en privat slutpunkt-anslutning. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Hämtar en privat slutpunkt anslutningsdefinitionen. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Skapar en ny privat slutpunkt-anslutning eller uppdaterar en befintlig privat slutpunkt-anslutning. |
> | Åtgärd | Microsoft.Network/privateLinkServices/read | Hämtar en privat länk-tjänstresurs. |
> | Åtgärd | Microsoft.Network/privateLinkServices/write | Skapar en ny privat länk-tjänst eller uppdaterar en befintlig privat länk-tjänst. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/delete | Tar bort en offentlig Ip-adress. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig ip-adress. Det kanske inte. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/write | Skapar en offentlig Ip-adress eller uppdaterar en befintlig offentlig Ip-adress.  |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/delete | Tar bort ett offentligt Ip-Prefix |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/join/action | Ansluter till en PublicIPPrefix. Det kanske inte. |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/read | Hämtar en Definition av offentliga Ip-Prefix |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/write | Skapar ett offentligt Ip-Prefix eller uppdaterar en befintlig offentlig Ip-Prefix |
> | Åtgärd | Microsoft.Network/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.Network/routeFilters/delete | Tar bort en väg filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/join/action | Ansluter till ett flödesfilter. Det kanske inte. |
> | Åtgärd | Microsoft.Network/routeFilters/read | Hämtar en väg filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/delete | Tar bort en regel för vägen filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/read | Hämtar en regel för vägen filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/write | Skapar en flödesfilterregeln eller uppdaterar en befintlig flödesfilterregeln |
> | Åtgärd | Microsoft.Network/routeFilters/write | Skapar ett flödesfilter eller uppdaterar ett befintligt route-filter |
> | Åtgärd | Microsoft.Network/routeTables/delete | Tar bort en väg tabelldefinition |
> | Åtgärd | Microsoft.Network/routeTables/join/action | Ansluter till en routningstabell. Det kanske inte. |
> | Åtgärd | Microsoft.Network/routeTables/read | Hämtar en väg tabelldefinition |
> | Åtgärd | Microsoft.Network/routeTables/routes/delete | Tar bort en väg-definition |
> | Åtgärd | Microsoft.Network/routeTables/routes/read | Hämtar en väg-definition |
> | Åtgärd | Microsoft.Network/routeTables/routes/write | Skapar en väg eller uppdaterar ett befintligt flöde |
> | Åtgärd | Microsoft.Network/routeTables/write | Skapar en routningstabell eller uppdaterar en befintlig routningstabell |
> | Åtgärd | Microsoft.Network/securegateways/delete | Delete Secure Gateway |
> | Åtgärd | Microsoft.Network/securegateways/read | Få säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/write | Skapar eller uppdaterar en säker Gateway |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/delete | Tar bort en Tjänstslutpunktsprincip |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/join/action | Ansluter till en Tjänstslutpunktsprincip. Det kanske inte. |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Ansluter till ett undernät till Tjänstslutpunktsprinciperna. Det kanske inte. |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/read | Hämtar en tjänstbeskrivning Endpoint princip |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Tar bort en Tjänstslutpunktens Principdefinition |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Hämtar en slutpunkt Policy Definition tjänstbeskrivning |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Skapar en Tjänstslutpunktens Principdefinition eller uppdaterar en befintlig Tjänstslutpunktens Principdefinition |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/write | Skapar en Tjänstslutpunktsprincip eller uppdaterar en befintlig Tjänstslutpunktsprincipen |
> | Åtgärd | Microsoft.Network/trafficManagerGeographicHierarchies/read | Hämtar Traffic Manager geografiska hierarkin som innehåller regioner som kan användas med geografisk routningsmetod |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Tar bort en Azure-slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager slutar att dirigera trafiken till den borttagna Azure-slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Hämtar en Azure-slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för att Azure-slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Lägg till en ny Azure-slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig Azure-slutpunkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/delete | Ta bort Traffic Manager-profilen. Alla inställningar som är associerade med Traffic Manager-profilen kommer att gå förlorade och profilen kan inte längre användas för att dirigera trafik. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Tar bort en extern slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager slutar att dirigera trafiken till den externa slutpunkten har tagits bort. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Hämtar en extern slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den externa slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Lägg till en ny extern slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig extern slutpunkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Hämtar den termiska kartan för Traffic Manager för den angivna Traffic Manager-profil som innehåller antalet och svarstid frågedata efter plats och käll-IP. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Tar bort en kapslad slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager slutar att dirigera trafiken till slutpunkten kapslade har tagits bort. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Hämtar en kapslad slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den kapslade slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Lägg till en ny kapslad slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig kapslad slutpunkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/read | Hämta profilkonfiguration för Traffic Manager. Detta inkluderar DNS-inställningar, inställningar för routning av trafik, övervakningsinställningarna för slutpunkten och listan över slutpunkter som dirigeras genom den här Traffic Manager-profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/write | Skapa en Traffic Manager-profil eller ändra konfigurationen för en befintlig Traffic Manager-profil.<br>Detta inkluderar att aktivera eller inaktivera en profil och ändra DNS-inställningar, inställningar för routning av trafik eller inställningarna för övervakning av slutpunkt.<br>Slutpunkter vidarebefordras av Traffic Manager-profilen kan har lagts till, tas bort, aktiveras eller inaktiveras. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Tar bort den prenumerationsnivå nyckel som används vid insamling av Användarmått i realtid. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/read | Hämtar den prenumerationsnivå nyckel som används vid insamling av Användarmått i realtid. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/write | Skapar en ny prenumerationsnivå-nyckel som ska användas vid insamling av Användarmått i realtid. |
> | Åtgärd | Microsoft.Network/unregister/action | Avregistrerar prenumerationen |
> | Åtgärd | Microsoft.Network/virtualHubs/delete | Tar bort en virtuell hubb |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Tar bort en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Hämta en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Skapa eller uppdatera en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/read | Hämta en virtuell hubb |
> | Åtgärd | Microsoft.Network/virtualHubs/write | Skapa eller uppdatera en virtuell hubb |
> | Åtgärd | microsoft.network/virtualnetworkgateways/connections/read | Hämta VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/delete | Tar bort en virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Skapa VpnClient-paket för virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Skapa paket för VpnProfile för VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Hämtar virtualNetworkGateway annonseras vägar |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Hämtar virtualNetworkGateway bgp-peer-status |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Hämtar virtualnetworkgateway lärt dig vägar |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Hämta Per anslutningsstatusen för Vpn-klienten för VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Hämta Vpnclient Ipsec parametrar för VirtualNetworkGateway P2S-klient. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Hämtar URL för en profil för förgenererade VPN-klientpaketet |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/read | Hämtar en VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/reset/action | Återställer en virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Återställa Vpnclient delad nyckel för VirtualNetworkGateway P2S-klient. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ange Vpnclient Ipsec-parametrarna för VirtualNetworkGateway P2S-klient. |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Listor stöds Vpn-enheter |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/write | Skapar eller uppdaterar en VirtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualNetworks/BastionHosts/action | Hämtar referenser Skyddsmiljö-värd i ett virtuellt nätverk. |
> | Åtgärd | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Hämtar referenser Skyddsmiljö-värd i ett virtuellt nätverk. |
> | Åtgärd | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Kontrollera om Ip-adress är tillgänglig på det angivna virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/delete | Tar bort ett virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/peer/action | Peerar ett virtuellt nätverk med ett annat virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/delete | Tar bort ett virtuellt nätverksundernät |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Det kanske inte. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till resursen, till exempel storage-konto eller SQL-databas till ett undernät. Det kanske inte. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Förbereder ett undernät genom att tillämpa nödvändiga nätverksprinciper |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/read | Hämtar en definition av undernät för virtuella nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Hämtar referenser till alla virtuella datorer i ett virtuellt nätverksundernät |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/write | Skapar ett virtuellt nätverksundernät eller uppdaterar ett befintligt undernät för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/usages/read | Hämta IP-användningar för varje undernät i det virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualMachines/read | Hämtar referenser till alla virtuella datorer i ett virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Tar bort en virtuell nätverkspeering |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Hämtar en definition för peering av virtuella nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Skapar en virtuell nätverkspeering eller uppdaterar en befintlig virtuell nätverkspeering |
> | Åtgärd | Microsoft.Network/virtualNetworks/write | Skapar ett virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/delete | Ta bort virtuellt nätverk tryck |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/join/action | Ansluter till ett virtuellt nätverk tryck. Det kanske inte. |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/read | Hämta tryck för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/write | Skapa eller uppdatera tryck för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualWans/delete | Tar bort ett virtuellt Wan |
> | Åtgärd | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Tar bort ett virtuellt Wan P2SVpnServerConfiguration |
> | Åtgärd | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Hämtar en virtuellt Wan P2SVpnServerConfiguration |
> | Åtgärd | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Skapar ett virtuellt Wan P2SVpnServerConfiguration eller uppdaterar ett befintligt virtuellt Wan P2SVpnServerConfiguration |
> | Åtgärd | Microsoft.Network/virtualWans/read | Hämta en virtuellt Wan |
> | Åtgärd | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Hämtar stöds VirtualWan säkerhetsleverantörer. |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubs/read | Hämtar alla virtuella hubbar som refererar till ett virtuellt WAN-nätverk. |
> | Åtgärd | Microsoft.Network/virtualwans/vpnconfiguration/action | Hämtar en Vpn-konfiguration |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSites/read | Hämtar alla VPN-platser som refererar till ett virtuellt WAN-nätverk. |
> | Åtgärd | Microsoft.Network/virtualWans/write | Skapa eller uppdatera ett virtuellt WAN-nätverk |
> | Åtgärd | Microsoft.Network/vpnGateways/delete | Tar bort en VpnGateway. |
> | Åtgärd | microsoft.network/vpngateways/listvpnconnectionshealth/action | Hämtar anslutningsstatusen för alla eller en delmängd av anslutningar på en VpnGateway |
> | Åtgärd | Microsoft.Network/vpnGateways/read | Hämtar en VpnGateway. |
> | Åtgärd | microsoft.network/vpngateways/reset/action | Återställer en VpnGateway |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/delete | Tar bort en VpnConnection. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/read | Hämtar en VpnConnection. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/write | Placerar en VpnConnection. |
> | Åtgärd | Microsoft.Network/vpnGateways/write | Placerar en VpnGateway. |
> | Åtgärd | Microsoft.Network/vpnsites/delete | Tar bort en resurs för Vpn-platsen. |
> | Åtgärd | Microsoft.Network/vpnsites/read | Hämtar en resurs för Vpn-platsen. |
> | Åtgärd | Microsoft.Network/vpnsites/write | Skapar eller uppdaterar en plats för Vpn-resurs. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Kontrollerar huruvida ett visst Namespace-resursnamn finns i NotificationHub-tjänsten. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Hämta listan över auktoriseringsregler för namnområden beskrivning. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till Namespace |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Hämta listan över auktoriseringsregler för namnområden beskrivning. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Namespace auktorisering återskapa primär/sekundär nyckel, ange den nyckel som behöver återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Kontrollerar huruvida ett visst NotificationHub-namn är tillgängligt i en Namespace. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/Delete | Ta bort Namespace-resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Hämta listan över auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Ta bort auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Hämta anslutningssträngen till Meddelandehubben |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Hämta listan över auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub auktorisering återskapa primär/sekundär nyckel, ange den nyckel som behöver återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Skapa auktoriseringsregler för Notification Hub och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Skicka ett push-testmeddelande. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Ta bort Notification Hub-resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Hämta alla Notification Hub PNS-autentiseringsuppgifter. Detta omfattar autentiseringsuppgifter för WNS, MPNS, APNS, GCM och Baidu |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Hämta listan över resursbeskrivningar för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Skapa en Meddelandehubb och uppdatera dess egenskaper. Egenskaperna är främst PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/read | Hämta listan över Namespace beskrivningar |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/write | Skapa en Namespace-resurs och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/operationResults/read | Returnerar Åtgärdsresultat för Notification Hubs-providern |
> | Åtgärd | Microsoft.NotificationHubs/operations/read | Returnerar en lista med åtgärder som stöds för Notification Hubs-providern |
> | Åtgärd | Microsoft.NotificationHubs/register/action | Registrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs |
> | Åtgärd | Microsoft.NotificationHubs/unregister/action | Avregistrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/read | Hämtar egenskaperna för ett Hyper-V-kluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/write | Skapar eller uppdaterar Hyper-V-kluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/delete | Tar bort Hyper-V-platsen |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/read | Hämtar egenskaperna för en Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/write | Skapar eller uppdaterar Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/jobs/read | Hämtar egenskaperna för en Hyper-V-jobb |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/read | Hämtar egenskaperna för en Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/start/action | Starta Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/stop/action | Stoppar Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hämtar egenskaperna för en Hyper-V-Åtgärdsstatus |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/read | Hämtar egenskaperna för en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/refresh/action | Uppdaterar objekt i en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hämtar egenskaperna för ett Hyper-V som kör som-konton |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/usage/read | Hämtar användningar av en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/write | Skapar eller uppdaterar Hyper-V-platsen |
> | Åtgärd | Microsoft.OffAzure/Operations/read | Läser de exponerade åtgärderna |
> | Åtgärd | Microsoft.OffAzure/register/action | Registrerar prenumerationen med Microsoft.OffAzure-resursprovidern |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/delete | Tar bort VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/jobs/read | Hämtar egenskaperna för en VMware-jobb |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/read | Hämtar egenskaperna för en VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/start/action | Starta VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stoppar VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Hämtar egenskaperna för en VMware-Åtgärdsstatus |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/read | Hämtar egenskaperna för en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/refresh/action | Uppdaterar objekt inom en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Hämtar egenskaperna för en VMware kör som-konton |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/usage/read | Hämtar användningar av en VMware-plats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/read | Hämtar egenskaperna för en VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/write | Skapar eller uppdaterar VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/write | Skapar eller uppdaterar den VMware-webbplatsen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationalInsights/linkTargets/read | Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Använd ett kund-id som returneras av den här åtgärden i kund-id-egenskapen för åtgärden Skapa arbetsyta för att länka Azure-prenumerationen till en arbetsyta. |
> | Åtgärd | microsoft.operationalinsights/operations/read | Visar en lista över alla tillgängliga OperationalInsights Rest API-åtgärder. |
> | Åtgärd | microsoft.operationalinsights/register/action | Rergisters prenumerationen. |
> | Åtgärd | Microsoft.OperationalInsights/register/action | Registrera en prenumeration på en resursprovider. |
> | Åtgärd | microsoft.operationalinsights/unregister/action | Avregistrerar prenumerationen. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med den nya motorn. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Hämta sökschema V2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/action | Sök med den nya motorn. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Hämta sökschema V2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Ta bort konfigurationsomfång |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Hämta konfigurationsomfång |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ange konfigurationsomfång |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/delete | Ta bort datakällor under en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/read | Hämta datakällor under en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/write | Skapa/uppdatera datakällor under en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/delete | Tar bort en arbetsyta. Om arbetsytan var länkad till en befintlig arbetsyta vid tidpunkten för skapandet raderas inte den arbetsyta som den var länkad till. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/gateways/delete | Tar bort en gateway som konfigurerats för arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Skapar registreringscertifikat för arbetsytan. Det här certifikatet används för att ansluta Microsoft System Center Operation Manager till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Inaktiverar ett informationspaket för en viss arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Aktiverar ett informationspaket för en viss arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Visar en lista över informationspaket som visas för en viss arbetsyta och huruvida paketet är aktiverat eller inaktiverat för den arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Ta bort länkade tjänster för angiven arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/read | Hämta länkade tjänster för angiven arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/write | Skapa/uppdatera länkade tjänster för angiven arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/listKeys/action | Hämtar lista över nycklar för arbetsytan. De här nycklarna används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/listKeys/read | Hämtar lista över nycklar för arbetsytan. De här nycklarna används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/managementGroups/read | Hämtar namn och metadata för System Center Operations Manager-hanteringsgrupper som är anslutna till den här arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Hämta Måttdefinitioner under arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Ta bort användarens meddelandeinställningar för arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Hämta användarens meddelandeinställningar för arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Ange användarens meddelandeinställningar för arbetsytan. |
> | Åtgärd | Microsoft.operationalinsights/Workspaces/Operations/Read | Hämtar status för en OperationalInsights arbetsyta-åtgärd. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/purge/action | Ta bort angivna data från arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Läsa data från tabellen AADDomainServicesAccountLogon |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Läsa data från tabellen AADDomainServicesAccountManagement |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Läsa data från tabellen AADDomainServicesDirectoryServiceAccess |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Läsa data från tabellen AADDomainServicesLogonLogoff |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Läsa data från tabellen AADDomainServicesPolicyChange |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Läsa data från tabellen AADDomainServicesPrivilegeUse |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Läsa data från tabellen AADDomainServicesSystemSecurity |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Läsa data från tabellen ADAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Läsa data från tabellen ADFActivityRun |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Läsa data från tabellen ADFPipelineRun |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Läsa data från tabellen ADFTriggerRun |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Läsa data från tabellen ADReplicationResult |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Läsa data från tabellen ADSecurityAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Alert/read | Läsa data från tabellen avisering |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Läsa data från tabellen AlertHistory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Läsa data från tabellen AppCenterError |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Läsa data från tabellen ApplicationInsights |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Läsa data från tabellen AuditLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Läsa data från tabellen AutoscaleEvaluationsLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Läsa data från tabellen AutoscaleScaleActionsLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Läsa data från tabellen AWSCloudTrail |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Läsa data från tabellen AzureActivity |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Läsa data från tabellen AzureAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Läsa data från tabellen AzureMetrics |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BlockchainApplicationLog/read | Läsa data från tabellen BlockchainApplicationLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BlockchainProxyLog/read | Läsa data från tabellen BlockchainProxyLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Läsa data från tabellen BoundPort |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Läsa data från tabellen CommonSecurityLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Läsa data från tabellen ComputerGroup |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Läsa data från tabellen ConfigurationChange |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Läsa data från tabellen ConfigurationData |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Läsa data från tabellen ContainerImageInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Läsa data från tabellen ContainerInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Läsa data från tabellen ContainerLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Läsa data från tabellen ContainerNodeInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Läsa data från tabellen ContainerServiceLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksAccounts/read | Läsa data från tabellen DatabricksAccounts |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksClusters/read | Läsa data från tabellen DatabricksClusters |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksDBFS/read | Läsa data från tabellen DatabricksDBFS |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksJobs/read | Läsa data från tabellen DatabricksJobs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksNotebook/read | Läsa data från tabellen DatabricksNotebook |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Läsa data från tabellen DatabricksSecrets |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksSQLPermissions/read | Läsa data från tabellen DatabricksSQLPermissions |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksSSH/read | Läsa data från tabellen DatabricksSSH |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksTables/read | Läsa data från tabellen DatabricksTables |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksWorkspace/read | Läsa data från tabellen DatabricksWorkspace |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Läsa data från tabellen DeviceAppCrash |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Läsa data från tabellen DeviceAppLaunch |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Läsa data från tabellen DeviceCalendar |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Läsa data från tabellen DeviceCleanup |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Läsa data från tabellen DeviceConnectSession |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Läsa data från tabellen DeviceEtw |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Läsa data från tabellen DeviceHardwareHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Läsa data från tabellen devicehealth koppla |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Läsa data från tabellen DeviceHeartbeat |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Läsa data från tabellen DeviceSkypeHeartbeat |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Läsa data från tabellen DeviceSkypeSignIn |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Läsa data från tabellen DeviceSleepState |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Läsa data från tabellen DHAppFailure |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Läsa data från tabellen DHAppReliability |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Läsa data från tabellen DHDriverReliability |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Läsa data från tabellen DHLogonFailures |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Läsa data från tabellen DHLogonMetrics |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Läsa data från tabellen DHOSCrashData |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Läsa data från tabellen DHOSReliability |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Läsa data från tabellen DHWipAppLearning |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Läsa data från tabellen DnsEvents |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Läsa data från tabellen DnsInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Läsa data från tabellen ETWEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Event/read | Läsa data från tabellen händelse |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Läsa data från tabellen ExchangeAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Läsa data från tabellen ExchangeOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Läsa data från tabellen pulsslag |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Läsa data från tabellen HuntingBookmark |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Läsa data från tabellen IISAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Läsa data från tabellen InboundConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Läsa data från tabellen InsightsMetrics |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Läsa data från tabellen IntuneAuditLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Läsa data från tabellen IntuneOperationalLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Läsa data från tabellen KubeEvents |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Läsa data från tabellen KubeNodeInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Läsa data från tabellen KubePodInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Läsa data från tabellen KubeServices |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Läsa data från tabellen LinuxAuditLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Läsa data från tabellen MAApplication |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Läsa data från tabellen MAApplicationHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Läsa data från tabellen MAApplicationHealthAlternativeVersions |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Läsa data från tabellen MAApplicationHealthIssues |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Läsa data från tabellen MAApplicationInstance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Läsa data från tabellen MAApplicationInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Läsa data från tabellen MAApplicationReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Läsa data från tabellen MADeploymentPlan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Läsa data från tabellen MADevice |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Läsa data från tabellen MADeviceNotEnrolled |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Läsa data från tabellen MADeviceNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Läsa data från tabellen MADevicePnPHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Läsa data från tabellen MADevicePnPHealthAlternativeVersions |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Läsa data från tabellen MADevicePnPHealthIssues |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Läsa data från tabellen MADeviceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Läsa data från tabellen MADriverInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Läsa data från tabellen MADriverReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Läsa data från tabellen MAOfficeAddin |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Läsa data från tabellen MAOfficeAddinEntityHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Läsa data från tabellen MAOfficeAddinHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Läsa data från tabellen MAOfficeAddinHealthEventNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Läsa data från tabellen MAOfficeAddinHealthIssues |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Läsa data från tabellen MAOfficeAddinInstance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Läsa data från tabellen MAOfficeAddinInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Läsa data från tabellen MAOfficeAddinReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Läsa data från tabellen MAOfficeApp |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Läsa data från tabellen MAOfficeAppCrashesNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Läsa data från tabellen MAOfficeAppHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Läsa data från tabellen MAOfficeAppInstance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Läsa data från tabellen MAOfficeAppInstanceHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Läsa data från tabellen MAOfficeAppReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Läsa data från tabellen MAOfficeAppSessionsNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Läsa data från tabellen MAOfficeBuildInfo |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Läsa data från tabellen MAOfficeCurrencyAssessment |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Läsa data från tabellen MAOfficeCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Läsa data från tabellen MAOfficeDeploymentStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatusNRT/read | Läsa data från tabellen MAOfficeDeploymentStatusNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Läsa data från tabellen MAOfficeMacroErrorNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Läsa data från tabellen MAOfficeMacroGlobalHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Läsa data från tabellen MAOfficeMacroHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Läsa data från tabellen MAOfficeMacroHealthIssues |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Läsa data från tabellen MAOfficeMacroIssueInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Läsa data från tabellen MAOfficeMacroIssueReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Läsa data från tabellen MAOfficeMacroSummary |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Läsa data från tabellen MAOfficeSuite |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Läsa data från tabellen MAOfficeSuiteInstance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Läsa data från tabellen MAProposedPilotDevices |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Läsa data från tabellen MAWindowsBuildInfo |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Läsa data från tabellen MAWindowsCurrencyAssessment |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Läsa data från tabellen MAWindowsCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Läsa data från tabellen MAWindowsDeploymentStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatusNRT/read | Läsa data från tabellen MAWindowsDeploymentStatusNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Läsa data från tabellen MAWindowsSysReqInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebApplicationLog/read | Läsa data från tabellen MicrosoftWebApplicationLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebFunctionExecutionLogs/read | Läsa data från tabellen MicrosoftWebFunctionExecutionLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Läsa data från tabellen MicrosoftWebStdOutStdErrLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebW3CLog/read | Läsa data från tabellen MicrosoftWebW3CLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Läsa data från tabellen NetworkMonitoring |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Läsa data från tabellen OfficeActivity |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Operation/read | Läsa data från tabellen åtgärden |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Läsa data från tabellen OutboundConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Perf/read | Läsa data från tabellen Perf |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Läsa data från tabellen ProtectionStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/read | Kör frågor över data i arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Läsa data från tabellen ReservedAzureCommonFields |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Läsa data från tabellen ReservedCommonFields |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Läsa data från tabellen SCCMAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Läsa data från tabellen SCOMAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Läsa data från tabellen SecurityAlert |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Läsa data från tabellen SecurityBaseline |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Läsa data från tabellen SecurityBaselineSummary |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Läsa data från tabellen SecurityDetection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Läsa data från tabellen SecurityEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Läsa data från tabellen SecurityIoTRawEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Läsa data från tabellen SecurityRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Läsa data från tabellen ServiceFabricOperationalEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Läsa data från tabellen ServiceFabricReliableActorEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Läsa data från tabellen ServiceFabricReliableServiceEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Läsa data från tabellen SfBAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Läsa data från tabellen SfBOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Läsa data från tabellen SharePointOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Läsa data från tabellen SigninLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Läsa data från tabellen SPAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Läsa data från tabellen SQLAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Läsa data från tabellen SQLQueryPerformance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SqlThreatProtectionLoginAudits/read | Läsa data från tabellen SqlThreatProtectionLoginAudits |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Läsa data från tabellen SqlVulnerabilityAssessmentResult |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Läsa data från tabellen Syslog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Läsa data från tabellen SysmonEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Läsa data från någon annan anpassad logg |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Läsa data från tabellen ThreatIntelligenceIndicator |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Läsa data från tabellen UAApp |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Läsa data från tabellen UAComputer |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Läsa data från tabellen UAComputerRank |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Läsa data från tabellen UADriver |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Läsa data från tabellen UADriverProblemCodes |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Läsa data från tabellen UAFeedback |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Läsa data från tabellen UAHardwareSecurity |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Läsa data från tabellen UAIESiteDiscovery |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Läsa data från tabellen UAOfficeAddIn |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Läsa data från tabellen UAProposedActionPlan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Läsa data från tabellen UASysReqIssue |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Läsa data från tabellen UAUpgradedComputer |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Update/read | Läsa data från tabellen Update |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Läsa data från tabellen UpdateRunProgress |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Läsa data från tabellen UpdateSummary |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Usage/read | Läsa data från tabellen användning |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Läsa data från tabellen VMBoundPort |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Läsa data från tabellen VMConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Läsa data från tabellen W3CIISLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Läsa data från tabellen WaaSDeploymentStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Läsa data från tabellen WaaSInsiderStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Läsa data från tabellen WaaSUpdateStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Läsa data från tabellen WDAVStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Läsa data från tabellen WDAVThreat |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Läsa data från tabellen WindowsClientAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Läsa data från tabellen WindowsEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Läsa data från tabellen WindowsFirewall |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Läsa data från tabellen WindowsServerAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WireData/read | Läsa data från tabellen WireData |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Läsa data från tabellen WorkloadMonitoringPerf |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Läsa data från tabellen WUDOAggregatedStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Läsa data från tabellen WUDOStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbetsyta |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Återskapar den delade nyckeln för angiven arbetsyta |
> | Åtgärd | Microsoft.operationalinsights/Workspaces/rules/Read | Hämta alla aviseringsregler. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Tar bort en sparad sökfråga |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/read | Hämtar en sparad sökfråga |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/results/read | Hämta sparade sökningar resultat. Inaktuell |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Ta bort schemalagd sökåtgärder. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Få schemalagda sökåtgärder. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Skapa eller uppdatera schemalagda sökåtgärder. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Ta bort schemalagda sökningar. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Få schemalagda sökningar. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Skapa eller uppdatera schemalagda sökningar. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/write | Skapar en sparad sökfråga |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/schema/read | Hämtar sökschemat för arbetsytan.  Sökschemat innehåller de visade fälten och deras typer. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Åtgärd | microsoft.operationalinsights/workspaces/search/read | Hämta sökresultaten. Föråldrad. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbetsytan. De här nycklarna används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbetsytan. De här nycklarna används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Tar bort en lagringskonfiguration. Detta förhindrar Microsoft Operational Insights från att läsa data från storage-kontot. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Hämtar en lagringskonfiguration. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Skapar en ny lagringskonfiguration. De här konfigurationerna används för att hämta data från en plats i ett befintligt lagringskonto. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Hämta Search uppgradera Translation Fellogg för arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/usages/read | Hämtar användningsdata för en arbetsyta inklusive mängd data som läses av arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/write | Skapar en ny arbetsyta eller länkar till en befintlig arbetsyta genom att ange kund-id från den befintliga arbetsytan. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/delete | Ta bort befintlig Hanteringskoppling |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/read | Hämta befintlig Hanteringskoppling |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/write | Skapa ny Hanteringskoppling |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/delete | Ta bort befintlig Hanteringskonfiguration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/read | Hämta befintlig Hanteringskonfiguration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/write | Skapa ny Hanteringskonfiguration |
> | Åtgärd | Microsoft.OperationsManagement/register/action | Registrera en prenumeration på en resursprovider. |
> | Åtgärd | Microsoft.OperationsManagement/solutions/delete | Ta bort befintlig OMS-lösning |
> | Åtgärd | Microsoft.OperationsManagement/solutions/read | Hämta befintlig OMS-lösning |
> | Åtgärd | Microsoft.OperationsManagement/solutions/write | Skapa ny OMS-lösning |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PolicyInsights/asyncOperationResults/read | Hämtar det asynkrona resultat. |
> | Åtgärd | Microsoft.PolicyInsights/operations/read | Hämtar stöds åtgärder på Microsoft.PolicyInsights-namnområde |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/action | Fråga om information om Principhändelser. |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/read | Fråga om information om Principhändelser. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/action | Fråga om information om statusen för. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/read | Fråga om information om statusen för. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/action | Fråga översiktsinformation om statusen för senaste. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/read | Fråga översiktsinformation om statusen för senaste. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Utlöser en ny utvärdering av efterlevnad för det valda omfånget. |
> | Åtgärd | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Fråga om information om resurser som krävs av DeployIfNotExists principer. |
> | Åtgärd | Microsoft.PolicyInsights/register/action | Registrerar Microsoft Policy Insights-resursprovidern och gör att åtgärder på den. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/cancel/action | Avbryta pågående Microsofts Policy reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/delete | Ta bort princip reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/listDeployments/read | Visar en lista över distributioner som krävs av en princip för reparation. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/read | Hämta princip reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/write | Skapa eller uppdatera Microsofts Policy reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/unregister/action | Avregistrerar Microsoft Policy Insights-resursprovidern. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Portal/consoles/delete | Tar bort Cloud Shell-instans. |
> | Åtgärd | Microsoft.Portal/consoles/read | Läser Cloud Shell-instans. |
> | Åtgärd | Microsoft.Portal/consoles/write | Skapa eller uppdatera en instans av Cloud Shell. |
> | Åtgärd | Microsoft.Portal/dashboards/delete | Tar bort instrumentpanelen från prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/read | Läser in instrumentpanelerna för prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/write | Lägg till eller ändra en instrumentpanel för en prenumeration. |
> | Åtgärd | Microsoft.Portal/register/action | Registrera till portalen |
> | Åtgärd | Microsoft.Portal/usersettings/delete | Tar bort användarinställningar Cloud Shell. |
> | Åtgärd | Microsoft.Portal/usersettings/read | Läser Cloud Shell-användarinställningar. |
> | Åtgärd | Microsoft.Portal/usersettings/write | Skapa eller uppdatera användarinställningen för Cloud Shell. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/delete | Tar bort Powerbi dedikerade kapacitet. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/read | Hämtar information om den angivna Power BI dedikerade kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/resume/action | Återupptar kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/skus/read | Hämta tillgänglig SKU-information för kapaciteten |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/suspend/action | Pausar kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/write | Skapar eller uppdaterar den angivna Power BI dedikerade kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Kontrollerar att namnet angivna dedikerade Power BI kapaciteten är giltigt och inte används. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/operationresults/read | Hämtar information om det angivna åtgärdsresultatet. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Hämtar information om den angivna åtgärdsstatusen. |
> | Åtgärd | Microsoft.PowerBIDedicated/operations/read | Hämtar information om åtgärder |
> | Åtgärd | Microsoft.PowerBIDedicated/register/action | Registrerar Power BI-dedikerade resursprovidern. |
> | Åtgärd | Microsoft.PowerBIDedicated/skus/read | Hämtar information om SKU: er |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Åtgärd | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Åtgärd | microsoft.recoveryservices/Locations/backupProtectedItem/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Åtgärd | microsoft.recoveryservices/Locations/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Åtgärd | microsoft.recoveryservices/Locations/backupStatus/action | Kontrollera Säkerhetskopieringsstatus för Recovery Services-valv |
> | Åtgärd | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Validera funktioner |
> | Åtgärd | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Kontrollera Namntillgänglighet för resursen är en API för att kontrollera om resursnamnet är tillgänglig |
> | Åtgärd | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar Åtgärdsstatus för en viss åtgärd |
> | Åtgärd | Microsoft.RecoveryServices/operations/read | Åtgärden returnerar listan över åtgärder för en Resursprovider |
> | Åtgärd | Microsoft.RecoveryServices/register/action | Registrerar prenumerationen för den angivna Resursprovidern |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupconfig/read | Returnerar konfiguration för Recovery Services-valv. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupconfig/write | Uppdaterar konfiguration för Recovery Services-valv. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupEngines/read | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/delete | Ta bort en säkerhetskopia för Avsiktsskydd |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | Hämta en säkerhetskopia för Avsiktsskydd |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhetskopia för Avsiktsskydd |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | Hämta alla behållare som kan skyddas |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | Tar bort registrerad behållare |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör förfrågan om arbetsbelastningar inom en behållare |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Säkerhetskopierar ett skyddat objekt. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Tar bort skyddat objekt |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för utförd åtgärd på skyddade objekt. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar information om det skyddade objektet |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Tillhandahåll direkt Objektåterställning för skyddat objekt |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återskapa återställningspunkter för skyddade objekt. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla direkt Objektåterställning för skyddat objekt |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållarlistan |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupJobs/cancel/action | Avbryt jobbet |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupJobs/operationResults/read | Returnerar resultat från jobbåtgärd. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupJobsExport/action | Export-jobb |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupOperationResults/read | Returnerar resultat från säkerhetskopiering för Recovery Services-valvet. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupOperations/read | Returnerar Säkerhetskopieringsstatus för Recovery Services-valv. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupPolicies/delete | Tar bort en Skyddsprincip |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupPolicies/operations/read | Hämta Status för principåtgärd. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupPolicies/write | Skapar en Skyddsprincip |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupProtectableItems/read | Returnerar lista över alla objekt som ska skyddas. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupProtectionIntents/read | Lista över alla backup Protection avsikter |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupSecurityPIN/action | Returnerar säkerhetskod Information för Recovery Services-valv. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupstorageconfig/read | Returnerar lagringskonfiguration för Recovery Services-valv. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupstorageconfig/write | Uppdaterar lagringskonfiguration för Recovery Services-valv. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Returnerar sammanfattning av skyddade objekt och skyddade servrar för Recovery Services. |
> | Åtgärd | microsoft.recoveryservices/Vaults/backupValidateOperation/action | Validera åtgärden på skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/certificates/write | Åtgärden Uppdatera Resurscertifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/delete | Ta bort valv raderas den angivna Azure-resursen av typen ”vault' |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löser aviseringen. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Hämtar meddelandekonfigurationen för Recovery services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfigurerar e-postaviseringar till Recovery services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Åtgärden avregistrera tjänstbehållare kan användas för att avregistrera en behållare. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta Åtgärdsresultat åtgärd kan du hämta åtgärdsstatusen och resultatet för åtgärden som skickats asynkront |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållare kan du använda åtgärden hämta behållare som är registrerade för en resurs. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Åtgärden registrera tjänstbehållare kan användas för att registrera en behållare med återställningstjänsten. |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa alla aviseringsinställningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Skapa eller uppdatera eventuella aviseringsinställningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar infrastrukturens enhetlighet |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Ta bort alla Infrastrukturresurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Distribuera Processerveravbildning |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrera Fabric till AAD |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läsa alla infrastrukturer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skapa en ny koppling Gateway |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Ta bort infrastruktur |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Läs några logiska nätverk |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Ta bort alla nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Skapa eller uppdatera eventuella nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Identifiera objekt som kan skyddas |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läsa alla skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Ta bort skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Ta bort alla objekt för migrering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrera objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Läsa alla återställningspunkter för migrering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Läsa alla objekt för migrering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Test migrera |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Test migrera rensning |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Skapa eller uppdatera alla objekt för migrering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läsa alla objekt som ska skyddas |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lägg till diskar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Använda återställningspunkt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Ta bort alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Redundansåtgärd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa alla återställningspunkter för replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Ta bort skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Ta bort diskar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Skicka Feedback |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Läsa alla storlekar på mål |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Redundanstest |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdateringen av Mobilitetstjänsten |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Skapa eller uppdatera alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Ta bort alla mappningar av skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Ta bort mappning av skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Skapa eller uppdatera alla mappningar av skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Växla skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Skapa eller uppdatera eventuella skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Ta bort alla Recovery Services-leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läsa alla Recovery Services-leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera Provider |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Ta bort Recovery Services-Provider |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Skapa eller uppdatera eventuella Recovery Services-leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läsa alla Lagringsklassificeringar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Ta bort alla mappningar av Lagringsklassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läsa alla mappningar av Lagringsklassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Skapa eller uppdatera alla mappningar av Lagringsklassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ta bort alla vCenters |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läsa alla vCenters |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Skapa eller uppdatera eventuella vCenters |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Skapa eller uppdatera eventuella Infrastrukturresurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Avbryt jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/read | Läsa alla jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Starta om jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Återuppta jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Läsa alla objekt för migrering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Läsa alla nätverk |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Ta bort alla principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läsa alla principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Skapa eller uppdatera eventuella principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Läsa alla skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Ta bort alla Återställningsplaner |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Återställningsplan för redundansåtgärd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Återställningsplan för planerad redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läsa alla Återställningsplaner |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Återskydd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Redundansåterställning testplan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Återställningsplan för test av Redundanstest |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställningsplan för redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Skapa eller uppdatera eventuella Återställningsplaner |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Läsa alla Recovery Services-leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Läsa alla mappningar av Lagringsklassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Läsa alla Lagringsklassificeringar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Läsa  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationUsages/read | Läsa alla Valvanvändning för replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Läsa alla Valvhälsa för replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Uppdatera Valvhälsa |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Läsa  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Skapa eller uppdatera alla  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Läsa alla vCenters |
> | Åtgärd | microsoft.recoveryservices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/vaults/usages/read | Läsa alla Valvanvändning |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valvtokenåtgärden kan användas för att hämta Valvtoken för serverdelsåtgärder på valvnivå. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/write | Med skapa valv så skapas en Azure-resurs av typen valv |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Relay/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.Relay/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell Använd CheckNameAvailability istället. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/action | Uppdateringar Namespace-auktoriseringsregeln. Detta API är inaktuell. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace istället... Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till Namespace |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/read | Hämta listan över auktoriseringsregler för namnområden beskrivning. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/Delete | Ta bort Namespace-resurs |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för Haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för Disaster Recovery primära Namespaces |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar Haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden under en viss prenumeration. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Tar bort den haveriberedskapskonfiguration som är kopplat till namnområdet. Den här åtgärden kan bara anropas via det primära namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och Omkonfigurerar namnområdesalias så att den pekar på det sekundära namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Åtgärd för att uppdatera Hybridanslutning. Den här åtgärden stöds inte på API-versionen 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för Hybridanslutning |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Hämta anslutningssträngen till Hybridanslutning |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Hämta listan över auktoriseringsregler för Hybridanslutning |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Skapa auktoriseringsregler för Hybridanslutning och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/Delete | Åtgärd för att ta bort Hybridanslutning resurs |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/read | Hämta listan över resursbeskrivningar för Hybridanslutning |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/write | Skapa eller uppdatera Hybridanslutning egenskaper. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/read | Hämtar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/write | Uppdaterar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/operationresults/read | Hämta status för Namespace åtgärden |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/read | Hämta listan över Namespace beskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Åtgärd för att uppdatera WcfRelay. Den här åtgärden stöds inte på API-versionen 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för WcfRelay |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Hämta anslutningssträngen till WcfRelay |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Hämta listan över auktoriseringsregler för WcfRelay |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Skapa auktoriseringsregler för WcfRelay och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/Delete | Åtgärd för att ta bort WcfRelay resurs |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/read | Hämta lista över WcfRelay resursbeskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/write | Skapa eller uppdatera WcfRelay egenskaper. |
> | Åtgärd | Microsoft.Relay/namespaces/write | Skapa en Namespace-resurs och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.Relay/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.Relay/register/action | Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser |
> | Åtgärd | Microsoft.Relay/unregister/action | Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Hämtar tillgänglighetsstatusen för den angivna resursen |
> | Åtgärd | Microsoft.ResourceHealth/AvailabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Åtgärd | Microsoft.ResourceHealth/events/read | Hämta Hälsotillståndshändelser för tjänsten för den angivna prenumerationen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Activated/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/InProgress/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Pending/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Resolved/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Updated/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.ResourceHealth/impactedResources/read | Hämta resurser som påverkas för angiven prenumeration |
> | Åtgärd | Microsoft.ResourceHealth/metadata/read | Gets Metadata |
> | Åtgärd | Microsoft.ResourceHealth/Operations/read | Hämta åtgärderna som är tillgängliga för Microsoft ResourceHealth |
> | Åtgärd | Microsoft.ResourceHealth/register/action | Registrerar prenumerationen för Microsoft ResourceHealth |
> | Åtgärd | Microsoft.ResourceHealth/unregister/action | Avregistrerar prenumerationen för Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Resources/checkPolicyCompliance/action | Kontrollera kompatibilitetsstatusen för en viss resurs mot resursprinciper. |
> | Åtgärd | Microsoft.Resources/checkResourceName/action | Kontrollera resursnamnets giltighet. |
> | Åtgärd | Microsoft.Resources/deployments/cancel/action | Avbryter en distribution. |
> | Åtgärd | Microsoft.Resources/deployments/delete | Tar bort en distribution. |
> | Åtgärd | Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Åtgärd | Microsoft.Resources/deployments/read | Hämtar eller listar distributioner. |
> | Åtgärd | Microsoft.Resources/deployments/validate/action | Verifierar en distribution. |
> | Åtgärd | Microsoft.Resources/deployments/whatIf/action | Förutsäger malländringar för distribution. |
> | Åtgärd | Microsoft.Resources/deployments/write | Skapar eller uppdaterar en distribution. |
> | Åtgärd | Microsoft.Resources/links/delete | Tar bort en resurslänk. |
> | Åtgärd | Microsoft.Resources/links/read | Hämtar eller listar resurslänkar. |
> | Åtgärd | Microsoft.Resources/links/write | Skapar eller uppdaterar en resurslänk. |
> | Åtgärd | Microsoft.Resources/marketplace/purchase/action | Köp en resurs från marketplace. |
> | Åtgärd | Microsoft.Resources/providers/read | Hämta listan med providers. |
> | Åtgärd | Microsoft.Resources/resources/read | Hämta listan över resurser baserade på filter. |
> | Åtgärd | Microsoft.Resources/subscriptions/locations/read | Hämtar listan över platser som stöds. |
> | Åtgärd | Microsoft.Resources/subscriptions/operationresults/read | Hämta Åtgärdsresultat för prenumerationen. |
> | Åtgärd | Microsoft.Resources/subscriptions/providers/read | Hämtar eller listar resursprovidrar. |
> | Åtgärd | Microsoft.Resources/subscriptions/read | Hämtar listan över prenumerationer. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/delete | Tar bort en resursgrupp och alla dess resurser. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Hämtar eller listar distributionsåtgärder. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Hämtar eller listar distributioner. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Skapar eller uppdaterar en distribution. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Flyttar resurser från en resursgrupp till en annan. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Hämtar resurserna för resursgruppen. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Verifiera flytt av resurser från en resursgrupp till en annan. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/write | Skapar eller uppdaterar en resursgrupp. |
> | Åtgärd | Microsoft.Resources/subscriptions/resources/read | Hämtar en prenumerations resurser. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/delete | Tar bort en prenumerationstagg. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/read | Hämtar eller listar prenumerationstaggar. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Tar bort ett prenumerationstaggsvärde. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Hämtar eller listar prenumerationstaggsvärden. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Lägger till ett prenumerationstaggsvärde. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/write | Lägger till en prenumerationstagg. |
> | Åtgärd | Microsoft.Resources/tags/delete | Tar bort alla taggar på en resurs. |
> | Åtgärd | Microsoft.Resources/tags/read | Hämtar alla taggar på en resurs. |
> | Åtgärd | Microsoft.Resources/tags/write | Uppdaterar taggar på en resurs genom att ersätta eller sammanfoga befintliga taggar med en ny uppsättning taggar, ta bort befintliga taggar. |
> | Åtgärd | Microsoft.Resources/tenants/read | Hämtar listan över klienter. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Scheduler/jobcollections/delete | Tar bort en jobbsamling. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/disable/action | Inaktiverar en jobbsamling. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/enable/action | Aktiverar en jobbsamling. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/delete | Tar bort ett jobb. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Genererar en logikappsdefinition som baseras på ett Scheduler-jobb. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Hämtar jobbhistorik. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/read | Hämtar jobb. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/run/action | Kör jobb. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/write | Skapar eller uppdaterar jobb. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/read | Hämta Jobbsamling |
> | Åtgärd | Microsoft.Scheduler/jobcollections/write | Skapar eller uppdaterar en jobbsamling. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Search/checkNameAvailability/action | Kontrollerar tillgängligheten för namnet på tjänsten. |
> | Åtgärd | Microsoft.Search/operations/read | Visar en lista över alla tillgängliga åtgärder av Microsoft.Search-providern. |
> | Åtgärd | Microsoft.Search/register/action | Registrerar prenumerationen för search-resursprovidern och gör möljligt search-tjänsterna. |
> | Åtgärd | Microsoft.Search/searchServices/createQueryKey/action | Skapar Frågenyckeln. |
> | Åtgärd | Microsoft.Search/searchServices/delete | Tar bort söktjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/deleteQueryKey/delete | Tar bort Frågenyckeln. |
> | Åtgärd | Microsoft.Search/searchServices/listAdminKeys/action | Läser administatörsnycklarna. |
> | Åtgärd | Microsoft.Search/searchServices/listQueryKeys/read | Returnerar listan över fråge-API-nycklar för den angivna Azure Search-tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/read | Läser söktjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/regenerateAdminKey/action | Återskapar admin-nyckel. |
> | Åtgärd | Microsoft.Search/searchServices/start/action | Startar söktjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/stop/action | Stoppar söktjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/write | Skapar eller uppdaterar söktjänsten. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Tillämpar viss trafik härdning regler genom att skapa matchande säkerhetsregler på den angivna Network Security grupperna |
> | Åtgärd | Microsoft.Security/adaptiveNetworkHardenings/read | Hämtar anpassningsbar Härdning av nätverket rekommendationer av en Azure-skyddade resursen |
> | Åtgärd | Microsoft.Security/advancedThreatProtectionSettings/read | Hämtar Advanced Threat Protection inställningar för resursen |
> | Åtgärd | Microsoft.Security/advancedThreatProtectionSettings/write | Uppdaterar Advanced Threat Protection inställningarna för resursen |
> | Åtgärd | Microsoft.Security/alerts/read | Hämtar alla tillgängliga säkerhetsaviseringar |
> | Åtgärd | Microsoft.Security/applicationWhitelistings/read | Hämtar programmet whitelistings |
> | Åtgärd | Microsoft.Security/applicationWhitelistings/write | Skapar en ny vitlistning av program eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/complianceResults/read | Hämtar kompatibilitetsresultaten för resursen |
> | Åtgärd | Microsoft.Security/informationProtectionPolicies/read | Hämtar information protection-principer för resursen |
> | Åtgärd | Microsoft.Security/informationProtectionPolicies/write | Uppdaterar informationsskyddsprinciper för resursen |
> | Åtgärd | Microsoft.Security/locations/alerts/activate/action | Aktivera en säkerhetsvarning |
> | Åtgärd | Microsoft.Security/locations/alerts/dismiss/action | Avvisa en säkerhetsavisering |
> | Åtgärd | Microsoft.Security/locations/alerts/read | Hämtar alla tillgängliga säkerhetsaviseringar |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Tar bort just-in-time-åtkomst nätverksprincip |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initierar begäran om principen en just-in-time-nätverksåtkomst |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Hämtar just-in-time-åtkomst nätverksprinciper |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Skapar en ny åtkomstprincip för just-in-time-nätverk eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/locations/read | Hämtar Dataplats säkerhet |
> | Åtgärd | Microsoft.Security/locations/tasks/activate/action | Aktivera en säkerhetsrekommendation |
> | Åtgärd | Microsoft.Security/locations/tasks/dismiss/action | Stänga en säkerhetsrekommendation |
> | Åtgärd | Microsoft.Security/locations/tasks/read | Hämtar alla tillgängliga säkerhetsrekommendationer |
> | Åtgärd | Microsoft.Security/locations/tasks/resolve/action | Lösa en säkerhetsrekommendation |
> | Åtgärd | Microsoft.Security/locations/tasks/start/action | Starta en säkerhetsrekommendation |
> | Åtgärd | Microsoft.Security/policies/read | Hämtar säkerhetsprincipen |
> | Åtgärd | Microsoft.Security/policies/write | Uppdaterar säkerhetsprincipen |
> | Åtgärd | Microsoft.Security/pricings/delete | Tar bort inställningarna för prissättning för detta scope |
> | Åtgärd | Microsoft.Security/pricings/read | Hämtar inställningarna för prissättning för detta scope |
> | Åtgärd | Microsoft.Security/pricings/write | Uppdaterar inställningarna för prissättning för detta scope |
> | Åtgärd | Microsoft.Security/register/action | Registrerar prenumerationen för Azure Security Center |
> | Åtgärd | Microsoft.Security/securityContacts/delete | Tar bort säkerhetskontakt |
> | Åtgärd | Microsoft.Security/securityContacts/read | Hämtar säkerhetskontakt |
> | Åtgärd | Microsoft.Security/securityContacts/write | Uppdaterar säkerhetskontakt |
> | Åtgärd | Microsoft.Security/securitySolutions/delete | Tar bort en säkerhetslösning |
> | Åtgärd | Microsoft.Security/securitySolutions/read | Hämtar säkerhetslösningarna |
> | Åtgärd | Microsoft.Security/securitySolutions/write | Skapar en ny säkerhetslösning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/securitySolutionsReferenceData/read | Hämtar säkerhetslösningarna referensdata |
> | Åtgärd | Microsoft.Security/securityStatuses/read | Hämtar säkerheten hälsotillstånd status för Azure-resurser |
> | Åtgärd | Microsoft.Security/securityStatusesSummaries/read | Hämtar säkerheten statusar sammanfattningar för detta scope |
> | Åtgärd | Microsoft.Security/settings/read | Hämtar inställningarna för detta scope |
> | Åtgärd | Microsoft.Security/settings/write | Uppdaterar inställningarna för detta scope |
> | Åtgärd | Microsoft.Security/tasks/read | Hämtar alla tillgängliga säkerhetsrekommendationer |
> | Åtgärd | Microsoft.Security/unregister/action | Avregistrerar prenumeration från Azure Security Center |
> | Åtgärd | Microsoft.Security/webApplicationFirewalls/delete | Tar bort en brandvägg för webbaserade program |
> | Åtgärd | Microsoft.Security/webApplicationFirewalls/read | Hämtar webben brandväggar för webbprogram |
> | Åtgärd | Microsoft.Security/webApplicationFirewalls/write | Skapar en ny Brandvägg för webbaserade program eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/workspaceSettings/connect/action | Ändra arbetsytan inställningar återanslutning inställningar |
> | Åtgärd | Microsoft.Security/workspaceSettings/delete | Tar bort inställningar för arbetsyta |
> | Åtgärd | Microsoft.Security/workspaceSettings/read | Hämtar inställningar för arbetsyta |
> | Åtgärd | Microsoft.Security/workspaceSettings/write | Uppdaterar inställningar för arbetsyta |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/delete | Tar bort en diagnostikinställning |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/read | Läser en diagnostikinställning |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/write | Skriva en diagnostikinställning |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Läser en diagnostikinställning kategorier |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ServiceBus/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.ServiceBus/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell Använd CheckNameAvailability istället. |
> | Åtgärd | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Tar bort VNet-regler i ServiceBus-Resursprovidern för det angivna virtuella nätverket |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/action | Uppdateringar Namespace-auktoriseringsregeln. Detta API är inaktuell. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace istället... Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till Namespace |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/read | Hämta listan över auktoriseringsregler för namnområden beskrivning. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/Delete | Ta bort Namespace-resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för Haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för Disaster Recovery primära Namespaces |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar Haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden under en viss prenumeration. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Tar bort den haveriberedskapskonfiguration som är kopplat till namnområdet. Den här åtgärden kan bara anropas via det primära namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och Omkonfigurerar namnområdesalias så att den pekar på det sekundära namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Tar bort Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Hämtar Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Skapar eller uppdaterar Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventhubs/read | Hämta listan över resursbeskrivningar för EventHub |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Ta bort IP-Filter resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Hämta IP-Filter |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Skapa resurs för IP-Filter |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/browse/action | Bläddra meddelnden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/defer/action | Skjut upp meddelanden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Ta emot meddelanden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Shedule meddelanden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Skicka meddelanden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/setstate/action | Set-sessionstillstånd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hämtar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/write | Uppdaterar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrate/action | Migreringsåtgärd för namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Tar bort konfigurationen för migrering. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Hämtar konfigurationen för migrering som anger status för migreringen och väntar på replikeringsåtgärder |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Återställer standard till premium-namnområdesmigreringen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Tilldelar DNS som är associerade med standard-namnområdet till premium-namnområde som Slutför migreringen och stoppar synkronisering resurserna från standard till premium-namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Skapar eller konfigurationen för migrering av uppdateringar. Detta startar synkronisering av resurser från standard till premium-namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Ta bort VNET-regel-resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/read | Hämtar NetworkRuleSet resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/write | Skapa resurs för VNET-regel |
> | Åtgärd | Microsoft.ServiceBus/namespaces/operationresults/read | Hämta status för Namespace åtgärden |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta listan över resursbeskrivningar för Namespace diagnostikinställningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta listan över resursbeskrivningar för Namespace diagnostikinställningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta lista över Namespace loggar resursbeskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Åtgärd för att uppdatera kön. Den här åtgärden stöds inte på API-versionen 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för kö |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Hämta anslutningssträngen till kö |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Hämta listan över auktoriseringsregler för kö |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Skapa auktoriseringsregler för kön och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/Delete | Åtgärden att ta bort resursen i kön |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/read | Hämta listan över resursbeskrivningar för kö |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/write | Skapa eller uppdatera köegenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/read | Hämta listan över Namespace beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Åtgärd för att uppdatera avsnittet. Den här åtgärden stöds inte på API-versionen 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för ämnet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Hämta anslutningssträngen till ämnet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Hämta listan över auktoriseringsregler för ämnet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Skapa auktoriseringsregler för ämnet och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/Delete | Åtgärd för att ta bort avsnittet resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/read | Hämta lista över avsnittet resursbeskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Åtgärd för att ta bort TopicSubscription resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Hämta lista över TopicSubscription resursbeskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Åtgärd för att ta bort regeln resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Hämta listan över resursbeskrivningar för regeln |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Skapa eller uppdatera regelegenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Skapa eller uppdatera TopicSubscription egenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/write | Skapa eller uppdatera avsnittet Egenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Ta bort VNET-regel-resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Hämtar Regelresursen för virtuellt nätverk |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Skapa resurs för VNET-regel |
> | Åtgärd | Microsoft.ServiceBus/namespaces/write | Skapa en Namespace-resurs och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.ServiceBus/register/action | Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser |
> | Åtgärd | Microsoft.ServiceBus/sku/read | Hämta lista över SKU: N resursbeskrivningar |
> | Åtgärd | Microsoft.ServiceBus/sku/regions/read | Hämta lista över SkuRegions resursbeskrivningar |
> | Åtgärd | Microsoft.ServiceBus/unregister/action | Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/delete | Ta bort valfritt program |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/read | Läs valfritt program |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/delete | Ta bort valfri tjänst |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Läs valfri Partition |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Läs valfri replik |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/read | Läs valfri tjänst |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Läs valfri Tjänststatus |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/write | Skapa eller uppdatera valfri tjänst |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/write | Skapa eller uppdatera valfritt program |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Ta bort valfri programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/read | Läs valfri programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Ta bort valfri Version av programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Läs valfri Version av programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Skapa eller uppdatera valfri Version av programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/write | Skapa eller uppdatera valfri programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/delete | Ta bort valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/clusters/nodes/read | Läs valfri nod |
> | Åtgärd | Microsoft.ServiceFabric/clusters/read | Läs valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/clusters/statuses/read | Läs valfri Klusterstatus |
> | Åtgärd | Microsoft.ServiceFabric/clusters/write | Skapa eller uppdatera valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/locations/clusterVersions/read | Läs valfri Klusterversion |
> | Åtgärd | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Läs valfri Klusterversion för en viss miljö |
> | Åtgärd | Microsoft.ServiceFabric/locations/operationresults/read | Läs valfritt Åtgärdsresultat |
> | Åtgärd | Microsoft.ServiceFabric/locations/operations/read | Läs valfri åtgärd efter plats |
> | Åtgärd | Microsoft.ServiceFabric/operations/read | Läs valfri tillgänglig åtgärd |
> | Åtgärd | Microsoft.ServiceFabric/register/action | Registrera valfri åtgärd |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.SignalRService/locations/checknameavailability/action | Kontrollerar om ett namn är tillgängliga för användning med en ny SignalR-tjänst |
> | Åtgärd | Microsoft.SignalRService/locations/operationresults/signalr/read | Fråga efter statusen för en asynkron åtgärd |
> | Åtgärd | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Fråga efter statusen för en asynkron åtgärd |
> | Åtgärd | Microsoft.SignalRService/locations/usages/read | Hämta kvot användningar för Azure SignalR service |
> | Åtgärd | Microsoft.SignalRService/operationresults/read | Fråga efter statusen för en asynkron åtgärd |
> | Åtgärd | Microsoft.SignalRService/operationstatus/read | Fråga efter statusen för en asynkron åtgärd |
> | Åtgärd | Microsoft.SignalRService/register/action | Registrerar 'Microsoft.SignalRService'-resursprovidern med en prenumeration |
> | Åtgärd | Microsoft.SignalRService/SignalR/delete | Ta bort hela SignalR-tjänsten |
> | Åtgärd | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Ta bort en event grid-filtret från en SignalR. |
> | Åtgärd | Microsoft.SignalRService/SignalR/eventGridFilters/read | Hämta egenskaperna för den angivna event grid-filtret eller listor event grid filter för den angivna SignalR. |
> | Åtgärd | Microsoft.SignalRService/SignalR/eventGridFilters/write | Skapa eller uppdatera en event grid-filtret för en SignalR med de angivna parametrarna. |
> | Åtgärd | Microsoft.SignalRService/SignalR/listkeys/action | Visa värdet för åtkomstnycklarna för SignalR i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/SignalR/read | Visa SignalR inställningar och konfigurationer i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/SignalR/regeneratekey/action | Ändra värdet för SignalR åtkomstnycklar i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/SignalR/restart/action | Starta om en Azure SignalR service i hanteringsportalen eller via API: et. Det kommer att vissa avbrott. |
> | Åtgärd | Microsoft.SignalRService/SignalR/write | Ändra SignalR inställningar och konfigurationer i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/unregister/action | Avregistrerar 'Microsoft.SignalRService'-resursprovidern med en prenumeration |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Visar en lista över programartefakter av programdefinitionen. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/delete | Tar bort en programdefinition. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/read | Hämtar en lista över programdefinitioner. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/write | Lägg till eller ändra en programdefinition. |
> | Åtgärd | Microsoft.Solutions/applications/applicationArtifacts/read | Visar en lista över programartefakter. |
> | Åtgärd | Microsoft.Solutions/applications/delete | Tar bort ett program. |
> | Åtgärd | Microsoft.Solutions/applications/read | Hämtar en lista över program. |
> | Åtgärd | Microsoft.Solutions/applications/refreshPermissions/action | Uppdaterar programbehörigheterna. |
> | Åtgärd | Microsoft.Solutions/applications/updateAccess/action | Uppdaterar programåtkomst. |
> | Åtgärd | Microsoft.Solutions/applications/write | Skapar ett program. |
> | Åtgärd | Microsoft.Solutions/jitRequests/delete | Ta bort en JitRequest |
> | Åtgärd | Microsoft.Solutions/jitRequests/read | Hämtar en lista över JitRequests |
> | Åtgärd | Microsoft.Solutions/jitRequests/write | Skapar en JitRequest |
> | Åtgärd | Microsoft.Solutions/locations/operationStatuses/read | Läser Åtgärdsstatus för resursen. |
> | Åtgärd | Microsoft.Solutions/register/action | Registrera dig för lösningar. |
> | Åtgärd | Microsoft.Solutions/unregister/action | Avregistrerar från lösningar. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Sql/checkNameAvailability/action | Kontrollera om namn angivna servern är tillgänglig för etablering i hela världen för en viss prenumeration. |
> | Åtgärd | Microsoft.Sql/instancePools/delete | Tar bort en instans-pool |
> | Åtgärd | Microsoft.Sql/instancePools/read | Hämtar en instans-pool |
> | Åtgärd | Microsoft.Sql/instancePools/usages/read | Hämtar en instans pool användningsinformation |
> | Åtgärd | Microsoft.Sql/instancePools/write | Skapar eller uppdaterar en instans-pool |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Hämta resultatet av server-blob auditing princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/capabilities/read | Hämtar funktionerna för den här prenumerationen i en viss plats |
> | Åtgärd | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/databaseOperationResults/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Hämtar pågående åtgärder på servern tagits bort |
> | Åtgärd | Microsoft.Sql/locations/deletedServerOperationResults/read | Hämtar pågående åtgärder på servern tagits bort |
> | Åtgärd | Microsoft.Sql/locations/deletedServers/read | Returnera listan över borttagna servrar eller hämtar egenskaperna för den angivna servern tagits bort. |
> | Åtgärd | Microsoft.Sql/locations/deletedServers/recover/action | Återställa en borttagen server |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Hämtar azure async-åtgärden för en elastisk pool asynkron åtgärd |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolOperationResults/read | Hämtar resultat från en elastisk pool-åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Hämtar pågående åtgärder på transparent data encryption krypteringsskyddet |
> | Åtgärd | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Hämtar pågående åtgärder på transparent data encryption krypteringsskyddet |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Hämtar status för en brandvägg Regelåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesOperationResults/read | Hämtar status för en brandvägg Regelåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/delete | Tar bort en befintlig instans en redundansgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Kör planerad redundans i en befintlig instans en redundansgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kör forcerad redundans i en befintlig instans en redundansgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/read | Returnerar listan över instans redundans grupper eller hämtar egenskaperna för den angivna instansen redundansgruppen. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/write | Skapar en redundansgrupp för instansen med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna instansen redundansgruppen. |
> | Åtgärd | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Hämtar status för en åtgärd för instans-pool |
> | Åtgärd | Microsoft.Sql/locations/instancePoolOperationResults/read | Hämtar resultaten för en åtgärd för instans-pool |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Returnerar information om ett visst gränssnitt endpoint Azure en asynkron åtgärd |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Returnerar information om den angivna gränssnittet endpoint profil åtgärden |
> | Åtgärd | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Hämtar status för ett jobb agentåtgärden. |
> | Åtgärd | Microsoft.Sql/locations/jobAgentOperationResults/read | Hämtar resultat från en agentåtgärden för jobbet. |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionBackups/read | Visar en lista över säkerhetskopior för långsiktig kvarhållning för alla databaser på varje server i en plats |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Visar en lista över säkerhetskopior för långsiktig kvarhållning för varje databas på en server |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Tar bort en långsiktig kvarhållning av säkerhetskopia |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Visar en lista över säkerhetskopior för långsiktig kvarhållning för en databas |
> | Åtgärd | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Slutför hanterad databasåterställning |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Hämtar pågående åtgärder på krypteringsskyddet för transparent data encryption hanterad instans |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Hämtar pågående åtgärder på krypteringsskyddet för transparent data encryption hanterad instans |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Hämtar pågående åtgärder på transparent datakryptering hanterade instans nycklar |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Hämtar pågående åtgärder på transparent datakryptering hanterade instans nycklar |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hämtar pågående åtgärder på hanterade databasen transparent datakryptering |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Hämtar pågående åtgärder på hanterade databasen transparent datakryptering |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Hämtar resultaten för en åtgärd för anslutning av privat slutpunkt |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Hämtar resultaten för en åtgärd för anslutning av privat slutpunkt |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionProxyAzureAsyncOperation/read | Hämtar resultaten för en privat slutpunkt anslutning proxy-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Hämtar resultaten för en privat slutpunkt anslutning proxy-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/read | Hämtar tillgängliga platser för en viss prenumeration |
> | Åtgärd | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Hämtar pågående åtgärder på transparent datakrypteringsnycklar för servern |
> | Åtgärd | Microsoft.Sql/locations/serverKeyOperationResults/read | Hämtar pågående åtgärder på transparent datakrypteringsnycklar för servern |
> | Åtgärd | Microsoft.Sql/locations/syncAgentOperationResults/read | Hämta resultatet av agenten synkroniseringsåtgärden för resursen |
> | Åtgärd | Microsoft.Sql/locations/syncDatabaseIds/read | Hämta ID: n för sync-databasen för en viss region och prenumeration |
> | Åtgärd | Microsoft.Sql/locations/syncGroupOperationResults/read | Hämta resultatet av åtgärden för synkronisering av resursen |
> | Åtgärd | Microsoft.Sql/locations/syncMemberOperationResults/read | Hämta resultatet av medlem synkroniseringsåtgärden för resursen |
> | Åtgärd | Microsoft.Sql/locations/usages/read | Hämtar en samling med användningsstatistik för den här prenumerationen på en plats |
> | Åtgärd | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Returnerar information om de angivna virtuella nätverksreglerna azure en asynkron åtgärd  |
> | Åtgärd | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Returnerar information om den angivna virtuella nätverket regler åtgärden  |
> | Åtgärd | Microsoft.Sql/managedInstances/administrators/delete | Tar bort en befintlig administratör för hanterad instans. |
> | Åtgärd | Microsoft.Sql/managedInstances/administrators/read | Hämtar en lista över hanterade instansen administratörer. |
> | Åtgärd | Microsoft.Sql/managedInstances/administrators/write | Skapar eller uppdaterar hanterad instans-administratör med de angivna parametrarna. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Hämtar en kort sikt bevarandeprincip för en hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kort sikt bevarandeprincip för en hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Batch update känslighet etiketter |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/delete | Tar bort en befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instansdatabaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/read | Hämtar befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/read | Hämta en hanterad databasschemat. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Hämta en hanterad databas-kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort etiketten känslighet för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Hämta en hanterad databas-tabell |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Hämta en lista med hanterad databas threat identifiering av principerna som konfigureras för en viss server |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Ändra den database threat principen för en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityEvents/read | Hämtar säkerhetshändelser hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Hämta information om databasen Transparent datakryptering på en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Ändra databasen Transparent datakryptering för en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/delete | Ta bort utvärdering av säkerhetsrisker för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Hämta vulnerability assessment-principer på en givendatabase |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Ta bort vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Hämta vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/write | Ändra vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Konvertera en befintlig genomsökningsresultatet till ett format. Om det finns inget redan händer |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Köra genomsökning för sårbarhetsbedömning databas. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Returnerar listan över databasen vulnerability assessment genomsökning poster eller hämta genomsökning posten för den angivna genomsökning-ID. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Ändra sårbarhetsbedömning för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/write | Skapar en ny databas eller uppdaterar en befintlig databas. |
> | Åtgärd | Microsoft.Sql/managedInstances/delete | Tar bort en befintlig hanterad instans. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/read | Returnerar en lista över server kryptering protectors eller hämtar egenskaperna för den angivna servern krypteringsskyddet. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/write | Uppdatera egenskaperna för den angivna Serverkrypteringsskydd. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/delete | Tar bort en befintlig Azure SQL Managed Instance-nyckel. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/read | Returnera listan över hanterade instansen nycklar eller hämtar egenskaperna för angivna hanterad instans-nyckeln. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för angivna hanterad instans-nyckeln. |
> | Åtgärd | Microsoft.Sql/managedInstances/metricDefinitions/read | Hämta måttdefinitioner för den hanterade instansen |
> | Åtgärd | Microsoft.Sql/managedInstances/metrics/read | Hämta mått för hanterad instans |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instanser |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för hanterade instanser |
> | Åtgärd | Microsoft.Sql/managedInstances/read | Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hantera instansen. |
> | Åtgärd | Microsoft.Sql/managedInstances/recoverableDatabases/read | Returnerar en lista över återställningsbara hanterade databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Hämtar en kort sikt bevarandeprincip för en förlorad hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Uppdaterar en kort sikt bevarandeprincip för en förlorad hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Returnerar en lista över återställningsbara bort hanterade databaser. |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Hämta en lista över hanterade servern threat identifiering av principerna som konfigureras för en viss server |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Ändra den hanterade server threat principen för en viss hanterad server |
> | Åtgärd | Microsoft.Sql/managedInstances/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Åtgärd | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Ta bort utvärdering av säkerhetsrisker för en viss hanterad instans |
> | Åtgärd | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Hämta vulnerability assessment-principer på en viss hanterad instans |
> | Åtgärd | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Ändra sårbarhetsbedömning för en viss hanterad instans |
> | Åtgärd | Microsoft.Sql/managedInstances/write | Skapar en hanterad instans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hantera instansen. |
> | Åtgärd | Microsoft.Sql/operations/read | Hämtar tillgängliga REST-åtgärder |
> | Åtgärd | Microsoft.Sql/register/action | Registrerar prenumerationen för Microsoft SQL Database-resursprovidern och gör det möjligt att skapa Microsoft SQL-databaser. |
> | Åtgärd | Microsoft.Sql/servers/administratorOperationResults/read | Hämtar pågående åtgärder på server-administratörer |
> | Åtgärd | Microsoft.Sql/servers/administrators/delete | Ta bort serveradministratör |
> | Åtgärd | Microsoft.Sql/servers/administrators/read | Hämta serverinformation för administratör |
> | Åtgärd | Microsoft.Sql/servers/administrators/write | Skapa eller uppdatera serveradministratör |
> | Åtgärd | Microsoft.Sql/servers/advisors/read | Returnerar lista över rådgivare som är tillgängliga för servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angivna advisor för servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/write | Uppdateringar ör-status för en klassificering på servernivå. |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/read | Hämta information om standard-server-tabell granskningsprincip som konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/write | Ändra standard server tabellgranskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/operationResults/read | Hämta resultatet av server-blob auditing princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/read | Hämta information om server blob granskningsprincipen konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/write | Ändra blobgranskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/read | Returnerar automatiska justeringsinställningar för servern |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/write | Uppdaterar inställningar för automatisk justering för servern och returnerar uppdaterade inställningarna |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Tar bort ett befintligt arkivering säkerhetskopieringsvalv. |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Den här åtgärden används för att få ett valv för säkerhetskopiering för långsiktig kvarhållning. Den returnerar information om det valv som har registrerats för den här servern |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Den här åtgärden används för att registrera en säkerhetskopiering långsiktig kvarhållning vault till en server |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/delete | Tar bort en befintlig server kommunikationskanal. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/read | Returnera listan över kommunikationslänkar av en viss server. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/write | Skapa eller uppdatera en kommunikationskanal för servern. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/read | Returnera listan med principer för server-anslutning för en viss server. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/write | Skapa eller uppdatera en princip för server. |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/read | Returnerar lista över rådgivare som är tillgängliga för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angivna advisor för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/write | Update-ör status för en klassificering på databasnivå. |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/read | Hämta information om tabellen granskningsprincip som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/write | Ändra tabell granskningsprincipen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/read | Hämta information om blob-granskningsprincip som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/write | Ändra blob granskningsprincipen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditRecords/read | Hämta granskningsposter för databas-blob |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/read | Returnerar automatiska justeringsinställningar för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/write | Uppdaterar inställningar för automatisk justering för en databas och returnerar uppdaterade inställningarna |
> | Åtgärd | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Returnera listan över principer för säkerhetskopiering arkivering av en angiven databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Skapa eller uppdatera en databas arkivering princip för säkerhetskopiering. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Hämtar en kort sikt bevarandeprincip för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kort sikt bevarandeprincip för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/read | Hämta information om principen konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/write | Ändra princip för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Batch update känslighet etiketter |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Returnera listan över databasdata maskering av principer. |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Ta bort datamaskning principregel för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Hämta information om datamaskning principregeln som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Ändra datamaskning principregel för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Ändra datamaskning princip för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Returnerar informationen distribuerade frågan steg av data warehouse-fråga för valda steg-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Returnerar data warehouse distribution läsa information för valda fråge-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Hämtar användaraktiviteter för en SQL Data Warehouse-instans som innehåller frågor som körs och inaktiverad |
> | Åtgärd | Microsoft.Sql/servers/databases/delete | Tar bort en befintlig databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/export/action | Exportera Azure SQL-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Hämta information om den utökade blobben granskningsprincip som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Ändra utökade blobben granskningsprincipen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/read | Hämtar en samling med tillägg för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/write | Ändra filnamnstillägget för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/failover/action | Initierad av kund databasredundans. |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Hämta principer för geo-säkerhetskopiering för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Skapa eller uppdatera en databas geobackup princip |
> | Åtgärd | Microsoft.Sql/servers/databases/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Hämtar en lista över tillgängliga underhållsfönster för den valda databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/read | Hämtar Underhåll windows-inställningar för den valda databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/write | Anger Underhåll windows-inställningar för den valda databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/metrics/read | Returnera mått för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/move/action | Ändra namnet på en befintlig databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/operationResults/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/cancel/action | Avbryter Azure SQL Database väntande asynkron åtgärd som inte har slutförts ännu. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/read | Returnera listan över åtgärder som utförs på databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/pause/action | Pause Azure SQL Datawarehouse Database |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Returnerar frågan texter som relaterar till de angivna parametrarna. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/read | Returnerar aktuella värden för Query Store-inställningar för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/write | Uppdaterar Query Store-inställningen för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/delete | Avsluta replikeringsrelationen kernelpaketet och med möjlig dataförlust |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Redundans efter synkronisering alla ändras från primärt gör den här databasen till replikering relationship\u0027s primärt och gör fjärransluten primär till en sekundär |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Redundans omedelbart utan förlust av data, gör den här databasen till replikering relationship\u0027s primärt och gör fjärransluten primär till en sekundär |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/read | Returnera listan över replikering länkar eller hämtar egenskaperna för den angivna replikeringslänkar. |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Avsluta replikeringsrelationen kernelpaketet eller efter synkronisering med partnern |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Uppdatera replikeringsläge för länken till synkron eller asynkron läge |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/action | Skapar en ny återställningspunkt |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/delete | Tar bort en återställningspunkt för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/read | Returnerar återställningspunkter för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/resume/action | Återuppta Azure SQL Datawarehouse-databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/read | Få ett databasschema. |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Få en databaskolumn. |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort etiketten känslighet för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/read | Få en databastabell. |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Hämta lista över indexrekommendationer på en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Tillämpa indexrekommendation |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Hämta en lista över database threat identifiering av principerna som konfigureras för en viss server |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Ändra den database threat principen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/securityMetrics/read | Hämtar en samling av säkerhetsmått för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/sensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Returnera förslag om att skala databasen upp eller ned baserat på frågan körning statistik för att förbättra prestanda och minska kostnaderna |
> | Åtgärd | Microsoft.Sql/servers/databases/skus/read | Hämtar en samling av SKU: er som är tillgängliga för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Avbryt synkronisering Gruppsynkronisering |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/delete | Tar bort en befintlig synkroniseringsgrupp. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Returnera listan över synkronisering hub databasscheman |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/logs/read | Returnera lista över loggar för synkronisering |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/read | Returnera listan över synkronisering grupper eller hämtar egenskaperna för den angivna synkroniseringsgruppen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Uppdatera synkroniseringsschemat hub databas |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Hämta resultatet av uppdateringsåtgärden för synkronisering hub schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Tar bort en befintlig synkroniseringsmedlemmen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Returnera listan över synkroniseringsmedlemmar eller hämtar egenskaperna för den angivna synkroniseringsmedlemmen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Uppdatera synkroniseringsschemat för medlem |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Hämta resultatet av uppdateringsåtgärden för synkronisering av medlem schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Returnera listan över synkronisering medlem databasscheman |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Skapar en synkroniseringsmedlem med de angivna parametrarna eller uppdaterar egenskaperna för den angivna synkroniseringsmedlemmen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Utlösaren synkronisering Gruppsynkronisering |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/write | Skapar en synkroniseringsgrupp med de angivna parametrarna eller uppdaterar egenskaperna för den angivna synkroniseringsgruppen. |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/queryText/action | Returnerar texten Transact-SQL för valda fråge-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/read | Returnerar aggregeras körningsstatistik för valda frågan i den valda tidsperioden |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/statistics/read | Returnerar aggregeras körningsstatistik för valda frågan i den valda tidsperioden |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Hämtar pågående åtgärder på transparent datakryptering |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Hämta status och information om transparent data encryption säkerhetsfunktion för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Ändra transparent data encryption tillstånd |
> | Åtgärd | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uppgradera Azure SQL Datawarehouse-databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/usages/read | Hämtar information för Azure SQL Database-användningar |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Ta bort utvärdering av säkerhetsrisker för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Hämta vulnerability assessment-principer på en givendatabase |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Ta bort vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Hämta vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Ändra vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Konvertera en befintlig genomsökningsresultatet till ett format. Om det finns inget redan händer |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Köra genomsökning för sårbarhetsbedömning databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Returnerar listan över databasen vulnerability assessment genomsökning poster eller hämta genomsökning posten för den angivna genomsökning-ID. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Ändra sårbarhetsbedömning för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Köra genomsökning för sårbarhetsbedömning databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Hämta resultatet från den databasen genomsökningen för sårbarhetsbedömning Kör åtgärden |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Hämta information om den utvärdering av säkerhetsrisker som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Ändra sårbarhetsbedömning för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/write | Skapar en databas med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna databasen. |
> | Åtgärd | Microsoft.Sql/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Tar bort en befintlig konfigurationer med katastrofåterställning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Redundans en DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Framtvinga redundans en DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Hämtar en samling av disaster recovery-konfigurationer som innehåller den här servern |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Ändra haveriberedskapskonfiguration för servern |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/read | Returnerar lista med elastisk pool beräkningar som redan har skapats för den här servern |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/write | Skapar ny elastiska pooluppskattningen lista över databaser som har angetts |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/read | Returnerar lista över rådgivare som är tillgängliga för den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angivna advisor för den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/write | Update-ör status för en klassificering som är på nivån för elastisk pool. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/databases/read | Hämtar en lista över databaser för en elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/delete | Ta bort befintlig elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Hämta aktiviteter och information om en viss elastic database-pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Hämta aktiviteter och information om en viss databas som ingår i elastic database-pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/failover/action | Kunden initiera redundans för elastisk pool. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Returnera typer av mått som är tillgängliga för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metrics/read | Returnera mått för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Avbryter Azure SQL-databaspool väntande asynkron åtgärd som inte har slutförts ännu. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/read | Returnera listan över åtgärder som utförs på den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/read | Hämta information om elastiska poolen på en viss server |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/skus/read | Hämtar en samling av SKU: er som är tillgängliga för en elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/write | Skapa en ny eller ändra egenskaperna för befintlig elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/read | Returnerar en lista över server kryptering protectors eller hämtar egenskaperna för den angivna servern krypteringsskyddet. |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/write | Uppdatera egenskaperna för den angivna Serverkrypteringsskydd. |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/read | Hämta information om den utökade server blob granskningsprincip som konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/write | Ändra den utökade blobgranskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/delete | Tar bort en befintlig redundansgrupp. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/failover/action | Kör planerad redundans i en befintlig redundansgrupp. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kör forcerad redundans i en befintlig redundansgrupp. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/read | Returnerar listan över redundans grupper eller hämtar egenskaperna för den angivna redundansgruppen. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/write | Skapar en redundansgrupp med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna redundansgruppen. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel för servern. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/read | Returnera listan över serverbrandvägg regler eller hämtar egenskaperna för den angivna servern brandväggsregel. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna uppdatera egenskaperna för den angivna regeln eller skriva över alla befintliga regler med nya regler för server-brandväggen. |
> | Åtgärd | Microsoft.Sql/servers/import/action | Skapa en ny databas på servern och distribuera schema och data från ett DacPac-paket |
> | Åtgärd | Microsoft.Sql/servers/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Tar bort den angivna gränssnittet endpoint-profilen |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Returnerar egenskaper för den angivna gränssnitt endpoint-profilen |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Skapar en profil för gränssnittet slutpunkt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna gränssnitt-slutpunkten |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/delete | Tar bort en Azure SQL DB jobbagent |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/read | Hämtar en Azure SQL DB jobbagent |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/write | Skapar eller uppdaterar en Azure SQL DB jobbagent |
> | Åtgärd | Microsoft.Sql/servers/keys/delete | Tar bort en befintlig servernyckel. |
> | Åtgärd | Microsoft.Sql/servers/keys/read | Returnera listan över server nycklar eller hämtar egenskaperna för den angivna servernyckeln. |
> | Åtgärd | Microsoft.Sql/servers/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servernyckeln. |
> | Åtgärd | Microsoft.Sql/servers/operationResults/read | Hämtar pågående åtgärder |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Tar bort en befintlig privat slutpunkt anslutning proxy |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Returnerar listan över privat slutpunkt anslutning proxyservrar eller hämtar egenskaperna för den angivna privata slutpunkt anslutning proxyn. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Verifierar en privat slutpunkt anslutning skapa anrop från NRP-sida |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Skapar en privat slutpunkt anslutning proxy med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna privata slutpunkt anslutning proxyn. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/delete | Tar bort en befintlig privat slutpunkt-anslutning |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/read | Returnerar listan över privat slutpunkt anslutningar eller hämtar egenskaperna för den angivna privata slutpunkt-anslutningen. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/write | Godkänner eller avvisar en befintlig privat slutpunkt-anslutning |
> | Åtgärd | Microsoft.Sql/servers/privateLinkResources/read | Hämta privat länk-resurser för motsvarande sql-server |
> | Åtgärd | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för servrar |
> | Åtgärd | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Hämta mått för rekommenderade elastic database-pooler för en viss server |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/read | Hämta rekommendationer för elastic database-pooler att minska kostnaderna eller förbättra prestanda baserat på historiska resursanvändningen |
> | Åtgärd | Microsoft.Sql/servers/recoverableDatabases/read | Den här åtgärden används för haveriberedskap för live-databas för att återställa databasen till senast fungerande god säkerhetskopieringspunkt. Den returnerar information om den senaste säkerhetskopian som bra men doesn\u0027t faktiskt återställa databasen. |
> | Åtgärd | Microsoft.Sql/servers/replicationLinks/read | Returnera listan över replikering länkar eller hämtar egenskaperna för den angivna replikeringslänkar. |
> | Åtgärd | Microsoft.Sql/servers/restorableDroppedDatabases/read | Hämta en lista över databaser som har tagits bort på en viss server som är fortfarande inom bevarandeprincip. |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Hämta resultatet av Skrivåtgärden server threat identifiering av princip |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/read | Hämta en lista över server threat identifiering av principerna som konfigureras för en viss server |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.Sql/servers/serviceObjectives/read | Hämta lista över servicenivåmål (även kallat prestandanivåer) finns på en viss server |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/delete | Tar bort en befintlig sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/generateKey/action | Generera registreringsnyckel för sync-agenten |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Returnera lista över synkronisera agenten länkade databaser |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/read | Returnera listan över synkroniseringsagenter eller hämtar egenskaperna för den angivna sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/write | Skapar en sync-agenten med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Åtgärd | Microsoft.Sql/servers/usages/read | Returnera server DTU-kvot och den aktuella DTU consuption av alla databaser i server |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.Sql/servers/vulnerabilityAssessments/delete | Ta bort utvärdering av säkerhetsrisker för en viss server |
> | Åtgärd | Microsoft.Sql/servers/vulnerabilityAssessments/read | Hämta vulnerability assessment-principer på en viss server |
> | Åtgärd | Microsoft.Sql/servers/vulnerabilityAssessments/write | Ändra sårbarhetsbedömning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/unregister/action | Avregistrerar prenumerationen för Microsoft SQL Database-resursprovidern och gör det möjligt att skapa Microsoft SQL-databaser. |
> | Åtgärd | Microsoft.Sql/virtualClusters/delete | Tar bort ett befintligt virtuellt kluster. |
> | Åtgärd | Microsoft.Sql/virtualClusters/read | Returnera listan med virtuella kluster eller hämtar egenskaperna för det angivna virtuella klustret. |
> | Åtgärd | Microsoft.Sql/virtualClusters/write | Uppdaterar virtuellt kluster taggar. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Storage/checknameavailability/read | Kontrollerar att kontonamnet är giltigt och används inte. |
> | Åtgärd | Microsoft.Storage/locations/checknameavailability/read | Kontrollerar att kontonamnet är giltigt och används inte. |
> | Åtgärd | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.Storage om att virtuella nätverket eller undernätet tas bort |
> | Åtgärd | Microsoft.Storage/locations/usages/read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |
> | Åtgärd | Microsoft.Storage/operations/read | Avsöker status för en asynkron åtgärd. |
> | Åtgärd | Microsoft.Storage/register/action | Registrerar prenumerationen för lagringsresursprovidern och gör det möjligt att skapa storage-konton. |
> | Åtgärd | Microsoft.Storage/skus/read | Visar en lista över SKU: er som stöds av Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Returnerar resultatet av att lägga till blobinnehåll |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Returnerar resultatet av att ta bort en automatiska ögonblicksbilder |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Returnerar listan över blobar under ett konto med matchande taggar filter |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller bloblista |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Returnerar resultatet av att läsa blob-taggar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Returnerar resultatet av att skriva blob-taggar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en blob |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Rensa blob-behållare juridiska håller |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Ta bort oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Utöka oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Lås oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Hämta oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Placera oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Returnerar resultatet av att leasa blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar lista över behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ställ in bevarande av juridiska skäl för blob-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av patch blob-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att put blob-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en nyckel för delegering av användaren för blob-tjänsten |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read | Returnerar blob tjänstegenskaper eller statistik |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/write | Returnerar resultatet av att placera blob tjänstegenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/delete | Tar bort ett befintligt lagringskonto. |
> | Åtgärd | Microsoft.Storage/storageAccounts/failover/action | Kunden är kan du styra redundansväxling i händelse av tillgänglighetsproblem |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read |  |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/read | Hämta egenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/listAccountSas/action | Returnerar konto SAS-token för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listServiceSas/action | Returnerar Service SAS-token för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/delete | Ta bort hanteringsprinciper för lagringskonto |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/read | Hämta kontoprinciper |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/write | Placera hanteringsprinciper för lagringskonto |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Ta bort slutpunkten för privat anslutning proxyservrar |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/validate/action | Verifiera privat slutpunkt anslutning proxyservrar |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Placera privat slutpunkt anslutning proxyservrar |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Ta bort slutpunkten för privat anslutning |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Hämta slutpunkten för privat anslutning |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Placera privat slutpunkt-anslutning |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateLinkResources/read | Hämta StorageAccount groupids |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returnerar resultatet av att ta bort en kö |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Returnerar resultatet av att lägga till ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Returnerar resultatet av att ta bort ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Returnerar resultatet av bearbetningen av ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returnerar ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Returnerar resultatet av att skriva ett meddelande |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller kölista. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returnerar resultatet av att skriva en kö |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Hämta egenskaper för kötjänst |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Returnerar egenskaper för kötjänst eller statistik. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/write | Returnerar resultatet av att ställa in egenskaper för kötjänst |
> | Åtgärd | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Återställa blob adressintervall till tillståndet för den angivna tiden |
> | Åtgärd | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Återkallar alla nycklar för delegering för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Skapa/uppdatera diagnostikinställningar för lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/tableServices/read | Hämta egenskaper för tabell |
> | Åtgärd | Microsoft.Storage/storageAccounts/write | Skapar ett lagringskonto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna eller lägger till den anpassade domänen för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/usages/read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | microsoft.storagesync/locations/checkNameAvailability/action | Kontrollerar det storage sync-tjänstnamnet är giltigt och används inte. |
> | Åtgärd | Microsoft.storagesync/Locations/Workflows/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | microsoft.storagesync/storageSyncServices/delete | Ta bort alla lagringstjänster för synkronisering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för lagringstjänster för synkronisering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/read | Läsa alla lagringstjänster för synkronisering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/delete | Ta bort alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för registrerad Server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/read | Läsa alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/write | Skapa eller uppdatera alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Ta bort alla Molnslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hämtar status för en asynkron säkerhetskopieringen/återställningen |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Anropa den här åtgärden efter säkerhetskopieringen |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Anropa den här åtgärden efter återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Anropa den här åtgärden innan säkerhetskopiering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Anropa den här åtgärden innan återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Läsa alla Molnslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Återställa pulsslag |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Skapa eller uppdatera alla Molnslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/delete | Ta bort några synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/read | Läs några synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Ta bort alla Serverslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Serverslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Läsa alla Serverslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Anropa den här åtgärden för att återställa filer till en server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Skapa eller uppdatera alla Serverslutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/write | Skapa eller uppdatera några synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/workflows/operationresults/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | microsoft.storagesync/storageSyncServices/workflows/operations/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | microsoft.storagesync/storageSyncServices/workflows/read | Läsa arbetsflöden |
> | Åtgärd | microsoft.storagesync/storageSyncServices/write | Skapa eller uppdatera alla lagringstjänster för synkronisering |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/delete | Tar bort åtkomstkontrollposterna |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/read | Visar en lista över eller hämtar åtkomstkontrollposterna |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/write | Skapa eller uppdatera åtkomstkontrollposterna |
> | Åtgärd | Microsoft.StorSimple/managers/alerts/read | Visar en lista över eller hämtar aviseringarna |
> | Åtgärd | Microsoft.StorSimple/managers/backups/read | Visar en lista över eller hämtar säkerhetskopia |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/delete | Tar bort en befintlig bandbreddsinställningar (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/read | Lista över bandbreddsinställningarna (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/write | Skapar en ny eller uppdaterar bandbreddsinställningar (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/certificates/write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.StorSimple/Managers/certificates/write | Åtgärden Uppdatera Resurscertifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Åtgärd | Microsoft.StorSimple/managers/clearAlerts/action | Rensa alla aviseringar som är associerade med Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Lista Molninstallationen konfigurationer som stöds |
> | Åtgärd | Microsoft.StorSimple/managers/configureDevice/action | Konfigurerar en enhet |
> | Åtgärd | Microsoft.StorSimple/managers/delete | Tar bort enhetshanterare |
> | Åtgärd | Microsoft.StorSimple/Managers/delete | Ta bort valv raderas den angivna Azure-resursen av typen ”vault' |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/read | Visar en lista över eller hämtar de aviseringsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/write | Skapa eller uppdatera aviseringsinställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/authorizeForServiceEncryptionKeyRollover/action | Auktorisera för tjänsten Datakrypteringsnyckeln av enheter |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Säkerhetskopiera en manuell säkerhetskopiering för att skapa en på begäran av alla volymer som skyddas av principen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Tar bort en befintlig säkerhetskopia principerna (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/read | Lista säkerhetskopieringen principerna (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Tar bort en befintliga scheman |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Visa scheman |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Skapar en ny eller uppdaterar scheman |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/write | Skapar en ny eller uppdaterar principerna för säkerhetskopiering (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/delete | Tar bort säkerhetskopian |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klona en resursen eller volymen med hjälp av en säkerhetskopieringselementets. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/read | Visar en lista över eller hämtar säkerhetskopia |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/restore/action | Återställa alla volymer från en säkerhetskopia. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Tar bort grupperna schema för säkerhetskopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Visar en lista över eller hämtar grupperna schema för säkerhetskopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Skapa eller uppdatera grupperna schema för säkerhetskopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/delete | Tar bort Chap-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/read | Visar en lista över eller hämtar Chap-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/write | Skapa eller uppdatera Chap-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/deactivate/action | Inaktiverar en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/delete | Tar bort enheter |
> | Åtgärd | Microsoft.StorSimple/managers/devices/disks/read | Hämtar diskarna eller visar en lista över |
> | Åtgärd | Microsoft.StorSimple/managers/devices/download/action | Ladda ned uppdateringar för en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/failover/action | Redundans för enheten. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/failover/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/failoverTargets/read | Visar en lista över eller hämtar redundansmål enheter |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Säkerhetskopiera en filserver. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/delete | Tar bort filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Visar en lista över eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Visar en lista över eller hämtar Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/read | Visar en lista över eller hämtar filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Tar bort filresurser |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Visar en lista över eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Visar en lista över eller hämtar Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Visar en lista över eller hämtar filresurser |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Skapa eller uppdatera filresurser |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/write | Skapa eller uppdatera filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Ändra kontrollantens energitillstånd av maskinvara komponentgrupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Lista över maskinvara komponentgrupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/install/action | Installera uppdateringar på en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/installUpdates/action | Installerar uppdateringar på enheter (endast 8000-serien). |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Säkerhetskopiera med en iSCSI-server. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Tar bort iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Tar bort diskar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Visar en lista över eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Visar en lista över eller hämtar Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Hämtar diskarna eller visar en lista över |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Skapa eller uppdatera diskar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Visar en lista över eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Visar en lista över eller hämtar Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/read | Visar en lista över eller hämtar iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/write | Skapa eller uppdatera iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Avbryta ett jobb som körs |
> | Åtgärd | Microsoft.StorSimple/managers/devices/jobs/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/jobs/read | Visar en lista över eller hämtar jobb |
> | Åtgärd | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Lista över uppsättningarna redundans för en befintlig enhet (endast 8000-serien). |
> | Åtgärd | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Lista över redundansmål av enheter (endast 8000-serien). |
> | Åtgärd | Microsoft.StorSimple/managers/devices/metrics/read | Visar en lista över eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Visar en lista över eller hämtar Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Bekräftar en lyckad migrering och sparar den. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigrationStatus/read | Lista de bekräfta Migreringsstatus |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Hämta bekräftelsestatus för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Hämta status för migreringsjobb för kostnadsuppskattning. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Hämta status för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importera konfigurationer som källa för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Lista på uppskattningen av migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationStatus/read | Lista statusen för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Starta migrering med hjälp av käll-konfigurationer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Starta ett jobb för att beräkna varaktigheten för migreringsprocessen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/read | Visar en lista över eller hämtar inställningarna för det |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/write | Skapar en ny eller uppdaterar nätverksinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Lista kryptering med offentlig nyckel för Enhetshanteraren |
> | Åtgärd | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publicera supportpaket för en befintlig enhet. Ett supportpaket för StorSimple är en enkel att använda mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med felsökning av problem med StorSimple-enheten. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/read | Hämtar enheterna eller visar en lista över |
> | Åtgärd | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Sök efter uppdateringar i en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/read | Lista över säkerhetsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synkronisera certifikat för fjärrhantering för en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Uppdatera säkerhetsinställningar. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/write | Skapar en ny eller uppdaterar säkerhetsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Skicka testaviseringsmeddelande till konfigurerade e-postmottagare. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/shares/read | Visar en lista över eller hämtar filresurser |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/read | Visar en lista över eller hämtar inställningarna för tid |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/write | Skapar en ny eller uppdaterar tidsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/devices/updateSummary/read | Hämtar uppdatering sammanfattningen eller visar en lista över |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Tar bort en befintlig Volymbehållare (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Lista över mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Lista över Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/read | Lista Volymbehållarna (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Tar bort en befintliga volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Lista över mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Lista över Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Visa resultatet för åtgärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Lista över volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Skapar en ny eller uppdaterar volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/write | Skapar en ny eller uppdaterar Volymbehållare (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumes/read | Lista över volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/write | Skapa eller uppdatera enheter |
> | Åtgärd | Microsoft.StorSimple/managers/encryptionSettings/read | Visar en lista över eller hämtar krypteringsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/delete | Tar bort utökad Vault-Information |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/delete | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/read | Hämtar Valvinformation utökade eller visar en lista över |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/write | Skapa eller uppdatera Valvinformation utökade |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.StorSimple/managers/features/read | Lista över funktionerna |
> | Åtgärd | Microsoft.StorSimple/managers/fileservers/read | Visar en lista över eller hämtar filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/getEncryptionKey/action | Hämta krypteringsnyckel för Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/iscsiservers/read | Visar en lista över eller hämtar iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/jobs/read | Visar en lista över eller hämtar jobb |
> | Åtgärd | Microsoft.StorSimple/managers/listActivationKey/action | Hämtar aktiveringsnyckel av StorSimple Device Manager. |
> | Åtgärd | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Lista över offentliga krypteringsnycklar för en StorSimple-enhetshanterare. |
> | Åtgärd | Microsoft.StorSimple/managers/metrics/read | Visar en lista över eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/metricsDefinitions/read | Visar en lista över eller hämtar Måttdefinitioner |
> | Åtgärd | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrera klassiska till Resource Manager för Mangers |
> | Åtgärd | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | En lista med konfigurationerna för migrering källa (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Skapa en ny molninstallation. |
> | Åtgärd | Microsoft.StorSimple/managers/read | Visar en lista över eller hämtar de enhetshanterare |
> | Åtgärd | Microsoft.StorSimple/Managers/read | Hämta valv-åtgärden hämtar ett objekt som representerar Azure-resursen av typen ”vault' |
> | Åtgärd | Microsoft.StorSimple/managers/regenerateActivationKey/action | Återskapa aktiveringsnyckeln för en befintlig StorSimple Device Manager. |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Tar bort autentiseringsuppgifter för Lagringskonto |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/read | Visar en lista över eller hämtar autentiseringsuppgifterna för Lagringskontot |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/write | Skapa eller uppdatera autentiseringsuppgifterna för Lagringskontot |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/delete | Tar bort Storage-domäner |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Visar en lista över eller hämtar den Åtgärdsresultat |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/read | Visar en lista över eller hämtar de Storage-domäner |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/write | Skapa eller uppdatera Storage-domäner |
> | Åtgärd | Microsoft.StorSimple/managers/write | Skapa eller uppdatera de enhetshanterare |
> | Åtgärd | Microsoft.StorSimple/Managers/write | Med skapa valv så skapas en Azure-resurs av typen valv |
> | Åtgärd | Microsoft.StorSimple/operations/read | Visar en lista över eller hämtar åtgärder |
> | Åtgärd | Microsoft.StorSimple/register/action | Registrera providern Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.StreamAnalytics/locations/quotas/Read | Läs Stream Analytics-Prenumerationskvot |
> | Åtgärd | Microsoft.StreamAnalytics/operations/Read | Läs Stream Analytics-åtgärder |
> | Åtgärd | Microsoft.StreamAnalytics/Register/action | Registrera prenumeration med Stream Analytics-Resursprovider |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Delete | Delete Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Ta bort Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Läs Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Hämta standarddefinitionen av en Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Testa Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Skriva Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Ta bort Stream Analytics-Jobbindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-Jobbindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Läs Stream Analytics-Jobbindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Exempel Stream Analytics-Jobbindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Testa Stream Analytics-Jobbindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Skriva Stream Analytics-Jobbindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Läs Måttdefinitioner |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-jobb |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Ta bort Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Läs Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Testa Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Skriva Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Läs diagnostikinställning. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Skriv diagnostikinställning. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Read | Läs Stream Analytics-jobb |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Start/action | Start Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stop Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Ta bort Stream Analytics-Jobbomvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Läs Stream Analytics-Jobbomvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Skriva Stream Analytics-Jobbomvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Write | Write Stream Analytics Job |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Subscription/CreateSubscription/action | Skapa en Azure-prenumeration |
> | Åtgärd | Microsoft.Subscription/register/action | Registrerar prenumerationen med Microsoft.Subscription-resursprovidern |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/read | Hämta en Azure-prenumerationsdefinition i en hanteringsgrupp. |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/write | Skapa en definition för Azure-prenumeration |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Support/register/action | Registrerar till supportresursprovidern |
> | Åtgärd | Microsoft.Support/supportTickets/read | Hämtar information om supportbegäran (inklusive status, allvarlighetsgrad, kontaktinformation och kommunikation) eller hämtar listan över supportbegäranden för alla prenumerationer. |
> | Åtgärd | Microsoft.Support/supportTickets/write | Skapar eller uppdaterar en supportbegäran. Du kan skapa en supportbegäran om tekniska problem, fakturering, kvoter eller prenumerationshantering. Du kan uppdatera allvarlighetsgraden, kontaktinformation och kommunikation för befintliga supportbegäranden. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Tar bort åtkomstprincipen. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hämta egenskaperna för en åtkomstprincip. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Skapar en ny åtkomstprincip för en miljö eller uppdaterar en befintlig åtkomstprincip. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/delete | Tar bort miljön. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Tar bort händelsekällan. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/read | Hämta egenskaperna för en händelsekälla. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/write | Skapar en ny händelsekälla för en miljö eller uppdaterar en befintlig händelsekälla. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/read | Hämta egenskaperna för en miljö. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Tar bort referensdatauppsättningen. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Hämta egenskaperna för en referensdatauppsättning. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Skapar en ny referens-datauppsättning för en miljö eller uppdaterar en befintlig referensdatauppsättningen. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/status/read | Hämta status för miljön, tillståndet för dess associerade åtgärder som att ingående. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/write | Skapar en ny miljö eller uppdaterar en befintlig miljö. |
> | Åtgärd | Microsoft.TimeSeriesInsights/register/action | Registrerar prenumerationen för Time Series Insights-resursprovidern och gör det möjligt att skapa Time Series Insights-miljöer. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.VisualStudio/Account/Delete | Ta bort konto |
> | Åtgärd | Microsoft.VisualStudio/Account/Extension/Read | Läs-/ Kontotillägg |
> | Åtgärd | Microsoft.VisualStudio/Account/Project/Read | Läs konto/projektet |
> | Åtgärd | Microsoft.VisualStudio/Account/Project/Write | Ange konto/projektet |
> | Åtgärd | Microsoft.VisualStudio/Account/Read | Läs-konto |
> | Åtgärd | Microsoft.VisualStudio/Account/Write | Ange konto |
> | Åtgärd | Microsoft.VisualStudio/Extension/Delete | Ta bort tillägg |
> | Åtgärd | Microsoft.VisualStudio/Extension/Read | Läsa tillägg |
> | Åtgärd | Microsoft.VisualStudio/Extension/Write | Ange tillägg |
> | Åtgärd | Microsoft.VisualStudio/Project/Delete | Ta bort projekt |
> | Åtgärd | Microsoft.VisualStudio/Project/Read | Läs projekt |
> | Åtgärd | Microsoft.VisualStudio/Project/Write | Ange projekt |
> | Åtgärd | Microsoft.VisualStudio/Register/Action | Registrera Azure-prenumerationen med Microsoft.VisualStudio providern |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | microsoft.web/apimanagementaccounts/apiacls/read | Hämta Apiacls för Api Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Ta bort Api Management konton API: er Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/read | Hämta Api Management konton API: er Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/write | Uppdatera Api Management konton API: er Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Hämta Api Management konton API: er Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Bekräfta godkännande kod Api Management-konton-API: er anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Ta bort Api Management-konton API: er anslutningar Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Hämta Api Management-konton API: er anslutningar Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Uppdatera Api Management-konton API: er anslutningar Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/delete | Ta bort Api Management-konton API: er-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Hämta Samtyckeslänkar för Api Management-konton API: er-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Lista anslutning nycklar Api Management-konton API: er anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Lista hemligheter Api Management-konton API: er anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/read | Hämta Api Management-konton API: er-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/write | Uppdatera Api Management-konton API: er-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/delete | Ta bort API: er för Api Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Ta bort Api Management API: er för konton lokaliserade definitioner. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Hämta Api Management API: er för konton lokaliserade definitioner. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Uppdatera Api Management-konton API: er lokaliserade definitioner. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/read | Hämta API: er för Api Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/write | Uppdatera API: er för Api Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/connectionacls/read | Hämta Connectionacls för Api Management-konton. |
> | Åtgärd | microsoft.web/availablestacks/read | Hämta tillgängliga stackar. |
> | Åtgärd | Microsoft.Web/certificates/Delete | Ta bort ett befintligt certifikat. |
> | Åtgärd | Microsoft.Web/certificates/Read | Hämta listan över certifikat. |
> | Åtgärd | Microsoft.Web/certificates/Write | Lägg till ett nytt certifikat eller uppdatera en befintlig. |
> | Åtgärd | microsoft.web/checknameavailability/read | Kontrollera om resursnamnet är tillgängligt. |
> | Åtgärd | microsoft.web/classicmobileservices/read | Hämta klassiska mobila tjänster. |
> | Åtgärd | Microsoft.Web/connectionGateways/Delete | Tar bort en Gateway för anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/Join/Action | Ansluter till en Gateway för anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/ListStatus/Action | Visar status för en Gateway-anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/Move/Action | Flyttar en Gateway för anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/Read | Hämta listan över gatewayer för anslutningen. |
> | Åtgärd | Microsoft.Web/connectionGateways/Write | Skapar eller uppdaterar en Gateway-anslutning. |
> | Åtgärd | microsoft.web/connections/confirmconsentcode/action | Bekräfta anslutningar medgivande kod. |
> | Åtgärd | Microsoft.Web/connections/Delete | Tar bort en anslutning. |
> | Åtgärd | Microsoft.Web/connections/Join/Action | Ansluter till en anslutning. |
> | Åtgärd | microsoft.web/connections/listconsentlinks/action | Lista Samtyckeslänkar för anslutningar. |
> | Åtgärd | Microsoft.Web/connections/Move/Action | Flyttar en anslutning. |
> | Åtgärd | Microsoft.Web/connections/Read | Hämta listan över anslutningar. |
> | Åtgärd | Microsoft.Web/connections/Write | Skapar eller uppdaterar en anslutning. |
> | Åtgärd | Microsoft.Web/customApis/Delete | Tar bort ett anpassat API. |
> | Åtgärd | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extraherar API-definition från ett WSDL. |
> | Åtgärd | Microsoft.Web/customApis/Join/Action | Ansluter till ett anpassat API. |
> | Åtgärd | Microsoft.Web/customApis/listWsdlInterfaces/Action | Visar en lista över WSDL-gränssnitt för ett anpassat API. |
> | Åtgärd | Microsoft.Web/customApis/Move/Action | Flyttar ett anpassat API. |
> | Åtgärd | Microsoft.Web/customApis/Read | Hämta listan över anpassade API: et. |
> | Åtgärd | Microsoft.Web/customApis/Write | Skapar eller uppdaterar ett anpassat API. |
> | Åtgärd | Microsoft.Web/deletedSites/Read | Hämta egenskaperna för en Webbapp som har tagits bort |
> | Åtgärd | microsoft.web/deploymentlocations/read | Hämta distribution platser. |
> | Åtgärd | Microsoft.Web/geoRegions/Read | Hämta listan över geografiska regionerna. |
> | Åtgärd | microsoft.web/hostingenvironments/capacities/read | Hämta som är värd för miljöer kapaciteter. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Delete | Ta bort en App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/detectors/read | Hämta som är värd för miljöer detektorerna. |
> | Åtgärd | microsoft.web/hostingenvironments/diagnostics/read | Hämta som är värd för miljöer diagnostik. |
> | Åtgärd | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Hämta nätverksslutpunkter av alla inkommande beroenden. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Join/Action | Ansluter till en App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/metricdefinitions/read | Hämta som är värd för definitioner av mätvärden för miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Hämta som är värd för miljöer definitioner av mätvärden för flera roller pooler. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/metrics/read | Hämta som är värd för miljöer flera roller pooler mått. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Hämta egenskaperna för en adresspool på klientdelen i en App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/skus/read | Hämta som är värd för miljöer flera roller pooler SKU: er. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/usages/read | Hämta som är värd för miljöer flera roller pooler användningar. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Skapa en ny adresspool på klientdelen i en App Service Environment eller uppdatera en befintlig |
> | Åtgärd | microsoft.web/hostingenvironments/operations/read | Hämta som är värd Operations miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Hämta nätverksslutpunkter alla utgående beroenden. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Godkänn privat slutpunkt-anslutningar |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Read | Hämta egenskaperna för en App Service Environment |
> | Åtgärd | Microsoft.Web/hostingEnvironments/reboot/Action | Starta om alla datorer i en App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/resume/action | Återuppta värdmiljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/serverfarms/read | Hämta som är värd för miljöer App Service-planer. |
> | Åtgärd | microsoft.web/hostingenvironments/sites/read | Hämta som är värd för Webbappar miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/suspend/action | Pausa värdmiljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/usages/read | Hämta som är värd för miljöer användningar. |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Hämta som är värd för miljöer Workerpools Måttdefinitioner. |
> | Åtgärd | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Hämta som är värd för miljöer Workerpools mått. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/Read | Hämta egenskaperna för en Arbetarpool i en App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/skus/read | Hämta som är värd för miljöer Workerpools SKU: er. |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/usages/read | Hämta som är värd för miljöer Workerpools användningar. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/Write | Skapa en ny Arbetarpool i en App Service Environment eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Write | Skapa en ny App Service Environment eller uppdatera befintlig |
> | Åtgärd | microsoft.web/ishostingenvironmentnameavailable/read | Få om namnet som är värd för miljön är tillgänglig. |
> | Åtgärd | microsoft.web/ishostnameavailable/read | Kontrollera om värdnamnet är tillgänglig. |
> | Åtgärd | microsoft.web/isusernameavailable/read | Kontrollera om användarnamnet är tillgängligt. |
> | Åtgärd | Microsoft.Web/listSitesAssignedToHostName/Read | Hämta namnen på webbplatser tilldelade till värdnamn. |
> | Åtgärd | microsoft.web/locations/apioperations/read | Hämta platser API-åtgärder. |
> | Åtgärd | microsoft.web/locations/connectiongatewayinstallations/read | Hämta platser anslutningen Gateway-installationer. |
> | Åtgärd | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Virtuellt nätverk eller undernät borttagning av meddelande för platser. |
> | Åtgärd | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrahera Api-Definition från WSDL för platser. |
> | Åtgärd | microsoft.web/locations/listwsdlinterfaces/action | Visa WSDL-gränssnitt för platser. |
> | Åtgärd | microsoft.web/locations/managedapis/apioperations/read | Hämta platser hanterade API-åtgärder. |
> | Åtgärd | Microsoft.Web/locations/managedapis/Join/Action | Ansluter till en hanterad API. |
> | Åtgärd | microsoft.web/locations/managedapis/read | Hämta platser hanterade API: er. |
> | Åtgärd | microsoft.web/operations/read | Hämta åtgärder. |
> | Åtgärd | microsoft.web/publishingusers/read | Hämta publicera användare. |
> | Åtgärd | microsoft.web/publishingusers/write | Uppdatera publicerar användare. |
> | Åtgärd | Microsoft.Web/recommendations/Read | Hämta lista över rekommendationer för prenumerationer. |
> | Åtgärd | microsoft.web/register/action | Registrera resursprovidern för Microsoft.Web för prenumerationen. |
> | Åtgärd | microsoft.web/resourcehealthmetadata/read | Get Resource Health Metadata. |
> | Åtgärd | microsoft.web/serverfarms/capabilities/read | Hämta funktioner för App Service-planer. |
> | Åtgärd | Microsoft.Web/serverfarms/Delete | Ta bort en befintlig App Service-Plan |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/delete | Ta bort App Service-planer första part appar-inställningarna. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/read | Hämta App Service-planer första part appar inställningar. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/write | Uppdatera App Service-planer första part appar inställningar. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/delete | Ta bort App Service-planer Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/read | Hämta App Service-planer Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionnamespaces/relays/sites/read | Hämta App Service-planer Hybrid anslutning namnområden reläer Web Apps. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Hämta Plan för App Service-planer Hybrid anslutningsgräns. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionrelays/read | Hämta App Service-planer Hybrid anslutning reläer. |
> | Åtgärd | microsoft.web/serverfarms/metricdefinitions/read | Hämta Måttdefinitioner för App Service-planer. |
> | Åtgärd | microsoft.web/serverfarms/metrics/read | Hämta mått för App Service-planer. |
> | Åtgärd | microsoft.web/serverfarms/operationresults/read | Hämta Åtgärdsresultat för App Service-planer. |
> | Åtgärd | Microsoft.Web/serverfarms/Read | Visa egenskaperna för en App Service Plan |
> | Åtgärd | Microsoft.Web/serverfarms/restartSites/Action | Starta om alla webbprogram i en App Service Plan |
> | Åtgärd | microsoft.web/serverfarms/sites/read | Hämta App Service-planer Web Apps. |
> | Åtgärd | microsoft.web/serverfarms/skus/read | Hämta SKU: er för App Service-planer. |
> | Åtgärd | microsoft.web/serverfarms/usages/read | Hämta App Service-planer användningar. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Uppdatera App Service-planer Vnet-anslutningar Gateways. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/read | Hämta virtuella nätverksanslutningar för App Service-planer. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Ta bort vägar för anslutningar i App Service-planer virtuella nätverk. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Hämta vägar för anslutningar i App Service-planer virtuella nätverk. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Uppdatera vägar för anslutningar i App Service-planer virtuella nätverk. |
> | Åtgärd | microsoft.web/serverfarms/workers/reboot/action | Starta om App Service-planer arbetare. |
> | Åtgärd | Microsoft.Web/serverfarms/Write | Skapa en ny App Service-Plan eller uppdatera en befintlig |
> | Åtgärd | microsoft.web/sites/analyzecustomhostname/read | Analysera anpassade värdnamnet. |
> | Åtgärd | Microsoft.Web/sites/applySlotConfig/Action | Tillämpa web app platskonfigurationen från målplatsen till aktuella webbappen |
> | Åtgärd | Microsoft.Web/sites/backup/Action | Skapa en ny säkerhetskopia av web app |
> | Åtgärd | microsoft.web/sites/backup/read | Hämta säkerhetskopiering av Webbappar. |
> | Åtgärd | microsoft.web/sites/backup/write | Uppdatera säkerhetskopiering av Webbappar. |
> | Åtgärd | Microsoft.Web/Sites/Backups/Action | Identifierar en befintlig säkerhetskopia av appen som kan återställas från en blob i Azure storage. |
> | Åtgärd | microsoft.web/sites/backups/delete | Ta bort säkerhetskopierade Webbappar. |
> | Åtgärd | Microsoft.Web/Sites/Backups/List/Action | Lista säkerhetskopierade Webbappar. |
> | Åtgärd | Microsoft.Web/sites/backups/Read | Hämta egenskaperna för en webbapp-säkerhetskopiering |
> | Åtgärd | Microsoft.Web/Sites/Backups/Restore/Action | Återställ säkerhetskopierade Webbappar. |
> | Åtgärd | Microsoft.Web/Sites/Backups/Write | Uppdatera säkerhetskopierade Webbappar. |
> | Åtgärd | microsoft.web/sites/config/delete | Ta bort appar Webbkonfiguration. |
> | Åtgärd | Microsoft.Web/sites/config/list/Action | Lista över Web App säkerhet känsliga inställningar, till exempel publicering av autentiseringsuppgifter, appinställningar och anslutningssträngar |
> | Åtgärd | Microsoft.Web/sites/config/Read | Hämta inställningar för Web App |
> | Åtgärd | microsoft.web/sites/config/snapshots/read | Hämta ögonblicksbilder för Web Apps-konfiguration. |
> | Åtgärd | Microsoft.Web/sites/config/Write | Uppdatera inställningar för Web App |
> | Åtgärd | microsoft.web/sites/containerlogs/action | Hämta zippade Behållarloggarna för Webbapp. |
> | Åtgärd | Microsoft.Web/Sites/containerlogs/Download/Action | Hämta Behållarloggarna för Web Apps. |
> | Åtgärd | microsoft.web/sites/continuouswebjobs/delete | Ta bort Webbjobb för Web Apps kontinuerlig. |
> | Åtgärd | microsoft.web/sites/continuouswebjobs/read | Få kontinuerlig Webbjobb för Web Apps. |
> | Åtgärd | microsoft.web/sites/continuouswebjobs/start/action | Starta Webbjobb för Web Apps kontinuerlig. |
> | Åtgärd | microsoft.web/sites/continuouswebjobs/stop/action | Stoppa Webbjobb för Web Apps kontinuerlig. |
> | Åtgärd | Microsoft.Web/sites/Delete | Ta bort en befintlig Webbapp |
> | Åtgärd | microsoft.web/sites/deployments/delete | Ta bort Web Apps-distributioner. |
> | Åtgärd | microsoft.web/sites/deployments/log/read | Hämta Web Apps distributioner Log. |
> | Åtgärd | microsoft.web/sites/deployments/read | Hämta Web Apps-distributioner. |
> | Åtgärd | microsoft.web/sites/deployments/write | Web Apps distributioner av uppdatering. |
> | Åtgärd | microsoft.web/sites/detectors/read | Hämta detektorer för Web Apps. |
> | Åtgärd | microsoft.web/sites/diagnostics/analyses/execute/Action | Kör Web Apps diagnostik analys. |
> | Åtgärd | microsoft.web/sites/diagnostics/analyses/read | Hämta Web Apps diagnostik analys. |
> | Åtgärd | microsoft.web/sites/diagnostics/aspnetcore/read | Hämta Web Apps diagnostik för ASP.NET Core-app. |
> | Åtgärd | microsoft.web/sites/diagnostics/autoheal/read | Hämta Web Apps diagnostik Autoheal. |
> | Åtgärd | microsoft.web/sites/diagnostics/deployment/read | Hämta appar diagnostik för webbdistribution. |
> | Åtgärd | microsoft.web/sites/diagnostics/deployments/read | Hämta Web Apps diagnostik distributioner. |
> | Åtgärd | microsoft.web/sites/diagnostics/detectors/execute/Action | Köra Web Apps diagnostik detektor. |
> | Åtgärd | microsoft.web/sites/diagnostics/detectors/read | Hämta Web Apps diagnostik detektor. |
> | Åtgärd | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Hämta Web Apps diagnostik misslyckade förfrågningar Per Uri. |
> | Åtgärd | microsoft.web/sites/diagnostics/frebanalysis/read | Hämta Web Apps diagnostik FREB analys. |
> | Åtgärd | microsoft.web/sites/diagnostics/loganalyzer/read | Hämta Web Apps diagnostik Log Analyzer. |
> | Åtgärd | microsoft.web/sites/diagnostics/read | Hämta Web Apps diagnostik kategorier. |
> | Åtgärd | microsoft.web/sites/diagnostics/runtimeavailability/read | Få Web Apps diagnostik Runtime tillgänglighet. |
> | Åtgärd | microsoft.web/sites/diagnostics/servicehealth/read | Hämta Tjänstehälsa för Web Apps-diagnostik. |
> | Åtgärd | microsoft.web/sites/diagnostics/sitecpuanalysis/read | Hämta Web Apps diagnostik plats CPU analys. |
> | Åtgärd | microsoft.web/sites/diagnostics/sitecrashes/read | Hämta Web Apps diagnostik plats kraschar. |
> | Åtgärd | microsoft.web/sites/diagnostics/sitelatency/read | Hämta svarstid för Web Apps diagnostik webbplats. |
> | Åtgärd | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Hämta Minnesanalyser för Web Apps diagnostik plats. |
> | Åtgärd | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Hämta Web Apps diagnostik plats omstart inställningen Update. |
> | Åtgärd | microsoft.web/sites/diagnostics/siterestartuserinitiated/read | Hämta Web Apps diagnostik plats omstart användarinitierad. |
> | Åtgärd | microsoft.web/sites/diagnostics/siteswap/read | Hämta Web Apps diagnostik plats växling. |
> | Åtgärd | microsoft.web/sites/diagnostics/threadcount/read | Hämta Trådantalet för Web Apps-diagnostik. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Hämta Web Apps diagnostik Workeravailability. |
> | Åtgärd | microsoft.web/sites/diagnostics/workerprocessrecycle/read | Hämta Web Apps diagnostik Worker omarbetning av processen. |
> | Åtgärd | microsoft.web/sites/domainownershipidentifiers/read | Få ägarskap identifierare för Web Apps-domän. |
> | Åtgärd | microsoft.web/sites/domainownershipidentifiers/write | Uppdatera Web Apps-domän ägarskap identifierare. |
> | Åtgärd | microsoft.web/sites/extensions/delete | Ta bort Webbplatstillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/extensions/read | Hämta Webbplatstillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/extensions/write | Uppdatera Webbplatstillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/functions/action | Functions-Webbappar. |
> | Åtgärd | microsoft.web/sites/functions/delete | Ta bort Web Apps-funktioner. |
> | Åtgärd | microsoft.web/sites/functions/listsecrets/action | Lista hemligheter Web Apps-funktioner. |
> | Åtgärd | Microsoft.Web/Sites/Functions/masterkey/Read | Hämta Masterkey för Web Apps-funktioner. |
> | Åtgärd | Microsoft.Web/Sites/Functions/Read | Hämta Web Apps-funktioner. |
> | Åtgärd | Microsoft.Web/Sites/Functions/token/Read | Hämta Web Apps funktioner Token. |
> | Åtgärd | Microsoft.Web/Sites/Functions/Write | Uppdatera Web Apps-funktioner. |
> | Åtgärd | microsoft.web/sites/hostnamebindings/delete | Ta bort Värdnamnsbindningar för Web Apps. |
> | Åtgärd | microsoft.web/sites/hostnamebindings/read | Hämta Värdnamnsbindningar för Web Apps. |
> | Åtgärd | microsoft.web/sites/hostnamebindings/write | Uppdatera Värdnamnsbindningar för Web Apps. |
> | Åtgärd | microsoft.web/sites/hostruntime/functions/keys/read | Hämta nycklar för Web Apps Hostruntime funktioner. |
> | Åtgärd | Microsoft.Web/sites/hostruntime/host/_master/read | Hämta Funktionsappens huvudnyckeln för administratörsåtgärder |
> | Åtgärd | Microsoft.Web/sites/hostruntime/host/action | Utföra Funktionsapp runtime åtgärd som synkronisera utlösare, Lägg till funktioner, anropa funktioner, ta bort funktioner osv. |
> | Åtgärd | microsoft.web/sites/hostruntime/host/read | Hämta Web Apps Hostruntime värd. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnection/delete | Ta bort Hybridanslutningen för Web Apps. |
> | Åtgärd | microsoft.web/sites/hybridconnection/read | Hämta Hybridanslutning för Web Apps. |
> | Åtgärd | microsoft.web/sites/hybridconnection/write | Uppdatera Hybridanslutning för Web Apps. |
> | Åtgärd | microsoft.web/sites/hybridconnectionnamespaces/relays/delete | Ta bort Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/sites/hybridconnectionnamespaces/relays/listkeys/action | Lista nycklar Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Hämta Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/sites/hybridconnectionnamespaces/relays/write | Uppdatera webb Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/sites/hybridconnectionrelays/read | Hämta Web Apps Hybrid anslutning reläer. |
> | Åtgärd | microsoft.web/sites/instances/deployments/delete | Ta bort Web Apps instanser distributioner. |
> | Åtgärd | microsoft.web/sites/instances/deployments/read | Hämta Web Apps instanser distributioner. |
> | Åtgärd | microsoft.web/sites/instances/extensions/log/read | Hämta Web Apps instanser tillägg Log. |
> | Åtgärd | microsoft.web/sites/instances/extensions/processes/read | Hämta Web Apps instanser tillägg processer. |
> | Åtgärd | microsoft.web/sites/instances/extensions/read | Hämta tillägg för Web Apps instanser. |
> | Åtgärd | microsoft.web/sites/instances/processes/delete | Ta bort Web Apps instanser processer. |
> | Åtgärd | microsoft.web/sites/instances/processes/modules/read | Hämta Web Apps instanser processer moduler. |
> | Åtgärd | microsoft.web/sites/instances/processes/read | Hämta Web Apps instanser processer. |
> | Åtgärd | Microsoft.Web/Sites/instances/processes/threads/Read | Hämta Web Apps instanser processer trådar. |
> | Åtgärd | microsoft.web/sites/instances/read | Hämta instanser för Web Apps. |
> | Åtgärd | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Lista synkronisering funktionen utlösaren Status Web Apps. |
> | Åtgärd | microsoft.web/sites/metricdefinitions/read | Hämta Måttdefinitioner för Web Apps. |
> | Åtgärd | microsoft.web/sites/metrics/read | Hämta Web Apps mått. |
> | Åtgärd | microsoft.web/sites/metricsdefinitions/read | Hämta Måttdefinitioner för Web Apps. |
> | Åtgärd | microsoft.web/sites/migratemysql/action | Migrera MySql Web Apps. |
> | Åtgärd | microsoft.web/sites/migratemysql/read | Hämta Web Apps migrera MySql. |
> | Åtgärd | microsoft.web/sites/networktrace/action | Nätverket Trace Web Apps. |
> | Åtgärd | microsoft.web/sites/networktraces/operationresults/read | Hämta Åtgärdsresultat för Web Apps nätverk spårningen. |
> | Åtgärd | microsoft.web/sites/newpassword/action | Nytt lösenord Web Apps. |
> | Åtgärd | microsoft.web/sites/operationresults/read | Hämta Åtgärdsresultat för Web Apps. |
> | Åtgärd | microsoft.web/sites/operations/read | Hämta åtgärder för Web Apps. |
> | Åtgärd | microsoft.web/sites/perfcounters/read | Få prestandaräknare för Web Apps. |
> | Åtgärd | microsoft.web/sites/premieraddons/delete | Ta bort Premier-tillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/premieraddons/read | Hämta Web Apps Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/premieraddons/write | Uppdatera webb Apps Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/privateaccess/read | Hämta data om privat plats åtkomst aktivering och auktoriserade virtuella nätverk som har åtkomst till webbplatsen. |
> | Åtgärd | Microsoft.Web/sites/PrivateEndpointConnectionsApproval/action | Godkänn privat slutpunkt-anslutningar |
> | Åtgärd | microsoft.web/sites/processes/modules/read | Hämta Web Apps processer moduler. |
> | Åtgärd | microsoft.web/sites/processes/read | Hämta processer för Web Apps. |
> | Åtgärd | microsoft.web/sites/processes/threads/read | Hämta Web Apps processer trådar. |
> | Åtgärd | microsoft.web/sites/publiccertificates/delete | Ta bort Web Apps offentliga certifikat. |
> | Åtgärd | microsoft.web/sites/publiccertificates/read | Hämta Web Apps offentliga certifikat. |
> | Åtgärd | microsoft.web/sites/publiccertificates/write | Uppdatera webb Apps offentliga certifikat. |
> | Åtgärd | Microsoft.Web/sites/publish/Action | Publicera en Webbapp |
> | Åtgärd | Microsoft.Web/sites/publishxml/Action | Hämta Publicera profil-xml för en Webbapp |
> | Åtgärd | microsoft.web/sites/publishxml/read | Hämta publicera XML-Webbappar. |
> | Åtgärd | Microsoft.Web/sites/Read | Hämta egenskaperna för en Webbapp |
> | Åtgärd | microsoft.web/sites/recommendationhistory/read | Hämta webbhistorik appar rekommendation. |
> | Åtgärd | microsoft.web/sites/recommendations/disable/action | Inaktivera Web Apps rekommendationer. |
> | Åtgärd | Microsoft.Web/sites/recommendations/Read | Hämta lista över rekommendationer för webbapp. |
> | Åtgärd | microsoft.web/sites/recover/action | Återställa Web Apps. |
> | Åtgärd | Microsoft.Web/sites/resetSlotConfig/Action | Återställ webbappkonfigurationen |
> | Åtgärd | microsoft.web/sites/resourcehealthmetadata/read | Hämta Metadata för Web Apps – resurs hälsotillstånd. |
> | Åtgärd | Microsoft.Web/sites/restart/Action | Starta om en Webbapp |
> | Åtgärd | microsoft.web/sites/restore/read | Hämta Web Apps återställning. |
> | Åtgärd | microsoft.web/sites/restore/write | Återställa Web Apps. |
> | Åtgärd | microsoft.web/sites/restorefrombackupblob/action | Återställa Webbapp från säkerhetskopia av Blob. |
> | Åtgärd | microsoft.web/sites/restorefromdeletedwebapp/action | Återställa Webbappar från borttagna App. |
> | Åtgärd | microsoft.web/sites/restoresnapshot/action | Återställa Web Apps ögonblicksbilder. |
> | Åtgärd | microsoft.web/sites/siteextensions/delete | Ta bort Webbplatstillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/siteextensions/read | Hämta Webbplatstillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/siteextensions/write | Uppdatera Webbplatstillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/slots/analyzecustomhostname/read | Hämta Web Apps fack analysera anpassat värdnamn. |
> | Åtgärd | Microsoft.Web/sites/slots/applySlotConfig/Action | Använda web app platskonfigurationen från målplatsen till aktuell plats. |
> | Åtgärd | Microsoft.Web/sites/slots/backup/Action | Skapa ny Web App Slot säkerhetskopia. |
> | Åtgärd | microsoft.web/sites/slots/backup/read | Hämta säkerhetskopiering av Webbappar platser. |
> | Åtgärd | microsoft.web/sites/slots/backup/write | Uppdatera platser säkerhetskopiering av Webbappar. |
> | Åtgärd | microsoft.web/sites/slots/backups/action | Upptäck fack säkerhetskopierade Webbappar. |
> | Åtgärd | microsoft.web/sites/slots/backups/delete | Ta bort säkerhetskopierade Webbappar platser. |
> | Åtgärd | microsoft.web/sites/slots/backups/list/action | Lista över platser säkerhetskopierade Webbappar. |
> | Åtgärd | Microsoft.Web/sites/slots/backups/Read | Hämta egenskaperna för en webbappsplatser-säkerhetskopiering |
> | Åtgärd | microsoft.web/sites/slots/backups/restore/action | Återställ säkerhetskopierade Webbappar platser. |
> | Åtgärd | microsoft.web/sites/slots/config/delete | Ta bort Webbkonfiguration appar platser. |
> | Åtgärd | Microsoft.Web/sites/slots/config/list/Action | Lista över Web App Slot security känsliga inställningar, till exempel publicering av autentiseringsuppgifter, appinställningar och anslutningssträngar |
> | Åtgärd | Microsoft.Web/sites/slots/config/Read | Hämta inställningar för Web App Slot |
> | Åtgärd | Microsoft.Web/sites/slots/config/Write | Uppdatera inställningar för Web App Slot |
> | Åtgärd | microsoft.web/sites/slots/containerlogs/action | Hämta zippade Behållarloggarna för Web App facket. |
> | Åtgärd | microsoft.web/sites/slots/containerlogs/download/action | Hämta behållare webbloggar appar platser. |
> | Åtgärd | microsoft.web/sites/slots/continuouswebjobs/delete | Ta bort kontinuerlig Webbjobb för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/continuouswebjobs/read | Få kontinuerlig Webbjobb för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/continuouswebjobs/start/action | Starta kontinuerlig Webbjobb för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/continuouswebjobs/stop/action | Stoppa kontinuerlig Webbjobb för Web Apps-platser. |
> | Åtgärd | Microsoft.Web/sites/slots/Delete | Ta bort en befintlig plats för Web App |
> | Åtgärd | microsoft.web/sites/slots/deployments/delete | Ta bort Web Apps fack distributioner. |
> | Åtgärd | microsoft.web/sites/slots/deployments/log/read | Hämta Web Apps fack distributioner Log. |
> | Åtgärd | microsoft.web/sites/slots/deployments/read | Hämta Web Apps fack distributioner. |
> | Åtgärd | microsoft.web/sites/slots/deployments/write | Web Apps fack distributioner av uppdatering. |
> | Åtgärd | microsoft.web/sites/slots/detectors/read | Hämta Web Apps fack detektorerna. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Kör Web Apps fack diagnostik analys. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/analyses/read | Hämta Web Apps fack diagnostik analys. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Hämta Web Apps fack diagnostik för ASP.NET Core-app. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/autoheal/read | Hämta Web Apps fack diagnostik Autoheal. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/deployment/read | Hämta Web Apps fack diagnostik distribution. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/deployments/read | Hämta Web Apps fack diagnostik distributioner. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Köra Web Apps fack diagnostik detektor. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/detectors/read | Hämta Web Apps fack diagnostik detektor. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Hämta Web Apps fack diagnostik FREB analys. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/loganalyzer/read | Hämta Web Apps fack diagnostik Log Analyzer. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/read | Hämta Web Apps fack Diagnostics. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/runtimeavailability/read | Få Web Apps fack diagnostik Runtime tillgänglighet. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/servicehealth/read | Hämta Tjänstehälsa för Web Apps fack diagnostik. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/sitecpuanalysis/read | Hämta Web Apps fack diagnostik plats CPU analys. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/sitecrashes/read | Hämta Web Apps fack diagnostik plats kraschar. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/sitelatency/read | Hämta svarstid för Web Apps fack diagnostik webbplats. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Hämta Web Apps fack diagnostik plats Minnesanalyser. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Hämta Web Apps fack diagnostik omstart inställningen Webbplatsuppdatering. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/siterestartuserinitiated/read | Hämta Web Apps fack diagnostik plats omstart användarinitierad. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/siteswap/read | Hämta Web Apps fack diagnostik plats växling. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/threadcount/read | Hämta Trådantalet för Web Apps fack diagnostik. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Hämta Web Apps fack diagnostik Workeravailability. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/workerprocessrecycle/read | Hämta omarbetning av Web Apps fack diagnostik Worker processen. |
> | Åtgärd | microsoft.web/sites/slots/domainownershipidentifiers/read | Hämta Web Apps fack domän ägarskap identifierare. |
> | Åtgärd | microsoft.web/sites/slots/functions/listsecrets/action | Lista hemligheter Web Apps fack-funktioner. |
> | Åtgärd | microsoft.web/sites/slots/functions/read | Hämta Web Apps fack funktioner. |
> | Åtgärd | microsoft.web/sites/slots/hostnamebindings/delete | Ta bort Web Apps fack Hostname-bindningar. |
> | Åtgärd | microsoft.web/sites/slots/hostnamebindings/read | Hämta Web Apps fack Hostname-bindningar. |
> | Åtgärd | microsoft.web/sites/slots/hostnamebindings/write | Uppdatera webb Apps fack Hostname-bindningar. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnection/delete | Ta bort Hybridanslutningen för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnection/read | Hämta Hybridanslutning för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnection/write | Uppdatera Hybridanslutning för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/delete | Ta bort Web Apps fack Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnectionnamespaces/relays/write | Uppdatera webb Apps fack Hybrid anslutning namnområden reläer. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnectionrelays/read | Hämta Web Apps fack Hybrid anslutning reläer. |
> | Åtgärd | microsoft.web/sites/slots/instances/deployments/read | Hämta Web Apps fack instanser distributioner. |
> | Åtgärd | microsoft.web/sites/slots/instances/processes/delete | Ta bort Web Apps fack instanser processer. |
> | Åtgärd | microsoft.web/sites/slots/instances/processes/read | Hämta Web Apps fack instanser processer. |
> | Åtgärd | microsoft.web/sites/slots/instances/read | Hämta Web Apps fack instanser. |
> | Åtgärd | microsoft.web/sites/slots/metricdefinitions/read | Hämta Måttdefinitioner för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/metrics/read | Få mätvärden från webben appar platser. |
> | Åtgärd | microsoft.web/sites/slots/migratemysql/read | Hämta Web Apps fack migrera MySql. |
> | Åtgärd | microsoft.web/sites/slots/networktrace/action | Nätverket Trace Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/networktraces/operationresults/read | Hämta Åtgärdsresultat för Web Apps fack nätverk spårningen. |
> | Åtgärd | microsoft.web/sites/slots/newpassword/action | Nytt lösenord Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/operationresults/read | Hämta Åtgärdsresultat för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/operations/read | Hämta åtgärder för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/perfcounters/read | Få prestandaräknare för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/phplogging/read | Hämta Web Apps fack Phplogging. |
> | Åtgärd | microsoft.web/sites/slots/premieraddons/delete | Ta bort Web Apps fack Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/premieraddons/read | Hämta Web Apps fack Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/premieraddons/write | Uppdatera webb Apps fack Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/processes/read | Hämta Web Apps fack processer. |
> | Åtgärd | microsoft.web/sites/slots/publiccertificates/delete | Ta bort Web Apps fack offentliga certifikat. |
> | Åtgärd | microsoft.web/sites/slots/publiccertificates/read | Hämta offentliga certifikat för Web Apps-fack. |
> | Åtgärd | microsoft.web/sites/slots/publiccertificates/write | Skapa eller uppdatera Web Apps fack offentliga certifikat. |
> | Åtgärd | Microsoft.Web/sites/slots/publish/Action | Publicera ett fack för Web App |
> | Åtgärd | Microsoft.Web/sites/slots/publishxml/Action | Hämta Publicera profil-xml för Web App Slot |
> | Åtgärd | Microsoft.Web/sites/slots/Read | Hämta egenskaperna för en Web App-distributionsfack |
> | Åtgärd | microsoft.web/sites/slots/recover/action | Återställa Web Apps-platser. |
> | Åtgärd | Microsoft.Web/sites/slots/resetSlotConfig/Action | Platskonfigurationen för återställning av web app |
> | Åtgärd | microsoft.web/sites/slots/resourcehealthmetadata/read | Hämta Metadata för Web Apps fack Resource Health. |
> | Åtgärd | Microsoft.Web/sites/slots/restart/Action | Starta om ett fack för Web App |
> | Åtgärd | microsoft.web/sites/slots/restore/read | Hämta Web Apps fack återställning. |
> | Åtgärd | microsoft.web/sites/slots/restore/write | Återställa Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/restorefrombackupblob/action | Återställa Web Apps plats från säkerhetskopia av Blob. |
> | Åtgärd | microsoft.web/sites/slots/restorefromdeletedwebapp/action | Återställa Webbappsplatser från borttagna App. |
> | Åtgärd | microsoft.web/sites/slots/restoresnapshot/action | Återställa Web Apps fack ögonblicksbilder. |
> | Åtgärd | microsoft.web/sites/slots/siteextensions/delete | Ta bort Webbplatstillägg för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/siteextensions/read | Hämta Webbplatstillägg för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/siteextensions/write | Uppdatera Webbplatstillägg för Web Apps platser. |
> | Åtgärd | Microsoft.Web/sites/slots/slotsdiffs/Action | Hämta konfigurationsavvikelser mellan webbapp och platser |
> | Åtgärd | Microsoft.Web/sites/slots/slotsswap/Action | Växla distributionsfack för Web App |
> | Åtgärd | microsoft.web/sites/slots/snapshots/read | Hämta Web Apps fack ögonblicksbilder. |
> | Åtgärd | Microsoft.Web/sites/slots/sourcecontrols/Delete | Ta bort konfigurationsinställningar för Web App Slot käll-kontroll |
> | Åtgärd | Microsoft.Web/sites/slots/sourcecontrols/Read | Hämta Web App Slot källkontroll konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/sites/slots/sourcecontrols/Write | Uppdatera konfigurationsinställningar för Web App Slot käll-kontroll |
> | Åtgärd | Microsoft.Web/sites/slots/start/Action | Starta ett fack för Web App |
> | Åtgärd | Microsoft.Web/sites/slots/stop/Action | Stoppa ett fack för Web App |
> | Åtgärd | microsoft.web/sites/slots/sync/action | Synkronisera Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/triggeredwebjobs/delete | Ta bort utlösta WebJobs för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/triggeredwebjobs/read | Hämta utlösta WebJobs för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/triggeredwebjobs/run/action | Kör utlösta WebJobs för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/usages/read | Hämta Web Apps fack användningar. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/delete | Ta bort virtuella nätverksanslutningar för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Uppdatera webb Apps fack virtuella anslutningar Nätverksgatewayer. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/read | Hämta virtuella nätverksanslutningar för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/write | Uppdatera virtuella nätverksanslutningar för Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/webjobs/read | Hämta WebJobs för Web Apps platser. |
> | Åtgärd | Microsoft.Web/sites/slots/Write | Skapa en ny plats för Web App eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/sites/slotsdiffs/Action | Hämta konfigurationsavvikelser mellan webbapp och platser |
> | Åtgärd | Microsoft.Web/sites/slotsswap/Action | Växla distributionsfack för Web App |
> | Åtgärd | microsoft.web/sites/snapshots/read | Hämta ögonblicksbilder för Web Apps. |
> | Åtgärd | Microsoft.Web/sites/sourcecontrols/Delete | Ta bort konfigurationsinställningar för Web App käll-kontroll |
> | Åtgärd | Microsoft.Web/sites/sourcecontrols/Read | Hämta Web App källkontroll konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/sites/sourcecontrols/Write | Uppdatera konfigurationsinställningar för Web App käll-kontroll |
> | Åtgärd | Microsoft.Web/sites/start/Action | Starta en Webbapp |
> | Åtgärd | Microsoft.Web/sites/stop/Action | Stoppa en Webbapp |
> | Åtgärd | microsoft.web/sites/sync/action | Synkronisera Web Apps. |
> | Åtgärd | microsoft.web/sites/syncfunctiontriggers/action | Synkronisera Function-utlösare för Web Apps. |
> | Åtgärd | microsoft.web/sites/triggeredwebjobs/delete | Ta bort utlösta WebJobs för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/triggeredwebjobs/History/Read | Hämta appar utlösta WebJobs webbhistorik. |
> | Åtgärd | microsoft.web/sites/triggeredwebjobs/read | Hämta utlösta WebJobs för Web Apps. |
> | Åtgärd | microsoft.web/sites/triggeredwebjobs/run/action | Kör utlösta WebJobs för Web Apps. |
> | Åtgärd | microsoft.web/sites/usages/read | Hämta Web Apps användningar. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/delete | Ta bort virtuella nätverksanslutningar för Web Apps. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/gateways/read | Hämta Web Apps virtuella anslutningar Nätverksgatewayerna. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/gateways/write | Uppdatera Web Apps Vnet-anslutningar Gateways. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/read | Hämta virtuella nätverksanslutningar för Web Apps. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/write | Uppdatera virtuella nätverksanslutningar för Web Apps. |
> | Åtgärd | microsoft.web/sites/webjobs/read | Hämta WebJobs för Web Apps. |
> | Åtgärd | Microsoft.Web/sites/Write | Skapa en ny Webbapp eller uppdatera en befintlig |
> | Åtgärd | microsoft.web/skus/read | Hämta SKU: er. |
> | Åtgärd | microsoft.web/sourcecontrols/read | Hämta källan kontroller. |
> | Åtgärd | microsoft.web/sourcecontrols/write | Uppdatera källa kontroller. |
> | Åtgärd | microsoft.web/unregister/action | Avregistrera Microsoft.Web resource provider för prenumerationen. |
> | Åtgärd | microsoft.web/validate/action | Verifiera. |
> | Åtgärd | microsoft.web/verifyhostingenvironmentvnet/action | Kontrollera som är värd för miljön Vnet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.WorkloadMonitor/components/read | Hämtar komponenter för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/componentsSummary/read | Hämtar sammanfattning av komponenter |
> | Åtgärd | Microsoft.WorkloadMonitor/monitorInstances/read | Hämtar instanser av Övervakare för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/monitorInstancesSummary/read | Hämtar sammanfattning av övervakaren instanser |
> | Åtgärd | Microsoft.WorkloadMonitor/monitors/read | Hämtar Övervakare för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/monitors/write | Konfigurera Övervakare för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/notificationSettings/read | Hämtar meddelandeinställningar för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurera inställningar för meddelanden för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/operations/read | Hämtar åtgärderna som stöds |

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Inbyggda roller för Azure-resurser](built-in-roles.md)
