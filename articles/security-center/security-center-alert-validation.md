---
title: Varningsvalidering (EICAR-testfil) i Azure Security Center | Microsoft-dokument
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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 5146878adf10e452f38fecb115ec40792ffa84f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79140005"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Aviseringsverifiering (EICAR-testfil) i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Aviseringar är de meddelanden som Security Center genererar när ett hot mot dina resurser har identifierats. Den prioriterar och listar aviseringarna tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center ger även rekommendationer för hur du kan åtgärda ett angrepp.
Mer information finns [i Säkerhetsvarningar i Security Center](security-center-alerts-overview.md) och Hantera och svara på [säkerhetsaviseringar](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Aviseringsverifiering

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Validera aviseringar på virtuella Windows-datorer<a name="validate-windows"></a>

När Security Center-agenten har installerats på datorn följer du dessa steg från datorn där du vill vara den an attackerade resursen för aviseringen:

1. Kopiera en körbar (till exempel **calc.exe)** till datorns skrivbord eller annan katalog för din bekvämlighet och byt namn på den som **ASC_AlertTest_662jfi039N.exe**.
1. Öppna kommandotolken och kör den här filen med ett argument (bara ett falskt argumentnamn), till exempel:```ASC_AlertTest_662jfi039N.exe -foo```
1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar [exemplet](#alert-validate) nedan ska visas:

> [!NOTE]
> När du granskar den här testvarningen för Windows kontrollerar du att fältet **Argumentgranskning aktiverad** är **sann**. Om det är **falskt**måste du aktivera granskning av kommandoradsargument. Så här aktiverar du det:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Validera aviseringar på virtuella Linux-datorer<a name="validate-linux"></a>

När Security Center-agenten har installerats på datorn följer du dessa steg från datorn där du vill vara den an attackerade resursen för aviseringen:
1. Kopiera en körbar till en lämplig plats och byt namn på den till **./asc_alerttest_662jfi039n,** till exempel:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Öppna kommandotolken och kör den här filen:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar [exemplet](#alert-validate) nedan ska visas:

### <a name="alert-example"></a>Exempel på avisering<a name="alert-validate"></a>

![Exempel på validering av avisering](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## <a name="validate-alerts-on-kubernetes"></a>Validera aviseringar på Kubernetes<a name="validate-kubernetes"></a>

Om du använder förhandsgranskningsfunktionen i Security Center för att integrera Azure Kubernetes-tjänsten kör du följande kubectl-kommando för att testa att aviseringarna fungerar:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Mer information om integreringen av Azure Kubernetes Service och Azure Security Center finns i [den här artikeln](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Validera hotidentifiering av Azure Key Vault i Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Hantera och svara på säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – Lär dig hur du hanterar aviseringar och svarar på säkerhetsincidenter i Security Center.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md) – Lär dig hur du övervakar hälsotillståndet för dina Azure-resurser.
* [Förstå säkerhetsaviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) – Läs mer om de olika typerna av säkerhetsaviseringar.
