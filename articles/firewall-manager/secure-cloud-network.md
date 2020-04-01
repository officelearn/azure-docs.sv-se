---
title: 'Självstudiekurs: Säkra ditt virtuella WAN med förhandsversionen av Azure Firewall Manager'
description: I den här självstudien får du lära dig hur du skyddar ditt virtuella WAN med Azure Firewall Manager med Azure-portalen.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77443065"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Självstudiekurs: Säkra ditt virtuella WAN med förhandsversionen av Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Med hjälp av förhandsversionen av Azure Firewall Manager kan du skapa säkra virtuella hubbar för att skydda din molnnätverkstrafik som är avsedd för privata IP-adresser, Azure PaaS och Internet. Trafikroutning till brandväggen är automatiserad, så du behöver inte skapa användardefinierade vägar (UDRs).

![skydda molnnätverket](media/secure-cloud-network/secure-cloud-network.png)

Firewall Manager stöder också en virtuell hubbarkitektur för nätverk. En jämförelse av de skyddade virtuella hubben och hubbarkitekturtyperna för virtuella nätverk finns i [Vilka är arkitekturalternativen för Azure Firewall Manager?](vhubs-and-vnets.md)

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa det virtuella ekernätverket
> * Skapa ett säkert virtuellt nav
> * Ansluta navet och eker-virtuella nätverk
> * Skapa en brandväggsprincip och skydda ditt nav
> * Dirigera trafik till navet
> * testa brandväggen.

## <a name="create-a-hub-and-spoke-architecture"></a>Skapa en hubb- och ekerarkitektur

Skapa först ett eker-VNet där du kan placera servrarna.

### <a name="create-a-spoke-vnet-and-subnets"></a>Skapa ett eker-nätverk och undernät

1. På startsidan för Azure-portalen väljer du **Skapa en resurs**.
2. Under **Nätverk**väljer du **Virtuellt nätverk**.
4. För **Namn**, skriv **Spoke-01**.
5. I fältet **Adressutrymme** skriver du **10.0.0.0/16**.
6. I fältet **Prenumeration** väljer du din prenumeration.
7. För **resursgrupp**väljer du **Skapa ny**och skriver **FW-Manager** för namnet och väljer **OK**.
8. För **Plats**väljer du **(USA) östra USA**.
9. Under **Undernät**för **Namntyp** **Arbetsbelastning-SN**.
10. I fältet **Adressintervall** skriver du **10.0.1.0/24**.
11. Acceptera de andra standardinställningarna och välj sedan **Skapa**.

Skapa sedan ett undernät för en hoppserver.

1. På startsidan för Azure-portalen väljer du **Resursgrupper** > **FW-Manager**.
2. Välj det virtuella nätverket **Spoke-01.**
3. Välj **undernät** > **+undernät**.
4. För **Namn**skriver du **Jump-SN**.
5. I fältet **Adressintervall** skriver du **10.0.2.0/24**.
6. Välj **OK**.

### <a name="create-the-secured-virtual-hub"></a>Skapa det skyddade virtuella navet

Skapa din säkra virtuella hubb med hjälp av Firewall Manager.

1. På startsidan för Azure-portalen väljer du **Alla tjänster**.
2. Skriv **Brandväggshanteraren** i sökrutan och välj **Brandväggshanteraren**.
3. På sidan **Brandväggshanteraren** väljer du **Skapa en säker virtuell hubb**.
4. På sidan **Skapa nya säkra virtuella hubb** väljer du din prenumeration och **resursgruppen FW-Manager.**
5. Skriv **Hub-01**för det **skyddade virtuella navnamnet**.
6. För **Plats**väljer du **Östra USA**.
7. För **Hub-adressutrymme**skriver du **10.1.0.0/16**.
8. För det nya vWAN-namnet skriver du **vwan-01**.
9. Lämna kryssrutan **Inkludera VPN-gateway för att aktivera betrodda säkerhetspartner** avmarkerad.
10. Välj **Nästa:Azure-brandvägg**.
11. Acceptera standardinställningen **för Azure-brandväggen** **aktiverad** och välj sedan **Nästa: Betrodd säkerhetspartner**.
12. Acceptera standardinställningen **Betrodd säkerhetspartner** **inaktiverad** och välj **Nästa: Granska + skapa**.
13. Välj **Skapa**. Det tar ungefär 30 minuter att distribuera.

