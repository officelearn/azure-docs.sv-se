---
title: Skriptåtgärdsutveckling med Linux-baserade HDInsight - Azure
description: Lär dig hur du använder Bash-skript för att anpassa Linux-baserade HDInsight-kluster. Funktionen för åtgärden skriptet i HDInsight kan du köra skript under eller när klustret har skapats. Skript kan användas för att ändra inställningar för klustrets eller installera ytterligare programvara.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: 6702bfabd27b56ce473fe8eb14aadc9dc6e8e7c9
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2019
ms.locfileid: "56338877"
---
# <a name="script-action-development-with-hdinsight"></a>Skriptåtgärdsutveckling med HDInsight

Lär dig hur du anpassar ditt HDInsight-kluster med Bash-skript. Skriptåtgärder är ett sätt att anpassa HDInsight under eller när klustret har skapats.

> [!IMPORTANT]  
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Vad är skriptåtgärder

Skriptåtgärder är Bash-skript som Azure körs på noderna i klustret för att kontrollera konfigurationsändringar eller installera programvara. En skriptåtgärd körs som rot och ger fullständiga åtkomsträttigheter till klusternoderna.

Skriptåtgärder kan tillämpas på följande sätt:

| Du kan använda den här metoden för att tillämpa ett skript... | Skapa kluster under... | På ett aktivt kluster... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klassisk Azure CLI |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-mall |✓ |&nbsp; |

Mer information om hur du använder dessa metoder för att tillämpa skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Metodtips för skriptutveckling av

När du utvecklar ett anpassat skript för ett HDInsight-kluster kan finns det flera bästa praxis att tänka på:

