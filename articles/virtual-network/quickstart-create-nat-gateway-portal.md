---
title: 'Snabb start: skapa en NAT-gateway – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Den här snabb starten visar hur du skapar en NAT-gateway med hjälp av Azure Portal
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: c6da4b54dbc982c69e9d3004a5da8f63deffa3e9
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246015"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Snabb start: skapa en NAT-gateway med hjälp av Azure Portal

Den här snabb starten visar hur du använder Azure Virtual Network NAT-tjänsten. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

>[!NOTE] 
>Azure Virtual Network NAT är tillgängligt som en offentlig för hands version för tillfället och är tillgänglig i en begränsad uppsättning [regioner](./nat-overview.md#region-availability). Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan ha begränsad funktionalitet. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste du skapa resurs gruppen och det virtuella nätverket.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resurs-grupp-namn >**  | myResourceGroupNAT |
| **\<virtuella-nätverks namn >** | myVNet          |
| **\<region – namn >**          | USA, östra 2      |
| **\<IPv4-adress utrymme >**   | 192.168.0.0 \ 16          |
| **\<under nätets namn >**          | mySubnet        |
| **\<undernät – adress intervall >** | 192.168.0.0 \ 24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Skapa en virtuell dator för att använda NAT-gatewayen

Nu ska vi skapa en virtuell dator för att använda NAT-tjänsten. Den här virtuella datorn har en offentlig IP-adress som kan användas som en offentlig IP-adress på instans nivå för att få åtkomst till den virtuella datorn. NAT-tjänsten är en flödes riktning som är medveten om och kommer att ersätta standard målet för Internet i ditt undernät. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

1. Välj **skapa en resurs** > **Compute** > **Ubuntu Server 18,04 LTS**på den övre vänstra sidan i portalen, eller Sök efter **Ubuntu Server 18,04 LTS** i Marketplace-sökningen.

2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Prenumerations** > **resurs grupp**: Välj **myResourceGroupNAT**.
   - **Instans information** > **namn på virtuell dator**: Skriv **myVM**.
   - **Instans information** > **region** > Välj **USA, östra 2**.
   - **Administratörs konto** > **Autentiseringstyp**: Välj **lösen ord**.
   - **Administratörs konto** > ange **användar namn**, **lösen ord**och **Bekräfta lösen ords** information.
   - **Regler för inkommande port** > **offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Regler för inkommande port** > **Välj inkommande portar**: Välj **SSH (22)**
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.

3. På fliken **nätverk** ser du till att följande är markerat:
   - **Virtuellt nätverk**: **myVnet**
   - **Undernät**: mina **undernät**
   - **Offentlig IP** > Välj **Skapa ny**.  I fönstret **skapa offentlig IP-adress** skriver du **MyPublicIPVM** i fältet **namn** och väljer **standard** för **SKU: n**.  Klicka på **OK**
   - **NIC-nätverks säkerhets grupp**: Välj **Basic**.
   - **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: bekräfta **SSH** är markerat.

4. På fliken **hantering** , under **övervakning**, ställer du in **startdiagnostik** på **av**.

5. Välj **Granska + skapa**. 

6. Granska inställningarna och klicka på **skapa**.

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller både och. Vi ska lägga till en offentlig IP-resurs, ett offentligt IP-prefix och en NAT-gateway-resurs.

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gateway-resursen:
  - En offentlig IP-pool och ett offentligt IP-prefix som används för utgående flöden som översätts av NAT gateway-resursen.
  - Ändra tids gränsen för inaktivitet från standardvärdet 4 minuter till 10 minuter.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs** > **nätverk** > **offentlig IP-adress**eller söker efter **offentlig IP-adress** i Marketplace-sökningen.

2. I **skapa offentlig IP-adress**anger eller väljer du den här informationen:

    | Inställning | Värde |
    | ------- | ----- |
    | IP-version | Välj **IPv4**.
    | SKU | Välj **standard**.
    | Namn | Ange **myPublicIP**. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **myResourceGroupNAT**. |
    | plats. | Välj **USA, östra 2**.|

3. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs** > **nätverk** > **offentliga IP-prefix**, eller Sök efter **offentliga IP-prefix** i Marketplace-sökningen. 

2. I **skapa ett offentligt IP-prefix**skriver eller väljer du följande värden på fliken **grundläggande** :
   - **Prenumerations** > **resurs grupp**: Välj **myResourceGroupNAT**>
   - **Instans information** > **namn**: Skriv **myPublicIPprefix**.
   - **Instans information** > **region**: Välj **USA, östra 2**.
   - **Instans information** > **prefixlängd**: Välj **/31 (2 adresser)**

3. Lämna resten av standardinställningarna och välj **Granska + skapa**.

4. Granska inställningarna och välj sedan **Skapa**.
   

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gateway-resurs

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs** > **nätverk** > NAT- **Gateway**eller Sök efter NAT- **Gateway** i Marketplace-sökningen.

2. I **skapa Network Address Translation-Gateway (NAT)** skriver eller väljer du följande värden på fliken **grundläggande** :
   - **Prenumerations** > **resurs grupp**: Välj **myResourceGroupNAT**.
   - **Instans information** > **NAT-gatewayens namn**: Skriv **myNATgateway**.
   - **Instans information** > **region**: Välj **USA, östra 2**.
   - **Instans information** > **tids gräns för inaktivitet (minuter)** : typ **10**.
   - Välj fliken **offentlig IP-adress** eller Välj **Nästa: offentlig IP**.

3. På fliken **offentlig IP-adress** skriver eller väljer du följande värden:
   - **Offentliga IP-adresser**: Välj **myPublicIP**.
   - **Offentliga IP-prefix**: Välj **myPublicIPprefix**.
   - Välj fliken **undernät** eller Välj **Nästa: undernät**.

4. På fliken **undernät** skriver eller väljer du följande värden:
   - **Virtual Network**: välj **myResourceGroupNAT** > **myVnet**.
   - **Under näts namn**: Markera kryss rutan bredvid **undernät**.

5. Välj **Granska + skapa**.

6. Granska inställningarna och välj sedan **Skapa**.

## <a name="discover-the-ip-address-of-the-vm"></a>Identifiera IP-adressen för den virtuella datorn

1. Välj **resurs grupper**på vänster sida av portalen.
2. Välj **myResourceGroupNAT**.
3. Välj **myVM**.
4. I **Översikt**, kopierar du värdet för den **offentliga IP-adressen** och klistrar in i anteckningar så att du kan använda det för att få åtkomst till den virtuella datorn.

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den för att få åtkomst till den virtuella datorn.

## <a name="sign-in-to-vm"></a>Logga in på virtuell dator

Öppna en [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Du är nu redo att använda NAT-tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, NAT-gatewayen och alla relaterade resurser när de inte längre behövs. Välj den resurs grupp **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en NAT-gateway och en virtuell dator för att använda den. 

Granska mått i Azure Monitor för att se hur NAT-tjänsten fungerar. Diagnostisera problem som resurs utbelastning av tillgängliga SNAT-portar.  Resurs utbelastningen på SNAT-portar åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix eller både och.


- Lär dig mer om [Azure Virtual Network NAT](./nat-overview.md)
- Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure Portal](./quickstart-create-nat-gateway-portal.md).
- [Ge feedback om den offentliga för hands versionen](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

