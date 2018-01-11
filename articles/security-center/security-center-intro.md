---
title: "Vad är Azure Security Center? | Microsoft Docs"
description: "Här får du lära dig om Azure Security Center, de viktigaste funktionerna och hur Security Center fungerar."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/08/2018
ms.author: terrylan
ms.openlocfilehash: 50a54b8d2a73807aa9a0217f7ccf971b8c516494
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Azure Security Center ger enhetlig hantering och skydd avancerade över hybrid cloud arbetsbelastningar. Med Security Center kan du tillämpa säkerhetsprinciper på din arbetsbelastning, begränsa din utsättas för hot, och identifiera och svara på attacker.

Varför ska man använda Security Center?

- **Centraliserad hantering av Grupprincip** – säkerställa efterlevnad med företagets eller reglerande säkerhetskrav genom att hantera principer centralt över alla dina hybrid cloud arbetsbelastningar.
- **Kontinuerlig security assessment** – övervaka säkerheten för datorer, nätverk, lagring och data tjänster och program för att identifiera potentiella säkerhetsproblem.
- **Rekommenderade åtgärder** – åtgärda säkerhetsproblem innan de kan utnyttjas av angripare med prioriterad och tillämplig säkerhetsrekommendationer.
- **Avancerade molnet försvar** – minska hot med just-in-time-åtkomst mot hanteringsportar och vitlistning styra program som körs på din virtuella dator.
- **Prioritera aviseringar och incidenter** -fokuserar på de mest kritiska hot först med-prioriteras på säkerhetsaviseringar och incidenter.
- **Integrerad säkerhetslösningar** – samla in, söka och analysera säkerhetsdata från olika källor, till exempel anslutna partnerlösningar.

Den **Security Center - översikt** ger en snabb överblick över säkerhetstillståndet på Azure och Azure-arbetsbelastningar, vilket gör det lättare att identifiera och utvärdera säkerheten för dina arbetsbelastningar och att identifiera och minimera risker. Inbyggda instrumentpanelen innehåller omedelbara insikter om säkerhetsaviseringar och säkerhetsproblem som kräver uppmärksamhet.

![Översikt][1]

## <a name="centralized-policy-management"></a>Centraliserad hantering av principer
En säkerhetsprincip definierar den önskade konfigurationen för arbetsbelastningarna och hjälper till att säkerställa efterlevnaden av företagets eller bestämmelsemässiga säkerhetskrav. I Security Center Definiera principer och anpassa dem till din typ av arbetsbelastning eller känslighet för dina data.

Security Center-principer innehåller följande komponenter:

- **Datainsamling**: avgör agent etablering och säkerhet [datainsamling](security-center-enable-data-collection.md) inställningar.
- **Säkerhetsprincip**: avgöra vilka styr Security Center Övervakare och rekommenderar genom att redigera den [säkerhetsprincip](security-center-policies.md).
- **E-postmeddelanden**: Anger säkerhet kontakter och [meddelanden via e-](security-center-provide-security-contact-details.md) inställningar.
- **Prisnivån**: definierar lediga eller Standard [priser markeringen](security-center-pricing.md). Den nivå som du väljer avgör vilka Security Center-funktioner är tillgängliga för resurser i sitt omfång.

![Säkerhetsprincip][2]

Se [principer Säkerhetsöversikt](security-center-policies-overview.md) för mer information.

## <a name="continuous-security-assessment"></a>Kontinuerlig security assessment
Security Center analyserar säkerhetstillståndet hos dina beräkningsresurser, virtuella nätverk, lagring och datatjänster och program. Kontinuerlig kontroll hjälper dig att identifiera potentiella säkerhetsproblem, till exempel system som saknar säkerhetsuppdateringar eller exponerade nätverksportar. Välj en panel i avsnittet förebyggande om du vill visa mer information, inklusive en lista över resurser och eventuella säkerhetsproblem har identifierats.

![Övervakning av säkerhetshälsa][3]

Se [övervakning av säkerhetshälsa](security-center-monitoring.md) för mer information.

## <a name="actionable-recommendations"></a>Rekommenderade åtgärder
Security Center analyserar säkerhetsläget för Azure och Azure-resurser för att identifiera potentiella säkerhetsrisker. En lista med prioriterade säkerhetsrekommendationer guidar dig genom processen för adressering säkerhetsproblem.

![Rekommendationer][4]

Se [hantera säkerhetsrekommendationer](security-center-recommendations.md) för mer information.

## <a name="just-in-time-vm-access"></a>Just-in-time-åtkomst till virtuell dator
Minska risken för angrepp nätverk med precis i tid, kontrollerad åtkomst till hanteringsportar på Azure Virtual Machines, vilket drastiskt minskar risken för brute force och andra nätverksattacker.

![Just-in-time-åtkomst till virtuell dator][5]

Ange regler för hur användare kan ansluta till virtuella datorer. Vid behov, kan åtkomst begäras från Security Center eller via PowerShell. Förutsatt att begäran uppfyller reglerna, beviljas automatiskt åtkomst för den begärda tiden.

Se [hantera virtuella åtkomst med hjälp av precis i tid](security-center-just-in-time.md) för mer information.

## <a name="adaptive-application-controls"></a>Anpassningsbara programkontroller
Blockera skadlig kod och andra oönskade program genom att använda vitlistning rekommendationer anpassade till din specifika Azure arbetsbelastningar och drivs av maskininlärning.

![Anpassningsbara programkontroller][6]

Granska och klicka om du vill tillämpa de rekommenderade vitlistning av regler som genereras av Security Center eller redigera regler som redan har konfigurerats.

Se [anpassningsbar programkontroller](security-center-adaptive-application.md) för mer information.

## <a name="prioritized-alerts-and-incidents"></a>Prioritetsklassade aviseringar och incidenter
Security Center använder avancerade analyser och globala hotinformation att upptäcka inkommande attacker och bryter mot aktivitet efter. Aviseringar är prioriteras och grupperade i incidenter, hjälpa du fokusera på de mest kritiska hot först. Du kan skapa egna anpassade säkerhetsaviseringar samt.

![Prioritetsklassade aviseringar och incidenter][7]

Du kan snabbt bedöma omfattningen och konsekvenserna av en attack med en undersökning visual, interaktiva upplevelse och använder fördefinierade eller ad hoc-frågor för ingående undersökning av säkerhetsdata.

Se [hantera och åtgärda säkerhetsaviseringar](security-center-managing-and-responding-alerts.md) för mer information.

## <a name="integrate-your-security-solutions"></a>Integrera din säkerhetslösningar
Du kan samla in, söka och analysera säkerhetsdata från olika källor, till exempel anslutna partnerlösningar som nätverkets brandväggar och andra Microsoft-tjänster i Security Center.

![Integrera säkerhetslösningar][8]

Se [integrera säkerhetslösningar](security-center-partner-integration.md) för mer information.

## <a name="next-steps"></a>Nästa steg

- Om du vill komma igång med Security Center, måste en prenumeration på Microsoft Azure. Om du inte har en prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Security Center kostnadsfria prisnivån aktiveras med din Azure-prenumeration. Om du vill dra nytta av avancerad säkerhet hanterings- och hot identifiering, måste du uppgradera till Standard prisnivån. Standard-nivån är kostnadsfri de första 60 dagarna. Finns det [Security Center sida med priser](https://azure.microsoft.com/pricing/details/security-center/) för mer information.
- Om du är redo att aktivera Security Center Standard nu den [Snabbstartsguide: publicera din Azure-prenumeration till Security Center Standard](security-center-get-started.md) vägleder dig genom stegen.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