* [Använder Apache Hadoop-version](#bPS1)
* [Target OS-Version](#bps10)
* [Ange stabil länkar till skriptresurser](#bPS2)
* [Använd förkompilerad resurser](#bPS4)
* [Kontrollera att klustret anpassning skriptet är idempotent](#bPS3)
* [Garantera hög tillgänglighet för klusterarkitektur](#bPS5)
* [Konfigurera anpassade komponenter om du vill använda Azure Blob storage](#bPS6)
* [Skriva information till STDOUT- och STDERR](#bPS7)
* [Spara filer i ASCII-format med LF radbrytningar](#bps8)
* [Använda logik för återförsök för att återställa från tillfälliga fel](#bps9)

> [!IMPORTANT]  
> Skriptåtgärder måste slutföras inom 60 minuter eller processen misslyckas. Under noden etablering, körs skriptet samtidigt med andra processer för installation och konfiguration. Konkurrens om resurser, till exempel CPU-tid eller nätverk bandbredd kan orsaka skriptet tar längre tid att slutföra än i din utvecklingsmiljö.

### <a name="bPS1"></a>Använder Apache Hadoop-version

Olika versioner av HDInsight har olika versioner av Hadoop-tjänster och komponenter som är installerade. Om skriptet förväntar sig en specifik version av en tjänst eller en komponent, bör du endast använda skriptet med versionen av HDInsight som innehåller komponenterna som krävs. Du kan hitta information om komponenten-versioner som ingår i HDInsight med hjälp av den [versionshantering för HDInsight](hdinsight-component-versioning.md) dokumentet.

### <a name="bps10"></a> Rikta in OS-version

Linux-baserade HDInsight baseras på Ubuntu Linux-distribution. Olika versioner av HDInsight förlitar sig på olika versioner av Ubuntu och som kan ändra hur skriptet fungerar. Till exempel HDInsight 3.4 och tidigare bygger på Ubuntu-versioner som använder Upstart. Version 3.5 och större baseras på Ubuntu 16.04 som använder Systemd. Systemd och Upstart förlitar sig på olika kommandon, så att skriptet ska skrivas till fungerar med båda.

En annan viktig skillnad mellan HDInsight 3.4 och 3.5 är att `JAVA_HOME` pekar nu på Java 8.

Du kan kontrollera versionen av Operativsystemet med hjälp av `lsb_release`. Följande kod visar hur du avgör om skriptet körs på Ubuntu 14 eller 16:

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

Du hittar den fullständiga skript som innehåller dessa kodfragment på https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Versionen av Ubuntu som används av HDInsight finns i [HDInsight komponentversionen](hdinsight-component-versioning.md) dokumentet.

Information om skillnaderna mellan Systemd och Upstart finns i [Systemd för Upstart användare](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Ange stabil länkar till skriptresurser

Skript och associerade resurser måste vara tillgängliga under hela klustrets livslängd. De här resurserna krävs om nya noder läggs till i klustret under skalningsåtgärder.

Det bästa sättet är att hämta och arkivera allt i ett Azure Storage-konto på din prenumeration.

> [!IMPORTANT]  
> Lagringskontot som används måste vara standardkontot för lagring för klustret eller en offentlig, skrivskyddade behållare för andra storage-konto.

Till exempel de exempel som tillhandahålls av Microsoft lagras i den [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) storage-konto. Den här platsen är en offentlig, skrivskyddade behållare som underhålls av HDInsight-teamet.

### <a name="bPS4"></a>Använd förkompilerad resurser

Undvik åtgärder som kompilera resurser från källkoden för att minska den tid det tar för att köra skriptet. Till exempel förväg kompilera resurser och lagra dem i en Azure Storage-konto blob i samma datacenter som HDInsight.

### <a name="bPS3"></a>Kontrollera att klustret anpassning skriptet är idempotent

Skript måste vara idempotenta. Om skriptet körs flera gånger, bör den returnera klustret till samma tillstånd varje gång.

Till exempel ett skript som ändrar konfigurationsfiler bör inte lägga till dubblettposter om körts flera gånger.

### <a name="bPS5"></a>Garantera hög tillgänglighet för klusterarkitektur

Linux-baserade HDInsight-kluster tillhandahåller två huvudnoder som är aktiva i klustret och skriptåtgärder kör på båda noderna. Om de komponenter som du installerar räknar bara en huvudnod, installera inte komponenterna på båda huvudnoder.

> [!IMPORTANT]  
> Tjänster som tillhandahålls som en del av HDInsight är utformade för att växla över mellan två huvudnoder efter behov. Den här funktionen utökas inte till anpassade komponenter installeras via skriptåtgärder. Om du behöver hög tillgänglighet för anpassade komponenter måste du implementera en egen mekanism för redundans.

### <a name="bPS6"></a>Konfigurera anpassade komponenter om du vill använda Azure Blob storage

Komponenter som du installerar på klustret kan ha en standardkonfiguration som använder Apache Hadoop Distributed File System (HDFS) lagring. HDInsight använder Azure Storage eller Data Lake Storage som standardlagring. Båda ger en kompatibel HDFS-filsystemets som data finns kvar även om klustret tas bort. Du kan behöva konfigurera komponenter som du installerar för WASB eller ADL istället för HDFS.

Du behöver inte ange filsystemet för de flesta åtgärder. Till exempel följande giraph-examples.jar filen kopieras från det lokala filsystemet till klusterlagringen:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

I det här exemplet på `hdfs` kommando använder transparent standardklusterlagringen. För vissa åtgärder, kan du behöva ange URI: N. Till exempel `adl:///example/jars` för Azure Data Lake Storage Gen1 `abfs:///example/jars` för Data Lake Storage Gen2 eller `wasb:///example/jars` för Azure Storage.

### <a name="bPS7"></a>Skriva information till STDOUT- och STDERR

HDInsight skriptutdata som skrivs till STDOUT- och STDERR-loggar. Du kan visa den här informationen med hjälp av Ambari-webbgränssnittet.

> [!NOTE]  
> Apache Ambari är endast tillgänglig om klustret har skapats. Om du använder en skriptåtgärd under klusterskapandet och skapa misslyckas i felsökningsavsnittet [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) andra sätt för att komma åt information som loggas.

De flesta verktyg och Installationspaketen skriva redan information till STDOUT- och STDERR, men du kanske vill lägga till ytterligare loggning. Använda för att skicka text STDOUT `echo`. Exempel:

```bash
echo "Getting ready to install Foo"
```

Som standard `echo` skickar strängen till STDOUT. Om du vill styra den till STDERR, lägger du till `>&2` innan `echo`. Exempel:

```bash
>&2 echo "An error occurred installing Foo"
```

Detta omdirigerar information skrivs till STDOUT till STDERR (2) i stället. Mer information om i/o-omdirigering finns i [ https://www.tldp.org/LDP/abs/html/io-redirection.html ](https://www.tldp.org/LDP/abs/html/io-redirection.html).

Mer information om hur du visar information som loggas av skriptåtgärder finns i [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Spara filer i ASCII-format med LF radbrytningar

Bash-skript ska lagras som ASCII-format med rader som avslutades av LF. Filer som lagras som UTF-8 eller använder CRLF som raden slutar misslyckas med följande fel:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Använda logik för återförsök för att återställa från tillfälliga fel

När du laddar ned filer, installera paket med apt-get eller andra åtgärder som överför data via internet, misslyckas åtgärden på grund av tillfälliga nätverksfel. Exempelvis kanske fjärresursen du kommunicerar med håller på att redundansväxla till en säkerhetskopiering nod.

För att göra ditt skript elastiska för tillfälliga fel, kan du implementera logik för omprövning. Följande funktion visar hur du implementerar logik för omprövning. Ett nytt försök görs igen tre gånger innan åtgärden misslyckas.

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

## <a name="helpermethods"></a>Hjälpkomponentmetoder för anpassade skript

Skriptet åtgärd hjälpmetoder är verktyg som du kan använda när du skriver anpassade skript. Dessa metoder finns i den [ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skript. Använd följande för att hämta och använda dem som en del av skriptet:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Följande hjälpfiler tillgängliga för användning i ditt skript:

| Helper-användning | Beskrivning |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Hämtar en fil från käll-URI: N till den angivna filsökvägen. Som standard den inte över en befintlig fil. |
| `untar_file TARFILE DESTDIR` |Extraherar tar-filen (med hjälp av `-xf`) i målkatalogen. |
| `test_is_headnode` |Om kördes på en klustrets huvudnod, returnerar 1. annars 0. |
| `test_is_datanode` |Om den aktuella noden är en datanod (worker), returnera en 1; annars 0. |
| `test_is_first_datanode` |Om den aktuella noden är den första data (worker) nod (namngivna workernode0) returnerar en 1; annars 0. |
| `get_headnodes` |Returnera det fullständigt kvalificerade domännamnet för huvudnoderna i klustret. Namnen är kommaavgränsade. En tom sträng returneras vid fel. |
| `get_primary_headnode` |Hämtar det fullständigt kvalificerade domännamnet för den primära huvudnoden. En tom sträng returneras vid fel. |
| `get_secondary_headnode` |Hämtar det fullständigt kvalificerade domännamnet för den sekundära huvudnoden. En tom sträng returneras vid fel. |
| `get_primary_headnode_number` |Hämtar du numeriskt suffix för den primära huvudnoden. En tom sträng returneras vid fel. |
| `get_secondary_headnode_number` |Hämtar du numeriskt suffix för den sekundära huvudnoden. En tom sträng returneras vid fel. |

## <a name="commonusage"></a>Vanliga mönster

Det här avsnittet innehåller anvisningar om hur du implementerar några av de vanliga användningsmönster som kan uppstå vid skrivning till ett eget skript.

### <a name="passing-parameters-to-a-script"></a>Skicka parametrar till ett skript

I vissa fall kan ditt skript kräver parametrar. Du kan till exempel behöva administratörslösenordet för klustret när du använder Ambari REST API.

Parametrarna som skickades till skriptet kallas *positionsparametrar*, och tilldelas till `$1` för den första parametern `$2` för andra, och så är på. `$0` innehåller namnet på själva skriptet.

Värden skickas till skriptet som parametrar ska omges av enkla citattecken ('). Detta innebär att det angivna värdet behandlas som en literal.

### <a name="setting-environment-variables"></a>Ange miljövariabler

Ange en miljövariabel utförs med följande uttryck:

    VARIABLENAME=value

Där VARIABLENAME är namnet på variabeln. Om du vill få åtkomst till variabeln måste använda `$VARIABLENAME`. Om du vill tilldela ett värde som tillhandahålls av en namngivna parametern som ett lösenord för miljövariabeln, skulle du till exempel använda följande uttryck:

    PASSWORD=$1

Efterföljande åtkomst till informationen kan sedan använda `$PASSWORD`.

Miljövariabler som anges i skriptet finns bara inom omfånget för skriptet. I vissa fall kan behöva du lägga till systemomfattande miljövariabler som behålls när skriptet har slutförts. Lägg till systemomfattande miljövariabler genom att lägga till variabeln till `/etc/environment`. Till exempel följande uttryck lägger till `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Åtkomst till platser där de anpassade skript lagras

Skript som används för att anpassa ett kluster behöver lagras i något av följande platser:

* En __Azure Storage-konto__ som är associerad med klustret.

* En __ytterligare lagringskonto__ kopplat till klustret.

* En __offentligt läsbara URI__. Till exempel en URL till data som lagras på OneDrive, Dropbox eller andra filer som är värd för tjänsten.

* En __Azure Data Lake-standardlagringskontot__ som är associerad med HDInsight-klustret. Mer information om hur du använder Azure Data Lake Storage med HDInsight finns i [snabbstarten: Konfigurera kluster i HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > Tjänstens huvudnamn HDInsight använder för att få åtkomst till Data Lake Storage måste ha läsbehörighet till skriptet.

Resurser som används av skriptet måste också vara offentligt tillgängliga.

Lagra filer i ett Azure Storage-konto eller Azure Data Lake Storage ger snabb åtkomst, som båda i Azure-nätverket.

> [!NOTE]  
> URI-format som används för att referera till skriptet skiljer sig beroende på tjänsten som används. Storage-konton som är associerade med HDInsight-kluster kan använda `wasb://` eller `wasbs://`. Offentligt läsbara URI: er använder `http://` eller `https://`. Data Lake Storage använder `adl://`.

### <a name="checking-the-operating-system-version"></a>Kontrollera versionen av operativsystemet

Olika versioner av HDInsight är beroende av specifika versioner av Ubuntu. Det kan finnas skillnader mellan OS-versioner måste du söka efter i ditt skript. Du kan behöva installera en binärfil som är kopplad till versionen av Ubuntu.

För att kontrollera versionen av Operativsystemet, använda `lsb_release`. Till exempel visar följande skript hur du refererar till en specifik tar-filen beroende på OS-version:

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

## <a name="deployScript"></a>Checklista för distribution av en skriptåtgärd

Här följer stegen gör när du förbereder att distribuera ett skript:

* Placera de filer som innehåller anpassade skript på en plats som kan nås av noderna i klustret under distributionen. Till exempel standardlagringen för klustret. Filer kan också lagras i offentligt läsbara värdtjänster.
* Kontrollera att skriptet är idempotent. På så sätt kan skript som ska köras flera gånger på samma nod.
* Använda en tillfällig katalog/tmp för att hålla de nedladdade filer som används av skripten och sedan rensa dem när skript har körts.
* Om inställningar för OS eller konfigurationsfiler för Hadoop-tjänsten ändras kan du starta om HDInsight-tjänsterna.

## <a name="runScriptAction"></a>Så här kör du en skriptåtgärd

Du kan använda skriptåtgärder för att anpassa HDInsight-kluster med hjälp av följande metoder:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-mallar
* HDInsight .NET SDK.

Mer information om hur du använder varje metod finns i [hur du använder skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Anpassade skriptexempel

Microsoft tillhandahåller exempelskript för att installera komponenter på ett HDInsight-kluster. Se följande länkar för mer exempel skriptåtgärder.

* [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md)
* [Installera och använda Apache Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md)
* [Installera och använda Apache Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md)
* [Installera eller uppgradera Mono på HDInsight-kluster](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Felsökning

Här följer några fel som kan uppstå när du använder skript som du har utvecklat:

**Fel**: `$'\r': command not found`. Ibland följt av `syntax error: unexpected end of file`.

*Orsak*: Det här felet uppstår om rader i ett skript som avslutas med CRLF. UNIX-system förväntar sig bara LF som raden slutar.

Det här problemet uppstår oftast när skriptet har skapats på en Windows-miljö, eftersom CRLF är en gemensam hållning slutar för många textredigerare på Windows.

*Upplösning*: Om det är ett alternativ i textredigerare, Välj Unix-format eller LF för raden slutar. Du kan också använda följande kommandon på ett Unix-system för att ändra CRLF till en LF:

> [!NOTE]  
> Följande kommandon kan grovt jämföras i att de ska ändra radbrytningar CRLF till LF. Välj en baserat på Verktyg som är tillgängliga på datorn.

| Kommando | Anteckningar |
| --- | --- |
| `unix2dos -b INFILE` |Den ursprungliga filen säkerhetskopieras med en. BAK-tillägg |
| `tr -d '\r' < INFILE > OUTFILE` |UTFIL innehåller en version med endast LF ändelser |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Ändrar-filen direkt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |UTFIL innehåller en version med endast LF ändelser. |

**Fel**: `line 1: #!/usr/bin/env: No such file or directory`.

*Orsak*: Det här felet uppstår när skriptet har sparats som UTF-8 med en Byte (BOM Order Mark).

*Upplösning*: Spara filen som ASCII eller som UTF-8 utan en struktur. Du kan också använda följande kommando i ett Linux- eller Unix-system för att skapa en fil utan Strukturen:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Ersätt `INFILE` med filen som innehåller Strukturen. `OUTFILE` ska vara ett nytt filnamn som innehåller skriptet som utan Strukturen.

## <a name="seeAlso"></a>Nästa steg

* Lär dig hur du [anpassa HDInsight-kluster med skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md)
* Använd den [HDInsight .NET SDK-referensen](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) mer information om hur du skapar .NET-program som hanterar HDInsight
* Använd den [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) att lära dig hur du använder REST för att utföra hanteringsåtgärder på HDInsight-kluster.
