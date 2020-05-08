---
title: Självstudie – distribuera vSphere-kluster i Azure
description: Lär dig att distribuera ett vSphere-kluster i Azure med hjälp av Azure VMWare-lösning (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: a11928dd936c29b8be0816257a64deadacda4578
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82928558"
---
# <a name="tutorial-deploy-an-avs-private-cloud-in-azure"></a>Självstudie: Distribuera ett privat AVS-moln i Azure

Med Azure VMware-lösningen (AVS) kan du distribuera ett vSphere-kluster i Azure. Den minsta första distributionen är tre värdar. Ytterligare värdar kan läggas till en i taget, upp till högst 16 värdar per kluster. 

Eftersom AVS inte tillåter att du hanterar ditt privata moln med din lokala vCenter vid lanseringen måste du utföra ytterligare konfiguration av och anslutning till en lokal vCenter-instans, ett virtuellt nätverk med mera. Dessa procedurer och relaterade krav kommer att behandlas i den här själv studie serien.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett privat AVS-moln
> * Verifiera att det privata molnet har distribuerats

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Lämpliga administrativa rättigheter och behörigheter för att skapa ett privat moln.
- Se till att du har rätt nätverks konfiguration enligt beskrivningen i [Självstudier: check lista för nätverk](tutorial-network-checklist.md).

## <a name="register-the-resource-provider"></a>Registrera resursprovidern

För att kunna använda Azure VMWare-lösningen måste du först registrera resurs leverantören. I följande exempel registreras resurs leverantören med din prenumeration.

```azurecli-interactive
az provider register -n Microsoft.VMwareVirtustream --subscription <your subscription ID>
```

Ytterligare sätt att registrera resurs leverantören finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-private-cloud"></a>Skapa ett privat moln

Du kan skapa ett privat AVS-moln med hjälp av [Azure Portal](#azure-portal) eller med hjälp av [Azure CLI](#azure-cli).

### <a name="azure-portal"></a>Azure Portal

I Azure Portal väljer du **+ skapa en ny resurs**. I text rutan **Sök i Marketplace** och välj `Azure VMware Solution` **Azure VMware-lösning** i listan. I fönstret **Azure VMware-lösning** väljer du **skapa**

Ange värden för fälten på fliken **grundläggande** . I följande tabell visas en detaljerad lista över egenskaperna.

| Fält   | Värde  |
| ---| --- |
| **Prenumeration** | Prenumerationen som du planerar att använda för distributionen.|
| **Resursgrupp** | Resurs gruppen för dina privata moln resurser. |
| **Position** | Välj en plats, t. ex. **USA, östra**.|
| **Resurs namn** | Namnet på ditt AVS-privata moln. |
| **SKU** | Välj followng SKU-värde: AV36 |
| **Lagras** | Detta är antalet värdar som ska läggas till i det privata moln klustret. Standardvärdet är 3. Det här värdet kan höjas eller sänkas efter distributionen.  |
| **administratörs lösen ord för vCenter** | Ange ett lösen ord för moln administratören. |
| **Lösen ord för NSX-T Manager** | Ange ett NSX-T-administratörs lösen ord. |
| **Adress block** | Ange ett IP-adressblock för CIDR-nätverket för det privata molnet. Ett exempel är 10.175.0.0/22. |

:::image type="content" source="./media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="skapa ett privat moln" border="true":::

När du är färdig väljer du **Granska + skapa**. På nästa skärm kontrollerar du att informationen har angetts. Om informationen är korrekt väljer du **skapa**.

> [!NOTE]
> Det här steget tar ungefär två timmar. 

### <a name="azure-cli"></a>Azure CLI

Du kan också använda Azure CLI för att skapa ett moln privat moln i Azure. Om du vill göra detta kan du använda Azure Cloud Shell. följande steg visar hur du gör detta.

#### <a name="open-azure-cloud-shell"></a>Öppna Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto.

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell i en separat webbläsarflik genom att gå till https://shell.azure.com/bash. Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

#### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Skapa ett privat moln

Om du vill skapa ett moln privat moln måste du ange ett resurs grupp namn, ett namn för det privata molnet, en plats, kluster storleken


|Egenskap  |Beskrivning  |
|---------|---------|
|Resurs grupps namn     | Namnet på den resurs grupp som du distribuerar det privata molnet till.        |
|Namn på privat moln     | Namnet på det privata molnet.        |
|Plats     | Den plats som används för det privata molnet         |
|Kluster storlek     | Klustrets storlek. Minimivärdet är 3.         |
|Nätverks block     | CIDR-intervallet som ska användas för det privata molnet. Vi rekommenderar att det är unikt från din lokala miljö och din Azure-miljö.        |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22
```

## <a name="verify-deployment-was-successful"></a>Verifiera att distributionen lyckades

Gå till den resurs grupp som du skapade och välj ditt privata moln när distributionen är klar visas följande skärm och du ser statusen **slutförd**.

:::image type="content" source="./media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verifiera att det privata molnet har distribuerats" border="true":::

## <a name="delete-a-private-cloud"></a>Ta bort ett privat moln

Om du har ett moln privat moln som du har kontrollerat att du inte längre behöver kan du ta bort det. När du tar bort ett privat moln raderas alla kluster tillsammans med alla komponenter.

Det gör du genom att navigera till ditt privata moln i Azure Portal och välja **ta bort**. På sidan bekräftelse bekräftar du med namnet på det privata molnet och väljer **Ja**.

> [!CAUTION]
> Borttagning av det privata molnet är en åtgärd som inte kan ångras. När det privata molnet har tagits bort går det inte att återställa data eftersom de avslutar alla aktiva arbets belastningar, komponenter och förstör alla privata moln data och konfigurations inställningar, inklusive offentliga IP-adresser. 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett privat AVS-moln
> * Verifierade att det privata molnet har distribuerats

Fortsätt till nästa självstudie och lär dig hur du skapar ett virtuellt nätverk för användning med ditt privata moln som en del av att konfigurera lokal hantering för dina privata moln kluster.

> [!div class="nextstepaction"]
> [Skapa en Virtual Network](tutorial-configure-networking.md)
