---
title: Hantera säkerhetsrekommendationer i Azure Security Center | Microsoft Docs
description: Det här dokumentet vägleder dig igenom hur i Azure Security Center hjälper dig att skydda dina Azure-resurser och vara kompatibla med säkerhetsprinciper.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 72070f46309adb526901192752fe421a3846398b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34364258"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Hantera säkerhetsrekommendationer i Azure Security Center
Det här dokumentet vägleder dig igenom hur du använder rekommendationer i Azure Security Center som hjälper dig att skydda dina Azure-resurser.

> [!NOTE]
> I det här dokumentet beskrivs tjänsten genom en exempeldistribution.  Det här dokumentet är inte en stegvis guide.
>
>

## <a name="what-are-security-recommendations"></a>Vad är säkerhetsrekommendationer?
Security Center analyserar regelbundet säkerhetstillståndet hos dina Azure-resurser. När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs.

## <a name="implementing-security-recommendations"></a>Utföra säkerhetsrekommendationerna
### <a name="set-recommendations"></a>Set-rekommendationer
I [ställa in säkerhetsprinciper i Azure Security Center](security-center-policies.md), du lär dig hur du:

* Konfigurera säkerhetsprinciper.
* Aktivera insamling av data.
* Välj vilka rekommendationer för att se som en del av din säkerhetsprincip.

Aktuella rekommendationer principcenter runt systemuppdateringar, baslinjeregler, program mot skadlig kod, [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md) på undernät och nätverksgränssnitt, SQL database auditing, SQL database transparent datakryptering och webbprogrammet brandväggar.  [Ställa in säkerhetsprinciper](security-center-policies.md) innehåller en beskrivning av varje rekommendation alternativ.

### <a name="monitor-recommendations"></a>Övervakaren rekommendationer
När du har angett en säkerhetsprincip analyseras säkerhetstillståndet för resurserna i Azure i Security Center för upptäckt av eventuella säkerhetsrisker. Den **rekommendationer** panelen **översikt** bedömer det totala antalet rekommendationer som identifieras av Security Center.

![Rekommendationer sida vid sida][1]

Om du vill se information om varje rekommendation, Välj den **rekommendationer panelen** under **översikt**. **Rekommendationer** öppnas.

![Filtrera rekommendationer][2]

Rekommendationerna visas i tabellformat där varje rad motsvarar en viss rekommendation. Kolumner i den här tabellen är:

* **Beskrivning**: förklarar rekommendationen och vad som måste göras för att åtgärda detta.
* **RESURSEN**: Visar en lista över de resurser som gäller för den här rekommendationen.
* **TILLSTÅND**: beskriver det aktuella tillståndet för rekommendationen:
  * **Öppna**: rekommendationen har inte utförts än.
  * **Pågående**: rekommendationen används för närvarande i resurser och ingen åtgärd krävs av dig.
  * **Matcha**: rekommendationen har redan slutförts (i det här fallet raden är nedtonad).
* **SEVERITY (ALLVARLIGHETSGRAD)**: Här visas hur viktig rekommendationen är.
  * **Hög**: ett säkerhetsproblem finns i en viktig resurs (till exempel ett program, en virtuell dator eller en nätverkssäkerhetsgrupp) som måste åtgärdas.
  * **Medel**: det finns ett säkerhetsproblem och icke-kritiska eller ytterligare åtgärder krävs att eliminera den eller för att slutföra en process.
  * **Låg**: det finns ett säkerhetsproblem som bör åtgärdas, men inte kräver omedelbara uppmärksamhet. (Låg rekommendationer visas inte som standard men du kan filtrera på låg rekommendationer om du vill se dem.)

Använd tabellen nedan som referens för att hjälpa dig att förstå rekommendationerna som är tillgängliga och hur var och en fungerar om du använder den.

> [!NOTE]
> Du vill förstå den [klassisk och Resource Manager distributionsmodellerna](../azure-classic-rm.md) för Azure-resurser.
>
>

