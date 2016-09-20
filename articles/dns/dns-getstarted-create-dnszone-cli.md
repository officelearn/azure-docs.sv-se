<properties
   pageTitle="Skapa en DNS-zon med CLI| Microsoft Azure"
   description="Lär dig hur du skapar DNS-zoner för Azure DNS steg för steg för att lagra din DNS-domän med hjälp av CLI"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/16/2016"
   ms.author="cherylmc"/>

# Skapa en Azure DNS-zon med CLI


> [AZURE.SELECTOR]
- [Azure Portal](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)


Den här artikeln beskriver steg för steg hur du skapar en DNS-zon med hjälp av CLI. Du kan också skapa en DNS-zon med hjälp av PowerShell- eller Azure-portalen. 

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)] 


## Innan du börjar

I dessa anvisningar används Microsoft Azure CLI. Se till att uppdatera till den senaste versionen av Azure CLI (0.9.8 eller senare) så att du kan använda Azure DNS-kommandona. Skriv `azure -v` för att kontrollera vilken version av Azure CLI som är installerad på datorn.

## Steg 1 – Konfigurera Azure CLI

### 1. Installera Azure CLI

Du kan installera Azure CLI för Windows, Linux eller Mac. Du måste utföra följande steg innan du kan hantera Azure DNS med hjälp av Azure CLI. Mer information finns på [Installera Azure CLI](../xplat-cli-install.md). DNS-kommandona kräver Azure CLI version 0.9.8 eller senare.

Du kan hitta alla nätverksproviderkommandon i CLI med följande kommando:

    Azure network

### 2. Växla CLI-läge

Azure DNS använder Azure Resource Manager. Se till att du byter CLI-läge så att du kan använda ARM-kommandon.

    Azure config mode arm

### 3. Logga in på ditt Azure-konto

Du uppmanas att autentisera dig med dina autentiseringsuppgifter. Tänk på att du bara kan använda ORGID-konton.

    Azure login -u "username"

### 4. Välja prenumerationen

Välj vilka av dina Azure-prenumerationer som du vill använda.

    Azure account set "subscription name"

### 5. Skapa en resursgrupp

Azure Resource Manager kräver att alla resursgrupper anger en plats. Detta används som standardplatsen för resurser i resursgruppen. Men eftersom alla DNS-resurser är globala, inte regionala, så påverkar inte valet av resursgruppens plats Azure DNS.

Du kan hoppa över det här steget om du använder en befintlig resursgrupp. 

    Azure group create -n myresourcegroup --location "West US"


### 6. Registrera dig

Azure DNS-tjänsten hanteras av Microsoft.Network-resursprovidern. Din Azure-prenumeration måste vara registrerad att använda den här resursprovidern innan du kan använda Azure DNS. Det här är en engångsåtgärd för varje prenumeration.

    Azure provider register --namespace Microsoft.Network


## Steg 2 – Skapa en DNS-zon

En DNS-zon skapas med hjälp av kommandot `azure network dns zone create`. Om du vill kan du också skapa en DNS-zon tillsammans med taggar. Taggar är en lista över namn/värde-par och används av Azure Resource Manager för att märka resurser för fakturerings- eller grupperingsändamål. Mer information om taggar finns i [Ordna dina Azure-resurser med hjälp av taggar](../resource-group-using-tags.md). 

I Azure DNS ska zonnamn anges utan avslutande **”.”**. Till exempel ”**contoso.com**” i stället för ”**contoso.com.**”.


### Så här skapar du en DNS-zon

Exemplet nedan skapar en DNS-zon med namnet *contoso.com* i resursgruppen med namnet *MyResourceGroup*. 

Använd exemplet för att skapa DNS-zonen och ersätt värdena med dina egna.

    Azure network dns zone create myresourcegroup contoso.com

### Så här skapar du en DNS-zon och taggar

Azure DNS CLI stöder taggar för DNS-zoner som angetts med hjälp av den valfria parametern *-Tag*. Följande exempel visar hur du skapar en DNS-zon med två taggar, project = demo och env = test.

