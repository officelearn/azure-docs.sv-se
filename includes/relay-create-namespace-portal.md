---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: bbcf38ab54632144920b729f433c222f426d96dc
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825871"
---
1. Logga in på [Azure Portal][Azure portal].
2. Välj **+ Skapa en resurs** på menyn till vänster. Välj sedan **Integrering** > **Relay**. Om du inte ser **Relay** i listan väljer du **Visa alla** i det övre högra hörnet. 
3. Under **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
4. I rutan **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
5. I rutan [Resursgrupp](../articles/azure-resource-manager/manage-resource-groups-portal.md) väljer du en befintlig resursgrupp där namnområdet ska placeras eller skapar en ny.  
6. I **Plats** väljer du land eller region där namnområdet ska finnas.
   
    ![Skapa namnområde][create-namespace]
7. Välj **Skapa**. Systemet skapar namnområdet och aktiverar det. Efter ett par minuter etableras resurser för ditt konto i systemet.

### <a name="get-management-credentials"></a>Få autentiseringsuppgifter för hantering

1. Välj **Alla resurser** och välj sedan den nyligen skapade namnrymden.
2. Under Relay-namnrymden väljer du **Principer för delad åtkomst**.  
3. Under **Principer för delad åtkomst** väljer du **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. Under **Princip: RootManageSharedAccessKey** väljer du knappen **Kopiera** intill **Anslutningssträng – Primärnyckel**. Anslutningssträngen kopieras då till Urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
   
    ![connection-string][connection-string]

5. Upprepa föregående steg för att kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
