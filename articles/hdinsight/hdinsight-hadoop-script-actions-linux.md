---
title: Utveckling av skriptåtgärder med Linux-baserat HDInsight - Azure | Microsoft Docs
description: Lär dig hur du använder Bash-skript för att anpassa Linux-baserade HDInsight-kluster. Funktionen skript åtgärd i HDInsight kan du köra skript under eller efter att klustret har skapats. Kan använda skript för att ändra inställningar för klustrets eller installera ytterligare programvara.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: cf4c89cd-f7da-4a10-857f-838004965d3e
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: d5df67021e997df3a6344701f50be4871a11386d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31406038"
---
# <a name="script-action-development-with-hdinsight"></a>Skriptutveckling med HDInsight

Lär dig hur du anpassar ditt HDInsight-kluster med hjälp av Bash-skript. Skriptåtgärder är ett sätt att anpassa HDInsight under eller efter att klustret har skapats.

> [!IMPORTANT]
> Stegen i det här dokumentet kräver ett HDInsight-kluster som använder Linux. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="what-are-script-actions"></a>Vad är skriptåtgärder

Skriptåtgärder är Bash-skript som Azure körs på klusternoderna för att göra konfigurationsändringar eller installera programvara. En skriptåtgärd körs som rot och ger fullständig behörighet till klusternoderna.

Skriptåtgärder kan tillämpas på följande sätt:

| Använd den här metoden för att använda ett skript... | Skapa kluster under... | På ett kluster som körs... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Azure CLI 1.0 |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Azure Resource Manager-mall |✓ |&nbsp; |

Mer information om hur du använder dessa metoder för att tillämpa skriptåtgärder finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="bestPracticeScripting"></a>Metodtips för skriptutveckling av

Finns flera bästa praxis att tänka på när du utvecklar ett anpassat skript för ett HDInsight-kluster:

* [Använder Hadoop-version](#bPS1)
* [Mål OS-Version](#bps10)
* [Ange stabil länkar till skriptresurser](#bPS2)
* [Använda fördefinierade kompilerade resurser](#bPS4)
* [Se till att klustret anpassning skriptet idempotent](#bPS3)
* [Garantera hög tillgänglighet för kluster-arkitektur](#bPS5)
* [Konfigurera anpassade komponenter om du vill använda Azure Blob storage](#bPS6)
* [Skriva information till STDOUT och STDERR](#bPS7)
* [Spara filer i ASCII-format med LF radbrytningar](#bps8)
* [Använda logik för att återställa från tillfälliga fel](#bps9)

> [!IMPORTANT]
> Skriptåtgärder måste slutföras inom 60 minuter eller processen misslyckas. Under noden etableringen körs skriptet samtidigt med andra processer för installation och konfiguration. Konkurrens om resurser, till exempel CPU-tid eller nätverket bandbredd kan göra att skriptet tar längre tid att slutföra än i din utvecklingsmiljö.

### <a name="bPS1"></a>Använder Hadoop-version

Olika versioner av HDInsight har olika versioner av Hadoop-tjänster och komponenter installeras. Om skriptet förväntar en viss version av en tjänst eller en komponent, bör du bara använda skriptet med versionen av HDInsight som innehåller de nödvändiga komponenterna. Du kan hitta information om komponenten-versioner som ingår i HDInsight med hjälp av den [HDInsight component-versioning](hdinsight-component-versioning.md) dokumentet.

### <a name="bps10"></a> Målversionen OS

Linux-baserat HDInsight baseras på Ubuntu Linux-distribution. Olika versioner av HDInsight förlitar sig på olika versioner av Ubuntu som kan ändra hur skriptet fungerar. Till exempel HDInsight 3,4 och tidigare baseras på Ubuntu-versioner som använder Upstart. Version 3.5 och större baseras på Ubuntu 16.04 som använder Systemd. Systemd och Upstart förlitar sig på olika kommandon så att skriptet ska skrivas till fungerar med båda.

En annan viktig skillnad mellan HDInsight 3.4 och 3.5 är att `JAVA_HOME` nu pekar på Java 8.

Du kan kontrollera versionen av Operativsystemet med hjälp av `lsb_release`. Följande kod visar hur du avgör om skriptet körs på Ubuntu 14 eller 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
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

Du hittar den fullständig skript som innehåller dessa kodavsnitt på https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh.

Versionen av Ubuntu som används av HDInsight finns i [HDInsight komponentversionen](hdinsight-component-versioning.md) dokumentet.

Om du vill förstå skillnaderna mellan Systemd och Upstart finns [Systemd för Upstart användare](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="bPS2"></a>Ange stabil länkar till skriptresurser

Skript och associerade resurser måste vara tillgängliga under hela livslängden för klustret. Dessa resurser krävs om nya noder läggs till i klustret under skalning åtgärder.

Det bästa sättet är att hämta och arkivera allt innehåll i ett Azure Storage-konto på din prenumeration.

> [!IMPORTANT]
> Storage-konto som används måste vara standardkontot för lagring för klustret eller en offentlig, skrivskyddad behållare för andra storage-konto.

Till exempel exemplen som tillhandahålls av Microsoft lagras i den [ https://hdiconfigactions.blob.core.windows.net/ ](https://hdiconfigactions.blob.core.windows.net/) storage-konto. Den här platsen är en offentlig, skrivskyddad behållare som underhålls av HDInsight-teamet.

### <a name="bPS4"></a>Använda fördefinierade kompilerade resurser

Undvik åtgärder som sammanställer resurser från källkod för att minska den tid det tar för att köra skriptet. Till exempel före kompilera resurser och lagra dem i en blob med Azure Storage-konto i samma datacenter som HDInsight.

### <a name="bPS3"></a>Se till att klustret anpassning skriptet idempotent

Skript måste vara idempotent. Om skriptet körs flera gånger, måste den returnera klustret till samma tillstånd varje gång.

Till exempel ett skript som ändrar konfigurationsfiler bör inte lägga till dubblettposter om körde flera gånger.

### <a name="bPS5"></a>Garantera hög tillgänglighet för kluster-arkitektur

Linux-baserade HDInsight-kluster tillhandahåller två huvudnoderna som är aktiva i klustret och skriptåtgärder köras på båda noderna. Om de komponenter du installerar förväntas bara en huvudnod kan du inte installera komponenterna på båda huvudnoderna.

> [!IMPORTANT]
> Tjänster som tillhandahålls som en del av HDInsight är utformade för att växla över mellan två huvudnoderna efter behov. Den här funktionen har inte utökats till anpassade komponenter som installeras via skriptåtgärder. Om du behöver hög tillgänglighet för anpassade komponenter måste du implementera en egen mekanism för växling vid fel.

### <a name="bPS6"></a>Konfigurera anpassade komponenter om du vill använda Azure Blob storage

Komponenter som installeras på klustret kan ha en standardkonfiguration som använder Hadoop Distributed File System (HDFS) lagring. HDInsight använder antingen Azure Storage eller Azure Data Lake Store som standardlagring. Båda ger ett kompatibelt HDFS-filsystem som kvarstår data även om klustret har tagits bort. Du kan behöva konfigurera komponenter som du installerar för att använda WASB eller ADL i stället för HDFS.

Du behöver inte ange filsystemet för de flesta åtgärder. Till exempel följande giraph examples.jar filen kopieras från det lokala filsystemet till klusterlagringen:

```bash
hdfs dfs -put /usr/hdp/current/giraph/giraph-examples.jar /example/jars/
```

I detta exempel på `hdfs` kommando använder transparent standard klusterlagringen. Du kan behöva ange URI för vissa åtgärder. Till exempel `adl:///example/jars` för Data Lake Store eller `wasb:///example/jars` för Azure Storage.

### <a name="bPS7"></a>Skriva information till STDOUT och STDERR

HDInsight loggar utdata från skriptet som skrivs till STDOUT och STDERR. Du kan visa den här informationen med Ambari-webbgränssnittet.

> [!NOTE]
> Ambari är endast tillgänglig om klustret har skapats. Om du använder en skriptåtgärd under klustret har skapats och skapa misslyckas, finns i avsnittet om felsökning [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting) för andra sätt att komma åt loggade informationen.

De flesta verktyg och Installationspaketen skriva redan information till STDOUT och STDERR, men du kanske vill lägga till ytterligare loggning. Om du vill skicka text STDOUT använda `echo`. Exempel:

```bash
echo "Getting ready to install Foo"
```

Som standard `echo` skickar strängen till STDOUT. Om du vill styra den till STDERR, lägger du till `>&2` innan `echo`. Exempel:

```bash
>&2 echo "An error occurred installing Foo"
```

Detta omdirigerar information skrivs till STDOUT till STDERR (2) i stället. Mer information om i/o-omdirigering finns [ http://www.tldp.org/LDP/abs/html/io-redirection.html ](http://www.tldp.org/LDP/abs/html/io-redirection.html).

Mer information om hur du visar information som loggas av skriptåtgärder finns [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md#troubleshooting)

### <a name="bps8"></a> Spara filer i ASCII-format med LF radbrytningar

Bash-skript ska lagras som ASCII-format med rader som avslutas av LF. Filer som lagras som UTF-8 eller använda CRLF som rad avslutas kan misslyckas med följande fel:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="bps9"></a> Använda logik för att återställa från tillfälliga fel

När du laddar ned filer som installerar paket med hjälp av lgh get eller andra åtgärder som överför data via internet, misslyckas åtgärden på grund av tillfälliga nätverksfel. Till exempel kanske du kommunicerar med fjärresursen håller på att inte körs på en nod för säkerhetskopiering.

Du kan implementera logik för att göra ditt skript flexibel att tillfälligt fel. Följande funktion visar hur du implementerar logik. Den försöker igen tre gånger innan åtgärden misslyckas.

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

## <a name="helpermethods"></a>Hjälpmetoder för anpassade skript

Skriptet åtgärd helper metoder är verktyg som du kan använda vid skrivning till anpassade skript. Metoderna finns i den[ https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh ](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skript. Använd följande för att hämta och använda dem som en del av skriptet:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Följande hjälpfiler tillgängligt för användning i skriptet:

| Helper användning | Beskrivning |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Hämtar en fil från käll-URI: N till den angivna sökvägen. Som standard den inte över en befintlig fil. |
| `untar_file TARFILE DESTDIR` |Extraherar tar-filen (med hjälp av `-xf`) i målkatalogen. |
| `test_is_headnode` |Om kördes på en klustrets huvudnod, returnerar 1. annars 0. |
| `test_is_datanode` |Om den aktuella noden är en datanod (worker), returnera 1; annars 0. |
| `test_is_first_datanode` |Om den aktuella noden är den första data (worker) nod (namngivna workernode0) returnerar 1. annars 0. |
| `get_headnodes` |Returnera fullständigt kvalificerade domännamnet för headnodes i klustret. Namn är kommaavgränsade. En tom sträng returneras vid fel. |
| `get_primary_headnode` |Hämtar fullständigt kvalificerade domännamnet för den primära headnode. En tom sträng returneras vid fel. |
| `get_secondary_headnode` |Hämtar fullständigt kvalificerade domännamnet för den sekundära headnode. En tom sträng returneras vid fel. |
| `get_primary_headnode_number` |Hämtar den primära headnode numeriskt suffix. En tom sträng returneras vid fel. |
| `get_secondary_headnode_number` |Hämtar den sekundära headnode numeriskt suffix. En tom sträng returneras vid fel. |

## <a name="commonusage"></a>Vanliga användningsmönster

Det här avsnittet ger vägledning om att implementera några av de vanliga användningsmönster som kan uppstå vid skrivning till ett eget skript.

### <a name="passing-parameters-to-a-script"></a>Skicka parametrar till ett skript

I vissa fall, kan skriptet kräver parametrar. Du kan till exempel behöva administratörslösenordet för klustret när du använder Ambari REST API.

Parametrar för skriptet kallas *positionsparametrarna*, och tilldelas `$1` för den första parametern `$2` för andra, och så på. `$0` innehåller namnet på själva skriptet.

Värden har överförts till skriptet som parametrar ska omges av enkla citattecken ('). På så sätt att det angivna värdet behandlas som en literal.

### <a name="setting-environment-variables"></a>Ställa in miljövariabler

Ange en miljövariabel utförs av följande sats:

    VARIABLENAME=value

Där VARIABELNAMN är namnet på variabeln. Om du vill få åtkomst till variabeln måste använda `$VARIABLENAME`. Om du vill tilldela ett värde som tillhandahålls av en positionsparametrarna parameter som miljövariabeln lösenord skulle du till exempel använda följande sats:

    PASSWORD=$1

Efterföljande åtkomst till informationen kan sedan använda `$PASSWORD`.

Miljövariabler som anges i skriptet kan bara finnas inom omfånget för skriptet. I vissa fall kan behöva du lägga till systemomfattande miljövariabler som behålls när skriptet har slutförts. Lägga till variabeln för att lägga till systemomfattande miljövariabler `/etc/environment`. Till exempel följande uttryck lägger till `HADOOP_CONF_DIR`:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Åtkomst till platser där anpassade skript lagras

Skript som används för att anpassa ett kluster måste lagras i något av följande platser:

* En __Azure Storage-konto__ som är associerad med klustret.

* En __ytterligare lagringskonto__ som är associerade med klustret.

* En __offentligt läsbar URI__. Till exempel en URL till data som lagras på OneDrive, Dropbox eller andra filer som är värd för tjänsten.

* En __Azure Data Lake Store-konto__ som är associerad med HDInsight-klustret. Mer information om hur du använder Azure Data Lake Store med HDInsight finns [skapar ett HDInsight-kluster med Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    > [!NOTE]
    > Tjänstens huvudnamn HDInsight använder för åtkomst till Data Lake Store måste ha läsbehörighet till skriptet.

Resurser som används av skriptet måste också vara offentligt tillgängliga.

Lagra filer i en Azure Storage-konto eller ett Azure Data Lake Store ger snabb åtkomst som både i Azure-nätverk.

> [!NOTE]
> URI-format som används för att referera till skriptet varierar beroende på vilken tjänst som används. Storage-konton som är associerade med HDInsight-klustret kan använda `wasb://` eller `wasbs://`. Offentligt läsbar URI: er använda `http://` eller `https://`. Data Lake Store använder `adl://`.

### <a name="checking-the-operating-system-version"></a>Kontrollerar operativsystemets version

Olika versioner av HDInsight är beroende av specifika versioner av Ubuntu. Det kan finnas skillnader mellan OS-versioner måste du kontrollera i skriptet. Du kan behöva installera en binärfil som är kopplade till versionen av Ubuntu.

För att kontrollera versionen av Operativsystemet, använda `lsb_release`. Till exempel visar följande skript hur du refererar till en specifik tar-filen beroende på OS-version:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS verion is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

## <a name="deployScript"></a>Checklista för distribution av en skriptåtgärd

Här följer stegen ta när du förbereder att distribuera ett skript:

* Placera de filer som innehåller anpassade skript på en plats som kan nås av klusternoder under distributionen. Till exempel standardlagring för klustret. Filer kan också lagras i offentligt läsbar värdtjänster.
* Kontrollera att skriptet är idempotent. På så sätt kan skriptet ska köras flera gånger på samma nod.
* Använd en tillfällig katalog /tmp för att hålla de hämtade filer som används av skripten och sedan rensa dem efter skript har körts.
* Om inställningar för OS-nivå eller konfigurationsfiler för Hadoop-tjänsten ändras, kan du vill starta om HDInsight-tjänster.

## <a name="runScriptAction"></a>Så här kör du en skriptåtgärd

Använd skriptåtgärder om du vill anpassa HDInsight-kluster med följande metoder:

* Azure Portal
* Azure PowerShell
* Azure Resource Manager-mallar
* HDInsight .NET SDK.

Mer information om hur du använder varje metod finns [hur du använder skriptåtgärd](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="sampleScripts"></a>Anpassade skriptexempel

Microsoft tillhandahåller exempelskript för att installera komponenterna på ett HDInsight-kluster. Se följande länkar för flera exempel skriptåtgärder.

* [Installera och använda Hue på HDInsight-kluster](hdinsight-hadoop-hue-linux.md)
* [Installera och använda Solr på HDInsight-kluster](hdinsight-hadoop-solr-install-linux.md)
* [Installera och använda Giraph på HDInsight-kluster](hdinsight-hadoop-giraph-install-linux.md)
* [Installera eller uppgradera Mono på HDInsight-kluster](hdinsight-hadoop-install-mono.md)

## <a name="troubleshooting"></a>Felsökning

Följande är fel som kan uppstå när du använder skript som du har utvecklat:

**Fel**: `$'\r': command not found`. Ibland följt av `syntax error: unexpected end of file`.

*Orsak*: det här felet uppstår om raderna i ett skript som avslutas med CRLF. UNIX-system förväntar sig endast LF som rad avslutas.

Det här problemet inträffar oftast när skriptet har skapats på en Windows-miljö som CRLF är en gemensam rader som slutar med för många textredigerare i Windows.

*Lösning*: om det är ett alternativ i en textredigerare, Välj Unix-format eller LF för raden avslutas. Du kan även använda följande kommandon på ett Unix-system för att ändra CRLF till en LF:

> [!NOTE]
> Följande kommandon är ungefär eftersom de bör ändra radbrytningar CRLF till LF. Välj en baserat på Verktyg som finns på datorn.

| Kommando | Anteckningar |
| --- | --- |
| `unix2dos -b INFILE` |Den ursprungliga filen säkerhetskopieras med en. BAK-tillägg |
| `tr -d '\r' < INFILE > OUTFILE` |UTFIL innehåller en version med endast LF ändelser |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Ändrar filen direkt |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |UTFIL innehåller en version med endast LF ändelser. |

**Fel**: `line 1: #!/usr/bin/env: No such file or directory`.

*Orsak*: det här felet uppstår när skriptet har sparats som UTF-8 en Byte ordning markering (BOM).

*Lösning*: spara filen som ASCII eller som UTF-8 utan en struktur. Du kan också använda följande kommando på en Linux eller Unix-system för att skapa en fil utan Strukturen:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Ersätt `INFILE` med den fil som innehåller Strukturen. `OUTFILE` måste vara ett nytt filnamn som innehåller skriptet utan Strukturen.

## <a name="seeAlso"></a>Nästa steg

* Lär dig hur du [anpassa HDInsight-kluster med skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md)
* Använd den [HDInsight .NET SDK referens](https://msdn.microsoft.com/library/mt271028.aspx) vill veta mer om hur du skapar .NET-program som hanterar HDInsight
* Använd den [HDInsight REST API](https://msdn.microsoft.com/library/azure/mt622197.aspx) att lära dig hur du använder REST för att utföra hanteringsåtgärder på HDInsight-kluster.
