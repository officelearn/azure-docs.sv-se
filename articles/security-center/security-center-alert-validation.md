---
title: Avisera verifiering (EICAR test-fil) i Azure Security Center | Microsoft Docs
description: I det här dokumentet får du hjälp med att verifiera säkerhetsaviseringar i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: rkarlin
ms.openlocfilehash: f65b4b74a1a91fa081bd9c0d8146d055cebb0de6
ms.sourcegitcommit: c0419208061b2b5579f6e16f78d9d45513bb7bbc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67626305"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Aviseringsverifiering (EICAR test-fil) i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Aviseringar är de meddelanden som Security Center genererar när hot på dina resurser. Den prioriterar och visar en lista över aviseringar tillsammans med den information som behövs att snabbt undersöka problemet. Security Center innehåller också rekommendationer för hur du kan avhjälpa angrepp.
Mer information finns i [säkerhetsaviseringar i Azure Security Center](security-center-alerts-overview.md) och [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Aviseringsverifiering

* [Windows](#validate-windows)
* [Linux](#validate-linux)

## Validering av på Windows virtuell dator <a name="validate-windows"></a>

När Security Center-agenten är installerad på datorn, följer du de här stegen från datorn där du vill ha den angripna resursen för aviseringen:

1. Kopiera en körbar fil (till exempel **calc.exe**) till datorns skrivbord eller en annan katalog för din bekvämlighet och Byt namn på det som **ASC_AlertTest_662jfi039N.exe**.
1. Öppna Kommandotolken och kör den här filen med ett argument (bara ett falskt argumentnamnet), till exempel: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar den [exempel](#alert-validate) nedan ska visas:

> [!NOTE]
> När du granskar den här aviseringen test för Windows, se till att fältet **argument-granskning aktiverat** är **SANT**. Om det är **FALSKT**, måste du aktivera granskning av kommandoradsargument. Aktivera det genom att använda följande kommandorad:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Validering av på Linux VM <a name="validate-linux"></a>

När Security Center-agenten är installerad på datorn, följer du de här stegen från datorn där du vill ha den angripna resursen för aviseringen:
1. Kopiera en körbar fil på en lämplig plats och Byt namn på den till **. / asc_alerttest_662jfi039n**, till exempel:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Öppna Kommandotolken och kör den här filen:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar den [exempel](#alert-validate) nedan ska visas:

### Aviseringsexemplet <a name="alert-validate"></a>

![Aviseringsverifiering exempel](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 

## <a name="see-also"></a>Se också
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
