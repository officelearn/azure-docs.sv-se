---
title: Visa Azure-reservationer som en molnlösningsleverantör
description: Lär dig hur du kan visa Azure-reservationer som en molnlösningsleverantör.
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/29/2020
ms.author: banders
ms.openlocfilehash: fde33640505c225167700215f32cb3243acc5196
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424949"
---
# <a name="view-azure-reservations-as-a-cloud-solution-provider-csp"></a>Visa Azure-reservationer som en molnlösningsleverantör

Molnlösningsleverantörer kan komma åt reservationer som har köpts åt deras kunder. Använd följande information för att visa reservationer i Azure-portalen.

1. Be din globala administratör lägga till dig som en **administratörsagent** i din klientorganisation.
    Alternativet är tillgängligt för globala administratörer i Partnercenter. Det finns under **Inställningar** (kugghjulssymbolen överst till höger på sidan) > **Användarhantering**.  
1. När du har administratörsagentprivilegiet går du till Azure-portalen via länken **Admin on Behalf Of** (Administratör för).
1. Gå till Partnercenter > **Kunder** > Expandera kundinformation > **Microsoft Azure-hanteringsportal**.
1. Gå till **Reservationer** i Azure-portalen.

> [!NOTE]
> Du kan inte visa reservationer som gäst i kundens klientorganisation. Om du har gäståtkomst måste du ta bort den från klientorganisationen. Administratörsagentprivilegiet åsidosätter inte gäståtkomst.

- Du tar bort gäståtkomst i Partnercenter genom att gå till **Mitt konto** >  **[Organisationer](https://myaccount.microsoft.com/organizations)** och välja **Lämna organisationen**.

Du kan också be en annan användare som har åtkomst till reservationen att lägga till ditt gästkonto i reservationsbeställningen.

## <a name="next-steps"></a>Nästa steg

- [Visa Azure-reservationer](view-reservations.md)