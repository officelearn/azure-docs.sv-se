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
ms.date: 09/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: c413c03c000ef9ff1ebf742359551567d488584b
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185716"
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
> | Åtgärd | Microsoft. AAD/domainServices/replicaSets/Delete | Ta bort kluster plats |
> | Åtgärd | Microsoft. AAD/domainServices/replicaSets/Read | Läs kluster plats |
> | Åtgärd | Microsoft. AAD/domainServices/replicaSets/Write | Skriv kluster webbplats |
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
> | Åtgärd | Microsoft.Advisor/generateRecommendations/action | Skapar rekommendationer |
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
> | Åtgärd | Microsoft.AnalysisServices/locations/operationresults/read | Hämtar information om det angivna åtgärds resultatet. |
> | Åtgärd | Microsoft.AnalysisServices/locations/operationstatuses/read | Hämtar information om den angivna åtgärds statusen. |
> | Åtgärd | Microsoft. AnalysisServices/Operations/Read | Hämtar information om åtgärder |
> | Åtgärd | Microsoft. AnalysisServices/register/åtgärd | Registrerar Analysis Services Resource Provider. |
> | Åtgärd | Microsoft.AnalysisServices/servers/delete | Tar bort Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Visar status för den gateway som är associerad med servern. |
> | Åtgärd | Microsoft.AnalysisServices/servers/read | Hämtar information om den angivna Analysis Server. |
> | Åtgärd | Microsoft. AnalysisServices/servrar/återuppta/åtgärd | Återupptar Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/skus/read | Hämta tillgänglig SKU-information för servern |
> | Åtgärd | Microsoft. AnalysisServices/-servrar/PAUSE/åtgärd | Pausar Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/servers/write | Skapar eller uppdaterar den angivna Analysis Server. |
> | Åtgärd | Microsoft.AnalysisServices/skus/read | Hämtar information om SKU: er |

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
> | Åtgärd | Microsoft. API Management/Service/authorizationServers/Read | Visar en uppsättning auktoriserade servrar som definierats inom en tjänst instans. eller hämtar information om den Authorization-server som anges av dess identifierare. |
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
> | Åtgärd | Microsoft. API Management/Service/Gateways/Delete | Tar bort en angiven Gateway. |
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
> | Åtgärd | Microsoft. API Management/Service/Identityprovider/Read | Visar en samling identitets leverantörer som kon figurer ATS i den angivna tjänst instansen. eller hämtar konfigurations information för den identitets leverantör som kon figurer ATS i angiven tjänst instans. |
> | Åtgärd | Microsoft. API Management/Service/Identityprovider/Write | Skapar eller uppdaterar IdentityProvider-konfigurationen. eller uppdaterar en befintlig IdentityProvider-konfiguration. |
> | Åtgärd | Microsoft. API Management/Service/Issues/Read | Visar en samling problem i den angivna tjänst instansen. eller får API Management problem information |
> | Åtgärd | Microsoft. API Management/Service/locations/networkstatus/Read | Hämtar nätverks åtkomst status för resurser som tjänsten är beroende av på platsen. |
> | Åtgärd | Microsoft. API Management/Service/loggar/ta bort | Tar bort den angivna loggaren. |
> | Åtgärd | Microsoft. API Management/Service/loggar/Läs | Visar en samling loggar i den angivna tjänst instansen. eller hämtar information om den loggning som anges av dess identifierare. |
> | Åtgärd | Microsoft. API Management/Service/loggar/Skriv | Skapar eller uppdaterar en loggare. eller uppdaterar en befintlig loggare. |
> | Åtgärd | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Managements tjänsten |
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
> | Åtgärd | Microsoft.ApiManagement/service/openidConnectProviders/read | Listor över alla OpenId Connect-providrar. eller hämtar en speciell OpenID Connect-Provider. |
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
> | Åtgärd | Microsoft. API Management/Service/Subscriptions/Read | Visar en lista över alla prenumerationer för API Management tjänst instansen. eller hämtar den angivna prenumerations enheten. |
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
> | Åtgärd | Microsoft. Authorization/elevateAccess/åtgärd | Beviljar åtkomst administratörs åtkomst till anroparens användare i klient omfånget |
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
> | Åtgärd | Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökad information för en Azure Stack Marketplace-produkt |
> | Åtgärd | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | Åtgärd | Microsoft. AzureStack/registreringar/produkter/uploadProductLog/åtgärd | Registrera Azure Stack Marketplace produkt åtgärds status och tidsstämpel |
> | Åtgärd | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure Stack registrering |
> | Åtgärd | Microsoft.AzureStack/registrations/write | Skapar eller uppdaterar en Azure Stack registrering |

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
> | Åtgärd | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Hämtar åtgärds resultatet för blockchain-medlemmar. |
> | Åtgärd | Microsoft.Blockchain/locations/checkNameAvailability/action | Kontrollerar att resurs namnet är giltigt och inte används. |
> | Åtgärd | Microsoft. blockchain/Operations/Read | Visa en lista med alla åtgärder i Microsoft blockchain Resource Provider. |
> | Åtgärd | Microsoft. blockchain/register/åtgärd | Registrerar prenumerationen för blockchain-resurs leverantören. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/assignmentOperations/Read | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/Delete | Ta bort eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/Read | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/whoisblueprint/Action | Hämta objekt-ID för tjänstens huvud namn för Azure-ritningar. |
> | Åtgärd | Microsoft. skiss/blueprintAssignments/Write | Skapa eller uppdatera eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/skisser/artefakter/ta bort | Ta bort eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/skisser/artefakter/läsning | Läs eventuella skiss artefakter |
> | Åtgärd | Microsoft. skiss/skisser/artefakter/skrivning | Skapa eller uppdatera eventuella skiss artefakter |
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
> | Åtgärd | Microsoft. CDN/CheckNameAvailability/åtgärd |  |
> | Åtgärd | Microsoft. CDN/CheckResourceUsage/åtgärd |  |
> | Åtgärd | Microsoft. CDN/edgenodes/Delete |  |
> | Åtgärd | Microsoft.Cdn/edgenodes/read |  |
> | Åtgärd | Microsoft. CDN/edgenodes/Write |  |
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
> | Åtgärd | Microsoft. ClassicCompute/domän namn/platser/roller/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
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
> | Åtgärd | Microsoft. ClassicCompute/Operations/Read | Hämtar listan över åtgärder. |
> | Åtgärd | Microsoft. ClassicCompute/operationStatuses/Read | Läser åtgärds statusen för resursen. |
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
> | Åtgärd | Microsoft. ClassicCompute/virtualMachines/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
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
> | Åtgärd | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Hämtar peering för det virtuella nätverket. |
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
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/blobServices/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/blobServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/commitMigration/Action | Aktiverar migrering av ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/Delete | Ta bort lagrings kontot. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Tar bort en specifik lagrings konto disk. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Läser åtgärds statusen för resursen. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagrings konto disken. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/disks/write | Lägger till en lagrings konto disk. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/fileServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/delete | Tar bort en specifik lagrings konto avbildning. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Returnerar åtgärds status för lagrings konto avbildning. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagrings konto avbildningen. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar åtkomst nycklar för lagrings kontona. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Läser åtgärds statusen för resursen. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Tar bort en specifik operativ Systems avbildning för lagrings konton. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returnerar operativ Systems avbildningen för lagrings kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/osImages/write | Lägger till en specifik operativ Systems avbildning för lagrings konton. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Förbereder migrering av ett lagrings konto. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/queueServices/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/read | Returnera lagrings kontot med det aktuella kontot. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Återskapar befintliga åtkomst nycklar för lagrings kontot. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningar. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hämtar mått definitionerna. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hämtar måtten. |
> | Åtgärd | Microsoft.ClassicStorage/storageAccounts/services/read | Hämta de tillgängliga tjänsterna. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningar. |
> | Åtgärd | Microsoft. ClassicStorage/storageAccounts/tableServices/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
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
> | DataAction | Microsoft. CognitiveServices/konton/autoföreslå/Sök/åtgärd | Den här åtgärden ger förslag på en viss fråga eller partiell fråga. |
> | DataAction | Microsoft. CognitiveServices/konton/ComputerVision/analysera/åtgärd | Den här åtgärden extraherar en omfattande uppsättning visuella funktioner baserat på avbildningens innehåll.  |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/areaofinterest/Action | Den här åtgärden returnerar en avgränsnings ruta runt det viktigaste avsnittet i bilden. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Beskrivning/åtgärd | Den här åtgärden genererar en beskrivning av en bild på ett läsligt språk med fullständiga meningar.<br> Beskrivningen baseras på en samling innehålls taggar, som också returneras av åtgärden.<br>Mer än en beskrivning kan genereras för varje avbildning.<br> Beskrivningar sorteras efter deras Tillförlitlighets resultat.<br>Alla beskrivningar är på engelska. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/detect/åtgärd | Den här åtgärden utför objekt identifiering på den angivna avbildningen.  |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/generatethumbnail/action | Den här åtgärden genererar en miniatyr bild med användarens angivna bredd och höjd.<br> Som standard analyserar tjänsten avbildningen, identifierar intresse området (ROI) och genererar smarta beskärnings koordinater baserade på ROI.<br> Smart beskärning hjälper när du anger ett proportioner som skiljer sig från indatakällans bild |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/models/analyze/action | Den här åtgärden identifierar innehåll i en avbildning genom att använda en domänbaserad modell.<br> Listan över domänbaserade modeller som stöds av API för visuellt innehåll kan hämtas med hjälp av/Models GET-begäran.<br> För närvarande tillhandahåller API följande domänbaserade modeller: kändisar, landmärken. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/Models/Read | Den här åtgärden returnerar listan över domänbaserade modeller som stöds av API för visuellt innehåll.  För närvarande stöder API följande domänbaserade modeller: kändis Recognizer, landmärkes tolk. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/OCR/Action | Optisk tecken läsning (OCR) identifierar text i en bild och extraherar de tolkade tecknen till en tecken ström som kan användas i datorn.    |
> | DataAction | Microsoft.CognitiveServices/accounts/ComputerVision/recognizetext/action | Använd det här gränssnittet för att få resultatet av en Identifiera text-åtgärd. När du använder Identifiera text-gränssnittet innehåller svaret ett fält med namnet "operation-location". Fältet "åtgärds plats" innehåller den URL som du måste använda för åtgärden Hämta Identifiera text åtgärds resultat. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/tag/Action | Den här åtgärden genererar en lista med ord, eller taggar, som är relevanta för innehållet i den angivna bilden.<br>API för visuellt innehåll kan returnera Taggar baserat på objekt, levnads, landskap eller åtgärder som finns i bilder.<br>Till skillnad från kategorier är Taggar inte organiserade enligt ett hierarkiskt klassificerings system, men motsvarar bild innehåll.<br>Taggar kan innehålla tips för att undvika tvetydighet eller ange sammanhang, till exempel kan taggen "cello" åtföljas av tipset "Musical instrument".<br>Alla Taggar är på engelska. |
> | DataAction | Microsoft. CognitiveServices/Accounts/ComputerVision/textoperations/Read | Det här gränssnittet används för att känna igen resultatet av text åtgärden. URL: en till det här gränssnittet ska hämtas från <b>"åtgärds plats"</b> -fältet som returneras från identifiera text-gränssnittet. |
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
> | DataAction | Microsoft. CognitiveServices/Accounts/TextAnalytics/sentiment/Action | API:t returnerar en poäng mellan 0 och 1.<br>Poängen nära 1 visar positiv sentiment, medan poängen nära 0 visar negativa sentiment.<br>Poängen på 0,5 anger att sentiment saknas (t. ex.<br>ett factoid-uttryck). |
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
> | Åtgärd | Microsoft.CognitiveServices/Operations/read | Visa en lista över alla tillgängliga åtgärder |
> | Åtgärd | Microsoft. CognitiveServices/register/åtgärd | Registrerar prenumeration för Cognitive Services |
> | Åtgärd | Microsoft. CognitiveServices/register/åtgärd | Registrerar prenumeration för Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Commerce/RateCard/Read | Returnerar erbjudande data, metadata för resurs/mätare och priser för den aktuella prenumerationen. |
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
> | Åtgärd | Microsoft.Compute/restorePointCollections/restorePoints/write | Skapar en ny återställnings punkt |
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
> | Åtgärd | Microsoft. förbrukning/saldo/Läs | Visa en lista över användnings sammanfattningen för en fakturerings period för en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/budget/ta bort | Ta bort budgetarna med en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/budget/Läs | Lista budgetarna efter en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/budget/Skriv | Skapar och uppdaterar budgetarna av en prenumeration eller en hanterings grupp. |
> | Åtgärd | Microsoft. förbrukning/avgifter/Läs | Lista avgifter |
> | Åtgärd | Microsoft. förbrukning/krediter/Läs | Lista krediter |
> | Åtgärd | Microsoft. förbrukning/händelser/läsa | Lista händelser |
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
> | Åtgärd | Microsoft.ContainerRegistry/registries/quarantineRead/read | Hämta eller hämta bilder i karantän från container Registry |
> | Åtgärd | Microsoft. ContainerRegistry/register/quarantineWrite/Write | Skriv/ändra karantän tillstånd för karantän avbildningar |
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
> | Åtgärd | Microsoft.ContainerRegistry/registries/sign/write | Push/pull-metadata för innehålls förtroende för ett behållar register. |
> | Åtgärd | Microsoft. ContainerRegistry/register/uppgifter/ta bort | Tar bort en aktivitet för ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Visa en lista med alla uppgifter om en aktivitet för ett behållar register. |
> | Åtgärd | Microsoft.ContainerRegistry/registries/tasks/read | Hämtar en uppgift för ett behållar register eller listar alla aktiviteter. |
> | Åtgärd | Microsoft. ContainerRegistry/register/uppgifter/Skriv | Skapar eller uppdaterar en uppgift för ett behållar register. |
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
> | Åtgärd | Microsoft. container service/managedClusters/Delete | Tar bort ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/Detections/Read | Hämta hanterad kluster detektor |
> | Åtgärd | Microsoft. container service/managedClusters/diagnosticsState/Read | Hämtar det diagnostiska läget för klustret |
> | Åtgärd | Microsoft. container service/managedClusters/listClusterAdminCredential/Action | Visa en lista över clusterAdmin-autentiseringsuppgiften för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | Åtgärd | Microsoft. container service/managedClusters/privateEndpointConnectionsApproval/Action | Anger om användaren får godkänna en privat slut punkts anslutning |
> | Åtgärd | Microsoft. container service/managedClusters/Read | Hämta ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Återställa AAD-profilen för ett hanterat kluster |
> | Åtgärd | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Återställa tjänstens huvud konto profil för ett hanterat kluster |
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

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ContentModerator/Applications/Delete | Ta bort åtgärd |
> | Åtgärd | Microsoft.ContentModerator/applications/listSecrets/action | Lista hemligheter |
> | Åtgärd | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Läsa token för enkel inloggning |
> | Åtgärd | Microsoft.ContentModerator/applications/read | Läs åtgärd |
> | Åtgärd | Microsoft.ContentModerator/applications/write | Skriv åtgärd |
> | Åtgärd | Microsoft.ContentModerator/applications/write | Skriv åtgärd |
> | Åtgärd | Microsoft.ContentModerator/listCommunicationPreference/action | Lista kommunikations inställningar |
> | Åtgärd | Microsoft.ContentModerator/operations/read | Läs åtgärder |
> | Åtgärd | Microsoft.ContentModerator/updateCommunicationPreference/action | Uppdatera kommunikations inställningar |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/delete | Ta bort den angivna cloudConnector. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/read | Ange cloudConnectors för den autentiserade användaren. |
> | Åtgärd | Microsoft.CostManagement/cloudConnectors/write | Skapa eller uppdatera angiven cloudConnector. |
> | Åtgärd | Microsoft. CostManagement/dimensions/Read | Visa en lista över alla dimensioner som stöds enligt ett definitions område. |
> | Åtgärd | Microsoft.CostManagement/exports/action | Kör den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/delete | Ta bort den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/exports/read | Lista exporten efter omfattning. |
> | Åtgärd | Microsoft.CostManagement/exports/run/action | Kör exporter. |
> | Åtgärd | Microsoft.CostManagement/exports/write | Skapa eller uppdatera den angivna exporten. |
> | Åtgärd | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Visa en lista över externalSubscriptions i en externalBillingAccount för den autentiserade användaren. |
> | Åtgärd | Microsoft.CostManagement/externalBillingAccounts/read | Ange externalBillingAccounts för den autentiserade användaren. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/Read | Ange externalSubscriptions för den autentiserade användaren. |
> | Åtgärd | Microsoft. CostManagement/externalSubscriptions/Write | Uppdatera den associerade hanterings gruppen för externalSubscription |
> | Åtgärd | Microsoft.CostManagement/query/action | Fråga användnings data efter ett omfång. |
> | Åtgärd | Microsoft.CostManagement/query/read | Fråga användnings data efter ett omfång. |
> | Åtgärd | Microsoft. CostManagement/register/åtgärd | Registrera åtgärd för omfånget för Microsoft. CostManagement av en prenumeration. |
> | Åtgärd | Microsoft. CostManagement/Reports/Action | Schemalägg rapporter om användnings data med ett omfång. |
> | Åtgärd | Microsoft. CostManagement/Reports/Read | Schemalägg rapporter om användnings data med ett omfång. |
> | Åtgärd | Microsoft. CostManagement/Tenants/register/åtgärd | Registrera åtgärd för omfånget för Microsoft. CostManagement av en klient. |

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
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Visar eller hämtar aviseringarna |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Tar bort bandbredds scheman |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Visar eller hämtar åtgärds resultatet |
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
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | Visar eller hämtar åtgärds status |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Delete | Tar bort order |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar eller hämtar order |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar eller hämtar order |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/order/Write | Skapar eller uppdaterar order |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Delete | Tar bort rollerna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Read | Visar eller hämtar roller |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Read | Visar eller hämtar roller |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Write | Skapar eller uppdaterar roller |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/Action | Sök efter uppdateringar |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Uppdatera säkerhets inställningar |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Delete | Tar bort resurserna |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Read | Listar eller hämtar resurserna |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Read | Listar eller hämtar resurserna |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/reshares/Refresh/åtgärd | Uppdatera dela metadata med data från molnet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Write | Skapar eller uppdaterar resurserna |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Delete | Tar bort autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Visar eller hämtar autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Visar eller hämtar autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | Skapar eller uppdaterar autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Delete | Tar bort utlösare |
> | Åtgärd | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Read | Listar eller hämtar utlösare |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Read | Listar eller hämtar utlösare |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Write | Skapar eller uppdaterar utlösarna |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary/Read | Visar eller hämtar uppdaterings sammanfattningen |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/Action | Ladda upp certifikat för enhets registrering |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Delete | Tar bort resurs användare |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/operationResults/Read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Read | Listar eller hämtar delnings användare |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Read | Listar eller hämtar delnings användare |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Write | Skapar eller uppdaterar resurs användare |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Skapar eller uppdaterar Data Box Edge enheter |
> | Åtgärd | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Skapar eller uppdaterar Data Box Edge enheter |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Databricks/locations/getNetworkPolicies/action | Hämta principer för nätverks avsikt för ett undernät baserat på den plats som används av NRP |
> | Åtgärd | Microsoft. Databricks/register/åtgärd | Registrera till Databricks. |
> | Åtgärd | Microsoft. Databricks/arbets ytor/ta bort | Tar bort en Databricks-arbetsyta. |
> | Åtgärd | Microsoft. Databricks/arbets ytor/providers/Microsoft. Insights/diagnosticSettings/Read | Ställer in tillgängliga diagnostikinställningar för arbets ytan Databricks |
> | Åtgärd | Microsoft. Databricks/arbets ytor/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningar. |
> | Åtgärd | Microsoft. Databricks/arbets ytor/providers/Microsoft. Insights/logDefinitions/Read | Hämtar tillgängliga logg definitioner för arbets ytan Databricks |
> | Åtgärd | Microsoft. Databricks/arbets ytor/läsa | Hämtar en lista över Databricks-arbetsytor. |
> | Åtgärd | Microsoft. Databricks/arbets ytor/Skriv | Skapar en Databricks-arbetsyta. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. DataCatalog/kataloger/ta bort | Ta bort katalog resurs för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/kataloger/läsa | Läs katalog resurs för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/kataloger/skrivning | Skriv katalog resurs för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/checkNameAvailability/Read | Kontrol lera katalog namns tillgänglighet för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/datacatalogs/Delete | Ta bort DataCatalog Resource för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/datacatalogs/Read | Läs DataCatalog-resursen för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/datacatalogs/Write | Skriv DataCatalog Resource för Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/Operations/Read | Läser alla tillgängliga åtgärder i Data Catalog Resource Provider. |
> | Åtgärd | Microsoft. DataCatalog/register/åtgärd | Registrera prenumerationen för Data Catalog Resource Provider |
> | Åtgärd | Microsoft. DataCatalog/avregistrera/åtgärd | Avregistrera prenumerationen för Data Catalog Resource Provider |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Kontrollerar om Data Factory namn är tillgängligt för användning. |
> | Åtgärd | Microsoft.DataFactory/datafactories/activitywindows/read | Läser aktivitets fönster i Data Factory med angivna parametrar. |
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
> | Åtgärd | Microsoft. DataFactory/factors/querydebugpipelineruns/Action | Frågar om fel söknings pipelinen körs. |
> | Åtgärd | Microsoft. DataFactory/factors/querypipelineruns/Action | Frågar om pipelinen körs. |
> | Åtgärd | Microsoft. DataFactory/factors/querypipelineruns/Read | Läser resultatet av pipeline-körningar. |
> | Åtgärd | Microsoft. DataFactory/factors/querytriggerruns/Action | Frågar om utlösaren körs. |
> | Åtgärd | Microsoft. DataFactory/factors/querytriggerruns/Read | Läser resultatet av utlösare som körs. |
> | Åtgärd | Microsoft. DataFactory/factors/Read | Läser Data Factory. |
> | Åtgärd | Microsoft. DataFactory/factors/sandboxpipelineruns/sandboxActivityRuns/Read | Hämtar fel söknings körnings information för aktiviteten. |
> | Åtgärd | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Startar en session med data flödes fel sökning. |
> | Åtgärd | Microsoft. DataFactory/factors/submitDataFlowForPreview/Action | Skicka data flöde för att hämta data för hands versionen med en felsökningssession. |
> | Åtgärd | Microsoft. DataFactory/factors/triggerruns/Read | Läser utlösare som körs. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/Delete | Tar bort alla utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/Read | Läser alla utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/starta/åtgärd | Startar alla utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/utlösare/stoppa/åtgärd | Stoppar eventuell utlösare. |
> | Åtgärd | Microsoft. DataFactory/factors/triggers/triggerruns/Read | Läser utlösare som körs. |
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
> | Åtgärd | Microsoft. DBforMariaDB/checkNameAvailability/Action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Åtgärd | Microsoft. DBforMariaDB/locations/azureAsyncOperation/Read | Returnera MariaDB-Server åtgärds resultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera ResourceGroup-baserade MariaDB Server åtgärds resultat |
> | Åtgärd | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera MariaDB-Server åtgärds resultat |
> | Åtgärd | Microsoft. DBforMariaDB/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft. DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Returnera en lista över resultat för Server hot identifiering. |
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
> | Åtgärd | Microsoft. DBforMariaDB/servers/Delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft. DBforMariaDB/servers/firewallRules/Delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/logFiles/read | Returnera listan över MariaDB-loggfiler. |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. DBforMariaDB/servers/providers/Microsoft. Insights/logDefinitions/Read | Hämtar de tillgängliga loggarna för MariaDB-servrar |
> | Åtgärd | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texterna för en lista med frågor |
> | Åtgärd | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texten för en fråga |
> | Åtgärd | Microsoft. DBforMariaDB/servrar/läsa | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
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
> | Åtgärd | Microsoft. DBforMySQL/checkNameAvailability/Action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Åtgärd | Microsoft. DBforMySQL/locations/azureAsyncOperation/Read | Returnera åtgärds resultat för MySQL-Server |
> | Åtgärd | Microsoft.DBforMySQL/locations/operationResults/read | Returnera ResourceGroup-baserade server åtgärds resultat för MySQL-Server |
> | Åtgärd | Microsoft.DBforMySQL/locations/operationResults/read | Returnera åtgärds resultat för MySQL-Server |
> | Åtgärd | Microsoft. DBforMySQL/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Åtgärd | Microsoft. DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Åtgärd | Microsoft. DBforMySQL/locations/securityAlertPoliciesOperationResults/Read | Returnera en lista över resultat för Server hot identifiering. |
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
> | Åtgärd | Microsoft. DBforMySQL/servers/Delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft. DBforMySQL/servers/firewallRules/Delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft. DBforMySQL/servers/firewallRules/Read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft. DBforMySQL/servers/firewallRules/Write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforMySQL/servers/logFiles/read | Returnera listan över PostgreSQL-loggfiler. |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/logDefinitions/Read | Hämtar tillgängliga loggar för MySQL-servrar |
> | Åtgärd | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/metricDefinitions/Read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft. DBforMySQL/servers/queryTexts/åtgärd | Returnera texterna för en lista med frågor |
> | Åtgärd | Microsoft. DBforMySQL/servers/queryTexts/åtgärd | Returnera texten för en fråga |
> | Åtgärd | Microsoft. DBforMySQL/servrar/läsa | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
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
> | Åtgärd | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
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
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/logFiles/read | Returnera listan över PostgreSQL-loggfiler. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Delete | Tar bort en befintlig anslutning till en privat slut punkt |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Read | Returnerar listan över anslutnings-proxy för privata slut punkter eller hämtar egenskaperna för den angivna anslutningen till den privata slut punkten. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/åtgärd | Verifierar en privat slut punkts anslutning skapa samtal från NRP sida |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Write | Skapar en anslutning till en privat slutpunkt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna proxyn för privat slut punkt anslutning. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Delete | Tar bort en befintlig privat slut punkts anslutning |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Read | Returnerar listan över anslutningar för privata slut punkter eller hämtar egenskaperna för den angivna privata slut punkts anslutningen. |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Write | Godkänner eller avvisar en befintlig privat slut punkts anslutning |
> | Åtgärd | Microsoft. DBforPostgreSQL/servers/privateLinkResources/Read | Hämta de privata länk resurserna för motsvarande PostgreSQL-Server |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Returnera texten för en fråga |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Returnera texterna för en lista med frågor |
> | Åtgärd | Microsoft.DBforPostgreSQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
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
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Tar bort en befintlig brand Väggs regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för postgres-servrar |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Åtgärd | Microsoft.DBforPostgreSQL/serversv2/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Devices/Account/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. Devices/Account/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generera verifierings kod |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifiera certifikat resurs |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Ta bort IotHub-klient resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Hämtar hälso tillståndet för alla Dirigerings slut punkter för en IotHub |
> | Åtgärd | Microsoft.Devices/elasticPools/iotHubTenants/Write | Skapa eller uppdatera klient organisations resursen för IotHub |
> | Åtgärd | Microsoft.Devices/ElasticPools/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generera verifierings kod |
> | Åtgärd | Microsoft. Devices/iotHubs/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft. Devices/iotHubs/certificates/verify/Action | Verifiera certifikat resurs |
> | Åtgärd | Microsoft. Devices/iotHubs/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/iotHubs/Delete | Ta bort IotHub-resurs |
> | Åtgärd | Microsoft. Devices/IotHubs/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
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
> | Åtgärd | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Hämtar hälso tillståndet för alla Dirigerings slut punkter för en IotHub |
> | Åtgärd | Microsoft.Devices/iotHubs/skus/Read | Hämta giltiga IotHub SKU: er |
> | Åtgärd | Microsoft. Devices/iotHubs/Write | Skapa eller uppdatera IotHub-resurs |
> | Åtgärd | Microsoft. Devices/locations/operationresults/Read | Resultat av hämtning av plats baserad åtgärd |
> | Åtgärd | Microsoft. Devices/operationresults/Read | Hämta åtgärds resultat |
> | Åtgärd | Microsoft. Devices/Operations/Read | Hämta alla ResourceProvider-åtgärder |
> | Åtgärd | Microsoft. Devices/provisioningServices/certificates/Delete | Tar bort certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generera verifierings kod |
> | Åtgärd | Microsoft. Devices/provisioningServices/certificates/Read | Hämtar certifikatet |
> | Åtgärd | Microsoft. Devices/provisioningServices/certificates/verify/Action | Verifiera certifikat resurs |
> | Åtgärd | Microsoft. Devices/provisioningServices/certificates/Write | Skapa eller uppdatera certifikat |
> | Åtgärd | Microsoft.Devices/provisioningServices/Delete | Ta bort IotDps-resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Hämta IotDps-nycklar för nyckel namn |
> | Åtgärd | Microsoft. Devices/provisioningServices/listnycklar/Action | Hämta alla IotDps-nycklar |
> | Åtgärd | Microsoft.Devices/provisioningServices/logDefinitions/read | Hämtar de tillgängliga logg definitionerna för etablerings tjänsten |
> | Åtgärd | Microsoft.Devices/provisioningServices/metricDefinitions/read | Hämtar tillgängliga mått för etablerings tjänsten |
> | Åtgärd | Microsoft. Devices/provisioningServices/operationresults/Read | Hämta resultat från DPS-åtgärd |
> | Åtgärd | Microsoft. Devices/provisioningServices/Read | Hämta IotDps-resurs |
> | Åtgärd | Microsoft.Devices/provisioningServices/skus/Read | Hämta giltiga IotDps SKU: er |
> | Åtgärd | Microsoft. Devices/provisioningServices/Write | Skapa IotDps-resurs |
> | Åtgärd | Microsoft. Devices/register/åtgärd | Registrera prenumerationen för IotHub Resource Provider och gör det möjligt att skapa IotHub-resurser |
> | Åtgärd | Microsoft. Devices/usages/Read | Hämta information om prenumerations användning för den här providern. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. DevSpaces/controllers/Delete | Ta bort Azure dev Spaces-kontroller och dataplanen-tjänster |
> | Åtgärd | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Lista över anslutnings information för Azure dev Spaces-infrastrukturen |
> | Åtgärd | Microsoft.DevSpaces/controllers/read | Läs egenskaper för Azure dev Spaces-kontrollant |
> | Åtgärd | Microsoft.DevSpaces/controllers/write | Skapa eller uppdatera egenskaper för Azure dev Spaces-kontrollant |
> | Åtgärd | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Kontrol lera befintlig styrenhets mappning för en behållar värd |
> | Åtgärd | Microsoft.DevSpaces/locations/operationresults/read | Läs status för en asynkron åtgärd |
> | Åtgärd | Microsoft.DevSpaces/register/action | Registrera Microsoft dev Spaces Resource Provider med en prenumeration |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DevTestLab/labCenters/delete | Ta bort labb Center. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/read | Läs labb Center. |
> | Åtgärd | Microsoft.DevTestLab/labCenters/write | Lägg till eller ändra labb Center. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Läs Azure Resource Manager-mallar. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Genererar en ARM-mall för den aktuella artefakten, överför de filer som krävs till ett lagrings konto och verifierar den genererade artefakten. |
> | Åtgärd | Microsoft. DevTestLab/Labs/artifactSources/artefakter/läsning | Läsa artefakter. |
> | Åtgärd | Microsoft. DevTestLab/Labs/artifactSources/Delete | Ta bort artefakt källor. |
> | Åtgärd | Microsoft. DevTestLab/Labs/artifactSources/Read | Läsa artefakt källor. |
> | Åtgärd | Microsoft.DevTestLab/labs/artifactSources/write | Lägg till eller ändra artefakt källor. |
> | Åtgärd | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Anspråk på en slumpmässig virtuell dator i labbet. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/read | Läs kostnader. |
> | Åtgärd | Microsoft.DevTestLab/labs/costs/write | Lägg till eller ändra kostnader. |
> | Åtgärd | Microsoft.DevTestLab/labs/CreateEnvironment/action | Skapa virtuella datorer i ett labb. |
> | Åtgärd | Microsoft. DevTestLab/Labs/customImages/Delete | Ta bort anpassade avbildningar. |
> | Åtgärd | Microsoft. DevTestLab/Labs/customImages/Read | Läs anpassade avbildningar. |
> | Åtgärd | Microsoft. DevTestLab/Labs/customImages/Write | Lägg till eller ändra anpassade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/delete | Ta bort labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Se till att den aktuella användaren har en giltig profil i labbet. |
> | Åtgärd | Microsoft. DevTestLab/labb/ExportResourceUsage/åtgärd | Exporterar labb resurs användningen till ett lagrings konto |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/read | Läs formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Åtgärd | Microsoft.DevTestLab/labs/galleryImages/read | Läsa Galleri bilder. |
> | Åtgärd | Microsoft. DevTestLab/labb/GenerateUploadUri/åtgärd | Generera en URI för att ladda upp anpassade disk avbildningar till ett labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importera en virtuell dator till ett annat labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/ListVhds/action | Lista tillgängliga disk avbildningar för att skapa en anpassad avbildning. |
> | Åtgärd | Microsoft. DevTestLab/Labs/notificationChannels/Delete | Ta bort meddelande kanaler. |
> | Åtgärd | Microsoft. DevTestLab/Labs/notificationChannels/notify/åtgärd | Skicka meddelande till angiven kanal. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/read | Läsa meddelande kanaler. |
> | Åtgärd | Microsoft.DevTestLab/labs/notificationChannels/write | Lägg till eller ändra meddelande kanaler. |
> | Åtgärd | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Utvärderar labb princip. |
> | Åtgärd | Microsoft. DevTestLab/Labs/policySets/policies/Delete | Ta bort principer. |
> | Åtgärd | Microsoft. DevTestLab/Labs/policySets/policies/Read | Läs principer. |
> | Åtgärd | Microsoft. DevTestLab/Labs/policySets/policies/Write | Lägg till eller ändra principer. |
> | Åtgärd | Microsoft. DevTestLab/Labs/policySets/Read | Läs princip uppsättningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/read | Läs labb. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft. DevTestLab/labb/scheman/kör/åtgärd | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Visar alla tillämpliga scheman |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/read | Läs scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/delete | Ta bort tjänst löpare. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/read | Läsa tjänst löpare. |
> | Åtgärd | Microsoft.DevTestLab/labs/serviceRunners/write | Lägg till eller ändra tjänst löpare. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/delete | Ta bort delade gallerier. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/read | Läsa delade gallerier. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Ta bort delade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Läs delade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Lägg till eller ändra delade avbildningar. |
> | Åtgärd | Microsoft.DevTestLab/labs/sharedGalleries/write | Lägg till eller ändra delade gallerier. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/delete | Ta bort användar profiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anslut och skapa lånet av disken till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/delete | Ta bort diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/Detach/action | Koppla från och Bryt lånet för den disk som är ansluten till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/read | Läs diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/disks/write | Lägg till eller ändra diskar. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/read | Läs miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/read | Läs användar profiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/delete | Ta bort hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/read | Läs hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/secrets/write | Lägg till eller ändra hemligheter. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Ta bort Service Fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Läs Service Fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Läs scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Starta en Service Fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stoppa en Service Fabric |
> | Åtgärd | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Lägg till eller ändra Service Fabric. |
> | Åtgärd | Microsoft.DevTestLab/labs/users/write | Lägg till eller ändra användar profiler. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Koppla en ny eller befintlig datadisk till den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Tillämpa artefakter på den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Bli ägare till en befintlig virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/delete | Ta bort virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Koppla från den angivna disken från den virtuella datorn. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/read | Läsa virtuella datorer. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Distribuera om en virtuell dator |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ändra storlek på virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Starta om en virtuell dator. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Läs scheman. |
> | Åtgärd | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Lägg till eller ändra scheman. |
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
> | Åtgärd | Microsoft.DevTestLab/labs/write | Lägg till eller ändra labb. |
> | Åtgärd | Microsoft.DevTestLab/locations/operations/read | Läs åtgärder. |
> | Åtgärd | Microsoft.DevTestLab/register/action | Registrerar prenumerationen |
> | Åtgärd | Microsoft.DevTestLab/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft. DevTestLab/Schemalägg/kör/åtgärd | Kör ett schema. |
> | Åtgärd | Microsoft.DevTestLab/schedules/read | Läs scheman. |
> | Åtgärd | Microsoft. DevTestLab/schemalägger/ommål/åtgärd | Uppdaterar ett schemas mål resurs-ID. |
> | Åtgärd | Microsoft.DevTestLab/schedules/write | Lägg till eller ändra scheman. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.DocumentDB/databaseAccountNames/read | Söker efter namn tillgänglighet. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/ta bort | Ta bort en samling. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/läsa | Läs en samling eller lista alla samlingar. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' MongoDB '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/inställningar/läsa | Läs en samlings data flöde. Gäller endast för API-typer: ' MongoDB '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/inställningar/Skriv | Uppdatera ett data flöde för samlingen. Gäller endast för API-typer: ' MongoDB '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/skriva | Skapa eller uppdatera en samling. Gäller endast för API-typer: ' MongoDB '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/ta bort | Ta bort en behållare. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/läsa | Läs en behållare eller lista alla behållare. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/inställningar/läsa | Läs ett data flöde för behållare. Gäller endast för API-typer: SQL. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/inställningar/Skriv | Uppdatera ett data flöde för behållare. Gäller endast för API-typer: SQL. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/skriva | Skapa eller uppdatera en behållare. Gäller endast för API-typer: SQL. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/ta bort | Ta bort en databas. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/Delete | Ta bort ett diagram. Gäller endast för API-typer: ' gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/graf/läsa | Läs ett diagram eller Visa en lista över alla grafer. Gäller endast för API-typer: ' gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/läsa | Läsa ett diagram genom strömning. Gäller endast för API-typer: ' gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/Skriv | Uppdatera ett diagram genom strömning. Gäller endast för API-typer: ' gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/diagram/skriva | Skapa eller uppdatera ett diagram. Gäller endast för API-typer: ' gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/läsa | Läs en databas eller lista alla databaser. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL, MongoDB, gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/inställningar/läsa | Läs en databas genom strömning. Gäller endast för API-typer: SQL, MongoDB, gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/inställningar/Skriv | Uppdatera ett databas data flöde. Gäller endast för API-typer: SQL, MongoDB, gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/skriva | Skapa en databas. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/Backsteg/Delete | Ta bort ett blank steg. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/disk utrymme/läsa | Läs ett tecken utrymme eller Visa en lista med alla blank steg. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/inställningar/läsa | Läs ett data flöde för genom strömning. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/inställningar/Skriv | Uppdatera ett flöde för genom strömning. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/ta bort | Ta bort en tabell. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/Backsteg/tables/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/läsa | Läs en tabell eller lista alla tabeller. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/inställningar/läsa | Läs tabell data flöde. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/inställningar/Skriv | Uppdatera ett tabell data flöde. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/disks/tabeller/skrivning | Skapa eller uppdatera en tabell. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API: er/disk utrymme/skrivning | Skapa ett blank steg. Gäller endast för API-typer: ' Cassandra '. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API/tables/Delete | Ta bort en tabell. Gäller endast för API-typer: tabell. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/tables/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: tabell. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API/tables/Read | Läs en tabell eller lista alla tabeller. Gäller endast för API-typer: tabell. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/tables/Settings/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: tabell. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/Apis/tables/Settings/Read | Läs tabell data flöde. Gäller endast för API-typer: tabell. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API/tables/Settings/Write | Uppdatera ett tabell data flöde. Gäller endast för API-typer: tabell. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/API/tables/Write | Skapa eller uppdatera en tabell. Gäller endast för API-typer: tabell. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/säkerhets kopiering/åtgärd | Skicka en begäran om att konfigurera säkerhets kopiering |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Ändra resurs grupp för ett databas konto |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Läser mått definitionerna för samlingen. |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/mått/läsa | Läser samlings måtten. |
> | Åtgärd | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Läs databas kontots partitionerings nyckel nivå mått |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/partitioner/mått/läsa | Läs mått för partitions nivå för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/partitioner/läsa | Läs databas konto partitioner i en samling |
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
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Read | Läs en anslutning till en privat slutpunkt för anslutnings-proxy för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/åtgärd | Verifiera en anslutning till en privat slutpunkt för anslutnings proxy för databas konto |
> | Åtgärd | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Skapa eller uppdatera en privat slut punkts anslutnings proxy för databas konto |
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
> | Åtgärd | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Delete | Ta bort ägarskaps identifierare |
> | Åtgärd | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Read | Lista ägarskaps identifierare |
> | Åtgärd | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Read | Hämta ägarskaps identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Skapa eller uppdatera identifierare |
> | Åtgärd | Microsoft.DomainRegistration/domains/operationresults/Read | Hämta en domän åtgärd |
> | Åtgärd | Microsoft. DomainRegistration/Domains/Read | Hämta listan över domäner |
> | Åtgärd | Microsoft. DomainRegistration/Domains/Read | Hämta domän |
> | Åtgärd | Microsoft. DomainRegistration/Domains/renew/åtgärd | Förnya en befintlig domän. |
> | Åtgärd | Microsoft.DomainRegistration/domains/Write | Lägg till en ny domän eller uppdatera en befintlig |
> | Åtgärd | Microsoft.DomainRegistration/generateSsoRequest/Action | Generera en begäran om signering till kontroll Center för domän. |
> | Åtgärd | Microsoft.DomainRegistration/listDomainRecommendations/Action | Hämta listan med domän rekommendationer baserat på nyckelord |
> | Åtgärd | Microsoft.DomainRegistration/operations/Read | Visa en lista med alla åtgärder från App Service Domain Registration |
> | Åtgärd | Microsoft.DomainRegistration/register/action | Registrera resurs leverantören för Microsoft-domäner för prenumerationen |
> | Åtgärd | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | List avtals åtgärd |
> | Åtgärd | Microsoft. DomainRegistration/topLevelDomains/Read | Hämta Toplevel-domäner |
> | Åtgärd | Microsoft. DomainRegistration/topLevelDomains/Read | Hämta Toplevel-domän |
> | Åtgärd | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Verifiera ett domän inköps objekt utan att skicka det |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. EventGrid/Domains/Delete | Ta bort en domän |
> | Åtgärd | Microsoft. EventGrid/Domains/Listnycklar/Action | Lista nycklar för en domän |
> | Åtgärd | Microsoft. EventGrid/Domains/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för domäner |
> | Åtgärd | Microsoft. EventGrid/Domains/Read | Läs en domän |
> | Åtgärd | Microsoft. EventGrid/Domains/regenerateKey/Action | Återskapa nyckel för en domän |
> | Åtgärd | Microsoft. EventGrid/Domains/topics/Delete | Ta bort ett domän ämne |
> | Åtgärd | Microsoft. EventGrid/Domains/topics/Read | Läs avsnittet om en domän |
> | Åtgärd | Microsoft. EventGrid/Domains/topics/Write | Skapa eller uppdatera ett domän ämne |
> | Åtgärd | Microsoft. EventGrid/Domains/Write | Skapa eller uppdatera en domän |
> | Åtgärd | Microsoft. EventGrid/eventSubscriptions/Delete | Ta bort en eventSubscription |
> | Åtgärd | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Hämta fullständig URL för händelse prenumerationen |
> | Åtgärd | Microsoft. EventGrid/eventSubscriptions/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för händelse prenumerationer |
> | Åtgärd | Microsoft. EventGrid/eventSubscriptions/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för händelse prenumerationer |
> | Åtgärd | Microsoft. EventGrid/eventSubscriptions/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för eventSubscriptions |
> | Åtgärd | Microsoft. EventGrid/eventSubscriptions/Read | Läs en eventSubscription |
> | Åtgärd | Microsoft. EventGrid/eventSubscriptions/Write | Skapa eller uppdatera en eventSubscription |
> | Åtgärd | Microsoft. EventGrid/extensionTopics/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för ämnen |
> | Åtgärd | Microsoft. EventGrid/extensionTopics/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för ämnen |
> | Åtgärd | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för ämnen |
> | Åtgärd | Microsoft. EventGrid/extensionTopics/Read | Läs en extensionTopic. |
> | Åtgärd | Microsoft. EventGrid/locations/eventSubscriptions/Read | Lista regionala händelse prenumerationer |
> | Åtgärd | Microsoft.EventGrid/locations/operationResults/read | Läs resultatet av en regional åtgärd |
> | Åtgärd | Microsoft.EventGrid/locations/operationsStatus/read | Läs status för en regional åtgärd |
> | Åtgärd | Microsoft. EventGrid/locations/topictypes/eventSubscriptions/Read | Lista regionala händelse prenumerationer efter TopicType |
> | Åtgärd | Microsoft.EventGrid/operationResults/read | Läs resultatet av en åtgärd |
> | Åtgärd | Microsoft. EventGrid/Operations/Read | Visa en lista över EventGrid-åtgärder. |
> | Åtgärd | Microsoft.EventGrid/operationsStatus/read | Läs status för en åtgärd |
> | Åtgärd | Microsoft. EventGrid/register/åtgärd | Registrerar prenumerationen för EventGrid-resurs leverantören. |
> | Åtgärd | Microsoft. EventGrid/ämnen/Delete | Ta bort ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/listKeys/action | Lista nycklar för ett ämne |
> | Åtgärd | Microsoft. EventGrid/ämnen/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för ämnen |
> | Åtgärd | Microsoft. EventGrid/ämnen/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för ämnen |
> | Åtgärd | Microsoft. EventGrid/ämnen/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för ämnen |
> | Åtgärd | Microsoft. EventGrid/ämnen/läsa | Läs ett ämne |
> | Åtgärd | Microsoft.EventGrid/topics/regenerateKey/action | Återskapa nyckel för ett ämne |
> | Åtgärd | Microsoft. EventGrid/ämnen/skriva | Skapa eller uppdatera ett ämne |
> | Åtgärd | Microsoft. EventGrid/topictypes/eventSubscriptions/Read | Lista globala händelse prenumerationer efter typ av ämne |
> | Åtgärd | Microsoft. EventGrid/topictypes/eventtypes/Read | Läs eventtypes som stöds av en TopicType |
> | Åtgärd | Microsoft. EventGrid/topictypes/Read | Läs en TopicType |
> | Åtgärd | Microsoft.EventGrid/unregister/action | Avregistrerar prenumerationen för EventGrid-resurs leverantören. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.EventHub/availableClusterRegions/read | Läs åtgärd för att visa en lista över tillgängliga företablerade kluster av Azure-regionen. |
> | Åtgärd | Microsoft. EventHub/checkNameAvailability/Action | Kontrollerar tillgänglighet för namn området under den aktuella prenumerationen. |
> | Åtgärd | Microsoft. EventHub/checkNamespaceAvailability/Action | Kontrollerar tillgänglighet för namn området under den aktuella prenumerationen. Detta API är inaktuellt Använd CheckNameAvailability i stället. |
> | Åtgärd | Microsoft. EventHub/Clusters/Delete | Tar bort en befintlig kluster resurs. |
> | Åtgärd | Microsoft. EventHub/kluster/namn områden/läsa | Visar namn rymds ARM-ID för namn områden i ett kluster. |
> | Åtgärd | Microsoft. EventHub/Clusters/operationresults/Read | Hämta status för en asynkron kluster åtgärd. |
> | Åtgärd | Microsoft. EventHub/Clusters/providers/Microsoft. Insights/metricDefinitions/Read | Hämta lista över resurs beskrivningar för kluster mått |
> | Åtgärd | Microsoft.EventHub/clusters/read | Hämtar kluster resurs beskrivningen |
> | Åtgärd | Microsoft.EventHub/clusters/write | Skapar eller ändrar en befintlig kluster resurs. |
> | Åtgärd | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Tar bort VNet-reglerna i EventHub-adressresursen för angivet VNet |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/action | Uppdaterar namn områdets auktoriseringsregel. Detta API är föråldrat. Använd ett parkera-anrop för att uppdatera auktoriseringsprincipen för namn områden i stället.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Hämta anslutnings strängen till namn området |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/read | Hämta listan över Beskrivning av auktoriseringsregler för namn områden. |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.EventHub/namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Åtgärd | Microsoft.EventHub/namespaces/Delete | Ta bort namn områdes resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar reglerna för auktoriseringsregler för det primära namn området för katastrof återställning |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för det primära namn området för haveri beredskap |
> | Åtgärd | Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Inaktiverar haveri beredskap och stoppar replikering av ändringar från primära till sekundära namn områden. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för namn rymds Ali Aset under den aktuella prenumerationen. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Tar bort den katastrof återställnings konfiguration som är kopplad till namn området. Den här åtgärden kan bara anropas via det primära namn området. |
> | Åtgärd | Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/redundans/åtgärd | Anropar en GEO DR-redundans och konfigurerar om namn rymds Ali Aset så att det pekar på det sekundära namn området. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hämtar den katastrof återställnings konfiguration som är kopplad till namn området. |
> | Åtgärd | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den katastrof återställnings konfiguration som är kopplad till namn området. |
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
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/Read | Hämta lista över beskrivningar av EventHub-resurser |
> | Åtgärd | Microsoft. EventHub/Namespaces/eventhubs/Write | Skapa eller uppdatera egenskaper för EventHub. |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/delete | Ta bort resurs för IP-filter |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/read | Hämta IP-filter resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/ipFilterRules/write | Skapa resurs för IP-filter |
> | DataAction | Microsoft. EventHub/Namespaces/meddelanden/ta emot/åtgärd | Ta emot meddelanden |
> | DataAction | Microsoft. EventHub/Namespaces/meddelanden/skicka/åtgärd | Skicka meddelanden |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/read | Hämtar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.EventHub/namespaces/messagingPlan/write | Uppdaterar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft. EventHub/Namespaces/networkruleset/Delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/networkruleset/Read | Hämtar NetworkRuleSet-resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/networkruleset/Write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/read | Hämtar NetworkRuleSet-resurs |
> | Åtgärd | Microsoft.EventHub/namespaces/networkrulesets/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft. EventHub/Namespaces/operationresults/Read | Hämta status för namn områdes åtgärd |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Write | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/logDefinitions/Read | Hämta lista över namn rymds loggar resurs beskrivningar |
> | Åtgärd | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/metricDefinitions/Read | Hämta lista över resurs beskrivningar för namn rymds mått |
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
> | Åtgärd | Microsoft. features/Operations/Read | Hämtar listan över åtgärder. |
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
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Läs en inställning för resurs diagnostik |
> | Åtgärd | Microsoft. Insights/AutoscaleSettings/providers/Microsoft. Insights/diagnosticSettings/Write | Skapa eller uppdatera en inställning för resurs diagnostik |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Läs logg definitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Läs mått definitioner |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Read | Läs en autoskalningsinställning |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoskalning nedåt initierad |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Autoskalning nedåt slutförd |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoskalning upp initierad |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Autoskalning slutfördes |
> | Åtgärd | Microsoft.Insights/AutoscaleSettings/Write | Skapa eller uppdatera en autoskalningsinställning |
> | Åtgärd | Microsoft. Insights/baseline/Read | Läs en mått bas linje (förhands granskning) |
> | Åtgärd | Microsoft. Insights/CalculateBaseline/Read | Beräkna bas linje för mått värden (förhands granskning) |
> | Åtgärd | Microsoft.Insights/Components/AnalyticsItems/Delete | Ta bort ett Application Insights Analytics-objekt |
> | Åtgärd | Microsoft. Insights/komponenter/AnalyticsItems/Read | Läser ett Application Insights Analytics-objekt |
> | Åtgärd | Microsoft. Insights/komponenter/AnalyticsItems/Write | Skriva ett Application Insights Analytics-objekt |
> | Åtgärd | Microsoft. Insights/komponenter/AnalyticsTables/åtgärd | Åtgärd för Application Insights Analytics-tabell |
> | Åtgärd | Microsoft. Insights/komponenter/AnalyticsTables/Delete | Ta bort ett Application Insights Analytics-tabell schema |
> | Åtgärd | Microsoft. Insights/komponenter/AnalyticsTables/Read | Läsa ett tabell schema för Application Insights Analytics |
> | Åtgärd | Microsoft. Insights/komponenter/AnalyticsTables/Write | Skriva ett tabell schema för Application Insights Analytics |
> | Åtgärd | Microsoft. Insights/komponenter/anteckningar/ta bort | Tar bort en Application Insights anteckning |
> | Åtgärd | Microsoft. Insights/komponenter/anteckningar/läsa | Läser en Application Insights anteckning |
> | Åtgärd | Microsoft. Insights/komponenter/anteckningar/Skriv | Skriva en Application Insights anteckning |
> | Åtgärd | Microsoft. Insights/komponenter/API/läsa | Läser Application Insights-komponenten för data-API |
> | Åtgärd | Microsoft. Insights/komponenter/ApiKeys/åtgärd | Genererar en Application Insights API-nyckel |
> | Åtgärd | Microsoft. Insights/komponenter/ApiKeys/Delete | Ta bort en Application Insights API-nyckel |
> | Åtgärd | Microsoft. Insights/komponenter/ApiKeys/Read | Läser en Application Insights API-nyckel |
> | Åtgärd | Microsoft. Insights/komponenter/BillingPlanForComponent/Read | Läser en fakturerings plan för Application Insights-komponenten |
> | Åtgärd | Microsoft. Insights/komponenter/CurrentBillingFeatures/Read | Läser aktuella fakturerings funktioner för Application Insights-komponenten |
> | Åtgärd | Microsoft. Insights/komponenter/CurrentBillingFeatures/Write | Skriver aktuella fakturerings funktioner för Application Insights-komponenten |
> | Åtgärd | Microsoft. Insights/komponenter/DailyCapReached/åtgärd | Nått den dagliga gränsen för Application Insights komponent |
> | Åtgärd | Microsoft. Insights/komponenter/DailyCapWarningThresholdReached/åtgärd | Uppnått varnings tröskelvärdet för dagligt tak för Application Insights komponent |
> | Åtgärd | Microsoft. Insights/komponenter/DefaultWorkItemConfig/Read | Läsa en Application Insights standard konfiguration av ALM-integrering |
> | Åtgärd | Microsoft. Insights/komponenter/ta bort | Tar bort en konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft. Insights/komponenter/händelser/läsa | Hämta loggar från Application Insights med OData-frågeuttryck |
> | Åtgärd | Microsoft. Insights/komponenter/ExportConfiguration/åtgärd | Åtgärd för Application Insights export inställningar |
> | Åtgärd | Microsoft. Insights/komponenter/ExportConfiguration/Delete | Ta bort Application Insights export inställningar |
> | Åtgärd | Microsoft. Insights/komponenter/ExportConfiguration/Read | Läser Application Insights export inställningar |
> | Åtgärd | Microsoft. Insights/komponenter/ExportConfiguration/Write | Skriver Application Insights export inställningar |
> | Åtgärd | Microsoft. Insights/komponenter/ExtendQueries/Read | Läser utökade frågeresultat för Application Insights komponent |
> | Åtgärd | Microsoft. Insights/komponenter/favoriter/ta bort | Ta bort en Application Insights favorit |
> | Åtgärd | Microsoft. Insights/komponenter/favoriter/läsa | Läsa en Application Insights favorit |
> | Åtgärd | Microsoft. Insights/komponenter/Favoriter/Skriv | Skriva en Application Insights favorit |
> | Åtgärd | Microsoft. Insights/komponenter/FeatureCapabilities/Read | Läser funktioner för Application Insights komponent funktioner |
> | Åtgärd | Microsoft. Insights/komponenter/GetAvailableBillingFeatures/Read | Läser Application Insights komponent tillgängliga fakturerings funktioner |
> | Åtgärd | Microsoft. Insights/komponenter/GetToken/Read | Läser en token för en Application Insights-komponent |
> | Åtgärd | Microsoft. Insights/komponenter/MetricDefinitions/Read | Läser Application Insights mått definitioner för komponenter |
> | Åtgärd | Microsoft. Insights/komponenter/mått/läsa | Läser Application Insights komponent mått |
> | Åtgärd | Microsoft. Insights/komponenter/flytta/åtgärd | Flytta en Application Insights-komponent till en annan resurs grupp eller prenumeration |
> | Åtgärd | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Ta bort ett Application Insights personligt analys objekt |
> | Åtgärd | Microsoft. Insights/komponenter/MyAnalyticsItems/Read | Läsa ett Application Insights personligt analys objekt |
> | Åtgärd | Microsoft. Insights/komponenter/MyAnalyticsItems/Write | Skriva ett Application Insights personligt analys objekt |
> | Åtgärd | Microsoft. Insights/komponenter/mina favoriter/läsa | Läsa en Application Insights personlig favorit |
> | Åtgärd | Microsoft. Insights/komponenter/åtgärder/Läs | Hämta status för långvariga åtgärder i Application Insights |
> | Åtgärd | Microsoft. Insights/komponenter/PricingPlans/Read | Läser pris Planen för en Application Insights komponent |
> | Åtgärd | Microsoft. Insights/komponenter/PricingPlans/Write | Skriva en pris plan för Application Insights komponent |
> | Åtgärd | Microsoft. Insights/komponenter/ProactiveDetectionConfigs/Read | Läser Application Insights proaktiv identifierings konfiguration |
> | Åtgärd | Microsoft. Insights/komponenter/ProactiveDetectionConfigs/Write | Skriver Application Insights proaktiv identifierings konfiguration |
> | Åtgärd | Microsoft. Insights/Components/providers/Microsoft. Insights/MetricDefinitions/Read | Läs mått definitioner |
> | Åtgärd | Microsoft. Insights/komponenter/rensning/åtgärd | Rensar data från Application Insights |
> | Åtgärd | Microsoft. Insights/komponenter/fråga/läsa | Köra frågor mot Application Insights loggar |
> | Åtgärd | Microsoft. Insights/komponenter/QuotaStatus/Read | Läser Application Insights komponent kvot status |
> | Åtgärd | Microsoft. Insights/komponenter/läsa | Läser konfiguration av en Application Insights-komponent |
> | Åtgärd | Microsoft. Insights/komponenter/SyntheticMonitorLocations/Read | Läser Application Insights webtest-platser |
> | Åtgärd | Microsoft. Insights/komponenter/webbtester/läsa | Läser en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/komponenter/WorkItemConfigs/Delete | Ta bort en Application Insights ALM-integrerings konfiguration |
> | Åtgärd | Microsoft. Insights/komponenter/WorkItemConfigs/Read | Läsa en Application Insights ALM-integrerings konfiguration |
> | Åtgärd | Microsoft. Insights/komponenter/WorkItemConfigs/Write | Skriva en Application Insights ALM-integrerings konfiguration |
> | Åtgärd | Microsoft. Insights/komponenter/skriva | Skriver till en konfiguration av Application Insights-komponent |
> | Åtgärd | Microsoft.Insights/DiagnosticSettings/Delete | Ta bort en inställning för resurs diagnostik |
> | Åtgärd | Microsoft. Insights/DiagnosticSettings/Read | Läs en inställning för resurs diagnostik |
> | Åtgärd | Microsoft. Insights/DiagnosticSettings/Write | Skapa eller uppdatera en inställning för resurs diagnostik |
> | Åtgärd | Microsoft.Insights/EventCategories/Read | Läs tillgängliga händelse kategorier för aktivitets loggen |
> | Åtgärd | Microsoft. Insights/eventtypes/digestevents/Read | Läs sammandrag av hanterings händelse typ |
> | Åtgärd | Microsoft. Insights/eventtypes/värden/Read | Läs aktivitets logg händelser |
> | Åtgärd | Microsoft. Insights/ExtendedDiagnosticSettings/Delete | Ta bort en diagnostisk inställning för nätverks flödes logg |
> | Åtgärd | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Läs en diagnostisk inställning för nätverks flödes logg |
> | Åtgärd | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Skapa eller uppdatera en diagnostisk inställning för nätverks flödes logg |
> | Åtgärd | Microsoft. Insights/ListMigrationDate/åtgärd | Hämta datum för migrering av prenumeration |
> | Åtgärd | Microsoft.Insights/ListMigrationDate/Read | Hämta datum för migrering av prenumeration |
> | Åtgärd | Microsoft. Insights/LogDefinitions/Read | Läs logg definitioner |
> | Åtgärd | Microsoft.Insights/LogProfiles/Delete | Ta bort en logg profil för aktivitets logg |
> | Åtgärd | Microsoft.Insights/LogProfiles/Read | Läs logg profil för aktivitets logg |
> | Åtgärd | Microsoft. Insights/LogProfiles/Write | Skapa eller uppdatera en logg profil för aktivitets logg |
> | Åtgärd | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Läsa data från tabellen ADAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/ADReplicationResult/Read | Läsa data från tabellen ADReplicationResult |
> | Åtgärd | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Läsa data från tabellen ADSecurityAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/loggar/varna/läsa | Läsa data från aviserings tabellen |
> | Åtgärd | Microsoft.Insights/Logs/AlertHistory/Read | Läsa data från tabellen AlertHistory |
> | Åtgärd | Microsoft. Insights/logs/ApplicationInsights/Read | Läsa data från tabellen ApplicationInsights |
> | Åtgärd | Microsoft. Insights/logs/AzureActivity/Read | Läsa data från tabellen AzureActivity |
> | Åtgärd | Microsoft.Insights/Logs/AzureMetrics/Read | Läsa data från tabellen AzureMetrics |
> | Åtgärd | Microsoft. Insights/logs/BoundPort/Read | Läsa data från tabellen BoundPort |
> | Åtgärd | Microsoft. Insights/logs/CommonSecurityLog/Read | Läsa data från tabellen CommonSecurityLog |
> | Åtgärd | Microsoft. Insights/logs/ComputerGroup/Read | Läsa data från tabellen ComputerGroup |
> | Åtgärd | Microsoft. Insights/logs/ConfigurationChange/Read | Läsa data från tabellen ConfigurationChange |
> | Åtgärd | Microsoft. Insights/logs/ConfigurationData/Read | Läsa data från tabellen ConfigurationData |
> | Åtgärd | Microsoft. Insights/logs/ContainerImageInventory/Read | Läsa data från tabellen ContainerImageInventory |
> | Åtgärd | Microsoft. Insights/logs/ContainerInventory/Read | Läsa data från tabellen ContainerInventory |
> | Åtgärd | Microsoft. Insights/logs/ContainerLog/Read | Läsa data från tabellen ContainerLog |
> | Åtgärd | Microsoft. Insights/logs/ContainerServiceLog/Read | Läsa data från tabellen ContainerServiceLog |
> | Åtgärd | Microsoft. Insights/logs/DeviceAppCrash/Read | Läsa data från tabellen DeviceAppCrash |
> | Åtgärd | Microsoft. Insights/logs/DeviceAppLaunch/Read | Läsa data från tabellen DeviceAppLaunch |
> | Åtgärd | Microsoft. Insights/logs/DeviceCalendar/Read | Läsa data från tabellen DeviceCalendar |
> | Åtgärd | Microsoft. Insights/logs/DeviceCleanup/Read | Läsa data från tabellen DeviceCleanup |
> | Åtgärd | Microsoft. Insights/logs/DeviceConnectSession/Read | Läsa data från tabellen DeviceConnectSession |
> | Åtgärd | Microsoft. Insights/logs/DeviceEtw/Read | Läsa data från tabellen DeviceEtw |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Läsa data från tabellen DeviceHardwareHealth |
> | Åtgärd | Microsoft.Insights/Logs/DeviceHealth/Read | Läsa data från tabellen DeviceHealth |
> | Åtgärd | Microsoft. Insights/logs/DeviceHeartbeat/Read | Läsa data från tabellen DeviceHeartbeat |
> | Åtgärd | Microsoft. Insights/logs/DeviceSkypeHeartbeat/Read | Läsa data från tabellen DeviceSkypeHeartbeat |
> | Åtgärd | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Läsa data från tabellen DeviceSkypeSignIn |
> | Åtgärd | Microsoft. Insights/logs/DeviceSleepState/Read | Läsa data från tabellen DeviceSleepState |
> | Åtgärd | Microsoft. Insights/logs/DHAppFailure/Read | Läsa data från tabellen DHAppFailure |
> | Åtgärd | Microsoft. Insights/logs/DHAppReliability/Read | Läsa data från tabellen DHAppReliability |
> | Åtgärd | Microsoft. Insights/logs/DHDriverReliability/Read | Läsa data från tabellen DHDriverReliability |
> | Åtgärd | Microsoft. Insights/logs/DHLogonFailures/Read | Läsa data från tabellen DHLogonFailures |
> | Åtgärd | Microsoft. Insights/logs/DHLogonMetrics/Read | Läsa data från tabellen DHLogonMetrics |
> | Åtgärd | Microsoft. Insights/logs/DHOSCrashData/Read | Läsa data från tabellen DHOSCrashData |
> | Åtgärd | Microsoft. Insights/logs/DHOSReliability/Read | Läsa data från tabellen DHOSReliability |
> | Åtgärd | Microsoft. Insights/logs/DHWipAppLearning/Read | Läsa data från tabellen DHWipAppLearning |
> | Åtgärd | Microsoft. Insights/logs/DnsEvents/Read | Läsa data från tabellen DnsEvents |
> | Åtgärd | Microsoft. Insights/logs/DnsInventory/Read | Läsa data från tabellen DnsInventory |
> | Åtgärd | Microsoft. Insights/logs/ETWEvent/Read | Läsa data från tabellen ETWEvent |
> | Åtgärd | Microsoft. Insights/loggar/händelse/läsa | Läsa data från händelse tabellen |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Läsa data från tabellen ExchangeAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Läsa data från tabellen ExchangeOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/loggar/pulsslag/läsning | Läsa data från pulsslags tabellen |
> | Åtgärd | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Läsa data från tabellen IISAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/InboundConnection/Read | Läsa data från tabellen InboundConnection |
> | Åtgärd | Microsoft. Insights/logs/KubeNodeInventory/Read | Läsa data från tabellen KubeNodeInventory |
> | Åtgärd | Microsoft. Insights/logs/KubePodInventory/Read | Läsa data från tabellen KubePodInventory |
> | Åtgärd | Microsoft. Insights/logs/LinuxAuditLog/Read | Läsa data från tabellen LinuxAuditLog |
> | Åtgärd | Microsoft. Insights/logs/MAApplication/Read | Läsa data från tabellen MAApplication |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealth/Read | Läsa data från tabellen MAApplicationHealth |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Läsa data från tabellen MAApplicationHealthAlternativeVersions |
> | Åtgärd | Microsoft. Insights/logs/MAApplicationHealthIssues/Read | Läsa data från tabellen MAApplicationHealthIssues |
> | Åtgärd | Microsoft. Insights/logs/MAApplicationInstance/Read | Läsa data från tabellen MAApplicationInstance |
> | Åtgärd | Microsoft. Insights/logs/MAApplicationInstanceReadiness/Read | Läsa data från tabellen MAApplicationInstanceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Läsa data från tabellen MAApplicationReadiness |
> | Åtgärd | Microsoft. Insights/logs/MADeploymentPlan/Read | Läsa data från tabellen MADeploymentPlan |
> | Åtgärd | Microsoft. Insights/logs/MADevice/Read | Läsa data från tabellen MADevice |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Läsa data från tabellen MADevicePnPHealth |
> | Åtgärd | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Läsa data från tabellen MADevicePnPHealthAlternativeVersions |
> | Åtgärd | Microsoft. Insights/logs/MADevicePnPHealthIssues/Read | Läsa data från tabellen MADevicePnPHealthIssues |
> | Åtgärd | Microsoft. Insights/logs/MADeviceReadiness/Read | Läsa data från tabellen MADeviceReadiness |
> | Åtgärd | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Läsa data från tabellen MADriverInstanceReadiness |
> | Åtgärd | Microsoft. Insights/logs/MADriverReadiness/Read | Läsa data från tabellen MADriverReadiness |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddin/Read | Läsa data från tabellen MAOfficeAddin |
> | Åtgärd | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Läsa data från tabellen MAOfficeAddinHealth |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinHealthIssues/Read | Läsa data från tabellen MAOfficeAddinHealthIssues |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinInstance/Read | Läsa data från tabellen MAOfficeAddinInstance |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinInstanceReadiness/Read | Läsa data från tabellen MAOfficeAddinInstanceReadiness |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAddinReadiness/Read | Läsa data från tabellen MAOfficeAddinReadiness |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeApp/Read | Läsa data från tabellen MAOfficeApp |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAppHealth/Read | Läsa data från tabellen MAOfficeAppHealth |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAppInstance/Read | Läsa data från tabellen MAOfficeAppInstance |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeAppReadiness/Read | Läsa data från tabellen MAOfficeAppReadiness |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeBuildInfo/Read | Läsa data från tabellen MAOfficeBuildInfo |
> | Åtgärd | Microsoft. Insights/logs/MAOfficeCurrencyAssessment/Read | Läsa data från tabellen MAOfficeCurrencyAssessment |
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
> | Åtgärd | Microsoft. Insights/logs/ProtectionStatus/Read | Läsa data från tabellen ProtectionStatus |
> | Åtgärd | Microsoft. Insights/loggar/läsa | Läser data från alla loggar |
> | Åtgärd | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Läsa data från tabellen ReservedAzureCommonFields |
> | Åtgärd | Microsoft.Insights/Logs/ReservedCommonFields/Read | Läsa data från tabellen ReservedCommonFields |
> | Åtgärd | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Läsa data från tabellen SCCMAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/SCOMAssessmentRecommendation/Read | Läsa data från tabellen SCOMAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/SecurityAlert/Read | Läsa data från tabellen SecurityAlert |
> | Åtgärd | Microsoft. Insights/logs/SecurityBaseline/Read | Läsa data från tabellen SecurityBaseline |
> | Åtgärd | Microsoft. Insights/logs/SecurityBaselineSummary/Read | Läsa data från tabellen SecurityBaselineSummary |
> | Åtgärd | Microsoft. Insights/logs/SecurityDetection/Read | Läsa data från tabellen SecurityDetection |
> | Åtgärd | Microsoft. Insights/logs/SecurityEvent/Read | Läsa data från tabellen SecurityEvent |
> | Åtgärd | Microsoft. Insights/logs/ServiceFabricOperationalEvent/Read | Läsa data från tabellen ServiceFabricOperationalEvent |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Läsa data från tabellen ServiceFabricReliableActorEvent |
> | Åtgärd | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Läsa data från tabellen ServiceFabricReliableServiceEvent |
> | Åtgärd | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Läsa data från tabellen SfBAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Läsa data från tabellen SfBOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Läsa data från tabellen SharePointOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/SPAssessmentRecommendation/Read | Läsa data från tabellen SPAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/SQLAssessmentRecommendation/Read | Läsa data från tabellen SQLAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/SQLQueryPerformance/Read | Läsa data från tabellen SQLQueryPerformance |
> | Åtgärd | Microsoft. Insights/loggar/syslog/Read | Läsa data från syslog-tabellen |
> | Åtgärd | Microsoft. Insights/logs/SysmonEvent/Read | Läsa data från tabellen SysmonEvent |
> | Åtgärd | Microsoft. Insights/loggar/tabeller. anpassad/läst | Läser data från valfri anpassad logg |
> | Åtgärd | Microsoft. Insights/logs/UAApp/Read | Läsa data från tabellen UAApp |
> | Åtgärd | Microsoft. Insights/logs/UAComputer/Read | Läsa data från tabellen UAComputer |
> | Åtgärd | Microsoft. Insights/logs/UAComputerRank/Read | Läsa data från tabellen UAComputerRank |
> | Åtgärd | Microsoft. Insights/logs/UADriver/Read | Läsa data från tabellen UADriver |
> | Åtgärd | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Läsa data från tabellen UADriverProblemCodes |
> | Åtgärd | Microsoft. Insights/logs/UAFeedback/Read | Läsa data från tabellen UAFeedback |
> | Åtgärd | Microsoft. Insights/logs/UAHardwareSecurity/Read | Läsa data från tabellen UAHardwareSecurity |
> | Åtgärd | Microsoft. Insights/logs/UAIESiteDiscovery/Read | Läsa data från tabellen UAIESiteDiscovery |
> | Åtgärd | Microsoft. Insights/logs/UAOfficeAddIn/Read | Läsa data från tabellen UAOfficeAddIn |
> | Åtgärd | Microsoft. Insights/logs/UAProposedActionPlan/Read | Läsa data från tabellen UAProposedActionPlan |
> | Åtgärd | Microsoft. Insights/logs/UASysReqIssue/Read | Läsa data från tabellen UASysReqIssue |
> | Åtgärd | Microsoft. Insights/logs/UAUpgradedComputer/Read | Läsa data från tabellen UAUpgradedComputer |
> | Åtgärd | Microsoft. Insights/loggar/uppdatera/läsa | Läs data från uppdaterings tabellen |
> | Åtgärd | Microsoft.Insights/Logs/UpdateRunProgress/Read | Läsa data från tabellen UpdateRunProgress |
> | Åtgärd | Microsoft.Insights/Logs/UpdateSummary/Read | Läsa data från tabellen UpdateSummary |
> | Åtgärd | Microsoft. Insights/loggar/användning/läsa | Läsa data från användnings tabellen |
> | Åtgärd | Microsoft. Insights/logs/W3CIISLog/Read | Läsa data från tabellen W3CIISLog |
> | Åtgärd | Microsoft. Insights/logs/WaaSDeploymentStatus/Read | Läsa data från tabellen WaaSDeploymentStatus |
> | Åtgärd | Microsoft. Insights/logs/WaaSInsiderStatus/Read | Läsa data från tabellen WaaSInsiderStatus |
> | Åtgärd | Microsoft. Insights/logs/WaaSUpdateStatus/Read | Läsa data från tabellen WaaSUpdateStatus |
> | Åtgärd | Microsoft. Insights/logs/WDAVStatus/Read | Läsa data från tabellen WDAVStatus |
> | Åtgärd | Microsoft. Insights/logs/WDAVThreat/Read | Läsa data från tabellen WDAVThreat |
> | Åtgärd | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Läsa data från tabellen WindowsClientAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/WindowsFirewall/Read | Läsa data från tabellen WindowsFirewall |
> | Åtgärd | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Läsa data från tabellen WindowsServerAssessmentRecommendation |
> | Åtgärd | Microsoft. Insights/logs/WireData/Read | Läsa data från tabellen WireData |
> | Åtgärd | Microsoft. Insights/logs/WUDOAggregatedStatus/Read | Läsa data från tabellen WUDOAggregatedStatus |
> | Åtgärd | Microsoft. Insights/logs/WUDOStatus/Read | Läsa data från tabellen WUDOStatus |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Delete | Ta bort en måtta avisering |
> | Åtgärd | Microsoft.Insights/MetricAlerts/Read | Läs en måtta-varning |
> | Åtgärd | Microsoft. Insights/MetricAlerts/status/läsa | Läs mått aviserings status |
> | Åtgärd | Microsoft. Insights/MetricAlerts/Write | Skapa eller uppdatera en måtta-avisering |
> | Åtgärd | Microsoft. Insights/MetricBaselines/Read | Läs mått bas linjer |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Läs mått definitioner |
> | Åtgärd | Microsoft. Insights/MetricDefinitions/providers/Microsoft. Insights/Read | Läs mått definitioner |
> | Åtgärd | Microsoft.Insights/MetricDefinitions/Read | Läs mått definitioner |
> | Åtgärd | Microsoft. Insights/Metricnamespaces/Read | Läs mått namn rymder |
> | Åtgärd | Microsoft.Insights/Metrics/Action | Mått åtgärd |
> | Åtgärd | Microsoft. Insights/Metrics/Microsoft. Insights/Read | Läs mått |
> | Åtgärd | Microsoft. Insights/Metrics/providers/Metrics/Read | Läs mått |
> | Åtgärd | Microsoft.Insights/Metrics/Read | Läs mått |
> | DataAction | Microsoft. Insights/Metrics/Write | Skriv mått |
> | Åtgärd | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrera prenumerationen till den nya pris sättnings modellen |
> | Åtgärd | Microsoft. Insights/åtgärder/läsa | Läsåtgärder |
> | Åtgärd | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft. Insights/RollbackToLegacyPricingModel/åtgärd | Återställ prenumerationen på en äldre pris modell |
> | Åtgärd | Microsoft. Insights/ScheduledQueryRules/Delete | Tar bort en schemalagd frågeregel |
> | Åtgärd | Microsoft. Insights/ScheduledQueryRules/Read | Läser en regel för schemalagda frågor |
> | Åtgärd | Microsoft. Insights/ScheduledQueryRules/Write | Skriver en regel för schemalagda frågor |
> | Åtgärd | Microsoft. Insights/klienter/register/åtgärd | Initierar Microsoft Insights-providern |
> | Åtgärd | Microsoft.Insights/Unregister/Action | Registrera Microsoft Insights-providern |
> | Åtgärd | Microsoft. Insights/webbtester/ta bort | Tar bort en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/webbtester/GetToken/Read | Läser en webtest-token |
> | Åtgärd | Microsoft. Insights/webbtester/MetricDefinitions/Read | Läsning av mått definitioner för webbtest |
> | Åtgärd | Microsoft. Insights/webbtester/mått/läsa | Läsning av mått för webbtest |
> | Åtgärd | Microsoft. Insights/webbtester/läsa | Läser en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/webbtester/skriva | Skriver till en webb test konfiguration |
> | Åtgärd | Microsoft. Insights/arbets böcker/ta bort | Ta bort en arbets bok |
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
> | Åtgärd | Microsoft. Kusto/kluster/aktivera/åtgärd | Startar klustret. |
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
> | Åtgärd | Microsoft. Kusto/kluster/databaser/läsa | Läser en databas-resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/RemovePrincipals/åtgärd | Tar bort databasens huvud namn. |
> | Åtgärd | Microsoft. Kusto/kluster/databaser/skriva | Skriver en databas-resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/inaktivera/åtgärd | Stoppar klustret. |
> | Åtgärd | Microsoft. Kusto/kluster/ta bort | Tar bort ett kluster resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/läsa | Läser ett kluster resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Clusters/SKUs/read | Läser ett kluster-SKU-resourceCopy. |
> | Åtgärd | Microsoft. Kusto/kluster/start/åtgärd | Startar klustret. |
> | Åtgärd | Microsoft. Kusto/kluster/stoppa/åtgärd | Stoppar klustret. |
> | Åtgärd | Microsoft. Kusto/kluster/skrivning | Skriver ett kluster resourceCopy. |
> | Åtgärd | Microsoft. Kusto/DetachFollowerDatabases/Action | Kopplar bort uppföljnings databaser. |
> | Åtgärd | Microsoft. Kusto/ListFollowerDatabases/Action | Visar efterföljandes databaser. |
> | Åtgärd | Microsoft. Kusto/locations/CheckNameAvailability/Action | Kontrollerar resourceCopy namn tillgänglighet. |
> | Åtgärd | Microsoft. Kusto/locations/operationresults/Read | Läs åtgärder resourceCopys |
> | Åtgärd | Microsoft.Kusto/Operations/read | Läs åtgärder resourceCopys |
> | Åtgärd | Microsoft. Kusto/register/åtgärd | Prenumerations registrerings åtgärd |
> | Åtgärd | Microsoft. Kusto/register/åtgärd | Registrerar prenumerationen på Kusto-resurs leverantören. |
> | Åtgärd | Microsoft.Kusto/SKUs/read | Läser en SKU-resourceCopy. |
> | Åtgärd | Microsoft.Kusto/Unregister/action | Avregistrerar prenumerationen på Kusto-resurs leverantören. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.LabServices/labAccounts/CreateLab/action | Skapa ett labb i ett labb konto. |
> | Åtgärd | Microsoft. LabServices/labAccounts/Delete | Ta bort labb konton. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/delete | Ta bort Galleri bilder. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/read | Läsa Galleri bilder. |
> | Åtgärd | Microsoft.LabServices/labAccounts/galleryImages/write | Lägg till eller ändra Galleri bilder. |
> | Åtgärd | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Hämta regional tillgänglighets information för varje storleks kategori som kon figurer ATS under ett labb konto |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/AddUsers/Action | Lägga till användare i ett labb |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/Delete | Ta bort labb. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Ta bort miljö inställning. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Ta bort miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Läs miljöer. |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/environmentSettings/miljöer/ResetPassword/åtgärd | Återställer användar lösen ordet i en miljö |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stoppar en miljö genom att stoppa alla resurser i miljön |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Lägg till eller ändra miljöer. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Bestämmelser/avetablerar nödvändiga resurser för en miljö inställning baserat på aktuell status för labb/miljö-inställningen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Läs miljö inställning. |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/åtgärd | Återställer lösen ordet för den virtuella datorns mall. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Sparar den aktuella mal Lav bildningen i det delade galleriet i labb kontot |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Ta bort scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Läs scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Lägg till eller ändra scheman. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Startar en mall genom att starta alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stoppar en mall genom att stoppa alla resurser i mallen. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Lägg till eller ändra miljö inställning. |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/Read | Läs labb. |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Skicka e-post med registrerings länk till labbet |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/Users/Delete | Ta bort användare. |
> | Åtgärd | Microsoft. LabServices/labAccounts/Labs/Users/Read | Läsa användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/users/write | Lägg till eller ändra användare. |
> | Åtgärd | Microsoft.LabServices/labAccounts/labs/write | Lägg till eller ändra labb. |
> | Åtgärd | Microsoft.LabServices/labAccounts/read | Läs labb konton. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Ta bort sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/read | Läs sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedGalleries/write | Lägg till eller ändra sharedgalleries. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/delete | Ta bort sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/read | Läs sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/sharedImages/write | Lägg till eller ändra sharedimages. |
> | Åtgärd | Microsoft.LabServices/labAccounts/write | Lägg till eller ändra Lab-konton. |
> | Åtgärd | Microsoft. LabServices/locations/Operations/Read | Läs åtgärder. |
> | Åtgärd | Microsoft. LabServices/register/åtgärd | Registrerar prenumerationen |
> | Åtgärd | Microsoft.LabServices/users/GetOperationBatchStatus/action | Hämta åtgärds status för batch |
> | Åtgärd | Microsoft. LabServices/Users/Fabricadaptor/Action | Hämtar status för tids krävande åtgärd |
> | Åtgärd | Microsoft. LabServices/Users/GetPersonalPreferences/Action | Hämta personliga inställningar för en användare |
> | Åtgärd | Microsoft.LabServices/users/ListAllEnvironments/action | Visa en lista över alla miljöer för användaren |
> | Åtgärd | Microsoft. LabServices/Users/register/Action | Registrera en användare i ett hanterat labb |
> | Åtgärd | Microsoft. LabServices/Users/ResetPassword/Action | Återställer användar lösen ordet i en miljö |
> | Åtgärd | Microsoft.LabServices/users/StartEnvironment/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Åtgärd | Microsoft. LabServices/Users/StopEnvironment/Action | Stoppar en miljö genom att stoppa alla resurser i miljön |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/delete | Tar bort avtalet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Hämtar återanrops-URL: en för avtals innehållet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/read | Läser avtalet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/agreements/write | Skapar eller uppdaterar avtalet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/delete | Tar bort sammansättningen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Hämtar återanrops-URL: en för sammansättnings innehåll i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/read | Läser sammansättningen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/assemblies/write | Skapar eller uppdaterar sammansättningen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Tar bort batch-konfigurationen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Läser batch-konfigurationen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Skapar eller uppdaterar batch-konfigurationen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/delete | Tar bort certifikatet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/read | Läser certifikatet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/certificates/write | Skapar eller uppdaterar certifikatet i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/delete | Tar bort integrations kontot. |
> | Åtgärd | Microsoft. Logic/integrationAccounts/JOIN/åtgärd | Kopplar samman integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Hämtar återanrops-URL: en för integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Hämtar nycklarna i nyckel valvet. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Loggar spårnings händelser i integrations kontot. |
> | Åtgärd | Microsoft. Logic/integrationAccounts/Maps/Delete | Tar bort kartan i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Hämtar återanrops-URL: en för kart innehåll i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/read | Läser kartan i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/maps/write | Skapar eller uppdaterar kartan i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/delete | Tar bort partnern i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Hämtar återanrops-URL: en för partner innehåll i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/read | Läser partnern i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/partners/write | Skapar eller uppdaterar partnern i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Läser integrerings kontots logg definitioner. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/read | Läser integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Återskapar åtkomst nyckel hemligheter. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/delete | Tar bort schemat i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Hämtar återanrops-URL: en för schema innehåll i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/read | Läser schemat i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/schemas/write | Skapar eller uppdaterar schemat i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/delete | Tar bort sessionen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/read | Läser batch-konfigurationen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/sessions/write | Skapar eller uppdaterar sessionen i integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationAccounts/write | Skapar eller uppdaterar integrations kontot. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/delete | Tar bort integrerings tjänst miljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/join/action | Kopplar Integration Service Environment. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Läser den hanterade API-åtgärden för integrerings tjänst miljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Läser den hanterade API: n för integrerings tjänst miljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Läser åtgärds status för integrerings tjänst miljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Läser mått definitionerna för integrerings tjänst miljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/read | Läser integrerings tjänst miljön. |
> | Åtgärd | Microsoft.Logic/integrationServiceEnvironments/write | Skapar eller uppdaterar integrerings tjänst miljön. |
> | Åtgärd | Microsoft. Logic/locations/arbetsflödens/recommendOperationGroups/Action | Hämtar arbets flödets rekommenderade åtgärds grupper. |
> | Åtgärd | Microsoft. Logic/locations/arbetsflödens/validate/Action | Verifierar arbets flödet. |
> | Åtgärd | Microsoft. Logic/Operations/Read | Hämtar åtgärden. |
> | Åtgärd | Microsoft. Logic/register/åtgärd | Registrerar Microsoft. Logic Resource Provider för en specifik prenumeration. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/accessKeys/Delete | Tar bort åtkomst nyckeln. |
> | Åtgärd | Microsoft. Logic/arbetsflöden/accessKeys/List/Action | Visar en lista över åtkomst nyckel hemligheter. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/accessKeys/Read | Läser åtkomst nyckeln. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/accessKeys/regenerate/Action | Återskapar åtkomst nyckel hemligheter. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/accessKeys/Write | Skapar eller uppdaterar åtkomst nyckeln. |
> | Åtgärd | Microsoft. Logic/arbetsflöden/Delete | Tar bort arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/inaktivera/åtgärd | Inaktiverar arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/aktivera/åtgärd | Aktiverar arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/listCallbackUrl/åtgärd | Hämtar återanrops-URL för arbets flöde. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/listSwagger/åtgärd | Hämtar Swagger för arbets flödes definitioner. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/flytta/åtgärd | Flyttar arbets flödet från dess befintliga prenumerations-ID, resurs grupp och/eller namn till ett annat prenumerations-ID, resurs grupp och/eller namn. |
> | Åtgärd | Microsoft. Logic/Workflows/providers/Microsoft. Insights/diagnosticSettings/Read | Läser diagnostikinställningar för arbets flöde. |
> | Åtgärd | Microsoft. Logic/Workflows/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för arbets flöde. |
> | Åtgärd | Microsoft. Logic/Workflows/providers/Microsoft. Insights/logDefinitions/Read | Läser logg definitioner för arbets flödet. |
> | Åtgärd | Microsoft. Logic/Workflows/providers/Microsoft. Insights/metricDefinitions/Read | Läser mått definitioner för arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/läsa | Läser arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/regenerateAccessKey/åtgärd | Återskapar åtkomst nyckel hemligheter. |
> | Åtgärd | Microsoft. Logic/arbets flöden/körning/åtgärd | Startar en körning av arbets flödet. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/listExpressionTraces/åtgärd | Hämtar spårning av uttryck för arbets flödets körnings åtgärd. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/Läs | Läser arbets flödets körnings åtgärd. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/repetitioner/listExpressionTraces/åtgärd | Hämtar spår för upprepnings uttryck för arbets flödets körnings åtgärd. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/repetitioner/läsning | Läser upprepningen av arbets flödets körnings åtgärd. |
> | Åtgärd | Microsoft. Logic/arbets flöden/körningar/åtgärder/repetitioner/requestHistories/Read | Läser historiken för arbets flödets körnings åtgärd för upprepning. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/requestHistories/Read | Läser historiken för arbets flödets körnings åtgärd. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/scoperepetitions/Read | Läser upprepningen av arbets flödets körnings åtgärd. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/körs/Avbryt/åtgärd | Avbryter körningen av ett arbets flöde. |
> | Åtgärd | Microsoft. Logic/-arbetsflöden/kör/ta bort | Tar bort en körning av ett arbets flöde. |
> | Åtgärd | Microsoft. Logic/-arbets flöden/körningar/åtgärder/Läs | Läser status för arbets flödets körnings åtgärd. |
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
> | Åtgärd | Microsoft.MachineLearningServices/locations/usages/read | Användnings rapport för AML-beräknings resurser i en prenumeration |
> | Åtgärd | Microsoft.MachineLearningServices/locations/vmsizes/read | Hämta VM-storlekar som stöds |
> | Åtgärd | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Hämtar status för en viss arbets ytans åtgärd |
> | Åtgärd | Microsoft.MachineLearningServices/register/action | Registrerar prenumerationen för Machine Learning Services Resource Provider |
> | Åtgärd | Microsoft. MachineLearningServices/arbets ytor/beräkningar/ta bort | Tar bort beräknings resurserna i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista hemligheter för beräknings resurser i Machine Learning Services arbets yta |
> | Åtgärd | Microsoft. MachineLearningServices/arbets ytor/computes/listNodes/Action | Lista noder för beräknings resurs i Machine Learning Services arbets yta |
> | Åtgärd | Microsoft. MachineLearningServices/arbets ytor/beräkningar/läsning | Hämtar beräknings resurserna i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/computes/write | Skapar eller uppdaterar beräknings resurserna i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/delete | Tar bort Machine Learning Services arbets ytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/experiment/ta bort | Tar bort experiment i Machine Learning Services-arbetsytor |
> | DataAction | Microsoft. MachineLearningServices/arbets ytor/experiment/läsa | Hämtar experiment i Machine Learning Services arbets ytor |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Skapar eller uppdaterar experiment i Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista hemligheter för en Machine Learning Services arbets yta |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/read | Hämtar Machine Learning Services arbets ytor |
> | Åtgärd | Microsoft.MachineLearningServices/workspaces/write | Skapar eller uppdaterar Machine Learning Services arbets ytor |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. ManagedIdentity/identiteter/läsa | Hämtar en befintlig systemtilldelad identitet |
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
> | Åtgärd | Microsoft. ManagedServices/operationStatuses/Read | Läser åtgärds statusen för resursen. |
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
> | Åtgärd | Microsoft. Media/locations/checkNameAvailability/åtgärd | Kontrollerar om ett Media Services konto namn är tillgängligt |
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
> | Åtgärd | Microsoft. Media/Media Services/Transforms/Write | Skapa eller uppdatera en transformering |
> | Åtgärd | Microsoft. Media/Media Services/Write | Skapa eller uppdatera ett Media Services konto |
> | Åtgärd | Microsoft. Media/Operations/Read | Hämta tillgängliga åtgärder |
> | Åtgärd | Microsoft. media/register/åtgärd | Registrerar prenumerationen för Media Services Resource Provider och gör det möjligt att skapa Media Services-konton |
> | Åtgärd | Microsoft.Media/unregister/action | Avregistrerar prenumerationen för Media Services Resource Provider |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/assessmentOptions/Read | Hämtar de bedömnings alternativ som är tillgängliga på den aktuella platsen |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/bedömningar/läsa | Visar en lista över utvärderingar inom ett projekt |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Delete | Tar bort bedömnings projektet |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/assessments/assessedmachines/Read | Hämta egenskaperna för en utvärderad dator |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/assessments/Delete | Tar bort en utvärdering |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/assessments/DownloadURL/Action | Laddar ned en utvärderings rapports URL |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/assessments/Read | Hämtar egenskaperna för en utvärdering |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/bedömningar/Write | Skapar en ny utvärdering eller uppdaterar en befintlig utvärdering |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/Delete | Tar bort en grupp |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/Read | Hämta egenskaperna för en grupp |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/updateMachines/Action | Uppdatera grupp genom att lägga till eller ta bort datorer |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Groups/Write | Skapar en ny grupp eller uppdaterar en befintlig grupp |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/hypervcollectors/Delete | Tar bort HyperV-insamlaren |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/hypervcollectors/Read | Hämtar egenskaperna för HyperV-insamlaren |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/hypervcollectors/Write | Skapar en ny HyperV-insamlare eller uppdaterar en befintlig HyperV-insamlare |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Machines/Read | Hämtar egenskaperna för en dator |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Read | Hämtar egenskaperna för bedömnings projekt |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Tar bort VMware-insamlaren |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Hämtar egenskaperna för VMware-insamlaren |
> | Åtgärd | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Skapar en ny VMware-insamlare eller uppdaterar en befintlig VMware-insamlare |
> | Åtgärd | Microsoft. Migrate/assessmentprojects/Write | Skapar ett nytt bedömnings projekt eller uppdaterar ett befintligt bedömnings projekt |
> | Åtgärd | Microsoft. Migrate/locations/assessmentOptions/Read | Hämtar de bedömnings alternativ som är tillgängliga på den aktuella platsen |
> | Åtgärd | Microsoft. Migrate/locations/checknameavailability/Action | Kontrollerar tillgänglighet för resurs namnet för den aktuella prenumerationen på den aktuella platsen |
> | Åtgärd | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Hämtar egenskaperna för en databas instans |
> | Åtgärd | Microsoft. Migrate/migrateprojects/databaser/läsa | Hämtar egenskaperna för en databas |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Delete | Tar bort ett migrerat projekt |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Machines/Read | Hämtar egenskaperna för en dator |
> | Åtgärd | Microsoft. Migrate/migrateprojects/MigrateEvents/Delete | Tar bort en migrerings händelse |
> | Åtgärd | Microsoft. Migrate/migrateprojects/MigrateEvents/Read | Hämtar egenskaperna för ett migrerande händelser. |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Read | Hämtar egenskaper för migrera projekt |
> | Åtgärd | Microsoft. Migrate/migrateprojects/RefreshSummary/Action | Uppdaterar sammanfattningen av den migrerade projektet |
> | Åtgärd | Microsoft. Migrate/migrateprojects/registerTool/Action | Registrerar verktyget för ett migrera projekt |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Solutions/cleanupData/Action | Rensa informationen om migrerade projekt lösningar |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Solutions/Delete | Tar bort en lösning för att migrera projekt |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Solutions/getconfig/Action | Hämtar konfigurationen för migrera projekt lösning |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Solutions/Read | Hämtar egenskaper för att migrera projekt lösning |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Solutions/Write | Skapar en ny lösning för att migrera projekt eller uppdaterar en befintlig lösning för att migrera projekt |
> | Åtgärd | Microsoft. Migrate/migrateprojects/Write | Skapar ett nytt migrerings projekt eller uppdaterar ett befintligt migrerat projekt |
> | Åtgärd | Microsoft. Migrate/Operations/Read | Visar en lista över åtgärder som är tillgängliga på Microsoft. Migrera resurs leverantör |
> | Åtgärd | Microsoft. Migrate/projekt/bedömningar/läsa | Visar en lista över utvärderingar inom ett projekt |
> | Åtgärd | Microsoft. Migrate/Projects/Delete | Tar bort projektet |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/assessments/assessedmachines/Read | Hämta egenskaperna för en utvärderad dator |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/assessments/Delete | Tar bort en utvärdering |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/assessments/DownloadURL/Action | Laddar ned en utvärderings rapports URL |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/assessments/Read | Hämtar egenskaperna för en utvärdering |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/assessments/Write | Skapar en ny utvärdering eller uppdaterar en befintlig utvärdering |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/Delete | Tar bort en grupp |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/Read | Hämta egenskaperna för en grupp |
> | Åtgärd | Microsoft. Migrate/Projects/Groups/Write | Skapar en ny grupp eller uppdaterar en befintlig grupp |
> | Åtgärd | Microsoft. Migrate/Projects/Keys/åtgärd | Hämtar delade nycklar för projektet |
> | Åtgärd | Microsoft. Migrate/Projects/Machines/Read | Hämtar egenskaperna för en dator |
> | Åtgärd | Microsoft. Migrate/Projects/Read | Hämtar egenskaperna för ett projekt |
> | Åtgärd | Microsoft. Migrate/Projects/Write | Skapar ett nytt projekt eller uppdaterar ett befintligt projekt |
> | Åtgärd | Microsoft. Migrate/registrera/åtgärd | Registrerar prenumeration med Microsoft. Migrate Resource Provider |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. MixedReality/register/åtgärd | Registrerar en prenumeration för resurs leverantören för Mixad verklighet. |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Skapa spatiala ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Ta bort avstånds ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | Åtgärd | Microsoft. MixedReality/spatialAnchorsAccounts/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för Microsoft. MixedReality/spatialAnchorsAccounts |
> | Åtgärd | Microsoft. MixedReality/spatialAnchorsAccounts/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för Microsoft. MixedReality/spatialAnchorsAccounts |
> | Åtgärd | Microsoft. MixedReality/spatialAnchorsAccounts/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för Microsoft. MixedReality/spatialAnchorsAccounts |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uppdatera egenskaper för spatiala ankare |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. NetApp/locations/checkfilepathavailability/Action | Kontrol lera om fil Sök vägen är tillgänglig |
> | Åtgärd | Microsoft. NetApp/locations/checknameavailability/Action | Kontrol lera om resurs namnet är tillgängligt |
> | Åtgärd | Microsoft.NetApp/locations/operationresults/read | Läser en åtgärds resultat resurs. |
> | Åtgärd | Microsoft. NetApp/locations/Read | Läser en resurs för tillgänglighets kontroll. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Tar bort en pool resurs. |
> | Åtgärd | Microsoft. NetApp/netAppAccounts/capacityPools/Read | Läser en pool resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Tar bort en volym resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Läser en resurs för monterings mål. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Läser en volym resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Tar bort en ögonblicks bild resurs. |
> | Åtgärd | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/snaps hots/Read | Läser en ögonblicks bild resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Skriver en ögonblicks bild resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Skriver en volym resurs. |
> | Åtgärd | Microsoft.NetApp/netAppAccounts/capacityPools/write | Skriver en grupp resurs. |
> | Åtgärd | Microsoft. NetApp/netAppAccounts/Delete | Tar bort en konto resurs. |
> | Åtgärd | Microsoft. NetApp/netAppAccounts/Read | Läser en konto resurs. |
> | Åtgärd | Microsoft. NetApp/netAppAccounts/Write | Skriver en konto resurs. |
> | Åtgärd | Microsoft.NetApp/Operations/read | Läser en åtgärds resurser. |
> | Åtgärd | Microsoft. NetApp/register/åtgärd | Registrerar prenumeration med Microsoft. NetApp Resource Provider |
> | Åtgärd | Microsoft. NetApp/avregistrera/åtgärd | Avregistrerar prenumeration med Microsoft. NetApp Resource Provider |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Hämta Application Gateway tillgängliga begärandehuvuden |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Hämta Application Gateway tillgängligt svars huvud |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Hämta Application Gateway tillgängliga servervariabler |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway en fördefinierad SSL-princip |
> | Åtgärd | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway tillgängliga SSL-alternativ |
> | Åtgärd | Microsoft. Network/applicationGatewayAvailableWafRuleSets/Read | Hämtar Application Gateway tillgängliga WAF-regel uppsättningar |
> | Åtgärd | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter till en programgateways backend-adresspool. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/applicationGateways/backendhealth/Action | Hämtar en server del hälso tillstånd för Application Gateway |
> | Åtgärd | Microsoft. Network/applicationGateways/Delete | Tar bort en Application Gateway |
> | Åtgärd | Microsoft. Network/applicationGateways/getBackendHealthOnDemand/Action | Hämtar en server del hälso tillstånd för Programgateway på begäran för den aktuella http-inställningen och backend-poolen |
> | Åtgärd | Microsoft.Network/applicationGateways/read | Hämtar en Application Gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/start/action | Startar en Application Gateway |
> | Åtgärd | Microsoft.Network/applicationGateways/stop/action | Stoppar en Programgateway |
> | Åtgärd | Microsoft.Network/applicationGateways/write | Skapar en Application Gateway eller uppdaterar en Programgateway |
> | Åtgärd | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Tar bort en Application Gateway WAF-princip |
> | Åtgärd | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Read | Hämtar en Application Gateway WAF-princip |
> | Åtgärd | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Skapar en Application Gateway WAF-princip eller uppdaterar en Application Gateway WAF-princip |
> | Åtgärd | Microsoft. Network/applicationSecurityGroups/Delete | Tar bort en program säkerhets grupp |
> | Åtgärd | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action | Ansluter en IP-konfiguration till program säkerhets grupper. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Kopplar en säkerhets regel till program säkerhets grupper. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Listar IP-konfigurationer i ApplicationSecurityGroup |
> | Åtgärd | Microsoft. Network/applicationSecurityGroups/Read | Hämtar ett ID för program säkerhets gruppen. |
> | Åtgärd | Microsoft.Network/applicationSecurityGroups/write | Skapar en program säkerhets grupp eller uppdaterar en befintlig program säkerhets grupp. |
> | Åtgärd | Microsoft.Network/azureFirewallFqdnTags/read | Hämtar Azure Firewall FQDN-Taggar |
> | Åtgärd | Microsoft. Network/azurefirewalls/Delete | Ta bort Azure-brandvägg |
> | Åtgärd | Microsoft.Network/azurefirewalls/read | Hämta Azure-brandvägg |
> | Åtgärd | Microsoft.Network/azurefirewalls/write | Skapar eller uppdaterar en Azure-brandvägg |
> | Åtgärd | Microsoft. Network/bastionHosts/Delete | Tar bort en skydds-värd |
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
> | Åtgärd | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Hämtar VPN-enhetens konfiguration av VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft. Network/Connections/Write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/delete | Tar bort en DDoS anpassad princip |
> | Åtgärd | Microsoft. Network/ddosCustomPolicies/Read | Hämtar en DDoS anpassad princip definitions definition |
> | Åtgärd | Microsoft.Network/ddosCustomPolicies/write | Skapar en anpassad princip för DDoS eller uppdaterar en befintlig DDoS anpassad princip |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/delete | Tar bort en DDoS Protection Plan |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/join/action | Kopplar en DDoS Protection-plan. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/read | Hämtar ett DDoS Protection Plan |
> | Åtgärd | Microsoft.Network/ddosProtectionPlans/write | Skapar en DDoS Protection plan eller uppdaterar en DDoS Protection Plan  |
> | Åtgärd | Microsoft. Network/dnsoperationresults/Read | Hämtar resultat från en DNS-åtgärd |
> | Åtgärd | Microsoft. Network/dnsoperationstatuses/Read | Hämtar status för en DNS-åtgärd  |
> | Åtgärd | Microsoft. Network/dnszones/A/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "A" från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/A/Read | Hämta post uppsättningen av typen "A" i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/A/write | Skapa eller uppdatera en post uppsättning av typen "A" i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv AAAA från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/AAAA/Read | Hämta post uppsättningen av typen AAAA i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/AAAA/write | Skapa eller uppdatera en post uppsättning av typen AAAA i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/dnszones/all/Read | Hämtar DNS-postuppsättningar mellan typer |
> | Åtgärd | Microsoft.Network/dnszones/CAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv CAA från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/CAA/Read | Hämta post uppsättningen av typen ' CAA ' i JSON-format. Post uppsättningen innehåller TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CAA/write | Skapa eller uppdatera en post uppsättning av typen ' CAA ' i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/delete | Ta bort post uppsättningen med ett angivet namn och skriv CNAME från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/CNAME/Read | Hämta post uppsättningen av typen CNAME i JSON-format. Post uppsättningen innehåller TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/CNAME/write | Skapa eller uppdatera en post uppsättning av typen CNAME i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/dnszones/Delete | Ta bort DNS-zonen i JSON-format. Zon egenskaperna är taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. |
> | Åtgärd | Microsoft. Network/dnszones/MX/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "MX" från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/MX/Read | Hämta post uppsättningen av typen MX i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/MX/write | Skapa eller uppdatera en post uppsättning av typen MX i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/dnszones/NS/Delete | Tar bort DNS-postuppsättningen av typen NS |
> | Åtgärd | Microsoft. Network/dnszones/NS/Read | Hämtar DNS-post uppsättning av typen NS |
> | Åtgärd | Microsoft. Network/dnszones/NS/Write | Skapar eller uppdaterar DNS-post uppsättning av typen NS |
> | Åtgärd | Microsoft. Network/dnszones/PTR/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "PTR" från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/PTR/Read | Hämta post uppsättningen av typen "PTR" i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft. Network/dnszones/PTR/Write | Skapa eller uppdatera en post uppsättning av typen PTR i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/dnszones/Read | Hämta DNS-zonen i JSON-format. Zon egenskaperna är taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. Observera att det här kommandot inte hämtar de post uppsättningar som ingår i zonen. |
> | Åtgärd | Microsoft. Network/dnszones/Recordset/Read | Hämtar DNS-postuppsättningar mellan typer |
> | Åtgärd | Microsoft. Network/dnszones/SOA/Read | Hämtar DNS-post uppsättning av typen SOA |
> | Åtgärd | Microsoft. Network/dnszones/SOA/Write | Skapar eller uppdaterar DNS-post uppsättning av typen SOA |
> | Åtgärd | Microsoft. Network/dnszones/SRV/Delete | Ta bort post uppsättningen med det angivna namnet och skriv-SRV från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/SRV/Read | Hämta post uppsättningen av typen "SRV" i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/SRV/write | Skapa eller uppdatera post uppsättning av typen SRV |
> | Åtgärd | Microsoft.Network/dnszones/TXT/delete | Ta bort post uppsättningen med ett angivet namn och skriv TXT från en DNS-zon. |
> | Åtgärd | Microsoft. Network/dnszones/TXT/Read | Hämta post uppsättningen av typen ' TXT ' i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/dnszones/TXT/write | Skapa eller uppdatera en post uppsättning av typen ' TXT ' i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/dnszones/Write | Skapa eller uppdatera en DNS-zon i en resurs grupp.  Används för att uppdatera taggarna på en DNS-zon resurs. Observera att det här kommandot inte kan användas för att skapa eller uppdatera post uppsättningar i zonen. |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/Authorizations/Delete | Tar bort en ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/auktoriseringar/läsa | Hämtar en ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/authorizations/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-auktorisering |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/Delete | Tar bort en ExpressRouteCircuit |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/JOIN/åtgärd | Ansluter till en Express Route-krets. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/peering/arpTables/Read | Hämtar en ExpressRouteCircuit peering-ArpTable |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/peering/Connections/Delete | Tar bort en ExpressRouteCircuit-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Hämtar en ExpressRouteCircuit-anslutning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-anslutnings resurs |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/peering/to delete | Tar bort en ExpressRouteCircuit-peering |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/peering/peerConnections/Read | Hämtar anslutning till peer Express Route-kretsen |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/motparter/Read | Hämtar en ExpressRouteCircuit-peering |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/peering/routeTables/Read | Hämtar en ExpressRouteCircuit peering-RouteTable |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/peering/routeTablesSummary/Read | Hämtar en ExpressRouteCircuit peering RouteTable-Sammanfattning |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Hämtar en ExpressRouteCircuit-peering stat |
> | Åtgärd | Microsoft. Network/expressRouteCircuits/motparter/Write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-peering |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/read | Hämta en ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/stats/read | Hämtar en ExpressRouteCircuit stat |
> | Åtgärd | Microsoft.Network/expressRouteCircuits/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/join/action | Ansluter till en Express vägs kors anslutning. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/expressRouteCrossConnections/peering/arpTables/Read | Hämtar en ARP-tabell med snabb flödes Cross-anslutning |
> | Åtgärd | Microsoft. Network/expressRouteCrossConnections/peering/to delete | Tar bort en Express-routning mellan anslutningar |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/peerings/read | Hämtar en Express routning mellan anslutningar |
> | Åtgärd | Microsoft. Network/expressRouteCrossConnections/peering/routeTables/Read | Hämtar en Express vägs kors anslutning för peering Route Table |
> | Åtgärd | Microsoft. Network/expressRouteCrossConnections/peering/routeTableSummary/Read | Hämtar en Sammanhangs oberoende kopplings väg tabell Sammanfattning för en Express Route |
> | Åtgärd | Microsoft. Network/expressRouteCrossConnections/motparter/Write | Skapar en ExpressRoute-peering med Express routning eller uppdaterar en befintlig snabb routning mellan anslutningar |
> | Åtgärd | Microsoft.Network/expressRouteCrossConnections/read | Hämta kors anslutning för Express Route |
> | Åtgärd | Microsoft. Network/expressRouteGateways/expressRouteConnections/Delete | Tar bort en Express Route-anslutning |
> | Åtgärd | Microsoft. Network/expressRouteGateways/expressRouteConnections/Read | Hämtar en Express Route-anslutning |
> | Åtgärd | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Skapar en Express Route-anslutning eller uppdaterar en befintlig Express Route-anslutning |
> | Åtgärd | Microsoft. Network/expressRouteGateways/JOIN/åtgärd | Ansluter till en Express Route-Gateway. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/expressRouteGateways/Read | Hämta en Express Route-Gateway |
> | Åtgärd | Microsoft.Network/expressRoutePorts/delete | Tar bort ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePorts/join/action | Ansluter till Express Route-portar. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/expressRoutePorts/links/read | Hämtar ExpressRouteLink |
> | Åtgärd | Microsoft.Network/expressRoutePorts/read | Hämtar ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePorts/write | Skapar eller uppdaterar ExpressRoutePorts |
> | Åtgärd | Microsoft.Network/expressRoutePortsLocations/read | Hämta platser för Express Route-portar |
> | Åtgärd | Microsoft.Network/expressRouteServiceProviders/read | Hämtar Express Route Service-leverantörer |
> | Åtgärd | Microsoft. Network/firewallPolicies/Delete | Tar bort en brand Väggs princip |
> | Åtgärd | Microsoft. Network/firewallPolicies/JOIN/åtgärd | Ansluter till en brand Väggs princip. Det går inte att avisera. |
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
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Read | Hämtar en brand Väggs princip för webb program |
> | Åtgärd | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Skapar eller uppdaterar en brand Väggs princip för webb program |
> | Åtgärd | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en backend-adresspool för belastnings utjämning. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/backendAddressPools/Read | Hämtar en definition för Server dels adress för belastnings utjämning |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/Delete | Tar bort en belastningsutjämnare |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/frontendIPConfigurations/JOIN/åtgärd | Ansluter till en IP-konfiguration för Load Balancer-frontend. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/frontendIPConfigurations/Read | Hämtar en IP-konfigurations definition för belastningsutjämnarens klient del |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter en inkommande NAT-pool för belastningsutjämnaren. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/inboundNatPools/Read | Hämtar en belastnings Utjämnings definition för inkommande NAT-pool |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/delete | Tar bort en inkommande NAT-regel för belastnings utjämning |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/read | Hämtar en inkommande NAT-regel definition för belastnings utjämning |
> | Åtgärd | Microsoft.Network/loadBalancers/inboundNatRules/write | Skapar en inkommande NAT-regel för belastnings utjämning eller uppdaterar en befintlig inkommande NAT-regel för belastnings utjämning |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/loadBalancingRules/Read | Hämtar en regel definition för belastnings utjämning för belastnings utjämning |
> | Åtgärd | Microsoft.Network/loadBalancers/networkInterfaces/read | Hämtar referenser till alla nätverks gränssnitt under en belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/outboundRules/read | Hämtar en regel definition för utgående belastningsutjämnare |
> | Åtgärd | Microsoft.Network/loadBalancers/probes/join/action | Tillåter att avsökningar av en belastningsutjämnare används. Till exempel kan den här behörighetens egenskap healthProbe-egenskap för VM Scale set referera till avsökningen. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/PROBES/Read | Hämtar en belastnings Utjämnings avsökning |
> | Åtgärd | Microsoft.Network/loadBalancers/read | Hämtar en belastnings Utjämnings definition |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/virtualMachines/Read | Hämtar referenser till alla virtuella datorer under en belastningsutjämnare |
> | Åtgärd | Microsoft. Network/belastningsutjämnare/Write | Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare |
> | Åtgärd | Microsoft. Network/localnetworkgateways/Delete | Tar bort LocalNetworkGateway |
> | Åtgärd | Microsoft. Network/localnetworkgateways/Read | Hämtar LocalNetworkGateway |
> | Åtgärd | Microsoft. Network/localnetworkgateways/Write | Skapar eller uppdaterar en befintlig LocalNetworkGateway |
> | Åtgärd | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Hämtar automatiskt godkända privata länk tjänster |
> | Åtgärd | Microsoft. Network/locations/availableDelegations/Read | Hämtar tillgängliga delegeringar |
> | Åtgärd | Microsoft. Network/locations/availablePrivateEndpointTypes/Read | Hämtar tillgängliga privata slut punkts resurser |
> | Åtgärd | Microsoft. Network/locations/availableServiceAliases/Read | Hämtar tillgängliga tjänst Ali Aset |
> | Åtgärd | Microsoft. Network/locations/bareMetalTenants/Action | Allokerar eller validerar en Bare Metal-klient |
> | Åtgärd | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Kontrollerar accelererat nätverks stöd |
> | Åtgärd | Microsoft. Network/locations/checkDnsNameAvailability/Read | Kontrollerar om DNS-etiketten är tillgänglig på den angivna platsen |
> | Åtgärd | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Kontrollerar synlighet för privata länk tjänster |
> | Åtgärd | Microsoft.Network/locations/operationResults/read | Hämtar åtgärds resultat för en asynkron POST-eller BORTTAGNINGs åtgärd |
> | Åtgärd | Microsoft. Network/locations/Operations/Read | Hämtar åtgärds resurs som representerar status för en asynkron åtgärd |
> | Åtgärd | Microsoft.Network/locations/serviceTags/read | Hämta Service märken |
> | Åtgärd | Microsoft. Network/locations/supportedVirtualMachineSizes/Read | Hämtar storlekar för virtuella datorer som stöds |
> | Åtgärd | Microsoft. Network/locations/usages/Read | Hämtar användnings statistik för resurser |
> | Åtgärd | Microsoft. Network/locations/virtualNetworkAvailableEndpointServices/Read | Hämtar en lista över tillgängliga Virtual Network slut punkts tjänster |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/delete | Tar bort en princip för nätverks avsikt |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/read | Hämtar en beskrivning av princip för nätverks avsikt |
> | Åtgärd | Microsoft.Network/networkIntentPolicies/write | Skapar en princip för nätverks avsikt eller uppdaterar en befintlig princip för nätverks avsikt |
> | Åtgärd | Microsoft.Network/networkInterfaces/delete | Tar bort ett nätverks gränssnitt |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hämta nätverks säkerhets grupper som kon figurer ATS på nätverks gränssnittet för den virtuella datorn |
> | Åtgärd | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Hämta routningstabellen som kon figurer ATS på nätverks gränssnittet för den virtuella datorn |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Ansluter till en IP-konfiguration för nätverks gränssnittet. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/networkInterfaces/ipconfigurations/read | Hämtar en IP-konfigurationsfil för nätverks gränssnitt.  |
> | Åtgärd | Microsoft.Network/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverks gränssnitt. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/networkInterfaces/loadBalancers/read | Hämtar alla belastningsutjämnare som nätverks gränssnittet ingår i |
> | Åtgärd | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverks gränssnitt.  |
> | Åtgärd | Microsoft. Network/networkInterfaces/tapConfigurations/Delete | Tar bort ett nätverks gränssnitt som trycker på konfiguration. |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/read | Hämtar ett nätverks gränssnitt genom att trycka på konfiguration. |
> | Åtgärd | Microsoft.Network/networkInterfaces/tapConfigurations/write | Skapar ett nätverks gränssnitt för att trycka på konfiguration eller uppdatera ett befintligt nätverks gränssnitt tryck på konfiguration. |
> | Åtgärd | Microsoft.Network/networkInterfaces/write | Skapar ett nätverks gränssnitt eller uppdaterar ett befintligt nätverks gränssnitt.  |
> | Åtgärd | Microsoft.Network/networkProfiles/delete | Tar bort en nätverks profil |
> | Åtgärd | Microsoft.Network/networkProfiles/read | Hämtar en nätverks profil |
> | Åtgärd | Microsoft.Network/networkProfiles/removeContainers/action | Tar bort behållare |
> | Åtgärd | Microsoft.Network/networkProfiles/setContainers/action | Anger behållare |
> | Åtgärd | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Anger behållarens nätverks gränssnitt |
> | Åtgärd | Microsoft.Network/networkProfiles/write | Skapar eller uppdaterar en nätverks profil |
> | Åtgärd | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/Read | Hämtar en standard säkerhets regel definition |
> | Åtgärd | Microsoft. Network/networkSecurityGroups/Delete | Tar bort en nätverks säkerhets grupp |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/read | Hämtar en definition för nätverks säkerhets grupp |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/delete | Tar bort en säkerhets regel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/read | Hämtar en säkerhets regel definition |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/securityRules/write | Skapar en säkerhets regel eller uppdaterar en befintlig säkerhets regel |
> | Åtgärd | Microsoft.Network/networkSecurityGroups/write | Skapar en nätverks säkerhets grupp eller uppdaterar en befintlig nätverks säkerhets grupp |
> | Åtgärd | Microsoft. Network/networkWatchers/availableProvidersList/Action | Returnerar alla tillgängliga Internet tjänst leverantörer för en angiven Azure-region. |
> | Åtgärd | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returnerar den relativa latens poängen för Internet tjänst leverantörer från en angiven plats till Azure-regioner. |
> | Åtgärd | Microsoft. Network/networkWatchers/configureFlowLog/Action | Konfigurerar flödes loggning för en mål resurs. |
> | Åtgärd | Microsoft. Network/networkWatchers/connectionMonitors/Delete | Tar bort en anslutnings övervakare |
> | Åtgärd | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Fråga om övervakning av anslutningar mellan angivna slut punkter |
> | Åtgärd | Microsoft. Network/networkWatchers/connectionMonitors/Read | Hämta information om anslutnings övervakaren |
> | Åtgärd | Microsoft. Network/networkWatchers/connectionMonitors/start/Action | Starta övervakning av anslutningar mellan angivna slut punkter |
> | Åtgärd | Microsoft. Network/networkWatchers/connectionMonitors/stop/action | Stoppa/pausa övervakning av anslutningar mellan angivna slut punkter |
> | Åtgärd | Microsoft. Network/networkWatchers/connectionMonitors/Write | Skapar en anslutnings övervakare |
> | Åtgärd | Microsoft. Network/networkWatchers/connectivityCheck/Action | Verifierar möjligheten att upprätta en direkt TCP-anslutning från en virtuell dator till en specifik slut punkt, inklusive en annan virtuell dator eller en godtycklig fjärrserver. |
> | Åtgärd | Microsoft. Network/networkWatchers/Delete | Tar bort en nätverks övervakare |
> | Åtgärd | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returnerar om paketet tillåts eller nekas till eller från ett visst mål. |
> | Åtgärd | Microsoft. Network/networkWatchers/linser/Delete | Tar bort en lins |
> | Åtgärd | Microsoft. Network/networkWatchers/linser/fråga/åtgärd | Fråga vid övervakning av nätverks trafik på en angiven slut punkt |
> | Åtgärd | Microsoft. Network/networkWatchers/linser/läsa | Hämta lins information |
> | Åtgärd | Microsoft. Network/networkWatchers/linser/start/åtgärd | Starta övervakning av nätverks trafik på en angiven slut punkt |
> | Åtgärd | Microsoft. Network/networkWatchers/linser/stoppa/åtgärd | Stoppa/pausa övervakning av nätverks trafik på en angiven slut punkt |
> | Åtgärd | Microsoft. Network/networkWatchers/linser/Write | Skapar en lins |
> | Åtgärd | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostik för nätverks konfiguration. |
> | Åtgärd | Microsoft. Network/networkWatchers/nextHop/Action | Returnera nästa hopp typ och nästa hopp-IP-adress för en angiven mål-och mål-IP-adress. |
> | Åtgärd | Microsoft. Network/networkWatchers/packetCaptures/Delete | Tar bort en paket avbildning |
> | Åtgärd | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Hämtar information om egenskaper och status för en paket fångst resurs. |
> | Åtgärd | Microsoft. Network/networkWatchers/packetCaptures/Read | Hämta paket fångsts definitionen |
> | Åtgärd | Microsoft. Network/networkWatchers/packetCaptures/stop/action | Stoppa den pågående paket insamlings sessionen. |
> | Åtgärd | Microsoft. Network/networkWatchers/packetCaptures/Write | Skapar en paket fångst |
> | Åtgärd | Microsoft. Network/networkWatchers/pingMeshes/Delete | Tar bort en PingMesh |
> | Åtgärd | Microsoft. Network/networkWatchers/pingMeshes/Read | Hämta information om PingMesh |
> | Åtgärd | Microsoft. Network/networkWatchers/pingMeshes/start/Action | Starta PingMesh mellan angivna virtuella datorer |
> | Åtgärd | Microsoft. Network/networkWatchers/pingMeshes/stop/action | Stoppa PingMesh mellan angivna virtuella datorer |
> | Åtgärd | Microsoft. Network/networkWatchers/pingMeshes/Write | Skapar en PingMesh |
> | Åtgärd | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Batch-fråga för övervakning av anslutningar mellan angivna slut punkter |
> | Åtgärd | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Hämtar status för flödes loggning på en resurs. |
> | Åtgärd | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Hämtar fel söknings resultatet från den tidigare körningen eller fel söknings åtgärden som körs. |
> | Åtgärd | Microsoft. Network/networkWatchers/Read | Hämta Network Watcher-definitionen |
> | Åtgärd | Microsoft.Network/networkWatchers/securityGroupView/action | Visa de konfigurerade och effektiva reglerna för nätverks säkerhets grupper som tillämpas på en virtuell dator. |
> | Åtgärd | Microsoft. Network/networkWatchers/topologi/åtgärd | Hämtar resursernas vy på nätverks nivå och deras relationer i en resurs grupp. |
> | Åtgärd | Microsoft. Network/networkWatchers/Felsök/åtgärd | Startar fel sökning på en nätverks resurs i Azure. |
> | Åtgärd | Microsoft. Network/networkWatchers/Write | Skapar en nätverks övervakare eller uppdaterar en befintlig nätverks övervakare |
> | Åtgärd | Microsoft.Network/operations/read | Hämta tillgängliga åtgärder |
> | Åtgärd | Microsoft. Network/p2sVpnGateways/Delete | Tar bort en P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generera VPN-profil för P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Hämtar hälso tillståndet för P2S VPN-anslutning för P2SVpnGateway |
> | Åtgärd | Microsoft. Network/p2sVpnGateways/getp2svpnconnectionhealthdetailed/Action | Hämtar en hälso status för P2S VPN-anslutning som är detaljerad för P2SVpnGateway |
> | Åtgärd | Microsoft.Network/p2sVpnGateways/read | Hämtar en P2SVpnGateway. |
> | Åtgärd | Microsoft. Network/p2sVpnGateways/Write | Placerar en P2SVpnGateway. |
> | Åtgärd | Microsoft.Network/privateDnsOperationResults/read | Hämtar resultat från en Privat DNS-åtgärd |
> | Åtgärd | Microsoft.Network/privateDnsOperationStatuses/read | Hämtar status för en Privat DNS-åtgärd |
> | Åtgärd | Microsoft. Network/privateDnsZones/A/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "A" från en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/A/Read | Hämta post uppsättningen av typen "A" i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/A/write | Skapa eller uppdatera en post uppsättning av typen "A" i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv AAAA från en Privat DNS zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/read | Hämta post uppsättningen av typen AAAA i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/AAAA/write | Skapa eller uppdatera en post uppsättning av typen AAAA i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/privateDnsZones/ALL/Read | Hämtar Privat DNS post uppsättningar mellan typer |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/delete | Ta bort post uppsättningen med ett angivet namn och skriv CNAME från en Privat DNS zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/read | Hämta post uppsättningen av typen CNAME i en Privat DNS zon i JSON-format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/CNAME/write | Skapa eller uppdatera en post uppsättning av typen CNAME i en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/Delete | Ta bort en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/MX/Delete | Ta bort post uppsättningen med ett angivet namn och skriv in MX från en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/MX/Read | Hämta post uppsättningen av typen MX i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/MX/write | Skapa eller uppdatera en post uppsättning av typen MX i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/privateDnsZones/PTR/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "PTR" från en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/PTR/Read | Hämta post uppsättningen av typen ' PTR ' i en Privat DNS zon, i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft. Network/privateDnsZones/PTR/Write | Skapa eller uppdatera en post uppsättning av typen PTR i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft. Network/privateDnsZones/Read | Hämta Privat DNS zon egenskaper i JSON-format. Observera att det här kommandot inte hämtar de virtuella nätverk som Privat DNS zonen är länkad till eller vilka post uppsättningar som ingår i zonen. |
> | Åtgärd | Microsoft. Network/privateDnsZones/Recordset/Read | Hämtar Privat DNS post uppsättningar mellan typer |
> | Åtgärd | Microsoft. Network/privateDnsZones/SOA/Read | Hämta post uppsättningen av typen SOA i en Privat DNS zon i JSON-format. |
> | Åtgärd | Microsoft. Network/privateDnsZones/SOA/Write | Uppdatera en post uppsättning av typen SOA i en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/SRV/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "SRV" från en Privat DNS zon. |
> | Åtgärd | Microsoft. Network/privateDnsZones/SRV/Read | Hämta post uppsättningen av typen SRV i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/SRV/write | Skapa eller uppdatera en post uppsättning av typen SRV i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/delete | Ta bort post uppsättningen med ett angivet namn och skriv TXT från en Privat DNS zon. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/read | Hämta post uppsättningen av typen ' TXT ' i en Privat DNS zon, i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Åtgärd | Microsoft.Network/privateDnsZones/TXT/write | Skapa eller uppdatera en post uppsättning av typen ' TXT ' i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Ta bort en Privat DNS zon länk till det virtuella nätverket. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Hämta Privat DNS zon länken till det virtuella nätverkets egenskaper i JSON-format. |
> | Åtgärd | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Skapa eller uppdatera en Privat DNS zon länk till ett virtuellt nätverk. |
> | Åtgärd | Microsoft. Network/privateDnsZones/Write | Skapa eller uppdatera en Privat DNS zon i en resurs grupp. Observera att det här kommandot inte kan användas för att skapa eller uppdatera virtuella nätverks länkar eller post uppsättningar i zonen. |
> | Åtgärd | Microsoft. Network/privateEndpointRedirectMaps/Read | Hämtar en privat slut punkt RedirectMap |
> | Åtgärd | Microsoft. Network/privateEndpointRedirectMaps/Write | Skapar en privat slut punkt RedirectMap eller uppdaterar en befintlig privat slut punkt RedirectMap |
> | Åtgärd | Microsoft. Network/privateEndpoints/Delete | Tar bort en privat slut punkts resurs. |
> | Åtgärd | Microsoft. Network/privateEndpoints/Read | Hämtar en privat slut punkts resurs. |
> | Åtgärd | Microsoft. Network/privateEndpoints/Write | Skapar en ny privat slut punkt eller uppdaterar en befintlig privat slut punkt. |
> | Åtgärd | Microsoft.Network/privateLinkServices/delete | Tar bort en tjänst resurs för privata länkar. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Tar bort en privat slut punkts anslutning. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Hämtar en anslutnings definition för privat slut punkt. |
> | Åtgärd | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Skapar en ny privat slut punkts anslutning eller uppdaterar en befintlig privat slut punkts anslutning. |
> | Åtgärd | Microsoft.Network/privateLinkServices/read | Hämtar en privat länk tjänst resurs. |
> | Åtgärd | Microsoft.Network/privateLinkServices/write | Skapar en ny privat länk-tjänst eller uppdaterar en befintlig privat länk-tjänst. |
> | Åtgärd | Microsoft. Network/publicIPAddresses/Delete | Tar bort en offentlig IP-adress. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/publicIPAddresses/read | Hämtar en offentlig IP-adress definition. |
> | Åtgärd | Microsoft. Network/publicIPAddresses/Write | Skapar en offentlig IP-adress eller uppdaterar en befintlig offentlig IP-adress.  |
> | Åtgärd | Microsoft. Network/publicIPPrefixes/Delete | Tar bort ett offentligt IP-prefix |
> | Åtgärd | Microsoft. Network/publicIPPrefixes/JOIN/åtgärd | Ansluter till en PublicIPPrefix. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/publicIPPrefixes/Read | Hämtar en offentlig IP-prefix definition |
> | Åtgärd | Microsoft. Network/publicIPPrefixes/Write | Skapar ett offentligt IP-prefix eller uppdaterar ett befintligt offentligt IP-prefix |
> | Åtgärd | Microsoft. Network/register/åtgärd | Registrerar prenumerationen |
> | Åtgärd | Microsoft. Network/routeFilters/Delete | Tar bort en flödes filter definition |
> | Åtgärd | Microsoft. Network/routeFilters/JOIN/åtgärd | Ansluter till ett flödes filter. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/routeFilters/Read | Hämtar en flödes filter definition |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/delete | Tar bort en regel definition för flödes filter |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/read | Hämtar en regel definition för väg filter |
> | Åtgärd | Microsoft.Network/routeFilters/routeFilterRules/write | Skapar en regel för väg filter eller uppdaterar en befintlig regel för väg filter |
> | Åtgärd | Microsoft. Network/routeFilters/Write | Skapar ett flödes filter eller uppdaterar ett befintligt flödes filter |
> | Åtgärd | Microsoft. Network/routeTables/Delete | Tar bort en väg tabells definition |
> | Åtgärd | Microsoft. Network/routeTables/JOIN/åtgärd | Kopplar ihop en routningstabell. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/routeTables/Read | Hämtar en väg tabells definition |
> | Åtgärd | Microsoft. Network/routeTables/routes/Delete | Tar bort en flödes definition |
> | Åtgärd | Microsoft. Network/routeTables/routes/Read | Hämtar en flödes definition |
> | Åtgärd | Microsoft. Network/routeTables/routes/Write | Skapar en väg eller uppdaterar en befintlig väg |
> | Åtgärd | Microsoft. Network/routeTables/Write | Skapar en routningstabell eller uppdaterar en befintlig väg tabell |
> | Åtgärd | Microsoft. Network/securegateways/Delete | Ta bort säker gateway |
> | Åtgärd | Microsoft. Network/securegateways/Read | Hämta säker gateway |
> | Åtgärd | Microsoft. Network/securegateways/Write | Skapar eller uppdaterar en säker gateway |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/delete | Tar bort en tjänst slut punkts princip |
> | Åtgärd | Microsoft. Network/serviceEndpointPolicies/JOIN/åtgärd | Ansluter till en tjänst slut punkts princip. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Ansluter ett undernät till tjänstens slut punkts principer. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/serviceEndpointPolicies/Read | Hämtar en beskrivning av tjänstens slut punkts princip |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Tar bort en princip definition för tjänst slut punkt |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Hämtar en beskrivning av en princip definition för tjänst slut punkt |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Skapar en princip definition för tjänst slut punkt eller uppdaterar en befintlig princip definition för tjänst slut punkt |
> | Åtgärd | Microsoft.Network/serviceEndpointPolicies/write | Skapar en tjänst slut punkts princip eller uppdaterar en befintlig tjänst slut punkts princip |
> | Åtgärd | Microsoft.Network/trafficManagerGeographicHierarchies/read | Hämtar Traffic Manager geografisk hierarki som innehåller regioner som kan användas med routningsmetod för geografisk trafik |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Tar bort en Azure-slutpunkt från en befintlig Traffic Managers profil. Traffic Manager upphör att vidarebefordra trafiken till den borttagna Azure-slutpunkt. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Hämtar ett Azure-slutpunkt som tillhör en Traffic Manager profil, inklusive alla egenskaper för den Azure-slutpunkt. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Lägg till en ny Azure-slutpunkt i en befintlig Traffic Manager profil eller uppdatera egenskaperna för en befintlig Azure-slutpunkt i den Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/delete | Ta bort Traffic Manager profilen. Alla inställningar som är associerade med Traffic Manager profilen går förlorade och profilen kan inte längre användas för att dirigera trafik. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Tar bort en extern slut punkt från en befintlig Traffic Manager-profil. Traffic Manager upphör att vidarebefordra trafik till den borttagna externa slut punkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Hämtar en extern slut punkt som tillhör en Traffic Manager profil, inklusive alla egenskaper för den externa slut punkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Lägg till en ny extern slut punkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig extern slut punkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Hämtar Traffic Manager värme kartan för den Traffic Manager profilen som innehåller antal frågor och svars tid för data efter plats och käll-IP. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Tar bort en kapslad slut punkt från en befintlig Traffic Manager-profil. Traffic Manager upphör att vidarebefordra trafik till den borttagna kapslade slut punkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Hämtar en kapslad slut punkt som tillhör en Traffic Manager profil, inklusive alla egenskaper för den kapslade slut punkten. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Lägg till en ny kapslad slut punkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig kapslad slut punkt i Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/read | Hämta profil konfigurationen för Traffic Manager. Detta inkluderar DNS-inställningar, inställningar för trafik cirkulation, inställningar för slut punkts övervakning och listan över slut punkter som vidarebefordras av den här Traffic Manager profilen. |
> | Åtgärd | Microsoft.Network/trafficManagerProfiles/write | Skapa en Traffic Manager-profil eller ändra konfigurationen för en befintlig Traffic Manager-profil.<br>Detta inkluderar aktivering eller inaktive ring av en profil och ändring av DNS-inställningar, inställningar för trafikflöde eller inställningar för slut punkts övervakning.<br>Slut punkter som vidarebefordras av Traffic Managers profilen kan läggas till, tas bort, aktive ras eller inaktive ras. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Tar bort den prenumerations nivå nyckel som används för insamling av real tids användar mått. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/read | Hämtar den prenumerations nivå nyckel som används för insamling av real tids användar mått. |
> | Åtgärd | Microsoft.Network/trafficManagerUserMetricsKeys/write | Skapar en ny prenumerations nivå nyckel som ska användas för insamling av real tids användar mått. |
> | Åtgärd | Microsoft.Network/unregister/action | Avregistrerar prenumerationen |
> | Åtgärd | Microsoft.Network/virtualHubs/delete | Tar bort en virtuell hubb |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Tar bort en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Hämta en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Skapa eller uppdatera en HubVirtualNetworkConnection |
> | Åtgärd | Microsoft.Network/virtualHubs/read | Hämta en virtuell hubb |
> | Åtgärd | Microsoft. Network/virtualHubs/routeTables/Delete | Ta bort en VirtualHubRouteTableV2 |
> | Åtgärd | Microsoft. Network/virtualHubs/routeTables/Read | Hämta en VirtualHubRouteTableV2 |
> | Åtgärd | Microsoft. Network/virtualHubs/routeTables/Write | Skapa eller uppdatera en VirtualHubRouteTableV2 |
> | Åtgärd | Microsoft.Network/virtualHubs/write | Skapa eller uppdatera en virtuell hubb |
> | Åtgärd | microsoft.network/virtualnetworkgateways/connections/read | Hämta VirtualNetworkGatewayConnection |
> | Åtgärd | Microsoft. Network/virtualNetworkGateways/Delete | Tar bort en virtualNetworkGateway |
> | Åtgärd | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generera VpnClient-paket för virtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generera VpnProfile-paket för VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Hämtar virtualNetworkGateway annonserade vägar |
> | Åtgärd | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/Action | Hämtar virtualNetworkGateway status för BGP-peer |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Hämtar virtualnetworkgateway-inlärda vägar |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Hämta hälso tillstånd per VPN-klient för VirtualNetworkGateway |
> | Åtgärd | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Hämta Vpnclient IPsec-parametrar för VirtualNetworkGateway P2S-klienten. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Hämtar URL: en för ett förgenererat profil paket för VPN-klienter |
> | Åtgärd | Microsoft.Network/virtualNetworkGateways/read | Hämtar en VirtualNetworkGateway |
> | Åtgärd | microsoft.network/virtualnetworkgateways/reset/action | Återställer en virtualNetworkGateway |
> | Åtgärd | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | Återställ Vpnclient-delad nyckel för VirtualNetworkGateway P2S-klienten. |
> | Åtgärd | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ange Vpnclient IPsec-parametrar för VirtualNetworkGateway P2S-klienten. |
> | Åtgärd | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Visar en lista över VPN-enheter som stöds |
> | Åtgärd | Microsoft. Network/virtualNetworkGateways/Write | Skapar eller uppdaterar en VirtualNetworkGateway |
> | Åtgärd | Microsoft.Network/virtualNetworks/BastionHosts/action | Hämtar skydds värd referenser i en Virtual Network. |
> | Åtgärd | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Hämtar skydds värd referenser i en Virtual Network. |
> | Åtgärd | Microsoft. Network/virtualNetworks/checkIpAddressAvailability/Read | Kontrol lera om IP-adressen är tillgänglig på det angivna virtuella nätverket |
> | Åtgärd | Microsoft. Network/virtualNetworks/Delete | Tar bort ett virtuellt nätverk |
> | Åtgärd | Microsoft. Network/virtualNetworks/JOIN/åtgärd | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/virtualNetworks/peer/Action | Peer-koppla ett virtuellt nätverk med ett annat virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/Delete | Tar bort en slut punkts princip för sammanhangsbaserade tjänster |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/Read | Hämtar slut punkts principer för kontextuella tjänster |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/contextualServiceEndpointPolicies/Write | Skapar en kontext för sammanhangsbaserade tjänst slut punkt eller uppdaterar en befintlig slut punkts princip för sammanhangsbaserade tjänster |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/Delete | Tar bort ett undernät för virtuellt nätverk |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Förbereder ett undernät genom att tillämpa nödvändiga nätverks principer |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/Read | Hämtar en under näts definition för virtuellt nätverk |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/unprepareNetworkPolicies/Action | Avförbereda ett undernät genom att ta bort tillämpade nätverks principer |
> | Åtgärd | Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read | Hämtar referenser till alla virtuella datorer i ett undernät för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/subnets/write | Skapar ett virtuellt nätverks under nät eller uppdaterar ett befintligt undernät för virtuellt nätverk |
> | Åtgärd | Microsoft. Network/virtualNetworks/usages/Read | Hämta IP-användning för varje undernät i det virtuella nätverket |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualMachines/read | Hämtar referenser till alla virtuella datorer i ett virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Tar bort en virtuell nätverks-peering |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Hämtar en peering-definition för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Skapar en virtuell nätverks-peering eller uppdaterar en befintlig peering för virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworks/write | Skapar ett virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/delete | Ta bort Virtual Network tryck |
> | Åtgärd | Microsoft. Network/virtualNetworkTaps/JOIN/åtgärd | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/read | Hämta Virtual Network tryck |
> | Åtgärd | Microsoft.Network/virtualNetworkTaps/write | Skapa eller uppdatera Virtual Network tryck |
> | Åtgärd | Microsoft. Network/virtualRouters/Delete | Tar bort en VirtualRouter |
> | Åtgärd | Microsoft. Network/virtualRouters/JOIN/åtgärd | Ansluter till en VirtualRouter. Det går inte att avisera. |
> | Åtgärd | Microsoft. Network/virtualRouters/Read | Hämtar en VirtualRouter |
> | Åtgärd | Microsoft. Network/virtualRouters/virtualRouterPeerings/Delete | Tar bort en VirtualRouterPeering |
> | Åtgärd | Microsoft. Network/virtualRouters/virtualRouterPeerings/Read | Hämtar en VirtualRouterPeering |
> | Åtgärd | Microsoft. Network/virtualRouters/virtualRouterPeerings/Write | Skapar en VirtualRouterPeering eller uppdaterar en befintlig VirtualRouterPeering |
> | Åtgärd | Microsoft. Network/virtualRouters/Write | Skapar en VirtualRouter eller uppdaterar en befintlig VirtualRouter |
> | Åtgärd | Microsoft.Network/virtualWans/delete | Tar bort ett virtuellt WAN |
> | Åtgärd | Microsoft. Network/virtualwans/generateVpnProfile/Action | Generera VirtualWanVpnServerConfiguration-VpnProfile |
> | Åtgärd | Microsoft.Network/virtualWans/read | Hämta ett virtuellt WAN |
> | Åtgärd | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Hämtar VirtualWan säkerhets leverantörer som stöds. |
> | Åtgärd | Microsoft.Network/virtualWans/virtualHubs/read | Hämtar alla virtuella hubbar som refererar till ett virtuellt WAN. |
> | Åtgärd | Microsoft.Network/virtualwans/vpnconfiguration/action | Hämtar en VPN-konfiguration |
> | Åtgärd | Microsoft. Network/virtualwans/vpnServerConfigurations/Action | Hämta VirtualWanVpnServerConfigurations |
> | Åtgärd | Microsoft.Network/virtualWans/vpnSites/read | Hämtar alla VPN-platser som hänvisar till ett virtuellt WAN. |
> | Åtgärd | Microsoft.Network/virtualWans/write | Skapa eller uppdatera ett virtuellt WAN |
> | Åtgärd | Microsoft.Network/vpnGateways/delete | Tar bort en VpnGateway. |
> | Åtgärd | microsoft.network/vpngateways/listvpnconnectionshealth/action | Hämtar anslutnings hälsa för alla eller en delmängd av anslutningar på en VpnGateway |
> | Åtgärd | Microsoft.Network/vpnGateways/read | Hämtar en VpnGateway. |
> | Åtgärd | microsoft.network/vpngateways/reset/action | Återställer en VpnGateway |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/delete | Tar bort en VpnConnection. |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/read | Hämtar en VpnConnection. |
> | Åtgärd | Microsoft. Network/vpnGateways/vpnConnections/vpnLinkConnections/Read | Hämtar en anslutning till en VPN-anslutning |
> | Åtgärd | microsoft.network/vpnGateways/vpnConnections/write | Placerar en VpnConnection. |
> | Åtgärd | Microsoft.Network/vpnGateways/write | Placerar en VpnGateway. |
> | Åtgärd | Microsoft. Network/vpnServerConfigurations/Delete | Ta bort VpnServerConfiguration |
> | Åtgärd | Microsoft. Network/vpnServerConfigurations/Read | Hämta VpnServerConfiguration |
> | Åtgärd | Microsoft. Network/vpnServerConfigurations/Write | Skapa eller uppdatera VpnServerConfiguration |
> | Åtgärd | Microsoft.Network/vpnsites/delete | Tar bort en VPN-webbplats resurs. |
> | Åtgärd | Microsoft.Network/vpnsites/read | Hämtar en VPN-webbplats resurs. |
> | Åtgärd | Microsoft. Network/vpnSites/vpnSiteLinks/Read | Hämtar en länk till VPN-platsen |
> | Åtgärd | Microsoft.Network/vpnsites/write | Skapar eller uppdaterar en VPN-webbplats resurs. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Kontrollerar om ett angivet namn områdes resurs namn är tillgängligt i NotificationHub-tjänsten. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Hämta listan över Beskrivning av auktoriseringsregler för namn områden. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Hämta anslutnings strängen till namn området |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Hämta listan över Beskrivning av auktoriseringsregler för namn områden. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Auktoriseringsregel för namnrymd återskapa primär-SecondaryKey, ange den nyckel som måste återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Kontrollerar om ett angivet NotificationHub-namn är tillgängligt i ett namn område. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/Delete | Ta bort namn områdes resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Hämta listan med auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Ta bort auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Hämta anslutnings strängen till Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Hämta listan med auktoriseringsregler för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Auktoriseringsregel för aviserings nav återskapa primär-SecondaryKey, ange den nyckel som måste återskapas |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Skapa auktoriseringsregler för Notification Hub och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Skicka ett test-push-meddelande. |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Ta bort Notification Hub-resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Hämta alla PNS-autentiseringsuppgifter för Notification Hub. Detta inkluderar, WNS, MPNS, APN, GCM och Baidu-autentiseringsuppgifter |
> | Åtgärd | Microsoft. NotificationHubs/namnrymder/NotificationHubs/Read | Hämta lista över resurs beskrivningar för Notification Hub |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Skapa en Notification Hub och uppdatera dess egenskaper. Egenskaperna omfattar huvudsakligen PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/read | Hämta listan över Beskrivning av namn områdes resurs |
> | Åtgärd | Microsoft.NotificationHubs/Namespaces/write | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.NotificationHubs/operationResults/read | Returnerar åtgärds resultat för Notification Hubs Provider |
> | Åtgärd | Microsoft.NotificationHubs/operations/read | Returnerar en lista med åtgärder som stöds för Notification Hubs Provider |
> | Åtgärd | Microsoft.NotificationHubs/register/action | Registrerar prenumerationen för NotificationHubs Resource Provider och gör det möjligt att skapa namn områden och NotificationHubs |
> | Åtgärd | Microsoft.NotificationHubs/unregister/action | Avregistrerar prenumerationen för NotificationHubs Resource Provider och gör det möjligt att skapa namn områden och NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/Clusters/Read | Hämtar egenskaperna för ett Hyper-V-kluster |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/kluster/Skriv | Skapar eller uppdaterar Hyper-V-klustret |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/delete | Tar bort Hyper-V-platsen |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/healthsummary/Read | Hämtar hälso översikten för Hyper-V-resursen |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/read | Hämtar egenskaperna för en Hyper-V-värd |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/hosts/write | Skapar eller uppdaterar Hyper-V-värden |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/Jobs/Read | Hämtar egenskaperna för ett Hyper-V-jobb |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/Machines/Read | Hämtar egenskaperna för en Hyper-V-dator |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/operationsstatus/Read | Hämtar egenskaperna för en Hyper-V-åtgärds status |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/read | Hämtar egenskaperna för en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/refresh/action | Uppdaterar objekten på en Hyper-V-plats |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/runasaccounts/Read | Hämtar egenskaperna för ett Hyper-V-kör som-konto |
> | Åtgärd | Microsoft. OffAzure/HyperVSites/Usage/Read | Hämtar användningen av en Hyper-V-plats |
> | Åtgärd | Microsoft.OffAzure/HyperVSites/write | Skapar eller uppdaterar Hyper-V-platsen |
> | Åtgärd | Microsoft. OffAzure/Operations/Read | Läser de exponerade åtgärderna |
> | Åtgärd | Microsoft. OffAzure/register/åtgärd | Registrerar prenumeration med Microsoft. OffAzure Resource Provider |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Jobs/Read | Hämtar egenskaperna för ett Server jobb |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Machines/Read | Hämtar egenskaperna för en server maskin vara |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Machines/Write | Skriv egenskaperna för en server maskin vara |
> | Åtgärd | Microsoft. OffAzure/ServerSites/operationsstatus/Read | Hämtar egenskaperna för en server åtgärds status |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Read | Hämtar egenskaperna för en server webbplats |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Refresh/åtgärd | Uppdaterar objekten på en server webbplats |
> | Åtgärd | Microsoft. OffAzure/ServerSites/runasaccounts/Read | Hämtar egenskaperna för ett Kör som-konto för Server |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Usage/Read | Hämtar användningen av en server webbplats |
> | Åtgärd | Microsoft. OffAzure/ServerSites/Write | Skapar eller uppdaterar Server platsen |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/delete | Tar bort VMware-webbplatsen |
> | Åtgärd | Microsoft. OffAzure/VMwareSites/healthsummary/Read | Hämtar hälso översikten för VMware-resursen |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/jobs/read | Hämtar egenskaperna för ett VMware-jobb |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/read | Hämtar egenskaperna för en VMware-dator |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/start/action | Starta VMware-datorer |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stoppar VMware-datorerna |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Hämtar egenskaperna för en VMware-åtgärds status |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/read | Hämtar egenskaperna för en VMware-plats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/refresh/action | Uppdaterar objekten i en VMware-webbplats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Hämtar egenskaperna för ett VMware-kör som-konto |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/usage/read | Hämtar användningen av en VMware-plats |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/read | Hämtar egenskaperna för en VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/vcenters/write | Skapar eller uppdaterar VMware vCenter |
> | Åtgärd | Microsoft.OffAzure/VMwareSites/write | Skapar eller uppdaterar VMware-webbplatsen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. OperationalInsights/linkTargets/Read | Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Om du vill länka den här Azure-prenumerationen till en arbets yta använder du ett kund-ID som returneras av den här åtgärden i egenskapen kund-ID för åtgärden skapa arbets yta. |
> | Åtgärd | Microsoft. operationalinsights/Operations/Read | Visar en lista över alla tillgängliga REST API-åtgärder för OperationalInsights. |
> | Åtgärd | Microsoft. operationalinsights/register/åtgärd | Rergisters prenumerationen. |
> | Åtgärd | Microsoft. OperationalInsights/register/åtgärd | Registrera en prenumeration på en resurs leverantör. |
> | Åtgärd | microsoft.operationalinsights/unregister/action | Avregistrerar prenumerationen. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Hämta Sök schema v2. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/action | Sök med ny motor. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Hämta Sök schema v2. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/configurationScopes/Delete | Ta bort konfigurations omfång |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/configurationScopes/Read | Hämta konfigurations omfång |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/configurationScopes/Write | Ange konfigurations omfång |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/data källor/ta bort | Ta bort data källor under en arbets yta. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/data källor/läsa | Hämta data källor under en arbets yta. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/data källor/skrivning | Skapa/uppdatera data källor under en arbets yta. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/ta bort | Tar bort en arbets yta. Om arbets ytan var länkad till en befintlig arbets yta när den skapades, raderas inte arbets ytan som den var länkad till. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/Gateway/Delete | Tar bort en gateway som kon figurer ATS för arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/generateregistrationcertificate/åtgärd | Genererar registrerings certifikat för arbets ytan. Det här certifikatet används för att ansluta Microsoft System Center Operation Manager till arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/intelligencepacks/Disable/åtgärd | Inaktiverar ett informations paket för en specifik arbets yta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Aktiverar ett informations paket för en specifik arbets yta. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Visar en lista över alla informations paket som är synliga för en specifik arbets yta och anger även om paketet är aktiverat eller inaktiverat för den arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/linkedServices/Delete | Ta bort länkade tjänster i den aktuella arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/linkedServices/Read | Hämta länkade tjänster för den aktuella arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/linkedServices/Write | Skapa/uppdatera länkade tjänster i den aktuella arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/Listnycklar/åtgärd | Hämtar List nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/Listnycklar/Read | Hämtar List nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/managementGroups/Read | Hämtar namn och metadata för System Center Operations Manager hanterings grupper som är anslutna till den här arbets ytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Hämta mått definitioner under arbets ytan |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/notificationSettings/Delete | Ta bort användarens meddelande inställningar för arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/notificationSettings/Read | Hämta användarens meddelande inställningar för arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/notificationSettings/Write | Ange användarens meddelande inställningar för arbets ytan. |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/åtgärder/Läs | Hämtar status för en OperationalInsights-arbetsyta åtgärd. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/rensning/åtgärd | Ta bort angivna data från arbets ytan |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AADDomainServicesAccountLogon/läsa | Läsa data från tabellen AADDomainServicesAccountLogon |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AADDomainServicesAccountManagement/läsa | Läsa data från tabellen AADDomainServicesAccountManagement |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Läsa data från tabellen AADDomainServicesDirectoryServiceAccess |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Läsa data från tabellen AADDomainServicesLogonLogoff |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AADDomainServicesPolicyChange/läsa | Läsa data från tabellen AADDomainServicesPolicyChange |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Läsa data från tabellen AADDomainServicesPrivilegeUse |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Läsa data från tabellen AADDomainServicesSystemSecurity |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Läsa data från tabellen ADAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AddonAzureBackupAlerts/läsa | Läsa data från tabellen AddonAzureBackupAlerts |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AddonAzureBackupJobs/läsa | Läsa data från tabellen AddonAzureBackupJobs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AddonAzureBackupPolicy/läsa | Läsa data från tabellen AddonAzureBackupPolicy |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AddonAzureBackupProtectedInstance/läsa | Läsa data från tabellen AddonAzureBackupProtectedInstance |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AddonAzureBackupStorage/läsa | Läsa data från tabellen AddonAzureBackupStorage |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Läsa data från tabellen ADFActivityRun |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Läsa data från tabellen ADFPipelineRun |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Läsa data från tabellen ADFTriggerRun |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Läsa data från tabellen ADReplicationResult |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Läsa data från tabellen ADSecurityAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/avisering/läsa | Läsa data från aviserings tabellen |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AlertHistory/läsa | Läsa data från tabellen AlertHistory |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AmlComputeClusterEvent/läsa | Läsa data från tabellen AmlComputeClusterEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AmlComputeClusterNodeEvent/läsa | Läsa data från tabellen AmlComputeClusterNodeEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AmlComputeJobEvent/läsa | Läsa data från tabellen AmlComputeJobEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ApiManagementGatewayLogs/läsa | Läsa data från tabellen ApiManagementGatewayLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AppCenterError/läsa | Läsa data från tabellen AppCenterError |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ApplicationInsights/läsa | Läsa data från tabellen ApplicationInsights |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AppPlatformLogsforSpring/läsa | Läsa data från tabellen AppPlatformLogsforSpring |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AppServiceEnvironmentPlatformLogs/läsa | Läsa data från tabellen AppServiceEnvironmentPlatformLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AuditLogs/läsa | Läsa data från tabellen AuditLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AutoscaleEvaluationsLog/läsa | Läsa data från tabellen AutoscaleEvaluationsLog |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Läsa data från tabellen AutoscaleScaleActionsLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AWSCloudTrail/läsa | Läsa data från tabellen AWSCloudTrail |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/AzureActivity/läsa | Läsa data från tabellen AzureActivity |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Läsa data från tabellen AzureAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Läsa data från tabellen AzureMetrics |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/BaiClusterEvent/läsa | Läsa data från tabellen BaiClusterEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/BaiClusterNodeEvent/läsa | Läsa data från tabellen BaiClusterNodeEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/BaiJobEvent/läsa | Läsa data från tabellen BaiJobEvent |
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
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHDriverReliability/läsa | Läsa data från tabellen DHDriverReliability |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHLogonFailures/läsa | Läsa data från tabellen DHLogonFailures |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHLogonMetrics/läsa | Läsa data från tabellen DHLogonMetrics |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHOSCrashData/läsa | Läsa data från tabellen DHOSCrashData |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHOSReliability/läsa | Läsa data från tabellen DHOSReliability |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DHWipAppLearning/läsa | Läsa data från tabellen DHWipAppLearning |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DnsEvents/läsa | Läsa data från tabellen DnsEvents |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/DnsInventory/läsa | Läsa data från tabellen DnsInventory |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ETWEvent/läsa | Läsa data från tabellen ETWEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/händelse/läsa | Läsa data från händelse tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Läsa data från tabellen ExchangeAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Läsa data från tabellen ExchangeOnlineAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/FailedIngestion/läsa | Läsa data från tabellen FailedIngestion |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Läsa data från pulsslags tabellen |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/HuntingBookmark/läsa | Läsa data från tabellen HuntingBookmark |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Läsa data från tabellen IISAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Läsa data från tabellen InboundConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Läsa data från tabellen InsightsMetrics |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/IntuneAuditLogs/läsa | Läsa data från tabellen IntuneAuditLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/IntuneOperationalLogs/läsa | Läsa data från tabellen IntuneOperationalLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/KubeEvents/läsa | Läsa data från tabellen KubeEvents |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/KubeHealth/läsa | Läsa data från tabellen KubeHealth |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/KubeNodeInventory/läsa | Läsa data från tabellen KubeNodeInventory |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Läsa data från tabellen KubePodInventory |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/KubeServices/läsa | Läsa data från tabellen KubeServices |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/LinuxAuditLog/läsa | Läsa data från tabellen LinuxAuditLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplication/läsa | Läsa data från tabellen MAApplication |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationHealth/läsa | Läsa data från tabellen MAApplicationHealth |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationHealthAlternativeVersions/läsa | Läsa data från tabellen MAApplicationHealthAlternativeVersions |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationHealthIssues/läsa | Läsa data från tabellen MAApplicationHealthIssues |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationInstance/läsa | Läsa data från tabellen MAApplicationInstance |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationInstanceReadiness/läsa | Läsa data från tabellen MAApplicationInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Läsa data från tabellen MAApplicationReadiness |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADeploymentPlan/läsa | Läsa data från tabellen MADeploymentPlan |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADevice/läsa | Läsa data från tabellen MADevice |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADeviceNotEnrolled/läsa | Läsa data från tabellen MADeviceNotEnrolled |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADeviceNRT/läsa | Läsa data från tabellen MADeviceNRT |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADevicePnPHealth/läsa | Läsa data från tabellen MADevicePnPHealth |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADevicePnPHealthAlternativeVersions/läsa | Läsa data från tabellen MADevicePnPHealthAlternativeVersions |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADevicePnPHealthIssues/läsa | Läsa data från tabellen MADevicePnPHealthIssues |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADeviceReadiness/läsa | Läsa data från tabellen MADeviceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Läsa data från tabellen MADriverInstanceReadiness |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MADriverReadiness/läsa | Läsa data från tabellen MADriverReadiness |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAddin/läsa | Läsa data från tabellen MAOfficeAddin |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Läsa data från tabellen MAOfficeAddinEntityHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Läsa data från tabellen MAOfficeAddinHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Läsa data från tabellen MAOfficeAddinHealthEventNRT |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAddinHealthIssues/läsa | Läsa data från tabellen MAOfficeAddinHealthIssues |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAddinInstance/läsa | Läsa data från tabellen MAOfficeAddinInstance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Läsa data från tabellen MAOfficeAddinInstanceReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Läsa data från tabellen MAOfficeAddinReadiness |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeApp/läsa | Läsa data från tabellen MAOfficeApp |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAppCrashesNRT/läsa | Läsa data från tabellen MAOfficeAppCrashesNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Läsa data från tabellen MAOfficeAppHealth |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAppInstance/läsa | Läsa data från tabellen MAOfficeAppInstance |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Läsa data från tabellen MAOfficeAppInstanceHealth |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Läsa data från tabellen MAOfficeAppReadiness |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Läsa data från tabellen MAOfficeAppSessionsNRT |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Läsa data från tabellen MAOfficeBuildInfo |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Läsa data från tabellen MAOfficeCurrencyAssessment |
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
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftDataShareShareLog/läsa | Läsa data från tabellen MicrosoftDataShareShareLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftHealthcareApisAuditLogs/läsa | Läsa data från tabellen MicrosoftHealthcareApisAuditLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftInsightsAzureActivityLog/läsa | Läsa data från tabellen MicrosoftInsightsAzureActivityLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftWebApplicationLog/läsa | Läsa data från tabellen MicrosoftWebApplicationLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftWebFunctionExecutionLogs/läsa | Läsa data från tabellen MicrosoftWebFunctionExecutionLogs |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Läsa data från tabellen MicrosoftWebStdOutStdErrLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftWebW3CLog/läsa | Läsa data från tabellen MicrosoftWebW3CLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/NetworkMonitoring/läsa | Läsa data från tabellen NetworkMonitoring |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/OfficeActivity/läsa | Läsa data från tabellen OfficeActivity |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/åtgärd/läsa | Läs data från åtgärds tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Läsa data från tabellen OutboundConnection |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Perf/read | Läs data från tabellen perf |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/ProtectionStatus/läsa | Läsa data från tabellen ProtectionStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/läsa | Köra frågor över data i arbets ytan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Läsa data från tabellen ReservedAzureCommonFields |
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
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Läsa data från tabellen SigninLogs |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SPAssessmentRecommendation/läsa | Läsa data från tabellen SPAssessmentRecommendation |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Läsa data från tabellen SQLAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SQLQueryPerformance/läsa | Läsa data från tabellen SQLQueryPerformance |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SqlThreatProtectionLoginAudits/läsa | Läsa data från tabellen SqlThreatProtectionLoginAudits |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SqlVulnerabilityAssessmentResult/läsa | Läsa data från tabellen SqlVulnerabilityAssessmentResult |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/SucceededIngestion/läsa | Läsa data från tabellen SucceededIngestion |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/syslog/läsa | Läsa data från syslog-tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Läsa data från tabellen SysmonEvent |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/tabeller. anpassad/läst | Läser data från valfri anpassad logg |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Läsa data från tabellen ThreatIntelligenceIndicator |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/UAApp/läsa | Läsa data från tabellen UAApp |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/UAComputer/läsa | Läsa data från tabellen UAComputer |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Läsa data från tabellen UAComputerRank |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Läsa data från tabellen UADriver |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Läsa data från tabellen UADriverProblemCodes |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/UAFeedback/läsa | Läsa data från tabellen UAFeedback |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Läsa data från tabellen UAHardwareSecurity |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/UAIESiteDiscovery/läsa | Läsa data från tabellen UAIESiteDiscovery |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Läsa data från tabellen UAOfficeAddIn |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Läsa data från tabellen UAProposedActionPlan |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Läsa data från tabellen UASysReqIssue |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/UAUpgradedComputer/läsa | Läsa data från tabellen UAUpgradedComputer |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Update/read | Läs data från uppdaterings tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Läsa data från tabellen UpdateRunProgress |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Läsa data från tabellen UpdateSummary |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/Usage/read | Läsa data från användnings tabellen |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Läsa data från tabellen VMBoundPort |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Läsa data från tabellen VMConnection |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/W3CIISLog/läsa | Läsa data från tabellen W3CIISLog |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WaaSDeploymentStatus/läsa | Läsa data från tabellen WaaSDeploymentStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WaaSInsiderStatus/läsa | Läsa data från tabellen WaaSInsiderStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WaaSUpdateStatus/läsa | Läsa data från tabellen WaaSUpdateStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WDAVStatus/läsa | Läsa data från tabellen WDAVStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WDAVThreat/läsa | Läsa data från tabellen WDAVThreat |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Läsa data från tabellen WindowsClientAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WindowsEvent/läsa | Läsa data från tabellen WindowsEvent |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Läsa data från tabellen WindowsFirewall |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Läsa data från tabellen WindowsServerAssessmentRecommendation |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WireData/läsa | Läsa data från tabellen WireData |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WorkloadMonitoringPerf/läsa | Läsa data från tabellen WorkloadMonitoringPerf |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WUDOAggregatedStatus/läsa | Läsa data från tabellen WUDOAggregatedStatus |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/fråga/WUDOStatus/läsa | Läsa data från tabellen WUDOStatus |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbets yta |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/regeneratesharedkey/åtgärd | Återskapar den angivna delade nyckeln för arbets ytan |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/regler/läsa | Hämta alla varnings regler. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Tar bort en sparad Sök fråga |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/savedSearches/Read | Hämtar en sparad Sök fråga |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/savedsearches/Results/Read | Hämta sparade Sök resultat. Inaktuell |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/savedsearches/schema/åtgärder/ta bort | Ta bort schemalagda Sök åtgärder. |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/savedsearches/schema/åtgärder/läsa | Hämta schemalagda Sök åtgärder. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Skapa eller uppdatera schemalagda Sök åtgärder. |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/savedsearches/scheman/ta bort | Ta bort schemalagda sökningar. |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/savedsearches/scheman/lästa | Hämta schemalagda sökningar. |
> | Åtgärd | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Skapa eller uppdatera schemalagda sökningar. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/savedSearches/write | Skapar en sparad Sök fråga |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/schema/läsa | Hämtar Sök schema för arbets ytan.  Sökschemat innehåller de visade fälten och deras typer. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/search/action | Kör en Sök fråga |
> | Åtgärd | Microsoft. operationalinsights/arbets ytor/search/Read | Hämta Sök resultat. Föråldrad. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/storageinsightconfigs/Delete | Tar bort en lagrings konfiguration. Detta kommer att stoppa Microsoft Operational Insights från att läsa data från lagrings kontot. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/storageinsightconfigs/Read | Hämtar en lagrings konfiguration. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/storageinsightconfigs/Write | Skapar en ny lagrings konfiguration. Dessa konfigurationer används för att hämta data från en plats i ett befintligt lagrings konto. |
> | Åtgärd | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Hämta logg för översättnings haveri för Sök uppgradering för arbets ytan |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/användning/läsning | Hämtar användnings data för en arbets yta, inklusive mängden data som läses av arbets ytan. |
> | Åtgärd | Microsoft. OperationalInsights/arbets ytor/Skriv | Skapar en ny arbets yta eller länkar till en befintlig arbets yta genom att ange kund-ID: t från den befintliga arbets ytan. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/delete | Ta bort befintlig hanterings Association |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/read | Hämta befintlig hanterings Association |
> | Åtgärd | Microsoft.OperationsManagement/managementAssociations/write | Skapa en ny hanterings Association |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/delete | Ta bort befintlig hanterings konfiguration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/read | Hämta befintlig hanterings konfiguration |
> | Åtgärd | Microsoft.OperationsManagement/managementConfigurations/write | Skapa en ny hanterings konfiguration |
> | Åtgärd | Microsoft.OperationsManagement/register/action | Registrera en prenumeration på en resurs leverantör. |
> | Åtgärd | Microsoft.OperationsManagement/solutions/delete | Ta bort befintlig OMS-lösning |
> | Åtgärd | Microsoft.OperationsManagement/solutions/read | Ta slut på OMS-lösning |
> | Åtgärd | Microsoft. OperationsManagement/lösningar/Skriv | Skapa ny OMS-lösning |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.PolicyInsights/asyncOperationResults/read | Hämtar det asynkrona åtgärds resultatet. |
> | DataAction | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Kontrol lera status för efterlevnad för en specifik komponent mot data policys. |
> | Åtgärd | Microsoft.PolicyInsights/operations/read | Hämtar åtgärder som stöds på Microsoft. PolicyInsights-namnrymd |
> | DataAction | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Logga händelser för resurs komponents princip. |
> | Åtgärd | Microsoft.PolicyInsights/policyEvents/queryResults/action | Fråga efter information om princip händelser. |
> | Åtgärd | Microsoft. PolicyInsights/policyEvents/queryResults/Read | Fråga efter information om princip händelser. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/action | Fråga efter information om princip tillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/queryResults/read | Fråga efter information om princip tillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/action | Fråga sammanfattnings information om principer senaste tillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/summarize/read | Fråga sammanfattnings information om principer senaste tillstånd. |
> | Åtgärd | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Utlöser en ny utvärdering av kompatibilitet för det valda omfånget. |
> | Åtgärd | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Fråga efter information om resurser som krävs av DeployIfNotExists-principer. |
> | Åtgärd | Microsoft.PolicyInsights/register/action | Registrerar resurs leverantören Microsoft policy Insights och aktiverar åtgärder på den. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/cancel/action | Avbryt pågående Microsoft policy-reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/delete | Ta bort princip reparationer. |
> | Åtgärd | Microsoft. PolicyInsights/reparationer/listDeployments/Read | Visar en lista över de distributioner som krävs av en princip reparation. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/read | Hämta princip reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/remediations/write | Skapa eller uppdatera Microsoft policy-reparationer. |
> | Åtgärd | Microsoft.PolicyInsights/unregister/action | Avregistrerar Microsoft policy Insights Resource Provider. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Portal/konsoler/ta bort | Tar bort Cloud Shell-instansen. |
> | Åtgärd | Microsoft. Portal/konsoler/läsa | Läser Cloud Shell-instansen. |
> | Åtgärd | Microsoft. Portal/konsoler/skrivning | Skapa eller uppdatera en Cloud Shell-instans. |
> | Åtgärd | Microsoft.Portal/dashboards/delete | Tar bort instrument panelen från prenumerationen. |
> | Åtgärd | Microsoft. Portal/instrument paneler/läsa | Läser instrument panelerna för prenumerationen. |
> | Åtgärd | Microsoft.Portal/dashboards/write | Lägg till eller ändra instrument panel till en prenumeration. |
> | Åtgärd | Microsoft. Portal/register/åtgärd | Registrera dig på portalen |
> | Åtgärd | Microsoft. Portal/usersettings/Delete | Tar bort Cloud Shell användar inställningar. |
> | Åtgärd | Microsoft.Portal/usersettings/read | Läser Cloud Shell användar inställningar. |
> | Åtgärd | Microsoft.Portal/usersettings/write | Skapa eller uppdatera Cloud Shell användar inställning. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. PowerBIDedicated/kapacitets/Delete | Tar bort Power BI dedikerad kapacitet. |
> | Åtgärd | Microsoft. PowerBIDedicated/kapaciteter/läsning | Hämtar information om den angivna Power BI dedikerade kapaciteten. |
> | Åtgärd | Microsoft. PowerBIDedicated/kapaciteter/återuppta/åtgärd | Återupptar kapaciteten. |
> | Åtgärd | Microsoft.PowerBIDedicated/capacities/skus/read | Hämta tillgänglig SKU-information för kapaciteten |
> | Åtgärd | Microsoft. PowerBIDedicated/kapacitet/PAUSE/åtgärd | Pausar kapaciteten. |
> | Åtgärd | Microsoft. PowerBIDedicated/kapacitet/Skriv | Skapar eller uppdaterar den angivna dedikerade kapaciteten för Power BI. |
> | Åtgärd | Microsoft. PowerBIDedicated/locations/checkNameAvailability/Action | Kontrollerar att angivet Power BI dedikerade kapacitets namn är giltigt och inte används. |
> | Åtgärd | Microsoft. PowerBIDedicated/locations/operationresults/Read | Hämtar information om det angivna åtgärds resultatet. |
> | Åtgärd | Microsoft. PowerBIDedicated/locations/operationstatuses/Read | Hämtar information om den angivna åtgärds statusen. |
> | Åtgärd | Microsoft. PowerBIDedicated/Operations/Read | Hämtar information om åtgärder |
> | Åtgärd | Microsoft. PowerBIDedicated/register/åtgärd | Registrerar Power BI dedikerad resurs leverantör. |
> | Åtgärd | Microsoft.PowerBIDedicated/skus/read | Hämtar information om SKU: er |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Åtgärd | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupPreValidateProtection/action |  |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupProtectedItem/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupStatus/action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | Åtgärd | Microsoft.RecoveryServices/Locations/backupValidateFeatures/action | Validera funktioner |
> | Åtgärd | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Kontrol lera resurs namns tillgänglighet är ett API för att kontrol lera om resurs namnet är tillgängligt |
> | Åtgärd | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärds status för en specifik åtgärd |
> | Åtgärd | Microsoft. RecoveryServices/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | Åtgärd | Microsoft. RecoveryServices/register/åtgärd | Registrerar prenumeration för den aktuella resurs leverantören |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returnerar konfiguration för Recovery Services valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupconfig/write | Uppdaterar konfigurationen för Recovery Services valv. |
> | Åtgärd | Microsoft. RecoveryServices/valv/backupEngines/läsa | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/delete | Ta bort en säkerhets kopie rad skydds avsikt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Få en säkerhets kopie rad skydds avsikt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhets kopia av skydds avsikt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Åtgärd | Microsoft. RecoveryServices/valv/backupFabrics/operationsStatus/Read | Returnerar status för åtgärden |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla skydds bara behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/delete | Tar bort den registrerade behållaren |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör förfrågan om arbets belastningar inom en behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skydds behållare. |
> | Åtgärd | Microsoft. RecoveryServices/valv/backupFabrics/protectionContainers/operationsStatus/Read | Hämtar status för utförd åtgärd på skydds container. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Utför säkerhets kopiering för skyddat objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Tar bort skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar statusen för den åtgärd som utförts på skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objekt information om det skyddade objektet |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Etablera snabb objekts återställning för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställnings punkter för skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återställa återställnings punkter för skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla direkt objekt återställning för skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållar listan |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Avbryt jobbet |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultatet av jobb åtgärden. |
> | Åtgärd | Microsoft. RecoveryServices/valv/backupJobs/operationsStatus/Read | Returnerar status för jobb åtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobb objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobb |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returnerar resultatet av säkerhets kopierings åtgärden för Recovery Services valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | Åtgärd | Microsoft. RecoveryServices/valv/backupPolicies/Delete | Ta bort en skydds princip |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämta resultat från princip åtgärd. |
> | Åtgärd | Microsoft. RecoveryServices/valv/backupPolicies/åtgärder/Läs | Hämta status för princip åtgärd. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skydds principer |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Skapar skydds princip |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Returnerar lista över alla objekt som ska skyddas. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/action | Returnerar information om säkerhets kod för Recovery Services valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returnerar lagrings konfiguration för Recovery Services valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupstorageconfig/write | Uppdaterar lagrings konfiguration för Recovery Services valv. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Verifiera åtgärd på skyddat objekt |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/delete | Åtgärden ta bort valv tar bort den angivna Azure-resursen av typen valv |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | Åtgärd | Microsoft. RecoveryServices/valv/monitoringAlerts/Write | Löser aviseringen. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Hämtar meddelande konfigurationen för Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfigurerar e-postaviseringar till Recovery Services-valvet. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Du kan använda åtgärden avregistrera behållare för att avregistrera en behållare. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | Åtgärd | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Du kan använda åtgärden registrera tjänst behållare för att registrera en behållare med återställnings tjänsten. |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läs eventuella aviserings inställningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Skapa eller uppdatera eventuella aviserings inställningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs eventuella händelser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar konsekvensen för infrastrukturen |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Ta bort eventuella infrastruktur resurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Distribuera process Server avbildning |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrera infrastruktur resurs till AAD |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs infrastruktur resurserna |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läs eventuella infrastruktur resurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Associera gatewayen igen |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Ta bort infrastruktur resurs |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur resurser |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Läs alla logiska nätverk |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationNetworks/replicationNetworkMappings/Delete | Ta bort eventuella nätverks mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Skapa eller uppdatera nätverks mappningar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Identifiera objekt som ska skyddas |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationProtectionContainers/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resurs skydds behållarna |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Ta bort skydds container |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Ta bort eventuella migreringsåtgärder |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrera objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Läs alla återställnings punkter för migrering |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/operationresults/Read | Spåra resultatet av en asynkron åtgärd på objekten för resursallokeringen |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Läs eventuella migreringsåtgärder |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Testa migrering |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Testa migrering av rensning |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Skapa eller uppdatera objekt i migreringen |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läs alla objekt som ska skyddas |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lägg till diskar |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Tillämpa återställnings punkt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Ta bort eventuella skyddade objekt |
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Genomför redundans |
> | Åtgärd | Microsoft. RecoveryServices/valv/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/operationresults/Read | Spåra resultatet av en asynkron åtgärd på resursens skyddade objekt |
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
> | Åtgärd | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
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
> | Åtgärd | Microsoft.RecoveryServices/Vaults/write | Skapa valv-åtgärd skapar en Azure-resurs av typen valv |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. relä/checkNameAvailability/åtgärd | Kontrollerar tillgänglighet för namn området under den aktuella prenumerationen. |
> | Åtgärd | Microsoft. relä/checkNamespaceAvailability/åtgärd | Kontrollerar tillgänglighet för namn området under den aktuella prenumerationen. Detta API är inaktuellt Använd CheckNameAvailability i stället. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/action | Uppdaterar namn områdets auktoriseringsregel. Detta API är föråldrat. Använd ett parkera-anrop för att uppdatera auktoriseringsprincipen för namn områden i stället.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Åtgärd | Microsoft. Relay/Namespaces/authorizationRules/listnycklar/Action | Hämta anslutnings strängen till namn området |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/read | Hämta listan över Beskrivning av auktoriseringsregler för namn områden. |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/Delete | Ta bort namn områdes resurs |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar reglerna för auktoriseringsregler för det primära namn området för katastrof återställning |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för det primära namn området för haveri beredskap |
> | Åtgärd | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Inaktiverar haveri beredskap och stoppar replikering av ändringar från primära till sekundära namn områden. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för namn rymds Ali Aset under den aktuella prenumerationen. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Tar bort den katastrof återställnings konfiguration som är kopplad till namn området. Den här åtgärden kan bara anropas via det primära namn området. |
> | Åtgärd | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/redundans/åtgärd | Anropar en GEO DR-redundans och konfigurerar om namn rymds Ali Aset så att det pekar på det sekundära namn området. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hämtar den katastrof återställnings konfiguration som är kopplad till namn området. |
> | Åtgärd | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den katastrof återställnings konfiguration som är kopplad till namn området. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Åtgärd för att uppdatera resursprovider. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Åtgärd för att ta bort resursprovider-auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Hämta anslutnings strängen till resursprovider |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Hämta listan över resursprovider-auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Skapa auktoriseringsregler för resursprovider och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/Delete | Åtgärd för att ta bort resursprovider-resurs |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/read | Hämta lista över beskrivningar av resursprovider-resurser |
> | Åtgärd | Microsoft.Relay/namespaces/HybridConnections/write | Skapa eller uppdatera resursprovider-egenskaper. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/read | Hämtar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.Relay/namespaces/messagingPlan/write | Uppdaterar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft. Relay/namnrymder/operationresults/Read | Hämta status för namn områdes åtgärd |
> | Åtgärd | Microsoft. Relay/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Åtgärd | Microsoft. Relay/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Write | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Åtgärd | Microsoft. Relay/Namespaces/providers/Microsoft. Insights/logDefinitions/Read | Hämta lista över namn rymds loggar resurs beskrivningar |
> | Åtgärd | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Åtgärd | Microsoft. Relay/namnrymder/läsa | Hämta listan över Beskrivning av namn områdes resurs |
> | Åtgärd | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnrymd |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Åtgärd för att uppdatera WcfRelay. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Åtgärd för att ta bort WcfRelay-auktoriseringsregler |
> | Åtgärd | Microsoft. Relay/Namespaces/WcfRelays/authorizationRules/listnycklar/Action | Hämta anslutnings strängen till WcfRelay |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Hämta listan över WcfRelay-auktoriseringsregler |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Skapa auktoriseringsregler för WcfRelay och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft. Relay/namnrymder/WcfRelays/Delete | Åtgärd för att ta bort WcfRelay-resurs |
> | Åtgärd | Microsoft. Relay/namnrymder/WcfRelays/Read | Hämta lista över beskrivningar av WcfRelay-resurser |
> | Åtgärd | Microsoft. Relay/namnrymder/WcfRelays/Write | Skapa eller uppdatera WcfRelay-egenskaper. |
> | Åtgärd | Microsoft. Relay/namnrymd/Skriv | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Åtgärd | Microsoft.Relay/operations/read | Hämta åtgärder |
> | Åtgärd | Microsoft. Relay/register/åtgärd | Registrerar prenumerationen för relä resurs leverantören och gör det möjligt att skapa relä resurser |
> | Åtgärd | Microsoft.Relay/unregister/action | Registrerar prenumerationen för relä resurs leverantören och gör det möjligt att skapa relä resurser |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Hämtar tillgänglighets status för den angivna resursen |
> | Åtgärd | Microsoft.ResourceHealth/AvailabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Åtgärd | Microsoft.ResourceHealth/events/read | Hämta Service Health händelser för den aktuella prenumerationen |
> | Åtgärd | Microsoft. Resourcehealth/healthevent/Action | Anger ändring av hälso tillståndet för den angivna resursen |
> | Åtgärd | Microsoft. Resourcehealth/healthevent/activated/åtgärd | Anger ändring av hälso tillståndet för den angivna resursen |
> | Åtgärd | Microsoft. Resourcehealth/healthevent/pågående/åtgärd | Anger ändring av hälso tillståndet för den angivna resursen |
> | Åtgärd | Microsoft. Resourcehealth/healthevent/väntar/åtgärd | Anger ändring av hälso tillståndet för den angivna resursen |
> | Åtgärd | Microsoft. Resourcehealth/healthevent/åtgärdad/åtgärd | Anger ändring av hälso tillståndet för den angivna resursen |
> | Åtgärd | Microsoft.Resourcehealth/healthevent/Updated/action | Anger ändring av hälso tillståndet för den angivna resursen |
> | Åtgärd | Microsoft.ResourceHealth/impactedResources/read | Hämta påverkade resurser för den aktuella prenumerationen |
> | Åtgärd | Microsoft.ResourceHealth/metadata/read | Hämtar metadata |
> | Åtgärd | Microsoft.ResourceHealth/Operations/read | Hämta de åtgärder som är tillgängliga för Microsoft-ResourceHealth |
> | Åtgärd | Microsoft.ResourceHealth/register/action | Registrerar prenumerationen för Microsoft-ResourceHealth |
> | Åtgärd | Microsoft.ResourceHealth/unregister/action | Avregistrerar prenumerationen för Microsoft-ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Resources/calculateTemplateHash/Action | Beräkna hashen för den angivna mallen. |
> | Åtgärd | Microsoft. Resources/checkPolicyCompliance/Action | Kontrol lera status för efterlevnad för en specifik resurs mot resurs principer. |
> | Åtgärd | Microsoft. Resources/checkResourceName/Action | Kontrol lera resurs namnet för giltigheten. |
> | Åtgärd | Microsoft. Resources/distributioner/Avbryt/åtgärd | Avbryter en distribution. |
> | Åtgärd | Microsoft. Resources/Deployments/Delete | Tar bort en distribution. |
> | Åtgärd | Microsoft. Resources/distributions/exportTemplate/åtgärd | Exportera mall för en distribution |
> | Åtgärd | Microsoft.Resources/deployments/operations/read | Hämtar eller visar distributions åtgärder. |
> | Åtgärd | Microsoft. Resources/Deployments/operationstatuses/Read | Hämtar eller visar distributions åtgärds status. |
> | Åtgärd | Microsoft. Resources/Deployments/Read | Hämtar eller visar distributioner. |
> | Åtgärd | Microsoft. Resources/Deployments/validate/Action | Verifierar en distribution. |
> | Åtgärd | Microsoft. Resources/distributions/whatIf/åtgärd | Förutsäger distribution av ändringar i mallen. |
> | Åtgärd | Microsoft. Resources/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | Åtgärd | Microsoft. Resources/Links/Delete | Tar bort en resurs länk. |
> | Åtgärd | Microsoft. Resources/Links/Read | Hämtar eller listar resurs länkar. |
> | Åtgärd | Microsoft. Resources/Links/Write | Skapar eller uppdaterar en resurs länk. |
> | Åtgärd | Microsoft. Resources/Marketplace/Purchase/Action | Köp en resurs från Marketplace. |
> | Åtgärd | Microsoft. Resources/providers/Read | Hämta listan över leverantörer. |
> | Åtgärd | Microsoft. Resources/Resources/Read | Hämta listan över resurser baserat på filter. |
> | Åtgärd | Microsoft. Resources/Subscriptions/locations/Read | Hämtar listan över platser som stöds. |
> | Åtgärd | Microsoft.Resources/subscriptions/operationresults/read | Hämta prenumerations åtgärds resultatet. |
> | Åtgärd | Microsoft. Resources/Subscriptions/providers/Read | Hämtar eller listar resurs leverantörer. |
> | Åtgärd | Microsoft. Resources/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | Åtgärd | Microsoft. Resources/Subscriptions/resourceGroups/Delete | Tar bort en resurs grupp och alla dess resurser. |
> | Åtgärd | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Operations/Read | Hämtar eller visar distributions åtgärder. |
> | Åtgärd | Microsoft. Resources/Subscriptions/ResourceGroups/distributioner/operationstatuses/Read | Hämtar eller visar distributions åtgärds status. |
> | Åtgärd | Microsoft. Resources/Subscriptions/ResourceGroups/distribution/Read | Hämtar eller visar distributioner. |
> | Åtgärd | Microsoft. Resources/Subscriptions/ResourceGroups/distributioner/Write | Skapar eller uppdaterar en distribution. |
> | Åtgärd | Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action | Flyttar resurser från en resurs grupp till en annan. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Åtgärd | Microsoft. Resources/Subscriptions/ResourceGroups/Resources/Read | Hämtar resurser för resurs gruppen. |
> | Åtgärd | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Verifiera flytt av resurser från en resurs grupp till en annan. |
> | Åtgärd | Microsoft. Resources/Subscriptions/resourceGroups/Write | Skapar eller uppdaterar en resurs grupp. |
> | Åtgärd | Microsoft. Resources/Subscriptions/resurser/Read | Hämtar resurser för en prenumeration. |
> | Åtgärd | Microsoft. Resources/Subscriptions/tagNames/Delete | Tar bort en prenumerations kod. |
> | Åtgärd | Microsoft. Resources/Subscriptions/tagNames/Read | Hämtar eller listar prenumerations koder. |
> | Åtgärd | Microsoft. Resources/Subscriptions/tagNames/tagValues/Delete | Tar bort ett värde för prenumerations kod. |
> | Åtgärd | Microsoft. Resources/Subscriptions/tagNames/tagValues/Read | Hämtar eller visar värden för prenumerations koder. |
> | Åtgärd | Microsoft. Resources/Subscriptions/tagNames/tagValues/Write | Lägger till ett värde för prenumerations tag gen. |
> | Åtgärd | Microsoft. Resources/Subscriptions/tagNames/Write | Lägger till en prenumerations kod. |
> | Åtgärd | Microsoft. Resources/Tags/Delete | Tar bort alla Taggar på en resurs. |
> | Åtgärd | Microsoft. Resources/Tags/Read | Hämtar alla Taggar på en resurs. |
> | Åtgärd | Microsoft. Resources/Tags/Write | Uppdaterar taggarna på en resurs genom att ersätta eller sammanfoga befintliga taggar med en ny uppsättning taggar eller ta bort befintliga taggar. |
> | Åtgärd | Microsoft. Resources/Tenants/Read | Hämtar listan över klienter. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Delete | Tar bort jobb samling. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Disable/åtgärd | Inaktiverar jobb samling. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Enable/åtgärd | Aktiverar jobb samling. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Delete | Tar bort jobb. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/generateLogicAppDefinition/åtgärd | Genererar logisk app-definition baserat på ett Scheduler-jobb. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/jobhistories/Read | Hämtar jobb historik. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Read | Hämtar jobb. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Run/Action | Kör jobb. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Write | Skapar eller uppdaterar jobb. |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Read | Hämta jobb samling |
> | Åtgärd | Microsoft. Scheduler/förfrågningsåtgärder/Write | Skapar eller uppdaterar jobb samling. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. search/checkNameAvailability/åtgärd | Kontrollerar tillgänglighet för tjänst namnet. |
> | Åtgärd | Microsoft. search/Operations/Read | Visar en lista över alla tillgängliga åtgärder för Microsoft. search-providern. |
> | Åtgärd | Microsoft. search/register/åtgärd | Registrerar prenumerationen för Sök resurs leverantören och gör det möjligt att skapa Sök tjänster. |
> | Åtgärd | Microsoft. search/searchServices/createQueryKey/Action | Skapar Frågeparametern. |
> | Åtgärd | Microsoft.Search/searchServices/delete | Tar bort Sök tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/deleteQueryKey/delete | Tar bort frågans nyckel. |
> | Åtgärd | Microsoft.Search/searchServices/listAdminKeys/action | Läser administrations nycklarna. |
> | Åtgärd | Microsoft. search/searchServices/listQueryKeys/Action | Returnerar listan över frågans API-nycklar för den aktuella Azure Kognitiv sökning-tjänsten. |
> | Åtgärd | Microsoft.Search/searchServices/listQueryKeys/read | Returnerar listan över frågans API-nycklar för den aktuella Azure Kognitiv sökning-tjänsten. |
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

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.ServiceBus/checkNameAvailability/action | Kontrollerar tillgänglighet för namn området under den aktuella prenumerationen. |
> | Åtgärd | Microsoft.ServiceBus/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namn området under den aktuella prenumerationen. Detta API är inaktuellt Använd CheckNameAvailability i stället. |
> | Åtgärd | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Tar bort VNet-reglerna i Service Bus Resource Provider för angivet VNet |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/action | Uppdaterar namn områdets auktoriseringsregel. Detta API är föråldrat. Använd ett parkera-anrop för att uppdatera auktoriseringsprincipen för namn områden i stället.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Hämta anslutnings strängen till namn området |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/read | Hämta listan över Beskrivning av auktoriseringsregler för namn områden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/Delete | Ta bort namn områdes resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar reglerna för auktoriseringsregler för det primära namn området för katastrof återställning |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för det primära namn området för haveri beredskap |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveri beredskap och stoppar replikering av ändringar från primära till sekundära namn områden. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för namn rymds Ali Aset under den aktuella prenumerationen. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Tar bort den katastrof återställnings konfiguration som är kopplad till namn området. Den här åtgärden kan bara anropas via det primära namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Anropar en GEO DR-redundans och konfigurerar om namn rymds Ali Aset så att det pekar på det sekundära namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hämtar den katastrof återställnings konfiguration som är kopplad till namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den katastrof återställnings konfiguration som är kopplad till namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Tar bort det Event Grid filter som är kopplat till namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Hämtar det Event Grid filter som är kopplat till namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Skapar eller uppdaterar det Event Grid filter som är kopplat till namn området. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/eventhubs/read | Hämta lista över beskrivningar av EventHub-resurser |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Ta bort resurs för IP-filter |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Hämta IP-filter resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Skapa resurs för IP-filter |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/ta emot/åtgärd | Ta emot meddelanden |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/skicka/åtgärd | Skicka meddelanden |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hämtar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/messagingPlan/write | Uppdaterar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrate/action | Migrera namn områdes åtgärd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Tar bort migrerings konfigurationen. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Hämtar migrerings konfigurationen som anger status för migreringen och väntande replikerings åtgärder |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Återställer migreringen av standard till Premium-namnrymd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Tilldelar den DNS som är kopplad till standard namn området till Premium-namnområdet som Slutför migreringen och stoppar synkroniseringen av resurser från standard till Premium-namnrymd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Skapar eller uppdaterar migrerings konfigurationen. Detta kommer att börja synkronisera resurser från standard till Premium-namnområdet |
> | Åtgärd | Microsoft. Service Bus/Namespaces/networkruleset/Delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft. Service Bus/namnrymder/networkruleset/Read | Hämtar NetworkRuleSet-resurs |
> | Åtgärd | Microsoft. Service Bus/namnrymder/networkruleset/Write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Ta bort VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/read | Hämtar NetworkRuleSet-resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/networkrulesets/write | Skapa VNET-regel resurs |
> | Åtgärd | Microsoft.ServiceBus/namespaces/operationresults/read | Hämta status för namn områdes åtgärd |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta lista över namn rymds loggar resurs beskrivningar |
> | Åtgärd | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Åtgärd för att uppdatera kön. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Åtgärd för borttagning av auktoriseringsregler för kön |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Hämta anslutnings strängen till kön |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Hämta listan över auktoriseringsregler för kön |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Skapa auktoriseringsregler för kön och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Åtgärd | Microsoft.ServiceBus/namespaces/queues/Delete | Åtgärd för att ta bort en Queue-resurs |
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
> | Åtgärd | Microsoft.SignalRService/SignalR/read | Visa signalers inställningar och konfigurationer i hanterings portalen eller via API |
> | Åtgärd | Microsoft. SignalRService/SignalR/regeneratekey/åtgärd | Ändra värdet för SignalR åtkomst nycklar i hanterings portalen eller via API |
> | Åtgärd | Microsoft. SignalRService/SignalR/avomstart/åtgärd | Starta om en Azure SignalR-tjänst i hanterings portalen eller via API. Det kommer att finnas vissa stillestånds tider. |
> | Åtgärd | Microsoft. SignalRService/Signalerare/Write | Ändra signal hanterarens inställningar och konfigurationer i hanterings portalen eller via API |
> | Åtgärd | Microsoft. SignalRService/avregistrera/åtgärd | Avregistrerar resurs leverantören "Microsoft. SignalRService" med en prenumeration |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Visar en lista över program artefakter för program definition. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/delete | Tar bort en program definition. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/read | Hämtar en lista över program definitioner. |
> | Åtgärd | Microsoft.Solutions/applicationDefinitions/write | Lägg till eller ändra en program definition. |
> | Åtgärd | Microsoft. Solutions/Applications/applicationArtifacts/Read | Visar en lista över program artefakter. |
> | Åtgärd | Microsoft. Solutions/Applications/Delete | Tar bort ett program. |
> | Åtgärd | Microsoft. Solutions/program/läsa | Hämtar en lista över program. |
> | Åtgärd | Microsoft. Solutions/Applications/refreshPermissions/åtgärd | Uppdaterar program behörighet (er). |
> | Åtgärd | Microsoft.Solutions/applications/updateAccess/action | Uppdaterar program åtkomst. |
> | Åtgärd | Microsoft.Solutions/applications/write | Skapar ett program. |
> | Åtgärd | Microsoft. Solutions/jitRequests/Delete | Ta bort en JitRequest |
> | Åtgärd | Microsoft.Solutions/jitRequests/read | Hämtar en lista över JitRequests |
> | Åtgärd | Microsoft.Solutions/jitRequests/write | Skapar en JitRequest |
> | Åtgärd | Microsoft. Solutions/locations/operationStatuses/Read | Läser åtgärds statusen för resursen. |
> | Åtgärd | Microsoft. Solutions/register/åtgärd | Registrera dig för lösningar. |
> | Åtgärd | Microsoft.Solutions/unregister/action | Avregistrerar från lösningar. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. SQL/checkNameAvailability/Action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Åtgärd | Microsoft.Sql/instancePools/delete | Tar bort en instans-pool |
> | Åtgärd | Microsoft.Sql/instancePools/read | Hämtar en instans-pool |
> | Åtgärd | Microsoft.Sql/instancePools/usages/read | Hämtar användnings information för en instans pool |
> | Åtgärd | Microsoft.Sql/instancePools/write | Skapar eller uppdaterar en instans-pool |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Hämta resultatet av åtgärden uppsättnings princip för utökad server-BLOB-granskning |
> | Åtgärd | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Hämta resultatet av åtgärden SET för Server BLOB Auditing-princip |
> | Åtgärd | Microsoft. SQL/locations/capabilites/Read | Hämtar funktionerna för den här prenumerationen på en specifik plats |
> | Åtgärd | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Hämtar status för en databas åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/databaseOperationResults/read | Hämtar status för en databas åtgärd. |
> | Åtgärd | Microsoft. SQL/locations/deletedServerAsyncOperation/Read | Hämtar pågående åtgärder på borttagen Server |
> | Åtgärd | Microsoft.Sql/locations/deletedServerOperationResults/read | Hämtar pågående åtgärder på borttagen Server |
> | Åtgärd | Microsoft. SQL/locations/deletedServers/Read | Returnera listan över borttagna servrar eller hämtar egenskaperna för den angivna borttagna servern. |
> | Åtgärd | Microsoft. SQL/locations/deletedServers/Recover/Action | Återställa en borttagen Server |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Hämtar Azure-asynkron åtgärd för asynkron åtgärd i elastisk pool |
> | Åtgärd | Microsoft.Sql/locations/elasticPoolOperationResults/read | Hämtar resultatet av en åtgärd för elastisk pool. |
> | Åtgärd | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering krypterings skydd |
> | Åtgärd | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering krypterings skydd |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Hämta resultatet av åtgärden uppsättnings princip för utökad server-BLOB-granskning |
> | Åtgärd | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Hämta resultatet av åtgärden uppsättnings princip för utökad server-BLOB-granskning |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Hämtar status för en brand Väggs regel åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/firewallRulesOperationResults/read | Hämtar status för en brand Väggs regel åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/delete | Tar bort en befintlig grupp för redundans. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Kör Planerad redundans i en befintlig instans för redundans. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kör framtvingad redundansväxling i en befintlig grupp för redundans. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/read | Returnerar listan över instans-redundansväxlingen eller hämtar egenskaperna för den angivna instansen av redundans. |
> | Åtgärd | Microsoft.Sql/locations/instanceFailoverGroups/write | Skapar en instans av redundans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna instansen av redundans. |
> | Åtgärd | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Hämtar status för en instans av en instans |
> | Åtgärd | Microsoft.Sql/locations/instancePoolOperationResults/read | Hämtar resultatet för en instans av en instans av poolen |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Returnerar information om en angiven gränssnitts slut punkt för Azure asynkron åtgärd |
> | Åtgärd | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Returnerar information om den angivna åtgärden för gränssnitts slut punkts profil |
> | Åtgärd | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Hämtar status för en jobb agent åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/jobAgentOperationResults/read | Hämtar resultatet av en jobb agent åtgärd. |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionBackups/read | Visar en lista över långsiktig kvarhållning av säkerhets kopior för varje databas på varje server på en plats |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Visar en lista över långsiktig kvarhållning av säkerhets kopior för varje databas på en server |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Tar bort en säkerhets kopia av långsiktig kvarhållning |
> | Åtgärd | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Visar en lista över långsiktig kvarhållning av säkerhets kopior för en databas |
> | Åtgärd | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Slutför återställnings åtgärden för hanterade databaser |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering hanterad instans krypterings skydd |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering hanterad instans krypterings skydd |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering hanterade instans nycklar |
> | Åtgärd | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering hanterade instans nycklar |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering i hanterad databas |
> | Åtgärd | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering i hanterad databas |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Åtgärd | Microsoft. SQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Åtgärd | Microsoft. SQL/locations/Read | Hämtar tillgängliga platser för en specifik prenumeration |
> | Åtgärd | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Åtgärd | Microsoft.Sql/locations/serverKeyOperationResults/read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
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
> | Åtgärd | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en hanterad databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/kolumner/läsa | Returnera en lista med kolumner för en hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Uppdatera känslighets etiketter för batch |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/Delete | Tar bort en befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instans databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/read | Hämtar befintlig hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/recommendedSensitivityLabels/Write | Rekommenderade känslighets etiketter för batch-uppdatering |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/restoreDetails/Read | Returnerar information om återställningen av hanterade databaser medan återställningen pågår. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/read | Hämta ett hanterat databas schema. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Hämta en hanterad databas-kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort känslighets etiketten för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighets rekommendationer för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighets rekommendationer för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighets etiketten för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighets etiketten för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Hämta en hanterad databas tabell |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/Read | Hämta en lista över principer för hot identifiering för hanterade databaser som kon figurer ATS för en angiven server |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/Write | Ändra principen för databas hot identifiering för en specifik hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/securityEvents/read | Hämtar säkerhets händelser för hanterade databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Hämta information om databasen transparent datakryptering på en specifik hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Ändra databas transparent datakryptering för en specifik hanterad databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Hämta principerna för sårbarhets bedömning på en givendatabase |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Ta bort regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Hämta regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Rules/planal/Write | Ändra regelns bas linje för sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Konvertera ett befintligt skannings resultat till ett läsligt format. Om det redan finns händer ingenting |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Kör databas genomsökning för sårbarhets bedömning. |
> | Åtgärd | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Returnera en lista över genomsöknings poster för databas sårbarhets bedömning eller hämta skannings posten för angivet skannings-ID. |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft. SQL/managedInstances/databaser/skriva | Skapar en ny databas eller uppdaterar en befintlig databas. |
> | Åtgärd | Microsoft. SQL/managedInstances/Delete | Tar bort en befintlig hanterad instans. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/read | Returnerar en lista över Server krypterings skydd eller hämtar egenskaperna för det angivna Server krypterings skyddet. |
> | Åtgärd | Microsoft. SQL/managedInstances/encryptionProtector/revalidate/åtgärd | Uppdatera egenskaperna för det angivna Server krypterings skyddet. |
> | Åtgärd | Microsoft.Sql/managedInstances/encryptionProtector/write | Uppdatera egenskaperna för det angivna Server krypterings skyddet. |
> | Åtgärd | Microsoft. SQL/managedInstances/Keys/Delete | Tar bort en befintlig Azure SQL-hanterad instans nyckel. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/read | Returnera listan över hanterade instans nycklar eller hämtar egenskaperna för den angivna hanterade instans nyckeln. |
> | Åtgärd | Microsoft.Sql/managedInstances/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hanterade instans nyckeln. |
> | Åtgärd | Microsoft.Sql/managedInstances/metricDefinitions/read | Hämta mått definitioner för hanterade instanser |
> | Åtgärd | Microsoft. SQL/managedInstances/Metrics/Read | Hämta mått för hanterade instanser |
> | Åtgärd | Microsoft. SQL/managedInstances/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. SQL/managedInstances/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instanser |
> | Åtgärd | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för hanterade instanser |
> | Åtgärd | Microsoft.Sql/managedInstances/read | Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hanterade instansen. |
> | Åtgärd | Microsoft.Sql/managedInstances/recoverableDatabases/read | Returnerar en lista med återställnings bara hanterade databaser |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en borttagen hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en borttagen hanterad databas |
> | Åtgärd | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Returnerar en lista med återställas nedbrutna hanterade databaser. |
> | Åtgärd | Microsoft. SQL/managedInstances/securityAlertPolicies/Read | Hämta en lista över principer för identifiering av hanterade servrar som kon figurer ATS för en angiven server |
> | Åtgärd | Microsoft. SQL/managedInstances/securityAlertPolicies/Write | Ändra principen för hot identifiering för hanterade servrar för en specifik hanterad server |
> | Åtgärd | Microsoft.Sql/managedInstances/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Åtgärd | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik hanterad instans |
> | Åtgärd | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Read | Hämta principerna för sårbarhets bedömning på en specifik hanterad instans |
> | Åtgärd | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik hanterad instans |
> | Åtgärd | Microsoft. SQL/managedInstances/Write | Skapar en hanterad instans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hanterade instansen. |
> | Åtgärd | Microsoft. SQL/Operations/Read | Hämtar tillgängliga REST-åtgärder |
> | Åtgärd | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Anger om användaren får godkänna en privat slut punkts anslutning |
> | Åtgärd | Microsoft. SQL/register/åtgärd | Registrerar prenumerationen för Microsoft SQL Database Resource Provider och gör det möjligt att skapa Microsoft SQL-databaser. |
> | Åtgärd | Microsoft. SQL/Servers/administrators/Delete | Tar bort ett enskilt Azure Active Directory administratörs objekt |
> | Åtgärd | Microsoft. SQL/Servers/administrators/Read | Hämtar ett enskilt Azure Active Directory administratörs objekt |
> | Åtgärd | Microsoft. SQL/Servers/administrators/Write | Lägger till eller uppdaterar ett enskilt Azure Active Directory administratörs objekt |
> | Åtgärd | Microsoft.Sql/servers/advisors/read | Returnerar lista över rådgivare som är tillgängliga för servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angiven rådgivare för servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/recommendedActions/write | Använd den rekommenderade åtgärden på servern |
> | Åtgärd | Microsoft.Sql/servers/advisors/write | Uppdaterar automatiskt körnings status för en rådgivare på server nivå. |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/read | Hämta information om standard gransknings principen för Server tabeller som kon figurer ATS på en specifik server |
> | Åtgärd | Microsoft.Sql/servers/auditingPolicies/write | Ändra standard granskning av Server tabell för en specifik server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/operationResults/read | Hämta resultatet av åtgärden SET för Server BLOB Auditing-princip |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/read | Hämta information om den gransknings princip för Server-blob som kon figurer ATS på en specifik server |
> | Åtgärd | Microsoft.Sql/servers/auditingSettings/write | Ändra serverns BLOB-granskning för en specifik server |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/read | Returnerar inställningar för automatisk justering för servern |
> | Åtgärd | Microsoft.Sql/servers/automaticTuning/write | Uppdaterar automatiska justerings inställningar för servern och returnerar uppdaterade inställningar |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Tar bort ett befintligt valv för säkerhets kopiering. |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Den här åtgärden används för att hämta en säkerhets kopia av ett långsiktigt bevarande valv. Den returnerar information om valvet som har registrerats på den här servern |
> | Åtgärd | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Den här åtgärden används för att registrera ett säkerhets kopierings valv med långsiktigt bevarande till en server |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/delete | Tar bort en befintlig server kommunikations länk. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/read | Returnera listan över kommunikations Länkar för en angiven server. |
> | Åtgärd | Microsoft.Sql/servers/communicationLinks/write | Skapa eller uppdatera en server kommunikations länk. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/read | Returnera listan över Server anslutnings principer för en angiven server. |
> | Åtgärd | Microsoft.Sql/servers/connectionPolicies/write | Skapa eller uppdatera en princip för Server anslutning. |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/read | Returnerar lista över rådgivare som är tillgängliga för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angiven rådgivare för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/advisors/write | Uppdatera status för automatisk körning av en rådgivare på databas nivå. |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/read | Hämta information om tabell gransknings principen som kon figurer ATS för en angiven databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingPolicies/write | Ändra tabell gransknings principen för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/read | Hämta information om den avgransknings princip för BLOB som kon figurer ATS för en angiven databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditingSettings/write | Ändra BLOB Auditing-principen för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/auditRecords/read | Hämta databasens BLOB audit-poster |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/read | Returnerar inställningar för automatisk justering för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/automaticTuning/write | Uppdaterar automatiska justerings inställningar för en databas och returnerar uppdaterade inställningar |
> | Åtgärd | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Hämtar status för en databas åtgärd. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Returnera listan över principer för arkivering av säkerhets kopiering för en angiven databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Skapa eller uppdatera en princip för säkerhets kopiering av databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/kolumner/läsa | Returnera en lista med kolumner för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/read | Hämta information om anslutnings principen som kon figurer ATS för en angiven databas |
> | Åtgärd | Microsoft.Sql/servers/databases/connectionPolicies/write | Ändra anslutnings princip för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Uppdatera känslighets etiketter för batch |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Returnera listan över data masknings principer för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Ta bort data mask princip regel för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Hämta information om den princip regel för data maskering som kon figurer ATS för en angiven databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Ändra princip regel för data maskning för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Ändra data masking-princip för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Returnerar steg informationen för den distribuerade frågan för data lager frågan för valt steg-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Returnerar information om distributions frågan för data lagret för det valda fråge-ID: t |
> | Åtgärd | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Hämtar användar aktiviteter för en SQL Data Warehouse-instans som omfattar körnings-och inaktiverade frågor |
> | Åtgärd | Microsoft.Sql/servers/databases/delete | Tar bort en befintlig databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/export/action | Exportera Azure SQL Database |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Hämta information om den utökade blobb gransknings principen som kon figurer ATS för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Ändra den utökade blobb gransknings principen för en viss databas |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/read | Hämtar en samling tillägg för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/extensions/write | Ändra tillägget för en specifik databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/redundans/åtgärd | Kundens initierade databas växling vid fel. |
> | Åtgärd | Microsoft. SQL/Servers/databaser/geoBackupPolicies/Read | Hämta principer för geo-säkerhetskopiering för en specifik databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/geoBackupPolicies/Write | Skapa eller uppdatera en databas princip för säkerhets kopiering |
> | Åtgärd | Microsoft.Sql/servers/databases/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Hämtar en lista över tillgängliga underhålls fönster för en vald databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/read | Hämtar inställningar för Windows-underhåll för en vald databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/maintenanceWindows/write | Anger inställningar för Windows-underhåll för en vald databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/metrics/read | Returnera mått för databaser |
> | Åtgärd | Microsoft. SQL/Servers/databaser/flytta/åtgärd | Ändra namnet på en befintlig databas. |
> | Åtgärd | Microsoft.Sql/servers/databases/operationResults/read | Hämtar status för en databas åtgärd. |
> | Åtgärd | Microsoft. SQL/Servers/databaser/åtgärder/Avbryt/åtgärd | Avbryter Azure SQL Database väntande asynkron åtgärd som inte har slutförts än. |
> | Åtgärd | Microsoft.Sql/servers/databases/operations/read | Returnera listan över åtgärder som utförts på databasen |
> | Åtgärd | Microsoft. SQL/Servers/databaser/paus/åtgärd | Pausa Azure SQL Data Warehouse-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft. SQL/Servers/databaser/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Returnerar samlingen av fråge texter som motsvarar de angivna parametrarna. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/read | Returnerar aktuella värden för inställningarna för Frågearkivet för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/queryStore/write | Uppdaterar inställning för Frågearkivet för databasen |
> | Åtgärd | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämta egenskaperna för den angivna databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/recommendedSensitivityLabels/Write | Rekommenderade känslighets etiketter för batch-uppdatering |
> | Åtgärd | Microsoft. SQL/Servers/-databaser/replicationLinks/Delete | Avsluta detvingad replikering av replikeringsrelationen och med potentiell data förlust |
> | Åtgärd | Microsoft. SQL/Servers/databaser/replicationLinks/redundans/åtgärd | Redundans efter synkronisering av alla ändringar från den primära, som gör databasen till den primära relationship\u0027s-servern och göra den till en sekundär |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Redundansväxling omedelbart med potentiell data förlust, vilket gör databasen till den primära replikerings-relationship\u0027s och gör den till en sekundär |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/read | Returnera listan över länkar för replikering eller hämta egenskaperna för de angivna replikeringslänken. |
> | Åtgärd | Microsoft. SQL/Servers/databaser/replicationLinks/ta bort länk/åtgärd | Avsluta replikeringen framtvingar eller efter synkronisering med partnern |
> | Åtgärd | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Uppdatera replikeringsläget för länk till synkront eller asynkront läge |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/action | Skapar en ny återställnings punkt |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/delete | Tar bort en återställnings punkt för databasen. |
> | Åtgärd | Microsoft.Sql/servers/databases/restorePoints/read | Returnerar återställnings punkter för databasen. |
> | Åtgärd | Microsoft. SQL/Servers/databaser/återuppta/åtgärd | Återuppta Azure SQL Data Warehouse-databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/read | Hämta ett databas schema. |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta en databas kolumn. |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort känslighets etiketten för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighets rekommendationer för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighets rekommendationer för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighets etiketten för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighets etiketten för en specifik kolumn |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/read | Hämta en databas tabell. |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Hämta lista över index rekommendationer för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Använd index rekommendation |
> | Åtgärd | Microsoft. SQL/Servers/databaser/securityAlertPolicies/Read | Hämta en lista över de principer för databas hot identifiering som kon figurer ATS för en angiven server |
> | Åtgärd | Microsoft. SQL/Servers/databaser/securityAlertPolicies/Write | Ändra principen för databas hot identifiering för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/securityMetrics/read | Hämtar en samling databas säkerhets mått |
> | Åtgärd | Microsoft.Sql/servers/databases/sensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Returnera förslag om att skala databasen upp eller ned baserat på statistik för körning av fråga för att förbättra prestandan eller minska kostnaderna |
> | Åtgärd | Microsoft.Sql/servers/databases/skus/read | Hämtar en samling SKU: er som är tillgängliga för en databas |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Avbryt synkronisering av synkronisera grupp |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/delete | Tar bort en befintlig Sync-grupp. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Returnera listan över databas scheman för Sync Hub |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/logs/read | Returnera listan över synkroniserade grupp loggar |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/read | Returnera listan över Sync-grupper eller hämta egenskaperna för den angivna Sync-gruppen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Uppdatera databas schema för Sync-hubb |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Hämta resultatet av schema uppdaterings åtgärden för Sync-hubben |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Tar bort en befintlig synkroniserad medlem. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Returnera listan över synkroniserade medlemmar eller hämta egenskaperna för den angivna Sync-medlemmen. |
> | Åtgärd | Microsoft. SQL/Servers/databases/syncGroups/syncMembers/refreshSchema/Action | Uppdatera synkronisera medlems schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Hämta resultatet av uppdaterings åtgärden för synkronisering av medlems schema |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Returnera listan över databas scheman för synkronisering av medlemmar |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Skapar en synkroniserad medlem med de angivna parametrarna eller uppdaterar egenskaperna för den angivna Sync-medlemmen. |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Utlös synkronisering av synkroniserad grupp |
> | Åtgärd | Microsoft.Sql/servers/databases/syncGroups/write | Skapar en Sync-grupp med de angivna parametrarna eller uppdaterar egenskaperna för den angivna Sync-gruppen. |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/queryText/action | Returnerar Transact-SQL-texten för valt fråge-ID |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/read | Returnerar sammanställd körnings statistik för den valda frågan under den valda tids perioden |
> | Åtgärd | Microsoft.Sql/servers/databases/topQueries/statistics/read | Returnerar sammanställd körnings statistik för den valda frågan under den valda tids perioden |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Hämtar pågående åtgärder vid transparent data kryptering |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Hämta status och information om funktionen transparent data krypterings säkerhet för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Ändra status för transparent data kryptering |
> | Åtgärd | Microsoft. SQL/Servers/databaser/upgradeDataWarehouse/åtgärd | Uppgradera Azure SQL Data Warehouse-databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/användning/läsa | Hämtar information om Azure SQL Database användning |
> | Åtgärd | Microsoft. SQL/Servers/-databaser/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/Read | Hämta principerna för sårbarhets bedömning på en givendatabase |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/regler/bas linjer/ta bort | Ta bort regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/regler/bas linjer/läsning | Hämta regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/regler/bas linjer/skrivning | Ändra regelns bas linje för sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Konvertera ett befintligt skannings resultat till ett läsligt format. Om det redan finns händer ingenting |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Kör databas genomsökning för sårbarhets bedömning. |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/skanningar/läsa | Returnera en lista över genomsöknings poster för databas sårbarhets bedömning eller hämta skannings posten för angivet skannings-ID. |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Kör databas genomsökning för sårbarhets bedömning. |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Hämta resultatet av databasens sårbarhets körnings åtgärd för sårbarhets bedömning |
> | Åtgärd | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Hämta information om den sårbarhets bedömning som kon figurer ATS för en angiven databas |
> | Åtgärd | Microsoft. SQL/Servers/databaser/vulnerabilityAssessmentSettings/Write | Ändra sårbarhets bedömning för en specifik databas |
> | Åtgärd | Microsoft.Sql/servers/databases/write | Skapar en databas med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna databasen. |
> | Åtgärd | Microsoft. SQL/Servers/Delete | Tar bort en befintlig server. |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Tar bort en befintlig katastrof återställnings konfiguration för en specifik server |
> | Åtgärd | Microsoft. SQL/Servers/disasterRecoveryConfiguration/redundans/åtgärd | Redundansväxla en DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Framtvinga redundans a DisasterRecoveryConfiguration |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Hämtar en samling haveri beredskaps konfigurationer som inkluderar den här servern |
> | Åtgärd | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Ändra server haveri återställnings konfiguration |
> | Åtgärd | Microsoft.Sql/servers/elasticPoolEstimates/read | Returnerar listan med uppskattningar för elastisk pool som redan har skapats för den här servern |
> | Åtgärd | Microsoft. SQL/Servers/elasticPoolEstimates/Write | Skapar ny uppskattning av elastisk pool för lista över databaser som tillhandahålls |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/read | Returnerar lista över rådgivare som är tillgängliga för den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder med angiven rådgivare för den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/advisors/write | Uppdatera status för automatisk körning av en rådgivare på elastisk Poolnivå. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/databases/read | Hämtar en lista över databaser för en elastisk pool |
> | Åtgärd | Microsoft. SQL/Servers/elasticPools/Delete | Ta bort befintlig elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Hämta aktiviteter och information om en specifik Elastic Database-pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Hämta aktiviteter och information om en specifik databas som är en del av Elastic Database-pool |
> | Åtgärd | Microsoft. SQL/Servers/elasticPools/redundans/åtgärd | Kunden initierade elastisk pool-redundans. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Returnera typer av mått som är tillgängliga för Elastic Database-pooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/metrics/read | Returnera mått för Elastic Database-pooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Avbryter en asynkron åtgärd i Azure SQL elastisk pool som inte har slutförts än. |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/operations/read | Returnera listan över åtgärder som utförts i den elastiska poolen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för Elastic Database-pooler |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/read | Hämta information om elastisk pool på en specifik server |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/skus/read | Hämtar en samling SKU: er som är tillgängliga för en elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/elasticPools/write | Skapa en ny eller ändra egenskaper för en befintlig elastisk pool |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/read | Returnerar en lista över Server krypterings skydd eller hämtar egenskaperna för det angivna Server krypterings skyddet. |
> | Åtgärd | Microsoft. SQL/Servers/encryptionProtector/revalidate/åtgärd | Uppdatera egenskaperna för det angivna Server krypterings skyddet. |
> | Åtgärd | Microsoft.Sql/servers/encryptionProtector/write | Uppdatera egenskaperna för det angivna Server krypterings skyddet. |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/read | Hämta information om den utökade gransknings principen för Server-blob som kon figurer ATS på en viss server |
> | Åtgärd | Microsoft.Sql/servers/extendedAuditingSettings/write | Ändra den utökade serverns BLOB-granskning för en viss server |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/delete | Tar bort en befintlig redundans grupp. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/failover/action | Kör Planerad redundans i en befintlig grupp för redundans. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kör framtvingad redundans i en befintlig grupp för växling vid fel. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/read | Returnerar listan över Redundansrelationer eller hämtar egenskaperna för den angivna gruppen för redundans. |
> | Åtgärd | Microsoft.Sql/servers/failoverGroups/write | Skapar en failover-grupp med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna gruppen för redundans. |
> | Åtgärd | Microsoft. SQL/Servers/firewallRules/Delete | Tar bort en befintlig server brand Väggs regel. |
> | Åtgärd | Microsoft.Sql/servers/firewallRules/read | Returnera listan över Server brand Väggs regler eller hämtar egenskaperna för den angivna Server brand Väggs regeln. |
> | Åtgärd | Microsoft. SQL/Servers/firewallRules/Write | Skapar en server brand Väggs regel med de angivna parametrarna, uppdaterar egenskaperna för den angivna regeln eller skriver över alla befintliga regler med nya server brand Väggs regler. |
> | Åtgärd | Microsoft. SQL/Servers/import/åtgärd | Skapa en ny databas på servern och distribuera schema och data från ett DacPac-paket |
> | Åtgärd | Microsoft.Sql/servers/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Tar bort angiven gränssnitts slut punkts profil |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Returnerar egenskaperna för den angivna gränssnittets slut punkts profil |
> | Åtgärd | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Skapar en gränssnitts slut punkts profil med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna gränssnitts slut punkten |
> | Åtgärd | Microsoft. SQL/Servers/jobAgents/Delete | Tar bort en Azure SQL DB-jobbiljett |
> | Åtgärd | Microsoft.Sql/servers/jobAgents/read | Hämtar en Azure SQL DB-jobbiljett |
> | Åtgärd | Microsoft. SQL/Servers/jobAgents/Write | Skapar eller uppdaterar en Azure SQL DB-Job agent |
> | Åtgärd | Microsoft.Sql/servers/keys/delete | Tar bort en befintlig server nyckel. |
> | Åtgärd | Microsoft.Sql/servers/keys/read | Returnera listan över Server nycklar eller hämta egenskaperna för den angivna Server nyckeln. |
> | Åtgärd | Microsoft.Sql/servers/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna Server nyckeln. |
> | Åtgärd | Microsoft.Sql/servers/operationResults/read | Hämtar pågående Server åtgärder |
> | Åtgärd | Microsoft. SQL/Servers/privateEndpointConnectionProxies/Delete | Tar bort en befintlig anslutning till en privat slut punkt |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Returnerar listan över anslutnings-proxy för privata slut punkter eller hämtar egenskaperna för den angivna anslutningen till den privata slut punkten. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Verifierar en privat slut punkts anslutning skapa samtal från NRP sida |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Skapar en anslutning till en privat slutpunkt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna proxyn för privat slut punkt anslutning. |
> | Åtgärd | Microsoft. SQL/Servers/privateEndpointConnections/Delete | Tar bort en befintlig privat slut punkts anslutning |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/read | Returnerar listan över anslutningar för privata slut punkter eller hämtar egenskaperna för den angivna privata slut punkts anslutningen. |
> | Åtgärd | Microsoft.Sql/servers/privateEndpointConnections/write | Godkänner eller avvisar en befintlig privat slut punkts anslutning |
> | Åtgärd | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/åtgärd | Anger om användaren får godkänna en privat slut punkts anslutning |
> | Åtgärd | Microsoft.Sql/servers/privateLinkResources/read | Hämta de privata länk resurserna för motsvarande SQL Server |
> | Åtgärd | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för servrar |
> | Åtgärd | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Hämta mått för rekommenderade Elastic Database-pooler för en specifik server |
> | Åtgärd | Microsoft.Sql/servers/recommendedElasticPools/read | Hämta rekommendationer för Elastic Database-pooler för att minska kostnaderna eller förbättra prestandan utifrån historisk resursutnyttjande |
> | Åtgärd | Microsoft.Sql/servers/recoverableDatabases/read | Den här åtgärden används för haveri beredskap för Live Database för att återställa databasen till den senast kända fungerande säkerhets kopierings punkten. Den returnerar information om den senaste korrekta säkerhets kopieringen, men det doesn\u0027t faktiskt återställer databasen. |
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

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Storage/checknameavailability/Read | Kontrollerar att konto namnet är giltigt och inte används. |
> | Åtgärd | Microsoft. Storage/locations/checknameavailability/Read | Kontrollerar att konto namnet är giltigt och inte används. |
> | Åtgärd | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft. Storage att det virtuella nätverket eller under nätet tas bort |
> | Åtgärd | Microsoft. Storage/locations/usages/Read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |
> | Åtgärd | Microsoft. Storage/Operations/Read | Avsöker statusen för en asynkron åtgärd. |
> | Åtgärd | Microsoft. Storage/register/åtgärd | Registrerar prenumerationen för lagrings resurs leverantören och gör det möjligt att skapa lagrings konton. |
> | Åtgärd | Microsoft.Storage/skus/read | Visar en lista över SKU: er som stöds av Microsoft. Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Returnerar resultatet av att lägga till BLOB-innehåll |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en BLOB |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/deleteAutomaticSnapshot/åtgärd | Returnerar resultatet av att ta bort en automatisk ögonblicks bild |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Returnerar listan över blobbar under ett konto med matchande Tags filter |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en BLOB eller en lista över blobbar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action | Returnerar resultatet av BLOB-kommandot |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Returnerar resultatet av att läsa BLOB-Taggar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Returnerar resultatet av att skriva BLOB-Taggar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en BLOB |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Rensa juridiskt undantag i BLOB-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Ta bort oföränderlighets-princip för BLOB container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Utöka oföränderlighets-princip för BLOB container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Lås oföränderlighets-princip för BLOB container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Hämta oföränderlighets-princip för BLOB container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Lägg till oföränderlighets-princip för BLOB container |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Returnerar resultatet av en BLOB-behållare för leasing |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar lista över behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ange juridiskt undantag för BLOB-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av korrigerings filens BLOB-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att skicka BLOB-behållare |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användar Delegerings nyckel för BLOB service |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/read | Returnerar egenskaper för BLOB service eller statistik |
> | Åtgärd | Microsoft.Storage/storageAccounts/blobServices/write | Returnerar resultatet av att ställa in BLOB service-egenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/delete | Tar bort ett befintligt lagrings konto. |
> | Åtgärd | Microsoft. Storage/storageAccounts/encryptionScopes/Read |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/encryptionScopes/Write |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/redundans/åtgärd | Kunden kan styra redundansväxlingen i händelse av tillgänglighets problem |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/actassuperuser/action | Hämta fil administratörs behörighet |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Returnerar resultatet av att ta bort en fil/mapp |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Returnerar resultatet av att ändra behörighet för en fil/mapp |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista över filer/mappar |
> | DataAction | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/read |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/fileServices/read | Hämta egenskaper för fil tjänst |
> | Åtgärd | Microsoft. Storage/storageAccounts/fileServices/Shares/Delete |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/fileServices/Shares/Read |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/fileServices/Shares/Read |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/fileServices/Shares/Write |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/fileServices/Write |  |
> | Åtgärd | Microsoft.Storage/storageAccounts/listAccountSas/action | Returnerar kontots SAS-token för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/listServiceSas/action | Returnerar SAS-token för tjänsten för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/delete | Ta bort principer för lagrings konto hantering |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/read | Hämta konto principer för lagrings hantering |
> | Åtgärd | Microsoft.Storage/storageAccounts/managementPolicies/write | Lägg till principer för lagrings konto hantering |
> | Åtgärd | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Delete |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Read |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/objectReplicationPolicies/Write |  |
> | Åtgärd | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Delete | Ta bort proxy för privat slut punkt anslutning |
> | Åtgärd | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Read | Hämta anslutnings proxy för privat slutpunkt |
> | Åtgärd | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Lägg till proxyservrar för privat slut punkt anslutning |
> | Åtgärd | Microsoft. Storage/storageAccounts/privateEndpointConnections/Delete | Ta bort privat slut punkts anslutning |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Hämta privat slut punkts anslutning |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Lägg till privat slut punkts anslutning |
> | Åtgärd | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/åtgärd | Godkänn anslutningar för privata slut punkter |
> | Åtgärd | Microsoft.Storage/storageAccounts/privateLinkResources/read | Hämta StorageAccount-groupids |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returnerar resultatet av att ta bort en kö |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Returnerar resultatet av att lägga till ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Returnerar resultatet av att ta bort ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Returnerar resultatet av att bearbeta ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returnerar ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Returnerar resultatet av att skriva ett meddelande |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller en lista över köer. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returnerar resultatet av att skriva en kö |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Hämta Kötjänst egenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/read | Returnerar egenskaper för Queue Service eller statistik. |
> | Åtgärd | Microsoft.Storage/storageAccounts/queueServices/write | Returnerar resultatet av att ställa in egenskaper för Queue Service |
> | Åtgärd | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomst nycklarna för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Återställ BLOB-intervall till tillstånd för den angivna tiden |
> | Åtgärd | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Återkallar alla användar Delegerings nycklar för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Skapa/uppdatera diagnostikinställningar för lagrings kontot. |
> | Åtgärd | Microsoft.Storage/storageAccounts/tableServices/read | Hämta Table service egenskaper |
> | Åtgärd | Microsoft.Storage/storageAccounts/write | Skapar ett lagrings konto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna eller lägger till en anpassad domän för det angivna lagrings kontot. |
> | Åtgärd | Microsoft.Storage/usages/read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. storagesync/locations/checkNameAvailability/Action | Kontrollerar att namnet på lagrings synkroniseringstjänsten är giltigt och inte används. |
> | Åtgärd | Microsoft. storagesync/locations/arbetsflöden/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Åtgärd | Microsoft. storagesync/Operations/Read | Hämtar en lista över de åtgärder som stöds |
> | Åtgärd | Microsoft. storagesync/register/åtgärd | Registrerar prenumerationen för Storage Sync-providern |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/Delete | Ta bort alla tjänster för synkronisering av lagring |
> | Åtgärd | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Storage Sync-tjänster |
> | Åtgärd | microsoft.storagesync/storageSyncServices/read | Läs alla tjänster för synkronisering av lagring |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/registeredServers/Delete | Ta bort alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för registrerad Server |
> | Åtgärd | microsoft.storagesync/storageSyncServices/registeredServers/read | Läs alla registrerade servrar |
> | Åtgärd | Microsoft. storagesync/storageSyncServices/registeredServers/Write | Skapa eller uppdatera alla registrerade servrar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Ta bort alla moln slut punkter |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hämtar status för en asynkron säkerhets kopiering/återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Anropa den här åtgärden efter säkerhets kopiering |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Anropa den här åtgärden efter återställning |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Anropa den här åtgärden innan du säkerhetskopierar |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Anropa den här åtgärden innan du återställer |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Läs eventuella slut punkter i molnet |
> | Åtgärd | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Återställ pulsslag |
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
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/read | Visar eller hämtar Access Control poster |
> | Åtgärd | Microsoft.StorSimple/managers/accessControlRecords/write | Skapa eller uppdatera Access Control poster |
> | Åtgärd | Microsoft.StorSimple/managers/alerts/read | Visar eller hämtar aviseringarna |
> | Åtgärd | Microsoft. StorSimple/chefer/säkerhets kopieringar/läsa | Listar eller hämtar säkerhets kopian |
> | Åtgärd | Microsoft. StorSimple/chefer/bandwidthSettings/Delete | Tar bort en befintlig bandbredds inställning (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/bandwidthSettings/Read | Visa en lista över bandbredds inställningar (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/bandwidthSettings/Write | Skapar en ny eller uppdaterar bandbredds inställningar (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/certificates/write | Skapa eller uppdatera certifikaten |
> | Åtgärd | Microsoft.StorSimple/Managers/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Åtgärd | Microsoft.StorSimple/managers/clearAlerts/action | Rensa alla aviseringar som är associerade med enhets hanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Visa en lista över konfigurationer som stöds av moln installationen |
> | Åtgärd | Microsoft. StorSimple/chefer/configureDevice/åtgärd | Konfigurerar en enhet |
> | Åtgärd | Microsoft. StorSimple/chefer/Delete | Tar bort enhets hanterare |
> | Åtgärd | Microsoft. StorSimple/chefer/Delete | Åtgärden ta bort valv tar bort den angivna Azure-resursen av typen valv |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/read | Visar eller hämtar aviserings inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/alertSettings/write | Skapa eller uppdatera aviserings inställningarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/authorizeForServiceEncryptionKeyRollover/Action | Auktorisera för förnyelse av tjänst krypterings nyckel för enheter |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/säkerhets kopiering/åtgärd | Gör en manuell säkerhets kopiering för att skapa en säkerhets kopiering på begäran av alla volymer som skyddas av principen. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/Delete | Tar bort ett befintligt säkerhets kopierings principer (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/operationResults/Read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/Read | Lista över säkerhets kopierings principer (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Tar bort ett befintligt schema |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/schema/Read | Lista scheman |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/schema/skrivning | Skapar ett nytt schema eller uppdateringar |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupPolicies/Write | Skapar en ny eller uppdaterar säkerhets kopierings principer (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/Delete | Tar bort säkerhets kopian |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/Elements/Clone/åtgärd | Klona en resurs eller volym med hjälp av ett säkerhets kopierings element. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/operationResults/Read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backups/Read | Listar eller hämtar säkerhets kopian |
> | Åtgärd | Microsoft. StorSimple/hanterare/enheter/säkerhets kopiering/återställning/åtgärd | Återställ alla volymer från en säkerhets kopia. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Delete | Tar bort schema grupper för säkerhets kopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Read | Listar eller hämtar schema grupper för säkerhets kopiering |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Write | Skapa eller uppdatera schema grupper för säkerhets kopiering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/delete | Tar bort CHAP-inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/read | Visar eller hämtar CHAP-inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/chapSettings/write | Skapa eller uppdatera CHAP-inställningarna |
> | Åtgärd | Microsoft. StorSimple/hanterare/enheter/inaktivera/åtgärd | Inaktiverar en enhet. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Delete | Tar bort enheterna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/disks/Read | Listar eller hämtar diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/hämtning/åtgärd | Download uppdateringar för en enhet. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/redundans/åtgärd | Enhetens redundans. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/redundans/operationResults/Read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/failoverTargets/Read | Visar eller hämtar enheternas Redundansrelationer |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/säkerhets kopiering/åtgärd | Gör en säkerhets kopia av en fil server. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Delete | Tar bort fil servrarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Metrics/Read | Visar eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Read | Listar eller hämtar fil servrar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Tar bort resurserna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Visar eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listar eller hämtar resurserna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Shares/Write | Skapa eller uppdatera resurserna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/fileservers/Write | Skapa eller uppdatera fil servrarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Ändra styrenhetens energi tillstånd för maskin varu komponent grupper |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Visa en lista över maskin varu komponent grupper |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/install/åtgärd | Installera uppdateringar på en enhet. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/installUpdates/Action | Installerar uppdateringar på enheterna (endast 8000-serien). |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/säkerhets kopiering/åtgärd | Gör en säkerhets kopia av en iSCSI-server. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Delete | Tar bort iSCSI-servrarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Delete | Tar bort diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Metrics/Read | Visar eller hämtar mått |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/metricsDefinitions/Read | Visar eller hämtar måtten definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Read | Listar eller hämtar diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Write | Skapa eller uppdatera diskarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Metrics/Read | Visar eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Read | Listar eller hämtar iSCSI-servrar |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/iscsiservers/Write | Skapa eller uppdatera iSCSI-servrarna |
> | Åtgärd | Microsoft. StorSimple/hanterare/enheter/jobb/Avbryt/åtgärd | Avbryta ett pågående jobb |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Jobs/operationResults/Read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Jobs/Read | Visar eller hämtar jobben |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/listFailoverSets/Action | Visa en lista över redundansväxlingen för en befintlig enhet (endast 8000-serien). |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/listFailoverTargets/Action | Visa en lista över redundansväxlingen för enheterna (endast 8000-serien). |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Metrics/Read | Visar eller hämtar mått |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/metricsDefinitions/Read | Visar eller hämtar måtten definitioner |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/confirmMigration/åtgärd | Bekräftar en lyckad migrering och genomför den. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/confirmMigrationStatus/Read | Ange status för bekräfta migrering |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/åtgärd | Hämta bekräftelse status för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Hämtar status för jobbet för migrerings uppskattning. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/fetchMigrationStatus/åtgärd | Hämta status för migreringen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importera käll konfiguration för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Lista uppskattning av migrering |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/migrationStatus/Read | Lista status för migrering |
> | Åtgärd | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/startMigration/åtgärd | Starta migrering med käll konfiguration |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/startMigrationEstimate/åtgärd | Starta ett jobb för att uppskatta varaktigheten för migreringsprocessen. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/read | Visar eller hämtar nätverks inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/networkSettings/write | Skapar en ny eller uppdaterar nätverks inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/publicEncryptionKey/Action | Visa en lista över den offentliga krypterings nyckeln för enhets hanteraren |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/publishSupportPackage/Action | Publicera support paketet för en befintlig enhet. Ett StorSimple-stödpaket är en lättanvänd mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med fel sökning av problem med StorSimple-enheter. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Read | Visar eller hämtar enheterna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/scanForUpdates/Action | Söker efter uppdateringar på en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/securitySettings/Read | Lista säkerhets inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synkronisera fjärrhanterings certifikatet för en enhet. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Uppdatera säkerhets inställningarna. |
> | Åtgärd | Microsoft.StorSimple/managers/devices/securitySettings/write | Skapar en ny eller uppdaterar säkerhets inställningar |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/sendTestAlertEmail/Action | Skicka test aviserings meddelande till konfigurerade e-postmottagare. |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Shares/Read | Listar eller hämtar resurserna |
> | Åtgärd | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/timeSettings/Read | Visar eller hämtar tids inställningarna |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/timeSettings/Write | Skapar ett nytt eller uppdaterings tids inställningar |
> | Åtgärd | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/updateSummary/Read | Visar eller hämtar uppdaterings sammanfattningen |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Delete | Tar bort en befintlig volym behållare (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Metrics/Read | Lista måtten |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/metricsDefinitions/Read | Visa en lista över mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Read | Lista volym containrar (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Delete | Tar bort en befintlig volym |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Metrics/Read | Lista måtten |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Visa en lista över mått definitioner |
> | Åtgärd | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Read | Lista volymer |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Write | Skapar en ny eller uppdateringar av volymer |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/volumeContainers/Write | Skapar en ny eller uppdaterar volym behållare (endast 8000-serien) |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Volumes/Read | Lista volymer |
> | Åtgärd | Microsoft. StorSimple/chefer/Devices/Write | Skapa eller uppdatera enheterna |
> | Åtgärd | Microsoft.StorSimple/managers/encryptionSettings/read | Visar eller hämtar krypterings inställningarna |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/delete | Tar bort information om det utökade valvet |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/delete | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/read | Visar eller hämtar information om utökade valv |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Åtgärd | Microsoft.StorSimple/managers/extendedInformation/write | Skapa eller uppdatera information om det utökade valvet |
> | Åtgärd | Microsoft.StorSimple/Managers/extendedInformation/write | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Åtgärd | Microsoft. StorSimple/chefer/features/Read | Visa en lista över funktionerna |
> | Åtgärd | Microsoft.StorSimple/managers/fileservers/read | Listar eller hämtar fil servrar |
> | Åtgärd | Microsoft.StorSimple/managers/getEncryptionKey/action | Hämta krypterings nyckel för enhets hanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/iscsiservers/read | Listar eller hämtar iSCSI-servrar |
> | Åtgärd | Microsoft. StorSimple/chefer/Jobs/Read | Visar eller hämtar jobben |
> | Åtgärd | Microsoft.StorSimple/managers/listActivationKey/action | Hämtar aktiverings nyckeln för StorSimple-Enhetshanteraren. |
> | Åtgärd | Microsoft. StorSimple/chefer/listPublicEncryptionKey/åtgärd | Visa en lista över offentliga krypterings nycklar för en StorSimple Enhetshanteraren. |
> | Åtgärd | Microsoft. StorSimple/chefer/Metrics/Read | Visar eller hämtar mått |
> | Åtgärd | Microsoft.StorSimple/managers/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Åtgärd | Microsoft. StorSimple/chefer/migrateClassicToResourceManager/åtgärd | Migrera klassiskt till Resource Manager för hanterare |
> | Åtgärd | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Visa en lista över konfigurationer för migrering av källa (endast 8000-serien) |
> | Åtgärd | Microsoft.StorSimple/managers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft. StorSimple/chefer/provisionCloudAppliance/åtgärd | Skapa en ny moln installation. |
> | Åtgärd | Microsoft.StorSimple/managers/read | Visar eller hämtar enhets ansvariga |
> | Åtgärd | Microsoft.StorSimple/Managers/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Åtgärd | Microsoft.StorSimple/managers/regenerateActivationKey/action | Återskapa aktiverings nyckeln för en befintlig StorSimple-Enhetshanteraren. |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Tar bort autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/read | Visar eller hämtar autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft.StorSimple/managers/storageAccountCredentials/write | Skapa eller uppdatera autentiseringsuppgifterna för lagrings kontot |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/delete | Tar bort lagrings domänerna |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/read | Listar eller hämtar lagrings domänerna |
> | Åtgärd | Microsoft.StorSimple/managers/storageDomains/write | Skapa eller uppdatera lagrings domänerna |
> | Åtgärd | Microsoft. StorSimple/chefer/Write | Skapa eller uppdatera enhets chefer |
> | Åtgärd | Microsoft.StorSimple/Managers/write | Skapa valv-åtgärd skapar en Azure-resurs av typen valv |
> | Åtgärd | Microsoft.StorSimple/operations/read | Visar eller hämtar åtgärder |
> | Åtgärd | Microsoft.StorSimple/register/action | Registrera providern Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. StreamAnalytics/locations/kvoter/read | Läs Stream Analytics prenumerations kvot |
> | Åtgärd | Microsoft. StreamAnalytics/Operations/Read | Läs Stream Analytics åtgärder |
> | Åtgärd | Microsoft. StreamAnalytics/register/åtgärd | Registrera prenumeration med Stream Analytics Resource Provider |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Delete | Ta bort Stream Analytics jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Functions/Delete | Ta bort Stream Analytics jobb funktion |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobb funktion |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Läs Stream Analytics jobb funktion |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Hämta standard definition för en Stream Analytics Job-funktion |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Functions/test/Action | Test Stream Analytics jobb funktion |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Skriv Stream Analytics jobb funktion |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Inputs/Delete | Ta bort Stream Analytics jobb inmatade |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Inputs/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobb-ingångar |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/indata/Read | Läs Stream Analytics jobb-ingångar |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/indata/sampel/åtgärd | Exempel på inmatade Stream Analytics jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Testa Stream Analytics jobb-ingångar |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Inputs/Write | Skriv Stream Analytics jobb inmatat |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Läs mått definitioner |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/outputs/Delete | Ta bort Stream Analytics jobbets utdata |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobbets utdata |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/utdata/Read | Läs Stream Analytics jobbets utdata |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/outputs/test/åtgärd | Test Stream Analytics jobbets utdata |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/utdata/Write | Skriv Stream Analytics jobbets utdata |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/providers/Microsoft. Insights/diagnosticSettings/Read | Läsa diagnostisk inställning. |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/providers/Microsoft. Insights/diagnosticSettings/Write | Skriv diagnos inställning. |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för streamingjobs |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för streamingjobs |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Read | Läs Stream Analyticss jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/start/Action | Starta Stream Analytics jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/stopp/åtgärd | Stoppa Stream Analytics jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Transforms/Delete | Ta bort transformering av Stream Analytics jobb |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/transformationer/Read | Läs Stream Analytics jobb omvandling |
> | Åtgärd | Microsoft. StreamAnalytics/streamingjobs/Transforms/Write | Skriv Stream Analytics jobb omvandling |
> | Åtgärd | Microsoft.StreamAnalytics/streamingjobs/Write | Skriv Stream Analytics jobb |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft. Subscription/Avbryt/åtgärd | Avbryter prenumerationen |
> | Åtgärd | Microsoft. prenumeration/CreateSubscription/åtgärd | Skapa en Azure-prenumeration |
> | Åtgärd | Microsoft. Subscription/registrera/åtgärd | Registrerar prenumeration med Microsoft. Subscription-resurs leverantör |
> | Åtgärd | Microsoft. Subscription/Byt namn/åtgärd | Byter namn på prenumerationen |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/read | Hämta en Azure-prenumerations definition i en hanterings grupp. |
> | Åtgärd | Microsoft.Subscription/SubscriptionDefinitions/write | Skapa en Azure-prenumerations definition |

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
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Tar bort åtkomst principen. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hämta egenskaperna för en åtkomst princip. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Skapar en ny åtkomst princip för en miljö eller uppdaterar en befintlig åtkomst princip. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/delete | Tar bort miljön. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Tar bort händelse källan. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/eventsources/read | Hämta egenskaperna för en händelse källa. |
> | Åtgärd | Microsoft. TimeSeriesInsights/miljöer/eventsources/Write | Skapar en ny händelse källa för en miljö eller uppdaterar en befintlig händelse källa. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/read | Hämta egenskaperna för en miljö. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Tar bort referens data uppsättningen. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Hämta egenskaperna för en referens data uppsättning. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Skapar en ny referens data uppsättning för en miljö eller uppdaterar en befintlig referens data uppsättning. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/status/read | Hämta status för miljön och tillståndet för dess associerade åtgärder, t. ex. inkommande. |
> | Åtgärd | Microsoft.TimeSeriesInsights/environments/write | Skapar en ny miljö eller uppdaterar en befintlig miljö. |
> | Åtgärd | Microsoft.TimeSeriesInsights/register/action | Registrerar prenumerationen för Time Series Insights Resource Provider och gör det möjligt att skapa Time Series Insights miljöer. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | Microsoft.VisualStudio/Account/Delete | Ta bort konto |
> | Åtgärd | Microsoft. VisualStudio/konto/tillägg/läsa | Läs konto/tillägg |
> | Åtgärd | Microsoft. VisualStudio/konto/projekt/läsa | Läs konto/projekt |
> | Åtgärd | Microsoft.VisualStudio/Account/Project/Write | Ange konto/projekt |
> | Åtgärd | Microsoft.VisualStudio/Account/Read | Läs konto |
> | Åtgärd | Microsoft.VisualStudio/Account/Write | Ange konto |
> | Åtgärd | Microsoft.VisualStudio/Extension/Delete | Ta bort tillägg |
> | Åtgärd | Microsoft. VisualStudio/tillägg/läsa | Läs tillägg |
> | Åtgärd | Microsoft.VisualStudio/Extension/Write | Ange tillägg |
> | Åtgärd | Microsoft.VisualStudio/Project/Delete | Ta bort projekt |
> | Åtgärd | Microsoft.VisualStudio/Project/Read | Läs projekt |
> | Åtgärd | Microsoft.VisualStudio/Project/Write | Ange projekt |
> | Åtgärd | Microsoft.VisualStudio/Register/Action | Registrera Azure-prenumeration med Microsoft. VisualStudio-providern |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Åtgärd | microsoft.web/apimanagementaccounts/apiacls/read | Hämta API Management-konton Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Ta bort API Management-konton API: er Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/read | Hämta API Management-konton API: er Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/apiacls/write | Uppdatera API Management-konton API: er Apiacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Hämta API Management-konton API: er Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Bekräfta API-anslutningar för medgivande kods API Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Ta bort API Management-konton API-anslutningar Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Hämta API Management-konton API-anslutningar Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Uppdatera API Management-konton API-anslutningar Connectionacls. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/delete | Ta bort API Management-konton API-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Hämta medgivande Länkar för API Management-API-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Visa lista över anslutnings nycklar API Management-konton API-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Lista hemligheter API Management konton API-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/read | Hämta API Management-konton API-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/connections/write | Uppdatera API Management-konton API-anslutningar. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/delete | Ta bort API: er för API Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Ta bort API Management-konton lokaliserade definitioner. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Hämta API Management-konton lokaliserade definitioner. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Uppdatera API Management-konton lokaliserade definitioner. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/read | Hämta API: er för API Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/apis/write | Uppdatera API: er för API Management-konton. |
> | Åtgärd | microsoft.web/apimanagementaccounts/connectionacls/read | Hämta API Management-konton Connectionacls. |
> | Åtgärd | Microsoft. Web/availablestacks/Read | Hämta tillgängliga stackar. |
> | Åtgärd | Microsoft.Web/certificates/Delete | Ta bort ett befintligt certifikat. |
> | Åtgärd | Microsoft. Web/certificates/Read | Hämta listan över certifikat. |
> | Åtgärd | Microsoft. Web/certificates/Write | Lägg till ett nytt certifikat eller uppdatera ett befintligt. |
> | Åtgärd | Microsoft. Web/checknameavailability/Read | Kontrol lera om resurs namnet är tillgängligt. |
> | Åtgärd | microsoft.web/classicmobileservices/read | Hämta klassiska Mobile Services. |
> | Åtgärd | Microsoft.Web/connectionGateways/Delete | Tar bort en gateway för anslutning. |
> | Åtgärd | Microsoft. Web/connectionGateways/JOIN/åtgärd | Ansluter till en gateway för anslutning. |
> | Åtgärd | Microsoft.Web/connectionGateways/ListStatus/Action | Visar status för en anslutnings-Gateway. |
> | Åtgärd | Microsoft. webb/connectionGateways/flytta/åtgärd | Flyttar en gateway för anslutning. |
> | Åtgärd | Microsoft. Web/connectionGateways/Read | Hämta listan över anslutnings-gatewayer. |
> | Åtgärd | Microsoft. Web/connectionGateways/Write | Skapar eller uppdaterar en anslutnings-Gateway. |
> | Åtgärd | microsoft.web/connections/confirmconsentcode/action | Bekräfta medgivande kod för anslutningar. |
> | Åtgärd | Microsoft.Web/connections/Delete | Tar bort en anslutning. |
> | Åtgärd | Microsoft. Web/Connections/Join/Action | Ansluter till en anslutning. |
> | Åtgärd | microsoft.web/connections/listconsentlinks/action | Visa en lista med medgivande Länkar för anslutningar. |
> | Åtgärd | Microsoft. Web/Connections/Move/åtgärd | Flyttar en anslutning. |
> | Åtgärd | Microsoft. Web/Connections/Read | Hämta listan över anslutningar. |
> | Åtgärd | Microsoft. Web/Connections/Write | Skapar eller uppdaterar en anslutning. |
> | Åtgärd | Microsoft.Web/customApis/Delete | Tar bort ett anpassat API. |
> | Åtgärd | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extraherar API-definition från ett WSDL. |
> | Åtgärd | Microsoft. Web/customApis/JOIN/åtgärd | Ansluter till en anpassad API. |
> | Åtgärd | Microsoft. Web/customApis/listWsdlInterfaces/åtgärd | Visar en lista över WSDL-gränssnitt för ett anpassat API. |
> | Åtgärd | Microsoft. webb/customApis/flytta/åtgärd | Flyttar ett anpassat API. |
> | Åtgärd | Microsoft. Web/customApis/Read | Hämta listan med anpassade API: er. |
> | Åtgärd | Microsoft. Web/customApis/Write | Skapar eller uppdaterar ett anpassat API. |
> | Åtgärd | Microsoft.Web/deletedSites/Read | Hämta egenskaperna för en borttagen webbapp |
> | Åtgärd | microsoft.web/deploymentlocations/read | Hämta distributions platser. |
> | Åtgärd | Microsoft. Web/Sverige/Read | Hämta listan över geo-regioner. |
> | Åtgärd | microsoft.web/hostingenvironments/capacities/read | Få värd miljöer. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/Delete | Ta bort ett App Service-miljön |
> | Åtgärd | Microsoft. Web/hostingenvironments/Detections/Read | Hämta identifierare för värd miljöer. |
> | Åtgärd | Microsoft. Web/hostingenvironments/Diagnostics/Read | Hämta diagnostik för värd miljöer. |
> | Åtgärd | Microsoft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Hämta nätverks slut punkter för alla inkommande beroenden. |
> | Åtgärd | Microsoft. Web/hostingEnvironments/JOIN/åtgärd | Ansluter till en App Service-miljön |
> | Åtgärd | microsoft.web/hostingenvironments/metricdefinitions/read | Hämta mått definitioner för värd miljöer. |
> | Åtgärd | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/Read | Hämta värd miljöer multiroll-mått definitioner för pooler. |
> | Åtgärd | Microsoft. Web/hostingenvironments/multirolepools/Metrics/Read | Hämta värd miljöer multiroll-mått för pooler. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Hämta egenskaperna för en FrontEnd-pool i en App Service-miljön |
> | Åtgärd | microsoft.web/hostingenvironments/multirolepools/skus/read | Hämta värd miljöer Multirole-pooler SKU: er. |
> | Åtgärd | Microsoft. Web/hostingenvironments/multirolepools/Usage/Read | Få värd miljöer för användning av pooler med multiroller. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Skapa en ny FrontEnd-pool i en App Service-miljön eller uppdatera en befintlig |
> | Åtgärd | microsoft.web/hostingenvironments/operations/read | Hämta åtgärder för värd miljöer. |
> | Åtgärd | Microsoft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Hämta nätverks slut punkter för alla utgående beroenden. |
> | Åtgärd | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Godkänn anslutningar för privata slut punkter |
> | Åtgärd | Microsoft. Web/hostingEnvironments/Read | Hämta egenskaperna för en App Service-miljön |
> | Åtgärd | Microsoft. Web/hostingEnvironments/rereboot/Action | Starta om alla datorer i en App Service-miljön |
> | Åtgärd | Microsoft. Web/hostingenvironments/återuppta/åtgärd | Återuppta värd miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/serverfarms/read | Få värd miljöer App Service planer. |
> | Åtgärd | microsoft.web/hostingenvironments/sites/read | Hämta värd miljöer Web Apps. |
> | Åtgärd | microsoft.web/hostingenvironments/suspend/action | Pausa värd miljöer. |
> | Åtgärd | microsoft.web/hostingenvironments/usages/read | Hämta användning av värd miljöer. |
> | Åtgärd | Microsoft. Web/hostingenvironments/workerpools/metricdefinitions/Read | Hämta värd miljöer Workerpools mått definitioner. |
> | Åtgärd | Microsoft. Web/hostingenvironments/workerpools/Metrics/Read | Hämta värd miljöer Workerpools mått. |
> | Åtgärd | Microsoft. Web/hostingEnvironments/workerPools/Read | Hämta egenskaperna för en arbets grupp i en App Service-miljön |
> | Åtgärd | microsoft.web/hostingenvironments/workerpools/skus/read | Hämta värd miljöer Workerpools SKU: er. |
> | Åtgärd | Microsoft. Web/hostingenvironments/workerpools/Usage/Read | Hämta värd miljöer Workerpools-användningar. |
> | Åtgärd | Microsoft. Web/hostingEnvironments/workerPools/Write | Skapa en ny arbets pool i ett App Service-miljön eller uppdatera en befintlig |
> | Åtgärd | Microsoft. Web/hostingEnvironments/Write | Skapa en ny App Service-miljön eller uppdatera en befintlig |
> | Åtgärd | Microsoft. Web/ishostingenvironmentnameavailable/Read | Ta reda på om värd miljö namnet är tillgängligt. |
> | Åtgärd | Microsoft. Web/ishostnameavailable/Read | Kontrol lera om hostname är tillgängligt. |
> | Åtgärd | microsoft.web/isusernameavailable/read | Kontrol lera om användar namnet är tillgängligt. |
> | Åtgärd | Microsoft. Web/listSitesAssignedToHostName/Read | Hämta namn på platser tilldelade till värdnamn. |
> | Åtgärd | Microsoft. Web/locations/apioperations/Read | Hämta API-åtgärder för platser. |
> | Åtgärd | microsoft.web/locations/connectiongatewayinstallations/read | Hämta platser anslutnings-Gateway-installationer. |
> | Åtgärd | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Borttagnings meddelande för VNet eller undernät för platser. |
> | Åtgärd | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrahera API-definition från WSDL för platser. |
> | Åtgärd | microsoft.web/locations/listwsdlinterfaces/action | Visa en lista över WSDL-gränssnitt för platser. |
> | Åtgärd | Microsoft. Web/locations/managedapis/apioperations/Read | Hämta platser hanterade API-åtgärder. |
> | Åtgärd | Microsoft. Web/locations/managedapis/JOIN/åtgärd | Ansluter till en hanterad API. |
> | Åtgärd | microsoft.web/locations/managedapis/read | Hämta platser hanterade API: er. |
> | Åtgärd | Microsoft. Web/locations/operationResults/Read | Hämta åtgärder. |
> | Åtgärd | Microsoft. Web/locations/Operations/Read | Hämta åtgärder. |
> | Åtgärd | microsoft.web/operations/read | Hämta åtgärder. |
> | Åtgärd | Microsoft. Web/publishingusers/Read | Hämta publicerings användare. |
> | Åtgärd | Microsoft. Web/publishingusers/Write | Uppdatera publicerings användare. |
> | Åtgärd | Microsoft. Web/rekommendationer/läsa | Hämta listan över rekommendationer för prenumerationer. |
> | Åtgärd | Microsoft. Web/register/åtgärd | Registrera Microsoft. Web Resource Provider för prenumerationen. |
> | Åtgärd | Microsoft. Web/resourcehealthmetadata/Read | Hämta Resource Health metadata. |
> | Åtgärd | microsoft.web/serverfarms/capabilities/read | Få App Services plan funktioner. |
> | Åtgärd | Microsoft.Web/serverfarms/Delete | Ta bort en befintlig App Service plan |
> | Åtgärd | Microsoft. Web/Server grupper/eventGridFilters/Delete | Ta bort Event Grid filter i Server gruppen. |
> | Åtgärd | Microsoft. Web/Server grupper/eventGridFilters/Read | Hämta Event Grid filter i Server gruppen. |
> | Åtgärd | Microsoft. Web/Server grupper/eventGridFilters/Write | Lägg Event Grid filter i Server gruppen. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/delete | Ta bort App Service planerar inställningar för första parts appar. |
> | Åtgärd | microsoft.web/serverfarms/firstpartyapps/settings/read | Hämta App Service planerar inställningar för den första partens appar. |
> | Åtgärd | Microsoft. Web/Server grupper/firstpartyapps/Settings/Write | Uppdatera App Service planerar inställningarna för den första parts-appen. |
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
