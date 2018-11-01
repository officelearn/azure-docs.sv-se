---
title: Avere vFXT krav – Azure
description: Krav för Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 823bf50a54ff43fa95f7136c137e3d8f3303c3e0
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634382"
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
* Tillåt controller klusternod att hantera resursgrupper och virtuella nätverk 
* Tillåt att kluster-kontrollanten för att skapa och ändra klusternoderna 

Det finns två lösningar om du inte vill ge ägaråtkomst till de användare som skapar vFXT:

* En resursgruppägare kan skapa ett kluster om dessa villkor är uppfyllda:

  * En prenumerant måste [acceptera Licensvillkor för programvara i Avere vFXT](#accept-software-terms-in-advance) och [skapa klusterrollen noden åtkomst](avere-vfxt-deploy.md#create-the-cluster-node-access-role).
  * Alla Avere vFXT resurser måste distribueras i resursgruppen, inklusive:
    * Kluster-domänkontrollant
    * Klusternoder
    * Blob Storage
    * Nätverkselement
 
* Användare med ingen ägare kan skapa vFXT kluster om en extra rollen skapas och tilldelas till användaren i tid. Den här rollen ger dock betydande behörigheter till dessa användare. [Den här artikeln](avere-vfxt-non-owner.md) förklarar hur du godkänner icke-ägare att skapa kluster.

## <a name="quota-for-the-vfxt-cluster"></a>Kvoten för vFXT klustret

Du måste ha tillräckligt många följande Azure-komponenterna. Om det behövs [öka kvoten](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

> [!NOTE]
> Virtuella datorer och SSD-komponenter som beskrivs här är för själva klustret vFXT. Du behöver större kvot för virtuella datorer och SSD som du planerar att använda för din beräknings-servergrupp.  Se till att kvoten är aktiverad för den region där du vill köra arbetsflödet.

|Azure-komponent|Kvot|
|----------|-----------|
|Virtuella datorer|3 eller fler D16s_v3 eller E32s_v3|
|Premium SSD-lagring|200 GB OS blanksteg plus 1 TB till 4 TB cache-minne per nod |
|Storage-konto (valfritt) |v2|
|Serverdelen datalagring (valfritt) |En ny LRS Blob-behållare |

## <a name="accept-software-terms-in-advance"></a>Acceptera villkoren i förväg för programvara

> [!NOTE] 
> Det här steget krävs inte om en prenumerationsägare skapar Avere vFXT klustret.

Innan du kan skapa ett kluster, måste du acceptera villkoren för Avere vFXT programvaran. Om du inte är en prenumerationsägare, har du en prenumerationsägare accepterar villkoren i tid. Det här steget behöver bara göras en gång per prenumeration.

Accepterar villkoren i förväg för programvaran: 

1. Öppna ett gränssnitt för molnet i Azure-portalen eller genom att bläddra till <https://shell.azure.com>. Logga in med ditt prenumerations-ID.

   ```azurecli
    az login
    az account set --subscription abc123de-f456-abc7-89de-f01234567890
   ```

1. Kör detta kommando för att acceptera tjänstvillkoren och aktiverar Programmeringsåtkomst för Avere vFXT för avbildningar i Azure-program: 

   ```azurecli
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-controller:latest
   az vm image accept-terms --urn microsoft-avere:vfxt:avere-vfxt-node:latest
   ```

## <a name="next-step-create-the-vfxt-cluster"></a>Nästa steg: skapa vFXT-kluster

När du har slutfört dessa krav, kan du gå vidare till skapa själva klustret. Läs [distribuera vFXT klustret](avere-vfxt-deploy.md) anvisningar.