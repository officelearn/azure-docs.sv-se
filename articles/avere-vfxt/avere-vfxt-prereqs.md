---
title: Aver vFXT-krav – Azure
description: Krav för aver vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: rohogue
ms.openlocfilehash: 0dafef7cf262153ccdb3b490aa0c7bd039b4a89b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75889176"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Förbereda för att skapa Avere vFXT

I den här artikeln beskrivs de nödvändiga åtgärderna för att skapa ett AVERT vFXT-kluster.

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

Börja med att skapa en ny Azure-prenumeration. Använd en separat prenumeration för varje AVERT vFXT-projekt så att du enkelt kan spåra alla projekt resurser och utgifter, skydda andra projekt från möjliga resurs begränsningar under etableringen och förenkla rensningen.

För att skapa en ny Azure-prenumeration i Azure Portal:

* Gå till [prenumerations bladet](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
* Klicka på knappen **+ Lägg till** överst
* Logga in om du uppmanas att göra det
* Välj ett erbjudande och gå igenom stegen för att skapa en ny prenumeration

## <a name="configure-subscription-owner-permissions"></a>Konfigurera ägar behörigheter för prenumeration

En användare med ägar behörigheter för prenumerationen bör skapa vFXT-klustret. Prenumerations ägarens behörigheter krävs för att godkänna program varu villkoren och utföra andra åtgärder.

Det finns vissa lösnings scenarier som låter en icke-ägare skapa ett AVERT-vFTX för Azure-kluster. I dessa scenarier ingår att begränsa resurser och tilldela ytterligare roller till Skaparen. I båda fallen måste en prenumerations ägare också [godkänna vFXT-programtermerna](#accept-software-terms) i förväg.

| Scenario | Begränsningar | Åtkomst roller som krävs för att skapa ett AVERT vFXT-kluster |
|----------|--------|-------|
| Resurs grupp administratör | Virtuella nätverk, kluster styrenheter och klusternoder måste skapas i resurs gruppen | [Användarens åtkomst administratör](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagar](../role-based-access-control/built-in-roles.md#contributor) roller, både riktade till mål resurs gruppen |
| Externt virtuellt nätverk | Kluster styrenheten och klusternoderna skapas i resurs gruppen, men ett befintligt virtuellt nätverk i en annan resurs grupp används | (1) [administratör för användar åtkomst](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagar](../role-based-access-control/built-in-roles.md#contributor) roller som är begränsade till resurs gruppen vFXT och (2) [virtuell dator deltagare](../role-based-access-control/built-in-roles.md#virtual-machine-contributor), [användar åtkomst administratör](../role-based-access-control/built-in-roles.md#user-access-administrator)och [AVERT deltagar](../role-based-access-control/built-in-roles.md#avere-contributor) roller som är begränsade till det virtuella nätverkets resurs grupp. |

Ett alternativ är att skapa en anpassad rollbaserad åtkomst kontroll (RBAC) i förväg och tilldela behörigheter till användaren, enligt beskrivningen i [den här artikeln](avere-vfxt-non-owner.md). Den här metoden ger viktiga behörigheter till dessa användare.

## <a name="quota-for-the-vfxt-cluster"></a>Kvot för vFXT-klustret

Du måste ha tillräcklig kvot för följande Azure-komponenter. Om det behövs kan du [begära en kvot ökning](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

> [!NOTE]
> De virtuella datorer och SSD-komponenter som anges här är för själva vFXT-klustret. Du behöver ytterligare kvot för de virtuella datorer och SSD som du tänker använda för beräknings gruppen.  Kontrol lera att kvoten är aktive rad för den region där du vill köra arbets flödet.

|Azure-komponent|Kvot|
|----------|-----------|
|Virtuella maskiner|3 eller flera E32s_v3|
|Premium SSD-lagring|200 GB OS-utrymme plus 1 TB till 4 TB cacheutrymme per nod |
|Lagringskonto (valfritt) |v2|
|Datalagring i serverdel (valfritt) |En ny LRS BLOB-behållare |

## <a name="accept-software-terms"></a>Godkänn program varu villkoren

> [!NOTE]
> Det här steget krävs inte om en prenumerations ägare skapar ett AVERT vFXT-kluster.

När klustret skapas måste du godkänna villkoren för tjänsten för den Avera vFXT-programvaran. Om du inte är prenumerations ägare måste du ha en prenumerations ägare som accepterar villkoren i förväg. Det här steget behöver bara utföras en gång per prenumeration.

Så här godkänner du program varu villkoren i förväg:

1. Öppna ett Cloud Shell i Azure Portal eller genom att bläddra till <https://shell.azure.com>. Logga in med ditt prenumerations-ID.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Utfärda det här kommandot för att acceptera tjänst villkoren och aktivera programmerings åtkomst för den Avera vFXT för Azures program avbildning:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Skapa en slut punkt för lagrings tjänsten i ditt virtuella nätverk (om det behövs)

En [tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) håller Azure Blob-trafik lokalt i stället för att dirigera den utanför det virtuella nätverket. Det rekommenderas för alla Avera vFXT för Azure-kluster som använder Azure Blob för Server dels data lagring.

Om du tillhandahåller ett befintligt virtuellt nätverk och skapar en ny Azure Blob-behållare för Server dels lagringen som en del av klustret skapas, måste du ha en tjänst slut punkt i nätverket för Microsoft-lagring. Den här slut punkten måste finnas innan klustret skapas, annars Miss kan skapandet.

En Storage Service-slutpunkt rekommenderas för alla Avera vFXT för Azure-kluster som använder Azure Blob Storage, även om du lägger till lagringen senare.

> [!TIP]
>
>* Hoppa över det här steget om du skapar ett nytt virtuellt nätverk som en del av skapandet av klustret.
>* Det här steget är valfritt om du inte skapar Blob Storage när klustret skapas. I så fall kan du skapa tjänst slut punkten senare om du bestämmer dig för att använda Azure blob.

Skapa lagrings tjänstens slut punkt från Azure Portal.

1. Öppna listan med virtuella nätverk i portalen.
1. Välj det virtuella nätverket för klustret.
1. Klicka på **tjänst slut punkter** på den vänstra menyn.
1. Klicka på **Lägg till** längst upp.
1. Välj tjänst ``Microsoft.Storage``.
1. Välj klustrets undernät.
1. Klicka på **Lägg till**längst ned på sidan.

   ![Azure Portal skärm bild med anteckningar för stegen för att skapa tjänst slut punkten](media/avere-vfxt-service-endpoint.png)

## <a name="next-step-create-the-vfxt-cluster"></a>Nästa steg: skapa vFXT-klustret

När du har slutfört de här kraven kan du gå vidare till skapa själva klustret. Läs [distribuera vFXT-klustret](avere-vfxt-deploy.md) för instruktioner.
