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
ms.date: 01/04/2016
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 9ed6eebd8a0c11158f9812edfc15b29a70ccc905


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

Med Azures säkerhetsövervakningstillägg söks olika säkerhetsrelevanta konfigurationer igenom och säkerhetsloggar från virtuella datorer samlas in. Genomsökningshanteraren används som en genomsökare av korrigeringsfiler.

Om installationen har utförts bör du se en post som liknar den nedan i granskningsloggarna för den virtuella måldatorn:

![Granskningsloggar](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig1.png)

Du kan också få mer information om installationsprocessen genom att läsa de agentloggar som finns på *%systemdrive%\windowsazure\logs* (exempel: C:\WindowsAzure\Logs).

> [!NOTE]
> Om Azure Security Center-agenten beter sig felaktigt måste du starta om den virtuella måldatorn eftersom det inte finns något kommando för att stoppa och starta agenten.
> 
> 

## <a name="troubleshooting-monitoring-agent-installation-in-linux"></a>Felsöka installationen av övervakningsagenten i Linux
När du felsöker en VM-agentinstallation i ett Linux-system bör du kontrollera att tillägget har laddats ned till /var/lib/waagent/. Du kan köra kommandot nedan för att kontrollera om det är installerat:

`cat /var/log/waagent.log` 

Andra loggfiler som du kan granska i felsökningssyfte är: 

* /var/log/mdsd.err
* /var/log/azure/

Du bör se en anslutning till mdsd-processen på TCP 29130 i ett fungerande system. Det här är syslog som kommunicerar med mdsd-processen. Du kan verifiera det här beteendet genom att köra kommandot nedan:

`netstat -plantu | grep 29130`

## <a name="contacting-microsoft-support"></a>Kontakta Microsoft Support
Vissa problem kan identifieras med hjälp av riktlinjerna i den här artikeln, andra hittar du också dokumenterade i Security Centers offentliga [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSecurityCenter). Om du behöver ytterligare felsökning kan du öppna en ny supportbegäran med hjälp av Azure Portal enligt nedan: 

![Microsoft Support](./media/security-center-troubleshooting-guide/security-center-troubleshooting-guide-fig2.png)

## <a name="see-also"></a>Se även
I det här avsnittet har vi berättat hur du ställer in säkerhetsprinciper i Azure Security Center. I följande avsnitt kan du lära dig mer om Azure Security Center:

* [Planerings- och bruksanvisning för Azure Security Center](security-center-planning-and-operations-guide.md) – Här får du lära dig att planera och vad du behöver tänka på när det gäller design när du ska börja använda Azure Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar dina Azure-resursers hälsa.
* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – Lär dig hur du hanterar och åtgärdar säkerhetsvarningar.
* [Övervaka partnerlösningar med Azure Security Center](security-center-partner-solutions.md) – Lär dig hur du övervakar dina partnerlösningars hälsostatus.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md) – Här hittar du vanliga frågor och svar om tjänsten
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/) – Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure




<!--HONumber=Dec16_HO1-->


