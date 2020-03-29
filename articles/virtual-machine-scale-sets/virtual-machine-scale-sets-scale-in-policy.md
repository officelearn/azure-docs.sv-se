---
title: Använda anpassade skalningsprinciper med Azure-skalningsuppsättningar för virtuella datorer
description: Lär dig hur du använder anpassade skalningsprinciper med Azure-skalningsuppsättningar för virtuella datorer som använder konfiguration för automatisk skalning för att hantera instansantal
services: virtual-machine-scale-sets
author: avirishuv
manager: vashan
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: avverma
ms.openlocfilehash: ffcdaf76bdd08ee5505ddbeff6a6698e231b6171
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919846"
---
# <a name="use-custom-scale-in-policies-with-azure-virtual-machine-scale-sets"></a>Använda anpassade skalningsprinciper med Azure-skalningsuppsättningar för virtuella datorer

En distribution av skalningsuppsättning för virtuella datorer kan skalas ut eller skalas in baserat på en matris med mått, inklusive anpassade mått för plattform och användardefinierade. Medan en utskalning skapar nya virtuella datorer baserat på skalningsuppsättningsmodellen, påverkar en skalning som körs virtuella datorer som kan ha olika konfigurationer och/eller funktioner när arbetsbelastningen för skalningsuppsättningen utvecklas. 

Funktionen för inskalningsprincip ger användarna ett sätt att konfigurera i vilken ordning virtuella datorer skalas in, genom tre inskalningskonfigurationer: 

1. Default
2. NyasteVM
3. OldestVM

### <a name="default-scale-in-policy"></a>Standardskalningsprincip

Som standard tillämpar skalningsuppsättningen för virtuella datorer den här principen för att avgöra vilka instanser som ska skalas in. Med *standardprincipen* väljs virtuella datorer för inskalning i följande ordning:

1. Balansera virtuella datorer över tillgänglighetszoner (om skalningsuppsättningen distribueras i zonkonfiguration)
2. Balansera virtuella datorer över feldomäner (bästa möjliga insats)
3. Ta bort virtuell dator med högsta instans-ID

Användare behöver inte ange en skalningsprincip om de bara vill att standardordningen ska följas.

Observera att balansering mellan tillgänglighetszoner eller feldomäner inte flyttar instanser över tillgänglighetszoner eller feldomäner. Avvägningen uppnås genom borttagning av virtuella datorer från obalanserade tillgänglighetszoner eller feldomäner tills fördelningen av virtuella datorer blir balanserad.

### <a name="newestvm-scale-in-policy"></a>NysteVM-inskalningspolicy

Den här principen tar bort den senaste skapade virtuella datorn i skalningsuppsättningen, efter att ha balanserat virtuella datorer över tillgänglighetszoner (för zondistributioner). För att aktivera den här principen krävs en konfigurationsändring på skaluppsättningsmodellen för den virtuella datorn.

### <a name="oldestvm-scale-in-policy"></a>Inskalningsprincip för äldstaVM

Den här principen tar bort den äldsta skapade virtuella datorn i skalningsuppsättningen, efter att ha balanserat virtuella datorer över tillgänglighetszoner (för zondistributioner). För att aktivera den här principen krävs en konfigurationsändring på skaluppsättningsmodellen för den virtuella datorn.

## <a name="enabling-scale-in-policy"></a>Aktivera inskalningsprincip

En skalningsprincip definieras i skaluppsättningsmodellen för den virtuella datorn. Som nämnts i avsnitten ovan behövs en principdefinition för skalning när principerna "NewestVM" och "OldestVM" använders. Skalan för virtuella datorer använder automatiskt skalorningsprincipen Standard om det inte finns någon principdefinition för skalning som finns på skalningsuppsättningsmodellen. 

En skalningsprincip kan definieras på skaluppsättningsmodellen för virtuella datorer på följande sätt:

### <a name="azure-portal"></a>Azure Portal
 
Följande steg definierar skalningsprincipen när du skapar en ny skalningsuppsättning. 
 
1. Gå till **skalningsuppsättningar för virtuella datorer**.
1. Välj **+ Lägg till** om du vill skapa en ny skaluppsättning.
1. Gå till fliken **Skalning.** 
1. Leta reda på avsnittet **Skala in princip.**
1. Välj en skalningsprincip i listrutan.
1. När du är klar med att skapa den nya skalningsuppsättningen väljer du **knappen Granska + skapa.**

### <a name="using-api"></a>Med API:et

Kör en PUT på den virtuella datorns skaluppsättning med API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["OldestVM"]  
        } 
    }    
} 
```
### <a name="azure-powershell"></a>Azure PowerShell

Skapa en resursgrupp och skapa sedan en ny skalningsuppsättning med skalningsprincipuppsättning som *OldestVM*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName "myResourceGroup" -Location "<VMSS location>"
New-AzVmss `
  -ResourceGroupName "myResourceGroup" `
  -Location "<VMSS location>" `
  -VMScaleSetName "myScaleSet" `
  -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

I följande exempel läggs en skalningsprincip till när du skapar en ny skalningsuppsättning. Skapa först en resursgrupp och skapa sedan en ny skalningsuppsättning med skalningsprincipen som *OldestVM*. 

```azurecli-interactive
az group create --name <myResourceGroup> --location <VMSSLocation>
az vmss create \
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --image UbuntuLTS \
  --admin-username <azureuser> \
  --generate-ssh-keys \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Använda mall

