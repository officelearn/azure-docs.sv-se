---
title: 'Snabbstart: Skapa en NAT-gateway - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: Den här snabbstarten visar hur du skapar en NAT-gateway med Azure-portalen
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
ms.openlocfilehash: 1ff13d8ef0ca4c6cf499c3245d3ef14370283075
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80066387"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>Snabbstart: Skapa en NAT-gateway med Azure-portalen

Den här snabbstarten visar hur du använder Azure Virtual Network NAT-tjänst. Du skapar en NAT-gateway för att tillhandahålla utgående anslutning för en virtuell dator i Azure. 

Om du vill kan du göra dessa steg med [Azure CLI,](quickstart-create-nat-gateway-cli.md) [Azure PowerShell](quickstart-create-nat-gateway-powershell.md)eller distribuera en [ARM-mall](quickstart-create-nat-gateway-powershell.md) i stället för portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste vi skapa resursgruppen och det virtuella nätverket.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroupNAT |
| **\<>virtuellt nätverksnamn** | myVNet          |
| **\<regionnamn>**          | USA, östra 2      |
| **\<IPv4-adress-utrymme>**   | 192.168.0.0\16          |
| **\<>i>**          | mySubnet        |
| **\<>för>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>Skapa en virtuell dator för att använda NAT-gatewayen

Vi ska nu skapa en virtuell dator för att använda NAT-tjänsten. Den här virtuella datorn har en offentlig IP att använda som en offentlig IP på instansnivå så att du kan komma åt den virtuella datorn. NAT-tjänsten är flödesriktningsmedveten och ersätter standardmålet för Internet i undernätet. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

1. På portalens övre vänstra sida väljer du **Skapa en resurs** > **Compute** > **Ubuntu Server 18.04 LTS**eller sök efter **Ubuntu Server 18.04 LTS** i Marketplace-sökningen.

2. I **Skapa en virtuell dator** skriver eller väljer du följande värden på fliken **Grundläggande**:
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**.
   - **Instansinformation** > **Virtuell datornamn:** Skriv **myVM**.
   - **Instance Details** > Region för**instansinformation** > väljer **östra USA 2**.
   - **Autentiseringstyp för administratörskonto:** > **Authentication type**Välj **lösenord**.
   - **Administratörskontot** > ange **information om användarnamn,** **lösenord**och **bekräfta lösenord.**
   - **Regler för inkommande portar** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Regler för inkommande port** > **Välj inkommande portar**: Välj **SSH (22)**
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.

3. Kontrollera att följande är markerade på fliken **Nätverk:**
   - **Virtuellt nätverk:** **myVnet**
   - **Undernät**: **mySubnet**
   - **Offentliga IP->** Välj **Skapa ny**.  Skriv **myPublicIPVM** i fältet **Namn** i fönstret **Skapa offentlig IP-adress** och välj **Standard** för **SKU**.  Klicka på **OK**.
   - **Säkerhetsgrupp för nätverksnätverk:** Välj **Grundläggande**.
   - **Inkommande inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: Bekräfta att **SSH** är markerat.

4. Ange **Startdiagnostik** på **Av**under **Övervakning**på fliken **Hantering** .

5. Välj **Granska + skapa**. 

6. Granska inställningarna och klicka på **Skapa**.

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller båda. Vi lägger till en offentlig IP-resurs, offentligt IP-prefix och en NAT-gatewayresurs.

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gatewayresursen:
  - En offentlig IP-pool och offentligt IP-prefix som ska användas för utgående flöden som översätts av NAT-gatewayresursen.
  - Ändra tidsgränsen för inaktiv tid från standardvärdet 4 minuter till 10 minuter.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. På portalens övre vänstra sida väljer du Skapa en offentlig**IP-adress**för > **nätverknätverk** > eller sök efter **offentlig IP-adress** i Marketplace-sökningen. **Create a resource**

2. Ange eller välj den här informationen i **Skapa offentlig IP-adress:**

    | Inställning | Värde |
    | ------- | ----- |
    | IP-version | Välj **IPv4**.
    | SKU | Välj **Standard**.
    | Namn | Ange **myPublicIP**. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **myResourceGroupNAT**. |
    | Location | Välj **USA, östra 2**.|

3. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

1. På portalens övre vänstra sida väljer du Skapa ett**offentligt IP-resursprefix**för **nätverknätverk** > **Networking** > eller sök efter **offentligt IP-prefix** i Marketplace-sökningen. 

2. Skriv eller välj följande värden på fliken **Grunderna** i **Skapa ett offentligt IP-prefix:**
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**>
   - **Förekomstinformation** > **Namn:** Skriv **myPublicIPprefix**.
   - **Instansinformation** > **Region:** Välj **Östra USA 2**.
   - **Instansinformation** > **Prefixstorlek**: Välj **/31 (2 adresser)**

3. Lämna resten standardvärdena och välj **Granska + skapa**.

4. Granska inställningarna och välj sedan **Skapa**.
   

### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

1. På portalens övre vänstra sida väljer du Skapa en**NAT-gateway**för > **nätverknätverk** > eller sök efter **NAT-gateway** i Marketplace-sökningen. **Create a resource**

2. Skriv eller välj följande värden på fliken **Grunderna** i **NÃ¤tt adressöversättning (NAT)**:
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**.
   - **Instansinformation** > **NAT gateway namn:** Skriv **myNATgateway**.
   - **Instansinformation** > **Region:** Välj **Östra USA 2**.
   - **Instansinformation** > **Inaktiv timeout (minuter):** Typ **10**.
   - Välj fliken **Offentlig IP** eller välj **Nästa: Offentlig IP**.

3. Skriv eller välj följande värden på fliken **Offentlig IP:**
   - **Offentliga IP-adresser:** Välj **myPublicIP**.
   - **Offentliga IP-prefix:** Välj **myPublicIPprefix**.
   - Välj fliken **Undernät** eller välj **Nästa: Undernät**.

4. Skriv eller välj följande värden på fliken **Undernät:**
   - **Virtuellt nätverk:** Välj **myResourceGroupNAT** > **myVnet**.
   - **Undernätsnamn**: Markera rutan bredvid **mySubnet**.

5. Välj **Granska + skapa**.

6. Granska inställningarna och välj sedan **Skapa**.

## <a name="discover-the-ip-address-of-the-vm"></a>Upptäck DEN virtuella datorns IP-adress

1. Välj **Resursgrupper**till vänster i portalen .
2. Välj **myResourceGroupNAT**.
3. Välj **myVM**.
4. Kopiera värdet för den **offentliga IP-adressen** i **Översikt**och klistra in det i anteckningar så att du kan använda det för att komma åt den virtuella datorn.

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den för att komma åt den virtuella datorn.

## <a name="sign-in-to-vm"></a>Logga in på virtuell dator

Öppna ett [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Du är nu redo att använda NAT-tjänsten.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, NAT-gatewayen och alla relaterade resurser. Markera resursgruppen **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en NAT-gateway och en virtuell dator för att använda den. 

Granska mått i Azure Monitor för att se nat-tjänsten fungera. Diagnostisera problem som resursutmattning av tillgängliga SNAT-portar.  Resursutmattning av SNAT-portar åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser eller båda.


- Lär dig mer om [AZURE Virtual Network NAT](./nat-overview.md)
- Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure-portalen](./quickstart-create-nat-gateway-portal.md).
> [!div class="nextstepaction"]

