---
title: 'Scenario: Azure Firewall – Interhub-routning'
titleSuffix: Azure Virtual WAN
description: Scenarier för Routning och routning mellan flera hubbar som har Azure-brandvägg
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: cherylmc
ms.openlocfilehash: 1057dc28682c90374b2f8e8e0297f3d769c08bd6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "85568982"
---
# <a name="scenario-azure-firewall---interhub-preview"></a>Scenario: Azure Firewall-Interhub (för hands version)

När du arbetar med virtuell WAN-routning för virtuella WAN finns det några tillgängliga scenarier. I det här scenariot är målet att dirigera mellan flera hubbar som innehåller Azure-brandvägg. Information om virtuell hubb routning finns i [om virtuell nav-routning](about-virtual-hub-routing.md).

## <a name="scenario-architecture"></a><a name="architecture"></a>Scenariots arkitektur

I det här scenariot antar vi följande konfiguration:

* Du har flera hubbar med Azure-brandväggen i varje hubb.
* Du använder säker virtuell hubb.
* Lämpliga principer för privat trafik (VNet), Internet och gren trafik har kon figurer ATS.
* VNet-till-Internet (V2I), VNet-till-gren (V2B), gren-till-VNet (B2V), allt går igenom Azure-brandväggen i varje hubb.

Fler saker att ha i åtanke:

* För ett scenario med en enda hubb med Azure Firewall är det antagandet att eker-virtuella nätverk inte associeras med separata routningstabeller. (t. ex. **VNet 1** kopplad till routningstabellen **A**och **VNet 2** kopplad till **väg tabell B**). Alla virtuella nätverk associeras med samma routningstabell som vägarna för Azure-brandväggen finns i.
* Att skydda via Azure Firewall är för närvarande begränsat till **gren <-> VNet** och **Internet** trafik. Gren-till-gren-flöde via Azure-brandväggen stöds inte för närvarande.

**Bild 1**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/architecture.png" alt-text="designen":::

## <a name="scenario-workflow"></a><a name="workflow"></a>Scenario arbets flöde

För att konfigurera det här scenariot vidtar du följande steg för att överväga:

### <a name="step-1"></a><a name="step-1"></a>Steg 1

Förutsatt att du redan har skyddat anslutningarna via Azure Firewall Manager, är det första steget att se till att alla förgreningar och VNet-anslutningar sprider sig **ingen**. Detta krävs för att säkerställa att trafiken ställs in via Azure-brandväggen.

1. Välj hubben och redigera tabellen **ingen** väg.
1. Uppdatera **spridningen** för att välja alla grenar och alla virtuella nätverk.

### <a name="step-2"></a><a name="step-2"></a>Steg 2

Konfigurera en anpassad routningstabell per hubb.

1. Skapa routningstabellen **RT_Hub1**i **hubb 1**.

    * Om du använde Azure Firewall Manager skapar den automatiskt en väg för 0.0.0.0/0 med nästa hopp **AZFW1** i hubbens standard väg tabell. Vi kommer att ändra den här inställningen i steg 3. För **RT_Hub1**skapar du en post för 0.0.0.0/0, explicit med nästa hopp **AZFW1**. Med den här inställningen aktive ras V2V, B2V och V2I via AZFW1.
    * Eftersom du vill att gren-och virtuella nätverk för **hubb 2** ska nås via **AZFW2** från **hubb 1** (i stället för via AZFW1), måste du lägga till ytterligare 2 aggregerade vägar för grenar **2** -grenar (10.5.0.0/16->AZFW2) och virtuella nätverk (10.2.0.0/16->AZFW2).

1. Skapa routningstabellen **RT_Hub2**i **hubb 2**.

    * Om du använde Azure Firewall Manager skapar den automatiskt en väg för 0.0.0.0/0 med nästa hopp **AZFW2** i hubbens standard väg tabell. Vi kommer att ändra den här inställningen i steg 3. För **RT_Hub2**skapar du en post för 0.0.0.0/0, explicit med nästa hopp **AZFW2**. Med den här inställningen aktive ras V2V, B2V och V2I via **AZFW2**.
    * Eftersom du vill att trafiken mellan hub ska skyddas av hubb 2 **AZFW2**behöver du inte uttryckligen lägga till ett steg som liknar den andra punkten i föregående hubb 1-steg.

### <a name="step-3"></a><a name="step-3"></a>Steg 3

Ändra **standard väg tabellen** i varje nav för att lägga till en statisk väg för **grenen till VNet** (B2V) och **gren till Internet** (B2I) flöden via Azure-brandväggen. Gren till gren stöds inte för närvarande via Azure-brandväggen.

1. För **hubb 1 standard väg tabell**:

    * **Gren till hubb 2 grenar via AZFW2**: 10.5.0.0/16->AZFW2. Den här konfigurationen konfigurerar en väg för NAV 2-brandvägg.
    * **Gren till hubb 2 virtuella nätverk via AZFW2**: 10.2.0.0/16->AZFW2. Den här konfigurationen konfigurerar en väg för NAV 2-brandvägg.
    * **Gren till gren (lokal)/gren till VNet (lokal)/förgrening till Internet**: 0.0.0.0/0->AZFW1.

2. För **nav 2 standard väg tabell**:

    * Gren till gren (lokal och fjärr)/gren till VNet (lokal och fjärran sluten)/gren till Internet: 0.0.0.0/0->AZFW2.

Detta leder till att konfigurations konfigurationen ändras så som visas i figuren nedan

**Bild 2**

:::image type="content" source="./media/routing-scenarios/between-hubs-firewall/workflow.png" alt-text="arbets flöde":::

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar](virtual-wan-faq.md).
* Mer information om routning av virtuell hubb finns i [om virtuell hubb](about-virtual-hub-routing.md).
* Mer information om hur du konfigurerar routning för virtuell hubb finns i [så här konfigurerar du routning av virtuell hubb](how-to-virtual-hub-routing.md).
