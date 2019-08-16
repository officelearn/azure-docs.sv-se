---
title: Rekommendationer för virtuella datorer i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver Azure Security Center rekommendationer för hur du kan skydda dina virtuella datorer och datorer och dina webbappar och App Service miljöer.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: 798e027ca611905766b1fb8bcdb89cba4aeaf9b2
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531333"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Förstå Azure Security Center resurs rekommendationer


## <a name="recommendations"></a>Rekommendationer
Använd tabellerna nedan som en referens för att hjälpa dig att förstå de tillgängliga rekommendationerna för beräknings-och app service och vad var och en gör om du använder den.

### <a name="computers"></a>Datorer
| Rekommendation | Beskrivning |
| --- | --- |
| Aktivera insamling av data för prenumerationer|Rekommenderar att du aktiverar datainsamling i säkerhetsprincipen för var och en av dina prenumerationer och alla virtuella datorer (VM) i dina prenumerationer. |
| Aktivera kryptering för Azure Storage-kontot| Rekommenderar att du aktiverar Azure Storage tjänst kryptering för vilande data. Kryptering för lagringstjänst (SSE) används för att kryptera data när de skrivs till Azure Storage och dekrypteras innan de kan hämtas. SSE är för närvarande endast tillgängligt för Azure-Blob Service och kan användas för block-blobar, Page blobbar och bifogade blobbar. Mer information finns i [kryptering för lagringstjänst för data i vila](../storage/common/storage-service-encryption.md).</br>SSE stöds endast på Resource Manager-lagringsenheter. Klassiska lagrings konton stöds för närvarande inte. Information om de klassiska distributions modellerna för och Resource Manager finns i distributions modeller för Azure. |
| Åtgärda säkerhetskonfigurationerna|Rekommenderar att du justerar dina OS-konfigurationer med rekommenderade säkerhets konfigurations regler, t. ex. Tillåt inte att lösen ord sparas. |
| Tillämpa systemuppdateringar |Rekommenderar att du distribuerar systemsäkerhet och viktiga uppdateringar för virtuella datorer som saknas. |
| Tillämpa en just-in-time-nätverksåtkomstkontroll| Rekommenderar att du använder just-in-Time-åtkomst för virtuella datorer. Funktionen just-in-Time är i för hands version och är tillgänglig på standard Security Centers nivån. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). |
| Starta om efter systemuppdateringar|Rekommenderar att du startar om en virtuell dator för att slutföra processen med att tillämpa uppdateringar. |
| Installera Endpoint Protection|Rekommenderar att du etablerar program mot skadlig kod för virtuella datorer (endast virtuella Windows-datorer). |
| Aktivera virtuell datoragent |Du kan se vilka virtuella datorer som kräver VM-agenten. VM-agenten måste installeras på virtuella datorer för att etablera korrigeringsgenomsökning, baslinjegenomsökning och program mot skadlig kod. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Artikeln [VM Agent and Extensions – Part 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (VM-agenter och tillägg – del 2) innehåller information om hur VM-agenten ska installeras. |
| Tillämpa diskkryptering|Rekommenderar att krypterar dina VM-diskar med Azure Disk Encryption (virtuella Windows- och Linux-datorer). Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn. |
| Uppdatera OS-versionen|Rekommenderar att du uppdaterar operativ system versionen (OS) för din moln tjänst till den senaste versionen som är tillgänglig för din OS-familj.  Mer information om Cloud Services finns i Översikt över Cloud Services. |
| Sårbarhetsbedömning inte installerad|Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| Åtgärda sårbarheter) |Gör att du kan visa system- och säkerhetssårbarheter som identifieras av sårbarhetsbedömningen som är installerad på den virtuella datorn. |

