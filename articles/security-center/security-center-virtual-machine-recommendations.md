---
title: Rekommendationer för virtuella datorer i Azure Security Center | Microsoft Docs
description: Det här dokumentet beskriver Azure Security Center-rekommendationer för hur du kan skydda dina virtuella datorer och datorer och web apps och App Service-miljöer.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2018
ms.author: rkarlin
ms.openlocfilehash: 52ca063a2e6345f80370c64b376ff09d72643ccc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230622"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Förstå rekommendationer för Azure Security Center-resurs


## <a name="recommendations"></a>Rekommendationer
Använd tabellen nedan som referens för att förstå beräkningarna som är tillgängliga och rekommendationer och vad var och en gör om du använder den för App services.

### <a name="computers"></a>Datorer
| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera insamling av data för prenumerationer](security-center-enable-data-collection.md) |Rekommenderar att du aktiverar datainsamling i säkerhetsprincipen för var och en av dina prenumerationer och alla virtuella datorer (VM) i dina prenumerationer. |
| [Aktivera kryptering för Azure Storage-konto](security-center-enable-encryption-for-storage-account.md) | Rekommenderar att du aktiverar Azure Storage Service Encryption för vilande data. Storage Service Encryption (SSE) fungerar genom att kryptera data när den skrivs till Azure-lagring och dekrypterar före hämtning. SSE är för närvarande endast tillgänglig för Azure Blob service och kan användas för blockblobbar, sidblobbar och tilläggsblobbar. Mer information finns i [Storage Service Encryption för vilande data](../storage/common/storage-service-encryption.md).</br>SSE stöds bara på Resource Manager-lagringskonton. Klassiska lagringskonton stöds inte för närvarande. Information om klassiska och Resource Manager distributionsmodellerna finns i [Azures distributionsmodeller](../azure-classic-rm.md). |
| [Åtgärda säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md) |Rekommenderar att du konfigurerar operativsystem enligt rekommenderade security konfigurationsregler, t.ex. tillåter inte att spara lösenord. |
| [Tillämpa systemuppdateringar](security-center-apply-system-updates.md) |Rekommenderar att du distribuerar systemsäkerhet och viktiga uppdateringar för virtuella datorer som saknas. |
| [Tillämpa en Just-In-Time nätverksåtkomstkontroll](security-center-just-in-time.md) | Rekommenderar att du tillämpar just-in-time-åtkomst till virtuell dator. Den bara i tid funktionen är en förhandsversion och är tillgängliga på standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). |
| [Starta om datorn efter uppdateringarna](security-center-apply-system-updates.md#reboot-after-system-updates) |Rekommenderar att du startar om en virtuell dator för att slutföra processen med att tillämpa uppdateringar. |
| [Installera slutpunktsskydd](security-center-install-endpoint-protection.md) |Rekommenderar att du etablerar program mot skadlig kod för virtuella datorer (endast virtuella Windows-datorer). |
| [Aktivera VM-Agent](security-center-enable-vm-agent.md) |Du kan se vilka virtuella datorer som kräver VM-agenten. VM-agenten måste installeras på virtuella datorer för att etablera korrigeringsgenomsökning, baslinjegenomsökning och program mot skadlig kod. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Artikeln [VM Agent and Extensions – Part 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (VM-agenter och tillägg – del 2) innehåller information om hur VM-agenten ska installeras. |
| [Tillämpa diskkryptering](security-center-apply-disk-encryption.md) |Rekommenderar att krypterar dina VM-diskar med Azure Disk Encryption (virtuella Windows- och Linux-datorer). Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn. |
| [Uppdatera OS-versionen](security-center-update-os-version.md) |Rekommenderar att du uppdaterar versionen av operativsystemet (OS) för din molntjänst till den senaste versionen som är tillgängliga för din OS-familj.  Mer information om molntjänster finns det [översikt över Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md) |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| [Åtgärda sårbarheter](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Gör att du kan visa system- och säkerhetssårbarheter som identifieras av sårbarhetsbedömningen som är installerad på den virtuella datorn. |

### <a name="app-services"></a>App Services
| Rekommendation | Beskrivning |
| --- | --- |
| App Service bör endast vara tillgängligt via HTTPS | Rekommenderar att du begränsar åtkomst till App Service via HTTPS endast. |
| Webbsockets bör inaktiveras för webbprogram| Rekommenderar att du noggrant läser användningen av Web Sockets i webbappar.  Web Sockets-protokollet är utsatt för olika typer av säkerhetshot. |
| Använd anpassade domäner för ditt webbprogram | Rekommenderar att du använder anpassade domäner för att skydda ett webbprogram mot vanliga attacker som nätfiske och andra DNS-relaterade attacker. |
| Konfigurera IP-begränsningar för webbprogram | Du bör definiera en lista över IP-adresser som ska kunna komma åt ditt program.  Användning av IP-restriktioner skyddar du webbappar mot vanliga attacker. |
| Tillåt inte alla ('* ') resurser för att komma åt ditt program | Rekommenderar att du inte anger parametern website_load_certificates. den ”*”. Ställa in parametern '*”innebär att alla certifikat läses till din web program personliga certifikatarkiv.  Detta kan leda till missbruk av principen om lägsta behörighet eftersom det är osannolikt att webbplatsen behöver åtkomst till alla certifikat vid körning. |
| CORS bör inte bevilja alla resurser åtkomst till ditt program | Rekommenderar att du tillåter endast nödvändiga domäner att interagera med ditt webbprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner får åtkomst till ditt webbprogram. |
| Använd senast stöds .NET Framework för webbprogram | Rekommenderar att du använder den senaste versionen av .NET Framework för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| Använd den senaste Java versionen för webbprogram | Rekommenderar att du använder den senaste Java-versionen för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| Använd den senaste PHP versionen för webbprogram | Rekommenderar att du använder den senaste PHP-versionen för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md) |Rekommenderar att du distribuerar en brandvägg för webbaserade program (WAF) för webb-slutpunkter. En WAF-rekommendation visas för valfri offentlig IP-adress (instans nivå IP eller belastningsutjämnade IP-Load) som har en nätverkssäkerhetsgrupp med öppna inkommande webbportar (80,443).</br></br>Security Center rekommenderar att du etablerar en WAF för att skydda mot hot mot dina webbprogram på virtuella datorer och på App Service Environment. App Service Environment (ASE) är en [Premium](https://azure.microsoft.com/pricing/details/app-service/) tjänsten Prenumerationsalternativ i Azure App Service som innehåller en helt isolerad och dedikerad miljö för säker körning av Azure App Service-appar. Mer information om ASE finns i [dokumentation för App Service Environment](../app-service/environment/intro.md).</br></br>Du kan skydda flera webbprogram i Security Center genom att lägga till dessa program till din befintliga WAF-distributioner. |
| [Slutför programskydd](security-center-add-web-application-firewall.md#finalize-application-protection) |Om du vill slutföra konfigurationen av en WAF måste trafik dirigeras om till den WAF-installationen. Efter den här rekommendationen Slutför de nödvändiga ändringarna. |
| Använd den senaste Node.js versionen för webbprogram | Rekommenderar att du använder den senaste versionen av Node.js för de senaste säkerhetsklasserna. Använder äldre klasser och typer kan du göra ditt program sårbart. |
| CORS bör inte bevilja alla resurser att få åtkomst till din Funktionsapp | Rekommenderar att du tillåter endast nödvändiga domäner att interagera med ditt webbprogram. Mellan ursprung ska sharing (CORS) inte tillåta alla domäner åtkomst till ditt funktionsprogram. |
| Använd anpassade domäner för Funktionsappen | Rekommenderar att du använder anpassade domäner för att skydda en funktionsapp från vanliga attacker som nätfiske och andra DNS-relaterade attacker. |
| Konfigurera IP-begränsningar för Funktionsappen | Du bör definiera en lista över IP-adresser som ska kunna komma åt ditt program. Användning av IP-restriktioner skyddar en funktionsapp från vanliga attacker. |
| Funktionen App bör enbart vara åtkomliga via HTTPS | Rekommenderar att du begränsar åtkomst till funktionsappar via HTTPS endast. |
| Fjärrfelsökning bör stängas av för Funktionsappen | Rekommenderar att du stänger av felsökning för Funktionsappen om du inte längre behöver använda den. Fjärrfelsökning kräver att inkommande portar öppnas på en Funktionsapp. |
| Webbsockets bör inaktiveras för Funktionsappen | Rekommenderar att du noggrant läser användningen av Web Sockets i Funktionsappar. Web Sockets-protokollet är utsatt för olika typer av säkerhetshot. |


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om rekommendationer som gäller för andra Azure-resurstyper finns i:

* [Övervaka identitet och åtkomst i Azure Security Center](security-center-identity-access.md)
* [Skydda nätverket i Azure Security Center](security-center-network-recommendations.md)
* [Skydda din Azure SQL-tjänst i Azure Security Center](security-center-sql-service-recommendations.md)

I följande avsnitt kan du lära dig mer om Security Center:

* [Skydda dina datorer och program i Azure Security Center](security-center-virtual-machine-protection.md)
* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
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
