---
title: 'Självstudiekurs: Skapa och testa en NAT Gateway - Azure-portal'
titlesuffix: Azure Virtual Network NAT
description: Den här självstudien visar hur du skapar en NAT-gateway med Azure-portalen och testar NAT-tjänsten
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumundD
Customer intent: I want to test a NAT Gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: ceadbb4297ad0c5ce28470dd75b3f3496c9c5152
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084751"
---
# <a name="tutorial-create-a-nat-gateway-using-the-azure-portal-and-test-the-nat-service"></a>Självstudiekurs: Skapa en NAT-gateway med Azure-portalen och testa NAT-tjänsten

I den här självstudien skapar du en NAT-gateway för att tillhandahålla utgående anslutning för virtuella datorer i Azure. Om du vill testa NAT-gatewayen distribuerar du en virtuell dator för källa och mål. Du ska testa NAT-gatewayen genom att skapa utgående anslutningar till en offentlig IP-adress från källan till den virtuella måldatorn.  Den här självstudien distribuerar källa och mål i två olika virtuella nätverk i samma resursgrupp endast för enkelhetens skull.

Om du vill kan du utföra de här stegen med hjälp av [Azure CLI](tutorial-create-validate-nat-gateway-cli.md) eller [Azure PowerShell](tutorial-create-validate-nat-gateway-powershell.md) i stället för portalen.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="prepare-the-source-for-outbound-traffic"></a>Förbereda källan för utgående trafik

Vi guidar dig genom konfiguration av en fullständig testmiljö och genomförandet av själva testerna i nästa steg. Vi börjar med källan, som kommer att använda NAT gateway-resurs som vi skapar i senare steg.

## <a name="virtual-network-and-parameters"></a>Virtuellt nätverk och parametrar

Innan du distribuerar en virtuell dator och kan använda din NAT-gateway måste vi skapa resursgruppen och det virtuella nätverket.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroupNAT |
| **\<>virtuellt nätverksnamn** | myVNetsource          |
| **\<regionnamn>**          | USA, östra 2      |
| **\<IPv4-adress-utrymme>**   | 192.168.0.0/16          |
| **\<>i>**          | mySubnetsource        |
| **\<>för>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-source-virtual-machine"></a>Skapa virtuell källdator

Vi ska nu skapa en virtuell dator för att använda NAT-tjänsten. Den här virtuella datorn har en offentlig IP att använda som en offentlig IP på instansnivå så att du kan komma åt den virtuella datorn. NAT-tjänsten är flödesriktningsmedveten och ersätter standardmålet för Internet i undernätet. Den virtuella datorns offentliga IP-adress används inte för utgående anslutningar.

Om du vill testa NAT-gatewayen tilldelar vi en offentlig IP-adressresurs som en offentlig IP-ip på instansnivå för åtkomst till den här virtuella datorn utifrån. Den här adressen används endast för att komma åt den för testet.  Vi visar hur NAT-tjänsten har företräde framför andra utgående alternativ.

Du kan också skapa den här virtuella datorn utan en offentlig IP och skapa en annan virtuell dator som ska användas som en jumpbox utan en offentlig IP som en övning.

1. På portalens övre vänstra sida väljer du **Skapa en resurs** > **Compute** > **Ubuntu Server 18.04 LTS**eller sök efter **Ubuntu Server 18.04 LTS** i Marketplace-sökningen.

2. Ange eller välj följande värden på fliken **Grunderna** i **Skapa en virtuell dator:**
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**.
   - **Instansinformation** > **Virtuell datornamn:** ange **myVMsource**.
   - **Instance Details** > Region för**instansinformation** > väljer **östra USA 2**.
   - **Administratörskonto** > **Autentisering ange:** Välj **lösenord**.
   - **Administratörskontot** > ange **information om användarnamn,** **lösenord**och **bekräfta lösenord.**
   - **Regler för inkommande portar** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Regler för inkommande port** > **Välj inkommande portar**: Välj **SSH (22)**
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.

