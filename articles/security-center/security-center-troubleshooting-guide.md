---
title: Felsökningsguide för Azure Security Center | Microsoft Docs
description: Den här guiden är för IT-proffs, säkerhetsanalytiker och moln administratörer som behöver felsöka Azure Security Center relaterade problem.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 55a4aa066739203f8697fb4c9083869f5a05ef4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79282671"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Felsökningsguide för Azure Security Center

Den här guiden riktar sig till de som arbetar med IT, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Security Center och behöver felsöka Security Center-relaterade problem.

Security Center använder Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns under [plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Centers funktionalitet efter övergången till Microsoft Monitoring Agent.

## <a name="troubleshooting-guide"></a>Felsökningsguide

I den här guiden förklaras hur du felsöker Security Center-relaterade problem.

Aviserings typer:

* Virtual Machine Behavioral Analysis (VMBA)
* Nätverksanalys
* SQL Database- och SQL Data Warehouse-analys
* Sammanhangsbaserad information

Beroende på aviseringstypen kan kunderna hämta den information som krävs för att undersöka aviseringen med hjälp av följande resurser:

* Säkerhetsloggarna i händelsevisaren för virtuell dator i Windows
* AuditD i Linux
* Azure-aktivitets loggarna och aktivera diagnostikloggar på angrepps resursen.

För vissa aviseringar har vi också en förtroende poäng. Förtroendepoäng i **Security Center** hjälper ditt team bedöma och prioritera aviseringar. **Security Center** använder automatiskt bransch bästa praxis, intelligenta algoritmer och processer som används av analytiker för att avgöra om ett hot är giltigt och ger meningsfulla insikter i form av en förtroende poäng.

Kunder kan dela feedback om aviseringens beskrivning och relevans. Gå till själva aviseringen och välj knappen **Hade du nytta av detta?** . Välj orsak och ange en kommentar för att förklara din feedback. Vi övervakar ständigt den här feedbackkanalen för att förbättra våra aviseringar.

## <a name="audit-log"></a>Gransknings logg

Den mesta felsökningen i Security Center kommer att ske genom att först granska [Granskningslogg](../azure-monitor/platform/platform-logs-overview.md)-posterna för den felaktiga komponenten. Via granskningsloggarna kan du fastställa:

* Vilka åtgärder som har vidtagits
* Vem som initierade åtgärden
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Granskningsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) som utförs på dina resurser, men omfattar inte läsåtgärder (GET).

## <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent

Security Center använder Microsoft Monitoring Agent – det här är samma agent som används av Azure Monitor-tjänsten – för att samla in säkerhets data från dina virtuella Azure-datorer. När datainsamling är aktiverat och agenten är korrekt installerad i måldatorn, ska de här processerna köras:

* HealthService.exe

Om du öppnar konsolen för tjänsthantering (services.msc) kan du även se att tjänsten Microsoft Monitoring Agent körs enligt nedan:

