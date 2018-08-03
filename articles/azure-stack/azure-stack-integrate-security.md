---
title: Integrering med Azure Stack datacenter - säkerhet
description: Lär dig hur du integrerar Azure Stack-säkerhet med din datacenter
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: ''
ms.openlocfilehash: 9f356b814ac1ac6ca8b6d6efe7cb9f5d9ed66270
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39442483"
---
# <a name="azure-stack-datacenter-integration---security"></a>Integrering med Azure Stack datacenter - säkerhet
Azure Stack utformades och byggdes med säkerhet i åtanke. Azure Stack är en låsning av systemet, så Programvaruinstallation security-agenten inte stöds.

Den här artikeln hjälper dig att integrera Azure Stack-säkerhetsfunktioner med säkerhetslösningar som redan har distribuerats i ditt datacenter.

## <a name="security-logs"></a>Säkerhetsloggar

Azure Stack samlar in operativsystem och säkerhetshändelser för infrastrukturroller och skala enhet noder varannan minut. Loggfilerna lagras i blob-behållare för storage-konto.

Det finns ett lagringskonto per roll för infrastruktur och ett allmänt lagringskonto för vanligt operativsystemhändelser.

Resursprovidern hälsotillstånd kan anropas via REST-protokollet för att hämta URL: en till blob-behållaren. Säkerhetslösningar från tredje part kan använda API: et och storage-konton för att hämta händelser för bearbetning.

### <a name="use-azure-storage-explorer-to-view-events"></a>Använda Azure Storage Explorer för att visa händelser

Du kan hämta händelser som samlas in av Azure Stack med hjälp av verktyget Azure Storage Explorer. Du kan ladda ned Azure Storage Explorer från [ http://storageexplorer.com ](http://storageexplorer.com).

Följande procedur är ett exempel som du kan använda för att konfigurera Azure Storage Explorer för Azure Stack:

1. Logga in på Azure Stack-administratörsportalen som en operatör.
1. Bläddra **lagringskonton** och leta efter **frphealthaccount**. Den **frphealthaccount** kontot är det allmänna lagringskonto som används för att lagra alla händelser i operativsystemet.

   ![Lagringskonton](media/azure-stack-integrate-security/storage-accounts.png)

1. Välj **frphealthaccount**, klicka sedan på **åtkomstnycklar**.

   ![Åtkomstnycklar](media/azure-stack-integrate-security/access-keys.png)

1. Kopiera åtkomstnyckeln till Urklipp.
1. Öppna Azure Storage Explorer.
1. På den **redigera** menyn och välj **Target Azure Stack**.
1. Välj **Lägg till konto**, och välj sedan **använder en lagringskontonamnet och nyckeln**.

   ![Ansluta storage](media/azure-stack-integrate-security/connect-storage.png)

1. Klicka på **Nästa**.
1. På den **Anslut extern lagring** sidan:

   a. Skriv kontonamnet **frphealthaccount**.

   b. Klistra in åtkomstnyckeln för lagringskontot.

   c. Under **domän för lagringsslutpunkter**väljer **andra**, och anger lagringsslutpunkten **[Region]. [ Domännamn]**.

   d. Välj den **Använd HTTP** markerar du kryssrutan.

   ![Anslut extern lagring](media/azure-stack-integrate-security/attach-storage.png)

1. Klicka på **nästa**, granskar du sammanfattningen och **Slutför** i guiden.
1. Du kan nu bläddra enskilda blob-behållare och hämta händelser.

   ![Bläddra efter blobar](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Använda programmeringsspråk på åtkomsthändelser

Du kan använda olika programmeringsspråk för att få åtkomst till ett lagringskonto. Använd följande dokumentation för att välja ett exempel som matchar ditt språk:

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Enheten åtkomstgranskning

Alla fysiska enheter i Azure Stack stöd för användning av TACACS eller RADIUS. Detta inkluderar åtkomst till hanteringsstyrenheten för baskort (BMC) och nätverksväxlar.

Azure Stack-lösningar levereras inte med RADIUS eller TACACS inbyggda. Lösningarna har har godkänt för att stödja användning av befintliga RADIUS eller TACACS lösningar som är tillgängliga på marknaden.

För RADIUS, har MSCHAPv2 verifierats. Detta representerar den säkraste implementeringen med RADIUS.
Kontakta maskinvaruleverantören OEM för att aktivera TACAS eller RADIUS i de enheter som ingår i din Azure Stack-lösning.

## <a name="syslog"></a>Syslog

Alla fysiska enheter i Azure Stack kan skicka Syslog-meddelanden. Azure Stack-lösningar levereras inte med en Syslog-server. Lösningarna har har godkänt för att stödja skicka meddelanden till befintliga Syslog lösningar som är tillgängliga på marknaden.

Syslog-måladressen är en valfri parameter som samlats in för distribution, men den kan också läggas till efter distribution.

## <a name="next-steps"></a>Nästa steg

[Hanteringsprincip](azure-stack-servicing-policy.md)
