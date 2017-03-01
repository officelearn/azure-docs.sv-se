---
title: "Felsökningsguide för Azure Security Center | Microsoft Docs"
description: "Det här dokumentet hjälper dig att felsöka problem i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 44462de6-2cc5-4672-b1d3-dbb4749a28cd
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: b9f4a8b185f9fb06f8991b6da35a5d8c94689367
ms.openlocfilehash: dbbec729c14d0d9dc5781e7a88a1db3f66f7df97


---
# <a name="azure-security-center-troubleshooting-guide"></a>Felsökningsguide för Azure Security Center
Den här guiden riktar sig till de som arbetar med IT, informationssäkerhetsanalytiker och molnadministratörer i organisationer som använder Azure Security Center och behöver felsöka Security Center-relaterade problem.

## <a name="troubleshooting-guide"></a>Felsökningsguide
I den här guiden förklaras hur du felsöker Security Center-relaterade problem. Den mesta felsökningen i Security Center kommer att ske genom att först granska [Granskningslogg](https://azure.microsoft.com/updates/audit-logs-in-azure-preview-portal/)-posterna för den felaktiga komponenten. Via granskningsloggarna kan du fastställa:

* Vilka åtgärder som har vidtagits
* Vem som initierade åtgärden
* När åtgärden utfördes
* Status för åtgärden
* Värdena för andra egenskaper som kan hjälpa dig undersöka åtgärden

Granskningsloggen innehåller alla skrivåtgärder (PUT, POST, DELETE) som utförs på dina resurser, men omfattar inte läsåtgärder (GET).

## <a name="troubleshooting-monitoring-agent-installation-in-windows"></a>Felsöka övervakningsagentinstallation i Windows
Övervakningsagenten i Security Center används för att samla in data. När datainsamling är aktiverat och agenten är korrekt installerad i måldatorn, ska de här processerna köras:

* ASMAgentLauncher.exe – Azure Monitoring Agent 
* ASMMonitoringAgent.exe – tillägg för Azure Security Monitoring
* ASMSoftwareScanner.exe – Azure Scan Manager

Med tillägget Azure Security Monitoring söks olika säkerhetsrelevanta konfigurationer igenom och säkerhetsloggar från virtuella datorer samlas in. Genomsökningshanteraren används som en genomsökare av korrigeringsfiler.

Om installationen har utförts bör du se en post som liknar den nedan i granskningsloggarna för den virtuella måldatorn:

![Granskningsloggar](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Du kan också få mer information om installationsprocessen genom att läsa de agentloggar som finns på *%systemdrive%\windowsazure\logs* (exempel: C:\WindowsAzure\Logs).

> [!NOTE]
> Om Azure Security Center-agenten beter sig felaktigt måste du starta om den virtuella måldatorn eftersom det inte finns något kommando för att stoppa och starta agenten.


Om du fortfarande har problem med insamling av data kan du avinstallera agenten genom att följa stegen nedan:

1. Från **Azure Portal** väljer du den virtuella dator som har problem med datasamling och klickar på **Tillägg**.
2. Högerklicka i **Microsoft.Azure.Security.Monitoring** och klicka på **Avinstallera**.

![Ta bort agenten](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig4.png)

Tillägget Azure Security Monitoring ska återinstalleras automatiskt inom några minuter.

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Felsöka installationen av övervakningsagenten i Linux
När du felsöker en VM-agentinstallation i ett Linux-system bör du kontrollera att tillägget har laddats ned till /var/lib/waagent/. Du kan köra kommandot nedan för att kontrollera om det är installerat:

`cat /var/log/waagent.log` 

Andra loggfiler som du kan granska i felsökningssyfte är: 

* /var/log/mdsd.err
* /var/log/azure/

Du bör se en anslutning till mdsd-processen på TCP 29130 i ett fungerande system. Det här är syslog som kommunicerar med mdsd-processen. Du kan verifiera det här beteendet genom att köra kommandot nedan:

`netstat -plantu | grep 29130`

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




<!--HONumber=Feb17_HO3-->


