---
title: Kryptera distributions data
description: Lär dig mer om kryptering av data bestående för dina behållar instans resurser och hur du krypterar data med en kundhanterad nyckel
ms.topic: article
ms.date: 01/10/2020
ms.author: danlep
ms.openlocfilehash: 146effd7f1a7ad1ddd94886d1a79e2914bd1c94b
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904216"
---
# <a name="encrypt-deployment-data"></a>Kryptera distributions data

När du kör Azure Container Instances-resurser (ACI) i molnet samlar ACI-tjänsten samman och behåller data som är relaterade till dina behållare. ACI krypterar automatiskt dessa data när de sparas i molnet. Den här krypteringen skyddar dina data och hjälper dig att uppfylla organisationens säkerhets-och efterlevnads åtaganden. ACI ger dig också möjlighet att kryptera dessa data med din egen nyckel, vilket ger dig större kontroll över data som är relaterade till dina ACI-distributioner.

## <a name="about-aci-data-encryption"></a>Om ACI Data Encryption 

Data i ACI krypteras och dekrypteras med hjälp av 256-bitars AES-kryptering. Den är aktive rad för alla ACI-distributioner och du behöver inte ändra din distribution eller behållare för att dra nytta av den här krypteringen. Detta inkluderar metadata om distributionen, miljövariabler, nycklar som skickas till dina behållare och loggar som behålls när behållarna har stoppats så att du fortfarande kan se dem. Kryptering påverkar inte prestanda för behållar gruppen och det finns ingen ytterligare kostnad för kryptering.

## <a name="encryption-key-management"></a>Hantering av krypterings nyckel

Du kan förlita dig på Microsoft-hanterade nycklar för kryptering av dina behållar data, eller så kan du hantera kryptering med dina egna nycklar. I följande tabell jämförs de här alternativen: 

|    |    Microsoft-hanterade nycklar     |     Kundhanterade nycklar     |
|----|----|----|
|    Kryptering/dekryptering    |    Azure    |    Azure    |
|    Nyckel lagring    |    Microsoft nyckel lager    |    Azure Key Vault    |
|    Största ansvar för nyckel rotation    |    Microsoft    |    Kund    |
|    Nyckel åtkomst    |    Endast Microsoft    |    Microsoft, kund    |

Resten av dokumentet beskriver de steg som krävs för att kryptera dina ACI-distributions data med din nyckel (kundhanterad nyckel). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="encrypt-data-with-a-customer-managed-key"></a>Kryptera data med en kundhanterad nyckel

### <a name="create-service-principal-for-aci"></a>Skapa tjänstens huvud namn för ACI

Det första steget är att se till att din [Azure-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) har ett huvud namn för tjänsten som tilldelas för att bevilja behörighet till tjänsten Azure Container instances. 

Följande CLI-kommando konfigurerar ACI SP i Azure-miljön:

```azurecli-interactive
az ad sp create --id 6bb8e274-af5d-4df2-98a3-4fd78b4cafd9
```

Utdata från att köra det här kommandot ska visa ett huvud namn för tjänsten som har kon figurer ATS med "displayName": "Azure Container instance service".

### <a name="create-a-key-vault-resource"></a>Skapa en Key Vault resurs

