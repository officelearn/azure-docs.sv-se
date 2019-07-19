---
title: Windows Defender Avancerat skydd med Azure Security Center
description: Det här dokumentet beskriver integrationen mellan Azure Security Center och Windows Defender Avancerat skydd.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2018
ms.author: v-mohabe
ms.openlocfilehash: 87f5a14bcd6003ad81b663ed97e5349dcbff2a30
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296519"
---
# <a name="windows-defender-advanced-threat-protection-with-azure-security-center"></a>Windows Defender Avancerat skydd med Azure Security Center

Azure Security Center utökar erbjudandet för arbetsbelastningsskydd för molnplattformar genom integration med [Windows Defender Avancerat skydd](https://www.microsoft.com/en-us/WindowsForBusiness/windows-atp) (ATP).
Den här ändringen ger dig omfattande funktioner för identifiering och svar på slutpunkter (EDR). Med Windows Defender ATP-integrering kan du upptäcka avvikelser. Du kan också identifiera och svara på avancerade attacker på Server slut punkter som övervakas av Azure Security Center.

## <a name="windows-defender-atp-features-in-security-center"></a>Windows Defender ATP-funktioner i Security Center

När du använder Windows Defender ATP får du:

- **Nästa generations sensorer för överträdelse avkänning**: Windows Defender ATP-sensorer för Windows-servrar samlar in en omfattande uppsättning beteende signaler.

- **Analys baserad, identifiering av molnbaserade data efter intrång**: Windows Defender ATP anpassas snabbt för att ändra hot. Den använder avancerade analyser och Big data. Windows Defender ATP förstärks av kraften hos Intelligent Security Graph med signaler över Windows, Azure och Office för att identifiera okända hot. Den innehåller aviseringar som kan åtgärdas och gör det möjligt att svara snabbt.

- **Hot information**: Windows Defender ATP identifierar angripare verktyg, tekniker och procedurer. När den identifierar dessa genererar aviseringar. Den använder data som genererats av Microsoft Threat Hunters och säkerhets team, som tillhandahålls av våra partner.

Dessa funktioner är nu tillgängliga i Azure Security Center:

- **Automatiserad onboarding**: Windows Defender ATP-sensorn aktive ras automatiskt för Windows-servrar som har publicerats på Azure Security Center.

- **En ruta med glas**: Azure Security Center-konsolen visar Windows Defender ATP-aviseringar.

- **Detaljerad dator undersökning**: Azure Security Center kunder kan komma åt Windows Defender ATP-konsolen för att utföra en detaljerad undersökning för att få en detaljerad undersökning av omfattningen av en överträdelse.

![Azure Security Center, Visa en lista över aviseringar och allmän information om varje avisering](media/security-center-wdatp/image1.png)

Du kan undersöka aviseringen ytterligare genom att pivotera till Windows Defender ATP. Där kan du se ytterligare information som varnings process trädet och incident diagrammet. Du kan också se en detaljerad dator tids linje som visar varje beteende för en historisk period på upp till sex månader.

![Sidan Windows Defender ATP med detaljerad information om en avisering](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plattformssupport

Windows Defender ATP i Security Center stöder identifiering på Windows Server 2012 R2 och Windows Server 2016-operativsystem som tillhör en tjänst prenumeration med standard typ.

> [!NOTE]
> När du använder Azure Security Center för att övervaka servrar skapas en Windows Defender ATP-klient automatiskt och Windows Defender ATP-data lagras i Europa som standard. Om du behöver flytta dina data till en annan plats måste du kontakta Microsoft Support för att återställa klienten.

## <a name="onboarding-servers-to-security-center"></a>Onboarding-servrar som ska Security Center 

Klicka på **gå till Azure Security Center för** att publicera servrar från Windows Defender ATP-serverns onboarding för att publicera servrar till Security Center.

1. På bladet **onboarding** väljer du eller skapar en arbets yta där data ska lagras. <br>
2. Om du inte kan se alla dina arbets ytor kan det bero på otillräcklig behörighet, se till att din arbets yta är inställd på Azure Security Standard-nivån. Mer information finns i [Uppgradera till Security Center standard nivå för ökad säkerhet](security-center-pricing.md).
    
3. Välj **Lägg till servrar** för att visa instruktioner om hur du installerar Microsoft Monitoring Agent. 

4. När du har registrerat dig kan du övervaka datorerna under **Compute och apps**.

   ![Publicera datorer](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-windows-defender-atp-integration"></a>Aktivera Windows Defender ATP-integrering

Om du vill se om Windows Defender ATP-integrering har Aktiver ATS väljer du priser för **Security Center** >  **& inställningar** > klickar på din prenumeration.

  ![Princip hantering för Azure Security Center](media/security-center-wdatp/policy-management.png)

Här kan du se de integreringar som är aktiverade för tillfället.

  ![Sidan Azure Security Center inställningar för hot identifiering med Windows Defender ATP-integrering aktive rad](media/security-center-wdatp/enable-integrations.png)

- Om du redan har registrerat servrarna på Azure Security Center standard nivå behöver du inte vidta några ytterligare åtgärder. Azure Security Center kommer automatiskt att publicera servrarna till Windows Defender ATP. Det kan ta upp till 24 timmar.

- Om du aldrig har publicerat servrarna på Azure Security Center standard nivå, kan du registrera dem på Azure Security Center som vanligt.

- Om du har publicerat servrarna via Windows Defender ATP:
  - I dokumentationen hittar du vägledning om [hur du avpublicera Server-datorer](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Publicera dessa servrar till Azure Security Center.

## <a name="access-to-the-windows-defender-atp-portal"></a>Åtkomst till Windows Defender ATP-portalen

Följ instruktionerna i [Tilldela användar åtkomst till portalen](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/assign-portal-access-windows-defender-advanced-threat-protection).

## <a name="set-the-firewall-configuration"></a>Ange brand Väggs konfigurationen

Om du har en proxy eller brand vägg som blockerar anonym trafik, som en Windows Defender ATP-sensor ansluter från systemets kontext, kontrollerar du att anonym trafik tillåts. Följ instruktionerna i [ge åtkomst till Windows Defender ATP-tjänst-URL: er i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/configure-proxy-internet-windows-defender-advanced-threat-protection#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testa funktionen

Så här skapar du en ofarlig test avisering för Windows Defender ATP:

1. Använd fjärr skrivbord för att få åtkomst till antingen en virtuell Windows Server 2012 R2-dator eller en virtuell Windows Server 2016-dator.  Öppna ett kommandotolksfönster.

2. Kopiera och kör följande kommando i kommando tolken. Kommando tolks fönstret stängs automatiskt.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Ett kommando tolks fönster med kommandot ovan](media/security-center-wdatp/image4.jpeg)

3. Om kommandot lyckas visas en ny avisering på instrument panelen för Azure Security Center och Windows Defender ATP-portalen. Den här aviseringen kan ta några minuter innan den visas.

4. Om du vill granska aviseringen i Security Center går du till **säkerhets varningar** >  ,**misstänkt PowerShell-kommandorad**.

5. I undersöknings fönstret väljer du länken för att gå till Windows Defender ATP-portalen.

## <a name="next-steps"></a>Nästa steg

- [Ställer in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
