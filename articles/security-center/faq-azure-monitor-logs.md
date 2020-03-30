---
title: Vanliga frågor och svar om Azure Security Center – frågor om befintliga MMA-program
description: Den här vanliga frågor och svar på frågor för kunder som redan använder Microsoft Monitoring Agent och överväger Azure Security Center, en produkt som hjälper dig att förebygga, identifiera och svara på hot.
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
ms.openlocfilehash: 5c433140c3982813e372fd3f63243a96197d220c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661897"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>Vanliga frågor och svar för kunder som redan använder Azure Monitor-loggar<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>Åsidosätter Security Center alla befintliga anslutningar mellan virtuella datorer och arbetsytor?

Om en virtuell dator redan har Microsoft Monitoring Agent installerad som ett Azure-tillägg åsidosätter Security Center inte den befintliga arbetsytans anslutning. I stället använder Security Center den befintliga arbetsytan. Den virtuella datorn kommer att skyddas under förutsättning att lösningen "Säkerhet" eller "SecurityCenterFree" har installerats på arbetsytan som den rapporterar. 

En Security Center-lösning installeras på den arbetsyta som valts på datainsamlingsskärmen om den inte redan finns, och lösningen tillämpas endast på relevanta virtuella datorer. När du lägger till en lösning distribueras den automatiskt som standard till alla Windows- och Linux-agenter som är anslutna till logganalysarbetsytan. [Med lösningsinriktning](../operations-management-suite/operations-management-suite-solution-targeting.md) kan du tillämpa ett scope på dina lösningar.

> [!TIP]
> Om Microsoft Monitoring Agent installeras direkt på den virtuella datorn (inte som ett Azure-tillägg), installerar Security Center inte Microsoft Monitoring Agent och säkerhetsövervakningen är begränsad.

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>Installerar Security Center lösningar på mina befintliga Log Analytics-arbetsytor? Vilka är faktureringskonsekvenserna?
När Security Center identifierar att en virtuell dator redan är ansluten till en arbetsyta som du har skapat, aktiverar Security Center lösningar på den här arbetsytan enligt din prisnivå. Lösningarna tillämpas endast på relevanta virtuella Azure-datorer, via [lösningsinriktning](../operations-management-suite/operations-management-suite-solution-targeting.md), så faktureringen förblir densamma.

- **Kostnadsfri nivå** – Security Center installerar SecurityCenterFree-lösningen på arbetsytan. Du debiteras inte för den kostnadsfria nivån.
- **Standardnivå** – Security Center installerar "Säkerhet"-lösningen på arbetsytan.

   ![Lösningar på standardarbetsytan](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>Jag har redan arbetsytor i min miljö, kan jag använda dem för att samla in säkerhetsdata?
Om en virtuell dator redan har Microsoft Monitoring Agent installerad som ett Azure-tillägg använder Security Center den befintliga anslutna arbetsytan. En Security Center-lösning installeras på arbetsytan om den inte redan finns, och lösningen tillämpas endast på relevanta virtuella datorer via [lösningsinriktning](../operations-management-suite/operations-management-suite-solution-targeting.md).

När Security Center installerar Microsoft Monitoring Agent på virtuella datorer används standardarbetsyterna som skapats av Security Center.

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>Jag har redan en säkerhetslösning på mina arbetsytor. Vilka är faktureringskonsekvenserna?
Security & Audit-lösningen används för att aktivera Security Center-standardnivåfunktioner för virtuella Azure-datorer. Om security & Audit-lösningen redan är installerad på en arbetsyta använder Security Center den befintliga lösningen. Faktureringen ändras inte.