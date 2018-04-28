---
title: Kontrollera trafik med Azure Network Watcher IP flöda Kontrollera - Azure CLI | Microsoft Docs
description: Den här artikeln beskrivs hur du kontrollerar om trafik till eller från en virtuell dator tillåts eller nekas med Azure CLI
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: b7122b632dca99eba6fae058beb644f945f67872
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2018
---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Kontrollera om trafik tillåts eller nekas till eller från en virtuell dator med IP-flöda Kontrollera en komponent i Azure Nätverksbevakaren

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI 1.0](network-watcher-check-ip-flow-verify-cli-nodejs.md)
> - [CLI 2.0](network-watcher-check-ip-flow-verify-cli.md)
> - [Azure REST-API](network-watcher-check-ip-flow-verify-rest.md)


IP-flöda verifiera är en funktion i Nätverksbevakaren som hjälper dig att kontrollera om tillåts trafik till eller från en virtuell dator. Det här scenariot är användbart för att hämta aktuella tillstånd om en virtuell dator kan kommunicera med en extern resurs eller backend. IP-flöde Kontrollera kan användas för att kontrollera om Nätverkssäkerhetsgrupp (NSG)-regler är korrekt konfigurerade och felsöka flöden som blockeras av NSG-regler. En annan orsak till att använda IP flödet Kontrollera är att kontrollera att trafik som du vill blockerade blockeras korrekt av NSG: N.

Den här artikeln använder våra nästa generations CLI för hantering av resursdistributionsmodell, Azure CLI 2.0, som är tillgänglig för Windows, Mac och Linux.

Om du vill utföra stegen i den här artikeln, måste du [installera Azure-kommandoradsgränssnittet för Mac, Linux och Windows (Azure CLI)](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="before-you-begin"></a>Innan du börjar

Det här scenariot förutsätter att du redan har följt stegen i [skapa en Nätverksbevakaren](network-watcher-create.md) att skapa en Nätverksbevakaren eller har en befintlig instans av Nätverksbevakaren. Det här scenariot förutsätter att det finns en resursgrupp med en giltig virtuell dator som ska användas.

## <a name="scenario"></a>Scenario

Det här scenariot använder IP-flöda Kontrollera för att verifiera om en virtuell dator kan du kontakta en känd Bing IP-adress. Om trafiken nekas returnerar säkerhetsregeln som nekar trafiken. Läs mer om IP-flöda Kontrollera [flöda Kontrollera översikt över IP](network-watcher-ip-flow-verify-overview.md)

## <a name="get-a-vm"></a>Hämta en virtuell dator

IP-flöde verifiera tester trafik till eller från en IP-adress på en virtuell dator till eller från en extern enhet. Ett Id för en virtuell dator krävs för cmdlet. Om du redan känner till ID: T för den virtuella datorn att använda, kan du hoppa över det här steget.

```azurecli
az vm show --resource-group MyResourceGroup5431 --name MyVM-Web
```

## <a name="get-the-nics"></a>Hämta Nätverkskorten

IP-adressen för ett nätverkskort på den virtuella datorn krävs. Hämta nätverkskort som är kopplad till en virtuell dator med det kommando som följer. Om du redan känner till IP-adressen som du vill testa på den virtuella datorn, kan du hoppa över det här steget.

```azurecli
az network nic show --resource-group MyResourceGroup5431 --name MyNic-Web
```

## <a name="run-ip-flow-verify"></a>Kontrollera kör IP-flöde

Kör den `az network watcher test-ip-flow` för att testa trafiken. I det här exemplet används första IP-adressen för det första nätverkskortet.

```azurecli
az network watcher test-ip-flow --resource-group resourceGroupName --direction directionInboundorOutbound --protocol protocolTCPorUDP --local ipAddressandPort --remote ipAddressandPort --vm vmNameorID --nic nicNameorID
```

> [!NOTE]
> IP-flöda Kontrollera kräver att den Virtuella datorresursen har allokerats för att köras.

## <a name="review-results"></a>Granska resultaten

När du har kört `az network watcher test-ip-flow` resultaten returneras, i följande exempel är resultatet har returnerats från föregående steg.

```azurecli
{
    "access": "Allow",
    "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

## <a name="next-steps"></a>Nästa steg

Om trafik blockeras och får inte vara, se [hantera Nätverkssäkerhetsgrupper](../virtual-network/manage-network-security-group.md) att spåra de grupp och säkerhet Nätverkssäkerhetsregler som har definierats.

Lär dig hur du granskningsinställningar NSG genom att besöka [granskning Nätverkssäkerhetsgrupp grupper (NSG) med Nätverksbevakaren](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png
