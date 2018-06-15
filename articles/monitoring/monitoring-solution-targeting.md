---
title: Målobjekt för hanteringslösningar i Azure | Microsoft Docs
description: Målobjekt för hanteringslösningar kan du begränsa hanteringslösningar till en specifik uppsättning agenter.  Den här artikeln beskriver hur du skapar en konfiguration för scope och tillämpa den på en lösning.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1f054a4e-6243-4a66-a62a-0031adb750d8
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2017
ms.author: bwren
ms.openlocfilehash: 65585e6c09def23101d9735c8b9c719d213938ac
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887848"
---
# <a name="targeting-management-solutions-in-azure-preview"></a>Målobjekt för hanteringslösningar i Azure (förhandsversion)
När du lägger till en lösning för din prenumeration distribueras den automatiskt som standard till alla Windows- och Linux-agenter ansluten till logganalys-arbetsytan.  Du kanske vill hantera kostnaderna och begränsa mängden data som samlas in för en lösning genom att begränsa den till en viss uppsättning agenter.  Den här artikeln beskriver hur du använder **lösning riktad** som är en funktion som gör att du kan använda ett omfång med dina lösningar.

## <a name="how-to-target-a-solution"></a>Så här anger du en lösning som mål
Det finns tre steg ska inriktning på en lösning som beskrivs i följande avsnitt. 


### <a name="1-create-a-computer-group"></a>1. Skapa en datorgrupp
Ange de datorer som du vill ska ingå i en omfattning genom att skapa en [datorgrupp](../log-analytics/log-analytics-computer-groups.md) i logganalys.  Gruppen kan baserat på en logg sökning eller importeras från andra källor, till exempel Active Directory eller WSUS-grupper. Som [beskrivs nedan](#solutions-and-agents-that-cant-be-targeted), bara datorer som är anslutna direkt till Log Analytics ska inkluderas i omfånget.

När du har skapat datorgrupp som skapats i din arbetsyta, och du måste inkludera den i en scope-konfiguration som kan tillämpas på en eller flera lösningar.
 
 
 ### <a name="2-create-a-scope-configuration"></a>2. Skapa en scope-konfiguration
 En **konfigurationen** innehåller en eller flera datorgrupper och kan tillämpas på en eller flera lösningar. 
 
 Skapa en scope-konfiguration på följande sätt.  

 1. I Azure-portalen går du till **logganalys** och välj din arbetsyta.
 2. I egenskaperna för arbetsytan under **arbetsytan datakällor** Välj **omfång konfigurationer**.
 3. Klicka på **Lägg till** att skapa en ny konfiguration för scope.
 4. Ange en **namn** för scope-konfiguration.
 5. Klicka på **Markera datorgrupper**.
 6. Välj datorgruppen som du skapade och eventuellt andra grupper för att lägga till i konfigurationen.  Klicka på **Välj**.  
 6. Klicka på **OK** att skapa scope-konfiguration. 


 ### <a name="3-apply-the-scope-configuration-to-a-solution"></a>3. Gäller konfigurationen av en lösning.
När du har en scope-konfiguration kan använda du den till en eller flera lösningar.  Observera att när en enda scope-konfiguration kan användas med flera lösningar, varje lösning kan bara använda en konfiguration för scope.

Använd ett scope-konfiguration på följande sätt.  

 1. I Azure-portalen går du till **logganalys** och välj din arbetsyta.
 2. I egenskaperna för arbetsytan väljer **lösningar**.
 3. Klicka på den lösning som du vill omfång.
 4. I egenskaperna för lösningen under **arbetsytan datakällor** Välj **lösning riktad**.  Om alternativet inte är tillgängligt sedan [den här lösningen kan inte riktas](#solutions-and-agents-that-cant-be-targeted).
 5. Klicka på **Lägg till konfigurationen**.  Om du redan har en konfiguration som tillämpas på den här lösningen sedan otillgängligt det här alternativet.  Du måste ta bort den befintliga konfigurationen innan du lägger till en annan.
 6. Klicka på konfigurationen av som du skapade.
 7. Titta på den **Status** av konfigurationen så att den visar **lyckades**.  Om statusen indikerar ett fel, klicka på tre punkter till höger om konfiguration och välj **redigera konfigurationen** att göra ändringar.

## <a name="solutions-and-agents-that-cant-be-targeted"></a>Lösningar och agenter som inte vara mål
Följande är kriterier för agenter och lösningar som inte kan användas med lösningen som mål.

- Mål för lösning gäller bara för lösningar som distribueras till agenter.
- Mål för lösning gäller bara för lösningar som tillhandahålls av Microsoft.  Gäller inte för lösningar [skapas av dig själv eller partners](monitoring-solutions-creating.md).
- Du kan bara filtrera ut agenter som ansluter direkt till Log Analytics.  Lösningar distribuerar automatiskt till alla eventuella agenter som en del av en ansluten hanteringsgrupp för Operations Manager oavsett om de finns med i en konfiguration för scope.

### <a name="exceptions"></a>Undantag
Mål för lösning kan inte användas med följande lösningar trots att de passar de angivna villkoren.

- Bedömning av hälsotillstånd för Agent

## <a name="next-steps"></a>Nästa steg
- Mer information om lösningar inklusive lösningar som är tillgängliga för installation i din miljö på [lägga till Azure logganalys hanteringslösningar till din arbetsyta](../log-analytics/log-analytics-add-solutions.md).
- Lär dig mer om hur du skapar datorgrupper på [datorgrupper i logganalys logga sökningar](../log-analytics/log-analytics-computer-groups.md).