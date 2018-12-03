---
title: 'Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal'
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall via Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 11/15/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 179a13d6fbb162ae7727c6a176b60879901dc4d1
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426194"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal

En viktig del av en övergripande säkerhetsplan för nätverket är att kontrollera utgående nätverksåtkomst. Du kanske till exempel vill begränsa åtkomst till webbplatser eller de utgående IP-adresser och portar som kan nås.

Med Azure Firewall kan du kontrollera åtkomsten till utgående nätverk från ett Azure-undernät. Med Azure Firewall kan du konfigurera:

* Programreglerna som definierar fullständigt kvalificerade domännamn (FQDN) kan nås från ett undernät.
* Nätverksregler som definierar källadress, protokoll, målport och måladress.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

I den här självstudien skapar du ett förenklat virtuellt nätverk med tre undernät för enkel distribution. I produktionsdistributioner rekommenderas en [modell med nav och ekrar](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) där brandväggen ligger i ett eget virtuellt nätverk, och arbetsbelastningsservrarna ligger i peerkopplade virtuella nätverk i samma region med ett eller flera undernät.

- **AzureFirewallSubnet** – brandväggen ligger i det här undernätet.
- **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
- **Jump-SN** – ”hopp”-servern ligger i det här undernätet. Hoppservern har en offentlig IP-adress som du kan ansluta till via Fjärrskrivbord. Därifrån kan du sedan ansluta till arbetsbelastningsservern (via ett annat Fjärrskrivbord).

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-rules-portal/Tutorial_network.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * Skapa en standardväg
> * Konfigurera ett program för att tillåta åtkomst till github.com
> * Konfigurera en nätverksregel för att tillåta åtkomst till externa DNS-servrar
> * Testa brandväggen

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="set-up-the-network"></a>Konfigurera nätverket

Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Resursgruppen innehåller alla resurser för den här självstudien.

1. Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).
2. På startsidan för Azure-portalen klickar du på **Resursgrupper** > **Lägg till**.
3. I fältet **Resursgruppsnamn** skriver du **Test-FW-RG**.
4. I fältet **Prenumeration** väljer du din prenumeration.
5. I fältet **Resursgruppsplats** väljer du en plats. Alla efterföljande resurser du skapar måste finnas på samma plats.
6. Klicka på **Skapa**.

### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk

Det här virtuella nätverket innehåller tre undernät.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Virtuella nätverk**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** skriver du **Test-FW-VN**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resursgrupp** väljer du **använd befintlig** > **Test-FW-RG**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**. Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
10. I fältet **Adressintervall** skriver du **10.0.1.0/24**.
11. Använd övriga standardinställningar och klicka på **Skapa**.

> [!NOTE]
> Den minsta storleken på AzureFirewallSubnet-undernätet är /26.

### <a name="create-additional-subnets"></a>Skapa ytterligare undernät

Skapa sedan undernät för hoppservern och ett undernät för arbetsbelastningsservrarna.

1. På startsidan för Azure-portalen klickar du på **Resursgrupper** > **Test-FW-RG**.
2. Klicka på det virtuella nätverket **Test-FW-VN**.
3. Klicka på **Undernät** > **+Undernät**.
4. I fältet **Namn** skriver du **Workload-SN**.
5. I fältet **Adressintervall** skriver du **10.0.2.0/24**.
6. Klicka på **OK**.

Skapa ett nytt undernät med namnet **Jump-SN** och adressintervallet **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu de virtuella hopp- och arbetsbelastningsdatorerna och placera dem i respektive undernät.

1. Klicka på **Skapa en resurs** i Azure Portal.
2. Klicka på **Compute** och sedan **Windows Server 2016 Datacenter** i listan Aktuella.
3. Ange följande värden för den virtuella datorn:

    - *Test-FW-RG* som resursgrupp.
    - *Srv-Jump* – som namn på den virtuella datorn.
    - *azureuser* – för administratörens användarnamn.
    - *Azure123456!* som lösenord.

4. Under **Regler för inkommande portar**, i fältet **Offentliga inkommande portar** klickar du på **Tillåt valda portar**.
5. I fältet **Välj inkommande portar** väljer du **RDP (3389)**.

6. Acceptera de andra standardvärdena och klicka på **Nästa: Diskar**.
7. Acceptera diskstandardvärdet och klicka på **Nästa: Nätverk**.
8. Kontrollera att **Test-FW-VN** har valts som virtuellt nätverk och att undernätet är **Jump-SN**.
9. I fältet **Offentlig IP-adress** klickar du på **Skapa ny**.
10. Skriv **Srv-Jump-PIP** som namn på den offentliga IP-adressen och klicka på **OK**.
11. Acceptera de andra standardvärdena och klicka på **Nästa: Hantering**.
12. Klicka på **Av** för att inaktivera startdiagnostik. Acceptera de andra standardvärdena och klicka på **Granska + skapa**.
13. Granska inställningarna på sammanfattningssidan och klicka sedan på **Skapa**.

Upprepa proceduren och skapa en annan virtuell dator med namnet **Srv-Work**.

Använd informationen i följande tabell och konfigurera den virtuella datorn Srv-Work. Resten av konfigurationen är samma som för den virtuella datorn Srv-Jump.

|Inställning  |Värde  |
|---------|---------|
|Undernät|Workload-SN|
|Offentlig IP-adress|Ingen|
|Offentliga inkommande portar|Ingen|

## <a name="deploy-the-firewall"></a>Distribuera brandväggen

Distribuera brandväggen till det virtuella nätverket.

