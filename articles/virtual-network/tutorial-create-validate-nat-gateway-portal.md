---
title: 'Självstudie: skapa och testa en NAT-gateway – Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: Den här självstudien visar hur du skapar en NAT-gateway med hjälp av Azure Portal och testar NAT-tjänsten
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: d798725892a9586c17cd7023863fe5cf7df05cb6
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417845"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Självstudie: skapa en NAT-gateway med hjälp av Azure Portal och testa NAT-tjänsten

I den här självstudien skapar du en NAT-gateway för att tillhandahålla utgående anslutning för virtuella datorer i Azure. Om du vill testa NAT-gatewayen distribuerar du en virtuell käll-och mål dator. Du testar NAT-gatewayen genom att göra utgående anslutningar till en offentlig IP-adress från källan till den virtuella mål datorn.  I den här självstudien distribueras källa och mål i två olika virtuella nätverk i samma resurs grupp för enkelhetens skull.

Om du vill kan du utföra de här stegen med hjälp av [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) eller [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) i stället för portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Förbered källan för utgående trafik

Vi vägleder dig genom konfigurationen av en fullständig test miljö och körningen av testerna i nästa steg. Vi kommer att börja med källan, som använder NAT-gatewayens resurs som vi skapar i senare steg.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste du skapa resurs gruppen och det virtuella nätverket.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetsource          |
| **\<region-name>**          | USA, östra 2      |
| **\<IPv4-address-space>**   | 192.168.0.0/16          |
| **\<subnet-name>**          | mySubnetsource        |
| **\<subnet-address-range>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Skapa virtuell käll dator

Nu ska vi skapa en virtuell dator för att använda NAT-tjänsten. Den här virtuella datorn har en offentlig IP-adress som kan användas som en offentlig IP-adress på instans nivå för att få åtkomst till den virtuella datorn. NAT-tjänsten är en flödes riktning som är medveten om och kommer att ersätta standard målet för Internet i ditt undernät. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

För att testa NAT-gatewayen tilldelar vi en offentlig IP-adressresurs som en offentlig IP-adress på instans nivå för att få åtkomst till den här virtuella datorn från utsidan. Den här adressen används bara för att komma åt den för testet.  Vi visar hur NAT-tjänsten prioriteras över andra utgående alternativ.

Du kan också skapa den här virtuella datorn utan en offentlig IP-adress och skapa en annan virtuell dator som du kan använda som en hoppning utan en offentlig IP-adress som en övning.

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **Compute**  >  **Ubuntu Server 18,04 LTS**eller söker efter **Ubuntu Server 18,04 LTS** i Marketplace-sökningen.

2. I **skapa en virtuell dator**anger eller väljer du följande värden på fliken **grundläggande** :
   - **Prenumeration**  >  **Resurs grupp**: Välj **myResourceGroupNAT**.
   - **Instans information**  >  **Namn på virtuell dator**: ange **myVMsource**.
   - **Instans information**  >  **Region** > väljer **USA, östra 2**.
   - **Administratörs konto**  >  **Autentisering retur**: Välj **lösen ord**.
   - **Administratörs konto** > ange **användar namn**, **lösen ord**och **Bekräfta lösen ords** information.
   - Regler för inkommande **portar**  >  **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - Regler för inkommande **portar**  >  **Välj inkommande portar**: Välj **SSH (22)**
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.

3. På fliken **nätverk** ser du till att följande är markerat:
   - **Virtuellt nätverk**: **myVnetsource**
   - **Undernät**: **mySubnetsource**
   - **Offentlig IP** > Välj **Skapa ny**.  I fönstret **skapa offentlig IP-adress** anger du **MyPublicIPsourceVM** i fältet **namn** . Välj **standard** för **SKU: n**. Lämna kvar resten av standardinställningarna och klicka på **OK**.
   - **NIC-nätverks säkerhets grupp**: Välj **Basic**.
   - **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: bekräfta **SSH** är markerat.

4. På fliken **hantering** , under **övervakning**, ställer du in **startdiagnostik** på **av**.

5. Välj **Granska + skapa**.

6. Granska inställningarna och klicka på **skapa**.

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller både och med NAT-gateway. Vi ska lägga till en offentlig IP-resurs, ett offentligt IP-prefix och en NAT-gateway-resurs.

I det här avsnittet beskrivs hur du kan skapa och konfigurera följande komponenter i NAT-tjänsten med hjälp av NAT-gateway-resursen:
  - En offentlig IP-pool och ett offentligt IP-prefix som används för utgående flöden som översätts av NAT gateway-resursen.
  - Ändra tids gränsen för inaktivitet från standardvärdet 4 minuter till 10 minuter.

### <a name="create-a-public-ip-address"></a>Skapa en offentlig IP-adress

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **nätverkets**  >  **offentliga IP-adress**eller söker efter **offentlig IP-adress** i Marketplace-sökningen. 

