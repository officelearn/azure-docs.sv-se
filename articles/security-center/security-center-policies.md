---
title: "Ange säkerhetsprinciper i Azure Security Center | Microsoft Docs"
description: "I det här avsnittet berättar vi hur du ställer in säkerhetsprinciper i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 3b9e1c15-3cdb-4820-b678-157e455ceeba
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7fba0338f6a61998b1c103210cd60c1d37eeffd5


---
# <a name="set-security-policies-in-azure-security-center"></a>Ange säkerhetsprinciper i Azure Security Center
I det här avsnittet får du genom instruktioner hjälp med att ställa in säkerhetsprinciper i Security Center.

## <a name="what-are-security-policies"></a>Vad är säkerhetsprinciper?
En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Security Center ställer du in principer för dina prenumerationer och resursgrupper i Azure enligt ditt företags behov och typ av program eller efter känslighetsnivån på datauppgifterna i respektive prenumeration.

De resurser som används för utveckling eller testning har kanske andra säkerhetskrav än de resurser som används för program i produktionen. På samma sätt behövs det kanske en högre säkerhetsnivå för sådant som det finns lagar om, till exempel personuppgifter. Säkerhetsprinciperna i Azure Security Center innehåller säkerhetsrekommendationer och möjlighet till övervakning som gör att du kan upptäcka potentiella säkerhetsrisker och avhjälpa hot. I [Planering- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md) finns mer information om hur du avgör vilket alternativ som passar dig bäst.

## <a name="set-security-policies-for-subscriptions"></a>Ställa in säkerhetsprinciper för prenumerationer
Det går att ställa in särskilda säkerhetsprinciper för varje prenumeration och resursgrupp. För att kunna ändra en säkerhetsprincip måste du vara ägare eller deltagare i den aktuella prenumerationen. Logga in på Azure-portalen och följ föregående steg för att konfigurera säkerhetsprinciper i Security Center:

1. Klicka på rutan **Policy** (Princip) i instrumentpanelen i Security Center.
2. På bladet **Säkerhetsprincip – ange princip för prenumeration eller resursgrupp** markerar du den prenumeration som du vill ställa in en säkerhetsprincip för. Om du vill ställa in en säkerhetsprincip för en resursgrupp i stället för hela prenumerationen går du ned till nästa avsnitt, där vi berättar hur du ställer in säkerhetsprinciper för resursgrupper.
   
    ![Ange princip](./media/security-center-policies/security-center-policies-fig1-ga.png)
3. Bladet **Säkerhetsprincip** för den valda prenumerationen öppnas med ett antal alternativ som liknar dem på följande skärm:
   
    ![Aktivera datainsamling](./media/security-center-policies/security-center-policies-fig2-ga.png)
   
    Alternativen på det här bladet är:
   
   * **Förebyggandeprincip**: Med det här alternativet kan du konfigurera principer per prenumeration eller resursgrupp.  
   * **E-postavisering**: Använd det här alternativet för att konfigurera ett e-postmeddelande som skickas vid den första dagliga förekomsten av en avisering och endast för varningar med hög angelägenhetsgrad. E-postinställningar kan bara konfigureras för prenumerationsprinciper. Mer information om hur du konfigurerar e-postaviseringar finns i [Lägga till säkerhetskontaktuppgifter i Azure Security Center](security-center-provide-security-contact-details.md).
   * **Prisnivå**: Använd det här alternativet för att uppgradera valet av prisnivå. Mer information om prisalternativ finns på [sidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/).
4. Kontrollera att alternativet **Samla in data från virtuella datorer** är inställt på **På**. Med den här inställningen samlas loggar in automatiskt för befintliga och nya resurser.
   
   > [!NOTE]
   > Vi rekommenderar att du aktiverar datainsamling för alla prenumerationer, eftersom du då kan vara säker på att alla befintliga och nya virtuella datorer är säkerhetsövervakade. När du aktiverar datainsamling installeras övervakningsagenten. Om du inte vill aktivera datainsamling här kan du göra det senare i vyerna **Hälsa** och **Rekommendationer**. Du kan även aktivera datainsamling för endast prenumerationen eller för specifika virtuella datorer. I avsnittet med [vanliga frågor och svar om Azure Security Center](security-center-faq.md) kan du läsa om vilka virtuella datorer som stöds.
   > 
   > 
5. Om ditt lagringskonto inte har konfigurerats än ser du antagligen en varning som i följande bild när du öppnar **Säkerhetsprincip**:
   
    ![Val av lagringsutrymme](./media/security-center-policies/security-center-policies-fig2.png)
6. Om du ser den här varningen klickar du på alternativet och väljer region så som visas på följande skärm:
   
    ![Val av lagringsutrymme](./media/security-center-policies/security-center-policies-fig3-ga.png)
7. För varje region där det finns virtuella datorer som körs väljer du på vilket lagringskonto data som samlas in från de datorerna ska lagras. På så sätt kan du behålla data inom samma geografiska område, vilket förenklar sekretess- och datalagstiftningshanteringen. När du har bestämt vilken region som du vill använda markerar du den regionen och väljer lagringskonto.
8. På bladet **Välj lagringskonton** klickar du på **OK**.
   
   > [!NOTE]
   > Om du vill kan du samla ihop data från virtuella datorer i olika regioner på ett enda centralt lagringskonto. Mer information finns i våra [vanliga frågor och svar om Azure Security Center](security-center-faq.md).
   > 
   > 