Lägg till följande under "egenskaper" i mallen:

```json
"scaleInPolicy": {  
      "rules": ["OldestVM"]  
}
```

Ovanstående block anger att den virtuella datorns skaluppsättning ska ta bort den äldsta virtuella datorn i en zonbalanserad skalningsuppsättning, när en inskalning utlöses (genom Automatisk skalning eller manuell borttagning).

När en skaluppsättning för virtuella datorer inte är zonbalanserad tas först skalningsuppsättningen bort virtuella datorer över de obalanserade zonerna. Inom de obalanserade zonerna använder skalningsuppsättningen den inskalningsprincipen som anges ovan för att avgöra vilken virtuell dator som ska skalas in. I det här fallet väljer skalningsuppsättningen den äldsta virtuella datorn i den zonen som ska tas bort inom en zon som är obalanserad.

För skalningsuppsättningen för den virtuella datorn som inte är zonbaserad väljer principen den äldsta virtuella datorn över skalningsuppsättningen för borttagning.

Samma process gäller när du använder "NewestVM" i ovanstående skaväg-in-policy.

## <a name="modifying-scale-in-policies"></a>Ändra skalningsprinciper

Ändra skalade principen följer samma process som att tillämpa inskalningsprincipen. Om du till exempel i exemplet ovan vill ändra principen från 'OldestVM' till 'NewestVM', kan du göra det genom att:

### <a name="azure-portal"></a>Azure Portal

Du kan ändra skalningsprincipen för en befintlig skalningsuppsättning via Azure-portalen. 
 
1. I en befintlig skalningsuppsättning för virtuella datorer väljer du **Skala** på menyn till vänster.
1. Välj fliken **Skala i princip.**
1. Välj en skalningsprincip i listrutan.
1. När du är klar väljer du **Spara**. 

### <a name="using-api"></a>Med API:et

Kör en PUT på den virtuella datorns skaluppsättning med API 2019-03-01:

```
PUT
https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<myRG>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVMSS>?api-version=2019-03-01 

{ 
"location": "<VMSS location>", 
    "properties": { 
        "scaleInPolicy": {  
            "rules": ["NewestVM"]  
        } 
    }    
}
```
### <a name="azure-powershell"></a>Azure PowerShell

Uppdatera skalningsprincipen för en befintlig skalningsuppsättning:

```azurepowershell-interactive
Update-AzVmss `
 -ResourceGroupName "myResourceGroup" `
 -VMScaleSetName "myScaleSet" `
 -ScaleInPolicy “OldestVM”
```

### <a name="azure-cli-20"></a>Azure CLI 2.0

Följande är ett exempel på hur du uppdaterar skalningsprincipen för en befintlig skalningsuppsättning: 

```azurecli-interactive
az vmss update \  
  --resource-group <myResourceGroup> \
  --name <myVMScaleSet> \
  --scale-in-policy OldestVM
