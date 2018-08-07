---
title: Verktyg för att arbeta med Azure Storage | Microsoft Docs
description: En lista över verktyg som gör det möjligt att visa/interagera med dina Azure Storage-data.
services: storage
author: dineshmurthy
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: dineshmurthy
ms.component: common
ms.openlocfilehash: 55fa63e75768662a0f909cef0e3cc04b9c2dae41
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526206"
---
# <a name="azure-storage-client-tools"></a>Azure Storage-klientverktyg
Användare av Azure Storage vill ofta kunna visa/interagera med sina data med ett Azure Storage-klientverktyg. I tabellerna nedan listas vi ett antal verktyg som gör det möjligt att göra detta. Vi placera ett ”X” i varje block om tjänsten ger möjlighet att antingen räkna upp och/eller få åtkomst till data abstraction. Tabellen visar även om verktyg är kostnadsfria eller inte. ”Utvärderingsversion” anger att det finns en kostnadsfri utvärderingsversion, men den fullständiga produkten är inte kostnadsfri. ”J/N” anger att en version är tillgänglig kostnadsfritt, medan en annan version är tillgänglig för köp.

Vi tillhandahåller endast en ögonblicksbild av de tillgängliga Azure Storage-klientverktyg. Dessa verktyg kan fortsätta att utvecklas och växer i funktioner. Om det finns ändringar eller uppdateringar kan du lämna en kommentar för att berätta för oss. Detsamma gäller om du känner av verktyg som bör vara här – vi är glada över att lägga till dem.

**Microsoft Azure Storage-klientverktyg**

<table>
  <tr>
    <th rowspan="2">Azure Storage-klientverktyg</th>
    <th rowspan="2">Blockblob</th>
    <th rowspan="2">Sidblob</th>
    <th rowspan="2">Bifoga Blob</th>
    <th rowspan="2">Tabeller</th>
    <th rowspan="2">Köer</th>
    <th rowspan="2">Filer</th>
    <th rowspan="2">Kostnadsfri</th>
    <th colspan="4">Plattform</th>
  </tr>
  <tr>
    <td>Webb</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure-portalen</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio Server Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Tredjeparts-Azure Storage-klientverktyg**

Vi har inte kontrollerat funktioner eller kvalitet som ägs av följande verktyg från tredje part och deras lista innebär inte att Microsoft.

<table>
  <tr>
    <th rowspan="2">Azure Storage-klientverktyg</th>
    <th rowspan="2">Blockblob</th>
    <th rowspan="2">Sidblob</th>
    <th rowspan="2">Bifoga Blob</th>
    <th rowspan="2">Tabeller</th>
    <th rowspan="2">Köer</th>
    <th rowspan="2">Filer</th>
    <th rowspan="2">Kostnadsfri</th>
    <th colspan="4">Plattform</th>
  </tr>
  <tr>
    <td>Webb</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="http://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Utvärdering</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgate: Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>J/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gapotchenko.com/cloudcombine">Kombinera för molnet</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Utvärdering</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="http://www.gladinet.com/Azure-Storage/index.htm">Gladinet molnet</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Utvärdering</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
