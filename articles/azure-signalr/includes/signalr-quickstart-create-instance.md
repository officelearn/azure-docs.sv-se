---
title: ta med fil
description: ta med fil
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 15eded28e38279ea01bf019566d4fda5e7ac6c3e
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57588180"
---
## <a name="create-an-azure-signalr-service-instance"></a>Skapa en Azure SignalR Service-instans

Din app ansluter till en SignalR Service-instans i Azure.

1. Välj knappen Nytt högst upp till vänster i Azure-portalen. På sidan Nytt skriver du *SignalR Service* i sökrutan och trycker på RETUR.

    ![Söka efter SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Välj **SignalR Service** i sökresultatet och välj sedan **Skapa**.

1. Ange följande inställningar.

    | Inställning      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Resursnamn** | Globalt unikt namn | Namn som identifierar din nya SignalR Service-instans. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den här nya SignalR Service-instansen har skapats i. | 
    | **[Resursgrupp](../../azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Namnet på den nya resursgruppen som SignalR Service-instansen ska skapas i. | 
    | **Plats** | Västra USA | Välj en [region](https://azure.microsoft.com/regions/) nära dig. |
    | **prisnivå** | Kostnadsfri | Prova Azure SignalR Service kostnadsfritt. |
    | **Antal enheter** |  Inte tillämpligt | Antal enheter anger hur många anslutningar som SignalR Service-instansen kan acceptera. Det kan bara konfigureras på Standard-nivån. |

    ![Skapa SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Välj **Skapa** för att börja distribuera SignalR Service-instansen.

1. När instansen har distribuerats kan du öppna den i portalen och leta upp dess inställningssidan. Ändra inställningen Service till *Serverless*.

    ![SignalR-tjänstläge](../media/signalr-concept-azure-functions/signalr-service-mode.png)