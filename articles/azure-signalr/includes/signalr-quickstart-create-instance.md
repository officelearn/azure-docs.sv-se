---
title: ta med fil
description: ta med fil
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: ac77f0b6a1d90fd78db64618c1e03ade198a67c0
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882228"
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
    | **Location** | Västra USA | Välj en [region](https://azure.microsoft.com/regions/) nära dig. |
    | **prisnivå** | Lediga | Prova Azure SignalR Service kostnadsfritt. |
    | **Antal enheter** |  Inte tillämpligt | Antal enheter anger hur många anslutningar som SignalR Service-instansen kan acceptera. Det kan bara konfigureras på Standard-nivån. |
    | **Tjänst läge** |  Serverlös | För användning med Azure Functions eller REST API. |

    ![Skapa SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Välj **Skapa** för att börja distribuera SignalR Service-instansen.

1. När instansen har distribuerats öppnar du den i portalen och letar upp dess inställnings sida. Ändra inställningen för tjänst läge till endast *Server* om du använder Azure SignalR-tjänsten via Azure Functions bindning eller REST API. Lämna det i *klassiskt* eller i annat fall.