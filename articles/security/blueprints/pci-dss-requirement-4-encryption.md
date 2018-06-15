---
title: Azure betalning bearbetning modell - kraven för datakryptering
description: PCI DSS krav 4
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 43f75ba9-cb4e-49ab-b3f4-09e48310bc18
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 6de3290fc2147e3c8ed63642b6e8470093898ef6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
ms.locfileid: "33896429"
---
# <a name="encryption-requirements-for-pci-dss-compliant-environments"></a>Kraven för datakryptering för PCI DSS-kompatibel miljöer 
## <a name="pci-dss-requirement-4"></a>PCI DSS krav 4

**Kryptera överföring av kreditkortsinformation över öppna offentliga nätverk**

> [!NOTE]
> Dessa krav definieras av den [PCI Payment Card Industry () Security Standards rådet](https://www.pcisecuritystandards.org/pci_security/) som en del av den [PCI Data Security Standard (DSS) Version 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Läs PCI DSS information om hur du testar vägledning för varje krav och procedurer.

Känslig information måste vara krypterade under överföring via nätverk som är lätt att komma åt obehöriga användare. Felkonfigurerad trådlösa nätverk och säkerhetsproblem i äldre kryptering och autentiseringsprotokoll fortsätta mål för obehöriga användare som dessa säkerhetsproblem för att få privilegierad åtkomst till Kortinnehavarens data miljöer.

## <a name="pci-dss-requirement-41"></a>PCI DSS krav 4.1

**4.1** använda stark kryptering och säkerhet protokoll (exempelvis TLS, IPSEC, SSH osv.) för att skydda känsliga Kortinnehavarens data under överföringen över öppna, offentliga nätverk, inklusive följande:
- Endast betrodda nycklar och certifikat som godkänns.
- Protokollet som används stöder endast säkra versioner eller konfigurationer.
- Krypteringsgrad är lämpligt för metoden som kryptering används. 

> [!NOTE]
> Om SSL för tidig TLS används måste kraven i bilaga A2 utföras.
>
> Exempel på öppna nätverk på offentliga inkludera, men är inte begränsade till:
> - Internet
> - Trådlösa tekniker, inklusive 802.11 och Bluetooth
> - Mobil tekniker, till exempel Global System for Mobile communications (GSM) Code division flera åtkomst CDMA)
> - Allmänna paket alternativknapp Service (GPRS)
> - Satellit


**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore är en PaaS-lösning som implementerar stark kryptografi för distributionen på följande sätt:<br /><br />Till krypterade data i vila krav [Azure Storage](https://azure.microsoft.com/services/storage/) använder du följande:<br /><br /><ul><li>[Azure Storage Service-kryptering (SSE) av Data i vila](/azure/storage/storage-service-encryption)</li><li>SQL Database: En PaaS SQL-databasinstans används för att presentera säkerhetsåtgärder för databasen. Mer information finns i [PCI - vägledning för Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).</li><li>[Azure Disk Encryption (Bitlocker)](/azure/security/azure-security-disk-encryption)</li></ul>Använda Azure Key Vault justerar med Azure Government och PCI DSS HIPAA krav.|



### <a name="pci-dss-requirement-411"></a>PCI DSS krav 4.1.1

**4.1.1** Kontrollera trådlösa nätverk som skickar kreditkortsinformation eller ansluten till Kortinnehavarens datamiljö, använda branschstandardens rekommendationer (till exempel IEEE 802.11i) för att implementera stark kryptering för autentisering och överföring.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Trådlösa och SNMP har inte implementerats i lösningen.|



## <a name="pci-dss-requirement-42"></a>PCI DSS krav 4.2

**4.2** Skicka aldrig oskyddade Panorera av slutanvändaren tekniker (till exempel e-post, snabbmeddelanden, SMS, chatt osv.).

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Contoso Webstore har inte någon meddelandelösningar implementerats som kan skicka oskyddade primära konto numret (PANORERA) data.|



## <a name="pci-dss-requirement-43"></a>PCI DSS krav 4.3

**4.3** Kontrollera att IPSec-principer och operativa procedurer för att kryptera överföringar av kreditkortsinformation dokumenterade används, och kända för alla berörda parter.

**Ansvarsområden:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Inte tillämpligt. |
| **Kunden<br />(PCI&#8209;DSS&nbsp;modell)** | Kunder ansvarar för att dokumentera och kryptera överföringar som innehåller kreditkortsinformation.|