1. På portalens startsida klickar du på **Skapa en resurs**.
2. Klicka på **Nätverk**, och sedan efter **Aktuella** klickar du på **Visa alla**.
3. Klicka på **Brandvägg** > **Skapa**. 
4. På sidan **Skapa en brandvägg** använder du följande tabell till att konfigurera brandväggen:
   
   |Inställning  |Värde  |
   |---------|---------|
   |Namn     |Test-FW01|
   |Prenumeration     |\<din prenumeration\>|
   |Resursgrupp     |**Använd befintlig**: Test-FW-RG |
   |Plats     |Välj samma plats som tidigare|
   |Välj ett virtuellt nätverk     |**Använd befintligt**: Test-FW-VN|
   |Offentlig IP-adress     |**Skapa ny**. Den offentliga IP-adressen måste vara Standard SKU-typen.|

2. Klicka på **Granska + skapa**.
3. Granska sammanfattningen och klicka sedan på **Skapa** för att skapa brandväggen.

   Distributionen kan ta några minuter.
4. När distributionen är färdig öppnar du resursgruppen **Test-FW-RG** och klickar på brandväggen **Test FW01**.
6. Skriv ned den privata IP-adressen. Du kommer att använda den senare när du skapar standardvägen.

## <a name="create-a-default-route"></a>Skapa en standardväg

För undernätet **Workload-SN** ställer du in att den utgående standardvägen ska gå via brandväggen.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Routningstabeller**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** skriver du **Firewall-route**.
5. I fältet **Prenumeration** väljer du din prenumeration.
6. I fältet **Resursgrupp** väljer du **Använd befintlig** och sedan **Test-FW-RG**.
7. Välj samma plats som tidigare i fältet **Plats**.
8. Klicka på **Skapa**.
9. Klicka på **Uppdatera** och klicka sedan på routningstabellen **Firewall-route**.
10. Klicka på **Undernät** > **Associera**.
11. Klicka på **Virtuellt nätverk** > **Test-FW-VN**.
12. I fältet **Undernät** klickar du på **Workload-SN**. Se till att du bara väljer **Workload-SN**-undernätet för den här vägen, annars fungerar inte brandväggen som den ska.

13. Klicka på **OK**.
14. Klicka på **Vägar** > **Lägg till**.
15. I fältet **Vägnamn** skriver du **FW-DG**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
18. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
19. Klicka på **OK**.

## <a name="configure-an-application-rule"></a>Konfigurera en programregel

Det här är den programregel som tillåter utgående åtkomst till github.com.

1. Öppna **Test-FW-RG** och klicka på brandväggen **Test-FW01**.
2. På sidan **Test-FW01**, under **Inställningar**, klickar du på **Regler**.
3. Klicka på fliken **Programregelsamling**.
4. Klicka på **Lägg till programregelsamling**.
5. I fältet **Namn** skriver du **App-Coll01**.
6. I fältet **Prioritet** skriver du **200**.
7. I fältet **Åtgärd** väljer du **Tillåt**.
8. Under **Regler**, **Fullständiga domännamn för mål**, i fältet **Namn** skriver du **AllowGH**.
9. I fältet **Källadresser** skriver du **10.0.2.0/24**.
10. I fältet **Protokoll: port** skriver du **http, https**.
11. I fältet **Fullständiga domännamn för mål** skriver du **github.com**.
12. Klicka på **Lägg till**.

Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Mer information finns i [Infrastruktur-FQDN](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurera en nätverksregel

Det här är nätverksregel som tillåter utgående åtkomst till två IP-adresser på port 53 (DNS).

1. Klicka på fliken **Nätverksregelsamling**.
1. Klicka på **Lägg till nätverksregelsamling**.
2. I fältet **Namn** skriver du **Net-Coll01**.
3. I fältet **Prioritet** skriver du **200**.
4. I fältet **Åtgärd** väljer du **Tillåt**.

6. Under **Regler**, i fältet **Namn**, skriver du **AllowDNS**.
8. I fältet **Protokoll** väljer du **UDP**.
9. I fältet **Källadresser** skriver du **10.0.2.0/24**.
10. I fältet Måladress skriver du **209.244.0.3,209.244.0.4**
11. I fältet **Målportar** skriver du **53**.
12. Klicka på **Lägg till**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Ändra den primära och sekundära DNS-adressen för nätverksgränssnittet **Srv-Work**

För testningen i den här självstudien konfigurerar du en primär och sekundär DNS-adress. Detta är inte ett allmänt krav i Azure Firewall.

1. Gå till Azure Portal och öppna resursgruppen **Test-FW-RG**.
2. Klicka på nätverksgränssnittet för den virtuella datorn **Srv-Work**.
3. Under **Inställningar** klickar du på **DNS-servrar**.
4. Under **DNS-servrar** klickar du på **Anpassad**.
5. Skriv **209.244.0.3** i textrutan **Lägg till DNS-server** och **209.244.0.4** i nästa textruta.
6. Klicka på **Spara**. 
7. Starta om den virtuella datorn **Srv-Work**.

## <a name="test-the-firewall"></a>Testa brandväggen

Nu ska du testa brandväggen för att bekräfta att den fungerar som förväntat.

1. Öppna Azure Portal, granska nätverksinställningarna för den virtuella datorn **Srv-Work** och anteckna den privata IP-adressen.
2. Anslut ett fjärrskrivbord till den virtuella datorn **Srv-Jump** och öppna därifrån en anslutning via Fjärrskrivbord till den privata IP-adressen för **Srv-Work**.

5. Öppna Internet Explorer och navigera till http://github.com.
6. Klicka på **OK** > **Stäng** i säkerhetsvarningarna.

   Du bör se startsidan för GitHub.

7. Bläddra till http://www.msn.com.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brandväggsreglerna fungerar:

- Du kan bläddra till en tillåten FQDN, men inte till andra.
- Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **Test-FW-RG** för att ta bort alla brandväggsrelaterade resurser.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
