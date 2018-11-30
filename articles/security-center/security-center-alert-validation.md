---
title: Aviseringsverifiering i Azure Security Center | Microsoft Docs
description: I det här dokumentet får du hjälp med att verifiera säkerhetsaviseringar i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: f8f17a55-e672-4d86-8ba9-6c3ce2e71a57
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 2c0bb2a68eaaa8183463efbdc2848567ab67d1b9
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52619752"
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

    ![Aviseringsverifiering](./media/security-center-alert-validation/security-center-alert-validation-fig2.png)

När du granskar den här aviseringen ska du se till att fältet Argument Auditing Enabled (argumentgranskning aktiverat) visas som true (sant). Om det visas som false (falskt) måste du aktivera granskning av kommandoradsargument. Du kan aktivera det här alternativet med följande kommandorad:

*reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"*


> [!NOTE]
> Titta på videoklippet [Alert Validation in Azure Security Center](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Alert-Validation-in-Azure-Security-Center) (aviseringsverifiering i Azure Security Center) för en demonstration av den här funktionen.

## <a name="see-also"></a>Se också
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Hantera och åtgärda säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Lär dig hur du hanterar aviseringar och åtgärdar säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Läs mer om de olika typerna av säkerhetsaviseringar.
* [Felsökningsguide för Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Lär dig hur du felsöker vanliga problem i Security Center.
* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md). Här finns vanliga frågor om att använda tjänsten.
* [Azures säkerhetsblogg](https://blogs.msdn.com/b/azuresecurity/). Här hittar du blogginlägg om säkerhet och regelefterlevnad i Azure.
