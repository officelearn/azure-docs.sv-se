---
title: 'Snabb start: köra ett arbets flöde – Microsoft Genomics'
description: I den här snabbstarten får du se hur du läser in indata i Azure Blob Storage och kör ett arbetsflöde genom Microsoft Genomics-tjänsten.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: quickstart
ms.date: 01/11/2019
ms.custom: tracking-python
ms.openlocfilehash: 167bcf4364b88529256b79574c6b8c03098fed02
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/09/2020
ms.locfileid: "84607133"
---
# <a name="quickstart-run-a-workflow-through-the-microsoft-genomics-service"></a>Snabbstart: Köra ett arbetsflöde genom Microsoft Genomics-tjänsten

I den här snabb starten laddar du upp indata till ett Azure Blob Storage-konto och kör ett arbets flöde via Microsoft Genomics tjänsten med hjälp av python-klienten för python. Microsoft Genomics är en skalbar, säker tjänst för sekundär analys som snabbt kan bearbeta ett genom, från råläsningar till produktion av anpassade läsningar och variantanrop. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Python 2.7.12 +](https://www.python.org/downloads/release/python-2714/), med `pip` installerat och `python` i din system Sök väg. Den Microsoft Genomics klienten är inte kompatibel med python 3. 

## <a name="set-up-create-a-microsoft-genomics-account-in-the-azure-portal"></a>Konfigurera: Skapa ett Microsoft Genomics-konto på Azure Portal

Om du vill skapa ett Microsoft Genomics konto går du till [skapa ett genomik-konto](https://portal.azure.com/#create/Microsoft.Genomics) i Azure Portal. Om du ännu inte har en Azure-prenumeration kan du skapa en innan du skapar ett Microsoft Genomics-konto. 

![Microsoft Genomics på Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-create-blade.png "Microsoft Genomics på Azure Portal")

Skapa ditt Genomics-konto med följande information (se föregående bild): 

 |**Inställning**          |  **Föreslaget värde**  | **Fältbeskrivning** |
 |:-------------       |:-------------         |:----------            |
 |Prenumeration         | Ditt prenumerationsnamn|Detta är faktureringsenheten för dina Azure-tjänster – mer information om din prenumeration finns under [Prenumerationer](https://account.azure.com/Subscriptions) |      
 |Resursgrupp       | MinResursgrupp       |  Resursgrupper gör att du kan gruppera flera Azure-resurser (lagringskonto, Genomics-konto, o.s.v.) i en enda grupp för enkel hantering. Mer information finns i [Resursgrupper](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups). Information om giltiga resursgruppnamn finns under [Namngivningsregler](/azure/architecture/best-practices/resource-naming) |
 |Kontonamn         | MittGenomicsKonto     |Välj ett unikt konto-ID. Se [Namngivningsregler](/azure/architecture/best-practices/resource-naming) för giltiga namn |
 |Location                   | USA, västra 2                    |    Tjänsten är tillgänglig i USA, västra 2, Europa, västra och Sydostasien |

Du kan välja **meddelanden** i det övre meny fältet för att övervaka distributions processen.

![Meddelanden](./media/quickstart-run-genomics-workflow-portal/genomics-notifications-box1.png "Meddelanden")

Mer information om Microsoft Genomics finns i [Vad är Microsoft Genomics?](overview-what-is-genomics.md)

## <a name="set-up-install-the-microsoft-genomics-python-client"></a>Konfigurera: Installera Microsoft Genomics Python-klienten

Du måste installera både python och Microsoft Genomics python-klienten i din lokala miljö. 

### <a name="install-python"></a>Installera Python

Microsoft Genomics python-klienten är kompatibel med python 2.7.12 eller en senare version av 2.7. xx. 2.7.14 är den föreslagna versionen. Du hittar nedladdningen [här](https://www.python.org/downloads/release/python-2714/). 

> [!IMPORTANT]
> Python 3.x är inte kompatibelt med Python 2.7.xx.  MSGen är ett Python 2.7-program. När du kör MSGen kontrollerar du att din aktiva Python-miljö använder en 2.7.xx-version av Python. Du kan få ett felmeddelande om du försöker använda MSGen med en 3.x-version av Python.

### <a name="install-the-microsoft-genomics-client"></a>Installera Microsoft Genomics-klienten

Använd python `pip` för att installera Microsoft Genomics-klienten `msgen` . I följande anvisningar förutsätts Python redan finnas i systemsökvägen. Om du har problem med att `pip` Installera inte är känt måste du lägga till python och undermappen skript till din system Sök väg.

```
pip install --upgrade --no-deps msgen
pip install msgen
```

Om du inte vill installera `msgen` som en systemomfattande binärfil och ändra systemomfattande python-paket, använder du `–-user` flaggan med `pip` .
Om du använder den paketbaserade installationen eller setup.py installeras alla nödvändiga paket. Annars är de grundläggande nödvändiga paketen för `msgen` 

 * [Azure-lagring](https://pypi.python.org/pypi/azure-storage). 
 * [Begär Anden](https://pypi.python.org/pypi/requests). 

Du kan installera de här paketen med `pip`, `easy_install` eller via `setup.py`-standardprocedurer. 

### <a name="test-the-microsoft-genomics-client"></a>Testa Microsoft Genomics-klienten
Om du vill testa Microsoft Genomics klienten laddar du ned konfigurations filen från ditt genomik-konto. I Azure Portal navigerar du till ditt genomik-konto genom att välja **alla tjänster** längst upp till vänster och sedan söka efter och välja genomik-konton.

![Hitta Microsoft Genomics på Azure Portal](./media/quickstart-run-genomics-workflow-portal/genomics-filter-box.png "Hitta Microsoft Genomics på Azure Portal")

Välj det konto för genomik som du nyss har skapat, navigera till **åtkomst nycklar**och ladda ned konfigurations filen.

![Ladda ned konfigurations filen från Microsoft Genomics](./media/quickstart-run-genomics-workflow-portal/genomics-mygenomicsaccount-box.png "Ladda ned konfigurations filen från Microsoft Genomics")

Kontrollera att Microsoft Genomics Python-klienten fungerar med följande kommando

```Python
msgen list -f "<full path where you saved the config file>"
```

## <a name="create-a-microsoft-azure-storage-account"></a>Skapa ett Microsoft Azure Storage konto 
I Microsoft Genomics-tjänsten förväntas indata lagras som blockblobar i ett Azure Storage-konto. Utdatafilerna skrivs också som blockblobar till en container som angetts av användaren i ett Azure Storage-konto. In- och utdata kan finnas i olika lagringskonton.
Om du redan har data i ett Azure Storage-konto behöver du bara se till att det finns på samma plats som Genomics-kontot. Annars uppstår utgående kostnader när Microsoft Genomicss tjänsten körs. Om du ännu inte har ett Azure Storage-konto måste du skapa ett och ladda upp dina data. Du hittar mer information om Azure Storage-konton [här](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account), inklusive vad ett lagrings konto är och vilka tjänster det tillhandahåller. Om du vill skapa ett Azure Storage-konto navigerar du till [skapa lagrings konto](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) i Azure Portal.  

![Sidan skapa lagrings konto](./media/quickstart-run-genomics-workflow-portal/genomics-storage-create-blade1.png "Sidan skapa lagrings konto")

Konfigurera ditt lagrings konto med följande information, som du ser i föregående bild. Använd de flesta standard alternativen för ett lagrings konto och ange endast att kontot är BlobStorage, inte generell användning. Blob-lagring kan vara 2–5 gånger snabbare för ned- och uppladdningar.  Standard distributions modellen Azure Resource Manager rekommenderas.  

 |**Inställning**          |  **Föreslaget värde**  | **Fältbeskrivning** |
 |:-------------------------       |:-------------         |:----------            |
 |Prenumeration         | Din Azure-prenumeration |Mer information om din prenumeration finns i [Prenumerationer](https://account.azure.com/Subscriptions) |      
 |Resursgrupp       | MinResursgrupp       |  Du kan välja samma resurs grupp som ditt genomik-konto. För giltiga resurs grupps namn, se [namngivnings regler](/azure/architecture/best-practices/resource-naming) |
 |Lagringskontots namn         | MittLagringskonto     |Välj ett unikt konto-ID. För giltiga namn, se [namngivnings regler](/azure/architecture/best-practices/resource-naming) |
 |Location                  | USA, västra 2                  | Använd samma plats som platsen för ditt genomik-konto, för att minska utgående kostnader och minska svars tiden.  | 
 |Prestanda                  | Standard                   | Standardinställningen är Standard. Mer information om standard-och Premium lagrings konton finns i [Introduktion till Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-introduction)    |
 |Typ av konto       | BlobStorage       |  Blob-lagring kan vara 2–5 gånger snabbare än lagring generell användning för ned- och uppladdningar. |
 |Replikering                  | Lokalt redundant lagring                  | Med lokalt redundant lagring replikeras dina data i datacentret i den region där du har skapat ditt lagringskonto. Mer information finns i [Azure Storage replikering](https://docs.microsoft.com/azure/storage/common/storage-redundancy)    |
 |Åtkomstnivå                  | Frekvent                   | Frekvent åtkomst indikerar att objekten på lagringskontot kommer att användas oftare.    |

Välj sedan **Granska och skapa** för att skapa ditt lagrings konto. Precis som du gjorde när du skapade ditt genomik-konto kan du välja **meddelanden** i det övre meny fältet för att övervaka distributions processen. 

## <a name="upload-input-data-to-your-storage-account"></a>Ladda upp indata till ditt lagringskonto

Den Microsoft Genomics tjänsten förväntar sig kopplade slut läsningar (fastq-eller BAM-filer) som indatafiler. Du kan välja att antingen ladda upp dina egna data eller utforska med offentligt tillgängliga exempeldata som du får. Om du vill använda offentligt tillgängliga exempeldata finns de här:

[https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_1.fq.gz)
[https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz](https://msgensampledata.blob.core.windows.net/small/chr21_2.fq.gz)

I ditt lagringskonto måste du skapa en blob-container för dina indata och en andra blob-container för dina utdata.  Ladda upp indata till blob-containern för indata. Du kan använda olika verktyg för att göra detta, inklusive [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), [BlobPorter](https://github.com/Azure/blobporter)eller [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). 

## <a name="run-a-workflow-through-the-microsoft-genomics-service-using-the-python-client"></a>Köra ett arbetsflöde genom Microsoft Genomics-tjänsten med hjälp av Python-klienten 

Om du vill köra ett arbets flöde via Microsoft Genomics tjänsten redigerar du filen *config. txt* och anger lagrings behållaren för indata och utdata för dina data.
Öppna filen *config. txt* som du laddade ned från ditt genomik-konto. De avsnitt du behöver ange är prenumerations nyckeln och de sex objekten längst ned, lagrings kontots namn, nyckel och behållar namn för både indata och utdata. Du kan hitta den här informationen genom att navigera i Azure Portal för att **komma åt nycklar** för ditt lagrings konto, eller direkt från Azure Storage Explorer.  

![Genomik-konfiguration](./media/quickstart-run-genomics-workflow-portal/genomics-config.png "Genomik-konfiguration")

Ange parametern till om du vill köra GATK4 `process_name` `gatk4` .

Som standard visar Genomics-tjänsten VCF-filer. Om du vill ha en gVCF-utdata i stället för en VCF-utmatning (motsvarar `-emitRefConfidence` i användas 3. x och `emit-ref-confidence` i användas 4. x) lägger du till `emit_ref_confidence` parametern i *config. txt* och anger den till `gvcf` , som visas i föregående bild.  Om du vill ändra tillbaka till VCF-utdata tar du antingen bort den från filen *config. txt* eller anger `emit_ref_confidence` parametern till `none` . 

### <a name="submit-your-workflow-to-the-microsoft-genomics-service-the-microsoft-genomics-client"></a>Skicka arbetsflödet till Microsoft Genomics-tjänsten

Använd Microsoft Genomics Python-klienten för att skicka ditt arbetsflöde med följande kommando:

```python
msgen submit -f [full path to your config file] -b1 [name of your first paired end read] -b2 [name of your second paired end read]
```

Du kan visa statusen för dina arbetsflöden med hjälp av följande kommando: 
```python
msgen list -f c:\temp\config.txt 
```

När arbets flödet har slutförts kan du Visa utdatafilerna i ditt Azure Storage-konto i behållaren för utdata som du har konfigurerat. 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du laddat upp exempel indata till Azure Storage och skickat ett arbets flöde till Microsoft Genomics tjänsten via `msgen` python-klienten. Mer information om andra typer av indatafiler som kan användas med Microsoft Genomics-tjänsten finns på följande sidor: [parad fastq](quickstart-input-pair-FASTQ.md)  |  [BAM](quickstart-input-BAM.md)  |  [Multiple fastq eller BAM](quickstart-input-multiple.md). Du kan även utforska den här självstudien med hjälp av vår [Azure-självstudie som anteckningsbok.](https://aka.ms/genomicsnotebook)
