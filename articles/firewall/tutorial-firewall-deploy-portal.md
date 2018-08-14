---
title: Distribuera och konfigurera Azure Firewall via Azure Portal
description: I den här självstudien får du lära dig att distribuera och konfigurera Azure Firewall via Azure Portal.
services: firewall
author: vhorne
manager: jpconnock
ms.service: firewall
ms.topic: tutorial
ms.date: 7/11/2018
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 84696b4135570168f8093b15f9a2deb4790eeebe
ms.sourcegitcommit: fc5555a0250e3ef4914b077e017d30185b4a27e6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2018
ms.locfileid: "39480891"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Självstudie: Distribuera och konfigurera Azure Firewall via Azure Portal

Azure Firewall har två typer av regler som styr utgående åtkomst:

- **Programregler**

   Med de här reglerna kan du konfigurera fullständigt kvalificerade domännamn (FQDN) som kan nås från ett undernät. Du kan till exempel tillåta åtkomst till *github.com* från undernätet.
- **Nätverksregler**

   Med de här reglerna kan du konfigurera regler som innehåller källadress, protokoll, målport och måladress. Du kan till exempel skapa en regel som tillåter trafik via port 53 (DNS) till IP-adressen för DNS-servern från undernätet.

Nätverkstrafiken måste följa konfigurerade brandväggsregler när du vidarebefordrar den till brandväggen som standardgateway för undernätet.

Program- och nätverksregler lagras i *regelsamlingar*. En regelsamling är en lista med regler som delar samma åtgärd och prioritet.  En nätverksregelsamling är en lista med nätverksregler och en programregelsamling är en lista med programregler.

Nätverksregelsamlingar bearbetas alltid innan programregelsamlingar. Alla regler fungerar avslutande, så om en matchning hittas i en nätverksregelsamling bearbetas inte efterföljande programregelsamlingar under sessionen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * konfigurera en testnätverksmiljö
> * distribuera en brandvägg
> * skapa en standardväg
> * konfigurera programregler
> * konfigurera nätverksregler
> * Testa brandväggen



Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Exemplen i Azure Firewall-artikeln förutsätter att du redan har aktiverat den offentliga förhandsversionen av Azure Firewall. Mer information finns i [Aktivera den offentliga förhandsversionen av Azure Firewall](public-preview.md).

I den här självstudien skapar du ett virtuellt nätverk med tre undernät:
- **FW-SN** – brandväggen ligger i det här undernätet.
- **Workload-SN** – arbetsbelastningsservern ligger i det här undernätet. Det här undernätets nätverkstrafik går genom brandväggen.
- **Jump-SN** – ”hopp”-servern ligger i det här undernätet. Hoppservern har en offentlig IP-adress som du kan ansluta till via Fjärrskrivbord. Därifrån kan du sedan ansluta till arbetsbelastningsservern (via ett annat Fjärrskrivbord).

![Självstudie om nätverksinfrastruktur](media/tutorial-firewall-rules-portal/Tutorial_network.png)

