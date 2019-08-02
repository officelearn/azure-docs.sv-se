---
title: Verktyg för att arbeta med Azure Storage | Microsoft Docs
description: En lista med verktyg som gör att du kan visa/interagera med dina Azure Storage data.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2017
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 14cbc076f4c9eebd2647cd667acc856b393e4d93
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68665735"
---
# <a name="azure-storage-client-tools"></a>Azure Storage-klientverktyg
Användare av Azure Storage ofta vill kunna visa/interagera med sina data med hjälp av ett Azure Storage klient verktyg. I tabellerna nedan visar vi ett antal verktyg som gör det möjligt att göra detta. Vi skriver "X" i varje block om det ger möjlighet att antingen räkna upp och/eller komma åt data abstraktionen. Tabellen visar även om verktygen är kostnads fria eller inte. "Utvärderings version" anger att det finns en kostnads fri utvärderings version, men den fullständiga produkten är inte kostnads fri. "Y/N" anger att en version är tillgänglig kostnads fritt, medan en annan version är tillgänglig för köp.

Vi har bara tillhandahållit en ögonblicks bild av tillgängliga Azure Storage klient verktyg. Dessa verktyg kan fortsätta att utvecklas och växa i funktioner. Om det finns korrigeringar eller uppdateringar ber vi dig lämna en kommentar för att berätta för oss. Samma sak gäller om du vet vilka verktyg som bör vara här – vi skulle vara glada att lägga till dem.

**Microsoft Azure Storage klient verktyg**

<table>
  <tr>
    <th rowspan="2">Azure Storage klient verktyg</th>
    <th rowspan="2">Blockblob</th>
    <th rowspan="2">Sidblob</th>
    <th rowspan="2">Bifoga blob</th>
    <th rowspan="2">Tabeller</th>
    <th rowspan="2">Köer</th>
    <th rowspan="2">Filer</th>
    <th rowspan="2">Lediga</th>
    <th colspan="4">Plattform</th>
  </tr>
  <tr>
    <td>Webb</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Microsoft Azure Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Microsoft Visual Studio-Server Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>J</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Klient verktyg från tredje part Azure Storage**

Vi har inte kontrollerat de funktioner eller den kvalitet som har ansökts av följande verktyg från tredje part och att deras lista inte innebär en bekräftelse från Microsoft.

<table>
  <tr>
    <th rowspan="2">Azure Storage klient verktyg</th>
    <th rowspan="2">Blockblob</th>
    <th rowspan="2">Sidblob</th>
    <th rowspan="2">Bifoga blob</th>
    <th rowspan="2">Tabeller</th>
    <th rowspan="2">Köer</th>
    <th rowspan="2">Filer</th>
    <th rowspan="2">Lediga</th>
    <th colspan="4">Plattform</th>
  </tr>
  <tr>
    <td>Webb</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
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
    <td><a href="https://www.red-gate.com/products/azure-development/azure-explorer/index">Redgates Azure Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>J</td>
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
    <td>X</td>
    <td>J</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
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
    <td><a href="https://www.gapotchenko.com/cloudcombine">Molnbaserad kombination</a></td>
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
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>J</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet-moln</a></td>
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
