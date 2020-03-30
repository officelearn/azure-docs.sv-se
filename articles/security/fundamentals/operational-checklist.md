---
title: Checklista för Azure-driftsäkerhet| Microsoft-dokument
description: Den här artikeln innehåller en uppsättning checklistor för Azures driftsäkerhet.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: fb940857a0f88590cb9bbbf56b9e6a791299309f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980950"
---
# <a name="azure-operational-security-checklist"></a>Checklista för Azure-driftsäkerhet
Det går snabbt, enkelt och kostnadseffektivt att distribuera ett program på Azure. Innan du distribuerar molnprogram i produktion användbart för att ha en checklista för att hjälpa till att utvärdera ditt program mot en lista över viktiga och rekommenderade operativa säkerhetsåtgärder för dig att överväga.

## <a name="introduction"></a>Introduktion

Azure tillhandahåller en uppsättning infrastrukturtjänster som du kan använda för att distribuera dina program. Azure Operational Security refererar till de tjänster, kontroller och funktioner som är tillgängliga för användare för att skydda deras data, program och andra resurser i Microsoft Azure.

-   För att få ut maximal nytta av molnplattformen rekommenderar vi att du använder Azure-tjänster och följer checklistan.
-   Organisationer som investerar tid och resurser bedöma den operativa beredskapen för sina ansökningar innan lanseringen har en mycket högre grad av tillfredsställelse än de som inte gör det. När du utför detta arbete kan checklistor vara en ovärderlig mekanism för att säkerställa att program utvärderas konsekvent och holistiskt.
-   Nivån på den operativa bedömningen varierar beroende på organisationens molnmognadsnivå och programmets utvecklingsfas, tillgänglighetsbehov och datakänslighetskrav.

## <a name="checklist"></a>Checklista

Den här checklistan är avsedd att hjälpa företag att tänka igenom olika operativa säkerhetsöverväganden när de distribuerar avancerade företagsprogram på Azure. Den kan också användas för att hjälpa dig att skapa en säker molnmigrering och verksamhetsstrategi för din organisation.

