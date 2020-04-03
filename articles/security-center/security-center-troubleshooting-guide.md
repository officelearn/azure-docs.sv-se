---
title: Felsökningsguide för Azure Security Center | Microsoft Docs
description: Den här guiden är för IT-proffs, säkerhetsanalytiker och molnadministratörer som behöver felsöka Azure Security Center-relaterade problem.
services: security-center
author: v-miegge
manager: dcscontentpm
ms.service: security-center
ms.topic: conceptual
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 47502e693b897a57517d267924cc6c2752c10440
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585331"
---
# <a name="azure-security-center-troubleshooting-guide"></a>Felsökningsguide för Azure Security Center

Den här guiden riktar sig till de som arbetar med IT, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Security Center och behöver felsöka Security Center-relaterade problem.

Security Center använder Log Analytics-agenten för att samla in och lagra data. Mer information finns under [plattformsmigrering i Azure Security Center](security-center-platform-migration.md). Informationen i den här artikeln representerar Security Center-funktionen efter övergången till Log Analytics-agenten.

## <a name="troubleshooting-guide"></a>Felsökningsguide

I den här guiden förklaras hur du felsöker Security Center-relaterade problem.

Varningstyper:

* Virtual Machine Behavioral Analysis (VMBA)
* Nätverksanalys
* SQL Database- och SQL Data Warehouse-analys
* Sammanhangsbaserad information

Beroende på aviseringstypen kan kunderna hämta den information som krävs för att undersöka aviseringen med hjälp av följande resurser:

* Säkerhetsloggarna i händelsevisaren för virtuell dator i Windows
* AuditD i Linux
* Azure-aktivitetsloggarna och aktivera diagnostikloggar på angreppsresursen.

Kunder kan dela feedback om aviseringens beskrivning och relevans. Gå till själva aviseringen och välj knappen **Hade du nytta av detta?**. Välj orsak och ange en kommentar för att förklara din feedback. Vi övervakar ständigt den här feedbackkanalen för att förbättra våra aviseringar.

## <a name="audit-log"></a>Granskningslogg

Den mesta felsökningen i Security Center kommer att ske genom att först granska [Granskningslogg](../azure-monitor/platform/platform-logs-overview.md)-posterna för den felaktiga komponenten. Via granskningsloggarna kan du fastställa:

* Vilka åtgärder som har vidtagits
* Vem som initierade åtgärden
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Granskningsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) som utförs på dina resurser, men omfattar inte läsåtgärder (GET).

## <a name="log-analytics-agent"></a>Log Analytics-agent

Security Center använder Log Analytics-agenten – det här är samma agent som används av Azure Monitor-tjänsten – för att samla in säkerhetsdata från dina virtuella Azure-datorer. När datainsamling är aktiverat och agenten är korrekt installerad i måldatorn, ska de här processerna köras:

* HealthService.exe

Om du öppnar konsolen för hantering av tjänster (services.msc) visas även tjänsten Log Analytics-agent som körs enligt nedan:

![Tjänster](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig5.png)

Om du vill se vilken version av agenten du har öppnar du **Aktivitetshanteraren**på fliken **Processer** leta reda på **Log Analytics-agenttjänsten**, högerklickar på den och klickar på **Egenskaper**. Filversionen visas på fliken **Information** enligt nedan:

![Fil](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig6.png)

## <a name="log-analytics-agent-installation-scenarios"></a>Installationsscenarier för logganalysagent

Det finns två installationsscenarier som kan ge olika resultat när du installerar Log Analytics-agenten på datorn. Scenarier som stöds är:

* **Agent som installeras automatiskt av Security Center**: i det här scenariot kommer du att kunna visa aviseringarna på båda platser, Security Center och Loggsökning. Du får e-postmeddelanden till den e-postadress som har konfigurerats i säkerhetsprincipen för den prenumeration som resursen tillhör.

* **Agent som installeras manuellt på en virtuell dator i Azure:** i det här scenariot, om du använder agenter som hämtats och installerats manuellt före februari 2017, kan du visa aviseringarna i Security Center-portalen endast om du filtrerar på prenumerationen som arbetsytan tillhör. Om du filtrerar på den prenumeration som resursen tillhör visas inga aviseringar. Du får e-postmeddelanden till den e-postadress som har konfigurerats i säkerhetsprincipen för den prenumeration som arbetsytan tillhör.

> [!NOTE]
> Kontrollera att du laddar ned den senaste versionen av agenten för att undvika det andra scenariot.

## <a name="monitoring-agent-health-issues"></a>Övervaka problem med hälsotillstånd <a name="mon-agent"></a>

**Övervakningstillstånd** definierar anledningen till att Security Center inte kan övervaka virtuella datorer och datorer som initierats för automatisk etablering. I följande tabell visas steg för värden, beskrivningar och lösningar för **övervakningstillstånd**.

