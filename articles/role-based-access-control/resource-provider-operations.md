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
ms.date: 07/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: ce4765a512b81d13f735a05ad4fba5408284a607
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501409"
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Åtgärder för Azure Resource Manager Resource Provider

Den här artikeln visar de åtgärder som är tillgängliga för varje Azure Resource Manager resurs leverantör. Dessa åtgärder kan användas i [anpassade roller](custom-roles.md) för att ge detaljerad [rollbaserad åtkomst kontroll (RBAC)](overview.md) till resurser i Azure. Åtgärds strängar har följande format:`{Company}.{ProviderName}/{resourceType}/{action}`

Resurs leverantörs åtgärderna utvecklas alltid. Använd [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) eller [AZ Provider operation List](/cli/azure/provider/operation#az-provider-operation-list)för att få de senaste åtgärderna.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. AAD/domainServices/Delete | Ta bort domän tjänst |
> | Action | Microsoft. AAD/domainServices/oucontainer/Delete | Ta bort ou-behållare |
> | Action | Microsoft. AAD/domainServices/oucontainer/Read | Läs ou-behållare |
> | Action | Microsoft. AAD/domainServices/oucontainer/Write | Skriv ou-behållare |
> | Action | Microsoft. AAD/domainServices/Read | Läs domän tjänster |
> | Action | Microsoft. AAD/domainServices/replicaSets/Delete | Ta bort kluster plats |
> | Action | Microsoft. AAD/domainServices/replicaSets/Read | Läs kluster plats |
> | Action | Microsoft. AAD/domainServices/replicaSets/Write | Skriv kluster webbplats |
> | Action | Microsoft. AAD/domainServices/Write | Skriv domän tjänst |
> | Action | Microsoft. AAD/locations/operationresults/Read |  |
> | Action | Microsoft. AAD/åtgärder/läsa |  |
> | Action | Microsoft. AAD/register/åtgärd | Registrera domän tjänst |
> | Action | Microsoft. AAD/avregistrera/åtgärd | Avregistrera domän tjänsten |

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. aadiam/diagnosticsettings/Delete | Tar bort en diagnostisk inställning |
> | Action | Microsoft. aadiam/diagnosticsettings/Read | Läser en diagnostisk inställning |
> | Action | Microsoft. aadiam/diagnosticsettings/Write | Skriva en diagnostisk inställning |
> | Action | Microsoft. aadiam/diagnosticsettingscategories/Read | Läser en diagnostisk inställnings kategori |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. addons/Operations/Read | Hämtar RP-åtgärder som stöds. |
> | Action | Microsoft. addons/register/åtgärd | Registrera den angivna prenumerationen med Microsoft. addons |
> | Action | Microsoft. addons/supportProviders/listsupportplaninfo/Action | Visar aktuell support Plans information för den angivna prenumerationen. |
> | Action | Microsoft. addons/supportProviders/supportPlanTypes/Delete | Tar bort det angivna kanoniska support avtalet |
> | Action | Microsoft. addons/supportProviders/supportPlanTypes/Read | Hämta det angivna kanoniska support Plans tillstånd. |
> | Action | Microsoft. addons/supportProviders/supportPlanTypes/Write | Lägger till den kanoniska support Plans typen som angetts. |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.ADHybridHealthService/addsservices/action | Skapa en ny skog för klient organisationen. |
> | Action | Microsoft.ADHybridHealthService/addsservices/addomainservicemembers/read | Hämtar alla servrar för det angivna tjänst namnet. |
> | Action | Microsoft.ADHybridHealthService/addsservices/alerts/read | Hämtar aviserings information för skogen, t. ex. AlertID, varnings datum, avisering senast identifierad, aviserings beskrivning, senast uppdaterad, aviserings nivå, aviserings tillstånd, fel söknings länkar osv. |
> | Action | Microsoft.ADHybridHealthService/addsservices/configuration/read | Hämtar tjänst konfiguration för skogen. Exempel – skogs namn, funktions nivå, FSMO-roll för domän namn givnings hanterare, FSMO-roll för schema hanterare osv. |
> | Action | Microsoft.ADHybridHealthService/addsservices/delete | Tar bort en tjänst och dess servrar tillsammans med hälso data. |
> | Action | Microsoft.ADHybridHealthService/addsservices/dimensions/read | Hämtar information om domäner och platser för skogen. Exempel – hälso status, aktiva aviseringar, lösta aviseringar, egenskaper som domän funktions nivå, skog, infrastruktur hanterare, PDC, RID-hanterare osv.  |
> | Action | Microsoft.ADHybridHealthService/addsservices/features/userpreference/read | Hämtar användar inställnings inställningen för skogen.<br>Exempel – MetricCounterName som ldapsuccessfulbinds, ntlmauthentications, kerberosauthentications, addsinsightsagentprivatebytes, ldapsearches.<br>Inställningar för GRÄNSSNITTs diagram osv. |
> | Action | Microsoft.ADHybridHealthService/addsservices/forestsummary/read | Hämtar skogs Sammanfattning för den aktuella skogen som skogs namn, antal domäner under den här skogen, antal platser och plats information osv. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metricmetadata/read | Hämtar listan över mått som stöds för en specifik tjänst.<br>Till exempel utelåsning av extra nät konton, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFS-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv. för ADDomainService.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Action | Microsoft.ADHybridHealthService/addsservices/metrics/groups/read | Med den här API: n hämtas mått informationen.<br>Detta API kan till exempel användas för att hämta information som rör: Konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänst.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Action | Microsoft.ADHybridHealthService/addsservices/premiumcheck/read | Detta API hämtar listan över alla inbyggda ADDomainServices för en Premium-klient. |
> | Action | Microsoft.ADHybridHealthService/addsservices/read | Hämtar tjänst information för det angivna tjänst namnet. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationdetails/read | Hämtar replikeringsinformation för alla servrar för det angivna tjänst namnet. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationstatus/read | Hämtar antalet domänkontrollanter och deras replikeringsfel om några. |
> | Action | Microsoft.ADHybridHealthService/addsservices/replicationsummary/read | Hämtar fullständig domänkontrollant-lista tillsammans med replikeringsinformation för den aktuella skogen. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/action | Lägg till en Server instans till tjänsten. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/credentials/read | Under Server registreringen av ADDomainService anropas detta API för att hämta autentiseringsuppgifterna för att registrera nya servrar. |
> | Action | Microsoft.ADHybridHealthService/addsservices/servicemembers/delete | Tar bort en server för en specifik tjänst och klient. |
> | Action | Microsoft.ADHybridHealthService/addsservices/write | Skapar eller uppdaterar ADDomainService-instansen för klienten. |
> | Action | Microsoft.ADHybridHealthService/configuration/action | Uppdaterar klient konfigurationen. |
> | Action | Microsoft.ADHybridHealthService/configuration/read | Läser klient konfigurationen. |
> | Action | Microsoft.ADHybridHealthService/configuration/write | Skapar en klient konfiguration. |
> | Action | Microsoft.ADHybridHealthService/logs/contents/read | Hämtar innehållet i agent installations-och registrerings loggar som lagras i BLOB. |
> | Action | Microsoft.ADHybridHealthService/logs/read | Hämtar agent installations-och registrerings loggar för klient organisationen. |
> | Action | Microsoft.ADHybridHealthService/operations/read | Hämtar en lista över åtgärder som stöds av systemet. |
> | Action | Microsoft.ADHybridHealthService/register/action | Registrerar ADHybrid-Hälsotjänst Resource Provider och gör det möjligt att skapa ADHybrid-Hälsotjänst resurs. |
> | Action | Microsoft. ADHybridHealthService/Reports/availabledeployments/Read | Hämtar en lista över tillgängliga regioner som används av DevOps för att stödja kund incidenter. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassword/read | Hämtar listan över felaktiga lösen ords försök för alla användare i Active Directory Federationstjänst. |
> | Action | Microsoft.ADHybridHealthService/reports/badpassworduseridipfrequency/read | Hämtar blobb-URI: n med status och det slutliga resultatet av det nyligen köade rapport jobbet för frekvensen av felaktiga användar namn/lösen ords försök per UserId per IP-adress och dag för en viss klient. |
> | Action | Microsoft.ADHybridHealthService/reports/consentedtodevopstenants/read | Hämtar listan över DevOps-godkända klienter. Används vanligt vis för kund support. |
> | Action | Microsoft.ADHybridHealthService/reports/isdevops/read | Hämtar ett värde som anger om klienten är DevOps eller inte. |
> | Action | Microsoft.ADHybridHealthService/reports/selectdevopstenant/read | Uppdaterar UserID (ObjectID) för den valda dev-klienten. |
> | Action | Microsoft.ADHybridHealthService/reports/selecteddeployment/read | Hämtar vald distribution för den angivna klienten. |
> | Action | Microsoft.ADHybridHealthService/reports/tenantassigneddeployment/read | Ett klient-ID hämtar klient organisationens lagrings plats. |
> | Action | Microsoft.ADHybridHealthService/reports/updateselecteddeployment/read | Hämtar den geografiska plats från vilken data kommer att nås. |
> | Action | Microsoft.ADHybridHealthService/services/action | Uppdaterar en tjänst instans i klient organisationen. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringarna för en tjänst. |
> | Action | Microsoft.ADHybridHealthService/services/alerts/read | Läser aviseringarna för en tjänst. |
> | Action | Microsoft.ADHybridHealthService/services/checkservicefeatureavailibility/read | Med ett funktions namn verifieras om en tjänst har allt som krävs för att använda funktionen. |
> | Action | Microsoft.ADHybridHealthService/services/delete | Tar bort en tjänst instans i klient organisationen. |
> | Action | Microsoft.ADHybridHealthService/services/exporterrors/read | Hämtar export fel för en specifik Sync-tjänst. |
> | Action | Microsoft.ADHybridHealthService/services/exportstatus/read | Hämtar export status för en specifik tjänst. |
> | Action | Microsoft. ADHybridHealthService/Services/feedbacktype/feedback/Read | Hämtar feedback om aviseringar för en specifik tjänst och server. |
> | Action | Microsoft.ADHybridHealthService/services/metricmetadata/read | Hämtar listan över mått som stöds för en specifik tjänst.<br>Till exempel utelåsning av extra nät konton, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFS-tjänsten.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv. för ADDomainService.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för ADSync-tjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/average/read | Med den här API: n hämtas medelvärdet för mått för en specifik tjänst.<br>Detta API kan till exempel användas för att hämta information som rör: Konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänst.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/read | Med den här API: n hämtas mått informationen.<br>Detta API kan till exempel användas för att hämta information som rör: Konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänst.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/metrics/groups/sum/read | Med den här API: n hämtas den sammanställda vyn för mått för en specifik tjänst.<br>Detta API kan till exempel användas för att hämta information som rör: Konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänst.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfiguration/write | Lägga till eller uppdatera övervaknings konfigurationen för en tjänst. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/read | Hämtar övervaknings konfigurationerna för en specifik tjänst. |
> | Action | Microsoft.ADHybridHealthService/services/monitoringconfigurations/write | Lägg till eller uppdatera övervaknings konfigurationerna för en tjänst. |
> | Action | Microsoft.ADHybridHealthService/services/premiumcheck/read | Detta API hämtar listan över alla inbyggda tjänster för en Premium-klient. |
> | Action | Microsoft.ADHybridHealthService/services/read | Läser tjänst instanserna i klienten. |
> | Action | Microsoft.ADHybridHealthService/services/reports/blobUris/read | Hämtar alla riskfyllda IP-rapport-URI: er under de senaste 7 dagarna. |
> | Action | Microsoft. ADHybridHealthService/Services/Reports/information/läsa | Hämtar rapport över främsta 50-användare med felaktiga lösen ords fel från de senaste 7 dagarna |
> | Action | Microsoft.ADHybridHealthService/services/reports/generateBlobUri/action | Genererar en riskfylld IP-rapport och returnerar en URI som pekar på den. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/action | Skapar en Server instans i tjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/alerts/read | Läser aviseringarna för en server. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/credentials/read | Under Server registreringen anropas detta API för att hämta autentiseringsuppgifterna för att registrera nya servrar. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/datafreshness/read | För en specifik server hämtar detta API en lista med data typer som laddas upp av servrarna och den senaste tiden för varje uppladdning. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/delete | Tar bort en Server instans i tjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/exportstatus/read | Hämtar information om synkronisering av export fel för en specifik synkroniseringstjänst. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/groups/read | Med den här API: n hämtas mått informationen.<br>Detta API kan till exempel användas för att hämta information som rör: Konto utelåsningar för extra nät, totalt antal misslyckade förfrågningar, utestående token-begäranden (proxy), token begär Anden per sekund för ADFederation-tjänst.<br>NTLM-autentiseringar/SEK, LDAP-lyckade bindningar/SEK, LDAP-bindnings tid, LDAP aktiva trådar, Kerberos-autentiseringar/SEK, antalet ATQ-trådar osv för ADDomain-tjänsten.<br>Kör profil svars tid, TCP-anslutningar etablerade, insikter agent privat byte, exportera statistik till Azure AD för synkroniseringstjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/metrics/read | Hämtar listan över kopplingar och kör profil namn för den tjänst-och tjänst medlem som angetts. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/read | Läser Server instansen i tjänsten. |
> | Action | Microsoft.ADHybridHealthService/services/servicemembers/serviceconfiguration/read | Hämtar tjänst konfigurationen för en specifik klient organisation. |
> | Action | Microsoft.ADHybridHealthService/services/tenantwhitelisting/read | Hämtar funktionens vit listning status för en specifik klient. |
> | Action | Microsoft.ADHybridHealthService/services/write | Skapar en tjänst instans i klienten. |
> | Action | Microsoft.ADHybridHealthService/unregister/action | Avregistrerar prenumerationen för ADHybrid Hälsotjänst Resource Provider. |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Advisor/configurations/read | Hämta konfigurationer |
> | Action | Microsoft.Advisor/configurations/write | Skapar/uppdaterar konfiguration |
> | Action | Microsoft.Advisor/generateRecommendations/action | Skapar rekommendationer |
> | Action | Microsoft.Advisor/generateRecommendations/read | Hämtar status för rekommendationer |
> | Action | Microsoft.Advisor/metadata/read | Hämta metadata |
> | Action | Microsoft.Advisor/operations/read | Hämtar åtgärderna för Microsoft Advisor |
> | Action | Microsoft. Advisor/rekommendationer/tillgänglig/åtgärd | Ny rekommendation finns i Microsoft Advisor |
> | Action | Microsoft. Advisor/rekommendationer/läsa | Läser rekommendationer |
> | Action | Microsoft. Advisor/rekommendationer/undertrycks/ta bort | Tar bort utelämnande |
> | Action | Microsoft. Advisor/rekommendationer/under tryckningar/läsning | Hämtar utelämnar |
> | Action | Microsoft. Advisor/rekommendationer/undertrycks/Skriv åtgärder | Skapar/uppdaterar ignorerar |
> | Action | Microsoft. Advisor/register/åtgärd | Registrerar prenumerationen för Microsoft Advisor |
> | Action | Microsoft. Advisor/undertrycks/ta bort | Tar bort utelämnande |
> | Action | Microsoft. Advisor/undertrycks/läsa | Hämtar utelämnar |
> | Action | Microsoft. Advisor/undertrycker/Skriv | Skapar/uppdaterar ignorerar |
> | Action | Microsoft. Advisor/avregistrering/åtgärd | Avregistrerar prenumerationen för Microsoft Advisor |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.AlertsManagement/actionRules/delete | Ta bort åtgärds regel i en specifik prenumeration. |
> | Action | Microsoft.AlertsManagement/actionRules/read | Hämta alla åtgärds regler för inkommande filter. |
> | Action | Microsoft.AlertsManagement/actionRules/write | Skapa eller uppdatera åtgärds regel i en specifik prenumeration |
> | Action | Microsoft.AlertsManagement/alerts/changestate/action | Ändra aviseringens status. |
> | Action | Microsoft. AlertsManagement/Alerts/Diagnostics/Read | Hämta all diagnostik för aviseringen |
> | Action | Microsoft. AlertsManagement/Alerts/History/Read | Hämta historik för aviseringen |
> | Action | Microsoft.AlertsManagement/alerts/read | Hämta alla aviseringar för inkommande filter. |
> | Action | Microsoft.AlertsManagement/alertsList/read | Hämta alla aviseringar för inkommande filter över prenumerationer |
> | Action | Microsoft. AlertsManagement/alertsMetaData/Read | Hämta aviserings-meta-data för indataparametern. |
> | Action | Microsoft.AlertsManagement/alertsSummary/read | Hämta sammanfattningen av aviseringar |
> | Action | Microsoft.AlertsManagement/alertsSummaryList/read | Få en översikt över aviseringar över prenumerationer |
> | Action | Microsoft. AlertsManagement/Operations/Read | Läser de åtgärder som tillhandahålls |
> | Action | Microsoft. AlertsManagement/register/åtgärd | Registrerar prenumerationen för Microsoft Alerts Management |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/delete | Ta bort varnings regeln för smart detektor i en specifik prenumeration |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/read | Hämta alla varnings regler för smart detektor för inkommande filter |
> | Action | Microsoft.AlertsManagement/smartDetectorAlertRules/write | Skapa eller uppdatera varnings regeln för smart detektor i en specifik prenumeration |
> | Action | Microsoft.AlertsManagement/smartGroups/changestate/action | Ändra status för den smarta gruppen |
> | Action | Microsoft. AlertsManagement/smartGroups/historik/läsa | Hämta historik för den smarta gruppen |
> | Action | Microsoft.AlertsManagement/smartGroups/read | Hämta alla smarta grupper för inkommande filter |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.AnalysisServices/locations/checkNameAvailability/action | Kontrollerar att angivet Analysis Server namn är giltigt och inte används. |
> | Action | Microsoft.AnalysisServices/locations/operationresults/read | Hämtar information om det angivna åtgärds resultatet. |
> | Action | Microsoft.AnalysisServices/locations/operationstatuses/read | Hämtar information om den angivna åtgärds statusen. |
> | Action | Microsoft. AnalysisServices/Operations/Read | Hämtar information om åtgärder |
> | Action | Microsoft. AnalysisServices/register/åtgärd | Registrerar Analysis Services Resource Provider. |
> | Action | Microsoft.AnalysisServices/servers/delete | Tar bort Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/listGatewayStatus/action | Visar status för den gateway som är associerad med servern. |
> | Action | Microsoft.AnalysisServices/servers/read | Hämtar information om den angivna Analysis Server. |
> | Action | Microsoft. AnalysisServices/servrar/återuppta/åtgärd | Återupptar Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/skus/read | Hämta tillgänglig SKU-information för servern |
> | Action | Microsoft. AnalysisServices/-servrar/PAUSE/åtgärd | Pausar Analysis Server. |
> | Action | Microsoft.AnalysisServices/servers/write | Skapar eller uppdaterar den angivna Analysis Server. |
> | Action | Microsoft.AnalysisServices/skus/read | Hämtar information om SKU: er |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. API Management/checkNameAvailability/Read | Kontrollerar om det angivna tjänst namnet är tillgängligt |
> | Action | Microsoft. API Management/Operations/Read | Läs alla API-åtgärder som är tillgängliga för Microsoft. API Management-resursen |
> | Action | Microsoft. API Management/register/åtgärd | Registrera prenumeration för Microsoft. API Management Resource Provider |
> | Action | Microsoft. API Management/Reports/Read | Hämta rapporter sammanställda per tids period, geografisk region, utvecklare, produkter, API: er, åtgärder, prenumeration och byRequest. |
> | Action | Microsoft. API Management/Service/API/Delete | Tar bort det angivna API: t för API Management tjänst instansen. |
> | Action | Microsoft. API Management/Service/Apis/diagnostik/ta bort | Tar bort den angivna diagnostiken från ett API. |
> | Action | Microsoft. API Management/Service/Apis/diagnostik/läsa | Visar all diagnostik för ett API. eller hämtar information om diagnostiken för ett API som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/Apis/diagnostik/Skriv | Skapar en ny diagnostik för ett API eller uppdaterar en befintlig. eller uppdaterar informationen om diagnostiken för ett API som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/API/problem/bilagor/ta bort | Tar bort den angivna kommentaren från ett problem. |
> | Action | Microsoft. API Management/Service/API/problem/bilagor/läsa | Visar alla bifogade filer för det problem som är associerat med angivet API. eller hämtar information om den bifogade filen för ett API som anges av dess identifierare. |
> | Action | Microsoft.ApiManagement/service/apis/issues/attachments/write | Skapar en ny bilaga för problemet i ett API eller uppdaterar ett befintligt. |
> | Action | Microsoft. API Management/Service/API/problem/kommentarer/ta bort | Tar bort den angivna kommentaren från ett problem. |
> | Action | Microsoft. API Management/Service/API/problem/kommentarer/läsa | Visar alla kommentarer för det problem som är associerat med angivet API. eller hämtar information om problem kommentaren för ett API som anges av dess identifierare. |
> | Action | Microsoft.ApiManagement/service/apis/issues/comments/write | Skapar en ny kommentar för problemet i ett API eller uppdaterar ett befintligt. |
> | Action | Microsoft. API Management/Service/API/problem/ta bort | Tar bort det angivna problemet från ett API. |
> | Action | Microsoft. API Management/Service/API/problem/läsa | Visar alla problem som är associerade med angivet API. eller hämtar information om problemet för ett API som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/API/problem/skriv | Skapar ett nytt problem för ett API eller uppdaterar ett befintligt. eller uppdaterar ett befintligt problem för ett API. |
> | Action | Microsoft. API Management/Service/API/Operations/Delete | Tar bort den angivna åtgärden i API: et. |
> | Action | Microsoft. API Management/Service/Apis/Operations/policies/Delete | Tar bort princip konfigurationen i API-åtgärden. |
> | Action | Microsoft. API Management/Service/Apis/Operations/policies/Read | Hämta listan över princip konfigurationen på API-åtgärds nivån. eller hämta princip konfigurationen på nivån API-åtgärd. |
> | Action | Microsoft. API Management/Service/API/Operations/policies/Write | Skapar eller uppdaterar princip konfigurationen för API-åtgärds nivån. |
> | Action | Microsoft. API Management/Service/Apis/Operations/policy/Delete | Ta bort princip konfigurationen på åtgärds nivå |
> | Action | Microsoft. API Management/Service/Apis/Operations/policy/Read | Hämta princip konfigurationen på åtgärds nivå |
> | Action | Microsoft. API Management/Service/Apis/Operations/policy/Write | Skapa princip konfiguration på åtgärds nivå |
> | Action | Microsoft.ApiManagement/service/apis/operations/read | Visar en samling åtgärder för angivet API. eller hämtar information om den API-åtgärd som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Apis/Operations/Tags/Delete | Koppla bort taggen från åtgärden. |
> | Action | Microsoft.ApiManagement/service/apis/operations/tags/read | Visar en lista över alla Taggar som är associerade med åtgärden. eller hämta taggen som är associerad med åtgärden. |
> | Action | Microsoft. API Management/Service/Apis/Operations/Tags/Write | Tilldela en tagg till åtgärden. |
> | Action | Microsoft. API Management/Service/Apis/åtgärder/skrivning | Skapar en ny åtgärd i API: et eller uppdaterar en befintlig. eller uppdaterar informationen om åtgärden i API: et som anges av dess identifierare. |
> | Action | Microsoft.ApiManagement/service/apis/operationsByTags/read | Visar en samling åtgärder som är kopplade till taggar. |
> | Action | Microsoft. API Management/Service/Apis/policies/Delete | Tar bort princip konfigurationen i API: et. |
> | Action | Microsoft. API Management/Service/Apis/policies/Read | Hämta princip konfigurationen på API-nivå. eller hämta princip konfigurationen på API-nivå. |
> | Action | Microsoft. API Management/Service/Apis/policies/Write | Skapar eller uppdaterar princip konfigurationen för API: et. |
> | Action | Microsoft. API Management/Service/Apis/princip/ta bort | Ta bort princip konfigurationen på API-nivå |
> | Action | Microsoft. API Management/Service/Apis/policy/Read | Hämta princip konfigurationen på API-nivå |
> | Action | Microsoft. API Management/Service/Apis/princip/Skriv | Skapa princip konfiguration på API-nivå |
> | Action | Microsoft. API Management/Service/Apis/Products/Read | Visar alla produkter som API: et är en del av. |
> | Action | Microsoft. API Management/Service/API/Read | Visar en lista över alla API: er för API Management tjänst instansen. eller hämtar information om det API som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/API/releases/Delete | Tar bort alla versioner av API: et eller tar bort den angivna versionen i API: et. |
> | Action | Microsoft.ApiManagement/service/apis/releases/read | Visar en lista över alla utgåvor av ett API.<br>En API-version skapas när en API-revision är aktuell.<br>Versioner används också för att återställa till tidigare revisioner.<br>Resultaten kommer att växlas och kan begränsas av $top och $skip parametrar.<br>eller returnerar information om en API-version. |
> | Action | Microsoft. API Management/Service/API/releases/Write | Skapar en ny version för API: et. eller uppdaterar informationen om versionen av API: et som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Apis/revisioner/Delete | Tar bort alla revisioner av ett API |
> | Action | Microsoft. API Management/Service/Apis/revisioner/Read | Visar alla revisioner av ett API. |
> | Action | Microsoft. API Management/Service/API/schema/ta bort | Tar bort schema konfigurationen i API: et. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/read | Hämta schema konfigurationen på API-nivå. eller hämta schema konfigurationen på API-nivå. |
> | Action | Microsoft.ApiManagement/service/apis/schemas/write | Skapar eller uppdaterar schema konfigurationen för API: et. |
> | Action | Microsoft. API Management/Service/API/tagDescriptions/Delete | Ta bort etikett beskrivning för API: et. |
> | Action | Microsoft. API Management/Service/API/tagDescriptions/Read | Visar en lista med beskrivningar av alla Taggar i API-omfånget. Modellen som liknar Swagger-tagDescription har definierats på API-nivå men taggen kan tilldelas till åtgärder eller hämta tag-beskrivningen i API-omfånget |
> | Action | Microsoft. API Management/Service/API/tagDescriptions/Write | Skapa/uppdatera tagg-beskrivning i omfånget för API: et. |
> | Action | Microsoft. API Management/Service/API/Taggar/ta bort | Koppla bort taggen från API: et. |
> | Action | Microsoft. API Management/Service/API/Taggar/läsa | Visar alla Taggar som är kopplade till API: et. eller hämta taggen som är associerad med API: et. |
> | Action | Microsoft. API Management/Service/API/Taggar/skriva | Tilldela en tagg till API: et. |
> | Action | Microsoft. API Management/Service/API/Write | Skapar nya eller uppdateringar av befintligt angivet API för API Management tjänst instansen. eller uppdaterar angivet API för API Management tjänst instansen. |
> | Action | Microsoft.ApiManagement/service/apisByTags/read | Visar en samling API: er som är associerade med taggar. |
> | Action | Microsoft. API Management/Service/apiVersionSets/Delete | Tar bort en angiven API-version. |
> | Action | Microsoft. API Management/Service/apiVersionSets/Read | Visar en samling av API-versioner som anges i den angivna tjänst instansen. eller hämtar information om den API-version som anges av dess identifierare. |
> | Action | Microsoft.ApiManagement/service/apiVersionSets/versions/read | Hämta lista över versions enheter |
> | Action | Microsoft. API Management/Service/apiVersionSets/Write | Skapar eller uppdaterar en API-version uppsättning. eller uppdaterar informationen om API-VersionSet som anges av dess identifierare. |
> | Action | Microsoft.ApiManagement/service/applynetworkconfigurationupdates/action | Uppdaterar de Microsoft. API Management-resurser som körs i Virtual Network för att välja uppdaterade nätverks inställningar. |
> | Action | Microsoft. API Management/Service/authorizationServers/Delete | Tar bort en angiven instans av auktoriseringsservern. |
> | Action | Microsoft. API Management/Service/authorizationServers/Read | Visar en uppsättning auktoriserade servrar som definierats inom en tjänst instans. eller hämtar information om den Authorization-server som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/authorizationServers/Write | Skapar en ny auktoriseringsprincip eller uppdaterar en befintlig auktoriseringsprincip. eller uppdaterar informationen om auktoriseringsservern som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/server del/Delete | Tar bort den angivna Server delen. |
> | Action | Microsoft. API Management/Service/backend/Read | Visar en samling av Server delar i den angivna tjänst instansen. eller hämtar information om Server delen som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/server delar/reconnect/åtgärd | Meddelar APIM proxy om att skapa en ny anslutning till Server delen efter den angivna tids gränsen. Om ingen tids gräns angavs används tids gränsen på 2 minuter. |
> | Action | Microsoft. API Management/Service/backend/Write | Skapar eller uppdaterar en server del. eller uppdaterar en befintlig server del. |
> | Action | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiera API Management tjänst till den angivna behållaren i ett användardefinierat lagrings konto |
> | Action | Microsoft. API Management/Service/Caches/Delete | Tar bort en angiven cache. |
> | Action | Microsoft. API Management/Service/cache/Read | Visar en samling med alla externa cacheminnen i den angivna tjänst instansen. eller hämtar information om cachen som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/cache/Write | Skapar eller uppdaterar ett externt cacheminne som ska användas i API Management-instansen. eller uppdaterar informationen om den cache som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/certificates/Delete | Tar bort ett speciellt certifikat. |
> | Action | Microsoft. API Management/Service/certificates/Read | Listar en samling med alla certifikat i den angivna tjänst instansen. eller hämtar information om det certifikat som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/certificates/Write | Skapar eller uppdaterar det certifikat som används för autentisering med Server delen. |
> | Action | Microsoft. API Management/Service/contentType/contentItems/Delete | Tar bort angivet innehålls objekt. |
> | Action | Microsoft. API Management/Service/contentType/contentItems/Read | Returnerar lista med innehålls objekt eller returnerar information om innehålls objekt |
> | Action | Microsoft. API Management/Service/contentType/contentItems/Write | Skapar nytt innehålls objekt eller uppdaterar angivet innehålls objekt |
> | Action | Microsoft. API Management/Service/contentType/Read | Returnerar lista över innehålls typer |
> | Action | Microsoft.ApiManagement/service/delete | Ta bort API Management tjänst instans |
> | Action | Microsoft. API Management/Service/Diagnostics/Delete | Tar bort den angivna diagnostiken. |
> | Action | Microsoft. API Management/Service/Diagnostics/Read | Visar en lista över alla diagnostiker för den API Management tjänst instansen. eller hämtar information om diagnostiken som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/Diagnostics/Write | Skapar en ny diagnostik eller uppdaterar en befintlig. eller uppdaterar informationen om diagnostiken som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/Gateways/åtgärd | Hämtar gateway-konfiguration. eller uppdaterar gatewayens pulsslag. |
> | Action | Microsoft. API Management/Service/Gateways/Delete | Tar bort en angiven Gateway. |
> | Action | Microsoft. API Management/Service/Gateways/nycklar/åtgärd | Hämtar Gateway-nycklar. |
> | Action | Microsoft. API Management/Service/Gateway/Read | Visar en lista med gateways som registrerats med tjänst instansen. eller hämtar information om den gateway som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Gateways/regeneratePrimaryKey/åtgärd | Återskapar den primära Gateway-nyckeln invalidationg alla tokens som skapats med den. |
> | Action | Microsoft. API Management/Service/Gateways/regenerateSecondaryKey/åtgärd | Återskapar en sekundär Gateway-nyckel invalidationg alla tokens som skapats med den. |
> | Action | Microsoft. API Management/Service/Gateways/token/Action | Hämtar autentiseringstoken för delad åtkomst för gatewayen. |
> | Action | Microsoft. API Management/Service/Gateway/Write | Skapar eller uppdaterar en gateway som ska användas i API Management-instansen. eller uppdaterar informationen om den gateway som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/getssotoken/åtgärd | Hämtar SSO-token som kan användas för att logga in på API Management tjänstens äldre portal som administratör |
> | Action | Microsoft. API Management/Service/Groups/Delete | Tar bort en angiven grupp av API Management tjänst instansen. |
> | Action | Microsoft. API Management/Service/Groups/Read | Visar en samling grupper som definierats inom en tjänst instans. eller hämtar information om den grupp som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Groups/Users/Delete | Ta bort befintlig användare från befintlig grupp. |
> | Action | Microsoft. API Management/Service/Groups/Users/Read | Visar en samling av användar enheter som är kopplade till gruppen. |
> | Action | Microsoft. API Management/Service/Groups/Users/Write | Lägg till befintlig användare i befintlig grupp |
> | Action | Microsoft. API Management/Service/Groups/Write | Skapar eller uppdaterar en grupp. eller uppdaterar detaljerna för den grupp som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Identityprovider/Delete | Tar bort den angivna konfigurationen för identitetsprovider. |
> | Action | Microsoft. API Management/Service/Identityprovider/Read | Visar en samling identitets leverantörer som kon figurer ATS i den angivna tjänst instansen. eller hämtar konfigurations information för den identitets leverantör som kon figurer ATS i angiven tjänst instans. |
> | Action | Microsoft. API Management/Service/Identityprovider/Write | Skapar eller uppdaterar IdentityProvider-konfigurationen. eller uppdaterar en befintlig IdentityProvider-konfiguration. |
> | Action | Microsoft. API Management/Service/Issues/Read | Visar en samling problem i den angivna tjänst instansen. eller får API Management problem information |
> | Action | Microsoft. API Management/Service/locations/networkstatus/Read | Hämtar nätverks åtkomst status för resurser som tjänsten är beroende av på platsen. |
> | Action | Microsoft. API Management/Service/loggar/ta bort | Tar bort den angivna loggaren. |
> | Action | Microsoft. API Management/Service/loggar/Läs | Visar en samling loggar i den angivna tjänst instansen. eller hämtar information om den loggning som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/loggar/Skriv | Skapar eller uppdaterar en loggare. eller uppdaterar en befintlig loggare. |
> | Action | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Managements tjänsten |
> | Action | Microsoft. API Management/Service/networkstatus/Read | Hämtar nätverks åtkomst status för resurser som tjänsten är beroende av. |
> | Action | Microsoft. API Management/Service/Notifications/Action | Skickar ett meddelande till en angiven användare |
> | Action | Microsoft. API Management/Service/Notifications/Read | Listar en samling egenskaper som definierats inom en tjänst instans. eller hämtar information om meddelandet som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Notifications/recipientEmails/Delete | Tar bort e-postmeddelandet från listan över meddelanden. |
> | Action | Microsoft. API Management/Service/Notifications/recipientEmails/Read | Hämtar listan över e-postmeddelandets mottagare som prenumererar på ett meddelande. |
> | Action | Microsoft. API Management/Service/Notifications/recipientEmails/Write | Lägger till e-postadressen i listan över mottagare för aviseringen. |
> | Action | Microsoft. API Management/Service/Notifications/recipientUsers/Delete | Tar bort API Management användaren från listan över meddelanden. |
> | Action | Microsoft. API Management/Service/Notifications/recipientUsers/Read | Hämtar listan över meddelande mottagarens användare som prenumererar på meddelandet. |
> | Action | Microsoft. API Management/Service/Notifications/recipientUsers/Write | Lägger till API Management användare i listan över mottagare för meddelandet. |
> | Action | Microsoft. API Management/Service/Notifications/Write | Skapa eller uppdatera API Management utgivar meddelande. |
> | Action | Microsoft. API Management/Service/openidConnectProviders/Delete | Tar bort en speciell OpenID Connect-Provider för API Management tjänst instansen. |
> | Action | Microsoft.ApiManagement/service/openidConnectProviders/read | Listor över alla OpenId Connect-providrar. eller hämtar en speciell OpenID Connect-Provider. |
> | Action | Microsoft. API Management/Service/openidConnectProviders/Write | Skapar eller uppdaterar OpenID Connect-providern. eller uppdaterar den angivna OpenID Connect-providern. |
> | Action | Microsoft. API Management/Service/operationresults/Read | Hämtar aktuell status för tids krävande åtgärd |
> | Action | Microsoft. API Management/Service/policies/Delete | Tar bort den globala princip konfigurationen för API Management-tjänsten. |
> | Action | Microsoft. API Management/Service/policies/Read | Visar en lista över alla globala princip definitioner för API Management-tjänsten. eller hämta den globala princip definitionen för API Management-tjänsten. |
> | Action | Microsoft. API Management/Service/policies/Write | Skapar eller uppdaterar den globala princip konfigurationen för API Management-tjänsten. |
> | Action | Microsoft. API Management/Service/policy/Delete | Ta bort princip konfigurationen på klient nivå |
> | Action | Microsoft. API Management/Service/policy/Read | Hämta princip konfigurationen på klient nivå |
> | Action | Microsoft. API Management/Service/policy/Write | Skapa princip konfiguration på klient nivå |
> | Action | Microsoft. API Management/Service/policySnippets/Read | Visar en lista över alla princip fragment. |
> | Action | Microsoft.ApiManagement/service/portalsettings/read | Få inloggnings inställningar för portalen eller få registrerings inställningar för portalen eller hämta Delegerings inställningar för portalen. |
> | Action | Microsoft. API Management/Service/portalsettings/Write | Uppdatera inloggnings inställningar. eller skapa eller uppdatera inloggnings inställningar. eller uppdatera registrerings inställningarna eller uppdatera inställningarna för registrering eller uppdatera delegering. eller skapa eller uppdatera Delegerings inställningar. |
> | Action | Microsoft. API Management/Service/Products/API/Delete | Tar bort angivet API från den angivna produkten. |
> | Action | Microsoft. API Management/Service/Products/API/Read | Visar en samling med API: er som är associerade med en produkt. |
> | Action | Microsoft. API Management/Service/Products/API/Write | Lägger till ett API till den angivna produkten. |
> | Action | Microsoft. API Management/Service/Products/Delete | Ta bort produkt. |
> | Action | Microsoft. API Management/Service/Products/Groups/Delete | Tar bort associationen mellan den angivna gruppen och produkten. |
> | Action | Microsoft. API Management/Service/Products/Groups/Read | Visar en lista med de utvecklings grupper som är associerade med den angivna produkten. |
> | Action | Microsoft. API Management/Service/Products/Groups/Write | Lägger till associationen mellan den angivna gruppen med utvecklare med den angivna produkten. |
> | Action | Microsoft. API Management/Service/Products/policies/Delete | Tar bort princip konfigurationen på produkten. |
> | Action | Microsoft. API Management/Service/Products/policies/Read | Hämta princip konfigurationen på produkt nivå. eller hämta princip konfigurationen på produkt nivå. |
> | Action | Microsoft. API Management/Service/Products/policies/Write | Skapar eller uppdaterar princip konfigurationen för produkten. |
> | Action | Microsoft. API Management/Service/Products/policy/Delete | Ta bort princip konfigurationen på produkt nivå |
> | Action | Microsoft. API Management/Service/Products/policy/Read | Hämta princip konfigurationen på produkt nivå |
> | Action | Microsoft. API Management/Service/Products/policy/Write | Skapa princip konfiguration på produkt nivå |
> | Action | Microsoft. API Management/Service/Products/Read | Listar en samling produkter i den angivna tjänst instansen. eller hämtar information om den produkt som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/Products/Subscriptions/Read | Visar en lista med prenumerationer på den angivna produkten. |
> | Action | Microsoft. API Management/Service/Products/Tags/Delete | Koppla bort taggen från produkten. |
> | Action | Microsoft. API Management/Service/Products/taggs/Read | Visar en lista över alla Taggar som är associerade med produkten. eller hämta taggen som är associerad med produkten. |
> | Action | Microsoft. API Management/Service/Products/Tags/Write | Tilldela en tagg till produkten. |
> | Action | Microsoft. API Management/Service/Products/Write | Skapar eller uppdaterar en produkt. eller uppdatera befintlig produkt information. |
> | Action | Microsoft. API Management/Service/productsByTags/Read | Listar en samling produkter som är associerade med taggar. |
> | Action | Microsoft. API Management/Service/Properties/Delete | Tar bort en angiven egenskap från API Management tjänst instansen. |
> | Action | Microsoft. API Management/Service/Properties/Read | Listar en samling egenskaper som definierats inom en tjänst instans. eller hämtar information om den egenskap som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Properties/Write | Skapar eller uppdaterar en egenskap. eller uppdaterar den angivna egenskapen. |
> | Action | Microsoft. API Management/Service/kvoter/perioder/Läs | Hämta kvot räknar värde för period |
> | Action | Microsoft. API Management/Service/kvoter/perioder/skrivning | Ange aktuellt värde för kvot räknare |
> | Action | Microsoft. API Management/Service/kvoter/läsa | Hämta värden för kvot |
> | Action | Microsoft. API Management/Service/kvoter/skrivning | Ange aktuellt värde för kvot räknare |
> | Action | Microsoft.ApiManagement/service/read | Läs metadata för en API Management tjänst instans |
> | Action | Microsoft. API Management/Service/region/läsa | Visar en lista över alla Azure-regioner där tjänsten finns. |
> | Action | Microsoft. API Management/Service/Reports/Read | Hämta rapport sammanställd per tids period eller hämta rapport som aggregerats per geografisk region eller hämta rapport aggregerade av utvecklare.<br>eller hämta rapporten sammanställt av produkter.<br>eller hämta rapport som aggregerats av API: er eller hämta rapporten sammanställt av åtgärder eller hämta rapport sammanställt av prenumerationen.<br>eller hämta rapporterings data för förfrågningar |
> | Action | Microsoft.ApiManagement/service/restore/action | Återställa API Management tjänst från den angivna behållaren i ett användardefinierat lagrings konto |
> | Action | Microsoft. API Management/Service/Subscriptions/Delete | Tar bort den angivna prenumerationen. |
> | Action | Microsoft. API Management/Service/Subscriptions/Read | Visar en lista över alla prenumerationer för API Management tjänst instansen. eller hämtar den angivna prenumerations enheten. |
> | Action | Microsoft. API Management/Service/Subscriptions/regeneratePrimaryKey/Action | Återskapar den primära nyckeln för en befintlig prenumeration på den API Management tjänst instansen. |
> | Action | Microsoft. API Management/Service/Subscriptions/regenerateSecondaryKey/Action | Återskapar sekundär nyckel för befintlig prenumeration på API Management tjänst instansen. |
> | Action | Microsoft. API Management/Service/Subscriptions/Write | Skapar eller uppdaterar prenumerationen för angiven användare till den angivna produkten. eller uppdaterar informationen om en prenumeration som anges med dess identifierare. |
> | Action | Microsoft. API Management/Service/tagResources/Read | Visar en samling resurser som är associerade med taggar. |
> | Action | Microsoft. API Management/Service/Tags/Delete | Tar bort en speciell tagg för API Management tjänst instansen. |
> | Action | Microsoft. API Management/Service/Tags/Read | Visar en uppsättning taggar som definierats inom en tjänst instans. eller hämtar information om taggen som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Tags/Write | Skapar en tagg. eller uppdaterar informationen om den tagg som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Templates/Delete | Återställ standard API Management e-postmall |
> | Action | Microsoft. API Management/Service/Templates/Read | Hämtar alla e-postmallar eller hämtar information om API Management e-postmall |
> | Action | Microsoft. API Management/Service/Templates/Write | Skapa eller uppdatera API Management e-postmall eller uppdateringar API Management e-postmall |
> | Action | Microsoft. API Management/Service/Tenant/Delete | Ta bort princip konfigurationen för klient organisationen |
> | Action | Microsoft. API Management/Service/klient/distribuera/åtgärd | Kör en distributions aktivitet för att tillämpa ändringarna från den angivna git-grenen till konfigurationen i databasen. |
> | Action | Microsoft.ApiManagement/service/tenant/operationResults/read | Hämta en lista över åtgärds resultat eller hämta resultatet för en speciell åtgärd |
> | Action | Microsoft. API Management/Service/Tenant/Read | Hämta den globala princip definitionen för API Management-tjänsten. eller hämta information om klient åtkomst information |
> | Action | Microsoft. API Management/Service/Tenant/regeneratePrimaryKey/åtgärd | Återskapa primär åtkomst nyckel |
> | Action | Microsoft. API Management/Service/Tenant/regenerateSecondaryKey/åtgärd | Återskapa sekundär åtkomst nyckel |
> | Action | Microsoft. API Management/Service/klient/spara/åtgärd | Skapar bekräftelse med konfigurations ögonblicks bild till den angivna grenen i databasen |
> | Action | Microsoft.ApiManagement/service/tenant/syncState/read | Hämta status för senaste git-synkronisering |
> | Action | Microsoft. API Management/Service/klient/verifiera/åtgärd | Verifierar ändringar från angiven git-gren |
> | Action | Microsoft.ApiManagement/service/tenant/write | Ange princip konfiguration för klient organisations-eller uppdaterings information om klient åtkomst |
> | Action | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikatet för en API Management-tjänst |
> | Action | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domän namn för en API Management-tjänst |
> | Action | Microsoft. API Management/Service/Users/Action | Registrera en ny användare |
> | Action | Microsoft. API Management/Service/Users/CONFIRMS/Send/Action | Skickar bekräftelse |
> | Action | Microsoft. API Management/Service/Users/Delete | Tar bort en speciell användare. |
> | Action | Microsoft.ApiManagement/service/users/generateSsoUrl/action | Hämtar en URL för omdirigering som innehåller en autentiseringstoken för att signera en viss användare till Developer-portalen. |
> | Action | Microsoft. API Management/Service/Users/Groups/Read | Visar en lista över alla användar grupper. |
> | Action | Microsoft. API Management/Service/Users/Identities/Read | Lista över alla användar identiteter. |
> | Action | Microsoft.ApiManagement/service/users/keys/read | Hämta nycklar kopplade till användaren |
> | Action | Microsoft. API Management/Service/Users/Read | Visar en samling registrerade användare i den angivna tjänst instansen. eller hämtar information om den användare som anges av dess identifierare. |
> | Action | Microsoft. API Management/Service/Users/Subscriptions/Read | Visar en lista med prenumerationer för den angivna användaren. |
> | Action | Microsoft. API Management/Service/Users/token/Action | Hämtar autentiseringstoken för delad åtkomst för användaren. |
> | Action | Microsoft. API Management/Service/Users/Write | Skapar eller uppdaterar en användare. eller uppdaterar informationen om den användare som anges av dess identifierare. |
> | Action | Microsoft.ApiManagement/service/write | Skapa en ny instans av API Management-tjänsten |
> | Action | Microsoft.ApiManagement/unregister/action | Avregistrera prenumerationen för Microsoft. API Management Resource Provider |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Authorization/classicAdministrators/Delete | Tar bort administratören från prenumerationen. |
> | Action | Microsoft. Authorization/classicAdministrators/operationstatuses/Read | Hämtar status för administratörsåtgärder för prenumerationen. |
> | Action | Microsoft. Authorization/classicAdministrators/Read | Läser in prenumerationens administratörer. |
> | Action | Microsoft. Authorization/classicAdministrators/Write | Lägg till eller ta bort en administratör för en prenumeration. |
> | Action | Microsoft. Authorization/denyAssignments/Delete | Ta bort ett tilldelningsnekande för det angivna reservationsomfånget. |
> | Action | Microsoft. Authorization/denyAssignments/Read | Hämta information om ett tilldelningsnekande. |
> | Action | Microsoft. Authorization/denyAssignments/Write | Skapa ett tilldelningsnekande för det definierade reservationsomfånget. |
> | Action | Microsoft. Authorization/elevateAccess/åtgärd | Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen |
> | Action | Microsoft. Authorization/lock/Delete | Ta bort lås i det specificerade omfånget. |
> | Action | Microsoft. Authorization/lock/Read | Hämtar lås i det specificerade omfånget. |
> | Action | Microsoft. Authorization/lock/Write | Lägg till lås i det specificerade omfånget. |
> | Action | Microsoft. Authorization/Operations/Read | Hämta listan över åtgärder |
> | Action | Microsoft. Authorization/Permissions/Read | Listar alla behörigheter som anroparen har i ett givet omfång. |
> | Action | Microsoft. Authorization/policyAssignments/Delete | Ta bort tilldelning av principer i det definierade området. |
> | Action | Microsoft. Authorization/policyAssignments/Read | Hämta information om en tilldelning av principer. |
> | Action | Microsoft. Authorization/policyAssignments/Write | Skapa en principtilldelning i det definierade området. |
> | Action | Microsoft. Authorization/policyDefinitions/Delete | Ta bort en definition av principen. |
> | Action | Microsoft. Authorization/policyDefinitions/Read | Hämta information om en principdefinition. |
> | Action | Microsoft. Authorization/policyDefinitions/Write | Skapa en anpassad principdefinition. |
> | Action | Microsoft. Authorization/policySetDefinitions/Delete | Ta bort en principuppsättningsdefinition. |
> | Action | Microsoft.Authorization/policySetDefinitions/read | Hämta information om en principuppsättningsdefinition. |
> | Action | Microsoft.Authorization/policySetDefinitions/write | Skapa en anpassad principuppsättningsdefinition. |
> | Action | Microsoft. Authorization/providerOperations/Read | Hämta åtgärder för alla providrar som kan användas i rolldefinitioner. |
> | Action | Microsoft. Authorization/roleAssignments/Delete | Ta bort en rolltilldelning i det specificerade omfånget. |
> | Action | Microsoft. Authorization/roleAssignments/Read | Hämta information om en rolltilldelning. |
> | Action | Microsoft.Authorization/roleAssignments/write | Skapa en rolltilldelning i det specificerade omfånget. |
> | Action | Microsoft. Authorization/roleDefinitions/Delete | Ta bort den angivna anpassade rolldefinitionen. |
> | Action | Microsoft.Authorization/roleDefinitions/read | Hämta information om en rolldefinition. |
> | Action | Microsoft.Authorization/roleDefinitions/write | Skapa eller uppdatera en anpassad rolldefinition med angivna behörigheter och tilldelningsbara scope. |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/read | Läs en Azure Automation DSCs registrerings information |
> | Action | Microsoft.Automation/automationAccounts/agentRegistrationInformation/regenerateKey/action | Skriver en begäran om att återskapa Azure Automation DSC-nycklar |
> | Action | Microsoft. Automation/automationAccounts/certificates/Delete | Tar bort en Azure Automation certifikats till gång |
> | Action | Microsoft. Automation/automationAccounts/certificates/getCount/Action | Läser antalet certifikat |
> | Action | Microsoft. Automation/automationAccounts/certifikat/läsa | Hämtar en Azure Automation certifikat till gång |
> | Action | Microsoft. Automation/automationAccounts/certificates/Write | Skapar eller uppdaterar en Azure Automation certifikat till gång |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Read | Läser en Azure Automation DSC-kompilering |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Read | Läser en Azure Automation DSC-kompilering |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Write | Skriver en Azure Automation DSC-kompilering |
> | Action | Microsoft. Automation/automationAccounts/compilationjobs/Write | Skriver en Azure Automation DSC-kompilering |
> | Action | Microsoft.Automation/automationAccounts/configurations/content/read | Läser innehållet i konfigurations mediet |
> | Action | Microsoft. Automation/automationAccounts/Configurations/Delete | Tar bort ett Azure Automation DSC-innehåll |
> | Action | Microsoft.Automation/automationAccounts/configurations/getCount/action | Läser antalet Azure Automation DSC-innehåll |
> | Action | Microsoft. Automation/automationAccounts/konfigurationer/läsa | Hämtar ett Azure Automation DSC-innehåll |
> | Action | Microsoft. Automation/automationAccounts/konfigurationer/Skriv | Skriver en Azure Automation DSC-innehåll |
> | Action | Microsoft. Automation/automationAccounts/Connections/Delete | Tar bort en Azure Automation anslutnings till gång |
> | Action | Microsoft. Automation/automationAccounts/Connections/getCount/Action | Läser antalet anslutningar |
> | Action | Microsoft. Automation/automationAccounts/Connections/Read | Hämtar en Azure Automation anslutnings till gång |
> | Action | Microsoft. Automation/automationAccounts/Connections/Write | Skapar eller uppdaterar en Azure Automation anslutnings till gång |
> | Action | Microsoft. Automation/automationAccounts/connectionTypes/Delete | Tar bort en Azure Automation Anslutnings typ till gång |
> | Action | Microsoft. Automation/automationAccounts/connectionTypes/Read | Hämtar en Azure Automation-tillgång av typen anslutning |
> | Action | Microsoft. Automation/automationAccounts/connectionTypes/Write | Skapar en Azure Automation Anslutnings typ till gång |
> | Action | Microsoft.Automation/automationAccounts/credentials/delete | Tar bort en Azure Automation Credential-till gång |
> | Action | Microsoft.Automation/automationAccounts/credentials/getCount/action | Läser antalet autentiseringsuppgifter |
> | Action | Microsoft.Automation/automationAccounts/credentials/read | Hämtar en Azure Automation till gång till autentiseringsuppgifter |
> | Action | Microsoft.Automation/automationAccounts/credentials/write | Skapar eller uppdaterar en Azure Automation Credential-till gång |
> | Action | Microsoft. Automation/automationAccounts/Delete | Tar bort ett Azure Automation konto |
> | Action | Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Delete | Tar bort Hybrid Runbook Worker resurser |
> | Action | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker resurser |
> | Action | Microsoft. Automation/automationAccounts/Jobs/utdata/Read | Hämtar utdata för ett jobb |
> | Action | Microsoft.Automation/automationAccounts/jobs/read | Hämtar ett Azure Automation jobb |
> | Action | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation jobb |
> | Action | Microsoft. Automation/automationAccounts/Jobs/runbookContent/åtgärd | Hämtar innehållet i Azure Automation Runbook vid tidpunkten för jobb körningen |
> | Action | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation jobb |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobb ström |
> | Action | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobb ström |
> | Action | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation jobb |
> | Action | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation jobb |
> | Action | Microsoft. Automation/automationAccounts/jobSchedules/Delete | Tar bort ett Azure Automation jobb schema |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation jobb schema |
> | Action | Microsoft.Automation/automationAccounts/jobSchedules/write | Skapar ett Azure Automation jobb schema |
> | Action | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar arbets ytan som är länkad till Automation-kontot |
> | Action | Microsoft. Automation/automationAccounts/Listnycklar/Action | Läser nycklarna för Automation-kontot |
> | Action | Microsoft.Automation/automationAccounts/modules/activities/read | Hämtar Azure Automation-aktiviteter |
> | Action | Microsoft.Automation/automationAccounts/modules/delete | Tar bort en Azure Automation PowerShell-modul |
> | Action | Microsoft.Automation/automationAccounts/modules/getCount/action | Hämtar antalet PowerShell-moduler i Automation-kontot |
> | Action | Microsoft.Automation/automationAccounts/modules/read | Hämtar en Azure Automation PowerShell-modul |
> | Action | Microsoft.Automation/automationAccounts/modules/write | Skapar eller uppdaterar en Azure Automation PowerShell-modul |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/delete | Tar bort en Azure Automation DSC-nods konfiguration |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/rawContent/action | Läser en Azure Automation DSC-nodens konfigurations innehåll |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/read | Läser en konfiguration av Azure Automation DSC-noden |
> | Action | Microsoft.Automation/automationAccounts/nodeConfigurations/write | Skriver en konfiguration av en Azure Automation DSC-nod |
> | Action | Microsoft. Automation/automationAccounts/nodecounts/Read | Läser Sammanfattning av antal noder för den angivna typen |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Delete | Tar bort Azure Automation DSC-noder |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Read | Läser Azure Automation DSC-noder |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Reports/Content/Read | Läser Azure Automation DSC-rapportens innehåll |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Reports/Read | Läser Azure Automation DSC-rapporter |
> | Action | Microsoft. Automation/automationAccounts/Nodes/Write | Skapar eller uppdaterar Azure Automation DSC-noder |
> | Action | Microsoft. Automation/automationAccounts/objectDataTypes/Fields/Read | Hämtar Azure Automation TypeFields |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/delete | Tar bort ett Azure Automation python 2-paket |
> | Action | Microsoft. Automation/automationAccounts/python2Packages/Read | Hämtar ett Azure Automation python 2-paket |
> | Action | Microsoft.Automation/automationAccounts/python2Packages/write | Skapar eller uppdaterar ett Azure Automation python 2-paket |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/delete | Tar bort ett Azure Automation python 3-paket |
> | Action | Microsoft. Automation/automationAccounts/python3Packages/Read | Hämtar ett Azure Automation python 3-paket |
> | Action | Microsoft.Automation/automationAccounts/python3Packages/write | Skapar eller uppdaterar ett Azure Automation python 3-paket |
> | Action | Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation konto |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/innehåll/läsa | Hämtar innehållet i en Azure Automation Runbook |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/Delete | Tar bort en Azure Automation Runbook |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/content/write | Skapar innehållet i ett utkast till en Azure Automation Runbook |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/operationResults/read | Hämtar Azure Automation åtgärds resultat för Runbook-utkast |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/Draft/Read | Hämtar ett utkast till en Azure Automation Runbook |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/Read | Hämtar ett utkast till test jobb för Runbook-Azure Automation |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/resume/action | Återupptar ett utkast till test jobb för en Azure Automation Runbook |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/Draft/testJob/stopp/åtgärd | Stoppar Azure Automation ett utkast till test jobb för Runbook-utkast |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/suspend/action | Pausar ett utkast till test jobb för en Azure Automation Runbook |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/testJob/write | Skapar Azure Automation ett utkast till test jobb för Runbook-utkast |
> | Action | Microsoft.Automation/automationAccounts/runbooks/draft/undoEdit/action | Ångra redigeringar i ett utkast till Azure Automation Runbook |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/getCount/Action | Hämtar antalet Azure Automation runbooks |
> | Action | Microsoft.Automation/automationAccounts/runbooks/publish/action | Publicerar ett utkast till en Azure Automation Runbook |
> | Action | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation Runbook |
> | Action | Microsoft. Automation/automationAccounts/Runbooks/Write | Skapar eller uppdaterar en Azure Automation Runbook |
> | Action | Microsoft.Automation/automationAccounts/schedules/delete | Tar bort en Azure Automation schema till gång |
> | Action | Microsoft.Automation/automationAccounts/schedules/getCount/action | Hämtar antalet Azure Automation scheman |
> | Action | Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation schema till gång |
> | Action | Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation schema till gång |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/delete | Tar bort en Azure Automation konfiguration för program uppdatering |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/read | Hämtar en Azure Automation konfiguration för program uppdatering |
> | Action | Microsoft.Automation/automationAccounts/softwareUpdateConfigurations/write | Skapar eller uppdaterar Azure Automation program uppdaterings konfiguration |
> | Action | Microsoft. Automation/automationAccounts/Statistics/Read | Hämtar Azure Automation statistik |
> | Action | Microsoft.Automation/automationAccounts/updateDeploymentMachineRuns/read | Hämta en Azure Automation distributions dator för uppdateringar |
> | Action | Microsoft. Automation/automationAccounts/updateManagementPatchJob/Read | Hämtar ett uppdaterings jobb för Azure Automation uppdaterings hantering |
> | Action | Microsoft. Automation/automationAccounts/Usage/Read | Hämtar Azure Automation användning |
> | Action | Microsoft.Automation/automationAccounts/variables/delete | Tar bort en Azure Automation variabel till gång |
> | Action | Microsoft. Automation/automationAccounts/variabler/läsa | Läser en Azure Automation variabel till gång |
> | Action | Microsoft. Automation/automationAccounts/variabler/Write | Skapar eller uppdaterar en Azure Automation variabel till gång |
> | Action | Microsoft. Automation/automationAccounts/Watcher/Delete | Ta bort ett Azure Automation Watcher-jobb |
> | Action | Microsoft. Automation/automationAccounts/Watcher/Read | Hämtar ett Azure Automation Watcher-jobb |
> | Action | Microsoft. Automation/automationAccounts/Watcher/start/åtgärd | Starta ett Azure Automation Watcher-jobb |
> | Action | Microsoft. Automation/automationAccounts/Watcher/stoppa/åtgärd | Stoppa ett Azure Automation Watcher-jobb |
> | Action | Microsoft. Automation/automationAccounts/Watcher/Streams/Read | Hämtar en Azure Automation övervakare jobb ström |
> | Action | Microsoft. Automation/automationAccounts/Watcher/watcherActions/Delete | Ta bort ett Azure Automation övervaknings jobb åtgärder |
> | Action | Microsoft. Automation/automationAccounts/Watcher/watcherActions/Read | Hämtar en Azure Automation övervakare jobb åtgärder |
> | Action | Microsoft. Automation/automationAccounts/Watcher/watcherActions/Write | Skapa ett Azure Automation övervaknings jobb åtgärder |
> | Action | Microsoft. Automation/automationAccounts/Watcher/Write | Skapar ett Azure Automation Watcher-jobb |
> | Action | Microsoft.Automation/automationAccounts/webhooks/action | Genererar en URI för en Azure Automation-webhook |
> | Action | Microsoft. Automation/automationAccounts/Webhooks/Delete | Tar bort en Azure Automation-webhook  |
> | Action | Microsoft. Automation/automationAccounts/Webhooks/Read | Läser en Azure Automation webhook |
> | Action | Microsoft.Automation/automationAccounts/webhooks/write | Skapar eller uppdaterar en Azure Automation webhook |
> | Action | Microsoft. Automation/automationAccounts/Write | Skapar eller uppdaterar ett Azure Automation konto |
> | Action | Microsoft. Automation/Operations/Read | Hämtar tillgängliga åtgärder för Azure Automation resurser |
> | Action | Microsoft. Automation/register/åtgärd | Registrerar prenumerationen på Azure Automation |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/delete | Ta bort B2C-katalogresursen |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/read | Visa B2C-katalogresurs |
> | Action | Microsoft.AzureActiveDirectory/b2cDirectories/write | Skapa eller uppdatera B2C Directory-resurs |
> | Action | Microsoft.AzureActiveDirectory/b2ctenants/read | Lista alla B2C-klientorganisationer där användaren är medlem |
> | Action | Microsoft.AzureActiveDirectory/operations/read | Läs alla API-åtgärder tillgängliga för resursprovidern Microsoft.AzureActiveDirectory |
> | Action | Microsoft. AzureActiveDirectory/register/åtgärd | Registrera prenumeration för resursprovidern Microsoft.AzureActiveDirectory |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.AzureStack/Operations/read | Hämtar egenskaper för en resurs leverantörs åtgärd |
> | Action | Microsoft.AzureStack/register/action | Registrerar prenumeration med Microsoft. AzureStack Resource Provider |
> | Action | Microsoft. AzureStack/Registration/customerSubscriptions/Delete | Tar bort en Azure Stack kund prenumeration |
> | Action | Microsoft. AzureStack/registreringar/customerSubscriptions/Read | Hämtar egenskaperna för en Azure Stack kund prenumeration |
> | Action | Microsoft.AzureStack/registrations/customerSubscriptions/write | Skapar eller uppdaterar en Azure Stack kund prenumeration |
> | Action | Microsoft.AzureStack/registrations/delete | Tar bort en Azure Stack registrering |
> | Action | Microsoft.AzureStack/registrations/getActivationKey/action | Hämtar den senaste Azure Stack aktiverings nyckeln |
> | Action | Microsoft.AzureStack/registrations/products/listDetails/action | Hämtar utökad information för en Azure Stack Marketplace-produkt |
> | Action | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | Action | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure Stack registrering |
> | Action | Microsoft.AzureStack/registrations/write | Skapar eller uppdaterar en Azure Stack registrering |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. batch/batchAccounts/Applications/Delete | Tar bort ett program |
> | Action | Microsoft. batch/batchAccounts/program/läsa | Visar en lista över program eller hämtar egenskaperna för ett program |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/activate/action | Aktiverar ett programpaket |
> | Action | Microsoft. batch/batchAccounts/Applications/versions/Delete | Tar bort ett programpaket |
> | Action | Microsoft. batch/batchAccounts/Applications/versions/Read | Hämtar egenskaperna för ett programpaket |
> | Action | Microsoft.Batch/batchAccounts/applications/versions/write | Skapar ett nytt programpaket eller uppdaterar ett befintligt programpaket |
> | Action | Microsoft. batch/batchAccounts/program/Skriv | Skapar ett nytt program eller uppdaterar ett befintligt program |
> | Action | Microsoft.Batch/batchAccounts/certificateOperationResults/read | Hämtar resultatet av en tids krävande certifikat åtgärd på ett batch-konto |
> | Action | Microsoft.Batch/batchAccounts/certificates/cancelDelete/action | Avbryter den misslyckade borttagningen av ett certifikat på ett batch-konto |
> | Action | Microsoft. batch/batchAccounts/certifikat/ta bort | Tar bort ett certifikat från ett batch-konto |
> | Action | Microsoft. batch/batchAccounts/certifikat/läsa | Visar en lista över certifikat på ett batch-konto eller hämtar egenskaperna för ett certifikat |
> | Action | Microsoft. batch/batchAccounts/certifikat/skriva | Skapar ett nytt certifikat för ett batch-konto eller uppdaterar ett befintligt certifikat |
> | Action | Microsoft. batch/batchAccounts/Delete | Tar bort ett batch-konto |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Delete | Tar bort ett jobb från ett batch-konto |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Read | Visar en lista med jobb på ett batch-konto eller hämtar egenskaperna för ett jobb |
> | DataAction | Microsoft. batch/batchAccounts/Jobs/Write | Skapar ett nytt jobb på ett batch-konto eller uppdaterar ett befintligt jobb |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Delete | Tar bort ett jobb schema från ett batch-konto |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Read | Visar en lista med jobb scheman på ett batch-konto eller hämtar egenskaperna för ett jobb schema |
> | DataAction | Microsoft. batch/batchAccounts/jobSchedules/Write | Skapar ett nytt jobb schema för ett batch-konto eller uppdaterar ett befintligt jobb schema |
> | Action | Microsoft. batch/batchAccounts/listnycklar/Action | Listar åtkomst nycklar för ett batch-konto |
> | Action | Microsoft.Batch/batchAccounts/operationResults/read | Hämtar resultatet från en tids krävande batch-konto-åtgärd |
> | Action | Microsoft.Batch/batchAccounts/poolOperationResults/read | Hämtar resultatet från en långvarig åtgärd för poolen i ett batch-konto |
> | Action | Microsoft. batch/batchAccounts/pooler/ta bort | Tar bort en pool från ett batch-konto |
> | Action | Microsoft. batch/batchAccounts/pooler/disableAutoscale/åtgärd | Inaktiverar automatisk skalning för en batch-adresspool |
> | Action | Microsoft. batch/batchAccounts/pooler/läsa | Listar pooler på ett batch-konto eller hämtar egenskaperna för en pool |
> | Action | Microsoft. batch/batchAccounts/pooler/stopResize/åtgärd | Stoppar en pågående storleks ändring i en batch-adresspool |
> | Action | Microsoft. batch/batchAccounts/pooler/Skriv | Skapar en ny pool på ett batch-konto eller uppdaterar en befintlig pool |
> | Action | Microsoft. batch/batchAccounts/Read | Visar en lista med batch-konton eller hämtar egenskaperna för ett batch-konto |
> | Action | Microsoft.Batch/batchAccounts/regeneratekeys/action | Återskapar åtkomst nycklar för ett batch-konto |
> | Action | Microsoft.Batch/batchAccounts/syncAutoStorageKeys/action | Synkroniserar åtkomst nycklar för det automatiska lagrings kontot som kon figurer ATS för ett batch-konto |
> | Action | Microsoft.Batch/batchAccounts/write | Skapar ett nytt batch-konto eller uppdaterar ett befintligt batch-konto |
> | Action | Microsoft.Batch/locations/accountOperationResults/read | Hämtar resultatet från en tids krävande batch-konto-åtgärd |
> | Action | Microsoft.Batch/locations/checkNameAvailability/action | Kontrollerar att konto namnet är giltigt och inte används. |
> | Action | Microsoft. batch/platser/kvoter/läsa | Hämtar batch-kvoter för den angivna prenumerationen på den angivna Azure-regionen |
> | Action | Microsoft. batch/åtgärder/Läs | Visar en lista över åtgärder som är tillgängliga på Microsoft. batch Resource Provider |
> | Action | Microsoft. batch/register/åtgärd | Registrerar prenumerationen för batch Resource providern och gör det möjligt att skapa batch-konton |
> | Action | Microsoft.Batch/unregister/action | Avregistrerar prenumerationen för batch Resource providern och förhindrar att batch-konton skapas |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. fakturering/billingAccounts/avdelningar/läsa | Lista alla avdelningar under ett fakturerings konto omfång |
> | Action | Microsoft. fakturering/billingAccounts/enrollmentAccounts/Read | Lista alla registrerings konton under ett fakturerings konto omfång |
> | Action | Microsoft. fakturering/billingAccounts/Läs | Lista alla fakturerings konton som användaren har åtkomst till |
> | Action | Microsoft. fakturering/billingPeriods/Läs | Visar en lista över tillgängliga fakturerings perioder |
> | Action | Microsoft. fakturering/avdelningar/läsa | Lista alla avdelningar som användaren har åtkomst till |
> | Action | Microsoft. fakturering/fakturor/läsa | Visar en lista över tillgängliga fakturor |
> | Action | Microsoft. fakturering/register/åtgärd | Registrerar prenumeration med Microsoft. fakturerings resurs leverantör |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.BingMaps/mapApis/Delete | Borttagningsåtgärd |
> | Action | Microsoft.BingMaps/mapApis/listSecrets/action | Lista hemligheterna |
> | Action | Microsoft.BingMaps/mapApis/listSingleSignOnToken/action | Läs auktoriseringstoken för enkel inloggning för resursen |
> | Action | Microsoft. Bingkartssökning/mapApis/Read | Läsåtgärd |
> | Action | Microsoft. Bingkartssökning/mapApis/regenerateKey/Action | Återskapa nyckeln |
> | Action | Microsoft.BingMaps/mapApis/Write | Skrivåtgärd |
> | Action | Microsoft.BingMaps/Operations/read | Beskrivning av åtgärden. |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. blockchain/blockchainMembers/Delete | Tar bort en befintlig blockchain-medlem. |
> | Action | Microsoft.Blockchain/blockchainMembers/listApiKeys/action | Hämtar eller visar befintliga blockchain-medlems API-nycklar. |
> | Action | Microsoft. blockchain/blockchainMembers/Read | Hämtar eller visar en lista över befintliga blockchain-medlemmar. |
> | DataAction | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Ansluter till en blockchain. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/delete | Tar bort en befintlig blockchain-medlems transaktions nod. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/listApiKeys/action | Hämtar eller visar befintliga blockchain för medlems transaktioner i noden. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hämtar eller visar befintliga blockchain för medlems transaktioner. |
> | Action | Microsoft.Blockchain/blockchainMembers/transactionNodes/write | Skapar eller uppdaterar en blockchain-medlems transaktions nod. |
> | Action | Microsoft.Blockchain/blockchainMembers/write | Skapar eller uppdaterar en blockchain-medlem. |
> | Action | Microsoft.Blockchain/locations/blockchainMemberOperationResults/read | Hämtar åtgärds resultatet för blockchain-medlemmar. |
> | Action | Microsoft.Blockchain/locations/checkNameAvailability/action | Kontrollerar att resurs namnet är giltigt och inte används. |
> | Action | Microsoft. blockchain/Operations/Read | Visa en lista med alla åtgärder i Microsoft blockchain Resource Provider. |
> | Action | Microsoft. blockchain/register/åtgärd | Registrerar prenumerationen för blockchain-resurs leverantören. |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. skiss/blueprintAssignments/assignmentOperations/Read | Läs eventuella skissartefakter |
> | Action | Microsoft. skiss/blueprintAssignments/Delete | Ta bort eventuella skissartefakter |
> | Action | Microsoft. skiss/blueprintAssignments/Read | Läs eventuella skissartefakter |
> | Action | Microsoft. skiss/blueprintAssignments/whoisblueprint/Action | Hämta objekt-ID för Azure Blueprints-tjänstens huvudkonto. |
> | Action | Microsoft. skiss/blueprintAssignments/Write | Skapa eller uppdatera eventuella skissartefakter |
> | Action | Microsoft. skiss/skisser/artefakter/ta bort | Ta bort eventuella skissartefakter |
> | Action | Microsoft. skiss/skisser/artefakter/läsning | Läs eventuella skissartefakter |
> | Action | Microsoft. skiss/skisser/artefakter/skrivning | Skapa eller uppdatera eventuella skissartefakter |
> | Action | Microsoft. skiss/skiss/ta bort | Ta bort eventuella skisser |
> | Action | Microsoft. skiss/skiss/läsa | Läs eventuella skisser |
> | Action | Microsoft.Blueprint/blueprints/versions/artifacts/read | Läs eventuella skissartefakter |
> | Action | Microsoft.Blueprint/blueprints/versions/delete | Ta bort eventuella skisser |
> | Action | Microsoft.Blueprint/blueprints/versions/read | Läs eventuella skisser |
> | Action | Microsoft. skiss/skisser/versioner/skriva | Skapa eller uppdatera eventuella skisser |
> | Action | Microsoft. skiss/skiss/skriva | Skapa eller uppdatera eventuella skisser |
> | Action | Microsoft. skiss/registrera/åtgärd | Registrerar Azure Blueprints-resursprovider |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. BotService/botServices/Channels/Delete | Ta bort en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Channels/Read | Läs en bot-tjänst |
> | Action | Microsoft.BotService/botServices/channels/write | Skriv en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Connections/Delete | Ta bort en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Connections/Read | Läs en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Connections/Write | Skriv en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Delete | Ta bort en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Read | Läs en bot-tjänst |
> | Action | Microsoft. BotService/botServices/Write | Skriv en bot-tjänst |
> | Action | Microsoft. BotService/locations/operationresults/Read | Läs status för en asynkron åtgärd |
> | Action | Microsoft. BotService/Operations/Read | Läs åtgärder för alla resurs typer |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. cache/checknameavailability/åtgärd | Kontrollerar om ett namn kan användas med nytt Redis Cache |
> | Action | Microsoft. cache/locations/operationresults/Read | Hämtar resultaten för en långvarig åtgärd för vilken sidhuvudet Location tidigare returnerats till klienten |
> | Action | Microsoft. cache/åtgärder/Läs | Listar åtgärderna som stöds av providern Microsoft.Cache. |
> | Action | Microsoft. cache/Redis/Delete | Ta bort hela Redis-cache |
> | Action | Microsoft.Cache/redis/export/action | Exportera Redis-data till prefixade lagringsblobbar i angivet format |
> | Action | Microsoft. cache/Redis/firewallRules/Delete | Ta bort IP-brandväggsregler för Redis Cache |
> | Action | Microsoft. cache/Redis/firewallRules/Read | Hämta IP-brandväggsregler för Redis Cache |
> | Action | Microsoft. cache/Redis/firewallRules/Write | Redigera IP-brandväggsregler för Redis Cache |
> | Action | Microsoft. cache/Redis/forceReboot/åtgärd | Tvinga omstart av en cache-instans, vilket kan medföra dataförlust. |
> | Action | Microsoft. cache/Redis/import/åtgärd | Importera data av ett angivet format från flera blobbar till Redis |
> | Action | Microsoft. cache/Redis/linkedservers/Delete | Ta bort länkad server från Redis Cache |
> | Action | Microsoft. cache/Redis/linkedservers/Read | Hämta länkade servrar som är kopplade till Redis Cache. |
> | Action | Microsoft. cache/Redis/linkedservers/Write | Lägg till länkad server till Redis Cache |
> | Action | Microsoft. cache/Redis/Listnycklar/åtgärd | Visa värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen |
> | Action | Microsoft.Cache/redis/listUpgradeNotifications/read | Lista de senaste uppgraderingsmeddelandena för cacheklienten. |
> | Action | Microsoft. cache/Redis/metricDefinitions/Read | Hämtar tillgängliga mått för ett Redis-cache |
> | Action | Microsoft. cache/Redis/patchSchedules/Delete | Ta bort uppdateringsschemat för Redis Cache |
> | Action | Microsoft. cache/Redis/patchSchedules/Read | Hämtar uppdateringsschemat för Redis Cache |
> | Action | Microsoft. cache/Redis/patchSchedules/Write | Ändra uppdateringsschemat för Redis Cache |
> | Action | Microsoft. cache/Redis/Read | Visa inställningarna och konfigurationen för Redis-cache i hanteringsportalen |
> | Action | Microsoft.Cache/redis/regenerateKey/action | Ändra värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen |
> | Action | Microsoft. cache/Redis/start/Action | Starta en cacheinstans. |
> | Action | Microsoft. cache/Redis/stoppa/åtgärd | Stoppa en cacheinstans. |
> | Action | Microsoft.Cache/redis/write | Ändra inställningarna och konfigurationen för Redis-cache i hanteringsportalen |
> | Action | Microsoft. cache/register/åtgärd | Registrerar Microsoft.Cache-resursprovidern med en prenumeration |
> | Action | Microsoft.Cache/unregister/action | Avregistrerar Microsoft.Cache-resursprovidern med en prenumeration |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Capacity/appliedreservations/Read | Läs alla reservationer |
> | Action | Microsoft.Capacity/calculateexchange/action | Beräknar Exchange-beloppet och priset för nya inköp och returnerar princip fel. |
> | Action | Microsoft.Capacity/calculateprice/action | Beräkna eventuella reservations priser |
> | Action | Microsoft. Capacity/Catalog/Read | Läs reservations katalog |
> | Action | Microsoft. Capacity/checkoffers/åtgärd | Kontrol lera eventuella prenumerations erbjudanden |
> | Action | Microsoft. Capacity/checkscopes/åtgärd | Kontrol lera vilken prenumeration som helst |
> | Action | Microsoft. Capacity/commercialreservationorders/Read | Hämta reservations order som skapats i en klient |
> | Action | Microsoft. Capacity/Exchange/Action | Exchange-reservation |
> | Action | Microsoft. Capacity/Operations/Read | Läs valfri åtgärd |
> | Action | Microsoft. Capacity/register/åtgärd | Registrerar kapacitets resurs leverantören och gör det möjligt att skapa kapacitets resurser. |
> | Action | Microsoft. Capacity/reservationorders/åtgärd | Uppdatera eventuella reservationer |
> | Action | Microsoft. Capacity/reservationorders/availablescopes/Action | Hitta tillgängliga omfång |
> | Action | Microsoft. Capacity/reservationorders/calculaterefund/Action | Beräknar bidrags beloppet och priset för nya inköp och returnerar princip fel. |
> | Action | Microsoft. Capacity/reservationorders/Delete | Ta bort alla reservationer |
> | Action | Microsoft. Capacity/reservationorders/merge/Action | Koppla valfri reservation |
> | Action | Microsoft. Capacity/reservationorders/Read | Läs alla reservationer |
> | Action | Microsoft. Capacity/reservationorders/reservation/åtgärd | Uppdatera eventuella reservationer |
> | Action | Microsoft. Capacity/reservationorders/reservations/Delete | Ta bort alla reservationer |
> | Action | Microsoft. Capacity/reservationorders/reservations/Read | Läs alla reservationer |
> | Action | Microsoft. Capacity/reservationorders/reservationer/revisioner/Read | Läs alla reservationer |
> | Action | Microsoft. Capacity/reservationorders/reservations/Write | Skapa valfri reservation |
> | Action | Microsoft. Capacity/reservationorders/retur/åtgärd | Returnera eventuella reservationer |
> | Action | Microsoft. Capacity/reservationorders/Split/Action | Dela alla reservationer |
> | Action | Microsoft. Capacity/reservationorders/växling/åtgärd | Byt ut eventuell reservation |
> | Action | Microsoft. Capacity/reservationorders/Write | Skapa valfri reservation |
> | Action | Microsoft. Capacity/Tenants/register/åtgärd | Registrera en klient |
> | Action | Microsoft. Capacity/avregistrera/åtgärd | Avregistrera en klient |
> | Action | Microsoft. Capacity/validatereservationorder/åtgärd | Verifiera eventuella reservationer |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. CDN/CheckNameAvailability/åtgärd |  |
> | Action | Microsoft. CDN/CheckResourceUsage/åtgärd |  |
> | Action | Microsoft. CDN/edgenodes/Delete |  |
> | Action | Microsoft.Cdn/edgenodes/read |  |
> | Action | Microsoft. CDN/edgenodes/Write |  |
> | Action | Microsoft. CDN/operationresults/Delete |  |
> | Action | Microsoft. CDN/operationresults/profileresults/CheckResourceUsage/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/delete |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/CheckResourceUsage/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/customdomainresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/delete |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/load/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/delete |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/originresults/read |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/originresults/Write |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/rensning/åtgärd |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/read |  |
> | Action | Microsoft. CDN/operationresults/profileresults/endpointresults/start/Action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/Stop/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/ValidateCustomDomain/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/endpointresults/write |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GenerateSsoUri/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/GetSupportedOptimizationTypes/action |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/read |  |
> | Action | Microsoft.Cdn/operationresults/profileresults/write |  |
> | Action | Microsoft. CDN/operationresults/Read |  |
> | Action | Microsoft. CDN/operationresults/Write |  |
> | Action | Microsoft. CDN/Operations/Read |  |
> | Action | Microsoft. CDN/profiler/CheckResourceUsage/åtgärd |  |
> | Action | Microsoft.Cdn/profiles/delete |  |
> | Action | Microsoft. CDN/profiler/slut punkter/CheckResourceUsage/åtgärd |  |
> | Action | Microsoft. CDN/profiler/slut punkter/customdomains/Delete |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/DisableCustomHttps/action |  |
> | Action | Microsoft.Cdn/profiles/endpoints/customdomains/EnableCustomHttps/action |  |
> | Action | Microsoft. CDN/profiler/slut punkter/customdomains/Read |  |
> | Action | Microsoft. CDN/profiler/slut punkter/customdomains/Write |  |
> | Action | Microsoft. CDN/profiler/slut punkter/ta bort |  |
> | Action | Microsoft. CDN/profiler/slut punkter/belastning/åtgärd |  |
> | Action | Microsoft. CDN/profiler/slut punkter/ursprung/ta bort |  |
> | Action | Microsoft. CDN/profiler/slut punkter/ursprung/läsa |  |
> | Action | Microsoft. CDN/profiler/slut punkter/ursprung/Skriv |  |
> | Action | Microsoft. CDN/profiler/slut punkter/rensning/åtgärd |  |
> | Action | Microsoft. CDN/profiler/slut punkter/läsning |  |
> | Action | Microsoft. CDN/profiler/slut punkter/start/åtgärd |  |
> | Action | Microsoft. CDN/profiler/slut punkter/stoppa/åtgärd |  |
> | Action | Microsoft. CDN/profiler/slut punkter/ValidateCustomDomain/åtgärd |  |
> | Action | Microsoft. CDN/profiler/slut punkter/skrivning |  |
> | Action | Microsoft. CDN/profiler/GenerateSsoUri/åtgärd |  |
> | Action | Microsoft. CDN/profiler/GetSupportedOptimizationTypes/åtgärd |  |
> | Action | Microsoft. CDN/profiler/läsa |  |
> | Action | Microsoft. CDN/profiler/Write |  |
> | Action | Microsoft. CDN/register/åtgärd | Registrerar prenumerationen för CDN-resursprovidern och aktiverar funktionen för att skapa CDN-profiler. |
> | Action | Microsoft.Cdn/ValidateProbe/action |  |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. CertificateRegistration/certificateOrders/certificates/Delete | Ta bort ett befintligt certifikat |
> | Action | Microsoft. CertificateRegistration/certificateOrders/certificates/Read | Hämta listan över certifikat |
> | Action | Microsoft. CertificateRegistration/certificateOrders/certificates/Write | Lägg till ett nytt certifikat eller uppdatera ett befintligt |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Delete | Ta bort en befintlig AppServiceCertificate |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Read | Hämta listan över CertificateOrders |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Reissue/åtgärd | Utfärda en befintlig certificateorder igen |
> | Action | Microsoft. CertificateRegistration/certificateOrders/renew/åtgärd | Förnya en befintlig certificateorder |
> | Action | Microsoft. CertificateRegistration/certificateOrders/resendEmail/Action | Skicka e-postmeddelande om certifikat |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Skicka e-postmeddelanden för begäran till en annan e-postadress |
> | Action | Microsoft.CertificateRegistration/certificateOrders/resendRequestEmails/Action | Hämta platsens sigill för en utfärdad App Service Certificate |
> | Action | Microsoft. CertificateRegistration/certificateOrders/retrieveCertificateActions/Action | Hämta listan över certifikat åtgärder |
> | Action | Microsoft. CertificateRegistration/certificateOrders/retrieveEmailHistory/Action | Hämta e-posthistorik för certifikat |
> | Action | Microsoft.CertificateRegistration/certificateOrders/verifyDomainOwnership/Action | Verifiera domänägarskap |
> | Action | Microsoft. CertificateRegistration/certificateOrders/Write | Lägg till en ny certificateOrder eller uppdatera en befintlig |
> | Action | Microsoft. CertificateRegistration/Operations/Read | Visa en lista med alla åtgärder från App Service Certificate Registration |
> | Action | Microsoft.CertificateRegistration/provisionGlobalAppServicePrincipalInUserTenant/Action | Etablera tjänstens huvud namn för tjänstens huvud namn |
> | Action | Microsoft. CertificateRegistration/register/åtgärd | Registrera resurs leverantören för Microsoft-certifikaten för prenumerationen |
> | Action | Microsoft.CertificateRegistration/validateCertificateRegistrationInformation/Action | Verifiera ett certifikat inköps objekt utan att skicka det |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ClassicCompute/Capabilities/Read | Visar funktionerna |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/action | Kontrollerar tillgängligheten för ett visst domännamn. |
> | Action | Microsoft.ClassicCompute/checkDomainNameAvailability/read | Hämtar tillgängligheten för ett visst domännamn. |
> | Action | Microsoft. ClassicCompute/domän namn/aktiv/skrivning | Anger det aktiva domännamnet. |
> | Action | Microsoft. ClassicCompute/domän namn/availabilitySets/Read | Visa tillgänglighetsuppsättningen för resursen. |
> | Action | Microsoft. ClassicCompute/domän namn/funktioner/läsa | Visar domännamnsfunktionerna |
> | Action | Microsoft. ClassicCompute/domän namn/ta bort | Ta bort resursernas domännamn. |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/Read | Visar distributionsplatserna. |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/roller/läsa | Hämta roll i domännamnets distributionsfack |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/roles/roleinstances/Read | Hämta rollinstans för roll i domännamnets distributionsfack |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/tillstånd/läsa | Hämta status för distributionsfacket. |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/tillstånd/skrivning | Lägg till status för distributionsfacket. |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/upgradedomain/Read | Hämta uppgraderingsdomän för distributionsfack i domännamn |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/upgradedomain/Write | Uppdatera uppgraderingsdomän för distributionsfack i domännamn |
> | Action | Microsoft. ClassicCompute/domän namn/deploymentslots/Write | Skapar eller uppdaterar distributionen. |
> | Action | Microsoft. ClassicCompute/domän namn/tillägg/ta bort | Ta bort domännamnstilläggen. |
> | Action | Microsoft. ClassicCompute/domän namn/tillägg/operationStatuses/läsa | Läser domännamnstilläggens åtgärdsstatus. |
> | Action | Microsoft. ClassicCompute/domän namn/tillägg/läsa | Returnerar domännamnstilläggen. |
> | Action | Microsoft. ClassicCompute/domän namn/tillägg/Skriv | Lägg till domännamnstilläggen. |
> | Action | Microsoft. ClassicCompute/domän namn/internalLoadBalancers/Delete | Ta bort en ny intern belastningsutjämning. |
> | Action | Microsoft.ClassicCompute/domainNames/internalLoadBalancers/operationStatuses/read | Läser åtgärdsstatusen för domännamnens interna lastbalanserare. |
> | Action | Microsoft. ClassicCompute/domän namn/internalLoadBalancers/Read | Hämtar de interna lastbalanserarna. |
> | Action | Microsoft. ClassicCompute/domän namn/internalLoadBalancers/Write | Skapa en ny intern belastningsutjämning. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/operationStatuses/read | Läser åtgärdsstatusen för belastningsutjämnade slutpunktsuppsättningar för domännamn. |
> | Action | Microsoft. ClassicCompute/domän namn/loadBalancedEndpointSets/Read | Hämta belastningsutjämnade slutpunktsuppsättningar. |
> | Action | Microsoft.ClassicCompute/domainNames/loadBalancedEndpointSets/write | Lägg till belastningsutjämnad slutpunktsuppsättning. |
> | Action | Microsoft. ClassicCompute/domän namn/operationstatuses/Read | Hämta åtgärdsstatus för domännamnet. |
> | Action | Microsoft. ClassicCompute/domän namn/operationStatuses/Read | Läser domännamnstilläggens åtgärdsstatus. |
> | Action | Microsoft. ClassicCompute/domän namn/läsa | Returnera resursernas domännamn. |
> | Action | Microsoft. ClassicCompute/domän namn/serviceCertificates/Delete | Ta bort de tjänstcertifikat som används. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/operationStatuses/read | Läser åtgärdsstatus för tjänstcertifikat för domännamn. |
> | Action | Microsoft.ClassicCompute/domainNames/serviceCertificates/read | Returnerar de tjänstcertifikat som används. |
> | Action | Microsoft. ClassicCompute/domän namn/serviceCertificates/Write | Lägg till eller ändra de tjänstcertifikat som används. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/abortMigration/åtgärd | Avbryter migrering av ett distributionsfack. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/commitMigration/åtgärd | Utför migrering av ett distributionsfack. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/ta bort | Tar bort en given distributionsplats. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/operationStatuses/läsa | Läser domännamnsplatsernas åtgärdsstatus. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/prepareMigration/action | Förbereder migrering av ett distributionsfack. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/läsa | Visar distributionsplatserna. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/delete | Ta bort tilläggsreferensen för distributionsplatsrollen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/operationStatuses/read | Läser åtgärdsstatus för rolltilläggsreferenser för domännamnsplatser. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/read | Returnerar tilläggsreferensen för distributionsplatsrollen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/extensionReferences/write | Lägg till eller ändra tilläggsreferensen för distributionsplatsrollen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/metricdefinitions/read | Hämta rollmåttsdefinitionen för domännamnet. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/roller/mått/läsa | Hämta rollmått för domännamnet. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/roller/operationstatuses/läsa | Hämta åtgärdsstatus för domännamnsfackrollen. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/roller/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningarna. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/roller/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningarna. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/roller/läsa | Hämta distributionsplatsens roll. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/downloadremotedesktopconnectionfile/action | Laddar ned fjärrskrivbordsanslutningsfil för rollinstansen på domännamnsfackrollen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/operationStatuses/read | Hämtar åtgärdsstatus för rollinstansen för domännamnsfackroll. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/read | Hämta rollinstansen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/rebuild/action | Återskapar rollinstansen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/reimage/action | Återställer avbildningen för rollinstansen. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/roleInstances/restart/action | Uppgradera domän |
> | Action | Microsoft.ClassicCompute/domainNames/slots/roles/skus/read | Hämta roll-SKU för distributionsfacket. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/roller/Skriv | Lägg till roll för distributionsfacket. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/start/åtgärd | Startar en distributionsplats. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/tillstånd/starta/skriva | Ändrar distributionsplatsens status till stoppad. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/state/stop/write | Ändrar distributionsplatsens status till startad. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/stoppa/åtgärd | Göra uppehåll för distributionsplatserna. |
> | Action | Microsoft.ClassicCompute/domainNames/slots/upgradeDomain/write | Gå igenom uppgraderingsdomän. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/validateMigration/åtgärd | Verifierar migrering av ett distributionsfack. |
> | Action | Microsoft. ClassicCompute/domän namn/platser/Skriv | Skapar eller uppdaterar distributionen. |
> | Action | Microsoft. ClassicCompute/domän namn/växling/åtgärd | Byta mellanlagringsplatsen till produktionsplatsen. |
> | Action | Microsoft. ClassicCompute/domän namn/skrivning | Lägg till eller ändra resursernas domännamn. |
> | Action | Microsoft. ClassicCompute/moveSubscriptionResources/Action | Flytta alla klassiska resurser till en annan prenumeration. |
> | Action | Microsoft.ClassicCompute/operatingSystemFamilies/read | Listar de gästoperativsystem som är tillgängliga i Microsoft Azure, och listar även de operativsystemsversioner som är tillgängliga för respektive familj. |
> | Action | Microsoft.ClassicCompute/operatingSystems/read | Listar de versioner av gästoperativsystemen som för tillfället är tillgängliga i Microsoft Azure. |
> | Action | Microsoft. ClassicCompute/Operations/Read | Hämtar listan över åtgärder. |
> | Action | Microsoft. ClassicCompute/operationStatuses/Read | Läser resursens åtgärdsstatus. |
> | Action | Microsoft. ClassicCompute/kvoter/läsa | Hämta prenumerationens kvot. |
> | Action | Microsoft. ClassicCompute/register/åtgärd | Registrera för klassisk beräkning |
> | Action | Microsoft. ClassicCompute/resourceTypes/SKU/Read | Hämtar SKU-listan för de resurstyper som stöds. |
> | Action | Microsoft. ClassicCompute/validateSubscriptionMoveAvailability/Action | Verifiera prenumerationens tillgänglighet för klassisk flyttåtgärd. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn. |
> | Action | Microsoft.ClassicCompute/virtualMachines/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till den virtuella datorn. |
> | Action | Microsoft. ClassicCompute/virtualMachines/asyncOperations/Read | Hämta möjliga asynkrona åtgärder |
> | Action | Microsoft.ClassicCompute/virtualMachines/attachDisk/action | Bifogar en datadisk till den virtuella datorn. |
> | Action | Microsoft.ClassicCompute/virtualMachines/capture/action | Avbilda en virtuell dator. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Delete | Tar bort virtuella datorer. |
> | Action | Microsoft.ClassicCompute/virtualMachines/detachDisk/action | Tar bort en datadisk från den virtuella datorn. |
> | Action | Microsoft.ClassicCompute/virtualMachines/diagnosticsettings/read | Hämta diagnostikinställningar för virtuell dator. |
> | Action | Microsoft.ClassicCompute/virtualMachines/disks/read | Hämtar lista över data diskar |
> | Action | Microsoft. ClassicCompute/virtualMachines/downloadRemoteDesktopConnectionFile/Action | Hämtar RDP-filen för den virtuella datorn. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/operationStatuses/read | Läser åtgärdsstatus för tillägg för virtuella datorer. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/read | Hämtar tillägget för virtuell dator. |
> | Action | Microsoft.ClassicCompute/virtualMachines/extensions/write | Placerar tillägget för virtuell dator. |
> | Action | Microsoft.ClassicCompute/virtualMachines/metricdefinitions/read | Hämta måttdefinitionen för virtuell dator. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Metrics/Read | Hämtar måtten. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet. |
> | Action | Microsoft.ClassicCompute/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till nätverksgränssnittet. |
> | Action | Microsoft. ClassicCompute/virtualMachines/operationStatuses/Read | Läser åtgärdsstatus för virtuella datorer. |
> | Action | Microsoft. ClassicCompute/virtualMachines/performMaintenance/Action | Utför underhåll av den virtuella datorn. |
> | Action | Microsoft. ClassicCompute/virtualMachines/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningarna. |
> | Action | Microsoft. ClassicCompute/virtualMachines/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningarna. |
> | Action | Microsoft.ClassicCompute/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Action | Microsoft. ClassicCompute/virtualMachines/Read | Hämtar en lista över virtuella datorer. |
> | Action | Microsoft. ClassicCompute/virtualMachines/redistribution/åtgärd | Distribuerar om den virtuella datorn. |
> | Action | Microsoft. ClassicCompute/virtualMachines/restart/Action | Startar om virtuella datorer. |
> | Action | Microsoft.ClassicCompute/virtualMachines/shutdown/action | Stänga av den virtuella datorn. |
> | Action | Microsoft. ClassicCompute/virtualMachines/start/Action | Startar den virtuella datorn. |
> | Action | Microsoft. ClassicCompute/virtualMachines/stopp/åtgärd | Stoppar den virtuella datorn. |
> | Action | Microsoft.ClassicCompute/virtualMachines/write | Lägg till eller ändra virtuella datorer. |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/operationstatuses/read | Hämta åtgärdsstatus för ExpressRoute-korsanslutning. |
> | Action | Microsoft. ClassicNetwork/expressroutecrossconnections/peering/Delete | Ta bort peering med ExpressRoute-korsanslutning. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/operationstatuses/read | Hämta åtgärdsstatus för en peering med ExpressRoute-korsanslutning. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/read | Hämta peering med ExpressRoute-korsanslutning. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/peerings/write | Lägg till peering med ExpressRoute-korsanslutning. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/read | Hämta ExpressRoute-korsanslutningar. |
> | Action | Microsoft.ClassicNetwork/expressroutecrossconnections/write | Lägg till ExpressRoute-korsanslutningar. |
> | Action | Microsoft. ClassicNetwork/gatewaySupportedDevices/Read | Hämtar listan över enheter som stöds. |
> | Action | Microsoft. ClassicNetwork/networkSecurityGroups/Delete | Tar bort nätverkssäkerhetsgruppen. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/operationStatuses/read | Läser åtgärdsstatus för nätverkssäkerhetsgrupp. |
> | Action | Microsoft. ClassicNetwork/networksecuritygroups/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar diagnostikinställningarna för nätverkssäkerhetsgrupper |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar diagnostikinställningar för nätverks säkerhets grupper. den här åtgärden kompletteras av Resource Provider för insikter. |
> | Action | Microsoft.ClassicNetwork/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read | Hämtar händelserna för nätverkssäkerhetsgrupp |
> | Action | Microsoft. ClassicNetwork/networkSecurityGroups/Read | Hämtar nätverkssäkerhetsgruppen. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/delete | Tar bort säkerhetsregeln. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/operationStatuses/read | Läser åtgärdsstatus för säkerhetsroller för nätverkssäkerhetsgrupp. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/read | Hämtar säkerhetsregeln. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/securityRules/write | Lägger till eller uppdaterar en säkerhetsregel. |
> | Action | Microsoft.ClassicNetwork/networkSecurityGroups/write | Lägger till en ny nätverkssäkerhetsgrupp. |
> | Action | Microsoft. ClassicNetwork/Operations/Read | Hämta åtgärder för klassiskt nätverk. |
> | Action | Microsoft. ClassicNetwork/kvoter/läsa | Hämta prenumerationens kvot. |
> | Action | Microsoft. ClassicNetwork/register/åtgärd | Registrera för klassiskt nätverk |
> | Action | Microsoft. ClassicNetwork/reservedIps/Delete | Ta bort en reserverad IP. |
> | Action | Microsoft. ClassicNetwork/reservedIps/JOIN/åtgärd | Anslut till en reserverad IP |
> | Action | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Action | Microsoft.ClassicNetwork/reservedIps/operationStatuses/read | Läser åtgärdsstatus för reserverade IP:ar. |
> | Action | Microsoft.ClassicNetwork/reservedIps/read | Hämtar reserverade IP-adresser |
> | Action | Microsoft.ClassicNetwork/reservedIps/write | Lägga till en ny reserverad IP |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/abortMigration/Action | Avbryter migreringen av ett virtuellt nätverk |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Capabilities/Read | Visar funktionerna |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/checkIPAddressAvailability/Action | Kontrollerar tillgängligheten för en given IP-adress i ett virtuellt nätverk. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/commitMigration/Action | Genomför migreringen av ett virtuellt nätverk |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Delete | Tar bort det virtuella nätverket. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/delete | Upphäver återkallningen av ett klientcertifikat. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/read | Läs de återkallade klientcertifikaten. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRevokedCertificates/write | Återkallar ett klientcertifikat. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/delete | Tar bort klientcertifikatet för virtuell nätverksgateway. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/hämtning/åtgärd | Hämtar certifikat efter tumavtryck. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/listPackage/action | Listar gatewaycertifikatspaketet för virtuella nätverk. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/read | Sök efter klientrotscertifikaten. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates/write | Laddar upp ett nytt klientrotscertifikat. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/Connections/Connect/åtgärd | Ansluter en plats-till-plats-gatewayanslutning. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/anslutningar/från koppling/åtgärd | Koppla från en plats-till-plats-gatewayanslutning. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/anslutningar/läsa | Hämtar listan över anslutningar. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/anslutningar/test/åtgärd | Testar en plats-till-plats-gatewayanslutning. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/Delete | Tar bort den virtuella nätverks-gatewayen. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDeviceConfigurationScript/åtgärd | Hämtar konfigurationsskriptet för enhet. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/downloadDiagnostics/åtgärd | Hämtar gateway-diagnostiken. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/listCircuitServiceKey/åtgärd | Hämtar kretstjänstnyckeln. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/listPackage/action | Listar gateway-paketet för virtuella nätverk. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/operationStatuses/read | Läser åtgärdsstatus för virtuella nätverksgatewayar. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/paket/läsa | Hämtar gateway-paketet för virtuella nätverk. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Read | Hämtar de virtuella nätverks-gatewayerna. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/gateways/startDiagnostics/action | Startar diagnostik för den virtuella Nätverksgatewayen. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/gateways/stopDiagnostics/åtgärd | Stoppar diagnostiken för den virtuella Nätverksgatewayen. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/Gateway/Write | Lägger till en virtuell nätverks-gateway. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/JOIN/åtgärd | Anslut till det virtuella nätverket. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/operationStatuses/read | Läser åtgärdsstatus för virtuella nätverk. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/peer/Action | Peerar ett virtuellt nätverk med ett annat virtuellt nätverk. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/prepareMigration/action | Förbereder migreringen av ett virtuellt nätverk |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/read | Hämtar det virtuella nätverket. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/Delete | Tar bort peering-proxyservern för det virtuella fjärrnätverket. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/read | Hämtar peering-proxyservern för det virtuella fjärrnätverket. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/remoteVirtualNetworkPeeringProxies/write | Lägger till eller uppdaterar peering-proxyservern för det virtuella fjärrnätverket. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete | Tar bort nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read | Läser åtgärds status för det virtuella nätverkets undernät associerade nätverks säkerhets gruppen. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/read | Hämtar nätverkssäkerhetsgruppen som är kopplad till undernätet. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/subnets/associatedNetworkSecurityGroups/write | Lägger till en nätverkssäkerhetsgrupp som är kopplad till undernätet. |
> | Action | Microsoft. ClassicNetwork/virtualNetworks/validateMigration/Action | Verifierar migreringen av ett virtuellt nätverk |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/virtualNetworkPeerings/read | Hämtar peering för det virtuella nätverket. |
> | Action | Microsoft.ClassicNetwork/virtualNetworks/write | Lägg till ett nytt virtuellt nätverk. |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ClassicStorage/Capabilities/Read | Visar funktionerna |
> | Action | Microsoft. ClassicStorage/checkStorageAccountAvailability/Action | Kontrollerar tillgängligheten för ett lagringskonto. |
> | Action | Microsoft. ClassicStorage/checkStorageAccountAvailability/Read | Hämta tillgänglighet för ett lagringskonto. |
> | Action | Microsoft. ClassicStorage/disks/Read | Returnerar lagringskontodisken. |
> | Action | Microsoft.ClassicStorage/images/operationstatuses/read | Hämtar åtgärdsstatus för avbildning. |
> | Action | Microsoft.ClassicStorage/images/read | Returnerar bilden. |
> | Action | Microsoft. ClassicStorage/Operations/Read | Hämtar åtgärder för klassisk lagring |
> | Action | Microsoft.ClassicStorage/osImages/read | Returnerar operativsystemsavbildningen. |
> | Action | Microsoft.ClassicStorage/osPlatformImages/read | Hämtar avbildningen av operativsystemets plattform. |
> | Action | Microsoft. ClassicStorage/publicImages/Read | Hämtar den offentliga avbildningen av den virtuella datorn. |
> | Action | Microsoft. ClassicStorage/kvoter/läsa | Hämta prenumerationens kvot. |
> | Action | Microsoft. ClassicStorage/register/åtgärd | Registrera för klassisk lagring |
> | Action | Microsoft. ClassicStorage/storageAccounts/abortMigration/Action | Avbryter migreringen av ett lagringskonto. |
> | Action | Microsoft. ClassicStorage/storageAccounts/commitMigration/Action | Utför migrering av ett lagringskonto. |
> | Action | Microsoft. ClassicStorage/storageAccounts/Delete | Ta bort lagringskontot. |
> | Action | Microsoft. ClassicStorage/storageAccounts/disks/Delete | Tar bort angiven lagringskontodisk. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagringskontodisken. |
> | Action | Microsoft.ClassicStorage/storageAccounts/disks/write | Lägger till en lagringskontodisk. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/delete | Tar bort en specifik lagrings konto avbildning. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/operationstatuses/read | Returnerar åtgärdsstatus för lagringskontoavbildning. |
> | Action | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagrings konto avbildningen. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Action | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Listar lagringskontots åtkomstnycklar. |
> | Action | Microsoft.ClassicStorage/storageAccounts/operationStatuses/read | Läser resursens åtgärdsstatus. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/delete | Tar bort en given operativsystemsavbildning för lagringskonto. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/read | Returnerar operativsystemsavbildningen för lagringskonto. |
> | Action | Microsoft.ClassicStorage/storageAccounts/osImages/write | Lägger till en angiven avbildning av lagringskontots operativsystem. |
> | Action | Microsoft.ClassicStorage/storageAccounts/prepareMigration/action | Förbereder migrering av ett lagringskonto. |
> | Action | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta diagnostikinställningarna. |
> | Action | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningarna. |
> | Action | Microsoft. ClassicStorage/storageAccounts/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar måttdefinitioner. |
> | Action | Microsoft.ClassicStorage/storageAccounts/read | Returnerar lagringskontot med det givna kontot. |
> | Action | Microsoft. ClassicStorage/storageAccounts/regenerateKey/Action | Återskapar befintliga åtkomstnycklar för lagringskontot. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/read | Hämta diagnostikinställningarna. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/diagnosticSettings/write | Lägg till eller ändra diagnostikinställningarna. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metricDefinitions/read | Hämtar måttdefinitioner. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/metrics/read | Hämtar måtten. |
> | Action | Microsoft.ClassicStorage/storageAccounts/services/read | Hämta de tjänster som är tillgängliga. |
> | Action | Microsoft. ClassicStorage/storageAccounts/validateMigration/Action | Verifierar migreringen av ett lagringskonto. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/delete | Tar bort en angiven avbildning av virtuell dator. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/operationstatuses/read | Hämtar åtgärdsstatus för en viss virtuell datoravbildning. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/read | Returnerar avbildningen av den virtuella datorn. |
> | Action | Microsoft.ClassicStorage/storageAccounts/vmImages/write | Lägger till en angiven avbildning av virtuell dator. |
> | Action | Microsoft.ClassicStorage/storageAccounts/write | Lägger till ett nytt lagringskonto. |
> | Action | Microsoft.ClassicStorage/vmImages/read | Listar avbildningar av virtuella datorer. |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
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
> | Action | Microsoft. CognitiveServices/Accounts/Delete | Tar bort API-konton |
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
> | Action | Microsoft. CognitiveServices/Accounts/Listnycklar/Action | Visa nyckellista |
> | DataAction | Microsoft. CognitiveServices/Accounts/LUIS/predict/Action | Hämtar den publicerade slut punkts förutsägelsen för den givna frågan. |
> | Action | Microsoft. CognitiveServices/konton/läsa | Läser API-konton. |
> | Action | Microsoft. CognitiveServices/Accounts/regenerateKey/Action | Återskapar nyckel |
> | Action | Microsoft.CognitiveServices/accounts/skus/read | Läser tillgängliga SKU:er för en befintlig resurs. |
> | DataAction | Microsoft. CognitiveServices/konton/TextAnalytics/entiteter/åtgärd | API: t returnerar en lista över kända entiteter och allmänna namngivna\"entiteter \"(\"person \"\",\" plats, organisation osv.) i ett visst dokument. |
> | DataAction | Microsoft. CognitiveServices/Accounts/TextAnalytics/-fras/åtgärd | API:t returnerar en lista med strängar som anger samtalsämnen i den inmatade texten. |
> | DataAction | Microsoft. CognitiveServices/konton/TextAnalytics/språk/åtgärd | API:t returnerar det identifierade språket och en poäng mellan 0 och 1. 1 poäng visar att korrekt språk har identifierats med 100 procents säkerhet. 120 språk stöds totalt. |
> | DataAction | Microsoft. CognitiveServices/Accounts/TextAnalytics/sentiment/Action | API:t returnerar en poäng mellan 0 och 1.<br>Poängen nära 1 visar positiv sentiment, medan poängen nära 0 visar negativa sentiment.<br>Poängen på 0,5 anger att sentiment saknas (t. ex.<br>ett factoid-uttryck). |
> | Action | Microsoft. CognitiveServices/konton/användning/Läs | Hämta kvotanvändning för en befintlig resurs. |
> | Action | Microsoft. CognitiveServices/konton/Skriv | Skriver API-konton. |
> | Action | Microsoft.CognitiveServices/checkDomainAvailability/action | Läser tillgängliga SKU: er för en prenumeration. |
> | Action | Microsoft. CognitiveServices/locations/checkSkuAvailability/Action | Läser tillgängliga SKU: er för en prenumeration. |
> | Action | Microsoft.CognitiveServices/locations/deleteVirtualNetworkOrSubnets/action | Meddelande från Microsoft. Network för att ta bort VirtualNetworks eller undernät. |
> | Action | Microsoft.CognitiveServices/Operations/read | Visa alla tillgängliga åtgärder i lista |
> | Action | Microsoft. CognitiveServices/register/åtgärd | Registrerar prenumerationen med Cognitive Services |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Commerce/RateCard/Read | Returnerar erbjudande data, metadata för resurs/mätare och priser för den aktuella prenumerationen. |
> | Action | Microsoft.Commerce/UsageAggregates/read | Hämtar Microsoft Azures förbrukning av en prenumeration. Resultatet innehåller mängd användnings data, prenumeration och resursinformation inom ett visst tidsintervall. |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Compute/availabilitySets/Delete | Tar bort tillgänglighetsuppsättningen |
> | Action | Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighetsuppsättning |
> | Action | Microsoft. Compute/availabilitySets/tillåtna storlekar/Read | Visar en lista över tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i tillgänglighetsuppsättningen |
> | Action | Microsoft. Compute/availabilitySets/Write | Skapar en ny tillgänglighetsuppsättning eller uppdaterar en befintlig |
> | Action | Microsoft.Compute/disks/beginGetAccess/action | Hämta diskens SAS-URI för blobåtkomst |
> | Action | Microsoft. Compute/disks/Delete | Tar bort disken |
> | Action | Microsoft.Compute/disks/endGetAccess/action | Återkalla diskens SAS-URI |
> | Action | Microsoft. Compute/disks/Read | Hämta egenskaperna för en disk |
> | Action | Microsoft. Compute/disks/Write | Skapar en ny disk eller uppdaterar en befintlig |
> | Action | Microsoft. Compute/Galleri/ta bort | Tar bort galleriet |
> | Action | Microsoft. Compute/Galleri/bilder/ta bort | Tar bort galleriavbildningen |
> | Action | Microsoft. Compute/Galleri/bilder/läsa | Hämtar egenskaperna för galleriavbildning |
> | Action | Microsoft. Compute/Galleri/images/versions/Delete | Tar bort galleriavbildningsversionen |
> | Action | Microsoft.Compute/galleries/images/versions/read | Hämtar egenskaperna för galleriavbildningsversion |
> | Action | Microsoft. Compute/Galleri/avbildningar/versioner/skriva | Skapar en ny galleriavbildningsversion eller uppdaterar en befintlig |
> | Action | Microsoft. Compute/Galleri/bilder/skriva | Skapar en ny galleriavbildning eller uppdaterar en befintlig |
> | Action | Microsoft. Compute/Galleri/läsa | Hämtar egenskaperna för galleri |
> | Action | Microsoft. Compute/Galleri/skriva | Skapar ett nytt galleri eller uppdaterar ett befintligt |
> | Action | Microsoft. Compute/hostGroups/Delete | Tar bort värd gruppen |
> | Action | Microsoft. Compute/hostGroups/Read | Hämta egenskaperna för en värd grupp |
> | Action | Microsoft.Compute/hostGroups/write | Skapar en ny värd grupp eller uppdaterar en befintlig värd grupp |
> | Action | Microsoft. Compute/hosts/Delete | Tar bort värden |
> | Action | Microsoft. Compute/hosts/Read | Hämta egenskaperna för en värd |
> | Action | Microsoft. Compute/hosts/Write | Skapar en ny värd eller uppdaterar en befintlig värd |
> | Action | Microsoft. Compute/images/Delete | Avbildningen tas bort |
> | Action | Microsoft. Compute/images/Read | Hämta egenskaperna för avbildningen |
> | Action | Microsoft. Compute/images/Write | Skapar en ny avbildning eller uppdaterar en befintlig |
> | Action | Microsoft. Compute/locations/capsOperations/Read | Hämtar status för en asynkron Caps-åtgärd |
> | Action | Microsoft. Compute/locations/diskOperations/Read | Hämtar status för en asynkron disk åtgärd |
> | Action | Microsoft.Compute/locations/logAnalytics/getRequestRateByInterval/action | Skapa loggar som visar det totala antalet förfrågningar per tidsintervall som hjälp vid begränsningsdiagnostik. |
> | Action | Microsoft.Compute/locations/logAnalytics/getThrottledRequests/action | Skapa loggar som visar aggregeringar av begränsade förfrågningar grupperade efter ResourceName, OperationName eller den tillämpade begränsningsprincipen. |
> | Action | Microsoft. Compute/locations/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Action | Microsoft. Compute/locations/Publishers/artifacttypes/erbjudande/läsa | Hämta egenskaperna för ett plattformsavbildningserbjudande |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/read | Hämta egenskaperna för en plattformsavbildnings-SKU |
> | Action | Microsoft.Compute/locations/publishers/artifacttypes/offers/skus/versions/read | Hämta egenskaperna för en plattformsavbildningsversion |
> | Action | Microsoft. Compute/locations/Publishers/artifacttypes/types/Read | Hämta egenskaperna för en VMExtension-typ |
> | Action | Microsoft. Compute/locations/utgivare/artifacttypes/types/versions/Read | Hämta egenskaperna för en VMExtension-version |
> | Action | Microsoft. Compute/locations/Publisher/Read | Hämta egenskaperna för en utgivare |
> | Action | Microsoft. Compute/locations/runCommands/Read | Listar tillgängliga körningskommandon på platsen |
> | Action | Microsoft. Compute/locations/usages/Read | Hämtar gränser för antalet tjänster och aktuell användning för prenumerationens beräkningsresurser på en plats |
> | Action | Microsoft.Compute/locations/vmSizes/read | Visar en lista över tillgängliga storlekar för virtuella datorer på en plats |
> | Action | Microsoft. Compute/Operations/Read | Visar en lista över åtgärder som är tillgängliga på Microsoft.Compute-resursprovidern |
> | Action | Microsoft. Compute/proximityPlacementGroups/Delete | Tar bort närhetsplaceringsgruppen |
> | Action | Microsoft.Compute/proximityPlacementGroups/read | Hämta egenskaperna för en närhetsplaceringsgrupp |
> | Action | Microsoft.Compute/proximityPlacementGroups/write | Skapar en ny närhetsplaceringsgrupp eller uppdaterar en befintlig |
> | Action | Microsoft. Compute/register/åtgärd | Registrerar prenumeration med Microsoft.Compute-resursprovidern |
> | Action | Microsoft. Compute/restorePointCollections/Delete | Tar bort samlingen med återställningspunkter och återställningspunkter den innehåller |
> | Action | Microsoft. Compute/restorePointCollections/Read | Hämta egenskaperna för en samling med återställningspunkter |
> | Action | Microsoft. Compute/restorePointCollections/restorePoints/Delete | Tar bort återställningspunkten |
> | Action | Microsoft. Compute/restorePointCollections/restorePoints/Read | Hämta egenskaperna för en återställningspunkt |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/retrieveSasUris/action | Hämta egenskaperna för en återställningspunkt tillsammans med blob-SAS-URI:er |
> | Action | Microsoft.Compute/restorePointCollections/restorePoints/write | Skapar en ny återställningspunkt |
> | Action | Microsoft. Compute/restorePointCollections/Write | Skapar en ny samling med återställningspunkter eller uppdaterar en befintlig |
> | Action | Microsoft. Compute/sharedVMImages/Delete | Tar bort SharedVMImage |
> | Action | Microsoft. Compute/sharedVMImages/Read | Hämta egenskaperna för en SharedVMImage |
> | Action | Microsoft. Compute/sharedVMImages/versions/Delete | Ta bort en SharedVMImageVersion |
> | Action | Microsoft. Compute/sharedVMImages/versions/Read | Hämta egenskaperna för en SharedVMImageVersion |
> | Action | Microsoft. Compute/sharedVMImages/versioner/replikera/åtgärd | Replikera en SharedVMImageVersion till målregioner |
> | Action | Microsoft. Compute/sharedVMImages/versions/Write | Skapa en ny SharedVMImageVersion eller uppdatera en befintlig |
> | Action | Microsoft. Compute/sharedVMImages/Write | Skapar en ny SharedVMImage eller uppdaterar en befintlig |
> | Action | Microsoft.Compute/skus/read | Hämtar listan över tillgängliga Microsoft.Compute-SKU:er för prenumerationen |
> | Action | Microsoft.Compute/snapshots/beginGetAccess/action | Hämta SAS-URI: n för ögonblicks bilden för BLOB-åtkomst |
> | Action | Microsoft. Compute/ögonblicks bilder/ta bort | Ta bort en ögonblicksbild |
> | Action | Microsoft.Compute/snapshots/endGetAccess/action | Återkalla SAS-URI för ögonblicks bilden |
> | Action | Microsoft. Compute/ögonblicks bilder/läsa | Hämta egenskaperna för en ögonblicksbild |
> | Action | Microsoft. Compute/ögonblicks bilder/skrivning | Skapa en ny ögonblicksbild eller uppdatera en befintlig |
> | Action | Microsoft. Compute/unregister/åtgärd | Avregistrerar prenumeration med Microsoft.Compute-resursprovidern |
> | Action | Microsoft.Compute/virtualMachines/capture/action | Avbildar den virtuella datorn genom att kopiera hårddiskar, och genererar en mall som kan användas för att skapa liknande virtuella datorer |
> | Action | Microsoft.Compute/virtualMachines/convertToManagedDisks/action | Konverterar den virtuella datorns blobbaserade diskar till hanterade diskar |
> | Action | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurser |
> | Action | Microsoft.Compute/virtualMachines/delete | Tar bort den virtuella datorn |
> | Action | Microsoft.Compute/virtualMachines/extensions/delete | Tar bort tillägget för virtuell dator |
> | Action | Microsoft.Compute/virtualMachines/extensions/read | Hämta egenskaperna för ett tillägg för virtuell dator |
> | Action | Microsoft.Compute/virtualMachines/extensions/write | Skapar ett nytt tillägg för virtuell dator eller uppdaterar ett befintligt |
> | Action | Microsoft.Compute/virtualMachines/generalize/action | Ställer in statusen för den virtuella datorn på Generaliserad och förbereder den virtuella datorn för avbildning |
> | Action | Microsoft. Compute/virtualMachines/instanceView/Read | Hämtar detaljerad körningsstatus för den virtuella datorn och dess resurser |
> | DataAction | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som vanlig användare |
> | DataAction | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med behörighet som Windows-administratör eller Linux-rotanvändare |
> | Action | Microsoft. Compute/virtualMachines/performMaintenance/Action | Utför underhållsåtgärd på den virtuella datorn. |
> | Action | Microsoft.Compute/virtualMachines/powerOff/action | Stänger av den virtuella datorn. Observera att den virtuella datorn fortsätter att faktureras. |
> | Action | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Action | Microsoft.Compute/virtualMachines/redeploy/action | Distribuerar om virtuell dator |
> | Action | Microsoft.Compute/virtualMachines/reimage/action | Avbildningen av den virtuella dator som använder differentieringsdisk återställs. |
> | Action | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Action | Microsoft.Compute/virtualMachines/runCommand/action | Kör ett fördefinierat skript på den virtuella datorn |
> | Action | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Action | Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar som de virtuella datorerna kan uppdateras till |
> | Action | Microsoft.Compute/virtualMachines/write | Skapar en ny virtuell dator eller uppdaterar en befintlig |
> | Action | Microsoft.Compute/virtualMachineScaleSets/deallocate/action | Stänger av och startar om beräkningsresurserna för VM-skalningsuppsättningens instanser  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete | Tar bort VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/delete/action | Tar bort VM-skalningsuppsättningens instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/delete | Hämta tillägg för VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/read | Hämtar egenskaperna för ett tillägg till VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/extensions/write | Skapar ett nytt tillägg för VM-skalningsuppsättningen eller uppdaterar ett befintligt |
> | Action | Microsoft.Compute/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action | Gå igenom plattformsuppdateringsdomänerna manuellt för en VM-skalningsuppsättning i Service Fabric för att slutföra den uppdatering som har fastnat |
> | Action | Microsoft. Compute/virtualMachineScaleSets/instanceView/Read | Hämtar instansvyn för VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/manualUpgrade/action | Uppdaterar manuellt instanser av den senaste modellen av VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/networkInterfaces/read | Hämta egenskaper för alla nätverksgränssnitt för en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osRollingUpgrade/action | Startar löpande uppgradering för att flytta alla instanser av skalningsuppsättningar för virtuell dator till den senast tillgängliga operativsystemversionen av plattformsavbildningen. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/osUpgradeHistory/read | Hämtar historiken för operativsystemuppgraderingar för en VM-skalningsuppsättning |
> | Action | Microsoft. Compute/virtualMachineScaleSets/performMaintenance/Action | Utför planerat underhåll på VM-skalningsuppsättningens instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/powerOff/action | Stänger av VM-skalningsuppsättningens instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/publicIPAddresses/read | Hämta egenskaper för alla offentliga IP-adresser för en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/read | Hämta egenskaperna för en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/redeploy/action | Distribuera om VM-skalningsuppsättningens instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimage/action | Skapar ny avbildning av VM-skalningsuppsättningens instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/reimageAll/action | Skapar nya avbildningar av alla diskar (operativsystemdisk och datadiskar) för instanserna för en skalningsuppsättning för virtuell dator  |
> | Action | Microsoft.Compute/virtualMachineScaleSets/restart/action | Startar om instanserna för VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/cancel/action | Avbryter samlad uppgradering av en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades/read | Hämta senaste status för den samlade uppgraderingen för VM-skalningsuppsättningen |
> | Action | Microsoft.Compute/virtualMachineScaleSets/scale/action | Kontrollera om en befintlig VM-skalningsuppsättning kan skala in/ut till det angivna antalet instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/skus/read | Visar en lista över giltiga SKU-koder för en befintlig VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/start/action | Startar VM-skalningsuppsättningens instanser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/deallocate/action | Stänger av och frigör beräkningsresurserna för en virtuell dator i en skaluppsättning. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/delete | Ta bort en viss virtuell dator i en skaluppsättning. |
> | Action | Microsoft. Compute/virtualMachineScaleSets/virtualMachines/instanceView/Read | Hämtar instansvyn för en virtuell dator i en skaluppsättning för virtuell dator. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/publicIPAddresses/read | Hämta egenskaper för offentlig IP-adress som skapats med skalnings uppsättning för virtuella datorer. Skalnings uppsättningen för virtuella datorer kan bara skapa en offentlig IP-adress per ipconfiguration (privat IP) |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read | Hämta egenskaper för en eller alla IP-konfigurationer för ett nätverks gränssnitt som skapats med skalnings uppsättning för virtuella datorer. IP-konfigurationer representerar privata IP-adresser |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/networkInterfaces/read | Hämta egenskaper för alla nätverksgränssnitt för en virtuell dator som har skapats med VM-skalningsuppsättningar |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/performMaintenance/action | Utför planerat underhåll på en virtuell datorinstans i en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/powerOff/action | Stänger av en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/read | Hämtar egenskaperna för en virtuell dator i en skaluppsättning för virtuell dator |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/redeploy/action | Distribuerar om en virtuell datorinstans i en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimage/action | Skapar ny avbildning av virtuell datorinstans i en VM-skalningsuppsättning. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/reimageAll/action | Skapa nya avbildningar av alla diskar (operativsystemdisk och datadiskar) för virtuell datorinstans i en skalningsuppsättning för virtuell dator. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/restart/action | Startar om en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/runCommand/action | Kör ett förinställt skript på en virtuell datorinstans i en skalningsuppsättning för virtuell dator. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/start/action | Startar en virtuell datorinstans i en skaluppsättning för virtuell dator. |
> | Action | Microsoft.Compute/virtualMachineScaleSets/virtualMachines/write | Uppdaterar egenskaperna för en virtuell dator i en VM-skalningsuppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/vmSizes/read | Lista tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i den virtuella datorns skal uppsättning |
> | Action | Microsoft.Compute/virtualMachineScaleSets/write | Skapar en ny VM-skalningsuppsättning eller uppdaterar en befintlig |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. förbrukning/saldo/Läs | Visa en lista över användnings sammanfattningen för en fakturerings period för en hanterings grupp. |
> | Action | Microsoft. förbrukning/budget/ta bort | Ta bort budgetarna med en prenumeration eller en hanterings grupp. |
> | Action | Microsoft. förbrukning/budget/Läs | Lista budgetarna efter en prenumeration eller en hanterings grupp. |
> | Action | Microsoft. förbrukning/budget/Skriv | Skapar och uppdaterar budgetarna av en prenumeration eller en hanterings grupp. |
> | Action | Microsoft. förbrukning/avgifter/Läs | Lista avgifter |
> | Action | Microsoft. förbrukning/krediter/Läs | Lista krediter |
> | Action | Microsoft. förbrukning/händelser/läsa | Lista händelser |
> | Action | Microsoft. förbrukning/prognoser/läsning | Lista prognoser |
> | Action | Microsoft. förbrukning/massor/läsning | Lista partier |
> | Action | Microsoft. förbrukning/marknads platser/läsa | Visa information om resursanvändning för Marketplace för en omfattning för EA-och WebDirect-prenumerationer. |
> | Action | Microsoft. förbrukning/operationresults/Read | Visa lista operationresults |
> | Action | Microsoft. förbrukning/åtgärder/läsa | Visa en lista över alla åtgärder som stöds av Microsoft. förbruknings resurs leverantör. |
> | Action | Microsoft. förbrukning/operationstatus/Read | Visa lista operationstatus |
> | Action | Microsoft. förbrukning/pricesheets/Read | Visa en lista över Pricesheets-data för en prenumeration eller en hanterings grupp. |
> | Action | Microsoft. förbrukning/register/åtgärd | Registrera till förbrukning RP |
> | Action | Microsoft.Consumption/reservationDetails/read | Visar användnings information för reserverade instanser med hjälp av en reservations order eller hanterings grupper. Informations data är per instans per dag nivå. |
> | Action | Microsoft.Consumption/reservationRecommendations/read | Lista enkla eller delade rekommendationer för reserverade instanser för en prenumeration. |
> | Action | Microsoft. förbrukning/reservationSummaries/Read | Visa en lista över användnings översikten för reserverade instanser med hjälp av reservations order eller hanterings grupper. Sammanfattnings data är antingen på varje månad eller per dag. |
> | Action | Microsoft.Consumption/reservationTransactions/read | Lista transaktions historiken för reserverade instanser efter hanterings grupper. |
> | Action | Microsoft. förbrukning/Taggar/läsa | Visa taggar för EA och prenumerationer. |
> | Action | Microsoft. förbrukning/klienter/läsning | Lista klienter |
> | Action | Microsoft. förbrukning/klient organisationer/register/åtgärd | Registrera åtgärds område för Microsoft. förbrukning av en klient. |
> | Action | Microsoft. förbrukning/villkor/läsa | Lista villkoren för en prenumeration eller en hanterings grupp. |
> | Action | Microsoft. förbrukning/usageDetails/Read | Visar användnings information för en omfattning för EA-och WebDirect-prenumerationer. |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.ContainerInstance/containerGroups/containers/exec/action | Exekvera till en viss container. |
> | Action | Microsoft. ContainerInstance/containerGroups/containers/logs/Read | Hämta loggar för en särskild container. |
> | Action | Microsoft. ContainerInstance/containerGroups/Delete | Tar bort den specifika containergruppen. |
> | Action | Microsoft. ContainerInstance/containerGroups/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar diagnostikinställningen för containergruppen. |
> | Action | Microsoft. ContainerInstance/containerGroups/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar diagnostikinställningen för containergruppen. |
> | Action | Microsoft.ContainerInstance/containerGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för containergruppen. |
> | Action | Microsoft. ContainerInstance/containerGroups/Read | Hämta alla behållar grupper. |
> | Action | Microsoft. ContainerInstance/containerGroups/restart/Action | Startar om en särskild containergrupp. |
> | Action | Microsoft. ContainerInstance/containerGroups/start/Action | Startar en särskild containergrupp. |
> | Action | Microsoft. ContainerInstance/containerGroups/stopp/åtgärd | Stoppar en angiven behållar grupp. Beräknings resurser frigörs och faktureringen stoppas. |
> | Action | Microsoft. ContainerInstance/containerGroups/Write | Skapa eller uppdatera en särskild containergrupp. |
> | Action | Microsoft. ContainerInstance/locations/cachedImages/Read | Hämtar de cachelagrade bilderna för prenumerationen i en region. |
> | Action | Microsoft. ContainerInstance/locations/capabilites/Read | Hämta funktionerna för en viss region. |
> | Action | Microsoft. ContainerInstance/locations/deleteVirtualNetworkOrSubnets/Action | Meddelar Microsoft.ContainerInstance att det virtuella nätverket eller undernätet håller på att tas bort. |
> | Action | Microsoft. ContainerInstance/platser/användning/läsa | Hämta användning för en viss region. |
> | Action | Microsoft. ContainerInstance/register/åtgärd | Registrerar prenumerationen för containerinstansens resursprovider och gör det möjligt att skapa containergrupper. |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.ContainerRegistry/checkNameAvailability/read | Kontrollerar om behållar register namnet är tillgängligt för användning. |
> | Action | Microsoft.ContainerRegistry/locations/deleteVirtualNetworkOrSubnets/action | Meddelar Microsoft. ContainerRegistry att det virtuella nätverket eller under nätet tas bort |
> | Action | Microsoft.ContainerRegistry/locations/operationResults/read | Hämtar ett asynkront åtgärds resultat |
> | Action | Microsoft.ContainerRegistry/operations/read | Visar en lista över alla tillgängliga Azure Container Registry REST API åtgärder |
> | Action | Microsoft.ContainerRegistry/register/action | Registrerar prenumerationen för resurs leverantören för behållar registret och gör det möjligt att skapa behållar register. |
> | Action | Microsoft. ContainerRegistry/register/artefakter/ta bort | Ta bort artefakt i ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/builds/cancel/action | Avbryter en befintlig version. |
> | Action | Microsoft.ContainerRegistry/registries/builds/getLogLink/action | Hämtar en länk för att hämta versions loggarna. |
> | Action | Microsoft.ContainerRegistry/registries/builds/read | Hämtar egenskaperna för den angivna versionen eller visar alla build-versioner för det angivna behållar registret. |
> | Action | Microsoft. ContainerRegistry/register/build/Write | Uppdaterar en version av ett behållar register med de angivna parametrarna. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/Delete | Tar bort en build-aktivitet från ett behållar register. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/listSourceRepositoryProperties/åtgärd | Visar en lista över käll kontroll egenskaper för en build-uppgift. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/Read | Hämtar egenskaperna för den angivna Bygg uppgiften eller visar alla Bygg aktiviteter för det angivna behållar registret. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/steg/Delete | Tar bort ett build-steg från en build-uppgift. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/steg/listBuildArguments/åtgärd | Visar en lista med build-argument för ett build-steg inklusive de hemliga argumenten. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/steg/läsa | Hämtar egenskaperna för det angivna build-steget eller visar en lista över alla build-steg för den angivna build-aktiviteten. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/steg/Skriv | Skapar eller uppdaterar ett build-steg för en build-aktivitet med de angivna parametrarna. |
> | Action | Microsoft. ContainerRegistry/register/buildTasks/Write | Skapar eller uppdaterar en build-uppgift för ett behållar register med de angivna parametrarna. |
> | Action | Microsoft. ContainerRegistry/register/ta bort | Tar bort ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/delete | Tar bort ett event Grid-filter från ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/read | Hämtar egenskaperna för det angivna Event Grid-filtret eller visar alla Event Grid-filter för det angivna behållar registret. |
> | Action | Microsoft.ContainerRegistry/registries/eventGridFilters/write | Skapar eller uppdaterar ett event Grid-filter för ett behållar register med de angivna parametrarna. |
> | Action | Microsoft.ContainerRegistry/registries/getBuildSourceUploadUrl/action | Hämtar överförings platsen för användaren att kunna överföra källan. |
> | Action | Microsoft.ContainerRegistry/registries/importImage/action | Importera avbildningen till behållar registret med de angivna parametrarna. |
> | Action | Microsoft.ContainerRegistry/registries/listBuildSourceUploadUrl/action | Hämta URL-plats för överföring av källa för ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/listCredentials/action | Visar inloggnings uppgifter för det angivna behållar registret. |
> | Action | Microsoft.ContainerRegistry/registries/listPolicies/read | Visar en lista över principer för det angivna behållar registret |
> | Action | Microsoft.ContainerRegistry/registries/listUsages/read | Visar kvot användningarna för det angivna behållar registret. |
> | Action | Microsoft.ContainerRegistry/registries/metadata/read | Hämtar metadata för en speciell lagrings plats för ett behållar register |
> | Action | Microsoft.ContainerRegistry/registries/metadata/write | Uppdaterar metadata för en lagrings plats för ett behållar register |
> | Action | Microsoft.ContainerRegistry/registries/operationStatuses/read | Hämtar åtgärds status för asynkrona register |
> | Action | Microsoft.ContainerRegistry/registries/pull/read | Hämta eller hämta avbildningar från ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/push/write | Push-överför eller Skriv avbildningar till ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/quarantineRead/read | Hämta eller hämta bilder i karantän från container Registry |
> | Action | Microsoft. ContainerRegistry/register/quarantineWrite/Write | Skriv/ändra karantän tillstånd för karantän avbildningar |
> | Action | Microsoft.ContainerRegistry/registries/queueBuild/action | Skapar en ny version utifrån parametrarna för begäran och lägger till den i versions kön. |
> | Action | Microsoft.ContainerRegistry/registries/read | Hämtar egenskaperna för det angivna behållar registret eller visar alla behållar register under den angivna resurs gruppen eller prenumerationen. |
> | Action | Microsoft.ContainerRegistry/registries/regenerateCredential/action | Återskapar en av inloggnings uppgifterna för det angivna behållar registret. |
> | Action | Microsoft. ContainerRegistry/register/replikeringar/ta bort | Tar bort en replikering från ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/replications/operationStatuses/read | Hämtar en asynkron åtgärds status för replikering |
> | Action | Microsoft.ContainerRegistry/registries/replications/read | Hämtar egenskaperna för den angivna replikeringen eller listar alla replikeringar för det angivna behållar registret. |
> | Action | Microsoft. ContainerRegistry/register/replikeringar/skrivning | Skapar eller uppdaterar en replikering för ett behållar register med de angivna parametrarna. |
> | Action | Microsoft. ContainerRegistry/register/kör/Avbryt/åtgärd | Avbryt en befintlig körning. |
> | Action | Microsoft.ContainerRegistry/registries/runs/listLogSasUrl/action | Hämtar SAS-webbadressen för loggen för en körning. |
> | Action | Microsoft.ContainerRegistry/registries/runs/read | Hämtar egenskaperna för en körning mot ett behållar register eller en lista över körningar. |
> | Action | Microsoft. ContainerRegistry/register/kör/Skriv | Uppdaterar en körning. |
> | Action | Microsoft.ContainerRegistry/registries/scheduleRun/action | Schemalägg en körning mot ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/sign/write | Push/pull-metadata för innehålls förtroende för ett behållar register. |
> | Action | Microsoft. ContainerRegistry/register/uppgifter/ta bort | Tar bort en aktivitet för ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/listDetails/action | Visa en lista med alla uppgifter om en aktivitet för ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/tasks/read | Hämtar en uppgift för ett behållar register eller listar alla aktiviteter. |
> | Action | Microsoft. ContainerRegistry/register/uppgifter/Skriv | Skapar eller uppdaterar en uppgift för ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/updatePolicies/write | Uppdaterar principerna för det angivna behållar registret |
> | Action | Microsoft. ContainerRegistry/register/Webhooks/Delete | Tar bort en webhook från ett behållar register. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/getCallbackConfig/action | Hämtar konfigurationen av tjänst-URI och anpassade rubriker för webhooken. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/listEvents/action | Visar en lista över senaste händelser för den angivna webhooken. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/operationStatuses/read | Hämtar en webhook-åtgärd status för asynkron åtgärd |
> | Action | Microsoft. ContainerRegistry/register/Webhooks/ping/åtgärd | Utlöser en ping-händelse som ska skickas till webhooken. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/read | Hämtar egenskaperna för den angivna webhooken eller visar en lista över alla webhookar för det angivna behållar registret. |
> | Action | Microsoft.ContainerRegistry/registries/webhooks/write | Skapar eller uppdaterar en webhook för ett behållar register med de angivna parametrarna. |
> | Action | Microsoft. ContainerRegistry/register/Skriv | Skapar eller uppdaterar ett behållar register med de angivna parametrarna. |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. container service/containerServices/Delete | Tar bort en containertjänst |
> | Action | Microsoft. container service/containerServices/Read | Hämta en containertjänst |
> | Action | Microsoft. container service/containerServices/Write | Skapar en ny containertjänst eller uppdaterar en befintlig |
> | Action | Microsoft. container service/locations/operationresults/Read | Hämtar status för resultatet för en asynkron åtgärd |
> | Action | Microsoft. container service/locations/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Action | Microsoft. container service/locations/Orchestration/Read | Visar en lista över containerdistributioner som stöds |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Hämta en åtkomstprofil för hanterat kluster efter rollnamn med hjälp av listautentiseringsuppgift |
> | Action | Microsoft.ContainerService/managedClusters/accessProfiles/read | Hämta en åtkomstprofil för hanterat kluster efter rollnamn |
> | Action | Microsoft. container service/managedClusters/agentPools/Delete | Tar bort en modempool |
> | Action | Microsoft. container service/managedClusters/agentPools/Read | Hämtar en agent |
> | Action | Microsoft. container service/managedClusters/agentPools/Write | Skapar en ny modempool eller uppdaterar en befintlig |
> | Action | Microsoft. container service/managedClusters/Delete | Tar bort ett hanterat kluster |
> | Action | Microsoft. container service/managedClusters/Detections/Read | Hämta hanterad kluster detektor |
> | Action | Microsoft. container service/managedClusters/listClusterAdminCredential/Action | Lista över clusterAdmin-autentiseringsuppgifter för ett hanterat kluster |
> | Action | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista över clusterUser-autentiseringsuppgifter för ett hanterat kluster |
> | Action | Microsoft. container service/managedClusters/Read | Hämta ett hanterat kluster |
> | Action | Microsoft.ContainerService/managedClusters/resetAADProfile/action | Återställa AAD-profilen för ett hanterat kluster |
> | Action | Microsoft.ContainerService/managedClusters/resetServicePrincipalProfile/action | Återställa tjänstens huvud konto profil för ett hanterat kluster |
> | Action | Microsoft. container service/managedClusters/upgradeprofiles/Read | Hämtar klustrets uppgraderingsprofil |
> | Action | Microsoft. container service/managedClusters/Write | Skapar ett nytt hanterat kluster eller uppdaterar ett befintligt |
> | Action | Microsoft. container service/openShiftClusters/Delete | Ta bort ett öppet Shift-kluster |
> | Action | Microsoft. container service/openShiftClusters/Read | Hämta ett öppet Shift-kluster |
> | Action | Microsoft. container service/openShiftClusters/Write | Skapar ett nytt öppet Shift-kluster eller uppdaterar ett befintligt |
> | Action | Microsoft. container service/openShiftManagedClusters/Delete | Ta bort ett öppet Skift hanterat kluster |
> | Action | Microsoft. container service/openShiftManagedClusters/Read | Hämta ett hanterat kluster med öppen Skift |
> | Action | Microsoft. container service/openShiftManagedClusters/Write | Skapar ett nytt hanterat kluster med öppen Skift eller uppdaterar ett befintligt |
> | Action | Microsoft. Container Service/Operations/Read | Visar en lista över åtgärder som är tillgängliga på Microsoft.ContainerService-resursprovidern |
> | Action | Microsoft. container service/register/åtgärd | Registrerar prenumeration på Microsoft.ContainerService-resursprovidern |
> | Action | Microsoft.ContainerService/unregister/action | Avregistrerar prenumeration på Microsoft.ComputeService-resursprovidern |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ContentModerator/Applications/Delete | Borttagningsåtgärd |
> | Action | Microsoft.ContentModerator/applications/listSecrets/action | Lista hemligheter |
> | Action | Microsoft.ContentModerator/applications/listSingleSignOnToken/action | Läsa token för enkel inloggning |
> | Action | Microsoft.ContentModerator/applications/read | Läsåtgärd |
> | Action | Microsoft.ContentModerator/applications/write | Skrivåtgärd |
> | Action | Microsoft.ContentModerator/applications/write | Skrivåtgärd |
> | Action | Microsoft.ContentModerator/listCommunicationPreference/action | Lista kommunikations inställningar |
> | Action | Microsoft.ContentModerator/operations/read | Läs åtgärder |
> | Action | Microsoft.ContentModerator/updateCommunicationPreference/action | Uppdatera kommunikations inställningar |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.CostManagement/cloudConnectors/delete | Ta bort den angivna cloudConnector. |
> | Action | Microsoft.CostManagement/cloudConnectors/read | Ange cloudConnectors för den autentiserade användaren. |
> | Action | Microsoft.CostManagement/cloudConnectors/write | Skapa eller uppdatera angiven cloudConnector. |
> | Action | Microsoft. CostManagement/dimensions/Read | Visa en lista över alla dimensioner som stöds enligt ett definitions område. |
> | Action | Microsoft.CostManagement/exports/action | Kör den angivna exporten. |
> | Action | Microsoft.CostManagement/exports/delete | Ta bort den angivna exporten. |
> | Action | Microsoft.CostManagement/exports/read | Lista exporten efter omfattning. |
> | Action | Microsoft.CostManagement/exports/run/action | Kör exporter. |
> | Action | Microsoft.CostManagement/exports/write | Skapa eller uppdatera den angivna exporten. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/externalSubscriptions/read | Visa en lista över externalSubscriptions i en externalBillingAccount för den autentiserade användaren. |
> | Action | Microsoft.CostManagement/externalBillingAccounts/read | Ange externalBillingAccounts för den autentiserade användaren. |
> | Action | Microsoft. CostManagement/externalSubscriptions/Read | Ange externalSubscriptions för den autentiserade användaren. |
> | Action | Microsoft. CostManagement/externalSubscriptions/Write | Uppdatera den associerade hanterings gruppen för externalSubscription |
> | Action | Microsoft.CostManagement/query/action | Fråga användnings data efter ett omfång. |
> | Action | Microsoft.CostManagement/query/read | Fråga användnings data efter ett omfång. |
> | Action | Microsoft. CostManagement/register/åtgärd | Registrera åtgärd för omfånget för Microsoft. CostManagement av en prenumeration. |
> | Action | Microsoft. CostManagement/Reports/Action | Schemalägg rapporter om användnings data med ett omfång. |
> | Action | Microsoft. CostManagement/Reports/Read | Schemalägg rapporter om användnings data med ett omfång. |
> | Action | Microsoft. CostManagement/Tenants/register/åtgärd | Registrera åtgärd för omfånget för Microsoft. CostManagement av en klient. |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/action | Skapa eller uppdatera Azure Customer Insights Adobe-metadata |
> | Action | Microsoft.CustomerInsights/hubs/adobemetadata/read | Läs alla Azure Customer Insights Adobe-metadata |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/delete | Ta bort valfri signatur princip för delad åtkomst för Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/Hubs/authorizationPolicies/Read | Läs alla Azure Customer Insights-signaturer för delad åtkomst |
> | Action | Microsoft. CustomerInsights/Hubbs/authorizationPolicies/regeneratePrimaryKey/Action | Återskapa primär nyckel för Azure Customer Insights Shared Access Signature policy |
> | Action | Microsoft.CustomerInsights/hubs/authorizationPolicies/regenerateSecondaryKey/action | Återskapa den sekundära nyckeln för Azure Customer Insights Shared Access Signature policy |
> | Action | Microsoft. CustomerInsights/hubbar/authorizationPolicies/Write | Skapa eller uppdatera en Azure Customer Insights-signatur för delad åtkomst |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/aktivera/åtgärd | Aktivera valfri Azure Customer Insights-anslutning |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/aktivera/åtgärd | Aktivera valfri Azure Customer Insights-anslutning |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/Delete | Ta bort alla Azure Customer Insights-kopplingar |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/getruntimestatus/åtgärd | Hämta all körnings status för Azure Customer Insights Connector |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/mappningar/aktivera/åtgärd | Aktivera valfri Azure Customer Insights Connector-mappning |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/mappningar/ta bort | Ta bort alla Azure Customer Insights kopplings mappningar |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/mappningar/åtgärder/läsa | Läs eventuella resultat för mappning av Azure Customer Insights kopplings koppling |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/mappningar/läsa | Läs alla Azure Customer Insights kopplings mappningar |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/mappningar/Skriv | Skapa eller uppdatera en Azure Customer Insights kopplings mappning |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/Operations/Read | Läs alla resultat för Azure Customer Insights Connector-åtgärden |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/läsa | Läs alla Azure Customer Insights-kopplingar |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/saveauthinfo/åtgärd | Skapa eller uppdatera Azure Customer Insights Connector-autentisering information |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/Update/åtgärd | Uppdatera Azure Customer Insights Connector |
> | Action | Microsoft. CustomerInsights/Hub/Connectors/Write | Skapa eller uppdatera en Azure Customer Insights-anslutning |
> | Action | Microsoft. CustomerInsights/Hubbs/crmmetadata/åtgärd | Skapa eller uppdatera Azure Customer Insights CRM-metadata |
> | Action | Microsoft. CustomerInsights/Hubs/crmmetadata/Read | Läs alla Azure Customer Insights CRM-metadata |
> | Action | Microsoft. CustomerInsights/Hub/Delete | Ta bort alla Azure Customer Insights Hub |
> | Action | Microsoft. CustomerInsights/hubbar/GDPR/Delete | Ta bort alla Azure Customer Insights-GDPR |
> | Action | Microsoft. CustomerInsights/Hubs/GDPR/Read | Läs alla Azure Customer Insights-GDPR |
> | Action | Microsoft. CustomerInsights/hubbar/GDPR/Write | Skapa eller uppdatera alla Azure Customer Insights-GDPR |
> | Action | Microsoft. CustomerInsights/Hubs/getbillingcredits/Read | Hämta fakturerings krediter för Azure Customer Insights Hub |
> | Action | Microsoft. CustomerInsights/Hubs/getbillinghistory/Read | Hämta fakturerings historik för Azure Customer Insights Hub |
> | Action | Microsoft.CustomerInsights/hubs/images/delete | Ta bort alla Azure Customer Insights-avbildningar |
> | Action | Microsoft. CustomerInsights/Hub/images/Read | Läs alla Azure Customer Insights-avbildningar |
> | Action | Microsoft. CustomerInsights/Hub/images/Write | Skapa eller uppdatera en Azure Customer Insights-avbildning |
> | Action | Microsoft. CustomerInsights/hubbar/interaktioner/ta bort | Ta bort alla Azure Customer Insights-interaktioner |
> | Action | Microsoft. CustomerInsights/hubbar/interaktioner/åtgärder/läsa | Läs eventuella resultat för interaktions åtgärder i Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/hubbar/interaktioner/läsa | Läs alla Azure Customer Insights-interaktioner |
> | Action | Microsoft.CustomerInsights/hubs/interactions/suggestrelationshiplinks/action | Föreslå Relations Länkar för alla Azure Customer Insights-interaktioner |
> | Action | Microsoft.CustomerInsights/hubs/interactions/write | Skapa eller uppdatera Azure Customer Insights-interaktion |
> | Action | Microsoft. CustomerInsights/hubbar/KPI/Delete | Ta bort alla prestanda indikatorer för Azure Customer Insights Key |
> | Action | Microsoft. CustomerInsights/hubbar/KPI/Operations/Read | Läs några resultat för prestanda indikatorer för Azure Customer Insights nyckel prestanda |
> | Action | Microsoft. CustomerInsights/hubbar/KPI/Read | Läs eventuella prestanda indikatorer för Azure Customer Insights Key |
> | Action | Microsoft. CustomerInsights/hubbar/KPI/Reprocessing/åtgärd | Bearbeta eventuella prestanda indikatorer för Azure Customer Insights Key |
> | Action | Microsoft. CustomerInsights/hubbar/KPI/Write | Skapa eller uppdatera en prestanda indikator för Azure Customer Insights Key |
> | Action | Microsoft. CustomerInsights/Hub/Links/Delete | Ta bort alla Azure Customer Insights-länkar |
> | Action | Microsoft. CustomerInsights/Hub/Links/Operations/Read | Läs eventuella resultat för Azure Customer Insights Links-åtgärd |
> | Action | Microsoft. CustomerInsights/Hub/Links/Read | Läs alla Azure Customer Insights-länkar |
> | Action | Microsoft. CustomerInsights/Hub/Links/Write | Skapa eller uppdatera Azure-kundlänkar |
> | Action | Microsoft. CustomerInsights/Hubbs/msemetadata/åtgärd | Skapa eller uppdatera metadata för Azure Customer Insights MSE |
> | Action | Microsoft. CustomerInsights/Hubs/msemetadata/Read | Läs alla Azure Customer Insights MSE-metadata |
> | Action | Microsoft. CustomerInsights/Hubs/operationresults/Read | Hämta Azure Customer Insights Hub-åtgärds resultat |
> | Action | Microsoft. CustomerInsights/Hub/förutsägelse/Delete | Ta bort alla Azure Customer Insights förutsägelser |
> | Action | Microsoft. CustomerInsights/Hubs/förutsägelses/Operations/Read | Läs alla resultat för Azure Customer Insights Förutsägelses-åtgärden |
> | Action | Microsoft. CustomerInsights/Hub/förutsägelse/Read | Läs alla Azure Customer Insights-förutsägelser |
> | Action | Microsoft. CustomerInsights/Hub/förutsägelse/Write | Skapa eller uppdatera alla Azure-kundförutsägelser |
> | Action | Microsoft. CustomerInsights/hubbar/predictivematchpolicies/Delete | Ta bort alla principer för förutsägelse matchning för Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/Hubs/predictivematchpolicies/Operations/Read | Läs några Azure Customer Insightss resultat för förutsägande matchning av principer |
> | Action | Microsoft. CustomerInsights/Hubs/predictivematchpolicies/Read | Läs alla principer för förutsägelse matchning för Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/hubbar/predictivematchpolicies/Write | Skapa eller uppdatera eventuella principer för förutsägelse matchning i Azure Customer Insights |
> | Action | Microsoft. CustomerInsights/Hubbs/profiler/Delete | Ta bort valfri Azure Customer Insights-profil |
> | Action | Microsoft. CustomerInsights/Hubs/profiler/Operations/Read | Läs alla resultat för Azure Customer Insights-profil åtgärden |
> | Action | Microsoft. CustomerInsights/hubbar/profiler/läsa | Läs valfri Azure Customer Insights-profil |
> | Action | Microsoft. CustomerInsights/hubbar/profiler/skriva | Skriv valfri Azure Customer Insights-profil |
> | Action | Microsoft. CustomerInsights/Hub/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft. CustomerInsights/Hub/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft. CustomerInsights/Hub/providers/Microsoft. Insights/logDefinitions/Read | Hämtar de tillgängliga loggarna för resursen |
> | Action | Microsoft. CustomerInsights/Hub/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för resursen |
> | Action | Microsoft. CustomerInsights/hubbar/läsa | Läs alla Azure Customer Insights Hub |
> | Action | Microsoft. CustomerInsights/hubbar/relationshiplinks/Delete | Ta bort alla Azure Customer Insights Relations länkar |
> | Action | Microsoft. CustomerInsights/Hubs/relationshiplinks/Operations/Read | Läs eventuella resultat för Azure Customer Insights Relations länkar |
> | Action | Microsoft. CustomerInsights/Hubs/relationshiplinks/Read | Läs alla länkar för Azure Customer Insights-relationer |
> | Action | Microsoft. CustomerInsights/hubbar/relationshiplinks/Write | Skapa eller uppdatera Azure Customer Insights Relations länkar |
> | Action | Microsoft. CustomerInsights/hubbar/relationer/ta bort | Ta bort alla Azure Customer Insights-relationer |
> | Action | Microsoft. CustomerInsights/hubbar/relationer/åtgärder/Läs | Läs alla resultat för Azure Customer Insights-relationer |
> | Action | Microsoft. CustomerInsights/hubbar/relationer/läsa | Läs alla Azure Customer Insights-relationer |
> | Action | Microsoft. CustomerInsights/hubbar/relationer/Skriv | Skapa eller uppdatera Azure Customer Insights-relationer |
> | Action | Microsoft. CustomerInsights/hubbar/roleAssignments/Delete | Ta bort alla Azure Customer Insights RBAC-tilldelning |
> | Action | Microsoft. CustomerInsights/Hubs/roleAssignments/Operations/Read | Läs eventuella resultat för Azure Customer Insights RBAC-tilldelning |
> | Action | Microsoft. CustomerInsights/Hubs/roleAssignments/Read | Läs en Azure Customer Insights RBAC-tilldelning |
> | Action | Microsoft. CustomerInsights/hubbar/roleAssignments/Write | Skapa eller uppdatera en Azure Customer Insights RBAC-tilldelning |
> | Action | Microsoft. CustomerInsights/hubbar/roller/läsa | Läs alla Azure Customer Insights RBAC-roller |
> | Action | Microsoft. CustomerInsights/Hubbs/salesforcemetadata/åtgärd | Skapa eller uppdatera alla Azure Customer Insights SalesForce-metadata |
> | Action | Microsoft. CustomerInsights/Hubs/salesforcemetadata/Read | Läs alla Azure Customer Insights SalesForce-metadata |
> | Action | Microsoft. CustomerInsights/Hub/segment/Delete | Ta bort alla Azure Customer Insights segment |
> | Action | Microsoft. CustomerInsights/hubbar/segment/dynamisk/åtgärd | Hantering av Azure-kunders Insight-dynamiska segment |
> | Action | Microsoft. CustomerInsights/hubbar/segment/läsa | Läs alla Azure Customer Insights-segment |
> | Action | Microsoft. CustomerInsights/hubbar/segment/statisk/åtgärd | Hantering av alla Azure-kunders Insight static-segment |
> | Action | Microsoft. CustomerInsights/hubbar/segment/skriva | Skapa eller uppdatera Azure Customer Insights segment |
> | Action | Microsoft. CustomerInsights/hubbar/sqlconnectionstrings/Delete | Ta bort alla Azure Customer Insights-SqlConnectionStrings |
> | Action | Microsoft. CustomerInsights/Hubs/sqlconnectionstrings/Read | Läs alla Azure Customer Insights-SqlConnectionStrings |
> | Action | Microsoft. CustomerInsights/hubbar/sqlconnectionstrings/Write | Skapa eller uppdatera alla Azure Customer Insights-SqlConnectionStrings |
> | Action | Microsoft. CustomerInsights/Hubbs/suggesttypeschema/åtgärd | Skapa ett schema för föreslå typ från exempel data |
> | Action | Microsoft. CustomerInsights/Hubs/tenantmanagement/Read | Hantera inställningar för Azure Customer Insights Hub |
> | Action | Microsoft. CustomerInsights/Hub/views/Delete | Ta bort valfri Azure Customer Insights app-vy |
> | Action | Microsoft. CustomerInsights/Hub/views/Read | Läs någon app-vy för Azure Customer Insights |
> | Action | Microsoft.CustomerInsights/hubs/views/write | Skapa eller uppdatera valfri Azure Customer Insights app-vy |
> | Action | Microsoft. CustomerInsights/Hubs/widgettypes/Read | Läs alla typer av widgetar för Azure Customer Insights app |
> | Action | Microsoft. CustomerInsights/Hub/Write | Skapa eller uppdatera Azure Customer Insights Hub |
> | Action | Microsoft.CustomerInsights/operations/read | Läs Azure Customer Insights API-metadata |
> | Action | Microsoft. CustomerInsights/register/åtgärd | Registrerar prenumerationen för Customer Insights Resource Provider och gör det möjligt att skapa Customer Insights resurser |
> | Action | Microsoft.CustomerInsights/unregister/action | Avregistrerar prenumerationen för Customer Insights Resource Provider |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. data-/jobb/bookShipmentPickUp/åtgärd | Gör det möjligt att boka upphämtning för returleveranser. |
> | Action | Microsoft. data-/jobb/Avbryt/åtgärd | Annullerar en pågående beställning. |
> | Action | Microsoft. data-/jobb/ta bort | Ta bort order |
> | Action | Microsoft. data-/jobb/listCredentials/åtgärd | Visar lista med okrypterade autentiseringsuppgifter för beställningen. |
> | Action | Microsoft. data-/jobb/läsa | Lista eller hämta order |
> | Action | Microsoft. data-/jobb/Skriv | Skapa eller uppdatera order |
> | Action | Microsoft. data-/plats/availableSkus/åtgärd | Den här metoden returnerar listan över tillgängliga SKU:er. |
> | Action | Microsoft. Data Center/locations/availableSkus/Read | Visa eller hämta tillgängliga SKU: er |
> | Action | Microsoft.DataBox/locations/operationResults/read | Visa eller hämta åtgärds resultat |
> | Action | Microsoft. data-/plats/validateAddress/åtgärd | Verifierar leveransadressen och anger alternativa adresser om sådana finns. |
> | Action | Microsoft. data-/plats/validateInputs/åtgärd |  |
> | Action | Microsoft. data-/register/åtgärd | Registrera providern Microsoft. data, |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Visar eller hämtar aviseringarna |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Alerts/Read | Visar eller hämtar aviseringarna |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Delete | Tar bort bandbredds scheman |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Listar eller hämtar bandbredds scheman |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Read | Listar eller hämtar bandbredds scheman |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/bandwidthSchedules/Write | Skapar eller uppdaterar bandbredds scheman |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Delete | Tar bort Data Box Edge enheter |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/downloadUpdates/Action | Ladda ned uppdateringar på enhet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/getExtendedInformation/Action | Hämtar utökad resursinformation |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/installUpdates/Action | Installera uppdateringar på enhet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Jobs/Read | Visar eller hämtar jobben |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/networkSettings/read | Visar eller hämtar nätverks inställningarna för enheten |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/operationsStatus/Read | Visar eller hämtar åtgärds status |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Delete | Tar bort order |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/orders/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar eller hämtar order |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Orders/Read | Listar eller hämtar order |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/order/Write | Skapar eller uppdaterar order |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Read | Listar eller hämtar Data Box Edge enheter |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Delete | Tar bort rollerna |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Read | Visar eller hämtar roller |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Read | Visar eller hämtar roller |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/roles/Write | Skapar eller uppdaterar roller |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/scanForUpdates/Action | Sök efter uppdateringar |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/securitySettings/update/action | Uppdatera säkerhetsinställningar |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Delete | Tar bort resurserna |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/shares/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Read | Listar eller hämtar resurserna |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Read | Listar eller hämtar resurserna |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/reshares/Refresh/åtgärd | Uppdatera resursens metadata med data från molnet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Shares/Write | Skapar eller uppdaterar resurserna |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Delete | Tar bort autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Visar eller hämtar autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Read | Visar eller hämtar autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/storageAccountCredentials/Write | Skapar eller uppdaterar autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Delete | Tar bort utlösare |
> | Action | Microsoft.DataBoxEdge/dataBoxEdgeDevices/triggers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Read | Listar eller hämtar utlösare |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Read | Listar eller hämtar utlösare |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/triggers/Write | Skapar eller uppdaterar utlösarna |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/updateSummary/Read | Visar eller hämtar uppdaterings sammanfattningen |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/uploadCertificate/Action | Ladda upp certifikat för enhetsregistrering |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Delete | Tar bort resurs användare |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/operationResults/Read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Read | Listar eller hämtar delnings användare |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Read | Listar eller hämtar delnings användare |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Users/Write | Skapar eller uppdaterar resurs användare |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Skapar eller uppdaterar Data Box Edge enheter |
> | Action | Microsoft. DataBoxEdge/dataBoxEdgeDevices/Write | Skapar eller uppdaterar Data Box Edge enheter |

## <a name="microsoftdatabricks"></a>Microsoft. Databricks

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Databricks/register/åtgärd | Registrera på Databricks. |
> | Action | Microsoft. Databricks/arbets ytor/ta bort | Tar bort en Databricks-arbetsyta. |
> | Action | Microsoft. Databricks/arbets ytor/providers/Microsoft. Insights/diagnosticSettings/Read | Ställer in tillgängliga diagnostikinställningar för Databricks-arbetsytan |
> | Action | Microsoft. Databricks/arbets ytor/providers/Microsoft. Insights/diagnosticSettings/Write | Lägg till eller ändra diagnostikinställningarna. |
> | Action | Microsoft. Databricks/arbets ytor/providers/Microsoft. Insights/logDefinitions/Read | Hämtar tillgängliga loggdefinitioner för Databricks-arbetsytan |
> | Action | Microsoft. Databricks/arbets ytor/läsa | Hämtar en lista över Databricks-arbetsytor. |
> | Action | Microsoft. Databricks/arbets ytor/Skriv | Skapar en Databricks-arbetsyta. |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. DataCatalog/kataloger/ta bort | Ta bort katalog resurs för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/kataloger/läsa | Läs katalog resurs för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/kataloger/skrivning | Skriv katalog resurs för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/checkNameAvailability/Read | Kontrol lera katalog namns tillgänglighet för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/datacatalogs/Delete | Ta bort DataCatalog Resource för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/datacatalogs/Read | Läs DataCatalog-resursen för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/datacatalogs/Write | Skriv DataCatalog Resource för Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/Operations/Read | Läser alla tillgängliga åtgärder i Data Catalog Resource Provider. |
> | Action | Microsoft. DataCatalog/register/åtgärd | Registrera prenumerationen för Data Catalog Resource Provider |
> | Action | Microsoft. DataCatalog/avregistrera/åtgärd | Avregistrera prenumerationen för Data Catalog Resource Provider |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DataFactory/checkazuredatafactorynameavailability/read | Kontrollerar om Data Factory namn är tillgängligt för användning. |
> | Action | Microsoft.DataFactory/datafactories/activitywindows/read | Läser aktivitets fönster i Data Factory med angivna parametrar. |
> | Action | Microsoft.DataFactory/datafactories/datapipelines/activities/activitywindows/read | Läser Windows aktivitets fönster för pipeline-aktiviteten med angivna parametrar. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/activitywindows/Read | Läser Windows aktivitets fönster för pipelinen med angivna parametrar. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/Delete | Tar bort en pipeline. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/PAUSE/åtgärd | Pausar en pipeline. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/Read | Läser en pipeline. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/Resume/åtgärd | Återupptar eventuell pipeline. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/Update/åtgärd | Uppdaterar en pipeline. |
> | Action | Microsoft. DataFactory/datafactories/datapipelines/Write | Skapar eller uppdaterar en pipeline. |
> | Action | Microsoft.DataFactory/datafactories/datasets/activitywindows/read | Läser Windows aktivitets fönster för data uppsättningen med angivna parametrar. |
> | Action | Microsoft.DataFactory/datafactories/datasets/delete | Tar bort alla data uppsättningar. |
> | Action | Microsoft. DataFactory/datafactories/data uppsättningar/läsa | Läser alla data uppsättningar. |
> | Action | Microsoft.DataFactory/datafactories/datasets/sliceruns/read | Läser data sektor körningen för den aktuella data mängden med den aktuella start tiden. |
> | Action | Microsoft. DataFactory/datafactories/data uppsättningar/sektorer/läsning | Hämtar data sektorerna under den aktuella perioden. |
> | Action | Microsoft.DataFactory/datafactories/datasets/slices/write | Uppdatera data sektorns status. |
> | Action | Microsoft.DataFactory/datafactories/datasets/write | Skapar eller uppdaterar alla data uppsättningar. |
> | Action | Microsoft.DataFactory/datafactories/delete | Tar bort Data Factory. |
> | Action | Microsoft. DataFactory/datafactories/gateways/ConnectionInfo/åtgärd | Läser anslutnings informationen för en gateway. |
> | Action | Microsoft. DataFactory/datafactories/gateways/Delete | Tar bort alla gatewayer. |
> | Action | Microsoft. DataFactory/datafactories/gateways/listauthkeys/åtgärd | Visar en lista över autentiseringsinställningar för alla gatewayer. |
> | Action | Microsoft. DataFactory/datafactories/Gateway/Read | Läser alla gatewayer. |
> | Action | Microsoft. DataFactory/datafactories/gateways/regenerateauthkey/åtgärd | Återskapar autentiseringsinställningarna för en gateway. |
> | Action | Microsoft. DataFactory/datafactories/Gateway/Write | Skapar eller uppdaterar en gateway. |
> | Action | Microsoft. DataFactory/datafactories/linkedServices/Delete | Tar bort alla länkade tjänster. |
> | Action | Microsoft. DataFactory/datafactories/linkedServices/Read | Läser en länkad tjänst. |
> | Action | Microsoft. DataFactory/datafactories/linkedServices/Write | Skapar eller uppdaterar en länkad tjänst. |
> | Action | Microsoft. DataFactory/datafactories/Read | Läser Data Factory. |
> | Action | Microsoft. DataFactory/datafactories/körs/LogInfo/Read | Läser en SAS-URI till en BLOB-behållare som innehåller loggarna. |
> | Action | Microsoft.DataFactory/datafactories/tables/delete | Tar bort alla data uppsättningar. |
> | Action | Microsoft. DataFactory/datafactories/tables/Read | Läser alla data uppsättningar. |
> | Action | Microsoft. DataFactory/datafactories/tables/Write | Skapar eller uppdaterar alla data uppsättningar. |
> | Action | Microsoft.DataFactory/datafactories/write | Skapar eller uppdaterar Data Factory. |
> | Action | Microsoft. DataFactory/factors/cancelpipelinerun/Action | Avbryter pipeline-körningen som anges av körnings-ID: t. |
> | Action | Microsoft.DataFactory/factories/createdataflowdebugsession/action | Skapar en session för data flödes fel sökning. |
> | Action | Microsoft. DataFactory/factors/data flöden/Delete | Tar bort data flöde. |
> | Action | Microsoft. DataFactory/factors/data flöden/Read | Läser data flöde. |
> | Action | Microsoft.DataFactory/factories/dataflows/write | Skapa eller uppdatera data flöde |
> | Action | Microsoft. DataFactory/factors/data uppsättningar/ta bort | Tar bort alla data uppsättningar. |
> | Action | Microsoft. DataFactory/factors/data uppsättningar/läsa | Läser alla data uppsättningar. |
> | Action | Microsoft. DataFactory/factors/data uppsättningar/skrivning | Skapar eller uppdaterar alla data uppsättningar. |
> | Action | Microsoft. DataFactory/factors/Delete | Tar bort Data Factory. |
> | Action | Microsoft.DataFactory/factories/deletedataflowdebugsession/action | Tar bort en data flödes fel söknings session. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/action | Hämtar åtkomst till ADF dataplanen-tjänsten. |
> | Action | Microsoft.DataFactory/factories/getDataPlaneAccess/read | Läser åtkomst till ADF dataplanen-tjänsten. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/action | Hämta funktions värde för exponerings kontroll för den angivna platsen. |
> | Action | Microsoft.DataFactory/factories/getFeatureValue/read | Läser funktions värdet för exponerings kontroll för den angivna platsen. |
> | Action | Microsoft. DataFactory/factors/getGitHubAccessToken/Action | Hämtar GitHub-åtkomsttoken. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/delete | Tar bort alla Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getconnectioninfo/read | Läser Integration Runtime anslutnings information. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getObjectMetadata/action | Hämta SSIS Integration Runtime metadata för den angivna Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/getstatus/read | Läser Integration Runtimes status. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/linkedIntegrationRuntime/action | Skapa länkad Integration Runtime referens på den angivna delade Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/listauthkeys/read | Visar en lista över autentiseringsinställningarna för alla Integration Runtime. |
> | Action | Microsoft. DataFactory/factors/integrationruntimes/monitoringdata/Read | Hämtar övervaknings data för alla Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/delete | Tar bort noden för angivet Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/ipAddress/action | Returnerar IP-adressen för den angivna noden i Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/read | Läser noden för angivet Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/nodes/write | Uppdaterar en egen värd Integration Runtime-nod. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/read | Läser alla Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/refreshObjectMetadata/action | Uppdatera SSIS Integration Runtime metadata för den angivna Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/regenerateauthkey/action | Återskapar autentiseringsinställningarna för den angivna Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/removelinks/action | Tar bort länkade Integration Runtime referenser från den angivna Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/start/action | Startar alla Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/stop/action | Stoppar alla Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/synccredentials/action | Synkroniserar autentiseringsuppgifterna för angivet Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/upgrade/action | Uppgraderar den angivna Integration Runtime. |
> | Action | Microsoft.DataFactory/factories/integrationruntimes/write | Skapar eller uppdaterar Integration Runtime. |
> | Action | Microsoft. DataFactory/factors/linkedServices/Delete | Tar bort länkad tjänst. |
> | Action | Microsoft. DataFactory/factors/linkedServices/Read | Läser länkad tjänst. |
> | Action | Microsoft. DataFactory/factors/linkedServices/Write | Skapa eller uppdatera länkade tjänster |
> | Action | Microsoft. DataFactory/factors/pipelineruns/activityruns/Read | Läser aktivitets körningar för angivet pipeline-körnings-ID. |
> | Action | Microsoft. DataFactory/factors/pipelineruns/Cancel/Action | Avbryter pipeline-körningen som anges av körnings-ID: t. |
> | Action | Microsoft. DataFactory/factors/pipelineruns/queryactivityruns/Action | Frågar aktiviteten körs för angivet pipeline-körnings-ID. |
> | Action | Microsoft. DataFactory/factors/pipelineruns/queryactivityruns/Read | Läser resultatet av frågans aktivitets körningar för det angivna pipeline-körnings-ID: t. |
> | Action | Microsoft. DataFactory/factors/pipelineruns/Read | Läser pipeline-körningar. |
> | Action | Microsoft. DataFactory/factors/pipeline/createrun/Action | Skapar en körning för pipelinen. |
> | Action | Microsoft. DataFactory/factors/pipeline/Delete | Tar bort pipeline. |
> | Action | Microsoft. DataFactory/factors/pipelines/pipelineruns/activityruns/Progress/Read | Hämtar förloppet för aktivitets körningar. |
> | Action | Microsoft. DataFactory/factors/pipelines/pipelineruns/Read | Läser körningen av pipelinen. |
> | Action | Microsoft. DataFactory/factors/pipelines/Read | Läser pipeline. |
> | Action | Microsoft. DataFactory/factors/pipeline/Write | Skapa eller uppdatera pipeline |
> | Action | Microsoft. DataFactory/factors/querydebugpipelineruns/Action | Frågar om fel söknings pipelinen körs. |
> | Action | Microsoft. DataFactory/factors/querypipelineruns/Action | Frågar om pipelinen körs. |
> | Action | Microsoft. DataFactory/factors/querypipelineruns/Read | Läser resultatet av pipeline-körningar. |
> | Action | Microsoft. DataFactory/factors/querytriggerruns/Action | Frågar om utlösaren körs. |
> | Action | Microsoft. DataFactory/factors/querytriggerruns/Read | Läser resultatet av utlösare som körs. |
> | Action | Microsoft. DataFactory/factors/Read | Läser Data Factory. |
> | Action | Microsoft.DataFactory/factories/startdataflowdebugsession/action | Startar en session med data flödes fel sökning. |
> | Action | Microsoft. DataFactory/factors/triggerruns/Read | Läser utlösare som körs. |
> | Action | Microsoft. DataFactory/factors/triggers/Delete | Tar bort alla utlösare. |
> | Action | Microsoft. DataFactory/factors/triggers/Read | Läser alla utlösare. |
> | Action | Microsoft. DataFactory/factors/utlösare/starta/åtgärd | Startar alla utlösare. |
> | Action | Microsoft. DataFactory/factors/utlösare/stoppa/åtgärd | Stoppar eventuell utlösare. |
> | Action | Microsoft. DataFactory/factors/triggers/triggerruns/Read | Läser utlösare som körs. |
> | Action | Microsoft. DataFactory/factors/triggers/Write | Skapar eller uppdaterar eventuella utlösare. |
> | Action | Microsoft. DataFactory/factors/Write | Skapa eller uppdatera Data Factory |
> | Action | Microsoft.DataFactory/locations/configureFactoryRepo/action | Konfigurerar lagrings platsen för fabriken. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/action | Hämta funktions värde för exponerings kontroll för den angivna platsen. |
> | Action | Microsoft.DataFactory/locations/getFeatureValue/read | Läser funktions värdet för exponerings kontroll för den angivna platsen. |
> | Action | Microsoft. DataFactory/Operations/Read | Läser alla åtgärder i Microsoft Data Factory-providern. |
> | Action | Microsoft. DataFactory/register/åtgärd | Registrerar prenumerationen för Data Factory Resource Provider. |
> | Action | Microsoft.DataFactory/unregister/action | Avregistrerar prenumerationen för Data Factory Resource Provider. |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/delete | Ta bort en beräknings princip. |
> | Action | Microsoft.DataLakeAnalytics/accounts/computePolicies/read | Hämta information om en beräknings princip. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/computePolicies/Write | Skapa eller uppdatera en beräknings princip. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/delete | Ta bort länken mellan ett DataLakeStore-konto och ett DataLakeAnalytics-konto. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/read | Hämta information om ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Action | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/Delete | Ta bort ett DataLakeAnalytics-konto. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/delete | Ta bort en brand Väggs regel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/read | Hämta information om en brand Väggs regel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/firewallRules/write | Skapa eller uppdatera en brand Väggs regel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/operationResults/read | Erhåll resultatet av en DataLakeAnalytics-konto åtgärd. |
> | Action | Microsoft.DataLakeAnalytics/accounts/read | Hämta information om ett befintligt DataLakeAnalytics-konto. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers/listSasTokens/action | Visa en lista med SAS-token för lagrings behållare för ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/containers/Read | Hämta behållare för ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/storageAccounts/Delete | Ta bort länken till ett lagrings konto från ett DataLakeAnalytics-konto. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/read | Hämta information om ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Action | Microsoft.DataLakeAnalytics/accounts/storageAccounts/write | Skapa eller uppdatera ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Action | Microsoft. DataLakeAnalytics/Accounts/TakeOwnership/Action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
> | Action | Microsoft.DataLakeAnalytics/accounts/transferAnalyticsUnits/action | Överför SystemMaxAnalyticsUnits mellan DataLakeAnalytics-konton. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/delete | Ta bort en regel för virtuellt nätverk. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/read | Hämta information om en virtuell nätverks regel. |
> | Action | Microsoft.DataLakeAnalytics/accounts/virtualNetworkRules/write | Skapa eller uppdatera en regel för virtuellt nätverk. |
> | Action | Microsoft.DataLakeAnalytics/accounts/write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | Action | Microsoft. DataLakeAnalytics/locations/Capability/Read | Få kapacitets information för en prenumeration angående användning av DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/locations/checkNameAvailability/action | Kontrol lera tillgängligheten för ett DataLakeAnalytics konto namn. |
> | Action | Microsoft.DataLakeAnalytics/locations/operationResults/read | Erhåll resultatet av en DataLakeAnalytics-konto åtgärd. |
> | Action | Microsoft. DataLakeAnalytics/platser/användning/läsa | Hämta information om kvot användning för en prenumeration avseende användning av DataLakeAnalytics. |
> | Action | Microsoft.DataLakeAnalytics/operations/read | Hämta tillgängliga åtgärder för DataLakeAnalytics. |
> | Action | Microsoft. DataLakeAnalytics/register/åtgärd | Registrera prenumeration på DataLakeAnalytics. |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DataLakeStore/accounts/delete | Ta bort ett DataLakeStore-konto. |
> | Action | Microsoft.DataLakeStore/accounts/enableKeyVault/action | Aktivera nyckel valv för ett DataLakeStore-konto. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/delete | Ta bort ett EventGrid-filter. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/read | Hämta ett EventGrid-filter. |
> | Action | Microsoft.DataLakeStore/accounts/eventGridFilters/write | Skapa eller uppdatera ett EventGrid-filter. |
> | Action | Microsoft. DataLakeStore/Accounts/firewallRules/Delete | Ta bort en brand Väggs regel. |
> | Action | Microsoft.DataLakeStore/accounts/firewallRules/read | Hämta information om en brand Väggs regel. |
> | Action | Microsoft. DataLakeStore/Accounts/firewallRules/Write | Skapa eller uppdatera en brand Väggs regel. |
> | Action | Microsoft.DataLakeStore/accounts/operationResults/read | Erhåll resultatet av en DataLakeStore-konto åtgärd. |
> | Action | Microsoft.DataLakeStore/accounts/read | Hämta information om ett befintligt DataLakeStore-konto. |
> | Action | Microsoft.DataLakeStore/accounts/Superuser/action | Bevilja superanvändare på Data Lake Store när de beviljas med Microsoft. Authorization/roleAssignments/Write. |
> | Action | Microsoft. DataLakeStore/Accounts/trustedIdProviders/Delete | Ta bort en betrodd identitets leverantör. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/read | Hämta information om en betrodd identitets leverantör. |
> | Action | Microsoft.DataLakeStore/accounts/trustedIdProviders/write | Skapa eller uppdatera en betrodd identitets leverantör. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/delete | Ta bort en regel för virtuellt nätverk. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/read | Hämta information om en virtuell nätverks regel. |
> | Action | Microsoft.DataLakeStore/accounts/virtualNetworkRules/write | Skapa eller uppdatera en regel för virtuellt nätverk. |
> | Action | Microsoft.DataLakeStore/accounts/write | Skapa eller uppdatera ett DataLakeStore-konto. |
> | Action | Microsoft. DataLakeStore/locations/Capability/Read | Få kapacitets information för en prenumeration angående användning av DataLakeStore. |
> | Action | Microsoft.DataLakeStore/locations/checkNameAvailability/action | Kontrol lera tillgängligheten för ett DataLakeStore konto namn. |
> | Action | Microsoft.DataLakeStore/locations/operationResults/read | Erhåll resultatet av en DataLakeStore-konto åtgärd. |
> | Action | Microsoft.DataLakeStore/locations/usages/read | Hämta information om kvot användning för en prenumeration avseende användning av DataLakeStore. |
> | Action | Microsoft.DataLakeStore/operations/read | Hämta tillgängliga åtgärder för DataLakeStore. |
> | Action | Microsoft.DataLakeStore/register/action | Registrera prenumeration på DataLakeStore. |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DataMigration/locations/operationResults/read | Hämta status för en tidskrävande åtgärd som berör 202 accepterade svar |
> | Action | Microsoft. data migration/locations/operationStatuses/Read | Hämta status för en tidskrävande åtgärd som berör 202 accepterade svar |
> | Action | Microsoft. data migration/register/åtgärd | Registrerar prenumerationen med Azure Database Migration Service-providern |
> | Action | Microsoft. data migration/Services/addWorker/Action | Lägger till en DMS-arbetare till tjänstens tillgängliga arbetare |
> | Action | Microsoft. data migration/Services/checkStatus/Action | Kontrollera om tjänsten har distribuerats och om den körs |
> | Action | Microsoft. data migration/Services/configureWorker/Action | Konfigurerar en DMS-arbetare för tjänstens tillgängliga arbetare |
> | Action | Microsoft. data migration/Services/Delete | En resursgrupp och alla dess underordnade tas bort. |
> | Action | Microsoft. data migration/Services/projekt/accessArtifacts/åtgärd | Generera en URL som kan användas för GET- eller PUT-projektsartefakter |
> | Action | Microsoft. data migration/Services/projekt/Delete | En resursgrupp och alla dess underordnade tas bort. |
> | Action | Microsoft. data migration/Services/projekt/filer/Delete | En resursgrupp och alla dess underordnade tas bort. |
> | Action | Microsoft. data migration/Services/projekt/filer/läsa | Läs information om resurser |
> | Action | Microsoft. data migration/Services/projekt/filer/Läs/åtgärd | Hämta en URL som kan användas för att läsa filens innehåll |
> | Action | Microsoft. data migration/Services/projekt/filer/readWrite/åtgärd | Hämta en URL som kan användas för att läsa eller skriva filens innehåll |
> | Action | Microsoft. data migration/Services/projekt/filer/skriva | Skapa eller uppdatera resurser och deras egenskaper |
> | Action | Microsoft. data migration/tjänster/projekt/läsa | Läs information om resurser |
> | Action | Microsoft. data migration/Services/Projects/tasks/Cancel/Action | Avbryt uppgiften om den körs |
> | Action | Microsoft. data migration/Services/Projects/tasks/Delete | En resursgrupp och alla dess underordnade tas bort. |
> | Action | Microsoft. data migration/Services/Projects/tasks/Read | Läs information om resurser |
> | Action | Microsoft. data migration/Services/Projects/tasks/Write | Kör Azure Database Migration Service-uppgifter |
> | Action | Microsoft. data migration/tjänster/projekt/skriva | Kör Azure Database Migration Service-uppgifter |
> | Action | Microsoft. data migration/Services/Read | Läs information om resurser |
> | Action | Microsoft. data migration/Services/removeWorker/Action | Tar bort en DMS-arbetare från tjänstens tillgänglig arbetare |
> | Action | Microsoft. data migration/Services/serviceTasks/Cancel/Action | Avbryt uppgiften om den körs |
> | Action | Microsoft. data migration/Services/serviceTasks/Delete | En resursgrupp och alla dess underordnade tas bort. |
> | Action | Microsoft. data migration/Services/serviceTasks/Read | Läs information om resurser |
> | Action | Microsoft. data migration/Services/serviceTasks/Write | Kör Azure Database Migration Service-uppgifter |
> | Action | Microsoft. data migration/Services/lots/Delete | En resursgrupp och alla dess underordnade tas bort. |
> | Action | Microsoft. data migration/tjänster/platser/läsa | Läs information om resurser |
> | Action | Microsoft. data migration/tjänster/platser/Skriv | Skapa eller uppdatera resurser och deras egenskaper |
> | Action | Microsoft. data migration/tjänster/start/åtgärd | Starta DMS-tjänsten så att den kan behandla migreringar igen |
> | Action | Microsoft. data migration/tjänster/stoppa/åtgärd | Stoppa DMS-tjänsten så att kostnaderna minimeras |
> | Action | Microsoft. data migration/Services/updateAgentConfig/Action | Uppdaterar DMS-agentens konfiguration med angivna värden. |
> | Action | Microsoft. data migration/Services/Write | Skapa eller uppdatera resurser och deras egenskaper |
> | Action | Microsoft.DataMigration/skus/read | Hämta en lista över SKU:er som stöds av DMS-resurser. |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. DBforMariaDB/checkNameAvailability/Action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Action | Microsoft. DBforMariaDB/locations/azureAsyncOperation/Read | Returnera MariaDB-Server åtgärds resultat |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera ResourceGroup-baserade MariaDB Server åtgärds resultat |
> | Action | Microsoft.DBforMariaDB/locations/operationResults/read | Returnera MariaDB-Server åtgärds resultat |
> | Action | Microsoft. DBforMariaDB/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Action | Microsoft. DBforMariaDB/locations/securityAlertPoliciesAzureAsyncOperation/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Action | Microsoft.DBforMariaDB/locations/securityAlertPoliciesOperationResults/read | Returnera en lista över resultat för Server hot identifiering. |
> | Action | Microsoft. DBforMariaDB/Operations/Read | Returnera listan över MariaDB-åtgärder. |
> | Action | Microsoft. DBforMariaDB/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Action | Microsoft.DBforMariaDB/register/action | Registrera MariaDB Resource Provider |
> | Action | Microsoft. DBforMariaDB/servers/administrators/Delete | Tar bort en befintlig administratör av MariaDB-servern. |
> | Action | Microsoft. DBforMariaDB/servers/administrators/Read | Hämtar en lista över MariaDB Server-administratörer. |
> | Action | Microsoft. DBforMariaDB/servers/administrators/Write | Skapar eller uppdaterar MariaDB Server-administratör med de angivna parametrarna. |
> | Action | Microsoft. DBforMariaDB/servrar/Advisor/createRecommendedActionSession/Action | Skapa en ny rekommendation för rekommendations åtgärd |
> | Action | Microsoft. DBforMariaDB/servrar/Advisor/Read | Returnera listan över rådgivare |
> | Action | Microsoft. DBforMariaDB/servrar/Advisor/Read | Returnera en rådgivare |
> | Action | Microsoft. DBforMariaDB/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Action | Microsoft. DBforMariaDB/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Action | Microsoft. DBforMariaDB/servrar/Advisor/recommendedActions/Read | Returnera en Rekommenderad åtgärd |
> | Action | Microsoft. DBforMariaDB/servrar/konfigurationer/läsa | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Action | Microsoft. DBforMariaDB/servrar/konfigurationer/Skriv | Uppdatera värdet för den angivna konfigurationen |
> | Action | Microsoft. DBforMariaDB/servrar/databaser/ta bort | Tar bort en befintlig MariaDB-databas. |
> | Action | Microsoft. DBforMariaDB/servrar/databaser/läsa | Returnera listan över MariaDB-databaser eller hämta egenskaperna för den angivna databasen. |
> | Action | Microsoft.DBforMariaDB/servers/databases/write | Skapar en MariaDB-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Action | Microsoft. DBforMariaDB/servers/Delete | Tar bort en befintlig server. |
> | Action | Microsoft. DBforMariaDB/servers/firewallRules/Delete | Tar bort en befintlig brand Väggs regel. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Action | Microsoft.DBforMariaDB/servers/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Action | Microsoft.DBforMariaDB/servers/logFiles/read | Returnera listan över MariaDB-loggfiler. |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft. DBforMariaDB/servers/providers/Microsoft. Insights/logDefinitions/Read | Hämtar de tillgängliga loggarna för MariaDB-servrar |
> | Action | Microsoft.DBforMariaDB/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texterna för en lista med frågor |
> | Action | Microsoft.DBforMariaDB/servers/queryTexts/action | Returnera texten för en fråga |
> | Action | Microsoft. DBforMariaDB/servrar/läsa | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Action | Microsoft.DBforMariaDB/servers/recoverableServers/read | Returnera information om återställnings bar MariaDB-Server |
> | Action | Microsoft. DBforMariaDB/servrar/repliker/läsa | Hämta Läs repliker av en MariaDB-Server |
> | Action | Microsoft. DBforMariaDB/-servrar/omstart/åtgärd | Startar om en speciell Server. |
> | Action | Microsoft. DBforMariaDB/servers/securityAlertPolicies/Read | Hämta information om den princip för Server hot identifiering som kon figurer ATS på en angiven server |
> | Action | Microsoft. DBforMariaDB/servers/securityAlertPolicies/Write | Ändra principen för Server hot identifiering för en specifik server |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Returnera listan över frågans statistik för de vanligaste frågorna. |
> | Action | Microsoft.DBforMariaDB/servers/topQueryStatistics/read | Returnera statistik för frågan |
> | Action | Microsoft.DBforMariaDB/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft.DBforMariaDB/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft. DBforMariaDB/servers/waitStatistics/Read | Returnera väntande statistik för en instans |
> | Action | Microsoft. DBforMariaDB/servers/waitStatistics/Read | Returnera en wait-statistik |
> | Action | Microsoft.DBforMariaDB/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. DBforMySQL/checkNameAvailability/Action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Action | Microsoft. DBforMySQL/locations/azureAsyncOperation/Read | Returnera åtgärds resultat för MySQL-Server |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Returnera ResourceGroup-baserade server åtgärds resultat för MySQL-Server |
> | Action | Microsoft.DBforMySQL/locations/operationResults/read | Returnera åtgärds resultat för MySQL-Server |
> | Action | Microsoft. DBforMySQL/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Action | Microsoft. DBforMySQL/locations/securityAlertPoliciesAzureAsyncOperation/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Action | Microsoft. DBforMySQL/locations/securityAlertPoliciesOperationResults/Read | Returnera en lista över resultat för Server hot identifiering. |
> | Action | Microsoft. DBforMySQL/Operations/Read | Returnera listan över MySQL-åtgärder. |
> | Action | Microsoft. DBforMySQL/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Action | Microsoft. DBforMySQL/register/åtgärd | Registrera MySQL Resource Provider |
> | Action | Microsoft. DBforMySQL/servers/administrators/Delete | Tar bort en befintlig administratör av MySQL-servern. |
> | Action | Microsoft. DBforMySQL/servers/administrators/Read | Hämtar en lista över MySQL Server-administratörer. |
> | Action | Microsoft. DBforMySQL/servers/administrators/Write | Skapar eller uppdaterar MySQL Server-administratören med de angivna parametrarna. |
> | Action | Microsoft. DBforMySQL/servrar/Advisor/createRecommendedActionSession/Action | Skapa en ny rekommendation för rekommendations åtgärd |
> | Action | Microsoft. DBforMySQL/servrar/Advisor/Read | Returnera listan över rådgivare |
> | Action | Microsoft. DBforMySQL/servrar/Advisor/Read | Returnera en rådgivare |
> | Action | Microsoft. DBforMySQL/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Action | Microsoft. DBforMySQL/servrar/Advisor/recommendedActions/Read | Returnera listan över rekommenderade åtgärder |
> | Action | Microsoft. DBforMySQL/servrar/Advisor/recommendedActions/Read | Returnera en Rekommenderad åtgärd |
> | Action | Microsoft. DBforMySQL/servrar/konfigurationer/läsa | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Action | Microsoft. DBforMySQL/servrar/konfigurationer/Skriv | Uppdatera värdet för den angivna konfigurationen |
> | Action | Microsoft. DBforMySQL/servrar/databaser/ta bort | Tar bort en befintlig MySQL-databas. |
> | Action | Microsoft. DBforMySQL/servrar/databaser/läsa | Returnera listan över MySQL-databaser eller hämtar egenskaperna för den angivna databasen. |
> | Action | Microsoft. DBforMySQL/servrar/databaser/skriva | Skapar en MySQL-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Action | Microsoft. DBforMySQL/servers/Delete | Tar bort en befintlig server. |
> | Action | Microsoft. DBforMySQL/servers/firewallRules/Delete | Tar bort en befintlig brand Väggs regel. |
> | Action | Microsoft. DBforMySQL/servers/firewallRules/Read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Action | Microsoft. DBforMySQL/servers/firewallRules/Write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Action | Microsoft.DBforMySQL/servers/logFiles/read | Returnera listan över PostgreSQL-loggfiler. |
> | Action | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar disagnostic-inställningen för resursen |
> | Action | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/logDefinitions/Read | Hämtar tillgängliga loggar för MySQL-servrar |
> | Action | Microsoft. DBforMySQL/servers/providers/Microsoft. Insights/metricDefinitions/Read | Returnera typer av mått som är tillgängliga för databaser |
> | Action | Microsoft. DBforMySQL/servers/queryTexts/åtgärd | Returnera texterna för en lista med frågor |
> | Action | Microsoft. DBforMySQL/servers/queryTexts/åtgärd | Returnera texten för en fråga |
> | Action | Microsoft. DBforMySQL/servrar/läsa | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Action | Microsoft. DBforMySQL/servers/recoverableServers/Read | Returnera information om återställnings bara MySQL-Server |
> | Action | Microsoft. DBforMySQL/servrar/repliker/läsa | Hämta Läs repliker av en MySQL-Server |
> | Action | Microsoft. DBforMySQL/-servrar/omstart/åtgärd | Startar om en speciell Server. |
> | Action | Microsoft. DBforMySQL/servers/securityAlertPolicies/Read | Hämta information om den princip för Server hot identifiering som kon figurer ATS på en angiven server |
> | Action | Microsoft. DBforMySQL/servers/securityAlertPolicies/Write | Ändra principen för Server hot identifiering för en specifik server |
> | Action | Microsoft. DBforMySQL/servers/topQueryStatistics/Read | Returnera listan över frågans statistik för de vanligaste frågorna. |
> | Action | Microsoft. DBforMySQL/servers/topQueryStatistics/Read | Returnera statistik för frågan |
> | Action | Microsoft. DBforMySQL/servers/updateConfigurations/åtgärd | Uppdatera konfigurationer för den angivna servern |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft.DBforMySQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft. DBforMySQL/servers/waitStatistics/Read | Returnera väntande statistik för en instans |
> | Action | Microsoft. DBforMySQL/servers/waitStatistics/Read | Returnera en wait-statistik |
> | Action | Microsoft. DBforMySQL/servrar/Skriv | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DBforPostgreSQL/checkNameAvailability/action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Action | Microsoft.DBforPostgreSQL/locations/azureAsyncOperation/read | Returnera PostgreSQL-Server åtgärds resultat |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Returnera ResourceGroup-baserade PostgreSQL Server åtgärds resultat |
> | Action | Microsoft.DBforPostgreSQL/locations/operationResults/read | Returnera PostgreSQL-Server åtgärds resultat |
> | Action | Microsoft. DBforPostgreSQL/locations/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Action | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionAzureAsyncOperation/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Action | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionOperationResults/Read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Action | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Action | Microsoft. DBforPostgreSQL/locations/privateEndpointConnectionProxyOperationResults/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesAzureAsyncOperation/read | Returnera en lista över resultat för Server hot identifiering. |
> | Action | Microsoft.DBforPostgreSQL/locations/securityAlertPoliciesOperationResults/read | Returnera en lista över resultat för Server hot identifiering. |
> | Action | Microsoft.DBforPostgreSQL/operations/read | Returnera listan över PostgreSQL-åtgärder. |
> | Action | Microsoft. DBforPostgreSQL/performanceTiers/Read | Returnerar listan med tillgängliga prestanda nivåer. |
> | Action | Microsoft.DBforPostgreSQL/register/action | Registrera PostgreSQL Resource Provider |
> | Action | Microsoft. DBforPostgreSQL/servers/administrators/Delete | Tar bort en befintlig administratör av PostgreSQL-servern. |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/read | Hämtar en lista över PostgreSQL Server-administratörer. |
> | Action | Microsoft.DBforPostgreSQL/servers/administrators/write | Skapar eller uppdaterar PostgreSQL Server-administratör med de angivna parametrarna. |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/read | Returnera listan över rådgivare |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActions/read | Returnera listan över rekommenderade åtgärder |
> | Action | Microsoft.DBforPostgreSQL/servers/advisors/recommendedActionSessions/action | Gör rekommendationer |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/read | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Action | Microsoft.DBforPostgreSQL/servers/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/delete | Tar bort en befintlig PostgreSQL-databas. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/read | Returnera listan över PostgreSQL-databaser eller hämta egenskaperna för den angivna databasen. |
> | Action | Microsoft.DBforPostgreSQL/servers/databases/write | Skapar en PostgreSQL-databas med de angivna parametrarna eller uppdaterar egenskaperna för den angivna databasen. |
> | Action | Microsoft.DBforPostgreSQL/servers/delete | Tar bort en befintlig server. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/delete | Tar bort en befintlig brand Väggs regel. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Action | Microsoft.DBforPostgreSQL/servers/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Action | Microsoft.DBforPostgreSQL/servers/logFiles/read | Returnera listan över PostgreSQL-loggfiler. |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Delete | Tar bort en befintlig anslutning till en privat slut punkt |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Read | Returnerar listan över anslutnings-proxy för privata slut punkter eller hämtar egenskaperna för den angivna anslutningen till den privata slut punkten. |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/validate/åtgärd | Verifierar en privat slut punkts anslutning skapa samtal från NRP sida |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnectionProxies/Write | Skapar en anslutning till en privat slutpunkt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna proxyn för privat slut punkt anslutning. |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Delete | Tar bort en befintlig privat slut punkts anslutning |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Read | Returnerar listan över anslutningar för privata slut punkter eller hämtar egenskaperna för den angivna privata slut punkts anslutningen. |
> | Action | Microsoft. DBforPostgreSQL/servers/privateEndpointConnections/Write | Godkänner eller avvisar en befintlig privat slut punkts anslutning |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för postgres-servrar |
> | Action | Microsoft.DBforPostgreSQL/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/action | Returnera texten för en fråga |
> | Action | Microsoft.DBforPostgreSQL/servers/queryTexts/read | Returnera texterna för en lista med frågor |
> | Action | Microsoft.DBforPostgreSQL/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Action | Microsoft.DBforPostgreSQL/servers/recoverableServers/read | Returnera information om återställnings bar PostgreSQL-Server |
> | Action | Microsoft.DBforPostgreSQL/servers/replicas/read | Hämta Läs repliker av en PostgreSQL-Server |
> | Action | Microsoft.DBforPostgreSQL/servers/restart/action | Startar om en speciell Server. |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/read | Hämta information om den princip för Server hot identifiering som kon figurer ATS på en angiven server |
> | Action | Microsoft.DBforPostgreSQL/servers/securityAlertPolicies/write | Ändra principen för Server hot identifiering för en specifik server |
> | Action | Microsoft.DBforPostgreSQL/servers/topQueryStatistics/read | Returnera listan över frågans statistik för de vanligaste frågorna. |
> | Action | Microsoft.DBforPostgreSQL/servers/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft.DBforPostgreSQL/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft.DBforPostgreSQL/servers/waitStatistics/read | Returnera väntande statistik för en instans |
> | Action | Microsoft.DBforPostgreSQL/servers/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/read | Returnera listan över konfigurationer för en server eller hämtar egenskaperna för den angivna konfigurationen. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/configurations/write | Uppdatera värdet för den angivna konfigurationen |
> | Action | Microsoft.DBforPostgreSQL/serversv2/delete | Tar bort en befintlig server. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/delete | Tar bort en befintlig brand Väggs regel. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/read | Returnera listan över brand Väggs regler för en server eller hämtar egenskaperna för den angivna brand Väggs regeln. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/firewallRules/write | Skapar en brand Väggs regel med de angivna parametrarna eller uppdaterar en befintlig regel. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar disagnostic-inställningen för resursen |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för postgres-servrar |
> | Action | Microsoft.DBforPostgreSQL/serversv2/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Action | Microsoft.DBforPostgreSQL/serversv2/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Action | Microsoft.DBforPostgreSQL/serversv2/updateConfigurations/action | Uppdatera konfigurationer för den angivna servern |
> | Action | Microsoft.DBforPostgreSQL/serversv2/write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Devices/checkNameAvailability/åtgärd | Kontrol lera om IotHub namn är tillgängligt |
> | Action | Microsoft.Devices/checkProvisioningServiceNameAvailability/Action | Kontrol lera om tjänst namnet för etablering är tillgängligt |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/ElasticPools/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Delete | Tar bort certifikat |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/generateVerificationCode/Action | Generera verifierings kod |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Read | Hämtar certifikatet |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/verify/Action | Verifiera certifikat resurs |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/certificates/Write | Skapa eller uppdatera certifikat |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Delete | Ta bort IotHub-klient resursen |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete | Ta bort EventHub-konsument grupp |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read | Hämta en EventHub-konsument grupp (er) |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write | Skapa en EventHub-konsument grupp |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/exportDevices/Action | Exportera enheter |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/getStats/Read | Hämtar IotHub-resursen för klient statistik |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/importDevices/Action | Importera enheter |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action | Hämtar IotHub klient nyckel |
> | Action | Microsoft. Devices/elasticPools/iotHubTenants/Jobs/Read | Hämta jobb information som skickats till den aktuella IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/listKeys/Action | Hämtar IotHub klient nycklar |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/logDefinitions/read | Hämtar de tillgängliga logg definitionerna för IotHub-tjänsten |
> | Action | Microsoft.Devices/ElasticPools/IotHubTenants/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/quotaMetrics/Read | Hämta kvot mått |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Read | Hämtar klient organisations resursen för IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testall/Action | Testa ett meddelande mot alla befintliga vägar |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routing/routes/$testnew/Action | Testa ett meddelande mot en angiven test väg |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/routingEndpointsHealth/Read | Hämtar hälso tillståndet för alla Dirigerings slut punkter för en IotHub |
> | Action | Microsoft.Devices/elasticPools/iotHubTenants/Write | Skapa eller uppdatera klient organisations resursen för IotHub |
> | Action | Microsoft.Devices/ElasticPools/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Action | Microsoft.Devices/iotHubs/certificates/Delete | Tar bort certifikat |
> | Action | Microsoft.Devices/iotHubs/certificates/generateVerificationCode/Action | Generera verifierings kod |
> | Action | Microsoft. Devices/iotHubs/certificates/Read | Hämtar certifikatet |
> | Action | Microsoft. Devices/iotHubs/certificates/verify/Action | Verifiera certifikat resurs |
> | Action | Microsoft. Devices/iotHubs/certificates/Write | Skapa eller uppdatera certifikat |
> | Action | Microsoft.Devices/iotHubs/Delete | Ta bort IotHub-resurs |
> | Action | Microsoft. Devices/IotHubs/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft. Devices/IotHubs/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Delete | Tar bort Event Grids filtret |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Read | Hämtar Event Grids filtret |
> | Action | Microsoft.Devices/iotHubs/eventGridFilters/Write | Skapa nytt eller uppdatera befintligt Event Grid filter |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Delete | Ta bort EventHub-konsument grupp |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Read | Hämta en EventHub-konsument grupp (er) |
> | Action | Microsoft.Devices/iotHubs/eventHubEndpoints/consumerGroups/Write | Skapa en EventHub-konsument grupp |
> | Action | Microsoft.Devices/iotHubs/exportDevices/Action | Exportera enheter |
> | Action | Microsoft. Devices/iotHubs/importDevices/Action | Importera enheter |
> | Action | Microsoft. Devices/iotHubs/iotHubKeys/listnycklar/Action | Hämta IotHub-nyckeln för det aktuella namnet |
> | Action | Microsoft.Devices/iotHubs/iotHubStats/Read | Hämta IotHub-statistik |
> | Action | Microsoft. Devices/iotHubs/Jobs/Read | Hämta jobb information som skickats till den aktuella IotHub |
> | Action | Microsoft. Devices/iotHubs/listnycklar/Action | Hämta alla IotHub-nycklar |
> | Action | Microsoft.Devices/IotHubs/logDefinitions/read | Hämtar de tillgängliga logg definitionerna för IotHub-tjänsten |
> | Action | Microsoft.Devices/IotHubs/metricDefinitions/read | Hämtar tillgängliga mått för IotHub-tjänsten |
> | Action | Microsoft. Devices/iotHubs/operationresults/Read | Hämta åtgärds resultat (föråldrat API) |
> | Action | Microsoft. Devices/iotHubs/quotaMetrics/Read | Hämta kvot mått |
> | Action | Microsoft. Devices/iotHubs/Read | Hämtar IotHub-resurs (er) |
> | Action | Microsoft. Devices/iotHubs/routing/$testall/Action | Testa ett meddelande mot alla befintliga vägar |
> | Action | Microsoft. Devices/iotHubs/routing/$testnew/Action | Testa ett meddelande mot en angiven test väg |
> | Action | Microsoft.Devices/iotHubs/routingEndpointsHealth/Read | Hämtar hälso tillståndet för alla Dirigerings slut punkter för en IotHub |
> | Action | Microsoft.Devices/iotHubs/skus/Read | Hämta giltiga IotHub SKU: er |
> | Action | Microsoft. Devices/iotHubs/Write | Skapa eller uppdatera IotHub-resurs |
> | Action | Microsoft. Devices/locations/operationresults/Read | Resultat av hämtning av plats baserad åtgärd |
> | Action | Microsoft. Devices/operationresults/Read | Hämta åtgärdsresultat |
> | Action | Microsoft. Devices/Operations/Read | Hämta alla ResourceProvider-åtgärder |
> | Action | Microsoft. Devices/provisioningServices/certificates/Delete | Tar bort certifikat |
> | Action | Microsoft.Devices/provisioningServices/certificates/generateVerificationCode/Action | Generera verifierings kod |
> | Action | Microsoft. Devices/provisioningServices/certificates/Read | Hämtar certifikatet |
> | Action | Microsoft. Devices/provisioningServices/certificates/verify/Action | Verifiera certifikat resurs |
> | Action | Microsoft. Devices/provisioningServices/certificates/Write | Skapa eller uppdatera certifikat |
> | Action | Microsoft.Devices/provisioningServices/Delete | Ta bort IotDps-resurs |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/provisioningServices/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Devices/provisioningServices/keys/listkeys/Action | Hämta IotDps-nycklar för nyckel namn |
> | Action | Microsoft. Devices/provisioningServices/listnycklar/Action | Hämta alla IotDps-nycklar |
> | Action | Microsoft.Devices/provisioningServices/logDefinitions/read | Hämtar de tillgängliga logg definitionerna för etablerings tjänsten |
> | Action | Microsoft.Devices/provisioningServices/metricDefinitions/read | Hämtar tillgängliga mått för etablerings tjänsten |
> | Action | Microsoft. Devices/provisioningServices/operationresults/Read | Hämta resultat från DPS-åtgärd |
> | Action | Microsoft. Devices/provisioningServices/Read | Hämta IotDps-resurs |
> | Action | Microsoft.Devices/provisioningServices/skus/Read | Hämta giltiga IotDps SKU: er |
> | Action | Microsoft. Devices/provisioningServices/Write | Skapa IotDps-resurs |
> | Action | Microsoft. Devices/register/åtgärd | Registrera prenumerationen för IotHub Resource Provider och gör det möjligt att skapa IotHub-resurser |
> | Action | Microsoft. Devices/register/åtgärd | Registrera prenumerationen för IotHub Resource Provider och gör det möjligt att skapa IotHub-resurser |
> | Action | Microsoft. Devices/usages/Read | Hämta information om prenumerations användning för den här providern. |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. DevSpaces/controllers/Delete | Ta bort Azure dev Spaces-kontroller och dataplanen-tjänster |
> | Action | Microsoft.DevSpaces/controllers/listConnectionDetails/action | Lista över anslutnings information för Azure dev Spaces-infrastrukturen |
> | Action | Microsoft.DevSpaces/controllers/read | Läs egenskaper för Azure dev Spaces-kontrollant |
> | Action | Microsoft.DevSpaces/controllers/write | Skapa eller uppdatera egenskaper för Azure dev Spaces-kontrollant |
> | Action | Microsoft.DevSpaces/locations/checkContainerHostMapping/action | Kontrol lera befintlig styrenhets mappning för en behållar värd |
> | Action | Microsoft.DevSpaces/locations/operationresults/read | Läs status för en asynkron åtgärd |
> | Action | Microsoft.DevSpaces/register/action | Registrera Microsoft dev Spaces Resource Provider med en prenumeration |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DevTestLab/labCenters/delete | Ta bort labb Center. |
> | Action | Microsoft.DevTestLab/labCenters/read | Läs labb Center. |
> | Action | Microsoft.DevTestLab/labCenters/write | Lägg till eller ändra labb Center. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/armTemplates/read | Läs Azure Resource Manager-mallar. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/artifacts/GenerateArmTemplate/action | Genererar en ARM-mall för den aktuella artefakten, överför de filer som krävs till ett lagrings konto och verifierar den genererade artefakten. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/artefakter/läsning | Läsa artefakter. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/Delete | Ta bort artefakt källor. |
> | Action | Microsoft. DevTestLab/Labs/artifactSources/Read | Läsa artefakt källor. |
> | Action | Microsoft.DevTestLab/labs/artifactSources/write | Lägg till eller ändra artefakt källor. |
> | Action | Microsoft.DevTestLab/labs/ClaimAnyVm/action | Anspråk på en slumpmässig virtuell dator i labbet. |
> | Action | Microsoft.DevTestLab/labs/costs/read | Läs kostnader. |
> | Action | Microsoft.DevTestLab/labs/costs/write | Lägg till eller ändra kostnader. |
> | Action | Microsoft.DevTestLab/labs/CreateEnvironment/action | Skapa virtuella datorer i ett labb. |
> | Action | Microsoft. DevTestLab/Labs/customImages/Delete | Ta bort anpassade avbildningar. |
> | Action | Microsoft. DevTestLab/Labs/customImages/Read | Läs anpassade avbildningar. |
> | Action | Microsoft. DevTestLab/Labs/customImages/Write | Lägg till eller ändra anpassade avbildningar. |
> | Action | Microsoft.DevTestLab/labs/delete | Ta bort labb. |
> | Action | Microsoft.DevTestLab/labs/EnsureCurrentUserProfile/action | Se till att den aktuella användaren har en giltig profil i labbet. |
> | Action | Microsoft. DevTestLab/labb/ExportResourceUsage/åtgärd | Exporterar labb resurs användningen till ett lagrings konto |
> | Action | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Action | Microsoft.DevTestLab/labs/formulas/read | Läs formler. |
> | Action | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Action | Microsoft.DevTestLab/labs/galleryImages/read | Läsa Galleri bilder. |
> | Action | Microsoft. DevTestLab/labb/GenerateUploadUri/åtgärd | Generera en URI för att ladda upp anpassade disk avbildningar till ett labb. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/delete | Ta bort inaktiva avstängning. |
> | Action | Microsoft. DevTestLab/Labs/idleShutdowns/Read | Läs inaktiva avstängning. |
> | Action | Microsoft.DevTestLab/labs/idleShutdowns/write | Lägg till eller ändra inaktiva avstängningar. |
> | Action | Microsoft.DevTestLab/labs/ImportVirtualMachine/action | Importera en virtuell dator till ett annat labb. |
> | Action | Microsoft.DevTestLab/labs/ListVhds/action | Lista tillgängliga disk avbildningar för att skapa en anpassad avbildning. |
> | Action | Microsoft. DevTestLab/Labs/notificationChannels/Delete | Ta bort notificationchannels. |
> | Action | Microsoft. DevTestLab/Labs/notificationChannels/notify/åtgärd | Skicka meddelande till angiven kanal. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/read | Läs notificationchannels. |
> | Action | Microsoft.DevTestLab/labs/notificationChannels/write | Lägg till eller ändra notificationchannels. |
> | Action | Microsoft.DevTestLab/labs/policySets/EvaluatePolicies/action | Utvärderar labb princip. |
> | Action | Microsoft. DevTestLab/Labs/policySets/policies/Delete | Ta bort principer. |
> | Action | Microsoft. DevTestLab/Labs/policySets/policies/Read | Läs principer. |
> | Action | Microsoft. DevTestLab/Labs/policySets/policies/Write | Lägg till eller ändra principer. |
> | Action | Microsoft. DevTestLab/Labs/policySets/Read | Läs princip uppsättningar. |
> | Action | Microsoft.DevTestLab/labs/read | Läs labb. |
> | Action | Microsoft.DevTestLab/labs/schedules/delete | Ta bort scheman. |
> | Action | Microsoft. DevTestLab/labb/scheman/kör/åtgärd | Kör ett schema. |
> | Action | Microsoft.DevTestLab/labs/schedules/ListApplicable/action | Visar alla tillämpliga scheman |
> | Action | Microsoft.DevTestLab/labs/schedules/read | Läs scheman. |
> | Action | Microsoft.DevTestLab/labs/schedules/write | Lägg till eller ändra scheman. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/delete | Ta bort tjänst löpare. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/read | Läsa tjänst löpare. |
> | Action | Microsoft.DevTestLab/labs/serviceRunners/write | Lägg till eller ändra tjänst löpare. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/delete | Ta bort delade gallerier. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/read | Läsa delade gallerier. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/delete | Ta bort delade avbildningar. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/read | Läs delade avbildningar. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/sharedImages/write | Lägg till eller ändra delade avbildningar. |
> | Action | Microsoft.DevTestLab/labs/sharedGalleries/write | Lägg till eller ändra delade gallerier. |
> | Action | Microsoft.DevTestLab/labs/users/delete | Ta bort användar profiler. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Attach/action | Anslut och skapa lånet av disken till den virtuella datorn. |
> | Action | Microsoft.DevTestLab/labs/users/disks/delete | Ta bort diskar. |
> | Action | Microsoft.DevTestLab/labs/users/disks/Detach/action | Koppla från och Bryt lånet för den disk som är ansluten till den virtuella datorn. |
> | Action | Microsoft.DevTestLab/labs/users/disks/read | Läs diskar. |
> | Action | Microsoft.DevTestLab/labs/users/disks/write | Lägg till eller ändra diskar. |
> | Action | Microsoft.DevTestLab/labs/users/environments/delete | Ta bort miljöer. |
> | Action | Microsoft.DevTestLab/labs/users/environments/read | Läs miljöer. |
> | Action | Microsoft.DevTestLab/labs/users/environments/write | Lägg till eller ändra miljöer. |
> | Action | Microsoft.DevTestLab/labs/users/read | Läs användar profiler. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/delete | Ta bort hemligheter. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/read | Läs hemligheter. |
> | Action | Microsoft.DevTestLab/labs/users/secrets/write | Lägg till eller ändra hemligheter. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/delete | Ta bort Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/ListApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/read | Läs Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/delete | Ta bort scheman. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/Execute/action | Kör ett schema. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/read | Läs scheman. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/schedules/write | Lägg till eller ändra scheman. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Start/action | Starta en Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/Stop/action | Stoppa en Service Fabric |
> | Action | Microsoft.DevTestLab/labs/users/serviceFabrics/write | Lägg till eller ändra Service Fabric. |
> | Action | Microsoft.DevTestLab/labs/users/write | Lägg till eller ändra användar profiler. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/AddDataDisk/action | Koppla en ny eller befintlig datadisk till den virtuella datorn. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ApplyArtifacts/action | Tillämpa artefakter på den virtuella datorn. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Claim/action | Bli ägare till en befintlig virtuell dator |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/delete | Ta bort virtuella datorer. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/DetachDataDisk/action | Koppla från den angivna disken från den virtuella datorn. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/GetRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/ListApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/read | Läsa virtuella datorer. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Redeploy/action | Distribuera om en virtuell dator |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Resize/action | Ändra storlek på virtuell dator. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Restart/action | Starta om en virtuell dator. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/delete | Ta bort scheman. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/Execute/action | Kör ett schema. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/read | Läs scheman. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/schedules/write | Lägg till eller ändra scheman. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Start/action | Starta en virtuell dator. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/Stop/action | Stoppa en virtuell dator |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/TransferDisks/action | Överför alla data diskar som är anslutna till den virtuella datorn och ägs av den aktuella användaren. |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/UnClaim/action | Frigör ägarskap för en befintlig virtuell dator |
> | Action | Microsoft.DevTestLab/labs/virtualMachines/write | Lägg till eller ändra virtuella datorer. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/delete | Ta bort virtuella nätverk. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/read | Läsa virtuella nätverk. |
> | Action | Microsoft.DevTestLab/labs/virtualNetworks/write | Lägg till eller ändra virtuella nätverk. |
> | Action | Microsoft.DevTestLab/labs/vmPools/delete | Ta bort pooler för virtuella datorer. |
> | Action | Microsoft.DevTestLab/labs/vmPools/read | Läs virtuella dator pooler. |
> | Action | Microsoft.DevTestLab/labs/vmPools/write | Lägg till eller ändra pooler för virtuella datorer. |
> | Action | Microsoft.DevTestLab/labs/write | Lägg till eller ändra labb. |
> | Action | Microsoft.DevTestLab/locations/operations/read | Läs åtgärder. |
> | Action | Microsoft.DevTestLab/register/action | Registrerar prenumerationen |
> | Action | Microsoft.DevTestLab/schedules/delete | Ta bort scheman. |
> | Action | Microsoft. DevTestLab/Schemalägg/kör/åtgärd | Kör ett schema. |
> | Action | Microsoft.DevTestLab/schedules/read | Läs scheman. |
> | Action | Microsoft. DevTestLab/schemalägger/ommål/åtgärd | Uppdaterar ett schemas mål resurs-ID. |
> | Action | Microsoft.DevTestLab/schedules/write | Lägg till eller ändra scheman. |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DocumentDB/databaseAccountNames/read | Söker efter namn tillgänglighet. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/ta bort | Ta bort en samling. Gäller endast för API-typer: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' MongoDB '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/läsa | Läs en samling eller lista alla samlingar. Gäller endast för API-typer: ' MongoDB '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/databases/collections/settings/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' MongoDB '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/inställningar/läsa | Läs en samlings data flöde. Gäller endast för API-typer: ' MongoDB '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/inställningar/Skriv | Uppdatera ett data flöde för samlingen. Gäller endast för API-typer: ' MongoDB '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/samlingar/skriva | Skapa eller uppdatera en samling. Gäller endast för API-typer: ' MongoDB '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/ta bort | Ta bort en behållare. Gäller endast för API-typer: SQL. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/läsa | Läs en behållare eller lista alla behållare. Gäller endast för API-typer: SQL. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/behållare/inställningar/läsa | Läs ett data flöde för behållare. Gäller endast för API-typer: SQL. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/inställningar/Skriv | Uppdatera ett data flöde för behållare. Gäller endast för API-typer: SQL. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/behållare/skriva | Skapa eller uppdatera en behållare. Gäller endast för API-typer: SQL. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/ta bort | Ta bort en databas. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/Delete | Ta bort ett diagram. Gäller endast för API-typer: ' gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/graf/läsa | Läs ett diagram eller Visa en lista över alla grafer. Gäller endast för API-typer: ' gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/läsa | Läsa ett diagram genom strömning. Gäller endast för API-typer: ' gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/grafer/inställningar/Skriv | Uppdatera ett diagram genom strömning. Gäller endast för API-typer: ' gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/diagram/skriva | Skapa eller uppdatera ett diagram. Gäller endast för API-typer: ' gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/läsa | Läs en databas eller lista alla databaser. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: SQL, MongoDB, gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/inställningar/läsa | Läs en databas genom strömning. Gäller endast för API-typer: SQL, MongoDB, gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/databaser/inställningar/Skriv | Uppdatera ett databas data flöde. Gäller endast för API-typer: SQL, MongoDB, gremlin'. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/databaser/skriva | Skapa en databas. Gäller endast för API-typer: SQL, MongoDB, gremlin'. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/Backsteg/Delete | Ta bort ett blank steg. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/disk utrymme/läsa | Läs ett tecken utrymme eller Visa en lista med alla blank steg. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft.DocumentDB/databaseAccounts/apis/keyspaces/settings/operationResults/read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/inställningar/läsa | Läs ett data flöde för genom strömning. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/inställningar/Skriv | Uppdatera ett flöde för genom strömning. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/ta bort | Ta bort en tabell. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/Backsteg/tables/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/läsa | Läs en tabell eller lista alla tabeller. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/inställningar/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/inställningar/läsa | Läs tabell data flöde. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disk utrymme/tabeller/inställningar/Skriv | Uppdatera ett tabell data flöde. Gäller endast för API-typer: ' Cassandra '. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/disks/tabeller/skrivning | Skapa eller uppdatera en tabell. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API: er/disk utrymme/skrivning | Skapa ett blank steg. Gäller endast för API-typer: ' Cassandra '. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/tables/Delete | Ta bort en tabell. Gäller endast för API-typer: tabell. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/tables/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: tabell. |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/tables/Read | Läs en tabell eller lista alla tabeller. Gäller endast för API-typer: tabell. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/tables/Settings/operationResults/Read | Läs status för den asynkrona åtgärden. Gäller endast för API-typer: tabell. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/Apis/tables/Settings/Read | Läs tabell data flöde. Gäller endast för API-typer: tabell. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/tables/Settings/Write | Uppdatera ett tabell data flöde. Gäller endast för API-typer: tabell. Endast tillämpligt för inställnings typer: "genomflödet". |
> | Action | Microsoft. DocumentDB/databaseAccounts/API/tables/Write | Skapa eller uppdatera en tabell. Gäller endast för API-typer: tabell. |
> | Action | Microsoft. DocumentDB/databaseAccounts/säkerhets kopiering/åtgärd | Skicka en begäran om att konfigurera säkerhets kopiering |
> | Action | Microsoft.DocumentDB/databaseAccounts/changeResourceGroup/action | Ändra resurs grupp för ett databas konto |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/metricDefinitions/read | Läser mått definitionerna för samlingen. |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/mått/läsa | Läser samlings måtten. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read | Läs databas kontots partitionerings nyckel nivå mått |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/partitioner/mått/läsa | Läs mått för partitions nivå för databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/partitioner/läsa | Läs databas konto partitioner i en samling |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/partitioner/användningar/läsa | Läs databas kontots partitionerings nivå användning |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/samlingar/användningar/läsa | Läser samlings användningen. |
> | Action | Microsoft.DocumentDB/databaseAccounts/databases/metricDefinitions/read | Läser mått definitionerna för databasen |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/Metrics/Read | Läser databas måtten. |
> | Action | Microsoft. DocumentDB/databaseAccounts/databaser/användning/läsa | Läser databas användningarna. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Delete | Tar bort databas kontona. |
> | Action | Microsoft. DocumentDB/databaseAccounts/failoverPriorityChange/Action | Ändra prioritet för redundans för regioner i ett databas konto. Detta används för att utföra manuell redundansväxling |
> | Action | Microsoft. DocumentDB/databaseAccounts/getBackupPolicy/Action | Hämta säkerhets kopierings principen för databas kontot |
> | Action | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/action | Hämta anslutnings strängar för ett databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/Listnycklar/Action | Lista nycklar för ett databas konto |
> | Action | Microsoft.DocumentDB/databaseAccounts/metricDefinitions/read | Läser definitionerna för databas konto mått. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Metrics/Read | Läser databas konto måtten. |
> | Action | Microsoft. DocumentDB/databaseAccounts/offlineRegion/Action | Offline en region i ett databas konto.  |
> | Action | Microsoft. DocumentDB/databaseAccounts/onlineRegion/Action | Online en region i ett databas konto. |
> | Action | Microsoft. DocumentDB/databaseAccounts/operationResults/Read | Läs status för den asynkrona åtgärden |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/Metrics/Read | Läs svars tids mått |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/läsa | Läs percentiler av replikeringsfördröjning |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/sourceRegion/targetRegion/Metrics/Read | Läs fördröjnings mått för en bestämd käll-och mål region |
> | Action | Microsoft. DocumentDB/databaseAccounts/percentil/targetRegion/Metrics/Read | Läs svars tids mått för en speciell mål region |
> | Action | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Delete | Ta bort en anslutning till en privat slutpunkt för databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Read | Läs en anslutning till en privat slutpunkt för anslutnings-proxy för databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/validate/åtgärd | Verifiera en anslutning till en privat slutpunkt för anslutnings proxy för databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/privateEndpointConnectionProxies/Write | Skapa eller uppdatera en privat slut punkts anslutnings proxy för databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/Read | Läser ett databas konto. |
> | Action | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databas kontots ReadOnly-nycklar. |
> | Action | Microsoft. DocumentDB/databaseAccounts/readonlykeys/Read | Läser databas kontots ReadOnly-nycklar. |
> | Action | Microsoft. DocumentDB/databaseAccounts/regenerateKey/Action | Rotera nycklar för ett databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/databaser/samlingar/mått/läsa | Läser de regionala samlings måtten. |
> | Action | Microsoft.DocumentDB/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read | Läs nationella databas kontons partitionerings nyckel nivå mått |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/databaser/samlingar/partitioner/mått/läsa | Läs mått för partitions nivå för regional databas konto |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/databaser/samlingar/partitioner/läsa | Läs regionala databas konto partitioner i en samling |
> | Action | Microsoft. DocumentDB/databaseAccounts/region/Metrics/Read | Läser regions-och databas konto mått. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Skicka en begäran om återställning |
> | Action | Microsoft. DocumentDB/databaseAccounts/Usage/Read | Läser databas kontots användningar. |
> | Action | Microsoft. DocumentDB/databaseAccounts/Write | Uppdatera ett databas konto. |
> | Action | Microsoft. DocumentDB/locations/deleteVirtualNetworkOrSubnets/Action | Meddelar Microsoft. DocumentDB att VirtualNetwork eller undernät tas bort |
> | Action | Microsoft.DocumentDB/locations/deleteVirtualNetworkOrSubnets/operationResults/read | Läs status för asynkron åtgärd för deleteVirtualNetworkOrSubnets |
> | Action | Microsoft.DocumentDB/locations/operationsStatus/read | Läser status för asynkrona åtgärder |
> | Action | Microsoft.DocumentDB/operationResults/read | Läs status för den asynkrona åtgärden |
> | Action | Microsoft. DocumentDB/Operations/Read | Läs åtgärder som är tillgängliga för Microsoft-DocumentDB  |
> | Action | Microsoft. DocumentDB/register/åtgärd |  Registrera Microsoft DocumentDB Resource Provider för prenumerationen |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.DomainRegistration/checkDomainAvailability/Action | Kontrol lera om en domän är tillgänglig för köp |
> | Action | Microsoft.DomainRegistration/domains/Delete | Ta bort en befintlig domän. |
> | Action | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Delete | Ta bort ägarskaps identifierare |
> | Action | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Read | Lista ägarskaps identifierare |
> | Action | Microsoft. DomainRegistration/Domains/domainownershipidentifiers/Read | Hämta ägarskaps identifierare |
> | Action | Microsoft.DomainRegistration/domains/domainownershipidentifiers/Write | Skapa eller uppdatera identifierare |
> | Action | Microsoft.DomainRegistration/domains/operationresults/Read | Hämta en domän åtgärd |
> | Action | Microsoft. DomainRegistration/Domains/Read | Hämta listan över domäner |
> | Action | Microsoft. DomainRegistration/Domains/Read | Hämta domän |
> | Action | Microsoft. DomainRegistration/Domains/renew/åtgärd | Förnya en befintlig domän. |
> | Action | Microsoft.DomainRegistration/domains/Write | Lägg till en ny domän eller uppdatera en befintlig |
> | Action | Microsoft.DomainRegistration/generateSsoRequest/Action | Generera en begäran om signering till kontroll Center för domän. |
> | Action | Microsoft.DomainRegistration/listDomainRecommendations/Action | Hämta listan med domän rekommendationer baserat på nyckelord |
> | Action | Microsoft.DomainRegistration/operations/Read | Visa en lista med alla åtgärder från App Service Domain Registration |
> | Action | Microsoft.DomainRegistration/register/action | Registrera resurs leverantören för Microsoft-domäner för prenumerationen |
> | Action | Microsoft.DomainRegistration/topLevelDomains/listAgreements/Action | List avtals åtgärd |
> | Action | Microsoft. DomainRegistration/topLevelDomains/Read | Hämta Toplevel-domäner |
> | Action | Microsoft. DomainRegistration/topLevelDomains/Read | Hämta Toplevel-domän |
> | Action | Microsoft.DomainRegistration/validateDomainRegistrationInformation/Action | Verifiera ett domän inköps objekt utan att skicka det |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. EventGrid/Domains/Delete | Ta bort en domän |
> | Action | Microsoft. EventGrid/Domains/Listnycklar/Action | Lista nycklar för en domän |
> | Action | Microsoft. EventGrid/Domains/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för domäner |
> | Action | Microsoft. EventGrid/Domains/Read | Läs en domän |
> | Action | Microsoft. EventGrid/Domains/regenerateKey/Action | Återskapa nyckel för en domän |
> | Action | Microsoft. EventGrid/Domains/topics/Read | Läs avsnittet om en domän |
> | Action | Microsoft. EventGrid/Domains/Write | Skapa eller uppdatera en domän |
> | Action | Microsoft. EventGrid/eventSubscriptions/Delete | Ta bort en eventSubscription |
> | Action | Microsoft.EventGrid/eventSubscriptions/getFullUrl/action | Hämta fullständig URL för händelse prenumerationen |
> | Action | Microsoft. EventGrid/eventSubscriptions/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för händelse prenumerationer |
> | Action | Microsoft. EventGrid/eventSubscriptions/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för händelse prenumerationer |
> | Action | Microsoft. EventGrid/eventSubscriptions/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för eventSubscriptions |
> | Action | Microsoft. EventGrid/eventSubscriptions/Read | Läs en eventSubscription |
> | Action | Microsoft. EventGrid/eventSubscriptions/Write | Skapa eller uppdatera en eventSubscription |
> | Action | Microsoft. EventGrid/extensionTopics/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för ämnen |
> | Action | Microsoft. EventGrid/extensionTopics/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för ämnen |
> | Action | Microsoft.EventGrid/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för ämnen |
> | Action | Microsoft. EventGrid/extensionTopics/Read | Läs en extensionTopic. |
> | Action | Microsoft. EventGrid/locations/eventSubscriptions/Read | Lista regionala händelse prenumerationer |
> | Action | Microsoft.EventGrid/locations/operationResults/read | Läs resultatet av en regional åtgärd |
> | Action | Microsoft.EventGrid/locations/operationsStatus/read | Läs status för en regional åtgärd |
> | Action | Microsoft. EventGrid/locations/topictypes/eventSubscriptions/Read | Lista regionala händelse prenumerationer efter TopicType |
> | Action | Microsoft.EventGrid/operationResults/read | Läs resultatet av en åtgärd |
> | Action | Microsoft. EventGrid/Operations/Read | Visa en lista över EventGrid-åtgärder. |
> | Action | Microsoft.EventGrid/operationsStatus/read | Läs status för en åtgärd |
> | Action | Microsoft. EventGrid/register/åtgärd | Registrerar prenumerationen för EventGrid-resurs leverantören. |
> | Action | Microsoft. EventGrid/ämnen/Delete | Ta bort ett ämne |
> | Action | Microsoft.EventGrid/topics/listKeys/action | Lista nycklar för ett ämne |
> | Action | Microsoft. EventGrid/ämnen/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för ämnen |
> | Action | Microsoft. EventGrid/ämnen/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för ämnen |
> | Action | Microsoft. EventGrid/ämnen/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för ämnen |
> | Action | Microsoft. EventGrid/ämnen/läsa | Läs ett ämne |
> | Action | Microsoft.EventGrid/topics/regenerateKey/action | Återskapa nyckel för ett ämne |
> | Action | Microsoft. EventGrid/ämnen/skriva | Skapa eller uppdatera ett ämne |
> | Action | Microsoft. EventGrid/topictypes/eventSubscriptions/Read | Lista globala händelse prenumerationer efter typ av ämne |
> | Action | Microsoft. EventGrid/topictypes/eventtypes/Read | Läs eventtypes som stöds av en TopicType |
> | Action | Microsoft. EventGrid/topictypes/Read | Läs en TopicType |
> | Action | Microsoft.EventGrid/unregister/action | Avregistrerar prenumerationen för EventGrid-resurs leverantören. |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.EventHub/availableClusterRegions/read | Läs åtgärd för att visa en lista över tillgängliga företablerade kluster av Azure-regionen. |
> | Action | Microsoft. EventHub/checkNameAvailability/Action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Action | Microsoft. EventHub/checkNamespaceAvailability/Action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuellt Använd CheckNameAvailability i stället. |
> | Action | Microsoft. EventHub/Clusters/Delete | Tar bort en befintlig kluster resurs. |
> | Action | Microsoft. EventHub/kluster/namn områden/läsa | Visar namn rymds ARM-ID för namn områden i ett kluster. |
> | Action | Microsoft. EventHub/Clusters/operationresults/Read | Hämta status för en asynkron kluster åtgärd. |
> | Action | Microsoft. EventHub/Clusters/providers/Microsoft. Insights/metricDefinitions/Read | Hämta lista över resurs beskrivningar för kluster mått |
> | Action | Microsoft.EventHub/clusters/read | Hämtar beskrivning av klusterresurs |
> | Action | Microsoft.EventHub/clusters/write | Skapar eller ändrar en befintlig kluster resurs. |
> | Action | Microsoft.EventHub/locations/deleteVirtualNetworkOrSubnets/action | Tar bort reglerna för virtuellt nätverk i EventHub-resursprovidern för det angivna virtuella nätverket |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/action | Uppdaterar namn områdets auktoriseringsregel. Detta API är föråldrat. Använd ett parkera-anrop för att uppdatera auktoriseringsprincipen för namn områden i stället.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.EventHub/namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Action | Microsoft.EventHub/namespaces/Delete | Ta bort namnområdesresurs |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Action | Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Action | Microsoft.EventHub/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/delete | Tar bort den katastrof återställnings konfiguration som är kopplad till namn området. Den här åtgärden kan bara anropas via det primära namn området. |
> | Action | Microsoft. EventHub/Namespaces/disasterRecoveryConfigs/redundans/åtgärd | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Action | Microsoft.EventHub/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/action | Åtgärd för att uppdatera EventHub. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Action | Microsoft. EventHub/Namespaces/eventhubs/authorizationRules/Delete | Åtgärd för att ta bort regler för EventHub-auktorisering |
> | Action | Microsoft. EventHub/Namespaces/eventhubs/authorizationRules/listnycklar/Action | Hämta anslutnings strängen till EventHub |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/read |  Hämta listan över regler för EventHub-auktorisering |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.EventHub/namespaces/eventhubs/authorizationRules/write | Skapa regler för EventHub-auktorisering och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Action | Microsoft. EventHub/Namespaces/eventHubs/consumergroups/Delete | Åtgärd för att ta bort ConsumerGroup-resurs |
> | Action | Microsoft. EventHub/Namespaces/eventHubs/consumergroups/Read | Hämta lista över beskrivningar av ConsumerGroup-resurser |
> | Action | Microsoft. EventHub/Namespaces/eventHubs/consumergroups/Write | Skapa eller uppdatera ConsumerGroup-egenskaper. |
> | Action | Microsoft. EventHub/Namespaces/eventhubs/Delete | Åtgärd för att ta bort EventHub-resurs |
> | Action | Microsoft. EventHub/Namespaces/eventhubs/Read | Hämta lista över beskrivningar av EventHub-resurser |
> | Action | Microsoft. EventHub/Namespaces/eventhubs/Write | Skapa eller uppdatera egenskaper för EventHub. |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/delete | Ta bort IP-filterresurs |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/read | Hämta IP-filterresurs |
> | Action | Microsoft.EventHub/namespaces/ipFilterRules/write | Skapa IP-filterresurs |
> | DataAction | Microsoft. EventHub/Namespaces/meddelanden/ta emot/åtgärd | Ta emot meddelanden |
> | DataAction | Microsoft. EventHub/Namespaces/meddelanden/skicka/åtgärd | Skicka meddelanden |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/read | Hämtar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.EventHub/namespaces/messagingPlan/write | Uppdaterar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft. EventHub/Namespaces/networkruleset/Delete | Ta bort regelresurs för virtuellt nätverk |
> | Action | Microsoft. EventHub/Namespaces/networkruleset/Read | Hämtar NetworkRuleSet-resurs |
> | Action | Microsoft. EventHub/Namespaces/networkruleset/Write | Skapa regelresurs för virtuellt nätverk |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/delete | Ta bort regelresurs för virtuellt nätverk |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/read | Hämtar NetworkRuleSet-resurs |
> | Action | Microsoft.EventHub/namespaces/networkrulesets/write | Skapa regelresurs för virtuellt nätverk |
> | Action | Microsoft. EventHub/Namespaces/operationresults/Read | Hämta status för namnområdesåtgärd |
> | Action | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Read | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Action | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/diagnosticSettings/Write | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Action | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/logDefinitions/Read | Hämta lista över namn rymds loggar resurs beskrivningar |
> | Action | Microsoft. EventHub/Namespaces/providers/Microsoft. Insights/metricDefinitions/Read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Action | Microsoft. EventHub/Namespaces/Read | Hämta listan över beskrivningar av namnområdesresurs |
> | Action | Microsoft.EventHub/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/delete | Ta bort regelresurs för virtuellt nätverk |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/read | Hämtar regelresurs för virtuellt nätverk |
> | Action | Microsoft.EventHub/namespaces/virtualNetworkRules/write | Skapa regelresurs för virtuellt nätverk |
> | Action | Microsoft. EventHub/namnrymd/Skriv | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Action | Microsoft.EventHub/operations/read | Hämta åtgärder |
> | Action | Microsoft. EventHub/register/åtgärd | Registrerar prenumerationen för EventHub-resursprovidern och gör det möjligt att skapa EventHub-resurser |
> | Action | Microsoft.EventHub/sku/read | Hämta lista med resurs beskrivningar för SKU |
> | Action | Microsoft.EventHub/sku/regions/read | Hämta lista över beskrivningar av SkuRegions-resurser |
> | Action | Microsoft.EventHub/unregister/action | Registrerar EventHub-resursprovidern |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. features/features/Read | Hämtar en prenumerations funktioner. |
> | Action | Microsoft. features/Operations/Read | Hämtar listan över åtgärder. |
> | Action | Microsoft. features/providers/features/Read | Hämtar en prenumerations funktion hos en given resursprovider. |
> | Action | Microsoft. features/providers/features/register/Action | Registrerar funktionen för en prenumerations hos en given resursprovider. |
> | Action | Microsoft. features/providers/features/unregister/Action | Avregistrerar funktionen för en prenumeration hos en viss resursprovider. |
> | Action | Microsoft. features/register/åtgärd | Registrerar funktionen för en prenumeration. |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. GuestConfiguration/guestConfigurationAssignments/Delete | Ta bort gäst konfigurations tilldelning. |
> | Action | Microsoft. GuestConfiguration/guestConfigurationAssignments/Read | Hämta gäst konfigurations tilldelning. |
> | Action | Microsoft. GuestConfiguration/guestConfigurationAssignments/Reports/Read | Hämta tilldelnings rapport över gäst konfiguration. |
> | Action | Microsoft. GuestConfiguration/guestConfigurationAssignments/Write | Skapa ny gäst konfigurations tilldelning. |
> | Action | Microsoft. GuestConfiguration/register/åtgärd | Registrerar prenumerationen för Microsoft. GuestConfiguration-resurs leverantören. |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. HDInsight/kluster/program/ta bort | Ta bort program för HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/program/läsa | Hämta program för HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/program/Skriv | Skapa eller uppdatera program för HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/changerdpsetting/åtgärd | Ändra RDP-inställningen för HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/konfigurationer/åtgärd | Uppdatera konfiguration av HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/konfigurationer/åtgärd | Hämta HDInsight-klusterkonfigurationer |
> | Action | Microsoft. HDInsight/kluster/konfigurationer/läsa | Hämta HDInsight-klusterkonfigurationer |
> | Action | Microsoft. HDInsight/kluster/ta bort | Ta bort ett HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/getGatewaySettings/åtgärd | Hämta Gateway-inställningar för HDInsight-kluster |
> | Action | Microsoft. HDInsight/Clusters/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursens HDInsight-kluster |
> | Action | Microsoft. HDInsight/Clusters/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursens HDInsight-kluster |
> | Action | Microsoft. HDInsight/Clusters/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/läsa | Hämta information om HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/roller/ändra storlek/åtgärd | Ändra storlek på ett HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/updateGatewaySettings/åtgärd | Uppdatera Gateway-inställningar för HDInsight-kluster |
> | Action | Microsoft. HDInsight/kluster/skrivning | Skapa eller uppdatera HDInsight-kluster |
> | Action | Microsoft. HDInsight/locations/Capabilities/Read | Hämta prenumerations funktioner |
> | Action | Microsoft. HDInsight/locations/checkNameAvailability/Read | Kontrollera namntillgänglighet |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ImportExport/Jobs/Delete | Tar bort ett befintligt jobb. |
> | Action | Microsoft. ImportExport/Jobs/listBitLockerKeys/åtgärd | Hämtar BitLocker-nycklar för det angivna jobbet. |
> | Action | Microsoft. ImportExport/Jobs/läsa | Hämtar egenskaperna för det angivna jobbet eller returnerar listan över jobb. |
> | Action | Microsoft. ImportExport/Jobs/Write | Skapar ett jobb med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för det angivna jobbet. |
> | Action | Microsoft. ImportExport/locations/Read | Hämtar egenskaperna för den angivna platsen eller returnerar listan över platser. |
> | Action | Microsoft. ImportExport/register/åtgärd | Registrerar prenumerationen för import-/export resurs leverantören och gör det möjligt att skapa import/export-jobb. |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Insights/ActionGroups/Delete | Ta bort en åtgärdsgrupp |
> | Action | Microsoft. Insights/ActionGroups/Read | Läs en åtgärdsgrupp |
> | Action | Microsoft. Insights/ActionGroups/Write | Skapa eller uppdatera en åtgärdsgrupp |
> | Action | Microsoft.Insights/ActivityLogAlerts/Activated/Action | Aktivitetsloggavisering har utlösts |
> | Action | Microsoft.Insights/ActivityLogAlerts/Delete | Ta bort en aktivitetsloggavisering |
> | Action | Microsoft.Insights/ActivityLogAlerts/Read | Läs en aktivitetsloggavisering |
> | Action | Microsoft.Insights/ActivityLogAlerts/Write | Skapa eller uppdatera en aktivitetsloggavisering |
> | Action | Microsoft.Insights/AlertRules/Activated/Action | Klassisk måttavisering har aktiverats |
> | Action | Microsoft.Insights/AlertRules/Delete | Ta bort en klassisk måttavisering |
> | Action | Microsoft.Insights/AlertRules/Incidents/Read | Läs en klassisk måttaviseringsincident |
> | Action | Microsoft. Insights/AlertRules/Read | Läs en klassisk måttavisering |
> | Action | Microsoft. Insights/AlertRules/åtgärdad/åtgärd | Klassisk måttavisering har lösts |
> | Action | Microsoft. Insights/AlertRules/begränsad/åtgärd | Regel för klassisk måttavisering har begränsats |
> | Action | Microsoft. Insights/AlertRules/Write | Skapa eller uppdatera en klassisk måttavisering |
> | Action | Microsoft.Insights/AutoscaleSettings/Delete | Ta bort en autoskalningsinställning |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/diagnosticSettings/Read | Läs en resursdiagnostikinställning |
> | Action | Microsoft. Insights/AutoscaleSettings/providers/Microsoft. Insights/diagnosticSettings/Write | Skapa eller uppdatera en resursdiagnostikinställning |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/logDefinitions/Read | Läs loggdefinitioner |
> | Action | Microsoft.Insights/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Action | Microsoft.Insights/AutoscaleSettings/Read | Läs en autoskalningsinställning |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaledown/Action | Autoskalning nedåt har initierats |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaledownResult/Action | Autoskalning nedåt har slutförts |
> | Action | Microsoft.Insights/AutoscaleSettings/Scaleup/Action | Autoskalning uppåt har initierats |
> | Action | Microsoft.Insights/AutoscaleSettings/ScaleupResult/Action | Autoskalning uppåt har slutförts |
> | Action | Microsoft.Insights/AutoscaleSettings/Write | Skapa eller uppdatera en autoskalningsinställning |
> | Action | Microsoft. Insights/baseline/Read | Läs en baslinje för mått (förhandsversion) |
> | Action | Microsoft. Insights/CalculateBaseline/Read | Beräkna baslinje för måttvärden (förhandsversion) |
> | Action | Microsoft.Insights/Components/AnalyticsItems/Delete | Tar bort ett Application Insights Analytics-objekt |
> | Action | Microsoft. Insights/komponenter/AnalyticsItems/Read | Läser ett Application Insights Analytics-objekt |
> | Action | Microsoft. Insights/komponenter/AnalyticsItems/Write | Skriver ett Application Insights Analytics-objekt |
> | Action | Microsoft. Insights/komponenter/AnalyticsTables/åtgärd | Åtgärd för Application Insights Analytics-tabell |
> | Action | Microsoft. Insights/komponenter/AnalyticsTables/Delete | Tar bort ett Application Insights Analytics-tabellschema |
> | Action | Microsoft. Insights/komponenter/AnalyticsTables/Read | Läser ett Application Insights Analytics-tabellschema |
> | Action | Microsoft. Insights/komponenter/AnalyticsTables/Write | Skriver ett Application Insights Analytics-tabellschema |
> | Action | Microsoft. Insights/komponenter/anteckningar/ta bort | Tar bort en Application Insights-anteckning |
> | Action | Microsoft. Insights/komponenter/anteckningar/läsa | Läser en Application Insights-anteckning |
> | Action | Microsoft. Insights/komponenter/anteckningar/Skriv | Skriver en Application Insights-anteckning |
> | Action | Microsoft. Insights/komponenter/API/läsa | Läser API för Application Insights-komponentdata |
> | Action | Microsoft. Insights/komponenter/ApiKeys/åtgärd | Skapar en API-nyckel för Application Insights |
> | Action | Microsoft. Insights/komponenter/ApiKeys/Delete | Tar bort en API-nyckel för Application Insights |
> | Action | Microsoft. Insights/komponenter/ApiKeys/Read | Läser en API-nyckel för Application Insights |
> | Action | Microsoft. Insights/komponenter/BillingPlanForComponent/Read | Läser ett faktureringsavtal för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/CurrentBillingFeatures/Read | Läser aktuella faktureringsfunktioner för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/CurrentBillingFeatures/Write | Skriver aktuella faktureringsfunktioner för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/DefaultWorkItemConfig/Read | Läser en standardkonfiguration av ALM-integrering för Application Insights |
> | Action | Microsoft. Insights/komponenter/ta bort | Tar bort konfiguration av Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/händelser/läsa | Hämta loggar från Application Insights med OData-frågeformat |
> | Action | Microsoft. Insights/komponenter/ExportConfiguration/åtgärd | Åtgärd för exportinställningar i Application Insights |
> | Action | Microsoft. Insights/komponenter/ExportConfiguration/Delete | Tar bort Application Insights-exportinställningar |
> | Action | Microsoft. Insights/komponenter/ExportConfiguration/Read | Läser Application Insights-exportinställningar |
> | Action | Microsoft. Insights/komponenter/ExportConfiguration/Write | Skriver Application Insights-exportinställningar |
> | Action | Microsoft. Insights/komponenter/ExtendQueries/Read | Läser utökade frågeresultat för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/favoriter/ta bort | Tar bort en Application Insights-favorit |
> | Action | Microsoft. Insights/komponenter/favoriter/läsa | Läser en Application Insights-favorit |
> | Action | Microsoft. Insights/komponenter/Favoriter/Skriv | Skriver en Application Insights-favorit |
> | Action | Microsoft. Insights/komponenter/FeatureCapabilities/Read | Läser funktioner för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/GetAvailableBillingFeatures/Read | Läser tillgängliga faktureringsfunktioner för Application Insight-komponent |
> | Action | Microsoft. Insights/komponenter/GetToken/Read | Läser en token för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/MetricDefinitions/Read | Läser definitioner av mätvärden för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/mått/läsa | Läser mätvärden för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/flytta/åtgärd | Flytta en Application Insights-komponent till en annan resursgrupp eller prenumeration |
> | Action | Microsoft.Insights/Components/MyAnalyticsItems/Delete | Tar bort ett personligt Application Insights Analytics-objekt |
> | Action | Microsoft. Insights/komponenter/MyAnalyticsItems/Read | Läser ett personligt Application Insights Analytics-objekt |
> | Action | Microsoft. Insights/komponenter/MyAnalyticsItems/Write | Skriver ett personligt Application Insights Analytics-objekt |
> | Action | Microsoft. Insights/komponenter/mina favoriter/läsa | Läser en personlig Application Insights-favorit |
> | Action | Microsoft. Insights/komponenter/åtgärder/Läs | Få status på tidskrävande åtgärder i Application Insights |
> | Action | Microsoft. Insights/komponenter/PricingPlans/Read | Läser ett prisavtal för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/PricingPlans/Write | Skriver ett prisavtal för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/ProactiveDetectionConfigs/Read | Läser konfiguration av proaktiv identifiering för Application Insights |
> | Action | Microsoft. Insights/komponenter/ProactiveDetectionConfigs/Write | Skriver konfiguration av proaktiv identifiering för Application Insights |
> | Action | Microsoft. Insights/Components/providers/Microsoft. Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Action | Microsoft. Insights/komponenter/rensning/åtgärd | Rensar data från Application Insights |
> | Action | Microsoft. Insights/komponenter/fråga/läsa | Kör frågor mot Application Insights-loggar |
> | Action | Microsoft. Insights/komponenter/QuotaStatus/Read | Läser kvotstatus för Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/läsa | Läser en konfiguration av Application Insights-komponent |
> | Action | Microsoft. Insights/komponenter/SyntheticMonitorLocations/Read | Läser Application Insights-webbtestplatser |
> | Action | Microsoft. Insights/komponenter/webbtester/läsa | Läser en webbtestkonfiguration |
> | Action | Microsoft. Insights/komponenter/WorkItemConfigs/Delete | Tar bort en konfiguration av ALM-integrering i Application Insights |
> | Action | Microsoft. Insights/komponenter/WorkItemConfigs/Read | Läser en konfiguration av ALM-integrering i Application Insights |
> | Action | Microsoft. Insights/komponenter/WorkItemConfigs/Write | Skriver en konfiguration av ALM-integrering i Application Insights |
> | Action | Microsoft. Insights/komponenter/skriva | Skriver till en konfiguration av Application Insights-komponent |
> | Action | Microsoft.Insights/DiagnosticSettings/Delete | Ta bort en resursdiagnostikinställning |
> | Action | Microsoft. Insights/DiagnosticSettings/Read | Läs en resursdiagnostikinställning |
> | Action | Microsoft. Insights/DiagnosticSettings/Write | Skapa eller uppdatera en resursdiagnostikinställning |
> | Action | Microsoft.Insights/EventCategories/Read | Läs tillgängliga kategorier av aktivitetslogghändelser |
> | Action | Microsoft. Insights/eventtypes/digestevents/Read | Läs sammandrag av hanteringshändelsetyp |
> | Action | Microsoft. Insights/eventtypes/värden/Read | Läs aktivitetslogghändelser |
> | Action | Microsoft. Insights/ExtendedDiagnosticSettings/Delete | Ta bort en diagnostikinställning för nätverksflödeslogg |
> | Action | Microsoft. Insights/ExtendedDiagnosticSettings/Read | Läs en diagnostikinställning för nätverksflödeslogg |
> | Action | Microsoft. Insights/ExtendedDiagnosticSettings/Write | Skapa eller uppdatera en diagnostikinställning för nätverksflödeslogg |
> | Action | Microsoft. Insights/ListMigrationDate/åtgärd | Hämta datum för prenumerationsmigrering |
> | Action | Microsoft.Insights/ListMigrationDate/Read | Hämta datum för prenumerationsmigrering |
> | Action | Microsoft. Insights/LogDefinitions/Read | Läs loggdefinitioner |
> | Action | Microsoft.Insights/LogProfiles/Delete | Ta bort en loggprofil för aktivitetslogg |
> | Action | Microsoft.Insights/LogProfiles/Read | Läs en loggprofil för aktivitetslogg |
> | Action | Microsoft. Insights/LogProfiles/Write | Skapa eller uppdatera en loggprofil för aktivitetslogg |
> | Action | Microsoft.Insights/Logs/ADAssessmentRecommendation/Read | Läs data från ADAssessmentRecommendation-tabellen |
> | Action | Microsoft.Insights/Logs/ADReplicationResult/Read | Läs data från ADReplicationResult-tabellen |
> | Action | Microsoft.Insights/Logs/ADSecurityAssessmentRecommendation/Read | Läs data från ADSecurityAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/loggar/varna/läsa | Läs data från Alert-tabellen |
> | Action | Microsoft.Insights/Logs/AlertHistory/Read | Läs data från AlertHistory-tabellen |
> | Action | Microsoft. Insights/logs/ApplicationInsights/Read | Läs data från ApplicationInsights-tabellen |
> | Action | Microsoft. Insights/logs/AzureActivity/Read | Läs data från AzureActivity-tabellen |
> | Action | Microsoft.Insights/Logs/AzureMetrics/Read | Läs data från AzureMetrics-tabellen |
> | Action | Microsoft. Insights/logs/BoundPort/Read | Läs data från BoundPort-tabellen |
> | Action | Microsoft. Insights/logs/CommonSecurityLog/Read | Läs data från CommonSecurityLog-tabellen |
> | Action | Microsoft. Insights/logs/ComputerGroup/Read | Läs data från ComputerGroup-tabellen |
> | Action | Microsoft. Insights/logs/ConfigurationChange/Read | Läs data från ConfigurationChange-tabellen |
> | Action | Microsoft. Insights/logs/ConfigurationData/Read | Läs data från ConfigurationData-tabellen |
> | Action | Microsoft. Insights/logs/ContainerImageInventory/Read | Läs data från ContainerImageInventory-tabellen |
> | Action | Microsoft. Insights/logs/ContainerInventory/Read | Läs data från ContainerInventory-tabellen |
> | Action | Microsoft. Insights/logs/ContainerLog/Read | Läs data från ContainerLog-tabellen |
> | Action | Microsoft. Insights/logs/ContainerServiceLog/Read | Läs data från ContainerServiceLog-tabellen |
> | Action | Microsoft. Insights/logs/DeviceAppCrash/Read | Läs data från DeviceAppCrash-tabellen |
> | Action | Microsoft. Insights/logs/DeviceAppLaunch/Read | Läs data från DeviceAppLaunch-tabellen |
> | Action | Microsoft. Insights/logs/DeviceCalendar/Read | Läs data från DeviceCalendar-tabellen |
> | Action | Microsoft. Insights/logs/DeviceCleanup/Read | Läs data från DeviceCleanup-tabellen |
> | Action | Microsoft. Insights/logs/DeviceConnectSession/Read | Läs data från DeviceConnectSession-tabellen |
> | Action | Microsoft. Insights/logs/DeviceEtw/Read | Läs data från DeviceEtw-tabellen |
> | Action | Microsoft.Insights/Logs/DeviceHardwareHealth/Read | Läs data från DeviceHardwareHealth-tabellen |
> | Action | Microsoft.Insights/Logs/DeviceHealth/Read | Läs data från DeviceHealth-tabellen |
> | Action | Microsoft. Insights/logs/DeviceHeartbeat/Read | Läs data från DeviceHeartbeat-tabellen |
> | Action | Microsoft. Insights/logs/DeviceSkypeHeartbeat/Read | Läs data från DeviceSkypeHeartbeat-tabellen |
> | Action | Microsoft.Insights/Logs/DeviceSkypeSignIn/Read | Läs data från DeviceSkypeSignIn-tabellen |
> | Action | Microsoft. Insights/logs/DeviceSleepState/Read | Läs data från DeviceSleepState-tabellen |
> | Action | Microsoft. Insights/logs/DHAppFailure/Read | Läs data från DHAppFailure-tabellen |
> | Action | Microsoft. Insights/logs/DHAppReliability/Read | Läs data från DHAppReliability-tabellen |
> | Action | Microsoft. Insights/logs/DHDriverReliability/Read | Läs data från DHDriverReliability-tabellen |
> | Action | Microsoft. Insights/logs/DHLogonFailures/Read | Läs data från DHLogonFailures-tabellen |
> | Action | Microsoft. Insights/logs/DHLogonMetrics/Read | Läs data från DHLogonMetrics-tabellen |
> | Action | Microsoft. Insights/logs/DHOSCrashData/Read | Läs data från DHOSCrashData-tabellen |
> | Action | Microsoft. Insights/logs/DHOSReliability/Read | Läs data från DHOSReliability-tabellen |
> | Action | Microsoft. Insights/logs/DHWipAppLearning/Read | Läs data från DHWipAppLearning-tabellen |
> | Action | Microsoft. Insights/logs/DnsEvents/Read | Läs data från DnsEvents-tabellen |
> | Action | Microsoft. Insights/logs/DnsInventory/Read | Läs data från DnsInventory-tabellen |
> | Action | Microsoft. Insights/logs/ETWEvent/Read | Läs data från ETWEvent-tabellen |
> | Action | Microsoft. Insights/loggar/händelse/läsa | Läs data från Event-tabellen |
> | Action | Microsoft.Insights/Logs/ExchangeAssessmentRecommendation/Read | Läs data från ExchangeAssessmentRecommendation-tabellen |
> | Action | Microsoft.Insights/Logs/ExchangeOnlineAssessmentRecommendation/Read | Läs data från ExchangeOnlineAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/loggar/pulsslag/läsning | Läs data från Hearbeat-tabellen |
> | Action | Microsoft.Insights/Logs/IISAssessmentRecommendation/Read | Läs data från IISAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/InboundConnection/Read | Läs data från InboundConnection-tabellen |
> | Action | Microsoft. Insights/logs/KubeNodeInventory/Read | Läs data från KubeNodeInventory-tabellen |
> | Action | Microsoft. Insights/logs/KubePodInventory/Read | Läs data från KubePodInventory-tabellen |
> | Action | Microsoft. Insights/logs/LinuxAuditLog/Read | Läs data från LinuxAuditLog-tabellen |
> | Action | Microsoft. Insights/logs/MAApplication/Read | Läs data från MAApplication-tabellen |
> | Action | Microsoft.Insights/Logs/MAApplicationHealth/Read | Läs data från MAApplicationHealth-tabellen |
> | Action | Microsoft.Insights/Logs/MAApplicationHealthAlternativeVersions/Read | Läs data från MAApplicationHealthAlternativeVersions-tabellen |
> | Action | Microsoft. Insights/logs/MAApplicationHealthIssues/Read | Läs data från MAApplicationHealthIssues-tabellen |
> | Action | Microsoft. Insights/logs/MAApplicationInstance/Read | Läs data från MAApplicationInstance-tabellen |
> | Action | Microsoft. Insights/logs/MAApplicationInstanceReadiness/Read | Läs data från MAApplicationInstanceReadiness-tabellen |
> | Action | Microsoft.Insights/Logs/MAApplicationReadiness/Read | Läs data från MAApplicationReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MADeploymentPlan/Read | Läs data från MADeploymentPlan-tabellen |
> | Action | Microsoft. Insights/logs/MADevice/Read | Läs data från MADevice-tabellen |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealth/Read | Läs data från MADevicePnPHealth-tabellen |
> | Action | Microsoft.Insights/Logs/MADevicePnPHealthAlternativeVersions/Read | Läs data från MADevicePnPHealthAlternativeVersions-tabellen |
> | Action | Microsoft. Insights/logs/MADevicePnPHealthIssues/Read | Läs data från MADevicePnPHealthIssues-tabellen |
> | Action | Microsoft. Insights/logs/MADeviceReadiness/Read | Läs data från MADeviceReadiness-tabellen |
> | Action | Microsoft.Insights/Logs/MADriverInstanceReadiness/Read | Läs data från MADriverInstanceReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MADriverReadiness/Read | Läs data från MADriverReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAddin/Read | Läs data från MAOfficeAddin-tabellen |
> | Action | Microsoft.Insights/Logs/MAOfficeAddinHealth/Read | Läs data från MAOfficeAddinHealth-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAddinHealthIssues/Read | Läs data från MAOfficeAddinHealthIssues-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAddinInstance/Read | Läs data från MAOfficeAddinInstance-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAddinInstanceReadiness/Read | Läs data från MAOfficeAddinInstanceReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAddinReadiness/Read | Läs data från MAOfficeAddinReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeApp/Read | Läs data från MAOfficeApp-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAppHealth/Read | Läs data från MAOfficeAppHealth-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAppInstance/Read | Läs data från MAOfficeAppInstance-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeAppReadiness/Read | Läs data från MAOfficeAppReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeBuildInfo/Read | Läs data från MAOfficeBuildInfo-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeCurrencyAssessment/Read | Läs data från MAOfficeCurrencyAssessment-tabellen |
> | Action | Microsoft.Insights/Logs/MAOfficeCurrencyAssessmentDailyCounts/Read | Läs data från MAOfficeCurrencyAssessmentDailyCounts-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeDeploymentStatus/Read | Läs data från MAOfficeDeploymentStatus-tabellen |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroHealth/Read | Läs data från MAOfficeMacroHealth-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeMacroHealthIssues/Read | Läs data från MAOfficeMacroHealthIssues-tabellen |
> | Action | Microsoft.Insights/Logs/MAOfficeMacroIssueInstanceReadiness/Read | Läs data från MAOfficeMacroIssueInstanceReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeMacroIssueReadiness/Read | Läs data från MAOfficeMacroIssueReadiness-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeMacroSummary/Read | Läs data från MAOfficeMacroSummary-tabellen |
> | Action | Microsoft. Insights/logs/MAOfficeSuite/Read | Läs data från MAOfficeSuite-tabellen |
> | Action | Microsoft.Insights/Logs/MAOfficeSuiteInstance/Read | Läs data från MAOfficeSuiteInstance-tabellen |
> | Action | Microsoft. Insights/logs/MAProposedPilotDevices/Read | Läs data från MAProposedPilotDevices-tabellen |
> | Action | Microsoft. Insights/logs/MAWindowsBuildInfo/Read | Läs data från MAWindowsBuildInfo-tabellen |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessment/Read | Läs data från MAWindowsCurrencyAssessment-tabellen |
> | Action | Microsoft.Insights/Logs/MAWindowsCurrencyAssessmentDailyCounts/Read | Läs data från MAWindowsCurrencyAssessmentDailyCounts-tabellen |
> | Action | Microsoft. Insights/logs/MAWindowsDeploymentStatus/Read | Läs data från MAWindowsDeploymentStatus-tabellen |
> | Action | Microsoft.Insights/Logs/MAWindowsSysReqInstanceReadiness/Read | Läs data från MAWindowsSysReqInstanceReadiness-tabellen |
> | Action | Microsoft. Insights/logs/NetworkMonitoring/Read | Läs data från NetworkMonitoring-tabellen |
> | Action | Microsoft. Insights/logs/OfficeActivity/Read | Läs data från OfficeActivity-tabellen |
> | Action | Microsoft. Insights/loggar/åtgärd/Läs | Läs data från Operation-tabellen |
> | Action | Microsoft. Insights/logs/OutboundConnection/Read | Läs data från OutboundConnection-tabellen |
> | Action | Microsoft. Insights/loggar/perf/Read | Läs data från Perf-tabellen |
> | Action | Microsoft. Insights/logs/ProtectionStatus/Read | Läs data från ProtectionStatus-tabellen |
> | Action | Microsoft. Insights/loggar/läsa | Läser data från alla dina loggar |
> | Action | Microsoft.Insights/Logs/ReservedAzureCommonFields/Read | Läs data från ReservedAzureCommonFields-tabellen |
> | Action | Microsoft.Insights/Logs/ReservedCommonFields/Read | Läs data från ReservedCommonFields-tabellen |
> | Action | Microsoft.Insights/Logs/SCCMAssessmentRecommendation/Read | Läs data från SCCMAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/SCOMAssessmentRecommendation/Read | Läs data från SCOMAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/SecurityAlert/Read | Läs data från SecurityAlert-tabellen |
> | Action | Microsoft. Insights/logs/SecurityBaseline/Read | Läs data från SecurityBaseline-tabellen |
> | Action | Microsoft. Insights/logs/SecurityBaselineSummary/Read | Läs data från SecurityBaselineSummary-tabellen |
> | Action | Microsoft. Insights/logs/SecurityDetection/Read | Läs data från SecurityDetection-tabellen |
> | Action | Microsoft. Insights/logs/SecurityEvent/Read | Läs data från SecurityEvent-tabellen |
> | Action | Microsoft. Insights/logs/ServiceFabricOperationalEvent/Read | Läs data från ServiceFabricOperationalEvent-tabellen |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableActorEvent/Read | Läs data från ServiceFabricReliableActorEvent-tabellen |
> | Action | Microsoft.Insights/Logs/ServiceFabricReliableServiceEvent/Read | Läs data från ServiceFabricReliableServiceEvent-tabellen |
> | Action | Microsoft.Insights/Logs/SfBAssessmentRecommendation/Read | Läs data från SfBAssessmentRecommendation-tabellen |
> | Action | Microsoft.Insights/Logs/SfBOnlineAssessmentRecommendation/Read | Läs data från SfBOnlineAssessmentRecommendation-tabellen |
> | Action | Microsoft.Insights/Logs/SharePointOnlineAssessmentRecommendation/Read | Läs data från SharePointOnlineAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/SPAssessmentRecommendation/Read | Läs data från SPAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/SQLAssessmentRecommendation/Read | Läs data från SQLAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/SQLQueryPerformance/Read | Läs data från SQLQueryPerformance-tabellen |
> | Action | Microsoft. Insights/loggar/syslog/Read | Läs data från Syslog-tabellen |
> | Action | Microsoft. Insights/logs/SysmonEvent/Read | Läs data från SysmonEvent-tabellen |
> | Action | Microsoft. Insights/logs/UAApp/Read | Läs data från UAApp-tabellen |
> | Action | Microsoft. Insights/logs/UAComputer/Read | Läs data från UAComputer-tabellen |
> | Action | Microsoft. Insights/logs/UAComputerRank/Read | Läs data från UAComputerRank-tabellen |
> | Action | Microsoft. Insights/logs/UADriver/Read | Läs data från UADriver-tabellen |
> | Action | Microsoft.Insights/Logs/UADriverProblemCodes/Read | Läs data från UADriverProblemCodes-tabellen |
> | Action | Microsoft. Insights/logs/UAFeedback/Read | Läs data från UAFeedback-tabellen |
> | Action | Microsoft. Insights/logs/UAHardwareSecurity/Read | Läs data från UAHardwareSecurity-tabellen |
> | Action | Microsoft. Insights/logs/UAIESiteDiscovery/Read | Läs data från UAIESiteDiscovery-tabellen |
> | Action | Microsoft. Insights/logs/UAOfficeAddIn/Read | Läs data från UAOfficeAddIn-tabellen |
> | Action | Microsoft. Insights/logs/UAProposedActionPlan/Read | Läs data från UAProposedActionPlan-tabellen |
> | Action | Microsoft. Insights/logs/UASysReqIssue/Read | Läs data från UASysReqIssue-tabellen |
> | Action | Microsoft. Insights/logs/UAUpgradedComputer/Read | Läs data från UAUpgradedComputer-tabellen |
> | Action | Microsoft. Insights/loggar/uppdatera/läsa | Läs data från Update-tabellen |
> | Action | Microsoft.Insights/Logs/UpdateRunProgress/Read | Läs data från UpdateRunProgress-tabellen |
> | Action | Microsoft.Insights/Logs/UpdateSummary/Read | Läs data från UpdateSummary-tabellen |
> | Action | Microsoft. Insights/loggar/användning/läsa | Läs data från Usage-tabellen |
> | Action | Microsoft. Insights/logs/W3CIISLog/Read | Läs data från W3CIISLog-tabellen |
> | Action | Microsoft. Insights/logs/WaaSDeploymentStatus/Read | Läs data från WaaSDeploymentStatus-tabellen |
> | Action | Microsoft. Insights/logs/WaaSInsiderStatus/Read | Läs data från WaaSInsiderStatus-tabellen |
> | Action | Microsoft. Insights/logs/WaaSUpdateStatus/Read | Läs data från WaaSUpdateStatus-tabellen |
> | Action | Microsoft. Insights/logs/WDAVStatus/Read | Läs data från WDAVStatus-tabellen |
> | Action | Microsoft. Insights/logs/WDAVThreat/Read | Läs data från WDAVThreat-tabellen |
> | Action | Microsoft.Insights/Logs/WindowsClientAssessmentRecommendation/Read | Läs data från WindowsClientAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/WindowsFirewall/Read | Läs data från WindowsFirewall-tabellen |
> | Action | Microsoft.Insights/Logs/WindowsServerAssessmentRecommendation/Read | Läs data från WindowsServerAssessmentRecommendation-tabellen |
> | Action | Microsoft. Insights/logs/WireData/Read | Läs data från WireData-tabellen |
> | Action | Microsoft. Insights/logs/WUDOAggregatedStatus/Read | Läs data från WUDOAggregatedStatus-tabellen |
> | Action | Microsoft. Insights/logs/WUDOStatus/Read | Läs data från WUDOStatus-tabellen |
> | Action | Microsoft.Insights/MetricAlerts/Delete | Ta bort en måttavisering |
> | Action | Microsoft.Insights/MetricAlerts/Read | Läs en måttavisering |
> | Action | Microsoft. Insights/MetricAlerts/status/läsa | Läs status för måttavisering |
> | Action | Microsoft. Insights/MetricAlerts/Write | Skapa eller uppdatera en måttavisering |
> | Action | Microsoft. Insights/MetricBaselines/Read | Läs mått bas linjer |
> | Action | Microsoft.Insights/MetricDefinitions/Microsoft.Insights/Read | Läs måttdefinitioner |
> | Action | Microsoft. Insights/MetricDefinitions/providers/Microsoft. Insights/Read | Läs måttdefinitioner |
> | Action | Microsoft.Insights/MetricDefinitions/Read | Läs måttdefinitioner |
> | Action | Microsoft. Insights/Metricnamespaces/Read | Läs mått namn rymder |
> | Action | Microsoft.Insights/Metrics/Action | Måttåtgärd |
> | Action | Microsoft. Insights/Metrics/Microsoft. Insights/Read | Läs mått |
> | Action | Microsoft. Insights/Metrics/providers/Metrics/Read | Läs mått |
> | Action | Microsoft.Insights/Metrics/Read | Läs mått |
> | DataAction | Microsoft. Insights/Metrics/Write | Skrivning av mått |
> | Action | Microsoft.Insights/MigrateToNewpricingModel/Action | Migrera prenumerationen till den nya prismodellen |
> | Action | Microsoft. Insights/åtgärder/läsa | Läsåtgärder |
> | Action | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Action | Microsoft. Insights/RollbackToLegacyPricingModel/åtgärd | Återställ prenumerationen till den äldre prismodellen |
> | Action | Microsoft. Insights/ScheduledQueryRules/Delete | Tar bort en schemalagd frågeregel |
> | Action | Microsoft. Insights/ScheduledQueryRules/Read | Läser en schemalagd frågeregel |
> | Action | Microsoft. Insights/ScheduledQueryRules/Write | Skriver en schemalagd frågeregel |
> | Action | Microsoft. Insights/klienter/register/åtgärd | Startar Microsoft Insights-providern |
> | Action | Microsoft.Insights/Unregister/Action | Registrera Microsoft Insights-providern |
> | Action | Microsoft. Insights/webbtester/ta bort | Tar bort en webbtestkonfiguration |
> | Action | Microsoft. Insights/webbtester/GetToken/Read | Läser en token för webbtest |
> | Action | Microsoft. Insights/webbtester/MetricDefinitions/Read | Läser definitioner av mått för webbtest |
> | Action | Microsoft. Insights/webbtester/mått/läsa | Läser ett webbtestmätvärde |
> | Action | Microsoft. Insights/webbtester/läsa | Läser en webbtestkonfiguration |
> | Action | Microsoft. Insights/webbtester/skriva | Skriver till en webbtestkonfiguration |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Intune/diagnosticsettings/Delete | Tar bort en diagnostisk inställning |
> | Action | Microsoft. Intune/diagnosticsettings/Read | Läser en diagnostisk inställning |
> | Action | Microsoft. Intune/diagnosticsettings/Write | Skriva en diagnostisk inställning |
> | Action | Microsoft. Intune/diagnosticsettingscategories/Read | Läser en diagnostisk inställnings kategori |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.IoTCentral/appTemplates/action | Hämtar alla tillgängliga programmallar på Azure IoT Central |
> | Action | Microsoft.IoTCentral/checkNameAvailability/action | Kontrollerar om ett IoT Central program namn är tillgängligt |
> | Action | Microsoft.IoTCentral/checkSubdomainAvailability/action | Kontrollerar om en IoT Central program under domän är tillgänglig |
> | Action | Microsoft. IoTCentral/IoTApps/Delete | Tar bort ett IoT Central program |
> | Action | Microsoft.IoTCentral/IoTApps/read | Hämtar ett enda IoT Central-program |
> | Action | Microsoft.IoTCentral/IoTApps/write | Skapar eller uppdaterar ett IoT Central program |
> | Action | Microsoft.IoTCentral/operations/read | Hämtar alla tillgängliga åtgärder på IoT Central program |
> | Action | Microsoft.IoTCentral/register/action | Registrera prenumerationen för Azure IoT Central Resource Provider |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.IoTSpaces/Graph/delete | Tar bort Microsoft. IoTSpaces Graph-resurs |
> | Action | Microsoft.IoTSpaces/Graph/read | Hämtar diagram resurs (er) för Microsoft. IoTSpaces |
> | Action | Microsoft.IoTSpaces/Graph/write | Skapa Microsoft. IoTSpaces Graph-resurs |
> | Action | Microsoft. IoTSpaces/register/åtgärd | Registrera prenumerationen för Microsoft. IoTSpaces Graph Resource Provider om du vill aktivera att resurser skapas |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. nyckel valv/checkNameAvailability/läsa | Kontrollera att nyckelvalvsnamnet är giltigt och inte används |
> | Action | Microsoft.KeyVault/deletedVaults/read | Visa egenskaperna för de ej permanent borttagna nyckelvalven |
> | Action | Microsoft. nyckel valv/hsmPools/ta bort | Ta bort en HSM-pool |
> | Action | Microsoft. nyckel valv/hsmPools/joinVault/åtgärd | Anslut ett nyckelvalv till en HSM-pool |
> | Action | Microsoft. nyckel valv/hsmPools/läsa | Visa egenskaperna för en HSM-pool |
> | Action | Microsoft. nyckel valv/hsmPools/skrivning | Skapa en ny HSM-pool eller uppdatera egenskaperna för en befintlig HSM-pool |
> | Action | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent borttaget nyckelvalv |
> | Action | Microsoft. nyckel-valv/platser/deletedVaults/läsa | Visa egenskaperna för ett ej permanent borttaget nyckelvalv |
> | Action | Microsoft.KeyVault/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.KeyVault att ett virtuellt nätverk eller undernät tas bort |
> | Action | Microsoft.KeyVault/locations/operationResults/read | Kontrollera resultatet för en långkörd åtgärd |
> | Action | Microsoft.KeyVault/operations/read | Listar de åtgärder som finns tillgängliga på resursprovidern Microsoft.KeyVault |
> | Action | Microsoft. nyckel valv/register/åtgärd | Registrerar en prenumeration |
> | Action | Microsoft.KeyVault/unregister/action | Avregistrerar en prenumeration |
> | Action | Microsoft.KeyVault/vaults/accessPolicies/write | Uppdatera en befintlig åtkomstprincip genom att sammanfoga, ersätta eller lägga till en ny åtkomstprincip till ett valv. |
> | Action | Microsoft.KeyVault/vaults/delete | Ta bort ett nyckelvalv |
> | Action | Microsoft.KeyVault/vaults/deploy/action | Tillåter åtkomst till hemligheter i ett nyckelvalv vid distribution av Azure-resurser |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/delete | Meddelar Microsoft.KeyVault att en EventGrid-prenumeration för Key Vault tas bort |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/read | Meddelar Microsoft.KeyVault att en EventGrid-prenumeration för Key Vault visas |
> | Action | Microsoft.KeyVault/vaults/eventGridFilters/write | Meddelar Microsoft.KeyVault att en ny EventGrid-prenumeration för Key Vault skapas |
> | Action | Microsoft.KeyVault/vaults/read | Visa egenskaperna för ett nyckelvalv |
> | Action | Microsoft.KeyVault/vaults/secrets/read | Visa egenskaperna för en hemlighet, men inte dess värde. |
> | Action | Microsoft.KeyVault/vaults/secrets/write | Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet. |
> | Action | Microsoft.KeyVault/vaults/write | Skapa ett nytt nyckelvalv eller uppdatera egenskaperna för ett befintligt nyckelvalv |

## <a name="microsoftkusto"></a>Microsoft. Kusto

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Kusto/kluster/aktivera/åtgärd | Startar klustret. |
> | Action | Microsoft. Kusto/kluster/AttachedDatabaseConfigurations/Delete | Tar bort en länkad databas konfigurations resurs. |
> | Action | Microsoft. Kusto/kluster/AttachedDatabaseConfigurations/Read | Läser en konfigurations resurs för anslutna databaser. |
> | Action | Microsoft. Kusto/kluster/AttachedDatabaseConfigurations/Write | Skriver en resurs för konfiguration av bifogad databas. |
> | Action | Microsoft. Kusto/kluster/CheckNameAvailability/åtgärd | Kontrollerar tillgänglighet för kluster namn. |
> | Action | Microsoft.Kusto/Clusters/Databases/AddPrincipals/action | Lägger till databasens huvud namn. |
> | Action | Microsoft. Kusto/kluster/databaser/CheckNameAvailability/åtgärd | Kontrollerar namn tillgänglighet för en specifik typ. |
> | Action | Microsoft. Kusto/kluster/databaser/DataConnections/Delete | Tar bort en data anslutnings resurs. |
> | Action | Microsoft. Kusto/kluster/databaser/DataConnections/Read | Läser en data anslutnings resurs. |
> | Action | Microsoft. Kusto/kluster/databaser/DataConnections/Write | Skriver en resurs för data anslutningar. |
> | Action | Microsoft. Kusto/kluster/databaser/DataConnectionValidation/åtgärd | Verifierar databas data anslutning. |
> | Action | Microsoft. Kusto/kluster/databaser/ta bort | Tar bort en databas resurs. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/delete | Tar bort en händelse för Event Hub-anslutningar. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/read | Läser en resurs för Event Hub-anslutningar. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnections/write | Skriver en resurs för Event Hub-anslutningar. |
> | Action | Microsoft.Kusto/Clusters/Databases/EventHubConnectionValidation/action | Verifierar anslutning till databasens händelsehubben. |
> | Action | Microsoft. Kusto/kluster/databaser/ListPrincipals/åtgärd | Visar en lista över databasens säkerhets objekt. |
> | Action | Microsoft. Kusto/kluster/databaser/läsa | Läser en databas resurs. |
> | Action | Microsoft. Kusto/kluster/databaser/RemovePrincipals/åtgärd | Tar bort databasens huvud namn. |
> | Action | Microsoft. Kusto/kluster/databaser/skriva | Skriver en databas resurs. |
> | Action | Microsoft. Kusto/kluster/inaktivera/åtgärd | Stoppar klustret. |
> | Action | Microsoft. Kusto/kluster/ta bort | Tar bort en kluster resurs. |
> | Action | Microsoft. Kusto/kluster/läsa | Läser en kluster resurs. |
> | Action | Microsoft.Kusto/Clusters/SKUs/read | Läser en kluster-SKU-resurs. |
> | Action | Microsoft. Kusto/kluster/start/åtgärd | Startar klustret. |
> | Action | Microsoft. Kusto/kluster/stoppa/åtgärd | Stoppar klustret. |
> | Action | Microsoft. Kusto/kluster/skrivning | Skriver en kluster resurs. |
> | Action | Microsoft. Kusto/locations/CheckNameAvailability/Action | Kontrollerar tillgänglighet för resurs namn. |
> | Action | Microsoft. Kusto/locations/operationresults/Read | Läser åtgärds resurser |
> | Action | Microsoft.Kusto/Operations/read | Läser åtgärds resurser |
> | Action | Microsoft. Kusto/register/åtgärd | Registrerar prenumerationen på Kusto-resurs leverantören. |
> | Action | Microsoft.Kusto/SKUs/read | Läser en SKU-resurs. |
> | Action | Microsoft.Kusto/Unregister/action | Avregistrerar prenumerationen på Kusto-resurs leverantören. |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.LabServices/labAccounts/CreateLab/action | Skapa ett labb i ett labb konto. |
> | Action | Microsoft. LabServices/labAccounts/Delete | Ta bort labb konton. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/delete | Ta bort Galleri bilder. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/read | Läsa Galleri bilder. |
> | Action | Microsoft.LabServices/labAccounts/galleryImages/write | Lägg till eller ändra Galleri bilder. |
> | Action | Microsoft.LabServices/labAccounts/GetRegionalAvailability/action | Hämta regional tillgänglighets information för varje storleks kategori som kon figurer ATS under ett labb konto |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Delete | Ta bort idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Read | Läs idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/idleShutdowns/Write | Lägg till eller ändra idleshutdowns. |
> | Action | Microsoft. LabServices/labAccounts/Labs/AddUsers/Action | Lägga till användare i ett labb |
> | Action | Microsoft. LabServices/labAccounts/Labs/Delete | Ta bort labb. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ClaimAny/åtgärd | Hävdar en slumpmässig miljö för en användare i miljö inställningar |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/delete | Ta bort miljö inställning. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Claim/action | Hävdar att miljön och tilldelar den till användaren |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/delete | Ta bort miljöer. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/read | Läs miljöer. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/miljöer/ResetPassword/åtgärd | Återställer användar lösen ordet i en miljö |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Start/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/Stop/action | Stoppar en miljö genom att stoppa alla resurser i miljön |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/environments/write | Lägg till eller ändra miljöer. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Publish/action | Bestämmelser/avetablerar nödvändiga resurser för en miljö inställning baserat på aktuell status för labb/miljö-inställningen. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/read | Läs miljö inställning. |
> | Action | Microsoft. LabServices/labAccounts/Labs/environmentSettings/ResetPassword/åtgärd | Återställer lösen ordet för den virtuella datorns mall. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/SaveImage/action | Sparar den aktuella mal Lav bildningen i det delade galleriet i labb kontot |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/delete | Ta bort scheman. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/read | Läs scheman. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/schedules/write | Lägg till eller ändra scheman. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Start/action | Startar en mall genom att starta alla resurser i mallen. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/Stop/action | Stoppar en mall genom att stoppa alla resurser i mallen. |
> | Action | Microsoft.LabServices/labAccounts/labs/environmentSettings/write | Lägg till eller ändra miljö inställning. |
> | Action | Microsoft. LabServices/labAccounts/Labs/Read | Läs labb. |
> | Action | Microsoft. LabServices/labAccounts/Labs/register/åtgärd | Registrera dig för Managed Lab. |
> | Action | Microsoft. LabServices/labAccounts/Labs/SendEmail/Action | Skicka e-post med registrerings länk till labbet |
> | Action | Microsoft. LabServices/labAccounts/Labs/Users/Delete | Ta bort användare. |
> | Action | Microsoft. LabServices/labAccounts/Labs/Users/Read | Läsa användare. |
> | Action | Microsoft.LabServices/labAccounts/labs/users/write | Lägg till eller ändra användare. |
> | Action | Microsoft.LabServices/labAccounts/labs/write | Lägg till eller ändra labb. |
> | Action | Microsoft.LabServices/labAccounts/read | Läs labb konton. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/delete | Ta bort sharedgalleries. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/read | Läs sharedgalleries. |
> | Action | Microsoft.LabServices/labAccounts/sharedGalleries/write | Lägg till eller ändra sharedgalleries. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/delete | Ta bort sharedimages. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/read | Läs sharedimages. |
> | Action | Microsoft.LabServices/labAccounts/sharedImages/write | Lägg till eller ändra sharedimages. |
> | Action | Microsoft.LabServices/labAccounts/write | Lägg till eller ändra Lab-konton. |
> | Action | Microsoft. LabServices/locations/Operations/Read | Läs åtgärder. |
> | Action | Microsoft. LabServices/register/åtgärd | Registrerar prenumerationen |
> | Action | Microsoft.LabServices/users/GetEnvironment/action | Hämtar information om den virtuella datorn |
> | Action | Microsoft.LabServices/users/GetOperationBatchStatus/action | Hämta åtgärds status för batch |
> | Action | Microsoft. LabServices/Users/Fabricadaptor/Action | Hämtar status för tids krävande åtgärd |
> | Action | Microsoft. LabServices/Users/GetPersonalPreferences/Action | Hämta personliga inställningar för en användare |
> | Action | Microsoft.LabServices/users/ListAllEnvironments/action | Visa en lista över alla miljöer för användaren |
> | Action | Microsoft.LabServices/users/ListEnvironments/action | Visa lista över miljöer för användaren |
> | Action | Microsoft. LabServices/Users/ListLabs/Action | Visa en lista med labb för användaren. |
> | Action | Microsoft. LabServices/Users/register/Action | Registrera en användare i ett hanterat labb |
> | Action | Microsoft. LabServices/Users/ResetPassword/Action | Återställer användar lösen ordet i en miljö |
> | Action | Microsoft.LabServices/users/StartEnvironment/action | Startar en miljö genom att starta alla resurser i miljön. |
> | Action | Microsoft. LabServices/Users/StopEnvironment/Action | Stoppar en miljö genom att stoppa alla resurser i miljön |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Logic/integrationAccounts/agreements/delete | Tar bort avtalet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/listContentCallbackUrl/action | Hämtar motringnings-URL:en för avtalsinnehållet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/read | Läser avtalet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/agreements/write | Skapar eller uppdaterar avtalet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/delete | Tar bort sammansättningen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/listContentCallbackUrl/action | Hämtar motringnings-URL:en för sammansättningsinnehållet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/read | Läser sammansättningen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/assemblies/write | Skapar eller uppdaterar sammansättningen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/delete | Tar bort batchkonfigurationen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/read | Läser batchkonfigurationen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/batchConfigurations/write | Skapar eller uppdaterar batchkonfigurationen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/delete | Tar bort certifikatet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/read | Läser certifikatet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/certificates/write | Skapar eller uppdaterar certifikatet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/delete | Tar bort integrationskontot. |
> | Action | Microsoft. Logic/integrationAccounts/JOIN/åtgärd | Ansluter integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/listCallbackUrl/action | Hämtar motringnings-URL:en för integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/listKeyVaultKeys/action | Hämtar nycklarna i nyckelvalvet. |
> | Action | Microsoft.Logic/integrationAccounts/logTrackingEvents/action | Loggar spårningshändelser i integrationskontot. |
> | Action | Microsoft. Logic/integrationAccounts/Maps/Delete | Tar bort kartan i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/maps/listContentCallbackUrl/action | Hämtar motringnings-URL:en för kartinnehållet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/maps/read | Läser kartan i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/maps/write | Skapar eller uppdaterar kartan i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/partners/delete | Tar bort partnern i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/partners/listContentCallbackUrl/action | Hämtar motringnings-URL:en för partnerinnehållet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/partners/read | Läser partnern i integrations kontot. |
> | Action | Microsoft.Logic/integrationAccounts/partners/write | Skapar eller uppdaterar partnern i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read | Läser integrationskontots loggdefinitioner. |
> | Action | Microsoft.Logic/integrationAccounts/read | Läser integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/regenerateAccessKey/action | Återskapar åtkomstnyckelhemligheter. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/delete | Tar bort schemat i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/listContentCallbackUrl/action | Hämtar motringnings-URL:en för schemainnehållet i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/read | Läser schemat i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/schemas/write | Skapar eller uppdaterar schemat i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/delete | Tar bort sessionen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/read | Läser batchkonfigurationen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/sessions/write | Skapar eller uppdaterar sessionen i integrationskontot. |
> | Action | Microsoft.Logic/integrationAccounts/write | Skapar eller uppdaterar integrationskontot. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/delete | Tar bort integreringstjänstmiljön. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/join/action | Går med i integreringstjänstmiljön. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/apiOperations/read | Läser den integreringstjänstmiljöhanterade API-åtgärden. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/managedApis/read | Läser det integreringstjänstmiljöhanterade API:et. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/operationStatuses/read | Läser åtgärdsstatusar för integreringstjänstmiljön. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/providers/Microsoft.Insights/metricDefinitions/read | Läser måttdefinitionerna för integreringstjänstmiljö. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/read | Läser integreringstjänstmiljön. |
> | Action | Microsoft.Logic/integrationServiceEnvironments/write | Skapar eller uppdaterar integreringstjänstmiljön. |
> | Action | Microsoft. Logic/locations/arbetsflödens/recommendOperationGroups/Action | Hämtar arbetsflödets rekommendationsåtgärdsgrupper. |
> | Action | Microsoft. Logic/locations/arbetsflödens/validate/Action | Verifierar arbetsflödet. |
> | Action | Microsoft. Logic/Operations/Read | Hämtar åtgärden. |
> | Action | Microsoft. Logic/register/åtgärd | Registrerar Microsoft.Logic-resursprovidern för en viss prenumeration. |
> | Action | Microsoft. Logic/-arbetsflöden/accessKeys/Delete | Tar bort åtkomstnyckeln. |
> | Action | Microsoft. Logic/arbetsflöden/accessKeys/List/Action | Radar upp åtkomstnyckelhemligheter. |
> | Action | Microsoft. Logic/-arbetsflöden/accessKeys/Read | Läser åtkomstnyckeln. |
> | Action | Microsoft. Logic/-arbetsflöden/accessKeys/regenerate/Action | Återskapar åtkomstnyckelhemligheter. |
> | Action | Microsoft. Logic/-arbetsflöden/accessKeys/Write | Skapar eller uppdaterar åtkomstnyckeln. |
> | Action | Microsoft. Logic/arbetsflöden/Delete | Tar bort arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/inaktivera/åtgärd | Inaktiverar arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/aktivera/åtgärd | Aktiverar arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/listCallbackUrl/åtgärd | Hämtar arbetsflödets motringnings-URL. |
> | Action | Microsoft. Logic/-arbets flöden/listSwagger/åtgärd | Hämtar arbetsflödets Swagger-definitioner. |
> | Action | Microsoft. Logic/-arbets flöden/flytta/åtgärd | Flyttar arbetsflödet från befintligt prenumerations-id, resursgrupp och/eller namn till ett annat prenumerations-id, resursgrupp och/eller namn. |
> | Action | Microsoft. Logic/Workflows/providers/Microsoft. Insights/diagnosticSettings/Read | Läser diagnostikinställningar för arbetsflödet. |
> | Action | Microsoft. Logic/Workflows/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar diagnostikinställning för arbetsflödet. |
> | Action | Microsoft. Logic/Workflows/providers/Microsoft. Insights/logDefinitions/Read | Läser loggdefinitioner för arbetsflödet. |
> | Action | Microsoft. Logic/Workflows/providers/Microsoft. Insights/metricDefinitions/Read | Läser måttdefinitioner för arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/läsa | Läser arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/regenerateAccessKey/åtgärd | Återskapar åtkomstnyckelhemligheter. |
> | Action | Microsoft. Logic/arbets flöden/körning/åtgärd | Startar en körning av arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/listExpressionTraces/åtgärd | Hämtar spåren av arbetsflödeskörningens åtgärdsuttryck. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/Läs | Läser arbetsflödets köråtgärd. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/repetitioner/listExpressionTraces/åtgärd | Hämtar upprepningsspåren av arbetsflödeskörningens åtgärdsuttryck. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/repetitioner/läsning | Läser upprepningen av arbetsflödets körningsåtgärd. |
> | Action | Microsoft. Logic/arbets flöden/körningar/åtgärder/repetitioner/requestHistories/Read | Återställer frågehistoriken för arbetsflödets körningsupprepningsåtgärd. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/requestHistories/Read | Återställer frågehistoriken för arbetsflödets körningsåtgärd. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/scoperepetitions/Read | Läser upprepningen av reservationsomfånget för arbetsflödets körningsåtgärd. |
> | Action | Microsoft. Logic/-arbetsflöden/körs/Avbryt/åtgärd | Avbryter körningen av ett arbetsflöde. |
> | Action | Microsoft. Logic/-arbetsflöden/kör/ta bort | Tar bort en körning av ett arbets flöde. |
> | Action | Microsoft. Logic/-arbets flöden/körningar/åtgärder/Läs | Läser åtgärdsstatus för arbetsflödeskörning. |
> | Action | Microsoft. Logic/-arbetsflöden/körs/läsa | Läser arbetsflödeskörningen. |
> | Action | Microsoft. Logic/-arbets flöden/Suspend/åtgärd | Gör uppehåll i arbetsflödet. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/historik/läsa | Läser utlösarhistorik. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/händelser/skicka igen/åtgärd | Skickar om arbetsflödets utlösare. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/listCallbackUrl/åtgärd | Hämtar motringnings-URL:en för utlösaren. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/läsa | Läser utlösarna. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/Återställ/åtgärd | Återställer utlösaren. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/kör/åtgärd | Utför utlösaren. |
> | Action | Microsoft. Logic/arbets flöden/utlösare/setState/åtgärd | Ställer in utlösartillstånd. |
> | Action | Microsoft.Logic/workflows/validate/action | Verifierar arbetsflödet. |
> | Action | Microsoft. Logic/-arbets flöden/versioner/läsa | Läser arbetsflödesversionen. |
> | Action | Microsoft. Logic/-arbetsflöden/-versioner/utlösare/listCallbackUrl/åtgärd | Hämtar motringnings-URL:en för utlösaren. |
> | Action | Microsoft. Logic/-arbets flöden/skriva | Skapar eller uppdaterar arbetsflödet. |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/move/action | Flytta en Machine Learning åtagande Plans koppling |
> | Action | Microsoft.MachineLearning/commitmentPlans/commitmentAssociations/read | Läs en Machine Learning åtagande Plans koppling |
> | Action | Microsoft.MachineLearning/commitmentPlans/delete | Ta bort en Machine Learning åtagande plan |
> | Action | Microsoft.MachineLearning/commitmentPlans/join/action | Delta i en Machine Learning åtagande plan |
> | Action | Microsoft.MachineLearning/commitmentPlans/read | Läs en Machine Learning åtagande plan |
> | Action | Microsoft.MachineLearning/commitmentPlans/write | Skapa eller uppdatera en Machine Learning åtagande plan |
> | Action | Microsoft.MachineLearning/locations/operationresults/read | Hämta resultatet av en Machine Learning-åtgärd |
> | Action | Microsoft.MachineLearning/locations/operationsstatus/read | Hämta status för en pågående Machine Learning åtgärd |
> | Action | Microsoft.MachineLearning/operations/read | Hämta Machine Learning åtgärder |
> | Action | Microsoft.MachineLearning/register/action | Registrerar prenumerationen för resurs leverantören för Machine Learning-webbtjänsten och gör det möjligt att skapa webb tjänster. |
> | Action | Microsoft.MachineLearning/skus/read | Hämta Machine Learning åtagande plan SKU: er |
> | Action | Microsoft.MachineLearning/webServices/action | Skapa regionala webb tjänst egenskaper för regioner som stöds |
> | Action | Microsoft.MachineLearning/webServices/delete | Ta bort alla Machine Learning webb tjänster |
> | Action | Microsoft.MachineLearning/webServices/listkeys/read | Hämta nycklar till en Machine Learning-webbtjänst |
> | Action | Microsoft.MachineLearning/webServices/read | Läs alla Machine Learning webb tjänster |
> | Action | Microsoft.MachineLearning/webServices/write | Skapa eller uppdatera webb tjänsten för Machine Learning |
> | Action | Microsoft. MachineLearning/arbets ytor/ta bort | Ta bort eventuella Machine Learning-arbetsyta |
> | Action | Microsoft.MachineLearning/Workspaces/listworkspacekeys/action | Lista nycklar för en Machine Learning-arbetsyta |
> | Action | Microsoft. MachineLearning/arbets ytor/läsa | Läs eventuella Machine Learning-arbetsyta |
> | Action | Microsoft.MachineLearning/Workspaces/resyncstoragekeys/action | Omsynkronisera nycklar för ett lagrings konto som har kon figurer ATS för en Machine Learning-arbetsyta |
> | Action | Microsoft.MachineLearning/Workspaces/write | Skapa eller uppdatera Machine Learning-arbetsyta |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.MachineLearningServices/locations/computeoperationsstatus/read | Hämtar status för en viss beräknings åtgärd |
> | Action | Microsoft.MachineLearningServices/locations/usages/read | Användnings rapport för AML-beräknings resurser i en prenumeration |
> | Action | Microsoft.MachineLearningServices/locations/vmsizes/read | Hämta VM-storlekar som stöds |
> | Action | Microsoft.MachineLearningServices/locations/workspaceOperationsStatus/read | Hämtar status för en viss arbets ytans åtgärd |
> | Action | Microsoft.MachineLearningServices/register/action | Registrerar prenumerationen för Machine Learning Services Resource Provider |
> | Action | Microsoft. MachineLearningServices/arbets ytor/beräkningar/ta bort | Tar bort beräknings resurserna i Machine Learning Services arbets ytor |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/listKeys/action | Lista hemligheter för beräknings resurser i Machine Learning Services arbets yta |
> | Action | Microsoft. MachineLearningServices/arbets ytor/computes/listNodes/Action | Lista noder för beräknings resurs i Machine Learning Services arbets yta |
> | Action | Microsoft. MachineLearningServices/arbets ytor/beräkningar/läsning | Hämtar beräknings resurserna i Machine Learning Services arbets ytor |
> | Action | Microsoft.MachineLearningServices/workspaces/computes/write | Skapar eller uppdaterar beräknings resurserna i Machine Learning Services arbets ytor |
> | Action | Microsoft.MachineLearningServices/workspaces/delete | Tar bort Machine Learning Services arbets ytor |
> | DataAction | Microsoft.MachineLearningServices/workspaces/experiments/write | Skapar eller uppdaterar experiment i Machine Learning Services arbets ytor |
> | Action | Microsoft.MachineLearningServices/workspaces/listKeys/action | Lista hemligheter för en Machine Learning Services arbets yta |
> | Action | Microsoft.MachineLearningServices/workspaces/read | Hämtar Machine Learning Services arbets ytor |
> | Action | Microsoft.MachineLearningServices/workspaces/write | Skapar eller uppdaterar Machine Learning Services arbets ytor |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ManagedIdentity/identiteter/läsa | Hämtar en befintlig systemtilldelad identitet |
> | Action | Microsoft.ManagedIdentity/register/action | Registrerar prenumerationen för den hanterade identitets resurs leverantören |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/assign/action | RBAC-åtgärd för att tilldela en befintlig användare tilldelad identitet till en resurs |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Tar bort en befintlig användare tilldelad identitet |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hämtar en befintlig användare tilldelad identitet |
> | Action | Microsoft.ManagedIdentity/userAssignedIdentities/write | Skapar en ny tilldelad identitet eller uppdaterar de taggar som är associerade med en befintlig användare som tilldelats identiteten |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ManagedServices/marketplaceRegistrationDefinitions/Read | Hämtar en lista över Managed Services-registreringsdefinitioner. |
> | Action | Microsoft. ManagedServices/operationStatuses/Read | Läser resursens åtgärdsstatus. |
> | Action | Microsoft. ManagedServices/register/åtgärd | Registrera dig för hanterade tjänster. |
> | Action | Microsoft. ManagedServices/registrationAssignments/Delete | Tar bort Managed Services-registreringstilldelning. |
> | Action | Microsoft. ManagedServices/registrationAssignments/Read | Hämtar en lista över Managed Services-registreringstilldelningar. |
> | Action | Microsoft. ManagedServices/registrationAssignments/Write | Lägg till eller ändra Managed Services-registreringstilldelning. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/Delete | Tar bort definition av Managed Services-registrering. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/Read | Hämtar en lista över Managed Services-registreringsdefinitioner. |
> | Action | Microsoft. ManagedServices/registrationDefinitions/Write | Lägg till eller ändra definition av Managed Services-registrering. |
> | Action | Microsoft. ManagedServices/avregistrera/åtgärd | Avregistrera från hanterade tjänster. |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Management/checkNameAvailability/action | Kontrollerar om det angivna hanterings grupp namnet är giltigt och unikt. |
> | Action | Microsoft.Management/getEntities/action | Lista alla entiteter (Hanteringsgrupper, prenumerationer osv.) för den autentiserade användaren. |
> | Action | Microsoft.Management/managementGroups/delete | Ta bort hanterings grupp. |
> | Action | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | Action | Microsoft.Management/managementGroups/subscriptions/delete | Ta bort prenumerationen från hanterings gruppen. |
> | Action | Microsoft.Management/managementGroups/subscriptions/write | Kopplar en befintlig prenumeration till hanterings gruppen. |
> | Action | Microsoft.Management/managementGroups/write | Skapa eller uppdatera en hanterings grupp. |
> | Action | Microsoft. Management/register/åtgärd | Registrera den angivna prenumerationen med Microsoft. Management |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | DataAction | Microsoft. Maps/Accounts/data/Read | Beviljar data Läs behörighet till ett Maps-konto. |
> | Action | Microsoft. Maps/Accounts/Delete | Ta bort ett Maps-konto. |
> | Action | Microsoft.Maps/accounts/eventGridFilters/delete | Ta bort ett Event Grid filter |
> | Action | Microsoft.Maps/accounts/eventGridFilters/read | Hämta ett Event Grid-filter |
> | Action | Microsoft.Maps/accounts/eventGridFilters/write | Skapa eller uppdatera ett Event Grid-filter |
> | Action | Microsoft. Maps/Accounts/Listnycklar/Action | Visa lista över mappnings konto nycklar |
> | Action | Microsoft. Maps/konton/läsa | Hämta ett Maps-konto. |
> | Action | Microsoft. Maps/Accounts/regenerateKey/Action | Skapa nytt Maps-konto primär eller sekundär nyckel |
> | Action | Microsoft.Maps/accounts/write | Skapa eller uppdatera ett Maps-konto. |
> | Action | Microsoft. Maps/Operations/Read | Läs leverantörs åtgärderna |
> | Action | Microsoft. Maps/register/åtgärd | Registrera providern |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/avtal/Läs | Returnerar ett avtal. |
> | Action | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/avtal/Skriv | Accepterar ett undertecknat avtal. |
> | Action | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/konfigurationer/importImage/åtgärd | Importerar en avbildning till slutanvändarens ACR. |
> | Action | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/konfigurationer/läsa | Returnerar en konfig. |
> | Action | Microsoft. Marketplace/offerTypes/utgivare/erbjudanden/planer/konfigurationer/Skriv åtgärder | Sparar en konfig. |
> | Action | Microsoft.Marketplace/register/action | Registrerar Microsoft.Marketplace-resursprovidern i prenumerationen. |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/delete | Tar bort en borttagnings åtgärd på en klassisk dev-tjänst resurs. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSecrets/action | Hämtar ett klassiskt utvecklings tjänst resurs hanterings nycklar. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/listSingleSignOnToken/action | Hämtar URL: en för enkel inloggning för en klassisk dev-tjänst. |
> | Action | Microsoft. MarketplaceApps/ClassicDevServices/Read | En GET-åtgärd i en klassisk dev-tjänst. |
> | Action | Microsoft.MarketplaceApps/ClassicDevServices/regenerateKey/action | Genererar ett klassiskt utvecklings tjänst resurs hanterings nycklar. |
> | Action | Microsoft.MarketplaceApps/Operations/read | Läs åtgärder för alla resurs typer. |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. MarketplaceOrdering/Agreements/offers/Plans/Cancel/Action | Avbryta ett avtal för ett angivet Marketplace-objekt |
> | Action | Microsoft. MarketplaceOrdering/Agreements/offers/Plans/Read | Returnera ett avtal för ett angivet Marketplace-objekt |
> | Action | Microsoft. MarketplaceOrdering/Agreements/offers/Planes/Sign/Action | Underteckna ett avtal för ett angivet Marketplace-objekt |
> | Action | Microsoft. MarketplaceOrdering/avtal/läsa | Returnera alla avtal under den aktuella prenumerationen |
> | Action | Microsoft. MarketplaceOrdering/offertypes/utgivare/erbjudanden/planer/avtal/läsning | Hämta ett avtal för ett angivet virtuellt dator objekt för Marketplace |
> | Action | Microsoft. MarketplaceOrdering/offertypes/utgivare/erbjudanden/planer/avtal/Skriv | Signera eller annullera ett avtal för ett angivet virtuellt dator objekt i Marketplace |
> | Action | Microsoft.MarketplaceOrdering/operations/read | Visa en lista över alla möjliga åtgärder i API: et |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Media/checknameavailability/åtgärd | Kontrollerar om ett Media Services konto namn är tillgängligt |
> | Action | Microsoft. Media/locations/checkNameAvailability/åtgärd | Kontrollerar om ett Media Services konto namn är tillgängligt |
> | Action | Microsoft. Media/Media Services/accountfilters/Delete | Ta bort eventuella konto filter |
> | Action | Microsoft. Media/Media Services/accountfilters/Read | Läs valfritt konto filter |
> | Action | Microsoft. Media/Media Services/accountfilters/Write | Skapa eller uppdatera eventuella konto filter |
> | Action | Microsoft. Media/Media Services/Asses/assetfilters/Delete | Ta bort eventuella till gångs filter |
> | Action | Microsoft. Media/Media Services/Asses/assetfilters/Read | Läsa filter för till gångar |
> | Action | Microsoft. Media/Media Services/Asses/assetfilters/Write | Skapa eller uppdatera ett till gångs filter |
> | Action | Microsoft. Media/Media Services/Asses/Delete | Ta bort alla till gångar |
> | Action | Microsoft. Media/Media Services/Asses/getEncryptionKey/Action | Hämta krypterings nyckel för till gång |
> | Action | Microsoft. Media/Media Services/Asses/listContainerSas/Action | Visa SAS-URL |
> | Action | Microsoft. Media/Media Services/Asses/listStreamingLocators/Action | Lista över strömmande positionerare för till gång |
> | Action | Microsoft. Media/Media Services/assets/Read | Läs en till gång |
> | Action | Microsoft. Media/Media Services/assets/Write | Skapa eller uppdatera en till gång |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/delete | Ta bort valfri princip för innehålls nyckel |
> | Action | Microsoft. Media/Media Services/contentKeyPolicies/getPolicyPropertiesWithSecrets/Action | Hämta princip egenskaper med hemligheter |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/read | Läs eventuell princip för innehålls nyckel |
> | Action | Microsoft.Media/mediaservices/contentKeyPolicies/write | Skapa eller uppdatera en innehålls nyckel princip |
> | Action | Microsoft. Media/Media Services/Delete | Ta bort ett Media Services konto |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/delete | Ta bort eventuella Event Grids filter |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/read | Läs eventuella Event Grid filter |
> | Action | Microsoft.Media/mediaservices/eventGridFilters/write | Skapa eller uppdatera eventuella Event Grids filter |
> | Action | Microsoft. Media/Media Services/liveEventOperations/Read | Läs valfri Live-händelse åtgärd |
> | Action | Microsoft. Media/Media Services/liveEvents/Delete | Ta bort valfri Live-händelse |
> | Action | Microsoft. Media/Media Services/liveEvents/liveOutputs/Delete | Ta bort direktsänd utdata |
> | Action | Microsoft. Media/Media Services/liveEvents/liveOutputs/Read | Läs alla direktsända utdata |
> | Action | Microsoft.Media/mediaservices/liveEvents/liveOutputs/write | Skapa eller uppdatera Live-utdata |
> | Action | Microsoft. Media/Media Services/liveEvents/Read | Läs valfri Live-händelse |
> | Action | Microsoft. Media/Media Services/liveEvents/reset/åtgärd | Återställ eventuell händelse åtgärd i real tid |
> | Action | Microsoft. Media/Media Services/liveEvents/start/Action | Starta valfri händelse åtgärd i real tid |
> | Action | Microsoft. Media/Media Services/liveEvents/stopp/åtgärd | Stoppa alla aktiva händelse åtgärder |
> | Action | Microsoft. Media/Media Services/liveEvents/Write | Skapa eller uppdatera valfri Live-händelse |
> | Action | Microsoft.Media/mediaservices/liveOutputOperations/read | Läs all pågående utmatnings åtgärd |
> | Action | Microsoft. Media/Media Services/Read | Läs valfritt Media Services konto |
> | Action | Microsoft. Media/Media Services/streamingEndpointOperations/Read | Läs eventuell slut punkts åtgärd för direkt uppspelning |
> | Action | Microsoft. Media/Media Services/strömnings slut punkter/Delete | Ta bort slut punkt för direkt uppspelning |
> | Action | Microsoft. Media/Media Services/strömnings slut punkter/Read | Läs eventuell slut punkt för direkt uppspelning |
> | Action | Microsoft.Media/mediaservices/streamingEndpoints/scale/action | Skala eventuella slut punkts åtgärder för direkt uppspelning |
> | Action | Microsoft. Media/Media Services/strömnings slut punkter/start/Action | Starta en slut punkts åtgärd för direkt uppspelning |
> | Action | Microsoft. Media/Media Services/strömnings slut punkter/stopp/åtgärd | Stoppa eventuella slut punkts åtgärder för direkt uppspelning |
> | Action | Microsoft. Media/Media Services/strömnings slut punkter/Write | Skapa eller uppdatera slut punkter för direkt uppspelning |
> | Action | Microsoft. Media/Media Services/streamingLocators/Delete | Ta bort alla strömmande positionerare |
> | Action | Microsoft.Media/mediaservices/streamingLocators/listContentKeys/action | Lista med innehålls nycklar |
> | Action | Microsoft. Media/Media Services/streamingLocators/listPaths/Action | Lista sökvägar |
> | Action | Microsoft. Media/Media Services/streamingLocators/Read | Läs alla strömmande positionerare |
> | Action | Microsoft. Media/Media Services/streamingLocators/Write | Skapa eller uppdatera en strömmande positionerare |
> | Action | Microsoft. Media/Media Services/streamingPolicies/Delete | Ta bort valfri strömmande princip |
> | Action | Microsoft. Media/Media Services/streamingPolicies/Read | Läs eventuell strömmande princip |
> | Action | Microsoft. Media/Media Services/streamingPolicies/Write | Skapa eller uppdatera en strömmande princip |
> | Action | Microsoft.Media/mediaservices/syncStorageKeys/action | Synkronisera lagrings nycklarna för ett kopplat Azure Storage-konto |
> | Action | Microsoft. Media/Media Services/Transforms/Delete | Ta bort transformering |
> | Action | Microsoft. Media/Media Services/Transforms/Jobs/cancelJob/åtgärd | Avbryt jobb |
> | Action | Microsoft. Media/Media Services/Transforms/Jobs/Delete | Ta bort alla jobb |
> | Action | Microsoft. Media/Media Services/Transforms/Jobs/Read | Läs valfritt jobb |
> | Action | Microsoft. Media/Media Services/Transforms/Jobs/Write | Skapa eller uppdatera ett jobb |
> | Action | Microsoft. Media/Media Services/Transforms/Read | Läs eventuell transformering |
> | Action | Microsoft. Media/Media Services/Transforms/Write | Skapa eller uppdatera en transformering |
> | Action | Microsoft. Media/Media Services/Write | Skapa eller uppdatera ett Media Services konto |
> | Action | Microsoft. Media/Operations/Read | Hämta tillgängliga åtgärder |
> | Action | Microsoft. media/register/åtgärd | Registrerar prenumerationen för Media Services Resource Provider och gör det möjligt att skapa Media Services-konton |
> | Action | Microsoft.Media/unregister/action | Avregistrerar prenumerationen för Media Services Resource Provider |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Migrate/assessmentprojects/assessmentOptions/Read | Hämtar de utvärderingsalternativ som är tillgängliga på den angivna platsen |
> | Action | Microsoft. Migrate/assessmentprojects/bedömningar/läsa | Listar utvärderingar i ett projekt |
> | Action | Microsoft. Migrate/assessmentprojects/Delete | Tar bort bedömnings projektet |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/assessments/assessedmachines/Read | Hämta egenskaperna för en utvärderad dator |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/assessments/Delete | Tar bort en utvärdering |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/assessments/DownloadURL/Action | Hämtar en URL för en utvärderingsrapport |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/assessments/Read | Hämtar egenskaperna för en utvärdering |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/bedömningar/Write | Skapar en ny utvärdering eller uppdaterar en befintlig |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/Delete | Tar bort en grupp |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/Read | Hämta en grupps egenskaper |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/updateMachines/Action | Uppdatera grupp genom att lägga till eller ta bort datorer |
> | Action | Microsoft. Migrate/assessmentprojects/Groups/Write | Skapar en ny grupp eller uppdaterar en befintlig |
> | Action | Microsoft. Migrate/assessmentprojects/hypervcollectors/Delete | Tar bort HyperV-insamlaren |
> | Action | Microsoft. Migrate/assessmentprojects/hypervcollectors/Read | Hämtar egenskaperna för HyperV-insamlaren |
> | Action | Microsoft. Migrate/assessmentprojects/hypervcollectors/Write | Skapar en ny HyperV-insamlare eller uppdaterar en befintlig HyperV-insamlare |
> | Action | Microsoft. Migrate/assessmentprojects/Machines/Read | Hämtar egenskaperna för en dator |
> | Action | Microsoft. Migrate/assessmentprojects/Read | Hämtar egenskaperna för bedömnings projekt |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/delete | Tar bort VMware-insamlaren |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/read | Hämtar egenskaperna för VMware-insamlaren |
> | Action | Microsoft.Migrate/assessmentprojects/vmwarecollectors/write | Skapar en ny VMware-insamlare eller uppdaterar en befintlig VMware-insamlare |
> | Action | Microsoft. Migrate/assessmentprojects/Write | Skapar ett nytt bedömnings projekt eller uppdaterar ett befintligt bedömnings projekt |
> | Action | Microsoft. Migrate/locations/assessmentOptions/Read | Hämtar de utvärderingsalternativ som är tillgängliga på den angivna platsen |
> | Action | Microsoft. Migrate/locations/checknameavailability/Action | Kontrollerar tillgänglighet för resurs namnet för den aktuella prenumerationen på den aktuella platsen |
> | Action | Microsoft.Migrate/migrateprojects/DatabaseInstances/read | Hämtar egenskaperna för en databas instans |
> | Action | Microsoft. Migrate/migrateprojects/databaser/läsa | Hämtar egenskaperna för en databas |
> | Action | Microsoft. Migrate/migrateprojects/Delete | Tar bort ett migrerat projekt |
> | Action | Microsoft. Migrate/migrateprojects/Machines/Read | Hämtar egenskaperna för en dator |
> | Action | Microsoft. Migrate/migrateprojects/MigrateEvents/Delete | Tar bort en migrerings händelse |
> | Action | Microsoft. Migrate/migrateprojects/MigrateEvents/Read | Hämtar egenskaperna för ett migrerande händelser. |
> | Action | Microsoft. Migrate/migrateprojects/Read | Hämtar egenskaper för migrera projekt |
> | Action | Microsoft. Migrate/migrateprojects/registerTool/Action | Registrerar verktyget för ett migrera projekt |
> | Action | Microsoft. Migrate/migrateprojects/Solutions/cleanupData/Action | Rensa informationen om migrerade projekt lösningar |
> | Action | Microsoft. Migrate/migrateprojects/Solutions/Delete | Tar bort en lösning för att migrera projekt |
> | Action | Microsoft. Migrate/migrateprojects/Solutions/getconfig/Action | Hämtar konfiguration för migreringsprojektslösning |
> | Action | Microsoft. Migrate/migrateprojects/Solutions/Read | Hämtar egenskaper för att migrera projekt lösning |
> | Action | Microsoft. Migrate/migrateprojects/Solutions/Write | Skapar en ny lösning för att migrera projekt eller uppdaterar en befintlig lösning för att migrera projekt |
> | Action | Microsoft. Migrate/migrateprojects/Write | Skapar ett nytt migrerings projekt eller uppdaterar ett befintligt migrerat projekt |
> | Action | Microsoft. Migrate/Operations/Read | Listar tillgängliga åtgärder på resursprovidern Microsoft.Migrate |
> | Action | Microsoft. Migrate/projekt/bedömningar/läsa | Listar utvärderingar i ett projekt |
> | Action | Microsoft. Migrate/Projects/Delete | Tar bort projektet |
> | Action | Microsoft. Migrate/Projects/Groups/assessments/assessedmachines/Read | Hämta egenskaperna för en utvärderad dator |
> | Action | Microsoft. Migrate/Projects/Groups/assessments/Delete | Tar bort en utvärdering |
> | Action | Microsoft. Migrate/Projects/Groups/assessments/DownloadURL/Action | Hämtar en URL för en utvärderingsrapport |
> | Action | Microsoft. Migrate/Projects/Groups/assessments/Read | Hämtar egenskaperna för en utvärdering |
> | Action | Microsoft. Migrate/Projects/Groups/assessments/Write | Skapar en ny utvärdering eller uppdaterar en befintlig |
> | Action | Microsoft. Migrate/Projects/Groups/Delete | Tar bort en grupp |
> | Action | Microsoft. Migrate/Projects/Groups/Read | Hämta en grupps egenskaper |
> | Action | Microsoft. Migrate/Projects/Groups/Write | Skapar en ny grupp eller uppdaterar en befintlig |
> | Action | Microsoft. Migrate/Projects/Keys/åtgärd | Hämtar delade nycklar för projektet |
> | Action | Microsoft. Migrate/Projects/Machines/Read | Hämtar egenskaperna för en dator |
> | Action | Microsoft. Migrate/Projects/Read | Hämtar egenskaperna för ett projekt |
> | Action | Microsoft. Migrate/Projects/Write | Skapar ett nytt projekt eller uppdaterar ett befintligt |
> | Action | Microsoft. Migrate/registrera/åtgärd | Registrerar prenumeration på resursprovidern Microsoft.Migrate |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. MixedReality/register/åtgärd | Registrerar en prenumeration för resurs leverantören för Mixad verklighet. |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Skapa spatiala ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Ta bort avstånds ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för Microsoft. MixedReality/SpatialAnchorsAccounts |
> | Action | Microsoft.MixedReality/SpatialAnchorsAccounts/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för Microsoft. MixedReality/SpatialAnchorsAccounts |
> | Action | Microsoft. MixedReality/SpatialAnchorsAccounts/providers/Microsoft. Insights/metricDefinitions/Read | Hämtar tillgängliga mått för Microsoft. MixedReality/SpatialAnchorsAccounts |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | DataAction | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | DataAction | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uppdatera egenskaper för spatiala ankare |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.NetApp/locations/operationresults/read | Läser en åtgärds resultat resurs. |
> | Action | Microsoft. NetApp/locations/Read | Läser en resurs för tillgänglighets kontroll. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/delete | Tar bort en pool resurs. |
> | Action | Microsoft. NetApp/netAppAccounts/capacityPools/Read | Läser en pool resurs. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/delete | Tar bort en volym resurs. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/mountTargets/read | Läser en resurs för monterings mål. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/read | Läser en volym resurs. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/delete | Tar bort en ögonblicks bild resurs. |
> | Action | Microsoft. NetApp/netAppAccounts/capacityPools/Volumes/snaps hots/Read | Läser en ögonblicks bild resurs. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/snapshots/write | Skriver en ögonblicks bild resurs. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/volumes/write | Skriver en volym resurs. |
> | Action | Microsoft.NetApp/netAppAccounts/capacityPools/write | Skriver en grupp resurs. |
> | Action | Microsoft. NetApp/netAppAccounts/Delete | Tar bort en konto resurs. |
> | Action | Microsoft. NetApp/netAppAccounts/Read | Läser en konto resurs. |
> | Action | Microsoft. NetApp/netAppAccounts/Write | Skriver en konto resurs. |
> | Action | Microsoft.NetApp/Operations/read | Läser en åtgärds resurser. |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Network/applicationGatewayAvailableRequestHeaders/read | Hämta Application Gateway tillgängliga begärandehuvuden |
> | Action | Microsoft.Network/applicationGatewayAvailableResponseHeaders/read | Hämta Application Gateway tillgängligt svars huvud |
> | Action | Microsoft.Network/applicationGatewayAvailableServerVariables/read | Hämta Application Gateway tillgängliga servervariabler |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/predefinedPolicies/read | Application Gateway en fördefinierad SSL-princip |
> | Action | Microsoft.Network/applicationGatewayAvailableSslOptions/read | Application Gateway tillgängliga SSL-alternativ |
> | Action | Microsoft. Network/applicationGatewayAvailableWafRuleSets/Read | Hämtar Application Gateway tillgängliga WAF-regel uppsättningar |
> | Action | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter till en programgateways backend-adresspool. Det går inte att avisera. |
> | Action | Microsoft. Network/applicationGateways/backendhealth/Action | Hämtar en server del hälso tillstånd för Application Gateway |
> | Action | Microsoft. Network/applicationGateways/Delete | Tar bort en Application Gateway |
> | Action | Microsoft.Network/applicationGateways/read | Hämtar en Application Gateway |
> | Action | Microsoft.Network/applicationGateways/start/action | Startar en Application Gateway |
> | Action | Microsoft.Network/applicationGateways/stop/action | Stoppar en Programgateway |
> | Action | Microsoft.Network/applicationGateways/write | Skapar en Application Gateway eller uppdaterar en Programgateway |
> | Action | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Delete | Tar bort en Application Gateway WAF-princip |
> | Action | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Read | Hämtar en Application Gateway WAF-princip |
> | Action | Microsoft. Network/ApplicationGatewayWebApplicationFirewallPolicies/Write | Skapar en Application Gateway WAF-princip eller uppdaterar en Application Gateway WAF-princip |
> | Action | Microsoft. Network/applicationSecurityGroups/Delete | Tar bort en program säkerhets grupp |
> | Action | Microsoft. Network/applicationSecurityGroups/joinIpConfiguration/Action | Ansluter en IP-konfiguration till program säkerhets grupper. Det går inte att avisera. |
> | Action | Microsoft.Network/applicationSecurityGroups/joinNetworkSecurityRule/action | Kopplar en säkerhets regel till program säkerhets grupper. Det går inte att avisera. |
> | Action | Microsoft.Network/applicationSecurityGroups/listIpConfigurations/action | Listar IP-konfigurationer i ApplicationSecurityGroup |
> | Action | Microsoft. Network/applicationSecurityGroups/Read | Hämtar ett ID för program säkerhets gruppen. |
> | Action | Microsoft.Network/applicationSecurityGroups/write | Skapar en program säkerhets grupp eller uppdaterar en befintlig program säkerhets grupp. |
> | Action | Microsoft.Network/azureFirewallFqdnTags/read | Hämtar Azure Firewall FQDN-Taggar |
> | Action | Microsoft. Network/azurefirewalls/Delete | Ta bort Azure-brandvägg |
> | Action | Microsoft.Network/azurefirewalls/read | Hämta Azure-brandvägg |
> | Action | Microsoft.Network/azurefirewalls/write | Skapar eller uppdaterar en Azure-brandvägg |
> | Action | Microsoft. Network/bastionHosts/Delete | Tar bort en skydds-värd |
> | Action | Microsoft. Network/bastionHosts/Read | Hämtar en skydds-värd |
> | Action | Microsoft. Network/bastionHosts/Write | Skapa eller uppdatera en skydds-värd |
> | Action | Microsoft.Network/bgpServiceCommunities/read | Hämta BGP-tjänstecommunities |
> | Action | Microsoft.Network/checkFrontDoorNameAvailability/action | Kontrollerar om ett namn på en front dörr är tillgängligt |
> | Action | Microsoft.Network/checkTrafficManagerNameAvailability/action | Kontrollerar tillgängligheten för ett Traffic Manager relativt DNS-namn. |
> | Action | Microsoft. Network/Connections/Delete | Tar bort VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/Connections/Read | Hämtar VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/Connections/REVOKE/åtgärd | Markerar en Express väg anslutnings status som återkallad |
> | Action | Microsoft.Network/connections/sharedkey/action | Hämta VirtualNetworkGatewayConnection-SharedKey |
> | Action | Microsoft. Network/Connections/sharedKey/Read | Hämtar VirtualNetworkGatewayConnection-SharedKey |
> | Action | Microsoft.Network/connections/sharedKey/write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection-SharedKey |
> | Action | Microsoft.Network/connections/vpndeviceconfigurationscript/action | Hämtar VPN-enhetens konfiguration av VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/Connections/Write | Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection |
> | Action | Microsoft.Network/ddosCustomPolicies/delete | Tar bort en DDoS anpassad princip |
> | Action | Microsoft. Network/ddosCustomPolicies/Read | Hämtar en DDoS anpassad princip definitions definition |
> | Action | Microsoft.Network/ddosCustomPolicies/write | Skapar en anpassad princip för DDoS eller uppdaterar en befintlig DDoS anpassad princip |
> | Action | Microsoft.Network/ddosProtectionPlans/delete | Tar bort en DDoS Protection Plan |
> | Action | Microsoft.Network/ddosProtectionPlans/join/action | Kopplar en DDoS Protection-plan. Det går inte att avisera. |
> | Action | Microsoft.Network/ddosProtectionPlans/read | Hämtar ett DDoS Protection Plan |
> | Action | Microsoft.Network/ddosProtectionPlans/write | Skapar en DDoS Protection plan eller uppdaterar en DDoS Protection Plan  |
> | Action | Microsoft. Network/dnsoperationresults/Read | Hämtar resultat från en DNS-åtgärd |
> | Action | Microsoft. Network/dnsoperationstatuses/Read | Hämtar status för en DNS-åtgärd  |
> | Action | Microsoft. Network/dnszones/A/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "A" från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/A/Read | Hämta post uppsättningen av typen "A" i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/A/write | Skapa eller uppdatera en post uppsättning av typen "A" i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft.Network/dnszones/AAAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv AAAA från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/AAAA/Read | Hämta post uppsättningen av typen AAAA i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/AAAA/write | Skapa eller uppdatera en post uppsättning av typen AAAA i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/dnszones/all/Read | Hämtar DNS-postuppsättningar mellan typer |
> | Action | Microsoft.Network/dnszones/CAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv CAA från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/CAA/Read | Hämta post uppsättningen av typen ' CAA ' i JSON-format. Post uppsättningen innehåller TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/CAA/write | Skapa eller uppdatera en post uppsättning av typen ' CAA ' i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft.Network/dnszones/CNAME/delete | Ta bort post uppsättningen med ett angivet namn och skriv CNAME från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/CNAME/Read | Hämta post uppsättningen av typen CNAME i JSON-format. Post uppsättningen innehåller TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/CNAME/write | Skapa eller uppdatera en post uppsättning av typen CNAME i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/dnszones/Delete | Ta bort DNS-zonen i JSON-format. Zon egenskaperna är taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. |
> | Action | Microsoft. Network/dnszones/MX/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "MX" från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/MX/Read | Hämta post uppsättningen av typen MX i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/MX/write | Skapa eller uppdatera en post uppsättning av typen MX i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/dnszones/NS/Delete | Tar bort DNS-postuppsättningen av typen NS |
> | Action | Microsoft. Network/dnszones/NS/Read | Hämtar DNS-post uppsättning av typen NS |
> | Action | Microsoft. Network/dnszones/NS/Write | Skapar eller uppdaterar DNS-post uppsättning av typen NS |
> | Action | Microsoft. Network/dnszones/PTR/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "PTR" från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/PTR/Read | Hämta post uppsättningen av typen "PTR" i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft. Network/dnszones/PTR/Write | Skapa eller uppdatera en post uppsättning av typen PTR i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/dnszones/Read | Hämta DNS-zonen i JSON-format. Zon egenskaperna är taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. Observera att det här kommandot inte hämtar de post uppsättningar som ingår i zonen. |
> | Action | Microsoft. Network/dnszones/Recordset/Read | Hämtar DNS-postuppsättningar mellan typer |
> | Action | Microsoft. Network/dnszones/SOA/Read | Hämtar DNS-post uppsättning av typen SOA |
> | Action | Microsoft. Network/dnszones/SOA/Write | Skapar eller uppdaterar DNS-post uppsättning av typen SOA |
> | Action | Microsoft. Network/dnszones/SRV/Delete | Ta bort post uppsättningen med det angivna namnet och skriv-SRV från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/SRV/Read | Hämta post uppsättningen av typen "SRV" i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/SRV/write | Skapa eller uppdatera post uppsättning av typen SRV |
> | Action | Microsoft.Network/dnszones/TXT/delete | Ta bort post uppsättningen med ett angivet namn och skriv TXT från en DNS-zon. |
> | Action | Microsoft. Network/dnszones/TXT/Read | Hämta post uppsättningen av typen ' TXT ' i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/dnszones/TXT/write | Skapa eller uppdatera en post uppsättning av typen ' TXT ' i en DNS-zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/dnszones/Write | Skapa eller uppdatera en DNS-zon i en resurs grupp.  Används för att uppdatera taggarna på en DNS-zon resurs. Observera att det här kommandot inte kan användas för att skapa eller uppdatera post uppsättningar i zonen. |
> | Action | Microsoft. Network/expressRouteCircuits/Authorizations/Delete | Tar bort en ExpressRouteCircuit-auktorisering |
> | Action | Microsoft. Network/expressRouteCircuits/auktoriseringar/läsa | Hämtar en ExpressRouteCircuit-auktorisering |
> | Action | Microsoft.Network/expressRouteCircuits/authorizations/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-auktorisering |
> | Action | Microsoft. Network/expressRouteCircuits/Delete | Tar bort en ExpressRouteCircuit |
> | Action | Microsoft. Network/expressRouteCircuits/JOIN/åtgärd | Ansluter till en Express Route-krets. Det går inte att avisera. |
> | Action | Microsoft. Network/expressRouteCircuits/peering/arpTables/Action | Hämtar en ExpressRouteCircuit peering-ArpTable |
> | Action | Microsoft. Network/expressRouteCircuits/peering/Connections/Delete | Tar bort en ExpressRouteCircuit-anslutning |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/read | Hämtar en ExpressRouteCircuit-anslutning |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/connections/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-anslutnings resurs |
> | Action | Microsoft. Network/expressRouteCircuits/peering/to delete | Tar bort en ExpressRouteCircuit-peering |
> | Action | Microsoft. Network/expressRouteCircuits/peering/peerConnections/Read | Hämtar anslutning till peer Express Route-kretsen |
> | Action | Microsoft. Network/expressRouteCircuits/motparter/Read | Hämtar en ExpressRouteCircuit-peering |
> | Action | Microsoft. Network/expressRouteCircuits/peering/routeTables/Action | Hämtar en ExpressRouteCircuit peering-RouteTable |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/routeTablesSummary/action | Hämtar en ExpressRouteCircuit peering RouteTable-Sammanfattning |
> | Action | Microsoft.Network/expressRouteCircuits/peerings/stats/read | Hämtar en ExpressRouteCircuit-peering stat |
> | Action | Microsoft. Network/expressRouteCircuits/motparter/Write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit-peering |
> | Action | Microsoft.Network/expressRouteCircuits/read | Hämta en ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCircuits/stats/read | Hämtar en ExpressRouteCircuit stat |
> | Action | Microsoft.Network/expressRouteCircuits/write | Skapar eller uppdaterar en befintlig ExpressRouteCircuit |
> | Action | Microsoft.Network/expressRouteCrossConnections/join/action | Ansluter till en Express vägs kors anslutning. Det går inte att avisera. |
> | Action | Microsoft. Network/expressRouteCrossConnections/peering/arpTables/Action | Hämtar en ARP-tabell med snabb flödes Cross-anslutning |
> | Action | Microsoft. Network/expressRouteCrossConnections/peering/to delete | Tar bort en Express-routning mellan anslutningar |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/read | Hämtar en Express routning mellan anslutningar |
> | Action | Microsoft. Network/expressRouteCrossConnections/peering/routeTables/Action | Hämtar en Express vägs kors anslutning för peering Route Table |
> | Action | Microsoft.Network/expressRouteCrossConnections/peerings/routeTableSummary/action | Hämtar en Sammanhangs oberoende kopplings väg tabell Sammanfattning för en Express Route |
> | Action | Microsoft. Network/expressRouteCrossConnections/motparter/Write | Skapar en ExpressRoute-peering med Express routning eller uppdaterar en befintlig snabb routning mellan anslutningar |
> | Action | Microsoft.Network/expressRouteCrossConnections/read | Hämta kors anslutning för Express Route |
> | Action | Microsoft. Network/expressRouteGateways/expressRouteConnections/Delete | Tar bort en Express Route-anslutning |
> | Action | Microsoft. Network/expressRouteGateways/expressRouteConnections/Read | Hämtar en Express Route-anslutning |
> | Action | Microsoft. Network/expressRouteGateways/expressRouteConnections/Write | Skapar en Express Route-anslutning eller uppdaterar en befintlig Express Route-anslutning |
> | Action | Microsoft. Network/expressRouteGateways/JOIN/åtgärd | Ansluter till en Express Route-Gateway. Det går inte att avisera. |
> | Action | Microsoft. Network/expressRouteGateways/Read | Hämta en Express Route-Gateway |
> | Action | Microsoft.Network/expressRoutePorts/delete | Tar bort ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/join/action | Ansluter till Express Route-portar. Det går inte att avisera. |
> | Action | Microsoft.Network/expressRoutePorts/links/read | Hämtar ExpressRouteLink |
> | Action | Microsoft.Network/expressRoutePorts/read | Hämtar ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePorts/write | Skapar eller uppdaterar ExpressRoutePorts |
> | Action | Microsoft.Network/expressRoutePortsLocations/read | Hämta platser för Express Route-portar |
> | Action | Microsoft.Network/expressRouteServiceProviders/read | Hämtar Express Route Service-leverantörer |
> | Action | Microsoft. Network/firewallPolicies/Delete | Tar bort en brand Väggs princip |
> | Action | Microsoft. Network/firewallPolicies/JOIN/åtgärd | Ansluter till en brand Väggs princip. Det går inte att avisera. |
> | Action | Microsoft. Network/firewallPolicies/Read | Hämtar en brand Väggs princip |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/Delete | Tar bort en regel grupp för brand Väggs principer |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/Read | Hämtar en regel grupp för brand Väggs principer |
> | Action | Microsoft. Network/firewallPolicies/ruleGroups/Write | Skapar en regel grupp för brand Väggs policy eller uppdaterar en befintlig regel grupp för brand Väggs policy |
> | Action | Microsoft. Network/firewallPolicies/Write | Skapar en brand Väggs princip eller uppdaterar en befintlig brand Väggs princip |
> | Action | Microsoft. Network/frontDoors/backendPools/Delete | Tar bort en backend-pool |
> | Action | Microsoft. Network/frontDoors/backendPools/Read | Hämtar en backend-pool |
> | Action | Microsoft. Network/frontDoors/backendPools/Write | Skapar eller uppdaterar en backend-pool |
> | Action | Microsoft. Network/frontDoors/Delete | Tar bort en front dörr |
> | Action | Microsoft. Network/frontDoors/frontendEndpoints/Delete | Tar bort en frontend-slutpunkt |
> | Action | Microsoft. Network/frontDoors/frontendEndpoints/disableHttps/Action | Inaktiverar HTTPS på en frontend-slutpunkt |
> | Action | Microsoft.Network/frontDoors/frontendEndpoints/enableHttps/action | Aktiverar HTTPS på en frontend-slutpunkt |
> | Action | Microsoft. Network/frontDoors/frontendEndpoints/Read | Hämtar en frontend-slutpunkt |
> | Action | Microsoft. Network/frontDoors/frontendEndpoints/Write | Skapar eller uppdaterar en klient delens slut punkt |
> | Action | Microsoft. Network/frontDoors/healthProbeSettings/Delete | Tar bort inställningar för hälso avsökning |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/read | Hämtar inställningar för hälso avsökning |
> | Action | Microsoft.Network/frontDoors/healthProbeSettings/write | Skapar eller uppdaterar inställningar för hälso avsökning |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/delete | Skapar eller uppdaterar inställningar för belastnings utjämning |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/read | Hämtar inställningar för belastnings utjämning |
> | Action | Microsoft.Network/frontDoors/loadBalancingSettings/write | Skapar eller uppdaterar inställningar för belastnings utjämning |
> | Action | Microsoft. Network/frontDoors/rensning/åtgärd | Rensa cachelagrat innehåll från en front dörr |
> | Action | Microsoft. Network/frontDoors/Read | Hämtar en front dörr |
> | Action | Microsoft.Network/frontDoors/routingRules/delete | Tar bort en regel för routning |
> | Action | Microsoft.Network/frontDoors/routingRules/read | Hämtar en regel för routning |
> | Action | Microsoft.Network/frontDoors/routingRules/write | Skapar eller uppdaterar en regel för routning |
> | Action | Microsoft.Network/frontDoors/validateCustomDomain/action | Verifierar en frontend-slutpunkt för en frontend-dörr |
> | Action | Microsoft.Network/frontDoors/write | Skapar eller uppdaterar en frontend-dörr |
> | Action | Microsoft. Network/frontDoorWebApplicationFirewallManagedRuleSets/Read | Hämtar hanterade regel uppsättningar för webb programs brand vägg |
> | Action | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Delete | Tar bort en brand Väggs princip för webb program |
> | Action | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Read | Hämtar en brand Väggs princip för webb program |
> | Action | Microsoft. Network/frontDoorWebApplicationFirewallPolicies/Write | Skapar eller uppdaterar en brand Väggs princip för webb program |
> | Action | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en backend-adresspool för belastnings utjämning. Det går inte att avisera. |
> | Action | Microsoft. Network/belastningsutjämnare/backendAddressPools/Read | Hämtar en definition för Server dels adress för belastnings utjämning |
> | Action | Microsoft. Network/belastningsutjämnare/Delete | Tar bort en belastningsutjämnare |
> | Action | Microsoft. Network/belastningsutjämnare/frontendIPConfigurations/JOIN/åtgärd | Ansluter till en IP-konfiguration för Load Balancer-frontend. Det går inte att avisera. |
> | Action | Microsoft. Network/belastningsutjämnare/frontendIPConfigurations/Read | Hämtar en IP-konfigurations definition för belastningsutjämnarens klient del |
> | Action | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter en inkommande NAT-pool för belastningsutjämnaren. Det går inte att avisera. |
> | Action | Microsoft. Network/belastningsutjämnare/inboundNatPools/Read | Hämtar en belastnings Utjämnings definition för inkommande NAT-pool |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/delete | Tar bort en inkommande NAT-regel för belastnings utjämning |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Det går inte att avisera. |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/read | Hämtar en inkommande NAT-regel definition för belastnings utjämning |
> | Action | Microsoft.Network/loadBalancers/inboundNatRules/write | Skapar en inkommande NAT-regel för belastnings utjämning eller uppdaterar en befintlig inkommande NAT-regel för belastnings utjämning |
> | Action | Microsoft. Network/belastningsutjämnare/loadBalancingRules/Read | Hämtar en regel definition för belastnings utjämning för belastnings utjämning |
> | Action | Microsoft.Network/loadBalancers/networkInterfaces/read | Hämtar referenser till alla nätverks gränssnitt under en belastningsutjämnare |
> | Action | Microsoft.Network/loadBalancers/outboundRules/read | Hämtar en regel definition för utgående belastningsutjämnare |
> | Action | Microsoft.Network/loadBalancers/probes/join/action | Tillåter att avsökningar av en belastningsutjämnare används. Till exempel kan den här behörighetens egenskap healthProbe-egenskap för VM Scale set referera till avsökningen. Det går inte att avisera. |
> | Action | Microsoft. Network/belastningsutjämnare/PROBES/Read | Hämtar en belastnings Utjämnings avsökning |
> | Action | Microsoft.Network/loadBalancers/read | Hämtar en belastnings Utjämnings definition |
> | Action | Microsoft. Network/belastningsutjämnare/virtualMachines/Read | Hämtar referenser till alla virtuella datorer under en belastningsutjämnare |
> | Action | Microsoft. Network/belastningsutjämnare/Write | Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare |
> | Action | Microsoft. Network/localnetworkgateways/Delete | Tar bort LocalNetworkGateway |
> | Action | Microsoft. Network/localnetworkgateways/Read | Hämtar LocalNetworkGateway |
> | Action | Microsoft. Network/localnetworkgateways/Write | Skapar eller uppdaterar en befintlig LocalNetworkGateway |
> | Action | Microsoft.Network/locations/autoApprovedPrivateLinkServices/read | Hämtar automatiskt godkända privata länk tjänster |
> | Action | Microsoft. Network/locations/availableDelegations/Read | Hämtar tillgängliga delegeringar |
> | Action | Microsoft. Network/locations/availablePrivateEndpointTypes/Read | Hämtar tillgängliga privata slut punkts resurser |
> | Action | Microsoft. Network/locations/bareMetalTenants/Action | Allokerar eller validerar en Bare Metal-klient |
> | Action | Microsoft.Network/locations/checkAcceleratedNetworkingSupport/action | Kontrollerar accelererat nätverks stöd |
> | Action | Microsoft. Network/locations/checkDnsNameAvailability/Read | Kontrollerar om DNS-etiketten är tillgänglig på den angivna platsen |
> | Action | Microsoft.Network/locations/checkPrivateLinkServiceVisibility/action | Kontrollerar synlighet för privata länk tjänster |
> | Action | Microsoft.Network/locations/operationResults/read | Hämtar åtgärds resultat för en asynkron POST-eller BORTTAGNINGs åtgärd |
> | Action | Microsoft. Network/locations/Operations/Read | Hämtar åtgärds resurs som representerar status för en asynkron åtgärd |
> | Action | Microsoft.Network/locations/serviceTags/read | Hämta Service märken |
> | Action | Microsoft. Network/locations/supportedVirtualMachineSizes/Read | Hämtar storlekar för virtuella datorer som stöds |
> | Action | Microsoft. Network/locations/usages/Read | Hämtar användnings statistik för resurser |
> | Action | Microsoft. Network/locations/virtualNetworkAvailableEndpointServices/Read | Hämtar en lista över tillgängliga Virtual Network slut punkts tjänster |
> | Action | Microsoft.Network/networkIntentPolicies/delete | Tar bort en princip för nätverks avsikt |
> | Action | Microsoft.Network/networkIntentPolicies/read | Hämtar en beskrivning av princip för nätverks avsikt |
> | Action | Microsoft.Network/networkIntentPolicies/write | Skapar en princip för nätverks avsikt eller uppdaterar en befintlig princip för nätverks avsikt |
> | Action | Microsoft.Network/networkInterfaces/delete | Tar bort ett nätverks gränssnitt |
> | Action | Microsoft.Network/networkInterfaces/effectiveNetworkSecurityGroups/action | Hämta nätverks säkerhets grupper som kon figurer ATS på nätverks gränssnittet för den virtuella datorn |
> | Action | Microsoft.Network/networkInterfaces/effectiveRouteTable/action | Hämta routningstabellen som kon figurer ATS på nätverks gränssnittet för den virtuella datorn |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/join/action | Ansluter till en IP-konfiguration för nätverks gränssnittet. Det går inte att avisera. |
> | Action | Microsoft.Network/networkInterfaces/ipconfigurations/read | Hämtar en IP-konfigurationsfil för nätverks gränssnitt.  |
> | Action | Microsoft.Network/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverks gränssnitt. Det går inte att avisera. |
> | Action | Microsoft.Network/networkInterfaces/loadBalancers/read | Hämtar alla belastningsutjämnare som nätverks gränssnittet ingår i |
> | Action | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverks gränssnitt.  |
> | Action | Microsoft. Network/networkInterfaces/tapConfigurations/Delete | Tar bort ett nätverks gränssnitt som trycker på konfiguration. |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/read | Hämtar ett nätverks gränssnitt genom att trycka på konfiguration. |
> | Action | Microsoft.Network/networkInterfaces/tapConfigurations/write | Skapar ett nätverks gränssnitt för att trycka på konfiguration eller uppdatera ett befintligt nätverks gränssnitt tryck på konfiguration. |
> | Action | Microsoft.Network/networkInterfaces/write | Skapar ett nätverks gränssnitt eller uppdaterar ett befintligt nätverks gränssnitt.  |
> | Action | Microsoft.Network/networkProfiles/delete | Tar bort en nätverks profil |
> | Action | Microsoft.Network/networkProfiles/read | Hämtar en nätverks profil |
> | Action | Microsoft.Network/networkProfiles/removeContainers/action | Tar bort behållare |
> | Action | Microsoft.Network/networkProfiles/setContainers/action | Anger behållare |
> | Action | Microsoft.Network/networkProfiles/setNetworkInterfaces/action | Anger behållarens nätverks gränssnitt |
> | Action | Microsoft.Network/networkProfiles/write | Skapar eller uppdaterar en nätverks profil |
> | Action | Microsoft. Network/networkSecurityGroups/defaultSecurityRules/Read | Hämtar en standard säkerhets regel definition |
> | Action | Microsoft. Network/networkSecurityGroups/Delete | Tar bort en nätverks säkerhets grupp |
> | Action | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | Action | Microsoft.Network/networkSecurityGroups/read | Hämtar en definition för nätverks säkerhets grupp |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/delete | Tar bort en säkerhets regel |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/read | Hämtar en säkerhets regel definition |
> | Action | Microsoft.Network/networkSecurityGroups/securityRules/write | Skapar en säkerhets regel eller uppdaterar en befintlig säkerhets regel |
> | Action | Microsoft.Network/networkSecurityGroups/write | Skapar en nätverks säkerhets grupp eller uppdaterar en befintlig nätverks säkerhets grupp |
> | Action | Microsoft. Network/networkWatchers/availableProvidersList/Action | Returnerar alla tillgängliga Internet tjänst leverantörer för en angiven Azure-region. |
> | Action | Microsoft.Network/networkWatchers/azureReachabilityReport/action | Returnerar den relativa latens poängen för Internet tjänst leverantörer från en angiven plats till Azure-regioner. |
> | Action | Microsoft. Network/networkWatchers/configureFlowLog/Action | Konfigurerar flödes loggning för en mål resurs. |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Delete | Tar bort en anslutnings övervakare |
> | Action | Microsoft.Network/networkWatchers/connectionMonitors/query/action | Fråga om övervakning av anslutningar mellan angivna slut punkter |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Read | Hämta information om anslutnings övervakaren |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/start/Action | Starta övervakning av anslutningar mellan angivna slut punkter |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/stop/action | Stoppa/pausa övervakning av anslutningar mellan angivna slut punkter |
> | Action | Microsoft. Network/networkWatchers/connectionMonitors/Write | Skapar en anslutnings övervakare |
> | Action | Microsoft. Network/networkWatchers/connectivityCheck/Action | Verifierar möjligheten att upprätta en direkt TCP-anslutning från en virtuell dator till en specifik slut punkt, inklusive en annan virtuell dator eller en godtycklig fjärrserver. |
> | Action | Microsoft. Network/networkWatchers/Delete | Tar bort en nätverks övervakare |
> | Action | Microsoft.Network/networkWatchers/ipFlowVerify/action | Returnerar om paketet tillåts eller nekas till eller från ett visst mål. |
> | Action | Microsoft. Network/networkWatchers/linser/Delete | Tar bort en lins |
> | Action | Microsoft. Network/networkWatchers/linser/fråga/åtgärd | Fråga vid övervakning av nätverks trafik på en angiven slut punkt |
> | Action | Microsoft. Network/networkWatchers/linser/läsa | Hämta lins information |
> | Action | Microsoft. Network/networkWatchers/linser/start/åtgärd | Starta övervakning av nätverks trafik på en angiven slut punkt |
> | Action | Microsoft. Network/networkWatchers/linser/stoppa/åtgärd | Stoppa/pausa övervakning av nätverks trafik på en angiven slut punkt |
> | Action | Microsoft. Network/networkWatchers/linser/Write | Skapar en lins |
> | Action | Microsoft.Network/networkWatchers/networkConfigurationDiagnostic/action | Diagnostik för nätverks konfiguration. |
> | Action | Microsoft. Network/networkWatchers/nextHop/Action | Returnera nästa hopp typ och nästa hopp-IP-adress för en angiven mål-och mål-IP-adress. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/Delete | Tar bort en paket avbildning |
> | Action | Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Hämtar information om egenskaper och status för en paket fångst resurs. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/Read | Hämta paket fångsts definitionen |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/stop/action | Stoppa den pågående paket insamlings sessionen. |
> | Action | Microsoft. Network/networkWatchers/packetCaptures/Write | Skapar en paket fångst |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/Delete | Tar bort en PingMesh |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/Read | Hämta information om PingMesh |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/start/Action | Starta PingMesh mellan angivna virtuella datorer |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/stop/action | Stoppa PingMesh mellan angivna virtuella datorer |
> | Action | Microsoft. Network/networkWatchers/pingMeshes/Write | Skapar en PingMesh |
> | Action | Microsoft.Network/networkWatchers/queryConnectionMonitors/action | Batch-fråga för övervakning av anslutningar mellan angivna slut punkter |
> | Action | Microsoft.Network/networkWatchers/queryFlowLogStatus/action | Hämtar status för flödes loggning på en resurs. |
> | Action | Microsoft.Network/networkWatchers/queryTroubleshootResult/action | Hämtar fel söknings resultatet från den tidigare körningen eller fel söknings åtgärden som körs. |
> | Action | Microsoft. Network/networkWatchers/Read | Hämta Network Watcher-definitionen |
> | Action | Microsoft.Network/networkWatchers/securityGroupView/action | Visa de konfigurerade och effektiva reglerna för nätverks säkerhets grupper som tillämpas på en virtuell dator. |
> | Action | Microsoft. Network/networkWatchers/topologi/åtgärd | Hämtar resursernas vy på nätverks nivå och deras relationer i en resurs grupp. |
> | Action | Microsoft. Network/networkWatchers/Felsök/åtgärd | Startar fel sökning på en nätverks resurs i Azure. |
> | Action | Microsoft. Network/networkWatchers/Write | Skapar en nätverks övervakare eller uppdaterar en befintlig nätverks övervakare |
> | Action | Microsoft.Network/operations/read | Hämta tillgängliga åtgärder |
> | Action | Microsoft. Network/p2sVpnGateways/Delete | Tar bort en P2SVpnGateway. |
> | Action | Microsoft.Network/p2sVpnGateways/generatevpnprofile/action | Generera VPN-profil för P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/getp2svpnconnectionhealth/action | Hämtar hälso tillståndet för P2S VPN-anslutning för P2SVpnGateway |
> | Action | Microsoft.Network/p2sVpnGateways/read | Hämtar en P2SVpnGateway. |
> | Action | Microsoft. Network/p2sVpnGateways/Write | Placerar en P2SVpnGateway. |
> | Action | Microsoft.Network/privateDnsOperationResults/read | Hämtar resultat från en Privat DNS-åtgärd |
> | Action | Microsoft.Network/privateDnsOperationStatuses/read | Hämtar status för en Privat DNS-åtgärd |
> | Action | Microsoft. Network/privateDnsZones/A/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "A" från en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/A/Read | Hämta post uppsättningen av typen "A" i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/privateDnsZones/A/write | Skapa eller uppdatera en post uppsättning av typen "A" i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/delete | Ta bort post uppsättningen med ett angivet namn och skriv AAAA från en Privat DNS zon. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/read | Hämta post uppsättningen av typen AAAA i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/privateDnsZones/AAAA/write | Skapa eller uppdatera en post uppsättning av typen AAAA i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/privateDnsZones/ALL/Read | Hämtar Privat DNS post uppsättningar mellan typer |
> | Action | Microsoft.Network/privateDnsZones/CNAME/delete | Ta bort post uppsättningen med ett angivet namn och skriv CNAME från en Privat DNS zon. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/read | Hämta post uppsättningen av typen CNAME i en Privat DNS zon i JSON-format. |
> | Action | Microsoft.Network/privateDnsZones/CNAME/write | Skapa eller uppdatera en post uppsättning av typen CNAME i en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/Delete | Ta bort en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/MX/Delete | Ta bort post uppsättningen med ett angivet namn och skriv in MX från en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/MX/Read | Hämta post uppsättningen av typen MX i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/privateDnsZones/MX/write | Skapa eller uppdatera en post uppsättning av typen MX i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/privateDnsZones/PTR/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "PTR" från en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/PTR/Read | Hämta post uppsättningen av typen ' PTR ' i en Privat DNS zon, i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft. Network/privateDnsZones/PTR/Write | Skapa eller uppdatera en post uppsättning av typen PTR i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft. Network/privateDnsZones/Read | Hämta Privat DNS zon egenskaper i JSON-format. Observera att det här kommandot inte hämtar de virtuella nätverk som Privat DNS zonen är länkad till eller vilka post uppsättningar som ingår i zonen. |
> | Action | Microsoft. Network/privateDnsZones/Recordset/Read | Hämtar Privat DNS post uppsättningar mellan typer |
> | Action | Microsoft. Network/privateDnsZones/SOA/Read | Hämta post uppsättningen av typen SOA i en Privat DNS zon i JSON-format. |
> | Action | Microsoft. Network/privateDnsZones/SOA/Write | Uppdatera en post uppsättning av typen SOA i en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/SRV/Delete | Ta bort post uppsättningen med ett angivet namn och skriv "SRV" från en Privat DNS zon. |
> | Action | Microsoft. Network/privateDnsZones/SRV/Read | Hämta post uppsättningen av typen SRV i en Privat DNS zon i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/privateDnsZones/SRV/write | Skapa eller uppdatera en post uppsättning av typen SRV i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft.Network/privateDnsZones/TXT/delete | Ta bort post uppsättningen med ett angivet namn och skriv TXT från en Privat DNS zon. |
> | Action | Microsoft.Network/privateDnsZones/TXT/read | Hämta post uppsättningen av typen ' TXT ' i en Privat DNS zon, i JSON-format. Post uppsättningen innehåller en lista med poster samt TTL, taggar och etag. |
> | Action | Microsoft.Network/privateDnsZones/TXT/write | Skapa eller uppdatera en post uppsättning av typen ' TXT ' i en Privat DNS zon. De angivna posterna ersätter de aktuella posterna i post uppsättningen. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/delete | Ta bort en Privat DNS zon länk till det virtuella nätverket. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/read | Hämta Privat DNS zon länken till det virtuella nätverkets egenskaper i JSON-format. |
> | Action | Microsoft.Network/privateDnsZones/virtualNetworkLinks/write | Skapa eller uppdatera en Privat DNS zon länk till ett virtuellt nätverk. |
> | Action | Microsoft. Network/privateDnsZones/Write | Skapa eller uppdatera en Privat DNS zon i en resurs grupp. Observera att det här kommandot inte kan användas för att skapa eller uppdatera virtuella nätverks länkar eller post uppsättningar i zonen. |
> | Action | Microsoft. Network/privateEndpoints/Delete | Tar bort en privat slut punkts resurs. |
> | Action | Microsoft. Network/privateEndpoints/Read | Hämtar en privat slut punkts resurs. |
> | Action | Microsoft. Network/privateEndpoints/Write | Skapar en ny privat slut punkt eller uppdaterar en befintlig privat slut punkt. |
> | Action | Microsoft.Network/privateLinkServices/delete | Tar bort en tjänst resurs för privata länkar. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/delete | Tar bort en privat slut punkts anslutning. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/read | Hämtar en anslutnings definition för privat slut punkt. |
> | Action | Microsoft.Network/privateLinkServices/privateEndpointConnections/write | Skapar en ny privat slut punkts anslutning eller uppdaterar en befintlig privat slut punkts anslutning. |
> | Action | Microsoft.Network/privateLinkServices/read | Hämtar en privat länk tjänst resurs. |
> | Action | Microsoft.Network/privateLinkServices/write | Skapar en ny privat länk-tjänst eller uppdaterar en befintlig privat länk-tjänst. |
> | Action | Microsoft. Network/publicIPAddresses/Delete | Tar bort en offentlig IP-adress. |
> | Action | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Det går inte att avisera. |
> | Action | Microsoft.Network/publicIPAddresses/read | Hämtar en offentlig IP-adress definition. |
> | Action | Microsoft. Network/publicIPAddresses/Write | Skapar en offentlig IP-adress eller uppdaterar en befintlig offentlig IP-adress.  |
> | Action | Microsoft. Network/publicIPPrefixes/Delete | Tar bort ett offentligt IP-prefix |
> | Action | Microsoft. Network/publicIPPrefixes/JOIN/åtgärd | Ansluter till en PublicIPPrefix. Det går inte att avisera. |
> | Action | Microsoft. Network/publicIPPrefixes/Read | Hämtar en offentlig IP-prefix definition |
> | Action | Microsoft. Network/publicIPPrefixes/Write | Skapar ett offentligt IP-prefix eller uppdaterar ett befintligt offentligt IP-prefix |
> | Action | Microsoft. Network/register/åtgärd | Registrerar prenumerationen |
> | Action | Microsoft. Network/routeFilters/Delete | Tar bort en flödes filter definition |
> | Action | Microsoft. Network/routeFilters/JOIN/åtgärd | Ansluter till ett flödes filter. Det går inte att avisera. |
> | Action | Microsoft. Network/routeFilters/Read | Hämtar en flödes filter definition |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/delete | Tar bort en regel definition för flödes filter |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/read | Hämtar en regel definition för väg filter |
> | Action | Microsoft.Network/routeFilters/routeFilterRules/write | Skapar en regel för väg filter eller uppdaterar en befintlig regel för väg filter |
> | Action | Microsoft. Network/routeFilters/Write | Skapar ett flödes filter eller uppdaterar ett befintligt flödes filter |
> | Action | Microsoft. Network/routeTables/Delete | Tar bort en väg tabells definition |
> | Action | Microsoft. Network/routeTables/JOIN/åtgärd | Kopplar ihop en routningstabell. Det går inte att avisera. |
> | Action | Microsoft. Network/routeTables/Read | Hämtar en väg tabells definition |
> | Action | Microsoft. Network/routeTables/routes/Delete | Tar bort en flödes definition |
> | Action | Microsoft. Network/routeTables/routes/Read | Hämtar en flödes definition |
> | Action | Microsoft. Network/routeTables/routes/Write | Skapar en väg eller uppdaterar en befintlig väg |
> | Action | Microsoft. Network/routeTables/Write | Skapar en routningstabell eller uppdaterar en befintlig väg tabell |
> | Action | Microsoft. Network/securegateways/Delete | Ta bort säker gateway |
> | Action | Microsoft. Network/securegateways/Read | Hämta säker gateway |
> | Action | Microsoft. Network/securegateways/Write | Skapar eller uppdaterar en säker gateway |
> | Action | Microsoft.Network/serviceEndpointPolicies/delete | Tar bort en tjänst slut punkts princip |
> | Action | Microsoft. Network/serviceEndpointPolicies/JOIN/åtgärd | Ansluter till en tjänst slut punkts princip. Det går inte att avisera. |
> | Action | Microsoft.Network/serviceEndpointPolicies/joinSubnet/action | Ansluter ett undernät till tjänstens slut punkts principer. Det går inte att avisera. |
> | Action | Microsoft. Network/serviceEndpointPolicies/Read | Hämtar en beskrivning av tjänstens slut punkts princip |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete | Tar bort en princip definition för tjänst slut punkt |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read | Hämtar en beskrivning av en princip definition för tjänst slut punkt |
> | Action | Microsoft.Network/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write | Skapar en princip definition för tjänst slut punkt eller uppdaterar en befintlig princip definition för tjänst slut punkt |
> | Action | Microsoft.Network/serviceEndpointPolicies/write | Skapar en tjänst slut punkts princip eller uppdaterar en befintlig tjänst slut punkts princip |
> | Action | Microsoft.Network/trafficManagerGeographicHierarchies/read | Hämtar Traffic Manager geografisk hierarki som innehåller regioner som kan användas med routningsmetod för geografisk trafik |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/delete | Tar bort en Azure-slutpunkt från en befintlig Traffic Managers profil. Traffic Manager upphör att vidarebefordra trafiken till den borttagna Azure-slutpunkt. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/read | Hämtar ett Azure-slutpunkt som tillhör en Traffic Manager profil, inklusive alla egenskaper för den Azure-slutpunkt. |
> | Action | Microsoft.Network/trafficManagerProfiles/azureEndpoints/write | Lägg till en ny Azure-slutpunkt i en befintlig Traffic Manager profil eller uppdatera egenskaperna för en befintlig Azure-slutpunkt i den Traffic Manager profilen. |
> | Action | Microsoft.Network/trafficManagerProfiles/delete | Ta bort Traffic Manager profilen. Alla inställningar som är associerade med Traffic Manager profilen går förlorade och profilen kan inte längre användas för att dirigera trafik. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/delete | Tar bort en extern slut punkt från en befintlig Traffic Manager-profil. Traffic Manager upphör att vidarebefordra trafik till den borttagna externa slut punkten. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/read | Hämtar en extern slut punkt som tillhör en Traffic Manager profil, inklusive alla egenskaper för den externa slut punkten. |
> | Action | Microsoft.Network/trafficManagerProfiles/externalEndpoints/write | Lägg till en ny extern slut punkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig extern slut punkt i Traffic Manager profilen. |
> | Action | Microsoft.Network/trafficManagerProfiles/heatMaps/read | Hämtar Traffic Manager värme kartan för den Traffic Manager profilen som innehåller antal frågor och svars tid för data efter plats och käll-IP. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/delete | Tar bort en kapslad slut punkt från en befintlig Traffic Manager-profil. Traffic Manager upphör att vidarebefordra trafik till den borttagna kapslade slut punkten. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/read | Hämtar en kapslad slut punkt som tillhör en Traffic Manager profil, inklusive alla egenskaper för den kapslade slut punkten. |
> | Action | Microsoft.Network/trafficManagerProfiles/nestedEndpoints/write | Lägg till en ny kapslad slut punkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig kapslad slut punkt i Traffic Manager profilen. |
> | Action | Microsoft.Network/trafficManagerProfiles/read | Hämta profil konfigurationen för Traffic Manager. Detta inkluderar DNS-inställningar, inställningar för trafik cirkulation, inställningar för slut punkts övervakning och listan över slut punkter som vidarebefordras av den här Traffic Manager profilen. |
> | Action | Microsoft.Network/trafficManagerProfiles/write | Skapa en Traffic Manager-profil eller ändra konfigurationen för en befintlig Traffic Manager-profil.<br>Detta inkluderar aktivering eller inaktive ring av en profil och ändring av DNS-inställningar, inställningar för trafikflöde eller inställningar för slut punkts övervakning.<br>Slut punkter som vidarebefordras av Traffic Managers profilen kan läggas till, tas bort, aktive ras eller inaktive ras. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/delete | Tar bort den prenumerations nivå nyckel som används för insamling av real tids användar mått. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/read | Hämtar den prenumerations nivå nyckel som används för insamling av real tids användar mått. |
> | Action | Microsoft.Network/trafficManagerUserMetricsKeys/write | Skapar en ny prenumerations nivå nyckel som ska användas för insamling av real tids användar mått. |
> | Action | Microsoft.Network/unregister/action | Avregistrerar prenumerationen |
> | Action | Microsoft.Network/virtualHubs/delete | Tar bort en virtuell hubb |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/delete | Tar bort en HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/read | Hämta en HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/hubVirtualNetworkConnections/write | Skapa eller uppdatera en HubVirtualNetworkConnection |
> | Action | Microsoft.Network/virtualHubs/read | Hämta en virtuell hubb |
> | Action | Microsoft.Network/virtualHubs/write | Skapa eller uppdatera en virtuell hubb |
> | Action | microsoft.network/virtualnetworkgateways/connections/read | Hämta VirtualNetworkGatewayConnection |
> | Action | Microsoft. Network/virtualNetworkGateways/Delete | Tar bort en virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/generatevpnclientpackage/Action | Generera VpnClient-paket för virtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/generatevpnprofile/action | Generera VpnProfile-paket för VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/getadvertisedroutes/action | Hämtar virtualNetworkGateway annonserade vägar |
> | Action | Microsoft. Network/virtualnetworkgateways/getbgppeerstatus/Action | Hämtar virtualNetworkGateway status för BGP-peer |
> | Action | microsoft.network/virtualnetworkgateways/getlearnedroutes/action | Hämtar virtualnetworkgateway-inlärda vägar |
> | Action | microsoft.network/virtualnetworkgateways/getvpnclientconnectionhealth/action | Hämta hälso tillstånd per VPN-klient för VirtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/getvpnclientipsecparameters/Action | Hämta Vpnclient IPsec-parametrar för VirtualNetworkGateway P2S-klienten. |
> | Action | microsoft.network/virtualnetworkgateways/getvpnprofilepackageurl/action | Hämtar URL: en för ett förgenererat profil paket för VPN-klienter |
> | Action | Microsoft.Network/virtualNetworkGateways/read | Hämtar en VirtualNetworkGateway |
> | Action | microsoft.network/virtualnetworkgateways/reset/action | Återställer en virtualNetworkGateway |
> | Action | Microsoft. Network/virtualnetworkgateways/resetvpnclientsharedkey/Action | Återställ Vpnclient-delad nyckel för VirtualNetworkGateway P2S-klienten. |
> | Action | microsoft.network/virtualnetworkgateways/setvpnclientipsecparameters/action | Ange Vpnclient IPsec-parametrar för VirtualNetworkGateway P2S-klienten. |
> | Action | Microsoft.Network/virtualnetworkgateways/supportedvpndevices/action | Visar en lista över VPN-enheter som stöds |
> | Action | Microsoft. Network/virtualNetworkGateways/Write | Skapar eller uppdaterar en VirtualNetworkGateway |
> | Action | Microsoft.Network/virtualNetworks/BastionHosts/action | Hämtar skydds värd referenser i en Virtual Network. |
> | Action | Microsoft. Network/virtualNetworks/bastionHosts/default/Action | Hämtar skydds värd referenser i en Virtual Network. |
> | Action | Microsoft. Network/virtualNetworks/checkIpAddressAvailability/Read | Kontrol lera om IP-adressen är tillgänglig på det angivna virtuella nätverket |
> | Action | Microsoft. Network/virtualNetworks/Delete | Tar bort ett virtuellt nätverk |
> | Action | Microsoft. Network/virtualNetworks/JOIN/åtgärd | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Action | Microsoft. Network/virtualNetworks/peer/Action | Peer-koppla ett virtuellt nätverk med ett annat virtuellt nätverk |
> | Action | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Action | Microsoft. Network/virtualNetworks/subnets/Delete | Tar bort ett undernät för virtuellt nätverk |
> | Action | Microsoft. Network/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Action | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | Action | Microsoft.Network/virtualNetworks/subnets/prepareNetworkPolicies/action | Förbereder ett undernät genom att tillämpa nödvändiga nätverks principer |
> | Action | Microsoft. Network/virtualNetworks/subnets/Read | Hämtar en under näts definition för virtuellt nätverk |
> | Action | Microsoft. Network/virtualNetworks/subnets/virtualMachines/Read | Hämtar referenser till alla virtuella datorer i ett undernät för virtuellt nätverk |
> | Action | Microsoft.Network/virtualNetworks/subnets/write | Skapar ett virtuellt nätverks under nät eller uppdaterar ett befintligt undernät för virtuellt nätverk |
> | Action | Microsoft. Network/virtualNetworks/usages/Read | Hämta IP-användning för varje undernät i det virtuella nätverket |
> | Action | Microsoft.Network/virtualNetworks/virtualMachines/read | Hämtar referenser till alla virtuella datorer i ett virtuellt nätverk |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Tar bort en virtuell nätverks-peering |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read | Hämtar en peering-definition för virtuellt nätverk |
> | Action | Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write | Skapar en virtuell nätverks-peering eller uppdaterar en befintlig peering för virtuellt nätverk |
> | Action | Microsoft.Network/virtualNetworks/write | Skapar ett virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk |
> | Action | Microsoft.Network/virtualNetworkTaps/delete | Ta bort Virtual Network tryck |
> | Action | Microsoft. Network/virtualNetworkTaps/JOIN/åtgärd | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Action | Microsoft.Network/virtualNetworkTaps/read | Hämta Virtual Network tryck |
> | Action | Microsoft.Network/virtualNetworkTaps/write | Skapa eller uppdatera Virtual Network tryck |
> | Action | Microsoft.Network/virtualWans/delete | Tar bort ett virtuellt WAN |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/delete | Tar bort en virtuell WAN-P2SVpnServerConfiguration |
> | Action | Microsoft.Network/virtualWans/p2sVpnServerConfigurations/read | Hämtar ett virtuellt WAN-P2SVpnServerConfiguration |
> | Action | Microsoft.network/virtualWans/p2sVpnServerConfigurations/write | Skapar en virtuell WAN-P2SVpnServerConfiguration eller uppdaterar en befintlig virtuell WAN-P2SVpnServerConfiguration |
> | Action | Microsoft.Network/virtualWans/read | Hämta ett virtuellt WAN |
> | Action | Microsoft.Network/virtualwans/supportedSecurityProviders/read | Hämtar VirtualWan säkerhets leverantörer som stöds. |
> | Action | Microsoft.Network/virtualWans/virtualHubs/read | Hämtar alla virtuella hubbar som refererar till ett virtuellt WAN. |
> | Action | Microsoft.Network/virtualwans/vpnconfiguration/action | Hämtar en VPN-konfiguration |
> | Action | Microsoft.Network/virtualWans/vpnSites/read | Hämtar alla VPN-platser som hänvisar till ett virtuellt WAN. |
> | Action | Microsoft.Network/virtualWans/write | Skapa eller uppdatera ett virtuellt WAN |
> | Action | Microsoft.Network/vpnGateways/delete | Tar bort en VpnGateway. |
> | Action | microsoft.network/vpngateways/listvpnconnectionshealth/action | Hämtar anslutnings hälsa för alla eller en delmängd av anslutningar på en VpnGateway |
> | Action | Microsoft.Network/vpnGateways/read | Hämtar en VpnGateway. |
> | Action | microsoft.network/vpngateways/reset/action | Återställer en VpnGateway |
> | Action | microsoft.network/vpnGateways/vpnConnections/delete | Tar bort en VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/read | Hämtar en VpnConnection. |
> | Action | microsoft.network/vpnGateways/vpnConnections/write | Placerar en VpnConnection. |
> | Action | Microsoft.Network/vpnGateways/write | Placerar en VpnGateway. |
> | Action | Microsoft.Network/vpnsites/delete | Tar bort en VPN-webbplats resurs. |
> | Action | Microsoft.Network/vpnsites/read | Hämtar en VPN-webbplats resurs. |
> | Action | Microsoft.Network/vpnsites/write | Skapar eller uppdaterar en VPN-webbplats resurs. |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.NotificationHubs/CheckNamespaceAvailability/action | Kontrollerar huruvida ett visst namnområdesresursnamn finns i NotificationHub-tjänsten. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/action | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/regenerateKeys/action | Auktoriseringsregel för namnområde: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas |
> | Action | Microsoft.NotificationHubs/Namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Action | Microsoft.NotificationHubs/Namespaces/CheckNotificationHubAvailability/action | Kontrollerar huruvida ett visst NotificationHub-namn finns i ett namnområde. |
> | Action | Microsoft.NotificationHubs/Namespaces/Delete | Ta bort namnområdesresurs |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/action | Hämta listan över auktoriseringsregler för Notification Hub |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/delete | Ta bort auktoriseringsregler för Notification Hub |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/listkeys/action | Hämta anslutningssträngen till meddelandehubben |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/read | Hämta listan över auktoriseringsregler för Notification Hub |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action | Auktoriseringsregel för Notification Hub: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules/write | Skapa auktoriseringsregler för Notification Hub och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/debugSend/action | Skicka ett push-testmeddelande. |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/Delete | Ta bort Notification Hub-resurs |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/metricDefinitions/read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/pnsCredentials/action | Hämta alla PNS-autentiseringsuppgifter för Notification Hub. Detta inkluderar, WNS, MPNS, APN, GCM och Baidu-autentiseringsuppgifter |
> | Action | Microsoft. NotificationHubs/namnrymder/NotificationHubs/Read | Hämta listan över resursbeskrivningar av Notification Hub |
> | Action | Microsoft.NotificationHubs/Namespaces/NotificationHubs/write | Skapa en Notification Hub och uppdatera dess egenskaper. Egenskaperna omfattar huvudsakligen PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL |
> | Action | Microsoft.NotificationHubs/Namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Action | Microsoft.NotificationHubs/Namespaces/write | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Action | Microsoft.NotificationHubs/operationResults/read | Returnerar åtgärdsresultat för Notification Hubs-provider |
> | Action | Microsoft.NotificationHubs/operations/read | Returnerar en lista över åtgärder som stöds för Notification Hubs-provider |
> | Action | Microsoft.NotificationHubs/register/action | Registrerar prenumerationen för NotificationHubs Resource Provider och gör det möjligt att skapa namn områden och NotificationHubs |
> | Action | Microsoft.NotificationHubs/unregister/action | Avregistrerar prenumerationen för NotificationHubs Resource Provider och gör det möjligt att skapa namn områden och NotificationHubs |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. OffAzure/HyperVSites/Clusters/Read | Hämtar egenskaperna för ett Hyper-V-kluster |
> | Action | Microsoft. OffAzure/HyperVSites/kluster/Skriv | Skapar eller uppdaterar Hyper-V-klustret |
> | Action | Microsoft.OffAzure/HyperVSites/delete | Tar bort Hyper-V-platsen |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/read | Hämtar egenskaperna för en Hyper-V-värd |
> | Action | Microsoft.OffAzure/HyperVSites/hosts/write | Skapar eller uppdaterar Hyper-V-värden |
> | Action | Microsoft. OffAzure/HyperVSites/Jobs/Read | Hämtar egenskaperna för ett Hyper-V-jobb |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/Read | Hämtar egenskaperna för en Hyper-V-dator |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/start/Action | Starta Hyper-V-datorer |
> | Action | Microsoft. OffAzure/HyperVSites/Machines/stop/action | Stoppar Hyper-V-datorer |
> | Action | Microsoft. OffAzure/HyperVSites/operationsstatus/Read | Hämtar egenskaperna för en Hyper-V-åtgärds status |
> | Action | Microsoft.OffAzure/HyperVSites/read | Hämtar egenskaperna för en Hyper-V-plats |
> | Action | Microsoft.OffAzure/HyperVSites/refresh/action | Uppdaterar objekten på en Hyper-V-plats |
> | Action | Microsoft. OffAzure/HyperVSites/runasaccounts/Read | Hämtar egenskaperna för ett Hyper-V-kör som-konto |
> | Action | Microsoft. OffAzure/HyperVSites/Usage/Read | Hämtar användningen av en Hyper-V-plats |
> | Action | Microsoft.OffAzure/HyperVSites/write | Skapar eller uppdaterar Hyper-V-platsen |
> | Action | Microsoft. OffAzure/Operations/Read | Läser de exponerade åtgärderna |
> | Action | Microsoft. OffAzure/register/åtgärd | Registrerar prenumeration med Microsoft. OffAzure Resource Provider |
> | Action | Microsoft.OffAzure/VMwareSites/delete | Tar bort VMware-webbplatsen |
> | Action | Microsoft.OffAzure/VMwareSites/jobs/read | Hämtar egenskaperna för ett VMware-jobb |
> | Action | Microsoft.OffAzure/VMwareSites/machines/read | Hämtar egenskaperna för en VMware-dator |
> | Action | Microsoft.OffAzure/VMwareSites/machines/start/action | Starta VMware-datorer |
> | Action | Microsoft.OffAzure/VMwareSites/machines/stop/action | Stoppar VMware-datorerna |
> | Action | Microsoft.OffAzure/VMwareSites/operationsstatus/read | Hämtar egenskaperna för en VMware-åtgärds status |
> | Action | Microsoft.OffAzure/VMwareSites/read | Hämtar egenskaperna för en VMware-plats |
> | Action | Microsoft.OffAzure/VMwareSites/refresh/action | Uppdaterar objekten i en VMware-webbplats |
> | Action | Microsoft.OffAzure/VMwareSites/runasaccounts/read | Hämtar egenskaperna för ett VMware-kör som-konto |
> | Action | Microsoft.OffAzure/VMwareSites/usage/read | Hämtar användningen av en VMware-plats |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/read | Hämtar egenskaperna för en VMware vCenter |
> | Action | Microsoft.OffAzure/VMwareSites/vcenters/write | Skapar eller uppdaterar VMware vCenter |
> | Action | Microsoft.OffAzure/VMwareSites/write | Skapar eller uppdaterar VMware-webbplatsen |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. OperationalInsights/linkTargets/Read | Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Om du vill länka den här Azure-prenumerationen till en arbets yta använder du ett kund-ID som returneras av den här åtgärden i egenskapen kund-ID för åtgärden skapa arbets yta. |
> | Action | Microsoft. operationalinsights/Operations/Read | Visar en lista över alla tillgängliga REST API-åtgärder för OperationalInsights. |
> | Action | Microsoft. operationalinsights/register/åtgärd | Rergisters prenumerationen. |
> | Action | Microsoft. OperationalInsights/register/åtgärd | Registrera en prenumeration på en resurs leverantör. |
> | Action | microsoft.operationalinsights/unregister/action | Avregistrerar prenumerationen. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Action | Microsoft.OperationalInsights/workspaces/analytics/query/schema/read | Hämta Sök schema v2. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/action | Sök med ny motor. |
> | Action | Microsoft.OperationalInsights/workspaces/api/query/schema/read | Hämta Sök schema v2. |
> | Action | Microsoft. OperationalInsights/arbets ytor/configurationScopes/Delete | Ta bort konfigurations omfång |
> | Action | Microsoft. OperationalInsights/arbets ytor/configurationScopes/Read | Hämta konfigurations omfång |
> | Action | Microsoft. OperationalInsights/arbets ytor/configurationScopes/Write | Ange konfigurations omfång |
> | Action | Microsoft. OperationalInsights/arbets ytor/data källor/ta bort | Ta bort data källor under en arbets yta. |
> | Action | Microsoft. OperationalInsights/arbets ytor/data källor/läsa | Hämta data källor under en arbets yta. |
> | Action | Microsoft. OperationalInsights/arbets ytor/data källor/skrivning | Skapa/uppdatera data källor under en arbets yta. |
> | Action | Microsoft. OperationalInsights/arbets ytor/ta bort | Tar bort en arbets yta. Om arbets ytan var länkad till en befintlig arbets yta när den skapades, raderas inte arbets ytan som den var länkad till. |
> | Action | Microsoft. OperationalInsights/arbets ytor/Gateway/Delete | Tar bort en gateway som kon figurer ATS för arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/generateregistrationcertificate/åtgärd | Genererar registrerings certifikat för arbets ytan. Det här certifikatet används för att ansluta Microsoft System Center Operation Manager till arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/intelligencepacks/Disable/åtgärd | Inaktiverar ett informations paket för en specifik arbets yta. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/enable/action | Aktiverar ett informations paket för en specifik arbets yta. |
> | Action | Microsoft.OperationalInsights/workspaces/intelligencepacks/read | Visar en lista över alla informations paket som är synliga för en specifik arbets yta och anger även om paketet är aktiverat eller inaktiverat för den arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/linkedServices/Delete | Ta bort länkade tjänster i den aktuella arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/linkedServices/Read | Hämta länkade tjänster för den aktuella arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/linkedServices/Write | Skapa/uppdatera länkade tjänster i den aktuella arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/Listnycklar/åtgärd | Hämtar List nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/Listnycklar/Read | Hämtar List nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/managementGroups/Read | Hämtar namn och metadata för System Center Operations Manager hanterings grupper som är anslutna till den här arbets ytan. |
> | Action | Microsoft.OperationalInsights/workspaces/metricDefinitions/read | Hämta mått definitioner under arbets ytan |
> | Action | Microsoft. OperationalInsights/arbets ytor/notificationSettings/Delete | Ta bort användarens meddelande inställningar för arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/notificationSettings/Read | Hämta användarens meddelande inställningar för arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/notificationSettings/Write | Ange användarens meddelande inställningar för arbets ytan. |
> | Action | Microsoft. operationalinsights/arbets ytor/åtgärder/Läs | Hämtar status för en OperationalInsights-arbetsyta åtgärd. |
> | Action | Microsoft. OperationalInsights/arbets ytor/rensning/åtgärd | Ta bort angivna data från arbets ytan |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AADDomainServicesAccountLogon/läsa | Läsa data från tabellen AADDomainServicesAccountLogon |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AADDomainServicesAccountManagement/läsa | Läsa data från tabellen AADDomainServicesAccountManagement |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesDirectoryServiceAccess/read | Läsa data från tabellen AADDomainServicesDirectoryServiceAccess |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesLogonLogoff/read | Läsa data från tabellen AADDomainServicesLogonLogoff |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AADDomainServicesPolicyChange/läsa | Läsa data från tabellen AADDomainServicesPolicyChange |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesPrivilegeUse/read | Läsa data från tabellen AADDomainServicesPrivilegeUse |
> | Action | Microsoft.OperationalInsights/workspaces/query/AADDomainServicesSystemSecurity/read | Läsa data från tabellen AADDomainServicesSystemSecurity |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADAssessmentRecommendation/read | Läs data från ADAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFActivityRun/read | Läsa data från tabellen ADFActivityRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFPipelineRun/read | Läsa data från tabellen ADFPipelineRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADFTriggerRun/read | Läsa data från tabellen ADFTriggerRun |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADReplicationResult/read | Läs data från ADReplicationResult-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ADSecurityAssessmentRecommendation/read | Läs data från ADSecurityAssessmentRecommendation-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/avisering/läsa | Läs data från Alert-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AlertHistory/läsa | Läs data från AlertHistory-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AppCenterError/läsa | Läsa data från tabellen AppCenterError |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ApplicationInsights/läsa | Läs data från ApplicationInsights-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AuditLogs/läsa | Läsa data från tabellen AuditLogs |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AutoscaleEvaluationsLog/läsa | Läsa data från tabellen AutoscaleEvaluationsLog |
> | Action | Microsoft.OperationalInsights/workspaces/query/AutoscaleScaleActionsLog/read | Läsa data från tabellen AutoscaleScaleActionsLog |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AWSCloudTrail/läsa | Läsa data från tabellen AWSCloudTrail |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/AzureActivity/läsa | Läs data från AzureActivity-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureAssessmentRecommendation/read | Läsa data från tabellen AzureAssessmentRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/AzureMetrics/read | Läs data från AzureMetrics-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/BlockchainApplicationLog/läsa | Läsa data från tabellen BlockchainApplicationLog |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/BlockchainProxyLog/läsa | Läsa data från tabellen BlockchainProxyLog |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/BoundPort/läsa | Läs data från BoundPort-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/CommonSecurityLog/läsa | Läs data från CommonSecurityLog-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ComputerGroup/läsa | Läs data från ComputerGroup-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ConfigurationChange/läsa | Läs data från ConfigurationChange-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ConfigurationData/läsa | Läs data från ConfigurationData-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerImageInventory/läsa | Läs data från ContainerImageInventory-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerInventory/läsa | Läs data från ContainerInventory-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerLog/läsa | Läs data från ContainerLog-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerNodeInventory/läsa | Läsa data från tabellen ContainerNodeInventory |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ContainerServiceLog/läsa | Läs data från ContainerServiceLog-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksAccounts/läsa | Läsa data från tabellen DatabricksAccounts |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksClusters/läsa | Läsa data från tabellen DatabricksClusters |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksDBFS/läsa | Läsa data från tabellen DatabricksDBFS |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksJobs/läsa | Läsa data från tabellen DatabricksJobs |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksNotebook/läsa | Läsa data från tabellen DatabricksNotebook |
> | Action | Microsoft.OperationalInsights/workspaces/query/DatabricksSecrets/read | Läsa data från tabellen DatabricksSecrets |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksSQLPermissions/läsa | Läsa data från tabellen DatabricksSQLPermissions |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksSSH/läsa | Läsa data från tabellen DatabricksSSH |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksTables/läsa | Läsa data från tabellen DatabricksTables |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DatabricksWorkspace/läsa | Läsa data från tabellen DatabricksWorkspace |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceAppCrash/läsa | Läs data från DeviceAppCrash-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceAppLaunch/läsa | Läs data från DeviceAppLaunch-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceCalendar/läsa | Läs data från DeviceCalendar-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceCleanup/läsa | Läs data från DeviceCleanup-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceConnectSession/läsa | Läs data från DeviceConnectSession-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceEtw/läsa | Läs data från DeviceEtw-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHardwareHealth/read | Läs data från DeviceHardwareHealth-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceHealth/read | Läs data från DeviceHealth-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceHeartbeat/läsa | Läs data från DeviceHeartbeat-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DeviceSkypeHeartbeat/läsa | Läs data från DeviceSkypeHeartbeat-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSkypeSignIn/read | Läs data från DeviceSkypeSignIn-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/DeviceSleepState/read | Läs data från DeviceSleepState-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHAppFailure/läsa | Läs data från DHAppFailure-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHAppReliability/läsa | Läs data från DHAppReliability-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHDriverReliability/läsa | Läs data från DHDriverReliability-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHLogonFailures/läsa | Läs data från DHLogonFailures-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHLogonMetrics/läsa | Läs data från DHLogonMetrics-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHOSCrashData/läsa | Läs data från DHOSCrashData-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHOSReliability/läsa | Läs data från DHOSReliability-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DHWipAppLearning/läsa | Läs data från DHWipAppLearning-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DnsEvents/läsa | Läs data från DnsEvents-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/DnsInventory/läsa | Läs data från DnsInventory-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ETWEvent/läsa | Läs data från ETWEvent-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/händelse/läsa | Läs data från Event-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeAssessmentRecommendation/read | Läs data från ExchangeAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ExchangeOnlineAssessmentRecommendation/read | Läs data från ExchangeOnlineAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/Heartbeat/read | Läs data från Hearbeat-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/HuntingBookmark/läsa | Läsa data från tabellen HuntingBookmark |
> | Action | Microsoft.OperationalInsights/workspaces/query/IISAssessmentRecommendation/read | Läs data från IISAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/InboundConnection/read | Läs data från InboundConnection-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/InsightsMetrics/read | Läsa data från tabellen InsightsMetrics |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/IntuneAuditLogs/läsa | Läsa data från tabellen IntuneAuditLogs |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/IntuneOperationalLogs/läsa | Läsa data från tabellen IntuneOperationalLogs |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/KubeEvents/läsa | Läsa data från tabellen KubeEvents |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/KubeNodeInventory/läsa | Läs data från KubeNodeInventory-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/KubePodInventory/read | Läs data från KubePodInventory-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/KubeServices/läsa | Läsa data från tabellen KubeServices |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/LinuxAuditLog/läsa | Läs data från LinuxAuditLog-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplication/läsa | Läs data från MAApplication-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationHealth/läsa | Läs data från MAApplicationHealth-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationHealthAlternativeVersions/läsa | Läs data från MAApplicationHealthAlternativeVersions-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationHealthIssues/läsa | Läs data från MAApplicationHealthIssues-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationInstance/läsa | Läs data från MAApplicationInstance-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAApplicationInstanceReadiness/läsa | Läs data från MAApplicationInstanceReadiness-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAApplicationReadiness/read | Läs data från MAApplicationReadiness-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADeploymentPlan/läsa | Läs data från MADeploymentPlan-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADevice/läsa | Läs data från MADevice-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADeviceNotEnrolled/läsa | Läsa data från tabellen MADeviceNotEnrolled |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADeviceNRT/läsa | Läsa data från tabellen MADeviceNRT |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADevicePnPHealth/läsa | Läs data från MADevicePnPHealth-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADevicePnPHealthAlternativeVersions/läsa | Läs data från MADevicePnPHealthAlternativeVersions-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADevicePnPHealthIssues/läsa | Läs data från MADevicePnPHealthIssues-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADeviceReadiness/läsa | Läs data från MADeviceReadiness-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MADriverInstanceReadiness/read | Läs data från MADriverInstanceReadiness-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MADriverReadiness/läsa | Läs data från MADriverReadiness-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAddin/läsa | Läs data från MAOfficeAddin-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinEntityHealth/read | Läsa data från tabellen MAOfficeAddinEntityHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealth/read | Läs data från MAOfficeAddinHealth-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinHealthEventNRT/read | Läsa data från tabellen MAOfficeAddinHealthEventNRT |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAddinHealthIssues/läsa | Läs data från MAOfficeAddinHealthIssues-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAddinInstance/läsa | Läs data från MAOfficeAddinInstance-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinInstanceReadiness/read | Läs data från MAOfficeAddinInstanceReadiness-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAddinReadiness/read | Läs data från MAOfficeAddinReadiness-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeApp/läsa | Läs data från MAOfficeApp-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAppCrashesNRT/läsa | Läsa data från tabellen MAOfficeAppCrashesNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppHealth/read | Läs data från MAOfficeAppHealth-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeAppInstance/läsa | Läs data från MAOfficeAppInstance-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppInstanceHealth/read | Läsa data från tabellen MAOfficeAppInstanceHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppReadiness/read | Läs data från MAOfficeAppReadiness-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeAppSessionsNRT/read | Läsa data från tabellen MAOfficeAppSessionsNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeBuildInfo/read | Läs data från MAOfficeBuildInfo-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessment/read | Läs data från MAOfficeCurrencyAssessment-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeCurrencyAssessmentDailyCounts/read | Läs data från MAOfficeCurrencyAssessmentDailyCounts-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeDeploymentStatus/läsa | Läs data från MAOfficeDeploymentStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAOfficeDeploymentStatusNRT/läsa | Läsa data från tabellen MAOfficeDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroErrorNRT/read | Läsa data från tabellen MAOfficeMacroErrorNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroGlobalHealth/read | Läsa data från tabellen MAOfficeMacroGlobalHealth |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealth/read | Läs data från MAOfficeMacroHealth-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroHealthIssues/read | Läs data från MAOfficeMacroHealthIssues-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueInstanceReadiness/read | Läs data från MAOfficeMacroIssueInstanceReadiness-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroIssueReadiness/read | Läs data från MAOfficeMacroIssueReadiness-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeMacroSummary/read | Läs data från MAOfficeMacroSummary-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuite/read | Läs data från MAOfficeSuite-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAOfficeSuiteInstance/read | Läs data från MAOfficeSuiteInstance-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAProposedPilotDevices/läsa | Läs data från MAProposedPilotDevices-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAWindowsBuildInfo/läsa | Läs data från MAWindowsBuildInfo-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessment/read | Läs data från MAWindowsCurrencyAssessment-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsCurrencyAssessmentDailyCounts/read | Läs data från MAWindowsCurrencyAssessmentDailyCounts-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAWindowsDeploymentStatus/läsa | Läs data från MAWindowsDeploymentStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MAWindowsDeploymentStatusNRT/läsa | Läsa data från tabellen MAWindowsDeploymentStatusNRT |
> | Action | Microsoft.OperationalInsights/workspaces/query/MAWindowsSysReqInstanceReadiness/read | Läs data från MAWindowsSysReqInstanceReadiness-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftWebApplicationLog/läsa | Läsa data från tabellen MicrosoftWebApplicationLog |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftWebFunctionExecutionLogs/läsa | Läsa data från tabellen MicrosoftWebFunctionExecutionLogs |
> | Action | Microsoft.OperationalInsights/workspaces/query/MicrosoftWebStdOutStdErrLog/read | Läsa data från tabellen MicrosoftWebStdOutStdErrLog |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/MicrosoftWebW3CLog/läsa | Läsa data från tabellen MicrosoftWebW3CLog |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/NetworkMonitoring/läsa | Läs data från NetworkMonitoring-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/OfficeActivity/läsa | Läs data från OfficeActivity-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/åtgärd/läsa | Läs data från Operation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/OutboundConnection/read | Läs data från OutboundConnection-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/Perf/read | Läs data från Perf-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/ProtectionStatus/läsa | Läs data från ProtectionStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/läsa | Köra frågor över data i arbets ytan |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedAzureCommonFields/read | Läs data från ReservedAzureCommonFields-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ReservedCommonFields/read | Läs data från ReservedCommonFields-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCCMAssessmentRecommendation/read | Läs data från SCCMAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SCOMAssessmentRecommendation/read | Läs data från SCOMAssessmentRecommendation-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityAlert/läsa | Läs data från SecurityAlert-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityBaseline/läsa | Läs data från SecurityBaseline-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityBaselineSummary/läsa | Läs data från SecurityBaselineSummary-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityDetection/read | Läs data från SecurityDetection-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SecurityEvent/läsa | Läs data från SecurityEvent-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityIoTRawEvent/read | Läsa data från tabellen SecurityIoTRawEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/SecurityRecommendation/read | Läsa data från tabellen SecurityRecommendation |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricOperationalEvent/read | Läs data från ServiceFabricOperationalEvent-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableActorEvent/read | Läs data från ServiceFabricReliableActorEvent-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/ServiceFabricReliableServiceEvent/read | Läs data från ServiceFabricReliableServiceEvent-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBAssessmentRecommendation/read | Läs data från SfBAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SfBOnlineAssessmentRecommendation/read | Läs data från SfBOnlineAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SharePointOnlineAssessmentRecommendation/read | Läs data från SharePointOnlineAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SigninLogs/read | Läsa data från tabellen SigninLogs |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SPAssessmentRecommendation/läsa | Läs data från SPAssessmentRecommendation-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SQLAssessmentRecommendation/read | Läs data från SQLAssessmentRecommendation-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SQLQueryPerformance/läsa | Läs data från SQLQueryPerformance-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SqlThreatProtectionLoginAudits/läsa | Läsa data från tabellen SqlThreatProtectionLoginAudits |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/SqlVulnerabilityAssessmentResult/läsa | Läsa data från tabellen SqlVulnerabilityAssessmentResult |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/syslog/läsa | Läs data från Syslog-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/SysmonEvent/read | Läs data från SysmonEvent-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/tabeller. anpassad/läst | Läser data från valfri anpassad logg |
> | Action | Microsoft.OperationalInsights/workspaces/query/ThreatIntelligenceIndicator/read | Läsa data från tabellen ThreatIntelligenceIndicator |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/UAApp/läsa | Läs data från UAApp-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/UAComputer/läsa | Läs data från UAComputer-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAComputerRank/read | Läs data från UAComputerRank-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriver/read | Läs data från UADriver-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UADriverProblemCodes/read | Läs data från UADriverProblemCodes-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/UAFeedback/läsa | Läs data från UAFeedback-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAHardwareSecurity/read | Läs data från UAHardwareSecurity-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/UAIESiteDiscovery/läsa | Läs data från UAIESiteDiscovery-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAOfficeAddIn/read | Läs data från UAOfficeAddIn-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UAProposedActionPlan/read | Läs data från UAProposedActionPlan-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UASysReqIssue/read | Läs data från UASysReqIssue-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/UAUpgradedComputer/läsa | Läs data från UAUpgradedComputer-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/Update/read | Läs data från Update-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read | Läs data från UpdateRunProgress-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read | Läs data från UpdateSummary-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/Usage/read | Läs data från Usage-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMBoundPort/read | Läsa data från tabellen VMBoundPort |
> | Action | Microsoft.OperationalInsights/workspaces/query/VMConnection/read | Läsa data från tabellen VMConnection |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/W3CIISLog/läsa | Läs data från W3CIISLog-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WaaSDeploymentStatus/läsa | Läs data från WaaSDeploymentStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WaaSInsiderStatus/läsa | Läs data från WaaSInsiderStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WaaSUpdateStatus/läsa | Läs data från WaaSUpdateStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WDAVStatus/läsa | Läs data från WDAVStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WDAVThreat/läsa | Läs data från WDAVThreat-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsClientAssessmentRecommendation/read | Läs data från WindowsClientAssessmentRecommendation-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WindowsEvent/läsa | Läsa data från tabellen WindowsEvent |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsFirewall/read | Läs data från WindowsFirewall-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/query/WindowsServerAssessmentRecommendation/read | Läs data från WindowsServerAssessmentRecommendation-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WireData/läsa | Läs data från WireData-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WorkloadMonitoringPerf/läsa | Läsa data från tabellen WorkloadMonitoringPerf |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WUDOAggregatedStatus/läsa | Läs data från WUDOAggregatedStatus-tabellen |
> | Action | Microsoft. OperationalInsights/arbets ytor/fråga/WUDOStatus/läsa | Läs data från WUDOStatus-tabellen |
> | Action | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbets yta |
> | Action | Microsoft. OperationalInsights/arbets ytor/regeneratesharedkey/åtgärd | Återskapar den angivna delade nyckeln för arbets ytan |
> | Action | Microsoft. operationalinsights/arbets ytor/regler/läsa | Hämta alla varnings regler. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/delete | Tar bort en sparad Sök fråga |
> | Action | Microsoft. OperationalInsights/arbets ytor/savedSearches/Read | Hämtar en sparad Sök fråga |
> | Action | Microsoft. operationalinsights/arbets ytor/savedsearches/Results/Read | Hämta sparade Sök resultat. Inaktuell |
> | Action | Microsoft. operationalinsights/arbets ytor/savedsearches/schema/åtgärder/ta bort | Ta bort schemalagda Sök åtgärder. |
> | Action | Microsoft. operationalinsights/arbets ytor/savedsearches/schema/åtgärder/läsa | Hämta schemalagda Sök åtgärder. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/actions/write | Skapa eller uppdatera schemalagda Sök åtgärder. |
> | Action | Microsoft. operationalinsights/arbets ytor/savedsearches/scheman/ta bort | Ta bort schemalagda sökningar. |
> | Action | Microsoft. operationalinsights/arbets ytor/savedsearches/scheman/lästa | Hämta schemalagda sökningar. |
> | Action | microsoft.operationalinsights/workspaces/savedsearches/schedules/write | Skapa eller uppdatera schemalagda sökningar. |
> | Action | Microsoft.OperationalInsights/workspaces/savedSearches/write | Skapar en sparad Sök fråga |
> | Action | Microsoft. OperationalInsights/arbets ytor/schema/läsa | Hämtar Sök schema för arbets ytan.  Sökschemat innehåller de visade fälten och deras typer. |
> | Action | Microsoft.OperationalInsights/workspaces/search/action | Kör en Sök fråga |
> | Action | Microsoft. operationalinsights/arbets ytor/search/Read | Hämta Sök resultat. Föråldrad. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Action | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/storageinsightconfigs/Delete | Tar bort en lagrings konfiguration. Detta kommer att stoppa Microsoft Operational Insights från att läsa data från lagrings kontot. |
> | Action | Microsoft. OperationalInsights/arbets ytor/storageinsightconfigs/Read | Hämtar en lagrings konfiguration. |
> | Action | Microsoft. OperationalInsights/arbets ytor/storageinsightconfigs/Write | Skapar en ny lagrings konfiguration. Dessa konfigurationer används för att hämta data från en plats i ett befintligt lagrings konto. |
> | Action | Microsoft.OperationalInsights/workspaces/upgradetranslationfailures/read | Hämta logg för översättnings haveri för Sök uppgradering för arbets ytan |
> | Action | Microsoft. OperationalInsights/arbets ytor/användning/läsning | Hämtar användnings data för en arbets yta, inklusive mängden data som läses av arbets ytan. |
> | Action | Microsoft. OperationalInsights/arbets ytor/Skriv | Skapar en ny arbets yta eller länkar till en befintlig arbets yta genom att ange kund-ID: t från den befintliga arbets ytan. |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.OperationsManagement/managementAssociations/delete | Ta bort befintlig hanterings Association |
> | Action | Microsoft.OperationsManagement/managementAssociations/read | Hämta befintlig hanterings Association |
> | Action | Microsoft.OperationsManagement/managementAssociations/write | Skapa en ny hanterings Association |
> | Action | Microsoft.OperationsManagement/managementConfigurations/delete | Ta bort befintlig hanterings konfiguration |
> | Action | Microsoft.OperationsManagement/managementConfigurations/read | Hämta befintlig hanterings konfiguration |
> | Action | Microsoft.OperationsManagement/managementConfigurations/write | Skapa en ny hanterings konfiguration |
> | Action | Microsoft.OperationsManagement/register/action | Registrera en prenumeration på en resurs leverantör. |
> | Action | Microsoft.OperationsManagement/solutions/delete | Ta bort befintlig OMS-lösning |
> | Action | Microsoft.OperationsManagement/solutions/read | Ta slut på OMS-lösning |
> | Action | Microsoft. OperationsManagement/lösningar/Skriv | Skapa ny OMS-lösning |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.PolicyInsights/asyncOperationResults/read | Hämtar resultat för asynkron åtgärd. |
> | Action | Microsoft.PolicyInsights/operations/read | Hämtar åtgärder som stöds på Microsoft. PolicyInsights-namnrymd |
> | Action | Microsoft.PolicyInsights/policyEvents/queryResults/action | Fråga efter information om principhändelser. |
> | Action | Microsoft. PolicyInsights/policyEvents/queryResults/Read | Fråga efter information om principhändelser. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/action | Fråga efter information om principtillstånd. |
> | Action | Microsoft.PolicyInsights/policyStates/queryResults/read | Fråga efter information om principtillstånd. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/action | Fråga efter sammanfattning av senaste principtillstånd. |
> | Action | Microsoft.PolicyInsights/policyStates/summarize/read | Fråga efter sammanfattning av senaste principtillstånd. |
> | Action | Microsoft.PolicyInsights/policyStates/triggerEvaluation/action | Aktiverar en kompatibilitetsutvärdering för det valda omfånget. |
> | Action | Microsoft.PolicyInsights/policyTrackedResources/queryResults/read | Fråga efter information om resurser som krävs av DeployIfNotExists-principer. |
> | Action | Microsoft.PolicyInsights/register/action | Registrerar resurs leverantören Microsoft policy Insights och aktiverar åtgärder på den. |
> | Action | Microsoft.PolicyInsights/remediations/cancel/action | Avbryt pågående Microsoft policy-reparationer. |
> | Action | Microsoft.PolicyInsights/remediations/delete | Ta bort principreparationer. |
> | Action | Microsoft. PolicyInsights/reparationer/listDeployments/Read | Visar lista över distributioner som krävs av en principreparation. |
> | Action | Microsoft.PolicyInsights/remediations/read | Hämta principreparationer. |
> | Action | Microsoft.PolicyInsights/remediations/write | Skapa eller uppdatera Microsoft policy-reparationer. |
> | Action | Microsoft.PolicyInsights/unregister/action | Avregistrerar Microsoft policy Insights Resource Provider. |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Portal/konsoler/ta bort | Tar bort Cloud Shell-instansen. |
> | Action | Microsoft. Portal/konsoler/läsa | Läser Cloud Shell-instansen. |
> | Action | Microsoft. Portal/konsoler/skrivning | Skapa eller uppdatera en Cloud Shell-instans. |
> | Action | Microsoft.Portal/dashboards/delete | Tar bort instrumentpanelen från prenumerationen. |
> | Action | Microsoft. Portal/instrument paneler/läsa | Läser in instrumentpanelerna för prenumerationen. |
> | Action | Microsoft.Portal/dashboards/write | Lägg till eller ändra en instrumentpanel för en prenumeration. |
> | Action | Microsoft. Portal/register/åtgärd | Registrera i portalen |
> | Action | Microsoft. Portal/usersettings/Delete | Tar bort Cloud Shell-användarinställningarna. |
> | Action | Microsoft.Portal/usersettings/read | Läser Cloud Shell-användarinställningarna. |
> | Action | Microsoft.Portal/usersettings/write | Skapa eller uppdatera Cloud Shell-användarinställningen. |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. PowerBIDedicated/kapacitets/Delete | Tar bort Power BI dedikerad kapacitet. |
> | Action | Microsoft. PowerBIDedicated/kapaciteter/läsning | Hämtar information om den angivna Power BI dedikerade kapaciteten. |
> | Action | Microsoft. PowerBIDedicated/kapaciteter/återuppta/åtgärd | Återupptar kapaciteten. |
> | Action | Microsoft.PowerBIDedicated/capacities/skus/read | Hämta tillgänglig SKU-information för kapaciteten |
> | Action | Microsoft. PowerBIDedicated/kapacitet/PAUSE/åtgärd | Pausar kapaciteten. |
> | Action | Microsoft. PowerBIDedicated/kapacitet/Skriv | Skapar eller uppdaterar den angivna dedikerade kapaciteten för Power BI. |
> | Action | Microsoft. PowerBIDedicated/locations/checkNameAvailability/Action | Kontrollerar att angivet Power BI dedikerade kapacitets namn är giltigt och inte används. |
> | Action | Microsoft. PowerBIDedicated/locations/operationresults/Read | Hämtar information om det angivna åtgärds resultatet. |
> | Action | Microsoft. PowerBIDedicated/locations/operationstatuses/Read | Hämtar information om den angivna åtgärds statusen. |
> | Action | Microsoft. PowerBIDedicated/Operations/Read | Hämtar information om åtgärder |
> | Action | Microsoft. PowerBIDedicated/register/åtgärd | Registrerar Power BI dedikerad resurs leverantör. |
> | Action | Microsoft.PowerBIDedicated/skus/read | Hämtar information om SKU: er |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Action | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Action | microsoft.recoveryservices/Locations/backupPreValidateProtection/action |  |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItem/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Action | microsoft.recoveryservices/Locations/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Action | Microsoft. recoveryservices/locations/backupStatus/Action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | Action | microsoft.recoveryservices/Locations/backupValidateFeatures/action | Validera funktioner |
> | Action | Microsoft.RecoveryServices/locations/checkNameAvailability/action | Kontrol lera resurs namns tillgänglighet är ett API för att kontrol lera om resurs namnet är tillgängligt |
> | Action | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärds status för en specifik åtgärd |
> | Action | Microsoft. RecoveryServices/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | Action | Microsoft. RecoveryServices/register/åtgärd | Registrerar prenumeration för den aktuella resurs leverantören |
> | Action | Microsoft. recoveryservices/valv/backupconfig/läsa | Returnerar konfiguration för Recovery Services valv. |
> | Action | Microsoft. recoveryservices/valv/backupconfig/Write | Uppdaterar konfigurationen för Recovery Services valv. |
> | Action | Microsoft. recoveryservices/valv/backupEngines/läsa | Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet. |
> | Action | Microsoft. recoveryservices/valv/backupFabrics/backupProtectionIntent/Delete | Ta bort en säkerhets kopie rad skydds avsikt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/read | Få en säkerhets kopie rad skydds avsikt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhets kopia av skydds avsikt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectableContainers/read | Hämta alla skydds bara behållare |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/delete | Tar bort den registrerade behållaren |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör förfrågan om arbets belastningar inom en behållare |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skyddsbehållare. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Säkerhetskopierar ett skyddat objekt. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/delete | Tar bort skyddat objekt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för utförd åtgärd på skyddade objekt. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objekt information om det skyddade objektet |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Etablera snabb objekts återställning för skyddat objekt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställningspunkter för skyddade objekt. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återskapa återställningspunkter för skyddade objekt. |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla direkt objekt återställning för skyddat objekt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Action | microsoft.recoveryservices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållar listan |
> | Action | Microsoft. recoveryservices/valv/backupJobs/Avbryt/åtgärd | Avbryt jobbet |
> | Action | Microsoft. recoveryservices/valv/backupJobs/operationResults/Read | Returnerar resultat från jobbåtgärd. |
> | Action | Microsoft. recoveryservices/valv/backupJobs/läsa | Returnerar alla jobb objekt |
> | Action | microsoft.recoveryservices/Vaults/backupJobsExport/action | Exportera jobb |
> | Action | microsoft.recoveryservices/Vaults/backupOperationResults/read | Returnerar resultat från säkerhetskopiering för Recovery Services-valvet. |
> | Action | microsoft.recoveryservices/Vaults/backupOperations/read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | Action | Microsoft. recoveryservices/valv/backupPolicies/Delete | Ta bort en skydds princip |
> | Action | Microsoft. recoveryservices/valv/backupPolicies/operationResults/Read | Hämtar resultat från principåtgärd. |
> | Action | Microsoft. recoveryservices/valv/backupPolicies/åtgärder/Läs | Hämta status för princip åtgärd. |
> | Action | Microsoft. recoveryservices/valv/backupPolicies/läsa | Returnerar alla skydds principer |
> | Action | Microsoft. recoveryservices/valv/backupPolicies/Write | Skapar skydds princip |
> | Action | microsoft.recoveryservices/Vaults/backupProtectableItems/read | Returnerar lista över alla objekt som ska skyddas. |
> | Action | microsoft.recoveryservices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Action | Microsoft. recoveryservices/valv/backupProtectionContainers/läsa | Returnerar alla behållare som hör till prenumerationen |
> | Action | Microsoft. recoveryservices/valv/backupProtectionIntents/läsa | Lista alla säkerhets avsikter för säkerhets kopiering |
> | Action | Microsoft. recoveryservices/valv/backupSecurityPIN/åtgärd | Returnerar information om säkerhets kod för Recovery Services valv. |
> | Action | Microsoft. recoveryservices/valv/backupstorageconfig/läsa | Returnerar lagrings konfiguration för Recovery Services valv. |
> | Action | Microsoft. recoveryservices/valv/backupstorageconfig/Write | Uppdaterar lagrings konfiguration för Recovery Services valv. |
> | Action | microsoft.recoveryservices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | Action | Microsoft. recoveryservices/valv/backupValidateOperation/åtgärd | Verifiera åtgärd på skyddat objekt |
> | Action | Microsoft.RecoveryServices/Vaults/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Action | Microsoft.RecoveryServices/Vaults/delete | Åtgärden ta bort valv tar bort den angivna Azure-resursen av typen valv |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/delete | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Action | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | Action | Microsoft. RecoveryServices/valv/monitoringAlerts/Write | Löser aviseringen. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/read | Hämtar meddelande konfigurationen för Recovery Services-valvet. |
> | Action | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/write | Konfigurerar e-postaviseringar till Recovery Services-valvet. |
> | Action | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/delete | Du kan använda åtgärden avregistrera behållare för att avregistrera en behållare. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | Action | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Du kan använda åtgärden registrera tjänst behållare för att registrera en behållare med återställnings tjänsten. |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läs eventuella aviserings inställningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationAlertSettings/write | Skapa eller uppdatera eventuella aviserings inställningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs eventuella händelser |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar infrastrukturens enhetlighet |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/delete | Ta bort eventuella infrastruktur resurser |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/deployProcessServerImage/action | Distribuera process Server avbildning |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/migratetoaad/action | Migrera infrastruktur resurs till AAD |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läs eventuella infrastruktur resurser |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Associera gatewayen igen |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/remove/action | Ta bort infrastruktur resurs |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur resurser |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationLogicalNetworks/read | Läs alla logiska nätverk |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Action | Microsoft. RecoveryServices/valv/replicationFabrics/replicationNetworks/replicationNetworkMappings/Delete | Ta bort eventuella nätverks mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write | Skapa eller uppdatera nätverks mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/action | Identifiera objekt som ska skyddas |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/remove/action | Ta bort skydds container |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/delete | Ta bort eventuella migreringsåtgärder |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrate/action | Migrera objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/migrationRecoveryPoints/read | Läs alla återställnings punkter för migrering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/read | Läs eventuella migreringsåtgärder |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrate/action | Testa migrering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/testMigrateCleanup/action | Testa migrering av rensning |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems/write | Skapa eller uppdatera objekt i migreringen |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läs alla objekt som ska skyddas |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/addDisks/action | Lägg till diskar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Tillämpa återställnings punkt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/delete | Ta bort eventuella skyddade objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Genomför redundans |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läs alla skyddade objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läs alla återställnings punkter för replikering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/remove/action | Ta bort skyddat objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/removeDisks/action | Ta bort diskar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd för skyddat objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ResolveHealthErrors/action |  |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/submitFeedback/action | Skicka feedback |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/targetComputeSizes/read | Läs alla mål beräknings storlekar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Rensning av redundanstest |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdatera mobilitets tjänsten |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/write | Skapa eller uppdatera skyddade objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/delete | Ta bort alla skydds behållar mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läs alla skydds behållar mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/remove/action | Ta bort skydds container mappning |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/write | Skapa eller uppdatera alla skydds behållar mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Växla skydds behållare |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/write | Skapa eller uppdatera eventuella skydds behållare |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete | Ta bort eventuella Recovery Services-leverantörer |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läs eventuella Recovery Services-leverantörer |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera Provider |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action | Ta bort Recovery Services Provider |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/write | Skapa eller uppdatera Recovery Services leverantörer |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läs alla lagrings klassificeringar |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/delete | Ta bort alla mappningar för lagrings klassificering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läs alla mappningar för lagrings klassificering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/write | Skapa eller uppdatera mappningar för lagrings klassificering |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/delete | Ta bort alla vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läs eventuella vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/write | Skapa eller uppdatera eventuella vCenter |
> | Action | Microsoft.RecoveryServices/vaults/replicationFabrics/write | Skapa eller uppdatera eventuella infrastruktur resurser |
> | Action | Microsoft. RecoveryServices/valv/replicationJobs/Avbryt/åtgärd | Avbryt jobb |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/read | Läs alla jobb |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/restart/action | Starta om jobb |
> | Action | Microsoft.RecoveryServices/vaults/replicationJobs/resume/action | Återuppta jobb |
> | Action | Microsoft.RecoveryServices/vaults/replicationMigrationItems/read | Läs eventuella migreringsåtgärder |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationNetworks/read | Läs alla nätverk |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/delete | Ta bort eventuella principer |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läs eventuella principer |
> | Action | Microsoft.RecoveryServices/vaults/replicationPolicies/write | Skapa eller uppdatera principer |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectedItems/read | Läs alla skyddade objekt |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainerMappings/read | Läs alla skydds behållar mappningar |
> | Action | Microsoft.RecoveryServices/vaults/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Action | Microsoft. RecoveryServices/valv/replicationRecoveryPlans/Delete | Ta bort eventuella återställnings planer |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Återställnings plan för redundans |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Återställnings plan för planerad redundansväxling |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läs eventuella återställnings planer |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Skydda återställnings plan |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Återställnings plan för redundanstest |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Återställnings plan för rensning av redundanstest |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställnings plan för redundans |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/write | Skapa eller uppdatera eventuella återställnings planer |
> | Action | Microsoft.RecoveryServices/vaults/replicationRecoveryServicesProviders/read | Läs eventuella Recovery Services-leverantörer |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassificationMappings/read | Läs alla mappningar för lagrings klassificering |
> | Action | Microsoft.RecoveryServices/vaults/replicationStorageClassifications/read | Läs alla lagrings klassificeringar |
> | Action | Microsoft.RecoveryServices/vaults/replicationSupportedOperatingSystems/read | Läs alla  |
> | Action | Microsoft.RecoveryServices/vaults/replicationUsages/read | Läs om användning av valv replikering |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/read | Läs all hälsa för valv-replikering |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultHealth/refresh/action | Uppdatera hälsa för valv |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/read | Läs alla  |
> | Action | Microsoft.RecoveryServices/vaults/replicationVaultSettings/write | Skapa eller uppdatera alla  |
> | Action | Microsoft.RecoveryServices/vaults/replicationvCenters/read | Läs eventuella vCenter |
> | Action | Microsoft. recoveryservices/valv/användning/läsning | Returnerar användningsinformation om Recovery Services-valvet. |
> | Action | Microsoft.RecoveryServices/vaults/usages/read | Läs alla valv användningar |
> | Action | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | Action | Microsoft.RecoveryServices/Vaults/write | Med skapa valv så skapas en Azure-resurs av typen valv |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. relä/checkNameAvailability/åtgärd | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Action | Microsoft. relä/checkNamespaceAvailability/åtgärd | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuellt Använd CheckNameAvailability i stället. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/action | Uppdaterar namn områdets auktoriseringsregel. Detta API är föråldrat. Använd ett parkera-anrop för att uppdatera auktoriseringsprincipen för namn områden i stället.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Action | Microsoft. Relay/Namespaces/authorizationRules/listnycklar/Action | Hämta anslutningssträngen till namnområdet |
> | Action | Microsoft.Relay/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Action | Microsoft.Relay/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.Relay/namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Action | Microsoft.Relay/namespaces/Delete | Ta bort namnområdesresurs |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Action | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/breakPairing/Action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Action | Microsoft.Relay/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/delete | Tar bort den katastrof återställnings konfiguration som är kopplad till namn området. Den här åtgärden kan bara anropas via det primära namn området. |
> | Action | Microsoft. Relay/Namespaces/disasterRecoveryConfigs/redundans/åtgärd | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Action | Microsoft.Relay/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/action | Åtgärd för att uppdatera resursprovider. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/delete | Åtgärd för att ta bort resursprovider-auktoriseringsregler |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/listkeys/action | Hämta anslutnings strängen till resursprovider |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/read |  Hämta listan över resursprovider-auktoriseringsregler |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.Relay/namespaces/HybridConnections/authorizationRules/write | Skapa auktoriseringsregler för resursprovider och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Action | Microsoft.Relay/namespaces/HybridConnections/Delete | Åtgärd för att ta bort resursprovider-resurs |
> | Action | Microsoft.Relay/namespaces/HybridConnections/read | Hämta lista över beskrivningar av resursprovider-resurser |
> | Action | Microsoft.Relay/namespaces/HybridConnections/write | Skapa eller uppdatera resursprovider-egenskaper. |
> | Action | Microsoft.Relay/namespaces/messagingPlan/read | Hämtar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.Relay/namespaces/messagingPlan/write | Uppdaterar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft. Relay/namnrymder/operationresults/Read | Hämta status för namnområdesåtgärd |
> | Action | Microsoft.Relay/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Action | Microsoft. Relay/namnrymder/läsa | Hämta listan över beskrivningar av namnområdesresurs |
> | Action | Microsoft.Relay/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/action | Åtgärd för att uppdatera WcfRelay. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/delete | Åtgärd för att ta bort WcfRelay-auktoriseringsregler |
> | Action | Microsoft. Relay/Namespaces/WcfRelays/authorizationRules/listnycklar/Action | Hämta anslutnings strängen till WcfRelay |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/read |  Hämta listan över WcfRelay-auktoriseringsregler |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/regeneratekeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.Relay/namespaces/WcfRelays/authorizationRules/write | Skapa auktoriseringsregler för WcfRelay och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Action | Microsoft. Relay/namnrymder/WcfRelays/Delete | Åtgärd för att ta bort WcfRelay-resurs |
> | Action | Microsoft. Relay/namnrymder/WcfRelays/Read | Hämta lista över beskrivningar av WcfRelay-resurser |
> | Action | Microsoft. Relay/namnrymder/WcfRelays/Write | Skapa eller uppdatera WcfRelay-egenskaper. |
> | Action | Microsoft. Relay/namnrymd/Skriv | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Action | Microsoft.Relay/operations/read | Hämta åtgärder |
> | Action | Microsoft. Relay/register/åtgärd | Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser |
> | Action | Microsoft.Relay/unregister/action | Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/current/read | Hämtar tillgänglighetsstatusen för den angivna resursen |
> | Action | Microsoft.ResourceHealth/AvailabilityStatuses/read | Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området |
> | Action | Microsoft.ResourceHealth/events/read | Hämta hälsotillståndshändelser för tjänsten för angiven prenumeration |
> | Action | Microsoft. Resourcehealth/healthevent/Action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Action | Microsoft. Resourcehealth/healthevent/activated/åtgärd | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Action | Microsoft. Resourcehealth/healthevent/pågående/åtgärd | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Action | Microsoft. Resourcehealth/healthevent/väntar/åtgärd | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Action | Microsoft. Resourcehealth/healthevent/åtgärdad/åtgärd | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Action | Microsoft.Resourcehealth/healthevent/Updated/action | Anger ändringen i hälsotillståndet för den angivna resursen |
> | Action | Microsoft.ResourceHealth/impactedResources/read | Hämta resurser som påverkas för angiven prenumeration |
> | Action | Microsoft.ResourceHealth/metadata/read | Hämtar metadata |
> | Action | Microsoft.ResourceHealth/Operations/read | Hämta tillgängliga åtgärder för Microsoft ResourceHealth |
> | Action | Microsoft.ResourceHealth/register/action | Registrerar prenumerationen för Microsoft ResourceHealth |
> | Action | Microsoft.ResourceHealth/unregister/action | Avregistrerar prenumerationen för Microsoft ResourceHealth |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Resources/checkPolicyCompliance/Action | Kontrollera kompatibilitetsstatusen för en viss resurs mot resursprinciper. |
> | Action | Microsoft. Resources/checkResourceName/Action | Kontrollera resursnamnets giltighet. |
> | Action | Microsoft. Resources/distributioner/Avbryt/åtgärd | Avbryter en distribution. |
> | Action | Microsoft. Resources/Deployments/Delete | Tar bort en distribution. |
> | Action | Microsoft.Resources/deployments/operations/read | Hämtar eller listar distributionsåtgärder. |
> | Action | Microsoft. Resources/Deployments/Read | Hämtar eller listar distributioner. |
> | Action | Microsoft. Resources/Deployments/validate/Action | Verifierar en distribution. |
> | Action | Microsoft. Resources/distributions/whatIf/åtgärd | Förutsäger distribution av ändringar i mallen. |
> | Action | Microsoft. Resources/Deployments/Write | Skapar eller uppdaterar en distribution. |
> | Action | Microsoft. Resources/Links/Delete | Tar bort en resurslänk. |
> | Action | Microsoft. Resources/Links/Read | Hämtar eller listar resurslänkar. |
> | Action | Microsoft. Resources/Links/Write | Skapar eller uppdaterar en resurslänk. |
> | Action | Microsoft. Resources/Marketplace/Purchase/Action | Köper en resurs på Marketplace. |
> | Action | Microsoft. Resources/providers/Read | Hämta listan över leverantörer. |
> | Action | Microsoft. Resources/Resources/Read | Hämta listan över resurser baserade på filter. |
> | Action | Microsoft. Resources/Subscriptions/locations/Read | Hämtar listan över platser som stöds. |
> | Action | Microsoft.Resources/subscriptions/operationresults/read | Hämtar prenumerationsåtgärdsresultaten. |
> | Action | Microsoft. Resources/Subscriptions/providers/Read | Hämtar eller listar resursprovidrar. |
> | Action | Microsoft. Resources/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/Delete | Tar bort resursgruppen och alla dess resurser. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/Operations/Read | Hämtar eller listar distributionsåtgärder. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/distributioner/operationstatuses/Read | Hämtar eller listar status för distributionsåtgärder. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/distribution/Read | Hämtar eller listar distributioner. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/distributioner/Write | Skapar eller uppdaterar en distribution. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/moveResources/Action | Flyttar resurser från en resursgrupp till en annan. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Action | Microsoft. Resources/Subscriptions/ResourceGroups/Resources/Read | Hämtar resurserna för resursgruppen. |
> | Action | Microsoft.Resources/subscriptions/resourceGroups/validateMoveResources/action | Verifiera flytt av resurser från en resursgrupp till en annan. |
> | Action | Microsoft. Resources/Subscriptions/resourceGroups/Write | Skapar eller uppdaterar en resursgrupp. |
> | Action | Microsoft. Resources/Subscriptions/resurser/Read | Hämtar en prenumerations resurser. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/Delete | Tar bort en prenumerationstagg. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/Read | Hämtar eller listar prenumerationstaggar. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/tagValues/Delete | Tar bort ett prenumerationstaggsvärde. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/tagValues/Read | Hämtar eller listar prenumerationstaggsvärden. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/tagValues/Write | Lägger till ett prenumerationstaggsvärde. |
> | Action | Microsoft. Resources/Subscriptions/tagNames/Write | Lägger till en prenumerationstagg. |
> | Action | Microsoft. Resources/Tags/Delete | Tar bort alla Taggar på en resurs. |
> | Action | Microsoft. Resources/Tags/Read | Hämtar alla Taggar på en resurs. |
> | Action | Microsoft. Resources/Tags/Write | Uppdaterar taggarna på en resurs genom att ersätta eller sammanfoga befintliga taggar med en ny uppsättning taggar eller ta bort befintliga taggar. |
> | Action | Microsoft. Resources/Tenants/Read | Hämtar listan över klienter. |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Delete | Tar bort en jobbsamling. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Disable/åtgärd | Inaktiverar en jobbsamling. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Enable/åtgärd | Aktiverar en jobbsamling. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Delete | Tar bort ett jobb. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/generateLogicAppDefinition/åtgärd | Genererar en logikappsdefinition som baseras på ett Scheduler-jobb. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/jobhistories/Read | Hämtar jobbhistorik. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Read | Hämtar jobb. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Run/Action | Kör jobb. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Jobs/Write | Skapar eller uppdaterar jobb. |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Read | Hämta jobbsamling |
> | Action | Microsoft. Scheduler/förfrågningsåtgärder/Write | Skapar eller uppdaterar en jobbsamling. |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. search/checkNameAvailability/åtgärd | Kontrollerar tillgänglighet för tjänst namnet. |
> | Action | Microsoft. search/Operations/Read | Visar en lista över alla tillgängliga åtgärder för Microsoft. search-providern. |
> | Action | Microsoft. search/register/åtgärd | Registrerar prenumerationen för Sök resurs leverantören och gör det möjligt att skapa Sök tjänster. |
> | Action | Microsoft. search/searchServices/createQueryKey/Action | Skapar Frågeparametern. |
> | Action | Microsoft.Search/searchServices/delete | Tar bort Sök tjänsten. |
> | Action | Microsoft.Search/searchServices/deleteQueryKey/delete | Tar bort frågans nyckel. |
> | Action | Microsoft.Search/searchServices/listAdminKeys/action | Läser administrations nycklarna. |
> | Action | Microsoft.Search/searchServices/listQueryKeys/read | Returnerar listan över frågans API-nycklar för den aktuella Azure Search tjänsten. |
> | Action | Microsoft.Search/searchServices/read | Läser Sök tjänsten. |
> | Action | Microsoft.Search/searchServices/regenerateAdminKey/action | Återskapar administratörs nyckeln. |
> | Action | Microsoft. search/searchServices/start/Action | Startar Sök tjänsten. |
> | Action | Microsoft. search/searchServices/stoppa/åtgärd | Stoppar Sök tjänsten. |
> | Action | Microsoft.Search/searchServices/write | Skapar eller uppdaterar Sök tjänsten. |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/enforce/action | Tillämpar de aktuella reglerna för trafik härdning genom att skapa matchande säkerhets regler för de nätverks säkerhets grupper som tilldelats |
> | Action | Microsoft.Security/adaptiveNetworkHardenings/read | Hämtar anpassade nätverks härdnings rekommendationer för en Azure-skyddad resurs |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/read | Hämtar inställningarna för avancerat skydd för resursen |
> | Action | Microsoft.Security/advancedThreatProtectionSettings/write | Uppdaterar inställningarna för avancerat skydd för resursen |
> | Action | Microsoft.Security/alerts/read | Hämtar alla tillgängliga säkerhets aviseringar |
> | Action | Microsoft. Security/applicationWhitelistings/Read | Hämtar appens whitelistings |
> | Action | Microsoft. Security/applicationWhitelistings/Write | Skapar ett nytt program vit listning eller uppdaterar ett befintligt |
> | Action | Microsoft. Security/complianceResults/Read | Hämtar kompabilitets resultat för resursen |
> | Action | Microsoft. Security/informationProtectionPolicies/Read | Hämtar information Protection-principer för resursen |
> | Action | Microsoft.Security/informationProtectionPolicies/write | Uppdaterar information Protection-principer för resursen |
> | Action | Microsoft. Security/locations/Alerts/Activate/åtgärd | Aktivera en säkerhets avisering |
> | Action | Microsoft.Security/locations/alerts/dismiss/action | Ignorera en säkerhets avisering |
> | Action | Microsoft. Security/locations/Alerts/Read | Hämtar alla tillgängliga säkerhets aviseringar |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/delete | Tar bort just-in-time-principen för nätverks åtkomst |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/initiate/action | Initierar en just-in-Time-begäran om nätverks åtkomst princip |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/read | Hämtar just-in-Time-principer för nätverks åtkomst |
> | Action | Microsoft.Security/locations/jitNetworkAccessPolicies/write | Skapar en ny princip för nätverks åtkomst just-in-Time eller uppdaterar en befintlig |
> | Action | Microsoft. Security/locations/Read | Hämtar säkerhets data platsen |
> | Action | Microsoft. Security/locations/tasks/Activate/Action | Aktivera en säkerhets rekommendation |
> | Action | Microsoft.Security/locations/tasks/dismiss/action | Ignorera en säkerhets rekommendation |
> | Action | Microsoft. Security/locations/tasks/Read | Hämtar alla tillgängliga säkerhets rekommendationer |
> | Action | Microsoft. Security/locations/tasks/resolve/Action | Lösa en säkerhets rekommendation |
> | Action | Microsoft. Security/locations/tasks/start/Action | Starta en säkerhets rekommendation |
> | Action | Microsoft. Security/Policies/Read | Hämtar säkerhets principen |
> | Action | Microsoft.Security/policies/write | Uppdaterar säkerhets principen |
> | Action | Microsoft. Security/prissättning/ta bort | Tar bort pris inställningarna för omfånget |
> | Action | Microsoft. Security/prissättning/läsa | Hämtar pris inställningarna för omfånget |
> | Action | Microsoft. Security/prissättning/Skriv | Uppdaterar pris inställningarna för omfånget |
> | Action | Microsoft. Security/register/åtgärd | Registrerar prenumerationen för Azure Security Center |
> | Action | Microsoft. Security/securityContacts/Delete | Tar bort säkerhets kontakten |
> | Action | Microsoft. Security/securityContacts/Read | Hämtar säkerhets kontakten |
> | Action | Microsoft.Security/securityContacts/write | Uppdaterar säkerhets kontakten |
> | Action | Microsoft.Security/securitySolutions/delete | Tar bort en säkerhets lösning |
> | Action | Microsoft.Security/securitySolutions/read | Hämtar säkerhetslösningarna |
> | Action | Microsoft.Security/securitySolutions/write | Skapar en ny säkerhets lösning eller uppdaterar en befintlig |
> | Action | Microsoft.Security/securitySolutionsReferenceData/read | Hämtar säkerhets lösningens referens data |
> | Action | Microsoft.Security/securityStatuses/read | Hämtar säkerhets hälso status för Azure-resurser |
> | Action | Microsoft.Security/securityStatusesSummaries/read | Hämtar säkerhets status sammanfattningar för omfattningen |
> | Action | Microsoft.Security/settings/read | Hämtar inställningarna för omfånget |
> | Action | Microsoft.Security/settings/write | Uppdaterar inställningarna för omfånget |
> | Action | Microsoft. Security/tasks/Read | Hämtar alla tillgängliga säkerhets rekommendationer |
> | Action | Microsoft.Security/unregister/action | Avregistrerar prenumerationen från Azure Security Center |
> | Action | Microsoft. Security/webApplicationFirewalls/Delete | Tar bort en brand vägg för webbaserade program |
> | Action | Microsoft. Security/webApplicationFirewalls/Read | Hämtar brand väggar för webb program |
> | Action | Microsoft. Security/webApplicationFirewalls/Write | Skapar en ny brand vägg för webbaserade program eller uppdaterar en befintlig |
> | Action | Microsoft.Security/workspaceSettings/connect/action | Ändra inställningar för arbets ytans åter anslutning |
> | Action | Microsoft.Security/workspaceSettings/delete | Tar bort inställningarna för arbets ytan |
> | Action | Microsoft.Security/workspaceSettings/read | Hämtar inställningar för arbets yta |
> | Action | Microsoft.Security/workspaceSettings/write | Uppdaterar inställningarna för arbets ytan |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/delete | Tar bort en diagnostisk inställning |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/read | Läser en diagnostisk inställning |
> | Action | Microsoft.SecurityGraph/diagnosticsettings/write | Skriva en diagnostisk inställning |
> | Action | Microsoft.SecurityGraph/diagnosticsettingscategories/read | Läser en diagnostisk inställnings kategori |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.ServiceBus/checkNameAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. |
> | Action | Microsoft.ServiceBus/checkNamespaceAvailability/action | Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuellt Använd CheckNameAvailability i stället. |
> | Action | Microsoft.ServiceBus/locations/deleteVirtualNetworkOrSubnets/action | Tar bort reglerna för virtuellt nätverk i ServiceBus-resursprovidern för det angivna virtuella nätverket |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/action | Uppdaterar namn områdets auktoriseringsregel. Detta API är föråldrat. Använd ett parkera-anrop för att uppdatera auktoriseringsprincipen för namn områden i stället.. Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/delete | Ta bort auktoriseringsregel för namnrymd. Det går inte att ta bort standard regeln för att tillåta namnrymd.  |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/listkeys/action | Hämta anslutningssträngen till namnområdet |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/read | Hämta listan över beskrivning av auktoriseringsregler för namnområden. |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.ServiceBus/namespaces/authorizationRules/write | Skapa en auktoriseringsregel för namn områdes nivå och uppdatera dess egenskaper. Åtkomst behörighet för auktoriseringsregler, de primära och sekundära nycklarna kan uppdateras. |
> | Action | Microsoft.ServiceBus/namespaces/Delete | Ta bort namnområdesresurs |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action | Hämtar auktoriseringsregelnycklar för haveriberedskapens primära namnområde |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules/read | Hämta auktoriseringsregler för primärt namnområde för haveriberedskap |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/breakPairing/action | Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden. |
> | Action | Microsoft.ServiceBus/namespaces/disasterrecoveryconfigs/checkNameAvailability/action | Kontrollerar tillgänglighet för alias för namnområden i en viss prenumeration. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/delete | Tar bort den katastrof återställnings konfiguration som är kopplad till namn området. Den här åtgärden kan bara anropas via det primära namn området. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/failover/action | Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/read | Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Action | Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/write | Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/delete | Raderar Event Grid-filtret som är kopplat till namnområdet. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/read | Hämtar Event Grid-filtret som är kopplat till namnområdet. |
> | Action | Microsoft.ServiceBus/namespaces/eventGridFilters/write | Skapar eller uppdaterar Event Grid-filtret som är kopplat till namnutrymmet. |
> | Action | Microsoft.ServiceBus/namespaces/eventhubs/read | Hämta lista över beskrivningar av EventHub-resurser |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/delete | Ta bort IP-filterresurs |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/read | Hämta IP-filterresurs |
> | Action | Microsoft.ServiceBus/namespaces/ipFilterRules/write | Skapa IP-filterresurs |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/bläddra/åtgärd | Bläddra messges |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/skjuta/åtgärd | Skjuta upp meddelanden |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/ta emot/åtgärd | Ta emot meddelanden |
> | DataAction | Microsoft.ServiceBus/namespaces/messages/schedule/action | Shedule-meddelanden |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/skicka/åtgärd | Skicka meddelanden |
> | DataAction | Microsoft. Service Bus/namnrymder/meddelanden/setState/åtgärd | Ange sessionstillstånd |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/read | Hämtar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/messagingPlan/write | Uppdaterar meddelande planen för ett namn område.<br>Detta API är föråldrat.<br>Egenskaper som exponeras via MessagingPlan-resursen flyttas till (överordnad) namn områdes resurs i senare API-versioner..<br>Den här åtgärden stöds inte för API-version 2017-04-01. |
> | Action | Microsoft.ServiceBus/namespaces/migrate/action | Migreringsåtgärd för namnområde |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/delete | Tar bort migreringskonfigurationen. |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/read | Hämtar migreringskonfigurationen som visar migreringens status och väntande replikeringsåtgärder |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/revert/action | Återställer migreringen från standard- till Premium-namnområde |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/upgrade/action | Det DNS som är kopplat till standardnamnområdet kopplas till Premium-namnområdet vilket slutför migreringen och stoppar synkronisering av resurser från standard- till Premium-namnområdet |
> | Action | Microsoft.ServiceBus/namespaces/migrationConfigurations/write | Skapar eller uppdaterar migrerings konfigurationen. Detta kommer att börja synkronisera resurser från standard till Premium-namnområdet |
> | Action | Microsoft. Service Bus/Namespaces/networkruleset/Delete | Ta bort regelresurs för virtuellt nätverk |
> | Action | Microsoft. Service Bus/namnrymder/networkruleset/Read | Hämtar NetworkRuleSet-resurs |
> | Action | Microsoft. Service Bus/namnrymder/networkruleset/Write | Skapa regelresurs för virtuellt nätverk |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/delete | Ta bort regelresurs för virtuellt nätverk |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/read | Hämtar NetworkRuleSet-resurs |
> | Action | Microsoft.ServiceBus/namespaces/networkrulesets/write | Skapa regelresurs för virtuellt nätverk |
> | Action | Microsoft.ServiceBus/namespaces/operationresults/read | Hämta status för namnområdesåtgärd |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/read | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/diagnosticSettings/write | Hämta lista över resurs beskrivningar för namn rymds diagnostiska inställningar |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/logDefinitions/read | Hämta lista över namn rymds loggar resurs beskrivningar |
> | Action | Microsoft.ServiceBus/namespaces/providers/Microsoft.Insights/metricDefinitions/read | Hämta lista över resurs beskrivningar för namn rymds mått |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/action | Åtgärd för att uppdatera kön. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/delete | Åtgärd för borttagning av auktoriseringsregler för kön |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/listkeys/action | Hämta anslutnings strängen till kön |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/read |  Hämta listan över auktoriseringsregler för kön |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.ServiceBus/namespaces/queues/authorizationRules/write | Skapa auktoriseringsregler för kön och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Action | Microsoft.ServiceBus/namespaces/queues/Delete | Åtgärd för att ta bort en Queue-resurs |
> | Action | Microsoft.ServiceBus/namespaces/queues/read | Hämta lista med beskrivningar av köade resurser |
> | Action | Microsoft.ServiceBus/namespaces/queues/write | Skapa eller uppdatera köegenskaper. |
> | Action | Microsoft.ServiceBus/namespaces/read | Hämta listan över beskrivningar av namnområdesresurs |
> | Action | Microsoft.ServiceBus/namespaces/removeAcsNamepsace/action | Ta bort ACS-namnområdet |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/action | Åtgärd att uppdatera ämne. Den här åtgärden stöds inte för API-version 2017-04-01. Auktoriseringsregler. Använd ett parkera-anrop för att uppdatera auktoriseringsregeln. |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/delete | Åtgärd för att ta bort auktoriseringsregler för ämnen |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/listkeys/action | Hämta anslutnings strängen till ämnet |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/read |  Hämta listan med auktoriseringsregler för ämnen |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/regenerateKeys/action | Återskapa den primära eller sekundära nyckeln till resursen |
> | Action | Microsoft.ServiceBus/namespaces/topics/authorizationRules/write | Skapa auktoriseringsregler för ämnen och uppdatera dess egenskaper. Åtkomst behörigheterna för auktoriseringsregler kan uppdateras. |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/ta bort | Åtgärd för att ta bort ämnes resurs |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/läsa | Hämta lista över beskrivningar av ämnes resurser |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/Delete | Åtgärd för att ta bort TopicSubscription-resurs |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/läsa | Hämta lista över beskrivningar av TopicSubscription-resurser |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/regler/ta bort | Åtgärd för att ta bort regel resurs |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/regler/läsa | Hämta lista med beskrivningar av regel resurser |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/regler/Skriv | Skapa eller uppdatera regel egenskaper. |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/prenumerationer/skriva | Skapa eller uppdatera TopicSubscription-egenskaper. |
> | Action | Microsoft. Service Bus/namnrymder/ämnen/Skriv | Egenskaper för att skapa eller uppdatera ämnen. |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/delete | Ta bort regelresurs för virtuellt nätverk |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/read | Hämtar regelresurs för virtuellt nätverk |
> | Action | Microsoft.ServiceBus/namespaces/virtualNetworkRules/write | Skapa regelresurs för virtuellt nätverk |
> | Action | Microsoft.ServiceBus/namespaces/write | Skapa en namn områdes resurs och uppdatera dess egenskaper. Namn områdets Taggar och kapacitet är de egenskaper som kan uppdateras. |
> | Action | Microsoft.ServiceBus/operations/read | Hämta åtgärder |
> | Action | Microsoft. Service Bus/register/åtgärd | Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser |
> | Action | Microsoft.ServiceBus/sku/read | Hämta lista med resurs beskrivningar för SKU |
> | Action | Microsoft.ServiceBus/sku/regions/read | Hämta lista över beskrivningar av SkuRegions-resurser |
> | Action | Microsoft.ServiceBus/unregister/action | Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. ServiceFabric/kluster/program/ta bort | Ta bort valfritt program |
> | Action | Microsoft.ServiceFabric/clusters/applications/read | Läs valfritt program |
> | Action | Microsoft. ServiceFabric/kluster/program/tjänster/ta bort | Ta bort valfri tjänst |
> | Action | Microsoft. ServiceFabric/kluster/program/tjänster/partitioner/läsa | Läs valfri partition |
> | Action | Microsoft.ServiceFabric/clusters/applications/services/partitions/replicas/read | Läs valfri replik |
> | Action | Microsoft. ServiceFabric/kluster/program/tjänster/läsa | Läs valfri tjänst |
> | Action | Microsoft. ServiceFabric/kluster/program/tjänster/status/läsning | Läs valfri tjänststatus |
> | Action | Microsoft. ServiceFabric/kluster/program/tjänster/skrivning | Skapa eller uppdatera valfri tjänst |
> | Action | Microsoft. ServiceFabric/kluster/program/Skriv | Skapa eller uppdatera valfritt program |
> | Action | Microsoft. ServiceFabric/kluster/applicationTypes/Delete | Ta bort valfri programtyp |
> | Action | Microsoft. ServiceFabric/kluster/applicationTypes/Read | Läs valfri programtyp |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/versions/Delete | Ta bort valfri version av programtyp |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/versions/Read | Läs valfri version av programtyp |
> | Action | Microsoft. ServiceFabric/Clusters/applicationTypes/versions/Write | Skapa eller uppdatera valfri version av programtyp |
> | Action | Microsoft. ServiceFabric/kluster/applicationTypes/Write | Skapa eller uppdatera valfri programtyp |
> | Action | Microsoft. ServiceFabric/kluster/ta bort | Ta bort valfritt kluster |
> | Action | Microsoft.ServiceFabric/clusters/nodes/read | Läs valfri nod |
> | Action | Microsoft. ServiceFabric/kluster/läsa | Läs valfritt kluster |
> | Action | Microsoft. ServiceFabric/kluster/status/läsning | Läs valfri klusterstatus |
> | Action | Microsoft.ServiceFabric/clusters/write | Skapa eller uppdatera valfritt kluster |
> | Action | Microsoft.ServiceFabric/locations/clusterVersions/read | Läs valfri klusterversion |
> | Action | Microsoft.ServiceFabric/locations/environments/clusterVersions/read | Läs valfri klusterversion för en viss miljö |
> | Action | Microsoft.ServiceFabric/locations/operationresults/read | Läs valfritt åtgärdsresultat |
> | Action | Microsoft.ServiceFabric/locations/operations/read | Läs valfri åtgärd efter plats |
> | Action | Microsoft.ServiceFabric/operations/read | Läs valfri tillgänglig åtgärd |
> | Action | Microsoft. ServiceFabric/register/åtgärd | Registrera valfri åtgärd |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. SignalRService/locations/checknameavailability/Action | Kontrollerar om ett namn är tillgängligt för användning med en ny signal tjänst |
> | Action | Microsoft. SignalRService/locations/operationresults/signalerer/Read | Fråga efter status för en asynkron åtgärd |
> | Action | Microsoft.SignalRService/locations/operationStatuses/operationId/read | Fråga efter status för en asynkron åtgärd |
> | Action | Microsoft. SignalRService/platser/användning/läsa | Hämta kvot användningarna för Azure SignalR service |
> | Action | Microsoft. SignalRService/operationresults/Read | Fråga efter status för en asynkron åtgärd |
> | Action | Microsoft. SignalRService/operationstatus/Read | Fråga efter status för en asynkron åtgärd |
> | Action | Microsoft. SignalRService/register/åtgärd | Registrerar resurs leverantören "Microsoft. SignalRService" med en prenumeration |
> | Action | Microsoft. SignalRService/SignalR/Delete | Ta bort hela signal tjänsten |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/delete | Ta bort ett event Grid-filter från en signaler. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/read | Hämta egenskaperna för det angivna Event Grid-filtret eller visar alla Event Grid-filter för den angivna Signaleraren. |
> | Action | Microsoft.SignalRService/SignalR/eventGridFilters/write | Skapa eller uppdatera ett event Grid-filter för en SignalR med de angivna parametrarna. |
> | Action | Microsoft. SignalRService/SignalR/listnycklar/åtgärd | Visa värdet för SignalR åtkomst nycklar i hanterings portalen eller via API |
> | Action | Microsoft.SignalRService/SignalR/read | Visa signalers inställningar och konfigurationer i hanterings portalen eller via API |
> | Action | Microsoft. SignalRService/SignalR/regeneratekey/åtgärd | Ändra värdet för SignalR åtkomst nycklar i hanterings portalen eller via API |
> | Action | Microsoft. SignalRService/SignalR/avomstart/åtgärd | Starta om en Azure SignalR-tjänst i hanterings portalen eller via API. Det kommer att finnas vissa stillestånds tider. |
> | Action | Microsoft. SignalRService/Signalerare/Write | Ändra signal hanterarens inställningar och konfigurationer i hanterings portalen eller via API |
> | Action | Microsoft. SignalRService/avregistrera/åtgärd | Avregistrerar resurs leverantören "Microsoft. SignalRService" med en prenumeration |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Solutions/applicationDefinitions/applicationArtifacts/read | Visar lista över programartefakter för programdefinition. |
> | Action | Microsoft.Solutions/applicationDefinitions/delete | Tar bort en programdefinition. |
> | Action | Microsoft.Solutions/applicationDefinitions/read | Hämtar en lista över programdefinitioner. |
> | Action | Microsoft.Solutions/applicationDefinitions/write | Lägg till eller ändra en programdefinition. |
> | Action | Microsoft. Solutions/Applications/applicationArtifacts/Read | Visar lista över programartefakter. |
> | Action | Microsoft. Solutions/Applications/Delete | Tar bort ett program. |
> | Action | Microsoft. Solutions/program/läsa | Hämtar en lista över program. |
> | Action | Microsoft. Solutions/Applications/refreshPermissions/åtgärd | Uppdaterar programbehörighet(er). |
> | Action | Microsoft.Solutions/applications/updateAccess/action | Uppdaterar program åtkomst. |
> | Action | Microsoft.Solutions/applications/write | Skapar ett program. |
> | Action | Microsoft. Solutions/jitRequests/Delete | Ta bort en JitRequest |
> | Action | Microsoft.Solutions/jitRequests/read | Hämtar en lista över JitRequests |
> | Action | Microsoft.Solutions/jitRequests/write | Skapar en JitRequest |
> | Action | Microsoft. Solutions/locations/operationStatuses/Read | Läser resursens åtgärdsstatus. |
> | Action | Microsoft. Solutions/register/åtgärd | Registrera dig för lösningar. |
> | Action | Microsoft.Solutions/unregister/action | Avregistrerar från lösningar. |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. SQL/checkNameAvailability/Action | Verifiera om det tillhandahållna Server namnet är tillgängligt för etablering över hela världen för en specifik prenumeration. |
> | Action | Microsoft.Sql/instancePools/delete | Tar bort en instans-pool |
> | Action | Microsoft.Sql/instancePools/read | Hämtar en instans-pool |
> | Action | Microsoft.Sql/instancePools/usages/read | Hämtar användnings information för en instans pool |
> | Action | Microsoft.Sql/instancePools/write | Skapar eller uppdaterar en instans-pool |
> | Action | Microsoft.Sql/locations/auditingSettingsAzureAsyncOperation/read | Hämta resultatet av åtgärden uppsättnings princip för utökad server-BLOB-granskning |
> | Action | Microsoft.Sql/locations/auditingSettingsOperationResults/read | Hämta resultatet av åtgärden SET för Server BLOB Auditing-princip |
> | Action | Microsoft. SQL/locations/capabilites/Read | Hämtar funktionerna för den här prenumerationen på en specifik plats |
> | Action | Microsoft.Sql/locations/databaseAzureAsyncOperation/read | Hämtar status för en databas åtgärd. |
> | Action | Microsoft.Sql/locations/databaseOperationResults/read | Hämtar status för en databas åtgärd. |
> | Action | Microsoft. SQL/locations/deletedServerAsyncOperation/Read | Hämtar pågående åtgärder på borttagen Server |
> | Action | Microsoft.Sql/locations/deletedServerOperationResults/read | Hämtar pågående åtgärder på borttagen Server |
> | Action | Microsoft. SQL/locations/deletedServers/Read | Returnera listan över borttagna servrar eller hämtar egenskaperna för den angivna borttagna servern. |
> | Action | Microsoft. SQL/locations/deletedServers/Recover/Action | Återställa en borttagen Server |
> | Action | Microsoft.Sql/locations/elasticPoolAzureAsyncOperation/read | Hämtar Azure-asynkron åtgärd för asynkron åtgärd i elastisk pool |
> | Action | Microsoft.Sql/locations/elasticPoolOperationResults/read | Hämtar resultatet av en åtgärd för elastisk pool. |
> | Action | Microsoft.Sql/locations/encryptionProtectorAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering krypterings skydd |
> | Action | Microsoft.Sql/locations/encryptionProtectorOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering krypterings skydd |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsAzureAsyncOperation/read | Hämta resultatet av åtgärden uppsättnings princip för utökad server-BLOB-granskning |
> | Action | Microsoft.Sql/locations/extendedAuditingSettingsOperationResults/read | Hämta resultatet av åtgärden uppsättnings princip för utökad server-BLOB-granskning |
> | Action | Microsoft.Sql/locations/firewallRulesAzureAsyncOperation/read | Hämtar status för en brand Väggs regel åtgärd. |
> | Action | Microsoft.Sql/locations/firewallRulesOperationResults/read | Hämtar status för en brand Väggs regel åtgärd. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/delete | Tar bort en befintlig grupp för redundans. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/failover/action | Kör Planerad redundans i en befintlig instans för redundans. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/forceFailoverAllowDataLoss/action | Kör framtvingad redundansväxling i en befintlig grupp för redundans. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/read | Returnerar listan över instans-redundansväxlingen eller hämtar egenskaperna för den angivna instansen av redundans. |
> | Action | Microsoft.Sql/locations/instanceFailoverGroups/write | Skapar en instans av redundans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna instansen av redundans. |
> | Action | Microsoft.Sql/locations/instancePoolAzureAsyncOperation/read | Hämtar status för en instans av en instans |
> | Action | Microsoft.Sql/locations/instancePoolOperationResults/read | Hämtar resultatet för en instans av en instans av poolen |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileAzureAsyncOperation/read | Returnerar information om en angiven gränssnitts slut punkt för Azure asynkron åtgärd |
> | Action | Microsoft.Sql/locations/interfaceEndpointProfileOperationResults/read | Returnerar information om den angivna åtgärden för gränssnitts slut punkts profil |
> | Action | Microsoft.Sql/locations/jobAgentAzureAsyncOperation/read | Hämtar status för en jobb agent åtgärd. |
> | Action | Microsoft.Sql/locations/jobAgentOperationResults/read | Hämtar resultatet av en jobb agent åtgärd. |
> | Action | Microsoft.Sql/locations/longTermRetentionBackups/read | Visar en lista över långsiktig kvarhållning av säkerhets kopior för varje databas på varje server på en plats |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read | Visar en lista över långsiktig kvarhållning av säkerhets kopior för varje databas på en server |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete | Tar bort en säkerhets kopia av långsiktig kvarhållning |
> | Action | Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read | Visar en lista över långsiktig kvarhållning av säkerhets kopior för en databas |
> | Action | Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action | Slutför återställnings åtgärden för hanterade databaser |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering hanterad instans krypterings skydd |
> | Action | Microsoft.Sql/locations/managedInstanceEncryptionProtectorOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering hanterad instans krypterings skydd |
> | Action | Microsoft.Sql/locations/managedInstanceKeyAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering hanterade instans nycklar |
> | Action | Microsoft.Sql/locations/managedInstanceKeyOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering hanterade instans nycklar |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionAzureAsyncOperation/read | Hämtar pågående åtgärder för transparent data kryptering i hanterad databas |
> | Action | Microsoft.Sql/locations/managedTransparentDataEncryptionOperationResults/read | Hämtar pågående åtgärder för transparent data kryptering i hanterad databas |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionAzureAsyncOperation/read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionOperationResults/read | Hämtar resultatet för en anslutnings åtgärd för privat slut punkt |
> | Action | Microsoft. SQL/locations/privateEndpointConnectionProxyAzureAsyncOperation/Read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Action | Microsoft.Sql/locations/privateEndpointConnectionProxyOperationResults/read | Hämtar resultatet för en åtgärd för en privat slut punkts anslutnings proxy |
> | Action | Microsoft. SQL/locations/Read | Hämtar tillgängliga platser för en specifik prenumeration |
> | Action | Microsoft.Sql/locations/serverKeyAzureAsyncOperation/read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Action | Microsoft.Sql/locations/serverKeyOperationResults/read | Hämtar pågående åtgärder på transparent data kryptering Server nycklar |
> | Action | Microsoft.Sql/locations/syncAgentOperationResults/read | Hämta resultatet av resurs åtgärden för Sync-agenten |
> | Action | Microsoft.Sql/locations/syncDatabaseIds/read | Hämta Synkronisera databas-ID: n för en viss region och prenumeration |
> | Action | Microsoft.Sql/locations/syncGroupOperationResults/read | Hämta resultatet av resurs åtgärden för Sync-gruppen |
> | Action | Microsoft.Sql/locations/syncMemberOperationResults/read | Hämta resultatet av resurs åtgärden för synkronisera medlem |
> | Action | Microsoft. SQL/locations/usages/Read | Hämtar en samling användnings statistik för den här prenumerationen på en plats |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesAzureAsyncOperation/read | Returnerar information om de angivna virtuella nätverks reglerna Azure async operation  |
> | Action | Microsoft.Sql/locations/virtualNetworkRulesOperationResults/read | Returnerar information om den angivna åtgärden för virtuella nätverks regler  |
> | Action | Microsoft. SQL/managedInstances/administrators/Delete | Tar bort en befintlig administratör för den hanterade instansen. |
> | Action | Microsoft. SQL/managedInstances/administrators/Read | Hämtar en lista över hanterade instans administratörer. |
> | Action | Microsoft. SQL/managedInstances/administrators/Write | Skapar eller uppdaterar hanterad instans administratör med de angivna parametrarna. |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en hanterad databas |
> | Action | Microsoft.Sql/managedInstances/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en hanterad databas |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Action | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/write | Uppdatera känslighets etiketter för batch |
> | Action | Microsoft. SQL/managedInstances/databaser/Delete | Tar bort en befintlig hanterad databas |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft. SQL/managedInstances/databaser/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Sql/managedInstances/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instans databaser |
> | Action | Microsoft.Sql/managedInstances/databases/read | Hämtar befintlig hanterad databas |
> | Action | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/read | Hämta ett hanterat databas schema. |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/read | Hämta en hanterad databas-kolumn |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort känslighets etiketten för en specifik kolumn |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighets rekommendationer för en specifik kolumn |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighets rekommendationer för en specifik kolumn |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighets etiketten för en specifik kolumn |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighets etiketten för en specifik kolumn |
> | Action | Microsoft.Sql/managedInstances/databases/schemas/tables/read | Hämta en hanterad databas tabell |
> | Action | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/Read | Hämta en lista över principer för hot identifiering för hanterade databaser som kon figurer ATS för en angiven server |
> | Action | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/Write | Ändra principen för databas hot identifiering för en specifik hanterad databas |
> | Action | Microsoft.Sql/managedInstances/databases/securityEvents/read | Hämtar säkerhets händelser för hanterade databaser |
> | Action | Microsoft.Sql/managedInstances/databases/sensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/read | Hämta information om databasen transparent datakryptering på en specifik hanterad databas |
> | Action | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/write | Ändra databas transparent datakryptering för en specifik hanterad databas |
> | Action | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik databas |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/read | Hämta principerna för sårbarhets bedömning på en givendatabase |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/delete | Ta bort regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/rules/baselines/read | Hämta regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Action | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Rules/planal/Write | Ändra regelns bas linje för sårbarhets bedömning för en specifik databas |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/export/action | Konvertera ett befintligt skannings resultat till ett läsligt format. Om det redan finns händer ingenting |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/initiateScan/action | Kör databas genomsökning för sårbarhets bedömning. |
> | Action | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/scans/read | Returnera en lista över genomsöknings poster för databas sårbarhets bedömning eller hämta skannings posten för angivet skannings-ID. |
> | Action | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik databas |
> | Action | Microsoft. SQL/managedInstances/databaser/skriva | Skapar en ny databas eller uppdaterar en befintlig databas. |
> | Action | Microsoft. SQL/managedInstances/Delete | Tar bort en befintlig hanterad instans. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/read | Returnerar en lista över Server krypterings skydd eller hämtar egenskaperna för det angivna Server krypterings skyddet. |
> | Action | Microsoft.Sql/managedInstances/encryptionProtector/write | Uppdatera egenskaperna för det angivna Server krypterings skyddet. |
> | Action | Microsoft. SQL/managedInstances/Keys/Delete | Tar bort en befintlig Azure SQL-hanterad instans nyckel. |
> | Action | Microsoft.Sql/managedInstances/keys/read | Returnera listan över hanterade instans nycklar eller hämtar egenskaperna för den angivna hanterade instans nyckeln. |
> | Action | Microsoft.Sql/managedInstances/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hanterade instans nyckeln. |
> | Action | Microsoft.Sql/managedInstances/metricDefinitions/read | Hämta mått definitioner för hanterade instanser |
> | Action | Microsoft. SQL/managedInstances/Metrics/Read | Hämta mått för hanterade instanser |
> | Action | Microsoft. SQL/managedInstances/providers/Microsoft. Insights/diagnosticSettings/Read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft. SQL/managedInstances/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för hanterade instanser |
> | Action | Microsoft.Sql/managedInstances/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för hanterade instanser |
> | Action | Microsoft.Sql/managedInstances/read | Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hanterade instansen. |
> | Action | Microsoft.Sql/managedInstances/recoverableDatabases/read | Returnerar en lista med återställnings bara hanterade databaser |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en borttagen hanterad databas |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en borttagen hanterad databas |
> | Action | Microsoft.Sql/managedInstances/restorableDroppedDatabases/read | Returnerar en lista med återställas nedbrutna hanterade databaser. |
> | Action | Microsoft. SQL/managedInstances/securityAlertPolicies/Read | Hämta en lista över principer för identifiering av hanterade servrar som kon figurer ATS för en angiven server |
> | Action | Microsoft. SQL/managedInstances/securityAlertPolicies/Write | Ändra principen för hot identifiering för hanterade servrar för en specifik hanterad server |
> | Action | Microsoft.Sql/managedInstances/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Action | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik hanterad instans |
> | Action | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Read | Hämta principerna för sårbarhets bedömning på en specifik hanterad instans |
> | Action | Microsoft. SQL/managedInstances/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik hanterad instans |
> | Action | Microsoft. SQL/managedInstances/Write | Skapar en hanterad instans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hanterade instansen. |
> | Action | Microsoft. SQL/Operations/Read | Hämtar tillgängliga REST-åtgärder |
> | Action | Microsoft. SQL/privateEndpointConnectionsApproval/Action | Anger om användaren får godkänna en privat slut punkts anslutning |
> | Action | Microsoft. SQL/register/åtgärd | Registrerar prenumerationen för Microsoft SQL Database Resource Provider och gör det möjligt att skapa Microsoft SQL-databaser. |
> | Action | Microsoft.Sql/servers/administratorOperationResults/read | Hämtar pågående åtgärder på Server administratörer |
> | Action | Microsoft. SQL/Servers/administrators/Delete | Ta bort Server administratör |
> | Action | Microsoft. SQL/Servers/administrators/Read | Hämta information om Server administratör |
> | Action | Microsoft. SQL/Servers/administrators/Write | Skapa eller uppdatera Server administratör |
> | Action | Microsoft.Sql/servers/advisors/read | Returnerar lista över rådgivare som är tillgängliga för servern |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angiven rådgivare för servern |
> | Action | Microsoft.Sql/servers/advisors/recommendedActions/write | Använd den rekommenderade åtgärden på servern |
> | Action | Microsoft.Sql/servers/advisors/write | Uppdaterar automatiskt körnings status för en rådgivare på server nivå. |
> | Action | Microsoft.Sql/servers/auditingPolicies/read | Hämta information om standard gransknings principen för Server tabeller som kon figurer ATS på en specifik server |
> | Action | Microsoft.Sql/servers/auditingPolicies/write | Ändra standard granskning av Server tabell för en specifik server |
> | Action | Microsoft.Sql/servers/auditingSettings/operationResults/read | Hämta resultatet av åtgärden SET för Server BLOB Auditing-princip |
> | Action | Microsoft.Sql/servers/auditingSettings/read | Hämta information om den gransknings princip för Server-blob som kon figurer ATS på en specifik server |
> | Action | Microsoft.Sql/servers/auditingSettings/write | Ändra serverns BLOB-granskning för en specifik server |
> | Action | Microsoft.Sql/servers/automaticTuning/read | Returnerar inställningar för automatisk justering för servern |
> | Action | Microsoft.Sql/servers/automaticTuning/write | Uppdaterar automatiska justerings inställningar för servern och returnerar uppdaterade inställningar |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/delete | Tar bort ett befintligt valv för säkerhets kopiering. |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/read | Den här åtgärden används för att hämta en säkerhets kopia av ett långsiktigt bevarande valv. Den returnerar information om valvet som har registrerats på den här servern |
> | Action | Microsoft.Sql/servers/backupLongTermRetentionVaults/write | Den här åtgärden används för att registrera ett säkerhets kopierings valv med långsiktigt bevarande till en server |
> | Action | Microsoft.Sql/servers/communicationLinks/delete | Tar bort en befintlig server kommunikations länk. |
> | Action | Microsoft.Sql/servers/communicationLinks/read | Returnera listan över kommunikations Länkar för en angiven server. |
> | Action | Microsoft.Sql/servers/communicationLinks/write | Skapa eller uppdatera en server kommunikations länk. |
> | Action | Microsoft.Sql/servers/connectionPolicies/read | Returnera listan över Server anslutnings principer för en angiven server. |
> | Action | Microsoft.Sql/servers/connectionPolicies/write | Skapa eller uppdatera en princip för Server anslutning. |
> | Action | Microsoft.Sql/servers/databases/advisors/read | Returnerar lista över rådgivare som är tillgängliga för databasen |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder för angiven rådgivare för databasen |
> | Action | Microsoft.Sql/servers/databases/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på databasen |
> | Action | Microsoft.Sql/servers/databases/advisors/write | Uppdatera status för automatisk körning av en rådgivare på databas nivå. |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/read | Hämta information om tabell gransknings principen som kon figurer ATS för en angiven databas |
> | Action | Microsoft.Sql/servers/databases/auditingPolicies/write | Ändra tabell gransknings principen för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/read | Hämta information om den avgransknings princip för BLOB som kon figurer ATS för en angiven databas |
> | Action | Microsoft.Sql/servers/databases/auditingSettings/write | Ändra BLOB Auditing-principen för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/auditRecords/read | Hämta databasens BLOB audit-poster |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/read | Returnerar inställningar för automatisk justering för en databas |
> | Action | Microsoft.Sql/servers/databases/automaticTuning/write | Uppdaterar automatiska justerings inställningar för en databas och returnerar uppdaterade inställningar |
> | Action | Microsoft.Sql/servers/databases/azureAsyncOperation/read | Hämtar status för en databas åtgärd. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/read | Returnera listan över principer för arkivering av säkerhets kopiering för en angiven databas. |
> | Action | Microsoft.Sql/servers/databases/backupLongTermRetentionPolicies/write | Skapa eller uppdatera en princip för säkerhets kopiering av databas. |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/read | Hämtar en kortsiktig bevarande princip för en databas |
> | Action | Microsoft.Sql/servers/databases/backupShortTermRetentionPolicies/write | Uppdaterar en kortsiktig bevarande princip för en databas |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/read | Hämta information om anslutnings principen som kon figurer ATS för en angiven databas |
> | Action | Microsoft.Sql/servers/databases/connectionPolicies/write | Ändra anslutnings princip för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/currentSensitivityLabels/write | Uppdatera känslighets etiketter för batch |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/read | Returnera listan över data masknings principer för databasen. |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/delete | Ta bort data mask princip regel för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/read | Hämta information om den princip regel för data maskering som kon figurer ATS för en angiven databas |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/rules/write | Ändra princip regel för data maskning för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/dataMaskingPolicies/write | Ändra data masking-princip för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read | Returnerar steg informationen för den distribuerade frågan för data lager frågan för valt steg-ID |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseQueries/read | Returnerar information om distributions frågan för data lagret för det valda fråge-ID: t |
> | Action | Microsoft.Sql/servers/databases/dataWarehouseUserActivities/read | Hämtar användar aktiviteter för en SQL Data Warehouse-instans som omfattar körnings-och inaktiverade frågor |
> | Action | Microsoft.Sql/servers/databases/delete | Tar bort en befintlig databas. |
> | Action | Microsoft.Sql/servers/databases/export/action | Exportera Azure SQL Database |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Hämta information om den utökade blobb gransknings principen som kon figurer ATS för en viss databas |
> | Action | Microsoft.Sql/servers/databases/extendedAuditingSettings/write | Ändra den utökade blobb gransknings principen för en viss databas |
> | Action | Microsoft.Sql/servers/databases/extensions/read | Hämtar en samling tillägg för databasen. |
> | Action | Microsoft.Sql/servers/databases/extensions/write | Ändra tillägget för en specifik databas |
> | Action | Microsoft. SQL/Servers/databaser/redundans/åtgärd | Kundens initierade databas växling vid fel. |
> | Action | Microsoft. SQL/Servers/databaser/geoBackupPolicies/Read | Hämta principer för geo-säkerhetskopiering för en specifik databas |
> | Action | Microsoft. SQL/Servers/databaser/geoBackupPolicies/Write | Skapa eller uppdatera en databas princip för säkerhets kopiering |
> | Action | Microsoft.Sql/servers/databases/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindowOptions/read | Hämtar en lista över tillgängliga underhålls fönster för en vald databas. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/read | Hämtar inställningar för Windows-underhåll för en vald databas. |
> | Action | Microsoft.Sql/servers/databases/maintenanceWindows/write | Anger inställningar för Windows-underhåll för en vald databas. |
> | Action | Microsoft.Sql/servers/databases/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Action | Microsoft.Sql/servers/databases/metrics/read | Returnera mått för databaser |
> | Action | Microsoft. SQL/Servers/databaser/flytta/åtgärd | Ändra namnet på en befintlig databas. |
> | Action | Microsoft.Sql/servers/databases/operationResults/read | Hämtar status för en databas åtgärd. |
> | Action | Microsoft. SQL/Servers/databaser/åtgärder/Avbryt/åtgärd | Avbryter Azure SQL Database väntande asynkron åtgärd som inte har slutförts än. |
> | Action | Microsoft.Sql/servers/databases/operations/read | Returnera listan över åtgärder som utförts på databasen |
> | Action | Microsoft. SQL/Servers/databaser/paus/åtgärd | Pausa Azure SQL Data Warehouse-databas |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft. SQL/Servers/databaser/providers/Microsoft. Insights/diagnosticSettings/Write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/logDefinitions/read | Hämtar tillgängliga loggar för databaser |
> | Action | Microsoft.Sql/servers/databases/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för databaser |
> | Action | Microsoft.Sql/servers/databases/queryStore/queryTexts/read | Returnerar samlingen av fråge texter som motsvarar de angivna parametrarna. |
> | Action | Microsoft.Sql/servers/databases/queryStore/read | Returnerar aktuella värden för inställningarna för Frågearkivet för databasen. |
> | Action | Microsoft.Sql/servers/databases/queryStore/write | Uppdaterar inställning för Frågearkivet för databasen |
> | Action | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämta egenskaperna för den angivna databasen. |
> | Action | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Action | Microsoft. SQL/Servers/-databaser/replicationLinks/Delete | Avsluta detvingad replikering av replikeringsrelationen och med potentiell data förlust |
> | Action | Microsoft. SQL/Servers/databaser/replicationLinks/redundans/åtgärd | Redundans efter synkronisering av alla ändringar från den primära, som gör databasen till den primära relationship\u0027s-servern och göra den till en sekundär |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action | Redundansväxling omedelbart med potentiell data förlust, vilket gör databasen till den primära replikerings-relationship\u0027s och gör den till en sekundär |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/read | Returnera listan över länkar för replikering eller hämta egenskaperna för de angivna replikeringslänken. |
> | Action | Microsoft. SQL/Servers/databaser/replicationLinks/ta bort länk/åtgärd | Avsluta replikeringen framtvingar eller efter synkronisering med partnern |
> | Action | Microsoft.Sql/servers/databases/replicationLinks/updateReplicationMode/action | Uppdatera replikeringsläget för länk till synkront eller asynkront läge |
> | Action | Microsoft.Sql/servers/databases/restorePoints/action | Skapar en ny återställningspunkt |
> | Action | Microsoft.Sql/servers/databases/restorePoints/delete | Tar bort en återställnings punkt för databasen. |
> | Action | Microsoft.Sql/servers/databases/restorePoints/read | Returnerar återställnings punkter för databasen. |
> | Action | Microsoft. SQL/Servers/databaser/återuppta/åtgärd | Återuppta Azure SQL Data Warehouse-databas |
> | Action | Microsoft.Sql/servers/databases/schemas/read | Hämta ett databas schema. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta en databas kolumn. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/delete | Ta bort känslighets etiketten för en specifik kolumn |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/disable/action | Inaktivera känslighets rekommendationer för en specifik kolumn |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/enable/action | Aktivera känslighets rekommendationer för en specifik kolumn |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/read | Hämta känslighets etiketten för en specifik kolumn |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/write | Skapa eller uppdatera känslighets etiketten för en specifik kolumn |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/read | Hämta en databas tabell. |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/read | Hämta lista över index rekommendationer för en databas |
> | Action | Microsoft.Sql/servers/databases/schemas/tables/recommendedIndexes/write | Använd index rekommendation |
> | Action | Microsoft. SQL/Servers/databaser/securityAlertPolicies/Read | Hämta en lista över de principer för databas hot identifiering som kon figurer ATS för en angiven server |
> | Action | Microsoft. SQL/Servers/databaser/securityAlertPolicies/Write | Ändra principen för databas hot identifiering för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/securityMetrics/read | Hämtar en samling databas säkerhets mått |
> | Action | Microsoft.Sql/servers/databases/sensitivityLabels/read | Visa känslighets etiketter för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/serviceTierAdvisors/read | Returnera förslag om att skala databasen upp eller ned baserat på statistik för körning av fråga för att förbättra prestandan eller minska kostnaderna |
> | Action | Microsoft.Sql/servers/databases/skus/read | Hämtar en samling SKU: er som är tillgängliga för en databas |
> | Action | Microsoft.Sql/servers/databases/syncGroups/cancelSync/action | Avbryt synkronisering av synkronisera grupp |
> | Action | Microsoft.Sql/servers/databases/syncGroups/delete | Tar bort en befintlig Sync-grupp. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/hubSchemas/read | Returnera listan över databas scheman för Sync Hub |
> | Action | Microsoft.Sql/servers/databases/syncGroups/logs/read | Returnera listan över synkroniserade grupp loggar |
> | Action | Microsoft.Sql/servers/databases/syncGroups/read | Returnera listan över Sync-grupper eller hämta egenskaperna för den angivna Sync-gruppen. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchema/action | Uppdatera databas schema för Sync-hubb |
> | Action | Microsoft.Sql/servers/databases/syncGroups/refreshHubSchemaOperationResults/read | Hämta resultatet av schema uppdaterings åtgärden för Sync-hubben |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/delete | Tar bort en befintlig synkroniserad medlem. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/read | Returnera listan över synkroniserade medlemmar eller hämta egenskaperna för den angivna Sync-medlemmen. |
> | Action | Microsoft. SQL/Servers/databases/syncGroups/syncMembers/refreshSchema/Action | Uppdatera synkronisera medlems schema |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read | Hämta resultatet av uppdaterings åtgärden för synkronisering av medlems schema |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/schemas/read | Returnera listan över databas scheman för synkronisering av medlemmar |
> | Action | Microsoft.Sql/servers/databases/syncGroups/syncMembers/write | Skapar en synkroniserad medlem med de angivna parametrarna eller uppdaterar egenskaperna för den angivna Sync-medlemmen. |
> | Action | Microsoft.Sql/servers/databases/syncGroups/triggerSync/action | Utlös synkronisering av synkroniserad grupp |
> | Action | Microsoft.Sql/servers/databases/syncGroups/write | Skapar en Sync-grupp med de angivna parametrarna eller uppdaterar egenskaperna för den angivna Sync-gruppen. |
> | Action | Microsoft.Sql/servers/databases/topQueries/queryText/action | Returnerar Transact-SQL-texten för valt fråge-ID |
> | Action | Microsoft.Sql/servers/databases/topQueries/read | Returnerar sammanställd körnings statistik för den valda frågan under den valda tids perioden |
> | Action | Microsoft.Sql/servers/databases/topQueries/statistics/read | Returnerar sammanställd körnings statistik för den valda frågan under den valda tids perioden |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/operationResults/read | Hämtar pågående åtgärder vid transparent data kryptering |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/read | Hämta status och information om funktionen transparent data krypterings säkerhet för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/transparentDataEncryption/write | Ändra status för transparent data kryptering |
> | Action | Microsoft. SQL/Servers/databaser/upgradeDataWarehouse/åtgärd | Uppgradera Azure SQL Data Warehouse-databas |
> | Action | Microsoft. SQL/Servers/databaser/användning/läsa | Hämtar information om Azure SQL Database användning |
> | Action | Microsoft. SQL/Servers/-databaser/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik databas |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/Read | Hämta principerna för sårbarhets bedömning på en givendatabase |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/regler/bas linjer/ta bort | Ta bort regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/regler/bas linjer/läsning | Hämta regel bas linjen för sårbarhets bedömning för en specifik databas |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/regler/bas linjer/skrivning | Ändra regelns bas linje för sårbarhets bedömning för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/export/action | Konvertera ett befintligt skannings resultat till ett läsligt format. Om det redan finns händer ingenting |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessments/scans/initiateScan/action | Kör databas genomsökning för sårbarhets bedömning. |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/skanningar/läsa | Returnera en lista över genomsöknings poster för databas sårbarhets bedömning eller hämta skannings posten för angivet skannings-ID. |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/action | Kör databas genomsökning för sårbarhets bedömning. |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/operationResults/read | Hämta resultatet av databasens sårbarhets körnings åtgärd för sårbarhets bedömning |
> | Action | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/read | Hämta information om den sårbarhets bedömning som kon figurer ATS för en angiven databas |
> | Action | Microsoft. SQL/Servers/databaser/vulnerabilityAssessmentSettings/Write | Ändra sårbarhets bedömning för en specifik databas |
> | Action | Microsoft.Sql/servers/databases/write | Skapar en databas med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna databasen. |
> | Action | Microsoft. SQL/Servers/Delete | Tar bort en befintlig server. |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/delete | Tar bort en befintlig katastrof återställnings konfiguration för en specifik server |
> | Action | Microsoft. SQL/Servers/disasterRecoveryConfiguration/redundans/åtgärd | Redundansväxla en DisasterRecoveryConfiguration |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action | Framtvinga redundans a DisasterRecoveryConfiguration |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/read | Hämtar en samling haveri beredskaps konfigurationer som inkluderar den här servern |
> | Action | Microsoft.Sql/servers/disasterRecoveryConfiguration/write | Ändra server haveri återställnings konfiguration |
> | Action | Microsoft.Sql/servers/elasticPoolEstimates/read | Returnerar listan med uppskattningar för elastisk pool som redan har skapats för den här servern |
> | Action | Microsoft. SQL/Servers/elasticPoolEstimates/Write | Skapar ny uppskattning av elastisk pool för lista över databaser som tillhandahålls |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/read | Returnerar lista över rådgivare som är tillgängliga för den elastiska poolen |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/read | Returnerar lista över rekommenderade åtgärder med angiven rådgivare för den elastiska poolen |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/recommendedActions/write | Tillämpa den rekommenderade åtgärden på den elastiska poolen |
> | Action | Microsoft.Sql/servers/elasticPools/advisors/write | Uppdatera status för automatisk körning av en rådgivare på elastisk Poolnivå. |
> | Action | Microsoft.Sql/servers/elasticPools/databases/read | Hämtar en lista över databaser för en elastisk pool |
> | Action | Microsoft. SQL/Servers/elasticPools/Delete | Ta bort befintlig elastisk pool |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolActivity/read | Hämta aktiviteter och information om en specifik Elastic Database-pool |
> | Action | Microsoft.Sql/servers/elasticPools/elasticPoolDatabaseActivity/read | Hämta aktiviteter och information om en specifik databas som är en del av Elastic Database-pool |
> | Action | Microsoft. SQL/Servers/elasticPools/redundans/åtgärd | Kunden initierade elastisk pool-redundans. |
> | Action | Microsoft.Sql/servers/elasticPools/metricDefinitions/read | Returnera typer av mått som är tillgängliga för Elastic Database-pooler |
> | Action | Microsoft.Sql/servers/elasticPools/metrics/read | Returnera mått för Elastic Database-pooler |
> | Action | Microsoft.Sql/servers/elasticPools/operations/cancel/action | Avbryter en asynkron åtgärd i Azure SQL elastisk pool som inte har slutförts än. |
> | Action | Microsoft.Sql/servers/elasticPools/operations/read | Returnera listan över åtgärder som utförts i den elastiska poolen |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read | Hämtar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write | Skapar eller uppdaterar den diagnostiska inställningen för resursen |
> | Action | Microsoft.Sql/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för Elastic Database-pooler |
> | Action | Microsoft.Sql/servers/elasticPools/read | Hämta information om elastisk pool på en specifik server |
> | Action | Microsoft.Sql/servers/elasticPools/skus/read | Hämtar en samling SKU: er som är tillgängliga för en elastisk pool |
> | Action | Microsoft.Sql/servers/elasticPools/write | Skapa en ny eller ändra egenskaper för en befintlig elastisk pool |
> | Action | Microsoft.Sql/servers/encryptionProtector/read | Returnerar en lista över Server krypterings skydd eller hämtar egenskaperna för det angivna Server krypterings skyddet. |
> | Action | Microsoft.Sql/servers/encryptionProtector/write | Uppdatera egenskaperna för det angivna Server krypterings skyddet. |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/read | Hämta information om den utökade gransknings principen för Server-blob som kon figurer ATS på en viss server |
> | Action | Microsoft.Sql/servers/extendedAuditingSettings/write | Ändra den utökade serverns BLOB-granskning för en viss server |
> | Action | Microsoft.Sql/servers/failoverGroups/delete | Tar bort en befintlig redundans grupp. |
> | Action | Microsoft.Sql/servers/failoverGroups/failover/action | Kör Planerad redundans i en befintlig grupp för redundans. |
> | Action | Microsoft.Sql/servers/failoverGroups/forceFailoverAllowDataLoss/action | Kör framtvingad redundans i en befintlig grupp för växling vid fel. |
> | Action | Microsoft.Sql/servers/failoverGroups/read | Returnerar listan över Redundansrelationer eller hämtar egenskaperna för den angivna gruppen för redundans. |
> | Action | Microsoft.Sql/servers/failoverGroups/write | Skapar en failover-grupp med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna gruppen för redundans. |
> | Action | Microsoft. SQL/Servers/firewallRules/Delete | Tar bort en befintlig server brand Väggs regel. |
> | Action | Microsoft.Sql/servers/firewallRules/read | Returnera listan över Server brand Väggs regler eller hämtar egenskaperna för den angivna Server brand Väggs regeln. |
> | Action | Microsoft. SQL/Servers/firewallRules/Write | Skapar en server brand Väggs regel med de angivna parametrarna, uppdaterar egenskaperna för den angivna regeln eller skriver över alla befintliga regler med nya server brand Väggs regler. |
> | Action | Microsoft. SQL/Servers/import/åtgärd | Skapa en ny databas på servern och distribuera schema och data från ett DacPac-paket |
> | Action | Microsoft.Sql/servers/importExportOperationResults/read | Hämtar pågående import/export-åtgärder |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/delete | Tar bort angiven gränssnitts slut punkts profil |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/read | Returnerar egenskaperna för den angivna gränssnittets slut punkts profil |
> | Action | Microsoft.Sql/servers/interfaceEndpointProfiles/write | Skapar en gränssnitts slut punkts profil med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna gränssnitts slut punkten |
> | Action | Microsoft. SQL/Servers/jobAgents/Delete | Tar bort en Azure SQL DB-jobbiljett |
> | Action | Microsoft.Sql/servers/jobAgents/read | Hämtar en Azure SQL DB-jobbiljett |
> | Action | Microsoft. SQL/Servers/jobAgents/Write | Skapar eller uppdaterar en Azure SQL DB-Job agent |
> | Action | Microsoft.Sql/servers/keys/delete | Tar bort en befintlig server nyckel. |
> | Action | Microsoft.Sql/servers/keys/read | Returnera listan över Server nycklar eller hämta egenskaperna för den angivna Server nyckeln. |
> | Action | Microsoft.Sql/servers/keys/write | Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna Server nyckeln. |
> | Action | Microsoft.Sql/servers/operationResults/read | Hämtar pågående Server åtgärder |
> | Action | Microsoft. SQL/Servers/privateEndpointConnectionProxies/Delete | Tar bort en befintlig anslutning till en privat slut punkt |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/read | Returnerar listan över anslutnings-proxy för privata slut punkter eller hämtar egenskaperna för den angivna anslutningen till den privata slut punkten. |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/validate/action | Verifierar en privat slut punkts anslutning skapa samtal från NRP sida |
> | Action | Microsoft.Sql/servers/privateEndpointConnectionProxies/write | Skapar en anslutning till en privat slutpunkt med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna proxyn för privat slut punkt anslutning. |
> | Action | Microsoft. SQL/Servers/privateEndpointConnections/Delete | Tar bort en befintlig privat slut punkts anslutning |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/read | Returnerar listan över anslutningar för privata slut punkter eller hämtar egenskaperna för den angivna privata slut punkts anslutningen. |
> | Action | Microsoft.Sql/servers/privateEndpointConnections/write | Godkänner eller avvisar en befintlig privat slut punkts anslutning |
> | Action | Microsoft. SQL/Servers/privateEndpointConnectionsApproval/åtgärd | Anger om användaren får godkänna en privat slut punkts anslutning |
> | Action | Microsoft.Sql/servers/privateLinkResources/read | Hämta de privata länk resurserna för motsvarande SQL Server |
> | Action | Microsoft.Sql/servers/providers/Microsoft.Insights/metricDefinitions/read | Returnera typer av mått som är tillgängliga för servrar |
> | Action | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/databases/read | Hämta mått för rekommenderade Elastic Database-pooler för en specifik server |
> | Action | Microsoft.Sql/servers/recommendedElasticPools/read | Hämta rekommendationer för Elastic Database-pooler för att minska kostnaderna eller förbättra prestandan utifrån historisk resursutnyttjande |
> | Action | Microsoft.Sql/servers/recoverableDatabases/read | Den här åtgärden används för haveri beredskap för Live Database för att återställa databasen till den senast kända fungerande säkerhets kopierings punkten. Den returnerar information om den senaste korrekta säkerhets kopieringen, men det doesn\u0027t faktiskt återställer databasen. |
> | Action | Microsoft.Sql/servers/replicationLinks/read | Returnera listan över länkar för replikering eller hämta egenskaperna för de angivna replikeringslänken. |
> | Action | Microsoft.Sql/servers/restorableDroppedDatabases/read | Hämta en lista över databaser som har släppts på en specifik server som fortfarande ligger inom bevarande principen. |
> | Action | Microsoft.Sql/servers/securityAlertPolicies/operationResults/read | Hämta resultat från princip Skriv åtgärd för Server hot identifiering |
> | Action | Microsoft. SQL/Servers/securityAlertPolicies/Read | Hämta en lista över Server hot identifierings principer som kon figurer ATS för en angiven server |
> | Action | Microsoft. SQL/Servers/securityAlertPolicies/Write | Ändra principen för Server hot identifiering för en specifik server |
> | Action | Microsoft.Sql/servers/serviceObjectives/read | Hämta en lista över service nivå mål (även kallade prestanda nivåer) som är tillgängliga på en viss server |
> | Action | Microsoft.Sql/servers/syncAgents/delete | Tar bort en befintlig Sync-agent. |
> | Action | Microsoft. SQL/Servers/syncAgents/generateKey/Action | Generera registrerings nyckel för Sync-agent |
> | Action | Microsoft. SQL/Servers/syncAgents/linkedDatabases/Read | Returnera listan över länkade databaser för Sync-agenten |
> | Action | Microsoft.Sql/servers/syncAgents/read | Returnera listan över Sync-agenter eller hämta egenskaperna för den angivna Sync-agenten. |
> | Action | Microsoft.Sql/servers/syncAgents/write | Skapar en Sync-agent med de angivna parametrarna eller uppdaterar egenskaperna för den angivna Sync-agenten. |
> | Action | Microsoft.Sql/servers/tdeCertificates/action | Skapa/uppdatera TDE-certifikat |
> | Action | Microsoft.Sql/servers/usages/read | Returnera serverns DTU-kvot och aktuella DTU-consuption av alla databaser på servern |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/delete | Tar bort en befintlig Virtual Network regel |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/read | Returnera listan över virtuella nätverks regler eller hämtar egenskaperna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft.Sql/servers/virtualNetworkRules/write | Skapar en virtuell nätverks regel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverks regeln. |
> | Action | Microsoft. SQL/Servers/vulnerabilityAssessments/Delete | Ta bort sårbarhets utvärderingen för en specifik server |
> | Action | Microsoft. SQL/Servers/vulnerabilityAssessments/Read | Hämta principerna för sårbarhets bedömning på en specifik server |
> | Action | Microsoft. SQL/Servers/vulnerabilityAssessments/Write | Ändra sårbarhets bedömning för en specifik server |
> | Action | Microsoft. SQL/Servers/Write | Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern. |
> | Action | Microsoft.Sql/unregister/action | Avregistrerar prenumerationen för Microsoft SQL Database Resource Provider och gör det möjligt att skapa Microsoft SQL-databaser. |
> | Action | Microsoft. SQL/virtualClusters/Delete | Tar bort ett befintligt virtuellt kluster. |
> | Action | Microsoft. SQL/virtualClusters/Read | Returnera listan över virtuella kluster eller hämtar egenskaperna för det angivna virtuella klustret. |
> | Action | Microsoft.Sql/virtualClusters/write | Uppdaterar taggar för virtuella kluster. |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Storage/checknameavailability/Read | Kontrollerar att kontonamnet är giltigt och används. |
> | Action | Microsoft. Storage/locations/checknameavailability/Read | Kontrollerar att kontonamnet är giltigt och används. |
> | Action | Microsoft.Storage/locations/deleteVirtualNetworkOrSubnets/action | Aviserar Microsoft.Storage om att det virtuella nätverket eller undernätet tas bort |
> | Action | Microsoft. Storage/locations/usages/Read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |
> | Action | Microsoft. Storage/Operations/Read | Avsöker status för en asynkron åtgärd. |
> | Action | Microsoft. Storage/register/åtgärd | Registrerar prenumerationen för lagringsresursprovidern och gör det möjligt att skapa lagringskonton. |
> | Action | Microsoft.Storage/skus/read | Listar SKU:erna som stöds av Microsoft.Storage. |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action | Returnerar resultatet av att lägga till blobinnehåll |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en blob |
> | DataAction | Microsoft. Storage/storageAccounts/blobServices/containers/blobbar/deleteAutomaticSnapshot/åtgärd | Returnerar resultatet av borttagningen av en automatisk ögonblicksbild |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action | Returnerar listan över blobar i ett konto med matchande taggfilter |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en blob eller bloblista |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read | Returnerar resultatet av läsning av blobtaggar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write | Returnerar resultatet av skrivning av blobtaggar |
> | DataAction | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en blob |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/clearLegalHold/action | Ta bort bevarande av juridiska skäl för blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en container |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/delete | Ta bort oföränderlighetsprincipen för blobcontainern |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action | Utöka oföränderlighetsprincip för blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action | Lås oföränderlighetsprincip för blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/read | Hämta oföränderlighetsprincip för blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/immutabilityPolicies/write | Placera oföränderlighetsprincip för blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/lease/action | Returnerar resultatet av att leasa blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar en container |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar lista över containrar |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/setLegalHold/action | Ställ in bevarande av juridiska skäl för blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av korrigera blobcontainer |
> | Action | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att skicka BLOB-behållare |
> | Action | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användardelegeringsnyckel för Blob Service |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read |  |
> | Action | Microsoft.Storage/storageAccounts/blobServices/read | Returnerar egenskaper för Blob Service eller statistik |
> | Action | Microsoft.Storage/storageAccounts/blobServices/write | Returnerar resultatet av att placera Blob Service-egenskaper |
> | Action | Microsoft.Storage/storageAccounts/delete | Tar bort ett befintligt lagringskonto. |
> | Action | Microsoft. Storage/storageAccounts/redundans/åtgärd | Kunden kan styra redundansväxlingen vid tillgänglighetsproblem |
> | Action | Microsoft.Storage/storageAccounts/fileServices/read | Hämta egenskaper för fil tjänst |
> | Action | Microsoft.Storage/storageAccounts/listAccountSas/action | Returnerar SAS-token för konto för det angivna lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/listServiceSas/action | Returnerar SAS-token för tjänst för det angivna lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/delete | Ta bort hanteringsprinciper för lagringskonto |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/read | Hämta principer för lagringshanteringskonto |
> | Action | Microsoft.Storage/storageAccounts/managementPolicies/write | Placera hanteringsprinciper för lagringskonto |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Delete | Ta bort proxy för privat slut punkt anslutning |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnectionProxies/Write | Lägg till proxyservrar för privat slut punkt anslutning |
> | Action | Microsoft. Storage/storageAccounts/privateEndpointConnections/Delete | Ta bort privat slut punkts anslutning |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/read | Hämta privat slut punkts anslutning |
> | Action | Microsoft.Storage/storageAccounts/privateEndpointConnections/write | Lägg till privat slut punkts anslutning |
> | Action | Microsoft. Storage/storageAccounts/PrivateEndpointConnectionsApproval/åtgärd | Godkänn anslutningar för privata slut punkter |
> | Action | Microsoft.Storage/storageAccounts/privateLinkResources/read | Hämta StorageAccount-groupids |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Returnerar resultatet av att ta bort en kö |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Returnerar resultatet av att lägga till ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Returnerar resultatet av att ta bort ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Returnerar resultatet av att bearbeta ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Returnerar ett meddelande |
> | DataAction | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Returnerar resultatet av att skriva ett meddelande |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller kölista. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/queues/write | Returnerar resultatet av att skriva en kö |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Hämta Kötjänst egenskaper |
> | Action | Microsoft.Storage/storageAccounts/queueServices/read | Returnerar egenskaper för kötjänst eller statistik. |
> | Action | Microsoft.Storage/storageAccounts/queueServices/write | Returnerar resultatet av att ställa in egenskaper för kötjänst |
> | Action | Microsoft.Storage/storageAccounts/read | Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/restoreBlobRanges/action | Återställ BLOB-intervall till tillstånd för den angivna tiden |
> | Action | Microsoft.Storage/storageAccounts/revokeUserDelegationKeys/action | Återkallar alla användardelegeringsnycklar för det angivna lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/services/diagnosticSettings/write | Skapa/uppdatera diagnostikinställningar för lagringskontot. |
> | Action | Microsoft.Storage/storageAccounts/tableServices/read | Hämta Table service egenskaper |
> | Action | Microsoft.Storage/storageAccounts/write | Skapar ett lagringskonto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna, eller lägger till anpassad domän för det angivna lagringskontot. |
> | Action | Microsoft.Storage/usages/read | Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen |

## <a name="microsoftstoragesync"></a>Microsoft. storagesync

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. storagesync/locations/checkNameAvailability/Action | Kontrollerar att namnet på lagrings synkroniseringstjänsten är giltigt och inte används. |
> | Action | Microsoft. storagesync/locations/arbetsflöden/Operations/Read | Hämtar status för en asynkron åtgärd |
> | Action | Microsoft. storagesync/Operations/Read | Hämtar en lista över de åtgärder som stöds |
> | Action | Microsoft. storagesync/register/åtgärd | Registrerar prenumerationen för Storage Sync-providern |
> | Action | Microsoft. storagesync/storageSyncServices/Delete | Ta bort alla tjänster för synkronisering av lagring |
> | Action | microsoft.storagesync/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Storage Sync-tjänster |
> | Action | microsoft.storagesync/storageSyncServices/read | Läs alla tjänster för synkronisering av lagring |
> | Action | Microsoft. storagesync/storageSyncServices/registeredServers/Delete | Ta bort alla registrerade servrar |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för registrerad Server |
> | Action | microsoft.storagesync/storageSyncServices/registeredServers/read | Läs alla registrerade servrar |
> | Action | Microsoft. storagesync/storageSyncServices/registeredServers/Write | Skapa eller uppdatera alla registrerade servrar |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/delete | Ta bort alla moln slut punkter |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read | Hämtar status för en asynkron säkerhets kopiering/återställning |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action | Anropa den här åtgärden efter säkerhets kopiering |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action | Anropa den här åtgärden efter återställning |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action | Anropa den här åtgärden innan du säkerhetskopierar |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action | Anropa den här åtgärden innan du återställer |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/read | Läs eventuella slut punkter i molnet |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action | Återställ pulsslag |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/cloudEndpoints/write | Skapa eller uppdatera eventuella moln slut punkter |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/Delete | Ta bort eventuella Sync-grupper |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Sync-grupper |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/read | Läs alla Sync-grupper |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Delete | Ta bort alla Server slut punkter |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för Server slut punkter |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Read | Läs alla Server slut punkter |
> | Action | microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints/recallAction/action | Anropa den här åtgärden för att återställa filer till en server |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints/Write | Skapa eller uppdatera alla Server slut punkter |
> | Action | Microsoft. storagesync/storageSyncServices/syncGroups/Write | Skapa eller uppdatera alla Sync-grupper |
> | Action | Microsoft. storagesync/storageSyncServices/arbets flöden/operationresults/Read | Hämtar status för en asynkron åtgärd |
> | Action | Microsoft. storagesync/storageSyncServices/arbets flöden/åtgärder/Läs | Hämtar status för en asynkron åtgärd |
> | Action | Microsoft. storagesync/storageSyncServices/arbets flöden/läsa | Läs arbets flöden |
> | Action | Microsoft. storagesync/storageSyncServices/Write | Skapa eller uppdatera lagrings tjänster för synkronisering |
> | Action | Microsoft. storagesync/avregistrera/åtgärd | Avregistrerar prenumerationen för Storage Sync-providern |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/delete | Tar bort Access Control poster |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/read | Visar eller hämtar Access Control poster |
> | Action | Microsoft.StorSimple/managers/accessControlRecords/write | Skapa eller uppdatera Access Control poster |
> | Action | Microsoft.StorSimple/managers/alerts/read | Visar eller hämtar aviseringarna |
> | Action | Microsoft. StorSimple/chefer/säkerhets kopieringar/läsa | Listar eller hämtar säkerhets kopian |
> | Action | Microsoft. StorSimple/chefer/bandwidthSettings/Delete | Tar bort en befintlig bandbredds inställning (endast 8000-serien) |
> | Action | Microsoft.StorSimple/managers/bandwidthSettings/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/bandwidthSettings/Read | Visa en lista över bandbredds inställningar (endast 8000-serien) |
> | Action | Microsoft. StorSimple/chefer/bandwidthSettings/Write | Skapar en ny eller uppdaterar bandbredds inställningar (endast 8000-serien) |
> | Action | Microsoft.StorSimple/managers/certificates/write | Skapa eller uppdatera certifikaten |
> | Action | Microsoft.StorSimple/Managers/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Action | Microsoft.StorSimple/managers/clearAlerts/action | Rensa alla aviseringar som är associerade med enhets hanteraren. |
> | Action | Microsoft.StorSimple/managers/cloudApplianceConfigurations/read | Visa en lista över konfigurationer som stöds av moln installationen |
> | Action | Microsoft. StorSimple/chefer/configureDevice/åtgärd | Konfigurerar en enhet |
> | Action | Microsoft. StorSimple/chefer/Delete | Tar bort enhets hanterare |
> | Action | Microsoft. StorSimple/chefer/Delete | Åtgärden ta bort valv tar bort den angivna Azure-resursen av typen valv |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/read | Visar eller hämtar aviserings inställningarna |
> | Action | Microsoft.StorSimple/managers/devices/alertSettings/write | Skapa eller uppdatera aviserings inställningarna |
> | Action | Microsoft. StorSimple/chefer/Devices/authorizeForServiceEncryptionKeyRollover/Action | Auktorisera för förnyelse av tjänst krypterings nyckel för enheter |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/säkerhets kopiering/åtgärd | Gör en manuell säkerhets kopiering för att skapa en säkerhets kopiering på begäran av alla volymer som skyddas av principen. |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/Delete | Tar bort ett befintligt säkerhets kopierings principer (endast 8000-serien) |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/operationResults/Read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/Read | Lista över säkerhets kopierings principer (endast 8000-serien) |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/delete | Tar bort ett befintligt schema |
> | Action | Microsoft.StorSimple/managers/devices/backupPolicies/schedules/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/schema/Read | Lista scheman |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/schema/skrivning | Skapar ett nytt schema eller uppdateringar |
> | Action | Microsoft. StorSimple/chefer/Devices/backupPolicies/Write | Skapar en ny eller uppdaterar säkerhets kopierings principer (endast 8000-serien) |
> | Action | Microsoft. StorSimple/chefer/Devices/backups/Delete | Tar bort säkerhets kopian |
> | Action | Microsoft. StorSimple/chefer/Devices/backups/Elements/Clone/åtgärd | Klona en resurs eller volym med hjälp av ett säkerhets kopierings element. |
> | Action | Microsoft.StorSimple/managers/devices/backups/elements/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/backups/operationResults/Read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/backups/Read | Listar eller hämtar säkerhets kopian |
> | Action | Microsoft. StorSimple/hanterare/enheter/säkerhets kopiering/återställning/åtgärd | Återställ alla volymer från en säkerhets kopia. |
> | Action | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Delete | Tar bort schema grupper för säkerhets kopiering |
> | Action | Microsoft.StorSimple/managers/devices/backupScheduleGroups/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Read | Listar eller hämtar schema grupper för säkerhets kopiering |
> | Action | Microsoft. StorSimple/chefer/Devices/backupScheduleGroups/Write | Skapa eller uppdatera schema grupper för säkerhets kopiering |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/delete | Tar bort CHAP-inställningarna |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/read | Visar eller hämtar CHAP-inställningar |
> | Action | Microsoft.StorSimple/managers/devices/chapSettings/write | Skapa eller uppdatera CHAP-inställningarna |
> | Action | Microsoft. StorSimple/hanterare/enheter/inaktivera/åtgärd | Inaktiverar en enhet. |
> | Action | Microsoft. StorSimple/chefer/Devices/Delete | Tar bort enheterna |
> | Action | Microsoft. StorSimple/chefer/Devices/disks/Read | Listar eller hämtar diskarna |
> | Action | Microsoft. StorSimple/chefer/Devices/hämtning/åtgärd | Download uppdateringar för en enhet. |
> | Action | Microsoft. StorSimple/chefer/Devices/redundans/åtgärd | Enhetens redundans. |
> | Action | Microsoft. StorSimple/chefer/Devices/redundans/operationResults/Read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/failoverTargets/Read | Visar eller hämtar enheternas Redundansrelationer |
> | Action | Microsoft. StorSimple/chefer/Devices/fileservers/säkerhets kopiering/åtgärd | Gör en säkerhets kopia av en fil server. |
> | Action | Microsoft. StorSimple/chefer/Devices/fileservers/Delete | Tar bort fil servrarna |
> | Action | Microsoft. StorSimple/chefer/Devices/fileservers/Metrics/Read | Visar eller hämtar mått |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/fileservers/Read | Listar eller hämtar fil servrar |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/delete | Tar bort resurserna |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metrics/read | Visar eller hämtar mått |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/devices/fileservers/shares/read | Listar eller hämtar resurserna |
> | Action | Microsoft. StorSimple/chefer/Devices/fileservers/Shares/Write | Skapa eller uppdatera resurserna |
> | Action | Microsoft. StorSimple/chefer/Devices/fileservers/Write | Skapa eller uppdatera fil servrarna |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/changeControllerPowerState/action | Ändra styrenhetens energi tillstånd för maskin varu komponent grupper |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/devices/hardwareComponentGroups/read | Visa en lista över maskin varu komponent grupper |
> | Action | Microsoft. StorSimple/chefer/Devices/install/åtgärd | Installera uppdateringar på en enhet. |
> | Action | Microsoft. StorSimple/chefer/Devices/installUpdates/Action | Installerar uppdateringar på enheterna (endast 8000-serien). |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/säkerhets kopiering/åtgärd | Gör en säkerhets kopia av en iSCSI-server. |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/Delete | Tar bort iSCSI-servrarna |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Delete | Tar bort diskarna |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Metrics/Read | Visar eller hämtar mått |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/metricsDefinitions/Read | Visar eller hämtar måtten definitioner |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/disks/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Read | Listar eller hämtar diskarna |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/disks/Write | Skapa eller uppdatera diskarna |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/Metrics/Read | Visar eller hämtar mått |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Action | Microsoft.StorSimple/managers/devices/iscsiservers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/Read | Listar eller hämtar iSCSI-servrar |
> | Action | Microsoft. StorSimple/chefer/Devices/iscsiservers/Write | Skapa eller uppdatera iSCSI-servrarna |
> | Action | Microsoft. StorSimple/hanterare/enheter/jobb/Avbryt/åtgärd | Avbryta ett pågående jobb |
> | Action | Microsoft. StorSimple/chefer/Devices/Jobs/operationResults/Read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/Jobs/Read | Visar eller hämtar jobben |
> | Action | Microsoft. StorSimple/chefer/Devices/listFailoverSets/Action | Visa en lista över redundansväxlingen för en befintlig enhet (endast 8000-serien). |
> | Action | Microsoft. StorSimple/chefer/Devices/listFailoverTargets/Action | Visa en lista över redundansväxlingen för enheterna (endast 8000-serien). |
> | Action | Microsoft. StorSimple/chefer/Devices/Metrics/Read | Visar eller hämtar mått |
> | Action | Microsoft. StorSimple/chefer/Devices/metricsDefinitions/Read | Visar eller hämtar måtten definitioner |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/confirmMigration/åtgärd | Bekräftar en lyckad migrering och genomför den. |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/confirmMigrationStatus/Read | Ange status för bekräfta migrering |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/åtgärd | Hämta bekräftelse status för migreringen. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action | Hämtar status för jobbet för migrerings uppskattning. |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/fetchMigrationStatus/åtgärd | Hämta status för migreringen. |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/import/action | Importera käll konfiguration för migrering |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/migrationEstimate/read | Lista uppskattning av migrering |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/migrationStatus/Read | Lista status för migrering |
> | Action | Microsoft.StorSimple/managers/devices/migrationSourceConfigurations/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/startMigration/åtgärd | Starta migrering med käll konfiguration |
> | Action | Microsoft. StorSimple/chefer/Devices/migrationSourceConfigurations/startMigrationEstimate/åtgärd | Starta ett jobb för att uppskatta varaktigheten för migreringsprocessen. |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/read | Visar eller hämtar nätverks inställningar |
> | Action | Microsoft.StorSimple/managers/devices/networkSettings/write | Skapar en ny eller uppdaterar nätverks inställningar |
> | Action | Microsoft.StorSimple/managers/devices/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/publicEncryptionKey/Action | Visa en lista över den offentliga krypterings nyckeln för enhets hanteraren |
> | Action | Microsoft. StorSimple/chefer/Devices/publishSupportPackage/Action | Publicera support paketet för en befintlig enhet. Ett StorSimple-stödpaket är en lättanvänd mekanism som samlar in alla relevanta loggar för att hjälpa Microsoft Support med fel sökning av problem med StorSimple-enheter. |
> | Action | Microsoft. StorSimple/chefer/Devices/Read | Visar eller hämtar enheterna |
> | Action | Microsoft. StorSimple/chefer/Devices/scanForUpdates/Action | Söker efter uppdateringar på en enhet. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/securitySettings/Read | Lista säkerhets inställningarna |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/syncRemoteManagementCertificate/action | Synkronisera fjärrhanterings certifikatet för en enhet. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/update/action | Uppdatera säkerhets inställningarna. |
> | Action | Microsoft.StorSimple/managers/devices/securitySettings/write | Skapar en ny eller uppdaterar säkerhets inställningar |
> | Action | Microsoft. StorSimple/chefer/Devices/sendTestAlertEmail/Action | Skicka test aviserings meddelande till konfigurerade e-postmottagare. |
> | Action | Microsoft. StorSimple/chefer/Devices/Shares/Read | Listar eller hämtar resurserna |
> | Action | Microsoft.StorSimple/managers/devices/timeSettings/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/timeSettings/Read | Visar eller hämtar tids inställningarna |
> | Action | Microsoft. StorSimple/chefer/Devices/timeSettings/Write | Skapar ett nytt eller uppdaterings tids inställningar |
> | Action | Microsoft.StorSimple/managers/devices/updates/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/updateSummary/Read | Visar eller hämtar uppdaterings sammanfattningen |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Delete | Tar bort en befintlig volym behållare (endast 8000-serien) |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Metrics/Read | Lista måtten |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/metricsDefinitions/Read | Visa en lista över mått definitioner |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Read | Lista volym containrar (endast 8000-serien) |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Delete | Tar bort en befintlig volym |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Metrics/Read | Lista måtten |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/metricsDefinitions/read | Visa en lista över mått definitioner |
> | Action | Microsoft.StorSimple/managers/devices/volumeContainers/volumes/operationResults/read | Lista åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Read | Lista volymer |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Volumes/Write | Skapar en ny eller uppdateringar av volymer |
> | Action | Microsoft. StorSimple/chefer/Devices/volumeContainers/Write | Skapar en ny eller uppdaterar volym behållare (endast 8000-serien) |
> | Action | Microsoft. StorSimple/chefer/Devices/Volumes/Read | Lista volymer |
> | Action | Microsoft. StorSimple/chefer/Devices/Write | Skapa eller uppdatera enheterna |
> | Action | Microsoft.StorSimple/managers/encryptionSettings/read | Visar eller hämtar krypterings inställningarna |
> | Action | Microsoft.StorSimple/managers/extendedInformation/delete | Tar bort information om det utökade valvet |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/delete | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Action | Microsoft.StorSimple/managers/extendedInformation/read | Visar eller hämtar information om utökade valv |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/read | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Action | Microsoft.StorSimple/managers/extendedInformation/write | Skapa eller uppdatera information om det utökade valvet |
> | Action | Microsoft.StorSimple/Managers/extendedInformation/write | Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv? |
> | Action | Microsoft. StorSimple/chefer/features/Read | Visa en lista över funktionerna |
> | Action | Microsoft.StorSimple/managers/fileservers/read | Listar eller hämtar fil servrar |
> | Action | Microsoft.StorSimple/managers/getEncryptionKey/action | Hämta krypterings nyckel för enhets hanteraren. |
> | Action | Microsoft.StorSimple/managers/iscsiservers/read | Listar eller hämtar iSCSI-servrar |
> | Action | Microsoft. StorSimple/chefer/Jobs/Read | Visar eller hämtar jobben |
> | Action | Microsoft.StorSimple/managers/listActivationKey/action | Hämtar aktiverings nyckeln för StorSimple-Enhetshanteraren. |
> | Action | Microsoft. StorSimple/chefer/listPublicEncryptionKey/åtgärd | Visa en lista över offentliga krypterings nycklar för en StorSimple Enhetshanteraren. |
> | Action | Microsoft. StorSimple/chefer/Metrics/Read | Visar eller hämtar mått |
> | Action | Microsoft.StorSimple/managers/metricsDefinitions/read | Visar eller hämtar måtten definitioner |
> | Action | Microsoft. StorSimple/chefer/migrateClassicToResourceManager/åtgärd | Migrera klassiskt till Resource Manager för hanterare |
> | Action | Microsoft.StorSimple/managers/migrationSourceConfigurations/read | Visa en lista över konfigurationer för migrering av källa (endast 8000-serien) |
> | Action | Microsoft.StorSimple/managers/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft. StorSimple/chefer/provisionCloudAppliance/åtgärd | Skapa en ny moln installation. |
> | Action | Microsoft.StorSimple/managers/read | Visar eller hämtar enhets ansvariga |
> | Action | Microsoft.StorSimple/Managers/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Action | Microsoft.StorSimple/managers/regenerateActivationKey/action | Återskapa aktiverings nyckeln för en befintlig StorSimple-Enhetshanteraren. |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/delete | Tar bort autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/read | Visar eller hämtar autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft.StorSimple/managers/storageAccountCredentials/write | Skapa eller uppdatera autentiseringsuppgifterna för lagrings kontot |
> | Action | Microsoft.StorSimple/managers/storageDomains/delete | Tar bort lagrings domänerna |
> | Action | Microsoft.StorSimple/managers/storageDomains/operationResults/read | Visar eller hämtar åtgärds resultatet |
> | Action | Microsoft.StorSimple/managers/storageDomains/read | Listar eller hämtar lagrings domänerna |
> | Action | Microsoft.StorSimple/managers/storageDomains/write | Skapa eller uppdatera lagrings domänerna |
> | Action | Microsoft. StorSimple/chefer/Write | Skapa eller uppdatera enhets chefer |
> | Action | Microsoft.StorSimple/Managers/write | Med skapa valv så skapas en Azure-resurs av typen valv |
> | Action | Microsoft.StorSimple/operations/read | Visar eller hämtar åtgärder |
> | Action | Microsoft.StorSimple/register/action | Registrera providern Microsoft. StorSimple |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. StreamAnalytics/locations/kvoter/read | Läs Stream Analytics prenumerations kvot |
> | Action | Microsoft. StreamAnalytics/Operations/Read | Läs Stream Analytics åtgärder |
> | Action | Microsoft. StreamAnalytics/register/åtgärd | Registrera prenumeration med Stream Analytics Resource Provider |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Delete | Ta bort Stream Analytics jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/Delete | Ta bort Stream Analytics jobb funktion |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobb funktion |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/Read | Läs Stream Analytics jobb funktion |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/RetrieveDefaultDefinition/Action | Hämta standard definition för en Stream Analytics Job-funktion |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/test/Action | Test Stream Analytics jobb funktion |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Functions/Write | Skriv Stream Analytics jobb funktion |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/Delete | Ta bort Stream Analytics jobb inmatade |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobb-ingångar |
> | Action | Microsoft. StreamAnalytics/streamingjobs/indata/Read | Läs Stream Analytics jobb-ingångar |
> | Action | Microsoft. StreamAnalytics/streamingjobs/indata/sampel/åtgärd | Exempel på inmatade Stream Analytics jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/test/Action | Testa Stream Analytics jobb-ingångar |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Inputs/Write | Skriv Stream Analytics jobb inmatat |
> | Action | Microsoft. StreamAnalytics/streamingjobs/metricdefinitions/Read | Läs mått definitioner |
> | Action | Microsoft.StreamAnalytics/streamingjobs/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/outputs/Delete | Ta bort Stream Analytics jobbets utdata |
> | Action | Microsoft.StreamAnalytics/streamingjobs/outputs/operationresults/Read | Läs åtgärds resultat för Stream Analytics jobbets utdata |
> | Action | Microsoft. StreamAnalytics/streamingjobs/utdata/Read | Läs Stream Analytics jobbets utdata |
> | Action | Microsoft. StreamAnalytics/streamingjobs/outputs/test/åtgärd | Test Stream Analytics jobbets utdata |
> | Action | Microsoft. StreamAnalytics/streamingjobs/utdata/Write | Skriv Stream Analytics jobbets utdata |
> | Action | Microsoft. StreamAnalytics/streamingjobs/providers/Microsoft. Insights/diagnosticSettings/Read | Läsa diagnostisk inställning. |
> | Action | Microsoft. StreamAnalytics/streamingjobs/providers/Microsoft. Insights/diagnosticSettings/Write | Skriv diagnos inställning. |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/logDefinitions/read | Hämtar de tillgängliga loggarna för streamingjobs |
> | Action | Microsoft.StreamAnalytics/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read | Hämtar tillgängliga mått för streamingjobs |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Read | Läs Stream Analyticss jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/start/Action | Starta Stream Analytics jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/stopp/åtgärd | Stoppa Stream Analytics jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Transforms/Delete | Ta bort transformering av Stream Analytics jobb |
> | Action | Microsoft. StreamAnalytics/streamingjobs/transformationer/Read | Läs Stream Analytics jobb omvandling |
> | Action | Microsoft. StreamAnalytics/streamingjobs/Transforms/Write | Skriv Stream Analytics jobb omvandling |
> | Action | Microsoft.StreamAnalytics/streamingjobs/Write | Skriv Stream Analytics jobb |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft. Subscription/Avbryt/åtgärd | Avbryter prenumerationen |
> | Action | Microsoft. prenumeration/CreateSubscription/åtgärd | Skapa en Azure-prenumeration |
> | Action | Microsoft. Subscription/registrera/åtgärd | Registrerar prenumeration med Microsoft. Subscription-resurs leverantör |
> | Action | Microsoft. Subscription/Byt namn/åtgärd | Byter namn på prenumerationen |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/read | Hämta en Azure-prenumerations definition i en hanterings grupp. |
> | Action | Microsoft.Subscription/SubscriptionDefinitions/write | Skapa en Azure-prenumerations definition |

## <a name="microsoftsupport"></a>Microsoft.Support

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.Support/register/action | Registrerar till supportresursprovidern |
> | Action | Microsoft.Support/supportTickets/read | Hämtar information om supportärendet (inklusive status, allvarlighetsgrad, kontaktinformation och kommunikation) eller hämtar listan över supportärenden för alla prenumerationer. |
> | Action | Microsoft.Support/supportTickets/write | Skapar eller uppdaterar en supportbegäran. Du kan skapa en supportbegäran om tekniska problem, fakturering, kvoter eller prenumerationshantering. Du kan uppdatera allvarlighetsgraden, kontaktinformation och kommunikation för befintliga supportbegäranden. |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/delete | Tar bort åtkomst principen. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/read | Hämta egenskaperna för en åtkomst princip. |
> | Action | Microsoft.TimeSeriesInsights/environments/accesspolicies/write | Skapar en ny åtkomst princip för en miljö eller uppdaterar en befintlig åtkomst princip. |
> | Action | Microsoft.TimeSeriesInsights/environments/delete | Tar bort miljön. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/delete | Tar bort händelse källan. |
> | Action | Microsoft.TimeSeriesInsights/environments/eventsources/read | Hämta egenskaperna för en händelse källa. |
> | Action | Microsoft. TimeSeriesInsights/miljöer/eventsources/Write | Skapar en ny händelse källa för en miljö eller uppdaterar en befintlig händelse källa. |
> | Action | Microsoft.TimeSeriesInsights/environments/read | Hämta egenskaperna för en miljö. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/delete | Tar bort referens data uppsättningen. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/read | Hämta egenskaperna för en referens data uppsättning. |
> | Action | Microsoft.TimeSeriesInsights/environments/referencedatasets/write | Skapar en ny referens data uppsättning för en miljö eller uppdaterar en befintlig referens data uppsättning. |
> | Action | Microsoft.TimeSeriesInsights/environments/status/read | Hämta status för miljön och tillståndet för dess associerade åtgärder, t. ex. inkommande. |
> | Action | Microsoft.TimeSeriesInsights/environments/write | Skapar en ny miljö eller uppdaterar en befintlig miljö. |
> | Action | Microsoft.TimeSeriesInsights/register/action | Registrerar prenumerationen för Time Series Insights Resource Provider och gör det möjligt att skapa Time Series Insights miljöer. |

## <a name="microsoftvisualstudio"></a>Microsoft.VisualStudio

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.VisualStudio/Account/Delete | Ta bort konto |
> | Action | Microsoft. VisualStudio/konto/tillägg/läsa | Läs konto/tillägg |
> | Action | Microsoft. VisualStudio/konto/projekt/läsa | Läs konto/projekt |
> | Action | Microsoft.VisualStudio/Account/Project/Write | Ange konto/projekt |
> | Action | Microsoft.VisualStudio/Account/Read | Läs konto |
> | Action | Microsoft.VisualStudio/Account/Write | Ange konto |
> | Action | Microsoft.VisualStudio/Extension/Delete | Ta bort tillägg |
> | Action | Microsoft. VisualStudio/tillägg/läsa | Läs tillägg |
> | Action | Microsoft.VisualStudio/Extension/Write | Ange tillägg |
> | Action | Microsoft.VisualStudio/Project/Delete | Ta bort projekt |
> | Action | Microsoft.VisualStudio/Project/Read | Läs projekt |
> | Action | Microsoft.VisualStudio/Project/Write | Ange projekt |
> | Action | Microsoft.VisualStudio/Register/Action | Registrera Azure-prenumeration med Microsoft. VisualStudio-providern |

## <a name="microsoftweb"></a>microsoft.web

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | microsoft.web/apimanagementaccounts/apiacls/read | Hämta API Management-konton Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/delete | Ta bort API Management-konton API: er Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/read | Hämta API Management-konton API: er Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/apiacls/write | Uppdatera API Management-konton API: er Apiacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connectionacls/read | Hämta API Management-konton API: er Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/confirmconsentcode/action | Bekräfta API-anslutningar för medgivande kods API Management-konton. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/delete | Ta bort API Management-konton API-anslutningar Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/read | Hämta API Management-konton API-anslutningar Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/connectionacls/write | Uppdatera API Management-konton API-anslutningar Connectionacls. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/delete | Ta bort API Management-konton API-anslutningar. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/getconsentlinks/action | Hämta medgivande Länkar för API Management-API-anslutningar. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listconnectionkeys/action | Visa lista över anslutnings nycklar API Management-konton API-anslutningar. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/listsecrets/action | Lista hemligheter API Management konton API-anslutningar. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/read | Hämta API Management-konton API-anslutningar. |
> | Action | microsoft.web/apimanagementaccounts/apis/connections/write | Uppdatera API Management-konton API-anslutningar. |
> | Action | microsoft.web/apimanagementaccounts/apis/delete | Ta bort API: er för API Management-konton. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/delete | Ta bort API Management-konton lokaliserade definitioner. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/read | Hämta API Management-konton lokaliserade definitioner. |
> | Action | microsoft.web/apimanagementaccounts/apis/localizeddefinitions/write | Uppdatera API Management-konton lokaliserade definitioner. |
> | Action | microsoft.web/apimanagementaccounts/apis/read | Hämta API: er för API Management-konton. |
> | Action | microsoft.web/apimanagementaccounts/apis/write | Uppdatera API: er för API Management-konton. |
> | Action | microsoft.web/apimanagementaccounts/connectionacls/read | Hämta API Management-konton Connectionacls. |
> | Action | Microsoft. Web/availablestacks/Read | Hämta tillgängliga stackar. |
> | Action | Microsoft.Web/certificates/Delete | Ta bort ett befintligt certifikat. |
> | Action | Microsoft. Web/certificates/Read | Hämta listan över certifikat. |
> | Action | Microsoft. Web/certificates/Write | Lägg till ett nytt certifikat eller uppdatera ett befintligt. |
> | Action | Microsoft. Web/checknameavailability/Read | Kontrol lera om resurs namnet är tillgängligt. |
> | Action | microsoft.web/classicmobileservices/read | Hämta klassiska Mobile Services. |
> | Action | Microsoft.Web/connectionGateways/Delete | Tar bort en gateway för anslutning. |
> | Action | Microsoft. Web/connectionGateways/JOIN/åtgärd | Ansluter till en gateway för anslutning. |
> | Action | Microsoft.Web/connectionGateways/ListStatus/Action | Visar status för en anslutnings-Gateway. |
> | Action | Microsoft. webb/connectionGateways/flytta/åtgärd | Flyttar en gateway för anslutning. |
> | Action | Microsoft. Web/connectionGateways/Read | Hämta listan över anslutnings-gatewayer. |
> | Action | Microsoft. Web/connectionGateways/Write | Skapar eller uppdaterar en anslutnings-Gateway. |
> | Action | microsoft.web/connections/confirmconsentcode/action | Bekräfta medgivande kod för anslutningar. |
> | Action | Microsoft.Web/connections/Delete | Tar bort en anslutning. |
> | Action | Microsoft. Web/Connections/Join/Action | Ansluter till en anslutning. |
> | Action | microsoft.web/connections/listconsentlinks/action | Visa en lista med medgivande Länkar för anslutningar. |
> | Action | Microsoft. Web/Connections/Move/åtgärd | Flyttar en anslutning. |
> | Action | Microsoft. Web/Connections/Read | Hämta listan över anslutningar. |
> | Action | Microsoft. Web/Connections/Write | Skapar eller uppdaterar en anslutning. |
> | Action | Microsoft.Web/customApis/Delete | Tar bort ett anpassat API. |
> | Action | Microsoft.Web/customApis/extractApiDefinitionFromWsdl/Action | Extraherar API-definition från ett WSDL. |
> | Action | Microsoft. Web/customApis/JOIN/åtgärd | Ansluter till en anpassad API. |
> | Action | Microsoft. Web/customApis/listWsdlInterfaces/åtgärd | Visar en lista över WSDL-gränssnitt för ett anpassat API. |
> | Action | Microsoft. webb/customApis/flytta/åtgärd | Flyttar ett anpassat API. |
> | Action | Microsoft. Web/customApis/Read | Hämta listan med anpassade API: er. |
> | Action | Microsoft. Web/customApis/Write | Skapar eller uppdaterar ett anpassat API. |
> | Action | Microsoft.Web/deletedSites/Read | Hämta egenskaperna för en borttagen webbapp |
> | Action | microsoft.web/deploymentlocations/read | Hämta distributions platser. |
> | Action | Microsoft. Web/Sverige/Read | Hämta listan över geo-regioner. |
> | Action | microsoft.web/hostingenvironments/capacities/read | Få värd miljöer. |
> | Action | Microsoft.Web/hostingEnvironments/Delete | Ta bort ett App Service-miljön |
> | Action | Microsoft. Web/hostingenvironments/Detections/Read | Hämta identifierare för värd miljöer. |
> | Action | Microsoft. Web/hostingenvironments/Diagnostics/Read | Hämta diagnostik för värd miljöer. |
> | Action | Microsoft. Web/hostingenvironments/inboundnetworkdependenciesendpoints/Read | Hämta nätverks slut punkter för alla inkommande beroenden. |
> | Action | Microsoft. Web/hostingEnvironments/JOIN/åtgärd | Ansluter till en App Service-miljön |
> | Action | microsoft.web/hostingenvironments/metricdefinitions/read | Hämta mått definitioner för värd miljöer. |
> | Action | Microsoft. Web/hostingenvironments/multirolepools/metricdefinitions/Read | Hämta värd miljöer multiroll-mått definitioner för pooler. |
> | Action | Microsoft. Web/hostingenvironments/multirolepools/Metrics/Read | Hämta värd miljöer multiroll-mått för pooler. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Read | Hämta egenskaperna för en FrontEnd-pool i en App Service-miljön |
> | Action | microsoft.web/hostingenvironments/multirolepools/skus/read | Hämta värd miljöer Multirole-pooler SKU: er. |
> | Action | Microsoft. Web/hostingenvironments/multirolepools/Usage/Read | Få värd miljöer för användning av pooler med multiroller. |
> | Action | Microsoft.Web/hostingEnvironments/multiRolePools/Write | Skapa en ny FrontEnd-pool i en App Service-miljön eller uppdatera en befintlig |
> | Action | microsoft.web/hostingenvironments/operations/read | Hämta åtgärder för värd miljöer. |
> | Action | Microsoft. Web/hostingenvironments/outboundnetworkdependenciesendpoints/Read | Hämta nätverks slut punkter för alla utgående beroenden. |
> | Action | Microsoft.Web/hostingEnvironments/PrivateEndpointConnectionsApproval/action | Godkänn anslutningar för privata slut punkter |
> | Action | Microsoft. Web/hostingEnvironments/Read | Hämta egenskaperna för en App Service-miljön |
> | Action | Microsoft. Web/hostingEnvironments/rereboot/Action | Starta om alla datorer i en App Service-miljön |
> | Action | Microsoft. Web/hostingenvironments/återuppta/åtgärd | Återuppta värd miljöer. |
> | Action | microsoft.web/hostingenvironments/serverfarms/read | Få värd miljöer App Service planer. |
> | Action | microsoft.web/hostingenvironments/sites/read | Hämta värd miljöer Web Apps. |
> | Action | microsoft.web/hostingenvironments/suspend/action | Pausa värd miljöer. |
> | Action | microsoft.web/hostingenvironments/usages/read | Hämta användning av värd miljöer. |
> | Action | Microsoft. Web/hostingenvironments/workerpools/metricdefinitions/Read | Hämta värd miljöer Workerpools mått definitioner. |
> | Action | Microsoft. Web/hostingenvironments/workerpools/Metrics/Read | Hämta värd miljöer Workerpools mått. |
> | Action | Microsoft. Web/hostingEnvironments/workerPools/Read | Hämta egenskaperna för en arbets grupp i en App Service-miljön |
> | Action | microsoft.web/hostingenvironments/workerpools/skus/read | Hämta värd miljöer Workerpools SKU: er. |
> | Action | Microsoft. Web/hostingenvironments/workerpools/Usage/Read | Hämta värd miljöer Workerpools-användningar. |
> | Action | Microsoft. Web/hostingEnvironments/workerPools/Write | Skapa en ny arbets pool i ett App Service-miljön eller uppdatera en befintlig |
> | Action | Microsoft. Web/hostingEnvironments/Write | Skapa en ny App Service-miljön eller uppdatera en befintlig |
> | Action | Microsoft. Web/ishostingenvironmentnameavailable/Read | Ta reda på om värd miljö namnet är tillgängligt. |
> | Action | Microsoft. Web/ishostnameavailable/Read | Kontrol lera om hostname är tillgängligt. |
> | Action | microsoft.web/isusernameavailable/read | Kontrol lera om användar namnet är tillgängligt. |
> | Action | Microsoft. Web/listSitesAssignedToHostName/Read | Hämta namn på platser tilldelade till värdnamn. |
> | Action | Microsoft. Web/locations/apioperations/Read | Hämta API-åtgärder för platser. |
> | Action | microsoft.web/locations/connectiongatewayinstallations/read | Hämta platser anslutnings-Gateway-installationer. |
> | Action | microsoft.web/locations/deleteVirtualNetworkOrSubnets/action | Borttagnings meddelande för VNet eller undernät för platser. |
> | Action | microsoft.web/locations/extractapidefinitionfromwsdl/action | Extrahera API-definition från WSDL för platser. |
> | Action | microsoft.web/locations/listwsdlinterfaces/action | Visa en lista över WSDL-gränssnitt för platser. |
> | Action | Microsoft. Web/locations/managedapis/apioperations/Read | Hämta platser hanterade API-åtgärder. |
> | Action | Microsoft. Web/locations/managedapis/JOIN/åtgärd | Ansluter till en hanterad API. |
> | Action | microsoft.web/locations/managedapis/read | Hämta platser hanterade API: er. |
> | Action | microsoft.web/operations/read | Hämta åtgärder. |
> | Action | Microsoft. Web/publishingusers/Read | Hämta publicerings användare. |
> | Action | Microsoft. Web/publishingusers/Write | Uppdatera publicerings användare. |
> | Action | Microsoft. Web/rekommendationer/läsa | Hämta listan över rekommendationer för prenumerationer. |
> | Action | Microsoft. Web/register/åtgärd | Registrera Microsoft. Web Resource Provider för prenumerationen. |
> | Action | Microsoft. Web/resourcehealthmetadata/Read | Hämta Resource Health metadata. |
> | Action | microsoft.web/serverfarms/capabilities/read | Få App Services plan funktioner. |
> | Action | Microsoft.Web/serverfarms/Delete | Ta bort en befintlig App Service plan |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/delete | Ta bort App Service planerar inställningar för första parts appar. |
> | Action | microsoft.web/serverfarms/firstpartyapps/settings/read | Hämta App Service planerar inställningar för den första partens appar. |
> | Action | Microsoft. Web/Server grupper/firstpartyapps/Settings/Write | Uppdatera App Service planerar inställningarna för den första parts-appen. |
> | Action | Microsoft. Web/Server grupper/hybridconnectionnamespaces/reläs/Delete | Ta bort App Service planer hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Server grupper/hybridconnectionnamespaces/reläs/Read | Hämta App Service planer hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Server grupper/hybridconnectionnamespaces/reläs/Sites/Read | Hämta App Service planer hybrid anslutnings namn områden reläer Web Apps. |
> | Action | microsoft.web/serverfarms/hybridconnectionplanlimits/read | Få App Service planer begränsningar för Hybrid anslutnings planen. |
> | Action | microsoft.web/serverfarms/hybridconnectionrelays/read | Få App Service planer hybrid anslutnings reläer. |
> | Action | microsoft.web/serverfarms/metricdefinitions/read | Hämta mått definitioner för App Service planer. |
> | Action | microsoft.web/serverfarms/metrics/read | Hämta mått för App Service planer. |
> | Action | microsoft.web/serverfarms/operationresults/read | Hämta App Service planer åtgärds resultat. |
> | Action | Microsoft.Web/serverfarms/Read | Hämta egenskaperna för en App Service plan |
> | Action | Microsoft.Web/serverfarms/restartSites/Action | Starta om alla Web Apps i en App Service-plan |
> | Action | microsoft.web/serverfarms/sites/read | Hämta App Services planer Web Apps. |
> | Action | microsoft.web/serverfarms/skus/read | Hämta App Service planer SKU: er. |
> | Action | Microsoft. Web/Server grupper/usages/Read | Få App Services Plans användningar. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/gateways/write | Uppdatera App Service-planer Virtual Network anslutningar-gatewayer. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/read | Få App Services planer Virtual Network anslutningar. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/delete | Ta bort App Service planer Virtual Network anslutnings vägar. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/read | Få App Services planer Virtual Network anslutnings vägar. |
> | Action | microsoft.web/serverfarms/virtualnetworkconnections/routes/write | Uppdatera App Service planer Virtual Network anslutnings vägar. |
> | Action | Microsoft. Web/Server grupper/Worker/reboot/åtgärd | Starta om App Service planerar arbetare. |
> | Action | Microsoft. Web/Server grupper/Write | Skapa en ny App Service plan eller uppdatera en befintlig |
> | Action | Microsoft. Web/Sites/analyzecustomhostname/Read | Analysera anpassat värdnamn. |
> | Action | Microsoft.Web/sites/applySlotConfig/Action | Använd webb program plats konfigurationen från mål platsen till den aktuella webb programmet |
> | Action | Microsoft. Web/Sites/backup/åtgärd | Skapa en ny säkerhets kopia av webbapp |
> | Action | Microsoft. Web/Sites/backup/Read | Hämta Web Apps säkerhets kopiering. |
> | Action | Microsoft. Web/Sites/backup/Write | Uppdatera Web Apps säkerhets kopiering. |
> | Action | Microsoft. Web/Sites/säkerhets kopieringar/åtgärd | Identifierar en befintlig säkerhets kopia av appen som kan återställas från en BLOB i Azure Storage. |
> | Action | Microsoft. Web/Sites/backups/Delete | Ta bort Web Apps säkerhets kopior. |
> | Action | Microsoft. Web/Sites/backups/List/Action | Visa Web Apps säkerhets kopieringar. |
> | Action | Microsoft. Web/Sites/backups/Read | Hämta egenskaperna för en webb programs säkerhets kopia |
> | Action | Microsoft. Web/Sites/backups/Restore/Action | Återställa Web Apps säkerhets kopior. |
> | Action | Microsoft. Web/Sites/backups/Write | Uppdatera Web Apps säkerhets kopieringar. |
> | Action | microsoft.web/sites/config/delete | Ta bort Web Apps config. |
> | Action | Microsoft. Web/Sites/config/List/Action | Visa en lista över säkerhets känsliga inställningar för webbappen, till exempel att publicera autentiseringsuppgifter, appinställningar och anslutnings strängar |
> | Action | Microsoft.Web/sites/config/Read | Hämta konfigurations inställningar för webb program |
> | Action | Microsoft. Web/Sites/config/snaps hots/Read | Hämta Web Apps config-ögonblicksbilder. |
> | Action | Microsoft. Web/Sites/config/Write | Uppdatera konfigurations inställningar för webb program |
> | Action | microsoft.web/sites/containerlogs/action | Hämta zippade behållar loggar för Web App. |
> | Action | Microsoft. Web/Sites/containerlogs/hämtning/åtgärd | Ladda ned Web Apps container-loggar. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/Delete | Ta bort Web Apps kontinuerliga webb jobb. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/Read | Hämta Web Apps kontinuerliga webb jobb. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/start/Action | Starta Web Apps kontinuerliga webb jobb. |
> | Action | Microsoft. Web/Sites/continuouswebjobs/stoppa/åtgärd | Stoppa Web Apps kontinuerliga webb jobb. |
> | Action | Microsoft.Web/sites/Delete | Ta bort en befintlig webbapp |
> | Action | Microsoft. Web/Sites/Deployments/Delete | Ta bort Web Apps distributioner. |
> | Action | Microsoft. Web/Sites/distributioner/log/Read | Hämta Web Apps distributions logg. |
> | Action | Microsoft. Web/Sites/Deployments/Read | Hämta Web Apps-distributioner. |
> | Action | Microsoft. Web/Sites/distributioner/Write | Uppdatera Web Apps-distributioner. |
> | Action | Microsoft. Web/Sites/Detections/Read | Hämta Web Apps identifieringar. |
> | Action | Microsoft. Web/Sites/diagnostik/analyser/kör/åtgärd | Kör Web Apps Diagnostics-analys. |
> | Action | Microsoft. Web/Sites/Diagnostics/analyser/Read | Hämta Web Apps Diagnostics-analys. |
> | Action | microsoft.web/sites/diagnostics/aspnetcore/read | Hämta Web Apps Diagnostics för ASP.NET Core-appen. |
> | Action | microsoft.web/sites/diagnostics/autoheal/read | Hämta Web Apps Diagnostics autoläka. |
> | Action | Microsoft. Web/Sites/Diagnostics/Deployment/Read | Hämta Web Apps Diagnostics-distribution. |
> | Action | Microsoft. Web/Sites/Diagnostics/distribution/Read | Hämta Web Apps Diagnostics-distributioner. |
> | Action | Microsoft. Web/Sites/Diagnostics/Detections/EXECUTE/Action | Kör Web Apps Diagnostics detektor. |
> | Action | Microsoft. Web/Sites/Diagnostics/Detections/Read | Hämta Web Apps Diagnostics detektor. |
> | Action | microsoft.web/sites/diagnostics/failedrequestsperuri/read | Hämta Web Apps diagnostik misslyckade förfrågningar per URI. |
> | Action | microsoft.web/sites/diagnostics/frebanalysis/read | Hämta Web Apps Diagnostics FREB Analysis. |
> | Action | Microsoft. Web/Sites/Diagnostics/loganalyzer/Read | Hämta Web Apps Diagnostics Log Analyzer. |
> | Action | Microsoft. Web/Sites/Diagnostics/Read | Hämta Web Apps diagnostiska kategorier. |
> | Action | Microsoft. Web/Sites/Diagnostics/runtimeavailability/Read | Hämta Web Apps Diagnostics runtime-tillgänglighet. |
> | Action | microsoft.web/sites/diagnostics/servicehealth/read | Hämta Web Apps Diagnostics Service Health. |
> | Action | Microsoft. Web/Sites/Diagnostics/sitecpuanalysis/Read | Hämta CPU-analys för Web Apps Diagnostics site. |
> | Action | Microsoft. Web/Sites/Diagnostics/sitecrashes/Read | Hämta Web Apps Diagnostics site kraschar. |
> | Action | Microsoft. Web/Sites/Diagnostics/sitelatency/Read | Hämta svars tid för Web Apps Diagnostics. |
> | Action | microsoft.web/sites/diagnostics/sitememoryanalysis/read | Hämta minnes analys för Web Apps Diagnostics site. |
> | Action | microsoft.web/sites/diagnostics/siterestartsettingupdate/read | Hämta Web Apps Diagnostics site restart-inställningen uppdatera. |
> | Action | Microsoft. Web/Sites/Diagnostics/siterestartuserinitiated/Read | Hämta Web Apps Diagnostics site starta om användaren. |
> | Action | Microsoft. Web/Sites/Diagnostics/siteswap/Read | Hämta Web Apps Diagnostics site swap. |
> | Action | Microsoft. Web/Sites/Diagnostics/ThreadCount/Read | Hämta antal trådar för Web Apps diagnostik. |
> | Action | Microsoft. Web/Sites/Diagnostics/workeravailability/Read | Hämta Web Apps Diagnostics Workeravailability. |
> | Action | Microsoft. Web/Sites/Diagnostics/workerprocessrecycle/Read | Hämta återkallning av arbets process för Web Apps Diagnostics. |
> | Action | Microsoft. Web/Sites/domainownershipidentifiers/Read | Hämta Web Apps domänens Ägarskaps identifierare. |
> | Action | Microsoft. Web/Sites/domainownershipidentifiers/Write | Uppdatera Web Apps domän Ägarskaps identifierare. |
> | Action | microsoft.web/sites/extensions/delete | Ta bort Web Apps plats tillägg. |
> | Action | microsoft.web/sites/extensions/read | Hämta Web Apps webbplats tillägg. |
> | Action | microsoft.web/sites/extensions/write | Uppdatera Web Apps plats tillägg. |
> | Action | Microsoft. Web/Sites/Functions/åtgärd | Functions Web Apps. |
> | Action | Microsoft. Web/Sites/Functions/Delete | Ta bort Web Apps-funktioner. |
> | Action | Microsoft. Web/Sites/Functions/listsecrets/Action | Lista hemligheter Web Apps funktioner. |
> | Action | Microsoft. Web/Sites/Functions/MasterKey/Read | Hämta Web Apps Functions MasterKey. |
> | Action | Microsoft. Web/Sites/Functions/Read | Hämta Web Apps-funktioner. |
> | Action | Microsoft. Web/Sites/Functions/token/Read | Hämta Web Apps Functions-token. |
> | Action | Microsoft. Web/Sites/Functions/Write | Uppdatera Web Apps-funktioner. |
> | Action | Microsoft. Web/Sites/hostnamebindings/Delete | Ta bort Web Apps hostname-bindningar. |
> | Action | Microsoft. Web/Sites/hostnamebindings/Read | Hämta Web Apps hostname-bindningar. |
> | Action | Microsoft. Web/Sites/hostnamebindings/Write | Uppdatera Web Apps hostname-bindningar. |
> | Action | microsoft.web/sites/hostruntime/functions/keys/read | Hämta Web Apps funktions nycklar för Hostruntime. |
> | Action | Microsoft. Web/Sites/hostruntime/Host/_master/Read | Hämta Funktionsapp huvud nyckeln för administratörs åtgärder |
> | Action | Microsoft. Web/Sites/hostruntime/värd/åtgärd | Utför Funktionsapp körnings åtgärd som Sync-utlösare, Lägg till funktioner, anropa funktioner, ta bort funktioner osv. |
> | Action | Microsoft. Web/Sites/hostruntime/Host/Read | Hämta Web Apps Hostruntime-värden. |
> | Action | Microsoft. Web/Sites/resursprovider/Delete | Ta bort Web Apps hybrid anslutning. |
> | Action | Microsoft. Web/Sites/resursprovider/Read | Hämta Web Apps hybrid anslutning. |
> | Action | Microsoft. Web/Sites/resursprovider/Write | Uppdatera Web Apps hybrid anslutning. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/reläs/Delete | Ta bort Web Apps hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/relays/listnycklar/Action | Visar en lista över nycklar Web Apps hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/reläs/Read | Hämta Web Apps hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Sites/hybridconnectionnamespaces/reläs/Write | Uppdatera Web Apps hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Sites/hybridconnectionrelays/Read | Hämta Web Apps hybrid anslutnings reläer. |
> | Action | Microsoft. Web/Sites/instances/Deployments/Delete | Ta bort Web Apps instans distributioner. |
> | Action | Microsoft. Web/Sites/instances/Deployments/Read | Hämta Web Apps instans distributioner. |
> | Action | microsoft.web/sites/instances/extensions/log/read | Hämta Web Apps Instanss Extensions-logg. |
> | Action | Microsoft. Web/Sites/instances/Extensions/Processes/Read | Hämta Web Apps Instanss Extensions-processer. |
> | Action | microsoft.web/sites/instances/extensions/read | Hämta Web Apps instans tillägg. |
> | Action | Microsoft. Web/Sites/instances/processs/Delete | Ta bort Web Apps instans processer. |
> | Action | Microsoft. Web/Sites/instances/Processes/modules/Read | Hämta Web Apps instanser bearbetar moduler. |
> | Action | Microsoft. Web/Sites/instances/processs/Read | Hämta Web Apps instans processer. |
> | Action | Microsoft. Web/Sites/instances/processs/threads/Read | Hämta Web Apps instanser bearbetar trådar. |
> | Action | Microsoft. Web/Sites/instances/Read | Hämta Web Apps instanser. |
> | Action | microsoft.web/sites/listsyncfunctiontriggerstatus/action | Visa lista över status för Sync-funktionen trigger Web Apps. |
> | Action | microsoft.web/sites/metricdefinitions/read | Hämta Web Apps mått definitioner. |
> | Action | Microsoft. Web/Sites/Metrics/Read | Hämta Web Apps mått. |
> | Action | Microsoft. Web/Sites/metricsdefinitions/Read | Hämta Web Apps mått definitioner. |
> | Action | microsoft.web/sites/migratemysql/action | Migrera MySql-Web Apps. |
> | Action | microsoft.web/sites/migratemysql/read | Hämta Web Apps migrera MySql. |
> | Action | microsoft.web/sites/networktrace/action | Web Apps för nätverks spårning. |
> | Action | microsoft.web/sites/networktraces/operationresults/read | Få Web Apps nätverks spårnings åtgärds resultat. |
> | Action | Microsoft. Web/Sites/newpassword/Action | Newpassword Web Apps. |
> | Action | microsoft.web/sites/operationresults/read | Hämta Web Apps åtgärds resultat. |
> | Action | microsoft.web/sites/operations/read | Hämta Web Apps-åtgärder. |
> | Action | microsoft.web/sites/perfcounters/read | Hämta Web Apps prestanda räknare. |
> | Action | microsoft.web/sites/premieraddons/delete | Ta bort Web Apps Premier-tillägg. |
> | Action | microsoft.web/sites/premieraddons/read | Få Web Apps Premier-tillägg. |
> | Action | microsoft.web/sites/premieraddons/write | Uppdatera Web Apps Premier-tillägg. |
> | Action | Microsoft. Web/Sites/privateaccess/Read | Hämta data runt åtkomst till privata webbplatser och auktoriserade virtuella nätverk som har åtkomst till webbplatsen. |
> | Action | Microsoft. Web/Sites/PrivateEndpointConnectionsApproval/Action | Godkänn anslutningar för privata slut punkter |
> | Action | microsoft.web/sites/processes/modules/read | Hämta moduler för Web Apps processer. |
> | Action | Microsoft. Web/Sites/Processes/Read | Hämta Web Appss processer. |
> | Action | Microsoft. Web/Sites/Processes/threads/Read | Hämta Web Apps bearbetar trådar. |
> | Action | microsoft.web/sites/publiccertificates/delete | Ta bort Web Apps offentliga certifikat. |
> | Action | microsoft.web/sites/publiccertificates/read | Hämta Web Apps offentliga certifikat. |
> | Action | microsoft.web/sites/publiccertificates/write | Uppdatera Web Apps offentliga certifikat. |
> | Action | Microsoft. Web/Sites/Publish/Action | Publicera en webbapp |
> | Action | Microsoft. Web/Sites/publishxml/Action | Hämta XML för publicerings profil för en webbapp |
> | Action | microsoft.web/sites/publishxml/read | Hämta Web Apps Publishing XML. |
> | Action | Microsoft. Web/Sites/Read | Hämta egenskaperna för en webbapp |
> | Action | Microsoft. Web/Sites/recommendationhistory/Read | Hämta Web Apps rekommendations historik. |
> | Action | Microsoft. Web/Sites/rekommendationer/inaktivera/åtgärd | Inaktivera Web Apps rekommendationer. |
> | Action | Microsoft. Web/Sites/rekommendationer/läsa | Hämta listan över rekommendationer för Web App. |
> | Action | Microsoft. Web/Sites/Recover/Action | Återställ Web Apps. |
> | Action | Microsoft.Web/sites/resetSlotConfig/Action | Återställ konfiguration av webbapp |
> | Action | Microsoft. Web/Sites/resourcehealthmetadata/Read | Hämta Web Apps Resource Health metadata. |
> | Action | Microsoft. Web/Sites/restart/Action | Starta om en webbapp |
> | Action | microsoft.web/sites/restore/read | Hämta Web Apps Restore. |
> | Action | Microsoft. Web/Sites/Restore/Write | Återställ Web Apps. |
> | Action | Microsoft. Web/Sites/restorefrombackupblob/Action | Återställ en webbapp från en backup-blob. |
> | Action | Microsoft. Web/Sites/restorefromdeletedapp/Action | Återställ Web Apps från den borttagna appen. |
> | Action | Microsoft. Web/Sites/restoresnapshot/Action | Återställa Web Apps ögonblicks bilder. |
> | Action | microsoft.web/sites/siteextensions/delete | Ta bort Web Apps plats tillägg. |
> | Action | microsoft.web/sites/siteextensions/read | Hämta Web Apps webbplats tillägg. |
> | Action | microsoft.web/sites/siteextensions/write | Uppdatera Web Apps plats tillägg. |
> | Action | microsoft.web/sites/slots/analyzecustomhostname/read | Hämta Web Apps platser analysera anpassat värdnamn. |
> | Action | Microsoft.Web/sites/slots/applySlotConfig/Action | Använd webb program plats konfigurationen från mål platsen till den aktuella platsen. |
> | Action | Microsoft. Web/Sites/platser/säkerhets kopiering/åtgärd | Skapa ny säkerhets kopia av webb program plats. |
> | Action | microsoft.web/sites/slots/backup/read | Hämta säkerhets kopiering av Web Apps platser. |
> | Action | microsoft.web/sites/slots/backup/write | Uppdatera säkerhets kopierings Web Apps platser. |
> | Action | Microsoft. Web/Sites/platser/säkerhets kopiering/åtgärd | Identifiera säkerhets kopiering av Web Apps platser. |
> | Action | microsoft.web/sites/slots/backups/delete | Ta bort säkerhets kopior för Web Apps platser. |
> | Action | Microsoft. webb/platser/platser/säkerhets kopieringar/lista/åtgärd | Visa Web Apps säkerhets kopiering av platser. |
> | Action | Microsoft. Web/Sites/lotss/backups/Read | Hämta egenskaperna för en säkerhets kopia av Web App-platser |
> | Action | Microsoft. Web/Sites/platser/säkerhets kopiering/återställning/åtgärd | Återställa säkerhets kopior av Web Apps platser. |
> | Action | microsoft.web/sites/slots/config/delete | Ta bort konfiguration av Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/config/List/Action | Visa en lista med säkerhets känsliga inställningar för webbappens plats, till exempel autentiseringsuppgifter för publicering, appar och anslutnings strängar |
> | Action | Microsoft. Web/Sites/lotss/config/Read | Hämta konfigurations inställningar för webb program plats |
> | Action | Microsoft.Web/sites/slots/config/Write | Uppdatera konfigurations inställningar för webb program plats |
> | Action | microsoft.web/sites/slots/containerlogs/action | Hämta zippade behållar loggar för webb program plats. |
> | Action | Microsoft. Web/Sites/lotss/containerlogs/hämtning/åtgärd | Hämta behållar loggar för Web Apps platser. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/delete | Ta bort Web Apps platser kontinuerliga webb jobb. |
> | Action | microsoft.web/sites/slots/continuouswebjobs/read | Hämta Web Apps platser kontinuerliga webb jobb. |
> | Action | Microsoft. Web/Sites/lotss/continuouswebjobs/start/Action | Starta Web Apps platser kontinuerliga webb jobb. |
> | Action | Microsoft. webb/platser/platser/continuouswebjobs/stoppa/åtgärd | Stoppa Web Apps platser kontinuerligt webb jobb. |
> | Action | Microsoft.Web/sites/slots/Delete | Ta bort en befintlig webbapp |
> | Action | microsoft.web/sites/slots/deployments/delete | Ta bort distributioner av Web Apps platser. |
> | Action | microsoft.web/sites/slots/deployments/log/read | Hämta distributions logg för Web Apps platser. |
> | Action | microsoft.web/sites/slots/deployments/read | Hämta distributioner av Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Deployments/Write | Uppdatera distributioner av Web Apps-platser. |
> | Action | microsoft.web/sites/slots/detectors/read | Hämta Web Apps platser detektorer. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/analyser/EXECUTE/Action | Kör diagnostik för Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/analyser/Read | Hämta diagnos analys för Web Apps platser. |
> | Action | microsoft.web/sites/slots/diagnostics/aspnetcore/read | Hämta Web Apps kortplatser-diagnostik för ASP.NET Core-appen. |
> | Action | microsoft.web/sites/slots/diagnostics/autoheal/read | Hämta Web Apps platser diagnostik reläka. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/Deployment/Read | Hämta diagnostisk distribution av Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/distribution/Read | Hämta diagnostik-distributioner för Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/Detections/EXECUTE/Action | Kör Web Apps platser Diagnostics detektor. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/Detections/Read | Hämta Web Apps platsers diagnostiska detektor. |
> | Action | microsoft.web/sites/slots/diagnostics/frebanalysis/read | Hämta Web Apps platser diagnostik FREB analys. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/loganalyzer/Read | Hämta Web Apps platser diagnostisk logg analys. |
> | Action | microsoft.web/sites/slots/diagnostics/read | Hämta diagnostik för Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/runtimeavailability/Read | Hämta Web Apps platser diagnostisk körnings tillgänglighet. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/servicehealth/Read | Hämta Web Apps platser Diagnostics Service Health. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/sitecpuanalysis/Read | Hämta Web Apps platser diagnostisk webbplats CPU-analys. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/sitecrashes/Read | Hämta Web Apps platser Diagnostics site kraschar. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/sitelatency/Read | Hämta svars tid för webbplats för Web Apps platser. |
> | Action | microsoft.web/sites/slots/diagnostics/sitememoryanalysis/read | Hämta Web Apps platser Diagnostics site Memory Analysis. |
> | Action | microsoft.web/sites/slots/diagnostics/siterestartsettingupdate/read | Hämta Web Apps platser diagnostisk webbplats starta om uppdatering. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/siterestartuserinitiated/Read | Hämta Web Apps platser diagnostisk webbplats starta om användaren. |
> | Action | microsoft.web/sites/slots/diagnostics/siteswap/read | Hämta Web Apps platser diagnostiska plats växling. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/ThreadCount/Read | Hämta antal trådar för Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/workeravailability/Read | Hämta Web Apps platser Diagnostics Workeravailability. |
> | Action | Microsoft. Web/Sites/lotss/Diagnostics/workerprocessrecycle/Read | Hämta Web Apps platser Diagnostics Worker-processen återvinns. |
> | Action | Microsoft. Web/Sites/lotss/domainownershipidentifiers/Read | Hämta Web Apps platser domän Ägarskaps identifierare. |
> | Action | microsoft.web/sites/slots/functions/listsecrets/action | Lista hemligheter Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/Functions/Read | Hämta Web Apps platser-funktioner. |
> | Action | Microsoft. Web/Sites/lotss/hostnamebindings/Delete | Ta bort bindningar för Web Apps-kortplatser. |
> | Action | Microsoft. Web/Sites/lotss/hostnamebindings/Read | Hämta Web Apps platser hostname-bindningar. |
> | Action | Microsoft. Web/Sites/lotss/hostnamebindings/Write | Uppdatera bindningar för Web Apps-kortplatser. |
> | Action | Microsoft. Web/Sites/lotss/resursprovider/Delete | Ta bort Web Apps platser hybrid anslutning. |
> | Action | Microsoft. Web/Sites/lotss/resursprovider/Read | Hämta Web Apps platser hybrid anslutning. |
> | Action | microsoft.web/sites/slots/hybridconnection/write | Uppdatera Web Apps platser hybrid anslutning. |
> | Action | Microsoft. Web/Sites/lotss/hybridconnectionnamespaces/reläs/Delete | Ta bort Web Apps platser hybrid anslutnings namn rymder reläer. |
> | Action | Microsoft. Web/Sites/lotss/hybridconnectionnamespaces/reläs/Write | Uppdatera Web Apps platser hybrid anslutnings namn rymder reläer. |
> | Action | microsoft.web/sites/slots/hybridconnectionrelays/read | Hämta Web Apps platser hybrid anslutnings reläer. |
> | Action | microsoft.web/sites/slots/instances/deployments/read | Hämta distributioner av instanser av Web Apps platser. |
> | Action | microsoft.web/sites/slots/instances/processes/delete | Ta bort instans processer för Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/instances/processs/Read | Hämta instans processer för Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/instances/Read | Hämta instanser av Web Apps platser. |
> | Action | microsoft.web/sites/slots/metricdefinitions/read | Hämta mått definitioner för Web Apps platser. |
> | Action | microsoft.web/sites/slots/metrics/read | Hämta mått för Web Apps platser. |
> | Action | microsoft.web/sites/slots/migratemysql/read | Hämta Web Apps platser migrera MySql. |
> | Action | microsoft.web/sites/slots/networktrace/action | Nätverks spårning Web Apps platser. |
> | Action | microsoft.web/sites/slots/networktraces/operationresults/read | Hämta Web Apps platser nätverks spårnings åtgärds resultat. |
> | Action | microsoft.web/sites/slots/newpassword/action | Newpassword Web Apps-kortplatser. |
> | Action | microsoft.web/sites/slots/operationresults/read | Hämta Web Apps platser åtgärds resultat. |
> | Action | microsoft.web/sites/slots/operations/read | Hämta åtgärder för Web Apps platser. |
> | Action | microsoft.web/sites/slots/perfcounters/read | Hämta prestanda räknare för Web Apps platser. |
> | Action | microsoft.web/sites/slots/phplogging/read | Hämta Web Apps platser Phplogging. |
> | Action | microsoft.web/sites/slots/premieraddons/delete | Ta bort Web Apps platser Premier-tillägg. |
> | Action | microsoft.web/sites/slots/premieraddons/read | Hämta Web Apps platser Premier-tillägg. |
> | Action | microsoft.web/sites/slots/premieraddons/write | Uppdatera Web Apps platser Premier-tillägg. |
> | Action | microsoft.web/sites/slots/processes/read | Hämta processer för Web Apps platser. |
> | Action | microsoft.web/sites/slots/publiccertificates/delete | Ta bort offentliga certifikat för Web Apps platser. |
> | Action | microsoft.web/sites/slots/publiccertificates/read | Hämta offentliga certifikat för Web Apps platser. |
> | Action | microsoft.web/sites/slots/publiccertificates/write | Skapa eller uppdatera offentliga certifikat för Web Apps platser. |
> | Action | Microsoft. Web/Sites/platser/publicera/åtgärd | Publicera en webbapp |
> | Action | Microsoft. Web/Sites/lotss/publishxml/åtgärd | Hämta publicerings profil-XML för webb program plats |
> | Action | Microsoft. Web/Sites/lots/Read | Hämta egenskaperna för en webb program distributions plats |
> | Action | Microsoft. Web/Sites/lotss/Recover/Action | Återställa Web Apps platser. |
> | Action | Microsoft.Web/sites/slots/resetSlotConfig/Action | Återställ webb program plats konfiguration |
> | Action | Microsoft. Web/Sites/lotss/resourcehealthmetadata/Read | Hämta Web Apps platser Resource Health metadata. |
> | Action | Microsoft. Web/Sites/lotss/restart/Action | Starta om en webbapp |
> | Action | microsoft.web/sites/slots/restore/read | Hämta Web Apps platser återställning. |
> | Action | microsoft.web/sites/slots/restore/write | Återställa Web Apps-kortplatser. |
> | Action | microsoft.web/sites/slots/restorefrombackupblob/action | Återställ Web Apps-kortplats från backup blob. |
> | Action | Microsoft. Web/Sites/lotss/restorefromdeletedapp/åtgärd | Återställ webb program platser från den borttagna appen. |
> | Action | Microsoft. Web/Sites/lotss/restoresnapshot/åtgärd | Återställa ögonblicks bilder av Web Apps platser. |
> | Action | microsoft.web/sites/slots/siteextensions/delete | Ta bort Web Apps platser plats tillägg. |
> | Action | microsoft.web/sites/slots/siteextensions/read | Hämta Web Apps platser plats tillägg. |
> | Action | microsoft.web/sites/slots/siteextensions/write | Uppdatera Web Apps platser plats tillägg. |
> | Action | Microsoft. Web/Sites/lotss/slotsdiffs/åtgärd | Få skillnader i konfigurationen mellan webbapp och platser |
> | Action | Microsoft. Web/Sites/lotss/slotsswap/åtgärd | Växla distributions platser för webbappar |
> | Action | microsoft.web/sites/slots/snapshots/read | Hämta ögonblicks bilder av Web Apps platser. |
> | Action | Microsoft. Web/Sites/lotss/sourcecontrols/Delete | Ta bort konfigurations inställningar för webb program platsens käll kontroll |
> | Action | Microsoft. Web/Sites/lotss/sourcecontrols/Read | Hämta konfigurations inställningar för webb program platsens käll kontroll |
> | Action | Microsoft. Web/Sites/lotss/sourcecontrols/Write | Uppdatera konfigurations inställningar för webb program platsens käll kontroll |
> | Action | Microsoft. Web/Sites/lotss/start/Action | Starta en webbapp |
> | Action | Microsoft. Web/Sites/platser/stoppa/åtgärd | Stoppa en webbapp |
> | Action | microsoft.web/sites/slots/sync/action | Synkronisera Web Apps platser. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/delete | Ta bort Web Apps platser utlösta WebJobs. |
> | Action | microsoft.web/sites/slots/triggeredwebjobs/read | Hämta Web Apps platser utlöst WebJobs. |
> | Action | Microsoft. Web/Sites/lotss/triggeredwebjobs/kör/åtgärd | Kör Web Apps platser utlöst WebJobs. |
> | Action | microsoft.web/sites/slots/usages/read | Hämta Web Apps kort platser. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/delete | Ta bort Web Apps platser Virtual Network anslutningar. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/gateways/write | Uppdatera Web Apps platser Virtual Network anslutningar-gatewayer. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/read | Hämta Web Apps platser Virtual Network anslutningar. |
> | Action | microsoft.web/sites/slots/virtualnetworkconnections/write | Uppdatera Web Apps platser Virtual Network anslutningar. |
> | Action | microsoft.web/sites/slots/webjobs/read | Hämta Web Apps platser WebJobs. |
> | Action | Microsoft. Web/Sites/lotss/Write | Skapa en ny webbapp eller uppdatera en befintlig |
> | Action | Microsoft. Web/Sites/slotsdiffs/Action | Få skillnader i konfigurationen mellan webbapp och platser |
> | Action | Microsoft. Web/Sites/slotsswap/Action | Växla distributions platser för webbappar |
> | Action | Microsoft. Web/Sites/snaps hots/Read | Hämta Web Apps ögonblicks bilder. |
> | Action | Microsoft. Web/Sites/sourcecontrols/Delete | Ta bort konfigurations inställningar för webb programs käll kontroll |
> | Action | Microsoft. Web/Sites/sourcecontrols/Read | Hämta konfigurations inställningar för webb programs käll kontroll |
> | Action | Microsoft. Web/Sites/sourcecontrols/Write | Uppdatera konfigurations inställningar för webb programs käll kontroll |
> | Action | Microsoft. Web/Sites/start/Action | Starta en webbapp |
> | Action | Microsoft. Web/Sites/stoppa/åtgärd | Stoppa en webbapp |
> | Action | microsoft.web/sites/sync/action | Synkronisera Web Apps. |
> | Action | microsoft.web/sites/syncfunctiontriggers/action | Synkronisera funktions utlösare för Web Apps. |
> | Action | Microsoft. Web/Sites/triggeredwebjobs/Delete | Ta bort Web Apps utlösta WebJobs. |
> | Action | Microsoft. Web/Sites/triggeredwebjobs/historik/Read | Hämta Web Apps utlösta WebJobs-historik. |
> | Action | Microsoft. Web/Sites/triggeredwebjobs/Read | Hämta Web Apps utlösta WebJobs. |
> | Action | Microsoft. Web/Sites/triggeredwebjobs/Run/Action | Kör Web Apps utlösta WebJobs. |
> | Action | Microsoft. Web/Sites/usages/Read | Få Web Apps-användning. |
> | Action | microsoft.web/sites/virtualnetworkconnections/delete | Ta bort Web Apps Virtual Network anslutningar. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/read | Hämta Web Apps Virtual Network anslutningar-gatewayer. |
> | Action | microsoft.web/sites/virtualnetworkconnections/gateways/write | Uppdatera Web Apps Virtual Network anslutningar-gatewayer. |
> | Action | microsoft.web/sites/virtualnetworkconnections/read | Hämta Web Apps Virtual Network anslutningar. |
> | Action | microsoft.web/sites/virtualnetworkconnections/write | Uppdatera Web Apps Virtual Network anslutningar. |
> | Action | Microsoft. Web/Sites/WebJobs/Read | Hämta Web Apps WebJobs. |
> | Action | Microsoft. Web/Sites/Write | Skapa en ny webbapp eller uppdatera en befintlig |
> | Action | microsoft.web/skus/read | Hämta SKU: er. |
> | Action | Microsoft. Web/sourcecontrols/Read | Hämta käll kontroller. |
> | Action | Microsoft. Web/sourcecontrols/Write | Uppdatera käll kontroller. |
> | Action | microsoft.web/unregister/action | Avregistrera Microsoft. Web Resource Provider för prenumerationen. |
> | Action | microsoft.web/validate/action | Kontrollerar. |
> | Action | microsoft.web/verifyhostingenvironmentvnet/action | Verifiera värd miljön VNet. |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tdCol2BreakAll"]
> | Åtgärdstyp | Åtgärd | Beskrivning |
> | --- | --- | --- |
> | Action | Microsoft.WorkloadMonitor/components/read | Hämtar komponenter för resursen |
> | Action | Microsoft.WorkloadMonitor/componentsSummary/read | Hämtar Sammanfattning av komponenter |
> | Action | Microsoft. WorkloadMonitor/monitorInstances/Read | Hämtar instanser av Övervakare för resursen |
> | Action | Microsoft. WorkloadMonitor/monitorInstancesSummary/Read | Hämtar Sammanfattning av övervaknings instanser |
> | Action | Microsoft. WorkloadMonitor/Monitors/Read | Hämtar Övervakare för resursen |
> | Action | Microsoft. WorkloadMonitor/Monitors/Write | Konfigurera övervakaren för resursen |
> | Action | Microsoft. WorkloadMonitor/notificationSettings/Read | Hämtar meddelande inställningar för resursen |
> | Action | Microsoft.WorkloadMonitor/notificationSettings/write | Konfigurera meddelande inställningar för resursen |
> | Action | Microsoft.WorkloadMonitor/operations/read | Hämtar de åtgärder som stöds |

## <a name="next-steps"></a>Nästa steg

- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Inbyggda roller för Azure-resurser](built-in-roles.md)