|Kategori för checklista| Beskrivning|
| ------------ | -------- |
| [<br>Säkerhetsroller & åtkomstkontroller](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Använd [rollbaserad åtkomstkontroll (RBAC)](../../role-based-access-control/role-assignments-portal.md) för att tillhandahålla användarspecifik som används för att tilldela behörigheter till användare, grupper och program i ett visst omfång.</li></ul> |
| [<br>Lagring & datainsamling](../../storage/blobs/security-recommendations.md)|<ul><li>Använd Hanteringsplansäkerhet för att skydda ditt lagringskonto med hjälp av [rollbaserad åtkomstkontroll (RBAC).](../../role-based-access-control/role-assignments-portal.md)</li><li>Dataplansäkerhet för att skydda åtkomsten till dina data med hjälp av [SAS-signaturer (Shared Access Signatures)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) och principer för lagrad åtkomst.</li><li>Använd kryptering på transportnivå – Med HTTPS och kryptering som används av [SMB (Servermeddelandeblockprotokoll) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) för [Azure File Shares](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>Använd [kryptering på klientsidan](../../storage/common/storage-client-side-encryption.md) för att skydda data som du skickar till lagringskonton när du behöver ensam kontroll över krypteringsnycklar. </li><li>Använd [SSE (Storage Service Encryption)](../../storage/common/storage-service-encryption.md) för att automatiskt kryptera data i Azure Storage och [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) för att kryptera virtuella datordiskfiler för operativsystem och datadiskar.</li><li>Använd Azure [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) för att övervaka auktoriseringstyp; Med Blob Storage kan du se om användarna har använt en signatur för delad åtkomst eller lagringskontonycklarna.</li><li>Använd [CORS (Cross-Origin Resource Sharing)](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) för att komma åt lagringsresurser från olika domäner.</li></ul> |
|[<br>Säkerhetsprinciper & rekommendationer](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Använd [Azure Security Center](../../security-center/security-center-install-endpoint-protection.md) för att distribuera slutpunktslösningar.</li><li>Lägg till en [brandvägg för webbprogram (WAF)](../../application-gateway/waf-overview.md) för att skydda webbprogram.</li><li>   Använd en [brandvägg](../../sentinel/connect-data-sources.md) från en Microsoft-partner för att öka säkerhetsskyddet. </li><li>Använd säkerhetskontaktinformation för din Azure-prenumeration. detta kontaktar [Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) dig om den upptäcker att dina kunddata har använts av en olaglig eller obehörig part.</li></ul> |
| [<br>Hantering av & åtkomst](identity-management-best-practices.md)|<ul><li>[Synkronisera din lokala katalog med molnkatalogen med Azure AD](../../active-directory/hybrid/whatis-hybrid-identity.md).</li><li>Använd Enkel inloggning för att göra det möjligt för användare att komma åt sina [SaaS-program](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) baserat på deras organisationskonto i Azure AD.</li><li>Använd rapporten [Registreringsaktivitet för lösenordsåterställning](../../active-directory/active-directory-passwords-reporting.md) för att övervaka de användare som registrerar sig.</li><li>Aktivera [MFA (Multi Factor Authentication)](../../active-directory/authentication/multi-factor-authentication.md) för användare.</li><li>Utvecklare kan använda säkra identitetsfunktioner för appar som [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Övervaka aktivt för misstänkta aktiviteter med hjälp av Azure AD Premium-avvikelserapporter och [Azure AD-identitetsskyddsfunktioner](../../active-directory/identity-protection/overview.md).</li></ul> |
|[<br>Fortlöpande säkerhetsövervakning](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Använd Azure [Monitor-loggar](../../log-analytics/log-analytics-queries.md) för bedömning av skadlig programvara för att rapportera om statusen för skydd mot skadlig kod i infrastrukturen.</li><li>Använd [uppdateringsutvärdering](../../automation/automation-update-management.md) för att fastställa den totala exponeringen för potentiella säkerhetsproblem och om eller hur viktiga dessa uppdateringar är för din miljö.</li><li>[Identitet och åtkomst](../../security-center/security-center-monitoring.md) ger dig en översikt över </li><ul><li>Tillstånd för användarens identitet.</li><li>antal misslyckade försök att logga in,</li><li> användarens konto som användes under dessa försök, konton som var utelåst</li> <li>konton med ändrat eller återställa lösenord </li><li>Antal konton som är inloggade.</li></ul></ul> |
| [<br>Identifieringsfunktioner för Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Använd [identifieringsfunktioner](../../security-center/security-center-alerts-overview.md#detect-threats)för att identifiera aktiva hot som är inriktade på dina Microsoft Azure-resurser.</li><li>Använd [integrerad hotinformation](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) som söker efter kända dåliga aktörer genom att utnyttja global hotinformation från Microsofts produkter och tjänster, [Microsoft Digital Crimes Unit (DCU),](https://www.microsoft.com/trustcenter/security/cybercrime) [Microsoft Security Response Center (MSRC)](response-center.md)och externa flöden.</li><li>Använd [beteendeanalys](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) som tillämpar kända mönster för att upptäcka skadligt beteende. </li><li>Använd [Avvikelseidentifiering](https://msdn.microsoft.com/library/azure/dn913096.aspx) som använder statistisk profilering för att skapa en historisk baslinje.</li></ul> |
| [<br>Utvecklarverksamhet (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastruktur som kod (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) är en praxis som gör det möjligt att automatisera och validera skapande och rivning av nätverk och virtuella datorer för att hjälpa till med att leverera säkra, stabila programhostingplattformar.</li><li>[Kontinuerlig integrering och distribution](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) driver den pågående sammanslagningen och testningen av kod, vilket leder till att hitta fel tidigt. </li><li>[Hantering av utgivning](https://msdn.microsoft.com/library/vs/alm/release/overview) Hantera automatiska distributioner genom varje steg i pipelinen.</li><li>[Appprestandaövervakning](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) av program som körs, inklusive produktionsmiljöer för programhälsa och kundanvändning, hjälper organisationer att bilda en hypotes och validera eller motbevisa strategier snabbt.</li><li>Med hjälp av [belastningstestning & Automatisk skalning](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) kan vi hitta prestandaproblem i vår app för att förbättra distributionskvaliteten och se till att vår app alltid är uppe eller tillgänglig för att tillgodose företagets behov.</li></ul> |


## <a name="conclusion"></a>Slutsats
Många organisationer har distribuerat och drivit sina molnprogram på Azure. Checklistorna som tillhandahålls belyser flera checklistor som är viktiga och hjälper dig att öka sannolikheten för framgångsrika distributioner och frustrationsfria åtgärder. Vi rekommenderar starkt dessa operativa och strategiska överväganden för dina befintliga och nya programdistributioner på Azure.

## <a name="next-steps"></a>Nästa steg
Mer information om säkerhet finns i följande artiklar:

- [Design och driftsäkerhet](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Planering och åtgärder för Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md).