| Rekommendation | Beskrivning |
| --- | --- |
| [Aktivera insamling av data för prenumerationer](security-center-enable-data-collection.md) |Rekommenderar att du aktiverar datainsamling i säkerhetsprincipen för var och en av dina prenumerationer och alla virtuella Azure-datorer (VM) och Azure-datorer. |
| [Åtgärda säkerhetskonfigurationer](security-center-remediate-os-vulnerabilities.md) |Rekommenderar att du justera OS-konfigurationer med de rekommenderade konfigurationen säkerhetsreglerna, till exempel, tillåter inte lösenord ska sparas. |
| [Tillämpa systemuppdateringar](security-center-apply-system-updates.md) |Rekommenderar att du distribuerar saknas system säkerhetsuppdateringar och viktiga uppdateringar till Windows och Linux virtuella datorer och datorer. |
| [Tillämpa Just-In-Time nätverk åtkomstkontroll](security-center-just-in-time.md) | Rekommenderar att du installerar just-in-time VM-åtkomst. Den bara i tiden funktionen är i förhandsvisning och finns på standardnivån av Security Center. Mer information om prisalternativen för Security Center finns i [Priser](security-center-pricing.md). |
| [Starta om datorn efter uppdateringarna](security-center-apply-system-updates.md#reboot-after-system-updates) |Rekommenderar att du startar om en virtuell dator för att slutföra processen med att tillämpa uppdateringar. |
| [Lägga till en brandvägg för webbappar](security-center-add-web-application-firewall.md) |Rekommenderar att du distribuerar en brandvägg för webbaserade program (Brandvägg) för web-slutpunkter. En Brandvägg rekommendation visas för alla offentliga Internetriktade IP-adresser (instans nivå IP eller Load belastningsutjämnade IP) som har en nätverkssäkerhetsgrupp med öppna webbplats för inkommande portar (80,443). </br>Security Center rekommenderar att du etablerar en Brandvägg för att skydda mot attacker målobjekt för webbaserade program på virtuella datorer samt på Apptjänst-miljö. En App Service miljö (ASE) är en [Premium](https://azure.microsoft.com/pricing/details/app-service/) service plan alternativet för Azure App Service som tillhandahåller en helt isolerad och dedikerad miljö för Azure App Service-program som körs på ett säkert sätt. Mer information om ASE finns i [dokumentationen till App Service-miljö](../app-service/environment/intro.md).</br>Du kan skydda flera webbprogram i Security Center genom att lägga till dessa program till din befintliga Brandvägg-distributioner. |
| [Slutför programskydd](security-center-add-web-application-firewall.md#finalize-application-protection) |Om du vill slutföra konfigurationen av en Brandvägg måste trafik dirigeras till Brandvägg-enhet. Efter den här rekommendationen är klar nödvändiga ändringar. |
| [Lägga till en nästa generations brandvägg](security-center-add-next-generation-firewall.md) |Rekommenderar att du lägger till en nästa generations Brandvägg från en Microsoft-partner att öka din säkerhetsskydd. |
| [Dirigera trafiken endast via NGFW](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Rekommenderar att du konfigurerar (NSG) regler för nätverkssäkerhetsgrupper som tvingar inkommande trafik till den virtuella datorn via din nästa generations Brandvägg. |
| [Installera slutpunktsskydd](security-center-install-endpoint-protection.md) |Rekommenderar att du etablerar program mot skadlig kod för virtuella datorer (endast virtuella Windows-datorer). |
| [Aktivera Nätverkssäkerhetsgrupper för undernät eller virtuella datorer](security-center-enable-network-security-groups.md) |Rekommenderar att du aktiverar NSG: er på undernät eller virtuella datorer. |
| [Begränsa åtkomst via Internetuppkopplad slutpunkt](security-center-restrict-access-through-internet-facing-endpoints.md) |Rekommenderar att du konfigurerar regler för inkommande trafik för NSG: er. |
| [Aktivera granskning och hotidentifiering på SQL-servrar](security-center-enable-auditing-on-sql-servers.md) |Rekommenderar att du aktiverar granskning och hotidentifiering identifiering för Azure SQL-servrar. (Endast azure SQL-tjänsten. Inkluderar inte SQL som körs på virtuella datorer.) |
| [Aktivera granskning och hotidentifiering på SQL-databaser](security-center-enable-auditing-on-sql-databases.md) |Rekommenderar att du aktiverar granskning och hotidentifiering identifiering för Azure SQL-databaser. (Endast azure SQL-tjänsten. Inkluderar inte SQL som körs på virtuella datorer.) |
| [Aktivera Transparent datakryptering på SQL-databaser](security-center-enable-transparent-data-encryption.md) |Rekommenderar att du aktiverar kryptering för SQL-databaser. (Endast azure SQL-tjänsten.) |
| [Aktivera VM-Agent](security-center-enable-vm-agent.md) |Du kan se vilka virtuella datorer som kräver VM-agenten. VM-agenten måste installeras på virtuella datorer för att etablera korrigering genomsökning baslinjen genomsökning och program mot skadlig kod. VM-agenten installeras som standard för virtuella datorer som distribueras från Azure Marketplace. Artikeln [VM Agent and Extensions – Part 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) (VM-agenter och tillägg – del 2) innehåller information om hur VM-agenten ska installeras. |
| [Tillämpa diskkryptering](security-center-apply-disk-encryption.md) |Rekommenderar att krypterar dina VM-diskar med Azure Disk Encryption (virtuella Windows- och Linux-datorer). Kryptering rekommenderas både för OS- och datavolymer på den virtuella datorn. |
| [Ange säkerhetskontaktinformation](security-center-provide-security-contact-details.md) |Rekommenderar att du ger säkerhet kontaktinformation för var och en av dina prenumerationer. Kontaktinformation är en e-postadress och telefonnummer. Informationen används för att kontakta dig om vår säkerhetsteam hittar att dina resurser komprometteras. |
| [Uppdatera OS-versionen](security-center-update-os-version.md) |Rekommenderar att du uppdaterar versionen av operativsystemet (OS) för din molntjänst till den senaste versionen för OS-familjen.  Mer information om molntjänster finns i [översikt över molntjänster](../cloud-services/cloud-services-choose-me.md). |
| [Sårbarhetsbedömning inte installerad](security-center-vulnerability-assessment-recommendations.md) |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| [Åtgärda sårbarheter](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Gör att du kan visa system- och säkerhetssårbarheter som identifieras av sårbarhetsbedömningen som är installerad på den virtuella datorn. |
| [Aktivera kryptering för Azure Storage-konto](security-center-enable-encryption-for-storage-account.md) | Rekommenderar att du aktiverar Azure Storage Service-kryptering för data i vila. Kryptering för lagring-tjänsten (SSE) fungerar genom att kryptera data när de skrivs till Azure-lagring och dekrypterar före hämtning. SSE är endast tillgänglig för Azure Blob-tjänsten och kan användas för blockblobbar, sidblobbar, och tilläggsblobar. Läs mer i [Lagringstjänstens kryptering av vilande data](../storage/common/storage-service-encryption.md).</br>SSE stöds endast i hanteraren för filserverresurser storage-konton. |

Du kan filtrera och ignorera rekommendationer.

1. Välj **Filter** på den **rekommendationer** bladet. Den **Filter** blad öppnas och du väljer allvarlighetsgrad och status värdena som du vill se.

2. Om du upptäcker att en rekommendation inte är tillämplig, kan du stänga rekommendationen och filtrera bort vyn. Det finns två sätt att avvisa en rekommendation. Ett sätt är att högerklicka på ett objekt och välj sedan **Ignorera**. Den andra är hovra över ett objekt genom att klicka på de tre punkterna till höger och välj sedan **Ignorera**. Du kan visa Avvisad rekommendationer genom att klicka på **Filter**, och sedan välja **avvisat**.

    ![Ignorera rekommendation][3]

### <a name="apply-recommendations"></a>Tillämpa rekommendationer
När du har granskat Bestäm alla rekommendationer som du bör använda först. Vi rekommenderar att du använder allvarlighetsgraden som parametern huvudsakligen för att utvärdera vilka rekommendationer som ska användas först.

Välj en rekommendation i tabellen i rekommendationerna ovan och gå igenom den som ett exempel på hur du har tillämpat en rekommendation.

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
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
