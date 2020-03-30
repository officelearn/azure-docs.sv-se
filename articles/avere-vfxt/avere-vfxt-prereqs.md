---
title: Avere vFXT-förutsättningar - Azure
description: Förutsättningar för Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: rohogue
ms.openlocfilehash: a183989cc666f00da4be077c719c40d2524fd6e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252550"
---
# <a name="prepare-to-create-the-avere-vfxt"></a>Förbereda för att skapa Avere vFXT

I den här artikeln beskrivs nödvändiga uppgifter för att skapa ett Avere vFXT-kluster.

## <a name="create-a-new-subscription"></a>Skapa en ny prenumeration

Börja med att skapa en ny Azure-prenumeration. Använd en separat prenumeration för varje Avere vFXT-projekt för att förenkla utgiftsspårning och rensning och för att se till att andra projekt inte påverkas av kvoter eller resursbegränsning vid etablering av klusterarbetsflödet.

Så här skapar du en ny Azure-prenumeration i Azure-portalen:

1. Navigera till [bladet Prenumerationer](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)
1. Klicka på knappen **+ Lägg till** högst upp
1. Logga in om du uppmanas att göra det
1. Välj ett erbjudande och gå igenom stegen för att skapa en ny prenumeration

## <a name="configure-subscription-owner-permissions"></a>Konfigurera behörigheter för prenumerationsägare

En användare med ägarbehörigheter för prenumerationen ska skapa vFXT-klustret. Klusterskapande kräver att en ägare accepterar programvaruvillkoren för tjänsten och auktoriserar ändringar av nätverks- och lagringsresurser.

Det finns några lösningar som gör det möjligt för en icke-ägare att skapa ett Avere vFXT för Azure-kluster. Dessa scenarier innebär att begränsa resurser och tilldela ytterligare rollbaserade åtkomstkontroll (RBAC) roller till skaparen. I alla dessa fall måste en prenumerationsägare också [acceptera avere vFXT-programvillkoren](#accept-software-terms) i förväg.

| Scenario | Begränsningar | Åtkomstroller som krävs för att skapa Avere vFXT-klustret |
|----------|--------|-------|
| Resursgruppsadministratör skapar vFXT | Det virtuella nätverket, klusterstyrenheten och klusternoderna måste skapas inom resursgruppen. | [Användaråtkomstadministratörs-](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagarroller,](../role-based-access-control/built-in-roles.md#contributor) som båda är begränsade till målresursgruppen. |
| Använda ett befintligt, externt virtuellt nätverk | Klusterstyrenheten och klusternoderna skapas i vFXT:s resursgrupp men använder ett befintligt virtuellt nätverk i en annan resursgrupp. | (1) [Användaråtkomstadministratörs-](../role-based-access-control/built-in-roles.md#user-access-administrator) och [deltagarroller](../role-based-access-control/built-in-roles.md#contributor) som omfattas av resursgruppen vFXT. och (2) [Deltagare i virtuell dator,](../role-based-access-control/built-in-roles.md#virtual-machine-contributor) [användaråtkomstadministratör](../role-based-access-control/built-in-roles.md#user-access-administrator)och [Avere-deltagarroller](../role-based-access-control/built-in-roles.md#avere-contributor) som omfattas av det virtuella nätverkets resursgrupp. |
| Anpassad roll för klusterskapare | Inga begränsningar för resursplacering. Den här metoden ger icke-ägare betydande privilegier. | Prenumerationsägaren skapar en anpassad RBAC-roll som förklaras i [den här artikeln](avere-vfxt-non-owner.md). |

## <a name="quota-for-the-vfxt-cluster"></a>Kvot för vFXT-klustret

Kontrollera att du har tillräcklig kvot för följande Azure-komponenter. Begär vid behov [en kvotökning](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> De virtuella datorer och SSD-komponenter som anges här är för vFXT-klustret självt. Kom ihåg att du också behöver kvot för de virtuella datorer och SSD-datorer som du ska använda för beräkningsgruppen.
>
> Kontrollera att kvoten är aktiverad för den region där du tänker köra arbetsflödet.

|Azure-komponent|Kvot|
|----------|-----------|
|Virtuella datorer|3 eller fler E32s_v3 (en per klusternod) |
|Premium SSD-lagring|200 GB OS-utrymme plus 1 TB till 4 TB cacheutrymme per nod |
|Lagringskonto (valfritt) |v2|
|Data backend-lagring (valfritt) |En ny LRS Blob-behållare |
<!-- this table also appears in the overview - update it there if updating here -->

## <a name="accept-software-terms"></a>Acceptera programvaruvillkor

> [!TIP]
> Hoppa över det här steget om en prenumerationsägare skapar Avere vFXT-klustret.

När klustret skapas måste du acceptera användarvillkoren för Avere vFXT-programvaran. Om du inte är en prenumerationsägare ber du en prenumerationsägare godkänna villkoren i förväg.

Det här steget behöver bara göras en gång per prenumeration.

Så här accepterar du programvaruvillkoren i förväg:

1. Öppna ett molnskal i Azure-portalen <https://shell.azure.com>eller genom att bläddra till . Logga in med ditt prenumerations-ID.

   ```azurecli
    az login
    az account set --subscription <subscription ID>
   ```

1. Utfärda det här kommandot om du vill acceptera tjänstvillkor och aktivera programmatisk åtkomst för avbildningen av Avere vFXT för Azure:

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   ```

## <a name="create-a-storage-service-endpoint-in-your-virtual-network-if-needed"></a>Skapa en slutpunkt för lagringstjänster i det virtuella nätverket (om det behövs)

En [tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) håller Azure Blob-trafik lokal i stället för att dirigera den utanför det virtuella nätverket. Vi rekommenderar alla Avere vFXT för Azure-kluster som använder Azure Blob för serverdelsdatalagring.

Om du skapar ett nytt virtuellt nätverk när du skapar klustret skapas en slutpunkt automatiskt. Om du tillhandahåller ett befintligt virtuellt nätverk måste den ha en slutpunkt för Microsoft-lagringstjänster om du vill skapa en ny Blob-lagringsbehållare när klustret skapas.<!-- if there is no endpoint in that situation, the cluster creation will fail -->

> [!TIP]
>
>* Hoppa över det här steget om du skapar ett nytt virtuellt nätverk som en del av klusterskapandet.
>* En slutpunkt är valfri om du inte skapar Blob-lagring när klustret skapas. I så fall kan du skapa tjänstslutpunkten senare om du bestämmer dig för att använda Azure Blob.

Skapa slutpunkten för lagringstjänster från Azure-portalen.

1. Öppna listan över virtuella nätverk i portalen.
1. Välj det virtuella nätverket för klustret.
1. Klicka på **Tjänstslutpunkter** från den vänstra menyn.
1. Klicka på **Lägg till** högst upp.
1. Välj tjänsten ``Microsoft.Storage``.
1. Markera klustrets undernät.
1. Klicka på **Lägg till**längst ned.

   ![Skärmbild av Azure-portalen med anteckningar för stegen för att skapa tjänstens slutpunkt](media/avere-vfxt-service-endpoint.png)

## <a name="next-steps"></a>Nästa steg

När du har slutfört dessa förutsättningar kan du skapa klustret. Läs [Distribuera vFXT-klustret](avere-vfxt-deploy.md) för instruktioner.
