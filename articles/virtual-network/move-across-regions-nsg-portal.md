---
title: Flytta Azure Network Security Group (NSG) till en annan Azure-region med Azure-portalen
description: Använd Azure Resource Manager-mallen för att flytta Azure-nätverkssäkerhetsgrupp från en Azure-region till en annan med hjälp av Azure-portalen.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/31/2019
ms.author: allensu
ms.openlocfilehash: dce267178c3caf813ccdcac4bba86ccfde3f3421
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75647194"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-the-azure-portal"></a>Flytta Azure Network Security Group (NSG) till en annan region med Hjälp av Azure-portalen

Det finns olika scenarier där du vill flytta dina befintliga NSG:er från en region till en annan. Du kanske till exempel vill skapa en NSG med samma konfigurations- och säkerhetsregler för testning. Du kanske också vill flytta en NSG till en annan region som en del av katastrofåterställningsplanering.

Azure-säkerhetsgrupper kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera de befintliga konfigurations- och säkerhetsreglerna för en NSG.  Du kan sedan iscensätta resursen i en annan region genom att exportera NSG till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Krav

- Kontrollera att säkerhetsgruppen för Azure-nätverk finns i Azure-regionen som du vill flytta från.

- Azure-nätverkssäkerhetsgrupper kan inte flyttas mellan regioner.  Du måste associera den nya NSG till resurser i målregionen.

- Om du vill exportera en NSG-konfiguration och distribuera en mall för att skapa en NSG i en annan region behöver du rollen Nätverksbidragsgivare eller högre.

- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, offentliga IP-adresser och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa NSG:er i målregionen som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att din prenumeration har tillräckligt med resurser för att stödja tillägg av NSG:er för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder nätverkssäkerhetsgruppen för konfigurations- och säkerhetsregelflytten med hjälp av en Resource Manager-mall och flyttar NSG-konfigurations- och säkerhetsreglerna till målregionen med hjälp av portalen.


### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportera mallen och distribuera från portalen

1. Logga in på [Azure Portal](https://portal.azure.com) > **Resource Groups**.
2. Leta upp resursgruppen som innehåller käll-NSG och klicka på den.
3. Välj**mallen** **Exportera inställningar** > > inställningar .
4. Välj **Distribuera** i **mallbladet Exportera.**
5. Klicka på > **MALLRedigeringsparametrar** för att öppna **filen parameters.json** i onlineredigeraren. **TEMPLATE**
6. Om du vill redigera parametern för NSG-namnet ändrar du **egenskapen värde** under **parametrar:**

    ```json
            {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
               "value": "<target-nsg-name>"
                }
               }
            }
    ```

7. Ändra käll-NSG-värdet i redigeraren till ett namn som du väljer för målet NSG. Se till att du bifogar namnet inom citationstecken.

8.  Klicka på **Spara** i redigeraren.

9.  Klicka på > **MALLRedigeringsmall för** att öppna **filen template.json** i onlineredigeraren. **TEMPLATE**

10. Om du vill redigera målområdet där NSG-konfigurationen och säkerhetsreglerna ska flyttas ändrar du platsegenskapen under **resurser** i onlineredigeraren: **location**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
             }
            }
           ]

    ```

11. Information om hur du hämtar regionplatskoder finns i [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är regionnamnet utan blanksteg, **Central US** = **centralus**.

12. Du kan också ändra andra parametrar i mallen om du vill och är valfria beroende på dina behov:

    * **Säkerhetsregler** - Du kan redigera vilka regler som distribueras till målet NSG genom att lägga till eller ta bort regler i **avsnittet securityRules** i **filen template.json:**

        ```json
           "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "TCP",
                            "sourcePortRange": "*",
                            "destinationPortRange": "3389",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 300,
                            "direction": "Inbound",
                            "sourcePortRanges": [],
                            "destinationPortRanges": [],
                            "sourceAddressPrefixes": [],
                            "destinationAddressPrefixes": []
                             }
                    },
                ]
            }
        ```

      Om du vill slutföra tillägget eller borttagningen av reglerna i mål-NSG måste du också redigera de anpassade regeltyperna i slutet av **filen template.json** i formatet nedan:

      ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
      ```

13. Klicka på **Spara** i onlineredigeraren.

14. Klicka på > **BASICS-prenumerationen** om du vill välja den prenumeration där mål-NSG ska distribueras. **BASICS**

15. Klicka på**RESURSGRUPP** FÖR **GRUNDLÄGGANDE** > om du vill välja den resursgrupp där mål-NSG ska distribueras.  Du kan klicka på **Skapa ny** om du vill skapa en ny resursgrupp för mål-NSG.  Kontrollera att namnet inte är samma som källresursgruppen för den befintliga NSG.Ensure the name isn't the same as the source resource group of the existing NSG.

16. Kontrollera **att BASICS-platsen** > **Location** är inställd på målplatsen där du vill att NSG ska distribueras.

17. Kontrollera under **INSTÄLLNINGAR** att namnet matchar namnet som du angav i parameterredigeraren ovan.

18. Markera rutan under **VILLKOR**.

19. Klicka på knappen **Inköp** om du vill distribuera säkerhetsgruppen för målnätverket.

## <a name="discard"></a>Ignorera

Om du vill ignorera målet NSG tar du bort resursgruppen som innehåller målet NSG.  Om du vill göra det markerar du resursgruppen från instrumentpanelen i portalen och väljer **Ta bort** högst upp på översiktssidan.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av NSG tar du bort käll-NSG- eller resursgruppen. Det gör du genom att välja nätverkssäkerhetsgruppen eller resursgruppen från instrumentpanelen i portalen och välja **Ta bort** högst upp på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure-nätverkssäkerhetsgrupp från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
