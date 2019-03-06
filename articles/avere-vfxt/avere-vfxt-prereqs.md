---
title: Avere vFXT prerequisites - Azure
description: Krav för Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0fedfdbb04dd7aa3ff077c13653a22ed710aff9f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405325"
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

En användare med behörigheter för prenumerationen som ska skapa vFXT-kluster. Ägarbehörighet för prenumerationen krävs för dessa åtgärder, bland annat:

* Acceptera villkoren för Avere vFXT programvara
* Skapa klusterrollen noden åtkomst 

Det finns två lösningar om du inte vill ge ägaråtkomst till de användare som skapar vFXT:

* En resursgruppägare kan skapa ett kluster om dessa villkor är uppfyllda:

  * En prenumerant måste [acceptera Licensvillkor för programvara i Avere vFXT](#accept-software-terms) och [skapa klusterrollen noden åtkomst](#create-the-cluster-node-access-role). 
  * Alla Avere vFXT resurser måste distribueras i resursgruppen, inklusive:
    * Kluster-domänkontrollant
    * Klusternoder
    * Blob Storage
    * Nätverkselement
 
* Användare med ingen ägare kan skapa vFXT kluster med hjälp av rollbaserad åtkomstkontroll (RBAC i tid) för att tilldela behörigheter till användaren. Den här metoden ger betydande behörigheter till dessa användare. [Den här artikeln](avere-vfxt-non-owner.md) förklarar hur du skapar en åtkomstroll för att auktorisera icke-ägare att skapa kluster.

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

## <a name="create-access-roles"></a>Skapa roller för åtkomst 

[Rollbaserad åtkomstkontroll](../role-based-access-control/index.yml) (RBAC) ger vFXT styrenhet och klustret klusternoderna behörighet att utföra åtgärderna.

* Kontrollanten klustret behöver behörighet för att skapa och ändra virtuella datorer för att skapa klustret. 

* Enskilda vFXT noder måste till exempel läsa Azure resursegenskaper, hantera lagring och styra inställningar för nätverksgränssnittet andra noder som en del av normal klusteråtgärden.

Innan du kan skapa Avere vFXT klustret, måste du definiera en anpassad roll som ska användas med noderna i klustret. 

Du kan acceptera standardroll från mallen för kluster-styrenheten. Standard ger klustret ägarprivilegier för controller resource group. Om du vill skapa en anpassad roll för styrenheten Se [anpassad åtkomst kontrollantrollen](avere-vfxt-controller-role.md).

> [!NOTE] 
> Endast en prenumerationsägare eller en användare med rollen ägare eller administratör för användaråtkomst, skapa roller. Rollerna som kan skapas i förväg.  

### <a name="create-the-cluster-node-access-role"></a>Skapa klusterrollen noden åtkomst

<!-- caution - this header is linked to in the template so don't change it unless you can change that -->

Du måste skapa noden klusterrollen innan du kan skapa Avere vFXT för Azure-kluster.

> [!TIP] 
> Interna Microsoft-användare bör använda befintlig roll med namnet ”Avere kluster Runtime-operatör” i stället för att försök för att skapa en. 

1. Kopiera den här filen. Lägg till ditt prenumerations-ID i AssignableScopes-rad.

   (Den aktuella versionen av den här filen lagras i databasen som github.com/Azure/Avere [AvereOperator.txt](https://github.com/Azure/Avere/blob/master/src/vfxt/src/roles/AvereOperator.txt).)  

   ```json
   {
      "AssignableScopes": [
          "/subscriptions/PUT_YOUR_SUBSCRIPTION_ID_HERE"
      ],
      "Name": "Avere Operator",
      "IsCustom": "true",
      "Description": "Used by the Avere vFXT cluster to manage the cluster",
      "NotActions": [],
      "Actions": [
          "Microsoft.Compute/virtualMachines/read",
          "Microsoft.Network/networkInterfaces/read",
          "Microsoft.Network/networkInterfaces/write",
          "Microsoft.Network/virtualNetworks/subnets/read",
          "Microsoft.Network/virtualNetworks/subnets/join/action",
          "Microsoft.Network/networkSecurityGroups/join/action",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "DataActions": [
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
          "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ]
   }
   ```

1. Spara filen som ``avere-operator.json`` eller liknande egen filnamn. 


1. Öppna ett Azure Cloud shell och logga in med ditt prenumerations-ID (beskrivs [tidigare i det här dokumentet](#accept-software-terms)). Använd följande kommando för att skapa rollen:

   ```bash
   az role definition create --role-definition /avere-operator.json
   ```

Rollnamnet används när klustret skapas. I det här exemplet heter ``avere-operator``.

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