---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76020978"
---
1. Logga in på [Azure-portalen][Azure portal].
1. Välj **Skapa en resurs**. Välj sedan > **Integrationsrelä**. **Integration** Om du inte ser **Relay** i listan väljer du **Visa alla** i det övre högra hörnet.
1. Välj **Skapa**och ange ett namnområdesnamn i fältet **Namn.** Azure-portalen kontrollerar om namnet är tillgängligt.
1. Välj en Azure-prenumeration där namnområdet ska skapas.
1. För [resursgrupp](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)väljer du en befintlig resursgrupp där namnområdet ska placeras eller skapa ett nytt.  
1. Välj det land eller den region där namnområdet ska vara värd.

    ![Skapa namnområde][create-namespace]

1. Välj **Skapa**. Azure-portalen skapar ditt namnområde och aktiverar det. Efter ett par minuter etableras resurser för ditt konto i systemet.

### <a name="get-management-credentials"></a>Få autentiseringsuppgifter för hantering

1. Välj **Alla resurser**och välj sedan det nyskapade namnområdesnamnet.
1. Välj **principer för delad åtkomst**.  
1. Under **Principer för delad åtkomst** väljer du **RootManageSharedAccessKey**.
1. Under **SAS-princip: RootManageSharedAccessKey**väljer du knappen **Kopiera** bredvid **Primär anslutningssträng**. Den här åtgärden kopierar anslutningssträngen till Urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
1. Upprepa föregående steg för att kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
