---
title: Aviseringsverifiering i Azure Security Center | Microsoft Docs
description: "I det här dokumentet får du hjälp med att verifiera säkerhetsaviseringar i Azure Security Center."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: a9cfd6052b58fe7a800f1b58113aec47a74095e3
ms.openlocfilehash: 121b5d8f023a9b663d0e7af26dce8f81db27672c
ms.contentlocale: sv-se
ms.lasthandoff: 08/11/2017

---
# <a name="alerts-validation-in-azure-security-center"></a>Aviseringsverifiering i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Security Center samlar automatiskt in, analyserar och integrerar loggdata från Azure-resurser, nätverket och anslutna partnerlösningar som brandväggs- och slutpunktsskyddslösningar för att identifiera och uppmärksamma dig på hot. I [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) kan du läsa mer om säkerhetsaviseringar, och i [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) får du veta mer om olika typer av aviseringar.

## <a name="alert-validation"></a>Aviseringsverifiering
När Security Center-agenten är installerad på datorn följer du stegen nedan från den dator där du vill ha den angripna resursen som aviseras:

1. Kopiera en körbar fil (till exempel calc.exe) till datorns skrivbord eller en annan katalog som är lätt att hitta.
2. Byt namn på filen till **ASC_AlertTest_662jfi039N.exe**.
3. Öppna kommandotolken och kör den här filen med ett argument (bara ett falskt argumentnamnet), exempelvis: *ASC_AlertTest_662jfi039N.exe -foo*
4. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. Där bör du hitta en avisering liknande denna:

    ![Aviseringsverifiering](./media/security-center-alert-validation/security-center-alert-validation-fig1.png)

När du granskar den här aviseringen ska du se till att fältet Argument Auditing Enabled (argumentgranskning aktiverat) visas som true (sant). Om det visas som false (falskt) måste du aktivera granskning av kommandoradsargument. Du kan aktivera det här alternativet med följande kommandorad:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


## <a name="see-also"></a>Se även
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center. 
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](http://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.


