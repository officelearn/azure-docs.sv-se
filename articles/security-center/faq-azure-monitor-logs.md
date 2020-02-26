---
title: Azure Security Center FAQ – frågor om befintliga MMA
description: I det här avsnittet får du svar på frågor för kunder som redan använder Microsoft Monitoring Agent och som överväger Azure Security Center, en produkt som hjälper dig att förhindra, identifiera och svara på hot.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 528ff47be2b18cb7d9b938e988383a9e81be67fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599514"
---
# Vanliga frågor och svar om kunder som redan använder Azure Monitor loggar<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Security Center åsidosätter alla befintliga anslutningar mellan virtuella datorer och arbetsytor?

Om en virtuell dator redan har Microsoft Monitoring Agent installerad som en utökning av Azure, åsidosätts inte den befintliga arbetsyta-anslutningen i Security Center. Security Center använder i stället den befintliga arbetsytan. Den virtuella datorn kommer att skyddas förutsatt att lösningen "säkerhet" eller "SecurityCenterFree" har installerats på den arbets yta som den rapporterar till. 

En Security Center lösning installeras på den arbets yta som valts på skärmen data insamling om den inte redan finns, och lösningen tillämpas bara på de relevanta virtuella datorerna. När du lägger till en lösning distribueras den automatiskt som standard att alla Windows- och Linux-agenter är anslutna till Log Analytics-arbetsytan. Med [lösnings mål](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du tillämpa ett omfång på dina lösningar.

Om Microsoft Monitoring Agent är installerad direkt på den virtuella datorn (inte som en utökning av Azure), Security Center installera inte Microsoft Monitoring Agent och virtuella datorer är begränsad.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Security Center kan installeras lösningar i min befintliga Log Analytics-arbetsytor? Vad är fakturering effekterna?
När Security Center identifierar att en virtuell dator är redan ansluten till en arbetsyta som du skapade, kan Security Center-lösningar i den här arbetsytan enligt din prisnivå. Lösningarna tillämpas bara på relevanta virtuella Azure-datorer, via [lösnings mål](../operations-management-suite/operations-management-suite-solution-targeting.md), så att faktureringen förblir densamma.

- **Kostnads fri nivå** – Security Center installerar lösningen "SecurityCenterFree" på arbets ytan. Du debiteras inte för den kostnads fria nivån.
- **Standard-nivån** – Security Center installerar Security-lösningen på arbets ytan.

   ![Lösningar på standardarbetsytan][1]

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Jag har redan arbetsytor i Min miljö, kan jag använda dem för att samla in säkerhetsdata?
Om en virtuell dator har redan Microsoft Monitoring Agent installerad som en utökning av Azure kan använder Security Center den befintliga anslutna arbetsytan. En Security Center lösning installeras på arbets ytan om den inte redan finns, och lösningen tillämpas bara på relevanta virtuella datorer via [lösnings mål](../operations-management-suite/operations-management-suite-solution-targeting.md).

När Security Center installeras Microsoft Monitoring Agent på virtuella datorer använder standard-arbetsytor som skapats av Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Jag har redan säkerhetslösning på Mina arbetsytor. Vad är fakturering effekterna?
Säkerhet och granskning lösningen används för att aktivera Security Center Standard-nivån funktioner för virtuella Azure-datorer. Om lösningen för säkerhet och granskning är redan installerad på en arbetsyta kan använder Security Center den befintliga lösningen. Det finns ingen ändring i fakturering.


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/solutions.png