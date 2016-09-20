<properties
   pageTitle="Delegera din domän till Azure DNS | Microsoft Azure"
   description="Lär dig hur du ändrar domändelegering och använder Azure DNS-namnservrar för att tillhandahålla domänvärdtjänster."
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2016"
   ms.author="cherylmc"/>


# Delegera en domän till Azure DNS

Med Azure DNS kan du vara värd för en DNS-zon och hantera DNS-posterna för en domän i Azure. För att DNS-frågor för en domän ska nå Azure DNS måste domänen delegeras till Azure DNS från den överordnade domänen. Tänk på att Azure DNS inte är domänregistratorn. Den här artikeln förklarar hur domändelegering fungerar och hur du delegerar domäner till Azure DNS.




## Så här fungerar DNS-delegering

### Domäner och zoner

Domain Name System är en hierarki av domäner. Hierarkin startar från rotdomänen, vars namn är ”**.**”.  Under detta kommer toppdomänerna, till exempel ”com”, ”net”, ”org”, ”se” eller ”uk”.  Under dessa finns domänerna på den andra nivån, till exempel ”org.se” eller ”co.uk”.  Och så vidare. Domänerna i DNS-hierarkin använder separata DNS-zoner. Zonerna distribueras globalt och finns på DNS-namnservrar runtom i världen.

**DNS-zon**

En domän är ett unikt namn i Domain Name System, t.ex. ”contoso.com”. En DNS-zon används som värd åt DNS-posterna för en viss domän. Domänen ”contoso.com” kan till exempel innehålla flera DNS-poster, till exempel ”mail.contoso.com” (för en e-postserver) och ”www.contoso.com” (för en webbplats).

**Domänregistrator**

En domänregistrator är ett företag som kan tillhandahålla Internetdomännamn. De kontrollerar om Internetdomänen som du vill använda är tillgänglig så att du kan köpa den. När domännamnet har registrerats är du domännamnets juridiska ägare. Om du redan har en Internetdomän använder du domänregistratorn för att delegera till Azure DNS.