3. Kontrollera att följande är markerade på fliken **Nätverk:**
   - **Virtuellt nätverk:** **myVnetsource**
   - **Undernät**: **mySubnetsource**
   - **Offentliga IP->** Välj **Skapa ny**.  I fönstret **Skapa offentlig IP-adress** anger du **myPublicIPsourceVM** i fältet **Namn.** Välj **Standard** för **SKU**. Lämna resten vid standardinställningarna och klicka på **OK**.
   - **Säkerhetsgrupp för nätverksnätverk:** Välj **Grundläggande**.
   - **Inkommande inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: Bekräfta att **SSH** är markerat.

4. Ange **Startdiagnostik** på **Av**under **Övervakning**på fliken **Hantering** .

5. Välj **Granska + skapa**.

6. Granska inställningarna och klicka på **Skapa**.

## <a name="create-the-nat-gateway"></a>Skapa NAT-gatewayen

Du kan använda en eller flera offentliga IP-adressresurser, offentliga IP-prefix eller båda med NAT-gateway. Vi lägger till en offentlig IP-resurs, offentligt IP-prefix och en NAT-gatewayresurs.

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
    | Namn | Ange **myPublicIPsource**. |
    | Prenumeration | Välj din prenumeration.|
    | Resursgrupp | Välj **myResourceGroupNAT**. |
    | Location | Välj **USA, östra 2**.|

3. Lämna resten av standardinställningarna och välj **Skapa**.

### <a name="create-a-public-ip-prefix"></a>Skapa ett offentligt IP-prefix

1. På portalens övre vänstra sida väljer du Skapa ett**offentligt IP-resursprefix**för **nätverknätverk** > **Networking** > eller sök efter **offentligt IP-prefix** i Marketplace-sökningen.

2. Ange eller markera följande värden på fliken **Grunderna** i **Skapa ett offentligt IP-prefix:**
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**>
   - **Förekomstinformation** > **Namn:** ange **myPublicIPprefixsource**.
   - **Instansinformation** > **Region:** Välj **Östra USA 2**.
   - **Instansinformation** > **Prefixstorlek**: Välj **/31 (2 adresser)**

3. Lämna resten standardvärdena och välj **Granska + skapa**.

4. Granska inställningarna och välj sedan **Skapa**.


### <a name="create-a-nat-gateway-resource"></a>Skapa en NAT-gatewayresurs

1. På portalens övre vänstra sida väljer du Skapa en**NAT-gateway**för > **nätverknätverk** > eller sök efter **NAT-gateway** i Marketplace-sökningen. **Create a resource**

2. Ange eller välj följande värden på fliken Grunderna i **Skapa nätverksadressöversättning (NAT):In Create network address translation (NAT) gateway**, enter or select the following values in the **Basics** tab:
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**.
   - **Instansinformation** > **NAT gateway namn:** ange **myNATgateway**.
   - **Instansinformation** > **Region:** Välj **Östra USA 2**.
   - **Instansinformation** > **Inaktiv timeout (minuter):** ange **10**.
   - Välj fliken **Offentlig IP** eller välj **Nästa: Offentlig IP**.

3. Ange eller välj följande värden på fliken **Offentlig IP:**
   - **Offentliga IP-adresser:** Välj **myPublicIPsource**.
   - **Offentliga IP-prefix:** Välj **myPublicIPprefixsource**.
   - Välj fliken **Undernät** eller välj **Nästa: Undernät**.

4. Ange eller välj följande värden på fliken **Undernät:**
   - **Virtuellt nätverk:** Välj **myResourceGroupNAT** > **myVnetsource**.
   - **Undernätsnamn**: Markera rutan bredvid **mySubnetsource**.

5. Välj **Granska + skapa**.

6. Granska inställningarna och välj sedan **Skapa**.

All utgående trafik till Internet-destinationer använder nu NAT-tjänsten.  Det är inte nödvändigt att konfigurera en UDR.


