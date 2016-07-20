<properties
   pageTitle="Hantera och åtgärda säkerhetsaviseringar i Azure Security Center | Microsoft Azure"
   description="I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.topic="hero-article"
   ms.devlang="na"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/22/2016"
   ms.author="yurid"/>

# Hantera och åtgärda säkerhetsaviseringar i Azure Security Center
I det här dokumentet beskrivs hur du hanterar och åtgärdar säkerhetsaviseringar med hjälp av funktionerna i Azure Security Center.

> [AZURE.NOTE] Informationen i det här dokumentet rör förhandsversionen av Azure Security Center.

## Vad är Azure Security Center?
 Med hjälp av Security Center kan du förebygga, upptäcka och åtgärda hot med bättre överblick och kontroll över säkerheten för dina resurser i Azure. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## Vad är säkerhetsaviseringar?
Loggdata från resurserna i Azure, nätverket och de integrerade programmen mot skadlig kod och brandväggarna samlas in, analyseras och integreras i Security Center för upptäckt av verkliga hot och falsklarm. I Security Center visas en lista med prioritetssorterade säkerhetsaviseringar, även från integrerade partnerlösningar, samt information om hur du snabbt kan undersöka problemen och rekommendationer för hur du kan avhjälpa angrepp.

Microsofts säkerhetsundersökare analyserar hela tiden nya hot från hela världen, till exempel nya angreppsmönster och trender som de ser i Microsofts konsument- och företagsinriktade produkter och onlinetjänster. På så sätt kan upptäcktsalgoritmerna i Security Center uppdateras i takt med att nya säkerhetsrisker och trojaner upptäcks. Exempel på olika typer av hot som kan upptäckas med Security Center:

- **Nyckelsökningsangrepp via nätverksdata**: Här används machine-learningmodeller som vet hur nätverkstrafikmönstren vanligtvis brukar se ut för dina program, vilket gör att det går att upptäcka åtkomstförsök som sannolikt utförs av obehöriga användare.
- **Nyckelsökningsangrepp via slutpunktsdata**: Genom analys av datorloggar går det att se skillnad mellan misslyckade och lyckade försök.
- **Virtuella datorer som kommunicerar med skadliga IP-adresser**: Nätverkstrafiken jämförs med Microsofts globala hotinformation, vilket möjliggör upptäckt av datorer som är angripna och kommunicerar med kommando- och kontrollservrar och tvärtom.
- **Angripna virtuella datorer**: Genom beteendeanalys av datorloggar och jämförelse med andra signaler identifieras avvikande aktiviteter som sannolikt beror på att datorer har blivit angripna och utnyttjade.

## Hantera säkerhetsaviseringar

Du kan se aktuella aviseringar i rutan **Security alerts (Säkerhetsaviseringar)**. Nedan beskrivs hur du gör för att se mer information om de olika aviseringarna.

1. På instrumentpanelen i Security Center hittar du rutan **Security alerts (Säkerhetsaviseringar)**.

    ![Rutan med säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-new.png)

2.  Om du klickar på rutan öppnas bladet **Security alerts (Säkerhetsaviseringar)** med mer information om aviseringarna så som visas på bilden nedan.

    ![Bladet med säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-new.png)

Längst ned i bladet visas information om de olika aviseringarna. Du kan sortera listan genom att klicka på den kolumn som du vill sortera efter. Här följer en förklaring av de olika kolumnerna:

- **Alert (Avisering)**: en kort förklaring av aviseringen
- **Count (Antal)**: antalet problem av just den här typen som upptäckts en specifik dag
- **Detected by (Upptäcktes genom)**: den tjänst som utlöste aviseringen
- **Date (Datum)**: det datum då händelsen inträffade
- **State (Status)**: aktuell status för den här aviseringen Det finns tre olika statusar:
    - **Active (Aktiv)**: Säkerhetsproblemet är upptäckt.
    - **Dismissed (Avvisad)**: Säkerhetsaviseringen har avvisats av användaren. Den här statusen visas oftast för problem som undersökts men som avhjälpts eller som visat sig inte vara något verkligt angrepp.

