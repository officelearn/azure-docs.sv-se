---
title: Vad är Azure FarmBeats
description: Ger en översikt över Azure FarmBeats
author: uhabiba04
ms.topic: overview
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 6f0a782309edc33a8a5ce661652922494ead2ec0
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80667313"
---
# <a name="overview-of-azure-farmbeats-preview"></a>Översikt över Azure FarmBeats (förhandsversion)

Azure FarmBeats är ett business-to-business-erbjudande som är tillgängligt på Azure Marketplace. Det möjliggör aggregering av jordbruksdatauppsättningar mellan leverantörer. Med Azure FarmBeats kan du skapa modeller för artificiell intelligens (AI) eller maskininlärning (ML) baserat på sammansmälta datauppsättningar. Genom att använda Azure FarmBeats kan jordbruksföretag fokusera på grundläggande mervärde i stället för odifferentierade tunga lyft av datateknik.

> [!NOTE]
> Azure FarmBeats är för närvarande i offentlig förhandsversion. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Azure FarmBeats tillhandahålls utan ett servicenivåavtal. Använd [Azure FarmBeats Forum](https://aka.ms/FarmBeatsMSDN ) för support.

![Projekt Farm Beats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

Med förhandsversionen av Azure FarmBeats kan du:

- Bedöma gårdens hälsa med vegetationsindex och vattenindex baserat på satellitbilder.
- Få rekommendationer om hur många markfuktsensorer som ska användas och var de ska placeras.
- Spåra jordbruksförhållanden genom att visualisera markdata som samlas in av sensorer från olika leverantörer.
- Få markfukt karta baserat på fusion av satellit-och sensordata.
- Få användbara insikter genom att bygga AI/ML-modeller ovanpå aggregerade datauppsättningar.
- Bygg eller utöka din digitala jordbrukslösning genom att tillhandahålla jordbrukshälsorekommendationer.

## <a name="datahub"></a>Datahub (datahub)

Azure FarmBeats Datahub är ett API-lager som möjliggör aggregering, normalisering och kontextualisering av olika jordbruksdatauppsättningar mellan leverantörer. Du kan använda Azure FarmBeats för att få:
- **Sensordata** från två sensorleverantörer [Davis Instruments](https://www.davisinstruments.com/product/enviromonitor-gateway/), [Teralytic](https://teralytic.com/), [Pessl Instruments](https://metos.at/)
- **Satellitbilder** från Europeiska rymdorganisationens satellituppdrag [Sentinel-2](https://sentinel.esa.int/web/sentinel/home)
- **Drone bilder** från tre drönare bilder leverantörer [senseFly](https://www.sensefly.com/) , [SlantRange](https://slantrange.com/) , [DJI](https://dji.com/)

Datahub är utformad som en utökningsbar API-plattform. Vi arbetar med många fler leverantörer för att integrera med Azure FarmBeats, så att du har fler valmöjligheter när du skapar din lösning.

## <a name="accelerator"></a>Accelerator

Azure FarmBeats Accelerator är ett exempel webbprogram, som bygger på datahub. Accelerator-jump-startar ditt användargränssnitt och modellutveckling. Azure FarmBeats-acceleratorn använder Azure FarmBeats API:er. Det visualiserar intas sensordata som diagram och modellutdata som kartor. Du kan till exempel använda gaspedalen för att snabbt skapa en gård och enkelt få en vegetationsindexkarta eller en sensorplaceringskarta för den gården.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

En administratör kan definiera åtkomstregler för Azure FarmBeats med hjälp av en av de fördefinierade rollerna. Roller avgör vilka områden i programmet en användare har åtkomst till och vilka åtgärder de kan utföra. Det finns två typer av roller i Azure FarmBeats – för användare och partner.

### <a name="user-roles"></a>Användarroller

En [administratör kan lägga till och hantera användare](manage-users-in-azure-farmbeats.md) och definiera deras åtkomstnivåer baserat på två användarroller: Admin och Skrivskyddad.

### <a name="partner-roles"></a>Partnerroller

En administratör kan lägga till flera partner som dataleverantörer i Azure FarmBeats. Följande sammanfattar tillgängliga partnerroller i FarmBeats och deras behörigheter:

| Typ av partner    |   Åtgärder  | Omfång |
| ---- | -------- | -------- |
| Sensor Partner  |   Skapa, läsa, uppdatera <br/> <br/> Läsa, uppdatera | DeviceModel, Enhet, SensorModel, Sensor <br/> <br/> ExtendedType (Uttr. |
| Bildspråk Partner  |   Skapa, läsa, uppdatera <br/> <br/> Läsa, uppdatera <br/> <br/> Läsa | Scen, SceneFile <br/> <br/> ExtendedType (Uttr. <br/> <br/> Gård |
| Väder partner * <br/> <br/>  (* Kommer snart) |   Skapa, läsa, uppdatera <br/> <br/> Läsa, uppdatera <br/> <br/> Läsa | WeatherDataModel, WeatherDataLocation, JobType <br/> <br/> ExtendedType (Uttr. <br/> <br/> Gård |

## <a name="resources"></a>Resurser

Azure FarmBeats erbjuds utan extra kostnad och du betalar bara för de Azure-resurser du använder. Du kan använda nedanstående resurser för att veta mer om erbjudandet:

- Håll dig informerad om de senaste Azure FarmBeats-nyheterna genom att besöka vår [Azure FarmBeats-blogg.](https://aka.ms/farmbeatsblog)
- Sök hjälp genom att publicera en fråga på vårt [supportforum för Azure FarmBeats](https://aka.ms/farmbeatssupport).
- Ge feedback genom att publicera eller rösta för en funktionsidé på vårt feedbackforum för [Azure FarmBeats](https://aka.ms/farmbeatsfeedback).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Installera Azure FarmBeats](install-azure-farmbeats.md)
