---
title: Utveckla skriptåtgärder för att anpassa Azure HDInsight-kluster
description: Lär dig hur du använder Bash-skript för att anpassa HDInsight-kluster. Med skriptåtgärder kan du köra skript under eller efter att klustret har skapats för att ändra klusterkonfigurationsinställningarna eller installera ytterligare programvara.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: ad9b4b69b0be34c89d03b677c1889e486aae0379
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931701"
---
# <a name="script-action-development-with-hdinsight"></a>Utveckling av skriptåtgärder med HDInsight

Lär dig hur du anpassar ditt HDInsight-kluster med Bash-skript. Skriptåtgärder är ett sätt att anpassa HDInsight under eller efter att klustret skapas.

## <a name="what-are-script-actions"></a>Vad är skriptåtgärder

Skriptåtgärder är Bash-skript som Azure kör på klusternoderna för att göra konfigurationsändringar eller installera programvara. En skriptåtgärd körs som root och ger klusternoder fullständiga åtkomsträttigheter.

Skriptåtgärder kan tillämpas med hjälp av följande metoder:

| Använd den här metoden om du vill använda ett skript... | När klustret skapas... | På ett kluster som körs... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klassisk Azure CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-mall |✓ |&nbsp; |

Mer information om hur du använder dessa metoder för att tillämpa skriptåtgärder finns i [Anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Metodtips för skriptutveckling

När du utvecklar ett anpassat skript för ett HDInsight-kluster finns det flera metodtips att tänka på:

* [Rikta in dig på Apache Hadoop-versionen](#bPS1)
* [Rikta in dig på OS-versionen](#bps10)
* [Tillhandahålla stabila länkar till skriptresurser](#bPS2)
* [Använd förkompilerade resurser](#bPS4)
* [Kontrollera att klusteranpassningsskriptet är idempotent](#bPS3)
* [Säkerställa hög tillgänglighet för klusterarkitekturen](#bPS5)
* [Konfigurera anpassade komponenter för att använda Azure Blob-lagring](#bPS6)
* [Skriv information till STDOUT och STDERR](#bPS7)
* [Spara filer som ASCII med LF-radslut](#bps8)
* [Använd logik för återförsök för att återställa från tillfälliga fel](#bps9)

> [!IMPORTANT]  
> Skriptåtgärder måste slutföras inom 60 minuter eller så misslyckas processen. Under nodetablering körs skriptet samtidigt med andra inställnings- och konfigurationsprocesser. Konkurrens om resurser som CPU-tid eller nätverksbandbredd kan orsaka att skriptet tar längre tid att slutföra än det gör i utvecklingsmiljön.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Rikta in dig på Apache Hadoop-versionen

Olika versioner av HDInsight har olika versioner av Hadoop-tjänster och komponenter installerade. Om skriptet förväntar sig en viss version av en tjänst eller komponent bör du bara använda skriptet med den version av HDInsight som innehåller de nödvändiga komponenterna. Du hittar information om komponentversioner som ingår i HDInsight med hjälp av [HDInsight-komponentversionsdokumentet.](hdinsight-component-versioning.md)

### <a name="checking-the-operating-system-version"></a>Kontrollera operativsystemets version

Olika versioner av HDInsight är beroende av specifika versioner av Ubuntu. Det kan finnas skillnader mellan operativsystemsversioner som du måste söka efter i skriptet. Du kan till exempel behöva installera en binär som är knuten till versionen av Ubuntu.

Om du vill kontrollera `lsb_release`OS-versionen använder du . Följande skript visar till exempel hur du refererar till en viss tjärfil beroende på OS-versionen:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>Rikta in operativsystemets version

HDInsight är baserat på Ubuntu Linux-distributionen. Olika versioner av HDInsight förlitar sig på olika versioner av Ubuntu, vilket kan ändra hur ditt skript beter sig. HdInsight 3.4 och tidigare baseras till exempel på Ubuntu-versioner som använder Upstart. Version 3.5 och mer är baserade på Ubuntu 16.04, som använder Systemd. Systemd och Upstart förlitar sig på olika kommandon, så skriptet ska skrivas för att fungera med båda.

En annan viktig skillnad mellan HDInsight 3,4 `JAVA_HOME` och 3,5 är att nu pekar på Java 8. Följande kod visar hur du avgör om skriptet körs på Ubuntu 14 eller 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Du hittar hela skriptet som innehåller dessa https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.shutdrag på .

Den version av Ubuntu som används av HDInsight finns i [hdinsight-komponentversionen.](hdinsight-component-versioning.md)

Information om skillnaderna mellan Systemd och Upstart finns i [Systemd för uppkomlingsanvändare](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Tillhandahålla stabila länkar till skriptresurser

Skriptet och tillhörande resurser måste vara tillgängliga under hela klustrets livstid. Dessa resurser krävs om nya noder läggs till i klustret under skalningsåtgärder.

Det bästa är att hämta och arkivera allt i ett Azure Storage-konto för din prenumeration.

> [!IMPORTANT]  
> Lagringskontot som används måste vara standardlagringskontot för klustret eller en offentlig, skrivskyddad behållare på något annat lagringskonto.

Exempel på exempel lagras exempel från [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) Microsoft i lagringskontot. Den här platsen är en offentlig, skrivskyddad behållare som underhålls av HDInsight-teamet.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Använd förkompilerade resurser

Undvik åtgärder som kompilerar resurser från källkoden om du vill minska den tid det tar att köra skriptet. Till exempel förkompilera resurser och lagra dem i en Azure Storage-kontoblob i samma datacenter som HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Kontrollera att klusteranpassningsskriptet är idempotent

Skript måste vara idempotenta. Om skriptet körs flera gånger bör det återställa klustret till samma tillstånd varje gång.

Ett skript som ändrar konfigurationsfiler bör till exempel inte lägga till dubblettposter om de kördes flera gånger.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Säkerställa hög tillgänglighet för klusterarkitekturen

Linux-baserade HDInsight-kluster tillhandahåller två huvudnoder som är aktiva i klustret och skriptåtgärder körs på båda noderna. Om de komponenter du installerar bara förväntar sig en huvudnod ska du inte installera komponenterna på båda huvudnoderna.

> [!IMPORTANT]  
> Tjänster som tillhandahålls som en del av HDInsight är utformade för att växla över mellan de två huvudnoderna efter behov. Den här funktionen utökas inte till anpassade komponenter som installeras genom skriptåtgärder. Om du behöver hög tillgänglighet för anpassade komponenter måste du implementera din egen redundansmekanism.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Konfigurera anpassade komponenter för att använda Azure Blob-lagring

Komponenter som du installerar i klustret kan ha en standardkonfiguration som använder HDFS-lagring (Apache Hadoop Distributed File System). HDInsight använder antingen Azure Storage eller Data Lake Storage som standardlagring. Båda tillhandahåller ett HDFS-kompatibelt filsystem som innehåller data även om klustret tas bort. Du kan behöva konfigurera komponenter som du installerar för att använda WASB eller ADL i stället för HDFS.

För de flesta åtgärder behöver du inte ange filsystemet. Följande kopierar till exempel filen hadoop-common.jar från det lokala filsystemet till klusterlagring:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

I det här `hdfs` exemplet använder kommandot transparent standardklusterlagringen. För vissa åtgärder kan du behöva ange URI. Till exempel `adl:///example/jars` för Azure Data Lake `abfs:///example/jars` Storage Gen1, `wasb:///example/jars` för Data Lake Storage Gen2 eller för Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Skriv information till STDOUT och STDERR

HDInsight loggar skriptutdata som skrivs till STDOUT och STDERR. Du kan visa den här informationen med webbgränssnittet Ambari.

> [!NOTE]  
> Apache Ambari är endast tillgängligt om klustret har skapats. Om du använder en skriptåtgärd när klustret skapas och skapandet misslyckas läser du felsökningsavsnittet [Anpassa HDInsight-kluster med hjälp](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) av skriptåtgärd för andra sätt att komma åt logginformation.

De flesta verktyg och installationspaket skriver redan information till STDOUT och STDERR, men du kanske vill lägga till ytterligare loggning. Om du vill skicka text `echo`till STDOUT använder du . Ett exempel:

```bash
echo "Getting ready to install Foo"
```

Som standard `echo` skickar strängen till STDOUT. Om du vill rikta den `>&2` `echo`till STDERR lägger du till den innan . Ett exempel:

```bash
>&2 echo "An error occurred installing Foo"
```

Detta omdirigerar information skriven till STDOUT till STDERR (2) istället. Mer information om omdirigering av IO finns i [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html).

Mer information om hur du visar information som loggas av skriptåtgärder finns i [Anpassa HDInsight-kluster med hjälp av skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a>Spara filer som ASCII med LF-radslut

Bash-skript bör lagras som ASCII-format, med rader som avslutas av LF. Filer som lagras som UTF-8, eller använda CRLF som radslut kan misslyckas med följande fel:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a>Använd logik för återförsök för att återställa från tillfälliga fel

När du hämtar filer, installerar paket med apt-get eller andra åtgärder som överför data via internet kan åtgärden misslyckas på grund av tillfälliga nätverksfel. Fjärrresursen som du kommunicerar med kan till exempel vara i färd med att gå över till en nod för säkerhetskopiering.

Om du vill göra skriptet motståndskraftigt mot tillfälliga fel kan du implementera logik för återförsök. Följande funktion visar hur du implementerar logik för återförsök. Åtgärden försökers igen tre gånger innan den misslyckas.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Följande exempel visar hur du använder den här funktionen.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Hjälpmetoder för anpassade skript

Skriptåtgärdshjälpmetoder är verktyg som du kan använda när du skriver anpassade skript. Dessa metoder finns i [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skriptet. Använd följande för att hämta och använda dem som en del av skriptet:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Följande hjälphjälpare som är tillgängliga för användning i skriptet:

| Användning av hjälpare | Beskrivning |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Hämtar en fil från käll-URI till den angivna filsökvägen. Som standard skrivs inte en befintlig fil över. |
| `untar_file TARFILE DESTDIR` |Extraherar en tar-fil (med `-xf`) till målkatalogen. |
| `test_is_headnode` |Om kördes på en klusterhuvudnod returnerar du 1; annars 0. |
| `test_is_datanode` |Om den aktuella noden är en datanod (arbetare) returnerar du en 1; annars 0. |
| `test_is_first_datanode` |Om den aktuella noden är den första datanoden (arbetare) (med namnet workernode0) returnerar en 1; annars 0. |
| `get_headnodes` |Returnera det fullständigt kvalificerade domännamnet för headnodes i klustret. Namn är komma avgränsade. En tom sträng returneras vid fel. |
| `get_primary_headnode` |Hämtar det fullständigt kvalificerade domännamnet för den primära headnoden. En tom sträng returneras vid fel. |
| `get_secondary_headnode` |Hämtar det fullständiga domännamnet för den sekundära headnoden. En tom sträng returneras vid fel. |
| `get_primary_headnode_number` |Hämtar det numeriska suffixet för den primära headnoden. En tom sträng returneras vid fel. |
| `get_secondary_headnode_number` |Hämtar det numeriska suffixet för den sekundära headnoden. En tom sträng returneras vid fel. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Vanliga användningsmönster

Det här avsnittet innehåller vägledning om hur du implementerar några av de vanliga användningsmönster som du kan stöta på när du skriver ett eget anpassat skript.

### <a name="passing-parameters-to-a-script"></a>Skicka parametrar till ett skript

I vissa fall kan skriptet kräva parametrar. Du kan till exempel behöva administratörslösenordet för klustret när du använder Ambari REST API.

Parametrar som skickas till skriptet kallas *positionsparametrar*och tilldelas `$1` `$2` för den första parametern, för den andra och så vidare. `$0`innehåller namnet på själva skriptet.

Värden som skickas till skriptet som parametrar ska omges av enstaka citattecken ('). Detta säkerställer att det skickade värdet behandlas som en litteral.

### <a name="setting-environment-variables"></a>Ange miljövariabler

Ställa in en miljövariabel utförs av följande uttryck:

    VARIABLENAME=value

Där VARIABLENAME är namnet på variabeln. Använd för att `$VARIABLENAME`komma åt variabeln . Om du till exempel vill tilldela ett värde som tillhandahålls av en positionsparameter som en miljövariabel med namnet LÖSENORD använder du följande uttryck:

    PASSWORD=$1

Efterföljande tillgång till informationen `$PASSWORD`kan då använda .

Miljövariabler som anges i skriptet finns bara inom skriptets omfång. I vissa fall kan du behöva lägga till systemomfattande miljövariabler som kvarstår när skriptet är klart. Om du vill lägga till systemomfattande `/etc/environment`miljövariabler lägger du till variabeln i . Följande sats lägger `HADOOP_CONF_DIR`till:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Åtkomst till platser där de anpassade skripten lagras

Skript som används för att anpassa ett kluster måste lagras på någon av följande platser:

* Ett __Azure Storage-konto__ som är associerat med klustret.

* Ett __ytterligare lagringskonto__ som är associerat med klustret.

* En __allmänt läsbar URI__. En URL till data som lagras på OneDrive, Dropbox eller annan filvärdtjänst.

* Ett __Azure Data Lake Storage-konto__ som är associerat med HDInsight-klustret. Mer information om hur du använder Azure Data Lake Storage med HDInsight finns [i Snabbstart: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > Tjänstens huvudnamn HDInsight använder för att komma åt Data Lake Storage måste ha läsbehörighet till skriptet.

Resurser som används av skriptet måste också vara allmänt tillgängliga.

Lagring av filer i ett Azure Storage-konto eller Azure Data Lake Storage ger snabb åtkomst, eftersom både inom Azure-nätverket.

> [!NOTE]  
> URI-formatet som används för att referera till skriptet skiljer sig åt beroende på vilken tjänst som används. Använd `wasb://` eller `wasbs://`. För offentligt läsbara URI:er använder `http://` eller `https://`. Använd `adl://`för Lagring av Datasjö.

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Checklista för distribution av en skriptåtgärd

Här är de åtgärder som vidtas när du förbereder distributionen av ett skript:

* Placera filerna som innehåller de anpassade skripten på en plats som är tillgänglig för klusternoderna under distributionen. Till exempel standardlagringen för klustret. Filer kan också lagras i allmänt läsbara värdtjänster.
* Kontrollera att skriptet är idempotent. Om du gör det kan skriptet köras flera gånger på samma nod.
* Använd en tillfällig filkatalog /tmp för att behålla de nedladdade filerna som används av skripten och sedan rensa dem efter att skript har körts.
* Om inställningar på OS-nivå eller konfigurationsfiler för Hadoop-tjänsten ändras kanske du vill starta om HDInsight-tjänster.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Så här kör du en skriptåtgärd

Du kan använda skriptåtgärder för att anpassa HDInsight-kluster med följande metoder:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-mallar
* The HDInsight .NET SDK.

Mer information om hur du använder varje metod finns i [Så här använder du skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Anpassade skriptexempel

Microsoft tillhandahåller exempelskript för att installera komponenter i ett HDInsight-kluster. Se [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md) som en exempelskriptåtgärd.

## <a name="troubleshooting"></a>Felsökning

Följande är fel som du kan stöta på när du använder skript som du har utvecklat:

**Fel:** `$'\r': command not found`. Ibland följt `syntax error: unexpected end of file`av .

*Orsak*: Det här felet uppstår när raderna i ett skript slutar med CRLF. Unix-system förväntar sig bara LF som radslut.

Det här problemet uppstår oftast när skriptet skapas i en Windows-miljö, eftersom CRLF är en vanlig rad som slutar för många textredigerare i Windows.

*Lösning*: Om det är ett alternativ i textredigeraren väljer du Unix-format eller LF för radslutningen. Du kan också använda följande kommandon på ett Unix-system för att ändra CRLF till en LF:

> [!NOTE]  
> Följande kommandon är ungefär likvärdiga genom att de ska ändra CRLF-radändarna till LF. Välj en baserat på de verktyg som är tillgängliga på ditt system.

| Kommando | Anteckningar |
| --- | --- |
| `unix2dos -b INFILE` |Originalfilen säkerhetskopieras med en . BAK förlängning |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE innehåller en version med endast LF-ändelser |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Ändrar filen direkt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE innehåller en version med endast LF-ändelser. |

**Fel:** `line 1: #!/usr/bin/env: No such file or directory`.

*Orsak*: Det här felet uppstår när skriptet sparades som UTF-8 med en strukturlista (Byte Order Mark).

*Lösning*: Spara filen antingen som ASCII eller som UTF-8 utan strukturlista. Du kan också använda följande kommando på ett Linux- eller Unix-system för att skapa en fil utan strukturlistan:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Ersätt `INFILE` med filen som innehåller strukturlistan. `OUTFILE`ska vara ett nytt filnamn som innehåller skriptet utan strukturlistan.

## <a name="next-steps"></a><a name="seeAlso"></a>Nästa steg

* Lär dig hur du [anpassar HDInsight-kluster med hjälp av skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md)
* Använd [HDInsight .NET SDK-referensen](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) om du vill veta mer om hur du skapar .NET-program som hanterar HDInsight
* Använd [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) för att lära dig hur du använder REST för att utföra hanteringsåtgärder på HDInsight-kluster.
