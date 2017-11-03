---
title: Skydda dina personliga data med Azure Security Center | Microsoft Docs
description: "skydda personliga data med hjälp av Azure security center"
services: security
documentationcenter: na
author: Barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/24/2017
ms.author: barclayn
ms.custom: 
ms.openlocfilehash: 3a941389713a4d3dbffbbfe8a717409927d85c6d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="protect-personal-data-from-breaches-and-attacks-azure-security-center"></a>Skydda personliga data från överträdelser och attacker: Azure Security Center

Den här artikeln hjälper dig att förstå hur du använder Azure Security Center för att skydda personliga data från överträdelser och attacker.

## <a name="scenario"></a>Scenario 

Ett stort kryssning företag, sitt säte i USA utökar åtgärderna för att erbjuda färdvägar i Medelhavsområdet och baltiska havet samt Förenta staterna. Hjälpen i dessa åtgärder har det fått flera mindre kryssning rader i Italien, Tyskland, Danmark och Storbritannien

Företaget använder Microsoft Azure för att lagra företagets data i molnet. Detta inkluderar personligt identifierbar information, till exempel namn, adresser, telefonnummer och kreditkortsinformation. Den inkluderar också personal information såsom:

- Adresser
- telefonnummer
- skatt ID-nummer
- medicinska information

Raden kryssning har också en stor databas med ersättning och förmåner medlemmar. Företagets anställda åtkomst till nätverket från företagets fjärranslutna kontor och resa agenter finns runtom i världen har åtkomst till vissa företagsresurser.
Personliga data överförs över nätverket mellan dessa platser och Microsoft-datacenter.

## <a name="problem-statement"></a>Problembeskrivning

Företaget är orolig för angrepp på sina Azure-resurser. De vill förhindra exponering av kunders och anställdas personuppgifter till obehöriga. De vill ha hjälp med både förebyggande och svar/reparation, samt ett effektivt sätt att övervaka pågående säkerheten för deras molnresurser.
Behöver de en stark försvarslinje mot dagens avancerade och organiserade angripare.

## <a name="company-goal"></a>Företagets mål

En av företagets mål är att säkerställa säkerheten för kunderna och anställdas personliga data genom att skydda mot hot. En av sina mål är svarar omedelbart på tecken på intrång att minimera effekten. Det krävs ett sätt att utvärdera det aktuella tillståndet för säkerhet, identifiera sårbara konfigurationer och åtgärda dem.

## <a name="solutions"></a>Lösningar

Microsoft Azure Security Center (ASC) ger en integrerad säkerhet säkerhetsövervakning och principhantering hanteringslösning. Lätt att använda och effektivt hot ger funktioner för förebyggande, identifiering och svar.

### <a name="prevention"></a>Prevention (Skydd)

ASC hjälper dig att förhindra överträdelser genom att du kan ställa in säkerhetsprinciper, ger just-in-time-åtkomst och implementera säkerhetsrekommendationer.

En säkerhetsprincip definierar ett antal kontrollfunktioner som rekommenderas för resurser inom den angivna prenumerationen. Just-in-time-åtkomst kan användas för att låsa inkommande trafik till din virtuella Azure-datorer, vilket minskar risken för attacker. Säkerhetsrekommendationer skapas ASC när du har analyserat säkerhetstillståndet hos dina Azure-resurser.

#### <a name="how-do-i-set-security-policies-in-asc"></a>Hur ställer in säkerhetsprinciper i ASC?

Det går att ställa in särskilda säkerhetsprinciper för varje prenumeration. För att kunna ändra en säkerhetsprincip måste du vara ägare eller deltagare i den aktuella prenumerationen. Gör följande i Azure-portalen:

1. Välj **princip** i ASC instrumentpanelen.

2. Välj den prenumeration som du vill aktivera principen.

3. Välj **skyddsprincip** att konfigurera principer per prenumeration. **Samla in data från virtuella datorer** ska anges till **på.**

4. I den **skyddsprincip** alternativ, Välj **på** att aktivera säkerhetsrekommendationer som är relevanta för prenumerationen.

![](media/protect-personal-data-azure-security-center/prevention-policy.png)

