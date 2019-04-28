---
title: Utföra intrångsidentifiering för nätverk med Azure Network Watcher och verktyg för öppen källkod | Microsoft Docs
description: Den här artikeln beskriver hur du använder Azure Network Watcher och verktyg med öppen källkod för att utföra avkänning av nätverksintrång
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 0f043f08-19e1-4125-98b0-3e335ba69681
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 41b039c6598334d49b9ddb0608d7ad2ae045b223
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115508"
---
# <a name="perform-network-intrusion-detection-with-network-watcher-and-open-source-tools"></a>Utföra intrångsidentifiering för nätverk med Network Watcher och verktyg för öppen källkod

Fånga in är en viktig komponent för att implementera nätverk intrång identifiering (ID) och utföra Network Security Monitoring (NSM). Det finns flera ID: N verktyg för öppen källkod som bearbetar infångade paket och leta efter signaturer för nätverket intrång och skadlig aktivitet. Med hjälp av paketet samlar in angivna av Network Watcher kan du analysera nätverket för skadliga intrång eller säkerhetsproblem.

Ett sådant verktyg med öppen källkod är Suricata, en motor med ID: N som använder regeluppsättningar för att övervaka nätverkstrafik och utlöser varningar när misstänkta händelser inträffar. Suricata erbjuder en flertrådiga motor, vilket innebär att den kan utföra analysen av nätverkstrafik med ökad hastighet och effektivitet. Mer information om Suricata och dess funktioner, gå till webbplatsen på https://suricata-ids.org/.

## <a name="scenario"></a>Scenario

Den här artikeln beskriver hur du ställer in din miljö för att utföra intrångsidentifiering i nätverk med Network Watcher, Suricata och Elastic Stack. Network Watcher får du med infångade som används för att utföra avkänning av nätverksintrång. Suricata bearbetar infångade paket och utlöser aviseringar baserat på paket som matchar dess given regeluppsättning hot. Dessa aviseringar lagras i en loggfil på den lokala datorn. Med Elastic Stack kan loggar som genereras av Suricata vara indexerade och används för att skapa en Kibana-instrumentpanelen, vilket ger dig en visuell representation av loggarna och ett sätt att snabbt få information om säkerhetsrisker i dina nätverk.  

![enkel programscenariot][1]

Båda verktyg för öppen källkod kan ställas in på en virtuell dator i Azure, så att du kan utföra den här analysen inom din egen Azure-nätverksmiljön.

## <a name="steps"></a>Steg

### <a name="install-suricata"></a>Installera Suricata

Alla andra metoder för installation, besök https://suricata.readthedocs.io/en/latest/install.html

1. Kör följande kommandon i kommandoradsterminal för den virtuella datorn:

    ```
    sudo add-apt-repository ppa:oisf/suricata-stable
    sudo apt-get update
    sudo sudo apt-get install suricata
    ```

1. Om du vill verifiera installationen, kör du kommandot `suricata -h` att se en fullständig lista över kommandon.

### <a name="download-the-emerging-threats-ruleset"></a>Ladda ned växande hot RuleSet-metod

I det här skedet har vi inte några regler för Suricata ska köras. Du kan skapa dina egna regler om det finns specifika hot mot ditt nätverk som du vill identifiera eller du kan också använda utvecklade regeluppsättningar från ett antal leverantörer, till exempel växande hot eller VRT regler från Snort. Vi använder den fritt tillgänglig växande hot RuleSet-metod här:

Hämta regeluppsättningen och kopiera dem till katalogen:

```
wget https://rules.emergingthreats.net/open/suricata/emerging.rules.tar.gz
tar zxf emerging.rules.tar.gz
sudo cp -r rules /etc/suricata/
```

### <a name="process-packet-captures-with-suricata"></a>Fjärrinfångade processen paket med Suricata

Samlar in med hjälp av Suricata, kör du följande kommando för att bearbeta paketet:

```
sudo suricata -c /etc/suricata/suricata.yaml -r <location_of_pcapfile>
```
Läs filen fast.log för att kontrollera varningarna:
```
tail -f /var/log/suricata/fast.log
```

### <a name="set-up-the-elastic-stack"></a>Konfigurera Elastic Stack

När de loggar som producerar Suricata innehåller värdefull information om vad som händer på vårt nätverk, inte dessa loggfiler är den enklaste att läsa och förstå. Genom att ansluta Suricata med Elastic Stack, kan vi skapa en instrumentpanel med Kibana vad kan vi söka, diagram, analysera och slutsatser från våra loggar.

#### <a name="install-elasticsearch"></a>Installera Elasticsearch

1. Elastic Stack från version 5.0 och senare kräver Java 8. Kör kommandot `java -version` kan kontrollera din version. Om du inte har installerat java läser dokumentationen på den [Azure kodblock JDKs](https://aka.ms/azure-jdks).

1. Ladda ned rätt binära paket för ditt system:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-5.2.0.deb
    sudo dpkg -i elasticsearch-5.2.0.deb
    sudo /etc/init.d/elasticsearch start
    ```

    Andra installationsmetoder finns på [Elasticsearch-Installation](https://www.elastic.co/guide/en/beats/libbeat/5.2/elasticsearch-installation.html)

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

Ytterligare anvisningar för installation elastiska sökning, referera till sidan [Installation](https://www.elastic.co/guide/en/elasticsearch/reference/5.2/_installation.html)

### <a name="install-logstash"></a>Installera Logstash

1. Installera Logstash kör följande kommandon:

    ```
    curl -L -O https://artifacts.elastic.co/downloads/logstash/logstash-5.2.0.deb
    sudo dpkg -i logstash-5.2.0.deb
    ```
1. Därefter måste vi konfigurera Logstash för att läsa från utdata från eve.json fil. Skapa en logstash.conf filen med:

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

1. Se till att ge behörighet till filen eve.json så att Logstash kan mata in filen.
    
    ```
    sudo chmod 775 /var/log/suricata/eve.json
    ```

1. Starta Logstash kör kommandot:

    ```
    sudo /etc/init.d/logstash start
    ```

För ytterligare instruktioner om hur du installerar Logstash avser den [officiella dokumentationen](https://www.elastic.co/guide/en/beats/libbeat/5.2/logstash-installation.html)

### <a name="install-kibana"></a>Install Kibana

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

1. Om du vill visa Kibana-webbgränssnittet, gå till `http://localhost:5601`
1. Det här scenariot är mönstret index används för Suricata loggar ”logstash-*”

1. Om du vill visa Kibana-instrumentpanelen via en fjärranslutning, skapar du en inkommande NSG-regel som tillåter åtkomst till **port 5601**.

### <a name="create-a-kibana-dashboard"></a>Skapa en instrumentpanel i Kibana

I den här artikeln har vi lagt till ett exempel på en instrumentpanel som du kan visa trender och information i dina aviseringar.

1. Ladda ned filen instrumentpanelen [här](https://aka.ms/networkwatchersuricatadashboard), filen visualisering [här](https://aka.ms/networkwatchersuricatavisualization), och filen sparad sökning [här](https://aka.ms/networkwatchersuricatasavedsearch).

1. Under den **Management** fliken av Kibana, gå till **sparade objekt** och importera alla tre filerna. Sedan från den **instrumentpanelen** fliken kan du öppna och läsa in exempelinstrumentpanel.

Du kan också skapa egna visualiseringar och instrumentpaneler som är skräddarsydda för din egen mätvärden. Läs mer om att skapa Kibana visualiseringar från Kibana's [officiella dokumentationen](https://www.elastic.co/guide/en/kibana/current/visualize.html).

![kibana-instrumentpanelen][2]

### <a name="visualize-ids-alert-logs"></a>Visualisera ID: N avisering loggar

Exempelinstrumentpanelen innehåller flera visualiseringar Suricata avisering loggar:

1. Aviseringar efter GeoIP – en karta som visar fördelningen av aviseringar efter ursprungslandet baserat på geografisk plats (bestäms av IP)

    ![GEO-ip][3]

1. Främsta 10 aviseringar – en sammanfattning av de 10 vanligaste utlösta aviseringarna och deras beskrivning. Klicka på en enskild varning filtrerar ned instrumentpanelen till den information som hör till den specifika aviseringen.

    ![bild 4][4]

1. Antal aviseringar – det totala antalet aviseringar som utlöses av den RuleSet-metod

    ![image 5][5]

1. Översta 20 källans/målets IP-adresser/portar – diagram som visar de översta 20 IP-adresser och portar som aviseringar har utlösts på. Du kan filtrera nedåt i specifika IP-adresser/portar att se hur många och vilka typer av aviseringar som utlöses.

    ![image 6][6]

1. Aviseringssammanfattning – en tabell som sammanställer specifik information om varje enskild avisering. Du kan anpassa den här tabellen för att visa andra parametrar av intresse för varje avisering.

    ![Bild 7][7]

Mer dokumentation om hur du skapar anpassade visualiseringar och instrumentpaneler i [Kibanas officiella dokumentationen](https://www.elastic.co/guide/en/kibana/current/introduction.html).

## <a name="conclusion"></a>Sammanfattning

Samlar in angivna av Network Watcher och verktyg för öppen källkod-ID: N, till exempel Suricata, du kan utföra avkänning av nätverksintrång för en mängd olika hot genom att kombinera paket. Dessa instrumentpaneler kan du snabbt upptäcka trender och avvikelser i ditt nätverk som och fördjupa data för att identifiera rotorsaken till aviseringar, till exempel skadliga användaragenter eller sårbara portar. Med den här extraherade data kan du fatta välgrundade beslut om hur du reagera på och skydda ditt nätverk från alla skadliga intrångsförsök och skapa regler för att förhindra framtida intrång i nätverket.

## <a name="next-steps"></a>Nästa steg

Lär dig hur du utlöser infångade paket baserat på varningar genom att besöka [använda infångade paket för att utföra proaktiv nätverksövervakning med Azure Functions](network-watcher-alert-triggered-packet-capture.md)

Lär dig att visualisera dina NSG-flödesloggar med Power BI genom att besöka [visualisera NSG-flödesloggar med Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)



<!-- images -->
[1]: ./media/network-watcher-intrusion-detection-open-source-tools/figure1.png
[2]: ./media/network-watcher-intrusion-detection-open-source-tools/figure2.png
[3]: ./media/network-watcher-intrusion-detection-open-source-tools/figure3.png
[4]: ./media/network-watcher-intrusion-detection-open-source-tools/figure4.png
[5]: ./media/network-watcher-intrusion-detection-open-source-tools/figure5.png
[6]: ./media/network-watcher-intrusion-detection-open-source-tools/figure6.png
[7]: ./media/network-watcher-intrusion-detection-open-source-tools/figure7.png