9. På bladet **Säkerhetsprincip** klickar du på **På** för de säkerhetsrekommendationer du vill aktivera för aktuell prenumeration. Klicka på **Skyddsprincip** för att se alternativ som de som finns i följande skärmbild:
   
    ![Val av säkerhetsprinciper](./media/security-center-policies/security-center-policies-fig4-ga-new.png)

Använd följande tabell som referens för vad de olika alternativen innebär:

| Princip | När den är aktiverad |
| --- | --- |
| Systemuppdateringar |Hämtar en daglig lista med tillgängliga säkerhetsuppdateringar och viktiga uppdateringar från Windows Update eller Windows Server Update Services. Den hämtade listan beror på vilken tjänst som har konfigurerats för den virtuella datorn och rekommenderar att saknade uppdateringar tillämpas. I Linux-system använder principen pakethanteringssystemet med distribution för att se vilka paket som har tillgängliga uppdateringar. Den söker också efter säkerhetsuppdateringar och viktiga uppdateringar från virtuella datorer i [Azure Cloud Services](../cloud-services/cloud-services-how-to-configure.md). |
| Sårbarheter i operativsystem |Analyserar dagligen konfigurationer av operativsystemet för att fastställa problem som kan göra den virtuella datorn sårbar för angrepp. Principen rekommenderar också konfigurationsändringar för att åtgärda dessa sårbarheter. Se [listan med rekommenderade baslinjer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) för mer information om vilka konfigurationer som övervakas. |
| Slutpunktsskydd |Rekommendationer går ut om att slutpunktsskydd ska installeras på alla virtuella datorer med Windows så att virus, spionprogram och annan skadlig programvara kan tas bort. |
| Diskkryptering |Rekommenderar att aktivera diskkryptering på alla virtuella datorer för att förbättra skydd av data i vila. |
| Nätverkssäkerhetsgrupper |Rekommenderar att [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) ska konfigureras för kontroll av inkommande och utgående trafik till virtuella datorer med offentliga slutpunkter. Nätverkssäkerhetsgrupper som konfigureras för ett undernät ärvs av alla virtuella datorers nätverksgränssnitt om inget annat anges. Den här principen innebär, förutom kontroll av om nätverkssäkerhetsgrupp har konfigurerats, även att inkommande säkerhetsregler kontrolleras för att se om det finns några regler som tillåter inkommande trafik. |
| Brandvägg för webbaserade program |Rekommenderar att en brandvägg för webbaserade program etableras på virtuella datorer när något av följande stämmer:</br></br>[Offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) (ILPIP) används och de inkommande säkerhetsreglerna för den associerade nätverkssäkerhetsgruppen är konfigurerad för att tillåta åtkomst till port 80/443.</br></br>Belastningsutjämnad IP-adress används och associerad nätverksbelastning och inkommande NAT-regler (Network Address Translation) är konfigurerade för att tillåta åtkomst till port 80/443. (Mer information finns i [Azure Resource Manager-stöd för Load Balancer](../load-balancer/load-balancer-arm.md). |
| Nästa generations brandvägg |Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Distributioner som nästa generations brandvägg rekommenderas för upptäcks av Security Center och du kan göra en virtuell installation. |
| SQL-granskning och hotidentifiering |Rekommenderar att granskning av åtkomst till Azure-databasen aktiveras för lag- och regelefterlevnad, avancerad hotidentifiering och undersökning. |
| SQL transparent datakryptering |Rekommenderar att kryptering i vila ska aktiveras för Azure SQL Database, tillhörande säkerhetskopior och transaktionsloggfiler. Även om intrång i datan sker, går den inte att läsa. |
| Sårbarhetsbedömning |Rekommenderar att du installerar en lösning för sårbarhetsbedömning på den virtuella datorn. |

När du har ställt in alla alternativ klickar du på **OK** i bladet **Säkerhetsprincip** med rekommendationerna och sedan på **Spara** i bladet **Säkerhetsprincip** med grundinställningarna.

## <a name="set-security-policies-for-resource-groups"></a>Ställa in säkerhetsprinciper för resursgrupper
Om du hellre vill ställa in säkerhetsprinciper för resursgrupper går det till på ungefär samma sätt som när du ställer in säkerhetsprinciper för prenumerationer. Den största skillnaden är att du måste expandera prenumerationsnamnet och markera den resursgrupp som du vill ställa in en unik säkerhetsprincip för:

![Val av resursgrupp](./media/security-center-policies/security-center-policies-fig5-ga.png)

När du har markerat en resursgrupp öppnas bladet **Säkerhetsprincip**. Som standard är alternativet **Arv** aktiverat. Det innebär att alla säkerhetsprinciper för den här resursgruppen ärvs från prenumerationsnivån. Om du vill att den här resursgruppen ska ha en egen säkerhetsprincip kan du ändra den här inställningen. I så fall väljer du **Unik** och gör ändringarna under alternativet **Skyddsprincip**.

![Säkerhetsprincip för resursgrupp](./media/security-center-policies/security-center-policies-fig6-ga.png)

> [!NOTE]
> Om en princip på prenumerationsnivå motsäger en princip på resursgruppsnivå gäller principen på resursgruppsnivå i första hand.
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




<!--HONumber=Nov16_HO2-->


