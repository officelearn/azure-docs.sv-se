---
title: Felsöka Övervakare för för hands version av Windows Virtual Desktop – Azure
description: Fel sökning av problem med Azure Monitor för virtuella Windows-datorer.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 12/01/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 91cf6729911cdb674c5451f172e76a2e9d5943e4
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467676"
---
# <a name="troubleshoot-azure-monitor-for-windows-virtual-desktop-preview"></a>Felsöka Azure Monitor för Windows Virtual Desktop (för hands version)

>[!IMPORTANT]
>Azure Monitor för det virtuella Windows-skrivbordet är för närvarande en offentlig för hands version. Den här för hands versionen tillhandahålls utan service nivå avtal och vi rekommenderar inte att du använder den för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här artikeln visar kända problem och lösningar för vanliga problem i Azure Monitor för Windows Virtual Desktop (för hands version).

## <a name="the-configuration-workbook-isnt-working-properly"></a>Konfigurations arbets boken fungerar inte korrekt

Om arbets boken Azure Monitor konfiguration inte fungerar kan du använda dessa resurser för att konfigurera dess delar manuellt:

- Om du vill aktivera diagnostik manuellt eller komma åt Log Analytics-arbetsytan läser du [Skicka diagnostik för virtuella Windows-datorer till Log Analytics](diagnostics-log-analytics.md).
- Om du vill installera Log Analytics-tillägget på en värd manuellt, se [Log Analytics tillägg för virtuell dator för Windows](../virtual-machines/extensions/oms-windows.md).
- Om du vill konfigurera en ny Log Analytics arbets yta, se [skapa en Log Analytics arbets yta i Azure Portal](../azure-monitor/learn/quick-create-workspace.md).
- Information om hur du lägger till eller tar bort prestanda räknare finns i [Konfigurera prestanda räknare](../azure-monitor/platform/data-sources-performance-counters.md).
- Information om hur du konfigurerar händelser för en Log Analytics arbets yta finns i [samla in Windows händelse logg data källor med Log Analytics-agenten](../azure-monitor/platform/data-sources-windows-events.md).

Alternativt kan problemet bero på brist på resurser eller inte har de behörigheter som krävs.

Om prenumerationen inte har några Windows-virtuella Skriv bords resurser visas den inte i *prenumerations* parametern.

Om du inte har Läs behörighet till rätt prenumerationer visas de inte i *prenumerations* parametern och du kan inte se deras data i instrument panelen. Lös problemet genom att kontakta Prenumerationens ägare och be om Läs behörighet.

## <a name="my-data-isnt-displaying-properly"></a>Mina data visas inte korrekt

Om dina data inte visas korrekt kan något inträffa under Azure Monitor konfigurations processen. Se först till att du har fyllt i alla fält i konfigurations arbets boken enligt beskrivningen i [använda Azure Monitor för Windows Virtual Desktop för att övervaka distributionen](azure-monitor.md). Du kan när som helst ändra inställningarna för både nya och befintliga miljöer. Om du saknar räknare eller händelser visas inte de data som är kopplade till dem i Azure Portal.

Om du inte saknar någon information men dina data fortfarande inte visas korrekt, kan det finnas ett problem i frågan eller data källorna. 

Om du inte ser några installations fel och fortfarande inte ser de data du förväntar dig, kanske du vill vänta i 15 minuter och uppdatera flödet. Azure Monitor har en svars tid på 15 minuter för att fylla logg data. Läs mer i logg data Inhämtnings [tid i Azure Monitor](../azure-monitor/platform/data-ingestion-time.md).

Slutligen, om du inte har saknat någon information men dina data fortfarande inte visas, kan det finnas ett problem i frågan eller data källorna. Om så är fallet kan du behöva kontakta supporten för att lösa problemet.

## <a name="i-want-to-customize-azure-monitor-for-windows-virtual-desktop"></a>Jag vill anpassa Azure Monitor för virtuellt Windows-skrivbord

Azure Monitor för virtuella Windows-datorer använder Azure Monitor-arbetsböcker. Med arbets böcker kan du spara en kopia av mallen för Windows Virtual Desktop-arbetsböcker och göra egna anpassningar.

Anpassade mallar uppdateras inte när produkt gruppen uppdaterar den ursprungliga mallen. Detta är avsiktligt i arbets boks verktyget. du måste spara en kopia av den uppdaterade mallen och återskapa anpassningarna för att kunna tillämpa uppdateringar. Mer information finns i [Felsöka arbets boksbaserade insikter](../azure-monitor/insights/troubleshoot-workbooks.md) och [Översikt över arbets böcker](../azure-monitor/platform/workbooks-overview.md).

## <a name="i-cant-interpret-the-data"></a>Jag kan inte tolka data

Lär dig mer om data termer på [Azure Monitor för Virtual Desktop-ordlista](azure-monitor-glossary.md).

## <a name="the-data-i-need-isnt-available"></a>De data jag behöver är inte tillgängliga

Kan du inte hitta någon data punkt för att diagnostisera ett problem? Skicka oss feedback!

- Information om hur du lämnar feedback finns i [fel söknings översikt, feedback och support för Windows Virtual Desktop](troubleshoot-set-up-overview.md).
- Du kan också lämna feedback för virtuella Windows-datorer i [hubben Windows Virtual Desktop feedback](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app) eller i [vårt UserVoice-forum](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

## <a name="known-issues"></a>Kända problem

Detta är de problem som vi för närvarande är medvetna om och som arbetar med att åtgärda:

- Du kan för närvarande bara välja en prenumeration, en resurs grupp och en modempool som ska övervakas i taget. På grund av detta måste du, när du använder sidan användar rapporter för att förstå en användares upplevelse, kontrol lera att du har rätt värd-pool som användaren har använt eller att data inte kommer att fyllas i de visuella objekten.

- Det går för närvarande inte att spara favorit inställningar i Azure Monitor om du inte sparar en anpassad mall i arbets boken. Det innebär att IT-administratörer måste ange sina prenumerations namn, resurs grupp namn och inställningar för värdstat varje gång de öppnar Azure Monitor för virtuellt Windows-skrivbord.

- Det finns för närvarande inget sätt att exportera data från Azure Monitor för virtuella Windows-datorer till Excel.

- All allvarlighets grad 1 Azure Monitor aviseringar för alla produkter i den valda prenumerationen visas på översikts sidan. Detta är avsiktligt, som aviseringar från andra produkter i prenumerationen kan påverka det virtuella Windows-skrivbordet. Just nu är frågan begränsad till allvarlighets grad 1 varningar, exklusive allvarlighets grad 0-aviseringar med hög prioritet från översikts sidan.

- Vissa fel meddelanden formuleras inte på ett användarvänligt sätt, och alla fel meddelanden beskrivs inte i dokumentationen.

## <a name="next-steps"></a>Nästa steg

Om du är osäker på hur du tolkar data eller vill lära dig mer om vanliga villkor kan du läsa [Azure Monitor för ord listan för virtuella Windows-datorer](azure-monitor-glossary.md). Om du vill lära dig hur du konfigurerar och använder Azure Monitor för virtuella Windows-datorer, se [använd Azure Monitor för Windows Virtual Desktop för att övervaka distributionen](azure-monitor.md).