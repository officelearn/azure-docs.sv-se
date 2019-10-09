---
title: Microsoft Defender Avancerat skydd med Azure Security Center
description: Det här dokumentet beskriver integrationen mellan Azure Security Center och Microsoft Defender Avancerat skydd.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: memildin
ms.openlocfilehash: fb401226e9697afb71dfa7a30f9814e07c3422c2
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028077"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Avancerat skydd med Azure Security Center

Azure Security Center utökar sitt moln skydds plattforms skydds plattform genom att integrera med [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Den här ändringen ger dig omfattande funktioner för identifiering och svar på slutpunkter (EDR). Med Microsoft Defender ATP-integrering kan du upptäcka avvikelser. Du kan också identifiera och svara på avancerade attacker på Server slut punkter som övervakas av Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Microsoft Defender ATP-funktioner i Security Center

När du använder Microsoft Defender ATP får du:

- **Avancerade sensorer efter intrång i spårning**: Microsoft Defender ATP-sensorer för Windows-servrar samlar in en omfattande uppsättning beteende signaler.

- **Analys baserad, identifiering av molnbaserade data efter intrång**: Microsoft Defender ATP anpassar snabbt för att ändra hot. Den använder avancerade analyser och Big data. Microsoft Defender ATP förstärks av kraften hos Intelligent Security Graph med signaler över Windows, Azure och Office för att identifiera okända hot. Den innehåller aviseringar som kan åtgärdas och gör det möjligt att svara snabbt.

- **Hot information**: Microsoft Defender ATP genererar aviseringar när det identifierar angrepps verktyg, tekniker och procedurer. Den använder data som genererats av Microsoft Threat Hunters och säkerhets team, som tillhandahålls av våra partner.

Följande funktioner är nu tillgängliga i Azure Security Center:

- **Automatiserad onboarding**: Microsoft Defender ATP-sensorn aktive ras automatiskt för Windows-servrar som har publicerats på Azure Security Center.

- **En ruta med glas**: I Azure Security Center-konsolen visas Microsoft Defender ATP-aviseringar.

- **Detaljerad dator undersökning**: Azure Security Center kunder kan använda Microsoft Defender ATP-konsolen för att utföra en detaljerad undersökning för att avhjälpa omfattningen av en överträdelse.

![Azure Security Center, Visa en lista över aviseringar och allmän information om varje avisering](media/security-center-wdatp/image1.png)

Undersök ytterligare genom att använda Microsoft Defender ATP. Microsoft Defender ATP tillhandahåller ytterligare information som varnings process trädet och incident diagrammet. Du kan också se en detaljerad dator tids linje som visar varje beteende för en historisk period på upp till sex månader.

![Sidan Microsoft Defender ATP med detaljerad information om en avisering](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plattformssupport

Microsoft Defender ATP i Security Center stöder identifiering på Windows Server 2016, 2012 R2 och 2008 R2 SP1 operativ system i en standard tjänst prenumeration.

> [!NOTE]
> När du använder Azure Security Center för att övervaka servrar skapas en Microsoft Defender ATP-klient automatiskt och Microsoft Defender ATP-data lagras i Europa som standard. Om du behöver flytta dina data till en annan plats måste du kontakta Microsoft Support för att återställa klienten. Övervakning av Server slut punkt som använder denna integrering har inaktiverats för Office 365 GCC-kunder.

## <a name="onboarding-servers-to-security-center"></a>Onboarding-servrar som ska Security Center 

Om du vill publicera servrar till Security Center klickar **du på gå till Azure Security Center till onboard-servrar** från Microsoft Defender ATP-serverns onboarding.

1. I området **onboarding** väljer eller skapar du en arbets yta där data ska lagras. <br>
2. Om du inte kan se alla dina arbets ytor kan det bero på otillräcklig behörighet, se till att din arbets yta är inställd på Azure Security Standard-nivån. Mer information finns i [Uppgradera till Security Center standard nivå för ökad säkerhet](security-center-pricing.md).
    
3. Välj **Lägg till servrar** för att visa instruktioner om hur du installerar Microsoft Monitoring Agent. 

4. När du har registrerat dig kan du övervaka datorerna under **Compute och apps**.

   ![Publicera datorer](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Aktivera Microsoft Defender ATP-integrering

Om du vill se om Microsoft Defender ATP-integrering har Aktiver ATS väljer du **& inställningar** för **Security Center** >  > klickar på din prenumeration.
Här kan du se de integreringar som är aktiverade för tillfället.

  ![Sidan Azure Security Center inställningar för hot identifiering med Microsoft Defender ATP-integrering aktive rad](media/security-center-wdatp/enable-integrations.png)

- Om du redan har registrerat servrarna på Azure Security Center standard nivå behöver du inte vidta några ytterligare åtgärder. Azure Security Center kommer automatiskt att publicera servrarna till Microsoft Defender ATP. Onboarding kan ta upp till 24 timmar.

- Om du aldrig har publicerat servrarna på Azure Security Center standard nivå, kan du registrera dem på Azure Security Center som vanligt.

- Om du har publicerat servrarna via Microsoft Defender ATP:
  - I dokumentationen hittar du vägledning om [hur du avpublicera Server-datorer](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Publicera dessa servrar till Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Åtkomst till Microsoft Defender ATP-portalen

Följ instruktionerna i [Tilldela användar åtkomst till portalen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Ange brand Väggs konfigurationen

Om du har en proxy eller brand vägg som blockerar anonym trafik, som en Microsoft Defender ATP-sensor ansluter från systemets kontext, kontrollerar du att anonym trafik tillåts. Följ instruktionerna i [ge åtkomst till Microsoft Defender ATP-tjänstens URL-adresser i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testa funktionen

Så här skapar du en ofarlig test avisering för Microsoft Defender ATP:

1. Använd fjärr skrivbord för att få åtkomst till antingen en virtuell Windows Server 2012 R2-dator eller en virtuell Windows Server 2016-dator.  Öppna ett kommandotolksfönster.

2. Kopiera och kör följande kommando i kommando tolken. Kommando tolks fönstret stängs automatiskt.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-WDATP-test\\invoice.exe'); Start-Process 'C:\\test-WDATP-test\\invoice.exe'
    ```

   ![Ett kommando tolks fönster med kommandot ovan](media/security-center-wdatp/image4.jpeg)

3. Om kommandot lyckas visas en ny avisering på instrument panelen för Azure Security Center och Microsoft Defender ATP-portalen. Den här aviseringen kan ta några minuter innan den visas.

4. Om du vill granska aviseringen i Security Center går du till **säkerhets aviseringar** >  **misstänkt PowerShell-kommandorad**.

5. I undersöknings fönstret väljer du länken för att gå till Microsoft Defender ATP-portalen.

## <a name="next-steps"></a>Nästa steg

- [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)
- [Ställer in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md): Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
