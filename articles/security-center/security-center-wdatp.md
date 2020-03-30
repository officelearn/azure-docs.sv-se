---
title: Avancerat skydd mot Microsoft Defender – Azure Security Center
description: Det här dokumentet introducerar integreringen mellan Azure Security Center och Microsoft Defender Advanced Threat Protection.
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
ms.openlocfilehash: 13852acb39a420e2f0da84e18bef4df823c1fa78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206273"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Avancerat skydd mot Microsoft Defender med Azure Security Center

Azure Security Center utökar sitt erbjudande om skydd av molnarbetsbelastningsskydd genom att integrera med [Microsoft Defender Advanced Threat Protection](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) (ATP).
Den här ändringen ger dig omfattande funktioner för identifiering och svar på slutpunkter (EDR). Med Microsoft Defender ATP-integrering kan du upptäcka avvikelser. Du kan också identifiera och svara på avancerade attacker på serverslutpunkter som övervakas av Azure Security Center.

## <a name="microsoft-defender-atp-features-in-security-center"></a>Microsoft Defender ATP-funktioner i Security Center

När du använder Microsoft Defender ATP får du:

- **Avancerade sensorer för identifiering efter intrång:** Microsoft Defender ATP-sensorer för Windows-servrar samlar in ett brett spektrum av beteendesignaler.

- **Analysbaserad, molnbaserad identifiering efter överträdelse:** Microsoft Defender ATP anpassar sig snabbt till förändrade hot. Den använder avancerad analys och stordata. Microsoft Defender ATP förstärks av kraften i intelligent säkerhetsgraf med signaler i Windows, Azure och Office för att upptäcka okända hot. Det ger användbara aviseringar och gör att du kan svara snabbt.

- **Hotinformation**: Microsoft Defender ATP genererar aviseringar när den identifierar angripare verktyg, tekniker och procedurer. Den använder data som genereras av Microsofts hotjägare och säkerhetsteam, förstärkta av underrättelser från partner.

Följande funktioner är nu tillgängliga i Azure Security Center:

- **Automatisk introduktion:** Microsoft Defender ATP-sensorn aktiveras automatiskt för Windows-servrar som är inbyggda i Azure Security Center.

- **En enda glasruta**: Azure Security Center-konsolen visar Microsoft Defender ATP-varningar.

- **Detaljerad maskinundersökning:** Azure Security Center-kunder kan använda Microsoft Defender ATP-konsol för att göra en detaljerad undersökning för att avslöja omfattningen av ett brott.

![Azure Security Center, visar en lista över aviseringar och allmän information om varje avisering](media/security-center-wdatp/image1.png)

Om du vill undersöka vidare använder du Microsoft Defender ATP. Microsoft Defender ATP innehåller ytterligare information, till exempel varningsprocessträdet och incidentdiagrammet. Du kan också se en detaljerad tidslinje för datorer som visar varje beteende under en historisk period på upp till sex månader.

![Microsoft Defender ATP-sida med detaljerad information om en avisering](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plattformssupport

Microsoft Defender ATP i Security Center stöder identifiering på Windows Server 2016, 2012 R2 och 2008 R2 SP1, för virtuella Azure-datorer behöver du en standardprenumeration och för virtuella datorer utanför Azure behöver du endast standardnivå på arbetsytan.

> [!NOTE]
> När du använder Azure Security Center för att övervaka servrar skapas en Microsoft Defender ATP-klient automatiskt och Microsoft Defender ATP-data lagras som standard i Europa. Om du behöver flytta dina data till en annan plats måste du kontakta Microsoft Support för att återställa klienten. Serverslutpunktsövervakning med den här integreringen har inaktiverats för Office 365 GCC-kunder.

## <a name="onboarding-servers-to-security-center"></a>Registrera servrar i Security Center 

Om du vill använda servrar till Security Center klickar du på **Gå till Azure Security Center till inbyggda servrar** från Microsoft Defender ATP-serverns introduktion.

1. I området **Onboarding** markerar eller skapar du en arbetsyta där data ska lagras. <br>
2. Om du inte kan se alla dina arbetsytor kan det bero på brist på behörigheter, kontrollera att arbetsytan är inställd på Azure Security Standard-nivå. Mer information finns i [Uppgradera till Security Centers standardnivå för ökad säkerhet](security-center-pricing.md).
    
3. Välj **Lägg till servrar** om du vill visa instruktioner om hur du installerar Microsoft Monitoring Agent. 

4. Efter introduktion kan du övervaka datorerna under **Beräkning och appar**.

   ![Ombord datorer](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Aktivera Microsoft Defender ATP-integrering

Om du vill visa om Microsoft Defender ATP-integrering är aktiverad väljer du**Prissättning &-inställningar för** **säkerhetscenter** > > klickar på din prenumeration.
Här kan du se de integrationer som för närvarande är aktiverade.

  ![Sidan Azure Security Center-inställning för hotidentifiering med Microsoft Defender ATP-integrering aktiverad](media/security-center-wdatp/enable-integrations.png)

- Om du redan har tagit in servrarna på Azure Security Center-standardnivå behöver du inte vidta några ytterligare åtgärder. Azure Security Center kommer automatiskt att gå in på servrarna till Microsoft Defender ATP. Ombordsättning kan ta upp till 24 timmar.

- Om du aldrig har ockitlat servrarna till Azure Security Center-standardnivå, ombord på dem till Azure Security Center som vanligt.

- Om du har tagit till dig servrarna via Microsoft Defender ATP:
  - Se dokumentationen för vägledning om [hur du offboard servermaskiner](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Ombord på dessa servrar till Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Tillgång till Microsoft Defender ATP-portalen

Följ instruktionerna i [Tilldela användaråtkomst till portalen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

## <a name="set-the-firewall-configuration"></a>Ange brandväggskonfiguration

Om du har en proxy eller brandvägg som blockerar anonym trafik, när en Microsoft Defender ATP-sensor ansluter från systemkontexten, kontrollerar du att anonym trafik är tillåten. Följ instruktionerna i [Aktivera åtkomst till Microsoft Defender ATP-tjänstadresser i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testa funktionen

Så här skapar du en godartad Microsoft Defender ATP-testavisering:

1. Skapa en mapp "C:\test-MDATP-test".

1. Använd Fjärrskrivbord för att komma åt antingen en virtuell Windows Server 2012 R2-dator eller en virtuell Windows Server 2016-dator. Öppna ett kommandoradsfönster.

1. Kopiera och kör följande kommando vid prompten. Kommandotolksfönstret stängs automatiskt.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Ett kommandotolksfönster med kommandot ovan](media/security-center-wdatp/image4.jpeg)

3. Om kommandot lyckas visas en ny avisering på Instrumentpanelen i Azure Security Center och Microsoft Defender ATP-portalen. Den här aviseringen kan ta några minuter att visas.

4. Om du vill granska aviseringen i Security Center går du till **Säkerhetsvarningar** > **Misstänkt PowerShell CommandLine**.

5. Välj länken i fönstret Microsoft Defender i undersökningsfönstret.

## <a name="next-steps"></a>Nästa steg

- [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)
- [Ange säkerhetsprinciper i Azure Security Center:](tutorial-security-policy.md)Lär dig hur du konfigurerar säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper.
- [Hantera säkerhetsrekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.
