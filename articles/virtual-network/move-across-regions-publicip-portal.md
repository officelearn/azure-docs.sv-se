---
title: Flytta Azure Public IP till en annan Azure-region med Azure-portalen
description: Använd Azure Resource Manager-mallen för att flytta Azure Public IP från en Azure-region till en annan med hjälp av Azure-portalen.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 6d10265e8383b68ebe13c95d8b2a9632668e85da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75641409"
---
# <a name="move-azure-public-ip-to-another-region-using-the-azure-portal"></a>Flytta Azure Public IP till en annan region med Azure-portalen

Det finns olika scenarier där du vill flytta dina befintliga Offentliga Azure-IPs från en region till en annan. Du kanske till exempel vill skapa en offentlig IP med samma konfiguration och sku för testning. Du kanske också vill flytta en offentlig IP till en annan region som en del av katastrofåterställningsplanering.

Offentliga Azure-IPs är regionspecifika och kan inte flyttas från en region till en annan. Du kan dock använda en Azure Resource Manager-mall för att exportera den befintliga konfigurationen av en offentlig IP.  Du kan sedan arrangera resursen i en annan region genom att exportera den offentliga IP-adressen till en mall, ändra parametrarna för att matcha målregionen och sedan distribuera mallen till den nya regionen.  Mer information om Resurshanteraren och mallar finns i [Snabbstart: Skapa och distribuera Azure Resource Manager-mallar med hjälp av Azure-portalen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).


## <a name="prerequisites"></a>Krav

- Kontrollera att Azure Public IP finns i Azure-regionen som du vill flytta från.

- Offentliga Azure-IPs kan inte flyttas mellan regioner.  Du måste associera den nya offentliga ip till resurser i målregionen.

- Om du vill exportera en offentlig IP-konfiguration och distribuera en mall för att skapa en offentlig IP-adress i en annan region behöver du rollen Nätverksbidragsgivare eller högre.

- Identifiera källnätverkslayouten och alla resurser som du använder för tillfället. Den här layouten innehåller men är inte begränsad till belastningsutjämnare, nätverkssäkerhetsgrupper (NSG) och virtuella nätverk.

- Kontrollera att din Azure-prenumeration gör att du kan skapa offentliga IP-adresser i målområdet som används. Kontakta supporten och aktivera den kvot som krävs.

- Kontrollera att prenumerationen har tillräckligt med resurser för att stödja tillägg av offentliga IPs för den här processen.  Läs mer i [Azure-prenumeration och tjänstbegränsningar, kvoter och krav](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits).


## <a name="prepare-and-move"></a>Förbereda och flytta
Följande steg visar hur du förbereder den offentliga IP-adressen för konfigurationsflytten med hjälp av en Resource Manager-mall och flyttar den offentliga IP-konfigurationen till målregionen med hjälp av Azure-portalen.

### <a name="export-the-template-and-deploy-from-a-script"></a>Exportera mallen och distribuera från ett skript

1. Logga in på [Azure Portal](https://portal.azure.com) > **Resource Groups**.
2. Leta upp resursgruppen som innehåller käll-IP-adressen och klicka på den.
3. Välj**mallen** **Exportera inställningar** > > inställningar .
4. Välj **Distribuera** i **mallbladet Exportera.**
5. Klicka på > **MALLRedigeringsparametrar** för att öppna **filen parameters.json** i onlineredigeraren. **TEMPLATE**
8. Om du vill redigera parametern för det offentliga IP-namnet ändrar du egenskapen under > **parametervärdet** från källvärdet för offentliga IP-namn till namnet på din offentliga IP-adress, och ser till att namnet är inom citationstecken: **parameters**

    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "publicIPAddresses_myVM1pubIP_name": {
            "value": "<target-publicip-name>"
              }
             }
            }

    ```
8.  Klicka på **Spara** i redigeraren.

9.  Klicka på > **MALLRedigeringsmall för** att öppna **filen template.json** i onlineredigeraren. **TEMPLATE**

10. Om du vill redigera målområdet där den **location** offentliga IP-adressen ska flyttas ändrar du platsegenskapen under **resurser:**

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]
    ```

11. Information om hur du hämtar regionplatskoder finns i [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Koden för en region är regionnamnet utan blanksteg, **Central US** = **centralus**.

12. Du kan också ändra andra parametrar i mallen om du vill och är valfria beroende på dina behov:

    * **Sku** - Du kan ändra sku av den offentliga IP i konfigurationen från standard till grundläggande eller grundläggande till standard genom att ändra **sku** > **namn** egenskapen i **filen template.json:**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
        ```

        Mer information om skillnaderna mellan grundläggande och standard sku offentliga IPS, se [Skapa, ändra eller ta bort en offentlig IP-adress:](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)

    * **Offentlig IP-allokeringsmetod** och **inaktiv timeout** - Du kan ändra båda dessa alternativ i mallen genom att ändra egenskapen **publicIPAllocationMethod** från **dynamisk** till **statisk** eller **statisk** till **dynamisk**. Tidsgränsen för inaktiv kan ändras genom att ändra egenskapen **idleTimeoutInMinutes** till önskad mängd.  Standard är **4:**

        ```json
          "resources": [
         {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []

        ```

        Mer information om allokeringsmetoderna och tidsgränsen för inaktiva tidsutgång finns i [Skapa, ändra eller ta bort en offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).


13. Klicka på **Spara** i onlineredigeraren.

14. Klicka på > **BASICS-prenumerationen** om du vill välja den prenumeration där den offentliga mål-IP-adressen ska distribueras. **BASICS**

15. Klicka på**RESURSGRUPP** FÖR **GRUNDLÄGGANDE** > om du vill välja den resursgrupp där den offentliga mål-IP-adressen ska distribueras.  Du kan klicka på **Skapa ny** om du vill skapa en ny resursgrupp för den offentliga IP-adressen för målet.  Kontrollera att namnet inte är samma som källresursgruppen för den befintliga käll-IP-adressen.

16. Kontrollera **att BASICS-platsen** > **Location** är inställd på målplatsen där du vill att den offentliga IP-adressen ska distribueras.

17. Kontrollera under **INSTÄLLNINGAR** att namnet matchar namnet som du angav i parameterredigeraren ovan.

18. Markera rutan under **VILLKOR**.

19. Klicka på **knappen Inköp** om du vill distribuera den offentliga mål-IP-adressen.

## <a name="discard"></a>Ignorera

Om du vill ignorera den offentliga mål-IP-adressen tar du bort resursgruppen som innehåller den offentliga ip-adressen för målet.  Om du vill göra det markerar du resursgruppen från instrumentpanelen i portalen och väljer **Ta bort** högst upp på översiktssidan.

## <a name="clean-up"></a>Rensa

Om du vill genomföra ändringarna och slutföra flytten av den offentliga IP-adressen tar du bort den offentliga käll-IP-adressen eller resursgruppen. Om du vill göra det väljer du den offentliga IP- eller resursgruppen från instrumentpanelen i portalen och väljer **Ta bort** högst upp på varje sida.

## <a name="next-steps"></a>Nästa steg

I den här självstudien flyttade du en Azure Public IP från en region till en annan och rensade källresurserna.  Mer information om hur du flyttar resurser mellan regioner och haveriberedskap i Azure finns i:


- [Flytta resurser till en ny resursgrupp eller prenumeration](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Flytta virtuella Azure-datorer till en annan region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
