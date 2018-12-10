---
title: Vad är Azure Security Center?| Microsoft Docs
description: Här får du lära dig om Azure Security Center, de viktigaste funktionerna och hur Security Center fungerar.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 682291cfe0e7a06aca1d4b1bef48ba36c72f48d9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839882"
---
# <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Med Security Center kan du tillämpa säkerhetsprinciper i arbetsbelastningarna, begränsa hotexponeringen samt identifiera och åtgärda attacker.

Varför ska man använda Security Center?

- **Centraliserad principhantering** – Säkerställ att företagets eller reglerade säkerhetskrav följer standarden genom att hantera principer centralt i alla dina arbetsbelastningar i hybridmoln.
- **Kontinuerlig säkerhetsbedömning** – Övervaka säkerheten i datorer, nätverk, lagring, datatjänster och program så att du kan identifiera potentiella säkerhetsproblem.
- **Rekommendationer som kan åtgärdas** – Åtgärda säkerhetsproblem innan de kan utnyttjas av angripare med hjälp av rangordnade säkerhetsrekommendationer.
- **Rangordnade aviseringar och incidenter** – Fokusera på de mest kritiska hoten först med rangordnade säkerhetsaviseringar och incidenter.
- **Avancerat molnförsvar** – Minska hot med just-in-time-åtkomst till hanteringsportar och anpassningsbara programkontroller som körs på dina virtuella datorer.
- **Integrerade säkerhetslösningar** – Samla in, sök efter och analysera säkerhetsdata från flera olika källor, till exempel anslutna partnerlösningar.

**Security Center – Översikt** ger en snabb överblick över säkerhetstillståndet för dina Azure- och icke-Azure-arbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Den inbyggda instrumentpanelen innehåller omedelbara insikter om säkerhetsaviseringar och sårbarheter som kräver uppmärksamhet.

![Översikt][1]

## <a name="centralized-policy-management"></a>Centraliserad principhantering
Avsnittet **Princip och efterlevnad** (se ovan) ger snabb information om din prenumerationstäckning, principefterlevnad och säkerhet.

### <a name="subscription-coverage"></a>Prenumerationstäckning
Det här avsnittet visar det totala antalet prenumerationer som du har läs- eller skrivåtkomst till och vilken Security Center-täckning (Standard eller Kostnadsfri) som gäller för en viss prenumeration:

- **Omfattas (standard)** – De prenumerationer som omfattas körs med den maximala skyddsnivå som erbjuds av Security Center
- **Omfattas (kostnadsfri)** – De prenumerationer som omfattas körs med det begränsade kostnadsfria skydd som erbjuds av Security Center
- **Omfattas inte** – Prenumerationer med den här statusen övervakas inte av Azure Security Center

Om du väljer diagrammet öppnas fönstret **Täckning**. Om du väljer en flik (**Omfattas inte**, **Grundläggande täckning**, eller **Standardtäckning**) visas en lista över prenumerationer för respektive status. Om du väljer en prenumeration under en av flikarna visas ytterligare information om en prenumerationen. Med den här informationen kan du identifiera prenumerationens ägare och kontakta hen om att aktivera Security Center eller öka prenumerationens täckning.

![Security Center-täckning][9]

### <a name="policy-compliance"></a>Principefterlevnad
Principefterlevnad bestäms av efterlevnadsfaktorer för alla tilldelade principer. Den övergripande efterlevnadsgraden för en hanteringsgrupp, prenumeration eller arbetsyta är tilldelningarnas viktade genomsnitt. Viktade medelvärdesfaktorer är antalet principer i en enda tilldelning och antalet resurser som omfattas av tilldelningen.

Om din prenumeration till exempel innehåller två virtuella datorer som är tilldelade ett initiativ med fem principer har prenumerationen tio utvärderingar. Om en av de virtuella datorerna inte uppfyller två av principerna är den totala efterlevnadsgraden 80 % för din prenumerations tilldelning.

Det här avsnittet visar ditt övergripande efterlevnadsförhållande och dina minst kompatibla prenumerationer. Om du väljer **Visa miljöns principefterlevnad** öppnas fönstret **Principhantering**. I **Principhantering** visas den hierarkiska strukturen för hanteringsgrupper, prenumerationer och arbetsytor. Här kan du hantera dina säkerhetsprinciper genom att välja en prenumeration eller hanteringsgrupp.

