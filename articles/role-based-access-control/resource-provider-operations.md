---
title: Azure Resource Manager Resource Provider-åtgärder | Microsoft Docs
description: Visar en lista över de åtgärder som är tillgängliga för resurs leverantörerna för Microsoft Azure Resource Manager
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9d4b4134fa26fd2cb904a862ac16544873bf8bcb
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934469"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Åtgärder för Azure Resource Manager Resource Provider

Den här artikeln visar de åtgärder som är tillgängliga för varje Azure Resource Manager resurs leverantör. Dessa åtgärder kan användas i [anpassade roller](custom-roles.md) för att ge detaljerad [rollbaserad åtkomst kontroll (RBAC)](overview.md) till resurser i Azure. Åtgärds strängar har följande format: `{Company}.{ProviderName}/{resourceType}/{action}`. En lista över hur resurs namn rymder mappar till Azure-tjänster finns i [matcha Resource Provider to service](../azure-resource-manager/azure-services-resource-providers.md).

Resurs leverantörs åtgärderna utvecklas alltid. Använd [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) eller [AZ Provider operation List](/cli/azure/provider/operation#az-provider-operation-list)för att få de senaste åtgärderna.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. AAD/domainServices/Delete | Ta bort domän tjänst |
> | Åtgärd | Microsoft. AAD/domainServices/oucontainer/Delete | Ta bort ou-behållare |
> | Åtgärd | Microsoft. AAD/domainServices/oucontainer/Read | Läs ou-behållare |
> | Åtgärd | Microsoft. AAD/domainServices/oucontainer/Write | Skriv ou-behållare |
> | Åtgärd | Microsoft. AAD/domainServices/Read | Läs domän tjänster |
> | Åtgärd | Microsoft. AAD/domainServices/Write | Skriv domän tjänst |
> | Åtgärd | Microsoft. AAD/locations/operationresults/Read |  |
> | Åtgärd | Microsoft. AAD/åtgärder/läsa |  |
> | Åtgärd | Microsoft. AAD/register/åtgärd | Registrera domän tjänst |
> | Åtgärd | Microsoft. AAD/avregistrera/åtgärd | Avregistrera domän tjänsten |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. aadiam/diagnosticsettings/Delete | Tar bort en diagnostisk inställning |
> | Åtgärd | Microsoft. aadiam/diagnosticsettings/Read | Läser en diagnostisk inställning |
> | Åtgärd | Microsoft. aadiam/diagnosticsettings/Write | Skriva en diagnostisk inställning |
> | Åtgärd | Microsoft. aadiam/diagnosticsettingscategories/Read | Läser en diagnostisk inställnings kategori |
> | Åtgärd | Microsoft. aadiam/metricDefinitions/Read | Läser mått definitioner på klient nivå |
> | Åtgärd | Microsoft. aadiam/Metrics/Read | Läser mått på klient nivå |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. addons/Operations/Read | Hämtar RP-åtgärder som stöds. |
> | Åtgärd | Microsoft. addons/register/åtgärd | Registrera den angivna prenumerationen med Microsoft. addons |
> | Åtgärd | Microsoft. addons/supportProviders/listsupportplaninfo/Action | Visar aktuell support Plans information för den angivna prenumerationen. |
> | Åtgärd | Microsoft. addons/supportProviders/supportPlanTypes/Delete | Tar bort det angivna kanoniska support avtalet |
> | Åtgärd | Microsoft. addons/supportProviders/supportPlanTypes/Read | Hämta det angivna kanoniska support Plans tillstånd. |
> | Åtgärd | Microsoft. addons/supportProviders/supportPlanTypes/Write | Lägger till den kanoniska support Plans typen som angetts. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/action | Skapa en ny skog för klient organisationen. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Hämtar alla servrar för det angivna tjänst namnet. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/alerts/read | Hämtar aviserings information för skogen, t. ex. AlertID, varnings datum, avisering senast identifierad, aviserings beskrivning, senast uppdaterad, aviserings nivå, aviserings tillstånd, fel söknings länkar osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/configuration/read | Hämtar tjänst konfiguration för skogen. Exempel – skogs namn, funktions nivå, FSMO-roll för domän namn givnings hanterare, FSMO-roll för schema hanterare osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/delete | Tar bort en tjänst och dess servrar tillsammans med hälso data. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Hämtar information om domäner och platser för skogen. Exempel – hälso status, aktiva aviseringar, lösta aviseringar, egenskaper som domän funktions nivå, skog, infrastruktur hanterare, PDC, RID-hanterare osv.  |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Hämtar användar inställnings inställningen för skogen.<br>Exempel – MetricCounterName som ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Inställningar för GRÄNSSNITTs diagram osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Hämtar skogs Sammanfattning för den aktuella skogen som skogs namn, antal domäner under den här skogen, antal platser och plats information osv. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Hämtar listan över mått som stöds för en specifik tjänst.<br>Till exempel utelåsning av extra nät konton, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFS-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv. för ADDomainService.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Med den här API: n hämtas mått informationen.<br>Detta API kan till exempel användas för att hämta information relaterad till: konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, väntande token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Detta API hämtar listan över alla inbyggda ADDomainServices för en Premium-klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/read | Hämtar tjänst information för det angivna tjänst namnet. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Hämtar replikeringsinformation för alla servrar för det angivna tjänst namnet. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Hämtar antalet domänkontrollanter och deras replikeringsfel om några. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Hämtar fullständig domänkontrollant-lista tillsammans med replikeringsinformation för den aktuella skogen. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Lägg till en Server instans till tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Under Server registreringen av ADDomainService anropas detta API för att hämta autentiseringsuppgifterna för att registrera nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Tar bort en server för en specifik tjänst och klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/addsservices/write | Skapar eller uppdaterar ADDomainService-instansen för klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/action | Uppdaterar klient konfigurationen. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/read | Läser klient konfigurationen. |
> | Åtgärd | Microsoft.ADHybridHealthService/configuration/write | Skapar en klient konfiguration. |
> | Åtgärd | Microsoft.ADHybridHealthService/logs/contents/read | Hämtar innehållet i agent installations-och registrerings loggar som lagras i BLOB. |
> | Åtgärd | Microsoft.ADHybridHealthService/logs/read | Hämtar agent installations-och registrerings loggar för klient organisationen. |
> | Åtgärd | Microsoft.ADHybridHealthService/operations/read | Hämtar en lista över åtgärder som stöds av systemet. |
> | Åtgärd | Microsoft.ADHybridHealthService/register/action | Registrerar ADHybrid-Hälsotjänst Resource Provider och gör det möjligt att skapa ADHybrid-Hälsotjänst resurs. |
> | Åtgärd | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Hämtar en lista över tillgängliga regioner som används av DevOps för att stödja kund incidenter. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/badpassword/read | Hämtar listan över felaktiga lösen ords försök för alla användare i Active Directory Federationstjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Hämtar blobb-URI: n med status och det slutliga resultatet av det nyligen köade rapport jobbet för frekvensen av felaktiga användar namn/lösen ords försök per UserId per IP-adress och dag för en viss klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Hämtar listan över DevOps-godkända klienter. Används vanligt vis för kund support. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/isdevops/read | Hämtar ett värde som anger om klienten är DevOps eller inte. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Uppdaterar UserID (ObjectID) för den valda dev-klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Hämtar vald distribution för den angivna klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Ett klient-ID hämtar klient organisationens lagrings plats. |
> | Åtgärd | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Hämtar den geografiska plats från vilken data kommer att nås. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/action | Uppdaterar en tjänst instans i klient organisationen. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringarna för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringarna för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Med ett funktions namn verifieras om en tjänst har allt som krävs för att använda funktionen. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/delete | Tar bort en tjänst instans i klient organisationen. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/exporterrors/read | Hämtar export fel för en specifik Sync-tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/exportstatus/read | Hämtar export status för en specifik tjänst. |
> | Åtgärd | Microsoft. ADHybridHealthService/Services/feedbacktype/feedback/Read | Hämtar feedback om aviseringar för en specifik tjänst och server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metricmetadata/read | Hämtar listan över mått som stöds för en specifik tjänst.<br>Till exempel utelåsning av extra nät konton, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFS-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv. för ADDomainService.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Med den här API: n hämtas medelvärdet för mått för en specifik tjänst.<br>Detta API kan till exempel användas för att hämta information relaterad till: konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, väntande token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/read | Med den här API: n hämtas mått informationen.<br>Detta API kan till exempel användas för att hämta information relaterad till: konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, väntande token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Med den här API: n hämtas den sammanställda vyn för mått för en specifik tjänst.<br>Detta API kan till exempel användas för att hämta information relaterad till: konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, väntande token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Lägga till eller uppdatera övervaknings konfigurationen för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Hämtar övervaknings konfigurationerna för en specifik tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Lägg till eller uppdatera övervaknings konfigurationerna för en tjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/premiumcheck/read | Detta API hämtar listan över alla inbyggda tjänster för en Premium-klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/read | Läser tjänst instanserna i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Hämtar alla riskfyllda IP-rapport-URI: er under de senaste 7 dagarna. |
> | Åtgärd | Microsoft. ADHybridHealthService/Services/Reports/information/läsa | Hämtar rapport över främsta 50-användare med felaktiga lösen ords fel från de senaste 7 dagarna |
> | Åtgärd | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Genererar en riskfylld IP-rapport och returnerar en URI som pekar på den. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/action | Skapar en Server instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Läser aviseringarna för en server. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Under Server registreringen anropas detta API för att hämta autentiseringsuppgifterna för att registrera nya servrar. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | För en specifik server hämtar detta API en lista med data typer som laddas upp av servrarna och den senaste tiden för varje uppladdning. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/delete | Tar bort en Server instans i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Hämtar information om synkronisering av export fel för en specifik synkroniseringstjänst. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Med den här API: n hämtas mått informationen.<br>Detta API kan till exempel användas för att hämta information relaterad till: konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, väntande token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Hämtar listan över kopplingar och kör profil namn för den tjänst-och tjänst medlem som angetts. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/read | Läser Server instansen i tjänsten. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Hämtar tjänst konfigurationen för en specifik klient organisation. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Hämtar funktionens vit listning status för en specifik klient. |
> | Åtgärd | Microsoft.ADHybridHealthService/services/write | Skapar en tjänst instans i klienten. |
> | Åtgärd | Microsoft.ADHybridHealthService/unregister/action | Avregistrerar prenumerationen för ADHybrid Hälsotjänst Resource Provider. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Advisor/configurations/read | Hämta konfigurationer |
> | Åtgärd | Microsoft.Advisor/configurations/write | Skapar/uppdaterar konfiguration |
> | Åtgärd | Microsoft.Advisor/generateRecommendations/action | Hämtar status för rekommendationer |
> | Åtgärd | Microsoft.Advisor/generateRecommendations/read | Hämtar status för rekommendationer |
> | Åtgärd | Microsoft.Advisor/metadata/read | Hämta metadata |
> | Åtgärd | Microsoft.Advisor/operations/read | Hämtar åtgärderna för Microsoft Advisor |
> | Åtgärd | Microsoft. Advisor/rekommendationer/tillgänglig/åtgärd | Ny rekommendation finns i Microsoft Advisor |
> | Åtgärd | Microsoft. Advisor/rekommendationer/läsa | Läser rekommendationer |
> | Åtgärd | Microsoft. Advisor/rekommendationer/undertrycks/ta bort | Tar bort utelämnande |
> | Åtgärd | Microsoft. Advisor/rekommendationer/under tryckningar/läsning | Hämtar utelämnar |
> | Åtgärd | Microsoft. Advisor/rekommendationer/undertrycks/Skriv åtgärder | Skapar/uppdaterar ignorerar |
> | Åtgärd | Microsoft. Advisor/register/åtgärd | Registrerar prenumerationen för Microsoft Advisor |
> | Åtgärd | Microsoft. Advisor/undertrycks/ta bort | Tar bort utelämnande |
> | Åtgärd | Microsoft. Advisor/undertrycks/läsa | Hämtar utelämnar |
> | Åtgärd | Microsoft. Advisor/undertrycker/Skriv | Skapar/uppdaterar ignorerar |
> | Åtgärd | Microsoft. Advisor/avregistrering/åtgärd | Avregistrerar prenumerationen för Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AlertsManagement/actionRules/delete | Ta bort åtgärds regel i en specifik prenumeration. |
> | Åtgärd | Microsoft.AlertsManagement/actionRules/read | Hämta alla åtgärds regler för inkommande filter. |
> | Åtgärd | Microsoft.AlertsManagement/actionRules/write | Skapa eller uppdatera åtgärds regel i en specifik prenumeration |
> | Åtgärd | Microsoft.AlertsManagement/alerts/changestate/action | Ändra aviseringens status. |
> | Åtgärd | Microsoft. AlertsManagement/Alerts/Diagnostics/Read | Hämta all diagnostik för aviseringen |
> | Åtgärd | Microsoft. AlertsManagement/Alerts/History/Read | Hämta historik för aviseringen |
> | Åtgärd | Microsoft.AlertsManagement/alerts/read | Hämta alla aviseringar för inkommande filter. |
> | Åtgärd | Microsoft.AlertsManagement/alertsList/read | Hämta alla aviseringar för inkommande filter över prenumerationer |
> | Åtgärd | Microsoft. AlertsManagement/alertsMetaData/Read | Hämta aviserings-meta-data för indataparametern. |
> | Åtgärd | Microsoft.AlertsManagement/alertsSummary/read | Hämta sammanfattningen av aviseringar |
> | Åtgärd | Microsoft.AlertsManagement/alertsSummaryList/read | Få en översikt över aviseringar över prenumerationer |
> | Åtgärd | Microsoft. AlertsManagement/Operations/Read | Läser de åtgärder som tillhandahålls |
> | Åtgärd | Microsoft. AlertsManagement/register/åtgärd | Registrerar prenumerationen för Microsoft Alerts Management |
> | Åtgärd | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Ta bort varnings regeln för smart detektor i en specifik prenumeration |
> | Åtgärd | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Hämta alla varnings regler för smart detektor för inkommande filter |
> | Åtgärd | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Skapa eller uppdatera varnings regeln för smart detektor i en specifik prenumeration |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/changestate/action | Ändra status för den smarta gruppen |
> | Åtgärd | Microsoft. AlertsManagement/smartGroups/historik/läsa | Hämta historik för den smarta gruppen |
> | Åtgärd | Microsoft.AlertsManagement/smartGroups/read | Hämta alla smarta grupper för inkommande filter |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Kontrollerar att angivet Analysis Server namn är giltigt och inte används. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationresults/read | Retrieves the information of the specified operation result. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationstatuses/read | Retrieves the information of the specified operation status. |
> | Åtgärd | Microsoft. AnalysisServices/Operations/Read | Retrieves the information of operations |
> | Åtgärd | Microsoft. AnalysisServices/register/åtgärd | Registrerar Analysis Services Resource Provider. |
> | Åtgärd | Microsoft.AnalysisServices/servers/delete | Tar bort Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Visar status för den gateway som är associerad med servern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/read | Hämtar information om den angivna Analysis Server. |
> | Åtgärd | Microsoft. AnalysisServices/servrar/återuppta/åtgärd | Återupptar Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/skus/read | Hämta tillgänglig SKU-information för servern |
> | Åtgärd | Microsoft. AnalysisServices/-servrar/PAUSE/åtgärd | Pausar Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/write | Skapar eller uppdaterar den angivna Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/skus/read | Retrieves the information of Skus |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. API Management/checkNameAvailability/Read | Kontrollerar om det angivna tjänst namnet är tillgängligt |
> | Åtgärd | Microsoft. API Management/Operations/Read | Läs alla API-åtgärder som är tillgängliga för Microsoft. API Management-resursen |
> | Åtgärd | Microsoft. API Management/register/åtgärd | Registrera prenumeration för Microsoft. API Management Resource Provider |
> | Åtgärd | Microsoft. API Management/Reports/Read | Hämta rapporter sammanställda per tids period, geografisk region, utvecklare, produkter, API: er, åtgärder, prenumeration och byRequest. |
> | Åtgärd | Microsoft. API Management/Service/API/Delete | Tar bort det angivna API: t för API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/Apis/diagnostik/ta bort | Tar bort den angivna diagnostiken från ett API. |
> | Åtgärd | Microsoft. API Management/Service/Apis/diagnostik/läsa | Visar all diagnostik för ett API. eller hämtar information om diagnostiken för ett API som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Apis/diagnostik/Skriv | Skapar en ny diagnostik för ett API eller uppdaterar en befintlig. eller uppdaterar informationen om diagnostiken för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/bilagor/ta bort | Tar bort den angivna kommentaren från ett problem. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/bilagor/läsa | Visar alla bifogade filer för det problem som är associerat med angivet API. eller hämtar information om den bifogade filen för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/attachments/write | Skapar en ny bilaga för problemet i ett API eller uppdaterar ett befintligt. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/kommentarer/ta bort | Tar bort den angivna kommentaren från ett problem. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/kommentarer/läsa | Visar alla kommentarer för det problem som är associerat med angivet API. eller hämtar information om problem kommentaren för ett API som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/issues/comments/write | Skapar en ny kommentar för problemet i ett API eller uppdaterar ett befintligt. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/ta bort | Tar bort det angivna problemet från ett API. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/läsa | Visar alla problem som är associerade med angivet API. eller hämtar information om problemet för ett API som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/API/problem/skriv | Skapar ett nytt problem för ett API eller uppdaterar ett befintligt. eller uppdaterar ett befintligt problem för ett API. |
> | Åtgärd | Microsoft. API Management/Service/API/Operations/Delete | Tar bort den angivna åtgärden i API: et. |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/policies/Delete | Tar bort princip konfigurationen i API-åtgärden. |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/policies/Read | Hämta listan över princip konfigurationen på API-åtgärds nivån. eller hämta princip konfigurationen på nivån API-åtgärd. |
> | Åtgärd | Microsoft. API Management/Service/API/Operations/policies/Write | Skapar eller uppdaterar princip konfigurationen för API-åtgärds nivån. |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/policy/Delete | Ta bort princip konfigurationen på åtgärds nivå |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/policy/Read | Hämta princip konfigurationen på åtgärds nivå |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/policy/Write | Skapa princip konfiguration på åtgärds nivå |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/read | Visar en samling åtgärder för angivet API. eller hämtar information om den API-åtgärd som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/Tags/Delete | Koppla bort taggen från åtgärden. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operations/tags/read | Visar en lista över alla Taggar som är associerade med åtgärden. eller hämta taggen som är associerad med åtgärden. |
> | Åtgärd | Microsoft. API Management/Service/Apis/Operations/Tags/Write | Tilldela en tagg till åtgärden. |
> | Åtgärd | Microsoft. API Management/Service/Apis/åtgärder/skrivning | Skapar en ny åtgärd i API: et eller uppdaterar en befintlig. eller uppdaterar informationen om åtgärden i API: et som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/operationsByTags/read | Visar en samling åtgärder som är kopplade till taggar. |
> | Åtgärd | Microsoft. API Management/Service/Apis/policies/Delete | Tar bort princip konfigurationen i API: et. |
> | Åtgärd | Microsoft. API Management/Service/Apis/policies/Read | Hämta princip konfigurationen på API-nivå. eller hämta princip konfigurationen på API-nivå. |
> | Åtgärd | Microsoft. API Management/Service/Apis/policies/Write | Skapar eller uppdaterar princip konfigurationen för API: et. |
> | Åtgärd | Microsoft. API Management/Service/Apis/princip/ta bort | Ta bort princip konfigurationen på API-nivå |
> | Åtgärd | Microsoft. API Management/Service/Apis/policy/Read | Hämta princip konfigurationen på API-nivå |
> | Åtgärd | Microsoft. API Management/Service/Apis/princip/Skriv | Skapa princip konfiguration på API-nivå |
> | Åtgärd | Microsoft. API Management/Service/Apis/Products/Read | Visar alla produkter som API: et är en del av. |
> | Åtgärd | Microsoft. API Management/Service/API/Read | Visar en lista över alla API: er för API Management tjänst instansen. eller hämtar information om det API som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/API/releases/Delete | Tar bort alla versioner av API: et eller tar bort den angivna versionen i API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/releases/read | Visar en lista över alla utgåvor av ett API.<br>En API-version skapas när en API-revision är aktuell.<br>Versioner används också för att återställa till tidigare revisioner.<br>Resultaten kommer att växlas och kan begränsas av $top och $skip parametrar.<br>eller returnerar information om en API-version. |
> | Åtgärd | Microsoft. API Management/Service/API/releases/Write | Skapar en ny version för API: et. eller uppdaterar informationen om versionen av API: et som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Apis/revisioner/Delete | Tar bort alla revisioner av ett API |
> | Åtgärd | Microsoft. API Management/Service/Apis/revisioner/Read | Visar alla revisioner av ett API. |
> | Åtgärd | Microsoft. API Management/Service/API/schema/ta bort | Tar bort schema konfigurationen i API: et. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/read | Hämta schema konfigurationen på API-nivå. eller hämta schema konfigurationen på API-nivå. |
> | Åtgärd | Microsoft.ApiManagement/service/apis/schemas/write | Skapar eller uppdaterar schema konfigurationen för API: et. |
> | Åtgärd | Microsoft. API Management/Service/API/tagDescriptions/Delete | Ta bort etikett beskrivning för API: et. |
> | Åtgärd | Microsoft. API Management/Service/API/tagDescriptions/Read | Visar en lista med beskrivningar av alla Taggar i API-omfånget. Modellen som liknar Swagger-tagDescription har definierats på API-nivå men taggen kan tilldelas till åtgärder eller hämta tag-beskrivningen i API-omfånget |
> | Åtgärd | Microsoft. API Management/Service/API/tagDescriptions/Write | Skapa/uppdatera tagg-beskrivning i omfånget för API: et. |
> | Åtgärd | Microsoft. API Management/Service/API/Taggar/ta bort | Koppla bort taggen från API: et. |
> | Åtgärd | Microsoft. API Management/Service/API/Taggar/läsa | Visar alla Taggar som är kopplade till API: et. eller hämta taggen som är associerad med API: et. |
> | Åtgärd | Microsoft. API Management/Service/API/Taggar/skriva | Tilldela en tagg till API: et. |
> | Åtgärd | Microsoft. API Management/Service/API/Write | Skapar nya eller uppdateringar av befintligt angivet API för API Management tjänst instansen. eller uppdaterar angivet API för API Management tjänst instansen. |
> | Åtgärd | Microsoft.ApiManagement/service/apisByTags/read | Visar en samling API: er som är associerade med taggar. |
> | Åtgärd | Microsoft. API Management/Service/apiVersionSets/Delete | Tar bort en angiven API-version. |
> | Åtgärd | Microsoft. API Management/Service/apiVersionSets/Read | Visar en samling av API-versioner som anges i den angivna tjänst instansen. eller hämtar information om den API-version som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Hämta lista över versions enheter |
> | Åtgärd | Microsoft. API Management/Service/apiVersionSets/Write | Skapar eller uppdaterar en API-version uppsättning. eller uppdaterar informationen om API-VersionSet som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Uppdaterar de Microsoft. API Management-resurser som körs i Virtual Network för att välja uppdaterade nätverks inställningar. |
> | Åtgärd | Microsoft. API Management/Service/authorizationServers/Delete | Tar bort en angiven instans av auktoriseringsservern. |
> | Åtgärd | Microsoft. API Management/Service/authorizationServers/listSecrets/Action | Hämtar hemligheter för auktoriseringsservern. |
> | Åtgärd | Microsoft. API Management/Service/authorizationServers/Read | Visar en uppsättning auktoriserade servrar som definierats inom en tjänst instans. eller hämtar information om auktoriseringsservern utan hemligheter. |
> | Åtgärd | Microsoft. API Management/Service/authorizationServers/Write | Skapar en ny auktoriseringsprincip eller uppdaterar en befintlig auktoriseringsprincip. eller uppdaterar informationen om auktoriseringsservern som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/server del/Delete | Tar bort den angivna Server delen. |
> | Åtgärd | Microsoft. API Management/Service/backend/Read | Visar en samling av Server delar i den angivna tjänst instansen. eller hämtar information om Server delen som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/server delar/reconnect/åtgärd | Meddelar APIM proxy om att skapa en ny anslutning till Server delen efter den angivna tids gränsen. Om ingen tids gräns angavs används tids gränsen på 2 minuter. |
> | Åtgärd | Microsoft. API Management/Service/backend/Write | Skapar eller uppdaterar en server del. eller uppdaterar en befintlig server del. |
> | Åtgärd | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiera API Management tjänst till den angivna behållaren i ett användardefinierat lagrings konto |
> | Åtgärd | Microsoft. API Management/Service/Caches/Delete | Tar bort en angiven cache. |
> | Åtgärd | Microsoft. API Management/Service/cache/Read | Visar en samling med alla externa cacheminnen i den angivna tjänst instansen. eller hämtar information om cachen som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/cache/Write | Skapar eller uppdaterar ett externt cacheminne som ska användas i API Management-instansen. eller uppdaterar informationen om den cache som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/certificates/Delete | Tar bort ett speciellt certifikat. |
> | Åtgärd | Microsoft. API Management/Service/certificates/Read | Listar en samling med alla certifikat i den angivna tjänst instansen. eller hämtar information om det certifikat som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/certificates/Write | Skapar eller uppdaterar det certifikat som används för autentisering med Server delen. |
> | Åtgärd | Microsoft. API Management/Service/contentType/contentItems/Delete | Tar bort angivet innehålls objekt. |
> | Åtgärd | Microsoft. API Management/Service/contentType/contentItems/Read | Returnerar lista med innehålls objekt eller returnerar information om innehålls objekt |
> | Åtgärd | Microsoft. API Management/Service/contentType/contentItems/Write | Skapar nytt innehålls objekt eller uppdaterar angivet innehålls objekt |
> | Åtgärd | Microsoft. API Management/Service/contentType/Read | Returnerar lista över innehålls typer |
> | Åtgärd | Microsoft.ApiManagement/service/delete | Ta bort API Management tjänst instans |
> | Åtgärd | Microsoft. API Management/Service/Diagnostics/Delete | Tar bort den angivna diagnostiken. |
> | Åtgärd | Microsoft. API Management/Service/Diagnostics/Read | Visar en lista över alla diagnostiker för den API Management tjänst instansen. eller hämtar information om diagnostiken som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Diagnostics/Write | Skapar en ny diagnostik eller uppdaterar en befintlig. eller uppdaterar informationen om diagnostiken som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/åtgärd | Hämtar gateway-konfiguration. eller uppdaterar gatewayens pulsslag. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/API/Delete | Tar bort angivet API från den angivna gatewayen. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/API/Read | Visar en samling med API: er som är associerade med en gateway. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/API: er/skriva | Lägger till ett API till den angivna gatewayen. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/Delete | Tar bort en angiven Gateway. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/hostnameConfigurations/Read | Visar en lista med hostname-konfigurationer för den angivna gatewayen. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/nycklar/åtgärd | Hämtar Gateway-nycklar. |
> | Åtgärd | Microsoft. API Management/Service/Gateway/Read | Visar en lista med gateways som registrerats med tjänst instansen. eller hämtar information om den gateway som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/regeneratePrimaryKey/åtgärd | Återskapar den primära Gateway-nyckeln invalidationg alla tokens som skapats med den. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/regenerateSecondaryKey/åtgärd | Återskapar en sekundär Gateway-nyckel invalidationg alla tokens som skapats med den. |
> | Åtgärd | Microsoft. API Management/Service/Gateways/token/Action | Hämtar autentiseringstoken för delad åtkomst för gatewayen. |
> | Åtgärd | Microsoft. API Management/Service/Gateway/Write | Skapar eller uppdaterar en gateway som ska användas i API Management-instansen. eller uppdaterar informationen om den gateway som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/getssotoken/åtgärd | Hämtar SSO-token som kan användas för att logga in på API Management tjänstens äldre portal som administratör |
> | Åtgärd | Microsoft. API Management/Service/Groups/Delete | Tar bort en angiven grupp av API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/Groups/Read | Visar en samling grupper som definierats inom en tjänst instans. eller hämtar information om den grupp som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Groups/Users/Delete | Ta bort befintlig användare från befintlig grupp. |
> | Åtgärd | Microsoft. API Management/Service/Groups/Users/Read | Visar en samling av användar enheter som är kopplade till gruppen. |
> | Åtgärd | Microsoft. API Management/Service/Groups/Users/Write | Lägg till befintlig användare i befintlig grupp |
> | Åtgärd | Microsoft. API Management/Service/Groups/Write | Skapar eller uppdaterar en grupp. eller uppdaterar detaljerna för den grupp som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Identityprovider/Delete | Tar bort den angivna konfigurationen för identitetsprovider. |
> | Åtgärd | Microsoft. API Management/Service/Identityprovider/listSecrets/Action | Hämtar hemligheter för identitetsprovider. |
> | Åtgärd | Microsoft. API Management/Service/Identityprovider/Read | Visar en samling identitets leverantörer som kon figurer ATS i den angivna tjänst instansen. eller hämtar konfigurations informationen för identitets leverantören utan hemligheter. |
> | Åtgärd | Microsoft. API Management/Service/Identityprovider/Write | Skapar eller uppdaterar IdentityProvider-konfigurationen. eller uppdaterar en befintlig IdentityProvider-konfiguration. |
> | Åtgärd | Microsoft. API Management/Service/Issues/Read | Visar en samling problem i den angivna tjänst instansen. eller får API Management problem information |
> | Åtgärd | Microsoft. API Management/Service/locations/networkstatus/Read | Hämtar nätverks åtkomst status för resurser som tjänsten är beroende av på platsen. |
> | Åtgärd | Microsoft. API Management/Service/loggar/ta bort | Tar bort den angivna loggaren. |
> | Åtgärd | Microsoft. API Management/Service/loggar/Läs | Visar en samling loggar i den angivna tjänst instansen. eller hämtar information om den loggning som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/loggar/Skriv | Skapar eller uppdaterar en loggare. eller uppdaterar en befintlig loggare. |
> | Åtgärd | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Managements tjänsten |
> | Åtgärd | Microsoft. API Management/Service/namedValues/Delete | Tar bort ett angivet namngivet värde från API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/namedValues/listSecrets/Action | Hämtar hemligheterna för det namngivna värdet som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/namedValues/Read | Visar en lista med namngivna värden som definierats inom en tjänst instans. eller hämtar information om det namngivna värde som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/namedValues/Write | Skapar eller uppdaterar ett namngivet värde. eller uppdaterar det angivna namngivna värdet. |
> | Åtgärd | Microsoft. API Management/Service/networkstatus/Read | Hämtar nätverks åtkomst status för resurser som tjänsten är beroende av. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/Action | Skickar ett meddelande till en angiven användare |
> | Åtgärd | Microsoft. API Management/Service/Notifications/Read | Listar en samling egenskaper som definierats inom en tjänst instans. eller hämtar information om meddelandet som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/recipientEmails/Delete | Tar bort e-postmeddelandet från listan över meddelanden. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/recipientEmails/Read | Hämtar listan över e-postmeddelandets mottagare som prenumererar på ett meddelande. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/recipientEmails/Write | Lägger till e-postadressen i listan över mottagare för aviseringen. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/recipientUsers/Delete | Tar bort API Management användaren från listan över meddelanden. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/recipientUsers/Read | Hämtar listan över meddelande mottagarens användare som prenumererar på meddelandet. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/recipientUsers/Write | Lägger till API Management användare i listan över mottagare för meddelandet. |
> | Åtgärd | Microsoft. API Management/Service/Notifications/Write | Skapa eller uppdatera API Management utgivar meddelande. |
> | Åtgärd | Microsoft. API Management/Service/openidConnectProviders/Delete | Tar bort en speciell OpenID Connect-Provider för API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/openidConnectProviders/listSecrets/Action | Hämtar vissa OpenID Connect-providerspecifika hemligheter. |
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/read | Listor över alla OpenId Connect-providrar. eller hämtar en speciell OpenID Connect-Provider utan hemligheter. |
> | Åtgärd | Microsoft. API Management/Service/openidConnectProviders/Write | Skapar eller uppdaterar OpenID Connect-providern. eller uppdaterar den angivna OpenID Connect-providern. |
> | Åtgärd | Microsoft. API Management/Service/operationresults/Read | Hämtar aktuell status för tids krävande åtgärd |
> | Åtgärd | Microsoft. API Management/Service/policies/Delete | Tar bort den globala princip konfigurationen för API Management-tjänsten. |
> | Åtgärd | Microsoft. API Management/Service/policies/Read | Visar en lista över alla globala princip definitioner för API Management-tjänsten. eller hämta den globala princip definitionen för API Management-tjänsten. |
> | Åtgärd | Microsoft. API Management/Service/policies/Write | Skapar eller uppdaterar den globala princip konfigurationen för API Management-tjänsten. |
> | Åtgärd | Microsoft. API Management/Service/policy/Delete | Ta bort princip konfigurationen på klient nivå |
> | Åtgärd | Microsoft. API Management/Service/policy/Read | Hämta princip konfigurationen på klient nivå |
> | Åtgärd | Microsoft. API Management/Service/policy/Write | Skapa princip konfiguration på klient nivå |
> | Åtgärd | Microsoft. API Management/Service/policyDescriptions/Read | Visar en lista över alla princip beskrivningar. |
> | Åtgärd | Microsoft. API Management/Service/policySnippets/Read | Visar en lista över alla princip fragment. |
> | Åtgärd | Microsoft.ApiManagement/service/portalsettings/read | Få inloggnings inställningar för portalen eller få registrerings inställningar för portalen eller hämta Delegerings inställningar för portalen. |
> | Åtgärd | Microsoft. API Management/Service/portalsettings/Write | Uppdatera inloggnings inställningar. eller skapa eller uppdatera inloggnings inställningar. eller uppdatera registrerings inställningarna eller uppdatera inställningarna för registrering eller uppdatera delegering. eller skapa eller uppdatera Delegerings inställningar. |
> | Åtgärd | Microsoft. API Management/Service/Products/API/Delete | Tar bort angivet API från den angivna produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/API/Read | Visar en samling med API: er som är associerade med en produkt. |
> | Åtgärd | Microsoft. API Management/Service/Products/API/Write | Lägger till ett API till den angivna produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/Delete | Ta bort produkt. |
> | Åtgärd | Microsoft. API Management/Service/Products/Groups/Delete | Tar bort associationen mellan den angivna gruppen och produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/Groups/Read | Visar en lista med de utvecklings grupper som är associerade med den angivna produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/Groups/Write | Lägger till associationen mellan den angivna gruppen med utvecklare med den angivna produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/policies/Delete | Tar bort princip konfigurationen på produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/policies/Read | Hämta princip konfigurationen på produkt nivå. eller hämta princip konfigurationen på produkt nivå. |
> | Åtgärd | Microsoft. API Management/Service/Products/policies/Write | Skapar eller uppdaterar princip konfigurationen för produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/policy/Delete | Ta bort princip konfigurationen på produkt nivå |
> | Åtgärd | Microsoft. API Management/Service/Products/policy/Read | Hämta princip konfigurationen på produkt nivå |
> | Åtgärd | Microsoft. API Management/Service/Products/policy/Write | Skapa princip konfiguration på produkt nivå |
> | Åtgärd | Microsoft. API Management/Service/Products/Read | Listar en samling produkter i den angivna tjänst instansen. eller hämtar information om den produkt som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Products/Subscriptions/Read | Visar en lista med prenumerationer på den angivna produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/Tags/Delete | Koppla bort taggen från produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/taggs/Read | Visar en lista över alla Taggar som är associerade med produkten. eller hämta taggen som är associerad med produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/Tags/Write | Tilldela en tagg till produkten. |
> | Åtgärd | Microsoft. API Management/Service/Products/Write | Skapar eller uppdaterar en produkt. eller uppdatera befintlig produkt information. |
> | Åtgärd | Microsoft. API Management/Service/productsByTags/Read | Listar en samling produkter som är associerade med taggar. |
> | Åtgärd | Microsoft. API Management/Service/Properties/Delete | Tar bort en angiven egenskap från API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/Properties/listSecrets/Action | Hämtar hemligheterna för den egenskap som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Properties/Read | Listar en samling egenskaper som definierats inom en tjänst instans. eller hämtar information om den egenskap som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Properties/Write | Skapar eller uppdaterar en egenskap. eller uppdaterar den angivna egenskapen. |
> | Åtgärd | Microsoft. API Management/Service/kvoter/perioder/Läs | Hämta kvot räknar värde för period |
> | Åtgärd | Microsoft. API Management/Service/kvoter/perioder/skrivning | Ange aktuellt värde för kvot räknare |
> | Åtgärd | Microsoft. API Management/Service/kvoter/läsa | Hämta värden för kvot |
> | Åtgärd | Microsoft. API Management/Service/kvoter/skrivning | Ange aktuellt värde för kvot räknare |
> | Åtgärd | Microsoft.ApiManagement/service/read | Läs metadata för en API Management tjänst instans |
> | Åtgärd | Microsoft. API Management/Service/region/läsa | Visar en lista över alla Azure-regioner där tjänsten finns. |
> | Åtgärd | Microsoft. API Management/Service/Reports/Read | Hämta rapport sammanställd per tids period eller hämta rapport som aggregerats per geografisk region eller hämta rapport aggregerade av utvecklare.<br>eller hämta rapporten sammanställt av produkter.<br>eller hämta rapport som aggregerats av API: er eller hämta rapporten sammanställt av åtgärder eller hämta rapport sammanställt av prenumerationen.<br>eller hämta rapporterings data för förfrågningar |
> | Åtgärd | Microsoft.ApiManagement/service/restore/action | Återställa API Management tjänst från den angivna behållaren i ett användardefinierat lagrings konto |
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/Delete | Tar bort den angivna prenumerationen. |
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/listSecrets/Action | Hämtar de angivna prenumerations nycklarna. |
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/Read | Visar en lista över alla prenumerationer för API Management tjänst instansen. eller hämtar den angivna prenumerations enheten (utan nycklar). |
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/regeneratePrimaryKey/Action | Återskapar den primära nyckeln för en befintlig prenumeration på den API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/regenerateSecondaryKey/Action | Återskapar sekundär nyckel för befintlig prenumeration på API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/Write | Skapar eller uppdaterar prenumerationen för angiven användare till den angivna produkten. eller uppdaterar informationen om en prenumeration som anges med dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/tagResources/Read | Visar en samling resurser som är associerade med taggar. |
> | Åtgärd | Microsoft. API Management/Service/Tags/Delete | Tar bort en speciell tagg för API Management tjänst instansen. |
> | Åtgärd | Microsoft. API Management/Service/Tags/Read | Visar en uppsättning taggar som definierats inom en tjänst instans. eller hämtar information om taggen som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Tags/Write | Skapar en tagg. eller uppdaterar informationen om den tagg som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Templates/Delete | Återställ standard API Management e-postmall |
> | Åtgärd | Microsoft. API Management/Service/Templates/Read | Hämtar alla e-postmallar eller hämtar information om API Management e-postmall |
> | Åtgärd | Microsoft. API Management/Service/Templates/Write | Skapa eller uppdatera API Management e-postmall eller uppdateringar API Management e-postmall |
> | Åtgärd | Microsoft. API Management/Service/Tenant/Delete | Ta bort princip konfigurationen för klient organisationen |
> | Åtgärd | Microsoft. API Management/Service/klient/distribuera/åtgärd | Kör en distributions aktivitet för att tillämpa ändringarna från den angivna git-grenen till konfigurationen i databasen. |
> | Åtgärd | Microsoft. API Management/Service/Tenant/listSecrets/åtgärd | Hämta information om klient åtkomst information |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/operationResults/read | Hämta en lista över åtgärds resultat eller hämta resultatet för en speciell åtgärd |
> | Åtgärd | Microsoft. API Management/Service/Tenant/Read | Hämta den globala princip definitionen för API Management-tjänsten. eller hämta information om klient åtkomst information |
> | Åtgärd | Microsoft. API Management/Service/Tenant/regeneratePrimaryKey/åtgärd | Återskapa primär åtkomst nyckel |
> | Åtgärd | Microsoft. API Management/Service/Tenant/regenerateSecondaryKey/åtgärd | Återskapa sekundär åtkomst nyckel |
> | Åtgärd | Microsoft. API Management/Service/klient/spara/åtgärd | Skapar bekräftelse med konfigurations ögonblicks bild till den angivna grenen i databasen |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/syncState/read | Hämta status för senaste git-synkronisering |
> | Åtgärd | Microsoft. API Management/Service/klient/verifiera/åtgärd | Verifierar ändringar från angiven git-gren |
> | Åtgärd | Microsoft.ApiManagement/service/tenant/write | Ange princip konfiguration för klient organisations-eller uppdaterings information om klient åtkomst |
> | Åtgärd | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikatet för en API Management-tjänst |
> | Åtgärd | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domän namn för en API Management-tjänst |
> | Åtgärd | Microsoft. API Management/Service/Users/Action | Registrera en ny användare |
> | Åtgärd | Microsoft. API Management/Service/Users/CONFIRMS/Send/Action | Skickar bekräftelse |
> | Åtgärd | Microsoft. API Management/Service/Users/Delete | Tar bort en speciell användare. |
> | Åtgärd | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Hämtar en URL för omdirigering som innehåller en autentiseringstoken för att signera en viss användare till Developer-portalen. |
> | Åtgärd | Microsoft. API Management/Service/Users/Groups/Read | Visar en lista över alla användar grupper. |
> | Åtgärd | Microsoft. API Management/Service/Users/Identities/Read | Lista över alla användar identiteter. |
> | Åtgärd | Microsoft.ApiManagement/service/users/keys/read | Hämta nycklar kopplade till användaren |
> | Åtgärd | Microsoft. API Management/Service/Users/Read | Visar en samling registrerade användare i den angivna tjänst instansen. eller hämtar information om den användare som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/Users/Subscriptions/Read | Visar en lista med prenumerationer för den angivna användaren. |
> | Åtgärd | Microsoft. API Management/Service/Users/token/Action | Hämtar autentiseringstoken för delad åtkomst för användaren. |
> | Åtgärd | Microsoft. API Management/Service/Users/Write | Skapar eller uppdaterar en användare. eller uppdaterar informationen om den användare som anges av dess identifierare. |
> | Åtgärd | Microsoft.ApiManagement/service/write | Skapa en ny instans av API Management-tjänsten |
> | Åtgärd | Microsoft.ApiManagement/unregister/action | Avregistrera prenumerationen för Microsoft. API Management Resource Provider |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Authorization/classicAdministrators/Delete | Tar bort administratören från prenumerationen. |
> | Åtgärd | Microsoft. Authorization/classicAdministrators/operationstatuses/Read | Hämtar prenumerationens administratörs åtgärds status. |
> | Åtgärd | Microsoft. Authorization/classicAdministrators/Read | Läser prenumerationens administratörer. |
> | Åtgärd | Microsoft. Authorization/classicAdministrators/Write | Lägg till eller ändra administratör för en prenumeration. |
> | Åtgärd | Microsoft. Authorization/denyAssignments/Delete | Ta bort en neka-tilldelning i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/denyAssignments/Read | Hämta information om en neka-tilldelning. |
> | Åtgärd | Microsoft. Authorization/denyAssignments/Write | Skapa en neka-tilldelning i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/elevateAccess/åtgärd | Beviljar anroparen Användaråtkomst Administratörsåtkomst för klientomfånget |
> | Åtgärd | Microsoft. Authorization/lock/Delete | Ta bort lås i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/lock/Read | Hämtar lås i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/lock/Write | Lägg till lås i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/Operations/Read | Hämtar listan över åtgärder |
> | Åtgärd | Microsoft. Authorization/Permissions/Read | Visar en lista över alla behörigheter som anroparen har inom ett angivet omfång. |
> | Åtgärd | Microsoft. authorization/policies/audit/Action | Åtgärder som vidtas till följd av utvärdering av Azure Policy med gransknings verkan |
> | Åtgärd | Microsoft. Authorization/policys/auditIfNotExists/Action | Åtgärder som vidtas till följd av utvärdering av Azure Policy med "auditIfNotExists"-inverkan |
> | Åtgärd | Microsoft. Authorization/policys/Deny/åtgärd | Åtgärder som vidtas till följd av utvärdering av Azure Policy med "Neka"-inverkan |
> | Åtgärd | Microsoft. Authorization/policys/deployIfNotExists/Action | Åtgärder som vidtas till följd av utvärdering av Azure Policy med "deployIfNotExists"-inverkan |
> | Åtgärd | Microsoft. Authorization/policyAssignments/Delete | Ta bort en princip tilldelning i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/policyAssignments/Read | Hämta information om en princip tilldelning. |
> | Åtgärd | Microsoft. Authorization/policyAssignments/Write | Skapa en princip tilldelning i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/policyDefinitions/Delete | Ta bort en princip definition. |
> | Åtgärd | Microsoft. Authorization/policyDefinitions/Read | Hämta information om en princip definition. |
> | Åtgärd | Microsoft. Authorization/policyDefinitions/Write | Skapa en anpassad princip definition. |
> | Åtgärd | Microsoft. Authorization/policySetDefinitions/Delete | Ta bort en princip uppsättnings definition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/read | Hämta information om en princip uppsättnings definition. |
> | Åtgärd | Microsoft.Authorization/policySetDefinitions/write | Skapa en anpassad princip uppsättnings definition. |
> | Åtgärd | Microsoft. Authorization/providerOperations/Read | Hämta åtgärder för alla resurs leverantörer som kan användas i roll definitioner. |
> | Åtgärd | Microsoft. Authorization/roleAssignments/Delete | Ta bort en roll tilldelning i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/roleAssignments/Read | Hämta information om en roll tilldelning. |
> | Åtgärd | Microsoft.Authorization/roleAssignments/write | Skapa en roll tilldelning i det angivna omfånget. |
> | Åtgärd | Microsoft. Authorization/roleDefinitions/Delete | Ta bort den angivna anpassade roll definitionen. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/read | Hämta information om en roll definition. |
> | Åtgärd | Microsoft.Authorization/roleDefinitions/write | Skapa eller uppdatera en anpassad roll definition med angivna behörigheter och tilldelnings bara omfång. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Läs en Azure Automation DSCs registrerings information |
> | Åtgärd | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Skriver en begäran om att återskapa Azure Automation DSC-nycklar |
> | Åtgärd | Microsoft. Automation/automationAccounts/certificates/Delete | Tar bort en Azure Automation certifikats till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/certificates/getCount/Action | Läser antalet certifikat |
> | Åtgärd | Microsoft. Automation/automationAccounts/certifikat/läsa | Hämtar en Azure Automation certifikat till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/certificates/Write | Skapar eller uppdaterar en Azure Automation certifikat till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/compilationjobs/Read | Läser en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft. Automation/automationAccounts/compilationjobs/Read | Läser en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft. Automation/automationAccounts/compilationjobs/Write | Skriver en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft. Automation/automationAccounts/compilationjobs/Write | Skriver en Azure Automation DSC-kompilering |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/content/read | Läser innehållet i konfigurations mediet |
> | Åtgärd | Microsoft. Automation/automationAccounts/Configurations/Delete | Tar bort ett Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/configurations/getCount/action | Läser antalet Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft. Automation/automationAccounts/konfigurationer/läsa | Hämtar ett Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft. Automation/automationAccounts/konfigurationer/Skriv | Skriver en Azure Automation DSC-innehåll |
> | Åtgärd | Microsoft. Automation/automationAccounts/Connections/Delete | Tar bort en Azure Automation anslutnings till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/Connections/getCount/Action | Läser antalet anslutningar |
> | Åtgärd | Microsoft. Automation/automationAccounts/Connections/Read | Hämtar en Azure Automation anslutnings till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/Connections/Write | Skapar eller uppdaterar en Azure Automation anslutnings till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/connectionTypes/Delete | Tar bort en Azure Automation Anslutnings typ till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/connectionTypes/Read | Hämtar en Azure Automation-tillgång av typen anslutning |
> | Åtgärd | Microsoft. Automation/automationAccounts/connectionTypes/Write | Skapar en Azure Automation Anslutnings typ till gång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/delete | Tar bort en Azure Automation Credential-till gång |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/getCount/action | Läser antalet autentiseringsuppgifter |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/read | Hämtar en Azure Automation till gång till autentiseringsuppgifter |
> | Åtgärd | Microsoft.Automation/automationAccounts/credentials/write | Skapar eller uppdaterar en Azure Automation Credential-till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/Delete | Tar bort ett Azure Automation konto |
> | Åtgärd | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Delete | Tar bort Hybrid Runbook Worker resurser |
> | Åtgärd | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker resurser |
> | Åtgärd | Microsoft. Automation/automationAccounts/Jobs/utdata/Read | Hämtar utdata för ett jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/read | Hämtar ett Azure Automation jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation jobb |
> | Åtgärd | Microsoft. Automation/automationAccounts/Jobs/runbookContent/åtgärd | Hämtar innehållet i Azure Automation Runbook vid tidpunkten för jobb körningen |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobb ström |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobb ström |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation jobb |
> | Åtgärd | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Tar bort ett Azure Automation jobb schema |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation jobb schema |
> | Åtgärd | Microsoft.Automation/automationAccounts/jobSchedules/write | Skapar ett Azure Automation jobb schema |
> | Åtgärd | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar arbets ytan som är länkad till Automation-kontot |
> | Åtgärd | Microsoft. Automation/automationAccounts/Listnycklar/Action | Läser nycklarna för Automation-kontot |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/activities/read | Hämtar Azure Automation-aktiviteter |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/delete | Tar bort en Azure Automation PowerShell-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/getCount/action | Hämtar antalet PowerShell-moduler i Automation-kontot |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/read | Hämtar en Azure Automation PowerShell-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/modules/write | Skapar eller uppdaterar en Azure Automation PowerShell-modul |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Tar bort en Azure Automation DSC-nods konfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Läser en Azure Automation DSC-nodens konfigurations innehåll |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Läser en konfiguration av Azure Automation DSC-noden |
> | Åtgärd | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Skriver en konfiguration av en Azure Automation DSC-nod |
> | Åtgärd | Microsoft. Automation/automationAccounts/nodecounts/Read | Läser Sammanfattning av antal noder för den angivna typen |
> | Åtgärd | Microsoft. Automation/automationAccounts/Nodes/Delete | Tar bort Azure Automation DSC-noder |
> | Åtgärd | Microsoft. Automation/automationAccounts/Nodes/Read | Läser Azure Automation DSC-noder |
> | Åtgärd | Microsoft. Automation/automationAccounts/Nodes/Reports/Content/Read | Läser Azure Automation DSC-rapportens innehåll |
> | Åtgärd | Microsoft. Automation/automationAccounts/Nodes/Reports/Read | Läser Azure Automation DSC-rapporter |
> | Åtgärd | Microsoft. Automation/automationAccounts/Nodes/Write | Skapar eller uppdaterar Azure Automation DSC-noder |
> | Åtgärd | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/Read | Hämtar Azure Automation TypeFields |
> | Åtgärd | Microsoft.Automation/automationAccounts/python2Packages/delete | Tar bort ett Azure Automation python 2-paket |
> | Åtgärd | Microsoft. Automation/automationAccounts/python2Packages/Read | Hämtar ett Azure Automation python 2-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python2Packages/write | Skapar eller uppdaterar ett Azure Automation python 2-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python3Packages/delete | Tar bort ett Azure Automation python 3-paket |
> | Åtgärd | Microsoft. Automation/automationAccounts/python3Packages/Read | Hämtar ett Azure Automation python 3-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/python3Packages/write | Skapar eller uppdaterar ett Azure Automation python 3-paket |
> | Åtgärd | Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation konto |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/innehåll/läsa | Hämtar innehållet i en Azure Automation Runbook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/Delete | Tar bort en Azure Automation Runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Skapar innehållet i ett utkast till en Azure Automation Runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Hämtar Azure Automation åtgärds resultat för Runbook-utkast |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/Draft/Read | Hämtar ett utkast till en Azure Automation Runbook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Read | Hämtar ett utkast till test jobb för Runbook-Azure Automation |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Återupptar ett utkast till test jobb för en Azure Automation Runbook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/stopp/åtgärd | Stoppar Azure Automation ett utkast till test jobb för Runbook-utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Pausar ett utkast till test jobb för en Azure Automation Runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Skapar Azure Automation ett utkast till test jobb för Runbook-utkast |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Ångra redigeringar i ett utkast till Azure Automation Runbook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/getCount/Action | Hämtar antalet Azure Automation runbooks |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publicerar ett utkast till en Azure Automation Runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation Runbook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Runbooks/Write | Skapar eller uppdaterar en Azure Automation Runbook |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/delete | Tar bort en Azure Automation schema till gång |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/getCount/action | Hämtar antalet Azure Automation scheman |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation schema till gång |
> | Åtgärd | Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation schema till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationMachineRuns/Read | Hämtar en Azure Automation dator för att köra program uppdaterings konfiguration |
> | Åtgärd | Microsoft. Automation/automationAccounts/softwareUpdateConfigurationRuns/Read | Hämtar en Azure Automation körning av konfiguration av program uppdatering |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Tar bort en Azure Automation konfiguration för program uppdatering |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Tar bort en Azure Automation konfiguration för program uppdatering |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Hämtar en Azure Automation konfiguration för program uppdatering |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Hämtar en Azure Automation konfiguration för program uppdatering |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Skapar eller uppdaterar Azure Automation program uppdaterings konfiguration |
> | Åtgärd | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Skapar eller uppdaterar Azure Automation program uppdaterings konfiguration |
> | Åtgärd | Microsoft. Automation/automationAccounts/Statistics/Read | Hämtar Azure Automation statistik |
> | Åtgärd | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Hämta en Azure Automation distributions dator för uppdateringar |
> | Åtgärd | Microsoft. Automation/automationAccounts/updateManagementPatchJob/Read | Hämtar ett uppdaterings jobb för Azure Automation uppdaterings hantering |
> | Åtgärd | Microsoft. Automation/automationAccounts/Usage/Read | Hämtar Azure Automation användning |
> | Åtgärd | Microsoft.Automation/automationAccounts/variables/delete | Tar bort en Azure Automation variabel till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/variabler/läsa | Läser en Azure Automation variabel till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/variabler/Write | Skapar eller uppdaterar en Azure Automation variabel till gång |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/Delete | Ta bort ett Azure Automation Watcher-jobb |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/Read | Hämtar ett Azure Automation Watcher-jobb |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/start/åtgärd | Starta ett Azure Automation Watcher-jobb |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/stoppa/åtgärd | Stoppa ett Azure Automation Watcher-jobb |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/Streams/Read | Hämtar en Azure Automation övervakare jobb ström |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/watcherActions/Delete | Ta bort ett Azure Automation övervaknings jobb åtgärder |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/watcherActions/Read | Hämtar en Azure Automation övervakare jobb åtgärder |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/watcherActions/Write | Skapa ett Azure Automation övervaknings jobb åtgärder |
> | Åtgärd | Microsoft. Automation/automationAccounts/Watcher/Write | Skapar ett Azure Automation Watcher-jobb |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/action | Genererar en URI för en Azure Automation-webhook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Webhooks/Delete | Tar bort en Azure Automation-webhook  |
> | Åtgärd | Microsoft. Automation/automationAccounts/Webhooks/Read | Läser en Azure Automation webhook |
> | Åtgärd | Microsoft.Automation/automationAccounts/webhooks/write | Skapar eller uppdaterar en Azure Automation webhook |
> | Åtgärd | Microsoft. Automation/automationAccounts/Write | Skapar eller uppdaterar ett Azure Automation konto |
> | Åtgärd | Microsoft. Automation/Operations/Read | Hämtar tillgängliga åtgärder för Azure Automation resurser |
> | Åtgärd | Microsoft. Automation/register/åtgärd | Registrerar prenumerationen på Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Ta bort B2C Directory-resurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visa B2C Directory-resurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2cDirectories/write | Skapa eller uppdatera B2C Directory-resurs |
> | Åtgärd | Microsoft.AzureActiveDirectory/b2ctenants/read | Visar en lista över alla B2C-klienter där användaren är medlem |
> | Åtgärd | Microsoft.AzureActiveDirectory/operations/read | Läs alla API-åtgärder som är tillgängliga för Microsoft. AzureActiveDirectory Resource Provider |
> | Åtgärd | Microsoft. AzureActiveDirectory/register/åtgärd | Registrera prenumeration för Microsoft. AzureActiveDirectory Resource Provider |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.AzureStack/Operations/read | Hämtar egenskaper för en resurs leverantörs åtgärd |
> | Åtgärd | Microsoft.AzureStack/register/action | Registrerar prenumeration med Microsoft. AzureStack Resource Provider |
> | Åtgärd | Microsoft. AzureStack/Registration/customerSubscriptions/Delete | Tar bort en Azure Stack kund prenumeration |
> | Åtgärd | Microsoft. AzureStack/registreringar/customerSubscriptions/Read | Hämtar egenskaperna för en Azure Stack kund prenumeration |
> | Åtgärd | Microsoft.AzureStack/registrations/customerSubscriptions/write | Skapar eller uppdaterar en Azure Stack kund prenumeration |
> | Åtgärd | Microsoft.AzureStack/registrations/delete | Tar bort en Azure Stack registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/getActivationKey/action | Hämtar den senaste Azure Stack aktiverings nyckeln |
> | Åtgärd | Microsoft. AzureStack/registreringar/produkter/getProduct/åtgärd | Hämtar Azure Stack Marketplace-produkt |
> | Åtgärd | Microsoft. AzureStack/registreringar/produkter/getProducts/åtgärd | Hämtar en lista över Azure Stack Marketplace-produkter |
> | Åtgärd | Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökad information för en Azure Stack Marketplace-produkt |
> | Åtgärd | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | Åtgärd | Microsoft. AzureStack/registreringar/produkter/uploadProductLog/åtgärd | Registrera Azure Stack Marketplace produkt åtgärds status och tidsstämpel |
> | Åtgärd | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure Stack registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/write | Skapar eller uppdaterar en Azure Stack registrering |
> | Åtgärd | Microsoft. AzureStack/verificationKeys/getCurrentKey/Action | Hämtar den aktuella versionen av Azure Stack offentlig nyckel för signering |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. batch/batchAccounts/Applications/Delete | Tar bort ett program |
> | Åtgärd | Microsoft. batch/batchAccounts/program/läsa | Visar en lista över program eller hämtar egenskaperna för ett program |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktiverar ett programpaket |
> | Åtgärd | Microsoft. batch/batchAccounts/Applications/versions/Delete | Tar bort ett programpaket |
> | Åtgärd | Microsoft. batch/batchAccounts/Applications/versions/Read | Hämtar egenskaperna för ett programpaket |
> | Åtgärd | Microsoft.Batch/batchAccounts/applications/versions/write | Skapar ett nytt programpaket eller uppdaterar ett befintligt programpaket |
> | Åtgärd | Microsoft. batch/batchAccounts/program/Skriv | Skapar ett nytt program eller uppdaterar ett befintligt program |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Hämtar resultatet av en tids krävande certifikat åtgärd på ett batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Avbryter den misslyckade borttagningen av ett certifikat på ett batch-konto |
> | Åtgärd | Microsoft. batch/batchAccounts/certifikat/ta bort | Tar bort ett certifikat från ett batch-konto |
> | Åtgärd | Microsoft. batch/batchAccounts/certifikat/läsa | Visar en lista över certifikat på ett batch-konto eller hämtar egenskaperna för ett certifikat |
> | Åtgärd | Microsoft. batch/batchAccounts/certifikat/skriva | Skapar ett nytt certifikat för ett batch-konto eller uppdaterar ett befintligt certifikat |
> | Åtgärd | Microsoft. batch/batchAccounts/Delete | Tar bort ett batch-konto |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Delete | Tar bort ett jobb från ett batch-konto |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Read | Visar en lista med jobb på ett batch-konto eller hämtar egenskaperna för ett jobb |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Write | Skapar ett nytt jobb på ett batch-konto eller uppdaterar ett befintligt jobb |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Tar bort ett jobb schema från ett batch-konto |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Read | Visar en lista med jobb scheman på ett batch-konto eller hämtar egenskaperna för ett jobb schema |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Write | Skapar ett nytt jobb schema för ett batch-konto eller uppdaterar ett befintligt jobb schema |
> | Åtgärd | Microsoft. batch/batchAccounts/listnycklar/Action | Listar åtkomst nycklar för ett batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/operationResults/read | Hämtar resultatet från en tids krävande batch-konto-åtgärd |
> | Åtgärd | Microsoft.Batch/batchAccounts/poolOperationResults/read | Hämtar resultatet från en långvarig åtgärd för poolen i ett batch-konto |
> | Åtgärd | Microsoft. batch/batchAccounts/pooler/ta bort | Tar bort en pool från ett batch-konto |
> | Åtgärd | Microsoft. batch/batchAccounts/pooler/disableAutoscale/åtgärd | Inaktiverar automatisk skalning för en batch-adresspool |
> | Åtgärd | Microsoft. batch/batchAccounts/pooler/läsa | Listar pooler på ett batch-konto eller hämtar egenskaperna för en pool |
> | Åtgärd | Microsoft. batch/batchAccounts/pooler/stopResize/åtgärd | Stoppar en pågående storleks ändring i en batch-adresspool |
> | Åtgärd | Microsoft. batch/batchAccounts/pooler/Skriv | Skapar en ny pool på ett batch-konto eller uppdaterar en befintlig pool |
> | Åtgärd | Microsoft. batch/batchAccounts/Read | Visar en lista med batch-konton eller hämtar egenskaperna för ett batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/regeneratekeys/action | Återskapar åtkomst nycklar för ett batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synkroniserar åtkomst nycklar för det automatiska lagrings kontot som kon figurer ATS för ett batch-konto |
> | Åtgärd | Microsoft.Batch/batchAccounts/write | Skapar ett nytt batch-konto eller uppdaterar ett befintligt batch-konto |
> | Åtgärd | Microsoft.Batch/locations/accountOperationResults/read | Hämtar resultatet från en tids krävande batch-konto-åtgärd |
> | Åtgärd | Microsoft.Batch/locations/checkNameAvailability/action | Kontrollerar att konto namnet är giltigt och inte används. |
> | Åtgärd | Microsoft. batch/platser/kvoter/läsa | Hämtar batch-kvoter för den angivna prenumerationen på den angivna Azure-regionen |
> | Åtgärd | Microsoft. batch/åtgärder/Läs | Visar en lista över åtgärder som är tillgängliga på Microsoft. batch Resource Provider |
> | Åtgärd | Microsoft. batch/register/åtgärd | Registrerar prenumerationen för batch Resource providern och gör det möjligt att skapa batch-konton |
> | Åtgärd | Microsoft.Batch/unregister/action | Avregistrerar prenumerationen för batch Resource providern och förhindrar att batch-konton skapas |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. fakturering/billingAccounts/avtal/Läs |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingPermissions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/billingPermissions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/kunder/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/fakturor/pris dokument/hämtning/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/billingPermissions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/Move/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/transfer/Action |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/billingSubscriptions/validateMoveEligibility/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/produkter/flytta/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/produkter/överföring/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/Products/validateMoveEligibility/Action |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/invoiceSections/Write |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/pris dokument/hämtning/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/Write |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingProfiles/Write |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/billingSubscriptions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/kunder/billingPermissions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/kunder/Läs |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/avdelningar/läsa |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/enrollmentAccounts/billingPermissions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/enrollmentAccounts/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/enrollmentDepartments/billingPermissions/Read |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/listInvoiceSectionsWithCreateSubscriptionPermission/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/produkter/läsa |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/Läs |  |
> | Åtgärd | Microsoft. fakturering/billingAccounts/skrivning |  |
> | Åtgärd | Microsoft. fakturering/avdelningar/läsa |  |
> | Åtgärd | Microsoft. fakturering/fakturor/Hämta/åtgärd | Ladda ned faktura med Hämta länk från lista |
> | Åtgärd | Microsoft. fakturering/fakturor/läsa |  |
> | Åtgärd | Microsoft. fakturering/register/åtgärd |  |
> | Åtgärd | Microsoft. fakturering/validateAddress/åtgärd |  |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.BingMaps/mapApis/Delete | Ta bort åtgärd |
> | Åtgärd | Microsoft.BingMaps/mapApis/listSecrets/action | Visa en lista över hemligheterna |
> | Åtgärd | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Läs token för enkel inloggning för resursen |
> | Åtgärd | Microsoft. Bingkartssökning/mapApis/Read | Läs åtgärd |
> | Åtgärd | Microsoft. Bingkartssökning/mapApis/regenerateKey/Action | Återskapar nyckeln |
> | Åtgärd | Microsoft.BingMaps/mapApis/Write | Skriv åtgärd |
> | Åtgärd | Microsoft.BingMaps/Operations/read | Beskrivning av åtgärden. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. blockchain/blockchainMembers/Delete | Tar bort en befintlig blockchain-medlem. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Hämtar eller visar befintliga blockchain-medlems API-nycklar. |
> | Åtgärd | Microsoft. blockchain/blockchainMembers/Read | Hämtar eller visar en lista över befintliga blockchain-medlemmar. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Ansluter till en blockchain. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Tar bort en befintlig blockchain-medlems transaktions nod. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Hämtar eller visar befintliga blockchain för medlems transaktioner i noden. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hämtar eller visar befintliga blockchain för medlems transaktioner. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Skapar eller uppdaterar en blockchain-medlems transaktions nod. |
> | Åtgärd | Microsoft.Blockchain/blockchainMembers/write | Skapar eller uppdaterar en blockchain-medlem. |
> | Åtgärd | Microsoft. blockchain/cordaMembers/Delete | Tar bort en befintlig blockchain-kabel-medlem. |
> | Åtgärd | Microsoft. blockchain/cordaMembers/Read | Hämtar eller visar befintliga blockchain-sladdar. |
> | Åtgärd | Microsoft. blockchain/cordaMembers/Write | Skapar eller uppdaterar en blockchain-Kabele-medlem. |
> | Åtgärd | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Hämtar åtgärds resultatet för blockchain-medlemmar. |
> | Åtgärd | Microsoft.Blockchain/locations/checkNameAvailability/action | Kontrollerar att resurs namnet är giltigt och inte används. |
> | Åtgärd | Microsoft. blockchain/Operations/Read | Visa en lista med alla åtgärder i Microsoft blockchain Resource Provider. |
> | Åtgärd | Microsoft. blockchain/register/åtgärd | Registrerar prenumerationen för blockchain-resurs leverantören. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/assignmentOperations/Read | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/Delete | Ta bort eventuella skissartefakter |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/Read | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/whoisblueprint/Action | Hämta objekt-ID för tjänstens huvud namn för Azure-ritningar. |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/Write | Skapa eller uppdatera eventuella skissartefakter |
> | Åtgärd | Microsoft. skiss/skisser/artefakter/ta bort | Ta bort eventuella skissartefakter |
> | Åtgärd | Microsoft. skiss/skisser/artefakter/läsning | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/skisser/artefakter/skrivning | Skapa eller uppdatera eventuella skissartefakter |
> | Åtgärd | Microsoft. skiss/skiss/ta bort | Ta bort alla ritningar |
> | Åtgärd | Microsoft. skiss/skiss/läsa | Läs eventuella skisser |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/artifacts/read | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/delete | Ta bort alla ritningar |
> | Åtgärd | Microsoft.Blueprint/blueprints/versions/read | Läs eventuella skisser |
> | Åtgärd | Microsoft. skiss/skisser/versioner/skriva | Skapa eller uppdatera eventuella skisser |
> | Åtgärd | Microsoft. skiss/skiss/skriva | Skapa eller uppdatera eventuella skisser |
> | Åtgärd | Microsoft. skiss/registrera/åtgärd | Registrerar resurs leverantören för Azure-ritningar |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. BotService/botServices/Channels/Delete | Ta bort en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Channels/Read | Läs en bot-tjänst |
> | Åtgärd | Microsoft.BotService/botServices/channels/write | Skriv en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Connections/Delete | Ta bort en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Connections/Read | Läs en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Connections/Write | Skriv en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Delete | Ta bort en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Read | Läs en bot-tjänst |
> | Åtgärd | Microsoft. BotService/botServices/Write | Skriv en bot-tjänst |
> | Åtgärd | Microsoft. BotService/locations/operationresults/Read | Läs status för en asynkron åtgärd |
> | Åtgärd | Microsoft. BotService/Operations/Read | Läs åtgärder för alla resurs typer |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. cache/checknameavailability/åtgärd | Kontrollerar om ett namn är tillgängligt för användning med en ny Redis Cache |
> | Åtgärd | Microsoft. cache/locations/operationresults/Read | Hämtar resultatet av en tids krävande åtgärd för vilken "location"-huvudet tidigare returnerades till klienten |
> | Åtgärd | Microsoft. cache/åtgärder/Läs | Visar en lista över de åtgärder som stöds av Microsoft. cache-providern. |
> | Åtgärd | Microsoft. cache/Redis/Delete | Ta bort hela Redis Cache |
> | Åtgärd | Microsoft.Cache/redis/export/action | Exportera Redis-data till fasta lagrings blobbar i angivet format |
> | Åtgärd | Microsoft. cache/Redis/firewallRules/Delete | Ta bort IP-brandväggens regler för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/firewallRules/Read | Hämta IP-brandväggens regler för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/firewallRules/Write | Redigera IP-brandväggens regler för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/forceReboot/åtgärd | Framtvinga omstart av en cache-instans, eventuellt med data förlust. |
> | Åtgärd | Microsoft. cache/Redis/import/åtgärd | Importera data från ett angivet format från flera blobbar till Redis |
> | Åtgärd | Microsoft. cache/Redis/linkedservers/Delete | Ta bort den länkade servern från en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/linkedservers/Read | Hämta länkade servrar som är associerade med en Redis-cache. |
> | Åtgärd | Microsoft. cache/Redis/linkedservers/Write | Lägg till den länkade servern i en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/Listnycklar/åtgärd | Visa värdet för Redis Cache åtkomst nycklar i hanterings portalen |
> | Åtgärd | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista de senaste uppgraderings meddelandena för cache-klienten. |
> | Åtgärd | Microsoft. cache/Redis/metricDefinitions/Read | Hämtar tillgängliga mått för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/patchSchedules/Delete | Ta bort uppdaterings schema för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/patchSchedules/Read | Hämtar uppdaterings schema för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/patchSchedules/Write | Ändra uppdaterings schema för en Redis Cache |
> | Åtgärd | Microsoft. cache/Redis/Read | Visa Redis Cacheens inställningar och konfiguration i hanterings portalen |
> | Åtgärd | Microsoft.Cache/redis/regenerateKey/action | Ändra värdet för Redis Cache åtkomst nycklar i hanterings portalen |
> | Åtgärd | Microsoft. cache/Redis/start/Action | Starta en cache-instans. |
> | Åtgärd | Microsoft. cache/Redis/stoppa/åtgärd | Stoppa en cache-instans. |
> | Åtgärd | Microsoft.Cache/redis/write | Ändra Redis Cache inställningar och konfiguration i hanterings portalen |
> | Åtgärd | Microsoft. cache/register/åtgärd | Registrerar resurs leverantören "Microsoft. cache" med en prenumeration |
> | Åtgärd | Microsoft.Cache/unregister/action | Avregistrerar resurs leverantören "Microsoft. cache" med en prenumeration |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Capacity/appliedreservations/Read | Läs alla reservationer |
> | Åtgärd | Microsoft.Capacity/calculateexchange/action | Beräknar Exchange-beloppet och priset för nya inköp och returnerar princip fel. |
> | Åtgärd | Microsoft.Capacity/calculateprice/action | Beräkna eventuella reservations priser |
> | Åtgärd | Microsoft. Capacity/Catalog/Read | Läs reservations katalog |
> | Åtgärd | Microsoft. Capacity/checkoffers/åtgärd | Kontrol lera eventuella prenumerations erbjudanden |
> | Åtgärd | Microsoft. Capacity/checkscopes/åtgärd | Kontrol lera vilken prenumeration som helst |
> | Åtgärd | Microsoft. Capacity/commercialreservationorders/Read | Hämta reservations order som skapats i en klient |
> | Åtgärd | Microsoft. Capacity/Exchange/Action | Exchange-reservation |
> | Åtgärd | Microsoft. Capacity/Operations/Read | Läs valfri åtgärd |
> | Åtgärd | Microsoft. Capacity/register/åtgärd | Registrerar kapacitets resurs leverantören och gör det möjligt att skapa kapacitets resurser. |
> | Åtgärd | Microsoft. Capacity/reservationorders/åtgärd | Uppdatera eventuella reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/availablescopes/Action | Hitta tillgängliga omfång |
> | Åtgärd | Microsoft. Capacity/reservationorders/calculaterefund/Action | Beräknar bidrags beloppet och priset för nya inköp och returnerar princip fel. |
> | Åtgärd | Microsoft. Capacity/reservationorders/Delete | Ta bort alla reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/merge/Action | Koppla valfri reservation |
> | Åtgärd | Microsoft. Capacity/reservationorders/Read | Läs alla reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/reservation/åtgärd | Uppdatera eventuella reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/reservations/availablescopes/Action | Hitta tillgängliga omfång |
> | Åtgärd | Microsoft. Capacity/reservationorders/reservations/Delete | Ta bort alla reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/reservations/Read | Läs alla reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/reservationer/revisioner/Read | Läs alla reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/reservations/Write | Skapa valfri reservation |
> | Åtgärd | Microsoft. Capacity/reservationorders/retur/åtgärd | Returnera eventuella reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/Split/Action | Dela alla reservationer |
> | Åtgärd | Microsoft. Capacity/reservationorders/växling/åtgärd | Byt ut eventuell reservation |
> | Åtgärd | Microsoft. Capacity/reservationorders/Write | Skapa valfri reservation |
> | Åtgärd | Microsoft. Capacity/Tenants/register/åtgärd | Registrera en klient |
> | Åtgärd | Microsoft. Capacity/avregistrera/åtgärd | Avregistrera en klient |
> | Åtgärd | Microsoft. Capacity/validatereservationorder/åtgärd | Verifiera eventuella reservationer |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Delete |  |
> | Åtgärd | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Read |  |
> | Åtgärd | Microsoft. CDN/cdnwebapplicationfirewallmanagedrulesets/Write |  |
> | Åtgärd | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Delete |  |
> | Åtgärd | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Read |  |
> | Åtgärd | Microsoft. CDN/cdnwebapplicationfirewallpolicies/Write |  |
> | Åtgärd | Microsoft. CDN/CheckNameAvailability/åtgärd |  |
> | Åtgärd | Microsoft. CDN/CheckResourceUsage/åtgärd |  |
> | Åtgärd | Microsoft. CDN/edgenodes/Delete |  |
> | Åtgärd | Microsoft.Cdn/edgenodes/read |  |
> | Åtgärd | Microsoft. CDN/edgenodes/Write |  |
> | Åtgärd | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Delete |  |
> | Åtgärd | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Read |  |
> | Åtgärd | Microsoft. CDN/operationresults/cdnwebapplicationfirewallpolicyresults/Write |  |
> | Åtgärd | Microsoft. CDN/operationresults/Delete |  |
> | Åtgärd | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/Action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Åtgärd | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Åtgärd | Microsoft. CDN/operationresults/profileresults/endpointresults/load/Action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Åtgärd | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Write |  |
> | Åtgärd | Microsoft. CDN/operationresults/profileresults/endpointresults/rensning/åtgärd |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Åtgärd | Microsoft. CDN/operationresults/profileresults/endpointresults/start/Action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Åtgärd | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Åtgärd | Microsoft. CDN/operationresults/Read |  |
> | Åtgärd | Microsoft. CDN/operationresults/Write |  |
> | Åtgärd | Microsoft. CDN/Operations/Read |  |
> | Åtgärd | Microsoft. CDN/profiler/CheckResourceUsage/åtgärd |  |
> | Åtgärd | Microsoft.Cdn/profiles/delete |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/CheckResourceUsage/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/customdomains/Delete |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Åtgärd | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/customdomains/Read |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/customdomains/Write |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/ta bort |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/belastning/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/ursprung/ta bort |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/ursprung/läsa |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/ursprung/Skriv |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/rensning/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/läsning |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/start/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/stoppa/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/ValidateCustomDomain/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/slut punkter/skrivning |  |
> | Åtgärd | Microsoft. CDN/profiler/GenerateSsoUri/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/GetSupportedOptimizationTypes/åtgärd |  |
> | Åtgärd | Microsoft. CDN/profiler/läsa |  |
> | Åtgärd | Microsoft. CDN/profiler/Write |  |
> | Åtgärd | Microsoft. CDN/register/åtgärd | Registrerar prenumerationen för CDN-adressresursen och gör det möjligt att skapa CDN-profiler. |
> | Åtgärd | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/certificates/Delete | Ta bort ett befintligt certifikat |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/certificates/Read | Hämta listan över certifikat |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/certificates/Write | Lägg till ett nytt certifikat eller uppdatera ett befintligt |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/Delete | Ta bort en befintlig AppServiceCertificate |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/Read | Hämta listan över CertificateOrders |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/Reissue/åtgärd | Utfärda en befintlig certificateorder igen |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/renew/åtgärd | Förnya en befintlig certificateorder |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/resendEmail/Action | Skicka e-postmeddelande om certifikat |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Skicka e-postmeddelanden för begäran till en annan e-postadress |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Hämta platsens sigill för en utfärdad App Service Certificate |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Hämta listan över certifikat åtgärder |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Hämta e-posthistorik för certifikat |
> | Åtgärd | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verifiera domän ägarskap |
> | Åtgärd | Microsoft. CertificateRegistration/certificateOrders/Write | Lägg till en ny certificateOrder eller uppdatera en befintlig |
> | Åtgärd | Microsoft. CertificateRegistration/Operations/Read | Visa en lista med alla åtgärder från App Service Certificate Registration |
> | Åtgärd | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Etablera tjänstens huvud namn för tjänstens huvud namn |
> | Åtgärd | Microsoft. CertificateRegistration/register/åtgärd | Registrera resurs leverantören för Microsoft-certifikaten för prenumerationen |
> | Åtgärd | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Verifiera ett certifikat inköps objekt utan att skicka det |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ClassicCompute/Capabilities/Read | Visar funktionerna |
> | Åtgärd | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Kontrollerar tillgängligheten för ett angivet domän namn. |
> | Åtgärd | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Hämtar tillgängligheten för ett angivet domän namn. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/aktiv/skrivning | Anger det aktiva domän namnet. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/availabilitySets/Read | Visa tillgänglighets uppsättning för resursen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/funktioner/läsa | Visar funktioner för domän namn |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/ta bort | Ta bort domän namn för resurser. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/Read | Visar distributions platserna. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/roller/läsa | Hämta roll på distributions plats för domän namn |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/roles/roleinstances/Read | Hämta roll instans för rollen på distributions platsen för domän namnet |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/tillstånd/läsa | Hämta status för distributions facket. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/tillstånd/skrivning | Lägg till status för distributions facket. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/upgradedomain/Read | Hämta uppgraderings domän för distributions plats på domän namn |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/upgradedomain/Write | Uppdatera uppgraderings domän för distributions plats på domän namn |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/deploymentslots/Write | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/tillägg/ta bort | Ta bort domän namns tilläggen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/tillägg/operationStatuses/läsa | Läser åtgärds status för domän namns tilläggen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/tillägg/läsa | Returnerar domän namns tilläggen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/tillägg/Skriv | Lägg till domän namns tilläggen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/internalLoadBalancers/Delete | Ta bort en ny intern belastnings utjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Läser åtgärds status för interna belastningsutjämnare för domän namn. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/internalLoadBalancers/Read | Hämtar de interna belastnings utjämningen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/internalLoadBalancers/Write | Skapar en ny intern belastnings utjämning. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Läser åtgärds status för belastningsutjämnade slut punkts uppsättningar för domän namn. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/loadBalancedEndpointSets/Read | Hämta de belastningsutjämnade slut punkts uppsättningarna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Lägg till den belastningsutjämnade slut punkts uppsättningen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/operationstatuses/Read | Hämta åtgärds status för domän namnet. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/operationStatuses/Read | Läser åtgärds status för domän namns tilläggen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/läsa | Returnera domän namnen för resurser. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/serviceCertificates/Delete | Ta bort de tjänst certifikat som används. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Läser åtgärds statusen för domän namns tjänst certifikaten. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Returnerar de tjänst certifikat som används. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/serviceCertificates/Write | Lägg till eller ändra de tjänst certifikat som används. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/abortMigration/åtgärd | Avbryter migreringen av ett distributions fack. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/commitMigration/åtgärd | Aktiverar migrering av ett distributions fack. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/ta bort | Tar bort en specifik distributions plats. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/operationStatuses/läsa | Läser åtgärds status för domän namns platserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Förbereder migrering av ett distributions fack. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/läsa | Visar distributions platserna. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Ta bort tilläggs referensen för distributions plats rollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Läser åtgärds status för roll tilläggs referenser för domän namns platser. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Returnerar tilläggs referensen för distributions plats rollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Lägg till eller ändra tilläggs referensen för distributions plats rollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Hämta roll mått definitionen för domän namnet. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/mått/läsa | Hämta roll mått för domän namnet. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/operationstatuses/läsa | Hämta åtgärds status för domän namns fack rollen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/läsa | Hämta rollen för distributions platsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Laddar ned anslutnings filen för fjärr skrivbord för roll instansen på domän namns fack rollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Hämtar åtgärds status för roll instansen på domän namns fack rollen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Hämta roll instansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Återskapar roll instansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Avbildningen av roll instansen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Startar om roll instanser. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Hämta roll-SKU för distributions platsen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/Skriv | Lägg till rollen för distributions platsen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/start/åtgärd | Startar ett distributions fack. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/tillstånd/starta/skriva | Ändrar distributions platsens tillstånd till stoppad. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Ändrar distributions platsens tillstånd till startat. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/stoppa/åtgärd | Pausar distributions platsen. |
> | Åtgärd | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Gå igenom uppgraderingen av domänen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/validateMigration/åtgärd | Verifierar migrering av ett distributions fack. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/Skriv | Skapar eller uppdaterar distributionen. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/växling/åtgärd | Växlar mellan mellanlagrings plats och produktions plats. |
> | Åtgärd | Microsoft. ClassicCompute/domän namn/skrivning | Lägg till eller ändra domän namn för resurser. |
> | Åtgärd | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Flytta alla klassiska resurser till en annan prenumeration. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystemFamilies/read | Visar en lista över de gäst operativ system familjer som är tillgängliga i Microsoft Azure och listar även de operativ system versioner som är tillgängliga för varje familj. |
> | Åtgärd | Microsoft.ClassicCompute/operatingSystems/read | Visar en lista över de versioner av gäst operativ systemet som för närvarande är tillgängliga i Microsoft Azure. |
> | Åtgärd | Microsoft. ClassicCompute/Operations/Read | Gets the list of operations. |
> | Åtgärd | Microsoft. ClassicCompute/operationStatuses/Read | Reads the operation status for the resource. |
> | Åtgärd | Microsoft. ClassicCompute/kvoter/läsa | Hämta kvoten för prenumerationen. |
> | Åtgärd | Microsoft. ClassicCompute/register/åtgärd | Registrera för klassisk beräkning |
> | Åtgärd | Microsoft. ClassicCompute/resourceTypes/SKU/Read | Hämtar SKU-listan för resurs typer som stöds. |
> | Åtgärd | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Verifiera prenumerationens tillgänglighet för klassisk flytt åtgärd. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Tar bort den nätverks säkerhets grupp som är kopplad till den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärds status för de virtuella datorerna som är associerade med nätverks säkerhets grupper. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Hämtar den nätverks säkerhets grupp som är kopplad till den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Lägger till en nätverks säkerhets grupp som är kopplad till den virtuella datorn. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Hämtar möjliga asynkrona åtgärder |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Kopplar en datadisk till en virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/capture/action | Avbilda en virtuell dator. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/Delete | Tar bort virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Kopplar från en datadisk från den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Hämta inställningar för diagnostik för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/disks/read | Hämtar lista över data diskar |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/Action | Laddar ned RDP-filen för den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Läser åtgärds status för tillägg för virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/read | Hämtar tillägget för den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/extensions/write | Placerar tillägget för virtuell dator. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Hämta mått definitionen för den virtuella datorn. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/Metrics/Read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Tar bort nätverks säkerhets gruppen som är kopplad till nätverks gränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärds status för de virtuella datorerna som är associerade med nätverks säkerhets grupper. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Hämtar nätverks säkerhets gruppen som är kopplad till nätverks gränssnittet. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Lägger till en nätverks säkerhets grupp som är kopplad till nätverks gränssnittet. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/operationStatuses/Read | Läser åtgärds status för de virtuella datorerna. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/performMaintenance/Action | Utför underhåll på den virtuella datorn. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/Read | Hämtar lista över virtuella datorer. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/redistribution/åtgärd | Distribuerar om den virtuella datorn. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/restart/Action | Startar om virtuella datorer. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Stäng av den virtuella datorn. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/start/Action | Starta den virtuella datorn. |
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/stopp/åtgärd | Stoppar den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicCompute/virtualMachines/write | Lägg till eller ändra virtuella datorer. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Hämta en snabb flödes kors anslutnings åtgärds status. |
> | Åtgärd | Microsoft. ClassicNetwork/expressroutecrossconnections/peering/Delete | Ta bort peering med Express Route-Cross-anslutning. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Hämta en snabb flödes kors anslutnings åtgärds status för peering. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Hämta Cross-peer-anslutning med Express Route. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Lägg till ExpressRoute-peering med Express Route. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Hämta kors anslutningar för Express Route. |
> | Åtgärd | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Lägg till Express vägs kors anslutningar. |
> | Åtgärd | Microsoft. ClassicNetwork/gatewaySupportedDevices/Read | Hämtar listan över enheter som stöds. |
> | Åtgärd | Microsoft. ClassicNetwork/networkSecurityGroups/Delete | Tar bort nätverks säkerhets gruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Läser åtgärds status för nätverks säkerhets gruppen. |
> | Åtgärd | Microsoft. ClassicNetwork/networksecuritygroups/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar diagnostikinställningar för nätverks säkerhets grupper |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för nätverks säkerhets grupper. den här åtgärden kompletteras av Resource Provider för insikter. |
> | Åtgärd | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelser för nätverks säkerhets grupp |
> | Åtgärd | Microsoft. ClassicNetwork/networkSecurityGroups/Read | Hämtar nätverks säkerhets gruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Tar bort säkerhets regeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Läser åtgärds status för säkerhets reglerna för nätverks säkerhets gruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Hämtar säkerhets regeln. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Lägger till eller uppdaterar en säkerhets regel. |
> | Åtgärd | Microsoft.ClassicNetwork/networkSecurityGroups/write | Lägger till en ny nätverks säkerhets grupp. |
> | Åtgärd | Microsoft. ClassicNetwork/Operations/Read | Hämta klassiska nätverks åtgärder. |
> | Åtgärd | Microsoft. ClassicNetwork/kvoter/läsa | Hämta kvoten för prenumerationen. |
> | Åtgärd | Microsoft. ClassicNetwork/register/åtgärd | Registrera till klassiskt nätverk |
> | Åtgärd | Microsoft. ClassicNetwork/reservedIps/Delete | Ta bort en reserverad IP-adress. |
> | Åtgärd | Microsoft. ClassicNetwork/reservedIps/JOIN/åtgärd | Anslut till en reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Läser åtgärds status för reserverade IP-adresser. |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/read | Hämtar de reserverade IP-adresserna |
> | Åtgärd | Microsoft.ClassicNetwork/reservedIps/write | Lägg till en ny reserverad IP |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/Action | Avbryter migreringen av en Virtual Network |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/Capabilities/Read | Visar funktionerna |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/Action | Kontrollerar tillgängligheten för en specifik IP-adress i ett virtuellt nätverk. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/Action | Genomför migreringen av en Virtual Network |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/Delete | Tar bort det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Återkallar ett klient certifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Läs de återkallade klient certifikaten. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Återkallar ett klient certifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Tar bort klient certifikatet för den virtuella Nätverksgatewayen. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/hämtning/åtgärd | Hämtar certifikat efter tumavtryck. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Visar en lista över certifikat paket för virtuell nätverksgateway. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Hitta klientens rot certifikat. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Överför ett nytt klient rot certifikat. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/Connections/Connect/åtgärd | Ansluter en plats till plats-Gateway-anslutning. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/anslutningar/från koppling/åtgärd | Kopplar från en plats till plats-Gateway-anslutning. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/anslutningar/läsa | Hämtar listan över anslutningar. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/anslutningar/test/åtgärd | Testar en plats-till-plats-Gateway-anslutning. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/Delete | Tar bort den virtuella Nätverksgatewayen. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/åtgärd | Hämtar enhetens konfigurations skript. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/åtgärd | Laddar ned Gateway-diagnostik. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/åtgärd | Hämtar krets tjänst nyckeln. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listar det virtuella Nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Läser åtgärds status för de virtuella nätverkets gatewayer. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/paket/läsa | Hämtar det virtuella nätverkets gateway-paket. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Read | Hämtar de virtuella Nätverksgatewayen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Startar diagnostik för den virtuella Nätverksgatewayen. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/åtgärd | Stoppar diagnostiken för den virtuella Nätverksgatewayen. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Write | Lägger till en virtuell nätverksgateway. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/JOIN/åtgärd | Ansluter till det virtuella nätverket. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Läser åtgärds status för de virtuella nätverken. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Peer-koppla ett virtuellt nätverk till ett annat virtuellt nätverk. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Förbereder migreringen av en Virtual Network |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/read | Hämta det virtuella nätverket. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Delete | Tar bort den fjärranslutna virtuella nätverkets peering-proxy. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Hämtar den fjärranslutna virtuella nätverkets peering-proxy. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Lägger till eller uppdaterar den fjärranslutna virtuella nätverkets peering-proxy. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Tar bort nätverks säkerhets gruppen som är kopplad till under nätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärds status för det virtuella nätverkets undernät associerade nätverks säkerhets gruppen. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Hämtar nätverks säkerhets gruppen som är kopplad till under nätet. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Lägger till en nätverks säkerhets grupp som är kopplad till under nätet. |
> | Åtgärd | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/Action | Verifierar migreringen av en Virtual Network |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Gets the virtual network peering. |
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/write | Lägg till ett nytt virtuellt nätverk. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ClassicStorage/Capabilities/Read | Visar funktionerna |
> | Åtgärd | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Kontrollerar tillgängligheten för ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/checkStorageAccountAvailability/Read | Få till gång till ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/disks/Read | Returnerar lagrings konto disken. |
> | Åtgärd | Microsoft.ClassicStorage/images/operationstatuses/read | Hämtar status för avbildnings åtgärd. |
> | Åtgärd | Microsoft.ClassicStorage/images/read | Returnerar bilden. |
> | Åtgärd | Microsoft. ClassicStorage/Operations/Read | Hämtar klassiska lagrings åtgärder |
> | Åtgärd | Microsoft.ClassicStorage/osImages/read | Returnerar operativ Systems avbildningen. |
> | Åtgärd | Microsoft.ClassicStorage/osPlatformImages/read | Hämtar plattforms avbildningen för operativ systemet. |
> | Åtgärd | Microsoft. ClassicStorage/publicImages/Read | Hämtar den offentliga virtuella dator avbildningen. |
> | Åtgärd | Microsoft. ClassicStorage/kvoter/läsa | Hämta kvoten för prenumerationen. |
> | Åtgärd | Microsoft. ClassicStorage/register/åtgärd | Registrera till klassiskt lagrings utrymme |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/abortMigration/Action | Avbryter migreringen av ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/blobServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/blobServices/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/blobServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/commitMigration/Action | Aktiverar migrering av ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/Delete | Ta bort lagrings kontot. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Tar bort en specifik lagrings konto disk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Reads the operation status for the resource. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagrings konto disken. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/write | Lägger till en lagrings konto disk. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/delete | Tar bort en specifik lagrings konto avbildning. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Returnerar åtgärds status för lagrings konto avbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagrings konto avbildningen. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar åtkomst nycklar för lagrings kontona. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Reads the operation status for the resource. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Tar bort en specifik operativ Systems avbildning för lagrings konton. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returnerar operativ Systems avbildningen för lagrings kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/write | Lägger till en specifik operativ Systems avbildning för lagrings konton. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Förbereder migrering av ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/read | Returnera lagrings kontot med det aktuella kontot. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Återskapar befintliga åtkomst nycklar för lagrings kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/read | Hämta de tillgängliga tjänsterna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/Write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/tableServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/validateMigration/Action | Verifierar migreringen av ett lagrings konto. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Tar bort en specifik avbildning av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Hämtar en specifik åtgärds status för virtuell dator avbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Returnerar avbildningen av den virtuella datorn. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Lägger till en specifik avbildning av en virtuell dator. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/write | Lägger till ett nytt lagrings konto. |
> | Åtgärd | Microsoft.ClassicStorage/vmImages/read | Visar avbildningar av virtuella datorer. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | DataAction | Microsoft. CognitiveServices/Accounts/AnomalyDetector/timeseries/hela/detect/åtgärd | Den här åtgärden genererar en modell med en hel serie, varje punkt identifieras med samma modell.<br>Med den här metoden används punkter före och efter en viss punkt för att avgöra om det är en avvikelse.<br>Hela identifieringen kan ge användaren en övergripande status för tids serien. |
> | DataAction | Microsoft. CognitiveServices/Accounts/AnomalyDetector/timeseries/Last/detect/åtgärd | Den här åtgärden genererar en modell som använder punkter före den senaste. Med den här metoden används endast historiska punkter för att avgöra om mål punkten är en avvikelse. Den senaste punkten som identifierar matchar scenariot för övervakning av affärs mått i real tid. |
> | DataAction | Microsoft. CognitiveServices/konton/autoföreslå/Sök/åtgärd | Den här åtgärden ger förslag på en viss fråga eller partiell fråga. |
> | DataAction | Microsoft. CognitiveServices/konton/ComputerVision/analysera/åtgärd | Den här åtgärden extraherar en omfattande uppsättning visuella funktioner baserat på avbildningens innehåll.  |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/areaofinterest/Action | Den här åtgärden returnerar en avgränsnings ruta runt det viktigaste avsnittet i bilden. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Beskrivning/åtgärd | Den här åtgärden genererar en beskrivning av en bild på ett läsligt språk med fullständiga meningar.<br> Beskrivningen baseras på en samling innehålls taggar, som också returneras av åtgärden.<br>Mer än en beskrivning kan genereras för varje avbildning.<br> Beskrivningar sorteras efter deras Tillförlitlighets resultat.<br>Alla beskrivningar är på engelska. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/detect/åtgärd | Den här åtgärden utför objekt identifiering på den angivna avbildningen.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Den här åtgärden genererar en miniatyr bild med användarens angivna bredd och höjd.<br> Som standard analyserar tjänsten bilden, identifierar det intressanta området (ROI) och skapar smarta beskärningskoordinater baserat på det intressanta området.<br> Smart beskärning hjälper när du anger ett proportioner som skiljer sig från indatakällans bild |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Den här åtgärden identifierar innehåll i en avbildning genom att använda en domänbaserad modell.<br> Listan över domänbaserade modeller som stöds av API för visuellt innehåll kan hämtas med hjälp av/Models GET-begäran.<br> För närvarande tillhandahåller API följande domänbaserade modeller: kändisar, landmärken. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Models/Read | Den här åtgärden returnerar listan över domänbaserade modeller som stöds av API för visuellt innehåll.  För närvarande stöder API följande domänbaserade modeller: kändis Recognizer, landmärkes tolk. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/OCR/Action | Optisk tecken läsning (OCR) identifierar text i en bild och extraherar de tolkade tecknen till en tecken ström som kan användas i datorn.    |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Read/analysera/åtgärd | Använd det här gränssnittet för att utföra en Läs åtgärd som använder den avancerade OCR-algoritmen (Optical Character igenkänning) som är optimerad för text intensiva dokument.<br>Den kan hantera handskrivna, skrivna eller blandade dokument.<br>När du använder Read-gränssnittet innehåller svaret ett huvud som kallas "åtgärds plats".<br>"Åtgärds platsens"-huvud innehåller den URL som du måste använda för att få Läs resultat-åtgärd för att få åtkomst till OCR-resultat. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Read/analyzeresults/Read | Använd det här gränssnittet för att hämta status och OCR-resultat för en Läs åtgärd.  URL: en som innehåller ' operationId ' returneras i svars huvudet för åtgärden Läs åtgärd-plats. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Read/Core/asyncbatchanalyze/Action | Använd det här gränssnittet för att få resultatet av en batch-läsa fil åtgärd, med den avancerade optiska bokstaven |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Read/Operations/Read | Det här gränssnittet används för att hämta OCR-resultat för Läs åtgärder. URL: en till det här gränssnittet bör hämtas från <b>"operation-location"</b> -fältet som returneras från batch-läsa fil gränssnitt. |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Använd det här gränssnittet för att få resultatet av en Identifiera text-åtgärd. När du använder Identifiera text-gränssnittet innehåller svaret ett fält med namnet "operation-location". Fältet "åtgärds plats" innehåller den URL som du måste använda för åtgärden Hämta Identifiera text åtgärds resultat. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/tag/Action | Den här åtgärden genererar en lista med ord, eller taggar, som är relevanta för innehållet i den angivna bilden.<br>API för visuellt innehåll kan returnera Taggar baserat på objekt, levnads, landskap eller åtgärder som finns i bilder.<br>Till skillnad från kategorier är Taggar inte organiserade enligt ett hierarkiskt klassificerings system, men motsvarar bild innehåll.<br>Taggar kan innehålla tips för att undvika tvetydighet eller ange sammanhang, till exempel kan taggen "cello" åtföljas av tipset "Musical instrument".<br>Alla Taggar är på engelska. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/textoperations/Read | Det här gränssnittet används för att känna igen resultatet av text åtgärden. URL: en till det här gränssnittet ska hämtas från <b>"åtgärds plats"</b> -fältet som returneras från identifiera text-gränssnittet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-funktion/åtgärd | Skapa avbildnings lista. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-/Delete | Bild listor – ta bort |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-filer/images/Delete | Ta bort en bild från din avbildnings lista. Avbildnings listan kan användas för att utföra fuzzy-matchning mot andra bilder när du använder API för avbildning/matchning. Ta bort alla avbildningar från listan. Avbildnings listan kan användas för att utföra fuzzy-matchning mot andra bilder när du använder API: et för avbildning/matchning. * |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-filer/bilder/läsa | Bild – hämta alla bild-ID: n |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-bilder/images/Write | Lägg till en bild i din avbildnings lista. Avbildnings listan kan användas för att utföra fuzzy-matchning mot andra bilder när du använder API för avbildning/matchning. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/imageer/Read | Bild listor – hämta information – bild listor – hämta alla |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-åtgärder/refreshindex/åtgärd | Bild listor – uppdatera Sök index |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/image-användare/skrivning | Avbildnings listor – uppdaterings information |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/processimage/evaluate/Action | Returnerar sannolikheten för den bild som innehåller vågat eller vuxen innehåll. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/processimage/findfaces/Action | Hitta ansikten i bilder. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/processimage/match/åtgärd | Fuzzily matchar en bild mot en av dina anpassade bild listor.<br>Du kan skapa och hantera dina anpassade avbildnings listor med hjälp av det här API: et.<br> |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/processimage/OCR/Action | Returnerar all text som finns i bilden för det angivna språket. Om inget språk anges i indatamängden används engelska som standard. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/processtext/detectlanguage/Action | Den här åtgärden identifierar språket för det angivna indatamängds innehållet.<br>Returnerar ISO 639-3-koden för det dominerande språket som består av den text som skickades.<br>Över 110 språk stöds. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/processtext/skärm/åtgärd | Åtgärden identifierar olämpliga ord på över 100 språk och matchar mot anpassade och delade blockeringslistor. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Jobs/åtgärd | Ett jobb-ID kommer att returneras för det avbildnings innehåll som publicerats på den här slut punkten.  |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Jobs/Read | Hämta jobb information för ett jobb-ID. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Reviews/Accesskey/Read | Hämta åtkomst nyckeln granska innehålls åtkomst för ditt team. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Reviews/Action | De granskningar som skapats visas för granskare i ditt team. När granskarna har slutfört granskningen skulle resultatet av granskningen publiceras (d.v.s. HTTP POST) på den angivna CallBackEndpoint. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Reviews/bild rutor/läsa | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Reviews/ramar/Write | Använd den här metoden för att lägga till ramar för en video granskning. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/rereviews/Publish/Action | Video granskningar har inlednings vis skapats i ett opublicerat tillstånd – vilket innebär att den inte är tillgänglig för granskare i ditt team och granskas ännu. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/recensioner/Read | Returnerar gransknings information för det granskade ID som har överförts. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Reviews/avskrift/åtgärd | Detta API lägger till en avskrifts fil (text version av alla ord som talas i en video) till en video granskning. Filen ska vara ett giltigt WebVTT-format. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Reviews/transcriptmoderationresult/Action | Detta API lägger till en avskrifts skärm text till resultat filen för en video granskning. Avskrifts skärm text resultat filen är ett resultat av API för skärm text. För att kunna generera text resultat filen för avskrifts skärmen måste en avskrifts fil visas för svordomar med skärm text API. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Settings/templates/Delete | Ta bort en mall i din grupp |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Settings/templates/Read | Returnerar en matris med gransknings mallar som har allokerats i det här teamet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/Settings/templates/Write | Skapar eller uppdaterar den angivna mallen |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/arbetsflöden/Read | Hämta information om ett enskilt arbets flöde i din grupp. Hämta alla arbets flöden som är tillgängliga för ditt team * |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/Teams/arbetsflöden/Write | Skapa ett nytt arbets flöde eller uppdatera ett befintligt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/Action | Skapa term lista. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/bulkupdate/Action | Term listor-Mass uppdatering |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/Delete | Term listor – ta bort |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/Read | Term listor – hämta alla-term listor – hämta information |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/refreshindex/Action | Term listor – uppdatera Sök index |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/villkor/Delete | Term-Delete-term – ta bort alla villkor |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/villkor/Read | Term – hämta alla villkor |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/villkor/Write | Term – Lägg till term |
> | DataAction | Microsoft. CognitiveServices/Accounts/ContentModerator/termlists/Write | Term listor – uppdaterings information |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/klassificera/iterationer/bild/åtgärd | Klassificera en bild och spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/klassificera/iterationer/avbildning/nostore/åtgärd | Klassificera en bild utan att spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/klassificerar/iterationer/URL/åtgärd | Klassificera en bild-URL och spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/klassificerar/iterationer/URL/nostore/åtgärd | Klassificera en bild-URL utan att spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/identifiera/iterationer/bild/åtgärd | Identifiera objekt i en bild och spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/identifiera/iterationer/avbildning/nostore/åtgärd | Identifiera objekt i en bild utan att spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/identifiera/iteration/URL/åtgärd | Identifiera objekt i en bild-URL och spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/identifiera/iteration/URL/nostore/åtgärd | Identifiera objekt i en bild-URL utan att spara resultatet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/Domains/Read | Hämta information om en speciell domän. Hämta en lista över tillgängliga domäner. * |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/labelproposals/Setting/Action | Ange Poolens storlek för etikett förslag. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/labelproposals/Setting/Read | Hämta Poolens storlek för etikett förslag för det här projektet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/migrering/åtgärd | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/åtgärd | Skapa ett projekt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/ta bort | Ta bort ett enskilt projekt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/åtgärd | Detta API accepterar bröd innehåll som multipart/form-data och program/oktett-Stream. När du använder multipart |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/ta bort | Ta bort bilder från uppsättningen utbildnings avbildningar. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/filer/åtgärd | Detta API godkänner en batch med filer och eventuellt taggar för att skapa avbildningar. Det finns en gräns på 64 bilder och 20 taggar. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/ID/läsa | Detta API kommer att returnera en uppsättning avbildningar för de angivna taggarna och eventuellt upprepning. Om ingen iteration har angetts är |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/förutsägelse/åtgärd | Detta API skapar en avbildning av avbildningar från förväntade avbildningar. Det finns en gräns på 64 bilder och 20 taggar. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/regionproposals/åtgärd | Med det här API: et får du region förslag för en avbildning tillsammans med förtroenden för regionen. Den returnerar en tom matris om inga förslag hittas. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/regioner/åtgärd | Detta API godkänner en batch med bild regioner, och eventuellt taggar, för att uppdatera befintliga bilder med region information. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/regioner/ta bort | Ta bort en uppsättning bild regioner. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/föreslagen/åtgärd | Detta API hämtar otaggade bilder som filtrerats efter föreslagna tagg-ID. Den returnerar en tom matris om inga bilder hittas. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/föreslagen/antal/åtgärd | Detta API tar i tagIds för att få antalet otaggade bilder per föreslagen tagg för ett angivet tröskelvärde. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/taggat/Count/Read | Filtreringen är i en och/eller-relation. Om exempelvis de angivna taggen ID: n är för "hund" och |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/märkt/läsa | Detta API stöder batch-och intervall markering. Som standard returnerar den bara de första 50 avbildningarna som matchar avbildningarna. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/Taggar/åtgärd | Associera en uppsättning avbildningar med en uppsättning taggar. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/Taggar/ta bort | Ta bort en uppsättning taggar från en uppsättning bilder. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/omärkt/Count/Read | Detta API returnerar avbildningar som inte har några taggar för ett angivet projekt och eventuellt en iteration. Om ingen iteration har angetts är |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/omärkt/läsa | Detta API stöder batch-och intervall markering. Som standard returnerar den bara de första 50 avbildningarna som matchar avbildningarna. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/bilder/URL/åtgärd | Detta API accepterar en batch med URL: er och eventuellt taggar för att skapa avbildningar. Det finns en gräns på 64 bilder och 20 taggar. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/ta bort | Ta bort en speciell iteration av ett projekt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/export/åtgärd | Exportera en utbildad iteration. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/export/läsa | Hämta listan över exporter för en speciell iteration. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/prestanda/bilder/antal/läsning | Filtreringen är i en och/eller-relation. Om exempelvis de angivna taggen ID: n är för "hund" och |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/prestanda/bilder/läsa | Detta API stöder batch-och intervall markering. Som standard returnerar den bara de första 50 avbildningarna som matchar avbildningarna. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/prestanda/läsa | Få detaljerad prestanda information om en iteration. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/publicera/åtgärd | Publicera en speciell iteration. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/publicera/ta bort | Avpublicera en speciell iteration. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/läsa | Hämta en speciell iteration. Hämta iterationer för projektet. * |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/iterationer/skriva | Uppdatera en speciell iteration. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/förutsägelse/ta bort | Ta bort en uppsättning förväntade avbildningar och deras associerade förutsägelse resultat. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/förutsägelse/fråga/åtgärd | Hämta avbildningar som skickats till din förutsägelse slut punkt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/Quicktest/avbildning/åtgärd | Snabb test av en bild. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/Quicktest/URL/åtgärd | Snabb test av en bild-URL. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/läsa | Hämta ett speciellt projekt. Hämta dina projekt. * |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/Taggar/åtgärd | Skapa en tagg för projektet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/Taggar/ta bort | Ta bort en tagg från projektet. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/Taggar/läsa | Hämta information om en speciell tagg. Hämta taggar för ett angivet projekt och iteration. * |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/Taggar/skriva | Uppdatera en tagg. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/tagsandregions/förslag/åtgärd | Med det här API: et får du förslag på taggar och regioner för en matris/grupp med otaggade bilder och förtroenden för taggarna. Den returnerar en tom matris om inga Taggar hittas. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/träna/åtgärd | Köar projekt för utbildning. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/projekt/skrivning | Uppdatera ett enskilt projekt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/kvot/åtgärd | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/kvot/ta bort | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/kvot/uppdatering/skrivning | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användning/förutsägelse/användare/läsning | Hämta användning för förutsägelse resurs för Oxford-användare |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användning/utbildning/resurs/nivå/läsa | Hämta användning för utbildnings resurser för Azure-användare |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användning/utbildning/användare/läsa | Hämta användning för utbildnings resurser för Oxford-användare |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användare/åtgärd | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/User/Delete | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användare/tillstånd/Skriv | Uppdatera användar tillstånd |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användare/nivå/Skriv | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/användare/läsa | *NotDefined* |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/vitlista/Delete | Tar bort en vit listas-användare med en speciell funktion |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/vitlista/Read | Hämtar en lista över vit listas-användare med en speciell funktion |
> | DataAction | Microsoft. CognitiveServices/Accounts/CustomVision. förutsägelse/vitlista/Write | Uppdaterar eller skapar en användare i vitlista med en speciell funktion |
> | Åtgärd | Microsoft. CognitiveServices/Accounts/Delete | Tar bort API-konton |
> | DataAction | Microsoft. CognitiveServices/Accounts/EntitySearch/search/Action | Hämta entiteter och placerar resultat för en specifik fråga. |
> | DataAction | Microsoft. CognitiveServices/konton/ansikte/identifiera/åtgärd | Identifiera mänskliga ansikten i en bild, returnera ansikts rektanglar och eventuellt med faceIds, landmärken och attribut. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/facelists/Delete | Ta bort en angiven ansikts lista. De relaterade ansikts bilderna i listan ansikts tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/persistedfaces/delete | Ta bort ett ansikte från en ansikts lista genom att ange faceListId och persisitedFaceId. Den relaterade ansikts bilden tas också bort. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/facelists/persistedfaces/Write | Lägg till en ansikte i en angiven ansikts lista, upp till 1 000 ansikten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/read | Hämta en ansikts listas faceListId, namn, userData och ansikten i ansikts listan. List FACET visar faceListId, Name och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/facelists/write | Skapa en tom ansikts lista med användarens angivna faceListId, namn och valfritt userData. Upp till 64 ansikts listor är tillåten uppdaterings information för en ansikts lista, inklusive namn och userData. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/findsimilars/Action | Ange faceId för frågans ansikte för att söka efter liknande ansikten från en faceId-matris, en ansikts lista eller en stor ansikts lista. faceId |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/group/action | Dela upp kandidat ansikten i grupper baserat på ansikts likhet. |
> | DataAction | Microsoft. CognitiveServices/konton/ansikte/identifiera/åtgärd | 1-till-många-identifiering för att hitta de närmaste matchningarna för den speciella frågan personens ansikte från en person grupp eller en stor person grupp. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largefacelists/Delete | Ta bort en angiven stor ansikts lista. De relaterade ansikts bilderna i den stora ansikts listan tas också bort. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largefacelists/persistedfaces/Delete | Ta bort ett ansikte från en stor ansikts lista genom att ange largeFaceListId och persisitedFaceId. Den relaterade ansikts bilden tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/persistedfaces/read | Hämta sparat ansikte i stor ansikts lista med largeFaceListId och persistedFaceId. Lista ansikten "persistedFaceId och userData" i en angiven stor ansikts lista. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largefacelists/persistedfaces/Write | Lägg till en ansikte i en angiven stor ansikts lista, upp till 1 000 000 ansikten. Uppdatera ett angivet ansiktes userData-fält i en stor ansikts lista efter dess persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/read | Hämta en stor ansikts List largeFaceListId, Name, userData. Lista med stora FACET visar information om largeFaceListId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/train/action | Skicka en övnings uppgift för stor ansikts lista. Utbildning är ett viktigt steg för att endast en tränad stor ansikts lista kan använda. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/training/read | För att kontrol lera att inlärnings statusen för den stora listan är klar eller fortfarande pågår. LargeFaceList-utbildning är en asynkron åtgärd |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largefacelists/write | Skapa en tom stor ansikts lista med användarens angivna largeFaceListId, namn och valfritt userData. Uppdatera information om en stor ansikts lista, inklusive namn och userData. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largepersongroups/Delete | Ta bort en befintlig stor person grupp med angiven personGroupId. Sparade data i den här stora person gruppen tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/action | Skapa en ny person i en angiven stor person grupp. Om du vill lägga till ansikte till den här personen ringer du |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largepersongroups/personer/Delete | Ta bort en befintlig person från en stor person grupp. Alla lagrade person data och ansikts bilder i person posten tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/delete | Ta bort ett ansikte från en person i en stor person grupp. Ansikts data och bild som är relaterade till den här ansikts posten tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/persistedfaces/read | Hämta information om personens ansikte. Den beständiga personens ansikte anges av dess largePersonGroupId, personId och persistedFaceId. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largepersongroups/personals/persistedfaces/Write | Lägg till en ansikts bild till en person i en stor person grupp för ansikts identifiering eller verifiering. För att hantera avbildningen av uppdatera en person bestående ansikte i fältet userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/read | Hämta en persons namn och userData och den beständiga faceIds som representerar den registrerade personens ansikts bild. Visa alla person uppgifter i den angivna gruppen med stora personer, inklusive personId, Name, userData och persistedFaceIds. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/persons/write | Uppdatera namn eller userData för en person. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largepersongroups/Read | Hämta information om en stor person grupp, inklusive dess namn och userData. Det här API: et returnerar en stor person grupps information som visar alla befintliga grupper med largePesonGroupId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/largepersongroups/train/action | Skicka in en grupp utbildnings uppgift för stor person. Utbildning är ett viktigt steg som bara en utbildad stor persons grupp kan använda. |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largepersongroups/Training/Read | För att kontrol lera att den stora person gruppens utbildnings status är slutförd eller fortfarande kontinuerlig. LargePersonGroup-utbildning är en asynkron åtgärd |
> | DataAction | Microsoft. CognitiveServices/Accounts/Face/largepersongroups/Write | Skapa en ny stor person grupp med användardefinierat largePersonGroupId, namn och valfritt userData. Uppdatera en befintlig stor persons grupps namn och userData. Egenskaperna sparas oförändrade om de inte finns i begär ande texten. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/delete | Ta bort en befintlig person grupp med angivet personGroupId. Sparade data i den här person gruppen tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/action | Skapa en ny person i en angiven person grupp. Om du vill lägga till ansikte till den här personen ringer du |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/delete | Ta bort en befintlig person från en person grupp. Alla lagrade person data och ansikts bilder i person posten tas bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/delete | Ta bort ett ansikte från en person i en person grupp. Ansikts data och bild som är relaterade till den här ansikts posten tas också bort. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/read | Hämta information om personens ansikte. Den beständiga personens ansikte anges av dess personGroupId, personId och persistedFaceId. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/persistedfaces/write | Lägg till en ansikts bild till en person i en person grupp för ansikts identifiering eller verifiering. För att hantera avbildningen av flera uppdateringar är det en person bestående av en person som är i fältet userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/read | Hämta en persons namn och userData och den beständiga faceIds som representerar den registrerade personens ansikts bild. Visa alla person uppgifter i den angivna person gruppen, inklusive personId, namn, userData och persistedFaceIds för registrerade. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/persons/write | Uppdatera namn eller userData för en person. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/read | Hämta person grupps namn och userData. Om du vill hämta person uppgifter under den här personGroup använder du List person gruppernas pesonGroupId, namn och userData. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/train/action | Skicka en utbildnings uppgift för person grupper. Utbildning är ett viktigt steg som bara en utbildad person grupp kan använda. |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/training/read | För att kontrol lera att person gruppens utbildnings status har slutförts eller fortfarande pågår. PersonGroup-utbildning är en asynkron åtgärd som utlöses |
> | DataAction | Microsoft.CognitiveServices/accounts/Face/persongroups/write | Skapa en ny person grupp med angivet personGroupId, namn och användar-angivet userData. Uppdatera en befintlig person grupps namn och userData. Egenskaperna sparas oförändrade om de inte finns i begär ande texten. |
> | DataAction | Microsoft. CognitiveServices/konton/ansikte/verifiera/åtgärd | Kontrol lera om två ansikten tillhör en person eller om en ansikte tillhör en person. |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/Custom/Models/analysera/åtgärd | Extrahera nyckel/värde-par från ett givet dokument. Indata-dokumentet måste vara av en av de innehålls typer som stöds: "Application/PDF", "image/jpeg" eller "image/png". Ett lyckat svar returneras i JSON. |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/Custom/Models/Delete | Ta bort modell artefakter. |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/Custom/Models/Keys/Read | Hämta nycklar för modellen. |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/Custom/Models/Read | Hämta information om en modell. Få information om alla tränade anpassade modeller * |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/Custom/träna/Action | Skapa och träna en anpassad modell.<br>Ombordpersonalen måste innehålla en käll parameter som antingen är en extern tillgänglig Azure Storage BLOB container-URI (helst en signatur-URI för delad åtkomst) eller en giltig sökväg till en datamapp på en lokalt monterad enhet.<br>När du har angett lokala sökvägar måste de följa formatet för Linux/UNIX-sökvägen och vara en absolut sökväg som är rotad till monterings konfigurationen för indatakälla |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/fördefinierad/inlevns/asyncbatchanalyze/åtgärd | Extrahera fält text och semantiska värden från ett givet kvitto dokument. Det inmatade bild dokumentet måste vara en av de innehålls typer som stöds – JPEG, PNG, BMP, PDF eller TIFF. Ett lyckat svar är en JSON som innehåller ett fält med namnet "operation-location", som innehåller URL: en för åtgärden Hämta ingångs resultat till asynkront Hämta resultaten. |
> | DataAction | Microsoft. CognitiveServices/Accounts/FormRecognizer/rekonstruerad/inlevns/Operations/Action | Fråga efter status och hämta resultatet av en åtgärd för att analysera inleverans. URL: en till det här gränssnittet kan hämtas från "operation-location"-huvudet i svaret för att analysera kvittot. |
> | DataAction | Microsoft. CognitiveServices/konton/ImageSearch/information/åtgärd | Returnerar insikter om en bild, till exempel webb sidor som innehåller bilden. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ImageSearch/search/Action | Hämta relevanta avbildningar för en specifik fråga. |
> | DataAction | Microsoft. CognitiveServices/konton/ImageSearch/trending/åtgärd | Hämta bilder för utveckling. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ImmersiveReader/getcontentmodelforreader/Action | Skapar en avancerad spårningssession |
> | DataAction | Microsoft. CognitiveServices/konton/InkRecognizer/identifiera/åtgärd | Med en uppsättning linje data analyseras innehållet och en lista över identifierade entiteter genereras, inklusive identifierad text. |
> | Åtgärd | Microsoft. CognitiveServices/Accounts/Listnycklar/Action | Lista nycklar |
> | DataAction | Microsoft. CognitiveServices/Accounts/LUIS/predict/Action | Hämtar den publicerade slut punkts förutsägelsen för den givna frågan. |
> | DataAction | Microsoft. CognitiveServices/Accounts/NewsSearch/categorysearch/Action | Returnerar nyheter för en angiven kategori. |
> | DataAction | Microsoft. CognitiveServices/Accounts/NewsSearch/search/Action | Hämta nyhets artiklar som är relevanta för en specifik fråga. |
> | DataAction | Microsoft. CognitiveServices/Accounts/NewsSearch/trendingtopics/Action | Hämta trender för ämnen som identifieras av Bing. Detta är samma ämnen som visas i informations fältet längst ned på Bing-startsidan. |
> | Åtgärd | Microsoft. CognitiveServices/konton/läsa | Läser API-konton. |
> | Åtgärd | Microsoft. CognitiveServices/Accounts/regenerateKey/Action | Återskapa nyckel |
> | Åtgärd | Microsoft.CognitiveServices/accounts/skus/read | Läser tillgängliga SKU: er för en befintlig resurs. |
> | DataAction | Microsoft. CognitiveServices/konton/stavning/stavning/åtgärd | Få resultatet av en stavnings kontroll fråga via GET eller POST. |
> | DataAction | Microsoft. CognitiveServices/konton/TextAnalytics/entiteter/åtgärd | API: t returnerar en lista över kända entiteter och allmänna namngivna entiteter (\"person\", \"plats\", \"organisation\" o.s.v.) i ett visst dokument. |
> | DataAction | Microsoft. CognitiveServices/Accounts/TextAnalytics/-fras/åtgärd | API:t returnerar en lista med strängar som anger samtalsämnen i den inmatade texten. |
> | DataAction | Microsoft. CognitiveServices/konton/TextAnalytics/språk/åtgärd | API:t returnerar det identifierade språket och en poäng mellan 0 och 1. 1 poäng visar att korrekt språk har identifierats med 100 procents säkerhet. 120 språk stöds totalt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/TextAnalytics/sentiment/Action | API:t returnerar en poäng mellan 0 och 1.<br>Poäng nära 1 anger en positiv attityd, medan poäng nära 0 anger en negativ attityd.<br>Poängen på 0,5 anger att sentiment saknas (t. ex.<br>ett factoid-uttryck). |
> | Åtgärd | Microsoft. CognitiveServices/konton/användning/Läs | Hämta kvot användning för en befintlig resurs. |
> | DataAction | Microsoft. CognitiveServices/konton/VideoSearch/information/åtgärd | Få insikter om en video, till exempel relaterade videor. |
> | DataAction | Microsoft. CognitiveServices/Accounts/VideoSearch/search/Action | Hämta videor som är relevanta för en specifik fråga. |
> | DataAction | Microsoft. CognitiveServices/konton/VideoSearch/trending/åtgärd | Hämta videor för utveckling. |
> | DataAction | Microsoft. CognitiveServices/Accounts/VisualSearch/search/Action | Returnerar en lista med taggar som är relevanta för den angivna avbildningen |
> | DataAction | Microsoft. CognitiveServices/Accounts/WebSearch/Search/Action | Hämta webb-, bild-, diskussions &s resultat för en specifik fråga. |
> | Åtgärd | Microsoft. CognitiveServices/konton/Skriv | Skriver API-konton. |
> | Åtgärd | Microsoft.CognitiveServices/checkDomainAvailability/action | Läser tillgängliga SKU: er för en prenumeration. |
> | Åtgärd | Microsoft. CognitiveServices/locations/checkSkuAvailability/Action | Läser tillgängliga SKU: er för en prenumeration. |
> | Åtgärd | Microsoft. CognitiveServices/locations/checkSkuAvailability/Action | Läser tillgängliga SKU: er för en prenumeration. |
> | Åtgärd | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Meddelande från Microsoft. Network för att ta bort VirtualNetworks eller undernät. |
> | Åtgärd | Microsoft. CognitiveServices/locations/operationresults/Read | Läs status för en asynkron åtgärd. |
> | Åtgärd | Microsoft.CognitiveServices/Operations/read | Visa en lista över alla tillgängliga åtgärder |
> | Åtgärd | Microsoft. CognitiveServices/register/åtgärd | Registrerar prenumeration för Cognitive Services |
> | Åtgärd | Microsoft. CognitiveServices/register/åtgärd | Registrerar prenumeration för Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Commerce/RateCard/Read | Returnerar erbjudande data, metadata för resurs/mätare och priser för den aktuella prenumerationen. |
> | Åtgärd | Microsoft. Commerce/register/åtgärd | Registrera prenumeration för Microsoft Commerce-UsageAggregate |
> | Åtgärd | Microsoft. Commerce/avregistrering/åtgärd | Avregistrera prenumerationen för Microsoft Commerce-UsageAggregate |
> | Åtgärd | Microsoft.Commerce/UsageAggregates/read | Hämtar Microsoft Azures förbrukning av en prenumeration. Resultatet innehåller mängd användnings data, prenumeration och resursinformation inom ett visst tidsintervall. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Compute/availabilitySets/Delete | Tar bort tillgänglighets uppsättningen |
> | Åtgärd | Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighets uppsättning |
> | Åtgärd | Microsoft. Compute/availabilitySets/tillåtna storlekar/Read | Lista tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i tillgänglighets uppsättningen |
> | Åtgärd | Microsoft. Compute/availabilitySets/Write | Skapar en ny tillgänglighets uppsättning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/diskEncryptionSets/Delete | Ta bort en disk krypterings uppsättning |
> | Åtgärd | Microsoft. Compute/diskEncryptionSets/Read | Hämta egenskaperna för en disk krypterings uppsättning |
> | Åtgärd | Microsoft. Compute/diskEncryptionSets/Write | Skapa en ny disk krypterings uppsättning eller uppdatera en befintlig |
> | Åtgärd | Microsoft.Compute/disks/beginGetAccess/action | Hämta SAS-URI för disken för BLOB-åtkomst |
> | Åtgärd | Microsoft. Compute/disks/Delete | Tar bort disken |
> | Åtgärd | Microsoft.Compute/disks/endGetAccess/action | Återkalla SAS-URI för disken |
> | Åtgärd | Microsoft. Compute/disks/Read | Hämta egenskaperna för en disk |
> | Åtgärd | Microsoft. Compute/disks/Write | Skapar en ny disk eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/Galleri/program/ta bort | Tar bort Galleri programmet |
> | Åtgärd | Microsoft. Compute/Galleri/program/läsa | Hämtar egenskaper för Galleri program |
> | Åtgärd | Microsoft. Compute/Galleri/program/versioner/Delete | Tar bort galleriets program version |
> | Åtgärd | Microsoft. Compute/Galleri/program/versioner/läsa | Hämtar egenskaperna för Galleri program version |
> | Åtgärd | Microsoft. Compute/Galleri/program/versioner/skriva | Skapar en ny Galleri program version eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/Galleri/program/Skriv | Skapar ett nytt Galleri program eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft. Compute/Galleri/ta bort | Tar bort galleriet |
> | Åtgärd | Microsoft. Compute/Galleri/bilder/ta bort | Tar bort Galleri bilden |
> | Åtgärd | Microsoft. Compute/Galleri/bilder/läsa | Hämtar egenskaperna för Galleri bilden |
> | Åtgärd | Microsoft. Compute/Galleri/images/versions/Delete | Tar bort Galleri avbildnings versionen |
> | Åtgärd | Microsoft.Compute/galleries/images/versions/read | Hämtar egenskaperna för Galleri avbildnings version |
> | Åtgärd | Microsoft. Compute/Galleri/avbildningar/versioner/skriva | Skapar en ny Galleri avbildnings version eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/Galleri/bilder/skriva | Skapar en ny Galleri avbildning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/Galleri/läsa | Hämtar egenskaperna för galleriet |
> | Åtgärd | Microsoft. Compute/Galleri/skriva | Skapar ett nytt Galleri eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft. Compute/hostGroups/Delete | Tar bort värd gruppen |
> | Åtgärd | Microsoft. Compute/hostGroups/hosts/Delete | Tar bort värden |
> | Åtgärd | Microsoft. Compute/hostGroups/hosts/Read | Hämta egenskaperna för en värd |
> | Åtgärd | Microsoft. Compute/hostGroups/hosts/Write | Skapar en ny värd eller uppdaterar en befintlig värd |
> | Åtgärd | Microsoft. Compute/hostGroups/Read | Hämta egenskaperna för en värd grupp |
> | Åtgärd | Microsoft.Compute/hostGroups/write | Skapar en ny värd grupp eller uppdaterar en befintlig värd grupp |
> | Åtgärd | Microsoft. Compute/images/Delete | Tar bort avbildningen |
> | Åtgärd | Microsoft. Compute/images/Read | Hämta egenskaperna för avbildningen |
> | Åtgärd | Microsoft. Compute/images/Write | Skapar en ny avbildning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/locations/capsOperations/Read | Hämtar status för en asynkron CAPS-åtgärd |
> | Åtgärd | Microsoft. Compute/locations/diskOperations/Read | Hämtar status för en asynkron disk åtgärd |
> | Åtgärd | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Skapa loggar för att Visa totalt antal förfrågningar per tidsintervall för att begränsa diagnostiken. |
> | Åtgärd | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Skapa loggar för att Visa mängder med begränsade begär Anden grupperade efter ResourceName, OperationName eller principen tillämpad begränsning. |
> | Åtgärd | Microsoft. Compute/locations/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft. Compute/locations/Publishers/artifacttypes/erbjudande/läsa | Hämta egenskaperna för ett plattforms avbildnings erbjudande |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Hämta egenskaperna för en plattforms avbildnings-SKU |
> | Åtgärd | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Hämta egenskaperna för en plattforms avbildnings version |
> | Åtgärd | Microsoft. Compute/locations/Publishers/artifacttypes/types/Read | Hämta egenskaperna för en VMExtension-typ |
> | Åtgärd | Microsoft. Compute/locations/utgivare/artifacttypes/types/versions/Read | Hämta egenskaperna för en VMExtension-version |
> | Åtgärd | Microsoft. Compute/locations/Publisher/Read | Hämta egenskaperna för en utgivare |
> | Åtgärd | Microsoft. Compute/locations/runCommands/Read | Visar en lista över tillgängliga körnings kommandon på platsen |
> | Åtgärd | Microsoft. Compute/locations/usages/Read | Hämtar tjänst gränser och aktuella användnings antal för prenumerationens beräknings resurser på en plats |
> | Åtgärd | Microsoft.Compute/locations/vmSizes/read | Visar en lista över tillgängliga storlekar för virtuella datorer på en plats |
> | Åtgärd | Microsoft. Compute/locations/vsmOperations/Read | Hämtar status för en asynkron åtgärd för skalnings uppsättningen för den virtuella datorn med tillägget för körnings tjänsten för virtuell dator |
> | Åtgärd | Microsoft. Compute/Operations/Read | Visar en lista över åtgärder som är tillgängliga på Microsoft. Compute Resource Provider |
> | Åtgärd | Microsoft. Compute/proximityPlacementGroups/Delete | Tar bort placerings gruppen för närhet |
> | Åtgärd | Microsoft.Compute/proximityPlacementGroups/read | Hämta egenskaperna för en närhets placerings grupp |
> | Åtgärd | Microsoft.Compute/proximityPlacementGroups/write | Skapar en ny närhets grupp eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/register/åtgärd | Registrerar prenumeration med Microsoft. Compute Resource Provider |
> | Åtgärd | Microsoft. Compute/restorePointCollections/Delete | Tar bort samlingen med återställnings punkter och innehåller återställnings punkter |
> | Åtgärd | Microsoft. Compute/restorePointCollections/Read | Hämta egenskaperna för en återställnings punkt samling |
> | Åtgärd | Microsoft. Compute/restorePointCollections/restorePoints/Delete | Tar bort återställnings punkten |
> | Åtgärd | Microsoft. Compute/restorePointCollections/restorePoints/Read | Hämta egenskaperna för en återställnings punkt |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Hämta egenskaperna för en återställnings punkt tillsammans med BLOB SAS-URI: er |
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/write | Creates a new restore point |
> | Åtgärd | Microsoft. Compute/restorePointCollections/Write | Skapar en ny återställnings punkt samling eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Compute/sharedVMImages/Delete | Tar bort SharedVMImage |
> | Åtgärd | Microsoft. Compute/sharedVMImages/Read | Hämta egenskaperna för en SharedVMImage |
> | Åtgärd | Microsoft. Compute/sharedVMImages/versions/Delete | Ta bort en SharedVMImageVersion |
> | Åtgärd | Microsoft. Compute/sharedVMImages/versions/Read | Hämta egenskaperna för en SharedVMImageVersion |
> | Åtgärd | Microsoft. Compute/sharedVMImages/versioner/replikera/åtgärd | Replikera en SharedVMImageVersion till mål regioner |
> | Åtgärd | Microsoft. Compute/sharedVMImages/versions/Write | Skapa en ny SharedVMImageVersion eller uppdatera en befintlig |
> | Åtgärd | Microsoft. Compute/sharedVMImages/Write | Skapar en ny SharedVMImage eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Compute/skus/read | Hämtar listan över Microsoft. Compute-SKU: er som är tillgängliga för din prenumeration |
> | Åtgärd | Microsoft.Compute/snapshots/beginGetAccess/action | Hämta SAS-URI: n för ögonblicks bilden för BLOB-åtkomst |
> | Åtgärd | Microsoft. Compute/ögonblicks bilder/ta bort | Ta bort en ögonblicks bild |
> | Åtgärd | Microsoft.Compute/snapshots/endGetAccess/action | Återkalla SAS-URI för ögonblicks bilden |
> | Åtgärd | Microsoft. Compute/ögonblicks bilder/läsa | Hämta egenskaperna för en ögonblicks bild |
> | Åtgärd | Microsoft. Compute/ögonblicks bilder/skrivning | Skapa en ny ögonblicks bild eller uppdatera en befintlig |
> | Åtgärd | Microsoft. Compute/unregister/åtgärd | Avregistrerar prenumeration med Microsoft. Compute Resource Provider |
> | Åtgärd | Microsoft.Compute/virtualMachines/capture/action | Avbildar den virtuella datorn genom att kopiera virtuella hård diskar och genererar en mall som kan användas för att skapa liknande virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konverterar BLOB-baserade diskar för den virtuella datorn till Managed disks |
> | Åtgärd | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräknings resurserna |
> | Åtgärd | Microsoft.Compute/virtualMachines/delete | Tar bort den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/delete | Tar bort tillägget för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/read | Hämta egenskaperna för ett tillägg för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/extensions/write | Skapar ett nytt tillägg för virtuell dator eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.Compute/virtualMachines/generalize/action | Ställer in den virtuella datorns tillstånd till generaliserad och förbereder den virtuella datorn för avbildning |
> | Åtgärd | Microsoft. Compute/virtualMachines/instanceView/Read | Hämtar den detaljerade körnings statusen för den virtuella datorn och dess resurser |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med Windows-administratör eller Linux rot användar privilegier |
> | Åtgärd | Microsoft. Compute/virtualMachines/performMaintenance/Action | Utför underhålls åtgärder på den virtuella datorn. |
> | Åtgärd | Microsoft.Compute/virtualMachines/powerOff/action | Stänger av den virtuella datorn. Observera att den virtuella datorn fortsätter att faktureras. |
> | Åtgärd | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/redeploy/action | Distribuerar om virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachines/reimage/action | Avbildningar av en virtuell dator som använder differentierings disk. |
> | Åtgärd | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/runCommand/action | Kör ett fördefinierat skript på den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar som den virtuella datorn kan uppdateras till |
> | Åtgärd | Microsoft.Compute/virtualMachines/write | Skapar en ny virtuell dator eller uppdaterar en befintlig virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Stänger av och frigör beräknings resurserna för instanser av skalnings uppsättningen för den virtuella datorn  |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/delete | Tar bort skalnings uppsättningen för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/delete/action | Tar bort instanser av skalnings uppsättningen för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Tar bort tillägget för skalnings uppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Hämtar egenskaperna för ett tillägg för skalnings uppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Skapar ett nytt tillägg för skalnings uppsättning för virtuell dator eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Gå manuellt till plattforms uppdaterings domänerna för en skalnings uppsättning för den virtuella Service Fabric-datorn för att slutföra en väntande uppdatering som fastnar |
> | Åtgärd | Microsoft. Compute/virtualMachineScaleSets/instanceView/Read | Hämtar instans visningen av den virtuella datorns skal uppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Uppdaterar manuellt instanser till den senaste modellen av den virtuella datorns skal uppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Hämta egenskaper för alla nätverks gränssnitt för en skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Startar en rullande uppgradering för att flytta alla instanser av den virtuella datorns skalnings uppsättning till den senaste tillgängliga plattforms avbildningens OS-version. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Hämtar historiken för OS-uppgraderingar för en skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft. Compute/virtualMachineScaleSets/performMaintenance/Action | Utför planerat underhåll på instanser av skalnings uppsättningen för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Stänger av instanser av skalnings uppsättningen för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Hämta egenskaper för alla offentliga IP-adresser för en skalnings uppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/read | Hämta egenskaperna för en skalnings uppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Distribuera om instanserna för den virtuella datorns skal uppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Avbildning av instanser av skalnings uppsättningen för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Återskapar avbildningar av alla diskar (operativ system disk och data diskar) för instanser av en skalnings uppsättning för virtuella datorer  |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/restart/action | Startar om instanserna för den virtuella datorns skal uppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Avbryter den löpande uppgraderingen av en skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Hämta den senaste rullande uppgraderings statusen för en skalnings uppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/scale/action | Kontrol lera om en befintlig skalnings uppsättning för virtuell dator kan skalas in i/ut till angivet instans antal |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/skus/read | Visar en lista med giltiga SKU: er för en befintlig virtuell dators skalnings uppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/start/action | Startar instanser av skalnings uppsättningen för den virtuella datorn |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Stänger av och frigör beräknings resurserna för en virtuell dator i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Ta bort en enskild virtuell dator i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/instanceView/Read | Hämtar instans visningen av en virtuell dator i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Hämta egenskaper för offentlig IP-adress som skapats med skalnings uppsättning för virtuella datorer. Skalnings uppsättningen för virtuella datorer kan bara skapa en offentlig IP-adress per ipconfiguration (privat IP) |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Hämta egenskaper för en eller alla IP-konfigurationer för ett nätverks gränssnitt som skapats med skalnings uppsättning för virtuella datorer. IP-konfigurationer representerar privata IP-adresser |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Hämta egenskaper för ett eller alla nätverks gränssnitt för en virtuell dator som skapats med skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Utför planerat underhåll på en virtuell dator instans i en skalnings uppsättning för virtuell dator |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Stänger av en virtuell dator instans i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Hämtar egenskaperna för en virtuell dator i en skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Distribuerar om en virtuell dator instans i en skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Avbildningar av en virtuell dator instans i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Återskapar avbildningar av alla diskar (operativ system disk och data diskar) för virtuell dator instans i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Startar om en virtuell dator instans i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Kör ett fördefinierat skript på en virtuell dator instans i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Startar en virtuell dator instans i en skalnings uppsättning för virtuella datorer. |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Uppdaterar egenskaperna för en virtuell dator i en skalnings uppsättning för virtuella datorer |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Lista tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i den virtuella datorns skal uppsättning |
> | Åtgärd | Microsoft.Compute/virtualMachineScaleSets/write | Skapar en ny skalnings uppsättning för virtuell dator eller uppdaterar en befintlig |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. förbrukning/aggregatedcost/Read | Visa AggregatedCost för hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/saldo/Läs | Visa en lista över användnings sammanfattningen för en fakturerings period för en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/budget/ta bort | Ta bort budgetarna med en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/budget/Läs | Lista budgetarna efter en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/budget/Skriv | Skapar och uppdaterar budgetarna av en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/avgifter/Läs | Lista avgifter |
> | Åtgärd | Microsoft. förbrukning/krediter/Läs | Lista krediter |
> | Åtgärd | Microsoft. förbrukning/händelser/läsa | Lista händelser |
> | Åtgärd | Microsoft. förbrukning/externalBillingAccounts/tagg/Read | Visa taggar för EA och prenumerationer. |
> | Åtgärd | Microsoft. förbrukning/externalSubscriptions/tagg/Read | Visa taggar för EA och prenumerationer. |
> | Åtgärd | Microsoft. förbrukning/prognoser/läsning | Lista prognoser |
> | Åtgärd | Microsoft. förbrukning/massor/läsning | Lista partier |
> | Åtgärd | Microsoft. förbrukning/marknads platser/läsa | Visa information om resursanvändning för Marketplace för en omfattning för EA-och WebDirect-prenumerationer. |
> | Åtgärd | Microsoft. förbrukning/operationresults/Read | Visa lista operationresults |
> | Åtgärd | Microsoft. förbrukning/åtgärder/läsa | Visa en lista över alla åtgärder som stöds av Microsoft. förbruknings resurs leverantör. |
> | Åtgärd | Microsoft. förbrukning/operationstatus/Read | Visa lista operationstatus |
> | Åtgärd | Microsoft. förbrukning/pricesheets/Read | Visa en lista över Pricesheets-data för en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/register/åtgärd | Registrera till förbrukning RP |
> | Åtgärd | Microsoft.Consumption/reservationDetails/read | Visar användnings information för reserverade instanser med hjälp av en reservations order eller hanterings grupper. Informations data är per instans per dag nivå. |
> | Åtgärd | Microsoft.Consumption/reservationRecommendations/read | Lista enkla eller delade rekommendationer för reserverade instanser för en prenumeration. |
> | Åtgärd | Microsoft. förbrukning/reservationSummaries/Read | Visa en lista över användnings översikten för reserverade instanser med hjälp av reservations order eller hanterings grupper. Sammanfattnings data är antingen på varje månad eller per dag. |
> | Åtgärd | Microsoft.Consumption/reservationTransactions/read | Lista transaktions historiken för reserverade instanser efter hanterings grupper. |
> | Åtgärd | Microsoft. förbrukning/Taggar/läsa | Visa taggar för EA och prenumerationer. |
> | Åtgärd | Microsoft. förbrukning/klienter/läsning | Lista klienter |
> | Åtgärd | Microsoft. förbrukning/klient organisationer/register/åtgärd | Registrera åtgärds område för Microsoft. förbrukning av en klient. |
> | Åtgärd | Microsoft. förbrukning/villkor/läsa | Lista villkoren för en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/usageDetails/Read | Visar användnings information för en omfattning för EA-och WebDirect-prenumerationer. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/containers/buildlogs/Read | Hämta build-loggar för en speciell behållare. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exec till en speciell behållare. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/containers/logs/Read | Hämta loggar för en speciell behållare. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/Delete | Ta bort den angivna behållar gruppen. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/operationResults/Read | Hämta asynkron åtgärds resultat |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för behållar gruppen. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för behållar gruppen. |
> | Åtgärd | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för behållar gruppen. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/Read | Hämta alla behållar grupper. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/restart/Action | Startar om en angiven behållar grupp. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/start/Action | Startar en speciell behållar grupp. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/stopp/åtgärd | Stoppar en angiven behållar grupp. Beräknings resurser frigörs och faktureringen stoppas. |
> | Åtgärd | Microsoft. ContainerInstance/containerGroups/Write | Skapa eller uppdatera en angiven behållar grupp. |
> | Åtgärd | Microsoft. ContainerInstance/locations/cachedImages/Read | Hämtar de cachelagrade avbildningarna för prenumerationen i en region. |
> | Åtgärd | Microsoft. ContainerInstance/locations/capabilites/Read | Hämta funktionerna för en region. |
> | Åtgärd | Microsoft. ContainerInstance/locations/deleteVirtualNetworkOrSubnets/Action | Meddelar Microsoft. ContainerInstance att det virtuella nätverket eller under nätet tas bort. |
> | Åtgärd | Microsoft. ContainerInstance/locations/Operations/Read | Visa en lista över åtgärder för Azure Container instance service. |
> | Åtgärd | Microsoft. ContainerInstance/platser/användning/läsa | Hämta användningen för en speciell region. |
> | Åtgärd | Microsoft. ContainerInstance/Operations/Read | Visa en lista över åtgärder för Azure Container instance service. |
> | Åtgärd | Microsoft. ContainerInstance/register/åtgärd | Registrerar prenumerationen för resurs leverantören för behållar instansen och gör det möjligt att skapa behållar grupper. |
> | Åtgärd | Microsoft. ContainerInstance/serviceassociationlinks/Delete | Ta bort tjänst kopplings länken som skapats av resurs leverantören för Azure Container instance i ett undernät. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontrollerar om behållar register namnet är tillgängligt för användning. |
> | Åtgärd | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft. ContainerRegistry att det virtuella nätverket eller under nätet tas bort |
> | Åtgärd | Microsoft.ContainerRegistry/locations/operationResults/read | Hämtar ett asynkront åtgärds resultat |
> | Åtgärd | Microsoft.ContainerRegistry/operations/read | Visar en lista över alla tillgängliga Azure Container Registry REST API åtgärder |
> | Åtgärd | Microsoft.ContainerRegistry/register/action | Registrerar prenumerationen för resurs leverantören för behållar registret och gör det möjligt att skapa behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/artefakter/ta bort | Ta bort artefakt i ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/cancel/action | Avbryter en befintlig version. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Hämtar en länk för att hämta versions loggarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/builds/read | Hämtar egenskaperna för den angivna versionen eller visar alla build-versioner för det angivna behållar registret. |
> | Åtgärd | Microsoft. ContainerRegistry/register/build/Write | Uppdaterar en version av ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/Delete | Tar bort en build-aktivitet från ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/listSourceRepositoryProperties/åtgärd | Visar en lista över käll kontroll egenskaper för en build-uppgift. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/Read | Hämtar egenskaperna för den angivna Bygg uppgiften eller visar alla Bygg aktiviteter för det angivna behållar registret. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/steg/Delete | Tar bort ett build-steg från en build-uppgift. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/steg/listBuildArguments/åtgärd | Visar en lista med build-argument för ett build-steg inklusive de hemliga argumenten. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/steg/läsa | Hämtar egenskaperna för det angivna build-steget eller visar en lista över alla build-steg för den angivna build-aktiviteten. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/steg/Skriv | Skapar eller uppdaterar ett build-steg för en build-aktivitet med de angivna parametrarna. |
> | Åtgärd | Microsoft. ContainerRegistry/register/buildTasks/Write | Skapar eller uppdaterar en build-uppgift för ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft. ContainerRegistry/register/ta bort | Tar bort ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Tar bort ett event Grid-filter från ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Hämtar egenskaperna för det angivna Event Grid-filtret eller visar alla Event Grid-filter för det angivna behållar registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Skapar eller uppdaterar ett event Grid-filter för ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft. ContainerRegistry/register/generateCredentials/åtgärd | Generera nycklar för en token för ett angivet behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Hämtar överförings platsen för användaren att kunna överföra källan. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/importImage/action | Importera avbildningen till behållar registret med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Hämta URL-plats för överföring av källa för ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listCredentials/action | Visar inloggnings uppgifter för det angivna behållar registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listPolicies/read | Visar en lista över principer för det angivna behållar registret |
> | Åtgärd | Microsoft.ContainerRegistry/registries/listUsages/read | Visar kvot användningarna för det angivna behållar registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/metadata/read | Hämtar metadata för en speciell lagrings plats för ett behållar register |
> | Åtgärd | Microsoft.ContainerRegistry/registries/metadata/write | Uppdaterar metadata för en lagrings plats för ett behållar register |
> | Åtgärd | Microsoft.ContainerRegistry/registries/operationStatuses/read | Hämtar åtgärds status för asynkrona register |
> | Åtgärd | Microsoft.ContainerRegistry/registries/pull/read | Hämta eller hämta avbildningar från ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/push/write | Push-överför eller Skriv avbildningar till ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/karantän/läsa | Hämta eller hämta bilder i karantän från container Registry |
> | Åtgärd | Microsoft. ContainerRegistry/register/karantän/skrivning | Skriv/ändra karantän tillstånd för karantän avbildningar |
> | Åtgärd | Microsoft.ContainerRegistry/registries/queueBuild/action | Skapar en ny version utifrån parametrarna för begäran och lägger till den i versions kön. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/read | Hämtar egenskaperna för det angivna behållar registret eller visar alla behållar register under den angivna resurs gruppen eller prenumerationen. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Återskapar en av inloggnings uppgifterna för det angivna behållar registret. |
> | Åtgärd | Microsoft. ContainerRegistry/register/replikeringar/ta bort | Tar bort en replikering från ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Hämtar en asynkron åtgärds status för replikering |
> | Åtgärd | Microsoft.ContainerRegistry/registries/replications/read | Hämtar egenskaperna för den angivna replikeringen eller listar alla replikeringar för det angivna behållar registret. |
> | Åtgärd | Microsoft. ContainerRegistry/register/replikeringar/skrivning | Skapar eller uppdaterar en replikering för ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft. ContainerRegistry/register/kör/Avbryt/åtgärd | Avbryt en befintlig körning. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Hämtar SAS-webbadressen för loggen för en körning. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/runs/read | Hämtar egenskaperna för en körning mot ett behållar register eller en lista över körningar. |
> | Åtgärd | Microsoft. ContainerRegistry/register/kör/Skriv | Uppdaterar en körning. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/scheduleRun/action | Schemalägg en körning mot ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/scopeMaps/Delete | Tar bort en omfångs karta från ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/scopeMaps/operationStatuses/Read | Hämtar en asynkron åtgärds status för en omfattnings karta. |
> | Åtgärd | Microsoft. ContainerRegistry/register/scopeMaps/Read | Hämtar egenskaperna för den angivna omfångs kartan eller visar en lista över alla omfångs mappningar för det angivna behållar registret. |
> | Åtgärd | Microsoft. ContainerRegistry/register/scopeMaps/Write | Skapar eller uppdaterar en omfattnings karta för ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/sign/write | Push/pull-metadata för innehålls förtroende för ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/uppgifter/ta bort | Tar bort en aktivitet för ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Visa en lista med alla uppgifter om en aktivitet för ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/read | Hämtar en uppgift för ett behållar register eller listar alla aktiviteter. |
> | Åtgärd | Microsoft. ContainerRegistry/register/uppgifter/Skriv | Skapar eller uppdaterar en uppgift för ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/token/Delete | Tar bort en token från ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/tokens/operationStatuses/Read | Hämtar en token asynkron åtgärds status. |
> | Åtgärd | Microsoft. ContainerRegistry/register/token/Read | Hämtar egenskaperna för den angivna token eller listar alla tokens för det angivna behållar registret. |
> | Åtgärd | Microsoft. ContainerRegistry/register/tokens/token/Write | Skapar eller uppdaterar en token för ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/updatePolicies/write | Uppdaterar principerna för det angivna behållar registret |
> | Åtgärd | Microsoft. ContainerRegistry/register/Webhooks/Delete | Tar bort en webhook från ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Hämtar konfigurationen av tjänst-URI och anpassade rubriker för webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Visar en lista över senaste händelser för den angivna webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Hämtar en webhook-åtgärd status för asynkron åtgärd |
> | Åtgärd | Microsoft. ContainerRegistry/register/Webhooks/ping/åtgärd | Utlöser en ping-händelse som ska skickas till webhooken. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/read | Hämtar egenskaperna för den angivna webhooken eller visar en lista över alla webhookar för det angivna behållar registret. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/webhooks/write | Skapar eller uppdaterar en webhook för ett behållar register med de angivna parametrarna. |
> | Åtgärd | Microsoft. ContainerRegistry/register/Skriv | Skapar eller uppdaterar ett behållar register med de angivna parametrarna. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. container service/containerServices/Delete | Tar bort en behållar tjänst |
> | Åtgärd | Microsoft. container service/containerServices/Read | Hämta en behållar tjänst |
> | Åtgärd | Microsoft. container service/containerServices/Write | Skapar en ny container tjänst eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. container service/locations/operationresults/Read | Hämtar status för ett asynkront åtgärds resultat |
> | Åtgärd | Microsoft. container service/locations/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft. container service/locations/Orchestration/Read | Visar en lista över de Dirigerare som stöds |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Hämta en hanterad kluster åtkomst profil efter rollnamn med hjälp av lista autentiseringsuppgifter |
> | Åtgärd | Microsoft.ContainerService/managedClusters/accessProfiles/read | Hämta en profil för hanterad kluster åtkomst per roll namn |
> | Åtgärd | Microsoft. container service/managedClusters/agentPools/Delete | Tar bort en modempool |
> | Åtgärd | Microsoft. container service/managedClusters/agentPools/Read | Hämtar en agent |
> | Åtgärd | Microsoft. container service/managedClusters/agentPools/upgradeProfiles/Read | Hämtar uppgraderings profilen för agenten |
> | Åtgärd | Microsoft. container service/managedClusters/agentPools/Write | Skapar en ny modempool eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. container service/managedClusters/availableAgentPoolVersions/Read | Hämtar tillgängliga versioner av klustret för agenter |
> | Åtgärd | Microsoft. container service/managedClusters/Delete | Tar bort ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/Detections/Read | Hämta hanterad kluster detektor |
> | Åtgärd | Microsoft. container service/managedClusters/diagnosticsState/Read | Hämtar det diagnostiska läget för klustret |
> | Åtgärd | Microsoft. container service/managedClusters/listClusterAdminCredential/Action | Visa en lista över clusterAdmin-autentiseringsuppgiften för ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/listClusterMonitoringUserCredential/Action | Visa en lista över clusterMonitoringUser-autentiseringsuppgiften för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/privateEndpointConnectionsApproval/Action | Determines if user is allowed to approve a private endpoint connection |
> | Åtgärd | Microsoft. container service/managedClusters/Read | Hämta ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Återställa AAD-profilen för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Återställa tjänstens huvud konto profil för ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/rotateClusterCertificates/Action | Rotera certifikat för ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/upgradeProfiles/Read | Hämtar uppgraderings profilen för klustret |
> | Åtgärd | Microsoft. container service/managedClusters/Write | Skapar ett nytt hanterat kluster eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft. container service/openShiftClusters/Delete | Ta bort ett öppet Shift-kluster |
> | Åtgärd | Microsoft. container service/openShiftClusters/Read | Hämta ett öppet Shift-kluster |
> | Åtgärd | Microsoft. container service/openShiftClusters/Write | Skapar ett nytt öppet Shift-kluster eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft. container service/openShiftManagedClusters/Delete | Ta bort ett öppet Skift hanterat kluster |
> | Åtgärd | Microsoft. container service/openShiftManagedClusters/Read | Hämta ett hanterat kluster med öppen Skift |
> | Åtgärd | Microsoft. container service/openShiftManagedClusters/Write | Skapar ett nytt hanterat kluster med öppen Skift eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft. Container Service/Operations/Read | Visar en lista över åtgärder som är tillgängliga på Microsoft. container service Resource Provider |
> | Åtgärd | Microsoft. container service/register/åtgärd | Registrerar prenumeration med Microsoft. container service Resource Provider |
> | Åtgärd | Microsoft.ContainerService/unregister/action | Avregistrerar prenumeration med Microsoft. container service Resource Provider |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. CostManagement/Alerts/Write | Uppdatera aviseringar. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/delete | Ta bort den angivna cloudConnector. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/read | Ange cloudConnectors för den autentiserade användaren. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/write | Skapa eller uppdatera angiven cloudConnector. |
> | Åtgärd | Microsoft. CostManagement/dimensions/Read | Visa en lista över alla dimensioner som stöds enligt ett definitions område. |
> | Åtgärd | Microsoft.CostManagement/exports/action | Kör den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/delete | Ta bort den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/read | Lista exporten efter omfattning. |
> | Åtgärd | Microsoft.CostManagement/exports/run/action | Kör exporter. |
> | Åtgärd | Microsoft.CostManagement/exports/write | Skapa eller uppdatera den angivna exporten. |
> | Åtgärd | Microsoft. CostManagement/externalBillingAccounts/dimension/Read | Visa en lista över alla dimensioner som stöds för externa BillingAccounts. |
> | Åtgärd | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Visa en lista över externalSubscriptions i en externalBillingAccount för den autentiserade användaren. |
> | Åtgärd | Microsoft. CostManagement/externalBillingAccounts/prediktion/åtgärd | Prognos användnings data för extern BillingAccounts. |
> | Åtgärd | Microsoft. CostManagement/externalBillingAccounts/prediktion/Read | Prognos användnings data för extern BillingAccounts. |
> | Åtgärd | Microsoft. CostManagement/externalBillingAccounts/fråga/åtgärd | Fråga efter användnings data för extern BillingAccounts. |
> | Åtgärd | Microsoft. CostManagement/externalBillingAccounts/Query/Read | Fråga efter användnings data för extern BillingAccounts. |
> | Åtgärd | Microsoft.CostManagement/externalBillingAccounts/read | Ange externalBillingAccounts för den autentiserade användaren. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/dimensions/Read | Visa en lista över alla dimensioner som stöds för extern prenumeration. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/prediktion/åtgärd | Prognos användnings data för extern BillingAccounts. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/prediktion/Read | Prognos användnings data för extern BillingAccounts. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/fråga/åtgärd | Fråga efter användnings data för extern prenumeration. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/Query/Read | Fråga efter användnings data för extern prenumeration. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/Read | Ange externalSubscriptions för den autentiserade användaren. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/Write | Uppdatera den associerade hanterings gruppen för externalSubscription |
> | Åtgärd | Microsoft. CostManagement/prediktion/åtgärd | Prognos användnings data med ett omfång. |
> | Åtgärd | Microsoft. CostManagement/prediktion/Read | Prognos användnings data med ett omfång. |
> | Åtgärd | Microsoft. CostManagement/Operations/Read | Visa en lista över alla åtgärder som stöds av Microsoft. CostManagement Resource Provider. |
> | Åtgärd | Microsoft.CostManagement/query/action | Fråga användnings data efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/query/read | Fråga användnings data efter ett omfång. |
> | Åtgärd | Microsoft. CostManagement/register/åtgärd | Registrera åtgärd för omfånget för Microsoft. CostManagement av en prenumeration. |
> | Åtgärd | Microsoft. CostManagement/Reports/Action | Schemalägg rapporter om användnings data med ett omfång. |
> | Åtgärd | Microsoft. CostManagement/Reports/Read | Schemalägg rapporter om användnings data med ett omfång. |
> | Åtgärd | Microsoft. CostManagement/Tenants/register/åtgärd | Registrera åtgärd för omfånget för Microsoft. CostManagement av en klient. |
> | Åtgärd | Microsoft. CostManagement/vyer/åtgärd | Skapa vy. |
> | Åtgärd | Microsoft. CostManagement/views/Delete | Ta bort sparade vyer. |
> | Åtgärd | Microsoft. CostManagement/views/Read | Lista alla sparade vyer. |
> | Åtgärd | Microsoft. CostManagement/vyer/skriva | Uppdatera vy. |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. data-/jobb/bookShipmentPickUp/åtgärd | Gör det möjligt att boka en plockning för retur leveranser. |
> | Åtgärd | Microsoft. data-/jobb/Avbryt/åtgärd | Avbryter en pågående order. |
> | Åtgärd | Microsoft. data-/jobb/ta bort | Ta bort order |
> | Åtgärd | Microsoft. data-/jobb/listCredentials/åtgärd | Visar en lista med okrypterade autentiseringsuppgifter relaterade till beställningen. |
> | Åtgärd | Microsoft. data-/jobb/läsa | Lista eller hämta order |
> | Åtgärd | Microsoft. data-/jobb/Skriv | Skapa eller uppdatera order |
> | Åtgärd | Microsoft. data-/plats/availableSkus/åtgärd | Den här metoden returnerar listan över tillgängliga SKU: er. |
> | Åtgärd | Microsoft. Data Center/locations/availableSkus/Read | Visa eller hämta tillgängliga SKU: er |
> | Åtgärd | Microsoft.DataBox/locations/operationResults/read | Visa eller hämta åtgärds resultat |
> | Åtgärd | Microsoft. data-/plats/regionConfiguration/åtgärd | Den här metoden returnerar konfigurationerna för regionen. |
> | Åtgärd | Microsoft. data-/plats/validateAddress/åtgärd | Verifierar leverans adressen och ger alternativa adresser om det finns några. |
> | Åtgärd | Microsoft. data-/plats/validateInputs/åtgärd | Den här metoden utför alla typer av valideringar. |
> | Åtgärd | Microsoft. data-/Operations/Read | Lista eller hämta åtgärderna |
> | Åtgärd | Microsoft. data-/register/åtgärd | Registrera providern Microsoft. data, |
> | Åtgärd | Microsoft. data-/prenumerations-resourceGroups/moveResources/åtgärd | Den här metoden utför resurs flytten. |
> | Åtgärd | Microsoft. data-/prenumerations-resourceGroups/validateMoveResources/åtgärd | Med den här metoden verifieras om resurs flyttning tillåts eller inte. |
> | Åtgärd | Microsoft. data-/avregistrera/åtgärd | Avregistrera providern Microsoft. data-providern |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Visar eller hämtar aviseringarna |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Tar bort bandbredds scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Listar eller hämtar bandbredds scheman |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Listar eller hämtar bandbredds scheman |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | Skapar eller uppdaterar bandbredds scheman |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Delete | Tar bort Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/Action | Hämta uppdateringar i enheten |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/Action | Hämtar utökad information för resurs |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/Action | Installera uppdateringar på enheten |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Jobs/Read | Visar eller hämtar jobben |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Visar eller hämtar nätverks inställningarna för enheten |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Nodes/Read | Listar eller hämtar noderna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | Visar eller hämtar åtgärds status |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Delete | Tar bort order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar eller hämtar order |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar eller hämtar order |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/order/Write | Skapar eller uppdaterar order |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Delete | Tar bort rollerna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Read | Lists or gets the roles |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Read | Lists or gets the roles |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/write | Creates or updates the roles |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/action | Scan for updates |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Update security settings |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/delete | Deletes the shares |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lists or gets the shares |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/read | Lists or gets the shares |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/refresh/action | Refresh the share metadata with the data from the cloud |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/write | Creates or updates the shares |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/delete | Deletes the storage account credentials |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lists or gets the storage account credentials |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/read | Lists or gets the storage account credentials |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/write | Creates or updates the storage account credentials |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/delete | Deletes the triggers |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Lists or gets the triggers |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/read | Lists or gets the triggers |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/write | Creates or updates the triggers |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/updateSummary/read | Lists or gets the update summary |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/action | Upload certificate for device registration |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/delete | Deletes the share users |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/operationResults/read | Lists or gets the operation result |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lists or gets the share users |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/read | Lists or gets the share users |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/users/write | Creates or updates the share users |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Creates or updates the Data Box Edge devices |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/write | Creates or updates the Data Box Edge devices |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Databricks/locations/getNetworkPolicies/action | Get Network Intent Polices for a subnet based on the location used by NRP |
> | Åtgärd | Microsoft.Databricks/locations/operationstatuses/read | Reads the operation status for the resource. |
> | Åtgärd | Microsoft.Databricks/operations/read | Gets the list of operations. |
> | Åtgärd | Microsoft.Databricks/register/action | Register to Databricks. |
> | Åtgärd | Microsoft.Databricks/workspaces/delete | Removes a Databricks workspace. |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/read | Sets the available diagnostic settings for the Databricks workspace |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/diagnosticSettings/write | Add or modify diagnostics settings. |
> | Åtgärd | Microsoft.Databricks/workspaces/providers/Microsoft.Insights/logDefinitions/read | Gets the available log definitions for the Databricks workspace |
> | Åtgärd | Microsoft.Databricks/workspaces/read | Retrieves a list of Databricks workspaces. |
> | Åtgärd | Microsoft.Databricks/workspaces/refreshPermissions/action | Refresh permissions for a workspace |
> | Åtgärd | Microsoft.Databricks/workspaces/updateDenyAssignment/action | Update deny assignment not actions for a managed resource group of a workspace |
> | Åtgärd | Microsoft.Databricks/workspaces/virtualNetworkPeerings/delete | Deletes a virtual network peering |
> | Åtgärd | Microsoft.Databricks/workspaces/virtualNetworkPeerings/read | Gets the virtual network peering. |
> | Åtgärd | Microsoft.Databricks/workspaces/virtualNetworkPeerings/write | Add or modify virtual network peering |
> | Åtgärd | Microsoft.Databricks/workspaces/write | Creates a Databricks workspace. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataCatalog/catalogs/delete | Delete catalogs resource for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/read | Read catalogs resource for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/catalogs/write | Write catalogs resource for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/checkNameAvailability/read | Check catalog name availability for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/datacatalogs/delete | Delete DataCatalog resource for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/datacatalogs/read | Read DataCatalog resource for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/datacatalogs/write | Write DataCatalog resource for Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/operations/read | Reads all available operations in Data Catalog Resource Provider. |
> | Åtgärd | Microsoft.DataCatalog/register/action | Register the subscription for Data Catalog Resource Provider |
> | Åtgärd | Microsoft.DataCatalog/unregister/action | Unregister  the subscription for Data Catalog Resource Provider |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Checks if the Data Factory Name is available to use. |
> | Åtgärd | Microsoft.DataFactory/datafactories/activitywindows/read | Reads Activity Windows in the Data Factory with specified parameters. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Läser Windows aktivitets fönster för pipeline-aktiviteten med angivna parametrar. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/activitywindows/Read | Läser Windows aktivitets fönster för pipelinen med angivna parametrar. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/Delete | Tar bort en pipeline. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/PAUSE/åtgärd | Pausar en pipeline. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/Read | Läser en pipeline. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/Resume/åtgärd | Återupptar eventuell pipeline. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/Update/åtgärd | Uppdaterar en pipeline. |
> | Åtgärd | Microsoft. DataFactory/datafactories/datapipelines/Write | Skapar eller uppdaterar en pipeline. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Läser Windows aktivitets fönster för data uppsättningen med angivna parametrar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/delete | Tar bort alla data uppsättningar. |
> | Åtgärd | Microsoft. DataFactory/datafactories/data uppsättningar/läsa | Läser alla data uppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Läser data sektor körningen för den aktuella data mängden med den aktuella start tiden. |
> | Åtgärd | Microsoft. DataFactory/datafactories/data uppsättningar/sektorer/läsning | Hämtar data sektorerna under den aktuella perioden. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/slices/write | Uppdatera data sektorns status. |
> | Åtgärd | Microsoft.DataFactory/datafactories/datasets/write | Skapar eller uppdaterar alla data uppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/delete | Tar bort Data Factory. |
> | Åtgärd | Microsoft. DataFactory/datafactories/gateways/ConnectionInfo/åtgärd | Läser anslutnings informationen för en gateway. |
> | Åtgärd | Microsoft. DataFactory/datafactories/gateways/Delete | Tar bort alla gatewayer. |
> | Åtgärd | Microsoft. DataFactory/datafactories/gateways/listauthkeys/åtgärd | Visar en lista över autentiseringsinställningar för alla gatewayer. |
> | Åtgärd | Microsoft. DataFactory/datafactories/Gateway/Read | Läser alla gatewayer. |
> | Åtgärd | Microsoft. DataFactory/datafactories/gateways/regenerateauthkey/åtgärd | Återskapar autentiseringsinställningarna för en gateway. |
> | Åtgärd | Microsoft. DataFactory/datafactories/Gateway/Write | Skapar eller uppdaterar en gateway. |
> | Åtgärd | Microsoft. DataFactory/datafactories/linkedServices/Delete | Tar bort alla länkade tjänster. |
> | Åtgärd | Microsoft. DataFactory/datafactories/linkedServices/Read | Läser en länkad tjänst. |
> | Åtgärd | Microsoft. DataFactory/datafactories/linkedServices/Write | Skapar eller uppdaterar en länkad tjänst. |
> | Åtgärd | Microsoft. DataFactory/datafactories/Read | Läser Data Factory. |
> | Åtgärd | Microsoft. DataFactory/datafactories/körs/LogInfo/Read | Läser en SAS-URI till en BLOB-behållare som innehåller loggarna. |
> | Åtgärd | Microsoft.DataFactory/datafactories/tables/delete | Tar bort alla data uppsättningar. |
> | Åtgärd | Microsoft. DataFactory/datafactories/tables/Read | Läser alla data uppsättningar. |
> | Åtgärd | Microsoft. DataFactory/datafactories/tables/Write | Skapar eller uppdaterar alla data uppsättningar. |
> | Åtgärd | Microsoft.DataFactory/datafactories/write | Skapar eller uppdaterar Data Factory. |
> | Åtgärd | Microsoft. DataFactory/factors/addDataFlowToDebugSession/Action | Lägg till data flöde i felsökningssession för för hands version. |
> | Åtgärd | Microsoft. DataFactory/factors/cancelpipelinerun/Action | Avbryter pipeline-körningen som anges av körnings-ID: t. |
> | Åtgärd | Microsoft. DataFactory/factors/cancelSandboxPipelineRun/Action | Avbryter en fel söknings körning för pipelinen. |
> | Åtgärd | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Skapar en session för data flödes fel sökning. |
> | Åtgärd | Microsoft. DataFactory/factors/data flöden/Delete | Tar bort data flöde. |
> | Åtgärd | Microsoft. DataFactory/factors/data flöden/Read | Läser data flöde. |
> | Åtgärd | Microsoft.DataFactory/factories/dataflows/write | Skapa eller uppdatera data flöde |
> | Åtgärd | Microsoft. DataFactory/factors/data uppsättningar/ta bort | Tar bort alla data uppsättningar. |
> | Åtgärd | Microsoft. DataFactory/factors/data uppsättningar/läsa | Läser alla data uppsättningar. |
> | Åtgärd | Microsoft. DataFactory/factors/data uppsättningar/skrivning | Skapar eller uppdaterar alla data uppsättningar. |
> | Åtgärd | Microsoft. DataFactory/factors/debugpipelineruns/Cancel/Action | Avbryter en fel söknings körning för pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/Delete | Tar bort Data Factory. |
> | Åtgärd | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Tar bort en data flödes fel söknings session. |
> | Åtgärd | Microsoft. DataFactory/factors/executeDataFlowDebugCommand/Action | Kör fel söknings kommando för data flöde. |
> | Åtgärd | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Hämtar åtkomst till ADF dataplanen-tjänsten. |
> | Åtgärd | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Läser åtkomst till ADF dataplanen-tjänsten. |
> | Åtgärd | Microsoft.DataFactory/factories/getFeatureValue/action | Hämta funktions värde för exponerings kontroll för den angivna platsen. |
> | Åtgärd | Microsoft.DataFactory/factories/getFeatureValue/read | Läser funktions värdet för exponerings kontroll för den angivna platsen. |
> | Åtgärd | Microsoft. DataFactory/factors/getGitHubAccessToken/Action | Hämtar GitHub-åtkomsttoken. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/delete | Tar bort alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Läser Integration Runtime anslutnings information. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Hämta SSIS Integration Runtime metadata för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Läser Integration Runtimes status. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Skapa länkad Integration Runtime referens på den angivna delade Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Visar en lista över autentiseringsinställningarna för alla Integration Runtime. |
> | Åtgärd | Microsoft. DataFactory/factors/integrationruntimes/monitoringdata/Read | Hämtar övervaknings data för alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Tar bort noden för angivet Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Returnerar IP-adressen för den angivna noden i Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Läser noden för angivet Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Uppdaterar en egen värd Integration Runtime-nod. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/read | Läser alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Uppdatera SSIS Integration Runtime metadata för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Återskapar autentiseringsinställningarna för den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Tar bort länkade Integration Runtime referenser från den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/start/action | Startar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Stoppar alla Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synkroniserar autentiseringsuppgifterna för angivet Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uppgraderar den angivna Integration Runtime. |
> | Åtgärd | Microsoft.DataFactory/factories/integrationruntimes/write | Skapar eller uppdaterar Integration Runtime. |
> | Åtgärd | Microsoft. DataFactory/factors/linkedServices/Delete | Tar bort länkad tjänst. |
> | Åtgärd | Microsoft. DataFactory/factors/linkedServices/Read | Läser länkad tjänst. |
> | Åtgärd | Microsoft. DataFactory/factors/linkedServices/Write | Skapa eller uppdatera länkade tjänster |
> | Åtgärd | Microsoft. DataFactory/factors/operationResults/Read | Hämtar åtgärds resultat. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelineruns/activityruns/Read | Läser aktivitets körningar för angivet pipeline-körnings-ID. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelineruns/Cancel/Action | Avbryter pipeline-körningen som anges av körnings-ID: t. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelineruns/queryactivityruns/Action | Frågar aktiviteten körs för angivet pipeline-körnings-ID. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelineruns/queryactivityruns/Read | Läser resultatet av frågans aktivitets körningar för det angivna pipeline-körnings-ID: t. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelineruns/Read | Läser pipeline-körningar. |
> | Åtgärd | Microsoft. DataFactory/factors/pipeline/createrun/Action | Skapar en körning för pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/pipeline/Delete | Tar bort pipeline. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelines/pipelineruns/activityruns/Progress/Read | Hämtar förloppet för aktivitets körningar. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelines/pipelineruns/Read | Läser körningen av pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelines/Read | Läser pipeline. |
> | Åtgärd | Microsoft. DataFactory/factorrs/pipeline/sandbox/åtgärd | Skapar en fel söknings körnings miljö för pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/pipelines/sandbox/skapa/åtgärd | Skapar en fel söknings körnings miljö för pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/-pipeline/sandbox/körning/åtgärd | Skapar en fel söknings körning för pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/pipeline/Write | Skapa eller uppdatera pipeline |
> | Åtgärd | Microsoft. DataFactory/factors/querydataflowdebugsessions/Action | Frågar en fel söknings session för data flöde. |
> | Åtgärd | Microsoft. DataFactory/factors/querydebugpipelineruns/Action | Frågar om fel söknings pipelinen körs. |
> | Åtgärd | Microsoft. DataFactory/factors/querypipelineruns/Action | Frågar om pipelinen körs. |
> | Åtgärd | Microsoft. DataFactory/factors/querypipelineruns/Read | Läser resultatet av pipeline-körningar. |
> | Åtgärd | Microsoft. DataFactory/factors/querytriggerruns/Action | Frågar om utlösaren körs. |
> | Åtgärd | Microsoft. DataFactory/factors/querytriggerruns/Read | Läser resultatet av utlösare som körs. |
> | Åtgärd | Microsoft. DataFactory/factors/Read | Läser Data Factory. |
> | Åtgärd | Microsoft. DataFactory/factors/sandboxpipelineruns/Action | Frågar om fel söknings pipelinen körs. |
> | Åtgärd | Microsoft. DataFactory/factors/sandboxpipelineruns/Read | Hämtar fel söknings körnings information för pipelinen. |
> | Åtgärd | Microsoft. DataFactory/factors/sandboxpipelineruns/sandboxActivityRuns/Read | Hämtar fel söknings körnings information för aktiviteten. |
> | Åtgärd | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Startar en session med data flödes fel sökning. |
> | Åtgärd | Microsoft. DataFactory/factors/triggerruns/Read | Läser utlösare som körs. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/Delete | Tar bort alla utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/geteventsubscriptionstatus/åtgärd | Status för händelse prenumeration. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/Read | Läser alla utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/starta/åtgärd | Startar alla utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/stoppa/åtgärd | Stoppar eventuell utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/subscribetoevents/åtgärd | Prenumerera på händelser. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/triggerruns/Read | Läser utlösare som körs. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/unsubscribefromevents/åtgärd | Avbryt prenumerationen på händelser. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/Write | Skapar eller uppdaterar eventuella utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/Write | Skapa eller uppdatera Data Factory |
> | Åtgärd | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfigurerar lagrings platsen för fabriken. |
> | Åtgärd | Microsoft.DataFactory/locations/getFeatureValue/action | Hämta funktions värde för exponerings kontroll för den angivna platsen. |
> | Åtgärd | Microsoft.DataFactory/locations/getFeatureValue/read | Läser funktions värdet för exponerings kontroll för den angivna platsen. |
> | Åtgärd | Microsoft. DataFactory/Operations/Read | Läser alla åtgärder i Microsoft Data Factory-providern. |
> | Åtgärd | Microsoft. DataFactory/register/åtgärd | Registrerar prenumerationen för Data Factory Resource Provider. |
> | Åtgärd | Microsoft.DataFactory/unregister/action | Avregistrerar prenumerationen för Data Factory Resource Provider. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Ta bort en beräknings princip. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Hämta information om en beräknings princip. |
> | Åtgärd | Microsoft. DataLakeAnalytics/Accounts/computePolicies/Write | Skapa eller uppdatera en beräknings princip. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Ta bort länken mellan ett DataLakeStore-konto och ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Hämta information om ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft. DataLakeAnalytics/Accounts/Delete | Ta bort ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Ta bort en brand Väggs regel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Hämta information om en brand Väggs regel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Skapa eller uppdatera en brand Väggs regel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Erhåll resultatet av en DataLakeAnalytics-konto åtgärd. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/read | Hämta information om ett befintligt DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Visa en lista med SAS-token för lagrings behållare för ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/containers/Read | Hämta behållare för ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Delete | Ta bort länken till ett lagrings konto från ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Hämta information om ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Skapa eller uppdatera ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft. DataLakeAnalytics/Accounts/TakeOwnership/Action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Överför SystemMaxAnalyticsUnits mellan DataLakeAnalytics-konton. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Ta bort en regel för virtuellt nätverk. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Hämta information om en virtuell nätverks regel. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Skapa eller uppdatera en regel för virtuellt nätverk. |
> | Åtgärd | Microsoft.DataLakeAnalytics/accounts/write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | Åtgärd | Microsoft. DataLakeAnalytics/locations/Capability/Read | Få kapacitets information för en prenumeration angående användning av DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Kontrol lera tillgängligheten för ett DataLakeAnalytics konto namn. |
> | Åtgärd | Microsoft.DataLakeAnalytics/locations/operationResults/read | Erhåll resultatet av en DataLakeAnalytics-konto åtgärd. |
> | Åtgärd | Microsoft. DataLakeAnalytics/platser/användning/läsa | Hämta information om kvot användning för en prenumeration avseende användning av DataLakeAnalytics. |
> | Åtgärd | Microsoft.DataLakeAnalytics/operations/read | Hämta tillgängliga åtgärder för DataLakeAnalytics. |
> | Åtgärd | Microsoft. DataLakeAnalytics/register/åtgärd | Registrera prenumeration på DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataLakeStore/accounts/delete | Ta bort ett DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Aktivera nyckel valv för ett DataLakeStore-konto. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Ta bort ett EventGrid-filter. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Hämta ett EventGrid-filter. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Skapa eller uppdatera ett EventGrid-filter. |
> | Åtgärd | Microsoft. DataLakeStore/Accounts/firewallRules/Delete | Ta bort en brand Väggs regel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/firewallRules/read | Hämta information om en brand Väggs regel. |
> | Åtgärd | Microsoft. DataLakeStore/Accounts/firewallRules/Write | Skapa eller uppdatera en brand Väggs regel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/operationResults/read | Erhåll resultatet av en DataLakeStore-konto åtgärd. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/read | Hämta information om ett befintligt DataLakeStore-konto. |
> | Åtgärd | Microsoft. DataLakeStore/Accounts/Shares/Delete | Ta bort en resurs. |
> | Åtgärd | Microsoft. DataLakeStore/Accounts/Shares/Read | Hämta information om en resurs. |
> | Åtgärd | Microsoft. DataLakeStore/konton/resurser/skriva | Skapa eller uppdatera en resurs. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/Superuser/action | Bevilja superanvändare på Data Lake Store när de beviljas med Microsoft. Authorization/roleAssignments/Write. |
> | Åtgärd | Microsoft. DataLakeStore/Accounts/trustedIdProviders/Delete | Ta bort en betrodd identitets leverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Hämta information om en betrodd identitets leverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Skapa eller uppdatera en betrodd identitets leverantör. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Ta bort en regel för virtuellt nätverk. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Hämta information om en virtuell nätverks regel. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Skapa eller uppdatera en regel för virtuellt nätverk. |
> | Åtgärd | Microsoft.DataLakeStore/accounts/write | Skapa eller uppdatera ett DataLakeStore-konto. |
> | Åtgärd | Microsoft. DataLakeStore/locations/Capability/Read | Få kapacitets information för en prenumeration angående användning av DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Kontrol lera tillgängligheten för ett DataLakeStore konto namn. |
> | Åtgärd | Microsoft.DataLakeStore/locations/operationResults/read | Erhåll resultatet av en DataLakeStore-konto åtgärd. |
> | Åtgärd | Microsoft.DataLakeStore/locations/usages/read | Hämta information om kvot användning för en prenumeration avseende användning av DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/operations/read | Hämta tillgängliga åtgärder för DataLakeStore. |
> | Åtgärd | Microsoft.DataLakeStore/register/action | Registrera prenumeration på DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataMigration/locations/operationResults/read | Hämta status för en tids krävande åtgärd som är relaterad till ett 202 godkänt svar |
> | Åtgärd | Microsoft. data migration/locations/operationStatuses/Read | Hämta status för en tids krävande åtgärd som är relaterad till ett 202 godkänt svar |
> | Åtgärd | Microsoft. data migration/register/åtgärd | Registrerar prenumerationen hos Azure Database Migration Service-providern |
> | Åtgärd | Microsoft. data migration/Services/addWorker/Action | Lägger till en DMS-arbetare till tjänstens är tillgänglig-arbetare |
> | Åtgärd | Microsoft. data migration/Services/checkStatus/Action | Kontrol lera om tjänsten har distribuerats och körs |
> | Åtgärd | Microsoft. data migration/Services/configureWorker/Action | Konfigurerar en DMS-arbetare till tjänstens är tillgänglig-arbetare |
> | Åtgärd | Microsoft. data migration/Services/Delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft. data migration/Services/projekt/accessArtifacts/åtgärd | Generera en URL som kan användas för att hämta eller placera projekt artefakter |
> | Åtgärd | Microsoft. data migration/Services/projekt/Delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft. data migration/Services/projekt/filer/Delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft. data migration/Services/projekt/filer/läsa | Läs information om resurser |
> | Åtgärd | Microsoft. data migration/Services/projekt/filer/Läs/åtgärd | Hämta en URL som kan användas för att läsa innehållet i filen |
> | Åtgärd | Microsoft. data migration/Services/projekt/filer/readWrite/åtgärd | Hämta en URL som kan användas för att läsa eller skriva innehållet i filen |
> | Åtgärd | Microsoft. data migration/Services/projekt/filer/skriva | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft. data migration/tjänster/projekt/läsa | Läs information om resurser |
> | Åtgärd | Microsoft. data migration/Services/Projects/tasks/Cancel/Action | Avbryt uppgiften om den körs för närvarande |
> | Åtgärd | Microsoft. data migration/Services/Projects/tasks/Delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft. data migration/Services/Projects/tasks/Read | Läs information om resurser |
> | Åtgärd | Microsoft. data migration/Services/Projects/tasks/Write | Kör aktiviteter Azure Database Migration Service uppgifter |
> | Åtgärd | Microsoft. data migration/tjänster/projekt/skriva | Kör aktiviteter Azure Database Migration Service uppgifter |
> | Åtgärd | Microsoft. data migration/Services/Read | Läs information om resurser |
> | Åtgärd | Microsoft. data migration/Services/removeWorker/Action | Tar bort en DMS-arbetare till tjänstens är tillgänglig-arbetare |
> | Åtgärd | Microsoft. data migration/Services/serviceTasks/Cancel/Action | Avbryt uppgiften om den körs för närvarande |
> | Åtgärd | Microsoft. data migration/Services/serviceTasks/Delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft. data migration/Services/serviceTasks/Read | Läs information om resurser |
> | Åtgärd | Microsoft. data migration/Services/serviceTasks/Write | Kör aktiviteter Azure Database Migration Service uppgifter |
> | Åtgärd | Microsoft. data migration/Services/lots/Delete | Tar bort en resurs och alla dess underordnade |
> | Åtgärd | Microsoft. data migration/tjänster/platser/läsa | Läs information om resurser |
> | Åtgärd | Microsoft. data migration/tjänster/platser/Skriv | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft. data migration/tjänster/start/åtgärd | Starta DMS-tjänsten så att den kan bearbeta migreringar igen |
> | Åtgärd | Microsoft. data migration/tjänster/stoppa/åtgärd | Stoppa DMS-tjänsten för att minimera dess kostnad |
> | Åtgärd | Microsoft. data migration/Services/updateAgentConfig/Action | Uppdaterar DMS-agentens konfiguration med angivna värden. |
> | Åtgärd | Microsoft. data migration/Services/Write | Skapa eller uppdatera resurser och deras egenskaper |
> | Åtgärd | Microsoft.DataMigration/skus/read | Hämta en lista över SKU: er som stöds av DMS-resurser. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. DBforMariaDB/checkNameAvailability/Action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Åtgärd | Microsoft. DBforMariaDB/locations/azureAsyncOperation/Read | Returnera MariaDB-Server åtgärds resultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera ResourceGroup-baserade MariaDB Server åtgärds resultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera MariaDB-Server åtgärds resultat |
> | Åtgärd | Microsoft. DBforMariaDB/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft. DBforMariaDB/locations/privateEndpointConnectionAzureAsyncOperation/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. DBforMariaDB/locations/privateEndpointConnectionOperationResults/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. DBforMariaDB/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. DBforMariaDB/locations/privateEndpointConnectionProxyOperationResults/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft. DBforMariaDB/locations/serverKeyAzureAsyncOperation/Read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft. DBforMariaDB/locations/serverKeyOperationResults/Read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft. DBforMariaDB/Operations/Read | Returnera listan över MariaDB-åtgärder. |
> | Åtgärd | Microsoft. DBforMariaDB/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft.DBforMariaDB/register/action | Registrera MariaDB Resource Provider |
> | Åtgärd | Microsoft. DBforMariaDB/servers/administrators/Delete | Tar bort en befintlig administratör av MariaDB-servern. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/administrators/Read | Hämtar en lista över MariaDB Server-administratörer. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/administrators/Write | Skapar eller uppdaterar MariaDB Server-administratör med de angivna parametrarna. |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/Advisor/createRecommendedActionSession/Action | Skapa en ny rekommendation för rekommendations åtgärd |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/Advisor/Read | Returnera listan över rådgivare |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/Advisor/Read | Returnera en rådgivare |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/Advisor/recommendedActions/Read | Returnera en Rekommenderad åtgärd |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/konfigurationer/läsa | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/konfigurationer/Skriv | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/databaser/ta bort | Tar bort en befintlig MariaDB-databas. |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/databaser/läsa | Returnera listan över MariaDB-databaser eller hämta egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/databases/write | Skapar en MariaDB-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/Delete | Deletes an existing server. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/firewallRules/Delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/nycklar/ta bort | Deletes an existing server key. |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/nycklar/läsa | Return the list of server keys or gets the properties for the specified server key. |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/nycklar/Skriv | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/logFiles/read | Returnera listan över MariaDB-loggfiler. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnectionProxies/Delete | Deletes an existing private endpoint connection proxy |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnectionProxies/Read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnectionProxies/validate/åtgärd | Validates a private endpoint connection create call from NRP side |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnectionProxies/Write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnections/Delete | Deletes an existing private endpoint connection |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnections/Read | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateEndpointConnections/Write | Approves or rejects an existing private endpoint connection |
> | Åtgärd | Microsoft. DBforMariaDB/servers/privateLinkResources/Read | Hämta de privata länk resurserna för motsvarande MariaDB-Server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft. DBforMariaDB/servers/providers/Microsoft. Insights/logDefinitions/Read | Hämtar de tillgängliga loggarna för MariaDB-servrar |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for databases |
> | Åtgärd | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texterna för en lista med frågor |
> | Åtgärd | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texten för en fråga |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/läsa | Return the list of servers or gets the properties for the specified server. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/recoverableServers/read | Returnera information om återställnings bar MariaDB-Server |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/repliker/läsa | Hämta Läs repliker av en MariaDB-Server |
> | Åtgärd | Microsoft. DBforMariaDB/-servrar/omstart/åtgärd | Startar om en speciell Server. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/securityAlertPolicies/Read | Hämta information om den princip för Server hot identifiering som kon figurer ATS på en angiven server |
> | Åtgärd | Microsoft. DBforMariaDB/servers/securityAlertPolicies/Write | Ändra principen för Server hot identifiering för en specifik server |
> | Åtgärd | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Returnera listan över frågans statistik för de vanligaste frågorna. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Returnera statistik för frågan |
> | Åtgärd | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/waitStatistics/Read | Returnera väntande statistik för en instans |
> | Åtgärd | Microsoft. DBforMariaDB/servers/waitStatistics/Read | Returnera en wait-statistik |
> | Åtgärd | Microsoft.DBforMariaDB/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. DBforMySQL/checkNameAvailability/Action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Åtgärd | Microsoft. DBforMySQL/locations/azureAsyncOperation/Read | Returnera åtgärds resultat för MySQL-Server |
> | Åtgärd | Microsoft.DBforMySQL/locations/operationResults/read | Returnera ResourceGroup-baserade server åtgärds resultat för MySQL-Server |
> | Åtgärd | Microsoft.DBforMySQL/locations/operationResults/read | Returnera åtgärds resultat för MySQL-Server |
> | Åtgärd | Microsoft. DBforMySQL/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft. DBforMySQL/locations/privateEndpointConnectionAzureAsyncOperation/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. DBforMySQL/locations/privateEndpointConnectionOperationResults/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. DBforMySQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. DBforMySQL/locations/privateEndpointConnectionProxyOperationResults/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft. DBforMySQL/locations/securityAlertPoliciesOperationResults/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft. DBforMySQL/locations/serverKeyAzureAsyncOperation/Read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft. DBforMySQL/locations/serverKeyOperationResults/Read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft. DBforMySQL/Operations/Read | Returnera listan över MySQL-åtgärder. |
> | Åtgärd | Microsoft. DBforMySQL/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft. DBforMySQL/register/åtgärd | Registrera MySQL Resource Provider |
> | Åtgärd | Microsoft. DBforMySQL/servers/administrators/Delete | Tar bort en befintlig administratör av MySQL-servern. |
> | Åtgärd | Microsoft. DBforMySQL/servers/administrators/Read | Hämtar en lista över MySQL Server-administratörer. |
> | Åtgärd | Microsoft. DBforMySQL/servers/administrators/Write | Skapar eller uppdaterar MySQL Server-administratören med de angivna parametrarna. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Advisor/createRecommendedActionSession/Action | Skapa en ny rekommendation för rekommendations åtgärd |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Advisor/Read | Returnera listan över rådgivare |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Advisor/Read | Returnera en rådgivare |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Advisor/recommendedActions/Read | Returnera en Rekommenderad åtgärd |
> | Åtgärd | Microsoft. DBforMySQL/servrar/konfigurationer/läsa | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/konfigurationer/Skriv | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft. DBforMySQL/servrar/databaser/ta bort | Tar bort en befintlig MySQL-databas. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/databaser/läsa | Returnera listan över MySQL-databaser eller hämtar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/databaser/skriva | Skapar en MySQL-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft. DBforMySQL/servers/Delete | Deletes an existing server. |
> | Åtgärd | Microsoft. DBforMySQL/servers/firewallRules/Delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft. DBforMySQL/servers/firewallRules/Read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft. DBforMySQL/servers/firewallRules/Write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/nycklar/ta bort | Deletes an existing server key. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/nycklar/läsa | Return the list of server keys or gets the properties for the specified server key. |
> | Åtgärd | Microsoft. DBforMySQL/servrar/nycklar/Skriv | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Åtgärd | Microsoft.DBforMySQL/servers/logFiles/read | Returnera listan över MySQL-loggfiler. |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnectionProxies/Delete | Deletes an existing private endpoint connection proxy |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnectionProxies/Read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnectionProxies/validate/åtgärd | Validates a private endpoint connection create call from NRP side |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnectionProxies/Write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnections/Delete | Deletes an existing private endpoint connection |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnections/Read | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateEndpointConnections/Write | Approves or rejects an existing private endpoint connection |
> | Åtgärd | Microsoft. DBforMySQL/servers/privateLinkResources/Read | Hämta de privata länk resurserna för motsvarande MySQL-Server |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/diagnosticSettings/Write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/logDefinitions/Read | Hämtar tillgängliga loggar för MySQL-servrar |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/metricDefinitions/Read | Return types of metrics that are available for databases |
> | Åtgärd | Microsoft. DBforMySQL/servers/queryTexts/åtgärd | Returnera texterna för en lista med frågor |
> | Åtgärd | Microsoft. DBforMySQL/servers/queryTexts/åtgärd | Returnera texten för en fråga |
> | Åtgärd | Microsoft. DBforMySQL/servrar/läsa | Return the list of servers or gets the properties for the specified server. |
> | Åtgärd | Microsoft. DBforMySQL/servers/recoverableServers/Read | Returnera information om återställnings bara MySQL-Server |
> | Åtgärd | Microsoft. DBforMySQL/servrar/repliker/läsa | Hämta Läs repliker av en MySQL-Server |
> | Åtgärd | Microsoft. DBforMySQL/-servrar/omstart/åtgärd | Startar om en speciell Server. |
> | Åtgärd | Microsoft. DBforMySQL/servers/securityAlertPolicies/Read | Hämta information om den princip för Server hot identifiering som kon figurer ATS på en angiven server |
> | Åtgärd | Microsoft. DBforMySQL/servers/securityAlertPolicies/Write | Ändra principen för Server hot identifiering för en specifik server |
> | Åtgärd | Microsoft. DBforMySQL/servers/topQueryStatistics/Read | Returnera listan över frågans statistik för de vanligaste frågorna. |
> | Åtgärd | Microsoft. DBforMySQL/servers/topQueryStatistics/Read | Returnera statistik för frågan |
> | Åtgärd | Microsoft. DBforMySQL/servers/updateConfigurations/åtgärd | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft. DBforMySQL/servers/waitStatistics/Read | Returnera väntande statistik för en instans |
> | Åtgärd | Microsoft. DBforMySQL/servers/waitStatistics/Read | Returnera en wait-statistik |
> | Åtgärd | Microsoft. DBforMySQL/servrar/Skriv | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Returnera PostgreSQL-Server åtgärds resultat |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/operationResults/read | Returnera ResourceGroup-baserade PostgreSQL Server åtgärds resultat |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/operationResults/read | Returnera PostgreSQL-Server åtgärds resultat |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionAzureAsyncOperation/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionOperationResults/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionProxyOperationResults/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/serverKeyAzureAsyncOperation/Read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft. DBforPostgreSQL/locations/serverKeyOperationResults/Read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft.DBforPostgreSQL/operations/read | Returnera listan över PostgreSQL-åtgärder. |
> | Åtgärd | Microsoft. DBforPostgreSQL/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft.DBforPostgreSQL/register/action | Registrera PostgreSQL Resource Provider |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/administrators/Delete | Tar bort en befintlig administratör av PostgreSQL-servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/administrators/read | Hämtar en lista över PostgreSQL Server-administratörer. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/administrators/write | Skapar eller uppdaterar PostgreSQL Server-administratör med de angivna parametrarna. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/read | Returnera listan över rådgivare |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Returnera listan över rekommenderade åtgärder |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Gör rekommendationer |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/configurations/read | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/databases/delete | Tar bort en befintlig PostgreSQL-databas. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/databases/read | Returnera listan över PostgreSQL-databaser eller hämta egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/databases/write | Skapar en PostgreSQL-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/delete | Deletes an existing server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servrar/nycklar/ta bort | Deletes an existing server key. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servrar/nycklar/läsa | Return the list of server keys or gets the properties for the specified server key. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servrar/nycklar/Skriv | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/logFiles/read | Returnera listan över PostgreSQL-loggfiler. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Delete | Deletes an existing private endpoint connection proxy |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/åtgärd | Validates a private endpoint connection create call from NRP side |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Delete | Deletes an existing private endpoint connection |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Read | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Write | Approves or rejects an existing private endpoint connection |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateLinkResources/Read | Hämta de privata länk resurserna för motsvarande PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for databases |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Returnera texten för en fråga |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Returnera texterna för en lista med frågor |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/read | Return the list of servers or gets the properties for the specified server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Returnera information om återställnings bar PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/replicas/read | Hämta Läs repliker av en PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/restart/action | Startar om en speciell Server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Hämta information om den princip för Server hot identifiering som kon figurer ATS på en angiven server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Ändra principen för Server hot identifiering för en specifik server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Returnera listan över frågans statistik för de vanligaste frågorna. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Returnera väntande statistik för en instans |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/delete | Deletes an existing server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for databases |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/read | Return the list of servers or gets the properties for the specified server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Devices/Account/diagnosticSettings/Read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft. Devices/Account/diagnosticSettings/Write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft. Devices/Account/logDefinitions/Read | Hämtar de tillgängliga logg definitionerna för IotHub-tjänsten |
> | Åtgärd | Microsoft. Devices/Account/metricDefinitions/Read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft. Devices/checkNameAvailability/åtgärd | Kontrol lera om IotHub namn är tillgängligt |
> | Åtgärd | Microsoft. Devices/checkNameAvailability/åtgärd | Kontrol lera om IotHub namn är tillgängligt |
> | Åtgärd | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Kontrol lera om tjänst namnet för etablering är tillgängligt |
> | Åtgärd | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Kontrol lera om tjänst namnet för etablering är tillgängligt |
> | Åtgärd | Microsoft. Devices/digitalTwins/Delete | Ta bort ett befintligt digitalt flätat-konto och alla dess underordnade |
> | Åtgärd | Microsoft. Devices/digitalTwins/operationresults/Read | Hämta status för en åtgärd mot ett elektroniskt konto med dubbla konton |
> | Åtgärd | Microsoft. Devices/digitalTwins/Read | Hämtar en lista över de digitala dubbla konton som är kopplade till en prenumeration |
> | Åtgärd | Microsoft. Devices/digitalTwins/SKU/Read | Hämta en lista över giltiga SKU: er för Digitals dubbla konton |
> | Åtgärd | Microsoft. Devices/digitalTwins/Write | Skapa ett nytt siffer konto med dubbla konton |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft. Devices/elasticPools/eventGridFilters/Delete | Tar bort Elastisk pool Event Grids filtret |
> | Åtgärd | Microsoft. Devices/elasticPools/eventGridFilters/Read | Hämtar Elastisk pool Event Grids filtret |
> | Åtgärd | Microsoft. Devices/elasticPools/eventGridFilters/Write | Skapa nya eller uppdatera befintliga Elastisk pool Event Grid filter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Deletes Certificate |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generate Verification code |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Gets the Certificate |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verify Certificate resource |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Create or Update Certificate |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Ta bort IotHub-klient resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Ta bort EventHub-konsument grupp |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Hämta en EventHub-konsument grupp (er) |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Skapa en EventHub-konsument grupp |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportera enheter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Hämtar IotHub-resursen för klient statistik |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importera enheter |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Hämtar IotHub klient nyckel |
> | Åtgärd | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/Read | Hämta jobb information som skickats till den aktuella IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Hämtar IotHub klient nycklar |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Hämtar de tillgängliga logg definitionerna för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Hämta kvot mått |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Read | Hämtar klient organisations resursen för IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testa ett meddelande mot alla befintliga vägar |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testa ett meddelande mot en angiven test väg |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Gets the health of all routing Endpoints for an IotHub |
> | Åtgärd | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/operationResults/Read | Hämta resultatet av den asynkrona åtgärden för IoT Hub-SecuritySettings |
> | Åtgärd | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Read | Hämta Azure Security Center inställningar på IoT-klientens hubb |
> | Åtgärd | Microsoft. Devices/elasticPools/iotHubTenants/securitySettings/Write | Uppdatera Azure Security Center inställningar på IoT-klientens hubb |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Write | Skapa eller uppdatera klient organisations resursen för IotHub |
> | Åtgärd | Microsoft.Devices/ElasticPools/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Delete | Deletes Certificate |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generate Verification code |
> | Åtgärd | Microsoft. Devices/iotHubs/certificates/Read | Gets the Certificate |
> | Åtgärd | Microsoft. Devices/iotHubs/certificates/verify/Action | Verify Certificate resource |
> | Åtgärd | Microsoft. Devices/iotHubs/certificates/Write | Create or Update Certificate |
> | Åtgärd | Microsoft.Devices/iotHubs/Delete | Ta bort IotHub-resurs |
> | Åtgärd | Microsoft. Devices/IotHubs/diagnosticSettings/Read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Tar bort Event Grids filtret |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Read | Hämtar Event Grids filtret |
> | Åtgärd | Microsoft.Devices/iotHubs/eventGridFilters/Write | Skapa nytt eller uppdatera befintligt Event Grid filter |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Ta bort EventHub-konsument grupp |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Hämta en EventHub-konsument grupp (er) |
> | Åtgärd | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Skapa en EventHub-konsument grupp |
> | Åtgärd | Microsoft.Devices/iotHubs/exportDevices/Action | Exportera enheter |
> | Åtgärd | Microsoft. Devices/iotHubs/importDevices/Action | Importera enheter |
> | Åtgärd | Microsoft. Devices/iotHubs/iotHubKeys/listnycklar/Action | Hämta IotHub-nyckeln för det aktuella namnet |
> | Åtgärd | Microsoft.Devices/iotHubs/iotHubStats/Read | Hämta IotHub-statistik |
> | Åtgärd | Microsoft. Devices/iotHubs/Jobs/Read | Hämta jobb information som skickats till den aktuella IotHub |
> | Åtgärd | Microsoft. Devices/iotHubs/listnycklar/Action | Hämta alla IotHub-nycklar |
> | Åtgärd | Microsoft.Devices/IotHubs/logDefinitions/read | Hämtar de tillgängliga logg definitionerna för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/IotHubs/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft. Devices/iotHubs/operationresults/Read | Hämta åtgärds resultat (föråldrat API) |
> | Åtgärd | Microsoft. Devices/iotHubs/quotaMetrics/Read | Hämta kvot mått |
> | Åtgärd | Microsoft. Devices/iotHubs/Read | Hämtar IotHub-resurs (er) |
> | Åtgärd | Microsoft. Devices/iotHubs/routing/$testall/Action | Testa ett meddelande mot alla befintliga vägar |
> | Åtgärd | Microsoft. Devices/iotHubs/routing/$testnew/Action | Testa ett meddelande mot en angiven test väg |
> | Åtgärd | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Gets the health of all routing Endpoints for an IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/securitySettings/operationResults/Read | Get the result of the Async Put operation for Iot Hub SecuritySettings |
> | Åtgärd | Microsoft.Devices/iotHubs/securitySettings/Read | Get the Azure Security Center settings on Iot Hub |
> | Åtgärd | Microsoft.Devices/iotHubs/securitySettings/Write | Update the Azure Security Center settings on Iot Hub |
> | Åtgärd | Microsoft.Devices/iotHubs/skus/Read | Get valid IotHub Skus |
> | Åtgärd | Microsoft.Devices/iotHubs/Write | Create or update IotHub Resource |
> | Åtgärd | Microsoft.Devices/locations/operationresults/Read | Get Location based Operation Result |
> | Åtgärd | Microsoft.Devices/operationresults/Read | Get Operation Result |
> | Åtgärd | Microsoft.Devices/operations/Read | Get All ResourceProvider Operations |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Delete | Deletes Certificate |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generate Verification code |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Read | Gets the Certificate |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/verify/Action | Verify Certificate resource |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/Write | Create or Update Certificate |
> | Åtgärd | Microsoft.Devices/provisioningServices/Delete | Delete IotDps resource |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Get IotDps Keys for key name |
> | Åtgärd | Microsoft.Devices/provisioningServices/listkeys/Action | Get all IotDps keys |
> | Åtgärd | Microsoft.Devices/provisioningServices/logDefinitions/read | Gets the available log definitions for the provisioning Service |
> | Åtgärd | Microsoft.Devices/provisioningServices/metricDefinitions/read | Gets the available metrics for the provisioning service |
> | Åtgärd | Microsoft.Devices/provisioningServices/operationresults/Read | Get DPS Operation Result |
> | Åtgärd | Microsoft.Devices/provisioningServices/Read | Get IotDps resource |
> | Åtgärd | Microsoft.Devices/provisioningServices/skus/Read | Get valid IotDps Skus |
> | Åtgärd | Microsoft.Devices/provisioningServices/Write | Create IotDps resource |
> | Åtgärd | Microsoft.Devices/register/action | Register the subscription for the IotHub resource provider and enables the creation of IotHub resources |
> | Åtgärd | Microsoft.Devices/usages/Read | Get subscription usage details for this provider. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DevSpaces/controllers/delete | Delete Azure Dev Spaces Controller and dataplane services |
> | Åtgärd | Microsoft.DevSpaces/controllers/listConnectionDetails/action | List connection details for the Azure Dev Spaces Controller's infrastructure |
> | Åtgärd | Microsoft.DevSpaces/controllers/read | Read Azure Dev Spaces Controller properties |
> | Åtgärd | Microsoft.DevSpaces/controllers/write | Create or Update Azure Dev Spaces Controller properties |
> | Åtgärd | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Check existing controller mapping for a container host |
> | Åtgärd | Microsoft.DevSpaces/locations/operationresults/read | Read status for an asynchronous operation |
> | Åtgärd | Microsoft.DevSpaces/register/action | Register Microsoft Dev Spaces resource provider with a subscription |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DevTestLab/labCenters/delete | Delete lab centers. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/read | Read lab centers. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/write | Add or modify lab centers. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Read azure resource manager templates. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Generates an Azure Resource Manager template for the given artifact, uploads the required files to a storage account, and validates the generated artifact. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/read | Read artifacts. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/delete | Delete artifact sources. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/read | Read artifact sources. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/write | Add or modify artifact sources. |
> | Åtgärd | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Claim a random claimable virtual machine in the lab. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/read | Read costs. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/write | Add or modify costs. |
> | Åtgärd | Microsoft.DevTestLab/labs/CreateEnvironment/action | Create virtual machines in a lab. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/delete | Delete custom images. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/read | Read custom images. |
> | Åtgärd | Microsoft.DevTestLab/labs/customImages/write | Add or modify custom images. |
> | Åtgärd | Microsoft.DevTestLab/labs/delete | Delete labs. |
> | Åtgärd | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Ensure the current user has a valid profile in the lab. |
> | Åtgärd | Microsoft.DevTestLab/labs/ExportResourceUsage/action | Exports the lab resource usage into a storage account |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/delete | Delete formulas. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/read | Read formulas. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/write | Add or modify formulas. |
> | Åtgärd | Microsoft.DevTestLab/labs/galleryImages/read | Read gallery images. |
> | Åtgärd | Microsoft.DevTestLab/labs/GenerateUploadUri/action | Generate a URI for uploading custom disk images to a Lab. |
> | Åtgärd | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Import a virtual machine into a different lab. |
> | Åtgärd | Microsoft.DevTestLab/labs/ListVhds/action | List disk images available for custom image creation. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/delete | Delete notification channels. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/Notify/action | Send notification to provided channel. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/read | Read notification channels. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/write | Add or modify notification channels. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Evaluates lab policy. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/delete | Delete policies. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/read | Read policies. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/policies/write | Add or modify policies. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/read | Read policy sets. |
> | Åtgärd | Microsoft.DevTestLab/labs/read | Read labs. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/delete | Delete schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/Execute/action | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Lists all applicable schedules |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/read | Read schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/write | Add or modify schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/delete | Delete service runners. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/read | Read service runners. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/write | Add or modify service runners. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/delete | Delete shared galleries. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/read | Read shared galleries. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Delete shared images. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Read shared images. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Add or modify shared images. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/write | Add or modify shared galleries. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/delete | Delete user profiles. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Attach/action | Attach and create the lease of the disk to the virtual machine. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/delete | Delete disks. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Detach/action | Detach and break the lease of the disk attached to the virtual machine. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/read | Read disks. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/write | Add or modify disks. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/delete | Delete environments. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/read | Read environments. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/write | Add or modify environments. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/read | Read user profiles. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/delete | Delete secrets. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/read | Read secrets. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/write | Add or modify secrets. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Delete service fabrics. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Read service fabrics. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Delete schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Read schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Add or modify schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Start a service fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stop a service fabric |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Lägg till eller ändra Service Fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/write | Lägg till eller ändra användar profiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Koppla en ny eller befintlig datadisk till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Tillämpa artefakter på den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Bli ägare till en befintlig virtuell dator |
> | Åtgärd | Microsoft. DevTestLab/Labs/virtualMachines/ClearArtifactResults/Action | Tar bort artefakt resultatet från den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/delete | Ta bort virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Koppla från den angivna disken från den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/read | Läsa virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Distribuera om en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ändra storlek på virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Starta om en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Delete schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Read schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Add or modify schedules. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Starta en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Stoppa en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Överför alla data diskar som är anslutna till den virtuella datorn och ägs av den aktuella användaren. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Frigör ägarskap för en befintlig virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/write | Lägg till eller ändra virtuella datorer. |
> | Åtgärd | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Delete | Ta bort bastionhosts. |
> | Åtgärd | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Read | Läs bastionhosts. |
> | Åtgärd | Microsoft. DevTestLab/Labs/virtualNetworks/bastionHosts/Write | Lägg till eller ändra bastionhosts. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/delete | Ta bort virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/read | Läsa virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualNetworks/write | Lägg till eller ändra virtuella nätverk. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/delete | Ta bort pooler för virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/read | Läs virtuella dator pooler. |
> | Åtgärd | Microsoft.DevTestLab/labs/vmPools/write | Lägg till eller ändra pooler för virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/write | Add or modify labs. |
> | Åtgärd | Microsoft.DevTestLab/locations/operations/read | Read operations. |
> | Åtgärd | Microsoft.DevTestLab/register/action | Registers the subscription |
> | Åtgärd | Microsoft.DevTestLab/schedules/delete | Delete schedules. |
> | Åtgärd | Microsoft. DevTestLab/Schemalägg/kör/åtgärd | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/schedules/read | Read schedules. |
> | Åtgärd | Microsoft. DevTestLab/schemalägger/ommål/åtgärd | Uppdaterar ett schemas mål resurs-ID. |
> | Åtgärd | Microsoft.DevTestLab/schedules/write | Add or modify schedules. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DocumentDB/databaseAccountNames/read | Söker efter namn tillgänglighet. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/ta bort | Ta bort en samling. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Read status of the asynchronous operation. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/läsa | Läs en samling eller lista alla samlingar. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Read status of the asynchronous operation. Gäller endast för API-typer: ' MongoDB '. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/inställningar/läsa | Läs en samlings data flöde. Gäller endast för API-typer: ' MongoDB '. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/inställningar/Skriv | Uppdatera ett data flöde för samlingen. Gäller endast för API-typer: ' MongoDB '. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/skriva | Skapa eller uppdatera en samling. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/ta bort | Ta bort en behållare. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/operationResults/Read | Read status of the asynchronous operation. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/läsa | Läs en behållare eller lista alla behållare. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/inställningar/operationResults/Read | Read status of the asynchronous operation. Gäller endast för API-typer: SQL. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/inställningar/läsa | Läs ett data flöde för behållare. Gäller endast för API-typer: SQL. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/inställningar/Skriv | Uppdatera ett data flöde för behållare. Gäller endast för API-typer: SQL. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/skriva | Skapa eller uppdatera en behållare. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/ta bort | Ta bort en databas. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/Delete | Ta bort ett diagram. Only applicable to API types: 'gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/operationResults/Read | Read status of the asynchronous operation. Only applicable to API types: 'gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/graf/läsa | Läs ett diagram eller Visa en lista över alla grafer. Only applicable to API types: 'gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/operationResults/Read | Read status of the asynchronous operation. Only applicable to API types: 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/läsa | Läsa ett diagram genom strömning. Only applicable to API types: 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/Skriv | Uppdatera ett diagram genom strömning. Only applicable to API types: 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/graphs/write | Create or update a graph. Only applicable to API types: 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/read | Read a database or list all the databases. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/read | Read a database throughput. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/settings/write | Update a database throughput. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/write | Skapa en databas. Only applicable to API types: 'sql', 'mongodb', 'gremlin'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/delete | Delete a keyspace. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/read | Read a keyspace or list all the keyspaces. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/read | Read a keyspace throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/write | Update a keyspace throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/delete | Delete a table. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/read | Read a table or list all the tables. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/read | Read a table throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/settings/write | Update a table throughput. Only applicable to API types: 'cassandra'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/tables/write | Create or update a table. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/write | Create a keyspace. Only applicable to API types: 'cassandra'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/delete | Delete a table. Only applicable to API types: 'table'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'table'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/read | Read a table or list all the tables. Only applicable to API types: 'table'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/operationResults/read | Read status of the asynchronous operation. Only applicable to API types: 'table'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/read | Read a table throughput. Only applicable to API types: 'table'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/settings/write | Update a table throughput. Only applicable to API types: 'table'. Only applicable for setting types: 'throughput'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/tables/write | Create or update a table. Only applicable to API types: 'table'. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/backup/action | Submit a request to configure backup |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Change resource group of a database account |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Reads the collection metric definitions. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metrics/read | Reads the collection metrics. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Read database account partition key level metrics |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/metrics/read | Read database account partition level metrics |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitions/read | Read database account partitions in a collection |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/partitioner/användningar/läsa | Läs databas kontots partitionerings nivå användning |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/användningar/läsa | Läser samlings användningen. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Läser mått definitionerna för databasen |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/Metrics/Read | Läser databas måtten. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/användning/läsa | Läser databas användningarna. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Delete | Tar bort databas kontona. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/Action | Ändra prioritet för redundans för regioner i ett databas konto. Detta används för att utföra manuell redundansväxling |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Hämta säkerhets kopierings principen för databas kontot |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Hämta anslutnings strängar för ett databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Listnycklar/Action | Lista nycklar för ett databas konto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Läser definitionerna för databas konto mått. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Metrics/Read | Läser databas konto måtten. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/offlineRegion/Action | Offline en region i ett databas konto.  |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/onlineRegion/Action | Online en region i ett databas konto. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/operationResults/Read | Läs status för den asynkrona åtgärden |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/percentil/Metrics/Read | Läs svars tids mått |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/percentil/läsa | Läs percentiler av replikeringsfördröjning |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/percentil/sourceRegion/targetRegion/Metrics/Read | Läs fördröjnings mått för en bestämd käll-och mål region |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/percentil/targetRegion/Metrics/Read | Läs svars tids mått för en speciell mål region |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Delete | Ta bort en anslutning till en privat slutpunkt för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/operationResults/Read | Läs status för asynkron åtgärd för anslutnings proxy för privat slutpunkt |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Read | Läs en anslutning till en privat slutpunkt för anslutnings-proxy för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/åtgärd | Verifiera en anslutning till en privat slutpunkt för anslutnings proxy för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Skapa eller uppdatera en privat slut punkts anslutnings proxy för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Delete | Ta bort en privat slut punkts anslutning för ett databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/operationResults/Read | Läs status för asynkron åtgärd för privateEndpointConnenctions |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Read | Läs en privat slut punkts anslutning eller lista alla anslutningar för privata slut punkter för ett databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnections/Write | Skapa eller uppdatera en privat slut punkts anslutning för ett databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateLinkResources/Read | Läs en privat länk resurs eller lista alla privata länk resurser för ett databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Read | Läser ett databas konto. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databas kontots ReadOnly-nycklar. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Read | Läser databas kontots ReadOnly-nycklar. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/regenerateKey/Action | Rotera nycklar för ett databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/region/databaser/samlingar/mått/läsa | Läser de regionala samlings måtten. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Läs nationella databas kontons partitionerings nyckel nivå mått |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/region/databaser/samlingar/partitioner/mått/läsa | Läs mått för partitions nivå för regional databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/region/databaser/samlingar/partitioner/läsa | Läs regionala databas konto partitioner i en samling |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/region/Metrics/Read | Läser regions-och databas konto mått. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Skicka en begäran om återställning |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Usage/Read | Läser databas kontots användningar. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Write | Uppdatera ett databas konto. |
> | Åtgärd | Microsoft. DocumentDB/locations/deleteVirtualNetworkOrSubnets/Action | Meddelar Microsoft. DocumentDB att VirtualNetwork eller undernät tas bort |
> | Åtgärd | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Läs status för asynkron åtgärd för deleteVirtualNetworkOrSubnets |
> | Åtgärd | Microsoft.DocumentDB/locations/operationsStatus/read | Läser status för asynkrona åtgärder |
> | Åtgärd | Microsoft.DocumentDB/operationResults/read | Läs status för den asynkrona åtgärden |
> | Åtgärd | Microsoft. DocumentDB/Operations/Read | Läs åtgärder som är tillgängliga för Microsoft-DocumentDB  |
> | Åtgärd | Microsoft. DocumentDB/register/åtgärd |  Registrera Microsoft DocumentDB Resource Provider för prenumerationen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DomainRegistration/checkDomainAvailability/Action | Kontrol lera om en domän är tillgänglig för köp |
> | Åtgärd | Microsoft.DomainRegistration/domains/Delete | Ta bort en befintlig domän. |
> | Åtgärd | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Delete | Delete ownership identifier |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | List ownership identifiers |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Read | Get ownership identifier |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Create or update identifier |
> | Åtgärd | Microsoft.DomainRegistration/domains/operationresults/Read | Get a domain operation |
> | Åtgärd | Microsoft.DomainRegistration/domains/Read | Get the list of domains |
> | Åtgärd | Microsoft.DomainRegistration/domains/Read | Get domain |
> | Åtgärd | Microsoft.DomainRegistration/domains/renew/Action | Renew an existing domain. |
> | Åtgärd | Microsoft.DomainRegistration/domains/Write | Add a new Domain or update an existing one |
> | Åtgärd | Microsoft.DomainRegistration/generateSsoRequest/Action | Generate a request for signing into domain control center. |
> | Åtgärd | Microsoft.DomainRegistration/listDomainRecommendations/Action | Retrieve the list domain recommendations based on keywords |
> | Åtgärd | Microsoft.DomainRegistration/operations/Read | List all operations from app service domain registration |
> | Åtgärd | Microsoft.DomainRegistration/register/action | Register the Microsoft Domains resource provider for the subscription |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | List Agreement action |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/Read | Get toplevel domains |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/Read | Get toplevel domain |
> | Åtgärd | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Validate domain purchase object without submitting it |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventGrid/domains/delete | Delete a domain |
> | Åtgärd | Microsoft.EventGrid/domains/listKeys/action | List keys for a domain |
> | Åtgärd | Microsoft.EventGrid/domains/providers/Microsoft.Insights/logDefinitions/read | Allows access to diagnostic logs |
> | Åtgärd | Microsoft.EventGrid/domains/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for domains |
> | Åtgärd | Microsoft.EventGrid/domains/read | Read a domain |
> | Åtgärd | Microsoft.EventGrid/domains/regenerateKey/action | Regenerate key for a domain |
> | Åtgärd | Microsoft.EventGrid/domains/topics/delete | Delete a domain topic |
> | Åtgärd | Microsoft.EventGrid/domains/topics/read | Read a domain topic |
> | Åtgärd | Microsoft.EventGrid/domains/topics/write | Create or update a domain topic |
> | Åtgärd | Microsoft.EventGrid/domains/write | Create or update a domain |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/delete | Delete an eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Get full url for the event subscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for event subscriptions |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for event subscriptions |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for eventSubscriptions |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/read | Read an eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/write | Create or update an eventSubscription |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for topics |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for topics |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for topics |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/read | Read an extensionTopic. |
> | Åtgärd | Microsoft.EventGrid/locations/eventSubscriptions/read | List regional event subscriptions |
> | Åtgärd | Microsoft.EventGrid/locations/operationResults/read | Read the result of a regional operation |
> | Åtgärd | Microsoft.EventGrid/locations/operationsStatus/read | Read the status of a regional operation |
> | Åtgärd | Microsoft.EventGrid/locations/topictypes/eventSubscriptions/read | List regional event subscriptions by topictype |
> | Åtgärd | Microsoft.EventGrid/operationResults/read | Read the result of an operation |
> | Åtgärd | Microsoft.EventGrid/operations/read | List EventGrid operations. |
> | Åtgärd | Microsoft.EventGrid/operationsStatus/read | Read the status of an operation |
> | Åtgärd | Microsoft.EventGrid/register/action | Registers the subscription for the EventGrid resource provider. |
> | Åtgärd | Microsoft.EventGrid/topics/delete | Delete a topic |
> | Åtgärd | Microsoft.EventGrid/topics/listKeys/action | List keys for a topic |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for topics |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for topics |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/logDefinitions/read | Allows access to diagnostic logs |
> | Åtgärd | Microsoft.EventGrid/topics/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for topics |
> | Åtgärd | Microsoft.EventGrid/topics/read | Read a topic |
> | Åtgärd | Microsoft.EventGrid/topics/regenerateKey/action | Regenerate key for a topic |
> | Åtgärd | Microsoft.EventGrid/topics/write | Create or update a topic |
> | Åtgärd | Microsoft.EventGrid/topictypes/eventSubscriptions/read | List global event subscriptions by topic type |
> | Åtgärd | Microsoft.EventGrid/topictypes/eventtypes/read | Read eventtypes supported by a topictype |
> | Åtgärd | Microsoft.EventGrid/topictypes/read | Read a topictype |
> | Åtgärd | Microsoft.EventGrid/unregister/action | Unregisters the subscription for the EventGrid resource provider. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventHub/availableClusterRegions/read | Read operation to list available pre-provisioned clusters by Azure region. |
> | Åtgärd | Microsoft.EventHub/checkNameAvailability/action | Checks availability of namespace under given subscription. |
> | Åtgärd | Microsoft.EventHub/checkNamespaceAvailability/action | Checks availability of namespace under given subscription. This API is deprecated please use CheckNameAvailability instead. |
> | Åtgärd | Microsoft.EventHub/clusters/delete | Deletes an existing Cluster resource. |
> | Åtgärd | Microsoft.EventHub/clusters/namespaces/read | List namespace Azure Resource Manager IDs for namespaces within a cluster. |
> | Åtgärd | Microsoft.EventHub/clusters/operationresults/read | Get the status of an asynchronous cluster operation. |
> | Åtgärd | Microsoft.EventHub/clusters/providers/Microsoft.Insights/metricDefinitions/read | Get list of Cluster metrics Resource Descriptions |
> | Åtgärd | Microsoft.EventHub/clusters/read | Gets the Cluster Resource Description |
> | Åtgärd | Microsoft.EventHub/clusters/write | Skapar eller ändrar en befintlig kluster resurs. |
> | Åtgärd | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Tar bort VNet-reglerna i EventHub-adressresursen för angivet VNet |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/action | Updates Namespace Authorization Rule. This API is deprecated. Please use a PUT call to update the Namespace Authorization Rule instead.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Get the Connection String to the Namespace |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Åtgärd | Microsoft.EventHub/namespaces/Delete | Delete Namespace Resource |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Gets the authorization rules keys for the Disaster Recovery primary namespace |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Get Disaster Recovery Primary Namespace's Authorization Rules |
> | Åtgärd | Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Disables Disaster Recovery and stops replicating changes from primary to secondary namespaces. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Checks availability of namespace alias under given subscription. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Deletes the Disaster Recovery configuration associated with the namespace. This operation can only be invoked via the primary namespace. |
> | Åtgärd | Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/redundans/åtgärd | Invokes a GEO DR failover and reconfigures the namespace alias to point to the secondary namespace. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Gets the Disaster Recovery configuration associated with the namespace. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Creates or Updates the Disaster Recovery configuration associated with the namespace. |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Åtgärd för att uppdatera EventHub. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/authorizationRules/Delete | Åtgärd för att ta bort regler för EventHub-auktorisering |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/authorizationRules/listnycklar/Action | Hämta anslutnings strängen till EventHub |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Hämta listan över regler för EventHub-auktorisering |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Skapa regler för EventHub-auktorisering och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventHubs/consumergroups/Delete | Åtgärd för att ta bort ConsumerGroup-resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventHubs/consumergroups/Read | Hämta lista över beskrivningar av ConsumerGroup-resurser |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventHubs/consumergroups/Write | Skapa eller uppdatera ConsumerGroup-egenskaper. |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/Delete | Åtgärd för att ta bort EventHub-resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/Read | Get list of EventHub Resource Descriptions |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/Write | Skapa eller uppdatera egenskaper för EventHub. |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/delete | Delete IP Filter Resource |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/read | Get IP Filter Resource |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/write | Create IP Filter Resource |
> | DataAction | Microsoft. EventHub/Namespaces/meddelanden/ta emot/åtgärd | Ta emot meddelanden |
> | DataAction | Microsoft. EventHub/Namespaces/meddelanden/skicka/åtgärd | Skicka meddelanden |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/read | Gets the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/write | Updates the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft. EventHub/Namespaces/networkruleset/Delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/networkruleset/Read | Gets NetworkRuleSet Resource |
> | Åtgärd | Microsoft. EventHub/Namespaces/networkruleset/Write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/operationresults/Read | Get the status of Namespace operation |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Read | Get list of Namespace diagnostic settings Resource Descriptions |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Write | Get list of Namespace diagnostic settings Resource Descriptions |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/logDefinitions/Read | Get list of Namespace logs Resource Descriptions |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/metricDefinitions/Read | Get list of Namespace metrics Resource Descriptions |
> | Åtgärd | Microsoft. EventHub/Namespaces/Read | Hämta listan över Beskrivning av namn områdes resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnrymd |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Hämtar regel resurs för VNET |
> | Åtgärd | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft. EventHub/namnrymd/Skriv | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft. EventHub/register/åtgärd | Registrerar prenumerationen för EventHub-adressresursen och gör det möjligt att skapa EventHub-resurser |
> | Åtgärd | Microsoft.EventHub/sku/read | Hämta lista med resurs beskrivningar för SKU |
> | Åtgärd | Microsoft.EventHub/sku/regions/read | Hämta lista över beskrivningar av SkuRegions-resurser |
> | Åtgärd | Microsoft.EventHub/unregister/action | Registrerar EventHub-adressresursen |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. features/features/Read | Hämtar funktionerna i en prenumeration. |
> | Åtgärd | Microsoft. features/Operations/Read | Gets the list of operations. |
> | Åtgärd | Microsoft. features/providers/features/Read | Hämtar funktionen för en prenumeration i en specifik resurs leverantör. |
> | Åtgärd | Microsoft. features/providers/features/register/Action | Registrerar funktionen för en prenumeration i en specifik resurs leverantör. |
> | Åtgärd | Microsoft. features/providers/features/unregister/Action | Avregistrerar funktionen för en prenumeration i en specifik resurs leverantör. |
> | Åtgärd | Microsoft. features/register/åtgärd | Registrerar funktionen för en prenumeration. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Ta bort gäst konfigurations tilldelning. |
> | Åtgärd | Microsoft. GuestConfiguration/guestConfigurationAssignments/Read | Hämta gäst konfigurations tilldelning. |
> | Åtgärd | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Hämta tilldelnings rapport över gäst konfiguration. |
> | Åtgärd | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Skapa ny gäst konfigurations tilldelning. |
> | Åtgärd | Microsoft. GuestConfiguration/Operations/Read | Hämtar åtgärderna för Resource-providern för Microsoft. GuestConfiguration |
> | Åtgärd | Microsoft. GuestConfiguration/register/åtgärd | Registrerar prenumerationen för Microsoft. GuestConfiguration-resurs leverantören. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. HDInsight/kluster/program/ta bort | Ta bort program för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/program/läsa | Hämta program för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/program/Skriv | Skapa eller uppdatera program för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/changerdpsetting/åtgärd | Ändra RDP-inställningen för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/konfigurationer/åtgärd | Uppdatera konfiguration av HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/konfigurationer/åtgärd | Hämta HDInsight-klusterkonfigurationer |
> | Åtgärd | Microsoft. HDInsight/kluster/konfigurationer/läsa | Hämta HDInsight-klusterkonfigurationer |
> | Åtgärd | Microsoft. HDInsight/kluster/ta bort | Ta bort ett HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/tillägg/ta bort | Ta bort kluster tillägg för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/tillägg/läsa | Hämta kluster tillägg för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/tillägg/Skriv | Skapa kluster tillägg för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/getGatewaySettings/åtgärd | Hämta Gateway-inställningar för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/Clusters/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursens HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/Clusters/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursens HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/Clusters/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/läsa | Hämta information om HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/roller/ändra storlek/åtgärd | Ändra storlek på ett HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/updateGatewaySettings/åtgärd | Uppdatera Gateway-inställningar för HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/kluster/skrivning | Skapa eller uppdatera HDInsight-kluster |
> | Åtgärd | Microsoft. HDInsight/locations/Capabilities/Read | Hämta prenumerations funktioner |
> | Åtgärd | Microsoft. HDInsight/locations/checkNameAvailability/Read | Kontrol lera namn tillgänglighet |
> | Åtgärd | Microsoft. HDInsight/register/åtgärd | Registrera HDInsight-resurs leverantör för prenumerationen |
> | Åtgärd | Microsoft. HDInsight/avregistrera/åtgärd | Avregistrera HDInsight-resurs leverantör för prenumerationen |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ImportExport/Jobs/Delete | Tar bort ett befintligt jobb. |
> | Åtgärd | Microsoft. ImportExport/Jobs/listBitLockerKeys/åtgärd | Hämtar BitLocker-nycklar för det angivna jobbet. |
> | Åtgärd | Microsoft. ImportExport/Jobs/läsa | Hämtar egenskaperna för det angivna jobbet eller returnerar listan över jobb. |
> | Åtgärd | Microsoft. ImportExport/Jobs/Write | Skapar ett jobb med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för det angivna jobbet. |
> | Åtgärd | Microsoft. ImportExport/locations/Read | Hämtar egenskaperna för den angivna platsen eller returnerar listan över platser. |
> | Åtgärd | Microsoft. ImportExport/Operations/Read | Hämtar de åtgärder som stöds av resurs leverantören. |
> | Åtgärd | Microsoft. ImportExport/register/åtgärd | Registrerar prenumerationen för import-/export resurs leverantören och gör det möjligt att skapa import/export-jobb. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Insights/ActionGroups/Delete | Ta bort en åtgärds grupp |
> | Åtgärd | Microsoft. Insights/ActionGroups/Read | Läs en åtgärds grupp |
> | Åtgärd | Microsoft. Insights/ActionGroups/Write | Skapa eller uppdatera en åtgärds grupp |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivitets logg avisering aktive rad |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Delete | Ta bort en aktivitets logg avisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Read | Läs en aktivitets logg avisering |
> | Åtgärd | Microsoft.Insights/ActivityLogAlerts/Write | Skapa eller uppdatera en aktivitets logg avisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Activated/Action | Klassisk måtta avisering aktive rad |
> | Åtgärd | Microsoft.Insights/AlertRules/Delete | Ta bort en klassisk måtta avisering |
> | Åtgärd | Microsoft.Insights/AlertRules/Incidents/Read | Läs en klassisk måtta varnings incident |
> | Åtgärd | Microsoft. Insights/AlertRules/Read | Läs en klassisk måtta avisering |
> | Åtgärd | Microsoft. Insights/AlertRules/åtgärdad/åtgärd | Klassisk mått avisering har lösts |
> | Åtgärd | Microsoft. Insights/AlertRules/begränsad/åtgärd | Varnings regeln för klassisk mått har begränsats |
> | Åtgärd | Microsoft. Insights/AlertRules/Write | Skapa eller uppdatera en klassisk måtta avisering |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Delete | Ta bort en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Read a resource diagnostic setting |
> | Åtgärd | Microsoft. Insights/AutoscaleSettings/providers/Microsoft. Insights/diagnosticSettings/Write | Create or update a resource diagnostic setting |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Read log definitions |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Read metric definitions |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Read | Läs en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoscale scale down initiated |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Autoscale scale down completed |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoscale scale up initiated |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Autoscale scale up completed |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Write | Create or update an autoscale setting |
> | Åtgärd | Microsoft.Insights/Baseline/Read | Read a metric baseline (preview) |
> | Åtgärd | Microsoft.Insights/CalculateBaseline/Read | Calculate baseline for metric values (preview) |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Delete | Deleting an Application Insights analytics item |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Read | Reading an Application Insights analytics item |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Write | Writing an Application Insights analytics item |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Action | Application Insights analytics table action |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Delete | Deleting an Application Insights analytics table schema |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Read | Reading an Application Insights analytics table schema |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsTables/Write | Writing an Application Insights analytics table schema |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Delete | Deleting an Application Insights annotation |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Read | Reading an Application Insights annotation |
> | Åtgärd | Microsoft.Insights/Components/Annotations/Write | Writing an Application Insights annotation |
> | Åtgärd | Microsoft.Insights/Components/Api/Read | Reading Application Insights component data API |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Action | Generating an Application Insights API key |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Delete | Deleting an Application Insights API key |
> | Åtgärd | Microsoft.Insights/Components/ApiKeys/Read | Reading an Application Insights API key |
> | Åtgärd | Microsoft.Insights/Components/BillingPlanForComponent/Read | Reading a billing plan for Application Insights component |
> | Åtgärd | Microsoft.Insights/Components/CurrentBillingFeatures/Read | Reading current billing features for Application Insights component |
> | Åtgärd | Microsoft.Insights/Components/CurrentBillingFeatures/Write | Writing current billing features for Application Insights component |
> | Åtgärd | Microsoft.Insights/Components/DailyCapReached/Action | Reached the daily cap for Application Insights component |
> | Åtgärd | Microsoft.Insights/Components/DailyCapWarningThresholdReached/Action | Reached the daily cap warning threshold for Application Insights component |
> | Åtgärd | Microsoft.Insights/Components/DefaultWorkItemConfig/Read | Reading an Application Insights default ALM integration configuration |
> | Åtgärd | Microsoft.Insights/Components/Delete | Deleting an application insights component configuration |
> | Åtgärd | Microsoft.Insights/Components/Events/Read | Get logs from Application Insights using OData query format |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Action | Application Insights export settings action |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Delete | Deleting Application Insights export settings |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Read | Reading Application Insights export settings |
> | Åtgärd | Microsoft.Insights/Components/ExportConfiguration/Write | Writing Application Insights export settings |
> | Åtgärd | Microsoft.Insights/Components/ExtendQueries/Read | Reading Application Insights component extended query results |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Delete | Deleting an Application Insights favorite |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Read | Reading an Application Insights favorite |
> | Åtgärd | Microsoft.Insights/Components/Favorites/Write | Writing an Application Insights favorite |
> | Åtgärd | Microsoft.Insights/Components/FeatureCapabilities/Read | Reading Application Insights component feature capabilities |
> | Åtgärd | Microsoft.Insights/Components/GetAvailableBillingFeatures/Read | Reading Application Insights component available billing features |
> | Åtgärd | Microsoft.Insights/Components/GetToken/Read | Reading an Application Insights component token |
> | Åtgärd | Microsoft.Insights/Components/MetricDefinitions/Read | Reading Application Insights component metric definitions |
> | Åtgärd | Microsoft.Insights/Components/Metrics/Read | Reading Application Insights component metrics |
> | Åtgärd | Microsoft.Insights/Components/Move/Action | Move an Application Insights Component to another resource group or subscription |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Deleting an Application Insights personal analytics item |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Read | Reading an Application Insights personal analytics item |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Write | Writing an Application Insights personal analytics item |
> | Åtgärd | Microsoft.Insights/Components/MyFavorites/Read | Reading an Application Insights personal favorite |
> | Åtgärd | Microsoft.Insights/Components/Operations/Read | Get status of long-running operations in Application Insights |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Read | Reading an Application Insights component pricing plan |
> | Åtgärd | Microsoft.Insights/Components/PricingPlans/Write | Writing an Application Insights component pricing plan |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Read | Reading Application Insights proactive detection configuration |
> | Åtgärd | Microsoft.Insights/Components/ProactiveDetectionConfigs/Write | Writing Application Insights proactive detection configuration |
> | Åtgärd | Microsoft.Insights/Components/providers/Microsoft.Insights/MetricDefinitions/Read | Read metric definitions |
> | Åtgärd | Microsoft.Insights/Components/Purge/Action | Purging data from Application Insights |
> | Åtgärd | Microsoft.Insights/Components/Query/Read | Run queries against Application Insights logs |
> | Åtgärd | Microsoft.Insights/Components/QuotaStatus/Read | Reading Application Insights component quota status |
> | Åtgärd | Microsoft.Insights/Components/Read | Reading an application insights component configuration |
> | Åtgärd | Microsoft.Insights/Components/SyntheticMonitorLocations/Read | Reading Application Insights webtest locations |
> | Åtgärd | Microsoft.Insights/Components/Webtests/Read | Läser en webb test konfiguration |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Delete | Deleting an Application Insights ALM integration configuration |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Read | Reading an Application Insights ALM integration configuration |
> | Åtgärd | Microsoft.Insights/Components/WorkItemConfigs/Write | Writing an Application Insights ALM integration configuration |
> | Åtgärd | Microsoft.Insights/Components/Write | Writing to an application insights component configuration |
> | Åtgärd | Microsoft.Insights/DataCollectionRuleAssociations/Delete | Delete a resource's association with a data collection rule |
> | Åtgärd | Microsoft.Insights/DataCollectionRuleAssociations/Read | Read a resource's association with a data collection rule |
> | Åtgärd | Microsoft.Insights/DataCollectionRuleAssociations/Write | Create or update a resource's association with a data collection rule |
> | DataAction | Microsoft.Insights/DataCollectionRules/Data/Write | Send data to a data collection rule |
> | Åtgärd | Microsoft.Insights/DataCollectionRules/Delete | Delete a data collection rule |
> | Åtgärd | Microsoft.Insights/DataCollectionRules/Read | Read a data collection rule |
> | Åtgärd | Microsoft.Insights/DataCollectionRules/Write | Create or update a data collection rule |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Delete | Delete a resource diagnostic setting |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Read | Read a resource diagnostic setting |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Write | Create or update a resource diagnostic setting |
> | Åtgärd | Microsoft.Insights/EventCategories/Read | Read available Activity Log event categories |
> | Åtgärd | Microsoft.Insights/eventtypes/digestevents/Read | Read management event type digest |
> | Åtgärd | Microsoft.Insights/eventtypes/values/Read | Read Activity Log events |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Delete | Delete a network flow log diagnostic setting |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Read | Read a network flow log diagnostic setting |
> | Åtgärd | Microsoft.Insights/ExtendedDiagnosticSettings/Write | Create or update a network flow log diagnostic setting |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Action | Get back Subscription migration date |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Read | Get back subscription migration date |
> | Åtgärd | Microsoft.Insights/LogDefinitions/Read | Read log definitions |
> | Åtgärd | Microsoft.Insights/LogProfiles/Delete | Delete an Activity Log log profile |
> | Åtgärd | Microsoft.Insights/LogProfiles/Read | Read an Activity Log log profile |
> | Åtgärd | Microsoft.Insights/LogProfiles/Write | Create or update an Activity Log log profile |
> | Åtgärd | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Read data from the ADAssessmentRecommendation table |
> | Åtgärd | Microsoft.Insights/Logs/ADReplicationResult/Read | Read data from the ADReplicationResult table |
> | Åtgärd | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Read data from the ADSecurityAssessmentRecommendation table |
> | Åtgärd | Microsoft.Insights/Logs/Alert/Read | Read data from the Alert table |
> | Åtgärd | Microsoft.Insights/Logs/AlertHistory/Read | Read data from the AlertHistory table |
> | Åtgärd | Microsoft.Insights/Logs/ApplicationInsights/Read | Read data from the ApplicationInsights table |
> | Åtgärd | Microsoft.Insights/Logs/AzureActivity/Read | Read data from the AzureActivity table |
> | Åtgärd | Microsoft.Insights/Logs/AzureMetrics/Read | Read data from the AzureMetrics table |
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
> | Åtgärd | Microsoft.Insights/Logs/DeviceHealth/Read | Läsa data från tabellen DeviceHealth |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHeartbeat/Read | Läsa data från tabellen DeviceHeartbeat |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeHeartbeat/Read | Läsa data från tabellen DeviceSkypeHeartbeat |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Läsa data från tabellen DeviceSkypeSignIn |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSleepState/Read | Läsa data från tabellen DeviceSleepState |
> | Åtgärd | Microsoft.Insights/Logs/DHAppFailure/Read | Läsa data från tabellen DHAppFailure |
> | Åtgärd | Microsoft.Insights/Logs/DHAppReliability/Read | Läsa data från tabellen DHAppReliability |
> | Åtgärd | Microsoft.Insights/Logs/DHDriverReliability/Read | Läsa data från tabellen DHDriverReliability |
> | Åtgärd | Microsoft.Insights/Logs/DHLogonFailures/Read | Läsa data från tabellen DHLogonFailures |
> | Åtgärd | Microsoft. Insights/logs/DHLogonMetrics/Read | Läsa data från tabellen DHLogonMetrics |
> | Åtgärd | Microsoft. Insights/logs/DHOSCrashData/Read | Läsa data från tabellen DHOSCrashData |
> | Åtgärd | Microsoft. Insights/logs/DHOSReliability/Read | Läsa data från tabellen DHOSReliability |
> | Åtgärd | Microsoft. Insights/logs/DHWipAppLearning/Read | Läsa data från tabellen DHWipAppLearning |
> | Åtgärd | Microsoft. Insights/logs/DnsEvents/Read | Läsa data från tabellen DnsEvents |
> | Åtgärd | Microsoft. Insights/logs/DnsInventory/Read | Read data from the DnsInventory table |
> | Åtgärd | Microsoft. Insights/logs/ETWEvent/Read | Read data from the ETWEvent table |
> | Åtgärd | Microsoft. Insights/loggar/händelse/läsa | Read data from the Event table |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Read data from the ExchangeAssessmentRecommendation table |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Read data from the ExchangeOnlineAssessmentRecommendation table |
> | Åtgärd | Microsoft. Insights/loggar/pulsslag/läsning | Read data from the Heartbeat table |
> | Åtgärd | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Read data from the IISAssessmentRecommendation table |
> | Åtgärd | Microsoft. Insights/logs/InboundConnection/Read | Read data from the InboundConnection table |
> | Åtgärd | Microsoft. Insights/logs/KubeNodeInventory/Read | Read data from the KubeNodeInventory table |
> | Åtgärd | Microsoft. Insights/logs/KubePodInventory/Read | Read data from the KubePodInventory table |
> | Åtgärd | Microsoft. Insights/logs/LinuxAuditLog/Read | Read data from the LinuxAuditLog table |
> | Åtgärd | Microsoft. Insights/logs/MAApplication/Read | Read data from the MAApplication table |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealth/Read | Read data from the MAApplicationHealth table |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Read data from the MAApplicationHealthAlternativeVersions table |
> | Åtgärd | Microsoft. Insights/logs/MAApplicationHealthIssues/Read | Read data from the MAApplicationHealthIssues table |
> | Åtgärd | Microsoft. Insights/logs/MAApplicationInstance/Read | Read data from the MAApplicationInstance table |
> | Åtgärd | Microsoft. Insights/logs/MAApplicationInstanceReadiness/Read | Read data from the MAApplicationInstanceReadiness table |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Read data from the MAApplicationReadiness table |
> | Åtgärd | Microsoft. Insights/logs/MADeploymentPlan/Read | Read data from the MADeploymentPlan table |
> | Åtgärd | Microsoft. Insights/logs/MADevice/Read | Read data from the MADevice table |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Read data from the MADevicePnPHealth table |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Read data from the MADevicePnPHealthAlternativeVersions table |
> | Åtgärd | Microsoft. Insights/logs/MADevicePnPHealthIssues/Read | Read data from the MADevicePnPHealthIssues table |
> | Åtgärd | Microsoft. Insights/logs/MADeviceReadiness/Read | Read data from the MADeviceReadiness table |
> | Åtgärd | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Read data from the MADriverInstanceReadiness table |
> | Åtgärd | Microsoft. Insights/logs/MADriverReadiness/Read | Read data from the MADriverReadiness table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddin/Read | Read data from the MAOfficeAddin table |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Read data from the MAOfficeAddinHealth table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinHealthIssues/Read | Read data from the MAOfficeAddinHealthIssues table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinInstance/Read | Read data from the MAOfficeAddinInstance table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinInstanceReadiness/Read | Read data from the MAOfficeAddinInstanceReadiness table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinReadiness/Read | Read data from the MAOfficeAddinReadiness table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeApp/Read | Read data from the MAOfficeApp table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAppHealth/Read | Read data from the MAOfficeAppHealth table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAppInstance/Read | Read data from the MAOfficeAppInstance table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAppReadiness/Read | Read data from the MAOfficeAppReadiness table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeBuildInfo/Read | Read data from the MAOfficeBuildInfo table |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeCurrencyAssessment/Read | Read data from the MAOfficeCurrencyAssessment table |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Läsa data från tabellen MAOfficeCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeDeploymentStatus/Read | Läsa data från tabellen MAOfficeDeploymentStatus |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Läsa data från tabellen MAOfficeMacroHealth |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeMacroHealthIssues/Read | Läsa data från tabellen MAOfficeMacroHealthIssues |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Läsa data från tabellen MAOfficeMacroIssueInstanceReadiness |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeMacroIssueReadiness/Read | Läsa data från tabellen MAOfficeMacroIssueReadiness |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeMacroSummary/Read | Läsa data från tabellen MAOfficeMacroSummary |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeSuite/Read | Läsa data från tabellen MAOfficeSuite |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Läsa data från tabellen MAOfficeSuiteInstance |
> | Åtgärd | Microsoft. Insights/logs/MAProposedPilotDevices/Read | Läsa data från tabellen MAProposedPilotDevices |
> | Åtgärd | Microsoft. Insights/logs/MAWindowsBuildInfo/Read | Läsa data från tabellen MAWindowsBuildInfo |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Läsa data från tabellen MAWindowsCurrencyAssessment |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Läsa data från tabellen MAWindowsCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft. Insights/logs/MAWindowsDeploymentStatus/Read | Läsa data från tabellen MAWindowsDeploymentStatus |
> | Åtgärd | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Läsa data från tabellen MAWindowsSysReqInstanceReadiness |
> | Åtgärd | Microsoft. Insights/logs/NetworkMonitoring/Read | Läsa data från tabellen NetworkMonitoring |
> | Åtgärd | Microsoft. Insights/logs/OfficeActivity/Read | Läsa data från tabellen OfficeActivity |
> | Åtgärd | Microsoft. Insights/loggar/åtgärd/Läs | Läs data från åtgärds tabellen |
> | Åtgärd | Microsoft. Insights/logs/OutboundConnection/Read | Läsa data från tabellen OutboundConnection |
> | Åtgärd | Microsoft. Insights/loggar/perf/Read | Läs data från tabellen perf |
> | Åtgärd | Microsoft.Insights/Logs/ProtectionStatus/Read | Läsa data från tabellen ProtectionStatus |
> | Åtgärd | Microsoft.Insights/Logs/Read | Reading data from all your logs |
> | Åtgärd | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Read data from the ReservedAzureCommonFields table |
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
> | Åtgärd | Microsoft.Insights/Logs/Syslog/Read | Read data from the Syslog table |
> | Åtgärd | Microsoft.Insights/Logs/SysmonEvent/Read | Read data from the SysmonEvent table |
> | Åtgärd | Microsoft.Insights/Logs/Tables.Custom/Read | Reading data from any custom log |
> | Åtgärd | Microsoft.Insights/Logs/UAApp/Read | Read data from the UAApp table |
> | Åtgärd | Microsoft.Insights/Logs/UAComputer/Read | Read data from the UAComputer table |
> | Åtgärd | Microsoft.Insights/Logs/UAComputerRank/Read | Read data from the UAComputerRank table |
> | Åtgärd | Microsoft.Insights/Logs/UADriver/Read | Read data from the UADriver table |
> | Åtgärd | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Read data from the UADriverProblemCodes table |
> | Åtgärd | Microsoft.Insights/Logs/UAFeedback/Read | Read data from the UAFeedback table |
> | Åtgärd | Microsoft.Insights/Logs/UAHardwareSecurity/Read | Read data from the UAHardwareSecurity table |
> | Åtgärd | Microsoft.Insights/Logs/UAIESiteDiscovery/Read | Read data from the UAIESiteDiscovery table |
> | Åtgärd | Microsoft.Insights/Logs/UAOfficeAddIn/Read | Read data from the UAOfficeAddIn table |
> | Åtgärd | Microsoft.Insights/Logs/UAProposedActionPlan/Read | Read data from the UAProposedActionPlan table |
> | Åtgärd | Microsoft.Insights/Logs/UASysReqIssue/Read | Read data from the UASysReqIssue table |
> | Åtgärd | Microsoft.Insights/Logs/UAUpgradedComputer/Read | Read data from the UAUpgradedComputer table |
> | Åtgärd | Microsoft.Insights/Logs/Update/Read | Read data from the Update table |
> | Åtgärd | Microsoft.Insights/Logs/UpdateRunProgress/Read | Read data from the UpdateRunProgress table |
> | Åtgärd | Microsoft.Insights/Logs/UpdateSummary/Read | Read data from the UpdateSummary table |
> | Åtgärd | Microsoft.Insights/Logs/Usage/Read | Read data from the Usage table |
> | Åtgärd | Microsoft.Insights/Logs/W3CIISLog/Read | Read data from the W3CIISLog table |
> | Åtgärd | Microsoft.Insights/Logs/WaaSDeploymentStatus/Read | Read data from the WaaSDeploymentStatus table |
> | Åtgärd | Microsoft.Insights/Logs/WaaSInsiderStatus/Read | Read data from the WaaSInsiderStatus table |
> | Åtgärd | Microsoft.Insights/Logs/WaaSUpdateStatus/Read | Read data from the WaaSUpdateStatus table |
> | Åtgärd | Microsoft.Insights/Logs/WDAVStatus/Read | Read data from the WDAVStatus table |
> | Åtgärd | Microsoft.Insights/Logs/WDAVThreat/Read | Read data from the WDAVThreat table |
> | Åtgärd | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Read data from the WindowsClientAssessmentRecommendation table |
> | Åtgärd | Microsoft.Insights/Logs/WindowsFirewall/Read | Read data from the WindowsFirewall table |
> | Åtgärd | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Read data from the WindowsServerAssessmentRecommendation table |
> | Åtgärd | Microsoft.Insights/Logs/WireData/Read | Read data from the WireData table |
> | Åtgärd | Microsoft.Insights/Logs/WUDOAggregatedStatus/Read | Read data from the WUDOAggregatedStatus table |
> | Åtgärd | Microsoft.Insights/Logs/WUDOStatus/Read | Read data from the WUDOStatus table |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Delete | Delete a metric alert |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Read | Read a metric alert |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Status/Read | Read metric alert status |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Write | Create or update a metric alert |
> | Åtgärd | Microsoft.Insights/MetricBaselines/Read | Read metric baselines |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Read metric definitions |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/providers/Microsoft.Insights/Read | Read metric definitions |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Read | Read metric definitions |
> | Åtgärd | Microsoft.Insights/Metricnamespaces/Read | Read metric namespaces |
> | Åtgärd | Microsoft.Insights/Metrics/Action | Metric Action |
> | Åtgärd | Microsoft.Insights/Metrics/Microsoft.Insights/Read | Read metrics |
> | Åtgärd | Microsoft.Insights/Metrics/providers/Metrics/Read | Read metrics |
> | Åtgärd | Microsoft.Insights/Metrics/Read | Read metrics |
> | DataAction | Microsoft.Insights/Metrics/Write | Write metrics |
> | Åtgärd | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrate subscription to new pricing model |
> | Åtgärd | Microsoft.Insights/Operations/Read | Läsåtgärder |
> | Åtgärd | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/RollbackToLegacyPricingModel/Action | Rollback subscription to legacy pricing model |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Delete | Deleting a scheduled query rule |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Read | Reading a scheduled query rule |
> | Åtgärd | Microsoft.Insights/ScheduledQueryRules/Write | Writing a scheduled query rule |
> | Åtgärd | Microsoft.Insights/Tenants/Register/Action | Initierar Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Unregister/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft. Insights/webbtester/ta bort | Tar bort en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/webbtester/GetToken/Read | Läser en webtest-token |
> | Åtgärd | Microsoft. Insights/webbtester/MetricDefinitions/Read | Läsning av mått definitioner för webbtest |
> | Åtgärd | Microsoft. Insights/webbtester/mått/läsa | Läsning av mått för webbtest |
> | Åtgärd | Microsoft. Insights/webbtester/läsa | Läser en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/webbtester/skriva | Skriver till en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/arbets böcker/ta bort | Ta bort en arbetsbok |
> | Åtgärd | Microsoft. Insights/arbets böcker/läsa | Läs en arbets bok |
> | Åtgärd | Microsoft. Insights/arbets böcker/skriva | Skapa eller uppdatera en arbets bok |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Intune/diagnosticsettings/Delete | Tar bort en diagnostisk inställning |
> | Åtgärd | Microsoft. Intune/diagnosticsettings/Read | Läser en diagnostisk inställning |
> | Åtgärd | Microsoft. Intune/diagnosticsettings/Write | Skriva en diagnostisk inställning |
> | Åtgärd | Microsoft. Intune/diagnosticsettingscategories/Read | Läser en diagnostisk inställnings kategori |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.IoTCentral/appTemplates/action | Hämtar alla tillgängliga programmallar på Azure IoT Central |
> | Åtgärd | Microsoft.IoTCentral/checkNameAvailability/action | Kontrollerar om ett IoT Central program namn är tillgängligt |
> | Åtgärd | Microsoft.IoTCentral/checkSubdomainAvailability/action | Kontrollerar om en IoT Central program under domän är tillgänglig |
> | Åtgärd | Microsoft. IoTCentral/IoTApps/Delete | Tar bort ett IoT Central program |
> | Åtgärd | Microsoft.IoTCentral/IoTApps/read | Hämtar ett enda IoT Central-program |
> | Åtgärd | Microsoft.IoTCentral/IoTApps/write | Skapar eller uppdaterar ett IoT Central program |
> | Åtgärd | Microsoft.IoTCentral/operations/read | Hämtar alla tillgängliga åtgärder på IoT Central program |
> | Åtgärd | Microsoft.IoTCentral/register/action | Registrera prenumerationen för Azure IoT Central Resource Provider |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.IoTSpaces/Graph/delete | Tar bort Microsoft. IoTSpaces Graph-resurs |
> | Åtgärd | Microsoft.IoTSpaces/Graph/read | Hämtar diagram resurs (er) för Microsoft. IoTSpaces |
> | Åtgärd | Microsoft.IoTSpaces/Graph/write | Skapa Microsoft. IoTSpaces Graph-resurs |
> | Åtgärd | Microsoft. IoTSpaces/register/åtgärd | Registrera prenumerationen för Microsoft. IoTSpaces Graph Resource Provider om du vill aktivera att resurser skapas |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. nyckel valv/checkNameAvailability/läsa | Kontrollerar att nyckel valvets namn är giltigt och inte används |
> | Åtgärd | Microsoft.KeyVault/deletedVaults/read | Visa egenskaperna för mjuka borttagna nyckel valv |
> | Åtgärd | Microsoft. nyckel valv/hsmPools/ta bort | Ta bort en HSM-pool |
> | Åtgärd | Microsoft. nyckel valv/hsmPools/joinVault/åtgärd | Ansluta ett nyckel valv till en HSM-pool |
> | Åtgärd | Microsoft. nyckel valv/hsmPools/läsa | Visa egenskaperna för en HSM-pool |
> | Åtgärd | Microsoft. nyckel valv/hsmPools/skrivning | Skapa en ny HSM-pool med uppdatera egenskaperna för en befintlig HSM-pool |
> | Åtgärd | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent borttaget nyckel valv |
> | Åtgärd | Microsoft. nyckel-valv/platser/deletedVaults/läsa | Visa egenskaperna för ett ej permanent borttaget nyckel valv |
> | Åtgärd | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft. nyckel valv att ett virtuellt nätverk eller undernät tas bort |
> | Åtgärd | Microsoft.KeyVault/locations/operationResults/read | Kontrol lera resultatet av en lång körnings åtgärd |
> | Åtgärd | Microsoft.KeyVault/operations/read | Visar en lista över åtgärder som är tillgängliga på Microsoft. valv resurs leverantör |
> | Åtgärd | Microsoft. nyckel valv/register/åtgärd | Registrerar en prenumeration |
> | Åtgärd | Microsoft.KeyVault/unregister/action | Avregistrerar en prenumeration |
> | Åtgärd | Microsoft.KeyVault/vaults/accessPolicies/write | Uppdatera en befintlig åtkomst princip genom att slå samman eller ersätta eller lägga till en ny åtkomst princip i ett valv. |
> | Åtgärd | Microsoft.KeyVault/vaults/delete | Ta bort ett nyckelvalv |
> | Åtgärd | Microsoft.KeyVault/vaults/deploy/action | Ger åtkomst till hemligheter i ett nyckel valv när du distribuerar Azure-resurser |
> | Åtgärd | Microsoft.KeyVault/vaults/eventGridFilters/delete | Meddelar Microsoft. Vault att en EventGrid-prenumeration för Key Vault tas bort |
> | Åtgärd | Microsoft.KeyVault/vaults/eventGridFilters/read | Meddelar Microsoft. Vault att en EventGrid-prenumeration för Key Vault visas |
> | Åtgärd | Microsoft.KeyVault/vaults/eventGridFilters/write | Meddelar Microsoft. Vault att en ny EventGrid-prenumeration för Key Vault skapas |
> | Åtgärd | Microsoft.KeyVault/vaults/read | Visa egenskaperna för ett nyckel valv |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/read | Visa egenskaperna för en hemlighet, men inte dess värde. |
> | Åtgärd | Microsoft.KeyVault/vaults/secrets/write | Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet. |
> | Åtgärd | Microsoft.KeyVault/vaults/write | Skapa ett nytt nyckel valv eller uppdatera egenskaperna för ett befintligt nyckel valv |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Kusto/kluster/aktivera/åtgärd | Starts the cluster. |
> | Åtgärd | Microsoft. Kusto/kluster/AttachedDatabaseConfigurations/Delete | Tar bort en bifogad databas konfigurations resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/AttachedDatabaseConfigurations/Read | Läser en länkad databas konfiguration resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/AttachedDatabaseConfigurations/Write | Skriver en bifogad databas konfigurations resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/CheckNameAvailability/åtgärd | Kontrollerar tillgänglighet för kluster namn. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Lägger till databasens huvud namn. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/CheckNameAvailability/åtgärd | Kontrollerar namn tillgänglighet för en specifik typ. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/DataConnections/Delete | Tar bort en data anslutning resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/DataConnections/Read | Läser en data anslutning resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/DataConnections/Write | Skriver en data anslutning resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/DataConnectionValidation/åtgärd | Verifierar databas data anslutning. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/ta bort | Tar bort en databas resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Tar bort en Event Hub-resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Läser resourceCopy för Event Hub-anslutningar. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Skriver resourceCopy för Event Hub-anslutningar. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Verifierar anslutning till databasens händelsehubben. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/ListPrincipals/åtgärd | Visar en lista över databasens säkerhets objekt. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/PrincipalAssignments/Delete | Tar bort en databas säkerhets tilldelning resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/PrincipalAssignments/Read | Läser en databas huvud tilldelning resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/PrincipalAssignments/Write | Skriver en databas säkerhets tilldelning resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/läsa | Läser en databas-resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/RemovePrincipals/åtgärd | Tar bort databasens huvud namn. |
> | Åtgärd | Microsoft.Kusto/Clusters/Databases/write | Writes a database resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/Deactivate/action | Stops the cluster. |
> | Åtgärd | Microsoft.Kusto/Clusters/delete | Deletes a cluster resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/DetachFollowerDatabases/action | Detaches follower's databases. |
> | Åtgärd | Microsoft.Kusto/Clusters/DiagnoseVirtualNetwork/action | Diagnoses network connectivity status for external resources on which the service is depedent on. |
> | Åtgärd | Microsoft.Kusto/Clusters/ListFollowerDatabases/action | Lists the follower's databases. |
> | Åtgärd | Microsoft.Kusto/Clusters/read | Reads a cluster resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/SKUs/read | Reads a cluster SKU resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/Start/action | Starts the cluster. |
> | Åtgärd | Microsoft.Kusto/Clusters/Stop/action | Stops the cluster. |
> | Åtgärd | Microsoft.Kusto/Clusters/write | Writes a cluster resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Locations/CheckNameAvailability/action | Checks resourceCopy name availability. |
> | Åtgärd | Microsoft.Kusto/Locations/GetNetworkPolicies/action | Gets Network Intent Policies |
> | Åtgärd | Microsoft.Kusto/locations/operationresults/read | Reads operations resourceCopys |
> | Åtgärd | Microsoft.Kusto/Operations/read | Reads operations resourceCopys |
> | Åtgärd | Microsoft.Kusto/register/action | Subscription Registration Action |
> | Åtgärd | Microsoft.Kusto/Register/action | Registers the subscription to the Kusto Resource Provider. |
> | Åtgärd | Microsoft.Kusto/SKUs/read | Reads a SKU resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Unregister/action | Unregisters the subscription to the Kusto Resource Provider. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LabServices/labAccounts/CreateLab/action | Create a lab in a lab account. |
> | Åtgärd | Microsoft.LabServices/labAccounts/delete | Delete lab accounts. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/delete | Delete gallery images. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/read | Read gallery images. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/write | Add or modify gallery images. |
> | Åtgärd | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Get regional availability information for each size category configured under a lab account |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/AddUsers/action | Add users to a lab |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/delete | Delete labs. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Delete environment setting. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Delete environments. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Read environments. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/ResetPassword/action | Resets the user password on an environment |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Starts an environment by starting all resources inside the environment. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stops an environment by stopping all resources inside the environment |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Add or modify environments. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Provisions/deprovisions required resources for an environment setting based on current state of the lab/environment setting. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Read environment setting. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/ResetPassword/action | Resets password on the template virtual machine. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Saves current template image to the shared gallery in the lab account |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Delete schedules. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Read schedules. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Add or modify schedules. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Starts a template by starting all resources inside the template. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stops a template by stopping all resources inside the template. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Add or modify environment setting. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/read | Read labs. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/SendEmail/action | Send email with registration link to the lab |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/delete | Delete users. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/read | Read users. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/write | Add or modify users. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/write | Add or modify labs. |
> | Åtgärd | Microsoft.LabServices/labAccounts/read | Read lab accounts. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Delete sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/read | Read sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/write | Add or modify sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/delete | Delete sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/read | Read sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/write | Add or modify sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/write | Add or modify lab accounts. |
> | Åtgärd | Microsoft.LabServices/locations/operations/read | Read operations. |
> | Åtgärd | Microsoft.LabServices/register/action | Registers the subscription |
> | Åtgärd | Microsoft.LabServices/users/ListAllEnvironments/action | List all Environments for the user |
> | Åtgärd | Microsoft.LabServices/users/Register/action | Register a user to a managed lab |
> | Åtgärd | Microsoft.LabServices/users/ResetPassword/action | Resets the user password on an environment |
> | Åtgärd | Microsoft.LabServices/users/StartEnvironment/action | Starts an environment by starting all resources inside the environment. |
> | Åtgärd | Microsoft.LabServices/users/StopEnvironment/action | Stops an environment by stopping all resources inside the environment |
> | Åtgärd | Microsoft.LabServices/users/UserSettings/action | Updates and returns personal user settings. |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/delete | Deletes the agreement in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Gets the callback URL for agreement content in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/read | Reads the agreement in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/write | Creates or updates the agreement in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/delete | Deletes the assembly in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Gets the callback URL for assembly content in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/read | Reads the assembly in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/write | Creates or updates the assembly in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Deletes the batch configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Reads the batch configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Creates or updates the batch configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/delete | Deletes the certificate in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/read | Reads the certificate in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/write | Creates or updates the certificate in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/delete | Deletes the integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/join/action | Joins the Integration Account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Gets the callback URL for integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Gets the keys in the key vault. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Logs the tracking events in the integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/delete | Deletes the map in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Gets the callback URL for map content in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/read | Reads the map in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/write | Creates or updates the map in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/delete | Deletes the partner in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Gets the callback URL for partner content in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/read | Reads the partner in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/write | Creates or updates the partner in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Reads the Integration Account log definitions. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/read | Reads the integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Regenerates the access key secrets. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/delete | Deletes the RosettaNet process configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/read | Reads the RosettaNet process configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/rosettaNetProcessConfigurations/write | Creates or updates the  RosettaNet process configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/delete | Deletes the schema in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Gets the callback URL for schema content in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/read | Reads the schema in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/write | Creates or updates the schema in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/delete | Deletes the session in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/read | Reads the batch configuration in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/write | Creates or updates the session in integration account. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/write | Creates or updates the integration account. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/availableManagedApis/read | Reads the integration service environment available managed APIs. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/delete | Deletes the integration service environment. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/join/action | Joins the Integration Service Environment. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Reads the integration service environment managed API operation. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/join/action | Joins the Integration Service Environment Managed API. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/operationStatuses/read | Reads the integration service environment managed API operation statuses. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Reads the integration service environment managed API. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/write | Creates or updates the integration service environment managed API. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Reads the integration service environment operation statuses. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Reads the integration service environment metric definitions. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/read | Reads the integration service environment. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/write | Creates or updates the integration service environment. |
> | Åtgärd | Microsoft.Logic/locations/workflows/recommendOperationGroups/action | Gets the workflow recommend operation groups. |
> | Åtgärd | Microsoft.Logic/locations/workflows/validate/action | Verifierar arbets flödet. |
> | Åtgärd | Microsoft.Logic/operations/read | Gets the operation. |
> | Åtgärd | Microsoft.Logic/register/action | Registers the Microsoft.Logic resource provider for a given subscription. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/delete | Deletes the access key. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/list/action | Lists the access key secrets. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/read | Reads the access key. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/regenerate/action | Regenerates the access key secrets. |
> | Åtgärd | Microsoft.Logic/workflows/accessKeys/write | Creates or updates the access key. |
> | Åtgärd | Microsoft.Logic/workflows/delete | Deletes the workflow. |
> | Åtgärd | Microsoft.Logic/workflows/detectors/read | Reads the workflow detector. |
> | Åtgärd | Microsoft.Logic/workflows/disable/action | Disables the workflow. |
> | Åtgärd | Microsoft.Logic/workflows/enable/action | Aktiverar arbetsflödet |
> | Åtgärd | Microsoft.Logic/workflows/listCallbackUrl/action | Gets the callback URL for workflow. |
> | Åtgärd | Microsoft.Logic/workflows/listSwagger/action | Gets the workflow swagger definitions. |
> | Åtgärd | Microsoft.Logic/workflows/move/action | Moves Workflow from its existing subscription id, resource group, and/or name to a different subscription id, resource group, and/or name. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/read | Reads the workflow diagnostic settings. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the workflow diagnostic setting. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/logDefinitions/read | Reads the workflow log definitions. |
> | Åtgärd | Microsoft.Logic/workflows/providers/Microsoft.Insights/metricDefinitions/read | Reads the workflow metric definitions. |
> | Åtgärd | Microsoft.Logic/workflows/read | Reads the workflow. |
> | Åtgärd | Microsoft.Logic/workflows/regenerateAccessKey/action | Regenerates the access key secrets. |
> | Åtgärd | Microsoft.Logic/workflows/run/action | Starts a run of the workflow. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/listExpressionTraces/action | Gets the workflow run action expression traces. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/read | Reads the workflow run action. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/listExpressionTraces/action | Gets the workflow run action repetition expression traces. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/read | Reads the workflow run action repetition. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/repetitions/requestHistories/read | Reads the workflow run repetition action request history. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/requestHistories/read | Reads the workflow run action request history. |
> | Åtgärd | Microsoft.Logic/workflows/runs/actions/scoperepetitions/read | Reads the workflow run action scope repetition. |
> | Åtgärd | Microsoft.Logic/workflows/runs/cancel/action | Cancels the run of a workflow. |
> | Åtgärd | Microsoft.Logic/workflows/runs/delete | Deletes a run of a workflow. |
> | Åtgärd | Microsoft.Logic/workflows/runs/operations/read | Reads the workflow run operation status. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/körs/läsa | Läser arbets flödes körningen. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/Suspend/åtgärd | Pausar arbets flödet. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/historik/läsa | Läser utlösnings historiken. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/händelser/skicka igen/åtgärd | Skickar om arbets flödes utlösaren. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/listCallbackUrl/åtgärd | Hämtar återanrops-URL för utlösare. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/läsa | Läser utlösaren. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/Återställ/åtgärd | Återställer utlösaren. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/kör/åtgärd | Kör utlösaren. |
> | Åtgärd | Microsoft. Logic/arbets flöden/utlösare/setState/åtgärd | Anger utlösarens tillstånd. |
> | Åtgärd | Microsoft.Logic/workflows/validate/action | Verifierar arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/versioner/läsa | Läser arbets flödes versionen. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/-versioner/utlösare/listCallbackUrl/åtgärd | Hämtar återanrops-URL för utlösare. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/skriva | Skapar eller uppdaterar arbets flödet. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Flytta en Machine Learning åtagande Plans koppling |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Läs en Machine Learning åtagande Plans koppling |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/delete | Ta bort en Machine Learning åtagande plan |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/join/action | Delta i en Machine Learning åtagande plan |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/read | Läs en Machine Learning åtagande plan |
> | Åtgärd | Microsoft.MachineLearning/commitmentPlans/write | Skapa eller uppdatera en Machine Learning åtagande plan |
> | Åtgärd | Microsoft.MachineLearning/locations/operationresults/read | Hämta resultatet av en Machine Learning-åtgärd |
> | Åtgärd | Microsoft.MachineLearning/locations/operationsstatus/read | Hämta status för en pågående Machine Learning åtgärd |
> | Åtgärd | Microsoft.MachineLearning/operations/read | Hämta Machine Learning åtgärder |
> | Åtgärd | Microsoft.MachineLearning/register/action | Registrerar prenumerationen för resurs leverantören för Machine Learning-webbtjänsten och gör det möjligt att skapa webb tjänster. |
> | Åtgärd | Microsoft.MachineLearning/skus/read | Hämta Machine Learning åtagande plan SKU: er |
> | Åtgärd | Microsoft.MachineLearning/webServices/action | Skapa regionala webb tjänst egenskaper för regioner som stöds |
> | Åtgärd | Microsoft.MachineLearning/webServices/delete | Ta bort alla Machine Learning webb tjänster |
> | Åtgärd | Microsoft.MachineLearning/webServices/listkeys/read | Hämta nycklar till en Machine Learning-webbtjänst |
> | Åtgärd | Microsoft.MachineLearning/webServices/read | Läs alla Machine Learning webb tjänster |
> | Åtgärd | Microsoft.MachineLearning/webServices/write | Skapa eller uppdatera webb tjänsten för Machine Learning |
> | Åtgärd | Microsoft. MachineLearning/arbets ytor/ta bort | Ta bort eventuella Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Lista nycklar för en Machine Learning-arbetsyta |
> | Åtgärd | Microsoft. MachineLearning/arbets ytor/läsa | Läs eventuella Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Omsynkronisera nycklar för ett lagrings konto som har kon figurer ATS för en Machine Learning-arbetsyta |
> | Åtgärd | Microsoft.MachineLearning/Workspaces/write | Skapa eller uppdatera Machine Learning-arbetsyta |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Hämtar status för en viss beräknings åtgärd |
> | Åtgärd | Microsoft. MachineLearningServices/locations/kvoter/read | Hämtar de nuvarande tilldelade kvoterna för arbets ytan baserat på VMFamily. |
> | Åtgärd | Microsoft. MachineLearningServices/locations/updateQuotas/Action | Uppdatera kvoten för varje VM-serie på arbets ytan. |
> | Åtgärd | Microsoft.MachineLearningServices/locations/usages/read | Användnings rapport för AML-beräknings resurser i en prenumeration |
> | Åtgärd | Microsoft.MachineLearningServices/locations/vmsizes/read | Hämta VM-storlekar som stöds |
> | Åtgärd | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Hämtar status för en viss arbets ytans åtgärd |
> | Åtgärd | Microsoft.MachineLearningServices/register/action | Registrerar prenumerationen för Machine Learning Services Resource Provider |
> | Åtgärd | Microsoft. MachineLearningServices/arbets ytor/beräkningar/ta bort | Tar bort beräknings resurserna i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista hemligheter för beräknings resurser i Machine Learning Services arbets yta |
> | Åtgärd | Microsoft. MachineLearningServices/arbets ytor/computes/listNodes/Action | Lista noder för beräknings resurs i Machine Learning Services arbets yta |
> | Åtgärd | Microsoft. MachineLearningServices/arbets ytor/beräkningar/läsning | Hämtar beräknings resurserna i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/write | Skapar eller uppdaterar beräknings resurserna i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/datadriftdetectors/Read | Hämtar data avvikelser i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/datadriftdetectors/Write | Skapar eller uppdaterar data avvikelser i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/registrerade/ta bort | Tar bort registrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/registrerade/för hands version/läsa | Hämtar för hands versionen av data uppsättningar för registrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/registrerad/profil/läsa | Hämtar data uppsättnings profiler för registrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/registrerade/profil/Skriv | Skapar eller uppdaterar data uppsättnings profiler i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/registrerade/lästa | Hämtar registrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/registrerade/Skriv | Skapar eller uppdaterar registrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/oregistrerade/ta bort | Tar bort oregistrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/oregistrerade/förhands granskning/läsa | Hämtar för hands versionen av data uppsättningen för oregistrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/avregistreras/profil/läsning | Hämtar data uppsättnings profiler för oregistrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/data uppsättningar/oregistrerade/lästa | Hämtar oregistrerade data uppsättningar i Machine Learning Services arbets ytor |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write | Creates or updates unregistered datasets in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datastores/delete | Deletes datastores in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datastores/read | Gets datastores in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/datastores/write | Creates or updates datastores in Machine Learning Services Workspace(s) |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/delete | Deletes the Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read | Gets published pipelines and pipeline endpoints  in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write | Creates or updates published pipelines and pipeline endpoints in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/build/action | Builds environments in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/read | Gets environments in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/readSecrets/action | Gets environments with secrets in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/environments/write | Creates or updates environments in Machine Learning Services Workspace(s) |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/eventGridFilters/read | Get an Event Grid filter for a particular workspace |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/delete | Deletes experiments in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/read | Gets experiments in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/runs/read | Gets runs in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/runs/scriptRun/submit/action | Creates or updates script runs in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/runs/write | Creates or updates runs in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Creates or updates experiments in Machine Learning Services Workspace(s) |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/listKeys/action | List secrets for a Machine Learning Services Workspace |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/delete | Deletes artifacts in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read | Gets artifacts in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write | Creates or updates artifacts in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/delete | Deletes snapshots in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read | Gets snapshots in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write | Creates or updates snapshots in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/delete | Deletes models in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/download/action | Downloads models in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/package/action | Packages models in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/read | Gets models in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/models/write | Creates or updates models in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/modules/read | Gets modules in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/modules/write | Creates or updates module in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/delete | Deletes pipeline drafts in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/read | Gets pipeline drafts in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write | Creates or updates pipeline drafts in Machine Learning Services Workspace(s) |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/read | Gets the Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aci/delete | Deletes ACI services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft.MachineLearningServices/workspaces/services/aci/listkeys/action | Lists keys for ACI services in Machine Learning Services Workspace(s) |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/ACI/Write | Skapar eller uppdaterar ACI-tjänster i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/DevTest/Delete | Tar bort DevTest AKS-tjänster i Machine Learning Services-arbetsytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/DevTest/listnycklar/åtgärd | Visar nycklar för DevTest AKS-tjänster i Machine Learning Services-arbetsytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/DevTest/score/åtgärd | Score DevTest AKS Services i Machine Learning Services-arbetsytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/DevTest/Write | Skapar eller uppdaterar DevTest AKS Services i Machine Learning Services-arbetsytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/Prod/Delete | Tar bort AKS-tjänster i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/Prod/listnycklar/Action | Visar nycklar för AKS-tjänster i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/Prod/score/åtgärd | Score AKS-tjänster i Machine Learning Services-arbetsytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/AKS/Prod/Write | Skapar eller uppdaterar Prod AKS Services i Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/tjänster/läsa | Hämtar tjänster i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/write | Skapar eller uppdaterar Machine Learning Services arbets ytor |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ManagedIdentity/identiteter/läsa | Hämtar en befintlig systemtilldelad identitet |
> | Åtgärd | Microsoft. ManagedIdentity/Operations/Read | Visar en lista över åtgärder som är tillgängliga på Microsoft. ManagedIdentity Resource Provider |
> | Åtgärd | Microsoft.ManagedIdentity/register/action | Registrerar prenumerationen för den hanterade identitets resurs leverantören |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC-åtgärd för att tilldela en befintlig användare tilldelad identitet till en resurs |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Tar bort en befintlig användare tilldelad identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hämtar en befintlig användare tilldelad identitet |
> | Åtgärd | Microsoft.ManagedIdentity/userAssignedIdentities/write | Skapar en ny tilldelad identitet eller uppdaterar de taggar som är associerade med en befintlig användare som tilldelats identiteten |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/Read | Hämtar en lista över registrerings definitioner för hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/Operations/Read | Hämtar en lista över hanterade tjänste åtgärder. |
> | Åtgärd | Microsoft. ManagedServices/operationStatuses/Read | Reads the operation status for the resource. |
> | Åtgärd | Microsoft. ManagedServices/register/åtgärd | Registrera dig för hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/registrationAssignments/Delete | Tar bort registrering av hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/registrationAssignments/Read | Hämtar en lista över uppgifter för registrering av hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/registrationAssignments/Write | Lägg till eller ändra registrerings tilldelningen för hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/registrationDefinitions/Delete | Tar bort registrerings definition för hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/registrationDefinitions/Read | Hämtar en lista över registrerings definitioner för hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/registrationDefinitions/Write | Lägg till eller ändra registrerings definitionen för hanterade tjänster. |
> | Åtgärd | Microsoft. ManagedServices/avregistrera/åtgärd | Avregistrera från hanterade tjänster. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Management/checkNameAvailability/action | Kontrollerar om det angivna hanterings grupp namnet är giltigt och unikt. |
> | Åtgärd | Microsoft.Management/getEntities/action | Lista alla entiteter (Hanteringsgrupper, prenumerationer osv.) för den autentiserade användaren. |
> | Åtgärd | Microsoft.Management/managementGroups/delete | Ta bort hanterings grupp. |
> | Åtgärd | Microsoft. Management/managementGroups/Descendants/Read | Hämtar alla underordnade (Hanteringsgrupper, prenumerationer) för en hanterings grupp. |
> | Åtgärd | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | Åtgärd | Microsoft.Management/managementGroups/subscriptions/delete | Ta bort prenumerationen från hanterings gruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/subscriptions/write | Kopplar en befintlig prenumeration till hanterings gruppen. |
> | Åtgärd | Microsoft.Management/managementGroups/write | Skapa eller uppdatera en hanterings grupp. |
> | Åtgärd | Microsoft. Management/register/åtgärd | Registrera den angivna prenumerationen med Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | DataAction | Microsoft. Maps/Accounts/data/Read | Beviljar data Läs behörighet till ett Maps-konto. |
> | Åtgärd | Microsoft. Maps/Accounts/Delete | Ta bort ett Maps-konto. |
> | Åtgärd | Microsoft.Maps/accounts/eventGridFilters/delete | Ta bort ett Event Grid filter |
> | Åtgärd | Microsoft.Maps/accounts/eventGridFilters/read | Hämta ett Event Grid-filter |
> | Åtgärd | Microsoft.Maps/accounts/eventGridFilters/write | Skapa eller uppdatera ett Event Grid-filter |
> | Åtgärd | Microsoft. Maps/Accounts/Listnycklar/Action | Visa lista över mappnings konto nycklar |
> | Åtgärd | Microsoft. Maps/konton/läsa | Hämta ett Maps-konto. |
> | Åtgärd | Microsoft. Maps/Accounts/regenerateKey/Action | Skapa nytt Maps-konto primär eller sekundär nyckel |
> | Åtgärd | Microsoft.Maps/accounts/write | Skapa eller uppdatera ett Maps-konto. |
> | Åtgärd | Microsoft. Maps/Operations/Read | Läs leverantörs åtgärderna |
> | Åtgärd | Microsoft. Maps/register/åtgärd | Registrera providern |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/avtal/Läs | Returnerar ett avtal. |
> | Åtgärd | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/avtal/Skriv | Accepterar ett signerat avtal. |
> | Åtgärd | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/konfigurationer/importImage/åtgärd | Importerar en avbildning till slutanvändarens ACR. |
> | Åtgärd | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/konfigurationer/läsa | Returnerar en config. |
> | Åtgärd | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/konfigurationer/Skriv åtgärder | Sparar en konfig. |
> | Åtgärd | Microsoft.Marketplace/register/action | Registrerar Microsoft. Marketplace-resurs leverantör i prenumerationen. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/delete | Tar bort en borttagnings åtgärd på en klassisk dev-tjänst resurs. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Hämtar ett klassiskt utvecklings tjänst resurs hanterings nycklar. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Hämtar URL: en för enkel inloggning för en klassisk dev-tjänst. |
> | Åtgärd | Microsoft. MarketplaceApps/ClassicDevServices/Read | En GET-åtgärd i en klassisk dev-tjänst. |
> | Åtgärd | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Genererar ett klassiskt utvecklings tjänst resurs hanterings nycklar. |
> | Åtgärd | Microsoft.MarketplaceApps/Operations/read | Läs åtgärder för alla resurs typer. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. MarketplaceOrdering/Agreements/offers/Plans/Cancel/Action | Avbryta ett avtal för ett angivet Marketplace-objekt |
> | Åtgärd | Microsoft. MarketplaceOrdering/Agreements/offers/Plans/Read | Returnera ett avtal för ett angivet Marketplace-objekt |
> | Åtgärd | Microsoft. MarketplaceOrdering/Agreements/offers/Planes/Sign/Action | Underteckna ett avtal för ett angivet Marketplace-objekt |
> | Åtgärd | Microsoft. MarketplaceOrdering/avtal/läsa | Returnera alla avtal under den aktuella prenumerationen |
> | Åtgärd | Microsoft. MarketplaceOrdering/offertypes/utgivare/erbjudanden/planer/avtal/läsning | Hämta ett avtal för ett angivet virtuellt dator objekt för Marketplace |
> | Åtgärd | Microsoft. MarketplaceOrdering/offertypes/utgivare/erbjudanden/planer/avtal/Skriv | Signera eller annullera ett avtal för ett angivet virtuellt dator objekt i Marketplace |
> | Åtgärd | Microsoft.MarketplaceOrdering/operations/read | Visa en lista över alla möjliga åtgärder i API: et |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Media/checknameavailability/åtgärd | Kontrollerar om ett Media Services konto namn är tillgängligt |
> | Åtgärd | Microsoft. Media/Media Services/accountfilters/Delete | Ta bort eventuella konto filter |
> | Åtgärd | Microsoft. Media/Media Services/accountfilters/Read | Läs valfritt konto filter |
> | Åtgärd | Microsoft. Media/Media Services/accountfilters/Write | Skapa eller uppdatera eventuella konto filter |
> | Åtgärd | Microsoft. Media/Media Services/Asses/assetfilters/Delete | Ta bort eventuella till gångs filter |
> | Åtgärd | Microsoft. Media/Media Services/Asses/assetfilters/Read | Läsa filter för till gångar |
> | Åtgärd | Microsoft. Media/Media Services/Asses/assetfilters/Write | Skapa eller uppdatera ett till gångs filter |
> | Åtgärd | Microsoft. Media/Media Services/Asses/Delete | Ta bort alla till gångar |
> | Åtgärd | Microsoft. Media/Media Services/Asses/getEncryptionKey/Action | Hämta krypterings nyckel för till gång |
> | Åtgärd | Microsoft. Media/Media Services/Asses/listContainerSas/Action | Visa SAS-URL |
> | Åtgärd | Microsoft. Media/Media Services/Asses/listStreamingLocators/Action | Lista över strömmande positionerare för till gång |
> | Åtgärd | Microsoft. Media/Media Services/assets/Read | Läs en till gång |
> | Åtgärd | Microsoft. Media/Media Services/assets/Write | Skapa eller uppdatera en till gång |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Ta bort valfri princip för innehålls nyckel |
> | Åtgärd | Microsoft. Media/Media Services/contentKeyPolicies/getPolicyPropertiesWithSecrets/Action | Hämta princip egenskaper med hemligheter |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/read | Läs eventuell princip för innehålls nyckel |
> | Åtgärd | Microsoft.Media/mediaservices/contentKeyPolicies/write | Skapa eller uppdatera en innehålls nyckel princip |
> | Åtgärd | Microsoft. Media/Media Services/Delete | Ta bort ett Media Services konto |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/delete | Ta bort eventuella Event Grids filter |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/read | Läs eventuella Event Grid filter |
> | Åtgärd | Microsoft.Media/mediaservices/eventGridFilters/write | Skapa eller uppdatera eventuella Event Grids filter |
> | Åtgärd | Microsoft. Media/Media Services/liveEventOperations/Read | Läs valfri Live-händelse åtgärd |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/Delete | Ta bort valfri Live-händelse |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/liveOutputs/Delete | Ta bort direktsänd utdata |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/liveOutputs/Read | Läs alla direktsända utdata |
> | Åtgärd | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Skapa eller uppdatera Live-utdata |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/Read | Läs valfri Live-händelse |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/reset/åtgärd | Återställ eventuell händelse åtgärd i real tid |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/start/Action | Starta valfri händelse åtgärd i real tid |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/stopp/åtgärd | Stoppa alla aktiva händelse åtgärder |
> | Åtgärd | Microsoft. Media/Media Services/liveEvents/Write | Skapa eller uppdatera valfri Live-händelse |
> | Åtgärd | Microsoft.Media/mediaservices/liveOutputOperations/read | Läs all pågående utmatnings åtgärd |
> | Åtgärd | Microsoft. Media/Media Services/Read | Läs valfritt Media Services konto |
> | Åtgärd | Microsoft. Media/Media Services/streamingEndpointOperations/Read | Läs eventuell slut punkts åtgärd för direkt uppspelning |
> | Åtgärd | Microsoft. Media/Media Services/strömnings slut punkter/Delete | Ta bort slut punkt för direkt uppspelning |
> | Åtgärd | Microsoft. Media/Media Services/strömnings slut punkter/Read | Läs eventuell slut punkt för direkt uppspelning |
> | Åtgärd | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skala eventuella slut punkts åtgärder för direkt uppspelning |
> | Åtgärd | Microsoft. Media/Media Services/strömnings slut punkter/start/Action | Starta en slut punkts åtgärd för direkt uppspelning |
> | Åtgärd | Microsoft. Media/Media Services/strömnings slut punkter/stopp/åtgärd | Stoppa eventuella slut punkts åtgärder för direkt uppspelning |
> | Åtgärd | Microsoft. Media/Media Services/strömnings slut punkter/Write | Skapa eller uppdatera slut punkter för direkt uppspelning |
> | Åtgärd | Microsoft. Media/Media Services/streamingLocators/Delete | Ta bort alla strömmande positionerare |
> | Åtgärd | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista med innehålls nycklar |
> | Åtgärd | Microsoft. Media/Media Services/streamingLocators/listPaths/Action | Lista sökvägar |
> | Åtgärd | Microsoft. Media/Media Services/streamingLocators/Read | Läs alla strömmande positionerare |
> | Åtgärd | Microsoft. Media/Media Services/streamingLocators/Write | Skapa eller uppdatera en strömmande positionerare |
> | Åtgärd | Microsoft. Media/Media Services/streamingPolicies/Delete | Ta bort valfri strömmande princip |
> | Åtgärd | Microsoft. Media/Media Services/streamingPolicies/Read | Läs eventuell strömmande princip |
> | Åtgärd | Microsoft. Media/Media Services/streamingPolicies/Write | Skapa eller uppdatera en strömmande princip |
> | Åtgärd | Microsoft.Media/mediaservices/syncStorageKeys/action | Synkronisera lagrings nycklarna för ett kopplat Azure Storage-konto |
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Delete | Ta bort transformering |
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Jobs/cancelJob/åtgärd | Avbryt jobb |
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Jobs/Delete | Ta bort alla jobb |
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Jobs/Read | Läs valfritt jobb |
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Jobs/Write | Skapa eller uppdatera ett jobb |
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Read | Läs eventuell transformering |
> | Åtgärd | Microsoft.Media/mediaservices/transforms/write | Create or Update any Transform |
> | Åtgärd | Microsoft.Media/mediaservices/write | Create or Update any Media Services Account |
> | Åtgärd | Microsoft.Media/operations/read | Get Available Operations |
> | Åtgärd | Microsoft.Media/register/action | Registers the subscription for the Media Services resource provider and enables the creation of Media Services accounts |
> | Åtgärd | Microsoft.Media/unregister/action | Unregisters the subscription for the Media Services resource provider |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/assessments/read | Lists assessments within a project |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/delete | Deletes the assessment project |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/delete | Deletes an assessment |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/read | Gets the properties of an assessment |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/delete | Deletes a group |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/read | Get the properties of a group |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/updateMachines/action | Update group by adding or removing machines |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/groups/write | Creates a new group or updates an existing group |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/hypervcollectors/delete | Deletes the HyperV collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/hypervcollectors/read | Gets the properties of HyperV collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/hypervcollectors/write | Creates a new HyperV collector or updates an existing HyperV collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/importcollectors/delete | Deletes the Import collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/importcollectors/read | Gets the properties of Import collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/importcollectors/write | Creates a new Import collector or updates an existing Import collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/machines/read | Gets the properties of a machine |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/read | Gets the properties of assessment project |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/servercollectors/read | Gets the properties of Server collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/servercollectors/write | Creates a new Server collector or updates an existing Server collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Deletes the VMware collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Gets the properties of VMware collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Creates a new VMware collector or updates an existing VMware collector |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/write | Creates a new assessment project or updates an existing assessment project |
> | Åtgärd | Microsoft.Migrate/locations/assessmentOptions/read | Gets the assessment options which are available in the given location |
> | Åtgärd | Microsoft.Migrate/locations/checknameavailability/action | Checks availability of the resource name for the given subscription in the given location |
> | Åtgärd | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Gets the properties of a database instance |
> | Åtgärd | Microsoft.Migrate/migrateprojects/Databases/read | Gets the properties of a database |
> | Åtgärd | Microsoft.Migrate/migrateprojects/delete | Deletes a migrate project |
> | Åtgärd | Microsoft.Migrate/migrateprojects/machines/read | Gets the properties of a machine |
> | Åtgärd | Microsoft.Migrate/migrateprojects/MigrateEvents/Delete | Deletes a migrate event |
> | Åtgärd | Microsoft.Migrate/migrateprojects/MigrateEvents/read | Gets the properties of a migrate events. |
> | Åtgärd | Microsoft.Migrate/migrateprojects/read | Gets the properties of migrate project |
> | Åtgärd | Microsoft.Migrate/migrateprojects/RefreshSummary/action | Refreshes the migrate project summary |
> | Åtgärd | Microsoft.Migrate/migrateprojects/registerTool/action | Registers tool to a migrate project |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/cleanupData/action | Clean up the migrate project solution data |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/Delete | Deletes a  migrate project solution |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/getconfig/action | Gets the migrate project solution configuration |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/read | Gets the properties of migrate project solution |
> | Åtgärd | Microsoft.Migrate/migrateprojects/solutions/write | Creates a new migrate project solution or updates an existing migrate project solution |
> | Åtgärd | Microsoft.Migrate/migrateprojects/write | Creates a new migrate project or updates an existing migrate project |
> | Åtgärd | Microsoft.Migrate/Operations/read | Lists operations available on Microsoft.Migrate resource provider |
> | Åtgärd | Microsoft.Migrate/projects/assessments/read | Lists assessments within a project |
> | Åtgärd | Microsoft.Migrate/projects/delete | Deletes the project |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/assessedmachines/read | Get the properties of an assessed machine |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/delete | Deletes an assessment |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/downloadurl/action | Downloads an assessment report's URL |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/read | Gets the properties of an assessment |
> | Åtgärd | Microsoft.Migrate/projects/groups/assessments/write | Creates a new assessment or updates an existing assessment |
> | Åtgärd | Microsoft.Migrate/projects/groups/delete | Deletes a group |
> | Åtgärd | Microsoft.Migrate/projects/groups/read | Get the properties of a group |
> | Åtgärd | Microsoft.Migrate/projects/groups/write | Creates a new group or updates an existing group |
> | Åtgärd | Microsoft.Migrate/projects/keys/action | Gets shared keys for the project |
> | Åtgärd | Microsoft.Migrate/projects/machines/read | Gets the properties of a machine |
> | Åtgärd | Microsoft.Migrate/projects/read | Gets the properties of a project |
> | Åtgärd | Microsoft.Migrate/projects/write | Creates a new project or updates an existing project |
> | Åtgärd | Microsoft.Migrate/register/action | Registers Subscription with Microsoft.Migrate resource provider |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.MixedReality/register/action | Registers a subscription for the Mixed Reality resource provider. |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action | Create spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/delete | Delete spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read | Discover nearby spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read | Get properties of spatial anchors |
> | Åtgärd | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Åtgärd | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for Microsoft.MixedReality/spatialAnchorsAccounts |
> | Åtgärd | Microsoft.MixedReality/spatialAnchorsAccounts/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Microsoft.MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read | Locate spatial anchors |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Submit diagnostics data to help improve the quality of the Azure Spatial Anchors service |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/write | Update spatial anchors properties |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.NetApp/locations/checkfilepathavailability/action | Check if file path is available |
> | Åtgärd | Microsoft.NetApp/locations/checknameavailability/action | Check if resource name is available |
> | Åtgärd | Microsoft.NetApp/locations/operationresults/read | Reads an operation result resource. |
> | Åtgärd | Microsoft.NetApp/locations/read | Reads an availability check resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/backupPolicies/delete | Deletes a backup policy resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/backupPolicies/read | Reads a backup policy resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/backupPolicies/write | Writes a backup policy resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Deletes a pool resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/read | Reads a pool resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/delete | Deletes a backup resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/read | Reads a backup resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/backups/write | Writes a backup resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/break/action | Break volume replication relations |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Deletes a volume resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Reads a mount target resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Reads a volume resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/ReplicationStatus/action | Reads the statuses of the Volume Replication. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/revert/action | Revert volume to specific snapshot |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Deletes a snapshot resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/read | Reads a snapshot resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Writes a snapshot resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Writes a volume resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/write | Writes a pool resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/delete | Deletes an account resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/read | Reads an account resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/vaults/read | Reads a vault resource. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/write | Writes an account resource. |
> | Åtgärd | Microsoft.NetApp/Operations/read | Reads an operation resources. |
> | Åtgärd | Microsoft.NetApp/register/action | Registers Subscription with Microsoft.NetApp resource provider |
> | Åtgärd | Microsoft.NetApp/unregister/action | Unregisters Subscription with Microsoft.NetApp resource provider |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Get Application Gateway available Request Headers |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Get Application Gateway available Response Header |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Get Application Gateway available Server Variables |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway Ssl Predefined Policy |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway available Ssl Options |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableWafRuleSets/read | Gets Application Gateway Available Waf Rule Sets |
> | Åtgärd | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Joins an application gateway backend address pool. Not Alertable. |
> | Åtgärd | Microsoft.Network/applicationGateways/backendhealth/action | Gets an application gateway backend health |
> | Åtgärd | Microsoft.Network/applicationGateways/delete | Deletes an application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/getBackendHealthOnDemand/action | Gets an application gateway backend health on demand for given http setting and backend pool |
> | Åtgärd | Microsoft.Network/applicationGateways/read | Gets an application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/start/action | Starts an application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/stop/action | Stops an application gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/write | Creates an application gateway or updates an application gateway |
> | Åtgärd | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Tar bort en Application Gateway WAF-princip |
> | Åtgärd | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Read | Hämtar en Application Gateway WAF-princip |
> | Åtgärd | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Skapar en Application Gateway WAF-princip eller uppdaterar en Application Gateway WAF-princip |
> | Åtgärd | Microsoft. Network/applicationSecurityGroups/Delete | Tar bort en program säkerhets grupp |
> | Åtgärd | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action | Ansluter en IP-konfiguration till program säkerhets grupper. Not alertable. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Kopplar en säkerhets regel till program säkerhets grupper. Not alertable. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Listar IP-konfigurationer i ApplicationSecurityGroup |
> | Åtgärd | Microsoft. Network/applicationSecurityGroups/Read | Hämtar ett ID för program säkerhets gruppen. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/write | Skapar en program säkerhets grupp eller uppdaterar en befintlig program säkerhets grupp. |
> | Åtgärd | Microsoft.Network/azureFirewallFqdnTags/read | Hämtar Azure Firewall FQDN-Taggar |
> | Åtgärd | Microsoft. Network/azurefirewalls/Delete | Ta bort Azure-brandvägg |
> | Åtgärd | Microsoft.Network/azurefirewalls/read | Hämta Azure-brandvägg |
> | Åtgärd | Microsoft.Network/azurefirewalls/write | Skapar eller uppdaterar en Azure-brandvägg |
> | Åtgärd | Microsoft. Network/bastionHosts/createbsl/Action | Skapar URL: er för delnings bara för de virtuella datorerna under en skydds och returnerar URL: erna |
> | Åtgärd | Microsoft. Network/bastionHosts/Delete | Tar bort en skydds-värd |
> | Åtgärd | Microsoft. Network/bastionHosts/deletebsl/Action | Tar bort delnings bara webb adresser för de tillhandahållna virtuella datorerna under en skydds |
> | Åtgärd | Microsoft. Network/bastionHosts/disconnectactivesessions/Action | Koppla bort aktuella aktiva sessioner i skydds-värden |
> | Åtgärd | Microsoft. Network/bastionHosts/getactivesessions/Action | Hämta aktiva sessioner i skydds-värden |
> | Åtgärd | Microsoft. Network/bastionHosts/getbsl/Action | Returnerar de delnings bara URL: erna för de angivna virtuella datorerna i ett skydds-undernät förutsatt att deras URL skapas |
> | Åtgärd | Microsoft. Network/bastionHosts/Read | Hämtar en skydds-värd |
> | Åtgärd | Microsoft. Network/bastionHosts/Write | Skapa eller uppdatera en skydds-värd |
> | Åtgärd | Microsoft.Network/bgpServiceCommunities/read | Hämta BGP-tjänstecommunities |
> | Åtgärd | Microsoft.Network/checkFrontDoorNameAvailability/action | Kontrollerar om ett namn på en front dörr är tillgängligt |
> | Åtgärd | Microsoft.Network/checkTrafficManagerNameAvailability/action | Kontrollerar tillgängligheten för ett Traffic Manager relativt DNS-namn. |
> | Åtgärd | Microsoft. Network/Connections/Delete | Tar bort VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft. Network/Connections/Read | Hämtar VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft. Network/Connections/REVOKE/åtgärd | Markerar en Express väg anslutnings status som återkallad |
> | Åtgärd | Microsoft.Network/connections/sharedkey/action | Hämta VirtualNetworkGatewayConnection-SharedKey |
> | Åtgärd | Microsoft. Network/Connections/sharedKey/Read | Hämtar VirtualNetworkGatewayConnection-SharedKey |
> | Åtgärd | Microsoft.Network/connections/sharedKey/write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection-SharedKey |
> | Åtgärd | Microsoft. Network/Connections/startpacketcapture/Action | Startar en avbildning av anslutnings paket för Virtual Network Gateway. |
> | Åtgärd | Microsoft. Network/Connections/stoppacketcapture/Action | Stoppar en avbildning av anslutnings paket för Virtual Network Gateway. |
> | Åtgärd | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Hämtar VPN-enhetens konfiguration av VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft. Network/Connections/Write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/delete | Tar bort en DDoS anpassad princip |
> | Åtgärd | Microsoft. Network/ddosCustomPolicies/Read | Hämtar en DDoS anpassad princip definitions definition |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/write | Skapar en anpassad princip för DDoS eller uppdaterar en befintlig DDoS anpassad princip |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/delete | Tar bort en DDoS Protection Plan |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/join/action | Kopplar en DDoS Protection-plan. Not alertable. |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/read | Hämtar ett DDoS Protection Plan |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/write | Skapar en DDoS Protection plan eller uppdaterar en DDoS Protection Plan  |
> | Åtgärd | Microsoft. Network/dnsoperationresults/Read | Hämtar resultat från en DNS-åtgärd |
> | Åtgärd | Microsoft. Network/dnsoperationstatuses/Read | Hämtar status för en DNS-åtgärd  |
> | Åtgärd | Microsoft. Network/dnszones/A/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "A" från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/A/Read | Hämta post uppsättningen av typen "A" i JSON-format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/dnszones/A/write | Skapa eller uppdatera en post uppsättning av typen "A" i en DNS-zon. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv AAAA från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/AAAA/Read | Hämta post uppsättningen av typen AAAA i JSON-format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/write | Skapa eller uppdatera en post uppsättning av typen AAAA i en DNS-zon. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft. Network/dnszones/all/Read | Gets DNS record sets across types |
> | Åtgärd | Microsoft.Network/dnszones/CAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv CAA från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/CAA/Read | Hämta post uppsättningen av typen ' CAA ' i JSON-format. Post uppsättningen innehåller TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CAA/write | Skapa eller uppdatera en post uppsättning av typen ' CAA ' i en DNS-zon. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/delete | Ta bort post uppsättningen med ett angivet namn och skriv CNAME från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/CNAME/Read | Hämta post uppsättningen av typen CNAME i JSON-format. Post uppsättningen innehåller TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/write | Skapa eller uppdatera en post uppsättning av typen CNAME i en DNS-zon. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft. Network/dnszones/Delete | Ta bort DNS-zonen i JSON-format. The zone properties include tags, etag, numberOfRecordSets, and maxNumberOfRecordSets. |
> | Åtgärd | Microsoft. Network/dnszones/MX/Delete | Remove the record set of a given name and type ‘MX’ from a DNS zone. |
> | Åtgärd | Microsoft.Network/dnszones/MX/read | Get the record set of type ‘MX’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/dnszones/MX/write | Create or update a record set of type ‘MX’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/dnszones/NS/delete | Deletes the DNS record set of type NS |
> | Åtgärd | Microsoft.Network/dnszones/NS/read | Gets DNS record set of type NS |
> | Åtgärd | Microsoft.Network/dnszones/NS/write | Creates or updates DNS record set of type NS |
> | Åtgärd | Microsoft.Network/dnszones/PTR/delete | Remove the record set of a given name and type ‘PTR’ from a DNS zone. |
> | Åtgärd | Microsoft.Network/dnszones/PTR/read | Get the record set of type ‘PTR’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/dnszones/PTR/write | Create or update a record set of type ‘PTR’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/dnszones/read | Get the DNS zone, in JSON format. The zone properties include tags, etag, numberOfRecordSets, and maxNumberOfRecordSets. Note that this command does not retrieve the record sets contained within the zone. |
> | Åtgärd | Microsoft.Network/dnszones/recordsets/read | Gets DNS record sets across types |
> | Åtgärd | Microsoft.Network/dnszones/SOA/read | Gets DNS record set of type SOA |
> | Åtgärd | Microsoft.Network/dnszones/SOA/write | Creates or updates DNS record set of type SOA |
> | Åtgärd | Microsoft.Network/dnszones/SRV/delete | Remove the record set of a given name and type ‘SRV’ from a DNS zone. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/read | Get the record set of type ‘SRV’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/write | Create or update record set of type SRV |
> | Åtgärd | Microsoft.Network/dnszones/TXT/delete | Remove the record set of a given name and type ‘TXT’ from a DNS zone. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/read | Get the record set of type ‘TXT’, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/write | Create or update a record set of type ‘TXT’ within a DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/dnszones/write | Create or update a DNS zone within a resource group.  Used to update the tags on a DNS zone resource. Note that this command can not be used to create or update record sets within the zone. |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/delete | Deletes an ExpressRouteCircuit Authorization |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/read | Gets an ExpressRouteCircuit Authorization |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/write | Creates or updates an existing ExpressRouteCircuit Authorization |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/delete | Deletes an ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/join/action | Joins an Express Route Circuit. Not alertable. |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/arpTables/read | Gets an ExpressRouteCircuit Peering ArpTable |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/delete | Deletes an ExpressRouteCircuit Connection |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Gets an ExpressRouteCircuit Connection |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Creates or updates an existing ExpressRouteCircuit Connection Resource |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/delete | Deletes an ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/peerConnections/read | Gets Peer Express Route Circuit Connection |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/read | Gets an ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/routeTables/read | Gets an ExpressRouteCircuit Peering RouteTable |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/read | Gets an ExpressRouteCircuit Peering RouteTable Summary |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Gets an ExpressRouteCircuit Peering Stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/write | Creates or updates an existing ExpressRouteCircuit Peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/read | Get an ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/stats/read | Gets an ExpressRouteCircuit Stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/write | Creates or updates an existing ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/join/action | Joins an Express Route Cross Connection. Not alertable. |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/arpTables/read | Gets an Express Route Cross Connection Peering Arp Table |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/delete | Deletes an Express Route Cross Connection Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/read | Gets an Express Route Cross Connection Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/routeTables/read | Gets an Express Route Cross Connection Peering Route Table |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/read | Gets an Express Route Cross Connection Peering Route Table Summary |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/write | Creates an Express Route Cross Connection Peering or Updates an existing Express Route Cross Connection Peering |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/read | Get Express Route Cross Connection |
> | Åtgärd | Microsoft.Network/expressRouteGateways/expressRouteConnections/delete | Deletes an Express Route Connection |
> | Åtgärd | Microsoft.Network/expressRouteGateways/expressRouteConnections/read | Gets an Express Route Connection |
> | Åtgärd | Microsoft.Network/expressRouteGateways/expressRouteConnections/write | Skapar en Express Route-anslutning eller uppdaterar en befintlig Express Route-anslutning |
> | Åtgärd | Microsoft. Network/expressRouteGateways/JOIN/åtgärd | Ansluter till en Express Route-Gateway. Not alertable. |
> | Åtgärd | Microsoft. Network/expressRouteGateways/Read | Hämta en Express Route-Gateway |
> | Åtgärd | Microsoft.Network/expressRoutePorts/delete | Tar bort ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePorts/join/action | Ansluter till Express Route-portar. Not alertable. |
> | Åtgärd | Microsoft.Network/expressRoutePorts/links/read | Hämtar ExpressRouteLink |
> | Åtgärd | Microsoft.Network/expressRoutePorts/read | Hämtar ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePorts/write | Skapar eller uppdaterar ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePortsLocations/read | Hämta platser för Express Route-portar |
> | Åtgärd | Microsoft.Network/expressRouteServiceProviders/read | Hämtar Express Route Service-leverantörer |
> | Åtgärd | Microsoft. Network/firewallPolicies/Delete | Tar bort en brand Väggs princip |
> | Åtgärd | Microsoft. Network/firewallPolicies/JOIN/åtgärd | Ansluter till en brand Väggs princip. Not alertable. |
> | Åtgärd | Microsoft. Network/firewallPolicies/Read | Hämtar en brand Väggs princip |
> | Åtgärd | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Tar bort en regel grupp för brand Väggs principer |
> | Åtgärd | Microsoft. Network/firewallPolicies/ruleGroups/Read | Hämtar en regel grupp för brand Väggs principer |
> | Åtgärd | Microsoft. Network/firewallPolicies/ruleGroups/Write | Skapar en regel grupp för brand Väggs policy eller uppdaterar en befintlig regel grupp för brand Väggs policy |
> | Åtgärd | Microsoft. Network/firewallPolicies/Write | Skapar en brand Väggs princip eller uppdaterar en befintlig brand Väggs princip |
> | Åtgärd | Microsoft. Network/frontDoors/backendPools/Delete | Tar bort en backend-pool |
> | Åtgärd | Microsoft. Network/frontDoors/backendPools/Read | Hämtar en backend-pool |
> | Åtgärd | Microsoft. Network/frontDoors/backendPools/Write | Skapar eller uppdaterar en backend-pool |
> | Åtgärd | Microsoft. Network/frontDoors/Delete | Tar bort en front dörr |
> | Åtgärd | Microsoft. Network/frontDoors/frontendEndpoints/Delete | Tar bort en frontend-slutpunkt |
> | Åtgärd | Microsoft. Network/frontDoors/frontendEndpoints/disableHttps/Action | Inaktiverar HTTPS på en frontend-slutpunkt |
> | Åtgärd | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Aktiverar HTTPS på en frontend-slutpunkt |
> | Åtgärd | Microsoft. Network/frontDoors/frontendEndpoints/Read | Hämtar en frontend-slutpunkt |
> | Åtgärd | Microsoft. Network/frontDoors/frontendEndpoints/Write | Skapar eller uppdaterar en klient delens slut punkt |
> | Åtgärd | Microsoft. Network/frontDoors/healthProbeSettings/Delete | Tar bort inställningar för hälso avsökning |
> | Åtgärd | Microsoft.Network/frontDoors/healthProbeSettings/read | Hämtar inställningar för hälso avsökning |
> | Åtgärd | Microsoft.Network/frontDoors/healthProbeSettings/write | Skapar eller uppdaterar inställningar för hälso avsökning |
> | Åtgärd | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Skapar eller uppdaterar inställningar för belastnings utjämning |
> | Åtgärd | Microsoft.Network/frontDoors/loadBalancingSettings/read | Hämtar inställningar för belastnings utjämning |
> | Åtgärd | Microsoft.Network/frontDoors/loadBalancingSettings/write | Skapar eller uppdaterar inställningar för belastnings utjämning |
> | Åtgärd | Microsoft. Network/frontDoors/rensning/åtgärd | Rensa cachelagrat innehåll från en front dörr |
> | Åtgärd | Microsoft. Network/frontDoors/Read | Hämtar en front dörr |
> | Åtgärd | Microsoft.Network/frontDoors/routingRules/delete | Tar bort en regel för routning |
> | Åtgärd | Microsoft.Network/frontDoors/routingRules/read | Hämtar en regel för routning |
> | Åtgärd | Microsoft.Network/frontDoors/routingRules/write | Skapar eller uppdaterar en regel för routning |
> | Åtgärd | Microsoft.Network/frontDoors/validateCustomDomain/action | Verifierar en frontend-slutpunkt för en frontend-dörr |
> | Åtgärd | Microsoft.Network/frontDoors/write | Skapar eller uppdaterar en frontend-dörr |
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/Read | Hämtar hanterade regel uppsättningar för webb programs brand vägg |
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Delete | Tar bort en brand Väggs princip för webb program |
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/JOIN/åtgärd | Ansluter en brand Väggs princip för webb program. Not Alertable. |
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Read | Hämtar en brand Väggs princip för webb program |
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Skapar eller uppdaterar en brand Väggs princip för webb program |
> | Åtgärd | Microsoft. Network/ipGroups/Delete | Tar bort en IpGroup |
> | Åtgärd | Microsoft. Network/ipGroups/JOIN/åtgärd | Ansluter till en IpGroup. Not alertable. |
> | Åtgärd | Microsoft. Network/ipGroups/Read | Hämtar en IpGroup |
> | Åtgärd | Microsoft. Network/ipGroups/Write | Skapar en IpGroup eller uppdaterar en befintlig IpGroups |
> | Åtgärd | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en backend-adresspool för belastnings utjämning. Not Alertable. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/backendAddressPools/Read | Hämtar en definition för Server dels adress för belastnings utjämning |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/Delete | Tar bort en belastningsutjämnare |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/frontendIPConfigurations/JOIN/åtgärd | Ansluter till en IP-konfiguration för Load Balancer-frontend. Not alertable. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/frontendIPConfigurations/Read | Hämtar en IP-konfigurations definition för belastningsutjämnarens klient del |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter en inkommande NAT-pool för belastningsutjämnaren. Not alertable. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/inboundNatPools/Read | Hämtar en belastnings Utjämnings definition för inkommande NAT-pool |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/delete | Tar bort en inkommande NAT-regel för belastnings utjämning |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Not Alertable. |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/read | Hämtar en inkommande NAT-regel definition för belastnings utjämning |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/write | Skapar en inkommande NAT-regel för belastnings utjämning eller uppdaterar en befintlig inkommande NAT-regel för belastnings utjämning |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/loadBalancingRules/Read | Hämtar en regel definition för belastnings utjämning för belastnings utjämning |
> | Åtgärd | Microsoft.Network/loadBalancers/networkInterfaces/read | Hämtar referenser till alla nätverks gränssnitt under en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/outboundRules/read | Hämtar en regel definition för utgående belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/probes/join/action | Tillåter att avsökningar av en belastningsutjämnare används. Till exempel kan den här behörighetens egenskap healthProbe-egenskap för VM Scale set referera till avsökningen. Not alertable. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/PROBES/Read | Hämtar en belastnings Utjämnings avsökning |
> | Åtgärd | Microsoft.Network/loadBalancers/read | Hämtar en belastnings Utjämnings definition |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/virtualMachines/Read | Hämtar referenser till alla virtuella datorer under en belastningsutjämnare |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/Write | Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare |
> | Åtgärd | Microsoft. Network/localnetworkgateways/Delete | Tar bort LocalNetworkGateway |
> | Åtgärd | Microsoft. Network/localnetworkgateways/Read | Hämtar LocalNetworkGateway |
> | Åtgärd | Microsoft. Network/localnetworkgateways/Write | Skapar eller uppdaterar en befintlig LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Hämtar automatiskt godkända privata länk tjänster |
> | Åtgärd | Microsoft. Network/locations/availableDelegations/Read | Hämtar tillgängliga delegeringar |
> | Åtgärd | Microsoft.Network/locations/availablePrivateEndpointTypes/read | Gets available Private Endpoint resources |
> | Åtgärd | Microsoft.Network/locations/availableServiceAliases/read | Gets Available Service Aliases |
> | Åtgärd | Microsoft.Network/locations/bareMetalTenants/action | Allocates or validates a Bare Metal Tenant |
> | Åtgärd | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Checks Accelerated Networking support |
> | Åtgärd | Microsoft.Network/locations/checkDnsNameAvailability/read | Checks if dns label is available at the specified location |
> | Åtgärd | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Checks Private Link Service Visibility |
> | Åtgärd | Microsoft.Network/locations/operationResults/read | Gets operation result of an async POST or DELETE operation |
> | Åtgärd | Microsoft.Network/locations/operations/read | Gets operation resource that represents status of an asynchronous operation |
> | Åtgärd | Microsoft.Network/locations/serviceTags/read | Get Service Tags |
> | Åtgärd | Microsoft.Network/locations/supportedVirtualMachineSizes/read | Gets supported virtual machines sizes |
> | Åtgärd | Microsoft.Network/locations/usages/read | Gets the resources usage metrics |
> | Åtgärd | Microsoft.Network/locations/virtualNetworkAvailableEndpointServices/read | Gets a list of available Virtual Network Endpoint Services |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/delete | Deletes an Network Intent Policy |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/read | Gets an Network Intent Policy Description |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/write | Creates an Network Intent Policy or updates an existing Network Intent Policy |
> | Åtgärd | Microsoft.Network/networkInterfaces/delete | Deletes a network interface |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Get Network Security Groups configured On Network Interface Of The Vm |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Get Route Table configured On Network Interface Of The Vm |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Joins a Network Interface IP Configuration. Not alertable. |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/read | Gets a network interface ip configuration definition.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/join/action | Joins a Virtual Machine to a network interface. Not Alertable. |
> | Åtgärd | Microsoft.Network/networkInterfaces/loadBalancers/read | Gets all the load balancers that the network interface is part of |
> | Åtgärd | Microsoft.Network/networkInterfaces/read | Gets a network interface definition.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/delete | Deletes a Network Interface Tap Configuration. |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/read | Gets a Network Interface Tap Configuration. |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/write | Creates a Network Interface Tap Configuration or updates an existing Network Interface Tap Configuration. |
> | Åtgärd | Microsoft.Network/networkInterfaces/write | Creates a network interface or updates an existing network interface.  |
> | Åtgärd | Microsoft.Network/networkProfiles/delete | Deletes a Network Profile |
> | Åtgärd | Microsoft.Network/networkProfiles/read | Gets a Network Profile |
> | Åtgärd | Microsoft.Network/networkProfiles/removeContainers/action | Removes Containers |
> | Åtgärd | Microsoft.Network/networkProfiles/setContainers/action | Sets Containers |
> | Åtgärd | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Sets Container Network Interfaces |
> | Åtgärd | Microsoft.Network/networkProfiles/write | Creates or updates a Network Profile |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/defaultSecurityRules/read | Gets a default security rule definition |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/delete | Deletes a network security group |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/join/action | Joins a network security group. Not Alertable. |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/read | Gets a network security group definition |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/delete | Deletes a security rule |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/read | Gets a security rule definition |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/write | Creates a security rule or updates an existing security rule |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/write | Creates a network security group or updates an existing network security group |
> | Åtgärd | Microsoft.Network/networkWatchers/availableProvidersList/action | Returns all available internet service providers for a specified Azure region. |
> | Åtgärd | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returns the relative latency score for internet service providers from a specified location to Azure regions. |
> | Åtgärd | Microsoft.Network/networkWatchers/configureFlowLog/action | Configures flow logging for a target resource. |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/delete | Deletes a Connection Monitor |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Query monitoring connectivity between specified endpoints |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/read | Get Connection Monitor details |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/start/action | Start monitoring connectivity between specified endpoints |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/stop/action | Stop/pause monitoring connectivity between specified endpoints |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/write | Creates a Connection Monitor |
> | Åtgärd | Microsoft.Network/networkWatchers/connectivityCheck/action | Verifies the possibility of establishing a direct TCP connection from a virtual machine to a given endpoint including another VM or an arbitrary remote server. |
> | Åtgärd | Microsoft.Network/networkWatchers/delete | Deletes a network watcher |
> | Åtgärd | Microsoft.Network/networkWatchers/flowLogs/delete | Deletes a Flow Log |
> | Åtgärd | Microsoft.Network/networkWatchers/flowLogs/read | Get Flow Log details |
> | Åtgärd | Microsoft.Network/networkWatchers/flowLogs/write | Creates a Flow Log |
> | Åtgärd | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returns whether the packet is allowed or denied to or from a particular destination. |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/delete | Deletes a Lens |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/query/action | Query monitoring network traffic on a specified endpoint |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/read | Get Lens details |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/start/action | Start monitoring network traffic on a specified endpoint |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/stop/action | Stop/pause monitoring network traffic on a specified endpoint |
> | Åtgärd | Microsoft.Network/networkWatchers/lenses/write | Creates a Lens |
> | Åtgärd | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostic of network configuration. |
> | Åtgärd | Microsoft.Network/networkWatchers/nextHop/action | For a specified target and destination IP address, return the next hop type and next hope IP address. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/delete | Deletes a packet capture |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Gets information about properties and status of a packet capture resource. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/read | Get the packet capture definition |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/stop/action | Stop the running packet capture session. |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/write | Creates a packet capture |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/delete | Deletes a PingMesh |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/read | Get PingMesh details |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/start/action | Start PingMesh between specified VMs |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/stop/action | Stop PingMesh between specified VMs |
> | Åtgärd | Microsoft.Network/networkWatchers/pingMeshes/write | Creates a PingMesh |
> | Åtgärd | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Batch query monitoring connectivity between specified endpoints |
> | Åtgärd | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Gets the status of flow logging on a resource. |
> | Åtgärd | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Gets the troubleshooting result from the previously run or currently running troubleshooting operation. |
> | Åtgärd | Microsoft.Network/networkWatchers/read | Get the network watcher definition |
> | Åtgärd | Microsoft.Network/networkWatchers/securityGroupView/action | View the configured and effective network security group rules applied on a VM. |
> | Åtgärd | Microsoft.Network/networkWatchers/topology/action | Gets a network level view of resources and their relationships in a resource group. |
> | Åtgärd | Microsoft.Network/networkWatchers/troubleshoot/action | Starts troubleshooting on a Networking resource in Azure. |
> | Åtgärd | Microsoft.Network/networkWatchers/write | Creates a network watcher or updates an existing network watcher |
> | Åtgärd | Microsoft.Network/operations/read | Get Available Operations |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/delete | Deletes a P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/disconnectp2svpnconnections/action | Disconnect p2s vpn connections |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generate Vpn Profile for P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Gets a P2S Vpn Connection health for P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/action | Gets a P2S Vpn Connection health detailed for P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/read | Gets a P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/write | Puts a P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/privateDnsOperationResults/read | Gets results of a Private DNS operation |
> | Åtgärd | Microsoft.Network/privateDnsOperationStatuses/read | Gets status of a Private DNS operation |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/delete | Remove the record set of a given name and type ‘A’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/read | Get the record set of type ‘A’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/write | Create or update a record set of type ‘A’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/delete | Remove the record set of a given name and type ‘AAAA’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/read | Get the record set of type ‘AAAA’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/write | Create or update a record set of type ‘AAAA’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/privateDnsZones/ALL/read | Gets Private DNS record sets across types |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/delete | Remove the record set of a given name and type ‘CNAME’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/read | Get the record set of type ‘CNAME’ within a Private DNS zone, in JSON format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/write | Create or update a record set of type ‘CNAME’ within a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/delete | Delete a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/delete | Remove the record set of a given name and type ‘MX’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/read | Get the record set of type ‘MX’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/write | Create or update a record set of type ‘MX’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/privateDnsZones/PTR/delete | Remove the record set of a given name and type ‘PTR’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/PTR/read | Get the record set of type ‘PTR’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/PTR/write | Create or update a record set of type ‘PTR’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/privateDnsZones/read | Get the Private DNS zone properties, in JSON format. Note that this command does not retrieve the virtual networks to which the Private DNS zone is linked or the record sets contained within the zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/recordsets/read | Gets Private DNS record sets across types |
> | Åtgärd | Microsoft.Network/privateDnsZones/SOA/read | Get the record set of type ‘SOA’ within a Private DNS zone, in JSON format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SOA/write | Update a record set of type ‘SOA’ within a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/delete | Remove the record set of a given name and type ‘SRV’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/read | Get the record set of type ‘SRV’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/write | Create or update a record set of type ‘SRV’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/delete | Remove the record set of a given name and type ‘TXT’ from a Private DNS zone. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/read | Get the record set of type ‘TXT’ within a Private DNS zone, in JSON format. The record set contains a list of records as well as the TTL, tags, and etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/write | Create or update a record set of type ‘TXT’ within a Private DNS zone. The records specified will replace the current records in the record set. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Delete a Private DNS zone link to virtual network. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Get the Private DNS zone link to virtual network properties, in JSON format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Create or update a Private DNS zone link to virtual network. |
> | Åtgärd | Microsoft.Network/privateDnsZones/write | Create or update a Private DNS zone within a resource group. Note that this command cannot be used to create or update virtual network links or record sets within the zone. |
> | Åtgärd | Microsoft.Network/privateEndpointRedirectMaps/read | Gets a Private Endpoint RedirectMap |
> | Åtgärd | Microsoft.Network/privateEndpointRedirectMaps/write | Creates Private Endpoint RedirectMap Or Updates An Existing Private Endpoint RedirectMap |
> | Åtgärd | Microsoft.Network/privateEndpoints/delete | Deletes an private endpoint resource. |
> | Åtgärd | Microsoft.Network/privateEndpoints/read | Gets an private endpoint resource. |
> | Åtgärd | Microsoft.Network/privateEndpoints/write | Creates a new private endpoint, or updates an existing private endpoint. |
> | Åtgärd | Microsoft.Network/privateLinkServices/delete | Deletes an private link service resource. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Deletes an private endpoint connection. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Gets an private endpoint connection definition. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Creates a new private endpoint connection, or updates an existing private endpoint connection. |
> | Åtgärd | Microsoft.Network/privateLinkServices/read | Gets an private link service resource. |
> | Åtgärd | Microsoft.Network/privateLinkServices/write | Creates a new private link service, or updates an existing private link service. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/delete | Deletes a public Ip address. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/join/action | Joins a public ip address. Not Alertable. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/read | Gets a public ip address definition. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/write | Creates a public Ip address or updates an existing public Ip address.  |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/delete | Deletes A Public Ip Prefix |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/join/action | Joins a PublicIPPrefix. Not alertable. |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/read | Gets a Public Ip Prefix Definition |
> | Åtgärd | Microsoft.Network/publicIPPrefixes/write | Creates A Public Ip Prefix Or Updates An Existing Public Ip Prefix |
> | Åtgärd | Microsoft.Network/register/action | Registers the subscription |
> | Åtgärd | Microsoft.Network/routeFilters/delete | Deletes a route filter definition |
> | Åtgärd | Microsoft.Network/routeFilters/join/action | Joins a route filter. Not Alertable. |
> | Åtgärd | Microsoft.Network/routeFilters/read | Gets a route filter definition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/delete | Deletes a route filter rule definition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/read | Gets a route filter rule definition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/write | Creates a route filter rule or Updates an existing route filter rule |
> | Åtgärd | Microsoft.Network/routeFilters/write | Creates a route filter or Updates an existing route filter |
> | Åtgärd | Microsoft.Network/routeTables/delete | Deletes a route table definition |
> | Åtgärd | Microsoft.Network/routeTables/join/action | Joins a route table. Not Alertable. |
> | Åtgärd | Microsoft.Network/routeTables/read | Gets a route table definition |
> | Åtgärd | Microsoft.Network/routeTables/routes/delete | Deletes a route definition |
> | Åtgärd | Microsoft.Network/routeTables/routes/read | Gets a route definition |
> | Åtgärd | Microsoft.Network/routeTables/routes/write | Creates a route or Updates an existing route |
> | Åtgärd | Microsoft.Network/routeTables/write | Creates a route table or Updates an existing route table |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/delete | Deletes a Service Endpoint Policy |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/join/action | Joins a Service Endpoint Policy. Not alertable. |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Joins a Subnet To Service Endpoint Policies. Not alertable. |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/read | Gets a Service Endpoint Policy Description |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Deletes a Service Endpoint Policy Definition |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Gets a Service Endpoint Policy Definition Description |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Creates a Service Endpoint Policy Definition or updates an existing Service Endpoint Policy Definition |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/write | Creates a Service Endpoint Policy or updates an existing Service Endpoint Policy |
> | Åtgärd | Microsoft.Network/trafficManagerGeographicHierarchies/read | Gets the Traffic Manager Geographic Hierarchy containing regions which can be used with the Geographic traffic routing method |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Deletes an Azure Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted Azure Endpoint. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Gets an Azure Endpoint which belongs to a Traffic Manager Profile, including all the properties of that Azure Endpoint. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Add a new Azure Endpoint in an existing Traffic Manager Profile or update the properties of an existing Azure Endpoint in that Traffic Manager Profile. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/delete | Delete the Traffic Manager profile. All settings associated with the Traffic Manager profile will be lost, and the profile can no longer be used to route traffic. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Deletes an External Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted External Endpoint. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Gets an External Endpoint which belongs to a Traffic Manager Profile, including all the properties of that External Endpoint. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Add a new External Endpoint in an existing Traffic Manager Profile or update the properties of an existing External Endpoint in that Traffic Manager Profile. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Gets the Traffic Manager Heat Map for the given Traffic Manager profile which contains query counts and latency data by location and source IP. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Deletes an Nested Endpoint from an existing Traffic Manager Profile. Traffic Manager will stop routing traffic to the deleted Nested Endpoint. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Gets an Nested Endpoint which belongs to a Traffic Manager Profile, including all the properties of that Nested Endpoint. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Add a new Nested Endpoint in an existing Traffic Manager Profile or update the properties of an existing Nested Endpoint in that Traffic Manager Profile. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/read | Get the Traffic Manager profile configuration. This includes DNS settings, traffic routing settings, endpoint monitoring settings, and the list of endpoints routed by this Traffic Manager profile. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/write | Create a Traffic Manager profile, or modify the configuration of an existing Traffic Manager profile.<br>This includes enabling or disabling a profile and modifying DNS settings, traffic routing settings, or endpoint monitoring settings.<br>Endpoints routed by the Traffic Manager profile can be added, removed, enabled or disabled. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Deletes the subscription-level key used for Realtime User Metrics collection. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/read | Gets the subscription-level key used for Realtime User Metrics collection. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/write | Creates a new subscription-level key to be used for Realtime User Metrics collection. |
> | Åtgärd | Microsoft.Network/unregister/action | Unregisters the subscription |
> | Åtgärd | Microsoft.Network/virtualHubs/delete | Deletes a Virtual Hub |
> | Åtgärd | Microsoft.Network/virtualHubs/effectiveRoutes/action | Gets effective route configured on Virtual Hub |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Deletes a HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Get a HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Create or update a HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/read | Get a Virtual Hub |
> | Åtgärd | Microsoft.Network/virtualHubs/routeTables/delete | Delete a VirtualHubRouteTableV2 |
> | Åtgärd | Microsoft.Network/virtualHubs/routeTables/read | Get a VirtualHubRouteTableV2 |
> | Åtgärd | Microsoft.Network/virtualHubs/routeTables/write | Create or Update a VirtualHubRouteTableV2 |
> | Åtgärd | Microsoft.Network/virtualHubs/write | Create or update a Virtual Hub |
> | Åtgärd | microsoft.network/virtualnetworkgateways/connections/read | Get VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/delete | Deletes a virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/disconnectvirtualnetworkgatewayvpnconnections/action | Disconnect virtual network gateway vpn connections |
> | Åtgärd | microsoft.network/virtualnetworkgateways/generatevpnclientpackage/action | Generate VpnClient package for virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generate VpnProfile package for VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Gets virtualNetworkGateway advertised routes |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getbgppeerstatus/action | Gets virtualNetworkGateway bgp peer status |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Gets virtualnetworkgateway learned routes |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Get Per Vpn Client Connection Health for VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnclientipsecparameters/action | Get Vpnclient Ipsec parameters for VirtualNetworkGateway P2S client. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Gets the URL of a pre-generated vpn client profile package |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/read | Gets a VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/reset/action | Resets a virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/resetvpnclientsharedkey/action | Reset Vpnclient shared key for VirtualNetworkGateway P2S client. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Set Vpnclient Ipsec parameters for VirtualNetworkGateway P2S client. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/startpacketcapture/action | Starts a Virtual Network Gateway Packet Capture. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/stoppacketcapture/action | Stops a Virtual Network Gateway Packet Capture. |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Lists Supported Vpn Devices |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/write | Creates or updates a VirtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualNetworks/BastionHosts/action | Gets Bastion Host references in a Virtual Network. |
> | Åtgärd | Microsoft.Network/virtualNetworks/bastionHosts/default/action | Gets Bastion Host references in a Virtual Network. |
> | Åtgärd | Microsoft.Network/virtualNetworks/checkIpAddressAvailability/read | Check if Ip Address is available at the specified virtual network |
> | Åtgärd | Microsoft.Network/virtualNetworks/delete | Deletes a virtual network |
> | Åtgärd | Microsoft.Network/virtualNetworks/join/action | Joins a virtual network. Not Alertable. |
> | Åtgärd | Microsoft.Network/virtualNetworks/peer/action | Peers a virtual network with another virtual network |
> | Åtgärd | Microsoft.Network/virtualNetworks/read | Get the virtual network definition |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/delete | Deletes A Contextual Service Endpoint Policy |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/read | Gets Contextual Service Endpoint Policies |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/write | Creates a Contextual Service Endpoint Policy or updates an existing Contextual Service Endpoint Policy |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/delete | Deletes a virtual network subnet |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/join/action | Joins a virtual network. Not Alertable. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Joins resource such as storage account or SQL database to a subnet. Not alertable. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Prepares a subnet by applying necessary Network Policies |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/read | Gets a virtual network subnet definition |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/unprepareNetworkPolicies/action | Unprepare a subnet by removing the applied Network Policies |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/virtualMachines/read | Gets references to all the virtual machines in a virtual network subnet |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/write | Creates a virtual network subnet or updates an existing virtual network subnet |
> | Åtgärd | Microsoft.Network/virtualNetworks/usages/read | Get the IP usages for each subnet of the virtual network |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualMachines/read | Gets references to all the virtual machines in a virtual network |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Deletes a virtual network peering |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Gets a virtual network peering definition |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Creates a virtual network peering or updates an existing virtual network peering |
> | Åtgärd | Microsoft.Network/virtualNetworks/write | Creates a virtual network or updates an existing virtual network |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/delete | Delete Virtual Network Tap |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/join/action | Joins a virtual network tap. Not Alertable. |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/read | Get Virtual Network Tap |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/write | Create or Update Virtual Network Tap |
> | Åtgärd | Microsoft.Network/virtualRouters/delete | Deletes A VirtualRouter |
> | Åtgärd | Microsoft.Network/virtualRouters/join/action | Joins A VirtualRouter. Not alertable. |
> | Åtgärd | Microsoft.Network/virtualRouters/peerings/delete | Deletes A VirtualRouterPeering |
> | Åtgärd | Microsoft.Network/virtualRouters/peerings/read | Gets A VirtualRouterPeering |
> | Åtgärd | Microsoft.Network/virtualRouters/peerings/write | Creates A VirtualRouterPeering or Updates An Existing VirtualRouterPeering |
> | Åtgärd | Microsoft.Network/virtualRouters/read | Gets A VirtualRouter |
> | Åtgärd | Microsoft.Network/virtualRouters/write | Creates A VirtualRouter or Updates An Existing VirtualRouter |
> | Åtgärd | Microsoft.Network/virtualWans/delete | Deletes a Virtual Wan |
> | Åtgärd | Microsoft.Network/virtualwans/generateVpnProfile/action | Generate VirtualWanVpnServerConfiguration VpnProfile |
> | Åtgärd | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Deletes a virtual Wan P2SVpnServerConfiguration |
> | Åtgärd | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Gets a virtual Wan P2SVpnServerConfiguration |
> | Åtgärd | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Creates a virtual Wan P2SVpnServerConfiguration or updates an existing virtual Wan P2SVpnServerConfiguration |
> | Åtgärd | Microsoft.Network/virtualWans/read | Get a Virtual Wan |
> | Åtgärd | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Gets supported VirtualWan Security Providers. |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubs/read | Gets all Virtual Hubs that reference a Virtual Wan. |
> | Åtgärd | Microsoft.Network/virtualwans/vpnconfiguration/action | Gets a Vpn Configuration |
> | Åtgärd | Microsoft.Network/virtualwans/vpnServerConfigurations/action | Get VirtualWanVpnServerConfigurations |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSites/read | Gets all VPN Sites that reference a Virtual Wan. |
> | Åtgärd | Microsoft.Network/virtualWans/write | Create or update a Virtual Wan |
> | Åtgärd | Microsoft.Network/vpnGateways/delete | Deletes a VpnGateway. |
> | Åtgärd | microsoft.network/vpngateways/listvpnconnectionshealth/action | Gets connection health for all or a subset of connections on a VpnGateway |
> | Åtgärd | Microsoft.Network/vpnGateways/read | Gets a VpnGateway. |
> | Åtgärd | microsoft.network/vpngateways/reset/action | Resets a VpnGateway |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/delete | Deletes a VpnConnection. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/read | Gets a VpnConnection. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/vpnLinkConnections/read | Gets a Vpn Link Connection |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/write | Puts a VpnConnection. |
> | Åtgärd | Microsoft.Network/vpnGateways/write | Puts a VpnGateway. |
> | Åtgärd | Microsoft.Network/vpnServerConfigurations/delete | Delete VpnServerConfiguration |
> | Åtgärd | Microsoft.Network/vpnServerConfigurations/read | Get VpnServerConfiguration |
> | Åtgärd | Microsoft.Network/vpnServerConfigurations/write | Create or Update VpnServerConfiguration |
> | Åtgärd | Microsoft.Network/vpnsites/delete | Deletes a Vpn Site resource. |
> | Åtgärd | Microsoft.Network/vpnsites/read | Gets a Vpn Site resource. |
> | Åtgärd | microsoft.network/vpnSites/vpnSiteLinks/read | Gets a Vpn Site Link |
> | Åtgärd | Microsoft.Network/vpnsites/write | Creates or updates a Vpn Site resource. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Checks whether or not a given Namespace resource name is available within the NotificationHub service. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Get the list of Namespaces Authorization Rules description. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Get the Connection String to the Namespace |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Namespace Authorization Rule Regenerate Primary/SecondaryKey, Specify the Key that needs to be regenerated |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Checks whether or not a given NotificationHub name is available inside a Namespace. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/Delete | Delete Namespace Resource |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Get the list of Notification Hub Authorization Rules |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Delete Notification Hub Authorization Rules |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Get the Connection String to the Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Get the list of Notification Hub Authorization Rules |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Notification Hub Authorization Rule Regenerate Primary/SecondaryKey, Specify the Key that needs to be regenerated |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Create Notification Hub Authorization Rules and Update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Send a test push notification. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Delete Notification Hub Resource |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Get All Notification Hub PNS Credentials. This includes, WNS, MPNS, APNS, GCM and Baidu credentials |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/read | Get list of Notification Hub Resource Descriptions |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Create a Notification Hub and Update its properties. Its properties mainly include PNS Credentials. Authorization Rules and TTL |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/read | Hämta listan över Beskrivning av namn områdes resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/write | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/operationResults/read | Returns operation results for Notification Hubs provider |
> | Åtgärd | Microsoft.NotificationHubs/operations/read | Returns a list of supported operations for Notification Hubs provider |
> | Åtgärd | Microsoft.NotificationHubs/register/action | Registers the subscription for the NotificationHubs resource provider and enables the creation of Namespaces and NotificationHubs |
> | Åtgärd | Microsoft.NotificationHubs/unregister/action | Unregisters the subscription for the NotificationHubs resource provider and enables the creation of Namespaces and NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/read | Gets the properties of a Hyper-V cluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/clusters/write | Creates or updates the Hyper-V cluster |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/delete | Deletes the Hyper-V site |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/healthsummary/read | Gets the health summary for Hyper-V resource |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/read | Gets the properties of a Hyper-V host |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/write | Creates or updates the Hyper-V host |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/jobs/read | Gets the properties of a Hyper-V jobs |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/machines/read | Gets the properties of a Hyper-V machines |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/operationsstatus/read | Gets the properties of a Hyper-V operation status |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/read | Gets the properties of a Hyper-V site |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/refresh/action | Refreshes the objects within a Hyper-V site |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/runasaccounts/read | Gets the properties of a Hyper-V run as accounts |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/usage/read | Gets the usages of a Hyper-V site |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/write | Creates or updates the Hyper-V site |
> | Åtgärd | Microsoft.OffAzure/Operations/read | Reads the exposed operations |
> | Åtgärd | Microsoft.OffAzure/register/action | Registers Subscription with Microsoft.OffAzure resource provider |
> | Åtgärd | Microsoft.OffAzure/ServerSites/jobs/read | Gets the properties of a Server jobs |
> | Åtgärd | Microsoft.OffAzure/ServerSites/machines/read | Gets the properties of a Server machines |
> | Åtgärd | Microsoft.OffAzure/ServerSites/machines/write | Write the properties of a Server machines |
> | Åtgärd | Microsoft.OffAzure/ServerSites/operationsstatus/read | Gets the properties of a Server operation status |
> | Åtgärd | Microsoft.OffAzure/ServerSites/read | Gets the properties of a Server site |
> | Åtgärd | Microsoft.OffAzure/ServerSites/refresh/action | Refreshes the objects within a Server site |
> | Åtgärd | Microsoft.OffAzure/ServerSites/runasaccounts/read | Gets the properties of a Server run as accounts |
> | Åtgärd | Microsoft.OffAzure/ServerSites/usage/read | Gets the usages of a Server site |
> | Åtgärd | Microsoft.OffAzure/ServerSites/write | Creates or updates the Server site |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/delete | Deletes the VMware site |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/healthsummary/read | Gets the health summary for VMware resource |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/jobs/read | Gets the properties of a VMware jobs |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/read | Gets the properties of a VMware machines |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/start/action | Start VMware machines |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stops the VMware machines |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Gets the properties of a VMware operation status |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/read | Gets the properties of a VMware site |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/refresh/action | Refreshes the objects within a VMware site |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Gets the properties of a VMware run as accounts |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/usage/read | Gets the usages of a VMware site |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/read | Gets the properties of a VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/write | Creates or updates the VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/write | Creates or updates the VMware site |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationalInsights/linkTargets/read | Lists existing accounts that are not associated with an Azure subscription. To link this Azure subscription to a workspace, use a customer id returned by this operation in the customer id property of the Create Workspace operation. |
> | Åtgärd | microsoft.operationalinsights/operations/read | Lists all of the available OperationalInsights Rest API operations. |
> | Åtgärd | microsoft.operationalinsights/register/action | Rergisters the subscription. |
> | Åtgärd | Microsoft.OperationalInsights/register/action | Register a subscription to a resource provider. |
> | Åtgärd | microsoft.operationalinsights/unregister/action | Unregisters the subscription. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/action | Search using new engine. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Get search schema V2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/action | Search using new engine. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Get search schema V2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/delete | Delete Configuration Scope |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/read | Get Configuration Scope |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/configurationScopes/write | Set Configuration Scope |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/delete | Delete datasources under a workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/read | Get datasources under a workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/datasources/write | Create/Update datasources under a workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/delete | Deletes a workspace. If the workspace was linked to an existing workspace at creation time then the workspace it was linked to is not deleted. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/gateways/delete | Removes a gateway configured for the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/generateregistrationcertificate/action | Generates Registration Certificate for the workspace. This Certificate is used to connect Microsoft System Center Operation Manager to the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/disable/action | Disables an intelligence pack for a given workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Enables an intelligence pack for a given workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Lists all intelligence packs that are visible for a given workspace and also lists whether the pack is enabled or disabled for that workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/delete | Delete linked services under given workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/read | Get linked services under given workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/linkedServices/write | Create/Update linked services under given workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/listKeys/action | Retrieves the list keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/listKeys/read | Retrieves the list keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/managementGroups/read | Gets the names and metadata for System Center Operations Manager management groups connected to this workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Get Metric Definitions under workspace |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/delete | Delete the user's notification settings for the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/read | Get the user's notification settings for the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/notificationSettings/write | Set the user's notification settings for the workspace. |
> | Åtgärd | microsoft.operationalinsights/workspaces/operations/read | Gets the status of an OperationalInsights workspace operation. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/purge/action | Delete specified data from workspace |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountLogon/read | Read data from the AADDomainServicesAccountLogon table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesAccountManagement/read | Read data from the AADDomainServicesAccountManagement table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Read data from the AADDomainServicesDirectoryServiceAccess table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Read data from the AADDomainServicesLogonLogoff table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPolicyChange/read | Read data from the AADDomainServicesPolicyChange table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Read data from the AADDomainServicesPrivilegeUse table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Read data from the AADDomainServicesSystemSecurity table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Read data from the ADAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupAlerts/read | Read data from the AddonAzureBackupAlerts table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupJobs/read | Read data from the AddonAzureBackupJobs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupPolicy/read | Read data from the AddonAzureBackupPolicy table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupProtectedInstance/read | Read data from the AddonAzureBackupProtectedInstance table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AddonAzureBackupStorage/read | Read data from the AddonAzureBackupStorage table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Read data from the ADFActivityRun table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Read data from the ADFPipelineRun table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Read data from the ADFTriggerRun table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Read data from the ADReplicationResult table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Read data from the ADSecurityAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AegDeliveryFailureLogs/read | Read data from the AegDeliveryFailureLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AegPublishFailureLogs/read | Read data from the AegPublishFailureLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Alert/read | Read data from the Alert table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AlertHistory/read | Read data from the AlertHistory table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterEvent/read | Read data from the AmlComputeClusterEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AmlComputeClusterNodeEvent/read | Read data from the AmlComputeClusterNodeEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AmlComputeJobEvent/read | Read data from the AmlComputeJobEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ApiManagementGatewayLogs/read | Read data from the ApiManagementGatewayLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppCenterError/read | Read data from the AppCenterError table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ApplicationInsights/read | Read data from the ApplicationInsights table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppPlatformLogsforSpring/read | Read data from the AppPlatformLogsforSpring table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppPlatformSystemLogs/read | Read data from the AppPlatformSystemLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppServiceAppLogs/read | Read data from the AppServiceAppLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppServiceAuditLogs/read | Read data from the AppServiceAuditLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppServiceConsoleLogs/read | Read data from the AppServiceConsoleLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppServiceEnvironmentPlatformLogs/read | Read data from the AppServiceEnvironmentPlatformLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AppServiceHTTPLogs/read | Read data from the AppServiceHTTPLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AuditLogs/read | Read data from the AuditLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleEvaluationsLog/read | Read data from the AutoscaleEvaluationsLog table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Read data from the AutoscaleScaleActionsLog table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AWSCloudTrail/read | Read data from the AWSCloudTrail table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureActivity/read | Read data from the AzureActivity table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Read data from the AzureAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Read data from the AzureMetrics table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BaiClusterEvent/read | Read data from the BaiClusterEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BaiClusterNodeEvent/read | Read data from the BaiClusterNodeEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/BaiJobEvent/read | Read data from the BaiJobEvent table |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/BlockchainApplicationLog/läsa | Läsa data från tabellen BlockchainApplicationLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/BlockchainProxyLog/läsa | Läsa data från tabellen BlockchainProxyLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/BoundPort/läsa | Läsa data från tabellen BoundPort |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/CommonSecurityLog/läsa | Läsa data från tabellen CommonSecurityLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ComputerGroup/läsa | Läsa data från tabellen ComputerGroup |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ConfigurationChange/läsa | Läsa data från tabellen ConfigurationChange |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ConfigurationData/läsa | Läsa data från tabellen ConfigurationData |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerImageInventory/läsa | Läsa data från tabellen ContainerImageInventory |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerInventory/läsa | Läsa data från tabellen ContainerInventory |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerLog/läsa | Läsa data från tabellen ContainerLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerNodeInventory/läsa | Läsa data från tabellen ContainerNodeInventory |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerRegistryLoginEvents/läsa | Läsa data från tabellen ContainerRegistryLoginEvents |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerRegistryRepositoryEvents/läsa | Läsa data från tabellen ContainerRegistryRepositoryEvents |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerServiceLog/läsa | Läsa data från tabellen ContainerServiceLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/CoreAzureBackup/läsa | Läsa data från tabellen CoreAzureBackup |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksAccounts/läsa | Läsa data från tabellen DatabricksAccounts |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksClusters/läsa | Läsa data från tabellen DatabricksClusters |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksDBFS/läsa | Läsa data från tabellen DatabricksDBFS |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksInstancePools/läsa | Läsa data från tabellen DatabricksInstancePools |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksJobs/läsa | Läsa data från tabellen DatabricksJobs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksNotebook/läsa | Läsa data från tabellen DatabricksNotebook |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Läsa data från tabellen DatabricksSecrets |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksSQLPermissions/läsa | Läsa data från tabellen DatabricksSQLPermissions |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksSSH/läsa | Läsa data från tabellen DatabricksSSH |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksTables/läsa | Läsa data från tabellen DatabricksTables |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksWorkspace/läsa | Läsa data från tabellen DatabricksWorkspace |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceAppCrash/läsa | Läsa data från tabellen DeviceAppCrash |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceAppLaunch/läsa | Läsa data från tabellen DeviceAppLaunch |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceCalendar/läsa | Läsa data från tabellen DeviceCalendar |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceCleanup/läsa | Läsa data från tabellen DeviceCleanup |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceConnectSession/läsa | Läsa data från tabellen DeviceConnectSession |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceEtw/läsa | Läsa data från tabellen DeviceEtw |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Läsa data från tabellen DeviceHardwareHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Läsa data från tabellen DeviceHealth |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceHeartbeat/läsa | Läsa data från tabellen DeviceHeartbeat |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceSkypeHeartbeat/läsa | Läsa data från tabellen DeviceSkypeHeartbeat |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Läsa data från tabellen DeviceSkypeSignIn |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Läsa data från tabellen DeviceSleepState |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHAppFailure/läsa | Läsa data från tabellen DHAppFailure |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHAppReliability/läsa | Läsa data från tabellen DHAppReliability |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHCPActivity/läsa | Läsa data från tabellen DHCPActivity |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHDriverReliability/läsa | Läsa data från tabellen DHDriverReliability |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHLogonFailures/läsa | Läsa data från tabellen DHLogonFailures |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHLogonMetrics/läsa | Läsa data från tabellen DHLogonMetrics |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHOSCrashData/läsa | Läsa data från tabellen DHOSCrashData |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHOSReliability/läsa | Läsa data från tabellen DHOSReliability |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHWipAppLearning/läsa | Läsa data från tabellen DHWipAppLearning |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DnsEvents/läsa | Läsa data från tabellen DnsEvents |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/DnsInventory/read | Read data from the DnsInventory table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ETWEvent/read | Read data from the ETWEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Event/read | Read data from the Event table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Read data from the ExchangeAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Read data from the ExchangeOnlineAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/FailedIngestion/read | Read data from the FailedIngestion table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/FunctionAppLogs/read | Read data from the FunctionAppLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Read data from the Heartbeat table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/HuntingBookmark/read | Read data from the HuntingBookmark table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Read data from the IISAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Read data from the InboundConnection table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Read data from the InsightsMetrics table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneAuditLogs/read | Read data from the IntuneAuditLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneDeviceComplianceOrg/read | Read data from the IntuneDeviceComplianceOrg table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IntuneOperationalLogs/read | Read data from the IntuneOperationalLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeEvents/read | Read data from the KubeEvents table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeHealth/read | Read data from the KubeHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeMonAgentEvents/read | Read data from the KubeMonAgentEvents table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeNodeInventory/read | Read data from the KubeNodeInventory table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Read data from the KubePodInventory table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubeServices/read | Read data from the KubeServices table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/LinuxAuditLog/read | Read data from the LinuxAuditLog table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplication/read | Read data from the MAApplication table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealth/read | Read data from the MAApplicationHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthAlternativeVersions/read | Read data from the MAApplicationHealthAlternativeVersions table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationHealthIssues/read | Read data from the MAApplicationHealthIssues table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstance/read | Read data from the MAApplicationInstance table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationInstanceReadiness/read | Read data from the MAApplicationInstanceReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Read data from the MAApplicationReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeploymentPlan/read | Read data from the MADeploymentPlan table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevice/read | Read data from the MADevice table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceNotEnrolled/read | Read data from the MADeviceNotEnrolled table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceNRT/read | Read data from the MADeviceNRT table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealth/read | Read data from the MADevicePnPHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthAlternativeVersions/read | Read data from the MADevicePnPHealthAlternativeVersions table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADevicePnPHealthIssues/read | Read data from the MADevicePnPHealthIssues table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADeviceReadiness/read | Read data from the MADeviceReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Read data from the MADriverInstanceReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverReadiness/read | Read data from the MADriverReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddin/read | Read data from the MAOfficeAddin table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Read data from the MAOfficeAddinEntityHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Read data from the MAOfficeAddinHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Read data from the MAOfficeAddinHealthEventNRT table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthIssues/read | Read data from the MAOfficeAddinHealthIssues table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstance/read | Read data from the MAOfficeAddinInstance table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Read data from the MAOfficeAddinInstanceReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Read data from the MAOfficeAddinReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeApp/read | Read data from the MAOfficeApp table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppCrashesNRT/read | Read data from the MAOfficeAppCrashesNRT table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Read data from the MAOfficeAppHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstance/read | Read data from the MAOfficeAppInstance table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Read data from the MAOfficeAppInstanceHealth table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Read data from the MAOfficeAppReadiness table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Read data from the MAOfficeAppSessionsNRT table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Read data from the MAOfficeBuildInfo table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Read data from the MAOfficeCurrencyAssessment table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Läsa data från tabellen MAOfficeCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeDeploymentStatus/läsa | Läsa data från tabellen MAOfficeDeploymentStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeDeploymentStatusNRT/läsa | Läsa data från tabellen MAOfficeDeploymentStatusNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Läsa data från tabellen MAOfficeMacroErrorNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Läsa data från tabellen MAOfficeMacroGlobalHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Läsa data från tabellen MAOfficeMacroHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Läsa data från tabellen MAOfficeMacroHealthIssues |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Läsa data från tabellen MAOfficeMacroIssueInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Läsa data från tabellen MAOfficeMacroIssueReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Läsa data från tabellen MAOfficeMacroSummary |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Läsa data från tabellen MAOfficeSuite |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Läsa data från tabellen MAOfficeSuiteInstance |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAProposedPilotDevices/läsa | Läsa data från tabellen MAProposedPilotDevices |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAWindowsBuildInfo/läsa | Läsa data från tabellen MAWindowsBuildInfo |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Läsa data från tabellen MAWindowsCurrencyAssessment |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Läsa data från tabellen MAWindowsCurrencyAssessmentDailyCounts |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAWindowsDeploymentStatus/läsa | Läsa data från tabellen MAWindowsDeploymentStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAWindowsDeploymentStatusNRT/läsa | Läsa data från tabellen MAWindowsDeploymentStatusNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Läsa data från tabellen MAWindowsSysReqInstanceReadiness |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/McasShadowItReporting/läsa | Läsa data från tabellen McasShadowItReporting |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftAzureBastionAuditLogs/läsa | Läsa data från tabellen MicrosoftAzureBastionAuditLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftDataShareReceivedSnapshotLog/läsa | Läsa data från tabellen MicrosoftDataShareReceivedSnapshotLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftDataShareSentSnapshotLog/läsa | Läsa data från tabellen MicrosoftDataShareSentSnapshotLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftDataShareShareLog/läsa | Läsa data från tabellen MicrosoftDataShareShareLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftDynamicsTelemetryPerformanceLogs/läsa | Läsa data från tabellen MicrosoftDynamicsTelemetryPerformanceLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftDynamicsTelemetrySystemMetricsLogs/läsa | Läsa data från tabellen MicrosoftDynamicsTelemetrySystemMetricsLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftHealthcareApisAuditLogs/läsa | Läsa data från tabellen MicrosoftHealthcareApisAuditLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftInsightsAzureActivityLog/läsa | Läsa data från tabellen MicrosoftInsightsAzureActivityLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/NetworkMonitoring/läsa | Läsa data från tabellen NetworkMonitoring |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/OfficeActivity/läsa | Läsa data från tabellen OfficeActivity |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/åtgärd/läsa | Läs data från åtgärds tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Läsa data från tabellen OutboundConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Perf/read | Läs data från tabellen perf |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ProtectionStatus/läsa | Läsa data från tabellen ProtectionStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/läsa | Köra frågor över data i arbets ytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Läsa data från tabellen ReservedCommonFields |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Läsa data från tabellen SCCMAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Läsa data från tabellen SCOMAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityAlert/läsa | Läsa data från tabellen SecurityAlert |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityBaseline/läsa | Läsa data från tabellen SecurityBaseline |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityBaselineSummary/läsa | Läsa data från tabellen SecurityBaselineSummary |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Läsa data från tabellen SecurityDetection |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityEvent/läsa | Läsa data från tabellen SecurityEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Läsa data från tabellen SecurityIoTRawEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Läsa data från tabellen SecurityRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Läsa data från tabellen ServiceFabricOperationalEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Läsa data från tabellen ServiceFabricReliableActorEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Läsa data från tabellen ServiceFabricReliableServiceEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Läsa data från tabellen SfBAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Läsa data från tabellen SfBOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Läsa data från tabellen SharePointOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SignalRServiceDiagnosticLogs/läsa | Läsa data från tabellen SignalRServiceDiagnosticLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Läsa data från tabellen SigninLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SPAssessmentRecommendation/läsa | Läsa data från tabellen SPAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Läsa data från tabellen SQLAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SQLQueryPerformance/läsa | Läsa data från tabellen SQLQueryPerformance |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SqlThreatProtectionLoginAudits/läsa | Läsa data från tabellen SqlThreatProtectionLoginAudits |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SqlVulnerabilityAssessmentResult/read | Read data from the SqlVulnerabilityAssessmentResult table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/StorageBlobLogs/read | Read data from the StorageBlobLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/StorageFileLogs/read | Read data from the StorageFileLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/StorageQueueLogs/read | Read data from the StorageQueueLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/StorageTableLogs/read | Read data from the StorageTableLogs table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SucceededIngestion/read | Read data from the SucceededIngestion table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Syslog/read | Read data from the Syslog table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Read data from the SysmonEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read | Reading data from any custom log |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Read data from the ThreatIntelligenceIndicator table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAApp/read | Read data from the UAApp table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputer/read | Read data from the UAComputer table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Read data from the UAComputerRank table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Read data from the UADriver table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Read data from the UADriverProblemCodes table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAFeedback/read | Read data from the UAFeedback table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Read data from the UAHardwareSecurity table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAIESiteDiscovery/read | Read data from the UAIESiteDiscovery table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Read data from the UAOfficeAddIn table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Read data from the UAProposedActionPlan table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Read data from the UASysReqIssue table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAUpgradedComputer/read | Read data from the UAUpgradedComputer table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Update/read | Read data from the Update table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Read data from the UpdateRunProgress table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Read data from the UpdateSummary table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Usage/read | Read data from the Usage table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Read data from the VMBoundPort table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMComputer/read | Read data from the VMComputer table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Read data from the VMConnection table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMProcess/read | Read data from the VMProcess table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/W3CIISLog/read | Read data from the W3CIISLog table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSDeploymentStatus/read | Read data from the WaaSDeploymentStatus table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSInsiderStatus/read | Read data from the WaaSInsiderStatus table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WaaSUpdateStatus/read | Read data from the WaaSUpdateStatus table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WDAVStatus/read | Read data from the WDAVStatus table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WDAVThreat/read | Read data from the WDAVThreat table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Read data from the WindowsClientAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsEvent/read | Read data from the WindowsEvent table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Read data from the WindowsFirewall table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Read data from the WindowsServerAssessmentRecommendation table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WireData/read | Read data from the WireData table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WorkloadMonitoringPerf/read | Read data from the WorkloadMonitoringPerf table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WUDOAggregatedStatus/read | Read data from the WUDOAggregatedStatus table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WUDOStatus/read | Read data from the WUDOStatus table |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/read | Gets an existing workspace |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/regeneratesharedkey/action | Regenerates the specified workspace shared key |
> | Åtgärd | microsoft.operationalinsights/workspaces/rules/read | Get all alert rules. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Deletes a saved search query |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/read | Gets a saved search query |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/results/read | Get saved searches results. Inaktuell |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/delete | Delete scheduled search actions. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/read | Get scheduled search actions. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Create or update scheduled search actions. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/delete | Delete scheduled searches. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/read | Get scheduled searches. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Create or update scheduled searches. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/write | Creates a saved search query |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/schema/read | Gets the search schema for the workspace.  Search schema includes the exposed fields and their types. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/search/action | Executes a search query |
> | Åtgärd | microsoft.operationalinsights/workspaces/search/read | Get search results. Inaktuellt. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Retrieves the shared keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Retrieves the shared keys for the workspace. These keys are used to connect Microsoft Operational Insights agents to the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/delete | Deletes a storage configuration. This will stop Microsoft Operational Insights from reading data from the storage account. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/read | Gets a storage configuration. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/write | Creates a new storage configuration. These configurations are used to pull data from a location in an existing storage account. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Get Search Upgrade Translation Failure log for the workspace |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/usages/read | Gets usage data for a workspace including the amount of data read by the workspace. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/write | Creates a new workspace or links to an existing workspace by providing the customer id from the existing workspace. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/delete | Delete existing Management Association |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/read | Get Existing Management Association |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/write | Create a new Management Association |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/delete | Delete existing Management Configuration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/read | Get Existing Management Configuration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/write | Create a new Management Configuration |
> | Åtgärd | Microsoft.OperationsManagement/register/action | Register a subscription to a resource provider. |
> | Åtgärd | Microsoft.OperationsManagement/solutions/delete | Delete existing OMS solution |
> | Åtgärd | Microsoft.OperationsManagement/solutions/read | Get exiting OMS solution |
> | Åtgärd | Microsoft.OperationsManagement/solutions/write | Create new OMS solution |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PolicyInsights/asyncOperationResults/read | Gets the async operation result. |
> | DataAction | Microsoft.PolicyInsights/checkDataPolicyCompliance/action | Check the compliance status of a given component against data policies. |
> | Åtgärd | Microsoft.PolicyInsights/operations/read | Gets supported operations on Microsoft.PolicyInsights namespace |
> | DataAction | Microsoft.PolicyInsights/policyEvents/logDataEvents/action | Log the resource component policy events. |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/action | Query information about policy events. |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/read | Query information about policy events. |
> | Åtgärd | Microsoft.PolicyInsights/policyMetadata/read | Get Policy Metadata resources. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/action | Query information about policy states. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/read | Query information about policy states. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/action | Query summary information about policy latest states. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/read | Query summary information about policy latest states. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Triggers a new compliance evaluation for the selected scope. |
> | Åtgärd | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Query information about resources required by DeployIfNotExists policies. |
> | Åtgärd | Microsoft.PolicyInsights/register/action | Registers the Microsoft Policy Insights resource provider and enables actions on it. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/cancel/action | Cancel in-progress Microsoft Policy remediations. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/delete | Delete policy remediations. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/listDeployments/read | Lists the deployments required by a policy remediation. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/read | Get policy remediations. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/write | Create or update Microsoft Policy remediations. |
> | Åtgärd | Microsoft.PolicyInsights/unregister/action | Unregisters the Microsoft Policy Insights resource provider. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Portal/consoles/delete | Removes the Cloud Shell instance. |
> | Åtgärd | Microsoft.Portal/consoles/read | Reads the Cloud Shell instance. |
> | Åtgärd | Microsoft.Portal/consoles/write | Create or update a Cloud Shell instance. |
> | Åtgärd | Microsoft.Portal/dashboards/delete | Removes the dashboard from the subscription. |
> | Åtgärd | Microsoft.Portal/dashboards/read | Reads the dashboards for the subscription. |
> | Åtgärd | Microsoft.Portal/dashboards/write | Add or modify dashboard to a subscription. |
> | Åtgärd | Microsoft.Portal/register/action | Register to Portal |
> | Åtgärd | Microsoft.Portal/usersettings/delete | Removes the Cloud Shell user settings. |
> | Åtgärd | Microsoft.Portal/usersettings/read | Reads the Cloud Shell user settings. |
> | Åtgärd | Microsoft.Portal/usersettings/write | Create or update Cloud Shell user setting. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/delete | Deletes the Power BI Dedicated Capacity. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/read | Retrieves the information of the specified Power BI Dedicated Capacity. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/resume/action | Resumes the Capacity. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/skus/read | Retrieve available SKU information for the capacity |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/suspend/action | Suspends the Capacity. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/write | Creates or updates the specified Power BI Dedicated Capacity. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/checkNameAvailability/action | Checks that given Power BI Dedicated Capacity name is valid and not in use. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/operationresults/read | Retrieves the information of the specified operation result. |
> | Åtgärd | Microsoft.PowerBIDedicated/locations/operationstatuses/read | Retrieves the information of the specified operation status. |
> | Åtgärd | Microsoft.PowerBIDedicated/operations/read | Retrieves the information of operations |
> | Åtgärd | Microsoft.PowerBIDedicated/register/action | Registers Power BI Dedicated resource provider. |
> | Åtgärd | Microsoft.PowerBIDedicated/skus/read | Retrieves the information of Skus |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp is internal operation used by service |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp is internal operation used by service |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Create a backup Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupStatus/action | Check Backup Status for Recovery Services Vaults |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validate Features |
> | Åtgärd | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Check Resource Name Availability is an API to check if resource name is available |
> | Åtgärd | Microsoft.RecoveryServices/locations/operationStatus/read | Gets Operation Status for a given Operation |
> | Åtgärd | Microsoft.RecoveryServices/operations/read | Operation returns the list of Operations for a Resource Provider |
> | Åtgärd | Microsoft.RecoveryServices/register/action | Registers subscription for given Resource Provider |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returns Configuration for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/write | Updates Configuration for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/read | Gets Backup Resource Encryption Configuration. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupEncryptionConfigs/write | Updates Backup Resource Encryption Configuration |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupEngines/read | Returns all the backup management servers registered with vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Delete a backup Protection Intent |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Get a backup Protection Intent |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Create a backup Protection Intent |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returns status of the operation |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/operationsStatus/read | Returns status of the operation |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Get all protectable containers |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Deletes the registered Container |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Do inquiry for workloads within a container |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Get all items in a container |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Gets result of Operation performed on Protection Container. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationsStatus/read | Gets status of Operation performed on Protection Container. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Performs Backup for Protected Item. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Deletes Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Gets Result of Operation Performed on Protected Items. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returns the status of Operation performed on Protected Items. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returns object details of the Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Provision Instant Item Recovery for Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Get Recovery Points for Protected Items. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Restore Recovery Points for Protected Items. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Revoke Instant Item Recovery for Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Create a backup Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returns all registered containers |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Creates a registered container |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Refreshes the container list |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Cancel the Job |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returns the Result of Job Operation. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/operationsStatus/read | Returns the status of Job Operation. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returns all Job Objects |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Export Jobs |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returns Backup Operation Result for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returns Backup Operation Status for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/delete | Delete a Protection Policy |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Get Results of Policy Operation. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Get Status of Policy Operation. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returns all Protection Policies |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Creates Protection Policy |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returns list of all Protectable Items. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returns the list of all Protected Items. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returns all containers belonging to the subscription |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | List all backup Protection Intents |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Returns Security PIN Information for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returns Storage Configuration for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Updates Storage Configuration for Recovery Services Vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returns summaries for Protected Items and Protected Servers for a Recovery Services . |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Validate Operation on Protected Item |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/delete | Åtgärden ta bort valv tar bort den angivna Azure-resursen av typen valv |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/write | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Gets the alerts for the Recovery services vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Resolves the alert. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Gets the Recovery services vault notification configuration. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Configures e-mail notifications to Recovery services vault. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | The UnRegister Container operation can be used to unregister a container. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | The Get Operation Results operation can be used get the operation status and result for the asynchronously submitted operation |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | The Get Containers operation can be used get the containers registered for a resource. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | The Register Service Container operation can be used to register a container with Recovery Service. |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Read any Alerts Settings |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Create or Update any Alerts Settings |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationEvents/read | Read any Events |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Checks Consistency of the Fabric |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Delete any Fabrics |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Deploy Process Server Image |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrate Fabric To AAD |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/operationresults/read | Track the results of an asynchronous operation on the resource Fabrics |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Read any Fabrics |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Reassociate Gateway |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Remove Fabric |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Renew Certificate for Fabric |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Read any Logical Networks |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete | Delete any Network Mappings |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Create or Update any Network Mappings |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Discover Protectable Item |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/operationresults/read | Track the results of an asynchronous operation on the resource Protection Containers |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Remove Protection Container |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Delete any Migration Items |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrate Item |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Read any Migration Recovery Points |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/read | Track the results of an asynchronous operation on the resource Migration Items |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Läs eventuella migreringsåtgärder |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Test Migrate |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Test Migrate Cleanup |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Create or Update any Migration Items |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Read any Protectable Items |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lägg till diskar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Apply Recovery Point |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Delete any Protected Items |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Failover Commit |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/read | Track the results of an asynchronous operation on the resource Protected Items |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läs alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läs alla återställnings punkter för replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Ta bort skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Ta bort diskar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Skicka feedback |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Läs alla mål beräknings storlekar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Rensning av redundanstest |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | växling vid fel |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdatera mobilitets tjänsten |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Skapa eller uppdatera skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Ta bort alla skydds behållar mappningar |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs skyddets behållar mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läs alla skydds behållar mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Ta bort skydds container mappning |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Skapa eller uppdatera alla skydds behållar mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Växla skydds behållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Skapa eller uppdatera eventuella skydds behållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Ta bort eventuella Recovery Services-leverantörer |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationRecoveryServicesProviders/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs Recovery Services Providers |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läs eventuella Recovery Services-leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera Provider |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Ta bort Recovery Services Provider |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Skapa eller uppdatera Recovery Services leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läs alla lagrings klassificeringar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Ta bort alla mappningar för lagrings klassificering |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs lagrings klassificerings mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läs alla mappningar för lagrings klassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Skapa eller uppdatera mappningar för lagrings klassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ta bort alla vCenter |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationvCenters/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resursens vCenter |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läs eventuella vCenter |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Skapa eller uppdatera eventuella vCenter |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Skapa eller uppdatera eventuella infrastruktur resurser |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationJobs/Avbryt/åtgärd | Avbryt jobb |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationJobs/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs jobben |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/read | Läs alla jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Starta om jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Återuppta jobb |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Läs eventuella migreringsåtgärder |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Läs alla nätverk |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationOperationStatus/läsa | Läs eventuell åtgärds status för valvets replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Ta bort eventuella principer |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationPolicies/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs principerna |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läs eventuella principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Skapa eller uppdatera principer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Läs alla skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Läs alla skydds behållar mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationRecoveryPlans/Delete | Ta bort eventuella återställnings planer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Återställnings plan för redundans |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationRecoveryPlans/operationresults/Read | Spåra resultatet av en asynkron åtgärd i resurs återställnings planerna |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Återställnings plan för planerad redundansväxling |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läs eventuella återställnings planer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Skydda återställnings plan |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Återställnings plan för redundanstest |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Återställnings plan för rensning av redundanstest |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställnings plan för redundans |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Skapa eller uppdatera eventuella återställnings planer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Läs eventuella Recovery Services-leverantörer |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Läs alla mappningar för lagrings klassificering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Läs alla lagrings klassificeringar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Läs alla  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationUsages/read | Läs om användning av valv replikering |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationVaultHealth/operationresults/Read | Spåra resultatet av en asynkron åtgärd i Resource Vault-replikeringens hälsa |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Läs all hälsa för valv-replikering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Uppdatera hälsa för valv |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Läs alla  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Skapa eller uppdatera alla  |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Läs eventuella vCenter |
> | Åtgärd | Microsoft.RecoveryServices/vaults/usages/read | Läs alla valv användningar |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/write | Create Vault operation creates an Azure resource of type 'vault' |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. relä/checkNameAvailability/åtgärd | Checks availability of namespace under given subscription. |
> | Åtgärd | Microsoft. relä/checkNamespaceAvailability/åtgärd | Checks availability of namespace under given subscription. This API is deprecated please use CheckNameAvailability instead. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/action | Updates Namespace Authorization Rule. This API is deprecated. Please use a PUT call to update the Namespace Authorization Rule instead.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Åtgärd | Microsoft. Relay/Namespaces/authorizationRules/listnycklar/Action | Get the Connection String to the Namespace |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Åtgärd | Microsoft.Relay/namespaces/Delete | Delete Namespace Resource |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Gets the authorization rules keys for the Disaster Recovery primary namespace |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Get Disaster Recovery Primary Namespace's Authorization Rules |
> | Åtgärd | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Disables Disaster Recovery and stops replicating changes from primary to secondary namespaces. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Checks availability of namespace alias under given subscription. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Deletes the Disaster Recovery configuration associated with the namespace. This operation can only be invoked via the primary namespace. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/failover/action | Invokes a GEO DR failover and reconfigures the namespace alias to point to the secondary namespace. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Gets the Disaster Recovery configuration associated with the namespace. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Creates or Updates the Disaster Recovery configuration associated with the namespace. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Operation to update HybridConnection. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Operation to delete HybridConnection Authorization Rules |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Get the Connection String to HybridConnection |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Get the list of HybridConnection Authorization Rules |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Create HybridConnection Authorization Rules and Update its properties. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/Delete | Operation to delete HybridConnection Resource |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/read | Get list of HybridConnection Resource Descriptions |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/write | Create or Update HybridConnection properties. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/read | Gets the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/write | Updates the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/operationresults/read | Get the status of Namespace operation |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Get list of Namespace diagnostic settings Resource Descriptions |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Get list of Namespace diagnostic settings Resource Descriptions |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/logDefinitions/read | Get list of Namespace logs Resource Descriptions |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Åtgärd | Microsoft.Relay/namespaces/read | Hämta listan över Beskrivning av namn områdes resurs |
> | Åtgärd | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnrymd |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Operation to update WcfRelay. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Operation to delete WcfRelay Authorization Rules |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/listkeys/action | Get the Connection String to WcfRelay |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Get the list of WcfRelay Authorization Rules |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Create WcfRelay Authorization Rules and Update its properties. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/Delete | Operation to delete WcfRelay Resource |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/read | Get list of WcfRelay Resource Descriptions |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/write | Create or Update WcfRelay properties. |
> | Åtgärd | Microsoft.Relay/namespaces/write | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.Relay/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft.Relay/register/action | Registers the subscription for the Relay resource provider and enables the creation of Relay resources |
> | Åtgärd | Microsoft.Relay/unregister/action | Registers the subscription for the Relay resource provider and enables the creation of Relay resources |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Gets the availability status for the specified resource |
> | Åtgärd | Microsoft.ResourceHealth/AvailabilityStatuses/read | Gets the availability statuses for all resources in the specified scope |
> | Åtgärd | Microsoft.ResourceHealth/events/read | Get Service Health Events for given subscription |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/action | Denotes the change in health state for the specified resource |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Activated/action | Denotes the change in health state for the specified resource |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/InProgress/action | Denotes the change in health state for the specified resource |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Pending/action | Denotes the change in health state for the specified resource |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Resolved/action | Denotes the change in health state for the specified resource |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Updated/action | Denotes the change in health state for the specified resource |
> | Åtgärd | Microsoft.ResourceHealth/impactedResources/read | Get Impacted Resources for given subscription |
> | Åtgärd | Microsoft.ResourceHealth/metadata/read | Gets Metadata |
> | Åtgärd | Microsoft.ResourceHealth/Notifications/read | Receives Azure Resource Manager notifications |
> | Åtgärd | Microsoft.ResourceHealth/Operations/read | Get the operations available for the Microsoft ResourceHealth |
> | Åtgärd | Microsoft.ResourceHealth/register/action | Registers the subscription for the Microsoft ResourceHealth |
> | Åtgärd | Microsoft.ResourceHealth/unregister/action | Unregisters the subscription for the Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Resources/calculateTemplateHash/action | Calculate the hash of provided template. |
> | Åtgärd | Microsoft.Resources/checkPolicyCompliance/read | Check the compliance status of a given resource against resource policies. |
> | Åtgärd | Microsoft.Resources/checkResourceName/action | Check the resource name for validity. |
> | Åtgärd | Microsoft.Resources/deployments/cancel/action | Cancels a deployment. |
> | Åtgärd | Microsoft.Resources/deployments/delete | Deletes a deployment. |
> | Åtgärd | Microsoft.Resources/deployments/exportTemplate/action | Export template for a deployment |
> | Åtgärd | Microsoft.Resources/deployments/operations/read | Gets or lists deployment operations. |
> | Åtgärd | Microsoft.Resources/deployments/operationstatuses/read | Gets or lists deployment operation statuses. |
> | Åtgärd | Microsoft.Resources/deployments/read | Gets or lists deployments. |
> | Åtgärd | Microsoft.Resources/deployments/validate/action | Validates an deployment. |
> | Åtgärd | Microsoft.Resources/deployments/whatIf/action | Predicts template deployment changes. |
> | Åtgärd | Microsoft.Resources/deployments/write | Creates or updates an deployment. |
> | Åtgärd | Microsoft.Resources/deploymentScripts/delete | Deletes a deployment script |
> | Åtgärd | Microsoft.Resources/deploymentScripts/logs/read | Gets or lists deployment script logs |
> | Åtgärd | Microsoft.Resources/deploymentScripts/read | Gets or lists deployment scripts |
> | Åtgärd | Microsoft.Resources/deploymentScripts/write | Creates or updates a deployment script |
> | Åtgärd | Microsoft.Resources/links/delete | Deletes a resource link. |
> | Åtgärd | Microsoft.Resources/links/read | Gets or lists resource links. |
> | Åtgärd | Microsoft.Resources/links/write | Creates or updates a resource link. |
> | Åtgärd | Microsoft.Resources/marketplace/purchase/action | Purchases a resource from the marketplace. |
> | Åtgärd | Microsoft.Resources/providers/read | Get the list of providers. |
> | Åtgärd | Microsoft.Resources/resources/read | Get the list of resources based upon filters. |
> | Åtgärd | Microsoft.Resources/subscriptions/locations/read | Gets the list of locations supported. |
> | Åtgärd | Microsoft.Resources/subscriptions/operationresults/read | Get the subscription operation results. |
> | Åtgärd | Microsoft.Resources/subscriptions/providers/read | Gets or lists resource providers. |
> | Åtgärd | Microsoft.Resources/subscriptions/read | Gets the list of subscriptions. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/delete | Deletes a resource group and all its resources. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/operations/read | Gets or lists deployment operations. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/operationstatuses/read | Gets or lists deployment operation statuses. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/read | Gets or lists deployments. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/deployments/write | Creates or updates an deployment. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/moveResources/action | Moves resources from one resource group to another. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/read | Gets or lists resource groups. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourcegroups/resources/read | Gets the resources for the resource group. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Validate move of resources from one resource group to another. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/write | Creates or updates a resource group. |
> | Åtgärd | Microsoft.Resources/subscriptions/resources/read | Gets resources of a subscription. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/delete | Deletes a subscription tag. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/read | Gets or lists subscription tags. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/tagValues/delete | Deletes a subscription tag value. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/tagValues/read | Gets or lists subscription tag values. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/tagValues/write | Adds a subscription tag value. |
> | Åtgärd | Microsoft.Resources/subscriptions/tagNames/write | Adds a subscription tag. |
> | Åtgärd | Microsoft.Resources/tags/delete | Removes all the tags on a resource. |
> | Åtgärd | Microsoft.Resources/tags/read | Gets all the tags on a resource. |
> | Åtgärd | Microsoft.Resources/tags/write | Updates the tags on a resource by replacing or merging existing tags with a new set of tags, or removing existing tags. |
> | Åtgärd | Microsoft.Resources/tenants/read | Gets the list of tenants. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Scheduler/jobcollections/delete | Deletes job collection. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/disable/action | Disables job collection. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/enable/action | Enables job collection. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/delete | Deletes job. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/generateLogicAppDefinition/action | Generates Logic App definition based on a Scheduler Job. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/jobhistories/read | Gets job history. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/read | Gets job. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/run/action | Runs job. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/jobs/write | Creates or updates job. |
> | Åtgärd | Microsoft.Scheduler/jobcollections/read | Get Job Collection |
> | Åtgärd | Microsoft.Scheduler/jobcollections/write | Creates or updates job collection. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Search/checkNameAvailability/action | Checks availability of the service name. |
> | Åtgärd | Microsoft.Search/operations/read | Lists all of the available operations of the Microsoft.Search provider. |
> | Åtgärd | Microsoft.Search/register/action | Registers the subscription for the search resource provider and enables the creation of search services. |
> | Åtgärd | Microsoft.Search/searchServices/createQueryKey/action | Creates the query key. |
> | Åtgärd | Microsoft.Search/searchServices/delete | Deletes the search service. |
> | Åtgärd | Microsoft.Search/searchServices/deleteQueryKey/delete | Deletes the query key. |
> | Åtgärd | Microsoft.Search/searchServices/listAdminKeys/action | Reads the admin keys. |
> | Åtgärd | Microsoft.Search/searchServices/listQueryKeys/action | Returns the list of query API keys for the given Azure Search service. |
> | Åtgärd | Microsoft.Search/searchServices/listQueryKeys/read | Returns the list of query API keys for the given Azure Search service. |
> | Åtgärd | Microsoft.Search/searchServices/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Åtgärd | Microsoft.Search/searchServices/privateEndpointConnectionProxies/read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy |
> | Åtgärd | Microsoft. search/searchServices/privateEndpointConnectionProxies/validate/Action | Validates a private endpoint connection create call from NRP side |
> | Åtgärd | Microsoft. search/searchServices/privateEndpointConnectionProxies/Write | Skapar en anslutning till en privat slutpunkt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna proxyn för privat slut punkt anslutning |
> | Åtgärd | Microsoft.Search/searchServices/read | Läser Sök tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/regenerateAdminKey/action | Återskapar administratörs nyckeln. |
> | Åtgärd | Microsoft. search/searchServices/start/Action | Startar Sök tjänsten. |
> | Åtgärd | Microsoft. search/searchServices/stoppa/åtgärd | Stoppar Sök tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/write | Skapar eller uppdaterar Sök tjänsten. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Tillämpar de aktuella reglerna för trafik härdning genom att skapa matchande säkerhets regler för de nätverks säkerhets grupper som tilldelats |
> | Åtgärd | Microsoft.Security/adaptiveNetworkHardenings/read | Hämtar anpassade nätverks härdnings rekommendationer för en Azure-skyddad resurs |
> | Åtgärd | Microsoft.Security/advancedThreatProtectionSettings/read | Hämtar inställningarna för avancerat skydd för resursen |
> | Åtgärd | Microsoft.Security/advancedThreatProtectionSettings/write | Uppdaterar inställningarna för avancerat skydd för resursen |
> | Åtgärd | Microsoft.Security/alerts/read | Hämtar alla tillgängliga säkerhets aviseringar |
> | Åtgärd | Microsoft. Security/applicationWhitelistings/Read | Hämtar appens whitelistings |
> | Åtgärd | Microsoft. Security/applicationWhitelistings/Write | Skapar ett nytt program vit listning eller uppdaterar ett befintligt |
> | Åtgärd | Microsoft. Security/assessmentMetadata/Read | Hämta tillgängliga metadata för säkerhets utvärdering i din prenumeration |
> | Åtgärd | Microsoft. Security/assessmentMetadata/Write | Skapa eller uppdatera metadata för säkerhets utvärdering |
> | Åtgärd | Microsoft. Security/assessments/Read | Få säkerhets utvärderingar i din prenumeration |
> | Åtgärd | Microsoft. Security/Assessment/Write | Skapa eller uppdatera säkerhets utvärderingar i din prenumeration |
> | Åtgärd | Microsoft. Security/complianceResults/Read | Hämtar kompabilitets resultat för resursen |
> | Åtgärd | Microsoft. Security/informationProtectionPolicies/Read | Hämtar information Protection-principer för resursen |
> | Åtgärd | Microsoft.Security/informationProtectionPolicies/write | Uppdaterar information Protection-principer för resursen |
> | Åtgärd | Microsoft. Security/locations/Alerts/Activate/åtgärd | Aktivera en säkerhets avisering |
> | Åtgärd | Microsoft.Security/locations/alerts/dismiss/action | Ignorera en säkerhets avisering |
> | Åtgärd | Microsoft. Security/locations/Alerts/Read | Hämtar alla tillgängliga säkerhets aviseringar |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Tar bort just-in-time-principen för nätverks åtkomst |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initierar en just-in-Time-begäran om nätverks åtkomst princip |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Hämtar just-in-Time-principer för nätverks åtkomst |
> | Åtgärd | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Skapar en ny princip för nätverks åtkomst just-in-Time eller uppdaterar en befintlig |
> | Åtgärd | Microsoft. Security/locations/Read | Hämtar säkerhets data platsen |
> | Åtgärd | Microsoft. Security/locations/tasks/Activate/Action | Aktivera en säkerhets rekommendation |
> | Åtgärd | Microsoft.Security/locations/tasks/dismiss/action | Ignorera en säkerhets rekommendation |
> | Åtgärd | Microsoft. Security/locations/tasks/Read | Hämtar alla tillgängliga säkerhets rekommendationer |
> | Åtgärd | Microsoft. Security/locations/tasks/resolve/Action | Lösa en säkerhets rekommendation |
> | Åtgärd | Microsoft. Security/locations/tasks/start/Action | Starta en säkerhets rekommendation |
> | Åtgärd | Microsoft. Security/Policies/Read | Hämtar säkerhets principen |
> | Åtgärd | Microsoft.Security/policies/write | Uppdaterar säkerhets principen |
> | Åtgärd | Microsoft. Security/prissättning/ta bort | Tar bort pris inställningarna för omfånget |
> | Åtgärd | Microsoft. Security/prissättning/läsa | Hämtar pris inställningarna för omfånget |
> | Åtgärd | Microsoft. Security/prissättning/Skriv | Uppdaterar pris inställningarna för omfånget |
> | Åtgärd | Microsoft. Security/register/åtgärd | Registrerar prenumerationen för Azure Security Center |
> | Åtgärd | Microsoft. Security/securityContacts/Delete | Tar bort säkerhets kontakten |
> | Åtgärd | Microsoft. Security/securityContacts/Read | Hämtar säkerhets kontakten |
> | Åtgärd | Microsoft.Security/securityContacts/write | Uppdaterar säkerhets kontakten |
> | Åtgärd | Microsoft.Security/securitySolutions/delete | Tar bort en säkerhets lösning |
> | Åtgärd | Microsoft.Security/securitySolutions/read | Hämtar säkerhetslösningarna |
> | Åtgärd | Microsoft.Security/securitySolutions/write | Skapar en ny säkerhets lösning eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/securitySolutionsReferenceData/read | Hämtar säkerhets lösningens referens data |
> | Åtgärd | Microsoft.Security/securityStatuses/read | Hämtar säkerhets hälso status för Azure-resurser |
> | Åtgärd | Microsoft.Security/securityStatusesSummaries/read | Hämtar säkerhets status sammanfattningar för omfattningen |
> | Åtgärd | Microsoft.Security/settings/read | Hämtar inställningarna för omfånget |
> | Åtgärd | Microsoft.Security/settings/write | Uppdaterar inställningarna för omfånget |
> | Åtgärd | Microsoft. Security/tasks/Read | Hämtar alla tillgängliga säkerhets rekommendationer |
> | Åtgärd | Microsoft.Security/unregister/action | Avregistrerar prenumerationen från Azure Security Center |
> | Åtgärd | Microsoft. Security/webApplicationFirewalls/Delete | Tar bort en brand vägg för webbaserade program |
> | Åtgärd | Microsoft. Security/webApplicationFirewalls/Read | Hämtar brand väggar för webb program |
> | Åtgärd | Microsoft. Security/webApplicationFirewalls/Write | Skapar en ny brand vägg för webbaserade program eller uppdaterar en befintlig |
> | Åtgärd | Microsoft.Security/workspaceSettings/connect/action | Ändra inställningar för arbets ytans åter anslutning |
> | Åtgärd | Microsoft.Security/workspaceSettings/delete | Tar bort inställningarna för arbets ytan |
> | Åtgärd | Microsoft.Security/workspaceSettings/read | Hämtar inställningar för arbets yta |
> | Åtgärd | Microsoft.Security/workspaceSettings/write | Uppdaterar inställningarna för arbets ytan |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/delete | Tar bort en diagnostisk inställning |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/read | Läser en diagnostisk inställning |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettings/write | Skriva en diagnostisk inställning |
> | Åtgärd | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Läser en diagnostisk inställnings kategori |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. SecurityInsights/agg regeringar/Read | Hämtar aggregerad information |
> | Åtgärd | Microsoft. SecurityInsights/alertRules/Actions/Delete | Tar bort svars åtgärder för en varnings regel |
> | Åtgärd | Microsoft. SecurityInsights/alertRules/Actions/Read | Hämtar svars åtgärder för en varnings regel |
> | Åtgärd | Microsoft. SecurityInsights/alertRules/Actions/Write | Uppdaterar svars åtgärderna för en varnings regel |
> | Åtgärd | Microsoft. SecurityInsights/alertRules/Delete | Tar bort aviserings regler |
> | Åtgärd | Microsoft. SecurityInsights/alertRules/Read | Hämtar aviserings reglerna |
> | Åtgärd | Microsoft. SecurityInsights/alertRules/Write | Uppdaterar aviserings regler |
> | Åtgärd | Microsoft. SecurityInsights/bok märken/ta bort | Tar bort bok märken |
> | Åtgärd | Microsoft. SecurityInsights/bok märken/Expand/åtgärd | Hämtar relaterade entiteter av en entitet med en speciell expansion |
> | Åtgärd | Microsoft. SecurityInsights/bok märken/läsa | Hämtar bok märken |
> | Åtgärd | Microsoft. SecurityInsights/bok märken/skriva | Uppdaterings bok märken |
> | Åtgärd | Microsoft.SecurityInsights/cases/comments/read | Gets the case comments |
> | Åtgärd | Microsoft.SecurityInsights/cases/comments/write | Creates the case comments |
> | Åtgärd | Microsoft.SecurityInsights/cases/delete | Deletes a case |
> | Åtgärd | Microsoft.SecurityInsights/cases/investigations/read | Gets the case investigations |
> | Åtgärd | Microsoft.SecurityInsights/cases/investigations/write | Updates the metadata of a case |
> | Åtgärd | Microsoft.SecurityInsights/cases/read | Gets a case |
> | Åtgärd | Microsoft.SecurityInsights/cases/write | Updates a case |
> | Åtgärd | Microsoft.SecurityInsights/dataConnectors/delete | Deletes a data connector |
> | Åtgärd | Microsoft.SecurityInsights/dataConnectors/read | Gets the data connectors |
> | Åtgärd | Microsoft.SecurityInsights/dataConnectors/write | Updates a data connector |
> | Åtgärd | Microsoft.SecurityInsights/register/action | Registers the subscription to Azure Sentinel |
> | Åtgärd | Microsoft.SecurityInsights/settings/read | Gets settings |
> | Åtgärd | Microsoft.SecurityInsights/settings/write | Inställningar för uppdateringar |
> | Åtgärd | Microsoft.SecurityInsights/unregister/action | Unregisters the subscription from Azure Sentinel |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ServiceBus/checkNameAvailability/action | Checks availability of namespace under given subscription. |
> | Åtgärd | Microsoft.ServiceBus/checkNamespaceAvailability/action | Checks availability of namespace under given subscription. This API is deprecated please use CheckNameAvailability instead. |
> | Åtgärd | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Deletes the VNet rules in ServiceBus Resource Provider for the specified VNet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/action | Updates Namespace Authorization Rule. This API is deprecated. Please use a PUT call to update the Namespace Authorization Rule instead.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Delete Namespace Authorization Rule. The Default Namespace Authorization Rule cannot be deleted.  |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Get the Connection String to the Namespace |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/read | Get the list of Namespaces Authorization Rules description. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/write | Create a Namespace level Authorization Rules and update its properties. The Authorization Rules Access Rights, the Primary and Secondary Keys can be updated. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/Delete | Delete Namespace Resource |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Gets the authorization rules keys for the Disaster Recovery primary namespace |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Get Disaster Recovery Primary Namespace's Authorization Rules |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Disables Disaster Recovery and stops replicating changes from primary to secondary namespaces. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Checks availability of namespace alias under given subscription. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Deletes the Disaster Recovery configuration associated with the namespace. This operation can only be invoked via the primary namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Invokes a GEO DR failover and reconfigures the namespace alias to point to the secondary namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Gets the Disaster Recovery configuration associated with the namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Creates or Updates the Disaster Recovery configuration associated with the namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Deletes the Event Grid filter associated with the namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Gets the Event Grid filter associated with the namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Creates or Updates the Event Grid filter associated with the namespace. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventhubs/read | Get list of EventHub Resource Descriptions |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Delete IP Filter Resource |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Get IP Filter Resource |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Create IP Filter Resource |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/receive/action | Ta emot meddelanden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/send/action | Skicka meddelanden |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/read | Gets the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/write | Updates the Messaging Plan for a namespace.<br>This API is deprecated.<br>Properties exposed via the MessagingPlan resource are moved to the (parent) Namespace resource in later API versions..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrate/action | Migrate namespace operation |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Deletes the Migration configuration. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Gets the Migration configuration which indicates the state of the migration and pending replication operations |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Reverts the standard to premium namespace migration |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Assigns the DNS associated with the standard namespace to the premium namespace which completes the migration and stops the syncing resources from standard to premium namespace |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Creates or Updates Migration configuration. This will start synchronizing resources from the standard to the premium namespace |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkruleset/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkruleset/read | Gets NetworkRuleSet Resource |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkruleset/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/read | Gets NetworkRuleSet Resource |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/operationresults/read | Get the status of Namespace operation |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Get list of Namespace diagnostic settings Resource Descriptions |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Get list of Namespace diagnostic settings Resource Descriptions |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Get list of Namespace logs Resource Descriptions |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Get list of Namespace metrics Resource Descriptions |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Operation to update Queue. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Operation to delete Queue Authorization Rules |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Get the Connection String to Queue |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Get the list of Queue Authorization Rules |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Create Queue Authorization Rules and Update its properties. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/Delete | Operation to delete Queue Resource |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/read | Hämta lista med beskrivningar av köade resurser |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/write | Skapa eller uppdatera köegenskaper. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/read | Hämta listan över Beskrivning av namn områdes resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnrymd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Åtgärd att uppdatera ämne. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för ämnen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Hämta anslutnings strängen till ämnet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Hämta listan med auktoriseringsregler för ämnen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Skapa auktoriseringsregler för ämnen och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/ta bort | Åtgärd för att ta bort ämnes resurs |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/läsa | Hämta lista över beskrivningar av ämnes resurser |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/Delete | Åtgärd för att ta bort TopicSubscription-resurs |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/läsa | Hämta lista över beskrivningar av TopicSubscription-resurser |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/regler/ta bort | Åtgärd för att ta bort regel resurs |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/regler/läsa | Hämta lista med beskrivningar av regel resurser |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/regler/Skriv | Skapa eller uppdatera regel egenskaper. |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/skriva | Skapa eller uppdatera TopicSubscription-egenskaper. |
> | Åtgärd | Microsoft. Service Bus/namnrymder/ämnen/Skriv | Egenskaper för att skapa eller uppdatera ämnen. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Hämtar regel resurs för VNET |
> | Åtgärd | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/write | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft. Service Bus/register/åtgärd | Registrerar prenumerationen för Service Bus Resource Provider och gör det möjligt att skapa Service Bus-resurser |
> | Åtgärd | Microsoft.ServiceBus/sku/read | Hämta lista med resurs beskrivningar för SKU |
> | Åtgärd | Microsoft.ServiceBus/sku/regions/read | Hämta lista över beskrivningar av SkuRegions-resurser |
> | Åtgärd | Microsoft.ServiceBus/unregister/action | Registrerar prenumerationen för Service Bus Resource Provider och gör det möjligt att skapa Service Bus-resurser |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/ta bort | Ta bort alla program |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/read | Läs valfritt program |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/tjänster/ta bort | Ta bort alla tjänster |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/tjänster/partitioner/läsa | Läs alla partitioner |
> | Åtgärd | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Läs valfri replik |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/tjänster/läsa | Läs valfri tjänst |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/tjänster/status/läsning | Läs vilken tjänst status som helst |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/tjänster/skrivning | Skapa eller uppdatera en tjänst |
> | Åtgärd | Microsoft. ServiceFabric/kluster/program/Skriv | Skapa eller uppdatera valfritt program |
> | Åtgärd | Microsoft. ServiceFabric/kluster/applicationTypes/Delete | Ta bort valfri program typ |
> | Åtgärd | Microsoft. ServiceFabric/kluster/applicationTypes/Read | Läs valfri program typ |
> | Åtgärd | Microsoft. ServiceFabric/Clusters/applicationTypes/versions/Delete | Ta bort valfri version av program typ |
> | Åtgärd | Microsoft. ServiceFabric/Clusters/applicationTypes/versions/Read | Läs valfri version av program typ |
> | Åtgärd | Microsoft. ServiceFabric/Clusters/applicationTypes/versions/Write | Skapa eller uppdatera valfri version av program typ |
> | Åtgärd | Microsoft. ServiceFabric/kluster/applicationTypes/Write | Skapa eller uppdatera valfri program typ |
> | Åtgärd | Microsoft. ServiceFabric/kluster/ta bort | Ta bort alla kluster |
> | Åtgärd | Microsoft.ServiceFabric/clusters/nodes/read | Läs valfri nod |
> | Åtgärd | Microsoft. ServiceFabric/kluster/läsa | Läs valfritt kluster |
> | Åtgärd | Microsoft. ServiceFabric/kluster/status/läsning | Läs vilken kluster status som helst |
> | Åtgärd | Microsoft.ServiceFabric/clusters/write | Skapa eller uppdatera valfritt kluster |
> | Åtgärd | Microsoft.ServiceFabric/locations/clusterVersions/read | Läs valfri kluster version |
> | Åtgärd | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Läs valfri kluster version för en speciell miljö |
> | Åtgärd | Microsoft.ServiceFabric/locations/operationresults/read | Läs eventuella åtgärds resultat |
> | Åtgärd | Microsoft.ServiceFabric/locations/operations/read | Läs alla åtgärder efter plats |
> | Åtgärd | Microsoft.ServiceFabric/operations/read | Läs alla tillgängliga åtgärder |
> | Åtgärd | Microsoft. ServiceFabric/register/åtgärd | Registrera en åtgärd |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. SignalRService/locations/checknameavailability/Action | Kontrollerar om ett namn är tillgängligt för användning med en ny signal tjänst |
> | Åtgärd | Microsoft. SignalRService/locations/operationresults/signalerer/Read | Fråga efter status för en asynkron åtgärd |
> | Åtgärd | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Fråga efter status för en asynkron åtgärd |
> | Åtgärd | Microsoft. SignalRService/platser/användning/läsa | Hämta kvot användningarna för Azure SignalR service |
> | Åtgärd | Microsoft. SignalRService/operationresults/Read | Fråga efter status för en asynkron åtgärd |
> | Åtgärd | Microsoft. SignalRService/Operations/Read | Visa en lista över åtgärder för Azure SignalR-tjänsten. |
> | Åtgärd | Microsoft. SignalRService/operationstatus/Read | Fråga efter status för en asynkron åtgärd |
> | Åtgärd | Microsoft. SignalRService/register/åtgärd | Registrerar resurs leverantören "Microsoft. SignalRService" med en prenumeration |
> | Åtgärd | Microsoft. SignalRService/SignalR/Delete | Ta bort hela signal tjänsten |
> | Åtgärd | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Ta bort ett event Grid-filter från en signaler. |
> | Åtgärd | Microsoft.SignalRService/SignalR/eventGridFilters/read | Hämta egenskaperna för det angivna Event Grid-filtret eller visar alla Event Grid-filter för den angivna Signaleraren. |
> | Åtgärd | Microsoft.SignalRService/SignalR/eventGridFilters/write | Skapa eller uppdatera ett event Grid-filter för en SignalR med de angivna parametrarna. |
> | Åtgärd | Microsoft. SignalRService/SignalR/listnycklar/åtgärd | Visa värdet för SignalR åtkomst nycklar i hanterings portalen eller via API |
> | Åtgärd | Microsoft. SignalRService/SignalR/privateEndpointConnectionProxies/Delete | Ta bort en proxy för anslutning till privat slutpunkt |
> | Åtgärd | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/read | Read a Private Endpoint Connetion Proxy |
> | Åtgärd | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/validate/action | Validate a Private Endpoint Connection Proxy |
> | Åtgärd | Microsoft.SignalRService/SignalR/privateEndpointConnectionProxies/write | Create a Private Endpoint Connection Proxy |
> | Åtgärd | Microsoft.SignalRService/SignalR/privateEndpointConnections/read | Read a Private Endpoint Connection |
> | Åtgärd | Microsoft.SignalRService/SignalR/privateEndpointConnections/write | Approve or reject a Private Endpoint Connection |
> | Åtgärd | Microsoft.SignalRService/SignalR/privateLinkResources/read | List all SignalR Private Link Resources |
> | Åtgärd | Microsoft.SignalRService/SignalR/read | View the SignalR's settings and configurations in the management portal or through API |
> | Åtgärd | Microsoft.SignalRService/SignalR/regeneratekey/action | Change the value of SignalR access keys in the management portal or through API |
> | Åtgärd | Microsoft.SignalRService/SignalR/restart/action | To restart an Azure SignalR service in the management portal or through API. There will be certain downtime. |
> | Åtgärd | Microsoft.SignalRService/SignalR/write | Modify the SignalR's settings and configurations in the management portal or through API |
> | Åtgärd | Microsoft.SignalRService/unregister/action | Unregisters the 'Microsoft.SignalRService' resource provider with a subscription |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Lists application artifacts of application definition. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/delete | Removes an application definition. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/read | Retrieves a list of application definitions. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/write | Add or modify an application definition. |
> | Åtgärd | Microsoft.Solutions/applications/applicationArtifacts/read | Lists application artifacts. |
> | Åtgärd | Microsoft.Solutions/applications/delete | Removes an application. |
> | Åtgärd | Microsoft.Solutions/applications/read | Retrieves a list of applications. |
> | Åtgärd | Microsoft.Solutions/applications/refreshPermissions/action | Refreshes application permission(s). |
> | Åtgärd | Microsoft.Solutions/applications/updateAccess/action | Updates application access. |
> | Åtgärd | Microsoft.Solutions/applications/write | Skapar ett program. |
> | Åtgärd | Microsoft.Solutions/jitRequests/delete | Remove a JitRequest |
> | Åtgärd | Microsoft.Solutions/jitRequests/read | Retrieves a list of JitRequests |
> | Åtgärd | Microsoft.Solutions/jitRequests/write | Creates a JitRequest |
> | Åtgärd | Microsoft.Solutions/locations/operationStatuses/read | Reads the operation status for the resource. |
> | Åtgärd | Microsoft.Solutions/operations/read | Gets the list of operations. |
> | Åtgärd | Microsoft.Solutions/register/action | Register to Solutions. |
> | Åtgärd | Microsoft.Solutions/unregister/action | Unregisters from Solutions. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Sql/checkNameAvailability/action | Verify whether given server name is available for provisioning worldwide for a given subscription. |
> | Åtgärd | Microsoft.Sql/instancePools/delete | Deletes an instance pool |
> | Åtgärd | Microsoft.Sql/instancePools/read | Gets an instance pool |
> | Åtgärd | Microsoft.Sql/instancePools/usages/read | Gets an instance pool's usage info |
> | Åtgärd | Microsoft.Sql/instancePools/write | Creates or updates an instance pool |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Retrieve result of the extended server blob auditing policy Set operation |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Retrieve result of the server blob auditing policy Set operation |
> | Åtgärd | Microsoft.Sql/locations/capabilities/read | Gets the capabilities for this subscription in a given location |
> | Åtgärd | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Gets the status of a database operation. |
> | Åtgärd | Microsoft.Sql/locations/databaseOperationResults/read | Gets the status of a database operation. |
> | Åtgärd | Microsoft.Sql/locations/deletedServerAsyncOperation/read | Gets in-progress operations on deleted server |
> | Åtgärd | Microsoft.Sql/locations/deletedServerOperationResults/read | Gets in-progress operations on deleted server |
> | Åtgärd | Microsoft.Sql/locations/deletedServers/read | Return the list of deleted servers or gets the properties for the specified deleted server. |
> | Åtgärd | Microsoft.Sql/locations/deletedServers/recover/action | Recover a deleted server |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Gets the azure async operation for an elastic pool async operation |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolOperationResults/read | Gets the result of an elastic pool operation. |
> | Åtgärd | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption encryption protector |
> | Åtgärd | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Gets in-progress operations on transparent data encryption encryption protector |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Retrieve result of the extended server blob auditing policy Set operation |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Retrieve result of the extended server blob auditing policy Set operation |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Gets the status of a firewall rule operation. |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesOperationResults/read | Gets the status of a firewall rule operation. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/delete | Deletes an existing instance failover group. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Executes planned failover in an existing instance failover group. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Executes forced failover in an existing instance failover group. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/read | Returns the list of instance failover groups or gets the properties for the specified instance failover group. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/write | Creates an instance failover group with the specified parameters or updates the properties or tags for the specified instance failover group. |
> | Åtgärd | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Gets the status of an instance pool operation |
> | Åtgärd | Microsoft.Sql/locations/instancePoolOperationResults/read | Gets the result for an instance pool operation |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Returns the details of a specific interface endpoint Azure async operation |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Returns the details of the specified interface endpoint profile operation |
> | Åtgärd | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Gets the status of an job agent operation. |
> | Åtgärd | Microsoft.Sql/locations/jobAgentOperationResults/read | Gets the result of an job agent operation. |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionBackups/read | Lists the long term retention backups for every database on every server in a location |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Lists the long term retention backups for every database on a server |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Deletes a long term retention backup |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Lists the long term retention backups for a database |
> | Åtgärd | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Slutför återställnings åtgärden för hanterade databaser |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption managed instance encryption protector |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Gets in-progress operations on transparent data encryption managed instance encryption protector |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Gets in-progress operations on transparent data encryption managed instance keys |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Gets in-progress operations on transparent data encryption managed instance keys |
> | Åtgärd | Microsoft. SQL/locations/managedInstanceLongTermRetentionPolicyAzureAsyncOperation/Read | Hämtar statusen för en långsiktig bevarande princip åtgärd för en hanterad databas |
> | Åtgärd | Microsoft. SQL/locations/managedInstanceLongTermRetentionPolicyOperationResults/Read | Hämtar statusen för en långsiktig bevarande princip åtgärd för en hanterad databas |
> | Åtgärd | Microsoft. SQL/locations/managedShortTermRetentionPolicyOperationResults/Read | Hämtar status för en kortsiktig bevarande princip åtgärd |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering i hanterad databas |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering i hanterad databas |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. SQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. SQL/locations/Read | Hämtar tillgängliga platser för en specifik prenumeration |
> | Åtgärd | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft.Sql/locations/serverKeyOperationResults/read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft. SQL/locations/shortTermRetentionPolicyOperationResults/Read | Hämtar status för en kortsiktig bevarande princip åtgärd |
> | Åtgärd | Microsoft.Sql/locations/syncAgentOperationResults/read | Hämta resultatet av resurs åtgärden för Sync-agenten |
> | Åtgärd | Microsoft.Sql/locations/syncDatabaseIds/read | Hämta Synkronisera databas-ID: n för en viss region och prenumeration |
> | Åtgärd | Microsoft.Sql/locations/syncGroupOperationResults/read | Hämta resultatet av resurs åtgärden för Sync-gruppen |
> | Åtgärd | Microsoft.Sql/locations/syncMemberOperationResults/read | Hämta resultatet av resurs åtgärden för synkronisera medlem |
> | Åtgärd | Microsoft. SQL/locations/usages/Read | Hämtar en samling användnings statistik för den här prenumerationen på en plats |
> | Åtgärd | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Returnerar information om de angivna virtuella nätverks reglerna Azure async operation  |
> | Åtgärd | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Returnerar information om den angivna åtgärden för virtuella nätverks regler  |
> | Åtgärd | Microsoft. SQL/managedInstances/administrators/Delete | Tar bort en befintlig administratör för den hanterade instansen. |
> | Åtgärd | Microsoft. SQL/managedInstances/administrators/Read | Hämtar en lista över hanterade instans administratörer. |
> | Åtgärd | Microsoft. SQL/managedInstances/administrators/Write | Skapar eller uppdaterar hanterad instans administratör med de angivna parametrarna. |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/backupLongTermRetentionPolicies/Read | Hämtar en långsiktig bevarande princip för en hanterad databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/backupLongTermRetentionPolicies/Write | Uppdaterar en långsiktig bevarande princip för en hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en hanterad databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/kolumner/läsa | Returnera en lista med kolumner för en hanterad databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/completeRestore/åtgärd | Slutför återställnings åtgärden för hanterade databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | List sensitivity labels of a given database |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Batch update sensitivity labels |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/Delete | Tar bort en befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/providers/Microsoft. Insights/diagnosticSettings/Write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instans databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/read | Hämtar befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | List sensitivity labels of a given database |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/recommendedSensitivityLabels/Write | Batch update recommended sensitivity labels |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/restoreDetails/Read | Returnerar information om återställningen av hanterade databaser medan återställningen pågår. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/read | Hämta ett hanterat databas schema. (endast schema) |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Hämta en hanterad databas-kolumn (endast schema) |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Delete the sensitivity label of a given column |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Disable sensitivity recommendations on a given column |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Enable sensitivity recommendations on a given column |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Get the sensitivity label of a given column |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Create or update the sensitivity label of a given column |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Hämta en hanterad databas tabell (endast schema) |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/Read | Hämta en lista över principer för hot identifiering för hanterade databaser som kon figurer ATS för en angiven server |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/Write | Ändra principen för databas hot identifiering för en specifik hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityEvents/read | Hämtar säkerhets händelser för hanterade databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | List sensitivity labels of a given database |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Hämta information om databasen transparent datakryptering på en specifik hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Ändra databas transparent datakryptering för en specifik hanterad databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Delete | Remove the vulnerability assessment for a given database |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Retrieve the vulnerability assessment policies on a givendatabase |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Remove the vulnerability assessment rule baseline for a given database |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Get the vulnerability assessment rule baseline for a given database |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Rules/planal/Write | Change the vulnerability assessment rule baseline for a given database |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Convert an existing scan result to a human readable format. If already exists nothing happens |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Execute vulnerability assessment database scan. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Return the list of database vulnerability assessment scan records or get the scan record for the specified scan ID. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/write | Change the vulnerability assessment for a given database |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/write | Creates a new database or updates an existing database. |
> | Åtgärd | Microsoft.Sql/managedInstances/delete | Deletes an existing  managed instance. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/read | Returns a list of server encryption protectors or gets the properties for the specified server encryption protector. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/revalidate/action | Update the properties for the specified Server Encryption Protector. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/write | Update the properties for the specified Server Encryption Protector. |
> | Åtgärd | Microsoft.Sql/managedInstances/inaccessibleManagedDatabases/read | Gets a list of inaccessible managed databases in a managed instance |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/delete | Deletes an existing Azure SQL Managed Instance  key. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/read | Return the list of managed instance keys or gets the properties for the specified managed instance key. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/write | Creates a key with the specified parameters or update the properties or tags for the specified managed instance key. |
> | Åtgärd | Microsoft.Sql/managedInstances/metricDefinitions/read | Get managed instance metric definitions |
> | Åtgärd | Microsoft.Sql/managedInstances/metrics/read | Get managed instance metrics |
> | Åtgärd | Microsoft.Sql/managedInstances/operations/cancel/action | Cancels Azure SQL Managed Instance pending asynchronous operation that is not finished yet. |
> | Åtgärd | Microsoft.Sql/managedInstances/operations/read | Get managed instance operations |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for managed instances |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for managed instances |
> | Åtgärd | Microsoft.Sql/managedInstances/read | Return the list of managed instances or gets the properties for the specified managed instance. |
> | Åtgärd | Microsoft.Sql/managedInstances/recoverableDatabases/read | Returns a list of recoverable managed databases |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Gets a short term retention policy for a dropped managed database |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Updates a short term retention policy for a dropped managed database |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Returns a list of restorable dropped managed databases. |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/read | Retrieve a list of managed server threat detection policies configured for a given server |
> | Åtgärd | Microsoft.Sql/managedInstances/securityAlertPolicies/write | Change the managed server threat detection policy for a given managed server |
> | Åtgärd | Microsoft.Sql/managedInstances/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Åtgärd | Microsoft.Sql/managedInstances/vulnerabilityAssessments/delete | Remove the vulnerability assessment for a given managed instance |
> | Åtgärd | Microsoft.Sql/managedInstances/vulnerabilityAssessments/read | Retrieve the vulnerability assessment policies on a given managed instance |
> | Åtgärd | Microsoft.Sql/managedInstances/vulnerabilityAssessments/write | Change the vulnerability assessment for a given managed instance |
> | Åtgärd | Microsoft.Sql/managedInstances/write | Creates a managed instance with the specified parameters or update the properties or tags for the specified managed instance. |
> | Åtgärd | Microsoft.Sql/operations/read | Gets available REST operations |
> | Åtgärd | Microsoft.Sql/privateEndpointConnectionsApproval/action | Determines if user is allowed to approve a private endpoint connection |
> | Åtgärd | Microsoft.Sql/register/action | Registers the subscription for the Microsoft SQL Database resource provider and enables the creation of Microsoft SQL Databases. |
> | Åtgärd | Microsoft.Sql/servers/administrators/delete | Deletes a specific Azure Active Directory administrator object |
> | Åtgärd | Microsoft.Sql/servers/administrators/read | Gets a specific Azure Active Directory administrator object |
> | Åtgärd | Microsoft.Sql/servers/administrators/write | Adds or updates a specific Azure Active Directory administrator object |
> | Åtgärd | Microsoft.Sql/servers/advisors/read | Returns list of advisors available for the server |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/read | Returns list of recommended actions of specified advisor for the server |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/write | Apply the recommended action on the server |
> | Åtgärd | Microsoft.Sql/servers/advisors/write | Updates auto-execute status of an advisor on server level. |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/read | Retrieve details of the default server table auditing policy configured on a given server |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/write | Change the default server table auditing for a given server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/operationResults/read | Retrieve result of the server blob auditing policy Set operation |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/read | Retrieve details of the server blob auditing policy configured on a given server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/write | Change the server blob auditing for a given server |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/read | Returns automatic tuning settings for the server |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/write | Updates automatic tuning settings for the server and returns updated settings |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Deletes an existing backup archival vault. |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | This operation is used to get a backup long term retention vault. It returns information about the vault registered to this server |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | This operation is used to register a backup long term retention vault to a server |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/delete | Deletes an existing server communication link. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/read | Return the list of communication links of a specified server. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/write | Create or update a server communication link. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/read | Return the list of server connection policies of a specified server. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/write | Create or update a server connection policy. |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/read | Returns list of advisors available for the database |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Returns list of recommended actions of specified advisor for the database |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Apply the recommended action on the database |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/write | Update auto-execute status of an advisor on database level. |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/read | Retrieve details of the table auditing policy configured on a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/write | Change the table auditing policy for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/read | Retrieve details of the blob auditing policy configured on a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/write | Change the blob auditing policy for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/auditRecords/read | Retrieve the database blob audit records |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/read | Returns automatic tuning settings for a database |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/write | Updates automatic tuning settings for a database and returns updated settings |
> | Åtgärd | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Gets the status of a database operation. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Return the list of backup archival policies of a specified database. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Create or update a database backup archival policy. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Gets a short term retention policy for a database |
> | Åtgärd | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Updates a short term retention policy for a database |
> | Åtgärd | Microsoft.Sql/servers/databases/columns/read | Return a list of columns for a database |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/read | Retrieve details of the connection policy configured on a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/write | Change connection policy for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | List sensitivity labels of a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Batch update sensitivity labels |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Return the list of database data masking policies. |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Delete data masking policy rule for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Retrieve details of the data masking policy rule configured on a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Change data masking policy rule for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Change data masking policy for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Returns the distributed query step information of data warehouse query for selected step ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Returns the data warehouse distribution query information for selected query ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Retrieves the user activities of a SQL Data Warehouse instance which includes running and suspended queries |
> | Åtgärd | Microsoft.Sql/servers/databases/delete | Deletes an existing database. |
> | Åtgärd | Microsoft.Sql/servers/databases/export/action | Export Azure SQL Database |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Retrieve details of the extended blob auditing policy configured on a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Change the extended blob auditing policy for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/read | Gets a collection of extensions for the database. |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/write | Change the extension for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/failover/action | Customer initiated database failover. |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/read | Retrieve geo backup policies for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/geoBackupPolicies/write | Create or update a database geobackup policy |
> | Åtgärd | Microsoft.Sql/servers/databases/importExportOperationResults/read | Gets in-progress import/export operations |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Gets a list of available maintenance windows for a selected database. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/read | Gets maintenance windows settings for a selected database. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/write | Sets maintenance windows settings for a selected database. |
> | Åtgärd | Microsoft.Sql/servers/databases/metricDefinitions/read | Return types of metrics that are available for databases |
> | Åtgärd | Microsoft.Sql/servers/databases/metrics/read | Return metrics for databases |
> | Åtgärd | Microsoft.Sql/servers/databases/move/action | Change the name of an existing database. |
> | Åtgärd | Microsoft.Sql/servers/databases/operationResults/read | Gets the status of a database operation. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/cancel/action | Cancels Azure SQL Database pending asynchronous operation that is not finished yet. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/read | Return the list of operations performed on the database |
> | Åtgärd | Microsoft.Sql/servers/databases/pause/action | Pause Azure SQL Datawarehouse Database |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for databases |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for databases |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Returns the collection of query texts that correspond to the specified parameters. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/read | Returns current values of Query Store settings for the database. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/write | Updates Query Store setting for the database |
> | Åtgärd | Microsoft.Sql/servers/databases/read | Return the list of databases or gets the properties for the specified database. |
> | Åtgärd | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | List sensitivity labels of a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/write | Batch update recommended sensitivity labels |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/delete | Terminate the replication relationship forcefully and with potential data loss |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/failover/action | Failover after synchronizing all changes from the primary, making this database into the replication relationship\u0027s primary and making the remote primary into a secondary |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Failover immediately with potential data loss, making this database into the replication relationship\u0027s primary and making the remote primary into a secondary |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/read | Returnera listan över länkar för replikering eller hämta egenskaperna för de angivna replikeringslänken. |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/unlink/action | Terminate the replication relationship forcefully or after synchronizing with the partner |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Update replication mode for link to synchronous or asynchronous mode |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/action | Creates a new restore point |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/delete | Deletes a restore point for the database. |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/read | Returns restore points for the database. |
> | Åtgärd | Microsoft.Sql/servers/databases/resume/action | Resume Azure SQL Datawarehouse Database |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/read | Get a database schema (schema only). |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Get a database column (schema only). |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Delete the sensitivity label of a given column |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Disable sensitivity recommendations on a given column |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Enable sensitivity recommendations on a given column |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Get the sensitivity label of a given column |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Create or update the sensitivity label of a given column |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/read | Get a database table (schema only). |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Retrieve list of index recommendations on a database |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Apply index recommendation |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/read | Retrieve a list of database threat detection policies configured for a given server |
> | Åtgärd | Microsoft.Sql/servers/databases/securityAlertPolicies/write | Change the database threat detection policy for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/securityMetrics/read | Gets a collection of database security metrics |
> | Åtgärd | Microsoft.Sql/servers/databases/sensitivityLabels/read | List sensitivity labels of a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Return suggestion about scaling database up or down based on query execution statistics to improve performance or reduce cost |
> | Åtgärd | Microsoft.Sql/servers/databases/skus/read | Gets a collection of skus available for a database |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Cancel sync group synchronization |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/delete | Deletes an existing sync group. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Return the list of sync hub database schemas |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/logs/read | Return the list of sync group logs |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/read | Return the list of sync groups or gets the properties for the specified sync group. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Refresh sync hub database schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Retrieve result of the sync hub schema refresh operation |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Deletes an existing sync member. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Return the list of sync members or gets the properties for the specified sync member. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchema/action | Refresh sync member schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Retrieve result of the sync member schema refresh operation |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Return the list of sync member database schemas |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Creates a sync member with the specified parameters or update the properties for the specified sync member. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Trigger sync group synchronization |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/write | Creates a sync group with the specified parameters or update the properties for the specified sync group. |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/queryText/action | Returns the Transact-SQL text for selected query ID |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/read | Returns aggregated runtime statistics for selected query in selected time period |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/statistics/read | Returns aggregated runtime statistics for selected query in selected time period |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Gets in-progress operations on transparent data encryption |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Retrieve status and details of transparent data encryption security feature for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Change transparent data encryption state |
> | Åtgärd | Microsoft.Sql/servers/databases/upgradeDataWarehouse/action | Upgrade Azure SQL Datawarehouse Database |
> | Åtgärd | Microsoft.Sql/servers/databases/usages/read | Gets the Azure SQL Database usages information |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/delete | Remove the vulnerability assessment for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/read | Retrieve the vulnerability assessment policies on a givendatabase |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/delete | Remove the vulnerability assessment rule baseline for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/read | Get the vulnerability assessment rule baseline for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/rules/baselines/write | Change the vulnerability assessment rule baseline for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Convert an existing scan result to a human readable format. If already exists nothing happens |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Execute vulnerability assessment database scan. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/read | Return the list of database vulnerability assessment scan records or get the scan record for the specified scan ID. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/write | Change the vulnerability assessment for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Execute vulnerability assessment database scan. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Retrieve the result of the database vulnerability assessment scan Execute operation |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Retrieve details of the vulnerability assessment configured on a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/write | Change the vulnerability assessment for a given database |
> | Åtgärd | Microsoft.Sql/servers/databases/workloadGroups/delete | Drops a specific workload group. |
> | Åtgärd | Microsoft.Sql/servers/databases/workloadGroups/read | Lists the workload groups for a selected database. |
> | Åtgärd | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/delete | Drops a specific workload classifier. |
> | Åtgärd | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/read | Lists the workload classifiers for a selected database. |
> | Åtgärd | Microsoft.Sql/servers/databases/workloadGroups/workloadClassifiers/write | Sets the properties for a specific workload classifier. |
> | Åtgärd | Microsoft.Sql/servers/databases/workloadGroups/write | Sets the properties for a specific workload group. |
> | Åtgärd | Microsoft.Sql/servers/databases/write | Creates a database with the specified parameters or update the properties or tags for the specified database. |
> | Åtgärd | Microsoft.Sql/servers/delete | Deletes an existing server. |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Deletes an existing disaster recovery configurations for a given server |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/failover/action | Failover a DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Force Failover a DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Gets a collection of disaster recovery configurations that include this server |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Change server disaster recovery configuration |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/read | Returns list of elastic pool estimates already created for this server |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/write | Creates new elastic pool estimate for list of databases provided |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/read | Returns list of advisors available for the elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Returns list of recommended actions of specified advisor for the elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Apply the recommended action on the elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/write | Update auto-execute status of an advisor on elastic pool level. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/databases/read | Gets a list of databases for an elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/delete | Delete existing elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Retrieve activities and details on a given elastic database pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Retrieve activities and details on a given database that is part of elastic database pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/failover/action | Customer initiated elastic pool failover. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Return types of metrics that are available for elastic database pools |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metrics/read | Return metrics for elastic database pools |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Cancels Azure SQL elastic pool pending asynchronous operation that is not finished yet. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/read | Return the list of operations performed on the elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Gets the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Creates or updates the diagnostic setting for the resource |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for elastic database pools |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/read | Retrieve details of elastic pool on a given server |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/skus/read | Gets a collection of skus available for an elastic pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/write | Create a new or change properties of existing elastic pool |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/read | Returns a list of server encryption protectors or gets the properties for the specified server encryption protector. |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/revalidate/action | Update the properties for the specified Server Encryption Protector. |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/write | Update the properties for the specified Server Encryption Protector. |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/read | Retrieve details of the extended server blob auditing policy configured on a given server |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/write | Change the extended server blob auditing for a given server |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/delete | Deletes an existing failover group. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/failover/action | Executes planned failover in an existing failover group. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Executes forced failover in an existing failover group. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/read | Returns the list of failover groups or gets the properties for the specified failover group. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/write | Creates a failover group with the specified parameters or updates the properties or tags for the specified failover group. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/delete | Deletes an existing server firewall rule. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/read | Return the list of server firewall rules or gets the properties for the specified server firewall rule. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/write | Creates a server firewall rule with the specified parameters, update the properties for the specified rule or overwrite all existing rules with new server firewall rule(s). |
> | Åtgärd | Microsoft.Sql/servers/import/action | Create a new database on the server and deploy schema and data from a DacPac package |
> | Åtgärd | Microsoft.Sql/servers/importExportOperationResults/read | Gets in-progress import/export operations |
> | Åtgärd | Microsoft.Sql/servers/inaccessibleDatabases/read | Return a list of inaccessible database(s) in a logical server. |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Deletes the specified interface endpoint profile |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Returns the properties for the specified interface endpoint profile |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Creates a interface endpoint profile with the specified parameters or updates the properties or tags for the specified interface endpoint |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/delete | Deletes an Azure SQL DB job agent |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/read | Gets an Azure SQL DB job agent |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/write | Creates or updates an Azure SQL DB job agent |
> | Åtgärd | Microsoft.Sql/servers/keys/delete | Deletes an existing server key. |
> | Åtgärd | Microsoft.Sql/servers/keys/read | Return the list of server keys or gets the properties for the specified server key. |
> | Åtgärd | Microsoft.Sql/servers/keys/write | Creates a key with the specified parameters or update the properties or tags for the specified server key. |
> | Åtgärd | Microsoft.Sql/servers/operationResults/read | Gets in-progress server operations |
> | Åtgärd | Microsoft.Sql/servers/operations/read | Return the list of operations performed on the server |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/delete | Deletes an existing private endpoint connection proxy |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Returns the list of private endpoint connection proxies or gets the properties for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Validates a private endpoint connection create call from NRP side |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Creates a private endpoint connection proxy with the specified parameters or updates the properties or tags for the specified private endpoint connection proxy. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/delete | Deletes an existing private endpoint connection |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/read | Returns the list of private endpoint connections or gets the properties for the specified private endpoint connection. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/write | Approves or rejects an existing private endpoint connection |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionsApproval/action | Determines if user is allowed to approve a private endpoint connection |
> | Åtgärd | Microsoft.Sql/servers/privateLinkResources/read | Get the private link resources for the corresponding sql server |
> | Åtgärd | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Return types of metrics that are available for servers |
> | Åtgärd | Microsoft.Sql/servers/read | Return the list of servers or gets the properties for the specified server. |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Retrieve metrics for recommended elastic database pools for a given server |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/read | Retrieve recommendation for elastic database pools to reduce cost or improve performance based on historical resource utilization |
> | Åtgärd | Microsoft.Sql/servers/recoverableDatabases/read | This operation is used for disaster recovery of live database to restore database to last-known good backup point. It returns information about the last good backup but it doesn\u0027t actually restore the database. |
> | Åtgärd | Microsoft.Sql/servers/replicationLinks/read | Returnera listan över länkar för replikering eller hämta egenskaperna för de angivna replikeringslänken. |
> | Åtgärd | Microsoft.Sql/servers/restorableDroppedDatabases/read | Hämta en lista över databaser som har släppts på en specifik server som fortfarande ligger inom bevarande principen. |
> | Åtgärd | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Hämta resultat från princip Skriv åtgärd för Server hot identifiering |
> | Åtgärd | Microsoft. SQL/Servers/securityAlertPolicies/Read | Hämta en lista över Server hot identifierings principer som kon figurer ATS för en angiven server |
> | Åtgärd | Microsoft. SQL/Servers/securityAlertPolicies/Write | Ändra principen för Server hot identifiering för en specifik server |
> | Åtgärd | Microsoft.Sql/servers/serviceObjectives/read | Hämta en lista över service nivå mål (även kallade prestanda nivåer) som är tillgängliga på en viss server |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/delete | Tar bort en befintlig Sync-agent. |
> | Åtgärd | Microsoft. SQL/Servers/syncAgents/generateKey/Action | Generera registrerings nyckel för Sync-agent |
> | Åtgärd | Microsoft. SQL/Servers/syncAgents/linkedDatabases/Read | Returnera listan över länkade databaser för Sync-agenten |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/read | Returnera listan över Sync-agenter eller hämta egenskaperna för den angivna Sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/syncAgents/write | Skapar en Sync-agent med de angivna parametrarna eller uppdaterar egenskaperna för den angivna Sync-agenten. |
> | Åtgärd | Microsoft.Sql/servers/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Åtgärd | Microsoft.Sql/servers/usages/read | Returnera serverns DTU-kvot och aktuell DTU-förbrukning av alla databaser på servern |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft.Sql/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Åtgärd | Microsoft. SQL/Servers/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik server |
> | Åtgärd | Microsoft. SQL/Servers/vulnerabilityAssessments/Read | Hämta principerna för sårbarhets bedömning på en specifik server |
> | Åtgärd | Microsoft. SQL/Servers/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik server |
> | Åtgärd | Microsoft. SQL/Servers/Write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/unregister/action | Avregistrerar prenumerationen för Microsoft SQL Database Resource Provider och gör det möjligt att skapa Microsoft SQL-databaser. |
> | Åtgärd | Microsoft. SQL/virtualClusters/Delete | Tar bort ett befintligt virtuellt kluster. |
> | Åtgärd | Microsoft. SQL/virtualClusters/Read | Returnera listan över virtuella kluster eller hämtar egenskaperna för det angivna virtuella klustret. |
> | Åtgärd | Microsoft.Sql/virtualClusters/write | Uppdaterar taggar för virtuella kluster. |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. SqlVirtualMachine/locations/availabilityGroupListenerOperationResults/Read | Hämta resultatet av en lyssnar funktion för tillgänglighets grupp |
> | Åtgärd | Microsoft. SqlVirtualMachine/locations/sqlVirtualMachineGroupOperationResults/Read | Hämta resultatet av en virtuell SQL-dator grupp åtgärd |
> | Åtgärd | Microsoft. SqlVirtualMachine/locations/sqlVirtualMachineOperationResults/Read | Hämta resultatet av åtgärden för virtuell SQL-dator |
> | Åtgärd | Microsoft. SqlVirtualMachine/Operations/Read |  |
> | Åtgärd | Microsoft. SqlVirtualMachine/register/åtgärd | Registrera prenumeration med Microsoft. SqlVirtualMachine Resource Provider |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Delete | Ta bort befintlig tillgänglighets grupps lyssnare |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Read | Hämta information om SQL tillgänglighets gruppens lyssnare på en specifik virtuell dator grupp i SQL |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/availabilityGroupListeners/Write | Skapa en ny eller ändra egenskaper för befintlig SQL tillgänglighets grupps lyssnare |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Delete | Ta bort befintlig virtuell SQL-dator grupp |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Read | Hämta information om virtuell SQL-dator grupp |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/sqlVirtualMachines/Read | Visa en lista över SQL-virtuella datorer med en viss virtuell SQL-dator grupp |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachineGroups/Write | Skapa en ny eller ändra egenskaper för befintlig virtuell SQL-dator grupp |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Delete | Ta bort befintlig virtuell SQL-dator |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Read | Hämta information om virtuell SQL-dator |
> | Åtgärd | Microsoft. SqlVirtualMachine/sqlVirtualMachines/Write | Skapa en ny eller ändra egenskaper för befintlig virtuell SQL-dator |
> | Åtgärd | Microsoft. SqlVirtualMachine/avregistrera/åtgärd | Avregistrera prenumeration med Microsoft. SqlVirtualMachine Resource Provider |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Storage/checknameavailability/Read | Kontrollerar att konto namnet är giltigt och inte används. |
> | Åtgärd | Microsoft. Storage/locations/checknameavailability/Read | Kontrollerar att konto namnet är giltigt och inte används. |
> | Åtgärd | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft. Storage att det virtuella nätverket eller under nätet tas bort |
> | Åtgärd | Microsoft. Storage/locations/usages/Read | Returns the limit and the current usage count for resources in the specified subscription |
> | Åtgärd | Microsoft. Storage/Operations/Read | Avsöker statusen för en asynkron åtgärd. |
> | Åtgärd | Microsoft. Storage/register/åtgärd | Registrerar prenumerationen för lagrings resurs leverantören och gör det möjligt att skapa lagrings konton. |
> | Åtgärd | Microsoft.Storage/skus/read | Visar en lista över SKU: er som stöds av Microsoft. Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Returnerar resultatet av att lägga till BLOB-innehåll |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en BLOB |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/deleteBlobVersion/åtgärd | Returns the result of deleting a blob version |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Returns the list of blobs under an account with matching tags filter |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returns a blob or a list of blobs |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Returns the result of the blob command |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Returns the result of reading blob tags |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Returns the result of writing blob tags |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returns the result of writing a blob |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Clear blob container legal hold |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returns the result of deleting a container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Delete blob container immutability policy |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Extend blob container immutability policy |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Lock blob container immutability policy |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Get blob container immutability policy |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Put blob container immutability policy |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Returns the result of leasing blob container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returns a container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returns list of containers |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Set blob container legal hold |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returns the result of patch blob container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returns the result of put blob container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returns a user delegation key for the blob service |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read | Returns blob service properties or statistics |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/write | Returns the result of put blob service properties |
> | Åtgärd | Microsoft.Storage/storageAccounts/delete | Deletes an existing storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/encryptionScopes/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/encryptionScopes/write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/failover/action | Customer is able to control the failover in case of availability issues |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Get File Admin Privileges |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Returns the result of deleting a file/folder |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Returns the result of modifying permission on a file/folder |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Returns a file/folder or a list of files/folders |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Returns the result of writing a file or creating a folder |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/read | Get file service properties |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/shares/delete |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/shares/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/shares/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/shares/write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/listAccountSas/action | Returns the Account SAS token for the specified storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listkeys/action | Returns the access keys for the specified storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listServiceSas/action | Returns the Service SAS token for the specified storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/delete | Delete storage account management policies |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/read | Get storage management account policies |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/write | Put storage account management policies |
> | Åtgärd | Microsoft.Storage/storageAccounts/objectReplicationPolicies/delete |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/objectReplicationPolicies/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/objectReplicationPolicies/write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/delete | Delete Private Endpoint Connection Proxies |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/read | Get Private Endpoint Connection Proxy |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnectionProxies/write | Put Private Endpoint Connection Proxies |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/delete | Delete Private Endpoint Connection |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Get Private Endpoint Connection |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Put Private Endpoint Connection |
> | Åtgärd | Microsoft.Storage/storageAccounts/PrivateEndpointConnectionsApproval/action | Godkänn anslutningar för privata slut punkter |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateLinkResources/read | Get StorageAccount groupids |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returns the result of deleting a queue |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Returns the result of adding a message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Returns the result of deleting a message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Returns the result of processing a message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returns a message |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Returns the result of writing a message |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returns a queue or a list of queues. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returns the result of writing a queue |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Get Queue service properties |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Returns queue service properties or statistics. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/write | Returns the result of setting queue service properties |
> | Åtgärd | Microsoft.Storage/storageAccounts/read | Returns the list of storage accounts or gets the properties for the specified storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/regeneratekey/action | Regenerates the access keys for the specified storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Restore blob ranges to the state of the specified time |
> | Åtgärd | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Revokes all the user delegation keys for the specified storage account. |
> | Åtgärd | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Create/Update storage account diagnostic settings. |
> | Åtgärd | Microsoft.Storage/storageAccounts/tableServices/read | Get Table service properties |
> | Åtgärd | Microsoft.Storage/storageAccounts/write | Creates a storage account with the specified parameters or update the properties or tags or adds custom domain for the specified storage account. |
> | Åtgärd | Microsoft.Storage/usages/read | Returns the limit and the current usage count for resources in the specified subscription |

## <a name="microsoftstoragesync"></a>microsoft.storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | microsoft.storagesync/locations/checkNameAvailability/action | Checks that storage sync service name is valid and is not in use. |
> | Åtgärd | microsoft.storagesync/locations/workflows/operations/read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | microsoft.storagesync/operations/read | Gets a list of the Supported Operations |
> | Åtgärd | microsoft.storagesync/register/action | Registers the subscription for the Storage Sync Provider |
> | Åtgärd | microsoft.storagesync/storageSyncServices/delete | Delete any Storage Sync Services |
> | Åtgärd | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Storage Sync Services |
> | Åtgärd | microsoft.storagesync/storageSyncServices/read | Read any Storage Sync Services |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/delete | Delete any Registered Server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for Registered Server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/read | Read any Registered Server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/write | Create or Update any Registered Server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Ta bort alla moln slut punkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hämtar status för en asynkron säkerhets kopiering/återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Anropa den här åtgärden efter säkerhets kopiering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Anropa den här åtgärden efter återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Anropa den här åtgärden innan du säkerhetskopierar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Anropa den här åtgärden innan du återställer |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Läs eventuella slut punkter i molnet |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Återställ pulsslag |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/syncGroups/cloudEndpoints/triggerChangeDetection/åtgärd | Anropa den här åtgärden för att utlösa identifiering av ändringar i en moln slut punkts fil resurs |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Skapa eller uppdatera eventuella moln slut punkter |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/syncGroups/Delete | Ta bort eventuella Sync-grupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Sync-grupper |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/read | Läs alla Sync-grupper |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Delete | Ta bort alla Server slut punkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Server slut punkter |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Read | Läs alla Server slut punkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Anropa den här åtgärden för att återställa filer till en server |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Write | Skapa eller uppdatera alla Server slut punkter |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/syncGroups/Write | Skapa eller uppdatera alla Sync-grupper |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/arbets flöden/operationresults/Read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/arbets flöden/åtgärder/Läs | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/arbets flöden/läsa | Läs arbets flöden |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/Write | Skapa eller uppdatera lagrings tjänster för synkronisering |
> | Åtgärd | Microsoft. storagesync/avregistrera/åtgärd | Avregistrerar prenumerationen för Storage Sync-providern |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/delete | Tar bort Access Control poster |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/read | Visar eller hämtar Access Control poster |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/write | Skapa eller uppdatera Access Control poster |
> | Åtgärd | Microsoft.StorSimple/managers/alerts/read | Visar eller hämtar aviseringarna |
> | Åtgärd | Microsoft. StorSimple/chefer/säkerhets kopieringar/läsa | Listar eller hämtar säkerhets kopian |
> | Åtgärd | Microsoft. StorSimple/chefer/bandwidthSettings/Delete | Tar bort en befintlig bandbredds inställning (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/bandwidthSettings/Read | Visa en lista över bandbredds inställningar (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/bandwidthSettings/Write | Skapar en ny eller uppdaterar bandbredds inställningar (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/certificates/write | Skapa eller uppdatera certifikaten |
> | Åtgärd | Microsoft.StorSimple/Managers/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Åtgärd | Microsoft.StorSimple/managers/clearAlerts/action | Rensa alla aviseringar som är associerade med enhets hanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Visa en lista över konfigurationer som stöds av moln installationen |
> | Åtgärd | Microsoft. StorSimple/chefer/configureDevice/åtgärd | Konfigurerar en enhet |
> | Åtgärd | Microsoft. StorSimple/chefer/Delete | Tar bort enhets hanterare |
> | Åtgärd | Microsoft. StorSimple/chefer/Delete | Åtgärden ta bort valv tar bort den angivna Azure-resursen av typen valv |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/read | Visar eller hämtar aviserings inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/write | Skapa eller uppdatera aviserings inställningarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/authorizeForServiceEncryptionKeyRollover/Action | Auktorisera för förnyelse av tjänst krypterings nyckel för enheter |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/säkerhets kopiering/åtgärd | Gör en manuell säkerhets kopiering för att skapa en säkerhets kopiering på begäran av alla volymer som skyddas av principen. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/Delete | Tar bort ett befintligt säkerhets kopierings principer (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/operationResults/Read | List the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/Read | Lista över säkerhets kopierings principer (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Tar bort ett befintligt schema |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/schema/Read | Lista scheman |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/schema/skrivning | Skapar ett nytt schema eller uppdateringar |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/Write | Skapar en ny eller uppdaterar säkerhets kopierings principer (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/Delete | Tar bort säkerhets kopian |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/Elements/Clone/åtgärd | Klona en resurs eller volym med hjälp av ett säkerhets kopierings element. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/operationResults/Read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/Read | Listar eller hämtar säkerhets kopian |
> | Åtgärd | Microsoft. StorSimple/hanterare/enheter/säkerhets kopiering/återställning/åtgärd | Återställ alla volymer från en säkerhets kopia. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Delete | Tar bort schema grupper för säkerhets kopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Read | Listar eller hämtar schema grupper för säkerhets kopiering |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Write | Skapa eller uppdatera schema grupper för säkerhets kopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/delete | Tar bort CHAP-inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/read | Visar eller hämtar CHAP-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/write | Skapa eller uppdatera CHAP-inställningarna |
> | Åtgärd | Microsoft. StorSimple/hanterare/enheter/inaktivera/åtgärd | Inaktiverar en enhet. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Delete | Tar bort enheterna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/disks/Read | Listar eller hämtar diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/hämtning/åtgärd | Hämta uppdateringar för en enhet. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/redundans/åtgärd | Enhetens redundans. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/redundans/operationResults/Read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/failoverTargets/Read | Visar eller hämtar enheternas Redundansrelationer |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/säkerhets kopiering/åtgärd | Gör en säkerhets kopia av en fil server. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Delete | Tar bort fil servrarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Metrics/Read | Lists or gets the Metrics |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Lists or gets the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Read | Lists or gets the File Servers |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Tar bort resurserna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Lists or gets the Metrics |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Lists or gets the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Lists or gets the Shares |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Shares/Write | Skapa eller uppdatera resurserna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Write | Skapa eller uppdatera fil servrarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Ändra styrenhetens energi tillstånd för maskin varu komponent grupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Visa en lista över maskin varu komponent grupper |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/install/åtgärd | Installera uppdateringar på en enhet. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/installUpdates/Action | Installerar uppdateringar på enheterna (endast 8000-serien). |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/säkerhets kopiering/åtgärd | Gör en säkerhets kopia av en iSCSI-server. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Delete | Tar bort iSCSI-servrarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Delete | Tar bort diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Metrics/Read | Lists or gets the Metrics |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/metricsDefinitions/Read | Lists or gets the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Read | Listar eller hämtar diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Write | Skapa eller uppdatera diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Metrics/Read | Lists or gets the Metrics |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Lists or gets the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Read | Lists or gets the iSCSI Servers |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Write | Skapa eller uppdatera iSCSI-servrarna |
> | Åtgärd | Microsoft. StorSimple/hanterare/enheter/jobb/Avbryt/åtgärd | Avbryta ett pågående jobb |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Jobs/operationResults/Read | List the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Jobs/Read | Lists or gets the Jobs |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/listFailoverSets/Action | Visa en lista över redundansväxlingen för en befintlig enhet (endast 8000-serien). |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/listFailoverTargets/Action | Visa en lista över redundansväxlingen för enheterna (endast 8000-serien). |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Metrics/Read | Lists or gets the Metrics |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/metricsDefinitions/Read | Lists or gets the Metrics Definitions |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/confirmMigration/åtgärd | Bekräftar en lyckad migrering och genomför den. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/confirmMigrationStatus/Read | Ange status för bekräfta migrering |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/åtgärd | Hämta bekräftelse status för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Hämtar status för jobbet för migrerings uppskattning. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/fetchMigrationStatus/åtgärd | Hämta status för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importera käll konfiguration för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Lista uppskattning av migrering |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/migrationStatus/Read | Lista status för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/startMigration/åtgärd | Starta migrering med käll konfiguration |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/startMigrationEstimate/åtgärd | Starta ett jobb för att uppskatta varaktigheten för migreringsprocessen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/read | Visar eller hämtar nätverks inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/write | Skapar en ny eller uppdaterar nätverks inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/publicEncryptionKey/Action | Visa en lista över den offentliga krypterings nyckeln för enhets hanteraren |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/publishSupportPackage/Action | Publicera support paketet för en befintlig enhet. Ett StorSimple-stödpaket är en lättanvänd mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med fel sökning av problem med StorSimple-enheter. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Read | Visar eller hämtar enheterna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/scanForUpdates/Action | Söker efter uppdateringar på en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/securitySettings/Read | Lista säkerhets inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synkronisera fjärrhanterings certifikatet för en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Uppdatera säkerhets inställningarna. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/write | Creates a new or updates Security Settings |
> | Åtgärd | Microsoft.StorSimple/managers/devices/sendTestAlertEmail/action | Send test alert email to configured email recipients. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/shares/read | Lists or gets the Shares |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/read | Lists or gets the Time Settings |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/write | Creates a new or updates Time Settings |
> | Åtgärd | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/updateSummary/read | Lists or gets the Update Summary |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/delete | Deletes an existing Volume Containers (8000 Series Only) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/metrics/read | List the Metrics |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/metricsDefinitions/read | List the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/read | List the Volume Containers (8000 Series Only) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/delete | Deletes an existing Volumes |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metrics/read | List the Metrics |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | List the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | List the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/read | List the Volumes |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/write | Creates a new or updates Volumes |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/write | Creates a new or updates Volume Containers (8000 Series Only) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumes/read | List the Volumes |
> | Åtgärd | Microsoft.StorSimple/managers/devices/write | Create or update the Devices |
> | Åtgärd | Microsoft.StorSimple/managers/encryptionSettings/read | Lists or gets the Encryption Settings |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/delete | Deletes the Extended Vault Information |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/delete | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/read | Lists or gets the Extended Vault Information |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/read | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/write | Create or update the Extended Vault Information |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/write | The Get Extended Info operation gets an object's Extended Info representing the Azure resource of type ?vault? |
> | Åtgärd | Microsoft.StorSimple/managers/features/read | List the Features |
> | Åtgärd | Microsoft.StorSimple/managers/fileservers/read | Lists or gets the File Servers |
> | Åtgärd | Microsoft.StorSimple/managers/getEncryptionKey/action | Get encryption key for the device manager. |
> | Åtgärd | Microsoft.StorSimple/managers/iscsiservers/read | Lists or gets the iSCSI Servers |
> | Åtgärd | Microsoft.StorSimple/managers/jobs/read | Lists or gets the Jobs |
> | Åtgärd | Microsoft.StorSimple/managers/listActivationKey/action | Gets the activation key of the StorSimple Device Manager. |
> | Åtgärd | Microsoft.StorSimple/managers/listPublicEncryptionKey/action | List public encryption keys of a StorSimple Device Manager. |
> | Åtgärd | Microsoft.StorSimple/managers/metrics/read | Lists or gets the Metrics |
> | Åtgärd | Microsoft.StorSimple/managers/metricsDefinitions/read | Lists or gets the Metrics Definitions |
> | Åtgärd | Microsoft.StorSimple/managers/migrateClassicToResourceManager/action | Migrate from Classic To Resource Manager |
> | Åtgärd | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | List the Migration Source Configurations (8000 Series Only) |
> | Åtgärd | Microsoft.StorSimple/managers/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/provisionCloudAppliance/action | Create a new cloud appliance. |
> | Åtgärd | Microsoft.StorSimple/managers/read | Lists or gets the Device Managers |
> | Åtgärd | Microsoft.StorSimple/Managers/read | The Get Vault operation gets an object representing the Azure resource of type 'vault' |
> | Åtgärd | Microsoft.StorSimple/managers/regenerateActivationKey/action | Regenerate the Activation key for an existing StorSimple Device Manager. |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Deletes the Storage Account Credentials |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/read | Lists or gets the Storage Account Credentials |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/write | Create or update the Storage Account Credentials |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/delete | Deletes the Storage Domains |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Lists or gets the Operation Results |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/read | Lists or gets the Storage Domains |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/write | Create or update the Storage Domains |
> | Åtgärd | Microsoft.StorSimple/managers/write | Create or update the Device Managers |
> | Åtgärd | Microsoft.StorSimple/Managers/write | Create Vault operation creates an Azure resource of type 'vault' |
> | Åtgärd | Microsoft.StorSimple/operations/read | Lists or gets the Operations |
> | Åtgärd | Microsoft.StorSimple/register/action | Register Provider Microsoft.StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.StreamAnalytics/locations/quotas/Read | Read Stream Analytics Subscription Quota |
> | Åtgärd | Microsoft.StreamAnalytics/operations/Read | Read Stream Analytics Operations |
> | Åtgärd | Microsoft.StreamAnalytics/Register/action | Register subscription with Stream Analytics Resource Provider |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Delete | Delete Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Delete | Delete Stream Analytics Job Function |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/operationresults/Read | Read operation results for Stream Analytics Job Function |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Read | Read Stream Analytics Job Function |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/RetrieveDefaultDefinition/action | Retrieve Default Definition of a Stream Analytics Job Function |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Test/action | Test Stream Analytics Job Function |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/functions/Write | Write Stream Analytics Job Function |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Delete | Delete Stream Analytics Job Input |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/operationresults/Read | Read operation results for Stream Analytics Job Input |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Read | Read Stream Analytics Job Input |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Sample/action | Sample Stream Analytics Job Input |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Test/action | Test Stream Analytics Job Input |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/inputs/Write | Write Stream Analytics Job Input |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/metricdefinitions/Read | Read Metric Definitions |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Read operation results for Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Delete | Delete Stream Analytics Job Output |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Read operation results for Stream Analytics Job Output |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Read | Read Stream Analytics Job Output |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Test/action | Test Stream Analytics Job Output |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/Write | Write Stream Analytics Job Output |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read | Read diagnostic setting. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write | Write diagnostic setting. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Gets the available logs for streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Gets the available metrics for streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/PublishEdgePackage/action | Publish edge package for Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Read | Read Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Scale/action | Scale Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Start/action | Starta Stream Analytics-jobb |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Stop/action | Stop Stream Analytics Job |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Delete | Delete Stream Analytics Job Transformation |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Read | Read Stream Analytics Job Transformation |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/transformations/Write | Write Stream Analytics Job Transformation |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Write | Write Stream Analytics Job |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Subscription/cancel/action | Cancels the Subscription |
> | Åtgärd | Microsoft.Subscription/CreateSubscription/action | Skapa en Azure-prenumeration |
> | Åtgärd | Microsoft.Subscription/register/action | Registers Subscription with Microsoft.Subscription resource provider |
> | Åtgärd | Microsoft.Subscription/rename/action | Renames the subscription |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/read | Get an Azure subscription definition within a management group. |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/write | Create an Azure subscription definition |

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
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Deletes the access policy. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Get the properties of an access policy. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Creates a new access policy for an environment, or updates an existing access policy. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/delete | Deletes the environment. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Deletes the event source. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/read | Get the properties of an event source. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/write | Creates a new event source for an environment, or updates an existing event source. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/read | Get the properties of an environment. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Deletes the reference data set. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Get the properties of a reference data set. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Creates a new reference data set for an environment, or updates an existing reference data set. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/status/read | Get the status of the environment, state of its associated operations like ingress. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/write | Creates a new environment, or updates an existing environment. |
> | Åtgärd | Microsoft.TimeSeriesInsights/register/action | Registers the subscription for the Time Series Insights resource provider and enables the creation of Time Series Insights environments. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.VisualStudio/Account/Delete | Ta bort konto |
> | Åtgärd | Microsoft.VisualStudio/Account/Extension/Read | Read Account/Extension |
> | Åtgärd | Microsoft.VisualStudio/Account/Project/Read | Read Account/Project |
> | Åtgärd | Microsoft.VisualStudio/Account/Project/Write | Set Account/Project |
> | Åtgärd | Microsoft.VisualStudio/Account/Read | Read Account |
> | Åtgärd | Microsoft.VisualStudio/Account/Write | Set Account |
> | Åtgärd | Microsoft.VisualStudio/Extension/Delete | Delete Extension |
> | Åtgärd | Microsoft.VisualStudio/Extension/Read | Read Extension |
> | Åtgärd | Microsoft.VisualStudio/Extension/Write | Set Extension |
> | Åtgärd | Microsoft.VisualStudio/Project/Delete | Delete Project |
> | Åtgärd | Microsoft.VisualStudio/Project/Read | Read Project |
> | Åtgärd | Microsoft.VisualStudio/Project/Write | Set Project |
> | Åtgärd | Microsoft.VisualStudio/Register/Action | Register Azure Subscription with Microsoft.VisualStudio provider |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | microsoft.web/apimanagementaccounts/apiacls/read | Get Api Management Accounts Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Delete Api Management Accounts APIs Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/read | Get Api Management Accounts APIs Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/write | Update Api Management Accounts APIs Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Get Api Management Accounts APIs Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Confirm Consent Code Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Delete Api Management Accounts APIs Connections Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Get Api Management Accounts APIs Connections Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Update Api Management Accounts APIs Connections Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/delete | Delete Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Get Consent Links for Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | List Connection Keys Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | List Secrets Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/read | Get Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/write | Update Api Management Accounts APIs Connections. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/delete | Delete Api Management Accounts APIs. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Delete Api Management Accounts APIs Localized Definitions. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Get Api Management Accounts APIs Localized Definitions. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Update Api Management Accounts APIs Localized Definitions. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/read | Get Api Management Accounts APIs. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/write | Update Api Management Accounts APIs. |
> | Åtgärd | microsoft.web/apimanagementaccounts/connectionacls/read | Get Api Management Accounts Connectionacls. |
> | Åtgärd | microsoft.web/availablestacks/read | Get Available Stacks. |
> | Åtgärd | Microsoft.Web/certificates/Delete | Delete an existing certificate. |
> | Åtgärd | Microsoft.Web/certificates/Read | Get the list of certificates. |
> | Åtgärd | Microsoft.Web/certificates/Write | Add a new certificate or update an existing one. |
> | Åtgärd | microsoft.web/checknameavailability/read | Check if resource name is available. |
> | Åtgärd | microsoft.web/classicmobileservices/read | Get Classic Mobile Services. |
> | Åtgärd | Microsoft.Web/connectionGateways/Delete | Deletes a Connection Gateway. |
> | Åtgärd | Microsoft.Web/connectionGateways/Join/Action | Joins a Connection Gateway. |
> | Åtgärd | Microsoft.Web/connectionGateways/ListStatus/Action | Lists status of a Connection Gateway. |
> | Åtgärd | Microsoft.Web/connectionGateways/Move/Action | Moves a Connection Gateway. |
> | Åtgärd | Microsoft.Web/connectionGateways/Read | Get the list of Connection Gateways. |
> | Åtgärd | Microsoft.Web/connectionGateways/Write | Creates or updates a Connection Gateway. |
> | Åtgärd | microsoft.web/connections/confirmconsentcode/action | Confirm Connections Consent Code. |
> | Åtgärd | Microsoft.Web/connections/Delete | Deletes a Connection. |
> | Åtgärd | Microsoft.Web/connections/Join/Action | Joins a Connection. |
> | Åtgärd | microsoft.web/connections/listconsentlinks/action | List Consent Links for Connections. |
> | Åtgärd | Microsoft.Web/connections/Move/Action | Moves a Connection. |
> | Åtgärd | Microsoft.Web/connections/Read | Get the list of Connections. |
> | Åtgärd | Microsoft.Web/connections/Write | Creates or updates a Connection. |
> | Åtgärd | Microsoft.Web/customApis/Delete | Deletes a Custom API. |
> | Åtgärd | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extracts API definition from a WSDL. |
> | Åtgärd | Microsoft.Web/customApis/Join/Action | Joins a Custom API. |
> | Åtgärd | Microsoft.Web/customApis/listWsdlInterfaces/Action | Lists WSDL interfaces for a Custom API. |
> | Åtgärd | Microsoft.Web/customApis/Move/Action | Moves a Custom API. |
> | Åtgärd | Microsoft.Web/customApis/Read | Get the list of Custom API. |
> | Åtgärd | Microsoft.Web/customApis/Write | Creates or updates a Custom API. |
> | Åtgärd | Microsoft.Web/deletedSites/Read | Get the properties of a Deleted Web App |
> | Åtgärd | microsoft.web/deploymentlocations/read | Get Deployment Locations. |
> | Åtgärd | Microsoft.Web/geoRegions/Read | Get the list of Geo regions. |
> | Åtgärd | microsoft.web/hostingenvironments/capacities/read | Get Hosting Environments Capacities. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Delete | Delete an App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/detectors/read | Get Hosting Environments Detectors. |
> | Åtgärd | microsoft.web/hostingenvironments/diagnostics/read | Get Hosting Environments Diagnostics. |
> | Åtgärd | microsoft.web/hostingenvironments/inboundnetworkdependenciesendpoints/read | Get the network endpoints of all inbound dependencies. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Join/Action | Joins an App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/metricdefinitions/read | Get Hosting Environments Metric Definitions. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/metricdefinitions/read | Get Hosting Environments MultiRole Pools Metric Definitions. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/metrics/read | Get Hosting Environments MultiRole Pools Metrics. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Get the properties of a FrontEnd Pool in an App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/skus/read | Get Hosting Environments MultiRole Pools SKUs. |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/usages/read | Get Hosting Environments MultiRole Pools Usages. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Create a new FrontEnd Pool in an App Service Environment or update an existing one |
> | Åtgärd | microsoft.web/hostingenvironments/operations/read | Get Hosting Environments Operations. |
> | Åtgärd | microsoft.web/hostingenvironments/outboundnetworkdependenciesendpoints/read | Get the network endpoints of all outbound dependencies. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Godkänn anslutningar för privata slut punkter |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Read | Get the properties of an App Service Environment |
> | Åtgärd | Microsoft.Web/hostingEnvironments/reboot/Action | Reboot all machines in an App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/resume/action | Resume Hosting Environments. |
> | Åtgärd | microsoft.web/hostingenvironments/serverfarms/read | Get Hosting Environments App Service Plans. |
> | Åtgärd | microsoft.web/hostingenvironments/sites/read | Get Hosting Environments Web Apps. |
> | Åtgärd | microsoft.web/hostingenvironments/suspend/action | Suspend Hosting Environments. |
> | Åtgärd | microsoft.web/hostingenvironments/usages/read | Get Hosting Environments Usages. |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/metricdefinitions/read | Get Hosting Environments Workerpools Metric Definitions. |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/metrics/read | Get Hosting Environments Workerpools Metrics. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/Read | Get the properties of a Worker Pool in an App Service Environment |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/skus/read | Get Hosting Environments Workerpools SKUs. |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/usages/read | Get Hosting Environments Workerpools Usages. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/workerPools/Write | Create a new Worker Pool in an App Service Environment or update an existing one |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Write | Create a new App Service Environment or update existing one |
> | Åtgärd | microsoft.web/ishostingenvironmentnameavailable/read | Get if Hosting Environment Name is available. |
> | Åtgärd | microsoft.web/ishostnameavailable/read | Check if Hostname is Available. |
> | Åtgärd | microsoft.web/isusernameavailable/read | Check if Username is available. |
> | Åtgärd | Microsoft.Web/listSitesAssignedToHostName/Read | Get names of sites assigned to hostname. |
> | Åtgärd | microsoft.web/locations/apioperations/read | Get Locations API Operations. |
> | Åtgärd | microsoft.web/locations/connectiongatewayinstallations/read | Get Locations Connection Gateway Installations. |
> | Åtgärd | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Vnet or subnet deletion notification for Locations. |
> | Åtgärd | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extract Api Definition from WSDL for Locations. |
> | Åtgärd | microsoft.web/locations/listwsdlinterfaces/action | List WSDL Interfaces for Locations. |
> | Åtgärd | microsoft.web/locations/managedapis/apioperations/read | Get Locations Managed API Operations. |
> | Åtgärd | Microsoft.Web/locations/managedapis/Join/Action | Joins a Managed API. |
> | Åtgärd | microsoft.web/locations/managedapis/read | Get Locations Managed APIs. |
> | Åtgärd | microsoft.web/locations/operationResults/read | Get Operations. |
> | Åtgärd | microsoft.web/locations/operations/read | Get Operations. |
> | Åtgärd | microsoft.web/operations/read | Get Operations. |
> | Åtgärd | microsoft.web/publishingusers/read | Get Publishing Users. |
> | Åtgärd | microsoft.web/publishingusers/write | Update Publishing Users. |
> | Åtgärd | Microsoft.Web/recommendations/Read | Get the list of recommendations for subscriptions. |
> | Åtgärd | microsoft.web/register/action | Register Microsoft.Web resource provider for the subscription. |
> | Åtgärd | microsoft.web/resourcehealthmetadata/read | Get Resource Health Metadata. |
> | Åtgärd | microsoft.web/serverfarms/capabilities/read | Get App Service Plans Capabilities. |
> | Åtgärd | Microsoft.Web/serverfarms/Delete | Delete an existing App Service Plan |
> | Åtgärd | Microsoft.Web/serverfarms/eventGridFilters/delete | Delete Event Grid Filter on server farm. |
> | Åtgärd | Microsoft.Web/serverfarms/eventGridFilters/read | Get Event Grid Filter on server farm. |
> | Åtgärd | Microsoft.Web/serverfarms/eventGridFilters/write | Put Event Grid Filter on server farm. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/delete | Delete App Service Plans First Party Apps Settings. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/read | Get App Service Plans First Party Apps Settings. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/write | Uppdatera App Service planerar inställningarna för den första parts-appen. |
> | Åtgärd | Microsoft. Web/Server grupper/hybridconnectionnamespaces/reläs/Delete | Ta bort App Service planer hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Server grupper/hybridconnectionnamespaces/reläs/Read | Hämta App Service planer hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Server grupper/hybridconnectionnamespaces/reläs/Sites/Read | Hämta App Service planer hybrid anslutnings namn områden reläer Web Apps. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Få App Service planer begränsningar för Hybrid anslutnings planen. |
> | Åtgärd | microsoft.web/serverfarms/hybridconnectionrelays/read | Få App Service planer hybrid anslutnings reläer. |
> | Åtgärd | microsoft.web/serverfarms/metricdefinitions/read | Hämta mått definitioner för App Service planer. |
> | Åtgärd | microsoft.web/serverfarms/metrics/read | Hämta mått för App Service planer. |
> | Åtgärd | microsoft.web/serverfarms/operationresults/read | Hämta App Service planer åtgärds resultat. |
> | Åtgärd | Microsoft.Web/serverfarms/Read | Hämta egenskaperna för en App Service plan |
> | Åtgärd | Microsoft.Web/serverfarms/restartSites/Action | Starta om alla Web Apps i en App Service-plan |
> | Åtgärd | microsoft.web/serverfarms/sites/read | Hämta App Services planer Web Apps. |
> | Åtgärd | microsoft.web/serverfarms/skus/read | Hämta App Service planer SKU: er. |
> | Åtgärd | Microsoft. Web/Server grupper/usages/Read | Få App Services Plans användningar. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Uppdatera App Service-planer Virtual Network anslutningar-gatewayer. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/read | Få App Services planer Virtual Network anslutningar. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Ta bort App Service planer Virtual Network anslutnings vägar. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Få App Services planer Virtual Network anslutnings vägar. |
> | Åtgärd | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Uppdatera App Service planer Virtual Network anslutnings vägar. |
> | Åtgärd | Microsoft. Web/Server grupper/Worker/reboot/åtgärd | Starta om App Service planerar arbetare. |
> | Åtgärd | Microsoft. Web/Server grupper/Write | Skapa en ny App Service plan eller uppdatera en befintlig |
> | Åtgärd | Microsoft. Web/Sites/analyzecustomhostname/Read | Analysera anpassat värdnamn. |
> | Åtgärd | Microsoft.Web/sites/applySlotConfig/Action | Använd webb program plats konfigurationen från mål platsen till den aktuella webb programmet |
> | Åtgärd | Microsoft. Web/Sites/backup/åtgärd | Skapa en ny säkerhets kopia av webbapp |
> | Åtgärd | Microsoft. Web/Sites/backup/Read | Hämta Web Apps säkerhets kopiering. |
> | Åtgärd | Microsoft. Web/Sites/backup/Write | Uppdatera Web Apps säkerhets kopiering. |
> | Åtgärd | Microsoft. Web/Sites/säkerhets kopieringar/åtgärd | Identifierar en befintlig säkerhets kopia av appen som kan återställas från en BLOB i Azure Storage. |
> | Åtgärd | Microsoft. Web/Sites/backups/Delete | Ta bort Web Apps säkerhets kopior. |
> | Åtgärd | Microsoft. Web/Sites/backups/List/Action | Visa Web Apps säkerhets kopieringar. |
> | Åtgärd | Microsoft. Web/Sites/backups/Read | Hämta egenskaperna för en webb programs säkerhets kopia |
> | Åtgärd | Microsoft. Web/Sites/backups/Restore/Action | Återställa Web Apps säkerhets kopior. |
> | Åtgärd | Microsoft. Web/Sites/backups/Write | Uppdatera Web Apps säkerhets kopieringar. |
> | Åtgärd | microsoft.web/sites/config/delete | Ta bort Web Apps config. |
> | Åtgärd | Microsoft. Web/Sites/config/List/Action | Visa en lista över säkerhets känsliga inställningar för webbappen, till exempel att publicera autentiseringsuppgifter, appinställningar och anslutnings strängar |
> | Åtgärd | Microsoft.Web/sites/config/Read | Hämta konfigurations inställningar för webb program |
> | Åtgärd | Microsoft. Web/Sites/config/snaps hots/Read | Hämta Web Apps config-ögonblicksbilder. |
> | Åtgärd | Microsoft. Web/Sites/config/Write | Uppdatera konfigurations inställningar för webb program |
> | Åtgärd | microsoft.web/sites/containerlogs/action | Hämta zippade behållar loggar för Web App. |
> | Åtgärd | Microsoft. Web/Sites/containerlogs/hämtning/åtgärd | Ladda ned Web Apps container-loggar. |
> | Åtgärd | Microsoft. Web/Sites/continuouswebjobs/Delete | Ta bort Web Apps kontinuerliga webb jobb. |
> | Åtgärd | Microsoft. Web/Sites/continuouswebjobs/Read | Hämta Web Apps kontinuerliga webb jobb. |
> | Åtgärd | Microsoft. Web/Sites/continuouswebjobs/start/Action | Starta Web Apps kontinuerliga webb jobb. |
> | Åtgärd | Microsoft. Web/Sites/continuouswebjobs/stoppa/åtgärd | Stoppa Web Apps kontinuerliga webb jobb. |
> | Åtgärd | Microsoft.Web/sites/Delete | Ta bort en befintlig webbapp |
> | Åtgärd | Microsoft. Web/Sites/Deployments/Delete | Ta bort Web Apps distributioner. |
> | Åtgärd | Microsoft. Web/Sites/distributioner/log/Read | Hämta Web Apps distributions logg. |
> | Åtgärd | Microsoft. Web/Sites/Deployments/Read | Hämta Web Apps-distributioner. |
> | Åtgärd | Microsoft. Web/Sites/distributioner/Write | Uppdatera Web Apps-distributioner. |
> | Åtgärd | Microsoft. Web/Sites/Detections/Read | Hämta Web Apps identifieringar. |
> | Åtgärd | Microsoft. Web/Sites/diagnostik/analyser/kör/åtgärd | Kör Web Apps Diagnostics-analys. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/analyser/Read | Hämta Web Apps Diagnostics-analys. |
> | Åtgärd | microsoft.web/sites/diagnostics/aspnetcore/read | Hämta Web Apps Diagnostics för ASP.NET Core-appen. |
> | Åtgärd | microsoft.web/sites/diagnostics/autoheal/read | Hämta Web Apps Diagnostics autoläka. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/Deployment/Read | Hämta Web Apps Diagnostics-distribution. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/distribution/Read | Hämta Web Apps Diagnostics-distributioner. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/Detections/EXECUTE/Action | Kör Web Apps Diagnostics detektor. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/Detections/Read | Hämta Web Apps Diagnostics detektor. |
> | Åtgärd | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Hämta Web Apps diagnostik misslyckade förfrågningar per URI. |
> | Åtgärd | microsoft.web/sites/diagnostics/frebanalysis/read | Hämta Web Apps Diagnostics FREB Analysis. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/loganalyzer/Read | Hämta Web Apps Diagnostics Log Analyzer. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/Read | Hämta Web Apps diagnostiska kategorier. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/runtimeavailability/Read | Hämta Web Apps Diagnostics runtime-tillgänglighet. |
> | Åtgärd | microsoft.web/sites/diagnostics/servicehealth/read | Hämta Web Apps Diagnostics Service Health. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/sitecpuanalysis/Read | Hämta CPU-analys för Web Apps Diagnostics site. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/sitecrashes/Read | Hämta Web Apps Diagnostics site kraschar. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/sitelatency/Read | Hämta svars tid för Web Apps Diagnostics. |
> | Åtgärd | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Hämta minnes analys för Web Apps Diagnostics site. |
> | Åtgärd | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Hämta Web Apps Diagnostics site restart-inställningen uppdatera. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/siterestartuserinitiated/Read | Hämta Web Apps Diagnostics site starta om användaren. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/siteswap/Read | Hämta Web Apps Diagnostics site swap. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/ThreadCount/Read | Hämta antal trådar för Web Apps diagnostik. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/workeravailability/Read | Hämta Web Apps Diagnostics Workeravailability. |
> | Åtgärd | Microsoft. Web/Sites/Diagnostics/workerprocessrecycle/Read | Hämta återkallning av arbets process för Web Apps Diagnostics. |
> | Åtgärd | Microsoft. Web/Sites/domainownershipidentifiers/Read | Hämta Web Apps domänens Ägarskaps identifierare. |
> | Åtgärd | Microsoft. Web/Sites/domainownershipidentifiers/Write | Uppdatera Web Apps domän Ägarskaps identifierare. |
> | Åtgärd | Microsoft. Web/Sites/eventGridFilters/Delete | Ta bort Event Grid filter i Web App. |
> | Åtgärd | Microsoft. Web/Sites/eventGridFilters/Read | Hämta Event Grid filter i Web App. |
> | Åtgärd | Microsoft. Web/Sites/eventGridFilters/Write | Lägg Event Grid filter i Web App. |
> | Åtgärd | microsoft.web/sites/extensions/delete | Ta bort Web Apps plats tillägg. |
> | Åtgärd | microsoft.web/sites/extensions/read | Hämta Web Apps webbplats tillägg. |
> | Åtgärd | microsoft.web/sites/extensions/write | Uppdatera Web Apps plats tillägg. |
> | Åtgärd | Microsoft. Web/Sites/Functions/åtgärd | Functions Web Apps. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Delete | Ta bort Web Apps-funktioner. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Keys/Delete | Ta bort funktions nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Keys/Write | Uppdatera funktions nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Functions/listnycklar/Action | Visa funktions nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Functions/listsecrets/Action | Visa en lista över funktions hemligheter. |
> | Åtgärd | Microsoft. Web/Sites/Functions/MasterKey/Read | Hämta Web Apps Functions MasterKey. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Properties/Read | Läs Web Apps Functions-egenskaper. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Properties/Write | Uppdatera Web Apps funktions egenskaper. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Read | Hämta Web Apps-funktioner. |
> | Åtgärd | Microsoft. Web/Sites/Functions/token/Read | Hämta Web Apps Functions-token. |
> | Åtgärd | Microsoft. Web/Sites/Functions/Write | Uppdatera Web Apps-funktioner. |
> | Åtgärd | Microsoft. Web/Sites/Host/functionkeys/Delete | Ta bort funktioner värd funktions nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Host/functionkeys/Write | Uppdatera funktioner värd funktions nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Host/listnycklar/Action | Lista funktions värd nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Host/listsyncstatus/Action | Visar status för funktionen utlösare för synkronisering. |
> | Åtgärd | Microsoft. Web/Sites/Host/Properties/Read | Läs Web Apps funktioner värd egenskaper. |
> | Åtgärd | Microsoft. Web/Sites/Host/Sync/Action | Synkronisera funktions utlösare. |
> | Åtgärd | Microsoft. Web/Sites/Host/systemkeys/Delete | Ta bort funktioner värd system nycklar. |
> | Åtgärd | Microsoft. Web/Sites/Host/systemkeys/Write | Uppdatera funktioner värd system nycklar. |
> | Åtgärd | Microsoft. Web/Sites/hostnamebindings/Delete | Ta bort Web Apps hostname-bindningar. |
> | Åtgärd | Microsoft. Web/Sites/hostnamebindings/Read | Hämta Web Apps hostname-bindningar. |
> | Åtgärd | Microsoft. Web/Sites/hostnamebindings/Write | Uppdatera Web Apps hostname-bindningar. |
> | Åtgärd | microsoft.web/sites/hostruntime/functions/keys/read | Hämta Web Apps funktions nycklar för Hostruntime. |
> | Åtgärd | Microsoft. Web/Sites/hostruntime/Host/_master/Read | Hämta Funktionsapp huvud nyckeln för administratörs åtgärder |
> | Åtgärd | Microsoft. Web/Sites/hostruntime/värd/åtgärd | Utför Funktionsapp körnings åtgärd som Sync-utlösare, Lägg till funktioner, anropa funktioner, ta bort funktioner osv. |
> | Åtgärd | Microsoft. Web/Sites/hostruntime/Host/Read | Hämta Web Apps Hostruntime-värden. |
> | Åtgärd | Microsoft. Web/Sites/resursprovider/Delete | Ta bort Web Apps hybrid anslutning. |
> | Åtgärd | Microsoft. Web/Sites/resursprovider/Read | Hämta Web Apps hybrid anslutning. |
> | Åtgärd | Microsoft. Web/Sites/resursprovider/Write | Uppdatera Web Apps hybrid anslutning. |
> | Åtgärd | Microsoft. Web/Sites/hybridconnectionnamespaces/reläs/Delete | Ta bort Web Apps hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Sites/hybridconnectionnamespaces/relays/listnycklar/Action | Visar en lista över nycklar Web Apps hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Sites/hybridconnectionnamespaces/reläs/Read | Hämta Web Apps hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Sites/hybridconnectionnamespaces/reläs/Write | Uppdatera Web Apps hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Sites/hybridconnectionrelays/Read | Hämta Web Apps hybrid anslutnings reläer. |
> | Åtgärd | Microsoft. Web/Sites/instances/Deployments/Delete | Ta bort Web Apps instans distributioner. |
> | Åtgärd | Microsoft. Web/Sites/instances/Deployments/Read | Hämta Web Apps instans distributioner. |
> | Åtgärd | microsoft.web/sites/instances/extensions/log/read | Hämta Web Apps Instanss Extensions-logg. |
> | Åtgärd | Microsoft. Web/Sites/instances/Extensions/Processes/Read | Hämta Web Apps Instanss Extensions-processer. |
> | Åtgärd | microsoft.web/sites/instances/extensions/read | Hämta Web Apps instans tillägg. |
> | Åtgärd | Microsoft. Web/Sites/instances/processs/Delete | Ta bort Web Apps instans processer. |
> | Åtgärd | Microsoft. Web/Sites/instances/Processes/modules/Read | Hämta Web Apps instanser bearbetar moduler. |
> | Åtgärd | Microsoft. Web/Sites/instances/processs/Read | Hämta Web Apps instans processer. |
> | Åtgärd | Microsoft. Web/Sites/instances/processs/threads/Read | Hämta Web Apps instanser bearbetar trådar. |
> | Åtgärd | Microsoft. Web/Sites/instances/Read | Hämta Web Apps instanser. |
> | Åtgärd | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Lista status för funktionen utlösare för synkronisering. |
> | Åtgärd | microsoft.web/sites/metricdefinitions/read | Hämta Web Apps mått definitioner. |
> | Åtgärd | Microsoft. Web/Sites/Metrics/Read | Hämta Web Apps mått. |
> | Åtgärd | Microsoft. Web/Sites/metricsdefinitions/Read | Hämta Web Apps mått definitioner. |
> | Åtgärd | microsoft.web/sites/migratemysql/action | Migrera MySql-Web Apps. |
> | Åtgärd | microsoft.web/sites/migratemysql/read | Hämta Web Apps migrera MySql. |
> | Åtgärd | microsoft.web/sites/networktrace/action | Web Apps för nätverks spårning. |
> | Åtgärd | microsoft.web/sites/networktraces/operationresults/read | Få Web Apps nätverks spårnings åtgärds resultat. |
> | Åtgärd | Microsoft. Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | Åtgärd | microsoft.web/sites/operationresults/read | Hämta Web Apps åtgärds resultat. |
> | Åtgärd | microsoft.web/sites/operations/read | Hämta Web Apps-åtgärder. |
> | Åtgärd | microsoft.web/sites/perfcounters/read | Hämta Web Apps prestanda räknare. |
> | Åtgärd | microsoft.web/sites/premieraddons/delete | Ta bort Web Apps Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/premieraddons/read | Få Web Apps Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/premieraddons/write | Uppdatera Web Apps Premier-tillägg. |
> | Åtgärd | Microsoft. Web/Sites/privateaccess/Read | Hämta data runt åtkomst till privata webbplatser och auktoriserade virtuella nätverk som har åtkomst till webbplatsen. |
> | Åtgärd | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/Action | Godkänn anslutningar för privata slut punkter |
> | Åtgärd | microsoft.web/sites/processes/modules/read | Hämta moduler för Web Apps processer. |
> | Åtgärd | Microsoft. Web/Sites/Processes/Read | Hämta Web Appss processer. |
> | Åtgärd | Microsoft. Web/Sites/Processes/threads/Read | Hämta Web Apps bearbetar trådar. |
> | Åtgärd | microsoft.web/sites/publiccertificates/delete | Ta bort Web Apps offentliga certifikat. |
> | Åtgärd | microsoft.web/sites/publiccertificates/read | Hämta Web Apps offentliga certifikat. |
> | Åtgärd | microsoft.web/sites/publiccertificates/write | Uppdatera Web Apps offentliga certifikat. |
> | Åtgärd | Microsoft. Web/Sites/Publish/Action | Publicera en webbapp |
> | Åtgärd | Microsoft. Web/Sites/publishxml/Action | Hämta XML för publicerings profil för en webbapp |
> | Åtgärd | microsoft.web/sites/publishxml/read | Hämta Web Apps Publishing XML. |
> | Åtgärd | Microsoft. Web/Sites/Read | Hämta egenskaperna för en webbapp |
> | Åtgärd | Microsoft. Web/Sites/recommendationhistory/Read | Hämta Web Apps rekommendations historik. |
> | Åtgärd | Microsoft. Web/Sites/rekommendationer/inaktivera/åtgärd | Inaktivera Web Apps rekommendationer. |
> | Åtgärd | Microsoft. Web/Sites/rekommendationer/läsa | Hämta listan över rekommendationer för Web App. |
> | Åtgärd | Microsoft. Web/Sites/Recover/Action | Återställ Web Apps. |
> | Åtgärd | Microsoft.Web/sites/resetSlotConfig/Action | Återställ konfiguration av webbapp |
> | Åtgärd | Microsoft. Web/Sites/resourcehealthmetadata/Read | Hämta Web Apps Resource Health metadata. |
> | Åtgärd | Microsoft. Web/Sites/restart/Action | Starta om en webbapp |
> | Åtgärd | microsoft.web/sites/restore/read | Hämta Web Apps Restore. |
> | Åtgärd | Microsoft. Web/Sites/Restore/Write | Återställ Web Apps. |
> | Åtgärd | Microsoft. Web/Sites/restorefrombackupblob/Action | Återställ en webbapp från en backup-blob. |
> | Åtgärd | Microsoft. Web/Sites/restorefromdeletedapp/Action | Återställ Web Apps från den borttagna appen. |
> | Åtgärd | Microsoft. Web/Sites/restoresnapshot/Action | Återställa Web Apps ögonblicks bilder. |
> | Åtgärd | microsoft.web/sites/siteextensions/delete | Ta bort Web Apps plats tillägg. |
> | Åtgärd | microsoft.web/sites/siteextensions/read | Hämta Web Apps webbplats tillägg. |
> | Åtgärd | microsoft.web/sites/siteextensions/write | Uppdatera Web Apps plats tillägg. |
> | Åtgärd | microsoft.web/sites/slots/analyzecustomhostname/read | Hämta Web Apps platser analysera anpassat värdnamn. |
> | Åtgärd | Microsoft.Web/sites/slots/applySlotConfig/Action | Använd webb program plats konfigurationen från mål platsen till den aktuella platsen. |
> | Åtgärd | Microsoft. Web/Sites/platser/säkerhets kopiering/åtgärd | Skapa ny säkerhets kopia av webb program plats. |
> | Åtgärd | microsoft.web/sites/slots/backup/read | Hämta säkerhets kopiering av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/backup/write | Uppdatera säkerhets kopierings Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/platser/säkerhets kopiering/åtgärd | Identifiera säkerhets kopiering av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/backups/delete | Ta bort säkerhets kopior för Web Apps platser. |
> | Åtgärd | Microsoft. webb/platser/platser/säkerhets kopieringar/lista/åtgärd | Visa Web Apps säkerhets kopiering av platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/backups/Read | Hämta egenskaperna för en säkerhets kopia av Web App-platser |
> | Åtgärd | Microsoft. Web/Sites/platser/säkerhets kopiering/återställning/åtgärd | Återställa säkerhets kopior av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/config/delete | Ta bort konfiguration av Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/config/List/Action | Visa en lista med säkerhets känsliga inställningar för webbappens plats, till exempel autentiseringsuppgifter för publicering, appar och anslutnings strängar |
> | Åtgärd | Microsoft. Web/Sites/lotss/config/Read | Hämta konfigurations inställningar för webb program plats |
> | Åtgärd | Microsoft.Web/sites/slots/config/Write | Uppdatera konfigurations inställningar för webb program plats |
> | Åtgärd | microsoft.web/sites/slots/containerlogs/action | Hämta zippade behållar loggar för webb program plats. |
> | Åtgärd | Microsoft. Web/Sites/lotss/containerlogs/hämtning/åtgärd | Hämta behållar loggar för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/continuouswebjobs/delete | Ta bort Web Apps platser kontinuerliga webb jobb. |
> | Åtgärd | microsoft.web/sites/slots/continuouswebjobs/read | Hämta Web Apps platser kontinuerliga webb jobb. |
> | Åtgärd | Microsoft. Web/Sites/lotss/continuouswebjobs/start/Action | Starta Web Apps platser kontinuerliga webb jobb. |
> | Åtgärd | Microsoft. webb/platser/platser/continuouswebjobs/stoppa/åtgärd | Stoppa Web Apps platser kontinuerligt webb jobb. |
> | Åtgärd | Microsoft.Web/sites/slots/Delete | Ta bort en befintlig webbapp |
> | Åtgärd | microsoft.web/sites/slots/deployments/delete | Ta bort distributioner av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/deployments/log/read | Hämta distributions logg för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/deployments/read | Hämta distributioner av Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Deployments/Write | Uppdatera distributioner av Web Apps-platser. |
> | Åtgärd | microsoft.web/sites/slots/detectors/read | Hämta Web Apps platser detektorer. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/analyser/EXECUTE/Action | Kör diagnostik för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/analyser/Read | Hämta diagnos analys för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Hämta Web Apps kortplatser-diagnostik för ASP.NET Core-appen. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/autoheal/read | Hämta Web Apps platser diagnostik reläka. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/Deployment/Read | Hämta diagnostisk distribution av Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/distribution/Read | Hämta diagnostik-distributioner för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/Detections/EXECUTE/Action | Kör Web Apps platser Diagnostics detektor. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/Detections/Read | Hämta Web Apps platsers diagnostiska detektor. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Hämta Web Apps platser diagnostik FREB analys. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/loganalyzer/Read | Hämta Web Apps platser diagnostisk logg analys. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/read | Hämta diagnostik för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/runtimeavailability/Read | Hämta Web Apps platser diagnostisk körnings tillgänglighet. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/servicehealth/Read | Hämta Web Apps platser Diagnostics Service Health. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/sitecpuanalysis/Read | Hämta Web Apps platser diagnostisk webbplats CPU-analys. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/sitecrashes/Read | Hämta Web Apps platser Diagnostics site kraschar. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/sitelatency/Read | Hämta svars tid för webbplats för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Hämta Web Apps platser Diagnostics site Memory Analysis. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Hämta Web Apps platser diagnostisk webbplats starta om uppdatering. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/siterestartuserinitiated/Read | Hämta Web Apps platser diagnostisk webbplats starta om användaren. |
> | Åtgärd | microsoft.web/sites/slots/diagnostics/siteswap/read | Hämta Web Apps platser diagnostiska plats växling. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/ThreadCount/Read | Hämta antal trådar för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/workeravailability/Read | Hämta Web Apps platser Diagnostics Workeravailability. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Diagnostics/workerprocessrecycle/Read | Hämta Web Apps platser Diagnostics Worker-processen återvinns. |
> | Åtgärd | Microsoft. Web/Sites/lotss/domainownershipidentifiers/Read | Hämta Web Apps platser domän Ägarskaps identifierare. |
> | Åtgärd | microsoft.web/sites/slots/functions/listsecrets/action | Lista hemligheter Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Functions/Read | Hämta Web Apps platser-funktioner. |
> | Åtgärd | Microsoft. Web/Sites/lotss/hostnamebindings/Delete | Ta bort bindningar för Web Apps-kortplatser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/hostnamebindings/Read | Hämta Web Apps platser hostname-bindningar. |
> | Åtgärd | Microsoft. Web/Sites/lotss/hostnamebindings/Write | Uppdatera bindningar för Web Apps-kortplatser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/resursprovider/Delete | Ta bort Web Apps platser hybrid anslutning. |
> | Åtgärd | Microsoft. Web/Sites/lotss/resursprovider/Read | Hämta Web Apps platser hybrid anslutning. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnection/write | Uppdatera Web Apps platser hybrid anslutning. |
> | Åtgärd | Microsoft. Web/Sites/lotss/hybridconnectionnamespaces/reläs/Delete | Ta bort Web Apps platser hybrid anslutnings namn rymder reläer. |
> | Åtgärd | Microsoft. Web/Sites/lotss/hybridconnectionnamespaces/reläs/Write | Uppdatera Web Apps platser hybrid anslutnings namn rymder reläer. |
> | Åtgärd | microsoft.web/sites/slots/hybridconnectionrelays/read | Hämta Web Apps platser hybrid anslutnings reläer. |
> | Åtgärd | microsoft.web/sites/slots/instances/deployments/read | Hämta distributioner av instanser av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/instances/processes/delete | Ta bort instans processer för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/instances/processs/Read | Hämta instans processer för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/instances/Read | Hämta instanser av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/metricdefinitions/read | Hämta mått definitioner för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/metrics/read | Hämta mått för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/migratemysql/read | Hämta Web Apps platser migrera MySql. |
> | Åtgärd | microsoft.web/sites/slots/networktrace/action | Nätverks spårning Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/networktraces/operationresults/read | Hämta Web Apps platser nätverks spårnings åtgärds resultat. |
> | Åtgärd | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps-kortplatser. |
> | Åtgärd | microsoft.web/sites/slots/operationresults/read | Hämta Web Apps platser åtgärds resultat. |
> | Åtgärd | microsoft.web/sites/slots/operations/read | Hämta åtgärder för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/perfcounters/read | Hämta prestanda räknare för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/phplogging/read | Hämta Web Apps platser Phplogging. |
> | Åtgärd | microsoft.web/sites/slots/premieraddons/delete | Ta bort Web Apps platser Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/premieraddons/read | Hämta Web Apps platser Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/premieraddons/write | Uppdatera Web Apps platser Premier-tillägg. |
> | Åtgärd | microsoft.web/sites/slots/processes/read | Hämta processer för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/publiccertificates/delete | Ta bort offentliga certifikat för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/publiccertificates/read | Hämta offentliga certifikat för Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/publiccertificates/write | Skapa eller uppdatera offentliga certifikat för Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/platser/publicera/åtgärd | Publicera en webbapp |
> | Åtgärd | Microsoft. Web/Sites/lotss/publishxml/åtgärd | Hämta publicerings profil-XML för webb program plats |
> | Åtgärd | Microsoft. Web/Sites/lots/Read | Hämta egenskaperna för en webb program distributions plats |
> | Åtgärd | Microsoft. Web/Sites/lotss/Recover/Action | Återställa Web Apps platser. |
> | Åtgärd | Microsoft.Web/sites/slots/resetSlotConfig/Action | Återställ webb program plats konfiguration |
> | Åtgärd | Microsoft. Web/Sites/lotss/resourcehealthmetadata/Read | Hämta Web Apps platser Resource Health metadata. |
> | Åtgärd | Microsoft. Web/Sites/lotss/restart/Action | Starta om en webbapp |
> | Åtgärd | microsoft.web/sites/slots/restore/read | Hämta Web Apps platser återställning. |
> | Åtgärd | microsoft.web/sites/slots/restore/write | Återställa Web Apps-kortplatser. |
> | Åtgärd | microsoft.web/sites/slots/restorefrombackupblob/action | Återställ Web Apps-kortplats från backup blob. |
> | Åtgärd | Microsoft. Web/Sites/lotss/restorefromdeletedapp/åtgärd | Återställ webb program platser från den borttagna appen. |
> | Åtgärd | Microsoft. Web/Sites/lotss/restoresnapshot/åtgärd | Återställa ögonblicks bilder av Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/siteextensions/delete | Ta bort Web Apps platser plats tillägg. |
> | Åtgärd | microsoft.web/sites/slots/siteextensions/read | Hämta Web Apps platser plats tillägg. |
> | Åtgärd | microsoft.web/sites/slots/siteextensions/write | Uppdatera Web Apps platser plats tillägg. |
> | Åtgärd | Microsoft. Web/Sites/lotss/slotsdiffs/åtgärd | Få skillnader i konfigurationen mellan webbapp och platser |
> | Åtgärd | Microsoft. Web/Sites/lotss/slotsswap/åtgärd | Växla distributions platser för webbappar |
> | Åtgärd | microsoft.web/sites/slots/snapshots/read | Hämta ögonblicks bilder av Web Apps platser. |
> | Åtgärd | Microsoft. Web/Sites/lotss/sourcecontrols/Delete | Ta bort konfigurations inställningar för webb program platsens käll kontroll |
> | Åtgärd | Microsoft. Web/Sites/lotss/sourcecontrols/Read | Hämta konfigurations inställningar för webb program platsens käll kontroll |
> | Åtgärd | Microsoft. Web/Sites/lotss/sourcecontrols/Write | Uppdatera konfigurations inställningar för webb program platsens käll kontroll |
> | Åtgärd | Microsoft. Web/Sites/lotss/start/Action | Starta en webbapp |
> | Åtgärd | Microsoft. Web/Sites/platser/stoppa/åtgärd | Stoppa en webbapp |
> | Åtgärd | microsoft.web/sites/slots/sync/action | Synkronisera Web Apps platser. |
> | Åtgärd | microsoft.web/sites/slots/triggeredwebjobs/delete | Ta bort Web Apps platser utlösta WebJobs. |
> | Åtgärd | microsoft.web/sites/slots/triggeredwebjobs/read | Hämta Web Apps platser utlöst WebJobs. |
> | Åtgärd | Microsoft. Web/Sites/lotss/triggeredwebjobs/kör/åtgärd | Kör Web Apps platser utlöst WebJobs. |
> | Åtgärd | microsoft.web/sites/slots/usages/read | Hämta Web Apps kort platser. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/delete | Ta bort Web Apps platser Virtual Network anslutningar. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Uppdatera Web Apps platser Virtual Network anslutningar-gatewayer. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/read | Hämta Web Apps platser Virtual Network anslutningar. |
> | Åtgärd | microsoft.web/sites/slots/virtualnetworkconnections/write | Uppdatera Web Apps platser Virtual Network anslutningar. |
> | Åtgärd | microsoft.web/sites/slots/webjobs/read | Hämta Web Apps platser WebJobs. |
> | Åtgärd | Microsoft. Web/Sites/lotss/Write | Skapa en ny webbapp eller uppdatera en befintlig |
> | Åtgärd | Microsoft. Web/Sites/slotsdiffs/Action | Få skillnader i konfigurationen mellan webbapp och platser |
> | Åtgärd | Microsoft. Web/Sites/slotsswap/Action | Växla distributions platser för webbappar |
> | Åtgärd | Microsoft. Web/Sites/snaps hots/Read | Hämta Web Apps ögonblicks bilder. |
> | Åtgärd | Microsoft. Web/Sites/sourcecontrols/Delete | Ta bort konfigurations inställningar för webb programs käll kontroll |
> | Åtgärd | Microsoft. Web/Sites/sourcecontrols/Read | Hämta konfigurations inställningar för webb programs käll kontroll |
> | Åtgärd | Microsoft. Web/Sites/sourcecontrols/Write | Uppdatera konfigurations inställningar för webb programs käll kontroll |
> | Åtgärd | Microsoft. Web/Sites/start/Action | Starta en webbapp |
> | Åtgärd | Microsoft. Web/Sites/stoppa/åtgärd | Stoppa en webbapp |
> | Åtgärd | microsoft.web/sites/sync/action | Synkronisera Web Apps. |
> | Åtgärd | microsoft.web/sites/syncfunctiontriggers/action | Synkronisera funktions utlösare. |
> | Åtgärd | Microsoft. Web/Sites/triggeredwebjobs/Delete | Ta bort Web Apps utlösta WebJobs. |
> | Åtgärd | Microsoft. Web/Sites/triggeredwebjobs/historik/Read | Hämta Web Apps utlösta WebJobs-historik. |
> | Åtgärd | Microsoft. Web/Sites/triggeredwebjobs/Read | Hämta Web Apps utlösta WebJobs. |
> | Åtgärd | Microsoft. Web/Sites/triggeredwebjobs/Run/Action | Kör Web Apps utlösta WebJobs. |
> | Åtgärd | Microsoft. Web/Sites/usages/Read | Få Web Apps-användning. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/delete | Ta bort Web Apps Virtual Network anslutningar. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/gateways/read | Hämta Web Apps Virtual Network anslutningar-gatewayer. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/gateways/write | Uppdatera Web Apps Virtual Network anslutningar-gatewayer. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/read | Hämta Web Apps Virtual Network anslutningar. |
> | Åtgärd | microsoft.web/sites/virtualnetworkconnections/write | Uppdatera Web Apps Virtual Network anslutningar. |
> | Åtgärd | Microsoft. Web/Sites/WebJobs/Read | Hämta Web Apps WebJobs. |
> | Åtgärd | Microsoft. Web/Sites/Write | Skapa en ny webbapp eller uppdatera en befintlig |
> | Åtgärd | microsoft.web/skus/read | Hämta SKU: er. |
> | Åtgärd | Microsoft. Web/sourcecontrols/Read | Hämta käll kontroller. |
> | Åtgärd | Microsoft. Web/sourcecontrols/Write | Uppdatera käll kontroller. |
> | Åtgärd | microsoft.web/unregister/action | Avregistrera Microsoft. Web Resource Provider för prenumerationen. |
> | Åtgärd | microsoft.web/validate/action | Kontrollerar. |
> | Åtgärd | microsoft.web/verifyhostingenvironmentvnet/action | Verifiera värd miljön VNet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.WorkloadMonitor/components/read | Hämtar komponenter för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/componentsSummary/read | Hämtar Sammanfattning av komponenter |
> | Åtgärd | Microsoft. WorkloadMonitor/monitorInstances/Read | Hämtar instanser av Övervakare för resursen |
> | Åtgärd | Microsoft. WorkloadMonitor/monitorInstancesSummary/Read | Hämtar Sammanfattning av övervaknings instanser |
> | Åtgärd | Microsoft. WorkloadMonitor/Monitors/Read | Hämtar Övervakare för resursen |
> | Åtgärd | Microsoft. WorkloadMonitor/Monitors/Write | Konfigurera övervakaren för resursen |
> | Åtgärd | Microsoft. WorkloadMonitor/notificationSettings/Read | Hämtar meddelande inställningar för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurera meddelande inställningar för resursen |
> | Åtgärd | Microsoft.WorkloadMonitor/operations/read | Hämtar de åtgärder som stöds |

## <a name="next-steps"></a>Nästa steg

- [Matcha Resource Provider till tjänst](../azure-resource-manager/azure-services-resource-providers.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Inbyggda roller för Azure-resurser](built-in-roles.md)