2. I **skapa offentlig IP-adress**anger eller väljer du den här informationen:

    | Inställningen | Värde |
    | ------- | ----- |
    | IP-version | Välj **IPv4**.
    | SKU | Välj **standard**.
    | Name | Ange **myPublicIPsource**. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **myResourceGroupNAT**. |
    | Location | Välj **USA, östra 2**.|

3. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **nätverk**  >  **offentlig IP-prefix**eller söker efter **offentliga IP-prefix** i Marketplace-sökningen.

2. I **skapa ett offentligt IP-prefix**anger eller väljer du följande värden på fliken **grundläggande** :
   - **Prenumeration**  >  **Resurs grupp**: Välj **myResourceGroupNAT**>
   - **Instans information**  >  **Namn**: ange **myPublicIPprefixsource**.
   - **Instans information**  >  **Region**: Välj **USA, östra 2**.
   - **Instans information**  >  **Prefixlängd**: Välj **/31 (2 adresser)**

3. Lämna resten av standardinställningarna och välj **Granska + skapa**.

4. Granska inställningarna och välj sedan **Skapa**.


### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gateway-resurs

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **nätverk**  >  **NAT gateway**eller söker efter **NAT-gateway** i Marketplace-sökningen.

2. I **skapa Network Address Translation (NAT) Gateway**anger eller väljer du följande värden på fliken **grundläggande** :
   - **Prenumeration**  >  **Resurs grupp**: Välj **myResourceGroupNAT**.
   - **Instans information**  >  **Namn på NAT-gateway**: ange **myNATgateway**.
   - **Instans information**  >  **Region**: Välj **USA, östra 2**.
   - **Instans information**  >  **Tids gräns för inaktivitet (minuter)**: ange **10**.
   - Välj fliken **offentlig IP-adress** eller Välj **Nästa: offentlig IP**.

3. På fliken **offentlig IP** anger eller väljer du följande värden:
   - **Offentliga IP-adresser**: Välj **myPublicIPsource**.
   - **Offentliga IP-prefix**: Välj **myPublicIPprefixsource**.
   - Välj fliken **undernät** eller Välj **Nästa: undernät**.

4. På fliken **undernät** anger eller väljer du följande värden:
   - **Virtual Network**: Välj **myResourceGroupNAT**  >  **myVnetsource**.
   - **Under näts namn**: Markera kryss rutan bredvid **mySubnetsource**.

5. Välj **Granska + skapa**.

6. Granska inställningarna och välj sedan **Skapa**.

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Förbered mål för utgående trafik

Nu ska vi skapa ett mål för den utgående trafik som översätts av NAT-tjänsten så att du kan testa den.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtuellt nätverk och parametrar för mål

Innan du distribuerar en virtuell dator för målet måste vi skapa ett virtuellt nätverk där den virtuella mål datorn kan finnas. Följande är samma steg som för den virtuella käll datorn med några små ändringar för att exponera mål slut punkten.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNetdestination          |
| **\<region-name>**          | USA, östra 2      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | mySubnetdestination        |
| **\<subnet-address-range>** | 10.1.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Skapa en virtuell mål dator

1. På den övre vänstra sidan av portalen väljer du **skapa en resurs**  >  **Compute**  >  **Ubuntu Server 18,04 LTS**eller söker efter **Ubuntu Server 18,04 LTS** i Marketplace-sökningen.

2. I **skapa en virtuell dator**anger eller väljer du följande värden på fliken **grundläggande** :
   - **Prenumeration**  >  **Resurs grupp**: Välj **myResourceGroupNAT**.
   - **Instans information**  >  **Namn på virtuell dator**: ange **myVMdestination**.
   - **Instans information**  >  **Region** > väljer **USA, östra 2**.
   - **Administratörs konto**  >  **Autentisering retur**: Välj **lösen ord**.
   - **Administratörs konto** > ange **användar namn**, **lösen ord**och **Bekräfta lösen ords** information.
   - Regler för inkommande **portar**  >  **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - Regler för inkommande **portar**  >  **Välj inkommande portar**: Välj **SSH (22)** och **http (80)**.
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.

3. På fliken **nätverk** ser du till att följande är markerat:
   - **Virtuellt nätverk**: **myVnetdestination**
   - **Undernät**: **mySubnetdestination**
   - **Offentlig IP** > Välj **Skapa ny**.  I fönstret **skapa offentlig IP-adress** anger du **MyPublicIPdestinationVM** i fältet **namn** . Välj **standard** för **SKU**. Lämna kvar resten av standardinställningarna och klicka på **OK**.
   - **NIC-nätverks säkerhets grupp**: Välj **Basic**.
   - **Offentliga inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: bekräfta **SSH** och **http** är markerat.

4. På fliken **hantering** , under **övervakning**, ställer du in **startdiagnostik** på **av**.

