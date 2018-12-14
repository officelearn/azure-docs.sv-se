---
title: Windows Defender Avancerat skydd med Azure Security Center
description: Det här dokumentet introducerar integrering mellan Azure Security Center och Windows Defender Avancerat skydd.
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
ms.date: 09/20/2018
ms.author: barclayn
ms.openlocfilehash: 1109e72d00ccddd6fc0f120ee71c88adaae72fed
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53337605"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Avancerat skydd med Azure Security Center

Azure Security Center utöka utbudet Molnplattformar arbetsbelastning skydd genom att integrera med [Windows Defender Avancerat skydd](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Den här ändringen ger dig omfattande funktioner för slutpunktsidentifiering och svar (EDR). Med Windows Defender ATP-integrering kan du upptäcka avvikelser. Du kan också upptäcka och svara på avancerade attacker på serverslutpunkter som övervakas av Azure Security Center.

Azure Security Center-kunder kan nu använda funktioner i Windows Defender ATP:

- **Nästa generations efter intrång identifiering sensorer**: Windows Defender ATP-sensorer för Windows-servrar samlar in ett brett utbud av beteendeanalys signaler.

- **Analytics-baserat och molndrivna efter intrång identifiering**: Windows Defender ATP snabbt anpassas efter ändra hot. Det använder avancerad analys och stordata. Windows Defender ATP framhävas av Intelligent Security Graph med signaler i Windows, Azure och Office för att identifiera okända hot. Det ger användbara aviseringar och gör att du kan svara snabbt.

- **Hotinformation**: Windows Defender ATP identifierar angripare verktyg, tekniker och procedurer. När den identifierar dessa genererar aviseringar. Det använder data som genereras av Microsoft threat intensiva kampanjperioder och security team, förstärkas med information som tillhandahålls av partner.

Dessa funktioner är nu tillgängliga i Azure Security Center:

- **Automatisk registrering**: Windows Defender ATP-sensorn aktiveras automatiskt för Windows-servrar som är registrerad på Azure Security Center.

- **Enda glasruta**: Azure Security Center-konsolen visar Windows Defender ATP-aviseringar.

- **Detaljerad datorn undersökning**: Azure Security Center-kunder komma åt Windows Defender ATP-konsolen för att utföra en detaljerade undersökningar för att upptäcka omfattningen av ett intrång.

![Azure Security Center, visas en lista över aviseringar och allmän information om varje avisering](media/security-center-wdatp/image1.png)

Du kan [undersöka aviseringen](security-center-investigation.md) i Azure Security Center:

![Aviseringen undersökningsinstrumentpanelen i Azure Security Center](media/security-center-wdatp/image2.png)

Du kan ytterligare undersöka aviseringen genom att pivotera till Windows Defender ATP. Det kan du se ytterligare information, till exempel aviseringar processträdet och incident graph. Du kan också se en tidslinje för detaljerade som visar var beteende för en tidigare period på upp till sex månader.

![Windows Defender ATP-sidan med detaljerad information om en avisering](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plattformssupport

Den här funktionen stöder identifiering på Windows Server 2012 R2 och Windows Server 2016.

Endast servrar i prenumerationer för standard-tjänstnivå stöds.

## <a name="onboarding-instructions"></a>Registreringsanvisningar

Om du vill visa om Windows Defender ATP-integrering aktiverat, Välj **Säkerhetscenter** > **säkerhetsprincip** > **prenumeration**  >  **Redigera inställningar för**.

  ![Azure Security Center-principhantering](media/security-center-wdatp/policy-management.png)

Här kan du se integreringar aktiverad för tillfället.

  ![Inställningssidan för Azure Security Center Threat identifiering med Windows Defender ATP-integrering aktiverat](media/security-center-wdatp/enable-integrations.png)

- Om du redan har integrerats servrar till Azure Security Center standard-nivån, måste du vidta någon ytterligare åtgärd. Azure Security Center att automatiskt publicera servrarna så Windows Defender ATP. Det kan ta upp till 24 timmar.

- Om du har aldrig har integrerats servrar till Azure Security Center standard-nivån kan publicera dem till Azure Security Center som vanligt.

- Om du har publicerat servrar via Windows Defender ATP:
  - Läs dokumentationen för vägledning på [så avregistrera serverdatorer](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Publicera dessa servrar till Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Åtkomst till Windows Defender ATP-portalen

Följ instruktionerna i [tilldela användaråtkomst till portalen](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Ange brandväggskonfigurationen

Om du har en proxy eller brandvägg som blockerar anonym trafik och för att ansluta en Windows Defender ATP-sensorn från systemets kontext, se till att anonym trafik är tillåten. Följ instruktionerna i [ge åtkomst till URL: er i Windows Defender ATP-tjänsten i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-windows-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testa funktionen

Så här genererar en avisering om ofarliga Windows Defender ATP test:

1. Använda Fjärrskrivbord för att komma åt en Windows Server 2012 R2-dator eller en Windows Server 2016 VM.  Öppna ett kommandotolksfönster.

2. Kopiera och kör följande kommando i Kommandotolken. Kommandotolkens fönster stängs automatiskt.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![En kommandotolk med kommandot ovan](media/security-center-wdatp/image4.jpeg)

3. Om kommandot lyckas visas en ny avisering på instrumentpanelen i Azure Security Center och Windows Defender ATP-portalen. Den här aviseringen kan ta några minuter innan den visas.

4. Om du vill granska aviseringen i Security Center går du till **säkerhetsaviseringar** >  **misstänkt Powershell-kommandorad**.

5. I fönstret undersökning markerar du länken som leder till Windows Defender ATP-portalen.

## <a name="next-steps"></a>Nästa steg

- [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