### App Services<a name="app-services"></a>
| Rekommendation | Beskrivning |
| --- | --- |
| App Service bör endast vara tillgängligt via HTTPS | Rekommenderar att du begränsar åtkomsten till App Service över HTTPS. |
| Webbsockets bör inaktiveras för webbprogram| Rekommenderar att du noga läser igenom användningen av Web Sockets i webb program.  Web Sockets-protokollet är utsatt för olika typer av säkerhetshot. |
| Använd anpassade domäner för ditt webbprogram | Rekommenderar att du använder anpassade domäner för att skydda ett webb program från vanliga attacker som nätfiske och andra DNS-relaterade attacker. |
| Konfigurera IP-begränsningar för webbprogram | Rekommenderar att du definierar en lista med IP-adresser som får åtkomst till ditt program.  Användning av IP-restriktioner skyddar du webbappar mot vanliga attacker. |
| Tillåt inte alla ('* ') resurser för att komma åt ditt program | Rekommenderar att du inte anger WEBSITE_LOAD_CERTIFICATES-parametern till *. Om du anger parametern till*, innebär det att alla certifikat kommer att läsas in i dina webbappars personliga certifikat arkiv.  Detta kan leda till missbruk av principen om lägsta behörighet eftersom det är osannolikt att webbplatsen behöver åtkomst till alla certifikat vid körning. |
| CORS bör inte tillåta alla resurser åtkomst till ditt program | Rekommenderar att du endast tillåter att nödvändiga domäner interagerar med ditt webb program. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner får åtkomst till ditt webbprogram. |
| Använd senast stöds .NET Framework för webbprogram | Rekommenderar att du använder den senaste .NET Framework versionen för de senaste säkerhets klasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| Använd den senaste Java versionen för webbprogram | Rekommenderar att du använder den senaste Java-versionen för de senaste säkerhets klasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| Använd den senaste PHP versionen för webbprogram | Rekommenderar att du använder den senaste PHP-versionen för de senaste säkerhets klasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md) |Rekommenderar att du distribuerar en brand vägg för webbaserade program (WAF) för webb slut punkter. En WAF-rekommendation visas för alla offentliga IP-adresser (antingen IP-adresser på instans nivå eller belastningsutjämnade IP) som har en associerad nätverks säkerhets grupp med öppna inkommande webb portar (80 443).</br></br>Security Center rekommenderar att du etablerar ett WAF för att skydda mot attacker som riktar sig mot dina webb program på virtuella datorer och på App Service-miljön. En App Service-miljön (ASE) är ett [Premium](https://azure.microsoft.com/pricing/details/app-service/) service-alternativ för Azure App Service som ger en helt isolerad och dedikerad miljö för säker körning av Azure App Service appar. Mer information om ASE finns i App Service-miljön- [dokumentationen](../app-service/environment/intro.md).</br></br>Du kan skydda flera webb program i Security Center genom att lägga till dessa program i dina befintliga WAF-distributioner. |
| [Slutför programskydd](security-center-add-web-application-firewall.md#finalize-application-protection) |För att slutföra konfigurationen av en WAF, måste trafiken dirigeras om till WAF-enheten. Efter den här rekommendationen slutförs de nödvändiga konfigurations ändringarna. |
| Använd den senaste Node.js versionen för webbprogram | Rekommenderar att du använder den senaste versionen av Node. js för de senaste säkerhets klasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| CORS bör inte bevilja alla resurser att få åtkomst till din Funktionsapp | Rekommenderar att du endast tillåter att nödvändiga domäner interagerar med ditt webb program. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner åtkomst till ditt funktionsprogram. |
| Använd anpassade domäner för Funktionsappen | Rekommenderar att du använder anpassade domäner för att skydda en Function-app från vanliga attacker som nätfiske och andra DNS-relaterade attacker. |
| Konfigurera IP-begränsningar för Funktionsappen | Rekommenderar att du definierar en lista med IP-adresser som får åtkomst till ditt program. Användning av IP-restriktioner skyddar en funktionsapp från vanliga attacker. |
| Funktionen App bör enbart vara åtkomliga via HTTPS | Rekommenderar att du begränsar åtkomsten till funktions appar över HTTPS. |
| Du bör stänga av fjärrfelsökning för funktionsappen | Rekommenderar att du inaktiverar fel sökning för Funktionsapp om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp. |
| Webbsockets bör inaktiveras för Funktionsappen | Rekommenderar att du noga läser igenom användningen av Web Sockets i Function Apps. Web Sockets-protokollet är utsatt för olika typer av säkerhetshot. |


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Skydda datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
