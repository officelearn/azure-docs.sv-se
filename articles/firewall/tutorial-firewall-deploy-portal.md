---
title: 'Självstudier: Distribuera och konfigurera Azure Firewall via Azure Portal'
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall via Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 4/9/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 9d7b9673101ed3b6ff85a9981ba061bc870762b1
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2019
ms.locfileid: "65405673"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Självstudier: Distribuera och konfigurera Azure Firewall via Azure Portal

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kan till exempel vill begränsa åtkomsten till webbplatser. Eller du kanske vill begränsa de utgående IP-adresser och portar som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här självstudien skapar du ett förenklat virtuellt nätverk med tre undernät för enkel distribution. Vid Produktionsdistribution måste en [NAV och ekrar modellen](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) rekommenderas, där den är i ett eget virtuellt nätverk. Arbetsbelastningen-servrar finns i peerkopplade virtuella nätverk i samma region med en eller flera undernät.

* **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
* **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
* **Jump-SN** – ”hopp”-servern ligger i det här undernätet. Hoppservern har en offentlig IP-adress som du kan ansluta till via Fjärrskrivbord. Därifrån kan du sedan ansluta till arbetsbelastningsservern (via ett annat Fjärrskrivbord).

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-rules-portal/Tutorial_network.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en regel för programmet för att tillåta åtkomst till www.google.com
> * Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
> * Testa brandväggen

Om du vill kan du utföra den här självstudien med [Azure PowerShell](deploy-ps.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen innehåller alla resurser för den här självstudien.

1. Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).
2. Azure portalens startsida, Välj **resursgrupper** > **Lägg till**.
3. I fältet **Resursgruppsnamn** skriver du **Test-FW-RG**.
4. I fältet **Prenumeration** väljer du din prenumeration.
5. I fältet **Resursgruppsplats** väljer du en plats. Alla efterföljande resurser du skapar måste finnas på samma plats.
6. Välj **Skapa**.

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Det här virtuella nätverket innehåller tre undernät.

1. Välj Azure portalens startsida, **skapa en resurs**.
2. Under **nätverk**väljer **virtuellt nätverk**.
4. I fältet **Namn** skriver du **Test-FW-VN**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resursgrupp**väljer **Test-VB-RG**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
10. I fältet **Adressintervall** skriver du **10.0.1.0/24**.
11. Acceptera de andra standardinställningarna och välj sedan **skapa**.

> [!NOTE]
> Den minsta storleken på AzureFirewallSubnet-undernätet är /26.

### <a name="create-additional-subnets"></a>Skapa ytterligare undernät

Skapa sedan undernät för hoppservern och ett undernät för arbetsbelastningsservrarna.

1. Azure portalens startsida, Välj **resursgrupper** > **Test-VB-RG**.
2. Välj den **Test-VB-VN** virtuellt nätverk.
3. Välj **undernät** > **+ undernät**.
4. I fältet **Namn** skriver du **Workload-SN**.
5. I fältet **Adressintervall** skriver du **10.0.2.0/24**.
6. Välj **OK**.

Skapa ett nytt undernät med namnet **Jump-SN** och adressintervallet **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu de virtuella hopp- och arbetsbelastningsdatorerna och placera dem i respektive undernät.

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

   |Inställning  |Värde  |
   |---------|---------|
   |Resursgrupp     |**Test-FW-RG**|
   |Namn på virtuell dator     |**Srv-Jump**|
   |Region     |Samma som föregående|
   |Administratörens användarnamn     |**azureuser**|
   |Lösenord     |**Azure123456!**|

4. Under **regler för inkommande portar**, för **offentliga inkommande portar**väljer **valda portar**.
5. I fältet **Välj inkommande portar** väljer du **RDP (3389)**.

6. Acceptera andra standardinställningar och välj **nästa: Diskar**.
7. Acceptera standardinställningarna för disken och välj **nästa: Nätverk**.
8. Kontrollera att **Test-FW-VN** har valts som virtuellt nätverk och att undernätet är **Jump-SN**.
9. För **offentlig IP-adress**, Använd ny offentlig ip-adress standardnamnet (Srv-hopp-ip).
11. Acceptera andra standardinställningar och välj **nästa: Hantering**.
12. Välj **av** inaktivera startdiagnostikinställningar. Acceptera andra standardinställningar och välj **granska + skapa**.
13. Granska inställningarna på sidan Sammanfattning och välj sedan **skapa**.

Använd informationen i följande tabell för att konfigurera en annan virtuell dator med namnet **Srv-arbete**. Resten av konfigurationen är samma som för den virtuella datorn Srv-Jump.

|Inställning  |Värde  |
|---------|---------|
|Undernät|**Workload-SN**|
|Offentlig IP|**Ingen**|
|Offentliga inkommande portar|**Ingen**|

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera brandväggen till det virtuella nätverket.