Mer detaljerade instruktioner och en beskrivning av principrekommendationer som kan aktiveras finns [ställa in säkerhetsprinciper i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-policies#set-security-policies).

#### <a name="how-do-i-configure-just-in-time-access-jit"></a>Hur konfigurerar jag precis i tid åtkomst (JIT)?

När JIT aktiveras låser Security Center ned inkommande trafik till din virtuella Azure-datorer genom att skapa en regel för NSG. Du väljer portar på den virtuella datorn som inkommande trafik ska låsas. Om du vill använda JIT åtkomst gör du följande:

1. Välj den **precis i tid VM åtkomst panelen** på bladet ASC.

2. Välj den **rekommenderas** fliken.

3. Under **VMs**, Välj de virtuella datorer som du vill aktivera. Detta placerar en bock bredvid en virtuell dator. 
4. Välj **aktivera JIT** på virtuella datorer.
5. Välj **Spara**.

Du kan se standardportarna som ASC rekommenderar aktiverats för JIT. Du kan också lägga till och konfigurera en ny port som du vill aktivera den bara i Tidslösning. Den **precis i tid VM access** panelen i Security Center visar hur många virtuella datorer som är konfigurerad för JIT-åtkomst. Den visar även antal godkända åtkomstbegäranden som görs i den senaste veckan.

![](media/protect-personal-data-azure-security-center/jit-vm.png)

Instruktioner om hur du gör detta, och ytterligare information om precis i Time-åtkomst finns i [hantera virtuella åtkomst med hjälp av precis i tid.](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

#### <a name="how-do-i-implement-asc-security-recommendations"></a>Hur jag implementera ASC säkerhetsrekommendationer?

När Security Center identifierar potentiella säkerhetsproblem skapas rekommendationer. Via rekommendationerna får du hjälp att ställa in de kontrollfunktioner som behövs. 
1. Välj den **rekommendationer** panelen på instrumentpanelen ASC.

2. Visa rekommendationer som visas i tabellformat där varje rad utgör en rekommendation.

3. Filtrera rekommendationer, Välj **Filter** och välj allvarlighetsgrad och status värdena som du vill se.

4. För att stänga en rekommendation inte är tillämplig du högerklicka och välj **Ignorera.**

5. Utvärdera vilka rekommendation tillämpas först.

6. Tillämpa rekommendationerna i prioritetsordning.

En lista över möjliga rekommendationer och praktiska på hur du använder varje finns [hantera säkerhetsrekommendationer i Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

### <a name="detection-and-response"></a>Identifiering och svar

Identifiering och svaret hör ihop, som du vill att svara så snabbt som möjligt efter ett hot har identifierats.
Hotidentifiering ASC fungerar genom att automatiskt samla in säkerhetsinformation från resurserna i Azure, nätverket och anslutna partnerlösningar. ASC kan snabbt uppdateras som angripare släpper nya och allt mer avancerade kryphål. Mer detaljerad information om hur ASCS hotidentifiering fungerar finns [identifieringsfunktionerna i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-detection-capabilities).

#### <a name="how-do-i-manage-and-respond-to-security-alerts"></a>Hur hanterar jag och åtgärdar säkerhetsaviseringar?

En lista med prioritetssorterade säkerhetsaviseringar visas i Security Center tillsammans med den information du behöver att snabbt undersöka problemet. Security Center innehåller också rekommendationer för hur du kan avhjälpa angrepp. Om du vill hantera din säkerhetsaviseringar, gör du följande:

1. Välj den **säkerhetsaviseringar** panelen i ASC instrumentpanelen. Detta visar information för varje avisering.

2. Om du vill filtrera aviseringar baserat på datum, status och allvarlighetsgrad, Välj **Filter** och sedan välja de värden som du vill se.

3. För att svara på en avisering, markerar du den och granska informationen, och välj sedan den angripna resursen.

4. I den **beskrivning** fältet visas information, inklusive rekommenderade åtgärderna.

![](media/protect-personal-data-azure-security-center/security-alerts.png)

Mer detaljerad information om åtgärda säkerhetsaviseringar finns [hantera och åtgärda säkerhetsaviseringar i Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

För ytterligare hjälp i undersöker säkerhetsaviseringar företaget kan integrera ASC aviseringar med sin egen SIEM-lösning med hjälp av [Azure Log-integrering](https://aka.ms/AzLog).

#### <a name="how-do-i-manage-security-incidents"></a>Hur hanterar säkerhetsincidenter?

Är en sammanställning av alla aviseringar för en resurs som överensstämmer med kill kedjan mönster i ASC, en säkerhetsincident. En incident visar en lista över relaterade aviseringar så att du kan få mer information om varje förekomst. Incidenter visas i rutan med säkerhetsaviseringar och bladet.

Granska och hantera säkerhetsincidenter genom att göra följande:

1. Välj den **säkerhetsaviseringar** panelen. Om en säkerhetsincident har upptäckts, visas den under säkerhet aviseringar diagrammet. Har en ikon som skiljer sig från andra aviseringar.

2. Välj objekt att visa mer information om den här säkerhetsincident. Ytterligare information innehåller en fullständig beskrivning, dess allvarlighetsgrad, det aktuella tillståndet, angripna resursen, reparationssteg för incidenten och aviseringar som ingår i den här incidenten.

Du kan filtrera om du vill se **incidenter endast**, **aviseringar endast**, eller **båda**.

#### <a name="how-do-i-access-the-threat-intelligence-report"></a>Hur kommer jag åt hot Intelligence rapporten?

ASC analyserar information från flera källor för att identifiera hot. För att underlätta incidenter team undersöka och åtgärda hot innehåller Security Center en hot intelligence-rapport som innehåller information om hot som har identifierats.

Security Center har tre typer av hot rapporter som kan variera efter attack.
De tillgängliga rapporterna är:

- Aktivitetsgrupp rapport: erbjuder djup dyker ned i angripare, mål och medvetande.

- Kampanj rapport: fokuserar på information om specifika attack kampanjer.

- Översiktsrapport för hot: omfattar alla objekt i de föregående två rapporterna.

Den här typen av information är mycket användbar när incidenter blir där det finns en pågående undersökning för att förstå källan för angrepp angriparens motiveringen och vad du gör om du vill undvika det här problemet vidarebefordras.

1. Om du vill komma åt den hot intelligence gör du följande:

2. Välj den **säkerhetsaviseringar** panelen på instrumentpanelen ASC.

3. Välj säkerhetsaviseringen som du vill få mer information.

4. I den **rapporter** fältet, klicka på länken till hot intelligence-rapporten.

5. PDF-fil som du kan hämta öppnas.

![](media/protect-personal-data-azure-security-center/security-alerts-suspicious-process.png)

Mer information om ASC hot intelligence rapporten finns [Azure Security Center hot Intelligence-rapporten.](https://docs.microsoft.com/azure/security-center/security-center-threat-report)

### <a name="assessment"></a>Bedömning

Om du vill hjälpa till med testning, bedömning och utvärdering av din säkerhetstillståndet, ger ASC för inbyggt säkerhetsproblem med Qualys molnet agenter som en del av den virtuella datorn rekommendationer komponenten.

Qualys agenten rapporterar säkerhetsproblem data till hanteringsplattform Qualys som sedan skickar säkerhetsproblem och hälsoövervakning data tillbaka till ASC. Rekommendationen att lägga till en vulnerability assessment lösning visas i den **rekommendationer** bladet på instrumentpanelen ASC.

När du har installerat lösningen för sårbarhetsbedömning på den virtuella måldatorn, genomsöker Security Center VM:en för att identifiera sårbarheter med systemet och med program. Identifierade problem visas under alternativet **rekommendationer för virtuella datorer**.

#### <a name="how-do-i-implement-a-vulnerability-assessment-solution"></a>Hur jag för att implementera en lösning för vulnerability assessment? 

Om en virtuell dator inte har ett inbyggt säkerhetsproblem assessment lösningen har redan distribuerats rekommenderar Security Center att installeras.

1. I instrumentpanelen för ASC på den **rekommendationer** bladet väljer **lägger till en vulnerability assessment lösning.**

2. Välj de virtuella datorerna där du vill installera vulnerability assessment lösning.

3. Klicka på **installera på virtuella datorer [antal].**

4. Markera en partnerlösning i Azure Marketplace eller under **använda befintlig lösning,** Välj **Qualys.**

5. Du kan aktivera inställningar för automatisk uppdatering eller inaktivera den **partnerlösningar** bladet.

Ytterligare instruktioner för hur du implementerar en lösning för vulnerability assessment finns [utvärdering av säkerhetsrisker i Azure Security Center.](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

## <a name="next-steps"></a>Nästa steg

- [Snabbstartsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-get-started)

- [Introduktion till Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

- [Integrera Azure Security Center-aviseringar med Azure logganalys-integration](https://docs.microsoft.com/azure/security-center/security-center-integrating-alerts-with-log-integration)

- [Öka Azure Security Center med integrerad Vulnerability Assessment](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/16/boost-azure-security-center-with-integrated-vulnerability-assessment/)