![Tjänster](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Om du vill se vilken version av agenten du har kan du öppna **Aktivitetshanteraren**i fliken **processer**, leta upp tjänsten **Microsoft Monitoring Agent**, högerklicka på den och klicka på **Egenskaper**. Filversionen visas på fliken **Information** enligt nedan:

![Fil](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="microsoft-monitoring-agent-installation-scenarios"></a>Scenarier för installation av Microsoft Monitoring Agent

Det finns två installationsscenarier som kan ge olika resultat när du installerar Microsoft Monitoring Agent på datorn. Scenarier som stöds är:

* **Agent som installeras automatiskt av Security Center**: i det här scenariot kommer du att kunna visa aviseringarna på båda platser, Security Center och Loggsökning. Du får e-postaviseringar till den e-postadress som har kon figurer ATS i säkerhets principen för den prenumeration som resursen tillhör.

* **Agent installeras manuellt på en virtuell dator som finns i Azure**: i det här scenariot kan du, om du använder agenter som har hämtats och installerats manuellt före februari 2017, bara Visa aviseringarna i Security Center portal om du filtrerar på prenumerationen som arbets ytan tillhör. Om du filtrerar på prenumerationen som resursen tillhör visas inga aviseringar. Du får e-postaviseringar till den e-postadress som har kon figurer ATS i säkerhets principen för prenumerationen som arbets ytan tillhör.

> [!NOTE]
> Kontrollera att du laddar ned den senaste versionen av agenten för att undvika det andra scenariot.

## Övervaka problem med hälsotillstånd <a name="mon-agent"></a>

**Övervakningstillstånd** definierar anledningen till att Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk etablering. I följande tabell visas steg för värden, beskrivningar och lösningar för **övervakningstillstånd**.

| Övervakningstillstånd | Beskrivning | Lösningsanvisningar |
|---|---|---|
| Väntande agentinstallation | Installationen av Microsoft Monitoring Agent körs fortfarande.  Installationen kan ta upp till några timmar. | Vänta tills den automatiska installationen är slutförd. |
| Energisparläge inaktiverat | Den virtuella datorn har stoppats.  Du kan bara installera Microsoft Monitoring Agent på en virtuell dator som körs. | Starta om den virtuella datorn. |
| Azure VM-agent saknas eller är ogiltig | Microsoft Monitoring Agent är inte installerat ännu.  En giltig agent för virtuella Azure-datorer krävs för att Security Center ska installera tillägget. | Installera, installera om eller uppgradera agenten för virtuella Azure-datorer på den virtuella datorn. |
| VM-status inte är redo för installation  | Microsoft Monitoring Agent har inte installerats ännu eftersom den virtuella datorn inte är redo för installation. Den virtuella datorn är inte redo för installation på grund av ett problem med VM-agenten eller VM-etableringen. | Kontrollera den virtuella datorns status. Återgå till **Virtual Machines** i portalen och välj den virtuella datorn för att få statusinformation. |
|Installationen misslyckades – allmänt fel | Microsoft Monitoring Agent installerades men misslyckades på grund av ett fel. | [Installera tillägget manuellt](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) eller avinstallera tillägget så försöker Security Center att installera det igen. |
| Installationen misslyckades – den lokala agenten är redan installerad | Installationen av Microsoft Monitoring Agent misslyckades. Security Center identifierat en lokal agent (Log Analytics eller System Center Operations Manager) som redan har installerats på den virtuella datorn. För att undvika konfiguration av flera värdar, där den virtuella datorn rapporterar till två separata arbetsytor, stoppades installationen av Microsoft Monitoring Agent. | Det finns två sätt att lösa detta: [att installera tillägget manuellt](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) och ansluta det till önskad arbetsyta. Du kan även ange den önskade arbetsytan som standardarbetsyta och aktivera automatisk etablering av agenten.  Se [aktivera automatisk etablering](security-center-enable-data-collection.md). |
| Agenten kan inte ansluta till arbetsytan | Microsoft Monitoring Agent installerades men misslyckades på grund av ett fel med nätverksanslutningen.  Kontrollera att du har internetåtkomst eller att en giltig HTTP-proxy har konfigurerats för agenten. | Se övervaknings agentens nätverks krav. |
| Agenten ansluten till arbetsyta som saknas eller är okänd | Security Center identifierade att Microsoft Monitoring Agent som är installerat på den virtuella datorn är anslutet till en arbetsyta som det inte har åtkomst till. | Det kan hända i två fall. Arbetsytan togs bort eller finns inte längre. Installera om agenten med rätt arbetsyta eller avinstallera agenten och tillåt att Security Center slutför sin automatiska etableringsinstallation. Det andra fallet är när arbetsytan är en del av en prenumeration som Security Center inte har behörighet till. Security Center kräver att prenumerationer tillåter att Microsoft Security Resource Provider får åtkomst till dem. Aktivera genom att registrera prenumerationen på Microsoft Security Resource Provider. Du kan göra det via API, PowerShell, portalen eller helt enkelt genom att filtrera prenumerationen i Security Centers **översiktsinstrumentpanel**. Mer information finns i [Resursproviders och resurstyper](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Agenten svarar inte eller saknar ID | Security Center kan inte hämta säkerhetsdata som genomsökts från den virtuella datorn, trots att agenten är installerad. | Agenten rapporterar inga data, inte heller pulsslag. Agenten kan vara skadad eller så är det något som blockerar trafiken. Eller så rapporterar agenten data men saknar Azure-resurs-ID så att det är omöjligt att matcha data till den virtuella Azure-datorn. För att felsöka Linux, se [fel söknings guide för Log Analytics agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Om du vill felsöka i Windows läser du [Felsökning av virtuella Windows-datorer](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agenten har inte installerats | Datainsamling är inaktiverat. | Aktivera datainsamling i säkerhetsprincipen eller installera Microsoft Monitoring Agent manuellt. |

## Felsöka nätverkskrav för övervakningsagenten <a name="mon-network-req"></a>

Agenter att ansluta till och registrera med Security Center, måste de ha åtkomst till nätverksresurser, inklusive portnummer och URL: er för domänen.

* För proxyservrar måste du se till att lämpliga proxy serverresurser konfigureras i agentinställningarna. Mer information om att [ändra proxyinställningarna](https://docs.microsoft.com/azure/log-analytics/log-analytics-windows-agents) finns i den här artikeln.
* Om du använder en brandvägg för att begränsa åtkomsten till Internet, måste du konfigurera brandväggen att tillåta åtkomst till Log Analytics. Ingen åtgärd krävs i agentinställningarna.

I följande tabell visas resurser som krävs för kommunikation.

| Agentresurs | Portar | Kringgå HTTPS-kontroll |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ja |
| *.oms.opinsights.azure.com | 443 | Ja |
| *.blob.core.windows.net | 443 | Ja |
| *.azure-automation.net | 443 | Ja |

Om du får problem med att komma igång med agenten, kan du hitta mer information i artikeln [Felsökning av problem med att komma igång med Operations Management Suite](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

## <a name="troubleshooting-endpoint-protection-not-working-properly"></a>Felsökning om Endpoint Protection inte fungerar korrekt

Gästagenten är den överordnade processen av allt tillägget [Microsoft Antimalware](../security/fundamentals/antimalware.md) gör. När gästagentprocessen misslyckas kan även Microsoft Antimalware som körs som en underordnad process för gästagenten misslyckas.  I scenarier som dessa rekommenderas det att verifiera följande alternativ:

* Om den virtuella datorn som är målet är en anpassad avbildning och skaparen av den virtuella datorn aldrig installerade gästagenten.
* Om målet är en virtuell Linux-dator i stället för en virtuell Windows-dator så kommer installationen av Windows-versionen av tillägget mot skadlig kod att misslyckas. Linux-gästagenten har särskilda krav gällande OS-version och nödvändiga paket. Om de här kraven inte uppfylls kommer inte VM-agenten att fungera där heller.
* Om den virtuella datorn skapades med en äldre version av gästagenten. Om den var det bör du vara medveten om att vissa gamla agenter inte kan uppdateras automatiskt till nyare versioner och att detta kan leda till det här problemet. Använd alltid den senaste versionen av gästagenten om du skapar dina egna avbildningar.
* Vissa administrationsprogram från tredje part kan inaktivera gästagenten eller blockera åtkomst till vissa filsökvägar. Om du har ett program från tredje part installerad på den virtuella datorn bör du kontrollera så att agenten är med på undantagslistan.
* Vissa brandväggsinställningar och nätverkssäkerhetsgrupper (NSG) blockerar nätverkstrafik till och från gästagenten.
* Vissa åtkomstkontrollistor (ACL) förhindrar åtkomst till disken.
* Otillräckligt diskutrymme kan blockera gästagenten från att fungera korrekt.

Som standard är användargränssnittet för Microsoft Antimalware inaktiverat, se [Enabling Microsoft Antimalware User Interface on Azure Resource Manager VMs Post Deployment](https://blogs.msdn.microsoft.com/azuresecurity/2016/03/09/enabling-microsoft-antimalware-user-interface-post-deployment/) (Aktivera användargränssnittet för Microsoft Antimalware på virtuella Azure Resource Manager-datorer efter distribution) för mer information om hur du aktiverar det om det behövs.

## <a name="troubleshooting-problems-loading-the-dashboard"></a>Felsöka problem med att läsa in instrumentpanelen

Om du har problem med att läsa in instrumentpanelen för Security Center ska du kontrollera att användaren som registrerar prenumerationen på Security Center (dvs. den första användaren som öppnade Security Center med prenumerationen) och användaren som vill aktivera datasamling är *ägare* eller *deltagare* i prenumerationen. Från det ögonblicket kan även användare som är *läsare* i prenumerationen se instrumentpanelen/aviseringar/rekommendationer/policy.

## <a name="contacting-microsoft-support"></a>Kontakta Microsoft Support

Vissa problem kan identifieras med hjälp av riktlinjerna i den här artikeln, andra hittar du också dokumenterade i Security Centers offentliga [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter). Om du behöver ytterligare felsökning kan du öppna en ny supportbegäran med hjälp av **Azure Portal** enligt nedan:

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och användningsguide för Azure Security Center](security-center-planning-and-operations-guide.md) – Lär dig mer om planering och viktiga designaspekter när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Här får du lära dig hur du hanterar och åtgärdar säkerhetsaviseringar.
* [Förstå säkerhetsaviseringar i Azure Security Center](security-center-alerts-type.md)
* [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)
* [Aviseringsverifiering i Azure Security Center](security-center-alert-validation.md)
* [E-postmeddelanden i Azure Security Center](security-center-provide-security-contact-details.md)
* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Förtroendepoäng för avisering](security-center-secure-score.md)
* [Undersöka incidenter och aviseringar i Azure Security Center](security-center-investigation.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](faq-general.md) – Här hittar du vanliga frågor och svar om tjänsten
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure
