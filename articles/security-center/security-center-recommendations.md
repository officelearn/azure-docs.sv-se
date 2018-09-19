---
title: Hantera säkerhetsrekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet vägleder dig genom hur rekommendationerna i Azure Security Center hjälpa dig att skydda dina Azure-resurser och uppfyller säkerhetsprinciper.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rkarlin
ms.openlocfilehash: 3af84e0659d1d62079c239ad6e650d31c9f9a6f1
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46125375"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Hantera säkerhetsrekommendationer i Azure Security Center
Det här dokumentet vägleder dig genom hur du använder rekommendationer i Azure Security Center för att hjälpa dig att skydda dina Azure-resurser.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="what-are-security-recommendations"></a>Vad är säkerhetsrekommendationer?
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

## <a name="implementing-security-recommendations"></a>Implementera säkerhetsrekommendationer
### <a name="set-recommendations"></a>Set-rekommendationer
I [ange säkerhetsprinciper i Azure Security Center](security-center-policies.md), lär du dig att:

* Konfigurera säkerhetsprinciper.
* Aktivera insamling av data.
* Välj vilka rekommendationer för att se som en del av din säkerhetsprincip.

Aktuella rekommendationer principcentret runt systemuppdateringar, baslinjeregler, program, [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) på undernät och nätverksgränssnitt, SQL database-granskning, SQL database transparent datakryptering, och brandväggar för webbaserade program.  [Ange säkerhetsprinciper](security-center-policies.md) innehåller en beskrivning av varje rekommendation alternativ.

### <a name="monitor-recommendations"></a>Övervakare för rekommendationer
När du har angett en säkerhetsprincip analyseras säkerhetstillståndet för resurserna i Azure i Security Center för upptäckt av eventuella säkerhetsrisker. Den **rekommendationer** panelen **översikt** gör att du vet att det totala antalet rekommendationer identifierades av Security Center.

![Panel för rekommendationer][1]

Om du vill se information om varje rekommendation, Välj den **rekommendationer panelen** under **översikt**. **Rekommendationer** öppnas.

![Filtrera rekommendationer][2]

Du kan filtrera rekommendationer. Om du vill filtrera rekommendationer, Välj **Filter** på den **rekommendationer** bladet. Den **Filter** bladet öppnas och du kan välja allvarlighetsgrad och status du vill se.

Rekommendationerna visas i tabellformat där varje rad motsvarar en viss rekommendation. Kolumner i den här tabellen är:

* **Beskrivning av**: förklarar rekommendationen och vad som behöver göras för att åtgärda detta.
* **RESURSEN**: Visar en lista över de resurser som den här rekommendationen gäller.
* **TILLSTÅND**: beskriver det aktuella tillståndet för rekommendationen:
  * **Öppna**: rekommendationen har inte utförts än.
  * **Pågår**: rekommendationen håller på att resurserna och ingen åtgärd krävs av dig.
  * **Löst**: rekommendationen har redan slutförts (i det här fallet raden är nedtonat).
* **SEVERITY (ALLVARLIGHETSGRAD)**: Här visas hur viktig rekommendationen är.
  * **Hög**: en säkerhetsrisk finns en viktig resurs (till exempel ett program, en virtuell dator eller en nätverkssäkerhetsgrupp) som måste åtgärdas.
  * **Medel**: det finns ett säkerhetsproblem och icke-kritiska eller ytterligare åtgärder som krävs för att eliminera den eller för att slutföra en process.
  * **Låg**: det finns en säkerhetsrisk som bör åtgärdas, men inte kräver omedelbar åtgärd. (Låg rekommendationer är inte visas som standard, men du kan filtrera fram om du vill se dem.)

Använd tabellen nedan som referens för att förstå rekommendationerna som är tillgängliga och vad var och en gör om du använder den.

> [!NOTE]
> Du vill förstå den [klassiska och Resource Manager-distributionsmodeller](../azure-classic-rm.md) för Azure-resurser.
>
>

| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera insamling av data för prenumerationer](security-center-enable-data-collection.md) |Rekommenderar att du aktiverar datainsamling i säkerhetsprincipen för var och en av dina prenumerationer och alla Azure-datorer (VM) och icke-Azure-datorer. |
| [Åtgärda säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md) |Rekommenderar att du justera operativsystem enligt rekommenderade security konfigurationsregler, till exempel, lösenord ska sparas. |
| [Tillämpa systemuppdateringar](security-center-apply-system-updates.md) |Rekommenderar att du distribuerar saknas systemsäkerhet och viktiga uppdateringar till Windows och Linux-datorer och datorer. |
| [Tillämpa en Just-In-Time nätverksåtkomstkontroll](security-center-just-in-time.md) | Rekommenderar att du tillämpar just-in-time-åtkomst till virtuell dator. Just in-time-funktionen är tillgänglig för standardnivån i Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). |
| [Starta om datorn efter uppdateringarna](security-center-apply-system-updates.md#reboot-after-system-updates) |Rekommenderar att du startar om en virtuell dator för att slutföra processen med att tillämpa uppdateringar. |
| [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md) |Rekommenderar att du distribuerar en brandvägg för webbaserade program (WAF) för webb-slutpunkter. En WAF-rekommendation visas för valfri offentlig IP-adress (instans nivå IP eller belastningsutjämnade IP-Load) som har en nätverkssäkerhetsgrupp med öppna inkommande webbportar (80,443). </br>Security Center rekommenderar att du etablerar en WAF för att skydda mot hot mot dina webbprogram på virtuella datorer och på App Service Environment. App Service Environment (ASE) är en [Premium](https://azure.microsoft.com/pricing/details/app-service/) tjänsten Prenumerationsalternativ i Azure App Service som innehåller en helt isolerad och dedikerad miljö för säker körning av Azure App Service-appar. Mer information om ASE finns i [dokumentation för App Service Environment](../app-service/environment/intro.md).</br>Du kan skydda flera webbprogram i Security Center genom att lägga till dessa program till din befintliga WAF-distributioner. |
| [Slutför programskydd](security-center-add-web-application-firewall.md#finalize-application-protection) |Om du vill slutföra konfigurationen av en WAF måste trafik dirigeras om till den WAF-installationen. Efter den här rekommendationen Slutför de nödvändiga ändringarna. |
| [Lägga till en nästa generations brandvägg](security-center-add-next-generation-firewall.md) |Rekommenderar att du lägger till en nästa Generation Firewall (NGFW) från en Microsoft-partner att öka din säkerhetsskydd. |
| [Dirigera trafiken endast via NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Rekommenderar att du konfigurerar nätverkssäkerhetsgrupper (NSG) Nätverkssäkerhetsregler som tvingar inkommande trafik till den virtuella datorn via din NGFW. |
| [Installera slutpunktsskydd](security-center-install-endpoint-protection.md) |Rekommenderar att du etablerar program mot skadlig kod för virtuella datorer (endast virtuella Windows-datorer). |
| [Aktivera Nätverkssäkerhetsgrupper på undernät eller virtuella datorer](security-center-enable-network-security-groups.md) |Rekommenderar att du aktiverar NSG: er på undernät eller virtuella datorer. |
| [Begränsa åtkomst via slutpunkt mot Internet](security-center-restrict-access-through-internet-facing-endpoints.md) |Rekommenderar att du konfigurerar regler för inkommande trafik för NSG: er. |
| [Aktivera granskning och hotidentifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hotidentifiering för Azure SQL-servrar. (Endast azure SQL-tjänsten. Omfattar inte SQL som körs på dina virtuella datorer.) |
| [Aktivera granskning och hotidentifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hotidentifiering för Azure SQL-databaser. (Endast azure SQL-tjänsten. Omfattar inte SQL som körs på dina virtuella datorer.) |
| [Aktivera Transparent datakryptering på SQL-databaser](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser. (Endast azure SQL-tjänst.) |
| [Aktivera VM-Agent](security-center-enable-vm-agent.md) |Du kan se vilka virtuella datorer som kräver VM-agenten. VM-agenten måste installeras på virtuella datorer för att etablera korrigeringsgenomsökning, baslinjegenomsökning och program mot skadlig kod. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Artikeln [VM Agent and Extensions – Part 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (VM-agenter och tillägg – del 2) innehåller information om hur VM-agenten ska installeras. |
| [Tillämpa diskkryptering](security-center-apply-disk-encryption.md) |Rekommenderar att krypterar dina VM-diskar med Azure Disk Encryption (virtuella Windows- och Linux-datorer). Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn. |
| [Ange säkerhetskontaktinformation](security-center-provide-security-contact-details.md) |Rekommenderar att du anger säkerhet kontaktinformation för var och en av dina prenumerationer. Kontaktinformation är ett e-postadress och telefonnummer tal. Informationen används för att kontakta dig om vårt säkerhetsteam skulle upptäcka att dina resurser äventyras. |
| [Uppdatera OS-versionen](security-center-update-os-version.md) |Rekommenderar att du uppdaterar versionen av operativsystemet (OS) för din molntjänst till den senaste versionen som är tillgängliga för din OS-familj.  Mer information om molntjänster finns det [översikt över Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md) |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| [Åtgärda sårbarheter](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Gör att du kan visa system- och säkerhetssårbarheter som identifieras av sårbarhetsbedömningen som är installerad på den virtuella datorn. |
| [Aktivera kryptering för Azure Storage-konto](security-center-enable-encryption-for-storage-account.md) | Rekommenderar att du aktiverar Azure Storage Service Encryption för vilande data. Storage Service Encryption (SSE) fungerar genom att kryptera data när den skrivs till Azure-lagring och dekrypterar före hämtning. SSE är för närvarande endast tillgänglig för Azure Blob service och kan användas för blockblobbar, sidblobbar och tilläggsblobbar. Mer information finns i [Storage Service Encryption för vilande data](../storage/common/storage-service-encryption.md).</br>SSE stöds bara på Resource Manager-lagringskonton. |
| [Aktivera anpassningsbara program kontroller](security-center-adaptive-application.md) | Rekommenderar att du installerar anpassningsbara programkontroller på dina virtuella Windows-datorer. Den här funktionen är tillgänglig på nivån Standard för Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). |
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
| Ange mer än en ägare av din prenumeration | Rekommenderar att du anger mer än en prenumerationsägare för att du har administratörsåtkomst. |
| Ange upp till 3 ägare av din prenumeration | Rekommenderar att du anger färre än 3 prenumerationsägare för att minska risken för intrång genom en komprometterad ägare. |
| Aktivera MFA för konton med ägarbehörigheter för din prenumeration | Rekommenderar att du aktiverar Multi-Factor Authentication (MFA) för alla prenumerationskonton med administratörsbehörighet för att förhindra att överträdelser görs avseende konton och resurser. |
| Aktivera MFA för konton med skrivbehörigheter för din prenumeration | Rekommenderar att du aktiverar Multi-Factor Authentication (MFA) för alla prenumerationskonton med skrivbehörighet för att förhindra att överträdelser görs avseende konton och resurser. |
| Aktivera MFA för konton med läsbehörigheter för din prenumeration | Rekommenderar att du aktiverar Multi-Factor Authentication (MFA) för alla prenumerationskonton med läsbehörighet för att förhindra att överträdelser görs avseende konton och resurser. |
| Ta bort externa konton med läsbehörigheter från prenumerationen | Rekommenderar att du tar bort externa konton med läsprivilegier från din prenumeration för att förhindra oövervakad åtkomst. |
| Ta bort externa konton med skrivbehörigheter från prenumerationen | Rekommenderar att du tar bort externa konton med skrivprivilegier från din prenumeration för att förhindra oövervakad åtkomst. |
| Ta bort externa konton med ägarbehörigheter från prenumerationen | Rekommenderar att du tar bort externa konton med ägarbehörigheter från prenumerationen för att förhindra oövervakad åtkomst. |
| Ta bort inaktuella konton från prenumerationen | Rekommenderar att du tar bort inaktuella konton från dina prenumerationer. |
| Ta bort inaktuella konton med ägarbehörigheter från prenumerationen | Rekommenderar att du tar bort inaktuella konton med ägarbehörigheter från dina prenumerationer. |

### <a name="apply-recommendations"></a>Tillämpa rekommendationer
När du har granskat avgör alla rekommendationer vilken du bör använda först. Vi rekommenderar att du använder risknivån eftersom parametern huvudsakliga för att utvärdera vilka rekommendationer ska tillämpas först.

Välj en rekommendation i tabellen i rekommendationerna ovan och gå igenom den som ett exempel på hur du tillämpar en rekommendation.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet berättade säkerhetsrekommendationer i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar Azure-resursernas hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