Använd exemplet nedan för att skapa en DNS-zon och taggar och ersätt värdena med dina egna.

    Azure network dns zone create myresourcegroup contoso.com -t "project=demo";"env=test"

## Visa poster

När du skapar en DNS-zon skapas även följande DNS-poster:

- SOA-posten (”Start of Authority”). Den här posten finns i roten på varje DNS-zon.

- Posterna för auktoritativa namnservrar (NS). Dessa poster visar vilka namnservrar som är värdar för zonen. Azure DNS använder en pool med namnservrar, vilket innebär att namnservrar kan tilldelas till olika zoner i Azure DNS. Mer information finns i [Delegera en domän till Azure DNS](dns-domain-delegation.md).

Du kan visa dessa poster genom att använda `azure network dns-record-set show`.<BR>
*Användning: network dns record-set show <resource-group> <dns-zone-name> <name> <type>*


Om du i exemplet nedan kör kommandot med resursgruppen *myresourcegroup*, postuppsättningen *”@”* (för en rotpost) och typen *SOA* returneras följande resultat:


    azure network dns record-set show myresourcegroup "contoso.com" "@" SOA
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/SOA/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/SOA
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    SOA record:
    data:      Email                         : msnhst.microsoft.com
    data:      Expire time                   : 604800
    data:      Host                          : edge1.azuredns-cloud.net
    data:      Minimum TTL                   : 300
    data:      Refresh time                  : 900
    data:      Retry time                    : 300
    data:                                    :
<BR>
Om du vill visa NS-posterna som skapats med zonen använder du följande kommando:

    azure network dns record-set show myresourcegroup "contoso.com" "@" NS
    info:    Executing command network dns-record-set show
    + Looking up the DNS record set "@"
    data:    Id                              : /subscriptions/#######################/resourceGroups/myresourcegroup/providers/Microsoft.Network/dnszones/contoso.com/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 3600
    data:    NS records
    data:        Name server domain name     : ns1-05.azure-dns.com
    data:        Name server domain name     : ns2-05.azure-dns.net
    data:        Name server domain name     : ns3-05.azure-dns.org
    data:        Name server domain name     : ns4-05.azure-dns.info
    data:
    info:    network dns-record-set show command OK

>[AZURE.NOTE] Postuppsättningar i roten (eller *toppen*) av en DNS-zon använder **@** som postuppsättningsnamn.

## Testa

Du kan testa din DNS-zon med hjälp av DNS-verktyg som nslookup och DIG eller med PowerShell-cmdleten `Resolve-DnsName`.

Om du inte har delegerat domänen att använda den nya zonen i Azure DNS än, så måste du dirigera DNS-frågan direkt till en av namnservrarna för zonen. Namnservrarna för zonen anges i NS-posterna från ”azure network dns record-set show” ovan. Var noga med att ersätta värdena för din zon i kommandot nedan.

I följande exempel används DIG för att fråga domänen contoso.com med hjälp av namnservrarna som tilldelats DNS-zonen. Frågan måste peka på en namnserver för vilken vi använde *@<name server for the zone>* och zonnamnet med hjälp av DIG.

     <<>> DiG 9.10.2-P2 <<>> @ns1-05.azure-dns.com contoso.com
    (1 server found)
    global options: +cmd
    Got answer:
    ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 60963
    flags: qr aa rd; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1
    WARNING: recursion requested but not available

    OPT PSEUDOSECTION:
    EDNS: version: 0, flags:; udp: 4000
    QUESTION SECTION:
    contoso.com.                        IN      A

    AUTHORITY SECTION:
    contoso.com.         300     IN      SOA     edge1.azuredns-cloud.net.
    msnhst.microsoft.com. 6 900 300 604800 300

    Query time: 93 msec
    SERVER: 208.76.47.5#53(208.76.47.5)
    WHEN: Tue Jul 21 16:04:51 Pacific Daylight Time 2015
    MSG SIZE  rcvd: 120

## Nästa steg

När du har skapat en DNS-zon skapar du [postuppsättningar och poster](dns-getstarted-create-recordset-cli.md) för att börja matcha namn för din Internetdomän.



<!--HONumber=sep16_HO1-->


