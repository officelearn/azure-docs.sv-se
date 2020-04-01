---
title: 'Självstudiekurs: Distribuera & konfigurera Azure-brandväggen med Azure-portalen'
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall via Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 02/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 064fcf618914bca31ad9e7e60c76df8f599cd8bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239576"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kanske till exempel vill begränsa åtkomsten till webbplatser. Du kanske vill begränsa de utgående IP-adresser och portar som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här självstudien skapar du ett förenklat virtuellt nätverk med tre undernät för enkel distribution. För produktionsdistributioner rekommenderas en [hubb- och ekermodell.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Brandväggen är i sitt eget virtuella nätverk. Arbetsbelastningsservrarna finns i peer-virtuella nätverk i samma region med ett eller flera undernät.

* **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
* **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
* **Jump-SN** – ”hopp”-servern ligger i det här undernätet. Hoppservern har en offentlig IP-adress som du kan ansluta till via Fjärrskrivbord. Därifrån kan du sedan ansluta till arbetsbelastningsservern (via ett annat Fjärrskrivbord).

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-rules-portal/Tutorial_network.png)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera en programregel för att ge åtkomst till www.google.com
> * Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
> * testa brandväggen.

Om du vill kan du utföra den här självstudien med [Azure PowerShell](deploy-ps.md).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen innehåller alla resurser för den här självstudien.

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com).
2. På Azure-portalmenyn väljer du **Resursgrupper** eller söker efter och väljer *Resursgrupper* på valfri sida. Välj sedan **Lägg till**.
3. För **resursgruppsnamn**anger du *Test-FW-RG*.
4. I fältet **Prenumeration** väljer du din prenumeration.
5. I fältet **Resursgruppsplats** väljer du en plats. Alla andra resurser som du skapar måste finnas på samma plats.
6. Välj **Skapa**.

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Det här virtuella nätverket innehåller tre undernät.

> [!NOTE]
> Storleken på undernätet AzureFirewallSubnet är /26. Mer information om undernätsstorleken finns i [Vanliga frågor och svar om Azure-brandväggen](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
1. Välj **Nätverk virtuellt** > **nätverk**.
1. I fältet **Namn** skriver du **Test-FW-VN**.
1. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
1. I fältet **Prenumeration** väljer du din prenumeration.
1. För **resursgrupp**väljer du **Test-FW-RG**.
1. Välj samma plats som tidigare i fältet **Plats**.
1. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
1. För **adressintervall**skriver du **10.0.1.0/26**.
1. Acceptera de andra standardinställningarna och välj sedan **Skapa**.

### <a name="create-additional-subnets"></a>Skapa ytterligare undernät

Skapa sedan undernät för hoppservern och ett undernät för arbetsbelastningsservrarna.

1. På Azure-portalmenyn väljer du **Resursgrupper** eller söker efter och väljer *Resursgrupper* på valfri sida. Välj sedan **Test-FW-RG**.
2. Välj det virtuella nätverket **Test-FW-VN.**
3. Välj **undernät** > **+undernät**.
4. I fältet **Namn** skriver du **Workload-SN**.
5. I fältet **Adressintervall** skriver du **10.0.2.0/24**.
6. Välj **OK**.

Skapa ett nytt undernät med namnet **Jump-SN** och adressintervallet **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu de virtuella hopp- och arbetsbelastningsdatorerna och placera dem i respektive undernät.

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Välj **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

   |Inställning  |Värde  |
   |---------|---------|
   |Resursgrupp     |**Test-FW-RG**|
   |Namn på virtuell dator     |**Srv-Hoppa**|
   |Region     |Samma som tidigare|
   |Administratörens användarnamn     |**azureuser**|
   |lösenord     |**Azure123456!**|

4. Under Regler för **inkommande port**väljer du Tillåt markerade **portar**för **inkommande portar**.
5. I fältet **Välj inkommande portar** väljer du **RDP (3389)**.

6. Acceptera de andra standardinställningarna och välj **Nästa: Diskar**.
7. Acceptera diskinställningarna och välj **Nästa: Nätverk**.
8. Kontrollera att **Test-FW-VN** har valts som virtuellt nätverk och att undernätet är **Jump-SN**.
9. För **offentlig IP**godkänner du standardnamnet för den nya offentliga IP-adressen (Srv-Jump-ip).
11. Acceptera de andra standardinställningarna och välj **Nästa: Hantering**.
12. Välj **Av** för att inaktivera startdiagnostik. Acceptera de andra standardinställningarna och välj **Granska + skapa**.
13. Granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.

Använd informationen i följande tabell för att konfigurera en annan virtuell dator med namnet **Srv-Work**. Resten av konfigurationen är samma som för den virtuella datorn Srv-Jump.

|Inställning  |Värde  |
|---------|---------|
|Undernät|**Workload-SN**|
|Offentlig IP-adress|**Inget**|
|Offentliga inkommande portar|**Inget**|

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera brandväggen till det virtuella nätverket.

1. Välj **Skapa en resurs** på menyn i Microsoft Azure-portalen eller från **startsidan**.
2. Skriv **brandväggen** i sökrutan och tryck på **Retur**.
3. Välj **Brandvägg** och välj sedan **Skapa**.
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:

   |Inställning  |Värde  |
   |---------|---------|
   |Prenumeration     |\<din prenumeration\>|
   |Resursgrupp     |**Test-FW-RG** |
   |Namn     |**Test-FW01**|
   |Location     |Välj samma plats som tidigare|
   |Välj ett virtuellt nätverk     |**Använd befintliga**: **Test-FW-VN**|
   |Offentlig IP-adress     |**Lägg till nya**. Den offentliga IP-adressen måste vara Standard SKU-typen.|

5. Välj **Granska + skapa**.
6. Granska sammanfattningen och välj sedan **Skapa** för att skapa brandväggen.

   Distributionen kan ta några minuter.
7. När distributionen är klar går du till resursgruppen **Test-FW-RG** och väljer brandväggen **Test-FW01.**
8. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **Workload-SN** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. På Portalmenyn Azure väljer du **Alla tjänster** eller söker efter och väljer *Alla tjänster* på valfri sida.
2. Under **Nätverk**väljer du **Rutttabeller**.
3. Välj **Lägg till**.
4. I fältet **Namn** skriver du **Firewall-route**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. För **resursgrupp**väljer du **Test-FW-RG**.
7. Välj samma plats som tidigare i fältet **Plats**.
8. Välj **Skapa**.
9. Välj **Uppdatera**och välj sedan rutttabellen **Brandvägg-rutt.**
10. Välj **Undernät** och välj sedan **Associera**.
11. Välj **Virtuellt nätverk** > **Test-FW-VN**.
12. För **Undernät**väljer du **Workload-SN**. Se till att du bara väljer **arbetsbelastning-SN-undernätet** för den här vägen, annars fungerar inte brandväggen korrekt.

13. Välj **OK**.
14. Välj **Rutter** och välj sedan **Lägg till**.
15. För **Ruttnamn**skriver du **fw-dg**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Välj **OK**.

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Det här är programregeln som tillåter utgående åtkomst till www.google.com.

1. Öppna **Test-FW-RG**och välj **test-FW01-brandväggen.**
2. Välj **Regler**under **Inställningar**på sidan **Test-FW01** .
3. Välj fliken **Samling programregel.**
4. Välj **Lägg till programregelsamling**.
5. I fältet **Namn** skriver du **App-Coll01**.
6. I fältet **Prioritet** skriver du **200**.
7. I fältet **Åtgärd** väljer du **Tillåt**.
8. Skriv **Tillåt-Google**för **namn**under **Regler**, **Target FQDNs**.
9. För **källtyp**väljer du **IP-adress**.
10. För **Källa**skriver du **10.0.2.0/24**.
11. I fältet **Protokoll: port** skriver du **http, https**.
12. För **Mål FQDNS**skriver **du www.google.com**
13. Välj **Lägg till**.

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Det här är nätverksregel som tillåter utgående åtkomst till två IP-adresser på port 53 (DNS).

1. Välj fliken **Samling av nätverksregel.**
2. Välj **Lägg till samling av nätverksregel**.
3. I fältet **Namn** skriver du **Net-Coll01**.
4. I fältet **Prioritet** skriver du **200**.
5. I fältet **Åtgärd** väljer du **Tillåt**.
6. Skriv **Tillåt-DNS**för namn under **Regler**, **IP-adresser**för **Namn**.
7. I fältet **Protokoll** väljer du **UDP**.
9. För **källtyp**väljer du **IP-adress**.
1. För **Källa**skriver du **10.0.2.0/24**.
2. För **Måladress**, typ **209.244.0.3,209.244.0.4**

   Dessa är offentliga DNS-servrar som drivs av CenturyLink.
1. I fältet **Målportar** skriver du **53**.
2. Välj **Lägg till**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändra den primära och sekundära DNS-adressen för nätverksgränssnittet **Srv-Work**

Konfigurera serverns primära och sekundära DNS-adresser för teständamål i den här självstudien. Detta är inte ett allmänt Azure-brandväggskrav.

1. På Azure-portalmenyn väljer du **Resursgrupper** eller söker efter och väljer *Resursgrupper* på valfri sida. Välj resursgruppen **Test-FW-RG.**
2. Välj nätverksgränssnitt för den virtuella **datorn Srv-Work.**
3. Under **Inställningar**väljer du **DNS-servrar**.
4. Under **DNS-servrar**väljer du **Anpassad**.
5. Skriv **209.244.0.3** i textrutan **Lägg till DNS-server** och **209.244.0.4** i nästa textruta.
6. Välj **Spara**.
7. Starta om den virtuella datorn **Srv-Work**.

## <a name="test-the-firewall"></a>testa brandväggen.

Testa nu brandväggen för att bekräfta att den fungerar som förväntat.

1. Öppna Azure Portal, granska nätverksinställningarna för den virtuella datorn **Srv-Work** och anteckna den privata IP-adressen.
2. Anslut ett fjärrskrivbord till den virtuella datorn **Srv-Jump** och logga in. Därifrån öppnar du en fjärrskrivbordsanslutning till den privata **IP-adressen Srv-Work.**
3. Öppna Internet Explorer och navigera till https://www.google.com.
4. Välj **OK** > **Stäng** på säkerhetsaviseringarna i Internet Explorer.

   Du bör se Googles hemsida.

5. Bläddra till https://www.microsoft.com.

   Du bör blockeras av brandväggen.

Så nu har du verifierat att brandväggsreglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.
* Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **Test-FW-RG** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
