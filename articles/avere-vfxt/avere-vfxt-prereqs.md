---
title: Avere vFXT prerequisites - Azure
description: Krav för Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 352833b12c00abbefcf7016d27dfb580ee25e450
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786448"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Förbereda för att skapa Avere vFXT

Den här artikeln beskriver de nödvändiga uppgifterna för att skapa ett Avere vFXT-kluster.

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

Börja med att skapa en ny Azure-prenumeration. Använda en separat prenumeration för varje Avere vFXT projekt så att du enkelt kan spåra alla projektresurser och utgifter, skydda andra projekt från möjliga resursbegränsning under etableringen och förenkla rensning.  

Skapa en ny Azure-prenumeration i Azure portal:

* Navigera till den [prenumerationsbladet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Klicka på den **+ Lägg till** överst
* Logga in om du uppmanas till detta
* Välj ett erbjudande och gå igenom stegen för att skapa en ny prenumeration

## <a name="configure-subscription-owner-permissions"></a>Konfigurera ägarbehörighet för prenumeration

En användare med behörigheter för prenumerationen som ska skapa vFXT-kluster. Ägarbehörighet för prenumerationen krävs för att godkänna Licensvillkor för programvara för tjänsten och utföra andra åtgärder. 

Det finns vissa scenarier för lösning som gör att en icke-ägarnoder att skapa en Avere vFTX för Azure-kluster. De här scenarierna innebära att begränsa resurser och tilldela ytterligare roller skapare. I båda dessa fall kan en Prenumerationens ägare måste också [acceptera Licensvillkor för programvara i Avere vFXT](#accept-software-terms) förbereds i förväg. 

| Scenario | Begränsningar | Åtkomst-roller som krävs för att skapa Avere vFXT klustret | 
|----------|--------|-------|
| Resurs-Gruppadministratör | Det virtuella nätverket, kluster-styrenhet och klusternoderna måste skapas i resursgruppen. | [Administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagare](../role-based-access-control/built-in-roles.md#contributor) roller, båda tillhöra målresursgruppen | 
| Externa virtuella nätverk | Kluster-styrenhet och klusternoderna skapas i resursgruppen men ett befintligt virtuellt nätverk i en annan resursgrupp används | (1) [administratör för användaråtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagare](../role-based-access-control/built-in-roles.md#contributor) roller begränsade till resursgruppen vFXT; och (2) [virtuell Datordeltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [användaråtkomst Administratören](../role-based-access-control/built-in-roles.md#user-access-administrator), och [Avere deltagare](../role-based-access-control/built-in-roles.md#avere-contributor) roller begränsade till resursgruppen VNET. |
 
Ett alternativ är att skapa en anpassad rollbaserad åtkomstkontroll (RBAC) roll i tid och tilldela behörigheter till användaren, enligt beskrivningen i [i den här artikeln](avere-vfxt-non-owner.md). Den här metoden ger betydande behörigheter till dessa användare. 

## <a name="quota-for-the-vfxt-cluster"></a>Kvoten för vFXT klustret

Du måste ha tillräckligt många följande Azure-komponenterna. Om det behövs [öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Virtuella datorer och SSD-komponenter som beskrivs här är för själva klustret vFXT. Du behöver större kvot för virtuella datorer och SSD som du planerar att använda för din beräknings-servergrupp.  Se till att kvoten är aktiverad för den region där du vill köra arbetsflödet.

|Azure-komponent|Kvot|
|----------|-----------|
|Virtuella datorer|3 eller fler E32s_v3|
|Premium SSD-lagring|200 GB OS-utrymme plus 1 TB till 4 TB cacheutrymme per nod |
|Lagringskonto (valfritt) |v2|
|Datalagring i serverdel (valfritt) |En ny LRS Blob-behållare |

## <a name="accept-software-terms"></a>Acceptera licensvillkoren för programvara

> [!NOTE] 
> Det här steget krävs inte om en prenumerationsägare skapar Avere vFXT klustret.

Du måste acceptera användarvillkoren för Avere vFXT programvaran när du skapar klustret. Om du inte är en prenumerationsägare, har du en prenumerationsägare accepterar villkoren i tid. Det här steget behöver bara göras en gång per prenumeration.

Accepterar villkoren i förväg för programvaran: 

1. Öppna ett gränssnitt för molnet i Azure-portalen eller genom att bläddra till <https://shell.azure.com>. Logga in med ditt prenumerations-ID.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Kör detta kommando för att acceptera tjänstvillkoren och aktiverar Programmeringsåtkomst för Avere vFXT för Azure-programavbildning: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Skapa en slutpunkt för lagring i ditt virtuella nätverk (om det behövs)

En [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) behåller Azure Blob-trafik lokalt i stället för att skicka det utanför det virtuella nätverket. Vi rekommenderar för alla Avere vFXT för Azure-kluster som använder Azure Blob för lagring av backend-data. 

Om du anger ett befintligt vnet och skapar en ny Azure Blob-behållare för backend-lagringen som en del av skapandet av klustret, måste du ha en tjänstslutpunkt i det virtuella nätverket för Microsoft-lagring. Den här slutpunkten måste finnas innan du skapar klustret eller skapa misslyckas. 

En tjänstslutpunkt för storage rekommenderas för alla Avere vFXT för Azure-kluster som använder Azure Blob-lagring, även om du senare lägga till lagring. 

> [!TIP] 
> * Hoppa över det här steget om du skapar ett nytt virtuellt nätverk som en del av klustret skapas. 
> * Det här steget är valfritt om du inte skapar Blob-lagring när klustret skapas. I så fall kan du skapa tjänstslutpunkten senare om du vill använda Azure Blob.

Skapa service-slutpunkt för lagring från Azure-portalen. 

1. I portalen klickar du på **virtuella nätverk** till vänster.
1. Välj det virtuella nätverket för klustret. 
1. Klicka på **tjänstslutpunkter** till vänster.
1. Klicka på **Lägg till** högst upp.
1. Lämna tjänsten som ``Microsoft.Storage`` och välj klustrets undernät.
1. Längst ned på sidan, klickar du på **Lägg till**.

   ![Azure portal skärmbild med anteckningar för stegen för att skapa tjänsteslutpunkt](media/avere-vfxt-service-endpoint.png)


## <a name="next-step-create-the-vfxt-cluster"></a>Nästa steg: Skapa vFXT-kluster

När du har slutfört dessa krav, kan du gå vidare till skapa själva klustret. Läs [distribuera vFXT klustret](avere-vfxt-deploy.md) anvisningar.