## <a name="prepare-destination-for-outbound-traffic"></a>Förbered destination för utgående trafik

Vi skapar nu en destination för den utgående trafik som översätts av NAT-tjänsten så att du kan testa den.


## <a name="virtual-network-and-parameters-for-destination"></a>Virtuellt nätverk och parametrar för mål

Innan du distribuerar en virtuell dator för målet måste vi skapa ett virtuellt nätverk där den virtuella måldatorn kan finnas. Följande är samma steg som för källdatorn med några små ändringar för att exponera målslutpunkten.

I det här avsnittet måste du ersätta följande parametrar i stegen med informationen nedan:

| Parameter                   | Värde                |
|-----------------------------|----------------------|
| **\<resursgruppnamn>**  | myResourceGroupNAT |
| **\<>virtuellt nätverksnamn** | myVNetdestination          |
| **\<regionnamn>**          | USA, östra 2      |
| **\<IPv4-adress-utrymme>**   | 192.168.0.0/16          |
| **\<>i>**          | mySubnetdestination        |
| **\<>för>** | 192.168.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-destination-virtual-machine"></a>Skapa virtuell måldator

1. På portalens övre vänstra sida väljer du **Skapa en resurs** > **Compute** > **Ubuntu Server 18.04 LTS**eller sök efter **Ubuntu Server 18.04 LTS** i Marketplace-sökningen.

2. Ange eller välj följande värden på fliken **Grunderna** i **Skapa en virtuell dator:**
   - **Subscription** > **Prenumerationsresursgrupp:** Välj **myResourceGroupNAT**.
   - **Instansinformation** > **Virtuell datornamn:** ange **myVMdestination**.
   - **Instance Details** > Region för**instansinformation** > väljer **östra USA 2**.
   - **Administratörskonto** > **Autentisering ange:** Välj **lösenord**.
   - **Administratörskontot** > ange **information om användarnamn,** **lösenord**och **bekräfta lösenord.**
   - **Regler för inkommande portar** > **Offentliga inkommande portar**: Välj Tillåt valda **portar**.
   - **Regler för inkommande port** > **Välj inkommande portar**: Välj **SSH (22)** och **HTTP (80)**.
   - Välj fliken **Nätverk** eller **Nästa: diskar** och sedan **Nästa: nätverk**.

3. Kontrollera att följande är markerade på fliken **Nätverk:**
   - **Virtuellt nätverk**: **myVnetdestination**
   - **Undernät**: **mySubnetdestination**
   - **Offentliga IP->** Välj **Skapa ny**.  I fönstret **Skapa offentlig IP-adress** anger du **myPublicIPdestinationVM** i fältet **Namn.** Välj **Standard** för **SKU**. Lämna resten vid standardinställningarna och klicka på **OK**.
   - **Säkerhetsgrupp för nätverksnätverk:** Välj **Grundläggande**.
   - **Inkommande inkommande portar**: Välj **Tillåt valda portar**.
   - **Välj inkommande portar**: Bekräfta **att SSH** och **HTTP** är markerat.

4. Ange **Startdiagnostik** på **Av**under **Övervakning**på fliken **Hantering** .

5. Välj **Granska + skapa**.

6. Granska inställningarna och välj sedan **Skapa**.

## <a name="prepare-a-web-server-and-test-payload-on-destination-vm"></a>Förbereda en webbserver och testa nyttolasten på måldatorn

Först måste vi upptäcka IP-adressen för målet VM. 

1. Välj **Resursgrupper**till vänster i portalen .
2. Välj **myResourceGroupNAT**.
3. Välj **myVMdestination**.
4. Kopiera värdet för den **offentliga IP-adressen** i **Översikt**och klistra in det i anteckningar så att du kan använda det för att komma åt den virtuella datorn.

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den i efterföljande steg. Ange att det här är den virtuella måldatorn.

### <a name="sign-in-to-destination-vm"></a>Logga in på mål-VM