5. Välj **Granska + skapa**.

6. Granska inställningarna och välj sedan **Skapa**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Förbered en webb server och testa nytto lasten på den virtuella mål datorn

Först måste vi identifiera IP-adressen för den virtuella mål datorn. 

1. Välj **resurs grupper**på vänster sida av portalen.
2. Välj **myResourceGroupNAT**.
3. Välj **myVMdestination**.
4. I **Översikt**, kopierar du värdet för den **offentliga IP-adressen** och klistrar in i anteckningar så att du kan använda det för att få åtkomst till den virtuella datorn.

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den i efterföljande steg. Ange det här är den virtuella mål datorn.

### <a name="sign-in-to-destination-vm"></a>Logga in på den virtuella mål datorn

Öppna en [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

Kopiera och klistra in följande kommandon när du har loggat in.  

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get -y install nginx && \
sudo ln -sf /dev/null /var/log/nginx/access.log && \
sudo touch /var/www/html/index.html && \
sudo rm /var/www/html/index.nginx-debian.html && \
sudo dd if=/dev/zero of=/var/www/html/100k bs=1024 count=100
```

De här kommandona kommer att uppdatera den virtuella datorn, installera nginx och skapa en 100-KByte-fil. Den här filen kommer att hämtas från den virtuella käll datorn med hjälp av NAT-tjänsten.

Stäng SSH-sessionen med den virtuella mål datorn.

## <a name="prepare-test-on-source-vm"></a>Förbered testet på den virtuella käll datorn

Först måste vi identifiera IP-adressen för den virtuella käll datorn.

1. Välj **resurs grupper**på vänster sida av portalen.
2. Välj **myResourceGroupNAT**.
3. Välj **myVMsource**.
4. I **Översikt**, kopierar du värdet för den **offentliga IP-adressen** och klistrar in i anteckningar så att du kan använda det för att få åtkomst till den virtuella datorn.

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra in den i ett anteckningar så att du kan använda den i efterföljande steg. Ange det här är den virtuella käll datorn.

### <a name="log-into-source-vm"></a>Logga in på den virtuella käll datorn

Öppna en ny flik för [Azure Cloud Shell](https://shell.azure.com) i webbläsaren.  Använd IP-adressen som hämtades i föregående steg för SSH till den virtuella datorn. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Kopiera och klistra in följande kommandon för att förbereda för att testa NAT-tjänsten.

```bash
sudo apt-get -y update && \
sudo apt-get -y upgrade && \
sudo apt-get -y dist-upgrade && \
sudo apt-get -y autoremove && \
sudo apt-get -y autoclean && \
sudo apt-get install -y nload golang && \
echo 'export GOPATH=${HOME}/go' >> .bashrc && \
echo 'export PATH=${PATH}:${GOPATH}/bin' >> .bashrc && \
. ~/.bashrc &&
go get -u github.com/rakyll/hey

```

Det här kommandot kommer att uppdatera den virtuella datorn, installera Go, installera [Hej](https://github.com/rakyll/hey) från GitHub och uppdatera din gränssnitts miljö.

Nu är du redo att testa NAT-tjänsten.

## <a name="validate-nat-service"></a>Verifiera NAT-tjänst

När du har loggat in på den virtuella käll datorn kan du använda **sväng** och **Hej** för att generera begär anden till målets IP-adress.

Använd sväng för att hämta filen 100-KByte.  Ersätt **\<ip-address-destination>** i exemplet nedan med mål-IP-adressen som du har kopierat tidigare.  Parametern **--output** anger att den hämtade filen kommer att tas bort.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Du kan också skapa en serie förfrågningar med hjälp av **Hej**. Ersätt igen **\<ip-address-destination>** med mål-IP-adressen som du tidigare har kopierat.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Det här kommandot genererar 100-begär Anden, 10 samtidigt, med en tids gräns på 30 sekunder och utan att återanvända TCP-anslutningen.  Varje begäran kommer att hämta 100 kByte.  I slutet av körningen rapporterar **Hej** viss statistik om hur bra NAT-tjänsten gjorde.

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, NAT-gatewayen och alla relaterade resurser när de inte längre behövs. Välj den resurs grupp **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här självstudien har du skapat en NAT-gateway, skapat en källa och en virtuell måldator och testat NAT-gatewayen.

Granska mått i Azure Monitor för att se hur NAT-tjänsten fungerar. Diagnostisera problem som resurs utbelastning av tillgängliga SNAT-portar.  Resurs överbelastning av SNAT-portar kan enkelt åtgärdas genom att lägga till ytterligare offentliga IP-adressresurser eller resurser för offentliga IP-prefix eller båda.

- Läs mer om [Virtual Network NAT](./nat-overview.md)
- Lär dig om [NAT gateway-resurs](./nat-gateway-resource.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabb start för att distribuera [NAT gateway-resurs med hjälp av Azure Portal](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

