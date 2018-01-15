---
title: "Ange säkerhetsprinciper i Azure Security Center | Microsoft Docs"
description: "Den här artikeln hjälper dig att ställa in säkerhetsprinciper i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2018
ms.author: yurid
ms.openlocfilehash: 4d1a30b046c0c398d934291a907af891e9ac7fdf
ms.sourcegitcommit: 719dd33d18cc25c719572cd67e4e6bce29b1d6e7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="set-security-policies-in-azure-security-center"></a>Ange säkerhetsprinciper i Azure Security Center
Den här artikeln hjälper dig att ställa in säkerhetsprinciper i Security Center.

## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. I Security Center kan du redigera principer och övervaka efterlevnaden av principer.

> [!NOTE]
> Nu kan du även utöka Security Center-principerna med hjälp av Azure Policy, som nu finns i en begränsad förhandsversion. Gå till [Registrera dig för Azure Policy](https://aka.ms/getpolicy) för att ansluta dig till förhandsversionen. Mer information finns i [Integrera Security Center-säkerhetsprinciper med Azure Policy](security-center-azure-policy.md).

Säkerhetskraven för de resurser som används för utveckling eller testning kan skilja sig från säkerhetskraven för resurser som används för program i produktionen. Det kanske även krävs en högre säkerhetsnivå för sådant som det finns lagar om, till exempel personligt identifierbar information. Säkerhetsprinciperna i Azure Security Center innehåller säkerhetsrekommendationer och möjlighet till övervakning som gör att du kan upptäcka potentiella säkerhetsrisker och avhjälpa hot. Mer information om hur du avgör vilket alternativ som passar dig bäst finns i [Planering- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md).

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer i Security Center. För att få ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för den aktuella prenumerationen. Gör följande för att konfigurera dina säkerhetsprinciper i Security Center:

1. Logga in på Azure Portal.

2. Välj **Säkerhetsprincip** under **Allmänt** på instrumentpanelen i **Security Center**.

3. Markera den prenumeration som du vill aktivera en säkerhetsprincip för.

4. I avsnittet **Principkomponenter** väljer du **Säkerhetsprincip**.  
    Det här är standardprincipen som har tilldelats av Security Center. Du kan aktivera eller inaktivera de tillgängliga säkerhetsrekommendationerna.

5. När du är färdig med redigeringarna väljer du **Spara**.

## <a name="available-security-policy-definitions"></a>Tillgängliga säkerhetsprincipdefinitioner

Använd följande tabell som referens för att förstå de principdefinitioner som finns tillgängliga i standard-säkerhetsprincipen:

| Princip | Vad principen gör |
| --- | --- |
| Systemuppdateringar |Hämtar en daglig lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services. Den hämtade listan beror på vilken tjänst som har konfigurerats för de virtuella datorerna och den rekommenderar att saknade uppdateringar tillämpas. I Linux-system använder principen pakethanteringssystemet med distribution för att se vilka paket som har tillgängliga uppdateringar. Den söker också efter säkerhetsuppdateringar och viktiga uppdateringar från virtuella datorer i [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md). |
| Säkerhetskonfigurationer |Analyserar dagligen konfigurationer av operativsystemet för att fastställa problem som kan göra den virtuella datorn sårbar för angrepp. Principen rekommenderar också konfigurationsändringar för att åtgärda dessa sårbarheter. Se [listan med rekommenderade baslinjer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för mer information om vilka konfigurationer som övervakas. (Just nu stöds inte Windows Server 2016 fullt ut.) |
| Slutpunktsskydd |Rekommendationer om att slutpunktsskydd ska installeras på alla virtuella datorer (VM) med Windows så att virus, spionprogram och annan skadlig programvara kan identifieras och tas bort. |
| Diskkryptering |Rekommenderar att aktivera diskkryptering på alla virtuella datorer för att förbättra skydd av data i vila. |
| Nätverkssäkerhetsgrupper |Rekommenderar att [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) ska konfigureras för kontroll av inkommande och utgående trafik till virtuella datorer med offentliga slutpunkter. Nätverkssäkerhetsgrupper som konfigureras för ett undernät ärvs av alla VM-nätverksgränssnitt om inget annat anges. Förutom att kontrollera om en nätverkssäkerhetsgrupp har konfigurerats kontrollerar även den här principen att inkommande säkerhetsregler för att se om det finns några regler som tillåter inkommande trafik. |
| Brandvägg för webbaserade program |Rekommenderar att en brandvägg för webbaserade program konfigureras på virtuella datorer när något av följande stämmer: <ul><li>En [Offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) används och de inkommande säkerhetsreglerna för den associerade nätverkssäkerhetsgruppen är konfigurerad för att tillåta åtkomst till port 80/443.</li><li>En belastningsutjämnad IP-adress används och associerad nätverksbelastning och inkommande NAT-regler (Network Address Translation) är konfigurerade för att tillåta åtkomst till port 80/443. Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](../load-balancer/load-balancer-arm.md).</li> |
| Nästa generations brandvägg |Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Security Center identifierar distributioner som nästa generations brandvägg rekommenderas för och du kan sedan göra en virtuell installation. |
| SQL-granskning och hotidentifiering |Rekommenderar att granskning av åtkomst till SQL-databasen aktiveras för både lag- och regelefterlevnad, avancerad hotidentifiering och undersökning. |
| SQL-kryptering |Rekommenderar att kryptering i vila ska aktiveras för SQL Database, tillhörande säkerhetskopior och transaktionsloggfiler. Även om intrång sker i dessa data så går dem inte att läsa. |
| Sårbarhetsbedömning |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| Lagringskryptering |Den här funktionen är för närvarande tillgänglig för blobar och Azure Files. När du har aktiverat Kryptering av lagringstjänst kommer endast nya data att krypteras. Befintliga filer i lagringskontot förblir okrypterade. |
| JIT-nätverksåtkomst |När JIT-nätverksåtkomst (Just-In-Time) är aktiverat begränsar Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en gruppregel för nätverkssäkerhet. Du väljer vilka portar på den virtuella datorn som inkommande trafik ska begränsas till. Mer information finns i [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Hantera åtkomsten till virtuella datorer med Just-In-Time). |


## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
