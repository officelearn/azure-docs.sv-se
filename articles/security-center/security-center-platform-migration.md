---
title: Migrering av Azure Security Center-plattformen | Microsoft Docs
description: "I det här dokumentet beskrivs några ändringar av hur Azure Security Center-data samlas in."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 80246b00-bdb8-4bbc-af54-06b7d12acf58
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/24/2017
ms.author: yurid
ms.openlocfilehash: 89970b50a2f7246a43ac9666be4d992649605cbf
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="azure-security-center-platform-migration"></a>Migrering av Azure Security Center-plattformen

Från och med början av juni 2017 har det gjorts viktiga ändringar av hur säkerhetsdata samlas in och lagras i Azure Security Center.  De här ändringarna möjliggör nya funktioner, som att enkelt kunna söka säkerhetsdata, och insamlingen ligger nu mer i linje med andra tjänster för hantering och övervakning i Azure.

> [!NOTE]
> Plattformsmigreringen bör inte påverka dina produktionsresurser och ingen åtgärd krävs från din sida.


## <a name="whats-happening-during-this-platform-migration"></a>Vad händer under den här plattformsmigreringen?

Tidigare användes Azure Monitoring Agent till att samla in säkerhetsdata från dina virtuella datorer i Security Center. Det här är bland annat information om säkerhetskonfigurationer som används till att identifiera säkerhetsproblem, och säkerhetshändelser som används till att identifiera hot. Dessa data lagrades i dina lagringskonton i Azure.

Från och med nu används Microsoft Monitoring Agent i Security Center (samma agent används av Operations Management Suite och Log Analytics-tjänsten). Data som samlas in från agenten lagras antingen i en befintlig *Log Analytics*-[arbetsyta](../log-analytics/log-analytics-manage-access.md) som är associerad med din Azure-prenumeration eller i en ny arbetsyta med hänsyn till den virtuella datorns geografiska plats.

## <a name="agent"></a>Agent

Som en del av övergången installeras Microsoft Monitoring Agent (för [Windows](../log-analytics/log-analytics-windows-agent.md) eller [Linux](../log-analytics/log-analytics-linux-agents.md)) på alla virtuella Azure-datorer som data samlas in från.  Om den virtuella datorn redan har Microsoft Monitoring Agent installerad används den installerade agenten i Security Center.

Under en tid (vanligtvis några dagar) körs båda agenterna sida vid sida för att säkerställa en smidig övergång utan att data går förlorade. På så sätt kan Microsoft verifiera att den nya pipelinen för data fungerar innan den nuvarande pipelinen avslutas. Därefter tas Azure Monitoring Agent bort från dina virtuella datorer. Ingen åtgärd krävs från din sida. Du får ett e-postmeddelande när alla kunder har migrerats.
 
Du bör inte avinstallera Azure Monitoring Agent manuellt under migreringen eftersom det kan uppstå luckor i säkerhetsdata. Kontakta [Microsofts kundservice och support](https://support.microsoft.com/contactus/) om du behöver mer hjälp. 

För Microsoft Monitoring Agent för Windows krävs användning av TCP-port 443, läs mer i [felsökningsguiden för Azure Security Center](security-center-troubleshooting-guide.md).


> [!NOTE] 
> Eftersom Microsoft Monitoring Agent kan användas av andra tjänster för hantering och övervakning i Azure avinstalleras agenten inte automatiskt när du stänger av datainsamling i Security Center. Du kan däremot avinstallera agenten manuellt om det behövs.

## <a name="workspace"></a>Arbetsyta

Så som beskrivits tidigare lagras data som samlas in från Microsoft Monitoring Agent (för Security Center) antingen i befintliga Log Analytics-arbetsytor som är associerade med din Azure-prenumeration eller i nya arbetsytor med hänsyn till den virtuella datorns geografiska plats.

Du kan bläddra om du vill se en lista med dina Log Analytics-arbetsytor, inklusive alla som skapats av Security Center i Azure Portal. En relaterad resursgrupp skapas för nya arbetsytor. Både har följande namnkonvention:

- Arbetsyta: *DefaultWorkspace-[prenumerations-id]-[geo]*
- Resursgrupp: *DefaultResourceGroup-[geo]* 
 
För arbetsytor som skapats av Security Center sparas data i 30 dagar. För befintliga arbetsytor baseras kvarhållningen på arbetsytans prisnivå.

> [!NOTE]
> Data som tidigare samlats in av Security Center ligger kvar i dina lagringskonton. När migreringen är klar kan du ta bort dessa lagringskonton.

### <a name="oms-security-solution"></a>OMS-säkerhetslösning 

För befintliga kunder som inte har OMS-säkerhetslösningen installerad så installerar Microsoft den på arbetsytan, men endast med virtuella Azure-datorer som mål. Avinstallera inte den här lösningen, eftersom det inte finns något automatiskt sätt att åtgärda detta om du gör det från OMS-hanteringskonsolen.


## <a name="other-updates"></a>Övriga uppdateringar

Tillsammans med plattformsmigreringen lanserar vi några ytterligare mindre uppdateringar:

- Fler OS-versioner stöds. Se listan [här](security-center-faq.md#virtual-machines).
- Listan med OS-säkerhetsproblem utökas. Se listan [här](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335).
- [Prissättningen](https://azure.microsoft.com/pricing/details/security-center/) kommer att räknas om per timme (tidigare var det per dag), vilket leder till kostnadsbesparingar för vissa kunder.
- Insamling av data kommer att krävas och aktiveras automatiskt för kunder på prisnivån Standard.
- Azure Security Center kommer att börja identifiera lösningar mot skadlig kod som inte har distribuerats via Azure-tillägg. Identifiering av Symantec Endpoint Protection och Defender för Windows 2016 blir tillgänglig först.
- Förebyggandeprinciper och -meddelanden kan endast konfigureras på *prenumerationsnivå*, men priser kan fortfarande anges på *resursgruppsnivå*l

