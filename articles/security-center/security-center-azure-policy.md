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
ms.openlocfilehash: 5e07cd6891a5ab04012f819b5f6b9379312e530d
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2017
---
# <a name="set-security-policies-in-security-center-powered-by-azure-policy"></a>Ställ in säkerhetsprinciper i Security Center med hjälp av Azure Policy
Det här dokumentet hjälper dig att konfigurera säkerhetsprinciper i Security Center med hjälp av Azure Policy, genom att guida dig genom de steg som behövs för att utföra den här uppgiften. 


## <a name="how-security-policies-work"></a>Hur fungerar säkerhetsprinciper?
Security Center skapar automatiskt en standardsäkerhetsprincip för var och en av dina Azure-prenumerationer. Du kan redigera principen i Security Center eller använda [Azure Policy](http://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) för att skapa nya principdefinitioner, tilldela principer över hanteringsgrupper (som kan motsvara hela organisationen, en affärsenhet, osv.) samt hantera efterlevnad av principerna.

> [!NOTE]
> Azure Policy finns i en begränsad förhandsversion. Klicka [här](https://aka.ms/getpolicy) om du vill ansluta. Om du vill ha mer information om Azure-principer kan du läsa [Create and manage policies to enforce compliance](http://docs.microsoft.com/en-us/azure/azure-policy/create-manage-policy) (Skapa och hantera principer för att genomdriva efterlevnad).

## <a name="edit-security-policies"></a>Redigera säkerhetsprinciper
Du kan redigera standardsäkerhetsprincipen för var och en av dina Azure-prenumerationer i Security Center. Om du vill ändra en säkerhetsprincip måste du vara ägare, deltagare eller säkerhetsadministratör för den prenumerationen, eller hanteringsgruppen som innehåller den. Logga in på Azure Portal och följ dessa steg för att visa säkerhetsprinciper i Security Center:

1. Klicka på **Säkerhetsprincip** under **Allmänt** i instrumentpanelen i **Security Center**.
2. Markera den prenumeration som du vill aktivera en säkerhetsprincip för.

    ![Principhantering](./media/security-center-policies/security-center-policies-fig10.png)

3. I avsnittet **PRINCIPKOMPONENTER** klickar du på **Säkerhetsprincip**.

    ![Principkomponenter](./media/security-center-policies/security-center-policies-fig12.png)

4. Det här är standardprincipen som har tilldelats till Security Center via Azure Policy. Du kan ta bort objekt som finns under **PRINCIPER OCH PARAMETRAR** eller så kan du lägga till andra principdefinitioner som finns under **TILLGÄNGLIGA ALTERNATIV**. Det gör du genom att bara klicka på plustecknet bredvid definitionens namn.

    ![Principdefinitioner](./media/security-center-policies/security-center-policies-fig11.png)

5. Om du vill ha en mer detaljerad förklaring av principen, klickar du på den så öppnas en sida med informationen och JSON-koden som har strukturen för [principdefinitionen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-policy/#policy-definition-structure):

    ![Json](./media/security-center-policies/security-center-policies-fig14.png)

6. När du är färdig med redigeringarna klickar du på **Spara**.


## <a name="available-security-policy-definitions"></a>Tillgängliga säkerhetsprincipdefinitioner

Använd följande tabell som referens för att förstå de principdefinitioner som finns tillgängliga i standard-säkerhetsprincipen: 

| Princip | När den är aktiverad |
| --- | --- |
| Systemuppdateringar |Hämtar en daglig lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services. Den hämtade listan beror på vilken tjänst som har konfigurerats för den virtuella datorn och rekommenderar att saknade uppdateringar tillämpas. I Linux-system använder principen pakethanteringssystemet med distribution för att se vilka paket som har tillgängliga uppdateringar. Den söker också efter säkerhetsuppdateringar och viktiga uppdateringar från virtuella datorer i [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md). |
| Sårbarheter i operativsystem |Analyserar dagligen konfigurationer av operativsystemet för att fastställa problem som kan göra den virtuella datorn sårbar för angrepp. Principen rekommenderar också konfigurationsändringar för att åtgärda dessa sårbarheter. Se [listan med rekommenderade baslinjer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för mer information om vilka konfigurationer som övervakas. (Just nu stöds inte Windows Server 2016 fullt ut.) |
| Slutpunktsskydd |Rekommendationer går ut om att slutpunktsskydd ska installeras på alla virtuella datorer med Windows så att virus, spionprogram och annan skadlig programvara kan tas bort. |
| Diskkryptering |Rekommenderar att aktivera diskkryptering på alla virtuella datorer för att förbättra skydd av data i vila. |
| Nätverkssäkerhetsgrupper |Rekommenderar att [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) ska konfigureras för kontroll av inkommande och utgående trafik till virtuella datorer med offentliga slutpunkter. Nätverkssäkerhetsgrupper som konfigureras för ett undernät ärvs av alla VM-nätverksgränssnitt om inget annat anges. Den här principen innebär, förutom kontroll av om nätverkssäkerhetsgrupp har konfigurerats, även att inkommande säkerhetsregler kontrolleras för att se om det finns några regler som tillåter inkommande trafik. |
| Brandvägg för webbaserade program |Rekommenderar att en brandvägg för webbaserade program etableras på virtuella datorer när något av följande stämmer: </br></br>[Offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) används och de inkommande säkerhetsreglerna för den associerade nätverkssäkerhetsgruppen är konfigurerad för att tillåta åtkomst till port 80/443.</br></br>Belastningsutjämnad IP-adress används och associerad nätverksbelastning och inkommande NAT-regler (Network Address Translation) är konfigurerade för att tillåta åtkomst till port 80/443. (Mer information finns i [Azure Resource Manager-stöd för Load Balancer](../load-balancer/load-balancer-arm.md). |
| Nästa generations brandvägg |Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Distributioner som nästa generations brandvägg rekommenderas för upptäcks av Security Center och du kan göra en virtuell installation. |
| SQL-granskning och hotidentifiering |Rekommenderar att granskning av åtkomst till Azure-databasen aktiveras för lag- och regelefterlevnad, avancerad hotidentifiering och undersökning. |
| SQL-kryptering |Rekommenderar att kryptering i vila ska aktiveras för Azure SQL Database, tillhörande säkerhetskopior och transaktionsloggfiler. Även om intrång i datan sker, går den inte att läsa. |
| Sårbarhetsbedömning |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |
| Lagringskryptering |Den här funktionen är för närvarande tillgänglig för Azure-blobbar och filer. När du har aktiverat Kryptering av lagringstjänst kommer endast nya data att krypteras. Befintliga filer i lagringskontot förbli okrypterade. |
| JIT-nätverksåtkomst |När JIT (Just-In-Time) är aktiverat begränsar Security Center inkommande trafik till dina virtuella Azure-datorer genom att skapa en NSG-regel. Du väljer vilka portar på den virtuella datorn som inkommande trafik ska begränsas till. Mer information finns i [Manage virtual machine access using just in time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) (Hantera åtkomsten till virtuella datorer med Just-In-Time). |


## <a name="next-step"></a>Nästa steg
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md). Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
