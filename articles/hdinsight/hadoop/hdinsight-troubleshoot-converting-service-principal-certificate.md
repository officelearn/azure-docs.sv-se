---
title: Konvertera certifikat innehåll till bas-64 – Azure HDInsight
description: Konvertera innehållet i tjänstens huvud namn till det Base64 64-kodade sträng formatet i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f4974d9e9a2ff3b63b36e45d406a016078bb290
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2020
ms.locfileid: "89008173"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>Konverterar innehåll för tjänstens huvud namn till formatet Base-64-kodat sträng format i HDInsight

Den här artikeln beskriver fel söknings steg och möjliga lösningar för problem med att interagera med Azure HDInsight-kluster.

## <a name="issue"></a>Problem

Du får ett fel meddelande om att indata inte är en giltig bas-64-sträng eftersom det innehåller ett tecken som inte är av basen 64, fler än två utfyllnad i utfyllnad eller ett tecken som inte är blank steg mellan utfyllnads tecknen.

## <a name="cause"></a>Orsak

När du använder en PowerShell-eller Azure-mall för att skapa kluster med Data Lake som primär eller ytterligare lagrings utrymme, är tjänstens huvud certifikat innehåll som har åtkomst till Data Lake lagrings kontot i formatet Base-64. Felaktig konvertering av PFX-certifikatets innehåll till den Base-64-kodade strängen kan leda till det här felet.

## <a name="resolution"></a>Lösning

När du har ett tjänst huvud certifikat i PFX-format (se [här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage) för exempel åtgärder för att skapa tjänstens huvud namn), använder du följande PowerShell-kommando eller C#-kodfragment för att konvertera certifikat innehållet till formatet base-64.

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

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).
