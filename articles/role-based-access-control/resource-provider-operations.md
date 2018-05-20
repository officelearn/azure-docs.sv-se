---
title: Azure Resource Manager resource provider operations | Microsoft Docs
description: Visar funktioner som är tillgängliga för Microsoft Azure Resource Manager-resursprovidrar
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/11/2018
ms.author: rolyon
ms.openlocfilehash: 08059da95baa9e70b8dba2dd847d0b28669778b7
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Provideråtgärder i Azure Resource Manager resurs

Den här artikeln visar funktioner som är tillgängliga för varje resursprovider för Azure Resource Manager. Dessa åtgärder kan användas i [anpassade roller](custom-roles.md) att tillhandahålla detaljerade [rollbaserad åtkomstkontroll (RBAC)](overview.md) till resurser i Azure. Åtgärden strängar har följande format: `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

Resurs-provideråtgärder utvecklas alltid. Om du vill hämta de senaste åtgärderna använder [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) eller [az providerlistan åtgärden](/cli/azure/provider/operation#az-provider-operation-list).

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AAD/domainServices/delete | Tar bort Domain Services. |
> | Åtgärd | Microsoft.AAD/domainServices/read | Läser Domain Services. |
> | Åtgärd | Microsoft.AAD/domainServices/write | Skriva Domain Services |
> | Åtgärd | Microsoft.AAD/locations/operationresults/read | Läsa status för en asynkron åtgärd. |
> | Åtgärd | Microsoft.AAD/Operations/read | Lokaliserad beskrivning för åtgärden, som det ska visas för användaren. |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.aadiam/diagnosticsettings/delete | Om du tar bort en diagnostikinställningen |
> | Åtgärd | Microsoft.aadiam/diagnosticsettings/Read | Läser en diagnostikinställningen |
> | Åtgärd | Microsoft.aadiam/diagnosticsettings/Write | Skriva ett diagnostikinställningen |
> | Åtgärd | Microsoft.aadiam/diagnosticsettingscategories/Read | Läser en diagnostikinställningen kategorier |
> | Åtgärd | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | microsoft.aadiam/tenants/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | microsoft.aadiam/tenants/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för klienter |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Addons/operations/read | Hämtar stöds RP-åtgärder. |
> | Åtgärd | Microsoft.Addons/register/action | Registrera den angivna prenumerationen med Microsoft.Addons |
> | Åtgärd | Microsoft.Addons/supportProviders/listsupportplaninfo/action | Visar en lista över aktuella planen supportinformation för den angivna prenumerationen. |
> | Åtgärd | Microsoft.Addons/supportProviders/supportPlanTypes/delete | Tar bort den angivna kanoniska supportavtal |
> | Åtgärd | Microsoft.Addons/supportProviders/supportPlanTypes/read | Hämta status för angivna kanoniska support-plan. |
> | Åtgärd | Microsoft.Addons/supportProviders/supportPlanTypes/write | Lägger till kanoniska stöd plantypen. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/action | Skapa en ny skog för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Hämtar alla servrar för namnet på angivna tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/alerts/read | Hämtar information om aviseringar för skogen som alertid, avisering aktiveras datum, senaste identifierade, varning aviseringsbeskrivningen, senaste uppdaterade, varning nivå, varning tillstånd, varning Felsöka länkar osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/configuration/read | Hämtar tjänstkonfigurationen för skogen. Exempel - skogsnamnet, Functionla nivå, domännamngivning master FSMO-rollen, FSMO schemahanterare osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/delete | Tar bort en tjänst och den servrar samt hälsa data. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Hämtar information om domäner och platser för skogen. Aviseringar, egenskaper som domänens funktionsnivå, skog, infrastrukturhanteraren, PDC, exempel-hälsotillstånd, aktiva aviseringar är lösta RID master osv.  |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Hämtar inställningen för inställningar för skogen.<br>Exempel - MetricCounterName som ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Inställningar för Användargränssnittet diagram osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Hämtar skog sammanfattning för den angivna skogen som namn på skog, antalet domäner under den här skogen, antalet platser och webbplatser information osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Hämtar listan över mått stöds för en viss tjänst.<br>För exempel extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFS-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomainService.<br>Kör profil svarstid, TCP-anslutningar upprättas, insikter Agent privata byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Detta API hämtar ges en tjänst informationen mått.<br>Detta API kan exempelvis användas för att få information som rör: extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomain Service.<br>Kör profil latens, TCP-anslutningar som upprättats insikter Agent privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Detta API hämtar listan över alla publicerats så ADDomainServices för premium-klientorganisation. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/read | Hämtar information om tjänsten för namnet på angivna tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Hämtar replikeringsinformation om för alla servrar för namnet på angivna tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Hämtar antalet domänkontrollanter och deras replikeringsfel eventuella. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Hämtar slutföra domain controller lista tillsammans med replikeringsinformation för den angivna skogen. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Lägg till en server-instans till tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Under server registreringen av ADDomainService anropas detta api för att hämta autentiseringsuppgifterna för onboarding av nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Tar bort en server för en viss tjänst och klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/write | Skapar eller uppdaterar ADDomainService-instans för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/action | Klientkonfiguration för uppdateringar. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/read | Läser konfigurationen av klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/write | Skapar en klientkonfiguration. |
> | Åtgärd | Microsoft.ADHybridHealthService/logs/contents/read | Hämtar innehållet i agentinstallation och registreringsloggar som lagras i blob. |
> | Åtgärd | Microsoft.ADHybridHealthService/logs/read | Hämtar agentinstallation och registreringsloggar för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/operations/read | Hämtar lista med åtgärder som stöds av systemet. |
> | Åtgärd | Microsoft.ADHybridHealthService/register/action | Registrerar Tjänstresursprovider för ADHybrid hälsa och kan skapa ADHybrid-tjänsten för hälsotillstånd resurs. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/availabledeployments/read | Hämtar listan över tillgängliga regioner som används av DevOps för att stödja kunden incidenter. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/badpassword/read | Hämtar listan över felaktiga lösenord för alla användare i Active Directory Federation Service. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Hämtar Blob SAS URI som innehåller status och eventuell resultatet nyligen köas Rapportjobbet för frekvensen av felaktigt användarnamn/lösenord försöker per användar-ID per IP-adress per dag för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Hämtar listan över DevOps godkänt innehavare. Används vanligtvis för teknisk support. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/isdevops/read | Hämtar ett värde som anger wheather teannt är DevOps godkänt eller inte. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Uppdaterar userid(objectid) för de valda dev ops. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Hämtar valda distribution för den angivna innehavaren. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Angivna klient-id hämtar klienten lagringsplats. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Hämtar geoplats som kommer att komma åt data. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/action | Uppdaterar en tjänstinstans i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringar för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringar för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Beroende på en funktion verifierar namn om en tjänst har allt som krävs för att använda den funktionen. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/delete | Tar bort en instans av tjänsten på klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/exporterrors/read | Hämtar exportfel för en given synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/exportstatus/read | Hämtar export-status för en viss tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/feedbacktype/feedback/read | Hämtar aviseringar feedback för en viss tjänst och en server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metricmetadata/read | Hämtar listan över mått stöds för en viss tjänst.<br>För exempel extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFS-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomainService.<br>Kör profil svarstid, TCP-anslutningar upprättas, insikter Agent privata byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Detta API hämtar ges en tjänst genomsnittet för mått för en viss tjänst.<br>Detta API kan exempelvis användas för att få information som rör: extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomain Service.<br>Kör profil latens, TCP-anslutningar som upprättats insikter Agent privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/read | Detta API hämtar ges en tjänst informationen mått.<br>Detta API kan exempelvis användas för att få information som rör: extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomain Service.<br>Kör profil latens, TCP-anslutningar som upprättats insikter Agent privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Detta API hämtar ges en tjänst aggregerade vyn för mått för en viss tjänst.<br>Detta API kan exempelvis användas för att få information som rör: extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomain Service.<br>Kör profil latens, TCP-anslutningar som upprättats insikter Agent privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Lägg till eller uppdaterar konfigurationen av övervakningen för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Hämtar övervakningskonfigurationer för en viss tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Lägg till eller uppdaterar övervakningskonfigurationer för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/premiumcheck/read | Detta API hämtar listan över alla publicerats så tjänster för en premium-klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/read | Läser tjänstinstanser i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/details/read | Hämtar rapport för övre 50 användarna med felaktigt lösenord fel från de senaste 7 dagarna |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/action | Skapar en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Läser aviseringar för en server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Under registreringen server kallas detta api för att hämta autentiseringsuppgifterna för onboarding av nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | För en given server hämtar en lista med datatyper som överförs av servrarna och den senaste tidpunkten för varje överför i detta API. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/delete | Tar bort en server-instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Hämtar exportera synkroniseringsfel-information för en viss synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Detta API hämtar ges en tjänst informationen mått.<br>Detta API kan exempelvis användas för att få information som rör: extranät kontoutelåsningar, totalt antal misslyckade begäranden, utestående Token-förfrågningar (Proxy), Token-begäranden/sek etc för ADFederation-tjänsten.<br>NTLM-autentiseringar per sekund, lyckade LDAP-bindningar per sekund, LDAP-bindning tid, aktiva LDAP-trådar, Kerberos-autentiseringar per sekund o.s.v. ATQ trådar totala för ADDomain Service.<br>Kör profil latens, TCP-anslutningar som upprättats insikter Agent privata byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/read | Läser serverinstansen i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Hämtar tjänstkonfigurationen för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Hämtar funktionen vitlistning av status för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/write | Skapar en instans av tjänsten på klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/unregister/action | Avregistrerar prenumerationen för ADHybrid hälsa Tjänstresursprovider. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Advisor/configurations/read | Hämta konfigurationer |
> | Åtgärd | Microsoft.Advisor/configurations/write | Konfiguration av skapar/uppdateringar |
> | Åtgärd | Microsoft.Advisor/generateRecommendations/action | Skapar rekommendationer |
> | Åtgärd | Microsoft.Advisor/generateRecommendations/read | Hämtar generera rekommendationer status |
> | Åtgärd | Microsoft.Advisor/operations/read | Hämtar åtgärder för Microsoft Advisor |
> | Åtgärd | Microsoft.Advisor/recommendations/read | Läser rekommendationer |
> | Åtgärd | Microsoft.Advisor/recommendations/suppressions/delete | Tar bort Undertryckning |
> | Åtgärd | Microsoft.Advisor/recommendations/suppressions/read | Hämtar suppressions |
> | Åtgärd | Microsoft.Advisor/recommendations/suppressions/write | Skapat/uppdaterat suppressions |
> | Åtgärd | Microsoft.Advisor/register/action | Registrerar prenumerationen för Microsoft Advisor |
> | Åtgärd | Microsoft.Advisor/suppressions/delete | Tar bort Undertryckning |
> | Åtgärd | Microsoft.Advisor/suppressions/read | Hämtar suppressions |
> | Åtgärd | Microsoft.Advisor/suppressions/write | Skapat/uppdaterat suppressions |
> | Åtgärd | Microsoft.Advisor/unregister/action | Avregistrerar prenumeration för Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AlertsManagement/alerts/changestate/action | Ändra tillståndet för aviseringen. |
> | Åtgärd | Microsoft.AlertsManagement/alerts/read | Hämta alla aviseringar för inkommande trafik. |
> | Åtgärd | Microsoft.AlertsManagement/alertsSummary/read | Få en översikt över aviseringar |
> | Åtgärd | Microsoft.AlertsManagement/Operations/read | Läser de åtgärder som anges |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/changestate/action | Ändra tillstånd för gruppen smart |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/read | Hämta alla smarta grupper för inkommande trafik |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Kontrollerar att namnet angivna Analysis Server är giltigt och inte i användning. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationresults/read | Hämtar information om resultatet för den angivna åtgärden. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationstatuses/read | Hämtar information om statusen för den angivna åtgärden. |
> | Åtgärd | Microsoft.AnalysisServices/operations/read | Hämtar information av åtgärder |
> | Åtgärd | Microsoft.AnalysisServices/register/action | Registrerar Analysis Services-resursprovidern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/delete | Tar bort analysserver. |
> | Åtgärd | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Visa status för gatewayen som associeras med servern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för Analysis Server |
> | Åtgärd | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för Analysis Server |
> | Åtgärd | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för servrar |
> | Åtgärd | Microsoft.AnalysisServices/servers/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Analysis Server |
> | Åtgärd | Microsoft.AnalysisServices/servers/read | Hämtar information för den angivna Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/resume/action | Återupptar analysserver. |
> | Åtgärd | Microsoft.AnalysisServices/servers/skus/read | Hämta information om tillgängliga SKU för servern |
> | Åtgärd | Microsoft.AnalysisServices/servers/suspend/action | Pausar analysserver. |
> | Åtgärd | Microsoft.AnalysisServices/servers/write | Skapar eller uppdaterar den angivna Analysis-servern. |
> | Åtgärd | Microsoft.AnalysisServices/skus/read | Hämtar information av SKU: er |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ApiManagement/checkNameAvailability/read | Kontrollerar om tjänstens namn är tillgängligt |
> | Åtgärd | Microsoft.ApiManagement/operations/read | Läsa alla API åtgärder som är tillgängliga för Microsoft.ApiManagement resurs |
> | Åtgärd | Microsoft.ApiManagement/register/action | Registrera prenumerationen för Microsoft.ApiManagement resursprovidern |
> | Åtgärd | Microsoft.ApiManagement/reports/read | Hämta rapporter sammanställs efter tidsperioder, geografisk region, utvecklare, produkter, API: er, åtgärder, prenumeration och byRequest. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/delete | Ta bort befintliga API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/delete | Ta bort befintliga diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/loggers/delete | Ta bort mappning av en logg med en diagnostikinställningen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/loggers/read | Hämta lista över befintliga diagnostiska loggar tangenttryckningar |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/loggers/write | Mappa loggaren till en diagnostikinställningen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/read | Hämta lista över diagnostik eller hämta information för diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/apis/diagnostics/write | Lägger till nya diagnostiska eller uppdaterar befintliga diagnosinformation |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/delete | Tar bort befintliga bifogad fil |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/read | Information om Erhåll problemet bifogade filer eller hämtar API Management problemet bifogad fil |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/write | Lägg till api problemet bifogad fil |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/delete | Tar bort befintliga kommentar |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/read | Hämtar Probleminformation kommentarer eller hämtar API Management problemet kommentar |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/write | Lägg till en kommentar för api-problem |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/delete | Tar bort befintliga problemet |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/read | Hämta problem som är associerade med API: et eller hämtar API Management Probleminformation |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/write | Lägg till api problemet eller uppdatera api-problem |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/delete | Ta bort befintliga API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/delete | Ta bort principkonfigurationen från principer för API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/read | Hämta principer för API-åtgärd eller Get princip konfigurationsinformation för API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policies/write | Ange information om principen för API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/delete | Ta bort principkonfigurationen från åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/read | Hämta information om principen för åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/policy/write | Ange information om konfiguration av principen för åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/read | Hämta listan över befintliga API: et eller hämta information om API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/delete | Ta bort associationen mellan befintlig tagg med befintliga åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/read | Hämta taggarna associerade med åtgärden eller hämta taggen information |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/write | Associera befintlig tagg med befintliga åtgärden |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/write | Skapa nya API-åtgärden eller uppdatera befintliga API-åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operationsByTags/read | Hämta lista över kopplingar åtgärden-tagg |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/delete | Ta bort principkonfigurationen från API-principer |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/read | Hämta principer för API: et eller Get princip konfigurationsinformation för API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policies/write | Ange information om principen för API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/delete | Ta bort principkonfigurationen från API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/read | Hämta information om principen för API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/policy/write | Ange information om principen för API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/products/read | Hämta alla produkter som API: et är en del av |
> | Åtgärd | Microsoft.ApiManagement/service/apis/read | Hämta lista över alla registrerade API: er eller hämta information om API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/delete | Tar bort alla versioner av API eller ta bort API-versionen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/read | Hämta versioner för en API eller hämta information om API reelase |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/write | Skapa ny API-version eller uppdatera befintliga API-versionen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/revisions/delete | Tar bort alla revisioner av API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/revisions/read | Hämta revisioner som hör till en API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/delete | Tar bort befintliga schemat |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/document/read | Hämta det dokument som beskriver schemat |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/document/write | Uppdatera dokumentet som beskriver schemat |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/read | Hämtar de scheman som används av API: et eller hämtar alla scheman för en given API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/write | Anger de scheman som används av API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/delete | Ta bort taggen beskrivning från API: et |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/read | Hämta taggar beskrivningar i omfånget för API: et eller hämta taggen beskrivning i omfånget för API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tagDescriptions/write | Skapa/ändra taggen beskrivning i omfattningen av API |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/delete | Ta bort den befintliga API-tagg kopplingen |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/read | Hämta alla API-tagg associationen för API: et eller hämta information om API-tagg association |
> | Åtgärd | Microsoft.ApiManagement/service/apis/tags/write | Lägga till en ny koppling för API-tagg |
> | Åtgärd | Microsoft.ApiManagement/service/apis/write | Skapa nya API eller uppdatera befintliga API-information |
> | Åtgärd | Microsoft.ApiManagement/service/apisByTags/read | Hämta lista över API-tagg kopplingar |
> | Åtgärd | Microsoft.ApiManagement/service/api-version-sets/delete | Ta bort befintliga VersionSet |
> | Åtgärd | Microsoft.ApiManagement/service/api-version-sets/read | Hämta lista över version grupp entiteter eller hämtar information om en VersionSet |
> | Åtgärd | Microsoft.ApiManagement/service/api-version-sets/versions/read | Hämta lista över version entiteter |
> | Åtgärd | Microsoft.ApiManagement/service/api-version-sets/write | Skapa nya VersionSet eller uppdatera den befintliga VersionSet information |
> | Åtgärd | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Uppdaterar de Microsoft.ApiManagement resurser som körs i virtuella nätverk för att hämta uppdaterade nätverksinställningar. |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/delete | Ta bort den befintliga auktorisering server |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/read | Hämta listan över servrar för auktorisering eller hämta information om auktorisering server |
> | Åtgärd | Microsoft.ApiManagement/service/authorizationServers/write | Skapa en ny auktoriserings-server eller uppdatera information om en befintlig server auktorisering |
> | Åtgärd | Microsoft.ApiManagement/service/backends/delete | Ta bort befintlig serverdel |
> | Åtgärd | Microsoft.ApiManagement/service/backends/read | Hämta listan över serverdelar eller hämta information om backend |
> | Åtgärd | Microsoft.ApiManagement/service/backends/reconnect/action | Skapa en begäran om återanslutningen |
> | Åtgärd | Microsoft.ApiManagement/service/backends/write | Lägg till en ny serverdel eller uppdatera befintliga backend-information |
> | Åtgärd | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiering API-tjänsten till den angivna behållaren i en användare som storage-konto |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/delete | Ta bort befintligt certifikat |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/read | Hämta listan över certifikat eller hämta information om certifikat |
> | Åtgärd | Microsoft.ApiManagement/service/certificates/write | Lägg till nytt certifikat |
> | Åtgärd | Microsoft.ApiManagement/service/delete | Ta bort en instans för API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/delete | Ta bort befintliga diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/loggers/delete | Ta bort mappning av en logg med en diagnostikinställningen |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/loggers/read | Hämta lista över befintliga diagnostiska loggar tangenttryckningar |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/loggers/write | Mappa loggaren till en diagnostikinställningen |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/read | Hämta lista över diagnostik eller hämta information för diagnostik |
> | Åtgärd | Microsoft.ApiManagement/service/diagnostics/write | Lägger till nya diagnostiska eller uppdaterar befintliga diagnosinformation |
> | Åtgärd | Microsoft.ApiManagement/service/getssotoken/action | Hämtar SSO-token som kan användas för att logga in i API Management-tjänsten äldre portal som administratör |
> | Åtgärd | Microsoft.ApiManagement/service/groups/delete | Ta bort en befintlig grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/read | Hämta listan över grupper eller hämtar information om en grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/delete | Ta bort befintliga användare från en befintlig grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/read | Hämta lista över gruppanvändare |
> | Åtgärd | Microsoft.ApiManagement/service/groups/users/write | Lägg till befintliga användare i en befintlig grupp |
> | Åtgärd | Microsoft.ApiManagement/service/groups/write | Skapa ny grupp eller uppdatera befintliga gruppinformation |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/delete | Ta bort den befintliga identitetsleverantören. |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/read | Hämta lista över identitetsleverantörer eller hämta information av identitetsleverantören. |
> | Åtgärd | Microsoft.ApiManagement/service/identityProviders/write | Skapa en ny identitetsleverantör eller uppdatera information om en befintlig identitetsleverantör |
> | Åtgärd | Microsoft.ApiManagement/service/locations/networkstatus/read | Hämtar nätverksstatus för åtkomst av resurser som tjänsten är beroende på plats. |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/delete | Ta bort befintliga transaktionsloggfiler |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/read | Hämta listan över loggar tangenttryckningar eller hämta information om loggning |
> | Åtgärd | Microsoft.ApiManagement/service/loggers/write | Lägg till ny loggare eller uppdatera information om befintliga transaktionsloggfiler |
> | Åtgärd | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/networkstatus/read | Hämtar nätverksstatus för åtkomst av resurser som tjänsten är beroende av. |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/action | Skickar meddelandet till en angiven användare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/read | Hämtar alla API Management publisher meddelanden eller hämta API Management publisher detaljer |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/delete | Tar bort befintlig e-postadress som är associerad med ett meddelande |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/read | Hämta e-postmottagare som är associerade med API Management Publisher meddelande |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientEmails/write | Skapa ny e-postmottagare för meddelandet |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/delete | Tar bort användare som är kopplad till den meddelandemottagare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/read | Hämta mottagaren användare som associeras med meddelandet |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/recipientUsers/write | Lägg till användare i meddelandemottagare |
> | Åtgärd | Microsoft.ApiManagement/service/notifications/write | Skapa eller API uppdateringshantering publisher meddelande |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/delete | Ta bort befintliga OpenID Connect-providern |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/read | Hämta lista över OpenID Connect providers eller hämta information för OpenID Connect Provider |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/write | Skapa en ny information för OpenID Connect Provider eller uppdatering av en befintlig OpenID Connect-Provider |
> | Åtgärd | Microsoft.ApiManagement/service/operationresults/read | Hämtar aktuell status för långvarig åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/policies/delete | Ta bort principkonfigurationen från innehavaren principer |
> | Åtgärd | Microsoft.ApiManagement/service/policies/read | Hämta principer för klient- eller Get princip konfigurationsinformation för klient |
> | Åtgärd | Microsoft.ApiManagement/service/policies/write | Ange information om principen för klienten |
> | Åtgärd | Microsoft.ApiManagement/service/policySnippets/read | Hämta alla princip kodavsnitt |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/read | Hämta logga in inställningar för portalen eller Get logga In inställningarna för portalen eller hämta delegeringsinställningar för portalen |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/write | Uppdatera registrera dig inställningar eller uppdatering registrera dig inställningar eller uppdatering-logga In inställningar eller uppdatering-logga In inställningar eller delegeringsinställningar för uppdatering eller uppdatering delegeringsinställningar |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/delete | Ta bort befintliga API från befintliga produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/read | Hämta lista över API: er som lagts till i befintliga produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/apis/write | Lägga till befintliga API för befintliga produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/delete | Ta bort befintliga produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/delete | Ta bort associationen av befintlig developer grupp med befintliga produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/read | Hämta lista över developer grupper som är associerade med produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/groups/write | Associera befintlig developer grupp med befintliga produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/delete | Ta bort principkonfigurationen från produkt-principer |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/read | Hämta principer för produkt eller Get princip konfigurationsinformation för produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/policies/write | Ange information om principen för produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/delete | Ta bort principkonfigurationen från befintliga produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/read | Hämta konfiguration av befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/policy/write | Konfigurera principen för befintliga produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/read | Hämta lista över produkter eller hämta information om produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/subscriptions/read | Hämta listan över prenumerationer för produkten |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/delete | Ta bort associationen mellan befintlig tagg med befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/read | Hämta taggarna associerade med produkt eller hämta taggen information |
> | Åtgärd | Microsoft.ApiManagement/service/products/tags/write | Associera befintlig tagg med befintlig produkt |
> | Åtgärd | Microsoft.ApiManagement/service/products/write | Skapa en ny produkt eller uppdatera befintliga produktinformation |
> | Åtgärd | Microsoft.ApiManagement/service/properties/delete | Tar bort befintlig egenskap |
> | Åtgärd | Microsoft.ApiManagement/service/properties/read | Hämtar information om den angivna egenskapen eller hämtar en lista över alla egenskaper |
> | Åtgärd | Microsoft.ApiManagement/service/properties/write | Skapar en ny egenskap eller uppdaterar värdet för den angivna egenskapen |
> | Åtgärd | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/periods/read | Hämta räknarvärdet kvoten för perioden |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/periods/write | Ange kvoten aktuella räknarvärdet |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/read | Hämta värden för kvot |
> | Åtgärd | Microsoft.ApiManagement/service/quotas/write | Ange kvoten aktuella räknarvärdet |
> | Åtgärd | Microsoft.ApiManagement/service/read | Läsa in metadata för en instans för API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/reports/read | Hämta rapport sammanställs efter tidsperioder eller Get-rapport som sammanställs efter geografisk region eller Get-rapport som aggregeras av utvecklare.<br>eller hämta rapport sammanställs efter produkter.<br>eller hämta rapport som har aggregerats med API: er eller Get rapporten sammanställs efter operations eller Get-rapport som aggregeras av prenumerationen.<br>eller hämta begäranden rapportdata |
> | Åtgärd | Microsoft.ApiManagement/service/restore/action | Återställa API Management-tjänsten från den angivna behållaren i en användare har angett storage-konto |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/delete | Ta bort prenumerationen. Den här åtgärden kan användas för att ta bort prenumerationen |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/read | Hämta en lista över produktprenumeration eller hämta information om produkten prenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/regeneratePrimaryKey/action | Återskapa primärnyckeln för prenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/regenerateSecondaryKey/action | Återskapa en sekundär nyckel för prenumeration |
> | Åtgärd | Microsoft.ApiManagement/service/subscriptions/write | Prenumerera på en befintlig användare till en befintlig produkt eller uppdatera befintliga prenumerationsinformation. Den här åtgärden kan användas för att förnya prenumerationen |
> | Åtgärd | Microsoft.ApiManagement/service/tagResources/read | Hämta lista över taggar med associerade resurser |
> | Åtgärd | Microsoft.ApiManagement/service/tags/delete | Ta bort befintlig tagg |
> | Åtgärd | Microsoft.ApiManagement/service/tags/read | Hämta lista över taggar eller hämta information om tagg |
> | Åtgärd | Microsoft.ApiManagement/service/tags/write | Lägg till ny tagg eller uppdatera information om befintliga tagg |
> | Åtgärd | Microsoft.ApiManagement/service/templates/delete | Återställ standardmall för API Management-e-post |
> | Åtgärd | Microsoft.ApiManagement/service/templates/read | Hämtar alla postmallar för e-eller hämtar API Management e-mallinformation |
> | Åtgärd | Microsoft.ApiManagement/service/templates/write | Skapa eller uppdatera API-hantering e-post eller uppdaterar API Management e-mallen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/delete | Ta bort konfiguration för klienten |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/deploy/action | Kör en åtgärd för distribution för att tillämpa ändringarna från den angivna git-grenen i konfigurationen i databasen. |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/operationResults/read | Hämta listan över Åtgärdsresultat eller hämta resultatet av en specifik åtgärd |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/read | Hämta konfiguration för den eller Get-klient access informationsdetaljer |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/regeneratePrimaryKey/action | Återskapa primärnyckeln |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/regenerateSecondaryKey/action | Återskapa den sekundära åtkomstnyckeln |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/save/action | Skapar commit med ögonblicksbild av konfigurationen för den angivna grenen i databasen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/syncState/read | Hämta status för senaste git-synkronisering |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/validate/action | Verifierar ändringar från den angivna git-grenen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/write | Konfigurera principen för klient eller information uppdateringsdetaljer klient åtkomst |
> | Åtgärd | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikat för en API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/service/users/action | Registrera en ny användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/attachments/delete | Tar bort en bilaga |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/attachments/read | Hämtar programmet bifogade filer eller hämtar bifogade filer |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/attachments/write | Lägga till bilaga till programmet |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/delete | Tar bort befintliga program |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/read | Hämta lista över alla program eller hämtar API Management programinformation |
> | Åtgärd | Microsoft.ApiManagement/service/users/applications/write | Registrerar ett program till API-hantering eller uppdateringar programinformation |
> | Åtgärd | Microsoft.ApiManagement/service/users/delete | Ta bort användarkonto |
> | Åtgärd | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Generera en URL för enkel inloggning. URL: en kan användas för åtkomst till administrationsportal |
> | Åtgärd | Microsoft.ApiManagement/service/users/groups/read | Hämta lista över användargrupper |
> | Åtgärd | Microsoft.ApiManagement/service/users/keys/read | Hämta lista över användarnycklar |
> | Åtgärd | Microsoft.ApiManagement/service/users/read | Hämta en lista över registrerade användare eller hämta kontoinformation för en användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/subscriptions/read | Hämta listan över användare prenumerationer |
> | Åtgärd | Microsoft.ApiManagement/service/users/token/action | Hämta token åtkomsttoken för en användare |
> | Åtgärd | Microsoft.ApiManagement/service/users/write | Registrera en ny användare eller uppdatera kontoinformation för en befintlig användare |
> | Åtgärd | Microsoft.ApiManagement/service/write | Skapa en ny instans av API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/unregister/action | Avregistrera prenumerationen för Microsoft.ApiManagement resursprovidern |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Authorization/checkAccess/action | Kontrollerar att anroparen har behörighet att utföra en viss åtgärd |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/delete | Tar bort administratören från prenumerationen. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/read | Läser in prenumerationens administratörer. |
> | Åtgärd | Microsoft.Authorization/classicAdministrators/write | Lägg till eller ta bort en administratör för en prenumeration. |
> | Åtgärd | Microsoft.Authorization/elevateAccess/action | Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen |
> | Åtgärd | Microsoft.Authorization/locks/delete | Ta bort lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/locks/read | Hämtar lås i det specificerade omfånget. |
> | Åtgärd | Microsoft.Authorization/locks/write | Lägg till lås i det specificerade omfånget. |
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
> | Åtgärd | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Läs en Azure Automation DSC-registreringsinformation |
> | Åtgärd | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Skriver en begäran om att återskapa nycklar för Azure Automation DSC |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/delete | Tar bort en Azure Automation-certifikattillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/getCount/action | Läser antal certifikat |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/read | Hämtar en Azure Automation-certifikattillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/certificates/write | Skapar eller uppdaterar en Azure Automation-certifikattillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/read | Läser en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/read | Läser en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/write | Skriver en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/compilationjobs/write | Skriver en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/content/read | Läser medieinnehåll konfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/delete | Tar bort en Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/getCount/action | Läser antal Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/read | Hämtar en Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/write | Skriver en Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/delete | Tar bort en Azure Automation-anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/getCount/action | Läser antal anslutningar |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/read | Hämtar en Azure Automation-anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connections/write | Skapar eller uppdaterar en Azure Automation-anslutningstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connectionTypes/delete | Tar bort en Azure Automation-anslutningstyptillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connectionTypes/read | Hämtar en Azure Automation-anslutningstyptillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/connectionTypes/write | Skapar en Azure Automation-anslutningstyptillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/delete | Tar bort en Azure Automation-autentiseringsuppgiftstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/getCount/action | Läser antal referenser |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/read | Hämtar en Azure Automation-autentiseringsuppgiftstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/write | Skapar eller uppdaterar en Azure Automation-autentiseringsuppgiftstillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/delete | Tar bort en Azure Automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Automation/automationAccounts/diagnosticSettings/write | Anger diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/delete | Tar bort Hybrid Runbook Worker-resurser |
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
> | Åtgärd | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar arbetsytan kopplad till automation-kontot |
> | Åtgärd | Microsoft.Automation/automationAccounts/listKeys/action | Läser nycklarna för automation-kontot |
> | Åtgärd | Microsoft.Automation/automationAccounts/logDefinitions/read | Hämtar de tillgängliga loggarna för automation-kontot |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/activities/read | Hämtar Azure Automation-aktiviteter |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/delete | Tar bort en Azure Automation-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/getCount/action | Hämtar antalet moduler i Automation-kontot |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/read | Hämtar en Azure Automation-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/write | Skapar eller uppdaterar en Azure Automation-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Tar bort en Azure Automation DSC-nodkonfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Läser en Azure Automation DSC-nod configuration innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Läser en Azure Automation DSC-nodkonfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Skriver en Azure Automation DSC-nodkonfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodecounts/read | Läser antalet noder sammanfattning för den angivna typen |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/delete | Tar bort Azure Automation DSC-noder |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/read | Läser en Azure Automation DSC-noder |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/reports/content/read | Läser innehåll i Azure Automation DSC |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/reports/read | Läser en Azure Automation DSC-rapporter |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodes/write | Skapar eller uppdaterar Azure Automation DSC-noder |
> | Åtgärd | Microsoft.Automation/automationAccounts/objectDataTypes/fields/read | Hämtar Azure Automation-TypeFields |
> | Åtgärd | Microsoft.Automation/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read | Hämtar Måttdefinitionerna för Automation |
> | Åtgärd | Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation-konto |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/content/read | Hämtar innehållet i en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/delete | Tar bort en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Skapar innehållet i ett Azure Automation-runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Hämtar prenumerationsåtgärdsresultaten utkast för Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/publish/action | Publicerar ett Azure Automation-runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/read | Hämtar ett Azure Automation-runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/read | Hämtar en Azure Automation runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Återupptar ett Azure Automation runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/stop/action | Stoppar ett Azure Automation runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Pausar ett Azure Automation runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Skapar en Azure Automation runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Ångra redigeringar i ett Azure Automation-runbookutkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/getCount/action | Hämtar antalet Azure Automation-runbooks |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/write | Skapar eller uppdaterar en Azure Automation-runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/delete | Tar bort en Azure Automation-schematillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/getCount/action | Hämtar antal Azure Automation-scheman |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation-schematillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation-schematillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/statistics/read | Hämtar Azure Automation-statistik |
> | Åtgärd | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Hämta en Azure Automation update distribution av dator |
> | Åtgärd | Microsoft.Automation/automationAccounts/updateManagementPatchJob/read | Hämtar en Azure Automation update management patch-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/usages/read | Hämtar Azure Automation-användning |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/delete | Tar bort en Azure Automation-variabeltillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/read | Läser en Azure Automation-variabeltillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/write | Skapar eller uppdaterar en Azure Automation-variabeltillgång |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/delete | Ta bort en Azure Automation-jobb för Övervakare |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/read | Hämtar en Azure Automation-jobb för Övervakare |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/start/action | Starta en watcher Azure Automation-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/stop/action | Stoppa ett Azure Automation-jobb för Övervakare |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/streams/read | Hämtar en Azure Automation watcher jobbström |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/watcherActions/delete | Ta bort en Azure Automation watcher jobbåtgärder |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/watcherActions/read | Hämtar en Azure Automation-watcher jobbåtgärder |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/watcherActions/write | Skapa en Azure Automation-watcher jobbåtgärder |
> | Åtgärd | Microsoft.Automation/automationAccounts/watchers/write | Skapar ett Azure Automation-jobb för Övervakare |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/action | Genererar en URI för en Azure Automation-webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/delete | Tar bort en Azure Automation-webhook  |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/read | Läser en Azure Automation-webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/write | Skapar eller uppdaterar en Azure Automation-webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/write | Skapar eller uppdaterar en Azure Automation-konto |
> | Åtgärd | Microsoft.Automation/operations/read | Hämtar tillgängliga åtgärder för Azure Automation-resurser |
> | Åtgärd | Microsoft.Automation/register/action | Registrerar prenumeration på Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Ta bort B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visa B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/write | Skapa eller uppdatera en B2C-katalogresurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/operations/read | Läs alla API-åtgärder tillgängliga för resursprovidern Microsoft.AzureActiveDirectory |
> | Åtgärd | Microsoft.AzureActiveDirectory/register/action | Registrera prenumeration för resursprovidern Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AzureStack/Operations/read | Hämtar egenskaperna för en resurs provider-åtgärd |
> | Åtgärd | Microsoft.AzureStack/register/action | Registrerar prenumeration med Microsoft.AzureStack resursprovidern |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/delete | Tar bort en Azure-stacken kund prenumeration |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/read | Hämtar egenskaperna för en Azure-stacken kund prenumeration |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/write | Skapar eller uppdaterar en Azure-stacken kund prenumeration |
> | Åtgärd | Microsoft.AzureStack/registrations/delete | Tar bort en Azure-Stack-registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/getActivationKey/action | Hämtar senaste Azure Stack aktiveringsnyckeln |
> | Åtgärd | Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökad information för en Azure-stacken Marketplace-produkten |
> | Åtgärd | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en Azure-stacken Marketplace-produkt |
> | Åtgärd | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure-Stack-registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/write | Skapar eller uppdaterar en Azure-Stack-registrering |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/delete | Tar bort ett program |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/read | Visar program eller hämtar egenskaperna för ett program |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktiverar ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/delete | Tar bort ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/read | Hämtar egenskaperna för ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/write | Skapar ett nytt programpaket eller uppdaterar ett befintligt programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/write | Skapar ett nytt program eller uppdaterar ett befintligt program |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Hämtar resultatet av en tidskrävande certifikatåtgärd i ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Avbryter misslyckad borttagning av ett certifikat på en Batch-kontot |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/delete | Tar bort ett certifikat från en Batch-kontot |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/read | Visar en lista över certifikat på en Batch-kontot eller hämtar egenskaperna för ett certifikat |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/write | Skapar ett nytt certifikat på en Batch-kontot eller uppdaterar ett befintligt certifikat |
> | Åtgärd | Microsoft.Batch/batchAccounts/delete | Tar bort ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/listkeys/action | Visar åtkomstnycklar för Batch-kontot |
> | Åtgärd | Microsoft.Batch/batchAccounts/operationResults/read | Hämtar resultatet av en långvarig åtgärd för Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/poolOperationResults/read | Hämtar resultatet av en tidskrävande pool-åtgärd i ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/delete | Tar bort poolen från ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/disableAutoscale/action | Inaktiverar automatisk skalning för en pool med Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/read | Visar en lista över programpooler på en Batch-kontot eller hämtar egenskaperna för en pool |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/stopResize/action | Stoppar ett pågående att ändra storlek på på poolen Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/upgradeOs/action | Uppgraderar operativsystemet på poolen Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/pools/write | Skapar en ny pool på ett Batch-konto eller uppdaterar en befintlig adresspool |
> | Åtgärd | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för Batch-tjänsten |
> | Åtgärd | Microsoft.Batch/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Batch-tjänsten |
> | Åtgärd | Microsoft.Batch/batchAccounts/read | Visar en lista över Batch-konton eller hämtar egenskaperna för ett Batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/regeneratekeys/action | Återskapar åtkomstnycklar för Batch-kontot |
> | Åtgärd | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synkroniserar åtkomstnycklar för lagringskontot automatiskt konfigurerade för Batch-kontot |
> | Åtgärd | Microsoft.Batch/batchAccounts/write | Skapar ett nytt batchkonto eller uppdaterar ett befintligt batchkonto |
> | Åtgärd | Microsoft.Batch/locations/checkNameAvailability/action | Kontrollerar att kontonamnet är giltigt och inte används. |
> | Åtgärd | Microsoft.Batch/locations/quotas/read | Hämtar Batch-kvoter för den angivna prenumerationen i den angivna Azure-regionen |
> | Åtgärd | Microsoft.Batch/register/action | Registrerar prenumerationen för Batch-Resursprovidern och kan skapa Batch-konton |
> | Åtgärd | Microsoft.Batch/unregister/action | Avregistrerar prenumerationen för Batch-Resursprovidern som hindrar skapandet av Batch-konton |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.BatchAI/clusters/delete | Tar bort ett Batch AI-kluster |
> | Åtgärd | Microsoft.BatchAI/clusters/read | Visar en lista över Batch AI-kluster eller hämtar egenskaperna för en Batch AI-kluster |
> | Åtgärd | Microsoft.BatchAI/clusters/remoteLoginInformation/action | Visar information om remote inloggningen för ett Batch AI-kluster |
> | Åtgärd | Microsoft.BatchAI/clusters/write | Skapar ett nytt Batch AI-kluster eller uppdaterar ett befintligt Batch AI-kluster |
> | Åtgärd | Microsoft.BatchAI/fileservers/delete | Tar bort en Batch AI-filserver |
> | Åtgärd | Microsoft.BatchAI/fileservers/read | Visar en lista över Batch AI fileservers eller hämtar egenskaperna för en Batch AI-filserver |
> | Åtgärd | Microsoft.BatchAI/fileservers/resume/action | Återupptar en Batch AI-filserver |
> | Åtgärd | Microsoft.BatchAI/fileservers/suspend/action | Pausar en Batch AI-filserver |
> | Åtgärd | Microsoft.BatchAI/fileservers/write | Skapar en ny Batch AI-filserver eller uppdaterar en befintlig Batch AI-filserver |
> | Åtgärd | Microsoft.BatchAI/jobs/delete | Tar bort ett Batch AI-jobb |
> | Åtgärd | Microsoft.BatchAI/jobs/read | Visar Batch AI jobb eller hämtar egenskaperna för en Batch AI-jobb |
> | Åtgärd | Microsoft.BatchAI/jobs/remoteLoginInformation/action | Visar information om remote inloggningen för ett Batch AI-jobb |
> | Åtgärd | Microsoft.BatchAI/jobs/terminate/action | Avslutar en Batch AI-jobb |
> | Åtgärd | Microsoft.BatchAI/jobs/write | Skapar ett nytt Batch AI-jobb eller uppdaterar ett befintligt Batch AI-jobb |
> | Åtgärd | Microsoft.BatchAI/register/action | Registrerar prenumerationen för Resursprovidern Batch AI och kan skapa Batch AI-resurser |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Billing/billingPeriods/read | Visar en lista över tillgängliga fakturering punkter |
> | Åtgärd | Microsoft.Billing/invoices/read | Visar tillgängliga fakturor |

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
> | Åtgärd | Microsoft.Capacity/register/action | Registrerar resursprovidern kapacitet och kan skapa kapacitet resurser. |
> | Åtgärd | Microsoft.Capacity/reservationorders/action | Uppdatera alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/delete | Ta bort alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/action | Uppdatera alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/delete | Ta bort alla Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/revisions/read | Läsa alla reservationer |
> | Åtgärd | Microsoft.Capacity/reservationorders/reservations/write | Skapa en Reservation |
> | Åtgärd | Microsoft.Capacity/reservationorders/write | Skapa en Reservation |

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
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningar för resursen |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för resursen |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för Microsoft.Cdn |
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
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/Read | Hämta en lista över CertificateOrders |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/reissue/Action | Ange en befintlig certificateorder |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/renew/Action | Förnya ett befintligt certificateorder |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendEmail/Action | Skicka e-post med certifikat |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Skicka om begäran e-post till en annan e-postadress |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Hämta platsen förseglingen för ett certifikat i App Service |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Hämta listan över certifikat-åtgärder |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Hämta certifikat e-historik |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verifiera domänägarskap |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/Write | Lägg till en ny certificateOrder eller uppdatera en befintlig |
> | Åtgärd | Microsoft.CertificateRegistration/operations/Read | Visa en lista med alla åtgärder från certifikatregistreringsplatsen för app service |
> | Åtgärd | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Etablera tjänstens huvudnamn för tjänstens huvudnamn för app |
> | Åtgärd | Microsoft.CertificateRegistration/register/action | Microsoft Certificates registerresursleverantören för prenumerationen |
> | Åtgärd | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Verifiera certifikatet köp objekt utan att skicka den |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicCompute/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Kontrollerar tillgängligheten för ett visst domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/active/write | Anger det aktiva domännamnet. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/availabilitySets/read | Visa tillgänglighetsuppsättningen för resursen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/capabilities/read | Visar domännamnsfunktionerna |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/delete | Ta bort resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/delete | Ta bort domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/operationStatuses/read | Läser domännamnstilläggens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/read | Returnerar domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/extensions/write | Lägg till domännamnstilläggen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/delete | Ta bort en ny intern belastningsutjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Läser åtgärdsstatusen för domännamnens interna belastningsutjämnare. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/read | Hämtar de interna belastningsutjämnarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/write | Skapa en ny intern belastningsutjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Läser åtgärdsstatusen för belastningsutjämnade slutpunktsuppsättningar för domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/read | Visar de belastningsutjämnade slutpunktsuppsättningarna |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/read | Returnera resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/delete | Ta bort de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Läser åtgärdsstatus för tjänstcertifikat för domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Returnerar de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/write | Lägg till eller ändra de tjänstcertifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/delete | Tar bort en given distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/operationStatuses/read | Läser domännamnsplatsernas åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/read | Visar distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Ta bort tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Läser åtgärdsstatus för rolltilläggsreferenser för domännamnsplatser. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Returnerar tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Lägg till eller ändra tilläggsreferensen för distributionsplatsrollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/read | Hämta distributionsplatsens roll. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Läser åtgärdsstatus för rollinstanser för domännamnsplatsroller. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Hämta rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Återskapar rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Återställer avbildningen för rollinstansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uppgradera domän |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/start/action | Startar en distributionsplats. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/start/write | Ändrar distributionsplatsens status till stoppad. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Ändrar distributionsplatsens status till startad. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/stop/action | Göra uppehåll för distributionsplatserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Gå igenom uppgraderingsdomän. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/write | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/swap/action | Byta mellanlagringsplatsen till produktionsplatsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/write | Lägg till eller ändra resursernas domännamn. |
> | Åtgärd | Microsoft.ClassicCompute/moveSubscriptionResources/action | Flytta alla klassiska resurser till en annan prenumeration. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystemFamilies/read | Listar de gästoperativsystem som är tillgängliga i Microsoft Azure, och listar även de operativsystemsversioner som är tillgängliga för respektive familj. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystems/read | Listar de versioner av gästoperativsystemen som för tillfället är tillgängliga i Microsoft Azure. |
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
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/delete | Tar bort virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Tar bort en datadisk från den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/disks/read | Hämtar en lista över datadiskar |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/action | Hämtar RDP-filen för den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Läser åtgärdsstatus för tillägg för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/read | Hämtar tillägget för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/write | Placerar tillägget för virtuell dator. |
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
> | Åtgärd | Microsoft.ClassicNetwork/gatewaySupportedDevices/read | Hämtar listan över enheter som stöds. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Tar bort säkerhetsregeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Läser åtgärdsstatus för säkerhetsroller för nätverkssäkerhetsgrupp. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Hämtar säkerhetsregeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Lägger till eller uppdaterar en säkerhetsregel. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/write | Lägger till en ny nätverkssäkerhetsgrupp. |
> | Åtgärd | Microsoft.ClassicNetwork/quotas/read | Hämta prenumerationens kvot. |
> | Åtgärd | Microsoft.ClassicNetwork/register/action | Registrera för klassiskt nätverk |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/delete | Ta bort en reserverad IP. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/join/action | Anslut till en reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Läser åtgärdsstatus för reserverade IP:ar. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/read | Hämtar reserverade IP-adresser |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/write | Lägga till en ny reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/checkIPAddressAvailability/action | Kontrollerar tillgängligheten för en given IP-adress i ett virtuellt nätverk. |
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
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Startar diagnostiken för den virtuella nätverks-gatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/action | Stoppar diagnostiken för den virtuella nätverks-gatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/write | Lägger till en virtuell nätverks-gateway. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/join/action | Anslut till det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Läser åtgärdsstatus för virtuella nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/peer/action | Peerar ett virtuellt nätverk med ett annat virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/read | Hämtar det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella nätverksundernät. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till undernätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/write | Lägg till ett nytt virtuellt nätverk. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicStorage/capabilities/read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicStorage/checkStorageAccountAvailability/action | Kontrollerar tillgängligheten för ett lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/disks/read | Returnerar lagringskontodisken. |
> | Åtgärd | Microsoft.ClassicStorage/images/read | Returnerar bilden. |
> | Åtgärd | Microsoft.ClassicStorage/osImages/read | Returnerar operativsystemsavbildningen. |
> | Åtgärd | Microsoft.ClassicStorage/publicImages/read | Hämtar den offentliga avbildningen av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/quotas/read | Hämta prenumerationens kvot. |
> | Åtgärd | Microsoft.ClassicStorage/register/action | Registrera för klassisk lagring |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/delete | Ta bort lagringskontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/delete | Tar bort angiven lagringskontodisk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagringskontodisken. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/write | Lägger till en lagringskontodisk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/delete | Tar bort en given lagringskontoavbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagringskontoavbildningen. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Tar bort en given operativsystemsavbildning för lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returnerar operativsystemsavbildningen för lagringskonto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/regenerateKey/action | Återskapar befintliga åtkomstnycklar för lagringskontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/read | Hämta de tjänster som är tillgängliga. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/write | Lägger till ett nytt lagringskonto. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.CognitiveServices/accounts/delete | Tar bort API-konton |
> | Åtgärd | Microsoft.CognitiveServices/accounts/listKeys/action | Visa nyckellista |
> | Åtgärd | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Cognitive Services. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/read | Läser API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/regenerateKey/action | Återskapar nyckel |
> | Åtgärd | Microsoft.CognitiveServices/accounts/skus/read | Läser tillgängliga SKU:er för en befintlig resurs. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/usages/read | Hämta kvotanvändning för en befintlig resurs. |
> | Åtgärd | Microsoft.CognitiveServices/accounts/write | Skriver API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/locations/checkSkuAvailability/action | Läser tillgängliga SKU:er för en prenumeration. |
> | Åtgärd | Microsoft.CognitiveServices/Operations/read | Visa alla tillgängliga åtgärder i lista |
> | Åtgärd | Microsoft.CognitiveServices/register/action | Registrerar prenumerationen med Cognitive Services |
> | Åtgärd | Microsoft.CognitiveServices/skus/read | Läser tillgängliga SKU:er för Cognitive Services. |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Commerce/RateCard/read | Returnerar erbjuder data, resurs-mätare metadata och priser för den angivna prenumerationen. |
> | Åtgärd | Microsoft.Commerce/UsageAggregates/read | Hämtar Microsoft Azure-förbrukningen av en prenumeration. Resultatet innehåller mängder användningsdata, prenumeration och resurs relaterad information, på ett angivet tidsintervall. |

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
> | Åtgärd | Microsoft.Compute/images/delete | Avbildningen tas bort |
> | Åtgärd | Microsoft.Compute/images/read | Hämta egenskaperna för avbildningen |
> | Åtgärd | Microsoft.Compute/images/write | Skapar en ny avbildning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/locations/capsOperations/read | Hämtar status för en asynkron Caps-åtgärd |
> | Åtgärd | Microsoft.Compute/locations/diskOperations/read | Hämtar status för en asynkron diskåtgärd |
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
> | Åtgärd | Microsoft.Compute/snapshots/endGetAccess/action | Återkalla ögonblicksbilden SAS-URI |
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
> | Åtgärd | Microsoft.Compute/virtualMachines/performMaintenance/action | Utför underhållsåtgärd på den virtuella datorn. |
> | Åtgärd | Microsoft.Compute/virtualMachines/powerOff/action | Stänger av den virtuella datorn. Observera att den virtuella datorn kommer att fortsätta debiteras. |
> | Åtgärd | Microsoft.Compute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitioner för den virtuella datorn |
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
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Hämtar historiken för operativsystemuppgraderingar för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/performMaintenance/action | Utför planerat underhåll på VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Stänger av VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitioner för den virtuella datorns skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Hämta egenskaper för alla offentliga IP-adresser för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/read | Hämta egenskaperna för en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Distribuera om VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Skapar ny avbildning av VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/restart/action | Startar om instanserna för VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Avbryter samlad uppgradering av en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Hämta senaste status för den samlade uppgraderingen för VM-skalningsuppsättningen |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/scale/action | Kontrollera om en befintlig VM-skalningsuppsättning kan skala in/ut till det angivna antalet instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/skus/read | Visar en lista över giltiga SKU-koder för en befintlig VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/start/action | Startar VM-skalningsuppsättningens instanser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Stänger av och frigör beräkningsresurserna för en virtuell dator i en skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Ta bort en viss virtuell dator i en skaluppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/instanceView/read | Hämtar instansvyn för en virtuell dator i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Hämta egenskaper för offentliga IP-adressen som skapats med hjälp av Skaluppsättning för virtuell dator. Skaluppsättning för virtuell dator kan skapa högst en offentlig IP per ipconfiguration (privat IP) |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Hämta egenskaperna för en eller alla IP-konfigurationer för ett nätverksgränssnitt som skapats med hjälp av Skaluppsättning för virtuell dator. IP-konfigurationer representerar privata IP-adresser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Hämta egenskaper för alla nätverksgränssnitt för en virtuell dator som har skapats med VM-skalningsuppsättningar |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Utför planerat underhåll på en virtuell datorinstans i en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Stänger av en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitioner för den virtuella datorn i skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Hämtar egenskaperna för en virtuell dator i en skaluppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Distribuerar om en virtuell datorinstans i en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Skapar ny avbildning av virtuell datorinstans i en VM-skalningsuppsättning. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Startar om en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Startar en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Uppdaterar egenskaperna för en virtuell dator i en VM-skalningsuppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/write | Skapar en ny VM-skalningsuppsättning eller uppdaterar en befintlig |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Consumption/balances/read | Lista över belastningen sammanfattning för faktureringsperioden för en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/budgets/read | Lista över budgetar genom en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/budgets/write | Skapa, uppdatera och ta bort budgetar genom en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/marketplaces/read | Visa användningsinformation för ett omfång för prenumerationer EA och WebDirect marketplace resurs. |
> | Åtgärd | Microsoft.Consumption/operations/read | Lista över de åtgärder som alla stöds av Microsoft.Consumption resursprovidern. |
> | Åtgärd | Microsoft.Consumption/pricesheets/read | Visa en lista över Pricesheets-data för en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/reservationDetails/read | Visa information om diskanvändning för reserverade instanser av reservation ordning eller hantering av grupper. Information om data är per instans per dag nivå. |
> | Åtgärd | Microsoft.Consumption/reservationRecommendations/read | Lista över rekommendationer för enkel eller delad för reserverade instanser för en prenumeration. |
> | Åtgärd | Microsoft.Consumption/reservationSummaries/read | Lista över belastningen sammanfattning för reserverade instanser av reservation ordning eller hantering av grupper. Sammanfattningsdata är antingen på månatliga eller daglig nivå. |
> | Åtgärd | Microsoft.Consumption/reservationTransactions/read | Visa transaktionshistoriken för reserverade instanser av hanteringsgrupper. |
> | Åtgärd | Microsoft.Consumption/terms/read | Ange villkoren för en prenumeration eller en hanteringsgrupp. |
> | Åtgärd | Microsoft.Consumption/usageDetails/read | Visa användningsinformation för ett omfång för prenumerationer EA och WebDirect. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/containers/logs/read | Hämta loggar för en särskild behållare. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/delete | Tar bort den specifika behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för behållargruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/read | Hämta alla behållargrupper. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/write | Skapa eller uppdatera en särskild behållargrupp. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontrollerar om registret behållarnamn är tillgängliga för användning. |
> | Åtgärd | Microsoft.ContainerRegistry/locations/operationResults/read | Hämtar en asynkron resultat |
> | Åtgärd | Microsoft.ContainerRegistry/operations/read | Visar en lista över alla tillgängliga Azure Container registret REST API-åtgärder |
> | Åtgärd | Microsoft.ContainerRegistry/register/action | Registrerar prenumerationen för resursprovidern behållare registret och kan skapa behållaren register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/delete | Tar bort ett register för behållaren. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Tar bort ett rutnät händelsefilter registret är behållare. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Hämtar egenskaperna för det angivna händelsen rutnätet filtret eller listar alla händelse rutnätet filter för den angivna behållaren registernyckeln. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Skapar eller uppdaterar ett händelsefilter rutnät för en behållare registret med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/importImage/action | Importera bild till behållaren registret med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listCredentials/action | Visar inloggningsuppgifterna för det angivna behållaren registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listUsages/read | Visar kvoten användningsområden för den angivna behållaren registernyckeln. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/operationStatuses/read | Hämtar status registret asynkron åtgärd |
> | Åtgärd | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.ContainerRegistry/registries/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Microsoft ContainerRegistry |
> | Åtgärd | Microsoft.ContainerRegistry/registries/read | Hämtar egenskaperna för det angivna behållaren registret eller listar alla behållare register under den angivna resursgrupp eller prenumeration. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Återskapar en inloggningsuppgifterna för det angivna behållaren registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/delete | Tar bort en replikering från en behållare registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Hämtar en replikeringsstatus asynkron åtgärd |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/read | Hämtar egenskaperna för den angivna replikeringen eller listar alla replikeringar för den angivna behållaren registernyckeln. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/write | Skapar eller uppdaterar en replikering för en behållare registret med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/delete | Tar bort en webhook från en behållare registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Hämtar konfigurationen för tjänsten URI och anpassade huvuden för webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Visar senaste händelser för den angivna webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Hämtar en webhook asynkrona Åtgärdsstatus |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/ping/action | Utlöser en ping-händelse skickas till webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/read | Hämtar egenskaperna för den angivna webhooken eller listar alla webhooks för den angivna behållaren registernyckeln. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/write | Skapar eller uppdaterar en webhook för en behållare registret med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/write | Skapar eller uppdaterar en behållare registret med de angivna parametrarna. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerService/containerServices/delete | Tar bort en behållartjänsten |
> | Åtgärd | Microsoft.ContainerService/containerServices/read | Hämta en behållartjänsten |
> | Åtgärd | Microsoft.ContainerService/containerServices/write | Skapar en ny behållartjänst eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Hämta en profil för åtkomst av hanterade kluster genom rollnamn med hjälp av listan autentiseringsuppgifter |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/read | Hämta en profil för åtkomst av hanterade kluster genom rollnamn |
> | Åtgärd | Microsoft.ContainerService/managedClusters/delete | Tar bort ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för en hanterad klusterresurs |
> | Åtgärd | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för en hanterad klusterresurs |
> | Åtgärd | Microsoft.ContainerService/managedClusters/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för hanterade kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/read | Hämta ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/write | Skapar ett nytt kluster som hanterade eller uppdaterar en befintlig |

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
> | Åtgärd | Microsoft.ContentModerator/listCommunicationPreference/action | Lista över inställningar för kommunikation |
> | Åtgärd | Microsoft.ContentModerator/operations/read | Läs-och skrivåtgärder |
> | Åtgärd | Microsoft.ContentModerator/updateCommunicationPreference/action | Uppdatera inställningar för kommunikation |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.CustomerInsights/hubs/adobemetadata/action | Skapa eller uppdatera alla Azure-kund insikter Adobe Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/adobemetadata/read | Läsa alla Azure-kund insikter Adobe Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Ta bort princip för delad åtkomst-signatur alla Azure-kund insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/read | Läsa några Azure kunden insikter delad åtkomstprincip för signatur |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/regeneratePrimaryKey/action | Återskapa en primär nyckel för Azure kunden insikter delad signatur åtkomstprincip |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Återskapa en sekundär nyckel för Azure kunden insikter delad signatur åtkomstprincip |
> | Åtgärd | Microsoft.CustomerInsights/hubs/authorizationPolicies/write | Skapa eller uppdatera princip för delad åtkomst-signatur alla Azure-kund insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivera alla Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/activate/action | Aktivera alla Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/delete | Ta bort alla Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/getruntimestatus/action | Hämta alla Azure kunden Insights Connector Körningsstatus |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/activate/action | Aktivera alla Azure kunden Insights Connector mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/delete | Ta bort alla Azure kunden Insights Connector mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/operations/read | Läs valfritt Åtgärdsresultat Azure kunden Insights Connector mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/read | Läsa alla Azure kunden Insights Connector mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/mappings/write | Skapa eller uppdatera alla Azure kunden Insights Connector mappning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/operations/read | Läs valfritt Åtgärdsresultat Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/read | Läsa alla Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/saveauthinfo/action | Skapa eller uppdatera alla Azure kunden Insights Connector autentisering information om Gatewayanslutningen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/update/action | Uppdatera alla Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/connectors/write | Skapa eller uppdatera alla Azure kunden Insights Connector |
> | Åtgärd | Microsoft.CustomerInsights/hubs/crmmetadata/action | Skapa eller uppdatera alla Azure-kund insikter Crm Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/crmmetadata/read | Läsa alla Azure-kund insikter Crm Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/delete | Ta bort alla Azure kunden Insights hubb |
> | Åtgärd | Microsoft.CustomerInsights/hubs/gdpr/delete | Ta bort alla Azure-kund insikter BNPR |
> | Åtgärd | Microsoft.CustomerInsights/hubs/gdpr/read | Läsa alla Azure-kund insikter BNPR |
> | Åtgärd | Microsoft.CustomerInsights/hubs/gdpr/write | Skapa eller uppdatera alla Azure-kund insikter BNPR |
> | Åtgärd | Microsoft.CustomerInsights/hubs/getbillingcredits/read | Hämta Azure kunden Insights hubb fakturering krediter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/getbillinghistory/read | Hämta Azure kunden Insights hubb fakturering historik |
> | Åtgärd | Microsoft.CustomerInsights/hubs/images/delete | Ta bort alla Azure-kund insikter avbildningen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/images/read | Läs valfri Azure kunden insikter bild |
> | Åtgärd | Microsoft.CustomerInsights/hubs/images/write | Skapa eller uppdatera någon Azure kunden insikter bild |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/delete | Ta bort alla azure kundernas insikter interaktioner |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/operations/read | Läs valfritt Åtgärdsresultat Azure kundinteraktion insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/read | Läsa alla Azure kundinteraktion insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Föreslå relationen länkar för alla Azure kundernas insikter interaktioner |
> | Åtgärd | Microsoft.CustomerInsights/hubs/interactions/write | Skapa eller uppdatera alla Azure kundinteraktion insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/delete | Ta bort alla Azure-kund insikter Key Performance Indicator |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter prestationsindikatorer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/read | Läsa alla Azure-kund insikter Key Performance Indicator |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/reprocess/action | Bearbeta alla Azure-kund insikter KPI: er |
> | Åtgärd | Microsoft.CustomerInsights/hubs/kpi/write | Skapa eller uppdatera en Azure-kund insikter Key Performance Indicator |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/delete | Ta bort alla Azure-kund insikter länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/read | Läsa alla Azure-kund insikter länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/links/write | Skapa eller uppdatera Azure kunden länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/msemetadata/action | Skapa eller uppdatera alla Azure-kund insikter mus Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/msemetadata/read | Läsa alla Azure-kund insikter mus Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/operationresults/read | Hämta Azure kunden Insights hubb Åtgärdsresultat |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/delete | Ta bort alla Azure-kund insikter förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/read | Läsa alla Azure-kund insikter förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictions/write | Skapa eller uppdatera alla Azure-kund förutsägelser |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/delete | Ta bort alla Azure-kund insikter förutsägande matchar principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter förutsägande matchar principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/read | Läsa alla Azure-kund insikter förutsägande matchar principer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/predictivematchpolicies/write | Skapa eller uppdatera policyer Azure kunden insikter förutsägande matchning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/delete | Ta bort alla Azure-kund insikter profil |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter profil |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/read | Läsa alla Azure-kund insikter profil |
> | Åtgärd | Microsoft.CustomerInsights/hubs/profiles/write | Skriv valfri Azure kunden insikter profil |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för resurs |
> | Åtgärd | Microsoft.CustomerInsights/hubs/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för resurs |
> | Åtgärd | Microsoft.CustomerInsights/hubs/read | Läsa alla Azure kunden Insights hubb |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/delete | Ta bort alla Azure-kund insikter relationen länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter relationen länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/read | Läsa alla Azure-kund insikter relationen länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationshiplinks/write | Skapa eller uppdatera Azure kunden insikter relationen länkar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/delete | Ta bort alla Azure-kund insikter relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/read | Läsa alla Azure-kund insikter relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/relationships/write | Skapa eller uppdatera Azure kunden insikter relationer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/delete | Ta bort alla Azure-kund insikter Rbac tilldelning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/operations/read | Läs valfritt Åtgärdsresultat Azure kunden insikter Rbac tilldelning |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/read | Läs Azure kunden insikter Rbac tilldelningar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roleAssignments/write | Skapa eller uppdatera Azure kunden insikter Rbac tilldelningar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/roles/read | Läsa alla Azure-kund insikter Rbac-roller |
> | Åtgärd | Microsoft.CustomerInsights/hubs/salesforcemetadata/action | Skapa eller uppdatera alla Azure-kund insikter SalesForce-Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/salesforcemetadata/read | Läsa alla Azure-kund insikter SalesForce-Metadata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/delete | Ta bort alla Azure kundsegment insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/dynamic/action | Hantering av alla Azure-kund Insight dynamiska segment |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/read | Läsa alla Azure kundsegment insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/static/action | Hantering av alla Azure-kund Insight statiska segment |
> | Åtgärd | Microsoft.CustomerInsights/hubs/segments/write | Skapa eller uppdatera alla Azure kundsegment insikter |
> | Åtgärd | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/delete | Ta bort alla Azure-kund insikter SqlConnectionStrings |
> | Åtgärd | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/read | Läsa alla Azure-kund insikter SqlConnectionStrings |
> | Åtgärd | Microsoft.CustomerInsights/hubs/sqlconnectionstrings/write | Skapa eller uppdatera alla Azure-kund insikter SqlConnectionStrings |
> | Åtgärd | Microsoft.CustomerInsights/hubs/suggesttypeschema/action | Generera föreslår typen Schema från exempeldata |
> | Åtgärd | Microsoft.CustomerInsights/hubs/tenantmanagement/read | Hantera Azure kunden Insights hubbsinställningar |
> | Åtgärd | Microsoft.CustomerInsights/hubs/views/delete | Ta bort en Azure-kund insikter App vy |
> | Åtgärd | Microsoft.CustomerInsights/hubs/views/read | Läs valfri Azure kunden insikter App vy |
> | Åtgärd | Microsoft.CustomerInsights/hubs/views/write | Skapa eller uppdatera Azure kunden insikter App visa |
> | Åtgärd | Microsoft.CustomerInsights/hubs/widgettypes/read | Läs en Azure kunden insikter App Widget typer |
> | Åtgärd | Microsoft.CustomerInsights/hubs/write | Skapa eller uppdatera alla Azure kunden Insights hubb |
> | Åtgärd | Microsoft.CustomerInsights/operations/read | Läs Azure kunden Insights Api Metadatas |
> | Åtgärd | Microsoft.CustomerInsights/register/action | Registrerar prenumerationen för resursprovidern kunden insikter och kan skapa kunden Insights-resurser |
> | Åtgärd | Microsoft.CustomerInsights/unregister/action | Avregistrerar prenumerationen för resursprovidern kunden insikter |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Databricks/workspaces/delete | Tar bort en arbetsyta. |
> | Åtgärd | Microsoft.Databricks/workspaces/read | Hämtar en lista över arbetsytor. |
> | Åtgärd | Microsoft.Databricks/workspaces/write | Skapar en arbetsyta. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataCatalog/catalogs/delete | Tar bort katalogen. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/read | Visa egenskaper för katalogen eller katalogerna under prenumeration eller resursgrupp. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/write | Katalogen skapas eller uppdateras taggar och egenskaper för katalogen. |
> | Åtgärd | Microsoft.DataCatalog/checkNameAvailability/action | Kontrollerar tillgängligheten för katalog-namnet för klienten. |
> | Åtgärd | Microsoft.DataCatalog/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft.DataCatalog resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/register/action | Registrerar prenumeration med Microsoft.DataCatalog resursprovidern. |
> | Åtgärd | Microsoft.DataCatalog/unregister/action | Avregistrerar prenumeration från Microsoft.DataCatalog resursprovidern. |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataFactory/datafactories/activitywindows/read | Läser aktivitet Windows i Datafabriken med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Läser aktivitet Windows för aktiviteten rörledningen med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/activitywindows/read | Läser aktivitet Windows för rörledningen med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/delete | Tar bort alla Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/pause/action | Pausar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/read | Läser en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/resume/action | Återupptar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/update/action | Uppdaterar alla Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/write | Skapar eller uppdaterar en Pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Läser aktivitet Windows för datauppsättningen med de angivna parametrarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/delete | Tar bort alla Dataset. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/read | Läser en Dataset. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Läser Data-segmentet som körs för den givna datamängden med den angivna starttiden. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/slices/read | Hämtar Datasektorer i den angivna perioden. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/slices/write | Uppdatera Status för Datasektorn. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/write | Skapar eller uppdaterar en Dataset. |
> | Åtgärd | Microsoft.DataFactory/datafactories/delete | Tar bort Data Factory. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/connectioninfo/action | Läser anslutningsinformation för en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/delete | Tar bort en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/listauthkeys/action | Listar autentiseringsnycklar för någon Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/read | Läser en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/regenerateauthkey/action | Återskapar nycklarna autentisering för en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/gateways/write | Skapar eller uppdaterar en Gateway. |
> | Åtgärd | Microsoft.DataFactory/datafactories/linkedServices/delete | Tar bort alla länkade tjänster. |
> | Åtgärd | Microsoft.DataFactory/datafactories/linkedServices/read | Läser alla länkade tjänster. |
> | Åtgärd | Microsoft.DataFactory/datafactories/linkedServices/write | Skapar eller uppdaterar en länkad tjänst. |
> | Åtgärd | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DataFactory/datafactories/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för datafactories |
> | Åtgärd | Microsoft.DataFactory/datafactories/read | Läser Data Factory. |
> | Åtgärd | Microsoft.DataFactory/datafactories/runs/loginfo/read | Läser en SAS-URI till en blob-behållare som innehåller loggarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/delete | Tar bort alla Dataset. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/read | Läser en Dataset. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/write | Skapar eller uppdaterar en Dataset. |
> | Åtgärd | Microsoft.DataFactory/datafactories/write | Skapar eller uppdaterar Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/cancelpipelinerun/action | Avbryter pipeline kör anges av körnings-ID. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/delete | Tar bort alla Dataset. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/read | Läser en Dataset. |
> | Åtgärd | Microsoft.DataFactory/factories/datasets/write | Skapar eller uppdaterar en Dataset. |
> | Åtgärd | Microsoft.DataFactory/factories/delete | Tar bort Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/delete | Tar bort alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Läser Integration Runtime anslutningsinformation. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Läser Integration Körningsstatus. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Visar en lista över autentiseringsnycklar för alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/monitoringdata/read | Hämtar övervakningsdata för alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Tar bort noden för den angivna Integration körningsmiljön. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Returnerar IP-adressen för den angivna noden av Integration körningsmiljön. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Uppdaterar en egen värdbaserade Integration Runtime nod. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/read | Läser alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Återskapar nycklarna autentisering för den angivna Integration körningsmiljön. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/start/action | Startar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Stoppar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synkroniserar autentiseringsuppgifterna för den angivna Integration körningsmiljön. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uppgraderar den angivna Integration körningsmiljön. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/write | Skapar eller uppdaterar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/linkedServices/delete | Tar bort länkade tjänsten. |
> | Åtgärd | Microsoft.DataFactory/factories/linkedServices/read | Läser länkade tjänsten. |
> | Åtgärd | Microsoft.DataFactory/factories/linkedServices/write | Skapa eller uppdatera länkade tjänst |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/activityruns/read | Läser aktiviteten körs för den angivna pipelinen kör ID. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelineruns/read | Läser Pipeline-körs. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/createrun/action | Skapar en körning för Pipeline. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/delete | Tar bort Pipeline. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/read | Läser Pipeline. |
> | Åtgärd | Microsoft.DataFactory/factories/pipelines/write | Skapa eller uppdatera pipelinen |
> | Åtgärd | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DataFactory/factories/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DataFactory/factories/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för fabriker |
> | Åtgärd | Microsoft.DataFactory/factories/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för fabriker |
> | Åtgärd | Microsoft.DataFactory/factories/read | Läser Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/delete | Tar bort alla utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/read | Läser alla utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/start/action | Startar alla utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/stop/action | Stoppar alla utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/triggerruns/read | Läser utlösaren körs. |
> | Åtgärd | Microsoft.DataFactory/factories/triggers/write | Skapar eller uppdaterar en utlösare. |
> | Åtgärd | Microsoft.DataFactory/factories/write | Skapa eller uppdatera Data Factory |
> | Åtgärd | Microsoft.DataFactory/register/action | Registrerar prenumerationen för Resursprovidern Data Factory. |
> | Åtgärd | Microsoft.DataFactory/unregister/action | Avregistrerar prenumerationen för Resursprovidern Data Factory. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Ta bort en princip för beräkning. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Hämta information om en princip för beräkning. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/write | Skapa eller uppdatera en princip för beräkning. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Avlänka ett DataLakeStore konto från ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Hämta information om ett länkade DataLakeStore-konto för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Skapa eller uppdatera en länkad DataLakeStore konto för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/delete | Ta bort ett DataLakeAnalytics konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Ta bort en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Hämta information om en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Skapa eller uppdatera en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Hämta resultatet av en DataLakeAnalytics konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningar för DataLakeAnalytics-kontot. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapa eller uppdatera diagnostikinställningar för DataLakeAnalytics-kontot. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/logDefinitions/read | Hämta de tillgängliga loggarna för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/providers/Microsoft.Insights/metricDefinitions/read | Hämta tillgängliga mått för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/read | Hämta information om ett befintligt DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Lista över SAS-token för behållare för lagring av länkade lagringskonton för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/read | Hämta behållare av länkade lagringskonton för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/delete | Avlänka ett lagringskonto från en DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Hämta information om ett länkat lagringskonto för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Skapa eller uppdatera ett länkat lagringskonto för DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/write | Skapa eller uppdatera ett DataLakeAnalytics konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/capability/read | Hämta Funktionsinformation om för en prenumeration angående med DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Kontrollera tillgängligheten för ett DataLakeAnalytics kontonamn. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/operationResults/read | Hämta resultatet av en DataLakeAnalytics konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/operations/read | Hämta tillgängliga åtgärder av DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/register/action | Registrera prenumeration på DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataLakeStore/accounts/delete | Ta bort ett DataLakeStore konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Aktivera KeyVault för en DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/delete | Ta bort en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/read | Hämta information om en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/write | Skapa eller uppdatera en brandväggsregel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/operationResults/read | Hämta resultatet av en DataLakeStore konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningar för DataLakeStore-kontot. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapa eller uppdatera diagnostikinställningar för DataLakeStore-kontot. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/logDefinitions/read | Hämta de tillgängliga loggarna för DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/providers/Microsoft.Insights/metricDefinitions/read | Hämta tillgängliga mått för DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/read | Hämta information om ett befintligt DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/Superuser/action | Bevilja superanvändare på Data Lake Store när beviljas med Microsoft.Authorization/roleAssignments/write. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/delete | Ta bort en betrodd identitetsleverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Hämta information om en betrodd identitetsleverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Skapa eller uppdatera en betrodd identitetsleverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/write | Skapa eller uppdatera ett DataLakeStore konto. |
> | Åtgärd | Microsoft.DataLakeStore/locations/capability/read | Hämta Funktionsinformation om för en prenumeration angående med DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Kontrollera tillgängligheten för ett DataLakeStore kontonamn. |
> | Åtgärd | Microsoft.DataLakeStore/locations/operationResults/read | Hämta resultatet av en DataLakeStore konto. |
> | Åtgärd | Microsoft.DataLakeStore/operations/read | Hämta tillgängliga åtgärder av DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/register/action | Registrera prenumeration på DataLakeStore. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforMySQL/locations/performanceTiers/read | Returnerar listan över tillgängliga prestandanivåer. |
> | Åtgärd | Microsoft.DBforMySQL/performanceTiers/read | Returnerar listan över tillgängliga prestandanivåer. |
> | Åtgärd | Microsoft.DBforMySQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/read | Returnera listan över brandväggen regler för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforMySQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mätvärden som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforMySQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforMySQL/servers/recoverableServers/read | Returnera återställningsbara MySQL-serverinformation |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/performanceTiers/read | Returnerar listan över tillgängliga prestandanivåer. |
> | Åtgärd | Microsoft.DBforPostgreSQL/performanceTiers/read | Returnerar listan över tillgängliga prestandanivåer. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Returnera listan över brandväggen regler för en server eller hämtar egenskaperna för den angivna brandväggsregeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för Postgres servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mätvärden som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Returnera återställningsbara PostgreSQL-serverinformation |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Hämta information om server threat detection principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Ändra server threat detection principen för en given server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Devices/checkNameAvailability/Action | Kontrollera om IotHub namn är tillgängligt |
> | Åtgärd | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Kontrollera om IotHub namn är tillgängligt |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Ta bort resursen IotHub-klient |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Ta bort EventHub konsumentgrupp |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Hämta EventHub konsumenten grupper |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Skapa EventHub konsumentgrupp |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportera enheter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Hämtar IotHub innehavaren stats resurs |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importera enheter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Hämtar klientnyckeln IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/jobs/Read | Hämta jobb information som skickats på angivna IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Hämtar IotHub klientnycklar |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för IotHub Service |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Hämtar tillgängliga mått för tjänsten IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Hämta mätvärden kvot |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Read | Hämtar resursen IotHub-klient |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testmeddelande mot alla befintliga vägar |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testmeddelande mot ett angivna test väg |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Hämtar hälsotillståndet för alla routning slutpunkter för en IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Write | Skapa eller uppdatera resursen IotHub-klient |
> | Åtgärd | Microsoft.Devices/ElasticPools/metricDefinitions/read | Hämtar tillgängliga mått för tjänsten IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generera Verifieringskod |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/verify/Action | Kontrollera resursens certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/Delete | Ta bort resursen med IotHub |
> | Åtgärd | Microsoft.Devices/IotHubs/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/IotHubs/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Tar bort händelsen rutnätet filter |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Read | Hämtar händelsen rutnätet filter |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Write | Skapa en ny eller uppdatera befintliga händelse rutnätet filter |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Ta bort EventHub konsumentgrupp |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Hämta EventHub konsumenten grupper |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Skapa EventHub konsumentgrupp |
> | Åtgärd | Microsoft.Devices/iotHubs/exportDevices/Action | Exportera enheter |
> | Åtgärd | Microsoft.Devices/iotHubs/importDevices/Action | Importera enheter |
> | Åtgärd | Microsoft.Devices/iotHubs/iotHubKeys/listkeys/Action | Hämta IotHub Key för det angivna namnet |
> | Åtgärd | Microsoft.Devices/iotHubs/iotHubStats/Read | Få statistik IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/jobs/Read | Hämta jobb information som skickats på angivna IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/listkeys/Action | Hämta alla IotHub-nycklar |
> | Åtgärd | Microsoft.Devices/IotHubs/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för IotHub Service |
> | Åtgärd | Microsoft.Devices/IotHubs/metricDefinitions/read | Hämtar tillgängliga mått för tjänsten IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/quotaMetrics/Read | Hämta mätvärden kvot |
> | Åtgärd | Microsoft.Devices/iotHubs/Read | Hämtar IotHub-resurser |
> | Åtgärd | Microsoft.Devices/iotHubs/routing/$testall/Action | Testmeddelande mot alla befintliga vägar |
> | Åtgärd | Microsoft.Devices/iotHubs/routing/$testnew/Action | Testmeddelande mot ett angivna test väg |
> | Åtgärd | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Hämtar hälsotillståndet för alla routning slutpunkter för en IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/skus/Read | Hämta giltiga IotHub Skus |
> | Åtgärd | Microsoft.Devices/iotHubs/Write | Skapa eller uppdatera IotHub resurs |
> | Åtgärd | Microsoft.Devices/operations/Read | Hämta alla ResourceProvider-åtgärder |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generera Verifieringskod |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/verify/Action | Kontrollera resursens certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/Delete | Ta bort IotDps resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/listkeys/Action | Hämta alla IotDps nycklar |
> | Åtgärd | Microsoft.Devices/provisioningServices/logDefinitions/read | Hämtar tillgängliga Loggdefinitioner för tjänsten etablering |
> | Åtgärd | Microsoft.Devices/provisioningServices/metricDefinitions/read | Hämtar tillgängliga mått för tjänsten etablering |
> | Åtgärd | Microsoft.Devices/provisioningServices/ProvisioningServiceKeys/listkeys/Action | Hämta IotDps nycklar för nyckelnamn |
> | Åtgärd | Microsoft.Devices/provisioningServices/Read | Hämta IotDps resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/skus/Read | Hämta giltiga IotDps Skus |
> | Åtgärd | Microsoft.Devices/provisioningServices/Write | Skapa IotDps resurs |
> | Åtgärd | Microsoft.Devices/register/action | Registrera prenumerationen för resursprovidern IotHub och kan skapa IotHub resurser |
> | Åtgärd | Microsoft.Devices/register/action | Registrera prenumerationen för resursprovidern IotHub och kan skapa IotHub resurser |
> | Åtgärd | Microsoft.Devices/usages/Read | Hämta prenumeration användningsinformation för den här providern. |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DevTestLab/labCenters/delete | Ta bort lab datacenter. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/read | Läsa lab datacenter. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/write | Lägg till eller ändra lab datacenter. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Läs azure resource manager-mallar. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Genererar en ARM-mall för den angivna artefakten, överför de nödvändiga filerna till ett lagringskonto och validerar artefakt som skapas. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Läsa artefakter. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/delete | Ta bort artefakt källor. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/read | Läsa artefakt källor. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/write | Lägg till eller ändra artefakt källor. |
> | Åtgärd | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Begär en slumpmässig claimable virtuell dator i labbet. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/read | Läsa kostnader. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/write | Lägg till eller ändra kostnaderna. |
> | Åtgärd | Microsoft.DevTestLab/labs/CreateEnvironment/action | Skapa virtuella datorer i ett labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/delete | Ta bort anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/read | Läsa anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/write | Lägg till eller ändra anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/delete | Ta bort övningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exporterar resursanvändningen lab till ett lagringskonto |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/read | Läsa formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/galleryImages/read | Läsa galleriavbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generera en URI för överföring av anpassade diskavbildningar till ett labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importera en virtuell dator till en annan labbet. |
> | Åtgärd | Microsoft.DevTestLab/labs/ListVhds/action | Lista över tillgängliga för att skapa en anpassad avbildning diskavbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/delete | Ta bort notificationchannels. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Skicka meddelande till angivna kanal. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/read | Läsa notificationchannels. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/write | Lägg till eller ändra notificationchannels. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Utvärderar lab princip. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/delete | Ta bort principer. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/read | Läsa principer. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/write | Lägg till eller ändra principer. |
> | Åtgärd | Microsoft.DevTestLab/labs/read | Läsa övningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Visar en lista över alla tillämpliga scheman |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/delete | Ta bort tjänsten utlöpare. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/read | Läsa service utlöpare. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/write | Lägg till eller ändra tjänsten utlöpare. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/delete | Ta bort användarprofiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Attach/action | Ansluta och skapa lånet på disken för att den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/delete | Ta bort diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Detach/action | Koppla från och ta bort lånet för den disk som är ansluten till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/read | Läsa diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/write | Lägg till eller ändra diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/read | Läsa miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/read | Läsa användarprofiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/delete | Ta bort hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/read | Läsa hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/write | Lägg till eller ändra hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Ta bort service fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Visar en lista över alla tillämpliga scheman |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Läsa service fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Starta ett service fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stoppa ett service fabric |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Lägg till eller ändra service fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/write | Lägg till eller ändra användarprofiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Ansluta en ny eller befintlig datadisk till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Tillämpa artefakter till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Bli ägare till en befintlig virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/delete | Ta bort virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Koppla bort den angivna disken från den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Visar en lista över alla tillämpliga scheman |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/read | Läsa virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Distribuera en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ändra storlek på virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Starta om en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Starta en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Stoppa en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Överför ägarskapet för virtuella hårddiskar till dig själv |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Släpp ägarskap för en befintlig virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/write | Lägg till eller ändra virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/delete | Ta bort virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/read | Läsa virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/write | Lägg till eller ändra virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/delete | Ta bort virtuella pooler. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/read | Läsa pooler med virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/write | Lägg till eller ändra virtuella pooler. |
> | Åtgärd | Microsoft.DevTestLab/labs/write | Lägg till eller ändra övningar. |
> | Åtgärd | Microsoft.DevTestLab/locations/operations/read | Läs-och skrivåtgärder. |
> | Åtgärd | Microsoft.DevTestLab/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.DevTestLab/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/schedules/Execute/action | Köra ett schema. |
> | Åtgärd | Microsoft.DevTestLab/schedules/read | Läsa scheman. |
> | Åtgärd | Microsoft.DevTestLab/schedules/Retarget/action | Uppdaterar ett schema målresurs Id. |
> | Åtgärd | Microsoft.DevTestLab/schedules/write | Lägg till eller ändra scheman. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DocumentDB/databaseAccountNames/read | Söker efter tillgänglighet. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Ändra resursgruppen för ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Läser samlingen måttdefinitioner. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Läser samling mått. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Läsa databas konto partition nivån nyckelvärden |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Läsa databas konto partition nivån måtten |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Läsa databas konto partitioner i en samling |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/usages/read | Läsa databaskonto partition nivån användningsområden |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/usages/read | Läser samling användningsområden. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Läser databasen måttdefinitioner |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/metrics/read | Läser databasen mått. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/usages/read | Läser databasen användningsområden. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/delete | Tar bort databasen konton. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/failoverPriorityChange/action | Ändra prioriteringarna för växling vid fel för områden i ett databaskonto. Används för att utföra manuell redundans |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Hämta anslutningssträngar för ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/listKeys/action | Lista nycklar för ett databaskonto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Läser databasen konto mått definitioner. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/metrics/read | Läser databasen konto mått. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/operationResults/read | Lässtatus för asynkron åtgärd |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/metrics/read | Läsa måtten för svarstid |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/read | Läs percentiler av replikeringsfördröjningar |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read | Läsa latens måtten för en viss region för källa och mål |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/percentile/targetRegion/metrics/read | Läsa latens måtten för en specifik målsökväg region |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga catageries för konto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för databasen konto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/read | Läser ett databaskonto. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databasen readonly nycklar. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/readonlykeys/read | Läser databasen readonly nycklar. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/regenerateKey/action | Rotera ett databaskonto nycklar |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/metrics/read | Läser regionala samling mått. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Läsa regionala databasen konto partition nivån nyckelvärden |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/metrics/read | Läsa regionala databasen konto partition nivån måtten |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitions/read | Läsa regionala databasen konto partitioner i en samling |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/metrics/read | Läser konto mätvärdena region och databasen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/usages/read | Läser databasen konto användningsområden. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/write | Uppdatera en databas-konton. |
> | Åtgärd | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft.DocumentDB att VirtualNetwork eller undernät tas bort |
> | Åtgärd | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Läsa Status för deleteVirtualNetworkOrSubnets asynkron åtgärd |
> | Åtgärd | Microsoft.DocumentDB/operationResults/read | Lässtatus för asynkron åtgärd |
> | Åtgärd | Microsoft.DocumentDB/operations/read | Läs-och skrivåtgärder som är tillgängliga för Microsoft DocumentDB  |
> | Åtgärd | Microsoft.DocumentDB/register/action |  Registerresursleverantören Microsoft DocumentDB för prenumerationen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DomainRegistration/checkDomainAvailability/Action | Kontrollera om en domän kan köpas |
> | Åtgärd | Microsoft.DomainRegistration/domains/Delete | Ta bort en befintlig domän. |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Delete | Ta bort ägarskap identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Lista över ägarskapet identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Hämta ägarskap identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Skapa eller uppdatera identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/operationresults/Read | En åtgärd som domän |
> | Åtgärd | Microsoft.DomainRegistration/domains/Read | Hämta listan över domäner |
> | Åtgärd | Microsoft.DomainRegistration/domains/Read | Hämta domän |
> | Åtgärd | Microsoft.DomainRegistration/domains/renew/Action | Förnya en befintlig domän. |
> | Åtgärd | Microsoft.DomainRegistration/domains/Write | Lägg till en ny domän eller uppdatera en befintlig |
> | Åtgärd | Microsoft.DomainRegistration/generateSsoRequest/Action | Skapa en begäran för att logga in kontrollcenter för domänen. |
> | Åtgärd | Microsoft.DomainRegistration/listDomainRecommendations/Action | Hämta listan domän rekommendationer baserat på nyckelord |
> | Åtgärd | Microsoft.DomainRegistration/operations/Read | Visa en lista med alla åtgärder från domänregistrering för app service |
> | Åtgärd | Microsoft.DomainRegistration/register/action | Microsoft Domains registerresursleverantören för prenumerationen |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | Listan avtal åtgärd |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/Read | Hämta toplevel domäner |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/Read | Hämta toplevel domän |
> | Åtgärd | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Verifiera domän köp objekt utan att skicka den |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/read | Visa Microsoft Dynamics AX 2012 R3 utvärdering distributioner i Microsoft Dynamics livscykel Services-projekt som hör till en användare |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/clouddeployments/write | Skapa Microsoft Dynamics AX 2012 R3 utvärdering distribution i ett Microsoft Dynamics livscykel Services-projekt som hör till en användare. Distributioner kan hanteras från Azure-hanteringsportalen |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/connectors/read | Läs kopplingar som hör till ett Microsoft Dynamics livscykel Services-projekt |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/connectors/write | Skapa och uppdatera kopplingar som hör till ett Microsoft Dynamics livscykel Services-projekt |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/delete | Ta bort Microsoft Dynamics livscykel Services-projekt som hör till användaren |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/read | Visa Microsoft Dynamics livscykel Services-projekt som hör till en användare |
> | Åtgärd | Microsoft.DynamicsLcs/lcsprojects/write | Skapa och uppdatera Microsoft Dynamics livscykel Services-projekt som tillhör användaren. Endast namn och beskrivning egenskaper kan uppdateras. Prenumeration och plats som är kopplade till projektet kan inte uppdateras när den har skapats |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/delete | Ta bort en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Hämta fullständiga URL: en för händelseprenumerationen |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för prenumerationer på händelser |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för prenumerationer på händelser |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för eventSubscriptions |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/read | Läs en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/write | Skapa eller uppdatera en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för avsnitt |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för avsnitt |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för avsnitt |
> | Åtgärd | Microsoft.EventGrid/register/action | Registrerar eventSubscription för resursprovidern EventGrid och kan skapa händelse rutnätet prenumerationer. |
> | Åtgärd | Microsoft.EventGrid/topics/delete | Ta bort ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/listKeys/action | Lista nycklar för avsnittet |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningen för avsnitt |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för avsnitt |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för avsnitt |
> | Åtgärd | Microsoft.EventGrid/topics/read | Läs ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/regenerateKey/action | Generera nyckel för avsnittet |
> | Åtgärd | Microsoft.EventGrid/topics/write | Skapa eller uppdatera ett avsnitt |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventHub/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.EventHub/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället. |
> | Åtgärd | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över kluster mått resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/clusters/read | Hämtar beskrivning av klusterresurs |
> | Åtgärd | Microsoft.EventHub/clusters/write | Hämtar beskrivning av klusterresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/action | Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/delete | Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/write | Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Åtgärden Uppdatera EventHub. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/delete | Åtgärden ta bort EventHub-auktoriseringsregler |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/listkeys/action | Hämta anslutningssträngen till EventHub |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Hämta en lista över EventHub-auktoriseringsregler |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Skapa auktoriseringsregler för EventHub och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventHubs/consumergroups/Delete | Åtgärden ta bort ConsumerGroup resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/eventHubs/consumergroups/read | Hämta lista över ConsumerGroup resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/eventHubs/consumergroups/write | Skapa eller uppdatera ConsumerGroup egenskaper. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/Delete | Åtgärden ta bort EventHub-resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/read | Hämta lista över EventHub resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/write | Skapa eller uppdatera EventHub-egenskaper. |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/read | Hämtar meddelanden planera för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare...<br>Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/write | Uppdaterar Messaging planera för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare...<br>Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta lista över Namespace diagnostikinställningar resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta lista över Namespace diagnostikinställningar resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta en lista över Namespace loggar resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Åtgärd | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.EventHub/namespaces/write | Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.EventHub/register/action | Registrerar prenumerationen för EventHub-resursprovidern och gör det möjligt att skapa EventHub-resurser |
> | Åtgärd | Microsoft.EventHub/sku/read | Hämta lista över Sku resurs beskrivningar |
> | Åtgärd | Microsoft.EventHub/sku/regions/read | Hämta lista över SkuRegions resurs beskrivningar |
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

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.HDInsight/clusters/changerdpsetting/action | Ändra RDP-inställningen för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/action | Uppdatera konfigurationen för HDInsight-kluster |
> | Åtgärd | Microsoft.HDInsight/clusters/configurations/read | Hämta konfigurationer för HDInsight-kluster |
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
> | Åtgärd | Microsoft.ImportExport/locations/read | Hämtar egenskaperna för den angivna platsen eller returnerar en lista med platser. |
> | Åtgärd | Microsoft.ImportExport/register/action | Registrerar prenumerationen för resursprovidern import/export- och kan skapa jobb för import och export. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Insights/ActionGroups/Delete | Tar bort en åtgärdsgrupp |
> | Åtgärd | Microsoft.Insights/ActionGroups/Read | Läser en åtgärdsgrupp |
> | Åtgärd | Microsoft.Insights/ActionGroups/Write | Skriver en åtgärdsgrupp |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivitetsloggavisering har utlösts |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Delete | Tar bort en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Read | Läser en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Write | Läser en aktivitetsloggavisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Activated/Action | Varningsregeln har aktiverats |
> | Åtgärd | Microsoft.Insights/AlertRules/Delete | Tar bort en varningsregelskonfiguration |
> | Åtgärd | Microsoft.Insights/AlertRules/Incidents/Read | Läser en konfiguration för varningsregelincidenter |
> | Åtgärd | Microsoft.Insights/AlertRules/Read | Läser en varningsregelskonfiguration |
> | Åtgärd | Microsoft.Insights/AlertRules/Resolved/Action | Varningsregeln har lösts |
> | Åtgärd | Microsoft.Insights/AlertRules/Throttled/Action | Varningsregeln begränsas |
> | Åtgärd | Microsoft.Insights/AlertRules/Write | Skriver till en varningsregelskonfiguration |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Delete | Tar bort en autoskalningskonfiguration |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Read | Läser en autoskalningskonfiguration |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Åtgärd för autoskalning nedåt |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Åtgärd för autoskalning uppåt |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Write | Skriver till en autoskalningskonfiguration |
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
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Read | Läser ett prisavtal för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Write | Skriver ett prisavtal för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Läser konfiguration av proaktiv identifiering för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Skriver konfiguration av proaktiv identifiering för Application Insights |
> | Åtgärd | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/Components/QuotaStatus/Read | Läser kvotstatus för Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/Read | Läser en konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Läser Application Insights-webbtestplatser |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Delete | Tar bort en konfiguration av ALM-integrering i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Read | Läser en konfiguration av ALM-integrering i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Write | Skriver en konfiguration av ALM-integrering i Application Insights |
> | Åtgärd | Microsoft.Insights/Components/Write | Skriver till en konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Delete | Tar bort konfigurationen av diagnostikinställningar |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Read | Läser en konfiguration av diagnostikinställningar |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Write | Skriver till konfiguration av diagnostikinställningar |
> | Åtgärd | Microsoft.Insights/EventCategories/Read | Läser en händelsekategori |
> | Åtgärd | Microsoft.Insights/eventtypes/digestevents/Read | Läs sammandrag av hanteringshändelsetyp |
> | Åtgärd | Microsoft.Insights/eventtypes/values/Read | Läs värden för hanteringshändelsetyp |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Tar bort konfigurationen av utökade diagnostikinställningar |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Läser konfiguration av utökade diagnostikinställningar |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Skriver till konfiguration av utökade diagnostikinställningar |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Action | Hämta datum för prenumerationsmigrering |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Read | Hämta datum för prenumerationsmigrering |
> | Åtgärd | Microsoft.Insights/LogDefinitions/Read | Läs loggdefinitioner |
> | Åtgärd | Microsoft.Insights/LogProfiles/Delete | Ta bort konfiguration för loggprofiler |
> | Åtgärd | Microsoft.Insights/LogProfiles/Read | Läs loggprofiler |
> | Åtgärd | Microsoft.Insights/LogProfiles/Write | Skriver till en konfiguration för loggprofil |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Delete | Tar bort en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Read | Läser en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Status/Read | Läser status för måttavisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Write | Skriver en måttavisering |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Åtgärd | Microsoft.Insights/Metrics/providers/Metrics/Read | Läs mått |
> | Åtgärd | Microsoft.Insights/Metrics/Read | Läs mått |
> | Åtgärd | Microsoft.Insights/Metrics/Write | Skrivning av mått |
> | Åtgärd | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrera prenumerationen till den nya prismodellen |
> | Åtgärd | Microsoft.Insights/Operations/Read | Läser åtgärder |
> | Åtgärd | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Återställ prenumerationen till den äldre prismodellen |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Delete | Tar bort en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Read | Läser en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Write | Skriver en schemalagd frågeregel |
> | Åtgärd | Microsoft.Insights/Tenants/Register/Action | Startar microsoft insights-providern |
> | Åtgärd | Microsoft.Insights/Unregister/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Webtests/Delete | Tar bort en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Webtests/GetToken/Read | Läser en token för webbtest |
> | Åtgärd | Microsoft.Insights/Webtests/MetricDefinitions/Read | Läser definitioner av mått för webbtest |
> | Åtgärd | Microsoft.Insights/Webtests/Metrics/Read | Läser ett webbtestmätvärde |
> | Åtgärd | Microsoft.Insights/Webtests/Read | Läser en webbtestkonfiguration |
> | Åtgärd | Microsoft.Insights/Webtests/Write | Skriver till en webbtestkonfiguration |

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
> | Åtgärd | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/read | Visa egenskaperna för ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/read | Visa egenskaperna för en hemlighet, men inte dess värde |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/write | Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet |
> | Åtgärd | Microsoft.KeyVault/vaults/write | Skapa ett nytt nyckelvalv eller uppdatera egenskaperna för ett befintligt nyckelvalv |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LabServices/labAccounts/CreateLab/action | Skapa ett labb i ett labb-konto. |
> | Åtgärd | Microsoft.LabServices/labAccounts/delete | Ta bort lab konton. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/delete | Ta bort övningar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/ClaimAny/action | Anspråk en slumpmässig miljö för en användare i en miljöinställningar |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Ta bort inställningen för miljön. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Anspråk miljön och tilldelar den till användaren |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Läsa miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stoppar en miljö genom att stoppa alla resurser i miljön |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Tillhandahåller/deprovisions krävs resurser för en miljö som inställningen baserat på aktuell status för inställningen labb-miljö. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Läsa miljöinställning. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Startar en mall genom att starta alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Startar en mall genom att starta alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Lägg till eller ändra inställningen för miljön. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/read | Läsa övningar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/Register/action | Registrera till hanterade labbet. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/delete | Ta bort användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/read | Läsa användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/write | Lägg till eller ändra användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/write | Lägg till eller ändra övningar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/read | Läsa lab konton. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sizes/read | Läsa storlekar. |
> | Åtgärd | Microsoft.LabServices/labAccounts/write | Lägg till eller ändra lab konton. |
> | Åtgärd | Microsoft.LabServices/locations/operations/read | Läs-och skrivåtgärder. |
> | Åtgärd | Microsoft.LabServices/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.LabServices/users/GetEnvironment/action | Hämtar information för virtuell dator |
> | Åtgärd | Microsoft.LabServices/users/ListEnvironments/action | Lista miljöer för användaren |
> | Åtgärd | Microsoft.LabServices/users/ListLabs/action | Lista över labs för användaren. |
> | Åtgärd | Microsoft.LabServices/users/Register/action | Registrera en användare till en hanterad labbet |
> | Åtgärd | Microsoft.LabServices/users/StartEnvironment/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft.LabServices/users/StopEnvironment/action | Stoppar en miljö genom att stoppa alla resurser i miljön |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/delete | (Föråldrad: Använd /providers/Microsoft.Maps) Ta bort en plats baserat Services-konto. |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/listKeys/action | (Föråldrad: Använd /providers/Microsoft.Maps) Visa en lista över nycklar plats baserat Services-konto |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/read | (Föråldrad: Använd /providers/Microsoft.Maps) Hämtar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/diagnosticSettings/write | (Föråldrad: Använd /providers/Microsoft.Maps) Skapar eller uppdaterar diagnostikinställningen för resursen |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/providers/Microsoft.Insights/metricDefinitions/read | (Föråldrad: Använd /providers/Microsoft.Maps) Hämtar tillgängliga mått för tjänstkonton för plats-baserad |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/read | (Föråldrad: Använd /providers/Microsoft.Maps) Hämta en plats baserat Services-konto. |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/regenerateKey/action | (Föråldrad: Använd /providers/Microsoft.Maps) Skapa ny plats baserat Services-konto primär eller sekundär nyckel |
> | Åtgärd | Microsoft.LocationBasedServices/accounts/write | (Föråldrad: Använd /providers/Microsoft.Maps) Skapa eller uppdatera en plats baserat Services-konto. |
> | Åtgärd | Microsoft.LocationBasedServices/register/action | (Föråldrad: Använd /providers/Microsoft.Maps) Registrera providern |

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
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Läser upprepningen av reservationsomfånget för arbetsflödets körningsåtgärd. |
> | Åtgärd | Microsoft.Logic/workflows/runs/cancel/action | Avbryter körningen av ett arbetsflöde. |
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
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Flytta alla Machine Learning åtagande Plan Association |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Läsa alla Machine Learning åtagande Plan Association |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/delete | Ta bort en dator utbildningsplan åtagande |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/join/action | Ansluta till en dator som utbildningsplan åtagande |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/read | Läs valfri dator utbildningsplan åtagande |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/write | Skapa eller uppdatera alla åtagande utbildningsplan datorn |
> | Åtgärd | Microsoft.MachineLearning/locations/operationresults/read | Hämta resultatet av en Machine Learning-åtgärd |
> | Åtgärd | Microsoft.MachineLearning/locations/operationsstatus/read | Hämta status för en pågående åtgärd i Machine Learning |
> | Åtgärd | Microsoft.MachineLearning/operations/read | Hämta åtgärder för Maskininlärning |
> | Åtgärd | Microsoft.MachineLearning/register/action | Registrerar prenumerationen för maskininlärning web tjänstresursprovider och kan skapa webbtjänster. |
> | Åtgärd | Microsoft.MachineLearning/skus/read | Hämta Machine Learning åtagande Plan SKU: er |
> | Åtgärd | Microsoft.MachineLearning/webServices/action | Skapa regionala egenskaper för webbtjänst för regioner som stöds |
> | Åtgärd | Microsoft.MachineLearning/webServices/delete | Ta bort alla Machine Learning-webbtjänst |
> | Åtgärd | Microsoft.MachineLearning/webServices/listkeys/read | Hämta nycklar till en Machine Learning-webbtjänst |
> | Åtgärd | Microsoft.MachineLearning/webServices/read | Läsa alla Machine Learning-webbtjänst |
> | Åtgärd | Microsoft.MachineLearning/webServices/write | Skapa eller uppdatera en Machine Learning-webbtjänst |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/delete | Ta bort alla Maskininlärning arbetsytan |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Lista nycklar för en Machine Learning-arbetsytan |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/read | Läsa alla Machine Learning arbetsytan |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Synkronisera nycklar för lagringskonto har konfigurerats för en Machine Learning-arbetsytan |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/write | Skapa eller uppdatera alla Maskininlärning arbetsytan |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/checkUpdate/action | Kontrollera om det finns uppdateringar för systemtjänster för operationalization klustret |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/delete | Ta bort alla värd-konto |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/listKeys/action | Lista nycklarna som associerats med operationalization kluster |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/read | Varje värd läskonto |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/updateSystem/action | Uppdatera systemtjänster i ett operationalization kluster |
> | Åtgärd | Microsoft.MachineLearningCompute/operationalizationClusters/write | Skapa eller uppdatera värd-konto |
> | Åtgärd | Microsoft.MachineLearningCompute/register/action | Registrerar prenumerations-ID för resursprovidern och kan skapa machine learning beräkningsresurser |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningModelManagement/accounts/delete | Ta bort alla värd-konto |
> | Åtgärd | Microsoft.MachineLearningModelManagement/accounts/read | Varje värd läskonto |
> | Åtgärd | Microsoft.MachineLearningModelManagement/accounts/write | Skapa eller uppdatera värd-konto |
> | Åtgärd | Microsoft.MachineLearningModelManagement/register/action | Registrerar prenumerations-ID för resursprovidern och kan skapa ett konto för värd |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningServices/register/action | Registrerar prenumerationen för Machine Learning Services Resource Provider |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/delete | Tar bort beräkningsresurser i Machine Learning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Visa en lista med hemligheter för beräkningsresurser i Machine Learning-arbetsytan för tjänster |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/read | Hämtar beräkningsresurserna i Machine Learning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/write | Skapar eller uppdaterar beräkningsresurser i Machine Learning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/delete | Tar bort Maskininlärning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista hemligheter för en arbetsyta i Machine Learning tjänster |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/read | Hämtar Maskininlärning Services arbetsytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/write | Skapar eller uppdaterar en Machine Learning Services arbetsytor |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC-åtgärden för att tilldela en befintlig användare som tilldelats en resurs identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Tar bort en befintlig användare som tilldelats identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hämtar en befintlig användare som tilldelats identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/write | Skapar en ny användare som tilldelats identitet eller uppdaterar taggarna associerade med en befintlig användare som tilldelats identitet |

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/CreateLab/action | Skapa ett labb i ett labb-konto. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/delete | Ta bort lab konton. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/delete | Ta bort övningar. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/delete | Ta bort inställningen för miljön. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/read | Läsa miljöer. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/read | Läsa miljöinställning. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/environmentSettings/write | Lägg till eller ändra inställningen för miljön. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/labVms/delete | Ta bort virtuella datorer för testlabbet. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/labVms/read | Läsa virtuella datorer för testlabbet. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/labVms/write | Lägg till eller ändra virtuella datorer för testlabbet. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/read | Läsa övningar. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/labs/write | Lägg till eller ändra övningar. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/read | Läsa lab konton. |
> | Åtgärd | Microsoft.ManagedLab/labAccounts/write | Lägg till eller ändra lab konton. |
> | Åtgärd | Microsoft.ManagedLab/locations/operations/read | Läs-och skrivåtgärder. |
> | Åtgärd | Microsoft.ManagedLab/register/action | Registrerar prenumerationen |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Management/checkNameAvailability/action | Kontrollerar om tillgänglighetsgruppens angivna namn är giltigt och unikt. |
> | Åtgärd | Microsoft.Management/getEntities/action | Lista över alla enheter (Hanteringsgrupper, prenumerationer, etc.) för den autentiserade användaren. |
> | Åtgärd | Microsoft.Management/managementGroups/delete | Ta bort hanteringsgruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/read | Lista över hanteringsgrupper för den autentiserade användaren. |
> | Åtgärd | Microsoft.Management/managementGroups/subscriptions/delete | Frigör associerar prenumeration från hanteringsgruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/subscriptions/write | Associerats befintliga prenumeration till hanteringsgruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/write | Skapa eller uppdatera en hanteringsgrupp. |
> | Åtgärd | Microsoft.Management/register/action | Registrera den angivna prenumerationen med Microsoft.Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Maps/accounts/delete | Ta bort ett Maps-konto. |
> | Åtgärd | Microsoft.Maps/accounts/listKeys/action | Lista mappar nycklar |
> | Åtgärd | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Maps/accounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Maps/accounts/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Maps-konton |
> | Åtgärd | Microsoft.Maps/accounts/read | Skaffa en Maps-konto. |
> | Åtgärd | Microsoft.Maps/accounts/regenerateKey/action | Skapa ny Maps konto primär eller sekundär nyckel |
> | Åtgärd | Microsoft.Maps/accounts/write | Skapa eller uppdatera en Maps-konto. |
> | Åtgärd | Microsoft.Maps/register/action | Registrera providern |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/read | Returnerar ett avtal. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/agreements/write | Accepterar ett signerat avtal. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/importImage/action | Importerar en bild till slutanvändarens ACR. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/read | Returnerar en config. |
> | Åtgärd | Microsoft.Marketplace/offerTypes/publishers/offers/plans/configs/write | Sparar en config. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/delete | Utför en borttagning på en resurs för klassiska dev-tjänst. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Hämtar en klassiska dev service resurs hantering av nycklar. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Hämtar enkel inloggning på URL: en för en klassiska dev-tjänst. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/read | Matchar en GET-åtgärd på en klassisk dev-tjänst. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Genererar en klassiska dev service resurs hantering av nycklar. |
> | Åtgärd | Microsoft.MarketplaceApps/Operations/read | Läs-och skrivåtgärder för alla typer av resurser. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/offers/plans/cancel/action | Avbryt ett avtal för ett givet marketplace-objekt |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/offers/plans/read | Returnera ett avtal för ett givet marketplace-objekt |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/offers/plans/sign/action | Registrera ett avtal för ett givet marketplace-objekt |
> | Åtgärd | Microsoft.MarketplaceOrdering/agreements/read | Returnera alla avtal enligt angivna prenumerationen |
> | Åtgärd | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/read | Hämta ett avtal för en given marketplace-objektet för virtuell dator |
> | Åtgärd | Microsoft.MarketplaceOrdering/offertypes/publishers/offers/plans/agreements/write | Logga in till eller avbryta ett avtal för en given marketplace-objektet för virtuell dator |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Media/checknameavailability/action | Kontrollerar om det finns ett Media Services-kontonamn |
> | Åtgärd | Microsoft.Media/locations/checkNameAvailability/action | Kontrollerar om det finns ett Media Services-kontonamn |
> | Åtgärd | Microsoft.Media/mediaservices/assets/delete | Ta bort alla tillgångar |
> | Åtgärd | Microsoft.Media/mediaservices/assets/getEncryptionKey/action | Hämta krypteringsnyckeln för tillgångsinformation |
> | Åtgärd | Microsoft.Media/mediaservices/assets/listContainerSas/action | Lista tillgången behållaren SAS-URL: er |
> | Åtgärd | Microsoft.Media/mediaservices/assets/read | Läs valfri tillgång |
> | Åtgärd | Microsoft.Media/mediaservices/assets/write | Skapa eller uppdatera alla tillgångar |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Ta bort valfri innehåll viktiga princip |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/getPolicyPropertiesWithSecrets/action | Hämta Principegenskaper för med hemligheter |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/read | Läs valfri innehåll Key-princip |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/write | Skapa eller uppdatera innehåll viktiga princip |
> | Åtgärd | Microsoft.Media/mediaservices/delete | Ta bort alla Media Services-konto |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/delete | Ta bort händelsen rutnätet Filter |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/read | Läsa alla händelsefilter rutnätet |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/write | Skapa eller uppdatera händelse rutnätet Filter |
> | Åtgärd | Microsoft.Media/mediaservices/liveEventOperations/read | Läsa alla åtgärder direktsänd händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/delete | Ta bort alla direktsända händelser |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/delete | Ta bort alla aktiva utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/read | Läsa alla Live utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Skapa eller uppdatera några Live utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/read | Läsa alla direktsända händelser |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/reset/action | Återställ alla åtgärder direktsänd händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/start/action | Starta alla åtgärder direktsänd händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/stop/action | Avbryta en direktsänd händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/write | Skapa eller uppdatera en direktsänd händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveOutputOperations/read | Läsa alla Live utdata-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/read | Läsa alla Media Services-konto |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpointOperations/read | Läsa alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/delete | Ta bort alla strömmande slutpunkt |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/read | Läs valfri strömmande slutpunkt |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skala någon strömmande slutpunkt-åtgärd |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/start/action | Starta alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/stop/action | Stoppa alla strömmande slutpunkt-åtgärder |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/write | Skapa eller uppdatera alla strömmande slutpunkt |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/delete | Ta bort alla Strömningslokaliserare |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista nycklar för Multiinnehåll |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/listPaths/action | Lista över sökvägar |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/read | Läsa alla Strömningslokaliserare |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/write | Skapa eller uppdatera alla Strömningslokaliserare |
> | Åtgärd | Microsoft.Media/mediaservices/streamingPolicies/delete | Ta bort valfri strömmande princip |
> | Åtgärd | Microsoft.Media/mediaservices/streamingPolicies/read | Läs valfri strömmande princip |
> | Åtgärd | Microsoft.Media/mediaservices/streamingPolicies/write | Skapa eller uppdatera strömmande princip |
> | Åtgärd | Microsoft.Media/mediaservices/syncStorageKeys/action | Synkronisera Lagringsnycklar för ett anslutna Azure Storage-konto |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/delete | Ta bort en transformering |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/cancelJob/action | Avbryta jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/delete | Ta bort alla jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/read | Läsa jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/jobs/write | Skapa eller uppdatera jobb |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/read | Läs en transformering |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/write | Skapa eller uppdatera en transformering |
> | Åtgärd | Microsoft.Media/mediaservices/write | Skapa eller uppdatera alla Media Services-konto |
> | Åtgärd | Microsoft.Media/operations/read | Hämta tillgängliga åtgärder |
> | Åtgärd | Microsoft.Media/register/action | Registrerar prenumerationen för resursprovidern Media Services och används för att skapa Media Services-konton |
> | Åtgärd | Microsoft.Media/unregister/action | Avregistrerar prenumerationen för Media Services-resursprovidern |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Migrate/Operations/read | Läser de exponerade åtgärderna |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Programmet Gateway Ssl fördefinierade princip |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Programmet Gateway Ssl alternativen |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Hämtar tillgängliga brandvägg regeluppsättningar Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter en serverdelsadresspool för programmet gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/backendhealth/action | Hämtar en programhälsan gateway backend |
> | Åtgärd | Microsoft.Network/applicationGateways/delete | Tar bort en Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/effectiveNetworkSecurityGroups/action | Hämta routningstabell som konfigurerats på Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/effectiveRouteTable/action | Hämta routningstabell som konfigurerats på Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/read | Hämtar en Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/setSecurityCenterConfiguration/action | Anger programmet Gateway Security Center-konfiguration |
> | Åtgärd | Microsoft.Network/applicationGateways/start/action | Startar en Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/stop/action | Stoppar en Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/write | Skapar eller uppdaterar en Programgateway |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/delete | Tar bort en säkerhetsgrupp för programmet |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/joinIpConfiguration/action | Ansluter till en IP-konfiguration till säkerhetsgrupper för programmet. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Kopplar ihop en säkerhetsregel säkerhetsgrupper för programmet. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/read | Hämtar en säkerhetsgrupp för program-ID. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/write | Skapar en säkerhetsgrupp för programmet eller uppdaterar en befintlig säkerhetsgrupp för programmet. |
> | Åtgärd | Microsoft.Network/bgpServiceCommunities/read | Hämta Bgp Service Communities |
> | Åtgärd | Microsoft.Network/checkTrafficManagerNameAvailability/action | Kontrollerar tillgängligheten för ett Traffic Manager relativt DNS-namn. |
> | Åtgärd | Microsoft.Network/connections/delete | Tar bort VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/connections/read | Hämtar VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/connections/sharedkey/action | Hämta VirtualNetworkGatewayConnection SharedKey |
> | Åtgärd | Microsoft.Network/connections/sharedKey/read | Hämtar VirtualNetworkGatewayConnection SharedKey |
> | Åtgärd | Microsoft.Network/connections/sharedKey/write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection SharedKey |
> | Åtgärd | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Hämtar Vpn-enhet konfigurationen av VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/connections/write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/delete | Tar bort en DDoS-skydd Plan Proxy |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/read | Hämtar en definition av DDoS-skydd planera Proxy |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/ddosProtectionPlanProxies/write | Skapar en DDoS-skydd planera Proxy eller uppdateringar och befintliga DDoS-skydd planera Proxy |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/delete | Tar bort en Plan för DDoS-skydd |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/join/action | Ansluter till en Plan för DDoS-skydd |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/read | Hämtar en Plan för DDoS-skydd |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/write | Skapar en Plan för DDoS-skydd eller uppdaterar en Plan för DDoS-skydd  |
> | Åtgärd | Microsoft.Network/dnsoperationresults/read | Hämtar resultat av en DNS-åtgärd |
> | Åtgärd | Microsoft.Network/dnsoperationstatuses/read | Hämtar status för en DNS-åtgärd  |
> | Åtgärd | Microsoft.Network/dnszones/A/delete | Ta bort postuppsättningen för ett angivet namn och typen ”A” från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/A/read | Hämta uppsättningen poster av typen ”A” i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/A/write | Skapa eller uppdatera en uppsättning poster av typen ”A” i en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/delete | Ta bort postuppsättningen för ett angivet namn och typen AAAA om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/read | Hämta postuppsättningen av typen AAAA, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/write | Skapa eller uppdatera en uppsättning poster av typen 'AAAA' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/all/read | Hämtar DNS-postuppsättningar över typer |
> | Åtgärd | Microsoft.Network/dnszones/CAA/delete | Ta bort postuppsättningen för ett angivet namn och typen CAA om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/CAA/read | Hämta postuppsättningen av typen CAA, i JSON-format. Uppsättningen poster innehåller TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CAA/write | Skapa eller uppdatera en uppsättning poster av typen 'CAA' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/delete | Ta bort postuppsättningen för ett angivet namn och typen CNAME om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/read | Hämta postuppsättningen av typen CNAME, i JSON-format. Uppsättningen poster innehåller TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/write | Skapa eller uppdatera en uppsättning poster av typen 'CNAME' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/delete | Ta bort DNS-zonen i JSON-format. Zonegenskaperna omfattar taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. |
> | Åtgärd | Microsoft.Network/dnszones/MX/delete | Ta bort postuppsättningen för ett angivet namn och typen MX om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/MX/read | Hämta postuppsättningen av typen MX, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/MX/write | Skapa eller uppdatera en uppsättning poster av typen 'MX' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/NS/delete | Tar bort DNS-uppsättningen av poster av typen NS |
> | Åtgärd | Microsoft.Network/dnszones/NS/read | Hämtar uppsättning av DNS-poster av typen NS |
> | Åtgärd | Microsoft.Network/dnszones/NS/write | Skapar eller uppdaterar DNS-postuppsättning av typen NS |
> | Åtgärd | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningar för DNS-zonen |
> | Åtgärd | Microsoft.Network/dnszones/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för DNS-zonen |
> | Åtgärd | Microsoft.Network/dnszones/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitionerna för DNS-zonen |
> | Åtgärd | Microsoft.Network/dnszones/PTR/delete | Ta bort postuppsättningen för ett angivet namn och typen PTR om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/PTR/read | Hämta postuppsättningen av typen PTR, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/PTR/write | Skapa eller uppdatera en uppsättning poster av typen 'PTR' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/read | Hämta DNS-zonen i JSON-format. Zonegenskaperna omfattar taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. Observera att det här kommandot inte hämtar postuppsättningar i zonen. |
> | Åtgärd | Microsoft.Network/dnszones/recordsets/read | Hämtar DNS-postuppsättningar över typer |
> | Åtgärd | Microsoft.Network/dnszones/SOA/read | Hämtar uppsättning av DNS-poster av typen SOA |
> | Åtgärd | Microsoft.Network/dnszones/SOA/write | Skapar eller uppdaterar DNS-postuppsättning av typen SOA |
> | Åtgärd | Microsoft.Network/dnszones/SRV/delete | Ta bort postuppsättningen för ett angivet namn och typen SRV om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/read | Hämta postuppsättningen av typen SRV, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/write | Skapa eller uppdatera en postuppsättning av typen SRV |
> | Åtgärd | Microsoft.Network/dnszones/TXT/delete | Ta bort postuppsättningen för ett angivet namn och typen TXT om du från en DNS-zon. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/read | Hämta postuppsättningen av typen TXT, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/write | Skapa eller uppdatera en uppsättning poster av typen 'TXT' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster. |
> | Åtgärd | Microsoft.Network/dnszones/write | Skapa eller uppdatera en DNS-zon i en resursgrupp.  Används för att uppdatera taggarna i en DNS-resursposter i zonen. Observera att det här kommandot inte kan användas för att skapa eller uppdatera postuppsättningar i zonen. |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/delete | Tar bort ExpressRouteCircuit tillstånd |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/read | Hämtar ExpressRouteCircuit tillstånd |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/delete | Tar bort en ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/join/action | Ansluter till en Express Route-krets |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/arpTables/action | Hämtar en ExpressRouteCircuit Peering ArpTable |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Tar bort en ExpressRouteCircuit-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Hämtar en ExpressRouteCircuit-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit anslutning resurs |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/delete | Tar bort en ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/read | Hämtar en ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/routeTables/action | Hämtar en ExpressRouteCircuit Peering Migreringstillståndet |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Hämtar en ExpressRouteCircuit Peering Migreringstillståndet sammanfattning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Hämtar en ExpressRouteCircuit Peering Stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningar för ExpressRoute-kretsar |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för ExpressRoute-kretsar |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read | Hämta händelser för ExpressRoute-kretsar |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read | Hämtar mått definitioner för ExpressRoute-kretsar |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/read | Hämta en ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/stats/read | Hämtar en ExpressRouteCircuit Stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/delete | Ta bort Expressroute mellan anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/join/action | Ansluter till en Express Route mellan anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/action | Hämtar en Express Route gränsöverskridande anslutning Peering Arp-tabell |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Tar bort en Express Route gränsöverskridande anslutning Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/read | Hämtar en Express Route gränsöverskridande anslutning Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/action | Hämtar en Express Route gränsöverskridande anslutning Peering routningstabellen |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Hämtar en Express Route gränsöverskridande anslutning Peering väg tabellsammanfattning |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/write | Skapar en mellan anslutning Peering i Expressroute eller uppdaterar en befintlig mellan anslutning Peering i Expressroute |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/read | Hämta Express Route mellan anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/write | Skapa eller uppdatera Express Route mellan anslutning |
> | Åtgärd | Microsoft.Network/expressRouteServiceProviders/read | Hämtar Express Route-leverantörer |
> | Åtgärd | Microsoft.Network/intendedPolicies/delete | Tar bort en principen |
> | Åtgärd | Microsoft.Network/intendedPolicies/read | Hämtar en beskrivning för principen |
> | Åtgärd | Microsoft.Network/intendedPolicies/write | Skapar en princip som är avsedd eller uppdaterar en befintlig avsedd Policy |
> | Åtgärd | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en belastningsutjämnare adress serverdelspool |
> | Åtgärd | Microsoft.Network/loadBalancers/backendAddressPools/read | Hämtar en load balancer definition av serverdelsadresspool |
> | Åtgärd | Microsoft.Network/loadBalancers/delete | Tar bort en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/frontendIPConfigurations/read | Hämtar en klientdel IP-konfiguration belastningsutjämnardefinition |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter till en belastningsutjämnare inkommande nat-pool |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatPools/read | Hämtar en belastningsutjämnare definition för inkommande nat-pool |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/delete | Tar bort en inkommande nat-regel för belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande nat-regel för belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/read | Hämtar en belastningsutjämnare definition för inkommande nat-regel |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/write | Skapar en inkommande nat-regel för belastningsutjämnare eller uppdaterar en befintlig inkommande nat-regel för belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/loadBalancingRules/read | Hämtar en belastningsutjämnare belastningsutjämnardefinition av belastningsutjämningsregel |
> | Åtgärd | Microsoft.Network/loadBalancers/networkInterfaces/read | Hämtar hänvisningar till alla nätverksgränssnitt under en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/outboundNatRules/read | Hämtar en utgående nat-regel belastningsutjämnardefinition |
> | Åtgärd | Microsoft.Network/loadBalancers/probes/join/action | Kan använda avsökningar av belastningsutjämning. Med den här behörigheten healthProbe egenskapen VM scale kan set referera exempelvis avsökningen. |
> | Åtgärd | Microsoft.Network/loadBalancers/probes/read | Hämtar en belastningsutjämningsavsökning |
> | Åtgärd | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar de diagnostiska inställningarna för belastningsutjämnaren |
> | Åtgärd | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för belastningen belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/read | Hämtar en belastningsutjämnardefinition |
> | Åtgärd | Microsoft.Network/loadBalancers/virtualMachines/read | Hämtar hänvisningar till alla virtuella datorer under en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/write | Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare |
> | Åtgärd | Microsoft.Network/localnetworkgateways/delete | Tar bort LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/localnetworkgateways/read | Hämtar LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/localnetworkgateways/write | Skapar eller uppdaterar en befintlig LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/locations/availableDelegations/read | Hämtar tillgängliga delegeringar |
> | Åtgärd | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Kontrollerar snabbare nätverk stöd |
> | Åtgärd | Microsoft.Network/locations/checkDnsNameAvailability/read | Kontrollerar om dns-etikett finns på den angivna platsen |
> | Åtgärd | Microsoft.Network/locations/operationResults/read | Hämtar resultat av en asynkron post- eller DELETE-åtgärden |
> | Åtgärd | Microsoft.Network/locations/operations/read | Hämtar åtgärdsresurs motsvarande status för en asynkron åtgärd |
> | Åtgärd | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Hämtar stöds storlekar för virtuella datorer |
> | Åtgärd | Microsoft.Network/locations/usages/read | Hämtar resurserna användningsstatistik |
> | Åtgärd | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Hämtar en lista med tillgängliga virtuella Endpoint nätverkstjänster |
> | Åtgärd | Microsoft.Network/networkInterfaces/delete | Tar bort ett nätverksgränssnitt |
> | Åtgärd | Microsoft.Network/networkInterfaces/diagnosticIdentity/read | Hämtar diagnostiska identiteten för resursen |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hämta Nätverkssäkerhetsgrupp konfigurerade på nätverket gränssnitt för den virtuella datorns |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Hämta routningstabell som konfigurerats på nätverksgränssnitt för den virtuella datorn |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/read | Hämtar en IP-konfigurationsdefinition av nätverksgränssnitt.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverksgränssnitt |
> | Åtgärd | Microsoft.Network/networkInterfaces/joinViaPrivateIp/action | Ansluter till en resurs till ett nätverksgränssnitt via en Association för tjänsten |
> | Åtgärd | Microsoft.Network/networkInterfaces/loadBalancers/read | Hämtar alla belastningsutjämnare som nätverksgränssnittet tillhör |
> | Åtgärd | Microsoft.Network/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för nätverksgränssnittet |
> | Åtgärd | Microsoft.Network/networkInterfaces/read | Hämtar en definition av nätverksgränssnitt.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/serviceAssociations/delete | Tar bort en Association för tjänsten |
> | Åtgärd | Microsoft.Network/networkInterfaces/serviceAssociations/read | Hämtar en Definition av tjänsten Association |
> | Åtgärd | Microsoft.Network/networkInterfaces/serviceAssociations/validate/action | Verifierar en Association för tjänsten |
> | Åtgärd | Microsoft.Network/networkInterfaces/serviceAssociations/write | Skapar en ny Service Association eller ändrar en befintlig tjänst-koppling |
> | Åtgärd | Microsoft.Network/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar en befintlig nätverksgränssnitt.  |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Hämtar en definition av säkerhetsregel standard |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/delete | Tar bort en nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/join/action | Ansluter en nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningarna för nätverkssäkerhetsgrupper |
> | Åtgärd | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för nätverkssäkerhetsgrupper. Åtgärden kompletteras av resursprovidern för Insights. |
> | Åtgärd | Microsoft.Network/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/read | Hämtar en definition av nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/delete | Tar bort en säkerhetsregel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/read | Hämtar en definition av säkerhetsregel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/write | Skapar en säkerhetsregel eller uppdaterar en befintlig säkerhetsregel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/write | Skapar en nätverkssäkerhetsgrupp eller uppdaterar en befintlig nätverkssäkerhetsgrupp |
> | Åtgärd | Microsoft.Network/networkWatchers/availableProvidersList/action | Returnerar alla tillgängliga internet-leverantörer för angivna Azure-region. |
> | Åtgärd | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returnerar relativa latens poängen för internet-leverantörer från en angiven plats till Azure-regioner. |
> | Åtgärd | Microsoft.Network/networkWatchers/configureFlowLog/action | Konfigurerar flöde loggning för en målresurs. |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/delete | Tar bort en anslutning Övervakare |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningar för övervakaren anslutning |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för anslutningen Övervakaren |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för övervakaren anslutning |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Frågan övervaka anslutningar mellan angivna slutpunkter |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/read | Hämta information om övervakaren anslutning |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Börja övervaka anslutningen mellan angivna slutpunkter |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Stoppa och pausa övervaka anslutningar mellan angivna slutpunkter |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/write | Skapar en anslutning Övervakare |
> | Åtgärd | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifierar möjligheten att upprätta en direkt TCP-anslutning från en virtuell dator till en viss slutpunkten, inklusive en annan virtuell dator eller en godtycklig fjärrserver. |
> | Åtgärd | Microsoft.Network/networkWatchers/delete | Tar bort en nätverksbevakaren |
> | Åtgärd | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returnerar om paketet tillåts eller nekas till eller från en viss destination. |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/delete | Tar bort en lins |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/query/action | Frågan övervaka nätverkstrafik på den angivna slutpunkten |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/read | Hämta lins information |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/start/action | Börja övervaka nätverkstrafik på den angivna slutpunkten |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/stop/action | Stoppa och pausa övervaka nätverkstrafik på den angivna slutpunkten |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/write | Skapar en lins |
> | Åtgärd | Microsoft.Network/networkWatchers/nextHop/action | Returnerar nästa hopptyp och nästa hoppas IP-adress för en angiven mål- och mål-IP-adress. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/delete | Tar bort en paketinsamling |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Hämtar information om egenskaper och status för en resurs för avbildningen av paketet. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/read | Hämta paket avbilda definition |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stoppa körs paket avbildningssessionen. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/write | Skapar en paketinsamling |
> | Åtgärd | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Hämtar status för flöde loggning på en resurs. |
> | Åtgärd | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Felsökning resultatet från körts tidigare eller för närvarande hämtar kör felsökning igen. |
> | Åtgärd | Microsoft.Network/networkWatchers/read | Hämta definition för Övervakare nätverk |
> | Åtgärd | Microsoft.Network/networkWatchers/securityGroupView/action | Visa de konfigurerade och effektivt regler för nätverkssäkerhetsgrupper tillämpas på en virtuell dator. |
> | Åtgärd | Microsoft.Network/networkWatchers/topology/action | Hämtar en nätverket vy på protokollnivå för resurser och deras relationer i en resursgrupp. |
> | Åtgärd | Microsoft.Network/networkWatchers/troubleshoot/action | Startar felsökning på en resurs för nätverk i Azure. |
> | Åtgärd | Microsoft.Network/networkWatchers/write | Skapar en nätverksbevakaren eller uppdaterar en befintlig nätverksbevakaren |
> | Åtgärd | Microsoft.Network/operations/read | Hämta tillgängliga åtgärder |
> | Åtgärd | Microsoft.Network/publicIPAddresses/delete | Tar bort en offentlig Ip-adress. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig ip-adress |
> | Åtgärd | Microsoft.Network/publicIPAddresses/loadBalancerPools/delete | Tar bort en offentlig IP-adress belastningsutjämnarens serverdelspool |
> | Åtgärd | Microsoft.Network/publicIPAddresses/loadBalancerPools/join/action | Ansluter till en offentlig IP-adress belastningsutjämnarens serverdelspool |
> | Åtgärd | Microsoft.Network/publicIPAddresses/loadBalancerPools/read | Hämtar en offentlig IP-adress i serverdelen poolen belastningsutjämnardefinition |
> | Åtgärd | Microsoft.Network/publicIPAddresses/loadBalancerPools/write | Skapar en offentlig IP-adress belastningsutjämnarens serverdelspool eller uppdaterar en befintlig offentlig IP-adress belastningsutjämnarens serverdelspool |
> | Åtgärd | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningar för offentlig IP-adress |
> | Åtgärd | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write | Skapa eller uppdatera diagnostikinställningar för offentlig IP-adress |
> | Åtgärd | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read | Hämta Loggdefinitioner för offentlig IP-adress |
> | Åtgärd | Microsoft.Network/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read | Hämta mätvärden definitionerna av offentlig IP-adress |
> | Åtgärd | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig ip-adress. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/write | Skapar en offentlig Ip-adress eller uppdaterar en befintlig offentlig Ip-adress.  |
> | Åtgärd | Microsoft.Network/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.Network/routeFilters/delete | Tar bort en väg filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/join/action | Ansluter till en vägfilter |
> | Åtgärd | Microsoft.Network/routeFilters/read | Hämtar en definition av flödet filter |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/delete | Tar bort en regel för vägen filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/read | Hämtar en regel för vägen filterdefinition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/write | Skapar en regel för vägen filter eller uppdaterar en befintlig väg filter regel |
> | Åtgärd | Microsoft.Network/routeFilters/write | Skapar ett vägfilter eller uppdaterar ett befintligt rotue filter |
> | Åtgärd | Microsoft.Network/routeTables/delete | Tar bort en definition av routningstabell |
> | Åtgärd | Microsoft.Network/routeTables/join/action | Ansluter till en routningstabell |
> | Åtgärd | Microsoft.Network/routeTables/read | Hämtar en definition av routningstabell |
> | Åtgärd | Microsoft.Network/routeTables/routes/delete | Tar bort en väg definition |
> | Åtgärd | Microsoft.Network/routeTables/routes/read | Hämtar en definition av väg |
> | Åtgärd | Microsoft.Network/routeTables/routes/write | Skapar en väg eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Network/routeTables/write | Skapar en routningstabell eller uppdaterar en befintlig tabell rotue |
> | Åtgärd | Microsoft.Network/securegateways/applicationRuleCollections/delete | Tar bort en regelsamling för programmet för säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/applicationRuleCollections/read | Hämta en regelsamling för programmet för en viss säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/applicationRuleCollections/write | Skapar eller uppdaterar en regelsamling för programmet för en säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/delete | Ta bort säkra Gateway |
> | Åtgärd | Microsoft.Network/securegateways/networkRuleCollections/delete | Tar bort en regelsamling för nätverket för en säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/networkRuleCollections/read | Hämta en regelsamling för nätverket för en given säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/networkRuleCollections/write | Skapar eller uppdaterar en regelsamling för nätverket för en säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/read | Få säker Gateway |
> | Åtgärd | Microsoft.Network/securegateways/write | Skapar eller uppdaterar en säker Gateway |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/delete | Tar bort en slutpunkt för för tjänsten |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/join/action | Ansluter till en slutpunkt för för tjänsten |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Ansluter till ett undernät till-slutpunkt-principer |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/read | Hämtar en tjänstbeskrivning Endpoint princip |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Tar bort en Principdefinition för Service-slutpunkt |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Hämtar en tjänsten Endpoint princip Definition beskrivning |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Skapar en Principdefinition för tjänsten Endpoint eller uppdaterar en befintlig Principdefinition för tjänsten slutpunkt |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/write | Skapar en slutpunkt för för tjänsten eller uppdaterar en befintlig Service Endpoint princip |
> | Åtgärd | Microsoft.Network/trafficManagerGeographicHierarchies/read | Hämtar Traffic Manager geografiska hierarkin som innehåller områden som kan användas med geografisk trafikroutningsmetod |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Tar bort en Azure-slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager stoppas dirigera trafiken till den Azure slutpunkt har tagits bort. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Hämtar en Azure-slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den Azure-slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Lägg till en ny Azure-slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig Azure-slutpunkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/delete | Ta bort Traffic Manager-profilen. Alla inställningar som är kopplade till trafikhanterarprofilen försvinner och profilen kan inte längre användas för att dirigera trafik. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Tar bort en extern slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager stoppas dirigera trafiken till den externa slutpunkt har tagits bort. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Hämtar en extern slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den externa slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Lägg till en ny extern slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig extern slutpunkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Hämtar Traffic Manager termisk karta för den angivna Traffic Manager-profil som innehåller antalet och fördröjning frågedata av plats- och käll-IP. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Tar bort en slutpunkt för kapslade från en befintlig Traffic Manager-profil. Traffic Manager stoppas dirigera trafiken till borttagna kapslade slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Hämtar en kapslad slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den kapslade slutpunkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Lägg till en ny kapslade slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig kapslade slutpunkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar inställningar för Traffic Manager-diagnostik |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för Traffic Manager åtgärden kompletteras av resursprovidern för insights. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för Traffic Manager |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Traffic Manager. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/read | Hämta profilkonfigurationen för Traffic Manager. Detta inkluderar DNS-inställningar, trafikdirigeringsinställningar, slutpunktsövervakningsinställningar och listan över slutpunkter som dirigeras av den här trafikhanterarprofilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/write | Skapa en trafikhanterarprofil eller ändra konfigurationen för en befintlig Traffic Manager-profil.<br>Detta inkluderar att aktivera eller inaktivera en profil och ändra DNS-inställningar, trafikdirigeringsinställningar och slutpunktsövervakningsinställningar.<br>Slutpunkter som dirigeras av trafikhanterarprofilen kan läggas till, tas bort, aktiveras eller inaktiveras. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Tar bort en prenumerationsnivån nyckel som används för realtid användaren mått samling. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/read | Hämtar-prenumerationsnivån-nyckel som används för realtid användaren mått samling. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/write | Skapar en ny prenumeration på objektnivå nyckel som ska användas för realtid användaren mått samling. |
> | Åtgärd | Microsoft.Network/unregister/action | Avregistrerar prenumerationen |
> | Åtgärd | Microsoft.Network/virtualHubs/delete | Tar bort en virtuell-hubb |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Tar bort en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Hämta en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Skapa eller uppdatera en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/read | Hämta en virtuell-hubb |
> | Åtgärd | Microsoft.Network/virtualHubs/write | Skapa eller uppdatera en virtuell-hubb |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/Connections/Read | Hämta VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/delete | Tar bort en virtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generera VpnClient paketet för virtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/generatevpnprofile/Action | Generera VpnProfile paketet för VirtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/getadvertisedroutes/Action | Hämtar virtualNetworkGateway annonserade vägar |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/getbgppeerstatus/Action | Hämtar virtualNetworkGateway bgp peer-status |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/getlearnedroutes/Action | Hämtar virtualnetworkgateway inlärda vägar |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Hämta Vpnclient Ipsec parametrar för VirtualNetworkGateway P2S-klienten. |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/getvpnprofilepackageurl/Action | Hämtar URL för en profil för förgenererade VPN-klientpaketet |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar diagnostikinställningar för virtuellt nätverk Gateway |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för virtuell nätverksgateway åtgärden kompletteras av resursprovidern för insights. |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för virtuell nätverksgateway |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för virtuell nätverksgateway |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/read | Hämtar en VirtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/Reset/Action | Återställer en virtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/setvpnclientipsecparameters/Action | Ange Vpnclient Ipsec-parametrarna för VirtualNetworkGateway P2S-klienten. |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Visar VPN-enheter som stöds |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/write | Skapar eller uppdaterar en VirtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Kontrollera om Ip-adress är tillgänglig på det angivna virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/customViews/get/action | Hämta en anpassad vy innehåll för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/customViews/read | Hämta definition av en anpassad vy av virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/delete | Tar bort ett virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/peer/action | Peers ett virtuellt nätverk med ett annat virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningar för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write | Skapa eller uppdatera diagnostikinställningar för det virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read | Hämta Loggdefinitioner för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/read | Hämta definitionen av virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete | Tar bort ett virtuellt nätverk peering-proxy |
> | Åtgärd | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Hämtar ett virtuellt nätverk peering proxydefinitioner |
> | Åtgärd | Microsoft.Network/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Skapar en peering proxy för virtuellt nätverk eller uppdaterar en befintlig peering proxy för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/delete | Tar bort ett undernät för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till resursen, till exempel lagringskonto eller SQL-databas till ett undernät. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/read | Hämtar en definition av undernät för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/delete | Tar bort en resurs navigeringslänk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/read | Hämta definitionen för resurslänken för navigering |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/resourceNavigationLinks/write | Skapar en Resurslänk navigering eller uppdaterar en befintlig resurs navigeringslänk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/delete | Tar bort en Service Association länk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/details/read | Hämtar en Definition av tjänsten Association länken detaljer |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/read | Hämtar en definition av tjänsten Association länk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/validate/action | Verifierar en Association Service-länk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/serviceAssociationLinks/write | Skapar en Association Service-länk eller uppdaterar en befintlig Service Association länk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Hämtar hänvisningar till alla virtuella datorer i ett undernät för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/write | Skapar ett undernät för virtuellt nätverk eller uppdaterar ett befintligt undernät för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/delete | Tar bort en konsument taggade trafik |
> | Åtgärd | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/read | Hämta märkta trafik konsument-definition |
> | Åtgärd | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/validate/action | Verifierar taggade trafik konsumenter |
> | Åtgärd | Microsoft.Network/virtualNetworks/taggedTrafficConsumers/write | Skapar taggade trafik konsumenter eller uppdaterar en befintlig märkta trafik konsumenten |
> | Åtgärd | Microsoft.Network/virtualNetworks/usages/read | Hämta IP-användningsområden för varje undernät i det virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualMachines/read | Hämtar hänvisningar till alla virtuella datorer i ett virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Tar bort ett virtuellt nätverk som peering |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Hämtar en definition av peering virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Skapar en peering virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk peering |
> | Åtgärd | Microsoft.Network/virtualNetworks/write | Skapar ett virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/delete | Ta bort tryck för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/join/action | Ansluter till ett virtuellt nätverk tryck |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/read | Hämta tryck för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/write | Skapa eller uppdatera tryck för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualWans/delete | Tar bort en virtuell Wan |
> | Åtgärd | Microsoft.Network/virtualWans/read | Hämta en virtuell Wan |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubProxies/delete | Tar bort en virtuell Hub-proxy |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubProxies/read | Hämtar en definition av virtuella hubb proxy |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubProxies/write | Skapar en virtuell hubb proxy eller uppdaterar en virtuell Hub-proxy |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubs/read | Hämtar alla virtuella hubbar som refererar till virtuella Wan. |
> | Åtgärd | Microsoft.Network/virtualwans/vpnconfiguration/action | Hämtar en Vpn-konfiguration |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSiteProxies/delete | Tar bort platsen för Vpn-proxy |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSiteProxies/read | Hämtar en definition av platsen för Vpn-proxy |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSiteProxies/write | Skapar en platsen för Vpn-proxy eller uppdaterar en platsen för Vpn-proxy |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSites/read | Hämtar alla VPN-platser som refererar till virtuella Wan. |
> | Åtgärd | Microsoft.Network/virtualWans/write | Skapa eller uppdatera virtuella Wan |
> | Åtgärd | Microsoft.Network/vpnGateways/read | Hämtar en VpnGateway. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/read | Hämtar en VpnConnection. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/write | Placerar en VpnConnection. |
> | Åtgärd | Microsoft.Network/vpnGateways/write | Placerar en VpnGateway. |
> | Åtgärd | Microsoft.Network/vpnsites/delete | Tar bort en platsen för Vpn-resurs. |
> | Åtgärd | Microsoft.Network/vpnsites/read | Hämtar en platsen för Vpn-resurs. |
> | Åtgärd | Microsoft.Network/vpnsites/write | Skapar eller uppdaterar en platsen för Vpn-resurs. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Kontrollerar huruvida ett visst namnområdesresursnamn finns i NotificationHub-tjänsten. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Auktoriseringsregel för namnområde: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Kontrollerar huruvida ett visst NotificationHub-namn finns i ett namnområde. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Hämta listan över auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Ta bort auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Hämta anslutningssträngen till meddelandehubben |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Hämta listan över auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Auktoriseringsregel för Notification Hub: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Skapa auktoriseringsregler för Notification Hub och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Skicka ett push-testmeddelande. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Ta bort Notification Hub-resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Hämta lista över Namespace mått resurs beskrivningar |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Hämta alla Notification Hub PNS-autentiseringsuppgifter. Detta omfattar WNS, MPNS, APN, GCM och Baidu autentiseringsuppgifter |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Hämta listan över resursbeskrivningar av Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Skapa en Meddelandehubb och uppdatera dess egenskaper. Egenskaperna är främst PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL-värde |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/write | Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/register/action | Registrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationalInsights/linkTargets/read | Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Använd en kund-id som returneras av den här åtgärden i kundens id-egenskapen för skapa arbetsytan igen för att länka den här Azure-prenumeration till en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/register/action | Registrera en prenumeration på en resursleverantör. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sökningen med nya motorn. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Hämta schema för sökning V2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/action | Sökningen med nya motorn. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Hämta schema för sökning V2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Ta bort konfigurationsomfång |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Hämta konfigurationsomfång |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Ange konfigurationsomfång |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/delete | Ta bort datakällor i en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/read | Hämta datakällor i en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/write | Skapa eller uppdatera datakällor i en arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/delete | Tar bort en arbetsyta. Om arbetsytan var kopplad till en befintlig arbetsyta vid skapandet bort arbetsytan den kopplats till inte. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Genererar registreringscertifikat för arbetsytan. Det här certifikatet används för att ansluta Microsoft System Center Operation Manager till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Inaktiverar en informationspaketet för en viss arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Gör en informationspaketet för en viss arbetsyta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Visar en lista över alla intelligence packs som är synliga för en viss arbetsyta och visar också om Packet är aktiverat eller inaktiverat för den arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Ta bort länkade tjänster enligt angivna arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/read | Hämta länkade tjänster enligt angivna arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/write | Skapa eller uppdatera länkade tjänster enligt angivna arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/listKeys/action | Hämtar lista nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/listKeys/read | Hämtar lista nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/managementGroups/read | Hämtar namn och metadata för System Center Operations Manager-hanteringsgrupper som är ansluten till den här arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Hämta mått definitioner under arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Ta bort användarens meddelandeinställningarna för arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Hämta användarens meddelandeinställningarna för arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Ange användarens meddelandeinställningarna för arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/purge/action | Ta bort angivna data från arbetsytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbetsyta |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Tar bort en sparad sökning |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/read | Hämtar en sparad sökning |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/write | Skapar en sparad sökning |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/schema/read | Hämtar Sök schemat för arbetsytan.  Sök-schemat innehåller exponerade fält och deras typer. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Tar bort en konfiguration för lagring. Microsoft åtgärdsinformation stoppas från att läsa data från lagringskontot. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Hämtar en konfiguration för lagring. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Skapar en ny konfiguration för lagring. Dessa konfigurationer för att hämta data från en plats i ett befintligt lagringskonto. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/usages/read | Hämtar användningsdata för en arbetsyta inklusive mängden data som läses av arbetsytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/write | Skapar en ny arbetsyta eller länkar till en befintlig arbetsyta genom att tillhandahålla kund-id från befintlig arbetsyta. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/delete | Ta bort befintliga Management-kopplingen |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/read | Hämta befintliga för att associera Management |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/write | Skapa en ny Management-Association |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/delete | Ta bort den befintliga Management Configuratin |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/read | Hämta befintliga Management-konfiguration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/write | Skapa en ny Management-konfiguration |
> | Åtgärd | Microsoft.OperationsManagement/register/action | Registrera en prenumeration på en resursleverantör. |
> | Åtgärd | Microsoft.OperationsManagement/solutions/delete | Ta bort befintliga OMS-lösning |
> | Åtgärd | Microsoft.OperationsManagement/solutions/read | Hämta avslutas OMS-lösning |
> | Åtgärd | Microsoft.OperationsManagement/solutions/write | Skapa ny OMS-lösning |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/action | Fråga efter information om Principhändelser. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/action | Fråga efter information om principen tillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/action | Fråga översiktlig information om principen senaste tillstånd. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Portal/dashboards/delete | Tar bort instrumentpanelen från prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/read | Läser in instrumentpanelerna för prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/write | Lägg till eller ändra en instrumentpanel för en prenumeration. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/checkNameAvailability/action | Kontrollerar att namnet angivna Power BI-dedikerade kapacitet är giltigt och inte i användning. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/delete | Tar bort Powerbi reserverad kapacitet. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för Power BI dedikerade kapacitet |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Power BI dedikerade kapacitet. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/read | Hämtar information för den angivna Power BI dedikerade kapacitet. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/resume/action | Återupptar kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/skus/read | Hämta information om tillgängliga SKU för kapacitet |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/suspend/action | Pausar kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/write | Skapar eller uppdaterar den angivna Power BI dedikerade kapacitet. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/operationresults/read | Hämtar information om resultatet för den angivna åtgärden. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Hämtar information om statusen för den angivna åtgärden. |
> | Åtgärd | Microsoft.PowerBIDedicated/operations/read | Hämtar information av åtgärder |
> | Åtgärd | Microsoft.PowerBIDedicated/register/action | Registrerar Power BI dedikerade resursprovidern. |
> | Åtgärd | Microsoft.PowerBIDedicated/skus/read | Hämtar information av SKU: er |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp är intern åtgärd som används av tjänsten |
> | Åtgärd | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Åtgärd | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrollera Status för säkerhetskopiering för Recovery Services-valv |
> | Åtgärd | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validera funktioner |
> | Åtgärd | Microsoft.RecoveryServices/operations/read | Åtgärd returnerar en lista över åtgärder för en resurs-Provider |
> | Åtgärd | Microsoft.RecoveryServices/register/action | Registrerar prenumerationen för angivna Resource Provider |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returnerar konfigurationen för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/write | Uppdateringar konfigurationen för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhetskopiering skydd avsett |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla behållare som kan skyddas |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Tar bort registrerade behållaren |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör en förfrågan för arbetsbelastningar i en behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Säkerhetskopierar ett skyddat objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Tar bort skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för utförd åtgärd på skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar information om objekt för det skyddade objektet |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Etablera omedelbar återställning för skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återskapa återställningspunkter för skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla omedelbar återställning för skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa en säkerhetskopiera skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar listan över behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Avbryts |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultat från jobbåtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobbobjekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobben |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Returnerar resultatet av jobbet exportåtgärden. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Returnerar metadata för hantering av säkerhetskopiering för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returnerar resultat från säkerhetskopiering för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar Säkerhetskopieringsåtgärden Status för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Ta bort en Skyddsprincip för |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämtar resultat från principåtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Hämta Status för princip-åtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla Protection-principer |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Skapar Protection-principen |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returnerar lista över alla objekt som ska skyddas. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Returnerar säkerhet PIN-kod Information för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returnerar lagringskonfigurationen för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Konfiguration för lagring av uppdateringar för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/certificates/write | Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/delete | Ta bort valvet åtgärden tar bort angivna Azure-resurs av typen 'valvet' |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringar för Recovery services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Löser du aviseringen. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Hämtar meddelandekonfigurationen Recovery services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfigurerar e-postaviseringar till Recovery services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/read | Azure Backup-diagnostik |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/diagnosticSettings/write | Azure Backup-diagnostik |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/logDefinitions/read | Azure Backup-loggar |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/providers/Microsoft.Insights/metricDefinitions/read | Azure Backup mått |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Åtgärden för att avregistrera behållaren kan användas för att avregistrera en behållare. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Registrera tjänstbehållaren åtgärden kan användas för att registrera en behållare med återställningstjänsten. |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läsa alla aviseringsinställningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Skapa eller uppdatera alla aviseringsinställningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läsa alla händelser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar infrastrukturens enhetlighet |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Ta bort alla Infrastrukturresurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Distribuera Processerveravbildning |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läsa alla Infrastrukturresurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skapa en ny koppling Gateway |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Ta bort Fabric |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läsa alla nätverk |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Ta bort alla nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läsa alla nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Skapa eller uppdatera alla nätverksmappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Identifiera skyddsobjekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läsa skydd behållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Ta bort Skyddsbehållaren |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läsa alla objekt som kan skyddas |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Tillämpa återställningspunkt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Ta bort alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Redundansåtgärd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läsa alla återställningspunkter för replikeringen |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Ta bort skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Skapa nytt skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Redundanstestningen |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdatera Mobilitetstjänsten |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Skapa eller uppdatera alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Ta bort alla mappningar för behållaren skydd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar för behållaren skydd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Ta bort skyddsbehållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Skapa eller uppdatera alla mappningar för behållaren skydd |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Växeln-Skyddsbehållaren |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Skapa eller uppdatera Protection behållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Ta bort alla Recovery Services-Providers |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läsa alla Recovery Services-Providers |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera providern |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Ta bort Recovery Services-Provider |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Skapa eller uppdatera alla Recovery Services-Providers |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läsa alla Lagringsklassificeringar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Ta bort alla mappningar för klassificering av lagring |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läsa alla mappningar för klassificering av lagring |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Skapa eller uppdatera alla mappningar för klassificering av lagring |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ta bort alla jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läsa alla jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Skapa eller uppdatera jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Skapa eller uppdatera alla Infrastrukturresurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/cancel/action | Avbryta jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/read | Läsa alla jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Starta om jobbet |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Återuppta jobbet |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Ta bort alla principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läsa alla principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Skapa eller uppdatera alla principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/delete | Ta bort alla Återställningsplaner |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Redundansåtgärd återställningsplan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Planerad redundans återställningsplan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läsa alla Återställningsplaner |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Skapa nytt återställningsplan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Testa redundans återställningsplan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Testa redundans Rensa återställningsplan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan för växling vid fel |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Skapa eller uppdatera alla Återställningsplaner |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Returnerar tokeninformation för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/vaults/usages/read | Läsa alla valvet användningsområden |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användningsinformation om Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Token valvet igen kan användas för att hämta valv Token för valvet nivån backend-åtgärder. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/write | Med skapa valv så skapas en Azure-resurs av typen valv |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Relay/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.Relay/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/action | Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/delete | Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/write | Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Åtgärden Uppdatera HybridConnection. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Åtgärden ta bort HybridConnection auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Hämta anslutningssträngen till HybridConnection |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Hämta en lista över HybridConnection auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Skapa auktoriseringsregler för HybridConnection och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/Delete | Åtgärden ta bort HybridConnection resurs |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/read | Hämta lista över HybridConnection resurs beskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/write | Skapa eller uppdatera HybridConnection egenskaper. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/read | Hämtar meddelanden planera för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare...<br>Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/write | Uppdaterar Messaging planera för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare...<br>Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resurs beskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Åtgärd | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Åtgärden Uppdatera WcfRelay. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Åtgärden ta bort WcfRelay auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Hämta anslutningssträngen till WcfRelay |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Hämta en lista över WcfRelay auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Skapa auktoriseringsregler för WcfRelay och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/Delete | Åtgärden ta bort WcfRelay resurs |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/read | Hämta lista över WcfRelay resurs beskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/write | Skapa eller uppdatera WcfRelay egenskaper. |
> | Åtgärd | Microsoft.Relay/namespaces/write | Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras. |
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
> | Åtgärd | Microsoft.ResourceHealth/register/action | Registrerar prenumerationen för Microsoft ResourceHealth |

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
> | Åtgärd | Microsoft.Search/checkNameAvailability/action | Kontrollerar tillgängligheten för tjänstnamnet. |
> | Åtgärd | Microsoft.Search/operations/read | Visar en lista över alla tillgängliga åtgärder av Microsoft.Search-providern. |
> | Åtgärd | Microsoft.Search/register/action | Registrerar prenumerationen för resursen sökleverantör och kan skapa search-tjänster. |
> | Åtgärd | Microsoft.Search/searchServices/createQueryKey/action | Skapar fråga för nyckeln. |
> | Åtgärd | Microsoft.Search/searchServices/delete | Tar bort söktjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/deleteQueryKey/delete | Tar bort Frågenyckeln. |
> | Åtgärd | Microsoft.Search/searchServices/diagnosticSettings/read | Hämtar diganostic inställningen skrivskyddade för resursen |
> | Åtgärd | Microsoft.Search/searchServices/diagnosticSettings/write | Skapar eller uppdaterar inställningen diganostic för resursen |
> | Åtgärd | Microsoft.Search/searchServices/listAdminKeys/action | Läser admin-nycklar. |
> | Åtgärd | Microsoft.Search/searchServices/listQueryKeys/read | Returnerar listan över frågan API-nycklar för den angivna Azure Search-tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/logDefinitions/read | Hämtar de tillgängliga loggarna för söktjänsten |
> | Åtgärd | Microsoft.Search/searchServices/metricDefinitions/read | Hämtar tillgängliga mått för söktjänsten |
> | Åtgärd | Microsoft.Search/searchServices/read | Läser search-tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/regenerateAdminKey/action | Återskapar admin-nyckel. |
> | Åtgärd | Microsoft.Search/searchServices/start/action | Startar söktjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/stop/action | Stoppar tjänsten sökning. |
> | Åtgärd | Microsoft.Search/searchServices/write | Skapar eller uppdaterar search-tjänsten. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Security/alerts/read | Hämtar alla tillgängliga säkerhetsaviseringar |
> | Åtgärd | Microsoft.Security/applicationWhitelistings/read | Hämtar programmet whitelistings |
> | Åtgärd | Microsoft.Security/applicationWhitelistings/write | Skapar en ny vitlistning av program eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/complianceResults/read | Hämtar kompatibilitetsresultaten för resursen |
> | Åtgärd | Microsoft.Security/locations/alerts/activate/action | Aktivera en säkerhetsvarning |
> | Åtgärd | Microsoft.Security/locations/alerts/dismiss/action | Avvisa en säkerhetsavisering |
> | Åtgärd | Microsoft.Security/locations/alerts/read | Hämtar alla tillgängliga säkerhetsaviseringar |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initierar en åtkomstprincip för just-in-time-nätverk |
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
> | Åtgärd | Microsoft.Security/pricings/delete | Tar bort prisnivå inställningar för scope |
> | Åtgärd | Microsoft.Security/pricings/read | Hämtar prisnivå inställningarna för scope |
> | Åtgärd | Microsoft.Security/pricings/write | Uppdaterar inställningarna för omfånget som prisnivå |
> | Åtgärd | Microsoft.Security/register/action | Registrerar prenumerationen för Azure Security Center |
> | Åtgärd | Microsoft.Security/securityContacts/delete | Tar bort kontakten säkerhet |
> | Åtgärd | Microsoft.Security/securityContacts/read | Hämtar säkerhet kontakt |
> | Åtgärd | Microsoft.Security/securityContacts/write | Uppdaterar säkerhet kontakt |
> | Åtgärd | Microsoft.Security/securitySolutions/delete | Tar bort en säkerhetslösning |
> | Åtgärd | Microsoft.Security/securitySolutions/read | Hämtar säkerhetslösningar |
> | Åtgärd | Microsoft.Security/securitySolutions/write | Skapar en ny lösning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/securitySolutionsReferenceData/read | Hämtar säkerhetslösningar referensdata |
> | Åtgärd | Microsoft.Security/securityStatuses/read | Hämtar säkerheten hälsa status för Azure-resurser |
> | Åtgärd | Microsoft.Security/securityStatusesSummaries/read | Hämtar säkerheten statusar sammanfattningar för scope |
> | Åtgärd | Microsoft.Security/tasks/read | Hämtar alla tillgängliga säkerhetsrekommendationer |
> | Åtgärd | Microsoft.Security/webApplicationFirewalls/delete | Tar bort en brandvägg för webbaserade program |
> | Åtgärd | Microsoft.Security/webApplicationFirewalls/read | Hämtar webben programmet brandväggar |
> | Åtgärd | Microsoft.Security/webApplicationFirewalls/write | Skapar en ny Brandvägg för webbaserade program eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/workspaceSettings/connect/action | Ändra arbetsytan inställningar för återanslutning |
> | Åtgärd | Microsoft.Security/workspaceSettings/delete | Tar bort arbetsytan inställningar |
> | Åtgärd | Microsoft.Security/workspaceSettings/read | Hämtar inställningar för arbetsyta |
> | Åtgärd | Microsoft.Security/workspaceSettings/write | Arbetsytan inställningar för uppdateringar |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/delete | Om du tar bort en diagnostikinställningen |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/read | Läser en diagnostikinställningen |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/write | Skriva ett diagnostikinställningen |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Läser en diagnostikinställningen kategorier |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ServiceBus/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.ServiceBus/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/action | Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort.  |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/write | Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/Delete | Ta bort namnområdesresurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Raderar Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Hämtar Event Grid-filtret som är kopplat till namnområdet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Skapar eller uppdaterar Event Grid-filtret som är kopplat till namnutrymmet. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventhubs/read | Hämta lista över EventHub resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hämtar meddelanden planera för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare...<br>Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/write | Uppdaterar Messaging planera för ett namnområde.<br>Detta API är inaktuell.<br>Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare...<br>Den här åtgärden stöds inte på API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrate/action | Migreringsåtgärd för namnområde |
> | Åtgärd | Microsoft.ServiceBus/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta lista över Namespace diagnostikinställningar resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta lista över Namespace diagnostikinställningar resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta en lista över Namespace loggar resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Namespace mått resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Åtgärden uppdatera kön. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Åtgärden ta bort kön auktoriseringsregler |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Hämta anslutningssträngen till kön |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Hämta en lista över auktoriseringsregler för kön |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Skapa auktoriseringsregler för kön och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/Delete | Åtgärden ta bort kön resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/read | Hämta lista över kön resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/write | Skapa eller uppdatera köegenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Åtgärden Uppdatera avsnittet. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Åtgärden ta bort avsnittet auktoriseringsregler |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Hämta anslutningssträngen till avsnittet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Hämta en lista över avsnittet auktoriseringsregler |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Skapa auktoriseringsregler för avsnittet och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/Delete | Åtgärden ta bort avsnittet resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/read | Hämta lista över avsnittet resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/Delete | Åtgärden ta bort TopicSubscription resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/read | Hämta lista över TopicSubscription resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/Delete | Åtgärden ta bort regeln resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/read | Hämta lista över regeln resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/rules/write | Skapa eller uppdatera regelegenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/subscriptions/write | Skapa eller uppdatera TopicSubscription egenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/write | Skapa eller uppdatera avsnittet Egenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/write | Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.ServiceBus/register/action | Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser |
> | Åtgärd | Microsoft.ServiceBus/sku/read | Hämta lista över Sku resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/sku/regions/read | Hämta lista över SkuRegions resurs beskrivningar |
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
> | Åtgärd | Microsoft.SignalRService/checknameavailability/action | Kontrollerar om ett namn är tillgängliga för användning med en ny SignalR-tjänst |
> | Åtgärd | Microsoft.SignalRService/register/action | Registrerar 'Microsoft.SignalRService' resursprovidern med en prenumeration |
> | Åtgärd | Microsoft.SignalRService/SignalR/delete | Ta bort hela SignalR |
> | Åtgärd | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.SignalRService/SignalR/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för SignalR |
> | Åtgärd | Microsoft.SignalRService/SignalR/read | Visa SignalR inställningar och konfigurationer i hanteringsportalen eller via API: T |
> | Åtgärd | Microsoft.SignalRService/SignalR/write | Ändra SignalR inställningar och konfigurationer i hanteringsportalen eller via API: T |
> | Åtgärd | Microsoft.SignalRService/unregister/action | Avregistrerar 'Microsoft.SignalRService' resursprovidern med en prenumeration |

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
> | Åtgärd | Microsoft.Solutions/locations/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Åtgärd | Microsoft.Solutions/register/action | Registrera dig för lösningar. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Sql/checkNameAvailability/action | Kontrollera den angivna servern namn är tillgängligt för att etablera över hela världen för en viss prenumeration. |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Hämta resultatet av server-blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/capabilities/read | Hämtar funktionerna för den här prenumerationen i en viss plats |
> | Åtgärd | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/databaseOperationResults/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Hämtar pågående åtgärder på servern som tagits bort |
> | Åtgärd | Microsoft.Sql/locations/deletedServerOperationResults/read | Hämtar pågående åtgärder på servern som tagits bort |
> | Åtgärd | Microsoft.Sql/locations/deletedServers/read | Returnera listan över borttagna servrar eller hämtar egenskaperna för den angivna servern som tagits bort. |
> | Åtgärd | Microsoft.Sql/locations/deletedServers/recover/action | Återställa en borttagen server |
> | Åtgärd | Microsoft.Sql/locations/deleteVirtualNetworkOrSubnets/action | Tar bort regler för virtuella nätverk som är kopplat till ett virtuellt nätverk eller undernät |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Hämtar azure async-åtgärden för en asynkron åtgärd elastisk pool |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolOperationResults/read | Hämtar resultatet av en elastisk pool. |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Hämta resultatet av den utökade server blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/delete | Tar bort en befintlig instans redundansväxlingsgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Kör planerad redundans i en befintlig instans redundansväxlingsgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kör framtvingad växling vid fel i en befintlig instans redundansväxlingsgrupp. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/read | Returnerar listan över instans redundans grupper eller hämtar egenskaperna för angivna instansen redundans-gruppen. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/write | Skapar en instansgrupp för växling vid fel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för angivna instansen redundans-gruppen. |
> | Åtgärd | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Slutför hanterade databasåterställning |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hämtar pågående åtgärder på hanterade databasen transparent datakryptering |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Hämtar pågående åtgärder på hanterade databasen transparent datakryptering |
> | Åtgärd | Microsoft.Sql/locations/networkInterfaceAzureAsyncOperation/read | Returnerar information om ett visst nätverksgränssnitt Azure asynkron åtgärd |
> | Åtgärd | Microsoft.Sql/locations/networkInterfaceOperationResults/read | Returnerar information om angivna gränssnittet nätverksåtgärden |
> | Åtgärd | Microsoft.Sql/locations/read | Hämtar tillgängliga platser för en viss prenumeration |
> | Åtgärd | Microsoft.Sql/locations/syncAgentOperationResults/read | Hämta resultatet av synkroniseringsåtgärd agent resurs |
> | Åtgärd | Microsoft.Sql/locations/syncDatabaseIds/read | Hämta ID: n för sync-databasen för en viss region och en prenumeration |
> | Åtgärd | Microsoft.Sql/locations/syncGroupOperationResults/read | Hämta resultatet av åtgärden för synkronisering av resursen |
> | Åtgärd | Microsoft.Sql/locations/syncMemberOperationResults/read | Hämta resultatet av synkroniseringsåtgärd medlem resurs |
> | Åtgärd | Microsoft.Sql/locations/usages/read | Hämtar en samling av användningsstatistik för den här prenumerationen på en plats |
> | Åtgärd | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Returnerar information om de angivna virtuella nätverk reglerna azure asynkron åtgärd  |
> | Åtgärd | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Returnerar information om den angivna virtuella nätverket regler åtgärden  |
> | Åtgärd | Microsoft.Sql/managedInstances/administrators/delete | Tar bort en befintlig administratör för hanterade instansen. |
> | Åtgärd | Microsoft.Sql/managedInstances/administrators/read | Hämtar en lista över hanterade instans administratörer. |
> | Åtgärd | Microsoft.Sql/managedInstances/administrators/write | Skapar eller uppdaterar hanterade instans administratör med de angivna parametrarna. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/delete | Tar bort en befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/read | Hämtar befintliga hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/read | Hämta information om databasen threat detection principen konfigurerats på en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/write | Ändra databasen threat detection principen för en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityEvents/read | Hämtar säkerhetshändelser hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Hämta information om databasen Transparent datakryptering på en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Ändra databasen Transparent datakryptering för en viss hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/write | Skapar en ny databas eller uppdaterar en befintlig databas. |
> | Åtgärd | Microsoft.Sql/managedInstances/delete | Tar bort en befintlig hanterade instans. |
> | Åtgärd | Microsoft.Sql/managedInstances/metricDefinitions/read | Hämta måttdefinitionerna för hanterade instans |
> | Åtgärd | Microsoft.Sql/managedInstances/metrics/read | Hämta hanterade instans mätvärden |
> | Åtgärd | Microsoft.Sql/managedInstances/read | Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hantera instansen. |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Hämta information om hanterad server threat detection principen på en viss hanterad server |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Ändra hanterad server threat detection principen för en viss hanterad server |
> | Åtgärd | Microsoft.Sql/managedInstances/write | Skapar en hanterad instans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hantera instansen. |
> | Åtgärd | Microsoft.Sql/operations/read | Hämtar tillgängliga REST-åtgärder |
> | Åtgärd | Microsoft.Sql/register/action | Registrerar prenumerationen för resursprovidern Microsoft SQL-databasen och kan skapa Microsoft SQL-databaser. |
> | Åtgärd | Microsoft.Sql/servers/administratorOperationResults/read | Hämtar pågående åtgärder på server-administratörer |
> | Åtgärd | Microsoft.Sql/servers/administrators/delete | Ta bort serveradministratör |
> | Åtgärd | Microsoft.Sql/servers/administrators/read | Hämta serverinformation för administratör |
> | Åtgärd | Microsoft.Sql/servers/administrators/write | Skapa eller uppdatera serveradministratör |
> | Åtgärd | Microsoft.Sql/servers/advisors/read | Returnerar en lista över rådgivare som är tillgängliga för servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/read | Returnerar en lista över rekommenderade åtgärder för angiven klassificering för servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/write | Uppdateringar köra automatiskt-status för en advisor på servernivå. |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/read | Hämta information om server Standardtabell granskningsprincip som konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/write | Ändra standard server tabellen granskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/operationResults/read | Hämta resultatet av server-blob granskning princip Set-åtgärd |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/read | Hämta information om server blob granskningsprincipen konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/write | Ändra server-blob som granskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/read | Returnerar automatisk justeringsinställningar för servern |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/write | Uppdaterar inställningar för automatisk justering för servern och returnerar uppdaterade inställningarna |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Tar bort en befintlig arkivering säkerhetskopieringsvalvet. |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Den här åtgärden används för att hämta en säkerhetskopiering för långsiktig kvarhållning valvet. Returnerar information om valvet som registrerats för den här servern |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Den här åtgärden används för att registrera en säkerhetskopiering för långsiktig kvarhållning valvet till en server |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/delete | Tar bort en befintlig server kommunikationskanal. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/read | Returnera listan över kommunikationslänkar för en server. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/write | Skapa eller uppdatera en server kommunikationslänk. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/read | Returnera listan med principer för server-anslutning för en server. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/write | Skapa eller uppdatera en princip för servern. |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/read | Returnerar en lista över rådgivare som är tillgängliga för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Returnerar en lista över rekommenderade åtgärder för angivna advisor för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/write | Uppdateringen automatiskt-köra status för en advisor på databasnivå. |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/read | Hämta information om tabellen granskningsprincip konfigureras på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/write | Ändra tabellen granskningsprincipen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/read | Hämta information om blob-granskningsprincip som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/write | Ändra granskningsprincipen blob för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditRecords/read | Hämta databasen blob granskningsposter |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/read | Returnerar automatisk justeringsinställningar för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/write | Uppdaterar inställningar för automatisk justering för en databas och returnerar uppdaterade inställningarna |
> | Åtgärd | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Returnera listan över säkerhetskopieringsprinciper för arkivering av en angiven databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Skapa eller uppdatera en databas arkivering säkerhetskopieringsprincip. |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/read | Hämta information om principen konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/write | Ändra princip för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Returnera listan över databasdata maskering av principer. |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Ta bort datamaskning principregel för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Hämta information om datamaskning principregeln som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Ändra datamaskning principregel för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Ändra datamaskning princip för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Returnerar den distribuerade fråga steg informationen av data warehouse fråga för valda steget-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Returnerar data warehouse distribution läsa information för valda fråge-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Hämtar användaraktiviteter på en instans av SQL Data Warehouse som innehåller körs och avbrutna frågor |
> | Åtgärd | Microsoft.Sql/servers/databases/delete | Tar bort en befintlig databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/export/action | Exportera Azure SQL-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Hämta information om den utökade blobben granskningsprincip som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Ändra den utökade blobben granskningsprincipen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/read | Hämtar en uppsättning tillägg för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/write | Ändra filnamnstillägget för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Hämta geo principer för säkerhetskopiering för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Skapa eller uppdatera en databas geobackup princip |
> | Åtgärd | Microsoft.Sql/servers/databases/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/databases/metricDefinitions/read | Returnera typer av mätvärden som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/metrics/read | Returnera mätvärden för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/move/action | Byt namn på Azure SQL-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/operationResults/read | Hämtar status för en databasåtgärd. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/cancel/action | Avbryter Azure SQL Database väntande asynkron åtgärd inte har slutförts ännu. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/read | Returnera en lista över åtgärder som utförs på databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/pause/action | Pausa Azure SQL Datawarehouse-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mätvärden som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Returnerar frågan texter som är kopplade till de angivna parametrarna. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/read | Returnerar aktuella värden för Query Store inställningar för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/write | Uppdaterar inställningen för Frågearkivet för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/delete | Avsluta replikeringsrelationen tvång och potentiell dataförlust |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Växling vid fel efter synkronisering alla ändras från primärt gör den här databasen till replikering relationship\u0027s primära och göra fjärransluten primär till en sekundär |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Redundans omedelbart med potentiell dataförlust, vilket gör den här databasen till replikering relationship\u0027s primära och göra fjärransluten primär till en sekundär |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/read | Returnerar information om replikeringslänkar som har upprättats för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Avsluta replikeringsrelationen tvång eller efter synkronisering med partnern |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Uppdatera replikeringsläge för länken till synkron eller asynkron läge |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/action | Skapar en ny återställningspunkt |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/delete | Tar bort en återställningspunkt för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/read | Returnerar återställningspunkter för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/resume/action | Återuppta Azure SQL Datawarehouse-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/read | Hämta listan över scheman för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta listan över kolumner i en tabell |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort känslig etikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslig etikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslig etikett för en viss kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/read | Hämta listan över tabeller i en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Hämta listan över indexrekommendationer för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Tillämpa indexrekommendationen |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Hämta information om hot identifiering principen som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Ändra threat detection principen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/securityMetrics/read | Hämtar en samling av databasen säkerhet mått |
> | Åtgärd | Microsoft.Sql/servers/databases/sensitivityLabels/read | Lista känslighet etiketter för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Returnera förslag om att skala databasen uppåt eller nedåt baserat på frågan körning statistik för att förbättra prestanda eller minska kostnaden för |
> | Åtgärd | Microsoft.Sql/servers/databases/skus/read | Hämtar en samling av SKU: er som är tillgängliga för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Avbryt synkronisering Gruppsynkronisering |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/delete | Tar bort en befintlig grupp för synkronisering. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Returnera listan över sync hubb databasen scheman |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/logs/read | Returnera listan över gruppen synkroniseringsloggar |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/read | Returnera listan över grupper eller hämtar egenskaperna för den angivna sync-gruppen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Uppdatera databasschemat för sync-hubb |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Hämta resultatet av uppdateringsåtgärden sync hubb schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Tar bort en befintlig sync-medlem. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Returnera listan över sync medlemmar eller hämtar egenskaperna för den angivna sync-medlemmen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Uppdatera synkroniseringsschema för medlemmen |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Hämta resultatet av uppdateringsåtgärden sync medlem schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Returnera listan över sync medlem databasen scheman |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Skapar medlem synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-medlemmen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Utlösaren sync Gruppsynkronisering |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/write | Skapar en grupp för synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-gruppen. |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/queryText/action | Returnerar Transact-SQL-text för valda fråge-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/read | Returnerar samman körningsstatistik för den valda frågan i vald tidsperiod |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/statistics/read | Returnerar samman körningsstatistik för den valda frågan i vald tidsperiod |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Hämtar pågående åtgärder på transparent datakryptering |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Hämta status och information om transparent data kryptering säkerhetsfunktion för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Ändra krypteringsstatus för transparent data |
> | Åtgärd | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Uppgradera Azure SQL Datawarehouse-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/usages/read | Hämtar information för Azure SQL Database användningsområden |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Ta bort vulnerability assessment för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Hämta information om utvärdering säkerhetsrisker som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Ta bort vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Hämta vulnerability assessment regeln baslinje för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Ändra vulnerability assessment regeln baslinjen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Konvertera ett befintligt genomsökning resultat till ett format. Om det finns inget redan händer |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Köra säkerhetsproblem genomsökning för utvärdering av databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Returnerar listan över databasen vulnerability assessment genomsökning poster eller hämta genomsökning posten för den angivna sökning-ID. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Ändra vulnerability assessment för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Köra säkerhetsproblem genomsökning för utvärdering av databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Hämta resultatet av databasen vulnerability assessment genomsökning Kör åtgärden |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Hämta information om utvärdering säkerhetsrisker som konfigurerats på en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Ändra vulnerability assessment för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/write | Skapar en databas med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna databasen. |
> | Åtgärd | Microsoft.Sql/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Tar bort en befintlig konfigurationer med katastrofåterställning för en given server |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Redundans en DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Tvinga redundans en DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Hämtar en samling av disaster recovery-konfigurationer som innehåller den här servern |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Ändra serverkonfiguration disaster recovery |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/read | Returnerar en lista över elastisk pool beräknar redan har skapats för den här servern |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/write | Skapar ny elastiska pooluppskattningen lista över databaser som har angetts |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/read | Returnerar en lista över rådgivare som är tillgängliga för den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Returnerar en lista över rekommenderade åtgärder för angiven klassificering för den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/write | Uppdateringen automatiskt-köra status för en advisor på elastisk pool-nivå. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/databases/read | Hämtar en lista över databaser för en elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/delete | Ta bort befintlig elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Hämta aktiviteter och information om en viss elastisk databaspool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Hämta aktiviteter och information om en viss databas som ingår i en elastisk databaspool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Returnera typer av mätvärden som är tillgängliga för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metrics/read | Returnera mått för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Avbryter SQL Azure elastisk pool väntande asynkron åtgärd inte har slutförts ännu. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/read | Returnera en lista över åtgärder som utförs på den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mätvärden som är tillgängliga för elastiska databaspooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/read | Hämta information om elastisk pool på en viss server |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/skus/read | Hämtar en samling av SKU: er som är tillgängliga för en elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/write | Skapa en ny eller ändra egenskaper för befintlig elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/read | Returnerar en lista över server kryptering skydd eller hämtar egenskaperna för den angivna servern kryptering skydd. |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/write | Uppdatera egenskaperna för den angivna servern kryptering skydd. |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/read | Hämta information om den utökade server blob granskningsprincip som konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/write | Ändra den utökade servern blobbgranskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/delete | Tar bort en befintlig grupp för växling vid fel. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/failover/action | Kör planerad redundans i en befintlig grupp för växling vid fel. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kör framtvingad växling vid fel i en befintlig grupp för växling vid fel. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/read | Returnerar listan över redundans grupper eller hämtar egenskaperna för den angivna failover-gruppen. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/write | Skapar en redundansväxlingsgrupp med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna failover-gruppen. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/delete | Tar bort en befintlig brandväggsregel för servern. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/read | Returnera listan över serverbrandvägg regler eller hämtar egenskaperna för den angivna servern brandväggsregel. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/write | Skapar en brandväggsregel för servern med de angivna parametrarna uppdatera egenskaperna för den angivna regeln eller skriva över alla befintliga regler med nya regler för server-brandväggen. |
> | Åtgärd | Microsoft.Sql/servers/import/action | Skapa en ny databas på servern och distribuera schema och data från ett DacPac-paket |
> | Åtgärd | Microsoft.Sql/servers/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/keys/delete | Tar bort en befintlig servernyckel. |
> | Åtgärd | Microsoft.Sql/servers/keys/read | Returnera listan över server nycklar eller hämtar egenskaperna för den angivna servernyckeln. |
> | Åtgärd | Microsoft.Sql/servers/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servernyckeln. |
> | Åtgärd | Microsoft.Sql/servers/networkInterfaces/read | Returnerar egenskaperna för det angivna nätverksgränssnittet |
> | Åtgärd | Microsoft.Sql/servers/networkInterfaces/write | Skapar ett nätverksgränssnitt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för det angivna nätverksgränssnittet |
> | Åtgärd | Microsoft.Sql/servers/operationResults/read | Hämtar pågående åtgärder |
> | Åtgärd | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för servrar |
> | Åtgärd | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Hämta mätvärden för rekommenderade elastiska databaspoolerna för en viss server |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/read | Hämta rekommendation för elastiska databaspooler att minska kostnaderna eller förbättra prestandan, utifrån historica resursutnyttjande |
> | Åtgärd | Microsoft.Sql/servers/recoverableDatabases/read | Den här åtgärden används för katastrofåterställning av live-databasen för att återställa databasen till senast fungerande säkerhetskopieringspunkt. Den returnerar information om den senaste korrekta säkerhetskopian men doesn\u0027t verkligen återställa databasen. |
> | Åtgärd | Microsoft.Sql/servers/restorableDroppedDatabases/read | Hämta en lista över databaser som har släppts på en viss server som är fortfarande inom bevarandeprincip. |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Hämta resultaten av Skrivåtgärden server threat detection princip |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/read | Hämta information om server threat detection principen konfigurerats på en viss server |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/write | Ändra server threat detection principen för en given server |
> | Åtgärd | Microsoft.Sql/servers/serviceObjectives/read | Hämta lista över servicenivåmål (även kallat prestandanivåer) finns på en viss server |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/delete | Tar bort en befintlig sync-agent. |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/generateKey/action | Generera sync agent registrering nyckel |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/linkedDatabases/read | Returnera listan över synkronisera agent länkade databaser |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/read | Returnera listan över sync agenter eller hämtar egenskaperna för den angivna sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/write | Skapar en agent för synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/usages/read | Returnera DTU-kvot för server och den aktuella DTU consuption alla databaser på servern |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/delete | Tar bort en befintlig regel för virtuella nätverk |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/write | Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln. |
> | Åtgärd | Microsoft.Sql/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/unregister/action | Avregistrerar prenumerationen för resursprovidern Microsoft SQL-databasen och kan skapa Microsoft SQL-databaser. |
> | Åtgärd | Microsoft.Sql/virtualClusters/read | Returnera listan med virtuella kluster eller hämtar egenskaperna för det angivna virtuella klustret. |
> | Åtgärd | Microsoft.Sql/virtualClusters/write | Uppdaterar virtuellt kluster taggar. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Storage/checknameavailability/read | Kontrollerar att kontonamnet är giltigt och används. |
> | Åtgärd | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.Storage om att det virtuella nätverket eller undernätet tas bort |
> | Åtgärd | Microsoft.Storage/operations/read | Avsöker status för en asynkron åtgärd. |
> | Åtgärd | Microsoft.Storage/register/action | Registrerar prenumerationen för lagringsresursprovidern och gör det möjligt att skapa lagringskonton. |
> | Åtgärd | Microsoft.Storage/skus/read | Listar SKU:erna som stöds av Microsoft.Storage. |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Ta bort bevarande av juridiska skäl för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Ta bort oföränderlighetsprincipen för blobbehållaren |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Utöka oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Lås oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Hämta oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Placera oföränderlighetsprincip för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en behållare eller behållarlista |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ställ in bevarande av juridiska skäl för blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att placera eller låna blobbehållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Microsofts lagringsmåttdefinitioner. |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read | Returnerar egenskaper för Blob Service eller statistik |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/write | Returnerar resultatet av att placera Blob Service-egenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/delete | Tar bort ett befintligt lagringskonto. |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Microsofts lagringsmåttdefinitioner. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listAccountSas/action | Returnerar SAS-token för konto för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listServiceSas/action | Returnerar SAS-token för tjänst för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Microsofts lagringsmåttdefinitioner. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Microsofts lagringsmåttdefinitioner. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returnerar resultatet av att ta bort en kö |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller kölista. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returnerar resultatet av att skriva en kö |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Returnerar egenskaper för kötjänst eller statistik. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/write | Returnerar resultatet av att ställa in egenskaper för kötjänst |
> | Åtgärd | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Skapa/uppdatera diagnostikinställningar för lagringskontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read | Hämta diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningen för resursen. |
> | Åtgärd | Microsoft.Storage/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över Microsofts lagringsmåttdefinitioner. |
> | Åtgärd | Microsoft.Storage/storageAccounts/write | Skapar ett lagringskonto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna, eller lägger till anpassad domän för det angivna lagringskontot. |
> | Åtgärd | Microsoft.Storage/usages/read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | microsoft.storagesync/storageSyncServices/delete | Ta bort alla lagringstjänster för synkronisering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för lagring Sync Services |
> | Åtgärd | microsoft.storagesync/storageSyncServices/read | Läsa alla lagringstjänster för synkronisering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/delete | Ta bort alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/read | Läsa alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/write | Skapa eller uppdatera alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Ta bort alla moln-slutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Plats-api för säkerhetskopiering async-anrop |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Anropa den här åtgärden efter säkerhetskopiering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Anropa den här åtgärden efter återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Anropa den här åtgärden före säkerhetskopiering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Anropa den här åtgärden innan återställningen |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Läsa alla moln-slutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Återställa pulsslag |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Skapa eller uppdatera alla moln-slutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/delete | Ta bort alla synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/read | Läsa alla synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/delete | Ta bort alla Server-slutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/read | Läsa Server-slutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Anropa den här åtgärden för att återställa filer till en server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/write | Skapa eller uppdatera några Server-slutpunkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/write | Skapa eller uppdatera alla synkroniseringsgrupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/write | Skapa eller uppdatera alla lagringstjänster för synkronisering |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/delete | Tar bort Access Control-poster |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/read | Visar eller hämtar Access Control-poster |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/write | Skapa eller uppdatera Access Control-poster |
> | Åtgärd | Microsoft.StorSimple/managers/alerts/read | Visar eller hämtar aviseringar |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/delete | Tar bort en befintlig bandbreddsinställningar (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/read | Visa en lista med bandbreddsinställningarna (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/write | Skapar en ny eller uppdaterar bandbreddsinställningar (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/Managers/certificates/write | Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet. |
> | Åtgärd | Microsoft.StorSimple/managers/clearAlerts/action | Avmarkera alla aviseringar som är associerade med Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Lista molntjänster anordningen konfigurationer som stöds |
> | Åtgärd | Microsoft.StorSimple/managers/configureDevice/action | Konfigurerar en enhet |
> | Åtgärd | Microsoft.StorSimple/managers/delete | Tar bort enheten chefer |
> | Åtgärd | Microsoft.StorSimple/Managers/delete | Ta bort valvet åtgärden tar bort angivna Azure-resurs av typen 'valvet' |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/read | Visar eller hämtar de aviseringsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/write | Skapa eller uppdatera aviseringsinställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/backup/action | Ta en manuell säkerhetskopiering för att skapa en på-begäran säkerhetskopiering av alla volymer som skyddas av principen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/delete | Tar bort en befintlig säkerhetskopia principer (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/read | Lista säkerhetskopieringen principer (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Tar bort en befintlig scheman |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/read | Visa en lista över scheman |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/write | Skapar en ny eller uppdaterar scheman |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/write | Skapar en ny eller uppdaterar principerna för säkerhetskopiering (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/delete | Tar bort säkerhetskopian |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/elements/clone/action | Klona en resurs eller en volym med en säkerhetskopiering element. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/read | Visar eller hämtar säkerhetskopia |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/restore/action | Återställa alla volymer från en säkerhetskopia. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/delete | Tar bort schemat för säkerhetskopiering-grupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/read | Visar eller hämtar grupperna schema för säkerhetskopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/write | Skapa eller uppdatera grupperna schema för säkerhetskopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/delete | Tar bort Chap-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/read | Visar eller hämtar Chap-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/write | Skapa eller uppdatera Chap-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/deactivate/action | Inaktiverar en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/delete | Tar bort enheter |
> | Åtgärd | Microsoft.StorSimple/managers/devices/download/action | Download uppdateringar för en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/failover/action | Redundans av enheten. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/backup/action | Ta säkerhetskopior av en filserver. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/delete | Tar bort filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/metrics/read | Visar eller hämtar mätvärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Visar eller hämtar mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/read | Visar eller hämtar filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Tar bort resurserna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Visar eller hämtar mätvärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Visar eller hämtar mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Visar eller hämtar resurserna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/write | Skapa eller uppdatera resurser |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/write | Skapa eller uppdatera filservrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Ändra domänkontrollant energisparläge av maskinvara komponentgrupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Visa en lista över maskinvara komponentgrupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/install/action | Installera uppdateringar på en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/installUpdates/action | Installerar uppdateringar på enheter |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/backup/action | Ta säkerhetskopior av en iSCSI-server. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/delete | Tar bort iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/delete | Tar bort diskarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metrics/read | Visar eller hämtar mätvärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/metricsDefinitions/read | Visar eller hämtar mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/read | Hämtar diskarna eller visar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/write | Skapa eller uppdatera diskar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/metrics/read | Visar eller hämtar mätvärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Visar eller hämtar mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/read | Visar eller hämtar iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/write | Skapa eller uppdatera iSCSI-servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/jobs/cancel/action | Avbryta ett jobb som körs |
> | Åtgärd | Microsoft.StorSimple/managers/devices/jobs/read | Visar eller hämtar jobb |
> | Åtgärd | Microsoft.StorSimple/managers/devices/listFailoverSets/action | Lista för växling vid fel anger för en befintlig enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/listFailoverTargets/action | Lista redundansmål enheter |
> | Åtgärd | Microsoft.StorSimple/managers/devices/metrics/read | Visar eller hämtar mätvärden |
> | Åtgärd | Microsoft.StorSimple/managers/devices/metricsDefinitions/read | Visar eller hämtar mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/confirmMigration/action | Bekräftar en lyckad migrering och sparar den. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action | Hämta bekräftelsestatus för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Hämta status för beräkning av migreringsjobb. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action | Hämta status för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importera källa konfigurationer för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigration/action | Starta migrering med hjälp av konfigurationer för källa |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action | Starta ett jobb för att beräkna varaktigheten för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/read | Visar eller hämtar nätverksinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/write | Skapar en ny eller uppdaterar nätverksinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/publicEncryptionKey/action | Offentlig krypteringsnyckel för listan med Enhetshanteraren |
> | Åtgärd | Microsoft.StorSimple/managers/devices/publishSupportPackage/action | Publicera supportpaket för en enhet för felsökning av Microsoft-supporten. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/read | Hämtar enheterna eller visar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/scanForUpdates/action | Sök efter uppdateringar i en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/read | Visa en lista med säkerhetsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synkronisera certifikat för fjärrhantering för en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Uppdatera säkerhetsinställningar. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/write | Skapar en ny eller uppdaterar säkerhetsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Skicka avisering testmeddelandet till konfigurerade e-postmottagare. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/read | Visar eller hämtar inställningarna för tid |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/write | Skapar en ny eller uppdaterar tidsinställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/updateSummary/read | Hämtar uppdatering sammanfattningen eller visar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Tar bort en befintlig Volymbehållare (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/listEncryptionKeys/action | Lista krypteringsnycklar för Volymbehållare |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | Visa en lista över mätvärdena |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | Visa en lista med definitionerna som mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/read | Visa en lista över Volymbehållarna (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/rolloverEncryptionKey/action | Krypteringsnycklarna för förnyelse av Volymbehållare |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Tar bort en befintlig volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | Visa en lista över mätvärdena |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Visa en lista med definitionerna som mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | Visa en lista över volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Skapar en ny eller uppdaterar volymer |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/write | Skapar en ny eller uppdaterar Volymbehållare (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/write | Skapa eller uppdatera enheterna |
> | Åtgärd | Microsoft.StorSimple/managers/encryptionSettings/read | Visar eller hämtar krypteringsinställningar |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/delete | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Åtgärd | Microsoft.StorSimple/managers/getActivationKey/action | Hämta aktiveringsnyckeln för Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/getEncryptionKey/action | Hämta krypteringsnyckeln för Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/listActivationKey/action | Hämtar aktiveringsnyckeln av StorSimple Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/listPrivateEncryptionKey/action | Hämtar privata krypteringsnyckel för en StorSimple Device Manager. |
> | Åtgärd | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | Lista över offentliga krypteringsnycklar en StorSimple Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/metrics/read | Visar eller hämtar mätvärden |
> | Åtgärd | Microsoft.StorSimple/managers/metricsDefinitions/read | Visar eller hämtar mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Skapa en ny installation av molnet. |
> | Åtgärd | Microsoft.StorSimple/managers/read | Visar eller hämtar enheten chefer |
> | Åtgärd | Microsoft.StorSimple/Managers/read | Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet' |
> | Åtgärd | Microsoft.StorSimple/managers/regenarateRegistationCertificate/action | Återskapa registreringscertifikat för enhetshanterare. |
> | Åtgärd | Microsoft.StorSimple/managers/regenerateActivationKey/action | Återskapa aktiveringsnyckeln för Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Tar bort autentiseringsuppgifter för Lagringskonto |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/listAccessKey/action | Lista över åtkomstnycklar för lagring av autentiseringsuppgifter för konton |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/read | Visar eller hämtar autentiseringsuppgifter för Lagringskonto |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/write | Skapa eller uppdatera autentiseringsuppgifter för Lagringskonto |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/delete | Tar bort Storage-domäner |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/read | Visar eller hämtar lagring-domäner |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/write | Skapa eller uppdatera lagring-domäner |
> | Åtgärd | Microsoft.StorSimple/managers/write | Skapa eller uppdatera enheten chefer |
> | Åtgärd | Microsoft.StorSimple/Managers/write | Med skapa valv så skapas en Azure-resurs av typen valv |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.StreamAnalytics/locations/quotas/Read | Läs Stream Analytics prenumerationens kvoter |
> | Åtgärd | Microsoft.StreamAnalytics/operations/Read | Läs Stream Analytics-åtgärder |
> | Åtgärd | Microsoft.StreamAnalytics/Register/action | Registrera prenumerationen med Stream Analytics-Resursprovidern |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Delete | Ta bort Stream Analytics-jobbet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Ta bort Stream Analytics-jobbfunktionen |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Läs Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Hämta standarddefinitionen för en funktion i Stream Analytics-jobbet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Stream Analytics-jobbet funktionsnamnet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Skriva Stream Analytics-jobbfunktion |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Ta bort Stream Analytics-jobbet indata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-jobbet indata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Läs Stream Analytics-jobbet indata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Stream Analytics-jobbet Exempelindata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Testa Stream Analytics-jobbet indata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Skriva Stream Analytics-jobbet indata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Läs Måttdefinitionerna |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-jobbet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Ta bort Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Läs Åtgärdsresultat för Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Läs Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Testa Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Skriva Stream Analytics-Jobbutdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Läsa diagnostikinställningen. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Skriva diagnostikinställningen. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Read | Läs Stream Analytics-jobbet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Start/action | Starta Stream Analytics-jobbet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stoppa Stream Analytics-jobbet |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Ta bort Stream Analytics-jobbet omvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Läs Stream Analytics-jobbet omvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Skriva Stream Analytics-jobbet omvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Write | Skriva Stream Analytics-jobbet |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Subscription/CreateSubscription/action | Skapa en Azure-prenumeration |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/read | Hämta en Azure-Prenumerationsdefinitionen i en hanteringsgrupp. |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/write | Skapa en definition för Azure-prenumeration |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Support/register/action | Registrerar till supportresursprovidern |
> | Åtgärd | Microsoft.Support/supportTickets/read | Hämtar information om supportärendet (inklusive status, allvarlighetsgrad, kontaktinformation och kommunikation) eller hämtar listan över supportärenden för alla prenumerationer. |
> | Åtgärd | Microsoft.Support/supportTickets/write | Skapar eller uppdaterar ett supportärende. Du kan skapa ett supportärende för tekniska, fakturering, kvoter eller prenumerationshantering relaterade problem. Du kan uppdatera allvarlighetsgrad, kontaktinformation och kommunikation för befintliga supportärenden. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Tar bort åtkomstprincipen. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hämta egenskaperna för en åtkomstprincip. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Skapar en ny åtkomstprincip för en miljö eller uppdaterar en befintlig åtkomstprincip. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/delete | Tar bort miljön. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Tar bort händelsekällan. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/eventsources/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för eventsources |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/read | Hämta egenskaperna för en händelsekälla. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/write | Skapar en ny händelsekälla för en miljö eller uppdaterar en befintlig händelsekälla. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för miljöer |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/read | Hämta egenskaperna för en miljö. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Tar bort datamängden som referens. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Hämta egenskaperna för en datauppsättning för referens. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Skapar en ny referens datauppsättning för en miljö eller uppdaterar en befintlig datamängd referens. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/status/read | Hämta status för miljön, tillståndet för dess associerade åtgärder som ingång. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/write | Skapar en ny miljö eller uppdaterar en befintlig miljö. |
> | Åtgärd | Microsoft.TimeSeriesInsights/register/action | Registrerar prenumerationen för resursprovidern tid serien insikter och kan skapa tid serien insikter miljöer. |

## <a name="microsoftvisualstudio"></a>Microsoft.visualstudio

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.VisualStudio/Account/Delete | Ta bort konto |
> | Åtgärd | Microsoft.VisualStudio/Account/Read | Läskonto |
> | Åtgärd | Microsoft.VisualStudio/Account/Write | Ange konto |
> | Åtgärd | Microsoft.VisualStudio/Extension/Delete | Ta bort tillägg |
> | Åtgärd | Microsoft.VisualStudio/Extension/Read | Läs tillägg |
> | Åtgärd | Microsoft.VisualStudio/Extension/Write | Ange tillägg |
> | Åtgärd | Microsoft.VisualStudio/Project/Delete | Ta bort projektet |
> | Åtgärd | Microsoft.VisualStudio/Project/Read | Läs projekt |
> | Åtgärd | Microsoft.VisualStudio/Project/Write | Ange projekt |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/apiacls/Read | Hämta Api Management konton Apiacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/apiacls/delete | Ta bort Api Management konton API: er Apiacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/apiacls/Read | Hämta Api Management konton API: er Apiacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/apiacls/Write | Uppdatera Apiacls för Api Management konton API: er. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/connectionacls/Read | Hämta Api Management konton API: er Connectionacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/confirmconsentcode/Action | Bekräfta ditt medgivande kod Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/connectionacls/delete | Ta bort Api Management konton API: er anslutningar Connectionacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/connectionacls/Read | Hämta Api Management konton API: er anslutningar Connectionacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/connectionacls/Write | Uppdatera Api Management konton API: er anslutningar Connectionacls. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/delete | Ta bort Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/getconsentlinks/Action | Få godkännande länkar för Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/listconnectionkeys/Action | Lista över anslutning nycklar Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/listsecrets/Action | Lista hemligheter Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/Read | Hämta Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Connections/Write | Uppdatera Api Management konton API: er anslutningar. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/delete | Ta bort API: er för Api Management-konton. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/localizeddefinitions/delete | Ta bort Api Management API: er för konton lokaliserade definitioner. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/localizeddefinitions/Read | Hämta Api-hantering konton API: er lokaliserade definitioner. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/localizeddefinitions/Write | Uppdatera Api Management konton API: er lokaliserade definitioner. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Read | Hämta API: er för Api Management-konton. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/Apis/Write | Uppdatera API: er för Api Management-konton. |
> | Åtgärd | Microsoft.Web/apimanagementaccounts/connectionacls/Read | Hämta Api Management konton Connectionacls. |
> | Åtgärd | Microsoft.Web/availablestacks/Read | Hämta tillgängliga grupper. |
> | Åtgärd | Microsoft.Web/billingmeters/Read | Hämta lista över fakturering mätare. |
> | Åtgärd | Microsoft.Web/certificates/Delete | Ta bort ett befintligt certifikat. |
> | Åtgärd | Microsoft.Web/certificates/Read | Hämta listan över certifikat. |
> | Åtgärd | Microsoft.Web/certificates/Write | Lägg till ett nytt certifikat eller uppdatera en befintlig. |
> | Åtgärd | Microsoft.Web/checknameavailability/Read | Kontrollera om resursnamnet är tillgängliga. |
> | Åtgärd | Microsoft.Web/classicmobileservices/Read | Hämta klassiska Mobile Services. |
> | Åtgärd | Microsoft.Web/connectionGateways/Delete | Tar bort en Gateway med anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/Join/Action | Ansluter till en Gateway med anslutning. |
> | Åtgärd | Microsoft.Web/connectiongateways/Liststatus/Action | Visa Status för anslutningen Gateways. |
> | Åtgärd | Microsoft.Web/connectionGateways/ListStatus/Action | Visar status för en Gateway med anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/Move/Action | Flyttar en Gateway med anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/Read | Hämta listan över anslutningen Gateways. |
> | Åtgärd | Microsoft.Web/connectionGateways/Write | Skapar eller uppdaterar en Gateway med anslutning. |
> | Åtgärd | Microsoft.Web/Connections/confirmconsentcode/Action | Bekräfta anslutningar medgivande kod. |
> | Åtgärd | Microsoft.Web/connections/Delete | Tar bort en anslutning. |
> | Åtgärd | Microsoft.Web/connections/Join/Action | Ansluter till en anslutning. |
> | Åtgärd | Microsoft.Web/Connections/listconsentlinks/Action | Länkar i listan medgivande för anslutningar. |
> | Åtgärd | Microsoft.Web/connections/Move/Action | Flyttar en anslutning. |
> | Åtgärd | Microsoft.Web/connections/Read | Hämta listan över anslutningar. |
> | Åtgärd | Microsoft.Web/connections/Write | Skapar eller uppdaterar en anslutning. |
> | Åtgärd | Microsoft.Web/customApis/Delete | Tar bort en anpassad API. |
> | Åtgärd | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extraherar API-definition från WSDL. |
> | Åtgärd | Microsoft.Web/customApis/Join/Action | Ansluter till en anpassad API. |
> | Åtgärd | Microsoft.Web/customApis/listWsdlInterfaces/Action | Listar WSDL-gränssnitt för en anpassad API. |
> | Åtgärd | Microsoft.Web/customApis/Move/Action | Flyttar en anpassad API. |
> | Åtgärd | Microsoft.Web/customApis/Read | Hämta listan över anpassade API. |
> | Åtgärd | Microsoft.Web/customApis/Write | Skapar eller uppdaterar en anpassad API. |
> | Åtgärd | Microsoft.Web/deploymentlocations/Read | Hämta platser för distributionen. |
> | Åtgärd | Microsoft.Web/geoRegions/Read | Hämta listan över geografiska regionerna. |
> | Åtgärd | Microsoft.Web/hostingenvironments/capacities/Read | Hämta värd miljöer kapacitet. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Delete | Ta bort Apptjänst-miljö |
> | Åtgärd | Microsoft.Web/hostingenvironments/Diagnostics/Read | Hämta värd miljöer diagnostik. |
> | Åtgärd | Microsoft.Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Hämta nätverksslutpunkter av alla inkommande beroenden. |
> | Åtgärd | Microsoft.Web/hostingenvironments/metricdefinitions/Read | Hämta värd miljöer Måttdefinitioner. |
> | Åtgärd | Microsoft.Web/hostingenvironments/multirolepools/metricdefinitions/Read | Hämta värd miljöer mellan pooler Måttdefinitioner. |
> | Åtgärd | Microsoft.Web/hostingenvironments/multirolepools/Metrics/Read | Hämta värd miljöer mellan pooler mått. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/metricDefinitions/Read | Hämtar tillgängliga mått för App Service-miljö MultiRole |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Hämta egenskaperna för en FrontEnd-Pool i en Apptjänst-miljö |
> | Åtgärd | Microsoft.Web/hostingenvironments/multirolepools/skus/Read | Hämta värd miljöer mellan pooler SKU: er. |
> | Åtgärd | Microsoft.Web/hostingenvironments/multirolepools/usages/Read | Hämta värd miljöer mellan pooler användningsområden. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Skapa en ny FrontEnd-Pool i en Apptjänst-miljö eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/hostingenvironments/Operations/Read | Hämta värd Operations miljöer. |
> | Åtgärd | Microsoft.Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Hämta nätverksslutpunkter av alla utgående beroenden. |
> | Åtgärd | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | microsoft.web/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Read | Hämta egenskaperna för en Apptjänstmiljö |
> | Åtgärd | Microsoft.Web/hostingEnvironments/reboot/Action | Starta om alla datorer i en Apptjänst-miljö |
> | Åtgärd | Microsoft.Web/hostingenvironments/Resume/Action | Återuppta värdbaserade miljöer. |
> | Åtgärd | Microsoft.Web/hostingenvironments/serverfarms/Read | Hämta värd miljöer App Service-planer. |
> | Åtgärd | Microsoft.Web/hostingenvironments/Sites/Read | Hämta värd miljöer Web Apps. |
> | Åtgärd | Microsoft.Web/hostingenvironments/suspend/Action | Pausa värdbaserade miljöer. |
> | Åtgärd | Microsoft.Web/hostingenvironments/usages/Read | Hämta värd miljöer användningsområden. |
> | Åtgärd | Microsoft.Web/hostingenvironments/workerpools/metricdefinitions/Read | Hämta värd miljöer Workerpools mått definitioner. |
> | Åtgärd | Microsoft.Web/hostingenvironments/workerpools/Metrics/Read | Hämta värd miljöer Workerpools mått. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read | Hämtar tillgängliga mått för App Service-miljö WorkerPool |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/Read | Hämta egenskaperna för en Arbetspool i en Apptjänst-miljö |
> | Åtgärd | Microsoft.Web/hostingenvironments/workerpools/skus/Read | Hämta värd miljöer Workerpools SKU: er. |
> | Åtgärd | Microsoft.Web/hostingenvironments/workerpools/usages/Read | Hämta värd miljöer Workerpools användningsområden. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/Write | Skapa en ny Arbetspool i en Apptjänst-miljö eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Write | Skapa en ny Apptjänstmiljö eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/ishostingenvironmentnameavailable/Read | Hämta om värd-Miljönamn är tillgängligt. |
> | Åtgärd | Microsoft.Web/ishostnameavailable/Read | Kontrollera om värdnamnet är tillgänglig. |
> | Åtgärd | Microsoft.Web/isusernameavailable/Read | Kontrollera om användarnamnet är tillgängliga. |
> | Åtgärd | Microsoft.Web/listSitesAssignedToHostName/Read | Hämta namnen på de platser som tilldelats värdnamn. |
> | Åtgärd | Microsoft.Web/Locations/apioperations/Read | Hämta platser API-åtgärder. |
> | Åtgärd | Microsoft.Web/Locations/connectiongatewayinstallations/Read | Hämta platser anslutning Gateway installationer. |
> | Åtgärd | Microsoft.Web/Locations/extractapidefinitionfromwsdl/Action | Extrahera Api-Definition från WSDL för platser. |
> | Åtgärd | Microsoft.Web/Locations/listwsdlinterfaces/Action | Visa WSDL-gränssnitt för platser. |
> | Åtgärd | Microsoft.Web/Locations/managedapis/apioperations/Read | Hämta platser hanterade API: et. |
> | Åtgärd | Microsoft.Web/locations/managedapis/Join/Action | Ansluter till en hanterad API. |
> | Åtgärd | Microsoft.Web/Locations/managedapis/Read | Hämta platser hanterade API: er. |
> | Åtgärd | Microsoft.Web/Operations/Read | Hämta åtgärder. |
> | Åtgärd | Microsoft.Web/publishingusers/Read | Hämta publicera användare. |
> | Åtgärd | Microsoft.Web/publishingusers/Write | Uppdatera publicering av användare. |
> | Åtgärd | Microsoft.Web/recommendations/Read | Hämta lista över rekommendationer för prenumerationer. |
> | Åtgärd | Microsoft.Web/register/Action | Registerresursleverantören Microsoft.Web för prenumerationen. |
> | Åtgärd | Microsoft.Web/resourcehealthmetadata/Read | Hämta Resource Health Metadata. |
> | Åtgärd | Microsoft.Web/serverfarms/Capabilities/Read | Hämta App Service-planer funktioner. |
> | Åtgärd | Microsoft.Web/serverfarms/Delete | Ta bort en befintlig App Service-Plan |
> | Åtgärd | Microsoft.Web/serverfarms/firstpartyapps/Settings/delete | Ta bort Apptjänst-planer första part appar inställningar. |
> | Åtgärd | Microsoft.Web/serverfarms/firstpartyapps/Settings/Read | Hämta Apptjänst första part appar inställningar för energischeman. |
> | Åtgärd | Microsoft.Web/serverfarms/firstpartyapps/Settings/Write | Uppdatera Apptjänstplaner första part appar inställningar. |
> | Åtgärd | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/delete | Ta bort Apptjänst-planer Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Read | Hämta App Service-planer Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/serverfarms/hybridconnectionnamespaces/relays/Sites/Read | Hämta App Service-planer Hybrid anslutning namnområden reläer Web Apps. |
> | Åtgärd | Microsoft.Web/serverfarms/hybridconnectionplanlimits/Read | Hämta Plan för App Service-planer Hybrid anslutningsbegränsningar. |
> | Åtgärd | Microsoft.Web/serverfarms/hybridconnectionrelays/Read | Hämta App Service-planer Hybrid anslutning reläer. |
> | Åtgärd | Microsoft.Web/serverfarms/metricdefinitions/Read | Hämta Måttdefinitionerna för App Service-planer. |
> | Åtgärd | Microsoft.Web/serverfarms/Metrics/Read | Hämta mätvärden för App Service-planer. |
> | Åtgärd | Microsoft.Web/serverfarms/operationresults/Read | Hämta App Service-planer Åtgärdsresultat. |
> | Åtgärd | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | microsoft.web/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Web/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read | Hämtar tillgängliga mått för App Service-Plan |
> | Åtgärd | Microsoft.Web/serverfarms/Read | Visa egenskaperna för en App Service-Plan |
> | Åtgärd | Microsoft.Web/serverfarms/restartSites/Action | Starta om alla webbprogram i en Apptjänstplan |
> | Åtgärd | Microsoft.Web/serverfarms/Sites/Read | Hämta App Service-planer Web Apps. |
> | Åtgärd | Microsoft.Web/serverfarms/skus/Read | Hämta App Service-planer SKU: er. |
> | Åtgärd | Microsoft.Web/serverfarms/usages/Read | Hämta App Service-planer användningsområden. |
> | Åtgärd | Microsoft.Web/serverfarms/virtualnetworkconnections/gateways/Write | Uppdatera Apptjänst planer virtuella anslutningar Nätverksgatewayerna. |
> | Åtgärd | Microsoft.Web/serverfarms/virtualnetworkconnections/Read | Hämta virtuella nätverksanslutningar för App Service-planer. |
> | Åtgärd | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/delete | Ta bort Apptjänst-planer virtuella anslutningar nätverksvägar. |
> | Åtgärd | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Read | Hämta App Service-planer virtuella anslutningar nätverksvägar. |
> | Åtgärd | Microsoft.Web/serverfarms/virtualnetworkconnections/routes/Write | Uppdatera Apptjänst planer virtuellt nätverk anslutningar vägar. |
> | Åtgärd | Microsoft.Web/serverfarms/Workers/reboot/Action | Starta om Apptjänst-planer arbetare. |
> | Åtgärd | Microsoft.Web/serverfarms/Write | Skapa en ny App Service-Plan eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/Sites/analyzecustomhostname/Read | Analysera anpassade värdnamnet. |
> | Åtgärd | Microsoft.Web/sites/applySlotConfig/Action | Tillämpa web app platskonfigurationen från mål-plats till det aktuella webbprogrammet |
> | Åtgärd | Microsoft.Web/sites/backup/Action | Skapa en ny säkerhetskopia av web app |
> | Åtgärd | Microsoft.Web/Sites/Backup/Read | Hämta Web Apps säkerhetskopiering. |
> | Åtgärd | Microsoft.Web/Sites/Backup/Write | Uppdatera Web Apps säkerhetskopiering. |
> | Åtgärd | Microsoft.Web/Sites/Backups/delete | Ta bort Web Apps säkerhetskopior. |
> | Åtgärd | Microsoft.Web/Sites/Backups/List/Action | Lista över Web Apps säkerhetskopiering. |
> | Åtgärd | Microsoft.Web/sites/backups/Read | Hämta egenskaperna för ett webbprogram säkerhetskopiering |
> | Åtgärd | Microsoft.Web/Sites/Backups/Restore/Action | Återställa Web Apps säkerhetskopior. |
> | Åtgärd | Microsoft.Web/Sites/config/delete | Ta bort Web Apps Config. |
> | Åtgärd | Microsoft.Web/sites/config/list/Action | Visa en lista med Web App känsliga säkerhetsinställningar, till exempel publicera autentiseringsuppgifter, app-inställningar och anslutningssträngar |
> | Åtgärd | Microsoft.Web/sites/config/Read | Hämta konfigurationsinställningar för webbprogram |
> | Åtgärd | Microsoft.Web/sites/config/Write | Uppdatera Web App konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/Sites/continuouswebjobs/delete | Ta bort Web Apps kontinuerlig Web jobb. |
> | Åtgärd | Microsoft.Web/Sites/continuouswebjobs/Read | Hämta Web Apps kontinuerlig Webjobs. |
> | Åtgärd | Microsoft.Web/Sites/continuouswebjobs/Start/Action | Starta Web Apps kontinuerlig Web jobb. |
> | Åtgärd | Microsoft.Web/Sites/continuouswebjobs/stop/Action | Stoppa Web Apps kontinuerlig Webjobs. |
> | Åtgärd | Microsoft.Web/sites/Delete | Ta bort en befintlig Webbapp |
> | Åtgärd | Microsoft.Web/Sites/Deployments/delete | Ta bort Web Apps distributioner. |
> | Åtgärd | Microsoft.Web/Sites/Deployments/log/Read | Hämta Web Apps distributioner loggen. |
> | Åtgärd | Microsoft.Web/Sites/Deployments/Read | Hämta Web Apps distributioner. |
> | Åtgärd | Microsoft.Web/Sites/Deployments/Write | Uppdatera Web Apps distributioner. |
> | Åtgärd | microsoft.web/sites/diagnostics/analyses/execute/Action | Kör Web Apps diagnostik analys. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/analyses/Read | Hämta Web Apps diagnostik analys. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/aspnetcore/Read | Hämta Web Apps diagnostik för ASP.NET Core app. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/autoheal/Read | Hämta Web Apps diagnostik säkert att Autoheal. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/Deployment/Read | Hämta Web Apps diagnostik-distributionen. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/Deployments/Read | Hämta Web Apps diagnostik distributioner. |
> | Åtgärd | microsoft.web/sites/diagnostics/detectors/execute/Action | Kör Web Apps diagnostik detektor. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/detectors/Read | Hämta Web Apps diagnostik detektor. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/failedrequestsperuri/Read | Hämta Web Apps diagnostik misslyckade förfrågningar Per Uri. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/frebanalysis/Read | Hämta Web Apps diagnostik FREB analys. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/loganalyzer/Read | Hämta Web Apps diagnostik loggen Analyzer. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/Read | Hämta Web Apps diagnostik kategorier. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/runtimeavailability/Read | Hämta Web Apps diagnostik Runtime tillgänglighet. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/servicehealth/Read | Hämta Web Apps diagnostik tjänstens hälsa. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/sitecpuanalysis/Read | Hämta Web Apps diagnostik plats CPU analys. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/sitecrashes/Read | Hämta Web Apps diagnostik plats kraschar. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/sitelatency/Read | Hämta svarstid för Web Apps diagnostik webbplats. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/sitememoryanalysis/Read | Hämta Web Apps diagnostik plats minnesanalys. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/siterestartsettingupdate/Read | Hämta Web Apps diagnostik plats omstart uppdateringen. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/siterestartuserinitiated/Read | Hämta Web Apps diagnostik plats omstart användarinitierad. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/siteswap/Read | Hämta Web Apps diagnostik plats växlingen. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/ThreadCount/Read | Hämta antal trådar för Web Apps diagnostik. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/workeravailability/Read | Hämta Web Apps diagnostik Workeravailability. |
> | Åtgärd | Microsoft.Web/Sites/Diagnostics/workerprocessrecycle/Read | Hämta Web Apps diagnostik Worker omarbetning av processen. |
> | Åtgärd | Microsoft.Web/Sites/domainownershipidentifiers/Read | Hämta Web Apps-domän ägarskap identifierare. |
> | Åtgärd | Microsoft.Web/Sites/domainownershipidentifiers/Write | Uppdatera Web Apps-domän ägarskap identifierare. |
> | Åtgärd | Microsoft.Web/Sites/Functions/Action | Funktioner Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/Functions/delete | Ta bort Web Apps funktioner. |
> | Åtgärd | Microsoft.Web/Sites/Functions/listsecrets/Action | Lista hemligheter Web Apps funktioner. |
> | Åtgärd | Microsoft.Web/Sites/Functions/masterkey/Read | Hämta Web Apps funktioner Masterkey. |
> | Åtgärd | Microsoft.Web/Sites/Functions/Read | Hämta Web Apps funktioner. |
> | Åtgärd | Microsoft.Web/Sites/Functions/token/Read | Get Web Apps funktioner Token. |
> | Åtgärd | Microsoft.Web/Sites/Functions/Write | Uppdatera Web Apps funktioner. |
> | Åtgärd | Microsoft.Web/Sites/hostnamebindings/delete | Ta bort Värdnamnsbindningar för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/hostnamebindings/Read | Hämta Värdnamnsbindningar för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/hostnamebindings/Write | Uppdatera Värdnamnsbindningar för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnection/delete | Ta bort Hybridanslutningen för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnection/Read | Hämta Hybridanslutning för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnection/Write | Uppdatera appar Hybrid-anslutning för webbprogram. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/delete | Ta bort Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/listkeys/Action | Lista nycklar Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Read | Hämta Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnectionnamespaces/relays/Write | Uppdatera Web Apps Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/hybridconnectionrelays/Read | Hämta Web Apps Hybrid anslutning reläer. |
> | Åtgärd | Microsoft.Web/Sites/instances/Deployments/delete | Ta bort Web Apps instanser distributioner. |
> | Åtgärd | Microsoft.Web/Sites/instances/Deployments/Read | Hämta Web Apps instanser distributioner. |
> | Åtgärd | Microsoft.Web/Sites/instances/Extensions/log/Read | Hämta Web Apps instanser tillägg loggen. |
> | Åtgärd | Microsoft.Web/Sites/instances/Extensions/Read | Hämta Web Apps instanser tillägg. |
> | Åtgärd | Microsoft.Web/Sites/instances/processes/delete | Ta bort Web Apps instanser processer. |
> | Åtgärd | Microsoft.Web/Sites/instances/processes/Read | Hämta Web Apps instanser processer. |
> | Åtgärd | Microsoft.Web/Sites/instances/Read | Hämta Web Apps-instanser. |
> | Åtgärd | Microsoft.Web/Sites/listsyncfunctiontriggerstatus/Action | Lista synkronisering funktionen utlösaren Status Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/metricdefinitions/Read | Hämta definitioner för Web Apps mått. |
> | Åtgärd | Microsoft.Web/Sites/Metrics/Read | Hämta mätvärden från webben appar. |
> | Åtgärd | Microsoft.Web/Sites/metricsdefinitions/Read | Hämta definitioner för Web Apps mått. |
> | Åtgärd | Microsoft.Web/Sites/migratemysql/Action | Migrera MySql Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/migratemysql/Read | Hämta Web Apps migrera MySql. |
> | Åtgärd | Microsoft.Web/Sites/networktrace/Action | Webbprogram för spårning i nätverket. |
> | Åtgärd | Microsoft.Web/Sites/NewPassword/Action | Nytt lösenord Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/operationresults/Read | Hämta Åtgärdsresultat för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/Operations/Read | Hämta åtgärder för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/perfcounters/Read | Hämta prestandaräknare för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/premieraddons/delete | Ta bort Web Apps Premier-tillägg. |
> | Åtgärd | Microsoft.Web/Sites/premieraddons/Read | Hämta Web Apps Premier-tillägg. |
> | Åtgärd | Microsoft.Web/Sites/premieraddons/Write | Uppdatera Web Apps Premier-tillägg. |
> | Åtgärd | Microsoft.Web/Sites/processes/Read | Hämta Web Apps processer. |
> | Åtgärd | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | microsoft.web/sites/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Web/sites/providers/Microsoft.Insights/metricDefinitions/Read | Hämtar tillgängliga mått för webbprogram |
> | Åtgärd | Microsoft.Web/Sites/publiccertificates/delete | Ta bort Web Apps offentliga certifikat. |
> | Åtgärd | Microsoft.Web/Sites/publiccertificates/Read | Hämta Web Apps offentliga certifikat. |
> | Åtgärd | Microsoft.Web/Sites/publiccertificates/Write | Uppdatera Web Apps offentliga certifikat. |
> | Åtgärd | Microsoft.Web/sites/publish/Action | Publicera ett webbprogram |
> | Åtgärd | Microsoft.Web/sites/publishxml/Action | Hämta Publicera profil-xml för en Webbapp |
> | Åtgärd | Microsoft.Web/Sites/publishxml/Read | Hämta publicera Web Apps XML. |
> | Åtgärd | Microsoft.Web/sites/Read | Hämta egenskaperna för en Webbapp |
> | Åtgärd | Microsoft.Web/Sites/recommendationhistory/Read | Hämta webbhistorik appar rekommendation. |
> | Åtgärd | Microsoft.Web/Sites/recommendations/disable/Action | Inaktivera Web Apps rekommendationer. |
> | Åtgärd | Microsoft.Web/sites/recommendations/Read | Hämta lista över rekommendationer för webbprogrammet. |
> | Åtgärd | Microsoft.Web/Sites/Recover/Action | Återställa Web Apps. |
> | Åtgärd | Microsoft.Web/sites/resetSlotConfig/Action | Återställ webbappkonfigurationen |
> | Åtgärd | Microsoft.Web/Sites/resourcehealthmetadata/Read | Hämta Web Apps Resource Health Metadata. |
> | Åtgärd | Microsoft.Web/sites/restart/Action | Starta om ett webbprogram |
> | Åtgärd | Microsoft.Web/Sites/Restore/Read | Hämta Web Apps återställning. |
> | Åtgärd | Microsoft.Web/Sites/Restore/Write | Återställa Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/restoresnapshot/Action | Återställa Web Apps ögonblicksbilder. |
> | Åtgärd | Microsoft.Web/Sites/siteextensions/delete | Ta bort Webbplatstillägg för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/siteextensions/Read | Hämta Webbplatstillägg för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/siteextensions/Write | Uppdatera Webbplatstillägg för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/slots/analyzecustomhostname/Read | Hämta Web Apps fack analysera anpassade värdnamnet. |
> | Åtgärd | Microsoft.Web/sites/slots/applySlotConfig/Action | Tillämpa web app platskonfigurationen från målet plats till den aktuella platsen. |
> | Åtgärd | Microsoft.Web/sites/slots/backup/Action | Skapa ny Webbprogramplats säkerhetskopia. |
> | Åtgärd | Microsoft.Web/Sites/slots/Backup/Read | Hämta Web Apps fack säkerhetskopiering. |
> | Åtgärd | Microsoft.Web/Sites/slots/Backup/Write | Uppdatera Web Apps fack säkerhetskopiering. |
> | Åtgärd | Microsoft.Web/Sites/slots/Backups/delete | Ta bort Web Apps fack säkerhetskopior. |
> | Åtgärd | Microsoft.Web/Sites/slots/Backups/List/Action | Lista Web Apps fack säkerhetskopiering. |
> | Åtgärd | Microsoft.Web/sites/slots/backups/Read | Hämta egenskaperna för en webbprogramplatser säkerhetskopiering |
> | Åtgärd | Microsoft.Web/Sites/slots/Backups/Restore/Action | Återställa Web Apps fack säkerhetskopior. |
> | Åtgärd | Microsoft.Web/Sites/slots/config/delete | Ta bort Web Apps fack Config. |
> | Åtgärd | Microsoft.Web/sites/slots/config/list/Action | Visa en lista med Web App fack känsliga säkerhetsinställningar, t.ex publicering autentiseringsuppgifter, app-inställningar och anslutningssträngar |
> | Åtgärd | Microsoft.Web/sites/slots/config/Read | Hämta Webbprogramplatss konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/sites/slots/config/Write | Uppdatera Webbprogramplatss konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/Sites/slots/continuouswebjobs/delete | Ta bort Web Apps fack kontinuerlig Web jobb. |
> | Åtgärd | Microsoft.Web/Sites/slots/continuouswebjobs/Read | Hämta Web Apps fack kontinuerlig Webjobs. |
> | Åtgärd | Microsoft.Web/Sites/slots/continuouswebjobs/Start/Action | Starta Web Apps fack kontinuerlig Web jobb. |
> | Åtgärd | Microsoft.Web/Sites/slots/continuouswebjobs/stop/Action | Stoppa Web Apps fack kontinuerlig Webjobs. |
> | Åtgärd | Microsoft.Web/sites/slots/Delete | Ta bort en befintlig Webbprogramplats |
> | Åtgärd | Microsoft.Web/Sites/slots/Deployments/delete | Ta bort Web Apps fack distributioner. |
> | Åtgärd | Microsoft.Web/Sites/slots/Deployments/log/Read | Hämta Web Apps fack distributioner loggen. |
> | Åtgärd | Microsoft.Web/Sites/slots/Deployments/Read | Hämta Web Apps fack distributioner. |
> | Åtgärd | Microsoft.Web/Sites/slots/Deployments/Write | Uppdatera Web Apps fack distributioner. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/analyses/execute/Action | Kör Web Apps fack diagnostik analys. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/analyses/Read | Hämta Web Apps fack diagnostik analys. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/aspnetcore/Read | Hämta Web Apps fack diagnostik för ASP.NET Core app. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/autoheal/Read | Hämta Web Apps fack diagnostik säkert att Autoheal. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/Deployment/Read | Hämta Web Apps fack diagnostik distributionen. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/Deployments/Read | Hämta Web Apps fack diagnostik distributioner. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/detectors/execute/Action | Kör Web Apps fack diagnostik detektor. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/detectors/Read | Hämta Web Apps fack diagnostik detektor. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/frebanalysis/Read | Hämta Web Apps fack diagnostik FREB analys. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/loganalyzer/Read | Hämta Web Apps fack diagnostik loggen Analyzer. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/Read | Hämta Web Apps fack diagnostik. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/runtimeavailability/Read | Hämta Web Apps fack diagnostik Runtime tillgänglighet. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/servicehealth/Read | Hämta Web Apps fack diagnostik tjänstens hälsa. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/sitecpuanalysis/Read | Hämta Web Apps fack diagnostik plats CPU analys. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/sitecrashes/Read | Hämta Web Apps fack diagnostik plats kraschar. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/sitelatency/Read | Hämta svarstid för Web Apps fack diagnostik webbplats. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/sitememoryanalysis/Read | Hämta Web Apps fack diagnostik plats minnesanalys. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/siterestartsettingupdate/Read | Hämta Web Apps fack diagnostik plats omstart uppdateringen. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/siterestartuserinitiated/Read | Hämta Web Apps fack diagnostik plats omstart användarinitierad. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/siteswap/Read | Hämta Web Apps fack diagnostik plats växlingen. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/ThreadCount/Read | Hämta Trådräkning för Web Apps fack diagnostik. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/workeravailability/Read | Hämta Web Apps fack diagnostik Workeravailability. |
> | Åtgärd | Microsoft.Web/Sites/slots/Diagnostics/workerprocessrecycle/Read | Hämta Web Apps fack diagnostik Worker processåtervinning. |
> | Åtgärd | Microsoft.Web/Sites/slots/domainownershipidentifiers/Read | Hämta Web Apps fack domän ägarskap identifierare. |
> | Åtgärd | Microsoft.Web/Sites/slots/hostnamebindings/delete | Ta bort Värdnamnsbindningar för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/hostnamebindings/Read | Hämta Värdnamnsbindningar för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/hostnamebindings/Write | Uppdatera Värdnamnsbindningar för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/hybridconnection/delete | Ta bort Hybridanslutningen för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/hybridconnection/Read | Hämta Hybridanslutning för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/hybridconnection/Write | Uppdatera Hybridanslutning för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/delete | Ta bort Web Apps fack Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/slots/hybridconnectionnamespaces/relays/Write | Uppdatera Web Apps fack Hybrid anslutning namnområden reläer. |
> | Åtgärd | Microsoft.Web/Sites/slots/hybridconnectionrelays/Read | Hämta Web Apps fack Hybrid anslutning reläer. |
> | Åtgärd | Microsoft.Web/Sites/slots/instances/Deployments/Read | Hämta Web Apps fack instanser distributioner. |
> | Åtgärd | Microsoft.Web/Sites/slots/instances/processes/delete | Ta bort Web Apps fack instanser processer. |
> | Åtgärd | Microsoft.Web/Sites/slots/instances/processes/Read | Hämta Web Apps fack instanser processer. |
> | Åtgärd | Microsoft.Web/Sites/slots/instances/Read | Hämta Web Apps fack instanser. |
> | Åtgärd | Microsoft.Web/Sites/slots/metricdefinitions/Read | Hämta Måttdefinitionerna för Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/Metrics/Read | Hämta Web Apps fack mått. |
> | Åtgärd | Microsoft.Web/Sites/slots/migratemysql/Read | Hämta Web Apps fack migrera MySql. |
> | Åtgärd | Microsoft.Web/Sites/slots/networktrace/Action | Nätverket Trace Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/NewPassword/Action | Nytt lösenord Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/operationresults/Read | Hämta Web Apps fack Åtgärdsresultat. |
> | Åtgärd | Microsoft.Web/Sites/slots/Operations/Read | Hämta Web Apps fack åtgärder. |
> | Åtgärd | Microsoft.Web/Sites/slots/perfcounters/Read | Hämta Web Apps fack prestandaräknare. |
> | Åtgärd | Microsoft.Web/Sites/slots/phplogging/Read | Hämta Web Apps fack Phplogging. |
> | Åtgärd | Microsoft.Web/Sites/slots/premieraddons/delete | Ta bort Web Apps fack Premier-tillägg. |
> | Åtgärd | Microsoft.Web/Sites/slots/premieraddons/Read | Hämta Web Apps fack Premier-tillägg. |
> | Åtgärd | Microsoft.Web/Sites/slots/premieraddons/Write | Uppdatera Web Apps fack Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | microsoft.web/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Web/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read | Hämtar tillgängliga mått för Webbprogramplats |
> | Åtgärd | Microsoft.Web/Sites/slots/publiccertificates/Read | Hämta Web Apps fack offentliga certifikat. |
> | Åtgärd | Microsoft.Web/Sites/slots/publiccertificates/Write | Skapa eller uppdatera Web Apps fack offentliga certifikat. |
> | Åtgärd | Microsoft.Web/sites/slots/publish/Action | Publicera en Webbprogramplats |
> | Åtgärd | Microsoft.Web/sites/slots/publishxml/Action | Hämta Publicera profil-xml för Webbprogramplats |
> | Åtgärd | Microsoft.Web/sites/slots/Read | Hämta egenskaperna för en distributionsplats för webbprogram |
> | Åtgärd | Microsoft.Web/Sites/slots/Recover/Action | Återställa Web Apps platser. |
> | Åtgärd | Microsoft.Web/sites/slots/resetSlotConfig/Action | Återställ platskonfigurationen för web app |
> | Åtgärd | Microsoft.Web/Sites/slots/resourcehealthmetadata/Read | Hämta Web Apps fack resurs hälsa Metadata. |
> | Åtgärd | Microsoft.Web/sites/slots/restart/Action | Starta om en Webbprogramplats |
> | Åtgärd | Microsoft.Web/Sites/slots/Restore/Read | Hämta Web Apps fack återställning. |
> | Åtgärd | Microsoft.Web/Sites/slots/Restore/Write | Återställa Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/restoresnapshot/Action | Återställa Web Apps fack ögonblicksbilder. |
> | Åtgärd | Microsoft.Web/Sites/slots/siteextensions/delete | Ta bort Webbplatstillägg i Web Apps fack. |
> | Åtgärd | Microsoft.Web/Sites/slots/siteextensions/Read | Hämta Webbplatstillägg i Web Apps fack. |
> | Åtgärd | Microsoft.Web/Sites/slots/siteextensions/Write | Uppdatera Webbplatstillägg i Web Apps fack. |
> | Åtgärd | Microsoft.Web/sites/slots/slotsdiffs/Action | Hämta konfigurationsavvikelser mellan webbprogrammet och platser |
> | Åtgärd | Microsoft.Web/sites/slots/slotsswap/Action | Växla distributionsplatser för webbprogram |
> | Åtgärd | Microsoft.Web/Sites/slots/Snapshots/Read | Hämta ögonblicksbilder för Web Apps platser. |
> | Åtgärd | Microsoft.Web/sites/slots/sourcecontrols/Delete | Ta bort konfigurationsinställningar för Web App fack källa kontroll |
> | Åtgärd | Microsoft.Web/sites/slots/sourcecontrols/Read | Hämta Webbprogramplatss källkontrollen konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/sites/slots/sourcecontrols/Write | Uppdatera inställningar för Web App fack källkontrollen configuration |
> | Åtgärd | Microsoft.Web/sites/slots/start/Action | Starta en Webbprogramplats |
> | Åtgärd | Microsoft.Web/sites/slots/stop/Action | Stoppa en Webbprogramplats |
> | Åtgärd | Microsoft.Web/Sites/slots/Sync/Action | Synkronisera Web Apps platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/triggeredwebjobs/delete | Ta bort Web Apps fack utlösta WebJobs. |
> | Åtgärd | Microsoft.Web/Sites/slots/triggeredwebjobs/Read | Hämta Web Apps fack utlösta WebJobs. |
> | Åtgärd | Microsoft.Web/Sites/slots/triggeredwebjobs/Run/Action | Kör Web Apps fack utlösta WebJobs. |
> | Åtgärd | Microsoft.Web/Sites/slots/usages/Read | Hämta Web Apps fack användningsområden. |
> | Åtgärd | Microsoft.Web/Sites/slots/virtualnetworkconnections/delete | Ta bort virtuella nätverksanslutningar för Web Apps-platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/virtualnetworkconnections/gateways/Write | Uppdatera Web Apps fack virtuella anslutningar Nätverksgatewayerna. |
> | Åtgärd | Microsoft.Web/Sites/slots/virtualnetworkconnections/Read | Hämta virtuella nätverksanslutningar för Web Apps-platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/virtualnetworkconnections/Write | Uppdatera virtuella nätverksanslutningar för Web Apps-platser. |
> | Åtgärd | Microsoft.Web/Sites/slots/webjobs/Read | Hämta Web Apps fack WebJobs. |
> | Åtgärd | Microsoft.Web/sites/slots/Write | Skapa en ny Webbprogramplats eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/sites/slotsdiffs/Action | Hämta konfigurationsavvikelser mellan webbprogrammet och platser |
> | Åtgärd | Microsoft.Web/sites/slotsswap/Action | Växla distributionsplatser för webbprogram |
> | Åtgärd | Microsoft.Web/Sites/Snapshots/Read | Hämta ögonblicksbilder för Web Apps. |
> | Åtgärd | Microsoft.Web/sites/sourcecontrols/Delete | Ta bort konfigurationsinställningar för Web App käll-kontroll |
> | Åtgärd | Microsoft.Web/sites/sourcecontrols/Read | Hämta Web App källkontrollen konfigurationsinställningar |
> | Åtgärd | Microsoft.Web/sites/sourcecontrols/Write | Uppdatera inställningar för Web App källkontrollen konfiguration |
> | Åtgärd | Microsoft.Web/sites/start/Action | Starta ett webbprogram |
> | Åtgärd | Microsoft.Web/sites/stop/Action | Stoppa ett webbprogram |
> | Åtgärd | Microsoft.Web/Sites/Sync/Action | Synkronisera Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/syncfunctiontriggers/Action | Synkronisera funktionen utlösare för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/triggeredwebjobs/delete | Ta bort Web Apps utlösta WebJobs. |
> | Åtgärd | Microsoft.Web/Sites/triggeredwebjobs/History/Read | Hämta appar utlösta WebJobs webbhistorik. |
> | Åtgärd | Microsoft.Web/Sites/triggeredwebjobs/Read | Hämta Web Apps utlösta WebJobs. |
> | Åtgärd | Microsoft.Web/Sites/triggeredwebjobs/Run/Action | Kör Web Apps utlöses WebJobs. |
> | Åtgärd | Microsoft.Web/Sites/usages/Read | Hämta Web Apps användningsområden. |
> | Åtgärd | Microsoft.Web/Sites/virtualnetworkconnections/delete | Ta bort virtuella nätverksanslutningar för Web Apps. |
> | Åtgärd | Microsoft.Web/Sites/virtualnetworkconnections/gateways/Read | Hämta Web Apps virtuella anslutningar Nätverksgatewayerna. |
> | Åtgärd | Microsoft.Web/Sites/virtualnetworkconnections/gateways/Write | Uppdatera Web Apps virtuella anslutningar Nätverksgatewayerna. |
> | Åtgärd | Microsoft.Web/Sites/virtualnetworkconnections/Read | Hämta Web Apps virtuella nätverksanslutningar. |
> | Åtgärd | Microsoft.Web/Sites/virtualnetworkconnections/Write | Uppdatera Web Apps virtuella nätverksanslutningar. |
> | Åtgärd | Microsoft.Web/Sites/webjobs/Read | Hämta Web Apps WebJobs. |
> | Åtgärd | Microsoft.Web/sites/Write | Skapa ett nytt webbprogram eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Web/skus/Read | Hämta SKU: er. |
> | Åtgärd | Microsoft.Web/sourcecontrols/Read | Hämta kontroller för datakällor. |
> | Åtgärd | Microsoft.Web/sourcecontrols/Write | Uppdatera källa kontroller. |
> | Åtgärd | Microsoft.Web/unregister/Action | Avregistrera Microsoft.Web resource provider för prenumerationen. |
> | Åtgärd | Microsoft.Web/Validate/Action | Validera. |
> | Åtgärd | Microsoft.Web/verifyhostingenvironmentvnet/Action | Kontrollera värd för virtuella nätverk i miljön. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.WorkloadMonitor/components/read | Läsa operationsresurser |
> | Åtgärd | Microsoft.WorkloadMonitor/healthInstances/read | Läsa operationsresurser |
> | Åtgärd | Microsoft.WorkloadMonitor/Operations/read | Läsa operationsresurser |
> | Åtgärd | Microsoft.WorkloadMonitor/workloads/delete | Tar bort en resurs för arbetsbelastning |
> | Åtgärd | Microsoft.WorkloadMonitor/workloads/read | Läser en arbetsbelastning resurs |
> | Åtgärd | Microsoft.WorkloadMonitor/workloads/write | Skriver en arbetsbelastning resurs |

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller](custom-roles.md)
- [Inbyggda roller](built-in-roles.md)
