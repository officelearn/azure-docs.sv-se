---
title: Förhindra fel konfiguration med Azure Security Center
description: Lär dig hur du använder Security Center alternativen "påtvinga" och "Neka" på rekommendations informations sidorna
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 19e9a33350b6a1a67986dc35a372f737e45ab39a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90906393"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Förhindra felaktig konfiguration med tvinga/neka-rekommendationer

Fel konfiguration av säkerhet är en stor orsak till säkerhets incidenter. Security Center kan nu hjälpa till *att förhindra* fel konfiguration av nya resurser med avseende på särskilda rekommendationer. 

Den här funktionen kan hjälpa till att hålla arbets belastningarna säkra och stabilisera dina säkra poäng.

Att framtvinga en säker konfiguration, som baseras på en viss rekommendation, erbjuds i två lägen:

- Genom att använda Azure Policy **neka** kan du förhindra att resurser som inte är felfria skapas
- Med alternativet **tillämpa** kan du dra nytta av Azure policys **DeployIfNotExist** -påverkan och automatiskt reparera icke-kompatibla resurser när de skapas

Du hittar det här längst upp på sidan resursinformation för de valda säkerhets rekommendationerna (se [rekommendationer med alternativen neka/tillämpa](#recommendations-with-denyenforce-options)).

## <a name="prevent-resource-creation"></a>Förhindra att resurser skapas

1. Öppna rekommendationen som dina nya resurser måste uppfylla och välj knappen **neka** överst på sidan.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Sidan rekommendation med knappen neka markerad":::

    Konfigurations fönstret öppnas och visar omfångs alternativen. 

1. Ange omfånget genom att välja den relevanta prenumerationen eller hanterings gruppen.

    > [!TIP]
    > Du kan använda de tre punkterna i slutet av raden för att ändra en enskild prenumeration eller använda kryss rutorna för att välja flera prenumerationer eller grupper. Välj sedan **ändra till neka**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Sidan rekommendation med knappen neka markerad":::


## <a name="enforce-a-secure-configuration"></a>Framtvinga en säker konfiguration

1. Öppna rekommendationen att distribuera en mall distribution för om nya resurser inte uppfyller den, och välj knappen **Använd** överst på sidan.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Sidan rekommendation med knappen neka markerad":::

    Konfigurations fönstret öppnas med alla princip konfigurations alternativ. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Sidan rekommendation med knappen neka markerad":::

1. Ange omfång, tilldelnings namn och andra relevanta alternativ.

1. Välj **Granska + skapa**.

## <a name="recommendations-with-denyenforce-options"></a>Rekommendationer med alternativen neka/Använd

Dessa rekommendationer kan användas med alternativet **neka** :

- Virtuella datorer ska migreras till nya Azure Resource Manager-resurser
- Lagrings konton ska migreras till nya Azure Resource Manager resurser
- Alla auktoriseringsregler utom RootManageSharedAccessKey ska tas bort från Event Hub-namnområdet
- Alla auktoriseringsregler utom RootManageSharedAccessKey bör tas bort från Service Bus namnrymd
- Säker överföring till lagringskonton ska vara aktiverat
- Endast säkra anslutningar till din Redis Cache ska vara aktiverade
- Variabler för Automation-konton ska vara krypterade
- Service Fabric kluster bör endast använda Azure Active Directory för klientautentisering
- Service Fabric-kluster ska ha egenskapen ClusterProtectionLevel inställd på EncryptAndSign
- Granska obegränsad nätverks åtkomst till lagrings konton


Dessa rekommendationer kan användas med alternativet **tillämpa** :

- Diagnostikloggar i Logic Apps ska vara aktive rad
- Diagnostikloggar i Data Lake Analytics ska vara aktive rad
- Diagnostikloggar i IoT Hub ska vara aktive rad
- Diagnostikloggar i batch-konton måste vara aktiverade
- Diagnostikloggar i Azure Stream Analytics ska vara aktive rad
- Diagnostikloggar i Service Bus ska vara aktive rad
- Diagnostikloggar i search Services ska vara aktive rad
- Diagnostikloggar i Händelsehubben måste vara aktive rad
- Diagnostikloggar i Virtual Machine Scale Sets ska vara aktive rad
- Diagnostikloggar i Key Vault ska vara aktive rad
- Granskning på SQL Server måste vara aktiverat
- Avancerad data säkerhet ska vara aktiverat på dina SQL-servrar



