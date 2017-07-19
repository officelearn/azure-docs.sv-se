---
title: "Felsökningsguide för Azure Security Center | Microsoft Docs"
description: "Det här dokumentet hjälper dig att felsöka problem i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: fa70dffc2a4bade44e1dec583bdfcf7b5dae6801
ms.contentlocale: sv-se
ms.lasthandoff: 06/17/2017


---
# <a name="azure-security-center-troubleshooting-guide"></a>Felsökningsguide för Azure Security Center
Den här guiden riktar sig till de som arbetar med IT, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Security Center och behöver felsöka Security Center-relaterade problem.

>[!NOTE] 
>Från och med tidig juni 2017 använder Security Center Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns under [plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.
>

## <a name="troubleshooting-guide"></a>Felsökningsguide
I den här guiden förklaras hur du felsöker Security Center-relaterade problem. Den mesta felsökningen i Security Center kommer att ske genom att först granska [Granskningslogg](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/)-posterna för den felaktiga komponenten. Via granskningsloggarna kan du fastställa:

* Vilka åtgärder som har vidtagits
* Vem som initierade åtgärden
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Granskningsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) som utförs på dina resurser, men omfattar inte läsåtgärder (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
Security Center använder Microsoft Monitoring Agent (samma agent används av Operations Management Suite och Log Analytics-tjänsten) för att samla in säkerhetsdata från dina virtuella Azure-datorer. När datainsamling är aktiverat och agenten är korrekt installerad i måldatorn, ska de här processerna köras:

* HealthService.exe

Om du öppnar konsolen för tjänsthantering (services.msc) kan du även se att tjänsten Microsoft Monitoring Agent körs enligt nedan:

![Tjänster](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Om du vill se vilken version av agenten du har kan du öppna **Aktivitetshanteraren**i fliken **processer**, leta upp tjänsten **Microsoft Monitoring Agent**, högerklicka på den och klicka på **Egenskaper**. Filversionen visas på fliken **Information** enligt nedan:

![Fil](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)
   

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scenarier för installation av Microsoft Monitoring Agent
Det finns två installationsscenarier som kan ge olika resultat när du installerar Microsoft Monitoring Agent på datorn. Scenarier som stöds är:

* **Agent som installeras automatiskt av Security Center**: i det här scenariot kommer du att kunna visa aviseringarna på båda platser, Security Center och Loggsökning. Du får e-postaviseringar till e-postadressen som konfigurerats i säkerhetsprincipen för prenumerationen som resursen hör till.
.
* **Agenten installeras manuellt på en virtuell dator i Azure**: i det här scenariot, om du använder agenter som har hämtats och installerats manuellt före februari 2017, kommer du endast att kunna visa aviseringarna i Security Center portal om du filtrerar i prenumerationen som arbetsytan tillhör. Om du filtrerar i prenumerationen som resursen tillhör kommer du inte att kunna se några aviseringar. Du får e-postaviseringar till e-postadressen som konfigurerats i säkerhetsprincipen för prenumerationen som arbetsytan hör till.

>[!NOTE]
> Kontrollera att du laddar ned den senaste versionen av agenten för att undvika detta problem.
> 

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Felsöka nätverkskrav för övervakningsagenten
Agenter att ansluta till och registrera med Security Center, måste de ha åtkomst till nätverksresurser, inklusive portnummer och URL: er för domänen.

- För proxyservrar måste du se till att lämpliga proxy serverresurser konfigureras i agentinställningarna. Mer information om att [ändra proxyinställningarna](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-windows-agents#configure-proxy-settings) finns i den här artikeln.
- Om du använder en brandvägg för att begränsa åtkomsten till Internet, måste du konfigurera brandväggen att tillåta åtkomst till OMS. Ingen åtgärd krävs i agentinställningarna.

I följande tabell visas resurser som krävs för kommunikation.

| Agentresurs | Portar | Kringgå HTTPS-kontroll |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ja |
| *.oms.opinsights.azure.com | 443 | Ja |
| *.blob.core.windows.net | 443 | Ja |
| *.azure-automation.net | 443 | Ja |

Om du får problem med att komma igång med agenten, kan du hitta mer information i artikeln [Felsökning av problem med att komma igång med Operations Management Suite](https://support.microsoft.com/en-us/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).


## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Felsökning om Endpoint Protection inte fungerar korrekt

Gästagenten är den överordnade processen av allt tillägget [Microsoft Antimalware](../security/azure-security-antimalware.md) gör. När gästagentprocessen misslyckas kan även Microsoft Antimalware som körs som en underordnad process för gästagenten misslyckas.  I scenarier som dessa rekommenderas det att verifiera följande alternativ:

- Om den virtuella datorn som är målet är en anpassad avbildning och skaparen av den virtuella datorn aldrig installerade gästagenten.
- Om målet är en virtuell Linux-dator i stället för en virtuell Windows-dator så kommer installationen av Windows-versionen av tillägget mot skadlig kod att misslyckas. Linux-gästagenten har särskilda krav gällande OS-version och nödvändiga paket. Om de här kraven inte uppfylls kommer inte VM-agenten att fungera där heller. 
- Om den virtuella datorn skapades med en äldre version av gästagenten. Om den var det bör du vara medveten om att vissa gamla agenter inte kan uppdateras automatiskt till nyare versioner och att detta kan leda till det här problemet. Använd alltid den senaste versionen av gästagenten om du skapar dina egna avbildningar.
- Vissa administrationsprogram från tredje part kan inaktivera gästagenten eller blockera åtkomst till vissa filsökvägar. Om du har ett program från tredje part installerad på den virtuella datorn bör du kontrollera så att agenten är med på undantagslistan.
- Vissa brandväggsinställningar och nätverkssäkerhetsgrupper (NSG) blockerar nätverkstrafik till och från gästagenten.
- Vissa åtkomstkontrollistor (ACL) förhindrar åtkomst till disken.
- Otillräckligt diskutrymme kan blockera gästagenten från att fungera korrekt. 

Som standard är användargränssnittet för Microsoft Antimalware inaktiverat, se [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Aktivera användargränssnittet för Microsoft Antimalware på virtuella Azure Resource Manager-datorer efter distribution) för mer information om hur du aktiverar det om det behövs.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Felsöka problem med att läsa in instrumentpanelen

Om du har problem med att läsa in instrumentpanelen för Security Center ska du kontrollera att användaren som registrerar prenumerationen på Security Center (dvs. den första användaren som öppnade Security Center med prenumerationen) och användaren som vill aktivera datasamling är *ägare* eller *deltagare* i prenumerationen. Från det ögonblicket kan även användare som är *läsare* i prenumerationen se instrumentpanelen/aviseringar/rekommendationer/policy.

## <a name="contacting-microsoft-support"></a>Kontakta Microsoft Support
Vissa problem kan identifieras med hjälp av riktlinjerna i den här artikeln, andra hittar du också dokumenterade i Security Centers offentliga [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter). Om du behöver ytterligare felsökning kan du öppna en ny supportbegäran med hjälp av **Azure Portal** enligt nedan: 

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Se även
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md) – Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsvarningar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure


