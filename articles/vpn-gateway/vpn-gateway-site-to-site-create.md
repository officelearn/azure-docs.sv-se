<properties
   pageTitle="Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med den klassiska Azure-portalen | Microsoft Azure"
   description="Skapa ett VNet med en S2S VPN Gateway-anslutning mellan flera platser och hybridkonfigurationer med hjälp av den klassiska distributionsmodellen."
   services="vpn-gateway"
   documentationCenter=""
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med den klassiska Azure-portalen

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Klassisk Azure-portal](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Den här artikeln visar hur du skapar ett virtuellt nätverk och VPN-anslutning från plats till plats till ditt lokala nätverk. Plats-till-plats-anslutningar kan användas för flera platser och hybridkonfigurationer. Den här artikeln gäller den klassiska distributionsmodellen och använder den klassiska Azure-portalen. 


**Om Azures distributionsmodeller**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## Anslutningsdiagram
 
![Plats-till-plats-diagram](./media/vpn-gateway-site-to-site-create/site2site.png "site-to-site")

**Distributionsmodeller och verktyg för plats-till-plats-anslutningar**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Om du vill koppla ihop VNets, men inte skapar någon anslutning till en lokal plats, kan du läsa mer i [Konfigurera en VNet-till-VNet-anslutning för den klassiska distributionsmodellen](virtual-networks-configure-vnet-to-vnet-connection.md). Om du letar efter en annan typ av anslutningskonfiguration, kan du läsa mer i [Anslutningstopologier för VPN Gateway](vpn-gateway-topology.md).

 
## Innan du börjar

Kontrollera att du har följande innan du påbörjar konfigurationen.

- En kompatibel VPN-enhet och någon som kan konfigurera den. Se [Om VPN-enheter](vpn-gateway-about-vpn-devices.md). Om du inte vet hur man konfigurerar VPN-enheten eller inte känner till IP-adressintervallen i din lokala nätverkskonfiguration, måste du vända dig till någon som kan ge den informationen till dig.

-  En extern offentlig IP-adress för VPN-enheten. Den här IP-adressen får inte finnas bakom en NAT.

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/).


## Skapa ditt virtuella nätverk

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com/).

2. I det nedre vänstra hörnet på skärmen klickar du på **Nytt**. I navigeringsfönstret klickar du på **Nätverkstjänster** och sedan på **Virtuellt nätverk**. Klicka på **Skapa anpassade** för att starta konfigurationsguiden.

3. Fyll i informationen på följande sidor för att skapa ditt VNet.

## Sidan Virtuell nätverksinformation

Ange följande information.

- **Namn**: Ge ett namn till det virtuella nätverket. Till exempel *EastUSVNet*. Du använder det här virtuella nätverksnamnet när du distribuerar dina virtuella datorer och PaaS-instanser, så du bör inte göra namnet för komplicerat. 
- **Plats**: Platsen är direkt relaterad till den fysiska platsen (region) där du vill att resurserna (VM) ska finnas. Om du exempelvis vill att de virtuella datorer som du distribuerar till det här virtuella nätverket ska finnas fysiskt i *östra USA*, väljer du den platsen. Du kan inte ändra den region som är associerad med det virtuella nätverket när du har skapat det.

## Sidan DNS-servrar och VPN-anslutning

Ange följande information och klicka sedan på pilen Nästa nere till höger.

- **DNS-servrar**: Ange DNS-servernamnet och IP-adressen, eller välj en tidigare registrerad DNS-server i snabbmenyn. Den här inställningen skapar inte någon DNS-server, utan låter dig ange vilka DNS-servrar du vill använda vid namnmatchning i det här virtuella nätverket.
- **Konfigurera VPN för plats-till-plats**: Markera kryssrutan för **Konfigurera VPN för plats-till-plats**.
- **Lokalt nätverk**: Ett lokalt nätverk representerar den fysiska lokala platsen. Du kan välja ett lokalt nätverk som du tidigare har skapat, eller skapa ett nytt lokalt nätverk. Men om du väljer att använda ett lokalt nätverk som du skapat tidigare, bör du gå till konfigurationssidan **Lokala nätverk** och kontrollera att VPN-enhetens IP-adress (offentlig IPv4-adress) för VPN-enheten som du använder till den här anslutningen är korrekt.

## Sidan Plats-till-plats-anslutning 

Om du skapar ett nytt lokalt nätverk visas sidan **Plats-till-plats-anslutning**. Om du vill använda ett lokalt nätverk som du skapat tidigare, visas inte den här sidan i guiden och du kan gå vidare till nästa avsnitt.

Ange följande information och klicka sedan på pilen Nästa.

-   **Namn**: Namnet som du valt för din lokala nätverksplats.
-   **IP-adress för VPN-enhet**: Detta är den offentliga IPv4-adressen för din lokala VPN-enhet som du använder för att ansluta till Azure. VPN-enheten får inte finnas bakom en NAT.
-   **Adressutrymme**: Ta med inledande IP-adress och CIDR (antal adresser). Det är här du anger de adressintervall som du vill ska skickas via den virtuella nätverksgatewayen till din lokala plats. Om målets IP-adress inte är inom det intervall som du anger här, kommer den att dirigeras via den virtuella nätverksgatewayen.
-   **Lägg till adressutrymme**: Om du har flera adressintervall som du vill ska skickas via den virtuella nätverksgatewayen, är det här du anger varje ytterligare adressintervall. Du kan lägga till eller ta bort intervall senare på sidan **Lokalt nätverk**.

## Sidan Adressutrymmen för virtuella nätverk

Ange det adressintervall som du vill använda i det virtuella nätverket. Detta är de dynamiska IP-adresser (DIPS) som ska tilldelas de virtuella datorerna och andra rollinstanser som du distribuerar till det här virtuella nätverket.

Det är särskilt viktigt att välja ett intervall som inte överlappar med något av de intervall som används i det lokala nätverket. Kontakta nätverksadministratören för att få mer information. Nätverksadministratören kan behöva dela ut ett intervall med IP-adresser från ditt lokala nätverksadressutrymme som du kan använda i det virtuella nätverket.

Ange följande information och klicka sedan på bockmarkeringen längst ned till höger för att konfigurera nätverket.

- **Adressutrymme**: Ta med inledande IP-adress och antal adresser. Kontrollera att de adressutrymmen du anger inte överlappar några adressutrymmen som du har i det lokala nätverket.
- **Lägg till undernät**: Ta med inledande IP-adress och antal adresser. Fler undernät krävs inte, men du kanske vill skapa ett separat undernät för virtuella datorer med statiska DIPS. Du kan också vilja ha dina virtuella datorer i ett undernät som är avgränsat från dina andra rollinstanser.
- **Lägg till gateway-undernät**: Klicka för att lägga till gateway-undernätet. Gateway-undernätet används bara för den virtuella nätverksgatewayen och krävs för den här konfigurationen.

Klicka på bockmarkeringen längst ned på sidan. Det virtuella nätverket börjar att skapas. När det är slutfört visas **Skapad** under **Status** på sidan **Nätverk** i den klassiska Azure-portalen. När du har skapat VNet kan du konfigurera din virtuella nätverksgateway.

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 

## Konfigurera den virtuella nätverksgatewayen

Därefter konfigurerar du den virtuella nätverksgatewayen för att skapa en säker plats-till-plats-anslutning. Se [Konfigurera en virtuell nätverksgateway i den klassiska Azure-portalen](vpn-gateway-configure-vpn-gateway-mp.md).

## Nästa steg

När anslutningen är klar kan du lägga till virtuella datorer till dina virtuella nätverk. Mer information finns i dokumentationen för [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/).



<!--HONumber=jun16_HO2-->


