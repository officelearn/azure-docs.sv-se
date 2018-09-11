---
title: Windows Defender Advanced Threat Protection (ATP) med Azure Security Center (förhandsversion) | Microsoft Docs
description: Det här dokumentet introducerar integrering mellan Azure Security Center och Windows Defender ATP.
services: security-center
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: barclayn
ms.openlocfilehash: f2cb4edd469d76f79e4134ca261bac5263bf3ce4
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296065"
---
# <a name="windows-defender-advanced-threat-protection-atp-with-azure-security-center-public-preview"></a>Windows Defender Advanced Threat Protection (ATP) med Azure Security Center (förhandsversion)

Azure Security Center utöka utbudet molnet arbetsbelastning Protection plattformar (CWPP) genom att integrera med [Windows Defender ATP](https://www.microsoft.com/WindowsForBusiness/windows-atp).
Den här ändringen ger dig omfattande funktioner för slutpunktsidentifiering och svar (EDR). Det kan du upptäcka avvikelser, identifiera och svara på avancerade attacker på serverslutpunkter som övervakas av ASC.

Azure Security Center-kunder kan nu använda Windows Defender ATP-funktioner:

- **Nästa generations efter intrång identifiering sensorer:** Windows Defender ATP-sensorn för Windows-servrar som samlar in ett brett utbud av beteendeanalys signaler att aktivera avancerad attack identifiering och undersökning.

- **Analytics-baserat och molndrivna efter intrång identifiering:** Windows Defender ATP snabbt anpassas till ändra hot. Den använder avancerad analys och stordata. Det är framhävas av Intelligent Security Graph med signaler i Windows, Azure och Office för att identifiera okända hot. Det ger användbara aviseringar och gör att du kan svara snabbt.

- **Hotinformation**: Windows Defender ATP identifierar angripare verktyg, tekniker och procedurer och genererar varningar när de observeras. Det använder data som genereras av Microsoft intensiva kampanjperioder security team, och förstärkas med hotinformation som tillhandahålls av partner.

Dessa funktioner är nu tillgängliga i Azure Security Center:

- Automatisk registrering – Windows Defender ATP-sensorn är automatiskt aktiverat på Windows-servrar som publiceras till ASC

- Enda glasruta i ASC - Windows Defender ATP-aviseringar är tillgängliga i ASC-konsolen

- Detaljerad datorn undersökning - ASC-kunder komma åt Windows Defender ATP-konsolen för att utföra detaljerade undersökningar för att upptäcka omfånget för intrång

![* Bild 1 se hela bilden när du undersöker inklusive aviseringar som genereras av ASC *](media/security-center-wdatp/image1.png)

Du kan undersöka aviseringen i Azure Security Center:

![Bild 2 - undersökning i Azure Security Center](media/security-center-wdatp/image2.png)

Du kan ytterligare undersöka aviseringen genom att pivotera till Windows Defender ATP. Det kan du se ytterligare information som aviseringen processträdet, incident diagram och en tidslinje för detaljerade exponera alla av beteenden för en tidigare period på upp till sex månader.

![Bild 3 undersökning – Windows Defender ATP](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plattformssupport

Den här funktionen stöder identifiering på Windows Server 2012 R2 och Windows Server 2016.

Endast servrar i prenumerationer på standardnivån

## <a name="onboarding-instructions"></a>Registreringsanvisningar

- Om du redan har integrerats servrar till ASC standardnivån - ingen åtgärd krävs ASC kommer publicerar automatiskt servrar till WDATP.

- Om du aldrig har integrerats servrar till ASC-standard-nivån – publicera till ASC som vanligt.

- Om du integrerade servrar via WDATP:
  - Läs dokumentationen för vägledning på [så avregistrera serverdatorer](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Publicera till ASC

## <a name="access-to-wdatp-portal"></a>Åtkomst till WDATP-portalen

Följ anvisningarna på [tilldela användaråtkomst till portalen](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection)

## <a name="set-firewall-configuration"></a>Konfiguration av brandvägg

Om du har en proxy eller brandvägg som blockerar anonym trafik, som Windows Defender ATP-sensorn ansluter från systemets kontext, se till att anonym trafik inte är tillåten. Följ instruktionerna [ge åtkomst till URL: er i Windows Defender ATP-tjänsten i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server)

## <a name="how-can-i-test-the-feature"></a>Hur kan jag testa funktionen?

 Följande steg skapar en ofarliga WDATP test avisering.

1. RDP till någon av Windows Server-datorer (2012 R2 eller 2016) för den prenumerationen och öppna ett kommandotolksfönster

2. Kopiera i Kommandotolken och kör du kommandot nedan. Observera att Kommandotolkens fönster stängs automatiskt.

    **PowerShell.exe - NoExit - ExecutionPolicy kringgå - WindowStyle dolda (New-Object System.Net.WebClient). DownloadFile (”http://127.0.0.1/1.exe” ”, C:\\test-WDATP-test\\invoice.exe”); Starta processen ”C:\\test-WDATP-test\\invoice.exe” **

  ![Bild av kommandot ovan i Kommandotolkens fönster](media/security-center-wdatp/image4.jpeg)

3. Om detta lyckas visas en ny avisering om några minuter i ASC och WD ATP-portalen.

4. Granska aviseringen i Azure Security Center, gå till **säkerhetsaviseringar -\> misstänkt Powershell-kommandorad**

5. Från undersökningen fönstret klickar du på länken för att omdirigera till WDATP portal

## <a name="next-steps"></a>Nästa steg

* [Ange säkerhetsprinciper i Azure Security Center](security-center-policies.md) – Här får du lära dig hur du ställer in säkerhetsprinciper för prenumerationer och resursgrupper i Azure.
* [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md) – Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig att övervaka hälsotillståndet hos dina Azure-resurser.
