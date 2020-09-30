---
title: Microsoft Defender Avancerat skydd – Azure Security Center
description: Det här dokumentet beskriver integrationen mellan Azure Security Center och Microsoft Defender Avancerat skydd.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/07/2020
ms.author: memildin
ms.openlocfilehash: 66a04ebec6eb9d7219d4d5db3bdd1dfd780a730f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445990"
---
# <a name="microsoft-defender-advanced-threat-protection-with-azure-security-center"></a>Microsoft Defender Avancerat skydd med Azure Security Center

Azure Security Center integreras med [Microsoft Defender Advanced Threat Protection (ATP)](https://www.microsoft.com/microsoft-365/windows/microsoft-defender-atp) för att tillhandahålla omfattande funktioner för slut punkts identifiering och-svar (EDR).


## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmänt tillgänglig (GA)|
|Priset|Kräver [Azure Defender](security-center-pricing.md)|
|Datorer som stöds:|![Ja](./media/icons/yes-icon.png) Azure-datorer som kör Windows<br>![Ja](./media/icons/yes-icon.png) Azure Arc-datorer som kör Windows|
|Nödvändiga roller och behörigheter:|Aktivera/inaktivera integrering: **säkerhets administratör** eller **ägare**<br>Så här visar du MDATP-aviseringar i Security Center: **säkerhets läsare**, **läsare**, **resurs grupps deltagare**, **resurs grupp ägare**, **säkerhets administratör**, **prenumerations ägare**eller **prenumerations deltagare**|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln.<br>![Nej](./media/icons/no-icon.png) GCC kunder som kör arbets belastningar i offentliga Azure-moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Nej](./media/icons/no-icon.png) Kina gov, andra gov|
|||


## <a name="microsoft-defender-atp-features-in-security-center"></a>Microsoft Defender ATP-funktioner i Security Center

Microsoft Defender ATP tillhandahåller:

- **Avancerade sensorer efter intrång**: Microsoft Defender ATP-sensorer för Windows-servrar samlar in en omfattande uppsättning beteende signaler.

- **Analys baserad, avkänning av intrång i molnet efter intrång**: Microsoft Defender ATP anpassas snabbt för att ändra hot. Den använder avancerade analyser och Big data. Microsoft Defender ATP förstärks av kraften hos Intelligent Security Graph med signaler över Windows, Azure och Office för att identifiera okända hot. Den innehåller aviseringar som kan åtgärdas och gör det möjligt att svara snabbt.

- **Hot information**: Microsoft Defender ATP genererar aviseringar när det identifierar angrepps verktyg, tekniker och procedurer. Den använder data som genererats av Microsoft Threat Hunters och säkerhets team, som tillhandahålls av våra partner.


Genom att integrera Defender ATP med Azure Security Center kan du också dra nytta av följande ytterligare funktioner:

- **Automatiserad onboarding**: integreringen aktiverar automatiskt Microsoft Defender ATP-sensorn för Windows-servrar som övervakas av Azure Security Center (om de inte kör Windows Server 2019).

- **En ruta i glas**: Azure Security Center-konsolen visar Microsoft Defender ATP-aviseringar. Undersök ytterligare genom att använda Microsoft Defender ATP. Microsoft Defender ATP tillhandahåller ytterligare information som varnings process trädet och incident diagrammet. Du kan också se en detaljerad dator tids linje som visar varje beteende för en historisk period på upp till sex månader.

    ![Sidan Microsoft Defender ATP med detaljerad information om en avisering](media/security-center-wdatp/image3.png)

## <a name="platform-support"></a>Plattformssupport

Microsoft Defender ATP i Security Center stöder identifiering på Windows Server 2016, 2012 R2 och 2008 R2 SP1. För virtuella Azure-datorer behöver du en för att aktivera Azure Defender på din prenumeration och för virtuella datorer som inte är Azure-datorer behöver du bara ha Azure Defender aktiverat på arbets ytans nivå.

Server slut punkts övervakning med denna integrering har inaktiverats för Office 365 GCC-kunder.

## <a name="data-storage-location"></a>Data lagrings plats

När du använder Azure Security Center för att övervaka servrar skapas en Microsoft Defender ATP-klient automatiskt. Data som samlas in av Microsoft Defender ATP lagras på den geo-platsen för klienten som identifieras under etableringen. Kund information i pseudonymized-formulär kan också lagras i de centrala lagrings-och bearbetnings systemen i USA. 

När du har konfigurerat kan du inte ändra den plats där dina data lagras. Om du behöver flytta dina data till en annan plats kontaktar du Microsoft Support för att återställa klienten.


