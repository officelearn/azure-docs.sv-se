---
title: "Vad är Azure Security Center?| Microsoft Docs"
description: "Här får du lära dig om Azure Security Center, de viktigaste funktionerna och hur Security Center fungerar."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Azure Security Center erbjuder enhetlig säkerhetshantering och avancerat skydd mot hot i olika hybridmolnarbetsbelastningar. Med Security Center kan du tillämpa säkerhetsprinciper i arbetsbelastningarna, begränsa hotexponeringen samt identifiera och åtgärda attacker.

Varför ska man använda Security Center?

- **Centraliserad principhantering** – Säkerställ att företagets eller reglerade säkerhetskrav följer standarden genom att hantera principer centralt i alla dina arbetsbelastningar i hybridmoln.
- **Kontinuerlig säkerhetsbedömning** – Övervaka säkerheten i datorer, nätverk, lagring, datatjänster och program så att du kan identifiera potentiella säkerhetsproblem.
- **Rekommendationer som kan åtgärdas** – Åtgärda säkerhetsproblem innan de kan utnyttjas av angripare med hjälp av rangordnade säkerhetsrekommendationer.
- **Avancerat molnförsvar** – Minska hot med just-in-time-åtkomst till hanteringsportar och listor över tillåtna, för att kunna styra program som körs på dina virtuella datorer.
- **Rangordnade aviseringar och incidenter** – Fokusera på de mest kritiska hoten först med rangordnade säkerhetsaviseringar och incidenter.
- **Integrerade säkerhetslösningar** – Samla in, sök efter och analysera säkerhetsdata från flera olika källor, till exempel anslutna partnerlösningar.

**Security Center – Översikt** ger en snabb överblick över säkerhetstillståndet för dina Azure- och icke-Azure-arbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för arbetsbelastningarna samt identifiera och minimera risker. Den inbyggda instrumentpanelen innehåller omedelbara insikter om säkerhetsaviseringar och sårbarheter som kräver uppmärksamhet.

![Översikt][1]

## <a name="centralized-policy-management"></a>Centraliserad principhantering
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Security Center kan du definiera principer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data.

Security Center-principer innehåller följande komponenter:

- **Datainsamling**: Fastställer agentetablering och säkerhetsinställningar för [datainsamlingen](security-center-enable-data-collection.md).
- **Säkerhetsprincip**: Fastställ vilka kontroller Security Center ska övervaka och rekommendera genom att redigera [säkerhetsprincipen](security-center-policies.md).
- **E-postmeddelanden**: Fastställer säkerhetskontakter och inställningar för [e-postmeddelanden](security-center-provide-security-contact-details.md).
- **Prisnivå**: Definierar [prissättningarna](security-center-pricing.md) Kostnadsfri och Standard. Vilken nivå du väljer avgör vilka Security Center-funktioner som blir tillgängliga för resurser i området.

![Säkerhetsprincip][2]

Mer information finns i [Översikt över säkerhetsprinciper](security-center-policies-overview.md).

## <a name="continuous-security-assessment"></a>Kontinuerlig säkerhetsbedömning
Security Center analyserar säkerhetstillståndet för dina beräkningsresurser, virtuella nätverk, lagrings- och datatjänster samt program. Med den kontinuerliga kontrollen identifieras potentiella säkerhetsproblem, till exempel system som saknar säkerhetsuppdateringar eller exponerade nätverksportar. Välj en panel i avsnittet Skydd om du vill se mer information, exempelvis en lista med resurser och eventuella sårbarheter som har hittats.

![Övervakning av säkerhetshälsa][3]

Se [Övervakning av säkerhetshälsa](security-center-monitoring.md) för mer information.

## <a name="actionable-recommendations"></a>Användbara rekommendationer
Säkerhetstillståndet för resurserna i Azure och utanför Azure analyseras i Security Center för upptäckt av eventuella sårbarheter. En lista med rangordnade säkerhetsrekommendationer vägleder dig genom processen att åtgärda säkerhetsproblem.

![Rekommendationer][4]

Se [Hantera säkerhetsrekommendationer](security-center-recommendations.md) för mer information.

## <a name="just-in-time-vm-access"></a>Just-in-time-åtkomst till virtuell dator
Minska ytan för nätverkskontakter med en kontrollerad just-in-time-åtkomst till hanteringsportar på virtuella Azure-datorer för att drastiskt minska exponeringen för råstyrkeattacker och andra nätverksattacker.

![Just-in-time-åtkomst till virtuell dator][5]

Ange regler för hur användarna kan ansluta till virtuella datorer. Vid behov kan åtkomst begäras från Security Center eller via PowerShell. Förutsatt att begäran uppfyller reglerna, beviljas automatiskt åtkomst för den begärda tiden.

Mer information finns i [Manage virtual machine access using just in time](security-center-just-in-time.md) (Hantera åtkomsten till virtuella datorer med Just-In-Time).

## <a name="adaptive-application-controls"></a>Anpassningsbara programkontroller
Blockera skadliga och andra oönskade program genom att tillämpa rekommendationer som använder maskininlärning och som anpassas efter dina specifika Azure-arbetsbelastningar.

![Anpassningsbara programkontroller][6]

Granska och klicka för att tillämpa de regler i listan med tillåtna program som genereras av Security Center, eller för att redigera regler som redan har konfigurerats.

Se [Anpassningsbara programkontroller](security-center-adaptive-application.md) för mer information.

## <a name="prioritized-alerts-and-incidents"></a>Rangordnade aviseringar och incidenter
Security Center använder avancerade analyser och global hotinformation för att upptäcka inkommande attacker och aktiviteter efter intrång. Aviseringar prioriteras och grupperas till incidenter, vilket gör det lättare för dig att fokusera på de mest kritiska hoten först. Du kan även skapa egna anpassade säkerhetsaviseringar.

![Rangordnade aviseringar och incidenter][7]

Du kan snabbt bedöma omfattningen och konsekvenserna av en attack med en visuell och interaktiv undersökning, samt med hjälp av fördefinierade eller ad hoc-frågor som undersöker dina säkerhetsdata mer ingående.

Se [Hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) för mer information.

## <a name="integrate-your-security-solutions"></a>Integrera din säkerhetslösningar
Du kan samla in, söka och analysera säkerhetsdata från olika källor, exempelvis anslutna partnerlösningar som nätverksbrandväggar och andra Microsoft-tjänster i Security Center.

![Integrera säkerhetslösningar][8]

Se [Integrera säkerhetslösningar](security-center-partner-integration.md) för mer information.

## <a name="next-steps"></a>Nästa steg

- För att komma igång med Security Center måste du ha en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Security Centers kostnadsfria prisnivå aktiveras med Azure-prenumerationen. Om du vill använda avancerad säkerhetshantering och hotidentifiering måste du uppgradera till prisnivån Standard. Standardnivån är kostnadsfri de första 60 dagarna. På [prissättningssidan för Security Center](https://azure.microsoft.com/pricing/details/security-center/) finns det mer information.
- Om du är redo att aktivera Security Center Standard kan du använda [Snabbstart: Registrera Azure-prenumerationen för Security Center Standard](security-center-get-started.md) som en vägledning.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