>[AZURE.NOTE] Om du vill ha mer information om vem som äger ett visst domännamn eller om du vill ha information om hur du köper en domän läser du [Internetdomänhantering i Azure AD](https://msdn.microsoft.com/library/azure/hh969248.aspx).

### Matchning och delegering

Det finns två typer av DNS-servrar:

- En _auktoritativ_ DNS-server är värd för DNS-zoner. Den svarar på DNS-frågor om poster i just dessa zoner.
- En _rekursiv_ DNS-server är inte värd för DNS-zoner. Den svarar på alla DNS-frågor genom att anropa auktoritativa DNS-servrar för att samla in de data som den behöver.

>[AZURE.NOTE] Azure DNS tillhandahåller en auktoritativ DNS-tjänst.  Någon rekursiv DNS-tjänst tillhandahålls inte.

> Cloud Services och virtuella datorer i Azure konfigureras automatiskt för användning av rekursiva DNS-tjänster som tillhandahålls separat som en del av Azures infrastruktur.  Information om hur du ändrar dessa DNS-inställningar finns i [Namnmatchning i Azure](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server).

DNS-klienter på datorer eller mobila enheter anropar vanligtvis en rekursiv DNS-server för att utföra DNS-frågor som klientprogrammen behöver.

När en rekursiv DNS-server tar emot en fråga om en DNS-post, till exempel ”www.contoso.com”, måste den först hitta namnservern som är värd för zonen för ”contoso.com”-domänen. För att göra det startar den vid rotnamnservrarna och letar därifrån upp namnservrarna som är värdar för ”com”-zonen. Därefter beordrar den ”com”-namnservrarna att hitta namnservrarna som är värdar för zonen ”contoso.com”.  Slutligen kan den fråga dessa namnservrar efter ”www.contoso.com”.  

Detta kallas för att DNS-namnet matchas. Strikt sett omfattar DNS-matchningen ytterligare steg, till exempel att följa CNAME-poster, men detta är inte viktigt för att förstå hur DNS-delegering fungerar.

Hur ”pekar” en överordnad zon på namnservrarna för en underordnad zon? Detta sker med hjälp av en särskild typ av DNS-post kallad en NS-post (NS står för namnserver). Rotzonen innehåller exempelvis NS-poster för ”com” och visar namnservrar för ”com”-zonen. ”Com”-zonen innehåller i sin tur NS-poster för ”contoso.com”, som visar namnservrarna för zonen ”contoso.com”. Att konfigurera NS-posterna för en underordnad zon i en överordnad zon kallas för att delegera domänen.


![DNS-namnserver](./media/dns-domain-delegation/image1.png)

Varje delegering har i själva verket två kopior av NS-posterna: en i den överordnade zonen som pekar på den underordnade zonen och en annan i den underordnade zonen. ”Contoso.com”-zonen innehåller NS-posterna för ”contoso.com” (förutom NS-posterna i ”com”). Dessa kallas för auktoritativa NS-poster och finns överst i den underordnade zonen.


## Delegera en domän till Azure DNS

När du skapar DNS-zonen i Azure DNS måste du ställa in NS-poster i den överordnade zonen så att Azure DNS blir den auktoritativa källan för namnmatchning för din zon. För domäner som köps från en registrator kan registratorn konfigurera dessa NS-poster.

>[AZURE.NOTE] Du behöver inte äga en domän för att kunna skapa en DNS-zon med det domännamnet i Azure DNS. Du behöver äga domänen för att konfigurera delegeringen till Azure DNS med registratorn.

Anta exempelvis att du köper domänen ”contoso.com” och skapar en zon med namnet ”contoso.com” i Azure DNS. Som ägare till domänen erbjuder sig registratorn att konfigurera namnserveradresserna (det vill säga NS-posterna) för din domän. Registratorn lagrar dessa NS-poster i den överordnade domänen, i detta fall ”.com”. Klienter över hela världen omdirigeras då till din domän i Azure DNS-zonen när du försöker matcha DNS-poster i ”contoso.com”.

### Hitta namnservernamnen

Innan du kan delegera din DNS-zon till Azure DNS måste du känna till namnservernamnen för din zon. Azure DNS allokerar namnservrar från en pool varje gång en zon skapas.

Det enklaste sättet att se namnservrarna som tilldelats din zon är via Azure-portalen.  I det här exemplet har zonen ”contoso.net” tilldelats namnservrarna ”ns1-01.azure-dns.com”, ”ns2-01.azure-dns.net”, ”ns3-01.azure-dns.org” och ”ns4-01.azure-dns.info”:

 ![DNS-namnserver](./media/dns-domain-delegation/viewzonens500.png)

Azure DNS skapar automatiskt auktoritativa NS-poster i din zon som innehåller de tilldelade namnservrarna.  Om du vill se namnservernamnen via Azure PowerShell eller Azure CLI behöver du bara hämta dessa poster.

Med Azure PowerShell kan auktoritativa NS-poster hämtas på följande sätt. Observera att postnamnet ”@” används för att referera till poster överst i zonen. 

    PS> $zone = Get-AzureRmDnsZone –Name contoso.net –ResourceGroupName MyResourceGroup
    PS> Get-AzureRmDnsRecordSet –Name “@” –RecordType NS –Zone $zone

    Name              : @
    ZoneName          : contoso.net
    ResourceGroupName : MyResourceGroup
    Ttl               : 3600
    Etag              : 5fe92e48-cc76-4912-a78c-7652d362ca18
    RecordType        : NS
    Records           : {ns1-01.azure-dns.com, ns2-01.azure-dns.net, ns3-01.azure-dns.org,
                        ns4-01.azure-dns.info}
    Tags              : {}

Du kan också använda plattformsoberoende Azure CLI för att hämta de auktoritativa NS-posterna och på så sätt identifiera namnservrarna som tilldelats din zon:

    C:\> azure network dns record-set show MyResourceGroup contoso.net @ NS
    info:    Executing command network dns record-set show
        + Looking up the DNS Record Set "@" of type "NS"
    data:    Id                              : /subscriptions/.../resourceGroups/MyResourceGroup/providers/Microsoft.Network/dnszones/contoso.net/NS/@
    data:    Name                            : @
    data:    Type                            : Microsoft.Network/dnszones/NS
    data:    Location                        : global
    data:    TTL                             : 172800
    data:    NS records
    data:        Name server domain name     : ns1-01.azure-dns.com.
    data:        Name server domain name     : ns2-01.azure-dns.net.
    data:        Name server domain name     : ns3-01.azure-dns.org.
    data:        Name server domain name     : ns4-01.azure-dns.info.
    data:
    info:    network dns record-set show command OK

### Så här konfigurerar du delegering

Varje registrator har sina egna DNS-hanteringsverktyg för att ändra namnserverposterna för en domän. På registratorns DNS-hanteringssida redigerar du NS-posterna och ersätter NS-posterna med dem som Azure DNS skapat.

När du delegerar en domän till Azure DNS måste du använda namnservernamnen som tillhandahålls av Azure DNS.  Du bör alltid använda alla fyra namnservernamn, oavsett vilket namn din domän har.  Domändelegering kräver inte att namnservernamnet använder samma toppnivådomän som din domän.

Använd inte ”fästposter” för att peka på IP-adresser för Azure DNS-namnservrar eftersom dessa IP-adresser kan komma att ändras i framtida. Delegering med hjälp av namnservernamn i din egen zon, även kallat ”vanity name servers”, stöds inte i Azure DNS.

### Så här kontrollerar du att namnmatchningen fungerar

När du har slutfört delegeringen kan du kontrollera att namnmatchningen fungerar med hjälp av ett verktyg som till exempel ”nslookup” för att fråga efter SOA-posten för din zon (som skapas automatiskt när zonen skapas).

Observera att du inte behöver ange Azure DNS-namnservrarna eftersom den normala DNS-matchningsprocessen hittar namnservrarna automatiskt om delegeringen har konfigurerats korrekt.

    nslookup –type=SOA contoso.com

    Server: ns1-04.azure-dns.com
    Address: 208.76.47.4

    contoso.com
    primary name server = ns1-04.azure-dns.com
    responsible mail addr = msnhst.microsoft.com
    serial = 1
    refresh = 900 (15 mins)
    retry = 300 (5 mins)
    expire = 604800 (7 days)
    default TTL = 300 (5 mins)

## Delegera underdomäner i Azure DNS

Om du vill konfigurera en separat underordnad zon kan du delegera en underdomän i Azure DNS. Anta till exempel att du har konfigurerat och delegerat ”contoso.com” i Azure DNS och att du vill konfigurera en separat underordnad zon, ”partners.contoso.com”. 

Konfigurationen av en underdomän följer i princip samma process som en normal delegering. Den enda skillnaden är att i steg 3 så måste NS-posterna skapas i den överordnade zonen ”contoso.com” i Azure DNS i stället för att konfigureras via en domänregistrator.


1. Skapa den underordnade zonen ”partners.contoso.com” i Azure DNS.
2. Leta upp de auktoritativa NS-posterna i den underordnade zonen  för att hämta namnservrarna som är värdar för den underordnade zonen i Azure DNS.
3. Delegera den underordnade zonen genom att konfigurera NS-poster i den överordnade zonen som pekar på den underordnade zonen.


### Så här delegerar du en underordnad domän

Följande PowerShell-exempel demonstrerar hur det fungerar. Du kan köra samma steg från Azure-portalen, eller via plattformsoberoende Azure CLI.

#### Steg 1. Skapa överordnade och underordnade zoner

Först ska vi skapa de överordnade och underordnade zonerna. Dessa kan finnas i samma resursgrupp eller i olika resursgrupper.

    $parent = New-AzureRmDnsZone -Name contoso.com -ResourceGroupName RG1
    $child = New-AzureRmDnsZone -Name partners.contoso.com -ResourceGroupName RG1

#### Steg 2. Hämta NS-poster

Nu ska vi hämta de auktoritativa NS-posterna från den underordnade zonen som du ser i nästa exempel.  Detta innehåller namnservrarna som tilldelats den underordnade zonen. 

    $child_ns_recordset = Get-AzureRmDnsRecordSet -Zone $child -Name "@" -RecordType NS

#### Steg 3. Delegera den underordnade zonen

Skapa motsvarande NS-poster i den överordnade zonen för att slutföra delegeringen. Observera att namnet på postuppsättningen i den överordnade zonen matchar namnet på den underordnade zonen, i detta fall ”partners”.

    $parent_ns_recordset = New-AzureRmDnsRecordSet -Zone $parent -Name "partners" -RecordType NS -Ttl 3600
    $parent_ns_recordset.Records = $child_ns_recordset.Records
    Set-AzureRmDnsRecordSet -RecordSet $parent_ns_recordset

### Så här kontrollerar du att namnmatchningen fungerar

Du kan kontrollera att allt är korrekt konfigurerat genom att leta upp SOA-posten för den underordnade zonen.

    nslookup –type=SOA partners.contoso.com

    Server: ns1-08.azure-dns.com
    Address: 208.76.47.8

    partners.contoso.com
        primary name server = ns1-08.azure-dns.com
        responsible mail addr = msnhst.microsoft.com
        serial = 1
        refresh = 900 (15 mins)
        retry = 300 (5 mins)
        expire = 604800 (7 days)
        default TTL = 300 (5 mins)

## Nästa steg

[Hantera DNS-zoner](dns-operations-dnszones.md)

[Hantera DNS-poster](dns-operations-recordsets.md)




<!--HONumber=sep16_HO1-->


