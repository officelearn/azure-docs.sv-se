<properties
   pageTitle="Komma igång med Azure Security Center | Microsoft Azure"
   description="I det här avsnittet berättar vi hur du ställer in säkerhetsprinciper i Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/03/2016"
   ms.author="yurid"/>

# Ange säkerhetsprinciper i Azure Security Center
I det här avsnittet får du genom instruktioner hjälp med att ställa in säkerhetsprinciper i Azure Security Center.

> [AZURE.NOTE] Informationen i det här dokumentet rör förhandsversionen av Azure Security Center.

## Vad är Azure Security Center?
Med hjälp av Azure Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## Vad är säkerhetsprinciper?
En säkerhetsprincip är ett antal kontrollfunktioner som rekommenderas för resurser inom en viss prenumeration eller resursgrupp. I Azure Security Center ställer du in principer för dina prenumerationer och resursgrupper i Azure enligt ditt företags behov och typ av program eller efter känslighetsnivån på datauppgifterna i respektive prenumeration.

De resurser som används för utveckling eller testning behöver kanske en annan typ av säkerhetsinställningar än de som används för program i produktion. På samma sätt behövs det kanske en högre säkerhetsnivå för sådant som det finns lagar om, till exempel personuppgifter. Genom säkerhetsprinciperna i Azure Security Center får du säkerhetsrekommendationer och möjlighet till övervakning som gör att du kan upptäcka potentiella säkerhetsrisker och avhjälpa hot.

## Ställa in säkerhetsprinciper för prenumerationer

Det går att ställa in särskilda säkerhetsprinciper för olika prenumerationer och resursgrupper. För att kunna ändra en säkerhetsprincip måste du vara ägare eller deltagare i den aktuella prenumerationen. Du ställer in säkerhetsprinciper i Azure Security Center enligt instruktionerna nedan:

1. Klicka på rutan **Security Policy (Säkerhetsprincip)** i instrumentpanelen i Azure Security Center.

2. På bladet **Security Policy - Define policy per subscription or resource group (Säkerhetsprincip – ange princip för prenumeration eller resursgrupp)** som öppnas på höger sida markerar du den prenumeration som du vill ställa in en säkerhetsprincip för. Om du vill ställa in en säkerhetsprincip för en resursgrupp istället för för hela prenumerationen går du ned till nästa avsnitt, där vi berättar hur du ställer in säkerhetsprinciper för resursgrupper.

    ![Ange princip](./media/security-center-policies/security-center-policies-fig01.png)

3. Bladet **Security policy (Säkerhetsprincip)** för den prenumerationen öppnas med ett antal alternativ liknande de du ser nedan:

    ![Aktivera datainsamling](./media/security-center-policies/security-center-policies-fig1-new.png)

4. Kontrollera att alternativet **Collect data from virtual machines (Samla in data från virtuella datorer)** är inställt på **On (På)**. Med den här inställningen samlas loggar in automatiskt för befintliga och nya resurser. 

    >[AZURE.NOTE] Vi rekommenderar starkt att du aktiverar datainsamling för alla prenumerationer, eftersom du då kan vara säker på att alla befintliga och nya virtuella datorer är säkerhetsövervakade. När du aktiverar datainsamling installeras övervakningsagenten. Om du inte vill aktivera datainsamling här kan du göra det senare i hälso- och rekommendationsvyerna. Du kan även aktivera datainsamling för endast prenumerationen eller för specifika virtuella datorer. I avsnittet med [vanliga frågor och svar om Azure Security Center](security-center-faq.md) kan du läsa om vilka virtuella datorer som stöds.

5. Om ditt lagringskonto inte har konfigurerats än ser du antagligen en varning som den på bilden nedan när du öppnar bladet **Security Policy (Säkerhetsprincip)**:

    ![Val av lagringsutrymme](./media/security-center-policies/security-center-policies-fig2.png)

6. Om du ser den här varningen klickar du på den och väljer region så som visas på bilden nedan:

    ![Val av lagringsutrymme](./media/security-center-policies/security-center-policies-fig3.png)

7. För var och en av de regioner där du har virtuella datorer väljer du på vilket lagringskonto data som samlas in från de datorerna ska lagras. På så sätt kan du behålla data inom samma geografiska område, vilket förenklar sekretess- och datalagstiftningshanteringen. När du har bestämt vilken region som du vill använda markerar du den regionen och väljer lagringskonto.

8. På bladet **Choose storage accounts (Välj lagringskonton)** klickar du på **OK**.

    > [AZURE.NOTE] Om du vill kan du samla ihop data från virtuella datorer i olika regioner på ett enda centralt lagringskonto. Mer information finns i våra [vanliga frågor och svar om Azure Security Center](security-center-faq.md).

9. På bladet **Security Policy (Säkerhetsprincip)** klickar du på **On (På)** för de säkerhetsrekommendationer du vill aktivera för aktuell prenumeration.  Klicka på alternativet **Skyddsprincip**. Bladet **Prevention policy (Säkerhetsprincip)** öppnas som på bilden nedan. 

    ![Val av säkerhetsprinciper](./media/security-center-policies/security-center-policies-fig1-1-new2.png)

