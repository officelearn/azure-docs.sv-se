---
title: Aviserings verifiering (EICAR Test fil) i Azure Security Center | Microsoft Docs
description: I det här dokumentet får du hjälp med att verifiera säkerhetsaviseringar i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 32f67fb94b207735e77583a6db62f7c8703dd991
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202734"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Aviserings verifiering (EICAR Test fil) i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Aviseringar är de meddelanden som Security Center genererar när de identifierar hot på dina resurser. Den prioriterar och listar aviseringarna tillsammans med den information som behövs för att du snabbt ska kunna undersöka problemet. Security Center innehåller också rekommendationer för hur du kan åtgärda ett angrepp.
Mer information finns i [säkerhets aviseringar i Azure Security Center](security-center-alerts-overview.md) och [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Aviseringsverifiering

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Verifiera avisering på virtuell Windows-dator<a name="validate-windows"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:

1. Kopiera en körbar fil (till exempel **calc. exe**) till datorns skriv bord, eller någon annan katalog och Byt namn på den som **ASC_AlertTest_662jfi039N. exe**.
1. Öppna kommando tolken och kör den här filen med ett argument (bara ett falsk argument namn), till exempel:```ASC_AlertTest_662jfi039N.exe -foo```
1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar [exemplet](#alert-validate) nedan ska visas:

> [!NOTE]
> När du granskar test aviseringen för Windows kontrollerar du att fält **argumenten granskning är aktive rad** är **Sant**. Om det är **falskt**måste du aktivera granskning av kommando rads argument. Använd följande kommando rad för att aktivera den:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Verifiera avisering på virtuell Linux-dator<a name="validate-linux"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:
1. Kopiera en körbar fil till en lämplig plats och Byt namn på den till **./asc_alerttest_662jfi039n**, till exempel:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Öppna kommando tolken och kör den här filen:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar [exemplet](#alert-validate) nedan ska visas:

### Aviserings exempel<a name="alert-validate"></a>

![Exempel på aviserings verifiering](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Se också
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
