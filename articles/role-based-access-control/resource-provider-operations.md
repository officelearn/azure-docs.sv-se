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
ms.date: 02/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: db0bebb49007059cfe3b8b9b416c4a148c3756fc
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287340"
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
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Hämtar listan över mått som stöds för en viss tjänst.<br>För exempel extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för AD FS-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomainService.<br>Kör profil svarstid, TCP-anslutningar upprättas, Insights agenten privata byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Med en tjänst kan hämtar detta API information för mått.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomain-tjänsten.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
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
> | Åtgärd | Microsoft.ADHybridHealthService/reports/riskyIp/blobUri/action | Rapport för riskfyllda IP-adresser och returnerar en URI som pekar till den. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/riskyIp/blobUris/read | Hämtar alla rapport för riskfyllda IP URI: er för de senaste 7 dagarna. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/riskyIp/GetAllBlobUri/read | Hämtar alla blob-uri för den rapport för riskfyllda ip-download som begärdes för en viss tjänst under senaste 7 dagarna. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/riskyIp/GetBlobUri/read | Hämtar endast den aktuella begärda ladda ned rapporten för riskfyllda ip för en viss tjänst. |
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
> | Åtgärd | Microsoft.ADHybridHealthService/services/metricmetadata/read | Hämtar listan över mått som stöds för en viss tjänst.<br>För exempel extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för AD FS-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomainService.<br>Kör profil svarstid, TCP-anslutningar upprättas, Insights agenten privata byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Med en tjänst kan hämtar detta API medelvärdet för mått för en viss tjänst.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomain-tjänsten.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/read | Med en tjänst kan hämtar detta API information för mått.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomain-tjänsten.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Med en tjänst kan hämtar detta API aggregerade vyn för mått för en viss tjänst.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomain-tjänsten.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Lägg till eller uppdaterar övervakningskonfigurationen för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Hämtar övervakningsinställningar för en viss tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Lägg till eller uppdaterar övervakningsinställningar för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/premiumcheck/read | Detta API hämtar listan över alla integrerade tjänster för en premium-klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/read | Läser tjänstinstanser i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/details/read | Hämtar rapporten över upp 50 användare med felaktiga lösenord fel från de senaste 7 dagarna |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/action | Skapar en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Läser aviseringarna för en server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Under server registration anropas denna api för att hämta autentiseringsuppgifter för onboarding av nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | För en viss server hämtar detta API en lista med datatyper som laddas av servrarna och den senaste tid för varje överföring. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/delete | Tar bort en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Hämtar exportera synkroniseringsfel-information för en viss tjänst för synkronisering. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Med en tjänst kan hämtar detta API information för mått.<br>Detta API kan till exempel användas för att hämta information som rör: Extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Tokenbegäranden (Proxy), Token-förfrågningar per sekund etc. för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva trådar för LDAP, Kerberos-autentiseringar per sekund osv ATQ trådar totalt för ADDomain-tjänsten.<br>Kör profil svarstid, TCP-anslutningar som upprättats insikter agenten privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
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
> | Åtgärd | Microsoft.ApiManagement/service/apis/delete | Ta bort befintliga API: |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/delete | Ta bort befintliga diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/read | Hämta lista över diagnostik eller hämta information för diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/write | Lägg till ny diagnostik eller uppdatera befintlig diagnostisk information |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Tar bort befintlig bifogad fil |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/read | Få problemet bifogade filer eller hämtar API Management problemet information för bifogad fil |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/write | Lägg till api problemet bifogad fil |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/delete | Tar bort befintlig kommentar |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/read | Hämtar Probleminformation kommentarer eller hämtar API Management problemet kommentar |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/write | Lägg till api-ärendekommentar |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/delete | Tar bort befintlig problemet |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/read | Få problem som är associerade med API: et eller hämtar API Management Probleminformation |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/write | Lägg till api-problem eller uppdatera api-problem |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/delete | Ta bort befintliga API-åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/delete | Ta bort konfiguration från API-åtgärden principer |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/read | Hämta principer för API-åtgärden eller hämta information om principen för API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/write | Ange information om principen för API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/delete | Ta bort konfiguration från åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/read | Hämta information om konfigurationen av princip för åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/write | Ange information om principen för åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/read | Hämta listan över befintliga API-åtgärder eller hämta information om API-åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/delete | Ta bort associationen mellan befintlig tagg med befintliga åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/read | Hämta taggar som är associerade med information om åtgärden eller hämta tagg |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/write | Koppla befintlig tagg till befintliga åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/write | Skapa nya API-åtgärden eller uppdatera befintliga API-åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operationsByTags/read | Hämta lista över åtgärden/taggen associationer |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/delete | Ta bort konfiguration från API-principer |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/read | Hämta principer för API: et eller hämta konfigurationsinformation för princip för API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/write | Ange information om principen för API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/delete | Ta bort konfiguration från API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/read | Hämta information om konfigurationen av princip för API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/write | Ange information om principen för API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/products/read | Hämta alla produkter som API: et är en del av |
> | Åtgärd | Microsoft.ApiManagement/service/apis/read | Hämta lista över alla registrerade API: er eller hämta information om API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/delete | Tar bort alla versioner av API: et eller ta bort API-versionen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/read | Hämta versioner för en API eller hämta information om API-versionen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/write | Skapa nya API-versionen eller uppdatera befintliga API-versionen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/revisions/delete | Tar bort alla revisioner av ett API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/revisions/read | Hämta revisioner som hör till ett API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/delete | Tar bort befintlig Schema |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/document/read | Hämta dokument som beskriver schemat |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/document/write | Uppdatera dokumentet som beskriver schemat |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/read | Hämtar de scheman som används av API: et eller hämtar alla scheman för ett visst API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/write | Anger de scheman som används av API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Ta bort taggen beskrivning från API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Hämta taggar beskrivningar i omfånget för API: et eller hämta tagg beskrivning i omfånget för API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Skapa/ändra taggen beskrivning i omfattningen av API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/delete | Ta bort befintliga API/tagg-kopplingen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/read | Hämta alla API/taggen associationen för API: et eller hämta information om API/taggen association |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/write | Lägga till en ny API/tagg-koppling |
> | Åtgärd | Microsoft.ApiManagement/service/apis/write | Skapa nya API: et eller uppdatera befintliga API-information |
> | Åtgärd | Microsoft.ApiManagement/service/apisByTags/read | Hämta lista över API/taggen associationer |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/delete | Ta bort befintliga VersionSet |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/read | Hämta lista över version grupp entiteter eller hämtar information om en VersionSet |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Hämta lista över version entiteter |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/write | Skapa ny VersionSet eller uppdatera befintlig VersionSet information |
> | Åtgärd | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Uppdaterar de Microsoft.ApiManagement-resurser som körs i virtuella nätverk för att hämta uppdaterade nätverksinställningar. |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/delete | Ta bort befintliga auktoriseringsservern |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/read | Hämta listan över auktorisering servrar eller hämta information om auktoriseringsservern |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/write | Skapa en ny auktorisering eller uppdatera information för en befintlig server för auktorisering |
> | Åtgärd | Microsoft.ApiManagement/service/backends/delete | Ta bort befintlig serverdel |
> | Åtgärd | Microsoft.ApiManagement/service/backends/read | Hämta lista över serverdelar eller hämta information om serverdelen |
> | Åtgärd | Microsoft.ApiManagement/service/backends/reconnect/action | Skapa en begäran om återanslutningen |
> | Åtgärd | Microsoft.ApiManagement/service/backends/write | Lägg till en ny serverdel eller uppdatera befintlig backend-information |
> | Åtgärd | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiering API Management-tjänsten till den angivna behållaren i en användare tillhandahålls storage-konto |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/delete | Ta bort befintligt certifikat |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/read | Hämta listan över certifikat eller hämta information om certifikat |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/write | Lägg till nytt certifikat |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/contentItems/delete | Tar bort angivna innehållsobjektet. |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/contentItems/read | Returnerar listan över innehållsposter eller returnerar innehållsobjekt detaljer |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/contentItems/write | Skapar nytt innehåll objekt eller uppdaterar angivna innehållsobjekt |
> | Åtgärd | Microsoft.ApiManagement/service/contentTypes/read | Returnerar lista över typer av innehåll |
> | Åtgärd | Microsoft.ApiManagement/service/delete | Ta bort instansen av tjänsten API Management |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/delete | Ta bort befintliga diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/read | Hämta lista över diagnostik eller hämta information för diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/write | Lägg till ny diagnostik eller uppdatera befintlig diagnostisk information |
> | Åtgärd | Microsoft.ApiManagement/service/getssotoken/action | Hämtar SSO-token som kan användas för att logga in i API Management-tjänsten äldre portal som administratör |
> | Åtgärd | Microsoft.ApiManagement/service/groups/delete | Ta bort befintlig grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/read | Hämta lista över grupper eller hämtar information om en grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/delete | Ta bort befintliga användare från grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/read | Lista över gruppanvändare |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/write | Lägga till befintliga användare till befintlig grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/write | Skapa ny grupp eller uppdatera befintliga gruppinformation |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/delete | Ta bort befintliga identitetsprovider |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/read | Hämta lista över identitetsleverantörer eller hämta information för identitetsprovidern |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/write | Skapa en ny identitetsprovider eller uppdatera information med en befintlig identitetsprovider |
> | Åtgärd | Microsoft.ApiManagement/service/locations/networkstatus/read | Hämtar nätverksstatus för åtkomst av resurser där tjänsten är beroende på plats. |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/delete | Ta bort befintliga loggare |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/read | Hämta lista över tangenttryckningar eller hämta information om loggare |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/write | Lägg till ny loggare eller uppdatera befintlig logger-information |
> | Åtgärd | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort de regionala distributionerna av API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/networkstatus/read | Hämtar nätverksstatus för åtkomst av resurser där tjänsten är beroende av. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/action | Skickar meddelande till en angiven användare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/read | Hämtar alla meddelanden för API Management-utgivaren eller hämta API Management utgivare meddelandeinformation |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Tar bort befintlig e-postadress som är associerad med ett meddelande |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Hämta e-postmottagare som är associerade med API Management Publisher-meddelande |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Skapa nya e-postmottagare av meddelandet |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Tar bort användare som är kopplad till den meddelandemottagare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Hämta mottagaren användare som är associerad med meddelandet |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Lägga till användare till meddelandemottagare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/write | Skapa eller uppdatera API Management publisher-meddelande |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/delete | Ta bort befintliga OpenID Connect-Provider |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/read | Hämta lista över OpenID Connect-providern eller hämta information för OpenID Connect-Provider |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/write | Skapa en ny OpenID Connect-Provider eller uppdatera information för en befintlig OpenID Connect-Provider |
> | Åtgärd | Microsoft.ApiManagement/service/operationresults/read | Hämtar aktuell status för långvarig åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/policies/delete | Ta bort konfiguration från klientprinciperna |
> | Åtgärd | Microsoft.ApiManagement/service/policies/read | Hämta principer för klient- eller Get configuration principinformation för klient |
> | Åtgärd | Microsoft.ApiManagement/service/policies/write | Ange information om principen för klient |
> | Åtgärd | Microsoft.ApiManagement/service/policySnippets/read | Hämta alla principkodavsnitt |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/read | Hämta inställningar-registrering för portalen eller Get inloggningsinställningar för portalen eller skaffa delegeringsinställningar för portalen |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/write | Uppdatera registrera dig inställningar eller uppdatering registrera dig inställningar eller uppdatering-logga In inställningar eller uppdatering-logga In inställningar eller uppdatera Delegeringsinställningarna eller uppdatera Delegeringsinställningarna |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/delete | Ta bort befintliga API: et från befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/read | Hämta lista över API: er som lagts till i befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/write | Lägga till befintliga API till befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/delete | Ta bort befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/delete | Ta bort associationen mellan befintlig grupp för utvecklare med befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/read | Hämta lista över developer-grupper som är associerade med produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/write | Koppla befintlig grupp för utvecklare med befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/delete | Ta bort konfiguration från produkt-principer |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/read | Hämta principer för produkt eller hämta princip-konfigurationsinformationen för produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/write | Ange princip konfigurationsinformationen för produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/delete | Ta bort konfiguration från befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/read | Hämta konfiguration av befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/write | Ange konfiguration för befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/read | Hämta lista över produkter eller hämta information om produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/subscriptions/read | Hämta lista över prenumerationer för produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/delete | Ta bort associationen mellan befintlig tagg med befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/read | Hämta taggar som är associerade med information om produkten eller hämta tagg |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/write | Koppla befintlig tagg till befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/write | Skapa ny produkt eller uppdatera befintliga produktinformation |
> | Åtgärd | Microsoft.ApiManagement/service/productsByTags/read | Hämta lista över produkt/taggen associationer |
> | Åtgärd | Microsoft.ApiManagement/service/properties/delete | Tar bort befintlig egenskap |
> | Åtgärd | Microsoft.ApiManagement/service/properties/read | Hämtar lista över alla egenskaper eller hämtar information om den angivna egenskapen |
> | Åtgärd | Microsoft.ApiManagement/service/properties/write | Skapar en ny egenskap eller uppdaterar värdet för egenskapen |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/periods/read | Hämta räknarvärde kvot för perioden |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/periods/write | Ange kvot aktuella räknarvärdet |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/read | Hämta de värden för kvot |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/write | Ange kvot aktuella räknarvärdet |
> | Åtgärd | Microsoft.ApiManagement/service/read | Läsa metadata för en API Management-tjänstinstans |
> | Åtgärd | Microsoft.ApiManagement/service/reports/read | Hämta rapport sammanställs efter tidsperioder eller Get-rapport som sammanställs efter geografiskt område eller Get-rapport som aggregeras av utvecklare.<br>eller hämta rapport sammanställs av produkter.<br>eller hämta rapport aggregeras av API: er eller Get rapporten aggregeras av driftsupport- eller Get-rapport som aggregeras av prenumerationen.<br>eller Get-begäranden som rapporterar data |
> | Åtgärd | Microsoft.ApiManagement/service/restore/action | Återställa API Management-tjänsten från den angivna behållaren i en användare som tillhandahålls av storage-konto |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/delete | Ta bort prenumerationen. Den här åtgärden kan användas för att ta bort prenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/read | Hämta en lista över prenumerationer för produkten eller hämta information om produktprenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Återskapa primärnyckel för prenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Återskapa sekundärnyckel för prenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/write | Prenumerera på en befintlig användare till en befintlig produkt eller uppdatera information om en befintlig prenumeration. Den här åtgärden kan användas för att förnya prenumerationen |
> | Åtgärd | Microsoft.ApiManagement/service/tagResources/read | Hämta lista över taggar med associerade resurser |
> | Åtgärd | Microsoft.ApiManagement/service/tags/delete | Ta bort befintlig tagg |
> | Åtgärd | Microsoft.ApiManagement/service/tags/read | Hämta lista över taggar eller hämta information om taggen |
> | Åtgärd | Microsoft.ApiManagement/service/tags/write | Lägg till ny tagg eller uppdatera befintliga tagginformation |
> | Åtgärd | Microsoft.ApiManagement/service/templates/delete | Återställ standard API Management e-postmall |
> | Åtgärd | Microsoft.ApiManagement/service/templates/read | Hämtar alla e-postmallar eller hämtar API Management e-mallinformationen |
> | Åtgärd | Microsoft.ApiManagement/service/templates/write | Skapa eller uppdatera e-postmall som API Management eller uppdaterar API Management e-postmall |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/delete | Ta bort konfiguration för klienten |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/deploy/action | Kör en distributionsuppgift för att tillämpa ändringar från den angivna git-grenen till konfigurationen i databasen. |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/operationResults/read | Hämta lista över Åtgärdsresultat eller hämta resultatet av en viss åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/read | Hämta konfiguration för den eller Get-klient information åtkomstinformation |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Återskapa primär åtkomstnyckel |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Återskapa sekundära åtkomstnyckeln |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/save/action | Skapar commit med ögonblicksbilden av webbplatskonfigurationen till den angivna förgreningen i databasen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/syncState/read | Hämta status för den senaste synkroniseringen av git |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/validate/action | Verifierar ändringar från den angivna git-grenen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/write | Ange konfiguration för klient- eller Update klientinformation åtkomst information |
> | Åtgärd | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikat för en API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/users/action | Registrera en ny användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/attachments/delete | Tar bort en bilaga |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/attachments/read | Hämtar programmet bifogade filer eller hämtar bifogade filer |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/attachments/write | Lägg till bifogad fil till program |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/delete | Tar bort befintliga program |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/read | Hämta lista över alla användarprogram eller hämtar API Management-programinformation |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/write | Registrerar ett program till API Management eller uppdateringar programinformation |
> | Åtgärd | Microsoft.ApiManagement/service/users/confirmations/send/action | Skickar en bekräftelse |
> | Åtgärd | Microsoft.ApiManagement/service/users/delete | Ta bort användarkonto |
> | Åtgärd | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Generera en URL för enkel inloggning. URL: en kan användas för åtkomst till administratörsportalen |
> | Åtgärd | Microsoft.ApiManagement/service/users/groups/read | Hämta lista över användargrupper |
> | Åtgärd | Microsoft.ApiManagement/service/users/keys/read | Hämta lista över användarnycklar |
> | Åtgärd | Microsoft.ApiManagement/service/users/read | Hämta en lista över registrerade användare eller hämta information om kontot för en användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/subscriptions/read | Hämta lista över användarprenumerationer |
> | Åtgärd | Microsoft.ApiManagement/service/users/token/action | Hämta token åtkomsttoken för en användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/write | Registrera en ny användare eller uppdatera kontoinformation för en befintlig användare |
> | Åtgärd | Microsoft.ApiManagement/service/write | Skapa en ny instans av tjänsten API Management |
> | Åtgärd | Microsoft.ApiManagement/unregister/action | Avregistrera prenumerationen för Microsoft.ApiManagement resursprovidern |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/delete | Tar bort administratören från prenumerationen. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/operationstatuses/read | Hämtar status för administratörsåtgärder för prenumerationen. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/read | Läser in prenumerationens administratörer. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/write | Lägg till eller ta bort en administratör för en prenumeration. |
> | Åtgärd | Microsoft.Authorization/denyAssignments/delete | Ta bort ett tilldelningsnekande för det angivna reservationsomfånget. |
> | Åtgärd | Microsoft.Authorization/denyAssignments/read | Hämta information om ett tilldelningsnekande. |
> | Åtgärd | Microsoft.Authorization/denyAssignments/write | Skapa ett tilldelningsnekande för det definierade reservationsomfånget. |
> | Åtgärd | Microsoft.Authorization/elevateAccess/action | Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen |
> | Åtgärd | Microsoft.Authorization/locks/delete | Ta bort lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/locks/read | Hämtar lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/locks/write | Lägg till lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/operations/read | Hämta listan över åtgärder |
> | Åtgärd | Microsoft.Authorization/permissions/read | Listar alla behörigheter som anroparen har i ett givet omfång. |
> | Åtgärd | Microsoft.Authorization/policyAssignments/delete | Ta bort tilldelning av principer i det definierade området. |
> | Åtgärd | Microsoft.Authorization/policyAssignments/read | Hämta information om en tilldelning av principer. |
> | Åtgärd | Microsoft.Authorization/policyAssignments/write | Skapa en principtilldelning i det definierade området. |
> | Åtgärd | Microsoft.Authorization/policyDefinitions/delete | Ta bort en definition av principen. |
> | Åtgärd | Microsoft.Authorization/policyDefinitions/read | Hämta information om en principdefinition. |
> | Åtgärd | Microsoft.Authorization/policyDefinitions/write | Skapa en anpassad principdefinition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/delete | Ta bort en principuppsättningsdefinition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/read | Hämta information om en principuppsättningsdefinition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/write | Skapa en anpassad principuppsättningsdefinition. |
> | Åtgärd | Microsoft.Authorization/providerOperations/read | Hämta åtgärder för alla providrar som kan användas i rolldefinitioner. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/delete | Ta bort en rolltilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/read | Hämta information om en rolltilldelning. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/write | Skapa en rolltilldelning i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/delete | Ta bort den angivna anpassade rolldefinitionen. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/read | Hämta information om en rolldefinition. |
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
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Ta bort B2C-katalogresursen |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visa B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/write | Skapa eller uppdatera en B2C-katalogresurs |
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
> | Åtgärd | Microsoft.BingMaps/mapApis/Delete | Borttagningsåtgärd |
> | Åtgärd | Microsoft.BingMaps/mapApis/listSecrets/action | Lista hemligheterna |
> | Åtgärd | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Läs auktoriseringstoken för enkel inloggning för resursen |
> | Åtgärd | Microsoft.BingMaps/mapApis/Read | Läsåtgärd |
> | Åtgärd | Microsoft.BingMaps/mapApis/regenerateKey/action | Återskapa nyckeln |
> | Åtgärd | Microsoft.BingMaps/mapApis/Write | Skrivåtgärd |
> | Åtgärd | Microsoft.BingMaps/Operations/read | Beskrivning av åtgärden. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/assignmentOperations/read | Läs eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/delete | Ta bort eventuella skissartefakter |
> | Åtgärd | Microsoft.Blueprint/blueprintAssignments/read | Läs eventuella skissartefakter |
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
> | Åtgärd | Microsoft.Blueprint/register/action | Registrerar Azure Blueprint-resursprovider |

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
> | Åtgärd | Microsoft.Cache/checknameavailability/action | Kontrollerar om ett namn kan användas med nytt Redis Cache |
> | Åtgärd | Microsoft.Cache/locations/operationresults/read | Hämtar resultaten för en långvarig åtgärd för vilken sidhuvudet Location tidigare returnerats till klienten |
> | Åtgärd | Microsoft.Cache/operations/read | Listar åtgärderna som stöds av providern Microsoft.Cache. |
> | Åtgärd | Microsoft.Cache/redis/delete | Ta bort hela Redis-cache |
> | Åtgärd | Microsoft.Cache/redis/export/action | Exportera Redis-data till prefixade lagringsblobbar i angivet format |
> | Åtgärd | Microsoft.Cache/redis/firewallRules/delete | Ta bort IP-brandväggsregler för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/firewallRules/read | Hämta IP-brandväggsregler för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/firewallRules/write | Redigera IP-brandväggsregler för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/forceReboot/action | Tvinga omstart av en cache-instans, vilket kan medföra dataförlust. |
> | Åtgärd | Microsoft.Cache/redis/import/action | Importera data av ett angivet format från flera blobbar till Redis |
> | Åtgärd | Microsoft.Cache/redis/linkedservers/delete | Ta bort länkad server från Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/linkedservers/read | Hämta länkade servrar som är kopplade till Redis Cache. |
> | Åtgärd | Microsoft.Cache/redis/linkedservers/write | Lägg till länkad server till Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/listKeys/action | Visa värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista de senaste uppgraderingsmeddelandena för cacheklienten. |
> | Åtgärd | Microsoft.Cache/redis/metricDefinitions/read | Hämtar tillgängliga mått för ett Redis-cache |
> | Åtgärd | Microsoft.Cache/redis/patchSchedules/delete | Ta bort uppdateringsschemat för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/patchSchedules/read | Hämtar uppdateringsschemat för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/patchSchedules/write | Ändra uppdateringsschemat för Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/read | Visa inställningarna och konfigurationen för Redis-cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/redis/regenerateKey/action | Ändra värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/redis/start/action | Starta en cacheinstans. |
> | Åtgärd | Microsoft.Cache/redis/stop/action | Stoppa en cacheinstans. |
> | Åtgärd | Microsoft.Cache/redis/write | Ändra inställningarna och konfigurationen för Redis-cache i hanteringsportalen |
> | Åtgärd | Microsoft.Cache/register/action | Registrerar Microsoft.Cache-resursprovidern med en prenumeration |
> | Åtgärd | Microsoft.Cache/unregister/action | Avregistrerar Microsoft.Cache-resursprovidern med en prenumeration |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Capacity/appliedreservations/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/calculateprice/action | Beräkna alla reserverade pris |
> | Åtgärd | Microsoft.Capacity/catalogs/read | Läs katalog med Reservation |
> | Åtgärd | Microsoft.Capacity/checkoffers/action | Kontrollera alla prenumerationserbjudanden |
> | Åtgärd | Microsoft.Capacity/checkscopes/action | Kontrollera alla prenumerationer |
> | Åtgärd | Microsoft.Capacity/commercialreservationorders/read | Hämta Reservationsbeställningar som skapats i alla klienter |
> | Åtgärd | Microsoft.Capacity/operations/read | Läsa alla åtgärder |
> | Åtgärd | Microsoft.Capacity/register/action | Registrerar resursprovidern kapaciteten och gör det möjligt att skapa kapacitet resurser. |
> | Åtgärd | Microsoft.Capacity/reservationorders/action | Uppdatera alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/availablescopes/action | Hitta alla tillgängliga Scope |
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
> | Åtgärd | Microsoft.Cdn/register/action | Registrerar prenumerationen för CDN-resursprovidern och aktiverar funktionen för att skapa CDN-profiler. |
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
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/read | Visar distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/read | Hämta roll i domännamnets distributionsfack |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/roles/roleinstances/read | Hämta rollinstans för roll i domännamnets distributionsfack |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/state/read | Hämta status för distributionsfacket. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/state/write | Lägg till status för distributionsfacket. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/read | Hämta uppgraderingsdomän för distributionsfack i domännamn |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/upgradedomain/write | Uppdatera uppgraderingsdomän för distributionsfack i domännamn |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/deploymentslots/write | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/delete | Ta bort domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Läser domännamnstilläggens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/read | Returnerar domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/write | Lägg till domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Ta bort en ny intern belastningsutjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Läser åtgärdsstatusen för domännamnens interna lastbalanserare. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Hämtar de interna lastbalanserarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Skapa en ny intern belastningsutjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Läser åtgärdsstatusen för belastningsutjämnade slutpunktsuppsättningar för domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Hämta belastningsutjämnade slutpunktsuppsättningar. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Lägg till belastningsutjämnad slutpunktsuppsättning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/operationstatuses/read | Hämta åtgärdsstatus för domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/operationStatuses/read | Läser domännamnstilläggens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/read | Returnera resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Ta bort de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Läser åtgärdsstatus för tjänstcertifikat för domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Returnerar de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Lägg till eller ändra de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/abortMigration/action | Avbryter migrering av ett distributionsfack. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/commitMigration/action | Utför migrering av ett distributionsfack. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/delete | Tar bort en given distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Läser domännamnsplatsernas åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Förbereder migrering av ett distributionsfack. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/read | Visar distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Ta bort tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Läser åtgärdsstatus för rolltilläggsreferenser för domännamnsplatser. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Returnerar tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Lägg till eller ändra tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Hämta rollmåttsdefinitionen för domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/metrics/read | Hämta rollmått för domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/operationstatuses/read | Hämta åtgärdsstatus för domännamnsfackrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/read | Hämta distributionsplatsens roll. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Laddar ned fjärrskrivbordsanslutningsfil för rollinstansen på domännamnsfackrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Hämtar åtgärdsstatus för rollinstansen för domännamnsfackroll. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Hämta rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Återskapar rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Återställer avbildningen för rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uppgradera domän |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Hämta roll-SKU för distributionsfacket. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/write | Lägg till roll för distributionsfacket. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/start/action | Startar en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Ändrar distributionsplatsens status till stoppad. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Ändrar distributionsplatsens status till startad. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/stop/action | Göra uppehåll för distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Gå igenom uppgraderingsdomän. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/validateMigration/action | Verifierar migrering av ett distributionsfack. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/write | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/swap/action | Byta mellanlagringsplatsen till produktionsplatsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/write | Lägg till eller ändra resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/moveSubscriptionResources/action | Flytta alla klassiska resurser till en annan prenumeration. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystemFamilies/read | Listar de gästoperativsystem som är tillgängliga i Microsoft Azure, och listar även de operativsystemsversioner som är tillgängliga för respektive familj. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystems/read | Listar de versioner av gästoperativsystemen som för tillfället är tillgängliga i Microsoft Azure. |
> | Åtgärd | Microsoft.ClassicCompute/operations/read | Hämtar listan över åtgärder. |
> | Åtgärd | Microsoft.ClassicCompute/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicCompute/quotas/read | Hämta prenumerationens kvot. |
> | Åtgärd | Microsoft.ClassicCompute/register/action | Registrera för klassisk beräkning |
> | Åtgärd | Microsoft.ClassicCompute/resourceTypes/skus/read | Hämtar SKU-listan för de resurstyper som stöds. |
> | Åtgärd | Microsoft.ClassicCompute/validateSubscriptionMoveAvailability/action | Verifiera prenumerationens tillgänglighet för klassisk flyttåtgärd. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/asyncOperations/read | Hämta möjliga asynkrona åtgärder |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Bifogar en datadisk till den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/capture/action | Avbilda en virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/delete | Tar bort virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Tar bort en datadisk från den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Hämta diagnostikinställningar för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/disks/read | Hämtar listan över datadiskar |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Hämtar RDP-filen för den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Läser åtgärdsstatus för tillägg för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/read | Hämtar tillägget för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/write | Placerar tillägget för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Hämta måttdefinitionen för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till nätverksgränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/operationStatuses/read | Läser åtgärdsstatus för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/performMaintenance/action | Utför underhåll av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/read | Hämtar en lista över virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/redeploy/action | Distribuerar om den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/restart/action | Startar om virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Stänga av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/start/action | Startar den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/stop/action | Stoppar den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/write | Lägg till eller ändra virtuella datorer. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Hämta åtgärdsstatus för ExpressRoute-korsanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/delete | Ta bort peering med ExpressRoute-korsanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Hämta åtgärdsstatus för en peering med ExpressRoute-korsanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Hämta peering med ExpressRoute-korsanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Lägg till peering med ExpressRoute-korsanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Hämta ExpressRoute-korsanslutningar. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Lägg till ExpressRoute-korsanslutningar. |
> | Åtgärd | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Hämtar listan över enheter som stöds. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp. |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningarna för nätverkssäkerhetsgrupper |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för den här åtgärden kompletteras av resursprovidern för insights Nätverkssäkerhetsgrupperna. |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Tar bort säkerhetsregeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Läser åtgärdsstatus för säkerhetsroller för nätverkssäkerhetsgrupp. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Hämtar säkerhetsregeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Lägger till eller uppdaterar en säkerhetsregel. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/write | Lägger till en ny nätverkssäkerhetsgrupp. |
> | Åtgärd | Microsoft.ClassicNetwork/operations/read | Hämta åtgärder för klassiskt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/quotas/read | Hämta prenumerationens kvot. |
> | Åtgärd | Microsoft.ClassicNetwork/register/action | Registrera för klassiskt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/delete | Ta bort en reserverad IP. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/join/action | Anslut till en reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Läser åtgärdsstatus för reserverade IP:ar. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/read | Hämtar reserverade IP-adresser |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/write | Lägga till en ny reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/abortMigration/action | Avbryter migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Kontrollerar tillgängligheten för en given IP-adress i ett virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/commitMigration/action | Genomför migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/delete | Tar bort det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Upphäver återkallningen av ett klientcertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Läs de återkallade klientcertifikaten. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Återkallar ett klientcertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Tar bort klientcertifikatet för virtuell nätverksgateway. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/download/action | Hämtar certifikat efter tumavtryck. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Listar gatewaycertifikatspaketet för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Sök efter klientrotscertifikaten. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Laddar upp ett nytt klientrotscertifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/connect/action | Ansluter en plats-till-plats-gatewayanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/disconnect/action | Koppla från en plats-till-plats-gatewayanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/read | Hämtar listan över anslutningar. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/connections/test/action | Testar en plats-till-plats-gatewayanslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/delete | Tar bort den virtuella nätverks-gatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/action | Hämtar konfigurationsskriptet för enhet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/action | Hämtar gateway-diagnostiken. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/action | Hämtar kretstjänstnyckeln. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listar gateway-paketet för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Läser åtgärdsstatus för virtuella nätverksgatewayar. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/packages/read | Hämtar gateway-paketet för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/read | Hämtar de virtuella nätverks-gatewayerna. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Startar diagnostik för den virtuella nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Stoppar diagnostiken för den virtuella nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Lägger till en virtuell nätverks-gateway. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/join/action | Anslut till det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Läser åtgärdsstatus för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Peerar ett virtuellt nätverk med ett annat virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Förbereder migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/read | Hämtar det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Tar bort peering-proxyservern för det virtuella fjärrnätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Hämtar peering-proxyservern för det virtuella fjärrnätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Lägger till eller uppdaterar peering-proxyservern för det virtuella fjärrnätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Läser Åtgärdsstatus för virtuella undernät kopplade nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/validateMigration/action | Verifierar migreringen av ett virtuellt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Hämtar peering för det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/write | Lägg till ett nytt virtuellt nätverk. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicStorage/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Kontrollerar tillgängligheten för ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/checkStorageAccountAvailability/read | Hämta tillgänglighet för ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/disks/read | Returnerar lagringskontodisken. |
> | Åtgärd | Microsoft.ClassicStorage/images/operationstatuses/read | Hämtar åtgärdsstatus för avbildning. |
> | Åtgärd | Microsoft.ClassicStorage/images/read | Returnerar bilden. |
> | Åtgärd | Microsoft.ClassicStorage/operations/read | Hämtar åtgärder för klassisk lagring |
> | Åtgärd | Microsoft.ClassicStorage/osImages/read | Returnerar operativsystemsavbildningen. |
> | Åtgärd | Microsoft.ClassicStorage/osPlatformImages/read | Hämtar avbildningen av operativsystemets plattform. |
> | Åtgärd | Microsoft.ClassicStorage/publicImages/read | Hämtar den offentliga avbildningen av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/quotas/read | Hämta prenumerationens kvot. |
> | Åtgärd | Microsoft.ClassicStorage/register/action | Registrera för klassisk lagring |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/abortMigration/action | Avbryter migreringen av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/commitMigration/action | Utför migrering av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/delete | Ta bort lagringskontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/delete | Tar bort angiven lagringskontodisk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagringskontodisken. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/write | Lägger till en lagringskontodisk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/delete | Tar bort en viss lagringskontoavbildning. (Inaktuell. Använd ”Microsoft.ClassicStorage/storageAccounts/vmImages”) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Returnerar åtgärdsstatus för lagringskontoavbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagringskontoavbildning. (Inaktuell. Använd ”Microsoft.ClassicStorage/storageAccounts/vmImages”) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Tar bort en given operativsystemsavbildning för lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returnerar operativsystemsavbildningen för lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/write | Lägger till en angiven avbildning av lagringskontots operativsystem. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Förbereder migrering av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Återskapar befintliga åtkomstnycklar för lagringskontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/read | Hämta de tjänster som är tillgängliga. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/validateMigration/action | Verifierar migreringen av ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Tar bort en angiven avbildning av virtuell dator. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Hämtar åtgärdsstatus för en viss virtuell datoravbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Returnerar avbildningen av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Lägger till en angiven avbildning av virtuell dator. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/write | Lägger till ett nytt lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/vmImages/read | Listar avbildningar av virtuella datorer. |

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
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Hämta en ansikte lista faceListId, namn, userData och ansikten i listan över ansikte.
Lista ansikte listor faceListId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Skapa en tom ansikts-lista med användardefinierade faceListId, namn och en valfri userData. Upp till 64 ansikte tillåts listor
Uppdatera information för en ansikts-lista, inklusive namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/findsimilars/action | Den angivna förfrågat ansikte faceId att söka i liknande ansikten från en matris med faceId, en ansikts-lista eller en stor ansikts-lista. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Dela upp kandidat ansikten i grupper baserat på likheter ansikte. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/identify/action | 1-till-många-identifiering för att hitta de närmaste matchningarna av specifika fråga person ansikte från en persongrupp eller stora persongrupp. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/delete | Ta bort en angiven stora ansikts-lista. Ansikts-relaterade bilder i listan över stora ansikte tas bort, för. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/delete | Ta bort ett ansikte från en stor ansikts-lista med angivna largeFaceListId och persisitedFaceId. Relaterade ansiktsbilden tas bort, för. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Hämta beständiga ansikte i stora ansikte lista med largeFaceListId och persistedFaceId.
Lista ansikten persistedFaceId och userData i en angiven stora ansikts-lista. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/write | Lägg till ett ansikte i en angiven stora ansikts-lista, upp till 1 000 000 ansikten.
Uppdatera ett angivna ansikte userData fält i en stor ansikte lista av dess persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Hämta en stor ansikte lista largeFaceListId, namn, userData.
Lista över stora ansikte visar information för largeFaceListId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Skicka en stor ansikte lista utbildning uppgift. Utbildning är ett viktigt steg som endast en tränad stora ansikts-lista kan användas av |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | Kontrollera stora ansikte lista utbildning status slutförda eller fortfarande pågår. LargeFaceList utbildning är en asynkron åtgärd |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Skapa en tom stora ansikts-lista med användardefinierade largeFaceListId, namn och en valfri userData.
Uppdatera information för en stor ansikts-lista, inklusive namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/delete | Ta bort en befintlig persongrupp för stora med angivna personGroupId. Beständiga data i den här stora person gruppen tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Skapa en ny person i en angiven stora person-grupp. Om du vill lägga till ansiktsigenkänning i den här personen Ring |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/delete | Ta bort en befintlig person från en stor persongrupp. Alla lagrade personliga data och ansikts-avbildningar i posten person kommer att tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Ta bort ett ansikte från en person i en stor persongrupp. Ansikts-data och avbildning som är relaterade till den här ansikts-posten tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Hämta information om personen ansikte. Beständiga person ansiktet anges av dess largePersonGroupId, personId och persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/write | Lägg till en ansiktsbilden till en person i en persongrupp med stora ansiktsidentifiering eller verifieringen. Utan avbildningen av
Uppdatera en person beständiga ansiktes userData fält. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Hämta ett namn och userData och beständiga faceIds som representerar ansiktsbilden registrerade person.
Lista alla personer informationen i den angivna stora persongrupp, inklusive personId, namn, userData och persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Uppdatera namn eller userData för en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/read | Hämta information för stora person grupper, inklusive dess namn och userData. Den här API: et returnerar stora person gruppinformation
Lista alla befintliga stora persongruppers largePesonGroupId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Skicka en stor person grupp utbildning uppgift. Utbildning är ett viktigt steg att endast en utbildad stora persongrupp kan användas |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/training/read | För att kontrollera stora person utbildning Gruppstatus slutförda eller fortfarande pågår. LargePersonGroup utbildning är en asynkron åtgärd |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/write | Skapa en ny persongrupp för stora med användardefinierade largePersonGroupId, namn och valfri userData.
Uppdatera en befintlig stora person gruppens namn och userData. Egenskaperna bibehålla om de inte är i begärandetexten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Ta bort en befintlig persongrupp med angivna personGroupId. Beständiga data i den här personen gruppen tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Skapa en ny person i en angiven person-grupp. Om du vill lägga till ansiktsigenkänning i den här personen Ring |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Ta bort en befintlig person från en persongrupp. Alla lagrade personliga data och ansikts-avbildningar i posten person kommer att tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Ta bort ett ansikte från en person i en persongrupp. Ansikts-data och avbildning som är relaterade till den här ansikts-posten tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Hämta information om personen ansikte. Beständiga person ansiktet anges av dess personGroupId, personId och persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Lägg till en ansiktsbilden till en person i en persongrupp med ansiktsidentifiering eller verifieringen. Utan avbildningen av flera
Uppdatera en person beständiga ansiktes userData fält. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Hämta ett namn och userData och beständiga faceIds som representerar ansiktsbilden registrerade person.
Visa information om alla personer i gruppen angiven person, inklusive personId, namn, userData och persistedFaceIds av registrerad. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Uppdatera namn eller userData för en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Hämta person gruppnamn och userData. Hämta person information under den här personGroup
Visa persongrupper pesonGroupId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Skicka en person grupp utbildning uppgift. Utbildning är ett viktigt steg som endast en utbildad persongrupp kan användas av |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | För att kontrollera person utbildning Gruppstatus slutförda eller fortfarande pågår. PersonGroup utbildning är en asynkron åtgärd som utlöste |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Skapa en ny persongrupp med angivna personGroupId, namn och användaren userData.
Uppdatera en befintlig persongrupp namn och userData. Egenskaperna bibehålla om de inte är i begärandetexten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/verify/action | Kontrollera om två ansikten tillhör samma person eller om ett ansikte tillhör en person. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/listKeys/action | Visa nyckellista |
> | DataAction | Microsoft.CognitiveServices/accounts/LUIS/predict/action | Hämtar förutsägelsen publicerade slutpunkten för den givna frågan. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/read | Läser API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/regenerateKey/action | Återskapar nyckel |
> | Åtgärd | Microsoft.CognitiveServices/accounts/skus/read | Läser tillgängliga SKU:er för en befintlig resurs. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/entities/action | API: et returnerar en lista med kända enheter och allmänna med namnet entiteter (\"Person\", \"plats\", \"organisation\" osv) i ett visst dokument. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/keyphrases/action | API:t returnerar en lista med strängar som anger samtalsämnen i den inmatade texten. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/languages/action | API:t returnerar det identifierade språket och en poäng mellan 0 och 1. 1 poäng visar att korrekt språk har identifierats med 100 procents säkerhet. 120 språk stöds totalt. |
> | DataAction | Microsoft.CognitiveServices/accounts/TextAnalytics/sentiment/action | API:t returnerar en poäng mellan 0 och 1.<br>Resultat nära 1 visar positiv attityd medan resultat nära 0 indikerar negativ attityd.<br>Ett resultat på 0,5 anger bristen på sentiment (t.ex. en instruktion factoid). |
> | Åtgärd | Microsoft.CognitiveServices/accounts/usages/read | Hämta kvotanvändning för en befintlig resurs. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/write | Skriver API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Läser tillgängliga SKU: er för en prenumeration. |
> | Åtgärd | Microsoft.CognitiveServices/Operations/read | Visa alla tillgängliga åtgärder i lista |
> | Åtgärd | Microsoft.CognitiveServices/register/action | Registrerar prenumerationen med Cognitive Services |

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
> | Åtgärd | Microsoft.Compute/availabilitySets/vmSizes/read | Visar en lista över tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i tillgänglighetsuppsättningen |
> | Åtgärd | Microsoft.Compute/availabilitySets/write | Skapar en ny tillgänglighetsuppsättning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/disks/beginGetAccess/action | Hämta diskens SAS-URI för blobåtkomst |
> | Åtgärd | Microsoft.Compute/disks/delete | Tar bort disken |
> | Åtgärd | Microsoft.Compute/disks/endGetAccess/action | Återkalla diskens SAS-URI |
> | Åtgärd | Microsoft.Compute/disks/read | Hämta egenskaperna för en disk |
> | Åtgärd | Microsoft.Compute/disks/write | Skapar en ny disk eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/galleries/delete | Tar bort galleriet |
> | Åtgärd | Microsoft.Compute/galleries/images/delete | Tar bort galleriavbildningen |
> | Åtgärd | Microsoft.Compute/galleries/images/read | Hämtar egenskaperna för galleriavbildning |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/delete | Tar bort galleriavbildningsversionen |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/read | Hämtar egenskaperna för galleriavbildningsversion |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/write | Skapar en ny galleriavbildningsversion eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/galleries/images/write | Skapar en ny galleriavbildning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/galleries/read | Hämtar egenskaperna för galleri |
> | Åtgärd | Microsoft.Compute/galleries/write | Skapar ett nytt galleri eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.Compute/images/delete | Avbildningen tas bort |
> | Åtgärd | Microsoft.Compute/images/read | Hämta egenskaperna för avbildningen |
> | Åtgärd | Microsoft.Compute/images/write | Skapar en ny avbildning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/locations/capsOperations/read | Hämtar status för en asynkron Caps-åtgärd |
> | Åtgärd | Microsoft.Compute/locations/diskOperations/read | Hämtar status för en asynkron diskåtgärd |
> | Åtgärd | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Skapa loggar som visar det totala antalet förfrågningar per tidsintervall som hjälp vid begränsningsdiagnostik. |
> | Åtgärd | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Skapa loggar som visar aggregeringar av begränsade förfrågningar grupperade efter ResourceName, OperationName eller den tillämpade begränsningsprincipen. |
> | Åtgärd | Microsoft.Compute/locations/operations/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/read | Hämta egenskaperna för ett plattformsavbildningserbjudande |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Hämta egenskaperna för en plattformsavbildnings-SKU |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Hämta egenskaperna för en plattformsavbildningsversion |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/types/read | Hämta egenskaperna för en VMExtension-typ |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/types/versions/read | Hämta egenskaperna för en VMExtension-version |
> | Åtgärd | Microsoft.Compute/locations/publishers/read | Hämta egenskaperna för en utgivare |
> | Åtgärd | Microsoft.Compute/locations/runCommands/read | Listar tillgängliga körningskommandon på platsen |
> | Åtgärd | Microsoft.Compute/locations/usages/read | Hämtar gränser för antalet tjänster och aktuell användning för prenumerationens beräkningsresurser på en plats |
> | Åtgärd | Microsoft.Compute/locations/vmSizes/read | Visar en lista över tillgängliga storlekar för virtuella datorer på en plats |
> | Åtgärd | Microsoft.Compute/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft.Compute-resursprovidern |
> | Åtgärd | Microsoft.Compute/register/action | Registrerar prenumeration med Microsoft.Compute-resursprovidern |
> | Åtgärd | Microsoft.Compute/restorePointCollections/delete | Tar bort samlingen med återställningspunkter och återställningspunkter den innehåller |
> | Åtgärd | Microsoft.Compute/restorePointCollections/read | Hämta egenskaperna för en samling med återställningspunkter |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/delete | Tar bort återställningspunkten |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/read | Hämta egenskaperna för en återställningspunkt |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Hämta egenskaperna för en återställningspunkt tillsammans med blob-SAS-URI:er |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/write | Skapar en ny återställningspunkt |
> | Åtgärd | Microsoft.Compute/restorePointCollections/write | Skapar en ny samling med återställningspunkter eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/sharedVMImages/delete | Tar bort SharedVMImage |
> | Åtgärd | Microsoft.Compute/sharedVMImages/read | Hämta egenskaperna för en SharedVMImage |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/delete | Ta bort en SharedVMImageVersion |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/read | Hämta egenskaperna för en SharedVMImageVersion |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/replicate/action | Replikera en SharedVMImageVersion till målregioner |
> | Åtgärd | Microsoft.Compute/sharedVMImages/versions/write | Skapa en ny SharedVMImageVersion eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Compute/sharedVMImages/write | Skapar en ny SharedVMImage eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/skus/read | Hämtar listan över tillgängliga Microsoft.Compute-SKU:er för prenumerationen |
> | Åtgärd | Microsoft.Compute/snapshots/beginGetAccess/action | Hämta ögonblicksbilden SAS-URI för blobåtkomst |
> | Åtgärd | Microsoft.Compute/snapshots/delete | Ta bort en ögonblicksbild |
> | Åtgärd | Microsoft.Compute/snapshots/endGetAccess/action | Återkalla diskens SAS-URI ögonblicksbilden |
> | Åtgärd | Microsoft.Compute/snapshots/read | Hämta egenskaperna för en ögonblicksbild |
> | Åtgärd | Microsoft.Compute/snapshots/write | Skapa en ny ögonblicksbild eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Compute/virtualMachines/capture/action | Avbildar den virtuella datorn genom att kopiera hårddiskar, och genererar en mall som kan användas för att skapa liknande virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konverterar den virtuella datorns blobbaserade diskar till hanterade diskar |
> | Åtgärd | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurser |
> | Åtgärd | Microsoft.Compute/virtualMachines/delete | Tar bort den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/delete | Tar bort tillägget för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/read | Hämta egenskaperna för ett tillägg för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/write | Skapar ett nytt tillägg för virtuell dator eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.Compute/virtualMachines/generalize/action | Ställer in statusen för den virtuella datorn på Generaliserad och förbereder den virtuella datorn för avbildning |
> | Åtgärd | Microsoft.Compute/virtualMachines/instanceView/read | Hämtar detaljerad körningsstatus för den virtuella datorn och dess resurser |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som vanlig användare |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med behörighet som Windows-administratör eller Linux-rotanvändare |
> | Åtgärd | Microsoft.Compute/virtualMachines/performMaintenance/action | Utför underhållsåtgärd på den virtuella datorn. |
> | Åtgärd | Microsoft.Compute/virtualMachines/powerOff/action | Stänger av den virtuella datorn. Observera att den virtuella datorn fortsätter att debiteras. |
> | Åtgärd | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/redeploy/action | Distribuerar om virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/reimage/action | Avbildningen av den virtuella dator som använder differentieringsdisk återställs. |
> | Åtgärd | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/runCommand/action | Kör ett fördefinierat skript på den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar som de virtuella datorerna kan uppdateras till |
> | Åtgärd | Microsoft.Compute/virtualMachines/write | Skapar en ny virtuell dator eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Stänger av och startar om beräkningsresurserna för VM-skalningsuppsättningens instanser  |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/delete | Tar bort VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/delete/action | Tar bort VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Hämta tillägg för VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Hämtar egenskaperna för ett tillägg till VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Skapar ett nytt tillägg för VM-skalningsuppsättningen eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Gå igenom plattformsuppdateringsdomänerna manuellt för en VM-skalningsuppsättning i Service Fabric för att slutföra den uppdatering som har fastnat |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/instanceView/read | Hämtar instansvyn för VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Uppdaterar manuellt instanser av den senaste modellen av VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Hämta egenskaper för alla nätverksgränssnitt för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Startar löpande uppgradering för att flytta alla instanser av skalningsuppsättningar för virtuell dator till den senast tillgängliga operativsystemversionen av plattformsavbildningen. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Hämtar historiken för operativsystemuppgraderingar för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Utför planerat underhåll på VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Stänger av VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Hämta egenskaper för alla offentliga IP-adresser för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/read | Hämta egenskaperna för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Distribuera om VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Skapar ny avbildning av VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Skapar nya avbildningar av alla diskar (operativsystemdisk och datadiskar) för instanserna för en skalningsuppsättning för virtuell dator  |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/restart/action | Startar om instanserna för VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Avbryter samlad uppgradering av en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Hämta senaste status för den samlade uppgraderingen för VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/scale/action | Kontrollera om en befintlig VM-skalningsuppsättning kan skala in/ut till det angivna antalet instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/skus/read | Visar en lista över giltiga SKU-koder för en befintlig VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/start/action | Startar VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Stänger av och frigör beräkningsresurserna för en virtuell dator i en skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Ta bort en viss virtuell dator i en skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Hämtar instansvyn för en virtuell dator i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Hämta egenskaper för offentliga IP-adressen som skapats med hjälp av Virtual Machine Scale Sets. Virtual Machine Scale Sets kan skapa högst en offentlig IP-adress per IP-konfiguration (privat IP) |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Hämta egenskaper för en eller alla IP-konfigurationer för ett nätverksgränssnitt som skapats med hjälp av Virtual Machine Scale Sets. IP-konfigurationer motsvarar privata IP-adresser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Hämta egenskaper för alla nätverksgränssnitt för en virtuell dator som har skapats med VM-skalningsuppsättningar |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Utför planerat underhåll på en virtuell datorinstans i en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Stänger av en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Hämtar egenskaperna för en virtuell dator i en skaluppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Distribuerar om en virtuell datorinstans i en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Skapar ny avbildning av virtuell datorinstans i en VM-skalningsuppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Skapa nya avbildningar av alla diskar (operativsystemdisk och datadiskar) för virtuell datorinstans i en skalningsuppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Startar om en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Kör ett förinställt skript på en virtuell datorinstans i en skalningsuppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Startar en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Uppdaterar egenskaperna för en virtuell dator i en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/write | Skapar en ny VM-skalningsuppsättning eller uppdaterar en befintlig |

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
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exekvera till en viss container. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Hämta loggar för en särskild container. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/delete | Tar bort den specifika containergruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för containergruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för containergruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för containergruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/read | Hämta alla behållargrupper. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/restart/action | Startar om en särskild containergrupp. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/start/action | Startar en särskild containergrupp. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/stop/action | Stoppar en särskild behållargrupp. Databearbetningen kommer att frigöra resurser och fakturering stoppas. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/write | Skapa eller uppdatera en särskild containergrupp. |
> | Åtgärd | Microsoft.ContainerInstance/locations/cachedImages/read | Hämtar de cachelagrade bilderna för prenumerationen i en region. |
> | Åtgärd | Microsoft.ContainerInstance/locations/capabilities/read | Hämta funktionerna för en viss region. |
> | Åtgärd | Microsoft.ContainerInstance/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft.ContainerInstance att det virtuella nätverket eller undernätet håller på att tas bort. |
> | Åtgärd | Microsoft.ContainerInstance/locations/usages/read | Hämta användning för en viss region. |
> | Åtgärd | Microsoft.ContainerInstance/register/action | Registrerar prenumerationen för containerinstansens resursprovider och gör det möjligt att skapa containergrupper. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontrollerar om namnet på behållarregistret är tillgängliga för användning. |
> | Åtgärd | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.ContainerRegistry att virtuella nätverket eller undernätet tas bort |
> | Åtgärd | Microsoft.ContainerRegistry/locations/operationResults/read | Hämtar en asynkron resultat |
> | Åtgärd | Microsoft.ContainerRegistry/operations/read | Visar en lista över alla tillgängliga Azure Container Registry REST API-åtgärder |
> | Åtgärd | Microsoft.ContainerRegistry/register/action | Registrerar prenumerationen för container registry-resursprovidern och gör möljligt behållarregister. |
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
> | Åtgärd | Microsoft.ContainerService/containerServices/delete | Tar bort en containertjänst |
> | Åtgärd | Microsoft.ContainerService/containerServices/read | Hämta en containertjänst |
> | Åtgärd | Microsoft.ContainerService/containerServices/write | Skapar en ny containertjänst eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/locations/operationresults/read | Hämtar status för resultatet för en asynkron åtgärd |
> | Åtgärd | Microsoft.ContainerService/locations/operations/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft.ContainerService/locations/orchestrators/read | Visar en lista över containerdistributioner som stöds |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Hämta en åtkomstprofil för hanterat kluster efter rollnamn med hjälp av listautentiseringsuppgift |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/read | Hämta en åtkomstprofil för hanterat kluster efter rollnamn |
> | Åtgärd | Microsoft.ContainerService/managedClusters/delete | Tar bort ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lista över clusterAdmin-autentiseringsuppgifter för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista över clusterUser-autentiseringsuppgifter för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/read | Hämta ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Återställning av AAD-profilen för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Återställa tjänstens huvudnamn profilen för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/upgradeprofiles/read | Hämtar klustrets uppgraderingsprofil |
> | Åtgärd | Microsoft.ContainerService/managedClusters/write | Skapar ett nytt hanterat kluster eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.ContainerService/openShiftClusters/delete | Ta bort ett Open Shift-kluster |
> | Åtgärd | Microsoft.ContainerService/openShiftClusters/read | Hämta ett Open Shift-kluster |
> | Åtgärd | Microsoft.ContainerService/openShiftClusters/write | Skapar ett nytt kluster för Open Shift eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/openShiftManagedClusters/delete | Ta bort ett hanterat kluster Open Shift |
> | Åtgärd | Microsoft.ContainerService/openShiftManagedClusters/read | Hämta ett hanterat kluster Open Shift |
> | Åtgärd | Microsoft.ContainerService/openShiftManagedClusters/write | Skapar ett nytt Open Shift hanterade kluster eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft.ContainerService-resursprovidern |
> | Åtgärd | Microsoft.ContainerService/register/action | Registrerar prenumeration på Microsoft.ContainerService-resursprovidern |
> | Åtgärd | Microsoft.ContainerService/unregister/action | Avregistrerar prenumeration på Microsoft.ComputeService-resursprovidern |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContentModerator/applications/delete | Borttagningsåtgärd |
> | Åtgärd | Microsoft.ContentModerator/applications/listSecrets/action | Lista hemligheter |
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
> | Åtgärd | Microsoft.CostManagement/dimensions/read | Lista alla dimensioner av ett omfång. |
> | Åtgärd | Microsoft.CostManagement/exports/action | Kör den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/delete | Ta bort den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/read | Lista över export av omfång. |
> | Åtgärd | Microsoft.CostManagement/exports/write | Skapa eller uppdatera den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/query/action | Fråga användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/query/read | Fråga användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/reports/action | Schema för rapporter om användningsdata efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/reports/read | Schema för rapporter om användningsdata efter ett omfång. |

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
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.DataBox/jobs/bookShipmentPickUp/action | Gör det möjligt att boka upphämtning för returleveranser. |
> | Åtgärd | Microsoft.DataBox/jobs/cancel/action | Annullerar en pågående beställning. |
> | Åtgärd | Microsoft.DataBox/jobs/delete | Ta bort order |
> | Åtgärd | Microsoft.DataBox/jobs/listCredentials/action | Visar lista med okrypterade autentiseringsuppgifter för beställningen. |
> | Åtgärd | Microsoft.DataBox/jobs/read | Visa eller hämta beställningar |
> | Åtgärd | Microsoft.DataBox/jobs/write | Skapa eller uppdatera order |
> | Åtgärd | Microsoft.DataBox/locations/availableSkus/action | Den här metoden returnerar listan över tillgängliga SKU:er. |
> | Åtgärd | Microsoft.DataBox/locations/operationResults/read | Lista eller hämta den Åtgärdsresultat |
> | Åtgärd | Microsoft.DataBox/locations/validateAddress/action | Verifierar leveransadressen och anger alternativa adresser om sådana finns. |
> | Åtgärd | Microsoft.DataBox/register/action | Registrera providern Microsoft.Databox |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Visar en lista över eller hämtar aviseringarna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/alerts/read | Visar en lista över eller hämtar aviseringarna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/delete | Tar bort bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Visar en lista över eller hämtar bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/read | Visar en lista över eller hämtar bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/write | Skapar eller uppdaterar bandbredd-scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/delete | Tar bort Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/action | Ladda ned uppdateringar på enhet |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/extendedInformation/action | Hämtar utökad resursinformation |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/action | ArmApiDesc_action_getExtendedInformation_dataBoxEdgeDevices |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/installUpdates/action | Installera uppdateringar på enhet |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/jobs/read | Visar en lista över eller hämtar jobb |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Visar en lista över eller hämtar nätverksinställningarna för enheten |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/delete | Tar bort order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Visar en lista över eller hämtar order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/read | Visar en lista över eller hämtar order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/write | Skapar eller uppdaterar order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Visar en lista över eller hämtar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Visar en lista över eller hämtar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/read | Visar en lista över eller hämtar Data Box Edge-enheter |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/delete | Tar bort rollerna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Hämtar rollerna eller visar en lista över |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/read | Hämtar rollerna eller visar en lista över |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Skapar eller uppdaterar rollerna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Söka efter uppdateringar |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Uppdatera säkerhetsinställningar |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Tar bort filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Visar en lista över eller hämtar filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Visar en lista över eller hämtar filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | ArmApiDesc_action_refresh_shares |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Skapar eller uppdaterar filresurser |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Tar bort autentiseringsuppgifter för lagringskonto |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Visar en lista över eller hämtar autentiseringsuppgifterna för lagringskontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Visar en lista över eller hämtar autentiseringsuppgifterna för lagringskontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Skapar eller uppdaterar autentiseringsuppgifterna för lagringskontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Tar bort utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Visar en lista över eller hämtar utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Visar en lista över eller hämtar utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Skapar eller uppdaterar utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Visar en lista över eller hämtar uppdateringen sammanfattning |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Ladda upp certifikat för enhetsregistrering |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Tar bort användare av den |
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
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Ställer in tillgängliga diagnostikinställningar för Databricks-arbetsytan |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggdefinitioner för Databricks-arbetsytan |
> | Åtgärd | Microsoft.Databricks/workspaces/read | Hämtar en lista över Databricks-arbetsytor. |
> | Åtgärd | Microsoft.Databricks/workspaces/write | Skapar en Databricks-arbetsyta. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataCatalog/catalogs/delete | Tar bort katalogen. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/read | Visa egenskaper för katalogen eller katalogerna under prenumeration eller resursgrupp. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/write | Skapar katalog eller uppdaterar taggar och egenskaper för katalogen. |
> | Åtgärd | Microsoft.DataCatalog/checkNameAvailability/action | Kontrollerar catalog namn är tillgängliga för klienten. |
> | Åtgärd | Microsoft.DataCatalog/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft.DataCatalog resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/register/action | Registrerar prenumerationen med Microsoft.DataCatalog-resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/unregister/action | Avregistrerar prenumeration från Microsoft.DataCatalog resursprovidern. |

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
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/delete | Tar bort dataflöde. |
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/read | Läser dataflöde. |
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/write | Skapa eller uppdatera dataflöde |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/delete | Tar bort alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/read | Läser alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/write | Skapar eller uppdaterar alla datauppsättningar. |
> | Åtgärd | Microsoft.DataFactory/factories/delete | Deletes Data Factory. |
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
> | Åtgärd | Microsoft.DataFactory/factories/triggerruns/read | Läser Trigger Runs. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/delete | Tar bort alla utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/read | Läser en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/start/action | Startar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/stop/action | Stoppar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/triggerruns/read | Läser Trigger Runs. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/write | Skapar eller uppdaterar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/write | Skapa eller uppdatera Data Factory |
> | Åtgärd | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfigurerar lagringsplatsen för fabriken. |
> | Åtgärd | Microsoft.DataFactory/locations/getDataPlaneAccess/action | Hämtar åtkomst till ADF dataplanen-tjänsten. |
> | Åtgärd | Microsoft.DataFactory/locations/getDataPlaneAccess/read | Läsningar åtkomst till ADF dataplanen-tjänsten. |
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
> | Åtgärd | Microsoft.DataMigration/services/checkStatus/action | Kontrollera om tjänsten har distribuerats och om den körs |
> | Åtgärd | Microsoft.DataMigration/services/delete | En resursgrupp och alla dess underordnade tas bort. |
> | Åtgärd | Microsoft.DataMigration/services/projects/accessArtifacts/action | Generera en URL som kan användas för GET- eller PUT-projektsartefakter |
> | Åtgärd | Microsoft.DataMigration/services/projects/delete | En resursgrupp och alla dess underordnade tas bort. |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/delete | En resursgrupp och alla dess underordnade tas bort. |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/read/action | Hämta en URL som kan användas för att läsa filens innehåll |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/readWrite/action | Hämta en URL som kan användas för att läsa eller skriva filens innehåll |
> | Åtgärd | Microsoft.DataMigration/services/projects/files/write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/services/projects/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/cancel/action | Avbryt uppgiften om den körs |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/delete | En resursgrupp och alla dess underordnade tas bort. |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/projects/tasks/write | Kör Azure Database Migration Service-uppgifter |
> | Åtgärd | Microsoft.DataMigration/services/projects/write | Kör Azure Database Migration Service-uppgifter |
> | Åtgärd | Microsoft.DataMigration/services/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/slots/delete | En resursgrupp och alla dess underordnade tas bort. |
> | Åtgärd | Microsoft.DataMigration/services/slots/read | Läs information om resurser |
> | Åtgärd | Microsoft.DataMigration/services/slots/write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/services/start/action | Starta DMS-tjänsten så att den kan behandla migreringar igen |
> | Åtgärd | Microsoft.DataMigration/services/stop/action | Stoppa DMS-tjänsten så att kostnaderna minimeras |
> | Åtgärd | Microsoft.DataMigration/services/write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/skus/read | Hämta en lista över SKU:er som stöds av DMS-resurser. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforMariaDB/locations/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMariaDB/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för MariaDB-servrar |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforMariaDB/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/recoverableServers/read | Returnera den återställningsbara MariaDB Server-informationen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforMySQL/locations/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMySQL/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforMySQL/servers/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforMySQL/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforMySQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för MySQL-servrar |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforMySQL/servers/queryTexts/action | Returnera texten till en lista över frågor |
> | Åtgärd | Microsoft.DBforMySQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforMySQL/servers/recoverableServers/read | Returnera den återställningsbara MySQL-Server-informationen |
> | Åtgärd | Microsoft.DBforMySQL/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforMySQL/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.DBforMySQL/servers/topQueryStatistics/read | Returnera listan över Frågestatistik för de viktigaste frågorna. |
> | Åtgärd | Microsoft.DBforMySQL/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforPostgreSQL/performanceTiers/read | Returnerar listan över prestandanivåer som är tillgängliga. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/read | Returnera lista över rådgivare |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Returnera listan med rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Se rekommendationer |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/configurations/read | Returnera lista över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Returnera listan över brandväggen för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för Postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Returnera texten i en fråga |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Returnera texten till en lista över frågor |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Returnera den återställningsbara PostgreSQL-Server-informationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Ändra den server threat principen för en viss server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Returnera listan över Frågestatistik för de viktigaste frågorna. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för regeln för angivna virtuella nätverket. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverksregel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Vänta statistik för en instans |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Devices/checkNameAvailability/Action | Kontrollera om IotHub-namn är tillgänglig |
> | Åtgärd | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Kontrollera om etablering tjänstnamnet är tillgänglig |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Skapa Verifieringskod |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifiera certifikat-resurs |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Ta bort resursen för IotHub-klient |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Devices/IotHubs/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/IotHubs/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Devices/operationresults/Read | Hämta åtgärdsresultat |
> | Åtgärd | Microsoft.Devices/operations/Read | Hämta alla ResourceProvider-åtgärder |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Skapa Verifieringskod |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verifiera certifikat-resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/Delete | Ta bort IotDps resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.DevSpaces/controllers/rotateAuthKey/action | Rotera autentiseringsnyckel för kontrollanten Azure Dev blanksteg och distribuera om tjänsterna |
> | Åtgärd | Microsoft.DevSpaces/controllers/write | Skapa eller uppdatera Azure Dev blanksteg Controller egenskaper |
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
> | Åtgärd | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporterar resursanvändningen lab till ett lagringskonto |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/read | Läsa formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/galleryImages/read | Läsa galleriavbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generera en URI för att ladda upp anpassade diskavbildningar till ett labb. |
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
> | Åtgärd | Microsoft.EventHub/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.EventHub/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell Använd CheckNameAvailability istället. |
> | Åtgärd | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över klustermått resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/clusters/read | Hämtar beskrivning av klusterresurs |
> | Åtgärd | Microsoft.EventHub/clusters/write | Hämtar beskrivning av klusterresurs |
> | Åtgärd | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Tar bort reglerna för virtuellt nätverk i EventHub-resursprovidern för det angivna virtuella nätverket |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/action | Uppdateringar Namespace-auktoriseringsregeln. Detta API är inaktuell. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace istället... Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Tar bort den haveriberedskapskonfiguration som är kopplat till namnområdet. Den här åtgärden kan bara anropas via det primära namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
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
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/delete | Ta bort IP-filterresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/read | Hämta IP-filterresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/write | Skapa IP-filterresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/read | Hämtar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/write | Uppdaterar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta listan över resursbeskrivningar för Namespace diagnostikinställningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta listan över resursbeskrivningar för Namespace diagnostikinställningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta lista över Namespace loggar resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Ta bort regelresurs för virtuellt nätverk |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Hämtar regelresurs för virtuellt nätverk |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Skapa regelresurs för virtuellt nätverk |
> | Åtgärd | Microsoft.EventHub/namespaces/write | Skapa en Namespace-resurs och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.EventHub/register/action | Registrerar prenumerationen för EventHub-resursprovidern och gör det möjligt att skapa EventHub-resurser |
> | Åtgärd | Microsoft.EventHub/sku/read | Hämta lista över SKU: N resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/sku/regions/read | Hämta lista över SkuRegions resursbeskrivningar |
> | Åtgärd | Microsoft.EventHub/unregister/action | Registrerar EventHub-resursprovidern |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Features/features/read | Hämtar en prenumerations funktioner. |
> | Åtgärd | Microsoft.Features/operations/read | Hämtar listan över åtgärder. |
> | Åtgärd | Microsoft.Features/providers/features/read | Hämtar en prenumerations funktion hos en given resursprovider. |
> | Åtgärd | Microsoft.Features/providers/features/register/action | Registrerar funktionen för en prenumerations hos en given resursprovider. |
> | Åtgärd | Microsoft.Features/providers/features/unregister/action | Avregistrerar funktionen för en prenumeration hos en viss resursprovider. |
> | Åtgärd | Microsoft.Features/register/action | Registrerar funktionen för en prenumeration. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/read | Hämta gäst configuration tilldelning. |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/reports/read | Konfigurationsrapport för tilldelning på Get gäst. |
> | Åtgärd | Microsoft.GuestConfiguration/guestConfigurationAssignments/write | Skapa ny tilldelning för gäst-konfiguration. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.HDInsight/clusters/applications/delete | Ta bort program för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/applications/read | Hämta program för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/applications/write | Skapa eller uppdatera program för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/changerdpsetting/action | Ändra RDP-inställningen för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/action | Uppdateringskonfigurationen för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/read | Hämta HDInsight klusterkonfigurationer |
> | Åtgärd | Microsoft.HDInsight/clusters/delete | Ta bort ett HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för resursen HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/read | Få information om HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/roles/resize/action | Ändra storlek på ett HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/write | Skapa eller uppdatera HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/locations/capabilities/read | Hämta prenumerationens funktioner |
> | Åtgärd | Microsoft.HDInsight/locations/checkNameAvailability/read | Kontrollera namntillgänglighet |

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
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivitetsloggavisering har utlösts |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Delete | Ta bort en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Read | Läs en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Write | Skapa eller uppdatera en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Activated/Action | Klassisk måttavisering har aktiverats |
> | Åtgärd | Microsoft.Insights/AlertRules/Delete | Ta bort en klassisk måttavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Incidents/Read | Läs en klassisk måttaviseringsincident |
> | Åtgärd | Microsoft.Insights/AlertRules/Read | Läs en klassisk måttavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Resolved/Action | Klassisk måttavisering har lösts |
> | Åtgärd | Microsoft.Insights/AlertRules/Throttled/Action | Regel för klassisk måttavisering har begränsats |
> | Åtgärd | Microsoft.Insights/AlertRules/Write | Skapa eller uppdatera en klassisk måttavisering |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Delete | Ta bort en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Läs en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Write | Skapa eller uppdatera en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Läs loggdefinitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Read | Läs en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoskalning nedåt har initierats |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Autoskalning nedåt har slutförts |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoskalning uppåt har initierats |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Autoskalning uppåt har slutförts |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Write | Skapa eller uppdatera en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/Baseline/Read | Läs en baslinje för mått (förhandsversion) |
> | Åtgärd | Microsoft.Insights/CalculateBaseline/Read | Beräkna baslinje för måttvärden (förhandsversion) |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Delete | Tar bort ett Application Insights Analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Read | Läser ett Application Insights Analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Write | Skriver ett Application Insights Analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Action | Åtgärd för Application Insights Analytics-tabell |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Delete | Tar bort ett Application Insights Analytics-tabellschema |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Read | Läser ett Application Insights Analytics-tabellschema |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Write | Skriver ett Application Insights Analytics-tabellschema |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Delete | Tar bort en Application Insights-anteckning |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Read | Läser en Application Insights-anteckning |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Write | Skriver en Application Insights-anteckning |
> | Åtgärd | Microsoft.Insights/Components/Api/Read | Läser API för Application Insights-komponentdata |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Action | Skapar en API-nyckel för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Delete | Tar bort en API-nyckel för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Read | Läser en API-nyckel för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/BillingPlanForComponent/Read | Läser ett faktureringsavtal för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Läser aktuella faktureringsfunktioner för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Skriver aktuella faktureringsfunktioner för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Läser en standardkonfiguration av ALM-integrering för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/Delete | Tar bort konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Events/Read | Hämta loggar från Application Insights med OData-frågeformat |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Action | Åtgärd för exportinställningar i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Delete | Tar bort Application Insights-exportinställningar |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Read | Läser Application Insights-exportinställningar |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Write | Skriver Application Insights-exportinställningar |
> | Åtgärd | Microsoft.Insights/Components/ExtendQueries/Read | Läser utökade frågeresultat för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Delete | Tar bort en Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Read | Läser en Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Write | Skriver en Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/FeatureCapabilities/Read | Läser funktioner för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Läser tillgängliga faktureringsfunktioner för Application Insight-komponent |
> | Åtgärd | Microsoft.Insights/Components/GetToken/Read | Läser en token för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/MetricDefinitions/Read | Läser definitioner av mätvärden för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Metrics/Read | Läser mätvärden för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Move/Action | Flytta en Application Insights-komponent till en annan resursgrupp eller prenumeration |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Tar bort ett personligt Application Insights Analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Read | Läser ett personligt Application Insights Analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Write | Skriver ett personligt Application Insights Analytics-objekt |
> | Åtgärd | Microsoft.Insights/Components/MyFavorites/Read | Läser en personlig Application Insights-favorit |
> | Åtgärd | Microsoft.Insights/Components/Operations/Read | Få status på tidskrävande åtgärder i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Read | Läser ett prisavtal för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Write | Skriver ett prisavtal för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Läser konfiguration av proaktiv identifiering för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Skriver konfiguration av proaktiv identifiering för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/Components/Purge/Action | Rensar data från Application Insights |
> | Åtgärd | Microsoft.Insights/Components/Query/Read | Kör frågor mot Application Insights-loggar |
> | Åtgärd | Microsoft.Insights/Components/QuotaStatus/Read | Läser kvotstatus för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Read | Läser en konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Läser Application Insights-webbtestplatser |
> | Åtgärd | Microsoft.Insights/Components/Webtests/Read | Läser en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Delete | Tar bort en konfiguration av ALM-integrering i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Read | Läser en konfiguration av ALM-integrering i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Write | Skriver en konfiguration av ALM-integrering i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/Write | Skriver till en konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Delete | Ta bort en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Read | Läs en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Write | Skapa eller uppdatera en resursdiagnostikinställning |
> | Åtgärd | Microsoft.Insights/EventCategories/Read | Läs tillgängliga kategorier av aktivitetslogghändelser |
> | Åtgärd | Microsoft.Insights/eventtypes/digestevents/Read | Läs sammandrag av hanteringshändelsetyp |
> | Åtgärd | Microsoft.Insights/eventtypes/values/Read | Läs aktivitetslogghändelser |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Ta bort en diagnostikinställning för nätverksflödeslogg |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Läs en diagnostikinställning för nätverksflödeslogg |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Skapa eller uppdatera en diagnostikinställning för nätverksflödeslogg |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Action | Hämta datum för prenumerationsmigrering |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Read | Hämta datum för prenumerationsmigrering |
> | Åtgärd | Microsoft.Insights/LogDefinitions/Read | Läs loggdefinitioner |
> | Åtgärd | Microsoft.Insights/LogProfiles/Delete | Ta bort en loggprofil för aktivitetslogg |
> | Åtgärd | Microsoft.Insights/LogProfiles/Read | Läs en loggprofil för aktivitetslogg |
> | Åtgärd | Microsoft.Insights/LogProfiles/Write | Skapa eller uppdatera en loggprofil för aktivitetslogg |
> | Åtgärd | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Läs data från ADAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ADReplicationResult/Read | Läs data från ADReplicationResult-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Läs data från ADSecurityAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Alert/Read | Läs data från Alert-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/AlertHistory/Read | Läs data från AlertHistory-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ApplicationInsights/Read | Läs data från ApplicationInsights-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/AzureActivity/Read | Läs data från AzureActivity-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/AzureMetrics/Read | Läs data från AzureMetrics-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/BoundPort/Read | Läs data från BoundPort-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/CommonSecurityLog/Read | Läs data från CommonSecurityLog-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ComputerGroup/Read | Läs data från ComputerGroup-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ConfigurationChange/Read | Läs data från ConfigurationChange-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ConfigurationData/Read | Läs data från ConfigurationData-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ContainerImageInventory/Read | Läs data från ContainerImageInventory-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ContainerInventory/Read | Läs data från ContainerInventory-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ContainerLog/Read | Läs data från ContainerLog-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ContainerServiceLog/Read | Läs data från ContainerServiceLog-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceAppCrash/Read | Läs data från DeviceAppCrash-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceAppLaunch/Read | Läs data från DeviceAppLaunch-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceCalendar/Read | Läs data från DeviceCalendar-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceCleanup/Read | Läs data från DeviceCleanup-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceConnectSession/Read | Läs data från DeviceConnectSession-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceEtw/Read | Läs data från DeviceEtw-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Läs data från DeviceHardwareHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHealth/Read | Läs data från DeviceHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Läs data från DeviceHeartbeat-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Läs data från DeviceSkypeHeartbeat-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Läs data från DeviceSkypeSignIn-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSleepState/Read | Läs data från DeviceSleepState-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHAppFailure/Read | Läs data från DHAppFailure-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHAppReliability/Read | Läs data från DHAppReliability-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHDriverReliability/Read | Läs data från DHDriverReliability-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHLogonFailures/Read | Läs data från DHLogonFailures-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHLogonMetrics/Read | Läs data från DHLogonMetrics-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHOSCrashData/Read | Läs data från DHOSCrashData-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHOSReliability/Read | Läs data från DHOSReliability-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DHWipAppLearning/Read | Läs data från DHWipAppLearning-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DnsEvents/Read | Läs data från DnsEvents-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/DnsInventory/Read | Läs data från DnsInventory-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ETWEvent/Read | Läs data från ETWEvent-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Event/Read | Läs data från Event-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Läs data från ExchangeAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Läs data från ExchangeOnlineAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Heartbeat/Read | Läs data från Hearbeat-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Läs data från IISAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/InboundConnection/Read | Läs data från InboundConnection-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/KubeNodeInventory/Read | Läs data från KubeNodeInventory-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/KubePodInventory/Read | Läs data från KubePodInventory-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/LinuxAuditLog/Read | Läs data från LinuxAuditLog-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplication/Read | Läs data från MAApplication-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealth/Read | Läs data från MAApplicationHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Läs data från MAApplicationHealthAlternativeVersions-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealthIssues/Read | Läs data från MAApplicationHealthIssues-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationInstance/Read | Läs data från MAApplicationInstance-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationInstanceReadiness/Read | Läs data från MAApplicationInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Läs data från MAApplicationReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADeploymentPlan/Read | Läs data från MADeploymentPlan-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADevice/Read | Läs data från MADevice-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Läs data från MADevicePnPHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Läs data från MADevicePnPHealthAlternativeVersions-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealthIssues/Read | Läs data från MADevicePnPHealthIssues-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADeviceReadiness/Read | Läs data från MADeviceReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Läs data från MADriverInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MADriverReadiness/Read | Läs data från MADriverReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddin/Read | Läs data från MAOfficeAddin-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Läs data från MAOfficeAddinHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinHealthIssues/Read | Läs data från MAOfficeAddinHealthIssues-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinInstance/Read | Läs data från MAOfficeAddinInstance-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinInstanceReadiness/Read | Läs data från MAOfficeAddinInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinReadiness/Read | Läs data från MAOfficeAddinReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeApp/Read | Läs data från MAOfficeApp-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAppHealth/Read | Läs data från MAOfficeAppHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAppInstance/Read | Läs data från MAOfficeAppInstance-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAppReadiness/Read | Läs data från MAOfficeAppReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeBuildInfo/Read | Läs data från MAOfficeBuildInfo-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeCurrencyAssessment/Read | Läs data från MAOfficeCurrencyAssessment-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Läs data från MAOfficeCurrencyAssessmentDailyCounts-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeDeploymentStatus/Read | Läs data från MAOfficeDeploymentStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Läs data från MAOfficeMacroHealth-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroHealthIssues/Read | Läs data från MAOfficeMacroHealthIssues-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Läs data från MAOfficeMacroIssueInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroIssueReadiness/Read | Läs data från MAOfficeMacroIssueReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroSummary/Read | Läs data från MAOfficeMacroSummary-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeSuite/Read | Läs data från MAOfficeSuite-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Läs data från MAOfficeSuiteInstance-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAProposedPilotDevices/Read | Läs data från MAProposedPilotDevices-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsBuildInfo/Read | Läs data från MAWindowsBuildInfo-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Läs data från MAWindowsCurrencyAssessment-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Läs data från MAWindowsCurrencyAssessmentDailyCounts-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsDeploymentStatus/Read | Läs data från MAWindowsDeploymentStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Läs data från MAWindowsSysReqInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/NetworkMonitoring/Read | Läs data från NetworkMonitoring-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/OfficeActivity/Read | Läs data från OfficeActivity-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Operation/Read | Läs data från Operation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/OutboundConnection/Read | Läs data från OutboundConnection-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Perf/Read | Läs data från Perf-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ProtectionStatus/Read | Läs data från ProtectionStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Read | Läser data från alla dina loggar |
> | Åtgärd | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Läs data från ReservedAzureCommonFields-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ReservedCommonFields/Read | Läs data från ReservedCommonFields-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Läs data från SCCMAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SCOMAssessmentRecommendation/Read | Läs data från SCOMAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SecurityAlert/Read | Läs data från SecurityAlert-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SecurityBaseline/Read | Läs data från SecurityBaseline-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SecurityBaselineSummary/Read | Läs data från SecurityBaselineSummary-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SecurityDetection/Read | Läs data från SecurityDetection-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SecurityEvent/Read | Läs data från SecurityEvent-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricOperationalEvent/Read | Läs data från ServiceFabricOperationalEvent-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Läs data från ServiceFabricReliableActorEvent-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Läs data från ServiceFabricReliableServiceEvent-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Läs data från SfBAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Läs data från SfBOnlineAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Läs data från SharePointOnlineAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SPAssessmentRecommendation/Read | Läs data från SPAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SQLAssessmentRecommendation/Read | Läs data från SQLAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SQLQueryPerformance/Read | Läs data från SQLQueryPerformance-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Syslog/Read | Läs data från Syslog-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/SysmonEvent/Read | Läs data från SysmonEvent-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAApp/Read | Läs data från UAApp-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAComputer/Read | Läs data från UAComputer-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAComputerRank/Read | Läs data från UAComputerRank-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UADriver/Read | Läs data från UADriver-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Läs data från UADriverProblemCodes-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAFeedback/Read | Läs data från UAFeedback-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Läs data från UAHardwareSecurity-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Läs data från UAIESiteDiscovery-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Läs data från UAOfficeAddIn-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Läs data från UAProposedActionPlan-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UASysReqIssue/Read | Läs data från UASysReqIssue-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Läs data från UAUpgradedComputer-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Update/Read | Läs data från Update-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UpdateRunProgress/Read | Läs data från UpdateRunProgress-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UpdateSummary/Read | Läs data från UpdateSummary-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/Usage/Read | Läs data från Usage-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/W3CIISLog/Read | Läs data från W3CIISLog-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Läs data från WaaSDeploymentStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Läs data från WaaSInsiderStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Läs data från WaaSUpdateStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WDAVStatus/Read | Läs data från WDAVStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WDAVThreat/Read | Läs data från WDAVThreat-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Läs data från WindowsClientAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WindowsFirewall/Read | Läs data från WindowsFirewall-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Läs data från WindowsServerAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WireData/Read | Läs data från WireData-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Läs data från WUDOAggregatedStatus-tabellen |
> | Åtgärd | Microsoft.Insights/Logs/WUDOStatus/Read | Läs data från WUDOStatus-tabellen |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Delete | Ta bort en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Read | Läs en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Status/Read | Läs status för måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Write | Skapa eller uppdatera en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/Metrics/Action | Måttåtgärd |
> | Åtgärd | Microsoft.Insights/Metrics/providers/Metrics/Read | Läs mått |
> | Åtgärd | Microsoft.Insights/Metrics/Read | Läs mått |
> | DataAction | Microsoft.Insights/Metrics/Write | Skrivning av mått |
> | Åtgärd | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrera prenumerationen till den nya prismodellen |
> | Åtgärd | Microsoft.Insights/Operations/Read | Läsåtgärder |
> | Åtgärd | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Återställ prenumerationen till den äldre prismodellen |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Delete | Tar bort en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Read | Läser en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Write | Skriver en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/Tenants/Register/Action | Startar Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Unregister/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Webtests/Delete | Tar bort en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Webtests/GetToken/Read | Läser en token för webbtest |
> | Åtgärd | Microsoft.Insights/Webtests/MetricDefinitions/Read | Läser definitioner av mått för webbtest |
> | Åtgärd | Microsoft.Insights/Webtests/Metrics/Read | Läser ett webbtestmätvärde |
> | Åtgärd | Microsoft.Insights/Webtests/Read | Läser en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Webtests/Write | Skriver till en webbtestkonfiguration |

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
> | Åtgärd | Microsoft.KeyVault/checkNameAvailability/read | Kontrollera att nyckelvalvsnamnet är giltigt och inte används |
> | Åtgärd | Microsoft.KeyVault/deletedVaults/read | Visa egenskaperna för de ej permanent borttagna nyckelvalven |
> | Åtgärd | Microsoft.KeyVault/hsmPools/delete | Ta bort en HSM-pool |
> | Åtgärd | Microsoft.KeyVault/hsmPools/joinVault/action | Anslut ett nyckelvalv till en HSM-pool |
> | Åtgärd | Microsoft.KeyVault/hsmPools/read | Visa egenskaperna för en HSM-pool |
> | Åtgärd | Microsoft.KeyVault/hsmPools/write | Skapa en ny HSM-pool eller uppdatera egenskaperna för en befintlig HSM-pool |
> | Åtgärd | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent borttaget nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/locations/deletedVaults/read | Visa egenskaperna för ett ej permanent borttaget nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.KeyVault att ett virtuellt nätverk eller undernät tas bort |
> | Åtgärd | Microsoft.KeyVault/locations/operationResults/read | Kontrollera resultatet för en långkörd åtgärd |
> | Åtgärd | Microsoft.KeyVault/operations/read | Listar de åtgärder som finns tillgängliga på resursprovidern Microsoft.KeyVault |
> | Åtgärd | Microsoft.KeyVault/register/action | Registrerar en prenumeration |
> | Åtgärd | Microsoft.KeyVault/unregister/action | Avregistrerar en prenumeration |
> | Åtgärd | Microsoft.KeyVault/vaults/accessPolicies/write | Uppdatera en befintlig åtkomstprincip genom att sammanfoga, ersätta eller lägga till en ny åtkomstprincip till ett valv. |
> | Åtgärd | Microsoft.KeyVault/vaults/delete | Ta bort ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/deploy/action | Tillåter åtkomst till hemligheter i ett nyckelvalv vid distribution av Azure-resurser |
> | Åtgärd | Microsoft.KeyVault/vaults/read | Visa egenskaperna för ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/read | Visa egenskaperna för en hemlighet, men inte dess värde |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/write | Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet |
> | Åtgärd | Microsoft.KeyVault/vaults/write | Skapa ett nytt nyckelvalv eller uppdatera egenskaperna för ett befintligt nyckelvalv |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Kusto/Clusters/Activate/action | Startar klustret. |
> | Åtgärd | Microsoft.Kusto/Clusters/CheckNameAvailability/action | Kontrollerar tillgängligheten för klustret namn. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Lägger till huvudkonton. |
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
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Startar en mall genom att starta alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stoppar en mall genom att stoppa alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Upload/action | Överför aktuella mall-bild till galleriet delade i labbkonto |
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
> | Åtgärd | Microsoft.LabServices/users/ListEnvironments/action | Lista miljöer för användaren |
> | Åtgärd | Microsoft.LabServices/users/ListLabs/action | Lista över labs för användaren. |
> | Åtgärd | Microsoft.LabServices/users/Register/action | Registrera en användare till en hanterad labb |
> | Åtgärd | Microsoft.LabServices/users/ResetPassword/action | Återställer användarlösenord i en miljö |
> | Åtgärd | Microsoft.LabServices/users/StartEnvironment/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft.LabServices/users/StopEnvironment/action | Stoppar en miljö genom att stoppa alla resurser i miljön |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/delete | (Inaktuell: Använd /providers/Microsoft.Maps) ta bort en Location Based Services-konto. |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/listKeys/action | (Inaktuell: Använd /providers/Microsoft.Maps)List Location Based Services-konto nycklar |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/read | (Inaktuell: Använd /providers/Microsoft.Maps) får en Location Based Services-konto. |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Inaktuell: Använd /providers/Microsoft.Maps) skapa nya Location Based Services-konto primära eller sekundära nycklar |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/write | (Inaktuell: Använd /providers/Microsoft.Maps) skapa eller uppdatera en Location Based Services-konto. |
> | Åtgärd | Microsoft.LocationBasedServices/register/action | (Inaktuell: Använd /providers/Microsoft.Maps) registrera providern |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LocationServices/accounts/delete | (Inaktuell: Använd /providers/Microsoft.Maps) ta bort en Location Services-konto. |
> | Åtgärd | Microsoft.LocationServices/accounts/listKeys/action | (Inaktuell: Använd /providers/Microsoft.Maps)List Location Based Services-konto nycklar |
> | Åtgärd | Microsoft.LocationServices/accounts/read | (Inaktuell: Använd /providers/Microsoft.Maps) får en Location Services-konto. |
> | Åtgärd | Microsoft.LocationServices/accounts/regenerateKey/action | (Inaktuell: Använd /providers/Microsoft.Maps) skapa nya Location Based Services-konto primära eller sekundära nycklar |
> | Åtgärd | Microsoft.LocationServices/accounts/write | (Inaktuell: Använd /providers/Microsoft.Maps) skapa eller uppdatera en Location Services-konto. |
> | Åtgärd | Microsoft.LocationServices/register/action | (Inaktuell: Använd /providers/Microsoft.Maps) registrera providern |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | DataAction | Microsoft.LogAnalytics/logs/ADAssessmentRecommendation/read | Läs data från ADAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ADReplicationResult/read | Läs data från ADReplicationResult-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ADSecurityAssessmentRecommendation/read | Läs data från ADSecurityAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Alert/read | Läs data från Alert-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/AlertHistory/read | Läs data från AlertHistory-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/AppCenterError/read | Läsa data från tabellen AppCenterError |
> | DataAction | Microsoft.LogAnalytics/logs/ApplicationInsights/read | Läs data från ApplicationInsights-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/AuditLogs/read | Läsa data från tabellen AuditLogs |
> | DataAction | Microsoft.LogAnalytics/logs/AzureActivity/read | Läs data från AzureActivity-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/AzureMetrics/read | Läs data från AzureMetrics-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/BoundPort/read | Läs data från BoundPort-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/CommonSecurityLog/read | Läs data från CommonSecurityLog-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ComputerGroup/read | Läs data från ComputerGroup-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationChange/read | Läs data från ConfigurationChange-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ConfigurationData/read | Läs data från ConfigurationData-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerImageInventory/read | Läs data från ContainerImageInventory-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerInventory/read | Läs data från ContainerInventory-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerLog/read | Läs data från ContainerLog-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ContainerServiceLog/read | Läs data från ContainerServiceLog-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppCrash/read | Läs data från DeviceAppCrash-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceAppLaunch/read | Läs data från DeviceAppLaunch-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCalendar/read | Läs data från DeviceCalendar-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceCleanup/read | Läs data från DeviceCleanup-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceConnectSession/read | Läs data från DeviceConnectSession-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceEtw/read | Läs data från DeviceEtw-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHardwareHealth/read | Läs data från DeviceHardwareHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHealth/read | Läs data från DeviceHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceHeartbeat/read | Läs data från DeviceHeartbeat-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeHeartbeat/read | Läs data från DeviceSkypeHeartbeat-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSkypeSignIn/read | Läs data från DeviceSkypeSignIn-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DeviceSleepState/read | Läs data från DeviceSleepState-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppFailure/read | Läs data från DHAppFailure-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHAppReliability/read | Läs data från DHAppReliability-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHDriverReliability/read | Läs data från DHDriverReliability-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonFailures/read | Läs data från DHLogonFailures-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHLogonMetrics/read | Läs data från DHLogonMetrics-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSCrashData/read | Läs data från DHOSCrashData-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHOSReliability/read | Läs data från DHOSReliability-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DHWipAppLearning/read | Läs data från DHWipAppLearning-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DnsEvents/read | Läs data från DnsEvents-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/DnsInventory/read | Läs data från DnsInventory-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ETWEvent/read | Läs data från ETWEvent-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Event/read | Läs data från Event-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeAssessmentRecommendation/read | Läs data från ExchangeAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ExchangeOnlineAssessmentRecommendation/read | Läs data från ExchangeOnlineAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Heartbeat/read | Läs data från Hearbeat-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/IISAssessmentRecommendation/read | Läs data från IISAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/InboundConnection/read | Läs data från InboundConnection-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/KubeEvents/read | Läsa data från tabellen KubeEvents |
> | DataAction | Microsoft.LogAnalytics/logs/KubeNodeInventory/read | Läs data från KubeNodeInventory-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/KubePodInventory/read | Läs data från KubePodInventory-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/KubeServices/read | Läsa data från tabellen KubeServices |
> | DataAction | Microsoft.LogAnalytics/logs/LinuxAuditLog/read | Läs data från LinuxAuditLog-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplication/read | Läs data från MAApplication-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealth/read | Läs data från MAApplicationHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthAlternativeVersions/read | Läs data från MAApplicationHealthAlternativeVersions-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationHealthIssues/read | Läs data från MAApplicationHealthIssues-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstance/read | Läs data från MAApplicationInstance-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationInstanceReadiness/read | Läs data från MAApplicationInstanceReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAApplicationReadiness/read | Läs data från MAApplicationReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADeploymentPlan/read | Läs data från MADeploymentPlan-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADevice/read | Läs data från MADevice-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceNotEnrolled/read | Läsa data från tabellen MADeviceNotEnrolled |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealth/read | Läs data från MADevicePnPHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthAlternativeVersions/read | Läs data från MADevicePnPHealthAlternativeVersions-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADevicePnPHealthIssues/read | Läs data från MADevicePnPHealthIssues-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADeviceReadiness/read | Läs data från MADeviceReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverInstanceReadiness/read | Läs data från MADriverInstanceReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MADriverReadiness/read | Läs data från MADriverReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddin/read | Läs data från MAOfficeAddin-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealth/read | Läs data från MAOfficeAddinHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinHealthIssues/read | Läs data från MAOfficeAddinHealthIssues-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstance/read | Läs data från MAOfficeAddinInstance-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinInstanceReadiness/read | Läs data från MAOfficeAddinInstanceReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAddinReadiness/read | Läs data från MAOfficeAddinReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeApp/read | Läs data från MAOfficeApp-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppHealth/read | Läs data från MAOfficeAppHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppInstance/read | Läs data från MAOfficeAppInstance-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeAppReadiness/read | Läs data från MAOfficeAppReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeBuildInfo/read | Läs data från MAOfficeBuildInfo-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessment/read | Läs data från MAOfficeCurrencyAssessment-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeCurrencyAssessmentDailyCounts/read | Läs data från MAOfficeCurrencyAssessmentDailyCounts-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeDeploymentStatus/read | Läs data från MAOfficeDeploymentStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealth/read | Läs data från MAOfficeMacroHealth-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroHealthIssues/read | Läs data från MAOfficeMacroHealthIssues-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueInstanceReadiness/read | Läs data från MAOfficeMacroIssueInstanceReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroIssueReadiness/read | Läs data från MAOfficeMacroIssueReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeMacroSummary/read | Läs data från MAOfficeMacroSummary-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuite/read | Läs data från MAOfficeSuite-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAOfficeSuiteInstance/read | Läs data från MAOfficeSuiteInstance-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAProposedPilotDevices/read | Läs data från MAProposedPilotDevices-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsBuildInfo/read | Läs data från MAWindowsBuildInfo-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessment/read | Läs data från MAWindowsCurrencyAssessment-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsCurrencyAssessmentDailyCounts/read | Läs data från MAWindowsCurrencyAssessmentDailyCounts-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsDeploymentStatus/read | Läs data från MAWindowsDeploymentStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/MAWindowsSysReqInstanceReadiness/read | Läs data från MAWindowsSysReqInstanceReadiness-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/NetworkMonitoring/read | Läs data från NetworkMonitoring-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/OfficeActivity/read | Läs data från OfficeActivity-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Operation/read | Läs data från Operation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/OutboundConnection/read | Läs data från OutboundConnection-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Perf/read | Läs data från Perf-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ProtectionStatus/read | Läs data från ProtectionStatus-tabellen |
> | Åtgärd | Microsoft.LogAnalytics/logs/read | Läser data från alla dina loggar |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedAzureCommonFields/read | Läs data från ReservedAzureCommonFields-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ReservedCommonFields/read | Läs data från ReservedCommonFields-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SCCMAssessmentRecommendation/read | Läs data från SCCMAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SCOMAssessmentRecommendation/read | Läs data från SCOMAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityAlert/read | Läs data från SecurityAlert-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaseline/read | Läs data från SecurityBaseline-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityBaselineSummary/read | Läs data från SecurityBaselineSummary-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityDetection/read | Läs data från SecurityDetection-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SecurityEvent/read | Läs data från SecurityEvent-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricOperationalEvent/read | Läs data från ServiceFabricOperationalEvent-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableActorEvent/read | Läs data från ServiceFabricReliableActorEvent-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/ServiceFabricReliableServiceEvent/read | Läs data från ServiceFabricReliableServiceEvent-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SfBAssessmentRecommendation/read | Läs data från SfBAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SfBOnlineAssessmentRecommendation/read | Läs data från SfBOnlineAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SharePointOnlineAssessmentRecommendation/read | Läs data från SharePointOnlineAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SigninLogs/read | Läsa data från tabellen SigninLogs |
> | DataAction | Microsoft.LogAnalytics/logs/SPAssessmentRecommendation/read | Läs data från SPAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SQLAssessmentRecommendation/read | Läs data från SQLAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SQLQueryPerformance/read | Läs data från SQLQueryPerformance-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Syslog/read | Läs data från Syslog-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/SysmonEvent/read | Läs data från SysmonEvent-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Tables.Custom/read | Läser data från valfri anpassad logg |
> | DataAction | Microsoft.LogAnalytics/logs/UAApp/read | Läs data från UAApp-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputer/read | Läs data från UAComputer-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAComputerRank/read | Läs data från UAComputerRank-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UADriver/read | Läs data från UADriver-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UADriverProblemCodes/read | Läs data från UADriverProblemCodes-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAFeedback/read | Läs data från UAFeedback-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAHardwareSecurity/read | Läs data från UAHardwareSecurity-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAIESiteDiscovery/read | Läs data från UAIESiteDiscovery-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAOfficeAddIn/read | Läs data från UAOfficeAddIn-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAProposedActionPlan/read | Läs data från UAProposedActionPlan-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UASysReqIssue/read | Läs data från UASysReqIssue-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UAUpgradedComputer/read | Läs data från UAUpgradedComputer-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Update/read | Läs data från Update-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateRunProgress/read | Läs data från UpdateRunProgress-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/UpdateSummary/read | Läs data från UpdateSummary-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/Usage/read | Läs data från Usage-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/VMBoundPort/read | Läsa data från tabellen VMBoundPort |
> | DataAction | Microsoft.LogAnalytics/logs/VMConnection/read | Läsa data från tabellen VMConnection |
> | DataAction | Microsoft.LogAnalytics/logs/W3CIISLog/read | Läs data från W3CIISLog-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSDeploymentStatus/read | Läs data från WaaSDeploymentStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSInsiderStatus/read | Läs data från WaaSInsiderStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WaaSUpdateStatus/read | Läs data från WaaSUpdateStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVStatus/read | Läs data från WDAVStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WDAVThreat/read | Läs data från WDAVThreat-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsClientAssessmentRecommendation/read | Läs data från WindowsClientAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsEvent/read | Läsa data från tabellen WindowsEvent |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsFirewall/read | Läs data från WindowsFirewall-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WindowsServerAssessmentRecommendation/read | Läs data från WindowsServerAssessmentRecommendation-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WireData/read | Läs data från WireData-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WorkloadMonitoringPerf/read | Läsa data från tabellen WorkloadMonitoringPerf |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOAggregatedStatus/read | Läs data från WUDOAggregatedStatus-tabellen |
> | DataAction | Microsoft.LogAnalytics/logs/WUDOStatus/read | Läs data från WUDOStatus-tabellen |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/delete | Tar bort avtalet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Hämtar motringnings-URL:en för avtalsinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/read | Läser avtalet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/write | Skapar eller uppdaterar avtalet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/delete | Tar bort sammansättningen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Hämtar motringnings-URL:en för sammansättningsinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/read | Läser sammansättningen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/write | Skapar eller uppdaterar sammansättningen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Tar bort batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Läser batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Skapar eller uppdaterar batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/delete | Tar bort certifikatet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/read | Läser certifikatet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/write | Skapar eller uppdaterar certifikatet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/delete | Tar bort integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/join/action | Ansluter integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Hämtar motringnings-URL:en för integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Hämtar nycklarna i nyckelvalvet. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Loggar spårningshändelser i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/delete | Tar bort kartan i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Hämtar motringnings-URL:en för kartinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/read | Läser kartan i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/write | Skapar eller uppdaterar kartan i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/delete | Tar bort partnern i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Hämtar motringnings-URL:en för partnerinnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/read | Läser partnern i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/write | Skapar eller uppdaterar partnern i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Läser integrationskontots loggdefinitioner. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/read | Läser integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Återskapar åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/delete | Tar bort schemat i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Hämtar motringnings-URL:en för schemainnehållet i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/read | Läser schemat i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/write | Skapar eller uppdaterar schemat i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/delete | Tar bort sessionen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/read | Läser batchkonfigurationen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/write | Skapar eller uppdaterar sessionen i integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/write | Skapar eller uppdaterar integrationskontot. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/delete | Tar bort integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/join/action | Går med i integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Läser den integreringstjänstmiljöhanterade API-åtgärden. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Läser det integreringstjänstmiljöhanterade API:et. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Läser åtgärdsstatusar för integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitionerna för integreringstjänstmiljö. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/read | Läser integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/write | Skapar eller uppdaterar integreringstjänstmiljön. |
> | Åtgärd | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Hämtar arbetsflödets rekommendationsåtgärdsgrupper. |
> | Åtgärd | Microsoft.Logic/locations/workflows/validate/action | Verifierar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/operations/read | Hämtar åtgärden. |
> | Åtgärd | Microsoft.Logic/register/action | Registrerar Microsoft.Logic-resursprovidern för en viss prenumeration. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/delete | Tar bort åtkomstnyckeln. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/list/action | Radar upp åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/read | Läser åtkomstnyckeln. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/regenerate/action | Återskapar åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/write | Skapar eller uppdaterar åtkomstnyckeln. |
> | Åtgärd | Microsoft.Logic/workflows/delete | Tar bort arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/disable/action | Inaktiverar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/enable/action | Aktiverar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/listCallbackUrl/action | Hämtar arbetsflödets motringnings-URL. |
> | Åtgärd | Microsoft.Logic/workflows/listSwagger/action | Hämtar arbetsflödets Swagger-definitioner. |
> | Åtgärd | Microsoft.Logic/workflows/move/action | Flyttar arbetsflödet från befintligt prenumerations-id, resursgrupp och/eller namn till ett annat prenumerations-id, resursgrupp och/eller namn. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Läser diagnostikinställningar för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställning för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Läser loggdefinitioner för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitioner för arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/read | Läser arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/regenerateAccessKey/action | Återskapar åtkomstnyckelhemligheter. |
> | Åtgärd | Microsoft.Logic/workflows/run/action | Startar en körning av arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Hämtar spåren av arbetsflödeskörningens åtgärdsuttryck. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/read | Läser arbetsflödets köråtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Hämtar upprepningsspåren av arbetsflödeskörningens åtgärdsuttryck. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/read | Läser upprepningen av arbetsflödets körningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Återställer frågehistoriken för arbetsflödets körningsupprepningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Återställer frågehistoriken för arbetsflödets körningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Läser upprepningen av reservationsomfånget för arbetsflödets körningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/cancel/action | Avbryter körningen av ett arbetsflöde. |
> | Åtgärd | Microsoft.Logic/workflows/runs/delete | Tar bort en körning av ett arbetsflöde. |
> | Åtgärd | Microsoft.Logic/workflows/runs/operations/read | Läser åtgärdsstatus för arbetsflödeskörning. |
> | Åtgärd | Microsoft.Logic/workflows/runs/read | Läser arbetsflödeskörningen. |
> | Åtgärd | Microsoft.Logic/workflows/suspend/action | Gör uppehåll i arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/histories/read | Läser utlösarhistorik. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/histories/resubmit/action | Skickar om arbetsflödets utlösare. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/listCallbackUrl/action | Hämtar motringnings-URL:en för utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/read | Läser utlösarna. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/reset/action | Återställer utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/run/action | Utför utlösaren. |
> | Åtgärd | Microsoft.Logic/workflows/triggers/setState/action | Ställer in utlösartillstånd. |
> | Åtgärd | Microsoft.Logic/workflows/validate/action | Verifierar arbetsflödet. |
> | Åtgärd | Microsoft.Logic/workflows/versions/read | Läser arbetsflödesversionen. |
> | Åtgärd | Microsoft.Logic/workflows/versions/triggers/listCallbackUrl/action | Hämtar motringnings-URL:en för utlösaren. |
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

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningModelManagement/accounts/delete | Ta bort alla som värd-konto |
> | Åtgärd | Microsoft.MachineLearningModelManagement/accounts/read | Läs valfri värd-konto |
> | Åtgärd | Microsoft.MachineLearningModelManagement/accounts/write | Skapa eller uppdatera eventuella värdbaserade konto |
> | Åtgärd | Microsoft.MachineLearningModelManagement/register/action | Registrerar prenumerations-ID till resursprovidern och gör det möjligt att skapa en värdtjänst konto |

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

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/CreateLab/action | Skapa ett labb i ett labbkonto. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/delete | Ta bort lab-konton. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/delete | Ta bort labs. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Ta bort miljöinställning. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Läs miljö. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Inställningen för Läs miljö. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Lägg till eller ändra inställningen för miljön. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Ta bort virtuella datorer för testlabbet. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Läsa virtuella datorer för testlabbet. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Lägg till eller ändra labb virtuella datorer. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/read | Läsa labs. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/write | Lägg till eller ändra labb. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/read | Läsa lab-konton. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/write | Lägg till eller ändra labb-konton. |
> | Åtgärd | Microsoft.ManagedLab/locations/operations/read | Läsåtgärder. |
> | Åtgärd | Microsoft.ManagedLab/register/action | Registrerar prenumerationen |

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
> | Åtgärd | Microsoft.Maps/register/action | Registrera providern |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Returnerar ett avtal. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepterar ett undertecknat avtal. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importerar en avbildning till slutanvändarens ACR. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Returnerar en konfig. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Sparar en konfig. |
> | Åtgärd | Microsoft.Marketplace/register/action | Registrerar Microsoft.Marketplace-resursprovidern i prenumerationen. |

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
> | Åtgärd | Microsoft.Migrate/locations/assessmentOptions/read | Hämtar de utvärderingsalternativ som är tillgängliga på den angivna platsen |
> | Åtgärd | Microsoft.Migrate/locations/checknameavailability/action | Kontrollerar tillgängligheten för resursnamnet för den givna prenumerationen på den angivna platsen |
> | Åtgärd | Microsoft.Migrate/migrateprojects/read | Hämtar egenskaperna för ett migreringsprojekt. |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Hämtar konfiguration för migreringsprojektslösning |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/read | Hämtar egenskaperna för en migreringsprojektslösning. |
> | Åtgärd | Microsoft.Migrate/Operations/read | Listar tillgängliga åtgärder på resursprovidern Microsoft.Migrate |
> | Åtgärd | Microsoft.Migrate/projects/assessments/read | Listar utvärderingar i ett projekt |
> | Åtgärd | Microsoft.Migrate/projects/delete | Tar bort projektet |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Hämta egenskaperna för en utvärderad dator |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/delete | Tar bort en utvärdering |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Hämtar en URL för en utvärderingsrapport |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/read | Hämtar egenskaperna för en utvärdering |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/write | Skapar en ny utvärdering eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Migrate/projects/groups/delete | Tar bort en grupp |
> | Åtgärd | Microsoft.Migrate/projects/groups/read | Hämta en grupps egenskaper |
> | Åtgärd | Microsoft.Migrate/projects/groups/write | Skapar en ny grupp eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Migrate/projects/keys/action | Hämtar delade nycklar för projektet |
> | Åtgärd | Microsoft.Migrate/projects/machines/read | Hämtar egenskaperna för en dator |
> | Åtgärd | Microsoft.Migrate/projects/read | Hämtar egenskaperna för ett projekt |
> | Åtgärd | Microsoft.Migrate/projects/write | Skapar ett nytt projekt eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.Migrate/register/action | Registrerar prenumeration på resursprovidern Microsoft.Migrate |

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
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/delete | Tar bort en brandväggsprincip för Web Application |
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/read | Hämtar en brandväggsprincip för Web Application |
> | Åtgärd | Microsoft.Network/frontDoorWebApplicationFirewallPolicies/write | Skapar eller uppdaterar en brandväggsprincip för Web Application |
> | Åtgärd | Microsoft.Network/interfaceEndpoints/delete | Tar bort en slutpunktsresurs i gränssnittet. |
> | Åtgärd | Microsoft.Network/interfaceEndpoints/read | Hämtar en slutpunktsresurs i gränssnittet. |
> | Åtgärd | Microsoft.Network/interfaceEndpoints/write | Skapar en ny slutpunkt för gränssnittet eller uppdaterar en befintlig gränssnittet slutpunkt. |
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
> | Åtgärd | Microsoft.Network/locations/availableDelegations/read | Hämtar tillgängliga delegeringar |
> | Åtgärd | Microsoft.Network/locations/bareMetalTenants/action | Allokerar eller validerar en Bare Metal-klient |
> | Åtgärd | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Kontrollerar stöd för Accelererat nätverk |
> | Åtgärd | Microsoft.Network/locations/checkDnsNameAvailability/read | Kontrollerar om dns-etikett är tillgängliga på den angivna platsen |
> | Åtgärd | Microsoft.Network/locations/operationResults/read | Hämtar resultat av en asynkron INLÄGG eller borttagningsåtgärd |
> | Åtgärd | Microsoft.Network/locations/operations/read | Hämtar åtgärden resurs som representerar statusen för en asynkron åtgärd |
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
> | Åtgärd | Microsoft.Network/privateLinkServices/delete | Tar bort en privat länk-tjänstresurs. |
> | Åtgärd | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/delete | Tar bort en slutpunkt-anslutning. |
> | Åtgärd | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/read | Hämtar en anslutning för gränssnittet slutpunktsdefinitionen. |
> | Åtgärd | Microsoft.Network/privateLinkServices/interfaceEndpointConnections/write | Skapar en ny anslutning för gränssnittet slutpunkten eller uppdaterar en befintlig anslutning för gränssnittet slutpunkt. |
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
> | Åtgärd | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Kontrollerar huruvida ett visst namnområdesresursnamn finns i NotificationHub-tjänsten. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Auktoriseringsregel för namnområde: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Kontrollerar huruvida ett visst NotificationHub-namn finns i ett namnområde. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Hämta listan över auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Ta bort auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Hämta anslutningssträngen till meddelandehubben |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Hämta listan över auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Auktoriseringsregel för Notification Hub: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Skapa auktoriseringsregler för Notification Hub och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Skicka ett push-testmeddelande. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Ta bort Notification Hub-resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Hämta alla Notification Hub PNS-autentiseringsuppgifter. Detta omfattar autentiseringsuppgifter för WNS, MPNS, APNS, GCM och Baidu |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Hämta listan över resursbeskrivningar av Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Skapa en Meddelandehubb och uppdatera dess egenskaper. Egenskaperna är främst PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/write | Skapa en Namespace-resurs och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/operationResults/read | Returnerar åtgärdsresultat för Notification Hubs-provider |
> | Åtgärd | Microsoft.NotificationHubs/operations/read | Returnerar en lista över åtgärder som stöds för Notification Hubs-provider |
> | Åtgärd | Microsoft.NotificationHubs/register/action | Registrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs |
> | Åtgärd | Microsoft.NotificationHubs/unregister/action | Avregistrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/read | Hämtar egenskaperna för ett Hyper-V-kluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/read | Hämtar egenskaperna för ett Hyper-V-kluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/write | Skapar eller uppdaterar Hyper-V-kluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/write | Skapar eller uppdaterar Hyper-V-kluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/delete | Tar bort Hyper-V-platsen |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/delete | Tar bort Hyper-V-platsen |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/read | Hämtar egenskaperna för en Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/read | Hämtar egenskaperna för en Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/write | Skapar eller uppdaterar Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/write | Skapar eller uppdaterar Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/jobs/read | Hämtar egenskaperna för en Hyper-V-jobb |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/jobs/read | Hämtar egenskaperna för en Hyper-V-jobb |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/read | Hämtar egenskaperna för en Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/read | Hämtar egenskaperna för en Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/start/action | Starta Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/start/action | Starta Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/stop/action | Stoppar Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/stop/action | Stoppar Hyper-V-datorer |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hämtar egenskaperna för en Hyper-V-Åtgärdsstatus |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Hämtar egenskaperna för en Hyper-V-Åtgärdsstatus |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/read | Hämtar egenskaperna för en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/read | Hämtar egenskaperna för en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/refresh/action | Uppdaterar objekt i en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/refresh/action | Uppdaterar objekt i en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hämtar egenskaperna för ett Hyper-V som kör som-konton |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Hämtar egenskaperna för ett Hyper-V som kör som-konton |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/usage/read | Hämtar användningar av en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/usage/read | Hämtar användningar av en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/write | Skapar eller uppdaterar Hyper-V-platsen |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/write | Skapar eller uppdaterar Hyper-V-platsen |
> | Åtgärd | Microsoft.OffAzure/Operations/read | Läser de exponerade åtgärderna |
> | Åtgärd | Microsoft.OffAzure/register/action | Registrerar prenumerationen med Microsoft.OffAzure-resursprovidern |
> | Åtgärd | Microsoft.OffAzure/register/action | Registrerar prenumerationen med Microsoft.OffAzure-resursprovidern |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/delete | Tar bort VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/delete | Tar bort VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/jobs/read | Hämtar egenskaperna för en VMware-jobb |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/jobs/read | Hämtar egenskaperna för en VMware-jobb |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/read | Hämtar egenskaperna för en VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/read | Hämtar egenskaperna för en VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/start/action | Starta VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/start/action | Starta VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stoppar VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stoppar VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Hämtar egenskaperna för en VMware-Åtgärdsstatus |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Hämtar egenskaperna för en VMware-Åtgärdsstatus |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/read | Hämtar egenskaperna för en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/read | Hämtar egenskaperna för en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/refresh/action | Uppdaterar objekt inom en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/refresh/action | Uppdaterar objekt inom en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Hämtar egenskaperna för en VMware kör som-konton |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Hämtar egenskaperna för en VMware kör som-konton |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/usage/read | Hämtar användningar av en VMware-plats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/usage/read | Hämtar användningar av en VMware-plats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/read | Hämtar egenskaperna för en VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/read | Hämtar egenskaperna för en VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/write | Skapar eller uppdaterar VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/write | Skapar eller uppdaterar VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/write | Skapar eller uppdaterar den VMware-webbplatsen |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/write | Skapar eller uppdaterar den VMware-webbplatsen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationalInsights/linkTargets/read | Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Använd ett kund-id som returneras av den här åtgärden i kund-id-egenskapen för åtgärden Skapa arbetsyta för att länka Azure-prenumerationen till en arbetsyta. |
> | Åtgärd | microsoft.operationalinsights/operations/read | Visar en lista över alla tillgängliga OperationalInsights Rest API-åtgärder. |
> | Åtgärd | Microsoft.OperationalInsights/register/action | Registrera en prenumeration på en resursprovider. |
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
> | Åtgärd | Microsoft.OperationalInsights/workspaces/purge/action | Ta bort angivna data från arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Läs data från ADAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Läs data från ADReplicationResult-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Läs data från ADSecurityAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Alert/read | Läs data från Alert-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Läs data från AlertHistory-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Läsa data från tabellen AppCenterError |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Läs data från ApplicationInsights-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Läsa data från tabellen AuditLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Läsa data från tabellen AutoscaleEvaluationsLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Läsa data från tabellen AutoscaleScaleActionsLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Läsa data från tabellen AWSCloudTrail |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Läs data från AzureActivity-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Läs data från AzureMetrics-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BoundPort/read | Läs data från BoundPort-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/CommonSecurityLog/read | Läs data från CommonSecurityLog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read | Läs data från ComputerGroup-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ConfigurationChange/read | Läs data från ConfigurationChange-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ConfigurationData/read | Läs data från ConfigurationData-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerImageInventory/read | Läs data från ContainerImageInventory-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerInventory/read | Läs data från ContainerInventory-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerLog/read | Läs data från ContainerLog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerNodeInventory/read | Läsa data från tabellen ContainerNodeInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ContainerServiceLog/read | Läs data från ContainerServiceLog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceAppCrash/read | Läs data från DeviceAppCrash-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceAppLaunch/read | Läs data från DeviceAppLaunch-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceCalendar/read | Läs data från DeviceCalendar-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceCleanup/read | Läs data från DeviceCleanup-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceConnectSession/read | Läs data från DeviceConnectSession-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceEtw/read | Läs data från DeviceEtw-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Läs data från DeviceHardwareHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Läs data från DeviceHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHeartbeat/read | Läs data från DeviceHeartbeat-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeHeartbeat/read | Läs data från DeviceSkypeHeartbeat-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Läs data från DeviceSkypeSignIn-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Läs data från DeviceSleepState-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHAppFailure/read | Läs data från DHAppFailure-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHAppReliability/read | Läs data från DHAppReliability-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHDriverReliability/read | Läs data från DHDriverReliability-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHLogonFailures/read | Läs data från DHLogonFailures-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHLogonMetrics/read | Läs data från DHLogonMetrics-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHOSCrashData/read | Läs data från DHOSCrashData-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHOSReliability/read | Läs data från DHOSReliability-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DHWipAppLearning/read | Läs data från DHWipAppLearning-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DnsEvents/read | Läs data från DnsEvents-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Läs data från DnsInventory-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Läs data från ETWEvent-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Event/read | Läs data från Event-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Läs data från ExchangeAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Läs data från ExchangeOnlineAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Läs data från Hearbeat-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Läsa data från tabellen HuntingBookmark |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Läs data från IISAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Läs data från InboundConnection-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Läsa data från tabellen IntuneAuditLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Läsa data från tabellen IntuneOperationalLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Läsa data från tabellen KubeEvents |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Läs data från KubeNodeInventory-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Läs data från KubePodInventory-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Läsa data från tabellen KubeServices |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Läs data från LinuxAuditLog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Läs data från MAApplication-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Läs data från MAApplicationHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Läs data från MAApplicationHealthAlternativeVersions-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Läs data från MAApplicationHealthIssues-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Läs data från MAApplicationInstance-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Läs data från MAApplicationInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Läs data från MAApplicationReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Läs data från MADeploymentPlan-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Läs data från MADevice-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Läsa data från tabellen MADeviceNotEnrolled |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Läs data från MADevicePnPHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Läs data från MADevicePnPHealthAlternativeVersions-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Läs data från MADevicePnPHealthIssues-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Läs data från MADeviceReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Läs data från MADriverInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Läs data från MADriverReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Läs data från MAOfficeAddin-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Läs data från MAOfficeAddinHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Läs data från MAOfficeAddinHealthIssues-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Läs data från MAOfficeAddinInstance-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Läs data från MAOfficeAddinInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Läs data från MAOfficeAddinReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Läs data från MAOfficeApp-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Läs data från MAOfficeAppHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Läs data från MAOfficeAppInstance-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Läs data från MAOfficeAppReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Läs data från MAOfficeBuildInfo-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Läs data från MAOfficeCurrencyAssessment-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Läs data från MAOfficeCurrencyAssessmentDailyCounts-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeDeploymentStatus/read | Läs data från MAOfficeDeploymentStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Läs data från MAOfficeMacroHealth-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Läs data från MAOfficeMacroHealthIssues-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Läs data från MAOfficeMacroIssueInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Läs data från MAOfficeMacroIssueReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Läs data från MAOfficeMacroSummary-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Läs data från MAOfficeSuite-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Läs data från MAOfficeSuiteInstance-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAProposedPilotDevices/read | Läs data från MAProposedPilotDevices-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsBuildInfo/read | Läs data från MAWindowsBuildInfo-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Läs data från MAWindowsCurrencyAssessment-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Läs data från MAWindowsCurrencyAssessmentDailyCounts-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsDeploymentStatus/read | Läs data från MAWindowsDeploymentStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Läs data från MAWindowsSysReqInstanceReadiness-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/NetworkMonitoring/read | Läs data från NetworkMonitoring-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/OfficeActivity/read | Läs data från OfficeActivity-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Operation/read | Läs data från Operation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Läs data från OutboundConnection-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Perf/read | Läs data från Perf-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ProtectionStatus/read | Läs data från ProtectionStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/read | Kör frågor över data i arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Läs data från ReservedAzureCommonFields-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Läs data från ReservedCommonFields-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Läs data från SCCMAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Läs data från SCOMAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read | Läs data från SecurityAlert-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read | Läs data från SecurityBaseline-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityBaselineSummary/read | Läs data från SecurityBaselineSummary-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Läs data från SecurityDetection-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityEvent/read | Läs data från SecurityEvent-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Läsa data från tabellen SecurityIoTRawEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Läsa data från tabellen SecurityRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Läs data från ServiceFabricOperationalEvent-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Läs data från ServiceFabricReliableActorEvent-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Läs data från ServiceFabricReliableServiceEvent-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Läs data från SfBAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Läs data från SfBOnlineAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Läs data från SharePointOnlineAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Läsa data från tabellen SigninLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SPAssessmentRecommendation/read | Läs data från SPAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Läs data från SQLAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SQLQueryPerformance/read | Läs data från SQLQueryPerformance-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Läs data från Syslog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Läs data från SysmonEvent-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Läser data från valfri anpassad logg |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Läsa data från tabellen ThreatIntelligenceIndicator |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Läs data från UAApp-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Läs data från UAComputer-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Läs data från UAComputerRank-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Läs data från UADriver-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Läs data från UADriverProblemCodes-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Läs data från UAFeedback-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Läs data från UAHardwareSecurity-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Läs data från UAIESiteDiscovery-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Läs data från UAOfficeAddIn-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Läs data från UAProposedActionPlan-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Läs data från UASysReqIssue-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Läs data från UAUpgradedComputer-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Update/read | Läs data från Update-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Läs data från UpdateRunProgress-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Läs data från UpdateSummary-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Usage/read | Läs data från Usage-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Läsa data från tabellen VMBoundPort |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Läsa data från tabellen VMConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Läs data från W3CIISLog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Läs data från WaaSDeploymentStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Läs data från WaaSInsiderStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Läs data från WaaSUpdateStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Läs data från WDAVStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Läs data från WDAVThreat-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Läs data från WindowsClientAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Läsa data från tabellen WindowsEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Läs data från WindowsFirewall-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Läs data från WindowsServerAssessmentRecommendation-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WireData/read | Läs data från WireData-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Läsa data från tabellen WorkloadMonitoringPerf |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Läs data från WUDOAggregatedStatus-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Läs data från WUDOStatus-tabellen |
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
> | Åtgärd | Microsoft.PolicyInsights/asyncOperationResults/read | Hämtar resultat för asynkron åtgärd. |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/action | Fråga efter information om principhändelser. |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/read | Fråga efter information om principhändelser. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/action | Fråga efter information om principtillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/read | Fråga efter information om principtillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/action | Fråga efter sammanfattning av senaste principtillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/read | Fråga efter sammanfattning av senaste principtillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Aktiverar en kompatibilitetsutvärdering för det valda omfånget. |
> | Åtgärd | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Fråga efter information om resurser som krävs av DeployIfNotExists-principer. |
> | Åtgärd | Microsoft.PolicyInsights/register/action | Registrerar Policy Insights-resursprovidern och aktiverar åtgärder på den. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/cancel/action | Avbryt pågående principreparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/delete | Ta bort principreparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/listDeployments/read | Visar lista över distributioner som krävs av en principreparation. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/read | Hämta principreparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/write | Skapa eller uppdatera principreparationer. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Portal/consoles/delete | Tar bort cloud shell-instans. |
> | Åtgärd | Microsoft.Portal/consoles/write | Skapa eller uppdatera en cloud shell-instans. |
> | Åtgärd | Microsoft.Portal/dashboards/delete | Tar bort instrumentpanelen från prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/read | Läser in instrumentpanelerna för prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/write | Lägg till eller ändra en instrumentpanel för en prenumeration. |
> | Åtgärd | Microsoft.Portal/register/action | Registrera i portalen |
> | Åtgärd | Microsoft.Portal/usersettings/delete | Tar bort användarinställningar för cloud shell. |
> | Åtgärd | Microsoft.Portal/usersettings/read | Läser molnet shell användarinställningar. |
> | Åtgärd | Microsoft.Portal/usersettings/write | Skapa eller uppdatera användarinställningen för cloud shell. |

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
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupStatus/action | Kontrollera Säkerhetskopieringsstatus för Recovery Services-valv |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validera funktioner |
> | Åtgärd | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Kontrollera Namntillgänglighet för resursen är en API för att kontrollera om resursnamnet är tillgänglig |
> | Åtgärd | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar Åtgärdsstatus för en viss åtgärd |
> | Åtgärd | Microsoft.RecoveryServices/operations/read | Åtgärden returnerar listan över åtgärder för en Resursprovider |
> | Åtgärd | Microsoft.RecoveryServices/register/action | Registrerar prenumerationen för den angivna Resursprovidern |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returnerar konfiguration för Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/write | Uppdaterar konfiguration för Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Ta bort en säkerhetskopia för Avsiktsskydd |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Hämta en säkerhetskopia för Avsiktsskydd |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhetskopia för Avsiktsskydd |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla behållare som kan skyddas |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Tar bort registrerad behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör förfrågan om arbetsbelastningar inom en behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Säkerhetskopierar ett skyddat objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Tar bort skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för utförd åtgärd på skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar information om det skyddade objektet |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Tillhandahåll direkt Objektåterställning för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återskapa återställningspunkter för skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla direkt Objektåterställning för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållarlistan |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Avbryt jobbet |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultat från jobbåtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export-jobb |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returnerar resultat från säkerhetskopiering för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar Säkerhetskopieringsstatus för Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Tar bort en Skyddsprincip |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Hämta Status för principåtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Skapar en Skyddsprincip |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returnerar lista över alla objekt som ska skyddas. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista över alla backup Protection avsikter |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Returnerar säkerhetskod Information för Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returnerar lagringskonfiguration för Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Uppdaterar lagringskonfiguration för Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattning av skyddade objekt och skyddade servrar för Recovery Services. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validera åtgärden på skyddat objekt |
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
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Använda återställningspunkt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Ta bort alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Redundansåtgärd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa alla återställningspunkter för replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Ta bort skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Skicka feedback |
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
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Läsa alla vCenters |
> | Åtgärd | Microsoft.RecoveryServices/vaults/usages/read | Läsa alla Valvanvändning |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
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
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Tar bort den haveriberedskapskonfiguration som är kopplat till namnområdet. Den här åtgärden kan bara anropas via det primära namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
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
> | Åtgärd | Microsoft.Relay/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resursbeskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
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
> | Åtgärd | Microsoft.ResourceHealth/events/read | Hämta hälsotillståndshändelser för tjänsten för angiven prenumeration |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Activated/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/InProgress/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Pending/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Resolved/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Updated/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Åtgärd | Microsoft.ResourceHealth/impactedResources/read | Hämta resurser som påverkas för angiven prenumeration |
> | Åtgärd | Microsoft.ResourceHealth/Operations/read | Hämta tillgängliga åtgärder för Microsoft ResourceHealth |
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
> | Åtgärd | Microsoft.Resources/deployments/write | Skapar eller uppdaterar en distribution. |
> | Åtgärd | Microsoft.Resources/links/delete | Tar bort en resurslänk. |
> | Åtgärd | Microsoft.Resources/links/read | Hämtar eller listar resurslänkar. |
> | Åtgärd | Microsoft.Resources/links/write | Skapar eller uppdaterar en resurslänk. |
> | Åtgärd | Microsoft.Resources/marketplace/purchase/action | Köper en resurs på Marketplace. |
> | Åtgärd | Microsoft.Resources/providers/read | Hämta listan över leverantörer. |
> | Åtgärd | Microsoft.Resources/resources/read | Hämta listan över resurser baserade på filter. |
> | Åtgärd | Microsoft.Resources/subscriptions/locations/read | Hämtar listan över platser som stöds. |
> | Åtgärd | Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
> | Åtgärd | Microsoft.Resources/subscriptions/providers/read | Hämtar eller listar resursprovidrar. |
> | Åtgärd | Microsoft.Resources/subscriptions/read | Hämtar listan över prenumerationer. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/delete | Tar bort resursgruppen och alla dess resurser. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Hämtar eller listar status för distributionsåtgärder. |
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
> | Åtgärd | Microsoft.Scheduler/jobcollections/read | Hämta jobbsamling |
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
> | Åtgärd | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Tar bort reglerna för virtuellt nätverk i ServiceBus-resursprovidern för det angivna virtuella nätverket |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/action | Uppdateringar Namespace-auktoriseringsregeln. Detta API är inaktuell. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace istället... Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för Namespace. Auktoriseringsregel för standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/write | Skapa auktoriseringsregler på en Namespace och uppdatera dess egenskaper. Den auktoriseringsregler för åtkomstbehörighet, den primära och sekundära nyckeln kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Tar bort den haveriberedskapskonfiguration som är kopplat till namnområdet. Den här åtgärden kan bara anropas via det primära namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Raderar Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Hämtar Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Skapar eller uppdaterar Event Grid-filtret som är kopplat till namnutrymmet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventhubs/read | Hämta listan över resursbeskrivningar för EventHub |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Ta bort IP-filterresurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Hämta IP-filterresurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Skapa IP-filterresurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hämtar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/write | Uppdaterar Meddelandeplanen för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som visas via MessagingPlan-resursen har flyttats till namnområdesresursen (överordnad) Namespace i senare API-versioner...<br>Den här åtgärden stöds inte på API-versionen 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrate/action | Migreringsåtgärd för namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Tar bort migreringskonfigurationen. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Hämtar migreringskonfigurationen som visar migreringens status och väntande replikeringsåtgärder |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Återställer migreringen från standard- till Premium-namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Det DNS som är kopplat till standardnamnområdet kopplas till Premium-namnområdet vilket slutför migreringen och stoppar synkronisering av resurser från standard- till Premium-namnområdet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Skapar eller konfigurationen för migrering av uppdateringar. Detta startar synkronisering av resurser från standard till premium-namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
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
> | Åtgärd | Microsoft.ServiceBus/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
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
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Ta bort regelresurs för virtuellt nätverk |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Hämtar regelresurs för virtuellt nätverk |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Skapa regelresurs för virtuellt nätverk |
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
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/partitions/read | Läs valfri partition |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Läs valfri replik |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/read | Läs valfri tjänst |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/statuses/read | Läs valfri tjänststatus |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/write | Skapa eller uppdatera valfri tjänst |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/write | Skapa eller uppdatera valfritt program |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/delete | Ta bort valfri programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/read | Läs valfri programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/versions/delete | Ta bort valfri version av programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/versions/read | Läs valfri version av programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/versions/write | Skapa eller uppdatera valfri version av programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applicationTypes/write | Skapa eller uppdatera valfri programtyp |
> | Åtgärd | Microsoft.ServiceFabric/clusters/delete | Ta bort valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/clusters/nodes/read | Läs valfri nod |
> | Åtgärd | Microsoft.ServiceFabric/clusters/read | Läs valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/clusters/statuses/read | Läs valfri klusterstatus |
> | Åtgärd | Microsoft.ServiceFabric/clusters/write | Skapa eller uppdatera valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/locations/clusterVersions/read | Läs valfri klusterversion |
> | Åtgärd | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Läs valfri klusterversion för en viss miljö |
> | Åtgärd | Microsoft.ServiceFabric/locations/operationresults/read | Läs valfritt åtgärdsresultat |
> | Åtgärd | Microsoft.ServiceFabric/locations/operations/read | Läs valfri åtgärd efter plats |
> | Åtgärd | Microsoft.ServiceFabric/operations/read | Läs valfri tillgänglig åtgärd |
> | Åtgärd | Microsoft.ServiceFabric/register/action | Registrera valfri åtgärd |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.SignalRService/locations/checknameavailability/action | Kontrollerar om ett namn är tillgängliga för användning med en ny SignalR-tjänst |
> | Åtgärd | Microsoft.SignalRService/locations/operationresults/signalr/read | Fråga efter statusen för en asynchorous-åtgärd |
> | Åtgärd | Microsoft.SignalRService/locations/usages/read | Hämta kvot användningar för Azure SignalR service |
> | Åtgärd | Microsoft.SignalRService/operationresults/read | Fråga efter statusen för en asynchorous-åtgärd |
> | Åtgärd | Microsoft.SignalRService/register/action | Registrerar 'Microsoft.SignalRService'-resursprovidern med en prenumeration |
> | Åtgärd | Microsoft.SignalRService/SignalR/delete | Ta bort hela SignalR-tjänsten |
> | Åtgärd | Microsoft.SignalRService/SignalR/listFeatures/read | Lista över anpassade FeatureFlags av en SignalR-tjänst. Som standard de som undantas. |
> | Åtgärd | Microsoft.SignalRService/SignalR/listkeys/action | Visa värdet för åtkomstnycklarna för SignalR i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/SignalR/read | Visa SignalR inställningar och konfigurationer i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/SignalR/regeneratekey/action | Ändra värdet för SignalR åtkomstnycklar i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/SignalR/restart/action | Starta om en Azure SignalR service i hanteringsportalen eller via API: et. Det kommer att vissa avbrott. |
> | Åtgärd | Microsoft.SignalRService/SignalR/switchFeatures/action | Växla på/av SignalR FeatureFlags med egenskaper som stöds i hanteringsportalen eller via API: et. |
> | Åtgärd | Microsoft.SignalRService/SignalR/write | Ändra SignalR inställningar och konfigurationer i hanteringsportalen eller via API: et |
> | Åtgärd | Microsoft.SignalRService/unregister/action | Avregistrerar 'Microsoft.SignalRService'-resursprovidern med en prenumeration |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/delete | Tar bort en programdefinition. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/read | Hämtar en lista över programdefinitioner. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/write | Lägg till eller ändra en programdefinition. |
> | Åtgärd | Microsoft.Solutions/applications/delete | Tar bort ett program. |
> | Åtgärd | Microsoft.Solutions/applications/read | Hämtar en lista över program. |
> | Åtgärd | Microsoft.Solutions/applications/write | Skapar ett program. |
> | Åtgärd | Microsoft.Solutions/jitRequests/delete | Ta bort en JitRequest |
> | Åtgärd | Microsoft.Solutions/jitRequests/read | Hämtar en lista över JitRequests |
> | Åtgärd | Microsoft.Solutions/jitRequests/write | Skapar en JitRequest |
> | Åtgärd | Microsoft.Solutions/locations/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.Solutions/register/action | Registrera dig för lösningar. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Sql/checkNameAvailability/action | Kontrollera om namn angivna servern är tillgänglig för etablering i hela världen för en viss prenumeration. |
> | Åtgärd | Microsoft.Sql/instancePools/delete | Tar bort en instans-pool |
> | Åtgärd | Microsoft.Sql/instancePools/read | Hämtar en instans-pool |
> | Åtgärd | Microsoft.Sql/instancePools/usages/read | Hämtar instansinformation för poolen om användningar. |
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
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Hämtar status för en brandvägg Regelåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesOperationResults/read | Hämtar status för en brandvägg Regelåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/delete | Tar bort en befintlig instans en redundansgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Kör planerad redundans i en befintlig instans en redundansgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kör forcerad redundans i en befintlig instans en redundansgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/read | Returnerar listan över instans redundans grupper eller hämtar egenskaperna för den angivna instansen redundansgruppen. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/write | Skapar en redundansgrupp för instansen med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna instansen redundansgruppen. |
> | Åtgärd | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Hämtar status för en instans pool-åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/instancePoolOperationResults/read | Hämtar resultat från en instans pool-åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Returnerar information om ett visst gränssnitt endpoint Azure en asynkron åtgärd |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Returnerar information om den angivna gränssnittet endpoint profil åtgärden |
> | Åtgärd | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Hämtar status för ett jobb agentåtgärden. |
> | Åtgärd | Microsoft.Sql/locations/jobAgentOperationResults/read | Hämtar resultat från en agentåtgärden för jobbet. |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionBackups/read | Visar en lista över säkerhetskopior för långsiktig kvarhållning för alla databaser på varje server i en plats |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Visar en lista över säkerhetskopior för långsiktig kvarhållning för varje databas på en server |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Tar bort en långsiktig kvarhållning av säkerhetskopia |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Visar en lista över säkerhetskopior för långsiktig kvarhållning för en databas |
> | Åtgärd | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Slutför hanterad databasåterställning |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hämtar pågående åtgärder på hanterade databasen transparent datakryptering |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Hämtar pågående åtgärder på hanterade databasen transparent datakryptering |
> | Åtgärd | Microsoft.Sql/locations/read | Hämtar tillgängliga platser för en viss prenumeration |
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
> | Åtgärd | Microsoft.Sql/managedInstances/databases/delete | Tar bort en befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instansdatabaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/read | Hämtar befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort etiketten känslighet för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Hämta information om den database threat principen konfigurerats på en viss hanterad databas |
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
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instanser |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för hanterade instanser |
> | Åtgärd | Microsoft.Sql/managedInstances/read | Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hantera instansen. |
> | Åtgärd | Microsoft.Sql/managedInstances/recoverableDatabases/read | Returnerar en lista över återställningsbara hanterade databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Hämtar en kort sikt bevarandeprincip för en förlorad hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Uppdaterar en kort sikt bevarandeprincip för en förlorad hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Returnerar en lista över återställningsbara bort hanterade databaser. |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Hämta information om den hanterade server threat principen konfigurerats på en viss hanterad server |
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
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/read | Hämta information om principen konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/write | Ändra princip för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Lista känslighet etiketter för en viss databas |
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
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Hämta principer för geo-säkerhetskopiering för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Skapa eller uppdatera en databas geobackup princip |
> | Åtgärd | Microsoft.Sql/servers/databases/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Hämtar en lista över tillgängliga underhållsfönster för den valda databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/read | Hämtar Underhåll windows-inställningar för den valda databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/write | Anger Underhåll windows-inställningar för den valda databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/metrics/read | Returnera mått för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/move/action | Byt namn på Azure SQL-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/operationResults/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/cancel/action | Avbryter Azure SQL Database väntande asynkron åtgärd som inte har slutförts ännu. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/read | Returnera listan över åtgärder som utförs på databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/pause/action | Pause Azure SQL Datawarehouse Database |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/read | Hämta lista över scheman för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta listan över kolumner i en tabell |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort etiketten känslighet för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighet rekommendationer för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighetsetikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/read | Hämta listan över tabeller i en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Hämta lista över indexrekommendationer på en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Tillämpa indexrekommendation |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Hämta information om den database threat principen konfigurerats på en viss databas |
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
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Returnera typer av mått som är tillgängliga för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metrics/read | Returnera mått för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Avbryter Azure SQL-databaspool väntande asynkron åtgärd som inte har slutförts ännu. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/read | Returnera listan över åtgärder som utförs på den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för servrar |
> | Åtgärd | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Hämta mått för rekommenderade elastic database-pooler för en viss server |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/read | Hämta rekommendationer för elastic database-pooler att minska kostnaderna eller förbättra prestanda baserat på historiska resursanvändningen |
> | Åtgärd | Microsoft.Sql/servers/recoverableDatabases/read | Den här åtgärden används för haveriberedskap för live-databas för att återställa databasen till senast fungerande god säkerhetskopieringspunkt. Den returnerar information om den senaste säkerhetskopian som bra men doesn\u0027t faktiskt återställa databasen. |
> | Åtgärd | Microsoft.Sql/servers/replicationLinks/read | Returnera listan över replikering länkar eller hämtar egenskaperna för den angivna replikeringslänkar. |
> | Åtgärd | Microsoft.Sql/servers/restorableDroppedDatabases/read | Hämta en lista över databaser som har tagits bort på en viss server som är fortfarande inom bevarandeprincip. |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Hämta resultatet av Skrivåtgärden server threat identifiering av princip |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/read | Hämta information om den server threat principen konfigurerats på en viss server |
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
> | Åtgärd | Microsoft.Storage/checknameavailability/read | Kontrollerar att kontonamnet är giltigt och används. |
> | Åtgärd | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.Storage om att det virtuella nätverket eller undernätet tas bort |
> | Åtgärd | Microsoft.Storage/locations/usages/read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |
> | Åtgärd | Microsoft.Storage/operations/read | Avsöker status för en asynkron åtgärd. |
> | Åtgärd | Microsoft.Storage/register/action | Registrerar prenumerationen för lagringsresursprovidern och gör det möjligt att skapa lagringskonton. |
> | Åtgärd | Microsoft.Storage/skus/read | Listar SKU:erna som stöds av Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Returnerar resultatet av att lägga till blobinnehåll |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en blob |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteAutomaticSnapshot/action | Returnerar resultatet av borttagningen av en automatisk ögonblicksbild |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller bloblista |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Returnerar resultatet av blobkommandot |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en blob |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Ta bort bevarande av juridiska skäl för blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Ta bort oföränderlighetsprincipen för blobcontainern |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Utöka oföränderlighetsprincip för blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Lås oföränderlighetsprincip för blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Hämta oföränderlighetsprincip för blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Placera oföränderlighetsprincip för blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Returnerar resultatet av att leasa blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar lista över containrar |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ställ in bevarande av juridiska skäl för blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av korrigera blobcontainer |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att put blob-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användardelegeringsnyckel för Blob Service |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read | Returnerar egenskaper för Blob Service eller statistik |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/write | Returnerar resultatet av att placera Blob Service-egenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/delete | Tar bort ett befintligt lagringskonto. |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/fileShare/delete | Tillåter användaren att ta bort filresursen |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/fileShare/read | Tillåter användaren att läsa filresursen |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/fileShare/write | Tillåter användaren att skriva till en filresurs |
> | Åtgärd | Microsoft.Storage/storageAccounts/listAccountSas/action | Returnerar SAS-token för konto för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listServiceSas/action | Returnerar SAS-token för tjänst för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returnerar resultatet av att ta bort en kö |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Returnerar resultatet av att lägga till ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Returnerar resultatet av att ta bort ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Returnerar resultatet av att bearbeta ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returnerar ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Returnerar resultatet av att skriva ett meddelande |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller kölista. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returnerar resultatet av att skriva en kö |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Returnerar egenskaper för kötjänst eller statistik. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/write | Returnerar resultatet av att ställa in egenskaper för kötjänst |
> | Åtgärd | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Återkallar alla användardelegeringsnycklar för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Skapa/uppdatera diagnostikinställningar för lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/write | Skapar ett lagringskonto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna, eller lägger till anpassad domän för det angivna lagringskontot. |
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
> | Åtgärd | microsoft.web/hostingenvironments/metricdefinitions/read | Hämta som är värd för definitioner av mätvärden för miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Hämta som är värd för miljöer definitioner av mätvärden för flera roller pooler. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/metrics/read | Hämta som är värd för miljöer flera roller pooler mått. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Hämta egenskaperna för en adresspool på klientdelen i en App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/skus/read | Hämta som är värd för miljöer flera roller pooler SKU: er. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/usages/read | Hämta som är värd för miljöer flera roller pooler användningar. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Skapa en ny adresspool på klientdelen i en App Service Environment eller uppdatera en befintlig |
> | Åtgärd | microsoft.web/hostingenvironments/operations/read | Hämta som är värd Operations miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Hämta nätverksslutpunkter alla utgående beroenden. |
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
> | Åtgärd | microsoft.web/sites/newpassword/action | Nytt lösenord Web Apps. |
> | Åtgärd | microsoft.web/sites/operationresults/read | Hämta Åtgärdsresultat för Web Apps. |
> | Åtgärd | microsoft.web/sites/operations/read | Hämta åtgärder för Web Apps. |
> | Åtgärd | microsoft.web/sites/perfcounters/read | Få prestandaräknare för Web Apps. |
> | Åtgärd | microsoft.web/sites/premieraddons/delete | Ta bort Premier-tillägg för Web Apps. |
> | Åtgärd | microsoft.web/sites/premieraddons/read | Hämta Web Apps Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/premieraddons/write | Uppdatera webb Apps Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/privateaccess/read | Hämta data om privat plats åtkomst aktivering och auktoriserade virtuella nätverk som har åtkomst till webbplatsen. |
> | Åtgärd | microsoft.web/sites/processes/read | Hämta processer för Web Apps. |
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
