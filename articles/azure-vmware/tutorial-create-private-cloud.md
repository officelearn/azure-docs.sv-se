---
title: Självstudie – distribuera vSphere-kluster i Azure
description: Lär dig att distribuera ett vSphere-kluster i Azure med hjälp av Azure VMWare-lösningen
ms.topic: tutorial
ms.date: 09/07/2020
ms.openlocfilehash: 69a29a459ba283bb34169112ac2fa174ac6a14af
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512391"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud-in-azure"></a>Självstudie: Distribuera ett privat moln för Azure VMware-lösningar i Azure

Med Azure VMware-lösningen kan du distribuera ett vSphere-kluster i Azure. Den minsta första distributionen är tre värdar. Ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster. 

Eftersom Azure VMware-lösningen inte tillåter att du hanterar ditt privata moln med din lokala vCenter vid start, krävs ytterligare konfiguration av och anslutning till en lokal vCenter-instans, ett virtuellt nätverk med mera. Dessa procedurer och relaterade krav beskrivs i den här självstudien.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett privat moln för Azure VMware-lösningen
> * Verifiera att det privata molnet har distribuerats

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Lämpliga administrativa rättigheter och behörigheter för att skapa ett privat moln.
- Se till att du har rätt nätverks konfiguration enligt beskrivningen i [Självstudier: check lista för nätverk](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Du kan skapa ett privat moln i Azure VMware-lösningen med hjälp av [Azure Portal](#azure-portal) eller med hjälp av [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Azure-portalen

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-avs-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

I stället för Azure Portal skapa ett privat moln i Azure VMware-lösningen kan du använda Azure CLI med hjälp av Azure Cloud Shell. Det är ett kostnads fritt interaktivt gränssnitt med vanliga Azure-verktyg förinstallerade och konfigurerade för användning med ditt konto. 

#### <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Om du vill öppna Cloud Shell väljer du **testa den** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/bash](https://shell.azure.com/bash) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```
azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Skapa ett privat moln

Ange ett resurs grupp namn, ett namn för det privata molnet, en plats, klustrets storlek.

| Egenskap  | Beskrivning  |
| --------- | ------------ |
| **-g** (resurs gruppens namn)     | Namnet på resurs gruppen för dina privata moln resurser.        |
| **-n** (namn på privat moln)     | Namnet på ditt privata moln i Azure VMware-lösningen.        |
| **--plats**     | Den plats som används för ditt privata moln.         |
| **--kluster storlek**     | Klustrets storlek. Minimivärdet är 3.         |
| **--Network-block**     | CIDR IP-adressens nätverks block som ska användas för ditt privata moln. Adress blocket får inte överlappa adress block som används i andra virtuella nätverk som finns i din prenumeration och lokala nätverk.        |
| **--SKU** | SKU-värdet: AV36 |

```
azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="delete-a-private-cloud-azure-portal"></a>Ta bort ett privat moln (Azure Portal)

Om du har ett privat moln i Azure VMware-lösningen som du inte längre behöver kan du ta bort det. När du tar bort ett privat moln raderas alla kluster, tillsammans med alla komponenter.

Det gör du genom att navigera till ditt privata moln i Azure Portal och välja **ta bort**. På sidan bekräftelse bekräftar du med namnet på det privata molnet och väljer **Ja**.

> [!CAUTION]
> Borttagning av det privata molnet är en åtgärd som inte kan ångras. När det privata molnet har tagits bort går det inte att återställa data eftersom de avslutar alla aktiva arbets belastningar, komponenter och förstör alla privata moln data och konfigurations inställningar, inklusive offentliga IP-adresser. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett privat moln för Azure VMware-lösningen
> * Verifierade att det privata molnet har distribuerats

Fortsätt till nästa självstudie och lär dig hur du skapar ett virtuellt nätverk för användning med ditt privata moln som en del av att konfigurera lokal hantering för dina privata moln kluster.

> [!div class="nextstepaction"]
> [Skapa en Virtual Network](tutorial-configure-networking.md)
