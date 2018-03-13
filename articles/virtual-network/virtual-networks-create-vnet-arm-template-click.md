---
title: "Skapa ett virtuellt nätverk | Azure Resource Manager-mall | Microsoft Docs"
description: "Lär dig hur du skapar ett virtuellt nätverk med en Azure Resource Manager-mall."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bb096f64a6bc41ad2e75c058c7a9f00bbe480207
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Skapa ett virtuellt nätverk med en Azure Resource Manager-mall

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure har två distributionsmodeller: Azure Resource Manager och klassisk. Microsoft rekommenderar att skapa resurser med Resource Manager-distributionsmodellen. Mer information om skillnaderna mellan de två modellerna finns i artikeln [Understand Azure deployment models](../azure-resource-manager/resource-manager-deployment-model.md) (Förstå Azure-distributionsmodellerna).
 
Den här artikeln förklaras hur du skapar ett VNet via Resource Manager-distributionsmodellen med hjälp av en Azure Resource Manager-mall. Du kan också skapa ett virtuellt nätverk med Resource Manager med hjälp av andra verktyg eller skapa ett virtuellt nätverk med hjälp av den klassiska distributionsmodellen genom att välja ett annat alternativ i följande lista:

> [!div class="op_single_selector"]
- [Portal](quick-create-portal.md)
- [PowerShell](quick-create-powershell.md)
- [CLI](quick-create-cli.md)
- [Mall](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (klassisk)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (klassisk)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (klassisk)](virtual-networks-create-vnet-classic-cli.md)

Lär dig hur du hämtar och ändrar och befintlig Azure Resource Manager-mall från GitHub och distribuerar mallen från GitHub, PowerShell och Azure CLI.