### <a name="connect-the-hub-and-spoke-vnets"></a>Ansluta navet och eker-virtuella nätverk

Nu kan du peer hubben och eker virtuella nätverk.

1. Välj **resursgruppen FW-Manager** och välj sedan det virtuella **WAN-wan:et vwan-01.**
2. Under **Anslutning**väljer du **Virtuella nätverksanslutningar**.
3. Välj **Lägg till anslutning**.
4. För **Anslutningsnamn**skriver du **hub-spoke**.
5. För **Hubbar**väljer du **Hub-01**.
6. För **virtuellt nätverk**väljer du **Spoke-01**.
7. Välj **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Skapa en brandväggsprincip och skydda ditt nav

En brandväggsprincip definierar samlingar av regler för att dirigera trafik på en eller flera säkra virtuella hubbar. Du skapar brandväggsprincipen och skyddar sedan navet.

1. Välj **Skapa en Azure-brandväggsprincip**från Brandväggshanteraren .
2. Välj din prenumeration och välj sedan **resursgruppen FW-Manager.**
3. Under **Policyinformation** **väljer** du Östra **USA**för **namntypen Policy-01** och region . **Name**
4. Välj **Nästa:Regler**.
5. Välj Lägg till **en regelsamling**på fliken **Regler** .
6. Skriv **RC-01** för **namnet**på sidan **Lägg till en regelsamling** .
7. För **regelsamlingstyp**väljer du **Program**.
8. För **Prioritet**skriver du **100**.
9. Kontrollera **att regelsamlingsåtgärden** är **Tillåt**.
10. För regeln **Namntyp** **Allow-msft**.
11. För **källadress** **\*** skriver du .
12. För **protokoll**skriver du **http,https.**
13. Kontrollera ** Måltyp är **FQDN**.
14. För **Mål**skriver du ** \*.microsoft.com**.
15. Välj **Lägg till**.
16. Välj **Nästa: Säkrade virtuella hubbar**.
17. Välj **Hub-01**på fliken **Säkra virtuella hubbar** .
19. Välj **Granska + skapa**.
20. Välj **Skapa**.

Detta kan ta ungefär fem minuter eller mer att slutföra.

## <a name="route-traffic-to-your-hub"></a>Dirigera trafik till navet

Nu måste du se till att nätverkstrafiken dirigeras till genom brandväggen.

1. Välj Säkra virtuella hubbar från **Brandväggshanteraren**.
2. Välj **Hub-01**.
3. Under **Inställningar**väljer du **Ruttinställningar**.
4. Under **Internettrafik**väljer **trafik från virtuella nätverk**, Skicka via **Azure-brandväggen**.
5. Under **Privat Azure-trafik**väljer **du Skicka** **via Azure-brandväggen**.
6. Välj **Redigera IP-adressprefix(er)**.
7. Välj **Lägg till ett IP-adressprefix**.
8. Skriv **10.0.1.0/24** som adress till undernätet Arbetsbelastning och välj **Spara**.
9. Under **Inställningar**väljer du **Anslutningar**.
10. Välj **hub-spoke-anslutningen** och välj sedan **Säker internettrafik** och välj sedan **OK**.


## <a name="test-your-firewall"></a>Testa brandväggen

Om du vill testa brandväggsreglerna måste du distribuera ett par servrar. Du ska distribuera Workload-Srv i undernätet Arbetsbelastning-SN för att testa brandväggsreglerna och Jump-Srv så att du kan använda Fjärrskrivbord för att ansluta från Internet och sedan ansluta till Workload-Srv.

### <a name="deploy-the-servers"></a>Distribuera servrarna

