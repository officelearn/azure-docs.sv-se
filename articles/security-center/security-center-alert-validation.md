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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 10ea15168d61d5e73aff976ef641e07b6327dbca
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604577"
---
# <a name="alert-validation-eicar-test-file-in-azure-security-center"></a>Aviserings verifiering (EICAR Test fil) i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Aviseringar är de meddelanden som Security Center genererar när de identifierar hot på dina resurser. Den prioriterar och listar aviseringarna tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center innehåller också rekommendationer för hur du kan åtgärda ett angrepp.
Mer information finns i [säkerhets aviseringar i Security Center](security-center-alerts-overview.md) och [Hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Aviseringsverifiering

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## Verifiera aviseringar på virtuella Windows-datorer<a name="validate-windows"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:

1. Kopiera en körbar fil (till exempel **calc. exe**) till datorns skriv bord, eller någon annan katalog och Byt namn på den som **ASC_AlertTest_662jfi039N. exe**.
1. Öppna kommando tolken och kör den här filen med ett argument (bara ett falsk argument namn), till exempel: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar [exemplet](#alert-validate) nedan ska visas:

> [!NOTE]
> När du granskar test aviseringen för Windows kontrollerar du att fält **argumenten granskning är aktive rad** är **Sant**. Om det är **falskt**måste du aktivera granskning av kommando rads argument. Använd följande kommando för att aktivera det:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## Verifiera aviseringar på virtuella Linux-datorer<a name="validate-linux"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:
1. Kopiera en körbar fil till en lämplig plats och Byt namn på den till **./asc_alerttest_662jfi039n**, till exempel:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Öppna kommando tolken och kör den här filen:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering som liknar [exemplet](#alert-validate) nedan ska visas:

### Aviserings exempel<a name="alert-validate"></a>

![Exempel på aviserings verifiering](./media/security-center-alert-validation/security-center-alert-validation-fig2.png) 


## Verifiera aviseringar på Kubernetes<a name="validate-kubernetes"></a>

Om du använder Security Center för hands versions funktionen i integrera Azure Kubernetes-tjänsten kör du följande kubectl-kommando för att testa att dina aviseringar fungerar:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Mer information om integreringen av Azure Kubernetes service och Azure Security Center finns i [den här artikeln](azure-kubernetes-service-integration.md).

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Hantera och svara på säkerhets aviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) – lär dig hur du hanterar aviseringar och svarar på säkerhets incidenter i Security Center.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Förstå säkerhets aviseringar i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) – lär dig mer om de olika typerna av säkerhets aviseringar.
* [Azure Security Center fel söknings guide](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) – lär dig hur du felsöker vanliga problem i Security Center.
* [Azures säkerhets blogg](https://blogs.msdn.com/b/azuresecurity/) – hitta blogg inlägg om säkerhet och efterlevnad i Azure.