I tabellen nedan beskrivs vad de olika alternativen innebär:

| Princip | Vid inställning På |
|----- |-----|
| Systemuppdateringar | En lista med tillgängliga säkerhetsuppdateringar och andra viktiga uppdateringar hämtas varje dag via Windows Update eller WSUS, beroende på vilken tjänst som är konfigurerad för den virtuella datorn, och rekommendationer går ut om att de uppdateringar som saknas ska installeras. |
| Baslinjeregler | Inställningarna i operativsystemet som kan göra de virtuella datorerna mer sårbara analyseras varje dag och rekommendationer går ut om att inställningarna bör ändras så att de aktuella riskerna åtgärdas. I [listan med rekommenderade baslinjer](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) hittar du mer information om vilka inställningar det är som övervakas. |
| Slutpunktsskydd | Rekommendationer går ut om att slutpunktsskydd ska installeras på alla virtuella datorer med Windows så att virus, spionprogram och annan skadlig programvara kan tas bort. 
| Nätverkssäkerhetsgrupper | Rekommendationer går ut om att [nätverkssäkerhetsgrupper](../virtual-network/virtual-networks-nsg.md) ska konfigureras för kontroll av inkommande och utgående trafik till undernät och nätverksgränssnitt. Nätverkssäkerhetsgrupper som konfigureras för ett undernät ärvs av alla virtuella datorers nätverksgränssnitt om inget annat anges. Det här alternativet innebär, förutom kontroll av om nätverkssäkerhetsgrupp har konfigurerats, även att inkommande säkerhetsregler kontrolleras för att se om det finns några som tillåter inkommande trafik. |
| Brandvägg för webbaserade program | Rekommendation går ut om att en brandvägg för webbaserade program ska finnas på alla virtuella datorer när [offentlig IP på instansnivå](../virtual-network/virtual-networks-instance-level-public-ip.md) används och när tillhörande inkommande säkerhetsregler för nätverkssäkerhetsgruppen är inställda på att tillåta åtkomst till port 80/443 och när belastningsutjämnad IP används och när tillhörande regler för belastningsutjämning och inkommande NAT är inställda på att tillåta åtkomst till port 80/443 (mer information finns i artikeln om [Azure Resource Manager-stöd för Load Balancer](../load-balancer/load-balancer-arm.md)) |
| Nästa generations brandvägg | Nätverksskyddet utökas utöver nätverkssäkerhetsgrupperna, som är inbyggda i Azure. Distributioner som nästa generations brandvägg rekommenderas för upptäcks av Security Center och du får möjlighet att göra en virtuell installation. |
| SQL-granskning | Rekommendation går ut om att granskning av åtkomst till Azures SQL-servrar och SQL-databaser ska aktiveras för lag- och regelefterlevnad, avancerad identifiering och undersökning. |
| SQL transparent datakryptering | Rekommendation går ut om att kryptering i vila ska aktiveras för Azure SQL-databaser, tillhörande säkerhetskopior och transaktionsloggfiler så att även om obehöriga kommer över datainformation kan de inte läsa den. |

11. När du har ställt in alla alternativ klickar du på **OK** i bladet **Security Policy (Säkerhetsprincip)** med rekommendationerna och sedan på **Save (Spara)** i bladet **Security Policy (Säkerhetsprincip)** med grundinställningarna.

## Ställa in säkerhetsprinciper för resursgrupper

Om du hellre vill ställa in säkerhetsprinciper för resursgrupper går det till på ungefär samma sätt som när du ställer in säkerhetsprinciper för prenumerationer. Den största skillnaden är att du måste expandera prenumerationsnamnet och markera den resursgrupp som du vill ställa in en unik säkerhetsprincip för.

![Val av resursgrupp](./media/security-center-policies/security-center-policies-fig4.png)

När du har markerat en resursgrupp öppnas bladet **Security Policy (Säkerhetsprincip)**. Som standard är alternativet **Inheritance (Arv)** förvalt, vilket innebär att alla säkerhetsprinciper för den här resursgruppen ärvs från prenumerationsnivån. Om du vill att den här resursgruppen ska ha en egen säkerhetsprincip kan du ändra den här inställningen. I så fall väljer du **Unique (Unik)** och gör ändringarna under alternativet **Prevention policy (Skyddsprincip)**.

![Säkerhetsprincip för resursgrupp](./media/security-center-policies/security-center-policies-fig5-new.png)

> [AZURE.NOTE] Om en princip på prenumerationsnivå motsäger en princip på resursgruppsnivå gäller principen på resursgruppsnivå i första hand.


## Nästa steg

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

- [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md): Här får du lära att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
- [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md): Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
- [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur övervakar dina partnerlösningars hälsostatus.
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Jun16_HO2-->


