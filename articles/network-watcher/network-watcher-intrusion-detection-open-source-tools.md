---
title: Utföra nätverket intrångsidentifiering med öppen källkod verktyg och Azure Nätverksbevakaren | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Nätverksbevakaren och Öppna källa verktyg för att utföra intrångsidentifiering för nätverk
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: aff1b5f9e8860d3b8dc09b37684bb8a4ac2bf134
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
ms.locfileid: "23864226"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Utföra nätverket intrångsidentifiering med öppen källkod verktyg och Nätverksbevakaren

Paketet insamlingar är en viktig del för att implementera nätverk intrång identifiering (ID) och utföra Network Security övervakning (NSM). Det finns flera ID: N verktyg för öppen källkod som bearbetar paket insamlingar och leta efter signaturer för intrång i nätverket och skadliga aktiviteter. Med hjälp av paketet samlar in angivna genom Nätverksbevakaren, kan du analysera nätverket för skadliga intrång eller säkerhetsproblem.

Ett sådant öppen källkod verktyg är Suricata, en ID-motor som använder regeluppsättningar att övervaka nätverkstrafik och utlöser aviseringar när misstänkta händelser inträffar. Suricata erbjuder ett flertrådat motorn, vilket innebär att den kan utföra nätverkstrafikanalysen med ökad hastighet och effektivitet. Mer information om Suricata och dess funktioner finns i deras webbplats på https://suricata-ids.org/.

## <a name="scenario"></a>Scenario

Den här artikeln beskriver hur du konfigurerar miljön för att utföra nätverket intrångsidentifiering med Nätverksbevakaren, Suricata och elastiska stacken. Nätverksbevakaren ger paket-insamlingar som används för att utföra nätverket intrångsidentifiering. Suricata bearbetar paket insamlingar och utlösa varningar baserat på paket som matchar dess given regeluppsättning hot. Dessa aviseringar som lagras i en loggfil på den lokala datorn. Med elastisk stacken kan loggar som genereras av Suricata indexeras och används för att skapa en Kibana instrumentpanel som ger dig en bild av loggarna och ett sätt att snabbt få insikter om potentiella säkerhetsproblem i nätverket.  

![enkel web application scenario][1]

Båda verktyg med öppen källkod kan ställas in på en virtuell dator i Azure, så att du kan utföra den här analysen i Azure network-miljön.

## <a name="steps"></a>Steg

### <a name="install-suricata"></a>Installera Suricata

Alla andra metoder för installation finns http://suricata.readthedocs.io/en/latest/install.html

1. Kör följande kommandon i Kommandotolken terminal på den virtuella datorn:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Verifiera installationen genom att köra kommandot `suricata -h` att se en fullständig lista över kommandon.

### <a name="download-the-emerging-threats-ruleset"></a>Hämta nya hot RuleSet-metod

I det här skedet har vi inte några regler för Suricata ska köras. Du kan skapa dina egna regler om det finns specifika hot mot nätverket som du vill identifiera och du kan också använda utvecklats uppsättningar från ett antal leverantörer, till exempel nya hot eller VRT regler från Snort. Vi använder fritt tillgänglig nya hot ruleset här:

Hämta regeluppsättningen och kopiera dem till katalogen:

```
wget http://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Processen paket fångar med Suricata

Samlar in med hjälp av Suricata, kör du följande kommando för att bearbeta paketet:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Läs filen fast.log för att kontrollera de resulterande aviseringarna:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurera elastisk Stack

När de loggar som producerar Suricata innehåller värdefull information om vad som händer på vårt nätverk, är dessa loggfiler inte enklast att läsa och förstå. Genom att ansluta Suricata med elastisk Stack kan skapa vi en instrumentpanel för Kibana vad gör att vi kan söka, diagram, analysera och härleda insikter från våra loggar.

#### <a name="install-elasticsearch"></a>Installera Elasticsearch

1. Elastisk stacken version 5.0 och senare kräver Java 8. Kör kommandot `java -version` att kontrollera din version. Om du inte har java installera, finns i dokumentationen till på [Oracles webbplats](http://docs.oracle.com/javase/8/docs/technotes/guides/install/install_overview.html)
1. Hämta rätt binära paket för ditt system:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Andra installationsmetoder finns på [Elasticsearch Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

1. Kontrollera att Elasticsearch körs med kommandot:

    ```
    curl http://127.0.0.1:9200
    ```

    Du bör se ett svar som liknar detta:

    ```
    {
    "name" : "Angela Del Toro",
    "cluster_name" : "elasticsearch",
    "version" : {
        "number" : "5.2.0",
        "build_hash" : "8ff36d139e16f8720f2947ef62c8167a888992fe",
        "build_timestamp" : "2016-01-27T13:32:39Z",
        "build_snapshot" : false,
        "lucene_version" : "6.1.0"
    },
    "tagline" : "You Know, for Search"
    }
    ```

Ytterligare information om installation elastisk Sök hänvisar till sidan [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Installera Logstash

1. Installera Logstash kör följande kommandon:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Nästa vi måste du konfigurera Logstash att läsa från utdata från eve.json-filen. Skapa filen logstash.conf med:

    ```
    sudo touch /etc/logstash/conf.d/logstash.conf
    ```

1. Lägg till följande innehåll i filen (Kontrollera att sökvägen till filen eve.json är korrekt):

    ```ruby
    input {
    file {
        path => ["/var/log/suricata/eve.json"]
        codec =>  "json"
        type => "SuricataIDPS"
    }

    }

    filter {
    if [type] == "SuricataIDPS" {
        date {
        match => [ "timestamp", "ISO8601" ]
        }
        ruby {
        code => "
            if event.get('[event_type]') == 'fileinfo'
            event.set('[fileinfo][type]', event.get('[fileinfo][magic]').to_s.split(',')[0])
            end
        "
        }

        ruby{
        code => "
            if event.get('[event_type]') == 'alert'
            sp = event.get('[alert][signature]').to_s.split(' group ')
            if (sp.length == 2) and /\A\d+\z/.match(sp[1])
                event.set('[alert][signature]', sp[0])
            end
            end
            "
        }
    }

    if [src_ip]  {
        geoip {
        source => "src_ip"
        target => "geoip"
        #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
        add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
        add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
        }
        mutate {
        convert => [ "[geoip][coordinates]", "float" ]
        }
        if ![geoip.ip] {
        if [dest_ip]  {
            geoip {
            source => "dest_ip"
            target => "geoip"
            #database => "/opt/logstash/vendor/geoip/GeoLiteCity.dat"
            add_field => [ "[geoip][coordinates]", "%{[geoip][longitude]}" ]
            add_field => [ "[geoip][coordinates]", "%{[geoip][latitude]}"  ]
            }
            mutate {
            convert => [ "[geoip][coordinates]", "float" ]
            }
        }
        }
    }
    }

    output {
    elasticsearch {
        hosts => "localhost"
    }
    }
    ```

1. Se till att ge behörighet till filen eve.json så att Logstash kan infognings-filen.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Om du vill starta Logstash köra kommandot:

    ```
    sudo /etc/init.d/logstash start
    ```

Ytterligare instruktioner om hur du installerar Logstash finns i den [officiella dokumentation](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Installera Kibana

1. Kör följande kommandon för att installera Kibana:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/kibana/kibana-5.2.0-linux-x86_64.tar.gz
    tar xzvf kibana-5.2.0-linux-x86_64.tar.gz

    ```
1. Om du vill köra Använd Kibana kommandon:

    ```
    cd kibana-5.2.0-linux-x86_64/
    ./bin/kibana
    ```

1. Om du vill visa dina Kibana webbgränssnitt, gå till `http://localhost:5601`
1. I det här scenariot index mönstret för Suricata loggarna är ”logstash-*”

1. Om du vill visa infopanelen Kibana via fjärranslutning, skapar du en inkommande NSG regel för att tillåta åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en Kibana instrumentpanel

Vi har angett ett exempel på en instrumentpanel där du kan visa trender och information i aviseringar för den här artikeln.

1. Hämta filen instrumentpanelen [här](https://aka.ms/networkwatchersuricatadashboard), visualisering filen [här](https://aka.ms/networkwatchersuricatavisualization), och filen sparad sökning [här](https://aka.ms/networkwatchersuricatasavedsearch).

1. Under den **Management** fliken av Kibana, gå till **sparade objekt** och importera alla tre filerna. Sedan från den **instrumentpanelen** fliken kan du öppna och läsa in de exempel på en instrumentpanelen.

Du kan också skapa egna visualiseringar och instrumentpaneler som skräddarsys mot mätvärden för ditt eget intresse. Läs mer om hur du skapar Kibana visualiseringar från Kibana's [officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![kibana instrumentpanelen][2]

### <a name="visualize-ids-alert-logs"></a>Visualisera ID avisering loggar

I exemplet på instrumentpanel innehåller flera visualiseringar Suricata avisering loggar:

1. Aviseringar av GeoIP – en karta med distribution av aviseringar för sina ursprung baserat på geografisk plats (bestäms av IP)

    ![GEO-ip][3]

1. Översta 10 aviseringar – en sammanfattning av de vanligaste 10 utlöst aviseringar och deras beskrivning. Klicka på en enskild varning filtrerar ned instrumentpanelen på den information som rör den specifika aviseringen.

    ![image 4][4]

1. Antal aviseringar – totalt antal aviseringar som har utlösts av den RuleSet-metod

    ![bild 5][5]

1. Övre 20 källan/målet IP-adresser portar - cirkeldiagram visar de översta 20 IP-adresser och portar som aviseringar har utlösts av. Du kan filtrera nedåt på specifika IP-adresser/portar att se hur många och vilka typer av aviseringar aktiveras.

    ![image 6][6]

1. Aviseringen sammanfattning – en tabell sammanfattar närmare för varje enskild varning. Du kan anpassa den här tabellen om du vill visa andra parametrar av intresse för varje avisering.

    ![Bild 7][7]

Mer dokumentation om hur du skapar anpassade visualiseringar och instrumentpaneler finns [Kibanas officiella dokumentation](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Sammanfattning

Samlar in angivna genom Nätverksbevakaren och verktyg för öppen källkod-ID: N som Suricata, du kan utföra nätverket intrångsidentifiering för en mängd olika hot genom att kombinera paket. Instrumentpanelerna kan du snabbt se trender och avvikelser i nätverket, som korrekt gräva i data för att identifiera rotorsaken till aviseringar, till exempel skadliga användaragenter eller sårbara portarna. Med den här extraherade data fatta du välgrundade beslut om hur du ta hänsyn till och skydda nätverket från skadliga intrångsförsök och skapa regler för att förhindra framtida intrång i nätverket.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du utlöser paket insamlingar baserat på aviseringar genom att besöka [använder paketinsamling för proaktiv nätverksövervakning med Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Lär dig hur du visualisera dina NSG flödet loggar med Power BI genom att besöka [visualisera NSG flödar loggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
