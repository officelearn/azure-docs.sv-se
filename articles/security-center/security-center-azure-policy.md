---
title: "Integrering av Azure Security Center-säkerhetsprinciper med Azure Policy | Microsoft Docs"
description: "Det här dokumentet hjälper dig konfigurera integrering av Azure Security Center-säkerhetsprinciper med Azure Policy."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: yurid
ms.openlocfilehash: 977f7c075005efb826e862ee582864b0f6efab38
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="integrate-security-center-security-policies-with-azure-policy"></a>Integrera Security Center-säkerhetsprinciper med Azure Policy
Den här artikeln hjälper dig att konfigurera Azure Security Center-säkerhetsprinciper som tillhandahålls av Azure Policy. 

## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principer i Security Center eller använda [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) för att göra följande:
* Skapa nya principdefinitioner.
* Tilldela principer över hanteringsgrupper som motsvarar en hel organisation eller affärsenhet inom organisationen.
* Övervaka principefterlevnad.

> [!NOTE]
> Azure Policy finns i en begränsad förhandsversion. Gå till [Registrera dig för Azure Policy](https://aka.ms/getpolicy) för att ansluta dig. Om du vill ha mer information om Azure Policy kan du läsa [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (Skapa och hantera principer för att genomdriva efterlevnad).

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer i Security Center. Om du vill ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för prenumerationen eller hanteringsgruppen som innehåller den. Gör följande för att se dina säkerhetsprinciper i Security Center:

1. Logga in på Azure Portal.

2. Välj **Säkerhetsprincip** under **Allmänt** på instrumentpanelen i **Security Center**.

    ![Fönstret för principhantering](./media/security-center-policies/security-center-policies-fig10.png)

3. Markera den prenumeration som du vill aktivera en säkerhetsprincip för.  

4. I avsnittet **Principkomponenter** väljer du **Säkerhetsprincip**.  
    Fönstret **Grundläggande** öppnas.

    ![Principkomponenter](./media/security-center-policies/security-center-policies-fig12.png)

5. Välj **Ta bort** under **Principer och parametrar** bredvid definitionen som du vill ta bort för att ta bort en principdefinition.

6. Klicka på **Spara**.  
    Fönstret **Available Definitions** (Tillgängliga definitioner) öppnas och visar standardprincipen som har tilldelats till Security Center via Azure Policy. 

7. (Valfritt) I fönstret **Available Definitions** (Tillgängliga definitioner) gör du något av följande:

    * Välj plustecken (+) bredvid definitionen för att lägga till en principdefinition.

    ![Tillgängliga principdefinitioner](./media/security-center-policies/security-center-policies-fig11.png)

    * Markera principen för att få en detaljerad förklaring om den.  
    Fönstret **Förhandsvisning** för definitionen öppnas. Den visar en beskrivning av definitionen och en länk till JSON-koden som innehåller strukturen för [principdefinitionen](../azure-policy/policy-definition.md).

    ![Fönstret Förhandsvisning för definitionen](./media/security-center-policies/security-center-policies-fig14.png)

7. När du är färdig med redigeringarna väljer du **Spara**.

## <a name="available-security-policy-definitions"></a>Tillgängliga säkerhetsprincipdefinitioner

Använd följande tabell som referens för att förstå de principdefinitioner som finns tillgängliga i standard-säkerhetsprincipen: 

| Princip | Vad den aktiverade principen gör |
| --- | --- |
| Systemuppdateringar |Hämtar en daglig lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services. Den hämtade listan beror på vilken tjänst som har konfigurerats för de virtuella datorerna och den rekommenderar att saknade uppdateringar tillämpas. I Linux-system använder principen pakethanteringssystemet med distribution för att se vilka paket som har tillgängliga uppdateringar. Den söker också efter säkerhetsuppdateringar och viktiga uppdateringar från virtuella datorer i [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md). |
| Sårbarheter i operativsystem |Analyserar dagligen konfigurationer av operativsystemet för att fastställa problem som kan göra den virtuella datorn sårbar för angrepp. Principen rekommenderar också konfigurationsändringar för att åtgärda dessa sårbarheter. Se [listan med rekommenderade baslinjer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för mer information om vilka konfigurationer som övervakas. (Just nu stöds inte Windows Server 2016 fullt ut.) |
| Slutpunktsskydd |Rekommendationer om att slutpunktsskydd ska installeras på alla virtuella datorer (VM) med Windows så att virus, spionprogram och annan skadlig programvara kan identifieras och tas bort. |
| Diskkryptering |Rekommenderar att aktivera diskkryptering på alla virtuella datorer för att förbättra skydd av data i vila. |
| Nätverkssäkerhetsgrupper |Rekommenderar att [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) ska konfigureras för kontroll av inkommande och utgående trafik till virtuella datorer med offentliga slutpunkter. Nätverkssäkerhetsgrupper som konfigureras för ett undernät ärvs av alla VM-nätverksgränssnitt om inget annat anges. Förutom att kontrollera om en nätverkssäkerhetsgrupp har konfigurerats kontrollerar även den här principen att inkommande säkerhetsregler för att se om det finns några regler som tillåter inkommande trafik. |
| Brandvägg för webbaserade program |Rekommenderar att en brandvägg för webbaserade program konfigureras på virtuella datorer när något av följande stämmer: <ul><li>En [Offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) används och de inkommande säkerhetsreglerna för den associerade nätverkssäkerhetsgruppen är konfigurerad för att tillåta åtkomst till port 80/443.</li><li>En belastningsutjämnad IP-adress används och associerad nätverksbelastning och inkommande NAT-regler (Network Address Translation) är konfigurerade för att tillåta åtkomst till port 80/443. Mer information finns i [Azure Resource Manager-stöd för belastningsutjämnare](../load-balancer/load-balancer-arm.md).</li> |
| Nästa generations brandvägg |Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Security Center identifierar distributioner som nästa generations brandvägg rekommenderas för och du kan sedan göra en virtuell installation. |
| SQL-granskning och hotidentifiering |Rekommenderar att granskning av åtkomst till Azure-databasen aktiveras för lag- och regelefterlevnad, avancerad hotidentifiering och undersökning. |
| SQL-kryptering |Rekommenderar att kryptering i vila ska aktiveras för Azure SQL Database, tillhörande säkerhetskopior och transaktionsloggfiler. Även om intrång sker i dessa data så går dem inte att läsa. |
| Sårbarhetsbedömning |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| Lagringskryptering |Den här funktionen är för närvarande tillgänglig för Azure Blob Storage och Azure Files. När du har aktiverat Kryptering av lagringstjänst kommer endast nya data att krypteras. Befintliga filer i lagringskontot förblir okrypterade. |
| JIT-nätverksåtkomst |När JIT-nätverksåtkomst (Just-In-Time) är aktiverat begränsar Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en gruppregel för nätverkssäkerhet. Du väljer vilka portar på den virtuella datorn som inkommande trafik ska begränsas till. Mer information finns i [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Hantera åtkomsten till virtuella datorer med Just-In-Time). |


## <a name="next-steps"></a>Nästa steg
I den här artikeln har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande artiklar kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Få svar på vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
