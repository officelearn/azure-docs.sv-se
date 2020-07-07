---
title: inkludera fil
description: inkludera fil
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 0a228f6d8b4f72acd3783f27bf192fe6bd13f988
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "75392154"
---
## <a name="create-an-azure-signalr-service-instance"></a>Skapa en Azure SignalR Service-instans

Din app ansluter till en SignalR Service-instans i Azure.

1. Välj knappen Nytt högst upp till vänster i Azure-portalen. På sidan Nytt skriver du *SignalR Service* i sökrutan och trycker på RETUR.

    ![Söka efter SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-new.png)

1. Välj **SignalR Service** i sökresultatet och välj sedan **Skapa**.

1. Ange följande inställningar.

    | Inställningen      | Föreslaget värde  | Beskrivning                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Resurs namn** | Globalt unikt namn | Namn som identifierar din nya SignalR Service-instans. Giltiga tecken är `a-z`, `0-9` och `-`.  | 
    | **Prenumeration** | Din prenumeration | Prenumerationen som den här nya SignalR Service-instansen har skapats i. | 
    | **[Resursgrupp](../../azure-resource-manager/management/overview.md)** |  myResourceGroup | Namnet på den nya resursgruppen som SignalR Service-instansen ska skapas i. | 
    | **Position** | USA, västra | Välj en [region](https://azure.microsoft.com/regions/) nära dig. |
    | **Pris nivå** | Kostnadsfri | Prova Azure SignalR Service kostnadsfritt. |
    | **Antal enheter** |  Ej tillämpligt | Antal enheter anger hur många anslutningar som SignalR Service-instansen kan acceptera. Det kan bara konfigureras på Standard-nivån. |
    | **Tjänst läge** |  Utan server | För användning med Azure Functions eller REST API. |

    ![Skapa SignalR Service](../media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-create.png)

1. Välj **Skapa** för att börja distribuera SignalR Service-instansen.

1. När instansen har distribuerats öppnar du den i portalen och letar upp dess inställnings sida. Ändra inställningen för tjänst läge till endast *Server* om du använder Azure SignalR-tjänsten via Azure Functions bindning eller REST API. Lämna det i *klassiskt* eller *i* annat fall.