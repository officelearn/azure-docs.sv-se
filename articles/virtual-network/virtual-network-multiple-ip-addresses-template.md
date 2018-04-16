---
title: "Flera IP-adresser för virtuella datorer i Azure - mall | Microsoft Docs"
description: "Lär dig hur du tilldelar flera IP-adresser till en virtuell dator med en Azure Resource Manager-mall."
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/08/2016
ms.author: jdial
ms.openlocfilehash: d4b189fb23dda1167c4f6b17b618c718d32dd98f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-an-azure-resource-manager-template"></a>Tilldela flera IP-adresser till virtuella datorer med en Azure Resource Manager-mall

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Den här artikeln förklaras hur du skapar en virtuell dator (VM) via Azure Resource Manager-distributionsmodellen med hjälp av en Resource Manager-mall. Flera offentliga och privata IP-adresser kan inte tilldelas till samma NIC när du distribuerar en virtuell dator via den klassiska distributionsmodellen. Om du vill veta mer om Azure distributionsmodeller kan läsa den [förstår distributionsmodellerna](../resource-manager-deployment-model.md) artikel.

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="template-description"></a>Beskrivning av mall

Distribuera en mall kan du snabbt och konsekvent sätt skapa Azure-resurser med olika konfigurationsvärden. Läs den [genomgång av Resource Manager-mall](../azure-resource-manager/resource-manager-template-walkthrough.md?toc=%2fazure%2fvirtual-network%2ftoc.json) artikel om du inte är bekant med Azure Resource Manager-mallar. Den [distribuera en virtuell dator med flera IP-adresser](https://azure.microsoft.com/resources/templates/101-vm-multiple-ipconfig) mall används i den här artikeln.

<a name="resources"></a>Distribuera mallen skapas i följande resurser:

|Resurs|Namn|Beskrivning|
|---|---|---|
|Nätverksgränssnitt|*myNic1*|De tre IP-konfigurationer som beskrivs i avsnittet scenario i den här artikeln skapas och tilldelas till det här nätverkskortet.|
|Offentliga IP-adressresurs|2 skapas: *myPublicIP* och *myPublicIP2*|Dessa resurser tilldelas statiska offentliga IP-adresser och har tilldelats den *IPConfig-1* och *IPConfig-2* IP-konfigurationer som beskrivs i scenariot.|
|Virtuell dator|*myVM1*|Standard DS3 VM.|
|Virtuellt nätverk|*myVNet1*|Ett virtuellt nätverk med ett undernät med namnet *mySubnet*.|
|Lagringskonto|Unik för distributionen|Ett lagringskonto.|

<a name="parameters"></a>När du distribuerar mallen, måste du ange värden för följande parametrar:

|Namn|Beskrivning|
|---|---|
|adminUsername|Administratörsanvändarnamnet. Användarnamnet måste vara kompatibel med [användarnamn för Azure krav](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json).|
|adminPassword|Administratörslösenordet lösenordet måste överensstämma med [Azure lösenordskrav](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
|dnsLabelPrefix|DNS-namnet för PublicIPAddressName1. DNS-namnet ska matcha någon av de offentliga IP-adresser som tilldelats den virtuella datorn. Namnet måste vara unikt i Azure-region (plats) som du skapar den virtuella datorn i.|
|dnsLabelPrefix1|DNS-namnet för PublicIPAddressName2. DNS-namnet ska matcha någon av de offentliga IP-adresser som tilldelats den virtuella datorn. Namnet måste vara unikt i Azure-region (plats) som du skapar den virtuella datorn i.|
|OSVersion|Windows-/ Linux-version för den virtuella datorn. Operativsystemet är en fullständigt uppdaterad bild av den valda angivna Windows-/ Linux-versionen.|
|imagePublisher|Windows-/ Linux avbildningens utgivare för den valda virtuella datorn.|
|imageOffer|Windows-/ Linux-bild för den valda virtuella datorn.|

Var och en av de resurser som distribueras av mallen har konfigurerats med flera standardinställningar. Du kan visa dessa inställningar via någon av följande metoder:

- **Visa mallen på GitHub:** om du är bekant med mallar kan du visa inställningarna i den [mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json).
- **Visa inställningarna när du har distribuerat:** om du inte är bekant med mallar kan du distribuera mallen med hjälp av stegen i följande avsnitt och visa inställningarna efter distributionen.

Du kan använda Azure portal, PowerShell eller Azure-kommandoradsgränssnittet (CLI) för att distribuera mallen. Alla metoder ger samma resultat. Utför stegen i följande avsnitt för att distribuera mallen:

## <a name="deploy-using-the-azure-portal"></a>Distribuera med Azure-portalen

Om du vill distribuera mallen med hjälp av Azure portal, gör du följande:

1. Ändra mallen, om så önskas. Mallen distribuerar resurser och inställningar som anges i den [resurser](#resources) i den här artikeln. Mer information om mallar och hur du redigerar dem i [redigera Azure Resource Manager-mallar](../azure-resource-manager/resource-group-authoring-templates.md?toc=%2fazure%2fvirtual-network%2ftoc.json)artikel.
2. Distribuera mallen med en av följande metoder:
    - **Välj mallen i portal:** slutföra stegen i den [distribuera resurser från anpassade mall](../azure-resource-manager/resource-group-template-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-resources-from-custom-template) artikel. Välj den befintliga mallen med namnet *101-vm-flera-ipconfig*.
    - **Direkt:** Klicka på följande om du vill öppna mallen direkt i portalen:<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-multiple-ipconfig%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Oavsett vilken metod du väljer, måste du ange värden för den [parametrar](#parameters) som angavs tidigare i den här artikeln. När den virtuella datorn har distribuerats kan ansluta till den virtuella datorn och Lägg till de privata IP-adresserna för operativsystemet som du har distribuerat genom att slutföra stegen i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna till operativsystemet.

## <a name="deploy-using-powershell"></a>Distribuera med hjälp av PowerShell

Utför följande steg för att distribuera mallen med hjälp av PowerShell:

1. Distribuera mallen genom att slutföra stegen i den [distribuera en mall med PowerShell](../azure-resource-manager/resource-group-template-deploy-cli.md) artikel. Den här artikeln innehåller flera alternativ för att distribuera en mall. Om du väljer att distribuera med den `-TemplateUri parameter`, URI: N för den här mallen är *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Om du väljer att distribuera med den `-TemplateFile` parameter, kopiera innehållet i den [mallfilen](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) från GitHub i en ny fil på din dator. Ändra mallen innehållet, om så önskas. Mallen distribuerar resurser och inställningar som anges i den [resurser](#resources) i den här artikeln. Mer information om mallar och hur du redigerar dem i [redigera Azure Resource Manager-mallar ](../azure-resource-manager/resource-group-authoring-templates.md)artikel.

    Oavsett vilket alternativ du väljer att distribuera mallen med, måste du ange värden för de parametervärden som anges i den [parametrar](#parameters) i den här artikeln. Om du väljer att ange parametrar med en fil med parametrar, kopiera innehållet i den [parameterfilen](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) från GitHub i en ny fil på din dator. Ändra värdena i filen. Använd den fil du skapade som värde för den `-TemplateParameterFile` parameter.

    För att fastställa de giltiga värdena för OSVersion, ImagePublisher och imageOffer parametrar, slutför du stegen i den [navigera och välj Windows VM-avbildningar artikel](../virtual-machines/windows/cli-ps-findimage.md) artikel.

    >[!TIP]
    >Om du inte vet om det finns en dnslabelprefix anger den `Test-AzureRmDnsAvailability -DomainNameLabel <name-you-want-to-use> -Location <location>` kommando för att ta reda på. Om det är tillgängligt kommandot returnerar `True`.

2. När den virtuella datorn har distribuerats kan ansluta till den virtuella datorn och Lägg till de privata IP-adresserna för operativsystemet som du har distribuerat genom att slutföra stegen i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna till operativsystemet.

## <a name="deploy-using-the-azure-cli"></a>Distribuera med hjälp av Azure CLI

Om du vill distribuera mallen med hjälp av Azure CLI 1.0, gör du följande:

1. Distribuera mallen genom att slutföra stegen i den [distribuera en mall med Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md) artikel. Den här artikeln innehåller flera alternativ för att distribuera mallen. Om du väljer att distribuera med den `--template-uri` (-f), URI: N för den här mallen är *https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json*. Om du väljer att distribuera med den `--template-file` (-f) parameter, kopiera innehållet i den [mallfilen](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.json) från GitHub i en ny fil på din dator. Ändra mallen innehållet, om så önskas. Mallen distribuerar resurser och inställningar som anges i den [resurser](#resources) i den här artikeln. Mer information om mallar och hur du redigerar dem i [redigera Azure Resource Manager-mallar ](../azure-resource-manager/resource-group-authoring-templates.md)artikel.

    Oavsett vilket alternativ du väljer att distribuera mallen med, måste du ange värden för de parametervärden som anges i den [parametrar](#parameters) i den här artikeln. Om du väljer att ange parametrar med en fil med parametrar, kopiera innehållet i den [parameterfilen](https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-multiple-ipconfig/azuredeploy.parameters.json) från GitHub i en ny fil på din dator. Ändra värdena i filen. Använd den fil du skapade som värde för den `--parameters-file` (-e) parameter.

    För att fastställa de giltiga värdena för OSVersion, ImagePublisher och imageOffer parametrar, slutför du stegen i den [navigera och välj Windows VM-avbildningar artikel](../virtual-machines/windows/cli-ps-findimage.md) artikel.

2. När den virtuella datorn har distribuerats kan ansluta till den virtuella datorn och Lägg till de privata IP-adresserna för operativsystemet som du har distribuerat genom att slutföra stegen i den [lägga till IP-adresser till ett VM-operativsystem](#os-config) i den här artikeln. Lägg inte till de offentliga IP-adresserna till operativsystemet.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
