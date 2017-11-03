---
title: "Integration av Azure Stack datacenter - säkerhet"
description: "Lär dig hur du integrerar Azure Stack säkerheten med din datacenter"
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/17/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: eb7c651362838d44d6558e080e6130b4a8041d1e
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2017
---
# <a name="azure-stack-datacenter-integration---security"></a>Integration av Azure Stack datacenter - säkerhet

*Gäller för: Azure Stack integrerat system*

Azure-stacken är utformat och skapat med säkerhet i åtanke. Azure stacken är ett system för låst, så Programvaruinstallation security-agenten inte stöds.

Den här artikeln hjälper dig att integrera Azure Stack säkerhetsfunktioner med säkerhetslösningar som redan har distribuerats i ditt datacenter.

## <a name="security-logs"></a>Säkerhetsloggar

Azure-stacken samlar in operativsystemet och säkerhetshändelser för infrastrukturroller och skala enhet noder varannan minut. Loggfilerna lagras i blob-behållare för storage-konto.

Det finns ett lagringskonto per roll för infrastruktur och ett allmänt lagringskonto för alla vanliga operativsystem-händelser.

Resursprovidern hälsa kan anropas via REST-protokollet för att hämta URL: en till blob-behållare. Säkerhetslösningar från tredje part kan använda API: et och storage-konton för att hämta händelser att bearbeta.

### <a name="use-azure-storage-explorer-to-view-events"></a>Använda Azure Lagringsutforskaren för att visa händelser

Du kan hämta händelser som samlas in av Azure-stacken med verktyget Azure Lagringsutforskaren. Du kan hämta Azure Lagringsutforskaren från [http://storageexplorer.com](http://storageexplorer.com).

Följande procedur är ett exempel som du kan använda för att konfigurera Azure Lagringsutforskaren för Azure-Stack:

1. Logga in på Azure Stack-administratörsportalen som en operator.
2. Bläddra **lagringskonton** och leta efter **frphealthaccount**. Den **frphealthaccount** kontot är allmänt storage-konto som används för att lagra alla händelser i operativsystemet.

   ![Lagringskonton](media/azure-stack-integrate-security/storage-accounts.png)

3. Välj **frphealthaccount**, klicka på **åtkomstnycklar**.

   ![Åtkomstnycklar](media/azure-stack-integrate-security/access-keys.png)

4. Kopiera åtkomstnyckeln till Urklipp.
5. Öppna Utforskaren i Azure Storage.
6. På den **redigera** väljer du **mål Azure Stack**.
7. Välj **Lägg till konto**, och välj sedan **använder ett lagringskontonamn och nyckel**.

   ![Anslut lagringsutrymmet](media/azure-stack-integrate-security/connect-storage.png)

8. Klicka på **Nästa**.
9. På den **Anslut extern lagring** sidan:

   a. Ange kontonamnet **frphealthaccount**.

   b. Klistra in åtkomstnyckeln för lagringskontot.

   c. Under **lagring slutpunkter domän**väljer **andra**, och ange lagring slutpunkten **[Region]. [ Domännamn]**.

   d. Välj den **Använd HTTP** kryssrutan.

   ![Anslut extern lagring](media/azure-stack-integrate-security/attach-storage.png)

10. Klicka på **nästa**, Granska sammanfattningen och **Slutför** i guiden.
11. Du kan nu bläddra enskilda blob-behållare och hämta händelser.

   ![Bläddra blobbar](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>Använd programmeringsspråk till händelser

Du kan använda olika programmeringsspråk för att få åtkomst till ett lagringskonto. Använd följande dokumentation om du vill välja ett exempel som matchar ditt språk:

[https://Azure.microsoft.com/resources/Samples/?term=Storage+Account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>Enheten granskning

Alla fysiska enheter i Azure-stacken stöder användning av TACACS eller RADIUS. Detta inbegriper tillgång till hanteringsstyrenhet för baskort (BMC) och nätverksväxlar.

Azure Stack lösningar levereras inte med RADIUS eller TACACS inbyggda. Men har lösningarna validerats för att stödja användning av befintliga RADIUS eller TACACS lösningar som är tillgängliga på marknaden.

För RADIUS, har MSCHAPv2 verifierats. Detta representerar den mest säkra implementeringen med RADIUS.
Kontakta OEM-maskinvaruleverantören att aktivera TACAS eller RADIUS i enheter som ingår i din Azure Stack-lösning.

## <a name="syslog"></a>Syslog

Alla fysiska enheter i Azure-stacken kan skicka Syslog-meddelanden. Azure Stack lösningar levereras inte med en Syslog-server. Men har lösningarna validerats för att stödja skicka meddelanden till befintliga Syslog lösningar som är tillgängliga på marknaden.

Måladress Syslog är en valfri parameter som samlats in för distribution, men det kan också läggas efter distributionen.

## <a name="next-steps"></a>Nästa steg

[Azure stacken datacenter integrering – publicera slutpunkter](azure-stack-integrate-endpoints.md)