1. Välj **Skapa en resurs** på Azure-portalen.
2. Välj **Windows Server 2016 Datacenter** i listan **Populär.**
3. Ange följande värden för den virtuella datorn:

   |Inställning  |Värde  |
   |---------|---------|
   |Resursgrupp     |**FW-Chef**|
   |Namn på virtuell dator     |**Hoppa-Srv**|
   |Region     |**Jag är mycket bra på att vi måste ta tid på Östra USA)**|
   |Administratörens användarnamn     |**azureuser**|
   |lösenord     |ange ditt lösenord|

4. Under Regler för **inkommande port**väljer du Tillåt markerade **portar**för **inkommande portar**.
5. I fältet **Välj inkommande portar** väljer du **RDP (3389)**.

6. Acceptera de andra standardinställningarna och välj **Nästa: Diskar**.
7. Acceptera diskinställningarna och välj **Nästa: Nätverk**.
8. Kontrollera att **Spoke-01** är markerat för det virtuella nätverket och att undernätet är **Jump-SN**.
9. För **offentlig IP**godkänner du det nya offentliga ip-adressnamnet (Jump-Srv-ip).
11. Acceptera de andra standardinställningarna och välj **Nästa: Hantering**.
12. Välj **Av** för att inaktivera startdiagnostik. Acceptera de andra standardinställningarna och välj **Granska + skapa**.
13. Granska inställningarna på sammanfattningssidan och välj sedan **Skapa**.

Använd informationen i följande tabell för att konfigurera en annan virtuell dator med namnet **Workload-Srv**. Resten av konfigurationen är samma som för den virtuella datorn Srv-Jump.

|Inställning  |Värde  |
|---------|---------|
|Undernät|**Workload-SN**|
|Offentlig IP-adress|**Inget**|
|Offentliga inkommande portar|**Inget**|

### <a name="add-a-route-table-and-default-route"></a>Lägga till en flödestabell och standardväg

Om du vill tillåta en Internetanslutning till Jump-Srv måste du skapa en vägtabell och en standardgatewayväg till Internet från **Jump-SN-undernätet.**

1. Välj **Skapa en resurs** på Azure-portalen.
2. Skriv **flödestabell** i sökrutan och välj sedan **Rutttabell**.
3. Välj **Skapa**.
4. Skriv **RT-01** för **Namn**.
5. Välj din prenumeration, **FW-Manager** för resursgruppen och **(USA) östra USA** för regionen.
6. Välj **Skapa**.
7. När distributionen är klar väljer du vägtabellen **RT-01.**
8. Välj **Rutter** och välj sedan **Lägg till**.
9. Skriv **hoppa-till-inet** för **routenamnet**.
10. Skriv **0.0.0.0/0** för **adressprefixet**.
11. Välj **Internet** för **typen Nästa hopp**.
12. Välj **OK**.
13. När distributionen är klar väljer du **Undernät**och väljer sedan **Associera**.
14. Välj **Spoke-01** för **virtuellt nätverk**.
15. Välj **Jump-SN** för **undernät**.
16. Välj **OK**.

### <a name="test-the-rules"></a>Testa reglerna

Testa nu brandväggsreglerna för att bekräfta att det fungerar som förväntat.

1. Granska nätverksinställningarna för den virtuella **datorn arbetsbelastning-Srv** från Azure-portalen och notera den privata IP-adressen.
2. Anslut ett fjärrskrivbord till **virtuell Jump-Srv-dator** och logga in. Därifrån öppnar du en fjärrskrivbordsanslutning till den privata **IP-adressen Workload-Srv.**

3. Öppna Internet Explorer och navigera till https://www.microsoft.com.
4. Välj **OK** > **Stäng** på säkerhetsaviseringarna i Internet Explorer.

   Du bör se Microsofts startsida.

5. Bläddra till https://www.google.com.

   Du bör blockeras av brandväggen.

Så nu har du verifierat att brandväggsreglerna fungerar:

* Du kan bläddra till en tillåten FQDN, men inte till andra.



## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om betrodda säkerhetspartner](trusted-security-partners.md)