Skapa en Azure Key Vault med [Azure Portal](https://docs.microsoft.com/azure/key-vault/quick-create-portal#create-a-vault), [CLI](https://docs.microsoft.com/azure/key-vault/quick-create-cli)eller [PowerShell](https://docs.microsoft.com/azure/key-vault/quick-create-powershell). 

Använd följande rikt linjer för egenskaperna för nyckel valvet: 
* Namn: ett unikt namn krävs. 
* Prenumeration: Välj en prenumeration.
* Under resurs grupp väljer du antingen en befintlig resurs grupp eller skapar en ny och anger ett resurs grupp namn.
* Välj en plats i rullgardinsmenyn plats.
* Du kan lämna de andra alternativen till standardinställningarna eller välja baserat på ytterligare krav.

> [!IMPORTANT]
> När du använder Kundhanterade nycklar för att kryptera en ACI, rekommenderar vi att du anger följande två egenskaper i nyckel valvet, mjuk borttagning och rensar inte. Dessa egenskaper är inte aktiverade som standard, men kan aktive ras med antingen PowerShell eller Azure CLI på ett nytt eller befintligt nyckel valv.

### <a name="generate-a-new-key"></a>Generera en ny nyckel 

När du har skapat nyckel valvet navigerar du till resursen i Azure Portal. På den vänstra navigerings menyn på resurs bladet, under Inställningar, klickar du på **nycklar**. Klicka på generera/importera i vyn för "nycklar" för att generera en ny nyckel. Använd ett unikt namn för den här nyckeln och andra inställningar som baseras på dina krav. 

![Generera en ny nyckel](./media/container-instances-encrypt-data/generate-key.png)

### <a name="set-access-policy"></a>Ange åtkomst princip

Skapa en ny åtkomst princip som gör att ACI-tjänsten kan komma åt din nyckel.

* När din nyckel har skapats går du tillbaka till resurs bladet för nyckel valv under Inställningar och klickar på **åtkomst principer**.
* På sidan "åtkomst principer" för ditt nyckel valv klickar du på **Lägg till åtkomst princip**.
* Ange *nyckel behörigheter* för att inkludera **Hämta** och ta fram **nyckel** ![Ange nyckel behörigheter](./media/container-instances-encrypt-data/set-key-permissions.png)
* För *Välj huvud konto*väljer du **Azure Container instance service**
* Klicka på **Lägg till** längst ned 

Åtkomst principen bör nu visas i nyckel valvets åtkomst principer.

![Ny åtkomst princip](./media/container-instances-encrypt-data/access-policy.png)

### <a name="modify-your-json-deployment-template"></a>Ändra mallen för JSON-distribution

> [!IMPORTANT]
> Kryptering av distributions data med en kundhanterad nyckel är tillgängligt i den senaste API-versionen (2019-12-01) som för närvarande är distribuerad. Ange den här API-versionen i distributions mal len. Om du har problem med detta kan du kontakta Azure-supporten.

När nyckel valvets nyckel och åtkomst princip har kon figurer ATS lägger du till följande egenskap i ACI-distributions mal len. Du kan lära dig mer om att distribuera ACI-resurser med en mall i [självstudien: Distribuera en grupp med flera behållare med hjälp av en Resource Manager-mall](https://docs.microsoft.com/azure/container-instances/container-instances-multi-container-group). 

Mer specifikt, under avsnittet Egenskaper för behållar grupp i distributions mal len, lägger du till en "encryptionProperties" som innehåller följande värden:
* vaultBaseUrl: DNS-namnet på ditt nyckel valv finns på bladet översikt i Key Vault-resursen i portalen
* nyckel namn: namnet på nyckeln som genererades tidigare
* nyckel version: den aktuella versionen av nyckeln. Du hittar detta genom att klicka på själva nyckeln (under "nycklar" i avsnittet Inställningar i din Key Vault-resurs)


```json
"resources": [
    {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",    
        "properties": {
            "encryptionProperties": {
                "vaultBaseUrl": "https://example.vault.azure.net",
                "keyName": "acikey",
                "keyVersion": "xxxxxxxxxxxxxxxx"
            },
            "containers": {
                [...]
            }
        }
    }
]
```

### <a name="deploy-your-resources"></a>Distribuera dina resurser

Om du har skapat och redigerat mallfilen på Skriv bordet kan du överföra den till din Cloud Shell katalog genom att dra filen till den. 

Skapa en resursgrupp med kommandot [az group create][az-group-create].

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Distribuera mallen med kommandot [AZ Group Deployment Create][az-group-deployment-create] .

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file deployment-template.json
```

Inom några sekunder bör du få ett första svar från Azure. När distributionen är klar krypteras alla data som är relaterade till den bestående av ACI-tjänsten med den nyckel du angav.

<!-- LINKS - Internal -->
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create