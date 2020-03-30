---
title: Konvertera certifikatinnehåll till bas-64 - Azure HDInsight
description: Konvertera huvudcertifikatinnehåll för tjänsten till bas-64-kodat strängformat i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: d6119e4f8c651ba482a24f46b44ff15f870858ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894176"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Konvertera huvudcertifikatinnehåll för tjänsten till bas-64-kodat strängformat i HDInsight

I den här artikeln beskrivs felsökningssteg och möjliga lösningar för problem när du interagerar med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Ett felmeddelande visas om att indata inte är en giltig Base-64-sträng eftersom den innehåller ett 64-tecken som inte är bas, fler än två utfyllnadstecken eller ett tecken på blanksteg bland utfyllnadstecknen.

## <a name="cause"></a>Orsak

När du använder PowerShell- eller Azure-malldistribution för att skapa kluster med DataSjö som primärt eller ytterligare lagringsutrymme, är tjänstens huvudcertifikatinnehåll som tillhandahålls för åtkomst till datasjölagringskontot i bas-64-format. Felaktig konvertering av pfx-certifikatinnehåll till bas-64-kodad sträng kan leda till det här felet.

## <a name="resolution"></a>Lösning

När du har tjänstens huvudcertifikat i pfx-format (se [här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) för steg för att skapa exempeltjänstens huvudnamn) använder du följande PowerShell-kommando eller C#-kodavsnitt för att konvertera certifikatinnehållet till bas-64-format.

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Om du inte såg problemet eller inte kan lösa problemet besöker du någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure Community Support](https://azure.microsoft.com/support/community/).

* Anslut [@AzureSupport](https://twitter.com/azuresupport) med – det officiella Microsoft Azure-kontot för att förbättra kundupplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en supportbegäran från [Azure-portalen](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **Stöd** i menyraden eller öppna **supporthubben Hjälp +.** Mer detaljerad information finns i Så här skapar du [en Azure-supportbegäran](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerationshantering och faktureringssupport ingår i din Microsoft Azure-prenumeration och teknisk support tillhandahålls via en av [Azure-supportplanerna](https://azure.microsoft.com/support/plans/).