```

### <a name="using-template"></a>Använda mall

I mallen, under "egenskaper", ändrar du mallen enligt nedan och distribuerar om: 

```json
"scaleInPolicy": {  
      "rules": ["NewestVM"]  
} 
```

Samma process gäller om du bestämmer dig för att ändra "NewestVM" till "Default" eller "OldestVM"

## <a name="instance-protection-and-scale-in-policy"></a>Instansskydd och inskalningspolitik

Skaluppsättningar för virtuella datorer ger två typer av [instansskydd:](./virtual-machine-scale-sets-instance-protection.md#types-of-instance-protection)

1. Skydda mot inskalning
2. Skydda mot skalningsuppsättningsåtgärder

En skyddad virtuell dator tas inte bort genom en skalningsåtgärd, oavsett vilken skalningsprincip som tillämpas. Om till exempel VM_0 (äldsta virtuella datorn i skalningsuppsättningen) är skyddad från skalning och skalningsuppsättningen har inskalningsprincipen "OldestVM", kommer VM_0 inte att beaktas för att skalas in, även om det är den äldsta virtuella datorn i skalningsuppsättningen. 

En skyddad virtuell dator kan tas bort manuellt av användaren när som helst, oavsett vilken skalningsprincip som är aktiverad i skalningsuppsättningen. 

## <a name="usage-examples"></a>Exempel på användning 

Nedanstående exempel visar hur en skaluppsättning för virtuella datorer väljer virtuella datorer som ska tas bort när en inskalningshändelse utlöses. Virtuella datorer med de högsta instans-ID:na antas vara de nyaste virtuella datorerna i skalningsuppsättningen och de virtuella datorerna med de minsta instans-ID:na antas vara de äldsta virtuella datorerna i skalningsuppsättningen. 

### <a name="oldestvm-scale-in-policy"></a>Inskalningsprincip för äldstaVM

| Händelse                 | Instans-ID:er i zon1  | Instans-ID:er i zon2  | Instans-ID:er i zon3  | Skala in markering                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inledande               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skala in              | 3, 4, 5, 10            | ***2***, 6, 9, 11      | 1, 7, 8                | Välj mellan zon 1 och 2, även om zon 3 har den äldsta virtuella datorn. Ta bort VM2 från zon 2 eftersom det är den äldsta virtuella datorn i den zonen.   |
| Skala in              | ***3,*** 4, 5, 10      | 6, 9, 11               | 1, 7, 8                | Välj zon 1 även om zon 3 har den äldsta virtuella datorn. Ta bort VM3 från zon 1 eftersom det är den äldsta virtuella datorn i den zonen.                  |
| Skala in              | 4, 5, 10               | 6, 9, 11               | ***1***, 7, 8          | Zonerna är balanserade. Ta bort VM1 i zon 3 eftersom det är den äldsta virtuella datorn i skalningsuppsättningen.                                               |
| Skala in              | ***4,*** 5, 10         | 6, 9, 11               | 7, 8                   | Välj mellan zon 1 och zon 2. Ta bort VM4 i zon 1 eftersom det är den äldsta virtuella datorn över de två zonerna.                              |
| Skala in              | 5, 10                  | ***6***, 9, 11         | 7, 8                   | Välj zon 2 även om zon 1 har den äldsta virtuella datorn. Ta bort VM6 i zon 1 eftersom det är den äldsta virtuella datorn i den zonen.                    |
| Skala in              | ***5,*** 10            | 9, 11                  | 7, 8                   | Zonerna är balanserade. Ta bort VM5 i zon 1 eftersom det är den äldsta virtuella datorn i skalningsuppsättningen.                                                |

För skalningsuppsättningar som inte är zonbaserade för den virtuella datorn väljer principen den äldsta virtuella datorn över skalningsuppsättningen för borttagning. Alla "skyddade" virtuella datorer hoppas över för borttagning.

### <a name="newestvm-scale-in-policy"></a>NysteVM-inskalningspolicy

| Händelse                 | Instans-ID:er i zon1  | Instans-ID:er i zon2  | Instans-ID:er i zon3  | Skala in markering                                                                                                               |
|-----------------------|------------------------|------------------------|------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| Inledande               | 3, 4, 5, 10            | 2, 6, 9, 11            | 1, 7, 8                |                                                                                                                                  |
| Skala in              | 3, 4, 5, 10            | 2, 6, 9, ***11***      | 1, 7, 8                | Välj mellan zon 1 och 2. Ta bort VM11 från zon 2 eftersom det är den nyaste virtuella datorn över de två zonerna.                                |
| Skala in              | 3, 4, 5, ***10***      | 2, 6, 9                | 1, 7, 8                | Välj zon 1 eftersom den har fler virtuella datorer än de andra två zonerna. Ta bort VM10 från zon 1 eftersom det är den nyaste virtuella datorn i zonen.          |
| Skala in              | 3, 4, 5                | 2, 6, ***9***          | 1, 7, 8                | Zonerna är balanserade. Ta bort VM9 i zon 2 eftersom det är den senaste virtuella datorn i skalningsuppsättningen.                                                |
| Skala in              | 3, 4, 5                | 2, 6                   | 1, 7, ***8***          | Välj mellan zon 1 och zon 3. Ta bort VM8 i zon 3 eftersom det är den senaste virtuella datorn i den zonen.                                      |
| Skala in              | 3, 4, ***5***          | 2, 6                   | 1, 7                   | Välj zon 1 även om zon 3 har den senaste virtuella datorn. Ta bort VM5 i zon 1 eftersom det är den senaste virtuella datorn i zonen.                    |
| Skala in              | 3, 4                   | 2, 6                   | 1, ***7***             | Zonerna är balanserade. Ta bort VM7 i zon 3 eftersom det är den senaste virtuella datorn i skalningsuppsättningen.                                                |

För skalningsuppsättningar som inte är zonbaserade för den virtuella datorn väljer principen den senaste virtuella datorn över skalningsuppsättningen för borttagning. Alla "skyddade" virtuella datorer hoppas över för borttagning. 

## <a name="troubleshoot"></a>Felsöka

1. Det gick inte att aktivera scaleInPolicy Om du får felmeddelandet BadRequest med felmeddelandet "Det gick inte att hitta medlemmen 'scaleInPolicy' på objekt av typen egenskaper'" kontrollerar du API-versionen som används för skalningsuppsättning för virtuella datorer. API-version 2019-03-01 eller senare krävs för den här funktionen.

2. Fel val av virtuella datorer för skalade in Se exemplen ovan. Om skaluppsättningen för den virtuella datorn är en zondistribution tillämpas skalningsprincipen först på de obalanserade zonerna och sedan över skalningsuppsättningen när den är zonbalanserad. Om ordningen för inskalning inte överensstämmer med exemplen ovan höjer du en fråga med det virtuella datorskaleuppsättningsteamet för felsökning.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [distribuerar ditt program](virtual-machine-scale-sets-deploy-app.md) på skalningsuppsättningar för virtuella datorer.