Öppna ett [Azure Cloud Shell](https://shell.azure.com) i webbläsaren. Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn.

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

Dessa kommandon uppdaterar din virtuella dator, installerar nginx och skapar en 100-KBytes-fil. Den här filen hämtas från källdatorn med NAT-tjänsten.

Stäng SSH-sessionen med den virtuella måldatorn.

## <a name="prepare-test-on-source-vm"></a>Förbereda test på käll-VM

Först måste vi upptäcka IP-adressen för käll-VM.

1. Välj **Resursgrupper**till vänster i portalen .
2. Välj **myResourceGroupNAT**.
3. Välj **myVMsource**.
4. Kopiera värdet för den **offentliga IP-adressen** i **Översikt**och klistra in det i anteckningar så att du kan använda det för att komma åt den virtuella datorn.

>[!IMPORTANT]
>Kopiera den offentliga IP-adressen och klistra sedan in den i ett anteckningsblock så att du kan använda den i efterföljande steg. Ange att det här är den virtuella datorn för källan.

### <a name="log-into-source-vm"></a>Logga in på käll-VM

Öppna en ny flik för [Azure Cloud Shell](https://shell.azure.com) i webbläsaren.  Använd IP-adressen som hämtades i föregående steg till SSH till den virtuella datorn. 

```azurecli-interactive
ssh <username>@<ip-address-source>
```

Kopiera och klistra in följande kommandon för att förbereda för testning av NAT-tjänsten.

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

Det här kommandot uppdaterar din virtuella dator, installerar go, installerar [hey](https://github.com/rakyll/hey) från GitHub och uppdaterar skalmiljön.

Du är nu redo att testa NAT-tjänsten.

## <a name="validate-nat-service"></a>Validera NAT-tjänsten

När du är inloggad på källdatorn kan du använda **curl** and **hey** för att generera begäranden till mål-IP-adressen.

Använd curl för att hämta filen 100-KBytes.  Ersätt ** \<ip-adress-mål>** i exemplet nedan med den mål-IP-adress som du tidigare har kopierat.  Parametern **--output** anger att den hämtade filen kommer att ignoreras.

```bash
curl http://<ip-address-destination>/100k --output /dev/null
```

Du kan också generera en serie begäranden med **hey**. Återigen, ersätta ** \<ip-adress-mål>** med målet IP-adress som du tidigare har kopierat.

```bash
hey -n 100 -c 10 -t 30 --disable-keepalive http://<ip-address-destination>/100k
```

Det här kommandot genererar 100 begäranden, 10 samtidigt, med en timeout på 30 sekunder och utan att återanvända TCP-anslutningen.  Varje begäran hämtar 100 Kbyte.  I slutet av körningen, **hey** kommer att rapportera en del statistik om hur bra NAT-tjänsten gjorde.

## <a name="clean-up-resources"></a>Rensa resurser

När det inte längre behövs tar du bort resursgruppen, NAT-gatewayen och alla relaterade resurser. Markera resursgruppen **myResourceGroupNAT** som innehåller NAT-gatewayen och välj sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg
I den här självstudien skapade du en NAT-gateway, skapade en käll- och mål-VM och testade sedan NAT-gatewayen.

Granska mått i Azure Monitor för att se nat-tjänsten fungera. Diagnostisera problem som resursutmattning av tillgängliga SNAT-portar.  Resursutmattning av SNAT-portar åtgärdas enkelt genom att lägga till ytterligare offentliga IP-adressresurser eller offentliga IP-prefixresurser eller båda.

- Lär dig mer om [NAT för virtuellt nätverk](./nat-overview.md)
- Lär dig mer om [NAT-gatewayresurs](./nat-gateway-resource.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure CLI](./quickstart-create-nat-gateway-cli.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure PowerShell](./quickstart-create-nat-gateway-powershell.md).
- Snabbstart för distribution av [NAT-gatewayresurs med Azure-portalen](./quickstart-create-nat-gateway-portal.md).

> [!div class="nextstepaction"]

