---
title: 'Snabbstart: Kör ett arbetsflöde - Microsoft Genomics'
description: I den här snabbstarten får du se hur du läser in indata i Azure Blob Storage och kör ett arbetsflöde genom Microsoft Genomics-tjänsten.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.openlocfilehash: 05b94ca9bd14392bad5288882a80f5c75590ef7b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "76931832"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Snabbstart: Köra ett arbetsflöde genom Microsoft Genomics-tjänsten

I den här snabbstarten laddar du upp indata till ett Azure Blob-lagringskonto och kör ett arbetsflöde via Microsoft Genomics-tjänsten med hjälp av Python Genomics-klienten. Microsoft Genomics är en skalbar, säker tjänst för sekundär analys som snabbt kan bearbeta ett genom, från råläsningar till produktion av anpassade läsningar och variantanrop. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12+](https://www.python.org/downloads/release/python-2714/) `pip` , med `python` installerat och i systemsökvägen. Microsoft Genomics-klienten är inte kompatibel med Python 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Konfigurera: Skapa ett Microsoft Genomics-konto på Azure Portal

Om du vill skapa ett Microsoft Genomics-konto navigerar du till [Skapa ett genomikkonto](https://portal.azure.com/#create/Microsoft.Genomics) i Azure-portalen. Om du ännu inte har en Azure-prenumeration kan du skapa en innan du skapar ett Microsoft Genomics-konto. 

![Microsoft Genomics på Azure-portalen](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics på Azure-portalen")

Skapa ditt Genomics-konto med följande information (se föregående bild): 

 |**Inställning**          |  **Föreslaget värde**  | **Fältbeskrivning** |
 |:-------------       |:-------------         |:----------            |
 |Prenumeration         | Ditt prenumerationsnamn|Detta är faktureringsenheten för dina Azure-tjänster – mer information om din prenumeration finns under [Prenumerationer](https://account.azure.com/Subscriptions) |      
 |Resursgrupp       | MinResursgrupp       |  Resursgrupper gör att du kan gruppera flera Azure-resurser (lagringskonto, Genomics-konto, o.s.v.) i en enda grupp för enkel hantering. Mer information finns i [Resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Information om giltiga resursgruppnamn finns under [Namngivningsregler](/azure/architecture/best-practices/resource-naming) |
 |Kontonamn         | MittGenomicsKonto     |Välj ett unikt konto-ID. Se [Namngivningsregler](/azure/architecture/best-practices/resource-naming) för giltiga namn |
 |Location                   | USA, västra 2                    |    Tjänsten är tillgänglig i USA, västra 2, Europa, västra och Sydostasien |

Du kan välja **Meddelanden** i den övre menyraden för att övervaka distributionsprocessen.

![Meddelanden](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Meddelanden")

Mer information om Microsoft Genomics finns i [Vad är Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Konfigurera: Installera Microsoft Genomics Python-klienten

Du måste installera både Python och Microsoft Genomics Python-klienten i din lokala miljö. 

### <a name="install-python"></a>Installera Python

Microsoft Genomics Python-klienten är kompatibel med Python 2.7.12 eller en senare version av 2.7.xx. 2.7.14 är den föreslagna versionen. Du hittar nedladdningen [här](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x är inte kompatibelt med Python 2.7.xx.  MSGen är ett Python 2.7-program. När du kör MSGen kontrollerar du att din aktiva Python-miljö använder en 2.7.xx-version av Python. Du kan få ett felmeddelande om du försöker använda MSGen med en 3.x-version av Python.

### <a name="install-the-microsoft-genomics-client"></a>Installera Microsoft Genomics-klienten

Använd `pip` Python för att installera `msgen`Microsoft Genomics-klienten . I följande anvisningar förutsätts Python redan finnas i systemsökvägen. Om du har `pip` problem med installationen som inte känns igen måste du lägga till Python och skriptundermappen i systemsökvägen.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Om du inte vill `msgen` installera som en systemomfattande binär och ändra systemomfattande `–-user` Python-paket använder du flaggan med `pip`.
Om du använder den paketbaserade installationen eller setup.py installeras alla nödvändiga paket. I annat fall är `msgen` de grundläggande paket som krävs för 

 * [Azure-storage](https://pypi.python.org/pypi/azure-storage). 
 * [Requests](https://pypi.python.org/pypi/requests). 

Du kan installera de här paketen med `pip`, `easy_install` eller via `setup.py`-standardprocedurer. 

### <a name="test-the-microsoft-genomics-client"></a>Testa Microsoft Genomics-klienten
Om du vill testa Microsoft Genomics-klienten laddar du ned konfigurationsfilen från ditt Genomics-konto. I Azure-portalen navigerar du till ditt Genomikkonto genom att välja **Alla tjänster** längst upp till vänster och sedan söka efter och välja Genomikkonton.

![Hitta Microsoft Genomics på Azure-portalen](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Hitta Microsoft Genomics på Azure-portalen")

Välj det Genomikkonto du just har gjort, navigera till **Access Keys**och hämta konfigurationsfilen.

![Ladda ner config-fil från Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Ladda ner config-fil från Microsoft Genomics")

Kontrollera att Microsoft Genomics Python-klienten fungerar med följande kommando

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Skapa ett Microsoft Azure Storage-konto 
I Microsoft Genomics-tjänsten förväntas indata lagras som blockblobar i ett Azure Storage-konto. Utdatafilerna skrivs också som blockblobar till en container som angetts av användaren i ett Azure Storage-konto. In- och utdata kan finnas i olika lagringskonton.
Om du redan har data i ett Azure Storage-konto behöver du bara se till att det finns på samma plats som Genomics-kontot. Annars uppstår utgående avgifter när du kör Microsoft Genomics-tjänsten. Om du ännu inte har ett Azure-lagringskonto måste du skapa ett och överföra dina data. Du kan hitta mer information om Azure-lagringskonton [här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), inklusive vad ett lagringskonto är och vilka tjänster det tillhandahåller. Om du vill skapa ett Azure-lagringskonto navigerar du till [Skapa lagringskonto](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) i Azure-portalen.  

![Skapa sida för lagringskonto](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Skapa sida för lagringskonto")

Konfigurera ditt lagringskonto med följande information, som visas i föregående bild. Använd de flesta standardalternativen för ett lagringskonto och ange bara att kontot är BlobStorage, inte allmänt ändamål. Blob-lagring kan vara 2–5 gånger snabbare för ned- och uppladdningar.  Standarddistributionsmodellen, Azure Resource Manager, rekommenderas.  

 |**Inställning**          |  **Föreslaget värde**  | **Fältbeskrivning** |
 |:-------------------------       |:-------------         |:----------            |
 |Prenumeration         | Din Azure-prenumeration |Mer information om din prenumeration finns i [Prenumerationer](https://account.azure.com/Subscriptions) |      
 |Resursgrupp       | MinResursgrupp       |  Du kan välja samma resursgrupp som ditt Genomics-konto. Giltiga resursgruppnamn finns i [Namngivningsregler](/azure/architecture/best-practices/resource-naming) |
 |Lagringskontots namn         | MittLagringskonto     |Välj ett unikt konto-ID. Giltiga namn finns i [Namngivningsregler](/azure/architecture/best-practices/resource-naming) |
 |Location                  | USA, västra 2                  | Använd samma plats som platsen för ditt Genomikkonto, för att minska utgående avgifter och minska svarstiden.  | 
 |Prestanda                  | Standard                   | Standardinställningen är Standard. Mer information om standard- och premiumlagringskonton finns i [Introduktion till Microsoft Azure-lagring](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Typ av konto       | BlobStorage (BlobStorage)       |  Blob-lagring kan vara 2–5 gånger snabbare än lagring generell användning för ned- och uppladdningar. |
 |Replikering                  | Lokalt redundant lagring                  | Med lokalt redundant lagring replikeras dina data i datacentret i den region där du har skapat ditt lagringskonto. Mer information finns i [Azure Storage replication](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Åtkomstnivå                  | Frekvent                   | Frekvent åtkomst indikerar att objekten på lagringskontot kommer att användas oftare.    |

Välj sedan **Granska + skapa** för att skapa ditt lagringskonto. Precis som när du skapar ditt Genomikkonto kan du välja **Meddelanden** i den övre menyraden för att övervaka distributionsprocessen. 

## <a name="upload-input-data-to-your-storage-account"></a>Ladda upp indata till ditt lagringskonto

Microsoft Genomics-tjänsten förväntar sig parade slutarläsningar (fastq- eller bam-filer) som indatafiler. Du kan välja att antingen ladda upp dina egna data eller utforska med offentligt tillgängliga exempeldata som du får. Om du vill använda offentligt tillgängliga exempeldata finns de här:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

I ditt lagringskonto måste du skapa en blob-container för dina indata och en andra blob-container för dina utdata.  Ladda upp indata till blob-containern för indata. Olika verktyg kan användas för att göra detta, inklusive [Microsoft Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/) [BlobPorter](https://github.com/Azure/blobporter)eller [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Köra ett arbetsflöde genom Microsoft Genomics-tjänsten med hjälp av Python-klienten 

Om du vill köra ett arbetsflöde via Microsoft Genomics-tjänsten redigerar du *filen config.txt* för att ange lagringsbehållaren för in- och utdatalagring för dina data.
Öppna *filen config.txt* som du hämtade från ditt Genomics-konto. De avsnitt du behöver ange är din prenumerationsnyckel och de sex objekten längst ned, lagringskontonamnet, nyckeln och behållarnamnet för både indata och utdata. Du hittar den här informationen genom att navigera i Azure-portalen för att **komma åt nycklar** för ditt lagringskonto eller direkt från Azure Storage Explorer.  

![Genomik config](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomik config")

Om du vill köra GATK4 `process_name` ställer `gatk4`du in parametern på .

Som standard visar Genomics-tjänsten VCF-filer. Om du vill ha en gVCF-utdata i `-emitRefConfidence` stället för en VCF-utdata (motsvarande i GATK 3.x `emit-ref-confidence` och i GATK 4.x) lägger du till `emit_ref_confidence` parametern i *config.txt* och ställer in den på `gvcf`, som visas i föregående bild.  Om du vill ändra tillbaka till VCF-utdata tar du antingen `emit_ref_confidence` bort `none`den från *filen config.txt* eller ställer in parametern på . 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Skicka arbetsflödet till Microsoft Genomics-tjänsten

Använd Microsoft Genomics Python-klienten för att skicka ditt arbetsflöde med följande kommando:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

Du kan visa statusen för dina arbetsflöden med hjälp av följande kommando: 
```python
msgen list -f c:\temp\config.txt 
```

När arbetsflödet är klart kan du visa utdatafilerna i ditt Azure-lagringskonto i utdatabehållaren som du har konfigurerat. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln laddade du upp exempeldata till Azure-lagring och `msgen` skickade ett arbetsflöde till Microsoft Genomics-tjänsten via Python-klienten. Mer information om andra indatafiltyper som kan användas med Tjänsten Microsoft Genomics finns på följande sidor: [parat FASTQ](quickstart-input-pair-FASTQ.md) | [BAM](quickstart-input-BAM.md) | [Multiple FASTQ eller BAM](quickstart-input-multiple.md). Du kan även utforska den här självstudien med hjälp av vår [Azure-självstudie som anteckningsbok.](https://aka.ms/genomicsnotebook)
