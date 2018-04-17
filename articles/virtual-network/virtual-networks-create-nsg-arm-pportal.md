---
title: Skapa nätverk säkerhetsgrupper - Azure-portalen | Microsoft Docs
description: Lär dig hur du skapar och distribuerar nätverkssäkerhetsgrupper med Azure-portalen.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 5bc8fc2e-1e81-40e2-8231-0484cd5605cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: dd05df542327f9d8dae924b7097d247980a0558b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="create-network-security-groups-using-the-azure-portal"></a>Skapa nätverk säkerhetsgrupper i Azure Portal

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Den här artikeln beskriver Resource Manager-distributionsmodellen. Du kan också [skapa NSG: er i den klassiska distributionsmodellen](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Exempel PowerShell-kommandona nedan förväntar sig en enkel miljö som redan har skapats baserat på scenariot ovan. Om du vill köra kommandon som de visas i det här dokumentet, först skapa testmiljön genom att distribuera [mallen](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), klickar du på **till Azure**, ersätta Standardparametervärden om det behövs och följ instruktionerna i portalen. Stegen nedan används **RG NSG** som namnet på resursgruppen som mallen har distribuerats till.

## <a name="create-the-nsg-frontend-nsg"></a>Skapa NSG-klientdel NSG: N
Att skapa den **NSG-klientdel** NSG enligt scenariot ovan, Följ stegen nedan.

1. Navigera till http://portal.azure.com från en webbläsare och logga in med ditt Azure-konto vid behov.
2. Klicka på **Bläddra >** > **Nätverkssäkerhetsgrupper**.
   
    ![Azure portal – NSG: er](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. I den **Nätverkssäkerhetsgrupper** bladet, klickar du på **Lägg till**.
   
    ![Azure portal – NSG: er](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. I den **skapa nätverkssäkerhetsgruppen** bladet, skapa en NSG med namnet *NSG-klientdel* i den *RG NSG* resursgrupp och klicka sedan på **skapa**.
   
    ![Azure portal – NSG: er](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Skapa regler i en befintlig nätverkssäkerhetsgrupp (NSG)
Följ stegen nedan för att skapa regler i en befintlig NSG från Azure-portalen.

1. Klicka på **Bläddra >** > **Nätverkssäkerhetsgrupper**.
2. I listan över NSG: er, klickar du på **NSG-klientdel** > **inkommande säkerhetsregler**
   
    ![Azure portal – NSG-klientdel](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. I listan över **inkommande säkerhetsregler**, klickar du på **Lägg till**.
   
    ![Azure portal – Lägg till regel](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. I den **Lägg till ingående säkerhetsregel** bladet, skapa en regel med namnet *regel för web* med prioriteten för *200* åtkomst via *TCP* till port *80* till någon virtuell dator från en datakälla och klicka sedan på **OK**. Observera att de flesta av de här inställningarna är standardvärden redan.
   
    ![Azure portal – regelinställningar](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Efter några sekunder visas den nya regeln i NSG: N.
   
    ![Azure portal – ny regel](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Upprepa steg 6 för att skapa en regel med namnet *rdp-regel* med prioritet *250* åtkomst via *TCP* till port *3389* till någon virtuell dator från en källa.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associera nätverkssäkerhetgruppen (NSG) med FrontEnd-undernätet
1. Klicka på **Bläddra >** > **resursgrupper** > **RG NSG**.
2. I den **RG NSG** bladet, klickar du på **...**   >  **TestVNet**.
   
    ![Azure portal – TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. I den **inställningar** bladet, klickar du på **undernät** > **klientdel** > **nätverkssäkerhetsgruppen** > **NSG-klientdel**.
   
    ![Azure portal – undernätsinställningar](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. I den **klientdel** bladet, klickar du på **spara**.
   
    ![Azure portal – undernätsinställningar](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Skapa NSG BackEnd NSG: N
Att skapa den **NSG BackEnd** NSG och koppla den till den **BackEnd** undernät, Följ stegen nedan.

1. Upprepa stegen i [skapa NSG-klientdel NSG](#Create-the-NSG-FrontEnd-NSG) att skapa en NSG som heter *NSG-serverdelen*
2. Upprepa stegen i [skapa regler i en befintlig NSG](#Create-rules-in-an-existing-NSG) att skapa den **inkommande** regler i tabellen nedan.
   
   | Regel för inkommande trafik | Regel för utgående trafik |
   | --- | --- |
   | ![Azure portal - regel för inkommande trafik](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Azure portal - regel för utgående trafik](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Upprepa stegen i [koppla NSG till undernätet FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) att associera den **NSG Backend** NSG till den **BackEnd** undernät.

## <a name="next-steps"></a>Nästa steg
* Lär dig hur du [hantera befintliga NSG: er](manage-network-security-group.md)
* [Aktivera loggning](virtual-network-nsg-manage-log.md) för NSG: er.

