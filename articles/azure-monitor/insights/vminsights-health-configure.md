---
title: Konfigurera övervakning i Azure Monitor for VMs gäst hälsa (för hands version)
description: Beskriver hur du ändrar standard övervakning för Azure Monitor for VMs gäst hälsa (för hands version) med hjälp av Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687155"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Konfigurera övervakning i Azure Monitor for VMs gäst hälsa (för hands version)
Azure Monitor for VMs gäst hälsa kan du se hälso tillståndet för en virtuell dator så som den definieras av en uppsättning prestanda mätningar som samplas med jämna mellanrum. I den här artikeln beskrivs hur du kan ändra standard övervakning med hjälp av Azure Portal. Det beskriver också grundläggande koncept för Övervakare som krävs för att [Konfigurera övervakning med en data insamlings regel](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Öppna övervaknings konfiguration
Öppna konfigurations lager plats för övervakning Azure Portal genom att välja övervakaren och sedan fliken **konfiguration** .

[![Konfiguration av övervaka Detaljer](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Aktivera eller inaktivera övervakare
Både enhets övervakare och sammanställda övervakare har status inställningar för **hälso tillstånd** som gör att du kan aktivera eller inaktivera övervakaren. När en övervakare är aktive rad visas dess hälsa och används för att ställa in hälso tillståndet för den virtuella datorn. När en övervakare är inaktive rad beräknas inte dess hälsa och används inte för att ange hälso tillståndet för den virtuella datorn.

| Inställningen | Beskrivning |
|:---|:---|
| Enabled | Övervakaren är aktive rad oavsett inställningen för dess överordnade. |
| Inaktiverad | Övervakaren är inaktive rad oavsett inställningen för dess överordnade. |
| Samma som överordnad | Övervakaren kommer att aktive ras eller inaktive ras beroende på inställningen för dess överordnade. |

När en övervakare är inaktive ring visas alla villkor som de inte är tillgängliga som de visas i följande exempel.

![Övervakaren är inaktive rad](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>Aktivera eller inaktivera virtuell dator
Du kan inaktivera övervakning för en virtuell dator för att tillfälligt stoppa alla Övervakare. Du kan till exempel inaktivera övervakning för en virtuell dator, till exempel när du utför underhåll på den.

| Inställningen | Beskrivning |
|:---|:---|
| Enabled  | Datorns hälso tillstånd visas. |
| Inaktiverad | Datorns hälso tillstånd visas som **inaktiverat**. Aviseringar skapas inte. |

## <a name="health-state-logic"></a>Hälso tillstånds logik
Hälso tillstånds logiken för en enhets övervakare definierar kriterierna för att ställa in hälso tillståndet. Du kan ange hur många hälso tillstånd som en övervakare har och tröskelvärdet för hur varje hälso tillstånd beräknas.

![Exempel på hälso kriterier](media/vminsights-health-configure/sample-health-criteria.png)

Sammanställda övervakare anger ingen hälso tillstånds logik. Deras hälso tillstånd anges av enhets övervakarna under dem enligt deras hälso tillstånds sammanslagning.

Enhets övervakare har två eller tre hälso tillstånd. Var och en kommer alltid att ha ett felfritt tillstånd och eventuellt ett varnings tillstånd, ett kritiskt tillstånd eller både och. Varnings-och kritisk tillstånd varje kräver unika villkor som definierar när övervakaren anges till det här tillståndet. Det felfria tillståndet har inga kriterier eftersom det här tillståndet anges när villkoren för de andra tillstånden inte är uppfyllda.

I följande exempel anges processor användningen till följande hälso tillstånd:

- Kritisk om den är större än 90%.
- Varning om den är större än eller lika med 80%.
- Felfri om den är under 80%. Detta är underförstådd eftersom det är villkoret där inget av de andra villkoren gäller.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera övervakare i skala med hjälp av data insamlings regler.](vminsights-health-configure-dcr.md)