1. Portalens startsida, Välj **skapa en resurs**.
2. Typ **brandväggen** i sökrutan och tryck på **RETUR**.
3. Välj **brandväggen** och välj sedan **skapa**.
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Prenumeration     |\<din prenumeration\>|
   |Resursgrupp     |**Test-FW-RG** |
   |Namn     |**Test-FW01**|
   |Location     |Välj samma plats som tidigare|
   |Välj ett virtuellt nätverk     |**Använd befintlig**: **Test-FW-VN**|
   |Offentlig IP-adress     |**Skapa ny**. Den offentliga IP-adressen måste vara Standard SKU-typen.|

5. Välj **Granska + skapa**.
6. Granska sammanfattningen och välj sedan **skapa** att skapa brandväggen.

   Distributionen kan ta några minuter.
7. När distributionen är klar går du till den **Test-VB-RG** resource gruppen och välj den **Test FW01** brandväggen.
8. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **Workload-SN** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. Välj Azure portalens startsida, **alla tjänster**.
2. Under **nätverk**väljer **routningstabeller**.
3. Välj **Lägg till**.
4. I fältet **Namn** skriver du **Firewall-route**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. För **resursgrupp**väljer **Test-VB-RG**.
7. Välj samma plats som tidigare i fältet **Plats**.
8. Välj **Skapa**.
9. Välj **uppdatera**, och välj sedan den **brandväggen-route** routningstabellen.
10. Välj **undernät** och välj sedan **associera**.
11. Välj **virtuellt nätverk** > **Test-VB-VN**.
12. För **undernät**väljer **arbetsbelastning-SN**. Kontrollera att du väljer bara den **arbetsbelastning-SN** undernät för den här vägen, annars brandväggen fungerar inte korrekt.

13. Välj **OK**.
14. Välj **vägar** och välj sedan **Lägg till**.
15. För **vägnamn**, typ **fw dg**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Välj **OK**.

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Det här är den program-regel som tillåter utgående åtkomst till www.google.com.

1. Öppna den **Test-VB-RG**, och välj den **Test FW01** brandväggen.
2. På den **Test-FW01** sidan under **inställningar**väljer **regler**.
3. Välj den **program regelsamlingen** fliken.
4. Välj **lägga till programmet regelsamlingen**.
5. I fältet **Namn** skriver du **App-Coll01**.
6. I fältet **Prioritet** skriver du **200**.
7. I fältet **Åtgärd** väljer du **Tillåt**.
8. Under **regler**, **Target FQDN**, för **namn**, typ **Tillåt Google-**.
9. I fältet **Källadresser** skriver du **10.0.2.0/24**.
10. I fältet **Protokoll: port** skriver du **http, https**.
11. För **Target FQDN**, typ **www.google.com**
12. Välj **Lägg till**.

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Det här är nätverksregel som tillåter utgående åtkomst till två IP-adresser på port 53 (DNS).

1. Välj den **Network regelsamlingen** fliken.
2. Välj **Lägg till nätverk regelsamlingen**.
3. I fältet **Namn** skriver du **Net-Coll01**.
4. I fältet **Prioritet** skriver du **200**.
5. I fältet **Åtgärd** väljer du **Tillåt**.

6. Under **regler**, för **namn**, typ **Tillåt DNS**.
7. I fältet **Protokoll** väljer du **UDP**.
8. I fältet **Källadresser** skriver du **10.0.2.0/24**.
9. I fältet Måladress skriver du **209.244.0.3,209.244.0.4**
10. I fältet **Målportar** skriver du **53**.
11. Välj **Lägg till**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändra den primära och sekundära DNS-adressen för nätverksgränssnittet **Srv-Work**

Konfigurera serverns primära och sekundära DNS-adresser i testsyfte i den här självstudien. Det är inte ett allmänt Azure-brandvägg krav.

1. Gå till Azure Portal och öppna resursgruppen **Test-FW-RG**.
2. Välj nätverksgränssnitt för den **Srv-arbete** virtuell dator.
3. Under **inställningar**väljer **DNS-servrar**.
4. Under **DNS-servrar**väljer **anpassad**.
5. Skriv **209.244.0.3** i textrutan **Lägg till DNS-server** och **209.244.0.4** i nästa textruta.
6. Välj **Spara**.
7. Starta om den virtuella datorn **Srv-Work**.

## <a name="test-the-firewall"></a>Testa brandväggen

Testa nu brandväggen för att bekräfta att den fungerar som förväntat.

1. Öppna Azure Portal, granska nätverksinställningarna för den virtuella datorn **Srv-Work** och anteckna den privata IP-adressen.
2. Ansluta ett fjärrskrivbord till **Srv-Jump** virtuella datorn och logga in. Därifrån kan du öppna en fjärrskrivbordsanslutning till den **Srv-arbete** privat IP-adress.

3. Öppna Internet Explorer och navigera till https://www.google.com.
4. Välj **OK** > **Stäng** säkerhetsaviseringar Internet Explorer.

   Du bör se startsidan Google.

5. Bläddra till https://www.microsoft.com.

   Du bör blockeras av brandväggen.

Nu har du kontrollera att brandväggsreglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.
* Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **Test-FW-RG** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Monitor Azure Firewall-loggar](./tutorial-diagnostics.md)