| Övervakningstillstånd | Beskrivning | Lösningsanvisningar |
|---|---|---|
| Väntande agentinstallation | Installationen av Log Analytics-agenten körs fortfarande.  Installationen kan ta upp till några timmar. | Vänta tills den automatiska installationen är slutförd. |
| Energisparläge inaktiverat | Den virtuella datorn har stoppats.  Log Analytics-agenten kan bara installeras på en virtuell dator som körs. | Starta om den virtuella datorn. |
| Azure VM-agent saknas eller är ogiltig | Log Analytics-agenten har inte installerats ännu.  En giltig agent för virtuella Azure-datorer krävs för att Security Center ska installera tillägget. | Installera, installera om eller uppgradera agenten för virtuella Azure-datorer på den virtuella datorn. |
| VM-status inte är redo för installation  | Log Analytics-agenten är inte installerad ännu eftersom den virtuella datorn inte är klar för installation. Den virtuella datorn är inte redo för installation på grund av ett problem med VM-agenten eller VM-etableringen. | Kontrollera den virtuella datorns status. Återgå till **Virtual Machines** i portalen och välj den virtuella datorn för att få statusinformation. |
|Installationen misslyckades – allmänt fel | Log Analytics-agenten installerades men misslyckades på grund av ett fel. | [Installera tillägget manuellt](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) eller avinstallera tillägget så försöker Security Center att installera det igen. |
| Installationen misslyckades – den lokala agenten är redan installerad | Installationen av Log Analytics-agenten misslyckades. Security Center identifierade en lokal agent (Log Analytics eller System Center Operations Manager) som redan är installerad på den virtuella datorn. För att undvika konfiguration med flera mål, där den virtuella datorn rapporterar till två separata arbetsytor, har installationen av Log Analytics-agenten stoppats. | Det finns två sätt att lösa detta: [att installera tillägget manuellt](../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension) och ansluta det till önskad arbetsyta. Du kan även ange den önskade arbetsytan som standardarbetsyta och aktivera automatisk etablering av agenten.  Se [aktivera automatisk etablering](security-center-enable-data-collection.md). |
| Agenten kan inte ansluta till arbetsytan | Log Analytics-agenten installerad men misslyckades på grund av nätverksanslutningen.  Kontrollera att du har internetåtkomst eller att en giltig HTTP-proxy har konfigurerats för agenten. | Se felsöka nätverkskrav för övervakningsagenten. |
| Agenten ansluten till arbetsyta som saknas eller är okänd | Security Center identifierade att Log Analytics-agenten som är installerad på den virtuella datorn är ansluten till en arbetsyta som den inte har åtkomst till. | Det kan hända i två fall. Arbetsytan togs bort eller finns inte längre. Installera om agenten med rätt arbetsyta eller avinstallera agenten och tillåt att Security Center slutför sin automatiska etableringsinstallation. Det andra fallet är när arbetsytan är en del av en prenumeration som Security Center inte har behörighet till. Security Center kräver att prenumerationer tillåter att Microsoft Security Resource Provider får åtkomst till dem. Aktivera genom att registrera prenumerationen på Microsoft Security Resource Provider. Du kan göra det via API, PowerShell, portalen eller helt enkelt genom att filtrera prenumerationen i Security Centers **översiktsinstrumentpanel**. Mer information finns i [Resursproviders och resurstyper](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). |
| Agenten svarar inte eller saknar ID | Security Center kan inte hämta säkerhetsdata som genomsökts från den virtuella datorn, trots att agenten är installerad. | Agenten rapporterar inga data, inte heller pulsslag. Agenten kan vara skadad eller så är det något som blockerar trafiken. Eller så rapporterar agenten data men saknar ett Azure-resurs-ID så det är omöjligt att matcha data med Den virtuella Azure-datorn. Mer om du vill felsöka Linux finns i [Felsökningsguide för Log Analytics Agent för Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md#im-not-seeing-any-linux-data-in-the-oms-portal). Om du vill felsöka i Windows läser du [Felsökning av virtuella Windows-datorer](https://github.com/MicrosoftDocs/azure-docs/blob/8c53ac4371d482eda3d85819a4fb8dac09996a89/articles/log-analytics/log-analytics-azure-vm-extension.md#troubleshooting-windows-virtual-machines). |
| Agenten har inte installerats | Datainsamling är inaktiverat. | Aktivera datainsamling i säkerhetsprincipen eller installera Log Analytics-agenten manuellt. |

## <a name="troubleshooting-monitoring-agent-network-requirements"></a>Felsöka nätverkskrav för övervakningsagenten <a name="mon-network-req"></a>

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

Vissa problem kan identifieras med hjälp av riktlinjerna i den här artikeln, andra hittar du också dokumenterade i Security Centers offentliga [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter). Men om du behöver ytterligare felsökning kan du öppna en ny supportbegäran med **Azure-portalen** som visas nedan:

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Se även

I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och driftsguide för Azure Security Center](security-center-planning-and-operations-guide.md) – Lär dig hur du planerar och förstår designövervägandena om att anta Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och svarar på säkerhetsaviseringar
* [Förstå säkerhetsaviseringar i Azure Security Center](security-center-alerts-type.md)
* [Självstudie: Reagera på säkerhetsincidenter](tutorial-security-incident.md)
* [Aviseringsverifiering i Azure Security Center](security-center-alert-validation.md)
* [E-postmeddelanden i Azure Security Center](security-center-provide-security-contact-details.md)
* [Hantera säkerhetsincidenter i Azure Security Center](security-center-incident.md)
* [Identifieringsfunktioner i Azure Security Center](security-center-detection-capabilities.md)
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar hälsostatusen för dina partnerlösningar.
* [Vanliga frågor och svar om Azure Security Center](faq-general.md) – Här finns vanliga frågor om tjänsten.
* [Azure Security Blog](https://blogs.msdn.com/b/azuresecurity/) – Hitta blogginlägg om Azure-säkerhet och efterlevnad