I den här självstudien används en förenklad nätverkskonfiguration som är enkel att distribuera. I produktionsdistributioner rekommenderas en [modell med nav och ekrar](https://docs.microsoft.com/en-us/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) där brandväggen ligger i ett eget virtuellt nätverk, och arbetsbelastningsservrarna ligger i peerkopplade virtuella nätverk i samma region med ett eller flera undernät.



## <a name="set-up-the-network-environment"></a>Konfigurera nätverksmiljön
Skapa först en resursgrupp som ska innehålla de resurser som behövs till att distribuera brandväggen. Sedan skapa ett virtuellt nätverk, undernät och testservrar.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp
1. Logga in på Azure Portal på [http://portal.azure.com](http://portal.azure.com).
1. Klicka på startsidan för Azure Portal, klicka på **Resursgrupper** och sedan på **Lägg till**.
2. I fältet **Resursgruppsnamn** skriver du **Test-FW-RG**.
3. I fältet **Prenumeration** väljer du din prenumeration.
4. I fältet **Resursgruppsplats** väljer du en plats. Alla efterföljande resurser du skapar måste finnas på samma plats.
5. Klicka på **Skapa**.


### <a name="create-a-vnet"></a>Skapa ett virtuellt nätverk
1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Nätverk** klickar du på **Virtuella nätverk**.
3. Klicka på **Lägg till**.
4. I fältet **Namn** skriver du **Test-FW-VN**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
7. I fältet **Prenumeration** väljer du din prenumeration.
8. I fältet **Resursgrupp** väljer du **Använd befintlig** och väljer sedan **Test-FW-RG**.
9. Välj samma plats som tidigare i fältet **Plats**.
10. Under **Undernät**, i fältet **Namn**, skriver du **AzureFirewallSubnet**.

    Brandväggen kommer att ligga i det här undernätet, och namnet på undernätet **måste** vara AzureFirewallSubnet.
11. I fältet **Adressintervall** skriver du **10.0.1.0/24**.
12. Använd övriga standardinställningar och klicka på **Skapa**.

> [!NOTE]
> Den minsta storleken på undernätet AzureFirewallSubnet är /25.

### <a name="create-additional-subnets"></a>Skapa ytterligare undernät

Skapa sedan undernät för hoppservern och ett undernät för arbetsbelastningsservrarna.

1. På startsidan för Azure Portal klickar du på **Resursgrupper** och sedan på **Test-FW-RG**.
2. Klicka på det virtuella nätverket **Test-FW-VN**.
3. Klicka på **Undernät** och sedan på **+Undernät**.
4. I fältet **Namn** skriver du **Workload-SN**.
5. I fältet **Adressintervall** skriver du **10.0.2.0/24**.
6. Klicka på **OK**.

Skapa ett nytt undernät med namnet **Jump-SN** och adressintervallet **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Skapa virtuella datorer

Skapa nu de virtuella hopp- och arbetsbelastningsdatorerna och placera dem i respektive undernät.

1. Klicka på **Alla tjänster** på startsidan i Azure Portal.
2. Under **Compute** klickar du på **Virtuella datorer**.
3. Klicka på **Lägg till**, klicka på **Windows Server**, klicka på **Windows Server 2016 Datacenter** och sedan på **Skapa**.

**Grundläggande inställningar**

1. I fältet **Namn** skriver du **Srv-Jump**.
5. Ange ett användarnamn och lösenord.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. I fältet **Resursgrupp** klickar du på **Använd befintlig** och väljer sedan **Test-FW-RG**.
8. Välj samma plats som tidigare i fältet **Plats**.
9. Klicka på **OK**.

**Storlek**

1. Välj en lämplig storlek för den virtuella testdator som ska köra Windows Server. Exempelvis **B2ms** (8 GB RAM-minne, 16 GB lagring).
2. Klicka på **Välj**.

**Inställningar**

1. Under **Nätverk**, i fältet **Virtuellt nätverk**, väljer du **Test-FW-VN**.
2. I fältet **Undernät** väljer du **Jump-SN**.
3. I fältet **Välj offentliga inkommande portar** väljer du **RDP (3389)**. 

    Du vill förmodligen begränsa åtkomsten till din offentliga IP-adress, men du måste öppna port 3389 så att du kan ansluta ett fjärrskrivbord till hoppservern. 
2. Lämna övriga standardvärden och klicka på **OK**.

**Sammanfattning**

Granska sammanfattningen och klicka sedan på **Skapa**. Det här kan ta några minuter.

Upprepa proceduren och skapa en annan virtuell dator med namnet **Srv-Work**.

Använd informationen i följande tabell och konfigurera **inställningarna** för den virtuella datorn Srv-Work. Resten av konfigurationen är samma som för den virtuella datorn Srv-Jump.


|Inställning  |Värde  |
|---------|---------|
|Undernät|Workload-SN|
|Offentlig IP-adress|Ingen|
|Välj offentliga inkommande portar|Inga offentliga inkommande portar|


## <a name="deploy-the-firewall"></a>Distribuera brandväggen

1. På portalens startsida klickar du på **Skapa en resurs**.
2. Klicka på **Nätverk**, och sedan efter **Aktuella** klickar du på **Visa alla**.
3. Klicka på **Brandvägg** och sedan på **Skapa**. 
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
10. Klicka på **Undernät** och sedan på **Associera**.
11. Klicka på **Virtuellt nätverk** och välj sedan **Test-FW-VN**.
12. I fältet **Undernät** klickar du på **Workload-SN**.
13. Klicka på **OK**.
14. Klicka på **Vägar** och sedan på **Lägg till**.
15. I fältet **Vägnamn** skriver du **FW-DG**.
16. I fältet **Adressprefix** skriver du **0.0.0.0/0**.
17. I fältet **Nästa hopptyp** väljer du **Virtuell installation**.

    Azure Firewall är egentligen en hanterad tjänst, men en virtuell installation fungerar i det här fallet.
1. I fältet **Nästa hoppadress** skriver du brandväggens privata IP-adress som du skrev ned tidigare.
2. Klicka på **OK**.


## <a name="configure-application-rules"></a>konfigurera programregler


1. Öppna **Test-FW-RG** och klicka på brandväggen **Test-FW01**.
1. På sidan **Test-FW01**, under **Inställningar**, klickar du på **Regler**.
2. Klicka på **Lägg till programregelsamling**.
3. I fältet **Namn** skriver du **App-Coll01**.
1. I fältet **Prioritet** skriver du **200**.
2. I fältet **Åtgärd** väljer du **Tillåt**.

6. Under **Regler**, i fältet **Namn**, skriver du **AllowGH**.
7. I fältet **Källadresser** skriver du **10.0.2.0/24**.
8. I fältet **Protokoll: port** skriver du **http, https**. 
9. I fältet **Fullständiga domännamn för mål** skriver du **github.com**.
10. Klicka på **Lägg till**.

> [!NOTE]
> Azure Firewall innehåller en inbyggd regelsamling för fullständiga domännamn för mål (FQDN) i infrastrukturen som tillåts som standard. Dessa FQDN är specifika för plattformen och kan inte användas för andra ändamål. Tillåtna FQDN i infrastrukturen är:
>- Compute-åtkomst till PIR-lagring (Plattform Image Repository).
>- Åtkomst till lagring av hanterade diskars status.
>- Windows-diagnostik
>
> Du kan åsidosätta den här inbyggda regelsamlingen för infrastrukturen genom att skapa en programregelsamling som *nekar alla* och som bearbetats sist. Den bearbetas alltid före regelsamlingen för infrastrukturen. Allt som inte ingår i regelsamlingen för infrastrukturen nekas som standard.

## <a name="configure-network-rules"></a>Konfigurera nätverksregler

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


## <a name="test-the-firewall"></a>testa brandväggen.

1. Öppna Azure Portal, granska nätverksinställningarna för den virtuella datorn **Srv-Work** och anteckna den privata IP-adressen.
2. Anslut ett fjärrskrivbord till den virtuella datorn **Srv-Jump** och öppna därifrån en anslutning via Fjärrskrivbord till den privata IP-adressen för **Srv-Work**.

5. Öppna Internet Explorer och navigera till http://github.com.
6. Klicka på **OK** och sedan **Stäng** i säkerhetsvarningarna.

   Du bör se startsidan för GitHub.

7. Bläddra till http://www.msn.com.

   Du bör blockeras av brandväggen.

Nu har du verifierat att brandväggsreglerna fungerar:

- Du kan bläddra till en tillåten FQDN, men inte till andra.
- Du kan omvandla DNS-namn med hjälp av den konfigurerade externa DNS-servern.

## <a name="clean-up-resources"></a>Rensa resurser

Du kan behålla dina brandväggsresurser för nästa självstudie eller, om de inte längre behövs, så tar du bort resursgruppen **Test-FW-RG** för att ta bort alla brandväggsrelaterade resurser.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Konfigurera nätverket
> * Skapa en brandvägg
> * Skapa en standardväg
> * Konfigurera brandväggsregler för program och nätverk
> * Testa brandväggen

Därefter kan du övervaka Azure Firewall-loggarna.

> [!div class="nextstepaction"]
> [Självstudie: Övervaka Azure Firewall-loggar](./tutorial-diagnostics.md)