## <a name="onboard-servers-to-security-center"></a>Onboard-servrar som ska Security Center 

Om du vill publicera servrar till Security Center klickar **du på gå till Azure Security Center till onboard-servrar** från Microsoft Defender ATP-serverns onboarding.

1. I området **onboarding** väljer eller skapar du en arbets yta där data ska lagras.

2. Om du inte kan se alla dina arbets ytor kan det bero på otillräcklig behörighet, se till att din arbets yta skyddas av Azure Defender.
    
3. Välj **Lägg till servrar** för att visa instruktioner om hur du installerar Log Analytics agenten. 

4. När du har registrerat dig kan du övervaka datorerna i [inventarie lagret](asset-inventory.md).

   ![Publicera datorer](media/security-center-wdatp/onboard-computers.png)

## <a name="enable-microsoft-defender-atp-integration"></a>Aktivera Microsoft Defender ATP-integrering

Om du vill se om Microsoft Defender ATP-integrering är aktive rad väljer du pris för **Security Center**  >  **& inställningar** > väljer din prenumeration.

Här kan du se de integreringar som är aktiverade för tillfället.

  ![Sidan Azure Security Center inställningar för hot identifiering med Microsoft Defender ATP-integrering aktive rad](media/security-center-wdatp/enable-integrations.png)

- Om du redan har aktiverat Azure Defender krävs ingen ytterligare åtgärd. Azure Security Center kommer automatiskt att publicera servrarna till Microsoft Defender ATP. Onboarding kan ta upp till 24 timmar.

- Om du aldrig har registrerat servrarna på Azure Security Center kan du publicera dem på Azure Security Center och aktivera Azure Defender som vanligt.

- Om du har publicerat servrarna via Microsoft Defender ATP:
  - I dokumentationen hittar du vägledning om [hur du avpublicera Server-datorer](https://go.microsoft.com/fwlink/p/?linkid=852906).
  - Publicera dessa servrar till Azure Security Center.

## <a name="access-to-the-microsoft-defender-atp-portal"></a>Åtkomst till Microsoft Defender ATP-portalen

1. Följ instruktionerna i [Tilldela användar åtkomst till portalen](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/assign-portal-access).

1. Kontrol lera om du har en proxy eller brand vägg som blockerar anonym trafik. Defender ATP-sensorn ansluter från systemets kontext, så anonym trafik måste tillåtas. För att säkerställa förhindrad åtkomst till Microsoft Defender ATP-portalen följer du anvisningarna i [ge åtkomst till Microsoft Defender ATP-tjänstens URL: er i proxyservern](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-proxy-internet#enable-access-to-microsoft-defender-atp-service-urls-in-the-proxy-server).

## <a name="test-the-feature"></a>Testa funktionen

Så här skapar du en ofarlig test avisering för Microsoft Defender ATP:

1. Skapa en mapp ' C:\test-MDATP-test '.

1. Använd fjärr skrivbord för att få åtkomst till antingen en virtuell Windows Server 2012 R2-dator eller en virtuell Windows Server 2016-dator. Öppna ett kommandotolksfönster.

1. Kopiera och kör följande kommando i kommando tolken. Kommando tolks fönstret stängs automatiskt.

    ```
    powershell.exe -NoExit -ExecutionPolicy Bypass -WindowStyle Hidden (New-Object System.Net.WebClient).DownloadFile('http://127.0.0.1/1.exe', 'C:\\test-MDATP-test\\invoice.exe'); Start-Process 'C:\\test-MDATP-test\\invoice.exe'
    ```

   ![Ett kommando tolks fönster med kommandot ovan](media/security-center-wdatp/image4.jpeg)

1. Om kommandot lyckas visas en ny avisering på instrument panelen för Azure Security Center och Microsoft Defender ATP-portalen. Den här aviseringen kan ta några minuter innan den visas.

1. Om du vill granska aviseringen i Security Center går du till **säkerhets varningar**  >  ,**misstänkt PowerShell-kommandorad**.

1. I undersöknings fönstret väljer du länken för att gå till Microsoft Defender ATP-portalen.

## <a name="next-steps"></a>Nästa steg

- [Plattformar och funktioner som stöds av Azure Security Center](security-center-os-coverage.md)
- [Ange säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper.
- [Hantera säkerhets rekommendationer i Azure Security Center](security-center-recommendations.md): Lär dig hur rekommendationer hjälper dig att skydda dina Azure-resurser.
- [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Lär dig hur du övervakar dina Azure-resursers hälsa.