Om du bara distribuerar Azure Resource Manager-mallen direkt från GitHub utan ändringar, går du till [distribuera en mall från github](#deploy-the-arm-template-by-using-click-to-deploy).

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Hämta och förstå Azure Resource Manager-mallen
Du kan hämta den befintliga mallen för att skapa ett VNet och två undernät från GitHub, göra ändringar du vill och återanvända den. Om du vill göra det, gör du följande:

1. Gå till [exempelmallsidan](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
2. Klicka på **azuredeploy.json** och klicka sedan på **RAW**.
3. Spara filen på en lokal mapp på datorn.
4. Om du är bekant med mallar kan du gå vidare till steg 7.
5. Öppna filen som du sparat och titta på innehållet i **parametrar** på rad 5. Azure Resource Manager-mallens parametrar fungerar som platshållare för värden som kan anges under distributionen.
   
   | Parameter | Beskrivning |
   | --- | --- |
   | **vnetName** |Namn för det nya VNet |
   | **addressPrefix** |Adressutrymmet för VNet, i CIDR-format |
   | **subnet1Name** |Namn för det första VNet |
   | **subnet1Prefix** |CIDR-block för det första undernätet |
   | **subnet2Name** |Namn för den andra VNet |
   | **subnet2Prefix** |CIDR-block för andra undernätet |
   
   > [!IMPORTANT]
   > Azure Resource Manager-mallar som finns på GitHub kan ändras med tiden. Var noga med att kontrollera mallen innan den används.
   > 
   > 
6. Kontrollera innehållet i **resurser** och observera följande:
   
   * **type**. Typ av resurs som skapas av mallen. I det här fallet **Microsoft.Network/virtualNetworks**, vilket motsvarar ett VNet.
   * **name**. Namn på den virtuella resursen. Observera användningen av **[parameters('vnetName')]**, vilket betyder att namnet som angavs som indata från användaren eller en parameterfil under distributionen.
   * **properties**. Lista över egenskaper för resursen. Den här mallen använder adressutrymmet och undernätsegenskaperna vid skapandet av ett VNet.
7. Gå tillbaks till [exempelmallsidan](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets).
8. Klicka på **azuredeploy-parameters.json** och klicka sedan på **RAW**.
9. Spara filen på en lokal mapp på datorn.
10. Öppna filen som du just har sparat och redigera värdena för parametrarna. Använd värdena nedan för att distribuera det VNet som beskrivs i scenariot:

    ```json
        {
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. Spara filen.


## <a name="deploy-the-template-using-powershell"></a>Distribuera mallen med hjälp av PowerShell

Utför följande steg för att distribuera mallen som du hämtade med hjälp av PowerShell:

1. Installera och konfigurera Azure PowerShell genom att slutföra stegen i den [installera och konfigurera Azure PowerShell](/powershell/azure/overview) artikel.
2. Kör följande kommando för att skapa en ny resursgrupp:

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    Kommandot skapar en resursgrupp med namnet *TestRG* i den *centrala USA* azure-region. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

    Förväntad utdata:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. Kör följande kommando för att distribuera det nya VNet med hjälp av mallen och parametern filer du hämtade och ändrade ovan:

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    Förväntad utdata:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. Kör följande kommando för att visa egenskaperna för det nya VNet:

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    Förväntad utdata:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>Distribuera mallen med Klicka för att distribuera

Du kan återanvända fördefinierade Azure Resource Manager-mallar som har överförts till en GitHub-databas som hanteras av Microsoft och öppen för allmänheten. De här mallarna kan distribueras direkt från GitHub, eller hämtas och ändras så att de passar dina behov. Om du vill distribuera en mall som skapar ett VNet med två undernät, gör du följande:

1. Från en webbläsare, navigerar du till [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Bläddra ned i mallistan och klicka på **101-vnet-two-subnets**. Kontrollera **README.md**-filen enligt nedan.

    ![README.md-filen i GitHub](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Klicka på **Distribuera till Azure**. Ange dina inloggningsuppgifter för Azure vid behov. 
4. I bladet **Parametrar** anger du de värden som du vill använda för att skapa ditt nya VNet och klickar sedan på **OK**. Följande bild visar värdena för scenariot:
   
    ![ARM-mallparametrar](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. Klicka på **Resursgrupp** och välj en resursgrupp att lägga till VNet till, eller klicka på **Skapa ny** för att lägga till VNet till en ny resursgrupp. Följande bild visar resursen resursgruppsinställningarna för en ny resursgrupp som kallas **TestRG**:

    ![Resursgrupp](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. Ändra vid behov inställningarna för **Prenumeration** och **Plats** för din VNet.
7. Om du inte vill se VNet som en ikon på **Startsidan**, inaktiverar du **Fäst på startsidan**.
8. Klicka på **juridiska villkor**, Läs villkoren och klicka på **köpa** accepterar. 
9. Skapa VNet genom att klicka på **Skapa**.
   
    ![Ikonen för Skicka in distribution i preview-portalen](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. När installationen är klar i Azure portal klickar du på **alla tjänster**, typen *virtuella nätverk* i filterrutan och klicka sedan på virtuella nätverk för att se virtuella nätverk-bladet. I bladet, klickar du på *TestVNet*. I den *TestVNet* bladet, klickar du på **undernät** att se skapade undernät, som visas i följande bild:
    
     ![Skapa VNet i preview-portalen](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du ansluter:

- En virtuell dator (VM) till ett virtuellt nätverk genom att läsa artiklarna om hur du [skapar en Windows-baserad virtuell dator](../virtual-machines/virtual-machines-windows-hero-tutorial.md) eller [en Linux-baserad virtuell dator](../virtual-machines/linux/quick-create-portal.md). I stället för att skapa ett virtuellt nätverk och undernät i stegen i artikeln kan du ansluta en virtuell dator till ett befintligt virtuellt nätverk och undernät.
- Det virtuella nätverket till andra virtuella nätverk genom att läsa artikeln [Connect VNets](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) (Ansluta virtuella nätverk).
- Det virtuella nätverket till ett lokalt nätverk med hjälp av ett virtuellt privat nätverk (VPN) för plats till plats eller en ExpressRoute-krets. Mer information finns i artiklarna [Connect a VNet to an on-premises network using a site-to-site VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) (Ansluta ett virtuellt nätverk till ett lokalt nätverk med hjälp av VPN för plats till plats) och [Link a VNet to an ExpressRoute circuit](../expressroute/expressroute-howto-linkvnet-arm.md) (Länka ett virtuellt nätverk till en ExpressRoute-krets).