![Principhantering][10]

En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Security Center kan du definiera principer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data, fastställa vilka kontroller som Security Center övervakar och rekommenderar. Du kan redigera säkerhetsprincipen i Security Center genom att klicka på en hanteringsgrupp eller prenumeration. Du kan också använda Azure Policy för att skapa nya definitioner, definiera ytterligare principer och tilldela hanteringsgrupper principer.

Välj **Redigera inställningar >** om du vill redigera följande Security Center-inställningar på prenumerations-, hanteringsgrupps-, resursgrupps- eller arbetsutrymmesnivå:

- **Datainsamling**: Fastställer agentetablering och säkerhetsinställningar för [datainsamlingen](security-center-enable-data-collection.md).
- **E-postmeddelanden**: Fastställer säkerhetskontakter och inställningar för [e-postmeddelanden](security-center-provide-security-contact-details.md).
- **Prisnivå**: Definierar [prissättningarna](security-center-pricing.md) Kostnadsfri och Standard. Vilken nivå du väljer avgör vilka Security Center-funktioner som blir tillgängliga för resurser i området.
- **Redigera säkerhetskonfigurationer**: Låter dig visa och ändra operativsystemskonfigurationer som utvärderats av Security Center för att identifiera potentiella säkerhetsrisker.

Mer information finns i [Integrera Security Center-säkerhetsprinciper med Azure Policy](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>Hantera och styr din säkerhetshållning
På den högra sidan av instrumentpanelen under **Princip och efterlevnad** får du information som du kan arbeta med för att omedelbart förbättra din övergripande säkerhetshållning. Några exempel:

- Definiera och tilldela Security Center-principer för att kunna granska och spåra att säkerheten följer standard
- Gör Security Center-aviseringar tillgängliga för en SIEM-anslutningsapp
- Principefterlevnad över tid

## <a name="continuous-security-assessment"></a>Kontinuerlig säkerhetsbedömning
I avsnittet för resurssäkerhetshygien under **Security Center – översikt** får du en snabb överblick över dina resursers säkerhethygien: där visas antalet identifierade problem och säkerhetsstatus för varje resurstyp. Med den kontinuerliga kontrollen identifieras potentiella säkerhetsproblem, till exempel system som saknar säkerhetsuppdateringar eller exponerade nätverksportar.

### <a name="secure-score"></a>Säkerhetspoäng
Azure Security Center går igenom dina säkerhetsrekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer du bör åtgärda först, vilket hjälper dig att hitta de mest allvarliga säkerhetsriskerna, så att du kan prioritera undersökningen. Secure Score är ett mätverktyg som hjälper dig att stärka din säkerhet så att du uppnår en säker arbetsbelastning. Mer information finns i [Secure Score i Azure Security Center](security-center-secure-score.md).

### <a name="health-monitoring"></a>Hälsoövervakning
Om du väljer en resurstyp under **Övervakning av resurshälsa** visas en lista över resurser och eventuella sårbarheter som har identifierats. Resurstyper är beräkningsar och program, nätverk, data och lagring, och identitet och åtkomst.

Vi har valt **Beräkning och appar**. Under **Beräkning** finns det fyra flikar:

- **Översikt**: Övervakning och rekommendationer som identifierats av Security Center.
- **Virtuella datorer och datorer**: Listar alla dina virtuella datorer, datorer och det aktuella säkerhetstillståndet för var och en.
- **Molntjänster**: Listar alla dina webb- och arbetarroller som övervakas av Security Center.
- **App Services (förhandsversion)**: Listar alla dina webbprogram och App Service-miljöer och det aktuella säkerhetstillståndet för var och en.

![Övervakning av säkerhetshälsa][3]

Mer information finns i [Övervakning av säkerhetshälsa](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Användbara rekommendationer
Säkerhetstillståndet för resurserna i Azure och utanför Azure analyseras i Security Center för upptäckt av eventuella sårbarheter. Om du väljer **Rekommendationer** under **Resurser** visas en lista med rangordnade säkerhetsrekommendationer som vägleder dig genom processen med att åtgärda säkerhetsproblem.

![Rekommendationer][4]

Mer information finns i [Hantera säkerhetsrekommendationer](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>De vanligaste rekommendationerna
På höger sida av instrumentpanelen under **Resurser** hittar du en lista över de vanligaste rekommendationerna för merparten av resurserna. Den vanligaste rekommendationerna visar var du bör fokusera din uppmärksamhet. Om du klickar på högerpilen visas den rekommendation som ger högsta påverkan.

![De vanligaste rekommendationerna][11]

Detta är den enskilt mest verkningsfulla rekommendationen i din miljö. Att åtgärda den här rekommendationen är den åtgärd som förbättrar din efterlevnad mest. I det här exemplet är denna rekommendationen Tillämpa diskkryptering. Om du väljer **Förbättra efterlevnad** får du en beskrivning av rekommendationen och en lista över de resurser som påverkas.

![Tillämpa diskkryptering][12]

## <a name="threat-protection"></a>Hotskydd
Det här området ger visar de säkerhetsaviseringar som har identifierats på dina resurser och anger deras allvarlighetsgrad.

### <a name="prioritized-alerts-and-incidents"></a>Rangordnade aviseringar och incidenter
Security Center använder avancerade analyser och global hotinformation för att upptäcka inkommande attacker och aktiviteter efter intrång. Aviseringar prioriteras och grupperas till incidenter, vilket gör det lättare för dig att fokusera på de mest kritiska hoten först. Du kan även skapa egna anpassade säkerhetsaviseringar.

Om du väljer **Säkerhetsaviseringar efter allvarlighetsgrad** eller **Säkerhetsaviseringar över tid** visas detaljerad information om aviseringarna.

![Rangordnade aviseringar och incidenter][7]

Du kan snabbt bedöma omfattningen och konsekvenserna av en attack med en visuell och interaktiv undersökning, samt med hjälp av fördefinierade eller ad hoc-frågor som undersöker dina säkerhetsdata mer ingående.

Mer information finns i [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md).

På höger sida i instrumentpanelen visas information som hjälper dig att prioritera vilka aviseringar som ska behandlas först och var de vanligaste sårbarheterna finns:

- **Mest angripna resurser**: Anger de specifika resurser som har det högsta antalet aviseringar
- **De vanligaste aviseringarna**: Visar de aviseringstyper som påverkar det största antalet resurser

## <a name="just-in-time-vm-access"></a>Just-in-time-åtkomst till virtuell dator
Minska ytan för nätverkskontakter med en kontrollerad just-in-time-åtkomst till hanteringsportar på virtuella Azure-datorer för att drastiskt minska exponeringen för råstyrkeattacker och andra nätverksattacker.

![Just-in-time-åtkomst till virtuell dator][5]

Ange regler för hur användarna kan ansluta till virtuella datorer. Vid behov kan åtkomst begäras från Security Center eller via PowerShell. Förutsatt att begäran uppfyller reglerna, beviljas automatiskt åtkomst för den begärda tiden.

Mer information finns i [Manage virtual machine access using just in time](security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).

## <a name="adaptive-application-controls"></a>Anpassningsbara programkontroller
Blockera skadliga och andra oönskade program genom att tillämpa rekommendationer som använder maskininlärning och som anpassas efter dina specifika Azure-arbetsbelastningar.

![Anpassningsbara programkontroller][6]

Granska och klicka för att tillämpa de regler i listan med tillåtna program som genereras av Security Center, eller för att redigera regler som redan har konfigurerats.

Mer information finns i [Anpassningsbara programkontroller](security-center-adaptive-application.md).

## <a name="integrate-your-security-solutions"></a>Integrera din säkerhetslösningar
Du kan samla in, söka och analysera säkerhetsdata från olika källor, exempelvis anslutna partnerlösningar som nätverksbrandväggar och andra Microsoft-tjänster i Security Center.

![Integrera säkerhetslösningar][8]

Mer information finns i [Integrera säkerhetslösningar](security-center-partner-integration.md).

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Security Center måste du ha en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Security Centers kostnadsfria prisnivå aktiveras med Azure-prenumerationen. Om du vill använda avancerad säkerhetshantering och hotidentifiering måste du uppgradera till prisnivån Standard. Du kan prova Standard-nivån kostnadsfritt. På [prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/) finns det mer information.
- Om du är redo att aktivera Security Center Standard kan du använda [Snabbstart: Registrera Azure-prenumerationen för Security Center Standard](security-center-get-started.md) som en vägledning.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