- **Severity (Allvarlighetsgrad)**: kan vara hög, medelhög eller låg

Aviseringarna kan filtreras efter datum, status och allvarlighetsgrad. Att filtrera kan vara bra när du vill begränsa hur många aviseringar du vill se. Kanske vill du till exempel se säkerhetsaviseringar från det senaste dygnet eftersom du undersöker ett potentiellt angrepp i systemet under den här tiden.

1. Klicka på **Filter** på bladet **Security Alerts (Säkerhetsaviseringar)**. Bladet **Filter** öppnas där du kan välja datum, status och vilka allvarlighetsgrader du vill se.

    ![Filtrera aviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-new.png)

2.  När du undersöker säkerhetsaviseringar upptäcker du kanske att det är ett falsklarm eller att aviseringen gäller förväntade händelser för en viss resurs. Oavsett anledning kan du, om du anser att säkerhetsaviseringen inte är relevant, avvisa den och filtrera bort den så att du inte ser den.  Det finns två sätt att avvisa en säkerhetsavisering. Du kan högerklicka på aviseringen och välja **Dismiss (Avvisa)** eller hålla muspekaren över den och klicka på de tre punkterna till höger om den för att sedan klicka på **Dismiss (Avvisa)**. Du kan se stängda säkerhetsaviseringar genom att klicka på **Filter** och välja **Dismissed (Avvisade)**.

    ![Filtrera aviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-new.png)

### Åtgärda säkerhetsaviseringar
Om du klickar på en säkerhetsavisering får du se vad det var som utlöste aviseringen och om det finns något du kan göra för att stoppa ett pågående angrepp. Säkerhetsaviseringarna är indelade i grupper efter typ och datum. När du klickar på en säkerhetsavisering öppnas ett blad med en lista över de gruppindelade aviseringarna.

![Åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig7.png)

I det här fallet utlöstes aviseringarna på grund av misstänkt Remote Desktop Protocol-aktivitet. I den första kolumnen ser du vilka resurser som är angripna, i den andra vilken tid angreppet upptäcktes, i den tredje statusen på aviseringen och i den fjärde angreppets allvarlighetsgrad. När du har läst den här informationen klickar du på den angripna resursen. Då öppnas ett nytt blad med fler förslag på vad du kan göra, enligt bilden nedan.

![Förslag på vad som kan göras för att åtgärda säkerhetsaviseringar i Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig8-1.png)

I fältet **Alert (Avisering)** på det här bladet finns mer information om vad som inträffat. Här kan du se vad det var som utlöste säkerhetsaviseringen, vilken resurs som är angripen, eventuell IP-adress som angreppet kommer ifrån och rekommendationer om hur du kan åtgärda problemet.  I vissa fall finns ingen IP-adress till källan eftersom IP-adresser inte ingår i alla säkerhetshändelseloggar i Windows.

> [AZURE.NOTE] Vilka åtgärder som rekommenderas varierar beroende på typ av säkerhetsavisering. I vissa fall måste du kanske använda andra funktioner i Azure för att utföra de rekommenderade åtgärderna. Den rekommenderade åtgärden för det här angreppet är till exempel att svartlista IP-adressen som angreppet kommer ifrån genom regler för [nätverks-ACL](../virtual-network/virtual-networks-acl.md) eller en [nätverkssäkerhetsgrupp](../virtual-network/virtual-networks-nsg.md).


## Nästa steg
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Security Center. I följande avsnitt kan du lära dig mer om Security Center:

- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
- [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md): Här får du lära dig hur du övervakar dina partnerlösningars hälsostatus.
- [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
- [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/): Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.



<!--HONumber=Jun16_HO2-->


