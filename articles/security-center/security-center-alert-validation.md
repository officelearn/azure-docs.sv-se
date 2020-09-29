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
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 0c777c2461047f21e8fb413faec6d5f1140fc75f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/29/2020
ms.locfileid: "91438803"
---
# <a name="alert-validation-in-azure-security-center"></a>Aviserings validering i Azure Security Center
I det här dokumentet får du hjälp med att verifiera systemet är rätt konfigurerat för aviseringar från Azure Security Center.

## <a name="what-are-security-alerts"></a>Vad är säkerhetsaviseringar?
Aviseringar är de meddelanden som Security Center genererar när ett hot mot dina resurser har identifierats. Den prioriterar och listar aviseringarna tillsammans med den information som behövs för att snabbt undersöka problemet. Security Center ger även rekommendationer för hur du kan åtgärda ett angrepp.
Mer information finns i [säkerhets aviseringar i Security Center](security-center-alerts-overview.md) och [Hantera och svara på säkerhets aviseringar](security-center-managing-and-responding-alerts.md)

## <a name="alert-validation"></a>Aviseringsverifiering

* [Windows](#validate-windows)
* [Linux](#validate-linux)
* [Kubernetes](#validate-kubernetes)

## <a name="validate-alerts-on-windows-vms"></a>Verifiera aviseringar på virtuella Windows-datorer <a name="validate-windows"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:

1. Kopiera en körbar fil (till exempel **calc.exe**) till datorns skriv bord eller någon annan katalog och Byt namn på den som **ASC_AlertTest_662jfi039N.exe**.
1. Öppna kommando tolken och kör den här filen med ett argument (bara ett falsk argument namn), till exempel: ```ASC_AlertTest_662jfi039N.exe -foo```
1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering ska visas.

> [!NOTE]
> När du granskar test aviseringen för Windows kontrollerar du att fält **argumenten granskning är aktive rad** är **Sant**. Om det är **falskt**måste du aktivera granskning av kommando rads argument. Använd följande kommando för att aktivera det:
>
>```reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\policies\system\Audit" /f /v "ProcessCreationIncludeCmdLine_Enabled"```

## <a name="validate-alerts-on-linux-vms"></a>Verifiera aviseringar på virtuella Linux-datorer <a name="validate-linux"></a>

När Security Center agent har installerats på datorn följer du de här stegen från den dator där du vill bli den angripna resursen för aviseringen:
1. Kopiera en körbar fil till en lämplig plats och Byt namn på den till **./asc_alerttest_662jfi039n**, till exempel:

    ```cp /bin/echo ./asc_alerttest_662jfi039n```

1. Öppna kommando tolken och kör den här filen:

    ```./asc_alerttest_662jfi039n testing eicar pipe```

1. Vänta 5–10 minuter och öppna sedan Security Center-aviseringar. En avisering ska visas.


## <a name="validate-alerts-on-kubernetes"></a>Verifiera aviseringar på Kubernetes <a name="validate-kubernetes"></a>

Om du har integrerat Azure Kubernetes-tjänsten med Security Center kan du testa att aviseringarna fungerar med följande kubectl-kommando:

```kubectl get pods --namespace=asc-alerttest-662jfi039n```

Mer information om att försvara dina Kubernetes-noder och-kluster finns i [Introduktion till Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)

## <a name="next-steps"></a>Nästa steg
I den här artikeln förklaras processen för aviseringsverifiering. Nu när du är bekant med den här verifieringen kan du titta på följande artiklar:

* [Verifiera Azure Key Vault hot identifiering i Azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336)
* [Hantera och svara på säkerhets aviseringar i Azure Security Center](security-center-managing-and-responding-alerts.md) – lär dig hur du hanterar aviseringar och svarar på säkerhets incidenter i Security Center.
* [Övervakning av säkerhets hälsa i Azure Security Center](security-center-monitoring.md) – lär dig hur du övervakar Azure-resursernas hälsa.
* [Förstå säkerhets aviseringar i Azure Security Center](security-center-alerts-type.md) – lär dig mer om de olika typerna av säkerhets aviseringar.