---
title: "Ange säkerhetsprinciper i Azure Security Center | Microsoft Docs"
description: "I det här avsnittet berättar vi hur du ställer in säkerhetsprinciper i Azure Security Center."
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
ms.date: 07/27/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: f4e3f74ce3f342eecf633cd748e2b7b21b2ccdd2
ms.contentlocale: sv-se
ms.lasthandoff: 07/27/2017

---
# <a name="set-security-policies-in-azure-security-center"></a>Ange säkerhetsprinciper i Azure Security Center
I det här avsnittet får du genom instruktioner hjälp med att ställa in säkerhetsprinciper i Security Center.

>[!NOTE] 
>Från och med början av juni 2017 använder Security Center Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns under [plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.
>

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration. I Security Center ställer du in principer för dina prenumerationer i Azure enligt ditt företags behov och typ av program eller efter känslighetsnivån på datauppgifterna i respektive prenumeration.

De resurser som används för utveckling eller testning har kanske andra säkerhetskrav än de resurser som används för program i produktionen. På samma sätt behövs det kanske en högre säkerhetsnivå för sådant som det finns lagar om, till exempel personuppgifter. Säkerhetsprinciperna i Azure Security Center innehåller säkerhetsrekommendationer och möjlighet till övervakning som gör att du kan upptäcka potentiella säkerhetsrisker och avhjälpa hot. I [Planering- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md) finns mer information om hur du avgör vilket alternativ som passar dig bäst.

## <a name="set-security-policies"></a>Ange säkerhetsprinciper
Det går att ställa in särskilda säkerhetsprinciper för varje prenumeration. För att kunna ändra en säkerhetsprincip måste du vara ägare eller deltagare i den aktuella prenumerationen. Logga in på Azure-portalen och följ föregående steg för att konfigurera säkerhetsprinciper i Security Center:

1. Klicka på rutan **Policy** (Princip) i instrumentpanelen i Security Center.
2. På bladet Säkerhetsprincip markerar du den prenumeration som du vill ställa in en säkerhetsprincip för.

    ![Ange princip](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Bladet **Säkerhetsprincip** för den valda prenumerationen öppnas med ett antal alternativ. Alternativen på det här bladet är:

   * **Förebyggandeprincip**: Med det här alternativet kan du konfigurera principer per prenumeration.  
   * **E-postavisering**: Använd det här alternativet för att konfigurera ett e-postmeddelande som skickas vid den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Mer information om hur du konfigurerar e-postaviseringar finns i [Lägga till säkerhetskontaktuppgifter i Azure Security Center](security-center-provide-security-contact-details.md).
   * **Prisnivå**: Använd det här alternativet för att uppgradera valet av prisnivå. Mer information om prisalternativen finns i [Security Center-prissättning](security-center-pricing.md).
4. Kontrollera att alternativet **Samla in data från virtuella datorer** är inställt på **På**. Det här alternativet möjliggör loggsamling för befintliga och nya resurser med hjälp av Microsoft Monitoring Agent – det här är samma agent används av Operations Management Suite och Log Analytics-tjänsten. Data som samlas in från agenten lagras antingen på befintliga Log Analytics-arbetsytor som är associerade med din Azure-prenumeration eller på nya arbetsytor, beroende på den virtuella datorns geografiska plats.

5. I bladet **säkerhetsprincip** bladet klickar du på **Skyddsprincip** för att se de tillgängliga alternativen. Klicka på **På** att för aktivera de säkerhetsrekommendationer som är relevanta för den här prenumerationen.

    ![Val av säkerhetsprinciper](./media/security-center-policies/security-center-policies-fig7.png)

Använd följande tabell som referens för vad de olika alternativen innebär:

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

När du har ställt in alla alternativ klickar du på **OK** i bladet **Säkerhetsprincip** med rekommendationerna och sedan på **Spara** i bladet **Säkerhetsprincip** med grundinställningarna.

> [!NOTE]
> Prisnivån gäller fortfarande för resursgruppsnivån. Mer information finns på sidan med [Priser](https://azure.microsoft.com/pricing/details/security-center/).
>
>

## <a name="see-also"></a>Se även
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md). Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md). Lär dig att hantera och åtgärda säkerhetsaviseringar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md). Lär dig att övervaka hälsotillståndet för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.

