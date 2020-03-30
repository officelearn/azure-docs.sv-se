---
title: Anslut Zimperium Mobile Threat Defense till Azure Sentinel| Microsoft-dokument
description: Läs om hur du ansluter Zimperium Mobile Threat Defense till Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 86854fa22a49f09e5d3d2fc5fdb53c245850fbac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587948"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>Anslut ditt Zimperium Mobile Threat Defense till Azure Sentinel


> [!IMPORTANT]
> Zimperium Mobile Threat Defense-dataanslutningen i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Zimperium Mobile Threat Defense-anslutningsappen ger dig möjlighet att ansluta Zimperium-hotloggen med Azure Sentinel för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra undersökningen. Detta ger dig mer insikt i organisationens mobila hotlandskap och förbättrar dina funktioner för säkerhetsdrift.

> [!NOTE]
> Data lagras på den geografiska platsen för arbetsytan där du kör Azure Sentinel.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>Konfigurera och anslut Zimperium Mobile Threat Defense

Zimperium Mobile Threat Defense kan integrera och exportera loggar direkt till **Azure Sentinel**.

1. I Azure Sentinel-portalen klickar du på Datakopplingar och väljer **Zimperium Mobile Threat Defense**.
2. Välj **Öppna kopplingssida**.
3. Följ instruktionerna på **Zimperium Mobile Threat Defense** kontaktsida, sammanfattas enligt följande.
 1. I zConsole klickar du på **Hantera** i navigeringsfältet.
 2. Klicka på fliken **Integreringar**.
 3. Klicka på knappen **Hotrapportering** och sedan knappen **Lägg till integreringar.**
 4. Skapa integreringen genom att välja **Microsoft Azure Sentinel** från tillgängliga integreringar och ange arbetsyte-ID och primärnyckel för att ansluta till Azure Sentinel.
 5. Alternativet att välja en filternivå för hotdata som ska skickas till Azure Sentinel är också tillgängligt. 

4. För ytterligare information, se [Zimperium kundsupport portal](https://support.zimperium.com).


## <a name="find-your-data"></a>Hitta dina data

När en lyckad anslutning har upprättats visas data i Log Analytics under CustomLogs ZimperiumThreatLog_CL och ZimperiumMitigationLog_CL.

Om du vill använda det relevanta schemat i Log Analytics för Zimperium Mobile Threat Defense söker du efter ZimperiumThreatLog_CL och ZimperiumMitigationLog_CL.


## <a name="validate-connectivity"></a>Verifiera anslutning

Det kan ta uppemot 20 minuter innan loggarna börjar visas i Log Analytics.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter Zimperium Mobile Threat Defense till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).

- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Använd arbetsböcker](tutorial-monitor-your-data.md) för att övervaka dina data.

Mer information om Zimperium finns i följande:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobil säkerhet Blogg](https://blog.zimperium.com)

- [Zimperium Kundsupport Portal](https://support.zimperium.com)

