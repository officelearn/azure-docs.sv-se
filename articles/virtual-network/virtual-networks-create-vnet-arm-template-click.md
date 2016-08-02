<properties
   pageTitle="Skapa ett virtuellt nätverk med en ARM-mall | Microsoft Azure"
   description="Lär dig hur du skapar ett virtuellt nätverk med en ARM-mall | Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Skapa ett virtuellt nätverk med en ARM-mall

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnet-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)] Det här dokumentet går igenom hur man skapar ett VNet med hjälp av Resource Manager-distributionsmodellen. Du kan också [skapa ett virtuellt nätverk i den klassiska distributionsmodellen](virtual-networks-create-vnet-classic-pportal.md).

Du kommer lära dig hur man hämtar och ändrar en befintlig ARM-mall från GitHub och distribuerar mallen från GitHub, PowerShell och Azure CLI.

Om du bara distribuerar ARM-mallen direkt från GitHub utan ändringar, kan du hoppa till [distribuera en mall från github](#deploy-the-arm-template-by-using-click-to-deploy).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-include](../../includes/virtual-networks-create-vnet-arm-template-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-ps-include](../../includes/virtual-networks-create-vnet-arm-template-ps-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-cli-include](../../includes/virtual-networks-create-vnet-arm-template-cli-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-arm-template-click-include](../../includes/virtual-networks-create-vnet-arm-template-click-include.md)]


<!--HONumber=Jun16_HO2-->


